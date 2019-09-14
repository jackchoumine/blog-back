---
title: 如何用对html标签
tags:
- html
categories:
- [html]
---

html 标签具有语义化，用对比不用好，不用比乱用好。用对的好处：

- 对机器友好：利于搜索引擎检索外，还能给读屏软件提供方便，使得残障人士更好地使用；
- 对人友好：文档结构清晰，增加可读性，方便团队合作和维护；
- 对自然语言的补充。

<!--more-->
***
@[TOC]

## 写语义化的标签应注意什么？

1. 尽量少用没有语义的标签`div`和`span`;
2. 不用纯样式标签，比如`b`、`font`等，`strong`默认加粗，`em`是斜体。
3. 使用表格时，标题使用`caption`，表头`thead`，主体`tbody`，尾部使用`tfoot`;
4. 表单域使用`fieldset`包裹，并用`legend`说明表单用途，`input`和`label`配合使用。

## 常见的语义化标签

- hN，标题标签，重要性依次降低，一个页面往往只允许一个`h1`，但是`h1`可嵌套在`article`内使用；多个`section`嵌套`h1`，会降级；
- p 段落，默认上下有间距；
- `ul`、`ol`、`li`类表元素；

## 常见的容易用混的标签

|            | em   | i    | strong | b    |
| ---------- | ---- | ---- | ------ | ---- |
| 是否有语义 | 重读 | 无   | 强调   | 无   |
| 样式       | 斜体 | 斜体 | 加粗   | 加粗 |
|            |      |      |        |      |

## 参考
- [对HTML语义化的一些理解和记录](https://juejin.im/post/5ae029bcf265da0b7155f15d)

- [HTML em tag](https://www.w3docs.com/learn-html/html-em-tag.html)