# 逐步雅加达挂毯

> 原文：<https://www.sitepoint.com/jakarta-tapestry/>

Jakarta Tapestry 框架是 Java web 开发的隐藏宝藏。它极大地简化了网页设计者和开发者的工作。它通过透明地处理大量令人厌烦的“管道工作”提供了极大的便利。它甚至改变了 web 开发的范式(至少在表示层方面),使之更加自然。

然而，Tapestry 对大多数 web 开发人员来说或多或少还是陌生的，原因有很多。最明显的是 Tapestry 传说中的“陡峭的学习曲线”，在我看来，这与其说是事实，不如说是神话:Tapestry 实际上比许多其他框架更容易学习和使用。是的，Tapestry 内部比它的前辈更复杂，就像当代摄像机比我们的祖父用来制作业余电影的机械设备复杂得多一样。但是便携式摄像机更难使用吗？不，这要简单得多(相信我，这两种设备我都试过)。

好了，洗脑够了，我们直接跳进去吧！在本教程结束时，您将能够自己决定 Tapestry 的易用性。我们不会在这里创建传统的“Hello World”web 应用程序——这对于 Tapestry 来说实在是太琐碎了。我们将从更现实的东西开始:一个登录表单。

在我们揭开 Tapestry 奇迹的过程中，我们需要经历一种炼狱——在我们的计算机上创建一个工作的、Tapestry 就绪的配置的过程。这或多或少是 Java web 开发的标准实践，所以我们将尽可能使它简单。

如果您曾经尝试过任何类型的 Java Web 开发——使用 servlets、JSP 或 Struts——很可能您的机器上已经有了大多数必要的组件。但是如果没有，不要担心:我们将非常快速和容易地安装一切。事实上，安装主要是下载和解压现成的软件。

##### 要求

我们需要以下物品:

*   Java 开发工具包(JDK)
*   Tomcat Web 服务器
*   Eclipse 集成开发环境(IDE)
*   Eclipse IDE 的主轴插件
*   Tapestry 特有的库

如果您的计算机上已经安装了这些组件，只需跳过下面说明中的适当部分。

尽管 Tapestry 4 刚刚发布，我们将从框架的第三个版本开始。Tapestry 开发的许多概念在两个版本中或多或少是相同的——尤其是从初学者的角度来看。此外，Spindle 是 Tapestry 开发的一个非常方便和有用的工具，目前只适用于 Tapestry 3。

我们现在要做的事情可能看起来并不令人兴奋，但我们只需要做一次。以下一些说明是特定于平台的，请选择适合您的说明。

##### 安装 JDK

如果您曾经尝试过 Java 开发，那么您的计算机上已经安装了 JDK。它预装了最新版本的 Mac OS X。要验证您是否拥有它，请尝试以下操作。

在 Windows 中，选择开始>运行…，然后键入`cmd`并按下确定按钮。在 Linux 或 Mac OS X，只需打开一个终端窗口。

在提示符下输入以下命令:

```
javac
```

按回车键。如果您的计算机安装并配置了 JDK，您将看到十几行输出，以以下内容开始:

```
Usage: javac <options> <source files> 

where possible options include: 

  -g                        Generate all debugging info 

... more lines of output ...
```

如果您的电脑无法识别该命令，您需要安装 JDK。你可以安装最新版本的 JDK 5.0，也可以安装之前的 JDK 1.4.2。对于本教程的目的来说，两者都很好。让我们假设您将安装最新的版本。

***1。下载 JDK***

