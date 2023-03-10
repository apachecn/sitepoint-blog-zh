# 大旅游:踢它 MVC 风格

> 原文：<https://www.sitepoint.com/the-grand-tour-kickin-it-mvc-style/>

对于 Java 新闻来说，一月是一个相当轻松的月份。哦，好吧，有更多的时间来继续我的 Java Web 开发之旅！还跟着一起？

[上次](https://www.sitepoint.com/blog/)，我向您展示了如何将“待办事项列表”servlet 转换成 JSP。不幸的是，就可读代码而言，我们得到的并没有好到哪里去—**嵌入在 HTML 文档中的 Java 代码并不比嵌入在 Java 类中的 HTML 代码更可读。**

怎么办？到目前为止，我们有两个工具可供使用:servlets 和 JSP。Servlets 非常适合包含 Java 代码，JSP 非常适合包含 HTML 代码。答案是**将我们的应用程序分开，这样 Java 代码进入 servlets，HTML 代码进入 JSP。**

这种称为[模型 2 架构](http://java.sun.com/blueprints/guidelines/designing_enterprise_applications_2e/web-tier/web-tier5.html#1078527)的标准方法已经存在很长时间了，但最近它以一个更具描述性的名字而为人所知:**模型-视图-控制器(MVC)。**

这个想法是将应用程序分成三个部分:

*   **模型**:执行应用程序实际工作的 Java 类的集合(通常称为**业务逻辑**，独立于 Web 界面。
*   **视图**，一组 JSP 或其他技术，产生用户看到的实际网页。
*   **控制器**，一个或多个 servlets，通过告诉模型做什么来处理浏览器请求，然后交给视图来响应。

现在我可以整天喋喋不休地谈论这个，但是通过一个例子更容易理解，所以**让我们开始修改我们的待办事项列表应用程序。**

**模型通常是最好的起点，**因为你通常不用太担心 Web 界面就可以编写它。在我们的待办事项列表应用程序中，我们已经*有了*我们的模型:它由类`ToDoList`和`ToDoItem`组成。这两个类完成了管理待办事项列表的所有工作。

当我们查看这些类时，让我们对`getToDoItems`方法做一个小的调整:我们将**使它返回一个`List`而不是一个`Iterator`。**当通过这种方法检索待办事项列表项时，这允许在如何使用它们方面有更多的灵活性。

```
 public List getToDoItems() {
    refreshList();
    return (List)list.clone();
  }
```

接下来，让我们来研究我们的控制器。我们知道浏览器会发出三种不同的请求:查看待办事项列表的简单请求、向列表添加新项目的表单帖子，以及从列表中删除现有项目的表单帖子。

我们现在有一个选择:**我们是编写一个知道如何处理所有这些不同请求类型的 servlet，还是编写三个简单的、专门的 servlet**，每个 servlet 都知道如何处理一种类型的请求？没有正确的答案，因为每种方法都有优点，但是让我们尽量保持我们的类简单，使用多个 servlets。

我们从`ToDoServlet`开始，它将处理查看待办事项列表的请求。**这个 servlet 需要做的第一件事是建立模型**—`ToDoList`的一个实例。回到[当我们第一次看 servlet](https://www.sitepoint.com/blog/)时，我们用 servlet 的`init()`方法来做这件事，它将`ToDoList`存储在 servlet 的一个实例变量中。

这一次，这样做不太好，因为**我们希望我们的三个 servlets 都能够访问模型。**我们不能在一个 servlet 的初始化过程中创建`ToDoList`,因为其他 servlet 可能首先需要它，我们也不能将它存储在实例变量中，因为其他 servlet 根本看不到它。我们需要的是一种在 Web 应用程序首次启动时创建模型的方法，以及一个任何 servlet 都可以访问的存储模型的地方。

**为了执行整个应用程序的初始化任务，我们必须使用一个`ServletContextListener`。**这不是一个要扩展的类，而是一个任何类都可以实现的接口。因为我们的“主”servlet 是做这些事情的好地方，所以我们将让它实现这个接口:

```
public class ToDoServlet extends HttpServlet
    implements ServletContextListener {
```

当一个类实现`ServletContextListener`时，它必须包含两个方法: **`contextInitialized`，它执行应用程序初始化任务**，和`contextDestroyed`，它执行应用程序清理:

```
 public void contextInitialized(ServletContextEvent sce) {
    ServletContext sc = sce.getServletContext();
    sc.setAttribute("toDoList",
        new ToDoList(sc.getInitParameter("jdbcDriver"),
                     sc.getInitParameter("jdbcConnectionString")));
  }

  public void contextDestroyed(ServletContextEvent sce) {
  }
```

这里的所有操作都在`contextInitialized`中，因为当应用程序关闭时，我们不需要做任何特殊的事情。正如您所看到的，这两个方法在被调用时都会收到一个`ServletContextEvent`，从中我们可以获得对`ServletContext`的引用。`ServletContext`是一个表示 Web 应用程序整体的对象——它是运行 *servlet* 的*上下文*。所有的 servlets 和 JSP 都可以访问`ServletContext`；因此，它提供了存放我们模型的完美场所！

`ServletContext`允许您设置和获取**属性，这些属性只是与字符串标签相关联的应用程序范围的值。**我们将把我们的模型，一个新的`ToDoList`对象，存储在一个标签为`"toDoList"`的属性中。

在我们继续之前，仔细看看创建`ToDoList` : 的代码

```
 new ToDoList(sc.getInitParameter("jdbcDriver"),
                     sc.getInitParameter("jdbcConnectionString")));
```

正如我们之前所做的，我们从初始化参数中获得了该类所需的 JDBC 驱动程序和连接字符串值，但是因为我们正在为整个应用程序执行初始化，`ToDoServlet`的初始化参数还不可用！这就是为什么我们调用`ServletContext`对象的`getInitParameter`方法，而不是我们习惯的 servlet 本身。这些被称为**上下文初始化参数**，与 servlet 初始化参数相对。

所以我们已经有了一个准备初始化我们的应用程序的`ServletContextListener`，只要它得到它需要的**上下文初始化参数**。正如您可能已经猜到的，我们需要在我们的`web.xml`文件中添加一些东西来使它工作:

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd" version="2.4">
  <display-name>ToDoMVC</display-name>
  <listener>
    <listener-class>com.sitepoint.ToDoServlet</listener-class>
  </listener>
  <context-param>
    <description>The JDBC driver class.</description>
    <param-name>jdbcDriver</param-name>
    <param-value>com.mysql.jdbc.Driver</param-value>
  </context-param>
  <context-param>
    <description>The JDBC connection string.</description>
    <param-name>jdbcConnectionString</param-name>
    <param-value>jdbc:mysql://localhost/todo?user=root&amp;password=password</param-value>
  </context-param>
</web-app>
```

`<listener>`标签让应用程序知道我们提供的上下文监听器，而`<context-param>`标签设置我们的应用程序范围的上下文初始化参数。

现在，在我们陷入所有这些应用程序初始化的东西之前，我似乎记得我们正在编写一个 servlet。那么**我们来看看`doGet`和`doPost`的方法:**

```
 public void doGet(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
    ToDoList toDoList = (ToDoList)getServletContext().getAttribute("toDoList");
    List toDoItems = toDoList.getToDoItems();
    request.setAttribute("toDoItems", toDoItems);

    RequestDispatcher view = request.getRequestDispatcher("/todo.jsp");
    view.forward(request, response);
  }

  public void doPost(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
    doGet(request, response);
  }
```

看那个！一个没有嵌入 HTML 代码的 servlet！这种事怎么可能？请记住，这个 **servlet 仅仅是我们 MVC 设计**中的控制器——在完成模型的工作后，它将请求传递给视图以显示实际的网页。仔细观察`doGet`方法，您会发现它确实是这样做的:

```
 ToDoList toDoList = (ToDoList)getServletContext().getAttribute("toDoList");
    List toDoItems = toDoList.getToDoItems();
    request.setAttribute("toDoItems", toDoItems);
```

在这里，它获取模型(存储在 servlet 上下文属性中的`ToDoList`),用它获得待办事项列表项的`List`,然后将这个`List`存储在视图能够找到它的地方——也就是在`request`对象中。原来**`request`对象也支持属性，这是向视图传递构建页面所需值的最佳方式。**

完成繁重的工作后，**我们准备将请求传递给视图**(在本例中，是应用程序根目录中的一个名为`todo.jsp`的 JSP):

```
 RequestDispatcher view = request.getRequestDispatcher("/todo.jsp");
    view.forward(request, response);
```

乍一看，这似乎有点令人迷惑，但是**这是我们将总是用来将请求从 servlet 转发到 JSP 的公式。**我们得到一个`RequestDispatcher`对象作为我们想要转发到的位置(在本例中，`"/todo.jsp"`指定了应用程序根目录中的`todo.jsp`文件)，然后我们调用它的`forward`方法，传递它完成工作所需的`request`和`response`对象。

好吧，我不得不唠叨一点来解释这个 servlet 中所有的新东西，但是如果你退后一步看看代码，你会发现它实际上非常整洁。由于它们没有所有应用程序设置的负担，我们的另外两个 servlets 甚至更整洁！

```
package com.sitepoint;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class AddToDoServlet extends HttpServlet {
  public void doPost(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {

    String newToDo = request.getParameter("newtodo");
    if (newToDo != null) {
      ToDoList toDoList = (ToDoList)getServletContext().getAttribute("toDoList");
      toDoList.addItem(newToDo);
    }
    response.sendRedirect("index.html");
  }
}
```

```
package com.sitepoint;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class DeleteToDoServlet extends HttpServlet {
  public void doPost(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
    String deleteid = request.getParameter("deleteid");
    if (deleteid != null) {
      try {
        ToDoList toDoList = (ToDoList)getServletContext().getAttribute("toDoList");
        toDoList.deleteItem(Integer.parseInt(deleteid));
      }
      catch (NumberFormatException e) {
        throw new ServletException("Bad deleteid value submitted.", e);
      }
    }
    response.sendRedirect("index.html");
  }
}
```

您可能会惊讶地注意到**这两个 servlets 在完成它们的工作后实际上并不转发给 JSP** 。相反，它们将浏览器重定向到`"index.html"`，我们将很快将其配置为指向我们的主`ToDoServlet`。

因为我们想在添加或删除项目后向用户显示更新后的待办事项列表，**我们*可以*将请求转发给`/todo.jsp`、**，但是，正如我过去解释的那样，**如果用户之后要刷新浏览器**，这会导致问题。浏览器将重新提交项目添加/删除请求，导致错误或重复的列表条目。重定向而不是转发可以解决这个问题，方法是让浏览器在提交表单后发出一个新的请求，这个请求可以安全地重复。

为了完善我们的 servlets，**我们需要在我们的`web.xml`文件中为它们分配 URL。我们将使用`/index.html`作为`ToDoServlet`，因为我们希望它在用户加载我们的应用程序目录时默认出现。`AddToDoServlet`和`DeleteToDoServlet`将分别获得`/AddItem.do`和`/DeleteItem.do`，遵循 Java Web 应用程序中给“动作”URL`.do`扩展的惯例。**

```
 <servlet>
    <servlet-name>todoservlet</servlet-name>
    <servlet-class>com.sitepoint.ToDoServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>todoservlet</servlet-name>
    <url-pattern>/index.html</url-pattern>
  </servlet-mapping>
  <servlet>
    <servlet-name>addtodoservlet</servlet-name>
    <servlet-class>com.sitepoint.AddToDoServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>addtodoservlet</servlet-name>
    <url-pattern>/AddItem.do</url-pattern>
  </servlet-mapping>
  <servlet>
    <servlet-name>deletetodoservlet</servlet-name>
    <servlet-class>com.sitepoint.DeleteToDoServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>deletetodoservlet</servlet-name>
    <url-pattern>/DeleteItem.do</url-pattern>
  </servlet-mapping>
```

servlets 完成后，**剩下的工作就是为我们的应用程序构建视图。**对于这个例子，我们只需要一个 JSP 文件:`todo.jsp`。

既然我们已经将所有的初始化和请求处理代码提取到 servlets 中，我们的 JSP 文件看起来更加合理:

```
<%@ page import="java.util.*,com.sitepoint.*,uk.co.anyware.html.*" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html >
<head>
  <title>To-Do List</title>
  <meta http-equiv="content-type" content="text/html; charset=iso-8859-1" />
  <link rel="stylesheet" type="text/css" href="<%= request.getContextPath() %>/styles.css" />
</head>
<body>
  <%
  List toDoItems = (List)request.getAttribute("toDoItems");
  Iterator it = toDoItems.iterator();
  if (it.hasNext()) {
    %>
    <form action="DeleteItem.do" method="post">
      <select name="deleteid" size="<%= Math.max(2, toDoItems.size()) %>">
        <%
        while (it.hasNext()) {
          ToDoItem toDoItem = (ToDoItem) it.next();
          %>
          <option value="<%= toDoItem.getId() %>"><%= HTMLEscaper.escape(toDoItem.toString()) %></option>
          <%
        }
        %>
      </select>
      <input type="submit" value="Delete Selected Item" />
    </form>
    <%
  }
  %>
  <form action="AddItem.do" method="post">
    <input type="text" name="newtodo" />
    <input type="submit" value="Add New Item" />
  </form>
</body>
</html>
```

如果你理解我们上次构建的 JSP，这个应该是小菜一碟！scriptlet 代码简单地将`List`从`request`的`"toDoItems"`属性中提取出来，生成待办事项列表，我们的两个表单现在提交给我们分配给相应 servlets 的`.do`URL。除了这些变化，**它仍然是旧的 JSP，但是 Java 代码少了很多。**

现在我们有了:一个 MVC Java Web 应用程序！下面是目录结构:

```
/todo.jsp
/styles.css
/WEB-INF/web.xml
/WEB-INF/classes/com/sitepoint/AddToDoServlet.class
/WEB-INF/classes/com/sitepoint/AddToDoServlet.java
/WEB-INF/classes/com/sitepoint/DeleteToDoServlet.class
/WEB-INF/classes/com/sitepoint/DeleteToDoServlet.java
/WEB-INF/classes/com/sitepoint/ToDoItem.class
/WEB-INF/classes/com/sitepoint/ToDoItem.java
/WEB-INF/classes/com/sitepoint/ToDoList.class
/WEB-INF/classes/com/sitepoint/ToDoList.java
/WEB-INF/classes/com/sitepoint/ToDoServlet.class
/WEB-INF/classes/com/sitepoint/ToDoServlet.java
/WEB-INF/classes/uk/co/anyware/html/HTMLEscaper.class
/WEB-INF/classes/uk/co/anyware/html/HTMLEscaper.java
/WEB-INF/lib/mysql-connector-java-version-bin.jar
```

[下载代码](https://www.sitepoint.com/examples/jgrind/ToDoMVC.war) (250KB)

这个应用程序开始看起来非常好，即使我自己这么说。手工键入一些代码可能会有点痛苦，但是使用一个好的 Java IDE 为您编写样板代码(上个月刚刚发布了一个重要的新版本， [NetBeans](http://www.netbeans.org/) 是一个受欢迎的免费选择)，许多繁重的工作就会消失。

这个阶段应用程序的主要弱点仍然是 JSP 中的 Java scriptlet 代码。我们已经去掉了这个文件中的大部分 Java 代码，但是对于一个网页设计者来说，其中的内容仍然是相当混乱的。大旅行的下一步将是查看现代 JSP 的一些特性，这些特性允许我们放弃这些 scriptlets，而使用标记和表达式。** 

## **分享这篇文章**