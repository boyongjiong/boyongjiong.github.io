---
title:  An interesting interview question
date: 2018-03-26 01:03:02
tags:
categories: 面试向
---

```javascript
function Foo() {
	getName = function () { alert(1); };
  return this;
}

Foo.getName = function () { alert(2); }

Foo.prototype.getName = function () { alert(3); }

var getName = function () { alert(4); }

function getName() { alert(5); }

Foo.getName();  // 2
getName();  // 4
Foo().getName(); // 1
getName();  // 1
new Foo.getName();  // 2
new Foo().getName();  // 3
new new Foo().getName();  // 3
```

请写出上面代码执行的结果，并解释原因