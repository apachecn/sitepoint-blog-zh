# 通用开发人员的 CFC 第 2 部分

> 原文：<https://www.sitepoint.com/cfcs-for-the-common-developer-part-2/>

所以[上次](https://www.sitepoint.com/cfcs-for-the-common-developer/)我们学习了创建 CFC 的基础知识，以及一个非常基础的 CFC 的整体结构。今天我们将讨论在我们的代码中使用 CFC 的不同方式。

那么我们如何在 CFC 中访问这个函数呢？我们可以使用 CFInclude 标签将它包含到 CFML 页面中吗？或者还有其他的吗？如果你猜到还有更多，那你就猜对了！CFC 有几种使用方式，基于标签的，基于脚本的，还有另一种基于标签的实现。

#### **基于标签的实现#1**

现在，在最基本的层面上，CFC 很像定制标签。为了使用你在 CFC 中创建的函数，你必须调用它们，然后输出它们返回的数据。因此，对于我们的问候 CFC，我们会这样称呼它:

```
<cfinvoke component="greeting" method="getGreeting" returnvariable="myGreeting">
<cfoutput>#myGreeting#</cfoutput>
```

这将显示*欢迎客人！祝你参观愉快，请尽快回来。无论我们在哪里输出变量 myGreeting。这里我们使用 CFInvoke 标记来“调用”我们的 CFC，这只是“调用”、“实例化”甚至“创建”的一种花哨说法。CFInvoke 标签有很多属性，但是我们现在只关心 3 个。Component 是我们要使用的组件的名称。在上面的代码中，greeting 组件与编写 CFInvoke 标记的页面位于同一文件夹中。如果我将 CFC 放在另一个文件夹中，我会使用 do 符号告诉 ColdFusion 在哪里可以找到我的 CFC。例如，如果我的页面位于根文件夹中，而我的 CFCs 位于根文件夹下名为 components 的文件夹中，我可以使用以下代码:*

```
<cfinvoke component="components.greeting" method="getGreeting" returnvariable="myGreeting">
<cfoutput>#myGreeting#</cfoutput>
```

如果我的 CFC 位于根文件夹之外名为 Global 的映射中，但位于名为 CFC 的子文件夹中，我会这样调用它:

```
<cfinvoke component="Global.cfc.greeting" method="getGreeting" returnvariable="myGreeting">
<cfoutput>#myGreeting#</cfoutput>
```

当告诉 ColdFusion 在哪里可以找到您的 CFC 时，您甚至可以使用变量:

```
<cfinvoke component="#application.CFCPath#.greeting" method="getGreeting" returnvariable="myGreeting">
<cfoutput>#myGreeting#</cfoutput>
```

如果我们想传入一个访问者的名字，我们该怎么做呢？我们的 CFC 可以接受一个名为“visitor”的参数，要将一个值传递给这个参数，您可以像这样使用 CFInvokeArgument 标记:

```
<cfinvoke component="greeting" method="getGreeting" returnvariable="myGreeting">
     <cfinvokeargument name="visitor" value="Eric">
</cfinvoke>
<cfoutput>#myGreeting#</cfoutput>
```

您可以在上面的代码中看到，我们添加了第 2 行和第 3 行。第 2 行是实际的 CFInvokeArgument 标记，它有两个属性，name 是我们的 CFC 中的变量名，value 是我们要传递到 CFC 中的值。第 3 行是结束的 CFInvoke 标记，在前面的例子中没有。对于那些想知道是否可以在 value 属性中传递变量的人，只需记住用井号(#)标记它们。

### **基于标签的实现#2**

那么为什么我们需要两种方法来调用带有标签的 CFC 呢？好的实施#1 是好的和有效的，但是 CFC 只存在于当时和那里。当您使用实现#1 中的代码调用 CFC 时，CFC 由 ColdFusion 创建，您也可以说 instantiated，这是同一件事，然后调用函数并输出数据。然后 ColdFusion 会破坏 CFC 并将其从服务器内存中清除，但不会破坏文件本身，所以不要担心。如果你以后不想使用 CFC 或它的任何功能，这很好，但如果你决定要使用，那么 ColdFusion 必须重新经历整个创建过程。在编程中，我们称这种创建/销毁过程为“昂贵的”,因为它每次都使用内存和处理器单元来完成任务。如果我们可以创建 CFC，然后将它存储在应用程序范围或会话范围内，并在整个站点中重用它，那将会好得多，这就是实现#2 的用武之地。

首先，我们必须创建 CFC，因为我们希望 CFC 一直存在，所以我们将在我们的应用程序范围内创建它。对于那些不知道应用程序作用域是一个持久作用域的人来说，这意味着在这个作用域中创建的任何变量都将持续存在，直到它们被重写、删除或服务器重新启动。这是一个存储您在整个应用程序中需要的东西的好地方。下面是完成这个小任务的代码:

```
<cfobject type="component" name="Application.siteGreeting" component="greeting">
<cfinvoke component="#application.sitegreeting#" method="getGreeting" returnvariable="myGreeting">
     <cfinvokeargument name="visitor" value="Eric">
</cfinvoke>
```

在很大程度上，这段代码看起来非常类似于我们在实现#1 中所做的，但是有所改变。首先看第 1 行，您会看到我们添加了一个名为 CFObject 的新标签，它有 3 个属性。type 属性告诉 ColdFusion 我们想要创建什么，在本例中它是一个组件。接下来是 name，这是将从这个标签创建的变量，它将是我的 CFC。最后是组件属性，它的工作方式就像 CFInvoke 标记中的组件属性一样，这意味着它告诉 ColdFusion 在哪里可以找到我们想要创建的组件。现在，一旦这个标签运行，我们将拥有一个 CFC 的持久化实例或副本，我们可以将其引用为 Application.siteGreeting。

接下来，我们使用 CFInvoke 标记来实际使用 CFC 并调用 getGreeting 函数。您会注意到，除了 component 属性之外，它与实现#1 完全一样。在这里，我们实际上是在传递我们用 CFObject 标记创建的变量，并敲打它，而不是把它传递给我们的 CFC。在这个例子中，CFObject 标记就在 CFInvoke 标记之前，但实际上这两个标记可能相隔多行，甚至在不同的文件中。只要创建了我的 application.siteGreeting，那么我的 CFInvoke 标记就会运行，不会有任何问题。

那么我们为什么要这样呢？毕竟这是额外的一行代码，它真的能为我们节省那么多吗？对于这个例子，不，可能不是，但是随着时间的推移，只使用 CFInvoke 标记来创建和销毁 CFC 的代价会非常高。想想如果成千上万的人同时点击这个页面。ColdFusion 会在每次运行页面时创建和销毁相同的 CFC。通过将它创建到一个限定了作用域的变量中，它更加持久，而且由于它已经存在，使用起来也不那么昂贵。就像在街角商店买汽水一样。每次你想喝东西的时候，你必须去商店，挑选你的汽水，在收银台付款，然后你就可以喝了。这就是实现#1 的工作方式，但是如果你已经买了汽水，甚至是一箱汽水，它们就在你的冰箱里，你可以省去“去商店”、“挑选”和“在收银台付款”这些步骤，因为你已经有了，可以随时享用汽水。

### **基于 CFScript 的实现**

如果你是一个写剧本的人，有时我也是，你可能会喜欢这个版本:

**版本#1**

```
<cfscript>
     siteGreeting = CreateObject('component','greeting');
     myGreeting = siteGreeting.getGreeting(visitor='Eric');
</cfscript>
```

**版本#2**

```
<cfscript>
     application.siteGreeting = CreateObject('component','greeting');
     myGreeting = application.siteGreeting.getGreeting(visitor='Eric');
</cfscript>
```

版本#1 和版本#2 99%相同，除了在版本#2 中，我们在应用范围内创建 CFC，就像基于标签的实现# 2 一样。在这里，您可以看到我们使用了 CFScript 标记来设置 ColdFusion 脚本环境。ColdFusion 主要是一种基于标记的语言，这是它的已知之处，但它也有一个脚本方面，允许您做很多事情，但不是您可以在标记方面做的所有事情。CFScript 标记之间的所有内容都是 ColdFusion 脚本，它可能看起来很像您见过的其他脚本语言。在上面例子的第 2 行，我们使用 CreateObject 函数创建 CFC，就像我们在实现#2 中使用 CFObject 标签一样。当你通过 CFScript 创建 CFC 时，你必须总是使用 CreateObject 函数，因为没有像基于标签的 CFInvoke 那样的一体化解决方案。因此，第一个版本我们创建 CFC 并将其赋给变量 siteGreeting，在第二个版本中，它被赋给变量 application.siteGreeting。在第 3 行，我们实际上使用点标记法调用我们的函数，记住基于标记的实现#1 中的点标记法，使用格式 CFCNAME.functionName。现在，因为第二个版本在应用程序范围中有 CFC，所以在那里有一个额外的点，它更像范围。CFCNAME.functionName 但是基本上是一回事。你会注意到，当我传入我们的参数 visitor 时，我是在括号内以 visitor='Eric '传递的。当使用 CFC 的 CFScript 方式时，你可以在这个 name=value 对中传递你的变量，或者你可以只是以逗号分隔列表的形式传递值，一定要引用字符串值。但是当您将它们作为列表传递时，您必须按照您的 CFC 设置了 cfargument 标记的顺序来传递这些值。例如，如果我的 CFC 看起来像这样:

```
<cfcomponent displayname="MyFirstCFC" hint="It's just a CFC folks, don't freak out">
     <cffunction name="getGreeting" output="false" returntype="string" description="Returns a greeting string">
          <cfargument name="visitorFirstName" required="no" type="string" displayname="Visitor First Name String" hint="The Visitors name">
          <cfargument name="visitorLastName" required="no" type="string" displayname="Visitor Last Name String" hint="The Visitors name">
          <cfif IsDefined('arguments.visitor') AND Len(Trim(arguments.visitor))>
               <cfreturn 'Welcome ' & arguments.visitorFirstName & ' ' & arguments.visitorLastName & '! I hope you enjoy your stay.'>
          <cfelse>
               <cfreturn 'Welcome guest! Enjoy your visit and please come back soon.'>
          </cfif>
     </cffunction>
</cfcomponent>
```

那么我要么像这样传递变量:

my greeting = application . site greeting . get greeting(visitor first name = ' Eric '，visitor last name = ' Jones ')；

运筹学

my greeting = application . site greeting . get greeting(' Eric '，' Jones ')；

只有在第一个例子中，我做了 name = value 对，我才能把它们混在一起，把“姓”放在前面，把“名”放在最后，而没有任何问题。如果我在第二个例子中把它们混在一起，那么我的名字将作为“Jones Eric”而不是“Eric Jones”返回。因此，在 name=value 对中传递参数总是一个好主意，除非只有一个参数要传递。

好了，这篇文章到此结束，我想我暂时不用为普通开发者使用 CFC 了，但是请随时在这里或者论坛上发表你的问题。谁知道也许你会启发我的下一篇文章！

## 分享这篇文章