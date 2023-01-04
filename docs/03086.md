# 下划线. js 入门

> 原文：<https://www.sitepoint.com/getting-started-with-underscore-js/>

*这篇文章由[阿邦汉姆·科林斯](https://www.sitepoint.com/author/acollins/)和[瑞安·陈基](https://www.sitepoint.com/author/rchenkie/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

[underscript . js](http://underscorejs.org/)是一个 JavaScript 库，由[杰瑞米·阿什肯纳斯](https://en.wikipedia.org/wiki/Jeremy_Ashkenas)编写，为我们作为开发人员在面对 web 项目时可能会遇到的各种用例提供功能实用程序。

它使得代码更容易阅读:

```
_.isEmpty({});
// true 
```

这使得代码更容易编写:

```
_.flatten([[0, 1], [2, 3], [4, 5]]);
// [0, 1, 2, 3, 4, 5] 
```

它提供了没有 1:1 本机方法的特性:

```
_.range(5);
// [0, 1, 2, 3, 4] 
```

它本身甚至可以用作模板引擎:

```
_.template('<p><%= text %></p>', {text: 'SitePoint Rocks!'});
// <p>SitePoint Rocks!</p> 
```

下划线是一个轻量级库(只有 5.7kb，minified 和 Gzipped ),被各种知名项目使用，例如:

*   [今日美国](http://www.usatoday.com/)
*   [LinkedIn](https://www.linkedin.com/)
*   [可汗学院](https://www.khanacademy.org/)

现在让我们更具体地了解一下它的主要功能。

## 好的部分

在本教程中，我将重点介绍下划线最常用的三种方法:

*   [_。每个()](http://underscorejs.org/#each)
*   [_。模板()](http://underscorejs.org/#template)
*   [_。过滤器()](http://underscorejs.org/#filter)

我将解释它们是如何单独使用的，然后将它们结合在一起构建一个演示应用程序，您可以在教程的结尾找到[。和以往一样，这个演示的代码可以在](#demo) [Github](https://github.com/sitepoint-editors/spotifyplayground) 上获得。

如果您希望跟随示例，您需要获取库的副本，例如从您最喜欢的 CDN:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.8.3/underscore-min.js"></script> 
```

如果你发现自己在这个过程中需要帮助，或者你只是好奇想了解更多，不要忘记[下划线的文档](http://underscorejs.org/)是广泛的。它还有一个庞大而活跃的社区，这意味着很容易找到帮助。

## `_.each`:编写可读循环

没有一个项目在代码中的某个地方没有类似于这个片段的内容:

```
var artists = ['Pharrel Williams', 'Led Zeppelin', 'Rolling Stones'];

for(var i = 0; i < artists.length; i++) {
  console.log('artist: ' + artists[i]);
} 
```

下划线使您能够使用可读性更强的语法编写等效的代码:

```
var artists = ['Pharrel Williams', 'Led Zeppelin', 'Rolling Stones'];

_.each(artists, function(artist, index, artists) {
  console.log('artist: ' + artist);
}); 
```

很整洁，是吧？`_.each()`需要两个参数:

*   要迭代的数组(或对象)。
*   回调函数。

对于数组中的每个元素，`_.each()`将调用回调函数(在文档中称为*迭代*)。在回调函数中，我们可以访问另外三个参数:

*   当前迭代索引的数组值(`artist`)。例如，对于上面的代码片段，我们在第一次迭代中得到“Pharrel Williams”。
*   当前迭代的编号(`index`)，在我们的例子中，它将从 0 变化到 2。
*   我们正在迭代的数组(`artists`)。

正如你所看到的，代码可读性更好，我们可以访问数组中的单个元素，而不需要使用`artists[i]`，正如我们在使用`for`循环的例子中看到的。

见笔 [_。通过](http://codepen.io/SitePoint/pen/PZwPza/) [CodePen](http://codepen.io) 上的 [@SitePoint](http://codepen.io/SitePoint) 的每一个。