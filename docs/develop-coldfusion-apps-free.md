# 进入巨龙:免费开发 ColdFusion 应用程序

> 原文：<https://www.sitepoint.com/develop-coldfusion-apps-free/>

对于刚刚起步的 web 开发人员来说，学习 ColdFusion 的想法可能会令人望而生畏，尤其是考虑到在 Adobe ColdFusion server 上托管一个商业网站可能会非常昂贵。虽然 Adobe 确实提供了 ColdFusion 服务器的免费、全功能开发版本，但它只允许本地机器上的浏览器和两个 IP 地址同时连接。这可能会对开发过程造成限制。例如，如果一个人正在寻求帮助，并希望与他人分享正在进行的工作，Adobe 的开发服务器会阻止这一点。

##### 介绍蓝龙

进入龙！也就是蓝龙服务器。BlueDragon 是一个 CFML (ColdFusion 标记语言)网络服务器(很像 Adobe 的 ColdFusion 服务器)，由一家名为 [New Atlanta](http://www.newatlanta.com/) 的公司创建。和 Adobe 服务器一样，BlueDragon 开发服务器也是免费的。但与 Adobe 的开发人员版本不同，它不限制 IP 地址，尽管免费版本不支持 SSL，不能用于商业应用程序。如果您的应用程序不需要 SSL，并且不是商业性的，那么 BlueDragon 是 Adobe 服务器的绝佳替代品。另一个优点是，您可以在自己的服务器上部署应用程序(只要它是非商业性的)。

在本文中，我将向您展示如何安装和配置 BlueDragon web 服务器，并编写一个非常简单的 ColdFusion 应用程序。这些说明主要针对第一次尝试 ColdFusion 风格开发的开发人员。

首先，让我们谈谈为 BlueDragon 和官方 Adobe 服务器提供的 CFML(冷融合标记语言)支持的差异。有些标签是不受支持的(尽管这通常是有充分理由的，因为它们不是被否决就是过时了)，有些标签是受支持的，只是略有不同(通常是以可以传递给标签的选项的形式)，有些标签包括对 Adobe 实现的增强。有关最新的兼容性信息，请访问 BlueDragon 服务器的 [New Atlanta 文档页面。](http://www.newatlanta.com/support/bluedragon/docs/index.jsp)

另一个需要注意的要点是，BlueDragon 目前不支持 CFMX8。因此，如果您正在使用 BlueDragon 学习基于 ColdFusion 的教程，请务必记住这一点，以及上面提到的标签兼容性差异。

##### 安装 BlueDragon

既然你已经意识到了 BlueDragon 和 Adobe 的 CFML 实现之间的差异，我们可以安装 BlueDragon 服务器并开始实验了。访问 [New Atlanta 的 BlueDragon](http://www.newatlanta.com/c/products/bluedragon/download/home) 下载页面下载安装程序。我假设您在本文中运行的免费版本位于页面的最底部。虽然本文只介绍了 Windows 版本的安装，但是安装程序也适用于 Mac 和 Linux。其他平台的安装说明可在[文档页面](http://www.newatlanta.com/support/bluedragon/docs/index.jsp)上找到。

安装过程很简单，会要求您同意软件许可，并决定服务器的安装位置。一个屏幕将询问您希望运行 web 服务器的端口号。默认端口是 8080，但是如果有必要的话，您可以选择另一个端口(其他开发环境，比如 Ruby On Rails，也为他们的内部 web 服务器使用这个端口)。您还可以选择将您的 BlueDragon 服务器与另一个 web 服务器集成，如图 1 所示。

![Figure 1\. Selecting the web server](img/8b3333db61923eadf7b3d659b364be7f.png)

如果您刚刚开始 ColdFusion 风格的开发，最好坚持使用内置的 BlueDragon 服务器，而不是使用 Apache 或 IIS，以防遇到任何兼容性障碍。

另一个安装步骤将要求您输入密码；由于免费的 BlueDragon 服务器不支持 SSL，所以选择一个你没有在其他系统上使用过的密码是一个好主意(考虑使用像[https://www.goodpassword.com](https://www.goodpassword.com)这样的网站来创建一个随机字符串)。安装完成后，BlueDragon 管理控制台将在您的 web 浏览器中启动。您应该看到一个登录表单，如图 2 所示，在这里您可以输入您在安装过程中指定的管理员密码。

![Figure 2\. The login screen indicating that installation was successful](img/72af3f12ee1d216e47a6baa65b13b673.png)

登录后，您将看到主控制台页面，如图 3 所示。这个控制台的详细情况超出了本文的范围，但是可以说，这里包含了 BlueDragon web 服务器的所有主要设置。

![Figure 3\. The main admin console of the BlueDragon server](img/3513e980f8fa8572c00ad14e37676816.png)

现在我们的服务器已经设置好了，是时候写一些代码来看看它的运行情况了。

##### 检查 Web 服务器

要验证除管理控制台之外的页面是否正确呈现，请在 web 浏览器中打开一个新选项卡，并在地址栏中键入以下 URL:`http://localhost:8080/`。如果您更改了默认端口 8080，请使用您的自定义端口号。应该会显示如图 4 所示的变量列表。

![Figure 4\. The default index page for the BlueDragon](img/54dde517f1fe31603c2835d1c1f4a5d0.png)

如果您有一些 web 服务器的经验，您可能想知道负责这个页面的索引文件存储在哪里。BlueDragon 服务器的文档根目录可以在 BlueDragon 的安装目录下的文件夹`wwwroot`中找到。对于 Windows 上的默认安装，文档根目录是文件夹`C:BlueDragon_Server_70wwwroot`。在这个目录中，你会找到一个名为`index.cfm`的文件。这是 BlueDragon 服务器为了生成图 4 中的页面而解析的文件。

如果您在文本编辑器中打开这个文件，您会看到一堆 HTML 以及许多以`cf`开头的标签。这是 CFML——cold fusion 的核心语言。如果您以前没有接触过 ColdFusion 开发，这段代码可能对您没有太大的意义，但请不要担心，我们现在将浏览它。

在我们分析这段代码或者编写我们自己的代码之前，让我们先用一个像样的代码编辑工具来设置一下。

##### 使用 CFEclipse 进行 ColdFusion 开发

使用 ColdFusion 开发的另一个潜在成本是 IDE(集成开发环境)的成本。当然，Adobe 出售用于开发的 Dreamweaver MX 产品，HomeSite+也可用于 ColdFusion 开发。但是这些产品是要花钱的，我们正在努力避免这种情况！幸运的是，我们可以使用 Eclipse IDE(集成开发环境)的一个免费附件，名为 [CFEclipse](http://www.cfeclipse.org/) 。

这个附加组件是通过 Eclipse 更新管理器安装的，所以首先您需要安装 [Eclipse](http://www.eclipse.org/downloads/) 。

一旦你这样做了，启动它，选择**帮助** > **软件更新** > **找到并安装**。当出现提示时，点击**选择新功能以安装**选项，这将弹出一个位置列表以供选择。没有列出 CFEclipse 插件，所以我们需要添加它——单击 **New Remote Site…** ，在 name 字段中输入`"CFEclipse"`,并输入`http://www.cfeclipse.org/update`作为 URL。完成后，确保只选中 CFEclipse 选项，然后单击**继续**。继续完成剩余的安装过程，完成后重启 Eclipse。

*Eclipse 不行为怎么办？*
*如果安装完插件后 Eclipse 没有启动，试着从命令行运行 eclipse `-clean`。*

现在已经安装了 CFEclipse，是时候创建一个新的 CFML 项目了。选择`File` > `New Project`调出新建项目向导。正如我在下面的图 5 中所做的，从 CFEclipse 类别中选择 **CFML 项目**。

![Figure 5\.  Creating a new CFML Project with CFEclipse](img/8f9332c9e968868e1685b317675679fa.png)

下一个屏幕将提示您输入项目名称—我们将第一个项目命名为 MyFirstColdfusion。从目录选项中取消选择**使用默认位置**，并浏览到我们在蓝龙安装过程中创建的`wwwroot`目录。

![Figure 6\. A sample project setup](img/d48616095f9b65db523a3fb0cc2197c3.png)

项目创建向导将提示您输入“引用的项目”，您现在可以安全地忽略这个设置。还可能会提示您切换视角。透视图是 Eclipse 用户界面的一个版本，可以根据您使用的编程语言和开发过程的阶段进行修改。继续前进，让 Eclipse 改变你当前的观点。

成功创建项目后，在项目导航器下找到 MyFirstColdfusion 文件夹，单击文件夹旁边的`+`展开视图，并找到`index.cfm`文件。双击文件名，在 Eclipse 中编辑它。

##### “你好世界！”ColdFusion 风格

默认情况下，我们的`index.cfm`文件包含许多令人困惑的标签；让我们删除该文件的全部内容，并用以下代码替换它:

```
<html>  

<head>  

  <title>BlueDragon CFML Test Page</title>  

</head>  

<body bgcolor=#FFFFFF>  

<h1>BlueDragon CFML Test Page</H1>  

<!-- Our code will go here -->  

</body>  

</html>
```

请注意，我在 HTML 文件的主体中留了一个注释，指示我们稍后将在何处添加 ColdFusion 代码。大多数编程语言的介绍都是以一句简单的“Hello world！”但是，因为我们可以使用一个简单的 HTML 标记轻松实现这个目标，所以我们将创建一个更加动态和特定于 CFML 的示例。以下是我们的示例代码:

```
<cfset hello_world = "Hello World!">  

<cfoutput>#hello_world#</cfoutput>
```

将你的网络浏览器指向`http://localhost:8080/`，你应该会看到一个页面，标题显示“蓝龙 CFML 测试页”和“Hello World！”在它的体内。

![Figure 7\. The resulting 'Hello World!' application](img/b100fc2a65af59d4a62163852513e0be.png)

祝贺您，您刚刚编写了您的第一个 CFML 程序！现在，你可能想知道，“我刚才做了什么？”让我们检查第一行代码:

```
<cfset hello_world = "Hello World!">
```

`cfset`标签用于设置变量。在本例中，我们创建了一个包含值“Hello World”的字符串，可以用名称`hello_world`来标识。在其最基本的形式中，`cfset`语法看起来像这样:

```
<cfset variable_name = value>
```

虽然我们在上面的例子中使用了一个字符串，但是我们也可以分配一个不同的数据类型，比如一个数字，一个数组或者一个列表。

让我们再看看第二行代码:

```
<cfoutput>#hello_world#</cfoutput>
```

`cfoutput`标签与其他语言中的`print`和`echo`等函数实现了相同的目标——它将输出写入浏览器(这就是它被称为，呃，`cfoutput`的原因)。标签内的`#`符号让 cfoutput 知道它包含的内容是动态的。您可以将变量或函数的结果放在`#`符号中，它将被评估并替换为动态内容。您还可以将常规文本与动态内容混合使用，如下所示:

```
<cfoutput>The variable hello_world = #hello_world#</cfoutput>
```

这将产生以下输出:

```
The variable hello_world = Hello World!
```

##### 结论

在本文中，我向您介绍了免费的蓝龙 CFML 服务器和 CFEclipse 插件。我们完成了这两个工具的安装过程，然后您使用它们编写并执行了您的第一个 CFML 脚本。如果对成本和远程 IP 限制的担心阻止了您投入 ColdFusion 开发，现在您再也没有借口了。您可以立即开始使用这些免费工具！

## 分享这篇文章