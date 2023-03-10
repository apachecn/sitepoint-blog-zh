# 关于 Java 你不知道的 10 件事

> 原文：<https://www.sitepoint.com/10-things-you-didnt-know-about-java/>

作为 Java 爱好者，我们总是对晦涩的细节感兴趣，这些细节可能没有直接的用处，但是可以教会我们更多关于 Java 和 JVM 的知识。这就是为什么我决定在 jooq.org 的[上发表 Lukas Eder](http://jooq.org) [最初写的](http://blog.jooq.org/2014/11/03/10-things-you-didnt-know-about-java/)这篇伟大的文章。

那么，你从一开始就一直在使用 Java？还记得那个被称为“橡树”的日子吗，那个 OO 还是热门话题的时候，那个 C++的乡亲们认为 Java 没有机会的时候，那个小程序还是个东西的时候？

我敢打赌，下面的事情你至少有一半不知道。本周，让我们从一些关于 Java 内部工作方式的惊人发现开始。

## 1.没有被检查的异常这种东西

没错！JVM 不知道这些，只有 Java 语言知道。

今天，每个人都同意受控异常是一个错误。正如布鲁斯·埃凯尔在 GeeCON 的闭幕主题演讲中所说的那样，除了 Java 之外，其他语言都没有使用过检查异常，甚至 Java 8 也不再在新的 Streams API 中使用检查异常(当你的 lambdas 使用 IO 或 JDBC 时，这实际上有点痛苦，T2)。

你想证明 JVM 不知道这样的事情吗？尝试以下代码:

```
public class Test {

    // No throws clause here
    public static void main(String[] args) {
        doThrow(new SQLException());
    }

    static void doThrow(Exception e) {
        Test.<RuntimeException> doThrow0(e);
    }

    @SuppressWarnings("unchecked")
    static <E extends Exception> void doThrow0(Exception e) throws E {
        throw (E) e;
    }
} 
```

这不仅编译，实际上还抛出了`SQLException`，你甚至不需要龙目岛的 [`@SneakyThrows`](http://projectlombok.org/features/SneakyThrows.html) 来编译。

关于以上的更多细节可以在本文这里找到，或者[这里，关于栈溢出](http://stackoverflow.com/q/12580598/521799)。

## 2.您可以拥有仅在返回类型上不同的方法重载

那不编译，对吧？

```
class Test {
    Object x() { return "abc"; }
    String x() { return "123"; }
} 
```

没错。Java 语言不允许两个方法在同一个类中成为*【等价覆盖】*，不管它们的`throws`子句或`return`类型可能不同。

但是等一下。查看 [`Class.getMethod(String, Class...)`](http://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getMethod-java.lang.String-java.lang.Class...-) 的 Javadoc。上面写着:

> 注意，一个类中可能有不止一个匹配的方法，因为 Java 语言禁止一个类声明多个具有相同签名但不同返回类型的方法，但 Java 虚拟机不允许。虚拟机中增加的灵活性可用于实现各种语言功能。例如，协变收益可以用桥接方法实现；桥方法和被重写的方法将具有相同的签名，但返回类型不同。

哇，是的，有道理。事实上，当您编写以下代码时，情况差不多就是这样:

```
abstract class Parent<T> {
    abstract T x();
}

class Child extends Parent<String> {
    @Override
    String x() { return "abc"; }
} 
```

检查`Child`中生成的字节码:

```
// Method descriptor #15 ()Ljava/lang/String;
// Stack: 1, Locals: 1
java.lang.String x();
  0  ldc </String><String "abc"> [16]
  2  areturn
    Line numbers:
      [pc: 0, line: 7]
    Local variable table:
      [pc: 0, pc: 3] local: this index: 0 type: Child

// Method descriptor #18 ()Ljava/lang/Object;
// Stack: 1, Locals: 1
bridge synthetic java.lang.Object x();
  0  aload_0 [this]
  1  invokevirtual Child.x() : java.lang.String [19]
  4  areturn
    Line numbers:
      [pc: 0, line: 1] 
```

所以，`T`其实只是字节码里的`Object`。这很好理解。

合成桥方法实际上是由编译器生成的，因为在某些调用点上,`Parent.x()`签名的返回类型可能会是`Object`。如果没有这样的桥方法，就不可能以二进制兼容的方式添加泛型。因此，修改 JVM 以允许这个特性是一个较小的痛苦(它也允许协变覆盖作为一个副作用……)聪明，是吧？

你对语言细节和内部有兴趣吗？[然后在这里找到一些更有趣的细节](http://stackoverflow.com/q/442026/521799)。

## 3.这些都是二维数组！

```
class Test {
    int[][] a()  { return new int[0][]; }
    int[] b() [] { return new int[0][]; }
    int c() [][] { return new int[0][]; }
} 
```

是的，是真的。即使您的 mental 解析器可能不会立即理解上述方法的返回类型，它们都是一样的！类似于下面这段代码:

```
class Test {
    int[][] a = {{}};
    int[] b[] = {{}};
    int c[][] = {{}};
} 
```

你觉得这很疯狂吗？想象一下在上面使用 [JSR-308 / Java 8 类型注释](https://jcp.org/en/jsr/detail?id=308)。语法可能性的数量爆炸了！

```
@Target(ElementType.TYPE_USE)
@interface Crazy {}

class Test {
    @Crazy int[][]  a1 = {{}};
    int @Crazy [][] a2 = {{}};
    int[] @Crazy [] a3 = {{}};

    @Crazy int[] b1[]  = {{}};
    int @Crazy [] b2[] = {{}};
    int[] b3 @Crazy [] = {{}};

    @Crazy int c1[][]  = {{}};
    int c2 @Crazy [][] = {{}};
    int c3[] @Crazy [] = {{}};
} 
```

> 键入注释。只有它的力量才能超越它的神秘

换句话说:

> 当我在 4 周假期前做最后一次承诺时
> 
> ![When I do that one last commit just before my 4 week vacation](img/ad44c0cd17d2325fd31c317842d4b726.png)

我把寻找上述任何一个用例的实际工作交给了你。

## 4.你没有得到条件表达式

所以，你认为你知道如何使用条件表达式？让我告诉你，你没有。大多数人会认为下面两个片段是等价的:

```
Object o1 = true ? new Integer(1) : new Double(2.0); 
```

…和这个一样？

```
Object o2;

if (true)
    o2 = new Integer(1);
else
    o2 = new Double(2.0); 
```

没有。让我们做一个快速测试

```
System.out.println(o1);
System.out.println(o2); 
```

该程序将打印:

```
1.0
1 
```

没错。条件操作符将实现数值类型提升，如果*“需要”*，在那个*“需要”*上加上一组非常非常强的引号。因为，你会期望这个程序抛出一个`NullPointerException`？

```
Integer i = new Integer(1);
if (i.equals(1))
    i = null;
Double d = new Double(2.0);
Object o = true ? i : d; // NullPointerException!
System.out.println(o); 
```

关于上述内容的更多信息可在此处找到。

## 5.你也没有得到复合赋值操作符

够古怪吗？让我们考虑下面两段代码:

```
i += j;
i = i + j; 
```

直观上，它们应该是等价的吧？但是你猜怎么着。他们不是！JLS 规定:

> 形式为 E1 op= E2 的复合赋值表达式等价于 E1 = (T)((E1) op (E2))，其中 T 是 E1 的类型，只是 E1 只计算一次。

这太美了，我想引用[彼得·劳里](https://twitter.com/PeterLawrey)的[对这个堆栈溢出问题](http://stackoverflow.com/a/8710747/521799)的回答:

> 这种转换的一个很好的例子是使用*=或/=
> 
> ```
> byte b = 10;
> b *= 5.7;
> System.out.println(b); // prints 57 
> ```
> 
> 或者
> 
> ```
> byte b = 100;
> b /= 2.5;
> System.out.println(b); // prints 40 
> ```
> 
> 或者
> 
> ```
> char ch = '0';
> ch *= 1.1;
> System.out.println(ch); // prints '4' 
> ```
> 
> 或者
> 
> ```
> char ch = 'A';
> ch *= 1.5;
> System.out.println(ch); // prints 'a' 
> ```

这有多有用呢？我将在我的应用程序中对字符进行强制转换。因为，你知道…

## 6.随机整数

现在，这更是一个难题。先别看解决方案。看看你能不能自己找到这个。当我运行以下程序时:

```
for (int i = 0; i < 10; i++) {
    System.out.println((Integer) i);
} 
```

…然后“有时”，我得到以下输出:

```
92
221
45
48
236
183
39
193
33
84 
```

这怎么可能呢？？

。

。

。

。

。

。剧透…前方解决方案…

。

。

。

。

。

[好的，解决方案在这里(https://blog . jooq . org/2013/10/17/add-some-entropy-to-your-JVM/)](https://blog.jooq.org/2013/10/17/add-some-entropy-to-your-jvm/ "Add Some Entropy to Your JVM")并且必须通过反射覆盖 JDK 的`Integer`缓存，然后使用自动装箱和自动取消装箱。在家不要这样！或者换句话说，让我们再一次这样思考

> 当我在 4 周假期前做最后一次承诺时
> 
> ![When I do that one last commit just before my 4 week vacation](img/ad44c0cd17d2325fd31c317842d4b726.png)

## 7.转到

这是我最喜欢的之一。Java 有 GOTO！键入它…

```
int goto = 1; 
```

这将导致:

```
Test.java:44: error: <identifier> expected
int goto = 1;
^ 
```

这是因为`goto`是[一个未使用的关键字](http://docs.oracle.com/javase/tutorial/java/nutsandbolts/_keywords.html)，以防万一…

但这不是令人兴奋的部分。令人兴奋的是，您实际上可以用`break`、`continue`和带标签的块实现 goto:

向前跳跃

```
label: {
  // do stuff
  if (check) break label;
  // do more stuff
} 
```

在字节码中:

```
2  iload_1 [check]
3  ifeq 6          // Jumping forward
6  .. 
```

向后跳

```
label: do {
  // do stuff
  if (check) continue label;
  // do more stuff
  break label;
} while(true); 
```

在字节码中:

```
2  iload_1 [check]
3  ifeq 9
6  goto 2          // Jumping backward
9  .. 
```

![](img/2eab7f7986e0cfb6121e103f1aed8c9e.png)

## 8.Java 有类型别名

在其他语言中([例如 Ceylon](https://blog.jooq.org/2013/12/03/top-10-ceylon-language-features-i-wish-we-had-in-java/ "Top 10 Ceylon Language Features I Wish We Had In Java") )，我们可以非常容易地定义类型别名:

```
interface People => Set<Person>; 
```

以这种方式构建的`People`类型可以与`Set<Person>`互换使用:

```
People?      p1 = null;
Set</Person><Person>? p2 = p1;
People?      p3 = p2; 
```

在 Java 中，我们不能在顶层定义类型别名。但是我们可以对一个类或一个方法的范围这样做。假设我们对`Integer`、`Long`等的命名不满意，我们想要更短的名字:`I`和`L`。简单:

```
class Test<I extends Integer> {
    <L extends Long> void x(I i, L l) {
        System.out.println(
            i.intValue() + ", " +
            l.longValue()
        );
    }
} 
```

在上面的程序中，对于`Test`类的范围，`Integer`被“别名”为`I`，而对于`x()`方法的范围，`Long`被“别名”为`L`。我们可以这样调用上面的方法:

```
new Test().x(1, 2L); 
```

这个技巧当然是不要当真的。在这种情况下，`Integer`和`Long`都是最终类型，这意味着类型`I`和`L`实际上是*的别名*(差不多。赋值兼容性只是单向的)。如果我们使用了非最终类型(例如`Object`，那么我们实际上是在使用普通的泛型。

受够了这些愚蠢的把戏。现在是真正了不起的时刻了！

## 9.有些类型关系是不可判定的！

好了，这将变得非常有趣，所以喝杯咖啡，集中注意力。考虑以下两种类型:

```
// A helper type. You could also just use List
interface Type<T> {}

class C implements Type<Type <? super C>> {}
class D<P> implements Type<Type <? super D<D<P>>>> {} 
```

那么，类型`C`和`D`到底是什么意思呢？

它们在某种程度上是递归的，就像 [`java.lang.Enum`](http://docs.oracle.com/javase/8/docs/api/java/lang/Enum.html) 是递归的一样。考虑:

```
public abstract class Enum<E extends Enum<E>> { ... } 
```

根据上面的规范，实际的`enum`实现仅仅是语法上的糖:

```
// This
enum MyEnum {}

// Is really just sugar for this
class MyEnum extends Enum<MyEnum> { ... } 
```

考虑到这一点，让我们回到我们的两种类型。下面的代码可以编译吗？

```
class Test {
    Type< ? super C> c = new C();
    Type< ? super D<Byte>> d = new D<Byte>();
} 
```

这个问题很难回答，罗斯·泰特对此有一个答案。这个问题实际上是不可判定的:

**C 是<类型的子类型吗？超 C >？**

```
Step 0) C <?: Type<? super C>
Step 1) Type<Type<? super C>> <?: Type (inheritance)
Step 2) C  (checking wildcard ? super C)
Step . . . (cycle forever) 
```

然后:

**D 是<类型的子类型吗？超 D <字节> >？**

```
Step 0) D<Byte> <?: Type<? super C<Byte>>
Step 1) Type<Type<? super D<D<Byte>>>> <?: Type<? super D<Byte>>
Step 2) D<Byte> <?: Type<? super D<D<Byte>>>
Step 3) Type<Type<? super C<C>>> <?: Type<? super C<C>>
Step 4) D<D<Byte>> <?: Type<? super D<D<Byte>>>
Step . . . (expand forever) 
```

试着在你的 Eclipse 中编译上面的代码，它会崩溃的！不要担心。我已经提交了一个 bug

让这一点深入人心…

> Java 中的一些类型关系是*不可判定的*！

如果你对这个特殊的 Java 怪癖的更多细节感兴趣，请阅读 [Ross Tate 的论文“在 Java 的类型系统中驯服通配符”](http://www.cs.cornell.edu/~ross/publications/tamewild/tamewild-tate-pldi11.pdf)(与 Alan Leung 和 Sorin Lerner 合著)，或者我们自己对[将子类型多态性与泛型多态性相关联的思考](https://blog.jooq.org/2013/06/28/the-dangers-of-correlating-subtype-polymorphism-with-generic-polymorphism/ "The Dangers of Correlating Subtype Polymorphism with Generic Polymorphism")

## 10.类型交叉点

Java 有一个非常奇特的特性，叫做类型交集。您可以声明一个(泛型)类型，它实际上是两个类型的交集。例如:

```
class Test<T extends Serializable & Cloneable> {
} 
```

您绑定到类`Test`实例的泛型类型参数`T`必须同时实现 `Serializable`和`Cloneable`的*。例如，`String`不是一个可能的界限，但是`Date`是:*

```
// Doesn't compile
Test<String> s = null;

// Compiles
Test<Date> d = null; 
```

这个特性在 Java 8 中得到了重用，现在可以将类型强制转换为特定的类型交集。这有什么用？几乎没有，但是如果你想把一个 lambda 表达式强制转换成这样的类型，没有别的办法。让我们假设您的方法有这个疯狂的类型约束:

```
<T extends Runnable & Serializable> void execute(T t) {} 
```

你需要一个也是 T1 的 T0，以防你想在别的地方执行它，并通过网络发送它。兰姆达斯和序列化有点古怪。

[Lambdas 可以序列化](http://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html#serialization):

> 如果 lambda 表达式的目标类型和捕获的参数是可序列化的，则可以序列化该表达式

但即使这是真的，它们也不会自动实现`Serializable`标记接口。若要将它们强制转换为该类型，必须进行强制转换。但是当你只投给`Serializable` …

```
execute((Serializable) (() -> {})); 
```

…那么 lambda 将不再能够运行。

呃…

所以…

将其转换为两种类型:

```
execute((Runnable & Serializable) (() -> {})); 
```

## 结论

我通常只说 SQL，但是是时候用下面的话来结束一篇文章了:

> Java 是一种神秘的设备，只有它的强大才能超越它

## 分享这篇文章