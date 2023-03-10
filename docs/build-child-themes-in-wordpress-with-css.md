# 用 2 个文件和 2 分钟在 WordPress 中创建一个子主题

> 原文：<https://www.sitepoint.com/build-child-themes-in-wordpress-with-css/>

快速建立并运行网站的最好方法之一是使用一个已经有近似外观和感觉的 WordPress 主题，并根据你的需要进行裁剪。但是，许多 WordPress 开发者犯的一个危险的错误是直接修改主题文件。这是一个大禁忌！即使它是一个简单的主题，就像 TwentyTwelve 的 WordPress 版本一样，子主题也给了你一个强大而简单的方法来做所有你想要的改变，而不会有任何风险让你精心设计的工作被一个喜欢升级的客户毁掉。

除了子主题的力量之外，我想向你展示我自己的快速创建主题设计变更的方法。使用几个现成的工具，我可以几乎按照要求的速度进行设计更改，将更改保存在子主题中，并继续进行出色、省时的设计工作。

本文需要的一些工具是 FTP、Chrome web 浏览器和一个文本编辑器。就是这样！正如我将要演示的，这个过程允许快速的站点更改，没有丢失主题升级工作的风险，这是一个常见的问题，因为越来越多的站点使用一些标准的基本主题进行开发。

### 什么是儿童主题？

子主题具有其父主题的所有特征、代码和功能。因此，如果你正在使用类似于[骨架](http://themes.simplethemes.com/skeleton/)或[响应](http://themeid.com/responsive-theme/)主题的东西，这两个很棒的免费基础主题，你可以获得父主题的所有好处，同时添加你的更改，而无需直接修改基础主题。

WordPress 将子主题作为代码的主要来源，所以如果你在父主题中有一个特定类的 CSS 在子主题中被修改，子主题的 CSS 将取代父主题。简而言之，你可以得到所有的好处，而不用担心编辑或升级会破坏你的主题。

子主题的另一个巨大但被忽视的好处是，你有一个*小得多的*文件，可以在以后引用。我曾经处理过许多主题，仅在它们的 CSS 文件中就有 2000 多行代码。我的子主题通常不到 200 行，所以跳回代码并找到我想要做的修改要容易得多。

### 步骤 1:创建一个子主题

如果你以前没有使用过儿童主题，我理解这种担心。谢天谢地，它们只需要几秒钟就可以设置好——真的。一旦就位，您就有了一个简单的方法来更改站点，而无需修改主题的核心文件。

创建一个新的 CSS 文件，并将这段代码放入其中。我假设你有 TwentyEleven 主题，因为在过去的一两年里，几乎所有的 WordPress 安装都有这个主题。

**您必须将这个文件命名为 style.css**

[source code language = " "]
/*
主题名称:二十一个孩子
模板:二十一个
*/
[/sourcecode]

将这个文件上传到你的 wp-content/themes 文件夹下的一个新文件夹中。给文件夹起什么名字并不重要，但是一个好的做法是给你正在创建子主题的主题名加上“-child”。因此，在上面的例子中，我将把我的新的子主题文档放到一个名为“twentyeleven-child”的文件夹中。

到目前为止，上面的代码没有做任何事情。但是，你可以在你的 WordPress 管理站点的管理->外观->主题部分看到它。通过选择我们刚刚创建的子主题作为您站点的主题，您将使用 2011 主题作为您的主要主题。

### 步骤 2:向您的子主题添加功能

现在，你所要做的就是添加一个 functions.php 文件到你的子主题文件夹中，你写的任何功能都将自动对你可用。如果您发现自己经常创建和重新创建相同的功能，这是一个快速向您的站点添加大量功能的强大方法。

举个例子，我有一些我喜欢使用的短代码，以及一些我希望能够快速添加到大多数客户网站的表单。所以我有一个标准的 functions.php 文件，我自动把它放到我创建的子主题文件夹中。

只需将您的功能添加到 functions.php 文件中，您就可以从头开始，并上传到您的子主题文件夹中。就是这样！否包括必要的语句或其他代码。

### 第三步:Chrome 的 Inspect Element 功能

毫无疑问，在一个实时网站上使用 CSS 最简单的方法之一就是通过谷歌 Chrome 的“Inspect Element”工具。每当您想要识别定义元素如何出现在页面上的 CSS 时，只需右键单击并选择“检查元素”:

![](img/5c9230cecb23e5e4203a27a1e1bfcf9b.png)

开发面板将弹出，您可以深入到哪个类和 CSS 被用来定义元素。

![](img/60c5c344d8c65228c9c80050bc6bb07a.png)

如果您想查看实际的 CSS 文档以及您感兴趣的 CSS 在文档中的位置，只需单击右侧面板中的文件名:

![](img/e6743a0397b9f1164cc819cabedea0fe.png)

这是一个从其他网站学习的强有力的方法，这些网站有你想要的设计元素。用 Chrome 检查一下，看看他们的 CSS 就知道了。你能学到的东西和快速适应你的网站是令人惊讶的。

### 步骤 4:使用 Chrome 进行实时 CSS 开发

Chrome 的 Inspect Element 特性中内置的另一个强大工具是，你可以实时操作 CSS。只需右键单击，检查元素，并在开发面板的右侧进行更改。您可以更改页面上的任何 CSS，并在更改时查看结果。

这是修补你的设计的一个很好的方法，看看 CSS 将如何影响整体的外观和感觉。我在进行初始更改时使用这种方法来查看我的结果，并确保我的 CSS 更改只影响我想更改的元素。(过去，我添加了一些 CSS 代码，无意中更改了一些其他内容。)

这非常有用的最后一个原因是，你不必更改 CSS，上传/保存更改，刷新页面，然后*再*查看你的更改如何影响站点。这是一个快速的方法，然后你只需要上传/保存你的 CSS 修改一次。

### 步骤 5:提交更改

一旦你有了你想要的 CSS，只需将新的 CSS 添加到你的子主题文件夹中的 style.css 文件中。你可以直接在 WordPress 的外观->编辑器菜单选项中编辑它，或者你可以使用 FTP 上传修改。

就这么简单！一旦你有了一个合适的目录，只需要几分钟就可以启动并运行子主题，并且你可以享受主题的好处，定期更新。

使用 Chrome 的 Inspect Element 特性进行快速开发可以让您使用快速、非破坏性的试错过程来实时测试 CSS 变化。然后，您只需将代码添加到您批准的 CSS 文件中，并上传/保存它。

*你在 WordPress 中还用了哪些 CSS 开发的招数？*

## 分享这篇文章