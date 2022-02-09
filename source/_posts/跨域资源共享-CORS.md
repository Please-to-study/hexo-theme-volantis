---
title: 跨域资源共享 CORS
seo_title: 跨域资源共享 CORS
toc: true
indent: true
comments: true
archive: true
cover: true
mathjax: false
pin: false
top_meta: true
bottom_meta: true
sidebar:
  - toc
icons: []
references:
  - title: 跨域资源共享 CORS 详解
    url: http://www.ruanyifeng.com/blog/2016/04/cors.html
date: 2021-11-01 15:06:00
updated: 2021-11-01 15:06:00
categories:
- 学习笔记
keywords: 同源策略 跨域资源共享 CORS
description: 什么是跨域？如何解决跨域问题。
headimg: https://cdn.pkubailu.cn/img/跨域封面.jpeg
thumbnail:
tags:
- 跨域
- CORS
---

{% note info:: 跨域知识点学习笔记 %}

## 一、同源

1995年，同源策略由 Netscape 公司引入浏览器。目前，所有浏览器都实行这个政策。
{% noteblock::所谓"同源"指的是"三个相同" %}
- 协议相同
- 域名相同
- 端口相同
{% endnoteblock %}

For example:
`http://www.pkubailu.com/index.html` 这个网址，协议是http://，域名是`www.pkubailu.com`，端口是80（默认端口可以省略）。它的同源情况如下。
{% noteblock:: %}
- `http://www.pkubailu.com/other.html`：同源
- `http://cdn.pkubailu.com/index.html`：不同源（域名不同）
- `https://www.pkubailu.com/index.html`：不同源（协议不同）
- `http://www.pkubailu.com:81/index.html`：不同源（端口不同）
{% endnoteblock %}

## 二、跨域

一个HTTP请求的URL的协议、域名、端口三者中的任何一个与当前源不同，则视为跨域请求。如果不做处理，我们会看到chrome抛出一个错误：
![](https://cdn.pkubailu.cn/img/cros.webp)
而在实际的场景中，我们在很多情况下需要进行跨域请求，下面讲解几种常见的跨域方案。

CORS是一个W3C标准，全称是"跨域资源共享"（Cross-origin resource sharing）。

它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。

本文详细介绍CORS的内部机制。

## 三、跨域方法
### 1.JSONP
JSONP：这是跨域请求的一个经典方案，其主要原理是通过JS动态创建`<script>`标签获取指定资源，然后前后端约定一个callback来获取json数据，`<script>`、`<iframe>`这些具有src属性的标签都是可直接跨域获取资源的，这种方式其实只是巧妙地绕过跨域限制，而且有其局限性，比如很明显的，只能发送GET请求，而且要判断请求是否失败也比较棘手。
```
// 客服端代码
<script>
    //获取input元素
    const input = document.querySelector('input');
    const p = document.querySelector('p');
    //声明 handle 函数
    function handle(data){
        //修改边框颜色
        input.style.border = "solid 1px #f00";
        //修改 p 标签的提交文本
        p.innerHTML = data.msg;
    }
    //绑定事件
    input.onblur = function () {
        //获取用户的输入值
        let username = this.value;
        //向服务端发送请求 检测用户名是否存在
        //1. 创建 script 标签
        const script = document.createElement('script');
        //2. 设置 script 标签的src属性
        script.src = 'http://127.0.0.1:8000/check-username';
        //3. 将 script 插入到文档中
        document.body.appendChild(script);
    }
</script>

// 服务端代码
app.all('/check-username', (request, response) =>{
    // response.send('console.log("hello jsonp")');
    const data = {
        exist: 1,
        msg: '用户名已经存在'
    };
    //将数据转化为字符串
    let str = JSON.stringify(data);
    //返回结果形式 是一个函数调用，而函数的实参就是我们想给客户端返回的结果数据
    response.end(`handle(${str})`);

});
```
### 2.Proxy代理
Proxy代理：由于同源策略只是浏览器的限制，服务器端并没有这个限制，所以只要A域客户端将请求发送一个代理服务器，然后由代理服务器去请求B域服务器就行了，比如前后端分离的工程，本地调试的时候我们启用nodejs代理服务、线上部署通过nginx代理转发等，都属于这个跨域模式。同样的，这个本质上也只是绕过浏览器的跨域限制而已。

### 3.CORS（Cross-Origin Resource Sharing）：跨域资源共享标准
#### 3.1简介
CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。
整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。
因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。

#### 3.2两种请求
浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。
只要同时满足以下两大条件，就属于简单请求。
{% noteblock:: %}
（1) 请求方法是以下三种方法之一：
- HEAD
- GET
- POST

