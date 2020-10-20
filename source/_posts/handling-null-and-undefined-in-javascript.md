---
title: <摘抄>如何在 JavaScript 中处理 null 和 undefined？
date: 2020-03-02 12:59:00
tags: 
categories: 技术向
---

[「原译文」如何在 JavaScript 中处理 null 和 undefined？](https://www.infoq.cn/article/tsfJmMq4sPa9kmsBKypY)
[「原文」handling null and undefined in javascript](https://medium.com/javascript-scene/handling-null-and-undefined-in-javascript-1500c65d51ae)

> 在 JavaScript 的开发工作中，许多开发人员都头疼的一个问题就是处理可选值。怎样才能最大程度减少由 null、undefined，或在运行时未初始化的值引发的错误，有哪些最佳策略呢？

有些语言针对这类情况有内置的解决方案。在某些静态类型的语言中，你可以认定 null 和 undefined 是非法值，并且让你的编程语言在编译时抛出 TypeError。但即使在这种语言中，也不能阻止 null 输入在运行时流入程序。

为了更好的处理这种问题，我们需要了解这些值的来源。一下是一些最常见的来源：
  * 用户输入；
  * 数据库/网络记录；
  * 未初始化的状态；
  * 无法返回任何内容的函数。

## 用户输入
在处理用户输入时，验证是第一道也是最好的防线。我经常依靠 schema 验证器来完成这项工作。比如，你可以试试 react-jsonschema-form。

**对输入的记录做 hydrate 处理**
我总是会把从网络、数据库或者用户输入中获得的输入传递给一个 hydrating 函数。例如，我会使用可以处理 undefined 值得 redux 动作创建者来 hydrate 用户记录：

[https://medium.com/javascript-scene/10-tips-for-better-redux-architecture-69250425af44](https://medium.com/javascript-scene/10-tips-for-better-redux-architecture-69250425af44)

```javascript
const setUser = ({ name = 'Anonymous', avatar = 'anon.png' } = {}) => ({
  type: setUser.type,
  payload: {
    name,
    avatar,
  }
});
setUser.type = 'userReducer/setUser';
```

有时，你需要根据数据的当前状态显示不同的内容。如果页面可以在所有数据初始化完毕之前显示，就可能会遇到这种情况。例如，当你想用户显示资金金额时，有时可能会在加载数据之前显示余额为 0。这种事情我见过很多次，这会让用户感到不安。你可创建一些自定义的数据类型，这些数据类型根据当前状态生成不同的输出。

```javascript
const createBalance = ({
  // 默认状态
  state = 'uninitialized',
  value = createBalance.empty,
} = {}) => createBalance.isValidState(state) && ({
  __proto__: {
    uninitialized: () => '--',
    initialized: () => value,
    format() {
      return this.getState();
    },
    getState: () => state,
    set: value => {
      const test = Number(value);
      assert(!Number.isNaN(test), `setBalance Invalid value: ${value}`);
      return createBalance({
        state: 'initialized',
        value
      });
    }
  }
});

createBalance.empty = '0';
createBalance.isValidState = state => {
  if (!['uninitialized', 'initialized'].includes(state)) {
    throw new Error(`createBalance Invalid state: ${state}`);
  }
  return true;
};

const setBalance = value => createBalance().set(value);
const emptyBalanceForDisplay = createBalance()
  .format();
console.log(emptyBalanceForDisplay); // '--'

const balanceForDisplay = setBalance('25')
  .format(balance);
console.log(balanceForDisplay); // '25'

// 取消下面调用的注释前缀就能看到错误示例
//  setBalance('foo'); // Error: setBalance Invalid value: foo
//  Error: createBalance Invalid state: THIS IS NOT VALID
//  createBalance({ state: 'THIS IS NOT VALID', value: '0' });
```

上面代码是一个状态机，其设计无法显示无效状态。首次创建余额数字时，它将被设置为一个 uninitialized 状态。如果你尝试在 uninitialized 状态时显示余额，则只会获得一个占位符值（”--“）。要调整这个设置，你必须调用 .set 方法，或调用我们在 createBalance 下面定义的 setBalance 捷径来显式设置一个值。

该状态本身经过封装，以保护其免受外界干扰，这样其它函数就无法捕获它并将其设置为无效状态了。

> 注意：想知道为什么我们使用字符串而不是数字吗？那是因为我用了精度很高的大数字符串来表示货币类型，以避免舍入错误，并能准确的表示加密货币交易中的数值（这类交易中的数值精度要求可能会非常高）。

如果你使用 Redux 或 Redux 架构，则可以使用 [Redux-DSM](https://github.com/ericelliott/redux-dsm) 来声明状态机。

## 避免创建 null 和 undefined 值
在你自己的函数中，你可以尽量避免创建 null 或 undefined 值。我想到了很多 JavaScript 的内置方法来做到这一点，见下文。

### 避免 null
我从未在 JavaScript 中显式创建 null 值，因为我觉得用两个不同的原始值来表示 ”这个值不存在“，实在是没什么意义的事情。

自 2015 年开始 JavaScript 就支持默认值了，当你没有为相关参数或者属性提供值时，它们就会装填默认值。这些默认值不适用于 null 值。根据我的经验，这通常会导致一个错误。为了避免这种陷阱，请不要再 JavaScript 中使用 null。

如果你希望处理未初始化的值或空值这类特殊情况，状态机是更好的选择，如前所述。

### 新的 JavaScript 功能
有几个功能可以帮助你处理 null 或 undefined 值。在撰写本文时，下面两个功能都是第三阶段的提案，将来你看到本文时，这两个功能可能已经正式发布了。

目前，可选链（optional chaining） 时第 3 阶段的提案。它的工作机制是这样的：

```javascript
const foo = {};
// console.log(foo.bar.baz); // throw error
console.log(foo.bar?.baz); // undefined
```

(关于可选链的更多细节，可以参考前段之巅之前的一片文章[《了解 JavaScript 新特性：Optional Chaining》](https://mp.weixin.qq.com/s?__biz=MzUxMzcxMzE5Ng==&mid=2247492849&idx=2&sn=c081a0a944abffd30d3a0ef9695a9333&scene=21#wechat_redirect))

### 空位合并运算符
这也是准备添加到规范中的第 3 阶段提案，”空位合并运算符（Nullish Coalescing Operator）“ 基本上是”回退值运算符“ 的一种高大上的说法。如果左侧的值是 undefined 或 null, 则其会等于右侧的值。他的工作机制是这样的：

```javascript
let baz;
console.log(baz); // undefined
console.log(baz ?? 'default baz');
// default baz
// Combine with optional chaining:
console.log(foo.bar?.baz ?? 'default baz'); // default baz
```

目前提案尚未正式进入规范，所以你需要安装 @babel/plugin-proposal-optional-chaining 和 @babel/plugin-proposal-nullish-coalescing-operator。

### 使用 Promise 实现异步 Either
如果某个函数可能不返回值，则最好将其包装在一个 Either 中。在函数式编程中，Either monad 是一种特殊的抽象数据类型，称为 Promise。你可以用它对 undefined 值进行声明式错误分支：

```javascript
const exists = x => x != null;
const ifExists = value => exists(value) ?
  Promise.resolve(value) :
  Promise.reject(`Invalid value: ${value}`);
ifExists(null).then(log).catch(log); // Invalid value: null;
ifExists('hello').then(log).catch(log); // hello
```

你可以根据需要编写一个同步版本，但这里我还用不到那一步，就留给你做练习吧。如果你在 functor 和 monad 方面有良好的基础，那么这个过程会很容易。如果这听起来很吓人，那也不用担心，只用 Promise 即可。它们是内置的，并且在大多数情况下都可以正常工作。

### 使用数组实现 Maybe
数组实现了一个 map 方法，这个方法会使用一个函数应用在数组的每个元素上。如果数组为空，则这个函数永远不会被调用。换句话说，JavaScript 中的数组可以充当 Haskell 等语言中的 Maybe 角色。

*Maybe 是什么？*
Maybe 是一种特殊的抽象数据类型，它封装了一个可选值。数据类型有两种形式：
  * Just - 包含一个值的 Maybe
  * Nothing - 没有值得 Maybe

下面是具体的机制：
```javascript
const log = x => console.log(x);
const exists = x => x != null;
const Just = value => ({
  map: f => Just(f(value)),
});
const Nothing = () => ({
  map: () => Nothing(),
});
const Maybe = value => exists(value) ?
  Just(value) :
  Nothing();
const empty = undefinde;
Maybe(empty).map(log); // does not log
```

这只是一个示例，用来演示这个概念。你可以围绕 maybe 建立一整套有用的数据库，实现 flatMap 和 flat 之类的操作（例如：在编写多个返回 Maybe 的函数时避免 Just(Just(value)) 这种情况）。但是 JavaScript 已经有一种数据类型可以直接实现这些功能，因此我通常会这样做：使用数据。

如果你要创建一个可能会，或可能不会产生结果的函数（尤其是可能有多个结果的情况下），那么这种情况下最好的方法可能就是返回一个数组。

```javascript
const log = x => console.log(x);
const exists = x => x != null;
const arr = [1, 2, 3];
const find = (p, list) => [list.find(p)].filter(exists);
find(x => x > 3, arr).map(log); // does not log anything
find(x => x < 3, arr).map(log); // logs 1
```

我发现在空列表上不会调用 map，这对避免 null 和 undefined 值来说非常有用，但是请记住，如果数据包含 null 和 undefined 值，它将使用这些值调用该函数，因此如果你在运行的函数可能会产生 null 或 undefined，你需要将其从返回的数组中过滤出来，如上所示。这可能会改变集合的长度。

在 Haskell 中，有一个函数 Maybe（就像 map 一样）将一个函数应用于一个值上。但是该值时可选的，并封装在 Maybe 中。我们可以使用 JavaScript 的 Array 数据类型做基本上相同的事情：

```javascript
// maybe = b => (a => b) => [a] => b
const maybe = (fallback, f = () => {}) => arr =>
  arr.map(f)[0] || fallback;
// turn a value (or null/undefined) into a maybeArray
const toMaybeArray = value => [value].filter(exists);
// maybe multiply the contents of an array by 2,
// default to 0 if the array is empty
const maybeDouble = maybe(0, x => x * 2);
const emptyArray = toMaybeArray(null);
const maybe2 = toMaybeArray(2);
// logs: "maybeDouble with fallback: 0"
console.log('maybeDouble with fallback: ', maybeDouble(emptyArray));
// logs: "maybeDouble(maybe2): 4"
console.log('maybeDouble(maybe2): ', maybeDouble(maybe2));
```

maybe 需要一个回退值，然后是一个映射到 maybe 数组上的函数，然后是一个 maybe 数组（包含一个值，或者为空的数组），最后返回将该函数应用于数组内容的结果，或者在数据为空时返回回退值。为了方便起见，我还定义了 toMaybeArray 函数，并 curry 了 maybe 函数来让它更显眼一些，方便展示。

如果你想在生产代码中执行类似的操作，我已经创建了一个经过单元测试的开源库，可以简化你的工作，这个库叫 [Maybearray](https://github.com/ericelliott/maybearray)。与其他 JavaScript Maybe 库相比，Maybearray 的优势在于它使用原生 JavaScript 数组来表示值，因此你不必对其进行任何特殊处理，也用不着来回转换。当你调试中遇到 Maybe 数组时，你不必问 ”这是什么奇怪的类型？“，它只是一个值的数组或一个 空数组，你已经看过一百万遍了。

## 作者介绍
Eric Elliott 是《撰写软件》和《编写 JavaScript 应用程序》这两本书的作者。他是 EricElliottJS.com 和 DevAnywhere.io 的共同创始人，并教授开发人员基本的软件开发技能。他创建并指导数字货币项目的开发团队，并为 Adobe Systems、Zumba Fitness、《华尔街日报》、ESPN、BBC 和包括 Usher、Frank Ocean、Metallica 等在内的顶级唱片艺术家贡献了自己的软件经验。他与漂亮的妻子一起过着隐士般的生活。