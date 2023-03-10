# 编号()。toFixed()舍入错误:已损坏但可修复

> 原文：<https://www.sitepoint.com/number-tofixed-rounding-errors-broken-but-fixable/>

*本文最初发表于[戴维·凯伊](https://dfkaye.wordpress.com/2017/12/06/number-tofixed-rounding-errors-broken-but-fixable/)。*

我在我尝试过的每个 JavaScript 环境(Chrome、Firefox、Internet Explorer、Brave 和 Node.js)的*中发现了一个舍入错误。修复出奇的简单。请继续阅读…*

## 变暖

我在`toFixed()`中发现了这个版本的舍入错误，同时修改了一个数字格式函数，该函数执行与`Intl.NumberFormat#format()`相同的事情。

```
(1.015).toFixed(2) // returns "1.01" instead of "1.02" 
```

失败的测试在第 42 行[这里](https://gist.github.com/dfkaye/0d84b88a965c5fae7719d941e7b99e2e#file-number-format-js-L42)。直到 2017 年 12 月，我一直没有发现它，这促使我检查其他问题。

查看我关于它的推文:

*   [Bug 警报](https://twitter.com/dfkaye/status/937766562576023552)
*   [与 Intl 比较。数字格式](https://twitter.com/dfkaye/status/937829022490181637)
*   [总结](https://twitter.com/dfkaye/status/937851125721579520)
*   [聚合填充](https://twitter.com/dfkaye/status/938129172119113728)

## 错误报告

关于使用`toFixed()`舍入误差的错误报告有很长的历史。

*   [铬合金](https://forums.asp.net/t/1966237.aspx?toFixed+not+working+in+Chrome)
*   [火狐](http://forums.mozillazine.org/viewtopic.php?f=9&t=999945)
*   [火狐，参见](https://bugzilla.mozilla.org/show_bug.cgi?id=186563)
*   [网络浏览器](https://stackoverflow.com/questions/10470810/javascript-tofixed-bug-in-ie6)

以下是关于此问题的 StackOverflow 问题的简短示例:

*   [例一](https://stackoverflow.com/questions/12105787/tofixed-javascript-function-giving-strange-results)
*   [例二](https://stackoverflow.com/questions/5490687/broken-tofixed-implementation)

一般来说，这些报告指出了*和*值的*和*错误，但是没有一个报告返回错误结果的值的范围或模式(至少我没有发现，我可能遗漏了一些东西)。这使得程序员只关注小问题，而看不到更大的模式。我不为此责怪他们。

## 寻找模式

基于输入的意外结果必定来自输入中的共享模式。因此，我没有回顾`Number().toFixed()` 的规格说明，而是专注于用一系列值进行测试，以确定每个系列中哪里出现了 bug。

### 测试功能

我创建了下面的测试函数来对从 1 到 a 的一系列整数进行测试，给每个整数加上 T2，比如. 005。`toFixed()`的`fixed`(位数)参数是根据`fraction`值的长度计算的。

```
 function test({fraction, maxValue}) {

      // Happy side-effect: `toString()` removes trailing zeroes.
      fraction = fraction.toString()
      var fixLength = fraction.split('.')[1].length - 1

      // All this to create the expectedFraction message...
      var last = Number(fraction.charAt(fraction.length - 1))
      var fixDigit = Number(fraction.charAt(fraction.length - 2))

      last &gt;= 5 &amp;&amp; (fixDigit = fixDigit + 1)

      // Replace last two digits with single `fixDigit`
      var expectedFraction = fraction.replace(/[\d]{2,2}$/, fixDigit)

      return Array(maxValue).fill(0)
        .map(function(ignoreValue, index) {
          return index + 1
        })
        .filter(function(integer) {
          // Compares 1.015 to 1.0151 b/c fixing by more than one decimal place rounds correctly.
          var number = integer + Number(fraction) // number 1.015
          var actual = number.toFixed(fixLength)  // string "1.015"
          var expected = Number(number + '1').toFixed(fixLength) // string "1.0151"

          // Report failures
          return expected != actual
        })
        .map(function(integer) {
          // Format reported failures
          var number = Number(integer) + Number(fraction)
          return {
            given: number.toString(),
            expected: (Number(integer.toFixed(0)) + Number(expectedFraction)).toString(),
            actual: number.toFixed(fixLength)
          }
        })
    } 
```

### 使用

下面的示例对整数 1 到 128 执行，将分数. 015 加到每个整数上，并返回一个“意外”结果数组。每个结果包含一个`given`、`expected`和`actual`字段。这里我们使用数组并打印每一项。

```
test({ fraction: .015, maxValue: 128 })
  .forEach(function(item) {
    console.log(item)
  }) 
```

### 输出

对于这种情况，有 6 个意外的结果。

```
Object { given: "1.015", expected: "1.02", actual: "1.01" }
Object { given: "4.015", expected: "4.02", actual: "4.01" }
Object { given: "5.015", expected: "5.02", actual: "5.01" }
Object { given: "6.015", expected: "6.02", actual: "6.01" }
Object { given: "7.015", expected: "7.02", actual: "7.01" }
Object { given: "128.015", expected: "128.02", actual: "128.01" } 
```

## 调查的结果

我发现这个 bug 由三部分组成:

1.  分数的最后一位有效数字必须是 5(0.015 和 0.01500 产生相同的结果)。
2.  固定长度必须将分数缩短一位数。
3.  当应用不同的整数值时，错误出现不一致。

## 不一致？

例如，对于从 1 到 128 的整数，具有以 5 结尾的不同三位数分数的`(value).toFixed(2)`会产生以下结果:

*   固定以. 005 结尾的号码总是失败(！！)
*   固定以. 015 结尾的数字对于 1、4 到 7、128 都是失败的
*   固定以. 025 结尾的数字会失败 1、2、3，然后是 16 到 63
*   对于 1，固定以. 035 结尾的号码失败，然后是 32 到 128
*   对于 1 到 15，然后是 128，修复以. 045 结尾的号码会失败
*   对于 1，固定以. 055 结尾的号码失败，然后是 4 到 63
*   对于 1、2、3、8 到 15、32 到 128，固定以. 065 结尾的数字会失败
*   固定以. 075 结尾的数字对于 1 失败，然后是 8 到 31，然后是 128
*   修复以. 085 结尾的数字对于 1 到 7 失败，然后是 64 到 127(！！)
*   固定以. 095 结尾的数字对于 1、4 到 7、16 到 128 都失败

你们中那些比我有更多二进制和浮点数学知识的人可能会推理出潜在的原因。我把它留给读者做练习。

## 固定`toFixed()`

通过超过一个小数位的*固定一个值*总是正确舍入；如`(1.0151).toFixed(2)`如预期返回“1.02”。测试和 polyfill 都使用这些知识进行正确性检查。

这意味着对所有的`toFixed()`实现都有一个简单的修复:如果值包含小数，在要修改的值的字符串版本的末尾追加“1”。这可能不“符合规格”，但这意味着我们将获得我们期望的结果，而不必重新访问更低级的二进制或浮点运算。

## 多填色

在修改完所有实现之前，您可以使用下面的 polyfill 来覆盖`toFixed()`，如果您愿意这样做的话(不是每个人都愿意)。

```
(1.005).toFixed(2) == "1.01" || (function(prototype) {
  var toFixed = prototype.toFixed

  prototype.toFixed = function(fractionDigits) {
    var split = this.toString().split('.')
    var number = +(!split[1] ? split[0] : split.join('.') + '1')

    return toFixed.call(number, fractionDigits)
  }
}(Number.prototype)); 
```

然后再次运行测试，检查结果的长度是否为零。

```
test({ fraction: .0015, maxValue: 516 }) // Array []
test({ fraction: .0015, maxValue: 516 }).length // 0 
```

或者只运行从这篇文章开始的初始转换。

```
(1.015).toFixed(2) // returns "1.02" as expected 
```

感谢您的阅读！

## 分享这篇文章