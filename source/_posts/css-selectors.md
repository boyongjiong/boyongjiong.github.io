---
title: 30个你必须记住的CSS选择器
date: 2017-09-09 16:16:24
tags:
categories:
---

当使用javascript库时，如流行的jquery，最好尽可能使用这些css3本身的选择器而不是使用库的自定义方法/选择器。这样能使我们的代码更快，因为这些选择器引擎本身就能被浏览器解析，而不是用这些库选择器。

##1.*
```
* {
  margin:0;
  padding:0;
}
```
对于初学者，在学习更多高级选择器之前，最先了解的选择器。
星号选择器将匹配页面里的每一个元素。很多开发者使用这个技巧将外边距好内边距重置为零。建议不要在生产代码中使用它，因为它给浏览器带来大量不必要的负担。
*也能作为子选字符使用。
```
#container * {
  border: 1px solid black;
}
```
这将匹配#container div的每一个后代元素。再次强调，尽量不要使用这种技术。

兼容性
IE6+
Firefox
Chrome
Safari
Opera

## 2.#X
```
#container {
  width:960px;
  margin:auto;
}
```
井号前缀允许我们选择id，这是常见的用法，不过应该慎重使用id选择器。
id选择符是唯一的，不允许重复使用。如果可能的话，先尝试使用一个标签名称，一个新的html5元素，甚至是一个伪类。

兼容性
IE6+
Firefox
Chrome
Safari
Opera

## 3. .X
.error {
 color: red;
}
现在介绍的是类选择器。id和类的不同之处在于后者可以多次使用。当你想给一组元素应用样式的时候就可以使用类选择器。另外，当你仅想给特殊元素应用的时候才使用id。

兼容性
IE6+
Firefox
Chrome
Safari
Opera

## 4.X Y
```
li a {
  text-decoration: none;
}
```
常用的后代选择符。当你需要给你的选择符增加特殊性的时候你可以使用。

兼容性
IE6+
Firefox
Chrome
Safari
Opera

## 5.X
```
a { color: red;}
ul { margin-left: 0;}
```
如果你想匹配页面上的所有元素，根据他们的类型，而不是id或类名。显而易见，使用类型选择符。如果你需要选择所有的无序列表，请使用ul {}。

兼容性
IE6+
Firefox
Chrome
Safari
Opera

