# 如何在 HTML5 中有效地使用 ARIA

> 原文：<https://www.sitepoint.com/how-to-use-aria-effectively-with-html5/>

ARIA 代表“可访问的丰富互联网应用程序”,可以帮助听力或视力障碍者更容易访问您的网站。让我们看看，作为开发人员，我们如何让他们的生活更轻松。

使用 ARIA 的一种方法是将它添加到我们的 HTML 中。你可能已经熟悉 HTML 中的语义元素，如`nav`、`button`或`header`。很容易看出这些元素的用途。这些元素赋予页面内容更多的意义，我们可以在标记中结合使用这些元素和 ARIA。但是，在一起使用它们时，有一些事情需要记住。

## 阿里尔·罗斯

ARIA 角色像属性一样被添加到 HTML 标记中。它们定义了元素的类型，并提出了元素的用途。以下示例将元素标识为某种横幅:

```
<header role="banner">
```

以下示例通常放在包含元素中，表明其内容提供了有关包含元素中内容的一些信息:

```
<div role="contentinfo">
    This website was built by Georgie.
</div>
```

具有动态内容的警报应使用`role="alert"`:

```
<div role="alert">
    Please upgrade to the latest version of your browser for the best experience.
</div>
```

这是我个人最喜欢的一个，当一个元素只是为了表现的时候使用。如果你想象有人在使用屏幕阅读器，想想那些他们不希望被读出的元素。一个例子是可能包含视觉装饰的元素，或者是仅提供图像或背景颜色的空元素。

```
<a href="aria.html" role="presentation">
  <img src="aria-thumbnail.jpg" role="presentation" alt="Use ARIA effectively">
</a>
```

## ARIA 属性

ARIA 属性与角色略有不同。它们以同样的方式添加到标记中，但是有一系列 ARIA 属性可供使用。所有 ARIA 属性都以`aria-`为前缀。有两种类型的属性，*状态*和*属性*。

*   作为用户交互的结果，*状态*的值必然会改变。
*   属性的值不太可能改变。

作为状态的 ARIA 属性的一个例子是`aria-checked`。这用于显示模拟交互元素的元素的状态，例如复选框和单选按钮，但不是本地元素本身(例如用`div`和`span`标签构建的自定义 UI 元素)。

```
<span role="checkbox" 
      aria-checked="true"
      tabindex="0"
      id="simulatedcheckbox">
</span>
```

ARIA 属性的一个例子是`aria-label`。当表单元素的标签在页面上不可见时使用(可能是因为使其可见没有意义，或者是设计要求这样)。对于标签文本可见的情况，`aria-labelledby`是更适合使用的属性。

这可以通过如下所示的`figure`元素来完成。

```
<figure aria-labelledby="operahouse_1" role="group">
    <img src="operahousesteps.jpg" alt="The Sydney Opera House">
    <figcaption id="operahouse_1">We saw the opera <cite>Barber of Seville</cite> here!</figcaption>
</figure>
```

