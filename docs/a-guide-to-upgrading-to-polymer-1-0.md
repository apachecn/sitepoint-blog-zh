# 升级到聚合物 1.0 的指南

> 原文：<https://www.sitepoint.com/a-guide-to-upgrading-to-polymer-1-0/>

在最近结束的 [Google IO 2015](https://events.google.com/io2015/) 上，Google 发布了期待已久的 1.0 版 Polymer，[宣布它已经可以生产了](http://googledevelopers.blogspot.in/2015/05/polymer-10-released.html)。对于那些还在开发者预览版时就已经在使用 Polymer 库的人来说，这篇文章将作为将现有应用程序迁移到 Polymer 最新版本的指南。

关于 [v1.0](https://www.polymer-project.org/1.0/docs/release-notes.html) 需要注意的一些重要事项:

1.  与[版本 0.5](https://www.polymer-project.org/0.5/) 不兼容，是之前的版本，也是目前存活时间最长的版本。
2.  新版本侧重于性能和效率，同时显著减小了库的整体大小。
3.  它已经完全重新构建，使开发人员能够更容易、更快地设计更像标准 DOM 元素的定制元素。
4.  [内部基准测试](https://github.com/polymerlabs/benchmarks/)显示，与之前的版本相比，1.0 版在 Chrome 上快了约 3 倍，在 Safari 上快了 4 倍。

安装最新版本的 Polymer 的步骤与本文中描述的[完全相同。要更新 Polymer 的现有安装，请导航到 app 目录，并在终端中运行以下命令:](https://www.sitepoint.com/introduction-to-web-components-and-polymer-tutorial/)

```
$ bower update
```

重要的是要意识到，这肯定会破坏你现有的应用程序，因为，如上所述，这两个版本是不兼容的。因此，建议在单独的文件夹中安装新的副本。为了说明从 v0.5 开始的变化，我们将使用我在 SitePoint 上的一篇文章中的信用卡定制元素作为参考，对两个版本进行比较。

## 多填充不支持的浏览器

新版本的 Polymer 不再需要包含在`webcomponents.js`库中的 shadow DOM polyfill。相反，使用小得多的`webcomponents-lite.js`库来填充旧的浏览器。

版本 0.5:

```
<script src="bower_components/webcomponentsjs/webcomponents.min.js"></script>
```

版本 1.0:

```
<script src="bower_components/webcomponentsjs/webcomponents-lite.min.js"></script>
```

“lite”版本的尺寸比上一代产品增加了大约`80kb`，这在性能是关键因素的情况下意义重大。

## 定义自定义元素

标签`<polymer-element>`已经被保存自定义元素定义的标签`<dom-module>`所取代。定制元素现在由`<dom-module>`标签的`id`属性来标识。命名定制元素的规则仍然保持不变。

版本 0.5:

```
<polymer-element name="credit-card">
  ...
</polymer-element>
```

版本 1.0:

```
<dom-module id="credit-card">
  ...
</dom-module>
```

## 注册自定义元素

以前，我们可以通过简单地调用`Polymer()`构造函数来注册定制元素。如果`<script>`标签在`<polymer-element>`标签内，指定定制元素名称是可选的。在这个版本中，定制元素现在通过使用原型上的`is`属性来注册。

版本 0.5:

```
Polymer('credit-card', {});
```

版本 1.0:

```
Polymer({
  is: 'credit-card'
});
```

属性`is`的值必须与定制元素的`<dom-module>`标签的`id`属性相匹配，并且与之前的版本不同，这是**而不是可选的**。

`<script>`标签可以在`<dom-module>`元素的内部或外部，但是元素的模板必须在调用聚合物构造函数之前进行解析。

## 自定义元素属性

任何属于`<polymer-element>`标签的属性现在都应该和数据类型一起在`properties`对象上声明。

版本 0.5:

```
<polymer-element name='credit-card' attributes='amount'>
```

版本 1.0:

```
Polymer({
  is: 'credit-card',
  properties: {
    amount: {
      type: Number
    }
  }
```

## 自定义元素样式

元素样式现在定义在`<template>`标签之外。

版本 0.5:

```
<polymer-element name="credit-card" attributes="amount">
  <template>
    <style> ... </style>
  </template>
</polymer-element>
```

版本 1.0:

```
<dom-module id="credit-card">
  <style> ... </style>

  <template>
    ...
  </template>
</dom-module>
```

使用 [HTML 导入](https://www.polymer-project.org/1.0/docs/devguide/styling.html#external-stylesheets)支持外部样式表。

## 数据绑定

Polymer 1.0 提供了两种类型的数据绑定:

*   方括号`[[]]`创建单向绑定。数据流是向下的，从主机到子级，绑定从不修改主机属性。
*   花括号`{{}}`创建自动绑定。数据流是单向的还是双向的，这取决于目标属性是否配置为双向绑定。

在此版本中，绑定必须替换节点的整个文本内容，或者属性的整个值。因此不支持字符串串联。对于属性值，建议使用计算绑定，而不是字符串连接。

版本 0.5:

```
<input type="submit" value="Donate {{amount}}">
```

版本 1.0:

```
<template>
  <input type="submit" value="[[computeValue(amount)]]">
</template>
```

```
Polymer({
  is: "inline-compute",
  computeValue: function(amount) {
    return 'Donate ' + amount;
  }
});
```

请注意，这意味着节点不能在绑定注释周围包含任何空白。

## 新的阴暗的世界

Polymer v0.5 使用 shadow DOM 为开发人员提供了一个更简单的元素接口，并通过将子树隐藏在影子根后面来抽象所有的复杂性。这构成了封装的基础，它在支持影子 DOM 的浏览器中运行良好。

对于尚不支持阴影 DOM 的浏览器来说，实现与原生阴影 DOM 完全一样的 polyfill 是很困难的，通常比原生实现要慢，并且需要大量代码。出于这些原因，Polymer 团队决定放弃 shadow DOM polyfill，转而构建一个轻量级的本地 DOM 版本，提供类似于 shadow DOM 的封装。

值得注意的是，shady DOM 和 shadow DOM 相互兼容，这意味着 shady DOM API 在浏览器中使用可用的原生 shadow DOM，而在不支持的浏览器中回退到 shady DOM。

## 结论

根据应用程序的复杂程度，将应用程序升级到 Polymer v1.0 可能是一个非常缓慢的过程，但在更快的加载时间和明显更小的有效载荷方面提供了巨大的好处。Polymer project 网站上提供的官方迁移指南更深入地介绍了内部 API 的一系列其他变化，请务必查看。

此外，Chuck Horton 已经建立了一个名为 [Road to Polymer 1.0](http://chuckh.github.io/road-to-polymer/) 的 Github 库，它提供了一个[代码转换工具](http://chuckh.github.io/road-to-polymer/convert-code.html)来将您的代码从 v0.5 更新到 v1.0。如果您不准备手动进行一些修饰性的更改，这可以作为您迁移的起点。

## 分享这篇文章