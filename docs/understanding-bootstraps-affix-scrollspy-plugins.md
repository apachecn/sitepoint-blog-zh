# 了解 Bootstrap 的词缀和 ScrollSpy 插件

> 原文：<https://www.sitepoint.com/understanding-bootstraps-affix-scrollspy-plugins/>

当你访问 [Bootstrap 的](http://getbootstrap.com/)网站时，你可能已经注意到的一件事是，当你滚动页面时，右边的导航会移动。这种滚动效果可以通过组合[词缀](http://getbootstrap.com/javascript/#affix)和 [ScrollSpy](http://getbootstrap.com/javascript/#scrollspy) jQuery 插件来实现，这两个插件都可以作为引导组件获得。

![Bootstrap's docs nav](img/dcec8a5471fd2e8de5bb4db8cb0354a2.png)

在本文中，我将向您展示如何将这个很酷的特性添加到您自己的项目中。和往常一样，为了更好地演示这些，我将使用一个[演示项目](http://codepen.io/SitePoint/full/GgOzwX/)。

让我们先来看看它的 HTML 结构。

## 我们演示的基本标记

对于小屏幕，我们使用单列布局。对于中型和大型屏幕，我们的布局将由两列组成。一方面，我们有导航，它占据了一行宽度的四分之一。另一方面，我们有主要的内容部分，它们占据了一行宽度的另外四分之三。这些部分中的每一个都有一个`id`属性值，它与相应的锚(`a`)元素的`href`属性值相匹配。

在我们的示例中，我们希望滚动效果仅在视口超过 991 像素(即中大屏幕)时出现。出于这个原因，我们将`hidden-xs`和`hidden-sm`助手类添加到导航中。

参见下面的代码。注意，为了简洁，我们不包括与技术无关的内容。

```
<div class="col-md-3 scrollspy">
  <ul id="nav" class="nav hidden-xs hidden-sm" data-spy="affix">
    <li>
      <a href="#web-design">Web Design</a>
    </li>
    <li>
      <a href="#web-development">Web Development</a>
      <ul class="nav">
        <li>
          <a href="#ruby">
            <span class="fa fa-angle-double-right"></span>Ruby
          </a>
        </li>
        <li>
          <a href="#python">
            <span class="fa fa-angle-double-right"></span>Python
          </a>
        </li>

      </ul><!--end of sub navigation-->
    </li>    

  </ul><!-- end of main navigation -->
</div>
<div class="col-md-9">
  <section id="web-design">
  </section>
  <section id="web-development">
    <section id="ruby">
    </section>
    <section id="python">
    </section>

  </section>
</div>
```

现在我们已经理解了项目的基本结构，我们可以包含插件来添加功能。

## 使用词缀

[附加插件](http://getbootstrap.com/javascript/#affix)将帮助我们“固定”导航部分的位置，同时允许我们根据用户滚动的位置向这个固定的元素添加垂直偏移。

要在我们的项目中使用词缀插件，我们必须指定将接收“词缀”行为的元素。我们可以通过向它添加`data-spy="affix"`属性/值来做到这一点。在我们的例子中，期望的元素是`ul`元素。

该插件在三个类别之间切换，如下所述:

1.  `affix-top`类，表示元素在它的最顶端。
2.  当元素开始滚动出屏幕时添加的`affix`类，它对元素应用了`position: fixed`属性。
3.  `affix-bottom`类，表示元素的`bottom`偏移量。

简而言之，当用户上下滚动页面时，插件改变元素的定位类型，使用三个类来实现。

注意，我们可以有选择地利用实现中的所有类。最重要的类是`affix`类，它允许我们在用户向下滚动页面时固定一个元素。然而，根据我们项目的结构，我们可能也想使用`affix-top`和/或`affix-bottom`类。

现在让我们看看如何在我们的例子中包含所有三个类。

首先，我们将`affix-top`类分配给`ul`元素。为此，我们可以使用定制的[数据-*属性](https://www.sitepoint.com/use-html5-data-attributes/)或 JavaScript。下面是必需的 jQuery/JavaScript:

```
$('#nav').affix({
    offset: {
        top: $('#nav').offset().top
    }
});
```

此时，我们已经应用了元素的`top`位置。这是 390px 左右。默认情况下，其位置设置为`static`。尽管 Bootstrap 提到不需要 CSS 定位，但我已经将其位置设置为`relative`。下面是一个带有注释的截图，显示了正在发生的事情:

![Our fixed navigation with Affix](img/e0e50a36a4bffcaf37fda4a0e30aa84d.png)

页面首次加载时，`ul`元素的 HTML 如下所示:

![Generated HTML for ul](img/c48804e81dbe9bc3fe69818bc309a8ca.png)

正如你所看到的，当用户第一次开始滚动时，`ul`元素有了`affix-top`类。当“滚动”超过元素的初始`top`位置(大约 390px)时，`ul`接收到`affix`类，其位置变为`fixed`。然后我们设置它的新的`top`(试着改变它的值来看区别)位置和`width`属性。以下是相应的样式:

```
.affix {
  top: 20px;
  width: 213px;
}

@media (min-width: 1200px) {
  .affix {
    width: 263px;
  }         
}
```

下面是与此阶段相对应的截图:

![Next phase in scrolling with Affix](img/a7ac2390b1ce055c235d4da47f872968.png)

以及生成的 HTML:

![Generated HTML when nav is fixed](img/1bb95a77cf467037cbd462467e7275bc.png)

当用户滚动时，`ul`被固定在页面的顶部。当然，这是一个很好的效果，但是我们最终想要停止锁定。我们可以通过使用`affix-bottom`类来做到这一点。为了让插件触发这个类，我们必须指定目标元素的`bottom`偏移量。同样，这可以通过使用定制属性或 jQuery/JavaScript 来实现。下面是使用 jQuery 的方法:

```
$('#nav').affix({
  offset: {
    bottom: ($('footer').outerHeight(true) + 
            $('.application').outerHeight(true)) + 
            40
  }
});
```

在这个阶段，我们必须使用 CSS 来指定元素的定位类型。例如，我们可以对它应用`position: absolute`或`position: relative`。在我们的例子中，我选择了第一个选项。我们还设置了它的`width`属性。以下是所需的样式:

```
.affix-bottom {
  position: absolute;
  width: 213px;
}

@media (min-width: 1200px) {
  .affix-bottom {
    width: 263px;
  }
}
```

在这个最后阶段，我们的演示将如下所示:

![The final stage of scrolling with Affix](img/fc485438d65b7f0a6ead7316fc7e8b63.png)

*注意:这张最终截图是基于典型的桌面屏幕分辨率。*

下面是生成的 HTML:

![Final stage generated HTML](img/11628c5c4f57331a053e90431973b5e1.png)

如上所示，基于我们用 jQuery 定义的`bottom`偏移量，插件向元素应用一个新的`top`偏移量。

最后，重要的是要提到，在这个特定的演示中，我们必须为元素指定一个`bottom`偏移量。这是因为教育相关部分之后还有内容。例如，如果我们没有指定偏移量，我们的页面看起来会像这样:

![Result without a bottom offset](img/466e78ae2715680b29f6e12d06ec8e4d.png)

现在我们已经有了我们的词缀插件，我们可以添加 ScrollSpy 功能。

## 使用 ScrollSpy

要将 ScrollSpy 添加到我们的项目中，我们必须定义在页面滚动期间想要“监视”的元素。通常这将是`body`元素。ScrollSpy 还要求我们使用一个[引导导航组件](http://getbootstrap.com/components/#nav)。

首先，我们将`data-spy="scroll"`属性/值应用于“spied”`body`元素。在这一点上，值得一提的是，Bootstrap 建议将我们的“被监视”元素的位置设置为`relative`。

接下来，我们通过将`data-target`属性添加到我们正在监视的元素(同样，在我们的例子中，这是`body`)来识别我们想要跟踪的元素的特定部分。该属性的值应该与 nav 组件的父组件的`id`或`class`相匹配。所以`body`标签应该是这样的(注意“.”包括作为`data-*`属性的值的一部分):

```
<body data-spy="scroll" data-target=".scrollspy">
```

类似于词缀插件，也有通过 JavaScript 激活 ScrollSpy 插件的选项。在我们的例子中，我们不会使用这个选项。

激活插件后，我们的 HTML 将如下所示:

```
<body data-spy="scroll" data-target=".scrollspy">
<!-- content here... -->

  <div class="col-md-3 scrollspy">
    <ul id="nav" 
        class="nav hidden-xs hidden-sm" 
        data-spy="affix">
        <!-- nav items here... -->
    </ul>
  </div>

  <!-- content here... -->
</body>
```

唯一必要的 CSS:

```
body {
  position: relative;
}
```

注意，我们“观察”的是`ul`的直接父元素，而不是`ul`。

我们可以使用浏览器的开发工具来理解插件的功能。当用户滚动时，插件“观察”目标元素并将`active`类添加到任何适当的`li`子元素中。然后，我们根据这个类来设计活动元素的样式。我们的一些`li`元素包含子导航元素，我们也可以对其进行样式化以获得良好的效果。

下面的截图显示了使用 ScrollSpy 插件时生成的 HTML:

![Generated HTML with ScrollSpy](img/e18ac6a12bace346ba3e684af82def57.png)

下面是我在演示中应用于导航组件的样式。请注意，样式考虑到了我使用的嵌套导航组件。

```
.nav .active {
  font-weight: bold;
  background: #72bcd4;
}

.nav .nav {
  display: none;
}

.nav .active .nav {
  display: block;
}

.nav .nav a {
  font-weight: normal;
  font-size: .85em;
}

.nav .nav span {
  margin: 0 5px 0 2px;
}

.nav .nav .active a,
.nav .nav .active:hover a,
.nav .nav .active:focus a {
  font-weight: bold;
  padding-left: 30px;
  border-left: 5px solid black;
}

.nav .nav .active span,
.nav .nav .active:hover span,
.nav .nav .active:focus span {
  display: none;
}
```

## 结论

这是最终演示的链接:

[引导词缀和 ScrollSpy 演示](http://codepen.io/SitePoint/full/GgOzwX/)

在本文中，我们看到了如何使用 Bootstrap 的词缀和 ScrollSpy 插件构建滚动效果。您可能已经在 Bootstrap 的文档页面上看到了这些组件，现在您知道它们是如何工作的了。

如果您已经使用了这些组件或来自第三方的类似组件，请随时让我们知道您在讨论中的体验。

## 分享这篇文章