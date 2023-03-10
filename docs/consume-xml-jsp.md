# 在 JSP 中使用 XML

> 原文：<https://www.sitepoint.com/consume-xml-jsp/>

XML 将拯救世界！

…至少，别人是这么告诉我的。但是不知何故，几年后，我们大多数人在日常编程中不再使用可扩展标记语言(XML)。

尽管如此，有时您可能会被要求提供一个消费或输出 XML 的服务，所以熟悉这项技术是一个好主意。

两个例子可能主导了我对 XML 的体验。第一种是股价服务，许多公司为希望在网站上显示其股价信息的企业提供这种服务。第二个是将公司的新闻稿作为 XML 提要输出给感兴趣的人。

在这里，我将概述我们在 JSP 环境中使用 XML 的技术，并简要评论在 JSP 环境中输出 XML。为了做到这一点，我将解释 JSP 如何处理 XML，然后我们可以继续在代码中进行尝试。

##### XML 共享文档

作为我工作的一部分，我经常要为那些希望在其网站上增加这项服务的公司提供股价信息。

这是一个典型的 XML 共享信息文档。这项服务通常是通过 HTTP 提供的——也就是说，你只需将浏览器指向一个 URL，比如 http://www.myinvestorinfo.com/sharefeed.jsp?company=XXX，就可以找到它

股价文档可能如下所示:

```
<shares> 

  <share> 

    <code>CSW</code> 

    <price>502.25</price> 

    <change>2.25</change> 

  <percentageChange>0.45</ 

percentageChange> 

    <open>500</open> 

    <high>506.32</high> 

    <low>499.85</low> 

    <yearLow>423.45</yearLow> 

    <yearHigh>586.92</yearHigh> 

    <volume>12486123</volume> 

    <date>12.45 12/06/2003</date> 

  </share> 

</shares>
```

如果您订阅了多个产品，则会提供多个元素。现在，让我们假设我们只需要单个股票的统计数据，我们特别需要该股票的价格、变化和百分比变化数字，以及价格设定的日期。

这些统计数据依次代表:

*   每股的当前价值
*   自上一次以美元表示的股票价值以来，该价值的变化量
*   自上次份额值以来，此值的变化量，以百分比表示
*   设置该值的日期和时间

通常，呈现最新的共享更新会涉及很高的成本，因此它们通常会过时 20 分钟或更久。

##### Java 和 SAX

对于大容量、时间要求严格的 XML 处理，推荐使用 Simple API for XML (SAX)。这基本上意味着每个元素和文本都按照出现的顺序传递给处理器或处理程序，处理器或处理程序被编程为以预定义的方式响应文档中的元素。

在这种情况下，我们在网站上呈现了潜在的大量数据。网络上的任何内容都是时间紧迫的，虽然人们通常会原谅网站所有者在财务数据更新方面的轻微延迟，但没有必要测试他们的耐心。具有讽刺意味的是，在免费内容的例子中，对最新信息的期望似乎更高。由于使用免费服务是没有成本的，用户会很快放弃这些设施，转而选择在网上广泛获取的最新信息。

另一个不容忽视的好处是，SAX 中文档处理所需的内存量非常小。此外，我们的任务非常简单(我们只是想提取信息来显示它)，所以不需要处理或操纵数据。

##### 数据类

正如我们已经讨论过的，我们感兴趣的信息是共享的:

*   名字
*   现价
*   美元兑换
*   时间
*   日期
*   百分比变动

价格、时间和除日期以外的所有其他值都将存储为字符串。

因此，本例中的数据类如下所示。注意，我省略了一些明显的代码，比如构造函数和简单的 getter-and-setter:

```
package com.clearlysomethingwrong;  

import java.util.Date;  

import java.text.SimpleDateFormat;  

import java.text.ParseException;  

public class InvestorInformation {  

  String name;  

  String currentPrice;  

  String change;  

  String time;  

  Date date;  

  String percentage;  

  public String getFormattedDate() {  

    SimpleDateFormat formatter = new SimpleDateFormat("dd-MMM-yyyy");  

    if(date!=null) {  

      return formatter.format(date);  

    } else {  

        return formatter.format(new Date());  

    }  

  }  

  public void setDate(String strdate) {  

    SimpleDateFormat formatter = new SimpleDateFormat("dd/MM/yy");  

    try {  

      date = formatter.parse(strdate);  

    } catch (ParseException e) {  

      date = new Date();  

    }  

  }  

}
```

