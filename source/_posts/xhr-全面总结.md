---
title: xhr 全面总结
date: 2019-06-16 17:30:54
tags:
- xhr
categories:
- [网络]
---
AJAX(Asynchronous JavaScript Xml)，异步的JS xml，以前客户端和服务器 通信，一般传递 xml，但是现在传递 `JSON`为主，ajax 能实现页面部分更新，使得页面性能高和体验良好。AJAX 是一种向服务器发送 HTTP 请求，实现与服务器交互的技术，已经是HTTP请的代名词，但是不只支持 http 协议，还支持 `ftp`、`file`协议。

<!-- more -->

## 一级xhr 二级 xhr

一级xhr或者老版本的xhr，有一些缺点：
- 只支持文本数据传输
- 无法读取和上传二进制文件
- 只用成功、失败、完成等状态，没进度信息
- 不能跨域

二级xhr，改进了这些问题：
- 可设置超时
- formData 对象管理表单数据
- 文件上传
- 可跨域
- 可获取二进制文件
- 可获取传输进度


##  XMLHttpRequest 对象

ajax 技术的核心是 XMLHttpRequest 对象，简称XHR，现在较为先进的浏览器都支持（低版本的IE可滚粗了）创建 xhr 对象，`new` 一个即可。
```js 
var xhr = new XMLHttpRequest();
```
### xhr 的用法


**通常步骤：**
第一步：`var xhr = new XMLHttpRequest()`---创建 xhr;
第二步：`xhr.open(METHOD,URL,isAsync)`---启动xhr;
第三步：`xhr.responseType = reposnseType`---设置返回值类型，默认 `''`,必须在返回值达到客户端前设置;
第四步：`xhr.onreadystatechange=handleFun`---监听请求状态变化；
第五步：`xhr.setRequestHeader('Content-Type','application/json;charset=utf-8')`---设置请求头，不需要设置，可省略，`必须在请求开启和发送之间设置`;
第六步：`xhr.send(data)`---发送请求。

完整的例子：
```js
var http = new XMLHttpRequest();
http.open('GET','/endpoint',true);
http.responseType = 'json';
http.onreaydstatechange = function(){
	if(http.readyState === 4){
		if(http.status === 200){
			 console.log(http.response);
		}else{
			console.error(http.response);
		}
	}
};
http.onerror = function(){
	 console.error(http.statusText);
}
http.send(null);
```
### 实例属性

#### readyState

只读属性，是一个`数字`，表示此时的 HTTP 请求状态。
|值|状态|其他|
|:---:|:----|:----:|
|0|xhr创建，`open`方法还未调用|
|1|`open`已调用，`send`未调用，此时可设置请求头|
|2|`send`已调用，且服务器返回的`头信息`和`状态码`已经收到|
|3|正在接收响应`body`，收到部分信息。|
|4|已收到服务返回即请求`完成`，可能失败，可能成功|

>0   UNSENT  open() has not been called yet.
1   OPENED  send() has been called.
2   HEADERS_RECEIVED    send() has been called, and headers and status are available.
3   LOADING Downloading; responseText holds partial data.
4   DONE    The operation is complete.
>

每当 `xhr`的状态发生变化，`readyState`属性就发生变化，就会触发`readyStateChange`事件，所以可在 readyStateChange 事件的监听函数里处理返回的请求信息。

#### onreadystatechange

事件，指向一个函数。执行`abort`方法，也会改变 readyState。

#### response

服务器返回的数据，只读，类型由 `responseType`决定。请求不成功或者数据不完整，值为	`null`。

#### responeType

可写属性，指定响应格式，在 `open`之后`send`之前，设置返回的数据类型。
|值|说明|
|:----|:----|
|''(空字符串)|默认值，等同于`text`,适用大多数情况。只是设置该值时，才可获取`responseText`|
|json|浏览器会自动调用`JSON.parse`解析JSON字符串，此时`response`是一个对象，**不是字符串**|
|blob|`Blob`对象，二进制对象，请求图片，下载文件等|
|document|`Document` 对象，文档对象，HTML/XML文档对象等，收到返回值后不用解析HTML字符串，直接进行DOM操作|
|arraybuffer|`ArrayBuffer`对象,二进制数组|

#### responseText

返回值类型是`String`，可用这个属性获取。也就是说 `responseType`设置`text`或者`''`，`response`和`responseText`是相同的。

#### responseXML

