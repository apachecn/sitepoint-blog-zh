# 可选如何打破单子定律，为什么它很重要

> 原文：<https://www.sitepoint.com/how-optional-breaks-the-monad-laws-and-why-it-matters/>

Java 8 给我们带来了 lambdas 和 streams，两者都是期待已久的特性。随之而来的是`Optional`来避免`NullPointerExceptions`出现在可能不返回元素的流管道的末端。在其他语言中，像`Optional`这样的可能包含或可能不包含值的类型是行为良好的*单子*——但在 Java 中却不是。这对我们这样的普通开发人员来说很重要！

## 介绍`java.util.Optional`

[`Optional<T>`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) 被描述为“一个可能包含也可能不包含非空值的容器对象”。这很好地概括了你应该从中期待什么。

它有一些有用的方法，如:

*   `of(x)`，它允许在一个值`x`上创建一个`Optional`容器包装。
*   当且仅当 containPer 对象包含非空值时，返回`true`。

再加上一些稍微不太有用(或者稍微有点危险)的，比如`get()`，它返回包含的值(如果存在的话)，并在调用空的`Optional`时抛出一个异常。

根据值的存在，还有其他表现不同的方法:

*   `orElse(v)`返回包含的值(如果有),或者默认情况下，如果容器为空，则返回`v`。
*   当且仅当有值时，执行代码块。

奇怪的是，你可以看到[在它的类描述](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)中没有提到像`map`、`flatMap`甚至`filter`这样的方法。它们都可以用来进一步处理由`Optional`包装的值。(除非为空:那么函数不会被调用，并且`Optional`保持为空)。他们的省略可能与这样一个事实有关，即在库创建者的意图中，`Optional`不应该是*单子*。

## 后退一步:单子

呀！我可以想象当你们读到这个名字时，有些人会嗤之以鼻。

