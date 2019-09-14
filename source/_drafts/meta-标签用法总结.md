---
title: meta 标签用法总结
tags:
- html
categories:
- [html]
---

一直很少关注该标签，今天就要总结一下。
模拟HTTP响应头和指定文档元数据，这些数据是其他元素`<base><link><script><style><title>`不能指定的。meta 标签不会出现在页面上，但可以被浏览器和服务器识别。
元数据：文档作者、描述、关键词、文档字符编码、视口大小等。

<!-- more -->
***

@[TOC]

## 属性
`content、http-equiv、name、charset`。

- charset：指定文档编码。比如`UTF-8`，只能有一个具有该属性的meta。
- http-equiv：用于模拟HTTP响应头。
- name：元数据名字。
- content：元数据的值。

## 用法
`charset`、`http-equiv`、`name`三个属性不能同时使用。`content` 必须和 `name` 或者 `http-equiv` 配对使用。

## name的取值
|name|描述|备注|
|:--|:---|:---|
|application-name|web应用名字|网页不是web应用时，name不能取application-name(什么是web应用)|
|author|文档作者||
|description|文档描述|最多一个，有利于seo|
|keywords|文档关键词，利于seo||
|referrer|跳转页面的策略|content属性值对应于从当前页面跳转到其他页面时，http请求的referrer策略|
|generator|生成文档的工具|一般不设置|

以上是HTML5的标准值，还可以设置其他非标准值：
```html
<meta name="viewport" content="width=device-width">
```
### referrer
|referrer|content|响应头referer|
|:---|:----|:---|
|no-referrer|never|删除Referer|
|no-referrer-when-downgrade|default|如果当前页面使用的是https协议，跳转的页面使用的是http协议，则取值为空。如果当前协议与跳转页面使用的协议一致，则取值为当前页面url的协议、域名、请求参数这三个值拼成的uri|
|unsafe-url|always|当前页面url的协议、域名、请求参数拼成的uri|
|origin-when-cross-origin|origin-when-crossorigin|跨域时取值与Origin字段一致，不是当前页面的完整url，只是协议和域名拼成的uri|