## 6.X:visited and X:link
```
a:link { color:red;}
a:visited { color:purple;}
```
我们使用 :link 伪类选择符选择所有已被点击过的锚标签
此外，我们也有 :visited 伪类选择符，正如你期望的，允许我们仅给页面上被点击过的或被访问过的锚标签应用样式。

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 7.X + Y
```
ul + p {
  color:red;
}
```
这被称作相邻选择符。它将只选择仅紧贴在X元素之后Y元素。上面的例子，仅每一个ul之后的每一个段落元素的文本为红色。

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 8.X > Y
```
div#container > ul {
  border: 1px solid black;
}
```
X Y和 X > Y之间的不同点是后者只选择直接子代。例如，考虑如下的标记。
```
<div id="container">
  <ul>
    <li>List Item
      <ul>
        <li>Child</li>
      </ul>
    </li>
    <li>List Item</li>
    <li>List Item</li>
    <li>List Item</li>
  </ul>
</div>
```
选择符#container > ul将只选择id为container的div的直接子代ul。它不匹配更深层的li的子代的ul。因此，使用子代选择符有性能上的优势。事实上，这同样适用于基于css选择器的javascript引擎。

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 9.X ~ Y
```
ul ~ p {
  color: red;
}
```
这是兄弟选择符和X + Y一样，然而，它没有约束。与相邻选择符（ul + li）仅选择前一个选择符后面的第一个元素比起来，兄弟选择符更宽泛。他会选择，我们上面的例子中选择跟在ul后面的任何p元素。

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 10.X[title]
```
a[title] {
  color: green;
}
```
被称为属性选择器，在我们上面的例子里，这只会选择有title属性的锚标签。没有此属性的锚标签将不受影响。

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 11.X[href="foo"]
```
a[href="http://net.tutsplus.com"] {
  color: #1f6053; /* nettuts green */
}
```
上面的代码段落将所有href属性为http://net.ttsplus.com的锚标签添加样式；他们将会显示为我们的品牌绿色。所有其它的锚标签将不受影响。
注意我们将href值用引号包裹。记住，当使用javascript的css选择符引擎时也这么做。如果可能的话，尽可能使用css3选择符代替非官方的方法。
这工作的很好，但有点不够灵活。如果连接确实直接连接到Nettus+还好，但是，也许路径是nettus的相对路径呢？在这种情况下，我们可以使用一点正则表达式语法。

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 12.X[href*="nettuts"]
```
a[href*="tuts"] {
  color: #1f6053; /* nettuts green */
}
```
这个就是我们需要的代码，*号指定了包含该属性的值必须包含定义的值。就是说，这句代码包含了href值为nettuts.com,net.tutsplus.com或者tutsplus.com。
记住这个描述过于宽泛，如果是某个锚点标签连接到某个连接中带有tuts非Envato的网站（tuts属于Envato旗下网站）呢？因此我们需要更多特性来限制，分别使用^和$来限定字符串的开始和结束。

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 13.X[href^="http"]
```
a[href^="http"] {
  background: url(path/to/external/icon.png) no-repeat;
  padding-left: 10px;
}
```
使用^(carat)符，^ 在正则表达式中表示字符串的开始。如果我们想指向所有以”http“开头的"href"属性的锚点的话，我们就可以使用类似于上面的那段代码了。
注意：我们并不需要搜索"http://",完全没有必要，因为我们还要包含以"https://"开头的链接呢。
如果我们想为所有连接到图片的链接定义样式呢？这种情况下，我们就得搜索字符串的结束了……

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 14.X[href$=".jpg"]

```
a[href$=".jpg"] {
  color: red;
}
```

还是使用正则表达式符号，$(dolor),来作为字符串的结束标记。这种情况下，我们就会搜索所有url以.jpg为结尾的锚点了，记住**这种情况下gif和png格式的图片不会被选择**。

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 15.X[data-*="fooo"]

```
a[data-filetype="image"] {
  color: red;
}
```
回顾最近一条，我们如何能包含各种图片类型：.png,.jpeg,.jpg,.gif?很容易想到，我们可以通过多个选择器来实现，像这样：

```
a[href$=".png"],
a[href$=".gif"],
a[href$="jpeg"],
a[href$="jpg"] {
  color: red;
}
```
不过这样很蛋疼，效率极低。另一个解决办法是使用自定义属性。如果我们加了一种自己的data-filetype属性给每一个链接到图片的锚点的话呢？

```
<a href="path/to/image/.jpg" data-filetype="image"> Image Link </a>
```
这样关联后，我们就能使用标准的属性选择器来指定这些链接了。如下面：

```
a[data-tiletype="image"] {
  color: red;
}
```

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 16.X[foo~="bar"]

```
a[data-info~="external"] {
  color: red;
}
a[data-info~="image"] {
  border:1px solid black;
}
```
这儿有个鲜为人知的特殊技巧，绝对让你印象深刻，~(tilda)符，它可以帮助我们指向那些属性为以空格隔开的多个值的元素。

```
<a href="path/to/image.jpg" data-info="external image">
  Click me,Fool
</a>
```
上面代码中我们创建了data-info属性，这个属性可以定义以空格分隔的多个值。这样，这个链接本身就是个icon，并且指向的也是一个图片链接。
有了这样适当的标记，通过使用~属性选择器的技巧，我们就可以指向任何具有着两种属性的任何一种了。

```
/*Target data-info attr that contains the value "external" */
a[data-info~="external"] {
  color: red;
}
/* And which contains the value "image" */
a[data-info~="image"] {
  border: 1px solid black;
}
```
it's awesome！

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 17.X:checked

```
input[type=radio]:checked {
  border: 1px solid black;
}
```
这种伪类只会匹配已经被选中的单选元素。就是介么简单0，0

兼容性：
IE9+
Firefox
Chrome
Safari
Opera

