# 使用 jQuery Mobile 在几分钟内构建列表和弹出窗口

> 原文：<https://www.sitepoint.com/working-with-jquery-mobile-1-2-0/>

在我的上一篇文章中，我解释了 jQuery Mobile 1.2.0 中的[新特性，这是流行的移动开发框架的最新版本。本文描述了新设备的兼容性、弹出窗口小部件、可折叠列表和几个不推荐使用的方法。今天，我将向您展示一个完整的实用示例，它将由 jQuery Mobile 最新版本中的新特性和小部件提供支持。](https://www.sitepoint.com/whats-new-in-jquery-mobile-1-2-0/ "What’s New in jQuery Mobile 1.2.0?") 

### 使用 jQuery Mobile 的新工具

讨论的第一个特性是**扩展的设备兼容性**。不幸的是，我所能做的就是展示 jQuery Mobile 增加的兼容性覆盖范围，建议您在一个新支持的设备(如 Tizen 支持的设备)上尝试下面的示例，看看 jQuery Mobile 1.2.0 如何增强页面。下一节提到了新的**弹出窗口小部件**，以及如何创建自己的基本弹出窗口。jQuery Mobile 允许您将任何想要的内容放入浮动在当前页面上方的容器中。弹出窗口小部件有三个可以捕捉和管理的事件:

1.  `popupbeforeposition`:在弹出窗口完成打开准备后，实际打开前触发
2.  `popupafteropen`:弹出窗口完全打开后触发
3.  `popupafterclose`:弹出窗口完全关闭后触发

最终代码将有两个打开弹出窗口的链接。ID 为`helloPopup`的第一个链接显示了一个包含简单文本的基本弹出窗口，而第二个链接不直接调用弹出窗口。前者会在每一个被触发时显示一个警告，以便更好地理解它们的执行顺序和它们被触发的时间。这将使用下面的代码来完成。

```
$("#helloPopup").on(
   "popupbeforeposition popupafteropen popupafterclose",
   function(event) {
      alert(event.type);
   }
);
```

后者将以编程方式打开弹出窗口，其中包含一个以当前窗口为中心的图像，如下所示。

```
$("#imagePopupLink").click(function() {
   $("#imagePopup").popup("open", {positionTo: "window"});
});
```

### jQuery 移动列表管理

jQuery 团队实现的下一组改进是关于列表的。前两个是 **Listview 自动分割器**和**可折叠列表**，它们将被合并到下面创建的示例页面中。自动分割器通过添加字母列表分割器来增强列表的可读性，而可折叠列表旨在充分利用移动设备上有限的屏幕空间。相关代码如下所示。

```
<div data-role="collapsible">
  <h3>Collapsible List with Listview Autodividers</h3>
  <p>
    The following list is inside a collapsible element and it
    uses also the new <code>data-autodividers</code> attribute.
  </p>
  <ul data-role="listview" data-autodividers="true" data-inset="true">
    <li><a href="https://www.sitepoint.com/author/aderosa/">Aurelio De Rosa</a></li>
    <li><a href="http://www.sitepoint.com">BuildMobile.com</a></li>
    <li><a href="http://www.sitepoint.com">JSPro.com</a></li>
    <li><a href="http://www.sitepoint.com">PHPMaster.com</a></li>
    <li><a href="http://www.sitepoint.com">SitePoint.com</a></li>
  </ul>
</div>
```

jQuery Mobile 1.2.0 的第三个主要增强是引入了**只读列表**。这种增加更多的是一种美学上的改变，而不是功能上的改变，所以您必须看到运行中的代码才能理解其中的区别。

最后，jQuery Mobile 的新版本带来了**页面加载废弃的方法和属性**。在下面的代码中，我包含了一个捕捉`mobileinit`事件的函数，使用新的`$.mobile.loader.prototype`属性来设置加载小部件的全局配置。

```
$(document).on("mobileinit", function() {
   $.mobile.loader.prototype.options.text = "Please wait...";
   $.mobile.loader.prototype.options.textVisible = true;
   $.mobile.loader.prototype.options.theme = "e";
});
```

此外，我将为`pageshow`事件附加一个函数，该函数将显示小部件，然后使用新的`$.mobile.loading()`方法在 1.5 秒后隐藏它。

```
$(document).on("pageshow", function() {
   $.mobile.loading("show");
   setTimeout(function() { $.mobile.loading("hide"); }, 1500);
});
```

最后，我将创建一个按钮，单击该按钮将显示相同的加载小部件，但这次它将使用本地配置。当您调用此方法创建特定样式时，本地配置允许您覆盖全局配置。就像之前加载的 widget 一样，1.5 秒后就会隐藏。

```
$("#loadingLink").click(function() {
   $.mobile.loading("show", {
      theme: "b",
      text: "Different message...",
      textVisible: true
   });
   setTimeout(function() { $.mobile.loading("hide"); }, 1500);
});
```

### 把所有的放在一起

正如所承诺的，下面是一个 jQuery 代码示例，它包含了 1.2.0 版本提供的所有上述新技术。下面的例子将利用并扩展上面的片段，向您展示所讨论的特性。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Working with jQuery Mobile 1.2.0</title>
    <link rel="stylesheet" href="https://code.jquery.com/mobile/1.2.0/jquery.mobile-1.2.0.min.css" />
    <script src="https://code.jquery.com/jquery-1.8.2.min.js"></script>
    <script>
      $(document).on("mobileinit", function() {
        $.mobile.loader.prototype.options.text = "Please wait...";
        $.mobile.loader.prototype.options.textVisible = true;
        $.mobile.loader.prototype.options.theme = "e";
      });
    </script>
    <script src="https://code.jquery.com/mobile/1.2.0/jquery.mobile-1.2.0.min.js"></script>
    <script>
      $(document).on("pageinit", function() {
        $("#helloPopup").on("popupbeforeposition popupafteropen popupafterclose", function(event) {
          alert(event.type);
        });
        $("#imagePopupLink").click(function() {
          $("#imagePopup").popup("open", {positionTo: "window"});
        });
        $("#loadingLink").click(function() {
          $.mobile.loading("show", {
            theme: "b",
            text: "Different message...",
            textVisible: true
          });
          setTimeout(function() { $.mobile.loading("hide"); }, 1500);
        });
      });
      $(document).on("pageshow", function() {
        $.mobile.loading("show");
        setTimeout(function() { $.mobile.loading("hide"); }, 1500);
      });
    </script>
  </head>
  <body>
    <div data-role="page" id="examplePage">
      <header data-role="header">
        <h1>What's new JQM 1.2.0</h1>
      </header>
      <div data-role="content" role="main">
        <p>
          This page will show you some of the new features, widgets and
          method of <b>jQuery Mobile 1.2.0</b> explained in the article
          "<a href="https://www.sitepoint.com/whats-new-in-jquery-mobile-1-2-0/"
             title="What's New in jQuery Mobile 1.2.0?"
             target="_blank">What's New in jQuery Mobile 1.2.0?</a>".
        </p>
        <div data-role="collapsible-set">
          <div data-role="collapsible">
            <h3>Popups</h3>
            <a href="#helloPopup" data-role="button" data-rel="popup">Basic Popup</a>
            <a href="#" data-role="button" data-rel="popup" id="imagePopupLink">Image Popup</a>
          </div>
          <div data-role="collapsible">
            <h3>Collapsible List with Listview Autodividers</h3>
            <p>
              The following list is inside a collapsible element and it
              uses also the new <code>data-autodividers</code> attribute.
            </p>
            <ul data-role="listview" data-autodividers="true" data-inset="true">
              <li><a href="https://www.sitepoint.com/author/aderosa/">Aurelio De Rosa</a></li>
              <li><a href="http://www.sitepoint.com">BuildMobile.com</a></li>
              <li><a href="http://www.sitepoint.com">JSPro.com</a></li>
              <li><a href="http://www.sitepoint.com">PHPMaster.com</a></li>
              <li><a href="http://www.sitepoint.com">SitePoint.com</a></li>
            </ul>
          </div>
          <div data-role="collapsible">
            <h3>Read-only Lists</h3>
            <h4>Articles published</h4>
            <ul data-role="listview" data-inset="true">
              <li>Aurelio De Rosa<span class="ui-li-count">3</span></li>
              <li>John Doe<span class="ui-li-count">2</span></li>
              <li>Jason Parker<span class="ui-li-count">5</span></li>
            </ul>
          </div>
          <div data-role="collapsible">
            <h3>Loading</h3>
            <a href="#" data-role="button" id="loadingLink">Keep loading...</a>
          </div>
        </div>
      </div>
      <div data-role="popup" id="helloPopup">
        <header data-role="header">
          <h1>Popup</h1>
        </header>
        <div data-role="content">
          <p>Hello! I'm a brand new popup widget.</p>
          <a href="#" title="Go back" data-role="button" data-rel="back">Close</a>
        </div>
      </div>
      <div data-role="popup" id="imagePopup">
        <a href="#" data-rel="back" data-role="button" data-icon="delete"
          data-iconpos="notext" class="ui-btn-right">Close</a>
        <div data-role="content">
          <img src="http://cdn.buildmobile.com/wp-content/themes/buildmobile-img/logo.png" alt="BuildMobile logo" />
        </div>
      </div>
      <footer data-role="footer">
        <h3>Aurelio De Rosa</h3>
      </footer>
    </div>
  </body>
</html>
```

### 结论

现在，您应该能够掌握 jQuery 团队在 jQuery Mobile 的新版本中实现的最新变化。这些可以很好地服务于你的移动应用和网站，并有望推动你的下一个移动项目达到新的高度。

## 分享这篇文章