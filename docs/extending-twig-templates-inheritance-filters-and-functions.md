# 扩展分支模板:继承、过滤器和函数

> 原文：<https://www.sitepoint.com/extending-twig-templates-inheritance-filters-and-functions/>

在 MVC 架构中工作时，通常使用模板库来填充视图的动态内容。PHP 有几十个这样的库，但是 Twig 是其中的佼佼者，因为它能够用自定义实现扩展核心特性。在本文中，我们将探索如何使用模板继承、过滤器和函数来扩展 Twig 模板。

## 模板库的限制

许多模板库最常见的限制是无法扩展模板特性。例如，在页面的页眉或页脚部分包含对 CSS 和 JavaScript 文件的所有引用是很常见的，因此我们创建一个页眉模板，并在所有页面中包含该文件。但是如果我们有一个需要在页面加载时初始化 Google 地图的页面呢？地图的 JavaScript 应该只从这一个页面调用，但是许多开发人员会不必要地将所有的 JavaScript 和 CSS 文件包含在一个页面头中。

另一种解决方法是在标题中包含公共共享文件，并根据需要在页面模板中包含其他特定文件。头文件可能如下所示:

```
<!DOCTYPE html>
<html>
 <head>
  <link rel="stylesheet" href="style.css">
  <script src="jQuery.js" type="text/javascript"></script>
 </head>
```

…地图模板可能如下所示:

```
<script src="googlemaps.js" type="text/javascript"></script>
<script type="text/javascript">
$(document).ready(function(){
    $("#google_map").google_map();
});
</script>
<div id="google_map"></div>
```

标题中引用了常见的 CSS 和 JavaScript，特定于地图的代码位于页面模板中。虽然这样做是可行的，但理想情况下，我们应该将所有这些引用保存在一个地方，以保持一致性和代码的整洁，而不是像这样在文件之间进行分割。

对于大多数模板库，我们除了采用上述技术之外别无选择。但是现在让我们看看 Twig 提供了什么来应对这种情况。

## 扩展树枝模板

Twig 让我们能够使用继承从父模板扩展子模板。要了解继承是如何工作的，最简单的方法可能就是用一个例子。以下代码是父模板:

```
<!DOCTYPE html>
<html>
 <head>
  {% block head %} Sample Content {% endblock %}
 </head>
 <body>
 </body>
</html>
```

我们在父模板中定义了一些块，这些块有一个名字和一些内容。然后我们可以扩展子模板中的已定义块，如下所示:

```
{% extends "parent.html" %}
{% block head %} Head 1 {% endblock %}
```

在子模板中，我们通过使用 extend 关键字后跟模板文件名来扩展父模板。然后我们重新定义 head 块来包含动态扩展的内容。省略子模板中的 head 块将导致父模板使用它已经定义为默认值的内容。

现在我们对 Twig 中的继承有了一个基本的概念，让我们创建一个更完整的页面布局。

```
<!DOCTYPE html>
<html>
 <head>
  {% block head %}
  <link rel="stylesheet" href="style.css">
  <script src="jQuery.js" type="text/javascript"></script>
  {% endblock %}
 </head>
 <body>
  <div id="grid">{% block content %} {% endblock %}</div>
  <div id="info">{% block sidebar %} {% endblock %}</div>
  <div id="footer">
   {% block widget1 %} {% endblock %}
   {% block widget2 %} {% endblock %}
   {% block widget3 %} {% endblock %}
   {% block footer %}
   &copy; 2013 example.com
   {% endblock %}
  </div>
 </body>
</html>
```

我已经创建了一个主模板，它包含了一个标准网页的所有组件。有一个标题块，我们可以在其中动态地包含文件，一个动态的主要内容区域和一个信息侧边栏。在页脚有三个动态部件，后面是版权信息。

对于其他模板系统，我们可能需要分别创建页眉、页脚、内容和侧边栏，以便在多个模板之间重用。但是有了 Twig 对继承的支持，我们可以创建一个主模板用于我们站点的所有页面。

假设我们想要一个依赖于特定 jQuery 插件的页面模板。基本上，我们需要包含依赖于我们已经提供的 jQuery 库的插件文件，所以我们将以这种方式扩展模板:

```
{% block head %}
{{ parent() }}
<script src="jquery.plugin.js"></script>
{% endblock %}
```

head 块被重新定义以引用自定义插件文件，但同时保留原始 head 部分的内容。`parent()`函数用于检索原始头内容。

