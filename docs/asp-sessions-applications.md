# ASP 会话和应用程序

> 原文：<https://www.sitepoint.com/asp-sessions-applications/>

ASP 之类的服务器端脚本语言最强大的一面是它们几乎神奇的能力，可以将 Web 一种原本是为一个又一个静态文档服务的媒介——转变为一个提供丰富的交互式体验的平台。这些体验以在线应用程序的形式出现，在不同的情况下对不同的用户有不同的响应。

为了使这种转换成为可能，ASP 不仅要能够响应请求动态生成网页。它还必须能够跟踪特定于一组相关 ASP 脚本(如在线论坛)的**用户**的信息，并允许这些脚本**彼此共享信息**。

在这篇 SitePoint 关于 ASP 系列的第四篇文章中，我将描述 ASP 的特性，这些特性使得上述成为可能:**应用程序**和**会话**。首先，我将带您完成在 Microsoft Internet Information Server(IIS)中创建 Web 应用程序所需的步骤，我将解释应用程序和会话如何满足对**持久数据**的需求。然后，我将向您介绍 ASP 中的`Application`和`Session`对象，它们是魔术的核心，并解释`global.asa`文件的作用。最后，我将带您浏览一个完整示例的代码，该示例处理在任何给定时间跟踪站点上的用户数量。

