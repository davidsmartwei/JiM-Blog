---
title: HTTP(HyperText Transfer Protocol)
date: 2015-11-14 12:36:00
categories: http 
tags: http
comments : true 
updated : 
layout : 
---

## HTTP(HyperText Transfer Protocol)

 MIME (*M*ultipurpose *I*nternet *M*ail *E*xtensions) 是描述消息内容类型的因特网标准。 

### 1 HTTP理解 

#### 1 HTTP 服务器:即网站服务器，主要提供文档(文本、图片、视频、音频)浏览服务，一般安装Apache、Nginx服务器软件。HTTP服务器可以结合某一编程语言处理业务逻辑，由此进行的开发，通常称之为**服务端开发**。常见的运行在服务端的编程语言包 括 php、java、.net、Python、Ruby、Perl等。

如何搭建自己的HTTP服务器？

*  安装wampsever:(Windows+Apache+Mysql+PHP)
*  配置安装根目录
*  配置虚拟主机

#### 2 HTTP 协议:超文本传输协议（HTTP，HyperText Transfer Protocol) 网站是基于HTTP协议的， 例如网站的图片、CSS、JS等都是基于HTTP协议进行传输的。HTTP协议是由从客户机到服务器的请求(Request)和从服务器到客户机的响应(Response)进行了约束和规范。即HTTP协议主要由请求和响应构成。

#### 3 请求和响应

- **3.1、请求行** 

由请求方式、请求URL和协议版本构成

```
GET /day01/code/login.php?username=123&password=123 HTTP/1.1
POST /day01/code/login.php HTTP/1.1
```

- **3.2、请求头**

Host：localhost请求的主机

Cache-Control：max-age=0控制缓存

Accept：*/* 接受的文档MIME类型

```html
1 如果是XHR对象和javascript标签发送请求，那么accept ：*/ *  代表接受所有的MIME类型的返回值;
2 jQuery ajax发送请求的时候，如果设置了dataType 属性

```

User-Agent：很重要

Referer：从哪个URL跳转过来的

Accept-Encoding：可接受的压缩格式

If-None-Match：记录服务器响应的ETag值，用于控制缓存

此值是由服务器自动生成的

If-Modified-Since：记录服务器响应的Last-Modified值

此值是由服务器自动生成的

- **3.3、请求主体**

即传递给服务端的数据

注：当以post形式提交表单的时候，请求头里会设置

Content-Type: application/x-www-form-urlencoded，以get形式当不需要

 #### 4 响应 

响应由服务器发出，其规范格式为：状态行、响应头、响应主体。

- **4.1、状态行**

由协议版本号、状态码和状态信息构成 HTTP/1.1 200 OK

- **4.2、响应头**

Date：响应时间

Server：服务器信息

Last-Modified：资源最后修改时间 由服务器自动生成

ETag：资源修改后生成的唯一标识

由服务器自动生成

Content-Length：响应主体长度

Content-Type：响应资源的类型(header()函数可以定义返回的资源类型text/html  、text/javascript  、 )

- **4.3、响应主体**

即服务端返回给客户端的内容；

### 2 请求的"进化"；

“标签”请求  form可以设置get和post  a标签href发送请求get请求   iframe src属性 img src属性 script src属性

#### 1 form表单method:get post方法请求   a 标签 都是以get发送请求

控制台  type document对象 ; 

代表有document对象直接发送请求(这就是为什么页面会刷新的原因)

#### 2 XMLHttpResponse 对象请求 ，该请求只允许请求当前源(域名，端口，协议)的资源，所以AJAX是不能跨域的

    控制台  type 是 XMLHttpRequest 对象 ；
    
    代表请求是由这个javascript内置对象发送的；所谓的ajax其实就是请求的发送以及响应体的接受都是由一个javascript对象XMLHttpRequest完成。页面不会刷新，可以通过js动态的加载元素。

#### 3 跨域请求(同源策略:域名 协议 端口相同，当前域不能访问其他域的资源)

*  script标签请求 script标签不受同源策略的限制 **script 的src获取javascript;link的src获取 css； img的src获取图片资源； iframe 的src获取页面标** 是允许跨域的，这些标签不受跨域的限制；

   控制台  type script ，(如果我们用script标签请求其他网站的jQuery库，可以减少本网站的请求)

* Cross-Origin Resource Sharing（CORS）跨域资源共享是一份浏览器技术的规范，提供了 Web 服务从不同域传来沙盒脚本的方法，以避开浏览器的同源策略，是 JSONP 模式的现代版。

##### 3.1 如何解决同源策略带来的问题，进行跨域？

 3.1.1 jsonp 单向跨域(get请求方式)

为什么script标签引入的文件不受同源策略的限制？因为script标签引入的文件内容是不能够被客户端的js获取到的，不会影响到被引用文件的安全，所以没必要使script标签引入的文件遵循浏览器的同源策略。而通过ajax加载的文件内容是能够被客户端js获取到的，所以ajax必须遵循同源策略，否则被引入文件的内容会泄漏或者存在其他风险。JSONP的缺点则是：它只支持GET请求而不支持POST等其它类型的HTTP请求

```html
//本域代码
<script>
	function getUsers (obj){
      console.log(obj)
	}