在这里，输入和输出日期的字符串格式是硬编码的，但是要改变这一点并不困难。getter 中的 NULL 检查通过将 NULL 传递给格式化程序来确保我们不会得到 NullPointerException。

##### 加载 XML 文档

这很简单，数据类也应该如此！我们要写的下一个类是一种工厂。我们给它一个 XML 源的 java.io.InputStream，它返回一个填充的 InvestorInformation 类。

SAX 解析通常使用相同的基本代码；您创建一个解析器来读取传入的 XML，并向解析器传递一个处理程序，该处理程序处理 XML 文件中的每种类型的内容。

由于组合代码(创建解析器和处理 XML 元素的代码)非常简单，我们将创建的下一个类将完成这两项任务。它将实现 Handler 类，并提供处理 XML 源的方法。

首先，我们扩展了`org.xml.sax.helpers.DefaultHandler`类:

```
package com.clearlysomethingwrong;  

import org.xml.sax.helpers.DefaultHandler;  

import org.xml.sax.SAXException;  

import javax.xml.parsers.ParserConfigurationException;  

import java.io.InputStream;  

import java.io.IOException;  

public class InvestorInfoLoader extends DefaultHandler {  

  static InvestorInformation info;  

  static String code;  

  static String name;  

  private boolean foundCode;  

  private boolean setName;  

  private boolean setCurrentPrice;  

  private boolean setChange;  

  private boolean setPercentage;  

  private boolean setTime;  

  private boolean setDate;
```

这些标志中的每一个都表示我们感兴趣的 XML 文档的状态。当程序输入每个适当的元素时，匹配标志将被设置。然后，当字符内容被发送到程序时，它可以将该内容分配给 InvestorInformation 类的适当属性。

属性指出已经找到了具有给定代码的共享。name 属性允许该类的用户用一个用户友好的名称来代替公司，因为通常的股份名称(如 IBM、SUNW、XOM 和 FNM)对于不了解内情的人来说可能是神秘的。如果值为 null，我们可以简单地返回代码作为共享信息对象的名称。

接下来，我们定义静态处理器方法`loadInvestorInfo()`。该方法通过工厂方法实例化一个解析器，创建一个自身的实例作为处理程序，然后传入输入流和自身。结果存储在该类的 static InvestorInformation 属性中，然后返回:

```
 public static InvestorInformation loadInvestorInfo(InputStream is,   

                              String code, String name)  

    throws SAXException, IOException, ParserConfigurationException {  

  // detect invalid code, invalid input stream will be detected by parser.  

  if(code==null || code.length() == 0) {  

    throw new IllegalArgumentException("Code is null. Please provide a " +  

                                "share code.");  

  }  

  InvestorInfoLoader.code = code;  

  InvestorInfoLoader.name = name;  

    InvestorInfoLoader iil = new InvestorInfoLoader();  

    // load the XML file  

    // create a new sax parser factory  

    javax.xml.parsers.SAXParserFactory factory =   

                  javax.xml.parsers.SAXParserFactory.newInstance();  

    // turn off namespaces for now  

    factory.setNamespaceAware(false);  

    factory.setValidating(true);  

    javax.xml.parsers.SAXParser parser = factory.newSAXParser();  

    parser.parse(is, iil);  

    // load each bit of info for font  

    return info;  

  }
```

相当标准的东西。处理程序方法也非常基本。请注意，我在这里关闭了名称空间感知——通常会提供一个 DTD，但是对于这里显示的简单级别的应用程序(我发现这是非常典型的),拥有名称空间感知可能并不重要。

