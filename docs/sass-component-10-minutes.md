# 10 分钟后的 Sass 部分

> 原文：<https://www.sitepoint.com/sass-component-10-minutes/>

今天的开发人员认识到，虽然把你的网站或应用程序代码写成一个更小的片段(即组件)的集合是很棒的，但要做到这一点并不容易。所以让我们用 Sass 来详细看看这个话题。

因为没有什么比一个好的老例子更好的了，我建议我们采用几乎每个网站或应用程序都需要的响应用户交互的东西:警告消息(或通知——或任何你们这些酷孩子现在称呼它们的东西)。

创建一个组件来处理不同类型的警告消息是提升您的 Sass 的最佳案例。所以卷起袖子吧，伙计们，因为我们要沉浸其中了！

## 定义我们的警告颜色

我们想要什么？消息！但是什么样的信息呢？让我们依靠一个已经做了类似事情的框架:Bootstrap。 [Bootstrap 定义了 4 种告警信息](http://getbootstrap.com/2.3.2/components.html#alerts):

*   确认
*   错误
*   警告
*   信息

对于我们的示例组件来说，这听起来不错！就像我们在现实生活中用语调来表达不同的情绪一样，我们也可以在网络上用颜色来传达信息。因此，有四种类型的警告，每种都有自己的配色方案。让我们再次借用 Bootstrap 已经完成的工作，定义我们的警告颜色:

*   绿色代表成功
*   红色代表错误
*   黄色或橙色表示警告
*   浅蓝色表示信息

## 编写我们的基本样式

所有的邮件将共享共同的样式，如填充、边距，可能还有一些印刷样式。最后，唯一有区别的样式将是与代表不同警报的颜色相关的样式。

正如我在文章 [Sass 中讨论的:Mixin 还是 Placeholder？最好的做法是为静态规则扩展一个占位符，并为动态规则使用一个 mixin(也就是说，那些可能根据上下文相关变量而变化的规则)。](https://www.sitepoint.com/sass-mixin-placeholder/)

让我们从占位符开始:

```
%message {
  padding: .5em;
  margin-bottom: .5em;
  border-radius: .15em;
  border: 1px solid;
}
```

首先要注意的是，我们没有指定任何字体样式，比如字号、字体系列或行高。这个模块将被用于一个已经在别处定义了这些模块的项目中，这有助于我们的组件更加独立。

我们也没有为边框定义颜色。默认情况下，`border-color`属性被设置为`currentcolor`，这是`color`值，在许多用户代理中，它将是黑色的。

现在是混音:

```
@mixin message($color) {
  @extend %message;
  color: $color;
  border-color: lighten($color, 20%);
  background: lighten($color, 40%);
}
```

正如您所看到的，mixin 做了两件事:首先，它设置与颜色相关的属性，接受一个独特的颜色作为参数，并创建两种不同的颜色，这要感谢 Sass 的`lighten` color 函数。使用 Sass [颜色操作函数](http://sass-lang.com/documentation/Sass/Script/Functions.html)是减少 mixin 参数数量的好方法。

mixin 做的第二件事是扩展`%message`占位符，这样就不必为每种消息类型重新输入这些样式。这样一来，代码变得非常枯燥(*不要重复自己*)。

## 呼唤混音

我们快完成了。我们剩下的就是为每种类型的消息调用 mixin，为我们想要的消息类型传入正确的颜色值:

```
.message-error {
  @include message(#b94a48);
}

.message-valid {
  @include message(#468847);
}

.message-warning {
  @include message(#c09853);
}

.message-info {
  @include message(#3a87ad);
}
```

## 让东西更干燥

到目前为止，我们所做的一切都很完美。添加一个新的警报类型只需要将带有您选择的颜色的 mixin 转储到新创建的类中。但是如果我们想让它变得更加便携呢？

如果有一种方法可以将每种类型的警告映射到一种颜色而不是其他颜色，那不是很好吗？嗯，有:嵌套列表。通过创建一个二维列表并遍历它的值，我们可以很容易地实现这一点。

```
$message-types: (
  (error    #b94a48)
  (valid    #468847)
  (warning  #c09853)
  (info     #3a87ad)
) !default;

@each $message-type in $message-types {
  $type:  nth($message-type, 1);
  $color: nth($message-type, 2);

  .message-#{$type} {
    @include message($color);
  }
}
```

这段 Sass 代码产生的结果与我们在上一节中编写的代码产生的结果完全相同。除了现在变量被设置在一个列表中；可以移动到样式表或配置文件顶部的列表，使得添加/编辑/删除消息类型变得非常容易。

*注意:如果你计划将它添加到一个库或框架中，我在`@message-types`变量中包含的 [`!default`标志](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#variable_defaults_)允许你在需要时轻松地覆盖这个变量。*

## 使用地图(Sass 3.3)

我们可以额外利用 Sass 3.3 中添加的全新 Sass 类型: [Maps](http://viget.com/extend/sass-maps-are-awesome) 。映射就像 PHP 中的关联数组，或者 JavaScript 中的对象。它们将一个值映射到另一个值。

```
$message-types: (
  error   :  #b94a48,
  valid   :  #468847,
  warning :  #c09853,
  info    :  #3a87ad
) !default;

@each $type, $color in $message-types {
  .message-#{$type} {
    @include message($color);
  }
}
```

多棒啊。

## 优雅地处理错误

Sass 开发人员经常忽略的一点是优雅地处理错误的能力。您应该始终确保传递给自定义函数和 mixins 的参数是准确的，如果不准确，就提供一条警告消息。这比让 Sass 编译器做某事失败要好得多。

在我们的例子中，我们应该确保传递给 mixin 的`$color`参数确实是一种颜色。

```
@mixin message($color) {
  @if type-of($color) == color {
    @extend %message;
    color: $color;
    border-color: lighten($color, 20%);
    background: lighten($color, 40%);
  }

  @else {
    @warn "#{$color} is not a color for `message`.";
  }
}
```

这足以防止 Sass 编译器在试图变亮一个不是有效颜色的值时崩溃。另外，它警告开发人员，他犯了一个错误，向 mixin 传递了一个无效的参数。这可能是一个打字错误或更深层次的东西——不管是什么，它都有帮助。

## 最后的话

在大约 30 行的 SCSS 代码中，我们成功地编写了一个组件:

*   干净易懂
*   干爽轻盈
*   便携且可配置
*   易于扩展

这是一组适用于所有组件的好规则。下面是一个展示代码运行的演示:

请看 [CodePen](http://codepen.io) 上 Hugo Giraudel([@ Hugo Giraudel](http://codepen.io/HugoGiraudel))的 Pen [消息& @extend](http://codepen.io/HugoGiraudel/pen/Dzloe) 。