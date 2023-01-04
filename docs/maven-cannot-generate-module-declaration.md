# 为什么 Maven 不能生成你的模块声明

> 原文：<https://www.sitepoint.com/maven-cannot-generate-module-declaration/>

### 目录 

*   [动机](#themotivation)
*   [关注点分离](#separationofconcerns)
*   [POM 依赖关系](#pomdependencies)
*   [模块声明](#moduledeclaration)
*   [胃插件](#mavenplugins)
*   [类路径和模块路径](#classpathsandmodulepaths)
*   [无关紧要](#notaconcern)
*   [POM 中没有位置](#noplaceinthepom)
*   [读取模块描述符](#readingthemoduledescriptor)
*   [模块声明生成器](#themoduledeclarationgenerator)
*   [结论](#conclusion)
*   [评论](#comments)

为了声明 Java 9 的模块系统的模块，我们需要创建一个文件`module-info.java`——一个所谓的*模块声明*。其中，它声明了对其他模块的依赖。如果项目使用构建工具，例如 Maven，它已经管理了依赖项。保持两个文件同步看起来肯定是多余的，而且容易出错，所以问题出现了:“Maven 不能为我生成模块信息文件吗？”不幸的是，答案是“不”，原因如下。

(为了从本文中获得最大收益，您应该熟悉模块系统的基础知识，尤其是它如何管理依赖关系。如果你想深入了解这方面的内容，请查看本实用指南。)

## 动机

所以大家疑惑，Maven 能生成模块声明吗？原因很明显:如果我向一个 POM 添加一个依赖项，它很可能也是一个需求。或者反过来:如果我向声明中添加一个需求，可能也必须向 POM 中添加一个匹配的依赖项。听起来像是可以自动化的东西。

让我们从尝试从需求回到依赖开始:由于缺乏信息，这是不可能的。模块名称无法转换为 Maven 坐标，缺少 groupId 和 artifactId 等信息。还有:选择哪个版本？模块声明对工件的哪个版本在路径上不感兴趣，只关心它是否可用。

另一方面，从依赖文件到模块名是可能的。然而，这不足以生成模块声明的所有元素。

## 关注点分离

当谈到这个主题时，有三个实体在起作用:Maven 的 POM、模块系统的 module-info 文件和涉及构建类和模块路径的 Maven 插件。这三者一起使得使用模块成为可能，但是它们都有自己的职责。

### POM 依赖性

依赖项的任务是(1)基于坐标拥有对文件的唯一引用，以及(2)指定何时使用它。*何时使用*部分由`scope`控制，基本上是`compile`、`test`和`runtime`的任意组合。

坐标至少由`groupId`、`artifactId`、`version`和从`type`导出的文件扩展名组合而成。可选地，也可以添加一个`classifier`。基于这些信息，可以引用本地存储库中的文件。它看起来像下面这样，其中 groupId 中的每个点都被一个斜杠替换:

```
${localRepo}/${groupId}/${artifactId}/${version}/
    ${artifactId}-${version}[-${classifier}].${ext} 
```

如您所见，您可以引用任何文件:文本文件、图像、可执行文件。在依赖关系的上下文中，这并不重要。此外，依赖项不知道文件的内容。例如在 JAR 的情况下:它是为 Java 8 还是 Java 1.4 构建的，如果您的项目必须与相当旧的 Java 版本兼容，这可能会有很大的不同。

### 模块声明

模块声明文件由五个声明组成:

requires

The module(s) that must be available to compile or run this application.

exports

The package(s) whose types are visible to a few or all modules.

opens

The package(s) whose types are [accessible via reflection](https://www.sitepoint.com/reflection-vs-encapsulation-in-the-java-module-system/) to a few or all modules.

uses

The service interface(s) that this module may discover.

provides

The implementation(s) provided for a certain service interface.

从这些声明中可以看出,`requires`子句与 Maven 项目的依赖项密切相关。如果 JDK/JRE 和 Maven 提供的依赖文件不能满足这些需求，这个项目就不能编译或运行。认为这是一个人必须遵守的质量规则。

### 腹部插塞

每个插件(或者实际上是插件目标)都可以指定依赖关系的解析范围，并访问这些文件。例如， [maven-compiler-plugin](http://maven.apache.org/plugins/maven-compiler-plugin/compile-mojo.html) 的编译目标声明它使用编译时依赖解析。这个插件根据 Maven 提供的依赖文件和插件的配置为 Java 编译器构造正确的参数。

## 类路径和模块路径

在 Java 8 之前，这非常简单:所有的 jar 都需要添加到类路径中。但是有了 Jigsaw，jar 可以出现在模块路径*或者类路径*上。这完全取决于另一个模块是否需要一个 JAR。认识到这一点很重要，我们必须 100%的正确，因为如果一个工件在错误的路径上结束，模块系统将拒绝配置，编译或启动将失败。

让我们假设我们已经为我们的项目写了一个模块声明。我们必须在哪里指定 JAR 是否是必需的模块？换句话说，它属于类路径还是模块路径？一个明显的位置是 POM 中的[依赖关系](http://maven.apache.org/ref/3.3.9/maven-model/maven.html#class_dependency)。然而，有几个原因说明这是行不通的。

### 没关系

首先，不是依赖者的关心。依赖关系是关于一个文件的引用和*何时*使用它(编译时间？运行时间？)不是*如何*。

### 在 POM 中没有位置

然后，在依赖关系的所有元素中，只有一个可以用来控制 JAR 的更细粒度的使用:即`scope`。然而，POM 的 modelVersion 4.0.0 有一组严格的作用域。尽管 POM 中充满了 Maven 的构建指令，但一旦安装或部署，它也是一个元文件，包含其他构建工具和 ide 的依赖信息，因此新的作用域可能会混淆或破坏这些产品。

出于同样的原因，为依赖关系引入新的 XML 元素也会有问题；这将与 XSD 相冲突，其他工具还没有为此做好准备。因此，在 POM 的依赖声明中没有模块化信息的空间，短期内，人们不应该期望 Jigsaw 会有新的 POM 定义。

那么用 maven-compiler-plugin 来配置怎么样？

```
<plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <!-- <version>x.y.z</version> -->
        <configuration>
                <!-- example -->
                <modulePath>
                        <!-- by dependency? -->
                        <dependency>com.foo.bar:library</dependency>
                        <!-- by module? -->
                        <module>library</module>
                </modulePath>
        </configuration>
</plugin> 
```

但是传递依赖关系呢，在这个例子中是库的依赖关系。这些依赖关系也需要在两条路径上划分。你需要配置吗？

或者，我们可能希望解析依赖关系的 *build* 信息，以推断在哪里放置它的依赖关系，但是这是不可行的，因为这种信息并不总是存在。即使这样的 JAR 是用 Maven 构建的，试图将 JAR 中的内容逆向工程为有效的插件配置通常也是不可能的(想想多个执行块的效果)。

### 读取模块描述符

因此，如果 POM 不合适，我们还能从哪里获得信息呢？最好照原样关注 JAR 中的文件，例如一个`module-info.class` …(编译后的模块声明，称为*模块描述符*)。

因此，关键文件似乎是`module-info`文件，因为它确切地指定了需求。一旦像 [ASM](http://asm.ow2.org/) 这样的工具可以读取模块描述符，maven-compiler-plugin 就可以提取该信息，并判断它是否匹配所需的模块。对于*我们的*项目，应该有一个`module-info.java`，这样 Maven 就知道在哪里放置*我们的*依赖项。为此，需要在编译之前分析*，这已经可以通过 [QDox](https://github.com/paul-hammant/qdox) 实现。*

因此，有了 JAR 中的所有`module-info.class`-文件和我们项目的`module-info.java`-文件，就有可能决定每个 JAR 属于哪里；类路径或模块路径。这使得模块声明和描述符成为成功构建的*成分*。但是问题仍然存在，难道这不是一个*结果*？

![Maven can not create module declarations](img/0b10d7ac0313e50d4e866bff115bfe67.png)

## 模块声明生成器

当分析这个项目中使用的所有模块描述符时，我们看到应该可以在模块路径和类路径上划分所有的 jar。但是有可能为我们的项目生成模块声明吗？

对于`src/main/java/module-info.java`中的要求，我们可以走得很远。我们可以说，所有范围为`compile`或`provided`的依赖都是需求，`test`当然不是，但是对于`runtime`(这意味着*在运行时只有*)来说，这取决于需求。但是也有需求，这些需求并没有映射到一个 JAR。相反，它们指的是 java 或 jdk 模块(例如`java.sql`、`java.xml`或`jdk.packager`)。这些必须为生成器配置，否则需要预先分析代码。

一个需求还可以有额外的修饰符。使用一个`requires static`子句，你可以指定一个模块在编译时是强制的，但在运行时是可选的。在 Maven 中，您可以将这种依赖标记为`optional`。使用`transitive`修饰符，你可以指定使用*这个*模块的项目不必在它们自己的模块声明文件中添加可传递模块。所以`transitive`对这个项目没有影响，它只帮助其他使用这个项目作为依赖项的项目。此类信息只能作为配置提供。

即使 POM 定义将被重新设计以支持这种依赖元数据，以便被转换到模块声明文件中的需求，也有更多的声明，它们更难生成。它已经从模块本身开始:它的名字是什么，它是否开放？

还有导出的和打开的包呢？看起来好像可以通过代码分析推断出来。尽管如此，将 java 文件作为源代码来分析还是不可能的。将类文件作为二进制文件进行分析意味着双重编译，首先只使用类路径来获得所有要分析的类，然后使用模块路径和类路径，这就是实际的编译。没有可行的解决方案。

最终*模块声明中的每一行*都是开发者的选择。向 POM 添加配置有意义吗？POM 看起来已经很像预期的模块声明文件了。

我们能得到的最接近的模板如下。让我们使用[速度](http://velocity.apache.org/engine/1.7/user-guide.html)，并调用模板`module-info.java.vm`。

```
open module M.N
{
     #set ( $requiredModules = ... ) ## these must somehow be available in the context

     ## requires {RequiresModifier} ModuleName ;
     #set( $transitiveModules = ["org.acme.M1", "org.acme.M2"] )
     #foreach( $requiredModule in ${requiredModules} )
         #if( $transitiveModules.contains( $requiredModule.name ) ) transitive #end #if( $requiredModule.optional ) static #end ${requiredModule.name} ;
     #end

     requires java.sql;
     requires java.xml;
     requires jdk.packager;

     ## exports PackageName [to ModuleName {, ModuleName}] ;
     exports com.acme.product;
     exports com.acme.service;

     ## opens PackageName [to ModuleName {, ModuleName}] ;

     ## uses TypeName ;

     ## provides TypeName with TypeName {, TypeName} ;
} 
```

将它转换成`module-info.java`可能需要一个单独的模板 maven-plugin，因为这超出了 maven-compiler-plugin 的编译任务和 maven-resource-plugin 的可选过滤任务的资源复制。与普通的旧(新)模块声明文件相比，这种带有一些脚本语法的模板是否更易于阅读和维护，这取决于开发人员。

有些人可能听说过 jdeps，这是一个从 JDK8 开始就可以使用的工具，可以分析依赖关系。它还有一个生成模块声明文件的选项，但不是以我们想要的方式。Jdeps 只使用编译过的类，所以必须在和`compile`阶段之后使用*。这个特性的引入是为了让开发者有一个`module-info.java`-文件来*开始*，但是一旦生成，就要由开发者来调整和维护它。*

## 结论

如果有一个模块声明生成器，它仍然需要大量的配置才能得到正确的结果文件。这不会比直接写文件的工作量少。总之，自己编写和维护模块声明可以保证它总是如您所愿。

当然，一些小的开源项目会弹出来并试图生成模块声明，但它只对项目的子集有效，这意味着我们不能为 Maven 公开它。

我更希望 IDEs 能为您解决这个问题。例如，如果您向 POM 添加一个依赖项，您也可以选择将它作为需求添加到模块声明中。或者它们提供一个向导，显示项目中的所有依赖项和包，让您选择向模块声明文件中添加什么。

然而，故事的这一部分结束了，我在这里告诉你，暂时 Maven 不会为你写模块声明。

## 分享这篇文章