返回值时 HMTL 或者 XML 对象，可用该属性获取，只读。该属性生效的前提是，响应头信息 `Content-Type`为 `text/xml`或者`application/xml`,这要求，发送请求前，将`responseType`设置为`document`。如果响应头信息	`Content-Type`不为 `text/xml`或者`application/xml`，就需要手动调用`overrideMimeType('text/xml')`，强制进行XML解析。
```js
var http = new XMLHttpRequest();
http.open('GET','/',true);
http.responseType = 'document';
http.onload = function(){
	if(http.reaydState === 4 && http.status  === 200){
		console.log(http.responseXML);
	}
}
http.send(null);
```

#### responseURL

只读，返回数据的服务器的url。和 open 里的 url 不一定相同，不含锚点。

#### status statusText

`status`一个整数，表示 HTTP 的状态码，只读，发出请求前，为	0。
`statusText`是状态信息，只读，无返回状态信息，默认 `OK`。
|status|statusText|状态|
|:----:|:----:|:-----:|
|200|OK|成功返回|
|301|Moved Permanently|永久移动|
|302|Moved Temporarily|暂时移动|
|304|Not Modified|未修改|
|307|Temporary Redirect|暂时重定向|
|400|Bad request|无效请求|
|401|Unauthorized|未授权|
|403|Forbindden|禁止访问|
|404|Not Found|未找到|
|405|Method not allowed|方法不被允许|
|500|Internal server error|服务器内部出错|
|503|Service unavailable|服务不可用|

基本上 2xx和 304，返回正常。
```js
if(http.readyState ===4 ){
  if((http.status >= 200 && http.status < 300)||(http.status === 304)){
		//处理返回数据
	}else{
		//返回出错
	}
}
```

#### timeout  ontimeout

`timeout`属性是一个整数，表示多少`毫秒`后，如果请求还未返回，就会自动终止。触发`timeout`事件， 为 0 ，表示无时间限制，不是整数，会先转换，成数字，转换失败，则不生效。
`ontimeout`指向一个函数，`timeout`触发，就执行该函数。

```js
var http = new XMLHttpRequest();
http.ontimeout = function(){
	console.log('请求超时');
}
http.onload =  function(){
	if(http.readyState === 4 && http.status === 200){
		//正常返回
	}else{
		console.log(http.statusText);
	}
}
http.open('GET','/users/12132',true);
http.timeout = 1000*10;// 10 秒超时
http.send(null);
```
#### withCredentials 

一个布尔值，表示跨域请求时，用户信息（Cookie、认证的 http 头信息）是否会包含在请求中，默认`false`。同源请求不需要设置。为了使得该属性生效，必须返回头信息 `Access-Control-Allow-Credentials`且值为	`true`。
打开了，跨域会发送 cookie，还会设置Cookie。

#### upload 用 AJAX 发送文件

xhr 不仅可以发送请求，还可发送送文件。发送文件后，`upload`属性可得到一个对象，通过该对象，可监听该对象的各个事件得到上传状态。
显示上传进度：
```html
<progress min="0" max="100" value="0">0% complete</progress>
```
监听`upload`对象的`progress`事件，可得到上传进度：
```js
function uploadFile({blodOrFile=null,type='json'}){
	var http = new XMLHttpRequest();
	http.open('POST','/srever',true);
	http.onload = function(){};
	var progressBar = document.querySelector('progress');
	http.upload.onprogress = function(event){
		if(event.lengthComputable){
			progressBar.value = (event.load / event.total)*100;
			// 兼容老式浏览器
			progressBar.textContent = progressBar.value;
		}
	}
	http.send(blodOrFile);
}
uploadFile({blodOrFile:new Blod(['hello ajax'])})
```
### 事件属性

可监听 xhr 的以下事件：**书写顺序为触发顺序，监听这些事件，必须在send之前进行**
|属性|事件|何时触发|
|:-----:|:-----:|:-----|:-----:|
|onloadstart|loadstart|请求发出,在 `readyState == 1` 和 `readyState == 2`之间,onloadstart 默认传入 progressEvent 对象 |
|onprogress|progress|正在发送或者加载返回的数据,onprogress 默认传入progressEvent对象|
|onabort|abort|终止请求，比如调用`abort`，调用该方法，readyState 被设置为 0|
|onerror|error|请求`失败`|
|ontimeout|timeout|请求`超时`|
|onload|load|请求`成功`，以上四个事件互斥，触发0次或者1次|
|onloadend|loadend|请求`完成`,可能成功，可能失败。**abort**、**load**、**timeout**、**readtState=4**都会伴随着**loadend**,onloadend 默认传入 progressEvent 对象|
|onreadystatechange|readystatechange|xhr状态，监听该事件，可得知请求是否`完成`，这个属性时`xhr`特有的，其他属性时`xhr.upload`和`xhr`共有的|

