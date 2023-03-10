# Linux 快速入门指南

> 原文：<https://www.sitepoint.com/jsp-quick-start-guide-linux/>

****【Java server Pages(JSP)**是一种服务器端技术，它允许开发人员创建基于 Web 的应用程序，这些应用程序可以利用根据 JavaBeans 规范用 Java 编程语言开发的组件。在某些方面，它可以与 ASP 和 PHP 等服务器端脚本语言相提并论。在 Java 语言的 C 风格语法中，JSP 类似于 PHP，但是 JSP 也被设计成可以使用 ASP 之类的组件进行扩展。**

然而，归根结底，JSP 是一种独一无二的技术。速度极快，独立于平台，高度可伸缩，内置了对分布式处理的支持，兼容所有主要的 Web 服务器，并且对于大多数用途都是免费的，有人可能会问为什么 JSP 没有得到更多的使用。

在我看来，有两个主要原因:

*   为了充分利用 JSP，开发人员必须首先熟悉 Java 编程语言。不幸的是，对于喜欢专注于设计并在必要时涉足一两种脚本语言的 Web 开发人员来说，这可能是一个相当高的要求。对于对学习 Java 感兴趣的人来说，有很多好的在线教程和书籍，但是这个过程可能很耗时，而且根据个人情况，相当具有挑战性，因为 Java 是一种纯面向对象的语言。
*   建立一个服务器环境来开发和测试 JSP 可能是一项令人困惑的任务，特别是因为大多数指导用户完成这个过程的文档都充斥着技术术语，假设您已经对 JSP 了如指掌。如果您刚刚开始使用 JSP，那么您需要一个快速启动并运行服务器的分步指南，这样您就可以集中精力学习这种语言，稍后再考虑服务器配置的细节。

