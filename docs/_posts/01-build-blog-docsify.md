# 01 搭建技术博客：Docsify + GitHub Pages

> 目标很朴素：写东西的地方不要变成折腾工具的地方。

我搭这个博客只花了半小时，之后每次写文章只需要新建一个 Markdown 文件。没有构建、没有打包、没有「改完要重新生成」这一步——**源码就是页面**。

如果你有 GitHub 加速工具，可以跳过第 1 步。

## 为什么选 Docsify

做静态博客的选择很多，我先列一下当时比对过的四个：

| 方案 | 构建步骤 | 上手成本 | 适合场景 | 不适合 |
| --- | --- | --- | --- | --- |
| Hexo | 有 | 中 | 主题生态丰富，偏「博客站」 | 每次写完要 `hexo g` 再推生成物 |
| Hugo | 有（快） | 中偏高 | 文章量大、构建速度敏感 | 要装 Go 环境，模板语法陡 |
| VitePress | 有 | 中 | 文档站、组件演示 | 依赖 Node 打包链，配置面大 |
| **Docsify** | **无** | **低** | 纯 Markdown 记录，随时改随时生效 | SEO 较弱（运行时渲染） |

我选 Docsify 的核心理由只有两条：

1. **零构建**。GitHub Pages 直接托管 `docs/` 目录，推上去 1–2 分钟就是新内容。
2. **改写成本为零**。写文章＝新建 `.md`，加一行侧边栏——不需要动配置。

代价我也认：Docsify 在浏览器里渲染 Markdown，搜索引擎抓取能力弱于预渲染方案。对我这种「记录为主、不指望搜索流量」的用途，这个牺牲是划算的。

如果你要靠文章做流量入口，选 Hugo 或 VitePress 更合适。

## 1. 准备：加速访问 GitHub

先装 [Watt Toolkit](https://steampp.net/)（原 Steam++），打开后勾选「GitHub 加速」。

这一步不是必须，但国内直接 `git clone` / `push` 经常卡在几 KB/s，后面会很难受。

## 2. 装 Node.js

到 [nodejs.org](https://nodejs.org/) 下载对应系统的安装包，一路默认安装即可（路径可自选）。

验证：

```bash
node -v
# v24.11.1
```

> Docsify CLI 通过 npm 分发，所以需要 Node 环境。但**只有本地预览需要**——部署到 Pages 之后，访客端不需要任何环境。

## 3. 初始化

```bash
npm i docsify-cli -g     # 全局装 CLI
docsify init docs        # 在当前目录生成 docs/
```

生成的结构：

```
docs/
├─index.html     # 入口与全部配置
├─README.md      # 首页（必须叫这个名字）
└─.nojekyll      # 空文件，告诉 Pages 别走 Jekyll
```

`.nojekyll` 这个空文件很关键：GitHub Pages 默认用 Jekyll 处理站点，它会忽略下划线开头的文件/目录。Docsify 的 `_sidebar.md`、`_404.md`、`_posts/` 全都会被吃掉。

**没有这个文件，你的侧边栏和文章目录会直接消失。**

## 4. 本地预览

```bash
docsify serve docs
```

打开 `http://localhost:3000`。改 Markdown 保存，页面自动刷新，不需要重启。

## 5. 部署到 Pages

**方式一：网页拖拽（最省事）**

进入仓库，点「Add file」→「Upload files」，把 `docs` 文件夹整个拖进去。

**方式二：命令行**

```bash
git add .
git commit -m "docs: 初始化博客"
git push origin main
```

然后在仓库 **Settings → Pages**：

- Source 选 `Deploy from a branch`
- Branch 选 `main`，目录选 `/docs`
- 保存，等 1–2 分钟

访问 `https://<你的用户名>.github.io` 即可。仓库名叫 `<用户名>.github.io` 的话，站点会直接挂在根域名上。

## 6. 搭起来之后改了什么

初始化出来的版本很素。我动的地方：

**加入侧边栏**（`index.html`）：

```javascript
window.$docsify = {
  name: 'Ryan 的技术笔记',
  loadSidebar: true,
  subMaxLevel: 3,        // 侧边栏展开到三级标题
  auto2top: true,        // 切页自动回顶部
  search: {
    placeholder: '搜索文章...',
    depth: 3
  },
  notFoundPage: '_404.md'
}
```

**文章放进 `_posts/`**，编号排序：

```
docs/_posts/
├─01-build-blog-docsify.md
├─02-deploy-navigation-site.md
└─03-fanqie-writer-mcp.md
```

**侧边栏分组**（`_sidebar.md`），主题一多就必须分组，否则一长串没法看：

```markdown
- **开始**
  - [首页](README.md)
- **工程实践**
  - [搭建技术博客](_posts/01-build-blog-docsify.md)
```

**全文搜索**：加 `search.min.js` 插件，Docsify 会为所有页面建索引，右上角直接搜。

## 7. 踩过的坑

**坑一：`_sidebar.md` 不生效**

99% 是少了 `.nojekyll`。Pages 用 Jekyll 编译时会把下划线开头的文件排除掉，浏览器请求 404，侧边栏自然不出现。

**坑二：CDN 偶尔抽风**

`index.html` 里的资源默认走 jsDelivr。国内偶尔慢，页面会白屏几秒。稳妥做法是把关键 CSS/JS 换成国内可达的 CDN，或者干脆 vendor 进仓库。

**坑三：改了文章但线上没变**

两个原因：一是 Pages 构建有 1–2 分钟延迟（看 Actions 或 Deployments 记录能确认）；二是浏览器缓存了 Markdown 文件，强刷 `Ctrl+F5`。

**坑四：首页和第一篇文章打架**

`README.md` 天生就是首页。如果直接把第一篇文章写进 `README.md`，站点就没有门面了——访客打开看到的是半篇文章，找不到目录。

我的做法：**知识型内容全部放 `_posts/`，`README.md` 只当门面**（介绍 + 文章索引 + 项目索引）。这样加文章不用动首页结构。

**坑五：中文路径**

文件名用英文（`01-build-blog-docsify.md`），标题写中文。中文文件名在部分 CDN、URL 编码、脚本处理上都容易出问题，没必要给自己找麻烦。

## 8. 现在的写作流程

```bash
# 1. 新建文章
vim docs/_posts/07-something.md

# 2. 加一行侧边栏
#    - [标题](_posts/07-something.md)

# 3. 本地看效果
docsify serve docs

# 4. 推上去
git add . && git commit -m "docs: 新增 XXX" && git push
```

四步，没有第五步。这是我当初选 Docsify 想要的东西。

---

上一篇：无（这是第一篇）｜下一篇：[为自己的技术能力打造门户](02-deploy-navigation-site.md)
