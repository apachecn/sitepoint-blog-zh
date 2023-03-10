# 加速你的网站！8 个 ASP.NET 性能提示

> 原文：<https://www.sitepoint.com/aspnet-performance-tips/>

现在你已经对你的网站做了最后的润色，并向全世界发布了它，名誉、财富和成功肯定会随之而来，不是吗？

不幸的是，您的 web 应用程序的成功可能会导致一些不尽如人意的事情——性能和伸缩问题。在一个传统的桌面应用程序中，一千个用户转化为一千台运行的客户端计算机，分担运行应用程序的负载。应用程序有效地分布在所有用户的机器上。但是，对于 web 应用程序来说，这一千个用户通常是由一台机器来服务的——您的 web 服务器。

对于 web 应用程序来说，成功是有代价的:带宽和服务器硬件的代价。然而，有一些聪明的方法可以减少——有时甚至消除——这些问题。在这一章中，我们将会看到一些提高 ASP.NET 网站性能的不同方法，这些方法摘自 ASP.NET 2.0 选集 [*、&黑客*T3【101 个基本技巧】。随意下载这一章——以及其他三章——作为离线参考。](https://www.sitepoint.com/books/aspnetant1/)

##### 我如何确定要优化什么？

您希望您的 web 应用程序是最好的，对吗？像我们所有人一样，你说的“最好”是指“最快”还有什么比优化一切更好的方法来创建速度惊人的应用程序呢？优化，优化，优化——对吗？不对。

过早的优化指的是在你理解问题之前，或者甚至在问题出现之前就修复性能问题，这是一个坏主意。

这并不是说您应该编写草率、低效的代码。我的观点是，你应该信任 ASP.NET 框架，并利用那些使它成为一个非常好的开发环境的特性，直到你遇到问题。一旦你遇到一个问题，你应该花时间去理解这个问题是什么，只有到那时，你才应该开始考虑如何最好地解决它。Joseph m . new merger 博士的文章“优化:你最大的敌人”，对黑暗中优化的危险进行了精彩的概述。

本章中的技巧为一些常见的性能问题提出了相当轻量级的解决方案。我已经避开了剧烈的变化，因为我不希望你为了减少 2%的页面加载时间而导致开发或维护时间翻倍。虽然可以完全绕过内置的 ASP.NET 页面机制(通过使用响应。写，或者构建一个基于 ASHX 的站点)，我不喜欢这些方法。ASP.NET 系统作为一个整体已经调整和改进了超过五年，它的速度相当快，直接开箱即用。很有可能试图对其进行改进会导致网站运行更慢，更难维护。

所以，抛开这些不谈，让我们假设你的一些页面运行缓慢。您如何确定要修复什么？

***解***

通过测量站点的实际速度来隔离站点中的瓶颈。这个练习可以使用日志、数据库概要和跟踪来执行。

我们已经在《第 13 章，处理错误》中讨论了使用 log4net 记录日志的任务。如果您怀疑数据库是导致速度变慢的原因(例如，您知道您的应用程序使用了一些大型查询)，您可以在调试模式下逐句通过页面，查看数据库调用是否需要很长时间才能返回，或者您可以使用“如何加快数据库查询速度”一节中讨论的 SQL Server Profiler 在本章的后面。对于页面中发生的事情的非常基本的分析，您可以使用 ASP.NET 跟踪；虽然它远不如一个全功能的日志系统好，但它很容易实现，并能为您提供大量的时间信息。

使用跟踪的第一步是养成添加跟踪语句的习惯。每当您认为在调试将来的问题时可能想看到某些东西时，就写入跟踪。在`Web.config`中启用跟踪之前，跟踪对您的站点没有任何真正的性能影响，当您需要解决问题时，您会很高兴有它在。

在《第 13 章，处理错误》中有更多关于跟踪的信息，但是总的想法是你可以像这样写跟踪对象:

```
Trace.Write("Here's a trace message.");
```

默认情况下，跟踪是禁用的；当您想让您的`Trace.Write`语句真正做一些事情时，您需要在`Web.config`文件中打开跟踪，如下所示:

```
Example 15.1\. Web.config (excerpt) 

<?xml version="1.0"?> 

<configuration> 

  <system.web> 

    <trace enabled="true" 

        mostRecent="true" 

        localOnly="true"/> 

  </system.web> 

</configuration>
```

在这个解决方案中，我们将查看一个执行一些不同操作的示例页面——它调用一个 web 服务，从数据库中检索一些数据，并抛出一个异常。我们将使用的每个函数在开始和结束时都会写一条跟踪消息，通过一个简单的名为`writeTrace`的实用方法。然而，它有一个稍微复杂的方面——它使用系统。诊断对象来计算方法名，所以我们不必传入它。我们示例页面的代码如下:

```
Example 15.2\. Trace.aspx.cs (excerpt) 

using System; 

using System.Web; 

public partial class _Default : System.Web.UI.Page 

{ 

  protected void Page_Load(object sender, EventArgs e) 

  { 

    hitAWebservice(); 

    getSomeData(); 

    doSomeProcessing(); 

    breakSomething(); 

    displayTheResults(); 

  } 

  private void getSomeData() 

  { 

    writeTrace(true); 

    simulateWaiting(8000); 

    writeTrace(false); 

  } 

  private void hitAWebservice() 

  { 

    writeTrace(true); 

    Trace.Write("A message to demonstrate tracing."); 

    simulateWaiting(2000); 

    writeTrace(false); 

  } 

  private void doSomeProcessing() 

  { 

    writeTrace(true); 

    simulateWaiting(1000); 

    writeTrace(false); 

  } 

  private void displayTheResults() 

  { 

    writeTrace(true); 

    simulateWaiting(500); 

    writeTrace(false); 

  } 

  private void breakSomething() 

  { 

    writeTrace(true); 

    try 

    { 

      int superBig = int.MaxValue; 

      superBig += 1; 

    } 

    catch (Exception ex) 

    { 

      Trace.Warn("Exception", "Oops", ex); 

    } 

  } 

  private void writeTrace(bool enteringFunction) 

  { 

    if (!Trace.IsEnabled) 

    return; 

    string callingFunctionName = "Undetermined method"; 

    string action = enteringFunction ? "Entering" : "Exiting"; 

    try 

    { 

      //Determine the name of the calling function. 

      System.Diagnostics.StackTrace stackTrace = 

      new System.Diagnostics.StackTrace(); 

      callingFunctionName = 

      stackTrace.GetFrame(1).GetMethod().Name; 

    } 

    catch { } 

    Trace.Write(action, callingFunctionName); 

  } 

  /// <summary> 

  /// Wait a bit. 

  /// </summary> 

  /// <param name="waitTime">Time in milliseconds to wait.</param> 

  private void simulateWaiting(int waitTime) 

  { 

    System.Threading.Thread.Sleep(waitTime); 

  } 

}
```

好的，我们已经准备好了追踪报告。现在，让我们假设这个页面花费了异常长的时间来加载，我们希望找到问题的根源。启用跟踪后，我们可以简单地加载页面，然后在我们的网站中浏览到`Trace.axd`;在`http://localhost:1209/MySite/Trace.axd`。

图 15.1 显示了从前面的代码返回的 Trace.axd 输出的第一部分。

![Figure 15.1\. Trace output for our sample page](img/60f71a25bbeae9133f502be834244391.png)

表 15.1 显示了跟踪输出的相关部分。

马上，我们可以看到页面加载的哪一个方面占用了大部分时间— `getSomeData`需要八秒钟来执行。如果没有这些信息，我们可能会认为 web 服务调用有问题，并花费宝贵的时间来解决错误的问题。这个例子展示了，有了一些真实的信息，我们如何开始解决正确的问题。

![Snapshot of Trace Output for our Sample Page](img/e5e7b6c936606264aaf6618f9868a08b.png)

##### 如何减小视图状态的大小？

ASP.NET 控件的一个便利之处是它们可以跨回发保持状态——我们在第 6 章“保持状态”中已经深入讨论过这个主题。当然，这是一个有代价的特性—为了实现它，我们向页面添加了一个隐藏字段来存储客户端和服务器之间传输的控件设置，但是根据页面使用的控件，视图状态有时会变得非常大。

减小视图状态大小的一个显而易见的方法是，如果不需要视图状态，就将其关闭。这种调整既可以在页面级别执行，也可以在控制级别执行。如果出于某种原因，您不能禁用视图状态(例如，您的页面使用依赖于视图状态的控件)，您可以采取一些其他步骤来至少减少它对页面大小的影响。

***解***

有两种方法可以减少视图状态对页面大小的影响，要么压缩视图状态，要么将其存储在服务器上。

**压缩视图状态**

下面简单的`CompressedViewStatePage`类实现了页面视图状态的基本 GZIP 压缩。它将我的示例页面上的`ViewState`对象的大小从 20，442 字节减少到了 6，056 字节——令人印象深刻地减少了 70%!这是这个班最辉煌的时刻:

```
Example 15.3\. CompressedViewStatePage.cs (excerpt)  

using System;  

using System.IO.Compression;  

using System.IO;  

using System.Web.UI;  

public class CompressedViewStatePage : System.Web.UI.Page  

{  

  static public byte[] Compress(byte[] b)  

  {  

    MemoryStream ms = new MemoryStream();  

    GZipStream zs = new GZipStream(ms, CompressionMode.Compress);  

    zs.Write(b, 0, b.Length);  

    return ms.ToArray();  

  }  

  static public byte[] Decompress(byte[] b)  

  {  

    MemoryStream ms = new MemoryStream(b.Length);  

    ms.Write(b, 0, b.Length);  

    // last 4 bytes of GZipStream = length of decompressed data  

    ms.Seek(-4, SeekOrigin.Current);  

    byte[] lb = new byte[4];  

    ms.Read(lb, 0, 4);  

    int len = BitConverter.ToInt32(lb, 0);  

    ms.Seek(0, SeekOrigin.Begin);  

    byte[] ob = new byte[len];  

    GZipStream zs = new GZipStream(ms, CompressionMode.Decompress);  

    zs.Read(ob, 0, len);  

    return ob;  

  }  

  protected override object LoadPageStateFromPersistenceMedium()  

  {  

    byte[] b = Convert.FromBase64String(Request.Form["__VSTATE"]);  

    LosFormatter lf = new LosFormatter();  

    return lf.Deserialize(Convert.ToBase64String(Decompress(b)));  

  }  

  protected override void SavePageStateToPersistenceMedium(  

      object state  

  )  

  {  

    LosFormatter lf = new LosFormatter();  

    StringWriter sw = new StringWriter();  

    lf.Serialize(sw, state);  

    byte[] b = Convert.FromBase64String(sw.ToString());  

    ClientScript.RegisterHiddenField("__VSTATE",  

    Convert.ToBase64String(Compress(b)));  

  }  

}
```

要使用 GZIP 压缩，只需从类中继承一个特定的页面，如下所示:

```
public partial class MyPage : CompressedViewStatePage
```

如果您担心压缩视图状态对性能的影响，不必担心。在任何给定的 web 服务器上，带宽更有可能是比 CPU 时间更大的瓶颈。尽管这一规则也有例外，但 GZIP 算法在今天的 CPU 上速度惊人。此外，如果你的服务器的 CPU 一直在 100%运行，你要担心的问题远比一堆页面的大小更严重。

这种压缩算法也可以实现为 HTTP 模块，然后通过简单的 Web.config 修改就可以应用于整个站点。如果你感兴趣的话，我建议你试着把这个模块作为一个练习来实现。MSDN 关于构建 HTTP 模块的文章是一个很好的起点。

**在服务器上存储视图状态**

减少视图状态对页面大小的影响的第二个选项是防止将视图状态数据一起发送到客户端，而是将数据存储在服务器上。

下面的`ServerViewStatePage`类允许我们使用会话对象来存储视图状态:

```
Example 15.4\. ServerViewStatePage.cs (excerpt)  

using System;  

using System.Web.UI;  

using System.Configuration;  

using System.IO;  

public class ServerViewStatePage : System.Web.UI.Page  

{  

  private const string _configKey = "ServerViewStateMode";  

  private const string _formField = "__SERVERVIEWSTATEKEY";  

  private string ViewStateData  

  {  

    get { return Request.Form[_formField]; }  

    set { ClientScript.RegisterHiddenField(_formField, value); }  

  }  

  private string PersistenceType  

  {  

    get { return (ConfigurationManager.AppSettings[_configKey]  

    ?? "").ToLower(); }  

  }  

  private object ToObject(string viewstate)  

  {  

    byte[] b = Convert.FromBase64String(viewstate);  

    LosFormatter lf = new LosFormatter();  

    return lf.Deserialize(Convert.ToBase64String(b));  

  }  

  private string ToBase64String(object state)  

  {  

    LosFormatter lf = new LosFormatter();  

    StringWriter sw = new StringWriter();  

    lf.Serialize(sw, state);  

    byte[] b = Convert.FromBase64String(sw.ToString());  

    return Convert.ToBase64String(b);  

  }  

  private string ToSession(string value)  

  {  

    string key = Guid.NewGuid().ToString();  

    Session.Add(key, value);  

    return key;  

  }  

  private string FromSession(string key)  

  {  

    string value = Convert.ToString(Session[key]);  

    Session.Remove(key);  

    return value;  

  }  

  protected override object LoadPageStateFromPersistenceMedium()  

  {  

    switch (PersistenceType)  

    {  

    case "session":  

      return ToObject(FromSession(ViewStateData));  

    default:  

      return base.LoadPageStateFromPersistenceMedium();  

    }  

  }  

  protected override void SavePageStateToPersistenceMedium(  

      object ViewStateObject  

  )  

  {  

    switch (PersistenceType)  

    {  

      case "session":  

        ViewStateData = ToSession(ToBase64String(ViewStateObject));  

        break;  

      default:  

        base.SavePageStateToPersistenceMedium(ViewStateObject);  

        break;  

    }  

  }  

}
```

要使用`ServerViewStatePage`，只需从这个类继承一个特定的页面，就像这样:

```
public partial class MyPage : ServerViewStatePage
```

该类通过`Web.config` : `ServerViewStateMode`中的单一设置进行配置。一旦配置了这个设置，您会注意到`ViewState`对象从页面中消失了——取而代之的是一个简单的 ID，用于在服务器上查询页面视图状态的内容，在`Session`对象中。如果您不喜欢将视图状态存储在`Session`中，这个类可以很容易地扩展到您喜欢的任何地方存储视图状态——在文件系统中，在 ASP.NET 缓存中，或者在数据库中。

像往常一样，这里没有免费的午餐。将视图状态推送到服务器并存储在会话中的决定有其自身的缺点。例如，如果 IIS 工作进程回收，会话对象可能会丢失(这种丢失在 IIS 中经常发生，除非您禁用了此默认行为)。此外，对底层应用程序文件的任何更改(比如编辑`Web.config`，或者向应用程序的 bin 文件夹添加新的二进制文件)也会导致 web 应用程序回收和会话数据丢失。如果您使用多台 web 服务器(比如在 web farm 环境中)，您将需要管理存储在数据库中的任何共享会话状态。

##### 我如何减少我的网站使用的带宽？

ASP.NET 从开发者那里抽象出许多传统的 web 开发细节。只需在表单上拖放几个控件，设置一些属性，写一点代码，然后——嘭！—你已经有了一个功能正常的网站。

然而，这并不意味着产生的 HTML 标记一定是高效的或小的。包含超过 100 千字节标记的 ASP.NET 页面并不罕见。我建议您密切关注 ASP.NET 网页产生的 HTML 标记——控制这些文件大小有时需要额外的努力，这也是我们在第 9 章“ASP.NET 和 web 标准”中讨论 web 标准主题的原因之一。

***解***

ASP.NET 带宽控制的第一条规则是知道你的页面有多大。在 Internet Explorer 中，文件>属性对话框会告诉你当前页面产生了多少千字节的 HTML 标记，如图 15.2 所示。Firefox 有一个类似的对话框，如图 15.3 所示，可以通过选择工具>页面信息来访问。

![Figure 15.2\. Viewing page information in Internet Explorer](img/b656947b8b1b454364adf130ce7e874c.png)

![Figure 15.3\. Viewing page information in Firefox](img/0aea213449f2689b74a16994ef9d5899.png)

但是，请注意，Firefox 中的 Size 字段报告的数字比 IE 7 中的小得多——13，976 字节对 49，774 字节。这是因为 Firefox 显示的是通过网络传输的实际字节数，而 IE 7 显示的是浏览器收到页面后的大小。

这种差异怎么可能存在？嗯，ASP.NET 网站在发送页面之前使用 HTTP 压缩来减小页面大小。HTTP 压缩是 W3C 标准，它允许服务器向客户机提供 HTML 内容的 GZIP 压缩版本，代价是 CPU 时间增加很少。客户端接收压缩的内容，然后在呈现页面之前动态解压缩。很快，您可以看到这只是一种简单的方法，可以将您使用的带宽量减少令人印象深刻的 72% —只需打开开关，为您的网站启用 HTTP 压缩。

您可以通过两种方式启用 HTTP 压缩。第一个发生在 web 服务器级别；第二个是通过 ASP.NET 应用程序级别的自定义 HTTP 模块实现的。

**在 IIS 6 中启用 HTTP 压缩支持**

使用 IIS 服务管理器在 IIS 6 中启用 HTTP 压缩。右键单击网站的节点，然后选择“属性”。服务选项卡包含与压缩相关的设置，如图 15.4 所示。

*只在 IIS 6 中需要手动配置*
没错:只需要在 IIS 6 及更早版本中配置 IIS 启用 HTTP 压缩，因为 IIS 7 默认启用静态压缩。Windows Server 2008(在撰写本文时尚未发布)可能会提供一个用户界面来配置动态 HTTP 压缩，但 Vista 的 IIS 管理器没有。

![Figure 15.4\. Configuring HTTP compression in IIS 6](img/6411908c3ec900342e505ec3f7f49735.png)

GUI 中可用的压缩设置起作用；但是，它只影响静态内容，如 HTML 页面和 CSS 文件。此设置不会压缩 ASPX 页面中的动态内容。我们必须借助编辑元数据库(用于配置和元数据存储的 IIS 数据库)来部署动态内容压缩:

*   在记事本中打开元数据库。对于 IIS 6，这位于`C:WINDOWSsystem32inetsrvMetaBase.xml`。对于 IIS 5，该文件是一个二进制文件，所以你需要[下载元数据编辑工具，而不是](http://www.microsoft.com/downloads/details.aspx?FamilyID=48364A72-D54E-46DC-AACF-E3BE887D17A6)。
*   搜索`<IIsCompressionScheme>`标签。应该有两个`<IIsCompressionScheme>`条目:一个用于 deflate，一个用于 GZIP——IIS 支持的两种压缩方法。默认情况下，IIS 使用 GZIP；很少使用 deflate。
*   搜索`HcScriptFileExtensions`部分。向列表中添加 aspx、asmx、php、cgi 和任何其他要动态压缩的文件扩展名。请小心遵循现有格式，它是用回车符分隔的，任何多余的空格都将阻止文件扩展名工作。在“放气”和“GZIP”中进行相同的更改。
*   将`HcDynamicCompressionLevel`设置为 9 级(默认值为 0，表示“不压缩”)。我推荐级别 9，基于我在网上看到的几个报告，表明级别 10 需要更多的 CPU 时间，而文件大小只比级别 9 减少了一点点。对“放气”和“GZIP”进行此更改。

请注意，这是一个影响所有网站的全局压缩规则。这种设置通常是您想要的，因为 HTTP 压缩非常有效，而且成本很低。然而，一些编码糟糕的 ASP.NET 网站可能与压缩不兼容。在这种情况下，您可能希望基于每个站点或每个文件夹启用或禁用压缩，IIS 也支持这种设置。配置该设置最简单的方法是使用命令行`adsutil.vbs`实用程序:

```
C:InetpubAdminScripts>adsutil.vbs set w3svc/ (site#) /root/DoStat   

&#10149;icCompression False   

C:InetpubAdminScripts>adsutil.vbs set w3svc/ (site#) /root/DoDyna   

&#10149;micCompression False
```

`(site#)`号可以从 IIS 服务器属性中的日志属性对话框中获得，通常采用`W3SVCn`的形式，其中`n`是一个任意的站点号。

**在 ASP.NET 应用中启用 HTTP 压缩支持**

也许您无法控制服务器上的 IIS 设置。或者，您可能只是想为您的特定 ASP.NET 应用程序启用压缩。那也是可能的。

开放源码的 HttpCompress 库很容易集成到 ASP.NET 网站中。首先，从官方网站下载最新版本的 HttpCompress。

将`blowery.Web.HttpCompress.dll`二进制文件放在某个逻辑位置，并添加对它的引用。

接下来，将以下特定于压缩的配置部分添加到您站点的`Web.config`中:

```
Example 15.5\. Web.config (excerpt)   

<configSections>   

  <sectionGroup name="blowery.web">   

    <section name="httpCompress"    

        type="blowery.Web.HttpCompress.SectionHandler,    

            blowery.Web.HttpCompress"/>   

  </sectionGroup>   

</configSections>   

<blowery.web>   

  <httpCompress preferredAlgorithm="gzip" compressionLevel="high">   

    <excludedMimeTypes>   

      <add type="image/png" />   

      <add type="image/jpeg" />   

      <add type="image/gif" />   

    </excludedMimeTypes>   

  </httpCompress>   

</blowery.web>
```

最后，将实际的压缩 HTTP 模块引入管道:

```
Example 15.6\. Web.config (excerpt)   

<system.web>   

  <httpModules>   

    <add name="CompressionModule"    

        type="blowery.Web.HttpCompress.HttpModule,    

            blowery.web.HttpCompress"/>   

  </httpModules>   

</system.web>
```

一旦完成，您应该会看到压缩的 ASPX 内容被返回到浏览器。为了验证这一点，使用 Port80 软件的方便的[实时压缩检查器](http://www.port80software.com/products/httpzip/compresscheck/)。

HttpCompress 模块方法的一个限制是，只有构成应用程序一部分的 ASPX 内容才会被压缩；CSS 和 JavaScript 不通过 ASP.NET ISAPI 处理程序提供，因此将保持未压缩状态。因此，我建议您尽可能在 web 服务器级别启用压缩，以便这些文件也能获得压缩的好处。

##### 我如何提高我的网站的速度？

大多数 ASP.NET 网络服务器执行许多不必要的重复工作。

例如，假设您有一个页面，其中的数据网格绑定到一个名为 Products 的表。每当用户请求产品页面时，ASP.NET 必须:

1.  在数据库中查找产品数据。

3.  处理页面。

5.  数据绑定产品数据。

7.  将结果呈现为 HTML。

9.  将结果输出到浏览器。

如果我们假设与用户请求产品列表的频率相比，产品列表很少改变，那么大部分工作都是不必要的。与其做这些工作来向所有用户发送相同的 HTML，为什么不直接存储 HTML 并重用它，直到 Products 表发生变化呢？

***解***

ASP.NET 缓存为 HTML 的高效存储和重用提供了关键。

有几种方法可以使用缓存；我们将重点介绍一些最简单的技巧，然后向您介绍一些资源，这些资源将帮助您解决如何更高级地利用缓存的问题。

最简单的解决方案是在页面或用户控件上使用`OutputCache`指令。例如，下面的页面将被缓存一个小时(3600 秒)。你可以随意刷新页面，但是`DateTime`的值。现在不会改变，直到页面从缓存中被清除。下面是检索当前时间的代码:

```
Example 15.7\. OutputCacheSimple.aspx (excerpt)    

<%@ Page Language="C#" AutoEventWireup="true"       

    CodeBehind="OutputCacheSimple.aspx.cs"    

    Inherits="chapter_15_performance.Performance.OutputCacheSimple"     

%>    

<%@ OutputCache Duration="3600" VaryByParam="none" %>    

<html >    

<head id="Head1" runat="server">    

  <title>Output Cache Example</title>    

</head>    

<body>    

  <form id="form1" runat="server">    

    <h1>Output Cache Example</h1>    

    <div>    

      <%= DateTime.Now.ToLongTimeString() %>    

    </div>    

  </form>    

</body>    

</html>
```

图 15.5 表示我们在晚上 11:01:41 的页面

![Figure 15.5\. Loading a cached page for the first time](img/183a84613824d0fa7d1349ef225f8413.png)

图 15.6 显示了晚上 11:23:01 时的情况

![Figure 15.6\. Subsequent reloads of a cached page showing no changes to the page content](img/ba15609b4b4f166d1a982de70e80e15a.png)

请注意，时间没有改变，因为页面没有重新呈现。

现在让我们看一个稍微复杂一点的缓存示例:

```
Example 15.8\. OutputCache.aspx (excerpt)    

<%@ Page Language="C#" AutoEventWireup="true"    

    CodeBehind="OutputCache.aspx.cs"     

    Inherits="chapter_15_performance.Performance.OutputCache" %>    

<%@ OutputCache Duration="30" VaryByParam="none" %>    

<html  >    

<head id="Head1" runat="server">    

  <title>Output Cache Example</title>    

  <script type="text/javascript">    

    var d = new Date();    

  </script>    

</head>    

<body>    

  <form id="form1" runat="server">    

  <!-- Pausing 5 seconds to simulate a database hit -->    

  <% System.Threading.Thread.Sleep(5000); %>    

  <h1>Output Cache Example</h1>    

  <div>    

    Time written by ASP.NET:    

    <%= DateTime.Now.ToLongTimeString() %>    

  </div>    

  <div>    

    Time written by Javascript:    

    <script type="text/javascript">    

      document.write(d.toLocaleTimeString())    

    </script>    

  </div>    

  <div id="divCached" style="margin-top:25px;width=300px;">    

    <script type="text/javascript">    

      var aspTime = new Date();    

      aspTime.setSeconds(<%= DateTime.Now.Second %>);    

      // If there are more than two seconds difference    

      // between the ASP.NET render and the javascript    

      // evaluation, the page is almost certainly cached.    

      if(Math.abs(d - aspTime) > 2000)    

      {    

        document.write('Probably Cached');    

        document.getElementById("divCached").style.backgroundColor =     

          "Coral";    

      }    

      else    

      {    

        document.write('Not Cached');    

        document.getElementById("divCached").style.backgroundColor =     

          "Aqua";    

      }    

    </script>    

  </div>    

</form>    

</body>    

</html>
```

上面的页面根据 ASP.NET(服务器端，缓存)和 JavaScript(客户端，不缓存)写出当前时间。此外，如果 JavaScript 时间比 ASP.NET 时间晚两秒以上，页面将报告它可能已经被缓存。

第一次查看这个页面时，有五秒钟的延迟(由于对 Thread 的调用。睡眠)，则显示图 15.7 所示的页面。

![Figure 15.7\. The page output on first load](img/2532030adc8fb92fbc3fed9e319b4f90.png)

但是如果你在 30 秒内刷新页面，你会注意到两个不同之处。首先，页面会立即返回。第二，它看起来像图 15.8。

![Figure 15.8\. The page output 30 seconds after its initial load](img/c50495b7a39fcd77620f2cb1d956f0b8.png)

*使用`VaryByParam`缓存参数化页面*
*当你开始考虑应用程序中的哪些页面可以被缓存时，你可能会发现很多页面包含 90%的静态内容。剩下的 10%的页面可能包含一两个经常变化的小部分。例如，考虑示例 Northwind 数据库中显示目录信息的页面，包括按类别筛选的产品。类别是由查询字符串中的一个参数设置的，所以下面两个 URL 会产生不同的结果:*

```
http://www.contoso.com/Northwind/Products.aspx?Category=Seafood

http://www.contoso.com/Northwind/Products.aspx?Category=Produce
```

*程序员新手可能会应用下面的代码来缓存这些页面:*

```
<%@ OutputCache Duration="30" %>
```

然而，如果你要应用这段代码，你会很快发现一个问题——类别过滤器会停止工作。显示的页面的第一个版本将被缓存，因此在后续的页面查看中过滤逻辑将被忽略。

*这就是使用`VaryByParam`属性的确切功能。在这种情况下，我们将把`OutputCache`指令改为如下:*

```
<%@ OutputCache Duration="30" VaryByParam="Category" %>
```

*`VaryByParam`属性告诉缓存系统，每当它在 URL 中看到类别的新值时，就存储页面的不同版本。一旦这个属性就位，我们的用户将能够访问海鲜和农产品类别页面的缓存副本。*

*请记住，为每个参数值存储页面的不同版本并不总是最好的主意——如果您有许多不同的参数值，您可以快速使用大量内存来保存页面的所有可能变化。当您的页面使用有限数量的参数值时，请使用`VaryByParam`。*

请注意，JavaScript 生成的时间中显示的秒数已经更新为 59 秒，而 ASP.NET 时间仍然显示 39 秒。这种差异表明，对于每个请求，服务器都向客户机发送相同的 HTML。假设我们将几个`GridView`绑定到几个昂贵的数据库查询的结果，而不是只显示当前时间。如果将缓存应用于这样的页面，节省的成本将是可观的。

您不必长时间缓存页面就能看到显著的性能提升——一分钟或更短时间的缓存就能显著提高您站点的性能。例如，在示例 Northwind 数据库中，向仅将一个`GridView`绑定到 Products 表的简单页面添加缓存将使该页面的性能提高约 500%,对于执行复杂数据库查询或处理器密集型计算的页面，这种节省可能会进一步放大。

*使用缓存后替换*
*虽然`OutputCache`指令允许您指定缓存应该随特定参数而变化，但是缓存几乎完全相同的一个页面的大量副本并不真正有效。*

*缓存后替换，使用`Substitution`控件，允许您将动态内容注入缓存页面。结果是，您可以缓存一些您可能认为无法缓存的页面。在 [Scott Guthrie 关于这个主题的文章](http://weblogs.asp.net/scottgu/archive/2006/11/28/tip-trick-implement-donut-caching-with-the-asp-net-2-0-output-cache-substitution-feature.aspx)中阅读更多关于这个主题的内容。*

但是，您可能会注意到这种方法的一个问题是延迟，即数据更新和相同的更新数据到达用户浏览器之间的时间延迟。在我们在这个解决方案中看到的例子中，我们在页面呈现后的 30 秒内显示了相同的时间戳——这对于性能来说非常好，但是如果您的用户要求数据总是最新的，这可能是一个问题。

幸运的是，ASP.NET 2.0 有一个解决这个问题的方案，我们接下来会看到。

##### 当数据改变时，我如何刷新我的缓存？

正如我们在上一篇技巧文章中看到的，明智地使用输出缓存可以极大地提高站点的性能。输出缓存通过保存为渲染的 ASP.NET 页面生成的 HTML 来工作。只要缓存有效，未来对该页面的请求将只返回存储的 HTML，而不是再次处理该页面，这意味着没有页面解析、数据库命中、数据绑定—任何需要宝贵带宽或处理器周期的任务。如果使用得当，缓存可以将高流量页面上的每秒请求数提高 100 倍或更多。

例如，假设您有一个包含绑定到名为 Products 的表的 DataGrid 的页面。如果没有缓存，每个页面请求都需要 ASP.NET 在数据库中查找产品数据，处理页面，数据绑定产品数据，将结果呈现为 HTML，并将结果输出到浏览器。

如果页面被缓存，那么只有对该页面的第一个请求需要所有的工作；在那之后，ASP.NET 会跳过所有艰苦的工作，提供 HTML，直到缓存过期。无论是否使用缓存，最终结果都是一样的:相同的 HTML 将被发送到浏览器。然而，由于显示缓存的 HTML 所需的工作和网络流量要少得多，服务器可以更快地为更多的人提供缓存的页面。

缓存页面的一个问题是，它们不会立即拾取已经更改的数据。继续上面的例子:如果我们要向 Products 表中添加一个新产品，在缓存过期之前，它不会显示在页面上。幸运的是，ASP.NET 2.0 提供了一个很好的机制，可以在底层数据发生变化时自动刷新缓存。

***解***

SQL 缓存依赖项就是为了解决这个问题而创建的。设置它需要几个步骤，但是一旦它启动并运行，您就可以在整个应用程序中使用它。

*在旧版本的 SQL Server 上使用 SQL 缓存依赖关系*
*在本书中，我将只讲述在 SQL Server 2005 上配置 SQL Server 缓存依赖关系的步骤；关于如何在 SQL Server 2000 上设置 MSDN，有大量的信息:*

*   [ASP.NET SQL Server 注册工具(Aspnet_regsql.exe)](http://msdn2.microsoft.com/en-us/library/ms229862(VS.80).aspx)
*   [使用 SqlCacheDependency 类在 ASP.NET 进行缓存](http://msdn2.microsoft.com/en-us/library/ms178604(VS.80).aspx)
*   [演练:对 SQL Server 使用 ASP.NET 输出缓存](http://msdn2.microsoft.com/en-us/library/e3w8402y(VS.80).aspx)

首先，您需要确保已经为您的数据库启用了 SQL Server Service Broker。您可以通过以下查询来确认这一点:

```
ALTER DATABASE Northwind SET ENABLE_BROKER;
```

接下来，您需要启动 SQL 依赖事件监听器。建议在`Global.asax`的`Application_Start`方法中进行触发该监听器的更改:

```
Example 15.9\. Global.asax (excerpt)    

void Application_Start(object sender, EventArgs e)    

{    

  string northwindConnection = WebConfigurationManager.ConnectionStr    

&#10149;ings["NorthwindConnectionString1"].ConnectionString;    

  SqlDependency.Start(northwindConnection);    

}
```

一旦上面的代码被添加到`Global.asax`文件中，我们的连接就可以使用 SQL 缓存依赖项。

为了说明如何利用 SQL 依赖，让我们看一个例子——一个简单的绑定到表的`GridView`。我们将把 Northwind Products 表拖到一个新页面上，然后在`SqlDataSource`控件中设置以下两个属性:

```
EnableCaching = "True" SqlCacheDependency =    

    "NorthwindConnectionString1:Products"
```

有了这一行简单的代码，`GridView`不会再次读取 Products 表，直到它发生变化。但是，只要 Products 表发生变化，SQL Server 就会通知 ASP.NET 转储缓存，随后的页面请求就会从数据库中重新加载页面。最终结果是，我们的应用程序获得了缓存带来的所有性能优势，但我们的用户永远不会看到陈旧的数据。

##### 我怎样才能获得对 ASP.NET 缓存的更多控制？

正如我们所看到的，声明式缓存提供了巨大的投资回报——它非常容易实现，并将给您带来一些立竿见影的性能优势。但是，只需多做一点工作，您就可以从 ASP.NET 缓存中获得更多好处。

***解***

如果你真的想利用 ASP.NET 的缓存，现在是时候见见缓存 API 了。声明式缓存可能很容易设置，但也只能做到这一步。与存储和重用呈现的 HTML 的声明式缓存不同，缓存 API 允许您在应用程序逻辑或代码隐藏代码中高效地存储数据。

考虑 ASP.NET 缓存的最简单方法是将其与应用程序对象进行比较。它们的相似之处在于，都可以通过字符串键来存储对象或值:

```
Cache["States"] = new string[] {"Alabama","Arkansas","Alaska"};     

Cache["ProductData"] = GetProductDataset();
```

那么应用程序对象和用于存储信息的缓存之间有什么区别呢？ASP.NET 可以在需要释放内存时从缓存中移除项目。缓存可使用的内存量有限，因此当新内容添加到缓存中时，缓存通常必须删除一些旧的缓存数据。

不同类别的信息可以存储在您的缓存中:

*   **昂贵数据**是您希望尽可能保留在缓存中的信息。术语“昂贵”是指这类数据的生成涉及宝贵的资源(数据库或处理能力)。
*   **廉价数据**指的是所有其他类型的信息，如果刚好有空间，您希望将这些信息放入缓存中，但生成这些信息并不特别耗费资源。

挑战在于防止廉价数据将昂贵数据推出缓存。

`Cache`对象提供了一些特性，让您可以控制哪些项目放在缓存中，以及它们在缓存中停留多长时间。使用`Cache`对象，您可以应用定制的缓存依赖关系，显式地设置缓存过期策略，并定义当一个项目从缓存中删除时触发的回调事件(这样您就可以决定是否要将它再次添加到缓存中)。

我最喜欢的功能之一是滑动过期，这是一种允许您指定项目应该保留在缓存中的设置，只要它在特定时间段内被使用过:

```
Cache.Insert("ProductData", GetProducts(), null,      

    System.Web.Caching.Cache.NoAbsoluteExpiration,     

    TimeSpan.FromMinutes(10));
```

上面的代码告诉缓存，我们希望它保存我们的产品数据集，只要它在过去十分钟内被使用过。这种方法的好处是，经常使用的数据将保持缓存，不经常使用的数据将过期并停止占用宝贵的空间。

我们可以定制缓存的设置，例如，对更昂贵的数据(比如 web 服务调用产生的数据)设置更长的滑动过期时间范围。如果需要的话，我们甚至可以在一个`GetLastUpdateTimestamp` web 服务调用的结果上添加一个缓存依赖来保持数据最新。但是请记住，任何数据仍然可以在任何时候从缓存中删除——我们的滑动过期时间设置实际上只是对缓存系统的一个建议。

***讨论***

一旦您开始缓存数据，您将开始看到一些经过验证的真正的缓存访问模式的好处。史蒂文·史密斯在他的[优秀的 MSDN 文章](http://msdn2.microsoft.com/en-us/library/aa478965.aspx)中提到了缓存数据参考模式。下面是实现这种模式的一些代码:

```
public DataTable GetCustomers(bool BypassCache)     

{     

  string cacheKey = "CustomersDataTable";     

  object cacheItem = Cache[cacheKey] as DataTable;     

  if((BypassCache) || (cacheItem == null))     

  {     

    cacheItem = GetCustomersFromDataSource();     

    Cache.Insert(cacheKey, cacheItem, null,     

        DateTime.Now.AddSeconds(GetCacheSecondsFromConfig(cacheKey),     

        TimeSpan.Zero);     

  }     

  return (DataTable)cacheItem;     

}
```

Smith 的文章更详细地解释了这种技术，但是要注意的最重要的一点是对象可能不在缓存中，或者可能随时被删除。上面的代码是安全的，因为它加载缓存的对象，检查对象是否为空，如果是，加载对象数据并将其添加回缓存。

我最喜欢的另一个缓存模式是 Gavin Joyce 在他的 DotNetKicks 网站上使用的模式——[勉强缓存模式](http://weblogs.asp.net/gavinjoyce/pages/The-Reluctant-Cache-Pattern.aspx),它依赖于他的`ReluctantCacheHelper`类。这种模式可以防止应用程序向缓存中添加不太可能用到的信息。

例如，当谷歌索引你的网站时，它会加载它能找到的每一页。如果您的站点实现了一个由大量页面使用的缓存，那么您的服务器将会执行大量不必要的工作来将数据添加到缓存中，而这些数据只会在添加其他页面时立即从缓存中删除。类似于滑动到期模式，但是相反，这种模式只在最近被频繁使用的情况下将数据添加到缓存中。这里有一个实现这种模式的例子:

```
public static List<Customer> GetCustomers() {     

  string cacheKey = "Customers";     

  int cacheDurationInSeconds = 5; // low number for demo purposes     

  object customers = HttpRuntime.Cache[cacheKey] as List<Customer>;     

  if (customers == null) {     

    customers = CustomerDao.GetCustomers();     

    if (new ReluctantCacheHelper(cacheKey,      

        cacheDurationInSeconds, 2).ThresholdHasBeenReached)     

    {     

      HttpRuntime.Cache.Insert(cacheKey,     

          customers,      

          null,      

          DateTime.Now.AddSeconds(cacheDurationInSeconds),     

          System.Web.Caching.Cache.NoSlidingExpiration);     

    }     

  }     

  return (List<Customer>)customers;     

}
```

##### 如何加快数据库查询的速度？

我们已经研究了一些优化你的 ASP.NET 代码的方法，但是如果你的查询很慢，你仍然会有一个拖拖拉拉的网站。如果您缓存数据或将服务器添加到您的 web 场中，您可以在某种程度上隐藏这个问题，但最终您将需要处理您的慢速查询。

当然，最好的办法是与优秀的数据库管理员(DBA)合作。我们是 ASP.NET 的开发人员，虽然我们在使用数据库的过程中会情不自禁地学习数据库，但是数据库管理并不是我们的全职工作。到目前为止，一个好的 DBA 是数据库问题的最佳解决方案，但是有时候，它不是一个选项。如果您在一个没有 DBA 的团队中工作，或者您对 DBA 的访问权限有限，那么您需要尽可能地解决问题。

*慢查询还是慢数据库？*
*决定是处理一个缓慢的查询还是整个缓慢的数据库很重要。是某个页面速度慢了，还是整个网站都在呻吟？这一解决方案将侧重于前者；如果您可以将数据库性能问题缩小到单个查询，请参考“如何解决慢速查询问题”一节在本章的后面。*

***解***

使用 SQL Server 性能工具文件夹中的 SQL 事件探查器和数据库优化顾问。

优化数据库服务器很困难。最初，可能很难找出是什么导致了速度变慢。即便如此，修复一个问题(例如，对表应用索引以提高`SELECT`语句的速度)可能会引入新的问题(例如更慢的`INSERT`和`UPDATE`)。

幸运的是，SQL 性能工具的存在意味着您不必为猜测而烦恼。

SQL Profiler 捕获数据库中正在发生的事情，包括正在执行哪些 SQL 语句、谁正在执行它们以及它们花费了多长时间。探查器是确定服务器上实际发生了什么的第一步。

请注意，探查器会捕获即席和动态 SQL。这意味着，当控件、库或框架代码在数据库中进行调用时，探查器特别有用——您可能无法访问 ASP.NET 代码，但探查器会准确显示正在执行哪些查询，以及它们需要多长时间。

如果您可以在实际的生产系统上运行分析器跟踪，这是最好的，但这并不总是可能的——分析服务器会降低服务器的速度，并且站点的普通用户可能不会意识到额外的延迟。如果您不能在生产系统上运行，您仍然可以通过模拟真实用户在活动服务器上使用您的站点的方式，对本地或开发服务器上的服务器活动有一个合理的了解。

第一次运行 profiler 可能有点令人畏惧——有许多高级设置需要监控和调整。最好从一个预定义的模板开始，就像我在图 15.9 中选择的那样。常规性能诊断的两个最有用的模板是 TSQL _ 持续时间和调优模板。

![Figure 15.9\. Selecting the TSQL_Duration template](img/0a3f3ce49c1757eeaa0a01285f05d30a.png)

TSQL _ 持续时间模板有助于您快速了解执行时间最长的查询和存储过程。图 15.10 显示了对 Northwind 示例数据库运行的一些示例查询。最慢的查询(持续时间最长的查询)在列表底部突出显示。

![Figure 15.10\. Query duration times in SQL Server Profiler](img/55805664613ca0f53630341f7a7f264f.png)

在上面这样一个简单的例子中，您也许能够从 TSQL _ 持续时间跟踪中推断出足够的信息来开始调优一个特定的查询。但是，如果您有任何疑问，最好使用优化模板运行探查器，并在数据库优化顾问(也称为 DTA)中分析结果，该工具用于分析数据库性能并建议应该索引哪些表。

为此，首先从 profiler 中保存您的跟踪文件，如图 15.11 所示。

![Figure 15.11\. Saving a trace file from SQL Server Profiler](img/911bdd0898e02d8e3904726fa9ec3db9.png)

现在，我们将使用 DTA 打开刚才保存的跟踪文件，如图 15.12 所示，并单击开始分析按钮。

![Figure 15.12\. Loading the trace file in the Database Tuning Advisor](img/5986c1abf92fc3068a4fc3996fa7ddb8.png)

对于我的示例查询，DTA 建议我应用一些索引，以产生大约 5%的性能提升，如图 15.13 所示。(Northwind 数据库已经或多或少地建立了索引；如果运气好的话，你估计的进步应该会高很多。)

![Figure 15.13\. The index recommendations suggested by the DTA](img/ac12b62a03e0ec54431924e34aa425ec.png)

如果向右滚动，以使“说明”列可见，您将确切地看到 DTA 建议的索引和统计更改。单击一个推荐，查看将添加建议索引的脚本的预览，如图 15.14 所示。

![Figure 15.14\. Viewing a preview of the script for applying one of the recommended indexes](img/b6481b79079183539c56bcd0b86c0314.png)

要实施这些更改，我建议您保存建议(操作>保存建议…)，在 SQL Server Management Studio (SSMS)中查看它们，如果您觉得合适，就应用它们。一旦完成了这一步，就要重复最初的分析测试，并验证这些更改是否提高了数据库性能。

*使用 SQL 工作负荷脚本文件运行 DTA*
*我们的 DTA 演练使用 SQL Server 跟踪文件来处理工作负荷，但是您也可以针对 SQL 脚本使用 DTA。这里是我在这个演练中使用的脚本的缩略副本:*

```
Example 15.10\. SampleSqlWorkload.sql (excerpt)     

USE [Northwind]     

GO     

SELECT * FROM [Order Details] od     

INNER JOIN Orders o on o.OrderID = od.OrderID     

GO     

SELECT * FROM [Category Sales for 1997]     

GO     

SELECT * FROM Invoices     

GO     

SELECT COUNT(OrderID) OrderCount, ShipPostalCode      

    FROM Orders     

GROUP BY ShipPostalCode     

ORDER BY COUNT(OrderID) DESC     

GO     

EXEC [Ten Most Expensive Products]     

GO     

SELECT COUNT(OrderID) OrderCount, c.CustomerID,      

    c.ContactName     

FROM Orders o INNER JOIN Customers c      

    ON o.CustomerID = c.CustomerID     

GROUP BY c.CustomerID, c.ContactName      

sORDER BY COUNT(OrderID) DESC     

GO     

SELECT LEN(ContactName), ContactName     

FROM Customers ORDER BY LEN(ContactName) DESC     

GO
```

需要注意的重要一点是，语句之间有 GO 分隔符，这可以确保它们独立执行。您将希望您的 SQL 工作负载脚本文件模拟实际使用情况，这意味着您应该包括对最常用查询的重复调用。

*使用性能仪表盘*
*SQL Server 包含动态管理视图(dmv)——包含大量有用的数据库管理和故障排除信息的数据库视图。所有的 DMV 视图都以前缀`sys.dm_`开始；比如:`sys.dm_index_usage_stats`。*

SSMS 包括一些利用 SQL Server 报告服务和车管所的内置报告。您可以在 SSMS 查看这些报告，如果您右键单击一个数据库并选择报告>标准报告…

SQL Server SP2 包含了定制报告的功能，其中最先发布的是[性能仪表板](http://www.microsoft.com/downloads/details.aspx?familyid=1d3a4a0d-7e0c-4730-8204-e419218c1efc)。安装后，性能控制面板会为您提供一个在浏览器中可见的图形快照，而无需您运行跟踪。图 15.15 显示了运行中的仪表板。

![Figure 15.15\. Viewing a handy third-party custom report in the Performance Dashboard](img/94260d746daf70f1883e4a2d0833a15f.png)

***讨论***

对运行缓慢的数据库进行故障排除的一个重要方面是了解是什么导致它运行缓慢。导致性能低下的潜在原因有很多，但一些常见问题是首要原因。让我们来看看其中的几个。

**缺少索引**

索引数据的能力是在数据库中存储信息的主要好处之一。想象一下，你需要在电话簿中查找一个名字。您利用了姓氏是按字母顺序排序的这一事实——如果条目是按随机顺序排列的，要找到一个名字将花费很长时间。出于同样的原因，数据库利用您的信息的排序方式。表中的默认排序顺序称为聚集索引。

当然，你可能想用几种不同的方法来搜索你的信息。继续电话簿示例，您可能希望通过邮政编码、企业类型或名称来查找企业。在数据库中实现这种功能的常见方法是根据最常见的搜索顺序对数据进行排序，然后放置附加索引以方便其他搜索标准。数据按聚集索引排序，但数据库存储了其他信息，以帮助它使用其他条件查找行。这种附加的查找信息称为非聚集索引。

查询缓慢的最常见原因之一是数据库正在执行表扫描，当数据库缺少用于查找数据的适当索引时，就会发生这种情况。要求数据库执行表扫描相当于要求某人在随机排列条目的电话簿中查找一个人。总之，正确的索引是数据库性能的必要条件。

**不正确的索引**

如果索引是好的，那么更多的索引是好的，对吗？

事实上，不是的。当你向数据库添加索引时，会产生成本。每次添加或更新一行时，所有的索引都需要更新，过多的索引会降低数据库的速度。选择有限数量的索引是很重要的，这样既可以快速查找，又不会降低更新速度。在本章的后面，我们将讨论一些工具来帮助你完成这项任务。

**写得不好的查询**

如果你不真正理解 SQL，你很容易陷入困境。SQL 不仅仅是另一种编程语言，它还是一种声明性的、基于集合的查询语言。您的许多标准编程技巧在这里并不适用。

我们将在“如何对缓慢的查询进行故障排除”一节中讨论对查询计划和编写不当的查询进行故障排除在本章的后面。

**死锁**

数据库使用锁来防止数据被冲突的更新弄乱。

例如，我们不能将 10%的折扣应用于我们所有的产品价格，同时删除所有定价超过 100 美元的产品——我们需要一次处理一个产品。锁允许我们这样做。SQL Server 引擎在如何使用数据库锁方面相当聪明——它通常会锁定表的一部分(称为页),有时会锁定单个行。

然而，两个事务总是有可能达到这样一种状态，即两个事务都在等待另一个事务同时持有的锁的释放。这种情况称为死锁。

例如，考虑两个使用 Products 和 Orders 表的查询。第一个查询，我们称之为`ProductsThenOrders`，首先使用 Products 表；第二个查询`OrdersThenProducts`首先使用订单表。

```
ProductsThenOrders locks the Products table and begins to make updates. Meanwhile, OrdersThenProducts locks the Orders table and performs its updates. No problems so far.
```

现在`ProductsThenOrders`准备好更新 Orders 表，但是它不能——另一个查询已经锁定了它。同样，`OrdersThenProducts`想要更新 Products 表，但是也因为同样的原因被阻止。我们陷入僵局了！

当 SQL Server 最终检测到死锁时，它将选择一个查询作为“死锁牺牲品”并杀死它，同时释放幸存者。这个冲突解决过程的结果是，其中一个查询将返回一个它无法完成的错误—这不是最有效的资源利用。

死锁不会太频繁地发生——除非您的应用程序执行大量事务。意识到它们并快速修复死锁情况是很重要的。死锁可以通过以下方法避免:

*   最小化事务长度
*   在竞争查询中以相同的顺序访问表

在上面的例子中，在两个查询中首先访问 Products 表可以防止死锁。

*no lock 查询优化器提示*
*即使没有遇到死锁，锁也会对性能产生一定的影响。锁以这样一种方式限制对数据的访问，即在任何时候只有一个查询可以使用它—这种方法安全但速度慢。*

*在某些情况下，您可能不需要锁定您的行。您可能会查询不会发生变化的历史数据，或者查询中返回的数据是否完全是最新的并不重要——博客上的评论可能属于这一类。*

*在这些情况下，您可以使用`NOLOCK`提示告诉 SQL Server 您想直接从表中读取数据，而不考虑任何锁。注意，这只对`SELECT`语句有意义——任何数据修改总是需要一个锁。最佳实践是尽可能避免使用表提示，即覆盖查询默认行为的参数。然而，只要您明白您可能正在查看未提交的更改，这种方法相对来说是无害的。只是不要在显示关键信息时使用它，比如财务数据。*

*你可以这样使用它:*

```
SELECT COUNT(1) FROM Orders WITH (NOLOCK)
```

*由于这个语句没有锁定它正在读取的数据，所以其他查询在使用`Orders`表之前不会被迫等待查询完成。*

您可以在 MSDN 的文章“使用 SQL Server Profiler 分析死锁”中了解更多关于死锁的信息

**硬件问题**

与任何软件应用程序一样，当 SQL Server 运行在足够强大的硬件上时，它会以最佳状态运行。

如果升级您的服务器是一个选项，您首先应该考虑的是内存，因为 SQL Server 大量使用可用内存来缓存经常使用的数据。而且新内存的成本相对便宜——通常比调优一台性能不佳的数据库服务器所需的时间要便宜。增加内存可以弥补缓慢的 CPU 或驱动器访问，因为缓存可以显著减少 SQL Server 需要完成的工作。

在用尽内存升级选项之后，下一个最常见的硬件问题是磁盘读/写瓶颈。数据库硬件配置是一个很大的主题，远远超出了 ASP.NET 书籍的范围，但是最好的第一步是将日志文件放在一个尽可能快的驱动器上，并且与操作系统和数据库文件分开。

将生产数据库服务器用于其他任务——尤其是 IIS——不是一个好主意。这在开发环境中通常是必要的，但在生产环境中会对性能产生影响。

##### 如何解决查询速度慢的问题？

优化数据库性能是一个复杂的主题，是许多厚书的主题，所以我不打算假装我们可以在短短几页内使您成为查询优化专家。相反，我将把重点放在一些我最喜欢的“开发人员对开发人员”的技巧上，为您指出正确的方向。

***解***

在你开始寻找解决方案之前，核实手头的问题是很重要的。然后你可以开始排除的过程。

**验证问题**

首先，验证您认为正在执行的 SQL 实际上正在执行。确认这一点的最佳方法是重现问题:在 SQL Server Management Studio (SSMS)中运行查询。

如果您对正在执行的 SQL 命令有任何疑问，请运行 SQL Profiler 进行确认(参见“如何加快数据库查询速度”一节)有关使用 SQL 事件探查器的详细信息)。当与使用声明性数据绑定的应用程序或为您处理数据访问的框架一起使用时，该工具特别有用。

*在 SSMS 测试时清除 SQL 缓存*
*SQL Server 使用智能缓存系统来增强性能。如果您对某个表运行频繁的查询，SQL Server 将识别这一事实，并将这些查询的源(和结果数据)存储在其内部缓存中。这样，以后的匹配查询就不需要查找这些数据，直到下次数据发生变化。*

此功能虽然有用，但如果您通过从 SSMS 运行查询来进行测试，可能会令人困惑——您的一些查询信息可能会被缓存，因此您的查询在第二次执行时会运行得更快。

*为了确保进行有效的比较而不返回缓存信息，请在每次运行查询时清除缓存。下面的脚本就是这样做的——首先，它丢弃缓存，然后调用一个`CHECKPOINT`将挂起的更改从内存刷新到磁盘，最后，它清除所有已经存储在内存中的数据:*

```
DBCC FREESESSIONCACHE      

DBCC FREEPROCCACHE      

DBCC FREESYSTEMCACHE('ALL')      

CHECKPOINT      

DBCC DROPCLEANBUFFERS      

GO -- Your query goes here
```

一旦你能够在 SSMS 重现这个问题，你就可以深入研究查询本身。

**检查大型结果集**

如果您的查询返回的行数比预期的多，那么有两个主要问题需要考虑——交叉连接和不完整的`WHERE`子句。

当您未能正确指定联接时，就会发生交叉联接。这里有一个例子:

```
SELECT * FROM Customers, Orders, [Order Details], Products
```

在上面的查询中，我们没有指定应该如何连接表，所以 SQL 解释器将尝试返回每一种可能的组合。这意味着我们的结果集将包括与每个客户相结合的每个订单(不仅仅是每个客户下的订单)。因此，该查询返回大约 125 亿行(91 个客户— 830 个订单— 2155 个订单详细信息— 77 种产品)—大约是 7.5GB 的数据。

考虑到系统中只有 830 个订单，这个退货显然是不合理的。当然，出于演示的目的，这是一个稍微夸张的例子，但是很容易看出单个交叉连接是如何浪费大量数据库资源(CPU 和内存)并延迟数据库和 web 服务器之间的网络流量的。

不完整的`WHERE`子句没有那么糟糕，但是仍然可以返回比您需要的更多的行。以下查询返回 2155 行:

```
SELECT * FROM [Order Details]
```

另一方面，这个函数返回三行:

```
SELECT * FROM [Order Details] WHERE OrderID = 10252
```

ADO.NET 让过滤服务器上的数据变得非常容易，但不幸的是，这个特性是一把双刃剑——它可能会掩盖大型结果集的问题。这就是为什么用真实、系统的测量来验证问题很重要，而不是仅仅假设只显示少数几行的`Gridview`不可能是问题的根源。

**检查查询计划**

如果您的查询确实返回了正确的行数，但是花费的时间仍然太长，那么下一步就是查看查询计划，它是查询返回其结果集所采取的步骤的可视化表示。

如果首先选择查询，然后从“查询”菜单中选择“显示估计的执行计划”(或使用工具栏按钮或键盘快捷键 Ctrl-L)，则可以在 SSMS 中查看估计的查询执行计划。您还可以选择包含实际的查询执行计划(也可以从查询菜单、工具栏和键盘快捷键 Ctrl-M 中获得)。实际的计划比估计的稍微精确一点，但是需要您实际执行查询并等待它完成。

让我们看看 SQL Server 附带的 AdventureWorks 示例数据库中的`uspGetBillOfMaterials`存储过程的实际执行计划。在“SSMS 查询”窗口中输入以下文本，然后打开“包括实际执行计划”选项并执行查询:

```
EXEC dbo.uspGetBillOfMaterials 800, '2001-01-09'
```

图 15.16 显示了结果。

![Figure 15.16\. The execution plan for the uspGetBillOfMaterials stored procedure](img/2175d46b0fdcbd474c2e7f2ff8b20853.png)

图 15.17 显示了我们计划右下角的特写。

![Figure 15.17\. A close-up of the execution plan for the uspGetBillOfMaterials stored procedure](img/bb075df90a66806cd10ed8fe638a2a08.png)

当你分析一个执行计划时，你需要寻找一些重要的东西:

*   **执行计划中的粗线**表示步骤之间传递了大量数据。我曾经对一个非常慢的摘要报告查询进行故障排除，该查询只返回十几行。当我查看执行计划时，我看到步骤之间的一些线条有一英寸厚，这表明这些步骤之间传递了数十亿行，然后这些行被过滤到浏览器中显示的最后十几行。解决方案是修改查询，以确保尽可能早地过滤数据。
*   大百分比数字表示最昂贵的操作——图 15.17 中的值 44%就是一个例子。
*   **如果计划中的一个步骤包含单词“Scan”**(或者，特别是“Table Scan”)，这表明 SQL 引擎必须遍历表中的每一行，才能找到它要查找的数据。这通常与高成本值相关联。有些情况下表扫描是可以接受的——比如当您对一个非常小的表执行查找时——但是一般情况下最好避免。

如果发现问题，可以在 SSMS 中进行故障排除:修改查询并查看更改对执行计划的影响。

如果看起来您的问题可能是由索引问题引起的，最佳解决方案是右键单击该查询，然后在数据库引擎优化顾问中选择“分析查询”。DTA 将启动，并带有所有必要的预选选项，因此您只需单击“开始分析”按钮。图 15.18 显示了这种分析的结果。

![Figure 15.18\. Analyzing a query in the DTA](img/af8cd0da15ebc8024c68b6badac3916b.png)

如您所见，DTA 推荐了两个索引更改和一个统计更改，这应该会将该查询的性能提高 31%。当然，您需要考虑这些额外的索引对受影响的表的更新的影响。在这种情况下，由于这个例子中的表可能很少更新，我认为这些新索引是有意义的。您可以通过操作菜单应用或保存这些更改。

**消除光标**

您需要寻找并消除任何不必要的游标——在数据库中遍历记录的指针。

游标允许您以过程化的方式编写，一次将逻辑应用于一个表行。虽然在棘手的情况下很容易退回到您最熟悉的那些技能，但是基于游标的查询会阻止数据库引擎利用索引优化和基于集合的操作，而这正是它的设计目的。

抵制冲动，摆脱你的光标！

在十年的专业编程生涯中，我写了很多 SQL，但还没有遇到过需要使用游标的情况。我最近将别人的复杂查询从使用游标改写为标准 SQL，结果操作的时间从八个小时减少到一分钟多一点。

想想怎么把问题描述成批量操作。例如，假设您思考查询的方式是这样的:

“我将遍历订单表，获取产品 ID，然后获取价格，并将其与……”

相反，可以考虑这样重新表述:

“我想查找价格高于一定金额的所有产品订单…”

记住，如果遇到困难，可以使用公共表表达式(cte)、表变量和临时表。虽然这些回退选项不如执行批量操作有效，但它们至少允许查询引擎利用索引。

**问题与`SELECT *`**

大多数开发人员会告诉你`SELECT *`查询是不好的，但原因是错误的。

通常理解的原因是`SELECT *`是浪费的，因为它返回了您不需要的列。虽然这是真的，但大多数规范化的表格并不包含那么多的列，所以这些额外的行通常不会对您的站点的性能产生明显的影响，除非它们的数量达到数百万。

通常，`SELECT *`更大的问题是它对执行计划的影响。虽然 SQL Server 主要使用索引来查找数据，但是如果索引恰好包含您请求的所有列，它甚至不需要在表中查找。这个概念被称为索引覆盖率。

比较以下两个查询(针对示例 AdventureWorks 数据库):

```
SELECT * FROM Production.TransactionHistoryArchive       

  WHERE ReferenceOrderID < 100      

SELECT ReferenceOrderLineID FROM       

    Production.TransactionHistoryArchive      

  WHERE ReferenceOrderID < 100
```

在这两种情况下，我们返回相同数量的行，SELECT *查询只比第二个查询多返回 15KB 的数据。然而，请看一下图 15.19 所示的执行计划。

![Figure 15.19\. The execution plan for two queries -- one using SELECT *, and one using the table name](img/a61da1e35d7945edbdfdd132a59f606c.png)

您会注意到，第一个查询占用了两个查询 99%的相对工作量。第二个查询能够通过索引查找在索引中查找值，这种搜索只涉及那些符合条件的行。然而，第一个查询必须扫描表中的所有行。在这种情况下，请求的列都包含在搜索索引中这一事实导致了近百倍的性能差异。

在索引中包含经常查询的数据很重要——如果使用`SELECT *`，这是不可行的。如果您只是查询您需要的行，DTA 将能够推荐索引来覆盖它们。

**访问更多信息**

当您遇到一个非常困难的 SQL 数据库问题时，可以求助于大量的资源。以下是其中的几个例子:

*   关于排除 SQL Server 2005 性能问题的 Microsoft TechNet 文章
*   [SQLTeam.com](http://www.sqlteam.com/)—众多 SQL Server 社区论坛之一
*   [SitePoint 数据库论坛](https://www.sitepoint.com/blog/)

##### 摘要

性能优化是一个迭代的过程——做好准备，按照重复的步骤分析站点的性能，调整应用程序，再次分析性能，然后再调整一些，直到站点按照您希望的方式运行。过早的优化——在不了解导致速度下降的原因的情况下进行优化——很可能会导致比它解决的问题更多的问题。

ASP.NET 和 SQL Server 2005 为地球上一些最受欢迎和最强大的网站提供支持，包括每天提供数十亿次页面浏览量的 MySpace。您已经拥有了最大限度地利用 web 服务器所需的所有工具——我希望本章中的提示能帮助您将它们付诸实践。

以上就是这个 [*的章节 ASP.NET 2.0 选集，101 个要诀，招数&Hacks*。随意下载这一章——以及其他三章——作为离线参考。](https://www.sitepoint.com/books/aspnetant1/)

## 分享这篇文章