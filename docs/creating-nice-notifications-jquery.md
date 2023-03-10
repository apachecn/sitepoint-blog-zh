# 用 jQuery 创建漂亮的通知

> 原文：<https://www.sitepoint.com/creating-nice-notifications-jquery/>

开发动态网站时，通知是最常用的功能之一。无论您的应用程序是在网页中插入 HTML 片段，还是发送用户填写的表单数据，您的应用程序都需要向用户提供一些反馈。有许多不同的技术可以用来向用户提供反馈，比如警告消息和对话框。

在本文中，我将解释如何通过使用一个名为 [noty](http://ned.im/noty/) 的 jQuery 插件，在网页中集成漂亮的通知。

## 什么不好？

noty 是一个 jQuery 插件，可以很容易地创建多种类型的反馈，如警告、成功或失败消息以及确认请求。我选择讨论这个插件并不是随机的。事实上，noty 是你可以在 [jQuery 插件注册表](http://plugins.jquery.com/tag/jquery/)上找到的最受关注和明星的插件之一。noty 最好的特性之一是，由于它提供了许多选项，它是高度可定制的，其中一些将在本文中讨论。另一个有趣的特性是，它允许您对通知进行排队，这样您就不必等到之前显示的通知的生命周期结束。但是，如果您不需要此功能，您可以禁用它。

## 开始使用 noty

就像许多 jQuery 插件一样，开始使用 noty 非常容易。第一步是下载插件，并将其包含在 jQuery 库之后的页面中。要下载 noty，您可以访问[的 GitHub 库](https://github.com/needim/noty)并下载可用的最新版本，或者键入以下 Bower 命令:

```
bower install noty
```

下载完插件后，您可以使用以下代码将它包含在您的页面中:

```
<script src="//code.jquery.com/jquery-1.11.1.min.js"></script>
<script src="path/to/noty/jquery.noty.packaged.min.js"></script>
```

您现在可以用 noty 创建通知了。使用这个插件的一个最小的例子如下所示:

```
<script>
   noty({ text: 'My first notification using noty'});
</script>
```

上述语句将导致创建一个新的通知。结果显示在下面的演示中，也可以作为 JSfiddle 使用[:](https://jsfiddle.net/q5cy5owr/embedded/result/)

[https://jsfiddle.net/q5cy5owr/embedded/](https://jsfiddle.net/q5cy5owr/embedded/)

## 选择

noty 提供了许多选项来配置单个通知，包括一个`default`属性来更改这些选项的默认值。使用`default`对象，您可以设置页面通知共享的值，这样您就不必一遍又一遍地编写它们。该插件还提供了各种钩子来在给定动作执行后执行回调。例如，您可以在显示或关闭通知后运行特定功能。在这一部分，我不会讨论所有的选项，但我会提到我认为最重要的选项。

我要提到的第一个选项是`type`，它定义了显示的通知类型。其默认值为`"alert"`，但您也可以设置`"success"`、`"error"`、`"warning"`、`"information"`和`"confirm"`。

我要介绍的第二个属性是`maxVisible`。默认情况下，该插件最多显示五个通知，但您可以根据需要更改该值。

当我们谈论多个通知时，有时您可能需要强制一个新的通知来替换一个或多个仍然可见的旧通知。在这种情况下，您可以将`killer`属性设置为`false`，其默认值为`true`。

另一个值得一提的选项是设置通知位置的`layout`。

我想强调的最后一个选项是`closeWith`。它指定如何关闭通知，并接受一组值。默认情况下，单击鼠标关闭通知，但是您可以使用其他值，如`"button"`和`"hover"`。

除了描述的属性，插件还有很多其他选项来定制动画、速度、按钮等等。

## 把所有的放在一起

上一节描述了 noty 中的一些可用选项。在这里，我们将把它们付诸行动，看看会发生什么。出于示例目的，我们将创建一个通知:

*   有“开心！”作为它的文本
*   必须显示在页面的中央
*   可以通过点击通知并悬停在其上来关闭
*   必须是成功的通知

此外，我们还希望每个新通知强制关闭其他通知。实现这一目标的代码如下所示:

```
$.noty.defaults.killer = true;

noty({
   text: 'Happy!',
   layout: 'center',
   closeWith: ['click', 'hover'],
   type: 'success'
});
```

下面展示了这段代码的一个现场演示，但也可以作为 JSfiddle 使用。

[https://jsfiddle.net/nhfek0cb/embedded/](https://jsfiddle.net/nhfek0cb/embedded/)

## 结论

在本文中，我们讨论了 noty，这是一个高度可定制的 jQuery 插件，它使我们能够轻松地创建通知。我希望你喜欢它，并发现它很有用。

你听说过这个插件吗？如果是的话，你曾经在你的项目中使用过吗？和我们分享你的经验吧！

## 分享这篇文章