（2）HTTP的头信息不超出以下几种字段：
- Accept
- Accept-Language
- Content-Language
- Last-Event-ID
- Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain
{% endnoteblock %}
这是为了兼容表单（form），因为历史上表单一直可以发出跨域请求。AJAX 的跨域设计就是，只要表单可以发，AJAX 就可以直接发。
凡是不同时满足上面两个条件，就属于非简单请求。
浏览器对这两种请求的处理，是不一样的。

#### 3.3简单请求
对于简单请求，浏览器直接发出CORS请求。具体来说，就是在头信息之中，增加一个{% span pink::Origin %}字段。
下面是一个例子，浏览器发现这次跨源AJAX请求是简单请求，就自动在头信息之中，添加一个{% span pink::Origin %}字段。
```
GET /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```
上面的头信息中，{% span red::Origin %}字段用来说明，本次请求来自哪个源（协议 + 域名 + 端口）。服务器根据这个值，决定是否同意这次请求。

如果{% span red::Origin %}指定的源，不在许可范围内，服务器会返回一个正常的HTTP回应。浏览器发现，这个回应的头信息没有包含{% span red::Access-Control-Allow-Origin %}字段（详见下文），就知道出错了，从而抛出一个错误，被XMLHttpRequest的onerror回调函数捕获。注意，这种错误无法通过状态码识别，因为HTTP回应的状态码有可能是200。

如果{% span red::Origin %}指定的域名在许可范围内，服务器返回的响应，会多出几个头信息字段。
```
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
Content-Type: text/html; charset=utf-8
```
上面的头信息之中，有三个与CORS请求相关的字段，都以{% span red::Access-Control- %}开头。

**（1）Access-Control-Allow-Origin**

该字段是必须的。它的值要么是请求时{% span red::Origin %}字段的值，要么是一个{% span red::* %}，表示接受任意域名的请求。

**（2）Access-Control-Allow-Credentials**

该字段可选。它的值是一个布尔值，表示是否允许发送Cookie。默认情况下，Cookie不包括在CORS请求之中。设为{% span red::true %}，即表示服务器明确许可，Cookie可以包含在请求中，一起发给服务器。这个值也只能设为{% span red::true %}，如果服务器不要浏览器发送Cookie，删除该字段即可。

**（3）Access-Control-Expose-Headers**

该字段可选。CORS请求时，{% span red::XMLHttpRequest %}对象的{% span red::getResponseHeader() %}方法只能拿到6个基本字段：{% span red::Cache-Control %}、{% span red::Content-Language %}、{% span red::Content-Type %}、{% span red::Expires %}、{% span red::Last-Modified %}、{% span red::Pragma %}。如果想拿到其他字段，就必须在{% span red::Access-Control-Expose-Headers %}里面指定。上面的例子指定，{% span red::getResponseHeader('FooBar') %}可以返回{% span red::FooBar %}字段的值。

上面说到，CORS请求默认不发送Cookie和HTTP认证信息。如果要把Cookie发到服务器，一方面要服务器同意，指定{% span red::Access-Control-Allow-Credentials %}字段。
```
Access-Control-Allow-Credentials: true
```
另一方面，开发者必须在AJAX请求中打开{% span red::withCredentials %}属性。
```
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```
否则，即使服务器同意发送Cookie，浏览器也不会发送。或者，服务器要求设置Cookie，浏览器也不会处理。

但是，如果省略{% span red::withCredentials %}设置，有的浏览器还是会一起发送Cookie。这时，可以显式关闭{% span red::withCredentials %}。
```
xhr.withCredentials = false;
```
需要注意的是，如果要发送Cookie，{% span red::Access-Control-Allow-Origin %}就不能设为星号，必须指定明确的、与请求网页一致的域名。同时，Cookie依然遵循同源政策，只有用服务器域名设置的Cookie才会上传，其他域名的Cookie并不会上传，且（跨源）原网页代码中的{% span red::document.cookie %}也无法读取服务器域名下的Cookie。

