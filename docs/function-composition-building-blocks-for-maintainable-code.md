# 功能组合:可维护代码的构建块

> 原文：<https://www.sitepoint.com/function-composition-building-blocks-for-maintainable-code/>

*这篇文章由[杰夫·莫特](https://www.sitepoint.com/author/jmott)、[丹·普林斯](https://www.sitepoint.com/author/dprince)和[塞巴斯蒂安·塞茨](https://www.sitepoint.com/community/users/m3g4p0p/activity)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

![Image of a conductor in front of glowing screens of code](img/ec32bac9cf50f89e7cca5890e3c3ff44.png)

从函数的角度考虑 JavaScript 的优势之一是能够使用简单易懂的单个函数构建复杂的功能。但有时这涉及到向后看问题，而不是向前看，以找出如何创造最优雅的解决方案。

在本文中，我将采用一步一步的方法来检查 JavaScript 中的函数组合，并演示它如何能够产生更容易推理且错误更少的代码。

## 嵌套函数

组合是一种技术，它允许您将两个或多个简单的函数组合成一个更复杂的函数，该函数按照逻辑顺序对您传入的任何数据执行每个子函数。

为了得到这个结果，您将一个函数嵌套在另一个函数中，并对内部函数的结果重复执行外部函数的操作，直到您产生一个结果。并且根据应用这些功能的顺序，结果可能不同。

使用我们在 JavaScript 中已经熟悉的编程技术，通过将一个函数调用作为参数传递给另一个函数，可以很容易地演示这一点:

```
function addOne(x) {
  return x + 1;
}
function timesTwo(x) {
  return x * 2;
}
console.log(addOne(timesTwo(3))); //7
console.log(timesTwo(addOne(3))); //8 
```

在这种情况下，我们定义了一个函数`addOne()`将一个值加 1，一个函数`timesTwo()`将一个值乘以 2。通过将一个函数的结果作为另一个函数的参数传入，我们可以看到将其中一个函数嵌套在另一个函数中会产生不同的结果，即使初始值相同。首先执行内部函数，然后将结果传递给外部函数。

## 命令式作文

如果您想要重复执行相同的操作序列，那么定义一个新函数来自动应用第一个函数，然后再应用另一个更小的函数可能会更方便。可能看起来像这样:

```
// ...previous function definitions from above
function addOneTimesTwo(x) {
  var holder = x;
  holder = addOne(holder);
  holder = timesTwo(holder);
  return holder;
}
console.log(addOneTimesTwo(3)); //8
console.log(addOneTimesTwo(4)); //10 
```

在这种情况下，我们所做的是以特定的顺序手动组合这两个函数。我们创建了一个新函数，它首先将传递的值分配给一个 holder 变量，然后通过执行第一个函数更新该变量的值，然后执行第二个函数，最后返回该 holder 的值。

(注意，我们使用一个名为`holder`的变量来保存我们临时传入的值。对于这样一个简单的函数，额外的局部变量似乎是多余的，但是即使在命令式 JavaScript 中，将传递给函数的参数值视为常量也是一个很好的做法。在本地修改它们是可能的，但是当它在一个函数的不同阶段被调用时，它会引起参数值的混淆。)

同样，如果我们想创建另一个新函数，以相反的顺序应用这两个较小的函数，我们可以这样做:

```
// ...previous function definitions from above
function timesTwoAddOne(x) {
  var holder = x;
  holder = timesTwo(holder);
  holder = addOne(holder);
  return holder;
}
console.log(timesTwoAddOne(3)); //7
console.log(timesTwoAddOne(4)); //9 
```

当然，这段代码开始看起来相当重复。我们的两个新组合函数几乎完全相同，除了它们调用的两个较小函数的执行顺序不同。我们需要把它擦干(比如不要重复你自己)。此外，像这样使用临时变量来改变它们的值是不太实用的，即使它隐藏在我们正在创建的组合函数中。

底线:我们可以做得更好。

## 创建功能组合

让我们设计一个组合函数，它可以将现有的函数按照我们想要的顺序组合在一起。为了以一致的方式做到这一点，而不必每次都处理内部函数，我们必须决定我们希望函数作为参数传入的顺序。

我们有两个选择。每个参数都是函数，可以从左到右执行，也可以从右到左执行。也就是说，使用我们提出的新函数，`compose(timesTwo, addOne)`可能意味着`timesTwo(addOne())`从右向左读取参数，或者`addOne(timesTwo())`从左向右读取参数。

从左到右执行参数的优点是，它们的阅读方式与英语的阅读方式相同，这与我们将复合函数命名为`timesTwoAddOne()`的方式非常相似，目的是暗示乘法应该发生在加法之前。我们都知道逻辑命名对于清理可读代码的重要性。

从左到右执行参数的缺点是要操作的值必须排在前面。但是将值放在第一位会使将来将结果函数与其他函数组合起来变得不太方便。为了很好地解释这一逻辑背后的思想，你不能击败布莱恩·朗斯多夫的经典视频[嘿强调一下，你做错了](https://www.youtube.com/watch?v=m3svKOdZijA)。(尽管需要注意的是，现在[有了一个针对下划线](https://github.com/stoeffel/underscore.string.fp)的 fp 选项，这有助于解决 Brian 在与函数编程库(如 [lodash-fp](https://github.com/lodash/lodash-fp) 或 [Ramda](http://ramdajs.com/) 一起使用下划线时讨论的函数编程问题。)

在任何情况下，我们真正想做的是首先传递所有的配置数据，最后传递要操作的值。正因为如此，定义我们的 compose 函数来读入它的参数并从右向左应用它们是最有意义的。

所以我们可以创建一个基本的`compose`函数，如下所示:

```
function compose(f1, f2) {
  return function(value) {
    return f1(f2(value));
  };
} 
```

使用这个非常简单的`compose`函数，我们可以更简单地构造我们之前的两个复杂函数，并且可以看到结果是相同的:

```
function addOne(x) {
  return x + 1;
}
function timesTwo(x) {
  return x * 2;
}
function compose(f1, f2) {
  return function(value) {
    return f1(f2(value));
  };
}
var addOneTimesTwo = compose(timesTwo, addOne);
console.log(addOneTimesTwo(3)); //8
console.log(addOneTimesTwo(4)); //10
var timesTwoAddOne = compose(addOne, timesTwo);
console.log(timesTwoAddOne(3)); //7
console.log(timesTwoAddOne(4)); //9 
```

虽然这个简单的`compose`函数可以工作，但是它没有考虑到许多限制其灵活性和适用性的问题。例如，我们可能想要组合两个以上的函数。此外，我们一路上失去了`this`的踪迹。

我们可以解决这些问题，但这对于理解构图是如何工作的来说是不必要的。比起自己开发，从某个函数库继承一个更健壮的`compose`可能更有效率，比如 [Ramda](http://ramdajs.com/docs/#compose) ，默认情况下，它确实考虑了参数从右到左的排序。

## 打字是你的责任

重要的是要记住，程序员有责任知道每个被组合的函数返回的类型，这样下一个函数才能正确地处理它。与进行严格类型检查的纯函数式编程语言不同，JavaScript 不会阻止您尝试编写返回值类型不合适的函数。

您不局限于传递数字，甚至不局限于从一个函数到下一个函数维护相同类型的变量。但是您有责任确保您正在编写的函数准备好处理前一个函数返回的任何值。

## 考虑你的观众

永远记住，将来可能会有人需要使用或修改你的代码。对于不熟悉函数范式的程序员来说，在传统 JavaScript 代码中使用组合可能显得复杂。目标是使代码更清晰，更易于阅读和维护。

但是随着 ES2015 语法的出现，甚至可以使用[箭头函数](https://www.sitepoint.com/javascript-arrow-functions/)创建一个简单的组合函数作为一行调用，而无需特殊的`compose`方法:

```
function addOne(x) {
  return x + 1;
}
function timesTwo(x) {
  return x * 2;
}
var addOneTimesTwo = x => timesTwo(addOne(x));
console.log(addOneTimesTwo(3)); //8
console.log(addOneTimesTwo(4)); //10 
```

## 今天开始作曲

和所有的函数式编程技术一样，记住你的组合函数应该是纯函数是很重要的。简而言之，这意味着每次将特定值传递给函数时，函数都应该返回相同的结果，并且函数不应该产生改变自身外部值的副作用。

当您有一组要应用于数据的相关功能时，组合嵌套会非常方便，并且您可以将该功能的组件分解为可重用且易于组合的函数。

与所有函数式编程技术一样，我建议明智地在现有代码中加入组合，以便熟悉它。如果你做得对，结果将是更干净、更干燥、更可读的代码。这不正是我们想要的吗？

## 分享这篇文章