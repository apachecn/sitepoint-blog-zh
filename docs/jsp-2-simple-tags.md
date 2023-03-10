# JSP 2.0 简单标签解释

> 原文：<https://www.sitepoint.com/jsp-2-simple-tags/>

JSP 2.0 引入了许多新的好东西，其中许多都致力于使开发人员的生活更加轻松。在本文中，我将看看我个人最喜欢的一个:简单的标记处理程序。简单的标记处理程序允许您创建定制标记，其性能优于基于标记文件的解决方案，并且比基于以前的定制标记 API 的标记更容易编写。

由于现在有几种不同的方法来编写自定义标记，我还将提供一些关于如何决定是否使用简单标记处理程序、标记文件以及 Sun Microsystems 现在称为“经典”标记的指针。

让我们先澄清一个潜在的误解。“简单标记处理程序”中的“简单”指的是这种自定义标记可以轻松编写，而不是它们有任何限制。几乎在所有情况下，简单标记都与使用经典标记 API 编写的标记一样强大，唯一的警告是不能在简单标记的主体中包含 scriptlet 代码。但是，您可以包括 JSTL 标签、EL 表达式和其他自定义操作，因此这应该很少会造成问题。

##### 第一步

一个简单的标签处理器子类化一个叫做`'SimpleTagSupport'`的支持类。这个类是`'SimpleTag'`接口的一个非常方便的实现。它提供了该接口所有 5 个方法的实现，其中最重要的是`doTag()`方法。`SimpleTagSupport`中的`doTag()`方法实际上什么也不做——由开发人员决定是否覆盖这个方法并编写标签功能的代码。让我们来看一个例子。下面的代码展示了这个方法的实际应用:

```
package demo.tags; 

import javax.servlet.jsp.tagext.*; 

import javax.servlet.jsp.*; 

public class Greeter extends SimpleTagSupport { 

    public void doTag() throws JspException { 

        PageContext pageContext = (PageContext) getJspContext(); 

        JspWriter out = pageContext.getOut(); 

        try { 

            out.println("Hello World"); 

        } catch (Exception e) { 

            // Ignore. 

        } 

    } 

}
```

这里没有什么特别的东西。这个类简单地使用`doTag()`将“Hello World”打印到输出流中。随着我们的进行，我们将使这个例子生动起来，但是，现在，有一些事情需要注意。

两条 import 语句让我们可以访问所有必需的类；您将需要在您的类路径中有 Servlet 和 JSP `API`类来编译这段代码。Tomcat 用户会在 common/lib 下找到这些，分别是`jasper-api.jar`和`servlet-api.jar`。

出于刚才讨论的原因，这个类扩展了`SimpleTagSupport`类，并希望我们覆盖`doTag()`方法。扩展`SimpleTagSupport`类的另一个结果是容器在`doTag()`之前调用了一个叫做`setJspContext()`的方法，这使得当前的 JSP 上下文信息可以通过`getJspContext()`获得。我们使用这个方法来访问 JSP 的输出流。

##### 将标签映射到类

假设这个类安装在/WEB-INF/classes 下，下一步将是编写一个 TLD 文件。TLD(标签库描述符)是一个 XML 文件，容器使用它将 JSP 中的定制标签映射到它们对应的简单标签处理程序实现类。下面我们看到了`demo.tld`，一个简单的 TLD 文件，当它安装在/WEB-INF/tlds 目录下时，会将一个名为`'greeter'`的定制标签映射到类`'demo.tags.Greeter'`。

```
<?xml version="1.0" encoding="UTF-8"?> 

<taglib version="2.0"  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee web-jsptaglibrary_2_0.xsd"> 

<tlib-version>1.0</tlib-version> 

  <short-name>demo</short-name> 

  <uri>DemoTags</uri> 

  <tag> 

    <name>greeter</name> 

    <tag-class>demo.tags.Greeter</tag-class> 

    <body-content>empty</body-content> 

  </tag> 

</taglib>
```

