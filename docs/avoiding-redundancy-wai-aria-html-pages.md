# 用 WAI-ARIA 避免 HTML 页面中的冗余

> 原文：<https://www.sitepoint.com/avoiding-redundancy-wai-aria-html-pages/>

自从我们开始在 HTML 代码中集成 ARIA 角色以来，生活变得简单了。ARIA 一直在提供额外的语义来帮助辅助技术(ATs ),并使开发人员能够为残疾人增强 web 应用程序的可用性。迄今为止，最基本的问题仍然存在 HTML 元素需要 ARIA 角色属性来公开它们的语义吗？

在本文中，我将讨论这个主题以及新的 HTML5 结构元素，这些元素具有竞争 ARIA 角色的默认隐式语义。

## 咏叹调基础和一般概念

WAI-ARIA(通常称为 ARIA)是一组可以添加到 HTML 元素中的属性。这些属性的目的很简单——通过 web 浏览器中的可访问性 API 向 at 传达角色、属性和状态语义。斯蒂芬的帖子[WAI-ARIA 简介](https://www.sitepoint.com/introduction-wai-aria/)对于那些不熟悉 ARIA 的人来说是必读书。

HTML 社区对 ARIA 的普遍看法是“如果 HTML 已经覆盖了你，就不要使用 ARIA 代码”。同样的事情可以说得更清楚一点:如果你的 HTML 元素已经实现了，但是还没有可访问性支持，那么使用 ARIA。

## 咏叹调角色对大多数元素的影响

在一些常见的情况下，HTML 元素的语义可以通过使用 ARIA 角色、属性或状态来公开。起初有点令人费解，这在 HTML 社区中被称为 HTML 元素的*默认隐式 ARIA 语义*。

然而，当用 HTML 编码时，最好在开始集成 ARIA 属性之前编写语义正确的 HTML(从而利用其原生语义)。

> ARIA 角色不会给大多数 HTML 元素的默认语义增加任何东西。

规则是保持简单——如果语义默认包含在 HTML 元素中，那么不要使用 ARIA。在不必要的地方集成 ARIA 会导致多余的代码。

## HTML4 需要 ARIA 角色吗？

正如可访问性专家 Steve Faulkner 所解释的那样，所有在 HTML4(以及更早的 HTML 版本)中定义的 HTML 元素都不需要添加 ARIA 角色来揭示它们的默认语义，因为它们已经被映射了。

事实上，在这种情况下使用 ARIA 角色和 HTML4 中定义的元素不会有什么不同。如果在基于 HTML4 的代码中使用了 ARIA 角色，这将需要你做额外的工作，由别人来审查你的代码。因此，如果可以避免的话，一般建议不要在 HTML 元素中添加 ARIA 角色。

## HTML5 中的新功能

根据 [HTML5 规范](https://www.w3.org/TR/html5/dom.html#aria-usage-note):

> 在大多数情况下，设置与默认隐式 aria 语义相匹配的 ARIA 角色和/或 aria-*属性是不必要的，也不建议这样做，因为浏览器已经设置了这些属性。

这意味着 HTML5 中定义的新特性具有大多数 web 浏览器公开的默认隐式 ARIA 语义。尽管如此，如果不先查找，就不能假定您使用的 HTML 元素已经映射到 ARIA。记住这一点，我建议您暂时添加 ARIA 角色，以保持安全——即使这意味着必须编写冗余代码。

## 咏叹调中的冗余

HTML5 中的交互元素是那些明确用于用户交互的元素(例如大多数表单元素或`button`元素)。向 HTML5 交互元素添加默认隐式 ARIA 角色不会对它们产生任何影响。这样做不会有不利的影响，但是，如前所述，不增加咏叹调角色不会对它造成伤害。交互式元素不需要 ARIA 角色，建议您不要添加它们，以节省开发时间。

交互式元素必须有可访问的名称。这意味着你必须给它的 accessibility API**accessible name**属性一些值。代码可以更好地解释这一点:

```
<label>title</label>
<input type="text">
```

上面的代码可以更恰当地写成:

```
<label for="title">title</label>
<input type="text" id="title">
```

在第二行代码中，提到了可见标签和可访问标签。包含`for`和`id`属性使得标签应用于输入变得很明显。

## 冗余的例子

让我们看一些使用 ARIA 时冗余的例子。请记住，下面的例子是你应该避免使用的**代码的例子。**

### 交互式元素的默认隐式角色

```
<button role="button">Submit</button>
```

在这种情况下，`role`是不必要的。`button`元素本身就足够了。

### ARIA 角色以及本机 HTML 对应物

```
<div hidden aria-hidden="true">
```

这个例子使用了 HTML 的`hidden`属性，所以不需要`aria-hidden`特性。

### ARIA 添加到长期实现的结构元素中

```
<h1 role="heading" aria-level="1">I am a Heading</h1>
```

在这种情况下，`role`和`aria-level`属性都是不必要的。

## 具有 HTML5 结构元素的 ARIA

HTML5 的出现带来了一组全新的结构元素和分段元素，它们具有映射到 ARIA 角色的默认隐式语义。

例如:

*   `aside`映射到`role=complementary`
*   `article`映射到`role=article`
*   `main`映射到`role=main`

这里需要注意的是，前面提到的一些元素只在特定条件下映射到 ARIA 角色。例如，`footer`映射到`role=contentinfo`，前提是它不在`article`或`section`元素中。类似地，`header`映射到`role=banner`，前提是它不在文章或节元素中。

从这些例子中可以明显看出，无论在哪里实现，浏览器都会自己公开默认的隐式语义。

### 浏览器支持

新添加到 HTML5 中的结构和分段元素处于一个很好的位置。大多数广泛使用的浏览器都实现了默认的隐式语义——或者在 Internet Explorer 的情况下，正在实现过程中。

有关 HTML5 中 ARIA 特性的浏览器实现的更多信息， [HTML5 Accessibility](http://www.html5accessibility.com/) 是帮助您入门的绝佳资源。

## 包装它

为了结束这篇文章，我想给你一个简短的总结:

*   如果特性是在 HTML5 建议中定义的，不要使用 ARIA 角色、属性或状态
*   许多 HTML5 元素都有默认的隐式 ARIA 语义。
*   除了 Internet Explorer，ARIA 支持在现代浏览器中是非常好的。

你对在 HTML 元素中加入 ARIA 属性有什么想法？如果我遗漏了什么，请在评论中留下你的建议。

## 分享这篇文章