回报通常是性能对安全，这是你的决定。毫无疑问，您的选择将取决于您与服务供应商的关系水平(许多供应商在没有通知的情况下更改了配置)，以及应用程序的关键程度。

##### 检测元素

每个元素的全限定名是上面定义的名称，`<price>`表示价格，依此类推。检测 share 元素是为了创建一个新的`InvestorInformation`实例。

```
 public void startElement(String uri, String localName, String qName,   

                                             org.xml.sax.Attributes attributes) {   

    if(qName.equals("share") && info==null) {   

      info=new InvestorInformation();   

    } else if (qName.equals("code")) {   

      setName=true;   

    } else if(qName.equals("price")) {   

      setCurrentPrice = true;   

    } else if(qName.equals("change")) {   

      setChange = true;   

    } else if(qName.equals("time")) {   

      setTime = true;   

    } else if(qName.equals("date")) {   

      setDate = true;   

    } else if(qName.equals("percentageChange")) {   

      setPercentage = true;   

    }   

  }   

  public void endElement(String uri, String localName, String qName) {   

    if(qName.equals("share")) {   

        foundCode = false;   

    } else if(qName.equals("code")) {   

      setName = false;   

    } else if(qName.equals("price")) {   

      setCurrentPrice = false;   

    } else if(qName.equals("change")) {   

      setChange = false;   

    } else if(qName.equals("time")) {   

      setTime = false;   

    } else if(qName.equals("date")) {   

      setDate = false;   

    } else if(qName.equals("percentageChange")) {   

      setPercentage = false;   

    }   

  }
```

##### 设置份额值

然后，`characters()`方法收集共享信息。

```
 public void characters(char[] ch, int start, int length){   

    if(setName) {   

      String localName = new String(ch, start, length);   

      if(localName.equals(code)) {   

    this.foundCode = true;   

    if(name==null) {   

            info.setName(localName);   

    } else {   

            info.setName(name);   

    }   

      } else {   

    // reset the found code class in case there are multiple share entries   

    foundCode = false;   

    }   

    } else if(foundCode && setCurrentPrice) {   

      info.setCurrentPrice(new String(ch, start, length));   

    } else if(foundCode && setChange) {   

      info.setChange(new String(ch, start, length));   

    } else if(foundCode && setPercentage) {   

      info.setPercentage(new String(ch, start, length));   

    } else if(foundCode && setTime) {   

      info.setTime(new String(ch, start, length));   

    } else if(foundCode && setDate) {   

      info.setDate(new String(ch, start, length));   

    }   

  }
```

这里唯一特别值得注意的是代码元素的处理。由于文档可能包含多个股票发行，我们需要检测正确的股票发行，但是只有在创建了一个新的`ShareInformation`类，并且我们位于股票发行对象内部之后，我们才能这样做。这就是`foundCode`标志的用武之地。

这段代码有趣的地方在于，几乎所有的 SAX 处理代码看起来都和上面的完全一样。这实际上非常简单，因此使用该服务收集的信息所需的大部分编程都在别处进行。

在这一点上，我强烈建议测试这个类，因为它的简单性可能会欺骗你，让你以为它第一次就能工作。尝试一些范围测试，以确保您已经涵盖了所有可能的值。

##### 设计 Web 组件

到目前为止，编码一直很简单。需要做出的决定相对较少，即我们是否要打开解析器的各种可选特性。

如果经过测试，该类没有像预期的那样工作，您可能会发现将处理程序从工作类中分离出来会更容易。尽管这些类很小，但是角色的分离通常有助于明确你哪里出错了。这取决于您在处理静态和实例角色时的自信程度。

此外，使`loadInvestorInfo()`方法成为静态的并不是绝对必要的。使 InvestorInfoLoader 类的实例化成为强制性的也是可行的。我猜想在我内心深处的某个地方，我考虑过缓存结果的可能性——一个类似静态工厂类的类是我们可以实现这一点的少数方法之一。

我考虑了 10 种不同的设计，然后决定采用这种解决方案——有时你必须开始写代码，否则什么也做不了。其他需要考虑的问题包括保存一个结果哈希表，该哈希表可以根据该级别的代码进行缓存(和刷新)和索引，而不是将数据保存在上下文中，等等。

