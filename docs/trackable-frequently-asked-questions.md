# 建立一个 FAQ 页面来跟踪流行的问题

> 原文：<https://www.sitepoint.com/trackable-frequently-asked-questions/>

从设计的角度来看，FAQ 页面通常相当简单。但是，他们也提出了一个问题:这些问题中哪些是真正“经常被问到”的？当涉及到 FAQ 内容时，我们可以通过跟踪 FAQ 页面上的用户活动并分析结果来找出哪些问题是真正常见的问题，而不是依靠猜测。

在本教程中，我将向您展示如何使用 JavaScript 和 PHP 创建一个简单的页面跟踪工具。我们将生成非常简单的文件，您可能能够直接放入您的网站，只需对您当前的页面进行很少的更改，就可以称之为好文件。

时不时地，我会被要求去帮助一个客户确定一个特定的页面在它特定的用途上有多有效。有时我可以使用 Google Analytics 或其他用户跟踪工具，但有时我需要一个定制的工具来真正了解页面的实际效果。

一个很好的例子是“常见问题”页面，其中很多都有内部链接。用户可以点击一个目录区域，页面会跳转到同一页面上的锚文本。在这种情况下，谷歌分析无法开箱即用，大多数其他跟踪工具也是如此。这是因为从技术上讲，用户并没有进入一个新的页面。

### 步骤 1:准备你的 FAQ 页面

您需要做的第一件事是让 FAQ 页面能够处理可点击的锚文本。考虑下面的 HTML 示例:

[sourcecode language="html"]

# 常见问题

<H3><a name = " TOC ">目录</a></H3>
<ul>
<李><a id = " Who _ TOC " href = " # Who ">谁会想要这个？</a></李>
< /ul >

### –[返回页首^](#toc)

<p>curabitur elementum consequat nisl vel ornare。你是一个聪明的人，你是一个聪明的人。< /p >

[/sourcecode]

这个例子是一个 FAQ 条目，但是你应该在你自己的页面上识别关键元素。您有一个目录和 FAQ 的标题，这也是一个到页面更下方区域的链接。然后你会在页面的后面看到完整的问题(和答案)和一个“返回页首”按钮。

对于 FAQ 页面来说，这是非常标准的格式。这里要注意的是，您有跟在“href”或“name”后面的

### 步骤 2:启用 jQuery

不要害怕！这可能是本教程中最简单的部分，因为您唯一要做的就是添加一个简单的引用。下面例子中的第一行是你需要添加的，但是我包含了上面的代码只是为了告诉你它需要被引用的地方。

(注意:如果您使用的是 WordPress 或其他 CMS，您可能已经运行了 jQuery。如果出现错误，尝试删除这一行，看看是否能消除冲突。)

[source code language = " html "]
<脚本 src = " https://code . jquery . com/jquery-1 . 10 . 1 . min . js "></脚本>

# 常见问题

<H3><a name = " TOC ">目录</a></H3>
<ul>
<李><a id = " Who _ TOC " href = " # Who ">谁会想要这个？</a></李>
< /ul >

### –[返回页首^](#toc)

<p>curabitur elementum consequat nisl vel ornare。你是一个聪明的人，你是一个聪明的人。< /p >

[/sourcecode]

### 第 3 步:添加一些 JavaScript 功能

为了让这个系统工作，我们将在页面中添加一些 JavaScript。具体来说，对于那些需要具体细节的人，我们使用 jQuery 和 AJAX。

你会问为什么？AJAX 允许我们将数据发送到可以存储数据的服务器，而无需刷新页面。在页面上使用锚链接的目的是确保用户不必每次都重新加载页面。所以 AJAX 允许我们在将数据尽可能不可见地传递给服务器的同时留在页面上。

我们要做的就是将 onclick 函数添加到我们的链接中:

[sourcecode language="html"]

# 常见问题

<H3><a name = " TOC ">目录</a></H3>
<ul>
<Li><a id = " Who _ TOC " href = " # Who " onclick = " logit(this . id)">谁会想要这个？</a></李>
< /ul >

### –[返回页首^](#toc)

<p>curabitur elementum consequat nisl vel ornare。你是一个聪明的人，你是一个聪明的人。< /p >

[/sourcecode]

下一步，我们将创建一个名为“logit”的 JavaScript 函数，它将处理我们的点击数据的存储。在这一步中，我们告诉浏览器在用户点击链接时运行该功能。

简而言之，当用户单击链接时，浏览器会将链接的 ID 发送给该函数。在上面的例子中，id 分别是“who_toc”和“who_back”。

### 步骤 4:创建 Javascript 代码

现在，我们需要创建这个“logit”函数，这样一旦用户单击页面上的链接，页面就知道该做什么。这是一个非常简单但功能强大的脚本，您可以在站点的任何地方使用:

[sourcecode language="html"]