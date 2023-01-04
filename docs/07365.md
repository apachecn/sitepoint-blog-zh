# CoffeeScript 中的类

> 原文：<https://www.sitepoint.com/classes-in-coffeescript/>

JavaScript 没有传统的类系统。相反，它有原型。原型可以非常通用和强大，但是对于新手来说它们很容易混淆。因此，CoffeeScript 创造了一个传统的阶级体系。但是…怎么做？CoffeeScript 的口号是“这只是 JavaScript”，而 JavaScript 显然缺少传统的类系统。在本文中，我们将回顾创建 CoffeeScript 类的基础知识。在下一篇文章中，我们将深入研究(相对高级的)生成的 JavaScript，以弄清楚魔法是如何工作的。


## 父类

我们将主要通过例子来实现这一点，因为对于那些已经阅读了我关于 coffeescript 的介绍性文章的人来说，了解正在发生的事情应该是相当容易的。

```
class Bourgeoisie
  constructor: (@age, @privilegeConstant) ->

  worry: ->
    console.log("My stocks are down 1%!")

  profit: (hardWork, luck) ->
    return (@age - 23) * hardWork * (luck + @privilegeConstant)

elite = new Bourgeoisie(29, 397)
elite.worry() # "My stocks are down 1%!"
elite.profit(20, 50) #53640
```

我们声明一个名为`Bourgeoisie`的类。类中的函数声明如下:

```
functionName: (arguments) ->
  code
```

很明显，构造函数被命名为`constructor`。它接受两个参数，`age`和`priviligeConstant`，并自动将它们分配为实例变量(`@`是 CoffeeScript 对`this`的替换，当用于构造函数的参数时，自动将变量分配给实例)。当你创建一个新的`Bourgeoisie`时，构造函数被自动调用，就像在代码示例的底部。我们还有另外两个功能。第一个是`worry`，没有参数。第二个函数`profit`接受两个参数并返回一个数字。

## 继承的类

现在我们想要一个继承自`Bourgeoisie`的类。我们就叫它`Senator`。

```
class Senator extends Bourgeoisie
  worry: ->
    console.log("The polls are down 1%!")

senator = new Senator(45, 992)
senator.worry() # "The polls are down 1%!")
senator.profit(6, 10) # 132264
```

这个类*扩展了* `Bourgeoisie`，也就是说它具备了父类的所有特征。`constructor`和`profit`函数是*完全相同的*，唯一的区别是在构造实例时你调用了`Senator`而不是`Bourgeoisie`。

另一方面，`worry`函数是不同的。比起股票，`Senator`更担心民调，所以他的`worry` *覆盖了*父类的民调。这种覆盖在一个`Student`类中再次出现，如下所示。

```
class Student extends Bourgeoisie
  worry: ->
    console.log("Does my privilege inherently make me complicit in the repression of less fortunate classes?")

  profit: (hardWork, luck, tuition) ->
    super(hardWork, luck) - tuition

student = new Student(21, 89)
student.worry() #"Does my privilege inherently make me complicit in the repression of less fortunate classes?"
student.profit(10, 10, 10000) #-11980
```

学生的`worry`覆盖了家长的`worry`(比参议员的更戏剧化)，他们的`profit`也被覆盖了。然而，覆盖现在依赖于父类的`profit`函数。它把这个数减去学费。现在不是做学生的时候！但是你真正应该从中学习的是`super`关键字，它调用父版本的函数。

## 但是，我喜欢原型

对你有好处！CoffeeScript 给你带来了方便，但它仍然给你留下了力量。让我们使用它！这是我们上次的简短例子:

```
object = (o) ->
    F = ->
    F.prototype = o
    new F()

soldier = new Object()
soldier.a = jump
soldier.r = machineGun

sniper = object(soldier)
sniper.r = snipe

woundedSniper = object(sniper)
woundedSniper.a = -> console.log('aaaargh my leg!')

woundedSoldier = object(soldier)
woundedSoldier.a = woundedSniper.a
```

这应该看起来很熟悉，因为 90%的变化是用尖箭头代替了几个`function`。原型继承系统没有被触及，因为记住，CoffeeScript 只是 JavaScript。语法更简洁，如果你想用我们上次使用的 Brendan Eich 风格实现原型，没有什么需要学习的了。这并不是说 CoffeeScript 不应用一些快捷方式。你可以用`::`代替`prototype`。然而，在 Brendan Eich 风格的原型中，我们只需要在`object(o)`方法中使用一次。我们也可以访问`extends`和`super`关键字，但是它们只在构造函数中使用——我们再次将它们隐藏在`object(o)`方法中。

## 结论

CoffeeScript 中的经典继承系统为普通开发人员提供了便利和舒适。此外，CoffeeScript 更简洁的语法使得实现一个真正的原型系统稍微容易一些。根本没办法输。

## 分享这篇文章