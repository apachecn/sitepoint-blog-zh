# Flex 4 有什么新的很酷的东西？

> 原文：<https://www.sitepoint.com/whats-new-flex-4/>

Flex 4 beta 1 的发布已经有一段时间了。它有一个非常公开的开发过程，始于去年 8 月，当时 Adobe 在其开源网站上公布了 Flex 4 的早期版本——以前的代号是 Gumbo。虽然在此期间 Flex 4 的一些功能集已经过修改和完善，但大多数早期功能仍然存在。在本文中，我们将介绍 Flex 4 中一些更重要的变化。

**最后，有一个测验—[测试您的 Flex 4 知识，](https://www.sitepoint.com/quiz/adobe/whats-new-flex-4)您将有机会获得一份 Flex Builder 3 Standard，并免费升级到 Flash Builder 4！**

##### 名称空间

Flex 使用名称空间的原因有很多:标识所用语言的版本，给出一个范围，我们可以用它将 XML 标记映射到特定的 ActionScript 类，以及告诉编译器自定义组件的位置。Flex 4 已经更新到新的 MXML 2009 名称空间，现在包含新的语言标签。组件现在在它们自己的名称空间中声明，Flex 3 (Halo)和 Flex 4 (Spark)组件都有自己的名称空间。

这意味着应用程序文件现在可能在应用程序标记中有三个名称空间声明:

```
<s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"  

  xmlns:s="library://ns.adobe.com/flex/spark"  

  xmlns:mx="library://ns.adobe.com/flex/halo"> 

</s:Application>
```

细心的读者会注意到，新的 MXML 2009 命名空间以`fx`为前缀。它用于特殊的语言实体；有些您已经很熟悉了，而有些则是随着 Flex 4 的推出才出现的。

许多 Flex 3 标签有 Flex 4 的对等物，但是一些新的语言标签已经作为这个名称空间的一部分被引入。其中包括:

*   `fx:Declarations`
*   `fx:Private`
*   `fx:Library`

```
fx:Declarations is used to define non-visual children of the application that represent property declarations; these are now no longer permitted to be under the application root on their own. One example would be the declaration of service calls, for example, RemoteObject, WebService, and HTTPService.

```
fx:Private is used to store information that will be ignored by the compiler, such as developer details and version numbers. It's also used to store design-related information that's unnecessary to the operation of Flash Builder or the compiler.  It's likely that this tag will contain additional data that's unique to a tool capable of exporting design content, such as Illustrator, which could be used by various Adobe tools as part of a Flash Catalyst workflow - more on that later.

```
fx:Library is used to define and store graphical fx:Definition children, which can be reused within the application. Graphical objects created in fx:Library are only instantiated when they're used.让我们用一个例子把所有这些放在一起。清单 1 中的[代码演示了`fx:Library`、`fx:Definition`和`fx:Private`标签的使用:它呈现了两个黄色的星星，这两个星星“夹”着一个由黑色到透明渐变填充的正方形。](https://www.sitepoint.com/examples/flex4beta/stars.mxml.txt)

![Stars and a square from Listing 1](img/3b3a298759520b19f58e4574009a038e.png)

说明性图形

Flex 4 将充分利用 CS4 图形应用程序使用的新图形交换格式 [FXG](http://opensource.adobe.com/wiki/display/flexsdk/FXG+1.0+Specification) 。Flex 4 和 Flash Player 10 可以一起呈现 FXG 图形元素。其中包括:

	*   图形和文本原语
	*   填充、描边、渐变和位图
	*   支持滤镜、蒙版、alphas 和混合模式。

FXG 图形元素可以以多种方式在 Flex 4 中使用。上面的示例代码包括两个 FXG 元素，一个正方形和一个星形。FXG 元素可以在 MXML 文件或组件中使用，也可以另存为。fxg 文件格式。CS4 工具通常会使用 FXG 2008 名称空间导出 FXG。因为这些文件是为在图形应用程序之间共享而设计的，你会发现它们的格式比 Flex 中使用的更严格；Flex 编译器将只使用 FXG 特定的标签。Adobe 的新应用程序 Flash Catalyst 将使创建与 Flex 4 兼容的 FXG 图形变得容易，然后您可以使用它来完成您的应用程序的用户界面和外观。

Flex 4 中添加了新的效果，这些效果也可以应用于 FXG 图形元素。这些新的效果将对新旧组件都有效。独立效果现在必须在`fx:Declarations`标签中声明，而不是在应用程序的主体中。同样，Flash Catalyst 有额外的工具来帮助更容易地使用这些效果。

CSS 处理在这个新版本中也得到了改进。其中一个原因是 CSS 在组件的皮肤中扮演着更重要的角色。添加了额外的选择器，包括 id 选择器和后代选择器，它们可以应用于组件；这允许您根据组件是否是某个特定组件的后代来应用样式，例如，`Button`是`HBox`的后代。如果您熟悉后代 CSS 选择器，您应该会发现这是处理这些组件的一种直观方式。

##### 布局

在 Flex 中，在构建组件时，您可以根据组件的布局属性来选择组件:一个`Canvas`将为您提供绝对的`x, y`定位，一个`VBox`的内容以垂直堆栈的形式布局，一个`HBox`将其内容以水平方式布局，应用程序标签提供了所有三种布局选项的选择。Flex 4 中新的`layout`标签也提供了所有三种布局选项的选择；它的目的是将布局规则从单个组件中分离出来，让开发人员能够控制组件子组件的位置。一个优点是可以在运行时为组件定义、更改或删除布局。

下面的示例代码指定了一个垂直布局，并将在应用程序中显示两个垂直堆叠的按钮:

```
<?xml version="1.0" encoding="utf-8"?>  

<s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"   

  xmlns:s="library://ns.adobe.com/flex/spark"   

  xmlns:mx="library://ns.adobe.com/flex/halo">  

  <s:layout>  

    <s:VerticalLayout/>  

  </s:layout>  

  <s:Button label="button one"/>  

  <s:Button label="button two"/>  

</s:Application> 
```

##### 州

向大多数 Flex 开发人员询问视图状态，他们会告诉你最好用 Flex Builder 来创建。在 Flex 3 中，states 描述了如何以编程方式在可视层中添加或删除可视元素，并使用类似的标签来修改样式、属性和事件。状态意味着可以在 Flex 中对 UI 进行有效的更改，并有效地重用可视元素。

使用 Flex Builder 进行此过程更容易的原因是，IDE 能够记录更改状态所需的各种步骤，并将这些更改转换为必要的代码。您可以编写与它自己生成的代码相同的代码，但是为什么要这样做呢？需要花费大量的时间来确定将要发生的事情的顺序。

现在，Flex 4 已经更新了状态:以前使用的标签已经过时，各种状态所需的更改现在是您使用的可视组件的元素。组件现在总是存在，而不是在切换到特定状态时以编程方式添加。这意味着不再有像过去那样因变量缺失而导致的失败。虽然可视化管理状态可能会更快，但在代码中应用更改的方式更符合逻辑。

状态本身的创建方式类似于 Flex 3；每一个都用嵌套在一对`states`标签中的`State`标签来定义。在过去，基本视图状态必须通过设置一个`currentState`属性来明确定义——相反，第一个状态被认为是默认状态。

Flex 3 中的`AddChild`和`RemoveChild`类已经被替换为`includeIn`和`excludeFrom` MXML 标签属性，如果需要，这两个属性都可以是多个状态的逗号分隔列表。可视化的`SetProperty`和`SetStyle`类，以及`SetEventHandler`类，已经被替换为新的`propertyName.stateName`语法。

让我们看一个例子，在清单 2 中有完整的展示。注意代码中两种状态的定义——一种是默认状态，另一种是表单提交后显示的状态:

```
<s:states>  

  <s:State name="defaultState"/>  

  <s:State name="formSubmit"/>  

</s:states>
```

我们还可以看到，`Label`组件有一个为`defaultState`定义的文本值:

```
<mx:Label id="formLabel" x="20" y="16" text.defaultState="Your name:"/>
```

还要注意`TextInput`上的`includeIn`属性——它只在我们处于`defaultState`时才出现:

```
<s:TextInput id="formInput" x="97" y="15" includeIn="defaultState"/>
```

对于不同的状态，`Button`具有属性变化:对于`formSubmit`状态，其`x`和`y`属性使用点符号来改变；那就是，`x.formSubmit="20"`。该按钮还有两个 click 事件处理程序，一个用于一种状态，另一个用于变回 defaultState:

```
<s:Button id="formButton" x="295" y="15.5" label="Submit"   

  label.formSubmit="Again" x.formSubmit="20" y.formSubmit="38"   

  click.defaultState="formHandler()"   

  click.formSubmit="currentState='defaultState'"/>
```

这些只是您在 Flex 4 开发中日常使用的一些特性。这个测试版是对 Flex 的重大升级；大量的改变将使 Flex 开发变得更容易，并让你对你的项目有更多创造性的控制。随着 Flex 爱好者逐渐熟悉这些优秀的新功能，我们一定会看到该技术的一些令人兴奋的新用途。您可以在 Adobe Labs 了解 Flex 4 的最新动态并获取 Flex 4 beta SDK。

感觉自信吗？[通过我们的快速测验，测试您对 Flex 4 新增功能的了解程度。](https://www.sitepoint.com/quiz/adobe/whats-new-flex-4)三名幸运的参赛者将赢得一份 Flex Builder Standard，并免费升级到 Flash Builder 4！

## 分享这篇文章

```

```

```