---
layout: mypost
title: 同源策略及跨域
tags: 跨域
---

## 前言

首先，何为同源策略？
`同源策略`是由Netspace提出来的一种安全策略。`同源`的`源`由一个URL的协议、主机、端口定义，如果这三者一致，那么就是同源。

当不同源的时候，哪些操作被允许呢？
1. `<script>`加载Javascript
2. `<link>` 加载CSS
3. `<img>`加载图片
4. `<video><audio>`加载多媒体
5. `<object><embed><applet>`加载插件
6. `<iframe>`加载任何东西
7. 链接、跳转、表单提交

哪些不允许呢？
1. 跨域文档之间使用Javascript脚本进行交互，API的访问有限制。比如iframe与父页面不同源，想要通过Javascript去操作父页面DOM是不被允许的
2. 不同源之间的`XMLHttpRequest`不被允许

## 跨域解决方案

### 修改源

一个页面的源可以修改的，通过`document.domain`,比如一个页面`a.b.com`嵌入iframe`c.b.com`，这时候只需要把两个页面的`document.domain`设置为`b.com`即可。

### 使用代理

跨域是针对前端来的，服务端是没有跨域这个东西的，所以后台设置一下，前端访问时访问一个同源页面，然后后台把请求的数据转到不同源的页面即可。

### JSONP

因为<script>内嵌资源是允许的，所以可以把请求的接口放在`<script>`标签上，然后附带我们的回调函数

```
<script src="http://other.com/ajax.json?callback=myFunction"></script>
```
接口获取到callback后，把返回的数据作为参数传给callback并执行。

所以它有三个明显的缺点：

1. 需要后端的配合
2. 只能是GET请求
3. 调用是异步的

### Web Messaging

这个是HTML5的一个接口，用到了接口里面的`postMessage`方法，主要用于两个窗口之间交换数据，不能用于与服务器交换数据。

```
otherWindow.postMessage(message, targetOrigin, [transfer]);
```
`otherWindow`是接收方窗口的引用。一般是以下几种方式：
1. window.iframe[0]
2. document.getElementByTagName('iframe')[0].contentWindow
3. window.open返回的引用
4. event.source 接收到数据的源头

而`message`是支持几乎所有形式的数据，`transfer`可省略

用法：

```
// 父页面
document.getElementByTagName('iframe')[0].contentWindow.postMessage({"age":10},'http://localhost:8080');
// 监听有没有数据返回来
window.addEventListener('message',function(e){
  console.log(e)
});
```

```
// iframe
window.addEventListener('mesage',function(e){
  if(e.origin !== 'http://localhost:8081'){
    return;
  }
  console.log(e);
  e.souce.postMessage('hello world',e.origin)
})
```
### 跨域资源共享CORS

CORS需要浏览器和服务器同时支持，关键是服务器，只要服务器实现了CORS接口就可以跨域通信。

#### 两种请求

CORS将请求分为两类：简单请求和非简单请求。

非简单请求的条件：
1. 请求方式是「PUT」、「DELETE」
2. `Content-type`字段是`application/json`

#### 简单请求

对于简单请求，浏览器在头信息里面加一个`origin`字段，说明本次请求来自哪个源(协议+主机+端口),服务器根据这个值，决定是否同意。如果`origin`不在指定的源里面，就会返回一个正常的HTTP回应，但是里面不包含`Access-Control-Allow-Origin`就知道出错了，从而抛出一个错误，被`XMLHttpRequest`的`onerror`回调函数捕获。

> 这种错误无法通过状态码判断，有可能是`200`

如果源在许可范围内，就会返回响应，并多出几个信息字段。

```
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
Content-Type: text/html; charset=utf-8
```

1. `Access-Control-Allow-Origin`必须字段。如果是`*`，表示接收任何域名的请求。
2. `Access-Control-Allow-Credentials`可选字段。CORS请求默认是不带cookie和HTTP信息的，如果为`true`，表明服务器许可请求中可以包含cookie
> 除了服务器允许请求附带cookie和HTTP信息，客户端也必须允许
> ```
> var xhr = new XMLHttpRequest();
> xhr.withCredentials = true;
> ```
3. `Access-Control-Expose-Headers: FooBar`该字段可选。CORS请求时，`XMLHttpRequest`对象的`getResponseHeader()`方法只能拿到6个基本字段：`Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma`。如果想拿到其他字段，就必须在`Access-Control-Expose-Headers`里面指定。上面的例子指定，`getResponseHeader('FooBar')`可以返回FooBar字段的值。

#### 非简单请求

非简单请求的不同之处在于在正式通信之前会有一次HTTP查询请求，成为「预检」请求(preflight)

##### Javascript脚本：
```
var url = 'http://api.alice.com/cors';
var xhr = new XMLHttpRequest();
xhr.open('PUT', url, true);
xhr.setRequestHeader('X-Custom-Header', 'value');
xhr.send();
```
##### 预检请求

浏览器发现这是一个非简单请求，就会自动发出一个预检请求。
```
OPTIONS /cors HTTP/1.1
Origin: http://api.bob.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: X-Custom-Header
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```

预检的请求方式是`OPTIONS`,表示这个请求是用来询问的。

##### 预检请求回应

服务器收到预检请求后，检查了`Origin`、`Access-Control-Request-Method`和`Access-Control-Request-Headers`字段以后确认允许跨域请求，然后做出回应

```
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
Content-Type: text/html; charset=utf-8
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```

`Access-Control-Allow-Methods`表明服务器支持的所有跨域请求的方法。

如果预检请求通过，以后每次的CORS请求就跟简单请求一样了。

### WebSocket

WebSocket是不受同源限制的，所以跨域什么的就不存在了。

基本用法：

```
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) {
  console.log("Connection open ...");
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
}; 
```
