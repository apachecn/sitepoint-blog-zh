# 如何制作可访问的 Web 组件——简要指南

> 原文：<https://www.sitepoint.com/accessible-web-components/>

马洛里·范·阿赫特博格对这篇文章进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

在之前的一篇文章中，我演示了如何创建多选 web 组件。在那篇文章的评论部分，读者触及了 web 组件可访问性这个非常重要的主题。毫无疑问，对于今天的 web 来说，可访问性是至关重要的，所以让我们来谈谈什么是可访问性，并看看(通过一个真实世界的例子)如何使 web 组件更易访问。

本文中的代码将基于我上一篇文章中的代码。你可以从我们的 [GitHub repo](https://github.com/sitepoint-editors/multiselect-web-component) 中获得它的副本，或者在文章末尾查看可访问组件的[演示](#demo)。

## Web 组件可访问性需要什么？

当谈到组件的可访问性时，我们通常会考虑以下几个方面:

1.  标记语义
2.  键盘支持
3.  视觉无障碍

让我们更详细地讨论一下每个方面。

### 标记语义

我相信你听说过屏幕阅读器。屏幕阅读器是一种辅助软件，允许盲人或视力受损的人通过大声朗读屏幕上显示的信息来使用应用程序。市面上有很多屏幕阅读器，其中包括 Windows 的 [NVDA](http://www.nvaccess.org) 和[大白鲨](http://www.freedomscientific.com/fs_products/JAWS_HQ.asp)，Chrome 的 [ChromeVox](http://www.chromevox.com/) ，OS X 的[画外音](http://www.apple.com/accessibility/osx/voiceover/)

当一个元素获得焦点时，屏幕阅读器向用户提供关于它的信息。因此，当一个 HTML `<input type="text">`被聚焦时，用户从屏幕阅读器中知道他们正在处理文本域(并且可以输入一些东西)。但如果元素只是一个光秃秃的`<div>`，屏幕阅读器对此也无话可说。

为了解决这个问题，我们可以使用[WAI-ARIA](https://www.w3.org/WAI/intro/aria)(Web Accessibility Initiative-Accessible Rich Internet Applications)来添加特殊的 ARIA 属性，以扩展组件标记的语义。这些额外的语义帮助辅助技术识别用户界面中的属性、关系和状态。使用 ARIA 的实用指南可以在这里找到: [WAI-ARIA 创作实践](https://www.w3.org/TR/wai-aria-practices-1.1/)，或者(快速复习)你可以阅读我们的[WAI-ARIA 介绍](https://www.sitepoint.com/introduction-wai-aria/)。

### 键盘支持

目标是使只使用[键盘](https://www.sitepoint.com/best-mechanical-keyboards/)与组件交互成为可能。WAI-ARIA 为[许多 UI 控件](https://www.w3.org/TR/wai-aria-practices-1.1/#aria_ex)定义了行为和键盘交互。要知道哪个组件应该支持哪些键，可以在规范中找到组件的描述或类似的描述，并使用它们。例如，多选类似于[组合框](https://www.w3.org/TR/wai-aria-practices-1.1/#combobox)。

即使有了键盘可访问性，让用户知道使用哪个键/组合键来与组件交互(例如通过在应用程序中提供一些指令)也是一个好的做法，因为这可能不是显而易见的。

### 视觉无障碍

这里我们讨论的是与组件外观相关的可访问性方面。确保您可以对以下问题回答“是”:

*   元素和文本是否足够大，可以清楚地看到它们？
*   您的组件在高对比度模式下看起来是否符合预期？
*   可以使用没有颜色的组件吗？

请记住，并非所有视障用户都是[法定盲人](http://www.allaboutvision.com/lowvision/legally-blind.htm)。例如，有许多用户视力低下或患有色盲。

## 使多选 Web 组件可访问

现在，我们将使用上面概述的所有技术使多选更容易访问。具体来说，我们将:

*   扩展标记语义
*   添加键盘支持
*   验证其视觉可及性

别忘了，你可以在文章最后查看组件的演示，或者从我们的 [GitHub repo](https://github.com/sitepoint-editors/multiselect-web-component) 下载代码。

所有的代码片段都可以在[multiselect.html](https://github.com/sitepoint-editors/multiselect-web-component/blob/master/src/multiselect.html)文件中找到。

### 扩展标记语义

可访问性的经验法则是使用原生 HTML 元素而不是自定义元素。这意味着，如果您可以使用具有内置可访问性的本机 HTML 控件，请这样做。仅当您确实需要创建自定义组件时，才添加 ARIA 属性。如果你想了解更多这方面的内容，请阅读[避免 HTML 页面中的 WAI-ARIA 冗余](https://www.sitepoint.com/avoiding-redundancy-wai-aria-html-pages/)。

在我们的例子中，multiselect 是一个定制组件，所以我们需要添加 ARIA 属性。首先，让我们在 [ARIA 规范](https://www.w3.org/TR/wai-aria-practices-1.1/)中找到一个类似于 multiselect 的组件。经过一点研究，combobox 的外观和行为似乎是相似的。很好，现在让我们看看根据[组合框的描述](http://w3c.github.io/aria/practices/aria-practices.html#combobox)，我们需要添加哪些 ARIA 属性。

从指南中我们可以看到，我们需要添加以下角色:

1.  `role="combobox"`到组件的根元素
2.  `role="listbox"`到弹出的项目列表中
3.  `role="option"`下拉列表中的每一项

要添加的 aria 状态属性:

1.  `aria-expanded="true/false"`到根元素来指示组件是打开的还是关闭的
2.  `aria-selected="true/false"`下拉列表中的每一项以表示选中状态

角色`combobox`和`listbox`可以直接添加到组件的标记中:

```
<div class="multiselect" role="combobox">
  <div class="multiselect-field"></div>
  <div class="multiselect-popup">
    <ul class="multiselect-list" role="listbox">
      <content select="li"></content>
    </ul>
  </div>
</div> 
```

为了将角色`option`添加到列表的每一项，我们在`refreshItems`方法中循环遍历各项。这个新方法在组件呈现时被调用:

```
multiselectPrototype.render = function() {
  this.attachHandlers();
  this.refreshField();
  this.refreshItems();
};

multiselectPrototype.refreshItems = function() {
  var itemElements = this.itemElements();

  for(var i = 0; i < itemElements.length; i++) {
    var itemElement = itemElements[i];

    // set role and aria-selected property of an item
    itemElement.setAttribute("role", "option");
    itemElement.setAttribute("aria-selected", itemElement.hasAttribute("selected"));
  }
};

multiselectPrototype.itemElements = function() {
  return this.querySelectorAll('li');
}; 
```

可以将`aria-expanded`属性添加到`togglePopup`方法中的控件，该方法(顾名思义)负责显示和隐藏弹出窗口:

```
multiselectPrototype.togglePopup = function(show) {
  this._isOpened = show;
  this._popup.style.display = show ? 'block' : 'none';

  // set aria-expanded property
  this._control.setAttribute("aria-expanded", show);
}; 
```

我们还根据项目的`selected`属性初始化项目的`aria-selected`属性。应该维护`aria-selected`属性，以反映当前项目的选中状态。我们可以用`selectItem`和`unselectItem`方法做到这一点:

```
multiselectPrototype.selectItem = function(item) {
  if(!item.hasAttribute('selected')) {
    // set aria-selected property of selected item
    item.setAttribute('aria-selected', true);

    item.setAttribute('selected', 'selected');
    this.fireChangeEvent();
    this.refreshField();
  }

  this.close();
};

multiselectPrototype.unselectItem = function(item) {
  // set aria-selected property of unselected item
  item.setAttribute('aria-selected', false);

  item.removeAttribute('selected');
  this.fireChangeEvent();
  this.refreshField();
}; 
```

就这样，添加了 ARIA 属性。下一步是键盘支持。

### 添加键盘支持

让我们打开[规范](https://www.w3.org/TR/wai-aria-practices-1.1/#combobox)并查看*键盘交互*部分，看看我们需要支持哪些交互。

以下是能够仅通过键盘使用多选的一组基本键:

1.  `Alt + Up/Down Arrow`–打开/关闭多选
2.  `Esc`–关闭多选
3.  `Up/Down Arrow`–浏览项目
4.  `Enter`–多选打开时选择一个项目
5.  `Backspace`–取消选择最后选择的项目

#### 使其可聚焦

添加键盘支持的第一步是使组件可聚焦。为此，我们需要设置 [tabindex](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex) 属性，其行为因`tabindex`值而异:

*   正整数—定义元素在键盘焦点导航中的顺序
*   `0`–键盘焦点导航中元素的顺序由浏览器定义
*   `-1`–使用键盘焦点导航无法到达该元素，但现在可以使用 JavaScript 的`focus()`方法通过编程接收焦点。

在定制组件的情况下，`tabindex`应该是`-1`或`0`，因为我们无法知道元素在目标页面上的顺序。因此，我们直接在标记中将多选字段上的`tabindex`设置为`0`:

```
<div class="multiselect-field" tabindex="0" aria-readonly="true"></div> 
```

下一步是处理多选上的`keydown`事件:

```
multiselectPrototype.attachHandlers = function() {
  this._control.addEventListener('keydown', this.keyDownHandler.bind(this));
  ...
}; 
```

`keyDownHandler`方法根据`event.which`属性值调用特定的键处理程序:

```
multiselectPrototype.keyDownHandler = function(event) {
  switch(event.which) {
    case 8:  // Backspace
      this.handleBackspaceKey();
      break;
    case 13: // Enter
      this.handleEnterKey();
      break;
    case 27: // Escape
      this.handleEscapeKey();
      break;
    case 38: // Up Arrow
      event.altKey ? this.handleAltArrowUpKey() : this.handleArrowUpKey();
      break;
    case 40: // Down Arrow
      event.altKey ? this.handleAltArrowDownKey() : this.handleArrowDownKey();
      break;
    default:
      return;
  }

  // prevent native browser key handling
  event.preventDefault();
}; 
```

一旦按键被处理，我们通过调用`event.preventDefault()`来阻止浏览器执行它的标准动作。

#### 用键盘打开/关闭

`Alt + Down Arrow`组合键应该打开多选，而`Alt + Up Arrow`和`Esc`键应该关闭多选:

```
multiselectPrototype.handleAltArrowDownKey = function() {
  this.open();
};

multiselectPrototype.handleAltArrowUpKey = function() {
  this.close();
};

multiselectPrototype.handleEscapeKey = function() {
  this.close();
}; 
```

`open`和`close`方法只是调用了`togglePopup`方法:

```
multiselectPrototype.open = function() {
  this.togglePopup(true);
};

multiselectPrototype.close = function() {
  this.togglePopup(false);
}; 
```

#### 使用键盘导航项目

首先，我们将每个多选项目的`tabindex`设置为`-1`，因此它们成为可聚焦的:

```
multiselectPrototype.refreshItems = function() {
  var itemElements = this.itemElements();

  for(var i = 0; i < itemElements.length; i++) {
    var itemElement = itemElements[i];
    ...
    // set item tabindex attribute
    itemElement.setAttribute("tabindex", -1);
  }

  // initialize focused item index
  this._focusedItemIndex = 0;
}; 
```

属性存储焦点项的索引。

`Up Arrow`和`Down Arrow`键允许用户浏览列表中的项目，保持当前聚焦的项目索引:

```
multiselectPrototype.handleArrowDownKey = function() {
  this._focusedItemIndex = (this._focusedItemIndex < this.itemElements().length - 1)
      ? this._focusedItemIndex + 1   // go to the next item
      : 0;                           // go to the first item

  this.refreshFocusedItem();
}; 
```

如果在列表末尾按下`Down Arrow`,焦点将转到第一个项目。

如果在列表的第一个项目上按下`Up Arrow`,焦点将转到最后一个列表项目:

```
multiselectPrototype.handleArrowUpKey = function() {
  this._focusedItemIndex = (this._focusedItemIndex > 0)
    ? this._focusedItemIndex - 1        // go to the previous item
    : this.itemElements().length - 1;   // go to the last item

  this.refreshFocusedItem();
}; 
```

`refreshFocusedItem`方法将焦点设置到索引等于`_focusedItemIndex`的项目上:

```
multiselectPrototype.refreshFocusedItem = function() {
  this.itemElements()[this._focusedItemIndex].focus();
}; 
```

最后，我们需要更改`open`和`close`方法，这样，当多选打开时，焦点会转到索引为`_focusedItemIndex`的项目，当控件关闭时，焦点会回到多选字段:

```
multiselectPrototype.open = function() {
  this.togglePopup(true);
  this.refreshFocusedItem();
};

multiselectPrototype.close = function() {
  this.togglePopup(false);
  this._field.focus();
}; 
```

现在，我们可以添加一些 CSS 来使聚焦的项目看起来更有吸引力:

```
::content li:focus {
  outline: dotted 1px #333;
  background: #efefef;
} 
```

#### 用键盘选择/取消选择项目

`Enter`键允许用户选择当前聚焦的项目。如果多选是打开的，我们获得焦点项并用`selectItem`方法选择它:

```
multiselectPrototype.handleEnterKey = function() {
  if(this._isOpened) {
    var focusedItem = this.itemElements()[this._focusedItemIndex];
    this.selectItem(focusedItem);
  }
}; 
```

`Esc`键应删除最后选择的项目。如果存在任何选中的项目，我们通过调用`unselectItem`方法获取最后一个项目并取消选择它:

```
multiselectPrototype.handleBackspaceKey = function() {
  var selectedItemElements = this.querySelectorAll("li[selected]");

  if(selectedItemElements.length) {
    this.unselectItem(selectedItemElements[selectedItemElements.length - 1]);
  }
}; 
```

现在我们已经支持了所有必要的键盘交互，所以该组件只能与键盘一起使用。

### 视觉无障碍

#### 组件大小

多选在`em`中有相对大小，它的大小取决于容器的字体大小。因此，它是可扩展的，并且可以在必要时轻松增加:

![multiselect different sizes](img/0d016b724b4eb165498e9e1f48a1ccf5.png)

#### 高对比度模式

视力低下或有其他视觉障碍的人有时会使用高对比度模式。OS X 允许用户在设置中启用高对比度，而 Windows 提供特殊的[高对比度主题](http://windows.microsoft.com/en-us/windows7/turn-on-high-contrast)。还有一个流行的 Chrome 扩展，叫做(令人惊讶的)[高对比度](https://chrome.google.com/webstore/detail/high-contrast/djcfdncoelnlbldjfhinnjlhdjlikmph?hl=en)，允许用户用高对比度彩色滤镜浏览网页。

让我们看看组件在高对比度模式下的样子:

![multiselect in high contrast mode](img/8ea157836dc52c8380d923d2b0b36e49.png)

它看起来不错，但有一个问题:选中的项目很难与未选中的项目区分开来。对选定的和聚焦的项目稍微改变一下颜色就可以解决这个问题:

![multiselect in high contrast mode - fixed](img/5262ca307879ff50ea96fe72c8bc470f.png)

#### 没有颜色

颜色可访问性是视觉可访问性的另一个重要方面。有很多色盲用户在探索网络。这意味着颜色不应该是向用户传达重要信息的唯一方式。在这里，我们可以检查组件在灰度模式下的外观(通常由操作系统或特殊应用程序提供)。也就是说，我们的组件只包含灰色，所以这是一个我们可以跳过的检查。

## 演示

实现了上述所有内容后，这就是最终产品:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen [多选 Web 组件](http://codepen.io/SitePoint/pen/grqjLr/)。