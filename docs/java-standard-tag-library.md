# Java 标准标记库简介

> 原文：<https://www.sitepoint.com/java-standard-tag-library/>

起初，有了 HTML——每个人都很高兴。然后，有一天，某个聪明的人决定他们的网站需要动态内容，发明了一种生成 HTML 的脚本语言，于是一切都乱套了。几个月内，网页就变成了 HTML、脚本代码和 SQL 语句的丛林。

起初，Java 对这一切的承担是 Servlet。虽然在某些情况下有用，但它被证明是笨重的。救星将以 Java 服务器页面的形式出现。JSP 看起来像脚本语言，行为也像脚本语言，但实际上只不过是编写 Servlets 的一种巧妙方式。JSP 解决了 Servlets 固有的许多问题，但是它们也引入了许多问题。

页面很容易变成一堆 scriptlet 代码和 HTML 片段。更糟糕的是，JSP 允许您不必编写 JavaBeans 来处理您的业务逻辑；将网站的所有功能放入网页本身是一个快速的解决方案，但往往会在你最意想不到的时候反噬你。最后，典型的前端开发人员喜欢 JSP 脚本，就像鱼喜欢自行车一样，避开复杂的向量、请求对象和空指针异常，喜欢简单的乐趣。

我为什么要告诉你这些？因为，就在一切似乎都在走下坡路的时候，出现了一个解决方案！进入 stage left，Java 标准标签库。

##### 认识一下 JSTL

Java 标准标记库(或 JSTL)只不过是一个为我们提供许多标准功能的 Java 标记库。好吧，好吧——换句话说，JSTL 会让你的 JSP 脚本和 HTML 看起来又像普通的老式 HTML 页面了。这是一件好事。您的页面将更具可读性(不再有 scriptlets)，您的代码架构将变得更加整洁(JavaBean 类中不再有 HTML)，最棒的是，与您合作的前端开发人员将像过去一样在午餐时间邀请您去酒吧。

好了，在我们讨论如何安装之前，为了吊起你的胃口，这里有一段你可能在 JSP 页面中看到的相当典型的代码:

```
<% For (Enumeration e = shoppingCart.getProducts(); 

        e.hasMoreElements();) { 

     Product thisProduct = (Product)e.nextElement(); 

%> 

  <br /><%=thisProduct.getDescription()%> 

<% } %>
```

像泥浆一样清澈，对吗？下面是使用 JSTL 编写的相同功能:

```
<c:forEach var="thisProduct" items="${shoppingCart.products}"> 

  <br /><c:out value="${thisProduct.description}"/> 

</c:forEach> 
```

到目前为止，和你一起工作的 HTML 开发人员不仅仅是邀请你去酒吧，他们也在四处走动！

