# 与 OSGi 一起打造精益模块化整体结构

> 原文：<https://www.sitepoint.com/building-a-lean-modular-monolith-with-osgi/>

### 目录 

*   [我们的领域:模块化消息应用](#ourdomainamodularmessagingapplication)
*   [通用项目设置和 OSGi 捆绑包](#commonprojectsetupandosgibundles)
*   [声明式服务](#declarativeservices)
*   [聊天 API](#thechatapi)
*   [外壳模块](#theshellmodule)
*   [IRC 模块](#theircmodule)
*   [补锅匠模块](#tinkerforgemodule)
*   [用 bndtools 包装](#packagingwithbndtools)
*   [一体式包装又称模块化整体包装](#theallinonepackagingakathemodularmonolith)
*   [运行应用程序](#runningtheapplication)
*   我们学到了什么？
*   [评论](#comments)

虽然微服务被大肆宣传，但著名专家警告不要以这种方式开始。相反，你可能想先建造一个[模块化整体](http://www.codingthearchitecture.com/presentations/sa2015-modular-monoliths)，如果考虑以后进入微服务，但还没有立即的需求，这是一个安全的赌注。本文向您展示了如何使用 OSGi 构建一个精简的整体，当这种风格成为适合应用程序扩展需求的解决方案时，它足够模块化，可以毫不费力地拆分为微服务。

创建良好模块化解决方案的一个非常好的策略是实现领域驱动设计(Eric Evans)。它已经关注于业务能力，并且具有提供必要模块化的有界上下文的概念。在本文中，我们将使用 OSGi 来实现服务，因为它为模块(包)和模块之间的轻量级通信(OSGi 服务)提供了良好的支持。正如我们将看到的，这也将为以后的微服务提供一个很好的途径。

这篇文章不需要预先了解 OSGi。我将解释相关的方面，如果你从这篇文章中了解到 OSGi 可以用来构建一个解耦的整体，为可能的微服务迁移做准备，那么它就实现了它的目标。您可以在 GitHub 上找到示例应用程序的源代码。

## 我们的领域:模块化消息传递应用程序

为了降低业务复杂性，我们将使用一个相当简单的例子——一个聊天应用程序。我们希望应用程序能够发送和接收广播消息，并在三个非常不同的通道中实现这一点:

*   外壳支架
*   irc 支持
*   使用基于 Tinkerforge 的显示器和运动检测器支持物联网

这些通道中的每一个都使用相同的接口来发送和接收消息。应该可以插入和拔出通道，并自动将它们相互连接。在 OSGi 术语中，每个通道将是一个捆绑包，并使用 OSGi 服务与其他通道进行通信。

如果您没有 Tinkerforge 硬件，请不要担心。显然，Tinkerforge 模块将不会工作，但它不会影响其他渠道。

## 通用项目设置和 OSGi 软件包

示例项目将使用 Maven 构建，并且大部分常规设置都在[父 pom](https://github.com/cschneider/osgi-chat/blob/master/pom.xml) 中完成。

OSGi 包只是带有增强清单的 JAR 文件，其中包含 OSGi 特有的条目。一个包必须声明它从其他包中导入哪些包，以及导出哪些包。幸运的是，这大部分是通过使用 [bnd-maven-plugin](https://github.com/bndtools/bnd/tree/master/maven/bnd-maven-plugin) 自动发生的。它分析 Java 源代码并自动创建合适的导入。导出和其他特殊设置在特殊文件`bnd.bnd`中定义。在大多数情况下，这个文件可以是空的，甚至可以被忽略。

下面的两个插件确保每个 Maven 模块创建一个有效的 OSGi 包。单个模块不需要在 pom 中进行特殊的 OSGi 设置——对于它们来说，引用这里正在构建的父 pom 就足够了。maven-jar-plugin 定义了我们想要使用来自 bnd 的`MANIFEST`文件，而不是默认的 maven 生成的文件。

```
<build>
    <plugins>
        <plugin>
            <groupId>biz.aQute.bnd</groupId>
            <artifactId>bnd-maven-plugin</artifactId>
            <version>3.3.0</version>
            <executions>
                <execution>
                    <goals>
                        <goal>bnd-process</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <version>2.5</version>
            <configuration>
                <archive>
                    <manifestFile>
                        ${project.build.outputDirectory}/META-INF/MANIFEST.MF
                    </manifestFile>
                </archive>
            </configuration>
        </plugin>
        <!-- ... more plugins ... -->
    </plugins>
</build> 
```

我们下面设计的每个模块都创建了一个 OSGi 包。每个模块的 POM 非常简单，因为大部分设置已经在父模块中完成，所以我们省略了它们。请看一下[OSGi 聊天项目](https://github.com/cschneider/osgi-chat)的来源，了解详情。

### 声明式服务

该示例使用[声明性服务(DS)](https://osgi.org/download/r6/osgi.cmpn-6.0.0.pdf) 作为依赖注入和服务框架。这是一个由 OSGi 规范定义的非常轻量级的系统，允许发布和使用服务以及消费配置。DS 非常适合 OSGi，因为它支持 OSGi 的全部动态，捆绑包和服务可以随时来来去去。DS 中的组件可以提供 OSGi 服务，并依赖于其他 OSGi 服务和配置。每个组件都有自己的动态生命周期，只有当所有必需的依赖项都存在时才会激活。它还将动态地适应服务和配置的变化，因此变化几乎是即时应用的。

由于 DS 负责处理依赖关系，开发人员可以专注于业务领域，而不必编写 OSGi 的动态代码。作为 DS 组件的第一个例子，参见下面的`ChatBroker`服务。在运行时，DS 使用 XML 文件来描述组件。bnd-maven-plugin 自动处理 DS 注释，并在构建过程中透明地创建 XML 文件。

## 聊天 API

在我们简单的聊天领域中，我们只需要一个服务接口`ChatListener`，来接收或发送聊天消息。一个`ChatListener`监听消息，想要接收消息的模块发布一个作为 OSGi 服务的`ChatListener`的实现来表示他们想要监听。这被称为[白板模式](http://enroute.osgi.org/doc/218-patterns.html)，并被广泛使用。

```
public interface ChatListener {

    void onMessage(ChatMessage message);

} 
```

`ChatMessage`是一个值对象，用于保存聊天消息的所有信息。

```
public class ChatMessage implements Serializable {

    private static final long serialVersionUID = 4385853956172948160L;

    private Date time;
    private String sender;
    private String message;
    private String senderId;

    public ChatMessage(String senderId, String sender, String message) {
        this.senderId = senderId;
        this.time = new Date();
        this.sender = sender;
        this.message = message;
    }

    // .. getters ..

} 
```

此外，我们使用一个`ChatBroker`组件，它允许向所有当前可用的监听器发送消息。这更像是一种便利的服务，因为每个通道都可以自己简单地实现这一功能。

```
@Component(service = ChatBroker.class, immediate = true)
public class ChatBroker {

    private static Logger LOG = LoggerFactory.getLogger(ChatBroker.class);

    @Reference
    volatile List<ChatListener> listeners;

    public void onMessage(ChatMessage message) {
        listeners.parallelStream().forEach((listener)->send(message, listener));
    }

    private static void send(ChatMessage message, ChatListener listener) {
        try {
            listener.onMessage(message);
        } catch (Exception e) {
            LOG.warn(e.getMessage(), e);
        }
    }

} 
```

`ChatBroker`被定义为使用 DS 注释的声明性服务组件。它将提供一个`ChatBroker` OSGi 服务，并在所有依赖项都存在时立即激活(默认情况下，DS 组件只有在它们的服务被另一个组件请求时才被激活)。

`@Reference`注释定义了对一个或多个 OSGi 服务的依赖。在这种情况下，`volatile List`标志着依赖关系是`(0..n)`。列表中自动填充了当前可用的`ChatListener`服务的线程安全表示。`send`方法使用 [Java 8 流](https://www.sitepoint.com/java-8-streams-filter-map-reduce/)并行发送给所有监听器。

在这个模块中，我们需要一个`bnd.bnd`文件来声明我们想要导出 API 包。事实上，这是我们在整个示例项目中对包创建进行的唯一调整。

```
Export-Package: net.lr.demo.chat.service 
```

### 外壳模块

shell 通道允许使用 [Felix Gogo Shell](http://felix.staging.apache.org/documentation/subprojects/apache-felix-gogo.html) 发送和接收聊天消息，这是一个命令行界面(很像 bash ),便于与 OSGi 通信。另见 Gogo shell 途中的 [appnote。](http://enroute.osgi.org/appnotes/gogo.html)

`SendCommand`类实现了一个 Gogo 命令，当在 shell 中键入命令`send <msg>`时，该命令向所有监听器发送一条消息。它宣称自己是一个具有特殊服务属性的 OSGi 服务。范围和功能定义了服务实现命令以及如何处理命令。我们命令的完整语法是`chat:send <msg>`，但是只要`send`是唯一的，它就可以缩写成`send <msg>`。

当 Felix Gogo 在 shell 上识别出一个命令时，它将调用带有该命令名称的方法，并将参数作为方法参数发送。在`SendCommand`的情况下，参数消息用于创建一个`ChatMessage`，然后将其发送给`ChatBroker`服务。

```
@Component(service = SendCommand.class,
    property = {"osgi.command.scope=chat", "osgi.command.function=send"}
)
public class SendCommand {

    @Reference
    ChatBroker broker;

    private String id;

    @Activate
    public void activate(BundleContext context) {
        this.id = "shell" + context.getProperty(Constants.FRAMEWORK_UUID);
    }

    public void send(String message) {
        broker.onMessage(new ChatMessage(id, "shell", message));
    }

} 
```

`ShellListener`类接收一个`ChatMessage`并将它打印到 shell 中。它实现了`ChatListener`接口，并将自己发布为一个服务，因此对于`ChatBroker`它将变得可见，并将被添加到它的聊天监听器列表中。当有消息进来时，调用`onMessage`方法并简单地打印到`System.out`，在 Gogo 中它代表 shell。

```
@Component
public class ShellListener implements ChatListener {

    public void onMessage(ChatMessage message) {
        System.out.println(String.format(
                "%tT %s: %s",
                message.getTime(),
                message.getSender(),
                message.getMessage()));
    }

} 
```

### IRC 模块

这个模块使用 [Apache Camel](http://camel.apache.org/) 连接到一个 IRC 通道，通过这个通道发送和接收消息。`IRCConnector`使用在 [OSGi 元类型规范 1.3](http://blog.osgi.org/2011/03/metatypes.html) 中定义的类型安全配置。这允许定义配置值的名称、类型和默认值。在运行时，这些由 Felix config admin 提供，并通过属性语法中的`.cfg`文件进行配置。在`activate`方法中，配置被赋予组件。

您可能会注意到 Camel 组件`irc`和`bean`有两个`@Reference`依赖项，在下面的代码中没有直接使用。这是一种变通办法，以确保我们等到组件被激活，因为 Apache Camel 没有与 DS 完全集成。

```
@Component(name = "connector.irc", immediate = true)
public class IRCConnector implements ChatListener {

    @Reference(target="(component=irc)")
    org.apache.camel.spi.ComponentResolver irc;

    @Reference(target="(component=bean)")
    org.apache.camel.spi.ComponentResolver bean;

    private OsgiDefaultCamelContext context;

    @Reference
    private ChatBroker broker;
    private ProducerTemplate producer;
    private String ircURI;

    @ObjectClassDefinition(name = "IRC config")
    @interface TfConfig {
        String nick() default "osgichat";
        String server() default "193.10.255.100";
        int port() default 6667;
        String channel() default "#osgichat";
    }

    @Activate
    public void activate(BundleContext bc, TfConfig config) throws Exception {
        context = new OsgiDefaultCamelContext(bc, new OsgiServiceRegistry(bc));
        ircURI = String.format(
                "irc:%s@%s:%d/%s",
                config.nick(),
                config.server(),
                config.port(),
                config.channel());
        context.addRoutes(new RouteBuilder() {
            public void configure() throws Exception {
                from(ircURI).bean(new ChatConverter()).bean(broker);
            }
        });
        context.start();
        producer = context.createProducerTemplate();
    }

    @Deactivate
    public void deactivate() throws Exception {
        context.shutdown();
    }

    public void onMessage(ChatMessage message) {
        if (!"irc".equals(message.getSenderId())) {
            try {
                producer.sendBody(ircURI, message.getMessage());
            } catch (CamelExecutionException e) {
                System.out.println(e.getMessage());
            }
        }
    }

} 
```

一旦依赖项和配置出现，OSGi 就调用`activate`方法。其中，使用 [Camel IRC 组件](http://camel.apache.org/irc.html)接收 IRC 消息的 Camel 上下文被实例化。这个路由是由`from(ircURI)`定义的，其中`ircURI`默认为`"irc://osgichat@193.10.255.100:6667/#osgichat"`，它打开一个到给定 IRC 服务器和通道的连接，并且将为从通道接收的每个消息调用这个路由。消息通过管道传输到一个`ChatConverter`，它将消息转换成我们的`ChatMessage`类型，然后发送到`ChatBroker`以便在我们的消息传递系统中传递。

在另一个方向，我们通过提供通常的`ChatListener`服务来监听`ChatMessages`。当消息到达`onMessage`时，它会通过`producerTemplate`被发送到另一条骆驼路线。在这个路由中，消息被简单地发送到同一个 IRC URI，它告诉 Camel 发送一个 IRC 消息到频道。

### Tinkerforge 模块

让我们通过添加一个与物联网设备交互的通道来使我们的应用程序更有趣一些。为此，我们使用了 [Tinkerforge 系统](http://tinkerforge.de)。它允许在不焊接的情况下试验物联网，还提供了一个与砖块守护程序通信的 Java 库，因此也不需要编写本机代码。

`TinkerConnect`组件创建并配置 Tinkerforge IPConnection，它与 brick 守护进程对话。

```
@Component(name = "tf",
        configurationPolicy = ConfigurationPolicy.REQUIRE,
        service = TinkerConnect.class)
@Designate(ocd = TinkerConnect.TfConfig.class)
public class TinkerConnect {
    private static Logger LOG = LoggerFactory.getLogger(TinkerConnect.class);
    private IPConnection ipcon;

    @ObjectClassDefinition(name = "Tinkerforge config")
    @interface TfConfig {
        String host() default "localhost";
        int port() default 4223;
    }

    @Activate
    public void activate(TfConfig config) throws Exception {
        ipcon = new IPConnection();
        ipcon.connect(config.host(), config.port());
    }

    @Deactivate
    public void deactivate() throws NotConnectedException {
        ipcon.disconnect();
    }

    IPConnection getConnection() {
        return ipcon;
    }
} 
```

`LCDWriter`是另一个`ChatListener`，它使用 TinkerConnect 服务连接到 LCD 显示器，并将所有的`ChatMessage`写入 LCD。完整的代码还支持消息缓冲区和滚动消息。在本文中，您可以找到要写入显示器的最少代码。

```
@Component
public class LCDWriter implements ChatListener {
    private BrickletLCD20x4 lcd;
    private DateFormat df;

    @Reference
    TinkerConnect tinkerConnect;

    @Activate
    public void activate() throws TimeoutException, NotConnectedException {
        this.df = DateFormat.getTimeInstance(DateFormat.MEDIUM, Locale.ENGLISH);
        IPConnection ipcon = tinkerConnect.getConnection();
        lcd = new BrickletLCD20x4("rV1", ipcon);
        lcd.backlightOn();
        lcd.clearDisplay();
        lcd.addButtonPressedListener((button) -> buttonPressed(button));
    }

    public void onMessage(ChatMessage message) {
        try {
            initlcd();
            lcd.clearDisplay();
            lcd.writeLine((short)0, (short)0, df.format(message.getTime()));
            lcd.writeLine((short)1, (short)0, message.getSender());
            lcd.writeLine((short)2, (short)0, message.getMessage());
            if (message.getMessage().length() > 20) {
                lcd.writeLine((short)3, (short)0, message.getMessage().substring(20));
            }
        } catch (Exception e) {
            // Ignore
        }
    }

} 
```

因为我们没有完整的 ASCII 输入设备，所以每当检测到运动时，我们就使用运动检测器来发送消息。

```
@Component(immediate = true, service=MotionDetector.class)
public class MotionDetector {

    @Reference
    TinkerConnect tinkerConnect;

    @Reference
    ChatBroker broker;

    private BrickletMotionDetector motion;
    private MotionDetectedListener listener;

    @Activate
    public void activate() throws Exception {
        IPConnection ipcon = tinkerConnect.getConnection();
        motion = new BrickletMotionDetector("sHt", ipcon);
        listener = () -> {
            broker.onMessage(new ChatMessage("sensor", "sensor", "Motion detected"));
        };
        motion.addMotionDetectedListener(listener);
    }

    @Deactivate
    public void deActivate() throws Exception {
        motion.removeMotionDetectedListener(listener);
    }

} 
```

![A modular monolith with OSGi can be a gateway to microservices](img/7203719f0cc5b4ad2a46b29721d3311a.png)

## 用 bndtools 打包

OSGi 需要一个单独的包装声明进行部署。这比常规的 Java 项目更复杂，因为 OSGi 完全是关于松耦合的。单个模块的 POM 通常只依赖于 API，所以它们通常不包含足够的信息来声明完整的 OSGi 设置。

对于 OSGi 的运行时打包，我们需要一个包列表以及容器和包的附加配置。可以“手动”指定包列表，但是这非常繁琐并且容易出错。更好的方法是以 OSGi 包存储库(OBR)索引的形式提供候选包列表，并使用 OSGi 解析器来确定要使用的实际包。

在我们的例子中，我们使用一个 [POM 文件](https://github.com/cschneider/osgi-chat/blob/master/packaging/index/pom.xml)来定义索引。在这个文件中，我们定义了包和其他索引 POM 上的 Maven 依赖关系。像 Aries RSA 这样的一些 OSGi 项目已经提供了这样的索引 POM，这使得添加它们变得非常容易。在构建期间，使用 bnd-indexer-plugin 创建一个 OBR 索引，它包含所有包的元数据。该数据最重要的部分是每个包的需求和功能列表。

```
<plugin>
    <groupId>biz.aQute.bnd</groupId>
    <artifactId>bnd-indexer-maven-plugin</artifactId>
    <version>3.2.0</version>
    <configuration>
        <localURLs>REQUIRED</localURLs>
    </configuration>
    <executions>
        <execution>
            <id>index</id>
            <goals>
                <goal>index</goal>
            </goals>
        </execution>
    </executions>
</plugin> 
```

为了解析这些包，我们使用 [bndtools](http://bndtools.org/) 。它是一个用于 OSGi 开发的 Eclipse 插件，也支持 Maven 插件来完成像解析包这样的任务。在 bndtools 中，部署由一个 [`bndrun`文件](https://github.com/cschneider/osgi-chat/blob/master/packaging/all/chat-all.bndrun)定义。这个文件包含了一些通用的配置，比如 OSGi 框架，以及一个指向索引和初始需求列表的指针。该文件可以手工创建，也可以使用`bndrun` Eclipse 编辑器创建。最初的需求列表只是索引中顶层包的列表。

```
-runrequires: \
osgi.identity;filter:='(osgi.identity=org.apache.felix.metatype)',\
osgi.identity;filter:='(osgi.identity=org.apache.felix.fileinstall)',\
osgi.identity;filter:='(osgi.identity=org.ops4j.pax.logging.pax-logging-service)',\
osgi.identity;filter:='(osgi.identity=org.apache.felix.gogo.command)',\
osgi.identity;filter:='(osgi.identity=org.apache.felix.gogo.shell)',\
osgi.identity;filter:='(osgi.identity=net.lr.demo.chat.command)',\
osgi.identity;filter:='(osgi.identity=net.lr.demo.chat.lcd)',\
osgi.identity;filter:='(osgi.identity=net.lr.demo.chat.irc)' 
```

前 5 个需求描述了配置管理和日志记录以及 Gogo shell 的基础设施。对于我们的实际应用程序，我们只需要为通道添加包。它们的所有依赖关系都是根据包和后备索引的要求解决的。

然后，通过从这些初始包开始工作并添加满足其需求所需的所有包来完成自动解析。结果是一个 runbundles 列表，它围绕需求形成了一个闭包，从而定义了一个有效的部署。

## 一体式包装又名模块化整体

在项目 *packaging-all* 中，我们用所有通道的包定义了一个`bndrun`文件。解析过程将自动添加所需的库，以支持 camel-core 和 camel-irc 包等通道。由于所有通道都在一个进程中运行，所以我们不需要任何远程处理——这些通道可以使用普通的 OSGi 服务进行通信。

在这种部署中，几乎没有通信开销，可以像对任何其他 java 应用程序一样进行调试。由于这种部署非常容易管理，因此只要可行，您就应该继续使用它。

### 运行应用程序

首先我们构建项目——这也将创建可运行的 jar。然后我们可以发射`chat-all.jar`。

```
mvn clean install

cd packaging/all
java -jar target/chat-all.jar 
```

这将运行一体化包装。chat-all.jar 包含 OSGi 框架以及所有的运行时包。配置放在`etc`目录中。

JAR 启动时应该没有错误，并生成一个显示提示的 Felix Gogo shell。在运行应用的同时，打开 freenode irc 上的 [#osgichat 频道。过了一会儿，一个用户 *osgichat* 应该会加入进来。](https://webchat.freenode.net/?channels=osgichat)

现在我们首先从 OSGi 应用程序向 IRC 发送一条消息。

```
g! send Hi there 
```

消息应该显示在 shell 和 IRC 通道上。

现在在 IRC 频道上发送消息。这条消息同样应该显示在 Gogo shell 上。结束进程和外壳类型`Ctrl-D`。

## 我们学到了什么？

在本文中，我们学习了如何以模块化的方式构建应用程序，而不从一开始就引入微服务的复杂性。我们基于有限的上下文(在这种情况下是不同的聊天技术)定义了单独的包，并将它们与 OSGi 服务松散地耦合在一起。然后我们创建了一个单一的部署单元(deployment monolith)，它易于部署和调试。

在第二篇文章中，我们将展示如何将这个应用分成两个微服务并部署它们。

## 分享这篇文章