```js
http.onload = function() {
 var responseText = xhr.responseText;
 console.log(responseText);
 // process the response.
};

http.onabort = function () {
  console.log('The request was aborted');
};
// `只有 onprogress 有一个事件参数`
http.onprogress = function (event) {
  console.log(event.loaded);//已返回的数据量
  console.log(event.total);//返回的总数据量
  console.log(event.lengthComputable);//加载进度是否可计算
};

function handleError() {
  console.log('There was an error!');
};

http.addEventListener('error', handleError);
```
### onload vs onreadyStateChange

处理返回值，是在 **onload**还是在**onreadyStateChange**?

onload 等于 `readyState==4 && xhr.status===200`

loadend 事件的触发：
```js
onreadystatechange
    readyState === 4
             ⇓
onload / onerror / onabort
             ⇓
        onloadend
```

使用 `onerror`并且不考虑老式浏览器，`推荐`用 `onload`而不用`onreadySateChange`。


### 实例方法

#### open

在`open`方法中指定请求参数，返回值为`undefined`。
`opend(METHOD,URL,isAsync,user,password)`,user 和 password 可选，统一请求，再次用`open`，就等同于调用`abort`。一般 URL 需要用 `encodeURI`或`encodeURICompent`编码。

#### GET 请求绕过缓存

GET 请求会缓存，在 url 后面加时间戳可绕过缓存。
```js
http://foo.com/bar.html -> http://foo.com/bar.html?12345
http://foo.com/bar.html?foobar=baz -> http://foo.com/bar.html?foobar=baz&12345
var oReq = new XMLHttpRequest();

oReq.open("GET", url + ((/\?/).test(url) ? "&" : "?") + (new Date()).getTime());
oReq.send(null);
```

#### send 

send 用于发送数据，可发发送多种类型的数据。
`String`、`Bold`、`Document`、`Formdata`、`ArrayBuffer`、`null`。

发送表单数据：
```html
<form id='registration' name='registration' action='/register'>
  <input type='text' name='username' value='张三'>
  <input type='email' name='email' value='zhangsan@example.com'>
  <input type='number' name='birthDate' value='1940'>
  <input type='submit' onclick='return sendForm(this.form);'>
</form>
```

```js
function sendForm(form) {
  var formData = new FormData(form);//对表单进行加工
  formData.append('csrf', 'e69a18d7db1286040586e6da1950128c');

  var xhr = new XMLHttpRequest();
  xhr.open('POST', form.action, true);
  xhr.onload = function() {
    // ...
  };
  xhr.send(formData);//发送

  return false;
}

var form = document.querySelector('#registration');
sendForm(form);
```

#### setRequestHeader(filed,value)

设置请求头信息，open 之后，send 之前调用。

#### overrideMimeType()

重新设置服务器返回的数据类型，必须在 send 之前调用。

#### getResponseHeader(filed):String

获取某个响应头信息，参数不区分大小写。

#### getResponseHeaders():String

获取所有响应头信息，每个头信息之间用 `\r\n`(换行)分割。
```js
date: Fri, 08 Dec 2017 21:04:30 GMT\r\n
content-encoding: gzip\r\n
x-content-type-options: nosniff\r\n
server: meinheld/0.6.1\r\n
x-frame-options: DENY\r\n
content-type: text/html; charset=utf-8\r\n
connection: keep-alive\r\n
strict-transport-security: max-age=63072000\r\n
vary: Cookie, Accept-Encoding\r\n
content-length: 6502\r\n
x-xss-protection: 1; mode=block\r\n
```
处理头信息：

```js
var arr = headers.trim().split(/[\r\n]+/);
var headerMap = {};

arr.forEach(function (line) {
  var parts = line.split(': ');//注意 '' 
  var header = parts.shift();//删除第一个元素，原数组改变，随便获取到 头字段。一举两得，妙
  var value = parts.join(': ');//\r\n 去哪儿了
  headerMap[header] = value;
});

headerMap['content-length'] // "6502"
```

#### abort

终止请求，使得 `readyState`为 4，`status`为 0。

## 页面卸载时发送数据