## 18.X:after

bofore和after伪元素也很蛋疼。貌似人们么天都能找到或者发明一些新办法来有效地使用它们，他们很容易控制选择器周围的内容。
很多第一次使用是因为他们需要对chear-fix进行改造。

```
.clearfix:after {
  content: "";
  display: block;
  clear: both;
  visibility: hidden;
  font-size: 0;
  height: 0;
}

.clearfix {
  display: inline-block;
  height: 1%;
}
```
这个改进使用了`：after`伪类元素来在元素后面增加一个空间，然后清除它。这个牛X的技巧我们应该收藏到工具包，特别是当`overflow: hidden`方法无能为力的时候。

通过CSS3选择器的标准说明书，我们应该有技巧的使用这些伪类语法--双冒号::。不过为了兼容，浏览器会接受一个双引号。

兼容性
IE8+
Firefox
Chrome
Safari
Opera

## 19.X:hover

```
div:hover {
  background:#e3e3e3;
}
```
典型的官方形式的用户触发伪类，想在用户在某个元素上面悬停时定义个别的样式？这个属性就是干这个的。
较老版本的IE，只能在锚点标签后使用这个hover。
这个选择器用的最多的情况，估计可能就是在锚点的悬停时加个border-bottom了。

```
a:hover {
  border-bottom: 1px solid black;
}
```

兼容性
IE6+(in IE6, :hover must be applied to an anchor element)
Firefox
Chrome
Safari
Opera

## 20.X:not(selector)

```
div:not(#container) {
  color: blue;
}
```
not 伪类非常有用。例如我要选择所有的div，除了id为container的。上面的代码片段就能完美的实现。
如果我想选择除了p以外的所有元素，我可以这么做

```
*:not(p) {
  color: green;
}
```

兼容性
IE9+
Firefox
Chrome
Safari
Opera

## 21.X::pseudoElement

```
p::first-line {
  font-weight: bold;
  font-size: 1.2em;
}
```
我们可以使用伪元素（以::表示）来定义元素的样式。例如第一行，第一个字符，记住，这种方法只能应用于同级元素才有效。
伪元素由两个冒号组成： `::` 
**指定p的第一个字符的样式：**

```
p::first-letter {
  float: left;
  font-size: 2em;
  font-weight: bold;
  font-family: cursive;
  padding-right: 2px; 
}
```
这段代码会找到所有段落，然后从中定义这些段落的第一个字符。
这常常使用在仿报纸的文章首字母样式。

**指定p的首行样式：**

```
p::first-line {
  font-weight: bold;
  font-size: 2em;
}
```
同样，这个`::first-line`会像我们期望的那样，只定义段落的第一行的样式。

兼容性
IE6+
Firefox
Chrome
Safari
Opera

## 22.X:nth-child(n)

```
li:nth-child(3) {
  color: red;
}
```
`:nth-chlid`伪类解决了无法从元素堆中选择元素的尴尬局面。
注意：nth-child接收整数和变量，不过不是从0开始的，如果你想选定第二个li，使用 li:nth-child(2).
我们甚至使用这个办法来选择任意的子元素。例如，我们可以用`li:nth-child(4n)`来完成4为倍数的所有元素的选择。

兼容性
IE9+
Firefox
Chrome
Safari
Opera

## 23.X:nth-last-child(n)

```
li:nth-last-child(2) {
  color: red;
}
```
如果我有非常多的li在ul里面，而我只想要最后3个li怎么办？可以使用nth-last-child伪类，即li:nth-last-child(3)

兼容性
IE9+
Firefox
Chrome
Safari
Opera

## 24.X:nth-of-type(n)

```
ul:first-child {
  border: 1px solid black;
}
```
应该会有很多时候想要元素类型来选择元素而不是通过后代选择。
想象一下标记5个无需列表（ul）。如果你想定义第三个ul，并且没有一个唯一的id来找到它，你就可以使用nth-of-type(3)伪类了，在上面代码中，只要第三个ul才会有黑色的边框。

兼容性
IE9+
Firefox 3.5+
Chrome
Safari

