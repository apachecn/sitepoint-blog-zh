# 供应商特定的属性

> 原文：<https://www.sitepoint.com/vendor-specific-properties/>

供应商——浏览器制造商——可以自由地实现 CSS 规范的扩展，在大多数情况下，这些扩展是他们的浏览器专有的。他们这样做可能有很多原因，比如为用户添加新功能，或者为了实验和调试。不过，最常见的情况是，这些扩展用于发布和测试在 W3C 草案准备阶段开发的浏览器功能，这些功能尚未达到候选推荐状态——这些扩展允许这些新属性在成为标准 CSS 属性之前得到广泛测试。

为了适应特定于供应商的扩展的发布，[CSS 规范](https://www.w3.org/TR/CSS21/syndata.html#vendor-keywords)定义了供应商应该遵循的特定格式。

格式非常简单:以–(破折号)或 _(下划线)开头的关键字和属性名是为特定于供应商的扩展名保留的。因此，供应商应使用以下格式:

```
'-' + vendor specific identifier + '-' + meaningful name

'_' + vendor specific identifier + '-' + meaningful name
```

这种方法允许任何特定于供应商的扩展与任何未来(或当前)的 CSS 属性共存，而不会导致冲突，因为根据 W3C 规范，CSS 属性名称绝不会以破折号或下划线开头:

任何当前或未来级别的 CSS 都保证不会在属性或关键字中使用开头的破折号或下划线。因此，典型的 CSS 实现可能无法识别此类属性，并且可能会根据处理解析错误的规则忽略它们。然而，由于首字母破折号或下划线是语法的一部分，CSS2.1 实现者应该总是能够使用符合 CSS 的解析器，不管它们是否支持任何特定于供应商的扩展。”

已知存在许多扩展。它们的前缀如表 1 所示。

<caption>Table 1\. Vendor Extension Prefixes</caption>
| 前缀 | 组织 |
| --- | --- |
| `-ms-` | 微软 |
| `mso-` | 微软办公 |
| `-moz-` | Mozilla 基金会([基于 Gecko 的浏览器)](http://en.wikipedia.org/wiki/Gecko_(layout_engine)) |
| `-o-` | Opera 软件 |
| `-atsc-` | 高级电视标准委员会 |
| `-wap-` | WAP 论坛 |
| `-webkit-` | Safari(和其他[基于 WebKit 的浏览器)](http://trac.webkit.org/projects/webkit/wiki/Applications%20using%20WebKit) |
| `-khtml-` | Konqueror browser |

> 小心使用这些扩展！即使保证特定于供应商的扩展不会引起冲突(当然，除非两个供应商碰巧选择了相同的标识符)，也应该认识到，这些扩展也可能会因供应商的一时兴起而改变，因为它们不构成 CSS 规范的一部分，即使它们经常模仿现有或即将出现的 CSS 属性的行为。
> 
> 尽管这些扩展有时很有用，但仍然建议您避免使用它们，除非绝对必要。同样值得注意的是，和私有代码的情况一样，扩展不会通过 CSS 验证。

如果必须使用扩展，则应该使用与等效 CSS 属性密切相关的扩展(如 CSS1、cs2 或 CS3)，以便以后可以切换到标准属性，并在浏览器实现正确的规范时删除扩展。

记住这一点，让我们回到几年前，以不透明度属性为例，它是 CSS3(2003 年 5 月的候选推荐)的一部分，很少有浏览器真正支持它(不透明度在 Firefox 1.0、Opera 9 和 Safari 1.2 中实现)。因此，作者求助于使用特定于供应商的扩展来迎合当时缺乏 CSS3 不透明支持的情况。基于 Gecko 的浏览器(如 Mozilla)使用–moz-opacity 属性，Safari 1.1 使用-khtml-opacity 属性。Internet Explorer 和更高版本使用了非标准的筛选器属性。

将上述扩展结合在一起，下面的方法曾经(现在仍然)被广泛用于将不透明性应用于一系列浏览器:

```
.test{
  background: red;
  /* IE filter extension */
  filter: progid:DXImageTransform.Microsoft.Alpha(opacity=60);
  width:100%;                /* Required for IE filter */
  -moz-opacity: 0.6;         /* Mozilla extension */
  -khtml-opacity:0.6;        /* Konqueror extension (Safari 1.1)*/
  opacity: 0.6;              /* the correct CSS3 syntax */
}
```

在上面的代码片段中，Internet Explorer 将使用 filter 属性并忽略其他不透明度声明。不理解 CSS3 opacity 属性的旧版 Gecko 浏览器将尊重–moz-opacity 属性，而 Safari 1.1 将尊重-khtml-opacity 属性。最后，如果支持的话，CSS3 不透明度属性将被其他浏览器和浏览器版本所尊重。当然，不支持元素不透明的浏览器会忽略很多。

Internet Explorer filter 属性是微软对 CSS 的专有扩展，它显然没有遵循供应商特定扩展的正确命名规则。另一方面，Mozilla 和 Safari (-moz-opacity 和–khtml-opacity)属性确实遵循这些规则，尽管代码不会验证，但可以肯定这些属性相对来说不会发生冲突。

尽管 Firefox、Opera 和 Safari 等浏览器最终实现了 CSS3 opacity 属性，但上例中的样式规则仍然有效，确保了新旧样式之间的无缝过渡。(注意 Konqueror 3 . 5 . 4 及以上版本不支持 CSS3 不透明度。)

正如您所看到的，扩展是有用的，并且可以提供长寿的度量；然而，依赖扩展的可用性是不明智的。CSS3 属性在成为标准之前也有可能被改变。因此，正如 W3C 所说，“作者应该避免特定于供应商的扩展。”

由于特定于供应商的扩展的本质，它们没有被很好地记录以供公共使用，所以很难提供所有可用扩展的完整和准确的列表。以下链接可用作指南，但如果您想使用这些扩展，我们强烈建议您进行自己的研究:

*   [Internet Explorer CSS 属性:索引](http://msdn.microsoft.com/workshop/author/css/reference/attributes.asp)
*   CSS 参考:Mozilla 扩展
*   [Mozilla 中的 CSS3 列](http://developer.mozilla.org/en/docs/CSS3_Columns)
*   [Safari CSS 参考](http://tinyurl.com/26e9vl)
*   [Webkit CSS 样式](http://qooxdoo.org/documentation/general/webkit_css_styles)
*   [Opera 9 CSS 支持](http://www.opera.com/docs/specs/opera9/css/)
*   [Opera CSS 扩展](http://www.blooberry.com/indexdot/css/properties/extensions/operaextensions.htm)

正如我们已经提到的，我们不建议您在实际应用程序中使用这些扩展。将它们用于测试目的，以及试验尚未实现的 CSS 属性是很好的。这将为正确的 CSS 语法变得可用于一般用途做好准备，并对您有所启发。

虽然对所有属性的解释超出了本书的范围，但是我们将查看一些可能对您有用的属性，并研究一些您可能在其他地方使用的扩展。

## 分享这篇文章