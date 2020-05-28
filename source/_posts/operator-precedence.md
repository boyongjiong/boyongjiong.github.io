---
title: JavaScript 运算符优先级
date: 2017-08-16 11:42:59
tags:
categories: 工具类
---

运算符的优先级决定了表达式中运算执行的先后顺序，优先级高的运算符最先被执行。

<table class="fullwidth-table">
 <tbody>
  <tr>
   <th>优先级</th>
   <th>运算类型</th>
   <th>关联性</th>
   <th>运算符</th>
  </tr>
  <tr>
   <td>19</td>
   <td><a href="/zh-CN/docs/Web/JavaScript/Reference/Operators/Grouping" title="圆括号运算符( )&nbsp;用来控制表达式中的运算优先级."><code>圆括号</code></a></td>
   <td>n/a</td>
   <td><code>( … )</code></td>
  </tr>
  <tr>
   <td rowspan="3">18</td>
   <td><a href="/zh-CN/docs/Web/JavaScript/Reference/Operators/Property_Accessors#点符号表示法" title="属性访问器提供了两种方式用于访问一个对象的属性，它们分别是点符号和括号。"><code>成员访问</code></a></td>
   <td>从左到右</td>
   <td><code>… . …</code></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/JavaScript/Reference/Operators/Property_Accessors#括号表示法" title="属性访问器提供了两种方式用于访问一个对象的属性，它们分别是点符号和括号。"><code>需计算的成员访问</code></a></td>
   <td>从左到右</td>
   <td><code>… [ … ]</code></td>
  </tr>
  <tr>
   <td><a href="/zh-CN/docs/Web/JavaScript/Reference/Operators/new" title="new运算符的作用是创建一个对象实例。这个对象可以是用户自定义的，也可以是一些系统自带的带构造函数的对象。"><code>new</code></a> (带参数列表)</td>
   <td>n/a</td>
   <td><code>new … ( … )</code></td>
  </tr>
  <tr>
   <td rowspan="2">17</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions" title="JavaScript/Reference/Operators/Special_Operators/function_call">函数调用</a></td>
   <td>从左到右</td>
   <td><code>… (&nbsp;<var>…&nbsp;</var>)</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new" title="JavaScript/Reference/Operators/Special_Operators/new_Operator">new</a>&nbsp;(无参数列表)</td>
   <td>从右到左</td>
   <td><code>new …</code></td>
  </tr>
  <tr>
   <td rowspan="2">16</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Increment" title="JavaScript/Reference/Operators/Arithmetic_Operators">后置递增</a>(运算符在后)</td>
   <td>n/a</td>
   <td><code>… ++</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Decrement" title="JavaScript/Reference/Operators/Arithmetic_Operators">后置递减</a>(运算符在后)</td>
   <td>n/a</td>
   <td><code>… --</code></td>
  </tr>
  <tr>
   <td rowspan="9">15</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_NOT">逻辑非</a></td>
   <td>从右到左</td>
   <td><code>! …</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_NOT" title="JavaScript/Reference/Operators/Bitwise_Operators">按位非</a></td>
   <td>从右到左</td>
   <td><code>~ …</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Unary_plus" title="JavaScript/Reference/Operators/Arithmetic_Operators">一元加法</a></td>
   <td>从右到左</td>
   <td><code>+ …</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Unary_negation" title="JavaScript/Reference/Operators/Arithmetic_Operators">一元减法</a></td>
   <td>从右到左</td>
   <td><code>- …</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Increment" title="JavaScript/Reference/Operators/Arithmetic_Operators">前置递增</a></td>
   <td>从右到左</td>
   <td><code>++ …</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Decrement" title="JavaScript/Reference/Operators/Arithmetic_Operators">前置递减</a></td>
   <td>从右到左</td>
   <td><code>-- …</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof" title="JavaScript/Reference/Operators/Special_Operators/typeof_Operator">typeof</a></td>
   <td>从右到左</td>
   <td><code>typeof …</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/void" title="JavaScript/Reference/Operators/Special_Operators/void_Operator">void</a></td>
   <td>从右到左</td>
   <td><code>void …</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/delete" title="JavaScript/Reference/Operators/Special_Operators/delete_Operator">delete</a></td>
   <td>从右到左</td>
   <td><code>delete …</code></td>
  </tr>
  <tr>
   <td rowspan="3">14</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Multiplication" title="JavaScript/Reference/Operators/Arithmetic_Operators">乘法</a></td>
   <td>从左到右</td>
   <td><code>… *&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Division" title="JavaScript/Reference/Operators/Arithmetic_Operators">除法</a></td>
   <td>从左到右</td>
   <td><code>… /&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Remainder" title="JavaScript/Reference/Operators/Arithmetic_Operators">取模</a></td>
   <td>从左到右</td>
   <td><code>… %&nbsp;…</code></td>
  </tr>
  <tr>
   <td rowspan="2">13</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Addition" title="JavaScript/Reference/Operators/Arithmetic_Operators">加法</a></td>
   <td>从左到右</td>
   <td><code>… +&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Subtraction" title="JavaScript/Reference/Operators/Arithmetic_Operators">减法</a></td>
   <td>从左到右</td>
   <td><code>… -&nbsp;…</code></td>
  </tr>
  <tr>
   <td rowspan="3">12</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators" title="JavaScript/Reference/Operators/Bitwise_Operators">按位左移</a></td>
   <td>从左到右</td>
   <td><code>… &lt;&lt;&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators" title="JavaScript/Reference/Operators/Bitwise_Operators">按位右移</a></td>
   <td>从左到右</td>
   <td><code>… &gt;&gt;&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators" title="JavaScript/Reference/Operators/Bitwise_Operators">无符号右移</a></td>
   <td>从左到右</td>
   <td><code>… &gt;&gt;&gt;&nbsp;…</code></td>
  </tr>
  <tr>
   <td rowspan="6">11</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Less_than_operator" title="JavaScript/Reference/Operators/Comparison_Operators">小于</a></td>
   <td>从左到右</td>
   <td><code>… &lt;&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Less_than__or_equal_operator" title="JavaScript/Reference/Operators/Comparison_Operators">小于等于</a></td>
   <td>从左到右</td>
   <td><code>… &lt;=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Greater_than_operator" title="JavaScript/Reference/Operators/Comparison_Operators">大于</a></td>
   <td>从左到右</td>
   <td><code>… &gt;&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Greater_than_or_equal_operator" title="JavaScript/Reference/Operators/Comparison_Operators">大于等于</a></td>
   <td>从左到右</td>
   <td><code>… &gt;=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/in" title="JavaScript/Reference/Operators/Special_Operators/in_Operator">in</a></td>
   <td>从左到右</td>
   <td><code>… in&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof" title="JavaScript/Reference/Operators/Special_Operators/instanceof_Operator">instanceof</a></td>
   <td>从左到右</td>
   <td><code>… instanceof&nbsp;…</code></td>
  </tr>
  <tr>
   <td rowspan="4">10</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Equality" title="JavaScript/Reference/Operators/Comparison_Operators">等号</a></td>
   <td>从左到右</td>
   <td><code>… ==&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Inequality" title="JavaScript/Reference/Operators/Comparison_Operators">非等号</a></td>
   <td>从左到右</td>
   <td><code>… !=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Identity" title="JavaScript/Reference/Operators/Comparison_Operators">全等号</a></td>
   <td>从左到右</td>
   <td><code>… ===&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Nonidentity" title="JavaScript/Reference/Operators/Comparison_Operators">非全等号</a></td>
   <td>从左到右</td>
   <td><code>… !==&nbsp;…</code></td>
  </tr>
  <tr>
   <td>9</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_AND" title="JavaScript/Reference/Operators/Bitwise_Operators">按位与</a></td>
   <td>从左到右</td>
   <td><code>… &amp;&nbsp;…</code></td>
  </tr>
  <tr>
   <td>8</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_XOR" title="JavaScript/Reference/Operators/Bitwise_Operators">按位异或</a></td>
   <td>从左到右</td>
   <td><code>… ^&nbsp;…</code></td>
  </tr>
  <tr>
   <td>7</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_OR" title="JavaScript/Reference/Operators/Bitwise_Operators">按位或</a></td>
   <td>从左到右</td>
   <td><code>… |&nbsp;…</code></td>
  </tr>
  <tr>
   <td>6</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_AND" title="JavaScript/Reference/Operators/Logical_Operators">逻辑与</a></td>
   <td>从左到右</td>
   <td><code>… &amp;&amp;&nbsp;…</code></td>
  </tr>
  <tr>
   <td>5</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Logical_OR" title="JavaScript/Reference/Operators/Logical_Operators">逻辑或</a></td>
   <td>从左到右</td>
   <td><code>… ||&nbsp;…</code></td>
  </tr>
  <tr>
   <td>4</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator" title="JavaScript/Reference/Operators/Special_Operators/Conditional_Operator">条件运算符</a></td>
   <td>从右到左</td>
   <td><code>… ? … : …</code></td>
  </tr>
  <tr>
   <td rowspan="12">3</td>
   <td rowspan="12"><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Assignment_Operators" title="JavaScript/Reference/Operators/Assignment_Operators">赋值</a></td>
   <td rowspan="12">从右到左</td>
   <td><code>… =&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… +=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… -=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… *=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… /=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… %=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… &lt;&lt;=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… &gt;&gt;=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… &gt;&gt;&gt;=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… &amp;=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… ^=&nbsp;…</code></td>
  </tr>
  <tr>
   <td><code>… |=&nbsp;…</code></td>
  </tr>
  <tr>
   <td rowspan="2" colspan="1">2</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield" title="JavaScript/Reference/Operators/yield">yield</a></td>
   <td>从右到左</td>
   <td><code>yield&nbsp;…</code></td>
  </tr>
  <tr>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield*" title="JavaScript/Reference/Operators/yield">yield*</a></td>
   <td>从右到左</td>
   <td>yield*&nbsp;…</td>
  </tr>
  <tr>
   <td>1</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator" title="JavaScript/Reference/Operators/Spread_operator">展开运算符</a></td>
   <td>n/a</td>
   <td><code>...</code>&nbsp;…</td>
  </tr>
  <tr>
   <td>0</td>
   <td><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comma_Operator" title="JavaScript/Reference/Operators/Comma_Operator">逗号</a></td>
   <td>从左到右</td>
   <td><code>… ,&nbsp;…</code></td>
  </tr>
 </tbody>
</table>

