# 5 个 JavaScript 面试练习

> 原文：<https://www.sitepoint.com/5-javascript-interview-exercises/>

根据我上一篇文章[的统计，5 个典型的 JavaScript 面试练习](https://www.sitepoint.com/5-typical-javascript-interview-exercises/)，看起来你们中的很多人正在寻找一份新工作，或者至少想要测试他们的 JavaScript 知识。不管是什么原因导致你阅读这篇文章，在与 JavaScript 频道编辑 Colin Ihrig 达成一致后，我决定再写一篇关于面试中其他一些典型问题的文章。玩得开心！

## 问题 1:关闭

考虑以下代码:

```
var nodes = document.getElementsByTagName('button');
for (var i = 0; i < nodes.length; i++) {
   nodes[i].addEventListener('click', function() {
      console.log('You clicked element #' + i);
   });
}
```

如果用户单击列表中的第一个和第四个按钮，控制台上会显示什么？为什么？

### 回答

上面的代码测试了 JavaScript 的一个非常重要的概念:**闭包**。正确理解和使用闭包对于每个想要在网页中编写超过五行代码的 JavaScript 开发人员来说是至关重要的。如果你需要这方面的入门知识或者只是想复习一下，我强烈建议你阅读由[科林·伊格里克](http://cjihrig.com/)撰写的教程 [JavaScript 闭包去神秘化](https://www.sitepoint.com/javascript-closures-demystified/)。

也就是说，代码打印了两次`You clicked element #NODES_LENGTH`，其中`NODES_LENGTH`是检索到的节点数。原因是在`for`循环完成后，变量`i`的值等于节点列表的长度。此外，因为在代码附加处理程序时,`i`在范围内，所以变量属于处理程序的闭包。正如您所记得的，闭包中变量的值不是静态的，因此`i`的值不是添加处理程序时的值(0 表示列表中的第一个按钮，1 表示第二个按钮，依此类推)。在执行处理程序的时候，控制台上会打印出变量`i`的当前值，该值等于节点列表的长度。

## 问题 2:关闭

修复前一个问题，以便处理程序为列表中的第一个按钮打印 0，为第二个按钮打印 1，依此类推。

### 回答

这个问题可以用几种不同的方法来解决，这里我将向你展示其中的两种。

第一个解决方案包括使用一个生命来创建另一个闭包，这样`i`的值就是预期的值。实现这种方法的代码如下:

```
var nodes = document.getElementsByTagName('button');
for (var i = 0; i < nodes.length; i++) {
   nodes[i].addEventListener('click', (function(i) {
      return function() {
         console.log('You clicked element #' + i);
      }
   })(i));
}
```

另一个可能的解决方案不涉及生命的使用，而是将函数移出循环。这种方法由以下代码实现:

```
function handlerWrapper(i) {
   return function() {
      console.log('You clicked element #' + i);
   }
}

var nodes = document.getElementsByTagName('button');
for (var i = 0; i < nodes.length; i++) {
   nodes[i].addEventListener('click', handlerWrapper(i));
}
```

## 问题 3:数据类型

考虑以下代码:

```
console.log(typeof null);
console.log(typeof {});
console.log(typeof []);
console.log(typeof undefined);
```

产量是多少？

### 回答

前一个问题看起来有点傻，但是它测试了`typeof`操作员的知识。许多 JavaScript 开发人员没有意识到`typeof`的一些特性。在本例中，控制台将显示以下内容:

```
object
object
object
undefined

```

最令人惊讶的输出可能是第三个。大多数开发者期望`typeof []`返回`Array`。如果您想测试变量是否包含数组，可以执行以下测试:

```
var myArray = [];
if (myArray instanceof Array) {
   // do something...
}
```

## 问题 4:事件循环

以下代码的结果是什么？解释一下你的答案。

```
function printing() {
   console.log(1); 
   setTimeout(function() { console.log(2); }, 1000); 
   setTimeout(function() { console.log(3); }, 0); 
   console.log(4);
}

printing();
```

### 回答

代码的输出是:

```
1
4
3
2

```

要理解为什么数字是按这个顺序打印的，你必须理解`setTimeout()`是做什么的，以及浏览器的事件循环是如何工作的。浏览器有一个事件循环，它检查事件队列并处理未决事件。UI 事件(比如点击、滚动等等)、Ajax 回调以及提供给`setTimeout()`和`setInterval()`的回调都由事件循环一次处理一个。因此，当调用`setTimeout()`函数时，所提供的回调被排队，即使指定的延迟为零。回调会一直留在队列中，直到指定的时间过去，并且引擎准备好执行该操作(即，如果它此时没有执行另一个操作)。因此，尽管传递给`setTimeout()`的回调被延迟了 0 毫秒，但它将在同一个函数中声明的其他非延迟语句之后排队并执行。

记住这一点，很容易理解首先打印“1 ”,因为它是函数的第一条语句，使用`setTimeout()`函数不会延迟。然后，我们有“4 ”,因为它是第一个要打印的非延迟数字，所以它不在延迟数字之后排队。现在，还剩“2”和“3”。两者都已添加到队列中，但前者必须等待一秒钟，后者可以在 0 秒钟后打印(这意味着在引擎完成所有其他进程后立即打印)。这就解释了为什么“3”会印在“2”之前。

## 问题 5:算法

写一个`isPrime()`函数，如果一个数是质数，返回`true`，否则返回`false`。

### 回答

我认为这是面试中最常被问到的问题之一。然而，尽管其本质是重复的和简单的，但候选人提供的解决方案讲述了候选人的数学和算法知识。

先说第一件事:这是 JavaScript，不是 C 或 Java，所以不能信任传递的数据类型。如果面试官没有明确告诉你可以直接找到解决方案，要么问他/她是否希望你检查提供的输入，要么以适当的检查开始工作。说真的，总是检查提供给函数的输入。

第二点要记住:负数不是质数。1 和 0 也一样。所以，先测试这些数字。此外，唯一的偶数是质数是 2。用一个循环来验证 4，6，8，等等真的是一派胡言。更有甚者，如果一个数不能被 2 整除，它也不能被 4、6、8 等等整除。因此，您的循环必须跳过这些数字。如果您针对偶数测试输入，您的算法将会慢 2 倍(您测试双倍的数字)。还有其他可以执行的智能优化，但我列举的这些在大多数情况下已经足够了。例如，如果一个数不能被 5 整除，它就不能被它的倍数整除。所以，针对 10、15、20 等等测试输入是没有用的。如果你想深入了解这个问题的解决方案，我建议你阅读相关的维基百科页面。

第三点也是最后一点:你不需要测试大于输入数字平方根的数字。我觉得人们可以忽略这一点，我认为他们不应该因此获得负面反馈。但是，表现出对这个概念的了解应该会加分。

现在您已经对这个问题有了一些背景知识，下面是考虑了前面所有要点的解决方案:

```
function isPrime(number) {
   // If your browser doesn't support the method Number.isInteger of ECMAScript 6,
   // you can implement your own pretty easily
   if (typeof number !== 'number' || !Number.isInteger(number)) {
      // Alternatively you can throw an error.
      return false;
   }

   if (number < 2) {
      return false;
   }

   if (number === 2) {
      return true;
   } else if (number % 2 === 0) {
      return false;
   }

   var squareRoot = Math.sqrt(number);
   for(var i = 3; i <= squareRoot; i += 2) {
      if (number % i === 0) {
         return false;
      }
   }

   return true;
}
```

## 结论

在本文中，借助一些问题和练习，我讨论了其他 JavaScript 关键概念，这些概念通常是前端开发人员面试的一部分。我希望你成功地回答了所有的问题，或者你学到了新的东西，这样你可以在下次面试中表现得更好。

## 分享这篇文章