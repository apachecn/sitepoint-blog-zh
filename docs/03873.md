# 为您的 Sass 框架创建测试

> 原文：<https://www.sitepoint.com/creating-tests-sass-framework/>

所以你写了(或者想写)一个很棒的 Sass 工具包、框架，甚至是一个小函数或者 mixin，你想和全世界分享。太棒了。这就是开源软件开发的伟大之处——您可以与社区中的其他支持开发人员合作开发代码，以创建许多人都可以从中受益的有用软件。

但是，不要这么快！在发布软件之前对其进行测试是非常重要的。最有效的方法之一是使用[单元测试](http://artofunittesting.com/definition-of-a-unit-test/)，这是测试单个代码单元的单个功能是否按照定义的规范运行的自动化脚本。

Sass 使您能够使用[函数](http://sass-lang.com/documentation/Sass/Script/Functions.html)、[操作符](https://www.sitepoint.com/sass-basics-operators/)、[变量](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#variables)和[控制指令和表达式](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#control_directives__expressions)来操作数据。`@function`的返回值也可以直接对照期望值进行测试。对于 CSS 输出和视觉回归测试，像 [PhantomCSS](https://github.com/Huddle/PhantomCSS) 这样的工具更适合这项工作，尽管`@mixins`可以在最新版本的 True 中测试。

我们将使用 [Eric Suzanne 的 **True**](http://www.ericsuzanne.com/true/) 测试框架对 Sass 中的样本函数进行单元测试，但是这里给出的指导原则适用于任何 Sass 测试框架。对于简单的测试，你可能想要使用一个极简的(并且很棒的)Sass 测试框架，比如[雨果·吉劳德的 SassyTester](https://github.com/HugoGiraudel/SassyTester) 。

## 安装

True 可以安装成 Ruby gem ( `gem install true`)或者 Bower 包(`bower install true`)。配置选项可以在 True 的文档中找到[。从那里，可以设置一个](http://www.ericsuzanne.com/true/)[加载路径](http://technology.customink.com/blog/2014/10/09/understanding-and-using-sass-load-paths/)来指向真正的安装。

**将测试文件保存在与 Sass 文件不同的目录中。**这是为了让测试可以很容易地进行，并且避免在观看和编译时的意外失误。如果一个项目主要是一个 Sass 项目，这个目录可以简单地命名为`/tests`，主测试文件位于`/tests/tests.scss`。

在`/tests`目录中，它帮助**通过模块**组织你的测试，这通常是通过它所代表的`@function`或`@mixin`。有时，相似的函数和混合可以被分组到同一个模块中。

```
// Sample Sass project structure
/css
/scss
/tests
  /api
    _foo.scss
    _bar.scss
    _all.scss // imports foo and bar tests
  /helpers
    _baz.scss
    _qux.scss
    _all.scss // imports baz and qux tests
  tests.scss  // imports api/_all and helpers/_all
index.html
package.json
// ... etc.
```

主测试文件`tests.scss`将负责导入和报告所有的测试*和*将被测试的实际 SCSS 项目:

```
@import 'true';

// Import the project
@import '../scss/project';

// Import the test files
@import 'api/all';
@import 'helpers/all';

// Run the tests
@include report();
```

## 测试功能

为了有效地单元测试功能，最好**收集被测试功能的规范。**在模块化架构中，每个功能都有一个**单一职责**——它有一个任务，通常返回相同类型的值。

为一个函数创建规格说明的一个简单方法是，在给定某个输入的情况下，说“**它应该(做某事)**”。这是[行为驱动开发](http://en.wikipedia.org/wiki/Behavior-driven_development)的一个宗旨，在其他测试框架中也可以看到，比如 [Jasmine (JavaScript)](http://jasmine.github.io/) 。

让我们来看一个示例函数(和相关变量):

```
// inside 'scss/project.scss'

$type-base-font-size: 16px !default;
$type-base-ratio: 1.4 !default;

@function type-scale(
 $exponent: 1,
 $base: $type-base-font-size,
 $ratio: $type-base-ratio,
 $factor: 1
) {
 @return ceil(($factor * $base) * pow($ratio, $exponent));
}
```

这是一个非常简单的函数，即**应该根据模数比例**返回适当的字体大小(参见 type-scale.com 的[了解这个概念的可视化表示)。这样，我们就有了第一个*规格*:](http://type-scale.com/)

```
// inside 'tests/api/type-scale.scss'

@include test-module('type-scale') {
 @include test('should return the appropriate font size based on the modular scale') {
 $actual: type-scale(1);
 $expected: 23px; // based on base 16px and ratio 1.4

 @include assert-equal($actual, $expected);
  }
}
```

这里有三个主要部分:

*   **测试模块**:您正在测试的代码的模块(或*单元*);在这种情况下，`type-scale`功能
*   **测试**:关于被测单元的*规范*或假设
*   **断言**:实际值满足期望值的*断言*。

为了使测试更容易管理，我的偏好是在测试断言之前，**将`$actual`和`$expected`的值存储在变量**中。事实上，有四种类型的断言:

*   `assert-true($value)`断言该值为*真值*
*   `assert-false($value)`断言值为*而非真值*，例如`null`或`false`
*   `assert-equal($assert, $expected)`断言实际(断言)值和期望值*相等*
*   `assert-unequal($assert, $expected)`断言实际(断言)和(非)期望值*不等于*

让我们回顾一下单元测试功能的一些策略。

### 如果需要，使用`@mixin`准备测试。

在上面的函数中，我们假设全局变量`$type-base-font-size == 16px`和`$type-base-ratio == 1.4`。假设在测试中是危险的，可变数据也是如此。设置一个`@mixin`，可以在每次测试前强制执行这些值:

```
@mixin test-type-scale-before() {
 $type-base-font-size: 16px !global;
 $type-base-ratio: 1.4 !global;
}
```

现在，这些变量在包含这个`@mixin`的每个测试中都不会被外部修改:

```
@include test-module('type-scale function') {
 @include test('...') {
 @include test-type-scale-before();

    // ... test code
  }
}
```

### 测试每个参数。

Sass `@function`可以包含许多参数(尽管最好限制这些参数)，最好单独测试每个参数以确保它们返回预期的结果:

```
@include test('should work with an integer $exponent') {
 @include test-type-scale-before();

 $actual: type-scale($exponent: 2);
 $expected: 32px;
 @include assert-equal($actual, $expected);
}

@include test('should work with a negative integer $exponent') {
 @include test-type-scale-before();

 $actual: type-scale($exponent: -2);
 $expected: 9px;
 @include assert-equal($actual, $expected);
}

@include test('should work with a numerical font $base') {
 @include test-type-scale-before();

 $actual: type-scale($exponent: 2, $base: 10px);
 $expected: 20px;
 @include assert-equal($actual, $expected);
}

// ... etc. for $ratio, $factor
```

### 测试默认参数。

当然，如果您的函数有默认参数，那么应该断言该函数在没有参数的情况下调用时会返回预期的结果。

```
@include test('should return the base font size with default arguments') {
 @include test-type-scale-before();

 $actual: type-scale();
 $expected: $type-base-font-size;
 @include assert-equal($actual, $expected);
}
```

### 遍历不同的潜在值。

有时，一个参数可以接受不同类型的值，也可以接受不同单位的值。使用`@each`、`@while`或`@for`指令遍历参数可以接受的所有潜在值:

```
@include test('should work with different units for $base') {
 @include test-type-scale-before();

 $values: (10px, 100%, 10pt); // etc.
 $units: ('px', 'percent', 'pt'); // etc.
 $expected-values: (14px, 140%, 14pt); // etc.

 @for $index from 1 through length($values) {
 $value: nth($values, $index);
 $unit: nth($units, $index);
 $actual-value: type-scale(1, $value);
 $expected-value: nth($expected-values, $index);

 @include assert-equal($actual-value, $expected-value, 'Works with #{$unit}');
  }
}
```

## 测试混合

如果为真，可以通过比较实际输出和预期输出来测试`@mixin`的正确性。语法类似于函数断言，增加了一个`@include input`和`@include expect` mixin。

下面是一个使用`type-scale()`函数的文字缩放混合示例:

```
@mixin type-scale($exponent: 0) {
 font-size: type-scale($exponent);
}
```

这是它的配套测试:

```
@include test('@mixin type-scale()') {
 @include assert('should output the appropriate font size') {
 @include input {
 @include type-scale(1);
    }

 @include expect {
 font-size: 23px;
    }
  }
}
```

现在，当测试运行时，您可以比较这个测试的`.input { ... }`块和`.expect { ... }`块:

```
/* @mixin type-scale() */
[data-module="type-scale function"] [data-test="@mixin type-scale()"] [data-assert="should output the appropriate font size"] .input {
 font-size: 23px;
}
[data-module="type-scale function"] [data-test="@mixin type-scale()"] [data-assert="should output the appropriate font size"] .expect {
 font-size: 23px;
}
```

测试 mixins 的准则应该与测试函数的准则相同。确保测试每个参数、默认值，并迭代不同的潜在值。

为了使混合易于测试，将逻辑从混合中分离出来，放入函数中。函数是直接可测试的，这将提供更大的模块化和项目内部关注点的分离。

## 运行您的测试

使用 True，有几种方法运行测试——通过终端中的`true-cli`,或者使用`node-sass`和您选择的测试运行程序，比如 [MochaJS](http://mochajs.org/) 。有关设置和运行测试的更多信息，请参考 [True 的文档](http://www.ericsuzanne.com/true/)。如果你使用的是终端，就像运行`true-cli path/to/your/tests.scss`一样简单。

在`@include test()`里面的所有上述测试应该在一个`@include test-module()`里面，用你正在测试的特定单元的名字作为模块的名字，比如`@include test-module('type-scale')`。你可以看看这个 [Sassmeister 要点](http://sassmeister.com/gist/23c18feba2566a01ca27)来看看上面所有的测试。

## 最终注释

单元测试可以通过自动测试项目的每个部分来节省开发时间。这样，您就可以放心地进行重构，如果所有的单元测试都通过了，代码就可以安全地使用并正确地实现了。单元测试自然也强调模块化的项目结构。

一个好的经验法则是:代码越少越好，单元测试越多越好。保持你的项目单元(混合和功能)的范围和职责较小，并且它们易于测试。测试每一个可能的输入，确保你的函数尽可能的[幂等](http://en.wikipedia.org/wiki/Idempotence)。

如果您想在 Sass 库中看到单元测试的一些真实用法，请查看我为 [Sassdash](https://github.com/davidkpiano/sassdash/tree/master/tests) 编写的单元测试。测试愉快！

## 分享这篇文章