本文假设您熟悉本系列前几篇文章中涉及的概念，从[开始学习 ASP](http://www.webmasterbase.com/article.php/371) 。如果你还没有读过它们，并且你是第一次接触 ASP，我建议你在开始这篇文章之前至少浏览一下以前的文章。

开始了。

##### IIS 中的 Web 应用程序

Web 应用程序与您在计算机上运行的任何其他软件应用程序非常相似，只是界面是由一组网页提供的，并且应用程序的代码主要在您从中获取这些网页的 Web 服务器上运行。如今常见的 Web 应用程序包括论坛、书店和自定义新闻提要。

然而，从一个 ASP 开发者的角度来看，一个 Web 应用程序只是一个 ASP 文件的集合，这些文件一起工作来提供这种服务。这组 ASP 文件通常存储在一个目录中，也可能存储在多个子目录中。利用 ASP 提供的特性来支持 Web 应用程序开发的第一步是识别 IIS 的“应用程序根目录”。

我现在假设您对用于 ASP 开发的 IIS 服务器拥有管理员级别的访问权限。在这种情况下，您的第一个任务是在服务器上定义一个 Web 应用程序。在服务器上的任意位置创建一个目录(我选择了`c:MyApplication`)，您将在本文的示例中使用这个目录。打开控制面板、管理工具和 Internet 信息服务，然后导航到默认网站。右键单击它并选择新建，虚拟目录。当向导提示时，键入将出现在 URL 中的目录的别名(例如，别名为`myapp`的虚拟目录在您的计算机上可以作为`http://localhost/myapp/`访问，在网络上的其他计算机上可以作为`http://your.host.name/myapp/`访问)，然后浏览到您刚刚创建的目录。对于访问权限，保持默认选项(读取和运行脚本)处于选中状态。完成向导后，新的虚拟目录应该会出现在树视图中，如下所示:

![The myapp Web Application](img/1c960b915e38a1c86e5cc38a96b1c220.png)

现在，您已经在服务器上定义了一个 Web 应用程序。该目录(或其子目录)中包含的所有 ASP 文件都将被视为`myapp`应用程序的一部分(或您选择的任何名称)。在我们进一步探索利用这一点的 ASP 特性之前，让我们停下来看看我们需要一个 Web 应用程序来做什么。

##### 对持久数据的需求

正如我们在本系列的前几篇文章中看到的，您可以将值存储在 ASP **变量**中，并将它们显示为动态网页的一部分。然而，变量的一个限制是，它们只在创建它们的特定 ASP 脚本运行期间存在。一旦 ASP 脚本结束，并且最后一个网页被发送到发出请求的浏览器，在脚本执行过程中创建的所有变量都被销毁以释放内存，从而能够处理后面的页面请求。

在许多情况下，在一个 ASP 脚本结束时将一个变量留在内存中是很方便的，这样存储在其中的值就可以被另一个脚本获取和使用。在[的上一篇文章](http://www.webmasterbase.com/article.php/420)中，我们看到了如何将信息从一个页面传递到另一个页面，要么通过表单提交，要么通过 URL 查询字符串；然而，这些方法并不总是足够的。

例如，以我们在那篇文章中看到的例子为例。在这里，我们用一个 HTML 表单提示用户输入他或她的名字，这个表单出现在用户进入站点时请求的第一个页面上。然后，我们用包含用户输入的名称的查询字符串将该名称传递给每个附加页面。想象一下在 SitePoint.com 这样大的地方将这个例子付诸实践的意义。每个页面中的每个链接都必须单独编码，才能将用户名变量传递到下一个页面。即使在给定页面上不需要显示任何动态数据，也不能使用普通的 HTML 页面，因为 HTML 页面不能将用户名传递给后续页面。

正如您所看到的，当您的目标是创建一个可以在整个站点中使用的变量时，将它从一个页面传递到下一个页面很少是一个实用的解决方案。幸运的是，ASP Web 应用程序在设计时就考虑到了这一点。您可能还记得我们上一篇文章中提到 ASP 有许多内置对象——`Request`和`Response`就是最好的例子。所有 ASP 脚本都可以访问的另外两个对象是`Application`和`Session`，正是这些特殊的对象让我们可以存储当前 Web 应用程序中所有 ASP 脚本都可以访问的数据。

##### 应用程序对象

尽管每个页面请求都有自己的`Request`和`Response`对象，但是 Web 应用程序中所有的 ASP 页面请求都共享同一个应用程序对象。该对象是在 Web 服务器启动后第一次从应用程序请求 ASP 页时创建的，并在 Web 服务器关闭或在 IIS 管理控制台中手动卸载 Web 应用程序时被销毁。因为该对象从一个页面请求持续到另一个页面请求，所以它可用于存储要与应用程序中所有其他页面共享的数据。

要在 application 对象中存储值，只需使用以下语法:

```
Application("varName") = value
```

然后，您可以像访问常规变量一样访问该存储值，除了作为`Application("varName")`。Application 对象的一个简单用途是用于计数器。您可以将您的网站获得的点击数记录为`Application("hits")`，并随着每个页面请求增加该值。下面是相关的基本代码:

```
1  <% Option Explicit %>  

2  <html>  

3  <head>  

4  <title> Simple counter </title>  

5  </head>  

6  <body>  

7  <p>Page requests: <%  

8    Application("hits") = Application("hits") + 1  

9    Response.Write(Application("hits"))  

10 %></p>  

11 </body>  

12 </html>
```

第 8 行给`Application("hits")`的值加 1，然后第 9 行打印出更新后的值。将上述代码保存在您的应用程序目录下的一个 ASP 文件中(如`counter.asp`)，然后反复加载到您的浏览器中(如`http://localhost/myapp/counter.asp`)。注意，随着每个页面请求，计数增加 1。通过在站点的每个页面上包含这个代码片段，您可以计算页面请求的总数，因为所有页面都将共享相同的`Application("hits")`值。

虽然上面的值存储和检索方法代表了您在 90%的情况下使用`Application`对象的方式，但是这个对象还有一些您应该知道的方法。

首先，`Application("hits")`其实只是一个快捷方式。hits 变量实际上是一个名为`Contents`的集合的成员，这个集合是`Applications`的一个属性。因此，手写版本为`Application.Contents("hits")`。当访问存储值时，您没有理由使用这个更长的版本，但是当您从应用程序中删除存储值时，您必须使用它。为此，调用`Contents`集合的`Remove`方法，如下所示:

```
Application.Contents.Remove("hits")
```

这段代码将从应用程序中删除`"hits"`存储值。要从应用程序中删除所有存储的值，可以使用`RemoveAll`方法:

```
Application.Contents.RemoveAll
```

为了理解`Application`对象的其余两个方法`Lock`和`Unlock`的用途，我们必须首先仔细看看上面点击计数器示例中的第 8 行:

```
8    Application("hits") = Application("hits") + 1
```

如您所见，这一行实际上访问了存储的应用程序值两次。第一次(在等号的右边)，它检索存储的值。然后，在将检索到的值加 1 后，它第二次访问存储的值(在等号的左边),在现有值的顶部记录更新后的值。

重要的是要理解，上述三个步骤并不一定是一系列不间断的事件。如果两个 Web 浏览器同时访问您的站点，每个浏览器的事件可能会混合在一起，产生如下序列:

*   browser 1 reads "hit"*   browser 2 reads "hit"*   browser 1 stores "hit + 1"*   browser 2 stores "hit + 1"

    如果您仔细观察这个序列，您会发现应该记录的两次点击只导致存储的“点击”值增加 1！为了避免这种情况发生，您的代码应该“锁定”Application 对象，然后再进行一系列这样的事件，这些事件应该以不间断的顺序发生。因此，上面的第 8 行应该替换为这三行:

    ```
    8    Application.Lock  

    9    Application("hits") = Application("hits") + 1  

    10   Application.Unlock
    ```

    在锁定对对象的访问之前，对第 8 行上的`Application.Lock`的调用等待任何其他进程完成对`Application`对象的访问。这迫使其他客户端等到第 10 行调用`Application.Unlock`之后，才能再次读取或写入存储的值。这有效地防止了类似上述混淆的发生，因为“浏览器 2”必须等待“浏览器 1”读取并存储其更新后的值，然后才能读取该值并自行更新。

    ##### 会话对象

    `Session`对象与`Application`对象非常相似，因为它允许您存储站点所有页面之间共享的值。这两者的主要区别在于，访问你的站点的所有页面和所有客户端共享一个`Application`对象，每个客户端(浏览器)被分配一个自己的`Session`对象。

    因此，必须为网站上发生的每个用户会话创建一个`Session`对象。下面是我们在上一节中看到的点击计数器示例的更新版本:

    ```
    1  <% Option Explicit %>   

    2  <html>   

    3  <head>   

    4  <title> Simple counter </title>   

    5  </head>   

    6  <body>   

    7  <p>Your page requests: <%   

    8    Session("hits") = Session("hits") + 1   

    9    Response.Write(Session("hits"))   

    10 %></p>   

    11 <p>Total page requests: <%   

    12   Application.Lock   

    13   Application("hits") = Application("hits") + 1   

    14   Application.Unlock   

    15   Response.Write(Application("hits"))   

    16 %></p>   

    17 </body>   

    18 </html>
    ```

    保存此页面并打开 Web 浏览器的两个副本。实际启动浏览器应用程序两次很重要——打开一次然后打开一个新窗口会使两个窗口成为同一个会话的一部分，而实际打开两个不同的浏览器副本会导致它们在服务器上被分配不同的会话对象。将上面的页面加载到两个浏览器中，并在每个浏览器中刷新几次。注意到存储在`Session`对象中的计数器只计算每个浏览器的点击次数，而存储在`Application`对象中的计数器存储页面请求总数。

    `Session`对象的一个典型用途是在在线购物网站上存储用户的购物车。显然，让所有用户共享一个购物车是没有意义的，所以每个用户都有一个自己的`Session`对象正好符合这个要求。

    至于`Application`对象，`Session("hits")`其实只是`Session.Contents("hits")`的简称。`Session.Contents.Remove("hits")`可用于删除单个存储值，而`Session.Contents.RemoveAll`将删除当前会话中存储的所有值。

    每个连接到您站点的用户在访问期间都被分配了自己的`Session`对象，因此必须有某种机制来防止旧的`Session`对象增加并消耗您服务器上的所有内存。默认情况下，如果对应于该会话的客户端在 10 分钟内没有从您的服务器请求页面，那么一个`Session`对象将过期并从您的服务器中删除。在大多数情况下，这意味着用户已经关闭了浏览器或者转到了另一个网站。如果您的应用程序的典型“页面请求间隔时间”超过 10 分钟，这个默认超时值将阻止您的应用程序正常工作。要指定您自己的会话超时间隔，请设置 session 对象的 timeout 属性:

    ```
    Session.Timeout = 30  ' The current session will end after   

                          ' 30 minutes of inactivity
    ```

    您也可以立即结束当前会话，例如，如果您想从 Web 应用程序中“注销”当前用户:

    ```
    Session.Abandon
    ```

    ##### global.asa 文件

    ```
    global.asa is a special file that you can create in the root directory of your Web application. It specifies the actions to be taken in response to four events:
    ```

    *   应用程序启动`(Application_OnStart)`*   申请结束`(Application_OnEnd)`*   会话开始`(Session_OnStart)`*   Session `(Session_OnEnd)`

    ```
    Application_OnStart can be used to set up initial values for application variables, or perhaps to restore some saved information from the last time the Web server was run, by loading it from a file or database. Application_OnEnd, meanwhile, is generally used to clean up any open resources, and save information that will be needed the next time the Web application is started. Session_OnStart and Session_OnEnd perform similar functions for user sessions.让我们创建一个简单的`global.asa`文件来查看这些活动。打开记事本(或您选择的文本编辑器)并在存放您的 Web 应用程序的目录中创建一个名为`global.asa`的文件。键入以下内容作为该文件的内容:

    ```
    1  <script language="VBScript" runat="Server">    

    2  Sub Application_OnStart    

    3    ' Commands to be run at startup    

    4  End Sub    

    5    

    6  Sub Application_OnEnd    

    7    ' Commands to be run at shutdown    

    8  End Sub    

    9    

    10 Sub Session_OnStart    

    11   ' Commands to be run at session start    

    12 End Sub    

    13    

    14 Sub Session_OnEnd    

    15   ' Commands to be run at session end    

    16 End Sub    

    17 </script>
    ```

      This is the basic structure of a `global.asa` file. Let's say, just for fun, we want to start counting our global `(Application)` click counter at 1000\. The code of `/Application_OnStart` (lines 2-4) may be as follows:  

    ```
    2  Sub Application_OnStart    

    3    Application("hits") = 1000  ' Pad the numbers :)    

    4  End Sub
    ```

      In a more practical example, you can use `Application_OnEnd` to save the current number of clicks to a text file when the server is shut down, and use `Application_OnStart` to load the value when the server is started again.   

    #####  Put it all together 

     To demonstrate the simultaneous use of `Application, Session,` and `global.asa` in a system, let's create a more practical and interesting version of the example we saw last time. In this example, users are prompted to enter their names, and then a personalized welcome message appears at the top of each page they visit. This time, we will use the `Session` object to track each user's name, instead of having to code every link on our site to pass variables to the next page. We will also use the `Application` object to track the number of users on our site (and how many of them gave us their names). 

     First, create a new `global.asa` file with the following contents: 

    ```
    1  <script language="VBScript" runat="Server">     

    2  Sub Application_OnStart     

    3    Application("users") = 0     

    4    Application("namedUsers") = 0     

    5  End Sub
    ```

     When our application is started, we set two variables (the first variable tracks the number of users, while the second variable tracks the logged-in users by providing user names [naming users]) to zero. 

    ```
    6  Sub Session_OnStart     

    7    Session.Timeout = 5 ' minutes     

    8    Session("name") = ""     

    9    Application.Lock     

    10   Application("users") = Application("users") + 1     

    11   Application.Unlock     

    12 End Sub
    ```

     When a new user connects to our site (starts a new session), we set the `Session.Timeout` value to 5 (minutes)-the session will end after 5 minutes of inactivity (instead of the usual 10 minutes). Then we create a variable to store the user's name (which he or she may eventually provide) and set it to ""(empty string), which means that the user has not been named yet. Then, we will increase the number of users by 1, and perform `Lock` on `Application` object at the same time. 

    ```
    13 Sub Session_OnEnd     

    14   Application.Lock     

    15   Application("users") = Application("users") - 1     

    16   If Len(Session("name")) > 0 Then     

    17     Application("namedUsers") = Application("namedUsers") - 1     

    18   End If     

    19   Application.Unlock     

    20 End Sub     

    21 </script>
    ```

     When users leave the site, and their session times out after the necessary 5 minutes of inactivity, we `Lock` this `Application` again to prepare for the changes we will make, and decrease the user count by 1\. Next, we check the length of the string stored in `Session("name")` with the help of the built-in `Len()` function. If the length of the string exceeds zero characters (i.e. ""), we know that the user has logged in by providing the name, so we also decrease the `namedUsers` count by 1\. 

     Now we need to create a sample page on our website. Name the file `default.asp` and type the following code: 

    ```
    1  <% Option Explicit %>     

    2  <!-- #include FILE="login.asp" -->     

    3  <html>     

    4  <head>     

    5  <title>My Sample Application</title>     

    6  </head>     

    7  <body bgcolor="#FFFFFF" text="#000000">     

    8  <p><!-- #include FILE="welcome.asp" --></p>     

    9  </body>     

    10 </html>
    ```

     The magic lies in the `<!-- #include ... -->` label. As a lot of codes will be involved in the process of logging in and displaying user records, and this may be something we want to handle on every page of the site, we will put the codes of these functions in separate files (`login.asp` and `welcome.asp`). This means that we only need to enter the code once, so that all pages on our website can share the same code. This `<!-- #include ... -->` tag basically inserts the contents of the specified file in the tag position before the ASP code in the file is processed. 

     Obviously, our next task is to create `login.asp` and `welcome.asp` files. Let's start with `welcome.asp`: 

    ```
    1  <%     

    2  ' Displays welcome message or login prompt     

    3  If Len(Session("name")) > 0 Then     

    4    Response.Write("Logged in as: " + Session("name"))     

    5  Else     

    6    Response.Write("Not logged in. <a href=""")     

    7    Response.Write(Request.ServerVariables("SCRIPT_NAME"))     

    8    Response.Write("?login=1"">Log in</a>")     

    9  End If
    ```

     We first check the length of the `Session("name")` variable to see if the user has logged in. If yes, we will display the message/"Login as: *name* ". Otherwise, we will display "Not logged in" followed by a link for users to log in. The code of this link seems a bit complicated at first glance, but please bear with me to explain. 

     First, we print out the beginning of the link label `(<a href=")`. Because we have used quotation marks to tell `Response.Write` where the string to be sent to the browser starts and ends, we use double quotation marks ("") for the quotation marks appearing in this code. As usual, the third quotation mark marks the end of the string. Next, we output the `"SCRIPT_NAME"` server variable. As we have seen before, this will make the link point to the same page that is currently being processed. Finally, we add `?login=1` to the end of the URL to indicate the user's intention to log in (`login.asp` will monitor this, which we will see later). Next, we print out the user count (and how many users are logged in) by simply outputting the appropriate `Application` variable. 

    ```
    10  ' Display tally of users     

    11 Response.Write("<br>" & Application("users") & " users online")     

    12 Response.Write(" (" & Application("namedUsers") & " logged in)")     

    13 %>
    ```

     This is the whole of `welcome.asp`; Here is `login.asp`: 

    ```
    1  <%     

    2  ' User wishes to log in     

    3  If Request.QueryString("login").Count > 0 Then     

    4    %>     

    5    <html>     

    6    <head>     

    7      <title> Log In </title>     

    8    </head>     

    9    <body>     

    10   <form action="<%=Request.ServerVariables("SCRIPT_NAME")%>"     

    11         method="POST">     

    12     <p>Enter your name:     

    13     <input type="text" name="name">     

    14     <input type="submit" value="OK"></p>     

    15   </form>     

    16   </body>     

    17   </html>     

    18   <%     

    19   Response.End     

    20 End If
    ```

     First, we capture the page request generated by the user clicking the "login" link generated by `welcome.asp` above. As we saw in the last article, we found this by checking whether `Request.QueryString("login").Count` is greater than zero. When such a request is found, we display a simple page to prompt the user to enter his or her name. The form is submitted to `Request.ServerVariables("SCRIPT_NAME")`, so our next task in this script is to handle the submission of the form, which will be marked as `Request.Form("name").Count` greater than zero: 

    ```
    21 ' User is logging in     

    22 If Request.Form("name").Count > 0 Then     

    23   Session("name") = Request.Form("name")     

    24   Application.Lock     

    25   Application("namedUsers") = Application("namedUsers") + 1     

    26   Application.Unlock     

    27   Response.Redirect(Request.ServerVariables("SCRIPT_NAME"))     

    28   Response.End     

    29 End If     

    42 %>
    ```

     This code starts with the submitted name in the form (`Request.Form("name")`) and stores it in `Session("name")`. Then we `Lock` `Application` and increased the number of designated users by 1\. Finally, we redirect the user to the originally requested page. 

     After all these files are prepared, load `default.asp` (for example, `http://localhost/myapp/default.asp`) in several instances of the browser, and you should see the system running! 

     ![Three Sessions, One Application](img/ac4221c564049bee97a9eef05581629e.png) 

    #####  Abstract 

     In this paper, we discuss the main mechanisms that ASP provides to support persistent data. Using the `Application` object, we can store data values shared by all pages and all users on the site, such as the simple click counter we see. At the same time, session objects provide the ability to associate persistent data with each active user on our site. In the last example, we use it to store each user's name and display it at the top of each page. 

     In public network forums, this stored value can make the user not have to type his or her name to post a message. Combined with the database that stores authorized users and their password lists, the system we developed in this paper can be extended to an access control system. 

     In the next article in this series, we will study how ASP interacts with relational databases (such as Microsoft Access). This very powerful combination of ASP and back-end database is the driving force of many of the hottest websites on the Internet today. 

    ```

## 分享这篇文章