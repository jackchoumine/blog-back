---
title: Fetch API 入门使用
date: 2019-06-09 18:30:47
tags:
- fetch
- html5
categories:
- [网络]
- [html5]
---
html5 引入了新的网络请求接口`Fetch API`，原生支持Promise，可配合 async/await 语法使用。

<!--more-->

## Fetch 实例

使用 fetch 用户数据列表。
```js
fetch('/users')
.then(res=>res.json())//服务器返回的是json
.then(user=>{console.log(user);})
.catch(error=>{console.log(error);})
```
async/await 语法
```js
async function getUsers() {
  try {
    let res = await fetch('/users');
    let users = await res.json();
    console.log(users)
  } catch (error) {
   console.log(error)
  }
}
```
fetch 返回的不是真正需要的数据，而是一个 `Promise`，所有还需要使用它提供的方法进一步获取想要的数据。

##  具体用法

两种调用方式：
```js
fetch(url,options)
fetch(req,options)
```
推荐使用第一种，一眼就可以看到url,更加直观。

options 是一个对象，可设置以下字段：
- method：请求方法，默认GET;
- headers：请头信息，可以是简单的对象，也可以是 `Headers` 的实例；
- body：发送数据。`Blod`、`bufferSource`、`FormData`、`URLSearchParams`、`USVstring`，GET、HEAD 没有body；
- mode：请求模式。
  - cors：跨域请求；
  - no-cors：只允许使用`GET`、`HEAD`、`POST`;
  - same-origin：同源请求；
  - navigate：支持页面导航（不太明白）。
- credentials：是否发送 `cookies`：
  - omit：不发送，默认；
  - same-origin：同源发送；
  - include：发送。
- cache：缓存策略：
  - default：请求之前检查缓存；
  - no-cache：有缓存，发送一个查询请求，缓存失效，再发送正常请求；
  - no-store：不检查缓存，直接请求；
  - reload：忽略缓存，拿到响应后，更新缓存；
  - force-cache：强制读取缓存，缓存过期，再发送正常请求；
  - only-if-cached：读取缓存，过期就报网络错误。 mode 设置为 `same-origin` 时有效。
- redirect：重定向时的处理方法：
  - follow：跟随；
  - error：报错；
  - manual：用户手动跟随。
- integrity：包含一个验证资源完整性的字符串。

## Headers 

Headers 用于构造请求头信息，构造函数接收一个对象，对象的`key-value`就是请求头的信息。

```js
let headers = new Headers(
  {
    'content-type':'text/plain',
    'content-length':data.toString().length
  }
);
headers.append('X-Custom-header','AnotherValue');//追加
headers.has('content-type');//true 查询
headers.get('content-type');//'text/plain' 获取
// headers.getAll('content-type');//['text/plain'] getAll 被移除了
headers.delete('content-type');//删除
headers.set('content-type','json');//重写
```

## Request

请求对象。可以新建一个，也可以从已有的对象中继承。

```js
let Url = '/users';
let req = new Request(Url,{method:'GET',headers})
// 扩展 request
let postReq= new Requset(req,{method:'POST'})
```

## Response

Response 实例是 fertch 处理完 promise 之后的返回的。也可以手动创建，在`servoceWorkers` 中才真实有用。
```js
let res = new Response(body,init)
```
body 可以是`Bolb`、`BufferSource`、`FormData`、`URLSearchParams`、`USVString` 这些值。

init 是一个对象，可包含以下字段：
- status：响应状态码；
- statusText：状态文本；
- headers：头部信息，普通对象或Headers 的实例。

response 的实例还有一些可读属性：
- ok：请求是否成功，状态码为 2xx 都为 `true` ；
- status：状态码；
- statusText：状态文本；
- bodyUesed：响应数据是否被用过；
- headers：头部信息；
- url：响应地址；
- type：响应类型：
  - basic：同源；
  - cors：跨域；
  - error：出错；
  - opaque：Request mode 设置为 `no-cors` 的响应。

response 有一些方法来 reslove 响应信息。
- json，解析响应信息为对象，resolve promise；
- text，…………字符串…………；
- bolb，转化响应Bold，resolve;
- formData,………………；
- arrayBuffer，…………。

## Fetch Vs AJAX

- 响应状态为404或5xx，都resolve，但是 `ok` 为false。只有当网络故障或者请求被阻止了，才会 `reject`；
- 默认情况不发送不接收cookies，需要设置；
- POST 的数据需要转为JSON;
- 不能设置超时和取消请求，可以通过`Promise.race`，了模拟两者；
- 文件上传和下载获取不到进度，可使用 response.body 是可读流，具有`getReader`，可根据这个来获取下载进度。；
- 不能直接获取到响应数据，需要调用响应方法，resolve 一下；

## 总结

- Fetch API 很底层，使用起来并不丝滑；
- Fetch 错误处理有点复杂；
- Fetch 不支持取消和超时设置；
- 上传和下载进度没有，使用可读流来获取，复杂性突然升高了。

还是使用原生的 xhr 和 axios 库来的爽快。

## 参考

- [Fetch: Download progress](https://javascript.info/fetch-progress)
- [Fetch: Abort](https://javascript.info/fetch-abort)
- [了解 Fetch API](https://aotu.io/notes/2017/04/10/fetch-API/index.html)
- [Fetch进阶指南](http://louiszhai.github.io/2016/11/02/fetch/)