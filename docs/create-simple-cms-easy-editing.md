# 如何创建一个简单的 CMS，使编辑变得容易

> 原文：<https://www.sitepoint.com/create-simple-cms-easy-editing/>

在这篇文章中，我们将会看到一个简单的方法来建立你自己的内容管理系统，使你的网站编辑和更新变得容易。

*我们与[弗罗阿拉](https://froala.com/?utm_source=Sitepoint&utm_medium=LeadsAcquisition&utm_content=article)合作创作了这篇文章。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

## 什么是 CMS

内容管理系统(CMSs)是强大而流行的工具，用于构建严重依赖内容的平台。它们为用户提供了创建、发布、查看和编辑内容的简单方法。

此外，他们通常需要很少或没有编码经验。这使得它们成为博客作者、企业、开发者或任何想轻松建立网站的人的好选择。

CMS 在不同的领域有不同的用途。例如，它们可以用于博客、公司或自由职业者网站、教育网站，甚至电子商务平台。正因为如此，使用 CMS 仍然是一个流行和相关的选择，在未来的几年里确保它在 web 开发中的地位。

市场上有很多可用的 CMS。一些是开源的，而另一些可以付费使用。开源和付费 CMS 都是可行的选择，各有优势。有些情况下，您可能想要创建自己的 CMS。

你可能是一个寻求挑战的开发人员，一个负责创建 CMS 的学生，或者一个渴望成为 CMS 行业知名人士的企业。如果你想创建一个内容管理系统，那么这篇文章就是为你准备的。

## 准备构建自己的 CMS

我们将使用以下工具制作一个基本的(即足够可用的)CMS:

*   前端的 HTML/CSS/JavaScript/jQuery
*   自举响应速度快，设计简单
*   后端的 PHP
*   用于存储数据的 MS SQL Server(或同等产品)
*   SSMS (SQL Server Management Studio 或等效软件),用于创建数据库和表
*   Froala，一个 WYSIWYG(所见即所得) [HTML 编辑器](https://froala.com/wysiwyg-editor/?utm_source=Sitepoint&utm_medium=LeadsAcquisition&utm_content=article)用于内容编辑

虽然我们将要制作的 CMS 是基本的，但它也将是相当坚固的。这是因为我们将使用的所见即所得编辑器 Froala 拥有 CMS 编辑器中的大部分特性。无论你的 CMS 的最终目标是什么，这篇文章都会让你步入正轨。

另外，请注意，我将使用的 DBMS(数据库管理系统)是 Microsoft SQL Server，因为这是我最熟悉的。您可以很容易地将其替换为您喜欢的 DBMS，如 MySQL 或 MariaDB。由于我们没有对数据库做任何特别复杂的事情， *DBMS 的选择在这里并不重要*。

在我们继续之前，让我们假设您已经在您的计算机上安装了 PHP、您选择的 DBMS 和 Froala。如果您还没有安装它们，您可以通过访问它们各自的网站并按照说明进行安装。

## CMS 里有什么？

通常，在内容管理系统中，有管理员和最终用户。管理员管理网站的页面、组件和内容。他们维护网站，确保每个功能正常工作，并在需要的地方进行改进。

另一方面，最终用户与 CMS 支持的网站的页面和组件进行交互，以创建、编辑和消费内容。两个用户通常与 CMS 中的 WYSIWYG 编辑器进行交互，以创建和编辑内容。

出于演示的目的，并且为了简单起见，我们将只为管理员实现一个单页 CMS。在生产 CMS 中，我们需要考虑许多超出本教程范围的其他因素。这些包括用户管理和访问权限、安全性(输入净化、参数化查询等。)，性能等等。

在我们简单的 CMS 中，管理员应该能够执行以下任务:

*   与工具栏交互以创建页面组件，如标题、文本、链接和一个 Froala 编辑器实例
*   与 Froala 实例交互，发布将要显示的内容
*   查看发布的内容
*   进入视图模式，隐藏工具栏

管理员通常也可以编辑和删除页面组件，但是让我们专注于创建和删除组件。记住这些功能，让我们通过创建一个数据库模式来开始我们的过程。

## 设置数据库

因为我们有简单的需求，所以我们也有更简单的数据库结构。对于我们的示例 CMS，我们将只使用两个不相关的表:

*   **发帖**
    *   `post_id` : int，identity(1，1)，not null，主键
    *   `post_content` : varchar(max)，非空
    *   `posted_on` : datetime2(7), not null
*   **组件**
    *   `component_id` : int，identity(1，1)，not null，主键
    *   `component_type` : char(8)，不为空
    *   `component_content` : varchar(255)，可空
    *   `created_on` : datetime2(7), not null

第一个表`post`将存储来自编辑器组件的内容，而`component`表将存储使用工具栏生成的页面元素。

注意，在大多数浏览器中，URL 的最大长度*为 2048 个字符*，但是在我们的数据库中，我们只将其设置为 255 个字符。此外，您可能还想为您的应用程序将两个表的 ID 列更改为随机字符串。

## 创建界面

我们将包括引导程序的 CDN 链接，引导图标，以及 Froala 的 CSS 和 JS 文件。我们还会有自定义的 CSS 和 JS 文件:

```
<!--Include Bootstrap CSS-->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-gH2yIJqKdNHPEq0n4Mqa/HGKIhSkIHeL5AyhkYV8i59U5AR6csBvApHHNl/vI1Bx" crossorigin="anonymous">

<!--Include Bootstrap Icons-->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.9.1/font/bootstrap-icons.css">

<!--Include Froala Editor CSS-->
<link href="css/froala_editor.pkgd.min.css" rel="stylesheet" type="text/css" />    

<!--Include Custom CSS-->
<link href="css/index.css" rel="stylesheet" type="text/css" />

<!--Include Popper JS-->
<script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.11.5/dist/umd/popper.min.js" integrity="sha384-Xe+8cL9oJa6tN/veChSP7q+mnSPaj5Bcu9mPX5F5xIGE0DVittaqT5lorf0EI7Vk" crossorigin="anonymous"></script>

<!--Include Bootstrap JS-->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0/dist/js/bootstrap.min.js" integrity="sha384-ODmDIVzN+pFdexxHEHFBQH3/9/vQ9uori45z4JjnFsRydbmQbmL5t1tQ0culUzyK" crossorigin="anonymous"></script>

<!--Include jQuery-->
<script src="https://code.jquery.com/jquery-3.6.1.js" integrity="sha256-3zlB5s2uwoUzrXK3BT7AX3FyvojsraNFxCc2vC/7pNI=" crossorigin="anonymous"></script>

<!--Include Froala JS-->
<script type="text/javascript" src="js/froala_editor.pkgd.min.js"></script>

<!--Include Custom JS-->
<script type="text/javascript" src="js/index.js"></script> 
```

下面我们将要用到的组件是导航栏和工具栏。导航栏包含网站的标题或品牌，以及一个用于切换工具栏可见性的图标按钮(可以将其视为“切换管理视图”按钮)。

另一方面，工具栏包含四个按钮，对应于可以通过编程方式添加到页面的四个组件:

```
<div class="row">
  <nav class="navbar navbar-expand-xl">
    <div class="container-fluid">
      <a class="navbar-brand text-white" href="#">Froala CMS</a>
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item">
          <a class="bi bi-eye text-white" href="#" onclick="toggleToolbar()"></a>
        </li>
      </ul>
    </div>
  </nav>
</div> 
```

上面的代码创建了导航栏和组件。导航条的背景色是`#0098F7`，给它一个蓝色的色调。`toggleToolbar()` onclick 事件负责隐藏工具栏元素。接下来，编写工具栏的代码:

```
<div class="row mt-5 px-xl-5 mx-xl-5" id="toolbar">
  <p class="lead ms-xl-5">
    This is your CMS toolbar. You can add new components to your page by clicking any of the buttons below. To toggle the visibility of the toolbar, click the eye (<i class="bi bi-eye"></i>) icon on the navbar.
  </p>
  <div class="col-xxl-3 col-md-4">
    <div class="m-xl-5 shadow p-xl-5 rounded border border-0 toolbarBox" data-bs-toggle="modal" data-bs-target="#headingModal">
      <div class="row text-center display-4">
        <i class="bi bi-type-h1"></i>
      </div>
      <div class="row text-center h3">
        <label>Heading</label>
      </div>
    </div>
  </div>
  <div class="col-xxl-3 col-md-4">
    <div class="m-xl-5 shadow p-xl-5 rounded border border-0 toolbarBox" data-bs-toggle="modal" data-bs-target="#textModal">
      <div class="row text-center display-4">
        <i class="bi bi-fonts"></i>
      </div>
      <div class="row text-center h3">
        <label>Text</label>
      </div>
    </div>
  </div>
  <div class="col-xxl-3 col-md-4">
    <div class="m-xl-5 shadow p-xl-5 rounded border border-0 toolbarBox" data-bs-toggle="modal" data-bs-target="#linkModal">
      <div class="row text-center display-4">
        <i class="bi bi-link-45deg"></i>
      </div>
      <div class="row text-center h3">
  <label>Link</label>
       </div>
     </div>
   </div>
   <div class="col-xxl-3 col-md-4">
    <div class="m-xl-5 shadow p-xl-5 rounded border border-0 toolbarBox" onclick="createComponent('editor')">
      <div class="row text-center display-4">
        <i class="bi bi-pencil-square"></i>
      </div>
      <div class="row text-center h3">
        <label>Editor</label>
      </div>
    </div>
   </div>
 </div> 
```

我们使用 Bootstrap 的网格显示(点击[此处](https://getbootstrap.com/docs/5.2/layout/grid/)了解更多)和尺寸选项来制作工具栏的按钮组件。为了更好的可读性，我们还在按钮标签旁边使用了引导图标。当你制作自己的 CMS 时，有很多方法可以表现你的工具栏。

通常的选项是*导航条*、*卡片*或*画布外*元素。在这种情况下，我们使用类似卡片的矩形来表示它们，每个矩形都有类似按钮的行为。

我们还将为工具栏框添加一些事件。请注意，我们将打开一个模态，其代码如下所示，用于前三个框(标题、文本和链接)。

每个都包含一个输入字段(两个用于链接，一个用于文本，一个用于 URL)。最后，我们将调用`createComponent`函数来初始化 CMS 的 [WYSIWYG HTML 编辑器:](https://froala.com/solutions/content-management/?utm_source=Sitepoint&utm_medium=LeadsAcquisition&utm_content=article)

```
<div class="modal fade" id="headingModal" tabindex="-1" aria-labelledby="headingModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="headingModalLabel">Add a heading:</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <div class="mb-3">
          <input type="text" class="form-control" id="headingContent">
        </div>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-light"  onclick="resetValue('heading')" data-bs-dismiss="modal">Cancel</button>
        <button type="button" class="btn btn-primary" onclick="createComponent('heading')" data-bs-dismiss="modal">Save</button>
      </div>
    </div>
  </div>
</div> 
```

上面的代码显示了其中一个模态，特别是用于向页面添加新标题的模态。当用户点击“标题”按钮/卡片时，显示该模式。当点击**退出**或**取消**按钮，或者添加一个标题时，该状态消失。

其他模式的工作方式类似，但是请注意，一个正确的链接输入应该有两个输入字段，分别用于文本和 URL(现在，我们只使用一个):

```
.toolbarBox:hover *, .toolbarBox:hover{
  background-color:rgb(228, 228, 228);
  cursor: pointer;
} 
```

最后，我们将上面的代码片段添加到我们的`index.css`文件中，以改变工具栏框的背景颜色和鼠标悬停时的光标。完成后，您的用户界面应该如下所示。

![Our interface so far](img/36babc06821943551b88bc80827c4ca7.png)

既然我们已经设置了界面，那么是时候用 JavaScript 添加功能了。

## 发送数据

在我们的`index.js`文件中，我们将为以下特性定义五个函数:创建组件、切换工具栏可见性、重置输入字段、加载帖子和加载组件。让我们看看他们每个人都做了什么。

1.  **createComponent(componentType)**

    我们将使用这个函数将创建的组件发送到数据库。它将描述组件类型(链接、标题、文本、编辑器)的`componentType`作为它的参数。首先，我们必须使用一个`switch-case`语句来确定组件类型。对于标题、文本和链接，我们将使用 AJAX 请求将它们的内容和组件类型一起发送给`php/add.php`。另一方面，对于编辑器，我们只需要发送组件类型。内容成功保存后，我们将调用`getComponents()`函数来“刷新”显示的组件。

2.  **toggleToolbar()**

    这个函数只是根据需要在工具栏中添加或删除`d-flex`和`d-none`类。

3.  **reset value(component type)**

    具有参数`componentType`的函数`resetValue`通过 ID 获取每个元素并将其值设置为空字符串来重新初始化输入字段的值。

4.  **getComponents()**

    这个函数首先向`php/load.php`发出一个 Ajax HTTP 请求，从数据库中获取组件的数据。如果成功，它将根据从数据库中检索到的组件动态生成 HTML(例如，标题组件的`<h1>`元素)。之后，对于每个编辑器组件，一个 Froala 实例和一个**保存**按钮一起被初始化。最后，一个`click`事件监听器被添加到按钮中，用于保存编辑器中写的文章。成功时，调用`getPosts()`函数来“刷新”文章列表。

5.  **getPosts()**

    `getPosts()`函数由一个 Ajax 请求组成，该请求通过`php/loadPosts.php`从数据库中检索所有帖子。类似于`getComponents()`，这个函数动态生成 HTML 来显示文章。

在文档加载时，`getComponents()`和`getPosts()`函数都被调用。既然已经有了向服务器发送数据所需的 JS 代码，剩下的就是在后端处理数据了。

## 准备后端

您可能已经注意到，我们有四个 PHP 文件——两个用于添加组件和文章，两个用于加载它们。它们由相似的代码组成，从用`sqlsrv_connect`连接数据库开始，获取`$_POST`变量(如果有的话)，以及定义和运行查询。下面列出了这些文件中涉及的查询:

1.  **load.php**:

```
 $query = "SELECT component_id, component_type, component_content FROM component ORDER BY created_on "; 
```

2.  **loadPosts.php**:

```
 $query = "SELECT post_id, post_content, posted_on FROM post ORDER BY posted_on "; 
```

3.  **add.php**:

```
 $query = "INSERT INTO component (component_type, component_content, created_on) VALUES (?, ?, GETDATE())"; 
```

4.  **addPost.php**:

```
 $query = "INSERT INTO post (post_content, posted_on) VALUES (?, GETDATE())"; 
```

一旦你设置了这些 PHP 文件，你应该能够使用所见即所得编辑器的编辑能力来执行 CMS 的所有基本功能。在制作您自己的 CMS 时，除了其他安全措施之外，请记住净化输入和参数化查询，以保护您的用户和应用程序免受某些漏洞的影响。

好奇想看看我们的基地 CMS 在行动吗？来测试一下吧！

首先，让我们使用我们构建的工具栏创建一些页面组件。

[https://player.vimeo.com/video/761459287?h=0424bb3dea&badge=0&autopause=0&player_id=0&app_id=58479](https://player.vimeo.com/video/761459287?h=0424bb3dea&badge=0&autopause=0&player_id=0&app_id=58479)