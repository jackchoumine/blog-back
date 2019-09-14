---
title: web worker 实现浏览器多线程
date: 2019-06-15 20:19:31
tags:
- html5
categories:
- [html5]
---
JS 天然属于单线程环境，也就是说不能同时运行多个脚本。为何这样？因为JS的设计目标是实现网页和用户交互，试想当用户点击页面，操作 DOM 时，有两个线程同时操作了DOM，那么以谁的结果为准呢？最坏的情况一个线程需要给一个元素添加内容，另一个线程却把元素删除了，这将导致用户无法和页面交互。这就是JS只能单线程的原因，所有任务都在一个线程上执行，不必考虑多线程的问题。JS 需要同时执行两个任务的地方，可通过定时器、事件处理器等**异步技术**实现*并行*（其实依然单线程）。HTML5 引入 web worker 实现真正的多线程。通过 Web Worker 在**后台执行**一些操作，例如触发长时间运行的脚本以处理计算密集型任务，同时却不会阻碍 UI 或其他脚本处理用户互动。
Worker 利用类似线程的消息传递实现并行。这非常适合您确保对 UI 的刷新、性能以及对用户的响应。
<!-- more -->

## web worker 的类型
两种类型：
- 专用 worker（Dedicated workers）：只能在创建它的脚本中使用；
- 共享 worker（Shared workers）：可在多个脚本中使用。

worker 和主线程之间通信通过消息机制进行--使用`postMessage` 函数向对方传递数据，对方通过监听`message`事件获取并处理数据。数据不是共享，而是**复制**。所以传递对象时，可以操作该对象，而不会影响另一个线程中的对象。

worker 和主线程的脚本**必须同源**，简单理解成同一个网站加载到浏览中。

## 专用 worker

