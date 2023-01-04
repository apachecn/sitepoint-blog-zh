# 奥雷利亚历险记:创建自定义 PDF 查看器

> 原文：<https://www.sitepoint.com/aurelia-custom-pdf-viewer-component/>

这篇文章由 [Vildan Softic](https://www.sitepoint.com/author/vildansoftic) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

在 web 应用程序中处理 PDF 文件一直是一件痛苦的事情。如果你幸运的话，你的用户只需要下载这个文件。但是，有时你的用户需要更多。在过去，我很幸运，但是这一次，我们的用户需要我们的应用程序显示 PDF 文档，这样他们就可以保存与每个页面相关的元数据。以前，人们可能通过运行在浏览器中的昂贵的 PDF 插件，如 Adobe Reader 来实现这一点。然而，经过一段时间的试验，我发现了一种更好的方法来将 PDF 查看器集成到 web 应用程序中。今天，我们将看看如何使用 [Aurelia](http://aurelia.io) 和 [PDF.js](https://mozilla.github.io/pdf.js/) 来简化 PDF 处理。

## 概述:目标

我们今天的目标是在 Aurelia 中构建一个 PDF 查看器组件，允许查看器和我们的应用程序之间的双向数据流。我们有三个主要要求。

1.  我们希望用户能够加载文档，滚动，放大和缩小，性能良好。
2.  我们希望能够将查看器属性(比如当前页面和当前缩放级别)双向绑定到应用程序中的属性。
3.  我们希望这个查看器是一个可重用的组件；我们希望能够将多个查看器同时放入我们的应用程序中，而不会发生冲突，也不需要太多的努力。

你可以在我们的 [GitHub repo](https://github.com/sitepoint-editors/aurelia-pdfjs) 上找到本教程的代码，也可以在这里找到完成代码的[演示。](https://sitepoint-editors.github.io/aurelia-pdfjs/)

## PDF.js 简介

js 是一个 JavaScript 库，由 [Mozilla 基金会](https://www.mozilla.org/en-US/foundation/)编写。它加载 PDF 文档，解析文件和相关元数据，并将页面输出呈现到一个 DOM 节点(通常是一个`<canvas>`元素)。该项目包含的默认查看器支持 Chrome 和 Firefox 中的嵌入式 PDF 查看器，可以用作独立页面或资源(嵌入在 iframe 中)。

无可否认，这很酷。这里的问题是，尽管默认的浏览器有很多功能，但它是作为一个独立的网页来设计的。这意味着虽然它可以集成到 web 应用程序中，但本质上它必须在 iframe 沙箱中运行。默认的查看器被设计成通过它的查询字符串来获取配置输入，但是在初始加载之后，我们不能轻易地更改配置，并且我们不能轻易地从查看器中获取信息和事件。为了将它与一个 Aurelia web 应用程序集成——包括事件处理和双向绑定——我们需要创建一个 Aurelia 自定义组件。

*注意:如果你需要复习 PDF.js，看看我们的教程:[用 Mozilla 的 PDF.js](https://www.sitepoint.com/custom-pdf-rendering/) 用 JavaScript 自定义 PDF 渲染 *

## 实施

为了实现我们的目标，我们将创建一个 Aurelia 自定义元素。然而，我们不会将默认的查看器放到我们的组件中。相反，我们将创建自己的查看器，它与 PDF.js 核心和查看器库挂钩，这样我们就可以最大限度地控制我们的可绑定属性和呈现。对于我们最初的概念验证，我们将从 [skeleton Aurelia 应用程序](https://github.com/aurelia/skeleton-navigation/tree/master/skeleton-esnext)开始。

### 样板文件

你可以看到，如果你点击上面的链接，skeleton 应用程序中有很多文件，其中很多我们并不需要。为了让生活更简单，我们准备了一个[骷髅](https://github.com/sitepoint-editors/aurelia-pdfjs/tree/skeleton/)的精简版，我们在上面添加了一些东西:

*   将我们的 PDF 文件复制到`dist`文件夹(Aurelia 用于捆绑)是一项艰巨的任务。
*   PDF.js 依赖项已添加到`package.json`。
*   在应用程序的根中，`index.html`和`index.css`已经接受了一些初始样式。
*   我们将要工作的文件的空副本已经被添加。
*   文件`src/resources/elements/pdf-document.css`包含一些定制元素的 CSS 样式。

因此，让我们启动并运行应用程序。

首先，确保 gulp 和 jspm 在全球范围内安装:

```
npm install -g gulp jspm 
```

然后克隆骨架并将`cd`放入其中。

```
git clone git@github.com:sitepoint-editors/aurelia-pdfjs.git -b skeleton
cd aurelia-pdfjs 
```

然后安装必要的依赖项:

```
npm install
jspm install -y 
```

最后运行`gulp watch`并导航到 [http://localhost:9000](http://localhost:9000) 。如果一切按计划进行，您应该会看到一条欢迎消息。

### 一些更多的设置

接下来要做的是找到几个 pdf 文件，并把它们放在`src/documents`中。将它们命名为`one.pdf`和`two.pdf`。为了最大限度地测试我们的自定义组件，如果其中一个 pdf 文件非常长就好了，例如可以在[古腾堡项目](https://www.gutenberg.org/)中找到的《战争与和平》。

准备好 pdf 文件后，打开`src/app.html`和`src/app.js`(按照惯例，`App`组件是 Aurelia 应用程序的根)并用这两个文件的内容替换那里的代码: [src/app.html](https://github.com/sitepoint-editors/aurelia-pdfjs/blob/master/src/app.html) 和 [src/app.js](https://github.com/sitepoint-editors/aurelia-pdfjs/blob/master/src/app.js) 。在本教程中，我们不会触及这些文件，但是代码被很好地注释了。

Gulp 将自动检测这些变化，您应该会看到我们的应用程序渲染的用户界面。这就是设置。现在节目开始了…

## 创建 Aurelia 自定义元素

我们希望创建一个可以在任何 Aurelia 视图中使用的插件组件。由于 Aurelia 视图只是包装在 HTML5 [template](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) 标签中的 HTML 片段，因此示例可能如下所示:

```
<template>
  <require from="resources/elements/pdf-document"></require>
  <pdf-document url.bind="document.url"
                page.bind="document.pageNumber"
                lastpage.bind="document.lastpage"
                scale.bind="document.scale">
  </pdf-document>
</template> 
```

`<pdf-document>`标签是定制元素的一个例子。它和它的属性(比如`scale`和`page`)并不是 HTML 固有的，但是我们可以使用 Aurelia 自定义元素来创建它。使用 Aurelia 的基本构建块:视图和视图模型，可以直接创建定制元素。因此，我们将首先搭建我们的视图模型，命名为`pdf-document.js`，如下所示:

```
// src/resources/elements/pdf-document.js

import {customElement, bindable, bindingMode} from 'aurelia-framework';

@customElement('pdf-document')

@bindable({ name: 'url' })
@bindable({ name: 'page', defaultValue: 1, defaultBindingMode: bindingMode.twoWay })
@bindable({ name: 'scale', defaultValue: 1, defaultBindingMode: bindingMode.twoWay })
@bindable({ name: 'lastpage', defaultValue: 1, defaultBindingMode: bindingMode.twoWay })

export class PdfDocument {
  constructor () {
    // Instantiate our custom element.
  }

  detached () {
    // Aurelia lifecycle method. Clean up when element is removed from the DOM.
  }

  urlChanged () {
    // React to changes to the URL attribute value.
  }

  pageChanged () {
    // React to changes to the page attribute value.
  }

  scaleChanged () {
    // React to changes to the scale attribute value.
  }

  pageHandler () {
    // Change the current page number as we scroll
  }

  renderHandler () {
    // Batch changes to the DOM and keep track of rendered pages
  }
} 
```

这里要注意的主要是`@bindable`装饰器；通过使用配置`defaultBindingMode: bindingMode.twoWay`创建可绑定的属性，并通过在我们的视图模型中创建处理程序方法(`urlChanged`、`pageChanged`等)，我们可以监视并响应我们放置在自定义元素上的相关属性的变化。这将允许我们简单地通过改变元素的属性来控制我们的 PDF 查看器。

然后，我们将创建初始视图来与我们的视图模型配对。

```
// src/resources/elements/pdf-document.html

<template>
  <require from="./pdf-document.css"></require>

  <div ref="container" class="pdf-container">
    My awesome PDF viewer.
  </div>
</template> 
```

## 集成 PDF.js

PDF.js 分为三个部分。有核心库，它处理 PDF 文档的解析和解释；显示库，在核心层之上构建可用的 API 最后是 web 浏览器插件，这是我们之前提到的预建网页。出于我们的目的，我们将通过 display API 使用核心库；我们将建立自己的浏览器。

display API 导出一个名为`PDFJS`的库对象，这允许我们设置一些配置变量，并使用`PDFJS.getDocument(url)`加载我们的文档。API 是完全异步的——它发送和接收来自 web worker 的消息，因此它在很大程度上建立在 JavaScript 承诺之上。我们将主要使用从`PDFJS.getDocument()`方法异步返回的 PDFDocumentProxy 对象，以及从`PDFDocumentProxy.getPage()`异步返回的 PDFPageProxy 对象。

虽然文档有点稀疏，但 PDF.js 有一些创建基本查看器的例子，这里是[这里是](https://github.com/mozilla/pdf.js/tree/master/examples/helloworld)，这里是[这里是](https://github.com/mozilla/pdf.js/tree/master/examples/learning)。我们将在这些例子的基础上构建我们的定制组件。

### Web worker 集成

PDF.js 使用一个 [web worker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 来卸载它的渲染任务。由于 web worker 在浏览器环境中运行的方式(它们实际上是沙箱化的)，我们被迫使用 JavaScript 文件的直接文件路径来加载 web worker，而不是通常的模块加载器。幸运的是，Aurelia 提供了一个加载器抽象，这样我们就不必引用静态文件路径(当我们捆绑应用程序时，静态文件路径可能会改变)。

如果你按照我们的版本进行回购，你将已经安装了 [pdfjs-dist](https://www.npmjs.com/package/pdfjs-dist) 包，否则，你现在就需要这样做(比如用 jspm `jspm install npm:pdfjs-dist@^1.5.391`)。然后我们将使用 Aurelia 的依赖注入模块注入 Aurelia 的加载器抽象，并使用加载器在我们的构造函数中加载 web worker 文件，如下所示:

```
// src/resources/elements/pdf-document.js

import {customElement, bindable, bindingMode, inject, Loader} from 'aurelia-framework';
import {PDFJS} from 'pdfjs-dist';

@customElement('pdf-document')

... // all of our @bindables

@inject(Loader)
export class PdfDocument {
  constructor (loader) {
    // Let Aurelia handle resolving the filepath to the worker.
    PDFJS.workerSrc = loader.normalizeSync('pdfjs-dist/build/pdf.worker.js');

    // Create a worker instance for each custom element instance.
    this.worker = new PDFJS.PDFWorker();
  }
  detached () {
    // Release and destroy our worker instance when the the PDF element is removed from the DOM.
    this.worker.destroy();
  }
  ...
} 
```

### 加载我们的页面

PDF.js 库处理 PDF 文档的加载、解析和显示。它内置了对部分下载和认证的支持。我们所要做的就是提供有问题的文档的 URI，PDF.js 将返回一个 promise 对象，解析为表示 PDF 文档及其元数据的 JavaScript 对象。

加载和显示 PDF 将由我们的可绑定属性驱动；在这种情况下，它将是`url`属性。本质上，当 URL 改变时，定制元素应该要求 PDF.js 发出文件请求。我们将在我们的`urlChanged`处理程序中这样做，对我们的构造函数做一些修改来初始化一些属性，对我们的`detached`方法做一些修改来进行清理。

对于文档的每一页，我们将在 DOM 中创建一个`<canvas>`元素，放在一个固定高度的可滚动容器中。为了实现这一点，我们将使用 Aurelia 的基本模板功能，使用一个中继器。因为每个 PDF 页面可以有自己的大小和方向，我们将根据 PDF 页面视口设置每个画布元素的宽度和高度。

以下是我们的观点:

```
// src/resources/elements/pdf-document.html

<template>
  <require from="./pdf-document.css"></require>

  <div ref="container" id.bind="fingerprint" class="pdf-container">
    <div repeat.for="page of lastpage" class="text-center">
      <canvas id="${fingerprint}-page${(page + 1)}"></canvas>
    </div>
  </div>
</template> 
```

在我们加载了 PDF 文档之后，我们需要获取 PDF 中每个页面的大小，这样我们就可以将每个`canvas`的大小与其页面大小相匹配。(此时这样做允许我们设置我们的查看器进行滚动；如果我们现在不这样做，我们就不会有每个页面的正确高度。)因此，在加载每个页面后，我们使用 Aurelia 的 TaskQueue 抽象将一个调整 canvas 元素大小的任务排队。(这是出于 DOM 性能的原因。你可以在这里阅读[更多关于微任务的内容。](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)

这是我们的视图模型:

```
// src/resources/elements/pdf-document.js

import {customElement, bindable, bindingMode, inject, Loader} from 'aurelia-framework';
import {TaskQueue} from 'aurelia-task-queue';
import {PDFJS} from 'pdfjs-dist';

@customElement('pdf-document')

... // all of our @bindables

@inject(Loader, TaskQueue)
export class PdfDocument {
  constructor (loader, taskQueue) {
    PDFJS.workerSrc = loader.normalizeSync('pdfjs-dist/build/pdf.worker.js');
    this.worker = new PDFJS.PDFWorker();

    // Hold a reference to the task queue for later use.
    this.taskQueue = taskQueue;

    // Add a promise property.
    this.resolveDocumentPending;

    // Add a fingerprint property to uniquely identify our DOM nodes.
    // This allows us to create multiple viewers without issues.
    this.fingerprint = generateUniqueDomId();

    this.pages = [];
    this.currentPage = null;
  }

  urlChanged (newValue, oldValue) {
    if (newValue === oldValue) return;

    // Load our document and store a reference to PDF.js' loading promise.
    var promise = this.documentPending || Promise.resolve();
    this.documentPending = new Promise((resolve, reject) => {
      this.resolveDocumentPending = resolve.bind(this);
    });

    return promise
      .then((pdf) => {
        if (pdf) {
          pdf.destroy();
        }
        return PDFJS.getDocument({ url: newValue, worker: this.worker });
      })
      .then((pdf) => {
        this.lastpage = pdf.numPages;

        pdf.cleanupAfterRender = true;

        // Queue loading of all of our PDF pages so that we can scroll through them later.
        for (var i = 0; i < pdf.numPages; i++) {
          this.pages[i] = pdf.getPage(Number(i + 1))
            .then((page) => {
              var viewport = page.getViewport(this.scale);
              var element = document.getElementById(`${this.fingerprint}-page${page.pageNumber}`);

              // Update page canvas elements to match viewport dimensions. 
              // Use Aurelia's TaskQueue to batch the DOM changes.
              this.taskQueue.queueMicroTask(() => {
                element.height = viewport.height;
                element.width = viewport.width;
              });

              return {
                element: element,
                page: page,
                rendered: false,
                clean: false
              };
            });
        }

        // For the initial render, check to see which pages are currently visible, and render them.
        /* Not implemented yet. */

        this.resolveDocumentPending(pdf);
      });
  }

  detached () {
    // Destroy our PDF worker asynchronously to avoid any race conditions.
    return this.documentPending
      .then((pdf) => {
        if (pdf) {
          pdf.destroy();
        }
        this.worker.destroy();
      })
      .catch(() => {
        this.worker.destroy();
      });
  }
}

// Generate unique ID values to avoid any DOM conflicts and allow multiple PDF element instances.
var generateUniqueDomId = function () {
  var S4 = function() {
    return (((1 + Math.random()) * 0x10000) | 0)
      .toString(16)
      .substring(1);
  };

  return `_${S4()}${S4()}-${S4()}-${S4()}-${S4()}-${S4()}${S4()}${S4()}`;
} 
```

保存您的工作，Gulp 应该会重新显示页面。您会注意到容器显示了相应 pdf 的正确页数。唯一的问题是它们是空白的。让我们解决这个问题！

### 渲染我们的页面

现在我们已经加载了页面，我们需要能够将它们呈现到 DOM 元素中。为了实现这一点，我们将依赖 PDF.js 的呈现功能。他们的网站上有一个很好的例子[，展示了如何创建一个`renderContext`对象并将其传递给 PDF.js 渲染方法。我们将从示例中提取这段代码，并将其包装在一个呈现函数中:](https://mozilla.github.io/pdf.js/examples/)

src/资源/元素/pdf-document.js

```
...
export class PdfDocument { ... }

var generateUniqueDomId = function () { ... }

var render = function (renderPromise, scale) {
  return Promise.resolve(renderPromise)
    .then((renderObject) => {
      if (renderObject.rendered) return Promise.resolve(renderObject);
      renderObject.rendered = true;

      var viewport = renderObject.page.getViewport(scale);
      var context = renderObject.element.getContext('2d');

      return renderObject.page.render({
        canvasContext: context,
        viewport: viewport
      })
        .promise.then(() => {
          return renderObject;
        });
  });
}; 
```

PDF 格式渲染。JS 有些贵。因此，我们希望限制负载；我们只想呈现当前可见的内容，所以我们将只呈现可见边界内的页面，而不是一次呈现所有内容。我们将做一些简单的数学运算来检查视窗中有什么:

```
// src/resources/elements/pdf-document.js

export class PdfDocument { ... }

var generateUniqueDomId = function () { ... }

var render = function (...) { ... }

var checkIfElementVisible = function (container, element) {
  var containerBounds = {
    top: container.scrollTop,
    bottom: container.scrollTop + container.clientHeight
  };

  var elementBounds = {
    top: element.offsetTop,
    bottom: element.offsetTop + element.clientHeight
  };

  return (!((elementBounds.bottom < containerBounds.top && elementBounds.top < containerBounds.top)
    || (elementBounds.top > containerBounds.bottom && elementBounds.bottom > containerBounds.bottom)));
} 
```

当我们第一次加载文档和滚动时，我们将运行这些视窗检查。现在，在加载时，我们将简单地渲染可见的内容，就像这样。

```
// src/resources/elements/pdf-document.js

export class PdfDocument {
...
  urlChanged (newValue, oldValue) {
    ...
        // For the initial render, check to see which pages are currently visible, and render them.
        this.pages.forEach((page) => {
          page.then((renderObject) => {
            if (checkIfElementVisible(this.container, renderObject.element))
            {
              if (renderObject.rendered) return;
              render(page, this.scale);
            }
          });
        });

        this.resolveDocumentPending(pdf);
      });
  } 
```

重新加载应用程序，您将看到每个 PDF 的第一页呈现。

### 实现滚动

为了提供熟悉的无缝体验，我们的组件应该将页面显示为完全可滚动文档的各个部分。我们可以通过 CSS 让我们的容器有一个固定的滚动溢出高度来实现这一点。

为了最大限度地提高较大文档的性能，我们将做一些事情。首先，我们将利用 Aurelia 的 TaskQueue 批量修改 DOM。其次，我们将跟踪 PDF.js 已经呈现的页面，这样它就不必重复已经完成的工作。最后，我们将通过使用 Aurelia 的`debounce`绑定行为，仅在滚动停止后呈现可见页面。这是我们滚动时要运行的方法:

```
// src/resources/elements/pdf-document.js

export class PdfDocument {
...
  renderHandler () {
    Promise.all(this.pages)
      .then((values) => {
        values.forEach((renderObject) => {
          if (!renderObject) return;

          if (!checkIfElementVisible(this.container, renderObject.element))
          {
            if (renderObject.rendered && renderObject.clean) {
              renderObject.page.cleanup();
              renderObject.clean = true;
            }

            return;
          }

          this.taskQueue.queueMicroTask(() => {
            if (renderObject.rendered) return;
            render(renderObject, this.scale);
          });
        });
    });
  }
...
} 
```

这是我们的观点。我们利用 Aurelia 在`scroll.trigger`中的事件绑定，使用我们定义的方法，以及去抖绑定行为。

```
// src/resources/elements/pdf-document.html

<template>
  <require from="./pdf-document.css"></require>

  <div ref="container" id.bind="fingerprint" class="pdf-container" scroll.trigger="pageHandler()" 
       scroll.trigger2="renderHandler() & debounce:100">
    <div repeat.for="page of lastpage" class="text-center">
      <canvas id="${fingerprint}-page${(page + 1)}"></canvas>
    </div>
  </div>
</template> 
```

我们在查看器中绑定了`page`属性。当它改变时，我们希望更新滚动位置以显示当前页面。我们也希望以另一种方式工作；当我们在文档中滚动时，我们希望当前页码更新到我们当前正在查看的页面。因此，我们将在视图模型中添加以下两个方法:

```
export class PdfDocument {
...
  // If the page changes, scroll to the associated element.
  pageChanged (newValue, oldValue) {
    if (newValue === oldValue || 
        isNaN(Number(newValue)) || 
        Number(newValue) > this.lastpage || 
        Number(newValue) < 0) {
      this.page = oldValue;
      return;
    }

    // Prevent scroll update collisions with the pageHandler method.
    if (Math.abs(newValue - oldValue) <= 1) return;

    this.pages[newValue - 1]
      .then((renderObject) => {
        this.container.scrollTop = renderObject.element.offsetTop;
        render(this.pages[newValue - 1], this.scale);
      });
  }

...

  // Change the current page number as we scroll.
  pageHandler () {
    this.pages.forEach((page) => {
      page.then((renderObject) => {
        if ((this.container.scrollTop + this.container.clientHeight) >= renderObject.element.offsetTop
      && (this.container.scrollTop <= renderObject.element.offsetTop))
        {
          this.page = renderObject.page.pageNumber;
        }
      });
    });
  }
...
} 
```

我们将在容器中的`scroll.trigger`事件中调用 pageHandler 方法。

**注意:**由于 Aurelia 模板的当前限制，不可能在一个事件处理程序中用单独的绑定行为声明多个方法。我们通过将这些行添加到视图模型的顶部来解决这个问题…

```
import {SyntaxInterpreter} from 'aurelia-templating-binding';
SyntaxInterpreter.prototype.trigger2 = SyntaxInterpreter.prototype.trigger; 
```

…并将新方法放在`scroll.trigger2`事件上。

Gulp 应该重新加载应用程序，您将看到 PDF 的新页面会在滚动到视图中时呈现出来。耶！

### 实现缩放

当我们缩放时，我们希望更新当前的缩放级别。我们在我们的`scaleChanged`属性处理程序中这样做。本质上，我们调整所有画布元素的大小，以反映给定比例的每个页面的新视口大小。然后，我们重新渲染当前视口中的内容，重新开始循环。

```
// src/resources/elements/pdf-document.js

export class PdfDocument {
...
  scaleChanged (newValue, oldValue) {
    if (newValue === oldValue || isNaN(Number(newValue))) return;

    Promise.all(this.pages)
      .then((values) => {
        values.forEach((renderObject) => {
          if (!renderObject) return;

          var viewport = renderObject.page.getViewport(newValue);

          renderObject.rendered = false;

          this.taskQueue.queueMicroTask(() => {
            renderObject.element.height = viewport.height;
            renderObject.element.width = viewport.width;

            if (renderObject.page.pageNumber === this.page) {
              this.container.scrollTop = renderObject.element.offsetTop;
            }
          });
        });

      return values;
    })
    .then((values) => {
      this.pages.forEach((page) => {
        page.then((renderObject) => {
          this.taskQueue.queueMicroTask(() => {
            if (checkIfElementVisible(this.container, renderObject.element)) {
              render(page, this.scale);
            }
          });
        });
      });
    });
  }
...
} 
```

## 最终结果

让我们回顾一下我们的目标:

1.  我们希望用户能够加载文档，滚动，放大和缩小，性能良好。
2.  我们希望能够将查看器属性(比如当前页面和当前缩放级别)双向绑定到应用程序中的属性。
3.  我们希望这个查看器是一个可重用的组件；我们希望能够将多个查看器同时放入我们的应用程序中，而不会发生冲突，也不需要太多的努力。

最终代码可以在我们的 [GitHub repo](https://github.com/sitepoint-editors/aurelia-pdfjs) 上找到，在这里可以看到完成代码的[演示。虽然还有改进的余地，但我们已经达到了目标！！](https://sitepoint-editors.github.io/aurelia-pdfjs/)

## 项目后分析和改进

总是有改进的空间，并且执行项目后分析并确定在未来迭代中要解决的区域总是一个好的实践。这些是我想在 PDF 查看器实现方面升级的一些东西:

### 单个页面组件

目前，这种概念验证只允许滚动视窗。理想情况下，我们能够在任何地方呈现任何页面，甚至在查看器之外——例如，生成 PDF 缩略图作为单独的元素。创建一个`<pdf-page>`定制元素或类似的东西可以提供这种功能，而查看者可以通过组合简单地使用这些元素。

### API 优化

PDF.js 有一个扩展的 API。虽然有使用 PDF.js 的好例子，但是它的显示 API 可以使用更多的文档。使用查看器 API，可能有更干净、更优化的方法来实现我们的目标。

### 虚拟滚动和性能优化

目前，文档查看器中画布元素的数量等于文档中页面的数量。所有画布都存在于 DOM 中，对于大型文档来说，这可能非常昂贵。

存在一个 Aurelia 插件——[ui 虚拟化插件](https://github.com/aurelia/ui-virtualization) ( [演示](http://aurelia.io/ui-virtualization/))——它通过动态添加和删除 DOM 中的元素来对应活动视口，从而极大地提高了非常大的数据集的性能。理想情况下，PDF 查看器应该能够结合这一点来提高性能(以避免在 DOM 中有数千个画布，这会真正影响性能)。这种优化与单独的页面组件结合起来，对于大型文档来说确实有很大的不同。

## 创建插件

Aurelia 提供了一个插件系统。将这个概念验证转换成一个 Aurelia 插件将使它成为任何 Aurelia 应用程序的一个直接资源。Aurelia Github 库提供了一个[插件框架项目](https://github.com/aurelia/skeleton-plugin)，这将是 kickstart 开发的一个好点。这样，其他人就可以使用这个功能，而不必重新构建它！

## 前进

在 web 应用程序中处理 PDF 文件一直是一件痛苦的事情。但是有了今天可用的资源，通过组合库和它们的功能，我们可以做比以前更多的事情。今天，我们已经看到了一个基本的 PDF 查看器的例子——它可以通过自定义功能进行扩展，因为我们可以完全控制它。可能性是无限的！你准备好造东西了吗？请在下面的评论中告诉我。

## 分享这篇文章