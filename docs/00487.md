# 5 jQuery.each()函数示例

> 原文：<https://www.sitepoint.com/jquery-each-function-examples/>

**这是对`jQuery.each()`函数的全面概述 jQuery 最重要和最常用的函数之一。在本文中，我们将找出原因，并看看如何使用它。**

## 什么是 jQuery.each()

[jQuery 的 each()函数](https://api.jquery.com/jquery.each/)用于遍历目标 jQuery 对象的每个元素，该对象包含一个或多个 DOM 元素，并公开所有 jQuery 函数。对于多元素 DOM 操作，以及对任意数组和对象属性的迭代，它非常有用。

除了这个函数之外，jQuery 还提供了一个同名的 helper 函数，可以在没有选择或创建任何 DOM 元素的情况下调用这个函数。

## jQuery.each()语法

让我们看看不同的模式在起作用。

以下示例选择网页上的每个`<div>`元素，并输出每个元素的索引和 ID:

```
// DOM ELEMENTS
$('div').each(function(index, value) {
  console.log(`div${index}: ${this.id}`);
}); 
```

可能的输出是:

```
div0:header
div1:main
div2:footer 
```

这个版本使用 jQuery 的`$(selector).each()`函数，而不是 utility 函数。

下一个示例显示了 utility 函数的用法。在这种情况下，要循环的对象作为第一个参数给出。在本例中，我们将展示如何循环访问一个数组:

```
// ARRAYS
const arr = [
  'one',
  'two',
  'three',
  'four',
  'five'
];

$.each(arr, function(index, value) {
  console.log(value);
  // Will stop running after "three"
  return (value !== 'three');
});

// Outputs: one two three 
```

在最后一个示例中，我们想要演示如何迭代对象的属性:

```
// OBJECTS
const obj = {
  one: 1,
  two: 2,
  three: 3,
  four: 4,
  five: 5
};

$.each(obj, function(key, value) {
  console.log(value);
});

// Outputs: 1 2 3 4 5 
```

这一切都归结为提供一个适当的回调。回调的上下文`this`将等于它的第二个参数，即当前值。然而，因为上下文总是一个对象，所以原始值必须被包装:

```
$.each({ one: 1, two: 2 } , function(key, value) {
  console.log(this);
});

// Number { 1 }
// Number { 2 } 
```

`

这意味着在价值和环境之间没有严格的相等性。

```
$.each({ one: 1 } , function(key, value) {
  console.log(this == value);
  console.log(this === value);
});

// true
// false 
```

`

第一个参数是当前索引，可以是数字(对于数组)或字符串(对于对象)。

## 1.基本 jQuery.each()函数示例

让我们看看 jQuery.each()函数如何结合 jQuery 对象帮助我们。第一个示例选择页面中的所有`a`元素，并输出它们的`href`属性:

```
$('a').each(function(index, value){
  console.log(this.href);
}); 
```

第二个例子输出 web 页面上的每个外部`href`(假设只有 HTTP(S)协议):

```
$('a').each(function(index, value){
  const link = this.href;

  if (link.match(/https?:\/\//)) {
    console.log(link);
  }
}); 
```

假设页面上有以下链接:

```
<a href="https://www.sitepoint.com/">SitePoint</a>
<a href="https://developer.mozilla.org">MDN web docs</a>
<a href="http://example.com/">Example Domain</a> 
```

第二个示例将输出:

```
https://www.sitepoint.com/
https://developer.mozilla.org/
http://example.com/ 
```

我们应该注意，来自 jQuery 对象的 DOM 元素在传递给`jQuery.each()`的回调函数中是“本地”形式。原因是 jQuery 实际上只是一个包装 DOM 元素数组的包装器。通过使用`jQuery.each()`，这个数组以与普通数组相同的方式被迭代。因此，我们不会将包装好的元素开箱即用。

参考我们的第二个例子，这意味着我们可以通过编写`this.href`来获得元素的`href`属性。如果我们想使用 [jQuery 的`attr()`](https://api.jquery.com/attr/) 方法，我们需要像这样重新包装元素:`$(this).attr('href')`。

## 2.jQuery.each()数组示例

让我们再看看如何处理一个普通的数组:

```
const numbers = [1, 2, 3, 4, 5];
$.each(numbers, function(index, value){
  console.log(`${index}: ${value}`);
}); 
```

该代码片段输出:

```
0:1
1:2
2:3
3:4
4:5 
```

这里没什么特别的。一个数组以数字索引为特征，所以我们获得从 *0* 开始一直到 *N-1* 的数字，其中 *N* 是数组中元素的数量。

## 3.jQuery.each() JSON 示例

我们可能有更复杂的数据结构，比如数组中的数组，对象中的对象，对象中的数组，或者数组中的对象。让我们看看`jQuery.each()`如何在这种情况下帮助我们:

```
const colors = [
  { 'red': '#f00' },
  { 'green': '#0f0' },
  { 'blue': '#00f' }
];

$.each(colors, function() {
  $.each(this, function(name, value) {
    console.log(`${name} = ${value}`);
  });
}); 
```

此示例输出:

```
red = #f00
green = #0f0
blue = #00f 
```

我们通过对`jQuery.each()`的嵌套调用来处理嵌套结构。外部调用处理变量数组`colors`；内部调用处理单个对象。在这个例子中，每个对象只有一个键，但是一般来说，任何数字都可以用这个代码来处理。

## 4.jQuery.each()类示例

这个例子展示了如何使用下面的 HTML 中给出的赋值类`productDescription`遍历每个元素:

```
<div class="productDescription">Red</div>
<div>Pink</div>
<div class="productDescription">Orange</div>
<div class="generalDescription">Teal</div>
<div class="productDescription">Green</div> 
```

我们在选择器上使用`each()`助手，而不是`each()`方法。

```
$.each($('.productDescription'), function(index, value) {
  console.log(index + ':' + $(value).text());
}); 
```

在这种情况下，输出是:

```
0:Red
1:Orange
2:Green 
```

我们不必包括指数和值。这些只是帮助确定我们当前迭代的 DOM 元素的参数。此外，在这种情况下，我们还可以使用更方便的`each`方法。我们可以这样写:

```
$('.productDescription').each(function() {
  console.log($(this).text());
}); 
```

我们将在控制台上获得:

```
Red
Orange
Green 
```

注意，我们将 DOM 元素包装在一个新的 jQuery 实例中，这样我们就可以使用 jQuery 的 [`text()`方法](https://api.jquery.com/text/)来获取元素的文本内容。

## 5.jQuery.each()延迟示例

在下一个例子中，当用户单击 ID 为`5demo`的元素时，所有列表项将立即设置为橙色。

```
<ul id="5demo">
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
  <li>Four</li>
  <li>Five</li>
</ul> 
```

在索引相关的延迟( *0* 、 *200* 、 *400* 、…毫秒)之后，我们淡出元素:

```
$('#5demo').on('click', function(e) {
  $('li').each(function(index) {
    $(this).css('background-color', 'orange')
           .delay(index * 200)
           .fadeOut(1500);
  });

  e.preventDefault();
}); 
```

## 结论

在这篇文章中，我们展示了如何使用`jQuery.each()`函数迭代 DOM 元素、数组和对象。这是一个强大而省时的小功能，开发者应该把它放在工具箱里。

如果你不喜欢 jQuery，你可以考虑使用 JavaScript 的本地 [Object.keys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) 和[array . prototype . foreach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)方法。还有像 [foreach](https://www.npmjs.com/package/foreach) 这样的库，可以让你迭代数组类对象或字典类对象的键值对。

**记住:** `$.each()`和`$(selector).each()`是两种不同的方法，用两种不同的方式定义。

这篇受欢迎的文章于 2020 年更新，以反映当前的最佳实践，并更新结论中关于使用现代 JavaScript 的本地解决方案的建议。要获得更深入的 JavaScript 知识，请阅读我们的书， [JavaScript:新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition?utm_source=blog&utm_medium=articles)。

## 分享这篇文章