[Referrer Policy 介绍](https://imququ.com/post/referrer-policy.html)
[关于meta的referer属性](https://951759534.github.io/2017/06/06/%E5%85%B3%E4%BA%8Emeta%E7%9A%84referer%E5%B1%9E%E6%80%A7/)

### referrer VS origin  VS host VS Remote Address

origin 指定请求来自哪个站点，跨域时使用：
```js 
origin: ""
origin: <scheme> "://" <host> [ ":" <port> ]
//host是域名或IP
origin: https://developer.mozilla.org
```
host 指定请求域名
http1.1必须设置，http1.0 可省略。

```js 
host: <host>:<port>
host: developer.cdn.mozilla.net
```
Remote Address 是一个响应头，是服务器的`ip:port`。
referer 用于指定请求的源页面。

## http-equiv

五个取值：
|值|描述|备注|
|:--|:--|:--|
|~~content-language~~|文档语言类型|使用 html的lang 代替|
|~~content-type~~|文档编码方式|charset 代替|
|refresh|页面自动刷新的时间间隔||
|set-cookie|content用于设置网页cookie|HTML5不推荐使用|
|default-style|content是文档预定义样式||



例子：
```html
<meta http-equiv="refresh" content="1"> 
<!--每隔1秒刷新页面-->
<meta http-equiv="refresh" content="1;url=baidu.com"> 
<!--1秒后重定向到百度-->
<meta charset="utf-8">
```
## 移动端常用meta
```html
<!DOCTYPE html> <!-- 使用 HTML5 doctype，不区分大小写 -->
<html lang="zh-CN"> <!-- 更加标准的 lang 属性写法 http://zhi.hu/XyIa -->
<head>
    <!-- 声明文档使用的字符编码 -->
    <meta charset=`utf-8`>
    <!-- 优先使用 IE 最新版本和 Chrome -->
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"/>
    <!-- 页面描述 -->
    <meta name="description" content="不超过150个字符"/>
    <!-- 页面关键词 -->
    <meta name="keywords" content="关键词1，关键词2"/>
    <!-- 网页作者 -->
    <meta name="author" content="name, email@gmail.com"/>
    <!-- 搜索引擎抓取 -->
    <meta name="robots" content="index,follow"/>
    <!-- 为移动设备添加 viewport -->
    <meta name="viewport" content="initial-scale=1, maximum-scale=3, minimum-scale=1, user-scalable=no">
    <!-- `width=device-width` 会导致 iPhone 5 添加到主屏后以 WebApp 全屏模式打开页面时出现黑边 http://bigc.at/ios-webapp-viewport-meta.orz -->
 
    <!-- iOS 设备 begin -->
    <meta name="apple-mobile-web-app-title" content="标题">
    <!-- 添加到主屏后的标题（iOS 6 新增） -->
    <meta name="apple-mobile-web-app-capable" content="yes"/>
    <!-- 是否启用 WebApp 全屏模式，删除苹果默认的工具栏和菜单栏 -->
 
    <meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL">
    <!-- 添加智能 App 广告条 Smart App Banner（iOS 6+ Safari） -->
    <meta name="apple-mobile-web-app-status-bar-style" content="black"/>
    <!-- 设置苹果工具栏颜色 -->
    <meta name="format-detection" content="telphone=no, email=no"/>
    <!-- 忽略页面中的数字识别为电话，忽略email识别 -->
    <!-- 启用360浏览器的极速模式(webkit) -->
    <meta name="renderer" content="webkit">
    <!-- 避免IE使用兼容模式 -->
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <!-- 不让百度转码 -->
    <meta http-equiv="Cache-Control" content="no-siteapp" />
    <!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
    <meta name="HandheldFriendly" content="true">
    <!-- 微软的老式浏览器 -->
    <meta name="MobileOptimized" content="320">
    <!-- uc强制竖屏 -->
    <meta name="screen-orientation" content="portrait">
    <!-- QQ强制竖屏 -->
    <meta name="x5-orientation" content="portrait">
    <!-- UC强制全屏 -->
    <meta name="full-screen" content="yes">
    <!-- QQ强制全屏 -->
    <meta name="x5-fullscreen" content="true">
    <!-- UC应用模式 -->
    <meta name="browsermode" content="application">
    <!-- QQ应用模式 -->
    <meta name="x5-page-mode" content="app">
    <!-- windows phone 点击无高光 -->
    <meta name="msapplication-tap-highlight" content="no">
    <!-- iOS 图标 begin -->
    <link rel="apple-touch-icon-precomposed" href="/apple-touch-icon-57x57-precomposed.png"/>
    <!-- iPhone 和 iTouch，默认 57x57 像素，必须有 -->
    <link rel="apple-touch-icon-precomposed" sizes="114x114" href="/apple-touch-icon-114x114-precomposed.png"/>
    <!-- Retina iPhone 和 Retina iTouch，114x114 像素，可以没有，但推荐有 -->
    <link rel="apple-touch-icon-precomposed" sizes="144x144" href="/apple-touch-icon-144x144-precomposed.png"/>
    <!-- Retina iPad，144x144 像素，可以没有，但推荐有 -->
    <!-- iOS 图标 end -->
 
    <!-- iOS 启动画面 begin -->
    <link rel="apple-touch-startup-image" sizes="768x1004" href="/splash-screen-768x1004.png"/>
    <!-- iPad 竖屏 768 x 1004（标准分辨率） -->
    <link rel="apple-touch-startup-image" sizes="1536x2008" href="/splash-screen-1536x2008.png"/>
    <!-- iPad 竖屏 1536x2008（Retina） -->
    <link rel="apple-touch-startup-image" sizes="1024x748" href="/Default-Portrait-1024x748.png"/>
    <!-- iPad 横屏 1024x748（标准分辨率） -->
    <link rel="apple-touch-startup-image" sizes="2048x1496" href="/splash-screen-2048x1496.png"/>
    <!-- iPad 横屏 2048x1496（Retina） -->
 
    <link rel="apple-touch-startup-image" href="/splash-screen-320x480.png"/>
    <!-- iPhone/iPod Touch 竖屏 320x480 (标准分辨率) -->
    <link rel="apple-touch-startup-image" sizes="640x960" href="/splash-screen-640x960.png"/>
    <!-- iPhone/iPod Touch 竖屏 640x960 (Retina) -->
    <link rel="apple-touch-startup-image" sizes="640x1136" href="/splash-screen-640x1136.png"/>
    <!-- iPhone 5/iPod Touch 5 竖屏 640x1136 (Retina) -->
    <!-- iOS 启动画面 end -->
 
    <!-- iOS 设备 end -->
    <meta name="msapplication-TileColor" content="#000"/>
    <!-- Windows 8 磁贴颜色 -->
    <meta name="msapplication-TileImage" content="icon.png"/>
    <!-- Windows 8 磁贴图标 -->
 
    <link rel="alternate" type="application/rss+xml" title="RSS" href="/rss.xml"/>
    <!-- 添加 RSS 订阅 -->
    <link rel="shortcut icon" type="image/ico" href="/favicon.ico"/>
    <!-- 添加 favicon icon -->

    <!-- sns 社交标签 begin -->
    <!-- 参考微博API -->
    <meta property="og:type" content="类型" />
    <meta property="og:url" content="URL地址" />
    <meta property="og:title" content="标题" />
    <meta property="og:image" content="图片" />
    <meta property="og:description" content="描述" />
    <!-- sns 社交标签 end -->
 
    <title>标题</title>
</head
```

## 参考
[Meta元素](https://www.wikiwand.com/zh/Meta%E5%85%83%E7%B4%A0)
[HTML:5meta标签](https://flycode.co/archives/243440)
[meta标签学习](https://blog.yanfangyao.guru/notes/20180717214533/)
[关于meta的referer属性](https://951759534.github.io/2017/06/06/%E5%85%B3%E4%BA%8Emeta%E7%9A%84referer%E5%B1%9E%E6%80%A7/)
[HTML元信息类标签](<https://time.geekbang.org/column/article/82711>)


