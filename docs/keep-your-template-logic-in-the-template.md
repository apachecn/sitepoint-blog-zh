# 将您的模板逻辑保留在模板中

> 原文：<https://www.sitepoint.com/keep-your-template-logic-in-the-template/>

最近我偶然发现了这篇[博文](http://codeeleven.blogspot.com/2007/11/keep-presentation-and-logic-seperate.html)。不幸的是，[乔纳森·霍兰德](http://codeeleven.blogspot.com/)已经把事情完全弄错了。首先也是最重要的一点，这篇文章只是谈到了所有意义上的表达。而且，在现代复杂的应用程序中，表示可以很容易地包含一点逻辑，使它具有市场营销类型所喜欢的“粘性”。你的核心，应用程序逻辑绝对不应该存在于 ASP.NET 的网页上。或者最好根本不在 web 项目中。但是你的模板逻辑可以很容易地存在于模板中，如果可能的话，应该更多地存在于 ASPX 模板中，而不是代码背后。

正如 Holland 先生的一些评论者所指出的，他的方法从一开始就有根本性的缺陷，因为它仍然依赖于相当松散的 FindControl()来发挥它的魔力。这有点弄巧成拙，因为依赖这种方法会破坏他所宣扬的一些优势。但是，除了依赖这种方法之外，还有其他更深层次、更致命的缺陷:

1.  如果您依赖 CodeBehind 来做所有事情，那么如果不重新编译和重新部署整个应用程序，或者至少只重新编译和重新部署主 DLL，您就无法在 repeater 中更改任何内容。这意味着做一个小的文本更改将导致一个完整的回收，转储任何现有的会话，并迫使用户等待通过 ASP。NET 的加速循环。这也意味着紧急修复需要开发人员使用 Visual Studio 并访问整个项目的源代码。而我曾在必要时在生产服务器上使用记事本进行紧急模板级别的修复。

    即使在设计阶段，对输出做一个小的改变也需要重新编译应用程序，这大大降低了开发过程中最可替换部分的速度。

2.  从几个角度来看，所有的服务器控件都会产生一些讨厌的开销。首先，它们有视图状态。其次，它们是服务器控件，因此将被解析并加载到控件树中。[编辑:正如霍兰德先生在下面指出的，这有点不清楚，部分是错误的。HtmlControls 也出现在控件树中，但是它们比真正的 WebControls“轻”得多——没有事件模型，没有视图状态。]

3.  在我看来，当 HTML 存在于 HTML 风格的模板中时，比完全脱离服务器端更容易理解 HTML。

4.  如果你和一个独立的设计团队一起工作，无论如何他们都会给你 HTML。为什么要大费周章地把它翻译成编译好的服务器端代码呢？此外，如今大多数优秀的设计师至少能在某种程度上处理 ASPX 模板风格的代码——他们知道不要进入领域，如果需要的话，通常可以“阅读”这些东西。更聪明的人甚至可以掌握足够多的数据绑定语法，几乎可以派上用场。

现在，你可能会问——当我在模板中需要一些条件逻辑时，我该怎么办？我肯定必须使用代码隐藏来支持它？而且，至少对于模板中需要的少量逻辑，答案是否定的，它们都可以内联完成。即使对于较重的逻辑，如果需求需要，也可以直接从模板调用更深层次的应用程序服务。让我们使用 Holland 先生的 rptrComment 的一个稍微复杂一点的版本，它绑定到一个包含以下字段的 comments 类中:

*   Guid Id
*   字符串作者名称
*   Uri AuthorWeb
*   字符串作者电子邮件
*   字符串 AuthorIpAddress
*   string CommentText
*   DateTime CommentTimeStamp
*   布尔问题被抑制

现在，就模板逻辑而言，您的需求陈述如下:

*   所有用户都应该能够看到非隐藏注释的 AuthorName、AuthorWeb 和 CommentText 以及 CommentTimeStamp 字段。
*   文章所有者和管理员将看到上述字段，以及 AuthorEmail 和 AuthorIpAddress。此外，他们将看到所有评论，甚至是被禁止的评论。
*   管理员应能够禁止评论。

现在，人们可以通过大量的服务器控件和代码隐藏来做到这一点。或者可以将所有显示层的东西都推到模板中，并从那里调用后端类，如您的 SecurityHelper:

```
 <asp:Repeater runat="server" ID="CommentsRepeater" DataSource='<%# GetComments %>'>
    <HeaderTemplate>
        <ul class="commentcontainer">
    </HeaderTemplate>
    <FooterTemplate>
        </ul>
    </FooterTemplate>
    <ItemTemplate>
        <li runat="server" visible='<%# ((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).IsSuppressed && !SitePoint.Core.Security.SecurityHelper.CanSeeCommentInfo(User) ? false : true %>'>
           <p><%# string.IsNullOrEmpty(((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).CommentText) ? "No comment . . ." : ((SitePoint.Core.Entities.Content)Container.DataItem).CommentText %></p>
           <p>
               By : <a href='<%# ((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).AuthorWeb.ToString() %>'><%# ((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).AuthorName %></a> on <%# ((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).CommentTimeStamp.ToShortDateString() %> at <%# ((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).CommentTimeStamp.ToShortTimeString() %></p>
           <p runat="server" visible='<%# SitePoint.Core.Security.SecurityHelper.CanSeeCommentInfo(User) %>'>
                <a href='mailto:<%#((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).AuthorEmail %>'><%# ((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).AuthorEmail %></a> 
                • 
                IP: <%# ((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).AuthorIpAddress %>
                <asp:LinkButton runat="server" ID="SuppressCommentButton" CommandArgument='<%# ((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).Id %>' Text='[Suppress Comment]' Visible='<%# !((SitePoint.Core.Entities.Content.ArticleComment)Container.DataItem).IsSuppressed && SitePoint.Core.Security.CanSuppressComment(User) %>' OnClick="DoSuppressComment" />
           </p>
        </li>
    </ItemTemplate>
</asp:Repeater> 
```

现在，乍一看，这看起来非常复杂。但事实并非如此——90%的数据绑定代码将容器的[repeater item]DataItem 强制转换为它的自然类:((SitePoint。core . entities . content . article comment)容器。DataItem)。一旦人们忽略了这一点(或者只是添加一个我不愿意做的),它就会变得更容易理解——实际上是一个简单的 ASPX 模板，仅此而已。此外，它比将所有这些模板逻辑放入代码中更具可塑性，因为您可以调整、重新加载、调整和重新加载，而无需重新构建。

你可能会问的一个问题是“为什么不调用 DataBinder 而进行所有的强制转换？Eval？嗯，因为，当一个人知道你的 DataItem 公开了什么类时，有很多好处:

1.  智能感知通常使用这种策略。我应该在这里指出，在某些情况下，使用这么多的数据绑定会让设计者抓狂，不需要总是听从它。
2.  数据绑定。Eval 由于使用反射而慢得多(参见本页的[上的注释)。](http://msdn2.microsoft.com/EN-US/library/2d76z3ck.aspx)
3.  我们可以在现在强类型化的引用上调用方法。数据绑定。Eval()只允许我们输出字符串。

你可能会问的第二个问题是“为什么在一些 HTML 标签上使用 runat='server '？”这里的原因是这些已经成为你想要隐藏的文章的容器。首先，在

*   元素上，如果 ArticleComment，我们隐藏整个内容。问题被抑制，如果用户没有适当的权限。后来，我们添加了一个

    来包含给定帖子的管理功能。

为了完全公开，我应该指出这种方法的一个重要问题:重构。现代工具，比如 Resharper 和 Visual Studio 2005+，都有一些很好的工具来自动命名和改变代码的位置。因为您的 ASPX 模板中的代码直到运行时才被编译，所以这些工具经常错过这个角度。因此，如果你有一个大的重构工作，你将不得不接触每个模板文件，以确保它们与后端仍然兼容。当然，如果你有一个大的重构工作，你可能会接触很多前端代码，所以这可能不是一个可怕的缺点。

记住孩子们，模板中的模板逻辑是个好东西。不要让那个邪恶的人告诉你别的。

[![kick it on DotNetKicks.com](img/fe2ae2e7b86a1d5a3c08a2eac248ed03.png)](https://www.dotnetkicks.com/kick/?url=http%3a%2f%2fwww.sitepoint.com%2fblogs%2f2007%2f11%2f25%2fkeep-your-template-logic-in-the-template%2f)

## 分享这篇文章