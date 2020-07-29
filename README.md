# github + hexo 搭建博客

大学时搭建过 github 博客，但是后来没怎么弄了。感觉有点折腾，就没有继续维护了，今天再来重新搭建一个博客。本文记录搭建操作。

<!--more-->

## 预备知识：

- npm 命令
- git 命令
- github 常见操作

环境要求：

- node
- git

我的环境：

```bash
$ git version
git version 2.15.1.windows.2
$ node -v
v8.11.1
$ npm -v
6.4.0
```

## 安装 hexo

```bash
npm i -g hexo-cli
```

在某个文件夹内初始化 hexo 博客所需要的文件

```bash
mkdir myBlog  # /e/HexoPages 新建一个文件夹
cd myBlog
npm i # 安装npm 依赖
```

得到如下目录：

```bash
├── _config.yml # 网站的配置信息，您可以在此配置大部分的参数。
├── package.json
├── scaffolds # 模版文件夹
├── source  # 资源文件夹，除 _posts 文件，其他以下划线_开头的文件或者文件夹不会被编译打包到public文件夹
|   ├── _drafts # 草稿文件
|   └── _posts # 文章Markdowm文件
└── themes  # 主题文件夹
```

本地预览博客：

```bash
hexo s
```

打开`http://localhost:4000`即可看到效果。

![hexo默认主题](https://raw.githubusercontent.com/JackZhouMine/jack-picture/master/hexo%E9%BB%98%E8%AE%A4%E4%B8%BB%E9%A2%98.png 'hexo默认主题')

## 创建托管博客的仓库

仓库名字必须用`用户名.github.io`，需要公开。

## 部署本地博客到 gitbub

1. 修改`_config.yml`

打开 \_config.yml ，将 deploy 修改如下**注意空格**

```bash
deploy:
  type: git # 版本管理工具
  repo: # 仓库信息
    github: https://github.com/jackchoumine/jackchoumine.github.io
  branch: master # 使用分支
```

其他配置也可以修改，比如我将站点信息修改如下：

```bash
# Site
title:jackzhoumine
subtitle:
description:
keywords:
author: jackzhoumine
language:
timezone:
```

2. 安装 hexo-deployer-git

```bash
npm i -S hexo-deployer-git
```

3. 部署

```bash
hexo g -d
```

打开我的主页链接 `https://jackchoumine.github.io`，看到页面就部署成功了。

![部署成功](https://raw.githubusercontent.com/jackchoumine/jack-picture/master/myblog.png '部署成功的页面')

## 创建文章

1. 创建文章

执行`hexo new '文章标题'`，会在 source/\_posts 文件夹内新建一个 md 文件，就可在里面写文章了，当然也可以手动创建。

```bash
$ hexo new  first-note
INFO  Created: E:\HexoPages\myBlog\source\_posts\first-note.md
```

2. 预览效果

创建完成，执行以下命令,在本地预览效果：

```bash
hexo g
hexo s
```

3. 部署到线上

```bash
hexo clean # 清除缓存文件（db.json）和静态文件。更改后不生效，就需要运行该命令。
hexo g -d # 部署到线上
```

4. 创建草稿

可先创建草稿，想发布时，在发布。

```bash
hexo new draft "文章标题" # 会在 /source/-drafts 里生成草稿
hexo publish filename
```

## 给 markdown 文章加入图片

使用本地图片，markdown 笔记移动后，图片路径失效，免费的图床可能到倒闭，毕竟都是小公司。解决办法是将所有图片在一个 git 仓库里管理起来，托管在 github （没有巨硬这个财大气粗的土豪做后台，也倒不了）上，安全放心。
需要用到图片时，打开图片，然后在代码块左上方点 `Download` 按钮，打开的新标签里只有图片了，复制地址栏图片链接，类似`https://raw.githubusercontent.com/xxxx`到文章里，就可放心饮用了。

## 高级主题

可以为博客增加评论功能，方便与他人交流。增加访客统计等。

### 增加评论功能

yilia 主题配置开启就可。
疑问：为何 disqus 评论底部会显示其他博客的评论内容呢？

### 增加访客统计

使用不蒜子统计功能，免费好用。
打开 `themes/yilia/layout/_partial/footer.ejs`，
在 id 为`footer-info`的 div 后面增加以下内容：

```ejs
<div calss="count-span">
      <span id="busuanzi_container_site_pv">
        总访问量: <span id="busuanzi_value_site_pv"></span>|
      </span>
      <span id="busuanzi_container_site_uv">
        总访客: <span id="busuanzi_value_site_uv"></span>
      </span>
</div>
```

在 footer 顶部增加统计脚本：

```js
<script
	async
	src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"
></script>
```

部署即可看到效果了，可增加一些好看的样式。

### 增加 stackoverflow 技术媒体

yilia 主题没有引入 stackoverflow ，可自行增加。
Acfun 已经凉了，在 `themes\yilia\source\main.0cf68a.css`中搜索 acfun，替换成 stackoverflow，图标就不换了。

## 写在最后

发现一些网站，专门爬取别人写的文章，也不注明出处，该今后发表文章，都增加不限于打赏码、微信公众号等信息。

如有帮助，谢谢您请我喝可乐：

<img src="https://raw.githubusercontent.com/jackchoumine/jack-picture/master/alipay2.jpg"  title="支付宝扫一扫" width="50%" height="50%"><img src="https://raw.githubusercontent.com/jackchoumine/jack-picture/master/weixin2.jpg"  title="微信扫一扫" width="50%" height="50%">
