# Dropwizard 入门

> 原文：<https://www.sitepoint.com/tutorial-getting-started-dropwizard/>

Dropwizard 是一个用 Java 构建 [RESTful](https://www.sitepoint.com/what-does-restful-really-mean/) web 服务的框架。在本教程中，我们将看看如何通过从头构建一个新的服务来开始开发一个 Dropwizard 应用程序。这篇文章将涵盖最基本的内容。目标是实现并运行一个能够响应 HTTP GET 请求的 Dropwizard 应用程序。

在简要介绍了 Dropwizard 和我们的应用程序域之后，我们将看到如何创建一个新项目，讨论它的结构，并配置它。稍后，在最终构建和运行您的应用程序之前，我们将实现一个简单的 RESTful API。

示例项目可以在 [GitHub](https://github.com/indrekots/events-service "Events service GitHub repository") 上获得。关键的提交被标记，我会在每一个主要的改变后引用它们。如果你觉得有些东西不清楚，希望看到整个项目的快照，请随意[查看](https://git-scm.com/docs/git-checkout "Git checkout documentation")引用的标签。

您需要安装的唯一先决条件是 Maven、JDK 和您选择的文本编辑器或 IDE。

## 什么是 Dropwizard？

如前所述，Dropwizard 是一个用 Java 构建 RESTful web 服务的框架。本质上，它是一个*胶合*框架，将流行的、经过实战检验的 Java 库和框架捆绑在一起，使构建新的 RESTful web 服务变得更加容易。

下面是 Dropwizard 使用的一些库的不完整列表。

*   [Jersey](https://jersey.java.net/)—[JAX-RS](https://jax-rs-spec.java.net/)的参考实现，Java 平台中 RESTful web 服务的 API
*   [Jackson](http://wiki.fasterxml.com/JacksonHome)–用于处理 JSON 数据的库
*   [Jetty](https://eclipse.org/jetty/)–HTTP 服务器和 Java Servlet 容器
*   [指标](http://metrics.dropwizard.io/)–用于捕获 JVM 和应用程序级指标进行监控的库
*   [番石榴](https://github.com/google/guava)–实用程序库
*   [日志返回](http://logback.qos.ch/)–日志库

## 介绍我们的演示应用领域

为了更好地理解 Dropwizard，我们将从头开始构建一个 RESTful web 服务。这将是一个假设事件应用程序的后端。假设你搬到了一个新的城市，想要计划你的夜晚。该应用程序将根据您的搜索条件和位置列出活动。后端应该能够提供一个事件列表。还应该可以添加新的事件和更新现有的事件。为了简单起见，我们现在将排除用户身份验证和授权。

## 项目结构

我将使用 [Maven](https://maven.apache.org/) 作为构建工具。在我看来，开始使用一个新应用程序最简单的方法是使用 [`dropwizard-archetype`](https://github.com/dropwizard/dropwizard/tree/master/dropwizard-archetypes) 创建一个项目。[原型](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html "Maven Archetype")本质上是一个从模板中引导 Maven 项目的工具。

在您的终端中运行以下命令，开始一个新的 Dropwizard 项目。

```
mvn archetype:generate \
	-DarchetypeGroupId=io.dropwizard.archetypes \
	-DarchetypeArtifactId=java-simple \
	-DarchetypeVersion=1.0.2 
```

截至 2016 年 10 月，Dropwizard 的最新版本是 1.0.2。请务必检查最新的[版本](https://mvnrepository.com/artifact/io.dropwizard/dropwizard-core)，并相应地更新`archetypeVersion`。运行`archetype:generate`命令会提示您输入一些项目特定的字段(例如，groupId、artifactId)。输入项目名称时，请记住该字段用于生成应用程序和配置类。使用 CamelCase，不要插入空格，以确保生成的类名是有效的。

例如，我将应用程序命名为*事件*。因此，生成的类将被命名为`EventsApplication`和`EventsConfiguration`。如果我使用 *events-app* 作为名称，就会生成`event-appApplication`和`event-appConfiguration`。不幸的是，这些是无效的类名，编译器会指出来。

成功完成后，这就是生成的文件夹结构的样子。

```
.
├── config.yml
├── pom.xml
├── README.md
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── mydomain
    │   │           ├── api
    │   │           ├── cli
    │   │           ├── client
    │   │           ├── core
    │   │           ├── db
    │   │           ├── EventsApplication.java
    │   │           ├── EventsConfiguration.java
    │   │           ├── health
    │   │           └── resources
    │   └── resources
    │       ├── assets
    │       └── banner.txt
    └── test
        ├── java
        │   └── com
        │       └── mydomain
        │           ├── api
        │           ├── client
        │           ├── core
        │           ├── db
        │           └── resources
        └── resources
            └── fixtures 
```

应用程序配置存储在`config.yml`中。我们将在下一段更详细地讨论它。大部分肉类位于`src/main/java`，包括前面提到的两个等级——`EventsApplication`和`EventsConfiguration`。

以下是哪些文件应该放入哪个文件夹的概述:

*   这里是你的应用程序表示，本质上是你的 API 的实体。它们被序列化和反序列化为 JSON。
*   `cli`–包含可以从命令行执行的命令。本教程不涉及这些内容。
*   `client`–API 的 HTTP 客户端实现。为了方便使用您的 API，您可以实现一个客户端并将其放在这里。我们不会在本教程中这样做。
*   这是您实现领域逻辑的地方。
*   `db`–与数据库访问相关的类。
*   `health`–包含健康检查。这些是运行时测试，用于验证您的应用程序是否运行正常。
*   `resources`–泽西岛资源。

我们不打算使用所有这些包，因为我想保持教程更容易的一面。Dropwizard 对包的命名方案并不严格。如果您愿意，您可以重命名和重组包层次结构，以更好地满足您的需求。

通过检查`starting-point`标签可以看到初始的项目结构。

![Published by Jared Tarbell under CC-BY 2.0](img/4f30fcccb781ea26a8441dcda8d9ff7e.png)

[发表](https://www.flickr.com/photos/generated/6035308729/ "Source on Flickr")由[贾里德·塔贝尔](https://www.flickr.com/photos/generated/ "Author on Flickr")在[下 CC-BY 2.0](https://creativecommons.org/licenses/by/2.0/ "Link to Licence")

## 应用程序配置

在配置文件中存储可能随执行环境而变化的变量被认为是一种好的做法。当运行您的应用程序时，Dropwizard 期望接收一个配置文件作为命令行参数。默认情况下，配置文件应该写在 [YAML](http://yaml.org/ "Official YAML website") 中。解析的配置字段用于构建配置类的新实例。

现在，让我们尽可能简单地保存配置文件。我想补充两点。首先，让我们更改应用程序的根路径，这样所有的 REST 端点都将从`/api/*`开始被服务。`rootPath`参数是 Dropwizard 提供的众多[内置配置参数](http://www.dropwizard.io/1.0.2/docs/manual/configuration.html "Example configuration file with build-in configuration parameters")中的一个，所以我们不需要修改任何代码就可以使用它。

其次，因为我们在处理事件，所以我们关心开始时间，它可以用多种方式表示。让我们添加一个日期格式配置参数，它使我们能够从配置文件控制输出日期格式。

首先，修改`config.yml`并添加以下配置。

```
server:
  rootPath: /api/*
dateFormat: yyyy-MM-dd'T'HH:mmZ 
```

当然，我们需要从正在运行的应用程序中访问定制配置参数`dateFormat`。当 Dropwizard 启动时，配置字段被映射到配置类的成员字段。

因此，让我们编辑我们的配置类并包含`dateFormat`字段。打开`EventsConfiguration`并添加以下行:

```
@NotEmpty
private String dateFormat;

public String getDateFormat() {
    return dateFormat;
} 
```

应用程序不应该相信外部配置文件是正确的。为了帮助验证其正确性，我们可以使用 [Hibernate 验证器](http://hibernate.org/validator/ "About Hibernate Validator")。`@NotEmpty`注释确保字符串不为空。否则，Dropwizard 将无法启动。

请记住，这并不检查日期格式模式实际上是否有效。它只检查是否在配置文件中定义了它。您可以创建自己的定制验证器，如果模式无效，它会在启动时打印出一条漂亮的错误消息。为了简单起见，我现在不打算处理这个问题。

签出`app-configuration`标签以查看配置更改后的项目。

## 应用程序类别

这就是生成的应用程序类的样子。

```
public class EventsApplication extends Application<EventsConfiguration> {
    public static void main(final String[] args) throws Exception {
        new EventsApplication().run(args);
    }

    @Override
    public void run(EventsConfiguration configuration, Environment environment) {
    }

    @Override
    public void initialize(Bootstrap<EventsConfiguration> bootstrap) {
    }
} 
```

当启动服务时，调用`EventsApplication`类中的`main`方法。这是 Dropwizard 应用程序的入口点。一个新的`EventsApplication`对象被创建，它的`run`方法被调用。`run`方法用于配置 Dropwizard 的环境。

这是应用我们的应用程序中使用的日期模式的地方。为此，我们配置了 Jackson 的`ObjectMapper`，它稍后会将请求和响应参数从 Java 类序列化(反序列化)到 JSON，反之亦然。让我们修改它的日期格式，方法是显式创建一个新的`SimpleDateFormat`实例，并将配置对象中的模式传递给它。

```
@Override
public void run(EventsConfiguration configuration, Environment environment) {
    DateFormat eventDateFormat = new SimpleDateFormat(configuration.getDateFormat())
    environment.getObjectMapper().setDateFormat(eventDateFormat);
} 
```

稍后我们将修改`run`方法来注册新的*资源*。

application 类还有一个我们需要研究的方法，尽管我们不打算在其中添加任何代码:`initialize`用于完成应用程序的引导阶段。这里你可以添加[包](http://www.dropwizard.io/1.0.2/docs/manual/core.html#bundles)和[命令](http://www.dropwizard.io/1.0.2/docs/manual/core.html#commands)等。捆绑包是一组可重用的功能，可以把它们看作是应用程序的附加组件。命令可用于创建可从命令行访问的附加功能。

签出`date-format-configuration`标签，查看应用程序类中更改后的项目。

## 创建域对象的表示

表示是 API 的实体。它们*代表*资源的当前或期望状态。例如，当客户机向*事件*资源发出 HTTP GET 请求时，服务器发回一个事件当前状态的表示。表示可以序列化为任何格式，但是在本教程中我们将使用 JSON。

让我们考虑一下要在事件表示类中包含哪些字段。事件应该有一个名称。此外，我们需要知道事件发生的时间和地点。宿主可能希望添加有关事件的附加信息。这需要一个描述字段。最好也包含一个 id，因为我们希望确保每个事件都是唯一可识别的。

在`com.mycompany.api`中创建一个新的`Event`类(或者您正在使用的任何包命名方案)。

```
public class Event {
    private long id;
    private String name;
    private String description;
    private String location;
    private Date date;

    //getters and setters omitted
} 
```

在大多数情况下，用一个字符串来表示一个位置可能是不够的。您可以创建一个`Location`类来保存更详细的信息(例如纬度、经度)。Dropwizard 将确保嵌套对象被正确序列化。

创建新的制图表达类后，检查`event-representation`标签以查看项目。

## 创建简单的资源

在幕后，Dropwizard 使用 [Jersey](https://jersey.java.net/) 构建 RESTful web 服务。球衣是[JAX-RS](https://jax-rs-spec.java.net/)([JSR 311](https://jax-rs-spec.java.net/)&[JSR 339](https://jcp.org/aboutJava/communityprocess/final/jsr339/index.html))的参考实现。在实现资源方面做了很多工作，我不打算让这篇文章成为对 Jersey 的全面概述。如果您希望了解更多信息，请随意[浏览 Jersey 的文档](https://jersey.java.net/documentation/latest/getting-started.html)。

要开始使用 Jersey，让我们为事件创建一个资源。此外，我们需要定义一个 URL，用于访问资源和检索其当前状态。

让我们创建一个非常简单的事件资源实现。目标是公开一个用于检索所有事件的 URL。现在我们将返回虚拟数据。在`resources`包中创建一个名为`EventResource`的新类。

```
@Path("events")
@Produces(MediaType.APPLICATION_JSON)
public class EventResource {

    @GET
    public List<Event> allEvents() {
        Event e = new Event();
        e.setDate(new Date());
        e.setName("Birthday");
        e.setId(1L);
        e.setDescription("Please be on time!");
        e.setLocation("221B Baker Street");

        return Collections.singletonList(e);
    }
} 
```

`@Path`注释指定了这个资源的 URL 根。在下一行中，我们指定了表示格式。表示类被序列化为 JSON 并返回给客户端。`EventResource`目前的状态只有一种方法:`allEvents`。它用它应该响应的 HTTP 动词进行了注释。因此，举例来说，当 Dropwizard 收到对`/events`的 GET 请求时，`allEvents`就会被调用。换句话说，GET 用于检索资源当前状态的表示。

在该方法中，一个新的`Event`对象被虚拟数据实例化。事件对象被包装在一个列表中，然后被返回。为了方便起见，我使用集合类中的 [`singletonList`](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#singletonList-T-) 。手动创建一个新的列表并将元素添加到列表中也能很好地工作。

要在资源创建后查看项目，请查看`event-resource`标签。

## 注册资源

默认情况下，Dropwizard 不知道资源类，我们需要明确地告诉它要公开哪些资源。从技术上讲，资源需要注册到 Dropwizard 的 Jersey 环境中。如前所述，资源注册可以在`EventsApplication` class' `run`方法中完成。

将其修改为如下所示。

```
@Override
public void run(EventsConfiguration configuration, Environment environment) {
    DateFormat eventDateFormat = new SimpleDateFormat(configuration.getDateFormat())
    environment.getObjectMapper().setDateFormat(eventDateFormat);

    EventResource eventResource = new EventResource();
    environment.jersey().register(eventResource);
} 
```

通过检查`register-resource`标签可以看到项目的当前状态。

## 构建应用程序

要运行 Dropwizard 应用程序，首先需要构建它。建议您[将您的应用程序构建为 *fat JAR 文件*](http://www.dropwizard.io/1.0.2/docs/getting-started.html#building-fat-jars) 。这是一个 JAR 文件，包含项目中的类和它所依赖的所有库，所以应用程序需要的所有东西都被打包到一个文件中，这使得部署和运行变得很容易。同一个工件可以从测试升级到产品，同时确保在使用的库中没有版本变化。

如果你按照这篇文章，用 [`dropwizard-archetype`](https://github.com/dropwizard/dropwizard/tree/master/dropwizard-archetypes) 创建了一个新项目，那么 Maven 已经被配置为使用 [maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/ "Apache Maven Shade Plugin") 构建一个胖 JAR 文件。

只需在项目的根目录(`pom.xml`所在的文件夹)中运行以下命令。

```
mvn package
```

## 运行应用程序

现在可以如下执行 jar 文件。

```
java -jar target/events-0.0.1-SNAPSHOT.jar server config.yml 
```

为了成功执行，我们需要将两个命令行参数传递给 Dropwizard。第一个是`server`，它指示 Dropwizard 启动一个 HTTP 服务器并运行服务。第二个参数是服务器命令所需的配置文件。

如果应用启动时没有任何错误，将你的浏览器(或命令行 http 工具，如 [curl](https://curl.haxx.se "command line tool and library for transferring data with URLs") )指向`http://localhost:8080/api/events`。服务器应该返回包含以下消息体的有效响应。

```
[{"id":1,"name":"Birthday","description":"Please be on time!","location":"221B Baker Street","date":"2016-10-03T12:09+0200"}] 
```

## 摘要

到目前为止，您应该对 Dropwizard 有了一个基本的了解。借助现有库，Dropwizard 使开发人员能够轻松创建新的 RESTful web 服务。

我们还有很多没讲的。首先，我们为应用程序设置的要求没有得到满足。此时，只有虚拟数据返回给客户端。我们将在后续文章中向事件服务添加额外的功能，并深入研究本文中未涉及的 Dropwizard 部分。

## 分享这篇文章