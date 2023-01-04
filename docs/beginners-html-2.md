# 初学者的 HTML–第 2 部分

> 原文：<https://www.sitepoint.com/beginners-html-2/>

在这个系列教程的第二部分，我们将了解如何创建到其他网页的链接，以及到我们自己页面其他部分的链接。我们的主页现在开始看起来有点杂乱，所以我们将制作一个全新的网页，其中将包含一些我们最喜欢的网站的链接。

##### 基本链接

首先，在文本编辑器中打开一个新的空白页，然后填充我们在第一部分中看到的基本起始 shell。为页面设置标题:

```
<html> 

  <head> 

    <title>My Favorite Web Sites</title> 

  </head> 

  <body> 

    content 

  </body> 

</html>
```

接下来，让我们添加一些格式和一些链接:

```
<html> 

  <head> 

    <title>My Favorite Web Sites</title> 

  </head> 

  <body> 

    <font size="+2"> 

      My Favorite Web Sites 

    </font> 

    <p> 

      SitePoint - http://www.sitepoint.com <br> 

      Google - http://www.google.com <br> 

      AltaVista - http://www.altavista.com <br> 

      Slashdot - http://www.slashdot.com 

    </p> 

  </body> 

</html>
```

现在，这个页面很好，但是如果一个用户想要访问你所列出的网站，他们将不得不在他们的浏览器中重新输入地址或者剪切并粘贴 URL。有一种更好的方法，那就是使用`<a>`标签。`<a>`标签的语法如下:

```
<a href="URL">TEXT TO DISPLAY</a>
```

您需要将上面的“URL”替换为该站点的完整地址，包括 http://，然后将“要显示的文本”替换为您希望用户点击以转到该 URL 的文本。一旦我们将这段代码添加到我们的页面中，它将看起来像:

```
<html> 

  <head> 

    <title>My Favorite Web Sites</title> 

  </head> 

    <body> 

      <font size="+2"> 

        My Favorite Web Sites 

      </font> 

    <p> 

      <a href="http://www.sitepoint.com">SitePoint</a><br> 

      <a href="http://www.google.com">Google</a> <br> 

      <a href="http://www.altavista.com">AltaVista</a> <br> 

      <a href="http://www.slashdot.com">SlashDot</a>  

    </p> 

  </body> 

</html>
```

现在，用户只需点击网站的标题(如 SitePoint)，就能立即被传送到该网站。

##### 组织链接

目前，我们的页面看起来相当不错。然而，一旦你添加了更多的链接，用户浏览页面并找到他们想要的链接就会变得更加困难。所以让我们把链接分成几类(你会注意到我已经在下面的列表中添加了一些链接):

```
<html>  

  <head>  

    <title>My Favorite Web Sites</title>  

  </head>  

  <body>  

    <font size="+2">  

      My Favorite Web Sites  

    </font>  

    <p>  

      <i>  

        Web Development <br>  

        Search Engines <br>  

        Miscellaneous  

      </i>  

    </p>  

    <p>  

      <b>Web Development</b><br>  

      <a href="http://www.sitepoint.com">SitePoint</a><br>  

      <a href="http://www.webmasterbase.com">WebmasterBase</a><br>  

      <a href="http://www.promotionbase.com">PromotionBase</a><br>  

      <a href="http://www.ecommercebase.com">eCommerceBase</a><br>  

      <a href="http://www.servicesbase.com">ServicesBase</a>  

    </p>  

    <p>  

      <b>Search Engines </b><br>  

        <a href="http://www.google.com">Google</a> <br>  

        <a href="http://www.altavista.com">AltaVista</a> <br>  

        <a href="http://www.yahoo.com">Yahoo</a><br>  

        <a href="http://www.dmoz.org">Open Directory Project (DMOZ) </a>  

      </p>  

      <p>  

        <b>Miscellaneous </b><br>  

        <a href="http://www.slashdot.com">SlashDot</a> <br>  

        <a href="http://www.theregister.co.uk">The Register</a> <br>  

        <a href="http://www.f-free.net">F-Free</a>  

      </p>  

  </body>  

</html>
```

