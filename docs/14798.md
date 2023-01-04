# 使用定制标签将 RSS 提要聚合到基于 JSP 的 Web 应用程序中

> 原文：<https://www.sitepoint.com/rss-feeds-jsp-based-web-apps/>

随着大量新闻和博客(Weblog)网站的持续增长，跟踪正在发生的事情可能是一项艰巨的任务。幸运的是，RSS(真正简单的联合)等标准提供了一种从特定网站获取内容并将其聚合到新闻阅读器应用程序中的简单方法。这意味着，你的新闻阅读器会监控你感兴趣的网站，并在新内容发布时下载，而不是你自己去寻找新闻。

这是一个很好的模式，许多人将这个概念带到了网络上，在他们自己的网站上聚合其他内容，并通过网络提供聚合服务。在本文中，我将向您展示如何使用 JSP 定制标记在您自己的基于 JSP 的 Web 应用程序中将这种类型的功能实现为可重用的组件。虽然我假设您对 Java 很熟悉，并且对使用 JavaServer Pages (JSP)构建 Web 应用程序有一定的了解，但是我不会假设您对 JSP 定制标记有任何了解。在我们开始之前，请在这里下载我们将在本文中使用的代码。

##### RSS 和新闻聚合器——简史

RSS 标准已经存在了几年，但是直到最近才真正开始流行起来。这背后的一个原因是，像 MovableType 和 Radio Userland 这样的软件已经让大众可以用一个负担得起的包来写博客。以前，网站的新闻部分包含特定公司或个人的快照，很少更新，这些 RSS 工具允许我们采取更动态的方法，提供了一种简单的机制，通过它可以向网站添加新的新闻项目。

多亏了这些工具，以及通过互联网呈现的大量新闻，保持最新信息比以往任何时候都更加困难。出于这个原因，定义了一种标准格式，允许通过称为新闻阅读器的桌面应用程序聚合新闻。

