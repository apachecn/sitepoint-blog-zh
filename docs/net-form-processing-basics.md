# ASP.NET 表单处理基础

> 原文：<https://www.sitepoint.com/net-form-processing-basics/>

在我的第一篇文章中，[ASP.NET](http://www.webmasterbase.com/article/781)入门(如果你还没有看过的话，你应该先看看)，我向你介绍了微软。NET 平台，带您完成了。NET 框架，并向您展示了如何创建一个非常基本的 ASP.NET 网页。

在这篇文章中，我们将采取下一步对 ASP.NET 的专业知识。我将用一个例子来演示如何使用 ASP.NET 来处理一个简单的表单提交。和以前一样，所有的例子都将在 C#和 VB.NET 中出现。

##### 个性化的问候

这是本系列上一篇文章中完成的示例(`hellothere-cs.aspx`):

```
<%@ Page Language="C#" %> 

<html> 

<head> 

<title>My First ASP.NET Page</title> 

<script runat="server"> 

  protected void Page_Load(Object Source, EventArgs E) 

  { 

    TimeLabel.Text = DateTime.Now.ToString(); 

  } 

</script> 

</head> 

<body> 

<p>Hello there!</p> 

<p>The time is now: <asp:label runat="server" id="TimeLabel" /></p> 

</body> 

</html>
```

您可能还记得，这个简单的页面根据运行它的服务器显示当前时间:

![My First ASP.NET Page](img/a556ccd92493744724beec8cbfa906df.png)

虽然有效，你必须承认这一页上的问候有点不近人情。如果页面用名字问候每个用户不是很好吗？

![Personalised Greeting](img/28bd819d3785fff77773092f3ac1cc01.png)

当然，要做到这一点，我们需要提示每个用户输入他或她的名字。为此，我们需要一个表单…

##### ASP.NET 服务器控件

下面是我们希望页面在提示用户输入姓名时的样子:

![Submission Form](img/f97f88301ca06fca273ed4d02c225611.png)

如您所见，该页面包含一个文本字段和一个提交按钮。如果您要用标准的 HTML 创建这个页面，表单的代码应该是这样的:

```
<form action="hellothere-cs.aspx">  

  <p>Do you have a name?</p>  

  <p><input type="text" name="NameBox" />  

     <input type="submit" value="Submit" /></p>  

</form>
```

现在，ASP.NET 提供了一组名为*服务器控件*的特殊标签。我们在前一篇文章的这行代码中看到了一个简单的服务器控件:

```
<p>The time is now: <**asp:label runat="server" id="TimeLabel" /**></p>
```

这里的`<asp:label>`标签是一个 ASP.NET 服务器控件的例子。服务器控件是 ASP.NET 网页(或者微软称之为 Web 窗体)的元素，可以用服务器端代码访问。在这种情况下，我们的`Page_Load`脚本设置了`<asp:label>`标签的文本来显示当前时间。

虽然有一些简单的服务器控件如`<asp:label>`是用于一般用途的，但大多数服务器控件是用于表单中的。例如，`<asp:textbox>`控件是 ASP.NET 最常用的控件之一。下面是我们的表单被修改为使用`<asp:textbox>`服务器控件后的样子:

```
<form **runat="server" id="NameForm"**>  

  <p>Do you have a name?</p>  

  <p><**asp:textbox runat="server" id="NameBox" /**>  

     <input type="submit" value="Submit" /></p>  

</form>
```

你会注意到我已经用一个`<asp:textbox>`标签替换了`<input type="text">`标签。属性告诉 ASP.NET，它负责将标签转换成 Web 浏览器能够理解的东西。我们还将`<input type="text">`标签的 name 属性更改为 id 属性，因为 ASP.NET 服务器控件希望通过它们的 id 来标识。

您还会注意到，我稍微修改了`<form>`标签。任何包含 ASP.NET 服务器控件的表单都必须有一个`runat="server"`属性，因为 ASP.NET 不仅需要知道服务器控件，还需要知道它们所属的表单。我还为表单分配了一个 id，这样如果需要的话，我们可以在代码的其他地方引用它。`<form>`标签的`action`属性已被完全移除；ASP.NET 负责在处理`<form>`标签时为我们添加它。

现在让我们把注意力集中在`<asp:textbox>`标签上。通过设置`<asp:textbox>`的各种属性，它可以作为 HTML `<input type="text">`和`<textarea>`标签的服务器端替代品。所以基本上任何时候你想让用户输入文本时，`<asp:textbox>`是可以使用的标签。在这种情况下，它的行为就像一个`<input type="text">`标签，除了它具有 ASP.NET 服务器控件的所有特性(对我们的目的来说最重要的是，它可以从我们的 ASP.NET 代码中方便地访问)。

让我们将这个表单代码插入到我们的`hellothere-cs.aspx`文件中:

```
<%@ Page Language="C#" %>  

<html>  

<head>  

<title>My First ASP.NET Page</title>  

<script runat="server">  

  protected void Page_Load(Object Source, EventArgs E)  

  {  

    TimeLabel.Text = DateTime.Now.ToString();  

  }  

</script>  

</head>  

<body>  

<p>Hello there!</p>  

<form **runat="server" id="NameForm"**>  

  <p>Do you have a name?</p>  

  <p><**asp:textbox runat="server" id="NameBox" /**>  

     <input type="submit" value="Submit" /></p>  

</form>  

<p>The time is now: <asp:label runat="server" id="TimeLabel" /></p>  

</body>  

</html>
```

在继续处理表单提交之前，我们将对这个页面的 HTML 再做一次修改。页面上的问候语目前由以下简单的 HTML 代码生成:

```
<p>Hello there!</p>
```

当用户提供姓名时，我们希望用他或她的名字替换单词“there”。我们将通过用一个`<asp:label>`标签包围这个单词来实现这一点:

```
<p>Hello <**asp:label runat="server" id="NameLabel"**>there  

<**/asp:label**>!</p>
```

之前，我们为`TimeLabel`使用了一个自结束的`<asp:label>`标签(即`<asp:label ... />`)来在文档中创建一个位置，我们可以在该位置插入当前时间。在本例中，我们用一个`<asp:label>`标签将一段文本(单词“there”)包围起来，以标记我们可以更改的文档部分。

您可以将“there”视为由`<asp:label>`服务器控件显示的默认文本。事实上，如果您喜欢使用自结束标记，您可以将文本(' there ')指定为属性:

```
<p>Hello <**asp:label runat="server" id="NameLabel" text="there" /**>!</p>
```

有了我们的 HTML 代码，让我们继续服务器端脚本。

##### 处理简单表单

在 ASP.NET，表单提交的工作方式与您可能习惯的略有不同。ASP.NET 表单提交回同一个页面，而不是提交到在`<form>`标签的 action 属性中指定的不同页面。虽然您可以选择将浏览器发送到不同的页面来响应表单提交，但是带有表单的页面确实需要以某种方式处理提交。

以下是目前为止我们页面的代码:

```
<%@ Page Language="C#" %>   

<html>   

<head>   

<title>My First ASP.NET Form</title>   

<**script runat="server">   

  protected void Page_Load(Object Source, EventArgs E)   

  {   

    TimeLabel.Text = DateTime.Now.ToString();   

  }**   

<**/script**>   

</head>   

<body>   

<p>Hello <asp:label runat="server" id="NameLabel">   

there</asp:label>!</p>   

<form runat="server" id="NameForm">   

  <p>Do you have a name?</p>   

  <p><asp:textbox runat="server" id="NameBox" />   

     <input type="submit" value="Submit" /></p>   

</form>   

<p>The time is now: <asp:label runat="server" id="TimeLabel" /></p>   

</body>   

</html>
```

现在，我们的服务器端脚本(上面用粗体突出显示)只是设置了`TimeLabel <asp:label>`标记，以便在页面加载时显示当前时间。我们需要扩展这个脚本，这样当用户提交表单时，会发生三件事:

*   该表单被隐藏，因为不再需要它。
*   `NameLabel <asp:label>`标签的文本被设置为用户提供的名称
*   用户名以粗体显示。

在`Page_Load`脚本中，我们可以通过检查页面的`IsPostBack`属性来确定页面是否是由于表单提交而被加载的。你看，当一个表单被发布(提交)回包含它的同一个页面时，这叫做回发。ASP.NET 自动提供一个名为`IsPostBack`的属性，当页面由于回发而被加载时为真，否则为假。

因此，如果我们使用 C#作为页面的 ASP.NET 语言，我们扩展的`Page_Load`脚本将采用以下形式:

```
<script runat="server">   

  protected void Page_Load(Object Source, EventArgs E)   

  {   

    **if (IsPostBack)   

    {   

      // Form processing code here ...   

    }**   

    TimeLabel.Text = DateTime.Now.ToString();   

  }   

</script>
```

粗体部分是一个 *if 语句*。出现在大括号之间的代码只有在括号之间的条件为真时才会被执行。因此，只有当回发导致页面加载时，才会执行表单处理代码。注意，因为设置文本`TimeLabel`的代码在 if 语句之外，所以它会在每次加载页面时发生——不仅仅是回发。

顺便说一句，在 C#中，以//开头的行将被忽略。这些被称为注释，让你写一些小笔记来提醒自己代码是如何工作的。在这些文章中，我将使用注释来帮助你理解上面的代码。

如果您喜欢 VB.NET，它也有一个 If 语句，尽管语法有些不同:

```
<script runat="server">   

  Sub Page_Load(Source As Object, E As EventArgs)   

    **If IsPostBack Then   

      ' Form processing code here ...   

    End If**   

    TimeLabel.Text = DateTime.Now.ToString()   

  End Sub   

</script>
```

你还会注意到，VB.NET 的评论以撇号(')开头。

好了，现在我们可以写代码，当页面是回发的结果时会发生什么。随着您对 ASP.NET 知识的提高，您将能够将提交的值存储到数据库中，发送包含表单中的值的电子邮件，或者检查用户是否被允许访问您的站点。然而，现在让我们为我们的例子要做的三件简单的事情添加代码。

首先，我们需要隐藏表单。因为我们在页面的 HTML 代码中为表单分配了一个 id`NameForm`,所以我们可以很容易地做到这一点:

```
NameForm.Visible = false;
```

接下来，我们需要用`id="NameLabel"`设置`<asp:label>`标签来显示用户提交的名称。我们已经知道如何设置一个`<asp:label>`标签的文本，但是我们如何获得用户提交的名字呢？嗯，由于用户用`id="NameBox"`将值输入到`<asp:textbox>`中，因此名称可以作为`NameBox.Text`来访问。因此，为了显示用户名，我们只需编写:

```
NameLabel.Text = NameBox.Text;
```

最后，我们想让用户的名字以粗体显示，让他或她感觉更重要，并突出我们的页面有多聪明！为此，我们将为显示用户名的`NameLabel`元素分配一个级联样式表(CSS)属性。这是如何做到的:

```
NameLabel.Style.Add( "font-weight", "bold" );
```

lines 中这三行代码实际上与我们刚刚看到的 C#版本相同；只需去掉每行末尾的分号。

我们用 C#完成的页面如下:

```
<%@ Page Language="C#" %>    

<html>    

<head>    

<title>My First ASP.NET Form</title>    

<script runat="server">    

  protected void Page_Load(Object Source, EventArgs E)    

  {    

    if (IsPostBack)    

    {    

      NameForm.Visible = false;    

      NameLabel.Text = NameBox.Text;    

      NameLabel.Style.Add( "font-weight", "bold" );    

    }    

    TimeLabel.Text = DateTime.Now.ToString();    

  }    

</script>    

</head>    

<body>    

<p>Hello <asp:label runat="server" id="NameLabel">there    

</asp:label>!</p>    

<form runat="server" id="NameForm">    

  <p>Do you have a name?</p>    

  <p><asp:textbox runat="server" id="NameBox" />    

     <input type="submit" value="Submit" /></p>    

</form>    

<p>The time is now: <asp:label runat="server" id="TimeLabel" /></p>    

</body>    

</html>
```

这是 VB.NET 的版本:

```
<%@ Page Language="VB" %>    

<html>    

<head>    

<title>My First ASP.NET Form</title>    

<script runat="server">    

  Sub Page_Load(Source As Object, E As EventArgs)    

    If IsPostBack Then    

      NameForm.Visible = false    

      NameLabel.Text = NameBox.Text    

      NameLabel.Style.Add( "font-weight", "bold" )    

    End If    

    TimeLabel.Text = DateTime.Now.ToString()    

  End Sub    

</script>    

</head>    

<body>    

<p>Hello <asp:label runat="server" id="NameLabel">there    

</asp:label>!</p>    

<form runat="server" id="NameForm">    

  <p>Do you have a name?</p>    

  <p><asp:textbox runat="server" id="NameBox" />    

     <input type="submit" value="Submit" /></p>    

</form>    

<p>The time is now: <asp:label runat="server" id="TimeLabel" /></p>    

</body>    

</html>
```

保存这些文件并将其放在您的 Web 服务器上。在浏览器中加载页面，您将看到预期的表单:

![Submission Form](img/f97f88301ca06fca273ed4d02c225611.png)

键入您的姓名并提交表单，您将收到您的个性化问候！

![Personalised Greeting](img/28bd819d3785fff77773092f3ac1cc01.png)

请务必花一点时间使用浏览器的查看源代码功能来检查页面每个版本的 HTML 代码。注意表单中的`<asp:textbox>`元素是如何作为普通的`<input type="text">`标签发送到浏览器的。还要注意，一旦提交了姓名，表单就完全消失了，并观察生成的使姓名加粗的样式属性。

您可能不理解的一部分代码是 ASP.NET 添加到表单中的隐藏的`__VIEWSTATE`字段。这实际上是一个非常令人兴奋的 ASP.NET 特性，但是需要一些解释才能理解，所以您必须等到本系列的后续文章才能了解它。

##### 敬请关注…

在我的下一篇文章中，我将揭开一些面向对象编程概念的神秘面纱，你需要理解这些概念来充分利用。NET 平台，包括 ASP.NET。我将向您介绍类、对象、属性和方法，然后解释继承的概念。最后，我们将使用这个概念来创建一个 ASP.NET 页面，其中所有的 C#或 VB.NET 代码都放在一个单独的文件中，称为代码隐藏文件。

一定要给这篇文章打分，让我知道你的想法！

## 分享这篇文章