最后，我选择将主要功能实现为一个标记。这允许我教设计人员(非程序员)如何使用标签，并提供合理的性能。此外，当在 Ant 中与标准构建脚本结合使用时，这种方法不需要我的干预，标准构建脚本复制必要的 jar 文件和配置以使标记工作。但是即使没有这些，工作中的设计者也可以确保脚本中存在所需的资产。

在我看来，使用 Servlet 甚至 struts 类的替代解决方案为站点上唯一的动态特性引入了太多的配置。它们还需要我在每次使用服务时进行实际的编程，这对于这个应用程序来说是不合适的。

##### 编写标签

编写标签现在应该相当简单了。我们需要从设计器中获得以下属性:

*   XML 源的 URL
*   该公司的代码(让我们使用 CSW，我的神话般的公司的友好名称-显然有问题的有限公司)
*   我们希望结果存储在其下的属性名

标签不需要有任何正文内容，所以我实现了`TagSupport`。

直接地，这给了我们:

```
package com.clearlysomethingwrong;    

import javax.servlet.http.HttpServletRequest;    

import javax.servlet.jsp.tagext.TagSupport;    

import javax.servlet.jsp.tagext.Tag;    

import javax.servlet.jsp.JspException;    

import org.xml.sax.SAXException;    

import javax.xml.parsers.ParserConfigurationException;    

import java.io.IOException;    

import java.net.URL;    

public class ShowSharePrices extends TagSupport {    

    public String investorURL;    

    public String code;    

    public String friendlyName;    

    public String attName;
```

首先，我们得到请求。我们将在这里存储属性，所以我们将需要它。在实际应用中，我们还可以检查所有值是否有效，是否以有意义的方式提供给用户(标签和系统)有用的错误消息。我们用给定的 URL 字符串创建一个 URL 对象。这个 URL 对象只是让我们访问一个 URL，包括提供一些关于它的细节，并打开一个到它的 InputStream。

如果 URL 的内容是我们想要的，那么这个值就可以传递给我们的投资者信息加载器，同时传递的还有提取适当的股票详细信息的代码和我们公司的可选用户友好名称。

如果结果不为空，我们使用给定的属性名设置属性，并退出:

```
 public int doStartTag() throws JspException {    

        HttpServletRequest request = (HttpServletRequest) pageContext.getRequest();    

        InvestorInformation info = null;    

        try{    

          URL url = new URL(investorURL);    

          info = InvestorInfoLoader.loadInvestorInfo(url.openStream(),     

                                     code, friendlyName);    

        } catch(IOException e) {    

            throw new JspException("Error reading XML source");    

        } catch (SAXException e) {    

            throw new JspException("Sax exception occurred: " + e.getMessage());    

        } catch (ParserConfigurationException e) {    

            throw new JspException("Configuration error:" + e.getMessage());    

        }    

        if(info!=null) {    

            request.setAttribute(attName, info);    

        }    

        return Tag.SKIP_BODY;    

    }
```

除了不言而喻的赋值函数和访问函数，我们现在可以创建标记库描述符了:

```
<?xml version="1.0" encoding="ISO-8859-1" ?>     

<!DOCTYPE taglib    

  PUBLIC "-//Sun Microsystems, Inc.//DTD JSP Tag Library 1.2//EN"    

  "http://java.sun.com/dtd/web-jsptaglibrary_1_2.dtd">    

<taglib>     

  <tlib-version>1.0</tlib-version>     

  <jsp-version>1.2</jsp-version>    

  <short-name>xmlInvestor</short-name>    

  <uri>/xmlInvestor</uri>    

  <description>    

    Investor Information Loader Tag    

  </description>    

  <tag>     

    <name>loader</name>     

    <tag-class>InvestorInfoLoader</tag-class>    

    <body-content>empty</body-content>    

    <description>    

      Loads investor information given a URL of the XML source, a company investor code, and an attribute name to    

  store the resultant InvestorInformation class. Optionally, a friendly name can be provided for the company.    

    </description>     

    <attribute>    

      <name>investorURL</name>    

      <required>true</required>    

      <rtexprvalue>false</rtexprvalue>    

    </attribute>        

    <attribute>    

      <name>code</name>    

      <required>true</required>    

      <rtexprvalue>false</rtexprvalue>    

    </attribute>        

    <attribute>    

      <name>attName</name>    

      <required>true</required>    

      <rtexprvalue>false</rtexprvalue>    

    </attribute>        

    <attribute>    

      <name>friendlyName</name>    

      <required>false</required>    

      <rtexprvalue>false</rtexprvalue>    

    </attribute>        

  </tag>    

</taglib>
```

