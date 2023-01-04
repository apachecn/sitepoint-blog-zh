# 赞美懒惰

> 原文：<https://www.sitepoint.com/java-in-praise-of-laziness/>

> 我很懒。但是是懒人发明了轮子和自行车，因为他们不喜欢走路或搬运东西。莱赫·瓦文萨

> 懒惰是所有坏习惯的根源。但最终她是一位母亲，我们应该尊重她。――鹿丸奈良

### 目录 

*   [什么是懒惰？](#whatislaziness)
*   [不同类型的懒惰](#differenttypesoflaziness)
*   [为什么所有的语言都很懒](#whyalllanguagesarelazy)
*   [Java 什么时候懒了？](#whenisjavalazy)
*   [懒惰的好处](#thebenefitsoflaziness)
*   [懒惰的代价](#thecostsoflaziness)
*   [让不可能成为可能](#makingtheimpossiblepossible)
*   [总结](#summary)
*   [评论](#comments)

在日常生活中，懒惰通常被视为一种必须与之斗争的坏习惯，甚至是一种必须治愈的疾病。但是不管你认为懒惰是好是坏，你可能会同意懒惰仅仅在于试图用最少的努力获得最多的东西。这意味着避免无用的努力。达到这个目标的一个方法是拖延，即在真正必要之前什么都不做。这就是计算中懒惰的含义。有些人会争辩说拖延就是什么都不做，即使是在真正必要的时候。在计算中，这将被简单地称为一个错误。

## 什么是懒惰？

在编程中，懒惰通常与事物的评估方式有关。*懒惰评估*意味着延迟计算直到需要结果，这与*急切评估*相反。使用懒惰评估的语言被称为*懒惰语言*。热切评估事物的语言被称为*严格语言*。

有些语言是懒惰的，有些是严格的。有些默认是严格的，也可以随意偷懒，而有些则……嗯，正好相反。据说 Java 是一种严格的语言。但这意味着什么呢？

当我们说 Java 是一种严格的语言时，它通常是在一个非常特定的上下文中:Java 在评估引用和方法参数时是严格的，这意味着引用和方法参数被急切地评估。

让我们举一个例子。如果你写`int x = 5`，它会导致文字值`5`被名字`x`引用。有时候有人说，值`5`是存储在变量`x`中的的*，类似于计算机的做法，计算机会在由`x`表示的某个位置存储一些表示`5`的数据。但是请注意，只要程序没有执行，写上面的行不会将`5`存储在`x`中。稍后将详细介绍。*

所以看起来很清楚，等号右边的数据是被这个符号左边的名字引用的。然而，如果我们写`int x = 2 + 3`，它不会导致变量`x`引用操作`2 + 3`。相反，`x`将引用操作的*结果*。

这就是*懒*和*严*的区别，或者说*懒评*和*热切评*的区别。使用急切求值时，首先计算等号右边的表达式，然后通过左边的名称引用结果。

考虑下面的 Java 程序:

```
public static void main(String... args) {
    int x = 2 + 3;
    int y = 18 / 2;
    System.out.println(y);
} 
```

计算出`x`的值是完全无用的，因为这个值在任何地方都不会被使用。如果 Java 是一种懒惰的语言，它只会计算`y`，这将是执行`println`方法的结果。

所以，Java 是严格的，这意味着它急切地评估事物，尤其是对于变量和方法参数。这可以通过另一个小例子来说明:

```
public static void main(String... args) {
    displayMessage(getLocalizedMessage(), getDefaultMessage());
}

private static void displayMessage(
        String localizedMessage, String defaultMessage) {
    if (localizedMessage != null && localizedMessage.length() != 0) {
        System.out.println(localizedMessage);
    } else {
        System.out.println(defaultMessage);
    }
}

private static String getDefaultMessage() {
    System.out.println("Evaluating default message");
    return "Bye!";
}

private static String getLocalizedMessage() {
    System.out.println("Evaluating localized message");
    return "Ciao!";
} 
```

运行该程序会显示:

```
Evaluating localized message
Evaluating default message
Ciao! 
```

这表明，虽然没有使用`defaultMessage`参数，但是已经对其进行了评估，从而导致了对`getDefaultMessage()`的调用。所以 Java 是一门严格的语言。

或者是？

## 不同类型的懒惰

懒惰可能会落实到语言层面。一个懒惰的语言只会在需要的时候评估一些东西。如果评估取决于外部条件，这可能会产生一些后果。如果要评估的表达式是*引用透明的*，这意味着它不依赖于外部的东西，因此可能不会随时间而改变，评估将在任何时候给出相同的结果。在这种情况下，惰性求值不会改变程序的结果。

这当然适用于像`int x = 2 + 3`这样的情况，但是`int x = getValue(y)`呢？`getValue()`方法可以访问网络来获取值。网络连接可能在某些时间可用，而在其他时间不可用。或者它可以读取可能被另一个线程改变的一些其他计算的结果。

其结果是，如果要计算的表达式是引用透明的，那么惰性计算本身就是引用透明的。在这个例子中，这意味着由`getValue()`方法返回的值应该只依赖于它的参数`y`。(这是一种简化，因为它也可能依赖于一些不可变的外部数据。)

但是除了语言懒惰，你还会遇到*类型懒惰*，如下面这个 Java 例子:

```
Supplier x = () -> getValue(y); 
```

这里，`x`被急切地赋值给一个类型为`Supplier<Integer>`的对象，但是`getValue`方法直到我们需要它的结果并通过调用`Supplier.get`得到它时才被调用。

因此，很明显，除了语言可以是懒惰的或严格的事实之外，我们仍然可以通过使用懒惰类型从懒惰中受益。主要的区别在于，在惰性语言中，惰性`A`将是一个`A`，而在严格语言中，惰性`A`将有另一种类型，与`A`无关(从某种意义上说，我们不能将惰性`A`值赋给类型`A`的引用)。

## 为什么所有语言都是懒惰的

所以很明显，Java 是一种严格的语言，尽管我们能够在类型中实现惰性。但是 Java 真的很严格吗？让我们看看`displayMessage`方法的实现:

```
if (localizedMessage != null && localizedMessage.length() != 0) {
    System.out.println(localizedMessage);
} else {
    System.out.println(defaultMessage);
} 
```

显然，条件指令`if..else`的第一个分支已经被求值，导致本地化的消息被打印到控制台，但是第二个分支还没有。这是幸运的，因为我们无法用一种总是评估条件指令的两个分支的语言做任何事情。这是可能的，因为`if..else`是一个懒惰的构造，只评估必要的分支。

所以 Java 并不是一门“严格”的语言。这种语言在某些领域是严格的，而在另一些领域是懒惰的。所有的语言在某些领域都是懒惰的，因为这是编程的本质:一个程序是一个懒惰的构造，当你运行它时，它会被评估。没有懒惰，就不会有可能的编程。

日常生活中也是如此。没有懒惰，我们就不能计划任何事情。当你写下购物清单时，虽然它是商品清单，但它并没有被评估。想象一下，如果你不马上(“急切地”)去商店买一件东西，就不能把它添加到列表中。这将是非常低效的。

所以这个列表实际上并不是一个商品列表，而是一个程序，在评估的时候最终会产生一个商品列表。在为聚会制作蛋糕的配料清单的情况下，如果聚会被取消了，你可能永远不会评估这个清单。这就是懒惰，等到确定东西真的需要了再去评价，显然是个好习惯。

所以 Java *到底有没有*懒惰。

![](img/9e6e5c0c99977bde882180bf03cce1a2.png)

## Java 什么时候懒了？

Java 有几个懒惰的构造和操作符。我们已经看到了`if ... else`结构是懒惰的。现实中，既严又懒。考虑一般形式:

```
if (<condition>) {
    <branch 1>
} else {
    <branch 2>
} 
```

您会看到条件总是被评估，但是只有一个分支被评估。所以我们可以说`if ... else`对于它的条件是严格的，对于分支是懒惰的。

其他懒惰的 Java 结构是`for`和`while`循环、`switch ... case`和`try ... catch ... finally`。像`if ... else`一样，这些结构对于一些元素是严格的，而对于另一些是懒惰的。

Java 也有一些懒惰的操作符。它们是所谓的“短路”布尔运算符`&&`和`||`以及三元运算符`?:`。考虑下面的例子:

```
String string = getString();
boolean condition = string != null && string.length() > 0; 
```

这里，如果`string`是`null`，就不会调用`length`方法，因为尽管`&&`操作符对其左边的参数很严格，但对右边的参数却很懒惰。

你能想出一种方法来实现同样的事情吗，比如:

```
String string = getString();
boolean condition = and(string != null, string.length() > 0);

boolean and(boolean condition1, boolean condition2) {
    // ?
} 
```

无论`and`方法的实现是什么，它都不会起作用，因为 Java 对方法参数有严格的要求，所以即使`condition1`为假，两个条件都会被计算，如果`string`为`null`，就会导致可能的`NullPointerException`。实现这种方法的唯一方法是改变类型:

```
String string = getString();
boolean condition = and(() -> string != null, () -> string.length() > 0);

boolean and(BooleanSupplier condition1, BooleanSupplier condition2) {
    if (condition1.getAsBoolean()) {
        if (condition2.getAsBoolean()) {
            return true;
        }
    }
    return false;
} 
```

## 懒惰的好处

除了所有语言在某种程度上都需要懒惰之外，在不同的情况下也需要懒惰评估。一种是，正如我们刚刚看到的，某些条件的组合可能会导致错误。在这种情况下，这些条件的延迟评估允许通过不执行程序的某个部分来避免错误。这是一种在使用数据之前经常(尽管可能不够经常)用于测试`null`或其他条件的模式。

懒惰的另一个用例是节省执行时间和处理资源。根据某些条件，我们通常只想执行程序的相关部分。这是大多数控制结构的目的。如果我们最终不需要一个值，我们为什么要花费时间和处理器资源来评估它呢？

懒惰有用的另一个领域是让我们的程序不受影响。这是函数式编程中非常重要的一点，但在其他范式中也会用到。在[函数式编程](https://www.sitepoint.com/what-is-functional-programming/)中，我们试图将效果(意味着与外界的交互)与程序的其余部分完全分离。原因是没有效果的程序更容易设计，更容易测试，也更安全，因为它们是确定性的。

## 懒惰的代价

无论惰性是在语言级别还是在类型级别实现，仍然需要选择惰性表达式的确切求值时间。如果懒惰是在语言层面实现的，那是语言设计者的选择。如果是在类型级别，这是类型设计者的选择，也就是程序员的选择。

我们已经看到，只有在需要值的时候，才会发生惰性评估。这与 Java 的做法相反，例如，方法一收到参数就对其求值。

考虑下面的例子:

```
public static void main(String... args) {
    display(createMessage("Bob"), true);
    display(createMessage("Mark"), false);
}

private static void display(String message, boolean condition) {
    if (condition) {
        System.out.println(message);
    } else {
        System.out.println("Hi!");
    }
}

private static String createMessage(String name) {
    System.out.println("Creating message for " + name);
    return "Hello, " + name;
} 
```

该示例将打印:

```
Creating message for Bob
Hello, Bob
Creating message for Mark
Hi! 
```

我们观察到的是，由于急切的评估，Bob 的消息被计算出来，尽管没有使用该值。所以下面的程序，使用类型懒惰，是更有效的:

```
public static void main(String... args) {
    display(() -> createMessage("Bob"), true);
    display(() -> createMessage("Mark"), false);
}

private static void display(Supplier<String> message, boolean condition) {
    if (condition) {
        System.out.println(message.get());
    } else {
        System.out.println("Hi!");
    }
}

private static String createMessage(String name) {
    System.out.println("Creating message for " + name);
    return "Hello, " + name;
} 
```

我们可以通过运行程序来验证，如果不使用它，它不会创建消息。另一方面，如果该值被多次使用，我们可能会得到相反的结果(就效率而言):

```
public static void main(String... args) {
    display(() -> createMessage("Bob"), true);
    display(() -> createMessage("Mark"), false);
}

private static void display(Supplier<String> message, boolean condition) {
    if (condition) {
        System.out.println(message.get());
        System.out.println(message.get());
        System.out.println(message.get());
    } else {
        System.out.println("Hi!");
        System.out.println("Hi!");
        System.out.println("Hi!");
    }
}

private static String createMessage(String name) {
    System.out.println("Creating message for " + name);
    return "Hello, " + name;
} 
```

现在，结果如下:

```
Creating message for Bob
Hello, Bob
Creating message for Bob
Hello, Bob
Creating message for Bob
Hello, Bob
Hi!
Hi!
Hi! 
```

这表明消息在每次使用时都会被重新构造。这个例子使用了名为 *call by name* evaluation 的方法，这意味着值在被需要之前不会被计算，而是在每次需要时被计算。

当然，由于我们使用的是类型 easy，并且实际上是我们自己在实现 easy，所以很容易解决这个问题，只需存储评估值，这样它就只被评估一次:

```
private static void display(Supplier<String> message, boolean condition) {
    if (condition) {
        String evaluatedMessage = message.get();
        System.out.println(evaluatedMessage);
        System.out.println(evaluatedMessage);
        System.out.println(evaluatedMessage);
    } else {
        // [...]
    }
} 
```

但是我们可能更喜欢将这个过程抽象成懒惰类型。这意味着我们必须创建自己的类型，而不是使用标准的 Java 8 `Supplier`接口。假设我们称这种类型为`Lazy<T>`，我们会这样使用它:

```
display(new Lazy<>(() -> createMessage("Bob")), true); 
```

如果实现得好，它将只在第一次调用时评估消息。这种类型的评估称为*按需调用*评估，它类似于称为*记忆*的函数优化技术，其中函数在第一次遇到参数值时计算与其参数对应的值，并将其存储在缓存中，以便下次需要时能够更快地从缓存中提供。

注意 *call by need* evaluation 也有一些弊端。一如既往，这是一个用一些资源交换另一些资源的问题。在这里，它是在用内存空间换取处理时间。但是与函数记忆化不同，我们只有一个值要缓存，所以通常不需要缓存无效(这是一个巨大的领域，有其自身的多个问题)。

根据需要调用的另一个缺点是，如果程序的结果依赖于评估的副作用，那么它可能会改变程序的结果。如果副作用不是程序的“业务”结果的一部分，例如，如果它记录或计算对一个方法的访问次数，那么这个改变可能是不相关的。但这可能是一个更“商业”的副作用，就像向进度条发送消息，从而修改评估的整体结果。

然而，如果我们参与函数式编程，这不是我们所关心的，因为在函数式编程中，函数和效果从来不会混淆。事实上，纯函数式程序的组织方式是效果发生在程序之外。

懒惰还可能影响依赖共享可变状态的程序的结果，因为它可能导致执行线程运行得更快，将访问顺序更改为可变状态。再一次，这不是函数式程序的问题，因为函数式程序不会以一种可能改变程序结果的方式共享可变状态。

但是*按需调用*的主要问题是每次调用时值应该何时改变。这显然不适用于赋值给引用的表达式值，但可能适用于递归方法调用，在递归方法调用中，值可能会随着每一步而变化。使用懒惰的*按需调用*求值，参数将在第一步求值，而不会在每个后续的递归步骤中重新求值。如果停止递归的条件是基于这个参数的值，递归将永远不会干净地停止。

## 让不可能成为可能

我们已经看到，通过避免无用的计算，懒惰可能被用来使程序更有效。在某些情况下，它甚至可以使一些原本不可能的计算成为可能，因为它们永远不会终止。

例如，考虑下面的程序:

*   以正整数列表为例
*   过滤质数
*   获取前一百个元素，并将结果显示到控制台。

这个程序将显示前一百个质数。没有懒惰，这将是不可能的，因为第二步将永远不会完成，因为正整数的列表是无限的。如果没有“正整数列表”的惰性表示，第一步也没有任何可计算的意义。

显然，我们不仅需要惰性标量类型(用于对单个值进行惰性操作)的强大实现，还需要惰性矢量类型(或惰性集合；用于对多个值进行操作)。这将是未来文章的主题。

## 摘要

我们探讨了懒惰如何适用于编程语言:

*   严格语言在事物被声明时对其进行评估；懒惰的语言在使用的时候会评估事物。
*   所有的编程语言在某种程度上都是懒惰的，因为程序的某些部分是不求值的，这取决于一些条件。
*   Java 被认为是一种严格的语言，因为它一声明就计算引用和方法参数，即使它们没有被使用。

然后我们发现了一些关于懒惰的更深层次的事实:

*   懒惰可能在语言级别(由语言设计者)或类型级别(由程序员)实现。
*   当涉及到懒惰时，引用透明性是非常重要的，因为它保证了表达式求值的结果在每次求值时都是相同的。
*   惰性求值可能在每次使用表达式值时发生(*按名称调用*)或者仅在第一次使用时发生，结果被缓存以便在需要时再次使用(*按需要调用*)
*   当不需要某个表达式的值时，懒惰会节省处理资源。另一方面，如果实现为通过名称调用*，并且一些值需要多次使用，则可能会消耗一些额外的处理资源。懒惰也使得处理无限的数据集合成为可能。*

## 分享这篇文章