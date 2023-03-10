# MooTools 入门

> 原文：<https://www.sitepoint.com/getting-started-with-mootools/>

本教程介绍了当今最流行的 JavaScript 框架之一 MooTools。MooTools，代表*我的面向对象工具*，兼容 Internet Explorer 6+，Firefox，Opera 和 Chrome。MooTools 的设计是紧凑的、模块化的，当然也是面向对象的。MooTools 是为中级到专家级的 JavaScript 程序员设计的，所以在开始之前要确保你有足够的经验。


## 获取 MooTools

获得 MooTools 相当简单。MooTools 分为两部分，称为核心和更多。Core 包含框架的核心类和模块，而 More 包含基于应用程序需求的附加类。MooTools 拥有强大的构建页面，可以根据我们的需求定制核心页面和 T2 页面。MooTools Core 的当前稳定版本(在撰写本文时)是 1.4.5，更多的是 1.4.0.1。

## 元素选择器

任何 JavaScript 框架最基本的操作之一就是选择元素。MooTools 使用 [Slick](https://github.com/mootools/slick "mootools/slick") 作为它的选择器引擎。MooTools 支持许多不同类型的选择器。本节涵盖了一些最有用和最重要的选择器。

### 按 ID 选择元素

如果我们想在普通 JavaScript 中通过 ID 选择一个元素，我们需要使用长的`document.getElementById('id_of_element')`语法。MooTools 和许多其他 JavaScript 框架一样，通过使用`$`选择元素来缩短这个时间。您的代码最终看起来像这样:

```
var element =  $('id_of_element');
```

如果你在同一个页面上包含 MooTools 和其他使用`$`(例如 jQuery)的库，这将会产生问题。为了克服这种情况，MooTools 提供了`document.id()`作为选择元素的另一种方式。您通过`id`选择元素的代码现在看起来像这样:

```
var element =  document.id('id_of_element');
```

### 按类选择元素

这将返回某个类的元素数组。为了获得每个单独的元素，我们需要遍历数组，如下所示。

```
$$('.class_name').each(function(ele){
  console.log(ele);
});
```

### 通过属性模式选择元素

以下示例选择其`id`和`class`属性以特定模式开始的元素。

```
$$('[id^=id_start_]').each(function(ele){
  console.log(ele);
});

$$('[class^=class_start_]').each(function(ele){
  console.log(ele);
});
```

类似地，下面的例子匹配其`id`和`class`属性以特定模式结束的元素。

```
$$('[id$=_end_id]').each(function(ele){
  console.log(ele);
});

$$('[class$=_end_class]').each(function(ele){
  console.log(ele);
});
```

## DomReady 事件

DomReady 是一个只能绑定到窗口对象的事件。DomReady 在 DOM 加载后立即执行，这可能比等待所有其他资源加载的`window.load`事件早得多。我建议在 DomReady 和`window.load` 的深度对比中阅读这个[。以下示例使用 DomReady 等待 DOM 加载后再进行查询。](http://www.xpertdeveloper.com/2010/09/mootools-domready-vs-window-onload/ "Mootools domready vs window.onload")

```
window.addEvent('domready', function(){
  if(document.id('id_of_element'))
  {
    alert('Element Exists');
  }
});
```

## 元素创建

MooTools 可以创建新的 HTML 元素，并将它们注入 DOM。在 MooTools 中创建新的 HTML 元素非常简单。例如，下面的代码创建了一个新的`div`元素。

```
var new_div = new Element('div', {'class': 'new_div'});
```

前面的代码创建了一个新元素，但是没有将它注入 DOM。要执行注入，您需要调用`adopt()`方法。下面的例子说明了这是如何做到的。

```
var new_div = new Element('div', {'class': 'new_div'});

$$('body').adopt(new_div);
```

当这段代码被执行时，您可以看到新的`div`被添加到结束的`body`标签之前。

## 事件绑定

当在元素上执行某些事件时，事件绑定导致代码被执行。点击、双击和悬停是常见事件的例子。您也可以创建自己的定制事件，但这超出了本文的范围。作为 MooTools 事件绑定的一个示例，以下代码向元素添加了一个简单的 click 事件处理程序。

```
document.id('id_of_ele').addEvent('click', function(){
  console.log('I was just clicked');
});
```

您还可以向动态创建的元素添加事件。下面的代码将 click 处理程序添加到动态创建的元素中。

```
var new_div = new Element('div', {'class': 'new_div'});
$$('body').adopt(new_div);
new_div.addEvent('click', function(){
  console.log('I was just clicked');
});
```

## 浏览器检测

最后但同样重要的是，使用 MooTools 进行浏览器检测。当您希望基于用户的浏览器编写条件代码时，这是必需的。MooTools 提供了`Browser`对象，它在页面加载时被填充。下面的例子使用了一个`switch`语句来识别当前的浏览器。

```
switch(Browser.name)
{
  case "safari":
    console.log('I am Safari');
    break;
  case "chrome":
    console.log('I am Chrome');
    break;
  case "firefox":
    console.log('I am FireFox');
    break;
  case "opera":
    console.log('I am Opera');
    break;
  case "ie":
    console.log('I am IE');
    break;
}
```

## 结论

本文涵盖了 MooTools 的许多基础知识。有一些学习有效使用 MooTools 的好资源。我从 MooTools 的文档和 T2 的大卫·沃什的博客中学到了很多。也可以参考[我的 MooTools 作品](http://www.xpertdeveloper.com/ "Expert PHP Developer")。

## 分享这篇文章