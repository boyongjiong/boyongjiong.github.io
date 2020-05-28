---
title: JavaScript 装载和执行
date: 2018-06-20 13:51:26
tags:
categories: 技术向
---

首先我想说一下 JavaScript 的装载和执行。通常来说，浏览器对于 JavaScript 的运行有两大特性：

	1. 载入后马上执行
	2. 执行时会阻塞后续的内容（包括页面的渲染、其它资源的下载）

于是，如果有多个 js 文件被引入，那么对于浏览器来说，这些 js 文件被串行的载入，并依次执行。

因为 JavaScript 可能会来操作HTML 文档的 DOM 树，所以，浏览器一般都不会像并行下载 css 文件并行下载 js 文件，因为这是 js 文件的特殊性造成的。所以，如果你的 JavaScript 想操作后面的 DOM 元素，基本上来说，浏览器都会报错说对象找不到。因为 JavaScript 执行时，后面的 HTML 被阻塞住了，DOM 树里还没有后面的 DOM 节点。所以程序也就报错了。

## 传统的方式
所以，当你在代码中写下如下的代码：
```html
  <script type="text/javascript" src="https://coolshell.cn/asyncjs/alert.js"></script>
```

基本上来说，head 里的 *script* 标签会阻塞后续资源的载入以及整个页面的生成——[实例一](https://coolshell.cn/asyncjs/async_test01.html)。注意：我的 alert.js 中只有一句话 alert(‘hello world’)，这更容易让你看到 JavaScript 是怎么阻塞后面的东西的。

所以你知道为什么很多网站把 JavaScript 放在网页的最后面了，要么就是动用了 window.onload 或是 document.ready 之类的事件。

另外，因为绝大多数的 JavaScript 代码并不需要等页面，所以我们需要异步载入的功能。那么我们怎么异步载入呢？

## document.write方式
于是你可能以为 document.write()这种方式能够解决不阻塞的方式。你当然会觉得，document.write 了 *script* 标签后就可以执行后面的东西去了，这没错。对于同一个 script 标签里的 JavaScript 代码来说，是这样的，但是对于整个页面来说，这个还是会阻塞。下面是一段测试代码：
```
  <script type="text/javascript" language="javascript">
    function loadjs(script_filename) {
      document.write('<' + 'script language="javascript" type="text/javascript"');
      document.write(' src="' + script_filename + '">');
      document.write('<'+'/script'+'>');
      alert("loadjs() exit...");
    }

    var script = 'https://coolshell.cn/asyncjs/alert.js';
    loadjs(script);
    alert("loadjs() finished!");
  </script>

  <script type="text/javascript" language="javascript">
    alert("another block");
  </script>
```

你觉得 alert 的顺序是什么？你可以在不同的浏览器中试一试。这是相关测试页面——[实例二](https://coolshell.cn/asyncjs/async_test02.html)

## script 的 defer 和 async 属性
IE 自从 IE6就支持 defer 标签，如：
```
<script defer type="text/javascript" src="./alert.js">
</script>
```

对于IE 来说，这个标签会让 IE 并行下载 js 文件，并且把其执行hold 到了整个 DOM 装载完毕（DOMContentLoaded），多个defer 的 *script* 在执行时也会按照其出现的顺序来运行。最重要的是 *script* 被加上 defer 后，其不会阻塞后续 DOM 的渲染。但是因为这个 defer 只是 IE 专用，所以一般用的比较少。

而我们标准的 HTML5也加入了一个异步载入 JavaScript 的属性：async，无论你对它赋什么样的值，只要它出现，它就开始异步加载 js 文件。但是，async 的异步加载会有一个比较严重的问题，那就是它忠实的践行着“载入后马上执行”这条军规，所以，虽然它并不阻塞页面的渲染，但是你也无法控制它执行的次序和时机。你可以[看看这个示例去感受一下](https://coolshell.cn/asyncjs/async_test01.async.html)

支持 async 标签的浏览器是：Firefox3.6+, Chrome 8.0+, Safari 5.0+, IE 10+, Opera 还不支持（[来自这里](http://caniuse.com/#feat=script-async)）。所以这个方法也不是太好。因为并不是所有的浏览器你都能行。

## 动态创建 DOM 方式
这种方式可能是用的最多的了。
```
function loadjs(script_filename) {
	var script = document.createElement('script');
	script.setAttribute('type', 'text/javascript');
	script.setAttribute('src', 'script_filename);
	script.setAttribute('id', 'coolshell_script_id');

	script_id = document.getElementById('coolshell_script_id');
	if (script_id) {
		document.getElementsByTagName('head')[0].removeChild(script_id);
	}
	document.getElementsByTagName('head')[0].appendChild(script);
}

var script = 'https://coolshell.cn/asyncjs/alert.js';
loadjs(script);
```

这种方式几乎成了标准的异步载入 js 文件的方式，这个方式的演示请参看：[示例三](https://coolshell.cn/asyncjs/async_test03.html)。这方式还被玩出了 JSONP 的东东，也就是我可以为 script 的 src 指定某个后台的脚本（如 PHP），而这个 PHP 返回一个 JavaScript 函数，其参数是一个 json 的字符串，返回来调用我们的预先定义好的 JavaScript 函数。你可以看一下这个示例 [t.js](https://coolshell.cn/t.js)(这个示例是我之前在微博征集的[一个异步调用的小例子](https://coolshell.cn/t.html))。

## 按需异步载入 js
上面那个 DOM 方式的例子解决了异步载入 JavaScript 的问题，但是没有解决我们想让他按我们指定的时机运行的问题。所以，我们只需要把上面那个 DOM 方式绑到某个事件上来就可以了。

比如:
*绑定在 window.onload 事件上*—— [示例四](https://coolshell.cn/asyncjs/async_test04.html)

你一定要比较一下示例四和示例三在执行上有什么不同，我在这两个示例中都专门用了个代码高亮的 JavaScript，看看那个代码高亮的脚本执行和我的 alert.js 的执行情况，你就知道不同了。
```javascript
window.onload = loadjs('https://coolshell.cn/asyncjs/alert.js');
```

*绑定在特定的事件上* —— [示例五](https://coolshell.cn/asyncjs/async_test05.html)

```html
<p style="cursor: pointer" onclick="loadJS()">Click to load alert.js</p>
```

这个示例很简单了。当你点击某个 DOM 元素，才会真正载入我们的 alert.js。

## 更多
但是绑定在某个特定事件上这个事似乎又过了一点，因为只有在点击的时候才会去真正的下载 js，这又会太慢了。好了，到这里，要抛出我们的终极问题 —— *我们想异步的把 js 文件下载到用户的本地，但是不执行，仅当在我们想要执行的时候去执行*。

要是我们有下面这样的方式就好了：

```
var script = document.createElement('script');
script.noexecute = true;
script.src = 'alert.js';
document.body.appendChild(script);

// 后面我们可以这么干
script.execute();
```

可惜的是，这只是一个美丽的梦想，今天我们的 JavaScript 还比较原始，这个 JS 梦 还没有实现呢。

所以，我们的程序员只能使用 hack的方式来搞。

有的程序员使用了非标准的 script 的 type 来 cache JavaScript。如
```
<script type="cache/script" src="./alert.js"></script>
```

因为“cache/script”，这个东西根本就不能被浏览器解析，所以浏览器也就不能把 alert.js 当 JavaScript 去执行，但是他又要去下载 js 文件，所以就可以搞定了。可惜的是，webkit 严格遵从了 HTML 的标准——对于这种不认识的东西，直接删除，什么也不干。于是，我们的梦又破了。

所以我们需要再 hack 一下，就像 N 多年前玩 preload 图片那样，我们可以动用 object 标签（也可以动用 iframe 标签），于是我们又下面这样的代码：
```
function cachejs(script_filename) {
	var cache = document.createElement('object');
	cache.data = script_filename;
	cache.id = "collshell_script_cache_id";
	cache.width: 0;
	cache.height = 0;
	document.body.appendChild(cache);
}
```

然后，我们在最后调用一下这个函数。请参看一下相关的示例：[示例六](https://coolshell.cn/asyncjs/async_test06.html)

在 Chrome 下按 Ctrl+Shift+I，切换到network 页，你就可以看到下载了 alert.js，但是没有执行。然后我们再用示例五的方式，因为浏览器端有缓存了，不会再从服务器上下载 alert.js 了。所以就能保证执行速度了。

关于 preload 这种东西你应该不会陌生了。你还可以使用 Ajax 的方式，如：
```
var xhr = new XMLHttpRequest();
xhr.open('GET', 'new.js');
xhr.send();
```

到这里我就不再多说了，也不给示例了，大家可以自己试试去。

最后再提两个 js，一个是 ControlJS，一个叫 HeadJS，专门用来做异步 load JavaScript 文件的。
