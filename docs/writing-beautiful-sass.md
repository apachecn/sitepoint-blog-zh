# 如何写出漂亮的 Sass

> 原文：<https://www.sitepoint.com/writing-beautiful-sass/>

以下是雨果·吉劳德和米丽娅姆·苏珊娜写的《快速起步与 T2》一书的节选。这是 Sass 入门的终极指南。SitePoint Premium 会员可以通过他们的会员身份访问，或者您可以在世界各地的商店购买一份。

干净、漂亮的代码应该是每个项目的目标。如果其他开发人员需要进行更改，他们应该能够阅读并理解那里的内容。可读代码是可维护性的核心，而迈向可读代码的第一步是一个好的 **linter** 。就像一个好的拼写检查器一样，linter 应该捕捉到你所有的小的拼写错误和格式错误，所以这不是留给别人去做的。这是与其他开发人员一起进行良好的代码审查之前的第一道防线。

Sass 有几个很棒的 linters:[scss-lint](https://github.com/brigade/scss-lint)是 Ruby gem，更新的 [sasslint](https://github.com/sasstools/sass-lint) 和 [stylelint](http://stylelint.io/) 是 Node 的 npm 包。两者都允许您为项目配置林挺规则，例如最大嵌套层数、小数前导零以及块中属性的组织。您甚至可以根据需要创建自己的规则。

Sass 指南对于组织你的项目、设置你的 linters、建立命名约定等等非常方便。Hugo 写的，这是一个固执己见的代码风格指南；这可能不完全适合你，但这是一个很好的起点。

如果您正在使用 Sass 变量、函数和混合，建议您记录它们是如何工作的。工具包作者会发现这一点特别重要，但是任何在项目中内置了大量工具的人也应该考虑为他们的团队编写文档。Hugo 的另一个很棒的工具是 [SassDoc](http://sassdoc.com) ，它是一个 npm 包，可以解析你的 Sass 评论并生成一个包含你的文档的漂亮的静态站点。

这是我们的`tint(..)`函数在 accuture-Colors 中的 SassDoc 注释。它从一般描述开始，然后明确记录每个参数和预期回报:

```
 /// Mix a color with `white` to get a lighter tint.
///
/// @param {String | list} $color -
///   The name of a color in your palette,
///   with optional adjustments in the form of `(<function-name>:<args>)`.
/// @param {Percentage} $percentage -
///   The percentage of white to mix in.
///   Higher percentages will result in a lighter tint.
///
/// @return {Color} -
///   A calculated css-ready color-value based on your global color palette.
@function tint(
  $color,
  $percentage
) {
  /* … */
} 
```

使用默认主题(有几个主题可供选择，或者您可以设计自己的主题)，SassDoc 将该评论转换为静态网站，如下所示。

![](img/b41b81e0a760c22cd1095b16adb37bd6.png)

如果你正在使用函数或混合做任何复杂的事情，测试也是很重要的。这是确保您的代码在任何时候进行调整时都不会中断的好方法，但它也有助于开发新功能。如果您首先编写测试，那么当您的测试通过时，您将确切地知道该特性是否正常工作！

是一个真正来自你的单元测试工具包，用纯 Sass 编写，所以它可以在任何编译 Sass 的地方工作。核心测试发生在断言函数中:`assert-equal(..)`、`assert-unequal(..)`、`assert-true(..)`和`assert-false(..)`。这些被组织成测试，并且可以被分组到测试模块中。这里有一个真实测试我们的
`tint(..)`函数的例子:

```
@include test-module('Tint [function]') {
  @include test('Adjusts the tint of a color') {
    @include assert-equal(
      tint('primary', 25%),
      mix(#fff, color('primary'), 25%),
      'Returns a color mixed with white at a given weight.');
  }
} 
```

编译时，True 将输出带有详细结果的 CSS 注释，并在任何测试失败时在控制台中警告您:

```
/* # Module: Tint [function] */
/* ------------------------- */
/* Test: Adjusts the tint of a color */
/*   ✔ Returns a color mixed with white at a given weight. */

/* … */

/* # SUMMARY ---------- */
/* 16 Tests: */
/*  - 14 Passed */
/*  - 0 Failed */
/*  - 2 Output to CSS */
/* -------------------- */

```

在这个例子中，两个测试“输出到 CSS”是什么意思？这些测试没有显示出来，但是它们在测试 mixin 输出。使用纯 CSS，True 只能确认函数测试的结果，所以 mixin 测试只是简单地输出到 CSS，在 CSS 中可以手动(粗略地)或用 CSS 解析器(更好！).为了让这变得容易，True 集成了 JavaScript 测试运行程序，如 [Mocha](https://mochajs.org/) ，并有一个由[斯科特·戴维斯](https://twitter.com/jetviper21)编写的 Ruby 命令行界面。任何一个都可以完全解析 CSS 输出，包括 mixin 的输出，并给出函数和 mixin 测试的完整结果:

```
Luminance [function]
  ✓ Returns luminance of a color

Contrast Ratio [function]
  ✓ Returns contrast ratio between two colors

Contrast [function]
  ✓ Dark on light
  ✓ Light on dark
  ✓ Default light fallback
  ✓ Default dark fallback
  ✓ Multiple contrast options

contrasted [mixin]
  ✓ Dark on light
  ✓ Light on dark

Tint [function]
  ✓ Adjusts the tint of a color

Shade [function]
  ✓ Adjusts the shade of a color

Color [function]
  ✓ Named color
  ✓ Referenced color
  ✓ Adjusted color
  ✓ Complex nesting of colors
  ✓ Multiple adjustment function arguments

16 passing (11ms) 
```

## `分享这篇文章`