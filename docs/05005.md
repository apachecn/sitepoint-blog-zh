# HTML 导入简介(教程)

> 原文：<https://www.sitepoint.com/introduction-html-imports-tutorial/>

HTML 导入是一种在页面上包含外部 HTML 文档和 web 组件的方式，无需发出 Ajax 请求或加载 iframe。由于这种能力，HTML 导入可能会导致更好的页面加载时间，重用代码的新机会，以及更容易与一些流行的服务(如 Google Maps)集成。

在撰写本文时，HTML Imports 技术还是 W3C 的工作草案，这意味着它还不是一个 web 标准，并且还不是所有的浏览器都支持。然而，HTML 导入可以安全地用在今天的网页上，用[一个类似于 Polymer](http://www.polymer-project.org/platform/html-imports.html) 中的聚合填充。

## HTML 导入以便重用、联合

从哲学上讲，软件开发人员，包括 web 开发人员，被教导不要重复他们自己，而是以模型、对象、函数或类似的方式组织代码，以便有一个单一的事实点和一个重用组件的简单方法。多亏了开发人员[迪夫·托马斯](http://en.wikipedia.org/wiki/Dave_Thomas_%28programmer%29)和[安德鲁·亨特](http://en.wikipedia.org/wiki/Andy_Hunt_%28author%29)，这种方法通常被称为干开发或干编程，意思是“不要重复自己”

某种程度上，HTML 导入支持干开发。这里有一个例子。想象一下，你有三条信息在几个网页上重复，甚至可能是在不同领域的网页上。不需要为每个页面或项目复制、粘贴或重新键入这些消息，可以使用 HTML 导入功能来包含它们。

下面是一个名为`messages.html`的文件的 HTML:

```
<div class="message-success">
    <h2>Success</h2>
    <p>Whatever you just did worked.</p>
</div>

<div class="message-failure">
    <h2>Failure</h2>
    <p>What a disappointment.</p>
</div>

<div class="message-ego-boost">
    <h2>You are amazing.</h2>
</div>
```

要重用`messages.html`中的内容，首先通过 HTML 导入包含文档。这相当于添加了一个`<link>`元素，其`rel`属性设置为“导入”,其`href`指向`messages.html`,如下所示:

```
<link rel="import" href="messages.html">
```

我们可以编写一小段 JavaScript 来访问导入的`messages.html`文档，并在页面上加载“成功”消息。其他消息中的每一个也可以以相同的方式加载，或者全部一起加载，这取决于人们想要如何使用它们。代码如下:

```
var htmlImport = document.querySelector('link[rel="import"]');
var htmlDoc = htmlImport.import;
var htmlMessage = htmlDoc.querySelector('.message-success');
document.body.appendChild(htmlMessage.cloneNode(true));
```

该脚本完成四项任务:

*   选择链接元素
*   导入外部 HTML 文档
*   选择导入的 DOM 的一部分(节点)
*   将所选节点的副本添加到当前页面

就这样，成功消息现在包含在页面上，类似于 Ajax 请求的工作方式。

当然，这个例子过于简化，在实际应用程序中对于 HTML 导入不是很实用，因为成功消息只有四行 HTML，而使用 HTML 导入加载成功消息需要七行代码。但是如果`messages.html`中的内容要复杂得多，可能有几百行支持 JavaScript 和 CSS 的 HTML 代码，HTML Imports 仍然只需要 7 行代码就可以加载它。

作为第二个导入重用的例子，考虑一下脚本语言通常是如何将可重用的内容加载到页面中的。下面的例子来自 WordPress 的[下划线起始主题](http://underscores.me/)的`single.php`文件:

```
<?php
/**
 * The template for displaying all single posts.
 *
 * @package _s
 */

get_header(); ?>

    <div id="primary" class="content-area">
        <main id="main" class="site-main" role="main">

        <?php while ( have_posts() ) : the_post(); ?>

            <?php get_template_part( 'content', 'single' ); ?>

            <?php _s_post_nav(); ?>

            <?php
                // If comments are open or we have at least one comment, load up the comment template
                if ( comments_open() || '0' != get_comments_number() ) :
                    comments_template();
                endif;
            ?>

        <?php endwhile; // end of the loop. ?>

        </main><!-- #main -->
    </div><!-- #primary -->

<?php get_sidebar(); ?>
<?php get_footer(); ?>
```

注意，`single.php`代码示例中有几个地方引用和使用了外部资源，包括获取页眉、加载模板、加载文章导航、加载侧边栏和加载页脚。

PHP 脚本调用外部资源的方式类似于 HTML 导入如何用于向页面添加内容和功能。当考虑到 HTML 导入是 HTML Web 组件草案的一部分时，这一点会变得更加清楚。

Web 组件有四个构件，如果你愿意的话。 [Eric Bidelman](https://plus.google.com/+EricBidelman/posts) ，他在谷歌开发人员关系部门工作，并于 2013 年和 2014 年在谷歌全球大会上发表了网络组件演讲，他描述了这些障碍:

*   **Shadow DOM**–提供 DOM 和样式封装。
*   HTML 模板是可克隆的 DOM 元素的惰性块，充当脚手架或蓝图。
*   **自定义元素**–扩展 HTML 现有词汇，扩展 DOM 对象。
*   **HTML 导入**–允许 web 组件被打包、共享、分发和重用。

记住 web 组件，您可能能够想象如何使用 HTML 导入将重要的特性和功能加载到任何 HTML 页面。这里有一些理论上的 web 组件，HTML 导入可以为您加载。

*   **使用 PayPal** 结账——在页面上放置一个`<x-paypal-express key="6827364828736" callback="some.html">`标签，传递一个嵌套的 HTML 表单，你就可以在任何页面上添加安全结账。
*   **电子邮件订阅表单**–使用 HTML 导入，添加电子邮件订阅表单可能就像导入一个外部 HTML 文档并添加一个`<email-subscribe>`标签一样简单。
*   **谷歌地图**–一个常用的 HTML 导入示例是添加一个带有简单定制元素的谷歌地图`<google-map lat="37.790" long="-122.390">. This works, of course, only after HTML Imports has loaded the document defining this custom tag.`

所有这些例子都依赖 HTML 导入来包含。

关于 HTML 导入如何帮助 web 和应用程序开发人员的第三个例子来自 Bidelman，他认为导入可以更有效地加载一些框架或其他 HTML 和相关资源的捆绑包。

在他的具体例子中，Bidleman 将 Bootstrap 及其许多文件和依赖项装入一个包中。

```
<link rel="import" href="bootstrap.html">
```

名为`bootstrap.html`的源代码看起来与你在 Bootstrap 驱动的站点的`<head>`中看到的相似:

```
<link rel="stylesheet" href="bootstrap.css">
<link rel="stylesheet" href="fonts.css">
<script src="jquery.js"></script>
<script src="bootstrap.js"></script>
<script src="bootstrap-tooltip.js"></script>
<script src="bootstrap-dropdown.js"></script>
```

## 浏览器对 HTML 导入的支持

如上所述，目前[几乎没有浏览器支持 HTML 导入](http://caniuse.com/#search=html)，默认情况下只有 Chrome 36+、Android 版 Chrome 37+和 Opera 23+提供原生支持。Firefox 32+确实支持 HTML 导入，但前提是用户在`about:config`中启用它们。

有多种填充可以使该功能在几乎任何浏览器中都可用。也许，最稳健的是前面提到的[聚合物进口 polyfill](http://www.polymer-project.org/platform/html-imports.html) 。

## 进一步阅读和资源

*   [W3C HTML 导入工作草案](https://www.w3.org/TR/html-imports/)
*   [W3C HTML 导入编辑稿](http://w3c.github.io/webcomponents/spec/imports/)
*   [埃里克·比德尔曼的 HTML 导入教程](http://www.html5rocks.com/en/tutorials/webcomponents/imports/)
*   [YouTube 上的 2013 年 Google I/O Web 组件会议](http://youtu.be/fqULJBBEVQE)
*   [YouTube 上的 2014 年谷歌 I/O 聚合物和网络组件会议](http://youtu.be/8OJ7ih8EE7s)

## 分享这篇文章