好了，让我们把这个东西安装好。您将需要一个符合 JSP 1.2 规范的应用服务器。对于大多数人来说，这意味着 Tomcat 4.0+。一旦安装了 Tomcat，您将需要下载 JSTL 本身的副本。您可以从 Jakarta Apache 站点获得一份副本(参见本文末尾的参考资料)。点击这里下载本文[的代码。](https://www.sitepoint.com/examples/jspdownload/jstl_code.zip)

1.  在 Tomcat 中创建一个新的 Web 应用程序(您可以在 webapps/目录下创建一个新目录)。对于本文，我们将把 Web 应用程序称为“jstl_stuff”。
2.  打开 JSTL 档案。
3.  在“jstl_stuff”中创建一个“WEB-INF”目录，在“WEB-INF”中创建一个“lib”目录。
4.  将 JSTL 归档文件的 lib 目录中的所有内容复制到<tomcat home="">/WEB apps/jstl _ stuff/we b-INF/lib 中。</tomcat>
5.  重新启动 Tomcat。

为了测试安装，在/jstl_stuff 目录中创建一个名为 index.jsp 的页面，并添加以下代码:

```
<%@ taglib prefix="c" uri="http://java.sun.com/jstl/core" %> 

<c:out value="Doh! It's working!"/>
```

现在尝试浏览到 http://localhost:8080/jstl _ stuff。如果您看到一个页面显示“Doh！起作用了！”除此之外，一切都很完美。如果您看到 java 异常激增，那么一定是哪里出错了，是时候开始检查您的 Tomcat 配置了。

##### 解释的组件

我将引用下面的类(`simpsons.Homer`)作为例子。你可以编译它并把它放在<Tomcat home>/WEB apps/we b-INF/classes/Simpsons 中，或者你也可以自己写。

```
package simpsons;  

import java.util.Vector;  

public class Homer {  

  private int iq;  

  private Vector braincells;  

  public Homer() {  

    iq = 24;  

    braincells = new Vector();  

    braincells.add("Eating");  

    braincells.add("Drinking");  

    braincells.add("Sleeping");  

  }  

  public int getIq() {  

    return iq;  

  }  

  public void setIq(int iq) {  

    if (iq < 100) {  

      this.iq = iq;  

    }  

  }  

  public Vector getBraincells() {  

    return braincells;  

  }  

}
```

注意:在下面的例子中，我们需要声明我们将在 JSP 页面中使用`Homer`类。编译完类后，记得在 JSP 页面的顶部添加下面一行:

```
<jsp:useBean id="homer" class="simpsons.Homer" scope="page" />
```

JSTL 分为 4 个部分:

1.  **核心**–库中使用的主要标签
2.  **XML**-一组 XML 处理标签
3.  **格式**-国际化和格式化标签
4.  **SQL** 用于访问 SQL 数据库的标记

我们将在本文中讨论标签的核心和格式。如果您想掌握其中任何一种，请参阅本文末尾的参考资料链接。

为了通知 Tomcat 我们将在页面中使用 JSTL 核心标签，我们需要在使用 JSTL 标签的每个页面中包含以下`taglib`指令:

```
<%@ taglib prefix="c" uri="http://java.sun.com/jstl/core" %>
```

正如我前面提到的，JSTL 背后的想法是为程序员提供基本的功能，这样就不再需要 JSP 脚本了。例如，如果我们想给一个变量赋值，我们可以使用`set`动作:

```
<c:set var="homer" value="Doh!" />
```

要将一些内容输出到 HTML，我们可以使用`out`动作:

```
<c:set var="homer" value="Doh!" />  

<c:out value="${homer}" />
```

运行这个应该会给我们提供一个简单的“Doh！”

您也可以设置 JavaBean 的属性。例如，如果想要设置`homer`的 IQ 属性，我们可以这样做:

```
<c:set target="${homer}" property="iq" value="34"/>
```

经验丰富的 JSP 编码人员可能会对此感到困惑。这部分代码可能看起来不寻常:

```
${homer} 
```

欢迎来到 JSTL 表情语言！EL 是一种访问 Java 变量的方法，比旧的“JSP 风格”要简单得多。EL 既简单又强大。使用它，我们可以以多种方式访问对象的变量:

```
${homer.iq}
```

上面的代码将访问 homer 对象的`iq`属性。

```
${homer.braincell[1]}
```

上面将返回`homer`对象的脑细胞集合的第二个元素。注意，我们不需要知道(事实上，也不关心)`homer`对象的 braincell 属性是数组还是向量(或者其他任何实现 List 接口的东西)；EL 为我们做了所有的工作。

我们也可以使用 EL 来计算表达式。因此，举例来说，以下将评估为真:

```
${homer.iq < 50}
```

我们现在可以开始使用 EL 和 JSTL 标签来从根本上简化我们的 JSP 页面。

##### 条件运算和迭代运算

大多数情况下，JSP scriptlets 用于执行条件(如果..else)或迭代(for，while)任务。JSTL 核心标签提供了这种功能，而不必求助于 scriptlets。`<c:if ...>`和`<c:choose ...>`标签提供了基本的条件操作。例如，一个简单的 if 语句如下所示:

```
<c:if test="${homer.iq < 50}">   

Doh! My IQ is <c:out value="${homer.iq}"/>                                             

</c:if>
```

要执行 if…else 操作，我们可以使用`<c:choose>`标签，如下所示:

```
<c:choose>   

<c:when test="${homer.iq < 50}">   

  Doh!   

</c:when>   

<c:when test="${homer.iq > 50}">   

  Wohoo!   

</c:when>   

      <c:otherwise>   

    An IQ of 50? Wohoo!    

</c:otherwise>   

</c:choose>
```

可以使用`<c:forEach ...>`标签进行循环:

```
<c:forEach var="braincell" items="${homer.braincells}">

    <br /><c:out value="${braincell}"/>

</c:forEach> /#epc#/

这将显示:
 `Eating   

Drinking   

Sleeping` 
```

标签为我们提供了一些可选的属性。例如，我们可以写:

```
<c:forEach items="${homer.braincells}" var="braincell" begin="1" end="2" varStatus="status">   

  <br />Braincell <c:out value="${status.count}"/>: <c:out value="${braincell}" />   

</c:forEach>
```

上面会显示:
`Braincell 1: Drinking    
Braincell 2: Sleeping`

这里我们使用了`begin`和`end`参数来定义循环的开始和结束位置。我们也使用`varStatus`对象。这个对象提供了一个进入循环状态的接口。查看最后的参考资料，了解更多信息。

##### JSTL 和 URL 生成

JSTL 还提供标签来帮助我们整理 URL 生成。Scriptlet 代码通常用于构造将参数传递给其他页面的 URL，并且可能很快变得混乱。有了 JSTL，我们可以用更简洁的方式构建 URL。例如:

```
<c:url var="thisURL" value="homer.jsp">   

<c:param name="iq" value="${homer.iq}"/>   

<c:param name="checkAgainst" value="marge simpson"/>   

</c:url>   

<a href="<c:out value="${thisURL}"/>">Next</a>
```

上面将生成一个 URL，内容如下:

```
homer.jsp?iq=24&checkAgainst=marge+simpson
```

注意 JSTL 是如何对 URL 中使用的参数`checkAgainst`进行编码的，用一个`+`代替空格。再也不用担心无效的网址！使用`url`标签的一个额外好处是帮助那些浏览器中没有会话 cookies 的人。如果会话 cookies 已被禁用，JSTL 将检查并自动向链接添加一个`sessionId`。

最后，简单提一下格式标签。这些与核心标签是分开的，并且必须与语句一起导入，因此:

```
<%@ taglib prefix="fmt" uri="http://java.sun.com/jstl/fmt" %>
```

上面的内容出现在 JSP 页面的顶部。格式标签提供了强大的文本格式化功能。作为一个简单的实验，尝试在 JSP 页面中运行以下内容:

```
<fmt:formatNumber value="34.5423432426236" maxFractionDigits="5" minFractionDigits="3"/>   

<fmt:formatNumber value="34.5423432426236" type="currency"/>   

<fmt:parseDate value="2/5/53" pattern="dd/MM/yy" var="homersBirthday"/>   

<fmt:formatDate value="${homersBirthday}" dateStyle="full"/>
```

上面的示例解析一个日期，然后以更详细的方式返回该日期。我们甚至可以在日期格式中添加地区细节，例如:

```
<fmt:timeZone value="US/Eastern">   

<fmt:parseDate value="2/5/53" pattern="dd/MM/yy" var="homersBirthday"/>   

<fmt:formatDate value="${homersBirthday}" dateStyle="short"/>   

</fmt:timeZone>
```

将输出:

```
5/2/53
```

##### 有什么大不了的？

如您所见，JSTL 是一个很大的话题。这里我们只涉及了核心库和格式库的一小部分；还有用于处理 XML 的库以及一组用于 JDBC 访问的标记。

到现在为止，你可能在想两件事情中的一件；要么你在想，“太好了，我的页面将不再看起来像一堆 scriptlets，”要么，“这有什么意义？我必须学习所有这些东西，但我真的没有得到任何额外的功能。”

正如我在本文开头提到的，从长远来看，JSTL 真的是为了让你的生活更轻松。使用 JSTL 有助于以下几个方面:

1.  它帮助我们将业务逻辑(分成 JavaBeans)和表示逻辑(分成 JSTL 标签)分开。

*   它允许非 Java 开发人员编写 Java 代码，而不必接触任何 Java。*   它将整理并简化您的 JSP 文件，使它们看起来不像一堆杂乱的 scriptlets。

当你开始使用 JSTL 时，你会注意到它迫使你写出更好、更干净的代码。起初这是一个微妙的变化，但是随着您对标签越来越熟悉，您会发现这比使用 scriptlets 开发要快。

在自己的项目中使用 JSTL 还有第四个原因，这是一个大问题:Java Server Faces。明年(2004 年)的某个时候，将会出现符合 JSP 2.0 规范的应用服务器，允许开发人员开始使用 Sun 大肆宣传的 Java Server Faces 框架。这是一个令人兴奋的发展，JSTL 的开发商将在这方面领先一步；JSF 将大量使用表达语言以及 JSTL 标签本身。现在利用 JSTL 不仅会对你今天的项目有所帮助，它还会在 2004 年 JSF 服务器问世时给你一个好的开端。

##### 资源

一个非常有用的参考是孙的规范 PDF，您可以在:
[、](http://jcp.org/aboutJava/communityprocess/final/jsr052//)
找到它，它是 PDF 格式的，是一个很好的参考。

太阳 JSTL 地区在:
[http://java.sun.com/products/jsp/jstl/](http://java.sun.com/products/jsp/jstl/)

IBM developerWorks 上的一本好的三部分入门书:
[http://www-106 . IBM . com/developer works/Java/library/j-jstl 0211 . html](http://www-106.ibm.com/developerworks/java/library/j-jstl0211.html)

雅加达 JSTL 网站:
[http://Jakarta . Apache . org/taglibs/doc/standard-1.0-doc/intro . html](http://jakarta.apache.org/taglibs/doc/standard-1.0-doc/intro.html)

下载
雅加达实现可以从以下网址下载:
[http://Apache . mirrors . Ili sys . com . au/Jakarta/taglibs/standard-1.0/binaries/Jakarta-taglibs-standard-1 . 0 . 4 . zip](http://apache.mirrors.ilisys.com.au/jakarta/taglibs/standard-1.0/binaries/jakarta-taglibs-standard-1.0.4.zip)

Tomcat 可以在这里下载:
[http://Apache . planet mirror . com . au/dist/Jakarta/Tomcat-4/Tomcat-4 . 1 . 27 . zip](http://apache.planetmirror.com.au/dist/jakarta/tomcat-4/tomcat-4.1.27.zip)

## 分享这篇文章