在页面卸载时发送数据，即在`unload`或者`beforeunload`的监听函数里，发送AJAX，但是不可靠。xhr 是异步的，可能在发送数据时，页面就卸载了，可打发送失败，那么用同步发送，但是同步发送数据，很多浏览器不支持，同步发送，用户体验不好。

用 `Navigator.sendBeacon(url,data)`，该方法与当前页面脱钩，作为浏览器任务，能保证把数据发出去。data 可以是字符串、表单对象、二进制对象等，返回 布尔值，true 说明发送成功，`可跨域`，`POST`，比较多的先进浏览器支持。
请求头信息 `Content-Type`根据发送的 data 类型设置，且不能设置其他头字段。
|data|Content-Type|
|:--:|:--:|
|Blod|Blod|
|formData|multipart/form-data|
|String|text/plain|
```html
// HTML 代码如下
<body onload="analytics('start')" onunload="analytics('end')">
```
JS:
```js
function analytics(state) {
  if (!navigator.sendBeacon) return;
  var URL = 'http://example.com/analytics';
  var data = 'state=' + state + '&location=' + window.location;
  navigator.sendBeacon(URL, data);
}
```
```js
var headers = {type: 'application/json'};
var blob = new Blob(request, headers);
navigator.sendBeacon('url/to/send', blob);
```

## 关于 formData 对象

xhr2 提供了 FormData 对象，为表单序列化和创建与表单相同的数据提供了便利。
其主要用于发送表单数据，也可构造与表单相同的数据，还可以发送文件。
- **formdata 发送表单**
```js
var formData = new FormData();//创建 formData 对象
formData.append('name','jack');//添加表单项
xhr.send(formData);
```
```HTML
	<form id="user-info">
	    <label for="user-name">姓名:</label>
        <input type="text" id="user-name" name="user-name" /><br>
        <label for="user-email">Email:</label>
        <input type="text" id="user-email" name="user-email" /><br>
        <input type="button" value="Submit" onclick="submitData()" />
    </form>
    <script type="text/javascript">
        function submitData() {
            var http = new XMLHttpRequest();
            http.open("post", "formdata", true);
            http.onreadystatechange = function (event) {
                if (http.readyState == 4) {
                    if (http.status == 200) {
                        console.log(http.responseText);
                    } else {
                        alert("Request was unsuccessful: " + http.status);
                    }
                }
            };
            var form = document.getElementById("user-info");
            var formdata = new FormData(form);//用表单初始化一个 FormData 对象
            formdata.append('额外字段', 123);//字段值不是字符串，会转为字符串
            http.send(formdata);
        }
    </script>
```
请求头信息：
```js
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryoCpZL9koEpmlasTW
```
请求体信息：
```js
------WebKitFormBoundaryoCpZL9koEpmlasTW
Content-Disposition: form-data; name="user-name"

afafa
------WebKitFormBoundaryoCpZL9koEpmlasTW
Content-Disposition: form-data; name="user-email"

afafa@163.com
------WebKitFormBoundaryoCpZL9koEpmlasTW
Content-Disposition: form-data; name="额外字段"

123
------WebKitFormBoundaryoCpZL9koEpmlasTW--
```
和表单`enctype="multipart/form-data"`提交一样的。
用 express 处理提交，服务端：
```js
/**
 * formData 上传表单
 * bodyParser 不能处理 formData 数据，需要额外的中间件
 */
let multer = require('multer');
let upload = multer();
app.post("/formdata", upload.fields([]), (req, res) => {
    console.log(req['body']);
    res.json({ success: true });
});
```
- **formdata 上传文件** TODO 如何用 express 接收呢？
客户端：
```html
    <form enctype="multipart/form-data" method="post" name="fileinfo">
        <label>Your email address:</label>
        <input type="email" autocomplete="on" autofocus name="userid" placeholder="email" required size="32" maxlength="64" /><br />
        <label>Custom file label:</label>
        <input type="text" name="filelabel" size="12" maxlength="32" /><br />
        <label>File to stash:</label>
        <input type="file" name="file" required />
        <input type="submit" value="Stash the file!" />
      </form>
```
## 待完成

- formData 对象
- 浏览器 Blod
- arrayBuffer

## xhr 封装