对于 Windows 和 Linux: [下载最新版本的 JDK 5.0](http://java.sun.com/j2se/1.5.0/download.jsp) (在撰写本文时，是 JDK 5.0 更新 6)。请注意:你将需要 JDK，而不是 JRE！

给 Linux 用户的一些建议:无论您使用哪个发行版，您都可能希望下载 Linux 自解压文件(`jdk-1_5_0_06-linux-i586.bin`)，而不是 Linux RPM 自解压文件(`jdk-1_5_0_06-linux-i586-rpm.bin`)。在某些情况下，前者更容易安装和配置。

对于 Mac OS X:你可能不需要下载任何东西，因为你的电脑上可能已经安装了类似 JDK 1.4.2_09 的软件。如果你想要新版的 JDK，[在这里下载](http://www.apple.com/support/downloads/java2se50release3.html)。然而，需要注意的是，在 Mac OS X 中更改 JDK 的默认版本并不是一件简单的事情，所以对于本教程来说，你可能需要坚持使用预装的 JDK 1.4.2。

为了更好地理解在 Mac OS X 上运行 Java 5 的问题，请参考 Javaranch.com 论坛上的讨论。

***2。安装 JDK***

对于 Windows 和 Mac OS X 版本，你没有太多选择；只需启动您下载的可执行文件，并按照说明进行操作。

在 Linux 中，您需要更改下载文件的权限，以便能够执行它:

```
chmod +x jdk-1_5_0_06-linux-i586-rpm.bin
```

然后，执行它:

```
./jdk-1_5_0_06-linux-i586-rpm.bin
```

一旦您同意显示给您的条款和条件，文件将自行解压缩。将结果目录`jdk1.5.0_06`移动到您希望它存在的位置(例如移动到`/usr/java`)。

现在，您需要将 bin 子目录(比如，`/usr/java/jdk1.5.0_06/bin`)添加到您的机器的路径中。在 Linux 下实现这一点的一种方法是将下面两行添加到您的`.bashrc`文件中(当然，如果您使用 bash 的话):

```
PATH=/usr/java/jdk1.5.0_06/bin:$PATH 

export PATH
```

就是这样！如果您正确遵循了这些说明，您应该已经安装了 JDK。

##### 安装 Tomcat

严格地说，Tomcat 不仅仅是一个 web 服务器——它被称为 servlet 容器，充当 Web 世界和 Java 世界之间的网关。但是它也有一个不错的 web 服务器，至少是为了开发的目的。安装 Tomcat 非常容易，但是我们确实需要在之后配置一个环境变量。

***1。下载雄猫***

这个步骤对于所有平台都是通用的，因为 Tomcat 是一个 Java 程序！去 http://tomcat.apache.org。如果你有 JDK 5，选择 Tomcat 的最新稳定版本(在撰写本文时，这是 5.5.12)，否则下载 Tomcat 5 . 0 . 28——对于我们的目的，它将完全一样。

***2。打开公猫*的包装**

将下载的包解压到您选择的目录中。这可能是 Windows 电脑上的`c:apache-tomcat-5.5.12`,类似于 Linux 下的`/home/alex/apache-tomcat-5.5.12`,或者 Mac OS X 下的`/Users/alex/jakarta-tomcat-5.5.12`, Windows 的安装向导使得这个过程更加容易。

***3。设置 JAVA_HOME 环境变量***

这个环境变量应该指向放置 JDK 的目录。因为我们现在处理的是计算机的操作系统，所以下面的指令是特定于平台的。

**Windows XP**

您的 JDK 可能是自动安装的，但要找到它的安装位置并不困难。它很可能位于`C:Program FilesJava`目录中，因此您的环境变量应该指向如下内容:

```
C:Program FilesJavajdk1.5.0_06
```

要设置变量，右键单击我的电脑并选择属性。在打开的对话框中，选择“高级”选项卡，然后点按底部的“环境变量”按钮。

系统变量列在窗口的下部(见图 1)。如果你没有看到`JAVA_HOME`，按下新建按钮创建它，否则按下编辑按钮编辑它。

![1508_figure1](img/1c8fa80ff873049a4a513037ffc4f50d.png)
*图 1 .在 Windows XP 中设置 JAVA _ HOME*

**Linux**

如果你按照上面的描述安装了 JDK，你会知道它在哪里。否则，您可能需要使用以下命令找到它:

```
which java
```

这将显示类似如下的内容:

```
/usr/java/jdk1.5.0_06/bin/java
```

因此，您的`JAVA_HOME`应该指向`/usr/java/jdk1.5.0_06`。

在 Linux 中，有几个地方可以设置环境变量。我的首选是编辑。bashrc 文件，添加以下行:

```
export JAVA_HOME=/usr/java/jdk1.5.0_06
```

麦克·OS X

Mac OSX 进程与我们用于 Linux 的进程基本相同，不同之处在于，在 Mac 上找到 JDK 的安装位置更加困难。要找到答案，请键入:

```
which java
```

这将为您提供如下内容:

```
/usr/bin/java
```

这种情况下，你的`JAVA_HOME`应该指向`/usr`。

要设置变量，编辑您的`.bash_profile`文件并添加以下行:

```
export JAVA_HOME=/usr
```

***4。测试一下！***

要测试您安装的 Tomcat 是否真的工作，请转到 Tomcat 安装目录的 bin 子目录。换句话说，如果你已经将 Tomcat 安装到了 **c:apache-tomcat-5.5.12** 中，那么进入**c:Apache-Tomcat-5 . 5 . 12 bin**。

在那里，你会发现以下脚本:`startup.sh`和`shutdown.sh`(用于 Linux 和 MacOS X)以及`startup.bat`和`shutdown.bat`(用于 Windows)。

请注意，在 Linux 或 Mac OS X 机器上，您可能需要更改该目录中所有`.sh`脚本的权限，以便能够执行它们，但这很简单:

```
chmod +x *.sh
```

要启动 Tomcat，请启动`startup.sh`或`startup.bat`(取决于您的平台)，等待几秒钟，然后将您的 web 浏览器导航到`http://localhost:8080`。如果一切运行正常，您将看到类似于图 2 的页面

![1508_figure2](img/45f055278ed1ee44cc36d0b1d6a7e170.png)
*图 2 .如果你看到这个页面，你的 Tomcat 安装成功了。*

在网络世界中，localhost 指的是您自己的计算机，当它被称为服务器时。当你导航到`localhost:8080`时，你实际上是在问你的计算机是否有人在监听端口 8080(你计算机上的端口就像你办公室里的电话分机)。默认情况下，这是 Tomcat 正在监听的地方，所以它露出了脸。

要在不再需要 Tomcat 时停止它，可以使用`shutdown.sh`或`shutdown.bat`脚本。如果你使用的是 Windows Tomcat 安装程序，你也会在任务栏中有一个方便的小监控工具。您可以使用它来启动和停止服务器。

##### 安装 Eclipse

一个高效便捷的 IDE 极大地提高了开发人员的生产力，当涉及 Tapestry 开发时，IDE 的自然选择是 Eclipse，这主要是因为我们将要使用的 Spindle 插件只适用于 Eclipse，当然，还因为 Eclipse 是一个优秀的 IDE！
安装 Eclipse 非常简单。进入[Eclipse 下载页面](http://eclipse.org/downloads)，下载适合你的版本，比如 Windows 的`eclipse-SDK-3.1.1-win32.zip`，Mac OS X 的`eclipse-SDK-3.1.1-macosx-carbon.tar.gz`，或者 Linux 的`eclipse-SDK-3.1.1-linux-gtk.tar.gz`。将下载文件解压到您选择的目录，例如`c:eclipse`或`/Users/alex/eclipse`。

您可以通过在 Windows 中启动 Mac OS X、Eclipse.app 中的 Eclipse.app，或者在 Linux 中键入 Eclipse 来启动 IDE。当您第一次启动 Eclipse 时，会要求您选择您的工作区——存储所有 Eclipse 项目的目录。您可以选择默认目录，也可以选择不同的目录。

就是这样！现在我们已经有了 JDK、Tomcat 和 Eclipse，所有这些都是一般 Java Web 开发的先决条件。现在，我们要用一些 Tapestry 特有的组件来丰富我们的工作环境。

##### 安装主轴

这个 Eclipse 插件极大地简化了 Tapestry 开发，并且非常容易安装。

打开您的 Eclipse IDE。选取“帮助”>“软件更新”>“查找并安装”…

![1508_figure3](img/4fba97edcb110adee811dcbc4892c604.png)
*图三。选择安装主轴*

在出现的对话框中，选择搜索要安装的新功能，然后按下一步。在下一个窗口中，按下新建远程站点…按钮。输入 Spindle 作为名称，输入`http://spindle.sf.net/updates`作为 URL，然后按 OK 并完成。系统将提示您选择镜像；选择离您最近的位置，然后按 OK。

Eclipse 将搜索插件并向您显示结果，如上面的图 3 所示。选择主轴，然后按下一步。

在下一个对话框中，接受许可协议中的条款，然后按下一步。最后，按完成。Eclipse 将下载安装 Spindle 所需的所有内容(这可能需要半个小时或更长时间，取决于您的连接)。然后，它会要求你确认你真的要安装“一个未签名的功能”(见图 4)。

选择 Install All，当安装完成时，同意重新启动您的工作台。

太好了！现在，您的 IDE 已经为 Tapestry 开发做好了准备。我们需要采取的最后一步是下载 Tomcat 运行 Tapestry Web 应用程序所需的所有库，并将它们放在适当的位置。

![1508_figure4](img/61e5859ec856d5b2c83f994194f0633c.png)
*图 4。安装主轴:验证阶段。*

##### 下载 Tapestry 和其他库

Tapestry 3 本身有两个版本。jar 文件:`tapestry-3.0.3.jar`和`tapestry-contrib-3.0.3.jar`。但是它也依赖于其他库，其中一些包含在它的发行版中，而其他的需要单独下载。获得所有必要库的最简单方法是[下载我为你创建的`tapestry_libs.zip`档案](https://www.sitepoint.com/examples/tapestry/tapestrylibs.zip)。
将这个归档文件的所有内容解压到 Tomcat 安装下的`shared/lib`目录中。比如说，它可以是 Windows 机器上的`c:apache-tomcat-5.5.12sharedlib`或者 Mac OS X 上的`/Users/alex/jakarta-tomcat-5.5.12/shared/lib`

咻，我们成功了！我们现在准备释放 Tapestry 的魔力。

##### 我们的第一个 Tapestry Web 应用程序

首先，让我们想象一下我们将要创造什么。我们的第一个 Tapestry 应用程序将非常简单——尽管仍然有用——并且有两个页面。第一页将显示一个登录表单:只是一个要求输入用户名和密码标准表单。第二个页面将使用访问者的登录名来欢迎他们——也就是说，如果他们成功地通过了身份验证。如果登录/密码组合错误，登录页面将再次重新显示。

图 5 显示了两个页面完成后的样子。为了简单起见，我在这里没有使用任何样式，但是你可以运用你的创造力来创造一个你自己的设计。

![1508_figure5a](img/6da88eb3d47bd8883d2875522be1e864.png)

![1508_figure5b](img/c736f94f7fa9379687d1470ad1d86fad.png)
*图五。这是我们的应用程序完成后的页面外观。*

事实上，通过首先创建 HTML“模型”来开始 Tapestry 应用程序的开发是有意义的，HTML“模型”是您未来页面的原型，它确切地显示了它们在工作应用程序中的外观。

创建模型时，你可以充分发挥你的所有创造技能，或者，如果你是一个优秀设计团队的经理或客户，告诉你的设计师不要限制他们的想象力。你不能通过插入难看的标签或者将精心制作的设计与难以理解的 HTML 和代码混合来破坏他们的努力。您很快就会看到，Tapestry 非常关注页面设计。

以下是我的页面模型的 HTML 代码:

第 1 页:
`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"  
   "https://www.w3.org/TR/html4/strict.dtd">  
<html>  
<head>  
 <title>Welcome to Tapestry!</title>  
</head>  
<body>  
 <h2>Welcome to Tapestry!</h2>  
 <form action="">  
   <p>User Name: <input type="text"/><br/>  
       Password: <input type="password"/><br/>  
       <input type="submit" value="Let me in!"/></p>  
 </form>  
</body>  
</html>`

第 2 页:
`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"  
   "https://www.w3.org/TR/html4/strict.dtd">  
<head>  
 <title>You've got it!</title>  
</head>  
<body>  
 <h2>Precious User!</h2>  
 <h3>Welcome to the wonderful World of Tapestry!</h3>  
</body>  
</html>`

现在我们已经准备好了模型，让我们创建我们的第一个 Tapestry 项目。

##### 创建 Tapestry 项目

启动 Eclipse(但还不是 Tomcat)。要创建一个新的 Tapestry 项目，选择 File > New > Other…,在 Tapestry 节点下，选择 Tapestry Web project，如图 6 所示。然后按下一步。

![1508_figure6](img/6cfc2d97898458382aa675b4384a7a8c.png)
*图六。选择 Tapestry Web 项目*

将项目命名为“Login”，并选中在 web.xml 中包含 Tapestry 重定向过滤器的复选框(参见图 7)。

![1508_figure7](img/36abc6021fecfd5d97d895572ca31b23.png)
*图 7。命名项目*

点击下一步，在下一个窗口中点击完成。您将看到新应用程序的结构，如图 8 所示

##### Java Web 应用程序的一般结构

![1508_figure8](img/b9502d1601b38701991eb3872278976f.png)
*图 8。新 Tapestry 项目的结构*

如果您没有任何 Java web 开发经验，那么对 Java web 应用程序标准结构的简要概述可能会对您有所帮助。这种结构对于所有基于 servlets 的框架都是通用的，包括 JSP、Struts、Tapestry 以及 Java web 世界中几乎所有的东西。

这种结构的简化形式如图 9 所示。整个应用程序包含在一个目录中，该目录以应用程序的名称命名。我们在图中标记了这个顶级目录`AppName`。

![1508_figure9](img/5a3c072b27e1dfe9652d0999b48194f2.png)
*图九。Java Web 应用程序的通用结构*

在这些资源的正下方，外界可以通过网络直接访问这些资源。这包括 HTML 页面、JSP 页面、CSS 文件、图像和应用程序外部可能需要的任何其他资源。我使用“外部”这个术语是因为如果你有一个名为`MyPage.html`的页面，它直接存储在`www.myserver.com`服务器上的`AppName`目录下，网络用户可以通过将浏览器指向`http://www.myserver.com/AppName/MyPage.html`来查看该页面。

图像和样式表通常放在一个子目录中——在本例中是一个名为 assets 的子目录。像这样将内容分组有助于保持整洁；每个人都可以访问这个子目录。

您会注意到一个名为`WEB-INF`的子目录(特别重要)。它应该像这样命名——而不是`web-inf`或`Web-Inf`——否则你的 Web 应用程序将无法工作。这是 Java Web 应用程序的内部密室:这个目录中的任何内容都不会直接暴露给外部世界(即 Web)。

在`WEB-INF`里面有一个名为`web.xml`的重要文件。它在所有 Java web 应用程序中都有相同的名称，是部署描述符。这个文件的目的是告诉 Tomcat 一些关于应用程序的一般的、重要的信息。

`WEB-INF`下还有两个子目录，分别命名为 classes 和 lib。组成应用程序的所有编译后的 Java 类都放在 classes 子目录中，应用程序使用的所有库都放在 lib 目录中——很简单！许多其他服务于不同目的的文件可以保存在`WEB-INF`下及其子目录中，但这是标准结构。

现在，让我们看看这种安排如何映射到我们的第一个 Tapestry 应用程序的结构，这个应用程序是由 Eclipse(和 Spindle 一起)为我们创建的。

##### 我们的 Web 应用程序的结构

您可以看到我们的应用程序的顶级目录名为 Login，这是我们给应用程序起的名字。

为了方便起见，创建了`src`子目录。这是我们所有 Java 类的源代码将被存储的地方。在它下面，你会看到两个库参考:Tapestry Framework 和 JRE System Library。Eclipse 在这里显示它们是为了表明我们可以在这个项目中使用它们。还有一个上下文子目录。我们应用程序的所有文件——图 9 中直接列在`AppName`下的所有文件——都存储在上下文子目录中。我们现在没有任何静态 HTML 页面，但以后可能会有。如果有的话，我们还会在这里保存任何图像或样式表。现在，我们看到的只是神圣的`WEB-INF`文件。

在`WEB-INF`中，您将看到部署描述符`web.xml`。不过，我们现在还不去探索它。

我们没有创建任何 Java 类，也没有使用任何库，所以还没有显示任何类或`lib`子目录。

还有一个包含应用程序级配置的`Login.application`文件。现在，我们将让它保持原样，但是稍后，当我们对它进行一些更改时，我们将讨论这个文件的内容。

现在我们来看这个过程中最有趣的部分:创建 Tapestry 页面。

##### 创建主页

您会注意到，Spindle 为我们又创建了两个文件:`Home.html`和`Home.page`。名为 Home 的页面是 Tapestry 应用程序的默认页面，非常类似于`index.html`是网站目录的默认页面。如果客户机的请求中没有指定页面，Tapestry 将显示主页。

我们将把我们的登录表单放在这个页面上。让我们看看 HTML Spindle 为我们创造了什么。双击`Home.html`文件，在编辑器中打开它:

```
<html>  

<head>  

</head>  

<body>  

</body>  

</html>
```

这只是一个 HTML 页面的空存根。让我们用第一页的模型来替换它。我们将在正文中添加一个 doctype、一个标题和一个简单的表单。

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"  

    "https://www.w3.org/TR/html4/strict.dtd">  

<html>  

<head>  

  <title>Welcome to Tapestry!</title>  

</head>  

<body>  

  <h2>Welcome to Tapestry!</h2>  

  <form action="">  

    <p>User Name: <input type="text"/><br/>  

        Password: <input type="password"/><br/>  

        <input type="submit" value="Let me in!"/></p>  

  </form>  

</body>  

</html>
```

假设我们刚刚从我们的设计团队收到这个精心制作的 HTML 页面。是时候应用我们的 Tapestry 知识，将模型转换成真实的东西了——Tapestry 页面的 HTML 模板！这种转换的最终结果如下:

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"  

    "https://www.w3.org/TR/html4/strict.dtd">  

<html>  

<head>  

  <title>Welcome to Tapestry!</title>  

</head>  

<body>  

  <h2>Welcome to Tapestry!</h2>  

  <form action="" jwcid="loginForm">  

    <p>User Name: <input type="text" jwcid="uname" /><br/>  

        Password: <input type="password" jwcid="password" /><br/>  

      <input type="submit" value="Let me in!"/></p>  

  </form>  

</body>  

</html>
```

当然，您的鹰眼已经发现了我们所做的更改，但是您的 web 浏览器会注意到它们吗？在您选择的浏览器中打开原始模型，然后打开转换后的版本，检查是否有任何差异。没有。因为从渲染的角度来看，那些奇怪的`jwcid`属性对 web 浏览器来说毫无意义，所以它会忽略它们。

您可能有兴趣知道 jwcid 的意思是“Java Web 组件 id”，这些 jwcid 属性是我们告诉 Tapestry“我们想在这里应用一些魔法”的方式。请将这些标准 HTML 元素转换成动态 Tapestry 组件。”结果，标准的 HTML 元素`<form>`将成为 Tapestry 组件，两个标准的`<input>`元素也是如此。

顺便说一下，分配给 jwcid 属性的名称或多或少是任意的。如果你愿意，你可以给它们起名叫`jwcid="bilbo"`或者`jwcid="FrodoBaggins"`。但是不要尝试像`jwcid="%@^&*"`这样的东西，魔多的语言在这里是行不通的。我建议您使用一些指示文件所代表的数据类型的东西。

![1508_figure10](img/ce6ce3696d72b14d0a9adc38785bf3e5.png)
*图 10。主轴发现错误*

好吧，那么如果组件 id 是任意的，Tapestry 如何确切地知道您想要使用哪个标准或定制组件来代替静态 HTML 呢？您可能会注意到，Spindle 对我们的新属性不太满意——它在第一个属性下面加了一条红线，并在编辑器窗口的左边界画了一个红色的十字圈(参见图 10)。如果您将光标悬停在带下划线的单词或红色圆圈上，您会看到以下错误消息:

```
Tag <form> on line 7 references unknown component id 'loginForm'.
```

从逻辑上讲，应该有一个地方可以为 Tapestry 指定我们设计的细节，而且确实有！这个地方被称为页面规范，对于我们的主页，它由已经由 Spindle 为我们创建的`Home.page`文件表示。

现在是讨论以下启示的时候了:Tapestry 页面实际上由三部分组成:

1.  一个 HTML 模板(例如，我们的第一页的 Home.html)

3.  一个页面规范(比如`Home.page`)

5.  一个页面类(我们称我们的为`Home.class`——稍后见)

HTML 模板定义了我们的用户将会看到什么。page 类包含页面的功能。页面规范将模板中的组件与页面类中的相应功能链接起来。它指定了这些组件的所有必要细节，还可能包含一些其他特定于页面的信息。

稍后我们将返回到 Tapestry 页面的三部分性质——而且不止一次——因为它非常重要。但是现在，让我们为主页创建页面规范。在 Eclipse 的包浏览器中双击`Home.page`文件。这是你应该看到的:

```
<?xml version="1.0" encoding="UTF-8"?>  

<!DOCTYPE page-specification PUBLIC  

  "-//Apache Software Foundation//Tapestry Specification 3.0//EN"  

  "http://jakarta.apache.org/tapestry/dtd/Tapestry_3_0.dtd">  

<!-- generated by Spindle, http://spindle.sourceforge.net -->  

<page-specification class="org.apache.tapestry.html.BasePage">  

  <description>add a description</description>  

</page-specification>
```

如果您对 XML 文档和 doctypes 没有太多的经验，不要太关注前五行。第一行只说明这是一个 XML 文档。看起来神秘的`<!DOCTYPE>`标签告诉 XML 解析器如何理解后面的文档。

我们对页面规范元素中的信息感兴趣，从属性`class="org.apache.tapestry.html.BasePage"`开始。

还记得我告诉过您，每个 Tapestry 页面都由三部分组成，其中一部分是包含页面功能的 Java 类吗？即使你不创建一个类，一个默认的类还是存在的——这个属性引用的`BasePage`类。当然，它不包含任何定制功能，但是它仍然有许多默认方法，让我们可以访问 Tapestry 的基础设施。

当我们决定创建我们的自定义页面类时，它总是会扩展这个默认的`BasePage`类。

接下来是描述元素。你可以让它保持原样，或者如果你像我一样，喜欢让你的东西井井有条，继续添加页面的描述——类似于“登录页面”。

在页面规范元素内部，我们必须向 Tapestry 解释我们写`jwcid="uname"`等的真正意思。

在这里，我将向您展示我们将要为我们的页面规范编写什么，并在我们将它键入 Eclipse 之前解释它的含义。一会儿您将看到我们如何使用 Spindle 和 Eclipse 的一些便利特性来快速编写所有这些规范。

首先，我们将描述我们的`loginForm`组件。它的规格如下所示:

```
<component id="loginForm" type="Form">  

  <binding name="listener" expression="listeners.onFormSubmit" />  

</component>
```

您可能已经推断出这段 XML 告诉 Tapestry:“我插入到 HTML 模板中的`jwcid="loginForm"`将是一个标准的 Tapestry 表单组件。”

binding 元素实际上在 HTML 模板和 page 类之间创建了一个链接。当绑定的名称是 listener(还有其他一些名称可用)时，我们描述的是提交表单时将调用的页面类上的方法的名称。

Tapestry 表单的侦听器是这样指定的:

```
<binding name="listener" expression="listeners.onFormSubmit"/>
```

该元素中唯一可以更改的部分是实际的监听器方法名，在本例中是`onFormSubmit`。您可以控制这个方法的名称，所以您可以称它为`onSubmit`，或者如果您愿意，对您来说更有意义的名称。现在让我们继续关注`onFormSubmit`;因此，Tapestry 将在 page 类中查找具有以下签名的方法:

```
public void onFormSubmit(IRequestCycle cycle) {}
```

提供这样一个方法的实现是你的责任。如果您决定将监听器命名为`myGloriousMethod`，确保您提供了一个带有签名的 Tapestry 方法:

```
public void myGloriousMethod(IRequestCycle cycle) {}.
```

哪里可以提供这样的自定义侦听器方法？在一个自定义页面类中(我们稍后会谈到)。现在也不要为那件事费心——我们一会儿就会谈到那件事。现在，让我们告诉 Tapestry 如何处理我们尚未指定的另外两个组件。

第一个是`uname`，用于存储用户名。应该这样指定它:

```
<component id="uname" type="TextField">  

  <binding name="value" expression="uname"/>      

</component>
```

这里，我们告诉 Tapestry，我们在 HTML 模板中标记为`jwcid="uname"`的东西实际上是一个标准的 Tapestry TextField 组件，从它那里获得的值应该存储在一个属性 uname 中。同样，这将是我们的自定义页面类的一个属性，但是我们将在后面讨论它。实际上，您可以随意命名这个属性，尽管将属性命名为与组件的 ID 相同是方便的(也是一种好的做法)。

我们主页上的最后一个组件是密码。应该这样指定它:

```
<component id="password" type="TextField">  

  <binding name="value" expression="password"/>  

  <static-binding name="hidden" value="true"/>      

</component>
```

这告诉 Tapestry，我们标记为 password 的组件也是一个 TextField，它的值应该存储在一个名为 password 的属性中，它的 hidden 参数应该设置为 true。

因为 Tapestry 没有特殊的密码组件，所以用星号替换击键的效果是通过将普通 TextField 组件的 hidden 属性设置为 true 来实现的，这样字符就不会回显到屏幕上。

`<binding>`和`<static-binding>`的区别在于`<binding>`在每次表单呈现或提交时都会被 Tapestry 评估，这对于不可预测或可变的值(谁知道用户会输入什么密码？).另一方面，`<static-binding>`适用于常量值:我们希望隐藏的参数始终为真；这个会被 Tapestry 记住，不会每次都评估 hidden，节省了宝贵的资源。

现在让我们编辑我们的`Home.page`文件，并向其中添加我们刚刚讨论过的组件规范。但是手工编写 XML 既枯燥又容易出错，所以我们在这里要依靠 Spindle 的帮助。

将光标放在`<description>`标签下方的行中，然后按 Ctrl + Space。在打开的代码辅助窗口中，选择 component(参见图 11)。

![1508_figure11](img/3dc0da8f78969b152f738f010882968d.png)
*图 11。借助代码辅助编写组件规范*

按回车键，您将得到一个组件规范的存根:

```
<component id="value"></component>
```

请注意，已经为您选择了 value，因此您可以输入一些内容来立即替换它。输入`loginForm`。按 Tab 键，光标将跳到下一个输入位置。如果您经常在浏览器和 Eclipse 窗口之间切换，这个特性可能不起作用，所以您可能希望打印本教程或安排您的工作区(或者，如果可能的话，记住这几个步骤)，以便可以一次完成这个过程。再次按 Ctrl + Space，从列表中选择类型，然后按 Enter。您的新规范应该是这样的:

```
<component id="loginForm" type="value"></component>
```

再次选择单词值。再次按下 Ctrl + Space 并键入字母“f”。您将看到以 f 开头的组件名称。选择 Form 并按 Enter 键。这就是你现在将看到的:

```
<component id="loginForm" type="Form"></component>
```

再次按 Tab 跳转到下一个入口点，就在`<component ...>`和`</component>`标签之间，并且——你猜对了——按 Ctrl + Space。从列表中选择绑定(空),然后按 Enter 键。在这一阶段，您的规范将如下所示(我又按了几次 Enter 键来很好地格式化代码；你可能也想这样做):

```
<component id="loginForm" type="Form">   

  <binding name="value"/>   

</component>
```

选中单词值后，按 Ctrl + Space。选择侦听器，按 Enter，然后再次按 Tab 和 Ctrl + Space。列表中的唯一选择是表达式，所以通过按 Enter 来选择它。最后输入`listeners.onFormSubmit`而不是 value。就是这样！我们已经完成了第一个组件的规格。

在您习惯使用代码辅助之后，您将会看到编写组件规范是多么容易。

现在继续练习吧。为其他两个组件创建规范，并在完成后查看这里。多次 Ctrl + Space 击键的最终结果应该如下所示:

```
<?xml version="1.0" encoding="UTF-8"?>   

<!DOCTYPE page-specification PUBLIC   

  "-//Apache Software Foundation//Tapestry Specification 3.0//EN"   

  "http://jakarta.apache.org/tapestry/dtd/Tapestry_3_0.dtd">   

<!-- generated by Spindle, http://spindle.sourceforge.net -->   

<page-specification class="org.apache.tapestry.html.BasePage">   

  <description>Login page</description>   

  <component id="loginForm" type="Form">   

    <binding name="listener"   

        expression="listeners.onFormSubmit"/>       

  </component>   

  <component id="uname" type="TextField">   

    <binding name="value" expression="uname"/>       

  </component>   

  <component id="password" type="TextField">   

    <binding name="value" expression="password"/>   

    <static-binding name="hidden" value="true"/>       

  </component>   

</page-specification>
```

我们已经完成了主页的页面规范，Spindle 语法检查器应该对我们所写的内容非常满意(也就是说，`Home.html`和`Home.page`中不应该有红色波浪线)。但是，页面还不行。记住，我们向 Tapestry 承诺，在 page 类中会有一个`onFormSubmit`监听器和两个名为 uname 和 password 的属性。

现在，这个页面使用默认的`BasePage`类，它没有任何定制的方法或属性。为了实现我们的定制逻辑，我们应该创建自己的 Java 类，它将继承自`BasePage`。

##### 创建页面类

在包资源管理器中右键单击(或者在 Mac 上按住 Ctrl)并选择 New > Other…，然后在 Select a wizard 对话框中选择 Class(参见图 12)。按下一步。

![1508_figure12](img/8879263a2943ae5095d09d8cbecd55e9.png)
*图 12。选择课程向导*

在 Java 类对话框中输入一个包名，例如`com.sitepoint.tapestry.login`。输入`Home`作为新类的名称，输入`org.apache.tapestry.html.BasePage`作为超类。确保取消选中 public static void main(String[]args)复选框—我们不会将该类作为独立程序运行。您可能会发现添加自动生成的注释很有用，因此选择“生成注释”复选框。最终结果应该如图 13 所示。

![1508_figure13](img/e778f7c7fa5988721f37a1a50be857c2.png)
*图十三。准备创建一个新类*

按 Finish，几秒钟后就会为您生成一个全新的页面类。它将被命名为`Home.java`，其代码如下所示:

```
/**   

 *    

 */   

package com.sitepoint.tapestry.login;   

import org.apache.tapestry.html.BasePage;   

/**   

 * @author alex   

 *   

 */   

public class Home extends BasePage {   

}
```

这是我们定义监听器方法的地方。像这样指定:

```
public class Home extends BasePage {   

  public void onFormSubmit(IRequestCycle cycle) {   

    // Some code will go here   

  }   

}
```

在这个阶段，我们将只创建方法的框架——其余的代码将在以后添加。神秘的`IRequestCycle`参数可以被认为是 Tapestry 在我们代码中的代表。每当我们需要框架中的某些东西时，我们会请求这个循环为我们获取它。我们很快就会看到这样的例子。

然而，此刻 Eclipse 告诉我们它不知道什么是`IRequestCycle`。为了让它更好，我们需要添加下面的导入语句:

```
import org.apache.tapestry.IRequestCycle;
```

现在我们有了一个定制的页面类，我们需要在我们的页面规范中指定它。将`Home.page`中的开始`<page-specification>`标记改为如下所示:

```
<page-specification class="com.sitepoint.tapestry.login.Home">
```

##### 用 Tapestry 方式指定属性

现在是处理两个属性 uname 和 password 的时候了。在 Tapestry 中有两种指定属性的方式:标准的 Java 方式(私有类成员、公共 getter 和 setter，可能还有一些其他的维护代码)和 Tapestry 方式。后者虽然看起来有点不寻常，但使用得更频繁，所以我们在这里将使用 Tapestry 方法。

我们不想手工编写所有与属性相关的代码，所以我们只需要让 Tapestry 在运行时自动为我们生成代码。我们需要做的就是描述属性应该如何命名，以及它们应该是什么类型(Tapestry 可用的任何 Java 类都可以作为类型)。

您可能已经猜到，我们将在页面规范中描述我们想要的属性。在`Home.page`类中，就在`<description>`标签的下面，添加下面两行(当然，你不必全部手写出来——使用你的代码辅助 Ctrl + Space 技能):

```
<property-specification name="uname" type="java.lang.String"/>   

<property-specification name="password" type="java.lang.String"/>
```

就是这样！现在您可以假设在运行时这些属性已经准备好了，Tapestry 会正确地管理它们，不需要您做任何工作，也不需要您编写任何代码。

以前，在我们指定这些属性之前，我们已经将它们连接到我们的两个组件:一个 ID 为`uname`的 TextField 组件绑定到属性 uname，一个 ID 为`password`的 TextField 组件绑定到 password 属性。让我描述一下 Tapestry 将根据我们的页面规范执行的所有活动:

1.  当主页被请求时，Tapestry 会为它创建 page 类(一个`com.sitepoint.tapestry.login.Home`的实例，由我们创建)。然后它会注意到我们请求向这个页面添加两个属性，所以 Tapestry 将创建我们的类的“增强”版本(即从 Home 继承的另一个类)，并将添加两个请求的属性以及处理它们可能需要的所有代码。
2.  Tapestry 然后要求增强的页面类实例将自己呈现给用户。为此，页面类实例将使用我们的 Home.html 模板。当呈现 uname 和 password 组件时，类实例将检查这些组件是否有任何默认值。我们没有指定任何内容，所以组件将显示为空白文本字段。
3.  当提交登录表单时，Tapestry(使用相同类型的 page 类实例，要么重新创建，要么从池中获取)将把用户输入的值放入两个文本字段，并把它们放入相应的属性中，这样我们就可以在代码中使用它们。

我用了三个段落来描述 Tapestry 会自动为我们做什么，因为页面规范中只有几行代码。当您开始使用 Tapestry 时，并不需要了解所有这些过程。您只需要知道，您可以指定您需要的属性，将它们连接到您的页面上使用的组件，因此，用户提交的任何内容都将通过这些属性在您的代码中可用。

唯一的细微差别是，要在代码中使用或编辑这种属性值，应该在 page 类中创建一个 getter 或 setter(或者两者都创建，如果需要的话)。不可否认，这有点不寻常:您没有在 Java 代码中创建任何属性，当然也没有编写类似这样的东西:

```
private String uname;   

private String password;
```

但是我们要求 Tapestry 在运行时提供它们，所以我们只需要相信这些属性就在那里。所有这些都有点…抽象。是的，我们将为这些阴影属性创建抽象的 getters。他们在这里:

```
public abstract String getUname();   

public abstract String getPassword();
```

我们将把这些 getters 添加到我们的 page 类中，因为它有抽象方法，所以这个类也将成为抽象的！这就是它的样子(为了简洁起见，去掉了注释):

```
package com.sitepoint.tapestry.login;   

import org.apache.tapestry.html.BasePage;   

import org.apache.tapestry.IRequestCycle;   

public abstract class Home extends BasePage {   

  public void onFormSubmit(IRequestCycle cycle) {   

    // Some code using getUname() and getPassword will go here   

  }   

  public abstract String getUname();   

  public abstract String getPassword();   

}
```

在应用程序中使用抽象类作为“最终产品”看起来有些奇怪，但是一旦你习惯了，你就会意识到这实际上是非常方便的:你写下你所需要的，Tapestry 会接管并巧妙地为你创建所有的“管道”。

顺便说一下，不仅在 Tapestry 中可以看到这种方法，在具有容器管理持久性的 EJB 中也可以看到。

对，我们已经写了足够多的东西来测试我们的第一个 Tapestry 页面。此时它不会做任何可见的事情，但至少我们可以检查 Tapestry 是否正确理解了我们的代码。如果一切按计划进行，我们将看到一个网页，看起来与我们的主页样机相同。

##### 通知 Tomcat 我们的 Web 应用程序

在第一次运行 Tapestry Web 应用程序之前，我们需要通知 Tomcat 我们已经创建了这个应用程序，以及在哪里可以找到它。为此，我们将创建一个非常简单的 XML 文件，一种针对 Tomcat 的注释。我们就叫它`Login.xml`。您可以使用任何文本编辑器来创建此文件；它不会成为我们 Web 应用程序的一部分。

这就是我们需要写的:

```
<Context docBase="/Users/alex/Documents/workspace/Login/context"    

    path="/Login"/>
```

在`docBase`属性中，您必须指定 Web 应用程序的上下文子目录的路径。这将取决于您在安装 Eclipse 时选择放置工作区目录的位置。在本例中，您可以在我的 Mac 上看到该目录的位置。在 Linux 机器上它将是类似的东西。

对于 Windows，请注意，在指定上下文的路径时，应该使用正斜杠，而不是 Windows 路径中通常使用的反斜杠。换句话说，你应该写`docBase="c:/workspace/Login/context"`，而不是`docBase="c:workspaceLogincontext"`。

将这个`Login.xml`文件放到 Tomcat 目录下的`conf/Catalina/localhost`子目录中(例如:`c:apache-tomcat-5.5.12confCatalinalocalhost`或`/Users/alex/jakarta-tomcat-5.0.28/conf/Catalina/localhost`)。

我们现在准备运行 Tapestry Web 应用程序。

##### 运行应用程序

按照“安装 Tomcat”一节中的说明启动 Tomcat 服务器。如果 Tomcat 已经在运行，将其关闭并重新启动——这将使 Tomcat 有机会在启动时读取我们刚刚创建的新配置文件。

将浏览器指向`http://localhost:8080/Login/app`。如果一切正常，您应该会看到如图 5 所示的登录页面——与预期的完全一样。相反，如果您得到一个错误，请返回并仔细检查是否遵循了每个步骤，然后重新启动 Tomcat 服务器并重试。

您已经知道对`http://localhost:8080`的请求将调用运行在您计算机上的 Tomcat。`/Login`告诉 Tomcat 我们希望我们的请求发送到哪个 Web 应用程序。记住，在上一节配置 Tomcat 时，我们使用`path="/Login"`告诉它应用程序的路径。

我们请求的`/app`部分告诉我们的应用程序这是对 Tapestry 部分的请求。默认情况下，它被配置成所有发送给 Tapestry 的请求都必须以`/app`结尾。这是针对我们的应用程序包含其他非 Tapestry 资源的情况——例如静态 HTML 页面。在本教程的下一部分，我将向您展示如何更改配置，以便所有对`/Login`的请求都被传递给 Tapestry。

恭喜你！您已经运行了第一个 Tapestry Web 应用程序。不可否认，它现在并不比“Hello World”更令人印象深刻，但是在幕后，这个页面已经使用了三个 Tapestry 组件，它只是在等待我们添加更多的功能。

让我们添加以下开创性的行为:当用户按下“让我进去！”按钮，将显示第二页。目前，我们不会对用户名或密码进行任何验证。我们将通过显示第二页来对按钮的按下做出反应。

哪第二页？我们现在就要创建的那个！

##### 创建第二页

让我们将第二个页面命名为 Content，就好像它包含一些需要用户验证的有价值的内容。

右键单击 Package Explorer，选择 New > Other…，找到并选择 Tapestry Page 选项。按下一步。

在打开的新 Tapestry 页面组件对话框中，输入页面名称的内容。确保生成一个关联的 HTML 文件。复选框已选中。将所有其他值保留为默认设置，然后按下一步。

在这里，您可以选择将哪个类用作页面类。我们将创建一些自定义功能，所以让我们创建自己的类。选择“创建新类”单选按钮。将包指定为`com.sitepoint.tapestry.login`，并使类成为公共的和抽象的(是的，我们将再次使用那些模糊的 Tapestry 属性)。您的对话框应该如图 14 所示。

![1508_figure14](img/6c7fca180eed775f3082d59a275203b0.png)
*图十四。使用自定义页面类创建新页面*

按 Finish，Spindle 将为您创建三个新文件:`Content.html`、`Content.page`和`Content.java`。

第二个页面的默认 HTML 模板不包含任何有用的内容，所以我们将用上一节(标题为“我们的第一个 Tapestry Web 应用程序”)中第 2 页的模型代码替换它的内容。将这个模型修改成真正的 HTML 模板，如下所示:

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"    

    "https://www.w3.org/TR/html4/strict.dtd">    

<html>    

<head>    

  <title>You've got it!</title>    

</head>    

<body>    

  <h2>Precious <span jwcid="uname">User</span>!</h2>    

  <h3>Welcome to the wonderful World of Tapestry!</h3>    

</body>    

</html>
```

您可以看到，在第二页上，我们将只使用一个 Tapestry 组件，它被命名为 uname，并伪装成一个标准的 HTML span 元素。您可能知道 span 元素通常用于对文本应用特殊的样式。我们不会在这里指定任何样式，只是我们神奇的`jwcid`，它对于 web 浏览器来说是不可理解和不可见的。

##### 第二页的规格

我们将在第二页的代码中需要用户名和密码，所以让我们在页面规范中创建同样的两个属性(您可以从`Home.page`中复制它们):

```
<property-specification name="uname" type="java.lang.String"/>    

<property-specification name="password" type="java.lang.String"/>
```

让我们也指定 HTML 模板中提到的 uname 组件。这一次，它将是一个标准的 Tapestry Insert 组件，只是将一个字符串插入到输出的 HTML 中。这是规范的外观:

```
<component id="uname" type="Insert">    

  <binding name="value" expression="uname"/>        

</component>
```

这里是完整的页面规范，它被放入`Content.page`文件:

```
<?xml version="1.0" encoding="UTF-8"?>    

<!DOCTYPE page-specification PUBLIC    

  "-//Apache Software Foundation//Tapestry Specification 3.0//EN"    

  "http://jakarta.apache.org/tapestry/dtd/Tapestry_3_0.dtd">    

<!-- generated by Spindle, http://spindle.sourceforge.net -->    

<page-specification class="com.sitepoint.tapestry.login.Content">    

  <description>Content page</description>    

  <property-specification name="uname" type="java.lang.String"/>    

  <property-specification name="password"    

      type="java.lang.String"/>    

  <component id="uname" type="Insert">    

    <binding name="value" expression="uname"/>        

  </component>    

</page-specification>
```

剩下的就是用一些功能填充我们的自定义页面类。

##### 向 Page 类添加功能

我们不仅希望能够读取页面的 uname 和 password 属性，还希望能够设置它们。这就是为什么我们要为 page 类创建两对抽象 getter/setter。请记住，从第一页开始，这些方法将是抽象的，因为我们没有自己创建属性，而是让 Tapestry 在运行时为我们创建。

添加了 getters 和 setters 之后，我们的`Content.java`看起来会像这样:

```
package com.sitepoint.tapestry.login;    

import org.apache.tapestry.html.BasePage;    

public abstract class Content extends BasePage {    

  public abstract String getUname();    

  public abstract String getPassword();    

  public abstract void setUname(String uname);    

  public abstract void setPassword(String password);    

}
```

现在是时候返回到第一个页面，登录并向它的 page 类添加功能，这样当表单被提交时，就会显示第二个页面(我们命名为 Content)。

##### 从一页导航到另一页

我们的监听器方法`onFormSubmit()`仍然是空的。让我们添加一些代码来显示第二页。这可以很简单地完成:我们只需要让 Tapestry 显示另一个页面。

我们如何让 Tapestry 从我们的代码中为我们做一些事情？我们得到了它的代表，cycle(类型为`IRequestCycle`)，它作为参数传递给我们的 listener 方法。所以我们就让这个循环对象来帮我们做这件事吧。这是我们在`Home.java`中的监听器方法的样子:

```
public void onFormSubmit(IRequestCycle cycle) {    

  cycle.activate("Content");    

}
```

让我们看看它是否有效。目前，我们需要在每次进行更改时重启 Tomcat，以便看到应用程序的最新版本。这绝对不是最方便的做法，有更容易的方法达到同样的结果。然而，我们已经有足够的分心，所以我会离开你去研究替代方案。目前，让我们加紧努力。

关闭正在运行的 Tomcat，然后再次启动它。将您的浏览器指向`http://localhost:8080/Login/app`，您将看到我们的登录表单。推一下让我进去！按钮，您应该会看到如图 15 所示的第二页。

![1508_figure15](img/49b1a785fe70f5559b6e8870cba04f90.png)
*图 15。第二页显示没有用户名*

请注意，没有输出用户名。嗯，我没有要求您在登录表单中输入登录名或密码！但是即使我这样做了，我们也没有在我们的 listener 方法中告诉第二个页面我们从用户那里收到了什么信息。让我们纠正这一点。

##### 将参数传递到第二页

现在，我将向您展示一种在 Tapestry 开发中经常使用的将变量传递到另一个页面的设计模式:“bucket brigade”模式。

我们将执行以下操作:

1.  请 Tapestry 给我们一个我们想要显示的下一个页面的引用。

3.  使用页面上的 setters 来分配我们想要传递给页面属性的值。

5.  让 Tapestry 显示下一页。

此时，您应该明白一件重要的事情:尽管我们的 web 应用程序的用户认为他们正在查看的页面是发送到他们浏览器的 HTML，但实际上(就 Tapestry 而言)，每个页面都是一个 Java 类——一个页面类——比如 Home 或 Content。这个阶层有很多人才；发送 HTML 响应的能力只是其中之一。

我们的下一页叫做内容。因此，如果我们想声明对下一页的引用，它应该是这样的:

```
Content nextPage;
```

我们可以告诉 Tapestry:“请给我们一个名为 Content 的页面的页面类实例的引用”。翻译成 Java，应该是这样的:

```
Content nextPage = (Content) cycle.getPage("Content");
```

收到引用后，我们可以用它来调用我们在下一页中准备的任何 setters。例如，如果我们有一个存储在 uname 和 password 变量中的用户名和密码，我们可以这样写:

```
nextPage.setUname(uname);    

nextPage.setPassword(password);
```

然后我们将要求 Tapestry 显示下一页:

```
cycle.activate(nextPage);
```

我们完成的侦听器方法如下所示:

```
public void onFormSubmit(IRequestCycle cycle) {    

  Content nextPage = (Content) cycle.getPage("Content");    

  nextPage.setUname(getUname());    

  nextPage.setPassword(getPassword());    

  cycle.activate(nextPage);    

}
```

注意，在`nextPage.setUname(getUname());`中，我们调用了 Home 类的抽象 getter，并将返回值传递给 Content 类的抽象 setter。这看起来很奇怪:我们怎么能使用一个尚未实现的方法呢？但是它在运行时工作得非常好，因为 Tapestry 为我们创建了所有抽象方法的实现。

让我们测试应用程序的新版本。关闭并启动 Tomcat 服务器，将浏览器指向`http://localhost:8080/Login/app`。当登录表单出现时，输入用户名和任何密码，然后按让我进去！按钮。根据您使用的用户名，结果可能如图 16 所示。

![1508_figure16](img/20343e1a4eb6d27e3e15bbe1eff68e09.png)
*图十六。第二页如预期显示*

##### 限制对页面的访问

因为我们需要用户名和密码，所以自然会限制对我们有价值的内容页面的访问，这样只有授权用户才能访问。现在，让我们为所有用户硬编码一个密码。

通常，我们会检查登录页面中的密码，如果正确，我们会向访问者发送隐藏的内容。但是，如果一些不受欢迎的访问者设法直接请求内容页面，而没有登录呢？从技术上讲，这是可能的。

解决这个问题的方法之一是在内容页面中检查密码。如果密码正确，我们显示页面的内容，否则我们将用户重定向到登录页面。

为了能够做到这一点，我们的内容页面需要与 Tapestry 达成协议。它应该问，“请通知我你什么时候要给我看，这样我可以检查一些东西。”如果页面实现了`PageValidateListener`接口，就可以实现这种协议。要实现这个接口，我们需要声明它，并在我们的 page 类中添加一个`pageValidate()`方法。有了这些补充，我们的`Content.java`看起来像这样:

```
package com.sitepoint.tapestry.login;    

import org.apache.tapestry.html.BasePage;    

import org.apache.tapestry.event.PageValidateListener;    

import org.apache.tapestry.event.PageEvent;    

public abstract class Content extends BasePage     

    implements PageValidateListener {    

  public void pageValidate(PageEvent event) {    

    // Some code will go here    

  }    

  public abstract String getUname();    

  public abstract String getPassword();    

  public abstract void setUname(String uname);    

  public abstract void setPassword(String password);    

}
```

注意两个新的导入语句。您不需要知道在哪里可以找到您需要的类或接口，也不需要手工编写这些语句。Eclipse 将为您完成所有这些工作。比如你输入`PageEvent`，Eclipse 会用红色下划线，因为它不知道你的意思。将光标放在这个单词上，选择 Source > Add Import，这个智能 IDE 将为您创建必要的导入语句！

Tapestry 将检查页面是否实现了`PageValidateListener`接口，因此它应该有一个`pageValidate()`方法。Tapestry 在向用户显示页面之前调用这个方法，由我们来定义应该在这个方法中进行哪些检查。

我们将检查用户指定的密码是否与我们硬编码的密码相同。如果没有，我们将告诉 Tapestry:“停止！不要显示此页面！请将用户重定向到主页。这是它在 Java 中的样子:

```
if (!"mellon".equals(getPassword())) {    

  throw new PageRedirectException("Home");    

}
```

如果用户输入的密码是“mellon”(精灵语中“朋友”的意思)，一切正常:他们会看到内容页面。如果没有，我们打开一个警报，告诉 Tapestry:“不管您在做什么，只要向这个用户显示我们的登录表单。”

完成的`Content.java`应该是这样的:

```
package com.sitepoint.tapestry.login;    

import org.apache.tapestry.PageRedirectException;    

import org.apache.tapestry.html.BasePage;    

import org.apache.tapestry.event.PageValidateListener;    

import org.apache.tapestry.event.PageEvent;    

public abstract class Content extends BasePage implements PageValidateListener {    

  public void pageValidate(PageEvent event) {    

    if (!"mellon".equals(getPassword())) {    

      throw new PageRedirectException("Home");    

    }    

  }    

  public abstract String getUname();    

  public abstract String getPassword();    

  public abstract void setUname(String uname);    

  public abstract void setPassword(String password);    

}
```

现在让我们测试我们完成的登录 web 应用程序。

启动或重启 Tomcat，并像往常一样导航到`http://localhost:8080/Login/app`。输入任何用户名和错误的密码，然后按让我进去！按钮。什么也不会发生——您只会看到相同的登录表单。现在键入我们上面硬编码的密码的秘密单词。您应该能够查看内容页面！

##### 我们学到了什么？

首先，我们看到 Tapestry HTML 模板可以在任何 web 浏览器中完美显示，因为 Tapestry 的服务器端挂钩仅限于标准 HTML 标记的属性，所以它们实际上是不可见的。

这一点非常重要，因为表示(HTML)完全独立于逻辑(Java 代码)，并且它们中的任何一个都可以被编辑，而不需要对另一个做任何修改。这是表示和逻辑之间最干净的分离——这是其他框架难以做到的，也是经常失败的。

我们还看到了 Eclipse IDE(带有 Spindle 插件)在 Tapestry 开发中是多么方便和有用。

我们动手创建了几个页面，看到每个页面都有三个部分:一个 HTML 模板、一个页面规范和一个页面类。

我们了解到 Tapestry 组件是在页面规范中声明的，并且看到了如何在 HTML 模板中组件的 jwcid 属性和事件侦听器或属性之间建立绑定。

我们还看到了 Tapestry 更复杂但更强大的一面:如何在页面规范中声明页面属性，以及我们如何处理它们，就好像它们真的存在一样，即使它们只是由 Tapestry 在运行时创建的。

我们学习了如何编写代码从一个页面导航到另一个页面，以及如何在页面之间传递参数(“bucket brigade”模式)。

最后，我们看到了 Tapestry 页面如何保护自己不被未授权的访问者访问，以及实现这一功能需要做的事情是多么少。

咻！对于这个介绍，我们已经掌握了相当多的东西，但是 Tapestry 的世界还有更多的东西！

***延伸阅读***

目前只有两本关于雅加达挂毯的英文书籍:

1.  《挂毯行动》由挂毯的创作者霍华德·刘易斯·希普创作。这本书有助于更深入地理解 Tapestry 背后的思想，以及 Tapestry 3 内部工作的细节。但是，作为初学者的教程，用处不是很大。
2.  [“享受 Tapestry 的 Web 开发”，作者 Ka lok 'Kent' Tong](http://www.amazon.com/gp/product/1411649133/qid=1138182732/) 。我会把这本书称为高级教程，它是每个 Tapestry 开发人员的必备书目。它包含了从基础开始到一些高级主题的例子和建议。当我开发自己的商业 Tapestry 应用程序时，我发现它非常有价值。这本书有两个版本:Tapestry 3 版和 Tapestry 4 版。

## 分享这篇文章