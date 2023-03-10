# ASP 基本提示和技巧

> 原文：<https://www.sitepoint.com/essential-asp-hints-tips/>

去年年初，我在 SitePoint 论坛上发了一个帖子，讲述了我在编写 ASP 时使用的技巧和技术。从那以后，这个帖子有了很多修改和补充，我已经在这里列出来了。

在 ASP 和 PHP 的讨论中，一个反复出现的说法是 PHP 更快。现在，我可能会被贴上叛徒的标签，并因为说了这些而收到威胁邮件，但我有时会同意。PHP 可以比写的很差的 ASP 更快。

在 ASP 中有很多做事的方法，有时候选择错误的方法比选择正确的方法更容易。新的解决方案总是在开发中:直到最近我才知道下面的一个技巧。每个人，就 ASP 而言，似乎都有不同的做事方式，我们不可能都是对的。我不是说我是对的，你是错的；这对我来说很简单。如果您有替代建议，[请发送到](mailto:editor@sitepoint.com)！好了，我们开始吧！

##### 结肠

去年年底，我偶然发现了这颗小宝石。据我所知，它根本不会影响你的脚本的性能。冒号允许您将跨越多行的代码压缩为一行。下面你可以看到一个例子。

```
Set objDBCon = Server.CreateObject("ADODB.Connection") 

strConnect = "Provider=Microsoft.Jet.OLEDB.4.0;Data Source=" &  

Server.MapPath("./data.mdb") 

objDBCon.open strConnect
```

使用冒号，我们可以缩短代码:

```
Set objDBCon = Server.CreateObject("ADODB.Connection") : strConnect =  

"Provider=Microsoft.Jet.OLEDB.4.0;Data Source=" &  

Server.MapPath("./data.mdb") : objDBCon.open strConnect
```

##### 更新 PWS

当我开始编写 ASP 时，我使用 PWS 的 Windows 98 作为我的开发平台。相比我现在用的(Windows。NET Server，IIS6)就像把一只羊比作一辆车……但那是我当时的全部，我心存感激。但是一件非常烦人的事情是，PWS 是 IIS4 的精简版本，这意味着你必须用 ASP 2.0 编码。厌倦了错过 ASP 3.0 的所有酷功能，我去寻找升级它的方法。

我最终发现，通过升级 WSH (Windows 脚本主机)，可以使用 PWS 访问一些 ASP 3.0 功能。请记住，并非 ASP 3.0 中的所有功能都可用:您实际做的是升级 VBScript，而不是实际的 ASP.DLL 文件(因此您将错过对响应、请求、服务器等的更新。对象)。这意味着`server.execute`和`server.transfer`等功能不可用。

在撰写本文时，WSH 的最新版本是 5.6。要快速找出您拥有的版本，请尝试以下代码:

```
<% 

s = ScriptEngine & " Version " 

s = s & ScriptEngineMajorVersion & "." 

s = s & ScriptEngineMinorVersion & "." 

response.write(s) 

%>
```