如你所见，我还在页面顶部包含了所有类别的列表，以帮助用户找到他们想要的类别。

目前，导航链接页面非常容易。但是随着我们链接列表的增长，访问者越来越难找到他们想要的东西。幸运的是，`<a>`标签的另一个功能允许我们链接到同一文档的其他部分。它对用户来说就像任何其他链接一样，但是允许他们移动到同一页面上的另一个位置，或者移动到同一站点内另一个页面上的一个位置。

这个标签有两个主要元素。

***指针***

首先，我们必须在页面上我们想要链接的位置放置一个指针。指针是:

```
<a name="myposition"></a>
```

您在标记中指定的名称将允许您通过普通链接导航到页面的该区域。这不会改变页面向用户显示的方式；看起来和以前一模一样。

**链接 **

下一步是创建指向这个指针的链接。指向页面特定区域的链接的工作方式如下:

```
<a href="http://www.mysite.com/index.html#myposition">click here</a>
```

在我们的特殊例子中，由于目的地和链接本身在同一个页面上，所以不需要指定完整的 URL 只需指定`#myposition`部分就足够了。

因此，一旦类别名称被制作成链接，我们的页面将如下所示:

```
<html>  

  <head>  

    <title>My Favorite Web Sites</title>  

  </head>  

    <body>  

      <font size="+2">  

         My Favorite Web Sites  

      </font>  

      <p>  

        <i>  

          <a href="#webdev">Web Development</a><br>  

          <a href="#search">Search Engines</a><br>  

          <a href="#misc">Miscellaneous</a>  

        </i>  

      </p>  

      <p>  

        <b><a name="webdev"></a>Web Development</b><br>  

        <a href="http://www.sitepoint.com">SitePoint</a><br>  

        <a href="http://www.webmasterbase.com">WebmasterBase</a><br>  

        <a href="http://www.promotionbase.com">PromotionBase</a><br>  

        <a href="http://www.ecommercebase.com">eCommerceBase</a><br>  

        <a href="http://www.servicesbase.com">ServicesBase</a>  

      </p>  

      <p>  

        <b><a name="search"></a>Search Engines</b><br>  

        <a href="http://www.google.com">Google</a><br>  

        <a href="http://www.altavista.com">AltaVista</a><br>  

        <a href="http://www.yahoo.com">Yahoo</a><br>  

        <a href="http://www.dmoz.org">Open Directory Project (DMOZ)</a>  

      </p>  

      <p>  

        <b><a name="misc"></a>Miscellaneous</b><br>  

        <a href="http://www.slashdot.com">SlashDot</a><br>  

        <a href="http://www.theregister.co.uk">The Register</a><br>  

        <a href="http://www.f-free.net">F-Free</a>  

      </p>  

    </body>  

</html>
```

现在，如果你访问一个特定类别的链接，你将被向下移动到该类别的页面，而不是一个单独的页面。请记住，如果您在高分辨率显示器上查看页面，这个示例可能不会出现，因为如果所有链接都显示在一个页面上，屏幕不会向下滚动。然而，如果页面上有更多的链接，或者你缩小了你的浏览器窗口，那么你会看到链接做他们的事情。

##### 电子邮件链接

还有一种类型的联系我们还没有看到。此链接能够打开访问者的电子邮件客户端，并允许他们向您发送消息。该标记与我们之前看到的相似:

```
<a href="mailto:you@email.com">Click here to email me!</a>
```

通常被称为“Mailto 链接”,它提供了一种快速简单的方式让访问者给你发送电子邮件，也许是关于你的网站的评论或建议，或者只是打个招呼！

##### 向您的站点添加图像

