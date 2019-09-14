---
title: yarn 常见命令
date: 2019-06-16 21:31:01
tags:
- yarn
categories:
- [node]
---

记录一下yarn常见命令，不至于在看到人家博客了使用，不知道什么意思，至于开发使用，npm 足够了。

<!--more-->

## 常用命令：

```bash
yarn init #初始化package.json

yarn add packge 添加依赖 默认开发依赖
yarn add package@version
yarn add package@tag
yarn # 安装项目全部依赖

yarn add package --dev 安装开发依赖
yarn add package --peer
yarn add package --optional

yarn upgrade packge

yarn remove package
```