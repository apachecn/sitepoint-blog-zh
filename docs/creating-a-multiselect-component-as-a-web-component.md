# 将多选组件创建为 Web 组件

> 原文：<https://www.sitepoint.com/creating-a-multiselect-component-as-a-web-component/>

**2016 年 5 月 12 日更新:**在评论中进行了一些讨论后，第二篇帖子被写出来，以解决这篇帖子的缺点——[如何使 Web 组件可访问](https://www.sitepoint.com/accessible-web-components/)。请务必也阅读这个。

*这篇文章由[瑞恩·刘易斯](https://github.com/ryanmurakami)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

Web 应用程序变得日益复杂，需要大量的标记、脚本和样式。为了管理和维护数百千字节的 HTML、JS 和 CSS，我们试图将我们的应用程序分割成可重用的组件。我们努力封装组件，防止风格冲突和脚本干扰。

最后，组件源代码分布在几个文件中:标记文件、脚本文件和样式表。我们可能会遇到的另一个问题是长标记中混杂着`div`和`span`符号。这种代码表达能力差，也很难维护。为了解决所有这些问题，W3C 引入了 Web 组件。

在这篇文章中，我将解释什么是 Web 组件，以及如何自己构建一个 Web 组件。

## 认识 Web 组件

Web 组件解决了引言中讨论的所有问题。使用 Web 组件，我们可以链接包含组件实现的单个 HTML 文件，并在页面上使用自定义 HTML 元素。它们简化了组件的创建，增强了封装，并使标记更具表现力。

Web 组件是用一套规范定义的:

*   [自定义元素](http://w3c.github.io/webcomponents/spec/custom/):允许为一个组件注册一个自定义的有意义的 HTML 元素
*   [HTML 模板](http://www.html5rocks.com/en/tutorials/webcomponents/template/):定义组件的标记
*   [Shadow DOM](http://w3c.github.io/webcomponents/spec/shadow/) :封装组件的内部，并在使用它的页面中隐藏它
*   [HTML 导入](http://w3c.github.io/webcomponents/spec/imports/):提供将组件包含到目标页面的能力。

描述了什么是 Web 组件之后，让我们来看看它们的作用。

## 如何构建生产就绪的 Web 组件

在这一节中，我们将构建一个可以在生产中使用的有用的多选小部件。结果可以在这个[演示页面](http://tabalinas.github.io/multiselect-web-component/)上找到，全部源代码可以在 [GitHub](https://github.com/sitepoint-editors/multiselect-web-component) 上找到。

### 要求

首先，让我们定义多选小部件的一些要求。

标记应该具有以下结构:

```
<x-multiselect placeholder="Select Item">
    <li value="1" selected>Item 1</li>
    <li value="2">Item 2</li>
    <li value="3" selected>Item 3</li>
</x-multiselect>
```

定制元素`<x-multiselect>`有一个`placeholder`属性来定义空多选的占位符。项目是用支持`value`和`selected`属性的`<li>`元素定义的。

multiselect 应该有返回所选项数组的`selectedItems` API 方法。

```
// returns an array of values, e.g. [1, 3]
var selectedItems = multiselect.selectedItems();
```

此外，小部件应该在每次选择的项目改变时触发一个事件`change`。

```
multiselect.addEventListener('change', function() {
    // print selected items to console
    console.log('Selected items:', this.selectedItems()); 
});
```

最后，这个小部件应该可以在所有现代浏览器中工作。

### 模板

我们开始创建包含组件所有源代码的`multiselect.html`文件:HTML 标记、CSS 样式和 JS 代码。

HTML 模板允许我们在一个特殊的 HTML 元素`<template>`中定义组件的模板。这是我们多选的模板:

```
<template id="multiselectTemplate">
    <style> /* component styles */ </style>

    <!-- component markup -->
    <div class="multiselect">
        <div class="multiselect-field"></div>
        <div class="multiselect-popup">
            <ul class="multiselect-list">
                <content select="li"></content>
            </ul>
        </div>
    </div>
</template>
```

组件标记包含多选字段和一个带有项目列表的弹出窗口。我们希望 multiselect 从用户标记中直接获取项目。我们可以用一个新的 HTML 元素`<content>`来做到这一点([你可以在 MDN](https://developer.mozilla.org/en-US/docs/Web/API/HTMLContentElement) 上找到更多关于`content`元素的信息)。它定义了从影子主机(用户标记中的组件声明)到影子 DOM(封装的组件标记)的标记插入点。

属性接受 CSS 选择器，并定义从影子主机中选择哪些元素。在我们的例子中，我们想要获取所有的`<li>`元素并设置`select="li"`。

### 创建组件

现在让我们创建一个组件并注册一个定制的 HTML 元素。将以下创建脚本添加到`multiselect.html`文件中:

```
<script> // 1\. find template
    var ownerDocument = document.currentScript.ownerDocument;
    var template = ownerDocument.querySelector('#multiselectTemplate');

    // 2\. create component object with the specified prototype 
    var multiselectPrototype = Object.create(HTMLElement.prototype);

    // 3\. define createdCallback
    multiselectPrototype.createdCallback = function() {
        var root = this.createShadowRoot();
        var content = document.importNode(template.content, true);
        root.appendChild(content);
    };

    // 4\. register custom element
    document.registerElement('x-multiselect', {
        prototype: multiselectPrototype
    }); </script>
```

Web 组件的创建包括四个步骤:

1.  在所有者文档中查找模板。
2.  用指定的原型对象创建一个新对象。在这种情况下，我们从现有的 HTML 元素继承，但是任何可用的元素都可以扩展。
3.  定义创建组件时调用的`createdCallback`。在这里，我们为组件创建一个影子根，并将模板的内容附加到里面。
4.  用`document.registerElement`方法为组件注册一个定制元素。

[要了解更多关于创建自定义元素的信息，我建议你查阅 Eric Bidelman 的指南](http://www.html5rocks.com/en/tutorials/webcomponents/customelements/)。

### 呈现多选字段

下一步是根据所选项目呈现多选字段。

切入点是`createdCallback`方法。让我们定义两个方法，`init`和`render`:

```
multiselectPrototype.createdCallback = function() {
    this.init();
    this.render();
};
```

`init`方法创建一个影子根，并找到所有内部组件(字段、弹出窗口和列表):

```
multiselectPrototype.init = function() {
    // create shadow root
    this._root = this.createRootElement();

    // init component parts
    this._field = this._root.querySelector('.multiselect-field');
    this._popup = this._root.querySelector('.multiselect-popup');
    this._list = this._root.querySelector('.multiselect-list');
};

multiselectPrototype.createRootElement = function() {
    var root = this.createShadowRoot();
    var content = document.importNode(template.content, true);
    root.appendChild(content);
    return root;
};
```

`render`方法进行实际的渲染。因此它调用了`refreshField`方法，该方法循环遍历选中的项目并为每个选中的项目创建标签:

```
multiselectPrototype.render = function() {
    this.refreshField();
};

multiselectPrototype.refreshField = function() {
    // clear content of the field
    this._field.innerHTML = '';

    // find selected items
    var selectedItems = this.querySelectorAll('li[selected]');

    // create tags for selected items
    for(var i = 0; i < selectedItems.length; i++) {
        this._field.appendChild(this.createTag(selectedItems[i]));
    }
};

multiselectPrototype.createTag = function(item) {
    // create tag text element
    var content = document.createElement('div');
    content.className = 'multiselect-tag-text';
    content.textContent = item.textContent;

    // create item remove button
    var removeButton = document.createElement('div');
    removeButton.className = 'multiselect-tag-remove-button';
    removeButton.addEventListener('click', this.removeTag.bind(this, tag, item));

    // create tag element
    var tag = document.createElement('div');
    tag.className = 'multiselect-tag';
    tag.appendChild(content);
    tag.appendChild(removeButton);

    return tag;
};
```

每个标签都有一个删除按钮。移除按钮单击处理程序从项目中移除选择并刷新多选字段:

```
multiselectPrototype.removeTag = function(tag, item, event) {
    // unselect item
    item.removeAttribute('selected');

    // prevent event bubbling to avoid side-effects
    event.stopPropagation();

    // refresh multiselect field
    this.refreshField();
};
```

### 打开弹出窗口并选择项目

当用户单击该字段时，我们应该显示弹出窗口。当他/她单击列表项时，它应该被标记为选中，并且弹出窗口应该被隐藏。

为此，我们处理字段和项目列表上的点击。让我们将`attachHandlers`方法添加到`render`中:

```
multiselectPrototype.render = function() {
    this.attachHandlers();
    this.refreshField();
};

multiselectPrototype.attachHandlers = function() {
    // attach click handlers to field and list
    this._field.addEventListener('click', this.fieldClickHandler.bind(this));
    this._list.addEventListener('click', this.listClickHandler.bind(this));
};
```

在“单击处理程序”字段中，我们切换弹出窗口的可见性:

```
multiselectPrototype.fieldClickHandler = function() {
    this.togglePopup();
};

multiselectPrototype.togglePopup = function(show) {
    show = (show !== undefined) ? show : !this._isOpened;
    this._isOpened = show;
    this._popup.style.display = this._isOpened ? 'block' : 'none';
};
```

在列表点击处理程序中，我们找到被点击的项目并将其标记为选中。然后，我们隐藏弹出窗口并刷新多选字段:

```
multiselectPrototype.listClickHandler = function(event) {
    // find clicked list item
    var item = event.target;
    while(item && item.tagName !== 'LI') {
        item = item.parentNode;
    }

    // set selected state of clicked item
    item.setAttribute('selected', 'selected');

    // hide popup
    this.togglePopup(false);

    // refresh multiselect field
    this.refreshField();
};
```

### 添加占位符属性

另一个多选特性是一个`placeholder`属性。用户可以指定当没有选择任何项目时在字段中显示的文本。为了完成这个任务，让我们读取组件初始化的属性值(在`init`方法中):

```
multiselectPrototype.init = function() {
    this.initOptions();
    ...
};

multiselectPrototype.initOptions = function() {
    // save placeholder attribute value
    this._options = {
        placeholder: this.getAttribute("placeholder") || 'Select'
    };
};
```

当没有选择任何项目时，`refreshField`方法将显示占位符:

```
multiselectPrototype.refreshField = function() {
    this._field.innerHTML = '';

    var selectedItems = this.querySelectorAll('li[selected]');

    // show placeholder when no item selected
    if(!selectedItems.length) {
        this._field.appendChild(this.createPlaceholder());
        return;
    }

    ...
};

multiselectPrototype.createPlaceholder = function() {
    // create placeholder element
    var placeholder = document.createElement('div');
    placeholder.className = 'multiselect-field-placeholder';
    placeholder.textContent = this._options.placeholder;
    return placeholder;
};
```

但这并不是故事的结尾。如果占位符属性值改变了会怎样？我们需要处理这个问题并更新字段。这里`attributeChangedCallback`回调派上了用场。每次属性值更改时都会调用此回调。在我们的例子中，我们保存一个新的占位符值并刷新多选字段:

```
multiselectPrototype.attributeChangedCallback = function(optionName, oldValue, newValue) {
    this._options[optionName] = newValue;
    this.refreshField();
};
```

### 添加`selectedItems`方法

我们需要做的就是给组件原型添加一个方法。`selectedItems`方法的实现很简单——遍历选中的项目并读取值。如果项目没有值，则返回项目文本:

```
multiselectPrototype.selectedItems = function() {
    var result = [];

    // find selected items
    var selectedItems = this.querySelectorAll('li[selected]');

    // loop over selected items and read values or text content
    for(var i = 0; i < selectedItems.length; i++) {
        var selectedItem = selectedItems[i];

        result.push(selectedItem.hasAttribute('value')
                ? selectedItem.getAttribute('value')
                : selectedItem.textContent);
    }

    return result;
};
```

### 添加自定义事件

现在让我们添加每次用户改变选择时将被触发的`change`事件。要触发一个事件，我们需要创建一个`CustomEvent`实例并分派它:

```
multiselectPrototype.fireChangeEvent = function() {
    // create custom event instance
    var event = new CustomEvent("change");

    // dispatch event
    this.dispatchEvent(event);
};
```

此时，我们需要在用户选择或取消选择项目时触发事件。在 list click 处理程序中，我们在实际选择一个项目时触发事件:

```
multiselectPrototype.listClickHandler = function(event) {
    ...

    if(!item.hasAttribute('selected')) {
        item.setAttribute('selected', 'selected');
        this.fireChangeEvent();
        this.refreshField();
    }

    ...
};
```

在 remove tag 按钮处理程序中，我们还需要触发`change`事件，因为一个项目已经被取消选择:

```
multiselectPrototype.removeTag = function(tag, item, event) {
    ...

    this.fireChangeEvent();
    this.refreshField();
};
```

### 式样

Shadow DOM 内部元素的样式非常简单。我们附加了一些特殊的类，如`multiselect-field`或`multiselect-popup`，并为它们添加了必要的 CSS 规则。

但是我们如何设计列表项的样式呢？问题是它们来自影子主机，不属于影子 DOM。特殊选择器`::content`来拯救我们了。

以下是我们列表项目的样式:

```
::content li {
    padding: .5em 1em;
    min-height: 1em;
    list-style: none;
    cursor: pointer;
}

::content li[selected] {
    background: #f9f9f9;
}
```

Web 组件引入了一些特殊的选择器，你可以在这里找到更多关于它们的信息。

## 使用

太好了！我们的多选功能已经完成，因此我们可以使用它了。我们需要做的就是导入多选 HTML 文件，并向标记中添加一个自定义元素:

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <link rel="import" href="multiselect.html">
</head>
<body>
    <x-multiselect placeholder="Select Value">
        <li value="1" selected>Item 1</li>
        <li value="2">Item 2</li>
        <li value="3" selected>Item 3</li>
        <li value="4">Item 4</li>
    </x-multiselect>
</body>
</html>
```

让我们订阅`change`事件，并在用户每次更改选择时将选择的项目打印到控制台:

```
<script> var multiselect = document.querySelector('x-multiselect');
    multiselect.addEventListener('change', function() {
        console.log('Selected items:', this.selectedItems());
    }); </script>
```

[进入演示页面，打开浏览器控制台](http://tabalinas.github.io/multiselect-web-component/)查看每次更改选择时选择的项目。

## 浏览器支持

如果我们看一下[浏览器支持](http://caniuse.com/#search=components)，我们会发现只有 Chrome 和 Opera 完全支持 Web 组件。尽管如此，我们仍然可以通过 polyfills webcomponentjs 的[套件使用 Web 组件，该套件允许在所有浏览器的最新版本中使用 Web 组件。](https://github.com/webcomponents/webcomponentsjs)

让我们应用这个 polyfill，以便能够在所有浏览器中使用我们的多选。它可以与 Bower 一起安装，然后包含在您的网页中。

```
bower install webcomponentsjs
```

如果我们在 Safari 中打开演示页面，我们会在控制台中看到错误*“null 不是对象”*。问题是`document.currentScript`并不存在。要解决这个问题，我们需要从多填充环境中获取`ownerDocument`(使用`document._currentScript`而不是`document.currentScript`)。

```
var ownerDocument = (document._currentScript || document.currentScript).ownerDocument;
```

有用！但是如果你在 Safari 中打开 multiselect，你会看到列表项没有样式。为了解决这个问题，我们需要调整模板内容的样式。可以用`WebComponents.ShadowCSS.shimStyling`方法完成。我们应该在追加影子根内容之前调用它:

```
multiselectPrototype.createRootElement = function() {
    var root = this.createShadowRoot();
    var content = document.importNode(template.content, true);

    if (window.ShadowDOMPolyfill) {
        WebComponents.ShadowCSS.shimStyling(content, 'x-multiselect');
    }

    root.appendChild(content);
    return root;
};
```

恭喜你！现在，我们的多选组件工作正常，在所有现代浏览器中看起来都像预期的那样。

Web 组件聚合填充非常棒！显然，要让这些规范在所有现代浏览器上都能工作需要付出巨大的努力。polyfill 源脚本的大小为 258Kb。虽然，缩小和 gzipped 版本是 38Kb，但我们可以想象这背后隐藏了多少逻辑。这不可避免地会影响表演。尽管作者们把重点放在性能上，把垫片做得越来越好。

## 聚合物和 X 标签

谈到 Web 组件，我应该提到[聚合物](https://www.polymer-project.org/1.0/)。Polymer 是一个构建在 Web 组件之上的库，它简化了组件的创建，并提供了大量现成的元素。`webcomponents.js` polyfill 是聚合物的一部分，被称为`platform.js`。后来，它被[提取出来，改名为](https://blog.polymer-project.org/announcements/2014/10/16/platform-becomes-webcomponents/)。

用聚合物创建 Web 组件要容易得多。Pankaj Parashar 的这篇文章展示了如何使用聚合物来创建 Web 组件。
如果你想深化这个话题，这里有一个可能有用的文章列表:

*   [使用 X 标签构建定制 Web 组件](https://www.sitepoint.com/building-custom-web-components-with-x-tag/)
*   [用聚合物构建图库组件](https://www.sitepoint.com/building-a-image-gallery-component-with-polymer/)
*   [将组件化引入 Web:Web 组件概述](https://www.sitepoint.com/bringing-componentization-web-overview-web-components/)

还有一个库可以让 Web 组件的工作变得更简单，那就是 [X-Tag](http://x-tag.github.io/) 。它是由 Mozilla 开发的，现在得到了微软的支持。

## 结论

Web 组件是 Web 开发领域的一大进步。它们有助于简化组件的提取，增强封装，并使标记更具表现力。

在本教程中，我们看到了如何用 Web 组件构建一个生产就绪的多选小部件。尽管缺乏浏览器支持，但由于高质量的 polyfill webcomponentsjs，我们今天可以使用 Web 组件。像 Polymer 和 X-Tag 这样的库提供了以更简单的方式创建 Web 组件的机会。

**现在请务必查看后续帖子:[如何制作可访问的 Web 组件](https://www.sitepoint.com/accessible-web-components/)。**

您已经在 web 应用程序中使用了 Web 组件吗？请在下面分享您的经验和想法。

## 分享这篇文章