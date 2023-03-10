# Java 9 终极指南

> 原文：<https://www.sitepoint.com/ultimate-guide-to-java-9/>

通过我们在 SitePoint 上的教程[在 Android 开发中桥接 Android 和 Java](https://www.sitepoint.com/premium/screencasts/bridging-android-and-java-in-android-development)，了解更多关于 Java 的知识。

### 目录 

*   [Java 平台模块系统](#javaplatformmodulesystem)
*   [语言变化](#languagechanges)
*   [私有接口方法](#privateinterfacemethods)
*   [有效最终变量的资源尝试](#trywithresourcesoneffectivelyfinalvariables)
*   [匿名类的菱形运算符](#diamondoperatorforanonymousclasses)
*   [私有方法的安全变量](#safevarargsonprivatemethods)
*   [不再有关于进口商品的贬值警告](#nomoredeprecationwarningsforimports)
*   [API](#apis)
*   [操作系统进程](#os-processes)
*   [多分辨率图像](#multiresolutionimages)
*   [堆垛行走](#stackwalking)
*   [重定向平台日志](#redirectedplatformlogging)
*   [反应流](#reactivestreams)
*   [集合工厂方法](#collectionfactorymethods)
*   [原生桌面集成](#nativedesktopintegration)
*   [反序列化过滤器](#deserializationfilter)
*   [联网](#networking)
*   [XML](#xml)
*   [对现有 API 的扩展](#extensionstoexistingapis)
*   [低级 API](#lowlevelapis)
*   [变量句柄又名变量句柄](#variablehandlesakavarhandles)
*   [增强的方法句柄](#enhancedmethodhandles)
*   [动态链接](#dynalink)
*   [Nashorn 解析器 API](#nashornparserapi)
*   [旋转等待提示](#spinwaithints)
*   [说再见的 APIs】](#apisthatgobyebye)
*   但是等等，还有更多！
*   [评论](#comments)

Java 9 来了！离预定的发布还有六个月，除了模块系统，它还带来了一些新的语言特性和许多新的改进的 API。我们需要看一看我们可以玩的所有闪亮的新东西，所以这里是 Java 9 的最终指南。(顺便说一句，你可以在 GitHub 上找到本文[中的一些片段。)](https://github.com/CodeFX-org/demo-java-9)

## Java 平台模块系统

首先，让我们把最大的大象踢出房间:Java 平台模块系统(JPMS)无疑是 Java 9 的旗舰特性，关于它已经写了很多:在模块系统的宏伟[状态下，在这个网站上当](http://openjdk.java.net/projects/jigsaw/spec/sotms/)[我们在我的博客](https://www.sitepoint.com/jvmls-2016/)、[上](http://blog.osgi.org/2016/08/osgi-with-java-modules-all-way-down.html)、[上](http://mydailyjava.blogspot.de/2015/12/project-jigsaw-incomplete-puzzle.html)[其他](http://blog.osgi.org/2016/08/osgi-with-java-modules-all-way-down.html)、，甚至[维基百科上也有一篇文章](https://en.wikipedia.org/wiki/Java_Module_System)——没用但还是。

有了所有这些来源，我们不必在这里重复任何东西——所以我们不会。相反，让我们转到不太为人所知的特性。而且有这么多！

## 语言变化

当 Sun 资金不足时，Java 7 只对语言做了一些小的改动。出于一种讽刺的幽默，包含它们的项目被命名为[项目硬币](http://openjdk.java.net/projects/coin/)。对于 Java 9 [JEP 213:碾磨工程硬币](http://openjdk.java.net/jeps/213)提炼这些和其他细节。

### 私有接口方法

Java 8 将[默认方法](http://blog.codefx.org/java/everything-about-default-methods/)带到了桌面上，这样[接口就可以进化为](http://blog.codefx.org/design/patterns/interface-evolution-with-default-methods-methods/)。这进行得相当顺利，但是有一个不幸的细节:在默认方法之间重用代码是令人不快的。

提取的方法要么必须是默认方法，必须是公共的，要么进入某个助手类以保持它们的私有性:

```
public interface InJava8 {

    default boolean evenSum(int... numbers) {
        return sum(numbers) % 2 == 0;
    }

    default boolean oddSum(int... numbers) {
        return sum(numbers) % 2 == 1;
    }

    // we don't want this to be public;
    // but how else do we resuse?
    default int sum(int[] numbers) {
        return IntStream.of(numbers).sum();
    }

} 
```

在 Java 9 中，我们可以简单地拥有一个私有接口方法:

```
public interface InJava9 {

    // as above

    private int sum(int[] numbers) {
        return IntStream.of(numbers).sum();
    }

} 
```

整洁，嗯？

### 有效最终变量的资源尝试

你做过类似的事情吗？

```
void doSomethingWith(Connection connection) throws Exception {
    try(Connection c = connection) {
        c.doSomething();
    }
} 
```

变量`c`之所以在这里，是因为 try-with-resources 语句的语法需要它——托管资源必须在语句头中声明。Java 9 放松了这一点:现在可以管理任何资源，只要它是最终有效的 T2。

所以在 Java 9 中我们可以直接使用`connection`:

```
void doSomethingWith(Connection connection) throws Exception {
    try(connection) {
        connection.doSomething();
    }
} 
```

### 匿名类的菱形运算符

假设我们有一个简单的类`Box<T>`,在某一点上，我们想为它创建一个匿名子类。在 Java 8 中，我们必须这样做:

```
<T> Box<T> createBox(T content) {
    // we have to put the `T` here :(
    return new Box<T>(content) { };
} 
```

这不是明摆着应该是一盒`T`吗？我的意思是，如果我们没有创建匿名子类，编译器可以推断出类型，那么为什么它不能在这里做同样的事情呢？

原因是*不可命名的类型*——编译器理解但 JVM 不理解的类型。在这种情况下，编译器[可能推断出一个不可命名的类型](http://mail.openjdk.java.net/pipermail/coin-dev/2010-April/002725.html)，但是不知道如何为 JVM 表达它。所以钻石操作者被[彻底拒绝](http://mail.openjdk.java.net/pipermail/coin-dev/2011-June/003283.html)匿名类。

Java 9 放宽了这一限制，如果推断出可命名类型，则允许使用菱形:

```
class inJava {

    <T> Box<T> createBox(T content) {
        // Java 9 can infer `T` because it is a denotable type
        return new Box<>(content) { };
    }

    Box<?> createCrazyBox(Object content) {
        List<?> innerList = Arrays.asList(content);
        // we can't do the following because the inferred type is non-denotable:
        // return new Box<>(innerList) { };
        // instead we have to denote the type we want:
        return new Box<List<?>>(innerList) { };
    }

} 
```

### 私有方法的 SafeVarargs

你知道`@SafeVarargs`吗？你可以用它来告诉编译器，你的特殊混合变量[和](http://docs.oracle.com/javase/1.5.0/docs/guide/language/varargs.html)[泛型](https://docs.oracle.com/javase/tutorial/java/generics/)是安全的。(如果你在问自己，为什么会不安全，看看 StackOverflow 上这个很棒的 [Q & A。)典型的用法如下:](http://stackoverflow.com/a/14252221/2525313)

```
@SafeVarargs
public static <T> Optional<T> first(T... args) {
    if (args.length == 0)
        return Optional.empty();
    else
        return Optional.of(args[0]);
} 
```

`@SafeVarargs`只能应用于不能被覆盖的方法([原因](https://bugs.openjdk.java.net/browse/JDK-7196160))。这显然包括静态、最终和私有方法以及构造函数。是吗？没有明显的原因，私有的、非 final 的方法以前不能被注释，Java 9 解决了这个问题。少了一个烦恼。

### 不再有关于导入的不赞成警告

如果您维护一个旧的项目，但是希望有一个无警告的构建，您可能会遇到一个令人烦恼的事实，即导入不推荐使用的类型会导致警告。

下面的类做了所有正确的事情:虽然它使用了一个不推荐使用的类型，但它本身也是不推荐使用的，所以看起来没有理由得到警告。

```
import java.io.LineNumberInputStream;

@Deprecated
public class DeprecatedImports {

    LineNumberInputStream stream;

} 
```

但是在 Java 8 中我们做到了！WTF？！好了，`@SuppressWarnings("deprecation")`来救援了！唉，导入不能被注释，注释`stream`声明也没有帮助。悲伤的熊猫。

再一次，一个小的深思熟虑的调整就是 Java 9 闪耀的全部:导入一个被否决的类型不再导致警告，所以上面的类是没有警告的。

## 蜜蜂

我不得不承认，与 Java 8 中的默认方法和 lambda 表达式相比，新的语言特性有点不起眼。但是对于 API 的改进却不是这样！《JDK》增加了许多有思想的、有趣的内容，你应该知道。

### 操作系统进程

JEP 102 扩展了流程 API，现在支持与系统级流程更丰富的交互。新的 [`ProcessBuilder`](http://download.java.net/java/jdk9/docs/api/java/lang/ProcessBuilder.html) 使构建流程甚至管道变得更加方便。

想调用`ls`，把结果管道到`grep`？没问题:

```
ProcessBuilder ls = new ProcessBuilder()
        .command("ls")
        .directory(Paths.get("/home/nipa/tmp").toFile());
ProcessBuilder grepPdf = new ProcessBuilder()
        .command("grep", "pdf")
        .redirectOutput(Redirect.INHERIT);
List<Process> lsThenGrep = ProcessBuilder
        .startPipeline(asList(ls, grepPdf)); 
```

同样新的 [`ProcessHandle`](http://download.java.net/java/jdk9/docs/api/java/lang/ProcessHandle.html) 使得与流程和流程树的交互更加方便。
`Process`实现了它的所有功能，只是在名义上，因为有几个返回类型不匹配。要得到适当的处理，请致电`Process::toHandle`。

想在将 PID 写入控制台之前等待两个进程都完成吗？给你:

```
CompletableFuture[] lsThenGrepFutures = lsThenGrep.stream()
        // onExit returns a CompletableFuture<Process>
        .map(Process::onExit)
        .map(processFuture -> processFuture.thenAccept(
                process -> System.out.println("PID: " + process.getPid())))
        .toArray(CompletableFuture[]::new);
// wait until all processes are finished
CompletableFuture
        .allOf(lsThenGrepFutures)
        .join(); 
```

更多信息请查看 Steffen Jacobs 关于扩展流程 API 的帖子。

![Java 9 - as hip as Shibuya](img/cf3cc498e38e1a70dda82efd4db76d5b.png)

[发表](https://commons.wikimedia.org/wiki/File:Shibuya_Night_(HDR).jpg "Source on Wikimedia")由[谷娃师](http://www.flickr.com/people/16877319@N07 "Author on Flickr")在 [CC-BY 2.0](https://creativecommons.org/licenses/by/2.0/ "Link to Licence")

### 多分辨率图像

[JEP 251](http://openjdk.java.net/jeps/251) 介绍 [`MultiResolutionImage`](http://download.java.net/java/jdk9/docs/api/java/awt/image/MultiResolutionImage.html) ，封装了一组不同分辨率的图像，并允许以所需的高度和宽度进行查询。提供了一个[抽象](http://download.java.net/java/jdk9/docs/api/java/awt/image/AbstractMultiResolutionImage.html)和一个[简单实现](http://download.java.net/java/jdk9/docs/api/java/awt/image/BaseMultiResolutionImage.html)。

```
public static void main(String[] args) throws IOException {
    MultiResolutionImage tokio = loadTokio();
    int desiredImageWidth = new Random().nextInt(1500);
    Image variant = tokio.getResolutionVariant(desiredImageWidth, 1);

    System.out.printf("Width of image for %d: %d%n",
            desiredImageWidth, variant.getWidth(null));
}

private static MultiResolutionImage loadTokio() throws IOException {
    List<Image> tokios = new ArrayList<>();
    for (String url : IMAGE_URLS) {
        tokios.add(ImageIO.read(new URL(url)));
    }
    return new BaseMultiResolutionImage(tokios.toArray(new Image[0]));
} 
```

### 堆栈审核

为了能够在不创建完整的(因此昂贵的)快照的情况下遍历堆栈， [JEP 259](http://openjdk.java.net/jeps/259) 引入了 [`StackWalker`](http://download.java.net/java/jdk9/docs/api/java/lang/StackWalker.html) 。在改进的 JVM 功能的支持下，它创建了一个堆栈帧流，可以根据需要对其进行延迟评估。这使我们能够利用 Stream API 的过滤和映射能力，同时使短途旅行表现得更好。

这里我们进行了几次调用，然后遍历堆栈，直到找到属于特定方法的帧:

```
public static void main(String[] args) { one(); }

static void one() { two(); }

static void two() { three(); }

static void three() {
    String line = StackWalker.getInstance().walk(StackWalking::walk);
    System.out.println(line);
}

private static String walk(Stream<StackFrame> stackFrameStream) {
    return stackFrameStream
            .filter(frame -> frame.getMethodName().contains("one"))
            .findFirst()
            .map(frame -> "Line " + frame.getLineNumber())
            .orElse("Unknown line");
} 
```

`StackWalker::walk`采用从`Stream<StackFrame>`到所需结果的函数。这有点不寻常，为什么它不简单地返回流呢？引用 JEP 的话:

> 以不受控制的方式返回包含堆栈指针的流以供进一步操作是行不通的，因为一旦流工厂返回，JVM 就可以自由地重组控制堆栈(例如，通过去优化)。

相反,`StackWalker::walk`创建流，将它传递给我们传递的函数，并在函数返回时关闭它。所以我们也不能存储流以供以后处理。

### 重定向平台日志记录

JDK 类使用他们自己的日志基础设施，这一事实让你感到沮丧吗？将这些消息通过管道传输到应用程序使用的日志后端不是很酷吗？如果是这样，那么你很幸运，因为这正是 JEP 264 所实现的。

JDK 类现在通过新的接口 [`Logger`](http://download.java.net/java/jdk9/docs/api/java/lang/System.Logger.html) (直接或者绕道`sun.util.logging.PlatformLogger`)传输消息——这些实例由单个 [`LoggerFinder`](http://download.java.net/java/jdk9/docs/api/java/lang/System.LoggerFinder.html) 提供。反过来，查找器——这是有趣的一点——是使用[服务加载器 API](http://download.java.net/java/jdk9/docs/api/java/util/ServiceLoader.html) 定位的。这使得 Log4J 或 Logback 之类的日志后端能够提供一个查找器，该查找器创建将消息转发到后端的记录器。

设置一切非常简单——查看[演示](https://github.com/CodeFX-org/demo-java-9/tree/master/src/org/codefx/demo/java9/api/platform_logging)。

### 反应流

反应式原则正在兴起，许多不同的库和框架都在实现它们。为了提高它们的互操作性 [JEP 266](http://openjdk.java.net/jeps/266) 包含了一个最小的接口集，它抓住了异步发布和订阅的核心。希望将来第三方能够实现它们，从而在一组共享的类型上集合。

他们在这里:

`Publisher`

Produces items for subscribers to consume. The only method is `subscribe(Subscriber)`, whose purpose should be obvious.

`Subscriber`

Subscribes to publishers (usually only one) to receive items (via method `onNext(T)`), error messages (`onError(Throwable)`), or a signal that no more items are to be expected (`onComplete()`). Before any of those things happen, though, the publisher calls `onSubscription(Subscription)`.

`Subscription`

The connection between a single publisher and a single subscriber. The subscriber will use it to request more items (`request(long)`) or to sever the connection (`cancel()`).

流程如下:

1.  创建一个`Publisher`和一个`Subscriber`。
2.  用`Publisher::subscribe`为订阅者订阅。
3.  发布者创建一个`Subscription`并用它调用`Subscriber::onSubscription`,这样订阅者就可以存储订阅。
4.  在某个时刻，订户调用`Subscription::request`来请求一些项目。
5.  发布者通过调用`Subscriber::onNext`开始将项目交给订阅者。它永远不会发布超过请求数量的项目。
6.  发布者可能在某个时间耗尽或陷入困境，并分别调用`Subscriber::onComplete`或`Subscriber::onError`。
7.  订户可以继续不时地请求更多的项目，或者通过调用`Subscription::cancel`来切断连接。

所有这些都很简单，也许除了`Subscription::request`。为什么订户需要这样做？这是[背压](http://www.reactivemanifesto.org/glossary#Back-Pressure)的实现，一种消费者可以向生产者反馈他们可以处理多少项目的机制。如果没有这样的机制，生产者可能很容易压倒消费者，迫使他们丢弃项目或拥有无限的队列，在队列中存储未处理的项目。这两种解决方案都不稳定，因此如果消费者跟不上需求，就引入背压来抑制产品生产。

注意，JDK 只提供接口，不提供实现(除了 [`SubmissionPublisher`](http://download.java.net/java/jdk9/docs/api/java/util/concurrent/SubmissionPublisher.html) )！也没有为诸如遍历文件系统这样的异步任务创建发布者。所有描述的接口都是类 [`Flow`的内部类型，有很好的介绍文档](http://download.java.net/java/jdk9/docs/api/java/util/concurrent/Flow.html)。还有[一个官方的 GitHub 项目](https://github.com/reactive-streams/reactive-streams-jvm/tree/v1.0.0)，里面包含了 API 的代码和[一个详细的规范。我创建了一个简单的例子](https://github.com/reactive-streams/reactive-streams-jvm/tree/v1.0.0#specification)[，它也可以阐明正在发生的事情。](https://github.com/CodeFX-org/demo-java-9/tree/master/src/org/codefx/demo/java9/api/reactive_streams)

### 集合工厂方法

曾经有希望使用集合文字。这不是很好吗？

```
List<String> list = [ "a", "b", "c" ];
Map<String, Integer> map = [ "one" = 1, "two" = 2, "three" = 3 ]; 
```

嗯，尽管看起来很棒，但这并不是小事，而且有很好的理由不这么做。下一个最好的是什么？工厂方法！感谢 [JEP 269](http://openjdk.java.net/jeps/269) 我们现在能做到这一点:

```
List<String> list = List.of("a", "b", "c");
Map<String, Integer> mapImmediate = Map.of(
        "one", 1,
        "two", 2,
        "three", 3);
Map<String, Integer> mapEntries = Map.ofEntries(
        entry("one", 1),
        entry("two", 2),
        entry("three", 3)); 
```

也不错。这将使特别集合的创建更加方便，尤其是对于静态字段。关于这些集合，有一些有趣的事情需要注意。

首先，它们都是不可改变的。没错，`List.of("a").add("b")`失败(带一个`UnsupportedOperationException`)。我喜欢它:这些工厂方法将经常被用来初始化具有固定元素集合的字段，其中可变性在大多数情况下是不希望的。(如果您听说 Java 9 将支持不可变集合，那么请注意这就是它。不变性没有进入类型系统。)

工厂方法完全拒绝`null`值。不管是作为元素，作为键，作为值，它们都是被禁止的。好东西，打倒 null！

最后，这有点疯狂，这是随机的。实现这些集合是为了最大化性能，并简单地将其元素存储在字段(最多两个元素)或固定大小的数组(更多)中。这意味着不管`hashCode`实现如何，它们的迭代顺序在所有机器上都是相同的。这将大大增加程序逻辑的某些部分无意中依赖于这个顺序的可能性。

为了防止这种情况，项目的顺序在每次程序运行时都是随机的。在每次启动时，随机选取一个盐，然后在整个运行过程中使用它来在构造期间将元素放置在数组中。这使得这个过程在运行期间是确定性的*(因此对`Set.of("a", "b", "c")`的两个调用将总是具有相同的顺序)，但在*运行期间是随机的*。*

### 本机桌面集成

Java 桌面应用程序总是惹人注目。这种情况不会很快改变，但是 JEP 272 让它变得不那么明显。它的目标是“定义一个新的公共 API 来访问特定于平台的桌面特性，比如与任务栏或 dock 交互，或者监听系统或应用程序事件。”在 Linux 上只支持 Unity，所以我不能尝试，但代码看起来很有前途。

让我们以任务栏为例。以下是一些可用的功能:

*   应用程序可以请求注意，这将导致任务栏图标跳动或闪烁。
*   特定于应用程序的菜单可以添加到任务栏图标的上下文菜单中。
*   任务栏图标可以通过编程来更改。
*   文本或数字徽章可以动态添加到任务栏图标。
*   任务栏条目可以显示进度条和状态，如*关闭*、*暂停*或*错误*。
*   窗口预览可以有图标标记。

(注意，并非所有平台都支持所有功能。一个方便的`Taskbar::isSupported`方法允许检查哪些是可用的。)

更多请看[新包`java.awt.desktop`](http://download.java.net/java/jdk9/docs/api/java/awt/desktop/package-summary.html) 。

### 反序列化筛选器

很长一段时间以来，反序列化一直被认为是一个安全问题。 [JEP 290](http://openjdk.java.net/jeps/290) 旨在通过允许我们在同意反序列化之前审查字节流来改善这种情况。考虑了以下细节:

*   实例将被反序列化的类。
*   正在创建的数组的大小。
*   流度量:长度、流深度和引用数量。

可以配置基于这些细节的静态过滤器(通过命令行或属性文件)。它会将任何数值属性的类别和状态限制列入黑名单和/或白名单。或者，可以创建一个`ObjectInputFilter`，为每个序列化实例调用它，它可以动态评估相同的属性:

```
interface ObjectInputFilter {

    Status checkInput(Class<?> clazz,
            long size, long nRefs, long depth, long streamBytes);

    enum Status { UNDECIDED, ALLOWED, REJECTED; }
} 
```

不幸的是[问题](https://bugs.openjdk.java.net/browse/JDK-8154961)还没有实现，所以我们不能玩。

### 建立工作关系网

JDK 现在支持一些与网络相关的技术。这不是我的强项，所以我会给你一个有趣的名字和链接列表:

*   HTTP/2 ( [JEP 110](http://openjdk.java.net/jeps/110) ，[斯蒂芬·雅各布斯](http://blog.oio.de/2016/08/24/http2-client-java-9/)介绍)
*   数据报传输层安全(DTLS， [JEP 219](http://openjdk.java.net/jeps/219)
*   TLS 应用层协议协商扩展(TLS ALPN， [JEP 244](http://openjdk.java.net/jeps/244)
*   用于 TLS 的 OCSP 装订( [JEP 244](http://openjdk.java.net/jeps/244) )

### 可扩展标记语言

[JEP 268](http://openjdk.java.net/jeps/268) 实现了 [OASIS XML 目录标准 v1.1](https://www.oasis-open.org/committees/download.php/14809/xml-catalogs.html) 。它定义了“可以与接受解析器的 JAXP 处理器一起使用的目录和目录解析器抽象”。请注意，“使用内部 API 的现有库或应用程序将需要迁移到新的 API，以便利用新的功能”。可以在 [`javax.xml.catalog`](http://download.java.net/java/jdk9/docs/api/javax/xml/catalog/package-summary.html) 中找到。

JAXP 也得到了提升: [JEP 255](http://openjdk.java.net/jeps/255) 将 Xerces 2.10.0 和 2.11.0 之间的选定更改合并到 JDK 中。

### 对现有 API 的扩展

一些现有的 API 也得到了一些喜爱。仅举几个例子:

*   [`Optional`](http://download.java.net/java/jdk9/docs/api/java/util/Optional.html) ， [`Stream`](http://download.java.net/java/jdk9/docs/api/java/util/stream/Stream.html) ， [`Collectors`](http://download.java.net/java/jdk9/docs/api/java/util/stream/Collectors.html) 得到了几个新方法。这里有专门的岗位涵盖它们:`Optional` 的[、`Stream`](http://blog.codefx.org/java/dev/java-9-optional/) 的[、`Collectors`](http://blog.codefx.org/java/dev/java-9-stream/) 的[。](http://iteratrlearning.com/java9/2016/08/16/java9-collectors.html)
*   增加了多种返流方式，如 [`LocalDate::datesUntil`](http://download.java.net/java/jdk9/docs/api/java/time/LocalDate.html#datesUntil-java.time.LocalDate-) 或 [`Scanner::tokens`](http://download.java.net/java/jdk9/docs/api/java/util/Scanner.html#tokens--) /
    [`Scanner::findAll`](http://download.java.net/java/jdk9/docs/api/java/util/Scanner.html#findAll-java.util.regex.Pattern-) 。
*   Java 8 中引入的许多类型的 DateTime API 现在可以将自己转换为纪元秒，例如 [`Chronology`](http://download.java.net/java/jdk9/docs/api/java/time/chrono/Chronology.html#epochSecond-int-int-int-int-int-int-java.time.ZoneOffset-) 、 [`LocalDate`](http://download.java.net/java/jdk9/docs/api/java/time/LocalDate.html#toEpochSecond-java.time.LocalTime-java.time.ZoneOffset-) 和 [`OffsetTime`](http://download.java.net/java/jdk9/docs/api/java/time/OffsetTime.html#toEpochSecond-java.time.LocalDate-) 。
*   [`Duration`](http://download.java.net/java/jdk9/docs/api/java/time/Duration.html) 得到了一堆将它解释为`days:hours:minutes:seconds:ms:ns`并返回该解释的各个部分的方法。
*   `Matcher`获得了新的方法来用[替换火柴](http://download.java.net/java/jdk9/docs/api/java/util/regex/Matcher.html#replaceAll-java.util.function.Function-)和 [`Matcher::results`](http://download.java.net/java/jdk9/docs/api/java/util/regex/Matcher.html#results--) 现在返回一个`Stream<MatchResult>`。
*   小细节被添加到 IO: `InputStream`现在可以直接[转移到一个`OutputStream`](http://download.java.net/java/jdk9/docs/api/java/io/InputStream.html#transferTo-java.io.OutputStream-) 和`Buffer`可以[切片](http://download.java.net/java/jdk9/docs/api/java/nio/Buffer.html#slice--)和[复制](http://download.java.net/java/jdk9/docs/api/java/nio/Buffer.html#duplicate--)本身。
*   [`Atomic...`类](http://download.java.net/java/jdk9/docs/api/java/util/concurrent/atomic/package-summary.html)得到了一堆新方法，这些方法允许内存访问，其语义类似于现在用 [`VarHandles`](http://download.java.net/java/jdk9/docs/api/java/lang/invoke/VarHandle.html) 所能实现的。接入(`get`、`set`、`compareAndSet`和`compareAndExchange`)可以是普通的、不透明的、获得的或弱的，尽管不是所有的组合。

一些实用程序类也得到了改进:

*   在 [`Math`](http://download.java.net/java/jdk9/docs/api/java/lang/Math.html) 和 [`StrictMath`](http://download.java.net/java/jdk9/docs/api/java/lang/StrictMath.html) 中增加了算术方法，例如将`long`与`int` 相乘，如果发生溢出则例外。
*   [`Arrays`](http://download.java.net/java/jdk9/docs/api/java/util/Arrays.html) 现在可以比较所有类型的数组和数组片(即`a[3]-a[5]`和`b[2]-b[4]`)是否[相等](http://download.java.net/java/jdk9/docs/api/java/util/Arrays.html#equals-int:A-int-int-int:A-int-int-)，如果类型实现了`Comparable`或给定了`Comparator`，则比较[的字典顺序](http://download.java.net/java/jdk9/docs/api/java/util/Arrays.html#compare-int:A-int-int-int:A-int-int-)。很酷，如果有些不对劲， [`mismatch`](http://download.java.net/java/jdk9/docs/api/java/util/Arrays.html#mismatch-int:A-int-int-int:A-int-int-) 可以返回数组(或切片)转移的索引。
*   `Objects`不再只有[`requireNonNull`](http://download.java.net/java/jdk9/docs/api/java/util/Objects.html#requireNonNull-T-)——为了向`Optional`致敬，它得到了 [`requireNonNullElse`](http://download.java.net/java/jdk9/docs/api/java/util/Objects.html#requireNonNullElse-T-T-) 和 [`requireNonNullElseGet`](http://download.java.net/java/jdk9/docs/api/java/util/Objects.html#requireNonNullElseGet-T-java.util.function.Supplier-) ，它们不抛出异常，而是在测试值为空时返回默认值。它现在还推出了许多检查指数是否在理想范围内的方法。

## 低级 API

Java 9 带来了许多非常有趣的 API，这些 API 的操作比我们到目前为止讨论的更接近于金属(即 JVM)。让我们也快速地看一下它们——我们可能会学到一些全新的东西(我确实学到了)。

### 变量句柄又名 VarHandles

我已经力不从心了，所以让我引用 JEP 的话:

“随着 Java 中并发和并行编程的不断扩展，程序员越来越感到沮丧，因为他们不能使用 Java 构造来安排单个类的字段上的原子或有序操作；例如，自动递增计数字段。”这样做的现有可能性在某种意义上都是缺乏的:

*   类增加了“间接管理的空间开销和额外的并发问题”
*   `FieldUpdaters`往往导致“比操作本身更多的开销”
*   是不安全的——它既没有被标准化，也不受支持，在 Java 10 中将变得不可访问。

Java 9 引入了变量句柄，JEP 写道:“变量句柄是对变量的类型化引用，它支持在各种访问模式下对变量的读写访问。支持的变量种类包括实例字段、静态字段和数组元素。

这将解除[对`Unsafe`](https://www.infoq.com/articles/A-Post-Apocalyptic-sun.misc.Unsafe-World) 的一些压力。

### 增强的方法句柄

JEP 274 通过为循环、try/finally 块、参数处理以及接口方法的查找提供新的组合子，改进了方法句柄 API。

### 动态链接

Java 7 给语言带来了 [`invokedynamic`](http://docs.oracle.com/javase/7/docs/technotes/guides/vm/multiple-language-support.html#invokedynamic) :一条 JVM 指令，它通过回调语言来决定如何准确地动态执行调用。(与其他`invoke...`指令相反，它们的行为是在编译时静态定义的。)它本来是作为更动态的 JVM 语言的工具，但是 Java 本身开始严重依赖它。“indy”的第一个主要用途是 Java 8 中的 [lambda 表达式](http://blog.codefx.org/java/dev/lambdas-java-peek-hood/)。

在开发 [Nashorn](https://docs.oracle.com/javase/8/docs/technotes/guides/scripting/nashorn/toc.html) 的过程中，谨慎的做法是在它的基础上开发一个更高级别的 API。它表示类似“读取对象的属性”、“写入对象的属性”、“调用可调用对象”等操作。名字叫做 *Dynalink* 。现在 [JEP 276](http://openjdk.java.net/jeps/276) 让它成为 Java 9 中的一个公共 API 你可以在 [`jdk.dynalink`](http://download.java.net/java/jdk9/docs/jdk/api/dynalink/jdk/dynalink/package-summary.html#package.description) 包中找到它。

### Nashorn 解析器 API

说到 Nashorn……负责 JavaScript 的一切，它有一个解析器(很明显),驻留在内部包`jdk.nashorn.internal.ir`中——它并不打算供公众使用。但是工具仍然开始依赖它(最突出的是 IDEs 和往常一样),这阻止了 API 的自由发展，并成为 Jigsaw 强大封装的一个问题。

为了解决这一切， [JEP 236](http://openjdk.java.net/jeps/236) 为 Nashorn 创建了一个支持的解析器 API。你可以在 [`jdk.nashorn.api.tree`](http://download.java.net/java/jdk9/docs/jdk/api/nashorn/jdk/nashorn/api/tree/package-summary.html) 包里找到。

### 旋转等待提示

你写过这样的代码吗？

```
class EventHandler {
    volatile boolean eventNotificationReceived;

    void waitForEventAndHandleIt() {
        while (!eventNotificationReceived) { /* spinning */ }
        readAndProcessEvent();
    }

    void readAndProcessEvent() { /* ... */ }
} 
```

我也没有。但是一些开发者正在编写低级的东西，他们偶尔需要这样的循环。显然，一些硬件平台可以优化自旋循环的延迟和功耗，如果它们知道代码当前正在执行自旋循环的话。

这就是 [JEP 285](http://openjdk.java.net/jeps/285) 的用武之地。它声明了 [`Thread::onSpinWait`](http://download.java.net/java/jdk9/docs/api/java/lang/Thread.html#onSpinWait--) ，这个方法的主体是空的，但是 HotSpot 有一个内在的实现，在 C2 编译期间在 x86 机器上注入一个`pause`指令。您可以按如下方式使用它:

```
void waitForEventAndHandleIt() {
    while (!eventNotificationReceived) {
        Thread.onSpinWait()
    }
    readAndProcessEvent();
} 
```

现在 x86 机器将看到自旋循环，并能够优化它。

## 告别的 API

新事物来了…有时旧事物不得不离开。弃用一直是 Java 的一部分——事实上没有人真正知道如何对待它们。不推荐使用的 API 仅仅是不被鼓励还是有严重的负面影响？它们会被其他 API 取代吗？它们会被移除吗？

为了让事情更清楚一点， [JEP 277](http://openjdk.java.net/jeps/277) 通过给不赞成使用的注释两个标志来解决这个问题:`forRemoval`和`since`。一些现有的弃用已被更新以包括它们。还有一些新的反对意见，其中最重要的是:

*   applet API(浏览器中的 Java 还记得那个东西吗？)已被弃用，但尚未计划删除。( [JEP 289](http://openjdk.java.net/jeps/289) )
*   Corba 已被弃用。人们正在加热火坑，并期待着看到它很快燃烧。
*   原始包装器的显式构造函数(如`new Integer(5)`和`new Integer ("5")`)已经被弃用，取而代之的是工厂方法(`valueOf`或`parse...`)，因为它们使用内部化来提高性能。
*   [`Observer`](https://docs.oracle.com/javase/8/docs/api/java/util/Observer.html)[`Observable`](https://docs.oracle.com/javase/8/docs/api/java/util/Observable.html)已弃用。最后 API 有这么多漏洞，它还没有沉没真是个奇迹。
*   SHA-1 证书正在慢慢被淘汰。( [JEP 288](http://openjdk.java.net/jeps/288) )

如果你想知道更多关于还有什么被弃用，以及如何处理编译代码的弃用，你绝对应该看看 [JEP 277](http://openjdk.java.net/jeps/277) 。

Java 中有几样东西被永远淘汰了:

*   单个下划线(`_`)不再是有效的标识符。( [JEP 213](http://openjdk.java.net/jeps/213) )
*   在 Java 8 中，垃圾收集标志的某些组合已经被弃用，现在正在被移除。( [JEP 214](http://openjdk.java.net/jeps/214) )
*   hprof 工具正在被移除，因为其他工具(如 [JVisualVM](http://openjdk.java.net/projects/visualvm) )提供了相同或更好的特性。( [JEP 240](http://openjdk.java.net/jeps/240) )
*   因为有更好的堆可视化工具和分析器可用，所以正在移除工具。( [JEP 241](http://openjdk.java.net/jeps/241)

## 但是等等，还有更多！

是的，还有更多。我知道，我知道，我答应了终极指南，现在我让你挂着？但是这篇文章已经够长了，所以让我们就此打住，下周再来写另一篇吧。接下来，我们来看看在引擎盖下发生了什么变化:

[Inside Java 9 – Part I](https://www.sitepoint.com/inside-java-9-part-i/):

*   新版本模式和 GNU 风格的命令行选项
*   支持 GTK3、SHA-3、Unicode 8.0 和 TIFF 等技术
*   对 JVM 的补充(比如多版本 jar 和统一日志)

[Inside Java 9 – Part II](https://www.sitepoint.com/inside-java-9-part-ii/):

*   相当大的性能改进，特别是由于紧凑的字符串
*   编译器添加(比如提前编译)和重构(新的注释管道)
*   JavaDoc 改进

通过我们在 SitePoint 上的教程[在 Android 开发中桥接 Android 和 Java](https://www.sitepoint.com/premium/screencasts/bridging-android-and-java-in-android-development)，了解更多关于 Java 的知识。

## 分享这篇文章