# 03 给番茄作家后台写一个 MCP 服务

> 平台没有公开 API，但写作流程需要一个。于是把网页后台变成 MCP 工具，让 AI 客户端直接管章节。

## 起因

连载写到后期，发布变成一个纯体力活：本地有一整本正文，平台后台要一章一章新建、贴标题、贴正文、选分卷、点发布。

一次两次可以忍。几十上百次就不该由人做——**凡是能用脚本描述的重复劳动，都不该占用手工时间**。

问题是平台没有开放 API。所以这个项目做了三件事：

1. 用浏览器自动化抓下登录态；
2. 把后台的私有接口封装成 9 个工具；
3. 用 MCP 协议暴露给任意 AI 客户端，让「把第 1 到 20 章传到某某书」变成一句话。

代码：[fanqie-writer-mcp](https://github.com/CommitByRyan/fanqie-writer-mcp)，零第三方依赖，纯 Node 标准库。

## 为什么是 MCP，不是脚本

脚本能解决「我自己用」。MCP 解决「换个客户端也不用重写」。

MCP（Model Context Protocol）本质是一层约定：服务端声明自己有哪些工具、每个工具要什么参数，客户端负责调用。写好一次，Claude Desktop、Cursor、Windsurf、Zed、Trae 这些都能接。

代价是要守协议细节——而这里踩的坑，比业务逻辑本身多得多。

## 架构

```
          ┌──────────────────────────────┐
客户端  →  │  MCP 服务（server.js）        │
（stdio 或 │   9 个工具                    │
 HTTP）   │   ├ 鉴权检查 / 作品 / 分卷      │
          │   ├ 章节列表 / 上传 / 批量上传   │
          │   ├ 更新章节 / 预览 HTML        │
          │   └ 传输层：stdio | HTTP        │
          └───────────┬──────────────────┘
                      ↓ 复用同一套请求函数
          ┌──────────────────────────────┐
          │  番茄作家后台私有接口          │
          │  （Cookie + CSRF 鉴权）        │
          └──────────────────────────────┘
```

关键设计：**业务逻辑与传输层解耦**。所有工具函数只往 `stderr` 写日志、往调用方返回结构，不碰 `stdout`。

这一条不是洁癖，是硬要求。见下面第一个坑。

## 坑一：stdout 被日志污染，协议直接崩

stdio 传输下，MCP 的 JSON-RPC 报文走 `stdout`，一行一个 JSON。

如果你在里面随手 `console.log('上传成功')`，客户端收到的是一个非 JSON 行，协议解析失败——表现为「服务连上了但所有工具都报错」，而且**没有明确报错信息**，极难定位。

我的做法：

```javascript
// 一律走 stderr
process.stderr.write(`[fanqie-writer] 第 ${n} 章已提交\n`);

// stdout 只允许承载 JSON-RPC
process.stdout.write(JSON.stringify(response) + '\n');
```

排查时把这句写进注释，比事后 debug 便宜得多。

## 坑二：抓登录态，CDP 的 headers 里没有 Cookie

后台接口要两个东西：`Cookie` 和 `x-secsdk-csrf-token`。

第一版用 Chrome DevTools Protocol 的 `Network.requestWillBeSent` 抓请求头——拿到的 headers 里**没有 Cookie**。这是 CDP 的已知行为：Cookie 属于「extra info」，不在基础事件里。

第二版换 `requestWillBeSentExtraInfo`，能从 `params.headers` 顶层拿到完整头。能跑，但不稳——事件时序不由你控制，偶尔抓空。

第三版（现在用的）换思路：**不监听请求，直接在页面里挂钩子**。

```javascript
// Page.addScriptToEvaluateOnNewDocument —— 在页面脚本执行前注入
// 改写 fetch / XHR，把页面自己发出的 /api/author 请求头原样记下来
```

页面自己发请求时，Cookie 和 CSRF 一定都在。钩子只负责「顺手抄一份」，比外部监听稳得多。再用 `Network.getCookies` 兜底 httpOnly 部分。

**结论：抓鉴权态时，站在页面里面看，比站在外面猜可靠。**

## 坑三：章节顺序不由你决定

平台**没有章节排序 API**。章节在后台的排列顺序 = 创建顺序。

这意味着批量上传必须严格按章号先后发，中间漏一章、失败后重试顺序错了，整本书的目录就乱了。这个错误不可逆——只能删掉重传。

工具里的对策：

- `dry_run` 默认 `true`，先出计划再执行；
- `range=1-5` 先小批量试；
- `continue_on_error` 遇错可继续，但**鉴权失效必须立刻停**（继续只会全部失败）；
- `interval_ms` 默认 2000ms，压到 1000 以下容易被限流。

## 坑四：正文必须是 HTML 段落

后台不接受纯文本。传纯文本进去，段落会挤成一坨。

工具里做了转换：按空行分段 → 包 `<p>` → 去掉 Markdown 残留（`**`、`#`、`>` 开头的行）。标题也必须带「第N章」前缀，同样自动补齐。

这些规则不是文档写的，是**实测撞出来的**：传上去看不出来，读者端一看全挤在一起。

## 批量上传的三个参数，决定好不好用

```javascript
batch_upload({
  source: '我的作品/某书',   // 目录或 manifest
  book_id: '76xxxxxxxxxx',
  range: '1-20',
  mode: 'draft',            // draft 存草稿；publish 提交审核
  dry_run: true,            // 默认只看计划
  interval_ms: 2000,
  max_chapters: 20,
  volume_map: { '1': { volume_id: '...', volume_name: '第一卷' } }
})
```

`source` 支持三种形态，按省事程度排：

1. **章节索引式目录**——目录里有 `chapters.json` + 正文文件，工具自动读章号/标题/卷号；
2. **普通目录**——`*.md` 按文件名排序，首行「第N章 标题」作标题；
3. **manifest JSON**——最通用，`[{"title":"第1章 …","file":"a.md"}]`。

`mode=publish` 之后章节进入审核态（`display=5`），**审核中不可修改**，硬改会返回 `-3027`。所以流程上我坚持先 `draft`，抽查格式无误再提交。

## 17 个客户端，一键注册

MCP 的配置文件每个客户端都不一样——路径不同、字段名不同（有的叫 `mcpServers`，有的叫 `servers`）。手写 17 份是纯浪费。

`mcp-config.mjs` 用一个映射表统一处理：

```bash
node mcp-config.mjs            # 列出支持的客户端与本机检测结果
node mcp-config.mjs --all      # 给所有检测到的客户端写入配置
node mcp-config.mjs --client cursor
```

目前已覆盖：Claude Desktop、Claude Code、Cursor、VS Code、Windsurf、Cline、Roo Code、Zed、Trae、Cherry Studio、Gemini CLI、Amazon Q、OpenCode、WorkBuddy、LM Studio、Continue、Codex。

## 双传输：stdio 之外为什么还要 HTTP

stdio 适合桌面客户端本地起进程。但**平台型 Agent**（Dify、n8n、扣子这类）跑在服务端，起不了本地进程，只能走 HTTP。

所以加 `--http 8787` 时同时监听 Streamable HTTP（`POST/GET/DELETE /mcp`），stdio 照常可用。两个传输共用同一套工具函数——这也是前面「业务与传输解耦」的回报。

浏览器端 Agent 还要 CORS 预检，顺手放行。

## 能力边界（说清楚比吹有用）

| 能做 | 不能做 |
| --- | --- |
| 查登录态、作品、分卷、章节列表 | 不能改章节排序（平台无此 API） |
| 单章 / 批量上传，存草稿或提交审核 | 不能绕过审核 |
| 更新已有章节 | 审核中的章节改不了 |
| 本地出预览 HTML 校验格式 | 不能抓取读者数据 |

登录态会过期，过期后所有工具报鉴权失败——重跑 `capture-login.mjs` 即可。这是设计如此，不是 bug。

## 做完之后的感受

写这个项目最大的收获不是 MCP 本身，而是两件事：

1. **协议细节比业务逻辑重要。** 9 个工具的代码量，比不上「stdout 不能有杂音」这一条踩坑的时间。
2. **不可逆操作必须先 dry_run。** 章节顺序错了要删整本，这种代价一次就够记一辈子。

下篇开始写另一个项目——[徽写](04-huixie-state-machine.md)：为什么给长篇小说做状态机，比做文本生成难得多。

---

上一篇：[为自己的技术能力打造门户](02-deploy-navigation-site.md)｜下一篇：[长篇小说的状态机](04-huixie-state-machine.md)
