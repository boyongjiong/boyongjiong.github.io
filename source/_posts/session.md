---
title: <摘抄>Session机制详解
date: 2018-03-26 01:03:59
tags:
categories: 技术向
---
# 
摘自——[Session机制详解-啸笑天](http://justsee.iteye.com/blog/1570652)

虽然session机制在web应用程序中被采用已经很长时间了，但是仍然有很多不清楚session机制的本质，以致不能正确的应用这一技术。本文将详细讨论session的工作机制并且对在Java Web application应用中session机制时常见的问题作出解答。

## 一. 术语session
session，中文经常翻译为回话，其本来的含义是指有始有终的一系列动作/消息，比如打电话时从拿起电话拨号到挂断电话这中间的一系列过程可以被称为一个session。有时候我们可以看到这样的话“在一个浏览器会话期间，…”，这里的会话一词用的就是其本义，是指从一个浏览器窗口打开到关闭这个期间①。最混乱的是“用户（客户端）再一次绘画期间”这样一句话，他可能指用户的一系列动作（一般情况下是同某个具体的目的相关的一些列动作，比如从登陆到选购商品到结账登出这样一个网上购物的过程，有时候也被称为一个transaction），然而有时候也可能仅仅是指一次连接，也有可能是指含义①，其中的差别只能靠上下文来推断②。

然而当session一词与网络协议相关联时，他又往往隐含了“面向对象”或“保持状态”这样两个含义，“面向连接”指的是在通信双方在通信之前要先建立一个通信的渠道，比如打电话，直到对方接了电话同心才能开始，与此相对的是写信，在你把信发出去的时候你并不能确认对方的地址是否正确，通信渠道不一定能建立，但对发信人来说，通信已经开始了。“保持状态”则是指通信的一方能够把一系列的消息关联起来，使得消息之间可以相互依赖，比如一个服务员能够认出再次光临的老顾客并且记得上次这个顾客还欠店里一块钱。这一类的例子有”一个TCP session“ 或者”一个POP3 session“③。

而到了web服务器蓬勃发展的时代，session在web开发语境下的语义又有了新的扩展，它的含义是指一类用在客户端和服务器之间保持状态的解决方案④。有时候session也用来指这种解决方案的存储结构，如”把XXX保存在session里⑤“。由于各种用于web开发的语言在一定程度上都提供了对这种解决方案的支持，所以在某种特定语言的语境下，session也被用来指代该语言的解决方案，比如经常把java里提供的javax.servlet.http.HttpSession简称为session⑥。

鉴于这种混乱已不可改变，本文中session一词的运用也会根据上下文有不同的含义，请大家注意分辨。在本文中，使用中文”浏览器会话期间“来表达含义①，使用”session机制“来表达含义④，使用”session“表达含义⑤，使用具体的“HttpSession”来表达含义⑥。

## 二. HTTP协议与状态保持
HTTP协议本身是无状态的，这与HTTP协议本来的目的是相符的，客户端只需要简单的向服务器请求下载某些文件，无论是客户端还是服务器端都没有必要记录彼此过去的行为，每一次请求之间都是独立的，好比一个顾客和一个自动售货机或者一个普通的（非会员制）大卖场之间的关系一样。

然而聪明（或者贪心？）的人们很快发现如果能够提供一些按需生成的动态信息会使web变得更加有用，就想给有线电视上加的点播功能一样。这种需求一方面迫使HTML逐步添加了表单、脚本、DOM等客户端行为，另一方面在服务器端则出现了CGI规范以响应客户端的动态请求，作为传输载体的HTTP协议也添加了文件上载、cookie这些特性。其中cookie的作用就是为了解决HTTP协议无状态的缺陷所做出的努力。至于后来出现的session机制则是又一种在客户端与服务器之间保持状态的解决方案。

让我们用几个例子来描述一些cookie和session机制之间的区别和联系。某人曾经常去的一家咖啡店有喝五杯咖啡免费赠一杯咖啡的优惠，然而一次性消费五杯咖啡的机会微乎其微，这时就需要某种方式来记录顾客具体的消费数量。想象一下其实也无外乎下面几种方案：

1. 该店的店员很厉害，能记住每位顾客的消费数量，只要顾客一走进咖啡店，店员就知道怎么对待了。这种做法就是协议本身支持状态。
2. 发给顾客一张卡片，上面记录着消费的数量，一般还有个有效期限。每次消费时，如果顾客出示这张卡片，则此次消费就会与以前或以后的消费相联系起来。这种做法就是在客户端保持状态。
3. 发给顾客一张会员卡，除了卡号之外什么信息也不记录，每次消费时，如果顾客出示该卡片，则店员在店内的记录本上找到这个卡号对应的记录添加一些消费信息。这种做法就是在服务器端保持状态。

由于HTTP协议是无状态的，而出于种种考虑也不希望使之成为有状态的，因此，后面的两种方法就成为现实的选择。具体来说cookie机制采用的是在客户端保持状态的方案，而session机制采用的是在服务器端保持状态的方案。同时我们看到，由于采用服务端保持状态的方案在客户端也需要保存一个标识，所以session机制可能需要借助于cookie机制来达到保存标识的目的，但实际上还有其它选择。

## 三. 理解cookie机制
cookie机制的基本原理就如上面的例子一样简单，但是还有几个问题需要解决：“会员卡”如何分发；“会员卡”的内容，以及客户如何使用“会员卡”。

正统的cookie分发是通过扩展HTTP协议来实现的。服务器通过在HTTP的响应头中加上一行特殊的指示以提示浏览器按照指示生成相应的cookie。然而纯粹的客户端脚本JavaScript或者VBScript也可以生成cookie。

而cookie的使用是有浏览器按照一定的原则在后台自动发送给服务器的。浏览器检查所有存储的cookie，如果某个cookie所声明的作用范围大于等于将要请求的资源所在的位置，则把该cookie附在请求资源的HTTP请求头上发送给服务器。意思是麦当劳的会员卡只能在麦当劳的店里出示，如果某家分店还发行了
自己的会员卡，那么进这家店的时候，除了要出示麦当劳的会员卡，还要出事这家店的会员卡。

cookie的内容主要包括：名字、值、过期时间、路径和域。

其中域可以指定某一个域比如.google.com，相当于总店招聘，比如宝洁公司，也可以指定一个域下的具体某台机器比如www,google.com，可以用飘柔来做比。

路径就是跟在域名后面的URL路径，比如/或者 /foo 等等，可以用某飘柔专柜做比。路径与域合在一起就构成了cookie的作用范围。如果不设置过期时间，则表示这个cookie的生命期为浏览器会话期间，只要关闭浏览器窗口，cookie就消失了。这种生命期为浏览器会话期间的cookie被称为会话cookie。会话cookie一般不存储在硬盘上，而是保存在内存里，当然这种行为并不是规范规定的。如果设置了过期时间，浏览器就会把cookie保存到硬盘上，关闭后再次打开浏览器，这些cookie仍然有效直到超过设定的过期时间。

存储在硬盘上的cookie可以在不同的浏览器进程间共享，比如两个ie窗口，。而对于保存在内存中的cookie，不同的浏览器有不同的处理方式。对于IE，在一个打开的窗口上按Ctrl+N（或从文件菜单）打开的窗口可以与原窗口共享，而使用其他方式新开的IE进程则不能共享已经打开的窗口的内存cookie；对于Mozilla Firefox0.8，所有的进程和标签页都可以共享同样的cookie。一般来说是用JavaScript的window.open打开的窗口会与原窗口共享内存cookie。浏览器对于会话cookie的这种只认cookie不认人的处理方式经常采用session机制的web应用程序开发者造成很大的困扰。

下面就是一个google设置cookie的响应头的例子：
```
    HTTP/1.1 302 Found 
    Location: http://www.google.com/intl/zh-CN/ 
    Set-Cookie: PREF=ID=0565f77e132de138:NW=1:TM=1098082649:LM=1098082649:S=KaeaCFPo49RiA_d8; 
    expires=Sun, 17-Jan-2038 19:14:07 GMT; path=/; domain=.google.com 
    Content-Type: text/html 
```

## 四. 理解session机制
session机制是一种服务器端的机制，服务器使用一种类似于散列表的结构（也可能就是使用的散列表）来保存信息。

当程序需要为某个客户端的请求创建一个session的时候，服务器首先检查了这个客户端的请求里是否已包含了一个session标识 - 称为session id，如果已经包含一个session id则说明以前已经为此用户创建过session，服务器就按照session id 把这个session检索出来使用（如果检索不到，可能会新建一个），如果客户端请求不包含session id，则为此客户端创建一个session并且生成一个与此session相关联的session id，session id的值应该是一个既不会重复，又不容易被找到规律以仿制的字符串，这个session id将被在本次响应中返回给客户端保存。

保存这个session id的方式可以采用cookie，这样在交互过程中浏览器可以自动的按照规则把这个标识发给服务器。一般这个cookie的名字都是类似于SEEESSIONID, 而比如weblogic对于web应用程序生成的cookie，JSESSIONID=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764，它的名字就是JSESSIONID。

由于cookie可以被人为的禁止，必须有其它机制以便在cookie被禁止时仍然能够把session id传递回服务器，经常使用的一种技术叫做URL重写，就是把session id直接附加在URL路径的后面，附加方式也有两种，一种是作为URL路径的附加信息，表现形式为：
http://...../xxx;jsessionid=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764 

另一种是作为查询字符串附加在URL后面，表现形式为：http://...../xxx?jsessionid=ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764 
这两种方式对于用户来说是没有区别的，只是服务器在解析的时候处理的方式不同，采用第一种方式也有利于把session id的信息与正常程序参数区分开来。
为了在整个交互过程中始终保持状态，就必须在每个客户端可能请求的路径后面都包含这个session id。

另一种技术叫做表单隐藏字段。就是服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把session id传递回服务器。比如下面的表单：

```
<form name="testform" action="/xxx"> 
  <input type="text"> 
</form> 
```

在被传递给客户端之前被改写成：

```
<form name="testform" action="/xxx"> 
  <input type="hidden" name="jsessionid" value="ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764"> 
  <input type="text"> 
</form> 

```

但是这种技术在现在已较少使用，实际上这种技术可以简单地用对action应用URL重写来代替。

在谈论session机制的时候，常常听到的一种误解“只要关闭浏览器，session就消失了”。其实可以想象一下会员卡的例子，除非顾客主动对店家提出销卡，否则店家绝对不会轻易删除顾客的资料。对session来说也一样，除非程序通知服务器删除一个session，否则服务器会一直保留，程序一般都是在用户做logout的时候发个指令去删除session。然而浏览器从来不会主动在关闭之前通知服务器它将要关闭，因此服务器根本不会有机会知道浏览器已经关闭了，之所以会出现这种错觉，是大部分session机制都使用会话cookie来保存session id，而关闭浏览器之后这个session id就消失了，再次连接服务器时，也就无法找到原来的session。如果服务器设置的cookie被保存到硬盘上，或者使用某种手段改写了浏览器发出的HTTP请求头，把原来的session id发送给服务器，则再次打开浏览器仍然能够找到原来的session。

恰恰是由于关闭浏览器不会导致session被删除，迫使服务器为session设置了一个失效时间，当距离客户端上一次使用session的时间超过这个失效时间时，服务器就可以认为客户端已经停止了活动，才会把session删除以节省存储空间。

## 五. HttpSession在何时被创建
1. session在何时被创建
一个常见的误解是以为session在有客户端访问的时候就被创建，然而事实是直到某server端程序调用HttpServleRequest.getSession(true)这样的语句时才被创建，注意如果JSP没有显示的使用<% @page session=‘false’ %>关闭session，则JSP文件在编译成Servlet时将会自动加上这样一条语句 HttpSession session = HttpServletRequest.getSession(true); 这也是JSP中隐含的session对象的来历。
由于session会消耗内存资源，因此，如果不打算使用session，应该在所有JSP中关闭它。

2. session何时被删除
综合前面的讨论，session在下列情况下被删除：a. 程序调用HttpSession.invalidate(); b. 距离上一次收到客户端发送的session id 时间间隔超过了session的超时设置; c. 服务器进程被停止（非持久session）

3. 如何做到在浏览器关闭时删除session
严格的讲，做不到这一点。可以做一点努力的办法是在所有的客户端页面里使用JavaScript代码window.onclose来监视浏览器的关闭动作，然后向服务器发送一个请求来删除session。但是对于浏览器崩溃或者强行杀死进程这些非常规手段依然无能为力。

4. 有个HttpSessionListener是怎么回事
你可以创建这样的listener去监听session的创建和销毁事件，使得在发生这样的事件时你可以做一些相应的工作。注意是session的创建和销毁动作触发listener，而不是相反。类似的与HttpSession有关的listener还有HttpSessionBindingListener、HttpSessionActivationListener和HttpSessionAttributeListener。

5. 存放在session中的对象必须是可序列化的吗
不是必需的。要求对象可序列化只是为了session能够在集群中被复制或者能够持久保存或者在必要时server能够暂时把session交换出内存。在WeblogIC Server 的session中放置一个不可序列化的对象在控制台上会收到一个警告。我所用过的某个IPlanet版本如果session中有不可序列化的对象，在session销毁时会有一个Exception，很奇怪。

6. 如何才能正确的应付客户端禁止cookie的可能性
对所有的URL使用url重写，包括超链接，form的action，和重定向的url。

7. 开两个浏览器窗口访问应用程序会使用同一个session还是不同的session
参见第三小节对cookie的讨论，对session来说是只认ID 不认人，因此不同的浏览器，不同的窗口打开方式以及不同的cookie存储方式都会对这个问题的答案有影响。

8. 如何防止用户打开两个浏览器窗口操作导致session混乱
这个问题与防止表单多次提交是类似的，可以通过设置客户端的令牌来解决。就是在服务器每次生成一个不同的id返回给客户端，同时保存在session里，客户端提交表单时必须把这个id也返回服务器，程序首先比较返回的id与保存在session里的值是否一致，如果不一致则说明本次操作已经被提交过了。可以参看《J2EE核心模式》关于表示层模式的部分。需要注意的是对于使用JavaScript window.open打开的窗口，一般不设置这个id，或者使用单独的id，以防止主窗口无法操作，建议不要在window.open打开的窗口中做修改操作，这样就可以不用设置了。

9. 为什么在Weblogic Server中改变session的值后要重新调用一次session.setValue
做这个动作主要是为了在集群环境中提示WeblogIC server session中的值发生了改变，需要向其它服务器进程复制新的session值。

10. 为什么session不见了
排除session正常失效的因素之外，服务器本身的可能性是微乎其微的，出现这一问题的大部分原因都是程序的错误，最常见的就是在一个应用程序中去访问另外一个应用程序。