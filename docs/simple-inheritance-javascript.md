# JavaScript 的简单继承

> 原文：<https://www.sitepoint.com/simple-inheritance-javascript/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

我的很多朋友都是 C#或 C++开发人员。他们习惯于在他们的项目中使用继承，当他们想学习或发现 JavaScript 时，他们问的第一个问题是:“但是我怎样才能用 JavaScript 实现继承呢？”

实际上，JavaScript 使用不同于 C#或 C++的方法来创建面向对象的语言。它是一种基于原型的语言。原型的概念意味着行为可以通过克隆作为原型的现有对象来重用。JavaScript 中的每个对象都源于一个原型，该原型定义了该对象可以使用的一组函数和成员。没有课。只是物品。然后，每个对象都可以用作另一个对象的原型。

这个概念非常灵活，我们可以用它来模拟 OOP 中的一些概念，比如继承。

## 实现继承

让我们用 JavaScript 想象一下我们想要用这个层次结构创建什么:

![JavaScript Inheritance Hierarchy](img/4df4b4fdf087c102790b716ded73d038.png)

首先，我们可以轻松创建 ClassA。因为没有显式的类，所以我们可以通过创建这样的函数来定义一组行为(一个类……):

```
var ClassA = function() {
    this.name = "class A";
}
```

这个“类”可以使用关键字`new`实例化:

```
var a = new ClassA();
ClassA.prototype.print = function() {
    console.log(this.name);
}
```

并通过我们的对象来使用它:

```
a.print();
```

相当简单，对吧？

完整的示例只有 8 行:

```
var ClassA = function() {
    this.name = "class A";
}

ClassA.prototype.print = function() {
    console.log(this.name);
}

var a = new ClassA();

a.print();
```

现在让我们添加一个工具来创建类之间的“继承”。这个工具只需要做一件事:克隆原型:

```
var inheritsFrom = function (child, parent) {
    child.prototype = Object.create(parent.prototype);
};
```

这正是奇迹发生的地方！通过克隆原型，我们将所有成员和函数转移到新的类中。

因此，如果我们想添加第二个类，作为第一个类的子类，我们只需使用以下代码:

```
var ClassB = function() {
    this.name = "class B";
    this.surname = "I'm the child";
}

inheritsFrom(ClassB, ClassA);
```

然后，因为`ClassB`从`ClassA`继承了`print`函数，下面的代码正在工作:

```
var b = new ClassB();
b.print();
```

并产生以下输出:

```
class B
```

我们甚至可以覆盖`ClassB`的`print`函数:

```
ClassB.prototype.print = function() {
    ClassA.prototype.print.call(this);
    console.log(this.surname);
}
```

在这种情况下，生成的输出将如下所示:

```
class B 
I’m the child
```

这里的技巧是调用`ClassA.prototype`来获得基本的`print`函数。然后通过`call`函数，我们可以调用当前对象的基础函数(`this`)。

创建`ClassC`现在很明显:

```
var ClassC = function () {
    this.name = "class C";
    this.surname = "I'm the grandchild";
}

inheritsFrom(ClassC, ClassB);

ClassC.prototype.foo = function() {
    // Do some funky stuff here...
}

ClassC.prototype.print = function () {
    ClassB.prototype.print.call(this);
    console.log("Sounds like this is working!");
}

var c = new ClassC();
c.print();
```

输出是:

```
class C 
I’m the grandchild 
Sounds like this is working!
```

## JavaScript 的更多实践

这可能会让你有点惊讶，但微软有许多关于开源 JavaScript 主题的免费学习，我们的任务是随着 [Project Spartan 的到来](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)创造更多。查看我自己的:

*   [web GL 3D，HTML5，Babylon 介绍。JS](http://www.microsoftvirtualacademy.com/training-courses/introduction-to-webgl-3d-with-html5-and-babylon-js?WT.mc_id=12833-DEV-sitepoint-othercontent)
*   [用 ASP.NET 和安古拉杰构建单页应用](http://channel9.msdn.com/Events/Build/2014/3-644?WT.mc_id=12833-DEV-sitepoint-othercontent)
*   [HTML 中的前沿图形](http://channel9.msdn.com/events/Build/2014/3-558?WT.mc_id=12833-DEV-sitepoint-othercontent)

或者我们团队的学习系列:

*   [让你的 HTML/JavaScript 更快的实用性能技巧](http://channel9.msdn.com/Series/Practical-Performance-Tips-to-Make-Your-HTMLJavaScript-Faster/06)(从响应式设计到休闲游戏再到性能优化的 7 部分系列)
*   现代网络平台快速启动(HTML、CSS 和 JS 的基础)
*   [使用 HTML 和 JavaScript JumpStart 开发通用 Windows 应用](http://www.microsoftvirtualacademy.com/training-courses/developing-universal-windows-apps-with-html-and-javascript-jump-start?WT.mc_id=12833-DEV-sitepoint-othercontent)(使用你已经创建的 JS 来构建应用)

以及一些免费工具: [Visual Studio 社区](http://www.asp.net/get-started/websites?WT.mc_id=12779-DEV-sitepoint-link)、 [Azure 试用版](http://azure.microsoft.com/en-us/develop/net/aspnet/?WT.mc_id=12779-DEV-sitepoint-link)，以及[针对 Mac、Linux 或 Windows 的跨浏览器测试工具](http://modern.ie/?utm_source=SitePoint&utm_medium=other&utm_campaign=SitePoint)。

## 还有一些哲学…

最后，我只想明确声明，JavaScript 不是 C#也不是 C++。它有自己的哲学。如果你是一名 C++或 C#开发人员，并且你真的想充分发挥 JavaScript 的威力，我能给你的最好建议是:不要试图将你的语言复制到 JavaScript 中。没有最好或最差的语言。只是哲学不同罢了！

本文是微软网站开发技术系列的一部分。我们很高兴与你分享斯巴达项目的[和它的](http://blogs.msdn.com/b/ie/archive/2015/01/22/project-spartan-and-the-windows-10-january-preview-build.aspx/?WT.mc_id=12833-DEV-sitepoint-othercontent)[新渲染引擎](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)。在 [modern 获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://modern.ie/?utm_source=SitePoint&utm_medium=other&utm_campaign=SitePoint)。

## 分享这篇文章