# 向 Shepherd 介绍您的应用

> 原文：<https://www.sitepoint.com/introducing-application-shepherd/>

作为一名 web 开发人员，您可能意识到创建一个应用程序通常是容易的部分——向世界展示它本身就是一项艰巨的任务。有些人喜欢制作演示文稿，有些人喜欢制作视频。如果你有一些东西来帮助你的用户浏览你的应用程序，那不是很好吗？

进入谢博德中心。Shepherd 是一个简单的 JavaScript 库，它可以帮助您引导用户浏览您的应用程序。它帮助你指引你的用户到正确的地方，就像一个牧羊人照顾他的羊群一样。

也有其他库可以实现这一目的，但是我更喜欢 Shepherd 的原因是它没有任何依赖性。它还支持 CoffeeScript，尽管我们在这里只探索 JavaScript。

## 入门指南

Shepherd 是开源的，它的代码可以在 [GitHub](https://github.com/HubSpot/shepherd) 上找到。牧羊人的演示也可以在 Hubspot 上找到[。让我们开始吧。](http://github.hubspot.com/shepherd/docs/welcome/)

对于没有耐心的人，这里是入门的基本代码。这将创建一个一步到位的应用程序之旅。这将对话框绑定到选择器`#id_selector`匹配的元素的底部。

```
var tour = new Shepherd.Tour({
  defaults: {
    classes: 'shepherd-theme-arrows',
    scrollTo: true
  }
});

tour.addStep('myStep', {
  title: 'Hi there!',
  text: 'This would help you get started with this application!',
  attachTo: '#id_selector bottom',
  classes: 'shepherd shepherd-open shepherd-theme-arrows shepherd-transparent-text',
  buttons: [
    {
      text: 'Exit',
      classes: 'shepherd-button-secondary',
      action: function() {
        return tour.hide();
      }
    }
  ]
});
```

## 打败谢博德

现在您已经运行了简单的代码，让我们将这些步骤分成我们可以理解的部分。

### 包含

您需要包含单个 Shepherd JavaScript 文件。Shepherd 还带有一个默认主题，包含在一个 CSS 文件中。

```
<link type="text/css" rel="stylesheet" href="css/shepherd-theme-arrows.css" />
<script type="text/javascript" src="./shepherd.min.js"></script>
```

### 初始化 Shepherd

以下代码示例显示了如何通过 JavaScript 创建旅程。由于您将很快在您的旅程中添加步骤，初始化中的`defaults`选项会将这些选项添加到您的所有步骤中，除非您覆盖它们:

```
tour = new Shepherd.Tour({
  defaults: {
    classes: 'shepherd-theme-arrows',
    scrollTo: true
  }
});
```

### 创建步骤

让我们再次检查“入门”代码。下面是启动本教程的一个步骤的代码:

```
tour.addStep('myStep', {
  title: 'Hi there!',
  text: 'This would help you get started with this application!',
  attachTo: '#id_selector bottom',
  classes: 'shepherd shepherd-open shepherd-theme-arrows shepherd-transparent-text',
  buttons: [
    {
      text: 'Exit',
      classes: 'shepherd-button-secondary',
      action: function() {
        return tour.hide();
      }
    }
  ]
});
```

如果您计划有多个步骤，可以附加一个附加按钮。如果有两个步骤，下面是如何使用按钮的示例:

```
tour.addStep('step1', {
  ...
  buttons: [
    {
      text: 'Exit',
      classes: 'shepherd-button-secondary',
      action: function() {
        return tour.hide();
      }
    }, {
      text: 'Next',
      action: tour.next,
      classes: 'shepherd-button-example-primary'
    }
  ]
});

tour.addStep('step2', {
  ...
  buttons: [
    {
      text: 'Back',
      action: tour.back,
      classes: 'shepherd-button-example-primary'
    }, {
      text: 'Exit',
      classes: 'shepherd-button-secondary',
      action: function() {
        return tour.hide();
      }
    } 
  ]
});
```

### 开始参观

设置好旅程后，剩下的就是启动它了！

```
tour.start();
```

## API

Shepherd 提供了广泛的 API，以及解释其行为的[文档](http://github.hubspot.com/shepherd/)。在这里，我们将通过一些有用的调用。

### 旅游实例

首先，创建如下所示的旅程。

```
myTour = new Shepherd.Tour({ options })
```

现在，我们将看看如何处理这个实例。`steps`和`defaults`是旅游实例的选项。其方法如下所述。

*   `addStep(id, options)`–正如我们在上面看到的，一个步骤是通过给它分配一个 ID 来创建的，然后添加选项，如`text`或`buttons`，这将在后面描述。
*   `getById(id)`–该方法用于通过 ID 选择任何特定的步骤。
*   `show(id)`–通过 ID 显示特定步骤。
*   `on(event, handler)`–将活动绑定到您的旅程。这类似于 [jQuery 的`bind()`方法](https://api.jquery.com/bind/)。
*   `off(event, handler)`–解除事件绑定。

一个旅游实例也有类似于`start`、`complete`、`show`和`hide`的事件。

### 步伐

虽然我们之前已经添加了步骤，但还是让我们仔细看看。下表描述了您可以定义的选项。

*   你可以申请也可以不申请头衔。
*   `text`–在步骤中显示的文本。
*   `attachTo`–这有两个部分:步骤要附加到的元素的选择器，以及步骤要附加到的位置(即`#id_selector bottom`)。
*   添加到你的对话框中的额外的类。这取决于你使用的主题。
*   `buttons`–要显示的按钮列表。每个按钮都有一个`text`，一个额外的`classes`被添加到其中，一个`action`在点击按钮时被执行。

有各种各样的方法可以让你的任务变得更容易。以下是一些有用的方法:

*   `show()`–显示一个步骤。
*   `hide()`–隐藏一步。
*   `cancel()`–隐藏步骤并取消旅程。
*   `complete()`–隐藏步骤并完成旅程。
*   `destroy()`–破坏一个步骤。
*   `on(event, handler)`–绑定事件。
*   `on(event, handler)`–解除事件绑定。

## 结论

虽然 Shepherd 看起来很有前途，但我注意到的一个小问题是 IE 9+的浏览器支持。但是如果你不打算支持老浏览器，那就试试吧。

你可以在 [GitHub](http://sdaityari.github.io/shepherd-demo/) 上找到基于本文代码的现场演示。演示可以进一步修改。您可以尝试将箭头键的事件处理程序绑定到 Shepherd 导航。您还可以创建 CSS 类，并将它们附加到不同的元素上，以便将焦点从一个元素转移到另一个元素。

## 分享这篇文章