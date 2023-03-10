# 使用 coldfusion power 增强您的 flex 应用程序

> 原文：<https://www.sitepoint.com/flex-app-coldfusion-power/>

SitePoint 上有很多教程可以帮助你掌握使用 Flex 和 AIR 创建富互联网应用(RIA)的一些关键原则。您会发现 Flex 中的大多数开发工作都涉及到一个后端应用程序来与 Flex 客户端进行交互。

让我们讨论构成 Flex 应用程序背后的一些理论和原则，然后用 ColdFusion 应用程序将这些原则付诸实践。这里我们假设你已经有了一些 ColdFusion 开发的经验。

注意，因为最后有一个测验。感谢我们的赞助商 Adobe，前 100 名完成测验的人将赢得一本*Flex 3*入门。[参加测验！](https://www.sitepoint.com/quiz/adobe/supercharge-flex-app-coldfusion/)

##### 理解富互联网应用程序的架构

从高层次的角度来看，web 应用程序的通用系统架构通常包括三层。

底层由数据存储层组成，通常是关系数据库系统，如微软的 SQL Server、Oracle 或 MySQL。这种层提供了一个关系表模型，可用于存储和检索应用程序数据。

数据存储层之上的一层称为应用服务器或中间件。这个领域中常用的技术是 Adobe 的 ColdFusion、Java、PHP、Ruby on Rails 或. NET。这些平台用于开发业务和数据访问逻辑。

在此之上，或者甚至可能嵌入在中间件中，我们会发现一个负责 HTTP 交付的层——即像 IIS 或 Apache 这样的 web 服务器。在富 Internet 应用程序中，架构师有时必须处理 HTTP 之外的其他协议:例如，Flash Media Server 等技术支持实时流协议 RTMP。

在我的[早期教程](https://www.sitepoint.com/article/ajaxify-your-flex-application/)中，我展示了 Flex 应用程序如何与客户端的其他应用程序和数据通信。这一次，我们将与服务器端的业务和数据层进行通信。

##### Flex 如何通信

Flex 主要通过三种不同的方法访问远程数据:

*   对交付 Flex 可以使用的数据的平面文件、XML 文件或动态 URL 的 HTTP 调用
*   基于 SOAP 的 web 服务调用
*   操作消息格式(AMF)远程对象调用。

这些方法中的每一个都由不同的 ActionScript 类和 MXML 标签来表示。公平地说，当来自 ColdFusion 背景时，MXML 标记语法可能更容易使用，因为能够使用与 ColdFusion 的 CFML 语法相似的语法，你会感到很舒服。

##### HTTPService:通过 HTTP 检索数据

让我们想想标签`mx:HTTPService`。毫不奇怪，这是一个基于 HTTP 的服务，我们可以用它在运行时从其他地方获取一些数据。在 MXML，这种服务将被声明如下:

```
<mx:HTTPService id="dataService" 

url="http://www.example.com/xmlfile.xml" />
```

`id`属性提供了对服务对象的引用。`url`属性指向一个可以通过 HTTP 访问的静态 XML 文件，但也可以指向一个本地文件，如下所示:

```
<mx:HTTPService id="dataService" url="xmlfile.xml" />
```

在这种情况下，Flex 应用程序希望在与自身相同的路径中找到 xml 文件。

自然，让 ColdFusion 为我们生成一些 XML 是可能的。这里有一个非常简单的方法:

```
<cfsavecontent variable="xmlContent"><data> 

    <result>             

        <item>Taxes</item> 

        <amount>2000</amount> 

    </result> 

    ... 

</data></cfsavecontent> 

<cfcontent reset="true" type="text/xml"><cfoutput>#xmlContent#</cfoutput>
```

标签将创建的静态或动态内容存储在变量`xmlContent`中。然后，我们使用一个`cfcontent`标签来重置我们之前创建的任何输出，并指定一个`text/xml`内容类型。然后我们使用一个简单的`cfoutput`标签传递变量`xmlContent`的内容。瞧，一些 XML 出来了。

在下一页，我们将看到一个使用 XML 的 Flex 应用程序。

满足这种服务调用的 Flex 应用程序如下所示:

```
<?xml version="1.0" encoding="utf-8"?>  

<mx:Application  

  xmlns:mx="http://www.adobe.com/2006/mxml"  

  layout="horizontal"  

  creationComplete="cfmFile.send()">  

    <mx:Script>  

        <![CDATA[  

            import mx.controls.Alert;  

            import mx.rpc.events.ResultEvent;  

            import mx.rpc.events.FaultEvent;  

            import mx.collections.ArrayCollection;  

            [Bindable]  

            private var budgetData:ArrayCollection = new ArrayCollection();  

            private function cfmFileRH(e:ResultEvent):void  

            {  

                budgetData = e.result.data.result as ArrayCollection;  

            }  

            private function cfmFileFH(e:FaultEvent):void  

            {  

                mx.controls.Alert.show(e.fault.faultString,"Error when loading XML");  

            }  

        ]]>  

    </mx:Script>  

    <mx:HTTPService  

    id="cfmFile"  

    url="http://example.com/budgetXML.cfm"  

    result="cfmFileRH(event)"  

    fault="cfmFileFH(event)"  

    />  

    <mx:DataGrid dataProvider="{budgetData}" >  

    </mx:DataGrid>  

</mx:Application>
```

下面是正在发生的事情:我们使用一个`mx:HTTPService`来获取一些数据，并将来自服务的结果存储在一个名为`budgetData`的变量中。当应用程序被创建时，标签上的`creationComplete` 事件处理程序发送`HTTPService`请求。最后，一个`mx:DataGrid`对象将数据显示为表格。

仔细观察`mx:HTTPService`，我们可以看到它包含两个属性:`result`和`fault`。在 Flex 中，任何数据服务请求都是异步处理的，这意味着 Flex 应用程序将立即广播结果事件，而不是等待响应。所有的服务标签都提供了属性`result`和`fault`来处理这些结果。在这些属性中，您只需指定当服务调用返回结果(或错误)时您希望被调用的方法的名称。

实际上来说，`mx:HTTPService`的一个实例仅限于`GET`和`POST`请求，尽管原则上可以发出其他请求——但是这些请求是不可用的，除非 HTTP 请求是通过代理服务器路由的，比如 Adobe 的 [BlazeDS](http://opensource.adobe.com/wiki/display/blazeds/BlazeDS/) 或 [LiveCycle Data Services](http://www.adobe.com/products/livecycle/dataservices/) 。这种限制的原因是 Flash 播放器的安全模型，它只支持通过 HTTP 的直接`GET`或`POST`调用。这就是另一个概念进入游戏的地方:跨域策略文件。

通常，使用`mx:HTTPService`或基于 SOAP 的 web 服务的基于 Flash 的应用程序只能从与。swf 文件。为了确定数据是否来自同一个域，Flash Player 会比较用于 Flash 文件和远程数据源的域名。这意味着从 http://localhost/test.swf 加载的应用程序无法从 http://127 . 0 . 0 . 1/data/xmlfile . XML 访问 HTTP 数据，即使它们是同一台服务器。一种解决方案是使用名为 crossdomain.xml 的跨域策略文件，该文件位于提供数据的服务器的 web 根目录中。你可以在 Adobe 的文档中了解更多关于如何使用跨域策略文件[的信息。](http://www.adobe.com/devnet/flashplayer/articles/fplayer9_security.html)

接下来，让我们了解如何在 Flex 中发送请求参数。

##### 发送请求参数

也许您需要向您的服务发送参数。传递参数最简单的方法是添加一个`mx:request`标记作为 HTTP 服务的嵌套标记。假设我们需要向 ColdFusion 脚本发送两个参数，`dataMethod`和`userType`。我们将像这样使用`mx:request`元素:

```
<mx:HTTPService id="dataService" url="http://example.com/sendDataRequest.cfm" method="POST">   

   <mx:request>   

       <dataMethod>getAll</dataMethod>   

       <userType>administrator</userType>   

    </mx:request>   

</mx:HTTPService>
```

因为我们使用 HTTP `POST`方法，所以我们在这里创建的所有请求变量都将成为 ColdFusion 的`FORM`范围内的 ColdFusion 变量。在 ColdFusion 方面，我们因此将在 ColdFusion 模板上以`FORM.dataMethod`和`FORM.userType`结束。如果您选择了 HTTP `GET`(通过指定`method="GET"`)，您的请求数据将变成 URL 变量:在本例中是`URL.userType`和`URL.dataMethod`。

到目前为止，我们只是研究了如何通过 HTTP 服务从 ColdFusion 模板返回 XML 数据。虽然这是与 HTTP 服务和 ColdFusion 交互的一种非常常见的方式，但在某些情况下，HTTP 服务还有一些其他替代的返回格式可能更适合使用:

*   `object`:响应数据是 XML，将被转换成 ActionScript 对象树(`ArrayCollection`、`ObjectProxy`)
*   `xml`:响应数据是 XML，将被转换成类型为`XMLnode`的 ActionScript 对象-这是一种传统格式，这里只是为了兼容；最好避免使用。
*   `e4x`:响应数据是 XML，将被转换成 ActionScript XML 对象
*   `flashvars`:响应数据是一串键/值对:`name1=value1&'name2=value2`，依此类推
*   `text`:响应数据为文本，不进行转换。

```
resultFormat="object" is the default, and is quite often the right way to go. If you want to work with XML instead of ArrayCollections and ObjectProxy, e4x (ECMAScript for XML) is the preferred result format - xml makes use of a deprecated set of API classes that's part of Flex for compatibility reasons only.使用 ColdFusion 提供服务

所以:我们在这篇文章中花了很多时间讨论 HTTP 服务和服务的一般结构。既然我们理解了这些是如何工作的，讨论 web 服务和远程对象就变得容易多了。

让我们以下面的 ColdFusion 组件为例，它向外界提供了一个简单的`echo`方法，并尝试构建一个与 CFC 挂钩的 Flex 客户端:

```
<cfcomponent>x   

    <cffunction   

      name="echo"   

      returntype="string"   

      access="remote"   

      output="false"   

      hint="I echo whatever I'm being passed">   

        <cfargument name="input" type="string" required="true">   

        <cfreturn "Echoing..." & arguments.input>   

    </cffunction>   

</cfcomponent>
```

ColdFusion 提供了两种向调用者公开组件方法功能的方式:作为基于 SOAP 的 web 服务或作为远程对象。让我们每一个都试试。

##### 肥皂

从 SOAP web 服务开始，我们通过在`cffunction`标签中设置属性`access="remote"`使 echo 方法成为 web 服务方法。同时，该设置将使我们能够作为远程对象调用该方法。我们希望检索 WSDL 格式的数据，只需在 URL 的末尾添加`?WSDL`即可。

为了抓住 Flex 中的 WSDL，我们从使用`mx:WebService`开始，它的结构与`mx:HTTPService`非常相似:

```
<mx:WebService    

  id="dataWS"    

  wsdl="http://example.com/service.cfc?WSDL"    

  result="onDataServiceResult(event)"    

  fault="onDataServiceFault(event)"    

/>
```

对于 web 服务，您经常会发现一个服务提供了多种方法，并且您更喜欢为每个服务指定一个结果或错误处理程序方法。使用`mx:operation`时，这是可能的:

```
<mx:WebService    

      id="dataWS"    

      wsdl="http://example.com/service.cfc?WSDL"    

      fault="onDataServiceFault(event)"    

    >    

        <mx:operation name="echo" result="onResultEcho(event)" />    

    </mx:WebService>
```

为 web 服务调用提供参数的工作方式类似于为 HTTP 服务调用提供参数——通过`mx:request`——但是也可以使用 Java 或 C++等语言的开发人员熟悉的语法来完成:

```
dataWS.echo("Hello")
```

##### 动作消息格式

现在，让我们用行动信息格式(AMF)来试试。AMF 是一个已经存在了很长时间的协议，AMF3 的规范是由 Adobe 在 2007 年 12 月公开发布的。在服务器端，您需要支持 AMF3 的服务——幸运的是，作为 ColdFusion 开发人员，您已经准备好提供这种开箱即用的服务。

在 Flex 中，我们可以使用`mx:RemoteObject`标签与远程对象通信:

```
<mx:RemoteObject    

  id="dataRS"    

  destination="ColdFusion"    

  source="example.service">    

    <mx:method    

      name="echo"    

      result="onResultEcho(event)"    

      fault="onFaultEcho(event)"    

  />    

</mx:RemoteObject>
```

将这段代码与 Flex 中等效的 web 服务声明进行比较，有一些小的不同:我们现在处理的是名为“ColdFusion”的目的地，而`mx:operation`已经更改为`mx:method`。

当用户第一次在 Flex Builder 中为 ColdFusion 设置 Flex 项目时，设置向导会询问一系列关于 ColdFusion 服务器的位置、端口、URL 和上下文根的问题。所提供的信息将在 Flex 应用程序编译时用于向 Flex 提供您的 AMF URL 的详细信息。这对于简单的数据类型(如字符串或布尔值)甚至是内置的 ColdFusion 类型(如数组和结构)来说都很容易实现。

您甚至可能希望处理来自应用程序业务领域的整个对象，并来回传递它们。有一种称为[数据传输对象或值对象](http://www.adobe.com/devnet/flashremoting/articles/facades.html)的设计模式描述了这样一个过程，在 ColdFusion 组件中使用这种方法相当简单。您在 Flex 中的服务配置将基本保持不变，但它将是一个域对象，代表客户、购物车、员工或您计划处理的任何对象，而不是发送或期待来自远程对象调用的简单类型。要让 Flex 处理如此复杂的对象类型，ColdFusion 也必须了解它们，并且 ColdFusion 中必须有一个等效的组件。这是一个描述用户的 CFC:

```
<cfcomponent displayname="User" output="false">    

   <cfproperty name="user_id" type="numeric" />    

   <cfproperty name="user_name" type="string" />    

   <cffunction name="init" access="public" returntype="example.User" output="false">    

      <cfargument name="user_id" type="numeric" required="false"  />    

      <cfargument name="user_name" type="string" required="false"  />     

      <cfset this['user_id'] = arguments.user_id />    

      <cfset this['user_name'] = arguments.user_name />    

      <cfreturn this />    

   </cffunction>    

</cfcomponent> 
```

在下面的最小 ActionScript 3 类中，我们还描述了用户对象应该包含的内容:

```
[Bindable]    

[RemoteClass(alias="example.User")]    

public class User    

{    

        public var user_id:int=0;    

        public var user_name:String="";    

}
```

元数据标签`[Bindable]`允许在 Flex 数据绑定中使用`User`。`[RemoteClass(alias="example.User")]`将服务器端的 CFC 类型`example.User`映射到 ActionScript 中的`User`类。

##### 现在怎么办？

我们将何去何从？如果你只使用 ColdFusion，我猜你会发现对于这类项目只使用 RemoteObjects 更容易。如果您有一个包含多种技术的混合环境，那么研究 web 服务和 HTTP 服务调用来加载 XML 可能是值得的。无论哪种方式，你现在都有能力处理这两种情况:是时候享受乐趣了！

**觉得自己已经准备好用 ColdFusion 大干一场了吗？为什么不通过我们的测验来测试你的新知识呢——只有五个简单的问题，所有的答案都在本文中。前 100 名参加我们测验的人将赢得一本*Flex*入门，免费送货上门。[抓住你的！](https://www.sitepoint.com/quiz/adobe/supercharge-flex-app-coldfusion)** 

## 分享这篇文章

```