你可以在 W3C 网站上阅读更多关于[支持的状态和属性。](https://www.w3.org/TR/wai-aria/states_and_properties)

## 咏叹调的规则

在你变得过于热衷之前，请记住我们不希望在每个元素中添加 ARIA，原因有几个。

### 尽可能使用语义 HTML 元素

[默认隐式 ARIA 语义](https://www.w3.org/TR/html-aria/#sec-strong-native-semantics)指的是已经被浏览器应用到元素的语义。`nav`、`article`、`button`等元素分别有默认的`role="navigation"`、`role="article"`和`role="button"`的隐式 ARIA 语句。在语义 HTML 元素存在之前，拥有像`<div class="main-navigation" role="navigation">`这样的元素是很常见的。现在我们能够用`nav`代替`div`，但是我们不再需要添加`role="navigation"`，因为这已经是隐含的了。您可以参考这个 W3C 表[来检查某个元素是否需要 ARIA 属性。](https://www.w3.org/TR/html-aria/#docconformance)

### 您的元素只能有一个角色

一个元素不应该有多个 ARIA 角色。`role`的定义如下:

> 类型的主要指示器。这种语义关联允许工具以与用户对该类型的其他对象的期望一致的方式来呈现和支持与该对象的交互。

一个 HTML 元素不能有两个`role`。所有的角色都有这样或那样的语义，根据上面的定义，一个元素不能是两种类型的对象。你能有一个既是按钮又是标题的东西吗？不，非此即彼。选择最能描述您的元素功能的`role`。

### 不要更改本机语义

您不应该对已经具有语义的元素应用对比性的`role`，因为添加`role`会覆盖元素的原生语义。例如:

```
<footer role="button">
```

然而，ARIA use 的第二条规则建议，如果你必须使用嵌套的 HTML 元素。

```
<footer><button>Purchase this e-book</button></footer>
```

## 还有什么方法可以让你的标记更容易被访问？

### 尽可能多地使用语义元素

这需要实践，但是如果你想到你正在构建的东西的功能，它会给你一个好主意，你可以使用什么样的元素比`div`或`span`好得多。为了练习，你可以不断地向[询问有哪些元素](https://www.w3.org/TR/html-markup/elements.html)可供你使用并熟悉它们。

我最喜欢的一个例子是`blockquote`的用法，它经常被误用。还有其他类似的元素服务于特定的目的:

*   `q`–用于在段落文本中提供行内引用，例如某人的直接引用。
*   `cite`–用于引用文本中的创造性作品，例如提及一首诗。

下面是一个包含上述两个元素的示例:

```
<p>In <cite>The Love Song of J. Alfred Prufock</cite> by T.S. Eliot, the clinical imagery of the line <q>Like a patient etherized upon a table</q> suggests themes of loneliness.</p>
```

有很多 HTML 元素你可能没有考虑到，包括一些新的元素，所以确保你[已经看到了这些可能性！](https://www.w3.org/TR/html-markup/elements.html)

### `alt`属性

这是一个经常被遗忘的标记，它可以对标记的可访问性产生巨大的影响，尤其是对于屏幕阅读器。其实从 HTML2 开始就在[左右了，描述如下:](https://tools.ietf.org/html/rfc1866)

> 用于替代引用图像资源的文本，例如，由于处理约束或用户偏好。

*由于处理限制或用户偏好。*不考虑未加载的图像(“处理限制”)，视障用户实际上也没有偏好。默认情况下，他们只是很难像没有视觉障碍的人那样观看图像。虽然规范中没有提到术语可访问性，但它暗示了图像可能不会按要求加载，并且用户可以关闭图像加载。虽然我们生活在一个后者似乎难以置信的世界，但我们无法假设用户在另一端做什么。因此，我们需要为用户提供一种选择。

例如，人们经常在他们的狗在公园玩耍的照片上写下“狗”这样的文字。不幸的是，尽管包含了这段文字，但它并没有为视障人士描绘出一幅图画。以下内容更容易接受:

```
<img src="bobby.jpg" alt="My dog Bobby playing fetch in the park">
```

请注意，`alt`属性并不反映与`figcaption`元素相同的目的——`alt`的目的是为图像提供*可选文本*，而`figcaption`可以是`figure`的相关*标题*。使用相同的示例，这可能是合适的文本:

```
<figcaption>Isn’t Bobby cute?</figcaption>
```

## 一个使用语义 HTML 和 ARIA 的例子，考虑了可访问性

如果您查看我在本文前面包含的示例，您会发现我包含了以下内容:

*   对图像及其标题使用语义 HTML
*   适当地使用`cite`元素
*   提供适当的`alt`文本
*   使用了我已经提到的 ARIA 属性之一

```
<figure aria-labelledby="operahouse_1" role="group">
    <img src="operahousesteps.jpg" alt="The Sydney Opera House"/>
    <figcaption id="operahouse_1">We saw the opera <cite>Barber of Seville</cite> here!</figcaption>
</figure>
```

## 结论

当屏幕阅读器和其他辅助技术处理您的内容时，ARIA 角色和属性可以产生巨大的差异。随着辅助技术的使用越来越普遍，我们需要考虑将 ARIA 集成到我们的代码中，作为一种常规做法。

## 分享这篇文章