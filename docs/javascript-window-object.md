# 了解 JavaScript 窗口对象

> 原文：<https://www.sitepoint.com/javascript-window-object/>

**每个 JavaScript 环境都有一个** **全局对象**。在全局范围内创建的任何变量实际上都是这个对象的属性，任何函数都是它的方法。在浏览器环境中，全局对象是`window`对象，它代表包含网页的浏览器窗口。

在本文中，我们将介绍窗口对象的一些重要用途:

*   [浏览器对象模型](#the-browser-object-model)
*   [用 JavaScript 获取浏览器信息](#browser-information)
*   [获取和使用浏览器历史记录](#the-browser-history)
*   [创建和控制窗口](#controlling-windows)
*   [获取屏幕尺寸和显示细节](#screen-information)

本帖改编自我的著名课程: [JavaScript:新手到忍者](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition/read/1/)。

## 浏览器对象模型

浏览器对象模型(或简称为 BOM)是包含浏览器和计算机屏幕信息的属性和方法的集合。例如，我们可以找出哪个浏览器正在被用来查看一个页面(虽然，这种方法是不可靠的)。我们还可以找出它被浏览的屏幕尺寸，以及在当前页面之前访问过哪些页面。如果你想惹恼你的用户，它也可以用于创建弹出窗口这种相当可疑的做法。

BOM 没有官方标准，尽管有许多属性和方法被所有主流浏览器支持，形成了一种事实上的标准。这些属性和方法通过`window`对象可用。每个浏览器窗口、选项卡、弹出窗口、框架和 iframe 都有一个`window`对象。

#### BOM 仅在浏览器环境中有意义

记住 JavaScript 可以在不同的环境中运行。BOM 仅在浏览器环境中有意义。这意味着其他环境(比如 Node.js)可能不会有`window`对象，尽管它们仍然会有一个全局对象；例如，Node.js 有一个名为`global`的对象。

如果不知道全局对象的名称，也可以在全局范围内使用关键字`this`引用。下面的代码提供了一种将变量`global`赋给全局对象的快速方法:

```
// from within the global scope
const global = this;
```

### 走向全球

全局变量是不使用`const`、`let`或`var`关键字创建的变量。在程序的所有部分都可以访问全局变量。

全局变量是全局对象的实际属性。在浏览器环境中，全局对象是`window`对象。这意味着创建的任何全局变量实际上都是`window`对象的属性，如下例所示:

```
x = 6;  // global variable created
>> 6
window.x // same variable can be accessed as a property of the window object
>> 6
// both variables are exactly the samewindow.x === x;
>> true
```

一般情况下，应该不用`window`对象引用全局变量；输入更少，你的代码在不同的环境之间更容易移植。一个例外是，如果你需要检查是否已经定义了一个全局变量。例如，如果没有定义`x`，下面的代码将抛出一个 ReferenceError:

```
if (x) {
    // do something
}
```

然而，如果变量作为`window`对象的属性被访问，那么代码仍然会工作，因为`window.x`将简单地返回`false`，这意味着代码块不会被求值:

```
if (window.x) {
    // do something
}
```

我们已经见过的一些函数，比如`parseInt()`和`isNaN()`，实际上是全局对象的方法，在浏览器环境中，它们是`window`对象的方法:

像变量一样，习惯上省略通过`window`对象访问它们。

### 对话

在浏览器中有三个产生对话框的函数:`alert()`、`confirm()`和`prompt()`。这些不是 ECMAScript 标准的一部分，尽管所有主流浏览器都支持它们作为`window`对象的方法。

方法将暂停程序的执行，并在对话框中显示一条消息。该消息作为方法的参数提供，并且总是返回`undefined`:

```
window.alert('Hello');
>> undefined
```

![Alert Dialog](img/5156107ca55590bf9bb8db9982d30717.png)

Alert Dialog

`window.confirm()`方法将停止程序的执行，并显示一个确认对话框，显示作为参数提供的消息，并给出确定或取消的选项。如果用户点击 OK，它返回布尔值`true`，如果用户点击 Cancel，则返回`false`:

```
window.confirm('Do you wish to continue?');
>> undefined
```

![Confirm Dialog](img/d38fddba9416c402f3cb847d45acccef.png)

Confirm dialog

`window.prompt()`方法将停止程序的执行。它显示一个对话框，该对话框显示一条作为参数提供的消息，以及一个允许用户输入文本的输入字段。当用户单击“确定”时，该文本将作为字符串返回。如果用户点击取消，则返回`null`:

```
window.prompt('Please enter your name:');
```

![Prompt Dialog](img/10ea3354514c4703bb16895a90601339.png)

Prompt dialog

#### 小心使用窗口对话框

值得再次重申的是，这些方法会阻止程序的执行。这意味着一切都将在方法被调用时停止处理，直到用户点击 *OK* 或 *Cancel* 。如果程序需要同时处理其他事情或者程序正在等待回调函数，这会导致问题。

在某些情况下，这种功能可以作为一种优势，例如，一个`window.confirm()`对话框可以作为最后的检查，看看用户是否想要删除一个资源。当用户决定做什么时，这将阻止程序继续删除资源。

同样值得记住的是，大多数浏览器允许用户禁止任何对话框重复出现，这意味着它们不是一个可以依赖的特性。

## 浏览器信息

`window`对象有许多属性和方法来提供关于用户浏览器的信息。

### 用 Navigator 对象获取浏览器信息

`window`对象有一个`navigator`属性，返回对`Navigator`对象的引用。`Navigator`对象包含关于正在使用的浏览器的信息。它的`userAgent`属性将返回关于正在使用的浏览器和操作系统的信息。例如，如果我运行以下代码行，它显示我在 Mac OS 上使用的是 Safari 版本 10:

```
window.navigator.userAgent
>>"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/602.4.8 (KHTML, like Gecko) Version/10.0.3 Safari/602.4.8"
```

但是不要依赖这些信息，因为用户可以修改这些信息来伪装成不同的浏览器。也很难理解返回的字符串，因为所有浏览器在某种程度上都伪装成其他浏览器。例如，出于传统 Netscape 兼容性的原因，每个浏览器都会在其`userAgent`属性中包含字符串“Mozilla”。`userAgent`属性在官方规范中已经被弃用，但是它在所有主流浏览器中仍然受到很好的支持。

### 获取 URL 详细信息:路径、协议、端口等。

属性是一个包含当前页面 URL 信息的对象。它包含许多属性，这些属性提供了有关 URL 的不同片段的信息。

`href`属性以字符串形式返回完整的 URL:

```
window.location.href
>> "https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition/"
```

该属性(以及本节中的大多数其他属性)是一个读/写属性，这意味着它也可以通过赋值来更改。如果完成此操作，将使用新的属性重新加载页面。例如，在浏览器控制台中输入以下行会将页面重定向到 SitePoint 主页:

```
window.location.href = 'https://www.sitepoint.com/'
>> "https://www.sitepoint.com/"
```

`protocol`属性返回描述所用协议的字符串(如`http`、`https`、`pop2`、`ftp`等)。).注意结尾有一个冒号(`:`):

```
window.location.protocol
>> "https:"
```

`host`属性返回一个描述当前 URL 的域的字符串*和*端口号(如果使用默认端口 80，这通常被省略):

```
window.location.host
>> "www.sitepoint.com"
```

`hostname`属性返回描述当前 URL 的域的字符串:

```
window.location.hostname
>> "www.sitepoint.com"
```

`port`属性返回一个描述端口号的字符串，尽管如果 URL 中没有明确说明端口，它将返回一个空字符串:

```
window.location.port
>> ""
```

`pathname`属性返回域后面的路径字符串:

```
window.location.pathname
>> "/premium/books/javascript-novice-to-ninja-2nd-edition/"
```

属性返回一个以“？”开头的字符串后跟查询字符串参数。如果没有查询字符串参数，它将返回一个空字符串。这是我在 SitePoint 上搜索“达伦·琼斯”时得到的结果:

```
window.location.search
>> "?q=darren%20jones&firstSearch=true"
```

`hash`属性返回一个以“#”开头的字符串，后跟片段标识符。如果没有片段标识符，则返回空字符串:

```
window.location.hash
>> ""
```

属性返回一个字符串，该字符串显示当前页面来自的协议和域。此属性是只读的，因此不能更改:

```
window.location.origin
>> "https://www.sitepoint.com"
```

`window.location`对象也有以下方法:

*   方法可以用来强制重新加载当前页面。如果给它一个参数`true`，它将迫使浏览器从服务器重新加载页面，而不是使用缓存的页面。
*   `assign()`方法可用于从作为参数提供的 URL 加载另一个资源，例如:

```
window.location.assign('https://www.sitepoint.com/')
```

*   `replace()`方法与`assign()`方法几乎相同，只是当前页面不会存储在会话历史中，因此用户无法使用 back 按钮导航回当前页面。
*   `toString()`方法返回一个包含整个 URL 的字符串:

```
`window.location.toString();
>> "https://www.sitepoint.com/javascript/"`
```

 `## 浏览器历史

属性可以用来访问当前浏览器会话中任何以前访问过的页面的信息。避免与新的 HTML5 历史 API 混淆。(详情见《http://www.sitepoint.com/javascript-history-pushstate/邮报》)。)

属性显示在到达当前页面之前已经访问了多少个页面。

`window.history.go()`方法可用于转到特定页面，其中 0 表示当前页面:

```
window.history.go(1); // goes forward 1 page
window.history.go(0); // reloads the current page
window.history.go(-1); // goes back 1 page
```

还有`window.history.forward()`和`window.history.back()`方法可以用来分别向前和向后导航一页，就像使用浏览器的前进和后退按钮一样。

## 控制窗口

使用`window.open()`方法可以打开一个新窗口。它把要打开的页面的 URL 作为第一个参数，窗口标题作为第二个参数，属性列表作为第三个参数。这也可以赋给一个变量，这样以后就可以在代码中引用该窗口:

```
const popup = window.open('https://sitepoint.com','SitePoint','width=700,height=700,resizable=yes');
```

![A popup window](img/b4fb10be4937c1e0db47bd41eba6a66c.png)

A popup window

`close()`方法可以用来关闭一个窗口，假设你有一个对它的引用:

```
popup.close();
```

也可以使用`window.moveTo()`方法移动窗口。这需要两个参数，即窗口要移动到的屏幕的 X 和 Y 坐标:

```
window.moveTo(0,0); 
// will move the window to the top-left corner of the screen
```

您可以使用`window.resizeTo()`方法调整窗口大小。这需要两个参数来指定调整后的窗口尺寸的宽度和高度:

```
window.resizeTo(600,400);
```

#### 烦人的弹出窗口

这些方法在很大程度上给了 JavaScript 一个坏名声，因为它们被用来创建通常包含侵入性广告的烦人的弹出窗口。从可用性的角度来看，调整或移动用户的窗口也不是一个好主意。

许多浏览器会阻止弹出窗口，并且在某些情况下不允许调用其中的一些方法。例如，如果打开了多个标签，您就不能调整窗口的大小。您也不能移动不是使用`window.open()`创建的窗口或调整其大小。

使用这些方法中的任何一种都是明智的，这种情况很少见，所以在使用它们之前要仔细考虑。几乎总会有更好的选择，忍者程序员会努力找到它。

## 屏幕信息

`window.screen`对象包含关于浏览器显示屏幕的信息。您可以分别使用`height`和`width`属性找出屏幕的高度和宽度，以像素为单位:

```
window.screen.height
>> 1024
window.screen.width
>> 1280
```

`availHeight`和`availWidth`可用于查找屏幕的高度和宽度，不包括任何操作系统菜单:

```
window.screen.availWidth
>> 1280
window.screen.availHeight
>> 995
```

`colorDepth`属性可用于查找用户显示器的颜色位深度，尽管除了收集用户统计数据之外，很少有其他使用案例:

```
window.screen.colorDepth;
>> 24
```

#### 在手机上更有用

屏幕对象在移动设备上有更多的用途。它还允许你做一些事情，如关闭设备的屏幕，检测其方向的变化或将其锁定在特定的方向。

#### 小心使用

前一节中介绍的许多方法和属性在过去被滥用于可疑的活动，例如用户代理嗅探，或者检测屏幕尺寸以决定是否显示某些元素。这些实践(谢天谢地)现在已经被更好的实践所取代，比如媒体查询和特征检测，这将在下一章中讨论。

## 文档对象

每个`window`对象包含一个`document`对象。该对象具有处理已加载到窗口中的页面的属性和方法。在第 6 章中，我们介绍了文档对象模型以及用于操作页面上项目的属性和方法。`document`对象包含其他一些值得一看的方法。

### `document.write()`

`write()`方法只是将一串文本写入页面。如果页面已经加载，它将完全替换当前文档:

```
document.write('Hello, world!');
```

这将用字符串`Hello, world!`替换整个文档。字符串中可以包含 HTML，这将成为 DOM 树的一部分。例如，下面这段代码将创建一个`<h1>`标签节点和一个子文本节点:

```
document.write('<h1>Hello, world!</h1>');
```

`document.write()`方法也可以在文档中的`<script>`标记内使用，将字符串注入到标记中。这不会覆盖页面上的其余 HTML。以下示例将文本`"Hello, world!"`放在`<h1>`标签内，页面的其余部分将正常显示:

```
<h1>
    <script>document.write("Hello, world!")</script>
</h1>
```

使用`document.write()`非常令人不快，因为只有在 HTML 文档中混合使用 JavaScript 才能真正使用它。仍然有一些非常罕见的合法使用，但是忍者程序员几乎不需要使用它。

## 分享这篇文章`