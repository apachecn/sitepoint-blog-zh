# Google Closure:如何不写 JavaScript

> 原文：<https://www.sitepoint.com/google-closure-how-not-to-write-javascript/>

上周在珀斯举行的网络边缘会议(T1)上，我见到了 [Dmitry Baranovskiy](http://dmitry.baranovskiy.com/) ，他是[raphal](http://raphaeljs.com/)和[graph al](http://g.raphaeljs.com/)JavaScript 库的创建者。也许这些库做的最重要的事情是使复杂的矢量图形在 Internet Explorer 中成为可能，而在 Internet Explorer 中 JavaScript 性能相对较差。因此，Dmitry 对像他在谷歌刚刚发布的 [Closure Library](http://googlecode.blogspot.com/2009/11/introducing-closure-tools.html) 中发现的代码这样写得很差的 JavaScript 没有什么耐心。

在会议上发表了关于如何编写自己的 JavaScript 库的演讲后，Dmitry 在第二天早餐时分享了他对新库的想法。“这正是这个世界所需要的——又一个糟糕的 JavaScript 库，”他说。当我问他是什么让它变得“糟糕”时，他详细解释了。“这是一个由 Java 开发人员编写的 JavaScript 库，他们显然没有*懂* JavaScript。”

那天剩下的时间里，对于任何愿意听的人，Dmitry 列举了一个又一个他在研究闭包时发现的可怕代码的例子。他告诉我，他最大的恐惧是人们会从真正优秀的 JavaScript 库，如 [jQuery](http://jquery.com/) 转向基于 Google 名称的闭包。

“我和你做笔交易，”我告诉他。"给我发一些这种可怕代码的例子，我会把它发布在 SitePoint 上."

## 慢速循环

从 [array.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/array/array.js?r=2) ，第 63 行:

```
for (var i = fromIndex; i < arr.length; i++) {
```

这个`for`循环在每次循环中查找数组的`.length`属性(`arr`)。只需在循环开始时设置一个变量来存储这个数字，就可以使循环运行得更快:

```
for (var i = fromIndex, ii = arr.length; i < ii; i++) {
```

谷歌的开发人员似乎在同一文件中发现了这个窍门。从 [array.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/array/array.js?r=2) ，第 153 行:

```
var l = arr.length;  // must be fixed during loop... see docs
⋮
for (var i = l - 1; i >= 0; --i) {
```

这个循环更好，因为它避免了每次循环时都进行属性查找，但是这个特定的`for`循环非常简单，可以进一步简化为`while`循环，这样运行起来会更快:

```
var i = arr.length;
⋮
while (i--) {
```

但是并不是所有 Closure Library 的性能问题都是由于循环优化不佳造成的。从 [dom.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/dom/dom.js?r=2) ，第 797 行:

```
switch (node.tagName) {
  case goog.dom.TagName.APPLET:
  case goog.dom.TagName.AREA:
  case goog.dom.TagName.BR:
  case goog.dom.TagName.COL:
  case goog.dom.TagName.FRAME:
  case goog.dom.TagName.HR:
  case goog.dom.TagName.IMG:
  case goog.dom.TagName.INPUT:
  case goog.dom.TagName.IFRAME:
  case goog.dom.TagName.ISINDEX:
  case goog.dom.TagName.LINK:
  case goog.dom.TagName.NOFRAMES:
  case goog.dom.TagName.NOSCRIPT:
  case goog.dom.TagName.META:
  case goog.dom.TagName.OBJECT:
  case goog.dom.TagName.PARAM:
  case goog.dom.TagName.SCRIPT:
  case goog.dom.TagName.STYLE:
    return false;
}
return true;
```

这种代码实际上在 Java 中很常见，在那里会运行得很好。然而，在 JavaScript 中，每当开发人员检查特定的 HTML 元素是否允许有子元素时，这个`switch`语句就会像狗一样执行。

有经验的 JavaScript 开发人员知道，创建一个对象来封装这个逻辑要快得多:

```
var takesChildren = {}
takesChildren[goog.dom.TagName.APPLET] = 1;
takesChildren[goog.dom.TagName.AREA] = 1;
⋮
```

设置好对象后，检查标签是否接受子标签的功能可以运行得更快:

```
return !takesChildren[node.tagName];
```

可以使用`hasOwnProperty`对该代码进行进一步的防弹处理，防止外部干扰(见下文对此的完整解释)。

```
return !takesChildren.hasOwnProperty(node.tagName);
```

如果说我们对谷歌有什么期望的话，那就是专注于性能。见鬼，Google 发布了自己的浏览器 Google Chrome，主要是为了将 JavaScript 性能提升到一个新的水平！

看到这样的代码，人们不得不怀疑谷歌是否可以通过教其工程师编写更好的 JavaScript 代码来实现同样的事情。

## 在漏水的船上呆了六个月

暗示谷歌忽视了建筑物关闭过程中的表现是不公平的。事实上，该库提供了一种通用方法来缓存运行缓慢的函数的结果，但对于给定的一组参数，这些函数总是返回相同的结果。来自 [memoize.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/memoize/memoize.js?r=2) ，第 39 行:

```
goog.memoize = function(f, opt_serializer) {
  var functionHash = goog.getHashCode(f);
  var serializer = opt_serializer || goog.memoize.simpleSerializer;

  return function() {
    // Maps the serialized list of args to the corresponding return value.
    var cache = this[goog.memoize.CACHE_PROPERTY_];
    if (!cache) {
      cache = this[goog.memoize.CACHE_PROPERTY_] = {};
    }
    var key = serializer(functionHash, arguments);
    if (!(key in cache)) {
      cache[key] = f.apply(this, arguments);
    }
    return cache[key];
  };
};
```

这是许多主流 JavaScript 库中使用的一个聪明的性能技巧；问题是，Google 没有提供任何限制缓存大小的方法！如果一个缓存的函数只被不同参数的小集合调用，这是没问题的，但是这通常是一个危险的假设。

用来缓存一个函数的结果，比如说，基于鼠标指针的坐标，这段代码的内存占用将会迅速增长到无法控制的程度，并使浏览器变慢。

用 Dmitry 的话说，“我不确定这种模式在 Java 里叫什么，但在 JavaScript 里叫‘内存泄漏’。”

## 真空中的代码

在他关于构建 JavaScript 库的演讲中，Dmitry 将 JavaScript 的全局范围比作公共厕所。“你不能不去那里，”他说。"但是当你这样做的时候，试着限制你与物体表面的接触."

对于一个可靠的通用 JavaScript 库来说，它不仅必须避免干扰任何其他可能与其一起运行的 JavaScript 代码，还必须保护自己免受其他不那么友好的脚本的影响。

从 [object.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/object/object.js?r=2) ，第 31 行:

```
goog.object.forEach = function(obj, f, opt_obj) {
  for (var key in obj) {
    f.call(opt_obj, obj[key], key, obj);
  }
};
```

`for`–`in`像这样的循环在 JavaScript 库中本来就很危险，因为你永远不知道页面中还会运行什么 JavaScript 代码，以及它会给 JavaScript 的标准`Object.prototype`增加什么。

`Object.prototype`是包含所有 JavaScript 对象共享的属性的 JavaScript 对象。向`Object.prototype`添加一个新函数，页面中运行的每个 JavaScript 对象都会添加这个函数——即使它是预先创建的！像 [Prototype](http://www.prototypejs.org/) 这样的早期 JavaScript 库在为`Object.prototype`添加各种便利特性方面做了大量工作。

不幸的是，与`Object.prototype`提供的内置属性不同，添加到`Object.prototype`的自定义属性将作为对象属性出现在页面的任何`for`–`in`循环中。

简而言之，闭包库不能与任何给`Object.prototype`添加特性的 JavaScript 代码共存。

谷歌本可以通过使用`hasOwnProperty`来检查`for`–`in`循环中的每一项，以确保它属于对象本身，从而使其代码更加健壮:

```
goog.object.forEach = function(obj, f, opt_obj) {
  for (var key in obj) {
    if (obj.hasOwnProperty(key)) {
      f.call(opt_obj, obj[key], key, obj);
    }
  }
};
```

这是另一个特别脆弱的闭包库。从 [base.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/base.js?r=2) ，第 677 行:

```
goog.isDef = function(val) {
 return val !== undefined;
};
```

这个函数检查一个特定的变量是否有定义的值。或者确实如此，除非第三方脚本将全局`undefined`变量设置为其他值。页面中任何地方的这一行代码都会导致闭包库崩溃:

```
var undefined = 5;
```

依赖全局变量`undefined`是 JavaScript 库作者的另一个新手错误。

您可能认为给`undefined`赋值的人应该得到他们所得到的，但是这种情况下的修复是微不足道的:只需声明一个局部`undefined`变量在函数中使用！

```
goog.isDef = function(val) {
  var undefined;
  return val !== undefined;
};
```

## 典型的困惑

对于来自其他语言的开发人员来说，JavaScript 最令人困惑的方面之一是它的数据类型系统。Closure Library 包含了大量的错误，进一步揭示了它的作者在 JavaScript 的细节方面缺乏丰富的经验。

从 [string.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/string/string.js?r=2) ，第 97 行:

```
// We cast to String in case an argument is a Function. …
var replacement = String(arguments[i]).replace(…);
```

这段代码使用`String`转换函数将`arguments[i]`转换为字符串对象。这可能是执行这种转换最慢的方式，尽管对于许多来自其他语言的开发人员来说这是最明显的。

更快的方法是将空字符串(`""`)添加到要转换的值中:

```
var replacement = (arguments[i] + "").replace(…);
```

这里还有一些与字符串相关的类型混淆。从 [base.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/base.js?r=2) ，第 742 行:

```
goog.isString = function(val) {
  return typeof val == 'string';
};
```

JavaScript 实际上以两种不同的方式表示文本字符串——原始字符串值和字符串对象:

```
var a = "I am a string!";
alert(typeof a); // Will output "string"
var b = new String("I am also a string!");
alert(typeof b); // Will output "object"
```

大多数时候字符串被有效地表示为原始值(上面的`a`)，但是要调用字符串上的任何内置方法(例如`toLowerCase`)，必须首先将其转换为字符串对象(上面的`b`)。JavaScript 会根据需要自动在这两种表示形式之间来回转换字符串。这个特性被称为“自动装箱”，并且出现在许多其他语言中。

不幸的是，对于精通 Java 的 Google 开发人员来说，Java 只将字符串表示为对象。这是我对为什么 Closure Library 忽略了 JavaScript 中第二种类型的字符串的最佳猜测:

```
var b = new String("I am also a string!");
alert(goog.isString(b)); // Will output FALSE
```

这是另一个受 Java 启发的类型混淆的例子。从 [color.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/color/color.js?r=2) ，第 633 行:

```
return [
  Math.round(factor * rgb1[0] + (1.0 - factor) * rgb2[0]),
  Math.round(factor * rgb1[1] + (1.0 - factor) * rgb2[1]),
  Math.round(factor * rgb1[2] + (1.0 - factor) * rgb2[2])
];
```

这些事情很能说明问题。像 Java 这样的语言表示整数(`1`)不同于浮点数(`1.0`)。然而，在 JavaScript 中，数字就是数字。`(1 - factor)`本来也可以。

在 [fx.js](http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/fx/fx.js?r=2) 的第 465 行可以看到另一个带有 Java 味道的 JavaScript 代码示例:

```
goog.fx.Animation.prototype.updateCoords_ = function(t) {
  this.coords = new Array(this.startPoint.length);
  for (var i = 0; i 
```

看到他们如何在第二行创建一个数组了吗？

```
this.coords = new Array(this.startPoint.length);
```

尽管在 Java 中这是必要的，但在 JavaScript 中提前指定数组的长度是完全没有意义的。创建一个新变量来用`var i = new Number(0);`而不是`var i = 0;`存储数字也同样有意义。

相反，你可以建立一个空数组，让它随着你的填充而增长。不仅代码更短，而且运行速度也更快:

```
this.coords = [];
```

哦，你是否发现了那个函数中另一个低效的`for`循环？

## API 设计

如果上面所有的低级代码质量挑剔都不能说服你，我倒要看看你是否能尝试使用一些 Google 内置到 Closure Library 中的 API。

例如，Closure 的[图形类](http://closure-library.googlecode.com/svn/trunk/closure/goog/docs/closure_goog_graphics_graphics.js.html)是围绕 [HTML5 canvas API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-canvas-element.html) 建模的，这就是你对 HTML 标准团体设计的 JavaScript API 的期望。简而言之，这是重复的、低效的，并且完全不适合编码。

作为[raphal](http://raphaeljs.com/)和[graph al](http://g.raphaeljs.com/)的作者，Dmitry 拥有丰富的设计可用 JavaScript APIs 的经验。如果你想了解 canvas API(以及 Closure 的图形 API)的全部恐怖之处，可以看看来自 [Dmitry 的 Web Directions South 2009 talk](http://www.webdirections.org/resources/dmitry-baranovskiy-canvas/) 关于这个主题的音频和幻灯片。

## 谷歌对代码质量的责任

至此，我希望您相信 Closure Library 并不是 Web 所能提供的最好的 JavaScript 代码的光辉范例。如果你正在寻找，我可以推荐更多像 [jQuery](http://jquery.com/) 这样的成熟玩家吗？

但是你可能会想“那又怎么样？如果谷歌愿意，它可以发布垃圾代码——没有人强迫你使用它。”如果这是某个谷歌人以他或她自己的名字发布的个人项目，我会同意你的观点，但是谷歌已经认可了 Closure Library，给它打上了谷歌的商标。

事实是，开发者*将*切换到 Closure，因为它带有 Google 的名字，这才是真正的悲剧。不管喜欢与否，Google 在开发社区中是一个值得信任的名字，在决定 Closure 这样的库是否值得公开之前，它有责任为社区做一些功课。

## 分享这篇文章