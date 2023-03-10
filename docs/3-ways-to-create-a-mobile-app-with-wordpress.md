# 用 WordPress 创建移动应用程序的 3 种方法

> 原文：<https://www.sitepoint.com/3-ways-to-create-a-mobile-app-with-wordpress/>

SitePoint 之前已经讨论过 WordPress 驱动的移动应用程序的话题，然而，在这篇文章中，我们将深入探讨三个可用的选项，讨论每个选项的优缺点；App Builders、定制混合应用和 AppPresser(我是其中的联合创始人)。

超过 24%的互联网用户使用 WordPress，其中许多人想要一个移动应用。

在应用程序中使用 WordPress 的内容非常有意义，这样当网站更新时，应用程序也会更新。例如，如果你是一家在线杂志，你会希望你发布的任何新文章都可以在应用程序中立即看到。如果您对网站进行了任何更改，您也希望这些更改无需任何额外工作即可应用到应用程序中。

一些应用程序构建者使用第三方内容管理系统来创建应用程序内容。如果你有一个 WordPress 网站，为什么不用 WordPress 作为 CMS 呢？

WordPress 用户习惯于使用插件和主题来定制他们的网站，而这些并不总是能很好地转化为移动应用。当客户想要在移动应用程序中模仿他们的 WordPress 网站时，这给开发者带来了一些独特的挑战。例如，如果一个客户使用 BuddyPress 在他们的网站上创建一个社交网络，并且他们想要一个具有相同体验的移动应用程序，这如何实现呢？对于一个事件或电子商务插件来说，同样的情况会怎样？

