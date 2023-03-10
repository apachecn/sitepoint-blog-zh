# 不到 1 小时了解 Web 开发

> 原文：<https://www.sitepoint.com/understand-web-development-less-1-hour/>

*本文是与 [KTree](https://ktree.com/) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

这篇文章解释了什么是 web 开发，通过探索它是如何开始的以及它是如何发展的。这不是一部精确的网络进化编年史，而是更侧重于这种进化的需求是什么，这样我们才能理解技术。

一切都是从信息开始的。人类总是需要找到与他人分享信息的方法。如你所知，在互联网出现之前，信息是通过信件、报纸、广播和电视来分享的。每个都有自己的缺点，这使得互联网的信息高速公路走到了最前面。

## 1.什么是网络？

如果你可以在一个任何感兴趣的人都可以去阅读的地方发布信息，那会怎么样？这正是网络所做的。您将信息保存在 web 服务器上，人们可以使用客户端(浏览器)阅读这些信息。这种架构被称为“服务器-客户端架构”。

为什么是 HTTP？

最初，这些信息都是以文本形式存储的——这就是为什么超文本传输协议这个名称一直沿用至今，尽管现在文本、媒体和文件都是通过这个协议进行交换的。

## 2.信息是如何保存、检索和保存的？

在 web 上存储信息的最基本和最长久的方式是 HTML 文件。为了更好地理解，让我们举一个简单的例子，公司发布它的价格信息，这样它的供应商就可以下载和查看这个列表，这个列表包括带有价格和生效日期的产品。这在服务器上保存为 HTML 文件，可以使用 web 浏览器查看。浏览器向服务器请求文件，服务器提供文件并关闭连接。

HTML 是用于创建网页的标准标记语言。换句话说，它是一个简单的文本文件，带有帮助浏览器决定如何显示信息的标签。

```
<!DOCTYPE html>
<html>
<body>

<h2>Price List</h2>

<hr>

<table>
  <tr>
    <td>Product Name</td>
    <td>Sku</td>
    <td>Price</td>
  </tr>
  <tr>
    <td>KTree Web Service</td>
    <td>1234566</td>
    <td>60.USD Per Hr</td>
  </tr>
  <tr>
    <td>KTree Web Service</td>
    <td>1234566</td>
    <td>60.USD Per Hr</td>
  </tr>
</table>

<hr>

</body>
</html> 
```

![](img/84f3ee34423d0add252a7f46c6d0548b.png)

### 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

级联样式表(CSS)是一种样式表语言，用于描述以标记语言编写的文档的表示。基本的格式和样式可以通过 HTML 来完成，但是最好使用 CSS 来完成。

一个 web 应用程序包含许多动态或静态页面。如果我们使用 HTML 标签来设计信息的样式，我们必须在每一页中重复这些信息。假设我们想改变背景颜色——我们必须编辑作为站点一部分的每个页面的 HTML。

相反，我们可以使用 CSS 将样式定义存储在一个位置，并将每个 HTML 页面引用到该位置。通过改变 CSS 文件，我们可以改变每一页上的背景颜色，它依赖于样式表的样式定义。

当然，CSS 不仅仅是设置背景颜色:它允许我们为各种元素、字体、页面布局等等设置颜色。

我们已经使用 CSS 样式化了前面的例子。假设我们在不同的页面上使用表格，但是使用相同的 CSS 样式。我们可以将所有这些样式信息移到它自己的文件中。

```
<!DOCTYPE html>
<html>
<head>

<!–– for simplicity we have kept the CSS in inline in the HTML -  you can keep the css in any file with a .css extension and include it using <link rel="stylesheet" href="styles.css"> --> 

<style> table {
    font-family: arial, sans-serif;
    border-collapse: collapse;
    width: 100%;
}

td, th {
    border: 1px solid #dddddd;
    text-align: left;
    padding: 8px;
}

tr:nth-child(even) {
    background-color: #dddddd;
} </style>

</head>
<body>
<h2>Price List</h2>

<table>
  <tr>
    <td>Product Name</td>
    <td>Sku</td>
    <td>Price</td>
  </tr>
  <tr>
    <td>KTree Web Service</td>
    <td>1234564</td>
    <td>60.USD Per Hr</td>
  </tr>
  <tr>
    <td>KTree Web Service</td>
    <td>1234565</td>
    <td>40.USD Per Hr</td>
  </tr>
  <tr>
    <td>KTree Web Service</td>
    <td>1234566</td>
    <td>50.USD Per Hr</td>
  </tr>
</table>
</body>
</html> 
```

![](img/281b94f2b2cc333cf531be3fed25bdec.png)

### Java Script 语言

JavaScript 是 web 的第三大支柱，与 HTML 和 CSS 并列，通常用于使网页具有交互性。为了理解 JavaScript (JS)，我们需要知道 DOM 是什么。

文档对象模型(DOM)是一个独立于语言的应用程序编程接口，它将 HTML 文档转换成一个树形结构。每个文档的节点都被组织在一个叫做 DOM 树的树结构中，最顶端的节点叫做“文档对象”

![](img/f66564e0cc4ecc68e69667a2fac45c82.png)

*样本 DOM 树(来源:维基共享资源)*

当在浏览器中呈现 HTML 页面时，浏览器将 HTML 下载到本地内存中，并创建一个 DOM 树以在屏幕上显示页面。

使用 JS，我们可以用几种方式操作 DOM 树:

*   JS 可以通过添加、更改和删除页面中的所有 HTML 元素和属性来修改 DOM 树。
*   JS 可以改变页面上所有的 CSS 样式。
*   JS 可以对页面上的所有现有事件做出反应。
*   JS 可以在页面中创建新事件，然后对所有这些事件做出反应。

在我们的 JavaScript 示例中，我们继续我们的价目表示例，添加另一列—特价—默认情况下是隐藏的。一旦用户点击它，我们就会显示出来。用技术术语来说，我们使用一个附加到 web 元素(锚标签)的 click 事件，并改变 web 元素的现有文本，换句话说就是操纵 DOM。要做到这一点，我们必须使用浏览器接受的脚本语言，它始终是 JavaScript。

```
<!DOCTYPE html>
<html>
<head>
<!–– for simplicity we have kept the CSS in inline in the HTML -  you can keep the css in any file with a .css extension and include it using <link rel="stylesheet" href="styles.css"> --> 

    <style> table {
            font-family: arial, sans-serif;
            border-collapse: collapse;
            width: 100%;
        }

        td, th {
            border: 1px solid #dddddd;
            text-align: left;
            padding: 8px;
        }

        tr:nth-child(even) {
            background-color: #dddddd;
        }

        #specialprice {

        } </style>

</head>

<body>
<h2>Price List</h2>

<table>
    <tr>
        <td>Product Name</td>
        <td>Sku</td>
        <td>Price</td>
        <td>Special Price</td>
    </tr>
    <tr>
        <td>KTree Web Service</td>
        <td>1234564</td>
        <td>60.USD Per Hr</td>
        <td id="specialprice"> <a href="" onclick="return false;"> Click Here  </a> </td>
    </tr>
    <tr>
        <td>KTree Web Service</td>
        <td>1234565</td>
        <td>40.USD Per Hr</td>
        <td id="specialprice2"> <a href="" onclick="return false;"> Click Here  </a> </td>
    </tr>
    <tr>
        <td>KTree Web Service</td>
        <td>1234566</td>
        <td>50.USD Per Hr</td>
        <td id="specialprice3"> <a href="" onclick="return false;"> Click Here  </a> </td>
    </tr>
</table>

<script> document.getElementById("specialprice").onclick = function() {myFunction()};

    function myFunction() {
        document.getElementById("specialprice").innerHTML = "20% Off";
    } </script>

</body>
</html> 
```

![](img/9e97d45ed1ef9a554760c1d7df09894a.png)

![](img/1c78568dc6e54345c5255c3755496117.png)

### 形式

到目前为止，我们只讨论了从服务器获取数据。表单是 HTML 的另一面，它允许我们向服务器发送信息。我们可以使用表单来更新现有信息或添加新信息。HTML 表单中最常用的方法是`GET`和`POST`。

```
<!DOCTYPE html>
<html>
<body>

<form action="createproduct.php" method="post">
  First name:<br>
  <input type="text" name="Product Name" value="KTree Service">
  <br>
  Last name:<br>
  <input type="text" name="SKU" value="234555">
  <br><br>
  <input type="submit" value="Submit">
</form> 

<p>If you click the "Submit" button, the form-data will be sent to a page called "createproduct.php".</p>

</body>
</html> 
```

看一下上面的代码——我们有两个输入字段，用户可以在其中输入数据并点击提交按钮。点击提交按钮后，浏览器会将这两个输入字段的数据值和一些其他信息发送给 PHP 脚本`createproduct.php`。

在我们的例子中是 PHP，但也可以是 JSP、Python 或任何服务器端脚本。服务器端脚本可以读取浏览器通过 POST 发送的值，然后对其进行处理或将其存储在文件或数据库中。简而言之，这就是数据被推送到服务器，然后最终存储在文件或数据库中的方式。

**注意:**假设我们希望在提交之前添加验证，例如，产品应该至少包含 5 个字符，或者 SKU 字段不应该为空。我们可以使用 JavaScript 进行这些验证。我们需要对 Submit Click 事件做出反应，并检查 web 元素是否有我们需要的数据。如果缺少任何东西，我们可以显示一条错误消息，并停止向服务器发送数据。

### 数据库

一旦信息开始增长，从文件中获取正确的信息就会变得非常困难，更不用说速度之慢了。例如，让我们以相同的价格文件为例，假设该公司有数千种产品，我们想知道列表中最后一种产品的信息，这意味着我们需要读取所有产品，直到找到我们想要的产品。这不是检索信息的最佳方式，所以为了解决这个问题，数据库诞生了。

在数据库(DB)中，我们将数据存储在表中(一组结构化的数据)，因此我们可以轻松地执行搜索、排序和其他操作。

### 服务器端脚本语言和框架

我们需要编程语言:

*   向数据库或文件中存储或从中读取
*   对来自服务器的信息进行一定的处理。
*   从客户端读取`POST`信息，并做一些处理来存储/推送该信息。

像 C 和 Java 这样的典型编程语言可以读写数据库，但是它们不能直接在 web 服务器上运行。这催生了服务器端脚本语言。

服务器端脚本语言完成所有常规处理，可以与数据库对话，并且可以直接在 web 服务器上运行。流行的服务器端脚本语言有 PHP、Perl、JSP、Ruby on Rails 等等。

开发人员开始使用这些语言，很快他们意识到他们正在为所有项目编写相同的样板代码，这导致了框架的开发，这使得开发 web 应用程序更加容易和快速。

一些著名的框架:

*   **PHP** : Zend，YII，Symfony，CakePHP，Laravel
*   也用作框架的 PHP 产品 : Drupal、Joomla、SugarCRM、WordPress
*   **Java** : J2EE、Hibernate、Struts、Spring
*   **JavaScript** : Node.js

### MVC 架构和会话

MVC 架构帮助我们将代码分成多个文件，并让我们分离业务和表示逻辑，这样在后面的阶段修改它们就更容易了。

以博客平台为例，我们将重温到目前为止我们讨论过的所有主题，看看我们如何使用 MVC 架构进行编码。

博客平台管理动态内容，可能包含几个模块，例如:

*   用户
*   博客帖子
*   标签
*   种类

在讨论其他功能之前，让我们先来看看 Blog Posts 表的基本数据库设计。重要的字段是:

**tbl_blog_post**

| **ID** | **Title** | **内容** | **由**创建 | **名字** | **姓氏** | **创建于** |
| --- | --- | --- | --- | --- | --- | --- |
| one | 你好世界 1 | 你好世界 1 | 用户 A | 用户 | A | 2016 年 1 月 10 日 |
| Two | 你好世界 2 | 你好世界 2 | 用户 B | 用户 | B | 2016 年 6 月 10 日 |

如您所见，我们存储了重复的用户信息，如“名字”和“姓氏”。如果我们有 10，000 篇博客文章，我们将在所有 10，000 篇博客文章记录中存储所有重复的用户信息。可能有更多关于用户的信息要存储，例如他的名称、最后登录时间等。

正如您可能已经猜到的，另一种方法是将“用户”信息存储在另一个表中，并在这里用“相关”Id 与它相关联，如下所示。

TBL _ user

| **ID** | **用户名** | **名字** | **姓氏** | **创建于** |
| --- | --- | --- | --- | --- |
| One hundred and one | 用户 A | 用户 | A | 2016 年 1 月 10 日 |
| One hundred and two | 用户 B | 用户 | B | 2016 年 6 月 10 日 |

**tbl_blog_post**

| **ID** | **Title** | **内容** | **由**创建 | **创建于** |
| --- | --- | --- | --- | --- |
| one | 你好世界 1 | 你好世界 1 | One hundred and one | 2016 年 1 月 10 日 |
| Two | 你好世界 2 | 你好世界 2 | One hundred and two | 2016 年 6 月 10 日 |

将数据划分为多个表是**数据标准化的众多原则之一。**

下一个重要部分是让用户通过 HTML 表单在这些表中创建数据。请记住，我们做这个剖析是为了理解概念——这绝不是一个完整的编程教程。

#### 由经过验证的用户创建新的博客文章

为此，我们需要一个带有两个输入字段(标题，内容)的 HTML 表单，用户可以通过它创建一篇博客文章。

在用户输入信息并点击提交按钮“Create Post”后，这些表单值通过`POST`被发送到 web 服务器。可以使用任何服务器端脚本语言读取 POST 值。服务器脚本(PHP、Ruby on Rails、Python 等。)从表单中读取值，并将其推送到数据库。

该脚本还可以进行处理，包括获取服务器日期和时间，或者是基于从另一个表或 web 服务中检索的值的一些计算字段。

需要注意的另一点是:脚本还可以执行验证，也称为服务器端验证，以确保数据有效。如果数据是有效的，那么只有表单数据被持久化到 *tbl_blog_post* ，或者它向客户机发回一条消息，输入缺失的信息，然后过程继续。

在我们的表 *tbl_blog_post* 中，除了标题和内容，我们还有一个名为*的字段，由*创建。我们如何获得这个字段的值？

#### 用户登录

通常，大多数 web 应用程序都具有登录功能。每当用户成功通过身份验证时，用户信息都会存储在会话中，以便以后可以重用这些信息。

### 什么是会话？

HTTP 协议是一种无状态协议，这意味着客户端使用`GET`或`POST`向 web 服务器发出的任何请求都不会被跟踪。如果客户机(浏览器)发出两个请求，web 服务器不知道也不关心这两个请求是否来自同一个用户。这也意味着，举例来说，如果你登录到一个电子商务应用程序，你正在添加产品到你的购物车，服务器不知道你们是同一个用户。

为了克服这种无状态性，客户端需要在每个请求中发送附加信息，以便在多个请求期间保留会话信息。这些附加信息存储在客户端的 cookies 中，存储在服务器端的会话中。

会话是一个数组变量，它存储跨多个页面使用的信息。会话由一个惟一的 ID 标识，ID 的名称取决于编程语言——在 PHP 中，它被称为“PHP 会话 ID”。相同的会话 ID 需要作为 cookie 存储在客户端浏览器中，以便建立关联。

#### 显示个人博客文章

我们的下一个项目是显示个人博客文章。我们需要根据请求的博文 ID 从数据库中读取数据，然后显示标题和内容字段的内容。

用于显示单个博客帖子的高级伪代码:

*   从数据库中读取博客文章 ID 的数据
*   将数据与 CSS 和 JS 一起插入 HTML 模板

上述所有代码都可以写在一个文件中。这是早期的做法，但是开发团队意识到这并不是最优的。对于任何要添加的新特性，整个代码都需要更改，在多开发人员环境中工作并不容易。

这使得 web 开发人员采用了 MVC 架构，该架构本质上将代码分解为下面列出的三个组件。

*   **模型**:模型是领域/业务逻辑，独立于用户界面。在我们的示例中，从数据库中获取单个帖子的代码可以保存在这里。
*   **视图**:视图可以是任何输出的信息表示。我们显示 post 的 HTML 代码可以保存在这里，所以数据来自模型，但是 HTML 在视图中。
*   **控制器**:第三部分，控制器是我们点击查看帖子链接时调用的。它从模型中获取数据，并使用该数据呈现视图。

让我们检查一个 MVC 应用程序中的典型 URL，以便更好地理解它。

```
http://www.abc.com/blogpost/id/1 
```

这里的`blogpost`是控制器名，视图是控制器中的一个动作(方法)。`id`是博文的 ID。如果我们在浏览器中输入这个，请求会转到“BlogPost”控制器的动作“View ”,在这里它调用模型来获取 blog post ID“1”的内容作为模型对象。该对象被传递给“视图”来呈现它。

### Ajax 和单页面应用程序(SPA)

如果你出生在上个千年，你可能记得在 90 年代和 00 年代，Hotmail 和雅虎！是非常受欢迎的网络电子邮件提供商。如果你点击收件箱或者收件箱中的一封邮件，整个页面都会被刷新。大约在 2004 年，Gmail 有了一个重要的特性，Ajax。使用 Ajax 时，整个页面不会被刷新——只有需要更改的部分会被刷新。因此，如果你收到一封新邮件，不用刷新整个页面，你只需在顶部看到一封新邮件。这给用户带来了类似桌面的体验，并成为一种非常流行的构建应用程序的方式。

#### 什么是 Ajax？

Ajax 这个术语代表了一组广泛的 web 技术，这些技术可以在与后台服务器通信的应用程序中实现，而不会干扰页面的当前状态。

使用 Ajax，您向服务器发送一个`GET`请求，服务器将其响应作为输出发送，而不会阻塞当前网页，这意味着用户可以继续做他们正在做的任何事情而不会被中断。输出被追加或添加到当前网页。

在非 Ajax 网站中，每个用户操作都需要从服务器加载一个完整的页面。这一过程效率低下，造成了糟糕的用户体验。所有页面内容消失，然后重新出现。

Ajax 是我们可以用来构建单页面应用程序(spa)的技术之一。顾名思义，整个应用程序在一个页面中，所有内容都是动态加载的。可以使用 Angular、React 和 Backbone.js 等 JavaScript 框架来构建 spa。

### 网络服务器和浏览器

浏览器是网络的解释者。浏览器向 web 服务器请求数据，web 服务器处理该请求并将响应以 HTML(包括 CSS、JS、图片等)的形式发送给浏览器，然后显示出来。

我们可以使用以下三种重要方法中的任何一种向 web 服务器发出请求:

*   `GET`:获取请求的资源作为响应。
*   `HEAD`:与`GET`相同，但响应来自 Head 而不是 response。
*   `POST`:向服务器提交表单数据，或者通过 Ajax 提交任何数据。

例如，当您在浏览器中键入 google.com 时，浏览器会在后台向 google.com 服务器发送此命令。

```
GET: http://google.com 
```

Google web 服务器将处理其主/索引文件，并将响应发送回客户端。它通常发送 HTML 内容和 CSS 文件，以及任何其他媒体文件。

## 分享这篇文章