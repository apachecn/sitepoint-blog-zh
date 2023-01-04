# 什么是参照透明性？

> 原文：<https://www.sitepoint.com/what-is-referential-transparency/>

### 目录 

*   [参照透明度](#referentialtransparency)
*   [数学中的参照透明性](#referentialtransparencyinmaths)
*   [编程中的参照透明性](#referentialtransparencyinprogramming)
*   [命令式编程中的引用透明性](#referentialtransparencyinimperativeprogramming)
*   [总结](#summary)
*   [评论](#comments)

在函数式编程中，*引用透明性*通常被定义为这样一个事实:在程序中，一个表达式可以被它的值(或任何具有相同值的东西)所代替，而不会改变程序的结果。这意味着方法应该总是为给定的参数返回相同的值，而没有任何其他影响。不过，这种函数式编程概念也适用于命令式编程，可以帮助您使代码更加清晰。

要理解这篇文章，你应该知道如何用 Java 编写简单的[类](https://docs.oracle.com/javase/tutorial/java/javaOO/classes.html)和[方法](https://docs.oracle.com/javase/tutorial/java/javaOO/methods.html)。知道什么是[副作用](http://softwareengineering.stackexchange.com/a/40314)是有帮助的，但是如果你不知道，你会在途中学到。

## 对透明性有关的

参照透明性这个词被用在不同的领域，比如数学、逻辑、语言学、哲学和编程。在这些领域中，它有着完全不同的含义。这里，我们将只讨论计算机程序，尽管我们将展示与数学的类比(简单的数学，别担心)。然而，请注意，计算机科学家对编程中的*引用透明性*的含义并不一致。我们要看的是函数式程序员使用的引用透明性。

### 数学中的参照透明性

在数学中，引用透明性是表达式的一种属性，可以被具有相同值的其他表达式替换，而不会以任何方式改变结果。考虑下面的例子:

```
x = 2 + (3 * 4) 
```

我们可以用具有相同值的任何其他表达式替换子表达式`(3 * 4)`，而不改变结果(值`x`)。最明显的表达方式当然是`12`:

```
x = 2 + 12 
```

可以使用具有值`12`(可能是`(5 + 7)`)的任何其他表达式，而不改变结果。因此，子表达式`(3 * 4)`是引用透明的。

我们也可以用另一个具有相同值的表达式替换表达式`2 + 12`，而不改变`x`的值，因此它也是引用透明的:

```
x = 14 
```

您可以很容易地看到引用透明的好处:它允许推理。没有它，我们就无法在不考虑其他元素的情况下解析任何表达式。

### 编程中的引用透明性

在编程中，引用透明性适用于程序。因为程序是由子程序组成的，子程序本身就是程序，所以它也适用于那些子程序。子程序尤其可以用方法来表示。这意味着方法可以是引用透明的，如果对此方法的调用可以被其返回值替换，就是这种情况:

```
int add(int a, int b) {
    return a + b
}

int mult(int a, int b) {
    return a * b;
}

int x = add(2, mult(3, 4)); 
```

在这个例子中，`mult`方法是引用透明的，因为对它的任何调用都可以用相应的返回值替换。这可以通过用`12`替换`mult(3, 4)`来观察:

```
int x = add(2, 12) 
```

同样，`add(2, 12)`可以用相应的返回值`14`代替:

```
int x = 14; 
```

无论程序做什么，这些替换都不会改变程序的结果。注意，我们可以使用具有相同值的任何其他表达式，这在重构时很有用。

另一方面，考虑下面的程序:

```
int add(int a, int b) {
    int result = a + b;
    System.out.println("Returning " + result);
    return result;
} 
```

用相应的返回值替换对`add`方法的调用将改变程序的结果，因为消息将不再被打印。在这种情况下，它只会消除副作用，但在其他情况下，它可能会更改方法返回的值:

```
public static void main(String... args) {
    printFibs(10);
}

public static void printFibs(int limit) {
    Fibs fibs = new Fibs();
    for (int i = 0; i < limit; i++) {
        System.out.println(fibs.next());
    }
}

static class Fibs {
    private int previous = -1;
    private int last = 1;

    public Integer next() {
        last = previous + (previous = last);
        return previous + last;
    }
} 
```

在这里，`next`方法不能被具有相同值的任何东西替换，因为该方法被设计为在每次调用时返回不同的值。

使用这种非引用透明的方法需要很强的纪律性，以便不共享计算中涉及的可变状态。函数式风格避免了这种方法，而支持引用透明的版本。

### 命令式编程中的引用透明性

命令式和函数式编程都使用函数。尽管函数式编程只使用*函数，命令式编程使用:*

*   纯函数:返回值并且没有其他效果的方法
*   纯效果:方法什么也不返回，只是改变了它们之外的一些东西)
*   有副作用的函数:既返回值又改变某些东西的方法

众所周知，避免使用有副作用的函数是一种很好的做法。这就给命令式程序员留下了纯粹的功能和纯粹的效果。参照透明性是命令式程序员的一个强有力的工具，使他们的程序更容易推理，更容易测试。

![A snail reaching for referential transparency](img/b4dc68414a87d25d6036606946d6da04.png)

## 摘要

引用透明意味着一个函数调用可以被它的值或者另一个具有相同结果的引用透明调用所替代。它使得对程序的推理更加容易。它还使每个子程序独立，这大大简化了单元测试和重构。另一个好处是，引用透明的程序更容易阅读和理解，这也是函数式程序需要更少注释的原因之一。

要了解更多相关信息，您可以查看以下文档:

*   [参照透明度](http://www.dictionary.com/browse/referential-transparency)
*   什么是参照透明性？
*   [为什么他们称之为:参照透明](http://www.nobugs.org/blog/archives/2008/11/12/why-do-they-call-it-referentially-transparent/)
*   [蒯因:参照透明和不透明](https://faculty.washington.edu/smcohen/453/Quine.pdf)

## 分享这篇文章