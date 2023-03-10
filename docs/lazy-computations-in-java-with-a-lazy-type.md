# 具有惰性类型的 Java 中的惰性计算

> 原文：<https://www.sitepoint.com/lazy-computations-in-java-with-a-lazy-type/>

### 目录 

*   [抽象计算环境](#abstractingcomputationalcontexts)
*   一种简单的懒惰分类方法
*   [使用现有类型](#usinganexistingtype)
*   [构成懒惰类型](#composinglazytypes)
*   [设计先进的懒惰型](#designinganadvancedlazytype)
*   [延迟应用功能](#lazilyapplyingfunctions)
*   [映射返回懒惰类型的函数](#mappingafunctionreturningalazytype)
*   [将值放入上下文中](#puttingavalueincontext)
*   [抽象行为](#abstractingbehavior)
*   [处理特效的更好方法](#abetterwaytohandleeffects)
*   [记忆评估结果](#memoizingtheresultoftheevaluation)
*   [总结](#summary)
*   [评论](#comments)

> 我选择了一个懒惰的人去做一件艰难的工作。
> 因为一个懒惰的人会找到一个简单的方法去做。――比尔·盖茨

据说 Java 是一种严格的语言，这意味着表达式一被声明就被急切地求值，这与懒惰语言相反，懒惰语言只在需要表达式的值时才求值。这种差异很重要，因为根据某些条件，有些表达式值可能永远都不需要。

惰性求值通过避免不必要的计算来节省计算资源。懒惰使得操纵计算中不可能使用的数据成为可能，比如无限列表，或者溢出可用内存的有限数据，比如巨大的文件。因此，即使在本质上严格的语言中，如 Java，能够使用惰性求值也是很重要的。在这样的语言中，我们需要学习如何实现懒惰类型，这就是我们在本文中要做的。

在[之前的一篇文章](https://sitepoint.com/java-in-praise-of-laziness)中，我解释了*懒惰*是什么意思。我谈到了懒惰可能在哪里实现(在语言级别或在类型级别)，以及何时评估最终会发生。我们看到，*通过名称*调用导致每次需要一个值时进行评估，而*通过需要调用*包括在第一次需要时评估该值并存储它以备以后再次需要。我们还看到，在某个值最终不再需要的情况下，懒惰允许节省处理资源。但是还有更多。

## 抽象计算环境

懒惰不仅仅是节约资源的一种模式。这是一个可以应用于任何表达式的计算上下文。从这个意义上说，并不是要反对严格性(表达式一定义就进行求值的事实)。就是要反对断章取义的表达。表达式可以脱离上下文使用，也可以在上下文中使用。当然，有人会说所有的表达式都是在程序的上下文中定义的。因此，假设某些表达式可能是在附加的上下文层中定义的。由于所有表达式都是在程序的上下文中定义的(就我们而言)，我们将忘记这个“顶层”上下文，只考虑附加的上下文层。

懒惰是一个特定的语境。当一个表达式被放在这个上下文中时，它仅仅意味着它可能还没有被求值。此外，把它放在这个环境中会阻止评估，直到我们需要它发生。注意，我们可能会将一个已经求值过的表达式放入惰性上下文中。这当然不会改变任何关于求值的东西，但是组合表达式可能是有用的，我们很快就会看到。

还有很多其他可能的计算环境。我们可以处理需要很长时间来评估的表达式。这些表达式可以放在一个不同的上下文中，在这个上下文中求值会立即开始，但是允许我们在求值完成之前操作它们。或者我们可以将表达式放在特定的上下文中，允许我们透明地处理错误情况。在这种情况下，表达式要么根据结果求值，要么根据错误求值。但是如果一个评估需要很长时间并且可能产生错误呢？嗯，我们可以为此定义一个特定的上下文，但是我们可以更好地组合前两个上下文。组合不同类型的上下文是一个有趣的问题。但是暂时我们只会考虑把同一个上下文的几个实例组合起来，意思是组合懒惰。但在看这个之前，我们必须先实现*型懒惰*。

## 一种简单的懒惰分类方法

程序员总是知道如何在他们需要的地方实现最小的懒惰。实现惰性属性的最简单方法可能如下:

```
private String message;

public String getMessage() {
    if (message == null) {
        message = constructMessage();
    }
    return message;
} 
```

在这个例子中，`message`属性在我们第一次调用它的 getter 时被延迟计算。`constructMessage`方法被“懒惰地”调用，但这不是因为方法调用本身，而是因为它发生在一个`if`条件结构中，这是懒惰的。不幸的是，我们不能将这种技术用于方法参数，因为一旦我们将`message`属性用作方法参数，它就会被求值，即使我们不使用值:

```
public String message;

public String getMessage() {
    if (message == null) {
        message = constructMessage();
    }
    return message;
}

private String constructMessage() {
    System.out.println("Evaluating message");
    return "Message";
}

public void doNothingWith(String string) {
    // do nothing
}

public void testLaziness() {
    doNothingWith(getMessage());
} 
```

这个例子将在控制台上打印出`Evaluating message`，尽管我们从来没有使用结果值。我们能做得更好吗？

### 使用现有类型

是的，我们可以。我们总是可以的，但是现在有了 lambdas 和方法引用就容易多了。在 Java 8 之前，我们可以编写:

```
public void doNothingWith(Supplier<String> string) {
    // do nothing
}

public void testLaziness() {
    doNothingWith((new Supplier<String>() {
        @Override
        public String get() {
            return getMessage();
        }
    }));
} 
```

当然，由于`Supplier`接口不存在，我们必须创建它，这相当容易:

```
public interface Supplier<T> {

    T get();

} 
```

有了 Java 8，我们可以使用标准的`java.util.function.Supplier`接口和一个 lambda:

```
public void testLaziness() {
    doNothingWith(() -> getMessage());
} 
```

或者更好，我们可以用一个方法引用来代替 lambda:

```
public void testLaziness() {
    doNothingWith(this::getMessage);
} 
```

请注意，这些示例并不等同。使用匿名类将导致创建该类的一个实例。相反，使用 lambda 不会导致对象的创建，而只会导致方法的创建。如果使用方法引用，甚至不会创建任何方法，因为被引用的方法将被简单地调用。这在效率方面有一定的重要性，但不是唯一的。从程序员的角度来看，主要的结果是在第一种情况下,`this`引用将引用匿名类，而在 lambda 或方法引用的情况下将引用封闭类。

### 编写懒惰类型

我们通过使用`Supplier`获得的懒惰是有用的，但我们可以让它变得更强大。假设我们有两个想要连接的惰性字符串:

```
private String constructMessage(
        Supplier<String> greetings, Supplier<String> name) {
    return greetings.get() + name.get();
} 
```

如果我们被迫评估表达式来组成它们，懒惰有什么好处？如果我们确实知道我们将需要合成的结果，那么在合成表达式之前评估它们是有意义的。但是我们可能想要组合两个惰性表达式并得到一个惰性结果。这意味着我们想要在上下文中组合值，而不必脱离上下文。为此，我们只需将组合包装在一个新的*惰性*上下文实例中，这意味着一个`Supplier`:

```
private Supplier<String> constructMessage(
        Supplier<String> greetings, Supplier<String> name) {
    return () -> greetings.get() + name.get();
} 
```

当然，我们必须改变返回值的类型。我们不是返回合成值的结果，而是返回一个程序，该程序在执行时(意味着调用`Supplier.get()`时)，将评估两个字符串并合成它们以创建预期的结果。这就是懒惰:小程序包装表达式，这些表达式将在这些程序执行时被求值。

## 设计高级懒惰类型

这是一个好的开始，因为它已经比简单的懒惰更强大，但我们可以做得更好。在我们调用`get`方法之前，`Supplier`接口只能延迟求值。它本身不允许合成懒惰值。如果`get`方法抛出异常怎么办？如果我们想对这个懒惰值应用一个函数而不引起求值呢？我们可以在外部处理所有这些问题，但是由于 Java 是一种面向对象的语言，我们应该将这项工作委托给懒惰类型本身。我们可以从创建自己的界面开始:

```
@FunctionalInterface
public interface Lazy<A> {

    A get();

} 
```

我们可能希望能够在需要`Supplier`的地方使用我们的接口。为了使这成为可能，我们所要做的就是扩展`Supplier`:

```
@FunctionalInterface
public interface Lazy<A> extends Supplier<A> { } 
```

### 延迟应用功能

作为第一个特性，我们希望允许通过对评估结果延迟应用一个函数来转换尚未评估的值。为此，我们将创建一个`map`方法，将我们想要应用的函数作为其参数:

```
default <B> Lazy<B> map(Function<A, B> f) {
    return () -> f.apply(get());
} 
```

我们可能不得不处理在`get`方法中出现异常的可能性。我们有几种可能性:

*   再次引发异常。我们对此没有什么可做的，因为如果我们什么都不做，它就会自动发生。不过，应该注意的是，异常只能是一个`RuntimeException`，因为 JDK 函数接口中的方法不能抛出检查过的异常。因此，如果求值抛出一个检查过的异常，我们必须要么就地处理它，要么在重新抛出之前将其包装在一个未检查的异常中。然而，抛出异常是我们希望在函数式编程中避免的。

*   返回一个特殊类型，表示发生了错误。这可能是一个`Optional<B>`。主要缺点是`Optional`不能携带异常。它只表明一个值本来可以存在，虽然它没有，但它不能说明为什么。我们将在以后的文章中看到如何解决这个问题。

*   返回默认值。当然，我们不知道返回哪个值，所以它必须由调用者提供。

实现返回默认值的情况很容易:

```
default <B> Lazy<B> map(Function<A, B> f, B defaultValue) {
    return () -> {
        try {
            return f.apply(get());
        } catch (Exception e) {
            return defaultValue;
        }
    };
} 
```

这可以在下面的例子中使用:

```
static Lazy<String> name1 = () -> {
    System.out.println("Evaluating name1");
    return "Bob";
};

static Lazy<String> name2 = () -> {
    System.out.println("Evaluating name2");
    throw new RuntimeException();
};

static Function<String, String> constructMessage =
        name -> String.format("Hello, %s!", name);

public static void main(String... args) {
    String defaultValue = "Sorry, but I don't talk to anonymous people.";
    System.out.println(name1.map(constructMessage, defaultValue).get());
    System.out.println("----");
    System.out.println(name2.map(constructMessage, defaultValue).get());
} 
```

控制台上显示的结果如下:

```
Evaluating name1
Hello, Bob!
----
Evaluating name2
Sorry, but I don't talk to anonymous people. 
```

但是当然，我们有时需要使用一个延迟评估的默认值，这很简单:

```
default <B> Lazy<B> map(Function<A, B> f, Lazy<B> defaultValue) {
    return () -> {
        try {
            return f.apply(get());
        } catch (Exception e) {
            return defaultValue.get();
        }
    };
} 
```

返回一个`Optional`有点复杂。下面是一个可能的实现:

```
default <B> Lazy<Optional<B>> mapOption(Function<A, B> f) {
    return () -> {
        try {
            return Optional.of(f.apply(get()));
        } catch (Exception e) {
            return Optional.empty();
        }
    };
} 
```

如果我们稍微修改一下我们的示例程序，就会得到相同的结果:

```
public static void main(String... args) {
    String defaultValue = "Sorry, but I don't talk to anonymous people.";
    System.out.println(name1
            .mapOption(constructMessage)
            .get()
            .orElse(defaultValue));
    System.out.println("----");
    System.out.println(name2
            .mapOption(constructMessage)
            .get()
            .orElse(defaultValue));
} 
```

### 映射返回惰性类型的函数

有时，我们需要用一个结果来映射一个函数，而不是一个`Function<A, B>`。如果我们将这样一个函数传递给`map`方法，结果将是一个`Lazy<Lazy<B>>`。不管成为`Lazy`有什么用，懒惰是有点过分了，所以我们需要一种方法将`Lazy<Lazy<B>>`变成`Lazy<B>`。这也是一个很常见的用例，一般称为`flatten`或`join`。下面是我们实现它的方法:

```
static <A> Lazy<A> flatten(Lazy<Lazy<A>> lla) {
    return lla.get();
} 
```

这很好，但是它只能作为静态方法来实现。当映射一个函数时，这种需求出现得最频繁，最好创建一个特殊版本的`map`，我们称之为`flatMap`:

```
default <B> Lazy<B> flatMap(Function<A, Lazy<B>> f) {
    return () -> f.apply(get()).get();
} 
```

有了这样一个函数，我们可以根据需要构造任意多的惰性值，而不需要计算任何东西:

```
static Lazy<String> lazyGreetings = () -> {
    System.out.println("Evaluating greetings");
    return "Hello";
};

static Lazy<String> lazyFirstName = () -> {
    System.out.println("Evaluating first name");
    return "Jane";
};

static Lazy<String> lazyLastName = () -> {
    System.out.println("Evaluating last name");
    return "Doe";
};

public static void main(String... args) {
    Lazy<String> message = lazyGreetings
        .flatMap(greetings -> lazyFirstName
            .flatMap(firstName -> lazyLastName
                .map(lastName -> String.format(
                        "%s, %s %s!", greetings, firstName, lastName))));
    System.out.println("Message has been composed but nothing has been evaluated yet");
    System.out.print(message.get());
} 
```

结果是:

```
Message has been composed but nothing has been evaluated yet
Evaluating greetings
Evaluating first name
Evaluating last name
Hello, Jane Doe! 
```

这表明在对结果调用`get`方法之前，没有进行任何计算。

### 将一个值放在上下文中

正如我已经说过的，我们有时需要把一个已经计算过的表达式放在惰性上下文中。它不会改变任何关于求值的东西，但是需要用未求值的表达式组成求值的表达式。这是通过静态方法完成的，通常称为`return`或`unit`。Java 对此使用了不同的约定，称这个方法为`of`，所以我们将在我们的接口中定义这样一个方法:

```
static <A> Lazy<A> of(A a) {
    return () -> a;
} 
```

!["Lazy Types In Java"](img/13c70858c1ae724d18cacd84811c8688.png)

## 抽象行为

我们已经实现的`Lazy`类型与许多其他类型共享其行为，如`Optional`、`CompletableFuture`、`Stream`或函数式语言的函数式单链表(但不是`java.util.List`)。拥有一个`flatMap`方法以及从`A`的实例创建一个`Lazy<A>`的方法使得我们的懒惰类型`Lazy`成为一个*单子*(假设这些方法满足一些附加条件)。所有的单子都可以由相同类型的单子和函数组成，就像我们在这里做的一样。

通过提供一个可以进行普通计算的环境，使用单子允许从一个特定的行为(这里是懒惰)中抽象出这个行为，尽管这些普通的计算通常不适用。当我们将一个`Function<A, B>`应用到一个懒惰的`A`而没有将这个懒惰的`A`转换成一个被评估的`A`时，我们看到了这一点。

其他一些单子可能看起来与我们的`Lazy`单子非常相似。例如，`Future<A>`(不是 Java `Future`，更像是`CompletableFuture`)也表示未评估的数据。与`Lazy<A>`的最大区别在于`Lazy`旨在延迟评估(并可能避免评估),而`Future`主要旨在急切地开始评估，其结果只能在以后获得。除此之外，所有其他特征，比如通过使用`map`和`flatMap`来组合它们的方式，都是等价的，尽管这些方法被赋予了不同的名称。

### 处理效果的更好方法

在我们的例子中，我们一直在调用`get`方法，以便在我们希望能够将结果打印到控制台时开始评估。换句话说，我们从上下文中提取了值，以便对其应用效果。这抵消了懒惰的影响。当然，这有时最终是需要的，但是我们应该努力尽可能地延迟这一点。原教旨主义函数式程序员对此使用非常复杂的技术，但对于初学者，我们可以使用简单得多的技术。

我们上面实现的映射包括在惰性上下文中引入一个原始函数，而不是提取值传递给函数。我们可以对效果做同样的事情。我们可以将打印到控制台的效果传递到`Lazy`上下文中，而不是调用`get`将评估结果打印到控制台。为此，我们将使用一个叫做`forEach`的方法，尽管在上下文中只有一个值。这使得我们可以在一个名称下抽象出这个原则，这个原则在大多数情况下是相似的。

实现非常简单:

```
default void forEach(Consumer<A> c) {
    c.accept(get());
} 
```

使用这种方法，我们的最后一个例子可以改写为:

```
public static void main(String... args) {
    Lazy<String> message = lazyGreetings
        .flatMap(greetings -> lazyFirstName
            .flatMap(firstName -> lazyLastName
                .map(lastName -> String.format(
                        "%s, %s %s!", greetings, firstName, lastName))));
    System.out.println("Message has been composed but nothing has been evaluated yet");
    message.forEach(System.out::println);
} 
```

注意，我们的`forEach`方法接受一个`Consumer`，该值应用于上下文中的*每个*值。对于`Lazy`来说，它只能是一个单一的值，这个事实无关紧要。重要的是要清楚地看到，它与 Java 8 `Stream`中的`forEach`是完全相同的概念。这就是`Optional`做错的地方:调用这个方法`ifPresent`欺骗了用户，让他相信它与`Stream`的`forEach`方法不同，而它们的共同点比它们的区别更重要。

### 记住评估的结果

我们的接口允许延迟评估，但不允许在评估值后重用它。这意味着如果我们想两次使用这个值而不重新计算它，我们必须把它存储在某个地方。让`Lazy`类型为我们处理这个问题会更实际。

但是一个接口不能存储一个值，所以我们必须选择另一种设计。我们可以创建一个包含`Supplier`的类，而不是创建一个扩展`Supplier`的接口。这个类将存储一个`Supplier<A>`和一个`A`，使用我们在第一个例子中使用的相同技术:

```
public class Lazy<A> {

    private final Supplier<A> sValue;

    private A value;

    private Lazy(Supplier<A> value) {
        this.sValue = value;
    }

    public A get() {
        // Note that the following code is not thread safe. Thread safety
        // is not implemented here to keep the code simple, but can be
        // added easily.
        if (value == null) {
            value = sValue.get();
        }
        return value;
    }

    public <B> Lazy<B> map(Function<A, B> f) {
        return new Lazy<>(() -> f.apply(this.get()));
    }

    public <B> Lazy<B> map(Function<A, B> f, B defaultValue) {
        return new Lazy<>(() -> {
            try {
                return f.apply(this.get());
            } catch (Exception e) {
                return defaultValue;
            }
        });
    }

    public <B> Lazy<Optional<B>> mapOption(Function<A, B> f) {
        return new Lazy<>(() -> {
            try {
                return Optional.of(f.apply(this.get()));
            } catch (Exception e) {
                return Optional.empty();
            }
        });
    }

    public <B> Lazy<B> flatMap(Function<A, Lazy<B>> f) {
        return new Lazy<>(() -> f.apply(get()).get());
    }

    public void forEach(Consumer<A> c) {
        c.accept(get());
    }

    public static <A> Lazy<A> of(Supplier<A> a) {
        return new Lazy<>(a);
    }

    public static <A> Lazy<A> of(A a) {
        return new Lazy<>(() -> a);
    }

} 
```

注意两个静态工厂方法的存在，以便允许从一个`Supplier<A>`或者从一个已经求值的`A`创建一个`Lazy<A>`。运行以下测试显示了记忆化的好处:

```
static Lazy<String> name1 = Lazy.of(() -> {
    System.out.println("Evaluating name1");
    return "Bob";
});

static Lazy<String> name2 = Lazy.of(() -> {
    System.out.println("Evaluating name2");
    throw new RuntimeException();
});

static Function<String, String> constructMessage =
        name -> String.format("Hello, %s!", name);

public static void main(String... args) {
    String defaultValue = "Sorry, but I don't talk to anonymous people.";
    name1.map(constructMessage, defaultValue).forEach(System.out::println);
    System.out.println("----");
    name2.map(constructMessage, defaultValue).forEach(System.out::println);
    System.out.println("----");
    name1.mapOption(constructMessage).forEach(System.out::println);
    System.out.println("----");
    name2.mapOption(constructMessage).forEach(System.out::println);
} 
```

结果显示，成功评估仅出现一次:

```
Evaluating name1
Hello, Bob!
----
Evaluating name2
Sorry, but I don't talk to anonymous people.
----
Optional[Hello, Bob!]
----
Evaluating name2
Optional.empty 
```

在这里，失败评估的结果不会被记忆。这使我们有机会在下一次调用时重试，这可能有用，也可能没用，这取决于失败的原因。如果我们希望失败被记忆，我们可以只存储一个`Optional`作为调用的结果。

## 摘要

我们学习了如何有效地实现懒惰类型:

*   用`Supplier`代表懒惰
*   组合惰性类型以获得惰性结果
*   将返回计算值的函数映射到惰性类型
*   将返回惰性值的函数映射到惰性类型
*   将效果应用于懒惰类型
*   记住懒惰的类型

在学习这些技术的同时，我们发现了懒惰是如何成为一种计算环境的。我们已经学会了如何

*   把一个表达放在上下文中
*   结合上下文中的值
*   将效果应用于上下文中的值

区分各种类型的上下文并将这些上下文类型抽象成可以在更高的计算级别上组合的元素是函数式编程的主要部分。

## 分享这篇文章