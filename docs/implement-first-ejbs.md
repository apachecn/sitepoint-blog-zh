# 企业 Java Beans，入门:第 2 部分，实现您的第一个 EJB

> 原文：<https://www.sitepoint.com/implement-first-ejbs/>

对于初学 J2EE 的开发人员来说，最难的一步是构建他们的第一个 EJB。本文将抛开这些恐惧，帮助您探索 EJB 开发的世界。

如果您还不确定 EJB 是什么，或者为什么您应该考虑将它们用于特定的项目，请务必阅读本入门的第 1 部分。

##### 安装 J2EE 软件开发工具包

为了构建和运行 EJB，您必须安装一个符合 J2EE 标准的 EJB 容器。参考实现 EJB 容器可以在可从 Sun 获得的 J2EE 开发工具包中找到。你可以从 http://java.sun.com/j2ee 下载最新版本。

J2EE 开发工具包依赖于 Java 2 SDK，标准版(J2SE)开发工具包，所以您也需要下载它。你可以在 http://java.sun.com/j2se/index.html 找到它。您需要 J2SE SDK 来运行 J2EE 服务器，并构建和运行 J2EE 应用程序。

下载开发工具包后，安装它们并为每个工具包选择所需的位置。定义一个名为 JAVA_HOME 的环境变量，并将其指向 J2SE 开发工具包的安装目录。接下来，定义一个名为 J2EE_HOME 的环境变量，并将其指向 J2EE 开发工具包的安装目录。

现在，你可以开始写你的 EJB 了。

##### 编写会话 Bean

开发一个 EJB 涉及到您在开发普通旧 Java 对象(POJOs)时已经熟悉的大部分相同的步骤和概念，除了一些小的不同。以下步骤说明了开发和部署 EJB 的典型过程:

1.  为您的企业 bean 编写类和接口
2.  编写部署描述符
3.  将企业 bean 和相关文件打包到一个 jar 文件中
4.  部署 bean

在我们进入编写 bean 的血淋淋的细节之前，让我们再看一下两种不同类型的会话 bean。

***有状态会话 Beans 的概述***

有状态会话 bean 在单个会话期间维护与一个客户端的会话状态。这意味着有状态会话 bean 可以在单个会话期间跨来自一个客户端的多次调用维护实例变量。

一旦客户端完成了与企业 bean 的交互，并且 EJB 容器删除了企业 bean，bean 的会话就结束了，并且 bean 的所有状态数据都被丢弃。

***无状态会话豆***

无状态会话 bean 不会为每个单独的客户端维护会话状态。无状态会话 bean 的每次调用都应该被视为对一个全新对象实例的请求，因为任何实例变量状态都会在调用之间丢失。

无状态会话 beans 不会被 EJB 容器保存到二级存储中；因此，程序员必须认识到，对于每个客户机，所有数据在调用之间都是短暂的。无状态会话 bean 的瞬态特性允许 EJB 容器重用 bean 实例，因此通常会优化 bean 的性能。

*会话 Bean 关系*

图 1 展示了 EJB 客户端、EJB 容器和几个会话 beans 之间的关系:

![1250_architect](img/d3085088c342a0e11e6dc1c33a5f52b7.png)
图 1

会话 bean 至少由三个必需的类/接口组成。第一个强制类/接口是组件接口，它必须扩展 javax.ejb.EJBObject .我们将实现一个非常简单的无状态会话 bean，它从一个名为“retrieveMessage”的方法返回一个短字符串消息。

我们的组件接口代码如下:

```
package com.jeffhanson.ejb;  

import javax.ejb.EJBObject;  

import java.rmi.RemoteException;  

public interface SimpleMessenger extends EJBObject  

{  

  public String retrieveMessage() throws RemoteException;  

}
```

第二个必需的类/接口是 Home 接口，它必须扩展 javax.ejb.EJBHome。

```
package com.jeffhanson.ejb;  

import java.rmi.RemoteException;  

import javax.ejb.CreateException;  

import javax.ejb.EJBHome;  

public interface SimpleMessengerHome extends EJBHome  

{  

  SimpleMessenger create() throws RemoteException, CreateException;  

}
```

第三个必需的类/接口是企业 Bean 类，它必须实现 javax.ejb.SessionBean 接口。我们的企业 Bean 类的代码如下:

```
package com.jeffhanson.ejb;  

import java.rmi.RemoteException;  

import javax.ejb.SessionBean;  

import javax.ejb.SessionContext;  

public class SimpleMessengerBean  

  implements SessionBean  

{  

  private SessionContext sessionCtx = null;  

  public String retrieveMessage()  

  {  

      return "Hello from SimpleMessengerBean ";  

  }  

  public void ejbCreate()  

  {  

  }  

  public void ejbRemove()  

  {  

  }  

  public void ejbActivate()  

  {  

  }  

  public void ejbPassivate()  

  {  

  }  

  public void setSessionContext(SessionContext sessionCtx)  

  {  

    this.sessionCtx = sessionCtx;  

  }  

}
```

