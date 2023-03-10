# Jakarta Struts 的第一步，第 2 部分

> 原文：<https://www.sitepoint.com/struts-de-mystified-parts-3-4/>

**[上次我们看了 Jakarta Struts](https://www.sitepoint.com/article/struts-first-steps) ，我解释了持久性和业务对象层，我们编写了代码来从数据库中检索主题列表并将它们表示为对象。我们使用了一个 Struts ActionMapping 和一个 ActionForward，以尽可能地使用 JSP 在屏幕上显示主题。现在让我们更详细地看一下这个任务。**

如果您还没有，您可能首先需要下载我们将使用的代码:

*   [下载 struts _ web forum _ jars . zip](https://www.sitepoint.com/examples/struts/struts_webforum_jars.zip  
    )(855 KB)
*   [下载 struts _ web forum _ src . zip](https://www.sitepoint.com/examples/struts/struts_webforum_src.zip
    )(52.2 KB)

##### 显示主题

Struts 附带了一个提供 JavaBeans 相关功能的`bean` taglib 和一个呈现通用 HTML 属性和 JavaScript 事件处理程序的`html` taglib。两者都在`topics.jsp`内部使用，上次提到的`logic` taglib 也是如此。

下面的代码片段(来自`topics.jsp`)说明了`bean:message`标签的使用，该标签用于根据消息关键字和地区呈现国际化消息。这允许我们分离出应用程序中使用的用户界面字符串常量，而不必将它们硬编码到 JSP 中。这些字符串在一个 Java 属性文件中查找，该文件是使用 Struts 配置文件中的`message-resources`标签指定的。

```
<bean:message key="app.doctype" /> 

<html:html locale="true" xhtml="true"> 

  <head> 

    <bean:message key="app.content.language" /> 

    <bean:message key="app.content.type" /> 

    <link rel="stylesheet" type="text/css" 

      href="<html:rewrite page="/assets/css/webforum.css" />" /> 

    <title> 

      <bean:message key="topics.title" /> - <bean:message 

        key="app.title" /> 

    </title> 

  </head>
```

这段代码使用`bean:message`标记来呈现 DOCTYPE 声明，以及 HTML 内容类型和内容语言元声明。使用 XHTML 格式声明和适当的语言环境，`html:html`标签用于在文档的开头呈现 HTML 标签。

在`struts-config.xml`文件中，包含字符串常量的属性文件的声明如下所示:

```
<message-resources parameter="com.johntopley.webforum.view.Resources" 

  null="false"/> 

<message-resources parameter="com.johntopley.webforum.view.GlobalErrors" 

  null="false" key="GlobalErrors"/>
```

此部分必须位于<`action-mappings` >部分之后。它告诉 Struts 期望在文件`Resources.properties`和`GlobalErrors.properties`中找到所有的用户界面字符串常量，并且这两个文件都位于`com.johntopley.webforum.view`包中。

属性不是很直观；将其设置为 false，以确保在 JSP 上显示由于无法在属性文件中找到消息资源而导致的任何错误。这在调试时特别有用。我还声明了我将使用一个单独的属性文件来存储错误字符串常量。`key`属性告诉 Struts，我希望来自第二个属性文件的消息存储在一个单独的名为`GlobalErrors`的捆绑密钥中。一个`bean:message`标签必须有一个`bean="GlobalErrors"`属性来显示来自这个文件的消息。请注意，将错误字符串存储在属性文件中是完全可选的，它与用户界面文本的其余部分是分开的。我这样做是因为我喜欢额外的分离。

在样式表链接语句中使用了`html:rewrite`标记，为样式表的路径生成正确的 URL。然后像平常一样传递给 HTML 链接标签。

我们来看看`Resources.properties`文件。我喜欢使用的一个惯例是在应用程序范围的字符串常量前加上“app”。我给所有其他字符串常量加上前缀，即使用它们的 JSP 的名称。例如,“主题”页面的常量如下:

```
#-- topics.jsp -- 

topics.guest.welcome.1=Welcome Guest. 

topics.guest.welcome.2=Register 

topics.guest.welcome.3= or 

topics.guest.welcome.4=Log in 

topics.guest.welcome.5=to create a new topic. 

topics.heading.column1=Subject 

topics.heading.column2=Replies 

topics.heading.column3=Author 

topics.heading.column4=Posted 

topics.newtopic=New Topic 

topics.notopics=There are no topics to display. 

topics.table.summary=A list of topics and summary information about them 

topics.title=Topics 

topics.viewtopics=View Topics
```

回想一下上次，我们在 HTTP 请求中存储了一个`Posts`对象，它包含一个有序的`Post`对象集合。我们需要遍历这个集合，从每个`Post`对象中检索相关信息。Struts 用来自`logic` taglib 的三个标签来拯救我们。

我们使用`iterate`标签来遍历集合。但是首先，我们需要考虑这样一个事实，我们可能正在处理一个空的集合:可能没有任何主题可以显示。标签`notEmpty`和它的反义词`empty`标签允许基于特定变量是否为空、空字符串、集合或映射对象进行条件处理。下面是来自`topics.jsp`的大纲代码:

```
<logic:notEmpty name="com.johntopley.webforum.postlist" property="posts"> 

  <logic:iterate id="topic" name="com.johntopley.webforum.postlist" 

    type="com.johntopley.webforum.model.Post" 

    property="posts" length="16"> 

    . 

    <%-- There are posts, so display the details. --%> 

    . 

  </logic:iterate> 

</logic:notEmpty> 

<logic:empty name="com.johntopley.webforum.postlist" property="posts"> 

  . 

  <%-- No posts, display a message. --%> 

  . 

</logic:empty>
```

这里发生了很多事情。在`notEmpty`、`iterate`和`empty`标签中使用的`name`属性告诉 Struts 一个 JavaBean 的名称，这个 JavaBean 保存了要迭代的集合。在这种情况下，它是`com.johntopley.webforum.postlist`，您可能还记得它是 HTTP 请求键，我们将`Posts`对象存储在它下面的`ViewTopicsAction`动作类中。我们在这里使用了一点间接方法，因为我们没有直接引用这个键。相反，我们通过`KeyConstants`类中的`POST_LIST_KEY`公共静态变量来引用它。顺便说一下，注意我是如何用包名作为关键字的前缀的，以帮助避免 HTTP 请求中的名称空间冲突。

所有三个标签中都使用了`property`属性。它告诉 Struts 由`name`引用的 bean 上的哪些属性包含集合。该属性的值应该设置为集合类中的访问器(getter)方法的名称，但不带“get”(或者对于布尔属性，不带“is”)。我们的`Posts`类有一个`getPosts`方法，所以我们简单地将`property`设置为值`posts`。

`iterate`标记中的`id`属性在标记体内创建一个局部变量；我们可以用它来引用循环中的当前行。我称它为`topic`。可以把它想象成类似于“for”循环中的“I”变量。我还使用了`length`属性，指定我们只想显示前 16 个主题。

最后，`type`属性是完全限定的类名，我们希望将表示每一行的对象向下转换到这个类名。这很重要；如果我们不执行这种转换，我们将无法访问集合中每个单独的`Post`对象的属性，因为我们仍然要处理`java.lang.Object`。

注意，`notEmpty`和`empty`标记的组合有效地允许我们创建 if/else 结构，而不必求助于 JSP scriptlet 代码。尽量避免 JSP 中的 scriptlets 是一个好习惯——它们应该只包含标记，而不是裸露的 Java 代码。这个有条件的 if/else 处理模式在`logic`标签库中的其他标签中重复。

将主体添加到循环中，最终给出了我们的主题列表:

```
<logic:notEmpty name="com.johntopley.webforum.postlist" property="posts"> 

  <logic:iterate id="topic" name="com.johntopley.webforum.postlist" 

    type="com.johntopley.webforum.model.Post" 

    property="posts" length="16"> 

    <tr> 

      <td class="topics"> 

        <bean:write name="topic" property="subject" filter="true" /> 

      </td> 

      <td class="replies"> 

        <bean:write name="topic" property="replyCount" /> 

      </td> 

      <td class="author"> 

        <bean:write name="topic" property="author" /> 

      </td> 

      <td class="posted"> 

        <bean:write name="topic" property="timestamp" 

          formatKey="app.date.format" /> 

      </td> 

    </tr> 

  </logic:iterate> 

</logic:notEmpty> 

<logic:empty name="com.johntopley.webforum.postlist" property="posts"> 

  <tr> 

    <td class="topics"> 

      <bean:message key="topics.notopics" /> 

    </td> 

    <td class="replies"></td> 

    <td class="author"></td> 

    <td class="posted"></td> 

  </tr> 

</logic:empty>
```

这里要注意的最重要的一点是使用了`bean:write`标记来呈现 JavaBean 的内容，该内容由该标记的`name`属性引用。我们通过将该属性的值设置为由循环中每行的`iterate`标记提供的`topic` bean，将它们连接在一起。因为我们已经将集合中的对象转换为正确的类型，所以使用前面解释的 JavaBeans 约定，`write`标签可以访问每个`Post`对象的属性。

现在，我们已经编写了显示主题列表所需的大部分 JSP。不再拖延，让我们编写代码，使每个主题成为一个超链接。

##### 创建主题超链接

顾名思义，Struts `html:link`标签用于呈现超链接。下面的代码使用标记的`forward`属性来指定包含超链接 URI 的全局 ActionForward 的名称(在本例中为`ViewTopic`):

```
<tr> 

  <td class="topics"> 

    <html:link forward="ViewTopic" name="params"> 

      <bean:write name="topic" property="subject" filter="true" /> 

    </html:link> 

  </td>
```

一会儿我将解释`name`属性的用法。首先，我想解释一下`filter`属性在`bean:write`标签中的作用。很简单，当设置为 true 时，它用等价的实体替换 HTML 保留字符。虽然默认值为 true，但我已经将它包含在代码中，以使过程更加清晰。

现在，回到手头的任务。你可能还记得[这个系列的第一部分](https://www.sitepoint.com/article/struts-first-steps),我们希望当一个主题有新的回复时，主题超链接以未访问过的链接颜色出现。这意味着当有新的回复时，超链接需要微妙地改变。不知何故，我们不仅需要在超链接中编码我们想要查看的主题的惟一 ID，还需要编码另一个查询参数，该参数指示对该主题的回复数量。

`link`标签让我们引用包含查询参数映射的 JavaBean 或者具有一个属性，即*本身*包含这样一个映射。在这种情况下，我们可以创建这样一个 bean，并使用`link`标签中的`name`属性来引用它。在`logic:iterate`循环之外使用了`jsp:useBean`标签来创建一个名为`params`的页面范围 bean，它是一个散列表。

在循环中，我们有一个两行的 scriptlet:

*   将主题的回复数量存储在 HashMap 中名为`r`的键下
*   将主题的文章 ID 存储在 HashMap 中名为`pid`的键下

不幸的是，我无法找到消除 scriptlet 代码的方法。但至少只有两行。我敢肯定，它可以消除使用 JSTL，但这是另一个教程的主题！完整的代码如下所示:

```
<jsp:useBean id="params" class="java.util.HashMap" scope="page" /> 

<logic:notEmpty name="com.johntopley.webforum.postlist" property="posts"> 

  <logic:iterate id="topic" name="com.johntopley.webforum.postlist" 

    type="com.johntopley.webforum.model.Post" 

    property="posts" length="16"> 

    <% 

      params.put("r", topic.getReplyCount()); 

      params.put("pid", topic.getPostID()); 

    %> 

    <tr> 

      <td class="topics"> 

        <html:link forward="ViewTopic" name="params"> 

          <bean:write name="topic" property="subject" filter="true" /> 

        </html:link> 

      </td> 

      . 

      . 

      . 

    </tr> 

  </logic:iterate> 

</logic:notEmpty>
```

实际的超链接看起来像这样:

```
<a href="/webforum/ViewTopic.do?pid=1&amp;r=1">Some Topic</a> 

<a href="/webforum/ViewTopic.do?pid=2&amp;r=18">Another Topic</a>
```

##### 下次

在下一期文章中，我们将研究如何在列出的主题中添加超链接，当点击超链接时，用户可以查看主题文本和任何相关的回复。

## 分享这篇文章