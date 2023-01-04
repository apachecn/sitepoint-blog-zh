# 符合标准的世界文章中的新窗口链接

> 原文：<https://www.sitepoint.com/standards-compliant-world/>

**令世界各地的 Web 设计者懊恼的是，W3C 推荐的 HTML 4.0 Strict 和 XHTML 1.0 Strict 不再包含`<a>`标签的`target`属性。规范的过渡版本仍然包括它，但是根据定义，这些规范已经过时了。**

不管你个人对这种做法有什么感觉，这个属性最常见的应用——在新的浏览器窗口中用`target="_blank"`打开一个链接——在今天的互联网上仍然有用。因此，如果标准说我们不应该使用它，我们应该如何着手创建新窗口链接，同时遵循最新的网络标准？

##### 应用理想主义

Web 标准的编写考虑了很多理想。在许多情况下，既定的做法被“正确的事情”所抛弃。从严格标准中移除`target`属性所表达的理念之一是,( X)HTML 应该只关心浏览器窗口中显示的信息。

因此，一旦我们开始谈论打开新的浏览器窗口，理想主义的观点是，我们已经超越了(X)HTML 的责任，进入了客户端脚本(即 JavaScript)的世界。

但是在我们深入研究在新的浏览器窗口中打开链接所需的 JavaScript 代码之前，我们还有一个基本问题要解决。我们仍然需要一种方法来标记应该在新窗口中打开的链接，如果我们不能使用`target`属性来做到这一点，我们需要找到一些其他的方法——一种符合最新(X)HTML 标准背后的理想的方法。

##### 新的关系

HTML 4.0 规范去掉了`target`属性，但是增加了另一个属性:`rel`。该属性*旨在*指定包含链接的文档和链接目标之间的关系。规范为这个属性定义了一堆标准值(例如，`next`、`previous`、`chapter`、`section`)，其中大部分都与一个较大文档的小部分之间的关系有关。但是，规范允许开发人员出于特定的目的自由使用非标准值。

在 SitePoint 这里，我们为`rel`属性采用了一个自定义值来标记指向其他网站的链接。这些正是我们想要在新的浏览器窗口中打开的链接。对于这些链接，我们将`rel`属性设置为`external`。

**之前:**

`<a href="document.html" **target="_blank"**>external link</a>`

**之后:**

`<a href="document.html" **rel="external"**>external link</a>`

所以，现在我们已经以符合标准的方式标记了新窗口链接，我们需要编写 JavaScript 来使它们实际上在新窗口中打开。

##### 解构剧本

我们的脚本将在文档加载后运行，它的任务是找到文档中的所有超链接，并修改那些具有我们特殊的(但符合标准的！)`rel="external"`属性，以便它们在新窗口中打开。

听起来令人畏惧？别担心——代码不会太差。第一步是确保浏览器能够胜任任务:

`if (!document.getElementsByTagName) return;`

`getElementsByTagName`是现代浏览器支持的文档对象模型 1.0 (DOM1)标准的一种简便方法。由于一些仍然存在的旧浏览器，如 Netscape 4 和 Internet Explorer 4，不支持 DOM1，我们需要通过检查该功能的存在来清除它们。在这些浏览器上，我们只需返回并让外部链接在同一个浏览器窗口中打开——在大多数情况下，这不会造成很大的损失。

接下来，我们使用`getElementsByTagName`方法获取文档中所有`<a>`标签的列表:

`var anchors = document.getElementsByTagName("a");`

`anchors`将包含一个 JavaScript 数组。数组中的每个元素将代表文档中的一个`<a>`标签。

现在我们需要检查我们刚刚找到的所有的`<a>`标签，并修改那些代表新窗口链接的标签。您可以使用 JavaScript `for`循环依次处理每个标签:

`for (var i=0; i < anchors.length; i++) {
var anchor = anchors[i];`

对于`anchors`数组中的每个元素，我们在循环中创建一个名为`anchor`的变量来隔离我们当前关注的标签。

现在我们需要检查`anchor`是否代表一个新窗口链接。我相信你知道，`<a>`标签不仅仅是用来创建超链接的。有了一个`name`属性，你可以为链接创建一个指向页面中特定位置的目标。只有带有`href`属性的`<a>`标签才符合超链接的条件。因此，我们的代码必须检查`anchor`是否有一个`href`属性以及一个设置为`external`的`rel`属性。

`if (anchor.getAttribute("href") &&
anchor.getAttribute("rel") == "external")`

既然我们已经确认我们正在处理一个新窗口链接，我们可以将它的`target`属性设置为`"_blank"`:

`anchor.target = "_blank";`

这应该可以了！请继续阅读完整的代码，并了解为什么这个解决方案是可以接受的…

##### 一线之隔

作为一个有点完美主义的人，当我得知这个解决方案时，我的第一个冲动是问，“真的有区别吗？”是的，我经常在做研究的时候自言自语。

我的意思是，如果`<a>`标签的`target`属性被淘汰，那么我们用 JavaScript 而不是 HTML 来设置它真的有区别吗？当然，页面将根据 HTML 4.0 Strict 和 XHTML 1.0 Strict 文档类型定义进行验证，但是我们在技术上不是在作弊吗？

答案是否定的。文档对象模型(DOM)管理 JavaScript 代码可用的文档对象和属性，是一个完全独立于(X)HTML 的标准。还要考虑 2003 年 1 月发布的 DOM 2.0 标准(远在 XHTML 1.0 之后，更不用说 HTML 4.0 了)仍然包含这个属性。很明显，虽然这个属性将被淘汰出(X)HTML，但在可预见的未来，它将通过 DOM 供 JavaScript 使用。

许多其他符合标准的新窗口链接脚本建议使用`window.open()`在新窗口中加载文档。虽然这种方法表面上看起来工作得很好，但它的缺点是大多数浏览器不能在新页面的请求中正确地报告引用 URL，这可能是一个严重的问题，特别是在站点间链接中。

##### 完整的剧本

正如所承诺的，这是完整的脚本。注意最后一行，它将`externalLinks`函数分配给窗口的`onload`事件处理程序。这将在文档完成加载后触发该功能。

`function externalLinks() {
if (!document.getElementsByTagName) return;
var anchors = document.getElementsByTagName("a");
for (var i=0; i<anchors.length; i++) {
var anchor = anchors[i];
if (anchor.getAttribute("href") &&
anchor.getAttribute("rel") == "external")
anchor.target = "_blank";
}
}
window.onload = externalLinks;`

由于这是您希望在整个站点中部署的脚本类型，您应该将此代码复制到一个单独的文件中(例如`external.js`)，然后使用以下代码将其加载到站点的每个页面中，这些代码应该出现在每个文档的`<head>`标记中:

`<script type="text/javascript" src="/external.js">
</script>`

问题解决了！

## 分享这篇文章