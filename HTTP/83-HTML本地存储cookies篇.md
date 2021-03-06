---
title: cookie
date: 2016-08-13 12:36:00
categories: http cookies
tags : cookies
comments : true 
updated : 
layout : 
---

本地存储cookies篇

### 1 HTTP cookie，通常直接叫做cookie，是客户端用来存储数据的一种选项，它既可以在客户端设置也可以在服务器端设置。cookie会跟随任意HTTP请求一起发送。

优点：兼容性好

缺点：一是增加了网络流量；二则是它的数据容量有限，最多只能存储4KB的数据，浏览器之间各有不同；三是不安全。

### 2.cookie的用途及工作原理

那cookie具体能干什么呢？

cookie 将信息存储于用户硬盘，因此可以作为全局变量，这是它最大的一个优点。它最根本的用途是 Cookie 能够帮助 Web 站点 `保存有关访问者的信息` ，以下列举cookie的几种小用途。

① 保存用户登录信息。这应该是最常用的了。当您访问一个需要登录的界面，例如微博、百度及一些论坛，在登录过后一般都会有类似”下次自动登录”的选项，勾选过后下次就不需要重复验证。这种就可以通过cookie保存用户的id。

② 创建购物车。购物网站通常把已选物品保存在cookie中，这样可以实现 `不同页面` 之间 `数据的同步` (同一个域名下是可以共享cookie的)，同时在提交订单的时候又会把这些cookie传到后台。

我们可以随便打开天猫，进入自己的购物车，然后勾选几项，看下netWork中XHR的请求

③ 跟踪用户行为。例如百度联盟会通过cookie记录用户的偏好信息，然后向用户推荐个性化推广信息，所以浏览其他网页的时候经常会发现旁边的小广告都是自己最近百度搜过的东西。这是可以禁用的，这也是cookie的缺点之一。

那么，cookie是怎么起作用的呢？

