# 项目

> 这里是我在 GitHub 上公开的全部项目。按用途分组，每条标注技术栈与当前状态。

关于排序：**不问新旧，只问还在不在用。** 正在维护的排前面，课程结项的排后面——但它们都完整、可跑、有文档，不是交完就删的作业。

## 速览

| 项目 | 类别 | 技术栈 | 状态 |
| --- | --- | --- | --- |
| [huixie](https://github.com/CommitByRyan/huixie) | 写作工具链 | Python 3.10+（零依赖） | v1.0 可用 |
| [fanqie-writer-mcp](https://github.com/CommitByRyan/fanqie-writer-mcp) | 写作工具链 | Node.js（零依赖）· MCP | 可用 |
| [github-publish-guardrails](https://github.com/CommitByRyan/github-publish-guardrails) | 写作工具链 | Markdown 技能包 | 可用 |
| [agent-craft](https://github.com/CommitByRyan/agent-craft) | Agent 实践 | Markdown + Mermaid | 持续更新 |
| [CommitByRyan.github.io](https://github.com/CommitByRyan/CommitByRyan.github.io) | 个人站点 | Docsify + GitHub Pages | 维护中 |
| [Ryan-links](https://github.com/CommitByRyan/Ryan-links) | 个人站点 | Vue 3 + Vite | 维护中 |
| [quiet-tech-style](https://github.com/CommitByRyan/quiet-tech-style) | 设计规范 | 纯 CSS + 令牌 | v1.0 可用 |
| [online-cinema-ticket](https://github.com/CommitByRyan/online-cinema-ticket) | 课程项目 | Spring Boot + MyBatis + Thymeleaf | 结项 |
| [parking-lot-management-system](https://github.com/CommitByRyan/parking-lot-management-system) | 课程项目 | JSP + Servlet + MySQL | 结项 |
| [ParkingWeb](https://github.com/CommitByRyan/ParkingWeb) | 课程项目 | 材料归档（PPT / 报告） | 归档 |

---

## 一、写作工具链

这组项目解决同一个问题：**把长篇写作中能用程序保证的部分，从人手里拿走。**

### huixie · 徽写

> 长篇写作工作流。状态双层解耦、上下文硬预算、节拍器、写回钩子、发布件标准化。

| | |
| --- | --- |
| 技术栈 | Python 3.10+，零第三方依赖 |
| 规模 | 28 个文件：5 个脚本、6 段提示词、4 份规范、4 个模板 |
| 状态 | v1.0 可用 |

**做了什么**

- **状态双层**：静态设定（世界观、人物）与动态状态（`state.json`：时间线、主角面板、物品栏、活跃 NPC、伏笔池）分离，写作时只信后者；
- **上下文硬预算**：单章 ≤ 12,000 字符，五层配给，超限按 T1–T7 分级降级并**报告降级档位**；
- **节拍器**：正文之前先出 3–4 个冲突节点，校验字数偏差（≤12%）与单拍占比（≤45%）；
- **结构化闸门**：退出码 0/1/2 说话，异常路径不放行；
- **写回钩子**：一章过闸后增量写回状态，按章号幂等；
- **发布件三分离**：纯净正文 / 带元数据 md / manifest（含校验和）。

**验证**：自检 6 组、端到端 41 项断言；实测单章上下文从 87,590 字符压到 4,133（降幅 95.3%）。

📄 相关文章：[04 长篇小说的状态机](_posts/04-huixie-state-machine.md)｜[05 单章上下文 12k 硬预算](_posts/05-huixie-context-budget.md)｜[06 给 AI 工作流写测试](_posts/06-huixie-testable-workflow.md)

### fanqie-writer-mcp

> 番茄小说作家后台的 MCP 服务：把本地稿件目录批量上传成平台章节。

| | |
| --- | --- |
| 技术栈 | Node.js 标准库，零第三方依赖；MCP（stdio + Streamable HTTP） |
| 规模 | 9 个工具、14 个文件、17 个客户端一键注册 |
| 状态 | 可用 |

**解决了什么**：平台没有公开 API，发布只能手工一章一章贴。这个服务用浏览器自动化抓登录态，把后台接口封装成工具，让「把第 1 到 20 章传到某本书」变成一句话。

**几个不那么显然的点**

- `stdout` 只承载 JSON-RPC，日志一律走 `stderr` —— 混一行 `console.log` 就能让整个服务失效；
- 抓鉴权不靠监听网络事件，而是在页面里注入钩子，直接抄页面自己发出的请求头；
- 平台没有章节排序 API，**章节顺序 = 创建顺序**，所以批量上传必须严格按章号走，且默认 `dry_run=true`。

📄 相关文章：[03 给番茄作家后台写一个 MCP 服务](_posts/03-fanqie-writer-mcp.md)

### github-publish-guardrails

> GitHub 发布守门员：推送前校验五条署名红线，推送后同步站点项目索引。

| | |
| --- | --- |
| 形态 | 纯提示词技能包（`SKILL.md` + `README.md` + `LICENSE`），无脚本 |
| 状态 | v1.1 可用 |

五条红线：不改本地 Git 身份；提交信息不加 AI/Agent 署名；不向 README/LICENSE 注入「AI 生成」类标签；版权持有人保持作者本人；提交与发布文案用维护者第一人称或 Conventional Commits。

触发词覆盖「上传 / 提交 / 推送 / 发布 / 打 tag / 开 PR / 部署 Pages / 新增仓库」。

它在 v1.1 加了第六条规则：**发布后必须同步本站的项目索引**。新建或更名仓库时，项目页速览表、首页数字条、根 README 三处一起改，然后等 Pages 构建通过、目标页返回 200。规则里还写明「数量是易腐数据」——写死「N 个公开仓库」就得每次同步，连续两次忘记就降级为 `N+`。

它的价值在于把「我记得要检查」变成「每次都被检查」。这条规则的第一次执行对象就是它自己：本站的项目页正是在它上线后同步的。

---

## 二、Agent 实践

### agent-craft

> 我的 Agent 使用手册与迭代轨迹：稳定原则、可复用工作流、实验记录与可视化思想历程。

| | |
| --- | --- |
| 技术栈 | Markdown + Mermaid，无构建依赖 |
| 规模 | 首版 17 个文件、15 篇 Markdown |
| 状态 | 持续更新 |

**它解决什么**：把分散在全局指令、技能仓库、文章和日常对话里的使用方法集中起来。仓库不把结论和过程混成一锅，而是分成五层：

- **原则**：变化最慢，回答“什么情况下应该怎么做”；
- **工作流**：已经跑通的重复任务，回答“怎样稳定完成”；
- **实验**：有假设、执行过程与可复核结果；
- **决策**：记录重要取舍、代价和复查条件；
- **演进**：用时间线与关系图保留认识如何改变。

**为什么单独建仓**：稳定习惯、一次性实验和时间线有不同的更新频率。全部写进一个 `AGENTS.md` 会越来越臃肿；只留最终结论，又会丢掉“为什么后来改了做法”。仓库用结构把这些内容分开，再用模板保证新记录不会漏掉证据。

📄 [仓库](https://github.com/CommitByRyan/agent-craft)

---

## 三、个人站点

### CommitByRyan.github.io

> 本站。Docsify + GitHub Pages，无构建步骤。

| | |
| --- | --- |
| 技术栈 | Docsify 4、GitHub Pages（main / docs）、jsDelivr CDN |
| 状态 | 维护中 |

源码即内容：新增文章＝加一个 Markdown 文件加一行侧边栏。支持全文搜索、分组侧边栏、代码高亮、阅读进度条、上一篇/下一篇。

📄 [在线访问](https://commitbyryan.github.io/)｜[01 搭建过程](_posts/01-build-blog-docsify.md)

### Ryan-links

> 个人导航页，汇集 GitHub、博客与技术入口。

| | |
| --- | --- |
| 技术栈 | Vue 3（Composition API）+ Vite 5，原生 CSS，无 UI 框架 |
| 状态 | 维护中 |

单页应用，构建产物为纯静态文件，直接托管在 GitHub Pages 的子路径下。

这里踩过一个典型坑：Vite 默认生成绝对路径资源引用，部署到 `/Ryan-links/` 子目录后全 404，必须设 `base: './'`。

📄 [在线访问](https://commitbyryan.github.io/Ryan-links/)｜[02 部署与排错全记录](_posts/02-deploy-navigation-site.md)

---

## 四、设计规范

### quiet-tech-style · 静默科技

> 一套「有科技感但成熟克制」的界面规范。暗色只在首屏、强调色只有一个、分隔用发丝线不用阴影。

| | |
| --- | --- |
| 技术栈 | 纯 CSS（令牌层 + 组件层），零依赖、零框架 |
| 规模 | 8 个文件：2 份 CSS、3 份文档、1 份 prompt、1 个演示页 |
| 状态 | v1.0 可用 |

**它是什么**：不是主题皮肤，是一套**带理由的约束**。五条硬规则——单一强调色、暗色只进首屏、发丝线分隔、等宽字体只做标记、圆角 ≤2px——每条都写了"为什么"，因为风格会被模仿，理由才会被复用。

**三种用法**

1. **直接引 CSS**：`tokens.css` 定色与字号，`theme.css` 提供 `qt-*` 组件；
2. **复制 prompt**：`prompt.md` 整段丢给模型，替换占位符即可生成同风格页面；
3. **装成技能**：`SKILL.md` 放入技能目录，说"做个科技感但克制的落地页"自动触发。

**本站就用它**：你现在看到的暗色首屏、编号版块、发丝线分隔，都来自这套规范。

📄 [在线演示](https://commitbyryan.github.io/quiet-tech-style/)｜[仓库](https://github.com/CommitByRyan/quiet-tech-style)

---

## 五、课程项目

这组是我在校课程作业。**如实标注：它们是小组项目，不是个人作品。**

### online-cinema-ticket

> 在线点餐系统（O2O）：用户点餐、商家经营、平台管理三类角色。

| | |
| --- | --- |
| 技术栈 | JDK 17、Spring Boot 2.7、MyBatis、Thymeleaf、MySQL、Maven |
| 规模 | 128 个文件，含 GitHub Actions CI |
| 状态 | 课程结项 |

**功能**：用户端（注册登录、菜品分页搜索、购物车、模拟支付、订单多状态查询）；商家端（入驻审核、店铺与菜品管理、接单→制作→完成）；管理端（审核封禁、经营统计）。

**系统能力**：15 分钟未支付自动取消的定时任务、BCrypt 密码加密、统一异常处理与 JSON 返回、PageHelper 分页。

> **仓库名与内容不一致**：`online-cinema-ticket` 是初期的电影票项目名，需求后来改成点餐，仓库名沿用至今。README 开头有显式说明——这类历史包袱不藏，写清楚比改名安全。

### parking-lot-management-system

> 停车场管理系统：车辆进出、车位监控、收费结算。

| | |
| --- | --- |
| 技术栈 | JSP + Servlet + MySQL + Tomcat 8，MVC 架构 |
| 规模 | 68 个文件，六人小组 |
| 状态 | 课程结项 |

覆盖用户端（登录、出入口、车位可视化）与管理端（车位、IC 卡、临时车辆、固定车主、角色、用户管理 + 报表）。

仓库 README 里保留了完整的**分工表**——六个人各自负责哪块写得清清楚楚。小组作业该这样留痕。

### ParkingWeb

> 停车场项目的材料归档：项目汇报 PPT 与分散考核报告。

| | |
| --- | --- |
| 内容 | `.pptx` 汇报（含界面截图）、`.docx` 考核报告、IDEA 模块配置 |
| 状态 | 归档 |

代码在 [parking-lot-management-system](https://github.com/CommitByRyan/parking-lot-management-system)，这里是交付材料。分开存是为了让代码仓库保持干净——11 MB 的二进制文档不该和源码混在一起。

---

## 关于这些仓库的几条约定

**许可**：各仓库以自身 `LICENSE` 为准。课程项目 `online-cinema-ticket` 使用 Apache-2.0；`agent-craft` 当前保留所有权利。

**课程项目的边界**：小组作业标注团队规模与分工，不写成个人作品。代码能公开是因为课程允许，但署名权属于每个人。

**状态定义**：

| 状态 | 含义 |
| --- | --- |
| 可用 | 有真实使用场景，接口稳定 |
| 维护中 | 还在改，可能有不兼容变更 |
| 结项 | 课程已结束，代码保留可跑，不再加功能 |
| 归档 | 仅存材料，不再更新 |

**不再维护的仓库我不删。** 它们记录了当时的技术判断，包括做错的那些——比如给一次课程作业上完整 CI，当时觉得是加分项，现在看是把时间花在了不会被评分的地方。

---

想按主题读技术细节的，去 [文章列表](README.md)；想看具体某个项目的实现，点上面的仓库链接。
