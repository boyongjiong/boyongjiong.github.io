---
title: 理解和使用JavaScript中的回调函数
date: 2017-08-26 12:51:26
tags:
categories: 技术向
---

## 实现回调函数的基本原理
回调函数并不复杂，但是在我们开始创建并使用回调函数之前，我们应该熟悉几个实现回调函数的基本原理。

### 使用命名或匿名函数作为回调
在前面的jQuery例子以及forEach的例子中，我们使用了在参数位置定义的匿名函数作为回调函数。这是在回调函数使用中的一种普遍的魔术。另一种常见的模式是定义一个命名函数并将函数名作为变量传递给函数。比如下面的例子：

```
// 全局变量
var allUserData = [];

// 普通的logStuff函数，将内容打印到控制台
function logStuff(userData) {
  if(typeof userData === "string") {
    console.log(userData);
  } else if(typeof userData === "object") {
    for(var item in userData) {
      console.log(item + ":" + userData[item]);
    }
  }
}

// 一个接收两个参数的函数，后面一个是回调函数
function getInput(options, callback) {
  allUserData.push(options);
  callback(options);
}

// 当我们调用getInput函数时，我们将logStuff作为一个参数传递给它
// 因此logStuff将会在getInput函数内被回调（或执行）

getInput({name: "Rich", speciality: "JavaScript", logStuff});
```
## 传递参数给回调函数

既然回调函数在执行时仅仅是一个普通函数，我们就能给它传递参数。我们能够传递任何包含它的函数的属性（或者全局属性）作为回调函数的参数。在前面的例子中，我们将options作为一个参数传递给了回调函数。现在我们传递一个全局变量和一个本地变量：

```
// 全局变量
var generalLastName = "Cliton";

function getInput(options, callback) {
  allUserData.push(options);
  //将全局变量generalLastName传递给回调函数
  callback(generalLastName, options);
}
```

### 在执行之前确保回调函数是一个函数
在调用之前检查作为参数被传递的回调函数确实是一个函数，这样的做法是明智的。同时，这也是一个实现条件回调函数的最佳时间。

我们来重构上面例子中的getInput函数来确保检查是恰当的。

```
function getInput(options, callback) {
  allUserData.push(options);
  
  // 确保callback是一个函数
  if(typeof callback === "function") {
    // 调用它，既然我们已经确定了它是可调用的
    callback(options);
  }
}
```
如果没有适当的检查，如果getInput的参数中没有一个回调函数或者传递的回调函数事实上并不是一个函数，我们的代码将会导致运行错误。

### 使用this对象的方法作为回调函数时的问题

当回调函数是一个this对象的方法时，我们必须改变执行回调函数的方法来保证this对象的上下文。否则如果回调函数被传递给一个全局函数，this对象要么指向全局window对象（浏览器环境下），要么指向包含方法的对象。

//定义一个拥有一些属性和一个方法的对象
//我们接着会把方法作为回调函数传递给另一个函数

```
var clientData = {
  id: 094545,
  fullName: "Not Set",
  //setUserName 是一个在clientData对象中的方法
  setUserName: function(firstName, lastName){
    //这指向了对象中的fullName属性
    this.fullName = firstName + " " + lastName;
  }
}

function getUserInput(firstName, lastName, callback) {
  //在这做些什么来确认firstName/lastName
  
  // 现在存储names
  callback(firstName, lastName);
}
```

在下面你的代码例子中，当clientData.setUserName被调用执行时，this.fullName并没有设置clientData对象中的fullName属性。相反，它将设置window对象中的fullName属性，因为getUserInput是一个全局函数。全局函数中的this对象指向window对象。

```
getUserInput("Barack", "Obama", clientData.setUserName);

console.log(clientData.fullName);  // Not Set

// fullName属性将在window对象中被初始化
console.log(window.fullName); //Barack Obama
```

### 使用Call和Apply函数来保存this

我们可以使用Call或者Apply函数来修复上面的问题。我们知道每个JavaScript中的函数都有两个方法：Call和Apply。这些方法被用来设置函数内部的this对象以及给此函数传递的变量。

call接收的第一个参数为被用来在函数内部当做this的对象，传递给函数的参数被挨个儿传递（使用逗号分开）。Apply函数的第一个参数也是在函数内部作为this的对象，然而最后一个参数却是传递给函数的值的数组。

看我们如何使用Apply修复前面的例子中出现的问题：
```
// 注意我们增加了新的参数作为回调对象， 叫做callbackObj
function getUserInput(firstName, lastName, callback, callbackObj) {
  // 在这里来确认名字
  callback.apply(callbackObj, [firstName, lastName]);
}
```

使用Apply函数正确的设置了this对象，我们现在能正确的执行callback并在clientData对象中正确设置fullName属性了：

```
// 我们将clientData.setUserName方法和 clientData对象作为参数，clientData对象会被Apply方法使用来设置this对象

getUserName("Barack", "Obama", clientData.setUserName, clientData);

// clientData中的fullName属性被正确的设置
console.log(clientUser.fullName);  // Barack Obama
```
我们也可以使用Call函数:

```
// 注意我们增加了新的参数作为回调对象， 叫做callbackObj
function getUserInput(firstName, lastName, callback, callbackObj) {
  // 在这里来确认名字
  callback.call(callbackObj, firstName, lastName);
}
```

### 允许多重回调函数
我们可以将不止一个的回调函数作为参数传递给一个函数，就像我们能够传递不止一个变量一样。这里有一个jQuery中ajax的例子

```
function successCallback() {
  // 在信息被成功接收之后做点什么
}

function completeCallback() {
  // 在完成之后做点什么
}

function errorCallback() {
  // 当错误发生时做点什么
}

$.ajax({
  url: "http://fiddle.jshell.net/favicon.png",
  success: successCallback,
  complete: completeCallback,
  error: errorCallback
});
```

## "回调地狱"问题以及解决方案

在执行异步代码时，无论以什么顺序简单的执行代码，经常情况会变成许多层级的回调函数堆积以致代码变成以下的情形。这些杂乱无章的代码叫做回调地狱（因为回调太多而导致看懂代码变得异常困难），如下：
```
var p_client = new Db('integration_tests_20', new Server("127.0.0.1", 27017, {}), {'pk':CustomPKFactory});
p_client.open(function(err, p_client) {
  p_client.dropDatabase(function(err, done) {
    p_client.createCollection('test_custom_key', function(err, collection) {
      collection.insert({'a':1}, function(err, docs) {
        collection.find({'_id':new ObjectID("aaaaaaaaaaaa")}, function(err, cursor) {
          cursor.toArray(function(err, items) {
            test.assertEquals(1, items.length);

            // Let's close the db
            p_client.close();
          });
        });
      });
    });
  });
});

```

**关于这个问题的两种解决方案**

 - 给你的函数命名并传递他们的名字作为回调函数，而不是主函数的参数中定义匿名函数
 - 模块化 将你的代码分隔到模块中，这样你就可以导出一块代码来完成特定的工作。然后你可以在你的巨型应用中导入模块