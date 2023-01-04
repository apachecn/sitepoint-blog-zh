# 不要误用 REL 作为自定义属性

> 原文：<https://www.sitepoint.com/rel-not-a-custom-attribute/>

![don't misuse rel!](img/abde466261847728992ce746de78b27d.png)在很多情况下，向 HTML 标签添加自定义数据是很有用的。在我最近的系列文章[如何构建一个自动扩展的 Textarea jQuery 插件](https://www.sitepoint.com/build-auto-expanding-textarea-2/)中，我为一个`textarea`类属性指定了一个名称“expand”来触发 JavaScript 功能。随着我们(可能)向语义网发展，微格式[和](http://microformats.org/) [RDFa](http://rdfa.info/) 都向标签属性添加元数据，以增强网页中信息的*含义*。

浏览器可能不显示自定义数据，但作为编程挂钩很方便。大多数开发人员都知道 HTML4 中不存在自定义属性…

```
 <!-- this is not valid -->
<div myattribute="mydata"></div> 
```

浏览器通常不会抱怨，但是你不能绝对肯定属性将如何被处理。有些人会将它添加到 DOM 中，有些人可能会完全忽略它。您的页面也将无法通过验证，这使得发现真正的错误更加困难。

XHTML 允许您指定自定义标签和属性。然而，这个过程包括开发您自己的 DTD(文档类型定义),当您只需要一个简单的 JavaScript 小部件的少量数据时，这是多余的。

不幸的是，有一种常见的误解，认为‘rel’属性是未使用的，可以在任何需要定制数据的情况下使用。不要做这样的假设。在 HTML4.01 和 XHTML1.0 中，‘rel’只能作为`a`和`link`标签的属性。如果您将代码分配到其他地方，它将无效。

此外，“rel”描述了当前文档和 href 属性中指定的文档或锚点之间的关系。这种关系可以是以下一种或多种类型:替换、样式表、开始、下一页、上一页、目录、索引、术语表、版权、章、节、小节、附录、帮助和书签。在最好的情况下，您的自定义数据将被忽略。最坏的情况是，HTML 的未来版本可能会将数据定义为指定的链接类型，并以不希望的方式处理属性。

HTML5 规范略有不同。它还允许在`area`标签上使用 rel 属性，并定义链接类型:替换、归档、作者、书签、外部、提要、第一个、帮助、图标、索引、最后一个、许可、下一个、nofollow、noreferrer、pingback、预取、prev、搜索、样式表、侧栏、标签和 up。

然而，HTML5 也为我们提供了自定义数据属性，它可以是以字符串“data-”开头的任何名称，例如

```
 <!-- HTML5 custom data attribute -->
<span data-speed-mps="186282">speed of light</span> 
```

不幸的是，HTML5 规范是不完整的，浏览器支持充其量是不完整的。

那么，在 HTML5 成为现实之前，您应该为自定义元数据使用什么标签或属性呢？答案是:*你能找到的最合适的*。例如，您可能想要为出现在 JavaScript 支持的工具提示中的图像定义描述——“longdesc”属性可能是理想的。

或者，如果没有合适的属性，请使用“class”。大多数 HTML 元素支持 class 属性，多个名称可以用空白字符分隔。

## 分享这篇文章