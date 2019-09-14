---
title: html5 新增标签及新写法
tags:
- html5
categories:
- [html]
---

之前对HTML5的知识只是看过了解过，并没有做相关记录。今天开始记录一下H5的相关知识。

新增元素

- 主体结构元素：nav、section、article、aside、time。
- 非主体结构元素：header、hgroup、footer、address。

<!--more-->
***

@[TOC]

## 主体结构元素

### nav 
导航部分，使用频率高：
- 页面导航链接组；
- 侧边栏导航

```html
<nav>
	<ul>
		<li><a href="https://baidu.com">百度</a></li>
		<li><a href="https://google.com">谷歌</a></li>
	</ul>
<nav>
```
### section
部分，使用频率较低：
- 页面独立分块，往往是文章的章节；
- 通常由内容和标题组成，没有标题和内容不推荐使用section；
- **需要添加样式或者脚本行为时，使用div，不要使用section；**
- 可与 article 相互嵌套。
```html
<section>
	<h1>水果<h1>
	<article>
		<h2>苹果<h2>
		<div>苹果是啥?<div>
	</article>
	<article>
		<h2>桔子</h2>
		<div>桔子是是啥</div>
	</article>
</section>
<article>
	<h1>中国人物</h1>
	<section>
		<h2>三国</h2>
		<p>猛将</p>
	</section>
	<section>
		<h2>两晋</h2>
		<p>猛将</p>
	</section>
</article>
```
### article
文章、文档，使用频率极高，强调独立性：
- 一篇文章、一个页面、一个独立完整的内容部分；
- 一般会带标题，并放在header中；
- article 可相互嵌套。
```html
<article>
	<header>
		<h1>我是标题</h1>
	</header>
	<p>我是段落</p>
	<article>
		<div>我是内容</div>
	</article>
</article>
```
### aside
旁边、侧边，使用频率低：
- 在article中使用，作为主要内容的附属信息，比如参考资料、名词解释；
- 在article外使用，作为页面或者全局站点的附属信息，比如侧边栏、友情链接、广告等。

```html
<article>
	<h1>马云是谁</h1>
	<p>马云，男，1964年10月出生在浙江杭州，世界著名企业家……</p>
	<aside>
		<h1>参考：</h1>
		<p>维基百科</p>
	</aside>
</article>
```
###  time
时间，使用频率低：
- 某个时刻或者日期；
- 时刻允许带的时间差；
- 可定义多种格式的日期和时间。
```html
<time datetime="2019-05-12">2019年5月12日</time>
<time datetime="2019-05-12T15:49">2019年5月12日15:49</time>
<!--Z-UTC时间-->
<time datetime="2019-05-12T20:00Z">2019年5月12日20:00</time>
```

## 非主体标签

### header
语义上看作文档的页眉，使用频率极高：
- 具有引导和导航作用的元素，比如 h1,h2;
- 房子整个页面或者页面的一个内容区块的标题；
- 一个页面中没有限制header的数量。

```html
<header>
	<h1>我头部</h1>
</header>
<article>
	<header>
		<h1>我是文章头部</h1>
	</header>
	<p>我是身体</p>
</article>
```
### hgroup
标题组，使用频率高：
- 作为header的子元素；
- 使用了主标题h1和至少一个子标题才用hgroup;
- 通常会将h1-h6分组。

```html
<article>
	<header>
		<hgroup>
			<h1>我是主标题</h1>
			<h2>是子标题</h2>
			<h3>我是子标题</h3>
		</hgroup>
	</header>
	<p>内容</p>
</article>
```

### footer
脚注，使用频率高：
- 一个内容区块的脚注；
- 通常的内容是联系信息、相关阅读、版权信息等。

```html
<article>
	<p>吕布惊呆了</p>
	<footer>
		<ul>
			<li>关于三国</li>
			<li>地图信息</li>
		</ul>
	</footer>
</article>
```
### address
地址信息，使用频率低，html4就有了：
- 文档作者的联系信息；
- 内容通常是作者、网站链接、电子邮件、地址、电话等。

```html
<address>
	<a href="">作者：张三丰</a>
	<a href="">地址：武当山</a>
	<a href="">联系电话：112119</a>
</address>
```
## 新写法
文档类型定义
作用：告诉浏览器解析文档的规范，必需的声明。
```html
<!DOCTYPE html>
```
根元素
```html
<html lang="zh-CN>
```
字符编码
```html
<meta charset="utf-8">
<!--HTML4--->
< meta http-equiv="content-type" content="text/html;charset=utf-8">
```
js和css可省略文档类型声明
```html
<link rel="stylesheet" href="style.css">
<!--标准的js文件可省略type-->
<script src="main.js"></script>
<!--HTML4-->
<link rel="stylesheet" href="style.css" type="text/css">
<script  src="main.js" type="text/javascript"></script>
```
一份html5模板
```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>HTML5文档模板</title>
</head>

<body>
  <!-- 页面头部 -->
  <header>
    <h1>My Site</h1>
    <!-- 导航栏 -->
    <nav>
      <ul>
        <li> ... </li>
        <li> ... </li>
      </ul>
    </nav>
  </header>

  <!-- 单篇文章 -->
  <article>
    <header>
      <time datetime="2018-12-10">2018年12月10日</time>
      <hgroup>
        <h1>Hello, World!</h1>
        <h2>hello html5</h2>
      </hgroup>
    </header>
    <p>Lorem ipsum ...</p>
    <section>
      <p>文章的部分章节</p>
    </section>
  </article>

  <!-- 页脚 -->
  <footer>
    <address>
      <ul>
        <li>联系电话：183151</li>
        <li>通讯地址：中国，四川，成都，高新……</li>
      </ul>
      <p class="copyright">Copyright &copy; 2013</p>
    </address>
  </footer>
</body>

</html>
```