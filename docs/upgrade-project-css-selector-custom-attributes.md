# 使用 CSS 选择器和自定义属性升级您的项目

> 原文：<https://www.sitepoint.com/upgrade-project-css-selector-custom-attributes/>

*本文最初由 [TestProject](https://blog.testproject.io/2017/08/10/css-selector-custom-attributes/) 发布。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

Selenium WebDriver 的元素选择器是[自动化框架](https://blog.testproject.io/2017/03/26/test-automation-infrastructure-fundamentals/)的核心组件之一，也是与任何 web 应用程序交互的关键。在对[自动化元素选择器](https://blog.testproject.io/2017/02/09/inspect-web-elements-chrome-devtools/)的回顾中，我们将讨论各种策略，探索它们的能力，权衡它们的优缺点，并最终推荐[最佳选择器策略](https://blog.testproject.io/2017/08/10/css-selector-custom-attributes/#CustomAttributes)——用 CSS 选择器定制属性。

## 硒元素选择器

选择最佳的[元素选择器](https://blog.testproject.io/2017/02/09/inspect-web-elements-chrome-devtools/#ElementSelector)策略对于自动化工作的成功和易维护性至关重要。因此，在选择选择器时，您应该考虑易用性、多功能性、在线支持、文档和性能等方面。通过易于维护的自动化，对适当的选择器策略的充分考虑将在未来带来好处。

正如应该考虑元素选择器的技术方面一样，也应该考虑组织的文化。当在您的自动化中实现元素选择器时，开发人员和 QA 之间成熟的合作文化将会打开成功的大门。通过为软件开发生命周期的其他领域中的协作奠定基础，这不仅使组织受益于自动化工作。

所有代码示例都将出现在 [Python](https://www.python.org/) 和 [Selenium WebDriver](https://blog.testproject.io/2016/11/07/selenium-webdriver-3/) 命令中，但应该普遍适用于任何编程语言和框架。

### HTML 示例:

我将使用以下导航菜单的 HTML 片段作为每个部分的示例:

```
<div id="main-menu">
  <div class="menu"><a href="/home">Home</a></div>
  <div class="menu"><a href="/shop">Shop</a>
    <div class="submenu">
      <a href="/shop/gizmo">Gizmo</a>
      <a href="/shop/widget">Widget</a>
      <a href="/shop/sprocket">Sprocket</a>
    </div>
  </div>
</div>
```

## 错误:标签名称、链接文本、部分链接文本和名称

这些我不会花太多时间，因为都是用途有限的。它们通常不是在整个自动化框架中广泛采用的好选择。它们解决了其他元素选择器策略很容易涵盖的特定需求。仅当您有处理特殊情况的特殊需求时，才使用这些方法。即使这样，大多数特殊情况也没有特殊到可以使用这些。在没有其他选择器选项(如自定义标记或 id)的情况下，可以使用这些选项。

### 示例:

使用标记名，您可以选择与您提供的标记名相匹配的大量元素。这种方法用途有限，因为它只能在需要选择大量相同类型元素的情况下作为一种解决方案。以下示例将返回示例 HTML 中的所有 4 个 div 元素。

```
driver.find_elements(By.TAG_NAME, "div")
```

您可以选择下面这些例子的链接。如您所见，它们只能以锚定标签和这些锚定标签的文本为目标:

```
driver.find_elements(By.LINK_TEXT, "Home")
driver.find_elements(By.PARTIAL_LINK_TEXT, "Sprock")
```

最后，您可以通过 name 属性选择元素，但是正如您在示例 HTML 中看到的，没有带有 name 属性的标签。这在几乎任何应用程序中都是一个常见的问题，因为在每个 HTML 属性中添加 name 属性并不是一种常见的做法。如果主菜单元素具有如下名称属性:

```
<div id="main-menu" name="menu"></div>
```

您可以像这样选择它:

```
driver.find_elements(By.NAME, "menu")
```

如您所见，这些元素选择器策略的用途有限。接下来的方法都是更好的方法，因为它们更加通用和有效。

### 摘要:标签名称、链接文本、部分链接文本和名称

| **利弊** | **缺点** |
| Easy to use | 不通用 |
| 极其有限的使用 |
| 在某些情况下甚至可能不适用 |

## 好:XPath

XPath 是一种多功能的元素选择器策略。这也是我个人的喜好和喜爱。XPath 可以选择页面中的任何元素，而不管您是否有要使用的类和 id(尽管没有类或 id，它会变得难以维护，有时还很脆弱)。该选项特别通用，因为您可以选择[父元素](https://www.w3schools.com/jsref/prop_node_parentelement.asp)。XPath 还有许多内置函数，允许您定制元素选择。

然而，随着多功能性而来的是复杂性。与其他元素选择器策略相比，XPath 能够做这么多事情，因此您的学习曲线更加陡峭。这被很容易找到的在线文档所抵消。一个很好的资源是在 W3Schools.com 找到的 XPath 教程

还应该注意的是，在使用 XPath 时有一个权衡。虽然可以选择父元素并使用非常通用的内置函数，但是 XPath 在 Internet Explorer 中的性能很差。在选择元素选择器策略时，应该考虑这种权衡。如果您需要能够选择父元素，您需要考虑它对您在 Internet Explorer 中的[跨浏览器](https://blog.testproject.io/2017/02/09/cross-browser-testing-selenium-webdriver/)测试的影响。本质上，在 Internet Explorer 中运行自动化测试需要更长的时间。如果您的应用程序的用户群对 Internet Explorer 的使用率不高，这将是一个很好的选择，因为您可能会考虑在 Internet Explorer 中运行测试的频率比其他浏览器低。如果您的用户群大量使用 Internet Explorer，那么只有在其他更好的方法对您的组织不起作用时，您才应该考虑将 XPath 作为备用方法。

### 示例:

如果需要选择父元素，则必须选择 XPath。下面是如何做到这一点的:使用我们的示例，假设您想要基于锚元素之一定位父主菜单元素:

```
driver.find_elements(By.XPATH, "//a[id=menu]/../")
```

这个元素选择器将定位 id 等于“menu”的锚标记的第一个实例，然后用“/../，以父元素为目标。结果是，您将已经瞄准了主菜单元素。

### 摘要:XPath

| **利弊** | **缺点** |
| 可以选择父元素 | IE 表现不佳 |
| 高度通用 | 轻微的学习曲线 |
| 大量在线支持 |

## 太好了:ID 和类

ID 和类元素选择器是自动化中的两个不同选项，在应用程序中执行不同的功能。然而，考虑在您的自动化中使用什么元素选择器策略，它们差别很小，我们不需要单独考虑它们。在应用程序中，定义了元素的“id”和“class”属性后，UI 开发人员就可以操作和设计应用程序。对于自动化，我们使用它来定位自动化中交互的特定元素。

使用 id 和类元素选择器的一大好处是它们受应用程序中结构变化的影响最小。假设说，如果您要创建一个 XPath 或 CSS 选择器，它依赖于一串很少的元素和一些[子元素](https://www.w3schools.com/jsref/prop_element_children.asp)，当一个特性请求通过添加新元素来中断这个链时会发生什么？当使用 ID 和类元素选择器时，您可以针对特定的元素，而不是依赖于页面结构。您保留了自动化的健壮性，而不会对变化过于宽容。应该通过创建关注特定元素位置的测试用例，通过自动化来检测变更。变更不应该破坏您的整个自动化套件。然而，如果开发人员直接对自动化中使用的 ID 或类进行更改，那将会影响您的测试。

如果测试中的应用程序没有将 id 和类作为开发最佳实践的一部分来实现，那么这个元素选择器策略就不可用。如果 HTML 标签没有可以在自动化中使用的 id 和类，这种方法就很难使用。

### 示例:

在我们的示例中，如果我们要选择顶级菜单元素，它看起来会像这样:

```
driver.find_elements(By.ID, "main-menu")
```

如果我们选择第一个菜单项，它看起来会像这样:

```
driver.find_elements(By.CLASS_NAME, "menu")
```

### 摘要:ID 和类别

| **利弊** | **缺点** |
| 易于维护 | Developer may change them, breaking automation |
| 简单易学 |
| 受页面结构变化影响最小 |

## 最佳:用 CSS 选择器定制属性

如果您的 QA 组织与开发部门有良好的合作关系，那么您就有机会将这种最佳实践方法用于您的自动化。使用定制属性和 [CSS 选择器](https://www.sitepoint.com/css-selectors/)来定位元素对 QA 团队和组织都有多重好处。对于 QA 团队来说，这允许自动化工程师锁定他们需要的特定元素，而无需创建复杂的元素选择器。然而，这需要添加自动化团队可以在应用程序中使用的定制属性的能力。为了利用这种最佳实践方法，您的开发和 QA 团队应该合作来实现这种策略。

我想花一点时间来说明 CSS 选择器方法不依赖于自定义属性。CSS 选择器可以针对 HTML 文档中的任何标签和属性，就像 XPath 一样。

现在让我们看看这种方法需要什么。为了最好地执行这一点，您的自动化团队应该理解他们想要在自动化中以什么为目标。与开发人员(很可能是前端工程师)一起工作，然后他们将为一个定制属性制定一个模式，以放置到自动化团队需要挂钩的每个目标中。对于这个例子，我们将一个“tid”属性附加到目标元素上。

这里需要强调的一个技术问题是 CSS 选择器的局限性。故意不允许它们像 XPath 那样选择父元素。这样做是为了避免网页上 CSS 样式的无限循环。虽然这对于 web 设计来说是一件好事，但对于将其用作自动化元素选择器策略来说，这是一个限制。幸运的是，这种限制可以通过开发人员实现的自定义属性来避免。QA 应该请求适当的定制属性，这样就不需要选择父元素。

如果您的开发和 QA 团队之间的协作在您的组织中还不存在，不要担心！您应该实现这个策略，因为它可能是驱动协作的机制。不管这种文化是否存在，你都应该采用这种方法，并观察其结果。您不仅会有一个易于维护的元素选择器策略，还会看到协作扩展到组织的其他领域的好处。这将建立的合作关系将使你在质量保证的许多方面受益，如减少缺陷、缩短上市时间和提高生产率。

为了最好地实现这个元素选择器策略并创建协作，您的 QA 团队应该从一开始就参与设计过程。与开发部门一起工作，他们应该评审需求。当开发人员设计特性时，QA 应该建议在哪里可以实现定制属性，以最好地支持自动化工作。通过在设计阶段的开始鼓励这种协作，您将使 QA 和开发团队在协作方面更加紧密，并提高开发过程的效率。这可能会对软件开发生命周期的其他领域产生有益的溢出效应。在这里鼓励合作将使开发和 QA 彼此熟悉，这样其他领域的合作也可能发生。

### 示例:

在我们的示例 HTML 中，在锚标记上实现定制属性将会产生类似于下面这样的结果:

```
<div id="main-menu">
  <div class="menu"><a tid="home-link" href="/home">Home</a></div>
  <div class="menu"><a tid="shop-link" href="/shop">Shop</a>
    <div class="submenu">
      <a tid="gizmo-link" href="/shop/gizmo">Gizmo</a>
      <a tid="widget-link" href="/shop/widget">Widget</a>
      <a tid="sprocket-link" href="/shop/sprocket">Sprocket</a>
    </div>
  </div>
</div>
```

注意一些元素中的新属性。我们创建了一个新属性，它不与任何名为“tid”的标准 HTML 属性冲突。有了这个自定义属性，我们可以使用 CSS 选择器来定位它:

```
driver.find_element(By. CSS_SELECTOR, "[tid=home-link]")
```

假设您想要选择菜单中的所有链接，不管它是顶级菜单项还是子菜单。使用 CSS 选择器，您可以创建高度通用的元素选择器:

```
driver.find_element(By.CSS_SELECTOR, "#main-menu [tid*='-link']")
```

“*=”的作用是在任何元素的 tid 字段中对值“-link”进行通配符搜索。将它放在#main-menu ID 说明符之后，它将元素的搜索集中在主菜单中。

如果您想在不使用定制属性的情况下选择这个策略，那么您的思路还是正确的。例如，您可以使用以下方法定位商店子菜单中的链接:

```
driver.find_element(By. CSS_SELECTOR, "#main-menu .submenu a")
```

这个策略将允许自动化工程师创建可靠的自动化，易于维护，并且不会被 UI 中不相关的变化所破坏。选择这种策略是最好的方法。这不仅是一个易于维护的自动化解决方案，而且会鼓励 QA 团队和开发人员之间的合作。

### 摘要:带有 CSS 选择器的自定义属性

| **利弊** | **缺点** |
| 简单易学 | Initial effort involved in establishing a collaborative relationship with the development team |
| 大量在线支持 |
| 多才多艺的 |
| 在所有浏览器中表现出色 |

## 结论

在自动化框架中实现企业标准元素选择器策略有一些很好的选择。像标签名或链接文本这样的选项应该避免，除非这是你唯一的选择。XPath、ID 和类选择器是一个很好的途径。到目前为止，最好的方法是实现自定义属性，并用 CSS 选择器将它们作为目标。这也鼓励了开发和 QA 团队之间的合作。

以下是并列比较的选项:

| **–是****/–部分****✗–否** | **标签名称，** **链接文字等。)** | **XPath** | **ID &类** | **用 CSS 选择器自定义属性** |
| 使用方便 | **** | **** | **** | **** |
| 在线支持 | **** | **** | **** | **** |
| 多才多艺的 | **✗** | **** | **✗** | **/** |
| 表演 | **** | **✗** | **** | **** |
| 易于维护 | **✗** | **** | **** | **** |
| 鼓励协作 | **✗** | **✗** | **** | **** |

## 分享这篇文章