# 与 MySQL 对话:你对 JDBC 的第一印象

> 原文：<https://www.sitepoint.com/talking-to-mysql-your-first-look-at-jdbc/>

为了构建我们将用来探索各种可用的 Java Web 开发技术的待办事项列表应用程序，我们将从应用程序的主干开始:**一组连接到 MySQL 数据库**的类，用于检索、建模和更新待办事项列表。

在进一步学习之前，您需要 Java 作为编程语言的工作知识。至少，**你应该理解面向对象编程的基本概念**:类、对象、方法、属性和构造函数。关于这些概念和 Java 编程必要基础的概述，请参见我的文章:[Java 入门](https://www.sitepoint.com/article/getting-started-java)、 [Java 语言基础](https://www.sitepoint.com/article/java-language-basics)和[Java 中面向对象的概念](https://www.sitepoint.com/article/oriented-concepts-java-1)(第一部分和第二部分)。

由于我们连接到数据库，**我们将使用 [Java 数据库连接(JDBC)](http://java.sun.com/products/jdbc/) 类**，它们是 Java 2 企业版(J2EE)平台的一部分，由所有支持 Java 的 Web 服务器支持。这些类为连接到任何数据库服务器提供了一组通用的类。

为了用 JDBC 连接到 MySQL，我们需要抓取一个 MySQL 连接器/J 的副本，MySQL 的 JDBC 驱动程序。在告诉 Java 加载驱动程序后，我们可以让它连接到一个 MySQL 数据库，所有标准的 JDBC 类都会知道如何使用这个连接。

**下载并解压 MySQL Connector/J 归档文件。**在里面你会发现一个`mysql-connector-java-*version*-bin.jar`文件(撰写本文时版本为 3.0.15)，这是驱动程序库。忽略其余的无用内容，将 JAR 文件复制到您计划构建待办事项列表 Java 应用程序的工作目录中。

首先，让我们构建**一个名为`ToDoList`的类，它将代表存储在数据库**中的待办事项列表，并允许我们访问和修改它的条目。我们将把这个类放在一个名为 com.sitepoint 的 Java 包(或命名空间)中，因此在您的工作目录中创建一个名为`com`的目录和一个名为`sitepoint`的子目录，并在其中创建一个名为`ToDoList.java`的文件。以所需的包声明开始文件:

```
package com.sitepoint;
```

在这个类中，我们将使用来自`java.sql`的 JDBC 类和来自`java.util`的集合类，所以让我们导入它们:

```
import java.util.*;
import java.sql.*;
```

最后，我们将声明我们的类:

```
public class ToDoList {
```

为了使这个类尽可能地独立于数据库，**我们将设计它的构造函数来接受一个 JDBC 驱动程序的名字和一个 JDBC 连接字符串**，这两者一起给了它通过 JDBC 连接到数据库所需的一切。构造函数将立即加载驱动程序类，然后将 JDBC 连接字符串存储到私有属性中，以便对象可以在需要时使用它来连接数据库。

```
 private String jdbcConnectionString;

  public ToDoList(String jdbcDriver, String jdbcConnectionString) {
    this.jdbcConnectionString = jdbcConnectionString;

    // Load the driver
    try {
      Class.forName(jdbcDriver).newInstance();
    }
    catch (Exception ex) {
      System.err.println("Error loading database driver " + jdbcDriver +
                         ":n" + ex.getMessage());
    }
  }
```

```
Class.forName() obtains a reference to the JDBC driver class, and calling its `newInstance()` method makes sure it is loaded. We catch any exceptions that may be thrown in case the driver class is not available (if you forget to do this, the compiler will remind you).现在，我们这个类的第一个版本将允许 Java 程序(和网页)获得待办事项列表中的项目列表，获得列表中的项目数量，向列表中添加新项目，以及从列表中删除项目。因为从数据库中获取列表可能是一项耗时的操作，所以我们将让我们的类**在获取列表后保留一份副本，**只在必要时从数据库中更新它。

```
 private List list = new ArrayList();
  private boolean staleList = true;
```

`list`变量将存储从数据库中检索到的列表，第二个变量**将跟踪列表何时需要更新(即何时“过时”)。**我们从一个空的`ArrayList` (Java 的股票标准列表类，来自`java.util`包)开始，假设它需要更新。
现在，不管程序是想要待办事项的实际列表，还是仅仅想知道它们有多少，我们都需要从数据库中获取列表。因此，让我们编写一个私有方法来检查存储的列表(在`list`属性中)是否过时，并在必要时从数据库加载列表:

```
 private void refreshList() {
    if (staleList) {
      // Load list from the database...

      staleList = false;
    }
  }
```

从数据库加载列表是 JDBC 的工作。我们首先使用 JDBC 连接字符串(由构造函数存储在`jdbcConnectionString`中)来**连接数据库。**由于这是一个耗时的操作，我们将把连接存储在另一个名为`conn`的私有属性中，并且只在连接为空时创建一个新连接:

```
 if (conn == null) {
          conn = DriverManager.getConnection(jdbcConnectionString);
        }
```

接下来，我们从连接中创建一个`Statement`对象，并用它来**执行一个`SELECT`查询**并获得结果`ResultSet`对象:

```
 Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery("SELECT todoid, todo FROM todo");
```

有了结果，我们在`list`属性中存储一个新的空的`ArrayList`，然后**用从结果集的内容创建的`ToDoItem`对象**填充它:

```
 list = new ArrayList();
        while (rs.next()) {
          list.add(new ToDoItem(rs.getInt(1), rs.getString(2)));
        }
```

出于几个原因，这可能是一段令人困惑的代码。首先，我们使用**一个`while`循环来遍历结果集。**调用结果集的`next()`方法使其前进到下一条记录，或者如果没有剩余的记录，则返回`false`——从而结束`while`循环。
在这个循环中，我们使用`ArrayList`类的**和`add()`方法向列表中添加条目。**但是我们到底在添加什么呢？`ToDoItem`类的新实例。

```
ToDoItem is a dead simple little class that keeps track of an ID (the database ID of a to-do list item) and a string (the text for that item). These two values are "read only", in that there are no methods provided for altering them. Objects like this that represent records in the database are called data objects. Create this class by pasting the following code into a file called ToDoItem.java alongside the ToDoList.java file you're already working on:

```
package com.sitepoint;

public class ToDoItem {
  private int id;
  private String item;

  ToDoItem(int id, String item) {
    this.id = id;
    this.item = item;
  }

  public int getId() {
    return id;
  }

  public String getItem() {
    return item;
  }

  public String toString() {
    return getItem();
  }
}
```

在这个简单的类中唯一值得注意的细节是，构造函数**没有被声明为公共的，而是被保留为包私有的，**，这样只有`com.sitepoint`包中的类才能创建`ToDoItem`对象。
回到`ToDoList`,`ToDoItem`的构造函数期望得到该项目的 ID 和文本值。我们可以**从结果集的当前行获取这些值，**使用`getInt(1)`从第一列获取一个整数值(`todoid`)，使用`getString(2)`从第二列获取一个字符串值(`todo`)。
这就是填充列表的全部内容，除了处理来自所有这些**的数据库错误，我们必须捕捉任何可能抛出的`SQLException`**(同样，如果您忘记了，编译器会提醒您)。下面是完成的`refreshList()`方法，以及对`conn`属性的描述:

```
 private Connection conn;

  private void refreshList() {
    if (staleList) {
      try {
        if (conn == null) {
          conn = DriverManager.getConnection(jdbcConnectionString);
        }
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery("SELECT todoid, todo FROM todo");

        list = new ArrayList();
        while (rs.next()) {
          list.add(new ToDoItem(rs.getInt(1), rs.getString(2)));
        }
      }
      catch (SQLException ex) {
        System.err.println(
            "Error retrieving to-do list items from the database:n" +
            ex.getMessage());
      }
      staleList = false;
    }
  }
```

有了在`refreshList()`中完成的艰苦工作，实现`getToDoItems()`和`getItemCount()`，分别获得待办事项和这些事项的数量，就不在话下了:

```
 public Iterator getToDoItems() {
    refreshList();
    return list.iterator();
  }

  public int getItemCount() {
    refreshList();
    return list.size();
  }
```

Java 的 list 类**的`iterator()`方法返回一个`Iterator`** ，一个很像我们刚刚处理的数据库结果集的对象，它让请求对象遍历列表。与此同时，`size()`不言自明。
`addItem()`和`deleteItem()`方法的工作方式与`refreshList()`非常相似，除了**它们使用`PreparedStatement`而不是简单的`Statement`。**考虑来自`addItem`的这段代码:

```
 PreparedStatement stmt = conn.prepareStatement(
          "INSERT INTO todo (todo) VALUES (?)");
      stmt.setString(1, item);
      stmt.executeUpdate();
```

注意`INSERT`查询中的问号。这表示将向查询中添加一些未知值的位置(在本例中，是新的待办事项列表项的文本)。接下来的`setString(1, item)`方法调用将一个字符串值(在本例中由`item`变量提供)分配给查询中的第一个点。像`setInt()`这样的其他方法可以让你坚持其他类型的值。以这种方式将值粘贴到数据库查询中**巧妙地避开了其他语言中经常出现的字符转义问题**。
下面是`addItem()`和`deleteItem()`的完整代码，从而完成了我们的`ToDoList`类:

```
 public void addItem(String item) {
    try {
      if (conn == null) {
        conn = DriverManager.getConnection(jdbcConnectionString);
      }
      PreparedStatement stmt = conn.prepareStatement(
          "INSERT INTO todo (todo) VALUES (?)");
      stmt.setString(1, item);
      stmt.executeUpdate();
    }
    catch (SQLException ex) {
      System.err.println(
          "Error adding a to-do list item to the database:n" +
          ex.getMessage());
    }
    staleList = true;
  }

  public void deleteItem(int id) {
    try {
      if (conn == null) {
        conn = DriverManager.getConnection(jdbcConnectionString);
      }
      PreparedStatement stmt = conn.prepareStatement(
          "DELETE FROM todo WHERE todoid=?");
      stmt.setInt(1, id);
      stmt.executeUpdate();
    }
    catch (SQLException ex) {
      System.err.println(
          "Error deleting a to-do list item from the database:n" +
          ex.getMessage());
    }
    staleList = true;
  }
```

请注意，这些方法中的每一个在完成工作后都会将`staleList`设置为`true`，这样下一次尝试读取待办事项列表时，**会让`refreshList`再次从数据库中获取它。**
我们现在需要的是一个类来测试我们到目前为止所做的工作。这里有一个名为`ToDoTest`的类可以完成这个任务。将`ToDoTest.java`与您的其他文件放在一起:

```
package com.sitepoint;

import java.util.*;

public class ToDoTest {
  public static void main(String[] args) {
    ToDoList list = new ToDoList("com.mysql.jdbc.Driver",
        "jdbc:mysql://localhost/*todo*?user=*root*&password=*password*");

    System.out.println("The to-do list contains " + list.getItemCount() +
                       "items:");
    Iterator it = list.getToDoItems();
    while (it.hasNext()) {
      System.out.println(" - " + it.next());
    }

    list.addItem("Another todo item.");
    list.addItem("And yet another.");

    System.out.println("The to-do list contains " + list.getItemCount() +
                       " items:");
    it = list.getToDoItems();
    while (it.hasNext()) {
      System.out.println(" - " + it.next());
    }    
  }
}
```

您需要将您的 MySQL 数据库名称、用户名和密码替换到该文件顶部附近的 JDBC 连接字符串中。
从顶层工作目录编译您的三个类:

```
javac com/sitepoint/ToDoList.java com/sitepoint/ToDoItem.java 
com/sitepoint/ToDoTest.java

然后运行`ToDoTest`，将 MySQL JDBC 驱动程序添加到类路径中:

```

```
java -classpath .;mysql-connector-java-3.0.15-ga-bin.jar 
com.sitepoint.ToDoTest
```

在向列表中添加两个新项目之前和之后，您应该会看到待办事项列表项目的列表，如下所示:

```
The to-do list contains 2 items:
 - Another todo item.
 - And yet another.
The to-do list contains 4 items:
 - Another todo item.
 - And yet another.
 - Another todo item.
 - And yet another.
```

就是这样！我们准备围绕这些类构建一个 Web 应用程序。
[下载源代码](https://www.sitepoint.com/examples/jgrind/todo-backbone.zip) (5KB)

```

```

## 分享这篇文章