对于那些还没有享受到这种乐趣的人，我将试着总结一个关于这个难以捉摸的概念的介绍。被劝告并且半信半疑地采取下面的线！用道格拉斯·克洛克福特的[定义](https://www.youtube.com/watch?v=dkZFtimgAcM)、*单子*是“一旦开发者真的设法理解了，立刻就失去了向任何人解释的能力”。

我们可以将*单子*定义为:

*   一个参数化的类型`M<T>`:用 Java 术语来说就是`public class M<T>`。

*   一个*单元*函数，这是一个从元素`public <T> M<T> unit(T element)`生成*单子*的工厂函数。

*   一个*绑定*操作，该方法采用一个*单子*以及一个将元素映射到*单子*的函数，并将应用该函数的结果返回给包装在*单子*中的值:

    ```
    public static <T, U> M<U> bind(M<T> monad, Function<T, M<U>> f) {
        return f.apply(monad.wrappedValue());
    } 
    ```

这就是关于*单子*的全部知识吗？不完全是，但是现在已经足够了。如果你想阅读更多关于这个主题的内容，请随意查看文章末尾的推荐读物。

## `Optional`是单子吗？

是也不是。差不多。肯定有可能。

`Optional` *本身* [使](http://mail.openjdk.java.net/pipermail/lambda-dev/2013-February/008314.html)有资格成为*单子*，尽管[在 *Java 8* 库团队中有些抵触](http://mail.openjdk.java.net/pipermail/lambda-dev/2013-February/008305.html)。让我们看看它如何符合上述 3 个属性:

*   `M<T>`就是`Optional<T>`。
*   单元功能是`Optional.ofNullable`。
*   绑定操作是`Optional.flatMap`。

这样看来，`Optional`确实是一个*单子*，对吗？没那么快。

## 单子定律

任何一个阶级，要真正成为一个单子，都必须遵守三条法则:

1.  *左恒等式*，将单位函数应用到一个值上，然后将得到的*单子*绑定到函数`f`上，与对同一个值调用`f`是一样的:让`f`是一个返回一个*单子*的函数，然后`bind(unit(value), f) === f(value)`。
2.  *右恒等式*，将单元函数绑定到一个*单子*并不会改变*单子*:让`m`是一个单子值(一个`M<T>`的实例)，然后`bind(m, unit) === m`。
3.  *结合律*，如果我们有一个一元函数应用链，它们是如何嵌套的并不重要:`bind(bind(m, f), g) === bind(m, x -> g(f(x)))`。

左右恒等式都保证将一个*单子*应用于一个值将会包装它:这个值不会改变，T2 单子也不会改变。最后一个法则保证一元合成是结合的。所有的法则一起使代码更有弹性，防止反直觉的程序行为，这种行为取决于你如何以及何时创建*单子*，以及你如何以及以何种顺序组成你将用来映射*单子*的函数。

## `Optional`和单子定律

现在，你可以想象，问题是:`Optional<T>`有这些属性吗？

让我们通过检查属性 1，*左标识*来了解一下:

```
Function<Integer, Optional<Integer>> f = x -> {
    if (x == null) {
        x = -1;
    } else if (x == 2) {
        x = null;
    } else {
        x = x + 1;
    }
    return Optional.ofNullable(x);
};
// true, Optional[2] === Optional[2]
Optional.of(1).flatMap(f).equals(f.apply(1));
// true, Optional.empty === Optional.empty
Optional.of(2).flatMap(f).equals(f.apply(2)); 
```

这适用于空结果和非空结果。用`null`喂两边怎么样？

```
// false
Optional.ofNullable((Integer) null).flatMap(f).equals(f.apply(null)); 
```

这有点出乎意料。让我们看看会发生什么:

```
// prints "Optional.empty"
System.out.println(Optional.ofNullable((Integer) null).flatMap(f));     
// prints "Optional[-1]"
System.out.println(f.apply(null)); 
```

所以，总而言之，`Optional`到底是不是*单子*？严格地说，它不是一个行为良好的*单子*，因为它不遵守*单子*法则。然而，因为它确实满足了单子的定义，尽管它有一些错误的方法。

### `Optional::map`和结合律

如果你认为我们在`flatMap`上运气不好，那就等着看`map`会发生什么吧。

当我们使用`Optional.map`时，`null`也被映射成`Optional.empty`。假设我们将第一次映射的结果再次映射到另一个函数中。那么当第一个函数返回`null`时，第二个函数根本不会被调用。相反，如果我们将初始的`Optional`映射到两个函数的组合中，结果会非常不同。看看这个例子来阐明:

```
Function<Integer, Integer> f = x -> (x % 2 == 0) ? null : x;
Function<Integer, String > g = y -> y == null ? "no value" : y.toString();

Optional<Integer> opt = Optional.of(2);  // A value that f maps to null - this breaks .map

opt.map(f).map(g);                      // Optional.empty
opt.map(f.andThen(g));      // "no value" 
```

通过组合函数`f`和`g`(使用方便的 [`Function::andThen`](https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html#andThen-java.util.function.Function-) )我们得到了一个不同于我们逐个应用它们时得到的结果。一个更明显的例子是，如果参数是`null`，第一个函数返回`null`，第二个函数抛出`NullPointerException`。然后，重复的`map`工作正常，因为第二个方法从未被调用，但是组合抛出了异常。

所以，`Optional::map`打破了结合律。这甚至比`flatMap`违反左同一律还要糟糕(我们将在下一节回到这个问题)。

### `orElse`去救援？

你可能认为如果我们使用`orElse`会变得更好。除了[它没有](https://github.com/mlarocca/sitepoint/blob/master/Java/Optional/Test.java#L104)。

创建一个包含两个以上函数的链是很容易的，在不同的阶段获取`null`会导致不同的结果。不幸的是，在链的末端，我们没有办法告诉我们`null`是在哪里被第一次处理的。因此，当应用`orElse`时，无法提供正确的结果。更抽象地说，甚至更糟的是，通过使用`orElse`，我们将依赖于这样一个事实，即维护我们代码的每个开发人员，或者使用我们库的每个客户，都会坚持我们的选择，继续使用`orElse`。

## `Optional`有什么蹊跷？

问题是，按照设计，非空的`Optional`不能容纳`null`。毕竟，你可能会合理地反对它旨在摆脱`null`:事实上`Optional.of(null)`将抛出一个`NullPointerException`。当然`null`值仍然很常见，所以引入`ofNullable`是为了避免我们在代码中重复相同的 if-null-then- `empty` -else- `of`检查。然而——这是所有邪恶的本质——`Optional.ofNullable(null)`被翻译成`Optional.empty`。

最终结果是，如上所示，以下两种情况会导致不同的结果:

*   在将值包装到`Optional`之前应用函数；
*   首先将值包装到一个`Optional`中，然后将其映射到同一个函数中。

这听起来很糟糕:这意味着我们应用函数的顺序很重要。当我们使用`map`时，正如我们看到的，情况变得更糟，因为我们也失去了结合不变性，甚至函数的组成方式也很重要。

反过来，这些问题使得在重构过程中添加 bug 不仅可能，甚至容易得令人害怕。

## 真实世界的例子

现在，这可能看起来像是一个专门用*制造麻烦的例子。不是的。只需将`f`替换为`Map::get`(当指定的键不包含在映射*或*中时，返回`null`，如果它映射到值`null`)并将`g`替换为任何应该处理和转换`null`的函数。*

这里有一个更接近真实世界应用的例子。首先，让我们定义几个实用程序类:

*   `Account`，用 ID 和余额建模银行账户；
*   `Balance`，建模一个(金额，货币)对；
*   `Currency`，一个集合了一些最常见货币常量的枚举。

你可以在 GitHub 上找到这个例子[的完整代码。明确地说，这绝不是这个类的合适设计:我们做了很大的简化，只是为了让这个例子更清晰、更容易呈现。](https://github.com/mlarocca/sitepoint/blob/master/Java/Optional/Test.java)

现在让我们假设一个银行被表示为一个账户的集合，存储在一个`Map`中，将账户 id 链接到实例。让我们定义几个实用函数，从账户 ID 开始检索账户的美元余额。

```
Map<Long, Account> bank = new HashMap<>();

Function<Long, Account> findAccount = id -> bank.get(id);
Function<Account, Balance> extractBalance = account -> account != null
        ? account.getBalance()
        : new Balance(0., Currency.DOLLAR);
Function<Balance, Double> toDollars = balance -> {
    if (balance == null) {
            return 0.;
    }
    switch (balance.getCurrency()) {
        case DOLLAR: return balance.getAmount();
        case POUND: return balance.getAmount() * 1.3;
        case EURO: return balance.getAmount() * 1.1;
        default: return 0.;
    }
}; 
```

我们现在可以看到这三个函数的单独映射与它们的组合有什么不同。让我们考虑几个不同的情况，我们从一个包装在`Optional`中的账户 id 开始，并将其映射到该账户的美元金额。

```
Optional<Long> accountId3 = Optional.of(3L);
Optional<Long> accountId4 = Optional.of(4L);
Optional<Long> accountId5 = Optional.of(5L);

bank.put(4L, null);
bank.put(5L, new Account(5L, null)); 
```

对于一个空的`Optional`和一个用适当的余额包装非`null`帐户 ID 的非空的，我们是否一个接一个地映射我们的函数或者我们是否使用它们的组合都无关紧要，输出是相同的。为了简洁起见，我们在这里省略了这些案例，但是可以随意查看一下[回购](https://github.com/mlarocca/sitepoint/blob/master/Java/Optional/Test.java)。

相反，让我们尝试一下帐户 ID 不在地图中的情况:

```
accountId3.map(findAccount)
        .map(extractBalance)
        .map(toDollars)
        .ifPresent(System.out::println);  // Optional.empty
accountId3.map(findAccount
        .andThen(extractBalance)
        .andThen(toDollars))
        .ifPresent(System.out::println); // 0.0 
```

在这种情况下，`findAccount`返回`null`，它被映射到`Optional.empty`。这意味着当我们单独映射我们的函数时，`extractBalance`将永远不会被调用，所以最终结果将是`Optional.empty`。

另一方面，如果我们组成了`findAccount`和`extractBalance`，后者被称为`null`作为它的参数。因为该函数“知道”如何处理`null`值，所以它产生一个非空输出，该输出将被链下游的`toDollars`正确处理。

因此，这里我们有两个不同的结果，只取决于相同的函数，以相同的顺序，应用于我们的输入。哇！

如果我们在映射中为一个 ID 存储`null`，或者如果账户的余额是`null`，也会发生同样的事情，因为`toDollars`被类似地处理`null`。查看[回购](https://github.com/mlarocca/sitepoint/blob/master/Java/Optional/Test.java#L49)了解更多详情。

## 实际影响

除了关于`Optional`性质的理论争议外，`Optional::map`和`Optional::flatMap`违反单子定律的事实还有很多实际后果。这反过来又阻止了我们自由地应用函数组合，因为如果我们一个接一个地应用两个函数，或者直接应用它们的组合，我们会得到相同的结果。

这意味着我们不能再自由地重构我们的代码，并确保结果不会改变:可怕的后果可能不仅出现在您的代码库中，而且——甚至更糟——出现在您客户的代码中。在重组你的代码之前，你需要知道在每个人的代码中任何地方使用的*函数是否处理`null`，否则你可能会引入错误。*

 *![handcuffs](img/f7ee33eeabb0bef1c8b14b8c4359fa5f.png)

## 可能的修复

我们有两个主要的选择来纠正这个错误:

*   不要用`Optional`。
*   接受现实吧。

让我们来详细地看看它们中的每一个。

### 不要使用`Optional`

嗯，这看起来像是 [Tanenbaum](http://www.cs.rpi.edu/academics/courses/fall04/os/c10/index.html) 提到的解决死锁的鸵鸟算法。或者因为 JavaScript 的缺陷而忽略它。

一些 [Java 开发者](http://huguesjohnson.com/programming/java/java8optional.html)明确反对`Optional`，支持继续使用`null`。如果你不在乎转向更干净、更少错误的编程风格，你当然可以这么做。**TL；博士** : `Optional`允许你通过*单子*处理一些输入可能存在也可能不存在的工作流，这意味着以一种更模块化和更干净的方式。

### 接受现实吧

Optional 在一些情况下违反了单子定律，对此无能为力。但是我们可以从得出这个结论的方法中学到一些东西:

*   当用一个可能的`null`值和一个返回`Optional`的函数开始一个链时，请注意将函数应用于该值可能会导致与首先创建`Optional`然后平面映射该函数不同的结果。这反过来会导致函数不能被调用，如果你依赖于它的站点效果，这就是一个问题。
*   当组成`map`链时，要注意的是，虽然单个函数从未被用`null`调用，但是合并的函数可能会产生`null`作为中间结果，并将其传递给函数的非空安全部分，从而导致异常。
*   当将一个`map`分解成几个时，要注意的是，虽然最初的函数是作为一个整体执行的，但是如果前一个部分产生了`null`，那么它的一部分可能会以永远不会被调用的函数结束。这是一个问题，如果你依赖这些部分的网站效果。

根据经验，更喜欢`flatMap`而不是`map`，因为前者遵守结合律，而`map`不遵守，并且打破结合律比打破左恒等式更容易出错。总而言之，最好不要把`Optional`看作一个承诺容易组合的单子，而是一种避免`null`作为(平面)映射函数的参数出现的方法。

我已经想出了一些可能的方法来使代码更加健壮，可以在 [GitHub](https://github.com/mlarocca/sitepoint/blob/master/Java/Optional/Test.java#L76) 上随意查看

## 结论和进一步阅读

我们已经看到，跨`flatMap`和`map`链的某些重构会导致代码改变其行为。根本原因是`Optional` [被设计成](http://blog.codefx.org/java/dev/design-optional/)以避免陷入 NPEs，并且为了达到这个目的，它将`null`转换成`empty`。这反过来意味着这样的链并没有完全执行，而是在中间操作产生`empty`时结束。

当依赖于潜在的未执行功能所导致的站点效应时，记住这一点是很重要的。

如果您想进一步了解本文中的一些主题:

*   【Java 8 第四部分怎么了
*   [更多关于可选被破](https://developer.atlassian.com/blog/2015/08/optional-broken/)
*   [可选的设计](http://blog.codefx.org/java/dev/design-optional/)
*   [可选为良好实践](http://blog.codefx.org/techniques/intention-revealing-code-java-8-optional/)
*   [为什么要进行函数式编程](https://www.cs.kent.ac.uk/people/staff/dat/miranda/whyfp90.pdf)
*   [单子基础知识](https://en.wikipedia.org/wiki/Monad_(functional_programming))
*   [埃里克·梅耶尔论单子](https://www.infoq.com/interviews/meijer-monads)
*   [更多单子(从哈斯克尔的观点来看)](http://learnyouahaskell.com/a-fistful-of-monads)
*   对单子的另一种看法
*   [道格拉斯·克洛克福特，单子和生殖腺](https://www.youtube.com/watch?v=dkZFtimgAcM)

## 分享这篇文章*