# 大旅游:老学校 JSP

> 原文：<https://www.sitepoint.com/the-grand-tour-old-school-jsp/>

你以为我走了，是吗？十二月对我来说是疯狂的一个月，所以我不得不暂时搁置我的博客，但是随着新的一年到来，我有了新的优先事项，我回来为你们这些渴望 Java 的人们服务。

首先，今年是理解 Java Web 应用程序开发技术的又一步。之前，我们看了 servlets，即被设计来响应 Web 浏览器请求的 Java 类。使用 servlet，我们为数据库驱动的待办事项列表组装了一个简单的 Web 界面。**虽然这种方法行得通，但用这种方法编写实用的 Web 应用程序会很痛苦，因为应用程序逻辑都混合在页面的 HTML 代码中。**

JavaServer Pages (JSP)就是为了克服 servlets 的这一弱点而创建的。你不用写一个包含 HTML 代码的 Java 类，而是写一个包含 Java 代码的 HTML 页面。然后，服务器将这个页面动态地转换成一个 Java servlet，以便处理页面请求。正如我们将看到的，以这种方式逆转方法解决了一些问题，但也产生了一些新的问题。

从我们上次构建的待办事项列表 Web 应用程序开始，**让我们去掉讨厌的 servlet，用一个做同样事情的 JavaServer 页面来代替它:** `todo.jsp`。下面是我们的应用程序更新后的文件和目录结构:

```
/todo.jsp
/styles.css
/WEB-INF/web.xml
/WEB-INF/classes/com/sitepoint/ToDoItem.class
/WEB-INF/classes/com/sitepoint/ToDoItem.java
/WEB-INF/classes/com/sitepoint/ToDoList.class
/WEB-INF/classes/com/sitepoint/ToDoList.java
/WEB-INF/classes/uk/co/anyware/html/HTMLEscaper.class
/WEB-INF/classes/uk/co/anyware/html/HTMLEscaper.java
/WEB-INF/lib/mysql-connector-java-version-bin.jar
```

因此，让我们开始编写我们的`todo.jsp`文件，打开你最喜欢的文本编辑器，我们就可以开始烹饪了！

JSP 文件看起来很像 HTML 文件，除了其中散布着特殊的 JSP 标记。一般来说，**所有 JSP 标签都以`<%`开始，以`%>`结束。**随着我们在未来寻找更现代、更先进的 JSP 工作方式，这是一条将被打破的规则，但现在如果你认为这是真理，那也没关系。

我们需要做的第一件事是提供一些关于页面的信息。这是通过页面指令完成的。**指令是一种 JSP 标签，包含各种设置和其他类型的配置信息。**指令在开始`<%`后有一个`@`符号。这是我们的页面指令:

```
<%@ page import="java.util.*,com.sitepoint.*,uk.co.anyware.html.*" %>
```

page 指令最常见的用途是**导入 JSP 文件中的 Java 代码将使用的 Java 包。**在这种情况下，我们已经指出我们需要来自`java.util`、`com.sitepoint`和`uk.co.anyware.html`的类。这些相同的包是由我们的 to-do list servlet 导入的(您可能希望将这些代码放在附近进行比较)。

然后，我们可以像编写任何(X)HTML 文件一样编写页面:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html >
<head>
  <title>To-Do List</title>
  <meta http-equiv="content-type"
      content="text/html; charset=iso-8859-1" />
  <link rel="stylesheet" type="text/css"
      href="<%= request.getContextPath() %>/styles.css" />
</head>
```

仔细观察，您会看到另一个 JSP 标记:`<%= request.getContextPath() %>`。这就是我们所说的表达式，如标签中左`< %`后面的`=`符号所示。**表达式用于输出 Java 代码的值；**在这种情况下，我们输出

`request`对象的`getContextPath()`方法——我们的 Web 应用程序的根目录，浏览器需要它来定位`styles.css`文件。

是的，JSP 代码就像 servlet 代码一样，通过一个名为`request`的变量来访问浏览器的请求。还有一个用于浏览器响应的`response`变量。**这两个变量都被称为隐式对象，因为它们是为你隐式创建的。**

**其他隐式对象**包括`out`，一个工作方式类似于在 servlet 中用于向浏览器发送输出的`PrintWriter`的对象，`session`，一个用于在特定用户访问您的站点的过程中存储值的对象，`application`，一个用于在整个应用程序中存储值的对象。

现在，正如我们的 servlet 依靠**一个`ToDoList`对象来完成应用程序的许多工作一样，**我们的 JSP 文件也是如此。我们需要声明这个对象，为此**我们需要另一个 JSP 标签:声明。**

```
<%!
private ToDoList toDoList;

