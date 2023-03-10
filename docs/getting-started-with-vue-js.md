# Vue.js 入门

> 原文：<https://www.sitepoint.com/getting-started-with-vue-js/>

***2016 年 12 月 5 日**:本教程涵盖了 Vue.js 1.x，如果你在寻找 Vue 2 的内容，请看这里:[使用 Vue.js 2.0 框架入门并运行](https://www.sitepoint.com/up-and-running-vue-js-2-0/)。*

***2016 年 7 月 21 日**:文章已更新，涵盖了 Vue.js 1.0.x，并增加了关于组件的部分。*

[Vue.js](http://vuejs.org) 是一个 JavaScript 库，帮助你使用[MVVM(模型-视图-视图模型)架构模式](https://en.wikipedia.org/wiki/Model_View_ViewModel)构建 web 应用。乍一看，它似乎与 [AngularJS](https://angularjs.org/) 非常相似，但是一旦你开始使用它，你会很快意识到 Vue.js 不仅简单易学，而且更加灵活。

在本入门教程中，我将向您介绍 Vue.js 的基本概念，并对其最重要的特性进行全面概述。

> Vue.js 1.0.x 有一些与 Vue.js 0.12.x 不兼容的语法变化。如果您有使用这些早期版本的经验，您可能已经注意到本教程中的一些变化。你可以在这里得到所有变化的概述:[vue . js 1.0 的新功能](https://www.sitepoint.com/whats-new-in-vue-js-1-0/)

## 向页面添加 Vue.js

虽然你可以从 GitHub 获得 Vue.js 的最新版本，但你可能会发现从 CDN 加载它更容易:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/1.0.26/vue.min.js">  </script> 
```

## 创建视图模型

在 Vue.js 中，视图模型是使用`Vue`类创建的。如果你想知道什么是视图模型，你可以把它想象成一个对象，它可以让你很容易地在一个视图中显示你的模型数据(你可以把任何对象文字当作一个模型，把任何 HTML UI 元素当作一个视图)。

为了了解视图模型是如何工作的，让我们从创建一个视图开始。一个空的`<div>`就可以了:

```
<div id="my_view">
</div> 
```

这是一个对象文字，它将成为我们的模型。因为它处理 JavaScript 代码，所以要确保在一个`<script>`标签中或者在一个单独的 JS 文件中创建它。

```
var myModel = {
  name: "Ashley",
  age: 24
}; 
```

现在我们有了视图和模型，是时候创建一个视图-模型(一个`Vue`实例)将两者绑定在一起了:

```
var myViewModel = new Vue({
  el: '#my_view',
  data: myModel
}); 
```

如您所见，`el`属性指向视图(您可以在这里使用任何 CSS 选择器)，而`data`属性指向模型。我们的视图模型现在可以使用了。

要在视图中显示模型的数据，应该使用小胡子式绑定。例如，要显示模型的`age`属性，您可以在视图中添加字符串`{{ age }}`。以下代码片段使用了模型的两个属性:

```
<div id="my_view">
  {{ name }} is {{ age }} years old.
  <!-- Evaluated to "Ashley is 24 years old" -->
</div> 
```

您对模型所做的任何更改都将立即在视图中可见。

## 创建双向绑定

我们在前面的例子中使用的小胡子式绑定是一种单向绑定。这意味着它只能显示模型的数据，而不能修改它。如果您想让视图编辑模型，您应该使用`v-model`指令创建一个双向绑定。

让我们改变我们的视图，使它包含一个`input`元素，它的`v-model`指向`name`属性:

```
<div id="my_view">
  <label for="name">Enter name:</label>
  <input type="text" v-model="name" id="name" name="name" />
  <p>{{ name }} is {{ age }} years old.</p>
</div> 
```

此时，如果您编辑输入字段中的值，您的模型将立即改变。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [XbYZBJ](http://codepen.io/SitePoint/pen/XbYZBJ/) 。