# 让超快的 Ruby 成为现实的项目

> 原文：<https://www.sitepoint.com/projects-that-are-making-blazing-fast-ruby-a-reality/>

![Speed design over white background, vector illustration.](img/240c0111c10813b273c6d18f70dddef7.png)

Ruby 在科学计算或人工智能领域还不算大。然而，这种情况可能很快就会改变，因为有两个主要项目可能会在 2016 年让 Ruby 有所起色。

## OMR

OMR 项目是一个开源(soon)项目，旨在从 IBM J9 虚拟机开发可重用和可消费的组件。这些组件将用于任何期望的语言运行时，包括 Ruby。希望这将降低实现语言的门槛。

以下是该项目计划带来的一些东西:

1.  JIT 编译器
2.  企业级垃圾收集器
3.  方法分析器

目前速度提升不大，在 1 到 3 倍的范围内。然而，IBM 关注的是兼容性，而不是性能，以方便采用。他们声称已经能够运行 Rails。

IBM 向 GitHub 发布了他们的 OMR Ruby 实现的技术预览图。

## 红宝石+块菌+Graal

虽然 OMR 看起来很有前途，但真正的明星可能是甲骨文实验室正在开发的实验性 JRuby 后端。它用 Truffle AST 框架和 Graal JIT VM 实现了 Ruby。所宣称的性能提升已经是惊人的了——在非合成基准测试中高达 30 倍左右。这个项目的结果可能会让我们重新思考动态语言的可能性，它可能会让 Ruby 进入科学计算和人工智能的世界。

除了性能，Truffle+Graal 的另一个目标是实现语言之间的高互操作性。目前包括 R、JavaScript，当然还有 Ruby。这将更容易使用最好的库来完成工作，而不是满足于主要语言中的任何可用库。

至于成熟度，根据 Chris Seaton 的说法，该项目目前[“通过了 93%的 RubySpace 语言规范和 90%的核心库规范”](http://lists.ruby-lang.org/pipermail/jruby/2015-December/000262.html)。

### 松露

Truffle 是一个用于编写自优化 AST(抽象语法树)解释器的 Java 框架。ast 是包含源代码的树表示的数据结构，由解析器生成。通常使用解释器，AST 用于生成运行在虚拟机上的字节码。然而，Truffle 使用 AST 节点来直接控制 Graal 的机器代码发射。

当使用这种新方法实现一种语言时，我们主要关心的是块菌“层”。这就是为什么你可能会看到提到“块菌语言”。

### Graal

Graal 是 OpenJDK JVM 中一个新的 JIT 编译器实现。Graal 是用 Java 写的，所以它向正在运行的程序公开了一个 Java API。这允许语言直接控制编译器，从而从 AST ->机器码，没有字节码生成步骤。

### 方法调用

Ruby+Truffle 如此之快的原因之一是因为它去除了常见操作的方法调用。在 Ruby 中，几乎所有东西都是方法调用，包括计算结果为`1.+(1)`的`1+1`，以及计算结果为`foo.=(1)`的`foo=1`。这些调用还涉及分配对象。

Ruby+Truffle 通过内联操作完全取消了这些方法调用和分配，使得后端速度更快。最终的结果是，Ruby+Truffle 产生的手工机器代码看起来就像是由一个[演示向导手工制作的。这个过程被称为**部分逃逸分析**。](https://en.wikipedia.org/wiki/Demoscene)

### 主动缓存

你可能听说过 Ruby 的[“方法缓存”](http://jamesgolick.com/2013/4/14/mris-method-caches.html)以及它是如何经常失效的。Ruby 方法查找通常涉及两个缓存级别:

1.  全局方法缓存–将函数指针与类和方法名称组合相关联。
2.  内联缓存–在调用点缓存函数指针，以避免命中全局缓存。

Ruby+Truffle 增加了一个新的缓存级别:

1.  参数缓存–包含参数的通用内联缓存。

### 优化的数据结构

当你在 MRI 中创建一个类似于`[1,2,3]`的数组时，对象在 C 中被表示为一个由`VALUE`指针组成的[数组。这意味着每个数组元素不必是相同的类型，但它必须被装箱和取消装箱，这需要时间。](https://github.com/ruby/ruby/blob/cb3b463a50dee9d4e3d174dc9b1d7c8181678cd4/array.c)

Ruby+Truffle 后端足够智能，能够识别何时可以以未装箱的方式存储集合。它通过一个*专业化*的系统来做到这一点。它甚至对完全不分配存储的空集合进行了专门化。

第一次，在 Ruby 中执行密集的数学运算而不依赖于 C 扩展成为可能。然而，C 扩展的用户也有望看到显著的速度提升。

### GPU 后端

Graal repository 中的一些 GPU 后端已经成形。

*   PTX(并行线程执行)–在 CUDA (nvidia)硬件上实现通用计算
*   HSAIL(异构系统架构中间层)——AMD 集成 CPU 和 GPU 的解决方案

如果这些技术成熟，它可以创造一个环境，几乎任何语言都可以用于高级机器学习任务，并且速度非常接近本地语言。

### 尝试一下

JRuby 目前在 GitHub 上有实验性的 Truffle/Graal 后端。下面是一些关于如何用流行的 Ruby 版本管理器加载它的说明:

#### rbenv

```
$ rbenv install jruby-master+graal-dev
$ rbenv shell jruby-master+graal-dev
$ ruby -X+T -e 'puts Truffle.graal?'
true 
```

#### 延髓头端腹内侧核群

```
$ rvm mount -r http://lafo.ssw.uni-linz.ac.at/graalvm/jruby-dist-master+graal-macosx-x86_64-bin.tar.gz -n jruby-dev-graal
$ rvm use jruby-dev-graal
$ ruby -X+T -e 'puts Truffle.graal?'
true 
```

当使用 Graal 后端时，您应该会得到类似这样的结果:

```
$ ruby --version
jruby 9.0.5.0-SNAPSHOT (2.2.3) 2015-12-22 36276d3 OpenJDK 64-Bit Server VM25.40-b25-internal-graal-0.7 on 1.8.0-internal-b128 +jit [darwin-x86_64] 
```

请记住，这些都是开发版本。我第一次尝试 RVM 路线时遇到了一些安装问题，但几天后就正常了。

如果您决定运行一些基准测试，请记住，您仍然需要处理 JVM 的引导时间和预热时间。到目前为止，似乎很难得到项目组声称的那种结果。在启用了 Truffle(`-X+T`)的情况下，对于各种简单的病例，我通常获得的 MRI 性能甚至更低——即使是在多次运行之后。

如果你能从任何代码中获得 30 倍以上的收益，请在评论中告诉我们。注意:为了帮助处理预热问题，开发人员使用了 [benchmark-ips](https://github.com/evanphx/benchmark-ips)

## 结论

这些项目不仅仅影响 Ruby。任何实现的语言都会看到这些方法带来的速度优势。他们还有很长的路要走，但是他们进展得很好，所以我们有可能在今年的某个时候看到他们的普遍使用。

## 资源

*   JRuby+松露
*   [使用 Truffle 进行快速元编程](https://www.youtube.com/watch?v=lRMWwjqbXUo)
*   [快速制作 Ruby】](http://thepracticaldev.com/chris-seaton-making-ruby-fast)

## 分享这篇文章