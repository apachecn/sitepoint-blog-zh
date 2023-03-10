# 快速提示:如何在 Jekyll 中构建可定制的 HTML 小部件

> 原文：<https://www.sitepoint.com/quick-tip-how-to-build-customizable-html-widgets-in-jekyll/>

![Customizable HTML widgets in Jekyll](img/8ecf3a05ddd3d05380e934c1e10bb380.png)

静态站点生成器 Jekyll 在网页设计界以轻量级和“hacky”著称。但这只是事实的一部分。Jekyll 令人惊讶地强大，因为你可以用它做什么，以及它对非技术用户和客户有多友好。

在这个快速技巧中，我将展示如何构建 HTML 小部件，您的客户或团队成员可以轻松地定制这些小部件，并将其包含在 Jekyll 项目中的任何地方——不需要 Ruby 插件，只需要使用由 Shopify 创建的开源模板语言 [Liquid](https://shopify.github.io/liquid/) 和老式 HTML。

## 设置变量

有几种设置自定义变量的方法。在本文中，我将介绍其中的两种方法:内联的*方法*和前端的*方法*。

### 内嵌变量

如果这个小部件很有可能不止一次出现在博客文章中，那么内联设置变量是最好的选择。在这个例子中，我将使用贝宝按钮。

首先，在您的`_includes`文件夹中创建一个名为`paypal-widget.html`的新文件。然后，用以下代码填充它:

```
<form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_top">
  <input name="cmd" type="hidden" value="_s-xclick">
  <input name="hosted_button_id" type="hidden" value="VALUE">
  <button class="buy-button" name="submit">Buy Now</button>
  <img src="https://www.paypalobjects.com/en_US/i/scr/pixel.gif" alt="" width="1" height="1" border="0" style="display: none !important;">
</form>
```

上面的例子将生成一个简单的“立即购买”按钮，允许人们通过贝宝支付。只有一个问题:代码是静态的，不能定制。

这个小部件有两个您希望保持动态的元素:`hosted_button_id`的`value`和`button`标签。使用内联变量可以很快实现这一点:

```
<form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_top">
  <input name="cmd" type="hidden" value="_s-xclick">
  <input name="hosted_button_id" type="hidden" value="{{ include.id }}">
  <button class="buy-button" name="submit">{{ include.button }}</button>
  <img src="https://www.paypalobjects.com/en_US/i/scr/pixel.gif" alt="" width="1" height="1" border="0" style="display: none !important;">
</form>
```

你添加的变量是`include.id`和`include.button`。当您准备好将 HTML 小部件包含在一篇降价文章中时，您可以简单地这样做:

```
{% include paypal-widget.html id="EXAMPLE-ID" button="Buy Now | $30" %}
```

这将创建一个标签为“立即购买| $30”的按钮。您可以在同一页面中多次包含同一个文件，每次包含不同的`include.id`和`include.button`值，因为它们是单独内联设置的。

### 前沿问题变量

对于在一篇博客文章中只会出现一次的较长的文本字符串和小部件，你可以在你的文章和页面的[开头的](https://jekyllrb.com/docs/frontmatter/)中设置变量的值。在本例中，您将创建一个新闻快讯注册框。

像以前一样，首先在 Jekyll 项目的`_includes`文件夹中创建一个新文件，将其命名为像`signup-widget.html`这样有意义的名称。然后，用以下代码填充它:

```
<div class="signup-cta">

  <h2>Sign up to the newsletter</h2>
  <p>Receive daily updates straight to your inbox—just fill out your name and email address and hit Submit!</p>

  <form method="POST">
    <input id="name" placeholder="First name">
    <input type="email" placeholder="Email address">
    <button type="submit">Submit</button>
  </form>

</div>
```

在这个例子中，有三个元素需要保持动态:文本`h2`、元素下面的文本`p`和文本`button`:

```
<div class="signup-cta">

  <h2>{{ page.cta.title }}</h2>
  <p>{{ page.cta.body }}</p>

  <form method="POST">
    <input id="name" placeholder="First name">
    <input type="email" placeholder="Email address">
    <button type="submit">{{ page.cta.button }}</button>
  </form>

</div>
```

现在，当你把它包含在一个减价的帖子或页面中时，使用 [Yaml](http://yaml.org/) 在前面定义以下变量:

```
---
cta:
    title: "Get the newsletter"
    body: "Like this blog post? Sign up to receive more content like this, delivered straight to your inbox every day."
    button: "Sign me up!"
---
```

然后，您可以在文本中的任何位置包含小部件，就像您在另一个示例中所做的那样:

```
{% include signup-widget.html %}
```

添加一些 CSS 后的结果如下所示:

![Jekyll Newsletter sign-up widget in action](img/a3f3f442a5e9557f5f44b339e9030b16.png)

当然，您可以在一篇博客文章中不止一次地包含这些内容——但是两个实例都将从相同的源中检索它们的变量值，所以它们看起来完全一样。如果您想在一个页面或博客文章中多次包含一个小部件，并单独定制每个小部件，内联变量是一个不错的选择。

## 结论

好了，你知道了。为 Jekyll 项目创建强大模块的两种快速简单的方法。这项技术的可能性是无限的，我很想在下面的评论中听到你最终是如何使用它的。我很乐意回答你可能有的任何问题。

## 分享这篇文章