# 使用 Mozilla 的 PDF 在 JavaScript 中自定义 PDF 呈现。射流研究…

> 原文：<https://www.sitepoint.com/custom-pdf-rendering/>

*这篇文章由[贾尼·哈蒂凯宁](https://www.sitepoint.com/author/jhartikainen)、[弗洛里安·拉普](https://www.sitepoint.com/author/frappl/)、[杰曾·托马斯](https://www.sitepoint.com/author/jthomas/)和[杰夫·史密斯](https://www.sitepoint.com/author/jeffsmith/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

说到网络，几乎所有现代浏览器都支持查看 PDF 文档。但是，本地组件不受开发人员的控制。想象一下，由于 web 应用程序中的一些业务规则，您想要禁用`Print`按钮，或者只显示几个页面，而其他页面需要付费会员。您可以通过使用`embed`标签来使用浏览器的原生 PDF 渲染功能，但是由于您没有编程访问权限，因此您无法控制渲染阶段来满足您的需求。

幸运的是，现在有这样一个工具，由 Mozilla 实验室创建的 [PDF.js](http://mozilla.github.io/pdf.js/) ，它可以在你的浏览器中呈现 PDF 文档。最重要的是，作为开发人员，您可以根据自己的需求完全控制 PDF 文档页面的呈现。这不是很酷吗？是的，它是！

让我们看看 PDF.js 实际上是什么。

## 什么是 PDF.js

PDF.js 是围绕基于 HTML5 的技术构建的可移植文档格式(PDF ),这意味着它可以在现代浏览器中使用，而无需安装任何第三方插件。

PDF.js 已经在许多不同的地方使用，包括一些在线文件共享服务，如 [Dropbox](https://dropbox.com) 、 [CloudUp](https://cloudup.com) 和 [Jumpshare](https://jumpshare.com) ，让用户在线查看 PDF 文档，而不依赖于浏览器的原生 PDF 渲染功能。

毫无疑问，PDF.js 是 web 应用程序中一个非常棒的必备工具，但是集成它并不像看起来那么简单。关于如何集成某些功能，如渲染文本层或注释(外部/内部链接)，以及支持密码保护文件，几乎没有文档可用。

在本文中，我们将探索 PDF.js，并看看我们如何集成不同的功能。我们将涉及的一些主题有:

*   基本集成
*   使用 SVG 渲染
*   渲染文本层
*   放大/缩小

## 基本集成

### 下载必要的文件

PDF.js，顾名思义，是一个 JavaScript 库，可以在浏览器中用来呈现 PDF 文档。第一步是获取 PDF.js 正常工作所需的 JavaScript 文件。以下是 PDF.js 需要的两个主要文件:

*   pdf.js
*   pdf.worker.js

要获取上述文件，如果你是 Node.js 用户，你可以按照 GitHub repo 上提到的[这些步骤](https://github.com/mozilla/pdf.js/#getting-the-code)。使用完`gulp generic`命令后，您将拥有那些必要的文件。

如果你和我一样，对 Node.js 感到不舒服，有一个更简单的方法。您可以使用以下网址下载必要的文件:

*   [https://mozilla.github.io/pdf.js/build/pdf.js](https://mozilla.github.io/pdf.js/build/pdf.js)
*   [https://mozilla.github.io/pdf.js/build/pdf.worker.js](https://mozilla.github.io/pdf.js/build/pdf.worker.js)

上面提到的网址指向 Mozilla 的 PDF.js 的 [live demo](https://mozilla.github.io/pdf.js/web/viewer.html) ，通过这种方式下载文件，你将永远拥有最新版本的库。

### Web Workers 和 PDF.js

您下载的两个文件包含获取、解析和呈现 PDF 文档的方法。`pdf.js`是主库，它本质上有从某个 URL 获取 PDF 文档的方法。但是解析和呈现 PDF 并不是一项简单的任务。事实上，根据 PDF 的性质，解析和呈现阶段可能需要更长的时间，这可能会导致其他 JavaScript 函数被阻塞。

HTML5 引入了 [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) ，用于在独立于浏览器 JavaScript 线程的线程中运行代码。PDF.js 非常依赖 Web Workers，通过将解析和渲染等 CPU 密集型操作从主线程上移走来提高性能。在 Web Workers 中运行处理开销较大的代码是 PDF.js 中的默认设置，但如果需要，可以将其关闭。

### PDF.js 中的承诺

PDF.js 的 JavaScript API 非常优雅且易于使用，并且在很大程度上基于[承诺](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)。对 API 的每个调用都返回一个承诺，这允许干净地处理异步操作。

### 你好世界！

让我们整合一个简单的“Hello World！”PDF 文档。我们在这个例子中使用的文档可以在 http://Mozilla . github . io/pdf . js/examples/learning/hello world . pdf 中找到。

在您的本地 web 服务器下创建一个项目，以便可以使用[http://localhost/pdf js _ learning/index . html](http://localhost/pdfjs_learning/index.html)访问它。PDF.js 调用 Ajax 来获取块中的文档，因此为了使 Ajax 调用在本地工作，我们需要将 PDF.js 文件放在本地 web 服务器中。在本地网络服务器上创建`pdfjs_learning`文件夹后，将上面下载的文件(`pdf.js`、`pdf.worker.js`)放入其中。将以下代码放入`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>PDF.js Learning</title>
  </head>
  <body>
    <script type="text/javascript" src="pdf.js"></script>
  </body>
</html> 
```

如您所见，我们包含了一个到主库文件`pdf.js`的链接。PDF.js 会自动检测你的浏览器是否支持 Web Workers，如果支持，它会尝试从与`pdf.js`相同的位置加载`pdf.worker.js`。如果文件在另一个位置，您可以在包含主库后使用`PDFJS.workerSrc`属性对其进行配置:

```
<script type="text/javascript" src="pdf.js"></script>
<script type="text/javascript"> PDFJS.workerSrc = "/path/to/pdf.worker.js"; </script> 
```

如果您的浏览器不支持 Web Workers，也不必担心，因为`pdf.js`包含了在不使用 Web Workers 的情况下解析和呈现 PDF 文档所需的所有代码，但是根据您的 PDF 文档，它可能会暂停您的主 JavaScript 执行线程。

让我们写一些代码来呈现“Hello World！”PDF 文档。将下面的代码放在`pdf.js`标签下面的`script`标签中。

```
// URL of PDF document
var url = "http://mozilla.github.io/pdf.js/examples/learning/helloworld.pdf";

// Asynchronous download PDF
PDFJS.getDocument(url)
  .then(function(pdf) {
    return pdf.getPage(1);
  })
  .then(function(page) {
    // Set scale (zoom) level
    var scale = 1.5;

    // Get viewport (dimensions)
    var viewport = page.getViewport(scale);

    // Get canvas#the-canvas
    var canvas = document.getElementById('the-canvas');

    // Fetch canvas' 2d context
    var context = canvas.getContext('2d');

    // Set dimensions to Canvas
    canvas.height = viewport.height;
    canvas.width = viewport.width;

    // Prepare object needed by render method
    var renderContext = {
      canvasContext: context,
      viewport: viewport
    };

    // Render PDF page
    page.render(renderContext);
  }); 
```

现在在`body`标签中创建一个 id 为`the-canvas`的`<canvas>`元素。

```
<canvas id="the-canvas"></canvas> 
```

创建完`<canvas>`元素后，刷新你的浏览器，如果你已经把所有东西都放在了正确的位置，你应该会看到 **Hello，world！**在浏览器中打印。但那不是普通的**你好，世界！**。**你好，世界！**你看到的基本上是一个完整的 PDF 文档，通过使用 JavaScript 代码呈现在你的浏览器中。拥抱这份美好！

让我们讨论使 PDF 文档呈现成为可能的上述代码的不同部分。

`PDFJS`是一个全局对象，当您在浏览器中包含`pdf.js`文件时会得到这个对象。该对象是基本对象，包含各种方法。

`PDFJS.getDocument()`是主入口点，所有其他操作都在其中执行。它用于异步获取 PDF 文档，发送多个 Ajax 请求来下载大块文档，不仅速度快，而且效率高。有不同的参数可以传递给这个方法，但最重要的是指向 PDF 文档的 URL。

`PDFJS.getDocument()`返回一个可用于放置代码的承诺，该代码将在 PDF.js 完成获取文档时执行。Promise 的成功回调被传递了一个对象，该对象包含有关获取的 PDF 文档的信息。在我们的例子中，这个参数被命名为`pdf`。

您可能想知道，既然 PDF 文档是成块获取的，那么对于大型文档，是否只有在延迟几秒钟(甚至几分钟)后才会调用成功回调。事实上，一旦获取了第一页所需的字节，回调就会触发。

`pdf.getPage()`用于获取 PDF 文档中的单个页面。当您提供一个有效的页码时，`getPage()`会返回一个承诺，解析后会给我们一个代表所请求页面的`page`对象。`pdf`对象还有一个属性`numPages`，可以用来获取 PDF 文档的总页数。

`scale`是我们希望 PDF 文档页面呈现的缩放级别。

`page.getViewport()`返回给定缩放级别的 PDF 文档页面尺寸。

需要一个具有不同键/值对的对象来将 PDF 页面渲染到画布上。在我们的例子中，我们已经传递了 Canvas 元素的`2d`上下文和从`page.getViewport`方法获得的`viewport`对象。

## 使用 SVG 渲染

PDF.js 支持两种渲染模式。它的默认和流行的渲染模式是基于画布的。但是它也允许您使用 SVG 呈现 PDF 文档。让我们渲染 Hello World！上一个 SVG 示例中的 PDF 文档。

使用下面的代码更新`pdf.getPage()`的成功回调，以查看 PDF.js 的 SVG 呈现。

```
.then(function(page) {

  // Set scale (zoom) level
  var scale = 1.5;

  // Get viewport (dimensions)
  var viewport = page.getViewport(scale);

  // Get div#the-svg
  var container = document.getElementById('the-svg');

  // Set dimensions
  container.style.width = viewport.width + 'px';
  container.style.height = viewport.height + 'px';

  // SVG rendering by PDF.js
  page.getOperatorList()
    .then(function (opList) {
      var svgGfx = new PDFJS.SVGGraphics(page.commonObjs, page.objs);
      return svgGfx.getSVG(opList, viewport);
    })
    .then(function (svg) {
      container.appendChild(svg);
    });

}); 
```

用`<div id="the-svg"></div>`替换 body 标签中的`<canvas>`元素，并刷新浏览器。

如果你正确放置了代码，你会看到 **Hello，world！**正在渲染，不过这次用的是 SVG 而不是 Canvas。继续检查页面的 HTML，您将看到整个呈现都是使用标准 SVG 组件完成的。

如您所见，PDF.js 并没有将您限制在单一的呈现机制上。根据您的需求，您可以使用 Canvas 或 SVG 渲染。在本文的其余部分，我们将使用基于画布的渲染。

## 渲染文本层

PDF.js 使您能够在已经使用 Canvas 呈现的 PDF 页面上呈现文本层。为此，我们需要从 PDF.js GitHub 的 repo 中获取一个额外的 JavaScript 文件。继续下载 [text_layer_builder.js 插件](https://github.com/mozilla/pdf.js/raw/v1.4.20/web/text_layer_builder.js)。我们还需要获取其对应的 CSS 文件， [text_layer_builder.css](https://github.com/mozilla/pdf.js/raw/v1.4.20/web/text_layer_builder.css) 。下载这两个文件，并将它们放在本地服务器上的`pdfjs_learning`文件夹中。

在我们进入实际的文本层渲染之前，让我们得到一个比“Hello World！”更多内容的 PDF 文档举例。我们将要呈现的文档再次取自 Mozilla 的现场演示，这里是。

由于这个文档包含多页，我们需要稍微调整一下代码。首先，删除我们在上一个例子中创建的`<div>`标记，并替换为:

```
<div id="container"></div> 
```

这个容器将用于保存多页 PDF 文档。放置呈现为`Canvas`元素的页面的结构非常简单。在`div#container`内，PDF 的每一页都有自己的`<div>`。`<div>`的`id`属性将具有格式`page-#{pdf_page_number}`。例如，PDF 文档中的第一页会有一个属性设置为`page-1`的`<div>`，第 12 页会有`page-12`。在每个`page-#{pdf_page_number}`div 中，都有一个`Canvas`元素。

我们用下面的代码替换`getDocument()`的成功回调。不要忘记用`http://mozilla.github.io/pdf.js/web/compressed.tracemonkey-pldi-09.pdf`(或者你选择的其他在线 PDF 文档)更新`url`变量。

```
PDFJS.getDocument(url)
  .then(function(pdf) {

    // Get div#container and cache it for later use
    var container = document.getElementById("container");

    // Loop from 1 to total_number_of_pages in PDF document
    for (var i = 1; i <= pdf.numPages; i++) {

        // Get desired page
        pdf.getPage(i).then(function(page) {

          var scale = 1.5;
          var viewport = page.getViewport(scale);
          var div = document.createElement("div");

          // Set id attribute with page-#{pdf_page_number} format
          div.setAttribute("id", "page-" + (page.pageIndex + 1));

          // This will keep positions of child elements as per our needs
          div.setAttribute("style", "position: relative");

          // Append div within div#container
          container.appendChild(div);

          // Create a new Canvas element
          var canvas = document.createElement("canvas");

          // Append Canvas within div#page-#{pdf_page_number}
          div.appendChild(canvas);

          var context = canvas.getContext('2d');
          canvas.height = viewport.height;
          canvas.width = viewport.width;

          var renderContext = {
            canvasContext: context,
            viewport: viewport
          };

          // Render PDF page
          page.render(renderContext);
        });
    }
}); 
```

刷新您的浏览器并等待几秒钟(当新的 PDF 文档在后台获取时),一旦文档完成加载，您应该会在浏览器中看到呈现精美的 PDF 页面。现在我们已经看到了如何渲染多个页面，让我们来讨论如何渲染文本层。

将以下两行添加到`index.html`中，以包含文本层渲染所需的必要文件:

```
<link type="text/css" href="text_layer_builder.css" rel="stylesheet"> 
```

```
<script type="text/javascript" src="text_layer_builder.js"></script> 
```

PDF.js 在多个`<div>`元素中呈现画布上方的文本层，因此最好将所有这些`<div>`元素包装在一个容器元素中。用下面的代码替换`page.render(renderContext)`行，查看文本层的运行情况:

```
page.render(renderContext)
  .then(function() {
    // Get text-fragments
    return page.getTextContent();
  })
  .then(function(textContent) {
    // Create div which will hold text-fragments
    var textLayerDiv = document.createElement("div");

    // Set it's class to textLayer which have required CSS styles
    textLayerDiv.setAttribute("class", "textLayer");

    // Append newly created div in `div#page-#{pdf_page_number}`
    div.appendChild(textLayerDiv);

    // Create new instance of TextLayerBuilder class
    var textLayer = new TextLayerBuilder({
      textLayerDiv: textLayerDiv, 
      pageIndex: page.pageIndex,
      viewport: viewport
    });

    // Set text-fragments
    textLayer.setTextContent(textContent);

    // Render text-fragments
    textLayer.render();
  }); 
```

刷新您的浏览器，这一次您不仅可以看到正在呈现的 PDF 页面，还可以从中选择和复制文本。PDF.js 太酷了！

让我们来讨论上面代码片段的一些重要部分。

与 PDF.js 中的任何其他方法一样，`page.render()`返回一个承诺，当一个 PDF 页面被成功地呈现到屏幕上时，该承诺被解析。我们可以使用成功回调来呈现文本层。

`page.getTextContent()`是一个为特定页面返回文本片段的方法。这也将返回一个承诺，如果成功，将返回该承诺文本片段表示的回调。

`TextLayerBuilder`是一个类，它需要一些我们已经从`pdf.getPage()`中为每个页面获得的参数。`textLayerDiv`参数代表`<div>`，它将被用作一个容器来存放多个`<div>`，每个代表一些特定的文本片段。

新创建的`TextLayerBuilder`实例有两个重要的方法:`setTextContent()`，用于设置`page.getTextContent()`返回的文本片段，`render()`，用于渲染文本层。

如你所见，我们将一个 CSS 类`textLayer`分配给了`textLayerDiv`。这个类有一些样式，可以确保文本片段很好地放在画布元素上，这样用户就可以以自然的方式选择/复制文本。

## 放大/缩小

使用 PDF.js，您还可以控制 PDF 文档的缩放。事实上，缩放非常简单，我们只需要更新`scale`值。根据您想要的系数增加或减少`scale`,以改变缩放级别。这是留给读者的一个练习，但是一定要尝试一下，并在评论中告诉我们你的进展。

## 结论

PDF.js 是一个非常棒的工具，它为我们提供了一个使用 JavaScript 的浏览器原生 PDF 组件的灵活替代方案。这个 API 简单、精确、优雅，你可以随意使用。请在评论中告诉我你打算如何在下一个项目中使用 PDF.js！

## 分享这篇文章