# 数据绑定保护第 1 部分:数据绑定 101

> 原文：<https://www.sitepoint.com/databinding-protips-part-1-databinding-101/>

这段代码有什么问题？

ASPX 文件:

```
 <p>
    The time is now <asp:Literal runat="Server" ID="TimeContainer" />.
</p> 
```

代码隐藏:

```
 protected override void OnLoad(EventArgs e)
{
    TimeContainer.Text = DateTime.Now.ToString();
} 
```

技术上没什么。但是就编写可靠的、可维护的应用程序而言，这是一个大问题。如果你想改变界面文本，你可能需要找一个程序员来修改代码。根据您使用的 ASP.NET 项目的类型，您可能必须进行完全的重新部署，因为您必须重新编译应用程序来更改 UI 中的一些单词。对我来说，这有点工作量太大，风险也太大。

然而，有一种更好的方法:声明性数据绑定。

ASPX:

```
 <p runat="Server" id="DataBindingContainer">
The time is now <%# CurrentDateTime.ToString() %>.
<br />
And the current longdate is <%# CurrentDateTime.ToLongDateString() %>.
</p> 
```

代码隐藏:

```
 protected override void OnLoad(EventArgs e)
{
    DataBindingContainer.DataBind();
}

protected DateTime CurrentDateTime
{
    get { return DateTime.Now; }
} 
```

关于这里的代码，有一些事情需要注意。首先是对 DataBindingContainer 的调用。DataBind()。这是因为您必须使用任何声明绑定的元素调用 DataBind()容器。第二是注意我们如何为 CurrentDateTime 创建一个受保护的属性。我们可以直接绑定到日期时间。现在，但我选择将它显示为受保护的属性是有原因的:数据绑定实际上发生在系统生成的类中，该类继承自您的页面，因此从 ascx 模板调用的任何内容都必须可被该类访问；私有变量不需要应用。

您可能想知道为什么我坚持使用第二个数据绑定表达式，原因很简单:为了说明您可以调用数据绑定到的任何对象上的任何方法，而不仅仅是基本的 ToString()调用。这些是对完整对象的强类型调用，因此您可以调用代码隐藏中可以调用的任何内容。

Wyatt，这很酷，但是如果我想要一些逻辑呢？

然后你需要见见我们的新朋友，三元算子:

```
<p>Daylight savings time <%# CurrentDateTime.IsDaylightSavingTime() ? "is" : "is not" %> in effect.</p>
```

这是一个非常方便的语法技巧，可以将一个简单的 if 语句压缩成一行。上述示例将转化为:

```
private string GetDaylightSavingsStuff()
{
    if (CurrentDateTime.IsDaylightSavingTime())
    {
        return "is";
    }
    else
    {
        return "is not";
    }        
}
```

您也可以使用这种策略来设置服务器控件的可见属性。例如，假设我们想要发布一个小公告，如果它实际上是一个周末:

```
<asp:PlaceHolder runat="Server" ID="DataBindingContainer">
    <p>The time is now <%# CurrentDateTime.ToString() %>.</p>
    <p>Daylight savings time <%# CurrentDateTime.IsDaylightSavingTime() ? "is" : "is not" %> in effect.</p>
    <p runat="Server" id="Weekend" style="font-weight:bold; color: red" visible='<%# CurrentDateTime.DayOfWeek == DayOfWeek.Saturday || CurrentDateTime.DayOfWeek == DayOfWeek.Sunday ? true : false %>'>
        ITS THE WEEKEND! What are you doing coding man!
    </p>
    <p runat="server" id="Weekday" style="font-weight:bold; color: blue" visible='<%# CurrentDateTime.DayOfWeek == DayOfWeek.Saturday || CurrentDateTime.DayOfWeek == DayOfWeek.Sunday ? false : true %>'>
        ITS A WEEKDAY! Get back to work man!
    </p>
</asp:PlaceHolder>
```

现在，在这一点上，您想知道在您的模板化控件中到底要做什么，比如 Repeater 或 GridView。对于这些知识，您应该继续关注数据绑定保护第 2 部分:使用数据绑定器。娱乐和利润评估。

想玩上面例子中使用的代码吗？得到密码的人。

[![kick it on DotNetKicks.com](img/496ebc8224517222d6c7192111b695f9.png)](https://www.dotnetkicks.com/kick/?url=https://www.sitepoint.com/databinding-protips-part-1-databinding-101/) 如果你喜欢。

## 分享这篇文章