现在你已经创建了一个可以引导访问者到你最喜欢的网站的页面，那么一个带有几张图片的页面怎么样呢？它们可以是你的，你的房子，你的车——任何你想要的东西。对于本页的例子，我假设这些照片是我的房子，但是当然你的照片可以是任何东西。为了避免页面混乱，我将为此另开一个空白页。因此，让我们在你的文本编辑器中打开一个空白页面，并将其命名为 pics.html。填写我们之前看到的空白页轮廓。现在我们准备好了。

就像 HTML 中的所有其他东西一样，图像也使用标签。图像的标签是`<img>`。这个标签有很多属性，但是现在我们只看两个:

```
src="images/house.jpg"
```

`src`属性指定图像的位置。这可能是一个 URL，或者是本地网站上的一个路径，就像我在上面的例子中包含的那样。例如，“images/house.jpg”表示我们希望显示存储在 house.jpg 的图像，这个文件可以在包含该页面的 HTML 文件的目录的 images 子目录中找到。

```
alt="My House"
```

`alt`属性为图像提供了一个可以在许多情况下使用的描述。使用 alt 属性是可选的，但是这样做有很多好的理由，我们将在后面看到。

现在，让我们将几张图片放入一个 HTML 页面:

```
<html>   

  <head>   

    <title>   

      Pictures of My House   

    </title>   

  </head>   

  <body>   

    <p>   

      <font size="+2">   

        <b>   

          Pictures of My House   

        </b>   

      </font>   

    </p>   

    <p>   

      <img src="images/house1.jpg" alt="My Front Door">   

      <br>   

      <i>My Front Door</i>   

    </p>   

    <p>   

      <img src="images/house2.jpg" alt="My Back Yard">   

      <br>   

      <i>My Back Yard</i>   

    </p>   

  </body>   

</html>
```

正如你所看到的，我在这里只包含了两张图片，但是你可以在你的页面上包含多少张图片是没有限制的。

***为什么要费心使用 alt 属性？***

您可能想知道为什么人们使用 alt 属性，如果它不是必需的，并且当您查看您的页面时，它似乎不做任何事情。事实上，使用 alt 属性有很多好处。

首先，如果你的图像被删除、损坏或移动，那么用 alt 属性设置的描述将会显示在它的位置上。这将至少给观众一些应该出现的图像的描述。

第二，这意味着你的网站对残疾人更友好，因为许多这些访问者使用的纯文本浏览器和文本到语音转换系统不会拾取图像，但会识别 alt 属性。

此外，当您将鼠标移到图像上时，最近的浏览器(如 Microsoft Internet Explorer)将显示 alt 属性中提供的描述。也许唯一的缺点是包含 alt attrubutes 会使页面的文件大小稍微大一点；然而，速度上的差异可以忽略不计。我当然看不出有什么真正的理由不使用 alt 标签。

***更多属性为图像标签***

image 标签使用了大量可选但有用的属性。以下是几个例子:

```
border="n"
```

这将在图像周围放置一个边框。“n”以像素为单位指定边框的宽度。这在您将图像制作成链接时特别有用，因为默认情况下，这些图像周围会显示难看的彩色边框。

```
height="n"
```

这告诉浏览器图像的高度，以像素为单位。使用该属性是为了在处理和加载页面时，浏览器将为图像保留适量的空间。因此，您的页面布局将始终保持完美——即使图像仍在加载。

```
width="n"
```

该属性的作用与 height 属性相同，只是它显然定义了图像宽度。同样，应该以像素为单位指定。

还有更多的属性，但这些会给你一个好的开始。

##### 挑战

现在你已经知道了一些基本的 HTML，是时候接受挑战了！我们目前有 3 个页面:

*   主页
*   链接页面
*   图片页面

然而，目前没有办法在它们之间导航。为了纠正这一点，我们需要打开主页，并创建将用户带到其他两个页面的链接。在其中的每一页上，都应该有一个返回主页的链接。万一你被卡住了，这里有一个提示:你需要使用我们在本部分教程开始时学过的`<a>`标签。

该解决方案将在本系列的下一部分中提供，届时我们还将看看 HTML 布局函数中最有用的一个——表格。

## 分享这篇文章