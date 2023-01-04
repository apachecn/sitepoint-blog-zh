# 反射与封装——在 Java 模块系统中脱颖而出

> 原文：<https://www.sitepoint.com/reflection-vs-encapsulation-in-the-java-module-system/>

### 目录 

*   [设置场景](#settingthescene)
*   [模块速成班](#modulecrashcourse)
*   [倒影](#reflection)
*   [对峙](#thestandoff)
*   [武器的选择](#choiceofweapons)
*   [常规出口](#regularexports)
*   [合格出口](#qualifiedexports)
*   [打开包装](#openpackages)
*   [合格开包](#qualifiedopenpackages)
*   [打开模块](#openmodule)
*   [类路径奸计](#classpathtrickery)
*   [未命名模块](#unnamedmodule)
*   [自动模块](#automaticmodule)
*   [命令行转义符](#commandlineescapehatches)
*   [封装断路开关](#encapsulationkillswitch)
*   [总结](#summary)
*   [评论](#comments)

历史上[反射](https://www.sitepoint.com/java-reflection-api-tutorial)可以用来侵入运行在同一个 JVM 中的任何代码。有了 Java 9，这种情况将会改变。新模块系统的两个主要[目标之一是强封装；给模块一个安全的空间，任何代码都不能侵入。这两种技术显然是不一致的，那么如何解决这个问题呢？经过大量的讨论后，看起来最近](http://openjdk.java.net/projects/jigsaw/goals-reqs/)[提出的开放模块](http://mail.openjdk.java.net/pipermail/jpms-spec-experts/2016-October/000430.html)将会显示出一条出路。

如果你完全了解模块系统和反射的作用，你可以跳过下面的背景故事，直接进入[阶段](#thestandoff)。

## 设置场景

让我来设置一下[模块系统](http://openjdk.java.net/projects/jigsaw/spec/sotms)如何实现强封装以及它如何与反射冲突的场景。

### 模块速成班

Java 平台模块沙龙(JPMS)引入了模块的概念，模块最终只是带有模块描述符的普通罐子。描述符由一个`module-info.java`文件编译而来，该文件定义了一个模块的名称、它对其他模块的依赖关系以及它所提供的包:

```
module some.module {

    requires some.other.module;
    requires yet.another.module;

    exports some.module.package;
    exports some.module.other.package;

} 
```

在封装的情况下，有两点需要注意:

*   只能访问导出包中的公共类型、方法和字段。
*   只有需要导出模块的模块才能访问它们。

这里，“可访问”意味着代码可以针对这些元素进行编译，并且 JVM 将允许在运行时访问它们。因此，如果模块*用户*中的代码依赖于模块*所有者*中的代码，我们需要做的就是让*用户*要求*所有者*并让*所有者*导出包含所需类型的包:

```
module user {
    requires owner;
}

module owner {
    exports owner.api.package;
} 
```

这是常见的情况，除了使依赖关系和 API 明确并为模块系统所知之外，所有工作都像我们习惯的那样。

到目前为止，每个人都玩得很开心！然后，沉思出现了……谈话在句子中间停止，钢琴演奏者停止了他的曲子。

### 反射

在 Java 9 之前，[反射](https://www.sitepoint.com/java-reflection-api-tutorial)被允许闯入任何代码。除了一些讨厌的对`setAccessible`的调用之外，任何类中的每个类型、方法或字段都是可用的，可以被调用，可以被改变——见鬼，甚至 final 字段也不安全！

```
Integer two = 2;

Field value = Integer.class.getDeclaredField("value");
value.setAccessible(true);
value.set(two, 3);

if (1 + 1 != two)
    System.out.println("Math died!"); 
```

这种力量驱动着各种各样的框架——从 Hibernate 这样的 JPA 提供者开始，到 JUnit 和 TestNG 这样的测试库，到 Guice 这样的依赖注入器，最后到 Spring 这样令人着迷的类路径扫描器——它们反映在我们的应用程序或测试代码上，发挥它们的魔力。另一方面，我们的图书馆需要 JDK 的一些东西，但他们不愿意曝光(有人说`sun.misc.Unsafe`)吗？).在这里，反思也是答案。

所以这个人，习惯于得到他想要的，现在走进模块沙龙，酒保不得不告诉他不，这次不行。

## 对峙

在模块系统内部(让我们放弃沙龙吧，我想你明白这个笑话了)，反射只能访问导出包中的代码。模块内部的包是禁止入内的，这已经引起了不小的骚动。但它仍然允许使用反射来访问导出包中的其他所有东西，比如包可见的类或私有字段和方法——这被称为 [*深度反射*](http://mail.openjdk.java.net/pipermail/jpms-spec-experts/2016-October/000431.html) 。九月规则变得更加严格！现在深度反射也被禁止了，反射并不比我们编写的静态类型代码更强大:只有导出包中的公共类型、方法和字段是可访问的。

所有这一切当然引起了很多讨论——有些激烈，有些友好，但都有一种绝对重要的感觉。

有些人(包括我自己)喜欢强封装，认为模块需要一个安全的空间来组织它们的内部，而没有其他代码依赖它的风险。我喜欢举的例子是 JUnit 4，其中重写的一个重要原因是工具依赖于实现细节，反映到私有字段的级别；还有`Unsafe`，它的即将被移除给很多图书馆带来了很大压力。

其他人认为，反射提供的灵活性不仅为依赖它的许多框架提供了很好的可用性，在这些框架中，注释一些实体并将 *hibernate.jar* 放到类路径上就足以让事情正常工作。它也给了库用户自由，他们可以以他们想要的方式使用他们的依赖，这可能并不总是维护者想要的方式。这里，`Unsafe`为另一方提供了一个例子:许多现在对 Java 生态系统至关重要的库和框架之所以可行，完全是因为没有 JDK 团队的批准，一些黑客攻击*是可能的。*

即使我倾向于封装，我也看到了其他论点的有效性。那怎么办呢？除了封装内部结构和放弃反射，开发人员还有什么选择？

![Stand off between reflection and encapsulation in the Java Module System](img/3e5a4d9a277de28f4c6a3da3f7705fd6.png)

## 武器的选择

因此，假设我们处于这样一个位置，我们需要通过反射使模块的内部可用。可能是为了将它暴露给一个库或框架模块，或者可能是因为我们*是另一个模块的*，想要进入第一个模块。在本文的其余部分，我们将探索所有可用的选择，寻找这些问题的答案:

*   采用这种方法我们需要什么特权？
*   谁可以访问内部？
*   它们也能在编译时被访问吗？

在这次探索中，我们将创建两个模块。一个名为*所有者*，包含一个单独的类`Owner`(在包`owner`中)，每个可见性有一个不做任何事情的方法。另一个是*入侵者*，包含一个类`Intruder`，它与`Owner`没有编译时依赖性，但是试图通过反射调用它的方法。它的代码可以归结为:

```
Class<?> owner = Class.forName("owner.Owner");
Method owned = owner.getDeclaredMethod(methodName);
owned.setAccessible(true);
owned.invoke(null); 
```

对`setAccessible`的调用是这里的关键部分，它的成功或失败取决于我们决定如何创建和执行我们的模块。最终我们得到如下输出:

```
public: ✓   protected: ✗   default: ✗   private: ✗ 
```

(这里只能访问公共方法。)

我在这里使用的所有代码都可以在 GitHub 库中找到，包括为你运行它的 Linux 脚本。

### 常规出口

这是公开 API 的常规方法:模块*的所有者*简单地导出包`owner`。为此，我们当然需要能够改变拥有模块的描述符。

```
module owner {
    exports owner;
} 
```

这样我们得到了以下结果:

```
public: ✓   protected: ✗   default: ✗   private: ✗ 
```

到目前为止，呃…不太好。首先，我们只实现了部分目标，因为入侵模块只能访问公共元素。如果我们这样做*，所有依赖于*所有者*的*模块都可以针对它编译代码，并且所有模块都可以反映其内部。实际上，它们不再是内部的了，因为我们正确地导出了它们——包的公共类型现在被嵌入到模块的 API 中。

### 合格出口

如果出口是香草，这就是蔓越莓香草——这是一种默认选择，但有一个有趣的变化。拥有的模块可以将包*导出到特定的模块*，这被称为*限定的导出*:

```
module owner {
    exports owner to intruder;
} 
```

但是结果和常规导出是一样的——入侵模块只能访问公共元素:

```
public: ✓   protected: ✗   default: ✗   private: ✗ 
```

同样，我们只实现了目标的一部分，同样，我们在编译时和运行时都暴露了元素。这种情况得到了改善，因为只有指定的模块(在本例中为*intrusor*)被授予了访问权限，但为此我们接受了在编译时实际知道模块名称的必要性。

在 Guice 这样的框架中，知道入侵的模块可能是有责任的，但是一旦实现隐藏在 API 后面(JDK 团队称之为*抽象反射框架*；想想 JPA 和 Hibernate)这种方法就失败了。不管*是否工作*，在拥有模块的描述符中明确命名入侵模块可能被认为是不确定的。另一方面，拥有的模块可能已经依赖于入侵的模块，因为它需要一些注释或其他东西，在这种情况下，我们不会让事情变得更糟。

### 打开包装

现在变得有趣了。模块系统最近增加的一项功能是模块只能在运行时打开包。

```
module owner {
    opens owner;
} 
```

屈服:

```
public: ✓   protected: ✓   default: ✓   private: ✓ 
```

整洁！我们一石二鸟:

*   入侵模块获得了对整个包的深度访问，甚至允许它使用私有元素。
*   这种公开只存在于运行时，因此不能针对包的内容编译代码。

不过，有一个缺点:*现在，所有的*模块都可以反射打开的包。尽管如此，总的来说还是比出口好得多。

### 合格的开放式包装

与出口和合格出口一样，开放式包装也有一个合格的变体:

```
module owner {
    opens owner to intruder;
} 
```

运行该程序，我们得到与之前相同的结果，但现在只有*入侵者*可以实现它们:

```
public: ✓   protected: ✓   default: ✓   private: ✓ 
```

这给我们带来了与出口和合格出口之间相同的权衡，也不利于 API 和实现之间的分离。但是还有希望！

11 月，Mark Reinhold [提出了一种机制，允许打开包的模块中的代码将访问权转移到第三个模块。回到 JPA 和 Hibernate，这正好解决了这个问题。假设*所有者*的模块描述符如下:](http://mail.openjdk.java.net/pipermail/jpms-spec-experts/2016-November/000457.html)

```
module owner {
    // the JPA module is called java.persistence
    opens owner to java.persistence;
} 
```

在这种情况下，可以采用如下机制(几乎逐字引用提案):

> JPA 实体管理器是通过 [`Persistence::createEntityManagerFactory`方法](http://docs.oracle.com/javaee/7/api/javax/persistence/Persistence.html#createEntityManagerFactory-java.lang.String-)之一创建的，它定位并初始化一个合适的持久性提供者，比如 Hibernate。作为该过程的一部分，他们可以使用客户端模块*所有者*上的`addOpens`方法来打开 Hibernate 模块的`owner`包。这将起作用，因为`owner`模块将该包打开给`java.persistence`模块。

容器也有一种变体，可以将包开放给实现。不过，在当前的 EA 版本(b146)中，这个特性似乎还没有实现，所以我无法尝试。但是看起来绝对有希望！

### 开放模块

如果开放的包是一把手术刀，那么开放的模块就是一把切肉刀。有了它，一个模块在运行时放弃了对谁访问什么的任何控制，并向每个人开放所有的包，就好像每个包都有一个`opens`子句一样。

```
open module owner { } 
```

这导致了与单独打开的包装相同的访问:

```
public: ✓   protected: ✓   default: ✓   private: ✓ 
```

开放模块可以被认为是从类路径上的 JARs 到成熟的、强封装模块的迁移路径上的中间步骤。

### 类别路径欺骗

现在我们正在进入一个不那么模块化的领域。您可能知道`java`和`javac`要求模块在模块路径上，这类似于类路径，但是对于模块来说。但是类路径不会消失，jar 也不会。如果我们可以使用启动命令行*和*来推动工件，我们可以使用两个技巧(所以这不适用于 JDK 模块)。

#### 未命名模块

首先，我们可以将拥有的模块放到类路径上。

模块系统对此有何反应？因为所有东西都需要是一个模块，所以模块系统简单地创建了一个模块，即*未命名模块*，并把它在类路径上找到的所有东西都放在其中。在这个未命名的模块中，一切都很像今天，JAR hell 继续存在。因为未命名的模块是合成的，所以 JPMS 不知道它可能导出什么，所以它只是导出所有内容——在编译时和运行时。

如果类路径上的任何 JAR 意外地包含了模块描述符，这种机制会简单地忽略它。因此，拥有的模块被降级为常规的 JAR，它的代码在一个导出所有内容的模块中结束:

```
public: ✓   protected: ✓   default: ✓   private: ✓ 
```

哒哒！并且不触及拥有它的模块，所以我们可以对我们无法控制的模块这样做。小警告:我们不能要求未命名的模块，所以没有好的方法来编译其他模块中的代码。嗯，也许警告毕竟不是那么小…

#### 自动模块

第二种方法是剥离拥有它的模块的描述符，而*仍然*把它放在模块路径上。对于模块路径上的每个常规 JAR，JPMS 创建一个新模块，根据文件名自动命名它，并导出它的所有内容。由于所有内容都被导出，我们得到了与未命名模块相同的结果:

```
public: ✓   protected: ✓   default: ✓   private: ✓ 
```

很好。自动模块相对于未命名模块的主要优势是模块*可以*需要它，因此应用程序的其余部分仍然可以依赖它并针对它进行编译，而入侵者可以使用反射来访问它的内部。

一个缺点是模块的内部结构在运行时对系统中的每个其他模块都是可用的。不幸的是，在编译时也是如此，除非我们设法针对正确的拥有模块进行编译，然后在去往发射台的路上取出它的描述符。这是不确定的，棘手的，容易出错的。

### 命令行逃生舱口

既然我们无论如何都在摆弄命令行，有一个更干净的方法(也许我应该早点告诉你):两个`javac`和`java`都有一个新的标志`--add-opens`，它打开额外的包。

```
java
    --module-path mods
    --add-modules owner
    --add-opens owner/owner=intruder
    --module intruder 
```

这在不改变所属模块的情况下工作，并且也适用于 JDK 模块。所以，是的，比未命名和自动模块黑客好得多。

### 封装断路开关

如果所有这些看起来太麻烦，而你只是想让它为你的类路径应用程序工作，`--permit-illegal-access`，[友好地被称为封装“kill switch”](http://mail.openjdk.java.net/pipermail/jigsaw-dev/2017-March/011763.html)，对你来说是正确的命令行参数。有了它，未命名模块中的所有代码都可以访问其他类型，而不管强封装带来的任何限制。作为交换，你会得到关于非法访问的警告和生活在洞穴中的窒息感:这个选项只存在于 Java 9 中以简化迁移，所以你只为自己争取了两三年的时间。

下面是如何在模块上启动所有者，在类路径上启动入侵者:

```
java
    --class-path mods/intruder.jar
    --module-path mods/owner.jar
    --add-modules owner
    --permit-illegal-access
    intruder.Intruder 
```

下面是结果——请注意这些警告是如何干扰我精心创建的输出的:

```
WARNING: --permit-illegal-access will be removed in the next major release
public:WARNING: Illegal access by intruder.Intruder (file:mods/intruder.jar) to method owner.Owner.publicMethod() (permitted by --permit-illegal-access)
 ✓   protected:WARNING: Illegal access by intruder.Intruder (file:mods/intruder.jar) to method owner.Owner.protectedMethod() (permitted by --permit-illegal-access)
 ✓   default:WARNING: Illegal access by intruder.Intruder (file:mods/intruder.jar) to method owner.Owner.defaultMethod() (permitted by --permit-illegal-access)
 ✓   private:WARNING: Illegal access by intruder.Intruder (file:mods/intruder.jar) to method owner.Owner.privateMethod() (permitted by --permit-illegal-access)
 ✓ 
```

正如我所说的，这只有在入侵者来自未命名模块时才有效。这背后的基本原理是，适当模块化的代码，即存在于模块中的代码，不应该需要这样的黑客攻击。事实上，如果我们将两个工件都放在模块路径上，我们可以看到标志没有被使用(除了最初的一个之外没有警告),访问失败，因为`owner`的内部被强封装:

```
java
    --module-path mods
    --add-modules owner
    --permit-illegal-access
    --module intruder

WARNING: --permit-illegal-access will be removed in the next major release
public: ✗   protected: ✗   default: ✗   private: ✗ 
```

## 摘要

好吧，还记得我们做过的一切吗？没有吗？拯救行动的执行摘要表！

| 机制 | 接近 | 编译访问 | 反射访问 | 评论 |
| --- | --- | --- | --- | --- |
| 出口 | 描述符 | 所有代码>公共 | 所有代码>公共 | 公开 API |
| 合格出口 | 描述符 | 指定模块>公共 | 指定模块>公共 | 需要知道入侵模块 |
| 打开包装 | 描述符 | 没有人 | 所有代码>私有 |  |
| 合格的打开包装 | 描述符 | 没有人 | 指定模块>私有 | 可以转移到实施模块 |
| 开放模块 | 描述符 | 没有人 | 所有代码>私有 | 一个关键字打开所有包 |
| 未命名模块 | 命令行和工件 | 所有非模块>公共 | 所有代码>私有 |  |
| 自动模块 | 命令行和工件 | 所有代码>公共 | 所有代码>私有 | 需要摆弄藏物 |
| 命令行标志 | 命令行 | 没有人 | 所有代码>私有 |  |
| 切断开关 | 命令行 | 没有人 | 未命名模块>私有 |  |

哇，我们真的经历了相当多的选择！但是现在你知道了，如果你面临的任务是用反射来打破一个模块，你该怎么做。总之，我认为回答一个问题就可以涵盖绝大多数用例:

是自己的模块吗？

*   是的，⇝开放包(也许是合格的),或者，如果有太多的话，整个模块。
*   没有⇝使用命令行标志`--add-opens`或`--permit-illegal-access`。

## 分享这篇文章