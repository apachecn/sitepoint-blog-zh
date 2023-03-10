# 函数式 JavaScript 中的过滤和链接

> 原文：<https://www.sitepoint.com/filtering-and-chaining-in-functional-javascript/>

*这篇文章由[丹王子](https://www.sitepoint.com/author/dprince/)、[维尔丹·索维奇](https://www.sitepoint.com/author/vildansoftic/)和[琼·茵恩](https://github.com/newjs)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

![Noah filtering the animals waiting to board the ark](img/fb377cd4a3cb06b7728e9d67ebf62f7a.png)

我欣赏 JavaScript 的一点是它的多功能性。JavaScript 为您提供了使用面向对象编程、命令式编程甚至函数式编程的机会。您可以根据您当前的需求以及您团队的偏好和期望在它们之间来回切换。

尽管 JavaScript 支持函数式技术，但它并不像 Haskell 或 Scala 这样的语言那样针对纯函数式编程进行优化。虽然我通常不会将我的 JavaScript 程序构造成 100%功能性的，但我喜欢使用函数式编程概念来帮助我保持代码整洁，并专注于设计可以轻松重用和干净测试的代码。

## 过滤以限制数据集

随着 ES5 的出现，JavaScript 数组继承了一些方法，使得函数式编程更加方便。JavaScript 数组现在可以原生地映射、 [reduce](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 、 [filter](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 。这些方法中的每一个都遍历数组中的每一项，并且不需要循环或局部状态改变，执行一个分析，该分析可以返回一个结果，该结果可以立即使用或传递以供进一步操作。

在这篇文章中，我想向你介绍过滤。筛选允许您评估数组中的每一项，并根据您传入的测试条件，确定是否返回包含该元素的新数组。当您使用 Array 的`filter`方法时，您得到的是另一个数组，它的长度与原始数组相同或者更小，包含原始数组中符合您设置的条件的项目的子集。

## 使用循环演示过滤

这类问题的一个简单例子可能会从过滤中受益，那就是将字符串数组限制为只有三个字符的字符串。这并不是一个复杂的问题，我们可以使用普通的 JavaScript `for`循环而不使用`filter`方法来轻松解决。它可能看起来像这样:

```
var animals = ["cat","dog","fish"];
var threeLetterAnimals = [];
for (let count = 0; count < animals.length; count++){
  if (animals[count].length === 3) {
    threeLetterAnimals.push(animals[count]);
  }
}
console.log(threeLetterAnimals); // ["cat", "dog"] 
```

我们在这里做的是定义一个包含三个字符串的数组，并创建一个空数组，我们可以在其中存储只有三个字符的字符串。我们定义了一个 count 变量，当我们遍历数组时，它将在`for`循环中使用。每当我们遇到正好有三个字符的字符串时，我们就把它放入新的空数组。一旦完成，我们只需记录结果。

没有什么可以阻止我们修改循环中的原始数组，但是这样做我们会永久地丢失原始值。创建一个新数组而不动原来的数组要干净得多。

## 使用过滤方法

我们这样做在技术上没有任何问题，但是 Array 上的`filter`方法的可用性使我们的代码更加简洁明了。这里有一个例子，说明我们如何使用`filter`方法完成完全相同的事情:

```
var animals = ["cat","dog","fish"];
var threeLetterAnimals = animals.filter(function(animal) {
  return animal.length === 3;
});
console.log(threeLetterAnimals); // ["cat", "dog"] 
```

和以前一样，我们从一个包含原始数组的变量开始，我们为数组定义了一个新的变量，它将只包含三个字符的字符串。但是在这种情况下，当我们定义第二个数组时，我们将它直接赋给了对原始 animals 数组应用`filter`方法的结果。我们给`filter`传递了一个匿名的内嵌函数，如果它所运算的值的长度为三，这个函数就返回`true`。

`filter`方法的工作方式是，遍历数组中的每个元素，并对该元素应用测试函数。如果测试函数为该元素返回`true`，那么`filter`方法返回的数组将包含该元素。其他元素将被跳过。

您可以看到代码看起来有多干净。甚至不需要提前理解`filter`是做什么的，你也许可以看一下这段代码，然后理解它的意图。

函数式编程的一个令人愉快的副产品是整洁，这是由于减少了存储的本地状态的数量，并限制了从函数内部对外部变量的修改。在这种情况下，当我们遍历原始数组时，`count`变量和我们的`threeLetterAnimals`数组所采用的各种状态只是需要跟踪更多的状态。使用`filter`，我们已经设法消除了`for`循环和`count`变量。我们不会像以前那样多次改变新数组的值。我们定义它一次，并给它赋值，这个值来自于对原始数组应用我们的`filter`条件。

## 格式化过滤器的其他方式

如果我们利用`const`声明和匿名内嵌[箭头函数](https://www.sitepoint.com/javascript-arrow-functions/)，我们的代码会更加简洁。这些是 EcmaScript 6 (ES6)的特性，现在大多数浏览器和 JavaScript 引擎本身都支持这些特性。

```
const animals = ["cat","dog","fish"];
const threeLetterAnimals = animals.filter(item => item.length === 3);
console.log(threeLetterAnimals); // ["cat", "dog"] 
```

虽然在大多数情况下超越旧语法可能是一个好主意，但除非您需要使您的代码与现有的代码库相匹配，否则有所选择是很重要的。随着我们变得越来越简洁，我们代码的每一行都变得越来越复杂。

JavaScript 如此有趣的部分原因是，您可以尝试多种方法来设计相同的代码，以优化大小、效率、清晰度或可维护性，从而满足您团队的偏好。但这也给团队带来了更大的负担，他们需要创建共享的风格指南，并讨论每个选择的利弊。

在这种情况下，为了使我们的代码更具可读性和通用性，我们可能希望将匿名的内嵌箭头函数转换成传统的命名函数，并将该命名函数直接传递给`filter`方法。代码可能如下所示:

```
const animals = ["cat","dog","fish"];
function exactlyThree(word) {
  return word.length === 3;
}
const threeLetterAnimals = animals.filter(exactlyThree);
console.log(threeLetterAnimals); // ["cat", "dog"] 
```

我们在这里所做的只是提取上面定义的匿名内嵌箭头函数，并将其转换成一个单独的命名函数。正如我们所看到的，我们已经定义了一个纯函数，它为数组的元素取合适的值类型，并返回相同的类型。我们可以直接将该函数的名称作为条件传递给`filter`方法。

## 快速查看地图并缩小

过滤与 ES5 中的另外两个函数数组方法`map`和`reduce`协同工作。感谢 JavaScript 中链接方法的能力，您可以使用这种组合来编写非常干净的代码，执行一些非常复杂的功能。

作为一个快速提醒，`map`方法遍历数组中的每个元素，并根据函数修改它，返回一个具有修改值的相同长度的新数组。

```
const animals = ["cat","dog","fish"];
const lengths = animals.map(getLength);
function getLength(word) {
  return word.length;
}
console.log(lengths); //[3, 3, 4] 
```

`reduce`方法遍历一个数组并执行一系列操作，将这些操作的运行结果保存在一个累加器中。当它完成时，它返回一个最终结果。在这种情况下，我们使用第二个参数将初始累加器设置为 0。

```
const animals = ["cat","dog","fish"];
const total = animals.reduce(addLength, 0);
function addLength(sum, word) {
  return sum + word.length;
}
console.log(total); //10 
```

所有这三种方法都保持原始数组不变，这是正确的函数式编程实践应该做到的。如果你想了解一下`map`和`reduce`是如何工作的，你可以看看我之前关于在函数式 JavaScript 中使用 map 和 reduce[的文章。](https://www.sitepoint.com/map-reduce-functional-javascript/)

## 链接映射、归约和过滤

作为一个非常简单的例子，让我们假设您想要获取一个字符串数组，并返回一个只包含原始字符串中的三个字母的字符串，但是您想要在[study caps](https://en.wikipedia.org/wiki/Studly_caps)中格式化结果字符串。如果不使用`map`、`reduce`和`filter`，您可以尝试这样做:

```
const animals = ["cat","dog","fish"];
let threeLetterAnimalsArray = [];
let threeLetterAnimals;
let item;
for (let count = 0; count < animals.length; count++){
  item = animals[count];
  if (item.length === 3) {
    item = item.charAt(0).toUpperCase() + item.slice(1);
    threeLetterAnimalsArray.push(item);
  }
}
threeLetterAnimals = threeLetterAnimalsArray.join("");
console.log(threeLetterAnimals); // "CatDog" 
```

当然这是可行的，但是正如你所看到的，我们创建了一堆我们不需要的额外变量，并且维护了一个数组的状态，这个状态在我们经历不同的循环时会发生变化。我们可以做得更好。

如果你想知道变量声明背后的逻辑，我更喜欢用`let`来声明一个空的目标数组，尽管从技术上来说它可以被声明为`const`。使用`let`提醒我数组的内容将要被修改。一些团队可能更喜欢在这种情况下使用`const`，这是一个很好的讨论。

让我们创建一些接受字符串并返回字符串的纯函数。然后我们可以在一系列的`map`、`reduce`和`filter`方法中使用它们，以这种方式将结果从一个传递到下一个:

```
const animals = ["cat","dog","fish"];
function studlyCaps(words, word) {
  return words + word;
}
function exactlyThree(word) {
  return (word.length === 3);
}
function capitalize(word) {
  return word.charAt(0).toUpperCase() + word.slice(1);
}
const threeLetterAnimals = animals
  .filter(exactlyThree)
  .map(capitalize)
  .reduce(studlyCaps);
console.log(threeLetterAnimals); // "CatDog" 
```

在这种情况下，我们定义了三个纯函数，`studlyCaps`、`exactlyThree`和`capitalize`。我们可以在一个完整的链中将这些功能直接传递给`map`、`reduce`和`filter`。首先我们用`exactlyThree`过滤我们的原始数组，然后我们将结果映射到`capitalize`，最后我们用`studlyCaps`减少结果。我们将操作链的最终结果直接赋给我们新的`threeLetterAnimals`变量，没有循环，没有中间状态，并且保持我们原来的数组不变。

最终的代码非常简洁，易于测试，并为我们提供了纯粹的功能，我们可以在其他环境中轻松使用或根据需求的变化进行修改。

## 过滤和性能

值得注意的是，在浏览器和 JavaScript 引擎针对新的数组方法( [jsPerf](https://jsperf.com/array-loop-vs-filter) )进行优化之前，`filter`方法的执行速度可能会比使用`for`循环慢一点点。

正如我之前所说的，我建议无论如何都要使用这些函数数组方法，而不是使用循环，即使它们目前在性能上有点慢。我喜欢它们，因为它们产生更干净的代码。我总是建议以最干净、最易维护的方式编写代码，然后只在现实情况证明您需要更好的性能时才进行优化。对于我可以预见的大多数用例，我不认为过滤器性能是典型 web 应用程序中的一个重要瓶颈，但是您可以确定的唯一方法是尝试一下并找出答案。

过滤可能比使用`for`循环稍慢，这一事实不太可能在现实世界中引起明显的性能问题。但是如果是这样，如果你的用户受到负面影响，你会知道在哪里以及如何优化。随着 JavaScript 引擎针对这些新方法进行优化，性能只会变得更好。

不要害怕从今天开始过滤。该功能是 ES5 固有的，几乎得到了普遍支持。您生成的代码将会更清晰，更易于维护。使用`filter`方法，您可以确信您不会改变正在评估的数组的状态。每次都将返回一个新的数组，而原来的数组将保持不变。

同意吗？不同意？欢迎下方评论。

## 分享这篇文章