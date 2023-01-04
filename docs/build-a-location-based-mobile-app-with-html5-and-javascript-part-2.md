# 使用 HTML5 和 Javascript: 2 构建基于位置的移动应用程序

> 原文：<https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-2/>

在本系列的第一篇文章中，我展示了移动应用“我在哪里停车”的功能，并概述了将用于实现它的技术。在本文中，我将向您展示组成应用程序的所有 HTML 页面的源代码，突出每个页面的要点。我还将解释包含的简短样式表。


### 主页

除非您构建显式的定制配置，否则 jQuery Mobile 将使用 AJAX 加载所有的页面，因此“我停车的地方”所需的所有文件，比如 CSS 和 JavaScript 文件，都必须由入口点加载，即`index.html`。

索引页面将在页眉显示标题，在页脚显示署名(实际上是我的名字)，并有一个按钮可以查看作者(同样是我)的其他详细信息。正如第一部分所解释的，索引有三个按钮:一个用于查看和保存当前位置(您停车的位置)，一个用于查看您的当前位置(以及查看到汽车的路线)，一个用于查看之前保存的位置。

说了这么多，首页的代码就会产生下面这个源码。

```
<!DOCTYPE html>
<html>
  <head>
     <meta charset="UTF-8" />
     <meta name="viewport" content="width=device-width, initial-scale=1">
     <title>Where I Parked my Car</title>
     <link rel="stylesheet" href="css/jquery.mobile-1.2.0.min.css" type="text/css" media="all" />
     <link rel="stylesheet" href="css/style.css" type="text/css" media="all" />
     <script src="js/jquery-1.8.3.min.js"></script>
     <script src="js/jquery.mobile.config.js"></script>
     <script src="js/jquery.mobile-1.2.0.min.js"></script>
     <script src="js/cordova-2.0.0.js"></script>
     <script src="http://maps.google.com/maps/api/js?sensor=true"></script>
     <script src="js/functions.js"></script>
     <script src="js/maps.js"></script>
     <script src="js/positions.js"></script>
     <script>
        $(document).one('deviceready', initApplication);
     </script>
  </head>
  <body>
     <div id="welcome-page" data-role="page">
        <header data-role="header">
           <h1>Where I parked my car</h1>
        </header>
        <div id="content" data-role="content">
           <p>
              Where I parked my car lets you bookmark where you parked your car on a map
              and then find a route when you want to return to it. The app will also
              save a log of your saved positions (up to 50).
           </p>
           <a href="map.html?requestType=set" id="set-car-position" data-role="button">Set position</a>
           <a href="map.html?requestType=get" id="find-car" data-role="button">Find car</a>
           <a href="positions.html" id="positions-history" data-role="button">Positions history</a>
        </div>
        <footer data-role="footer">
           <h3>Created by Aurelio De Rosa</h3>
           <a href="aurelio.html" data-icon="info" data-iconpos="notext" class="ui-btn-right">Credits</a>
        </footer>
     </div>
  </body>
</html>
```

你可能会注意到，对于应用程序的页眉和页脚，我使用了新的 HTML5 `<header>`和`<footer>`标签，而不是通用的`<div>`。此外，`<header>`和`<footer>`标签中的所有按钮都使用属性`data-iconpos="notext"`来告诉 jQuery Mobile 隐藏链接的文本。这个属性对于小屏幕非常有用。然而，正如您稍后将看到的，我将为`pagebeforecreate`和`orientationchange`事件附加一个处理程序，因此我将通过编程测试屏幕的大小，如果找到更大的屏幕，该属性将被删除，文本将完整显示。

### 地图页面

地图页面(`map.html`)可能是应用程序中最重要的页面，令人惊讶的是，它也是最简单的页面。事实上，除了包含应用标题的标题之外，它还有一个显示地图的空的`<div>`。(地图将由 Google Maps API 动态生成，因此您不必担心标记问题。)

总之，地图页面的完整代码如下:

```
<!DOCTYPE html>
<html>
   <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <title>Where I parked my car</title>
   </head>
   <body>
      <div id="map-page" data-role="page">
         <header data-role="header">
            <a href="#" data-icon="back" data-rel="back" data-iconpos="notext" title="Go back">Back</a>
            <h1>Where I parked my car</h1>
         </header>
         <div id="content" data-role="content">
            <div id="map">
            </div>
         </div>
      </div>
   </body>
</html>
```

在这一页的`<head>`部分，我没有放入 CSS 和 JavaDcript 文件。我将依赖 jQuery Mobile 的标准 AJAX 机制，所以除了“我停车的地方”的入口点之外，不需要手动将它们添加到页面中。您可能已经注意到，在页面的`<header>`中，我放了一个“返回”按钮。这有两个原因，一个不太明显。第一个也是更直接的是增强可用性。关于第二个原因，请记住，使用 jQuery Mobile 和 Cordova 开发的应用程序可以部署在许多操作系统上。然而，尽管 Cordova APIs 在所有平台上以几乎相同的方式工作，但在物理设备之间还是有一些实质性的差异。例如，Android 设备有一个物理的“返回”按钮，所以如果你想回到上一页，你可以使用它。相反，iPhone 没有物理的“返回”按钮(除了“主屏幕”按钮，它会完全退出应用程序)，所以如果你不在你的界面中建立一个按钮，你的 iPhone 用户将会受到很大的惩罚。