在本文中，我将尝试通过指导您在 Linux 机器上设置支持 JSP 的 Web 服务器来纠正后一点。希望这将为您进入 JSP 开发的世界提供一个良好的开端。我将带您了解如何在您的服务器上运行一个基本的 JSP 页面，然后您就可以开始阅读我们的 JSP 文章系列了，[JSP 文件](https://www.sitepoint.com/blog/)装备齐全！

至于台下的 Windows 用户，不用苦恼！看看我的[JSP Windows 快速入门指南](https://www.sitepoint.com/blog/)！

#### 下载和安装 Apache 服务器

虽然有一些很棒的商业服务器可以用来测试 JSP 页面(包括 IBM WebSphere、BEA WebLogic 等)，但是对于那些只想增加一项新技能的普通 Web 开发人员来说，这些服务器可能是力所不及的。出于这个原因，我向 JSP 入门者推荐的服务器是 Apache Server。通过将它连接到 Jakarta 项目的 Tomcat 服务器(我们将在下一节安装它)，我们可以赋予它处理 JSP 页面的能力，以及像 Java Servlets 和 Enterprise JavaBean(EJB)这样的相关技术！

现在，如果您已经运行了 Apache(正如大多数 Linux 用户所做的那样)，您不必重新安装它。这对于已经在使用 Apache 运行 PHP 脚本或诸如此类的东西的人来说是个好消息，因为您将能够简单地向现有服务器添加 JSP 支持，而不会中断您已经设置的任何其他东西。

如果您还没有安装 Apache，现在是时候安装了。只需使用发行版的包管理器来安装 Apache。完成后，您可以跳到下一部分。如果您不确定如何使用您的发行版的软件包管理器，或者如果您更喜欢自己安装软件而不是让软件包管理器来安装，请继续阅读。

您需要去 Apache HTTP Server 项目的网站下载 Apache Server for Unix 的最新稳定源代码版本。截至本文撰写时，当前版本为 2.0.44(文件名为`httpd-2.0.44.tar.gz`)，可从以下地址下载:[http://www.apache.org/dist/httpd/](http://www.apache.org/dist/httpd/)。

下载完文件后，将它解压到您的主目录(或您认为方便的任何地方)以创建一个名为`httpd-2.0.44`的目录:

```
[kevin@sp kevin]$ **tar xfz httpd-2.0.44.tar.gz**  

[kevin@sp kevin]$ **cd httpd-2.0.44/**  

[kevin@sp httpd-2.0.44]$
```

注意，上面的`[kevin@sp kevin]$`部分代表命令提示符。你只需要输入 **`bold`** 所示的命令。

接下来，您需要配置 Apache，以便它可以用您需要的特性进行编译，并安装到您希望它安装的地方。下面的命令(应该在一行中输入)设置 Apache 安装在`/usr/local/apache2`中，并启用动态模块支持(我们需要与 Tomcat 接口)和`mod_rewrite`模块(这对本文来说并不重要，但这是您可能在某个时候需要的一个有用特性):

```
[kevin@sp httpd-2.0.44]$ **./configure --with-prefix=/usr/local/apache2**  

**--enable-so --enable-rewrite**
```

滚动所有诊断消息后，您应该返回到命令提示符。现在键入`make`命令来编译 Apache 服务器:

```
[kevin@sp httpd-2.0.44]$ **make**
```

又一次，一页页的消息将会滚动过去。当他们完成后，最后一步是安装 Apache。除非您在上面的主目录下指定了一个目标目录，否则您需要作为`root`用户登录(键入`su`来完成此操作)，然后才能继续:

```
[kevin@sp httpd-2.0.44]$ **su**  

Password: ************  

[root@sp httpd-2.0.44]# **make install**
```

安装了 Apache 之后，您可以通过以`root`的身份登录并使用`start`参数运行`/usr/local/apache2/bin`目录中的`apachectl`程序来运行它:

```
[root@sp /]# **/usr/local/apache2/bin/apachectl start**
```

打开网络浏览器，在地址栏中键入`http://localhost/`，然后按回车键。应该会出现一个底部带有 Powered by Apache 徽标的网页，说明 Apache 安装正确。祝贺您，您已经成功安装了 Apache！

##### 自动运行 Apache

显然，您不希望每次启动计算机时都必须手动启动 Apache。要让 Linux 在启动时启动 Apache，您需要在启动文件夹中放置一个 apachectl 程序的链接，用于您希望 Apache 运行的每个运行级(或操作系统模式)。

下面是将 Apache 设置为在运行级别 2、3、4 和 Linux 系统的所有正常操作模式)下自动运行，并在计算机关闭时关闭的一系列命令:

```
[root@sp /]# **cd /etc**  

[root@sp etc]# **ln -s /usr/local/apache2/bin/apachectl init.d/apache2**  

[root@sp etc]# **ln -s init.d/apache2 rc2.d/S91apache2**  

[root@sp etc]# **ln -s init.d/apache2 rc3.d/S91apache2**  

[root@sp etc]# **ln -s init.d/apache2 rc4.d/S91apache2**  

[root@sp etc]# **ln -s init.d/apache2 rc5.d/S91apache2**  

[root@sp etc]# **ln -s init.d/apache2 rc0.d/K20apache2**
```

应该可以了！为了进行测试，关闭您的计算机，再次启动它，看看您是否还能在`http://localhost/`访问 Apache 服务器。

#### 下载和安装 Tomcat

因为 Apache 不支持现成的 JSP，所以我们需要添加一些东西来提供这种支持。不幸的是，JSP 太复杂了，一个简单的 Apache 模块无法支持；因此，我们需要安装一个全新的程序来提供这种支持，然后指示 Apache 将对 JSP 页面的请求转发给该程序(注意:这听起来像 CGI，但实际上不是；就等着看吧)。我们将使用的程序叫做 Tomcat，也是由 Apache Group 编写的。

事实上，Tomcat 本身就是一个简单的 Web 服务器。然而，它不支持 Apache 的任何高级特性；那不是它的工作！通过将 Tomcat 和 Apache 链接在一起，您可以得到一个系统，它提供对 JSP 的完全支持(多亏了 Tomcat ),同时保持性能和可扩展性(PHP、Perl、SSL 等)。)的阿帕奇。

第一步应该是从 Sun 下载并安装 Java 2 软件开发工具包(Java 2 SDK)。这是运行 Tomcat(这是一个 Java 程序)和 Tomcat 能够编译 JSP 以供使用所必需的。撰写本文时，Java 2 SDK(也称为 JDK)的当前版本是 1.4.1_01，可以从这里[下载](http://java.sun.com/j2se/1.4.1/download.html)。下载~42MB 的“Linux 自解压文件”(一定要下载 SDK，而不是 JRE！).文件名应该是`j2sdk-1_4_1_01-linux-i586.bin`。使用这一系列命令将该文件解压到您的`/usr/local`目录中:

```
[kevin@sp kevin]$ **su**   

Password: ************   

[root@sp kevin]# **chmod u+x j2sdk-1_4_1_01-linux-i586.bin**   

[root@sp kevin]# **mv j2sdk-1_4_1_01-linux-i586.bin /usr/local/**   

[root@sp kevin]# **cd /usr/local**   

[root@sp local]# **./j2sdk-1_4_1_01-linux-i586.bin**
```

你现在可以删除`j2sdk-1_4_1_01-linux-i586.bin`文件，或者把它放在一个安全的地方以备将来使用；你已经玩完了。

Java 现在已经安装好了，但是您需要做一些事情来让它平稳地进入您的系统。首先，创建一个符号链接，这样您就可以使用更容易键入的名称`/usr/local/java`来访问 Java 安装:

```
[root@sp local]# **ln -s j2sdk1.4.1_01 java**
```

接下来，在`/usr/local/bin`目录中创建组成 Java SDK 的重要程序的链接，这样您就可以从任何地方运行它们:

```
[root@sp local]# **ln -s java/bin/java /usr/local/bin/java**   

[root@sp local]# **ln -s java/bin/javac /usr/local/bin/java**   

[root@sp local]# **ln -s java/bin/jar /usr/local/bin/java**
```

现在，我们需要在您的系统上编辑几个环境变量。仍然作为`root`，在您的首选文本编辑器中打开系统上的`/etc/profile`。查找以`PATH=`开头的行。当您在命令提示符下键入程序名时，此行将设置系统检查该程序的目录。检查以确保`/usr/local/bin`在列表中。如果不是，在现有的`PATH=`行之后添加以下行:

```
PATH="$PATH:/usr/local/bin"
```

您还需要设置`JAVA_HOME`环境变量来指向您的 Java 安装。为此，向下滚动到文件的底部，并将这两行添加到末尾:

```
JAVA_HOME="/usr/local/java"   

export JAVA_HOME
```

保存更改，注销，然后重新登录。Java 现在应该已经很好地安装并集成到您的系统中了。要测试它，您可以在命令提示符下键入`java -version`,并确保它正确地报告了您的 Java 版本。

现在您已经准备好安装 Tomcat 了。从[雅加达项目网站](http://jakarta.apache.org/)下载最新版本。截至本文撰写之时，最新版本是 4.1.18，可在`http://jakarta.apache.org/builds/jakarta-tomcat-4.0/release/v4.1.18/bin/`获得。文件应该叫`jakarta-tomcat-4.1.18-LE-jdk14.tar.gz`，大概 5.1MB。

下载完文件后，将其移动到`/usr/local`目录并解压缩:

```
[kevin@sp kevin]$ **su**   

Password: ************   

[root@sp kevin]# **mv jakarta-tomcat-4.1.18-LE-jdk14.tar.gz /usr/local/**   

[root@sp local]# **tar xfz jakarta-tomcat-4.1.18-LE-jdk14.tar.gz**
```

您现在可以删除`jakarta-tomcat-4.1.18-LE-jdk14.tar.gz`文件，或者将它放在安全的地方以备将来使用；你已经玩完了。

与 Java SDK 一样，为了方便起见，用一个更简洁的名称创建一个到安装目录的链接:

```
[root@sp local]# **ln -s jakarta-tomcat-4.1.18-LE-jdk14 tomcat**
```

和 Java 一样，Tomcat 需要设置自己的环境变量才能正常运行。再次打开您的`/etc/profile`文件，并将这两行添加到末尾:

```
CATALINA_HOME="/usr/local/tomcat"   

export CATALINA_HOME
```

再次保存您的更改，注销，然后重新登录以使更改生效。

您已经准备好启动 Tomcat 了！以`root`的身份登录，并键入以下命令来启动它:

```
[root@sp kevin]# **$CATALINA_HOME/bin/startup.sh**
```

如果一切顺利，`startup.sh`脚本应该会显示一些环境变量的值，然后 Tomcat 会在后台安静地启动。恭喜——Tomcat 已经开始运行了！

正如我之前所说的，Tomcat 提供了自己的简单 Web 服务器，我们可以用它来测试它是否正常工作。Tomcat 的 Web 服务器默认设置为使用端口 8080，因此打开您的 Web 浏览器并加载`http://localhost:8080/`。您应该会看到 Tomcat 的主页，上面有一些预先安装在服务器上的例子的链接。

如果您有兴趣，可以试一试这些例子，并注意 JSP 例子在您第一次使用时是如何花费大量时间来加载的，但是在随后的访问中却非常快。这是因为第一次访问 JSP 页面时，Tomcat 需要将其编译成 Java Servlet，这是一段纯 Java 代码，可以非常快速地运行以处理请求。然后，这个 Servlet 被保存在内存中，以极快的速度处理对同一个 JSP 的后续请求。

当你玩完 Tomcat 后，运行`shutdown.sh`脚本关闭它，就像你运行上面的`startup.sh`脚本一样(`$CATALINA_HOME/bin/shutdown.sh`)。Tomcat 将在一段时间后关闭。

##### 自动运行 Tomcat

如果您已经让 Apache 在系统启动时自动运行，那么您可能希望对 Tomcat 也这样做。从*复制*(不是链接)Tomcat 附带的`catalina.sh`脚本到您的`/etc/init.d/`目录开始:

```
[root@sp kevin]# **cd /etc**   

[root@sp etc]# **cp /usr/local/tomcat/bin/catalina.sh init.d/**
```

这个脚本依赖于我们之前设置的`JAVA_HOME`和`CATALINA_HOME`环境变量，但是这些变量在系统启动时并不存在。因此，我们必须稍微编辑一下脚本，以便它可以作为系统启动脚本。

在您最喜欢的文本编辑器中打开文件，向下滚动到开头注释后面的第一个空行(任何以`#`开头的行都是注释)。在该点添加这两行:

```
JAVA_HOME="/usr/local/java"   

CATALINA_HOME="/usr/local/tomcat"
```

这个脚本实际上试图检测`CATALINA_HOME`本身，但是如果脚本实际上不在 Tomcat 目录中(在本例中它不在目录中)，这个尝试就会失败。向下滚动并查找以`CATALINA_HOME=`开头的行。它应该是这样的:

```
CATALINA_HOME=`cd "$PRGDIR/.." ; pwd`
```

通过在该行的开头加上一个`#`将其注释掉:

```
#CATALINA_HOME=`cd "$PRGDIR/.." ; pwd`
```

这就完成了对`catalina.sh`的调整。现在，您可以在计算机的各种运行级别(操作模式)中将它指定为启动脚本:

```
[root@sp etc]# **ln -s init.d/catalina.sh rc2.d/S90tomcat**   

[root@sp etc]# **ln -s init.d/catalina.sh rc3.d/S90tomcat**   

[root@sp etc]# **ln -s init.d/catalina.sh rc4.d/S90tomcat**   

[root@sp etc]# **ln -s init.d/catalina.sh rc5.d/S90tomcat**   

[root@sp etc]# **ln -s init.d/catalina.sh rc0.d/K19tomcat**
```

应该可以了！要尝试一下，关闭您的计算机，再次启动它，看看您是否可以在`http://localhost:8080/`加载 Tomcat 主页。

#### 用`mod_jk`链接 Apache 和 Tomcat

既然 Apache 和 Tomcat 已经并行运行，那么您需要将它们链接在一起，这样 Apache 就可以通过将 JSP 请求交给 Tomcat 来处理它们。实际上有几种方法可以做到这一点，并且都涉及到安装 Apache 模块。第一种选择是使用`mod_jk`，它是专门为 Tomcat 3.x 设计的，可以与各种服务器一起工作，并使用一种相当高效的协议与 Tomcat 通信。

从 Tomcat 4.0 开始，一个链接 Apache 和 Tomcat 的新机制出现了:`mod_webapp`。这个 Apache 模块提供了 Apache 和 Tomcat 之间的有效通信，并且非常容易配置。不幸的是，负责`mod_webapp`的开发者对 Windows 有很强烈的反感，所以`mod_webapp`在那个平台上不能可靠地工作。与其在每个平台上使用不同的模块，我更喜欢在 Windows 和 Linux 上使用`mod_jk`。这样，我知道我已经有了一个可行的解决方案，不管我需要什么样的平台来完成一项特定的工作。

从 Tomcat 4.1 开始，`mod_jk`的一个改进的实现叫做`mod_jk2`。它被编写为更加模块化，提供更好的性能，并提高配置的简易性。不幸的是，关于`mod_jk2`的文档仍然有些粗略；因此，`mod_jk`是我们将在本文中安装的模块。

##### 难以捉摸的`mod_jk`

```
mod_jk comes in two parts: a connector plug-in for Tomcat and an Apache module. The connector plug-in is included with every copy of Tomcat 4.1\. The other half of the equation, the Apache module, is a different story.
```

因为 Apache 2.0 处于不断变化的状态，旧版本的`mod_jk`模块通常与新版本的 Apache 2.0 不兼容。一般来说，您需要一个在 Apache 2.0 发行版之后编译的`mod_jk`的副本，以便与它一起使用。Jakarta 团队总是试图在他们的网站上维护一个与最新的 Apache 2.0 版本兼容的`mod_jk`的可下载副本。

此时，`mod_jk`的最新版本可从[http://Jakarta . Apache . org/builds/Jakarta-Tomcat-connectors/JK/release/v 1 . 2 . 2/bin/Linux/i386/](http://jakarta.apache.org/builds/jakarta-tomcat-connectors/jk/release/v1.2.2/bin/linux/i386/)获得。该文件被命名为`mod_jk-2.0.43.so`,以表示它被编译为与 Apache 2.0.43 一起工作(它也将与 2.0.44 一起工作)。下载该文件，将其重命名为`mod_jk.so`，并将其复制到您的 Apache 2.0 安装(`/usr/local/apache2/modules`)的`modules`子目录中。

安装了 Apache 模块和服务器内置的 Tomcat 插件后，我们现在必须配置 Apache 和 Tomcat 使用`mod_jk`相互通信。

##### 配置 Tomcat

现在我们需要配置 Tomcat 来使用`mod_jk`。在 Tomcat 目录的`conf`子目录中找到`server.xml`文件，并在您选择的文本编辑器中打开它。

紧接着文件顶部附近的`<Server port="8005" ...>`标记，添加以下内容:

```
<Listener className="org.apache.ajp.tomcat4.config.ApacheConfig"    

  modJk="**/usr/local/apache2/**modules/mod_jk.so" jkDebug="info"    

  workersConfig="**/usr/local/tomcat/**conf/jk/workers.properties"    

  jkLog="**/usr/local/tomcat/**logs/mod_jk.log" /> 
```

如果路径不同，一定要用系统上的路径替换粗体的路径。

在文件进行到一半时，找到`<Host name="localhost" ...>`标记，并在它后面立即添加以下内容:

```
<Listener className="org.apache.ajp.tomcat4.config.ApacheConfig"     

  append="true" />
```

关闭`server.xml`，然后在同一个目录(`conf`)中创建一个名为`jk`的新目录。这个目录是我们创建上面第一个`<Listener>`标签中提到的`workers.properties`文件的地方。再次打开文本编辑器，键入(或复制)以下内容:

```
workers.tomcat_home=**/usr/local/tomcat**    

workers.java_home=$(JAVA_HOME)    

workers.th=$(workers.tomcat_home)    

ps=    

worker.list=ajp13, ajp14    

worker.ajp13.port=8009    

worker.ajp13.host=localhost    

worker.ajp13.type=ajp13    

worker.ajp13.lbfactor=1    

worker.ajp14.port=8010    

worker.ajp14.host=localhost    

worker.ajp14.type=ajp14    

worker.ajp14.secretkey=secret    

worker.ajp14.credentials=myveryrandomentropy    

worker.ajp14.lbfactor=1    

worker.loadbalancer.type=lb    

worker.loadbalancer.balanced_workers=ajp13    

worker.inprocess.type=jni    

worker.inprocess.class_path=$(workers.tomcat_home)$(ps)lib$(ps)tomcat.jar    

worker.inprocess.cmd_line=start    

worker.inprocess.jvm_lib=$(workers.th)$(ps)jre$(ps)bin$(ps)classic$(ps)jvm.dll    

worker.inprocess.stdout=$(workers.th)$(ps)logs$(ps)inprocess.stdout    

worker.inprocess.stderr=$(workers.th)$(ps)logs$(ps)inprocess.stderr
```

确保调整`tomcat_home`线(粗体显示)以匹配您的系统。在你新创建的`jk`目录中保存文件为`workers.properties`。

重新启动 Tomcat。如果你没有错过任何步骤，它应该会自动在它的`conf`目录下创建另一个名为`auto`的目录，里面有一个名为`mod_jk.conf`的文件。这个文件包含 Apache 使用 Tomcat 处理 JSP 所需要知道的一切。

##### 配置 Apache

因为 Tomcat 自动生成 Apache 的所有配置信息作为`mod_jk.conf`，所以我们需要做的就是将 Apache 指向那个文件！在文本编辑器中打开 Apache 2.0 配置文件(`/usr/local/apache2/conf/httpd.conf`)。向下滚动文件的 1/4，找到标题为`Dynamic Shared Object (DSO) Support`的部分。在这个标题下面出现的`LoadModule`行之后，添加下面一行(调整以匹配您的 Tomcat 目录):

```
Include "/usr/local/tomcat/conf/auto/mod_jk.conf"
```

将该行添加到`httpd.conf`中，保存您的更改并重新启动 Apache ( `apachectl graceful`)。现在，无论您加载`http://localhost:8080/examples/jsp/`(来自 Tomcat)，还是`http://localhost/examples/jsp/`(来自 Apache)，您都应该得到相同的页面。

#### 简单的 JSP

好吧，玩这些例子可能很有趣(剧透:你要找的两种颜色是黑色和青色)，但是真正的测试是建立一个你自己的 JSP 并让它运行。

打开记事本或您最喜欢的文本编辑器，键入以下内容:

```
<hr />This example brought to you by JSP and SitePoint.com!
```

将该文件保存为 Tomcat 的`webapps`目录下名为`sitepoint`的新子目录中的`theEnd.html`。在记事本中创建第二个新文件，并键入以下内容:

```
<%@ page language="Java" %>     

<html>     

<head>     

<title>A Simple JSP</title>     

</head>     

<body>     

<p>     

  <% String num = request.getParameter("num");     

     if (num == null) { // No number specified     

       // Display the form     

  %>     

       <form action="<%= HttpUtils.getRequestURL(request) %>">     

       What should I count to? <input type="text" size="2" name="num" />     

       <input type="submit" />     

       </form>     

  <%     

     } else for (int i=1; i<=Integer.parseInt(num); i++) {     

  %>     

       Counting: <%= i %><br />     

  <% }     

  %>     

</p>     

<%@ include file="theEnd.html" %>     

</body>     

</html>
```

将此作为`count.jsp`保存在新的`sitepoint`目录中，与`theEnd.html`放在一起。

现在，要使这两个文件可见，您需要创建一个 Java Web 应用程序来包含它们。这样做既好又简单。在新的`sitepoint`目录下创建一个名为`WEB-INF`的子目录。在那个子目录中，创建一个名为`web.xml`的文本文件，并在其中键入以下内容:

```
<?xml version="1.0" encoding="ISO-8859-1"?>     

<!DOCTYPE web-app     

    PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"     

    "http://java.sun.com/dtd/web-app_2_3.dtd">     

<web-app>     

</web-app>
```

保存该文件，然后重新启动 Tomcat 或重新启动您的系统，以使 Tomcat 能够识别新的 Web 应用程序。然后，您可以使用 URL `http://localhost:8080/sitepoint/count.jsp`通过 Tomcat 查看页面。

要使您的新 Web 应用程序通过 Apache 可见，只需重启 Apache。当您重启 Tomcat 时，它为 Apache 创建了一个新的自动配置文件；重新启动 Apache 将读入这个新的配置文件，并将您的新 Web 应用程序添加到队列中。每当您向 Tomcat 添加新的 Web 应用程序时，您都需要执行这个“重启 Tomcat，重启 Apache”过程；幸运的是，这种情况不常发生。

一旦 Apache 恢复运行，打开`http://localhost/sitepoint/count.jsp`。这就是你在 Apache 中的第一个工作 JSP！

##### 从这里去哪里？

如果您以前使用过 PHP 或其他脚本语言，那么上面这个简单的例子可能会让您觉得有点乏味。“就这样？”你可能会问。“我大费周章，只是为了做一些用 PHP 可以在 30 秒内完成的事情？”当然不是！作为一个简单的例子，上面没有使用 JSP 的任何更强大的特性，其中大部分需要对要使用的 Java 编程语言有相当透彻的理解。

因此，如果您还没有学习 Java，那么您的第一步应该是学习 Java。有很多好书(查看下面的相关文章，查看我最喜欢的)，但是如果你很急或者预算有限，我的文章【Java 入门和它的后继者应该能让你开始。

一旦您熟悉了 Java(我并不是要贬低它——这需要一些工作),您就可以将注意力转向 Java Servlets，然后是 JavaServer Pages。我的文章， [Java Servlets](http://www.webmasterbase.com/article/509) ，应该能让你对前者有所了解；至于后者，现在 SitePoint.com 上有一个 8 部分的教程系列，JSP 文件[，可以教你所有你需要知道的。尽情享受吧！](https://www.sitepoint.com/blog/)

祝你好运！如果你在途中遇到任何困难，[SitePoint.com 论坛](http://www.sitepointforums.com/)的成员会在这里帮助你！如果所有这些都失败了，你可以通过本文顶部的电子邮件链接联系我。

## 分享这篇文章