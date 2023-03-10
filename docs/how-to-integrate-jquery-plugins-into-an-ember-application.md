# 如何将 jQuery 插件集成到 Ember 应用程序中

> 原文：<https://www.sitepoint.com/how-to-integrate-jquery-plugins-into-an-ember-application/>

*这篇文章由 [Craig Bilner](https://github.com/craigbilner) 同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

由于其无处不在，jQuery 在 web 开发领域仍然扮演着重要的角色。它的正常使用不应该是一个惊喜，尤其是当使用像 Ember 这样的框架时。这个框架有一些类似于 jQuery 插件的组件，它们都被设计成在你的项目中有一个单一的职责。

在本文中，我们将开发一个简单的 Ember 组件。本教程将展示如何将 jQuery 插件集成到 Ember 应用程序中。该组件充当插件的包装器，显示图片缩略图列表。每当我们单击一个缩略图，它的一个更大的版本就会显示在图片预览器中。这是通过提取被点击缩略图的`src`属性来实现的。然后，我们将预览器的属性设置为缩略图的属性。[这篇文章的完整代码可以在 GitHub](https://github.com/sitepoint-editors/ember-with-jquery) 上找到。

考虑到这一点，让我们开始这个项目的工作。

## 设置项目

首先，让我们创建一个新的 Ember 项目。首先，在 CLI 上执行以下命令:

```
npm install -g ember-cli
```

完成后，可以通过运行以下命令来创建项目:

```
ember new emberjquery
```

这将在名为`emberjquery`的文件夹中创建一个新项目，并安装所需的依赖项。现在，通过写入`cd emberjquery`进入目录。

该项目包含我们将在本教程中编辑的不同文件。您必须编辑的第一个文件是`bower.json`文件。打开它，将您当前的 Ember 版本更改为 2.1.0。我为这个项目创建的 jQuery 插件是一个 Bower 包。通过将这一行添加到您的`bower.json`文件中，您可以将它包含在项目中:

```
"jquerypic": "https://github.com/LaminSanneh/sitepoint-jquerypic.git#faulty"
```

现在，要安装插件和 Ember 的新版本，运行命令:

```
bower install
```

由于这个插件不是 Ember 组件，我们需要手动包含所需的文件。在`ember-cli-build.js`文件中，在 return 语句之前添加以下两行:

```
// Lines to add
  app.import("bower_components/jquerypic/js/jquerypic.js");
  app.import("bower_components/jquerypic/styles/app.css");

  return app.toTree();
};
```

这几行导入两个文件，并将它们包含在构建中。一个是插件文件本身，另一个是插件的 CSS 文件。样式表是可选的，如果您想自己设计插件的样式，可以自由地排除它。

## 创建新的插件组件

将插件包含在应用程序中后，让我们通过执行以下命令开始创建一个新组件:

```
ember generate component jquery-pic
```

这个命令创建一个类文件和一个模板文件。在模板文件中，粘贴来自`bower_components/jquerypic/index.html`文件的内容。将内容放在`body`标签中，不包括脚本。

此时，模板文件应该如下所示:

```
{{yield}}
<div class="jquerypic" >
  <div class="fullversion-container">
    <img src="https://lorempixel.com/640/480/nature/1" alt="" class="full-version" >
  </div>
  <div class="thumbnails">
    <img src="https://lorempixel.com/640/480/nature/1" alt="" class="thumbnail">
    <img src="https://lorempixel.com/640/480/nature/2" alt="" class="thumbnail">
    <img src="https://lorempixel.com/640/480/nature/3" alt="" class="thumbnail">
    <img src="https://lorempixel.com/640/480/nature/4" alt="" class="thumbnail">
    <img src="https://lorempixel.com/640/480/nature/5" alt="" class="thumbnail">
  </div>
</div>
```

在类文件中，添加一个名为`didInsertElement`的函数:

```
import Ember from 'ember';

export default Ember.Component.extend({
  didInsertElement: function () {

  }
});
```

我们现在正处于一个关键时刻。使用 jQuery，插件初始化通常发生在如下所示的`document.ready`函数中:

```
$(document).ready(function(){
  //Initialize plugin here
});
```

相反，对于 Ember 组件，这种初始化发生在一个名为`didInsertElement`的特殊函数中。当一个组件准备好并成功插入 DOM 时，调用这个函数。通过将我们的代码包装在这个函数中，我们可以保证两件事:

*   插件仅针对该组件进行初始化
*   该插件不会干扰其他组件

在初始化我们的插件之前，让我们使用当前状态的组件。为此，使用以下命令创建一个索引模板:

```
ember generate template index
```

然后将以下代码添加到模板中，以使用该组件:

```
{{jquery-pic}}
```

完成后，在 Ember 服务器上加载

```
ember serve
```

使用此命令启动服务器。打开您选择的浏览器，访问命令行界面指定的 URL。您应该会在图片预览器下面看到缩略图列表。请注意，当你点击一个缩略图，什么都不会发生。这是因为我们没有连接插件事件处理程序。我们开始吧！

但是在描述如何执行正确的初始化之前，我将向您展示许多开发人员都会犯的一个错误。这个解决方案起初看起来似乎可行，但我会通过展示它引入的一个 bug 来证明它不是最好的。

## 成员组件初始化

为了说明这个问题，让我们从向`didInsertElement`函数添加以下代码开始:

```
$(".jquerypic").jquerypic();
```

当不使用 Ember 时，这是你通常初始化插件的方式。现在，检查你的浏览器窗口，点击缩略图。你应该会看到它们被加载到大图预览器中。一切看起来都很好，对吗？好吧，看看当我们添加组件的第二个实例时会发生什么。为此，在索引模板中添加另一行代码，其中包含我之前展示的代码。因此，您的模板现在应该如下所示:

```
{{jquery-pic}}
{{jquery-pic}}
```

如果切换到浏览器窗口，应该会看到组件的两个实例出现。单击其中一个实例的缩略图时，您可以注意到这个 bug。两个实例的预览器都会改变，而不仅仅是被单击的那个。

为了解决这个问题，我们需要稍微改变一下我们的初始化式。正确的说法如下:

```
this.$(".jquerypic").jquerypic();
```

不同的是，我们现在使用的是`this.$`，而不仅仅是`$`。这两个组件实例现在应该可以正常工作了。单击一个实例的缩略图不会对另一个组件产生影响。

当我们在一个组件中使用`this.$`时，我们指的是那个组件特有的 jQuery 处理程序。因此，我们对它进行的任何 DOM 操作都只会影响组件实例。此外，任何事件处理程序都将只在该组件上设置。当我们使用全局 jQuery 属性`$`时，我们指的是整个文档。这就是为什么我们的初始初始化会影响另一个组件。

我不得不修改我的插件来演示这个错误，这可能是未来文章的主题。然而，操作组件 DOM 的最佳实践是使用`this.$`。

## 销毁插件

到目前为止，我们已经看到了如何设置事件处理程序。现在是时候展示如何移除我们用插件设置的任何事件了。当我们的组件要从 DOM 中移除时，应该这样做。我们应该这样做，因为我们不希望有任何多余的事件处理程序。幸运的是，Ember 组件提供了另一个名为`willDestroyElement`的钩子。每当 Ember 要销毁一个组件实例并将其从 DOM 中删除时，就会调用这个钩子。我的插件有一个可在插件实例上调用的`stopEvents`方法。这个方法应该在 Ember 为我们提供的特殊钩子中调用。因此，在组件中添加以下函数:

```
willDestroyElement: function () {
  this.get('jquerypic').stop();
}
```

修改`didInsertElement function`,使其看起来像这样:

```
didInsertElement: function () {
  var jquerypic = this.$(".jquerypic").jquerypic();
  this.set('jquerypic', jquerypic);
},
```

在`didInsertElement`函数中，我们只是将插件实例存储在组件的一个属性中。我们执行这个操作，以便可以在其他函数中访问它。在`willDestroyElement`函数中，我们调用插件实例上的`stopEvents`方法。虽然这是一个好的实践，但是我们的应用程序没有办法触发这个钩子。因此，我们将设置一个演示点击处理程序。在这个处理程序中，我们将调用插件实例上的`stopEvents`方法。这允许我显示所有的事件处理程序都像我们预期的那样被删除了。

现在，让我们向组件添加一个点击函数处理程序:

```
actions: {
  stopEvents: function () {
    this.get('jquerypic').stop();
  }
}
```

然后向组件模板添加一个段落标记，如下所示:

```
<p {{action "stopEvents"}} >
  Stop Events
</p>
```

当这个标签被点击时，它调用`stopEvents`动作来销毁插件。点击段落后，插件应该不再响应点击事件。为了再次启用事件，你必须像我们在`didInsert`钩子中所做的那样初始化插件。

通过这最后一部分，我们已经完成了简单的 Ember 组件。恭喜你！

## 结论

在本教程中，你已经看到 jQuery 插件仍然在我们的职业生涯中扮演着重要的角色。有了强大的 API 和可用的 JavaScript 框架，知道如何将这两个世界结合在一起并使它们和谐工作是非常有用的。

在我们的例子中，组件充当插件的包装器，显示图片缩略图列表。每当我们单击一个缩略图，它的一个更大的版本就会显示在图片预览器中。这只是一个例子，但是你可以集成任何你想要的 jQuery 插件。

我想再次提醒你，代码可以在 GitHub 上找到。

你在 Ember 应用中使用 jQuery 插件吗？如果你想讨论它们，请在下面的部分随意评论。

## 分享这篇文章