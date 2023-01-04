# jQuery 选择框组件–选择与选择 2

> 原文：<https://www.sitepoint.com/jquery-select-box-components-chosen-vs-select2/>

*这篇文章由[马丁·马丁内斯](https://www.sitepoint.com/author/mmartinez/)和[克里斯·佩里](https://www.sitepoint.com/author/cperry/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

你有没有在一个项目中工作过，看起来好像有什么不对劲？也许项目快完成了，但有些元素看起来不太好？这些可能是小细节，但它们很重要。

如果你的项目包含不吸引人的选择框，并且你想给它们添加更多的特性，你会发现[选择](https://harvesthq.github.io/chosen/)和[选择 2](http://select2.github.io/) 非常有用。这是两个 [jQuery 插件](https://plugins.jquery.com/)，它们帮助你设计选择框的样式，改善它们的外观，定制它们的行为，使它们更加用户友好。

在本文中，我将把这两个插件放在一起，并比较它们的特性和用例，这样您就可以做出明智的选择，哪一个最适合您。

## 装置

选择的和选择的[都可以通过 GitHub 获得。这意味着您可以克隆各自的存储库并获得您需要的文件。](https://github.com/select2/select2)

```
 git clone https://github.com/harvesthq/chosen.git
git clone https://github.com/select2/select2.git 
```

否则，你可以用 [Bower](http://bower.io/) (作为 GitHub 的接口)安装这两个插件。如果 Bower 是您的首选路线，那么您也可以顺便使用 jQuery。

```
 bower install jquery
bower install select2
bower install chosen 
```

否则，从您选择的 CDN(例如 [CDNJS](https://cdnjs.com/) )中获取文件，并以通常的方式将它们包含在您的页面中。这就是我在下面的模板中所做的，你可以用它来跟随教程中的例子。

### 挑选出来的

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Chosen/Select2 Template</title>
    <link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/chosen/1.4.2/chosen.min.css">
  </head>
  <body>

    <script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
    <script src="//cdnjs.cloudflare.com/ajax/libs/chosen/1.4.2/chosen.jquery.min.js"></script>
    <script>// Additional logic goes here</script>
  </body>
</html> 
```

### 选择 2

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Select2 Template</title>
    <link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/select2/4.0.1/css/select2.min.css">
  </head>
  <body>

    <script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
    <script src="//cdnjs.cloudflare.com/ajax/libs/select2/4.0.1/js/select2.min.js"></script>
    <script>// Additional logic goes here</script>
  </body>
</html> 
```

## 项目是否得到积极维护？

正如你可以通过访问 GitHub 页面了解到的那样，所选插件的开发不像以前那么活跃了，所选插件的最新版本可以追溯到 2014 年 2 月 6 日。这并不是说图书馆已经被废弃了——远非如此！相反，开发者已经把它放到了他们想要的地方，它仍然是一个可靠和健壮的插件。

另一方面，与 Select2 相比，差异是显而易见的。这个库的开发正在全速前进，它最近发布了官方的第四版 (4.0.0)。这意味着正在添加新功能，而其他功能则被弃用和/或删除。

另一个有用的度量可能是栈溢出时带有这些标签的问题的数量。你可以在这里亲自尝试一下:[http://stackoverflow.com/tags](http://stackoverflow.com/tags)

如果你输入“Select2 ”,然后对比“Chosen ”,你会发现 Select2 插件周围似乎有更多的活动。你还会看到它有许多集成，比如与 [AngularJS](https://github.com/angular-ui/ui-select) 和 [Ruby on Rails](https://github.com/argerim/select2-rails) 的集成。

## 选择框和占位符

[Select2](https://select2.github.io/examples.html#single) 和[choosed](https://harvesthq.github.io/chosen/#standard-select)改变了简单多选框的出现方式。

在纯 HTML 的多个选择框中，用户可以看到列表中的一些可用选项。这在视觉上没有吸引力，必须对此视图进行更改。[选择](https://harvesthq.github.io/chosen/#multiple-select)和[选择 2](https://select2.github.io/examples.html#multiple) 移除“列表视图”,当您在选择框内点击时，选项列表将会出现。只有这样你才能做出选择。

你可以用这两个插件搜索你的选项。只需点击框内，然后输入你选择的第一个字符，然后结果会根据你的搜索实时出现。

Select2 使取消选择已经选择的选项变得更加容易。当下拉列表打开时，您可以单击任何选定的元素来取消选择它们。Chosen 尚未实现此功能。

选择的[和选择的](http://harvesthq.github.io/chosen/#default-text-support)和选择的[都支持单个和多个选择框的占位符文本。](https://select2.github.io/examples.html#placeholders)

## 有限数量的选择

有时在多选框中，您需要限制用户选择的选项数量。使用 Chosen 和 [Select2](https://select2.github.io/examples.html#multiple-max) 的优点是，您可以非常容易地更改选择的数量。

```
 max_selected_options: 2 // Chosen Plugin 
```

```
 maximumSelectionLength: 2 // Select2 Plugin 
```

你可以使用这两个插件来限制从多选框中选择的选项数量，尽管值得注意的是[choosed 的版本](http://harvesthq.github.io/chosen/#limit-selected-options-in-multiselect)更先进。达到限制后，如果您试图选择一个附加选项，则会调用一个事件。

在这个例子中，用户被要求选择一周中最有效率的两天。当他们试图选择第三天时，被选中者将调用一个事件。我已经设置了 Chosen up 来添加一个`alert`,显示已经达到极限:

见笔[限量选择:在](http://codepen.io/SitePoint/pen/gPvdOb/) [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )选择 vs 选择 2 。