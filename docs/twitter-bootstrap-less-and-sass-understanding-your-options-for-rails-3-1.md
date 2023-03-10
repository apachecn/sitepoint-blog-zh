# Twitter Bootstrap、Less 和 Sass:了解您对 Rails 3.1 的选择

> 原文：<https://www.sitepoint.com/twitter-bootstrap-less-and-sass-understanding-your-options-for-rails-3-1/>

到目前为止，我们都已经看到了[Twitter Bootstrap](http://twitter.github.com/bootstrap/)——这是一个很棒的 CSS 和 Javascript 库，由 Twitter 开源，可以轻松地创建一个外观非常精美的网站，对布局、导航、排版等提供了出色的支持。Twitter Bootstrap 基于 [Less.js](http://lesscss.org/) ，由 [Alexis Sellier 或@cloudhead](http://twitter.com/#!/cloudhead) 编写的流行动态 CSS 脚本语言。Less.js 和 [Node.js](http://nodejs.org/) 一样，完全是用 Javascript 实现的。虽然 Less 是基于 Javascript 而不是 Ruby 的，但是在过去的几个月里，已经做了一些很棒的工作，使得使用各种不同的方法在 Rails 3.1 应用程序中设置 Twitter Bootstrap 变得很容易。

| ![](img/caf1c15379c8b041500c1389dcf4b9ee.png) |
| Twitter Bootstrap 是快速建立一个非常完美的网站的好方法。 |

今天我要回顾一下 Twitter Bootstrap 的基础知识，然后仔细看看下面这些 gem 和库: [less-rails-bootstrap](https://github.com/metaskills/less-rails-bootstrap) 、 [sass-twitter-bootstrap](https://github.com/jlong/sass-twitter-bootstrap) 、 [bootstrap-sass](https://github.com/thomas-mcdonald/bootstrap-sass) 和 [bootstrap-rails](https://github.com/anjlab/bootstrap-rails) 。有了所有这些不同的选项，很难确切地知道如何开始使用 Twitter Bootstrap 和 Rails。在你开始构建下一个杀手级 Twitter Bootstrap Rails 3.1 应用之前，一定要了解这些不同的宝石是如何工作的，这样你才能决定哪一个适合你和你的应用。

## Twitter 引导基础知识

从表面上看，Twitter Bootstrap 只是一个 CSS 文件 bootstrap.css，它提供所有的样式和布局支持，以及一些实现各种动态特性的 Javascript 文件。这意味着开始使用它的最快和最简单的方法就是将 bootstrap.css 复制到 Rails 3.1 app/assets/stylesheets 文件夹中，如下所示:

```
$ git clone https://github.com/twitter/bootstrap.git
$ cp bootstrap/bootstrap.css path/to/app/assets/stylesheets/.
```

…你就要去参加比赛了！使用他们的[在线演示页面](http://twitter.github.com/bootstrap/)作为入门指南；您甚至可以在页面上使用“Inspect Element”或“View Source”来了解他们为每个特性使用了什么样式。

但是，正如我上面提到的，Twitter Bootstrap 是基于 Less.js 框架的，bootstrap.css 文件实际上是一个编译版的 Less 代码，包含在一系列的”。少"代码文件:

![](img/7891b3ee21af4f5ec7eb0843d49c7bf7.png)

Twitter 引导文件

在 Twitter Bootstrap github repo 中查看，少代码文件包含在 [lib 文件夹](https://github.com/twitter/bootstrap/tree/master/lib)中，而编译后的 bootstrap.css 文件就在[根文件夹](https://github.com/twitter/bootstrap)中。

如果您不熟悉 Less，它与 Sass 非常相似:它为创作 CSS 样式的代码提供了一种增强的、更强大的语言。与 Sass 一样，您可以使用变量、混合、嵌套等。不要认为 Sass 是用 Javascript 而不是 Ruby 实现的。如果你有兴趣了解更多关于 Less 的信息，看看[lesscss.org](http://lesscss.org/)；Jeremy Hixon 也有一篇很棒的文章，[对 LESS 的介绍，和 Sass 的比较](http://coding.smashingmagazine.com/2011/09/09/an-introduction-to-less-and-comparison-to-sass/)，比较了两种语言。

因此，正如在 [Twitter Bootstrap 介绍页面](http://twitter.github.com/bootstrap/#less)上所解释的，在 Rails 站点中使用 Twitter Bootstrap 的最佳方式是直接使用 Twitter 团队编写的较少的源代码，而不仅仅是编译的 CSS 输出。这允许您以非常直接的方式利用或覆盖 Twitter Bootstrap 提供的样式。但是由于 Rails 3.1 资产管道不支持 Less，这就有点问题了…

## 少轨自举

幸运的是，[肯·柯林斯](http://metaskills.net/)在过去的几个月里做了一些伟大的工作来解决这个问题；他写了一个名为 [less-rails-bootstrap](https://github.com/metaskills/less-rails-bootstrap) 的新宝石，为 Rails 3.1 资产管道增加了对 less 的支持。你可以阅读他的[博客文章](http://metaskills.net/2011/09/26/less-is-more-using-twitter-bootstrap-in-the-rails-3-1-asset-pipeline/)或者查看他的 [Github 自述页面](https://github.com/metaskills/less-rails-bootstrap)了解详情。Seyhun Akyürek 开发的另一个名为 [twitter-bootstrap-rails](https://github.com/seyhunak/twitter-bootstrap-rails) 的 gem 使用了相同的方法，尽管乍一看 twitter-bootstrap-rails 似乎没有任何测试，而 Ken 的 less-rails-bootstrap 确实有一个有效的 MiniTest::Spec 测试套件。

我将在这里重复 Ken 的博文中的设置步骤；首先，只需将 less-rails-bootstrap(或 twitter-bootstrap-rails)添加到您的 Gemfile 的:assets 组中:

```
group :assets do
  gem 'less-rails-bootstrap'
end
```

并使用“捆绑安装”来安装它然后，您需要做的就是从 app/assets/样式表/application.css 文件中获取 Twitter CSS 代码，如下所示:

```
/*
 *= require twitter/bootstrap
*/
```

正如 Ken 解释的那样，现在你可以自由地直接覆盖和操作 Twitter Bootstrap Less 代码；例如，如果您将此代码添加到扩展名为. css.less 的新文件中:

```
@import "twitter/bootstrap";

#foo {
  .border-radius(4px);
}
```

…现在您有了一个跨浏览器兼容的 CSS 样式，它将应用半径为 4 像素的圆形边框。但是这实际上是如何工作的呢？让我们仔细看看:

![](img/183832f7742a90282e2e221afb4eaef3.png)

什么宝石做什么？

在真正的 Rails 时尚中，事实证明幕后有很多“魔法”在进行。在您的应用程序中使用这种方法之前，重要的是要理解涉及到哪些宝石，以及所有这些宝石实际上为您做了什么。从上往下看，less-rails-bootstrap 是如何工作的:

*   [Less-rails-Bootstrap](https://github.com/metaskills/less-rails-bootstrap):Ken 的 gem 实际上包含了 Twitter Bootstrap Less 代码文件(在 vendor/assets 文件夹中)，并提供了一个 Rails 引擎，使您的应用程序可以访问它们。稍后会有更多相关内容…
*   less-rails:Ken written 也编写了这个 gem，它基于 Karst Hammer 的早期版本，有助于 Less 与 Rails 3.1 资产管道的集成，提供 Rails 开发人员所期望的扩展。请在评论中看到 Ken 对此的澄清。
*   less.rb :这个 gem 由 Charles Lowell 编写，是 less 语言编译器的一个薄薄的包装，允许你的 Rails 应用程序调用它。
*   这个 gem 也是由 Charles Lowell 编写的，它为 Ruby 程序，也就是你的 Rails 3.1 应用程序，提供了调用 V8 Javascript 引擎的能力。
*   libv8 :这个 gem 使得从源代码安装或编译实际的 V8 Javascript 库变得容易，它本身大部分是用 c 实现的。

涉及到这么多代码，您可能会问:less-rails-bootstrap 会让我的应用程序慢得像爬行一样吗？这里的好消息是，您只需要 Javascript V8 引擎和它上面的所有其他代码，就可以将较少的代码文件编译成 CSS。也就是说，当您编写或修改 CSS 样式时，您只需要将所有这些用于开发目的。在部署到生产环境之前，您将预编译您的. css.less 资产文件，就像您已经对其他 css 或 Javascript 文件所做的那样——并且这些 gem 实际上都不会在您的生产 web 服务器上使用。

Ken 实现的另一个重要魔术是他使用 Rails 引擎打包 Twitter 的较少代码文件的方式。这是如何工作的:

![](img/fd3b10a94d7d70a9849716e979ec0bd1.png)

少轨自举 gem

如果你不熟悉 Rails 引擎，花几分钟时间看看 Ryan Bates 解释它们是如何在 Rails 3.1 中实现的。Less-rails-bootstrap 实现了最简单的 rails 引擎，为您的 Rails 3.1 应用程序提供了对 Twitter 代码的访问，作为附加的静态资产文件。当来自用户的 HTTP 请求到来时——在本例中是您，因为您只在开发过程中使用这段代码——它被 Rails 转发到 less-rails-bootstrap gem 中的引擎。在实践中，您不需要担心这是如何工作的，但是知道 Twitter Less 代码文件的位置是很好的，以防您需要找到特定的样式定义或搜索其他内容:

```
$ cd `bundle show less-rails-bootstrap`
$ find vendor
vendor
vendor/assets
vendor/assets/javascripts
vendor/assets/javascripts/twitter
vendor/assets/javascripts/twitter/bootstrap
vendor/assets/javascripts/twitter/bootstrap/alerts.js
vendor/assets/javascripts/twitter/bootstrap/buttons.js
vendor/assets/javascripts/twitter/bootstrap/dropdown.js
vendor/assets/javascripts/twitter/bootstrap/modal.js
vendor/assets/javascripts/twitter/bootstrap/popover.js
vendor/assets/javascripts/twitter/bootstrap/scrollspy.js
vendor/assets/javascripts/twitter/bootstrap/tabs.js
vendor/assets/javascripts/twitter/bootstrap/twipsy.js
vendor/assets/javascripts/twitter/bootstrap.js
vendor/assets/stylesheets
vendor/assets/stylesheets/twitter
vendor/assets/stylesheets/twitter/bootstrap.css.less
vendor/frameworks
vendor/frameworks/twitter
vendor/frameworks/twitter/bootstrap
vendor/frameworks/twitter/bootstrap/bootstrap.less
vendor/frameworks/twitter/bootstrap/forms.less
vendor/frameworks/twitter/bootstrap/mixins.less
vendor/frameworks/twitter/bootstrap/patterns.less
vendor/frameworks/twitter/bootstrap/reset.less
vendor/frameworks/twitter/bootstrap/scaffolding.less
vendor/frameworks/twitter/bootstrap/tables.less
vendor/frameworks/twitter/bootstrap/type.less
vendor/frameworks/twitter/bootstrap/variables.less
vendor/frameworks/twitter/bootstrap.less
```

## 萨斯-推特-自举

虽然 Ken 在 less-rails-bootstrap 方面做得很好，但您可能更喜欢使用 Sass 而不是 less，因为它已经得到了 Rails 3.1 的现成支持，或者因为您的应用程序已经包含了大量的 Sass 代码，或者可能只是因为您对它更熟悉。乍一看，这对 Twitter Bootstrap 来说似乎是一个严重的问题:它不适合喜欢 Sass 的大部分 Rails 社区，因为它是用以 Javascript 为中心的 Less.js 技术实现的。

不要担心——其他一些伟大的开发人员(约翰·朗、杰瑞米·海因加德纳和其他人)已经遇到了这个困境，并提出了一个解决方案:他们将 Twitter Bootstrap 的 Less 代码翻译成 Sass，并在 Github 上作为一个单独的库发布，名为 [Sass-twitter-bootstrap](https://github.com/jlong/sass-twitter-bootstrap) 。

Sass-twitter-bootstrap 不是宝石，而是包含 twitter 翻译代码的 github repo。要在您的 Rails 3.1 应用程序中使用它，只需克隆 repo 并将引导 css 文件复制到您的应用程序中，如下所示:

```
$ git clone https://github.com/jlong/sass-twitter-bootstrap.git
$ cp sass-twitter-bootstrap/bootstrap.css path/to/app/assets/stylesheets/.
```

当然，这与直接从 Twitter Bootstrap repo 复制 bootstrap.css 文件没有什么不同；相反，您应该做的是将 Sass 源文件复制到您的应用程序中，如下所示:

```
$ rm path/to/app/assets/stylesheets/bootstrap.css
$ cp -r sass-twitter-bootstrap/lib path/to/app/assets/stylesheets/twitter
```

现在，由于 Rails 3.1 资产管道支持 Sass 开箱即用，我们可以开始了…几乎！如果你现在运行你的应用，你会看到一个错误:

```
ActionView::Template::Error (Undefined variable: "$baseline".
  (in /path/to/app/assets/stylesheets/twitter/forms.scss))
```

多亏了 Brent Collier，这个问题有了一个简单的解决方案:application.css 中的代码默认使用“require_tree”来包含 app/assets/样式表下的所有代码:

```
/*
 * This is a manifest file that'll automatically include all the stylesheets available in this directory
 * and any sub-directories. You're free to add application-wide styles to this file and they'll appear at
 * the top of the compiled file, but it's generally better to create a new file per style scope.
 *= require_self
 *= require_tree .
*/
```

正如 Brent 解释的那样，这样做的问题是 Twitter Bootstrap 的 Less 代码(和翻译后的 Sass 版本)被设计为使用 bootstrap.scss 文件包含一次，而 Bootstrap . scss 文件又包含所有其他文件。Brent 的解决方案非常完美:只需删除“require_tree”并直接要求 bootstrap.scss:

```
/*
 * This is a manifest file that'll automatically include all the stylesheets available in this directory
 * and any sub-directories. You're free to add application-wide styles to this file and they'll appear at
 * the top of the compiled file, but it's generally better to create a new file per style scope.
 *= require_self
 *= require twitter/bootstrap
*/
```

从概念上讲，这种设置如下所示:

![](img/1a070ece5252a2a11c28b095ae675685.png)

复制 sass 文件

这样做的好处是，现在您已经有了 Sass 代码，可以直接在 Rails 3.1 应用程序中实现 Twitter Bootstrap 了！这意味着您可以继续使用、覆盖或修改 Sass 来满足您的需求。

这里明显的缺点是涉及到手动复制过程。您需要将 Sass 代码文件复制到您的 Rails 应用程序中一次，然后每次 Twitter 发布更新、更好的 Twitter Bootstrap 版本时，您都必须重复复制。虽然这不会每天都发生，但为什么要让自己陷入持续的维护问题呢？

另一个缺点是，你使用的是二手的、翻译过的 Twitter 代码版本，你必须相信维护 sass-twitter-bootstrap 的人能够准确地将 Less 翻译成 sass。

## 引导-sass 和引导-rails

像 Rails 世界中的所有东西一样，总有其他好的解决方案可供你参考，在这种情况下，Thomas McDonald 的 [bootstrap-sass gem](https://github.com/thomas-mcdonald/bootstrap-sass) 和 [AnjlLab 的](http://anjlab.com/) [bootstrap-rails gem](https://github.com/anjlab/bootstrap-rails) 。这两种 gem 都将 Sass 翻译方法与 Rails 引擎相结合，避免了令人头疼的手动副本维护。

使用 bootstrap-sass(或 bootstrap-rails)非常简单，只需将其添加到您的 Gemfile 中即可:

```
group :assets do
  gem 'bootstrap-sass'
end
```

运行“捆绑安装”，然后向 app/assets/style sheets/application . CSS 添加 require 语句:

```
/*
 *= require bootstrap
*/
```

与 less-rails-bootstrap 类似，这是通过让 rails 3.1 从 gem 内部的 Rails 引擎加载 Sass 代码来实现的:

![](img/059c54cd7d9986faf4cad3cd53744adb.png)

自举 sass 和 rails

因此，没有必要下载和复制 Twitter Bootstrap Less 代码或其翻译的 Sass 版本。随着 Twitter Bootstrap 新版本的发布，托马斯·麦克唐纳可能会将 Less 代码重新翻译成 Sass，并重新发布他的 gem。然后你就可以运行“捆绑更新”来把新的 Twitter 代码放到你的应用程序中了！

## 其他选项

令人惊讶的是，集成 Rails 3.1 和 Twitter Bootstrap 还有更多选择，我今天没有时间介绍:

*   compass-twitter-bootstrap 类似于 bootstrap-sass 和 bootstrap-rails，使用 rails 引擎来提供 twitter 引导代码的翻译版本，但改为面向 [Compass CSS 框架](http://compass-style.org)。
*   [Twitter _ Bootstrap _ form_for](https://github.com/stouset/twitter_bootstrap_form_for)实现了一个自定义的 Form Builder 对象，在你的视图中由 Form _ for 生成的对象，专门为 Twitter Bootstrap 设计。
*   [css-bootstrap-rails](https://github.com/arunagw/css-bootstrap-rails) 类似于 bootstrap-sass 和 bootstrap-rails，但是使用 rails 引擎只包含 bootstrap.css，没有任何 Less 或 sass 文件。

## 一个六个，另一个半打

所有这些方法都同样有效:

*   通过 Rails 引擎使用原始的 Less 代码(less-rails-bootstrap 或 twitter-bootstrap-rails)
*   将翻译后的 Sass 代码直接复制到您的应用程序中(sass-twitter-bootstrap)，或者
*   通过 Rails 引擎使用翻译后的 Sass 版本(bootstrap-sass 或 bootstrap-rails)。

Less 和 Sass 非常相似，使用哪种语言真的是个人喜好问题。类似地，使用 Rails 引擎是将 Twitter 引导代码包含在 Rails 3.1 应用程序中并顺利管理升级的一种便捷方式。然而，使用 Rails 引擎为您的应用程序增加了一些额外的“魔力”,当您需要查找、检查或搜索 Sass 代码时，可能会有些混乱。将 Sass 代码直接复制到你的应用程序中有一种简单优雅的方法，你可能不需要经常更新到 Twitter Bootstrap 的新版本。

采取哪种方式由你决定！

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start Rails](https://learnable.com/books/jump-start-rails?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对这篇文章的评论已经关闭。有关于 Ruby on Rails 的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?227-Ruby-amp-Rails?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章