##### 编译会话 Bean

使用 javac 编译器以与 POJO 相同的方式编译 EJB，并且与 POJO 一样，您必须确保在您的类路径中找到适当的类文件。EJB 类文件是 javax.ejb 包的一部分，可以在 j2ee 参考实现的安装路径的 lib 目录下的 j2ee.jar 文件中找到。

以下命令行将编译我们为 EJB 定义的类和接口:

```
javac -classpath <J2EE_HOME>/lib/j2ee.jar -d <output directory> *.java
```

您必须将`<J2EE_HOME>`和`<output directory>`分别替换为您安装 J2EE 参考实现的路径和您想要放置新编译的类文件的目录。

为了让 EJB 和 EJB 客户端进行通信，必须生成 EJB 通信协议使用的代理类，通常是远程方法调用(RMI)。这些代理类在客户端称为存根，在 EJB 端称为骨架。为了生成存根和框架，我们必须通过一个实用程序来运行我们的类和接口，该实用程序将为我们计划使用的特定协议生成通信细节。因为我们将使用 RMI 作为通信协议，所以我们可以使用 J2SE 安装附带的 rmic 工具。以下命令说明了如何使用该工具来编译我们的远程类:

```
<JAVA_HOME>binrmic.exe -d <output directory> -classpath <JAVA_HOME>jrelibrt.jar;<J2EE_HOME>libj2ee.jar -sourcepath <source directory> com.jeffhanson.ejb.SimpleMessengerHome com.jeffhanson.ejb.SimpleMessenger
```

##### 部署会话 Bean

一旦编写并编译了会话 bean 的源代码，就必须将其部署到 EJB 容器的环境中。这包括描述您的 EJB 类型、您想要的交易类型、EJB 的本地类、EJB 的远程类以及其他信息。部署描述符用于包含这些描述。

***部署描述符***

要将 EJB 部署到 EJB 容器的环境中，必须向 EJB 容器提供一个部署描述符文件。部署描述符文件是一个名为 ejb-jar.xml 的 XML 文档，它指定了关于 bean 的信息，比如它的持久性类型和事务属性。EJB 的类文件和部署描述符必须打包到 JAR 或 EAR 文件中。

以下是我们的会话 bean 的部署描述符的示例:

```
<?xml version="1.0" encoding="UTF-8"?>   

<ejb-jar>   

  <description>A simple session bean</description>   

  <display-name>Simple Message EJB</display-name>   

  <enterprise-beans>   

    <session>   

      <ejb-name>SimpleMessenger</ejb-name>   

      <home>com.jeffhanson.ejb.SimpleMessengerHome</home>   

      <remote>com.jeffhanson.ejb.SimpleMessenger</remote>   

      <ejb-class>com.jeffhanson.ejb.SimpleMessengerBean</ejb-class>   

      <session-type>Stateless</session-type>   

      <transaction-type>Bean</transaction-type>   

    </session>   

  </enterprise-beans>   

</ejb-jar>
```

如果这看起来像是许多需要大惊小怪的细枝末节，不要绝望；我们马上会找到一个有用的工具来减轻我们的工作。与此同时，让我们看看我们如何准备好部署 EJB 的环境。

***启动 EJB 集装箱***

一旦编写并编译了 EJB，就必须将其部署到 EJB 容器的环境中。这通常意味着将 EJB 安装到应用服务器的环境中。我们将把会话 bean 部署到 J2EE 参考实现的服务器附带的 EJB 容器的环境中。

要启动 J2EE 参考实现的服务器，运行脚本文件 j2ee.bat(在 Unix/Linux 上是 j2ee.sh ),它位于<j2ee_home>bind 目录中。这将启动服务器侦听端口 8000 上的 HTTP 请求。要测试服务器，将您的 HTML 浏览器指向 http://localhost:8000。您应该会看到类似于图 2 中页面的内容。</j2ee_home>

![1250_javaserver](img/4a50ad88e940387a0029a72e98d90088.png)
图二

***使用部署实用程序***

我们将使用随 J2EE 参考实现一起安装的部署实用程序 deploytool 来打包和部署我们的 EJB。要启动 deploytool，请运行在 <j2ee_home>bin 目录中找到的 deploytool.bat 或 deploytool.sh 脚本文件。这将在 GUI 模式下启动 deploytool，并允许我们直观地看到我们的 EJB 被打包和部署。deploytool 的主页如图 3 所示。</j2ee_home>

![1250_deploytool](img/996b47735496ac57bb78f0815afda02f.png)
图 3

***创建 J2EE 应用***

为了将 EJB 部署到应用服务器的环境中，您必须为 EJB 创建一个应用程序。要在部署工具中创建新的 J2EE 应用程序:

