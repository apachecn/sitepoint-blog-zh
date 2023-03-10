# 使用 X-Tag 构建自定义 Web 组件

> 原文：<https://www.sitepoint.com/building-custom-web-components-with-x-tag/>

继谷歌和 T2 的 web 组件解决方案之后，现在轮到微软公开发布 T4 的 X 标签库 T5，进入这个领域。官网是这样定义的:

> X-Tag 是微软支持的开源 JavaScript 库，它包装了 W3C 标准的 Web 组件系列 API，为快速组件开发提供了一个紧凑、功能丰富的接口。虽然 X-Tag 为所有 Web 组件 API(定制元素、影子 DOM、模板和 HTML 导入)提供了特性挂钩，但它只需要定制元素支持就可以运行。在缺乏本地定制元素支持的情况下，X-Tag 使用一组与 Google 的 Polymer framework 共享的 [polyfills](https://github.com/webcomponents/webcomponentsjs) 。

换句话说， **X-Tag 对于微软就像 Polymer 对于 Google** 一样。两者唯一的共同点是底层的 polyfill 支持不支持的浏览器中的 web 组件。

### X-Tag 与聚合物有何不同？

Polymer 将所有四种 web 组件技术，即 [HTML 导入](http://w3c.github.io/webcomponents/spec/imports/)、[自定义元素](http://w3c.github.io/webcomponents/spec/custom/)、[影子 DOM](http://w3c.github.io/webcomponents/spec/shadow/) 和 [HTML 模板](http://www.html5rocks.com/en/tutorials/webcomponents/template/)组合成一个包。它为开发人员提供了一个易于使用的 API 来构建定制的 web 组件。另一方面，X-Tag 只提供定制元素 API 来构建定制 web 组件。当然，您也可以将其他 web 组件 API 与 X-Tag 库结合使用。

### X-Tag 不是 Mozilla 的项目吗？

是的，曾经是，但现在不再是了。经过一番挖掘，[我设法找到了](http://stackoverflow.com/questions/33929771/microsofts-x-tag-vs-mozillas-x-tags/33971721#33971721)X-Tag 项目的最初开发者， [Daniel Buchner](https://twitter.com/csuwildcat) 在创建这个库的时候曾在 Mozilla 工作。但从那以后，他去了微软，继续这个项目。此外，在一位前墨子学者的帮助下，他是图书馆的唯一贡献者。因此，它现在是一个由前 Mozillian 人创建的微软支持的项目。

## X-Tag 入门

在本文中，我们将使用 X-Tag 库构建一个自定义元素，使用以下标记嵌入 Google 地图街道视图:

```
<google-map latitude="40.7553231" longitude="-73.9752458"></google-map>
```

`<google-map>`定制元素将有两个属性，`latitude`和`longitude`，用来指定一个位置的坐标。由于这些属性是可选的，我们还将为它们中的每一个分配默认值，以防开发人员未能在标记中定义它们。

让我们从在文档的`<head>`中包含 X 标签 JavaScript 库开始。

```
<!DOCTYPE html>
<html>
  <head>
    <!-- X-Tag library including the polyfill -->
    <script src="https://rawgit.com/x-tag/core/master/dist/x-tag-core.min.js"></script>
  </head>
  <body>
    <!-- Custom element markup will appear here -->

    <script> <!-- The behavior of the custom element will be defined here --> </script>
  </body>
</html>
```

> 在文档的`<head>`中包含 X 标签库是很重要的。这是为了在呈现引擎遇到我们将在`<body>`中使用的定制元素标记之前，完全下载并解析它。

## 定义自定义元素

与 Polymer 不同，用 X-Tag 创建定制元素的过程完全是 JavaScript 驱动的。X-Tag 库提供了许多有用的方法、回调和属性来定义元素的自定义行为。创建带有 X 标签的定制元素的典型框架如下所示:

```
xtag.register('google-map', {

  content: '',

  lifecycle: {
    created  : function(){ 
      /* Called when the custom element is created */ 
    },
    inserted : function(){ 
      /* Called when the custom element is inserted into the DOM */ 
    },
    removed  : function(){ 
      /* Called when the custom element is removed from the DOM */ 
    },
    attributeChanged: function(attrName, oldValue, newValue){
      /* Called when the attribute of the custom element is changed */
    }
  },

  accessors : {},
  methods   : {},
  events    : {}
});
```

*   这是图书馆中最重要的方法。它接受自定义元素的名称作为第一个参数，后跟对象定义。顾名思义，它负责在 DOM 中注册定制元素。
*   `content`–通常，定制元素可能需要一些额外的标记用于结构或表示。它接受 HTML 字符串或多行注释字符串，将标记注入 DOM。
*   这包含有用的回调方法，旨在定制元素生命周期的不同阶段。
*   这提供了一个公共接口来访问对象属性、设置器和获取器，并将它们与相应的 HTML 属性链接起来。当属性链接到 getter/setter 时，它们的状态总是保持同步。
*   定制元素可能需要一些自己的、独特的方法来提供所需的功能。只需在顶层的`xtag.register()`定义对象中添加一个`methods`对象，并在其中包含所有用户定义的方法。
*   `events`–负责将事件附加到自定义元素。这个对象的关键字是您希望附加到自定义元素的事件的名称，如`tap`、`focus`等。

基本思想是使用 iframe 嵌入谷歌地图，并以如下格式指定`src` url:

```
https://www.google.com/maps/embed/v1/streetview?key=<API_KEY>&location=<LATITUDE>,<LONGITUDE>
```

要获得`API_KEY`，按照[这里描述的步骤](https://developers.google.com/maps/documentation/streetview/get-api-key)。一旦有了`API_KEY`，我们将在生命周期对象的`created`回调方法中创建一个`iframe`，并以上述格式指定`src`属性。

```
// Insert your API key here
var API_KEY = <API_KEY>;

xtag.register('google-map', {
  lifecycle: {
    created: function() { 
      var iframe = document.createElement('iframe');
      iframe.width = 600;
      iframe.height = 600;
      iframe.frameBorder = 0;
      iframe.src = 'https://www.google.com/maps/embed/v1/streetview?key=' + 
                    API_KEY + '&location=40.7553231,35.3434';
      this.appendChild(iframe);
    }
  }
});
```

上面的代码可以工作，但是我们需要去掉`iframe.src`中的硬编码坐标值，取而代之的是直接从定制元素属性中获取值。为此，我们将利用`accessors`对象，属性名构成了键。我们通过声明`attributes: {}`将它们与 HTML 属性联系起来。

```
accessors: {
  latitude: {
    attribute: {},
    set: function(value) {
      this.xtag.data.latitude = value;
    },
    get: function() {
      return this.getAttribute('latitude') || "40.7553231"; // Default value
    }
  },
  longitude: {
    attribute: {},
    set: function(value) {
      this.xtag.data.longitude = value;
    },            
    get: function() {
      return this.getAttribute('longitude') || "-73.9752458"; // Default value
    }
  }
}
```

然后，我们可以在指定`src` url 时直接使用这些变量:

```
iframe.src = 'https://www.google.com/maps/embed/v1/streetview?key=' + 
             API_KEY+'&location=' + 
             this.latitude+',' + 
             this.longitude;
```

## 进行最后的润色

定制元素的样式类似于任何其他 HTML 标记的样式。类、id 和属性选择器与定制元素一起工作。例如，我们将添加一个`box-shadow`和`border-radius`到我们新创建的`<google-map>`定制元素中。

```
google-map { 
  display: inline-block;
  box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 
              0 1px 5px 0 rgba(0, 0, 0, 0.12), 
              0 3px 1px -2px rgba(0, 0, 0, 0.2);
  padding: 1em;
  border-radius: .25em;
}
```

现在，使用定制元素就像在`<body>`中包含以下标记一样简单:

```
<!-- Use the custom element -->
<google-map latitude="40.7553231" longitude="-73.9752458"></google-map>
```

最终结果可以在下面的代码栏中看到:

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔 [X 标签<google>自定义元素</google>](http://codepen.io/SitePoint/pen/VevVpa/) 。