## 25.X:nth-last-of-type(n)
```
ul:nth-last-of-type(3) {
  border: 1px solid black;
}
```
我们也可以通过使用nth-last-of-type()来从结束开始回溯这个选择器，来找到我们想要的元素。

兼容性
IE9+
Firefox 3.5+
Chrome
Safari

## 26.X:first-child
```
ul li:first-child {
  border-top: none;
}
```
这个结构的伪类让我们可以选择某个元素的第一个后代。通常可以通过使用这个办法来删除第一个或者最后一个元素的边框。
例如有一系列的rows，每一个都有border-top和border-bottom。这种情况下，第一行和最后一行会看起来非常怪，所以很多设计师会使用first类和last类来弥补这个缺陷。

兼容性
IE7+
Firefox
Chrome
Safari
Opera

## 27.X:last-child
```
ul li{
  color: green;
}
```
与first-child不同，last-child会选择父节点的最后一个子节点

例如：
我们建立一个例子来示范这些类的可能的用法。我们会建立一种风格来显示。

html
```
<ul>
  <li>List Item</li>
  <li>List Item</li>
  <li>List Item</li>
  <li>List Item</li>
</ul>
```
css
```
ul {
  width:200px;
  background:#292929;
  color:white;
  list-style:none;
  padding-left:0;
}
li {
  padding:10px;
  border-bottom: 1px solid black;
  border-top: 1px solid #3c3c3c;
}
```
这个样式会设置一个背景，删除浏览器默认的ul的padding值，并定义边框给每一个li来提供一点深度。唯一的问题就是最上面的边框和最下面的边框看起来有点怪，使用`first-child`和`last-child`来解决这个问题
```
li:first-child {
  border-top: none;
}
li:last-child {
  border-bottom: none;
}
```
效果自行观察

兼容性
IE9+(IE8支持`first-child`而不支持`last-child`)
Firefox
Chrome
Safari
Opera

## 28.X:only-child
```
div p:only-child {
  color: red;
}
```
它可以帮助我们选择是父节点的独生子（没有别的后代）的元素。例如，使用上面代码，只有是div的唯一后代的p段落才会定义其颜色为red。
让我们假定下面的标记：

```
<div><p>My paragraph here.</p></div>
<div>
  <p>Two paragraphs total.</p>
  <p>Two paragraphs total.</p>
</div>
```

兼容性
IE 9+
Firefox
Chrome
Safari
Opera

## 29.X:only-of-type
```
li:only-of-type {
  font-weight: bold;
}
```
这种结构的伪类有几种非常聪明的用法。它可以选定在其父节点内没有兄弟节点的元素。例如，我们可以选择只有一个li为其后代的ul。
首先，取决于你想怎样完成这一目标。你可以使用`ul li`,不过，这会选择所有li元素。唯一的办法就是使用`only-of-type`.
```
ul>li:only-of-type {
  font-weight: bold;
}
```

兼容性
IE 9+
Firefox
Chrome
Safari
Opera

## 30.X:first-of-type
first-of-type伪类可以让你选择该类型的第一个兄弟节点。

for example：

为了更好的了解，我们来测试下
```
<div>
  <p>My paragraph here.</p>
  <ul>
    <li>List Item 1.</li>
    <li>List Item 2.</li>
  </ul>
  <ul>		
    <li>List Item 3.</li>
    <li>List Item 4.</li>
  </ul></div>
```

试试看如何能只选择”List Item 2“

### 解决办法1

```
ul:first-of-type > li:nth-child(2) {
  font-weight: bold;
}
```
这种方案是先找到第一个无序列表ul，然后利用`nth-child(2)`得到第二个`li`.

### 解决办法2

```
p + ul li:last-child {
  font-weight: bold;
}
```
在这个方案中，我们找到p的临近节点ul，然后选取ul的最后一个li后代

### 解决办法3

```
ul:first-of-type li:nth-last-child(1) {
  font-weight: bold;
}
```
这种方案先取到第一个ul，然后利用`nth-last-child`取到第二个li元素（因为它在最后，如果有两个以上元素取第二个就不能用这种方法）
