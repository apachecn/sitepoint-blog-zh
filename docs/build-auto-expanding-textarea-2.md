# 如何构建自动扩展的 Textarea jQuery 插件，第 2 部分

> 原文：<https://www.sitepoint.com/build-auto-expanding-textarea-2/>

在第一部分的[中，我们发现了如何构建](https://www.sitepoint.com/build-auto-expanding-textarea-1/)[自动扩展的文本区域](https://blogs.sitepointstatic.com/examples/tech/textarea-expander/demo.html)，并整理了需求。在我们关注实际的 JavaScript 之前，我们应该确定我们的代码将如何在网页中使用。

我们的自动扩展`textarea`框将如何初始化？由于我们的解决方案将是一个 jQuery 插件，我们可以使用下面的 jQuery 调用为页面上的每个`textarea`实现自动扩展功能:

```
 $("textarea").TextAreaExpander(); 
```

这种说法有点粗糙，因为每个文本区域都会自动扩展，而且我们没有指定单独的高度限制。因此，我们可能需要每个页面都有多个语句，例如

```
 $("#textarea1").TextAreaExpander();
$("#textarea2").TextAreaExpander(50, 200); // box will size between 50 and 200 pixels
$("#textarea3").TextAreaExpander(90, 300); // box will size between 90 and 300 pixels 
```

虽然这是可行的，但是我们可以有几十个文本区域。我们的服务器端代码也可能根据应用程序状态生成不同的`textarea` HTML。维护 JavaScript 声明列表会很快变得令人厌倦，并且容易出现开发人员错误。

因此，让我们为自己和使用我们组件的其他开发人员简化一些事情。如果我们想让一个`textarea`自动展开，我们将为标签分配一个 class 属性“expand”。尽管 class 属性通常用于 CSS 样式，但它也可以包含其他值，并且在所有风格的 HTML 和 XHTML 中都可用。如果您的 textarea 已经应用了 CSS 类，也不用担心，可以添加任意数量的空格分隔值:

```
 <!-- expanding textarea -->
<textarea name="textarea1" rows="3" cols="60" class="mystyle expand"></textarea> 
```

为了将`textarea`高度限制在某个范围内，我们可以将最小值和最大值添加到“扩展”名称中，例如

```
 <!-- expanding textarea, but limited between 50 and 200px -->
<textarea name="textarea2" rows="3" cols="60" class="expand50-200"></textarea> 
```

当我们的页面被加载时，JavaScript 将在 DOM 中搜索带有“expand”类的 textarea 节点，并自动对该元素应用自动扩展效果。这有几个优点:

1.  默认情况下，现有的`textarea`标签不会自动展开。
2.  我们的触发器是在`textarea` HTML 代码中指定的:它比单独的 JavaScript 声明列表更容易理解、阅读和维护。
3.  我们仍然可以在需要时使用 jQuery TextAreaExpander()方法，例如，如果在页面加载后将一个 textarea 添加到 DOM 中。
4.  渐进增强给我们一种温暖舒适的感觉！

现在我们已经确定了我们的 HTML 代码和自动扩展触发器，我们可以链接到页面底部的 JavaScript 源代码:

```
 <script type="text/javascript" src="jquery-1.3.2.min.js"></script>
<script type="text/javascript" src="jquery.textarea-expander.js"></script> 
```

这将加载最新的 jQuery 库(从[jQuery.com](http://jquery.com/)下载)和我们新的 TextAreaExpander 插件代码。

有用的资源:

*   [查看扩展文本区演示…](https://blogs.sitepointstatic.com/examples/tech/textarea-expander/demo.html)
*   [示例 HTML 页面](https://blogs.sitepointstatic.com/examples/tech/textarea-expander/index.html.txt)

准备好 JavaScript 编码了吗？倒满咖啡，为第三部分做准备……

*   [第三部分:JavaScript 插件代码](https://www.sitepoint.com/build-auto-expanding-textarea-3/)
*   [第 1 部分:构建一个自动扩展的文本区域](https://www.sitepoint.com/build-auto-expanding-textarea-1/)
*   [如何开发一个 jQuery 插件](https://www.sitepoint.com/how-to-develop-a-jquery-plugin/)

## 分享这篇文章