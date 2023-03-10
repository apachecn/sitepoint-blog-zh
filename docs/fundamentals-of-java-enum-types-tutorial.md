# Java 枚举类型的基础

> 原文：<https://www.sitepoint.com/fundamentals-of-java-enum-types-tutorial/>

### 目录 

*   [枚举基础](#enumfundamentals)
*   [定义枚举](#defininganenum)
*   [分配枚举](#assigninganenum)
*   [使用枚举](#usinganenum)
*   [列举为一类](#enumsasaclass)
*   [扩展 java.lang.Enum](#extendingjavalangenum)
*   [带字段的枚举](#enumswithfields)
*   [带方法的枚举](#enumswithmethods)
*   [实现接口](#implementinginterfaces)
*   [总结](#summary)
*   [评论](#comments)

无论你在做什么代码，你迟早会需要一个常量列表，比如周一、周二等等。所有工作日或一月、二月等。整整几个月。Java 用*枚举类型*覆盖了你，通常只称为*枚举*，这使得定义它变得非常容易。在这篇文章中，我将教你成为 Java 枚举的熟练用户所需要知道的一切。

## 枚举基础

最简单的 Java 枚举就是开发者在编写代码时定义的固定数量的常量。(例如，它可以定义所有的站点点通道。)与类非常相似，枚举定义了一个类型，它可以在任何可以使用类和接口的地方使用，例如字段、变量和参数。

枚举实际上可以不仅仅是常量，它们有自己的属性和方法，但是我稍后会谈到这一点。将它们视为常数是了解它们的好方法。

### 定义枚举

枚举的定义类似于类的定义，但是使用了`enum`而不是`class`关键字。常量值在枚举体中列出(意思是在花括号内)。虽然没有必要，但通常将常量全部用大写字母书写，以便于识别。

现在我们已经有了枚举的描述，是时候举例说明了。下面是列出了一些站点点通道的枚举的外观:

```
public enum SitePointChannel {
    JAVA,
    MOBILE,
    WEB,
    PHP,
    WORDPRESS,
    JAVASCRIPT,
    DESIGN
} 
```

`SitePointChannel`枚举中的元素被称为*枚举常数*。

### 分配枚举

一旦定义了枚举，就可以将其值赋给变量。这里有一个例子:

```
private SitePointChannel channel; 
```

`channel`字段属于类型`SitePointChannel`，因此它唯一可以被赋值的值是由这个枚举定义的值。作为一个局部变量，如下所示:

```
SitePointChannel channel = SitePointChannel.JAVA; 
```

### 使用枚举

枚举在 Java 中有各种各样的特性。它们的核心能力之一是可以在[身份比较](https://www.sitepoint.com/implement-javas-equals-method-correctly/#identityversusequality)和`switch`语句中使用。

#### 与`==`的同一性比较

可以通过使用关系运算符`==`来比较枚举常数的同一性。这里是一个`if()`条件的例子，在这种情况下总是假的。

```
SitePointChannel channel = SitePointChannel.JAVA;
if (channel == SitePointChannel.MOBILE) {
    System.out.println("...");
} 
```

#### Switch 语句

枚举的另一个非常广泛使用的特性是控制 [`switch`语句](https://sitepoint.com/javas-switch-statement)的能力。下面是一个打印给定频道内容的示例:

```
SitePointChannel channel = ... // specify an enumeration constant
switch (channel) {
    case JAVA:
        System.out.println("Java, web and desktop technologies");
        break;
    case MOBILE:
        System.out.println("Mobile technologies");
        break;
    case PHP:
        // as usual for switch, once a match was found, the execution
        // "falls through" to the next branch until it hits a break
    case WEB:
    case JAVASCRIPT:
    case WORDPRESS:
        System.out.println("Web technologies");
        break;
    default:
        throw new IllegalArgumentException(
                "Unknown channel '" + channel + "'.")
        break;
} 
```

在`switch`语句中，使用的枚举常量没有枚举类型名。这是因为枚举类型是在`switch`表达式中隐式指定的。列出所有的枚举常量是一个很好的做法(即使其中一些不做任何事情)*和*添加一个默认分支，通常有一个例外(万一添加了一个新的常量，有人错过了 switch 语句而没有更新它。)

![Enums for a list of sonstants - traffic lights, maybe?](img/615c0a9a3ebef753db01906c96f35841.png)

## 枚举作为一个类

有了 Java 枚举的基础知识，我们可以超越将枚举解释为固定数量的常数。事实上，枚举更像是类！

它们可以有字段和方法，也可以实现接口，我马上会解释所有这些。甚至枚举常量也没有那么特殊——它们只是枚举类型的公共、静态和最终成员。我们不需要把`public static final`放进去的唯一原因是编译器为我们填充了它。

与常规类的主要区别在于，它们不能扩展其他类(原因见下文),也不能用`new`关键字创建(以保持常量数量固定)。

### 延伸`java.lang.Enum`

所有枚举都隐式扩展`java.lang.Enum`。在 Java 中，一个类只能扩展一个父类，因此枚举不能扩展任何其他类(但是实现接口——见下文)。

扩展`Enum`意味着每一个枚举都有一些方法使它更有用:

*   静态`values()`
*   静态`valueOf(String)`
*   `name()`
*   `ordinal()`
*   `compareTo(Enum)`

#### `Enum`的`values()`方法

`values()`方法返回包含所有枚举常量的枚举类型变量的数组。这里有一个例子:

```
SitePointChannel[] channels = SitePointChannel.values();
for (SitePointChannel channel : channels) {
    System.out.println(channel + " Channel");
} 
```

输出是:

```
JAVA Channel
MOBILE Channel
WEB Channel
PHP Channel
WORDPRESS Channel
JAVASCRIPT Channel
DESIGN Channel 
```

如您所见，`values()`方法提供了一个很好的解决方案来循环遍历一个枚举的所有常量。

#### `Enum`的`valueOf(String)`方法

`valueOf(String)`方法返回一个枚举常量，其值对应于传递给它的字符串，或者如果没有找到具有指定名称的常量，则抛出一个`IllegalArgumentException`。小心，传递给`valueOf`的字符串是区分大小写的！

```
// returns SitePointChannel.JAVA enum
SitePointChannel.valueOf("JAVA");

// throws IllegalArgumentException
SitePointChannel.valueOf("java"); 
```

如前所述，所有枚举都自动继承`java.lang.Enum`。这个类定义了几个方法，可用于所有的枚举。

#### `name()`法

该方法返回该枚举常量的名称，与其枚举声明中声明的名称完全相同。这里有一个例子:

```
SitePointChannel channel = SitePointChannel.JAVA;
System.out.println(channel.name()); 
```

这个例子的输出是字符串`JAVA`。

#### `ordinal()`法

此方法用于获取枚举常数在常数列表中的位置。这被称为序数值。这里有一个例子:

```
SitePointChannel channel = SitePointChannel.JAVA;
System.out.println(channel.ordinal()); 
```

这个例子的输出是“0 ”,因为`JAVA`是`Sitepoint`枚举的第一个常数，并且初始常数被赋予零序数。因此，以下输出“3”:

```
SitePointChannel channel = SitePointChannel.PHP;
System.out.println(channel.ordinal()); 
```

#### `compareTo(Enum)`法

此方法用于比较同一枚举的两个常数的序数值。此方法基于正在比较的两个实例的序号位置返回一个负整数、零或正整数。

事情是这样的。让我们以一个引用 enum 实例`SitePointChannel.PHP`为例。

```
SitePointChannel channel  = SitePointChannel.PHP; 
```

当然，如果我们比较`channel`实例和`SitePointChannel.PHP`，`compareTo()`方法将返回值 0。

```
// returns 0
channel.compareTo(SitePointChannel.PHP); 
```

由于引用枚举实例位于枚举类中的第四个位置，并且在其位置之前和之后都有元素，因此它可以使用此方法返回正输出和负输出。

例如，如果与`SitePointChannel.JAVA`比较，`compareTo()`方法的输出将是正的，因为`SitePointChannel.JAVA`位于引用实例之前:

```
// actually returns 3
channel.compareTo(SitePointChannel.JAVA); 
```

如果将引用枚举实例与枚举类中它后面的实例进行比较，它将返回负值。例如，将其与`SitePointChannel.JAVASCRIPT`进行比较将返回值-2。

```
// returns -2
channel.compareTo(SitePointChannel.JAVASCRIPT); 
```

### 带字段的枚举

Java 枚举常量可以有字段，这些字段必须在创建时给定一个值。请记住，与常规类的实例一样，每个枚举常数都有自己的字段。为了定义值，封闭类型必须有一个接受参数的构造函数。正如我前面提到的，每个枚举常量都是其枚举类型的对象，因此为每个枚举常量调用一个构造函数。字段和构造函数(以及方法)必须在常量列表下定义。

这个问题我们来看一个例子。假设我们有一个包含频道列表的`SitePointChannel`枚举，我们需要向每个频道添加已发布文章的数量。我将为它声明一个字段:

```
public enum SitePointChannel {

    JAVA,
    MOBILE,
    WEB,
    PHP,
    WORDPRESS,
    JAVASCRIPT,
    DESIGN;

    private int numberOfArticles;
} 
```

到目前为止，一切顺利，但是该字段从未赋值。为此，我们还需要一个构造函数:

```
// DOES NOT COMPILE
public enum SitePointChannel {

    // [...] list of constants as before

    private int numberOfArticles;

    Sitepoint(int numberOfArticles) {
        this.numberOfArticles = numberOfArticles;
    }
} 
```

不幸的是，这无法编译，因为常量是用不再存在的无参数构造函数初始化的。为了解决这个问题，我们调用新的构造函数:

```
public enum SitePointChannel {

    JAVA(1344),
    MOBILE(2444),
    WEB(4311),
    PHP(5311),
    WORDPRESS(3221),
    JAVASCRIPT(3865),
    DESIGN(3492);

    // [...] field and constructor as before

} 
```

同样的逻辑适用于多个实例变量和不同的数据类型(`int`、`double`、`String`、…)。

### 带有方法的枚举

除了所有枚举都可用的基本方法之外，您还可以添加自定义方法来添加其他功能。

下面的示例演示枚举与方法一起使用的用处。在本文前面，我提到过`valueOf()`方法是区分大小写的。下面的解决方案将通过创建我们自己的方法，给出一个没有大小写限制的`valueOf()`方法的替代解决方案。

```
public enum SitePointChannel {

    JAVA,
    MOBILE,
    WEB,
    PHP,
    WORDPRESS,
    JAVASCRIPT,
    DESING;

    public static SitePointChannel valueOfIgnoreCase(String channelName) {
        channelName = channelName.toUpperCase();
        return valueOf(channelName);
    }

} 
```

要确保此解决方案有效，请运行以下代码片段。

```
if (SitePointChannel.valueOfIgnoreCase("jaVa") == SitePointChannel.JAVA){
    System.out.println("Ignore case enum");
} 
```

从示例中可以看出，`valueOfIgnoreCase()`函数接受字符串“jaVa”并返回`JAVA` enum。这个解决方案适用于表示一个`SitePointChannel`枚举实例的字符串的任何大写和小写字符组合。

### 实现接口

考虑到拥有字段和方法的能力，枚举也可以实现接口是有意义的。这就像在普通课堂上一样，所以没什么可说的。

这里有一个简单的例子:

```
public interface Printable {

    void print();

}

public enum SitePointChannel implements Printable {

    // [...] constants, field and constructor as before

    @Override
    public void print() {
        System.out.println("Channel " + name()
                + " has " + numberOfArticles + " articles.");
    }

} 
```

## 摘要

这些都是关于 Java 枚举的基础知识。我们看到它们可以描述固定数量的常数，以及如何使用`Enum`的方法`values()`、`valueOf(String)`、`name()`、`ordinal()`和`compareTo(Enum)`。但是我们也发现它们是成熟的类，允许添加字段、创建方法和实现接口。

## 分享这篇文章