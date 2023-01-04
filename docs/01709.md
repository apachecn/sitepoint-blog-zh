# 五分钟理解 Java 的反射 API

> 原文：<https://www.sitepoint.com/java-reflection-api-tutorial/>

### 目录 

*   [反射 API](#reflectionapi)
*   [基本类型和方法](#fundamentaltypesandmethods)
*   [注解](#annotations)
*   [总结](#summary)
*   [评论](#comments)

能够反射的语言，如 Java，允许开发人员检查类型、方法、字段、注释等。并将如何使用它们的决定从编译时推迟到运行时。为此，Java 的反射 API 提供了诸如`Class`、`Field`、`Constructor`、`Method`、`Annotation`等类型。使用它们可以与编译时未知的类型进行交互，例如创建未知类的实例并调用它们的方法。

这个快速技巧旨在让您从高层次理解反射是什么，它在 Java 中是什么样子，以及它可以用来做什么。在此之后，你将准备好开始或通过更长的教程工作。为了最大限度地利用它，你应该很好地理解 Java 是如何构造的，特别是什么是类和方法，以及它们是如何关联的。了解[注释](https://docs.oracle.com/javase/tutorial/java/annotations/)解锁一个单独的部分。

## 反射 API

我想从一个简单的例子开始，而不是从头开始。首先是普通的 Java 代码:

```
URL url = new URL("https://sitepoint.com/java");
String urlString = url.toExternalForm();
System.out.println(urlString); 
```

我在编译时(意思是，当我在写代码的时候)决定*创建一个`URL`对象，并在其中调用一些方法。下面是我如何用 Java 的反射 API 做同样的事情:*

```
// the gateway to reflection is the `Class` instance
// for the class you want to operate on
Class<?> type = Class.forName("java.net.URL");

// fetches the constructor that takes a `String` argument
// and uses it to create a new instance for the given string
Constructor<?> constructor = type.getConstructor(String.class);
Object instance = constructor.newInstance("https://sitepoint.com/java");

// fetches the `toExternalForm` method and invokes it on
// the instance that was just created
Method method = type.getMethod("toExternalForm");
Object methodCallResult = method.invoke(instance);

System.out.println(methodCallResult); 
```

使用反射 API 当然比直接写代码更麻烦。但是通过这种方式，曾经被嵌入到代码中的细节(比如我使用的`URL`或者我调用的方法)变成了一个参数。因此，不必在编译时决定`URL`和`toExternalForm`，它们可以在程序运行时决定。

这种情况的大多数用例出现在“框架”环境中。例如，想想 JUnit，它想要执行所有用`@Test`注释的方法。一旦它用[类路径扫描](http://stackoverflow.com/q/1456930/2525313)找到它们，它就用`getMethod`和`invoke`来调用它们。Spring 和其他 web 框架在寻找控制器和请求映射时的行为类似。希望在运行时加载用户提供的插件的可扩展应用程序是另一个用例。

### 基本类型和方法

进入反射 API 的入口是`Class::forName`。在[的简单形式](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#forName-java.lang.String-)中，这个静态方法只接受一个完全限定的类名，并为它返回一个`Class`实例。该实例可用于获取[字段](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getFields--)、[方法](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getMethods--)、[构造函数](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getConstructors--)，等等。

要获得一个特定的构造函数，可以用构造函数参数的类型调用`getConstructor`方法，就像我上面做的那样。类似地，可以通过调用`getMethod`并传递其名称和参数类型来访问特定的方法。上面的`getMethod("toExternalForm")`调用没有指定任何类型，因为该方法没有参数。

这里有一个方法:

```
Class<?> type = Class.forName("java.net.URL");
// `URL::openConnection` has an overload that accepts a java.net.Proxy
Method openConnection = type.getMethod("openConnection", Proxy.class); 
```

这些调用返回的实例的类型分别是 [`Constructor`](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Constructor.html) 和 [`Method`](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Method.html) 。为了调用底层成员，他们提供了像`Constructor::newInstance`和`Method::invoke`这样的方法。后者的一个有趣的细节是，调用该方法的实例需要作为第一个参数传递给它。其他参数将被传递给被调用的方法。

继续`openConnection`的例子:

```
openConnection.invoke(instance, someProxy); 
```

如果要调用静态方法，实例参数将被忽略，因此可以是`null`。

### 释文

注释是反射的重要组成部分。事实上，注释主要是针对反射的*。它们旨在提供可以在运行时访问的元信息，然后用于塑造程序的行为。(如前所述， [JUnit 的`@Test`](http://junit.org/junit5/docs/current/api/) 和 [Spring 的`@Controller`](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/stereotype/Controller.html) 和 [`@RequestMapping`](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestMapping.html) 就是很好的例子。)*

所有重要的反射相关类型如`Class`、`Field`、`Constructor`、`Method`和`Parameter`都实现了[和`AnnotatedElement`接口](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/AnnotatedElement.html)。链接的 Javadoc 包含了注释如何与这些元素相关(直接存在、间接存在或关联)的完整解释，但它最简单的形式是这样的:[`getAnnotations`方法](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/AnnotatedElement.html#getAnnotations--)以 [`Annotation`实例](https://docs.oracle.com/javase/8/docs/api/java/lang/annotation/Annotation.html)的数组形式返回该元素上存在的注释，然后可以访问这些实例的成员。

!["Using reflection to get what you need "](img/fce30e6ee6ac1e7a5c98a23294f44082.png)

## 摘要

Java 的反射 API 允许类型、方法、注释等的自省。以及编译时未知的构造函数和方法的调用。首先调用`Class.forName("fully.qualified.class.Name")`，然后调用`getConstructors`、`getMethods`、`getAnnotations`，或者类似的方法。对于构造函数使用`newInstance`，对于方法使用`invoke`进行调用。

你也可以使用[反射来插入代码](http://tutorials.jenkov.com/java-reflection/private-fields-and-methods.html)并改变非公共字段或调用非公共方法——尽管这是一种冒险的做法，而且在 Java 9 中实现[更加困难。如果你很好奇，想知道所有 API 的来龙去脉，试试 Java 官方文档](https://www.sitepoint.com/reflection-vs-encapsulation-in-the-java-module-system/)中的[反射轨迹。到目前为止，API 已经有点过时，有一些缺点，但是有更新的替代方法，看看](http://docs.oracle.com/javase/tutorial/reflect/index.html)[方法句柄](http://stackoverflow.com/q/8823793/2525313)(从 Java 7 开始)和[变量句柄](http://openjdk.java.net/jeps/193)(从 Java 9 开始)。

## 分享这篇文章