---
title: HTTP 协议详解
date: 2017-07-15 11:55:25
tags: 
categories: 技术向
---

## http？

> * 全称：超文本传输协议(HyperText Transfer Protocol)
> * 作用：设计之初是为了将**超文本标记语言**`(HTML)`文档从Web服务器传送到客户端的浏览器。现在`http`的作用已不局限于`html`的传输。
> * 版本：`http/1.0` `http/1.1*` `http/2.0`

### URL详解

一个示例URL
> http://www.mywebsite.com.sj/test;id=8039?name=sviergn&x=true#stuff

> *Schema:http
  host: www.website.com
  path: /sj/test
  URL params: id=8039
  Query String: name=sviergn&x=true
  Anchor: stuff*
  
- `scheme`: 指定低层使用的协议（例如：`http`,`https`,`ftp`）
- `host`: HTTP服务器的IP地址或者域名
- `port#`: `HTTP`服务器的默认端口是`80`，这种情况下端口号可以省略。如果使用了别的端口，必须指明，例如`http://www.mywebsite.com:8080/`
- `path`: 访问资源的路径
- `url-param`
- `query-string`: 发送给HTTP服务器的数据
- `anchor`: 锚

---

### 无状态的协议

http协议是**无状态**的：
> **同一个客户端的这次请求和上次请求是没有对应关系，对http服务器来说，它并不知道这两个请求来自同一个客户端**

解决方法：`Cookie`机制来维护状态

**既然http协议是无状态的，那么Connection:keep-alive 又是怎么一回事？**
> 无状态协议是指协议对于事物处理没有记忆能力，服务器不知道客户端是甚么状态。从另一方面讲，打开一个服务器上的网页和你之前打开这个服务器上的网页之间没有任何联系。

---

### `http`消息结构
1 . `Request`消息的结构：三部分
> 第一部分叫`Request line`（请求行），第二部分叫`http header`，第三部分是`body`

> + **请求行：**包括http请求的种类，请求资源的路径，http协议版本
> + `http header`:`http`头部信息
> + `body`:发送给服务器的query信息
 当使用的是“`GET`”方法的时候，`body`是为空的（`GET`只能读取服务器上的信息，POST能写入）
> ```http
GET /hope/ HTTP1.1   //---请求行
host: ce.sysu.edu.cn
Accept: */*
Accept-Encoding: gzip,deflate,sdch
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.6
Cache-Control: max-age=0
Cookie: ......
Referer: http://ce.sysu.edu.cn/hope/
User-Agent: Mozilla/5.0(Windows NT 10.0; WOW64) AppleWebKit/537.36(KHTML,like Gecko) Chrome/44.0.2403.130 Safari/537.36
>
---分隔行---
>
POST /hope/ HTTP/1.1    //---请求行
Host: ce.sysu.deu.cn
Accept: */*
Accept-Ecoding: gzip,deflate,sdch
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.6
Cache-Control: mac-age=0
Cookie: ......
Referer: http://ce.sysu.edu.cn/hope/
User-Agent: Mozilla/5.0(Windows NT 10.0; WOW64) AppleWebKit/537.36(KHTML,like Gecko) Chrome/44.0.2403.130 Safari/537.36
>
...body...
>

> ```

---

2 . `Response`消息的结构
也分为三部分，第一部分叫`Request line`，第二部分叫`Request header`，第三部分是 `body`
> + **请求行：**协议版本、状态码、`message`
> + `http header`:`request`头部信息
> + `body`:返回的请求资源主体
> ```
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Encoding: gzip
Content-Length: 4533
Content-Type: text/html
Date: Tue, 15 Dec 2015 00:35:33 GMT
ETag: "2788e6e7d01:0"
Last-Modified: Fri, 04 Sep 2015 13:37:55 GMT
Sever: Microsoft-IIS/7.5
Vary: Accept-Encoding
X-Powered_By: ASP.NET
> 
<!document html>
...
<>
...
> ```

---