*   选择文件->新建->应用程序菜单项。
*   单击 Browse 按钮，找到要创建应用程序的目录。现在，选择 <j2ee_home>public_html 目录。</j2ee_home>
*   在“文件名”框中输入 EJBTest，单击“新建应用程序”按钮，然后单击“确定”按钮。

现在我们已经创建了我们的应用程序，我们可以打包我们的会话 bean 并将其添加到应用程序中。

***包装企业 Bean***

我们将使用 deploytool 来打包我们的 EJB，并将其添加到我们的 EJBTest 应用程序中。

*   在部署工具中，选择文件->新建->企业 Bean…菜单项。这将启动新建企业 Bean 向导。
*   在 Introduction 页面上点击 Next 按钮，并确保选择 Create New JAR File in Application 单选按钮，并选择 EJBTest 作为应用程序。在“JAR 显示名称”框中，输入 SimpleMessenger。
*   按下内容文本框右侧的编辑按钮。这将启动一个新的对话框。在这个对话框中，导航到包含您的类文件的目录，选择所有。类文件，然后按 Add 按钮。然后按下确定按钮。

这会给您留下一个类似于图 4 的页面。

![1250_wizjar](img/498d6d526434357aa876a71382129857.png)
图 4

现在，按下一个按钮。在下一页:

*   选择“会话”单选按钮和“无状态”单选按钮。
*   在“企业 Bean 类”框中选择 SimpleMessengerBean，在“远程主接口”框中选择 SimpleMessengerHome，在“远程接口”框中选择 SimpleMessenger。这会给您留下一个类似图 5 的页面。

![1250_wizgeneral](img/75395ae3928e893aa0dca6f3d06c2986.png)
图 5

此时，按下“下一页”按钮，然后按下一页上的“完成”按钮。

***部署 J2EE 应用***

一旦打包了 EJB 并将其添加到 EJBTest 应用程序中，就可以将 EJB 部署到应用服务器中。以下步骤描述了如何在 deploytool 中实现这一点:

*   在 deploytool 的导航窗格中选择 EJBTest 应用程序。选择“JNDI 名称”选项卡，在“JNDI 名称”字段中输入 SimpleMessenger，然后按 enter 键。
*   选择工具->部署。。。菜单项。在第一页上，确保要部署的对象设置为 EJBTest，目标服务器设置为 localhost。选择“返回客户端 Jar”复选框，然后按“下一步”按钮。
*   在“JNDI 名称”页面上，单击“下一步”按钮，然后单击“完成”按钮。
*   在“查看”页面上，按“完成”按钮。
*   部署过程将开始，您将能够看到进度。部署过程完成后，按 OK 按钮。

现在，可以在 <j2ee_home>public_html 目录中查找两个 jar 文件；EJBTest.jar 和 EJBTestClient.jar。</j2ee_home>

您的 EJB 现在已经部署完毕，可以从客户端访问了。我们将在另一篇文章中讨论从客户端应用程序访问 EJB 的机制。

##### 摘要

对于初学 J2EE 的开发人员来说，最难的一步是构建他们的第一个 EJB。本文试图减轻这些恐惧，并帮助您进入 EJB 开发的世界。

为了构建和运行 EJB，您必须安装一个符合 J2EE 标准的 EJB 容器。参考实现 EJB 容器可以在 Sun 的 J2EE 开发工具包中找到。

开发一个 EJB 涉及到您在开发普通旧 Java 对象(POJOs)时已经熟悉的大部分相同的步骤和概念，除了一些小的不同。开发和部署 EJB 的步骤包括:

1.  为您的企业 bean 编写类和接口
2.  编写部署描述符
3.  将企业 bean 和相关文件打包到一个 jar 文件中
4.  部署 bean

会话 bean 至少由三个必需的类/接口组成:组件接口、Home 接口和企业 Bean 类。

使用 javac 编译器，以与 POJO 相同的方式编译 EJB，但是，为了 EJB 和 EJB 客户端进行通信，必须生成 EJB 通信协议(通常是 RMI)使用的代理类。这些代理类在客户端称为存根，在 EJB 端称为骨架。

一旦编写并编译了会话 bean 的源代码，就必须将其部署到 EJB 容器的环境中。这包括描述您的 EJB 类型、您想要的交易类型、EJB 的本地类、EJB 的远程类以及其他信息。部署描述符用于包含这些描述。部署描述符文件是一个名为 ejb-jar.xml 的 XML 文档，它指定了关于 bean 的信息，比如它的持久性类型和事务属性。

一旦你写好了你的 EJB，编译好了你的 EJB，你必须把它打包并部署到 EJB 容器的环境中。这通常是指将 EJB 部署到应用服务器的环境中。J2EE 参考实现的 deploytool 对于创建应用程序以及打包和部署 EJB 非常有用。

## 分享这篇文章