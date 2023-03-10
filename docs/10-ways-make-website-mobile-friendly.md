# 10 种方法让你的网站对手机更友好

> 原文：<https://www.sitepoint.com/10-ways-make-website-mobile-friendly/>

当通过智能手机或平板手机访问时，你的网站是否提供了优化的移动体验？如果没有，也不是世界末日，因为现代浏览器通过创新，如缩放和自动字体大小调整，使这种体验变得更容易忍受。如果你没有时间或金钱让你的网站移动友好，这里有 10 件简单的事情你可以今天做*让你的网站对移动用户来说更容易忍受。*

 *## 1.设置表单输入属性

如果您的网站使用输入栏来询问用户的姓名或地址，则关闭`autocorrect`并打开`autocapitalize`。

```
What's your name: <input type=text size=20 autocorrect=off autocapitalize=words>
```

如果你不这样做，手机会把他们的名字(如“尔万”)改成别的名字(如“埃里温”)，如果在字典里找不到的话。将自动大写设置为`words`将使他们不必为每个连续的名字切换大写键(例如，“肯·伯恩斯”变成“本·伯恩斯”)。

![autocorrect checkbox](img/67fdca568eea134042cba8b6b9513581.png)

想想你的用户会省去多少麻烦。

当你这样做的时候，如果你的网站要求用户的电子邮件，那么使用`email`特定的输入域，这样用户就可以使用`@`键，而不需要切换到数字/符号键盘。

```
What's your email: <input type=email size=20>
```

## 2.设置移动友好的首选宽度

在桌面浏览器中加载您的网站，将窗口调整到最窄的宽度，同时保持网站的可读性。这是您的最小观看尺寸。现在，通过将这个`meta`标签添加到页面的`head`中，获得窗口的大小，并将其设置为您网站的首选视窗宽度。

```
<meta name=viewport content='width=700'>
```

下次在移动设备上查看您的网站时，它会自动以这个大小显示您的网站，并删除网站左右两侧的所有多余空间，因此用户在第一次访问时无需缩小或放大。

![Device width example](img/62efb504fbae7c6a86270566b294effe.png)

这个网站右边有很多浪费的空间。

![Device width example](img/bb8ea205dc3e0a55b64352555a369cd4.png)

这个网站缩放得恰到好处。

如果你的网站已经建立在流体百分比的基础上，并且适用于所有的屏幕尺寸，你的工作就更容易了。只需尝试一种宽度，使移动设备上的视图舒适易读，并在`meta`标签中使用该宽度。

## 3.将图像宽度设置为 100%

现在你的网站设置了首选宽度，有些图片自然会太宽。这在以前是不会发生的，因为桌面屏幕很宽，大多数图像都可以在屏幕上显示。

![Image width example](img/e09d5fa59ec6aaaa664fbea87c71a881.png)

为了解决这个问题，给你的图片设置一个 100%的最大宽度，这样当它们对于移动设备来说太大的时候会自动调整大小。将此添加到您网站的 CSS 样式表中。

```
img {
  max-width: 100%
}
```

如果你的图片被设置为背景图片而不是标签，只需将`background-size` CSS 属性设置为`contain`。这将导致背景图像在屏幕过小时调整大小。

```
.header {
  background: url(header.png) 50% no-repeat;
  background-size: contain
}
```

等一下，缩小了图像不会失去清晰度吗？如果你用的是现代移动设备，就不会了。当用户放大一张图片时，浏览器会随着你的放大而恢复清晰。但是，请确保您的网站没有在`meta`标签中设置`user-scalable=no`属性。如果是这样，用户将无法放大和缩小。

```
<!-- DON'T DO THIS! -->
<meta name=viewport content='user-scalable=no'>
```

## 4.将输入宽度设置为 100%

在给你的图像一个`max-width`之后，在`input`域执行一个类似的技巧。只需将其添加到您网站的 CSS 样式表中。

```
input, textarea {
  max-width:100%
}
```

当在设置了首选宽度的移动设备上查看您的网站时，`input`字段不会超出屏幕边缘。

## 5.禁用提交按钮时要小心

您的网站是否在第一次点击后禁用表单`submit`按钮，以防止多次提交？如果是，就不要做(除非绝对必要)！

与台式电脑不同，移动设备经常会遇到网络中断。如果禁用该按钮，用户将无法再次单击以重新提交。我说的不仅仅是由于信号差或信号塔切换造成的网络中断。如果用户在提交表单时接到电话，移动浏览器将关闭以显示呼叫者屏幕，当浏览器重新打开时，可能无法从中断中恢复。

