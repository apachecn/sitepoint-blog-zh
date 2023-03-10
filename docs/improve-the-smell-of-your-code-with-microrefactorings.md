# 用微重构来改善代码的味道

> 原文：<https://www.sitepoint.com/improve-the-smell-of-your-code-with-microrefactorings/>

![](img/0535be6ec90e51ba71790225be3b3610.png)

你可能认为重构是关于可怕的复杂性。花了几个小时——不，几天——试图破译千行方法。一堆杂乱的调用和回调似乎只是偶然产生了工作代码。

有时重构就是所有这些事情，但更多时候，重构是关于检测熟悉的模式，然后有条不紊地响应它们。从远处看，大型重构似乎令人望而生畏。不可能。然而，仔细看，它们是由许多微小的变化组成的，而且每个变化都是合理的。可以解释。

重构与其说是关于千行方法。它是关于在千行方法中识别一个微小的模式，然后应用一步一步的方法来改变这个小事情。它一遍又一遍地这样做。

请注意，这里有两个部分:

*   将代码片段识别为表现出已知有问题的特征。
*   应用已知的更改来解决此类问题。

这不是一个严格的、可靠的过程。这涉及到一些猜测工作。有时你意识到了这个问题，但它隐藏在你难以表达的其他问题中，很难找到它。你可能无法很好地隔离它来修复它。或者你可能感觉到了一点问题，但是你不能确定问题到底是什么。对于一个问题，可能有几种已知的修复方法，但您不确定哪一种会起作用，因此尝试了两三种不同的方法，才找到一种合适的方法。

无论如何，这都归结为检测一个有问题的模式，然后执行一个系统的过程来修复它。

有问题的模式被称为“代码气味”。

> [A]代码气味是一种表面迹象，通常对应于系统中更深层次的问题。马丁·福勒

系统中一个更深层次的问题听起来有点不祥。感觉我们又回到了谈论那种需要几天才能理解，几周或几个月才能解决的事情。问题是，无论代码味道有多大，过程都是一样的。

如果你想在重构方面做得更好，在微观层面应用这个过程可以让你更容易在更大、更混乱的系统中开始处理它。

请看下面的代码片段:

```
sum = 0
numbers.each do |number|
  sum += number
end
sum 
```

对它做一个仓促的判断。

好/坏。干净/脏。简单/复杂。可读/神秘。

你不需要用逻辑或论据来支持你的直觉。注意你喜欢或不喜欢它的地方。

这里还有一个:

```
anagrams = []
candidates.each do |candidate|
  if anagram_of?(subject, candidate)
    anagrams << candidate
  end
end
anagrams 
```

检查你的直觉。将其与之前的示例进行比较。

更好/更坏。相同/不同。

这里还有一个:

```
mutations = 0
(0...strand1.length).each do |i|
  if strand1[i] != strand2[i]
    mutations += 1
  end
end
mutations 
```

还有一个:

```
oldest = ""
highest = 0
kids.each do |kid|
  if kid.age > highest
    oldest = kid.name
    highest = age
  end
end
oldest 
```

花点时间注意一下例子之间的相似和不同之处。

最明显的相似之处可能是，这些都是循环。同样，每个循环都是一个`each`。更重要的是，上面的每个代码示例都展示了一种或两种特定的微代码味道。

*   *带有临时变量的循环。*
*   *带有嵌套条件的循环。*

解决这两种代码味道的方法是选择一个更合适的可枚举方法。

起初，“选择一个更合适的可枚举方法”看起来像是魔术。你怎么知道的？你不知道。你基本上是去[可枚举方法](http://ruby-doc.org/core-2.3.0/Enumerable.html)的列表，看看是否能找到一个有意义的。你尝几个。你开始对它们有感觉了。您会发现，在某些情况下，不止一个可枚举方法做了令人钦佩的工作。随着时间的推移，你开始本能地寻找好的可枚举方法。

下面是与上面例子中展示的微气味相匹配的微重构。

第一个循环只有一个临时变量，没有嵌套条件。可以用`inject`或其别名`reduce`固定:

```
# before
sum = 0
numbers.each do |number|
  sum += number
end
sum

# after
numbers.inject(:+) 
```

第二个循环有一个临时变量和一个嵌套条件。我们正在过滤一个列表，而`select`和`reject`对于这类事情来说都是不错的。在这种情况下，既然我们要过滤以保留而不是丢弃，`select`或它的别名`keep_if`是一个不错的选择:

```
# before
anagrams = []
candidates.each do |candidate|
  if anagram_of?(subject, candidate)
    anagrams << candidate
  end
end
anagrams

# after
candidates.select do |candidate|
  anagram_of?(subject, candidate)
end 
```

第三个例子也展示了两种微气味。在这种情况下，我们计算差异:

```
# before
mutations = 0
(0...strand1.length).each do |i|
  if strand1[i] != strand2[i]
    mutations += 1
  end
end
mutations

# after
(0...strand1.length).count {|i| strand1[i] != strand2[i]} 
```

最后的代码示例有三重麻烦:两个临时变量和一个嵌套的条件变量。既然我们在某个属性上排名，也许`sort_by`会有用:

```
# before
oldest = ""
highest = 0
kids.each do |kid|
  if kid.age > highest
    oldest = kid.name
    highest = age
  end
end
oldest

# after
kids.sort_by {|kid| kid.age}.last.name 
```

事实证明还有更好的选择。如果您想要按任意属性排序的复杂对象列表的第一个或最后一个，您可以使用`min_by`和`max_by`:

```
# even more after
kids.max_by {|kid| kid.age}.name 
```

在现实世界中，循环不会总是一个`each`。它也不会总是特别冗长。有时条件句会很简洁:一个后缀`if`或`unless`，或者一个三元陈述。

不过，一旦你以这种精度识别出一种气味，这个问题几乎在任何情况下都很容易诊断和解决。

```
# before
words.inject([]) do |censured, word|
  censured << word if word.size == 4
  censured
end

# after
words.select {|word| word.size == 4} 
```

有时候你无法摆脱它——可能没有一个完美的可枚举方法。这就是代码气味的本质。它们通常表明有更好的方法来解决问题，但并不总是如此。

与 Martin Fowler 在他的书 [Refactoring](http://www.amazon.com/Refactoring-Ruby-Addison-Wesley-Professional-ebook/dp/B002TIOYWG/) 中描述的经典代码气味不同，微代码气味不太可能是系统中更深层次问题的征兆。微代码气味只会影响其附近的几行代码。

这仍然值得去做，不仅仅是因为你开始感觉到在一个好的重构中有多么少的戏剧性。

减少摩擦、去除样板文件和以微小的增量提高可读性的累积效果是，更大的代码味道浮出水面，变得更容易检测。

如果你能发现它，你就有很大的机会去修复它。

## 分享这篇文章