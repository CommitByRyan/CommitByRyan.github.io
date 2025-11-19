# 《搭建我的技术博客：从GitHub加速开始》

> #自己有加速器的可以直接跳过步骤1!
>
> 最近我用Docsify+GitHub Pages搭好了自己的技术博客，过程很顺畅，分享下步骤：

## 1. 准备工具：加速访问GitHub
先下载了Watt Toolkit（原Steam++），打开后勾选“GitHub加速”，这样后续克隆仓库、推送代码都快了很多。


## 2. 安装Node.js
从Node.js官网下载了对应系统的安装包，一路默认安装(可以根据个人意愿更改路径)，最后在终端输入`node -v`能显示版本号  如(v24.11.1)，说明安装成功。


## 3. 用Docsify初始化博客
打开终端，先选个文件夹当博客目录，然后执行：
```bash
npm i docsify-cli -g  # 全局安装Docsify
docsify init docs     # 在当前目录创建docs文件夹并初始化
```
执行完后，`docs`文件夹里出现了`index.html`、`README.md`这些核心文件。


## 4. 本地预览博客
在终端输入`docsify serve docs`，浏览器打开`http://localhost:3000`，就能看到博客的本地预览效果了。


## 5. 部署到GitHub Pages
把本地的`docs`文件夹推到GitHub仓库，

第一步推送好像就有点难倒各位了,全英文的网站会让编友无从下手,点击一个[uploading an existing file](本地推送),直接拖拽进入即可上传!!!

然后在仓库的“Settings-Pages”里，选择“main分支 /docs文件夹”，保存后等1-2分钟，访问`https://我的用户名.github.io`，博客就正式上线啦！


整个过程基本没踩坑，唯一注意的是：推代码前要确保Watt Toolkit的GitHub加速是开启的，不然可能会卡顿~
