# 用 ASP 处理提交的数据

> 原文：<https://www.sitepoint.com/handling-submitted-data-asp/>

开发人员热衷于使用动态服务器页面(ASP)来构建网站有两个主要原因:

*   它增加了交互性，而不会像 JavaScript 那样将您的代码暴露在审查之下，无论是出于安全原因还是为了保护您的知识产权。
*   它有助于利用服务器端资源(如数据库或信用卡处理设施)来增强您的站点的功能或使其更易于管理。

在这篇 site point ASP 系列的第三篇文章中，我们将关注这两个目标中的第一个。我们将看看 ASP 提供的请求和响应对象，以及如何使用它们来接收用户在表单中提交的信息，并生成动态计算的结果作为回报。因为我们之前还没有涉及 ASP 中对象的概念，所以我将在我们进行的过程中解释这些概念，以确保您能跟上进度。

本文假设您熟悉本系列前两篇文章中的概念:[ASP 入门](http://www.webmasterbase.com/article.php/371)和 [ASP 语言基础](http://www.webmasterbase.com/article.php/387)。如果你还没有读过它们，并且是第一次接触 ASP，我建议在进入本文之前至少浏览一下。

如果你准备好了，让我们开始吧…

##### 内置 ASP 对象

你可能偶尔听说过术语**面向对象编程**,无论是在报纸上招聘程序员的广告中，还是在其他一些与当前编程趋势相关的讨论中。很抱歉，严格地说，用 ASP 编写动态网页不是面向对象的编程。开发一个典型的 ASP 网站所涉及的大多数编程本质上都是过程性的；也就是说，它涉及到编写一系列要一次执行一条的指令，有可能出现循环和条件控制结构，就像我们在本系列的上一篇文章中看到的那样。

然而，ASP 的特殊之处在于，ASP 本身实际上内置了很少的功能。相反，ASP 是一个简单的框架，它允许你利用用其他面向对象语言(通常是 C++)编写的软件对象库来完成大部分繁重的工作。

一个软件对象基本上是两个东西的组合:**属性**和**方法**。ASP 中典型软件对象的一个例子可能是存储在服务器硬盘上的文件。文件的**属性**包括其:

*   姓名，
*   路径，
*   MIME 类型(如 gif 文件中的“image/gif ”),以及
*   尺寸。

**方法**是可以使用对象采取的动作。为了继续我们的文件对象，方法可能包括:

*   从文件中读取一行文本，
*   将文件移动到不同的目录，或者
*   完全删除文件。

属于特定对象的属性和方法取决于该对象的**类**(或类型)(即类“file”的每个对象都有一个文件名)。但是，属性的特定值对于每个对象都是唯一的(即每个文件都有自己的文件名，该文件名可以与其他所有文件不同)。

然而，在我进一步迷惑您之前，让我告诉您，我们已经在到目前为止所看到的简单脚本中使用了一个对象，让您稍微平静下来。事实上，这是我们看到的第一个脚本中非常简单的一行。你能认出那个物体吗？

```
Response.Write "<p>This is a test of ASP.</p>"
```

这一行的对象是`Response`。这是一个特殊的内置对象，可用于所有 ASP 脚本，它允许您写入或控制发送回浏览器以响应页面请求的数据流。响应对象也有一个对应对象，称为请求对象，我们将在本文后面详细讨论。在上面的代码行中，我们使用了`Response`对象的`Write`方法来编写一行 HTML 代码，作为页面的一部分返回给请求浏览器。所以代码`Response.Write`大致可以念成“在响应对象中，调用 Write 方法”。

组成该行剩余部分的字符串`"<p>This is a test of ASP.</p>"`，是一个传递给 Write 方法的**参数**。参数提供了方法完成工作所需的任何信息。在这种情况下，Write 方法需要知道要写出什么，而参数提供了该信息。

当然，响应对象不仅仅只有这一个方法。让我们看看其他一些方法，以及它支持的一些属性。

##### 响应对象

作为控制与发送回浏览器的响应相关的一切的对象，response 包含支持此目的的属性和方法。我们已经看到了 ASP 中最重要和最常用的方法 Write 方法——的实际应用，所以让我们看看 Response 支持的方法的另一个例子:Redirect 方法。

```
Response.Redirect "https://www.sitepoint.com/"
```

Redirect 方法向浏览器发送一个特殊的超文本传输协议(HTTP)代码(特别是 HTTP 响应代码“302 Object Moved”)，通知它所请求的页面实际上位于不同的地址，它应该尝试加载该地址。有问题的地址作为该方法的参数提供。因此，上面的行将导致浏览器加载 http://www.sitepoint.com，而不是它最初请求的文件。

当您希望将用户发送到另一个页面时，Redirect 方法非常有用。例如，当用户通过单击 logout.asp 的链接从您的站点注销时，您可能希望在用户从系统中注销后将他或她返回到您站点的登录页面(例如 login.asp):

```
<% ' logout.asp -- log out and redirect to the front page  

If bLoggedIn Then  

  ' ... Code to log the user out ...  

End If  

Response.Redirect "login.asp" ' Send back to login page  

%>
```

由于 Redirect 方法的工作方式(向浏览器发送 HTTP 响应代码)，它可能不会在任何文本或 HTML 作为页面的一部分发送后被调用。换句话说，如果你想把用户重定向到一个不同的页面，你必须在显示这个页面的任何部分之前做这件事——你不能两者兼得。

Response 对象提供的许多其他方法都与控制页面内容如何发送到浏览器有关。但是在我们看这些之前，我们必须首先检查响应对象的一个**属性**:Buffer。

```
Response.Buffer = False
```

正如您在这个示例中看到的，对象属性的行为与变量完全一样，只是您必须指定它所属的对象，后跟一个点(。)当你想参考的时候。在上面的行中，我们将响应对象的 Buffer 属性赋值为 False。

如果 **Buffer 属性设置为 True** ，ASP 将等待负责该页面的 ASP 脚本执行完毕，然后将整个页面作为一个大块发送给浏览器。如果 **Buffer 设置为 false** ，页面的内容将在脚本生成时逐行发送到浏览器。一般来说，一次发送页面比一点一点发送要快；因此，从 ASP 3.0 开始，Buffer 默认设置为 True。但是，如果您的脚本特别长，或者如果您希望访问者能够看到脚本生成时的输出，您可以使用上面的代码行将 Buffer 属性设置为 False。

即使你的脚本很长，你通常也希望对什么时候发送回浏览器有所控制。Response 支持的另外两种方法为您提供了这种控制。这些方法仅在响应时有效。缓冲区为真:

```
Response.Flush          ' Immediately send everything accumulated  

                        ' in the response buffer so far.  

Response.Clear          ' Throw away everything accumulated in  

                        ' the buffer without sending it.
```

例如，如果一个长脚本正在执行一系列复杂的计算，并且您想要显示每个计算完成时的结果，那么您可以使用 **Flush 方法**如下构建您的代码:

```
For calcNo = 1 To 1000  

  ...                                 ' Perform calculation...  

  Response.Write "The results of calculation number " & calcNo  

  Response.Write " are as follows..."  

  ...                                 ' Print out the results...  

  Response.Flush                      ' Send the results now  

Next
```

同时， **Clear 方法**在意外情况发生并且您想要打印出错误消息时非常有用。如果你在一个 HTML 表格的中间，ASP 脚本中发生了错误，你不能简单地打印出一个错误信息并在那里结束脚本，因为不完整的表格可能会导致一个页面不可见。使用 Clear 方法可以避免此类问题，它会在打印错误消息之前取消显示不完整的页面内容:

```
If errorHasOccurred Then  

  Response.Clear                   ' Clear the output buffer  

  Response.Write "An error has occurred!"  

  Response.End                     ' Terminate the page here  

End If
```

**End 方法**(上面用的)刷新缓冲区(如果 buffer 设置为 True，就是这样)，然后在那个点停止执行脚本。End 方法的另一个常见用途是，如果确定客户端(或浏览器)已经移动到另一个页面，或者已经停止接收该页面，则中止长时间的计算。通过检查响应对象的 **IsClientConnected** 属性来检测这种情况:

```
' Long calculations...  

If Not Response.IsClientConnected Then  

  Response.End  

End If  

' More long calculations...
```

若要了解 Response 对象支持的其他属性和方法，请查阅 Internet Information Server 或 Personal Web Server 附带的文档。

##### 请求对象

当 Web 浏览器向 Web 服务器请求网页时，它可以发送比简单的 URL 更多的信息。例如，如果用户提交一个表单来发出请求，那么用户在表单中输入的所有内容都会随页面请求一起发送。如果用户被分配了一个惟一的会话 ID，这个 ID 维护着一个他们选择购买的目录项目的购物篮，那么这个会话 ID 通常会作为一个 cookie 和每个页面请求一起被发送到这个站点。

如果您对处理将网页发送到请求浏览器的过程的响应对象的概念比较熟悉，那么对于您来说，还有一个处理请求的所有细节的请求对象也是有意义的。

Request 对象最常见的用途是检索浏览器随请求一起发送的信息。这些值可以有几种形式:

*   在查询字符串中定义
*   作为表单的一部分提交
*   存储在 cookies 中

对于其中的每一个，请求对象都有一个特殊的属性类型，称为集合，它包含作为请求的一部分通过这些方法提交的所有值。为了进行演示，让我们从传递信息和页面请求的最简单方法开始，即查询字符串。

##### 查询字符串

如果您不熟悉查询字符串，基本思想是在浏览器地址栏中出现的 URL 末尾添加一组变量。例如，如果您的网站上有一个 URL 为 http://www.yoursite.com/welcome.asp,的页面，那么您可以通过向该地址添加一个查询字符串来发送该页面我的名字和姓氏，如下所示:

```
http://www.yoursite.com/welcome.asp**?firstname=Kevin&lastname=Yank**
```

URL 中以粗体显示的部分是查询字符串。查询字符串总是以一个问号(`?`)开头，它标志着标准 URL 的结束。问号后面的查询字符串是一系列由&符号(`&`)分隔的一个或多个名称/值对。在这种情况下，我们有两个这样的对子。变量名`firstname`被赋予值`Kevin`，变量名`lastname`被赋予值`Yank`。

当像这样的 URL 用于加载 ASP 页面时，可以通过请求对象中的 QueryString 集合访问名称/值对。例如，您可以打印出随请求一起发送的查询字符串，如下所示:

```
Response.Write "Query string: " & Request.QueryString
```

对于上面的示例，输出如下所示:

<q>T2`Query string: firstname=Kevin&lastname=Yank`</q>

然而，当像这样传递多个值时，您通常希望能够分别检索这些值，这就是 QueryString 属性是一个集合这一事实派上用场的地方。例如，要检索并打印出上面查询字符串中定义的 firstname 变量的值，您可以使用以下代码:

```
Response.Write "First name: " & Request.QueryString("firstname")
```

上面一行指定我们只对查询字符串中定义的 firstname 变量的值感兴趣。此代码输出的行将如下所示:

<q>T2`First name: Kevin`</q>

查询字符串中的变量可以有多个值。考虑以下地址，它可能出现在提供货币转换器的网站上:

```
convert.asp?currency=CAD&currency=USD&currency=AUD
```

正如您所看到的，这个查询字符串包含三个名称/值对，这三个名称/值对为 currency 变量分配了不同的值。在我们假设的货币转换器站点上，这样的请求可能用于查询加拿大元、美元和澳大利亚元的当前汇率。收到这个请求后，如果 convert.asp 要打印出货币变量(`Request.QueryString("currency")`)的值，如我们上面演示的那样，它将产生以下输出:

<q>T2`CAD,USD,AUD`</q>

处理这个值的一个更有用的方法是一次考虑三个值中的每一个。这可以通过使用`QueryString("currency")`值的`Count`属性来完成，该属性包含提交的值的数量(在本例中为 3)。代码大概是这样的:

```
For i = 1 To Request.QueryString("currency").Count   

  currency = Request.QueryString("currency")(i)   

  ' Process currency...   

Next
```

在上面的代码中，`Request.QueryString("currency").Count`的值为 3，因此循环体将被处理三次。第一次通过循环时，循环变量`i`的值为 1，因此 currency 将被赋值为`Request.QueryString("currency")(1)`。这是指在查询字符串中分配给货币变量的三个值中的第一个，将产生`"CAD"`。类似地，循环的两次后续迭代将分别产生`"USD"`和`"AUD"`。

再举一个例子，假设你想在你的网站上增加个人风格，在每个页面上用名字问候你的访问者。下面是这样一个网站的基本页面的代码:

```
1  <% Option Explicit %>   

2  <%   

3     

4  If Request.QueryString("username").Count < 1 Then   

5    ' User has not given his/her name   

6    %>   

7    <html>   

8      <head>   

9        <title> Welcome! </title>   

10     </head>   

11     <body>   

12       <form action="<%=Request.ServerVariables("SCRIPT_NAME")%>"   

               method="GET">   

13         <p>Please enter your name:   

14           <input type="text" name="username">   

15           <input type="submit" value="OK"></p>   

16       </form>   

17     </body>   

18   </html>   

19   <%   

20   Response.End   

21 End If   

22    

23 Dim name   

24 name = Request.QueryString("username")   

25    

26 %>   

27    

28 <html>   

29   <head>   

30     <title> Typical Page </title>   

31   </head>   

32   <body>   

33     <p>Hi, <%=name%>!</p>   

34     <p>Click <a href="page2.asp?username=<%=Server.URLEncode(name)%>">   

          here</a> for more.</p>   

35   </body>   

36 </html>
```

由于这是迄今为止我们见过的最复杂的 ASP 脚本，并且因为其中有一些新的技巧，我将一次解释一行重要的内容:

```
4  If Request.QueryString("username").Count < 1 Then
```

该 If 语句的条件检查查询字符串中 username 变量的 Count 属性是否小于 1。只有在查询字符串中没有提交该变量的值时，才会出现这种情况，在这种情况下，计数将为 0。出现这种情况时，脚本将显示一个简单的表单，提示用户输入姓名，而不是显示页面的正常内容。

```
6    %>
```

虽然我们可以使用 Response。编写命令来输出要显示的表单的 HTML，一个更简单的选择是在 If 语句中切换出 ASP 模式。就像这个地方出现的语句一样，第 7 行到第 18 行的 HTML 代码只有在 if 语句的条件为真时才会显示。

```
12       <form action="<%=Request.ServerVariables("SCRIPT_NAME")%>"   

               method="GET">
```

尽管这一行看起来很复杂，但它实际上只是一个标准的 HTML `<form>`标签。对于那些不熟悉表单的人，我将解释一下`action`和`method`这两个属性的作用。

action 属性指定浏览器应该向其提交请求的脚本的 URL(以及表单中输入的信息)。在这种情况下，一旦用户输入了他或她的名字，我们希望将表单提交给生成它的同一个脚本，这样用户就可以看到他或她最初请求的页面。如果您简单地输入这个脚本的文件名，它会工作得很好，除了如果您曾经重命名过这个文件，您必须记得返回并更改这个属性的值。此外，如果每次使用时都要记住输入页面的文件名，这会使代码更难在站点的每个页面上重用。另一种方法是使用 ASP 打印出文件名，这正是上面的代码所做的。您应该认识到`<%=...%>`是在一段 HTML 中间打印出 ASP 值的快捷方式。在这个实例中输出的值是`Request.ServerVariables`集合的一个成员，该集合包含所有有用的值。此时，我们不会详细讨论这个系列；然而，我会解释我们使用的任何值。我们在这里使用的`"SCRIPT_NAME"`值总是包含被请求的 ASP 脚本的路径和文件名，就像它出现在 URL 中一样。因此，我们可以将它作为表单的 action 属性值打印出来，让浏览器将请求提交回同一个页面。

method 属性允许您为表单指定两种提交值的方式之一。当设置为“GET”时，就像这里一样，输入到表单域中的值被转换为名称/值对，并附加到由表单的 action 属性指定的 URL 的末尾。简而言之，“GET”告诉浏览器将表单作为查询字符串提交。另一种方法是“POST”，告诉浏览器在 HTTP 请求的正文中提交值。基本上，这意味着这些值不会作为 URL 的一部分出现(对于提交信用卡号或密码等敏感信息很有用)，而是隐藏在请求中。我们稍后将看到如何检索以这种方式提交的值。

```
14           <input type="text" name="username">
```

该标记创建一个文本输入字段，当用户完成该字段并将其作为表单的一部分提交时，会在查询字符串中创建一个名为 username 的变量。

```
20   Response.End
```

正如我们之前看到的，这一行导致脚本停止处理，并将到目前为止生成的所有输出发送到浏览器。这使得用户无法看到页面的实际内容，直到他或她用自己的名字填写了表单。

```
23 Dim name   

24 name = Request.QueryString("username")
```

一旦我们确定查询字符串中提供了一个名称，我们就声明一个变量，并将查询字符串中的名称存储在其中。对于脚本的其余部分，我们可以继续将该值称为`Request.QueryString("username")`,但是使用`name`是一种更方便的方式，可以节省输入。

```
33     <p>Hi, <%=name%>!</p>
```

然后，我们可以使用该变量在页面顶部显示该人的姓名，作为个性化问候的一部分！剩下的工作就是确保该值被传递给站点上的其他页面，如果查询字符串中没有提供名称，这些页面可能在顶部有相同的代码块提示用户输入名称。为了确保用户不会在他或她查看的每个页面上都得到提示，您必须对 HTML 链接进行编码，使它们包含一个包含用户名的查询字符串。下面一行包含这样一个链接:

```
34     <p>Click <a href="page2.asp?username=<%=Server.URLEncode(name)%>">   

          here</a> for more.</p>
```

您可能期望链接是`"page2.asp?username=<%=name%>"`；然而，由于适用于 URL 的规则，您不能在查询字符串中输入任何值。例如，空格是不允许的；大多数非字母数字字符也不是。要在查询字符串变量的值中包含这样的字符，必须对它们进行编码。例如，空格作为加号(`+`)插入。其他字符以百分号形式插入，后跟其 ASCII 字符代码。

ASP 提供了另一个名为 **Server** 的内置对象(类似于请求和响应),其中包含许多用于执行各种任务的有用方法。上面一行中使用的 **URLEncode** 方法获取一个文本字符串，并将其转换为查询字符串所需的编码。因此，要将 name 变量放在链接的查询字符串中，我们必须通过 URLEncode 方法传递它，如上所示。

这就是全部了！将文件保存为 ASP 脚本，并在您自己的服务器上试用。当您第一次加载页面时，会出现一个提示您输入姓名的表单。

![A form prompts the user to enter a name.](img/f2468f7080306d81ddf7000146145335.png)

一旦你输入了你的名字，如上所述，并点击“确定”，页面将重新加载并显示欢迎信息和一个简单的链接。

![The resulting page is personalized with the name entered.](img/c10d5e103be24db0f6a80dfa21d56a85.png)

请注意，URL 已更改为包含一个查询字符串，该字符串包含您在表单字段中输入的值。还要注意，我输入的值中的空格被浏览器自动编码为加号(`+`)。最后，将鼠标移到链接上，检查 URL 是否正确地生成了适当的查询字符串，以便将值带到站点的下一页。

##### 过帐变量

我在对上一个例子的解释中提到，表单可以使用两种方法之一:GET 或 POST。前者将值作为查询字符串的一部分提交，而后者在后台，在 HTTP 请求的主体中提交它们。除了我提到的安全优势，POST 变量还可能包含更多的信息(甚至是整个文件的内容！)比 URL 查询字符串允许的要多。然而，使用 POST 变量有两个缺点:

*   不能将使用 POST 方法的表单提交结果加入书签，因为书签不包含提交的变量及其值。GET 方法提交将所有内容存储在作为 URL 一部分的查询字符串中，从而成为书签的一部分。
*   POST 变量只能作为提交表单的结果创建，而查询字符串变量可以作为标准链接的一部分，或者可以由表单使用 GET 方法生成。通过一些巧妙的 JavaScript，您可以在页面上创建一个不可见的表单，并在用户点击一个标准链接时提交它，但这充其量是一个笨拙的解决方案。

另一方面，在 ASP 中使用 POST 变量与访问查询字符串变量非常相似。您只需使用 Form 集合而不是 QueryString 集合。因此，要打印输入到名为“testvalue”的表单中的值，可以使用下面一行代码:

```
Response.Write Request.Form("testvalue")
```

然而，在某些情况下，您可能希望能够访问一个变量的值，不管它是在查询字符串中提交的，还是作为表单的一部分、作为 cookie，甚至作为服务器变量(就像我们在前面的例子中使用的`"SCRIPT_NAME"`值)。Request 对象的一个方便的特性让您可以从这些源中访问一个变量，只需将 Request 对象视为其自身的一个集合(例如`Request("variableName")`)。这种简短的值检索方法非常方便。事实上，您可能希望在任何情况下都使用它，除非出于安全或其他原因，您希望确保变量是从特定来源发送的。

作为练习，重写前一节中的个性化欢迎消息示例，并尽可能使用这种简写符号(在脚本中有三个地方可以使用它)。

##### 进一步阅读的摘要和资源

在本文中，您了解了如何使用 ASP 来处理提交的值，这些值是通过普通链接中的查询字符串或通过要求用户提交表单来提供的。在这个过程中，你学习了 ASP 提供的一些重要的内置对象:`Request`、`Response`和`Server`。虽然我们没有时间来全面探究这些对象提供的所有方法、属性和集合，但是您现在应该已经了解了足够多的知识，可以使用在线文档来了解其他可用的内容。

在本系列的下一篇文章中，我们将了解另外两个重要的内置对象:`Application`和`Session`，它们不仅允许您构建动态页面，还允许您构建完整的 Web 应用程序！如果你急着开始，微软 IIS 或 PWS 附带的在线 ASP 文档是很好的起点；然而，缺乏经验的读者可能更喜欢投资一本好书。WROX 出版社为不同层次的体验提供了几本书；看看[我对它们的评论](http://www.webmasterbase.com/article.php/419)，找出哪一个最适合你！

## 分享这篇文章