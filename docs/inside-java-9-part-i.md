# Java 9 内部——版本模式、多版本 jar 等等

> 原文：<https://www.sitepoint.com/inside-java-9-part-i/>

两个月前，我带您深入 Java 9 了解了新的语言特性和 API。但是有很多东西我不得不忽略，所以这里有一个两部分来解决这个问题。在第一部分中，我们将关注新版本的字符串和命令行语法、多版本 jar、改进的日志记录等等。

### 目录 

*   [JShell](#jshell)
*   [新版本字符串](#newversionstrings)
*   [GNU 风格的命令行选项](#gnustylecommandlineoptions)
*   [扩展和更新](#extensionsandupdates)
*   [Unicode](#unicode)
*   [图形](#graphics)
*   [安全](#security)
*   [新的 Java 虚拟机特性](#newjavavirtualmachinefeatures)
*   [多释放震击器](#multireleasejars)
*   [统一记录](#unifiedlogging)
*   [命令行标志验证](#commandlineflagvalidation)
*   [预留堆栈区](#reservedstackareas)
*   [为关键部分预留帧](#reservingframesforcriticalsections)
*   [例子](#example)
*   [内部！](#internals)
*   [更！](#butwaitthereis_still_more)
*   [评论](#comments)

## JShell

在第一部分，我们跳过了拼图，因为每个人都在谈论它。在这一部分中，我们将对 JShell 做同样的事情，它是 Java 的全新 REPL。(也就是说，你输入 Java 代码，它会立即对其进行评估。)如果你还(不太)了解它，但想了解它，请观看罗伯特·菲尔德去年在比利时 Devoxx 大会上的精彩介绍。( [JEP 222](http://openjdk.java.net/jeps/222)

## 新版本字符串

为了简化这一点，让我们从简单的东西开始:版本字符串。

至少我是这样认为的，直到我试图理解 Java 的版本 [命名模式](https://en.wikipedia.org/wiki/Java_Platform,_Standard_Edition#Nomenclature.2C_standards_and_specifications)。它从 JDKs 1.0 和 1.1 开始——到目前为止很容易，但事情从那里开始走下坡路。显然，版本 1.2 到 1.5 在某个时候被重新命名为 Java 2。(还记得 *J2SE* ？那是在那里的 *2* 。)随着 JDK 1.5 的出现，很明显这并没有真正实现，所以 Sun 开始称它为 Java 5。在 Java 6 前后的某个时候，Java 2 的整个概念被悄悄地埋葬了，从那以后事情变得明朗了一些——我们简单地称之为“Java X”。(您知道吗，Java 7 之前的所有 Java 版本都有很酷的项目名称，如 *Tiger* 和 *Mustang* ？)

JVM 报告的版本字符串没有被改动——它们总是报告`1.x...`。现在，有了 [JEP 223](http://openjdk.java.net/jeps/223) ，版本字符串和命名模式一致了。如果您检查相关的系统属性(参见[演示](https://github.com/CodeFX-org/demo-java-9/blob/master/src/org/codefx/demo/java9/internal/version/VersionSchema.java)，您将得到以下输出:

```
java.version: 9-ea
java.runtime.version: 9-ea+138-jigsaw-nightly-h5561-20161003
java.vm.version: 9-ea+138-jigsaw-nightly-h5561-20161003
java.specification.version: 9
java.vm.specification.version: 9 
```

这并没有提供太多的信息，因为它是在早期的访问构建上运行的。将来，`java.version`将报告类似于`9.1.2`的字符串，它们遵循模式`$MAJOR.$MINOR.$SECURITY`:

*   标志着 Oracle 计划每两到三年发布一次的主要版本。
*   标记较小版本的错误修复和其他定期发布的细节——当新的主要版本发布时，它重置为零。
*   `$SECURITY`真的很有趣——它随着每个“包含关键修复，包括提高安全性所必需的修复”的版本而被取消，并且当`$MINOR`增加时**不会重置**。

为了不需要解析那个字符串，我们得到了 [`Version`](http://download.java.net/java/jdk9/docs/api/java/lang/Runtime.Version.html) ，一个很好的小类为我们做了这件事。

```
Version version = Runtime.version();
System.out.println("Reported by runtime: " + version);
switch (version.major()) {
    case 9:
        System.out.println("Modularity!");
        break;
    case 10:
        System.out.println("Value Types!");
        break;
} 
```

## GNU 风格的命令行选项

当涉及到命令行选项的语法时，Java 工具有很多特性:

*   有些人用单个破折号表示长版本(`-classpath`)，有些人用两个破折号(`--gzip`)
*   有些单词用破折号(`--no-gzip`)分开，有些则不用(再次用`-classpath`)
*   有些有单字母形式(`-d`)，有些有两个字母形式(`-cp`)，说真的那个选项有什么问题？！)
*   有些用等号(`-D<name>=<value>`)赋值，有些需要空格(我甚至不会…)

相比之下，在 Linux 和其他基于 GNU 的系统上，几乎所有工具的选项都使用相同的语法:

*   两个破折号代表长版本
*   单词用破折号隔开
*   缩写使用单个破折号，由单个字母组成

Java 9 大胆地修改了所有命令行选项来匹配这些规则，从而破坏了所有脚本！不，只是开玩笑…😎但是 JEP 293 建立了一个反映和适应这些规则的指导方针，新的选择将会遵循它。旧的选项可能会在某个时候向更清晰的语法迁移，但这不是 Java 9 的一部分。JEP 包含了很多细节和例子——读一读吧。

## 扩展和更新

Java 9 附带了许多扩展或更新现有功能的 jep。以下是它们，按主题排序，有些是摘要，有些更详细。

### 统一码

虽然 Java 本身可以用 UTF-16 编写(是的，您的代码可以显示表情符号)，但属性文件过去仅限于 ISO-8859-1。假设您有这样一个`config.properties`文件:

```
money = € / \u20AC 
```

那么用 Java 8 访问这个文件会产生:

```
money = â▯¬ / € 
```

随着 [JEP 226](http://openjdk.java.net/jeps/226) 的出现，那些时代终于结束了，不再需要 [Unicode 转义](https://www.sitepoint.com/java-unicode-mysterious-compile-error/)。在 Java 9 上运行相同的访问代码显示了我们预期的结果:

```
money = € / € 
```

([完整示例](https://github.com/CodeFX-org/demo-java-9/blob/master/resources/config.properties)甚至还有一个😎但是我们的代码荧光笔不能很好地处理这个问题。)

注意，有几种方法可以访问属性文件，只有通过`PropertyResourceBundle`的方法得到了更新。编码是如何确定的以及如何配置的，在 JavaDoc 的 API 注释中有详细说明。不过，缺省值是合理的，并使 API“只适用于”常见情况:

```
try (InputStream propertyFile = new FileInputStream("config.properties")) {
    PropertyResourceBundle properties = new PropertyResourceBundle(propertyFile);
    properties.getKeys().asIterator().forEachRemaining(key -> {
        String value = properties.getString(key);
        System.out.println(key + " = " + value);
    });
} catch (IOException e) {
    e.printStackTrace();
} 
```

你会在[的演示](https://github.com/CodeFX-org/demo-java-9/blob/master/src/org/codefx/demo/java9/internal/resources/ResourceFileEncoding.java)中找到这段代码以及与`Properties` API 的并列关系。如果您尝试在 Java 8 上运行它进行比较，您会发现 Java 9 为那些仍在使用古老的`Enumeration`的贫穷开发人员做了一个漂亮的小 API 改变。

其他 Unicode 相关新闻，Java 9 支持 **Unicode 8.0** 。耶！( [JEP 227](http://openjdk.java.net/jeps/227) ， [JEP 267](http://openjdk.java.net/jeps/267) )

### 制图法

**TIFF** 图像现在被图像 I/O 框架支持(在`javax.imageio`中)。 [Java 高级成像(JAI)项目](http://www.oracle.com/technetwork/java/iio-141084.html)实现了该格式的读取器和写入器，它们现在已经标准化并移入`javax.imageio.plugins.tiff`。( [JEP 262](http://openjdk.java.net/jeps/262) )

视网膜风格的 HiDPI 屏幕给桌面用户界面带来了独特的挑战。Java 已经在 Mac 上解决了这些问题，现在又在 Linux 和 Windows 上跟进。有了这个“窗口和 GUI 组件应该具有基于平台建议的适当大小，文本应该保持清晰，尽管 HiDPI 设置指示任何默认缩放，图标和图像应该平滑，并且最好具有适合显示器像素密度的细节。”( [JEP 263](http://openjdk.java.net/jeps/263)

在 Linux 上，Java 桌面三巨头(AWT、Swing 和 JavaFX)现在可以使用 **GTK 3** 。目前，JVM 将默认使用 GTK 2，并且只有在新的系统属性`jdk.gtk.version`或者“互操作性需要 GTK3，并且这个需求可以在足够早的时候被检测到”指示的情况下才使用 GTK 3。( [JEP 283](http://openjdk.java.net/jeps/283) )

JavaFX 用的是过时的 [**GStreamer**](https://gstreamer.freedesktop.org/) 版本，更新到 1.4.4。这将提高 JavaFX 重放媒体的稳定性和性能。( [JEP 257](http://openjdk.java.net/jeps/257) )

[**HarfBuzz**](http://harfbuzz.org/) 为新 [OpenType](http://www.microsoft.com/typography/otspec/) 布局引擎，取代 [ICU](http://userguide.icu-project.org/layoutengine) ，停产。( [JEP 258](http://openjdk.java.net/jeps/258) )

### 安全性

实现了 **SHA-3** 哈希算法 SHA3-224、SHA3-256、SHA3-384 和 SHA3-512。它们可以通过 [`MessageDigest` API](http://download.java.net/java/jdk9/docs/api/java/security/MessageDigest.html) 使用。( [JEP 287](http://openjdk.java.net/jeps/287) )

当使用`SecureRandom`(在任何 Java 版本上)时，你要么得到一个基于你的操作系统的本地熵源的本地实现，要么得到一个纯 Java 版本[。后者“使用了一种更老的基于 SHA1 的 RNG 实现，它没有被批准的 **DRBG** 【确定性随机位生成器】机制所使用的算法强大。”由于较老但特别是嵌入式系统依赖于 Java 变体，其安全性通过实施](https://docs.oracle.com/javase/8/docs/api/java/security/SecureRandom.html) [NIST 800-90Ar1](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-90Ar1.pdf) 中描述的 DRBG 机制得到了增强。除此之外，`SecureRandom`的 API 得到了改进，允许将参数传递给 DRGB 和未来的算法( [JEP 273](http://openjdk.java.net/jeps/273) ):

```
Instantiation instantiation = DrbgParameters.instantiation(128, RESEED_ONLY, null);
SecureRandom random = SecureRandom.getInstance("DRBG", instantiation);

byte[] bytes = new byte[20];
random.nextBytes(bytes); 
```

![Published by Ali Bindawood under CC-BY-ND 2.0](img/9ae83b5bc41f7224f7d4c7f3fbca7f7e.png)

[发布](https://www.flickr.com/photos/aliab55/15174758674/ "Source on Flickr")由[阿里·宾达伍德](https://www.flickr.com/photos/aliab55/ "Author on Flickr")在 [CC-BY-ND 2.0](https://creativecommons.org/licenses/by-nd/2.0/ "Link to Licence") 下发布

## 新的 Java 虚拟机特性

为我们做所有工作的机器——我想知道，有一天会出现吗？它以几个新功能的形式得到了一些喜爱，所以也许我们再让它平静几年。如果没有，我，作为一个人，欢迎我们新的 ~~[昆虫](https://www.youtube.com/watch?v=8lcUHQYhPTE)~~ 机器霸主。

### 多释放罐

有时你可能想写代码来区分你运行的 Java 版本——对 Java 8 做*这个*，对 Java 9 做*那个*。到目前为止，这还有点棘手，但是 Java 9 解决了这个难题的一个重要部分。Java 平台的所有相关部分现在都创建并理解多版本 jar，这些 jar 包含不同 Java 版本的相同类型的不同版本。

让我们来看一个例子:

1.  我创建了三个类，Java 8 的`Main`、`VersionDependent`和 Java 9 的`VersionDependent`，前者打印调用后者的结果，后者简单地返回“Java X version”，其中 *X* 要么是 8，要么是 9。

2.  接下来，我把`Main`和`VersionDependent`(针对 Java 8)编译成一个文件夹`out-mr/java8`，把`VersionDependent` (Java 9)编译成`out-mr/java9`。

3.  有趣的是如何包装它们。下面的命令创建一个包含两次`VersionDependent.class`的`mr.jar`(每次来自一个`out-mr/java-x`文件夹),其结构使得`java`选择正确的类:

    ```
    jar9 --create --file out-mr/mr.jar -C out-mr/java-8 . \
        --release 9 -C out-mr/java-9 . 
    ```

4.  实际上，用`java -cp out-mr/mr.jar ...Main`运行它在用 8 运行时会返回“Java 8 版本”,在用 9 运行时会返回“Java 9 版本”。

这是罐子从内部看起来的样子:

```
└ org
    └ codefx ... (moar folders)
        ├ Main.class
        └ VersionDependent.class
└ META-INF
    └ versions
        └ 9
            └ org
                └ codefx ... (moar folders)
                    └ VersionDependent.class 
```

这样，Java 版本 8 和更老的版本将简单地使用`org`中的类，但是新版本可以用右边`META-INF/versions`子文件夹中的内容覆盖其中的一些。干净利落。

### 统一日志记录

调试 JVM，也许是为了解释应用程序崩溃或寻找性能漏洞，已经足够复杂了。不同子系统的不同日志记录选项并没有让这变得更容易。感谢杰普斯 [158](http://openjdk.java.net/jeps/158) 和 [271](http://openjdk.java.net/jeps/271) 这将很快成为过去。它提供了一个新的命令行选项`-Xlog`(现在不应该是`--log`吗？)可用于将日志记录定义到非常详细的级别。以下是一些可用的设置:

*   每条消息可以有一堆标签，这取决于创建它的子系统和它当时在做什么——一些例子是`gc`、`modules`或`os`。可以选择单个标签来应用其他设置。
*   当然，消息有级别(`error`、`warning`、`info`、`debug`、`trace`、`develop`)，可以根据级别进行筛选。
*   输出可以定义为`stdout`、`stderr`或一个文件，可以设置为日志文件循环。
*   然后是*装饰*——每条消息附带的有用信息(pid、正常运行时间、…、技术上的标签和级别也是装饰)。它们可以包含在输出中。

所有这些都可以被固定在一个`-Xlog`选项中。让我们从简单地记录几个标记开始:

```
java9 -cp out-mr/mr.jar -Xlog:os,modules,gc ...Main

[0.002s][info][os] SafePoint Polling address: 0x00007feea4c96000
[0.002s][info][os] Memory Serialize Page address: 0x00007feea4c94000
[0.002s][info][os] HotSpot is running with glibc 2.22, NPTL 2.22
[0.009s][info][gc] Using G1
Java 9 version 
```

Mmh，模块真的什么都没有吗？让我们将该标签设为`debug`(如果没有指定，级别默认为`info`):

```
java9 -cp out-mr/mr.jar -Xlog:os,modules=debug,gc org.codefx.demo.java9.internal.multi_release.Main

[0.002s][info][os] SafePoint Polling address: 0x00007f3054a22000
[0.002s][info][os] Memory Serialize Page address: 0x00007f3054a20000
[0.002s][info][os] HotSpot is running with glibc 2.22, NPTL 2.22
[0.009s][info][gc] Using G1
[0.059s][debug][modules] set_bootloader_unnamed_module(): recording unnamed module for boot loader
[0.063s][debug][modules] define_javabase_module(): Definition of module: java.base, version: 9-ea, location: jrt:/java.base, package #: 159
[... snip ... many, many more module messages ... ] 
```

太多了。但是在这里我们可以看到这个:

```
[0.079s][info][modules,startuptime] Phase2 initialization, 0.0366552 secs 
```

哎，那是`info`！为什么之前没有出现？！因为它有两个标签，令人惊讶的是，如果命令行上定义的标签与消息标签中的*一个*匹配是不够的——它们必须与消息标签中的*全部*匹配。我们可以将匹配器扩展到`modules+startuptime`或者使用通配符:

```
java9 -cp out-mr/mr.jar -Xlog:os,modules*,gc* ...Main

[0.002s][info][os] SafePoint Polling address: 0x00007f9c7f307000
[0.002s][info][os] Memory Serialize Page address: 0x00007f9c7f305000
[0.003s][info][os] HotSpot is running with glibc 2.22, NPTL 2.22
[0.007s][info][gc,heap] Heap region size: 1M
[0.009s][info][gc     ] Using G1
[0.009s][info][gc,heap,coops] Heap address: 0x00000006c6200000, size: 3998 MB, Compressed Oops mode: Zero based, Oop shift amount: 3
[0.077s][info][modules,startuptime] Phase2 initialization, 0.0367418 secs
Java 9 version
[0.090s][info][gc,heap,exit       ] Heap
[0.090s][info][gc,heap,exit       ]  garbage-first heap   total 256000K, used 2048K [0x00000006c6200000, 0x00000006c63007d0, 0x00000007c0000000)
[0.090s][info][gc,heap,exit       ]   region size 1024K, 3 young (3072K), 0 survivors (0K)
[0.090s][info][gc,heap,exit       ]  Metaspace       used 4225K, capacity 4532K, committed 4864K, reserved 1056768K
[0.090s][info][gc,heap,exit       ]   class space    used 414K, capacity 428K, committed 512K, reserved 1048576K 
```

看，甚至垃圾收集器也有话要说——在这种情况下，是关于退出和堆。

这只是表面现象，还有更多的选项可以调整。JEP 在解释这一切方面做得很好，包括例子。

我提到了这样一个事实，即这种改进并不能解决所有的问题。原因是，JEPs 专注于提供基础设施和重新路由一些(许多？肯定是所有的 GC 消息)，但不一定是所有的。虽然我找不到任何其他不使用新机制的日志记录，但它很可能已经存在了。

### 命令行标志验证

这里有一个有趣的事实:Java 8 VM 直到实际需要时才验证命令行标志的值。不幸的是，这在应用程序的生命周期中可能已经很晚了，我可以看到随之而来的崩溃可能会带来一些挫折。让我们举这个例子:

```
java -cp out-mr/mr.jar -XX:BlockLayoutMinDiamondPercentage=120 ...Main 
```

虽然我不知道`BlockLayoutMinDiamondPercentage`是做什么的(也懒得去查)，但看起来`120`并不是一个有效的百分比。Java 8 并没有因此而受到影响，并愉快地执行了我们的 JAR，成功地运行了整个代码——显然，这个值在这个程序运行中是不需要的。或者也许`120`终究是一个有效值？但是 Java 9 不这么认为:

```
java9 -cp out-mr/mr.jar -XX:BlockLayoutMinDiamondPercentage=120 ...Main

intx BlockLayoutMinDiamondPercentage=120 is outside the allowed range [ 0 ... 100 ]
Improperly specified VM option 'BlockLayoutMinDiamondPercentage=120'
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit. 
```

更好…因为多亏了 [JEP 245](http://openjdk.java.net/jeps/245) Java 9 有能力在启动时验证所有的输入标志。从上面可以看出，它还打印了解释性的错误消息。

### 保留堆栈区域

如果一个线程用完了堆栈空间，它会抛出一个`StackOverflowError`。在用户代码中，这通常是应用程序的死刑判决，所以通常没有理由去担心。但是在库中，特别是在 Java 的核心中，即使在失败的情况下，保证某些不变量也是有意义的。

以 [`ReentrantLock`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/locks/ReentrantLock.html) 为例，其中一个`StackOverflowError`在错误的时刻会破坏锁的状态，使其永远无法再次解锁。如果应用程序决定捕捉错误并继续运行，那么锁会死锁所有相关的线程。不太好。

#### 为关键部分预留帧

[JEP 270](http://openjdk.java.net/jeps/270) 增加新注解`@ReservedStackAccess`。有了它，方法可以将自己标识为关键部分，如果系统不想以损坏的状态结束，就需要完成这些关键部分。JVM 关于执行堆栈的行为也相应地改变了。它保留了一些额外的堆栈空间，并且每当一个`StackOverflowError`发生时，它检查一个`@ReservedStackAccess`注释的方法是否在堆栈中。如果是这样的话，这个空间就可供它使用，这样它就可以做一些额外的工作。一旦完成，异常还是会被抛出。

这意味着使用注释并不神奇——异常会被抛出，线程很可能会像没有注释一样死去。只有当一段代码需要完成*并且*能够保证在为此目的保留的空间中完成时，使用它才有意义。

堆栈中预留了多少空间？目前，它默认为单个内存页，并且“实验表明，这足以覆盖到目前为止已经注释的`java.util.concurrent`锁的关键部分。”

#### 例子

编写一个例子并不简单，因为我发现很难以一种可控的方式持续溢出堆栈，同时仍然利用保留的空间做一些有用的事情。在演示项目中，你会发现[是我在那个](https://github.com/CodeFX-org/demo-java-9/blob/master/src/org/codefx/demo/java9/internal/stack/ReservingStackAreas.java)上的最佳尝试，但我不想在这篇文章中向你介绍它。相反，我将展示一些代码，它们总是以不受控制的方式溢出堆栈，并且没有对保留空间做任何有用的事情。

```
int depth;

@ReservedStackAccess
private void determineDepthWithReservedStack() {
    determineDepth();
}

private void determineDepth() {
    depth = 0;
    try {
        recurseToDetermineMaxDepth();
    } catch (StackOverflowError err) { }
    System.out.printf("Depth: %d%n", depth);
}

private void recurseToDetermineMaxDepth() {
    depth++;
    recurseToDetermineMaxDepth();
} 
```

从底部到顶部，您会看到一个方法无限递归并递增`depth`字段，另一个方法包装它，捕捉异常并打印结果深度，最后第三个方法请求访问保留的堆栈区域。我没有很好地利用保留的堆栈区域，因为我只是用更多的递归调用来耗尽它。这意味着调用`determineDepth`或`determineDepthWithReservedStack`将最终导致堆栈溢出，触发打印调用次数。但是输出应该不同，事实上:

```
DEPTH USING REGULAR STACK
Depth: 21392

DEPTH USING RESERVED STACK
Java HotSpot(TM) 64-Bit Server VM warning: Potentially dangerous stack overflow in ReservedStackAccess annotated method org.codefx.demo.java9.internal.stack.ReservingStackAreas.determineDepthWithReservedStack()V [1]
Depth: 59544 
```

尽管结果非常不稳定。我假设有很多其他机制在起作用，这个演示非常粗糙。

#### 内部！

如果你现在流口水，请注意这是内部 API。为了说服编译器让你访问注释，你必须用`--add-exports java.base/jdk.internal.vm.annotation=<module>`进入基本模块，其中`<module>`或者是你的模块名(如果你已经这么做了)或者是`ALL-UNNAMED`。但是 JVM 仍然会忽略注释，除非您用`-XX:-RestrictReservedStack`关闭对特权代码的限制。

看看演示项目中的`compile.sh`和`run.sh`是如何结合在一起的。

## 但是等等，还有*还有*更！

是的，还有更多，这次你不用等两个月了，我保证。第二部分应该在下周的某个时间完成，将集中在性能提升和编译器改进上。我确信时事通讯会给你一个先睹为快的机会，所以如果你好奇的话[订阅](https://go.sitepoint.com/h/y/43CDB766C5398A3B)(在世界协调时间周五晚上之前)或者在 Medium 上关注我们，或者[我](https://medium.com/@nipa)或者[站点点](https://medium.com/sitepoint)(或者两者都有)，它们将在下周出现。

如果您喜欢这篇文章，请帮助我们传播消息，并告诉您的朋友或同事！

## 分享这篇文章