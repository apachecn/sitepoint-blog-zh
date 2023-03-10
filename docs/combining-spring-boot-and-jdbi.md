# 结合 Spring Boot 和 JDBI

> 原文：<https://www.sitepoint.com/combining-spring-boot-and-jdbi/>

我最近发现自己正在设计另一个微服务 REST 后端。在过去的几年里，我用 [Spring](https://projects.spring.io/spring-framework/) 、[drowizard](http://www.dropwizard.io/)甚至用 Python 和 [web.py](http://webpy.org/) 从头开始编写后端，但是每一个都让我意犹未尽。在本文中，我将展示我如何以及为什么选择 Spring Boot 和 JDBI 作为框架的组合，这使我能够轻松地开发由关系数据存储支持的 REST 服务。

## REST 框架

让我们比较一下今天可以用来构建 REST 服务的一些现代 REST 框架。有一些明显的遗漏(例如 [Ruby on Rails](http://rubyonrails.org/) 、 [Flask](http://flask.pocoo.org/) 、 [Django](https://www.djangoproject.com/) 、 [NodeJS](https://nodejs.org/en/) )，但是我将把重点放在我个人用来构建重要的生产级服务层的框架上。

### 春天

现代微服务后端的祖父级产品， [Spring](https://projects.spring.io/spring-framework/) 有很棒的[依赖注入(DI)](https://en.wikipedia.org/wiki/Dependency_injection) 和一个很大的扩展插件库，但是与一些较新的微服务框架相比，它往往有很长的启动时间和[感觉相当沉重和企业化](http://samatkinson.com/why-i-hate-spring/)。Spring.io 最近发布了一篇好文章，提出了一些他们从开发人员那里听到的关于 Spring 框架的担忧。

它还以 ORM 层的形式提供了对 Hibernate 的开箱即用支持，以我的经验来看，这种支持(尽管在评论中向我发难)经常会成为障碍。它非常适合简单的关系结构，并且可以快速引导解决方案，但是一旦您处理复杂的数据结构或者想要特定类型的查询(比如为了效率)，它就变得非常麻烦。

### 下拉向导

[Dropwizard](http://www.dropwizard.io/1.0.2/docs/) 本质上是一个建立在轻量级 [Jersey](https://jersey.java.net/) 容器上的 REST 层，它为作为 ORM-light 解决方案的 [JDBI](http://jdbi.org/) 提供了强大的支持。我发现 JDBI 是一种与数据库交互的简单方式。是的，您必须手动将您的`ResultSet`绑定到您的模型对象，但我发现这是为它提供的巨大灵活性付出的小小代价，它允许我生成我想要的精确查询，而不必依赖(并信任)ORM 自动生成的 SQL。在我看来，这是一个非常好的解决方案，介于 JDBC 和成熟的 ORM 之间。

Dropwizard 做的不好的是 DI。通过像 [dropwizard-guice](https://github.com/HubSpot/dropwizard-guice) 这样的扩展来支持 Guice，但是我发现这些充其量是笨拙的。我对所有这些解决方案的最大问题是，你最终为每个模块提供了单独的 DI 图。对于简单的应用程序来说，这不是问题，但是我在复杂的应用程序中经历过，drowizard-guice 变得非常笨拙，单独的 DI 图最终会将相同的类注入不同的实例/状态，这可能会产生意想不到的结果。

我真的只是想要开箱即用的 DI，它易于使用并且不碍事。这应该是管道，而不是应用程序功能。

### web.py

很明显，其中一个和另一个不一样。我包含了 [web.py](http://webpy.org/) ,因为我在其中写了一个全功能的 rest 层，如果你的信仰是 Python，那么这是一个不错的选择。如果你真的在考虑一个基于 Java 的框架，那么这显然不是你喜欢的。

### Spring Boot

Spring Boot 是 Spring 的一个轻量级版本，致力于创建类似 Spring 的应用程序。如果你刚到 Spring Boot，在 [Spring Boot 参考指南](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)中有一些优秀的[入门指南](http://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-introducing-spring-boot)。

尽管 Spring 可能让人感觉很企业化，但 Spring Boot 感觉和表现都像一个真正的一流微服务框架，因为它的学习曲线很快，启动很快。它对 DI 的支持和 Spring 一样强大，并且拥有一个丰富的 maven 依赖库，可以用来增加对很多东西的支持。

然而，它仍然使用 [Hibernate](http://hibernate.org/) 作为它的“原生”ORM 层。如果你觉得没问题，那就去吧，Spring Boot 开箱即用是一个很好的解决方案，但对我来说，我只是觉得 Hibernate 太麻烦了。

## Hibernate vs JDBI

我已经说过 Hibernate“碍事”，JDBI 是“一个非常好的解决方案”，这需要进一步的讨论来引出几点。首先，有许多 Hibernate 应用程序在规模上运行良好(我已经开发了许多)，然而，有一种趋势是远离繁重的 ORM 解决方案，原因如下:

*   Hibernate 会产生大量不必要的查询，即使是由于一些小的错误配置(例如，这里的[和这里的](http://stackoverflow.com/questions/5442023/hibernate-issuing-too-many-queries-to-mysql)和[只是举例说明)。当正确配置时，它仍然](http://stackoverflow.com/questions/26596501/excessively-verbose-queries-generated-by-hibernate)[比简单的 SQL 查询](http://www.polepos.org/)表现差。
*   Hibernate 对你的数据结构固执己见。当你有一个不遵循键和关系概念的数据结构时,[对象-关系阻抗不匹配](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)会对你造成更大的打击。*混合 ORM* 框架，比如 JDBI，在映射到对象时允许更大的灵活性，因此在你必须开始编码解决这个问题的重要工作之前提供了更多的跑道。
*   在您扩展之前，您看不到性能问题！当你的应用程序很小很简单的时候，一切看起来都很顺利，当你扩展的时候，突然 hibernate 查询成了你性能问题的根源。我自己也遇到过很多次这种情况，总是需要大量的返工来解决问题。

这篇 quora 帖子是一个很好的讨论，它强化了我上面提出的一些观点，也触及了其他一些观点。请注意，我在这里并不是要讨厌 Hibernate。这是一个很好的工具，如果配置正确的话，在很多情况下都可以有[很好的性能](https://www.sitepoint.com/5-reasons-to-use-jpa-hibernate/#5caching)(我个人曾经用 Hibernate 作为 ORM 编写过高规模的解决方案)。

所以这肯定不是一个对或错的讨论，我也不想暗示 JBDI 是万恶之源。但是它是 Hibernate 这种“笨重”ORM 的替代品，可以让你的开发人员生活更轻松。我个人遇到过许多科技公司，他们很乐意在 Dropwizard 中使用 JDBI，而不愿意回到使用 Hibernate。

那么一个精明的软件工程师应该怎么做呢？

![bee](img/b96d211daf8c1358ac7aad8551512fbe.png)

## 集成 Spring Boot + JDBI

我既想要 Spring Boot DI 的优点，又想要一个使用 JDBI 的简单一致的层，所以我创建了 [spring-boot-jdbi-seed](https://github.com/dhagge/spring-boot-jdbi-seed) 。GitHub 上的这个项目具有完整的 rest 层(注意:没有安全性，这是另一篇博文的内容)以及集成测试、Checkstyle、FindBugs 等，如果你愿意，你可以将它用作下一个项目的脚手架。在这里，我想特别强调一下，为了将 Spring Boot 和 JDBI 集成起来，我必须做些什么。

### build.gradle

首先，我必须去掉那些 hibernate 库，所以我在我的`build.gradle`文件中排除了它们:

```
configurations {
    all*.exclude group: "org.hibernate", module: "hibernate-entitymanager"
    all*.exclude group: "org.apache.tomcat", module: "tomcat-jdbc"
} 
```

作为交换，我添加了我需要的依赖项:

```
dependencies {
    compile(
            ...
            "org.springframework.boot:spring-boot-starter-jdbc",

            "org.springframework.boot:spring-boot-starter-data-jpa",
            "org.jdbi:jdbi:2.71",
            "joda-time:joda-time:2.8.1",
            ...
    )

    runtime(
            "mysql:mysql-connector-java:5.1.38",
            "com.zaxxer:HikariCP:2.4.3"
    )
} 
```

我还添加了 [joda-time](http://www.joda.org/joda-time/) ，以允许`DateTime`对象被序列化到数据库或从数据库序列化。就个人而言，我更喜欢 Java 8 的日期和时间，因为`Interval`、`PeriodFormatter`或`PeriodType`在 Java-8 中不可用。

### 应用程序.属性

接下来，我向`application.properties`添加了数据访问属性配置。Spring Boot 很好地允许我们使用通用的`spring.datasource.*`属性，而不是任何特定于 jdbi 的属性。

```
spring.datasource.url=jdbc:mysql://localhost/services?createDatabaseIfNotExist=true
spring.datasource.username=root
spring.datasource.password=
spring.datasource.driver-class-name=com.mysql.jdbc.Driver 
```

请注意，我使用 MySQL 作为数据库，但它可以与您想要使用的任何驱动程序交换。

### 服务应用程序

接下来，我将 JVM 时区默认设置为 UTC，这样所有的`DateTime`对象都将被设置为 UTC。这大大有助于与数据库之间的序列化，并且不会意外地应用不应该应用的时区转换。其次，我们注册了 JodaModule，这样我们就可以序列化实例了。

```
@PostConstruct
public void postConstruct() {
    // set the JVM timezone to UTC
    TimeZone.setDefault(TimeZone.getTimeZone("UTC"));
}

@Bean
public Module jodaModule() {
    return new JodaModule();
} 
```

### 持久性配置

最后一步，我创建了 Spring Boot 配置，它将实际公开 JDBI 以及映射日期/时间对象。完整的代码可以在[PersistenceConfiguration.java](https://github.com/dhagge/spring-boot-jdbi-seed/blob/master/src/main/java/com/myco/PersistenceConfiguration.java)中找到，但我将在这里抽出关键代码:

```
@Configuration
public class PersistenceConfiguration {

    @Autowired
    DataSource dataSource;

    @Bean
    public DBI dbiBean() {
        DBI dbi = new DBI(dataSource);
        dbi.registerArgumentFactory(new DateTimeArgumentFactory());
        dbi.registerArgumentFactory(new LocalDateArgumentFactory());
        dbi.registerColumnMapper(new JodaDateTimeMapper());
        return dbi;
    }

    ...

    /**
     * DBI argument factory for converting joda DateTime to sql timestamp 
     */
    public static class DateTimeArgumentFactory 
            implements ArgumentFactory<DateTime> {

        @Override
        public boolean accepts(Class<?> expectedType, 
                               Object value, 
                               StatementContext ctx) {
            return value != null 
                    && DateTime.class.isAssignableFrom(value.getClass());
        }

        @Override
        public Argument build(Class<?> expectedType, 
                              final DateTime value, 
                              StatementContext ctx) {
            return new Argument() {
                    @Override
                    public void apply(int position, 
                                      PreparedStatement statement, 
                                      StatementContext ctx) throws SQLException {
                        long millis = value.withZone(DateTimeZone.UTC).getMillis();
                        statement.setTimestamp(
                                position, new Timestamp(millis), getUtcCalendar());
                }
            };
        }
    }

    /**
     * A {@link ResultColumnMapper} to map Joda {@link DateTime} objects.
     */
    public static class JodaDateTimeMapper 
            implements ResultColumnMapper<DateTime> {

        @Override
        public DateTime mapColumn(ResultSet rs, 
                                  int columnNumber, 
                                  StatementContext ctx) throws SQLException {
            final Timestamp timestamp = 
                    rs.getTimestamp(columnNumber, getUtcCalendar());
            if (timestamp == null) {
                return null;
            }
            return new DateTime(timestamp.getTime());
        }

        @Override
        public DateTime mapColumn(ResultSet rs, 
                                  String columnLabel, 
                                  StatementContext ctx) throws SQLException {
            final Timestamp timestamp = 
                    rs.getTimestamp(columnLabel, getUtcCalendar());
            if (timestamp == null) {
                return null;
            }
            return new DateTime(timestamp.getTime());
        }
    }

} 
```

## 自动点火并使用 jdbi

就这样，现在你可以自动连线并使用一个`DBI`对象来做 [JDBI](http://jdbi.org) 允许你做的任何事情。

```
@RestController
@RequestMapping("/test")
@Slf4j
public class TestResource {

    @Autowired
    DBI dbi;

    @RequestMapping(method = RequestMethod.GET)
    public String get() {
        String name;
        Handle handle = dbi.open();
        try {
            name = handle.createQuery("select description from my_test")
                    .map(StringMapper.FIRST)
                    .first();
        } finally {
            handle.close();
        }
        return name;
    }
} 
```

我个人倾向于使用 JDBI SQL Object API，我发现这是一种简单方便的数据查询方式，但是您可以阅读文档并使用适合您的方式。

## 运行代码

确保你的机器上安装了 [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 、 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 和 [MySQL](http://dev.mysql.com/doc/refman/5.7/en/installing.html) 。该项目包括一个 [gradle 包装器](https://docs.gradle.org/current/userguide/gradle_wrapper.html)，所以你不需要安装 gradle。

在命令行上执行以下命令:

```
# pull the code from git
git clone https://github.com/dhagge/spring-boot-jdbi-seed.git
# build the project, create the database schema and seed some test data
./gradlew build
./gradlew dbUpdate
./gradlew dbSeed 
```

现在您可以运行 Spring Boot 服务并调用 test REST 服务:

```
./gradlew bootRun

# now curl the service (from a new command prompt)
curl localhost:9090/test 
```

您将看到响应`Hello there, this is a test!`，它实际上是通过`TestResource`类从数据库中的`my_test`表获得的数据。

完整源代码可以在 [spring-boot-jdbi-seed](https://github.com/dhagge/spring-boot-jdbi-seed) 找到，有空请叉开使用！

## 摘要

Spring Boot 和 Dropwizard 都是很棒的微服务框架，但在我看来，它们都有不足之处。具体来说，Dropwizard 缺乏一流的依赖注入(DI)支持，Spring Boot 内置了 Hibernate，这可能不是满足您需求的最佳选择(至少对我来说通常不是)。我已经在上面展示了我们如何通过将 JDBI 与 Spring Boot 集成来结合两个世界的一些优点。我很高兴在一些重大项目中使用这种技术组合，我希望你也一样！

## 分享这篇文章