结果是 RSS，真正简单的聚合格式诞生了。本质上，RSS 只是一个 XML 文档，可以用来描述给定网站上的内容。通常，“内容”意味着新闻条目，但是 RSS 的其他用途包括摘要文章、短篇故事等等。RSS 提要的一个很好的例子是来自 [BBC](http://news.bbc.co.uk/rss/newsonline_world_edition/uk_news/rss091.xml) 的英国新闻。RSS 标准格式的引入使得内容的聚合比以前容易得多。

##### 从 Java 中读取 RSS 提要

因为 RSS 提要只不过是标准化的 XML 文档，所以在任何支持 XML 的语言中阅读和处理 RSS 都相当容易。既然 J2SE 1.4 提供了对 XML 的完整支持，那么使用适当的类读入 XML 文档就是一个简单的问题了。阅读完文档后，在桌面或基于 Web 的应用程序中将其呈现给用户就变得很简单了。

就本文而言，我将展示如何将 RSS 提要中的内容聚合到您自己的 Web 应用程序中。这种技术的使用可以应用到广泛的应用程序中，从聚集来自不同部门的内容的公司内部网站点，到聚集来自朋友和家人的内容的个人网站。

在我们更多地讨论定制标签之前，让我们快速地看一下如何使用 Java 代码读取 RSS 提要。用 Java 代码操作 XML 文档可能很乏味，因此，我选择构建一个非常简单的 RSS 提要对象表示，而不是针对原始 XML 进行编码。第一个类叫做`RssFeed`，它表示一个给定的包含许多条目的 RSS 提要。

```
package rss; 

import java.util.ArrayList; 

import java.util.Collection; 

import java.util.Collections; 

public class RssFeed { 

  private Collection items = new ArrayList(); 

  public void addItem(RssItem item) { 

    items.add(item); 

  } 

  public Collection getItems() { 

    return Collections.unmodifiableCollection(items); 

  } 

}
```

实际上，RSS 提要还有其他特征，但是条目的集合对于我们的目的来说已经足够了。接下来是表示 RSS 提要中的项目的 Java 类。通常，提要中的每一项都会显示几条信息；我选择将它们包装在一个对象中。在这个例子中，与每个条目相关的信息是它的标题和一个链接(URL ),该链接指向网络上的完整故事。

```
package rss; 

public class RssItem { 

  private String title; 

  private String link; 

  public String getTitle() { 

    return title; 

  } 

  public void setTitle(String title) { 

    this.title = title; 

  } 

  public String getLink() { 

    return link; 

  } 

  public void setLink(String link) { 

    this.link = link; 

  } 

}
```

难题的最后一块是实际读取 XML 文档并将其转换成我们的对象表示的类——`RssReader`类。为了避免陷入读取 XML 文件的语法和语义，这个类的实现被省略了。本质上，`read()`方法所做的就是访问指定 URL 上的 RSS 提要，并将提要中包含的每个条目转换成一个`RssItem`对象。

```
package rss; 

public class RssReader { 

  /** 

   * Reads the RSS feed at the specified URL and returns an RssFeed instance 

   * representing it. 

   */ 

  public RssFeed read(String url) { 

    ... body of method omitted ... 

  } 

}
```

这就是我们阅读 RSS 提要所需的所有逻辑。现在让我们来看看如何将它挂接到 JSP 页面中。

##### 从 JSP 中读取 RSS 提要

出于本文的目的，我将假设您已经选择使用 Java Web 技术构建您的 Web 应用程序，特别是，您将使用 JavaServer Pages (JSP)。这项技术的一大优点是，它使得向页面注入动态行为变得很容易——您所需要做的就是到处添加一些 Java。例如，我们可以使用我们刚刚在 JSP 页面中构建的类:

```
<%  

  RssReader reader = new RssReader();  

  RssFeed rssFeed = reader.read("http://www.acme.com/rss.xml");  

  Iterator it = rssFeed.getItems().iterator();  

  while (it.hasNext()) {  

    RssItem rssItem = (RssItem)it.next();  

%>  

    <a href="<%= rssItem.getLink() %>"><%= rssItem.getTitle() %></a>  

    <br />  

<%  

  }  

%>
```

这段代码从指定的 URL 获取 RSS 提要，并使用标准的`java.util.Iterator`类遍历每个条目，显示一个返回完整故事的超链接。

虽然将 Java 代码作为 scriptlet 嵌入是有用的，但它很快就会成为问题——特别是当您需要在 Web 应用程序的其他页面上重用该代码时。毕竟，没有简单的方法可以重用这样的代码；在整个应用程序中简单地复制和粘贴代码最终会导致可维护性问题，因为每次出现脚本时都需要重复修改。理想情况下，我们希望在组件级别重用代码，获取一个给定的组件，并在任何需要的地方使用它。JSP 定制标签就是答案。

##### 将 RSS 阅读器包装在 JSP 自定义标记中

JSP 自定义标记是一种将常见的和重复出现的逻辑包装成一种形式的方法，这种形式可以在 Web 应用程序的页面上重用。在 JSP 规范的当前版本(1.2)中，自定义标记的行为被实现为一个激活特定接口的 Java 类，例如，与实现 servlet 的方式非常相似。这个 Java 类通常被称为标记处理程序。

定制标记和常规 Java 类之间的关键区别在于它们在 JSP 页面中的使用方式。当 Java 代码简单地嵌入到页面中时，定制标记与 XML 语法一起使用。例如，下面是我们完成构建后如何使用定制标记。注意，这里我们有一个开始标签，一些正文内容，然后是结束标签。

```
<rss:rssFeed url="http://www.acme.com/rss.xml">  

  <a href="<%= rssItem.getLink() %>"><%= rssItem.getTitle() %></a>  

  <br />  

</rss:rssFeed>
```

这将获得与我们之前看到的 scriptlet 代码完全相同的结果——标签提供了提要中每个条目的迭代。本质上，标签现在是循环结构，标签的主体内容在每次迭代中被计算，就像 while 循环的内容在每次迭代中被计算一样。定制标记为我们提供了一种构建更干净、更简洁的 JSP 页面的方法，这些页面受益于重用组件的优势——增强的可维护性、质量、可靠性等等。

##### 构建标记处理程序类

既然您已经理解了为什么定制标记是有用的，那么让我们来看看如何构建一个定制标记。正如我前面说过的，行为被包装成一个实现特定接口的常规 Java 类。在定制标签的情况下，必须实现的接口(至少)是来自`javax.servlet.jsp.tagext`包的[标签](http://java.sun.com/j2ee/sdk_1.3/techdocs/api/javax/servlet/jsp/tagext/Tag.html)。这个接口相当简单，并且提供了许多回调方法，当在 JSP 页面上使用标记时，将执行这些方法。在运行时，JSP 页面为给定的定制标记创建标记处理程序类的新实例，并调用回调方法。这一开始听起来可能很复杂，但是一旦你为自己建立了几个标签，这就变得相当简单了。

为了方便起见，JSP 规范还提供了这个接口的基本实现，称为 [TagSupport](http://java.sun.com/j2ee/sdk_1.3/techdocs/api/javax/servlet/jsp/tagext/TagSupport.html) ，这是构建您自己的标签的理想起点。下面的代码片段显示了标记处理程序的源代码的开始，包括所有必需的导入。它还显示了几个属性——我们将在后面看到这些属性的用途。

```
package tagext;  

import java.util.Collection;  

import java.util.Iterator;  

import javax.servlet.jsp.JspException;  

import javax.servlet.jsp.PageContext;  

import javax.servlet.jsp.tagext.TagSupport;  

import rss.RssFeed;  

import rss.RssReader;  

public class RssFeedTag extends TagSupport {  

  private String url;  

  private Iterator iterator;
```

在我之前展示的标签用法示例中，我们看到有一个开始标签、一些正文内容和结束标签。标记的这些不同方面很重要，因为它们定义了 JSP 页面何时触发我们在标记处理程序类中编写的回调方法。我们需要实现的功能与前面显示的功能相同；我们需要从一个 URL 读入一个 RSS 提要，然后遍历提要中的每一项，这样就可以生成一个超链接。使用`TagSupport`类，我们可以使用的三种回调方法是`doStartTag()`、`doAfterBody()`和`doEndTag()`。让我们依次来看一下每一项。

当在 JSP 页面上遇到开始标记时，调用`doStartTag()`方法，对于任何给定的定制标记，只调用一次。

```
 public int doStartTag() throws JspException {  

    RssReader reader = new RssReader();  

    RssFeed feed = reader.read(url);  

    iterator = feed.getItems().iterator();  

    if (iterator.hasNext()) {  

      pageContext.setAttribute("rssItem", iterator.next());  

      return EVAL_BODY_INCLUDE;  

    } else {  

      return SKIP_BODY;  

    }  

  }
```

由于这段代码对每个标签只调用一次，并且是第一个被触发的回调方法，因此我们可以在这里读取 RSS 提要并设置一个迭代器来循环遍历条目集合。如果您将此方法视为 while 循环的开始，第一步是检查集合中是否有项。如果有的话，我们想让第一项在 JSP 页面中可用，这样我们就可以在标记的主体内容中使用它。为此，我们在 JSP `PageContext`上设置了一个属性来引用集合中的第一项。接下来，我们必须告诉 JSP 页面应该评估标签的主体内容，这是通过返回一个常量值`EVAL_BODY_INCLUDE`来实现的。然而，如果集合中没有任何条目，我们告诉 JSP 页面应该通过返回常量值`SKIP_BODY`跳过对正文内容的评估。

下一个感兴趣的回调方法是`doAfterBody()`方法，它在标签的主体内容被评估之后被调用。

```
 public int doAfterBody() throws JspException {  

    if (iterator.hasNext()) {  

      pageContext.setAttribute("rssItem", iterator.next());  

      return EVAL_BODY_AGAIN;  

    } else {  

      return SKIP_BODY;  

    }  

  }
```

一旦评估了正文内容，下一步就是查看集合中是否还有其他条目。如果有，我们想让新的条目对 JSP 页面可用，并通过返回一个常量值`EVAL_BODY_AGAIN`来指示应该重新评估主体内容。在这之后，再次调用`doAfterBody()`方法，看看是否还需要另一次评估。这个序列一直重复，直到集合中不再有条目，在这种情况下，返回一个常量值`SKIP_BODY`。

当所有的计算都完成后，最后的回调方法被执行。

```
 public int doEndTag() throws JspException {  

    return EVAL_PAGE;  

  }
```

这个实现只是告诉 JSP 页面，页面的其余部分应该像平常一样处理。事实上，`TagSupport`类的实现包含了这个方法的相同实现，这意味着我们实际上不需要实现它。然而，我在这里展示它是为了完整性。

我们需要实现的最后一个方法是 URL 的设置器。如果您回顾一下如何在页面上使用标记的示例，您会注意到 RSS 提要的 URL 被指定为自定义标记的一个属性。为了使这些信息对标记处理程序类可用，我们需要像为 JavaBean 属性编写 setter 方法一样编写一个 setter 方法。任何对应于自定义标记属性的 setter 方法都会在回调方法之前被调用，这样它们的值就可以在这些回调方法中使用。

```
 public void setUrl(String url) {  

    this.url = url;  

  }  

}
```

##### 编译标记处理程序类

要编译标记处理程序类，请确保您的类路径中有 Servlet 和 JSP 类。如果您使用的是 Tomcat 4，这些可以在`$TOMCAT_HOME/common/lib/servlet.jar`文件中找到。生成的类文件应该放在 WEB 应用程序的 WEB-INF/classes 目录下，在适当的目录/包结构中。

##### 描述标签

下一步是使用称为标记库描述符或 TLD 文件的 XML 文件来描述定制标记。这一步是必要的，因为它允许我们定义如何在页面上使用定制标记、其属性的名称等等。从顶部开始，我们有所有常见的 XML 头信息:

```
<?xml version="1.0" encoding="ISO-8859-1" ?>   

<!DOCTYPE taglib   

  PUBLIC "-//Sun Microsystems, Inc.//DTD JSP Tag Library 1.2//EN"   

  "http://java.sun.com/dtd/web-jsptaglibrary_1_2.dtd">
```

接下来，我们开始标记库定义。尽管自定义标记是可重用的，但它们必须在标记库的上下文中定义，标记库是一个或多个通常以某种方式相关的标记的集合。这个 XML 块允许我们定义标记库的版本、所需的 JSP 版本、短名称和标记库的描述。

```
<taglib>   

  <tlib-version>1.0</tlib-version>   

  <jsp-version>1.2</jsp-version>   

  <short-name>rss</short-name>   

  <description>   

    Tags used to present RSS information.   

  </description>
```

接下来是标签库中标签的定义。这里我们只有一个，它定义了将在 JSP 页面上使用的标记的名称、标记处理程序类的名称、主体内容类型以及简短描述。

```
 <tag>   

    <name>rssFeed</name>   

    <tag-class>tagext.RssFeedTag</tag-class>   

    <body-content>JSP</body-content>   

    <description>   

      A tag to present a headlines/titles of items from an RSS feed.   

    </description>
```

其中，正文内容可能需要更多的解释。JSP 规范提供了几种可以为定制标记定义的正文内容类型，其中最有用的两种是 empty 和 JSP。body content 类型为 empty 表示页面上将使用不带正文内容的定制标记，这在您只是希望标记执行某种操作时非常方便。另一方面，JSP 的 body 内容类型表示在开始和结束标记之间将使用常规的 JSP 构造。这就是我们在这个例子中所使用的，因为我们希望对 RSS 提要中的每个条目的正文内容进行评估。

XML 文件的下一部分描述了脚本变量，该变量将被引入到页面的标记主体内容中。在标记处理程序代码中，我们从集合中获取下一个 RSS 项，然后将对该对象的引用放在名为 RSS item 的页面上下文中。定制标记可以做的事情之一是使这些属性作为 JSP 页面上的脚本变量可用，这样就可以用`<%= ... %>`的请求时表达式语法来访问它们。在这里，我们指定了变量的名称和类型，以及一个范围`NESTED`来表示变量应该只在开始和结束标记之间是可访问的。

```
 <variable>   

      <name-given>rssItem</name-given>   

      <variable-class>rss.RssItem</variable-class>   

      <scope>NESTED</scope>   

    </variable>
```

标签要描述的最后一个方面是它的属性。在这个例子中，只有一个名为`url`的属性，用于指示 RSS 提要的来源。为了确保标签按预期工作，我们已经声明了在使用标签时必须提供该属性。属性标签的`rtexprvalue`元素表示属性的值必须在 JSP 页面中静态定义。换句话说，属性值不是请求时表达式的结果。

```
 <attribute>   

      <name>url</name>   

      <required>true</required>   

      <rtexprvalue>false</rtexprvalue>   

    </attribute>   

  </tag>   

</taglib>
```

##### 使用标签

出于这个例子的目的，让我们假设 TLD 文件已经保存为 Web 应用程序的`WEB-INF`目录下的`rss.tld`。要使用定制标记，首先需要告诉 JSP 页面在哪里可以找到该标记的描述。这是通过 taglib 指令实现的，其中的`uri`属性指向代表标签库的 TLD 文件，而`prefix`属性说明如何在页面上识别和使用标签库中的标签。然后，使用与前面相同的语法，我们可以使用标记来读取任何网站提供的 RSS 提要，并生成一组指向该网站上当前新闻故事的超链接。

```
<%@ taglib uri="/WEB-INF/rss.tld" prefix="rss" %>   

<rss:rssItems url="https://www.sitepoint.com/rss.php">   

  <a href="<%= rssItem.getLink() %>"><%= rssItem.getTitle() %></a>   

  <br />   

</rss:rssItems>
```

##### 未来的增强

这里给出的标记在实现上相当简单，并且可以进行许多增强。例如，每次请求 JSP 页面时，标记都会打开一个 HTTP 连接来检索 RSS 提要的内容。虽然这对于低流量站点来说没问题，但更好的解决方案是定期缓存提要。这将避免与为每个页面请求打开网络连接相关的性能损失。

此外，标签没有考虑如果网络出错会发生什么。例如，网站可能关闭或无法正常运行。在任何情况下，您可能都希望添加一些错误处理，也许是显示一条消息来提醒用户该提要当前不可用。

##### 摘要

在本文中，我们了解了什么是 RSS，如何阅读 RSS 提要，以及如何将这一功能集成到基于 JSP 的 Web 应用程序中。虽然我们可以使用 Java 代码 scriptlets 将该功能直接构建到 JSP 页面中，但是开发 JSP 自定义标记使我们能够构建更易于维护的组件，并且还具有可重用的优势。

与将 Java 代码嵌入到页面中相比，构建标记处理程序类和编写 TLD 文件确实需要做更多的工作。然而，我相信在可维护性和可重用性方面的好处很容易证明所涉及的额外努力是值得的。

## 分享这篇文章