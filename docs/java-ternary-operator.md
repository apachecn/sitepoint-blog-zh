# Java 的三元运算符三分钟

> 原文：<https://www.sitepoint.com/java-ternary-operator/>

### 目录 

*   [三元运算符](#ternaryoperator)
*   [语法](#syntax)
*   [例题](#examples)
*   [嵌套多个运算符](#nestingmultipleoperators)
*   [总结](#summary)
*   [评论](#comments)

三元运算符是用于`if` -then- `else`语句的[语法糖](https://en.wikipedia.org/wiki/Syntactic_sugar "Syntactic Sugar")的一种形式。它也被称为条件运算符，这可能是一个更有意义的名字，因为它像`if`一样计算条件。只要小心使用操作符，它可以在不牺牲可读性的情况下使代码更加简洁。

这篇文章要求你对 Java 中的[`if`-语句如何工作有一个坚实的理解。](https://www.sitepoint.com/javas-if-statement-tutorial/ "Java’s If Statement in Five Minutes")

## 三元运算符

*三元运算符*计算一个条件，并选择两个分支中的一个来执行。像许多其他的[编程语言](https://en.wikipedia.org/wiki/%3F: "Ternary Operators - ?:")、`?`和`:`符号被用来组成它。您可能会遇到的引用相同概念的其他常见名称有 *inline if* 、*三元 if* 和*条件运算符*。

### 句法

*三元*这个名字指的是运算符取三个操作数。

```
condition ? exprTrue : exprFalse 
```

`condition`是一个[布尔表达式](https://www.sitepoint.com/boolean-data-type/)，其计算结果为`true`或`false`。两者，`exprTrue`和`exprFalse`也是表达式，但是它们可以评估你想要它们评估的任何东西([除了`void`](https://docs.oracle.com/javase/specs/jls/se7/html/jls-15.html#jls-15.25) )。如果条件是`true`，三元运算符计算`exprTrue`。否则评估`exprFalse`。

三元运算符是一个表达式(比如像`price + 20`)，这意味着一旦执行，它就有一个值。并且该值需要进一步使用，例如通过将它赋给变量或从方法返回，否则表达式将无法编译。

值得一提的是，运算符在只计算使用过的表达式的意义上是[懒惰的](https://www.sitepoint.com/java-in-praise-of-laziness/):三元运算符不会计算未使用的分支。

## 例子

正如您所看到的，基本结构非常类似于一个`if` -then- `else`语句，但是它被压缩为一行。我们来看一个具体的例子。它演示了如何用三元运算符替换简单的`if` -then- `else`语句。

```
int price;
if (isPremiumMember()) {
    price = 80;
}
else {
    price = 100;
}

// is equivalent to

int price = isPremiumMember() ? 80 : 100; 
```

`price`变量根据用户是否是高级会员获得一个值。如您所见，三元运算符简洁明了，在这种情况下提高了可读性。

因为您可以将它作为一个表达式来使用，所以通常它使您能够通过用一个表达式替换它们来删除一个方法中的多个 return 语句。

```
int price() {
    if (isPremiumMember()) {
        return 80;
    }
    else {
        return 100;
    }
} 
```

可以替换为

```
int price() {
    return isPremiumMember() ? 80 : 100;
} 
```

重构后的`price`方法比以前短得多，而且清晰易读。

### 嵌套多个运算符

Java 允许将一个条件操作符嵌套到另一个操作符中。不过做那件事的时候要小心。嵌套的条件运算符会影响可读性。

```
int amount = 10;
String result = amount <= 2 ? "couple" : amount > 2 && amount < 5 ? "few" : amount <= 5 ? "several" : "many"; 
```

虽然这个例子可以通过格式化来改进，但有时最好避免使用三元运算符，而采用普通的老式 [`if` -then- `else`](https://www.sitepoint.com/javas-if-statement-tutorial/ "Java’s If Statement in Five Minutes") 或 [switch 语句](https://www.sitepoint.com/javas-switch-statement/ "Java’s Switch Statement in Three Minutes")路线。

![The ternary operator consists of three elements](img/de0fe9219059f94ef87421ee32343e47.png)

## 摘要

您已经了解了三元运算符允许您将一个`if` -then- `else`语句缩短为一行`condition ? exprTrue : exprFalse`。如果做得明智，它会使代码更加简洁，并提高可读性。

虽然代码行越少并不总是越好。很容易过度使用三元运算符，[会降低代码的可读性](http://softwareengineering.stackexchange.com/a/28315)。运用常识，记住[程序必须写给人们阅读，只是顺便让机器执行](https://www.goodreads.com/quotes/9168-programs-must-be-written-for-people-to-read-and-only "Harold Abelson, Structure and Interpretation of Computer Programs")。一种更易读的表达条件的方式，特别是当有很多条件时，可以是 [switch 语句](https://www.sitepoint.com/javas-switch-statement/ "Java’s Switch Statement in Three Minutes")。

## 分享这篇文章