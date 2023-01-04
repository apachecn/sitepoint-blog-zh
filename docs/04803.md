# 从 Ruby Sass 切换到 LibSass

> 原文：<https://www.sitepoint.com/switching-ruby-sass-libsass/>

如果您一直在关注 Sass 社区最近的重大公告，您可能已经在 SassConf 2014 上看到了巨大的 LibSass 3.0 公告。

一段时间以来，Sass Ruby gem 的功能越来越先进，而 LibSass 的更新速度却越来越慢。这意味着许多使用 LibSass 编译 Sass 的人(通常通过 Grunt/Gulp 工作流中的 node-sass)无法使用尖端的 Sass 工具，就像您在 SitePoint 上读到的那些工具。

一个重要的声明是，Ruby Sass 现在将等待与 [LibSass](https://github.com/sass/LibSass/releases/tag/3.0rc1) 的特性对等，然后它们将尽可能保持相同的速度。LibSass 3.0 已经发布，你可以用最新的 [node-sass 版本](https://www.npmjs.org/package/node-sass)将它集成到你的任务运行器中。

然而，是什么让 LibSass 如此重要呢？与 Ruby Sass 相比，它最大的优势是速度更快—[*比*](http://www.solitr.com/blog/2014/01/css-preprocessor-benchmark/)快得多。如果更快的编译时间对你来说听起来不错，这里有一个很好的介绍[安装 LibSass](http://mattferderer.com/compile-sass-with-sassc-and-libsass/) 。

在本文中，我将非常实际:我将分享我在尝试用 LibSass 编译我的 mixin 库时发现的所有“陷阱”。虽然 LibSass 3.0 与 Ruby Sass 3.4.6 很接近，但有几个方面并不完全相同。你可能不会在你的 Sass 中面对所有这些，但是如果你真的遇到了这些小错误，我希望这个列表能帮助你解决它们。

*注:在本文中，“Ruby Sass”总是指 3.4.6，“LibSass”总是指 3.0.1，除非另有说明。*

## 不要用`@if not ...`测试

在 Ruby Sass 中，`@if not not index($list, $value) {}`是一种确保值在列表中的聪明方法。自从 [Hugo 建议](https://www.sitepoint.com/tips-help-level-up-sass/)强制条件评估一个真正的布尔值以来，我一直在我的 Ruby Sass 项目中使用这个条件。

然而，在 LibSass 中`not`不起作用，所以这个方法似乎总是计算为 true。在 LibSass 中，可以通过使用以下方法之一来解决此问题:

```
@if index($list, $value) { ... }
@if index($list, $value) != null { ... }
@if index($list, $value) > 0 { ... }
```

请查看[这份萨斯梅斯特要点](http://sassmeister.com/gist/a253f11c6c94e248a4f4)。

## 注意你的数学语法。

Ruby Sass 将让您完成以下任务:

```
$map: (value: 1200px);
.container {
 max-width: map-get($map,value)-1;
}
// Ruby output: 1199px
// LibSass output: 1200px -1
```

然而，LibSass 希望您在减号前后加一个空格:

```
$map: (value: 1200px);
.container {
 max-width: map-get($map,value) - 1;
}
//output
.container {
 max-width: 1199px; }
```

请查看[这份萨斯梅斯特要点](http://sassmeister.com/gist/d77aa1c48281fdf3d5f9)。

## `@error`不起作用。

在 Ruby Sass 中，`@error`将错误打印到控制台并停止编译器。它还允许您将变量插入到有用的错误消息中。

然而，在 LibSass 中，`@error`行将被简单地打印到编译后的输出中，就好像它是一个 CSS 声明行一样。因为它只是被打印到输出，所以如果变量在引号内，它不会插入变量。我认为您仍然可以使用中断的`@error`指令，并在输出中搜索“@error”。如果是这样，您可以通过字符串连接来解决变量插值问题:

```
$error-text: 'error text';
.at-error {
 color: blue;
 @error 'Error: #{$error-text}.'
 @error 'Error: ' + $error-text + '.';
}
//output:
.at-error {
 color: blue;
 @error 'Error: #{$error-text}.';
 @error 'Error: error text.'; }
```

请看这个[萨斯梅斯特要点](http://sassmeister.com/gist/f7ec9301b72a1e5b9bee)的实际操作。

(`@warn`仍然向您的“控制台”打印警告，并继续编译。)

## 整洁的网格工作；苏西还没有工作。

不久前，我写了关于 Sass 网格从 Neat 转换到 Susy 的文章。但是，如果您正在使用 LibSass，我强烈建议您不要切换。 [Susy](http://susy.oddbird.net/) 还不能在 LibSass 上运行。然而，即使 Susy 不能正常工作，你也可以使用 Su[创建你自己的网格系统。](https://github.com/ericam/su)

另一方面，Neat 只对配置属性使用变量:它现在在 LibSass 上工作得很好。在这篇 Sassmeister 要点中，看看[如何在 LibSass 上工作。](http://sassmeister.com/gist/71fb0fdf341d92b001ef)

## 不支持带有`&`的`selector-*()`功能。

如果你是为数不多的为 Sass 3.4 中的新`selector-`函数找到了一个好的用例的人之一，这些不会跟随你进入 LibSass。对此，除了“审查您的嵌套策略”(这可能会完全消除对选择器插值的需求)，我没有其他建议。

## `@at-root`指令不起作用。

Ruby Sass 允许您使用`@at-root`将嵌套选择器移出其嵌套上下文。然而，LibSass 将`@at-root`视为选择器字符串，并将其打印到样式表:

```
.at-root-test {
 content: 'parent';
  .nest {
 content: 'child';
 @at-root .selector {
 content: "not nested";
    }
  }
}
//output
.at-root-test {
 content: 'parent'; }
  .at-root-test .nest {
 content: 'child';
 @at-root .selector {
 content: "not nested"; }
 }
```

同样，解决方法是重构嵌套。请点击[查看这个萨斯梅斯特要点](http://sassmeister.com/gist/7df232c817309b24016f)。

## 地图

地图支持是 LibSass 3 中最大的特性之一。常见的地图功能正如您所料。在 LibSass 3 中可以使用[`map-keys()``map-values()``map-has-keys()``map-get()``map-merge()`。](http://sassmeister.com/gist/a7ef48a20fbede0e337f)

正如你现在所收集的，地图在很大程度上介于 Libsass 3 和 Ruby Sass 之间。享受使用它们吧！

## 扩展

LibSass 3 的另一个主要新增功能是`@extend`支持。我现在还不知道 Ruby Sass 和 LibSass 在这方面有什么不同，但这是一个足够大的升级，值得在这里提及。

你可以看到 [`@extend`在 LibSass 工作在这个 Sassmeister gist](http://sassmeister.com/gist/c866e91f79a34c022a00) 。

## 结论

切换到 LibSass 将会带来巨大的速度提升。然而，仍然有一些地方 LibSass 的特性与 Ruby Sass 3.4+的特性不相上下。如果你的 mixin 库依赖于那些特定的特性，那么暂时不要更新。

如果你已经迁移到 LibSass 并发现了其他问题或其他语法调整或解决方法，请在评论中分享！

## 分享这篇文章