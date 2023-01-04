# 使用 sweetAlert 创建精美的提醒

> 原文：<https://www.sitepoint.com/creating-nice-alerts-sweetalert/>

当构建 JavaScript 驱动的网站时，我们经常需要向用户提供反馈，让他们知道他们执行的操作是否成功。在 web 的早期，开发人员曾经使用`window.alert()`函数创建消息。虽然`alert()`在实践中可以工作，并且在不同的浏览器中也是一致的，但是它不是很灵活，而且，说实话，它的外观和感觉很糟糕。今天有几种方法被采用，从模态到内联消息。在本文中，我将向您介绍 [sweetAlert](http://tristanedwards.me/sweetalert) ，这是一个替代 JavaScript 的`alert()`函数的库。

## 什么是 sweetAlert？

正如简介中提到的 [sweetAlert](http://tristanedwards.me/sweetalert) 是 JavaScript 的`window.alert()`函数的替代品，可以显示非常漂亮的模态窗口。这是一个没有依赖关系的独立库，由一个 JavaScript 文件和一个 CSS 文件组成。

这个库有三种不同的风格。第一个是你可以在任何 web 项目中使用的库，[第二个](https://github.com/lipis/bootstrap-sweetalert)是专门适用于 Bootstrap 的一个分支，[第三个](https://github.com/pedant/sweet-alert-dialog)是你可以在你的 Android 项目中使用的一个分支。这个项目一直在进行中，你可以从几天前发布的上一个版本中看到这一点。

现在你知道这个库是关于什么的了，让我们看看你如何在你的网站中使用它。

## 开始使用 sweetAlert

要在您的项目中使用 sweetAlert，您必须下载它，并将其包含在您打算使用该库的页面中。有几个选项可以下载这个库:第一个是通过访问 GitHub 库，第二个是通过 Bower。如果您选择使用 Bower，您必须运行命令:

```
bower install sweetalert
```

下载后，您可以像对任何其他 JavaScript 库一样，将 JavaScript 文件与常用的`script`元素包含在一起:

```
<script src="path/to/sweet-alert.min.js"></script>
```

此外，您必须包括如下所示的 CSS 文件:

```
<link rel="stylesheet" href="path/to/sweet-alert.css" />
```

完成后，您就可以在您的网站中使用 sweetAlert 了。下面是一个简单的使用示例:

```
sweetAlert('Congratulations!', 'Your message has been successfully sent', 'success');
```

产生以下输出:

[https://jsfiddle.net/9k6o13qw/embedded/](https://jsfiddle.net/9k6o13qw/embedded/)

我不知道你在想什么，但是我喜欢这个小动画！

正如您在前面的语句中看到的，这个库通过一个叫做`sweetAlert`的方法运行。它最多接受三个参数:

*   `title`(必填):表示所显示警报标题的字符串
*   `message`(可选):可选字符串，表示显示在标题下的消息
*   `type`(可选):表示要显示的消息类型的可选字符串。其值可以是`"success"`、`"error"`、`"warning"`、`"info"`中的一个。

该库还提供了一个调用名为`swal`的`sweetAlert`方法的快捷方式。因此，前面的语句可以重写如下:

```
swal('Congratulations!', 'Your message has been succesfully sent', 'success');
```

除了提供的参数之外，该库还提供了一整套选项，您可以通过作为方法的第一个参数传递的对象来设置这些选项。例如，前面的语句可以重写为:

```
swal({
   title: 'Congratulations!',
   text: 'Your message has been succesfully sent',
   type: 'success'
});
```

现在您已经了解了这个库的基本知识，让我们学习更多关于它的选项。

## 选择

我想介绍的第一个选项允许您更改显示的按钮的文本。例如，如果您想将成功消息的按钮文本从“OK”更改为“Yeah！”，您可以为名为`confirmButtonText`的选项设置值。如果您想更改取消按钮的文本，您必须设置`cancelButtonText`的值。这时，你们中观察力最敏锐的人应该举手说:“到目前为止，我还没有看到任何取消按钮，你在说什么？”如果你做到了，那太好了！

事实是，sweetAlert 允许您显示取消消息，但您必须明确指定您想要它。您可以通过将选项`showCancelButton`设置为`true`来实现。

以下代码利用了这三个选项:

```
swal({
   title: 'Confirm',
   text: 'Are you sure to delete this message?',
   type: 'warning',
   showCancelButton: true,
   confirmButtonText: 'Yes, sir',
   cancelButtonText: 'Not at all'
});
```

该代码会导致以下结果:

[https://jsfiddle.net/vy5osy7r/embedded/](https://jsfiddle.net/vy5osy7r/embedded/)

如果你不喜欢确认按钮的颜色，你也可以通过为`confirmButtonColor`选项设置一个哈希值来改变它。

另一个有趣的选项是，您可以设置消息显示固定的时间，然后自动关闭。您可以通过向一个名为`timer`的选项传递一个数字来完成这个任务，这个数字代表消息关闭之前的毫秒数。

下面的代码使用了另外两个选项:

```
swal({
   title: 'Confirm',
   text: 'Are you sure to delete this message?',
   type: 'warning',
   showCancelButton: true,
   confirmButtonColor: '#987463',
   timer: 1500
});
```

它会导致以下结果:

[https://jsfiddle.net/rpdzf7eq/embedded/](https://jsfiddle.net/rpdzf7eq/embedded/)

## 结论

在本文中，我介绍了 sweetAlert，这是一个旨在替代 JavaScript 的`window.alert()`函数的库，它允许您向用户显示非常好的消息。这个库可以在任何类型的设备上运行，所以你也可以在你的响应网站上使用它。我希望你和我一样喜欢这个图书馆，并且在你的一些项目中给它一个机会。

## 分享这篇文章