### `get`和`postt`区别
`http`协议定义了很多与服务器交互的方法，最基本的有四种，分别是`GET`,`POST`,`PUT`,`DELETE`。一个`URL`地址用于描述一个网络上的资源，而`HTTP`中的`GET`，`POST`,`PUT`,`DELETE`就对应着对这个资源的查，改，增，删4个操作。我们最常见的就是`GET`和`POST`了。`GET`一般用于获取/查询资源信息，而`POST`一般用于更新资源信息。
1 .`GET`提交的数据会放在`URL`之后，以`？`分割`URL` 和传输数据，参数之间以`&`相连，如`EditPosts.aspx?name=test1&id=123456`。`POST`方法是把提交的数据放在`HTTP`包得`body`中。
2 .`GET`提交的数据大小有限制（因为**浏览器对`URL`的长度有限制**），而POST方法提交的数据没有限制
3 .`GET`方式需要使用`Request.QuerryString`来取得变量的值，而`POST`方式通过Request.Form来获取变量的值
4 .`GET`方式提交数据，会带来**安全问题**，比如一个登录界面，通过`GET`方式提交数据时，用户名和密码将出现在`URL`上，如果页面可以被缓存或者其他人可以访问这台机器，就可以从历史记录获得该用户的账号和密码。

[http请求的get与post](https://www.zybuluo.com/yangfch3/note/123476)

---

### 状态码

`Response`消息中的第一行叫做**状态行**，由`http`协议版本号，状态码，状态消息 三部分组成。

状态码用来告诉`http`客户端，`http`服务器是否产生了预期的`Response`.

http/1.1中定义了5类状态码。

状态码由三位数字组成，第一个数字定义了相应的级别

- `1XX`提示信息 - 表示请求已被成功接收，继续处理
- `2XX`成功 - 表示请求已被成功接收，理解，接收
- `3XX`重定向 - 要完成请求必须进行更进一步的处理
- `4XX`客户端错误 - 请求有语法错误或请求无法实现
- `5XX`服务器端错误 - 服务器未能实现合法的请求

> 1.`200 OK`
    请求被成功的完成，所请求的资源发送会客户端
> 2.`302 Found`
    重定向，新的`URL`会在`Response`中的`Location`中返回，浏览器将会使用心得`URL`发出新的`Request`
> 3.`304 Not Modified`
    文档已经被缓存，直接从缓存调用
> 4.`400 Bad Request`
    客户端请求语法错误，不能被服务器所理解
    `403 Forbidden`
    服务器接收到请求，但是拒绝提供服务
    `404 Not Found`
    请求资源不存在
> 5.`500 Internal Server Error`
    服务器发生了不可预期的错误
    `503 Server Unavailable`
    服务器当前不能处理客户端的请求，一段时间后可能恢复正常

---

### http request header

`http`请求头包括很多键值对，这些键值对有什么意义和作用？如何根据功能为他们分一下组呢？

1.`cache`头域

> `If-Modified-Since`
用法：`If-Modified-Since:Tue, 15 Dec 2015 07:17:47 GMT`
把浏览器缓存页面的最后修改时间发送到服务器去，服务器会把这个时间与服务器上实际文件的最后修改时间进行对比。如果时间一致，那么返回304，客户端就直接使用本地缓存文件。如果时间不一致，就会返回200和新的文件内容。客户端接到之后，会丢弃旧文件，把新文件缓存起来，并显示在浏览器中。

> `If-None-Match`
用法：`If-None-Match: "03f2b33c-bfcc1:0"`
`If-None-Match`和`ETag`一起工作，**工作原理是在`http response`中添加`ETag`信息**。当用户再次请求该资源时，将在`http response`中加入`If-None-Match`信息(`ETag`的值)。如果服务器验证资源的`ETag`没有改变(该资源没有更新)，将返回一个304状态告诉客户端使用本地缓存文件。否则将返回`200`和新的资源和`ETag`.使用这样的机制将提高网站的性能

> `Pragma`:`Pragma:no-cache`
`Pragma`只有一个用法，例如：`Pragma: no-cache`
作用：防止页面被缓存，在`http/1.1`版本中，它和Cache-Control:no-cache作用一模一样

> `Cache-Control`
用法：
> - `Cache-Control:Public` 可以被任何缓存所缓存
> - `Cache-Control:Private` 内容只缓存到私有缓存中
> - `Cache-Control:no-cache` 所有内容都不会被缓存
作用：用来指定`Response-Request`遵循的缓存机制

2 . `Client`头域

> `Accept`
用法：Accept: */*,`Accept`: `text/html`
作用：浏览器端可以接受的媒体类型
`Accept: */*`代表浏览器可以处理所有回发的类型，**（一般浏览器给服务器发的都是这个）**
`Accept: text/html`代表浏览器可以接受服务器回发的类型为`text/html`，如果服务器无法返回`text/html`类型的数据，服务器应该返回一个`406`错误(`non acceptable`)

> `Accept-Encoding`
用法：`Accept-Encoding: gzip, deflate`
作用：浏览器申明自己接受的**文件**编码方法，通常指定**压缩方法**是否支持压缩，支持什么压缩方法（`gzip`,`deflate`），（注意：这不是指字符编码）

> `Accept-Language`
用法：`Accept-Language: en-us`
作用：在浏览器中申明自己接收得语言。

    **语言跟字符集的区别**: 中文是语言，中文有多种字符集，比如`big5`,`gb2312`,`gbk`等等；

> `User-Agent`
用法：`User-Agent: Mozilla/4.0......`
作用：告诉http服务器，客户端使用的操作系统和浏览器的名称和版本

> `Accept-Charset`
用法：`Accept-Charset: utf-8`
作用：浏览器申明自己接收得字符集

3.Cookie/login头域

> `Cookie`
 
 ```
Cookie: bdshare_firstime=1439081296143; ASP.NET_SessionId=rcqayd4ufldcke0wkbm1vhxb; pgv_pvi=7361416192; pgv_si=s6686106624; ce.sysu.edu.cn80.ASPXAUTH=9E099592DD5A414BEECD8CF43CFC71664
 ```
作用：**最重要**的**header**，将`cookie`的值发送给`http`服务器

4.`Entity`头域

> `Content-Length`
用法：`Content-Length:38`
作用：发送给http服务器数据的长度

> `Content-type`
用法：`Content-Type:application/x-www-form-urlencoded`
不常出现，一般出现在response头部，用于指定数据文件类型

5.`Miscellaneous`头域

> `Referer`
用法：Referer:http://ce/sysu.edu.cn/hope/
作用：请求爆头域主要用于指定被请求资源的`Internet`**主机和端口号**(默认80)，它通常从`http url`中提取出来的

### http response header

1.`Cache`头域

> `Date`
用法：Date：Sat, 11 Feb 2015 11:24:32 GMT
作用：生成消息的具体时间和日期

> Expires:
用法：`Expires: Tue, 08 Feb 2022 11:24:32 GMT`
作用：浏览器会在指定过期时间内使用本地缓存

> `Vary`
用法：`Vary:Accept-Encoding`

2.`Cookie/Login`头域

> P3P
用法：`Set-Cookie: sc=4c31523a; path=/; domain=.acookie.taobao.com`
作用：非常重要的`header`，用于把`cookie`发送到客户端浏览器，每一个写入`cookie`都会生成一个`Set-Cookie`

3.`Entity`头域
> Etag
用法：`ETag: "03f2b33c0bfcc1:0"`
作用：和`request header`的`If-None-Match`配合使用

> `last-Modified`
用法：`Last-Modified: Wed, 21 Dec 2011 09:09:10 GMT`
作用：用于指示资源的最后修改日期和时间

>`Content-Type`
用法：
> * Content-Type: text/html; charset=utf-8
> * Content-Type:text/html;charset=GB2312
> * Content-Type: image/jpeg

作用：WEB服务器告诉浏览器自己响应的对象类型和字符集

> `Content-Encoding`
用法：`Content-Encoding:gzip`
作用：WEB服务器表明自己使用了什么压缩方法（`gzip`,`deflate`）,压缩响应中的对象

> `Content-Language`
用法：`Content-Language:da`
WEB服务器告诉浏览器自己响应的对象的语言

4.`Miscelaneous`头域

> `Server`
用法：Server：Microsoft-IIS/7.5 
作用：指明http服务器的软件信息

> `X-AspNet-Version`
用法：`X-AspNet-Version: 4.0.30319`
作用：如果网站是用`ASP.NET`开发的，这个header用来表示ASP.NET的版本

> `X-Powered-By`
用法：`X-Powered-By：ASP.NET`
作用：表示网站是用什么技术开发的

5.Transport头域

> `Connection`
用法与作用：
> `Connection：keep-alive`：当一个网页打开完成后，客户端和服务器之间用于传输http数据的TCP连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的连接
> `Connection：close`：代表一个Request完成后，客户端和服务器之间用于传输http数据的TCP连接会关闭，当客户端再次发送Request，需要重新建立TCP连接

6.Location

> Location
用法：`Location：http://ce.sysu.edu.cn/hope/`
作用：用于重定向一个新的位置，包含新的URL地址
 






    