移动应用的技术限制意味着整合 WordPress 插件等网站功能通常是一项艰巨的任务。幸运的是，有一些选项可以让 WordPress 成为一个适合任何项目的移动应用。让我们来看看其中的三个。对于其中的每一种方法，我都将介绍使用混合应用技术和 PhoneGap 用 WordPress 构建一个移动应用(Narayan Prusty 在这里也介绍了 [WordPress 和 PhoneGap](https://www.sitepoint.com/building-a-phonegap-app-with-a-wordpress-backend/))。

## 应用构建者

应用程序构建器允许你创建一个移动应用程序，不需要任何编码，也不需要什么专业技术。

如果你不想花很多钱，并且你不需要任何真正定制的东西，它们可以很好地工作。有各种各样的 AppBuilder，包括 [Mobile Roadie](http://mobileroadie.com/) 、 [Shoutem](http://www.shoutem.com) 、 [Good Barber](http://goodbarber.com) 、 [Reactor](http://reactorapps.io) (使用 AppPresser 的) [Telerik AppBuilder](http://www.telerik.com/appbuilder) 。每一个都有不同程度的 WordPress 支持，大部分允许你整合文章和评论，但是其他的就不多了。

例如，Shoutem 允许你通过在你的 WordPress 站点上安装插件来获取文章。这让你可以显示你的 WordPress 内容并接受对你的应用的评论。

![Shoutem Example](img/3277374b4efafd949d55991b336ee5db.png)

这与大多数应用构建器类似，除了 Reactor 允许集成一些插件。如果你是 web 开发人员，你可以使用更高级的工具，比如 Telerik AppBuilder，大多数公司都有开发人员 API。

### 赞成的意见

如果你的应用程序中只需要 WordPress 的内容，并且你是非技术人员，那么应用构建器是一个很好的选择。

对于不想雇佣开发人员的非技术人员来说，应用程序构建者可能是唯一的选择。

### 骗局

如果你需要对你的应用程序进行定制，或者你希望你的应用程序中有 WordPress 插件，应用程序构建器不是最好的选择。

让我们看看用 WordPress 制作应用程序的其他几种方法。

## 定制混合应用程序

第一种方法是使用诸如 [Ionic Framework](http://ionicframework.com) 和 [WP-API](https://www.sitepoint.com/wp-api/) 等技术创建一个定制的混合应用。

传统的混合应用程序使用 HTML、CSS 和 JavaScript 等 web 技术。然后用 [PhoneGap](http://phonegap.com) 包装它，以允许访问原生设备功能，并为应用商店编译。

你可以把它想象成一个可以通过 API 访问 WordPress 内容的单页 web 应用程序。这就是 WP-API 的用武之地。

WP-API 是为 WordPress 核心提出的新特性。这是一个用于 WordPress 的 JSON REST API，这意味着它使你的大部分内容在外部应用程序中易于使用，比如移动应用程序。它还允许双向交流，例如从移动应用程序创建帖子和批准评论。

WP-API 使得将 WordPress 内容放入我们的移动应用变得容易。要使用它，你只需要在你的 WordPress 网站上安装并激活 [WP-API 插件](https://wordpress.org/plugins/rest-api/)。激活插件后，你可以通过访问一个端点如`http://yoursite.com/wp-json/wp/v2/posts`来查看你的 API 数据。

在那里你可以看到你在 JSON 上的帖子，然后我们可以把这些内容放到我们的移动应用中。例如，我们可以发送一个 AJAX 请求来获取 jQuery 中的所有 post 数据，如下所示:

```
 <ul id="app"></div>

  <script>
  jQuery(document).ready(function($) {
    $.get( "http://scottbolinger.com/wp-json/wp/v2/posts", function( data ) {
        $.each( data, function( i, val ) {
            $( "#app" ).append(
                '<li>' + 
                    '<h3>' +
                    val.title.rendered + 
                    '</h3>' +
                    '<p>' +
                    val.excerpt.rendered +
                    '</p>' +
                '</li>'
            );
        });
    });
  });
  </script> 
```

如果您将该代码添加到 HTML 文件中，并在 web 浏览器中加载它，您应该会看到类似下图的内容。如果你的站点上安装了 [WP-API v2 插件](https://wordpress.org/plugins/rest-api/),你可以将 URL 改为你自己的站点。

![Working With Custom Post Types WP API v2](img/d75db43e0f9d1b9d620831fdf7822722.png)

￼That's 举了一个非常简单的例子，除此之外，你还可以使用移动应用框架，比如 Ionic。这将允许你更容易地创建看起来像移动应用的东西。开发混合应用时，可以使用 HTML 和 JavaScript 等 web 技术来制作应用。使用 PhoneGap，您可以将应用程序包装在您的 web 代码周围，将其转换为 iOS 和 Android 应用程序商店的原生应用程序。

关于用 Ionic 和 WP-API 创建移动应用的深入文章，见[这篇文章](http://scottbolinger.com/ionic-wordpress-app/)或[这篇文章](https://www.sitepoint.com/wordpress-hybrid-client-wordpress-powered-ios-android-apps/)。

### 这种方法的优点

使用这种传统的混合移动应用程序方法，您可以创建一个快速的应用程序，允许原生转换、缓存、离线等。你可以创建一个完全或部分离线工作的应用程序，集成多个 API(不仅仅是 WordPress)，并访问设备功能，如摄像头和推送通知。

### 这种方法的缺点

这是将内容放入应用程序的一个很好的方法，但是 WordPress 的所有其他功能呢，比如插件？WP-API 对于拉进像帖子和页面这样的内容是很棒的，但是如果你想做别的事情，它就开始变得困难了。

许多人希望他们在网站上使用的插件可以在应用程序中使用。这很难通过 WP-API 做到，如果不是不可能的话。例如，让我们以重力形式为例。如果你的网站上有一个显示 Gravity 表单的短代码，那么表单的 HTML 标记就会传到应用程序中。您将看到输入字段和按钮，但它们不起作用。

这是因为 Gravity Forms(以及大多数插件)使用了 WordPress 网站上的脚本和 PHP 处理，这是 app 中没有的。结果是一个不起作用的表单。要使用这样的插件，您必须从头开始构建与 Gravity 表单通信的 AJAX 表单处理。尽管并非不可能，但这一特性会非常复杂。

一种解决方案是使用 iFrames 显示表单，类似于 Google Maps 或 YouTube。这在某些情况下是可行的，但是 iFrames 可能不可靠，而且它们并不适用于每个插件。

怎么才能解决这个问题？让我们看看 AppPresser，这是一种完全不同的方法。

## AppPresser

正如我们在上面看到的，你不能很容易地将 WordPress 插件和其他自定义功能加入到移动应用中。

在 Gravity Forms 示例中，您可以看到，如果没有大量的自定义代码，就不可能在我们的应用程序中处理表单。许多其他 WordPress 插件也是如此，比如电子商务插件、BuddyPress、bbPress 等等。基本上任何不仅仅是显示 HTML 的插件都不能在我们的应用中开箱即用。

[AppPresser](https://apppresser.com) 是一个[免费插件](https://wordpress.org/plugins/apppresser/)(也有一个带有额外功能的付费版本)，它通过在应用程序中加载你的整个 WordPress 网站来解决这个问题。这意味着我们可以毫无问题地使用应用程序中的几乎所有 WordPress 插件。对于许多插件来说，这是让它们在应用程序中工作的唯一方法。

我们以 BuddyPress 插件为例。BuddyPress 就像 WordPress 的“盒子里的脸书”。如果你有一个使用 BuddyPress 社交网络功能的网站，你可能想开发一个允许相同类型社交网络的应用程序。

![Activity](img/adbb0e5c6112ff9654f2ad1e4561386d.png)

其他集成包括 WooCommerce、Gravity Forms、bbPress 和数百个其他插件。

AppPresser 允许你在 WordPress 中制作应用程序，例如，你可以在一个名为[app-camera]的 WordPress 页面中添加一个短代码，当在应用程序中查看页面时，它会添加一个设备摄像头。这使得熟悉 WordPress 的人可以在没有之前的移动开发技能的情况下构建一个应用程序。

### 赞成的意见

如果你想要一款和你的 WordPress 网站一样好用的手机应用，在我看来，AppPresser 是你最好的选择。这是不需要大量定制开发就能整合大量不同插件的唯一方法。

### 骗局

AppPresser 的离线功能和设备存储有限。如果离线或速度是你主要关心的，它可能不适合你的项目。

## 哪个适合你的项目？

我们研究了三种不同的方法来构建一个集成 WordPress 网站的移动应用程序。每种方法都有利弊，每个项目都不一样。

如果你不是技术人员，并且你不想付钱给开发者，使用应用构建器可能是你唯一的选择。几乎每种类型的应用都有开发者，所以你不需要花很多钱就能做出有用的东西。然而，如果你想要大量的定制，或者深度的 WordPress 集成，你可能会失望。

使用 WP-API 和 Ionic 之类的框架创建一个定制的应用程序可以让你得到一个快速、高质量的应用程序。您几乎可以做任何您需要做的事情，包括构建离线功能、自定义设计和几乎任何您需要的功能。如果你需要集成 WordPress 插件，你基本上必须自己构建所有东西，例如用 Gravity 表单处理表单。将你的自定义代码与 WordPress 集成需要认真考虑安全性，并且需要大量的自定义代码。

如果你对一个模仿你的 WordPress 站点的应用感兴趣，并且你正在使用许多定制插件，AppPresser 可能适合你。这是让你的 WordPress 站点进入一个应用程序的一个快速且经济的方法。

每个项目都有不同的要求，有三种不同的方法来构建您的应用程序将有助于您每次都能找到最合适的方法。

## 分享这篇文章