# Opera 11.60 的新功能

> 原文：<https://www.sitepoint.com/opera-116-new-features/>

与我提到的某些浏览器供应商不同，Opera 在发布更新时会努力添加闪亮的新功能。11.60 版也不例外，尽管很少有用户会注意到这些变化，除非他们使用:

*   Opera 的内置电子邮件客户端——已经过重新设计，或者
*   地址栏——它有一个新的快速书签/快速拨号星形图标

除了各种错误修复，所有令人兴奋的增强都在 Presto 2.10 渲染引擎中。开发者:准备修补…

## 新的 HTML5 解析器

Opera 实现了 [W3C HTML 解析规范](http://dev.w3.org/html5/spec/parsing.html)。它为有效和无效的 HTML 定义了一组解析规则，以确保所有浏览器都生成相同的 DOM。这将提高网站渲染的兼容性，尽管我们需要等到所有的供应商都这样做。

## ECMAScript 5.1 支持

Opera 11.60 完全支持 [ECMAScript 5.1 规范](http://www.ecma-international.org/publications/standards/Ecma-262.htm)。

## 微数据 DOM API

可以使用 JavaScript 查询 itemprop 和 itemscope 等微数据属性。例如，假设一个页面包含使用[Schema.org 人员定义](http://schema.org/Person)定义的联系人详细信息:

```
 <div itemscope itemtype="http://schema.org/Person">
	<p itemprop="name">Craig Buckler</p>
	<p>URL: <a href="https://www.sitepoint.com/author/craig-buckler/" itemprop="url">https://www.sitepoint.com/author/craig-buckler/</a></p>
</div> 
```

我们可以使用 JavaScript 来查询这些数据:

```
 // fetch the first person in document
var person = document.getItems("http://schema.org/Person")[0];

// show first name defined
alert("This article was written by " + person.properties["name"][0].textContent); 
```

## CSS rem Units

CSS [rem](https://www.w3.org/TR/css3-values/#rem-unit) 单位计算相对于根而不是包含元素的字体大小。属性可以在 CSS 或 SVG 中使用。

## 径向梯度

Opera 现在提供[径向渐变](https://www.w3.org/TR/2011/WD-css3-images-20110217/#radial-gradients)和[重复径向渐变](https://www.w3.org/TR/css3-images/#repeating-gradients)来补充[线性渐变](https://www.w3.org/TR/2011/WD-css3-images-20110217/#linear-gradients)支持。语法:

```
-o-radial-gradient(position, size and shape, color stops);
```

例如:

```
-o-radial-gradient(50% 50%, 50px 50px, #000, #fff)
```

## CSS4 图像呈现属性

此属性指定应该对图像、背景、画布元素和边框图像使用哪些缩放算法，例如，清晰边缘、优化对比度、最近邻、优化速度、优化相等等。

## 更好的 HTML5

<audio>和<video>支持</video></audio>

已经实现了以下属性/特性:

*   **预载** —可设置为“无”(最小化服务器流量)、“元数据”(允许获取曲目列表、持续时间等)。)或“自动”(可以乐观地下载整个媒体资源)。
*   **缓冲** —媒体的时间范围
*   **可查找** —媒体可查找的时间范围
*   **静音** —静音音频输出

## 自定义协议和内容处理程序

您可能在链接中使用过“mailto:email@address.com”或“tel:01-234-567890”来启动默认的电子邮件客户端或电话应用程序。Opera 现在支持新的`navigator.registerProtocolHandler`和`navigator.registerContentHandler`对象，这允许您创建自己的协议和定义处理程序。更多信息，请参考这个 [DEV。戏曲篇](http://dev.opera.com/articles/view/html5-custom-protocol-and-content-handlers/)。

## DOM4 事件

像最近的 webkit 版本一样，Opera 11.60 允许您使用以下[DOM 4 API](https://www.w3.org/TR/domcore/):

1.  合成事件:从代码中触发一个 UI 事件，例如按下一个键，聚焦在一个输入字段上，将鼠标移动到一个元素上，等等。
2.  自定义事件:定义你自己的事件类型，例如当游戏角色被击中时的“死亡”事件。

## 推测解析

在正常情况下，浏览器会延迟下载和渲染，直到加载的脚本执行完毕。推测性解析继续加载图像和 CSS 文件等资源，为呈现做好准备—这可以提高脚本密集型页面的性能。

## 正常的 XML 失败

Opera 现在不会显示一个*“XML 解析失败”*错误，而是尝试将格式不正确的 XML (XHTML)文档呈现为 HTML——即使它们是以 application/xhtml+xml MIME 类型提供的。虽然这将修复许多渲染问题，但您可以在 Opera 的配置面板中关闭它。

对于那些感受到节日气氛的人来说更好的是，布鲁斯·劳森唱了一首关于这些变化的圣诞颂歌。

Opera 11.60 在[opera.com](http://www.opera.com/)可用于大多数平台。

## 分享这篇文章