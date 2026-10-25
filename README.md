# Ryan 的技术笔记

> 把做过的事拆开写清楚：为什么这么做、踩了什么坑、最后跑出什么数。

在线访问：**https://commitbyryan.github.io**

基于 [Docsify](https://docsify.js.org/) + GitHub Pages 搭建。无构建步骤，改 Markdown 即改站点，源码就是页面内容。

## 文章

| # | 标题 | 主题 |
| --- | --- | --- |
| 01 | [搭建技术博客：Docsify + GitHub Pages](docs/_posts/01-build-blog-docsify.md) | 站点搭建、方案对比、五个坑 |
| 02 | [大二，我为自己的技术能力打造门户](docs/_posts/02-deploy-navigation-site.md) | 个人导航站从想法到上线 |
| 03 | [给番茄作家后台写一个 MCP 服务](docs/_posts/03-fanqie-writer-mcp.md) | 零依赖 Node、双传输、17 客户端注册 |
| 04 | [长篇小说的状态机](docs/_posts/04-huixie-state-machine.md) | 静态设定与动态状态分离、伏笔四态 |
| 05 | [单章上下文 12k 硬预算](docs/_posts/05-huixie-context-budget.md) | 从 8.7 万字符压到 4 千 |
| 06 | [给 AI 工作流写测试](docs/_posts/06-huixie-testable-workflow.md) | 闸门 fail-open、41 项断言、品牌门禁 |

## 项目

全部公开仓库的汇总页：[docs/projects.md](docs/projects.md)

按用途分为写作工具链（huixie、fanqie-writer-mcp、github-publish-guardrails）、个人站点（本站、Ryan-links）、课程项目（点餐系统、停车场管理系统、材料归档），每条标注技术栈、规模与状态。课程项目如实标注小组规模，不写成个人作品。

## 目录结构

```
.
├─docs/                          # GitHub Pages 发布目录
│  ├─index.html                  # Docsify 入口与配置
│  ├─README.md                   # 首页（站点门面 + 索引）
│  ├─projects.md                 # 项目总览（全部公开仓库）
│  ├─_sidebar.md                 # 侧边栏目录（分组）
│  ├─_404.md                     # 404 页面
│  ├─assets/theme.css            # 首页品牌化主题（暗色首屏、数字条、滚动入场）
│  ├─_posts/                     # 文章正文，按编号排序
│  │  ├─01-build-blog-docsify.md
│  │  ├─02-deploy-navigation-site.md
│  │  ├─03-fanqie-writer-mcp.md
│  │  ├─04-huixie-state-machine.md
│  │  ├─05-huixie-context-budget.md
│  │  └─06-huixie-testable-workflow.md
│  └─.nojekyll                   # 禁用 Jekyll，否则 _posts/_sidebar 会被忽略
├─LICENSE
└─README.md
```

## 本地预览

```bash
npm i docsify-cli -g
docsify serve docs
```

打开 http://localhost:3000。改 Markdown 保存即生效，无需重启。

## 写新文章

1. 在 `docs/_posts/` 新建 `NN-slug.md`（编号连续，文件名用英文）；
2. 在 `docs/_sidebar.md` 对应分组下加一行；
3. 在 `docs/README.md` 的文章表和本文件的文章表各加一行；
4. 文末加「上一篇 / 下一篇」互链。

```markdown
- **徽写 · 长篇写作工作流**
  - [04 长篇小说的状态机](_posts/04-huixie-state-machine.md)
  - [07 新文章标题](_posts/07-your-post.md)
```

## 部署

GitHub Pages，配置为 **main 分支 / docs 目录**：

1. 推送到 `main`；
2. 仓库 Settings → Pages → Source 选 `main`、目录选 `/docs`；
3. 等 1–2 分钟生效。

## 约定

- 首页只当门面：介绍 + 文章索引 + 项目索引，正文一律放 `_posts/`；
- 文件名统一 `NN-english-slug.md`，标题写中文；
- 涉及真实项目的数据做脱敏，只保留结构与量级；
- 不放「十分钟入门」式整理稿：每篇对应一个真实落地的项目。

## License

MIT
