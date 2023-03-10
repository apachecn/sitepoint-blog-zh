# 快速提示:用 Gumroad 和 Jekyll 在 30 秒内完成电子商务

> 原文：<https://www.sitepoint.com/e-commerce-30-seconds-gumroad-jekyll/>

![Ecommerce with Jekyll and Gumroad in thirty seconds](img/4cfec6b7c17255cc88d3d7e4b1ce4cec.png)

在我上一篇快速技巧文章[如何在 Jekyll](https://www.sitepoint.com/quick-tip-how-to-build-customizable-html-widgets-in-jekyll/) 中构建可定制的 HTML 小部件中，您学习了如何为 Jekyll 网站制作自己的动态小部件。今天，我将向您展示如何利用这些知识将基于 Jekyll 的网站与 Gumroad 的服务相结合，从而在几秒钟内添加真正强大的电子商务功能。

## 什么是口香糖

对于那些不知道的人来说， [Gumroad](https://gumroad.com/) 是一家总部位于旧金山的电子商务初创公司，成立于 2012 年。他们的服务旨在让内容创作者更容易进行电子商务。

此外，Gumroad 还提供有用的工具，使您能够跟踪销售情况，并向潜在客户推销您的产品。你可以在 Gumroad 的网站上了解更多信息。

对于网页设计师来说，Gumroad 提供了一些令人兴奋的功能，特别是他们的小部件、 [webhooks](https://webhooks.pbworks.com/w/page/13385124/FrontPage) 和 API ( [应用编程接口](http://www.webopedia.com/TERM/A/API.html))。

## 在你的网站上嵌入产品

要开始用 Gumroad 启动您的 Jekyll 网站，请遵循以下步骤。

### 从 Gumroad 的 Widgets 页面获取代码

第一步是前往 Gumroad 的 [Widgets](https://gumroad.com/widgets) 页面，在那里你可以获得你将要用来创建你的 widget 的代码。

根据你希望 Gumroad 产品在网站上的展示方式，选择*覆盖*或*嵌入*(或者为什么不为每个选项制作一个小工具呢？).出于本教程的目的，选择*嵌入*并启用*重定向至 Gumroad* 选项。

![Widgets page on Gumroad Website for integration with Jekyll](img/123171469aaea556bc38381399291040.png)

接下来，向下滚动到页面底部，在那里您会找到 Gumroad 自动生成的代码片段。这段代码有两个部分:一个`<script>`元素，和一个包装锚标记的`<div>`元素(如果您选择*覆盖*，第二部分将是锚标记)。

![Code snippet generated by Gumroad to integrate with Jekyll](img/9b4add0cf17bd653ce4db2529e8008cc.png)

### 将 Gumroad 脚本添加到您的网站

首先将`script`元素复制粘贴到 Jekyll 站点的`/_includes/head.html`文件中。现在，你可以让它保持原样，但是除非你打算在你网站的每一页都展示 Gumroad 产品，否则没有必要*总是*加载脚本文件。为了优化加载时间，只需使用 [Liquid language](https://shopify.github.io/liquid/) 在`<script>`标签周围包装一个条件语句，它会自动检测您实际使用 Gumroad 小部件的时间，然后加载脚本:

```
{% if page.content contains "gumroad" %}
  <script src="https://gumroad.com/js/gumroad-embed.js"></script>
{% endif %}
```

### 创建小部件

既然第一部分已经完成，那么您要做的就是构建实际的 HTML 小部件。为此，在您的`/_includes/`文件夹中创建一个名为`gumroad-embed.html`的新文件，在这里您将复制粘贴自动生成代码的第二部分:

```
<div class="gumroad-product-embed" data-gumroad-product-id="demo" data-outbound-embed="true">
  <a href="https://gumroad.com/l/demo">Loading...  </a>
</div>
```

### 让它充满活力

然而，您还没有完成小部件的构建。如您所见，它仍然链接到默认的演示产品。相反，您希望它显示您选择的任何产品！

解决方案是用一个 Liquid 标签替换演示值`id`和链接属性`href`的值。在本教程中，您将设置内联变量，这使得在同一页面上多次使用该小部件成为可能(请参考[我以前的文章](https://www.sitepoint.com/quick-tip-how-to-build-customizable-html-widgets-in-jekyll/#settingvariables)，以了解更多关于在 Jekyll 中设置变量的其他方法)。

```
<div class="gumroad-product-embed" data-gumroad-product-id="{{ include.id }}" data-outbound-embed="true">
  <a href="https://gumroad.com/l/{{ include.id }}">Loading...</a>
</div>
```

注意:如果您正在使用*覆盖*小部件，您可能想要应用相同的技术来使元素动态化。方法如下:

```
<a class="gumroad-button" href="https://gum.co/{{ include.id }}" target="_blank">{{ include.button }}</a>
```

## 搞定了。

就是这样！恭喜您，现在您可以轻松地在网站的任何地方嵌入产品，只需一行液体标记:

```
{% include gumroad-embed.html id="PRODUCT ID HERE" %}
```

你在你的 Jekyll 网站上用过 Gumroad 吗？你觉得整合过程怎么样？你最喜欢的 Jekyll 网站的电子商务解决方案是什么？

让我们在下面的评论中了解一下吧。

## 分享这篇文章