public void jspInit() {
  toDoList = new ToDoList(getInitParameter("jdbcDriver"),
      getInitParameter("jdbcConnectionString")); 
}
%>
```

正如您所看到的，声明由在开始`<%`后面的感叹号(`!`)表示，**包含 Java 属性和方法声明**，就像您在 Java 类中看到的一样。

在这种情况下，我们声明了一个名为`toDoList`的`ToDoList`对象，并编写了一个名为`jspInit()`的方法，该方法根据初始化参数创建这个对象，就像我们在 servlet 的`init()`方法中所做的一样。`init()`是 servlet 的一个特殊方法，在第一次使用 servlet 之前由服务器调用， **`jspInit()`是一个特殊方法，它被自动调用来初始化 JSP 文件。**

接下来是在这个页面中处理表单提交的代码。您应该记得我们的 servlet，该页面将包含两个表单:一个用于向待办事项列表添加项目，另一个用于删除项目。处理这些提交的代码在我们的 JSP 中是相同的，除了它放在另一种类型的 JSP 标签中:scriptlet。

```
<%
String newToDo = request.getParameter("newtodo");
if (newToDo != null) {
  toDoList.addItem(newToDo);
  response.sendRedirect(request.getRequestURI());
  return;
}

String deleteid = request.getParameter("deleteid");
if (deleteid != null) {
  try {
    toDoList.deleteItem(Integer.parseInt(deleteid));

    response.sendRedirect(request.getRequestURI());
    return;
  }
  catch (NumberFormatException e) {
    throw new ServletException("Bad deleteid value submitted.", e);
  }
}
%>
```

最初构思 JSP 时，scriptlets 被认为是最常见的 JSP 标记类型，因此它们用简单的`<%`和`%>`分隔符来表示——在标记的开始部分没有特殊字符。**每次页面被请求时，scriptlet 中的 Java 代码都会被执行。**

表单处理完成后，剩下的就是生成表单本身了。首先，**我们有我们的待办事项列表，它显示当前的待办事项并允许用户删除它们:**

```
<body>
  <%
  Iterator toDoItems = toDoList.getToDoItems();
  if (toDoItems.hasNext()) {
    %>
    <form action="<%= request.getRequestURI() %>" method="post">
      <select name="deleteid" size="<%= Math.max(2, toDoList.getItemCount()) %>">
        <%
        while (toDoItems.hasNext()) {
          ToDoItem toDoItem = (ToDoItem) toDoItems.next();
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
```

看起来有点乱，不是吗？这里我们拥有的是 HTML 代码、JSP 脚本和 JSP 表达式的混合。请允许我给你分解一下。

```
 <%
  Iterator toDoItems = toDoList.getToDoItems();
  if (toDoItems.hasNext()) {
    %>
    ...
    <%
  }
  %>
```

**第一个 scriptlet 获取一个包含待办事项列表中项目的`Iterator`对象。我们的 servlet 做了完全相同的事情。因为如果待办事项列表不包含任何项目，我们就不需要显示它，所以我们使用一个`if`语句来检查它是否包含了`Iterator`的`hasNext()`方法。**

表单的所有代码都放在这个`if`语句中。`if`语句的右括号在页面的最后一个 scriptlet 中。如您所见，scriptlet 中的 Java 代码不需要自包含；**您可以将 JSP 标记之间的 HTML 代码视为输出该 HTML 代码的指令，scriptlets 提供了该 HTML 应该何时以及如何出现的逻辑。**

在表单中，我们**使用 JSP 表达式在我们的`<form>`和`<select>`标签中输出所需的值**:

```
 <form action="<%= request.getRequestURI() %>" method="post">
      <select name="deleteid" size="<%= Math.max(2, toDoList.getItemCount()) %>">
        ...
      </select>
      <input type="submit" value="Delete Selected Item" />
    </form>
```

最后，我们需要为待办事项列表中的每一项输出一个`<option>`标签。代码再次与我们的 servlet 非常相似，因为**我们使用了一个`while`循环来完成工作:**

```
 <%
        while (toDoItems.hasNext()) {
          ToDoItem toDoItem = (ToDoItem) toDoItems.next();
          %>
          <option value="<%= toDoItem.getId() %>">
            <%= HTMLEscaper.escape(toDoItem.toString()) %></option>
          <%
        }
        %>
```

乍看之下很乱，但是如果你仔细观察(或者更好的是，如果你有一个用不同颜色突出显示 JSP 标签的编辑器)，你可以看到 JSP scriptlets 中的逻辑是怎样的**，代码的其余部分只是带有 JSP 表达式的 HTML，用来输出动态值。**

与第一个相比，添加新待办事项列表项的表单是小菜一碟:

```
 <form action="<%= request.getRequestURI() %>" method="post">
    <input type="text" name="newtodo" />
    <input type="submit" value="Add New Item" />
  </form>
</body>
</html>
```

这就是我们的 JSP 文件！剩下的唯一一件事就是**为我们的应用程序更新`web.xml`配置文件**，以使初始化参数(`jdbcDriver`和`jdbcConnectionString`)可用于我们的 JSP 文件:

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd" version="2.4">
  <display-name>ToDoJSP</display-name>
  <servlet>
    <servlet-name>toDo</servlet-name>
    <jsp-file>/todo.jsp</jsp-file>
    <init-param>
      <description>The JDBC driver class.</description>
      <param-name>jdbcDriver</param-name>
      <param-value>com.mysql.jdbc.Driver</param-value>
    </init-param>
    <init-param>
      <description>The JDBC connection string.</description>
      <param-name>jdbcConnectionString</param-name>
      <param-value>jdbc:mysql://localhost/todo?user=root&amp;password=password</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>toDo</servlet-name>
    <url-pattern>/todo.jsp</url-pattern>
  </servlet-mapping>
</web-app>
```

与我们在 servlet 中使用的`web.xml`文件相比，有两个显著的变化:

*   我们用一个`<jsp-file>`标签来指示 JSP 文件，而不是用一个`<servlet-class>`标签来指示我们为其提供参数的 servlet，**。**

*   **`<servlet-mapping>`中的`<url-pattern>`标签也指向 JSP 文件**。

完成这些更改后，您就可以打包应用程序并将其部署到您的服务器上了。与 servlet 不同，**你不需要预先编译 JSP 文件**——服务器会把它们转换成 servlet，并为你动态编译。然而，您仍然需要编译 JSP 使用的类(`ToDoList`、`ToDoItem`、`HTMLEscaper`)。

与 servlet 一样，使用`jar`命令提示实用程序来**创建一个包含您的应用程序的`WAR`文件:**

```
jar cvf ToDoJSP.war .
```

将这个文件放到 Tomcat 的`webapps`目录中进行部署(或者使用您喜欢的任何 Java 兼容的服务器进行部署)，并编辑部署的`web.xml`文件以匹配您的数据库配置。

然后加载`http://localhost:8080/ToDoJSP/todo.jsp`来测试您的应用程序。**您应该会看到您熟悉的待办事项界面，这次是由 JSP 生成的。**

这里是完成的 WAR 文件，包括源代码，供您使用:

[下载代码](https://www.sitepoint.com/examples/jgrind/ToDoJSP.war) (250KB)

现在，您会回忆起 JSP 的全部目的是**克服将 HTML 代码混合到 servlet 的 Java 代码中的混乱**。如果您已经查看了我们新 JSP 文件的代码，我会原谅您对我们是否已经实现目标的怀疑。

我们的待办事项 JSP 文件是用我所谓的“老派 JSP”写的。这是纯粹的 JSP，因为它最初是在几年前构思的，没有任何现代设施。在接下来的几周里，我将向您展示如何使用 JSP 的当前和高级特性来修改它。首先，我们将把 servlets 带回到画面中(但是以更简单的形式！)，然后我们将看看像 JavaBeans、Java 标准标记库和 JSP 表达式语言这样的标准。

一般来说，我们的目标是去掉 JSP 文件中的所有 Java 代码，或者用设计者更友好的标记结构替换它，或者把它移到外部独立的 Java 类中。当我们完成的时候，你将知道如何写一个 JSP 文件，这样它就和标准的 HTML 文件几乎没有区别了。

## 分享这篇文章