当根据标准 JSP 标记要求打包时，使用标记就像给出下面的 JSP 文件一样简单:

```
<%@ page import="com.clearlysomethingwrong.InvestorInformation"%>    

<%@ taglib uri="/xmlInvestor" prefix="xmlInvestor"%>    

<xmlInvestor:loader   friendlyName="Something Clearly Wrong"     

                code="CSW" attName="InvestInfo"     

                investorURL="http://localhost:8080/source.xml"/>    

<%    

  InvestorInformation info = (InvestorInformation)request.getAttribute("InvestInfo");    

  if(info!=null) {    

    %><%=info.getName()%><br>    

    <%=info.getCurrentPrice()%><br>    

      <%=info.getChange()%><br>        

      <%=info.getTime()%><br>        

      <%=info.getFormattedDate()%><br>    

      <%=info.getPercentage()%><br>        

<%    

  }    

%>
```

唯一可能的抱怨可能是没有使用另一个或一组标签来输出数据，但是，我发现标签仍然会使 Dreamweaver 这样的程序感到困惑。

基本上提供这些脚本是因为 Dreamweaver 倾向于比它不知道的标签更优雅地处理它们。编写插件需要大量的支持，以防止普通设计人员在需要重新设计时破坏代码。

##### 关于在 JSP 中输出 XML 的说明

我的观点是，除非这个项目保证在软件开发方面的重大投资，而这对于一般的 Web 开发人员来说并不是核心，否则在基于 JSP 的应用程序中使用 DOM 编写复杂的代码来输出 XML 是适得其反的，并且会引入昂贵的维护需求。

总的来说，只需要一个合理的平面结构，提供 XML 输出的直观方法是将文档的格式复制到 JSP 页面中，并插入将插入必要数据的 scriptlets。

我们刚刚设计用来提供共享信息的文档如下所示:

```
<shares>    

  <share>    

    <code><%=info.getName()%></code>    

    <price><%=info.getPrice()%></price>    

          ... etc    

  </share>    

</shares>
```

在许多情况下，这节省了大量时间，非常容易检查(因为将它加载到 Internet Explorer 中也会检查它的有效性)，并且很好地完成了工作。

##### 运行这段代码

我的主要开发环境是 J2SDK1.4，其中 XML 处理是标准的。那些使用 JDK1.3 的用户需要从[http://java.sun.com/](http://java.sun.com/)下载 XML 包。否则，代码只包含标准的 java 类。

为了便于您运行代码，我根据 JSP 标记规范将标记打包在一个 jar 文件中。这使得在 JSP 文件中包含标签提供的功能变得非常简单。只需将 jar 文件放在`WEB-INF`内的 lib 目录中。我建议你花些时间来学习这个简单的方法，因为它可以省去项目结束时通常的配置工作，并且更易于重用。

##### 结论

代码非常简单，但是如果可能的话，这也是应该的。每个类都非常简单，很好地封装了它的角色，没有过多的角色重叠。

最重要的是 SAX 处理——使用上面的模板，很容易修改程序以满足您的任何需求。我们使用 XML 的一个目的是为了 ASP 和 JSP 之间的可靠通信，当我们通过 XML 提供信息时，我们通常会发现它可以用在其他地方。

点击这里下载本教程的示例文件[。](https://www.sitepoint.com/examples/xml/XMLArticle.war)

## 分享这篇文章