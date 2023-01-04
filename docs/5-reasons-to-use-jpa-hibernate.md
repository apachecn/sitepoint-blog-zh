# 使用 JPA / Hibernate 的 5 个理由

> 原文：<https://www.sitepoint.com/5-reasons-to-use-jpa-hibernate/>

在我们深入研究使用 JPA 的原因之前，让我快速解释一下它是什么。 [Java 持久性 API (JPA)](https://jcp.org/en/jsr/detail?id=338) 是 Java 中[对象关系映射](https://en.wikipedia.org/wiki/Object-relational_mapping)的规范。至于 [Java 社区进程](https://www.jcp.org/en/home/index)中的大多数标准，它是由不同的框架实现的。最受欢迎的是[冬眠](http://www.hibernate.org/)。

所有 JPA 实现都支持该规范定义的特性，并且经常使用定制功能对其进行扩展。这提供了两个主要优势:

1.  只要不使用任何专有特性，就可以快速切换 JPA 实现。
2.  不同的实现可以添加额外的功能，以比标准更快地创新。其中一些可能会在以后成为规范的一部分。

好了，理论到此为止。让我们先简单介绍一下 JPA，然后看看使用它的一些理由。

## JPA 入门

当然，不可能在短短的一节中全面深入地解释 JPA。但是我想给你看一个基本的用例，让你熟悉一般的概念。

让我们从 persistence.xml 文件开始。它的结构由 JPA 标准定义，它向持久性提供者提供配置，首先是数据库驱动程序和连接信息。您可以在下面的代码片段中看到一个简单的配置示例。

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<persistence  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" version="2.1" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd">
    <persistence-unit name="my-persistence-unit">
        <description>My Persistence Unit</description>
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
        <exclude-unlisted-classes>false</exclude-unlisted-classes>
        <properties>
            <property name="hibernate.dialect" value="org.hibernate.dialect.PostgreSQLDialect" />
            <property name="hibernate.generate_statistics" value="true" />

            <property name="javax.persistence.jdbc.driver" value="org.postgresql.Driver" />
            <property name="javax.persistence.jdbc.url" value="jdbc:postgresql://localhost:5432/test" />
            <property name="javax.persistence.jdbc.user" value="postgres" />
            <property name="javax.persistence.jdbc.password" value="postgres" />
        </properties>
    </persistence-unit>
</persistence> 
```

在配置了持久性提供者之后，就可以定义第一个实体了。下面的代码片段显示了一个简单的实体映射示例。

```
@Entity
public class Author {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id", updatable = false, nullable = false)
    private Long id;

    @Version
    @Column(name = "version")
    private int version;

    @Column
    private String firstName;

    @Column
    private String lastName;

    @ManyToMany(mappedBy="authors")
    private Set<Book> books = new HashSet<Book>();

    // constructors, getters/setters,
    // and everything else is as usual
} 
```

`@Entity`注释将`Author`类定义为一个实体。它被映射到一个同名的表，在本例中是`author`表。

`id`属性是实体和数据库表的主键。JPA 实现自动[生成主键值](http://www.thoughts-on-java.org/jpa-generate-primary-keys/?utm_source=sitepoint&utm_medium=post&utm_campaign=sitepoint-intro)，并使用版本属性进行乐观锁定，以避免同一数据库记录的并发更新。

`@Column`注释指定这个属性被映射到一个数据库列。类似于`@Entity`注释，它使用属性的名称作为默认的列名。

`@ManyToMany`注释定义了与另一个实体的关系。在本例中，它定义了与映射到另一个数据库表的`Book`实体的关系。

如您所见，您只需要添加一些注释来映射数据库表，并使用其他特性，如乐观锁定和主键生成。

![hibernate](img/8f9fc83d0a44659333923a88818d8dcb.png)

## 5 个原因

### 1.开发人员生产力

开发人员的生产力可能是 JPA 及其任何实现最常被提及的优势。这样做的主要原因是，您只需定义一次数据库表和域模型之间的映射，就可以将它用于所有的写操作和大多数读操作。除此之外，您还获得了许多额外的功能，否则您将需要自己实现这些功能，比如主键生成、并发管理和不同的性能优化。

但这只是 JPA 因其开发人员生产力而受欢迎的原因之一。它还提供了一个简单但非常有效的 API 来实现基本的 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 操作。您可以在下面的两个代码片段中看到一个例子。

在第一篇文章中，我向您展示了如何在数据库中持久化一个新的`Author`实体。

```
EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

Author a = new Author();
a.setFirstName("John");
a.setLastName("Doe");
em.persist(a);

em.getTransaction().commit();
em.close(); 
```

如您所见，您不需要做太多事情。本例中的第一行和最后两行是样板代码，您只需要为每个事务运行一次，就可以获得一个`EntityManager`并处理事务。如果您在 Java EE 容器或 Spring 应用程序中使用 JPA，您可以忽略这些行，因为您的框架会处理它们。

主要工作在第 4-7 行完成。我创建了一个`Author`实体的新对象，并调用 setter 方法来提供新作者的名和姓。然后我调用`EntityManager`接口上的 persist 方法，它告诉 JPA 实现生成一个 SQL `INSERT`语句并将其发送到数据库。

下一个例子的代码看起来类似。这一次，我想更新一个现有的作者。与前面的示例一样，代码片段的第一行和最后两行是样板代码，用于获取 EntityManager 并处理事务。这段代码中有趣的部分是第 4 行和第 5 行。在第 4 行，我使用了`EntityManager`的 find 方法，通过实体的主键获取实体。如您所见，我不需要为这个简单的查询编写任何 SQL。而且对于姓氏的更新也是一样的。您只需要调用您想要更改的属性的 setter 方法，您的 JPA 实现就会为它创建所需的 SQL `UPDATE`语句。

```
EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

Author a = em.find(Author.class, 1L);
a.setLastName("new last name");

em.getTransaction().commit();
em.close(); 
```

正如您所看到的，JPA 提供了一个易于使用的 API 来实现常见的 CRUD 用例，而无需编写任何 SQL。这使得常见用例的实现速度大大加快，但它也提供了另一个好处:您的 SQL 语句不会分布在您的代码中。这意味着您可以轻松地重命名数据库表或列。您唯一需要适应的是实体上的注释。

### 2.独立于数据库

如果您试图对不同的数据库使用相同的代码，很快就会遇到由不同的 SQL 方言引起的问题。SQL 是与数据库交互的标准语言，但每个数据库使用的方言略有不同。如果您的语句必须在不同的数据库上运行，这就成了一个大问题。

但是如果你使用 JPA 的话就不会了。它在 SQL 之上提供了独立于数据库的抽象。只要您没有使用任何本地查询，您就不必担心数据库的可移植性。您的 JPA 实现使每个 API 调用或 JPQL 查询中生成的 SQL 语句适应特定的数据库方言，并处理不同的特定于数据库的数据类型。

### 3.类型和参数处理

因为 JDBC 和 Java 数据类型并不完全一致，所以您必须找到正确的组合，并确保将它们作为查询参数提供。

如果你自己从来没有这样做过，这听起来可能很容易。但是如果你必须至少做一次，你就会知道很容易出错。更糟糕的是，它分散了实现业务逻辑的注意力，并且也是 SQL 注入漏洞的原因，这是 web 应用程序中最常见的安全问题之一。

避免这些问题并能够专注于业务逻辑的最佳方式是使用自动处理这些事情的框架或规范，如 JPA。

正如您在本文开头看到的，当您定义实体映射时，您不必定义任何 SQL 数据类型。您的 JPA 实现对代码隐藏了这些转换，并使用默认映射。

您的 [JPQL](https://en.wikipedia.org/wiki/Java_Persistence_Query_Language) 查询的参数处理采用了类似的方法。您只需在查询接口上设置参数，JPA 实现就会根据实体元数据来处理它。您可以在下面的代码片段中看到一个例子。

```
EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

TypedQuery<Author> q = em.createQuery("SELECT a FROM Author a WHERE id = :id", Author.class);
q.setParameter("id", 1L);
Author a = q.getSingleResult();

em.getTransaction().commit();
em.close(); 
```

### 4.避免不必要的查询

写后优化是 JPA 带来的几个性能优化之一。基本思想是尽可能延迟所有写操作，以便多个 update 语句可以合并成一个。因此，您的 JPA 实现将一个事务中使用的所有实体存储在一级缓存中。

因此，下面的代码片段只需要一个 SQL `UPDATE`语句，即使实体在应用程序中以不同的方法被改变。这大大减少了 SQL 语句的数量，尤其是在复杂的模块化应用程序中。

```
public void updateAuthor() {

    EntityManager em = emf.createEntityManager();
    em.getTransaction().begin();

    Author a = em.find(Author.class, 1L);
    a.setFirstName("New first name");

    // execute operations that need to happen
    // during the transaction and between
    // updating first and last name

    a.setLastName("new last name");

    em.getTransaction().commit();
    em.close();
} 
```

### 5.贮藏

如果使用 JPA，缓存是另一个几乎免费的性能调优特性。我已经解释了一级缓存如何用于后写优化。但这既不是唯一的缓存，也不是从中受益的唯一方式。JPA 定义了两种不同类型的缓存:

*   [一级缓存](http://www.thoughts-on-java.org/free-sample-lecture-hibernate-1st-level-cache?utm_source=sitepoint&utm_medium=post&utm_campaign=sitepoint-intro)，包含一个事务中使用的所有实体。
*   二级缓存，以独立于会话的方式存储实体。

这两种缓存都通过在本地内存中存储实体来帮助您减少执行 SQL 语句的数量。如果您必须在同一个或多个事务中多次读取同一个实体，这可以提供巨大的性能改进。最棒的是，你几乎不需要做任何事情就能获得这些好处。

一级缓存总是被激活的，你不需要做任何事情来使用它。JPA 实现在内部使用它来提高应用程序的性能。

需要激活二级缓存，您可以为所有实体或仅为特定实体激活二级缓存。一旦激活了缓存，JPA 实现就会透明地使用它。因此，您在实现业务逻辑时不需要考虑缓存，并且您可以在任何时间点激活或停用它，而无需任何重构。我总是建议为经常读取的实体激活二级缓存，而不改变它们。缓存这些实体可以提供最大的性能优势，并且只需要很少的缓存管理开销。

二级高速缓存的激活需要两个简单的步骤:

1.  在您的`persistence.xml`文件中配置缓存。
2.  将实体标记为可缓存。

先来看看`persistence.xml`文件。配置二级缓存唯一需要做的事情是配置 shared-cache-mode 参数。在这个例子中，我使用了`ENABLE_SELECTIVE`模式，这允许我为特定的实体启用缓存。

```
<persistence  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" version="2.1" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd">
    <persistence-unit name="my-persistence-unit">
        …

        <!--  enable selective 2nd level cache -->
        <shared-cache-mode>ENABLE_SELECTIVE</shared-cache-mode>

        …

    </persistence-unit>
</persistence> 
```

在下面的代码片段中，我向`Author`实体添加了`@Cacheable`注释，以激活它的二级缓存:

```
@Entity
@Cacheable
public class Author {
    …
} 
```

这就是为给定实体激活二级缓存并避免不必要的数据库查询所需做的全部工作。如您所见，JPA 中的基本配置只需要一个配置参数和一个注释。但是 JPA 规范没有定义缓存本身，您可能需要为它提供更多的配置参数。

## 摘要

在这篇文章中，我只展示了 JPA 提供的一小部分特性和优点。但是正如你所看到的，这些特性涵盖了广泛的主题，比如开发人员生产力、数据库可移植性和性能优化。因此，JPA 和 Hibernate 作为其最流行的实现，是实现数据库访问的最常见选择。

你有什么问题吗？欢迎在评论中发表，或者在 twitter 上联系我。

**编者按**:如果你喜欢这篇关于 Hibernate 的高级介绍，请继续关注，因为该领域其他专家的更多文章即将发表。顺便问一下，您知道每个标签都有一个 RSS 提要吗？这里有一个一般用于 [Java 的](https://www.sitepoint.com/java/)，还有一个专门用于 [Hibernate 的](https://www.sitepoint.com/5-reasons-to-use-jpa-hibernate/)。

## 分享这篇文章