# ASP.NET web . config 文件揭秘文章

> 原文：<https://www.sitepoint.com/web-config-file-demystified/>

XML 的应用已经被集成到。以至于 XML 不再是一个时髦的词汇。您可能知道，微软已经将 XML 作为其。NET 框架。XML 不仅是一种普遍接受的数据交换格式，它还用于存储配置设置。

任何 ASP.NET Web 应用程序的配置设置都可以存储在一个简单的文本文件中。这个名为 Web.config 的文件以易于理解的 XML 格式呈现，可以包含应用程序范围的数据，如数据库连接字符串、自定义错误信息和区域性设置。

因为 Web.config 是一个 XML 文件，所以它可以包含任何有效的 XML 标记，但是根元素应该总是`<configuration>`。嵌套在该标签中，您可以包括各种其他标签来描述您的设置。因为当您开始构建新的 Web 应用程序时，Web.config 文件是一个标准文件，所以让我们来看看 Visual Studio 生成的默认 XML 文件。网络:

```
<?xml version="1.0" encoding="utf-8" ?> 

<configuration> 

  <system.web> 

    <compilation  

         defaultLanguage="c#" 

         debug="true" 

    /> 

    <customErrors  

    mode="RemoteOnly"  

    />  

    <authentication mode="Windows" />  

    <authorization> 

        <allow users="*" /> 

    </authorization> 

    <trace 

        enabled="false" 

        requestLimit="10" 

        pageOutput="false" 

        traceMode="SortByTime" 

    localOnly="true" 

    /> 

    <sessionState  

            mode="InProc" 

            stateConnectionString="tcpip=127.0.0.1:42424" 

            sqlConnectionString="data source=127.0.0.1;Trusted_Connection=yes" 

            cookieless="false"  

            timeout="20"  

    /> 

    <globalization  

            requestEncoding="utf-8"  

            responseEncoding="utf-8"  

   /> 

 </system.web> 

</configuration>
```

有经验的 ASP.NET 程序员会注意到，我省略了文件自动生成的注释标签。我这样做是为了提供这里使用的 XML 的清晰视图。此外，我将在本文后面详细介绍每个配置标记，这种讨论将使注释标记变得过时。

如果您查看示例 XML，您会注意到`<configuration>`标签只有一个子标签，我们称之为 section group，即`<system.web>`标签。一个区间组通常包含设置区间，如:`compilation`、`customErrors`、`authentication`、`authorization`等。这种工作方式非常简单:您只需在适当的设置部分包含您的设置。例如，如果您想为您的 Web 应用程序使用不同的身份验证模式，您可以在身份验证部分更改该设置。

除了标准的 system.web 设置，您还可以使用`<appSettings>`标记定义自己的特定应用程序设置，比如数据库连接字符串。因此，最常见的 Web.config 大纲应该是:

```
<configuration> 

  <system.web> 

    <! -  sections-->    

  </system.web> 

  <appSettings> 

    <! -  sections -->    

  </appSettings > 

</configuration>
```

现在让我们讨论两个小组的细节。

##### `system.web`节组

在这个部分组中，您通常会包括配置设置，在预。NET 时代，您应该在 IIS 管理控制台中的某个位置进行设置。在微软的 MSDN 图书馆，你可以找到 system.web section group 理解的所有标签的概述，但是，根据你的网站的复杂程度，你可能从来没有使用过这些选项的一半。

让我们按照字母顺序，看看您可以在 system.web 部分组中做出的最有价值的调整。

**T2`<authentication>`**

authentication 部分控制 Web 应用程序中使用的身份验证类型，包含在属性模式中。如果任何人都可以访问您的应用程序，您将输入值“无”。如果需要鉴定，您将使用“Windows”、“Forms”或“Passport”来定义鉴定的类型。例如:

```
 <authentication mode="Windows" />
```

```
**<authorization>**

要允许或拒绝某些用户或角色访问您的 web 应用程序，请使用`<allow>`或`<deny>`子标签。

```

```
 <authorization> 

        <allow roles="Administrators,Users" /> 

        <deny users="*" /> 

    </authorization>
```

理解 ASP。NET 的授权模块遍历这些部分，应用对应于当前用户的第一条规则。在本例中，角色为 Administrators 或 users 的用户将被允许访问，而所有其他用户(用*通配符表示)将遇到第二个规则，随后将被拒绝访问。

```
**<compilation>**

在这里，您可以配置 ASP.NET 的编译器设置。您可以在这里使用许多属性，其中最常见的是`debug`和`defaultLanguage`。仅当您希望浏览器显示调试信息时，才将 debug 设置为“true”。由于打开此选项会降低性能，通常您会希望将其设置为“false”。属性告诉 ASP.NET 使用哪种语言的编译器，因为你可以使用任何一种 Visual Basic。例如. NET 或 C#。默认情况下，它的值为`vb`。

```

```
**<customErrors>**

为了向您的最终用户提供定制的、用户友好的错误消息，您可以将该部分的 mode 属性设置为`On`。如果将它设置为`RemoteOnly`，自定义错误将只显示给远程客户端，而本地主机用户将看到难看但有用的 ASP.NET 错误——显然，这在调试时很有帮助。将模式属性设置为`Off`将向所有用户显示 ASP.NET 错误。

如果您在`defaultRedirect`属性中提供一个相对地址(例如，/error404.html)或绝对地址(http://yourdomain.com/error404.html ),应用程序将在出错时自动重定向到这个地址。请注意，相对地址是相对于 Web.config 文件的位置，而不是发生错误的页面。此外，您可以使用`<error>`标签来提供一个`statusCode`和一个`redirect`属性:

```

