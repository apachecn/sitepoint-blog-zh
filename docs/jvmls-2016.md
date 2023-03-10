# JVMLS 2016:Java 虚拟机语言峰会综述

> 原文：<https://www.sitepoint.com/jvmls-2016/>

上周是 [Java 虚拟机语言峰会](http://openjdk.java.net/projects/mlvm/jvmlangsummit/)，语言设计师和 JVM 工程师每年都会聚在一起展示最新的发展并讨论虚拟机的未来。对于我们 Java 开发人员来说，这可能看起来少了一步，毕竟 [JVM](https://en.wikipedia.org/wiki/Java_virtual_machine) 只是执行我们代码的机器。即将到来的 [JavaOne 大会](https://www.oracle.com/javaone/index.html)彻底盖过了这个活动本身。

但是毫无疑问，JVM 早就走出了 Java 的阴影，过着自己的生活。想想所有在 JVM 上运行的创新语言，以及引发 Java 改进的语言(咳，lambda 表达式，咳)。不要忽视 JVM 的局限性和能力如何影响语言的发展。如果忽略 JVM，不仅无法更深入地理解 Java，而且无法在不考虑 JVM 的情况下思考 Java 的未来。

因此，要了解 Java 的发展方向，有必要了解 JVM 的发展方向。JVMLS 让我们做到了这一点！有很多有趣的话题，但我想集中讨论三个。

## 拼图项目

项目拼图是 [Java 9](http://openjdk.java.net/projects/jdk9/) 的旗舰特性，并将为语言带来[的模块化。它将允许开发人员创建模块，并承诺可靠的配置和强大的封装，以及改进的可伸缩性、安全性和性能。](http://openjdk.java.net/projects/jigsaw/spec/sotms/)

在 Java 9 中，我们将能够创建模块，其中模块只是一个包含新构造的 JAR 一个*模块描述符*。它描述一个模块的方式是给它一个名字，列出它需要的其他模块，以及它导出的包。在运行时，模块(大多数情况下)只能看到它们需要的模块(这被称为*可读性*)，并且只有当它们读取那个模块*和*时才能访问其他模块中的类型，那个模块导出包含该类型的包(被称为*可访问性*)。

这意味着编译器和 JVM 最终抛弃了这个大泥球，它们曾经把我们所有的代码和依赖项都扔进了这个泥球。相反，我们将得到一个真实的图表，它密切反映了我们对软件结构的理解。编译器和 JVM 都会理解这个图！他们会注意到什么时候丢失了什么，什么时候同一个模块有两个版本，什么时候两个模块导出了相同的包，等等。并且它们将在编译或启动时失败，而不是在运行时抛出错误，或者更糟的是，微妙地行为不端。

要获得更深入的见解，请查看 Alex Buckley 的演讲[“Poject Jigsaw-Under The Hood”](https://www.youtube.com/watch?v=QnMDsI2GbOc)或查看我的博客上的 [Jigsaw 资源，也许可以从](http://blog.codefx.org/tag/project-jigsaw/)[这个演练](http://blog.codefx.org/java/dev/jigsaw-hands-on-guide/)开始，亲自动手。其他与 Jigsaw 相关的演示有 Claes Redestad 的[“JLink-Time Optimization”](https://www.youtube.com/watch?v=ae13Wz3WIEc)和 Michael Haupt 的[“Nashorn 模块化”](https://www.youtube.com/watch?v=Zk6a6jNZAt0)对那些与 [Nashorn](http://winterbe.com/posts/2014/04/05/java8-nashorn-tutorial/) 一起工作的人来说尤其有趣

## 瓦尔哈拉工程

[项目 Valhalla](http://openjdk.java.net/projects/valhalla/) 旨在引入值类型，可以概括为自定义原语。

像基元一样，值类型既不会导致内存开销，也不会导致间接内存开销。带有两个`int`字段`x`和`y`的自定义`point`无论在哪里使用，都会被内联。它只需要这两个整数所需的内存，一个点的数组本质上是一个 x/y 对的数组。像图元一样，这些点没有同一性——虽然可以有两个不同的值为 5 的`Integer`对象，但不能有两个不同的 ints 5。

像类一样，值类型可以有方法和字段，并实现接口。因此有一句口号:[“代码像一个类，工作起来像一个整数。”](http://cr.openjdk.java.net/~jrose/values/values-0.html)这将允许我们不再权衡我们喜欢的抽象和我们需要的性能(我们想象的)。

但是这个项目很快就被搁置了。如你所知，泛型不适用于原语——不可能有`ArrayList<int>`。这对于八个原语来说已经很痛苦了(参见流或库 [Trove](https://bitbucket.org/trove4j/trove) 的[原语专门化),但是当开发人员可以定义更多原语时，这就变得难以忍受了。如果值类型必须被装箱才能与泛型交互(就像今天的基元一样)，它们的使用将会相当有限，并且它们将是不可行的。](http://hg.openjdk.java.net/jdk8/jdk8/jdk/file/687fd7c7986d/src/share/classes/java/util/stream/IntPipeline.java)

所以我们希望能够[使用具有值类型的泛型](http://cr.openjdk.java.net/~briangoetz/valhalla/specialization.html)——而原语可以顺其自然。最后，我们不仅希望实例化一个`ArrayList<int>`或`ArrayList<point>`，我们还希望它分别得到一个`int[]`或`point[]`的支持。这就是所谓的专业化，并打开了一个全新的蠕虫罐。

为了更好地了解这些蠕虫，请观看 Brian Goetz 的演讲[“参数多态性的冒险”](https://www.youtube.com/watch?v=Tc9vs_HFHVo)。如果你想更深入，看一下[去年的演讲](https://www.youtube.com/watch?v=uNgAFSUXuwc)——最好先看——接着看毛里齐奥·西马达莫尔的[“瓦尔哈拉倒影——一面镜子统治一切”](https://www.youtube.com/watch?v=YpZZjxvyIVU)，甚至可能是 Remi Forax’[“瓦尔哈拉反向移植”](https://www.youtube.com/watch?v=hrZJuVMXY8s)

如果我们幸运的话，部分甚至所有这些最终都会出现在 Java 10 中。

![machine](img/ec4be8d6b9eeccda9a02a59dc51166aa.png)

## 虚拟机的未来

然后是约翰·罗斯，灰色的额角坐在幕后操纵着一切。在谈到所有热门话题时，他谈到了诱惑虚拟机走向未来的诱惑:

*   对象、值、数组、方法等的统一模型
*   在性能方面继续努力，例如通过增加密度和共享来提高内存效率，以及持续优化热路径
*   纤程超越线程的粒度并发作为核心抽象
*   变得越来越有用，越来越兼容，越来越具有互操作性——对于运行在其上的语言和本地语言都是如此，从而弥合了它们之间的裂痕
*   堆栈具体化，其中堆栈将类和方法作为真正的对象，而不仅仅是字符串，并且每个框架的完整上下文(局部变量、监视器等)是可见的，并且堆栈可以由正在运行的程序编辑

老实说，约翰·罗斯设想的很多东西我都理解不了。但不管怎样，听他说话很棒，即使只是为了吸收他的热情。你可以不相信我的话，看[《VM 期货》](https://www.youtube.com/watch?v=gii6ySfsVfs)自己看吧！

罗斯的观点中很大一部分是巴拿马计划，米克尔·维德斯特和鸢·阿吉拉在 T2 的《走向本土化》中谈到了巴拿马计划，伊恩·格雷夫斯在巴拿马计划中加入了 T4 的《Java 矢量 API》(T5)。同样旨在弥合裂痕的是 Christian Wimmer 关于[“一个编译器”](https://www.youtube.com/watch?v=wwEVakQzVAw)的演讲。

## 摘要

JVM 是一只强大的野兽，许多聪明的人正在努力使它变得更强大，同时保持复杂性在控制之下。你认为他们做得好吗？

如果所有这些离您的日常工作太远，请在一个月后我们报道 JavaOne 大会 2016 时再回来！

## 分享这篇文章