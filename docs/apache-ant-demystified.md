# Apache Ant 揭秘——第 1 和第 2 部分

> 原文：<https://www.sitepoint.com/apache-ant-demystified/>

好的软件工程的原则之一是每日构建。如果你每天都构建你的软件，那么它就像一个晴雨表，显示项目的状态。如果某个东西坏了，它会更快被注意到，所以它会更快被修复。每日构建成为项目的心脏。这就是为什么微软每天构建 5gb 的 Windows 源代码的原因，即使在非常强大和昂贵的服务器上要花 12 个小时才能完成。

正如您可能想象的那样，为了能够执行日常构建，构建过程必须是自动的和可重复的。依赖于某个喋喋不休的同事在命令提示符窗口中键入复杂的命令来调用 Java 编译器(具有本文这么长的类路径)是没有好处的。开发人员的军械库中需要的是另一个简洁的工具。需要的是阿帕奇蚂蚁。

本教程的第 1 部分从下面开始。

[教程的第 2 部分从这里开始](https://www.sitepoint.com/apache-ant-demystified/)。

##### 关于蚂蚁

Ant 是 Apache Software Foundation 提供的基于 Java 的开源构建工具。它已经迅速成为 J2EE 项目事实上的构建工具，所以对于开发人员来说，至少熟悉一下 Ant 的基础知识是值得的。许多现代 Java 开发环境也支持 Ant。

Ant 使用构建文件，这些文件只是告诉 Ant 程序做什么的 XML 文件。因为 Ant 是一个 Java 程序，所以它是一个跨平台的工具；如果您没有使用任何特定于平台的特性，您可以将构建文件从一个操作系统移动到另一个操作系统，而不做任何更改。

虽然 Ant 是一个构建工具，但是不要认为它只适合编译 Java 代码。它的名字很贴切；想想一行蚂蚁能背上多少重物。Ant 是名副其实的功能性瑞士军刀。例如，完全可以使用 Ant 从源代码库中获取最新的代码，编译这些代码，运行单元测试并生成结果报告，生成 Javadoc 文档，将代码打包到 J2EE 应用程序中，将该应用程序部署到应用服务器上……然后在完成所有工作后给自己发送一封电子邮件。唷！只要您愿意，Ant 可以响应您的一个命令来完成所有这些工作。不错吧，嗯？毫无疑问:能够编写和维护 Ant 构建文件对于任何 Java 开发人员来说都是一项非常有用的技能。

##### 获取并安装 Ant

可以从 [Apache Ant 网站](http://ant.apache.org/)的[下载页面](http://ant.apache.org/bindownload.cgi)下载 Ant。像所有的 Apache 下载一样，它有二进制(预编译)和源代码两种形式。我建议您下载二进制发行版，至少在开始时是这样。

找到您下载的文件，该文件将被命名为类似 apache-ant-1.6.2-bin.zip 的文件。Ant 没有安装程序，所以您需要做的就是将下载文件解压缩到您想要安装软件的目录，例如 C:Program Files。如果使用 WinZip 从 Zip 文件中提取文件，请确保选中“使用文件夹名称”选项，以便保留 Ant 安装文件夹结构。

在开始使用 Ant 之前，我们需要修改系统路径，以便可以从任何命令提示符启动 Ant，而不管文件系统上的当前目录是什么。如果 Ant 要正确运行，我们还需要添加一些系统范围的环境变量。以下说明适用于 Windows XP 其他操作系统的用户应该查阅他们的文档，了解如何修改系统路径，以及添加和编辑环境变量。

右键单击我的电脑图标，然后选择属性。然后单击高级选项卡并选择环境变量按钮。根据您是否是电脑的管理员，您可以将 Ant 安装文件夹添加到全局路径(这将影响所有用户)或仅添加到您的用户帐户的路径。如果“系统变量”组中的“新建”、“编辑”和“删除”按钮是灰色的，那么您没有权限修改全局路径，只能为您自己的用户帐户设置 Ant。别担心，它仍然会工作得很好。让我们假设你会这么做。

如果您还没有一个名为 Path 的用户变量，单击 New 按钮，将变量名设置为`Path`，将变量值设置为 Ant 安装中 bin 文件夹的完整路径，例如 C:Program files Apache group Apache-Ant-1 . 6 . 2 bin，然后单击 OK。如果您已经有了一个`Path`用户变量，在列表中高亮显示它，然后点击编辑按钮。将分号添加到现有路径的末尾，然后将完整路径添加到 bin 文件夹，如上所述。您可能会得到一个看起来模糊的路径:C:Program FilesWidgetWareWidget；c:程序文件 Apache 组 apache-ant-1.6.2bin

完成编辑后，单击“确定”。

下一步是设置一个名为`ANT_HOME`的新环境变量。当您仍然在环境变量对话框中时，单击 User variables 组中的 New 按钮，创建一个名为`ANT_HOME`的新变量，其值对应于 Ant 安装文件夹。例如，C:Program Filesapache-ant-1.6.2 注意，这次末尾没有 bin。单击确定。

最后，我们需要设置另一个新的环境变量:它将告诉 Ant 您的 Java 开发工具包(JDK)安装在哪里。遵循上面针对`ANT_HOME`环境变量描述的过程，但是这一次，将变量名设置为`JAVA_HOME`，将变量值设置为 JDK 的安装位置。在我的机器上是 C:Program FilesJavajdk1.5.0，但你的可能不同。现在，多次单击 OK 关闭所有打开的对话框和属性表。

您可以通过打开命令提示符并输入`set ant_home`然后输入 Enter，然后输入`set java_home`然后输入 Enter 来确认这些步骤是否成功。应该会显示您在上述步骤中输入的各个路径。如果这不起作用，请尝试注销并再次登录。

我们还需要验证 Ant 已经正确安装并准备就绪。在同一个命令提示符窗口中，键入`ant â€“version`，然后按 Enter 键。如果一切正常，那么 Ant 应该会告诉您它是哪个版本以及编译时间。如果是这种情况，那么我们准备开始学习更多关于 Ant 的知识，并编写我们的第一个构建文件！

##### 好事有三种:项目、目标和任务

Ant 中的三个基本概念是*项目*、*目标*和*任务*。其实也有*属性*的，但是谁听说过好东西四个一起来的？概念非常简单:每个 Ant 构建文件包含且仅包含一个项目，该项目必须包含至少一个目标，但通常会包含更多。此外，每个目标都包含任务。让我们通过直接进入并开始编写一个构建文件来看看项目部分。

使用文本编辑器创建一个名为 build.xml 的新文件，并将其保存在某个位置。您可以使用记事本来完成这项任务，或者使用一个漂亮的语法高亮编辑器——这真的没关系。键入或粘贴以下内容:

```
<?xml version="1.0" encoding="UTF-8"?> 

<project name="helloworld" default="compile" basedir="."> 

  <description> 

    Build file for the Hello World application. 

  </description> 

</project>
```

第一行只是一个标准声明，说明这是一个使用 UTF-8 字符编码的 XML 1.0 文档。你以前可能遇到过这条线，或者类似的东西。Ant 实际上并不要求这样做，但是包含它是个好主意，因为 Ant 构建文件是 XML 文档，有效的 XML 文档必须以这个声明开始。

接下来，有一个名为`attribute`的项目标签。这只是我们想给这个项目起的一个名字；在这种情况下，它是极其缺乏想象力的`"helloworld"`。当我们从其他 Ant 构建文件中调用 Ant 构建文件时，会用到项目的名称，现在我们不需要担心这个问题。

当命令行上没有提供特定的目标时，`default`属性告诉 Ant 执行哪个目标。稍后会详细介绍。

最后，`basedir`属性是 Ant 在计算路径时应该使用的目录路径。通常情况下，它会被设置为`.`(单个句点)，这是 Windows 和 UNIX 中引用文件本身所在目录的简写。换句话说，如果您将 build.xml 文件保存在 C:SitePoint TutorialsAnt 中，那么`basedir`属性将具有值`C:SitePoint TutorialsAnt`；当 Ant 以后计算出任何相对路径时，它就以此为起点。

嵌套在`project`标签中的是一个`description`标签。如果使用构建文件的人请求关于构建文件的帮助，您可以在这里提供项目的更详细的描述。

就目前情况来看，这个构建文件不是很有用。事实上，它甚至不成立，因为我们还没有定义任何目标。目标是您希望执行的一组任务。您可以告诉 Ant 您想要执行哪个(哪些)目标，如果没有给定目标，则使用项目的默认目标。让我们让构建文件做一些有用的事情，并添加一个目标:

```
<?xml version="1.0" encoding="UTF-8"?>  

<project name="helloworld" default="compile" basedir=".">  

  <description>  

    Build file for the Hello World application.  

  </description>  

  <target name="compile" description="Compile all sources.">  

    <mkdir dir="classes" />  

    <javac srcdir="src" destdir="classes" />  

  </target>  

</project>
```

这里，我们在`project`标签中嵌套了一个`target`标签，并设置了它的`name`和`description`属性。同样，如果构建文件的用户请求帮助，描述会得到输出。我们稍后会看到如何做到这一点。两个任务嵌套在`target`标签中:`mkdir`和`javac`。Ant 自带的内置任务比你一棍子能解决的还要多；参见[在线手册](http://ant.apache.org/manual/)以获得一个想法。如果这些还不够，您可以使用其他人编写的可选任务来扩展 Ant。如果这对您来说还不够，那么您不仅要求很高，而且您总是可以使用 Java 编写自己的 Ant 任务(当然)。

`mkdir`任务，顾名思义，制作一个目录。在这种情况下，它生成了`classes`输出目录，我们编译的 Java 类将进入该目录。记住，这是相对于使用`project`标签的`basedir`属性指定的位置发生的。因此，使用前面的例子，我们的`mkdir`任务将直接在 C:SitePoint TutorialsAnt 下创建一个`classes`目录。

大多数 Ant 构建文件都会在某个时候使用`javac`任务。正如你可能已经猜到的，它是 javac.exe——Java 编译器的包装器。在本例中，我们使用`srcdir`属性告诉它在`src`目录下查找 Java 源代码，并将编译后的类输出到`classes`目录。

*重要:蚂蚁是个苛刻的小三；如果没有首先创建 classes 目录，javac 任务在试图输出一些文件时将会失败。这就是为什么我们必须首先使用 mkdir 任务。*

就是这样！我们已经定义了第一个目标，并使用了两个内置的 Ant 任务。如果您有一些简单的 Java 源代码，它不依赖于类路径上的任何其他库——我这里说的是“Hello World”级别的复杂性——并且这些源代码位于`src`目录中，那么这个构建文件会将这些类编译到 classes 目录中。Ant 唯一需要学习的部分是如何运行构建文件！不完全是。不过还是看看怎么做吧。

##### 使用 Ant

正如我前面所说的，许多现代 Java IDEs 都集成了对 Ant 的支持，但是在您自己探索您的 ide 是否支持 Ant 之前，让我们学习一下如何以自然的方式运行 Ant:从命令行。

在保存 build.xml 文件的目录下打开命令提示符。现在输入`ant â€“projecthelp`，然后回车。Ant 应该显示以下信息:

```
Buildfile: build.xml  

  Build file for the Hello World application.  

Main targets:  

 compile Compiles all sources.  

Default target: compile
```

Ant 告诉我们构建文件的名称、项目描述，并提供构建文件中的目标列表及其描述。请注意，如果您没有为一个目标提供描述，那么该目标根本不会作为项目帮助的一部分列出。因此，除非您能够查看构建文件的源代码，否则您甚至不会知道目标在那里。

Ant 还告诉我们默认目标是什么。在这种情况下，它是`compile`目标。请注意，我们不必告诉 Ant 我们的构建文件的名称。当您键入`ant`命令时，Ant 会在您所在的目录中查找名为 build.xml 的构建文件。这并不意味着您必须调用所有的构建文件 build.xml 这只是一个方便的功能。如果您使用另一个名称，您应该使用`â€“f`命令行开关来告诉 Ant 您的构建文件的名称。比如`ant â€“f build_me.xml`。

现在，键入`ant`并按回车键。您应该会看到类似下面的内容:

```
Buildfile: build.xml  

compile:  

  [mkdir] Created dir: C:SitePoint TutorialsAntclasses  

  [javac] Compiling 1 source file to C:SitePoint TutorialsAntclasses  

BUILD SUCCESSFUL  

Total time: 2 seconds
```

上面的代码显示 Ant 找到了 build.xml 并执行了它的默认目标`compile`。如果您检查该类的目录，您应该在那里找到一些 100%纯 Java。恭喜你！您已经成功地使用 Ant 编译了一些 Java 代码！Ant 在执行时在方括号中显示每个任务的名称，并告诉我们构建花了多长时间。当您的构建需要几个小时，并且您想在聚会上通过谈论您的项目有多复杂来让人们厌烦时，这是一个很好的特性。

现在要做的最后一件事是:再次键入`ant`并按回车键。您将看到以下输出:

```
Buildfile: build.xml  

compile:  

BUILD SUCCESSFUL  

Total time: 1 second
```

在这种情况下，Ant 什么都没做！它足够聪明，能够意识到代码已经编译过了，不需要再编译一次。蚂蚁练习建立回避。这意味着它检查文件的时间戳以确定它们是否已经更改，并且只编译已经更改的文件，从而减少构建时间，这通常在大型项目中非常明显。

在本系列的[第 1 部分中，我们学习了为什么使用 Ant 进行构建是一个好主意，并了解了项目、目标和任务的核心 Ant 概念。我们还编写并执行了一个简单的构建文件。](https://www.sitepoint.com/article/apache-ant-demystified)

现在，让我们看看 Ant 可以让 Java 开发人员的生活变得更轻松的其他一些方式。

##### 目标依赖关系

Ant 目标可以依赖于其他目标。仔细想想，这其实真的很有用。您可以有一个创建 JAR 文件的目标，这个目标依赖于编译源代码的目标。让我们在 build.xml 文件中再添加两个目标(注意[build . XML 的完整版本可以下载](https://www.sitepoint.com/examples/ant/ant.zip)):

```
<?xml version="1.0" encoding="UTF-8"?>   

<project name="helloworld" default="compile" basedir=".">   

  <description>   

    Build file for the Hello World application.   

  </description>   

  <target name="compile" description="Compile all sources.">   

    <mkdir dir="classes" />   

    <javac srcdir="src" destdir="classes" />   

  </target>   

  **<target name="clean" description="Clean up output directories.">   

    <delete dir="classes" />   

  </target>   

  <target name="rebuild" depends="clean,compile"   

          description="Cleanly compiles all sources." />**   

</project>
```

我在这里引入了一些新的语法。我们先来看一下`clean`目标。这包含一个`delete`任务，该任务简单地删除`classes`输出目录及其内容。Ant 的`delete`任务是一个多才多艺的小家伙:它可以以各种组合和方式删除文件和目录。我们还有一个`rebuild`目标，它根本不包含任何任务！这是怎么回事？

您会注意到`rebuild`目标有一个新的`depends`属性被设置为`clean,compile`。这意味着`rebuild`目标应该先执行`clean`目标，然后再执行`compile`目标。因此,`classes`输出目录将被删除，然后重新创建并重新编译 Java 代码。换句话说，发生了一次完全的重新构建，这在您想要编译所有内容时非常有用，并且确保您没有为那些您随后删除了源代码的类留下任何旧的类文件。在类路径中包含过时的类而没有意识到这一点会导致您浪费不必要的调试时间。

重要提示:ant 只执行一次目标，即使有多个目标依赖于它。

使用`ant rebuild`运行`rebuild`目标并观察输出。我们现在可以执行一个干净的构建，安全的知道我们只使用那些我们认为我们正在使用的类！

##### 干燥使用特性

编程的时候，遵循干巴巴的原则很重要:不要重复自己。如果你眼尖的话，你可能已经注意到我们的构建文件中存在一些重复。目录的名字被提到了三次:两次在目标`compile`中，第三次在目标`clean`中。我们需要的是一种引入某种间接方式的方法，这样，如果我们突然决定将编译好的 Java 代码放在一个名为`bytecode`的目录中，而不是`classes`，我们只需编辑一次。幸运的是，Ant 用属性覆盖了它。

让我们检查一下语法。一旦我们添加了一个属性来存储输出目录的名称，这就是我们的构建文件的样子:

```
<?xml version="1.0" encoding="UTF-8"?>   

<project name="webforum" default="compile" basedir=".">   

  <description>   

    Build file for the WebForum application.   

  </description>   

  **<property name="classes.dir" location="classes" />**   

  <target name="compile" description="Compile all sources.">   

    <mkdir dir="**${classes.dir}**" />   

    <javac srcdir="src" destdir="**${classes.dir}**" />   

  </target>   

  <target name="clean" description="Clean up output directories.">   

    <delete dir="**${classes.dir}**" />   

  </target>   

  <target name="rebuild" depends="clean,compile"   

          description="Cleanly compiles all sources." />   

</project>
```

属性只是名称/值对。在这种情况下，名称为`classes.dir`，值为`classes`。我们实际上使用了`location`属性来设置后者，因为我们正在处理一个文件路径，我们希望相对于为`basedir`属性设置的值来评估这个文件路径。对于其他类型的属性，使用`value`属性。当您想要引用属性所表示的实际值时，只需使用`${property name}`语法，例如`${classes.dir}`。如果您现在尝试使用构建文件，您应该会发现它和以前完全一样；重要的一点是更容易维护。

让我们假设，出于某种奇怪的原因，我们确实希望我们编译的类放在一个名为`bytecode`而不是`classes`的目录中。我们如何实现这一目标？我们可以编辑属性声明，只需要在一个地方进行修改就可以了，但是我想向你们展示另一种非常强大的方法。

Ant 允许您在命令行上覆盖属性。您可能遇到过 Java 的`-D`开关，它允许您设置系统属性。Ant 使用相同的语法。因此，运行`ant â€“Dclasses.dir=bytecode rebuild`并观察结果。

我们编译的类以`bytecode`而不是`classes`结束。您是否希望删除`classes`目录，因为我们告诉 Ant 运行`rebuild`目标，而这个目标又调用`clean`目标？如果是的话，一定要记住`clean`目标也是指我们传入的`${classes.dir}`的值。由于这个原因，旧的`classes`目录被保留了下来。另外，注意在`-D`和被覆盖的属性之间没有空格。

一般来说，只有当您只想对该构建进行更改时，才可以用这种方式覆盖属性。如果我们一直想用字节码代替类，我们可以在构建文件中编辑属性声明。记住:一个好的构建过程是可重复的。

##### 隐藏目标

有时，当您编写构建文件时，您会想要隐藏目标，以便它们不能被直接执行。您可能会想，如果目标不能被直接执行，它还有什么用。嗯，能够创建所谓的内部目标，为其他可见的目标做有用的工作是很有用的。要隐藏一个目标，只需在它的名字前加一个减号。将下面突出显示的代码添加到我们的构建文件中:

```
<?xml version="1.0" encoding="UTF-8"?>   

<project name="helloworld" default="compile" basedir=".">   

  <description>   

    Build file for the Hello World application.   

  </description>   

  <property name="classes.dir" location="classes" />   

  **<property name="dist.dir" location="dist" />   

  <property name="dist.jarfile" value="helloworld.jar" />**   

  <target name="compile" **depends="-init"** description="Compile all sources.">   

    <mkdir dir="${classes.dir}" />   

    <javac srcdir="src" destdir="${classes.dir}" />   

  </target>   

  <target name="clean" description="Clean up output directories.">   

    <delete dir="${classes.dir}" />   

  </target>   

  <target name="rebuild" depends="clean,compile"   

          description="Cleanly compiles all sources." />   

  **<target name="-init">   

    <!-- Create the time stamp. -->   

    <tstamp>   

      <format property="TODAY_UK" pattern="dd MMM yyyy HH.mm" locale="en_GB" />   

    </tstamp>   

  </target>   

  <target name="dist" depends="rebuild"   

          description="Creates the binary distribution.">   

    <mkdir dir="${dist.dir}/${TODAY_UK}" />   

    <jar basedir="${classes.dir}"   

         destfile="${dist.dir}/${TODAY_UK}/${dist.jarfile}" />   

  </target>**   

</project>
```

这里的变化还真不少！`compile`目标现在依赖于隐藏的`init`目标。`init`目标本身使用 Ant 的`tstamp`任务，使用英国的日期和时间格式为新属性`TODAY_UK`分配日期和时间戳。稍后我们将在构建文件中使用该属性，创建一个新目录，其名称反映了构建运行的日期和时间。我们隐藏这个目标是因为，就其本身而言，它不做任何非常有用的事情，所以我们不想从命令行调用它。

回到构建文件的顶部，我们已经设置了几个新属性来定义一个`dist`输出目录，以及一个输出 JAR 文件的文件名:`helloworld.jar`。

最后，我们添加了一个新的`dist`目标，它依赖于`rebuild`目标。我们的老朋友`mkdir`任务又被使用了。它使用了`dist.dir`和`TODAY_UK`属性来创建 JAR 文件将要存放的输出目录。

Ant 的`jar`任务然后被用来创建 JAR 文件。`jar`任务可以接受许多不同的参数，但是这里我们只设置其中的两个。`basedir`属性告诉`jar`任务在哪里可以找到我们想要放入 JAR 文件的编译后的 Java 类，而`destfile`属性指定输出 JAR 文件本身的名称。

如果您现在在命令提示符下输入`ant dist`，您应该会看到类似如下的输出:

```
Buildfile: build.xml   

clean:   

  [delete] Deleting directory C:SitePoint TutorialsAntclasses   

-init:   

compile:   

  [mkdir] Created dir: C:SitePoint TutorialsAntclasses   

  [javac] Compiling 1 source file to C:SitePoint TutorialsAntclasses   

rebuild:   

dist:   

  [mkdir] Created dir: C:SitePoint TutorialsAntdist26 Feb 2005 12.38   

    [jar] Building jar: C:SitePoint TutorialsAntdist26 Feb 2005 12.38helloworld.jar    

BUILD SUCCESSFUL   

Total time: 2 seconds
```

注意，Ant 遍历了我们创建的依赖关系树。我们告诉它执行`dist`目标，它依赖于`rebuild`目标，后者又依赖于`clean`目标，然后是`compile`目标。`compile`目标本身取决于`â€“init`目标。Ant 自动解开这一切，按照以下顺序执行目标:`clean`、`-init`、`compile`、`rebuild`最后`dist`。

需要注意的最后一点是，如果您查看 Ant 构建的`helloworld.jar`文件(您可以使用 WinZip 来完成这项工作)，您会看到 Ant 已经自动添加了一个`MANIFEST.MF`文件，其中包含了构建的简要细节，比如 Ant 的版本和使用的 Java 编译器。在我的机器上，它看起来像这样:

```
Manifest-Version: 1.0

Ant-Version: Apache Ant 1.6.2

Created-By: 1.5.0-b64 (Sun Microsystems Inc.)
```

当然，如果需要的话，您可以忽略这种行为，让 Ant 的`jar`任务使用您自己的清单文件。详细信息包含在 Ant 手册的`jar`任务文档中。

##### 目标混淆

我在构建文件中经常使用的一种技术是目标别名。这意味着我定义了一个除了调用另一个目标之外什么也不做的目标。这看起来像是浪费时间，但是考虑这样一个场景:通过 Ant 的 echo 任务向用户提供一个显示一些简单帮助文本的目标:

```
<target name="help" depends="usage" />   

<target name="usage" description="Display usage information.">   

  <echo message="  Execute 'ant -projecthelp' for build file help." />   

  <echo message="  Execute 'ant -help' for Ant help." />   

</target>
```

通过创建对`usage`目标的依赖，`help`目标被别名化为`usage`目标。现在，我们构建文件的用户可以通过键入`ant usage`或`ant help`获得在线帮助。注意，`help`目标没有出现在 Ant 的`â€“projecthelp`选项显示的目标列表中，因为我们没有为它提供描述。为了进一步细化，将`help`或`usage`目标设为默认目标，这样用户只需在命令行中输入`ant`，不带参数，就可以获得帮助。

提示:认真考虑让你的构建文件对用户友好，让它的默认目标不做任何潜在的耗时和/或危险的事情！显示帮助是默认的一个很好的选择。

##### 牛逼的蚂蚁

我们实际上只是触及了 Ant 功能的皮毛，但是我希望我已经向您展示了它是一个多么灵活和强大的工具。事实上，在 Java 开发领域，您很难想到任何有用的、ant 无法处理的事情。

值得花时间浏览 Ant 提供的核心任务列表，并尝试其中的一些任务。如果你去安装 Ant 的文件夹，你会找到一个`docs`文件夹。其中有一个`index.html`文件，它将打开 Apache Ant 网站的本地副本。这包含一个到 Ant 手册的本地副本的链接，它通常写得很好，因为 Ant 现在是一个成熟的程序。该手册还提供了很多关于 Ant 的外部资源的链接。快乐大厦！

## 分享这篇文章