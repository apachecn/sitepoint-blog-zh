# 使用 Realm.io 简化移动应用数据库

> 原文：<https://www.sitepoint.com/streamlining-mobile-app-databases-with-realm-io/>

这篇文章由马克·托勒同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

尽管大多数现代移动应用程序需要在线访问来发送和接收数据，但在应用程序开发的某些时候，您需要将数据存储在本地设备上。为此，您需要一个本地数据库。

数据库在读写数据和领域时通常很慢。io 是一个有前途的解决方案，可以使开发变得容易，并提供从 Android 上的 SQLite 和 iOS 上的核心数据的性能升级。

Realm 是一个跨平台的移动数据库，于 2014 年 7 月向公众发布，从头开始构建，专门用于直接在手机、平板电脑和可穿戴设备上运行。Realm 简单、快速且兼容 iOS 和 Android。

如果你不确定它的效率或者你为什么要使用它，看看它的性能图。

## Android 领域入门

你可以从 [GitHub](https://github.com/sitepoint-editors/Realm-Project) 下载一个基于本教程的示例项目。

打开 *build.gradle (app)* ，添加需要的依赖项，同步项目。

```
dependencies {
  …
   compile 'io.realm:realm-android:0.86.0'
}
```

阅读 Java 文档以获得更详细的指令。

## 数据模型

领域数据模型基于 [Java Beans](https://en.wikipedia.org/wiki/JavaBeans) 原则。数据模型对象看起来像这样。

```
public class Contact extends RealmObject {

   private String name;
   private String email;
   private String address;
   private int age;

   public String getName() {
       return name;
   }

   public void setName(String name) {
       this.name = name;
   }

   public String getEmail() {
       return email;
   }

   public void setEmail(String email) {
       this.email = email;
   }

   public String getAddress() {
       return address;
   }

   public void setAddress(String address) {
       this.address = address;
   }

   public int getAge() {
       return age;
   }

   public void setAge(int age) {
       this.age = age;
   }
}
```

上面介绍的 java 类将表示一个人的个人联系信息的`RealmObject`,并将在内存中存储新的联系信息，并进行查询以返回那些存储的联系信息。

## 领域基本操作

与任何其他数据库平台一样，领域数据库的基本操作称为 CRUD 操作(创建/读取/更新/删除)。这种操作发生在事务块内部。

Realm 既支持小操作的主 UI 线程上的事务，也支持后台线程中的事务，以避免阻塞更复杂操作的 UI 线程。

要使用 Realm，首先要获取主 UI 线程的 Realm 实例。

```
Realm realm = Realm.getInstance(this);
```

使用此实例来处理领域数据库。让我们试着创建一个`Contact`对象并将其写入 Realm。

Realm 处理封装在事务块中的写操作，以确保安全的多线程。写操作看起来像这样:

```
realm.beginTransaction();
Contact contact = realm.createObject(Contact.class);
contact.setName("Contact's Name");
contact.setEmail("Contact@hostname.com");
contact.setAddress("Contact's Address");
contact.setAge(20);
realm.commitTransaction();
```

这里 Realm 基于模型类`Contact.java`创建了一个新的`RealmObject`。我给新对象添加了一些值，并调用了`commitTransaction()`方法来结束事务块。在提交期间，所有更改都将被写入磁盘。

使用`executeTransaction()`方法可以完成相同的过程。此方法自动处理开始/提交事务方法。

```
realm.executeTransaction(new Realm.Transaction() {
   @Override
   public void execute(Realm realm) {
       Contact contact = realm.createObject(Contact.class);
       contact.setName("Contact's Name");
       contact.setEmail("Contact@hostname.com");
       contact.setAddress("Contact's Address");
       contact.setAge(20);
   }
});
```

Android 的一个好习惯是在后台线程上处理所有的写操作，以避免阻塞 UI 线程，所以 Realm 支持异步事务。在前面的`executeTransaction()`方法中，只需添加一个`Realm.Transaction.Callback()`参数，使进程异步。

```
realm.executeTransaction(new Realm.Transaction() {
   @Override
   public void execute(Realm realm) {
       Contact contact = realm.createObject(Contact.class);
       contact.setName("Contact's Name");
       contact.setEmail("Contact@hostname.com");
       contact.setAddress("Contact's Address");
       contact.setAge(20);
   }
}, new Realm.Transaction.Callback() {
   @Override
   public void onSuccess() {
       //Contact saved
   }

   @Override
   public void onError(Exception e) {
       //Transaction is rolled-back
   }
});
```

## 问题

使用 Realm 处理查询也很简单。您不需要构建复杂的查询并处理数据库异常，比如 SQLite 中最有可能出现的两个异常 [SQLException](http://developer.android.com/reference/android/database/SQLException.html) 或[CursorIndexOutOfBoundsException](http://developer.android.com/reference/android/database/CursorIndexOutOfBoundsException.html)。

```
//Query to retrieve all Contacts
RealmQuery<Contact> query = realm.where(Contact.class);
// Add query conditions: age over 18
query.greaterThan("age", 18);
// Execute the query:
RealmResults<Contact> result = query.findAll();
//Contacts stored in result
```

或者如果您希望它异步运行:

```
// Execute the query asynchronously:
RealmResults<Contact> result = query.findAllAsync();
```

从领域中删除数据很容易。

```
realm.beginTransaction();
result.remove(0);
realm.commitTransaction();
```

记住，领域操作是在事务块内部处理的。

对于那些坚持传统数据库原则的开发人员来说，Realm 提供了一种给`RealmObject`字段分配注释和特殊字段属性的方法，使它们更像“数据库字段”。

*   `@Required`–强制检查以禁止空值
*   `@Ignore`–字段不应保存到磁盘
*   `@Index`–向字段添加搜索索引
*   `@PrimaryKey`–唯一标识数据库中的每条记录。

RealmObjects 之间的关系被视为 SQL 数据库中表之间的关系。

如果使用 JSON 数据格式进行开发，Realm 集成了存储和检索 JSON 数据的方法。

## 来自王国的额外的东西

有可能将 Realm 与 Android 的其他常用库集成在一起。让你的应用程序开发变得更容易，并检查与领域集成的库。

## 结论

要在 Android 应用程序中存储数据，您通常必须了解 SQL，理解主键、外键的概念，以及如何构建查询和管理表关系。

Realm 简化了这一点，我认为它将成为移动存储领域的一个里程碑。你怎么看？

## 分享这篇文章