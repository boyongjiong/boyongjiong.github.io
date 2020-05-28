---
title: Promise
date: 2018-05-13 12:51:26
tags:
categories: 技术向
---

## Promise 与事件循环
可以简单的认为：
> 立即 resolve 的 Promise 对象，是在本轮“事件循环”（event loop）的结束时，而不是在下一轮“事件循环”的开始时。

```
setTimeout(() => {
	console.log(1);
}, 0);

new Promise(function executor(resolve) {
	console.log(2);
	for (let i = 0; i < 10000; i ++) {
		i === 9999 && resolve();
	}
	console.log(3);
}).then(() => {
	console.log(4);
});
console.log(5);
// 2 3 5 4 1

// 分析
// 2 3 5
// 本轮事件循环结束
// 4
// 本轮事件循环结束时， `.then()`
// 下一轮事件循环开始
// 1
```

## Promise 内的 return
	1. 与普通函数无二致：一旦遇到 return 函数便执行完毕，跳出。
	2. 在没有遇到 `return` 之前，Promise 内的代码能一直执行。

## .then()/.catch() 返回值
两个方法返回的都是一个新的 Promise 对象，可以轻易实现 *回调链*。

### 回调函数没有显示返回值
`.then ` 返回一个新的 Promise 对象，该 Promise 对象的：
	1. [[PromiseStatus]] 为 `resolved`
	2. [[PromiseValue]] 为 `undefined`

```javascript
const a = new Promise((resolve) => {resolve(1)});
const b = a.then((value) => { console.log(value + 1); });

b;
// Promise {
// 	[[PromiseStatus]]: 'resolved',
// 	[[PromiseValue]]: 'undefined',
// }
```

### 回调函数返回非 Promise 值
`.then()` 返回一个新的 Promise 对象，该对象的：
	1. [[PromiseStatus]] 为 `resolved`
	2. [[PromiseValue]] 为 `then` 内回调函数的返回值

```javascript
const obj = {key: 'value'};
const a = new Promise((resolve) => {resolve(obj)});
const b = a.then((value) => { return value; });

b;
// Promise {
//    [[PromiseStatus]]: "resolved", 
//    [[PromiseValue]]: {
//        key: 'value'
//    }
// }
```

### 回调函数返回值为一个新的 Promise
`.then()` 返回值为一个 Promise 对象，该 Promise 对象即为回调函数返回的 Promise 对象。
```
const a = new Promise((resolve) => { resolve(1); });
const b = a.then((value) => {
	return new Promise((resolve, reject) => {
		resolve(value + 1);
	});
});

b;
// Promise {
//   [[PromiseStatus]]: "resolved",
//   [[PromiseValue]]: 2
// }
```

## Promise 错误处理
Promise 错误能被 `.then()` 或 `.catch()` 处理，这两个对错误的类似于`try...catch`的错误处理。

### 未处理的 reject
一个 Promise 内执行了 `reject`， 该 Promise 对象的状态由 `pending` 变为 `rejected`,此时根据是否有 `then()` （第二个回调函数参数）或 `catch()` 来处理这个 `rejection` 会有不同的表现：

在 `.then()` （第二个回调函数参数） 或 `.catch()` 中处理了 `rejection`
	JavaScript Runtime 不会报 `Uncaught (in Promise)` 错误
一直到代码执行完毕 `rejection` 都没有处理
	JavaScript Runtime 报 `Uncaught (in Promise)` 错误
	Runtime 不知道何处会有处理的逻辑，所以在最后才会通报1中的错误
	代码会继续执行直到完毕，不退出

注意： 以后如果有未处理的`rejection` 会导致程序 `non-zero exit`（在诸如 `node server` 这些运行时是致命的），所以确保 Promise 的错误有处理。

### Promise 内同步错误
*错误在 resolve() 或 reject() 之前*
Promise 内的运行错误相当于： `reject(error)` + `return`

