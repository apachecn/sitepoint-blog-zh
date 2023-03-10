# 如何使用标准查询选择 POJOs

> 原文：<https://www.sitepoint.com/how-to-select-pojos-with-a-criteriaquery/>

### 目录 

*   [如何使用标准查询选择 POJOs】](#howtoselectpojoswithacriteriaquery)
*   [问题](#problem)
*   [解决方案](#solution)
*   [源代码](#sourcecode)
*   [了解更多信息](#learnmore)
*   [总结](#summary)
*   [评论](#comments)

Criteria API 提供了一种以编程方式定义查询的类型安全方法。与 JPQL 类似，您可以使用它来选择一组实体属性，并将每个结果集记录映射到一个普通的旧 Java 对象(POJO)。

Criteria API 支持与 JPQL 相同的特性集，我在新书 [Hibernate Tips 中展示了 70 多种常见 Hibernate 问题的解决方案](http://www.hibernate-tips.com/?utm_source=BookLaunch&utm_medium=Sitepoint)如何使用它来实现各种用例。这是一本 Hibernate cookbook，有 70 多个现成的方法，主题包括基本和高级映射、日志、Java 8 支持、缓存以及静态和动态定义的查询。你可以本周在亚马逊上以仅 2.99 美元的特价买到它。

让我们来看看书中包含的其中一个食谱。它使用 Criteria API 以编程方式定义一个查询，该查询选择一组数据库列并将它们作为 POJOs 返回。为了更好地理解这篇文章，你应该已经熟悉了 Hibernate 的基本概念。

* * *

## 如何使用标准查询选择 POJOs

### 问题

JPQL 支持构造函数表达式来选择 POJOs，而不是实体或标量值。我可以用标准 API 做同样的事情吗？

### 解决办法

您可以像在 JPQL 查询中一样，在 Criteria API 中使用类似的构造函数表达式。在下面的例子中，我想选择`AuthorValue`对象。

```
public class AuthorValue {

   private String firstName;
   private String lastName;

   public AuthorValue(String firstName, String lastName) {
       this.firstName = firstName;
       this.lastName = lastName;
   }

   ...
} 
```

`CriteriaQuery`的定义遵循与选择实体相同的方法。

您首先需要从`EntityManager`中获取一个`CriteriaBuilder`实例。它是一个工厂类，帮助您定义查询的不同部分，比如绑定参数、函数调用和谓词。

然后创建一个`CriteriaQuery`实例，它是表示查询的对象图的根。我建议将查询的返回类型作为参数提供给`createQuery`方法。它创建了一个`CriteriaQuery`接口的类型化实例。我想在这个例子中选择`AuthorValue`对象，因此，提供`AuthorValue.class`作为参数。

在下一步中，您将定义查询的`FROM`子句。在这个例子中，我使用`Author`实体作为查询的根。

然后，您可以定义查询的投影。在这个例子中，它是对`AuthorValue`构造函数的调用。`CriteriaBuilder`的`construct`方法允许你定义构造函数调用。使用 Hibernate 实例化的类作为第一个参数调用这个方法，并使用一个可选的列表`Selections`作为构造函数的参数。

在这个例子中，我使用 JPA 元模型类`Author_`来引用`Author`实体的`firstName`和`lastName`属性。如果您不熟悉 JPA 元模型，可以看看 Hibernate 技巧如何以类型安全的方式引用实体属性，它提供了一种舒适且类型安全的方式来引用实体属性。

`WHERE`子句的定义是可选的。如果您想从数据库中选择所有记录，可以跳过这个块并执行查询。这就是我在这个例子中所做的。您可以在如何使用 CriteriaQuery 选择实体一章中看到一个`WHERE`子句定义的示例。

```
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<AuthorValue> q = cb.createQuery(AuthorValue.class);
Root<Author> root = q.from(Author.class);
q.select(
   cb.construct(
       AuthorValue.class,
       root.get(Author_.firstName),
       root.get(Author_.lastName))); 
```

这就是定义`CriteriaQuery`所需要做的全部工作。现在，您可以分两步执行它。你首先需要用你的`CriteriaQuery`调用`EntityManager`的`createQuery`方法。这个方法调用返回与 JPQL 查询中使用的相同的`TypedQuery`接口。您可以使用它来设置绑定参数值或对查询结果进行分页。我在这个例子中没有使用任何绑定参数，因此可以跳过这一部分。在最后一步，您需要调用`TypedQuery`接口上的`getResultList`方法来执行查询并检索`AuthorValue`对象的`List`。

```
TypedQuery<AuthorValue> query = em.createQuery(q);
List<AuthorValue> authors = query.getResultList(); 
```

当我创建查询时，我在构造函数调用定义中引用了`firstName`和`lastName`属性。正如您在下面的日志消息中看到的，Hibernate 使用这些引用来生成一个 SQL 查询，该查询从`Author`表中选择`firstName`和`lastName`列。

```
13:43:09,884 DEBUG [org.hibernate.SQL] -
   select
       author0_.firstName as col_0_0_,
       author0_.lastName as col_1_0_
   from
       Author author0_ 
```

### 源代码

您可以在[书](http://www.hibernate-tips.com/?utm_source=BookLaunch&utm_medium=Sitepoint)中找到一个下载链接，该链接包含针对这个 Hibernate 技巧的可执行测试用例。

### 了解更多信息

如果您不想创建一个 POJO 来表示您的查询结果，您也可以[选择一组标量值](http://www.thoughts-on-java.org/hibernate-tips-select-multiple-scalar-values-criteria-query/)。在*如何在一个标准查询中选择多个标量值*一章中，我会告诉你如何去做。

如果你想在你的项目中使用 Criteria API，你也应该看看 [JPA 元模型](http://www.thoughts-on-java.org/static-metamodel/)。它提供了一种以类型安全的方式创建查询的好方法。在*如何以类型安全的方式引用实体属性*一章中，我将向您展示如何使用和生成所需的类。

* * *

![Using the CriteriaQuery easily gets you POJOs from Hibernate](img/da7d3613c14f177144be65963ed7dcca.png)

## 摘要

Criteria API 提供了一个以编程方式定义查询的类型安全选项。正如您在本书摘录中看到的，您可以使用它来选择一组数据库列，并让 Hibernate 将每个结果集记录映射到一个 POJO。

在我的新书 [Hibernate Tips:常见 Hibernate 问题的 70 多种解决方案](http://www.hibernate-tips.com/?utm_source=BookLaunch&utm_medium=Sitepoint)中获得更多类似的食谱。它为您提供了 70 多个现成的方法，主题包括基本和高级映射、日志记录、Java 8 支持、缓存以及静态和动态定义的查询。仅仅几天，你就可以[买到 2.99 美元的电子书和 12.99 美元的平装本。](http://www.hibernate-tips.com/?utm_source=BookLaunch&utm_medium=Sitepoint)

## 分享这篇文章