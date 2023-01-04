# 用聚合物构建图像库组件

> 原文：<https://www.sitepoint.com/building-a-image-gallery-component-with-polymer/>

Web 组件正在成为 Web 应用程序开发的未来趋势。它们允许我们将 HTML 标记、脚本和样式组合成一个可重用的组件。这些组件是浏览器的一部分，因此不需要像 jQuery 这样的外部 JavaScript 库来提供其功能。据[维基百科](https://en.wikipedia.org/wiki/Web_Components)报道，

> Web 组件是目前由 Google 工程师作为 W3C 规范开发的一组标准，允许在 web 文档和 web 应用程序中创建可重用的小部件或组件。他们背后的意图是将基于组件的软件工程引入万维网。组件模型允许单独 HTML 元素的封装和互操作性。

简而言之，web 组件解决了 Web 页面中元素的复杂性，并提供了更简单、更容易理解的元素结构。HTML 已经提供了一系列内置标签，比如标题、段落、列表等等。然而，在某些情况下，现有的标签不足以为大型 Web 应用程序提供正确的支持，这就是 Web 组件的用武之地。一些库，最著名的是 [Polymer](https://github.com/polymer/polymer) ，正在用 Polyfill web 组件使 Web 组件在不支持的浏览器中可用。

在本教程中，我们将学习如何用 Polymer 版创建一个图库组件。[本文提供的所有代码在 GitHub](https://github.com/sitepoint-editors/Polymer-Image-Gallery-Component) 上都有。

## 聚合物概论

图片库是网站开发中常用的。一般来说，开发人员倾向于使用现有的图库，他们经常不得不面对由这些图库生成的元素结构的复杂性这一令人厌烦的问题。下面的代码预览了一个非常基本的图像库的结构。

```
<div id="gallery-panel" class="gallery-panel">
    <div class="slides">
        <div id="links" name="links">
            <img src="images/thumbnails/img01.jpg" alt="Title 1">
            <img src="images/thumbnails/img02.jpg" alt="Title 2">
            <img src="images/thumbnails/img03.jpg" alt="Title 3">
            <img src="images/thumbnails/img04.jpg" alt="Title 4">
            <img src="images/thumbnails/img05.jpg" alt="Title 5">
        </div>
    </div>
    <div class="modal fade">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header"> <a class="close"><button type="button" class="close" aria-hidden="true"  >X</button></a>

                     <h4 class="modal-title"></h4>

                </div>
                <div class="modal-body next">
                    <img class='modal-img' />
                </div>
                <div class="modal-footer">
                    <button id="previous" type="button" class="btn btn-default pull-left prev">Previous</button>
                    <button id="next" type="button" class="btn btn-default next">Next</button>
                </div>
            </div>
        </div>
    </div>
</div>
```

尽管这是一个代码片段，展示了一个基本图像库的结构，但很明显它并没有想象中那么简单。我们可能不得不为网页中的每个图库重复相同的代码集，因此网页变得非常大并且难以管理。

以下是 Polymer 通过使用 web 组件提供解决方案的位置和方式。Polymer 是为构建定制 web 组件而创建的库。有几个库可用于创建 web 组件，但浏览器兼容性是大多数库尚未解决的问题。Polymer 通过提供能够在不兼容的浏览器中管理 web 组件的 polyfills 提供了最佳解决方案。[你可以在这里找到更多关于聚合物及其用途的信息](https://www.polymer-project.org/1.0/)。

当使用 Polymer 作为 web 组件构建图库时，我们的代码应该如下所示。

```
<simple-gallery>
    <img src="images/thumbnails/img01.jpg" alt="Title 1">
    <img src="images/thumbnails/img02.jpg" alt="Title 2">
    <img src="images/thumbnails/img03.jpg" alt="Title 3">
    <img src="images/thumbnails/img04.jpg" alt="Title 4">
    <img src="images/thumbnails/img05.jpg" alt="Title 5">
</simple-gallery>
```

正如你所看到的，我们的代码变得简单多了，它只有基本的标签。所有其他复杂的编码对用户来说都是隐藏的，使其高度可重用。现在，让我们开始用聚合物构建画廊组件。

## 如何安装聚合物

可以使用 [Bower](http://bower.io/) 运行以下命令来安装 Polymer 及其所有依赖项:

```
bower install --save Polymer/polymer#^1.1.0
```

这将在名为`bower_components`的文件夹中安装聚合物库和 web 组件 polyfills。

### 规划图像库组件

在向前迈出一步之前，我们必须确定一个基本图库所需的特性。我们计划为一个图片库创建一个 web 组件，我们需要为图片库组件创建一个单独的文件。下面，您可以找到一个列表，列出了我们在正确构建图库时必须考虑的几点:

*   用于显示图库图像的 HTML 标记；
*   带有大图像的模式弹出窗口的 HTML 标记；
*   图库组件的 CSS 样式；
*   用于下一个、上一个和关闭事件的按钮；
*   处理画廊打开、关闭和遍历的 JavaScript。

与普通的 web 页面不同，所有这些都是在一个组件文件中定义的。因此，所有图库代码都独立于其他组件，并且可以在多个地方重用。也就是说，我们可以开始构建主要的 web 页面和图像库组件。

### 用聚合物定义画廊元素

首先，我们必须创建我们的网站主页，以包括网络组件。我们可以在项目根文件夹中创建一个名为`index.html`的页面。然后，我们需要包括必要的聚合物文件和图片画廊的 HTML 标记。以下代码片段显示了如何继续:

```
<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>Polymer</title>
        <script src="bower_components/webcomponentsjs/webcomponents.js"></script>
        <link rel="import" href="bower_components/polymer/polymer.html">
        <link rel="import" href="simple-gallery.html">
    </head>
    <body>
        <simple-gallery>
            <img data-original="images/img01.jpg" data-index='s1' src="images/thumbnails/img01.jpg" alt="Title 1">
            <img data-original="images/img02.jpg" data-index='s2' src="images/thumbnails/img02.jpg" alt="Title 2">
            <img data-original="images/img03.jpg" data-index='s3' src="images/thumbnails/img03.jpg" alt="Title 3">
        </simple-gallery>
    </body>
</html>
```

首先，我们需要使用`link`元素从`bower_components/polymer`文件夹导入`polymer.html`文件。然后，我们需要从`bower_components/webcomponentsjs`文件夹中添加一个名为`webcomponents.js`的文件。这是负责在不兼容的浏览器中处理自定义元素的文件。如果你熟悉 Polymer 0.5，你可能知道这个文件叫`platform.js`。这在最新的 1.0 版本中已经被取代，现在一个名为`webcomponents.js`的文件处理相同的功能。最后，我们需要使用一个`link`元素导入我们的自定义图库组件。在我们的例子中，我们将自定义组件命名为*简单图库*。

现在，我们可以开始使用一些 HTML 标签和 web 组件来定义我们的布局结构。我们添加了一个名为*简单图库*的定制组件，在开始和结束标签中包含所有图库图片。您可能会注意到，我们使用了几个名为`data-original`和`data-index`的数据属性。这些属性用于简化处理原始图像的 URL 和图库中图像的索引的过程。当然，即使没有这些数据，也可以通过操作 DOM 来创建图库。

### 创建图像库组件

我们已经将必要的文件导入到我们的主页中，现在我们准备创建 gallery 组件。第一步是在项目文件夹中为 gallery 组件创建一个名为`simple-gallery.html`的新文件。我们可以添加以下代码来定义画廊组件的结构:

```
<link rel="import" href="bower_components/polymer/polymer.html">

<dom-module id="simple-gallery" >
   <style> /* Styles for the Gallery Component */ </style>

   <script> HTMLImports.whenReady(function () {
          (function () {
              var current_index = 0;
              var image_length = 0;

              Polymer({
                  is: "simple-gallery",
                  ready: function () {},
                  load_popup: function (e, detail, sender) {},
                  next: function () {},
                  prev: function () {},
                  close: function () {},
              });
          })();

      }); </script>

   <template></template>
 </dom-module>
```

首先，我们必须像往常一样包含`polymer.html`文件。然后，我们开始定义图库组件。Polymer 1.0 使用`dom-module`元素来定义新的组件。组件名应该被用作`dom-module`元素的`id`属性。

我们使用`style`元素来定义组件所需的所有样式。甚至这个版本也比 0.5 版本有所改变，在 0.5 版本中，我们在`template`元素中使用了`style`。在 1.0 版本中，它现在作为一个独立的标签放在了`template`元素之外。图库组件的 HTML 标记位于`template`元素内部。最后，我们可以初始化`HTMLImports.whenReady`回调函数内部的 Polymer 元素。该函数确保在执行代码之前加载所有导入的文档。

我们必须使用带有组件名( *simple-gallery* )的`is`属性来注册一个聚合物组件。此过程不同于使用 0.5 版的过程，在 0.5 版中，我们使用以下语法:

```
Polymer('simple-gallery', {});
```

我们必须为自定义组件定义必要的功能。我们有五个功能:`ready`、`load_popup_`、`prev`、`next`和`close`。让我们逐一确定它们的功能:

*   `ready`:这是一个聚合体功能，一旦聚合体对象准备好就执行。我们使用这个函数来初始化组件中的任何特性。
*   `load_popup`:该函数用于在模态弹出窗口中加载原始图像。
*   `prev`:该功能用于向后遍历图库图像
*   `next`:该功能用于向前遍历图库图像
*   `close`:该功能用于关闭模态弹出窗口

在下一节中，我们可以根据我们在这一节中创建的结构开始实现 gallery 组件。

### 创建图像库模板

模板是 web 组件中最重要的部分之一。在这一节中，我们将看到哪些是显示给最终用户的标记。终端用户看不到`template`中的内容，因为用户只能看到源代码中的`simple-gallery`标记，而其他内部元素只有使用特殊的浏览器工具才能看到。

此时，我们必须在这个`template`标签中实现显示图像的 HTML 和模式弹出的 HTML。让我们看看图库组件模板的代码:

```
<div id="gallery-panel" class="gallery-panel">
    <!-- The container for the modal slides -->
    <div class="slides">
        <div id="links" name="links"></div>
    </div>
    <!-- The modal dialog, which will be used to wrap the lightbox content -->
    <div class="modal fade">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header"> <a class="close"><button type="button" class="close" aria-hidden="true" on-click="close" >X</button></a>

                     <h4 class="modal-title"></h4>

                </div>
                <div class="modal-body next">
                    <img class='modal-img' />
                </div>
                <div class="modal-footer">
                    <button id="previous" type="button" class="btn btn-default pull-left prev" on-click="prev">Previous</button>
                    <button id="next" type="button" class="btn btn-default next" on-click="next">Next</button>
                </div>
            </div>
        </div>
    </div>
</div>
```

组件模板包含两个部分。第一个是用具有类`slides`的`div`元素定义的。这个容器将被我们从 simple-gallery 组件提供的图像填充。默认情况下，该模板部件对用户可见。第二部分从`div`和`class="modal fade"`开始。这将用于模式弹出窗口，因此默认情况下对用户隐藏。我们也有必要的按钮，上一个，下一个和关闭聚合物`on-click`呼叫。Polymer 使用`on-event`语法来定义元素上的事件。

既然我们已经为模板准备好了所有必要的标记，我们的下一个任务将是在`style`标签中设置元素的样式。如果需要，还可以将外部样式表导入到组件中。我们不打算在这里包括所有的风格。您可以在源代码文件夹中找到我们组件的所有样式。以下代码包含我们组件的一些示例样式。

```
 .modal {
        display: none;
        width: 100%;
        height: 100%;
        overflow: hidden;
        background: rgba(0, 0, 0, 0.9) none repeat scroll 0 0;
        overflow: hidden;
        position: fixed;
        z-index: 999999;
        top:0;
    } 
```

我们已经使用了普通的 CSS 类和选择器。因此这些选择器将成为 gallery 元素的派生选择器。比如`.modal`类会写成`.modal.simple-gallery`。你可以使用聚合物`:host`来瞄准特定的组成元素及其祖先。[你可以在这里找到更多关于如何设计组件风格的信息](https://www.polymer-project.org/1.0/docs/devguide/styling.html)。

### 实现图库功能

在上一节中，我们为 gallery 组件定义了五个函数。让我们开始逐一实现这些功能。

#### 就绪功能

我们使用`ready`函数获取 simple-gallery 元素中定义的图像，并将它们添加到模板中的`#links`容器中。下面的代码包含第一个函数的实现。

```
ready: function () {
    var images = Polymer.dom(this).querySelectorAll('img');
    var container = this.$.links;

    for (var img in images) {
        images[img].addEventListener('click', this.load_popup);
        container.appendChild(images[img]);
    }
}
```

我们通过使用`Polymer.dom(this)`对象上的`querySelectorAll`函数来选择组件内的所有图像。然后我们遍历每个元素并将其添加到`#links`容器中，同时定义一个自定义的点击事件来调用`load_popup`函数。

#### load_popup 函数

此函数用于在单击图库中的图像时打开模式弹出窗口并显示原始图像。下面的代码显示了如何实现它:

```
load_popup: function (e, detail, sender) {
    e.preventDefault();
    var links = document.getElementById('links');
    image_length = links.getElementsByTagName('img').length;

    var image_url = e.target.getAttribute('data-original');
    var modalbody = document.getElementsByClassName("modal-body")[0];
    var modal_img = modalbody.getElementsByTagName('img')[0];
    modal_img.setAttribute("src", image_url);
    var modal = document.getElementsByClassName("modal")[0];
    modal.style.display = 'block';

    current_index = parseInt(e.target.getAttribute('data-index').replace("s", ""));
    return false;
}
```

我们可以使用`e.target`得到被点击的图像。然后，我们使用`data-original`属性获取原始图像 URL，并使用一些 DOM 操作将图像添加到模态窗口中，并为用户打开模态窗口。我们还可以使用`data-index`属性存储所选图像的索引。通常，我们倾向于使用 jQuery 等库来进行这种类型的 DOM 操作。然而，我们在这个例子中使用了普通的 JavaScript 函数，我将在下一节解释避免使用 jQuery 的原因。

#### 下一个功能

该函数用于遍历弹出窗口中的图库图像。弹出窗口打开后，我们可以使用下一个和上一个按钮遍历图库，而不是单击图库中的每个缩略图。我们来看一下`next`函数的实现。

```
next: function () {
    current_index = current_index + 1;
    if (current_index == (image_length + 1)) {
        current_index = 1;
    }
    var current_image = document.querySelectorAll("[data-index='s" + current_index + "']");
    image_url = current_image[0].getAttribute('data-original');
    var modalbody = document.getElementsByClassName("modal-body")[0];
    var modal_img = modalbody.getElementsByTagName('img')[0];
    modal_img.setAttribute("src", image_url);
}
```

我们使用从`load_poup`函数生成的当前索引从图库中获取下一张图片。该图像由`data-original`属性标识，并被替换到现有的模式窗口图像中。这个过程继续进行，一旦我们到达终点，索引就被设置为 1 以从头开始。`prev`函数的实现也与此类似，因此在此不再赘述。您可以在源代码文件夹中找到它。

有了这最后一个片段，我们已经用 Polymer 完成了基本的图库组件。你可以使用源代码文件来看看它是如何工作的。这里有。

### 使用现有图库插件

图片库是大多数网站的常见组件。因此，有大量的纯 JavaScript 和 jQuery 库可以用来创建图片库。您可能想知道为什么我们应该创建一个图片库，而不是将 jQuery 图片库转换成 web 组件。这将是更容易和更好的选择。然而，问题是许多 jQuery 插件不能将 Polymer 作为 web 组件使用。这些插件经常产生冲突，因此我们不得不从头开始构建它们。

确定不推荐在 Polymer web 组件中使用 jQuery 插件或 jQuery 代码的原因非常重要。有两种类型的 DOM 元素，称为本地 DOM 和影子 DOM:

*   本地 DOM:这些元素对用户是可见的。在这个场景中，gallery 组件中的所有图像都是本地 DOM 的一部分；
*   **Shadow DOM** :这些元素对用户不可见，由 web 组件生成。在这个场景中，图库容器和弹出窗口是阴影 DOM。

大多数 jQuery 插件在本地 DOM 上工作，并期望元素在本地 DOM 中。但是从 web 组件生成的元素被放在影子 DOM 中，因此 jQuery 插件不能识别这些元素。因此，不建议在 web 组件中使用 jQuery 插件或 jQuery 代码。我建议您使用普通的 JavaScript 函数来代替 jQuery 功能。考虑到可用的 jQuery 插件的数量，这似乎是一个限制，但是 web 组件正在快速构建，很快我们就会看到它们取代大多数 jQuery 插件。

## 结论

我们预计 web 组件将成为应用程序开发的未来，这要归功于它们以不必要的复杂性创建和管理网页的强大方式。然而，它们的实现仍处于早期阶段，尚未成为严格的标准。即使像 Polymer 这样的库使得在不兼容的浏览器中使用这些组件成为可能，您仍然可能会发现问题，尤其是在移动浏览器中。

是否在实际应用中使用它们取决于您和您的具体情况。就我个人而言，我希望 web 组件能很快被广泛使用。

## 分享这篇文章