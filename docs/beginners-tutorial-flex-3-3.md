# 关于 Flex 3 的绝对初学者教程文章

> 原文：<https://www.sitepoint.com/beginners-tutorial-flex-3-3/>

##### 更进一步:ActionScript 3.0

MXML 定义了 Flex 应用程序的结构，而 ActionScript 3.0 定义了应用程序的行为。

现在，你可能会想，“等一下。如果我可以用 MXML 做这么多事情，为什么我还需要 ActionScript 3.0？”这是令人困惑的部分。MXML 实际上是 ActionScript 3.0 的一个漂亮的形式。事实上，当你编译它时，MXML 被转换成 ActionScript 3.0。让我们来看一个例子，展示 MXML 和 ActionScript 3.0 是多么的相似。下面的代码创建了相同的组件(一个按钮)，首先在 MXML 中，然后在 ActionScript 3.0 中:

```
<?xml version="1.0" encoding="utf-8"?>   

<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml"    

    layout="absolute" creationComplete="init()">   

  <mx:Button label="This one is done by MXML" x="10" y="10" />  <mx:Script>   

    <![CDATA[   

    import mx.controls.Button;   

    //Init Function is executed when the application boots   

    private function init():void {   

      //Create a new button   

      var newButton:Button = new Button();   

      //Modify Properties   

      newButton.label = "This one is done by ActionScript";   

      newButton.x = 10;   

      newButton.y = 40;   

      //Add the new button to the stage (Screen)   

      this.addChild(newButton);   

    }   

    ]]>   

  </mx:Script>         

</mx:Application>
```

编译该文件时生成的应用程序将如下所示:

![MXML and ActionScript 3.0 buttons](img/e6140d5fc6f725f19bc19a6617256234.png)

正如你所看到的，创建按钮的两种方法产生了相同的结果——但是与 ActionScript 3.0 相比，MXML 涉及的输入要少得多。用 ActionScript 3.0 设计应用程序将是一场噩梦。创建 MXML 是为了简化你的工作。

但是，您仍然需要在应用程序中使用 ActionScript 例如，您需要定义点击按钮时会发生什么。从这个角度来看:你用 MXML 设计你的应用程序，用 ActionScript 3.0 使它工作。通过使用 MXML 和 ActionScript，您可以将结构代码与编程逻辑分离开来。这是构建 Flex 应用程序时要记住的一个重要原则——尤其是当您正在构建复杂的组件时。

ActionScript 3.0 是一种基于 ECMAScript 的脚本语言，这意味着它采用 ECMA 脚本语言标准。ActionScript 3.0 是其前身 ActionScript 2.0 的巨大飞跃。原因是 ActionScript 3.0 现在是真正的面向对象编程(OOP)语言。事实上，Flex 的整个框架是由 Adobe 编写的对象类组成的。

如果你想开发复杂的 RIA，我建议你花一些时间去理解 OOP。在 Flex 中完成的大多数编程都是事件驱动的，这意味着当组件触发事件时(例如，当鼠标单击页面上的按钮时)，函数就会运行。Adobe Livedocs 站点有一些面向对象的 ActionScript 的很好的例子。

ActionScript 3.0 语法和 OOP 的全部细节超出了本文的范围，但是如果您以前做过任何 JavaScript 编程，那么您肯定已经很熟悉了。

##### 资源

Flex 3.0 正在迅速发展；因此，对于那些想开始用 Flex 构建 RIA 的人来说，这里有一些很棒的资源。这里有一个例子:

*   Flex Livedocs
*   [Flex.org](http://flex.org/)
*   [FlexCoders](http://tech.groups.yahoo.com/group/flexcoders/)
*   [开源 Flex](http://opensource.adobe.com/wiki/display/flexsdk/Flex+SDK)
*   [灵活食谱](http://www.adobe.com/cfusion/communityengine/index.cfm?event=homepage&productId=2)
*   [Flex 3 入门](http://learn.adobe.com/wiki/display/Flex/Getting+Started)

##### 摘要

这篇文章仅仅触及了 Flex 框架的表面，尽管我们确实涵盖了框架所提供的基础知识，以及 MXML 和 ActionScript 3.0 如何协同工作。虽然这是对 Flex 背后的概念的一个非常温和的介绍，但它应该给你足够的概念基础，让你自己去尝试。

现在，您已经迈出了 Flex 开发的第一步，下一步是真正理解组件，使用它们来构建您的第一个应用程序，并应用一些 ActionScript 来赋予它一些生命力。

**Go to page:** [1](https://sitepoint.com/beginners-tutorial-flex-3) | [2](https://sitepoint.com/beginners-tutorial-flex-3-2/) | [3](https://sitepoint.com/beginners-tutorial-flex-3-3/)

## 分享这篇文章