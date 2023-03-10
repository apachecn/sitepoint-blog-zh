# 用 CSS 构建你的网站导航

> 原文：<https://www.sitepoint.com/build-site-navigation-css/>

还有什么比基本的文本链接更无聊的吗？纯文本以不太吸引人的蓝色和紫色显示，没有背景色，没有翻转，没有任何东西让页面在视觉上吸引人…

这种描述可能准确地描述了纯文本链接，但是使用 CSS 属性样式的文本链接完全是另外一回事！CSS 可以创建看起来像按钮或菜单的文本链接，并模仿图像文件或 JavaScript 滚动的效果。所有这些都没有图像和 JavaScript 的下载时间、可访问性和推广问题！

##### 没有加载时间的“外观”

基于图像的导航让您可以控制演示和布局，甚至允许很酷的 DHTML 效果。基于文本的导航创建了一个更易访问、下载更快、对搜索引擎蜘蛛更友好的网站。

网站设计者使用图像进行网站导航，因为它们作为独立的页面元素看起来很酷。当您添加一些 JavaScript 代码来创建翻转效果时(例如，当鼠标被拖动到按钮上时，按钮会改变颜色和大小)，您也在告诉访问者“嘿！这是一个链接！点开它，深入我的地盘！”

但有一个问题:你依赖图像来获得这种效果。JavaScript 翻转要求每个链接至少有两个图像(一个图像用于基本按钮，另一个用于翻转)。如果你链接到十个不同的页面，那么你就已经在你的页面上加载了 20 张图片，这些图片必须在你的导航系统对访问者清晰之前下载。

拨号连接速度慢的人可能不愿意等待。他们可以从数以亿计的网页中进行选择，可能并不认为你的网站有什么特别之处——尤其是当导航系统看起来坏了或者不存在的时候。

因此，我们在本教程中的目标是建立一个丰富多彩的，有吸引力的基于文本的导航系统，而不使用图像。

##### 了解 CSS 链接伪类

首先，让我们花一分钟来回顾一些基础知识:CSS 链接伪类。如你所知，CSS 类是用来引用特定类别的元素的。类似地，CSS 伪类指的是元素的特定状态。我们将使用不同链接状态的伪类来创建导航系统。

人可以有不同的意识状态:醒着或睡着是最明显的两种。在 CSS 中，一个超链接可以有四种不同的状态，你可以为每种状态设置样式属性:

