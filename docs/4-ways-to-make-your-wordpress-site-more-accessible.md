# 让你的 WordPress 网站更易访问的 4 种方法

> 原文：<https://www.sitepoint.com/4-ways-to-make-your-wordpress-site-more-accessible/>

大部分时间，我的写作侧重于搜索引擎优化技术。那么，我为什么要在 WordPress 中写可访问性呢？

因为，在很大程度上，为搜索引擎优化网站和为残疾人访问优化网站是一回事。允许搜索机器人正确索引网站的技术也允许屏幕阅读器向有视觉障碍的用户正确描述内容。

类似地，适当地构建页面会使其对机器友好，不管那台机器是什么。添加一些常识性的技术，让用户(包括残疾用户)和机器可以直接找到有意义的内容，你就可以让你的网站变得既容易访问又对搜索引擎友好。

我们都知道 WordPress 是一个非凡的工具，它允许人们用很少的编程或编码知识来建立博客和网站。这个平台很容易建立，并为主题和插件提供了一个坚实的框架，它将决定你的内容和功能如何呈现。

如果你希望你的网站对残疾人来说是可访问的，你会发现 basic WordPress 有很多功能来帮助开发一个可访问的网站，并且通过添加插件和良好的技术来进一步发展并不难。

在我们开始之前，有两个重要的注意事项。首先，单独实施这四项技术不会使任何网站完全可访问，以达到符合 [WCAG](https://www.w3.org/TR/2008/REC-WCAG20-20081211/) 或[第 508 条](https://www.section508.gov/)的所有要求的程度，但它将解决网络访问的最常见障碍，并提供更好的可访问性。

其次，重要的是要明白，可访问性只能通过设计、开发和内容创作实践的合作来实现。设计者可能会设计一个可访问的主题，开发人员可能会添加可访问的功能，但是如果内容作者插入了一个没有 alt 标记的有意义的图像，则该网页的完整含义将是不可访问的。

考虑到这一点，以下是我的四个让 WordPress 网站更易访问的建议。

## 用替代文本标记您的图像

众所周知，网页可访问性的最大障碍是正确使用替代文本来描述网页上的图片。如果每个建立网站的人都注意到这一点，就能消除大量的无障碍投诉。

要理解的基本要点是，如果你要把图片放在你的网站上，用替换文字正确地标记它们是很重要的。换句话说，网页上每个有意义的图像都必须有一个 alt 标签。

alt 标签中的信息允许屏幕阅读器(为有视觉障碍的用户朗读网页)、纯文本浏览器和不加载图像的慢速连接的用户理解图像所传达的信息。

如果一幅图像没有意义，例如它纯粹是用来装饰的，有两种方法可以处理它。如果使用 CSS 将它作为背景图像加载，屏幕阅读器会忽略它，并且该图像不需要 alt 标记(注意，因为这也适用于有意义的图像:如果在后台加载，屏幕阅读器会忽略它)。如果一个图像使用 HTML 显示，但纯粹是装饰性的，那么使用 alt 标签，但将其留空。这告诉屏幕阅读器图像是存在的，但对用户没有意义，可以忽略。

当你给你的图片加标签时，使用描述性的文字，让看不见的人明白图片的意思。这需要判断:图像要传达什么意思，什么词最能传达同样的意思。不要拘泥于每一个视觉细节的文字描述，传达出其中的含义。

一个经典的例子是一个放大镜的图像，用作开始搜索的按钮。形象是有意义的，所以要描述。将它描述为“放大镜”对用户没有帮助。他们需要知道的是，点击这个按钮将开始搜索，所以“点击这里搜索”的替代文本，甚至只是“搜索”可能是最合适的。

只有服务于一个目的的图像必须被标记。这些例子如下:

*   用作按钮的图像
*   用作链接的图像
*   用于任何其他控件的图像
*   与内容直接相关的图像

这个技巧适用于所有的网站，当然，不仅仅是 WordPress 网站。然而，WordPress 比大多数网站都要简单，首先，它允许内容作者在可视和 HTML 视图之间切换，其次，每当使用添加媒体功能插入图像时，它都提供一个替代文本字段。

## 使用插件来增强可访问性

使用各种插件可以增强 WordPress 的可访问性。这些插件很容易设置，它们将使各种残疾的用户能够访问网站。这里有一些我觉得有用的插件。

### [访问键](http://wordpress.org/extend/plugins/access-keys/)

访问键插件允许将访问键分配给网站上的链接和控件。例如，主页链接可以被分配访问键组合 alt+h，而搜索按钮可以被分配 alt+s 键组合。访问键，如所提供的示例，使视力受损的用户以及行动不便的用户能够轻松访问他们想要的内容。

### [轻松转发](http://wordpress.org/extend/plugins/easy-retweet/)

Easy Retweet 是一个插件，允许你在页面上添加可访问的按钮，以方便在 Twitter 上分享你的内容。这样，如果残疾人希望与他们的朋友共享您的页面，他们可以毫无困难地这样做。

### [压舱物增压器](http://wordpress.org/extend/plugins/astickypostorderer/)

AstickyPostOrderER 允许您选择内容的显示顺序。您可以选择按从旧到新或相反的顺序显示内容。

### [黑客搜索引擎优化目录](http://wordpress.org/extend/plugins/hackadelic-table-of-content-boxes/)

Hackadelic SEO 目录插件允许你为你的文章或页面提供目录。这将使你的内容容易找到。

### [WP-Polls](http://wordpress.org/extend/plugins/wp-polls/)

WP-Polls 允许残疾人参与您网站上的任何投票

新的插件一直在开发。使用 WordPress 仪表盘功能搜索更多信息。尝试不同的插件，并让残疾用户给你反馈

当使用任何插件时，确保插件的使用与你选择的主题兼容，并且彼此兼容。如果你的插件与你的主题不兼容，它们要么不能工作，要么会给试图浏览你的网站的残疾用户带来更多的问题。如果你的插件彼此不兼容，你的网站可能会变得不可用或者内容显示不正确。

## 正确使用标题

当你设计网页时，按照正确的顺序使用标题来组织内容是至关重要的:`<h1>`、`<h2>`、`<h3>`等等。当您使用标题组织内容时，使用屏幕阅读器的用户可以获得页面内容的摘要，并更容易地浏览它们。屏幕阅读器用户可以通过按字母“H”前进和“shift+H”后退来访问他们想要的内容部分。

您可以使用标题一到六来标记您的内容。检查你的主题如何使用标题(例如，你的主题可能会也可能不会将`<h1>`应用于网站标题，网站标题可能会也可能不会出现在每个网页上。无论哪种方式，确保使用的第一个标题是`<h1>`，随后的标题(页面标题、页面小节、段落标题)按顺序下降到`<h6>`，如果合适的话。

排序有一定的灵活性，但最明智的选择是保持标题的数字顺序，不要混淆(`<h1>`、`<h4>`、`<h2>`等)。

```
<html> 
<head> 
    <title>Must See Horror Movies</title> 
</head>   

<body> 
    <h1>Horror Movies of the Decade </h1> 
    (This is the title of the page that will be assigned a header by default in most WordPress themes. 
    This header is always assigned to h1\. Structure the rest of the content to follow what has been started)   

    <h2>Horror Movies for 2012</h2> 
    (The list of 2012 horror movies will go here)   

    <h3>2012 Cult Movies</h3> 
    (a sub-list of 2012 movies)   

    <h3>2012 Sci Fi Horror Movies</h3> 
    (another sub-list of 2012 movies)   

    <h2>Horror Movies for 2011</h2>
    (The list of 2011 horror movies goes here)   

    <h2>Horror Movies for 2010</h2> 
    (The list of 2010 horror movies goes here)   

    <h2>Horror Movies for 2009</h2> 
    (The list of 2009 horror movies goes here)

    <h2>Horror Movies for 2008</h2> 
    (The list of 2008 horror movies goes here)   

    <h4>Why Use Our Horror Movies Reference </h4> 
    (This gets a new, lower header level due to the fact that this section of the page comes after those 
    listing the movies, and it is not as important as the movie listings) 
</body> 
</html>
```

## 使用导航链接

最后，你需要在你的网站设计中有导航链接。这些导航链接将允许残疾用户跳过导航栏或其他菜单和搜索框，并通过单击这些链接跳转到页面上的特定位置。或者，用户可以快速跳转到站点上想要的部分。这些链接需要出现在任何需要用户跳转到有意义内容的地方。这些链接可能会说的例子有:

*   跳到主要内容
*   跳转到导航栏
*   跳转到搜索

一些 WordPress 主题可能已经提供了这样的链接。如果是这种情况，你需要查看这些链接，看看你是否需要添加自己的链接。然而，其他主题将不提供导航链接。因此，您需要自己添加这些链接。

## 结论

我要再次强调，这些都不能保证你的网站 100%可访问。

然而，如果你采用了这四种技术，那么你可以指望你的 WordPress 站点比 90%的 WordPress 站点更容易访问，并且你已经解决了 90%的人指出的网络不可访问性的问题。

残障人士将能够更好地访问您的网站，以及您的所有其他访问者。由于这些访问者也包括搜索机器人，你可以放心，使您的网站无障碍也将有利于您的商业模式，并有助于最大限度地提高您的盈利潜力。

## 分享这篇文章