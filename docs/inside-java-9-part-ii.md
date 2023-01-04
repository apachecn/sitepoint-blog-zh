# Java 9 内幕——性能、编译器等等

> 原文：<https://www.sitepoint.com/inside-java-9-part-ii/>

除了模块化之外，Java 9 还有很多功能:[新的语言特性和许多新的或改进的 API](https://www.sitepoint.com/ultimate-guide-to-java-9/)、 [GNU 风格的命令选项、多版本 jar、改进的日志](https://www.sitepoint.com/inside-java-9-part-i/)等等。让我们深入探讨这一点，看看性能的提高，这主要归功于字符串欺骗、编译器、垃圾收集和 JavaDoc。

### 目录 

*   [性能提升](#performanceimprovements)
*   [紧凑的琴弦](#compactstrings)
*   [实施](#implementation)
*   [性能](#performance)
*   [独立字符串连接](#indifiedstringconcatenation)
*   [另一款混合包](#anothermixedbag)
*   [垃圾收集](#garbagecollection)
*   [编译器](#compiler)
*   [为旧版本 Java 编译](#compileforolderjavaversions)
*   [JVM 编译器接口](#jvmcompilerinterface)
*   [提前编译](#aheadoftimecompilation)
*   [内部构件](#internals)
*   [JavaDoc](#javadoc)
*   [没了！](#nomore)
*   [评论](#comments)

## 性能改进

Java 从一个版本到另一个版本变得更加高效，9 也不例外。有几个有趣的变化旨在减少 CPU 周期或节省内存。

### 紧凑字符串

当你查看一个 Java 应用程序的堆，去掉我们用来组织状态的所有对象头和指针时，只剩下原始数据。它由什么组成？当然是原语——许多许多，其中许多是`char`的，集中在支持`String`实例的`char`数组中。事实证明，这些数组占据了普通应用程序活动数据(包括头和指针)的 20 %到 30 %。这方面的任何改进对于大部分 Java 程序来说都是一个巨大的胜利！的确，还有改进的余地。

一个`char`占用了两个字节，因为它代表了一个完整的 UTF-16 代码单元，但结果是绝大多数字符串只需要一个字节的 ISO-8859-1。这是巨大的！有了一种尽可能只使用一个字节的新表示法，由字符串引起的内存占用可以减少一半。这将使普通应用程序的内存消耗减少 10 %到 15 %,并且通过减少垃圾收集时间来减少运行时间。

当然，这只有在没有开销的情况下才会发生。有人要免费午餐吗？ [JEP 254](http://openjdk.java.net/jeps/254) 试了一下…

#### 履行

在 Java 8 `String`中有一个字段`char[] value`——这是我们刚刚讨论过的数组，它保存字符串的字符。这个想法是使用一个`byte`数组，根据所需的编码，每个字符花费一个或两个字节。

这听起来像是 UTF-8 等可变大小记录的情况，其中每个字符区分一个字节和两个字节。但是没有办法预测单个字符将占据哪个数组槽，因此需要随机访问(例如`charAt(int)`)来执行线性扫描。将随机存取性能从常数降低到线性时间是一种不可接受的回归。

相反，每个字符可以用一个字节编码，在这种情况下，这是选择的表示，或者如果其中至少有一个字符需要两个字节，则两个字节将用于所有字符。一个新的字段`coder`将表示字节如何编码字符，并且`String`中的许多方法评估它以选择正确的代码路径。

当在 Java 8 中构造一个新的字符串时，通常会重新创建一个`char`数组，然后用构造函数参数填充它。例如，当调用`new String(myChars)`时，`Arrays.copyOf`被用来将`myChars`的副本分配给`value`。这样做是为了防止与用户代码共享数组，只有少数情况下不复制数组，例如从另一个字符串创建一个字符串。因此，由于`value`数组从不与`String`之外的代码共享，重构为`byte`数组是安全的(易于封装)。因为构造函数参数总是被复制的，所以转换它不会增加令人望而却步的开销。

这是它的样子:

```
// this is a simplified version of a String constructor,
// where `char[] value` is the argument
if (COMPACT_STRINGS) {
    byte[] val = StringUTF16.compress(value);
    if (val != null) {
        this.value = val;
        this.coder = LATIN1;
        return;
    }
}
this.coder = UTF16;
this.value = StringUTF16.toBytes(value); 
```

这里有几点需要注意:

*   布尔标志`COMPACT_STRINGS`，它是命令行标志`XX:-CompactStrings`的实现，使用它可以禁用整个特性。
*   实用程序类`StringUTF16`首先用于尝试将`value`数组压缩为单字节，如果失败并返回`null`，则将其转换为双字节。
*   `coder`字段被赋予相应的常数，用于标记适用的情况。

如果你觉得这个话题很有趣，以至于你现在还没有睡着，我强烈推荐你去看 Aleksey Shipilev 关于紧凑字符串和独立字符串连接的富有教育意义和娱乐性的[演讲，还有一个很棒的副标题:](https://www.youtube.com/watch?v=wIyeOaitmWM)

> 为什么那些[咒骂的][咒骂的][咒骂的]不能在一个月内做这个功能，而是花一年？！

#### 表演

在我们真正研究性能之前，有一个小细节需要注意。JVM 对内存中的对象进行 8 字节对齐，这意味着当一个对象占用的字节数少于 8 字节时，剩余的字节就被浪费了。在 JVM 最常见的配置中，一个带有压缩引用的 64 位 VM，一个字符串需要 20 个字节(12 个字节用于对象头，4 个字节用于`value`数组，最后 4 个字节用于缓存的散列)，这样就剩下 4 个字节可以挤进`coder`字段，而不会增加内存占用。很好。

紧凑字符串在很大程度上是一种内存优化，因此观察垃圾收集器是有意义的。试图理解 G1 的日志超出了本文的范围，所以我把重点放在运行时性能上。这是有意义的，因为如果字符串需要更少的内存，创建它们也应该更快。

为了评估运行时性能，我运行了[这段代码](https://github.com/CodeFX-org/demo-java-9/blob/master/src/org/codefx/demo/java9/internal/string/CompactionPerformance.java):

```
long launchTime = System.currentTimeMillis();
List<String> strings = IntStream.rangeClosed(1, 10_000_000)
        .mapToObj(Integer::toString)
        .collect(toList());
long runTime = System.currentTimeMillis() - launchTime;
System.out.println("Generated " + strings.size() + " strings in " + runTime + " ms.");

launchTime = System.currentTimeMillis();
String appended = strings.stream()
        .limit(100_000)
        .reduce("", (left, right) -> left + right);
runTime = System.currentTimeMillis() - launchTime;
System.out.println("Created string of length " + appended.length() + " in " + runTime + " ms."); 
```

首先，它创建了一个包含 1000 万个字符串的列表，然后以一种极其简单的方式将前 10 万个字符串串联起来。实际上，无论是使用紧凑字符串(这是 Java 9 的默认设置)还是不使用紧凑字符串(使用`-XX:-CompactStrings`)运行代码，我都观察到了相当大的差异:

```
# with compact strings
Generated 10000000 strings in 1044 ms.
Created string of length 488895 in 3244 ms.
# without compact strings
Generated 10000000 strings in 1075 ms.
Created string of length 488895 in 7005 ms. 
```

现在，每当有人谈论这样的微基准时，如果他们不使用 [JMH](http://openjdk.java.net/projects/code-tools/jmh/) ，你应该立刻怀疑他们。但是在这种情况下，我不想经历用 Java 9 运行 JMH 的潜在麻烦，所以我采取了简单的方法。这意味着结果可能是完全垃圾，因为一些优化或其他拧我了。因此，以一卡车盐的结果为例，将它们视为第一个迹象，而不是性能提高的证据。

但你不必相信我。在上面链接的谈话中，Aleksey 展示了他的测量结果，[从 36:30](https://www.youtube.com/watch?v=wIyeOaitmWM&t=36m30s) 开始，引用了 1.36 倍的更高吞吐量和 45 %的更少垃圾。

### 独立字符串连接

快速重复一下字符串串联的工作原理…假设您编写了以下代码:

```
String s = greeting + ", "  + place + "!"
```

然后，编译器将创建字节码，该字节码使用`StringBuilder`来创建`s`，首先附加各个部分，然后调用`toString`来获得结果。在运行时，JIT 编译器可以识别这些附加链，如果这样做，它可以大大提高性能。它将生成检查参数长度的代码，创建一个正确大小的数组，将字符直接复制到该数组中，然后，瞧，把它包装成一个字符串。

没有比这更好的了，但是识别这些附加链并证明它们可以被优化的代码所取代并不容易，而且很快就会崩溃。显然，你所需要的只是连接中的一个`long`或一个`double`，而 JIT 将无法优化。

但是为什么要这么努力呢？为什么不干脆有一个字节码调用的方法`String.concat(String... args)`？因为在性能关键路径上创建 varargs 数组不是最好的主意。此外，除非你事先把它们都`toString`出来，否则原语并不能很好地适应这种情况，这反过来又阻止了把它们直接字符串化到目标数组中。而且想都别想`String.concat(Object... args)`，哪个会箱*每个*原始人。

因此，需要另一种解决方案来获得更好的性能。下一个最好的方法是让`javac`发出更好的字节码，但是这也有缺点:

*   每次实现新的优化，字节码都会再次改变。
*   为了让用户从这些优化中获益，他们必须重新编译他们的代码——如果可行的话，这是 Java 通常会避免的。
*   因为所有的 JVM 都应该能够 JIT 编译所有的变体，所以测试矩阵爆炸了。

那么还能做什么呢？也许，这里缺少了一个抽象概念？字节码就不能声明“连接这些东西”的意图，让 JVM 处理剩下的事情吗？

是的，这几乎是 [JEP 280](http://openjdk.java.net/jeps/280) 采用的解决方案——至少对于前一部分。由于`invokedynamic` 的[魔力，字节码可以表达意图和参数(不需要装箱)，但是 JVM 不必提供该功能，而是可以路由回 JDK 进行实现。这很好，因为在 JDK 中，所有类型的私有 API 都可以用于各种技巧(`javac`只能使用公共 API)。](http://www.javaworld.com/article/2860079/learn-java/invokedynamic-101.html)

让我再一次向您推荐 Aleksey 的演讲——后半部分，[从 37:58](https://www.youtube.com/watch?v=wIyeOaitmWM&t=37m58s) 开始，涵盖了这一部分。它还包含一些数字，显示速度提高了 2.6 倍，垃圾减少了 70%——而且这还不包括紧凑字符串！

### 又一个大杂烩

还有另一个与字符串相关的改进，但这个我不太明白。据我所知，不同的 JVM 进程可以通过类数据共享(CDS)档案共享加载的类。在这些档案中，类数据(更准确地说，常量池)中的字符串被表示为 UTF-8 字符串，并根据需要转换为`String`实例。通过不总是创建新的实例，而是在不同的 JVM 之间共享它们，可以减少内存占用。为了让垃圾收集器与这种机制合作，它需要提供一个名为*钉住区域*的特性，这个特性只有 G1 才有。这种理解似乎与 JEP 的标题**将互联网字符串存储在 CDS 档案**相冲突，所以如果你对此感兴趣，你应该自己看看。( [JEP 250](http://openjdk.java.net/jeps/250) )

Java 并发的一个基本构建块是监视器——每个对象有一个监视器，每个监视器一次最多可以被一个线程*拥有*。一个线程要获得一个监视器的所有权，它必须调用该对象声明的一个`synchronized`方法，或者进入一个在该对象上同步的`synchronized`块。如果几个线程试图这样做，除了一个之外，所有线程都被放入*等待集*中，监视器被称为*争用*，这就产生了一个性能瓶颈。首先，应用程序本身会浪费时间等待，但除此之外，JVM 还必须做一些工作来协调**锁争用**并在监控器再次可用时选择一个新线程。JVM 的这种编排是经过改进的，这将提高高度竞争的代码的性能。( [JEP 143](http://openjdk.java.net/jeps/143) )

在 Java 2D 中，所有的反走样(字体除外)都是由所谓的光栅化器执行的。这是一个内部子系统，没有可供 Java 开发人员使用的 API。但它位于热路径上，其性能对于许多图形密集型应用程序至关重要。OpenJDK 使用 Pisces，Oracle JDK 使用 Ductus，前者的性能比后者差得多。Pisces 现在将被替换为马林图形渲染器，它承诺在相同的质量和精度下有更好的性能。Marlin 很可能在质量、准确性和单线程性能方面与 Dustus 相匹配，甚至在多线程场景中超过 Dustus。( [JEP 265](http://openjdk.java.net/jeps/265) ，部分[历史与脉络](https://github.com/bourgesl/marlin-renderer/wiki/History-and-context))

坊间证据表明，使用活动的**安全管理器**运行应用程序会降低 10 %到 15 %的性能。人们努力通过各种小的优化来缩小这一差距。( [JEP 232](http://openjdk.java.net/jeps/232)

SPARC 和英特尔 CPU 最近推出了非常适合加密操作的指令。这些用于提高 **GHASH 和 RSA** 计算的性能。( [JEP 246](http://openjdk.java.net/jeps/246)

![car-repair-inside-java-9-performance-compiler](img/c00fc038e33c90a426973ea5a1132e08.png)

## 碎片帐集

Java 9 最有争议的变化之一，仅次于竖锯项目，是垃圾优先(G1)将成为新的默认垃圾收集器( [JEP 248](http://openjdk.java.net/jeps/248) )。幸运的是，从 Java 8 开始，它就已经可以投入生产了，所以我现在不必真的讨论它。

快速总结:G1 限制暂停时间，并为此放弃一些吞吐量。在实现方面，它没有将堆分成连续的空间，如*伊甸园*、*年轻*和*年老*，而是分成固定大小的区域，G1 在开始使用某个区域时为其分配一个角色，并在收集到该区域的全部内容后重置该角色。说到收集，那些集中在垃圾最多的区域，因此得名，因为它承诺的工作量最少。

G1 是一个有趣的野兽，我建议花一些时间来看看它。如果你不想自己做那件事，留下来，因为这个频道将很快讨论它。我发现的一个很好的细节是字符串重复数据删除(由 [JEP 192](http://openjdk.java.net/jeps/192) 在 8u20 中引入)，其中 G1 将识别具有相同`value`数组的`String`实例，然后让它们共享同一个数组实例。显然，重复的字符串很常见，这种优化保护了大约 10 %的堆空间——不过，这是在压缩字符串之前，所以现在可能接近 5 %。

最后， [JEP 214](http://openjdk.java.net/jeps/214) 移除了一些 [JEP 173](http://openjdk.java.net/jeps/173) 弃用的 GC 选项。

## 编译程序

### 为旧的 Java 版本编译

您是否曾经使用`-source`和`-target`选项来编译您的代码以便在一个旧的 JRE 上运行，结果却发现它在运行时崩溃了，因为一些方法调用由于一个看似无法解释的错误而失败了？一个可能的原因是您忘记指定`-bootclasspath`。因为如果没有这一点，编译器将链接到当前版本的核心库 API，这会使字节码与旧版本不兼容。为了修复这个常见的操作错误，Java 9 编译器附带了一个`--release`标志，将其他三个选项设置为正确的值。

### JVM 编译器接口

我发现这非常有趣！ [JEP 243](http://openjdk.java.net/jeps/243) 开发了 **Java 虚拟机编译器接口**(JVMCI)——一组 Java 接口，JVM 可以使用这些接口的实现来执行实时编译，从而取代 C1/C2 编译器。这仍然是一个实验性的特性，必须在命令行上显式激活，但是轨迹是清楚的:拥有一个用 Java 实现的 JIT 编译器。一个可能的候选是由 [Graal 项目](http://openjdk.java.net/projects/graal/)开发的，它已经实现了 JVMCI。

以防你问自己“但是为什么？”以下是 JEP 对此的看法:

> 优化编译器是一个复杂的软件，它极大地受益于 Java 提供的特性，例如自动内存管理、异常处理、同步、优秀的(和免费的)ide、优秀的单元测试支持和通过服务加载器的运行时可扩展性等等。此外，编译器不需要许多其他 JVM 子系统所需的低级语言特性，如字节码解释器和垃圾收集器。这些观察强烈地表明，用 Java 编写 JVM 编译器应该可以生产出高质量的编译器，比用 C 或 C++开发的现有编译器更容易维护和改进。

有道理，对吧？

### 超前编译

Java 就是“编写一次，在任何地方运行”这很好，但是如果你不愿意为此付费呢？如果你想为一个方法调用启动一个 JVM(有人说过[无服务器](http://martinfowler.com/articles/serverless.html)吗？)，那么 JIT 不会给你带来太多好处——为了获得最佳性能，你甚至在启动之前就需要机器码。

进入提前编译( [JEP 295](http://openjdk.java.net/jeps/295) )！有了它，您可以使用本地 JDK 附带的 Graal 编译器来编译您将要使用的代码，然后告诉`java`使用那些工件，而不是它周围的字节码。下面是 JEP 的一个小片段，它编译用户代码和所需的 JDK 模块，并使用它们启动 Java:

```
jaotc --output libHelloWorld.so HelloWorld.class
jaotc --output libjava.base.so --module java.base
java9 -XX:AOTLibrary=./libHelloWorld.so,./libjava.base.so HelloWorld 
```

这提出了一些问题:

*   如果一个模块的字节码和机器码版本不对齐怎么办？
*   如果运行时启动时使用的 VM 参数不同于代码编译时使用的参数，会怎么样？(例如，压缩对象指针。)
*   编译后的代码应该收集分析信息以允许进一步的优化吗？

当然，这些问题以及更多的问题正在得到解决，JEP 是一个很好的答案来源。

很像 JVMCI，这显然是一个实验性的特性(我甚至没有在最近的版本中发现它),它仍然有严重的限制——最明显的是它只能在 64 位 Linux 系统上工作，并且只能编译`java.base`模块。但这是 Java 发展的一个有趣方向。

### 中间部分

编译器的性能也有所提升。在某些情况下(例如对于嵌套的 lambdas ),类型推理会有一个指数运行时间——这并不好。分层归属解决了这个问题。( [JEP 215](http://openjdk.java.net/jeps/215) 、 [2 分钟视频总结](https://www.youtube.com/watch?v=vKYzmIi_1LM&t=48m25s))

由于注释管道是为 Java 5 创建的，所以必须对其进行多次扩展，以适应新的特性，如重复注释、类型注释以及 lambda 表达式带来的新的语法有效位置。“[它]不能开箱即用地处理此类案件；结果，为了适应新的用例，原始设计被拉长了，导致实现变得脆弱且难以维护。”对于 Java 9 来说，一个完整的重新设计,**注解管道 2.0** 被创建和实现。它没有增加任何特性，但是应该为将来的扩展提供一个更好的基础。( [JEP 217](http://openjdk.java.net/jeps/217) ，[标注管道 2.0 项目](http://openjdk.java.net/projects/anno-pipeline/))

然后是关于启用**运行时可管理的、方法相关的编译器标志**，这我一点也不明白。( [JEP 165](http://openjdk.java.net/jeps/165) )

最后， [JEP 237](http://openjdk.java.net/jeps/237) 将用于 **Linux/AArch64** 的 JDK 9 端口集成到 OpenJDK 中。

## JavaDoc

你看过 Java 9 的临时 Javadoc 吗？你注意到什么新的东西了吗？如果没有，现在去[那里](http://download.java.net/java/jdk9/docs/api/)，找到右上角的文本框，开始输入一个 JDK 类的名字。整洁，对不对？( [JEP 225](http://openjdk.java.net/jeps/225) )

Javadoc 现在可以生成 HTML 5 页面，包括“结构元素，如*标题*、*页脚*、*导航*等。”由于 ARIA 的出现，可访问性得到了提高。( [JEP 224](http://openjdk.java.net/jeps/224) )

把最好的留到最后，至少关于 JavaDoc，我想以新的 Doclet API 结束。Doclets 是 Javadoc 插件，你可以自己创建来处理你的 JavaDoc 注释(你是在[注释你的代码](http://blog.codefx.org/techniques/documentation/comment-your-fucking-code/)，对吗？).旧的 API 非常深奥，最有趣的特性是你必须用正确的名字创建静态方法，这样工具才能调用你的插件。(那是在 interfaces 之前还是什么？)新的 API 消除了这种疯狂。它还提供了对[语言模型 API](https://docs.oracle.com/javase/8/docs/api/javax/lang/model/package-summary.html) 和[文档树 API](https://docs.oracle.com/javase/8/docs/jdk/api/javac/tree/com/sun/source/doctree/package-summary.html) 的访问，让你浏览源代码并创建输出。( [JEP 224](http://openjdk.java.net/jeps/224) )

## 不再有了！

Java 9 之旅到此结束。忽略一些小事，这三篇文章展示了 Java 9 提供的一切:

*   Java 9 的终极指南:语言特性，面向用户的 API，移除
*   Java 9 内部-第一部分:JVM 的各种更新和扩展
*   Java 9 内幕–第二部分:性能改进、GC、编译器、JavaDoc

但是到目前为止，我们只是触及了表面——在接下来的几个月中，我们将发布更多关于 Java 9 的内容，深入到任何主题的细节中。因此，关注这个空间，例如通过 [RSS](https://www.sitepoint.com/java/) ，或者[订阅](https://go.sitepoint.com/h/y/43CDB766C5398A3B)我们的时事通讯。

## 分享这篇文章