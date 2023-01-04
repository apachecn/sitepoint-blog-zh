# 在 ASP.NET 使用页面标题

> 原文：<https://www.sitepoint.com/working-with-the-page-title-in-aspnet/>

最近，我有机会使用 ASP.NET 2.0，但是我仍然没有真正投入进去的商业理由。我注意到下一个版本对页面元素有了更多的访问。一个特别的特性是访问页面的标题，所以可以很容易地在代码中修改它。我经常想这样做，我通常利用传递代码来完成一些动态的事情。

谢天谢地，这可以在当前版本的 ASP.NET 中实现。通过将页面标题标签设置为服务器控件(即使用
runat="server ")并在代码隐藏或页面上使用以下内容，这非常简单:

 `Protected pageTitle As System.Web.UI.HtmlControls.HtmlGenericControl
Private Sub Page_Load(ByVal sender As System.Object, _
ByVal e As System.EventArgs) Handles MyBase.Load
pageTitle.InnerText = "Title"
End Sub`

这是 VB.NET，但很容易翻译成 C#。

## 分享这篇文章