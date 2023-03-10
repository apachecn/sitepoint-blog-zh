# 使用 CSS 样式化 Web 窗体

> 原文：<https://www.sitepoint.com/style-web-forms-css/>

作为网站设计者，我们工作的很大一部分是让东西看起来漂亮，功能良好。我们花了几个小时的时间来使我们网站的每个方面在视觉上引人注目，直观，用户友好，可访问和整体美观。我们的表单也不例外！我们的形式应该是美丽的，易于使用，并应与我们网站的其余部分保持一致。我们可以用 CSS 轻松做到这一点。

这个过程并不困难，你只需要知道每个标签做什么，以及如何设计它的样式。我们需要做的第一件事是引入我们的 HTML。下面是我们的示例表单中的 HTML。

```
<form>
  <div>
    <h1>Contact Form :</h1>
    <label>
      <span>Your name</span><input id="name" type="text" name="name" />
    </label>

    <label>
      <span>Email Address</span><input id="email" type="text" name="email" />
    </label>

    <label>
      <span>Subject</span><input id="subject" type="text" name="subject" />
    </label>

    <label>
      <span>Message</span><textarea id="feedback" name="feedback"></textarea>
      <input type="button" value="Submit Form" />
    </label>

  </div>
</form>
```

您会注意到，在 HTML 中，我使用了有意义的单词、名称和 id。它们与您期望的每个字段的名称一致。每个字段都被包装在一个标签中，以便于我们设计样式。我们的表单看起来非常简单，没有任何样式，正如您从下面的示例中看到的:

![](img/2369a3695071c180cdc4514bbe80c353.png)

我们的形体没有结构，没有颜色，也没有个性。我们可以用一点代码来改变它。首先，我们将设计表单标签本身的样式。

```
form {
  background: -webkit-linear-gradient(bottom, #CCCCCC, #EEEEEE 175px);
  background: -moz-linear-gradient(bottom, #CCCCCC, #EEEEEE 175px);
  background: linear-gradient(bottom, #CCCCCC, #EEEEEE 175px);
  margin: auto;
  position: relative;
  width: 550px;
  height: 450px;
  font-family: Tahoma, Geneva, sans-serif;
  font-size: 14px;
  font-style: italic;
  line-height: 24px;
  font-weight: bold;
  color: #09C;
  text-decoration: none;
  border-radius: 10px;
  padding: 10px;
  border: 1px solid #999;
  border: inset 1px solid #333;
  -webkit-box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.3);
  -moz-box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.3);
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.3);
}
```

上面的代码看起来很长，但是分解起来相当简单。单调的颜色可能会很无聊，所以添加一点渐变可以打破单调，给你的设计增加一些维度。这是用背景样式完成的。当使用这个属性和渐变时，你必须包含某些浏览器的特定前缀，比如 Firefox，否则它们不会显示出来。两者都在说同样的事情。创建一个线性渐变，从底部开始，使用一个中灰色和一个浅灰色，混合超过 175 像素。

因为这是包含整个表单的地方，所以我决定将 margin 设置为 auto，使表单在浏览器中居中。将 Position 设置为 Relative 是为了稍后对齐一个元素，这将在后面解释。我指定了表单的宽度和高度，使用的字体，并将其样式化为粗体、斜体，大小为 14px，行高(每行文本之间的间距)为 24px。

边框半径为我们的盒子提供了圆角。增加圆角的数量。填充在文本和窗体边缘之间提供了一些空间，这样文本就不会超出窗体及其圆角的边界。

你可以为对比和尺寸创造微妙的边界。我还在整个表单中添加了框阴影，所以如果这成为一个弹出式表单，它将增加维度，使表单看起来像是漂浮在网站的其余部分上。这是目前流行的技术。这是另一种样式，需要您指定适当的前缀才能显示出来。您的表单应该是这样的:

![](img/fae9de67aeacad928b5f6bbda7aee382.png)

接下来，我们应该设计输入区域的样式。这是文本实际输入到每个字段的位置。

```
input {
  width: 375px;
  display: block;
  border: 1px solid #999;
  height: 25px;
  -webkit-box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.3);
  -moz-box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.3);
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.3);
}
```

