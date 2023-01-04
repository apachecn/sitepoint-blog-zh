# 从 LoginViews 中获取强类型控件引用——不使用 FindControl()

> 原文：<https://www.sitepoint.com/getting-stongly-typed-controls-references-out-of-loginviews-without-findcontrol/>

今天我在开发一个相当复杂的 web 表单，它使用了大量的 [LoginView](http://msdn2.microsoft.com/en-us/library/system.web.ui.webcontrols.loginview.aspx) 控件，以及包含自定义用户控件的特定于角色的模板。现在，起初我认为我可以直接引用 LoginView 的 ContentTemplates 中的项目。但事实并非如此。ControlTemplates 表现为松散类型的 ITemplates。现在可以使用可靠的老式 FindControl(string)方法来推测控件名。但是这总让我觉得有点讨厌，因为它在编译时没有进行类型检查。例如，如果有人更改了控件的 ID，就不必担心出错的可能性。或者将其包装在父控件中，从而在控件层次结构中隐藏它。Hello 对象引用未设置为对象的实例。

相反，我意识到一个人可以非常容易和干净地做到以下几点:

1.  在代码文件(或任何包含 LoginView 的文件)中，创建所需的 UserControl 或 custom ServerControl 或其他类型的受保护变量。
2.  在需要引用的控件中，连接 OnInit 事件。
3.  在 OnInit 事件的处理程序中，将发送者映射到您在步骤 1 中创建的变量。

或者，如果这没有意义，只需查看下面的代码示例:

首先是 ASPX 电码:

```
 <asp:LoginView ID="PermissionsLoginView" runat="Server">
	<RoleGroups>
		<asp:RoleGroup Roles="Administrator">
			<ContentTemplate>
				<wwb:UserPermissions 
					ID="UserPermissionsCtl" 
					runat="server" 
					OnInit="UserPermissionsCtl_Init" 
				/>
			</ContentTemplate>
		</asp:RoleGroup>
	</RoleGroups>
</asp:LoginView> 
```

这里的关键项是 wwb:UserPermissions 对 OnInit 的调用。

现在是代码文件部分:

```
 //Variable to contain the user control reference.
protected UserPermissions UserPermissions = null;

//handler for the UserPermissionsCtl's Init event.
protected void UserPermissionsCtl_Init(object sender, EventArgs e)
{
	UserPermissions = (UserPermissions)sender;
} 
```

除了 LoginView 之外，我还没有尝试过它，但是它应该适用于其他多模板控件(比如 MultiView)。享受快乐编码。

## 分享这篇文章