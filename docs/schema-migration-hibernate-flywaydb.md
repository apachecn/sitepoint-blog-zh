# 使用 Hibernate 和 FlywayDB 进行模式迁移

> 原文：<https://www.sitepoint.com/schema-migration-hibernate-flywaydb/>

本教程解释了使用自动化模式迁移工具(如 FlywayDB)的好处，以及 Hibernate 模式生成实用程序( *hbm2ddl* )的局限性。本文假设几乎没有 Hibernate 知识，初级和高级开发人员都可以阅读。

## 介绍

在关系数据库中，数据存储在表行中，关系通过外键表示。虽然数据库负责存储和检索数据，但业务逻辑通常嵌入在应用层。当使用面向对象的编程语言时，对象图成为数据最自然的表示。图状结构和数据库元组之间的差异通常被称为[对象关系阻抗不匹配](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)。

[对象关系映射(ORM)](https://en.wikipedia.org/wiki/Object-relational_mapping) 模式旨在解决这一问题，而 [Hibernate ORM](http://hibernate.org/) 无疑是这一模式最成功的实现之一。然而，Hibernate 不仅仅是一个符合 [Java 持久性 API](http://www.oracle.com/technetwork/java/javaee/tech/persistence-jsp-140049.html) 规范的 ORM 框架。

Hibernate 是一个实现许多模式的数据访问框架，Martin Fowler 在他的书[企业应用架构的模式](https://www.amazon.com/gp/product/0321127420)中推广了这些模式。例如，除了典型的对象关系映射特性，Hibernate 还实现了以下数据访问模式:

*   [工作单位](https://vladmihalcea.com/2014/07/30/a-beginners-guide-to-jpahibernate-entity-state-transitions/)
*   身份地图
*   懒人加载
*   [乐观和悲观锁定](https://vladmihalcea.com/2015/01/12/a-beginners-guide-to-java-persistence-locking/)
*   [跨越多个 web 请求的应用级对话](https://vladmihalcea.com/2014/09/22/preventing-lost-updates-in-long-conversations/)

## 数据模型质量

虽然 ORM 工具允许我们将对象图状态变化转换成 SQL 语句，但是我们仍然管理两个不同的数据模型。一方面，数据库模式定义了应该如何存储数据的规则，而在应用程序方面，域模型是底层数据库模式的对象图镜像。

那么，谁负责驱动模型表示呢？是数据库还是领域模型？

## 模式所有权

Hibernate 很久以前就提供了一个[模式生成工具](http://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#schema-generation)，它获取实体映射并将它们转换成实际的数据库模式。这个工具对于测试非常有用。例如，Hibernate 单元测试依赖它在运行测试套件之前生成一个新的数据库模式，只是在测试运行之后删除每个新创建的数据库结构。

除了创建和删除模式之外，该工具还提供了一个`update`选项，该选项可以生成一个 DDL 脚本，该脚本能够将旧的数据库模式迁移到当前实体结构给出的新版本。

然而，不建议在生产环境中使用 hbm2ddl 的自动模式生成，因为它不支持每个特定于数据库的 ddl 结构(如[部分索引](https://www.postgresql.org/docs/current/static/indexes-partial.html)、[用户定义类型](https://www.postgresql.org/docs/9.2/static/sql-createtype.html)、触发器、检查等)。).因此，一旦数据库模式利用了高级 DDL 特性，模式迁移也必须使用它们，这只能通过手动创建的增量脚本来完成。

> 模式所有权属于数据库，应用程序级的域模型映射到底层的数据库结构。

![schema-migration](img/483b50bb1a7a2ac148badd12ce0c32e2.png)

## 模式迁移

正如代码库的发展一样，数据库模式也在发展。事实上，生成数据库模式的脚本也是源代码，所有源代码都驻留在版本控制系统中，比如 [Git](https://en.wikipedia.org/wiki/Git) 。

数据库模式演变必须由增量模式迁移脚本捕获。每当数据库模式必须经历某种变化时，就添加一个新的模式迁移脚本。

拥有这些模式迁移脚本的好处在于，我们可以自动化整个应用程序部署。为此，我们需要一个工具来自动化数据库模式迁移脚本的部署过程。

在 Java 中，有两个自动模式迁移选项: [Liquibase](http://www.liquibase.org/) 和 [FlywayDB](https://flywaydb.org/) 。在本文中，我将向您展示在 Spring 和 Hibernate 企业应用程序中集成 FlywayDB 是多么容易。

首先，模式迁移脚本必须驻留在`src/main/resources`文件夹中:

```
resources
  flyway
    db
      postgresql
        migration
          V1_0__initial_script.sql
          V1_1__post_details.sql
          V1_2__post_comment.sql
          V1_3__post_tag.sql 
```

考虑到我们有一个使用 Hibernate 实现持久性的 Spring 框架应用程序，为了使用 FlywayDB，我们只需配置一个
`org.flywaydb.core.Flyway` Spring bean:

```
@Bean(initMethod = "migrate")
public Flyway flyway() {
    Flyway flyway = new Flyway();
    flyway.setDataSource(actualDataSource());
    flyway.setBaselineOnMigrate(true);
    flyway.setLocations(
        String.format(
            "classpath:/flyway/db/%1$s/migration",
            databaseType()
        )
    );
    return flyway;
} 
```

另一件要考虑的事情是，负责实例化 Java 持久性的`LocalContainerEntityManagerFactoryBean`必须在组件`Flyway`之后初始化*，这样那些迁移才能在引导 Hibernate 之前执行。*

```
@Bean
// depends on flyway bean, so that database initialization
// runs before Hibernate gets bootstrapped
@DependsOn("flyway")
public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
    // the usual initialization
} 
```

如果在当前的开发迭代中，我们意识到我们需要一个新的数据库模式迁移脚本:

```
CREATE TABLE users (id BIGINT NOT NULL, name VARCHAR(255), PRIMARY KEY (id)); 
```

我们只需将它添加到 Flyway 资源文件夹中:

```
resources
  flyway
    db
      postgresql
        migration
          V1_0__initial_script.sql
          V1_1__post_details.sql
          V1_2__post_comment.sql
          V1_3__post_tag.sql
          V1_4__users.sql 
```

当应用程序启动时，Flyway 将自动应用它:

```
INFO  : DbValidate - Validated 5 migrations (execution time 00:00.025s)
DEBUG : DbSchemas - Schema "public" already exists. Skipping schema creation.
DEBUG : Table - Locking table "public"."schema_version"...
DEBUG : Table - Lock acquired for table "public"."schema_version"
INFO  : DbMigrate - Current version of schema "public": 1.3
INFO  : DbMigrate - Migrating schema "public" to version 1.4 - users
DEBUG : SqlScript - Found statement at line 1: create table users (id bigint not null, name varchar(255), primary key (id))
DEBUG : SqlScript - Executing SQL: create table users (id bigint not null, name varchar(255), primary key (id))
DEBUG : DbMigrate - Successfully completed and committed migration of schema "public" to version 1.4
DEBUG : MetaDataTableImpl - MetaData table "public"."schema_version" successfully updated to reflect changes
DEBUG : Table - Locking table "public"."schema_version"...
DEBUG : Table - Lock acquired for table "public"."schema_version"
INFO  : DbMigrate - Successfully applied 1 migration to schema "public" (execution time 00:00.091s). 
```

从应用程序日志中，我们可以看到 Flyway 如何设法发现并应用新的模式迁移脚本，从而提升数据库模式版本。在后台，Flyway 使用一个单独的数据库表(例如`schema_version`)来记录它以前应用的每个脚本:

| 版本 | 描述 | 脚本 | 安装于 | 执行时间 | 成功 |
| --- | --- | --- | --- | --- | --- |
| One | 初始脚本 | V1_0__initial_script.sql | 2016-08-05 07:50:22.418753 | One hundred and twenty-six | t |
| One point one | 帖子详细信息 | V1_1__post_details.sql | 2016-08-05 07:50:22.729402 | One hundred and sixty-four | t |
| One point two | 发表评论 | V1_2__post_comment.sql | 2016-08-05 07:50:22.92953 | Ninety-seven | t |
| One point three | 发布标签 | V1_3__post_tag.sql | 2016-08-05 07:50:23.038776 | eight | t |
| One point four | 用户 | V1 _ 4 _ _ 用户 | 2016-08-05 07:51:35.315481 | Forty-three | t |

## 结论

虽然 Hibernate 提供的模式生成工具对于测试，甚至对于创建第一个初始脚本都很有用，但是从长远来看，使用 FlywayDB 这样的自动模式迁移工具要灵活得多。通过这种方式，我们可以利用特定于数据库的 DDL 语法提供的所有特性，只需要最少的配置。

如果你喜欢这篇文章，你可能也会对阅读我的书感兴趣。

## 分享这篇文章