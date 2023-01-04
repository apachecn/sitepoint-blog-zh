# 大旅行:无 Java JSP 速成班

> 原文：<https://www.sitepoint.com/the-grand-tour-a-crash-course-in-java-less-jsp/>

是时候从 JavaServer 页面中取出 Java 了。

在我的 Java Web 应用程序技术“大旅行”的最后一期中，我们**将我们的待办事项列表 Web 应用程序分成三个部分**:**模型**，管理我们应用程序核心数据的类；**控制器**，处理传入请求并向视图发送显示值的 servlets 而**视图**，JSP 文件从那些显示值生成网页。

虽然这种分离在将 Java 应用程序逻辑从 HTML 用户界面代码中分离出来方面做得很好，但是它仍然在 JSP 文件中留下了一些 Java 代码。这段代码用于获取控制器提供的值，并将它们传递到我们的 HTML 界面中。我们称之为**显示逻辑**。

这一次，我将向您展示如何去除 JSP 文件中的 Java 代码，并用更简单、更强大的替代方法来实现显示逻辑。

让我们首先回顾一下我们示例的 JSP 文件中的 Java 显示逻辑是做什么的。首先，**获取控制器提供的项目列表:**

```
 List toDoItems = (List)request.getAttribute("toDoItems");
  Iterator it = toDoItems.iterator();
```

然后**检查列表中是否有项目:**

```
 if (it.hasNext()) {
```

为了**保持 HTML `<select>`显示为正确大小的标准列表**而不是下拉列表，需要显示逻辑来设置大小属性:

```
<select name="deleteid" size="<%= Math.max(2, toDoItems.size()) %>">
```

然后使用显示逻辑**依次处理每个待办事项列表项目**:

```
 while (it.hasNext()) {
          ToDoItem toDoItem = (ToDoItem) it.next();
```

最后，显示逻辑用于**提取每个待办事项列表项**的 ID 和文本内容，并对后者中可能出现的任何特殊字符进行转义:

```
<option value="<%= toDoItem.getId() %>"><%= HTMLEscaper.escape(toDoItem.toString()) %></option>
```

鉴于这是一个相对简单的应用程序，相当多的 Java 代码仍然停留在一个文件中，该文件表面上是由 Web 设计者创建和编辑的**，他们通常并不精通 Java 语言和 API 的细节。**

那么，该怎么办呢？显然，这种显示逻辑是必需的，但是我们需要一种比在页面中包含 Java 代码更好的方式来实现它。

