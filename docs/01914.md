# 上下文和依赖注入(CDI)简介

> 原文：<https://www.sitepoint.com/introduction-contexts-dependency-injection-cdi/>

### 目录 

*   [使用 CDI 的简单依赖注入](#easydependencyinjectionwithcdi)
*   [创建注射点](#creatinganinjectionpoint)
*   [自举焊接](#bootstrappingweld)
*   [用@Default 和@Alternative 限定实现](#qualifyingimplementationswithdefaultandalternative)
*   [介绍@命名注释](#introducingthenamedannotation)
*   [使用 Field 和 Setter 注入](#usingfieldandsetterinjection)
*   [使用@产生注释](#usingtheproducesannotation)
*   [使用自定义限定符](#workingwithcustomqualifiers)
*   [总结](#summary)
*   [评论](#comments)

[CDI](http://docs.oracle.com/javaee/6/tutorial/doc/giwhb.html) (上下文和依赖注入)是 Java EE 6 及更高版本捆绑的[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection) (DI)规范。它实现了一个基于注释的 DI 框架，使得完全自动化依赖管理更加容易，并让开发人员专注于编码，而不是处理所有不同方面的 DI。

DI 是在[面向对象设计](https://en.wikipedia.org/wiki/Object-oriented_design)中使用的[控制反转](https://en.wikipedia.org/wiki/Inversion_of_control)最普遍的形式之一。但是这并不意味着手动实现模式本身就很容易，因为它总是需要显式地实现某种创造性的模式，例如作为底层注入器的[工厂](https://en.wikipedia.org/wiki/Factory_method_pattern)或[构建者](https://en.wikipedia.org/wiki/Builder_pattern)。

不可否认，几个流行的框架，包括 Google Guice T1，甚至 T2 Spring T3，都会让对象图的构建变得不那么痛苦。不过，CDI 是标准化的，让您可以选择几种实现。如果你只是想做简单的 DI，与 Spring 这样成熟的框架的负担作斗争并不总是最好的方法。另一方面，CDI 的目标是让阿迪变得简单，而不需要借助任何外部框架。

首先，它需要一个有效的实现来启动和运行。现在有几个可用的，包括 [OpenWebBeans](http://openwebbeans.apache.org) 、 [CauchoCanDI](http://www.caucho.com/resin-4.0/admin/config-candi.xtp) 和 [Weld](http://weld.cdi-spec.org) 。后者是参考实现，可以在不同的上下文和环境中使用，包括 Java SE。

最后但同样重要的是，对于注入依赖项没有限制，这个特性允许快速注入 Java EE 资源，比如 [JPA](https://en.wikipedia.org/wiki/Java_Persistence_API) 实体管理器。其他框架(Guice 就是一个很好的例子)也提供了对 [JPA / entity manager 注入](https://github.com/google/guice/wiki/JPA)的支持，但是这个过程非常繁琐，并且没有完全标准化。这为在数据库驱动的应用程序中使用该标准打开了大门，我计划在后续文章中深入讨论这个主题。

也就是说，在本教程中，我将为您提供在 Java SE 中使用 CDI / Weld 的实用指南。

## 使用 CDI 轻松实现依赖注入

当涉及到设计基于几种多态实现的解耦组件时，DI 本身就成为一种必要。典型的用例是通过接口或抽象类定义的[隔离契约](https://en.wikipedia.org/wiki/Interface_segregation_principle)，以及多个实现，其中一个或多个实现必须在运行时注入到客户端对象中。

### 创建注入点

考虑以下示例，它是一个简单应用程序的一部分，该应用程序以几种基本方式处理给定的字符串:

```
public interface TextProcessor {

    String processText(String text);

} 
```

处理字符串的 API 非常基础，不值得进一步分析。下面是相应的实现:

```
public class LowerCaseTextProcessor implements TextProcessor {

    public String processText(String text) {
        return text.toLowerCase();
    }

}

public class UppercaseTextProcessor implements TextProcessor {

    public String processText(String text) {
        return text.toUpperCase();
    }

}

public class ReverseTextProcessor implements TextProcessor {

    public String processText(String text) {
        return new StringBuilder(text).reverse().toString();
    }

} 
```

至此，我们已经创建了三个简单的实现，其功能归结为小写、大写和反转给定的字符串。

现在，假设我们想要在运行时将这些类的一个实例注入到一个客户机对象中，以便处理在控制台中输入的一个字符串。在这种情况下，首先我们需要定义一个类似于下面的类:

```
public class TextApplication {

    private TextProcessor textProcessor;
    private BufferedReader userInputReader;

    @Inject
    public TextApplication(TextProcessor textProcessor) {
        this.textProcessor = textProcessor;
        this.userInputReader =
                new BufferedReader(new InputStreamReader(System.in));
    }

    public void run() throws IOException {
        System.out.println("Enter the text to process : ");
        String text = userInputReader.readLine();
        System.out.println(textProcessor.processText(text));
    }

} 
```

除了我用来创建一个`BufferedReader`对象的邪恶的`new`操作符(现在请耐心等待，因为我将在以后重构它)，这里首先值得指出的是`@Inject`注释的使用，它告诉 CDI 必须将`TextProcessor`接口的一个实例注入到类的构造函数中。这是简单的构造函数注入！

### 自举焊接

嗯，没那么容易。首先我们需要下载焊接工件——这是给 Maven 的:

```
<dependency>
    <groupId>org.jboss.weld.se</groupId>
    <artifactId>weld-se</artifactId>
    <version>2.4.0.Final</version>
</dependency> 
```

有了焊接工件，我们必须在`src/main/java/resources/META-INF/`目录中创建一个`beans.xml`文件，因为 CDI 需要扫描这个文件，即使它不包含额外的注射选项。该文件的一个典型版本大概是这样的:

```
<?xml version="1.0" encoding="UTF-8"?>
<beans
        xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/beans_1_1.xsd"
        bean-discovery-mode="all">
</beans> 
```

接下来，我们必须以编程方式[引导焊接](http://docs.jboss.org/weld/reference/latest-master/en-US/html/environments.html#weld-se)，从焊接容器中获取`TextApplication`类的一个实例，最后随意开始使用它，如下所示:

```
public class Main {

    public static void main(String[] args) throws IOException {
        Weld weld = new Weld();
        WeldContainer container = weld.initialize();
        TextApplication textApplication =
                container.instance().select(TextApplication.class).get();
        textApplication.run();
        weld.shutdown();
    }

} 
```

一切都应该按预期运行。还是不应该？

### 使用`@Default`和`@Alternative`限定实施

当您运行应用程序时，CDI 将抛出一个难看的`DeploymentException`，并显示消息“带有限定符@Default 的 type TextProcessor 的未满足的依赖项”。简而言之，CDI 只是告诉你它不知道将什么实现注入到`TextApplication`类的构造函数中。

我们如何告诉 CDI 在运行时选择什么实现？第一种也是最基本的方法是使用`@Default`和`@Alternative`注释，并指示 CDI 注入一个特定的`TextProcessor`实现。

默认情况下，CDI 将`@Default`注释分配给所有潜在的可注入对象。因此，如果我们想要注入`UppercaseTextProcessor`实现，实现者应该被注释如下:

```
@Default
public class UppercaseTextProcessor implements TextProcessor { ... }

@Alternative
public class LowercaseTextProcessor implements TextProcessor { ... }

@Alternative
public class ReverseTextProcessor implements TextProcessor { ... } 
```

更改类后，再次运行应用程序。您应该被提示在控制台中输入一个字符串，正如所料，输出将是一个大写的漂亮版本！看看使用 CDI / Weld 有多简单，它们有多强大，即使是在做像解析字符串这样琐碎的事情时？

然而，如果你足够挑剔，你会想，如果只有标有`@Default`的接口实现会被注入，那么用`@Alternative`注释几个接口实现又有什么意义呢？备选方案是一个很棒的特性，它允许您在启动时选择实现，而不是像上面那样在编译时选择。要做到这一点，用`@Alternative`注释来注释所有的实现者，并使用强制的`beans.xml`文件来指定哪个实现应该被注入给定的客户机对象:

```
<beans>
    <alternatives>
<!-- in a real system the class names need to be fully qualified -->
        <class>UppercaseTextProcessor</class>
<!--    <class>LowercaseTextProcessor</class> -->
<!--    <class>ReverseTextProcessor</class> -->
    </alternatives>
</beans> 
```

如上所示，所有被注释掉的实现都不会被 CDI 解析，因此`UppercaseTextProcessor`实现将是被注入的实现。显然，这里有足够的实验空间。如果你有兴趣看一些更方便的选项，请随意查看[官方文档](http://docs.oracle.com/javaee/6/tutorial/doc/gjsdf.html)。

但是还有更多的内容要介绍。即使`@Default`和`@Alternative`注释在指导 CDI 在运行时向客户端对象注入什么实现方面做得相当不错，它们的功能也相当有限，因为大多数 ide 不会给你任何关于你要注入什么和在哪里注入的线索。

为了解决这个问题，CDI 提供了一些更直观的机制来实现相同的结果，包括`@Named`注释和自定义限定符。

## 介绍@Named 注释

事实上，使用`@Named`注释与使用`@Default`和`@Alternative`没有什么不同。它的主要好处在于能够将更具语义和意义的名称绑定到特定的实现。此外，许多 ide 会提示您想要注入什么依赖项。

考虑前面实现的重构版本:

```
@Named("Uppercase")
public class UppercaseTextProcessor implements TextProcessor { ... }

@Named("Lowercase")
public class LowercaseTextProcessor implements TextProcessor { ... }

@Named("Reverse")
public class ReverseTextProcessor implements TextProcessor { ... } 
```

现在，如果我们想要注入一个`UppercaseTextProcessor`类的实例，注入点应该被重构如下:

```
@Inject
public TextApplication(
        @Named("Uppercase") TextProcessor textProcessor) { ... } 
```

这将像一个魔咒一样工作，您将得到您想要的类型，而不会对它产生编译时依赖。更好的是，尝试注入不同的实现，并在控制台中输入几个值，您将亲自看到为什么 CDI 在以简单明了的方式管理依赖项方面是一个强大的工具。

此外，CDI 不会限制您只进行简单的构造函数注入，因为 field 和 setter 注入也得到很好的支持。

![Published by Andreas Ruda under CC-BY 2.0](img/9b6b430984e91eff216c103d18f10ef1.png)

[发表](https://www.flickr.com/photos/andresrueda/16538668397 "Source on Flickr")由[安德里亚斯鲁达](https://www.flickr.com/photos/andresrueda/ "Author on Flickr")在 [CC-BY 2.0](https://creativecommons.org/licenses/by/2.0/ "Link to Licence")

## 使用字段和设置器注入

即使经验丰富的开发人员普遍认为构造函数注入被认为是进行 DI 的最有效的方法，因为它确保对象总是在有效的状态下初始化，CDI 也将让您以一种无痛的方式进行字段和 setter 注入。

下面是当`TextProcessor`接口的实现者使用`@Default`和`@Alternative`注释时，前者应该如何做:

```
@Inject
private TextProcessor textProcessor; 
```

这里是相同的注入方法，但是这次使用了`@Named`注释:

```
@Inject
@Named("Uppercase")
private TextProcessor textProcessor; 
```

关于注入语法的相同规则适用于后者:

```
// setter injection with @Default / @Alternative
@Inject
public void setTextProcessor(TextProcessor textProcessor) {
    this.textProcessor = textProcessor;
}

// setter injection with @Named
@Inject
public void setTextProcessor(
        @Named("Uppercase") TextProcessor textProcessor) {
    this.textProcessor = textProcessor;
} 
```

在这一点上，可以清楚地看到，CDI 将允许你注入任何类型的对象…是的，注入任何其他对象，无论是通过使用字段、setter 还是构造函数注入。

但是这里有一个小问题:如果一个依赖项需要某种类型的初始配置，或者有自己的依赖项网络，会发生什么？换句话说，如何处理一个类声明依赖于整个对象图的情况？

这正是`TextApplication`类的情况。当然，它使用了一个`BufferedReader`对象，但是这个对象是在构造函数中特别创建的。这正是 CDI 试图解决的代码味道，也是它到底存在的原因！

## 使用@Produces 注释

为此，该标准提供了`@Produces`注释，它允许在工厂类的围墙后面封装依赖关系的创建。

考虑一下`TextApplication`类的重构构造函数:

```
@Inject
public TextApplication(
        @UppercaseTextProcessor TextProcessor textProcessor,
        BufferedReader userInputReader) {
    this.textProcessor = textProcessor;
    this.userInputReader = userInputReader;
} 
```

毫无疑问，它看起来好多了。邪恶的`new`操作符已经从构造函数中移除，因此使得这个类更具声明性并且更易于测试。它不再隐藏对一个`BufferedReader`对象的直接依赖。从逻辑上讲，这个合作者必须以某种方式被创造出来。

这里正是`@Produces`注释发挥作用的地方:

```
public class BufferedReaderProducer {

    @Produces
    BufferedReader getBufferedReader() {
        return new BufferedReader(new InputStreamReader(System.in));
    }

} 
```

简而言之，`BufferedReaderProducer`类只是一个创建`BufferedReader`对象的普通工厂。考虑到`getBufferedReader()`方法已经用`@Produces`注释进行了注释，在 CDI 术语中，该方法被称为*生产者*。

在这种情况下，`BufferedReader`类不是任何特定接口的实现者，CDI 将首先调用相关的 producer 方法来获取有问题的对象，然后将它注入到 client 类中。

显而易见，*生产者*方法是一个非常强大的 CDI 特性，因为它们是[工厂模式](https://en.wikipedia.org/wiki/Factory_method_pattern)的简洁实现，包装在一个注释的边界内。但是 CDI 为定义注入点提供了更有用和更高级的特性，比如自定义限定符。

## 使用自定义限定符

当您想要在应用程序中创建更多语义、选择性和有意义的注入点时，定制限定符是必须的。虽然名字有点吓人，但自定义限定符只是一个与特定实现相关联的简单注释。乍一看，它就像一个总是与同一个字符串限定符一起使用的`@Named`注释。

例如，对于文本处理器类，限定符可以定义如下:

```
@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD,
        ElementType.TYPE, ElementType.PARAMETER})
public @interface LowercaseTextProcessor { }

@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD,
        ElementType.TYPE, ElementType.PARAMETER})
public @interface UppercaseTextProcessor { }

@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD,
        ElementType.TYPE, ElementType.PARAMETER})
public @interface ReverseTextProcessor { } 
```

`@Qualifier`注释告诉 CDI 它们将被用于注入。但是首先需要将它们应用到实现中，以便 CDI 知道它们与哪些类型相关联:

```
@LowercaseTextProcessor
public class LowercaseTextProcessor implements TextProcessor { ... }

@UppercaseTextProcessor
public class UppercaseTextProcessor implements TextProcessor { ... }

@ReverseTextProcessor
public class ReverseTextProcessor implements TextProcessor { ... } 
```

现在 CDI 知道了，例如，`@UppercaseTextProcessor`(注释)注入`UppercaseTextProcessor`(实现)，相应的注入点可以是这样的:

```
@Inject
public TextApplication(
    @UppercaseTextProcessor TextProcessor textProcessor,
    BufferedReader userInputReader) {
        ...
} 
```

很简单，对吧？使用定制限定符相对于更简单的注释(如`@Named`)的最大好处可以归结为以下几点:

1.  *让类型安全性更强*。例如，将自定义限定符绑定到给定层次结构中基类的子类，可以确保只有子类型会被注入。`@Default`、`@Alternative`和`@Named`标注目前不支持此功能。此外，两者都不能绑定到[保留策略](https://docs.oracle.com/javase/7/docs/api/java/lang/annotation/RetentionPolicy.html?is-external=true)或[合法目标](https://docs.oracle.com/javase/7/docs/api/java/lang/annotation/ElementType.html)，但是自定义限定符可以，这使得创建更具针对性的限定符变得更加容易。

2.  *解决注入歧义异常*。很容易产生不明确的情况，例如，如果不止一个实现用`@Default`标记，或者用相同的限定符标记`@Named`。由于显而易见的原因，在这种情况下，CDI 将无法在运行时计算出必须将什么实现注入到目标对象中(注入过程是不明确的)，因此将抛出一个部署异常——在 CDI 术语中，这是一个*依赖注入不明确异常*。自定义限定符通过语义注释(包含与注入相关的元数据)更容易避免这些歧义。

3.  *收集元数据并将其绑定到可注入类*。这包括保留策略和可以应用限定符的合法目标，例如字段和方法。

在这一点上，很容易看出自定义限定符是将实现绑定到注入点的一种更加精炼和有效的机制。但是它们也有一个值得强调的缺点:它们很容易产生指数级的接口注释，从而不必要地污染您的代码。

生产者方法也是如此:你不想让一堆工厂分散在你的应用程序中，只是因为你可以，对吗？

根据经验，只有在真正需要的时候才使用这些特性，并且只有在`@Default`、`@Alternative`和`@Named`注释不符合您的需要时才使用。

## 摘要

像许多其他设计模式一样，依赖注入是一种语言无关的范式，在面向对象设计中起着重要作用。更简单地说:如果你正在做好的面向对象设计，你就离不开 DI。

在本教程中，我向您介绍了一个关于以非常实用的方式使用 CDI / Weld 的简单指南。您了解了如何通过使用标准的核心特性(如`@Default`和`@Alternative`注释)以及更精细的特性(包括*生产者*方法和定制限定符)来进行无痛 DI。

很有可能，CDI(及其所有实现)最吸引人的方面是它做了它表面上承诺的事情，没有任何附加条件:不使用外部框架的纯 DI。此外，考虑到只要使用标准的注释，任何对象都可以成为可注入的组件，以一种更有效的方式使用 CDI 是非常简单的。

显而易见，使用 CDI / Weld 作为 Java SE 中的底层 DI 机制是可以掌握的，而不用去碰壁。当然，您可能想知道如何在真实的用例中使用 CDI。从教学的角度来看，构建一个在控制台上运行的字符串解析应用程序是很好的，当然也可以作为熟悉该标准基本事实的起点。

但是还有更多的地方需要讨论。正如我在介绍中提到的，CDI 允许像注入[POJO](https://en.wikipedia.org/wiki/Plain_Old_Java_Object)一样轻松地注入 Java EE 资源。在开发数据库驱动的应用程序时，可以利用这种固有的能力来使用 CDI。

为了说明这一点，在第二部分中，我们将学习如何使用该标准来开发基于 JPA 的应用程序。

敬请期待！

## 分享这篇文章