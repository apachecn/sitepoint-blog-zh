# 主页讨厌逻辑。向它们抛出接口。

> 原文：<https://www.sitepoint.com/masterpages-hate-logic-throw-interfaces-at-them/>

母版页可以说是 ASP.NET 2.0 中最有用的新功能。它们解决了 1.1 中最大的问题之一——提供了一种在应用程序中实现通用外观的简洁方法。事实上，他们是如此的狡猾，以至于最终鼓励了一些不好的做法。即使用站点的母版页作为站点的基类。现在，说三遍:母版页不是为了逻辑，而是为了看起来&的感觉。

这并不是说使用某种基类是个坏主意。事实上，这是一个非常好的主意。但是一个基页类仍然意味着一个继承自[系统的类。Web.UI.Page](http://search.msdn.microsoft.com/search/Redirect.aspx?title=Page+Class&url=http://msdn.microsoft.com/library/en-us/cpref/html/frlrfsystemwebuipageclasstopic.asp) 。现在的挑战是，有了母版页和其他东西，人们最终会有点困惑:最终，这种逻辑需要在 web 控件中表达出来。母版页(而不是基类)直接知道这些控件。但是基页类不是，至少不是，除非一个人想开始一个乱七八糟的叫主人的游戏。FindControl()。然而，我认为基类不需要直接知道这些控件，而只需要知道这些控件的某些功能。一种合同，如果你愿意的话。。NET 方便地拥有编程契约，它们只是被称为[接口](http://search.msdn.microsoft.com/search/Redirect.aspx?title=Interfaces+(C%23)+&url=http://msdn2.microsoft.com/en-us/library/ms173156.aspx)。他们很棒。

我失去你了吗？好了，让我们开始深入场景和代码示例，好吗？

**场景**

您的项目需要在所有页面上显示一组通用的按钮。这些按钮是添加、编辑、删除和退出。现在，有些页面不允许使用这些按钮中的一个或全部。当这些按钮被点击时会发生什么的细节需要由每个具体的页面来处理。

**战略**

我们现在要做的是:

1.  创建一个接口 ICommandStrip，它封装了必要的功能。即用于添加、删除、编辑和退出的事件。以及一组用于启用或禁用命令的布尔值。
2.  为要实现的包含控件创建另一个接口 ICommandStripContainer。它的唯一目的是传递对具体 ICommandStrip 的引用
3.  实现 ICommandStrip 的用户控件
4.  实现 ICommandStripContainer 并实现一些基本管道的抽象基 MasterPage 类。
5.  一个具体的母版页类，实现了#4 中的抽象类。
6.  一个基类，它提供了对 ICommandStripContainer 的引用，并为该容器处理一些基本的管道。
7.  一个实际的页面，与命令条通信，以证明它一切顺利。

首先，我要说的是:

```
 /// <summary>
/// Command strip interface contract.
/// </summary>
public interface ICommandStrip
{
	/// <summary>
	/// Fired when the Add command is enacted.
	/// </summary>
	event EventHandler AddClicked;
	/// <summary>
	/// Fired when the Edit command is enacted.
	/// </summary>
	event EventHandler EditClicked;
	/// <summary>
	/// Fired when the Delete command is enacted.
	/// </summary>
	event EventHandler DeleteClicked;
	/// <summary>
	/// Fired when the Exit command is enacted.
	/// </summary>
	event EventHandler ExitClicked;

	/// <summary>
	/// Is the Add commmand enabled?
	/// </summary>
	bool IsAddable { get; set; }
	/// <summary>
	/// Is the Delete command enabled?
	/// </summary>
	bool IsDeletable { get; set; }
	/// <summary>
	/// Is the Edit command enabled?
	/// </summary>
	bool IsEditable { get; set; }
	/// <summary>
	/// Is the Exit command enabled?
	/// </summary>
	bool IsExitable { get; set;}
} 

```

现在，ICommandStripContainer:

```
 /// <summary>
/// Container interface for our Command Strip
/// </summary>
public interface ICommandStripContainer
{
	/// <summary>
	/// Gets a reference to the CommandStrip
	/// </summary>
	ICommandStrip CommandStrip { get;}
} 

```

跟我到目前为止？现在我们开始进入精彩部分。让我们看看用户控件:

```
 <%@ Control Language="C#" AutoEventWireup="true" CodeFile="ButtonCommandStrip.ascx.cs" Inherits="ButtonCommandStrip" %>
<div style="background-color: #e2e2e2; padding:5px; text-align: right;"> 
    <asp:Button runat="Server" ID="AddButton" OnClick="AddButtonClicked" Text="Add" />
    <asp:Button runat="Server" ID="EditButton" OnClick="EditButtonClicked" Text="Edit" />
    <asp:Button runat="Server" ID="DeleteButton" OnClick="DeleteButtonClicked" Text="Delete" />
    <asp:Button runat="Server" ID="ExitButton" OnClick="ExitButtonClicked" Text="Exit" />
</div> 

```

和代码隐藏:

```
 public partial class ButtonCommandStrip : System.Web.UI.UserControl, ICommandStrip
{
    public ButtonCommandStrip()
    {
        PreRender += new EventHandler(bindButtonAbilities);
    }

    private void bindButtonAbilities(object sender, EventArgs e)
    {
        AddButton.Enabled = IsAddable;
        EditButton.Enabled = IsEditable;
        DeleteButton.Enabled = IsDeletable;
        ExitButton.Enabled = IsExitable;        
    }

    #region ICommandStrip Members

    private event EventHandler addClicked;
    public event EventHandler AddClicked
    {
        add { addClicked += value; }
        remove { addClicked -= value; }
    }

    private event EventHandler editClicked;
    public event EventHandler EditClicked
    {
        add { editClicked += value; }
        remove { editClicked -= value; }
    }

    private event EventHandler deleteClicked;
    public event EventHandler DeleteClicked
    {
        add { deleteClicked += value; }
        remove { deleteClicked -= value; }
    }

    private event EventHandler exitClicked;
    public event EventHandler ExitClicked
    {
        add { exitClicked += value; }
        remove { deleteClicked -= value; }
    }

    public bool IsAddable
    {
        get
        {
            if (ViewState["IsAddable"]==null)
            {
                return true;
            }
            return (bool)ViewState["IsAddable"];
        }
        set
        {
            ViewState["IsAddable"] = value;
        }
    }

    public bool IsDeletable
    {
        get
        {
            if (ViewState["IsDeletable"] == null)
            {
                return true;
            }
            return (bool)ViewState["IsDeletable"];
        }
        set
        {
            ViewState["IsDeletable"] = value;
        }
    }

    public bool IsEditable
    {
        get
        {
            if (ViewState["IsEditable"] == null)
            {
                return true;
            }
            return (bool)ViewState["IsEditable"];
        }
        set
        {
            ViewState["IsEditable"] = value;
        }
    }

    public bool IsExitable
    {
        get
        {
            if (ViewState["IsExitable"] == null)
            {
                return true;
            }
            return (bool)ViewState["IsExitable"];
        }
        set
        {
            ViewState["IsExitable"] = value;
        }
    }

    #endregion

    protected void AddButtonClicked(object sender, EventArgs e)
    {
        if (addClicked != null)
        {
            addClicked(this, e);
        }
    }
    protected void EditButtonClicked(object sender, EventArgs e)
    {
        if (editClicked != null)
        {
            editClicked(this, e);
        }
    }
    protected void DeleteButtonClicked(object sender, EventArgs e)
    {
        if (deleteClicked != null)
        {
            deleteClicked(this, e);
        }
    }
    protected void ExitButtonClicked(object sender, EventArgs e)
    {
        if (exitClicked != null)
        {
            exitClicked(this, e);
        }
    }
} 

```

那里没什么特别的。它只是实现了 ICommandStrip，以及一些适当启用或禁用按钮的管道。

现在回到母版页基类的 App_Code:

```
 public abstract class SiteMasterPageBase : MasterPage, ICommandStripContainer
{
	/// <summary>
	/// Internal mapping property to get a reference to the concrete ICommandStrip control
	/// </summary>
	protected abstract ICommandStrip CommandStripControl { get; }

	/// <summary>
	/// Returns a reference to the CommandStripControl for use by the SitePage.
	/// </summary>
	public ICommandStrip CommandStrip
	{
		get { return CommandStripControl; }
	}
} 

```

这里唯一的巫术是它是一个抽象类。你会问为什么？非常简单——一个站点可以有多个母版页。如果您维护相同的后端接口，您可以在它们之间动态切换。或者至少在 PreInit 事件中。但是接下来的诀窍是将母版页连接到它们必须操作的控件上；因此出现了抽象的 CommandStripControl 属性。它的目的是充当具体母版页类和普通接口之间的桥梁。

为了证明这很容易，让我们看看我们网站的主页:

```
 <%@ Master Language="C#" AutoEventWireup="true" CodeFile="Site.master.cs" Inherits="Site" %>
<%@ Register Src="ButtonCommandStrip.ascx" TagName="ButtonCommandStrip" TagPrefix="SpUc" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html  >
<head runat="server">
    <title>SpBlog Test Page</title>
</head>
<body>
    <form id="form1" runat="server">
        <h1>Master Page Command Strip Demo</h1>
        <SpUc:ButtonCommandStrip id="CommandStripCtl" runat="server" />
        <asp:contentplaceholder id="MainContentPlaceHolder" runat="server" />
    </form>
</body>
</html> 

```

代码隐藏告诉母版页如何找到 ICommandStrip:

```
 public partial class Site : SiteMasterPageBase
{
    protected override ICommandStrip CommandStripControl
    {
        get { return this.CommandStripCtl; }
    }
} 

```

与这个 SiteMasterPageBase 类一起工作的是 SitePageBase 类:

```
 public class SitePageBase : Page
{
	public SitePageBase()
	{
		//Wire up the command strip. 
		//Note that Init is the earliest we can hook up the events.
		//They are not avaliable before the MasterPage has loaded its controls.
		Init+=new EventHandler(BindCommandStrip);
	}

	/// <summary>
	/// Gets a reference to the Master CommandStripContainer
	/// </summary>
	protected ICommandStripContainer CommandStripContainer
	{
		get
		{
			return (ICommandStripContainer)Master;
		}
	}

	#region commandstrip utility

	/// <summary>
	/// Binds the command strip events
	/// </summary>
	/// <param name="sender">sender</param>
	/// <param name="e">empty event args</param>
	private void BindCommandStrip(object sender, EventArgs e)
	{
		if (CommandStripContainer != null)
		{
			CommandStripContainer.CommandStrip.AddClicked += new EventHandler(AddClicked);
			CommandStripContainer.CommandStrip.DeleteClicked += new EventHandler(DeleteClicked);
			CommandStripContainer.CommandStrip.EditClicked += new EventHandler(EditClicked);
			CommandStripContainer.CommandStrip.ExitClicked += new EventHandler(ExitClicked);
		}
	}

	#region virtual event handler stubs

	protected virtual void ExitClicked(object sender, EventArgs e)
	{}

	protected virtual void EditClicked(object sender, EventArgs e)
	{}

	protected virtual void DeleteClicked(object sender, EventArgs e)
	{}

	protected virtual void AddClicked(object sender, EventArgs e)
	{ }
	#endregion

	#endregion
} 

```

这里也没什么特别的。一些连接事件的代码，一些便于以后覆盖的虚拟存根方法。请注意 CommandStripContainer 方法，它是回到母版页的 ICommandStrip 控件的桥梁。

现在是精彩的部分——一个具体实现的简单例子。首先是 ASPX 的文件:

```
 <%@ Page Language="C#" MasterPageFile="~/Site.master" AutoEventWireup="true" CodeFile="CommandStripPage.aspx.cs" Inherits="CommandStripPage" Title="Untitled Page" %>
<asp:Content ID="MainContentPlaceHolder" ContentPlaceHolderID="MainContentPlaceHolder" Runat="Server">
<p>Last Command: <asp:Label runat="Server" ID="WhatWasClicked" Text="Nothing Yet" ForeColor="red" Font-Bold="true" /></p>
<asp:CheckBox ID="AddableCheckbox" Text="Addable" runat="Server" Checked="true" /><br />
<asp:CheckBox ID="EditableCheckbox" Text="Editable" runat="Server" Checked="true" /><br />
<asp:CheckBox ID="DeletableCheckbox" Text="Deletable" runat="Server" Checked="true" /><br />
<asp:CheckBox ID="ExitableCheckbox" Text="Exitable" runat="Server" Checked="true" /><br />
<asp:Button runat="Server" ID="ApplyChangesButton" Text="Apply Ability Changes" OnClick="ApplyChanges" />
</asp:Content> 

```

现在是 C#位:

```
 public partial class CommandStripPage : SitePageBase
{
    protected override void EditClicked(object sender, EventArgs e)
    {
        WhatWasClicked.Text = "EDIT was clicked!";
    }

    protected override void DeleteClicked(object sender, EventArgs e)
    {
        WhatWasClicked.Text = "DELETE was clicked!";
    }

    protected override void AddClicked(object sender, EventArgs e)
    {
        WhatWasClicked.Text = "ADD was clicked!";
    }

    protected override void ExitClicked(object sender, EventArgs e)
    {
        WhatWasClicked.Text = "EXIT was clicked!";
    }

    protected void ApplyChanges(object sender, EventArgs e)
    {
        CommandStripContainer.CommandStrip.IsAddable = AddableCheckbox.Checked;
        CommandStripContainer.CommandStrip.IsDeletable = DeletableCheckbox.Checked;
        CommandStripContainer.CommandStrip.IsEditable = EditableCheckbox.Checked;
        CommandStripContainer.CommandStrip.IsExitable = ExitableCheckbox.Checked;
    }
} 

```

如你所见，这里也没有什么特别花哨的东西。只是像普通的 ASP.NET 页面一样处理一些事件。但诀窍是所有这些事件都由 SiteMasterPageBase & SitePageBase 连接起来，而这个页面一点也不关心。当你需要交换控制结构时，你只需要在一个地方交换它们，因为你的页面只共享定义的接口而不是类。

享受吧，快乐编码，如果你喜欢的话不要忘记[踢它](http://www.dotnetkicks.com)。

[一些编辑注释&大声喊出来:这个演示的灵感来自于 [SitePoint 的 ASP.NET 论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=141)上的[这个帖子](https://www.sitepoint.com/forums/showthread.php?t=417234)。而且我确实借用了[浦发的帖子](https://www.sitepoint.com/forums/showpost.php?p=3017227&postcount=6)的大部分界面。他的 enum 比我的 bools 更优雅一点，但是我变懒了。]

## 分享这篇文章