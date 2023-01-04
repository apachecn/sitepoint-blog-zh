# 使用 App Framework 创建移动 HTML5 应用程序

> 原文：<https://www.sitepoint.com/creating-mobile-html5-application-app-framework/>

## 什么是 App 框架？

App Framework 是一个用于移动 HTML5 应用开发的 JavaScript 库。它允许您构建简单、丰富和完整的 HTML5/JavaScript 移动应用程序。这个简短的教程是对 App 框架的介绍，它介绍了基本概念及其主要概念。

App 框架库的灵感来源于 jQuery。App Framework 可以为 Android 或 iOS 等移动设备设计强大的界面。App 框架由三个元素组成:查询和事件管理库、图形界面库和 WebKit 插件库。

该库在功能上比 jQuery Mobile 更丰富。App Framework 的另一个优点是它只需要 3KB 的内存，而 jQuery 需要 35KB。Android 上的脚本也比 jQuery 快 3 倍，iOS 上快 2.2 倍。

## 竞争框架

App Framework 有几个竞争框架。这些框架与 App Framework 的功能大致相同。我们可以提到其中最著名的:jQuery Mobile，Sencha Touch，jQTouch。App 框架最大的优势是它的重量和它的执行速度。App Framework 是移动 HTML 5 框架最强大的解决方案。

## 先决条件

### 代理人的创建

对于教程的特定需求，我们将需要通过 Ajax 进行跨域请求。为了处理这些请求，设置 Apache 和 PHP 服务器是必要的。由于 JavaScript 不直接管理跨域请求，我们将通过 php 建立一个小型代理。将以下代码复制并粘贴到一个 server.php 文件中，该文件与我们的 HTML 页面位于同一位置。必须在 php.ini 中启用“php_curl”模块。

```
<? php
$ url1 = $ _GET ["url"];
$ ch1 = curl_init () / / Check that the php_curl extension is enabled in php.ini
curl_setopt ($ ch1, CURLOPT_URL, $ url);
curl_setopt ($ ch1, CURLOPT_HEADER, false);
curl_setopt ($ ch1, CURLOPT_RETURNTRANSFER, true);
$ xml1 = curl_exec ($ ch1);
echo $ xml1;
curl_close ($ ch1);
?>
```

### 应用框架