### worker 特性检测
在主线程检测浏览器是否支持 worker,在决定是否创建 worker。
```js
if(window.worker){
  //do something
}
```
目前主要浏览器都支持。[更多详情](https://caniuse.com/#feat=webworkers)

### 使用入门
web worker 在独立线程中运行，可将其代码保存在一个单独的JS文件中，然后在创建 worker 时引入该脚本。很多浏览器还不支持本地文件运行worker，我们需要结合服务器时间。可使用 express 创建一个服务器。

在 **public/javascripts** 文件夹在创建 worker.js：
```js
this.addEventListener('message', (event) => {
  console.log(this)
  console.log(event)
  console.log(event.data)
  console.log(event.origin)
  console.log(event.ports)
  console.log(event.source)
  console.log(event.lastEventId)
  self.postMessage({ name: 'worker.js', data: 'hello,main.js!' })
}, false)
```
这就是 worker 线程的代码。

在页面的 JS 代码中创建 worker，为了简单起见，在script 标签中写脚本：
```html
<script>
let worker = new Worker('/javascripts/worker.js');//创建一个 worker
let workerButton = document.getElementByI('workerButton');
  workerButton.addEventListener('click', (event)=> {
    console.log(event)
    worker.postMessage({ name: 'main.js', data:'hello,worker.js!' });//向 worker 线程发送消息
  }, false)
  // 监听 worker 线程发送过来的消息
  worker.addEventListener('message', (event) => {
    console.log(event.data);
 }, false)
</script>
```
创建 worker 时指定的脚本是异步加载的，如果加载成功，会生成一个 worker 线程。完全加载和执行之前，系统不好生成 worker。如果脚本不存在，返回404，创建 worker失败。

postMessage 用来启动 worker，也用来发送消息。

### 停止 worker 

在主线程中，通过`worker.terminate()` 停止 worker;在worker 线程中，使用`close()`来停止worker。

```js
let stopWorker = document.getElementById('stopWorker');
stopWorker.addEventListener('click', () => {
  let result = worker.terminate();
  console.log(result);
}, false);
```
worker 自己停止：

在主线程中通知worker停止：
```js
let stopWorker = document.getElementById('stopWorker');
stopWorker.addEventListener('click', () => {
  let result = worker.postMessage('stop');
  console.log(result);//undefined
}, false)
```
worker.js
```js
this.addEventListener('message', (event) => {
  console.log(this)
  console.log(event)
  console.log(event.data)
  console.log(event.origin)
  console.log('cache',this.cache)
  console.log(event.source)
  console.log(event.lastEventId)
  if ('stop' === event.data) {
    let result = this.close();
    console.log('worker stop ',result);//undefined
  }
  self.postMessage({ name: 'worker.js', data: 'hello,main.js!' })
}, false)
```

### worker 环境

worker 作用域：在worker脚本文件内，this 和 self 都是全局作用域。上面的`console.log(this)`的输出是：
```js
DedicatedWorkerGlobalScope。
```
worker 线程中并不能完成使用主线程的功能，只能使用部分JS功能：
- navigator 对象；
- location 对象；
- XMLHttpRequest 对象；
- 定时器；
- 应用缓存；
- importScripts();
- 生成其他 worker。

worker 中无法使用：
- DOM(非线程安全)；
- window 对象；
- document 对象；
- parent 对象。

我们在 worker.js 发发送一个http 请求，拿到返回值后传递给主线程：
```js


```
[更多详细情况](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Functions_and_classes_available_to_workers)

### 加载外 worker 脚本
可使用`importScripts` 函数加载外部的脚本进入 worker 脚本执行。

在 worker.js 记载一个脚本：

```js
importScripts('./importTest.js');// 可传递多个路径，路径是相对于 worker.js 的
```
importTest.js
```js
test(5)
function test(time) {
  setInterval(() => {
    console.log('你好');
  }, 1000 * time);
```
worker 线程停止后，加载的外部脚本也停止执行。

### 错误处理
可在主线程中处理 worker 线程的错误，在主线程中监听 worker 的 错误事件：
```js
worker.addEventListener('error', (event) => {
  console.log(event.colno);
  console.log(event.filename);
  console.log(event.message);
}, false);
```
输出该错误事件，可看到事件的一些属性：
不冒泡、可取消等，具有普通事件的一些属性。

worker 生成一个错误：
```js
this.addEventListener('message', (event) => {
  if ('stop' === event.data) {
    let result = this.close();
    console.log('worker stop ', result);
  }
  self.postMessage({ name: 'worker.js', data: 'hello,main.js!' })
  this.postMessage(new Error('制造一个错误！'));
}, false)
```
### 内嵌 worker

上面的例子，将 worker 代码放在单独的文件里，这样便于管理代码，也方便修改，因为有语法高亮，可以将 worker 代码放在和主线程相关的html文件里:

```html
<script id="worker" type="javascript/worker">
    this.addEventListener('message', (event) => {
      if ('stop' === event.data) {
        this.close();
      }
      console.log(event.data);
      self.postMessage({ name: 'worker.js', data: 'hello,main.js!' });
    }, false);
  </script>
  <script>
    let workerContent = document.getElementById('worker').textContent;
    let blob = new Blob([workerContent], { type: 'text/javacript' });
    let url = URL.createObjectURL(blob);
    console.log(url);
    let worker = new Worker(url);
    worker.addEventListener('message', (event) => {
      console.log(event.data);
    });
    let workerButton = document.querySelector('#workerButton');
    workerButton.addEventListener('click', (event) => {
      worker.postMessage({ name: "主线程" });
    }, false);
  </script>
```

将 worker 的代码在 script 标签中，声明类型为`javascript/worker`,就不会被浏览器解析成JS代码，而是当成普通的 html 标签，可通过DOM api 获取标签内的文本，创建 worker。
使用 Blob 生成一个blob 对象，再使用 URL.createObjectURL成连接，再生成 worker。
```js
let blob = new Blob([workerContent], { type: 'text/javacript' });
let url = URL.createObjectURL(blob);
console.log(url);
let worker = new Worker(url);
```
[关于Blob](https://www.javascripture.com/Blob)
[关于Blob和URL](https://javascript.info/blob)
### worker 的使用场景
常用的场景：
- 预先抓取和/或缓存数据以便稍后使用；
- 突出显示代码语法或其他实时文本格式；
- 拼写检查程序；
- 分析视频或音频数据；
- 背景 I/O 或网络服务轮询；
- 处理较大数组或超大 JSON 响应；
- \<canvas> 中的图片过滤；
- 更新本地网络数据库中的多行内容。

## 更多参考

[Web Workers 的基本信息](https://www.html5rocks.com/zh/tutorials/workers/basics/)

[使用 Web Workers](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers)

[worker读取文件](https://www.html5rocks.com/en/tutorials/file/filesystem-sync/)