# 云中的生命

> 原文：<https://www.sitepoint.com/liferay-in-the-cloud/>

## 为什么生活在云中？

如今，每个人都希望在云中完成事情。在云中运行应用程序让您不必管理和扩展硬件。数据库、应用服务器、持续集成等管理由提供商完成，让您专注于核心业务。有一件事你不会轻易发现，那就是如何在云中建立并运行 Liferay 门户。Liferay 本身不提供云服务，所以我们必须利用现有的提供商构建自己的云服务。当然，可以通过 IaaS 提供商生成一台机器，并从头开始安装一切以将 Liferay 部署到云中——但我们的目标是利用一个 PaaS 平台来部署 Liferay，并使之对您来说更容易。

对于本教程，我使用了 [Cloudbees](http://www.cloudbees.com/ "Cloudbees") 。有两种方法可以开始:常规方法和快速方法，后者通过最近增加的“点击开始”成为可能。后者显然是最容易的，但看看我们从哪里来也不错。它有助于理解潜在的过程。

## 在开始之前

在开始之前，我们应该安装 Cloudbees SDK。这里可以找到[。下载压缩文件，并解压。完成后，设置 BEES_HOME 环境变量，并指向解压 SDK 的文件夹。将 BEES_HOME 添加到 PATH 环境变量中。你可以打开一个命令窗口，输入“bees help”来检查你的安装。第一次，Cloudbees SDK 将尝试从其服务器下载配置文件。如果没有找到，它会要求您的 Cloudbees 登录和密码来创建一个。一旦完成，必要的插件被下载，你就可以开始了。](http://cloudbees-downloads.s3.amazonaws.com/sdk/cloudbees-sdk-1.2.1-bin.zip "here")

*注意:如果您使用代理，您可以编辑 bees.bat/bees.sh 文件并更改 JAVA_OPTS:*

`JAVA_OPTS=-Dbees.home=%BEES_HOME% -Dhttp.proxyHost=<your proxy host> -Dhttp.proxyPort=<your proxy port> -Xmx256m`

## “常规”方式

使用 Cloudbees 平台，按照这些简单的步骤在云中设置 Liferay。当然，你需要注册一个账户。

– Create Database (LiferayTryout)– Download [Liferay Tomcat bundle](http://sourceforge.net/projects/lportal/files/Liferay%20Portal/6.1.1%20GA2/liferay-portal-tomcat-6.1.1-ce-ga2-20120731132656558.zip "Liferay Tomcat Bundle")– Unzip it– Download [Unbundled Liferay](http://sourceforge.net/projects/lportal/files/Liferay%20Portal/6.1.1%20GA2/liferay-portal-6.1.1-ce-ga2-20120731132656558.war/download "Unbundled Liferay")– Copy all files from the Bundled version’s *tomcat-7.0.27/lib/ext* folder into the unbundled WAR’s *WEB-INF/lib folder* (14 items, although ccpp.jar is double and can be skipped). In windows, you can use 7Zip to browse the war-file, and drag and drop the jar files into the right folder– Create a *portal-ext.properties* file in the unbundled WAR’s *WEB-INF/classes* folder (again, you can create it outside the war, and drag and drop it in afterwards)– Update *portal-ext.properties*, to have following properties:

> portal-ext . properties liferay . home =/tmp/liferay-home-EB
> JDBC . default . driver class name = com . MySQL . JDBC . driver
> JDBC . default . URL = JDBC:MySQL://your databaseserver:3306/liferay-username？use unicode = true&character encoding = UTF-8&useFastDateParsing = false
> JDBC . default . username = liferay-username
> JDBC . default . password = liferay-password

–您可以在 Cloudbees 数据库的“管理”选项卡中找到您的数据库服务器
–Create App(JVM Web Application WAR)
–Liferay 需要相当大的内存。Cloudbees 免费版自带 128MB，可能不够用。你必须切换到付费(中型或大型)版本
——使用 Cloudbees SDK 上传新的 WAR 文件。这将需要一段时间，因为它相当大。后续部署会更快，因为 SDK 使用增量系统来仅部署更改。要进行部署，请使用以下命令:

`       bees app:deploy liferay-portal-6.1.1-ce-ga2.war jvmPermSize=256`

–注意，我们已经设置了参数*jvmpersize*。
–SDK 将询问您想要部署哪个应用程序。输入名称，并观察它的进展。
–您的 liferay 实例现在已经在云中运行了！

## 最快的方法，使用 Liferay 点击开始

*点击开始*是项目模板，有点像 Maven 原型，如果你曾经使用过的话。有一个 Liferay 点击开始模板，可以在这里找到:【https://github.com/CloudBees-community/liferay-clickstart T3

要使用此点击开始，并在云中拥有我们自己的 Liferay，请遵循以下步骤:

– Click the button “Deploy instantly on Cloudbees”– Choose name– Source repository (GIT) is initialized, Jenkins is configured to automatically build the code, a web application is created, DB created and configured– Be patient, this can take a while…– Again, you will have to increase your application memory to 512MB or 1024MB– Now, use the Cloudbees SDK to locally create the app

`       bees create <your-applicationname>`

– Go into the created folder– Change the source code if you want, or immediately upload the application again

`       bees deploy -a <your-username>/<your-applicationname>`

–您的应用程序现已启动并运行！

## 结论

As you can see, the ClickStart way is a lot quicker. On top of that, you also have the immediate creation of a source repository (GIT, in this case) and a Jenkins configuration. While the first method requires a lot of effort and some insight, the second one is quick and easy. We demonstrated use of ClickStarts and in process also deployed Liferay to cloud.

## 分享这篇文章