---
title: JavaScript 异步方案整理
date: 2020-09-04 12:22:25
tags: 
categories: 技术向
---

> 相关章节
[图解JavaScript——代码实现（Object.create()、flat()等十四种代码原理实现不香吗？）](https://mp.weixin.qq.com/s/N_QKDJqMX82cjvI1MYnwXQ)
[图解JavaScript——基础篇](https://mp.weixin.qq.com/s/jOjUPR-t-wX0TI9rD3P45Q)
[图解 JavaScript——进阶篇](https://mp.weixin.qq.com/s/sAKkyR9XNtQIYWL6F0PDIA)
[图解23种设计模式（TypeScript版）](https://mp.weixin.qq.com/s/GzPqSgna9Fwqal-5Oyg5EA)
> 参考链接
[Promise/A+](https://promisesaplus.com/)
[Promise的源码实现](https://segmentfault.com/a/1190000018428848?utm_source=tag-newest)
[ES6 入门教程](https://es6.ruanyifeng.com/)

## 六种异步方案
> 本节主要阐述六种异步方案：回调函数、事件监听、发布/订阅、Promise、Generator 和 Async。其中重点是发布/订阅、Promise、Async 的原理实现。

![1](https://tvax1.sinaimg.cn/large/a9034e0egy1gjvtm488x4j213m122amm.jpg)

### 回调函数
异步编程最基本的方法，把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。

 * 优点：简单、容易理解和实现
 * 缺点：多次调用会使代码结构混乱，形成回调地狱
```javascript
function sleep(time, callback) {
  setTimeout(() => {
    // 一些逻辑代码
    callback();
  }, time);
}
```

### 事件监听
异步任务的执行不取决于代码的执行顺序，而取决于某个事件是否发生。

 * 优点：易于理解，此外对于每个事件可以指定多个回调函数，而且可以“去耦合”，有利于实现模块化
 * 缺点：整个程序都要变成事件驱动型，运行流程会变得很不清晰
```javascript
dom.addEventListener('click', () => {
  console.log('dom 被点击后出发！！！');
});
```

### 发布/订阅
发布/订阅模式是在观察着的基础上，在目标和观察者之间增加一个调度中心。订阅者（观察者）吧自己想要订阅的时间注册到调度中心，当该事件触发的时候，发布者（目标）发布该事件到调度中心，由调度中心统一调度订阅者注册到调度中心的处理代码。

### Promise
Promise 是异步编程的一种解决方案，是为解决回调函数回调地狱问题而提出的，它不是新的语法功能，而是一种新的写法，允许将回调函数的嵌套改为链式调用。

 * 优点：将回调函数的嵌套改为链式调用；使用 then 方法之后，异步任务两端执行看的更加清楚
 * 缺点：Promise 的最大问题是代码冗余，原来的任务被 Promise 包装了一下，不管什么操作，一眼看去都是一堆 then，原来的语义变得不清楚
```javascript
const promise = new Promise((resolve, reject) => {
  if (/* 如果异步成功 */) {
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then((value) => {
  // ...success
}, (reason) => {
  // ...failure
});
```

### Generator
Generator 函数时 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。其最大的特点是可以空时函数的执行。

 * 优点：异步操作表示很简洁，此外可以控制函数的执行。
 * 缺点：流程管理不方便，不能实现自动化的流程管理
```javascript
function* genF() {
  yield 'come on!';
  yield 'Front End Engineer';
  return 'goood';
}

const gF = genF();
gF.next(); // {value: 'come!', done: false}
gF.next(); // {value: 'Front End Engineer', done: false}
gF.next(); // {value: 'good', done: true}
gF.next(); // {value: undefined, done: true}
```

### Async
ES2017 标准引入了 async 函数，使得异步操作变得更加方便。简言之，该函数就是 Generator 函数的语法糖。

 * 优点：内置执行器，可以自动执行；语义相比 Generator 更加清晰；返回值是 Promise，比 Generator 函数的返回值是 It二胺投入对象操作更加方便。
 * 缺点：增加学习成本
```javascript
async function asyncFun() {
  await func1();
  await func2();
  return '666';
}

function func1() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('888');
    }, 100);
  }).then((value) => {
    console.log(value);
  });
}

function func2() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('777');
    }, 100);
  }).then((value) => {
    console.log(value);
  });
}

asyncFun().then((value) => {
  console.log(value);
});

// 888
// 777
// 666
```

## Promise 原理实现
不管是实际开发还是面试过程中，都会遇到 Promise 的实现原理，下面我们根据 PromiseA+规范来进行实现，然后对其相关的静态方法（Promise.resolve(), Promise.reject(), Promise.all(), Promise.race()）和实例方法 （Promise.prototype.catch(), Promise.prototype.finally()）进行实现。
![2](https://tva4.sinaimg.cn/large/a9034e0egy1gjvtm4jzf3j20uu0legpu.jpg)

### 思考一下
首先用一幅图来展示我考虑实现这个函数的思路
![3](https://tva2.sinaimg.cn/large/a9034e0egy1gjvtm4qvh6j21110retdp.jpg)

### 根据 Promise/A+ 规范实现 Promise
人家有相关标准，我们就要遵守，笔记遵纪守法才是好公民，现在只能硬着头皮把这个标准过一遍。

  1. Promise 状态：Promise 状态转换只能从 pending 转换至 fulfilled or rejected。转换成功则有其成功的 value 值，失败则有失败的 reason 值。
    1. 状态分类：pending(进行中)、fulfilled(已成功)、rejected(已失败)
    2. 状态转换：
      1. pending 转换至 fulfilled or rejected
      2. fulfilled  不能转换状态（必须有一个 value 值）
      3. rejected 不能转变状态（必须有一个 reject 的 reason）
  2. 必须提供then 方法接受来访问最终结果（promise.then(onFulfilled, onRejected)）
    1. onFulfilled 和 onRejected 是可选参数
      1. 如果 onFulfilled 不是一个函数，它必须被忽略
      2. 如果 onRejected 不是一个函数，它必须被忽略
    2. 如果 onFulfilled 是一个函数
      1. 在状态变为 fulfilled 时被调用，且 promise 的值作为其第一个参数
      2. 状态不是 fulfilled 不能被调用
      3. 最多调用一次
    3. 如果 onRejected 是一个函数
      1. 在状态变为 rejected 被调用，且 promise 的值作为其第一个参数
      2. 状态不是 rejected 不能被调用
      3. 最多调用一次
    4. 在执行上下文栈只包含平台代码之前，onFulfilled 和 onRejected 不能被调用。由于 promise 被考虑为“平台代码”，所以在自身处理程序被调用时可能已经包含一个任务调度队列。
    5. onFulfilled 和 onRejected 必须被作为函数调用
    6. then 可以被调用多次
      1. 如果 promise 变为 fulfilled 状态，则所有的onFulfilled 按照 then 的原始调用顺序执行
      2. 如果 promise 变为 rejected 状态，则所有的 onRejected 按照then 的原始调用顺序执行
    7. then 必须返回一个 promise `promise2 = promise1.then(onFulfilled, onRejected);`
      1. onFulfilled 或 onRejected 返回的结果为 x，调用 `[[resolve]](promise2, x)`
      2. onFulfilled 或 onRejected 抛出一个异常 e, promise 必须以 e 的理由失败
      3. 如果 onFulfilled  不是一个函数且状态为 fulfilled，则 promise2 也应该是 fulfilled 状态且传递的 value 为 promise1 的 value
      4. 如果 onRejected 不是一个函数且状态为 rejected，则 promise2 也应该是 rejected 状态且传递的 reason 为 promise1 的 reason
  3. The PromiseResolution Procedure - 为了运行 `[[Resolve]](promise, x)` 执行以下步骤
    1. 如果 promise 和 x 引用同一个对象，会报 TypeError 错误
    2. 如果 x 是一个 promise
      1. 如果 x 是 pending 状态，则会保持 pending 状态直到 fulfilled 状态或 rejected 状态
      2. 如果 x 是 fulfilled 状态，promise 也是 fulfilled 且传给改 value 值
      3. 如果 x 是 rejected 状态，promise 也是 rejected 且传给该 reason 值
    3. 如果 x 是一个对象或函数
      1. 让 then 变成 x.then
      2. 如果检索属性 x.then 抛出异常 e，则以 e 为原因拒绝 promise
      3. 如果 then 是一个函数，用 x 作为 this 调用它。第一个参数是 resolvePromise，第二个参数是 rejectPromise
        1. 如果 resolvePromise 用一个值 y 调用，运行 `[[Resolve]](promise, y)`
        2. 如果 rejectPromise 用一个原因 r 调用，用 r 拒绝 promise
        3. 如果 resolvePromise 和 rejectPromise 都被调用，或者对同一个参数进行多次调用，那么第一次调用优先，以后的调用都会被忽略
        4. 如果调用 then 抛出异常
          1. 如果 resolvePromise 或 rejectPromise 已经被调用，则忽略
          2. 否则以 e 作为理由拒绝 promise
      4. 如果 then 不是一个函数，变为 fulfilled 状态并传值为 x
    4. 如果 x 不是一个对象获函数，状态变为 fulfilled 并传值 x

![4](https://tvax3.sinaimg.cn/large/a9034e0egy1gjvtm4xnlbj21bk1cg16t.jpg)

> 下面就是基于 Promise/A+ 规范实现的代码，已经经过 promise-aplus-tests 库进行了验证
```javascript
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

/**
 * Promise 构造函数
 * excutor: 内部同步执行的
 */
class Promise {
  constructor(excutor) {
    const self = this;
    self.status = PENDING;
    self.onFulfilled = []; // 成功的回调
    self.onRejected = []; // 失败的回调
    
    // 异步处理成功调用的函数
    // Promise/A+ 2.1 状态只能由 pending 转为 fulfilled 或 rejected; fulfilled 状态必须有一个 value 值；rejected 状态必须有一个 reason 值。
    function resolve(value) {
      if (self.status === PENDING) {
        self.status = FULFILLED;
        self.value = value;
        // Promise/A+ 2.2.6.1 相同 promise 的 then 可以被调用多次，当 promise 变为 fulfilled 状态，全部的 onFulfilled 回调按照原始调用 then 的顺序执行
        self.onFulfilled.forEach(fn => fn());
      }
    }

    function reject(reason) {
      if (self.status === PENDING) {
        self.status = REJECTED;
        self.reason = reason;
        // Promise/A+ 2.2.6.2 相同 promise 的 then 可以被调用多次，当 promise 变为 rejected 状态，全部的 onRejected 回调按照原始调用 then 的顺序执行
        self.onRejected.forEach(fn = fn());
      }
    }

    try {
      excutor(resolve, reject);
    } catch (e) {
      reject(e);
    }
  }

  then(onFulfilled, onRejected) {
    // Promise/A+ 2.2.1 onFulfilled 和 onRejected 是可选参数
    // Promise/A+ 2.2.5 onFulfilled 和 onRejected 必须被作为函数调用
    // Promise/A+ 2.2.7.3 如果 onFulfilled 不是函数且 promise1 的状态是 fulfilled，则 promise2 有相同的值且也是 fulfilled 状态
    // Promise/A+ 2.2.7.4 如果 onRejected 不是函数且 promise1 的状态是 rejected，则 promise2 有相同的值且是 rejected 状态
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason };

    const self = this;
    const promise = new Promise((resolve, reject) => {
      const handle = (callback, data) => {
        // Promise/A+ 2.2.4 onFulfilled 或者 onRejected 需要在自己的执行上下文栈里被调用，所以此处用 setTimeout
        setTimeout(() => {
          try {
            // Promise/A+ 2.2.2 如果 onFulfilled 是函数，则在 fulfilled 状态之后调用，第一个参数为 value
            // Promise/A+ 2.2.3 如果 onRejected 是函数，则在 rejected 状态之后调用，多一个参数为 reason
            const x = callback(data);
            // Promise/A+ 2.2.7.1 如果 onFulfilled 或 onRejected 返回一个 x 值，运行这 `[[Resolve]](promise, x)`
            resolvePromise(promise, x, resolve, reject);
          } catch (e) {
            // Promise/A+ 2.2.7.2 onFulfilled 或 onRejected 抛出一个异常 e，promise2 必须 yie 的理由失败
            reject(e);
          }
        });
      };

      if (self.status === PENDING) {
        self.onFulfilled.push(() => {
          handle(onFulfilled, self.value);
        });

        self.onRejected.push(() => {
          handle(onRejected, self.reason);
        });
      } else if (self.status === FULFILLED) {
        setTimeout(() => {
          handle(onFulfilled, self.value);
        });
      } else if (self.status === REJECTED) {
        setTimeout(() => {
          handle(onRejected, self.reason);
        });
      }
    });

    return promise;
  }
}

function resolvePromise(promise, x, resolve, reject) {
  // Promise/A+ 2.3.1 如果 promise 和 x 引用引用同一对象，会以 TypeError 错误 reject promise
  if (promise === x) {
    reject(new TypeError('Chaining Cycle'));
  }

  if (x && typeof x === 'object' || typeof x === 'function') {
    // Promise/A+ 2.3.3.3.3 如果 resolvePromise 和 rejectPromise 都被调用，或者对同一参数进行多次调用，那么第一次调用优先，以后的调用都会被忽略
    let used;
    try {
      // Promise/A+ 2.3.3.1 let then be x.then
      // Promise/A+ 2.3.2 调用 then 方法已经包含了该条（该条是 promise 的处理）
      let then = x.then;
      if (typeof then === 'function') {
        // Promise/A+ 2.3.3.3 如果 then 是一个函数，用 x 作为 this 调用它。第一个参数是 resolvePromise，第二个参数是 rejectPromise
        // Promise/A+ 2.3.3.3.1 如果 resolvePromise 用一个值 y 调用，运行`[[Resolve]](promise, y)`
        // Promise/A+ 2.3.3.3.2 如果 rejectPromise 用一个原因 r 调用，用 r 拒绝 promise
        then.call(x, (y) => {
          if (used) return;
          used = true;
          resolvePromise(promise, y, resolve, reject);
        }, (r) => {
          if (used) return;
          used = true;
          reject(r);
        });
      } else {
        // Promise/A+ 如果 then 不是一个函数，变为 fulfilled 状态并传值为 x
        if (used) return;
        used = true;
        resolve(x);
      }
    } catch (e) {
      // Promise/A+ 2.3.3.2 如果检索属性 x.then 抛出异常 e，则以 e 为原因拒绝 promise
      // Promise/A+ 2.3.3.4 如果调用 then 抛出异常，但是 resolvePromise 或 rejectPromise 已经执行，则忽略它
      if (used) return;
      used = true;
      reject(e);
    }
  } else {
    // Promise/A+ 2.3.4 如果 x 不是一个对象或函数，状态变为 fulfilled 并传值 x
    resolve(x);
  }
}
```

### 其它方法
按照 Promise/A+ 规范实现了 Promise 的核心内容，但是其只实现了 Promise.prototype.then() 方法，那其它方法呢？下面我们就唠一唠其它方法，包括静态方法（Promise.resolve()、Promise.reject()、Promise.all()、Promise.race()） 和实例方法（Promise.prototype.catch()、Promise.prototype.finally()）。
  * 其他方法
    * 静态方法
      * Promise.resolve()
      * Promise.reject()
      * Promise.all()
      * Promise.race()
    * 实例方法
      * Promise.prototype.catch()
      * Promise.prototype.finally()

#### Promise.resolve()
将现有对象转换为 Promise 对象
*参数说明：*
  * 参数是一个 Promise 实例（如果参数是 Promise 实例，那么 Promise.resolve 将不做任何修改、原封不动的返回这个实例）
  * 参数是一个 thenable 对象（具有 then 方法的对象），Promise.resolve 方法会将这个对象转化为 Promise 对象，然后就立即执行 thenable对象的 then 方法
  * 参数不是具有 then 方法的对象，或根本就不是对象，Promise.resolve 方法返回一个新的 Promise 对象，状态为 resolved
  * 不带有任何参数，直接返回一个 resolved 状态的 Promise 对象

```javascript
class Promise {
  // ...
  // 将现有对象转换为 Promise 对象
  static resolve(value) {
    // 如果参数是 Promise 实例，那么 Promise.resolve 将不做任何修改、原封不动地返回这个实例。
    if (value instanceof Promise) return value;

    // 参数是一个 thenable 对象（具有 then 方法的对象），Promise.resolve 方法会将这个对象转为 Promise 对象，然后就立即执行 thenable 对象的 then 方法
    if (typeof value === 'object' || typeof value === 'function') {
      try {
        let then = value.then;
        if (typeof then === 'function') {
          return new Promise(then.bind(value));
        }
      } catch (e) {
        return new Promise((resolve, reject) => {
          reject(e);
        });
      }
    }

    // 参数不是具有 then 方法的对象，或根本就不是对象，Promise.resolve 方法返回一个新的 Promise 对象，状态为 resolved
    return new Promise((resolve, reject) => {
      resolve(value);
    });
  }
}
```

#### Promise.reject()
返回一个新的 Promise 实例，该实例的状态为 rejected。
注意：Promise.reject() 方法的参数，会原封不动的作为 reject 的理由，变成后续方法的参数

```javascript
class Promise {
  // ...
  // 返回一个新的 Promise 实例，该市里的状态为 rejected
  static reject(reason) {
    return new Promise((resolve, reject) => {
      reject(reason);
    });
  }
}
```

#### Promise.all()
用于将多个 Promise 实例，包装秤一个新的 Promise 实例
`const p = Promise.all([p1, p2, p3])` 
接收数组（或者具备 Iterator）作为参数，p1, p2, p3 都是 Promise 实例，如果不是，就会先调用下面讲到的 Promise.resolve 方法，将参数转为 Promise 实例，再进一步处理。
只有所有状态都变为 fulfilled，p 的状态才会是 fulfilled，p1, p2, p3 的返回值组成一个数组，传递给 p 的回调函数；否则是 rejected 状态，此时第一个被 reject 的实例的返回值，会传递给 p 的回调函数。

```javascript
class Promise {
  // ...
  // 用于将多个 Promise 实例，包装成一个新的 Promise 实例。只有所有状态都变为 fulfilled，p 的状态才会是 fulfilled
  static all(promises) {
    const values = [];
    let resolvedCount = 0;
    return new Promise((resolve, reject) => {
      promises.forEach((p, index) => {
        Promise.resolve(p).then(value => {
          resolvedCount++:
          values[index] = value;
          if (resolvedCount === promises.length) {
            resolve(values);
          }
        }, reason => {
          reject(reason);
        });
      });
    });
  }
}
```

#### Promise.race()
`const p = Promise.race([p1, p2, p3])` 只要 p1、p2、p3 之中有一个实例率先改变状态，状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给回调函数。

```javascript
class Promise {
  // ...
  // 只要有一个实例率先改变状态，状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给回调函数。
  static race(promises) {
    return new Promise((resolve, reject) => {
      promises.forEach((p, index) => {
        Promise.resolve(p).then(value => {
          resolve(value);
        }, reason => {
          reject(reason);
        });
      });
    });
  }
}
```

#### Promise.catch()
是 .then(null, rejection) 或 .then(undefined, rejection) 的别名，用于指定发生错误时的回调函数。
Promise 对象的错误具有 “冒泡”性质，会一直向后传递，知道被捕获为止。也就是说，错误总会被下一个 catch 语句捕获。

```javascript
class Promise {
  // ...
  // 是 .then(null, rejection) 或 .then(undefined, rejection) 的别名，用于指定发生错误时的回调函数
  catch(onRejected) {
    return this.then(undefined, onRejected);
  }
}
```

#### Promise.finally()
用于指定不管 Promise 对象最后状态如何，都会执行的操作
本质是 then 方法的特例

```javascript
class Promise {
  // ...
  // 用于指定不管 Promise 对象最后状态如何，都会执行的操作。
  finally(callback) {
    return this.then(
      value => Promise.resolve(callback()).then(() => value),
      reason => Promise.resolve(callback()).then(() => { throw reason })
    );
  }
}
```

## Async 原理实现
在开发过程中常用的另一种异步方案莫过于 Async，通过 async 函数的引入使得异步操作变得更加方便。实质上，async 是 Generator 的语法糖，最大的亮点是 async 内置执行器，调用后即可自动执行，不像 Generator 需要调用 next() 方法才能执行。

ES2017 标准引入了 async 函数，使得异步操作变得更加方便。简言之，该函数就是 Generator 函数的语法糖。
优点：内置执行器，可以自动执行；语义相比 Generator 更加清晰；返回值是 Promise，比 Generator 函数的返回值是 Iterator 对象操作更加方便。
缺点：增加学习成本。
async 函数对 Generator 函数的改进：
  1. 内置执行器。Generator 函数的执行必须依靠执行器
  2. 更好的语义。async 和 await 比起 星号（*）和 yield 语义更加清楚了
  3. 适用度更广。co 模块约定，yield 命令后面只能是 Thunk 函数或者 Promise 对象，而 async 函数的 await 命令后面，可以说 Promise 对象和原始类型的值
  4. 返回值是 Promise。async 函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便，可以用 then 指示下一步操作

这是 Async 的实现原理，即将 Generator 函数作为参数放入 run 函数中，最终实现自动执行并返回 Promise 对象。
```javascript
function run(genF) {
  // 返回值是 Promise
  return new Promise((resolve, reject) => {
    const gen = genF();
    function step(nextF) {
      let next;
      try {
        // 执行该函数，获取一个有着 value 和 done 两个属性的对象
        next = nextF();
      } catch (e) {
        // 出现异常则将该 Promise 变为 rejected 状态
        reject(e);
      }

      // 判断是否到达末尾，Generator 函数到达末尾则将该 Promise 变为 fulfilled 状态
      if (next.done) {
        return resolve(next.value);
      }

      // 没到达末尾，则利用 Promise 封装该 value，知道执行完毕，反复调用 step 函数，实现自动执行
      Promise.resolve(next.value).then((v) => {
        step(() => gen.next(v));
      }, (e) => {
        step(() => gen.throw(e));
      });
    }

    step(() => gen.next(undefined));
  });
}
```

## 发布/订阅实现
发布/订阅模式在观察着模式的基础上，在目标和观察者之间增加了一个调度中心。订阅者（观察者）把自己想要订阅的事件注册到调度中心，当该事件出发的时候，发布者（目标）发布该事件到调度中心，由调度中心统一调度订阅者注册到调度中心的处理代码。

![5](https://tva4.sinaimg.cn/large/a9034e0egy1gjvtm5463nj20i309m0t5.jpg)

```typescript
// 发布订阅（TypeScript 版）
interface Publish {
  registerObserver(eventType: string, subscribe: Subscribe): void;
  remove(eventType: string, subscribe?: Subscribe): void;
  notifyObservers(eventType: string): void;
}
interface SubscribesObject {
  [key: string]: Array<Subscribe>
}

class ConcretePublish implements Publish {
  private subscribes: SubscribesObject;

  constructor() {
    this.subscribes = {};
  }

  registerObserver(eventType: string, subscribe: Subscribe): void {
    if (!this.subscribes[eventType]) {
      this.subscribes[eventType] = [];
    }

    this.subscribes[eventType].push(subscribe);
  }

  remove(eventType: string, subscribe?: Subscribe): void {
    const subscribeArray = this.subscribes[eventType];
    if (subscribeArray) {
      if (!subscribe) {
        delete this.subscribes[eventType];
      } else {
        for (let i = 0; i < subscribeArray.length; i++) {
          if (subscribe === subscribeArray[i]) {
            subscribeArray.splice(i, 1);
          }          
        }
      }
    }
  }

  notifyObservers(eventType: string, ...args: any[]): void {
    const subscribes = this.subscribes[eventType];
    if (subscribes) {
      subscribes.forEach(subscribe => subscribe.update(...args));
    }
  }
}

interface Subscribe {
  update(...value: any[]): void;
}

class ConcreteSubscribe1 implements Subscribe {
  public update(...value: any[]): void {
    console.log('已经执行更新操作 1，值为', ...value);
  }
}

class ConcreteSubscribe2 implements Subscribe {
  public update(...value: any[]): void {
    console.log('已经执行更新操作 2，值为', ...value);
  }
}

function main() {
  const publish = new ConcretePublish();
  const subscribe1 = new ConcreteSubscribe1();
  const subscribe2 = new ConcreteSubscribe2();

  publish.registerObserver('1', subscribe1);
  publish.registerObserver('2', subscribe2);

  publish.notifyObservers('2', '222222');
}

main();
```