### 职位历史

位置历史页面(`positions.html`)将显示先前保存的位置列表，使您能够搜索特定位置，甚至删除一个或多个项目。该页面有一个空列表，将通过编程填充，稍后您将看到这一点。列表有两个需要分析的关键属性，分别是`data-filter="true"`通知 jQuery Mobile 您希望在列表上方设置一个搜索过滤器，以及`data-split-icon="delete"`在列表项(地址)中显示的文本右侧设置图标。后者用于*分割按钮列表*，这是一种列表，其中每个项目有两个执行不同动作的链接:当用户点击列表项目文本(第一个链接文本)时激活的主动作，以及通过替换次级链接文本的按钮激活的次级动作，位于项目文本之后。

`positions.html`的完整来源如下:

```
<!DOCTYPE html>
<html>
   <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <title>Positions' history</title>
   </head>
   <body>
      <div id="positions-page" data-role="page">
         <header data-role="header">
            <a href="#" data-icon="back" data-rel="back" data-iconpos="notext" title="Go back">Back</a>
            <h1>Positions' history</h1>
         </header>
         <div id="content" data-role="content">
            <ul id="positions-list" data-role="listview" data-inset="true" data-split-icon="delete" data-filter="true">
            </ul>
         </div>
         <footer data-role="footer">
            <h3>Created by Aurelio De Rosa</h3>
            <a href="aurelio.html" data-icon="info" data-iconpos="notext" class="ui-btn-right">Credits</a>
         </footer>
      </div>
   </body>
</html>
```

### 学分页面

信用页面(`aurelio.html`)是最简单的一个，没有特殊的属性或元素需要分析。它仅仅包含作者的一些信息，包括一张照片。你可以看到下面页面的代码:

```
<!DOCTYPE html>
<html>
   <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <title>Aurelio De Rosa</title>
   </head>
   <body>
      <div data-role="page">
         <header data-role="header">
            <a href="#" data-icon="back" data-iconpos="notext" data-rel="back" title="Go back">Back</a>
            <h1>Aurelio De Rosa</h1>
         </header>
         <div id="content" data-role="content">
            <div class="person">
               <img class="photo" src="images/aurelio-de-rosa.png" alt="Photo of Aurelio De Rosa" />
               <dl class="informations">
                  <dt>Email:</dt>
                  <dd><a href="mailto:aurelioderosa@gmail.com">aurelioderosa@gmail.com</a></dd>
                  <dd><a href="mailto:a.derosa@audero.it">a.derosa@audero.it</a></dd>
                  <dt>Twitter:</dt>
                  <dd><a href="https://twitter.com/AurelioDeRosa" target="_blank">@AurelioDeRosa</a></dd>
                  <dt>Information:</dt>
                  <dd>
                     I've a Bachelor degree in Computer Science. I'm a self-employed web developer
                     who have been worked for more than 2 years at the University of Salerno.
                     I'm also a technical writer for the <a href="http://ug.audero.it/">Audero user group</a>
                     and for the SitePoint network on <a href="https://www.sitepoint.com/author/aderosa/">SitePoint.com</a>,
                     <a href="https://www.sitepoint.com/author/aderosa/">JSPro.com</a> and
                     <a href="https://www.sitepoint.com/author/aderosa/">PHPMaster.com</a>.
                     I'm a very IT addicted and my main fields are web technologies, especially HTML, CSS,
                     JavaScript, PHP, jQuery, jQuery Mobile, PhoneGap and Zend Framework.<br />
                  </dd>
               </dl>
            </div>
         </div>
      </div>
   </body>
</html>
```

### 风格调整

jQuery Mobile 在增强页面界面方面做得非常好，尽管如此，仍然有一些风格调整的空间。默认情况下，即使页面的页眉或页脚中没有按钮，框架仍然会在元素的两侧保留一些空间。因此，如果页眉或页脚中的文本——通常是您在列出的源代码中看到的标题——超出了这个空间，jQuery Mobile 会剪切文本并附加一个省略号。然而，这也发生在其他元素上，我并不总是希望它发生。因此，在我在上一篇文章的[中提到的非常短的样式表`style.css`中，我通过应用规则`white-space: normal !important;`对其进行了更改。另一个要讨论的相关点涉及到 id 为`map`的`<div>`。如前所述，它将由谷歌地图 API 以编程方式填充，因此在开始时它没有自己的高度(它只是一个空的`<div>`)。这意味着如果你不设置高度，你将看不到地图，即使谷歌地图 API 正在工作。我把高度设置为 600px，但是你可以根据自己的口味进行更改。](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-1/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 1")

下面列出了讨论的样式表的来源:

```
.ui-header .ui-title,
.ui-footer .ui-title,
.ui-btn-inner *
{
   white-space: normal !important;
}

.photo
{
   display: block;
   margin: 0px auto;
}

dl.informations dt
{
   font-weight: bold;
}

#map
{
   width: 100%;
   height: 600px;
}
```

### 结论

在本系列的这一部分中，我已经展示了“我停车的地方”的所有 HTML 页面，所以现在您可以浏览它们了。然而，目前，页面缺少控制功能和交互的业务逻辑。在下一篇文章中，我将开始解释为应用程序提供动力的一些 JavaScript 文件，并将深入研究 Cordova APIs。

## 分享这篇文章