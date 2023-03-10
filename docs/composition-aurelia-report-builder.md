# Aurelia.io 中的组合:创建报表生成器

> 原文：<https://www.sitepoint.com/composition-aurelia-report-builder/>

当了解一个新的框架时，我们经常看到描述框架基本特性的简单演示，例如众所周知的 [TodoMVC 应用程序](http://todomvc.com/)。这很好——我的意思是谁不喜欢待办事项应用程序，对吗？今天，我们将采取一个稍微不同的策略。我们将避开这一共性，转而关注 Aurelia 框架的一个独特的核心特性:视觉合成。

Aurelia，block 上的[新人，已经在以前的文章中介绍过了，还有它扩展 HTML](https://www.sitepoint.com/creating-next-generation-javascript-application-aurelia/) 的[能力。到本文结束时，我们应该能更好地理解合成是如何帮助将小的可重用组件组装成复杂的屏幕的。为此，我们将创建一个报表生成器应用程序。你可以在这里](https://www.sitepoint.com/extending-html-aurelia-io-way/)找到[这个应用的演示，在这里](http://zewa666.github.io/aurelia-reporter)找到[完整的源代码。](https://github.com/sitepoint-editors/aurelia-reporter)

## 什么是视觉构图？

计算机科学中组合的基本思想是，在对象组合的情况下，将简单的对象/数据类型的小实体组合成更大更复杂的实体。同样的事情也适用于函数组合，其中一个函数的结果作为属性传递给下一个函数，依此类推。视觉合成共享这一基本概念，允许将多个不同的子视图聚合成一个更复杂的视图。

在谈论视觉构成时，需要考虑的一个重要问题是异质子项和同质子项之间的区别。为了理解这一点，让我们看下图。

![Diagram depicting homogeneous and heterogeneous composition](img/e3e4886285a9d8ea7918cf997dc8c46e.png)

*视觉构图类型比较*

在左边，我们看到一个同质构图的例子。顾名思义，这就是呈现具有相同类型和不同内容的项目。大多数框架在创建重复列表时都会使用这种类型的组合。如示例所示，想象一个简单的项目列表被依次呈现。在右边，我们可以看到一个异质组合的例子。主要的区别在于具有*不同*类型和视图的项目的组装。该示例演示了一个由几个具有不同内容和用途的构建块组成的页面。

许多框架通过路由器视图提供该功能，其中特定的视图区域被放置在屏幕上，不同的路由端点被加载。这种方法的明显缺点是应用程序需要路由器。除此之外，创建复杂的视图组合仍然是一项非常乏味的任务，尤其是当您考虑到嵌套组合时。

另一方面，除了路由器视图之外，Aurelia 还提供了另一种方法，通过自定义元素将视觉合成作为一流的功能来展示。这样，它甚至在视觉层面上加强了关注点的分离，从而引导开发人员创建小的和可重用的组件。结果是增加了模块性，并有机会从已有的视图中创建新的视图。

## 使用 Aurelia 的作曲元素

为了利用 Aurelia 中的可视组合，我们可以利用预定义的组合定制元素。它基于 Aurelia 的一个关键约定，视图和视图-模型(VM)对(本文也称之为页面)。简而言之，`compose`允许我们在另一个视图中的任何特定位置包含一个页面。

下面的代码片段演示了如何使用它。在我们希望包含`Hello World`页面的位置，我们简单地定义了自定义元素，并将其`view-model`属性的值设置为包含 VM 定义的文件名。

```
<template>
  <h1>Hello World</h1>
  <compose view-model="hello-world" 
           model.bind="{ demo: 'test' }"></compose>
</template>
```

如果我们需要将一些额外的数据传递给被引用的模块，我们可以使用`model`属性并将一个值绑定到它。在这种情况下，我们传递一个简单的对象，但也可以引用来自调用 VM 的属性。

现在,`HelloWorld` VM 可以定义一个 activate 方法，该方法将绑定的模型数据作为参数传递。这个方法甚至可能返回一个承诺，例如，为了从后端获取数据，这将使合成过程等待，直到它被解决。

```
export class HelloWorld {
  constructor() { }

  activate(modelData) {
    console.log(modelData); // --> { demo: 'test' }
  }
}
```

除了加载 VM，相应的`HelloWorld`视图也将被加载，其内容被放入 compose 元素。

但是假设我们不想遵循 VM 和视图对默认约定。在这种情况下，我们可以使用附加属性`view`并将其指向我们希望用作视图的 HTML 文件。

```
<compose view-model="hello-world"
         model.bind="{ demo: 'test' }"
         view="alternative-hello-world.html"></compose>
```

在这种情况下，VM 仍将被加载，但是合成引擎将把`alternative-hello-world.html`的内容插入到合成元素中，而不是加载`hello-world.html`。现在，如果我们需要动态地决定应该使用哪个视图呢？我们可以做到这一点的一个方法是将`view`属性绑定到调用 VM 的一个属性，其值将由一些逻辑决定。

```
// calling VM
export class App {
  pathToHelloWorld = "alternative-hello-world.html";
}

// calling view
<compose view-model="hello-world"
         model.bind="{ demo: 'test' }"
         view.bind="pathToHelloWorld"></compose>
```

这很好，但可能不适合每个用例。如果 HelloWorld VM 需要自己决定想要显示哪个视图，该怎么办？在这种情况下，我们简单地让它实现一个名为`getViewStrategy`的函数，该函数必须以字符串形式返回视图文件的名称。需要注意的重要一点是，这将在`activate`函数之后被调用，该函数允许我们使用传递的模型数据来确定应该显示哪个视图。

```
export class HelloWorld {
  constructor() { }

  activate(modelData) {
    this.model = modelData;
  }

  getViewStrategy() {
    if( this.model.demo === 'test' )
      return 'alternative-hello-world.html';
    else
      return 'hello-world.html';
  }
}
```

## 准备项目设置

既然我们已经看到了 compose 元素是如何发挥其魔力的，那么让我们来看看 report builder 应用程序。为了启动开发，我们在[框架导航应用](https://github.com/aurelia/skeleton-navigation)的基础上构建了它。一些部分，例如路由器，已经被剥离，因为这个应用程序只使用一个由其他子视图组成的复杂视图。首先，访问[我们的 GitHub repo](https://github.com/sitepoint-editors/aurelia-reporter) ，下载主分支并将其解压到一个文件夹，或者通过打开终端并执行以下命令在本地克隆它:

```
git clone https://github.com/sitepoint-editors/aurelia-reporter.git
```

要完成安装，请遵循项目自述文件中“运行应用程序”下面列出的步骤。

## 创建报告视图

我们应用的入口点是页面`app.html`(位于`src`文件夹中)。VM ( `app.js`)只是一个空类，预装了 Twitter 引导程序。如下面的代码片段所示，视图充当主应用程序的容器。你会注意到它由两个独立的页面组成，分别叫做`toolbox`和`report`。第一个充当各种可拖动工具的容器，而第二个是放置这些小部件的表单。

```
<template>
  <div class="page-host">
    <h1 class="non-printable">Report Builder</h1>

    <div class="row">
      <compose class="col-md-2 non-printable" view-model="toolbox"></compose>
      <compose class="col-md-10 printable" view-model="report"></compose>
    </div>
  </div>
</template>
```

查看`toolbox.html`,我们看到视图正在输出一个可用部件的列表，旁边是打印或清除报告的按钮。

```
<template>
  <h3>Toolbox</h3>
  <ul class="list-unstyled toolbox au-stagger" ref="toolboxList">
    <li repeat.for="widget of widgets" 
        class="au-animate" 
        title="${widget.type}">
          <i class="fa ${widget.icon}"/> ${widget.name}
    </li>
  </ul>
  <button click.delegate="printReport()" 
          type="button" 
          class="btn btn-primary fa fa-print"> Print</button>
  <button click.delegate="clearReport()" 
          type="button" 
          class="btn btn-warning fa fa-remove"> Clear Report</button>
</template>
```

`toolbox` VM 通过声明一个同名的属性并在其构造函数中实例化它来公开这些小部件。这是通过从它们各自的位置导入小部件并将它们的实例——由 Aurelia 的依赖注入创建——传递给`widgets`数组来实现的。此外，声明一个`EventAggregator`并将其分配给一个属性。我们稍后会谈到这一点。

```
import {inject} from 'aurelia-framework';
import {EventAggregator} from 'aurelia-event-aggregator';

import {Textblock} from './widgets/textblock';
import {Header} from './widgets/header';
import {Articles} from './widgets/articles';
import {Logo} from './widgets/logo';

@inject(EventAggregator, Textblock, Header, Articles, Logo);
export class Toolbox {

  widgets;

  constructor(evtAgg, textBlock, header, articles, logo) {
    this.widgets = [
      textBlock,
      header,
      articles,
      logo
    ];
    this.ea = evtAgg;
  }

  ...
}
```

那么这些部件包含什么呢？查看项目结构，我们可以在子文件夹`src/widgets`中找到它们。让我们从一个简单的开始:徽标小部件。这个小部件只是在其视图中显示一个图像。VM 通过实现属性`type`、`name`和`icon`遵循默认模式。我们已经在工具箱的 repeater 块中看到了这些。

```
// logo.html
<template>
  <img src="images/main-logo.png" />
</template>

// logo.js
export class Logo { 
  type = 'logo';
  name = 'Logo';
  icon = 'fa-building-o';
}
```

查看`textblock`小部件，我们看到一个额外的 activate 方法，接受来自合成引擎的初始模型数据

```
// textblock.js
export class Textblock {    
  type = 'textblock';
  name = 'Textblock';
  icon = 'fa-font';
  text = 'Lorem ipsum';

  activate(model) {
    this.text = model;
  }
}
```

为了查看该模型是如何对视图可用的，让我们看一下`report`页面。在它看来，我们看到的是同质和异质成分的混合。该报告本质上是一个无序列表，将输出添加到其中的任何小部件——这是同构部分。现在，每个小部件本身都有不同的显示和行为，构成了异构部分。compose 标签传递初始模型，以及子视图的名称'`view-model`。此外，还会绘制一个移除图标，可用于从报告表中移除一个小部件。

```
<template>
  <ul class="list-unstyled report" ref="reportSheet">
    <li repeat.for="widget of widgets" class="au-animate">
      <compose
        model.bind="widget.model"
        view-model="widgets/${widget.type}" class="col-md-11"></compose>
      <i class="remove-widget fa fa-trash-o col-md-1 non-printable"
         click.trigger="$parent.removeWidget(widget)"></i>
    </li>
  </ul>
</template>
```

移除是通过寻找相应的小部件的`id`并从`report.widget`数组中拼接来实现的。Aurelia 的 repeater 将负责更新视图，以实际删除 DOM 元素。

```
removeWidget(widget) {
  let idx = this.widgets.map( (obj, index) => {
    if( obj.id === widget.id )
      return index;
  }).reduce( (prev, current) => {
    return current || prev;
  });

  this.widgets.splice(idx, 1);
}
```

## 通过事件的组件间通信

我们已经提到工具箱有一个“清除报告”按钮，但是它是如何触发清除所有添加到`report`页面的小部件的呢？一种可能是在工具箱中包含对`report` VM 的引用，并调用它提供的方法。但是，这种机制会在这两个元素之间引入紧密耦合，因为没有报告页面，工具箱就无法使用。随着系统的增长，越来越多的部分变得相互依赖，这将最终导致过度复杂的情况。

另一种方法是使用应用程序范围的事件。如下图所示，工具箱的按钮将触发一个自定义事件，报表将订阅该事件。一旦接收到这个事件，它将执行清空小部件列表的内部任务。使用这种方法，两个部分变得松散耦合，因为事件可能由另一个实现甚至另一个组件触发。

![A screenshot of the report builder app with two speech bubbles detailing custom events](img/c36de85db4d8bc06267cbb93264a05fb.png)

*用于创建清除所有特征的事件*

为了实现这一点，我们可以使用 Aurelia 的 EventAggregator。如果您查看上面的`toolbox.js`代码片段，您可以看到`EventAggregator`已经被注入到了`toolbox`虚拟机中。我们可以在`clearReport`方法中看到它的作用，该方法简单地发布了一个名为`clearReport`的新事件。

```
clearReport() {
  this.ea.publish('clearReport');
}
```

> 请注意，我们还可以传递一个额外的数据负载，以及通过自定义类型而不是字符串来标识事件。

然后,`report` VM 在其构造函数中订阅该事件，并根据请求清除 widgets 数组。

```
import {inject} from 'aurelia-framework';
import {EventAggregator} from 'aurelia-event-aggregator';
import sortable from 'sortable';

@inject(EventAggregator)
export class Report {

  constructor(evtAgg) {
    this.ea = evtAgg;
    this.ea.subscribe('clearReport', () => {
      this.widgets = [];
    });
  }

...
```

## 通过插件使用外部代码

到目前为止，我们还没有看到实际的拖放功能，我们将使用它将小部件从工具箱拖到报告表上。当然，人们可以通过[原生 HTML5 拖放](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Drag_and_drop)来创建功能，但是为什么要重新发明轮子呢，因为已经有了一堆很好的库，比如 [Sortable](http://rubaxa.github.io/Sortable/) 来为我们工作。

因此，开发应用程序时的一个常见模式是依赖提供开箱即用功能的外部代码库。但不仅仅是第三方代码可以这样共享。通过利用 Aurelia 的插件系统，我们可以对我们自己的可重用特性做同样的事情。想法是一样的。我们没有为每个应用程序重写代码，而是创建了一个定制的 Aurelia 插件，托管所需的功能，并使用简单的助手导出它。这不仅限于纯 UI 组件，也可以用于共享业务逻辑或复杂功能，如身份验证/授权场景。

### 利用微妙的动画

在这种情况下，让我们看看 Aurelia Animator CSS T1，一个简单的 Aurelia 动画库。

Aurelia 的动画库是围绕一个简单的接口构建的，它是[模板库](https://github.com/aurelia/templating)的一部分。它充当实际实现的一种通用接口。在内置特性与 DOM 元素一起工作的特定情况下，这个接口由 Aurelia 在内部调用。例如，`repeater`用它来触发列表中新插入/删除的元素的动画。

按照选择加入的方法，为了利用动画，有必要安装一个具体的实现(比如 CSS-Animator ),它通过在样式表中声明 CSS3 动画来施展魔法。为了安装它，我们可以使用以下命令:

```
jspm install aurelia-animator-css
```

之后，最后一步是向应用程序注册插件，这是在报表构建器示例的`main.js`文件中的手动引导阶段完成的。

```
export function configure(aurelia) {
  aurelia.use
    .standardConfiguration()
    .developmentLogging()
    .plugin('aurelia-animator-css');  // <-- REGISTER THE PLUGIN

  aurelia.start().then(a => a.setRoot());
}
```

**注意:**插件本身只是另一个 Aurelia 项目，它遵循了一个`index.js`文件公开一个`configure`函数的惯例，该函数接收 Aurelia 的一个实例作为参数。`configure`方法为插件做初始化工作。例如，它可能注册定制元素、属性或值转换器等组件，以便它们可以开箱即用(就像使用`compose`定制元素一样)。一些插件接受回调作为第二个参数，这个参数可以用来在初始化后配置插件。一个例子就是 [i18n 插件](https://github.com/zewa666/aurelia-i18next)。

报告构建器在合成阶段利用微妙的动画来指示从报告中移除了一个小部件。前者在`toolbox`视图中完成。我们将类`au-stagger`添加到无序列表中，以表明每一项都应该按顺序制作动画。现在每个列表项都需要类`au-animate`，它告诉动画师我们希望这个 DOM 元素被动画化。

```
<ul class="list-unstyled toolbox au-stagger" ref="toolboxList">
  <li repeat.for="widget of widgets" 
      class="au-animate" 
      title="${widget.type}">
        <i class="fa ${widget.icon}"/> ${widget.name}
  </li>
</ul>
```

我们对`reports`视图小部件重复器做同样的事情:

```
<li repeat.for="widget of widgets" class="au-animate">
```

如前所述，CSS-Animator 将在动画阶段向元素添加特定的类。我们需要做的就是在我们的[样式表](https://github.com/sitepoint-editors/aurelia-reporter/blob/master/styles/styles.css)中声明它们。

### 添加拖放

至于包含第三方库，我们可以利用 Aurelia 的默认包管理器 JSPM。为了安装前面提到的库 Sortable.js，我们需要执行下面的命令，这个命令将在名称`sortable`下安装这个包。

```
jspm install sortable=github:rubaxa/sortable@1.2.0
```

安装后，JSPM 将自动更新文件`config.js`并添加其包映射:

```
System.config({
  "map": {
    ...
    "sortable": "github:rubaxa/sortable@1.2.0",
    ...
  }
});
```

现在这个包已经安装好了，我们可以在我们的`toolbox` VM 中使用它，首先导入它，然后在`attached`钩子中为我们的小部件列表注册拖放&特性。此时这样做很重要，因为这是视图完全生成并附加到 DOM 的时候。

```
import sortable from 'sortable';
...

export class Toolbox {
  ...
  attached() {
    new sortable(this.toolboxList, {
      sort: false,
      group: {
        name: "report",
        pull: 'clone',
        put: false
      }
    });
  }
}
```

> 你可能想知道`this.toolboxList`从何而来。看看上面动画部分中`toolbox`视图的`ref`属性。这只是为视图和 VM 之间的元素创建了一个映射。

最后一部分是在`report` VM 中接受被丢弃的元素。要做到这一点，我们可以利用 Sortable.js 的`onAdd`处理程序。因为被拖动的列表元素本身不会放在报表中，而是放在由视图组成的引用小部件中，所以我们首先必须删除它。之后，我们检查小部件的类型，如果是 textblock，我们初始化一个文本提示，它将被用作小部件的模型数据。最后，我们创建一个包装器对象，包括小部件的`id`、`type`和`model`，它将被`report`视图用来组成小部件。

```
attached() {
  new sortable(this.reportSheet, {
    group: 'report',
    onAdd: (evt) => {
      let type = evt.item.title,
          model = Math.random(),
          newPos = evt.newIndex;

      evt.item.parentElement.removeChild(evt.item);

      if(type === 'textblock') {
        model = prompt('Enter textblock content');
        if(model === undefined || model === null)
          return;
      }

      this.widgets.splice(newPos, 0, {
        id: Math.random(),
        type: type,
        model: model
      });
    }
  });
}
```

## 结论

仅此而已。我们已经看到了 Aurelia 的 compose 元素如何帮助我们创建一个复杂的视觉合成，并很好地将所有组件分成小的可重用部分。除此之外，我还演示了 Aurelia 插件的概念，在多个项目之间共享代码，以及如何使用第三方库。我们 Aurelia 团队希望您喜欢阅读这篇文章，并乐意回答任何问题，无论是在评论中还是在我们的 [Gitter 频道](https://gitter.im/aurelia)上。

## 分享这篇文章