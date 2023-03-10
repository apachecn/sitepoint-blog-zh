# 如何应对 Python 面试

> 原文：<https://www.sitepoint.com/tackle-python-interview/>

你和人力资源部的第一轮通话结束了吗？你是要亲自去 Python 面试吗？如果你想知道可能会问到哪些与 Python 相关的问题，本指南应该会有所帮助。

在第一部分，我们将讨论几个关于 Python 哲学的问题——这些问题有助于您对项目的架构做出决策。在下一节中，我们将讨论与 Pythonic 编程方式相关的问题——这些问题可能以查看或查找代码片段输出的形式出现。

在我们开始之前，有一点需要注意。本指南主要讨论 Python 的内置功能。本指南的目的是帮助您快速掌握内在的 Python 功能，从而实现快速开发。因此，我们不可能涵盖你可能面临的来自不同类型公司的所有问题。

想要提高您的 Python 技能并在快速增长的市场中脱颖而出吗？查看 [SitePoint Premium](https://www.sitepoint.com/premium/library) ！你会找到一些书籍来帮助你入门(比如《Python 学徒】中的[)和发展工作技能(比如《Python 中的前端测试](https://www.sitepoint.com/premium/books/the-python-apprentice/)】)。使用 Python 大师[提高您的技能，并访问超过 400 本关于 web 设计和开发的书籍和课程的不断增长的图书馆。](https://www.sitepoint.com/premium/books/the-python-master/)

## Python 开发:项目架构

### Python 是什么？为什么应该使用 Python？

如果你在面试一个 Python 角色，你应该对 Python 是什么以及它与其他编程语言有何不同有一个清晰的概念。这里有几个关于 Python 的要点，你应该知道。

首先，你不应该弄错词源。一大部分 Python 程序员错误地认为吉多·范·罗苏姆以蛇命名了它！相反，Python 是以英国小品喜剧[巨蟒剧团(Monty Python)的飞行马戏团](https://en.wikipedia.org/wiki/Monty_Python%27s_Flying_Circus)命名的。

其次，Python 是一种高级的、面向对象的、解释型编程语言。这意味着 Python 代码是逐行执行的。Python 也是动态类型的，因为它不要求你在声明变量时指定变量的类型。

鉴于 Python 的易用性，它已经为常见的自动化任务找到了用途。Python 通常是懂多种语言的程序员的首选脚本。随着基于 Python 的 web 框架(如 Django 和 Flask)越来越受欢迎，Python 的份额近年来显著增加。

### Python 的局限性

虽然了解一种编程语言的能力是件好事，但意识到它的局限性以真正理解您需要警惕的情况也是件好事。

Python 的第一个限制是执行速度。尽管 Python 的开发速度很快，但与 C++等编译语言相比，执行类似的 Python 代码块通常要慢一些。由于这个原因，黑客马拉松经常给 Python 程序一些额外的执行时间。不过，有一些方法可以绕过这个问题。例如，您可以将 Python 与 C 之类的编译语言集成，通过另一种语言来执行核心处理。

在一个移动先行的世界里，Python 不是移动开发的原生语言。你很少会发现用 Python 开发的移动应用程序。两大移动操作系统 Android 和 iOS 都不支持 Python 作为官方编程语言。

### 包装决定:Django vs Flask

除了 Python 的能力和局限性之外，在面试中很流行的一类问题集中在根据您的需求选择包。让我们来看看你在处理这类问题时可以采取的一种方法。

假设您可以在 Django 和 Flask 之间选择启动一个 web 应用程序。这个问题的答案应该是项目需求和组织文化的融合。

首先，你应该知道插件的使用没有正确的答案:你可以使用任何一种框架来创建相似的应用程序。然而，每个框架的设计理念之间有着明显的差异。Flask 为您提供了创建 web 应用程序的最基本的功能，如 URL 路由、模板、单元测试和开发服务器，从而为您设计应用程序提供了很大的自由度。另一方面，Django 从一开始就为您提供了大量内置特性——数据库支持、广泛的管理功能和安全特性。

如果您正在构建一个使用关系数据库的应用程序，有很多动态内容，那么您可能应该选择 Django。然而，如果你在你的项目中寻找更多的自由，你应该选择 Flask。

## Pythonic 式编程

Python 面试的一个重要部分是动手编程。它可能以查找输出或检查代码片段的形式出现，或者只是讨论在某些情况下会有帮助的特定 Python 特性。在这一节中，我们来看看 Python 开发的各种特性和指南。

### 循环`zip`和`enumerate`

在 Python 中创建循环的方式不同于其他编程语言。Python 的内置函数`zip`和`enumerate`帮助你创建运行更快的更有效的循环。我们来看看这两个函数的演示。

假设你想从两个不同的列表中访问相应的元素。您可以遍历列表的长度，并从每个列表中访问相应的元素。例如，下面的代码打印汽车及其相应的制造商:

```
cars = ['CR-V', 'Silverado', 'F-150']
manufacturers = ['Honda', 'GM', 'Ford']

for i in range(len(cars)):
  print(cars[i], manufacturers[i]) 
```

这可以通过使用 Python 中一个更简单的函数来实现，`zip`:

```
for car, manufacturer in zip(cars, manufacturers):
  print(car, manufacturer) 
```

在这个例子中，`zip`在两个列表的元素之间创建对。它也可以用于连接两个以上的列表。

让我们看看`enumerate`是如何工作的。如果您想访问索引以及列表元素的值，您可以运行下面的代码片段:

```
for i in range(len(cars)):
  print (i, cars[i]) 
```

然而，`enumerate`函数执行相同的任务:

```
for i, car in enumerate(cars):
  print (i, car) 
```

使用`zip`和`enumerate`不仅能使你的代码精简，还能使它运行得更快。

### 单行列表操作

如果你想修改一个列表的元素，你可能希望使用一个类似于`map`的函数，可能如下:

```
numbers = [1, 2, 3, 4, 5]

def squared(num):
  return num**2

squares = map(squared, numbers) 
```

然而，Python 允许单行列表操作来实现这样的任务。实现相同任务的两种方法如下所示:

```
squares = [squared(x) for x in numbers]
squares = [x**2 for x in numbers] 
```

此外，您也可以在这样的操作中使用单行`if ... else`语句:

```
square_only_evens = [x**2 if x%2 == 0 else x for x in numbers] 
```

也可以在这些单行操作中使用 [lambda 表达式](https://docs.python.org/3/tutorial/controlflow.html#lambda-expressions)。

### 使用`pdb`进行调试

虽然您可以使用 print 语句来调试其他编程语言中的代码，但是 Python 附带了一个内置的调试器，`pdb`。在程序中插入以下代码行，执行将在该行停止，以便您调试环境:

```
import pdb; pdb.set_trace() 
```

在此之前，所有可用的变量和函数都可供您检查和发现任何错误。

### 数据结构

要利用 Python 的全部功能，您必须熟悉内置的数据结构。列表是项目的集合，这些项目可能不属于同一类型。元组类似于列表，但它是不可变的。集合是项目的无序集合，没有任何重复。字典是键值对的集合。

您必须了解这些数据结构可能被使用的各种情况。

## 最后的想法

至此，我们结束了对 Python 面试的考虑。希望他们已经为你提供了一些关于如何准备下一次面试的有用指导。祝你好运！

* * *

**使用 [SitePoint Remote](https://www.sitepoint.com/jobs/) 找到您的下一份远程 Python 工作，在这里我们为开发人员、设计师和数字专业人士精心挑选最佳远程工作。**

## 分享这篇文章