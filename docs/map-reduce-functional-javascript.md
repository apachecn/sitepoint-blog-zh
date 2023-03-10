# 在函数式 JavaScript 中使用 Map 和 Reduce

> 原文：<https://www.sitepoint.com/map-reduce-functional-javascript/>

**关于支持 ECMAScript 6 中令人惊叹的新功能的工作流，我们很容易忘记 ECMAScript 5 为我们带来了一些很好的工具来支持我们今天可以使用的 JavaScript 函数式编程。其中包括基本 JavaScript `Array`对象上的本地 [map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 和 [reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 方法。**

如果你今天没有使用`map()`和`reduce()`，是时候开始了。大多数当代 JavaScript 平台原生支持 ECMAScript 5。映射和缩减可以使您的代码更加整洁，更易于阅读和维护，并使您走上更优雅的功能开发之路。

## 性能:警告

当然，当情况需要时，阅读和维护代码必须与性能相平衡。目前，使用更麻烦的传统技术，比如循环，浏览器的执行效率更高。

我的方法通常是首先为了可读性和可维护性而编写代码[，然后如果我注意到现实情况中的问题，就优化性能。过早的优化是魔鬼。](https://www.sitepoint.com/importance-of-code-that-humans-can-read/)

同样值得考虑的是，使用像`map()`和`reduce()`这样的方法可能会更好地利用 JavaScript 引擎的改进，因为将来浏览器会为它们进行优化。除非我在性能问题上碰壁，否则我更喜欢乐观地编码，并把那些使我的代码不那么吸引人的性能调整放在我的口袋里，以备不时之需。

## 使用地图

映射是一种基本的函数式编程技术，用于对数组中的所有元素进行操作，并使用转换后的内容生成另一个相同长度的数组。

为了更具体一点，让我们来看一个简单的用例。例如，假设您有一个单词数组，您需要将其转换为包含每个单词长度的数组。(我知道，对于您的复杂应用程序来说，这不是您经常需要做的那种复杂的火箭科学，但是理解它在这样一个简单的情况下是如何工作的，将有助于您在它可以为您的代码增加真正价值的情况下应用它)。

你可能已经知道如何在数组上使用一个`for`循环来完成我刚刚描述的事情。它可能看起来像这样:

```
var animals = ["cat","dog","fish"];
var lengths = [];
var item;
var count;
var loops = animals.length;
for (count = 0; count < loops; count++){
  item = animals[count];
  lengths.push(item.length);
}
console.log(lengths); //[3, 3, 4] 
```

我们所做的就是定义几个变量:一个名为`animals`的数组，包含我们的单词；一个名为`lengths`的空数组，包含我们操作的输出；一个名为`item`的变量，临时存储我们将在数组的每个循环中操作的每一项。我们用一个临时内部`count`变量和一个`loops`变量建立了一个`for`循环来优化我们的`for`循环。然后，我们遍历每一项，直到达到`animals`数组的长度。对于每一个，我们计算长度，并将其推送到`lengths`数组中。

*注意:可以说，如果没有 item 变量，我们可以更简洁地将`animals[count]`的长度直接推送到`lengths`数组，而不需要中间赋值。这将为我们节省一点代码，但也会降低可读性，即使对于这个非常简单的例子也是如此。类似地，为了提高性能，但不那么简单，我们可以使用已知长度的`animals`数组将我们的`lengths`数组初始化为`new Array(animals.length)`，然后通过索引插入条目，而不是使用 push。这完全取决于你在现实世界中如何使用这些代码。*

这种方法在技术上没有任何问题。它应该可以在任何标准的 JavaScript 引擎中工作，并且能够完成任务。但是一旦你知道如何使用`map()`，这样做看起来就很笨拙。

让我向您展示我们如何使用`map()`来实现这一点:

```
var animals = ["cat","dog","fish"];
var lengths = animals.map(function(animal) {
  return animal.length;
});
console.log(lengths); //[3, 3, 4] 
```

在这种情况下，我们再次从动物类型的`animals`数组的变量开始。然而，我们声明的另外一个变量是`lengths`，我们将它的值直接赋给了将一个匿名内嵌函数映射到`animals`数组的每个元素的结果。这个匿名函数对每只动物执行一次操作，返回长度。结果，`lengths`变成了一个和原来的`animals`数组一样长的数组，包含每个单词的长度。

关于这种方法需要注意一些事情。首先，比原来短了很多。其次，我们必须声明更少的变量。变量越少，意味着全局名称空间中的噪音越少，如果相同代码的其他部分使用同名变量，发生冲突的机会也就越少。此外，我们的变量从始至终都不需要改变它们的值。随着您对函数式编程的深入，您将会体会到使用常量和[不可变变量](https://www.sitepoint.com/immutability-javascript/)的优雅力量，并且开始使用永远不会太早。

这种方法的另一个优点是，我们有机会通过分离一个命名函数来提高它的通用性，在这个过程中产生更干净的代码。匿名的内嵌函数看起来很混乱，并且很难重用代码。我们可以定义一个命名的`getLength()`函数，并在上下文中这样使用它:

```
var animals = ["cat","dog","fish"];
function getLength(word) {
  return word.length;
}
console.log(animals.map(getLength)); //[3, 3, 4] 
```

看到那看起来有多干净了吗？仅仅将映射作为工具箱的一部分就可以将您的代码带到一个全新的功能级别。

### 什么是函子？

有趣的是，通过向 array 对象添加映射，ECMAScript 5 将基本的 array 类型变成了一个完整的函子，使得函数式编程对我们所有人来说更加容易。

根据经典的函数式编程定义，函子满足三个标准:

1.  它包含一组值
2.  它实现了一个映射函数来操作每个元素
3.  它的映射函数返回相同大小的函子

这是您下次 JavaScript 会议要讨论的问题。

如果你想了解更多关于函子的知识，看看 Mattias Petter Johansson 的视频。

## 使用 Reduce

[reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 方法在 ECMAScript 5 中也是新的，它类似于`map()`，除了`reduce()`不是产生另一个函子，而是产生一个可能是任何类型的结果。例如，假设您想要获得我们的`animals`数组中所有单词的长度之和作为一个数字。你可以这样开始:

```
var animals = ["cat","dog","fish"];
var total = 0;
var item;
for (var count = 0, loops = animals.length; count < loops; count++){
  item = animals[count];
  total += item.length;
}
console.log(total); //10 
```

在我们定义了初始数组之后，我们为运行总数创建了一个变量`total`，初始设置为零。我们还创建了一个变量`item`来保存`animals`数组在`for`循环中的每次迭代，一个变量`count`用于循环计数器，还有一个变量`loops`用于优化我们的迭代。然后我们运行一个`for`循环来遍历`animals`数组中的所有单词，将每个单词赋给`item`变量。最后，我们将每个项目的长度加到总数中。

同样，这种方法在技术上没有任何问题。我们从一个数组开始，以一个结果结束。但是使用`reduce()`方法，我们可以让这变得更加简单:

```
var animals = ["cat","dog","fish"];
var total = animals.reduce(function(sum, word) {
  return sum + word.length;
}, 0);
console.log(total); 
```

这里发生的事情是，我们定义了一个新变量`total`，并使用两个参数将减少`animals`数组的结果赋给它:一个匿名内联函数和一个初始运行总值零。Reducing 遍历数组中的每一项，对该项执行一个函数，并将其添加到传递给下一次迭代的运行总和中。这里，我们的内嵌函数接受两个参数:运行总和，以及当前正在从数组中处理的单词。该函数将`total`的当前值与当前单词的长度相加。

注意，我们将`reduce()`的第二个参数设置为零，这就确定了`total`将包含一个数字。如果没有第二个参数，reduce 方法仍然可以工作，但是结果不一定是您所期望。(试一试，看看您是否能推导出在不考虑累计时 JavaScript 使用的逻辑。)

这看起来可能比实际需要的要复杂一些，因为在调用`reduce()`方法时集成了内嵌函数的定义。让我们再做一次，但是让我们先定义一个命名函数，而不是使用一个匿名的内嵌函数:

```
var animals = ["cat","dog","fish"];
var addLength = function(sum, word) {
  return sum + word.length;
};
var total = animals.reduce(addLength, 0);
console.log(total); 
```

这有点长，但更长并不总是一件坏事。从这个角度来看，reduce 方法发生了什么应该会更清楚一点。

`reduce()`方法有两个参数:一个应用于数组中每个元素的函数，一个用于累计总数的初始值。在本例中，我们传递了一个名为`addLength`的新函数的名称，以及运行总数的初始值零。我们已经创建了`addLength()`函数，因此它也接受两个参数:一个运行总和和一个要处理的字符串。

## 结论

习惯于定期使用`map()`和`reduce()`将为您提供使您的代码更干净、更通用、更易维护的替代方法，并为您使用功能更多的 JavaScript 技术铺平道路。

`map()`和`reduce()`方法只是添加到 ECMAScript 5 中的两个新方法。十有八九，您今天使用它们所看到的代码质量和开发人员满意度的提高将远远超过对性能的任何暂时影响。使用功能技术进行开发，并在决定`map()`和`reduce()`是否适合您的应用程序之前，测量在现实世界中的影响。

*这篇文章由 [Panayiotis Velisarakos](https://www.sitepoint.com/author/pvelisarakos) 、 [Tim Severien](https://www.sitepoint.com/author/tseverien) 和 [Dan Prince](https://www.sitepoint.com/author/dprince/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章