# 使用 Sass 转换印刷单位

> 原文：<https://www.sitepoint.com/converting-typographic-units-sass/>

![Typography Units Conversion with Sass](img/b2203ade8ef2a2efe3571ca0fb3e9731.png)

*这是 2015 年 3 月 5 日首次发表的一篇文章的更新版本。*

很久以前，在互联网历史的迷雾中，勇敢的冒险家们以大致相同的方式应对危险:抛弃固定的 960 像素布局，在固定的网格中战斗，并以像素为单位发射字体以抵御邪恶。

响应式网页设计已经改变了这一切，它将我们从迷雾中带进了一个启蒙时代。然而，在网络上使用排版有时仍然是一件痛苦的事情。随着从像素或点到百分比和 ems 的推进，我发现自己不得不在每个项目开始时，甚至在整个过程中不断地谷歌“像素到百分比转换图”或类似的东西。

在沮丧中，我最终求助于 [Sass](http://sass-lang.com/) 的力量来打造一个新的、无所不包的函数，以寻求克服这些问题，今天你将和我一起构建这个函数。

它将允许你在像素、em 和百分比值之间自由转换，而不必每次都查阅图表，并且有希望减轻你在这个过程中的许多头痛。

## 在 Sass 中设置印刷单位转换

首先，在 CSS 中定义一个默认的`font-size`是非常重要的。大多数浏览器默认使用`16px`，但是如果你的项目需要不同的东西，确保你的 CSS 知道它。此外，大多数样板文件都将`16px`定义为它们的默认值，因此我将假设这也是本教程的默认值。

然后你需要决定支持哪些单位。因为这可能在打印到 web 环境中有所帮助，或者甚至只是一个在 Photoshop 中开始并在浏览器中结束的项目，所以您将查看像素、点、ems 和百分比。

您还想在它们之间自由转换，所以您已经可以说您的函数至少需要三个参数:

```
@function convert($value, $currentUnit, $convertUnit) {}
```

第一个参数是您希望转换其单位的`font-size`数字(例如 16)，第二个参数是您计划转换的单位(例如像素)，第三个参数是您想要的单位(例如百分比)。例如，如果您想将 16 个像素转换成一个百分比值，您可以这样做:

```
.foo{
 font-size: convert(16, px, percent);
}
```

这将给你:

```
.foo{
  font-size: 100%;
}
```

## 让我们加油吧

现在，是时候解决大括号之间的部分了。

首先，你希望能够处理像素、ems、点数和百分比，所以你需要四个语句来处理它们。

如果您使用的是成熟的编程语言，您可能会使用一个 [switch 语句](https://www.sitepoint.com/jquery-switch-statement/)。因为这是 Sass，所以您将坚持使用 [if 语句](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#_if):

```
@function convert($value, $currentUnit, $convertUnit){
 @if $currentUnit == px{

    // stuff for pixels

  }@else if $currentUnit == ems{

    // stuff for ems

  }@else if $currentUnit == percent{

    // stuff for percentage

  }@else if $currentUnit == pts{

    // stuff for points

  }
}
```

现在，每个可能的输入单位都有一个 if 语句(无论您是希望以像素、ems、点还是百分比开始)。这大约是 50%的路程。你现在要做的就是在这些 if 语句中加入一些很棒的东西！

## 为 Sass 排印单位转换加入数学

好的，所以在这一点上事情变得非常数学化。假设您使用 16px 作为默认的`font-size`，您必须将其转换为 ems 和百分比，如下所示:

```
@if $currentUnit == px{
 @if $convertUnit == ems{
 @return $value / 16 + 0em;
  }
 @else if $convertUnit == percent{
 @return percentage($value / 16);
  }
}
```

同样，每个转换使用一个 if 语句(所以一个用于 ems，一个用于百分比)，然后做一点数学运算来得到期望的输出。我不打算做一个点值的例子，因为这些只适用于打印 CSS。

使用 ems(默认大小为 16px)，您只需除以 16 并添加“em”单位(`+ 0em`)。

带 Sass 的百分比有点棘手。你不能像对待 ems 那样在语句末尾抛出一个“%”，因为 Sass 会立即抛出一个错误(大意是“你把它放在那里干什么！”).所以这里你需要结合 [Sass 的百分比函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#percentage-instance_method)来返回一个有效的百分比单位。

这样，您就有了一个将像素转换为 ems 或百分比的函数！对于许多开发人员来说，这通常已经足够了，但是让我们看看如何扩展这个函数，以涵盖 ems 到像素的转换和百分比到像素的转换:

```
@else if $currentUnit == ems{
 @if $convertUnit == px{
 @return $value * 16 + 0px;
  }
 @else if $convertUnit == percent{
 @return percentage($value);
  }
}
```

每条语句的数学都需要改变，但这将整理出 ems。

接下来，告诉你如何将百分比转换成像素和单位:

```
@else if $currentUnit == percent{
 @if $convertUnit == px{
 @return $value * 16 / 100 + 0px;
  }
 @else if $convertUnit == ems{
 @return $value / 100 + 0em;
  }
}
```

最后，轮到像素点数、ems 点数和百分比转换点数了:

```
@else if $currentUnit == pts{
 @if $convertUnit == px{
 @return $value * 1.3333 + 0px;
  }
 @else if $convertUnit == ems{
 @return $value / 12 + 0em;
  }
 @else if $convertUnit == percent{
 @return percentage($value / 12)
  }
}
```

你完了！您已经创建了一个函数，可以让您在任何单位之间自由转换任何值。

## 综上

Sass 中印刷单位转换的最后一个函数如下所示:

```
@function convert($value, $currentUnit, $convertUnit){
 @if $currentUnit == px{

 @if $convertUnit == ems{
 @return $value / 16 + 0em;
    }
 @else if $convertUnit == percent{
 @return percentage($value / 16);
    }

  }@else if $currentUnit == ems{

 @if $convertUnit == px{
 @return $value * 16 + 0px;
    }
 @else if $convertUnit == percent{
 @return percentage($value);
    }

  }@else if $currentUnit == percent{

 @if $convertUnit == px{
 @return $value * 16 / 100 + 0px;
    }
 @else if $convertUnit == ems{
 @return $value / 100 + 0em;
    }

  }@else if $currentUnit == pts{

 @if $convertUnit == px{
 @return $value * 1.3333 +0px;
    }
 @else if $convertUnit == ems{
 @return $value / 12 + 0em;
    }
 @else if $convertUnit == percent{
 @return percentage($value / 12)
    }
  }
}
```

这看起来有点令人生畏，但它真正做的只是获取初始大小，然后将其从第一个单位转换为第二个单位，并返回结果。唯一困难的部分是记录要进行的计算。

如果你想玩这个功能，你可以在这个 [Sassmeister 演示](http://sassmeister.com/gist/19e7b3c68c326ccde263)中这样做。

和往常一样，当你在网上使用排版时，请随意窃取、篡改、重新排列或以其他方式使用这些对你最有帮助的东西。

您可以轻松地扩展现有的函数，可能包括像 rem 单位转换、对无效输入的错误处理，或者设置您一直使用的默认单位。

如果你对这个 Sass 单位转换函数有任何其他的好主意，请在下面的评论区告诉我们。

## 分享这篇文章