# ASP 中的蜘蛛捕捉

> 原文：<https://www.sitepoint.com/spider-catching-asp/>

**正如你们许多人可能意识到的那样，[关于提供网络蜘蛛独特内容(又名](http://www.promotionbase.com/article/756)[“伪装”](http://www.searchengineworld.com/misc/cloaking.htm))的辩论已经成为 SitePoint.com 的热门话题，[论坛](http://www.sitepointforums.com/)已经挤满了帖子。一些人支持这种做法，另一些人回避这种做法，许多人警告这会损害你在搜索引擎中的声誉(禁止是针对“伪装”网站的常见手段)。**

我将向您展示一些我在 ASP 中用来隐藏我自己的页面的高级算法，希望不相信隐藏的人和相信隐藏的人都会感兴趣。

##### 传统方法

传统的隐藏方法包括检测`[USER_AGENT](http://www.siteware.ch/webresources/useragents)`，然后根据这个标准为用户提供另一个页面。你可以在这里获得[常用用户代理列表。你提供的页面当然会有丰富的元标签和你希望优化你的网站的词。实现这一点的代码如下。它应该出现在页面的顶部:](http://www.icehousedesigns.com/useragents/)

```
<%  

Dim spidercheck 

Spidercheck = Request.ServerVariables("HTTP_USER_AGENT") 

If  Spidercheck = "Googlebot" Then  

Server.Redirect("spiderrichpage.asp") 

%>
```

熟悉 ASP 的人可以扩展这段代码，在遇到任何已知的蜘蛛名称时进行重定向。你可以在这里找到详细的名单。

***我的访客看到了什么？***

因为所有的代码都是在服务器端执行的，所以你的访问者看不到任何东西。蜘蛛会向一个方向移动，你的访问者会看到代码所在的页面。然而，还记得我们如何把蜘蛛送到 spiderrichpage.asp 吗？此页面包含与您的站点相关的元标签和内容。这是蜘蛛将索引并存储在其数据库中的页面；因此，它也将是出现在搜索结果中的页面。

由于我们不希望您的潜在访问者看到一个丑陋的包含以蜘蛛为中心的内容的 META 标签页面，我们还必须在该页面上放置代码，以将您的访问者发送到您希望他们看到的页面。

所以，在 spiderrichpage.asp 的顶部我们插入:

```
<%  

Dim spidercheck 

Spidercheck = Request.ServerVariables("HTTP_USER_AGENT") 

If  Spidercheck <> "Googlebot" Then  

Server.Redirect("visitorpage.asp") 

%>
```

##### 非常规算法

好了，我们已经为一个特定的蜘蛛名提供了一个页面。但是如果搜索引擎正在反击呢？假设他们试图通过使用与用于识别浏览器的用户代理几乎相同的蜘蛛名称来欺骗我们的脚本。任何维护 Web 日志的人都会认出以下用户代理:

Mozilla/5.0(兼容；MSIE 6.0Windows 2000)
Mozilla/4.0(兼容；MSIE 6.0Windows NT 5.1MSIECrawler)

***哪种是哪种蛛形纲动物？***

发现蜘蛛？是的，没错！以 MSIECrawler 为用户代理的就是蜘蛛。许多蜘蛛试图保持友好，靠近浏览器名称，这样他们就不会被发现和重定向。你如何应对这种情况？ASP 已经内置了一个很好的函数来帮助我们。

```
Instr stands for IN STRING, and with this we can detect if words such as CRAWLER or BOT or SPIDER appear within the User Agent. Another problem with my original code is that it only detects one spider at a time (unless you extend it to use IF THEN ELSE statements). I'm now going to attempt to kill two spiders with one can of fly spray! The dictionary object can be easily added to as more spider names appear in your site logs: 

```
<% 

  Sub AddViolation(objDict, strWord) 

    'Adds a violation (a robot in this case) 

    objDict.Add strWord, False 

  End Sub 

  Function CheckStringForViolations(strString, objDict) 

    'Determines if the string strString has any violations 

    Dim bolViolations 

    bolViolations = False 

    Dim strKey 

    For Each strKey in objDict 

      If InStr(1, strString, strKey, vbTextCompare) > 0  

        bolViolations = True 

        objDict(strKey) = True 

      End If 

    Next 

  CheckStringForViolations = bolViolations 

  End Function 

  Dim objDictViolations 

  Set objDictViolations = Server.CreateObject("Scripting.Dictionary") 

  AddViolation objDictViolations, "Googlebot" 

  AddViolation objDictViolations, "Lycos" 

  AddViolation objDictViolations, "Ultraseek" 

  AddViolation objDictViolations, "Sidewinder" 

  AddViolation objDictViolations, "InfoSeek" 

  AddViolation objDictViolations, "Scooter" 

  AddViolation objDictViolations, "WebCrawler" 

  AddViolation objDictViolations, "UTV" 

  Dim strCheck, strKey 

  strCheck = Request.ServerVariables("HTTP_USER_AGENT") 

  If Len(strCheck) > 0 then 

    If CheckStringForViolations(strCheck, objDictViolations) then 

  Response.Redirect("spiderrichpage.asp") 

    Else 

Response.Redirect("userpage.asp") 

    End If 

  End If 

%>
```

现在我们有了一种方法，可以在蜘蛛出现时添加它们，并为它们提供一个特殊的页面。你们这些高级程序员将能够为不同的蜘蛛发送不同的页面——我将把这作为一个学习练习。

##### 创新算法

下一个算法非常有趣。所有的网页设计者和开发者都知道，元标签仍然在一些搜索引擎排名系统中发挥着很小的作用。那么，如果我们可以改变我们的元标签来匹配蜘蛛正在搜索的任何东西呢？是的，这是可能的。我们可以使用`HTTP_REFERRER` 来确定搜索蜘蛛在寻找什么，然后将它们写入我们的元标签，如果我们愿意，还可以写入我们的主内容。

```
<%  

If InStr( Request.ServerVariables("HTTP_REFERER"), "google") > 0 Then  

KeyURL = Request.ServerVariables("HTTP_REFERER")  

'KeyURL = "http://www.google.com/search?hl=en&ie=  

ISO-8859-1&q= spider+food+for+fun&btnG=Google%20Search"  

' Remove all up to q=  

KeyLen = Len(KeyURL)  

kStart = InStr( KeyURL, "q=" )  

kStart = kStart  + 1  

KeyRight = KeyLen - kStart  

Keyword = Right( keyURL, KeyRight )  

' Check for trailing query string and remove text  

If Instr(Keyword, "&") > 0 Then  

kEnd = InStr(Keyword, "&")  

kEnd = kEnd - 1  

Keyword = Left( Keyword, kEnd )  

End If  

' Turn encoding into text phrase  

Keyword = Replace(Keyword, "+"," ")  

Keyword = Replace(Keyword, ",",", ")  

Keyword = "," & Keyword  

End If  

%>
```

 ***这个怎么用？***

现在，您可以使用 Response 将该关键字写入您的内容。写(关键词)。这可以是你的元标签的空间，也可以是隐藏在页面之外的 html 层。

##### 提示和技巧

本文中提供的代码可以很容易地修改，只要有点创造力，您就可以设计一些有趣的算法。您可以修改它，为每个蜘蛛提供不同的页面，也许可以在数据库中跟踪它们，或者为 Web 社区设计一个统计系统来显示蜘蛛访问您站点的频率。

有很多很好的推广工具来优化你打算提供给蜘蛛的页面。下面列出了本文中使用的链接，以及一些您在优化页面时可能感兴趣的额外链接。

*   [www.searchenginewatch.com](http://www.searchenginewatch.com)

*   [www.webposition.com](http://www.webposition.com)

*   [www.spiderfood.net](http://www.spiderfood.net)

*   [www.icehousedesigns.com/agents/](http://www.icehousedesigns.com/agents/)

*   [www.siteware.ch/webresources/useragents/](http://www.siteware.ch/webresources/useragents/)

## 分享这篇文章

```