在上一节中我们知道 cookie 是存在用户硬盘中，用户每次访问站点时，Web应用程序都可以读取 Cookie 包含的信息。当用户再次访问这个站点时，**浏览器就会在本地硬盘上 查找 与该 URL 相关联的 Cookie 。如果该 Cookie 存在，浏览器就将它添加到 request header的Cookie字段中，与 http请求`一起发送到该站点。**

在开发者工具network可以看到，如果有关该URL的cookie会被添加到请求上

要注意的是，添加到 request header 中是 浏览器的行为 ，存储在cookie的数据 每次 都会被浏览器 `自动` 放在http请求中。因此，如果这些数据不是每次都要发给服务器的话，这样做无疑会增加网络流量，这也是cookie的缺点之一。为了避免这点，我们必须考虑什么样的数据才应该放在cookie中，而不是滥用cookie。每次请求都需要携带的信息，最典型的就是 身份验证了，其他的大多信息都不适合放在cookie中。

cookie流程

1. 用户在浏览器输入url,发送请求,服务器接受请求
2. 服务器在响应报文中生成一个Set-Cookie报头,发给客户端
3. 浏览器取出响应中Set-Cookie中内容,以cookie.txt形式保存在客户端
4. 如果浏览器继续发送请求,浏览器会在硬盘中找到cookie文件,产生Cookie报头,与HTTP请求一起发送.
5. 服务器接受含Cookie报头的请求,处理其中的cookie信息,找到对应资源给客户端.
6. 浏览器每一次请求都会包含已有的cookie.

### 3 cookie属性

name、value 是 cookie 的名和值。domian 、Path 、 Expires/max-age 、Size 、Http 、 Secure等均属cookie的属性。

**domain 和 path**

这两个选项共同决定了cookie能被哪些页面共享。

**domain 参数是用来控制 cookie对「哪个域」有效**，**默认为设置** cookie的那个域。这个值可以包含子域，也可以不包含它。如上图的例子，Domain选项中，可以是".google.com.hk`"(不包含子域,表示它对`google.com.hk`的所有子域都有效)，也可以是"`www.google.com.hk"(包含子域)。

如网址为www.jb51.net/test/test.aspx，那么domain默认为www.jb51.net。而跨域访问，如域A为t1.test.com，域B为t2.test.com，那么在域A生产一个令域A和域B都能访问的cookie就要将该cookie的domain设置为.test.com；如果要在域A生产一个令域A不能访问而域B能访问的cookie就要将该cookie的domain设置为t2.test.com

path用来控制cookie发送的指定域的「路径」，默认为"/"，表示指定域下的所有路径都能访问。**它是在域名的基础下，指定可以访问的路径**。例如cookie设置为"`domain=.google.com.hk; path=/webhp`"，那么只有"`.google.com.hk/webhp`"及"`/webhp`"下的任一子目录如"`/webhp/aaa`"或"`/webhp/bbb`"会发送cookie信息，而"`.google.com.hk`"就不会发送，即使它们来自同一个域。

### expries/max-age失效时间

expries 和 max-age 是用来决定cookie的生命周期的，也就是cookie何时会被删除。

字段为此cookie超时时间。若设置其值为一个时间，那么当到达此时间后，此cookie失效。不设置的话默认值是Session，意思是cookie会和session一起失效。当浏览器关闭(不是浏览器标签页，而是整个浏览器) 后，此cookie失效。

expries 表示的是失效时间，准确讲是「时刻」，max-age表示的是生效的「时间段」，以「秒」为单位。

若 `max-age` 为正值，则表示 cookie 会在 max-age 秒后失效。如例四中设置"max-age=10800;"，也就是生效时间是3个小时，那么 cookie 将在三小时后失效。

若 `max-age` 为负值，则cookie将在浏览器会话结束后失效，即 session，max-age的默认值为-1。若 `max-age` 为0，则表示删除cookie。

### secure

默认情况为空，不指定 secure 选项，即不论是 http 请求还是 https 请求，均会发送cookie。

是 cookie 的安全标志，是cookie中唯一的一个非键值对儿的部分。指定后，cookie只有在使用`SSL`连接（如`HTTPS`请求或其他安全协议请求的）时才会发送到服务器。

### httponly（即http）

`httponly`属性是用来限制客户端脚本对cookie的访问。将 cookie 设置成 httponly 可以减轻xss（[跨站脚本攻击 ](http://baike.baidu.com/view/2633667.htm)Cross Site Scripting）攻击的危害，

防止cookie被窃取，以增强cookie的安全性。（由于cookie中可能存放身份验证信息，放在cookie中容易泄露）

默认情况是不指定 httponly，即可以通过 js 去访问。

如果设置了该字段，那么在客户端，就不能通过document.cookies获取；

Cookie都是通过document对象获取的，我们如果能让cookie在浏览器中不可见就可以了，那HttpOnly就是在设置cookie时接受这样一个参数，一旦被设置，在浏览器的document对象中就看不到cookie了。而浏览器在浏览网页的时候不受任何影响，因为Cookie会被放在浏览器头中发送出去(包括Ajax的时候)，应用程序也一般不会在JS里操作这些敏感Cookie的，对于一些敏感的Cookie我们采用HttpOnly，对于一些需要在应用程序中用JS操作的cookie我们就不予设置，这样就保障了Cookie信息的安全也保证了应用。

###4、如何利用以上属性去设置cookie？

**服务器端设置**

服务器通过发送一个名为 Set-Cookie 的HTTP头来创建一个cookie，作为 Response Headers 的一部分。如下图所示，每个Set-Cookie 表示一个 cookie（**如果有多个cookie,需写多个Set-Cookie**），每个属性也是以名/值对的形式（除了secure），属性间以分号加空格隔开。格式如下：

```javascript
set-cookie: vcsaas_test16_uid=595177f83a5a44138e611ce2; Max-Age=315360000; Expires=Sun, 16-Apr-2028 09:29:29 GMT; Domain=test.com; Path=/; HttpOnly
set-cookie: vcsaas_test16_org_id=5ac34b76909483050c8cd52d; Max-Age=315360000; Expires=Sun, 16-Apr-2028 09:29:29 GMT; Domain=test.com; Path=/; HttpOnly
set-cookie: vcsaas_test16_token=5ad86179cff47e3486c5ed00; Max-Age=315360000; Expires=Sun, 16-Apr-2028 09:29:29 GMT; Domain=test.com; Path=/; HttpOnly
```

**Set-Cookie: name=value[; expires=GMTDate][; domain=domain][; path=path][; secure]**

只有cookie的名字和值是必需的。

　　**客户端设置**(在服务器没有设置httpOnly的情况下)

客户端设置cookie的格式和Set-Cookie头中使用的格式一样。如下：

**document.cookie = "name=value[; expires=GMTDate][; domain=domain][; path=path][; secure]"**

若想要添加多个cookie，只能重复执行 document.cookie（如上）。这可能和平时写的 js 不太一样，一般重复赋值是会覆盖的，

但对于cookie，重复执行 document.cookie 并「不覆盖」，而是「添加」（针对「不同名」的）。

### 5、cookie的缺点

安全性：由于cookie在http中是明文传递的，其中包含的数据都可以被他人访问，可能会被篡改、盗用。

大小限制：cookie的大小限制在4kb左右，不适合大量存储。

增加流量：cookie每次请求都会被自动添加到Request Header中，无形中增加了流量。cookie信息越大，对服务器请求的时间越长

### 6 跨域不会发送cookies等用户凭证，如何解决？

```j
本地模拟www.zawaliang.com向www.xxx.com发送带cookie的认证请求，我们需求做以下几步工作：
默认情况下widthCredentials为false，我们需要设置widthCredentials为true：
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://www.xxx.com/api');
xhr.withCredentials = true;
xhr.onload = onLoadHandler;
xhr.send();
请求头，注意此时已经带上了cookie：
GET http://www.xxx.com/api HTTP/1.1
Host: www.xxx.com
User-Agent: Mozilla/5.0 (Windows NT 5.1; rv:18.0) Gecko/20100101 Firefox/18.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip, deflate
DNT: 1
Referer: http://www.zawaliang.com/index.html
Origin: http://www.zawaliang.com
Connection: keep-alive
Cookie: guid=1
设置服务端响应头：
Access-Control-Allow-Credentials: true
如果服务端不设置响应头，响应会被忽略不可用；同时，服务端需指定一个域名（Access-Control-Allow-Origin:www.zawaliang.com），而不能使用泛型（Access-Control-Allow-Origin: *）
响应头：
HTTP/1.1 200 OK
Date: Wed, 06 Feb 2013 03:33:50 GMT
Server: Apache/2
X-Powered-By: PHP/5.2.6-1+lenny16
Access-Control-Allow-Origin: http://www.zawaliang.com
Access-Control-Allow-Credentials: true
Set-Cookie: guid=2; expires=Thu, 07-Feb-2013 03:33:50 GMT
Content-Length: 38
Content-Type: text/plain; charset=UTF-8
X-Cache-Lookup: MISS from proxy:8080
有一点需要注意，设置了widthCredentials为true的请求中会包含远程域的所有cookie，但这些cookie仍然遵循同源策略，所以你是访问不了这些cookie的。
```

