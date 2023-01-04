# 在 5 分钟内测试 Sass 功能

> 原文：<https://www.sitepoint.com/testing-sass-function-5-minutes/>

前几天，我在玩 Eduardo Bouç as 的 [include-media](https://github.com/eduardoboucas/include-media/) 库，我想快速测试我构建的一个函数，所以我开始编写一个小 mixin 来帮助我测试许多不同的场景。几分钟后，我想出了你可能得到的最简单的 Sass 测试引擎。

虽然这篇文章可能有点技术性，但我相信它会对许多人有所帮助，因为测试它应该是每个开发人员的责任。此外，当你一件一件地经历事情的时候，你会发现它其实并不那么难理解。

## 创建一个虚拟函数进行测试

一切都从要测试的函数开始。对于我们的目的，我建议我们用一个非常简单的函数。比方说，一个使数翻倍的函数。

```
@function double($value) {
 @return $value * 2;
}
```

听起来很简单。虽然，只是为了我们的演示，我们将主动在函数中引入一个 bug，这样我们就可以看到我们的一个测试失败了。

```
@function double($value) {
  // Voluntarily introduced bug for demonstration purpose
 @if $value == 3 {
 @return 5;
  }

 @return $value * 2;
}
```

## 写作测试

您可能会觉得奇怪，但是在我们的系统中编写测试就像编写 Sass 映射一样简单，其中键是函数输入，值是预期结果。

```
$tests-double: (
 1: 2,
 2: 4,
 3: 6,
 4: 8
);
```

就是这样！我们已经写好了测试。同样:左边是输入，右边是期望的输出。

## 测试赛跑者

到目前为止，一切顺利。我们已经构建了我们的函数，并且编写了我们的测试。我们现在只需要创建测试运行程序。

如果您熟悉 Sass，您可能已经理解了它的发展方向。我们的测试运行程序将在测试图上迭代，为每个输入调用函数，并确保它匹配预期的输出。然后，它将打印我们的测试结果。

这是我们的测试跑步者的样子:

```
/// Run a function ($function) on a test suite ($tests)
/// @param {Map} $tests - Test suite
/// @param {String} $function - Name of function to test
@mixin run-tests($tests, $function) { .. }
```

好吧。让我们深入野兽的腹部。这个想法是用每个测试的结果构建一个字符串，一旦一切都完成了，就用`@error`指令打印这个字符串。例如，我们也可以将它传递给伪元素的`content`属性，但是它稍微复杂一些，所以我们将坚持使用`@error`。

首先要做的是迭代测试套件。对于每个测试，我们动态地从名字中调用函数(用`call(..)`函数),并检查结果是否如预期的那样。

```
@mixin run-tests($tests, $function) {
 $output: '';

 @each $test, $expected-result in $tests {
 $result: call($function, $test...);

 @if $result == $expected-result {
      // Test passed
 $output: $output + 'Test passed; ';
    } @else {
      // Test failed
 $output: $output + 'Test failed; ';
    }
  }

  // Print output
 @error $output;
}
```

此时，我们的系统正在工作。让我们在测试套件上运行它，看看它是什么样子。

```
@include run-tests($tests-double);
```

```
Test passed; Test passed; Test failed; Test passed;
```

嘿！这是一个开始，对吗？现在我们只需要让输出更有帮助(和友好)。

## 推销产品

此时，您可以定制输出，使其看起来像您想要的那样。没有单一的方法可以做到这一点，你可以输出任何你喜欢的。注意，根据 CSS 规范，可以使用`\a`在字符串中换行。

就我而言，我是这样做的:

```
@mixin run-tests($tests, $function) {
 $output: '';
 $length: length($tests);
 $failing: 0;

 @each $test, $expected-result in $tests {
 $result: call($function, $test...);
 $test-index: index(map-keys($tests), $test);
 $output: $output + '\a Test #{$test-index} out of #{$length}... ';

 @if $result == $expected-result {
      // Test passed
 $output: $output + 'âœ”';
    } @else {
      // Test failed
 $failing: $failing + 1;
 $output: $output + 'âœ˜\a   Expected : `#{$expected-result}`.\a   Actual   : `#{$result}`.';
    }
  }

  // Print output
 @error 'Started tests for function `#{$function}`\a '
 + '-----------------------------------'
 + $output + '\a '
 + '-----------------------------------\a '
 + 'Over: #{$length - $pass} test(s) out of #{$length} failing.';
}
```

如果我们在`double`函数的`$tests-double`上再次运行它，我们得到的是:

```
Started tests for function `double`
-----------------------------------
Test 1 out of 4... ✔
Test 2 out of 4... ✔
Test 3 out of 4... ✘
  Expected : `6`.
  Actual   : `5`.
Test 4 out of 4... ✔
-----------------------------------
Over: 1 test(s) out of 4 failing.
```

这很棒，不是吗？

## 测试具有多个参数的函数

在我们的例子中，我们的函数只有一个参数，但是我们可以依赖这样一个事实，即 Sass maps 接受任何东西作为键(包括列表),来测试带有几个参数的函数。它看起来像这样:

```
@function my-function($string, $color, $length) { .. }

$tests-my-function: (
  ('a', red, 42px): 'My expected result',
  // ...
);
```

如果你回头看看我们的 mixin，你会看到当用`call(..)`调用函数时，我们给`$test`变量添加了一个省略号(`...`)。

```
$result: call($function, $test...);
```

这意味着我们将值`$test`作为`arglist`传递。换句话说，如果`$test`是一个列表(例如`('a', red, 42px)`，那么它将作为几个参数传递，而不是作为一个列表。

## 最后的想法

伙计们，现在你们有了:有史以来最简约的 Sass 测试引擎。这个小小的测试系统可以非常方便地测试你的项目中可能有的几个功能，特别是如果你打算把它提供给其他开发人员(框架、库……)。另外，我发现在 SassMeister 上快速测试一个函数非常方便。只需将 mixin 和您的函数放在那里，然后运行您的测试！

当然，如果你正在寻找更深入的解决方案，你可能想看看埃里克·苏珊娜的 [True](http://www.ericsuzanne.com/true/) 。作为 Sass 的完整测试框架，它更适合于全局单元测试基础设施。

如果你想看一看(稍微高级一点的版本)代码，我打开了 [SassyTester](https://github.com/HugoGiraudel/SassyTester) 库来收集一切。

你觉得怎么样？

## 分享这篇文章