实际上，网络上有很多由不同团体编写的替代方案。从简单的 **JSP 标记库**(可以与 HTML 混合以实现显示逻辑的定制标记的集合)到完整的 JSP 替换，众多的选项令人汗颜。然而，JSP 2.0 标准提出的解决方案是一个名为 **[JSP 标准标记库](http://java.sun.com/products/jsp/jstl/)【JSTL】**的标记库和一个名为 **[JSP 表达式语言](http://java.sun.com/products/jsp/syntax/2.0/syntaxref207.html)【EL】**的不使用 Java 代码访问 Java 值的语法的组合。

虽然 EL 内置在 JSP 2.0 中，因此可以在 JSP 文件中自动使用， **JSTL 是一个单独的库，在您可以使用它之前，它必须专门包含在您的 Web 应用程序**中。在 Tomcat 5.x 中，您可以从服务器附带的 jsp-examples Web 应用程序中获取 JSTL 1.1 库文件。只需进入服务器安装的 WEB apps/JSP-examples/WEB-INF/lib 子目录，抓取 jstl.jar 和 standard.jar 并将其复制到待办事项 WEB 应用的 we b-INF/lib 子目录中。

现在可以在 JSP 文件中使用 JSTL 标签了。让我们修改 todo.jsp 文件来**产生相同的待办事项列表用户界面，而不需要任何 Java 脚本。**

首先，我们需要**声明我们打算在文件中使用 JSTL 标签。** JSTL 实际上被分成许多子库，所以如果你只是想使用它的几个标签，你不必加载整个库。最常用的标记位于核心子库中，我们可以在文件顶部的@page 指令后立即使用@taglib 指令加载它:

```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

这是我们的 todo.jsp 文件中的第一段 Java 代码。我们用它来获取 Web 应用程序的上下文路径(根)，这样我们就可以**定位页面要使用的 CSS 文件:**

```
 <link rel="stylesheet" type="text/css" href="<%= request.getContextPath() %>/styles.css" />
```

例如，如果我们的 Web 应用程序安装在 http://www.example.com/todo,，那么**应用程序的上下文路径是/todo** ，因此我们的样式表将正确地位于/todo/styles.css

**JSTL 核心库**的`<c:url>`标签让我们自动生成相对于上下文路径的 URL。调整后的代码如下所示:

```
 <link rel="stylesheet" type="text/css" href="<c:url value="/styles.css"/>" />
```

是的，你没看错。这是一个 HTML 标签的属性值中的 JSTL 标签。坚持 XML 的观众可能会对此咬牙切齿，但是 JSP 从来就不是有效的 XML。为了符合 XHTML 标准，您只需要确保代码*生成了*有效的 XML。

我们的 todo.jsp 文件中的下一段 Java 代码更具挑战性。它获取控制器存储在名为 toDoItems 的请求对象属性中的列表对象，然后**检查它是否实际包含任何待办事项:**

```
 <%
  List toDoItems = (List)request.getAttribute("toDoItems");
  Iterator it = toDoItems.iterator();
  if (it.hasNext()) {
    %>
```

**`<c:if>`标签让我们测试一个条件**，并且只在条件为真时输出标签的内容。标签看起来像这样:

```
 <c:if test="<i>condition</i>">...</c:if>
```

但是我们如何指定一个条件呢？这就是 JSP 表达式语言(EL)的用武之地。标签`<c:if>`的测试属性**将一个 EL 表达式作为要测试的条件**。

EL 被特别设计成非常非常擅长从请求、会话和应用程序属性中获取值。事实上，获取 toDoItems 请求属性的 EL 表达式很简单:

```
${toDoItems}
```

出于我们的目的，我们实际上想更进一步，检查 toDoItems 列表中有多少项。为此，**我们需要使用一个 EL 函数，**,它需要在页面顶部添加另一个 JSTL 子库，并带有@taglib 指令:

```
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
```

完成后，我们可以用这个 EL 表达式**获得列表**中的项目数:

```
${fn:length(toDoItems)}
```

我们可以用这个表达式**确定列表是否包含项目**:

```
${fn:length(toDoItems) > 0}
```

我们现在可以**编写`<c:if>`标签**来测试这个条件:

```
 <c:if test="${fn:length(toDoItems) > 0}">...</c:if>
```

接下来，我们将使用 JSTL 而不是替换一些 Java 设计逻辑，而是在我们的 todo.jsp 文件中改进一些纯 HTML 。显示待办事项列表项并允许用户删除它们的表单以这个`<form>`标记开始:

```
 <form action="DeleteItem.do" method="post"></form>
```

只要我们的 JSP 在 Web 应用程序的根目录中，这就很好，但是如果我们将它移动到子目录中，这个标记的 action 属性将不再指向应用程序根目录中的 DeleteItem.do。**为了确保这个属性总是指向正确的 URL，**我们可以使用一个`<c:url>`标签，就像我们在上面的样式表 URL 中使用的一样:

```
 <form action="<c:url value="/DeleteItem.do"/>" method="post">
```

顺便提一下，我们可以对页面上指向 AddItem.do 的第二个标签做同样的修改

todo.jsp 的下一个 Java 显示逻辑有点儿难用 JSTL/EL: 重现

```
 <select name="deleteid" size="<%= Math.max(2, toDoItems.size()) %>">
```

没有与 Java 的 Math.max 方法等价的 JSTL 或 EL，所以要获得一个大小等于 toDoItems 中项目数但不小于 2 的列表，只使用标准的 JSTL/EL 特性，我们需要**更详细地实现这个逻辑:**

```
 <c:set var="selectSize" value="${fn:length(toDoItems)}"/>
      <c:if test="${selectSize < 2}">
        <c:set var="selectSize" value="2"/>
      </c:if>
```

**JSTL`<c:set>`标签让我们设置一个变量**，用于页面其余部分的 EL 表达式中。在本例中，我们将它设置为 toDoItems 中的项目数(这也是通过 fn:length EL 函数获得的)。然后，我们使用一个`<c:if>`来测试值是否小于 2，在这种情况下，我们使用另一个`<c:set>`将变量设置为最小值。

然后，我们可以继续操作，**在我们的`<select>`标签的 size 属性中输出带有表达式**的值:

```
 <select name="deleteid" size="${selectSize}">
```

是的–**您也可以在 HTML 标签属性中使用 EL 表达式。**

不过，这种解决方案相当混乱。如果我们有一个和 Math.max 做同样事情的 EL 函数不是更好吗？事实证明，用你自己的函数扩展 EL 是相当容易的。

首先，您需要**用一个公共的静态方法**创建一个 Java 类，该方法执行您希望 EL 函数执行的操作。对于这个项目，让我们创建一个名为 com.sitepoint.jsp.Functions 的类(在 com/site point/JSP/functions . Java 中)。这个类的代码非常简单:

```
package com.sitepoint.jsp;

public class Functions {
  public static int max(int i1, int i2) {
    return Math.max(i1, i2);
  }
}
```

接下来，您需要**定义一个自定义标记库**来将这个类的 max 方法设置为一个自定义 EL 函数。为此，创建一个名为 functions.tld 的 XML 文件，并将其放在项目的 WEB-INF 目录中。除了第一个标记(总是相同的)，大部分代码都是不言自明的:

```
<?xml version="1.0" encoding="iso-8859-1"?>
<taglib 
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"
  version="2.0">
  <tlib-version>1.0</tlib-version>
  <short-name>Functions</short-name>
  <uri>https://www.sitepoint.com/jsp/taglibs/functions</uri>
  <function>
    <name>max</name>
    <function-class>com.sitepoint.jsp.Functions</function-class>
    <function-signature>int max(int, int)</function-signature>
  </function>
</taglib>
```

这个文件将 URL(https://www.sitepoint.com/jsp/taglibs/functions)形式的唯一标识符与 EL 函数库关联起来。这个库中的一个函数将被称为 max ，并将调用 com.sitepoint.jsp.Functions 类的 max 方法。

为了在我们的 JSP 中使用这个库，我们需要**为它添加一个@taglib 指令**到页面的顶部:

```
<%@ taglib prefix="spfn" uri="https://www.sitepoint.com/jsp/taglibs/functions" %>
```

我们现在可以使用一个 EL 表达式来计算我们的`<select>`标签**的大小属性**

```
 <select name="deleteid" size="${spfn:max(2,fn:length(toDoItems))}">
```

接下来在我们的 Java 显示逻辑中，我们有**一个 while 循环，它遍历待办事项列表项，**依次取出每一项进行处理:

```
 <%
        while (it.hasNext()) {
          ToDoItem toDoItem = (ToDoItem) it.next();
          %>
```

用 **JSTL 的`<c:forEach>`标签:**做这件事简单得几乎令人羞愧

```
 <c:forEach var="toDoItem" items="${toDoItems}">
```

同样，我们使用简单的 EL 表达式${toDoItems}来访问控制器提供的请求属性。**这个标签将为列表中的每一项输出一次内容，**将该项分配给由 var 属性指定的 EL 变量名(本例中为 toDoItem)。

我们最后的 Java 显示逻辑花絮用于**输出包含在列表中的 ToDoItem 对象**的 ID 和文本:

```
 <option value="<%= toDoItem.getId() %>"><%= HTMLEscaper.escape(toDoItem.toString()) %></option>
```

这就是 EL 真正发挥作用的地方。除了极大地简化了从请求属性中提取值的过程，它还非常善于从这些值中提取值！让我告诉你我的意思…

虽然我当时没有提到，但是 ToDoItem 类是根据**JavaBean s 标准设计的。尽管它有一些更复杂的部分，JavaBeans 规范(不要与 Enterprise JavaBeans 混淆，这是一个完全不同的规范)主要只是**类和方法的命名约定。****

在 JavaBeans 规范下，如果一个对象有一个属性(一个可以读写的值)，那么**它应该有 getPropertyName 和 setPropertyName 方法**来访问它。如果它是只读属性，那么它应该只有一个 getPropertyName 方法。如果该属性包含布尔值，则应改为调用“get”方法 isPropertyName。

规范中还有很多内容，但这些都是最基本的。因为 ToDoItem 类有一个 getId 方法，所以它有一个名为 Id 的只读属性。

现在，所有这一切的要点是，EL 是 JavaBeans 通的。这使得获取 JavaBean 属性的值变得非常容易。所以为了**输出 ToDoItem 的 id 属性的值，**我们可以使用这个表达式:

```
${toDoItem.id}
```

至于 ToDoItem 的文本值，EL 将使用其 toString 方法自动将任何对象转换为字符串，因此我们可以只使用${toDoItem}。然而，我们的 Java 代码也使用 HTMLEscaper 类来对任何可能被解释为 HTML 代码的字符进行转义。但是等等:JSTL/埃尔也可以做到这一点！

实际上有两种方法可以用 JSTL/EL 来转义 HTML 特殊字符。第一种是**使用带有 escapeXml 属性的 JSTL `<c:out>`标签:**

```
<c:out value="${toDoItem}" escapeXml="true"/>
```

更方便的是**EL 函数 fn:escapeXml:**

```
${fn:escapeXml(toDoItem)}
```

下面是如何在新的 todo.jsp 文件中**输出我们的待办事项列表项目**:

```
 <option value="${toDoItem.id}">${fn:escapeXml(toDoItem)}</option>
```

由于我们不再使用 HTMLEscaper 类，我们可以从 Web 应用程序中删除相关文件。我们还可以删除@page 指令，该指令导入了 Java 显示逻辑代码使用的这个类和其他类:没有 Java 代码，不需要导入！

做了所有这些更改后，**我们完成的 todo.jsp 文件**看起来是这样的:

```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
<%@ taglib prefix="spfn" uri="https://www.sitepoint.com/jsp/taglibs/functions" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html >
<head>
  <title>To-Do List</title>
  <meta http-equiv="content-type"
      content="text/html; charset=iso-8859-1" />
  <link rel="stylesheet" type="text/css"
      href="<c:url value="/styles.css"/>" />
</head>
<body>
  <c:if test="${fn:length(toDoItems) > 0}">
    <form action="<c:url value="/DeleteItem.do"/>" method="post">
      <select name="deleteid" size="${spfn:max(2,fn:length(toDoItems))}">
        <c:forEach var="toDoItem" items="${toDoItems}">
          <option value="${toDoItem.id}">${fn:escapeXml(toDoItem)}</option>
        </c:forEach>
      </select>
      <input type="submit" value="Delete Selected Item"/>
    </form>
  </c:if>
  <form action="<c:url value="/AddItem.do"/>" method="post">
    <input type="text" name="newtodo"/>
    <input type="submit" value="Add New Item"/>
  </form>
</body>
</html>
```

虽然肯定是新的，但是这个文件中的 JSTL/EL 语法比以前的 Java 代码更容易被一般的网页设计者掌握。

下面是我们更新的应用程序的**文件和目录结构**:

```
/todo.jsp
/styles.css
/WEB-INF/functions.tld
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
/WEB-INF/classes/com/sitepoint/jsp/Functions.class
/WEB-INF/classes/com/sitepoint/jsp/Functions.java
/WEB-INF/lib/jstl.jar
/WEB-INF/lib/mysql-connector-java-*version*-bin.jar
/WEB-INF/lib/standard.jar
```

[下载代码](https://www.sitepoint.com/examples/jgrind/ToDoMVC2.war) (759KB)

就 JSP 最佳实践而言，这个更新的待办事项列表示例是遵循官方规范所能获得的最好的结果。不可否认，我们已经有了一个非常强大的网络应用平台。您当然可以只使用 Java 类作为模型，Java servlets 作为控制器，JSP 使用 JSTL/EL 作为视图来构建大型应用程序，但是还有更强大的选择。

您可以使用一个**持久性框架**将您的数据库与一组自动生成的 Java 对象自动同步，而不是在您的模型类中手工编码数据库访问。[冬眠](http://www.hibernate.org/)是这个地区白天的宠儿。

您可以使用像 [Struts](https://www.sitepoint.com/article/struts-first-steps) 这样的 **Web 应用程序框架**来管理复杂性，而不是将所有的应用程序逻辑写成一组错综复杂的 servlets。

你可以**使用其他标签库**，如 [JavaServer Faces](http://java.sun.com/j2ee/javaserverfaces/) 或众多[开源标签库](http://jakarta.apache.org/taglibs/)中的一个，而不是依赖 JSTL 标签和用你自己的定制函数增强的 EL 表达式，或者你可以远离 JSP 和**使用模板系统**，如 [Velocity](http://jakarta.apache.org/velocity/) 或 [XMLC](http://xmlc.objectweb.org/) 。

你几乎可以说，用 Java 构建 Web 应用程序的方法比不用 Java 要多得多！在接下来的几个月里，我将以批判的眼光审视这些选项，并尝试建议您在什么样的项目中使用哪些选项。

## 分享这篇文章