# 超越 POJOs 用 Lombok 减少样板文件的十种方法

> 原文：<https://www.sitepoint.com/beyond-pojos-ten-ways-reduce-boilerplate-lombok/>

### 目录 

*   [记录注释](#loggingannotations)
*   [懒惰的吸杂者](#lazygetters)
*   [更安全的同步](#safersynchronized)
*   [空检查](#nullchecks)
*   [带值的类型推理](#typeinferencewithval)
*   [@ sneaky rows](#sneakythrows)
*   [实验特征](#experimentalfeatures)
*   [扩展方法](#extensionsmethods)
*   [实用程序类构造函数](#utilityclassconstructors)
*   [灵活的@访问器](#flexibleaccessors)
*   [字段默认值](#fielddefaults)
*   [结论](#conclusions)
*   [评论](#comments)

编者按:在 SitePoint，我们总是在寻找那些了解自己作品的作者。如果你是一个有经验的 Java 开发者，并且想分享你的知识，为什么不[为我们](https://www.sitepoint.com/write-for-us/)写一篇文章呢？

Lombok 是一个很棒的库，它的主要卖点是如何整理 POJO 定义。但并不局限于那个用例！在本文中，我将向您展示六个稳定的和四个实验性的 Lombok 特性，它们可以使您的 Java 代码更加整洁。它们涵盖了许多不同的主题，从日志到访问器，从空安全到实用程序类。但它们都有一个共同点:减少样板文件，使代码更容易阅读，更具表现力。

## 记录注释

有多少次你把一个记录器定义从一个类复制到另一个类，却忘了更改类名？

```
public class LogTest {

    private static final Logger log =
            LoggerFactory.getLogger(NotTheLogTest.class);

    public void foo() {
        log.info("Info message");
    }

} 
```

为了确保这种情况不会再次发生，Lombok 提供了几个注释，允许您轻松地在类中定义一个 logger 实例。因此，您可以使用 Lombok 的注释来删除样板文件，并确保记录器属于正确的类，而不是像上面那样编写代码:

```
@Slf4j
public class LogTest {

    public void foo() {
        log.info("Info message");
    }

} 
```

根据您选择的日志框架，您可以使用以下注释之一:

*   `@Log4J`–使用 [Log4j](http://logging.apache.org/log4j/1.2/) 框架
*   `@Log4J2`–使用 [Log4j2](http://logging.apache.org/log4j/2.x/) 框架
*   `@Slf4J`–使用 [SLF4J](http://www.slf4j.org/) 框架
*   `@CommonsLog`–使用 [Apache Commons 日志](https://commons.apache.org/proper/commons-logging/)
*   `@JBossLog`–使用 [JBoss 日志](https://github.com/jboss-logging/jboss-logging)
*   `@Log`–使用标准的 [Java util 记录器](https://docs.oracle.com/javase/7/docs/api/java/util/logging/Logger.html)
*   `@XSlf4j`–使用[扩展 SLF4J 记录器](http://www.slf4j.org/extensions.html#extended_logger)

## 懒惰的吸气者

另一个很酷的 Lombok 特性是支持惰性初始化。[惰性初始化](https://en.wikipedia.org/wiki/Lazy_initialization)是一种优化技术，用于延迟昂贵的字段初始化。为了正确地实现它，您需要保护您的初始化不受竞争条件的影响，这将导致代码难以理解并且容易出错。使用 Lombok，您可以用`@Getter(lazy = true)`来注释一个字段。

要使用这个注释，我们需要定义一个私有的和最终的字段，并将一个函数调用的结果赋给它:

```
public class DeepThought {

    @Getter(lazy = true)
    private final String theAnswer = calculateTheUltimateAnswer();

    public DeepThought() {
        System.out.println("Building DeepThought");
    }

    // This function won't be called during instance creation
    private String calculateTheUltimateAnswer() {
        System.out.println("Thinking for 7.5 million years");
        return "42";
    }

} 
```

如果我们创建这个类的一个实例，就不会计算这个值。相反,`theAnswer`只有在我们第一次访问它的时候才会被初始化。假设我们如下使用`DeepThought`类:

```
DeepThought deepThought = new DeepThought();
System.out.println("DeepThought is ready");
deepThought.getTheAnswer(); 
```

然后，我们将收到以下输出:

```
Building DeepThought
DeepThought is ready
Thinking for 7.5 million years 
```

如您所见，最终答案的值不是在对象初始化期间计算的，而是在我们第一次访问它的值时才计算的。

## 更安全`synchronized`

当 Java 的`synchronized`关键字应用于方法级时，它使用`this`引用同步特定的方法。以这种方式使用`synchronized`可能很方便，但是没有什么可以阻止你的类的用户获得同样的锁，并且通过打乱你精心设计的锁定策略来搬起石头砸自己的脚。

防止这种情况发生的常见模式是创建一个专用于锁的私有字段，并在`lock`对象上进行同步:

```
public class Foo {

    private final Object lock = new Object();

    public void foo() {
        synchronized(lock) {
            // ...
        }
    }

} 
```

但是这样的话，你就不能在方法层使用`synchronized`关键字，这也不会让你的代码更清晰。

对于这种情况，Lombok 提供了`@Synchronized`注释。它的用法与`synchronized`关键字类似，但是它没有使用`this`引用，而是创建了一个私有字段并对其进行同步:

```
public class Foo {

    // synchronized on a generated private field
    @Synchronized
    public void foo() {
        // ...
    }

} 
```

如果您需要同步不同锁上的不同方法，您可以向`@Synchronized`注释提供一个锁对象的名称，但是在这种情况下，您需要自己定义锁:

```
public class Foo {

    // lock to synchronize on
    private Object barLock = new Object();

    @Synchronized("barLock")
    public void bar() {
        // ...
    }

} 
```

在这种情况下，Lombok 将同步`barLock`对象上的`bar`方法。

## 无效支票

Java 中样板文件的另一个来源是空检查。为了防止字段为空，您可以编写如下代码:

```
public class User {

    private String name;
    private String surname;

    public User(String name, String surname) {
        if (name == null) {
            throw new NullPointerException("name");
        }
        if (surname == null) {
            throw new NullPointerException("surname");
        }
        this.name = name;
        this.surname = surname;
    }

    public void setName(String name) {
        if (name == null) {
            throw new NullPointerException("name");
        }
        this.name = name;
    }

    public void setSurname(String surname) {
        if (surname == null) {
            throw new NullPointerException("surname");
        }
        this.surname = surname;
    }

} 
```

为了使这一点变得简单，您可以使用 Lombok 的`@NotNull`注释。如果你用它标记一个字段、方法或构造函数参数，Lombok 会自动为你生成空校验代码。

```
@Data
@Builder
public class User {

    @NonNull
    private String name;
    @NonNull
    private String surname;
    private int age;

    public void setNameAndSurname(@NonNull String name, @NonNull String surname) {
        this.name = name;
        this.surname = surname;
    }

} 
```

如果`@NonNull`应用于一个字段，Lombok 将在 setter 和 constructor 中添加一个空检查。此外，您不仅可以对类字段应用`@NonNull`,还可以对方法参数应用`@NonNull`。

因此，下面代码片段的每一行都会引发一个`NullPointerException`:

```
User user = new User("John", null, 23);
user.setSurname(null);
user.setNameAndSurname(null, "Doe"); 
```

## 用`val`进行类型推断

这可能很激进，但是 Lombok 允许您在 Java 代码中添加类似 Scala 的结构。当创建一个新的局部变量时，不需要键入类型的名称，您可以简单地编写`val`。就像在 Scala 中一样，变量类型将从赋值操作符的右边扣除，变量将被定义为 final。

```
import lombok.val;

val list = new ArrayList<String>();
list.add("foo");
for (val item : list) {
    System.out.println(item);
} 
```

如果你认为这非常不像 Java，你可能想要习惯它，因为 [Java 完全有可能在将来有一个类似的关键字](https://www.sitepoint.com/what-java-might-one-day-look-like/)。

## `@SneakyThrows`

`@SneakyThrows`让不可思议的事情成为可能:它允许你在没有使用`throws`声明的情况下抛出检查过的异常*。根据你的世界观，这要么修正了 Java 最糟糕的设计决策，要么打开了你代码库中间的潘多拉盒子。*

例如，如果您需要从像`Runnable`这样具有非常严格的接口的方法中引发一个异常，那么`@SneakyThrows`就派上了用场:

```
public class SneakyRunnable implements Runnable {

    @SneakyThrows(InterruptedException.class)
    public void run() {
        throw new InterruptedException();
    }

} 
```

这段代码编译，如果你执行`run`方法，它将抛出`Exception`实例。没有必要像你可能做的那样用`RuntimeException`把它包起来。

这种注释的一个缺点是，您无法捕获未声明的已检查异常。以下代码将不会编译:

```
try {
    new SneakyRunnable().run();
} catch (InterruptedException ex) {
    // javac: exception java.lang.InterruptedException
    // is never thrown in body of corresponding try statement
    System.out.println(ex);
} 
```

![](img/811ed2a966cdb2abd7fba8a8e7a4cd29.png)

## 实验特性

除了我们目前看到的稳定特性，Lombok 还有一组[实验特性](https://projectlombok.org/features/experimental/)。如果你想从龙目岛榨取尽可能多的钱，请随意使用它们，但你需要了解其中的风险。这些特性可能会在即将发布的版本中得到提升，但也可能会在将来的次要版本中被删除。实验特性的 API 也可能改变，你可能需要更新你的代码。

可以写一篇单独的文章来讨论实验性的特性，但是在这里，我将只讨论那些很有可能升级为稳定版的特性，只做很小的改动或者不做改动。

### 扩展方法

几乎每个 Java 项目都有所谓的实用类——只包含静态方法的类。通常，它们的作者更希望这些方法是它们所涉及的接口的一部分。例如，`StringUtils`类中的方法对字符串进行操作，如果能在`String`实例中直接调用它们就好了。

```
public class StringUtils {

    // would be nice to call "abc".isNullOrEmpty()
    // instead of StringUtils.isNullOrEmpty("abc")
    public static boolean isNullOrEmpty(String str) {
        return str == null || str.isEmpty();
    }

} 
```

Lombok 对这个用例有一个注释，灵感来自其他语言的扩展方法(例如 Scala 中的[)。它将实用程序类中的方法添加到对象的接口中。因此，我们需要做的就是将`isNullOrEmpty`添加到`String`接口，并将定义它的类传递给`@ExtensionMethod`注释。在这种情况下，每个静态方法都被添加到其第一个参数的类中:](http://alvinalexander.com/scala/scala-how-to-add-new-methods-to-existing-classes)

```
@ExtensionMethod({StringUtils.class})
public class App {

    public static void main(String[] args) {
        String s = null;
        String s2 = "str";
        s.isNullOrEmpty();   // returns "true"
        s2.isNullOrEmpty();  // returns "false";
    }

} 
```

我们也可以将这个注释用于内置的实用程序类，如`Arrays`:

```
@ExtensionMethod({Arrays.class})
public class App {

    public static void main(String[] args) {
        String[] arr = new String[] {"foo", "bar", "baz"};
        List<String> list = arr.asList();
    }

} 
```

这只会对注释类内部产生影响，在本例中为`App`。

### 实用程序类构造函数

谈论实用程序类…关于它们要记住的一件重要的事情是，我们需要传达这个类不应该被实例化。一种常见的方法是创建一个抛出异常的私有构造函数(以防有人使用反射来调用它):

```
public class StringUtils {

    private StringUtils() {
        throw new UnsupportedOperationException(
                "Utility class should not be instantiated");
    }

    public static boolean isNullOrEmpty(String str) {
        return str == null || str.isEmpty();
    }

} 
```

这个构造函数令人分心而且麻烦。幸运的是，Lombok 有一个为我们生成它的注释:

```
@UtilityClass
class StringUtils {

    public static boolean isNullOrEmpty(String str) {
        return str == null || str.isEmpty();
    }

} 
```

现在，我们可以像以前一样调用这个实用程序类的方法，但我们不能实例化它:

```
StringUtils.isNullOrEmpty("str"); // returns "false"
// javac: StringUtils() has private access in lomboktest.StringUtils
new StringUtils(); 
```

### 灵活`@Accessors`

该功能本身不工作，但用于配置 [`@Getter`和`@Setter`注释](https://www.sitepoint.com/declutter-pojos-with-lombok-tutorial#usinglombok)如何生成新方法。它有三个配置其行为的标志:

*   `chain`:让 setters 返回`this`引用，而不是`void`
*   `fluent`:创建流畅的界面。这将所有的获取器和设置器命名为`name`，而不是`getName`和`setName`。除非另外指定，否则它还将`chain`设置为真。
*   一些开发者更喜欢以前缀“f”作为字段名的开头。该注释允许从 getters 和 setters 中删除指定的前缀，以避免使用像`fName`或`getFName`这样的方法名。

下面是一个具有流畅接口的类的示例，其中所有字段都带有“f”前缀:

```
@Accessors(fluent = true, prefix = "f")
@Getter
@Setter
class Person {

    private String fName;
    private int fAge;

} 
```

你可以这样使用它:

```
Person person = new Person()
    .name("John")
    .age(34);
System.out.println(person.name()); 
```

### 字段默认值

在一个类中，所有字段都有一组相同的修饰符，这种情况并不少见。通读它们很烦人，一遍又一遍地输入它们更烦人:

```
public class Person {

    private final String name;
    private final int age;

    // constructor and getters

} 
```

为了帮助解决这个问题，Lombok 有一个实验性的`@FieldDefaults`注释，它定义了应该添加到类中所有字段的修饰符。下面的示例将所有字段设为公共的和最终的:

```
@FieldDefaults(level = AccessLevel.PUBLIC, makeFinal = true)
@AllArgsConstructor
public class Person {

    String name;
    int age;

} 
```

因此，您可以从包外部访问`name`:

```
Person person = new Person("John", 34);
System.out.println(person.name); 
```

如果需要用不同注释定义的字段很少，可以在字段级重新定义它们:

```
@FieldDefaults(level = AccessLevel.PUBLIC, makeFinal = true)
@AllArgsConstructor
public class Person {

    // Make this field package private
    @PackagePrivate String name;
    // Make this field non final
    @NonFinal int age;

} 
```

## 结论

Lombok 提供了广泛的工具，可以将您从数千行样板文件中拯救出来，并使您的应用程序更加简洁明了。主要的问题是，您的一些团队成员可能不同意使用什么功能以及何时使用它们。像`@Log`这样的注释不太可能引起重大分歧，而`val`和`@SneakyThrows`可能会有很多反对者。在你开始使用 Lombok 之前，我建议你就如何使用它达成一致。

在任何情况下都要记住，所有的 Lombok 注释都可以使用 [`delombok`](https://projectlombok.org/features/delombok.html) 命令轻松地转换成普通的 Java 代码。

如果 Lombok 引起了你的兴趣，你想了解它的魔法是如何工作的，或者想马上开始使用它，请阅读我的 [Lombok 教程](https://www.sitepoint.com/declutter-pojos-with-lombok-tutorial)。

## 分享这篇文章