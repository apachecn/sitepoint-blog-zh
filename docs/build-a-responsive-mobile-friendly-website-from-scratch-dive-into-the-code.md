# 从头开始构建一个响应迅速、移动友好的网站:语义 HTML

> 原文：<https://www.sitepoint.com/build-a-responsive-mobile-friendly-website-from-scratch-dive-into-the-code/>

在最后一篇文章中，正如所承诺的，我们已经开始做一些实际工作，计划和创建一个模型。现在，我们已经关注了如何响应性地构建个人项目线框思维，以及响应性设计如何适应三种目标布局:默认(PC 桌面)、写字板和智能手机。

我们已经看到了如何通过创建一个表示常见内容元素的模板，在三个主要设备类别中排列典型网站的元素。在本系列的第三部分中，我将集中精力编写 HTML 代码并处理主页的结构，而在下一部分中，我将设置第一个 CSS 样式规则来构建我们项目的初始设计。

### 第一步:HTML 代码

让我们开始通过使用 HTML 代码来构建我们网站的结构。为了避免混淆和遗漏一些步骤，我们将把布局分成三个部分，就像我们在样机设计阶段所做的那样。我们会不时地暂停在代码的某个部分，这个部分是这个单独部分所固有的。我们要面对的第一个部分当然是标题，也就是我们的模型中由以下元素组成的部分:横幅、时事通讯框和主菜单栏。现在，我们要做的第一个任务是声明 HTML5 的类型文档，它以如下方式表示:

```
<!DOCTYPE html>
```

接下来，我们必须添加必要的部分，以帮助 web 浏览器解释以下主体部分中的内容。此部分包含特定的标签、CSS 样式表和其他元素，它们保持隐藏状态，不会在浏览器中显示。这些元素一般被称为**元信息**(基本上是“关于信息的信息”)，因为它们的功能是描述页面上是什么样的信息。

第一个需要规范的元标签是“元字符集”；它定义了页面中使用的字符编码。在我们的例子中，我们将其设置为 UTF-8，这是一种可变宽度的 Unicode 格式，与基本字母数字字符的 ASCII 或纯文本兼容。通过使用 8 位 ASCII 集的“上半部分”和扩展代码，它可以处理超过一百万个独特的字符。

任何网页都必须在其标题信息中定义页面的格式，这样所有的浏览器才能确切地知道如何阅读和解释内容。否则，他们会使用他们的“最佳猜测”来显示角色，这并不总是准确的。猜测可能是错误的，这将导致向用户显示错误的字符。

我们要注意的下一项是**视窗**属性。设置视口告诉浏览器内容应该如何适合设备的屏幕，并通知浏览器该站点已针对移动设备进行了优化。比如说…

```
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

…告诉浏览器将视口设置为设备的宽度，初始比例为 1。之后，我们在标题标签中插入我们为网站选择的名称。最后，我们必须将我们的 HTML 页面链接到 **CSS 样式表**，我们将用它来定义我们主页的风格；HTML“link”元素指定了当前文档和其他文档之间的关系。有了这行代码…

```
<link rel="stylesheet" href="css/common.css" type="text/css" />
```

…我们将文档链接到公共样式表(即包含将由项目的三个版本共享的所有公共 CSS 规则的文档)，而这个…

```
<link rel="stylesheet" href="css/responsive.css" type="text/css" />
```

…调用文件，其中包含将仅应用于指定特定设备的特定规则的规范。请注意，由于我们使用 HTML5，您实际上不需要指定`type="text/css"`。

关闭头部标签并移动到**主体**部分。此时，我们必须创建两个独立的 div:一个用于**横幅**，另一个用于**新闻信箱**。

首先，除了 id 之外，对于可访问性来说，使用 WAI-ARIA 角色是一个很好的实践；通常角色描述部件和结构。结构角色作为元素的属性被添加到标记中，这使得它非常适合我们的目的。

**WAI-ARIA**(*Web Accessibility Initiative-Accessible Rich Internet Applications*)是由万维网联盟发布的技术规范，规定了如何提高网页的可访问性。它通过向动态 web 应用程序添加角色、属性和状态信息，允许网页(或网页的一部分)将自己声明为应用程序，而不是静态文档，以便残疾用户可以访问控件和内容更新。这就是所有这些规格如此重要的原因。

让我们通过构建一个**简讯选择加入表单**来继续我们的项目。首先，添加一个带有<表单>标签的表单。开始标记应该包括表单名称、可以是“query”或“post”的方法，以及表单应该将输入发送到的目的地或“action”(我用#表示它不是固定的)。

现在，添加一个带有标签的文本字段来收集电子邮件地址，并为您可能应用于表单的以下样式更改分配一个 ID 和一个类。最后，向表单添加一个提交按钮。当访问者单击 submit 按钮时，输入到表单字段中的信息将被发送到接收页面进行处理。在输入类型、名称和值字段中插入单词“submit ”,以创建一个 Submit 按钮，并关闭 form 和 div 标记。

因此，此时，您应该有以下代码:

```
<!DOCTYPE html>
<html>
   <head>
      <meta charset="UTF-8"/>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Website name</title>
      <link rel="stylesheet" href="css/common.css" type="text/css" />
      <link rel="stylesheet" href="css/responsive.css" type="text/css" />
  </head>
   <body>
      <header>
         <div id="banner" role="banner"></div>
         <div id="newsletter-box">
            <form name="newsletter-form" action="#" method="post">
               <label for="newsletter-email">Newsletter:</label>
               <input type="email" id="newsletter-email" class="newsletter-email" name="email" required="required" placeholder="Your email" />
               <input type="submit" name="submit" value="Submit" />
            </form>
         </div>
      </header>