#### 3.4非简单请求
##### 预检请求
非简单请求是那种对服务器有特殊要求的请求，比如请求方法是{% span red::PUT %}或{% span red::DELETE %}，或者{% span red::Content-Type %}字段的类型是{% span red::application/json %}。

非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为"预检"请求（preflight）。

浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的{% span red::XMLHttpRequest %}请求，否则就报错。

下面是一段浏览器的JavaScript脚本。
```
var url = 'http://api.alice.com/cors';
var xhr = new XMLHttpRequest();
xhr.open('PUT', url, true);
xhr.setRequestHeader('X-Custom-Header', 'value');
xhr.send();
```
上面代码中，HTTP请求的方法是{% span red::PUT %}，并且发送一个自定义头信息{% span red::X-Custom-Header %}。

浏览器发现，这是一个非简单请求，就自动发出一个"预检"请求，要求服务器确认可以这样请求。下面是这个"预检"请求的HTTP头信息。
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
"预检"请求用的请求方法是{% span red::OPTIONS %}，表示这个请求是用来询问的。头信息里面，关键字段是{% span red::Origin %}，表示请求来自哪个源。

除了{% span red::Origin %}字段，"预检"请求的头信息包括两个特殊字段。

**（1）Access-Control-Request-Method**

该字段是必须的，用来列出浏览器的CORS请求会用到哪些HTTP方法，上例是{% span red::OPTIONS %}PUT。

**（2）Access-Control-Request-Headers**

该字段是一个逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段，上例是{% span red::X-Custom-Header %}。

##### 预检请求的回应
服务器收到"预检"请求以后，检查了{% span red::Origin %}、{% span red::Access-Control-Request-Method %}和{% span red::Access-Control-Request-Headers %}字段以后，确认允许跨源请求，就可以做出回应。
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
上面的HTTP回应中，关键的是{% span red::Access-Control-Allow-Origin %}字段，表示`http://api.bob.com`可以请求数据。该字段也可以设为星号，表示同意任意跨源请求。
```
Access-Control-Allow-Origin: *
```
如果服务器否定了"预检"请求，会返回一个正常的HTTP回应，但是没有任何CORS相关的头信息字段。这时，浏览器就会认定，服务器不同意预检请求，因此触发一个错误，被{% span red::XMLHttpRequest %}对象的{% span red::onerror %}回调函数捕获。控制台会打印出如下的报错信息。
```
XMLHttpRequest cannot load http://api.alice.com.
Origin http://api.bob.com is not allowed by Access-Control-Allow-Origin.
```
服务器回应的其他CORS相关字段如下。
```
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
Access-Control-Allow-Credentials: true
Access-Control-Max-Age: 1728000
```
**（1）Access-Control-Allow-Methods**

该字段必需，它的值是逗号分隔的一个字符串，表明服务器支持的所有跨域请求的方法。注意，返回的是所有支持的方法，而不单是浏览器请求的那个方法。这是为了避免多次"预检"请求。

**（2）Access-Control-Allow-Headers**

如果浏览器请求包括{% span red::Access-Control-Request-Headers %}字段，则{% span red::Access-Control-Allow-Headers %}字段是必需的。它也是一个逗号分隔的字符串，表明服务器支持的所有头信息字段，不限于浏览器在"预检"中请求的字段。

**（3）Access-Control-Allow-Credentials**

该字段与简单请求时的含义相同。

**（4）Access-Control-Max-Age**

该字段可选，用来指定本次预检请求的有效期，单位为秒。上面结果中，有效期是20天（1728000秒），即允许缓存该条回应1728000秒（即20天），在此期间，不用发出另一条预检请求。

##### 浏览器的正常请求和回应
一旦服务器通过了"预检"请求，以后每次浏览器正常的CORS请求，就都跟简单请求一样，会有一个Origin头信息字段。服务器的回应，也都会有一个{% span red::Access-Control-Allow-Origin %}头信息字段。

下面是"预检"请求之后，浏览器的正常CORS请求。
```
PUT /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
X-Custom-Header: value
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```
上面头信息的{% span red::Origin %}字段是浏览器自动添加的。

下面是服务器正常的回应。
```
Access-Control-Allow-Origin: http://api.bob.com
Content-Type: text/html; charset=utf-8
```
上面头信息中，{% span red::Access-Control-Allow-Origin %}字段是每次回应都必定包含的。
