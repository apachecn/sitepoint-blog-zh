# 无需访问 CF Administrator 即可安排任务

> 原文：<https://www.sitepoint.com/scheduling-tasks-without-access-to-cf-administrator/>

*…发帖人* [大卫·梅德洛克](https://www.sitepoint.com/forums/member.php?u=24385) *:*

这是我在调查我们的读者时要求的，我想知道我们的读者想看什么样的文章。

如果您在基于 Unix 的服务器上，您通常可以通过 telnet 访问您的服务器，并设置 cron 作业来为您自动执行流程。例如，您可能希望创建一个邮件队列，从数据库或目录中检索要发送的电子邮件列表，然后发送这些电子邮件。如果你在邮件列表或时事通讯应用程序中发送大量邮件，这是必不可少的。

在这种情况下，您可能希望每晚、每 30 分钟或以其他时间间隔发送电子邮件。你不想必须登录到你的系统，然后按下一个按钮。因此，您需要一个预定的任务来为您执行这项工作。

这样做的问题是，我们大多数人从共享主机上运行我们的网站，在那里我们不容易访问 ColdFusion Administrator，在那里我们通常会安排任务自动执行。

不要害怕！您可以使用一个叫做 CFSCHEDULE 的漂亮的小标签来实现这一点。它使用起来也非常简单。基本格式如下:

 `<cfschedule action="update" task="MyScheduledTask" interval="[Daily|Weekly|Monthly|Interval Value in Seconds]" startdate="#DateFormat(Now(), " mm="" dd="" yyyy="" starttime="#TimeFormat(Now(), " hh:mm:ss="" tt="" operation="HTTPRequest">url="http://www.example.com"</cfschedule>` 

这段代码创建任务“MyScheduledTask ”(如果它不存在),如果存在就更新它。任务属性指定任务的名称，时间间隔是任务运行的频率。(您还可以指定结束日期和结束时间，以便它在特定日期和时间后停止运行。)operation 属性指定这是对另一个 URL 的 HTTP 请求。然后，您指定想要使用的 URL。

向 example.com 发送一个 HTTP 请求实际上不会做任何事情。不过，我们可以选择通过指定 publish、path 和 file 属性的值来保存 HTTP 请求的结果。(显然，在 path 属性中指定不带文件名的完整路径，在 file 属性中指定文件名。)确保 publish 等于“Yes”。然后，在任务运行之后，任务的结果将被写入该文件。

您还可以选择删除和运行任务。如果您想要使用其中一个，请在操作中指定“删除”或“运行”,并在“任务”属性中指定任务的名称。

这个标签还有一些其他选项，可以在 [LiveDocs](http://livedocs.macromedia.com/coldfusion/6/CFML_Reference/Tags-pt229.htm) 中查看。你可以从这个标签中做任何你可以在管理员中做的事情。(如果您没有访问管理员的权限，您可以将此标记与数据库结合使用，并构建自己的任务管理员。)

请注意，有些主机不允许调度任务，我*认为*有些甚至完全禁用了 CFSCHEDULE 标签，所以在尝试使用它之前，请与您的主机核实一下。

## 分享这篇文章