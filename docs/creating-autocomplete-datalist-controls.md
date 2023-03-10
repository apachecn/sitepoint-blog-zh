# 创建自动完成数据列表控件

> 原文：<https://www.sitepoint.com/creating-autocomplete-datalist-controls/>

如果你在生活中见过相当多的网站，你肯定会注意到其中大多数都有一组重复出现的小部件。这些小部件包括搜索框、时事通讯小部件和文本框自动完成小部件。后者是一个广泛使用的组件，特别是当网站需要一个有几个可能值的字段，而且还需要允许创建一个全新的值时。这个组件被广泛采用，以至于许多 JavaScript 框架都有自己的自动完成小部件。

直到几年前，还没有一个原生的 HTML 元素来处理这种情况，开发人员已经以不同的方式实现了这个概念。HTML 拼图的这一块不再缺少了。今天，我们有一个名为`datalist`的 HTML 元素来服务于这个目的。在本文中，我们将发现它是什么以及如何使用它。

## 什么是`datalist`元素？

[`datalist`元素](https://www.w3.org/html/wg/drafts/html/master/forms.html#the-datalist-element) <q cite="https://www.w3.org/TR/html-markup/datalist.html">表示一组选项元素，包含其他控件的预定义选项。</q>因此，这个元素可以被看作是一组`option`的包装器，这些包装器表达了一个`input`可以假定的可能值，而不仅仅局限于这些值。默认情况下`datalist`及其子节点是隐藏的，所以你不会在网页中看到它们。事实上，`datalist`必须通过使用设置在其他元素上的`list`属性链接到另一个元素。该属性的值必须设置为要使用的`datalist`的 ID。

使用此元素的 HTML 代码的简单示例如下所示:

```
<input name="city" list="cities" />
<datalist id="cities">
   <option value="Naples" />
   <option value="London" />
   <option value="Berlin" />
   <option value="New York" />
   <option value="Frattamaggiore" />
</datalist>
```

上面的代码定义了一个`input`和一个包含几个`option`的`datalist`元素。如您所见，`datalist`以“城市”作为其 ID，`input`通过使用`list`属性(也以“城市”作为其值)链接到它。

下面显示了代码的一个现场演示，其中的[可以作为 JSFiddle](https://jsfiddle.net/s8a932s2/) 获得。

[https://jsfiddle.net/s8a932s2/embedded/](https://jsfiddle.net/s8a932s2/embedded/)

由于其性质，这个元素非常适合与 JavaScript 结合使用。例如，您可以对服务器执行 Ajax 请求，根据用户的输入检索相关的值。

下面的演示展示了一个例子，[作为 JSFiddle](https://jsfiddle.net/4gpyekg2/) 提供，其中`datalist`的`option`是动态生成的。

[https://jsfiddle.net/4gpyekg2/embedded/](https://jsfiddle.net/4gpyekg2/embedded/)

到目前为止，我们已经讨论了如何自动完成文本字段，但是这不是我们可以使用这个 HTML 元素的唯一情况。

### `datalist`和`<input type="color">`

前面的例子很好，但是您可以使用`datalist`做更多的事情。如果您想通过使用`<input type="color">`向您的用户建议一种颜色，该怎么办？在这种情况下，您可以编写以下代码:

```
<input type="color" list="colors" />
<datalist id="colors">
   <option value="#00000"/>
   <option value="#478912"/>
   <option value="#FFFFFF" />
   <option value="#33FF99" />
   <option value="#5AC6D9" />
   <option value="#573905" />
</datalist>
```

这个演示目前只受到 Chrome 37 和 Opera 24 的良好支持。Internet Explorer 11 将该字段显示为文本字段，而 Firefox 32 不显示建议的颜色。

下面显示了代码的一个现场演示，其中的[可以作为 JSFiddle](https://jsfiddle.net/7rhe5zuw/) 获得。

[https://jsfiddle.net/7rhe5zuw/embedded/](https://jsfiddle.net/7rhe5zuw/embedded/)

### `datalist`和`<input type="range">`

另一个使用示例是与`<input type="range">`元素结合使用:

```
<input type="range" value="0" min="0" max="100" list="numbers" />
<datalist id="numbers">
   <option value="20" /> 
   <option value="40" /> 
   <option value="60" /> 
   <option value="80" /> 
</datalist>
```

在这种情况下，在支持该演示的浏览器(Internet Explorer、Opera 和 Chrome)上，范围栏将有四个等间距的垂直符号，每个符号对应于`datalist`中定义的一个值。

下面显示了代码的一个现场演示，其中的[可以作为 JSFiddle](https://jsfiddle.net/weew16za/) 获得。

[https://jsfiddle.net/weew16za/embedded/](https://jsfiddle.net/weew16za/embedded/)

## 浏览器支持

[can use](http://caniuse.com/#feat=datalist)展示了`datalist`如何在桌面浏览器中得到很好的支持。事实上，旧版本的 Firefox、Chrome 和 Opera 以及 Internet Explorer 10+都支持它。这意味着您可以在您的项目中可靠地使用它。Safari 不支持，Mac 用户有点倒霉。

很少有移动浏览器实现了该元素。唯一支持它的浏览器是 Firefox 和 Chrome for mobile，以及最新版本的 Blackberry 浏览器。

### 多填充物

如果你想在不理解这个元素的浏览器中提供支持，你可以使用[相关下拉菜单](https://github.com/CSS-Tricks/Relevant-Dropdowns)或者 [jQuery HTML5 数据列表插件](https://github.com/miketaylr/jquery.datalist.js)。请记住，这些聚合填充仅在使用文本字段的情况下作为替换，这意味着您不能聚合填充范围或颜色。

## 结论

在本文中，我向您介绍了`datalist`元素，以及如何使用它来创建本机自动完成小部件。正如您在示例中看到的，它也可以用于颜色和范围等非文本字段。最后，浏览器之间的支持足够好，可以在产品中采用这个元素。

你知道这个元素吗？你用过吗？我们开始讨论吧。

## 分享这篇文章