```js
/**
 * options = {
		url		提交请求的url
		method	请求的类型，默认为GET
        data	请求体的数据
        cache   是否缓存数据，默认为false
		async	是否以异步方式进行，默认为true 
		success	成功后的处理方法
		error	失败后的处理方法
	}
 */
function ajax(options) {
    var options = options || {};
    options.method = options.method.toUpperCase() || 'GET';
    options.async = options.async || true;
    options.cache = options.cache || true;
    // 格式化参数的函数
    var getParams = function (data) {
        var arr = [];
        for (param in data) {
            //查询字符串中每个参数的名称和值都必须使用 encodeURIComponent()进行编码
            arr.push(encodeURIComponent(param) + "=" + encodeURIComponent(data[param]));
        }
        // 在 url 末尾加上一个随机数，避免相同值使用 IE 缓存
        if (options['cache']) {
            arr.push(("randomNum=" + Math.random()).replace(".", ""));
        }
        // 返回带有 & 的查询字符串
        return arr.join("&");
    }
    var params = getParams(options['data']);
    var http = new XMLHttpRequest();
    http.onload = function () {
        if (http.status === 200 || http.status === 304) {
            // 成功
            options.success && options.success(http.response);
        } else {
            // 失败
            options.fail && options.fail(http.status);
        }
    }
    switch (options['method']) {
        case 'GET':
            http.open("GET", options['url'] + "?" + params, options['async']);
            // 发送请求
            http.send(null);
            break;
        case 'POST':
            http.open('POST', options['url'], options['async']);
            http.setRequestHeader('content-type', 'application/x-www-form-urlencoded');
            http.send(params)
            break;
        default:
            console.log('不支持的方法');
            //TODO: 如何支持其他方法？
            break;
    }
}
```
promise 版本：
```js
function http({
  url = '',
  method = 'get',
  data = {},
  dataType = 'multipart',
  responseType = 'json',
  headers = {},
} = {}) {
  let body = null;
  method = method.toUpperCase();
  if (!url) {
    console.error('url是必需参数！');
    return;
  }
  function xhr(resolve, reject) {
    let http = new XMLHttpRequest();
    http.responseType = responseType;
    http.onload = function() {
      if (200 == http.status || 304 === http.status) {
        resolve(http.response);
      } else {
        reject(http.statusText);
      }
    };
    http.onerror = function(e) {
      reject(e);
    };
    switch (method.toLowerCase()) {
      case 'get':
        var queryString = '';
        if (JSON.stringify(data) !== '{}') {
          queryString = Object.keys(data)
            .map(function(key) {
              return encodeURIComponent(key) + '=' + encodeURIComponent(data[key]);
            })
            .join('&');
        }
        if (queryString) {
          url += '?' + queryString;
        }
        http.open('GET', url, true);
        break;
      case 'post':
        http.open('POST', url, true);
        if ('json' === dataType) {
          body = JSON.stringify(data);
          http.setRequestHeader('content-type', 'application/json;charset=UTF-8');
        } else if ('urlencoded' === dataType) {
          body = Object.keys(data)
            .map(function(key) {
              return encodeURIComponent(key) + '=' + encodeURIComponent(data[key]);
            })
            .join('&');
          http.setRequestHeader('content-type', 'application/x-www-form-urlencoded');
        } else if ('xml' === dataType) {
          http.setRequestHeader('content-type', 'text/xml');
        } else {
          body = data;
        }
        break;
    }
    for (const [key, value] of Object.entries(headers)) {
      http.setRequestHeader(key, value);
    }
    http.send(body);
  }
  let promise = new Promise(xhr);
  return promise;
}
```

## 参考

- [XMLHttpRequest 对象 - JavaScript 教程 - 网道][1]
- [Navigator.sendBeacon() to pass header information][2]
- [Is onload equal to readyState==4 in XMLHttpRequest?][3]
- [What is meaning of xhr.readystate===4][4]
- [Why Use onload in Cross-domain Ajax][5]
- [XMLHttpRequest Living Standard — Last Updated 28 November 2018][6]


[1]: https://wangdoc.com/javascript/bom/xmlhttprequest.html
[2]: https://stackoverflow.com/questions/40523469/navigator-sendbeacon-to-pass-header-information
[3]: https://stackoverflow.com/questions/9181090/is-onload-equal-to-readystate-4-in-xmlhttprequest
[4]: https://stackoverflow.com/questions/30522565/what-is-meaning-of-xhr-readystate-4
[5]: https://zqzhang.github.io/blog/2016/04/18/why-use-onload-in-cross-domain-ajax.html
[6]: https://xhr.spec.whatwg.org/#progressevent