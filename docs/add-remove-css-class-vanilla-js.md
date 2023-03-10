# 快速提示:用普通的 JavaScript 添加或删除 CSS 类

> 原文：<https://www.sitepoint.com/add-remove-css-class-vanilla-js/>

有时你需要用 JavaScript 添加或删除一个 CSS 类，你不想像 jQuery 一样包含一个完整的库来完成。

当您希望页面元素响应用户操作而改变时，这很有用。

示例用途包括:

*   显示或隐藏菜单
*   突出显示表单错误
*   显示一个对话框
*   响应于选择而显示不同的内容
*   动画元素以响应点击

有两个 JavaScript 属性允许您使用类:`className`和`classList`。前者是[广泛兼容](https://developer.mozilla.org/en-US/docs/Web/API/Element/className#Browser_compatibility)，后者是[更现代](https://developer.mozilla.org/en-US/docs/Web/API/Element/className#Browser_compatibility)更方便。如果不需要支持 IE 8 和 9，可以跳过`className`。

我们先从兼容版本开始。

> 注意:本教程假设您对函数和变量等 JavaScript 概念有所了解。

## 以兼容的方式修改类

JavaScript `className`属性允许您访问 HTML 元素的`class`属性。一些字符串操作将允许我们添加和删除类。

我们将使用 [`querySelectorAll()`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) 访问 HTML 元素，它兼容 IE8 及以上版本的浏览器。

### 添加一个类

要添加一个类，我们将编写一个函数，它接受我们想要更改的元素，并为所有这些元素添加一个指定的类。

```
function addClass(elements, myClass) {

  // if there are no elements, we're done
  if (!elements) { return; }

  // if we have a selector, get the chosen elements
  if (typeof(elements) === 'string') {
    elements = document.querySelectorAll(elements);
  }

  // if we have a single DOM element, make it an array to simplify behavior
  else if (elements.tagName) { elements=[elements]; }

  // add class to all chosen elements
  for (var i=0; i<elements.length; i++) {

    // if class is not already found
    if ( (' '+elements[i].className+' ').indexOf(' '+myClass+' ') < 0 ) {

      // add class
      elements[i].className += ' ' + myClass;
    }
  }
} 
```

您将很快看到该函数是如何工作的，但是要观看该函数的运行，请随意使用这个 CSS:

```
.red {
  background: red;
}

.highlight {
  background: gold;
} 
```

…还有这个 HTML:

```
<div id="iddiv" class="highlight">ID div</div>

<div class="classdiv">Class div</div>
<div class="classdiv">Class div</div>
<div class="classdiv">Class div</div> 
```

下面是函数本身的一些用法示例:

```
addClass('#iddiv','highlight');
addClass('.classdiv','highlight');

addClass(document.getElementById('iddiv'),'highlight');
addClass(document.querySelector('.classdiv'),'highlight');
addClass(document.querySelectorAll('.classdiv'),'highlight'); 
```

请注意，您可以通过选择器来标识想要更改的 HTML 元素，也可以直接传入元素本身。

### 我们的 addClass 函数如何工作

我们的`addClass`函数首先接受两个参数:我们想要修改的 HTML 元素和我们想要添加的类。我们的目标是遍历每个 HTML 元素，确保该类不在那里，然后添加该类。

第一，如果元素列表是空的，我们的函数就没什么可做的了，可以早点出来。

```
// if there are no elements, we're done
if (!elements) { return; } 
```

接下来，如果我们已经选择通过一个选择器如`#iddiv`或`.classdiv`来识别我们的 HTML 元素，那么我们可以使用`querySelectorAll()`来获取所有我们想要的元素。

```
// if we have a selector, get the chosen elements
if (typeof(elements) === 'string') {
  elements = document.querySelectorAll(elements);
} 
```

然而，如果 DOM 元素被直接输入到函数中，我们可以循环访问它们。如果只有一个 DOM 元素(而不是一个列表)，我们将把它作为一个数组，这样我们就可以使用相同的循环并简化代码。我们可以判断是否只有一个元素，因为一个元素有一个[标记名](https://developer.mozilla.org/en-US/docs/Web/API/Element/tagName)属性，而一个列表没有。

```
// if we have a single DOM element, make it an array to simplify behavior
else if (elements.tagName) { elements=[elements]; } 
```

现在我们有了可以循环的格式的元素，我们将遍历每个元素，检查类是否已经存在，如果没有，我们将添加类。

```
// add class to all chosen elements
for (var i=0; i<elements.length; i++) {

  // if class is not already found
  if ( (' '+elements[i].className+' ').indexOf(' '+myClass+' ') < 0 ) {

    // add class
    elements[i].className += ' ' + myClass;
  }
} 
```

请注意，我们在开头和结尾添加了一个空格，以简化我们正在寻找的模式，并避免需要正则表达式。

无论如何，我们已经完成了——您现在可以添加一个类了！

### 删除一个类

要删除一个类，我们可以使用下面的函数:

```
function removeClass(elements, myClass) {

  // if there are no elements, we're done
  if (!elements) { return; }

  // if we have a selector, get the chosen elements
  if (typeof(elements) === 'string') {
    elements = document.querySelectorAll(elements);
  }

  // if we have a single DOM element, make it an array to simplify behavior
  else if (elements.tagName) { elements=[elements]; }

  // create pattern to find class name
  var reg = new RegExp('(^| )'+myClass+'($| )','g');

  // remove class from all chosen elements
  for (var i=0; i<elements.length; i++) {
    elements[i].className = elements[i].className.replace(reg,' ');
  }
} 
```

这个`removeClass`函数的大部分工作方式与我们的`addClass`函数相同；通过收集所需的 HTML 元素并遍历它们。唯一的区别是类被删除的部分。

下面是更详细的类删除:

```
// create pattern to find class name
var reg = new RegExp('(^| )'+myClass+'($| )','g');

// remove class from all chosen elements
for (var i=0; i<elements.length; i++) {
  elements[i].className = elements[i].className.replace(reg,' ');
} 
```

首先，我们创建一个正则表达式来寻找我们想要的类的所有实例。表达式`'(^| )'+myClass+'($| )'`表示开头或空格，后跟`myClass`，后跟结尾或空格。`'g'`意味着全局匹配，这意味着找到模式的所有实例。

使用我们的模式，我们用空格替换类名。这样，列表中间的类名将保持分离，如果被删除的类在末尾也没有什么坏处。

## 以现代方式修改类

IE10 及以上版本的浏览器支持名为 [classList](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList#Browser_compatibility) 的属性，这使得元素的类更容易处理。

在[之前的一篇文章](https://www.sitepoint.com/jquery-vs-raw-javascript-2-css3-animation/)中，Craig Buckler 列出了`classList`可以做的事情:

> 以下是可用的属性:
> 
> length —应用的类名数量
> item(index) —特定索引
> 包含的类名(class) —如果节点应用了该类，则返回 true
> add(class) —向节点应用新类
> remove(class) —从节点
> 切换中删除类(class)—分别删除或添加已应用或未应用的类
> 
> 我们可以优先使用这个属性，而不是 clunkier className 属性:
> 
> ```
> document.getElementById("myelement").classList.add("myclass"); 
> ```

让我们使用这些信息来创建函数，从所有匹配选择器的元素中添加和删除类。

这些函数将获取所有需要的元素，遍历它们，并为每个元素添加或移除一个类。

### 添加类别

```
function addClass(selector, myClass) {

  // get all elements that match our selector
  elements = document.querySelectorAll(selector);

  // add class to all chosen elements
  for (var i=0; i<elements.length; i++) {
    elements[i].classList.add(myClass);
  }
}

// usage examples:
addClass('.class-selector', 'example-class');
addClass('#id-selector', 'example-class'); 
```

### 移除类别

```
function removeClass(selector, myClass) {

  // get all elements that match our selector
  elements = document.querySelectorAll(selector);

  // remove class from all chosen elements
  for (var i=0; i<elements.length; i++) {
    elements[i].classList.remove(myClass);
  }
}

// usage examples:
removeClass('.class-selector', 'example-class');
removeClass('#id-selector', 'example-class'); 
```

## 结论

我们已经介绍了如何通过`className`(兼容方式)和`classList`(更现代的方式)添加和删除类。

当您可以通过 JavaScript 控制 CSS 类时，您可以解锁许多功能，包括内容显示更新、动画、错误消息、对话框、菜单等等。

希望这篇文章对你有所帮助，如果你有任何问题或想法，欢迎在评论中分享。

## 分享这篇文章