`'tlib-version'`和`'short-name'`元素非常简单，分别与标签库版本和默认标签前缀相关。然而,`'uri'`元素值得一些讨论。启动时，容器使用自动发现特性将所有的`uri`元素值映射到相应的 TLD；因此，该字符串在应用程序中必须是唯一的。从 JSP 1.2 开始，我们不再需要为了部署定制标记而对 web.xml 文件进行任何编辑，自动发现特性为我们省去了这种不便。

真正有趣的部分是`<tag>`元素的内容。在这里，我们可以给我们的标签起一个名字，定义它相关的`tag handler`类，并决定我们的标签是否允许有正文内容。

下面的代码展示了我们如何在 JSP 中使用示例标签`greeter`。

```
<%@taglib prefix="t" uri="DemoTags"  %> 

<html> 

    <head><title>JSP Page</title></head> 

    <body> 

<!-- prints Hello World. --> 

          <t:greeter /> 

</body> 

</html>
```

由于`taglib`指令的`'uri'`属性，我们已经告诉 JSP 我们的 TLD 文件在哪里，因此，我们的简单的`tag handler`实现类也在哪里。`'uri'`属性直接映射到容器启动时创建的映射之一；容器映射又指向 TLD 信息。

一般来说，您不需要关心这个，但是如果`uri`属性没有解析为容器映射，那么它被认为是一个文件路径。这只有在不同的 TLD 文件中遇到相同的`uri`元素值时才有用。如果遵循诸如使用域名或其他一些唯一的字符串之类的约定，这种情况应该永远不会发生。

##### 处理标签属性

当您将自定义标记配置为使用属性时，它们开始变得更加有趣。为了实现这一点，我们向`tag handler`类添加了实例变量和相应的属性设置器方法。容器为我们调用这些 setter 方法，将自定义标记属性值作为参数传递。

让我们假设我们想让我们的`greeter`标签接受一个属性，这个属性将决定它应该问候谁。我们可以重写`tag handler`类来容纳`'name'`属性，如下所示:

```
public class Greeter extends SimpleTagSupport { 

    private String name = "World"; 

    public void setName(String name){this.name = name;} 

    public void doTag() throws JspException { 

        PageContext pageContext = (PageContext) getJspContext(); 

        JspWriter out = pageContext.getOut(); 

        try { 

            out.println("Hello " + name); 

        } catch (Exception e) { 

            // Ignore. 

        } 

    } 

}
```

我们还需要更新 TLD 文件来处理我们新定义的属性。以下代码显示了更新后的 TLD 的相关部分:

`<tag>
   <name>greeter</name>
   <tag-class>demo.tags.Greeter</tag-class>
   <body-content>empty</body-content>
   <attribute>
       <name>name</name>
       <rtexprvalue>true</rtexprvalue>
       <required>false</required>
    </attribute>
 </tag>`

`attribute`元素设置了一个名为`'name'`的属性，声明它不是必需的，并进一步声明它将接受`'runtime expression values'`。然后，我们可以通过以下任何一种方式使用标签:

```
<t:greeter name="Andy"/> 

<t:greeter name="${param['name']}"/> 

<t:greeter />
```

第一次调用只是打印“Hello Andy”。正如您所料，`setName()`方法是用字符串文字`'Andy'`作为参数调用的。

第二个调用类似，但是通过一个`EL`表达式从传入的请求中获取它的值。您可以通过将`'rtexprvalue'`元素设置为`'false'`来禁用这个特性，或者，因为`false`是默认值，所以可以完全省略这个元素。

最后一次调用没有使用属性；相反，它使用实例变量`'name'`的默认值。您可以通过为`'required'`元素选择一个值`'true'`(默认情况下是`false`)来使该属性成为强制属性，或者您可以通过测试 null 以编程方式测试它的存在性——无论哪种方式对您的应用程序都有意义。

##### 处理正文内容

定制标记经常需要访问它们的主体内容，简单的标记处理程序提供了一种优雅的方式来处理这种需求。首先，需要对 TLD 做一个简单的修改——`body-content`元素需要一个值`'scriptless'`。使用`'scriptless'`时，你可以将模板文本、标准动作和自定义动作放在标签主体中，但 java 代码不行。