*   **Hover**: Mimics the onMouseOver event in JavaScript. When a visitor moves the mouse over the link, the link’s appearance changes according to the properties you’ve defined.*   **Visited**: Sets the appearance of links that have already been visited and are in the user’s history file.*   **Active**: Changes the appearance of links when the user clicks on them or selects them with the keyboard.*   **Link**: Sets the default appearance of links that haven’t been visited or opened.

    对于我们的目的来说，悬停和链接是两个最重要的链接状态，但是我们将为这四个状态定义属性。

    ##### 带有 CSS 的导航按钮

    设计过程可能会变得非常复杂，因为我们要处理多个链接状态，并为每个状态设置不同的属性。在开始编码之前，我总是创建一个规划网格来清楚地定义我的 CSS 属性。

    ![1033_table1](img/6b066ee07d09b44feafbd79362fc0887.png)

    请注意，我们实际上只设置了两个不同的样式定义，并将每个定义应用于两个不同的链接状态。“链接”和“已访问”属性获得相同的样式定义，并且活动和悬停状态也以相同的方式显示。

    下面是实际的样式定义。

    ```
    <style type="text/css"> 

    a.button:link  

    { 

      font-size:14px; 

      font-weight:bold; 

      text-decoration:none; 

      border-style:outset; 

      border-color:red; 

      border-width:5px; 

      background-color:#FFFFCE; 

      width:125px;  

      color:navy; 

    } 

    a.button:visited  

    { 

      font-size:14px; 

      font-weight:bold; 

      text-decoration:none; 

      border-style:outset; 

      border-color:red; 

      border-width:5px; 

      background-color:#FFFFCE; 

      width:125px;  

      color:navy; 

    } 

    a.button:active  

    { 

      font-size:14px; 

      font-weight:bold; 

      text-decoration:none; 

      border-style:inset; 

      border-color:red; 

      border-width:5px; 

      background-color:#FFFFCE; 

      width:125px; 

      color:maroon; 

    } 

    a.button:hover  

    { 

      font-size:14px; 

      font-weight:bold; 

      text-decoration:none; 

      border-style:inset; 

      border-color:red; 

      border-width:5px; 

      background-color:#FFFFCE; 

      width:125px;  

      color:maroon; 

    } 

    </style>
    ```

    把它放在你的文档的`<head>`部分，或者通过一个外部样式表附加它(如果你这样做的话，记得去掉`<style>`和`</style>`标签！).看着它，你可能会奇怪为什么我没有让后面的定义继承第一个定义的所有公共属性。理想情况下，样式表应该是这样工作的:定义按钮一次，然后对于后面的状态，只需根据需要进行更改。不幸的是，浏览器对此的支持参差不齐！将所有属性和值添加到每个链接状态更安全，即使这意味着复制一些信息。

    像对待任何普通文本链接一样，在 Web 页面上包含按钮，但是一定要将类名添加到`<a href>`标记中:

    ```
    <a href="https://www.sitepoint.com/" class="button"  

    title="Visit SitePoint.com for valuable webmaster  

    resources">SitePoint</a>
    ```

    `border`属性实际上给了按钮一个按钮的外观。我们将`border-style`属性设置为 outstart，为`:link`和`:visited`州赋予一个常规按钮的外观，并为`:active`和`:hover`州提供一个插入值。这使得链接看起来像是被按下的按钮。

    注意，我们将每个链接定义为一个名为“button”的依赖 CSS 类否则，网页上的每个链接看起来都像我们的导航按钮——甚至是文本内容中的链接和 mailto 链接。当您为导航系统创建一个单独的类时，您可以自由地以一种不太显眼的方式设计文本内容和 mailto 链接。

    我可以详细解释这一切在浏览器中会是什么样子，但是这个示例页面更清楚地展示了效果。它有不同的按钮，甚至包含一个简单的水平导航菜单，由并排放置的各个按钮组成。

    Explorer 和 Netscape 创建了最吸引人的按钮。Opera 版本 6 和版本 7 之间的显示略有不同。WebTV 显示按钮，但没有边框或翻转效果。a

    ##### 带有 CSS 的导航菜单

    接下来，让我们使用同样的技术创建一个垂直导航菜单。

    与前面的样式定义的唯一区别是，我们将去掉每个文本链接的边框，并将链接放在一个带边框的 DIV 标签中。

    这一次规划网格是这样的:

    ![1033_table2](img/b6cb6d6944ceeb4c7c5d1ef7476612df.png)

    和以前一样，相同的对是链接/访问和活动/悬停。我们将定义一个`<div>`类来包含我们的菜单系统，并给它一个吸引人的、完美的外观，所有链接周围都有一个边框。

    下面是链接类和`div`的样式定义。请注意，我们再次使用了类，而不是将样式应用于页面上的每个链接和每个`div`!

    ```
    <style type="text/css">  

    a.vertical:link  

    {  

      font-size:14px;  

      text-decoration:none;  

      color:#9966CC;  

      background:#FFFF00;  

      font-weight:bold;  

      width:150px;  

    }  

    a.vertical:active  

    {  

      font-size:14px;  

      text-decoration:none;  

      color:white;  

      background:white;  

      font-weight:bold;  

      width:150px;  

    }  

    a.vertical:visited   

    {  

      font-size:14px;  

      text-decoration:none;  

      color:#9966CC;  

      background:#FFFF00;  

      font-weight:bold;  

      width:150px;  

    }  

    a.vertical:hover  

    {  

      font-size:14px;  

      text-decoration:none;  

      color:white;  

      background:#9966CC;  

      font-weight:bold;  

      width:150px;  

    }  

    .verticalBorder  

    {  

      background:#FFFF00;  

      border-style:solid;  

      border-color:#9966CC;  

      border-width:5px;  

      width:160px;  

    }     

    </style>
    ```

    将菜单系统的链接放在您希望它在页面上显示的任何位置。确保使用正确的类信息，并将链接包装在我们刚刚定义的`<div>`中:

    ```
    <div class="verticalBorder" align="center">  

    <a href="http://www.sitepoint.com"   

    class="vertical">Site Point Home</a><br/>  

    <a href="http://www.sitepoint.com"   

    class="vertical">CSS Tips</a><br/>  

    <a href="http://www.sitepoint.com"   

    class="vertical">JavaScript Tips</a><br/>  

    <a href="http://www.sitepoint.com"   

    class="vertical">Domain Names</a><br/>  

    <a href="http://www.sitepoint.com"   

    class="vertical">Beginner Tips</a>  

    </div>
    ```

    在链接之间使用一个 break ( `<br />`)标签创建一个垂直菜单。该标签将链接删除到下一行。这比使用 display 属性将链接转换为块级元素更可靠。

    您可能需要尝试不同的边框样式、宽度和颜色来为您的个人站点定制 CSS 属性。设置链接和`<div>`的宽度可能特别棘手，所以一定要在几个浏览器中测试菜单，以确保它显示一致。

    第二个示例页面显示了一个垂直菜单系统。它在所有主流浏览器(甚至 WebTV)中返回相当一致的结果，除了 Netscape 4.7(它的爱好是破坏样式表，无论您使用什么技术)。

    作为一个非常酷的测试，使用 Opera 查看页面并关闭图像。将文本链接导航菜单的外观与其下方的图像地图进行比较。

    ##### 关于访问者偏好的一句话

    很容易将菜单系统的`<a href>`标签放在`<body>`部分。但是实际上让它们看起来像你想要的样子需要一些尝试和错误。理想情况下，除了文本内容之外，您希望每个链接看起来都一样。所以宽度，颜色，对齐等。每个文本链接都需要与其他链接相匹配。

    这意味着你必须通过定义字体和设置绝对字体大小来控制你的访问者。相对于周围的文字和访问者的偏好，相对的字体大小太冒险了。

    我们在这里使用了一个非常紧凑的布局，如果访问者试图在浏览器的文本大小设置为最大或最小可用值的情况下查看它，这个布局将会被破坏，甚至可能难以辨认。通常，最好的答案是一个折衷方案:为导航系统设置绝对字体值，但要确保它们足够大，以便清晰阅读。在我看来，12 像素是最少的。然后，你可以让访问者的偏好来决定页面内容的其余部分。

    尽管如此，即使有这么多精心的计划和控制，你也不能保证你的链接在所有浏览器中对所有访问者都有效。限制 CSS 被广泛采用的一个因素是不一致的浏览器支持。在本文中，我试图坚持使用 IE 5.x 及以上版本、Netscape 6.x 及以上版本和 Opera 6.x 及以上版本支持的技术。

    ##### 所有这些工作真的值得吗？

    你打赌。你会在很多方面受益，你的访客也是如此！

    *   ***Faster Downloads***

    更少的图像文件意味着更少的下载时间！慢速连接的访问者可以更快、更可靠地查看您的页面。你可能有一个快速的宽带互联网连接，但其他人就没那么幸运了。虽然高速线路的使用正在增加，但全球绝大多数用户仍然通过拨号连接。

    *   ***Browser Compatibility***

    所有浏览器都支持文本链接，但是有些浏览器不支持 JavaScript 或显示图像(想想 WebTV 和文本浏览器)。复杂的 DHTML 菜单结构对于网络电视访问者来说可能是完全不可访问的。网速较慢的访问者可能更喜欢在关闭图像的情况下上网。有时候，问题出在你身上。服务器可能不会将图像发送到浏览器，或者编码错误可能会阻止图像正常显示。

    *   ***Better Accessibility***

    有严重视觉障碍的访问者经常使用屏幕阅读器或网页阅读器来收听网页。如果你看不到图像，基于图像的导航系统是很难破译的。

    *   ***Easier Maintenance***

    当链接实际上是文本格式而不是图像格式时，更改链接中的文本要容易得多。假设你的老板决定将“联系我们”链接改为“给我们发邮件”。如果是文字链接，你改一个字。如果它是一个图像链接或按钮，你必须自己修改整个图像文件，或者付钱给图形设计师帮你修改。

    *   ***Keywords in Link Text***

    一些搜索引擎算法(比如谷歌的)特别重视链接文本中的关键词。关键词是你希望人们输入搜索引擎来定位你的网站的单词或短语。您可以通过在链接文本中使用您网站的目标关键词，在结果页面上稍微提升您的网站。您可以在图片链接的 ALT 和 TITLE 属性中使用关键字(您应该这样做！)，但文字更重要。

    *   ***More Likely to be Spidered***

    大多数搜索引擎声称他们的蜘蛛能够爬遍网站的每一层并索引每一页。这被称为“深度蜘蛛”,因为蜘蛛读取内容，即使它位于网站的深处。但是蜘蛛必须先找到内部页面，然后才能索引它们。

    想想蜘蛛到底是什么:基本上是一个简单的文本浏览器。蜘蛛看不到你的图片，有时跟踪 JavaScript 超链接会有问题。因此，您站点上的 DHTML 下拉菜单系统看起来可能很棒，但它也可能对搜索引擎蜘蛛关上了大门。他们不能索引他们找不到的东西！

    …还是不相信？在您自己的一些示例页面上尝试这些技术，并考虑如何将它们集成到您的站点设计中。文字链接和 CSS 一起使用，对于网站设计和推广来说是如此强大的工具，以至于你可能再也不会使用图像导航了！

## 分享这篇文章