App Framework 是 jQuery 的移动版，所以两个框架使用相同的语法。对于本教程，最好了解一些 jQuery 知识。App 框架可以在[这个地址](http://app-framework-software.intel.com/)下载。将下列文件夹和文件复制并粘贴到您站点的目录中:

*   刻划现代生活不光明的一面的
*   插件
*   用户界面

注意:要测试，你需要一个兼容 webkit 和 HTML5 的浏览器。

## 辅导的

我们将通过这个例子向您展示如何使用 App Framework 来构建您的移动应用程序。简而言之，我们的示例将基于 RSS 提要的小型阅读器的概念。这只是给你一个 App 框架的基础知识。首先，我们将使用 jqUI 构建我们的 GUI，并使用 App Framework 实现一些功能。

### 创建页面

作为第一步，我们将创建我们的页面并配置我们的应用程序框架。下面是一个应用程序框架应用程序的框架:

```
<html>
<head>
<meta content="text/html; http-equiv="Content-type" charset=utf-8"/>
<script src="//cdn.app-framework-software.intel.com/2.0/appframework.min.js" type="text/javascript"> 
</script><script type="text/javascript" charset="utf-8"src="./ui/appframework.ui.min.js"> 
<link rel="stylesheet" type="text/css" href="css /af.ui.css"title="default" />
</script><script>
$.ui.ready (function ( ) {
            $.ui.backButtonText ="Back". / / We override the back button text to always say" Back"
} ) ;

        if (! ( (window.DocumentTouch && document instanceof  DocumentTouch ) |  | ' ontouchstart ' in window )) {
            var script = document.createElement (" script");
            script.src =" plugins/af.desktopBrowsers.js" ;
            var tag = $ (" head" ). append ( script) ;
            $.os.android = true;. / / let 's make it run like an android device
            $.os.desktop = true. ;
        }
 </script>
</head>
<body>
   <div id="afui">
   </ div>
</ body>
</ html>
```

在服务器的根目录下创建一个“index.html”页面，并添加上面的代码。这段代码是我们页面的基础，它导入了使用应用程序框架所必需的脚本。我们的应用程序将被分解成两个组件:一个页面及其内容和一个菜单。使用 App Framework，为了创建页面，我们将在 HTML 文件的“content”div 中创建几个 div。

我们将从创建我们的主页“RSS”开始，它将包含一个允许用户输入 RSS 链接的输入和一个包含 RSS 流中标题列表的面板。在“afui”div 中，我们将添加“内容”div。我们将在这个特定的 div 中创建应用程序的不同页面。我们的第一页将被命名为“RSS”。

```
<div id="afui">
       <div id="content">
           <div title='RSS' class="panel" id="rss" style ="overflow: hidden" >
           </ div>
       </ div>
      </ div>
```

我们将添加到我们的页面输入元素，标签和提交按钮，允许用户输入一个网址。我们将使用 HTML5 标签“字段集”。这

<fieldset>element allows a grouping of input fields into logical categories (thematic).

```
</fieldset><fieldset>
    <legend> RSS 2.0  
        <form id="parser" onsubmit="return false">
          <label for="links"> Enter  rss 2.0 url  <br />
          <input type="text"id="links" name="links" class='jq-ui-forms'/>
            <input type="submit" class="button" value="Submit" onclick=" Rssparse ()"/>
     </ form>
</ fieldset>
```

input 和 label 标签是经典的 HTML 标签，我们添加了由 afui 直接提供的类。稍后将实现“Rssparse()”函数。

现在我们已经有了页面的第一部分，我们将不得不创建显示 RSS 提要结果的部分。我们希望以列表的形式显示 RSS 提要中的各种标题。这个部分将主要是动态创建的，但我们必须做好准备。与表单一样，我们将向现有字段集添加第二个字段集。

```
<fieldset>
        <legend> Rss Feed 
            <ul id="flux">
            </ul>
</ fieldset>
```

我们添加了 HTML 标签“ul ”,因为我们的内容是标题列表。应用程序框架将插入“李”标签。

## 创建菜单

我们将为我们的应用程序创建一个菜单。App 框架可以轻松创建两种类型的菜单。第一个是应用程序底部的导航栏。第二个是应用程序左侧的菜单。第二个菜单持续显示在大屏幕上，但在较小的屏幕上(智能手机)可以伸缩。我们将创建第二种类型的菜单。

首先，我们将从创建应用程序的第二个页面开始。只需用类“panel”创建第二个 div。

```
<div title="Project" class="panel" id="test">
```

我们现在将通过标签“nav”添加菜单。此标签将放在#content 之外。菜单是由 afui 自动创建的。“标题”类允许我们定义一个部分标题。在 afui 中，应用程序内部页面之间的链接由它们的 id 指定。所以我们的页面测试的调用将由 href ="# test "进行。

```
<nav>
             <div class='title'> Business </div>
<ul>
             <li class="BusinessHome">
                     <a href="#rss"> Home </a>
             </li>
              <li >
                   <a href="#test"> test </a>
             </li>
</ul>
</nav >
```

## DOM 的请求和修改

现在我们有了 GUI 的基础，我们将实现内部特性并使其动态化。

## 选择器

作为第一步，我们将恢复用户在表单中确认 URL 时的操作。创建该表单时，我们将提交按钮定义如下:

```
<input type="submit" class="button" value="Submit" onclick=" Rssparse ()"/>
```

当用户点击提交按钮时，会自动调用`Rssparse()`函数。所以我们需要实施它。我们现在将转向 JavaScript。我们将在 index.html 文件的末尾，在结束标记“/html”之后实现`parserRss()`函数。最初，这个函数将显示用户验证的 URL。

```
<script>
function Rssparse ()
{
alert ($ ("# link1").val ())
}
```

语法与 jQuery 相同。这些选择器将允许我们从它们的 id、它们的类或它们的标签类型中获得不同的 DOM 对象。

请记住，在创建表单的过程中，我们为 text 类型的输入分配了一个 id = link。通过调用选择器$ ("# link1 "),返回的是包含用户输入的 url 的输入文本。一旦选择了这个元素，我们就调用 val()函数，它返回我们所选择的项目的值。$ (" # link1 ")。val()返回用户输入的 URL！

## Ajax 请求

和 jQuery 一样，App Framework 可以发出 Ajax 请求。由于浏览器的跨域限制，App Framework 和 jQuery 一样，不允许跨域 Ajax 请求。在我们的例子中，为了从 URL 获取 RSS 提要，我们希望在不属于我们的服务器上发出请求。为了发出这个请求，我们将使用我们的代理(参见先决条件)。

```
function Rssparse ()
{
$. ajax ( {
url : ' ? server.php url =' + $ ("# link1" ).val ( ),
success : function ( data) {
alert (" ok");
},
error: function () {alert ("fail");}
} ) ;
}
```

这里，我们在代理上发出一个中间请求，向他指定请求的真实 URL。然后，我们的代理将小心地用 PHP 发出请求，并将结果发送给我们。

## Xml 解析器和 DOM 的动态修改

收集了 xml 数据之后，我们现在必须处理它们。App Framework 为我们提供了一个名为 parseXML 方法，它允许从 XML 文本构建一个 DOM 文档对象。我们可以直接在由 parseXML 函数产生的对象上调用 getElementsByTagName 等方法。

```
function Rssparse ()
{
$. ajax ( {
url : ' ? server.php url =' + $ ("# link" ) val ( ).
success : function ( data) {
var xmld = $.parseXML (data) ;
var nodes =xmld.getElementsByTagName ("item" ) ;
$("# flux" ).empty ();

for (i + + var i = 0 ; i < nodes.length )
{
var  titles= nodes[ i ].getElementsByTagName ("title" );
var dom = $ ("# flux" ).append (" <li> <a href=' ' >" + titles [0] childNodes [0]. nodeValue +"< / li >"...) ;
}
}
error: function () {alert ("fail");}
} ) ;
}
```

## 结论

App Framework 提供了适用于移动设备的 jQuery 的大部分特性。其丰富的库和无与伦比的性能，使它成为一个全面的移动 HTML5 框架。这个框架将允许您在很短的时间内构建移动应用程序。

你可以在这里找到文档[http://app-framework-software.intel.com/documentation.php](http://app-framework-software.intel.com/documentation.php)。

</fieldset>

## 分享这篇文章