容器调用的另一个重要方法是`setJspBody()`。这个方法使得标签的主体内容可以作为任何`EL`表达式、自定义动作和模板文本的可执行片段。您用`getJspBody()`方法访问这个片段，并且您可以使用`JspFragment`对象的`invoke()`方法执行它。以下代码对此进行了说明:

```
public void doTag() throws JspException { 

        JspFragment body = getJspBody(); 

        PageContext pageContext = (PageContext) getJspContext(); 

        JspWriter out = pageContext.getOut(); 

        try { 

            StringWriter stringWriter = new StringWriter(); 

            StringBuffer buff = stringWriter.getBuffer(); 

            buff.append("<h1>"); 

            body.invoke(stringWriter); 

            buff.append("</h1>"); 

            out.println(stringWriter); 

        } catch (Exception e) { 

            // Ignore. 

        } 

    }
```

让我们来分解这个代码。`doTag()`方法从调用`getJspBody()`开始，产生的`JspFragment`存储在一个名为`'body'`的变量中。`JspFragment`有一个有趣的方法叫做`invoke()`，它以一个`java.io.Writer`作为参数。这里是真正重要的一点:当调用`invoke()`时，片段被执行，然后写入这个`Writer`对象。

在简单的情况下，您可以给`invoke()`提供一个 null 参数，让它使用当前 JSP 的编写器，从而将执行的片段直接打印到页面上。但是，在许多情况下，您会希望在将正文内容发送到输出流之前，先以某种方式对其进行处理。

完成这一处理的一种方法是使用一个`StringWriter`并使用它的底层`StringBuffer`。正如您在清单 6 中看到的，我在缓冲区中添加了一个`H1`标记，使用 invoke 来执行并将正文内容写入编写器(及其底层缓冲区)，然后通过添加结束的`H1`标记来结束。对`out.println()`的简单调用负责将处理后的正文内容发送到输出流。

一旦你掌握了使用可执行文件`JspFragment`的要点，操作主体内容就变得轻而易举了。请记住，您可以访问 JSP `pageContext`对象(通过`getJspContext()`)，所以您可以使用它的`setAttribute()`和`getAttribute()`方法将值返回到 JSP，或者将您的标记的功能与它所在的 JSP 协调起来。

##### 做出正确的选择

希望您现在对如何使用简单的标记处理程序编写定制标记有了大致的了解。那么，是时候抛弃标记文件和经典的标记处理程序`API`了吗？这里有几点建议。

如果您的标签必须使用脚本元素(scriptlets)，您将需要使用经典的标签方法。这应该很少成为问题，因为我们已经用`JSTL`、自定义动作和`EL`表达式替换了 scriptlets。

经典标签的一个明显优势是可以优化容器，为它们提供池化特性；另一方面，简单的标记处理程序在 JSP 页面中每次出现时都会被实例化。通常情况下，池涉及的开销比它的价值更多。因此，除非您有一个会产生大量昂贵资源并被重复使用的标记，否则我不会阻止您采用简单的标记处理程序方法。事实上，即使多次调用被证明是昂贵的，您也可以经常坚持使用简单的标记处理程序，并对`PageContext`对象保持引用的能力稍加创新。

当你需要生成大量的标记时，比如 HTML，应该使用标签文件。当更 RAD 的开发方法足够时，它们也很方便。但是，请记住，它们通常比编译形式的标签要慢一些。

当您有大量 Java 代码并且性能很重要时，简单的标记处理程序是一个很好的选择。它们很容易编写，并且有足够的能力来完成任务。

##### 摘要

我们仅仅触及了使用简单标记处理程序的可能性的表面。我建议你参考 JSP 规范了解更多信息。您可能还想看看您的 IDE 在支持定制标记方面提供了什么。例如，Netbeans 4 负责为您编写 TLD 文件，提供自定义标记代码完成，并具有许多其他加快标记开发的功能。

## 分享这篇文章