有以下几个特征：
	1. Promise 内的代码 *不再往下执行*
	2. 相当于立即执行 `reject(error)` 并 `return` （然后根据有无处理 rejection 就划归为上文相关问题）
	
```
// bbb 未定义
var a = new Promise((resolve, reject) => {
    console.log(bbb);
    console.log('aaa');
    resolve(1);
});
// 输出
// Uncaught (in promise) ReferenceError: bbb is not defined
//    at Promise (<anonymous>:1:55)
//    at Promise (<anonymous>)
//    at <anonymous>:1:9
a;
// 输出
// Promise {
//     [[PromiseStatus]]: "rejected", 
//     [[PromiseValue]]: ReferenceError: bbb is not defined
//         at Promise (<anonymous>:1:55)
//         at Promise (<anonymous>)
//     …
// }
```

### 错误在 resolve() 或 reject() 后
Promise 内的运行错误相当于:  `reject(error)` + `return`

特征：
	1. resolve() 与 reject() 后的代码继续执行，直到遇到错误或 `return`
	2. 错误被忽略，不会被 Runtime 捕获

```javascript
// foo 未定义
const a = new Promise((resolve, reject) => {
	resolve(1);
	console.log(foo);
	console.log('baz');
});
// Runtime 并没有通报 console.log(foo) 这里的错误
a;
// 输出
// Promise {
//     [[PromiseStatus]]: "resolved",
//     [[PromiseValue]]: 1
// }
```

## Promise.all([p1, p2, p3])
有以下几点需要注意：
	1. 接受一个具有 Iterator 接口的可遍历类型（典型的： 如数组）作为参数
	2. `Promise.all()` 会遍历传入的可遍历结构，如果其中元素（p1, p2, p3…）不是 Promise，就会先调用下面讲到的 `Promise.resolve`方法，将参数转为 Promise 实例
	3. 返回一个新的 Promise 对象

关于 Promise 的状态与数据：
	1. 只有 p1, p2, p3 的状态都变成 `fulfilled`，新 Promise 对象的状态才会变成 `fulfilled`，*此时 p1, p2, p3 的返回值（有次序的）组成一个数组，传递给 p 的回调函数*。
	2. 只要 p1, p2, p3 之中有一个被 rejected，p 的状态就变成 rejected，此时*第一个被 reject 的实例的返回值，会传递给新 Promise 对象的回调函数*。

## Promise.race([p1, p2, p3])
与 `Promise.all()` 类似，但是新 Promise 的状态与回调函数数据来源于 p1, p2, p3 中最先状态发生变化的那个。

## Promise.resolve() —— 万物皆可 Promise
（1） *参数是一个 Promise 实例*
	如果参数是 Promise 实例，那么 `Promise.resolve` 将不做任何修改、原封不动地返回这个实例。

（2） *参数是一个`thenable`对象*
	`thenable` 对象指的是具有 `then` 方法的对象（在 Java 里通常形象地说是一个类实现了 `thenable` 接口），比如下面这个对象。

```javascript
// 便于理解，将其视为一个伪 Promise
let thenable = {
  then: function(resolve, reject) {
    // 在这里可以 resolve 与 reject
  }
};
```

（3） *参数是一个不具有 then 方法的对象，或者根本就不是对象*
	返回一个新的 Promise 对象
		1. [[PromiseStatus]] 为 Resolved
		2. [[PromiseValue]] 为传入的参数

（4） *不带入任何参数*
	相当于 （3）中传入 undefined

## 两个额外的好用方法
```javascript
// 用于回调链尾部的收尾处理
Promise.prototype.done = function (onFulfilled, onRejected) {
	this.then(onFulfilled, onRejected)
	.catch(function (resson) {
		// 抛出一个全局错误
		setTimeout(() => { throw reason }, 0);
	});
}

// 用于在回调链尾部接一个普通的回调函数
Promise.prototype.finally = function (callback) {
	let P = this.constructor;
	return this.then(
		value => P.resolve(callback()).then(() => value),
		reason => P.resolve(callback()).then(() => { throw reason })
	);
};
```
