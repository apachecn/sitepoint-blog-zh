# ASP.NET MVC 4 开发者预览版:新功能

> 原文：<https://www.sitepoint.com/asp-net-mvc-4-developer-preview-whats-new/>

微软正在加快其一些产品的发布周期，ASP.NET MVC 框架更新的惊人速度就是证明。

最新版本，MVC 4 开发者预览版，在它的军械库中加入了一些很酷的新东西。在接下来的几周里，我会看看这个框架的一些新特性，以及如何在你的网站中使用它们。

添加到框架中的更引人注目的特性包括:

*   移动项目模板
*   显示模式
*   方法
*   异步控制器的任务支持
*   Azure SDK
*   错误修复

### 装置

在进行任何开发之前，您需要安装 MVC 4 版本。最简单的方法是通过 Web 平台安装程序。MVC 4 可用于 Visual Studio 2010 或 Visual Studio 2011 开发人员预览版。我创作的所有 MVC 文章都是在 Visual Studio 2011 开发者预览版中开发的。以下是开始使用的链接。

*   [用于 Visual Studio 2010 的 ASP.NET MVC 4](http://www.microsoft.com/web/gallery/install.aspx?appid=MVC4VS2010&prerelease=true)
*   [ASP.NET MVC 4 for Visual Studio 2011 开发者预览版](http://www.microsoft.com/web/gallery/install.aspx?appid=MVC4VS11&prerelease=true)

一旦你安装好了所有的东西，乐趣才真正开始！

### 异步控制器的任务支持

我今天要关注的特性是异步控制器的任务支持。

没有人喜欢等待，那么你的用户为什么要等待一个长时间运行的异步任务呢？没道理啊！

从 MVC 3 开始就可以开发异步控制器，但是为了让它工作，你必须写一堆额外的代码——我喜欢称之为*代码噪声—*。

以将 Twitter 整合到网页中为例。在 MVC 3 中，代码需要遵循特定的规则。不是只有一种行动方法，而是必须有两种行动方法。两者的名称相同，但是对于开始异步请求的方法，您需要将 *Async* 附加到动作名称上。对于处理异步请求结束的方法，您需要将 *Completed* 添加到动作名称中。

如果你看到一些代码，就更容易理解了。下面的示例代码从 Twitter 异步请求数据。

```
public void SearchTwitterAsync()
{
        const string url = "http://search.twitter.com/search.atom?q=guycode&rpp=100&result_type=mixed";

        // the asynchronous operation is declared
        AsyncManager.OutstandingOperations.Increment();

        var webClient = new WebClient();
        webClient.DownloadStringCompleted += (sender, e) =>
        {
              AsyncManager.Parameters["results"] = e.Result;
              AsyncManager.OutstandingOperations.Decrement();
        };
        webClient.DownloadStringAsync(new Uri(url)); //the asynchronous process is launched
}

public ActionResult SearchTwitterCompleted(string results)
{
        // Now return the twitter results to the client
        return Json(ReadTwitterResults(results), JsonRequestBehavior.AllowGet);
}
```

上面的代码发送到 Twitter，搜索数据并异步返回结果。这里有很多代码噪音，对我来说，这违反了——因为没有更好的词——不要重复自己(干)的原则。

嗯，在 MVC 4 中，这些任务被合并成了一个。现在，您可以将异步动作方法编写为返回类型为 [Task](http://msdn.microsoft.com/en-us/library/system.threading.tasks.task.aspx) 或[Task<action result>的对象的单个方法。](http://msdn.microsoft.com/en-us/library/system.threading.tasks.task.aspx)

这些特性只有在 MVC 4 或 C# 5 中才有。下面是简化的代码。

```
public async Task<ActionResult> Search()
{
        string url = "http://search.twitter.com/search.atom?q=guycode&rpp=100&result_type=mixed";
        var webClient = new WebClient();
        string xmlResult = await webClient.DownloadStringTaskAsync(url);
        return Json(ReadTwitterResults(xmlResult), JsonRequestBehavior.AllowGet);
}
```

结果是相同的，但是现在您可以减少完成相同结果所需的代码量。

返回[任务](http://msdn.microsoft.com/en-us/library/system.threading.tasks.task.aspx)实例的异步动作方法也可以支持超时。要为您的操作方法设置时间限制，您可以使用 [AsyncTimeout](http://msdn.microsoft.com/en-us/library/system.web.mvc.asynctimeoutattribute.aspx) 属性。下面的示例显示了一个超时为 2500 毫秒的异步操作方法。一旦超时，视图“timed out”将显示给用户。

```
[AsyncTimeout(2500)]
[HandleError(ExceptionType = typeof(TaskCanceledException), View = "TimedOut")]
public async Task<ActionResult> Search()
{
        string url = "http://search.twitter.com/search.atom?q=guycode&rpp=100&result_type=mixed";
        var webClient = new WebClient();
        string xmlResult = await webClient.DownloadStringTaskAsync(url);
        return Json(ReadTwitterResults(xmlResult), JsonRequestBehavior.AllowGet);
}
```

异步操作没有改变任何东西。

控制器仍然需要从 [AsyncController](http://msdn.microsoft.com/en-us/library/system.web.mvc.asynccontroller.aspx) 中派生，您仍然可以用同样的方式访问动作，但是您必须编写更少的代码。

异步控制器非常适合运行很长的代码。大多数情况下，您将使用数据库，因此能够异步调用数据库对最终用户来说是一大优势。

他们为什么要等？

所有的代码示例都可以在这里下载。

## 分享这篇文章