# 设计博士–从文件扩展名到屏蔽电子邮件

> 原文：<https://www.sitepoint.com/file-extensions-masked-email/>

**又是一个月，另一间候诊室里挤满了等待博士设计的病人。像往常一样，他从容应对电子邮件地址问题、多窗口问题以及众多文件扩展名的诊断…**

##### 文件扩展名–哪个是哪个？

**嘿博士设计。HTML 有这么多不同的文件扩展名。。html，。htm，。xhtml，。shtml，等等…你怎么知道使用哪一个和为了什么？—汤姆**

Tom，一旦有人向你解释了，大多数用于 HTML 的扩展就很容易理解了。让我来告诉你一些基本知识，这样在你的下一次 HTML 聚会上，你就能听起来像我一样聪明了！

*T1。htm:T3 符合 DOS 中常见的 8/3 命名标准的 HTML 文件。简单地说，这是给文件命名的“老”方法。8/3 是你得到如此惊人的应用程序名称的地方，如 winipcfg.exe、mywebpag.htm 等。除非您喜欢 3 个字符的文件扩展名，否则没有理由使用它。*

*T1。html:T3 扩展名为. HTML 的 HTML 文件。这些文件和。htm 文件，除了额外的信。*

*T1。xhtml:* 这个扩展是为符合 XML 的 html 保留的，通常被称为，你猜对了，xhtml。它实际上代表“可扩展的 HTML”。这种扩展在真正想跟上时代的 Web 开发人员中最为常见，因为 XHTML 实际上是 HTML 的未来。XHTML 文件同样可以被调用。htm，。html，。xht 和. xhtm。因为 XHTML 实际上只是格式正确的 HTML，所以你不能简单地通过看它来区分它们。

*T1。shtml: 其中最常见的是 SSI，它允许您将标题放在一个文件中，将菜单放在另一个文件中，然后动态地将它们包含到。动态创建 shtml 文件。PHP，ASP，JSP 等。所有这些都允许你做同样的事情，但是，它们要求你实际使用这些语言。SHTML 允许您在不学习复杂语言的情况下使用 includes 的强大功能。*

而周围还有很多其他人，包括但不限于:。php，. php3，.asp，。aspx，。xml，。cfm，。cfml，。jsp，。cgi 还有。pl，这个列表应该能让你暂时过得去。此外，后一组中的大多数扩展是特定于特定编程语言的。对于 asp 文件，。php for PHP 文件等)。

##### 剥掉我的书页！

医生，我想我刚刚在你的一篇文章中读到了如何做到这一点，但是我怎么也找不到！我假设我可以放一个链接到另一个 HTML 页面，在一个表格中显示该页面的精简版本。通过查看 SitePoint 上页面的“Print Article”版本的代码，看起来使用了一个表格宽度为 468 的模板，尽管我想知道是否有必要使用服务器端脚本语言(php)来实现这一点？—黄原胶

虽然我可以花半个小时来告诉你如何在不使用任何服务器端代码的情况下快速轻松地完成这项工作，但我会让这方面的专家来做。Eric Meyer，网景公司的标准顾问，最近写了一篇关于这个主题的文章。它涵盖了您正在寻找的一切，甚至更多！

##### 如何屏蔽电子邮件地址？

**Dr. Design:有没有办法屏蔽点击 mailto 链接后出现在‘收件人’栏的邮件地址？例如，不是 abc@xxx.com 出现，我希望一个名字出现:“ABC”——杰米**

嗯。我们很狡猾吗？杰米，你会很高兴知道事实上有一种方法可以做到这一点！我相信你知道，一个普通的 mailto: link 看起来很像这样:

```
<a href=mailto:me@me.com>mail me</a>
```

以下代码将允许您隐藏电子邮件地址并显示一个姓名:

```
<a href="mailto:Jeremy Wright<jwright@tacf.org>">Mail!</a>
```

##### 瞄准主窗口？关闭弹出窗口

嘿，医生，当用户点击一个指向主窗口的链接时，有可能自动关闭一个弹出窗口吗？例如，我有一个框架网站。main 打开一个弹出窗口。弹出窗口有一个链接，链接到必须在主框架窗口中加载的游戏。我让这部分工作，但弹出窗口仍然在顶部。我知道我可以改变焦点到主窗口，但我希望弹出窗口自动关闭，如果用户点击游戏链接。怎么做到的？安娜

Anna，我假设您当前的链接代码如下所示:

```
<a href="javascript:myFunc;">Go to the game!<a>
```

简单地把它改成这样，你会在不知不觉中为你的访问者节省宝贵的点击次数！

```
<a href="javascript:myFunc; window.close();">Go to the game!<a>
```

##### 让我的按钮创建一个弹出窗口

你好。当访问者订阅我的邮件列表时，没有出现“订阅成功”窗口，如何让提交按钮创建一个小的弹出窗口，显示“订阅成功”？这可能吗？现在，我的输入代码是:

**T2`<input type="submit" name="Submit" value="go">`**

**—乔希**

乔希，这是一个很好的问题！通过允许你的访问者订阅你的时事通讯，而不用离开他们所在的页面，你可以让他们继续浏览你的网站，而不需要浪费任何点击。干得好！虽然您可能认为在表单的提交按钮上需要一些 JavaScript 来实现这种功能，但是您可能会惊讶地发现这与按钮没有任何关系！你所需要的只是一些简单的 HTML！

我希望您当前的`<form>`代码看起来很像这样:

```
<form method="POST" action="MyForm.asp">
```

简单的改成这样，就大功告成了！

```
<form method="POST" action="MyForm.asp" target="_blank">
```

你可能想做的另一件事是允许你的用户通过点击一个按钮来关闭这个小窗口。以防你不确定，以下是代码:

```
<a href="javascript:window.close();">close this window</a>
```

这个月就这些了！要预约下一次，今天就给 drdesign@sitepoint.com 的设计博士发电子邮件。

传播爱！
设计博士

## 分享这篇文章