如果返回的版本低于 5.6，你应该[现在就升级](http://msdn.microsoft.com/downloads/default.asp?URL=/downloads/sample.asp?url=/msdn-files/027/001/733/msdncompositedoc.xml)！

##### Eval()标记

如果你懂 JavaScript，你会知道`Eval()`函数已经存在很多年了，无论是在客户端还是服务器端。该函数对拥有版本 5 的 VBScript 开发人员可用，并随 Windows 2000 一起提供。

它是做什么的？如果你在 JavaScript 中使用过它，那么你已经知道了。它评估字符串以返回 true 或 false。有道理吗？没有吗？这里有一个简单的例子。

```
<% 

evalString = "meetingTime = time()" 

meetingTime = request.form("meetingTime") 

response.write(evalString) 

%>
```

你们应该都知道上面的脚本是做什么的:它应该简单地将`evalString`返回给浏览器:

```
meetingTime = time()
```

但是如果我们像这样使用 Eval()函数:

```
evalString = "meetingTime = time()" 

meetingTime = request.form("meetingTime") 

response.write(eval(evalString))
```

根据值是否匹配，`evalString` 将被评估为真或假。

##### 执行语句

如果你需要返回真或假，函数当然很好，但是如果你有一个你想执行的 ASP，比如一个函数或子程序，该怎么办呢？你可以的！`execute()` 函数可以让你执行整行代码！试试这个:

```
<%  

executeString = "Sub checkMeeting(timeOfMeeting)" & vbcrlf _  

   & "   If ( eval(meetingTime = time()) ) then" & vbcrlf _  

   & "      response.write(""Meeting!!"")" & vbcrlf _  

   & "   else" & vbcrlf _  

   & "      response.write(""No meeting"")" & vbcrlf _  

      & "End If" & vbcrlf _  

   & "end sub" & vbcrlf  

execute(executeString)  

   ' loads of code here...  

checkMeeting(request.form("meetingTtme"))  

%>
```

这是什么意思？你不仅可以在你的站点上包含动态内容，还可以包含动态 ASP

##### 随着

“`with`”构造是 VBScript 5 的新增功能，它允许您在创建对象的同时定义对象的属性，而无需重新限定对象。让我们来看看:

```
with object  

   .property = "with this value"  

   .property2 = "also with this"  

   doSomething = .execute  

end with
```

现在，实际上？让我们创建一个记录集:

```
Set RS = Server.CreateObject("ADODB.Recordset")  

With RS  

   .CursorLocation = 3  

   .Open "SQL statement", connectString  

   .PageSize = 3  

   .AbsolutePage = CurrPage  

End With
```

##### 文本日期

这里有一个快速的！有两个函数可以用来返回文本版本的月份和星期几，`MonthName()`和`WeekDay()`。这两个函数在 VBScript 的第二版中就已经存在了，但是我遇到过很多不知道它们存在的人。

它们非常简单:

```
Response.write(MonthName(month(date())))  

Response.write(weekday(date()))
```

##### 是一个对象。

我有一个脚本，当它被调用时，检查是否已经建立了到数据库的连接，如果没有，它就建立一个连接(反之亦然)。它通过检查我使用的变量是否是一个对象来做到这一点。

我为此使用了两个函数:`isObject()`和`typeName()`。这些函数分别从 VBScript 的版本 1 和版本 2 开始就已经存在了。`isObject()`函数返回一个布尔值，而`typeName()`返回一个字符串。

怎么才能应用呢？像这样:

```
Dim objDBCon  

Public Function connectDB()  

On Error Resume Next  

If ( NOT isObject(objDBCon) OR _  

     cStr(typeName(objDBCon)) = "Nothing" ) then  

Set objDBCon = Server.CreateObject("ADODB.Connection")  

strConnect = "connect details"  

objDBCon.open strConnect  

else  

objDBCon.close : Set objDBCon = nothing  

end if  

If ( err.Number <> 0 ) then  

response.write("<font face=""tahoma"" color=""red""   

size=""-1""><strong>There seems to be a problem with   

the database.  Please bear with us while we repair   

it.<br /><br />We apologise for any inconvenience   

this may cause.</strong></font>")  

response.end  

end if  

End Function
```

##### 用户还在吗？

我开发的每个页面都调用下面的子程序。这是使用响应对象的`isClientConnected`方法。此方法返回一个布尔值，表明客户端是否仍在等待响应。该函数对此进行检查，如果返回 false，则清除并结束响应。对于那些“谁在线”的脚本来说，这是非常方便的，并且在大量数据检索之前节省了资源！

```
Public sub checkClientConnect()  

If ( NOT response.IsClientConnected )   

thenresponse.clearresponse.endend ifEnd Sub
```

##### Server.transfer()

在 ASP 3 中引入，这是为了取代`Response.Redirect()`。`Response.Redirect()` 告诉浏览器加载不同的页面，因此，浏览器必须请求两个页面。这使得服务器处理两个页面，并创建从客户端到服务器的两次往返。

```
Server.Transfer() is designed to make up for this by simply executing a different script without letting the browser know about the change.  Use it the same way as Response.Redirect(), with the limitation that Server.Transfer() cannot transfer execution to a page on a different server.  

```

```
Server.Transfer("pageName.asp")
```

##### 服务器。执行()

ASP 3 中也引入了,`Server.Execute()` 像包含文件一样工作。但是与包含文件不同，它在页面被解释时被处理，这意味着它可以更有效地用于`If...End If`语句。

但是它到底是做什么的？它将服务器执行转移到另一个页面，当该文件完成时，它将执行返回到原始页面。这意味着页面不会像服务器端包含(SSI)那样自动包含。

##### 减轻

缓冲页面。简单明了:打开响应缓冲，这在 ASP 3 中是默认的。这将减少数据发送到客户端的次数，从而提高整体性能。当 TCP/IP 可以发送几个大数据块时，它的工作效率要比必须发送许多小数据块时高得多，因为它的数据包开销相对较高。

怎么做？在向客户端发送任何内容之前，添加以下行:

```
<%response.buffer = True%>
```

##### 弗拉辛

打开缓冲后，用户会觉得 ASP 运行得很慢(这是 PHP 运行得更快的原因吗？)，当然不是这样。它只是等待整个页面加载后再发送到浏览器，从长远来看，这样做确实更快。但是因为用户会看到几秒钟的空白页面，所以看起来很慢。

您可以使用 flush 命令来解决这个问题，并选择何时向客户端发送数据，如下所示:

```
response.flush()
```

当调用此方法时，从页面开始或自上次刷新页面以来的所有内容都将被转储到浏览器。

##### 更快地遍历数据库

遍历一堆数据库记录并显示它们，我想这是您必须经常做的事情。如果你还不知道，我保证有一天你会知道的。

现在，你可能学会了使用这种技术或类似的东西:

```
Set getData = objDB... ' get data from database   

Do Until getData.EOF   

   response.write("<p>Name: " & getData("name") & "<br>" & vbcrlf _   

   & "Age: " & getData("age") & "<br>" & vbcrlf _   

   & "Position: " & getData("position") & "</p>" & vbcrlf)   

   getData.MoveNext   

Loop
```

眼熟吗？原以为如此…

现在让我们让它更快更高效吧！

```
Set getData = objDB... ' get data from database   

Set name = getData("name")   

Set age = getData("age")   

Set position = getData("position")   

Do Until getData.EOF   

   response.write("<p>Name: " & name & "<br>" & vbcrlf _   

   & "Age: " & age & "<br>" & vbcrlf _   

   & "Position: " & position & "</p>" & vbcrlf)   

   getData.MoveNext   

Loop
```

但是为什么更快呢？因为，在循环中我们不需要指定我们想要访问的字段——已经指定了，数据已经被引用了！

##### 检查服务器上是否有可用的对象

尼科斯·利奥卡罗斯

这个函数检查一个对象在服务器上是否可用。

```
 Function CheckObject(obj)  
  Dim msg  
  On Error Resume Next  
  CreateObject obj  
  If Err = 0 then  
      msg = "OK"    
    else    
      msg = "Error:" & Err.Description  
    end if      
  CheckObject = obj & " - " & Msg & "<br>" & vbCrLf  
  set obj = nothing  
End Function 
```

你可以这样使用它:

```
Response.write(CheckObject("ADODB.Recordset"))   

Response.write(CheckObject("Scripting.FileSystemObject "))
```

…或者检查应用程序所需的任何其他对象。

Nikos Liokalos 提供的这个提示。Nikos 拥有计算机科学学位和电子商务技术理学硕士学位。他的兴趣在于内容管理和电子商务领域，业余时间他会更新自己的网络空间。

##### 结论

正如我在开始时提到的，有许多方法可以用 ASP 实现特定的目标。我在这里展示的只是我用来完成工作的一些技巧——但是请随意在中发送你自己的想法。

我希望你在使用我所介绍的技术时有乐趣，最重要的是，我希望你喜欢编写 ASP！

## 分享这篇文章