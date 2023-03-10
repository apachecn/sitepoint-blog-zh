# ASP.NET 4.5 捆绑和缩小更新

> 原文：<https://www.sitepoint.com/asp-net-4-5-bundling-and-minification-update/>

最近我写了一篇关于 ASP.NET 4.5 中新的捆绑和缩小特性的文章。开箱即用，您无需做任何事情就可以获得捆绑和精简。如果你错过了，你可以在这里找到它。

自从那篇文章之后，ASP.NET 4.5 有了一些变化。以前，您需要在 Global.asax 文件中手动添加以下代码行:

```
protected void Application_Start() 
{
    BundleTable.Bundles.EnableDefaultBundles();
} 
```

自从更新到 ASP.NET 4.5，你不再需要手动添加那行代码。当你开始一个新项目时，它会自动出现。

```
protected void Application_Start()
{
    BundleTable.Bundles.RegisterTemplateBundles();
} 
```

引用捆绑和缩小的 CSS 和 JavaScript 文件的代码也已经更新。以前，要引用一个包，您需要添加以下代码:

```
 <script src="scripts/js"></script>
<link href="content/css" rel="stylesheet" /> 

```

这是假设您的 JavaScript 文件在一个名为 *scripts* 的文件夹中，样式表在一个名为 *content* 的文件夹中。这仍然适用于这个新版本。以这种方式引用文件的问题是，当您更新文件时，旧版本仍然会被您的浏览器或 web 代理缓存。默认情况下，文件缓存 1 年，这是伟大的，如果你从来没有更新它们！微软已经解决了这个问题，允许你通过 [ResolveBundleUrl](http://bit.ly/zLWTNY "ResolveBundleUrl") 引用捆绑和压缩的文件。

```
 <script src="@System.Web.Optimization.BundleTable.Bundles.ResolveBundleUrl("~/Scripts/js")"></script>
<link href="@System.Web.Optimization.BundleTable.Bundles.ResolveBundleUrl("~/Content/css")" rel="stylesheet" type="text/css" /> 
```

使用 [ResolveBundleUrl](http://bit.ly/zLWTNY "ResolveBundleUrl") 的好处是它在文件名的末尾附加了一个神奇的字符串。这个神奇的字符串只有在文件改变时才会改变，这意味着文件将被缓存 1 年或直到它们改变。如果你通过谷歌浏览器中的开发者工具来检查这个页面，你会看到神奇的字符串。

[![](img/ce8862185716a46e5700c611df9eb783.png "asprequesturl")](https://www.sitepoint.com/wp-content/uploads/2012/03/asprequesturl.png)

您可以使用关键字包含一个*@来消除完全限定系统的需要。优化程序集。*

```
 @using System.Web.Optimization 

```

在使用 ASP.NET 4.5 进行开发时，一定要记住这个特性的用法。

## 分享这篇文章