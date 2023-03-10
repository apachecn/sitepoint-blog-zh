# 在会话超时之前捕捉它们

> 原文：<https://www.sitepoint.com/catching-session-timeouts-before-they-bite/>

使用会话存储用户信息是 ASP.NET 的一个非常方便的特性。但它也不是没有阴暗面。主要困难之一是会话可能会意外死亡。发生这种情况的原因有很多。可能会超时。服务器可以决定它需要回收应用程序。用户可以清除他的会话 cookies。可能有太阳黑子。

在轻微的情况下，这只会导致一些“对象引用未设置为对象实例”的错误。在糟糕的情况下，当试图基于不存在的数据更新底层数据存储时，它会导致数据损坏。在任何情况下，都可以使用一点代码来阻止它。

策略是这样的:首先，我们需要创建一个 SessionTimeoutWatcher 类来监视超时。这个类需要一些信息来运行——即当前会话 ID、前一个请求的会话 ID，以及对一些页面级变量和事件的访问。

为了抽象存储和检索会话 id 的机制，我们将创建一个小接口:

```
 public interface ISessionContainer
{
	/// <summary>
	/// Identifying string of the current requests' Session.
	/// </summary>
	string CurrentSessionId { get; set;}
	/// <summary>
	/// Identifying string of the previous requests' Session.
	/// </summary>
	string PreviousSessionId { get; set; }
	/// <summary>
	/// Flag determining if exceptions will be thrown upon Session timeout.
	/// </summary>
	bool EnforceSessionWatch { get; }
} 

```

我还添加了一个标志，允许容器确定会话观察是否被强制执行。

接下来，我们需要观察器本身:

```
 public class SessionTimeoutWatcher
{
	private ISessionContainer sessionContainer;
	private Page page;

	/// <summary>
	/// Initializes and sets up the Session Timeout watcher.
	/// </summary>
	/// <param name="sessionContainer">Container session data used by watcher</param>
	/// <param name="currentPage">Page requested.</param>
	public SessionTimeoutWatcher(ISessionContainer sessionContainer, Page currentPage)
	{
		page = currentPage;
		this.sessionContainer = sessionContainer;
		page.Load += new EventHandler(setupSessionWatch);
		page.LoadComplete += new EventHandler(checkSession);
	}

	private void setupSessionWatch(object sender, EventArgs e)
	{
		sessionContainer.PreviousSessionId = sessionContainer.CurrentSessionId;
		sessionContainer.CurrentSessionId = page.Session.SessionID;
	}

	private void checkSession(object sender, EventArgs e)
	{
		//Make sure we must enforce this logic.
		if (sessionContainer.EnforceSessionWatch)
		{
			//Check the session ids to see if they match.
			//If previous ID is null or empty, it means the session is new, which is OK.
			if (
				!string.IsNullOrEmpty(sessionContainer.PreviousSessionId)
				&&
				sessionContainer.CurrentSessionId != sessionContainer.PreviousSessionId
			   )
			{
				throw new SessionExpiredException("Session ID has changed.");
			}
			//Check if the session has been reset.
			if (
				sessionContainer.CurrentSessionId == sessionContainer.PreviousSessionId
				&&
				page.Session.IsNewSession
			   )
			{
				throw new SessionExpiredException("Session has been reset.");
			}
		}
	}
} 

```

以上不算太花哨。它只是引用 ISessionContainer 和构造器中的当前页面，然后将自己连接到几个页面事件中。

关键方法是 checkSession(object，EventArgs)方法，它处理实际的检查。检查是双重的。第一个 if 语句确保会话 id 没有改变。例如，这将表明用户清除了他们的 cookies。第二个检查会话的 IsNewSession 标志，以确定会话是否已回收但具有相同的 ID。

如果检查无效，它会抛出一个自定义异常，告诉程序会话已经过期。然后，应用程序可以使用页面级或应用程序级错误处理程序来确定要做什么(如果有的话)。

最后，在页面上将其连接起来非常简单。添加这个函数和许多其他全局函数的最佳位置是应用程序中所有页面共享的一个基类。

```
 public class PageBase : Page, ISessionContainer
{
    public PageBase()
    {
        timeoutWatcher = new SessionTimeoutWatcher(this, this);
    }

    private SessionTimeoutWatcher timeoutWatcher;

    #region ISessionContainer Members

    public string CurrentSessionId
    {
        get
        {
            return (string)ViewState["__CurrentSessionId"];
        }
        set
        {
            ViewState["__CurrentSessionId"] = value;
        }
    }

    public string PreviousSessionId
    {
        get
        {
            return (string)ViewState["__PreviousSessionId"];
        }
        set
        {
            ViewState["__PreviousSessionId"] = value;
        }
    }

    public virtual bool EnforceSessionWatch
    {
        get 
        {
            return true;
        }
    }

    #endregion
} 

```

这里需要注意的一点是，EnforceSessionWatch 方法被有意声明为虚拟的。这允许开发人员在给定页面上轻松地覆盖它，以跳过会话实施。

享受& [喜欢就踢](https://www.dotnetkicks.com/)。

最后，您可以从这里下载包含上面使用的所有代码的示例项目。

## 分享这篇文章