事实上，Twig 模板非常灵活，能够满足任何类型的需求。块可以放在任何地方，块内容可以以各种方式改变以适应我们的需要。

到目前为止，我们只讨论了如何使用模板继承来扩展我们的模板。Twig 提供了许多特性来进一步扩展模板；现在让我们看看过滤器和函数是如何适应这个过程的。

## 树枝过滤器的作用

过滤器用于修改模板中的变量。您可以在 Twig 文档中找到内置过滤器的[列表，但是为了说明这个概念，我将向您展示一个过滤器的基本用法，从一个值中删除前导和尾随空白。](http://twig.sensiolabs.org/doc/filters/index.html "Filters - Documentation - Twig - The flexible, fast, and secure PHP template engine")

```
{{ "Varible content" | trim() }}
```

内置过滤器非常有用，但过滤器的真正威力来自自定义过滤器的实现。Twig 提供了一个定义良好的方法来创建我们自己的过滤器。由于数据网格是在 web 应用程序中呈现数据的一种常见方式，所以让我们假设我们有一个网格来显示包含不同类别的产品表。我们被要求用特定的颜色突出显示每个类别的数据。我们可以使用自定义过滤器来实现这一功能。

通过使用匿名 PHP 函数和`Twig_SimpleFilter`类创建新的过滤器。

```
<?php
$filter = new Twig_SimpleFilter("highlight", function ($key) {
    switch (trim($key)) {
        case "book_category":
            echo '<span class="books_color">';
            break;
        case "cd_category":
            echo '<span class="cd_color">';
            break;
        case "ebook_category":
            echo '<span class="ebook_color">';
            break;
        default:
            // nothing
    }
});
$twig->addFilter($filter);
```

我创建了一个名为“高光”的新滤镜，然后使用`addFilter()`方法将其添加到 Twig 环境中。类别将作为变量传递给过滤器，在函数内部，我们切换类别并输出一个具有特定 CSS 类的元素，以在网格中生成动态突出显示。

我们可以像这样在模板中使用这个过滤器，将产品作为一个数组，由我们的产品名称和类别组成:

```
{% block content %}
 {% for product in products %}
  <div>
   {% filter highlight %}
   {{ product.category }}
   {% endfilter %}
   {{ product.product }}
   </span>
  </div>
 {% endfor %}  
{% endblock %}
```

## 分支函数的作用

Twig 函数用于向我们的模板添加动态内容。有一个内置函数的[选择，但是和过滤器一样，函数的真正力量来自于创建我们自己的实现的能力。](http://twig.sensiolabs.org/doc/functions/index.html "Functions - Documentation - Twig - The flexible, fast, and secure PHP template engine")

```
<?php
$function = new Twig_SimpleFunction("function_name", function ($arg1, $arg2) {
    // implementation
});
$twig->addFunction($function);
```

同样，我们可以使用匿名函数来创建动态枝函数。`Twig_SimpleFunction`类的第一个参数是函数名，第二个是函数定义。使用`addFunction()`将该函数添加到 Twig 环境中。

让我们假设我们使用一个库来处理表单域，如文本框、复选框等。通常我们只需在模板中直接编写所需的 HTML，如下所示:

```
<input type="text" name="fname" class="chosen">
```

但是如果这个库需要某些属性才能工作，就很难在不出错的情况下为每个表单包含必要的 HTML。我们可以创建一个 Twig 函数来插入每个字段。

```
<?php
$function = new Twig_SimpleFunction("form_text", function ($name, $id, $value = "", $class = "form_text") {
    echo '<input type="text" name="'.$name.'" id="'.$id.'" value="'.$value.'" class="'.$class.'">";
});
$twig->addFunction($function);
```

这种技术确保我们包含正确的属性而不会出错。我们将使用下面的代码从模板中调用这个函数:

```
{{ form_text("fname", "fname", "", "chosen") }}
```

在扩展 Twig 模板时，继承、过滤器和函数是一个强大的组合。我们可以以不同的方式使用这些来轻松处理我们的应用程序的高级需求。

## 摘要

创建可扩展模板的能力允许我们在需要时在应用程序的多个地方重用模板和特性，而 Twig 是支持扩展核心库功能的最佳模板库之一。Twig 还附带了大量文档，解释了模板创建过程的各个方面，这使得扩展变得很容易。

让我知道你用 Twig 的经验和你对它的模板扩展过程的想法。我期待着收到你的来信。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章