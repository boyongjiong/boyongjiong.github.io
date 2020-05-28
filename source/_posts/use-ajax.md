---
title:  使用 Ajax($.ajax)
date: 2017-07-18 09:52:22
tags:
categories: 技术向
---

## 工作原理
首先需要向服务器发送一个请求，然后调用一个服务，接着返回数据。不过，与通过响应提交表单，载入新页面或使用iframe和远程脚本不同，Ajax实在同一个Web页面文档中处理这些活动的。

为了管理服务器端和客户端之间的异步通信，需要一特殊的对象，或者是微软早期提供的ActiveXObject，或者是更通用和标准的XMLHttpRequest。异步通信的意思是当发送请求时，但是客户端并不需要停止、保持，并且一直等待服务器端的处理完成。在此同时，客户端将指定一个在请求状态发生改变时调用的函数。

在这个函数中，将检查请求的状态，如果请求已经处理完成，并且没有发现服务器端出现错误，那么将对从该服务器返回的数据进行处理并通常以某种形式输出到页面上。

对于Web页面的访问者而言，这些活动看起来就像在页面中发生的一样，而不是通过客户端/服务器端交互。在访问服务器时，除非用户在检测服务器请求，或者应用程序提供了一些操作提示，否则用户是无法看到可视化指示器的。

---

## Hello Ajax World
最常见的Ajax应用是基于用户触发的事件返回一些数据

第一个例子在启动完服务器后，浏览器输入http://localhost/httpDemo/helloAjax/访问

### XMLHttpRequest对象及请求的准备与发送
在上面的项目中的getXMLHttp中，定义了一个初始值为null的局部变量xmlhttp，代码先检查是否作为window对象的属性支持XMLHttpRequest对象。如果支持，则创建一个新的XMLHttpRequest对象并将它设置为局部变量。如果通过老版本的IE访问该页面，则创建一个ActiveX对象并将其赋给相同的局部变量，最后改函数的返回值就是这个局部变量。

### XMLHttpRequeat对象的方法
XMLHttpRequest是一个很简单的对象，提供了一些方法和属性

 - open
 open方法的语法是open(method, url[,async,username,password]).它用来打开到指定URL的连接（通过指定的方法）。可选的参数有3个：async参数用来将请求设置成异步模式（true，默认值）或同步（false）；username和password用来指定服务器所需的用户名和密码。
 - setRequestHeader
该方法为请求的标头添加标签/值对
 - send
用来发送该请求，同事附上相关联的数据
 - getAllResponseHeaders
它将以字符串的形式返回所有HTTP响应的标头，其中的信息包含激活超时值
content-type、服务相关信息和日期信息
 - getResponseHeader
它将返回指定HTTP响应的标头
 - abort
abort方法的语法为abort(),用来终止当前请求

### XMLHttpRequeat对象的属性
| 属性 | 用途 |
| ---- | ---- |
|onreadystatechange| 当请求的ready状态，用来保存所调用函数的句柄|
|readyState|5个可选值：0表示请求未初始化;1表示开放请求;2表示请求已发送;3表示正在接收响应;4表示响应完成载入|
|responesText|文本格式的响应|
|responseXML|XML格式的响应，然后可以将其视为有效XML处理|
|status|返回请求的状态，如404/500，或者希望的200（表示一切正常）|
|statusText|以文字表示请求状态|

### HTTP请求中常用的方法：

 - GET
从服务器请求数据
 - POST
向服务器发送数据
 - DELETE
删除指定资源的数据
 - PUT
保存指定资源的数据
 - HEAD
与GET类似，但是不返回响应正文(用于获取信息)
 
## Ajax数据：XML或JSON

### 数据的MIME类型
上面的例子中，服务器应答是以html/text格式返回的，应用程序可以通过XMLHttpRequest的responseText属性访问应答信息；而当使用XML格式时，重要的是：服务器端应用程序返回的响应内容是MIME(Multipurpose Internet Mail Extension，对用途Internet邮件扩展)类型的text/xml属性，否则它最终不会出现在XMLHttpRequset对象的responseXML容器中。

#### 在服务器端生成XML数据
用于创建Web服务的大多数编程语言（如PHP）大多都提供了一些库，通过他们生成XML数据是很容易的

#### 在客户端处理XML数据

```
//处理返回的数据
function getCities(){
  if(xmlHttpRequest.readyState == 4 && xmlHttpObj.status === 200){
    try{
      //访问城市选项
      var citySelection = document.getElementById("citySelection");
      
      //在外部XML文档中也使用DOM方法
      var citynodes = xmlHttpObj.responseXML.getElementByTagName("city");
      
      //遍历每个city节点
      for(var i=0; i<citynodes.length; i++){
        var name = value = null;
        
        //查找相关联城市的显示名称和选项值
        for(var j=0; j<citynodes[i].childNodes.length; j++){
          var elem = citynodes[i].childNodes[j].nodeName;
          var nodevalue = citynodes[i].childNodes[j].firstChild.nodeValue;
          
          if(elem == "value"){
            value = nodeValue;
          } else {
            name = nodevalue;
          }
        }
        
        //为下拉列表添加新选项
        citySelection.options[i] = new Option(name, value);
    }
    } catch(e) {
      alert(e.message);
    } 
  } else if(xmlHttpObj.readyState ==4 && xmlHttpObj.status !=200){
    document.getElementById("cities").innerHTML = "Error:preSearch Failed!";
  }
}

```

---

### JSON

JSON是一种“轻量级的数据交换格式”。不是作为逗号分隔的字符串驶入链式引用，或者必须处理XML的复杂性，JSON提供了一种格式，该格式可以把服务器端结构转化成在实际中可以立即使用的JavaScript对象。

JSON实际上使用JavaScript语法定义对象。对于对象，该语法包括一对大括号以及其中的成员

```
object{ }  or object{ string: value...}
```
对于数组而言，他是由一组元素加上一对方括号组成的：
```
array[] or array[value, value, ..., value]
```

JavaScript和JSON之间的一个主要区别在于JavaScript可以用单引号或双引号来引用字符串，而JSON只支持双引号。

## jQuery.ajax(url,[settings])

说明：执行一个异步的http(ajax)请求。

| 参数 | 类型 | 说明 |
| ---- | ---- | ---- |
| url | string | 一个用来包含发送请求的URL字符串 |
| url | string | 一个以"{键:值}"组成的ajax请求设置。所有选项都是可选的。可以使用%。ajaxSetup()设置任何默认参数。 |

### [jQuery.ajax([settings])](http://www.jquery123.com/jQuery.ajax/)

点击上面的标题链接到jQuery API 中文文档 ajax 说明页面
