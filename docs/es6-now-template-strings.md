# 目前的 ES6:模板字符串

> 原文：<https://www.sitepoint.com/es6-now-template-strings/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

ES6 是 JavaScript 的未来，它已经到来了。这是一个完善的规范，它带来了一门语言保持竞争力所需的许多特性。并不是 ES6 中的所有东西都适合你，在这个小系列的帖子中，我将展示一些非常方便并且已经可以使用的特性。

如果你看看我写的 JavaScript 代码，你会发现我总是用单引号而不是双引号来定义字符串。JavaScript 都可以，下面两个例子做了完全相同的事情:

```
 var animal = "cow";
var animal = 'cow'; 
```

我更喜欢单引号的原因是，首先，这样可以更容易地用正确引用的属性组装 HTML 字符串:

```
 // with single quotes, there's no need to
// escape the quotes around the class value
var but = '<button class="big">Save</button>';

// this is a syntax error:
var but = "<button class="big">Save</button>";

// this works:
var but = "<button class=\"big\">Save</button>"; 
```

现在唯一需要转义的时候是在 HTML 中使用单引号的时候，这应该是非常罕见的情况。我唯一能想到的就是内联 JavaScript 或者 CSS，这意味着你很有可能对你的标记做一些见不得人或者不顾一切的事情。即使在你的文本中，你可能最好不要使用单引号，而是使用更令人愉快的印刷字体。

当然，HTML 对于省略引号或者在属性周围使用单引号是足够宽容的，但是我更喜欢为人类创建可读的标记，而不是依赖解析器的宽容。我们让 HTML5 解析器变得宽容，是因为人们在过去编写了糟糕的标记，而不是作为继续这样做的借口。

在 document.write 的 DHTML 时代，我已经受够了在新的弹出窗口中的框架集内创建文档以及其他令人厌恶的事情，再也不想使用转义符了。有时，我们需要三个一组的，那甚至是在我们的编辑器中有颜色编码之前。真是一团糟。

## 字符串中的表达式替换？

我更喜欢单引号的另一个原因是，在我的时间里，我为性能非常重要的大型网站编写了大量的 PHP。在 PHP 中，单引号和双引号是有区别的。单引号中的字符串没有任何替换，双引号中的有。这意味着在 PHP 3 和 4 时代，使用单引号要快得多，因为解析器不必遍历字符串来替换值。这里有一个例子说明这意味着什么:

```
 <?php
  $animal = 'cow';
  $sound = 'moo';

  echo 'The animal is $animal and its sound is $sound';
  // => The animal is $animal and its sound is $sound

  echo "The animal is $animal and its sound is $sound";
  // => The animal is cow and its sound is moo
?> 
```

JavaScript 没有这种替换，这就是为什么我们必须连接字符串来达到相同的结果。这是非常笨拙的，因为你需要一直在引号中跳来跳去。

```
 var animal = 'cow';
var sound = 'moo';

alert('The animal is ' + animal + ' and its sound is ' + sound);
// => "The animal is cow and its sound is moo" 
```

## 多线混乱

这对于更长更复杂的字符串来说会变得非常混乱，尤其是当我们汇编大量 HTML 时。而且，很有可能你迟早会用你的林挺工具来抱怨行尾的+后面的尾随空格。这是基于 JavaScript 没有多行字符串的问题:

```
 // this doesn't work
var list = '<ul> 
<li>Buy Milk</li> 
<li>Be kind to Pandas</li> 
<li>Forget about Dre</li> 
</ul>'; 

// This does, but urgh… 
var list = '<ul>\
<li>Buy Milk</li>\ 
<li>Be kind to Pandas</li>\ 
<li>Forget about Dre</li>\ 
</ul>';

// This is the most common way, and urgh, too… 
var list = '<ul>' +
 ' <li>Buy Milk</li>' +
 ' <li>Be kind to Pandas</li>' +
 ' <li>Forget about Dre</li>' + 
 '</ul>'; 
```

## 客户端模板解决方案

