# Ryan 的技术笔记

> 全栈实践与思考，记录技术变现之路。

本站是基于 [Docsify](https://docsify.js.org/) + GitHub Pages 搭建的个人技术博客，源码即页面内容——改 Markdown 即改网站。

**在线访问**：https://commitbyryan.github.io

## 内容

| 文章 | 说明 |
| --- | --- |
| [搭建我的技术博客：从 GitHub 加速开始](docs/README.md) | 用 Docsify + GitHub Pages 搭博客的完整步骤 |
| [大二，我为自己的技术能力打造门户](docs/_posts/02-deploy-navigation-site.md) | 个人导航站从想法到上线的部署与踩坑全记录 |

站内支持全文搜索（右上角搜索框）与阅读进度条。

## 目录结构

```
.
├─docs/                  # GitHub Pages 发布目录
│  ├─index.html          # Docsify 入口与配置（主题、侧边栏、搜索、分页）
│  ├─README.md           # 首页文章
│  ├─_sidebar.md         # 侧边栏目录
│  ├─_404.md             # 404 页面
│  ├─_posts/             # 文章正文
│  └─.nojekyll           # 禁用 Jekyll，交给 Pages 原生处理
├─LICENSE
└─README.md
```

## 本地预览

```bash
npm i docsify-cli -g
docsify serve docs
```

然后打开 http://localhost:3000 预览。

## 写新文章

在 `docs/_posts/` 下新建 Markdown 文件，然后在 `docs/_sidebar.md` 中加入条目：

```markdown
- **技术博客**
  - [开篇：搭建我的技术博客](README.md)
  - [导航站部署全记录](_posts/02-deploy-navigation-site.md)
  - [新文章标题](_posts/03-your-post.md)
```

## 部署

站点通过 GitHub Pages 发布，配置为 **main 分支 / docs 目录**：

1. 推送到 `main` 分支；
2. 仓库 Settings → Pages → Source 选择 `main` / `/docs`；
3. 等待 1–2 分钟生效。

## License

MIT