上面显示的代码选择了所有的文本输入区域，并将它们的宽度设置为 375px，并将显示设置为垂直堆叠。添加 1px 边框有助于强调每个输入区域，将高度设置为 25px 可以为用户输入文本提供足够的视觉空间。

我为 dimension 添加了一个方框阴影，但是记住要为每个浏览器添加前缀。前两个数字控制阴影的偏移。正数将阴影向右上方推，负数将阴影向左下方推。第三个数字决定了阴影模糊的程度。数字越大，模糊越大。在括号内，前三个数字决定阴影的红色、绿色和蓝色值，十进制数字决定阴影本身的不透明度。1 表示 100%不透明度，0.1 表示 10%不透明度。添加这些样式后，您的表单应该开始成形，看起来像下图:

![](img/4d2a33863c14cc89b8e062f2c70618c2.png)

一切都对齐了，但是注意提交按钮受到了宽度样式的影响。我们稍后会解决这个问题。消息区看起来不太对，但是我们可以很容易地解决这个问题。

```
textarea#feedback {
  width: 375px;
  height: 150px;
}
```

您可以直接指定宽度和高度，但这仍然不能使 textarea 与其他字段保持一致。

![](img/91d6b6e5e8a4fbdb7d5987c4baeda473.png)

我们必须手动将`display`属性设置为`block`，这样它就可以像输入区域一样工作。

```
textarea#feedback {
  width: 375px;
  height: 150px;
  display: block;
}
```

![](img/50e18cd99dc79c376066c0e6f2c13b55.png)

现在一切都已正确对齐，我们可以开始修复提交按钮了。我们需要解决这个问题的 CSS 相当简单:

```
button {
  width: 100px;
  position: absolute;
  right: 20px;
  bottom: 20px;
  background: #09C;
  color: #fff;
  font-family: Tahoma, Geneva, sans-serif;
  height: 30px;
  border-radius: 15px;
  border: 1p solid #999;
}

input.button:hover {
  background: #fff;
  color: #09C;
}
```

我们选择名为 submit 的按钮，将其宽度定义为 100 像素，将其位置设置为 absolute。正如我们前面提到的，我们已经设计了具有相对位置的表单。其工作方式是，当你设置一个绝对位置时，它寻找最后一个位置设置为相对的元素。如果该元素嵌套在具有相对位置的元素内，则其绝对位置相对于该元素。换句话说，提交按钮将被放置在表单容器边界内的某个地方。我定义了从右到下分别为 20px 的样式。

我设置背景为蓝色，文字为白色。我给它定了 30px 的高度，圆角。我也有一个 1px 的灰色边框。这是提交按钮的正常状态。

您会注意到我为提交按钮定义了一个悬停状态。一旦用户将鼠标悬停在按钮上，这里定义的样式将覆盖原来的样式。我将背景改为白色，文本改为蓝色，当用户将鼠标放在按钮上时，给用户一种强烈的对比效果。

这是正常状态:

![](img/55fa3dec5db219a6de62f728b45f979b.png)

这是悬停状态:

![](img/6c88289d6c907116b16cfb4d14892dc9.png)

我们的表单结构已经完成。你可以在这里停下来，你会有一个伟大的形式，所有的 CSS 样式。但是，您可以更进一步，在文本输入区域添加一些用户友好的样式，这样用户就可以知道他们在哪里输入了。您可以使用少量的 CSS 来实现这一点:

```
textarea:focus, input:focus {
  border: 1px solid #09C;
}
```

它告诉浏览器，如果一个人选择了文本输入或文本区域，它需要在活动输入区域周围添加一个 1px 的蓝色边框，这样用户就可以直观地知道他们在表单中的位置。这只是一点额外的东西，受到许多用户的赞赏。

![](img/a1a42c102749fe164e634195cab493f0.png)

## 结论

你可以用 CSS 做一些奇妙的事情，包括为你的用户创建漂亮的 web 表单。只需一点 CSS 和一些想象力，您就可以让像填写表单这样枯燥的事情变得更加有趣，让任何访问您的站点的人都更加愉快。

下面你会发现一个 CodePen 演示，它包含了我们在本帖中构建的示例:

在[码笔](http://codepen.io)上看到 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[729 CEB 6 e 733 cbb 086 a 414 ed 7 a 4d 381 e 4](http://codepen.io/SitePoint/pen/729ceb6e733ccb086a414ed7a4d381e4/)。