为了解决 JavaScript 中字符串处理和连接的混乱，我们做了我们一直在做的事情——我们写了一个库。有许多 HTML 模板库，其中的 [Mustache.js](https://github.com/janl/mustache.js) 可能是开创性的一个。所有这些都遵循自己的-非标准化的-语法，并在这种思维框架下工作。这有点像说你用 markdown 写你的内容，然后意识到对“markdown”的意思有很多不同的想法。

## 输入模板字符串

随着 ES6 及其标准化的出现，我们现在可以高兴了，因为 JavaScript 现在有了一个处理字符串的新成员:[模板字符串](http://www.ecma-international.org/ecma-262/6.0/#sec-static-semantics-templatestrings)。当前浏览器对模板字符串的[支持令人鼓舞:Chrome 44+，Firefox 38+，微软 Edge](http://kangax.github.io/compat-table/es6/#template_strings) 和 Webkit 都在其中。很遗憾，Safari 不是，但它会实现的。

模板字符串的天才之处在于它使用了一个新的字符串分隔符:反斜杠(`)，它在 HTML 和普通文本中都没有使用。

使用这个，我们现在有了 JavaScript 中的字符串表达式替换:

```
 var animal = 'cow';
var sound = 'moo';

alert(`The animal is ${animal} and its sound is ${sound}`);
// => "The animal is cow and its sound is moo" 
```

`${}`构造可以接受任何返回值的 JavaScript 表达式，例如，您可以进行计算，或者访问对象的属性:

```
 var out = `ten times two totally is ${ 10 * 2 }`;
// => "ten times two totally is 20"

var animal = {
  name: 'cow',
  ilk: 'bovine',
  front: 'moo',
  back: 'milk',
}
alert(`
  The ${animal.name} is of the 
  ${animal.ilk} ilk, 
  one end is for the ${animal.front}, 
  the other for the ${animal.back}
`);
// => 
/*
  The cow is of the 
  bovine ilk, 
  one end is for the moo, 
  the other for the milk
*/ 
```

最后一个例子也向您展示了多行字符串不再是一个问题。

## 标记模板

您可以对模板字符串做的另一件事是在它们前面加上一个标记，这个标记是被调用的函数的名称，它将字符串作为参数获取。例如，您可以对 URL 的结果字符串进行编码，而不必总是求助于糟糕的名称`encodeURIComponent`。

```
 function urlify (str) {
  return encodeURIComponent(str);
}

urlify `http://beedogs.com`;
// => "http%3A%2F%2Fbeedogs.com"
urlify `woah$£$%£^$"`;
// => "woah%24%C2%A3%24%25%C2%A3%5E%24%22"

// nesting also works:

var str = `foo ${urlify `&&`} bar`;
// => "foo %26%26 bar" 
```

这是可行的，但是依赖于隐式的数组到字符串的强制。发送给函数的参数不是字符串，而是字符串和值的数组。如果按照我在这里展示的方式使用，为了方便起见，它被转换成一个字符串，但是正确的方法是直接访问数组成员。

## 从模板字符串中检索字符串和值

在 tag 函数中，你不仅可以得到完整的字符串，还可以得到它的各个部分。

```
 function tag (strings, values) {
  console.log(strings);
  console.log(values);
  console.log(strings[1]);
}
tag `you ${3+4} it`;
/* =>

Array [ "you ", " it" ]
7
it

*/ 
```

还有一个提供给你的原始字符串数组，这意味着你得到了字符串中的所有字符，包括控制字符。例如，您添加一个换行符\n。您将得到字符串中的双空格，但原始字符串中的\n 字符:

```
 function tag (strings, values) {
  console.log(strings);
  console.log(values);
  console.log(strings[1]);
  console.log(strings.raw[1]);
}

tag `you ${3+4} \nit`;
/* =>

Array [ "you ", "  it" ]
7

it
 \nit
*/ 
```

## 结论

模板字符串是 ES6 中可以立即使用的漂亮的小胜利之一。如果你必须支持旧的浏览器，你当然可以把你的 ES6 移植到 ES5 您可以使用类似于 [featuretests.io](http://featuretests.io/) 的库或使用以下代码对模板字符串支持进行特性测试:

```
 var templatestrings = false;
try {
  new Function( "`{2+2}`" );
  templatestrings = true;
} catch (err) {
  templatestrings = false;
} 

if (templatestrings) {
	// …
} 
```

关于模板字符串的更多文章:

*   [了解 ECMAScript 6:模板字符串](https://www.sitepoint.com/understanding-ecmascript-6-template-strings/)
*   [用 ES6 模板字符串获取文字](https://developers.google.com/web/updates/2015/01/ES6-Template-Strings?hl=en)
*   [ES6 深度:模板字符串](https://hacks.mozilla.org/2015/05/es6-in-depth-template-strings-2/)
*   [ECMAScript 6 中的新字符串特性](http://www.2ality.com/2015/01/es6-strings.html)
*   [了解 ES6:模板字符串](https://leanpub.com/understandinges6/read/#leanpub-auto-template-strings)
*   [使用 ES6 模板字符串的 HTML 模板](http://www.2ality.com/2015/01/template-strings-html.html)

## JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13423-DEV-sitepoint-article47) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13423-DEV-sitepoint-article47)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article47&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article47&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article47&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article47&utm_campaign=SitePoint)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development)

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=13423-DEV-sitepoint-article47)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=13423-DEV-sitepoint-article47)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com/) 和 [babylonJS](http://babylonjs.com/) 项目)
*   [托管网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations/?WT.mc_id=13423-DEV-sitepoint-article47)(来自律师奶爸和基里尔·赛克谢诺夫，包括[流形。JS](http://manifold.js.com/) 项目)

为网络平台提供更多免费的跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=13423-DEV-sitepoint-article47)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=13423-DEV-sitepoint-article47) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13423-DEV-sitepoint-article47)

## 分享这篇文章