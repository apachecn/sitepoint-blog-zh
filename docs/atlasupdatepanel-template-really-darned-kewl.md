# Atlas:UpdatePanel + Template =真该死 Kewl

> 原文：<https://www.sitepoint.com/atlasupdatepanel-template-really-darned-kewl/>

我必须说[ASP.NET 图册](http://atlas.asp.net/)，微软的免费 Ajax 框架非常酷。毫无疑问，我最喜欢的特性是，它允许我，一个挑战 Javascript 的 C#头，在不离开我的服务器端舒适区的情况下，构建真正光滑的 Ajaxy 应用程序。所有需要做的就是编写应用程序，使其在正常的 ASP.NET 生命周期、事件和参数内工作。然后放一些 Atlas:UpdatePanels 进去，让框架做它自己的事情。

至少我是这么认为的。我试图将这些 UpdatePanels 包含在一个模板化的控件中，比如一个 Repeater。阿特拉斯爆炸了。现在，这不是一个不可克服的问题。人们总是可以在更新面板中包围整个中继器。或者回到经典的，非地图集的方法。或者学习 Javascript，制作自己的管道。这些都不是很吸引人的选择。

方便的是，Atlas 团队也认为这是一个值得修复的问题。6 月 30 日，[他们向公众发布了另一个 CTP](http://weblogs.asp.net/atlas/archive/2006/06/30/455316.aspx)——创造性地称之为“Atlas”6 月 CTP。它有许多调整和缺陷修复，其中一个是我最喜欢的:

> 更新面板可以在整个页面生命周期中动态添加到页面中，包括模板中的更新面板。UpdatePanels 现在也可以在 WebParts 中工作，并且 WebParts 可以在 UpdatePanels 中。

所以，我做了一个小演示来展示这个新功能有多方便。而且，通过推论，阿特拉斯可以有多酷。

**场景:**您有一个包含四个字段的联系人列表:Id、名字、姓氏和电子邮件。这是要求一个人不能看到一个电子邮件地址，没有点击一个链接，指示应用程序显示该地址。一个[Xml 格式数据集的例子贴在本帖](#xml)的最后，以供参考。

现在，我们可以用两种方法来实现。首先，可以依靠传统的回发和全页面刷新。或者一个人可以拥抱 Web 2.0 并打开他们的地图集。真正巧妙的是，这两种方法可以使用完全相同的代码隐藏文件[好吧，不完全是，您需要更改类名以匹配。aspx 文件]。看起来是这样的:

```
 private DataSet peopleDs;

protected DataTable People
{
	get
	{
		if (peopleDs == null)
		{
			peopleDs = new DataSet();
			peopleDs.ReadXml(Server.MapPath("~/App_Data/Data.xml"));
		}
		return peopleDs.Tables[0];
	}
}

protected void Page_Load(object sender, EventArgs e)
{
	if (!this.IsPostBack)
	{
		this.DataBind();
	}
}

protected void ItemButtonClicked(object sender, EventArgs e)
{
	Control csender = (Control)sender;
	Literal litid = (Literal)csender.NamingContainer.FindControl("PersonId");
	string email=getEmailAddress(litid.Text);
	HyperLink link = (HyperLink)csender.NamingContainer.FindControl("EmailAddress");
	link.NavigateUrl = string.Format("mailto:{0}", email);
	link.Text = email;
	csender.Visible = false;
	link.Visible = true;
}

private string getEmailAddress(string id)
{
	string ret = "NO EMAIL";
	DataRow[] match = People.Select("Id=" + id);
	if (match.Length > 0)
	{
		ret = (string)match[0]["Email"];
	}
	return ret;
} 

```

现在，显示这个的传统方法非常简单。请参见下面的示例:

```
 <form id="form1" runat="server">
	<div>
	<asp:Repeater runat="server" ID="PersonRepeater" DataSource="<%# People %>">
		<ItemTemplate>
			<div style="border: solid 1px black; padding: 5px">
				<asp:Literal ID="PersonId" runat="server" 
						Visible="false" Text='<%# DataBinder.Eval(Container.DataItem, "Id") %>' />
					First Name:
					<%# DataBinder.Eval(Container.DataItem, "FirstName") %>
					<br />
					Last Name:
					<%# DataBinder.Eval(Container.DataItem, "LastName") %>
					<br />
				<asp:LinkButton runat="server" ID="ShowEmail" OnClick="ItemButtonClicked" 
								Text="Show Contact Info" />
				<asp:HyperLink ID="EmailAddress" runat="server" Visible="False" />
			</div>
		</ItemTemplate>
	</asp:Repeater>
	</div>
</form> 

```

但是，如果您的客户要求他的站点支持 Ajax 呢？这简单得令人不安——只需添加一点 Atlas 管道和一个更新面板。哦，别忘了 Atlas:UpdateProgress，这样你就可以证明账单的合理性了。

```
 <form id="form1" runat="server">
	<atlas:ScriptManager ID="TheScriptManager" runat="server" EnablePartialRendering="true" />
	<div>
		<atlas:UpdateProgress ID="UpdateProgress" runat="server">
			<ProgressTemplate>
				<span style="position: absolute; top: 0; left: 0; height: 15px; color: White; background-color: Red;
					width: 50px">UPDATING</span>
			</ProgressTemplate>
		</atlas:UpdateProgress>
		<asp:Repeater runat="server" ID="PersonRepeater" DataSource="<%# People %>">
			<ItemTemplate>
				<div style="border: solid 1px black; padding: 5px">
					<asp:Literal ID="PersonId" runat="server" 
						Visible="false" Text='<%# DataBinder.Eval(Container.DataItem, "Id") %>' />
					First Name:
					<%# DataBinder.Eval(Container.DataItem, "FirstName") %>
					<br />
					Last Name:
					<%# DataBinder.Eval(Container.DataItem, "LastName") %>
					<br />
					<atlas:UpdatePanel ID="RepeaterUpdatePanel" runat="Server" Mode="conditional">
						<Triggers>
							<atlas:ControlEventTrigger ControlID="ShowEmail" EventName="Click" />
						</Triggers>
						<ContentTemplate>
							<asp:LinkButton runat="server" ID="ShowEmail" OnClick="ItemButtonClicked" 
								Text="Show Contact Info" />
							<asp:HyperLink ID="EmailAddress" runat="server" Visible="False" />
						</ContentTemplate> 
					</atlas:UpdatePanel>
				 </div> 
			</ItemTemplate>
		</asp:Repeater>
	</div>
</form> 

```

上面首先要注意的是新的 Atlas:SomeControlName 标签。剧本经理主持演出；需要“启用地图集”页面。UpdateProgress 控件是一个视觉上的亮点——它允许用户告诉用户更新正在进行中，就像你在 Gmail 右上角看到的“加载”一样。

这让我们想到了 Atlas:UpdatePanel。这是一个非常灵活的控件，允许页面部分呈现，并基于注册的事件进行更新。在本例中，我们提供了一个模板(包含 LinkButton 和与之交互的超链接)和一个触发器。这个触发器是 Atlas 用来启动更新的。一个面板可以有多个触发器。在我们的例子中，我们只需要一个 LinkButton 的 click 事件。如代码示例所示，该事件的行为与标准的整页回发几乎相同。

6.4 分钟从零到 Web 2.0。无需编写一个 Javascript 字符，也无需修改 C#。

**附录:Xml 文件**

```
 <Persons>
  <Person>
    <Id>1</Id>
    <FirstName>Donald</FirstName>
    <LastName>Duck</LastName>
    <Email>DonaldDuck@disney.com</Email>
  </Person>
  <Person>
    <Id>2</Id>
    <FirstName>Mickey</FirstName>
    <LastName>Mouse</LastName>
    <Email>MickeyMouse@disney.com</Email>
  </Person>
  <Person>
    <Id>3</Id>
    <FirstName>Peter</FirstName>
    <LastName>Pan</LastName>
    <Email>Peter@neverland.net</Email>
  </Person>
  <Person>
    <Id>4</Id>
    <FirstName>Captain</FirstName>
    <LastName>Hook</LastName>
    <Email>CapHook@neverland.net</Email>
  </Person>
</Persons> 

```

## 分享这篇文章