```
 <customErrors mode="RemoteOnly" defaultRedirect="/error.html"> 

        <error statusCode="403" redirect="/accessdenied.html" /> 

        <error statusCode="404" redirect="/pagenotfound.html" /> 

    </customErrors>
```

```
**<globalization>**

当您想要更改应用程序的编码或文化时，全球化部分非常有用。全球化是一个如此广泛的主题，以至于可以用一整篇文章来专门讨论这个问题。简而言之，这一部分允许您定义服务器应该使用哪个字符集向客户端发送数据(例如 UTF-8，这是默认设置)，以及服务器应该使用哪些设置来解释和显示特定于文化的字符串，如数字和日期。

```

```
 <globalization requestEncoding="utf-8" responseEncoding="utf-8"  

        culture="nl-NL" />
```

编码是通过属性`requestEncoding`和`responseEncoding`完成的。在所有单服务器环境中，这些值应该相等。在此示例中，应用程序区域性设置为荷兰语。如果不提供区域性，应用程序将使用服务器的区域设置。

**T2`<httpRuntime>`**

您可以使用`httpRuntime`部分来配置一些通用运行时设置，其中两个特别方便。

```
 <httpRuntime appRequestQueueLimit="100" executionTimeout="600" />
```

第一个属性指定在流量大时服务器可以在内存中排队的请求数。在本例中，如果已经有 100 个请求等待处理，下一个请求将导致 503 错误(“服务器太忙”)。

`executionTimeout`属性表示在超时之前 ASP.NET 可以处理请求的秒数。

**T2`<sessionState>`**

在 Web.config 文件的这一部分，我们告诉 ASP.NET 在哪里存储会话状态。缺省值是在进程自身中:

```
 <sessionState mode="InProc" />
```

会话变量非常强大，但是也有一些缺点。当 ASP.NET 进程崩溃时，信息会丢失，在 Web 场(多个 Web 服务器)的情况下，会话通常是无用的。在这种情况下，共享会话服务器可以解决您的问题。就这个话题展开讨论超出了本文的范围，但还是值得一提。关于 sessionState 的更多信息可以在 MSDN 图书馆在线找到。

**T2`<trace>`**

应用程序的跟踪日志位于应用程序根文件夹中，名为`trace.axd`。您可以在跟踪部分更改跟踪信息的显示。

您最初将寻找的属性已启用:`localOnly`和`pageOutput`。

```
 <trace enabled="true" localOnly="true" pageOutput="false" />
```

将`localOnly`设置为“false ”,以从任何客户端访问跟踪日志。如果将`pageOutput`的值设置为“真”，跟踪信息将被添加到每个网页的底部。

##### `appSettings`节组

除了我在前面的段落中谈到的网站配置设置，您会知道程序员经常喜欢使用定制的应用程序范围的常量来存储多个页面上的信息。这种自定义常量最吸引人的例子是数据库连接字符串，但是根据您自己的经验，您可能还会想到更多这样的例子。

这些常数的共同点是您希望以编程方式从代码中检索它们的值。Web.config 文件提供了这样做的可能性，但是作为一种安全措施，这些常量必须包含在`<appSettings>`节组中。就像`<system.web>`，`<appSettings>`是 Web.config 的配置根的直接子标签。

典型的自定义节组如下所示:

```
 <appSettings> 

        <add key="sqlConn" value="Server=myPc;Database=Northwind" /> 

        <add key="smtpServer" value="smtp.mydomain.com" /> 

    </appSettings>
```

这个例子显示了键和值可以通过一个`<add>`标签包含在定制的应用程序设置中。在任何网页中访问此类值的方法如下所示:

```
 ConfigurationSettings.AppSettings("sqlConn")
```

是的，就这么简单！请注意，这些设置的值始终是字符串格式。

##### 其他几个问题

我不会在这里深入讨论它们，但是 Web.config 文件可以包含除了前面提到的`system.web`和`appSettings`之外的其他几个节组，比如`configSettings`组。

*   一个 Web 应用程序可以包含多个 Web.config 文件。文件中的设置适用于它所在的目录以及所有子目录。子目录中的 Web.config 文件优先于父目录中指定的设置。
*   Web.config 文件受 IIS 保护，因此客户端无法访问它们。如果您试图检索一个现有的 http://mydomain.com/Web.config 文件，您会看到一个“访问被拒绝”的错误信息。
*   IIS 监视 Web.config 文件的更改，并出于性能原因缓存内容。修改 Web.config 文件后，无需重新启动 Web 服务器。

##### 结束语

在本文中，我已经提到了 Web.config 文件为 ASP.NET 程序员提供的可能性。您可以使用易于访问的 XML 文件来定义您的应用程序设置，而无需使用 IIS 管理控制台。通过 Web.config 文件，ASP.NET 允许您以一种简单明了的方式添加、更改和删除基本配置设置，如身份验证和授权、自定义错误显示和跟踪。

此外，Web.config 文件为您提供了定义所需的任何自定义键的空间，例如数据库连接字符串。我们随后看到的是，只需一行代码，您就可以从应用程序的任何页面中检索所需的信息。

## 分享这篇文章