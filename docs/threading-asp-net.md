# ASP.NET 的穿线

> 原文：<https://www.sitepoint.com/threading-asp-net/>

本教程试图用 ASP.NET 来阐明线程化的主题。线程是一种技术，用于给用户一种多个任务同时执行的感觉。的。NET Framework 为我们提供了许多类型，允许我们轻松快速地将多线程添加到我们的。NET Web 应用程序。我假设您对 ASP.NET 有所了解，并且至少有一些用 Visual Basic 编写代码的经验。

通过本教程，我们将构建一个非常简单的 Web 应用程序，它应该是一些基本线程概念的一个很好的例子。基本上，我们将有 2 个文本框，在其中输入一个短语，并输入一个时间段，在此期间，您希望该短语被重建。我们的 Web 线程应用程序由两个页面组成，其中一个是启动线程的页面，另一个是显示线程进度的结果页面。所提供的代码应该是非常可移植的，并允许您非常快速地实现自己的线程应用程序。

***在我们开始之前……***

在我们深入 Web 应用程序之前，让我先快速浏览一下您将看到的一些代码。

首先，我们需要导入系统。线程命名空间，这样我们就可以访问。NET 框架。将这一行添加到。aspx/。ascx 文件:

```
<%@ Import NameSpace="System.Threading" %> 
```

或者在 VB 中。网络:

```
Imports System.Threading
```

出于演示的目的，这里有一个长时间运行的方法示例。在现实生活中，这个方法最有可能执行一个任务，比如处理一个 Web 表单，或者完成一个耗时的数据库查询。

```
Public Sub SomeLongMethod() 

        'your code that takes a while to execute 

End Sub
```

现在，为了执行这个方法而不使我们的 Web 表单挂起，我们将启动一个新线程，让`SomeLongMethod`在这个新线程上执行。为此，我们有几个选择。我将使用的技术是建立一个新的方法来启动我们的新线程运行。下面是一个示例线程启动函数:

```
Public Sub SomeLongMethod_Thread() 

'first, declare a new Thread, passing the constructor the address 

'of SomeLongMethod. NOTE: SomeLongMethod can be replaced with your 

'own method 

Dim NewThread As Thread = New _ 

            Thread(AddressOf SomeLongMethod) 

'next we set the priority of our thread to lowest, setting 

'the priority to a higher level can cause unexpected results. 

NewThread.Priority = ThreadPriority.Lowest 

'finally we start the thread executing 

NewThread.Start() 

End Sub
```

就是这样！我们现在要做的就是用对`SomeLongMethod_Thread`的调用替换对 SomeLongMethod 的调用，long 方法将在自己的线程上执行。通常，我们会在`SomeLongMethod_Thread`方法结束时将用户重定向到结果页面。然而，在本例中，为了避免混淆，我省略了这一点——我将在下面的例子中演示它，这个例子说明了线程在 ASP.NET Web 应用程序中的使用。

##### 使用线程重建字符串

我们要查看的第一个文件是 default.aspx。这将是我们从用户那里获取 2 个值并启动一个新线程的页面。我们要查看的第二个文件是结果页面，在这里我们将轮询线程中创建的会话变量，并向用户显示当前的线程统计数据。我将逐个方法地检查 default.aspx，然后对 results.aspx 页面进行同样的操作。这个文件的源代码可以在本教程的末尾找到。

注意:我假设您的 Web 服务器启用了会话变量。如果禁用了会话变量，或者禁用了浏览器上的 cookies，以下示例的结果将不会正确显示。

***(default . aspx)*****简单的例子**

 **让我们从查看 default.aspx 的`Page_Load` 函数开始

```
Sub Page_Load(ByVal sender As System.Object, ByVal e   

As System.EventArgs)  

SyncLock Session.SyncRoot  

'here, we initialize 3 session variables to hold our results  

Session("Complete") = False  

Session("Status") = ""  

Session("Phrase") = ""  

End SyncLock  

d Sub
```

在这个方法中，我们简单地初始化 3 个会话变量，我们将在接下来的几个方法中使用它们。`Session("Complete")`将成为我们结果页面的哨兵。当线程完成时，我们将把`Session("Complete")`设置为`True`。`Session("Phrase")` 变量将是我们在慢慢构建时用来保存部分短语的变量。`Session("Status")`只是一个保存开始时间和结束时间的变量。现在让我们看看我们的短语重组方法:

