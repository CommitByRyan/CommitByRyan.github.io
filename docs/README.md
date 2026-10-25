# Ryan 的技术笔记

> 把做过的事拆开写清楚：为什么这么做、踩了什么坑、最后跑出什么数。

这里不写「十分钟入门」式的整理稿。每篇都对应一个真实落地的项目，代码能跑、数据可复现、坑是真踩过的。

## 最近在做什么

**徽写 V1.0** —— 一套把长篇小说拆成可执行管线的写作工作流。

起因很具体：连载写到中后期，模型开始记错事——主角的伤好了又被写成没好，前文交出去的钥匙下一章还在兜里。查下来不是模型记性差，是**状态没有唯一出口**：时间线在摘要里、人物在设定库、道具在正文、伏笔在另一份账本，每写一章都要重新拼一次。

徽写把这四件事压成三层：静态设定、动态状态、生成视图。配套做了上下文硬预算（单章 ≤ 12,000 字符）、节拍器（正文前先出 3–4 个冲突节点）、结构化闸门（退出码说话，不靠肉眼）。

实测把单章上下文从 8.7 万字符压到 4 千，降幅 95.3%，未触发降级。代码在 [huixie](https://github.com/CommitByRyan/huixie)，MIT。

## 文章

| # | 标题 | 一句话 |
| --- | --- | --- |
| 01 | [搭建技术博客：Docsify + GitHub Pages](_posts/01-build-blog-docsify.md) | 不用构建工具，改 Markdown 即改站点 |
| 02 | [为自己的技术能力打造门户](_posts/02-deploy-navigation-site.md) | 个人导航站从想法到上线的部署与踩坑 |
| 03 | [给番茄作家后台写一个 MCP 服务](_posts/03-fanqie-writer-mcp.md) | 零依赖 Node 实现，双传输，17 个客户端一键注册 |
| 04 | [长篇小说的状态机：静态设定与动态状态分离](_posts/04-huixie-state-machine.md) | 伏笔四态、相关度打分、为什么单个布尔位不够用 |
| 05 | [单章上下文 12k 硬预算](_posts/05-huixie-context-budget.md) | 从 8.7 万字符压到 4 千，且关键信息一条不少 |
| 06 | [给 AI 工作流写测试](_posts/06-huixie-testable-workflow.md) | 闸门 fail-open、41 项断言、品牌门禁抓出我自己的泄漏 |

## 项目

| 项目 | 是什么 |
| --- | --- |
| [huixie](https://github.com/CommitByRyan/huixie) | 长篇写作工作流：状态双层、上下文预算、节拍器、写回钩子、发布件标准化 |
| [fanqie-writer-mcp](https://github.com/CommitByRyan/fanqie-writer-mcp) | 番茄作家后台的 MCP 服务，让 AI 客户端直接管章节 |
| [Ryan-links](https://github.com/CommitByRyan/Ryan-links) | 个人导航页，Vue 3 单页 |
| [parking-lot-management-system](https://github.com/CommitByRyan/parking-lot-management-system) | 课程项目：JSP 停车场管理系统 |
| [online-cinema-ticket](https://github.com/CommitByRyan/online-cinema-ticket) | 课程项目：在线点餐系统，含 CI |

## 关于

计算机专业在读。日常在写代码、写小说、把两者的流程互相借。

写作上的经验（状态管理、一致性校验、可复现的验收）拿去改软件工程，反过来也一样——**长篇小说的连贯性问题，本质是一个数据一致性问题**。

## 本站

Docsify + GitHub Pages，无构建步骤，源码即内容。支持全文搜索（右上角）、侧边栏目录、代码高亮、阅读进度条。

本地预览：

```bash
npm i docsify-cli -g
docsify serve docs
```

想看我怎么做这个站的，见 [第 01 篇](_posts/01-build-blog-docsify.md)。
