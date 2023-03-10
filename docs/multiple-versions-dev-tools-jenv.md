# 如何使用 Jenv 运行所有开发工具的多个版本

> 原文：<https://www.sitepoint.com/multiple-versions-dev-tools-jenv/>

### 目录 

*   [介绍 jenv](#introducingjenv)
*   [安装新工具](#installinganewtool)
*   [安装特定版本的工具](#installingaspecificversionofatool)
*   [查看有哪些版本可以安装](#seeingwhatversionsareavailabletoinstall)
*   [切换工具版本](#switchingversionsofatool)
*   [总结](#summary)
*   [评论](#comments)

如果您需要一个独立于平台的工具，允许您管理基于 Java 的应用程序(如 Maven、Gradle 和 Tomcat)的多个安装，那么 [jenv](http://www.jenv.io/) 是正确的选择。使用 jenv，您可以轻松地并排安装几个版本，并轻松选择在系统范围内使用哪个版本，或者只在单个 shell 上使用哪个版本。这使得用 Maven 3.1 构建一个项目和用 3.5 构建另一个项目变得非常容易。

## 介绍 jenv

Java 生态系统中有大量您可能想要使用的工具——从 Java 本身，到 Maven 和 Gradle 等构建工具，再到 ActiveMQ 和 Tomcat 等第三方应用程序。通常，您会在不同的项目中需要这些工具的不同版本，或者希望在不同的版本中尝试一个项目来进行兼容性测试。

管理这些不同的版本可能是一项复杂的任务。每个工具都有不同的方法来获得它们，安装它们，控制正在使用的版本，通常做任何事情。

谢天谢地，在这方面有一个工具可以让生活变得容易得多: [jenv](http://www.jenv.io/) (不要和 [jEnv](http://www.jenv.be/) 混淆，后者设置了`JAVA_HOME`)。该工具可以在 Windows、Mac 和 Linux 上使用，使得管理大量基于 Java 的工具的多个版本变得非常容易。在撰写本文时，它可以与您可能想要使用的 200 多种不同的工具一起工作。

jenv 的安装因你使用的平台而异，但是网站上给出了如何安装的明确说明。虽然所有示例都是从 Mac 的角度编写的，但它们同样适用于任何受支持的系统。

### 安装新工具

安装新工具非常简单:

```
$ gradle
-bash: gradle: command not found

$ jenv install gradle
Installing: gradle 3.5
Parsing http://jenv.mvnsearch.org/candidate/gradle/download/3.5/Darwin/x86_64

Downloading: gradle 3.5

Downloading http://get.jenv.mvnsearch.org/download/gradle/gradle-3.5.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 69.8M  100 69.8M    0     0   390k      0  0:03:03  0:03:03 --:--:--  350k

Do you want gradle 3.5 to be set as default? (Y/n): y
Setting gradle 3.5 as default.
Done installing! 
```

这在一条命令中就实现了:

*   已确定工具的最新版本
*   下载了工具
*   将它安装到本地系统上
*   已将本地系统配置为默认使用此版本的工具

这很有效:

```
$ gradle
Starting a Gradle Daemon (subsequent builds will be faster)
:help

Welcome to Gradle 3.5.

To run a build, run gradle <task> ...

To see a list of available tasks, run gradle tasks

To see a list of command-line options, run gradle --help

To see more detail about a task, run gradle help --task <task>

BUILD SUCCESSFUL

Total time: 2.691 secs 
```

### 安装工具的特定版本

相反，如果您想安装某个工具的特定版本，您可以:

```
$ jenv install tomcat 7.0.68
Installing: tomcat 7.0.68
Parsing http://jenv.mvnsearch.org/candidate/tomcat/download/7.0.68/Darwin/x86_64

Downloading: tomcat 7.0.68

Downloading http://get.jenv.mvnsearch.org/download/tomcat/tomcat-7.0.68.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 9268k  100 9268k    0     0   233k      0  0:00:39  0:00:39 --:--:--  342k

Do you want tomcat 7.0.68 to be set as default? (Y/n): n
Done installing! 
```

与上面完全相同，但是通过指定版本号，这就是我们得到的版本。

### 查看可以安装的版本

为了安装特定的版本，您可能需要知道有哪些选项。这可以简单地通过以下方式实现:

```
$ jenv ls maven
Available maven Versions
=========================
>* 3.5.0
 * 3.3.9
 * 3.3.3
   3.3.1
 * 3.2.5
   3.2.3
   3.2.2
   3.2.1
   3.1.1
   3.1.0
   3.0.5-mvnsearch
 * 3.0.5
 * 3.0.4
   2.2.1
   2.0.11 
```

这个列表实际上显示了三件不同的事情:

*   每一行都是 jenv 支持的版本
*   旁边带有星号的每一行都已经下载完毕，可以使用了
*   旁边带箭头的行是当前正在使用的版本

所以上面显示我已经下载了 Maven 3.5.0，3.3.9，3.3.3，3.2.5，3.0.5，3.0.4，目前在用 3.5.0。

### 切换工具的版本

当您安装了多个版本，并且需要从一个版本切换到另一个版本时，可以通过以下方式实现:

```
$ jenv use maven 3.0.4
Using maven(3.0.4) in this shell. 
```

需要注意的一点是，这为这个 shell 改变了 it *。您已经打开的任何其他会话都不会受到此命令的影响，因此在您想要进行快速测试的任何时候，或者在同时处理多个项目时，都可以非常安全地使用它。*

通过运行以下命令来更改默认版本(在所有 shells 中使用):

```
$ jenv default maven 3.5.0
Default maven version set to 3.5.0 
```

![Using jenv to sort out tools](img/c881ccef74cc89118db56cce068520e0.png)

## 摘要

jenv 工具使得在您的系统上安装基于 Java 的工具(使用`jenv install`)和为特定用例切换版本(`jenv use`)变得很容易。本文非常快速地介绍了这个工具可以实现的功能，但是它还可以提供更多的帮助。一定要阅读[网站](http://www.jenv.io/)和工具帮助，最后不要再强调管理你的 Java 工具链。

## 分享这篇文章