```

让我们从布局的中心部分开始，也就是用于不同类型内容的部分。

首先，让我们创建一个经典的**菜单**，由五个选项组成，这五个选项将成为整个网站的主要类别:主页、新闻、图形、移动和网页设计。在这种情况下，最适合我们目的的元素是 **nav** 元素，这是一个语义元素，用于声明网站的导航部分(即链接到其他页面或页面内部分的页面部分)。

在 nav 标签中，让我们添加一个无序列表(我们将为其分配一个名为“menu-item”的类)。对于内容部分，让我们创建一个 ID 为“content”的主 div，一个包装器 div，并在其中放置一篇文章。 **article** 元素是一种专门的节；作为页面的一个独立、自包含的部分，它具有更具体的语义。在我们的示例中，它将用于包含我们网站目的的描述。

至于包装器 div，它将包含另外三个元素:一个包含关于作者的所有信息的 div(如果您愿意，还可以包含一张照片)，一个作者可以添加他的截屏视频的侧边，以及另一个用于他的最新推文的侧边。在元素旁边的**上有两点:用于切向相关的内容。然而，在对这一新要素的解释中，对于如何使用它存在一些混乱；请记住，仅仅因为一些内容出现在主要内容的左侧或右侧，并不足以成为使用 side 元素的理由。决定你是否应该使用旁白的更好的方法包括问你自己是否可以在不减少主要内容的意义的情况下删除旁白中的内容，或者主要内容是否真正依赖于旁白。引用、词汇表，甚至相关链接都是一些适合作为题外话的无关内容的例子。**

第二部分的代码如下:

```
<nav class="menu">
         <ul class="menu-list">
            <li class="menu-item">
               <a class="menu-item-link" href="./homepage" title="Homepage">Homepage</a>
            </li>
            <li class="menu-item">
               <a class="menu-item-link" href="./news" title="News">News</a>
            </li>
            <li class="menu-item">
               <a class="menu-item-link" href="./graphics" title="Graphics">Graphics</a>
            </li>
            <li class="menu-item">
               <a class="menu-item-link" href="./mobile" title="Mobile">Mobile</a>
            </li>
            <li class="menu-item">
               <a class="menu-item-link" href="./webdesign" title="Web Design">Web Design</a>
            </li>
         </ul>
      </nav>
      <div id="content">
         <article id="description-box">
            <h3>Title</h3>
            <p>
               Your content here
            </p>
         </article>
         <div id="wrapper">
            <div id="author-info-box">
               <p>
                  Information about the author <br />
               </p>
            </div>
	    <aside id="video-box">
               <video>
 <source src="movie.mp4" type='video/mp4;' />
 <source src="movie.webm" type='video/webm;' />
</video>
            </aside>
            <aside id="twitter-box">
               <p>
                  <img class="twitter-bird" src=img/twitter-bird.png" alt="Twitter Bird" />
                  Follow me on Twitter! <br />
                  We're in (number of follower)
               </p>
            </aside>
               <br class="clear-both" />
         </div>
      </div>
```

最后剩下的部分是名为 **footer** 的部分，它通常包含关于其部分、作者、相关文档的链接、版权数据等信息。在某些情况下，你还可以找到无处不在的社交网络(如脸书、Twitter、Google+和 LinkedIn)的个人资料或个人页面的链接。因此，我们将主要信息放在插入新 HTML5 标签<页脚>的段落中，并将指向上面提到的社交简档的链接放在一个 div 中，该 div 的主类名为“social-bar”。

这是最后一部分的代码:

```
<footer id="main-footer">
         <p>
            Author's name. All rights reserved. Copyright &copy; 2012 - 2013.
         </p>
         <div class="social-bar">
            <a href="./facebook" title="Facebook">
               <img src=img/icons/facebook.png" alt="Facebook icon" />
            </a>
            <a href="./twitter" title="Twitter">
               <img src=img/icons/twitter.png" alt="Twitter icon" />
            </a>
            <a href="./googleplus" title="Google+">
               <img src=img/icons/google.png" alt="Google+ icon" />
            </a>
            <a href="./linkedin" title="LinkedIn">
               <img src=img/icons/linkedin.png" alt="Linkedin icon" />
            </a>
         </div>
      </footer>
   </body>
</html>
```

### 结论

在本文中，我们看到了如何从零开始构建一个网站，特别关注 HTML 代码及其主要元素。在下一篇文章中，我将介绍第一个 CSS 规则，以便对我们网站的主页将显示的图形样式有一个大致的了解。一旦我们通过了这一步，我们将把注意力集中在将我们的静态主页转换成可以在各种不同设备上正确查看的响应性主页的各个方面和步骤上。

## 分享这篇文章