如果必须禁用`submit`按钮，只需禁用几秒钟。

## 6.对长字符串使用`word-wrap`

有时有必要显示长字符串，如参考代码、银行账号，甚至 URL。如果您的网站太窄，无法在移动设备上显示完整的数字，它可能会超出屏幕边缘。

![Text Overflow example](img/2a2a6146c7596bd495c0e1a9d62da28d.png)

要解决这个问题，只需用`word-wrap`样式包装任何长字符串。现在，当文本到达边缘时，它会换到下一行，这样用户就可以看到整个文本，而不需要四处滚动。

```
Your passcode is:
<span style='word-wrap:break-word'>435143a1b5fc8bb70a3aa9b10f6673a8</span>
```

## 7.小心使用多余的空格

当向用户显示长串数字时，一种常见的技术是将它们分成 5 个字母组，在它们之间留有空格(例如，43514 3a1b5)，以便用户可以一次记住 5 个数字，同时在另一个应用程序中键入它们。对于聪明的用户来说，他们简单地复制并粘贴整个字符串，然后删除空格。

在台式电脑上，删除空白处是一件小事。但是在移动设备上，这要耗费更多的时间。要解决这个问题，不要在五个字母组之间显示空格，只需将五个字母组放在一个元素中，中间用一些填充。

```
<style> .split m {
  padding: 0em 0.5em
} </style>

Your passcode is:
<span class='split'><m>43514</m><m>3a1b5</m><m>fc8bb</m></span>
```

现在各组之间会有一个空格，但是当复制和粘贴它们时，空格不会出现。这是为你的用户节省时间。

## 8.利用媒体的询问

当所有这些都失败时，你不希望不得不调整你的网站，让它们变小，以便它们在移动设备上看起来更好，只是让它们在你的台式电脑上看起来太小。这就是媒体提问的地方。

您可以创建仅在移动设备上查看(或在小浏览器窗口中查看)时生效而在桌面浏览器上不生效的自定义样式规则。简单地在媒体查询中添加目标样式，如下所示。

```
<style>
/* regular css */
.tabs {
  padding: 10px 2em
}

@media screen and (max-width: 500px) {
/* applies only if the screen is narrower than 500px */
  .tabs {
    padding: 3px 1em
  }
}
</style>
```

现在，你可以做一些小的调整，让你的网站在桌面和移动设备上看起来都不错。

## 9.避免`fixed`定位

如果你的网站有一个固定的标题或侧边栏，其 CSS `position`属性设置为`fixed`，请注意，当用户放大你的网站时，你的标题也将放大，并可能遮蔽整个屏幕。

![Zoom example](img/a9ca1b92f9b3f435488971fbf2970843.png)

最简单的解决方法是在移动设备上查看您的网站时禁用固定位置。您可以使用上一篇技巧文章中的媒体查询方法做到这一点。

```
<style>
/* regular css */
#header {
  position: fixed
}

@media screen and (max-width: 500px) {
/* applies only if the screen is narrower than 500px */
  #header {
    position: static
  }
}
</style>
```

## 10.使用标准字体

使用自定义字体使您的网站具有专业设计的外观，但用户需要下载大字体文件才能查看您的网站。在移动设备上，下载可能需要几秒钟的时间，在此期间，用户会看到一个应该显示文本的空白处。

![Web fonts example](img/f88a376acd643dcaf34c48aea0fefba2.png)

如果您使用 Google 字体加载器来加载您的字体，您可以选择首先以默认字体显示文本，然后在下载字体后以新字体重新呈现页面。为此，您需要在引用自定义字体的任何地方编写两套 CSS 规则。一组规则使用默认字体，另一组规则在字体下载后激活。这样，用户可以两全其美。他们可以在等待下载时阅读文本，并在下载后欣赏您的自定义字体。

```
<script src='//ajax.googleapis.com/ajax/libs/webfont/1.4.7/webfont.js'></script>
<script>
WebFont.load({
  google: {
    families: ['Open Sans']
  }
});
</script>
<style type='text/css'>
.header {
  font-family: Arial
}
.wf-opensans-n4-active .header {
  font-family: 'Open Sans'
}
</style>
```

注意字体加载完成后，字体加载器动态添加到网站的`.wf-opensans-n4-active`类选择器。

## 结论

这篇文章中描述的十件事是你可以对现有网站做的小改变。它们将意味着用户在尝试使用小屏幕和键盘与你的网站互动时，不需要沮丧地拔头发。我鼓励你今天就在你的网站上实现它们！

## 分享这篇文章*