</script>
<script src="http://www.google.com/getUsers.php"></script>
```

```php
//需要谷歌支持，服务器代码如下，返回的是一个js代码的字符串，
<?php 
 	echo 'getUsers(["Jhon","JiM","kobe"])';
 ?>
```

3.1.2 [CORS跨域  Cross-Origin Resource Sharing  跨域资源共享](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

MDN定义：Cross-Origin Resource Sharing ([CORS](https://developer.mozilla.org/en-US/docs/Glossary/CORS)) is a mechanism that uses additional [HTTP](https://developer.mozilla.org/en-US/docs/Glossary/HTTP) headers to let a [user agent](https://developer.mozilla.org/en-US/docs/Glossary/user_agent) gain permission to access selected resources from a server on a different origin (domain) than the site currently in use. A user agent makes a **cross-origin HTTP request** when it requests a resource from a different domain, protocol, or port than the one from which the current document originated（发起）

An example of a cross-origin request: A HTML page served from `http://domain-a.com`makes an `<img> src` request for `http://domain-b.com/image.jpg`. Many pages on the web today load resources like CSS stylesheets, images, and scripts from separate domains, such as content delivery networks (CDNs).

For security reasons, browsers restrict cross-origin HTTP requests initiated from within scripts. For example, [`XMLHttpRequest`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) and the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) follow the [same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy). This means that a web application using those APIs can only request HTTP resources from the same domain the application was loaded from unless CORS headers are used.

`前端与后端进行 Ajax 通信时，通过自定义 HTTP 头部设置从而决定请求或响应是否生效`。

比如前端代码(url 为 [http://127.0.0.1:3001)写了段](http://127.0.0.1:3001)) Ajax，代码如下：

```
const xhr = new XMLHttpRequest()
xhr.onreadystatechange = function () {
  if (xhr.readyState === 4) {
    if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
      console.log('responseTesx:' + xhr.responseText)
    }
  }
}
xhr.open('get', 'http://127.0.0.1:3000', true)
xhr.send()
```

因为端口不一致的关系这时候导致不同源了，这时候会在 Request Headers 中发现多了这么一行字段，

```
Origin: http://127.0.0.1:3001
```

而且控制台中会报出如下错误:

```
Failed to load http://127.0.0.1:3000/: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://127.0.0.1:3001' is therefore not allowed access.
```

这时候就需要在服务端设置字段 `Access-Control-Allow-Origin`，它的作用就是设置允许来自什么源的请求，如果值设置为 `*`，表明允许来自任意源的请求。服务端代码示例如下：

```
http.createServer((req, res) => {
  res.setHeader('Access-Control-Allow-Origin', 'http://127.0.0.1:3001') // 设置允许来自 http://127.0.0.1:3001 源的请求
})
```

跨域请求又会分为： 简单请求和 预检请求，具体还是看MDN 

##### 4 HTML5新增API postMessage方法，HTML5系列博文有篇文章专门分析总结

