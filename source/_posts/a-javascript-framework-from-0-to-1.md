---
title: <摘抄>从零开始编写自己的 JavaScript 框架
date: 2016-11-07 09:00:00
tags: 
categories: 技术向
---

[[原文]从零开始编写自己的JavaScript框架](http://www.ituring.com.cn/article/48461)

## 1. 模块的定义和加载
### 1.1 模块的定义
一个框架想要能支撑较大的应用，首先要考虑怎么做模块化，有了内核和模块加载系统，外围的模式就可以一个一个的增加。不同的 JavaScript 框架，实现模块化方式各有不同，我们来选择一种比较优雅的方式做个讲解。

先问个问题：我们做模块系统的目的是什么？如果觉得这个问题难以回答，可以从反面来考虑：假如不做模块系统，有什么样的坏处？

我们经历过比较粗放、混乱的前端开发阶段，页面里充满了全局变量，全局函数。那时候要复。用 js 文件，就是把某些 js 函数放到一个文件里，然后让多个页面都来引用。

考虑到一个页面可以引用多个这样的 js，这些 js 互相又不知道别人里面写了什么，很容易造成命名的冲突，而产生这种冲突的时候，又没有哪里能够提示出来。所以我们要有一种办法，把作用域比较好的隔开。

JavaScript 这种语言比较奇怪，奇怪在哪里呢？它的现有版本里没有 package 跟 class，要是有，我们也没必要来考虑什么自己做模块化了。那它是要用什么东西来隔绝作用域呢？

在很多传统高级语言里，变量作用域的边界是大括号，在{}里面定义的变量，作用域不会传到外面去，但我们的 JavaScript 大人不是这样的，它的边界是 function。所以我们这段代码，仍然能打印出值

```javascript
for (var i=0; i<5; i++) {
  // do something...
}
alert(i);
```

那么我们只能选用 function 做变量的容器，把每个模块封装到一个 function 里。现在问题又要来了，这个 function 本身的作用域是全局的，怎么办？我们想不到办法，拔剑四顾心茫然。

我们有没有可以参照的东西呢？这时候，脑海中一群语言飘过：C 语言飘过：“我不是面向对象语言哦，不需要想你这么组织哦”，“死开！”，Java 飘过：“我是纯面向对象语言哟，连 main 都要在类中，编译的时候通过装箱清单指定入口哦”，“死开！”，C++ 飘过：“我也是纯面向对象语言哦”，等等，C++是纯面向对象的语言吗？你的 main 是什么？？？ main 是特例，不在任何类中！
TIPS：如何利用面向对象编程的 4 个原理（封装、抽象、继承和多态）

啊，我们发现了什么，既然无法避免全局的作用域，那与其让100个 function 都全局，不如只让一个来全局，其他的都由它管理。

本来我们打算自己当上帝的，现在只好改行先当个工商局长。你想开店吗？先来注册，不然封杀你！于是良民们纷纷来注册。店名叫什么，从哪进货，卖什么的，一一登记在案，为了方便下面的讨论，我们连进货的过程都让工商局管理起来。

店名，指的就是这里的模块名，从哪里进货，代表他依赖什么其他模块，卖什么，表示它对外提供一些什么特性。

好了，考虑到我们的这个注册管理机构是个全局作用域，我们还得把它挂在 window 上作为属性，然后再用一个function 隔离出来，要不然，别人也定义一个同名的，就把我们覆盖掉了。

```javascript
(function() {
  window.thin = {
    define: function (name, dependencies, factory) {
      // register a module
    }
  }
})
```

在这个 Module 方法内部，应当怎么去实现呢？我们的 Module 应当有一个地方存储，但存储是要在工商局内部的，不是随便什么人都可以看到的，所以这个存储结构也放在工商局同样的作用域里。

用什么结构去存储呢？工商局备案的时候，店名不能跟已有的重复，所以我们发现这是用 map 的很好场景，考虑到 JavaScript 语言层面没有 map，我们弄个 Object 来存。

```javascript
(function () {
  var moduleMap = {};
  window.thin = {
    define: function (name, dependencies, factory) {
      if (!moduleMap[name]) {
        var module = {
          name: name,
          dependencies: dependencies,
          factory: factory,
        };
        moduleMap[name] = module;
      }
      return moduleMap[name];
    }
  }
})
```

现在，模块的存储结构就搞好了。

### 1.2 模块的使用

存的部分搞好了，我们来看看怎么取。现在来了一个商家，卖木器的，他需要从一个卖钉子的那边进货，卖钉子的已经来注册过了，现在要让这个木器厂能买到钉子。现在的问题是，两个商家处于不同的作用域，也就是说，他们互相不可见，那通过什么方式，我们才能让它们产生调用关系呢？

个人解决不了的问题还是得靠政府，有困难要坚决克服，没有困难就制造困难来克服。现在困难有了，该克服了。商家说，我能不能给你我的进货名单，你帮我查一下它们在哪家店，然后告诉我？这么简单的要求当然一口答应下来，但是采用什么方式传递给你呢？这可犯难了。

我们参考 AngularJS 框架，写了一个类似的代码：
```javascript
thin.define("A", [], function () {
  // module A
});
thin.define("B", ["A"], function () {
  // module B
  var a = new A();
});
```

看这段代码特别在哪里呢？模块 A 的定义，毫无特别之处，主要看模块 B。它在依赖关系里写了一个字符串的 A，然后在工厂方法的形参写了一个真真切切的 A 类型。嗯？这个有些奇怪啊，你的 A 类型要怎么传递过来呢？其实很简单的，因为我们声明了依赖项的数组，所以可以从依赖项，挨个得到对应的的工厂方法，然后创建实例，传进来。

```javascript
use: function (name) {
  var module = moduleMap[name];
  if (!module.entity) {
    var args = [];
    for (var i = 0; i < module.dependencies.length; i++) {
      if (moduleMap[module.dependencies[i]].entiry) {
        args.push(moduleMap[module.dependencies[i]].entity);
      } else {
        args.push(this.use(module.dependencies[i]));
      }
    }
    module.entity = module.factory.apply(noop, args);
  }
  return module.entity;
}
```

我们可以看到，这里面递归获取了依赖项，然后当做参数，用这个模块的工厂方法来实例化了一下。这里我们多做了一个判断，如果模块工厂已经执行过，就缓存在 entity 属性上，不需要每次都创建。以此类推，加入一个模块有多个依赖项，也可以用类似的方式写，毫无压力：
```javascript
thin.define("D", ["A", "B", "C"], function (A, B, C) {
  // module D
  var a = new A();
  var b = new B();
  var c = new C();
});
```

注意了，D模块的工厂，实参的名称未必就要是跟依赖项一致，比如，以后我们代码较多，可以给依赖项和模块名称加命名空间，可能变成这样：
```javascript
thin.define("foo.D", ["foo.A", "foo.B", "foo.C"], function (A, B, C) {
  // module D
  var a = new A();
  var b = new B();
  var c = new C();
});
```

这段代码仍然可以正常运行。我们来做另外一个测试，改变形参的顺序：
```javascript
thin.define("A", [], function) {
  return "a";
};

thin.define("B", [], function) {
  return "b";
};

thin.define("C", [], function) {
  return "c";
};

thin.define("D", ["A", "B", "C"], function (B, A, C) {
  return B + A + C;
});

var D = thin.use("D");
alert(D);
```

试试看，我们的 D 打出什么结果呢？结果是”abc”，所以说，模块工厂的实参只跟依赖项的定义有关，跟形参的顺序无关。我们看到，在 AngularJS 里面，并非如此，实参的顺序是跟形参一致的，这是怎么做到的呢？

我们先离开代码，思考这么一个问题：如何得知函数的形参名数组？对，我们是可以用 func.length 得到形参个数，但无法得到每个形参的变量名，那怎么办呢？

AngularJS 使用了一种比较极端的办法，分析了函数的字面量。众所周知，在 JavaScript 中，任何对象都隐含了 toString 方法，对于一个函数来说，它的 toString 就是自己的实现代码，包含函数签名和注释。下面我贴一下 AngularJS 里面的这部分代码：
```javascript
var FN_ARGS = /^function\s*[^\(]*\(\s*([^\)]*)\)/m;
var FN_ARG_SPLIT = /,/;
var FN_ARG = /^\s*(_?)(\S+?)\1\s*$/;
var STRIP_COMMENTS = /((\/\/.*$)|(\/\*[\s\S]*?\*\/))/mg;
function annotate(fn) {
  var $inject,
      fnText,
      argDecl,
      last;

  if (typeof fn == 'function') {
    if (!($inject = fn.$inject)) {
      $inject = [];
      fnText = fn.toString().replace(STRIP_COMMENTS, '');
      argDecl = fnText.match(FN_ARGS);
      forEach(argDecl[1].split(FN_ARG_SPLIT), function(arg){
        arg.replace(FN_ARG, function(all, underscore, name){
          $inject.push(name);
        });
      });
      fn.$inject = $inject;
    }
  } else if (isArray(fn)) {
    last = fn.length - 1;
    assertArgFn(fn[last], 'fn');
    $inject = fn.slice(0, last);
  } else {
    assertArgFn(fn, 'fn', true);
  }
  return $inject;
}
```
可以看到，这个代码也不长，重点是类型为 function 的那段，首先去除了注释，然后获取了形参列表字符串，这段正则能获取到两个结果，第一个是全函数的实现，第二个才是真正的形参列表，取第二个出来 split，就得到了形参的字符串列表了，然后按照这个顺序再去加载依赖模块，就可以让形参列表不对应于依赖项数组了。

ANgularJS 这段代码很强大，但是要损耗一些性能，考虑到我们的框架首要原则是简单，甚至可以为此牺牲一些灵活性，我们不做这么复杂的事情了。

### 1.3 模块的加载
到目前为止，我们可以把多个模块都定义在一个文件中，然后手动引入这个 js 文件，但是如果一个页面要引用很多个模块，引入工作就变得比较麻烦，比如说，单页面应用程序（SPA）一般比较复杂，往往包含数以万计行数的 js 代码，这些代码至少分布在几十个甚至成百上千的模块中，如果我们也在主界面就加载他们，载入时间会非常难以接受。但我们可以这样看：主界面加载的时候，并不是用到了所有这些功能，能否先加载那些必须的，而把剩下的放在需要用的时候再去加载？

所以我们可以考虑万能的 AJAX，从服务端获取一个 js 的内容，然后….，怎么办，你当然说不能 eval 了，因为据说 eval 很 evil 啦，但是它 evil 在哪里呢？主要是破坏全局作用域啦，怎么怎么，但是如果这些文件里面都是按照我们规定的模块格式写，好像也没有什么在全局作用域的…，好吧。

算啦，我们还是用最简单的方式，就是动态创建 script 标签，然后设置 src，添加到 document.head 里面，然后监听它们的完成事件，做后续操作。真的很简单，因为我们的框架不需要考虑那么多种情况，不需要 AMD，不需要 require 那么麻烦，用着框架的人必须按照这里的原则写。

所以，说真的我们这里没那么复杂啦，要是你们想看更详细原理不如去看这个，解释的比我好诶：
[JavaScript 的装载和执行](http://coolshell.cn/articles/9749.html#jtss-tsina)
[补一段，@Franky 大神指出了这篇文章中一些不符合现状的地方，我把它也贴在这里，共读者参考]

> 很多观点都是，史蒂夫那本老书上的观点，和那时候同期产生的一些数据和资料…所以显得不少东西说的都太想当然了…譬如 script 标签的加载和执行会阻塞后面资源的加载和执行之类的。说的过于肯定了。比如 Chrome 7+ 就开始逐渐改进的——预加载机制，就分 head 里面的资源、body 里面的资源，两个资源是否跨界三种情形，不提这些浏览器，我们看看 ie10 也同样改进了死循环10秒，这后面的图片能被提前加载，就不用说其他 A 级浏览器的丰富的优化策略了。所以还是建议博主，别拿几年前的老资料作为依据，尤其这些数据是用来说明更新速度像在赛跑一样的各个浏览器了。
>  
> 关于 defer，似乎史蒂夫的老书上是这么说的么？显然没有测试全非 ie 浏览器的各个版本，或者是他测试数据的时候 ff 某大版本的几个 beta 子版本还没出现？
>  
> 其次是就你的加载器提到的预加载策略，你有测过所有浏览器用 Object 预加载可能涉及到的问题么（比如 chrome8，9的预加载的会话级别的资源类型缓存 bug）。抛开这个问题不谈，假设你预加载到一般，用户再次触发了加载。你觉得这种情况如果频繁发生，是否合适？你的预加载策略连script.onload 状态都无法测知，进一步优化的可能性就消失了。考虑下为什么 seajs 的 umd 要设计成那个样子？
>  
> 最后吐槽下你的代码，有注意到你用 document.body.appendChild 来向 dom 中插入脚本，我的建议是，永远不要这样做 ，除非你可以无视 ie6用户、以及 ie7缺失某些补丁的子版本。
>  
> 你可以选择 body，可以但请使用 insertBefore。但在某些极端情况下，这仍然会发生问题，最佳实践是 head.insertBefore 向其第一个子节点插入。（你甚至无需检测是否存在子节点，这个 api 会在没有子节点的时候，行为同 appendChild）。而更加稳妥的情况是：如果注入 script，发现document.head 还没有被构建时，可以自己造一个，这才是一个通用加载器要做到的程度…

我也偷懒了，只是贴一下代码，顺便解释一下，界面把所依赖的 js 文件路径放在数组里，然后挨个创建 script 标签，src 设置为路径，添加到 head 中，监听他们的完成事件。在这个完成时间里，我们要做这么一些事情：在 fileMap 记录当前 js 文件的路径，防止以后重复加载，检查列表中所有文件，看看是否全部加载完了，如果全加载好了，就执行回调。

```javascript
require: function (pathArr, callback) {
  for (var i = 0; i < pathArr.length; i++) {
    var path = pathArr[i];

    if (!fileMap[path]) {
      var head = document.getElementsByTagName('head')[0];
      var node = document.createElement('script');
      node.type = 'text/javascript';
      node.async = 'true';
      node.src = path + '.js';
      node.onload = function () {
        fileMap[path] = true;
        head.removeChild(node);
        checkAllFiles();
      };
      head.appendChild(node);
    }
  }

  function checkAllFiles () {
    var allLoaded = true;
    for (var i = 0; i < pathArr.length; i++) {
      if (!fileMap[pathArr[i]]) {
        allLoaded = false;
        break;
      }
    }
    if (allLoaded) {
      callback();
    }
  }
}
```

### 1.4 小结
到此为止，我们的建议框架的模块定义系统就完成了。完整的代码如下：
```javascript
(function () {
  var moduleMap = {};
  var fileMap = {};

  var noop = function () {};

  var thin = {
    define: function (name, dependencies, factory) {
      if (!moduleMap[name]) {
        var module = {
          name: name,
          dependencies: dependencies,
          factory: factory,
        };

        moduleMap[name] = module;
      }

      return moduleMap[name];
    },

    use: function (name) {
      var module = moduleMap[name];

      if (!module.entity) {
        var args = [];
        for (var i = 0; i < module.dependencies.length; i++) {
          if (moduleMap[module.dependencies[i]].entity) {
            args.push(moduleMap[module.dependencies[i]].entity);
          } else {
            args.push(this.use(module.dependencies[i]));
          }
        }

        module.entity = module.factory.apply(noop, args);
      }

      return module.entity;
    },

    require: function (pathArr, callback) {
      for (var i = 0; i < pathArr.length; i++) {
        var path = pathArr[i];

        if (!fileMap[path]) {
          var head = document.getElementsByTagName('head')[0];
          var node = document.createElement('script');
          node.type = 'text/javascript';
          node.async = 'true';
          node.src = path + '.js';
          node.onload = function () {
            fileMap[path] = true;
            head.removeChild(node);
            checkAllFiles();
          };
          head.appendChild(node);
        }
      }

      function checkAllFiles() {
        var allLoaded = true;
        for (var i = 0; i < pathArr.length; i++) {
          if (!fileMap[pathArr[i]]) {
            allLoaded = false;
            break;
          }
        }

        if (allLoaded) {
          callback();
        }
      }
    }
  }

  window.thin = thin;
})();

```

测试代码如下：
```javascript
thin.define('constant.PI', [], function () {
  return 3.1415926;
});

thin.define('shape.Circle', ['constant.PI'], function (pi) {
  var Circle = function (r) {
    this.r = r;
  };

  Circle.prototype = {
    area: function () {
      return pi * this.r * this.r;
    },
  };

  return Circle;
});

thin.define('shape.Rectangle', [], function () {
  var Rectangle = function (l, w) {
    this.l = l;
    this.w = w;
  };

  Rectangle.prototype = {
    area: function () {
      return this.l * this.w;
    }
  };

  return Rectangle;
});

thin.define('ShapeTypes', ['shape.Circle', 'shape.Rectangle'], function (Circle, Rectangle) {
  return {
    CIRCLE: Circle,
    RECTANGLE: Rectangle,
  };
});

thin.define('ShapeFactory', ['ShapeTypes'], function (ShapeTypes) {
  return {
    getShape: function (type) {
      var shape;

      switch (type) {
        case CIRCLE:
          shape = new ShapeTypes[type](arguments[1]);
          break;
        default:
          shape = new ShapeTypes[type](arguments[1], arguments[2]);
          break;
      }

      return shape;
    }
  };
});

var ShapeFactory = thin.use('ShapeFactory');
alert(ShapeFactory.getShape('CIRCLE', 5).area());
alert(ShapeFactory.getShape('RECTANGLE', 3, 4).area());
```
在这个例子中，定义了四个模块，每个模块只需要定义自己所直接依赖的模块，其他的可以不必定义。也可以来这里看测试链接：[测试用例](http://xufei.github.io/thin/demo/demo.0.1.html)

## 2. 数据绑定
### 2.1 数据绑定的原理

数据绑定是一种很便捷的特性，一些RIA框架带有双向绑定功能，比如Flex和Silverlight, 当某个数据发生变更时，所绑定的界面元素也发生变更；当界面元素发生变化时，数据也跟着变化，这种功能在处理表单数据的填充和收集时，是非常有用的。

在HTML中，原生是没有这样的功能的，但有些框架做到了，它们是怎么做到的呢？我们来做个简单的demo，顺便探讨一下其中原理

先看数据到界面上的绑定：
```javascript
<input vm-value="name">

var person = {
  name: "Tom"
};
```

如果我们直接给name重新赋值，person.name = “jerry”，怎样才能让界面得到变更？

从直觉来说，我们需要在name发生改变时，触发一个事件，或者调用某个指定的方法，然后才好着手做后面的事情，比如：

```javascript
var person = {
  name: "Tom",
  setName: function(newName) {
    this.name = newName;
    //do something
  }
};
```

这样我们可以在setName里面去给input赋值。推而广之，为了使得实体包含的多个属性都可以运作，可以这么做：

```javascript
var person = {
  name: "Tom",
  gender: m,
  set: function (key, value) {
    this[key] = value;
    //do something
  }
}
```

或者合并两个方法，只判断是否传了参数：
```javascript
Person.prototype.name = function (value) {
  if (arguments.length === 0) {
    return this._name;
  } else {
    this._name = value;
  }
}
```

在这种情况下，赋值的时候就是Person.name(“Tom”)， 取值的时候就是var name = Person.name() 了。

有一些框架是通过这种方式来变通实现数据绑定的，对数据的写入只能通过方法调用。但这种方式很不直接，我们来想点别的。

在C#等一些语言中，有一种东西叫存取器，比如说：
```C#
class Person
{
  private string name;

  public string Name
  {
    get
    {
      return name;
    }
    set
    {
      name = value;
    }
  }
}
```

用的时候，Person.Name = “Jerry”, 就会调用到set里，相当于是个方法。

这一点非常好，很符合我们的需要，那JavaScript里面有没有类似存取器的特性呢？老早以前是没有的，但现在有了，那就是Object.defineProperty, 它的第三个参数就是可选的存取函数。比如说：

```javascript
var person = {};

// Add an accessor property to the object.
Object.defineProperty(person, "name", {
  set: function (value) {
    this._name = value;
    // do something
  },
  get: function () {
    return this._name;
  },
  enumerable: true,
  configurable: true
});
```

赋值的时候，person.name = “Tom”, 取值的时候，var name = person.name,简直太美妙了。注意这里define的时候，是定义在实例上的，如果想要定义在类型里面，可以在构造器里面定义。

现在我们从数据到DOM的绑定可以解决掉了，至少我们能够在变量被变更的时候去做一些自己的事情，比如查找这个属性被绑定到那些空间了，然后挨个对其赋值。框架怎么知道属性被绑定到哪些控件了呢？这个直接在第二部分的实现过程中讨论。

再看控件到数据的绑定，这个其实很好理解。无非就是给控件添加change之类的事件监听，在这里，我们在原理方面已经没有什么问题了，现在开始准备把它写出来。

### 2.2 数据绑定的实现
我们的框架在启动之前，要先把前面所说的这种绑定关系收集起来，这种属性会分布于DOM的各个角落，一个很现实的做法是，递归遍历界面的每个DOM节点，检测该属性，于是我们的代码结构大致如下所示：
```javascript
function parseElement(element) {
  for (var i=0, l=element.attributes.length; i<l; i++) {
    parseAttribute(element.attributes[i]);
  }

  for (var i=0, l=element.children.length; i<l; i++) {
    parseElement(element.children[i]);
  }
}
```

但是我们这时候面临一个问题，比如说你的输入框绑定在name变量上，这个name应该从属于什么？它是全局变量吗？

我们在开始做这个框架的时候强调了一个原则：业务模块不允许定义全局变量，框架内部也尽量少有全局作用域，到目前为止，我们只暴露了thin一个全局变量，所以这里不能破坏这个原则。

因此，我们要求业务开发人员去定义一个视图模型，把变量包装起来，所包装的不限于变量，也可以有方法。比如下面，我们定义了一个实体叫Person，带两个变量，两个方法，然后我们演示一下怎么把它们绑定到HTML界面。

```
thin.define("Person", [], function () {
  function Person() {
    this.name = "Tom";
    this.age = 5;
  }
  Person.prototype = {
    growUp: function () {
      this.age++;
    }
  };

  return Person
});
```

模型方面都准备好了，先在来看界面：
```
<div vm-model="Person">
  <input type="text" vm-value="name"/>
  <input type="text" vm-value="age"/>
  <input type="button" vm-click="growUp" value="Grow Up"/>
</div>
```

为了使得结构更加容易看，我们把界面的无关属性比如样式之类都去掉，只留下不能再减少的这么一段。先在我们可以看到，在界面的顶层定义一个vm-model属性，值为实体的名称。两个输入框通过vm-value来绑定到实例属性，vm-init绑定界面的初始方法，vm-click绑定按钮的点击事件。

好了，现在我们可以来扫描这个简单的DOM 结构了。想要做这么一个绑定，首先要考虑数据从哪里来？在绑定name和age属性之前，毫无疑问，应当先实例化一个Person，我们怎样才能知道需要把Person模块实例化呢？

当扫描到一个DOM元素的时候，我们要先检测它的vm-model属性，如果有值，就取这个值来实例化，然后，把这个值一直传递下去，在扫描其他属性或者下属DOM元素的时候带进去。这么一来，parseElement就变成一个递归了，于是，它只好有两个参数，变成了这样：

```
function parseElement (element, vm) {
  var model = vm;

  if (element.getAttribute("vm-model")) {
    model = bindModel(element.getAttribute("vm-model"));
  }

  for (var i = 0, l = element.attributes.length; i < l; i++) {
    parseAttribute(element, element.attributes[i], model);
  }

  for (var i = 0, l = element.children.length;; i < l; i++) {
    parseElement(element.children[i], model);
  }
}
```

看看我们打算怎么来实例化这个模型，这个bindModel方法的参数是模块名，于是我们先去use一下，从工厂里生成出来，然后new一下，先这么return出去吧。

```
function bindModel(modelName) {
  thin.log("model" + modelName);

  var model = thin.use(modelName, true);
  var instance = new model();

  return instance;
}
```

现在我们开始关注parseAttribute函数，可能的attribute有哪些种类呢？我列举了一些很常用的:

* init,  用于绑定初始化方法
* click,  用于绑定点击
* value,  绑定变量
* enable 和 disable，绑定可用状态
* visible 和 invisible，绑定可见状态

然后就可以实现我们parseAttribute函数了：

```
function parseAttribute(element, attr, model) {
  if (attr.name.indexOf("vm-") === 0) {
    var type = attr.name.slice(3);

    switch (type) {
      case "init":
        bindInit(element, attr.value, model);
        break;
      case "value":
        bindValue(element, attr.value, model);
      case "click":
        bindClick(element, attr.value, model);
        break;
      case "enable":
        bindEnable(element, attr.value, model, true);
        break;
      case "disable":
        bindEnable(element, attr.value, model, false);
        break;
      case "visible":
        bindVisible(element, attr.value, model, true);
        break;
      case "invisible":
        bindVisible(element, attr.value, model, false);
        break;
      case "element":
        model[attr.value] = element;
        break;
      default:
        // statements_def
        break;
    }
  }
}
```

注意到最后面还有个element类型，本来可以不要这个，但我们考虑到将来，一切都是组件化的时候，界面上打算不写id，也不依靠选择器，而是用某个标志来定位元素，所以就加上了这个，文章最后的示例中使用了它。

这么多绑定，不打算都讲，用bindValue函数来说明一下吧：
```javascript
function bindValue(element, key, vm) {
  thin.log("binding value: " + key);

  vm.$watch(key, function (value, oldValue) {
    element.value = value || "";
  });

  element.onkeyup = function () {
    vm[key] = element.value;
  };

  element.onpaste = function () {
    vm[key] = element.value;
  }
}
```

我们假定每个模型实例上带有一个$watch方法，用于监控某流量的变化，可以传入一个监听函数，当变量变化的时候，自动调用这个函数，并且把新旧两个值传回来。

在这个代码里，我们使用$watch方法给传入的key添加一个监听，监听器里面给监听元素赋值。我们这里偷懒了一下，假定所有的绑定元素都是输入框，所以直接给element.value设置值，为了防止值为空导致显示undefined，把值跟空字符串用短路表达式做了个转换。

接下来，也对element的几个可能导致值变化的事件进行了监听，在里面把模型上对应的值更新掉。这样双向绑定就做好了。

然后回头来看$watch的实现。很显然这里也要一个Map，我们给它取名为$watchers， 存放属性的绑定关系，它的值需要保存一份，供getter获取，同时还有一个数组，存放了该属性绑定的处理函数。当属性发生变更的时候，去挨个把他们调用一下。

```javascript
var Binder = {
  $watch: function (key, watcher) {
    if (!this.$watchers[key]) {
      this.$watchers[key] = {
        value: this[key],
        list: []
      };

      Object.defineProperty(this, key, {
        set: function (val) {
          var oldValue = this.$watchers[key].value;
          this.$watchers[key].value = val;

          for (var i = 0; i < this.$watchers[key].list.length; i++) {
            this.$watchers[key].list[i](val, oldValue);
          }
        },

        get: function () {
          return this.$watchers[key].value;
        }
      });
    }

    this.$watchers[key].list.push(watcher);
  }
}
```

但是vm怎么就有$watch呢，每个地方都去判断一下非空然后再去创建其实挺麻烦的，所以，这个属性我们可以直接在实例化模型的时候创建出来。

```javascript
function bindModel(name) {
  thin.log("binding model: " + name);

  var model = thin.use(name, true);
  var instance = new model().extend(Binder);
  instance.$watchers = {};

  return instance;
}
```

看看这里的写法，为什么$watchers要额外设置，而$watch就可以放在Binder里面来extend呢？

先解释extend干了什么，它做的是一个对象的浅拷贝，也就是说，把Binder的属性和方法都复制给了创建出来的model实例，注意，这个所谓的复制，如果是简单类型，那确实复制了，如果是引用类型，那复制的其实只是一个引用，所以如果$watchers也放在Binder里，不同的instance就共享一个$watchers，逻辑就是错误的。那为什么$watch又可以放在这里复制呢？因为它是函数，它的this始终指向当前的执行主体，也就是说，如果放在instance1上执行，指向的就是instance1，放在instance2上执行，指向的就是instance2，我们利用这一点，就可以不用让每个实例都创建一份$watch方法，而是共用同一个。

```
<!DOCTYPE html>
<html>
<head>
    <title>Simple binding demo</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="binding">
    <meta name="author" content="xu.fei@outlook.com">
    <script type="text/javascript" src="../js/thin.js"></script>
</head>
<body>
<div vm-model="test.Person">
    <input type="text" vm-value="name"/>
    <input type="text" vm-value="age"/>
    <input type="text" vm-value="age"/>
    <input type="button" vm-click="growUp" value="Grow Up"/>
</div>

<div vm-model="test.Person" vm-init="init">
    <input type="text" vm-value="name"/>
    <input type="text" vm-value="age"/>
    <input type="button" vm-click="growUp" value="Grow Up"/>
</div>
<script type="text/javascript">
    thin.define("test.Person", [], function () {
        function Person() {
            this.name = "Tom";
            this.age = 5;
        }

        Person.prototype = {
            init: function () {
                this.name = "Jerry";
                this.age = 3;
            },

            growUp: function () {
                this.age++;
            }
        };

        return Person;
    });
</script>
</body>
</html>
```

或者访问这里：[从零开始编写自己的 JavaScript 框架）—— Demo](http://xufei.github.io/thin/demo/simple-binding.html)
以刚才文章提到的内容，还不能完全解释这个例子的效果，因为没看到在哪里调用 parseElement 的。说来也简单，就在 thin.js 里面，直接写了一个 thin.ready，在那边调用了这个函数，去解析了 document.body，于是测试页面里面才可以只写绑定和视图模型。

我们还有一个更实际一点的例子，结合了另外一个系列里面写的简单 DataGrid 控件，做了一个很基础的人员管理界面：[http://xufei.github.io/thin/demo/binding.html](http://xufei.github.io/thin/demo/binding.html)

### 2.3 小结
到此为止，我们的绑定框架勉强能够运行起来了！虽然简陋，而且要比较新的浏览器才能跑，但是毕竟跑起来了。

注意：Object.defineProperty 仅在 Chrome 等浏览器中可用，IE 需要9以上才比较正常。在司徒正美的 avalon 框架中，巧妙使用 VBScript 绕过这一限制，利用 vbs 的 property 和两种语言的互通，实现了低版本IE的兼容，我们这个框架的目标不是兼容，而是为了说明原理，所以感兴趣的朋友可以去看看 avalon 的源码。