```
Sub PhraseBuilder()  

        Dim str As String = ""  

        Dim i As Integer = 0  

        Dim startTimeTicks As Long = 0  

        Dim strStartTime As String = ""  

        Dim totalSleepTime As Double = 0.0  

        'log our start time, in ticks, and in Long Date format  

        startTimeTicks = DateTime.Now.Ticks  

        strStartTime = "Thread Started: " & DateTime.Now  

        ' get phrase  

        str = txtPhrase.Text  

        'convert users time from seconds to milliseconds  

        totalSleepTime = 1000.0  

        totalSleepTime = totalSleepTime * CInt(txtTotalThreadLife.Text)  

        totalSleepTime = (totalSleepTime / str.Length)  

        For i = 0 To str.Length - 1  

        'this method will put our thread to sleep for the specified  

        'number of milliseconds. without the sleep, this method would   

        'execute too fast to see the thread working.  

        Thread.Sleep(totalSleepTime)  

  'we use synclock to block any other thread from accessing  

  'session variables while we are changing their values.  

        SyncLock Session.SyncRoot  

  Session("Status") = "Thread is " & _  

  Format((i / (str.Length - 1)) * 100, "#0.00") & _  

        "% complete." & " - Time Elapsed: " & _  

        Format((DateTime.Now.Ticks - startTimeTicks) / 10000000 _  

        , "#0.00") & " sec. Target: " & txtTotalThreadLife.Text & _  

        ".00 sec."  

        End SyncLock  

        SyncLock Session.SyncRoot  

  'rebuild phrase 1 letter at a time  

        Session("Phrase") &= str.Chars(i).ToString  

        End SyncLock  

        Next  

  'our method is complete, so set the Session variables  

  'accordingly  

        SyncLock Session.SyncRoot  

        Session("Status") = strStartTime & _  

  "<br>Thread Complete. End Time: " & DateTime.Now & "<br>"  

        Session("Complete") = True  

        End SyncLock  

    End Sub
```

好了，现在我们来稍微解剖一下这个方法。基本上，我们在这里所做的是强制一个本来可以快速运行的方法按照基于用户输入的时间表运行。这是使用`Thread.Sleep(ByVal millisecond as Integer)`方法完成的。这个方法允许我们让线程休眠指定的毫秒数。这个 `Sleep`方法可以在任何方法中使用，而不仅仅是在新线程上执行的方法。

我们利用的另一个有趣的技术是使用`Synclock`方法。Synclock 用于阻止其他线程试图获得相同的`Synclock`。为了保护变量不被同时访问，我们需要在访问代码中其他地方的变量之前获得相同的 Synclock。这在多线程 Web 应用程序中是必要的，以确保两个方法不会同时读/写同一个变量。`Synclock`方法与使用`Monitor.Enter(Me)`方法相同，后者也在`System.Threading Namespace`中提供。

只剩下两种方法了！我们要看的下一个方法是`PhraseBuilder_Thread` 函数。该函数几乎与本文开头的示例相同:

```
Sub PhraseBuilder_Thread()   

        'method to start our phrase builder method executing   

        'on a new thread.   

        Dim myThread As Thread = New Thread(AddressOf PhraseBuilder)   

        myThread.Priority = ThreadPriority.Lowest   

        '//start the new thread   

        myThread.Start()   

        'now redirect to the results page   

        Response.Redirect("results.aspx")   

End Sub
```

现在剩下的就是当用户点击提交按钮时调用我们的`PhraseBuilder_Thread` 方法。下面是简短的代码:

```
Sub btnSubmit_Click(ByVal sender As System.Object, ByVal e    

As System.EventArgs)   

'start PhraseBuilder thread...   

PhraseBuilder_Thread()   

End Sub
```

**(results . aspx)简单的例子**

 **现在我们来看看结果页面。基本上，我们的结果页面将检查`Page_Load`上的 `Session("completed")` 变量的状态，并做出相应的反应。下面是 results.aspx 的`Page_load` 函数:

```
Sub Page_Load(ByVal sender As System.Object, ByVal e As System.EventArgs)   

'Put user code to initialize the page here   

'check the value of Session("Completed"), if it is True, stop writing   

If Session("Complete") <> True Then   

   'make sure session variables are enabled, if not warn user   

   If Session("Complete") <> False Then   

    'error with session variable, Session("Complete") is not   

    'True or False   

    lblComplete.Text = "Error with Session('Complete')"   

    Else   

'set page to auto refresh page every 2 seconds, until thread is done   

    Response.Write("<META HTTP-EQUIV=Refresh CONTENT='2; URL='>")   

SyncLock Session.SyncRoot   

    lblStatus.Text = Session("Status") & "<br>Processing. . ."   

    lblPhrase.Text = Session("Phrase")   

    lblComplete.Text = Session("Complete")   

End SyncLock   

    End If   

Else   

'thread is complete, stop writing refresh tag, and display   

'results   

SyncLock Session.SyncRoot   

    lblStatus.Text = Session("Status")   

    lblPhrase.Text = Session("Phrase")   

    lblComplete.Text = Session("Complete")   

End SyncLock   

End If   

End Sub
```

此`Page_Load`功能检查`Session("Complete")` 变量的状态并做出相应反应。随意定制你的结果页面来满足你的需要，但是我建议检查一下 `Session("Completed")` 变量没有设置为真或假的情况。这通常发生在会话变量被禁用或者 cookies 被禁用的时候。另外，如果不希望页面自动刷新，可以删除`Response.Write` 语句。

##### 最后

嗯，这就是全部了！希望您已经使用线程编写了第一个 ASP.NET 应用程序。

在应用程序中正确地使用线程可以极大地提高 Web 服务器的性能，并为用户提供更好的 Web 体验。我希望你喜欢这个教程！欲了解更多信息，请访问:

*   [网上有用的线程教程](http://www.123aspx.com/directory.aspx?dir=66)
*   在可视化 Basic.NET 中实现后台线程
*   [通过多线程提升网络性能](http://www.fawcette.com/vsm/2002_11/magazine/features/chester/default_pf.asp)** 

## **分享这篇文章****