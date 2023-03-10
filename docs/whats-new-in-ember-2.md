# Ember 2.0 有什么新功能？

> 原文：<https://www.sitepoint.com/whats-new-in-ember-2/>

*这篇文章由[斯蒂芬·马克斯](https://www.sitepoint.com/author/smax/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

Ember 是一个 JavaScript 框架，基于模型-视图-控制器(MVC)模式，用于创建单页面应用程序。最近，[发布了该框架的 2.0 版本](http://emberjs.com/blog/2015/08/13/ember-2-0-released.html)，口号是“稳定而不停滞”。这意味着 Ember 2.0 没有附带任何新功能，因为那些为 2.0 版本设计的功能(如 Glimmer 渲染引擎)在以前的版本中已经存在。

Ember 2.0 还删除了许多在 1.x 版本中已经过时的旧 API。这意味着，如果你能创建一个没有弃用警告的 Ember 1.13 应用，升级到 2.0 将是无缝的。这与去年围绕着 Angular 2.0 发布的狂热形成了鲜明的对比。

Ember 2.0 的特性被 Ember 团队称为“先驱特性”。因此，在本文的其余部分，我将重点介绍主要的前驱特性以及如何使用它们。我们还将研究未来版本中的一些特性。

如果你想继续下去，你需要使用最新版本的框架创建一个 Ember 项目。 *[演示一下](#)* 。

Ember 使用 npm 安装。关于 npm 的教程，可以看[这里](https://www.sitepoint.com/beginners-guide-node-package-manager/)。

```
npm install -g ember-cli
```

在撰写本文时，将会引入 1.13 版本

```
ember -v
=> version: 1.13.8
```

接下来，创建一个新的 Ember 应用程序:

```
ember new hello-world
```

导航到该目录并编辑`bower.json`文件，以包含最新版本的 Ember 和 ember-data

```
{
  "name": "hello-world",
  "dependencies": {
    "ember": "^2.0.0",
    "ember-data": "^2.0.0",
    ...
  }
}
```

回到终端运行:

```
bower install
```

Bower 可能会提示您 Ember 的版本解析。从提供的列表中选择 2.x 版本，并在前面加上感叹号，以将解决方案保留到`bower.json`。

接下来启动 Ember CLI 的开发服务器:

```
ember server
```

最后导航到 [http://localhost:4200/](http://localhost:4200/) 并检查浏览器控制台的版本。

## Ember 2.0 的先驱特性

### 视图

视图已被弃用，取而代之的是组件。组件由两部分组成(一个 JavaScript 组件文件和一个 Handlebars 模板)，组件在设计上是隔离的，更适合在整个应用程序中重用(不像视图)。一个兼容性插件是可用的，它将由 Ember 团队维护，直到 Ember 的 2.6 版本。这有助于将大量使用视图的应用程序过渡到最新版本。这个插件与 Ember 的兼容性将在 2.4 版本停止。当构建新的 Ember 应用程序时，开发人员应该更喜欢组件而不是视图。

### 数组控制器和对象控制器

`ArrayController`和`ObjectController`已经被弃用，取而代之的是通用的`Controller`类。这是因为[他们在开发者中制造了一些不必要的混乱](http://stackoverflow.com/questions/12596619/objectcontroller-and-arraycontroller)，如果没有指定控制器类型，Ember 会产生不明确的控制器类型。在这种情况下，将只有一种类型的控制器。

要进行这种转换，请更改如下所示的代码:

```
exports default Ember.ObjectController.extend({ 
```

或者:

```
exports default Ember.ArrayController.extend({ 
```

收件人:

```
exports default Ember.Controller.extend({ 
```

一个[控制器插件](https://github.com/Emberjs/Ember-legacy-controllers)也支持到 2.6 月，以帮助过渡应用程序。

### 属性绑定

在过去，`{{bind-attr}}`助手用于将属性绑定到 DOM 属性。有了 Ember 2.0，你不再需要这个助手了。而不是像这样做:

```
<a {{bind-attr href=location }} >Link Text</a> 
```

你现在可以做这样的事情，这样更好，更清晰

```
<a href={{location}} >Link Text</a> 
```

### 类绑定

随着`{{bind-attr}}`助手的弃用，将类绑定到属性变得更加简单。例如，不这样做

```
<div {{bind-attr class="isVisible"}} ></div> 
```

您现在可以这样做了

```
<div class='{{if isVisible "is-visible" }}' ></div> 
```

### 每个辅助对象上的视图和控制器选项

以下属性`itemView`、`itemViewClass`、`tagName`、`emptyView`、`emptyViewClass`和`itemController`已被弃用。这些是您在使用`{{each}}`助手时可以设置的选项。这种弃用是从视图和控制器转向组件的结果。

可以说组件提供了这些选项所提供的所有功能。例如:

```
{{each view.comments itemController="comment"
                     itemView="commentView"
                     emptyView="noCommentsView"
                     tagName="ul"}} 
```

变成了:

```
<ul>
  {{#each comments as |comment|}}
    {{post-comment comment=comment}}
  {{else}}
    {{no-comments}}
  {{/each}}
</ul> 
```

[阅读更多关于这一变化的信息](http://emberjs.com/deprecations/v1.x/#toc_view-and-controller-options-on-the-code-each-code-helper)。

### 块参数

引入了块参数。当使用几个手柄辅助工具时，这有助于统一和熟悉。例如当使用`{{each}}`助手时，这段代码

```
{{#each person in people}} 
```

现在变成了

```
{{#each people as |person| }} 
```

至于`{{with}}`助手，这段代码

```
{{#with teacher as person}} 
```

现在变成了

```
{{#with teacher as |person| }} 
```

[阅读更多关于这一变化的信息](http://emberjs.com/blog/2014/12/08/ember-1-9-0-released.html#toc_block-params)

### 控制器需求

控制器的`needs`属性不见了。这用于使一个控制器能够访问另一个控制器。现在，您可以将控制器名称设置为一个注入了值的属性。所以这段代码

```
exports default Ember.Controller.extend({
  needs: ['application']
}) 
```

更改为:

```
exports default Ember.Controller.extend({
  application:  Ember.inject.controller()
}) 
```

您现在可以以`application`的身份访问注入的控制器。与我们以前使用的较长的表格`controllers.application`相比，这个表格较短。

### 观察者参数排序

观察者参数现在会以相反的顺序排列。函数曾经是第一个参数，后面是依赖属性。现在我们将函数作为最后一个参数，而不是第一个。所以本质上这个代码

```
Ember.observer(function(){

}, 'someProperty1', 'someProperty2') 
```

成为

```
Ember.observer('someProperty1', 'someProperty2', function(){

}) 
```

### IE8 支持

对 Internet Explorer 8 的支持在 Ember 2.0 中已被删除。从现在开始，只支持 Internet Explorer 9 及以上版本。甚至有传言说要放弃对 IE9 的支持，但是经过大量的讨论后，最终决定在 Ember 2.0 中放弃 IE9 的好处没有那么大。

## 余烬 2.0 后的金丝雀特性

这些是新版本 Ember 发布时的一些预期特性。目前，它们还处于实验阶段，只能在金丝雀建筑中找到。要使用它们，您需要使用 Ember 配置文件中的`FEATURES`属性选择加入。可用功能列表[可以在这里找到](https://github.com/Emberjs/Ember.js/blob/master/FEATURES.md)。

### 尖括号组件

组件的尖括号语法已经实现。这使得以与常规 HTML 元素相同的样式调用组件成为可能。因此，您可以将下面的代码从

```
{{#my-component}}{{/my-component}} 
```

收件人:

```
<my-component></my-component> 
```

为了给现有应用程序的开发人员足够的时间进行升级，旧的语法将暂时与新的语法一起使用。除了更清晰的语法之外，尖括号组件将引入一个默认的单向数据流([，您可能从 React](https://www.sitepoint.com/video-introducing-one-way-data-flow/) 中了解到它)，并为双向数据流提供一个选择。

### 单向数据流和 Mut 关键字

在 Ember 的早期版本中，组件属性通常有两种绑定方式。这意味着组件的属性和它的数据源都是可变的。在 Ember 2.0 中，组件属性现在默认是不可变的。这种行为是对组件使用新的尖括号语法的结果。例如在下面的代码中使用旧的方法来调用一个组件。

```
{{#my-component firstName=model.name }}{{/my-component}} 
```

`firstName`属性被绑定到模型的`name`属性。当`name`属性的值改变时，这种改变反映在`firstName`属性中。同样，我们可以改变`firstName`属性，这种改变会反映到模型的`name`属性中。

使用新的语法(如下所示),我们只能更改`name`属性的值。我们不能更改`firstName`属性的值，因为它是只读的。

```
<my-component firstName=model.name ></my-component> 
```

然而，有一个新的`mut`关键字来规避这种默认行为。

```
<my-component firstName={{mut model.name}} ></my-component> 
```

这个关键字通过给属性两个属性使属性变得可变。首先，一个名为`update`的函数，您可以使用它来设置属性值，如下所示:

```
this.attrs.firstName.update("newFirstNameValue");
```

第二，一个叫`value`的属性。它保存属性的实际值，可以这样访问

```
this.attrs.firstName.value;
```

### 组件中的 Attrs 属性

新尖括号组件引入的另一个特性是组件的`attr`属性。调用期间传递的任何属性都不会直接出现在组件上，而是出现在一个名为`attr`的组件属性中。例如，当我们调用这个组件时:

```
<my-component property1="somevalue" property2="someothervalue" >
</my-component> 
```

过去，你可以像这样访问这两处房产。

```
this.get('property1')
this.get('property2') 
```

但是有了新的语法，你必须像这样访问它们

```
this.attrs.property1
this.attrs.property2 
```

### 反应式快速重新渲染

在 Ember 的早期版本中，重新渲染组件曾经是一项计算量很大的操作。Ember 曾经为组件拆除整个 DOM 并从头开始重建。在名为[微光](https://github.com/emberjs/ember.js/pull/10501)的新引擎中，`rerender`功能现在智能多了。它只重建 DOM 中需要改变的部分。这允许更快和更有效的重新渲染。

## 结论

Ember 2.0 无疑是朝着正确方向迈出的一步。对一致性的关注和对网络标准的坚持使 Ember 成为你下一个新项目的 JavaScript 框架的有力竞争者。请在下面的评论中告诉我们你对这些令人兴奋的新功能的想法。

对于那些希望深入了解这一点的人，我建议观看 Tom Dale 和 Yehuda Katz 的 EmberConf2015 开幕主题演讲，讲述在通往 Ember 2.0 的道路上发生的一切。尽情享受吧！

[https://www.youtube.com/embed/o12-90Dm-Qs](https://www.youtube.com/embed/o12-90Dm-Qs)

## 分享这篇文章