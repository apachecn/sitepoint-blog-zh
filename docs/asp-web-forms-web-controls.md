# 使用 C#和 VB.NET 4 建立你自己的 ASP.NET 网站

> 原文：<https://www.sitepoint.com/asp-web-forms-web-controls/>

**你可能已经从我们在[上一章](https://www.sitepoint.com/vb-dot-net-c-sharp-programming/)中的工作中意识到，ASP.NET 的核心是它创建动态表单内容的能力。无论您是创建一个复杂的购物车应用程序，还是创建一个收集用户信息并通过电子邮件发送结果的简单页面，Web 表单都有一个解决方案。它们允许您使用 HTML 控件和 Web 控件来创建用户可以与之交互的动态页面。在这一章中，你将学习到 Web 表单、HTML 控件和 Web 控件，以及 VB.NET 和 C#代码如何改变你看待和开发 Web 的方式。在这一章中，我将向你介绍以下概念:**

*   HTML 控件
*   Web 表单
*   Web 控件
*   处理页面导航
*   用 CSS 格式化控件

在本章的末尾，您将把所有这些概念应用到现实世界的应用程序中！我将介绍您将在本书中构建的 Dorknozzle Intranet 应用程序，并看看您在本章中学到的知识如何应用到项目的一些页面中。请记住，如果你想打印出来离线阅读，你可以[下载 PDF 格式的章节](https://www.sitepoint.com/books/aspnet1/signoff.php)。

##### 使用 HTML 控件

HTML 控件表面上与普通的老式 HTML 4.0 标签相同，但是使用了`runat="server"`属性。对于每一个 HTML 最常见的标签，都有一个相应的服务器端 HTML 控件，尽管微软为每个标签添加了一些标签和一些额外的属性。创建 HTML 控件很容易——我们只需在普通 HTML 标签的末尾添加一个`runat="server"`属性来创建该标签的 HTML 控件版本。表 4.1 给出了当前 HTML 控件类及其相关标签的完整列表。

这些 HTML 控件类都包含在`System.Web.UI.HtmlControls`名称空间中。

因为 HTML 控件是由 ASP.NET 运行时在服务器端处理的，所以我们可以通过页面中其他地方的代码轻松访问它们的属性。如果您熟悉 JavaScript、HTML 和 CSS，那么您会知道在 HTML 标记中操作文本，或者甚至在 HTML 标记中操作内联样式，可能会很麻烦并且容易出错。HTML 控件旨在解决这个问题，它允许您通过选择。NET 语言，例如，使用 VB.NET 或 C#。我们将从查看 HTML 控件库开始，然后当我们处理一个包含 HTML 控件和代码的简单表单时，我们将更详细地探索控件公开的属性。

**表 4.1。HTML 控件类**

![1329_table4.1](img/9e8ba8baab89f1a2cc6c8b0422877248.png)

**T2`HtmlAnchor`**

`HtmlAnchor`控件创建一个服务器端 HTML `<a href="...">`标签。

```
<a href="somepage.aspx" runat="server">Click Here</a>
```

这一行将创建一个带有文本“单击此处”的新超链接一旦链接被点击，用户将被重定向到 href 属性给出的`somepage.aspx`。

**T2`HtmlButton`**

`HtmlButton`控件创建一个服务器端 HTML `<button>`标签。

```
<button id="myButton" OnServerClick="Click" runat="server">Click  

Here</button>
```

请注意，我们在这里使用事件。在 HTML 控件上，我们需要使用`OnServerClick`来指定按钮上的点击的 ASP.NET 处理程序，因为 onclick 是为在客户端用 JavaScript 处理点击而保留的。在本例中，处理程序子例程被称为`Click`，它将在一个脚本块中声明，其形式与我们之前查看的`<asp:Button>`标签的`Click`处理程序相同:

```
<script runat="server" language="VB"> 

Sub Click(s As Object, e As EventArgs) 

  Response.Write(myButton.ID) 

End Sub 

</script> 

<script runat="server" language="C#"> 

void Click(Object s, EventArgs e) { 

  Response.Write(myButton.ID); 

} 

</script>
```

在这种情况下，当用户点击按钮时，会引发`ServerClick`事件，调用`Click()`子例程对其进行处理，并用`Response.Write()`(`Response`对象的`Write()`方法)将`HtmlButton`控件的 ID 写到屏幕上。

**T2`HtmlForm`**

`HtmlForm`控件创建一个服务器端的`<form>`标签。大多数 HTML 控件、Web 控件等。，必须放在一个`HtmlForm`控件内。

```
<form runat="server"> 

<!-- ASP.NET controls in here --> 

</form>
```

**T2`HtmlImage`**

`HtmlImage`控件创建一个服务器端的`<img>`标签。下面的代码展示了我们如何在页面上放置一个`HtmlImage`控件和一个`HtmlButton`:

```
<img id="myimage" src="arrow.gif" runat="server" /> 

<button id="myButton" runat="server" OnServerClick="Click">Click  

Here</button>
```

如果我们添加如下代码，用户可以通过按下按钮来动态更改此图像:

```
<script runat="server" language="VB"> 

Sub Click(s As Object, e As EventArgs) 

  myimage.Src = "welcome.gif" 

End Sub 

</script> 

<script runat="server" language="C#"> 

void Click(Object s, EventArgs e) { 

  myimage.Src = "welcome.gif"; 

} 

</script>
```

如果这些控件和脚本块一起放在一个页面上会发生什么？首先会出现图像`arrow.gif`。当点击`HtmlButton`控件时，它会变成`welcome.gif`。在幕后，当点击按钮时会引发`ServerClick`事件，从而调用`Click()`子例程，并将`HtmlImage`控件的 Src 属性从`arrow.gif`更改为`welcome.gif`。

**T2`HtmlGenericControl`**

`HtmlGenericControl`为没有关联 HTML 控件的 HTML 标签创建一个服务器端控件。最好的例子就是`<span>`和`<div>`标签。下面的例子说明了如何修改`<span>`标签中的文本，将内容从`I like ASP.NET`动态地更改为`Why would anyone need PHP?`。

```
<span id="myGenericControl" runat="server">I like ASP.NET</span> 

<br /> 

<button id="myButton" runat="server" OnServerClick="Click">Click  

Here</button>
```

我们只需添加以下代码来响应`ServerClick`事件并更改文本:

```
<script runat="server" language="VB"> 

Sub Click(s As Object, e As EventArgs) 

  myGenericControl.InnerText = "Why would anyone need PHP?" 

End Sub 

</script> 

<script runat="server" language="C#"> 

void Click(Object s, EventArgs e) { 

  myGenericControl.InnerText = "Why would anyone need PHP?"; 

} 

</script>
```

**T2`HtmlInputButton`**

`HtmlInputButton`控件创建一个服务器端的`<input type="submit">`、`<input type="reset">`或`<input type="button">` HTML 标签。

```
<input type="submit" value="Click Here" runat="server" /> 

<input type="reset" value="Click Here" runat="server" /> 

<input type="button" value="Click Here" runat="server" />
```

与`HtmlButton`一样，您可以使用`OnServerClick`属性将服务器端事件处理程序分配给这种类型的控件。

**T2`HtmlInputCheckBox`**

`HtmlInputCheckBox`控件创建一个服务器端的`<input type="checkbox">` HTML 标签。

```
<input type="checkbox" id="cb1" value="ASP.NET" runat="server" 

  />ASP.NET<br /> 

<input type="checkbox" id="cb2" value="PHP" runat="server" 

  />PHP<br /> 

<input type="checkbox" id="cb3" value="JSP" runat="server" 

  />JSP<br /> 

<input type="checkbox" id="cb4" value="CGI" runat="server" 

  />CGI<br /> 

<input type="checkbox" id="cb5" value="Coldfusion" runat="server" 

  />Coldfusion<br>
```

当您希望允许用户从列表中选择多个项目时，`HtmlInputCheckBox`控件是最佳选择。

**T2`HtmlInputFile`**

`HtmlInputFile`控件在 HTML 中创建一个服务器端的`<input type="file">`标签。这将显示一个文本框和浏览按钮，允许用户从 ASP.NET 页面上传文件。这个标签没有对应的 Web 控件，所以在处理文件上传时通常需要它——即使是 Web 表单(我们稍后将讨论)。

```
<input type="file" id="fileUpload" runat="server" />
```

**T2`HtmlInputHidden`**

`HtmlInputHidden`控件创建一个服务器端的`<input type="hidden">`标签。

```
<input type="hidden" id="hiddenField" runat="server" />
```

尝试从浏览器中查看任何一个 ASP.NET 页面的源代码，您很可能会发现这个标签被用来存储视图状态信息。

**T2`HtmlInputImage`**

`HtmlInputImage`控件创建一个服务器端的`<input type="image">`标签。

```
<input type="image" id="imgMap" runat="server" 

  src="ButtonImage.jpg" />
```

这个标签提供了对`HtmlInputButton`控件的替代。它们的功能是一样的。不同之处在于,`HtmlInputImage`控件使用自定义图像，而不是倾斜的灰色 Windows 风格按钮。当用户单击这种类型的控件时，鼠标坐标也会随表单提交一起发送。

**T2`HtmlInputRadioButton`**

`HtmlInputRadioButton`控件创建一个服务器端单选按钮。例如，下面的代码提供了男性或女性的选择:

```
Gender?<br /> 

<input type="radio" id="radio1" runat="server" />Male<br /> 

<input type="radio" id="radio2" runat="server" />Female
```

类似于`HtmlInputCheckBox`控件，`HtmlInputRadioButton`控件创建一个项目列表供用户选择。然而，区别在于用户一次只能选择一个项目。

**T2`HtmlInputText`**

`HtmlInputText`控件创建一个服务器端的`<input type="text">`或`<input type="password">`标签。

```
Please Login<br /> 

Username:<br /> 

<input type="text" id="username" runat="server" /><br /> 

Password:<br /> 

<input type="password" id="password" runat="server" />
```

前面的代码创建了一个典型的登录屏幕布局。

**T2`HtmlSelect`**

`HtmlSelect`控件创建一个服务器端版本的`<select>`标签，用于创建下拉列表或列表框。下面的代码创建了一个下拉菜单:

```
Select your favorite movie:<br /> 

<select id="selectMovie" runat="server"> 

<option>Star Wars</option> 

<option>Spider Man</option> 

<option>The Godfather</option> 

<option>Lord of the Rings</option> 

</select>
```

下面的代码创建了一个多选列表框:

```
Which of these movies do you like?<br /> 

<select id="selectMovie" runat="server" multiple="true" size="4"> 

<option>Star Wars</option> 

<option>Spider Man</option> 

<option>The Godfather</option> 

<option>Lord of the Rings</option> 

</select>
```

您会注意到主`<select>`标签中的`<option>`标签；这用于表示出现在列表框或下拉菜单中的每个项目。

**`HtmlTable`、`HtmlTableRow`和`HtmlTableCell`**

`HtmlTable`、`HtmlTableRow`和`HtmlTableCell`控件创建了`<table>`、`<tr>`、`<td>`和`<th>`标签的服务器端版本。以下代码创建一个服务器端表:

```
<table id="myTable" border="1" cellspacing="0" cellpadding="0"  

runat="server">  

<tr runat="server" id="row1"> 

<td runat="server" id="cell1">Table Data 1</td> 

<td runat="server" id="cell2">Table Data 2</td> 

</tr> 

<tr runat="server" id="row2"> 

<td runat="server" id="cell3">Table Data 3</td> 

<td runat="server" id="cell4">Table Data 4</td> 

</tr> 

</table> 

<button id="myButton" OnServerClick="Click" runat="server">Click  

Here</button>
```

您可以添加以下代码来响应由`HtmlButton`控件引发的`Click`事件，并将第一个单元格的内容更改为“Hello World”

```
<script runat="server" language="VB"> 

Sub Click(s As Object, e As EventArgs) 

  cell1.InnerText = "Hello World" 

End Sub 

</script> 

<script runat="server" language="C#"> 

void Click(Object s, EventArgs e) { 

  cell1.InnerText = "Hello World"; 

} 

</script>
```

**T2`HtmlTextArea`**

`HtmlTextArea`控件创建了一个服务器端版本的`<textarea>`标签。

```
<textarea cols="60" rows="10" runat="server"></textarea>
```

我们只是简单浏览了一下 HTML 控件，因为根据您的 HTML 经验，它们应该都很熟悉。但是如果你想知道更多关于 HTML 控件的信息，包括每个控件的属性、方法和事件，请看*附录 A，HTML 控件参考*。

##### 处理简单表单

现在你已经对 ASP.NET 页面结构、VB.NET 和 C#语言以及 HTML 控件有了基本的了解，让我们把所有的东西放在一起，创建一个简单的 ASP.NET 应用程序。我们将在 VB.NET 和 C#中创建的应用程序将是一个简单的调查表，它使用以下 HTML 控件:

*   `HtmlForm`
*   `HtmlButton`
*   `HtmlInputText`
*   `HtmlSelect`

让我们从在您最喜欢的代码编辑器中创建新文件开始。以下代码为调查创建了可视界面:

**例 4.1。`SimpleForm.aspx`(节选)**

```
<html>  

<head>  

...  

</head>  

<body>  

<form runat="server">  

  <h2>Take the Survey!</h2>  

  <p>Name:<br />  

  <input type="text" id="txtName" runat="server" /></p>  

  <p>Email:<br />  

  <input type="text" id="txtEmail" runat="server" /></p>  

  <p>Which server technologies do you use?<br />  

  <select id="servermodel" runat="server" multiple="true">  

    <option>ASP.NET</option>  

    <option>PHP</option>  

    <option>JSP</option>  

    <option>CGI</option>  

    <option>Coldfusion</option>  

  </select></p>  

  <p>Do you like .NET so far?<br />  

  <select id="likedotnet" runat="server">  

    <option selected>Yes</option>  

    <option>No</option>  

  </select></p>  

  <p><button id="myButton" OnServerClick="Click" runat="server">  

  Confirm</button></p>  

</form>  

</body>  

</html>
```

从我们已经介绍的 HTML 控件中，你应该对这个页面的外观有一个很好的想法。我们所做的就是将一些`HtmlInputText`控件、一个`HtmlButton`控件和一个`HtmlSelect`控件放入强制`HtmlForm`控件中。记住，HTML 控件本质上只是带有`runat="server"`属性的 HTML 标签。完成后，界面将如图 4.1 所示。

**图 4.1。使用 HTML 控件创建 ASP.NET 页面的界面。**

![1329_fig1](img/d76e14d11bd8bebdd7c4189b01b2672b.png)

当用户点击按钮时，我们将简单地在他们的浏览器中显示他们的响应。在实际的应用程序中，我们可能更倾向于将它保存到数据库中，并以图表的形式显示结果。无论是哪种情况，我们都会访问 HTML 控件的属性，如下面的代码所示:

**例 4.2。`SimpleForm.aspx`(节选)**

```
<script runat="server" language="VB">  

Sub Click(s As Object, e As EventArgs)  

  Response.Write("Your name is: " & txtName.value & "<br />")  

  Response.Write("Your email is: " & txtEmail.value & "<br />")  

  Response.Write("You like to work with: " & servermodel.value & _  

    "<br />")  

  Response.Write("You like .NET: " & likedotnet.value)  

End Sub  

</script>
```

**例 4.3。`SimpleForm.aspx`(节选)**

```
<script runat="server" language="C#">  

void Click(Object s, EventArgs e) {  

  Response.Write("Your name is: " + txtName.Value + "<br />");  

  Response.Write("Your email is: " + txtEmail.Value + "<br />");  

  Response.Write("You like to work with: " + servermodel.Value +  

    "<br />");  

  Response.Write("You like .NET: " + likedotnet.Value);  

}  

</script>
```

正如你在前面章节的例子中看到的，我们将 VB.NET 和 C#代码放在页面的`<head>`部分的服务器端脚本块中。接下来，我们创建一个新的`Click`事件处理程序，它接受两个常用参数。最后，我们使用`Response`对象的`Write()`方法打印出页面中用户的响应。

一旦你写了代码，你可以保存你的工作并从你的浏览器测试结果。输入一些信息，然后单击按钮。当点击按钮时，您输入的内容应该出现在页面的顶部。

##### Web 窗体简介

随着新技术的出现，总会有新的术语需要掌握。ASP.NET 也不例外。有了 ASP.NET，以前用来描述网页的最简单的术语也发生了变化，以反映网页中发生的过程。在我们开始描述 Web 表单所遵循的过程之前，让我们讨论一下 Web 页面的基础概念。

在最基本的层面上，*网页*是包含标记的文本文件。网页应该在浏览器窗口中查看，浏览器窗口解析包含标记的文件，以开发人员设想的布局向用户显示信息。网页可以包括文本、视频、声音、动画、图形，甚至是来自各种技术的“代码”块。

正如您在前面几节中了解到的，HTML 表单是包含一个或多个表单元素的页面，这些元素组合在一个 HTML `<form>`标签中。用户与各种表单元素进行交互，以做出某些选择或提供某些信息；单击提交按钮后，这些信息将被发送到服务器进行处理。这对我们这些 ASP.NET 开发者来说很有用，因为常规的 HTML 表单有一个内置的机制，允许表单提交到服务器。表单提交后，服务器上需要某种额外的技术——在本例中是 ASP.NET——来执行实际的表单处理。

在 ASP.NET，我们称网页*为网络表单*；它们包含 HTML 表单中的表示元素(ASP.NET Web 控件)，以及我们为页面的动态特性添加的任何代码(处理逻辑)。

典型的 Web 表单如图 4.2 所示。

**图 4.2。Web 窗体包含用于处理逻辑的代码和用于表示目的的 Web 控件。**

![1329_fig2](img/9d49bfdb2bd9eff0a02a2e3c96022931.png)

下一节将介绍各种 Web 控件以及如何在 Web 表单中使用它们。它们在外观上与 HTML 非常相似，所以你应该很容易掌握它们。

##### Web 控件简介

正如我们刚刚看到的，Web 表单允许用户使用 *Web 控件*与我们的站点进行交互。有了 Web 控件，微软基本上从零开始重新发明了 HTML。例如，它创建了两个不同的 Web 控件，对应于两个不同版本的 HTML `<select>`标签:一个`DropDownList`控件和一个`ListBox`控件。这意味着在 Web 控件和标准 HTML 标签之间没有直接的一对一的对应关系，就像 HTML 控件一样。Web 控件遵循与 HTML 标签相同的基本模式，但是标签名称以`asp:`开头，并且名称使用“CamelCasing”大写。考虑 HTML `<input>`标签，它在屏幕上创建一个输入文本框:

```
<input type="text" name="username" size="30" />
```

等效的 Web 控件是`TextBox`控件，如下所示:

```
<asp:TextBox id="username" Columns="30" runat="server">  

</asp:TextBox>
```

注意，与许多 HTML 标签不同，Web 控件总是需要一个结束标签(上面的`</asp:TextBox>`部分)。如果我们的 Web 控件标签在其开始和结束标签之间不包含任何内容，我们也可以使用简写的`/>`语法。所以，我们也可以把这个`TextBox`写成这样:

```
<asp:TextBox id="username" Columns="30" runat="server" />
```

总而言之，使用 Web 控件时要记住的要点是:

*   所有 Web 控件必须放在`<form runat="server">/#epc#/ tag to function properly.`中
*   所有 Web 控件都需要`id`和`runat="server"`属性才能正常工作。
*   所有的 Web 控件都遵循相同的模式，但是不同的控件有不同的属性。
*   它们都以前缀`asp`开头，后面跟一个冒号。

Web 控件比 HTML 控件多，有些控件提供了 HTML 所没有的高级功能。我们将在本章和后续章节中讨论的控件如下:

*   基本 Web 控件(*第 4 章，Web 表单和 Web 控件*)
*   验证 Web 控件(*第 5 章，验证控件*)
*   数据控件(*第 9 章，数据网格和数据列表控件*)
*   用户控件(*第十六章，丰富控件和用户控件*)
*   丰富控件(*第 16 章，丰富控件和用户控件*)

##### 基本 Web 控件

基本 Web 控件执行网页的屏幕布局，并在许多方面反映了基于常规 HTML 的 HTML 控件。然而，它们提供了一些新的改进和增强，应该尽可能地用它们来代替 HTML。在本节中，我们将了解该组中的控件，即:

*   `Label`
*   `TextBox`
*   `Button`
*   `Image`
*   `ImageButton`
*   `LinkButton`
*   `HyperLink`
*   `RadioButton`
*   `RadioButtonList`
*   `CheckBox`
*   `CheckBoxList`
*   `DropDownList`
*   `ListBox`
*   `Panel`
*   `PlaceHolder`

**T2`Label`**

在页面上显示静态文本的最简单的方法就是将文本添加到页面的正文中，而不要用任何标签将它括起来。但是，如果您想修改 ASP.NET 代码页面上显示的文本，您可以在一个`Label`控件中显示您的文本。这里有一个典型的例子:

```
<asp:Label id="lblMessage" Text="" runat="server" />
```

下面的代码设置`Label`控件的 Text 属性以显示文本“Hello World”:

```
Public Sub Page_Load()  

  lblMessage.Text = "Hello World"  

End Sub  

public void Page_Load() {  

  lblMessage.Text = "Hello World";  

}
```

阅读这个`Page_Load()`处理程序代码，我们可以看到当页面第一次加载时，ID 为`lblMessage`的`Label`控件的文本属性将被设置为“Hello World”

**T2`TextBox`**

`TextBox`控件用于在屏幕上创建一个框，用户可以在其中键入或阅读标准文本。使用`TextMode`属性，可以将这个 Web 控件设置为显示标准的 HTML 文本输入字段、HTML 密码字段或 HTML 文本区域。下面的代码展示了我们如何在一个简单的登录页面中使用它:

```
<p>Username:  

<asp:TextBox id="txtUser" TextMode="SingleLine" Columns="30"   

    runat="server" /></p>  

<p>Password:  

<asp:TextBox id="txtPassword" TextMode="Password" Columns="30"   

    runat="server" /></p>  

<p>Comments:  

<asp:TextBox id="txtComments" TextMode="MultiLine" Columns="30"   

    Rows="10" runat="server" /></p>
```

在上述三个实例中，属性`TextMode`决定了要呈现的文本框的类型。

**T2`Button`**

默认情况下，`Button`控件呈现的表单提交按钮与 HTML `<input type="Submit">`标签呈现的按钮相同。当单击一个按钮时，包含该按钮的表单被提交给服务器进行处理，同时引发 click 和 command 事件。下面的代码显示了一个`Button`控件和一个`Label`:

```
<asp:Button id="btnSubmit" Text="Submit" runat="server"   

    OnClick="WriteText" />  

<asp:Label id="lblMessage" runat="server" />
```

注意控件上的`OnClick`属性。与`HtmlButton` HTML 控件不同，`OnClick`分配了一个*服务器端*事件处理程序——不需要记住使用`OnServerClick`。当点击按钮时，将引发 Click 事件并调用`WriteText()`子例程。`WriteText()`子例程将包含执行该按钮预期功能的代码，例如为用户显示一条消息:

```
Public Sub WriteText(s As Object, e As EventArgs)  

  lblMessage.Text = "Hello World"  

End Sub  

public void WriteText(Object s, EventArgs e) {  

  lblMessage.Text = "Hello World";  

}
```

重要的是要认识到大多数 Web 控件都有与之相关联的事件，基本思想和技术与`Button`控件的`Click`事件相同。

**T2`Image`**

一个`Image`控件在页面上放置了一个可以从代码中动态访问的图像；它相当于 HTML 中的`<img>`标签。这里有一个例子:

```
<asp:Image id="myImage" ImageUrl="mygif.gif" runat="server"  

    AlternateText="description" />
```

**T2`ImageButton`**

一个`ImageButton`控件类似于一个`Button`控件，但是它使用你提供的图像来代替典型的灰色窗口样式的按钮。例如:

```
<asp:ImageButton id="myImgButton" ImageUrl="myButton.gif"  

    runat="server" />
```

**T2`LinkButton`**

一个`LinkButton`控件在你的页面上呈现一个超链接。从 ASP.NET 电码的角度来看，`LinkButtons`可以像按钮一样处理，因此得名。

```
<asp:LinkButton id="myLinkButon" Text="Click Here" runat="server"  

    />
```

**T2`HyperLink`**

类似于`LinkButton`控件的`HyperLink`控件在页面上创建一个超链接。它比`LinkButton`更简单，处理速度也更快，但是不像`LinkButton`控件，它提供了诸如`Click`事件和验证的特性，`HyperLink`只能用于点击和从一个页面导航到下一个页面。

```
<asp:HyperLink id="myLink" NavigateUrl="http://www.example.com/"   

    ImageUrl="myButton.gif" runat="server">My Link</asp:HyperLink>
```

如果指定了`ImageUrl`属性，它会使控件显示一个链接的图像，而不是所提供的文本。

**T2`RadioButton`**

您可以使用`RadioButton`控件将单个单选按钮逐个添加到页面中。使用 GroupName 属性将单选按钮组合在一起。一次只能从每组中选择一个`RadioButton`控件。

```
<asp:RadioButton id="radSanDiego" GroupName="City"  

    Text="San Diego" runat="server" />  

<asp:RadioButton id="radBoston" GroupName="City" Text="Boston"   

    runat="server" />  

<asp:RadioButton id="radPhoenix" GroupName="City" Text="Phoenix"   

    runat="server" />  

<asp:RadioButton id="radSeattle" GroupName="City" Text="Seattle"   

    runat="Server" />
```

与`RadioButtons`关联的主事件是`CheckChanged`事件；这可以用`OnCheckChanged`属性来处理。

**T2`RadioButtonList`**

像`RadioButton`控件一样，`RadioButtonList`控件代表单选按钮。然而，`RadioButtonList`控件表示一个单选按钮列表，并使用更紧凑的语法。这里有一个例子:

```
<asp:RadioButtonList id="radlFavColor" runat="server">  

  <asp:ListItem Text="Red" Value="red" />  

  <asp:ListItem Text="Blue" Value="blue" />  

  <asp:ListItem Text="Green" Value="green" />  

</asp:RadioButtonList>
```

`RadioButtonList`的一个重要特性是它能够将绑定到数据源上进行*。例如，假设您在数据库中有一个雇员列表。您可以创建一个页面，将数据库中的选择绑定到`RadioButtonList`控件，以动态列出控件中的某些雇员。然后，用户将能够从该列表中选择一个(且只能选择一个)雇员，我们的代码可以决定选择。*

由`RadioButtonList`产生的最有用的事件是`SelectedIndexChanged`事件，您可以用`OnSelectedIndexChanged`属性为其分配一个处理程序。

**T2`CheckBox`**

您可以使用一个`CheckBox`控件来表示一个只能是 yes(选中)或 no(未选中)值的选择。

```
<asp:CheckBox id="chkQuestion" Text="I like .NET!" runat="server"  

  />
```

与`RadioButton`控件一样，与`CheckBox`相关联的主事件是`CheckChanged`事件；这可以用`OnCheckChanged`属性来处理。

**T2`CheckBoxList`**

您可能已经猜到了，`CheckBoxList`控件代表一组复选框；相当于在 row 中使用了几个`CheckBox`控件:

`<asp:CheckBoxList id="chklFavDrinks" runat="server">  
 <asp:ListItem Text="Pizza" Value="pizza" />  
 <asp:ListItem Text="Tacos" Value="tacos" />  
 <asp:ListItem Text="Pasta" Value="pasta" />  
</asp:CheckBoxList>`

像`RadioButtonList`控件一样，`CheckBoxList`控件能够绑定到数据源，并产生一个可以用`OnSelectedIndexChanged`处理的`SelectedIndexChanged`事件。

**T2`DropDownList`**

一个`DropDownList`控件类似于 HTML `<select>`标签。`DropDownList`控件允许您使用下拉菜单从列表中选择一个项目。

```
<asp:DropDownList id="ddlFavColor" runat="server">  

  <asp:ListItem Text="Red" value="red" />  

  <asp:ListItem Text="Blue" value="blue" />  

  <asp:ListItem Text="Green" value="green" />  

</asp:DropDownList>
```

与其他基于集合的控件一样，如`CheckBoxList`和`RadioButtonList`控件，`DropDownList`控件可以绑定到数据库，从而允许您将动态内容提取到下拉菜单中。如你所料，这个控件产生的主事件是`SelectedIndexChanged`，用`OnSelectedIndexChanged`处理。

**T2`ListBox`**

一个`ListBox`控件等同于 HTML `<select>`标签，其`size`属性设置为 2 或更多。`ListBox`控件允许您从多行菜单中选择项目。如果您将`SelectionMode`属性设置为`Multiple`，用户将能够从列表中选择多个项目，如下例所示:

```
<asp:ListBox id="listTechnologies" runat="server"  

    SelectionMode="Multiple">  

  <asp:ListItem Text="ASP.NET" Value="aspnet" />  

  <asp:ListItem Text="JSP" Value="jsp" />  

  <asp:ListItem Text="PHP" Value="php" />  

  <asp:ListItem Text="CGI" Value="cgi" />  

  <asp:ListItem Text="Coldfusion" Value="cf" />  

</asp:ListBox>
```

同样，因为`ListBox`控件是基于集合的控件，所以它可以动态绑定到数据源。该控件提供的最有用的事件是——您猜对了——`SelectedIndexChanged`,以及相应的`OnSelectedIndexChanged`属性。

**T2`Panel`**

`Panel`控件的功能类似于 HTML 中的`<div>`标签，因为标签中的一组项目可以作为一个组来操作。例如，`Panel`可以通过`Button's Click`事件变得可见或隐藏:

```
<asp:Panel id="pnlMyPanel" runat="server">  

  <p>Username:  

    <asp:TextBox id="txtUsername" Columns="30" runat="server" />  

  </p>  

  <p>Password:  

    <asp:TextBox id="txtPassword" TextMode="Password"   

        Columns="30" runat="server" /></p>  

</asp:Panel>  

<asp:Button id="btnHide" Text="Hide Panel" OnClick="HidePanel"   

    runat="server" />
```

上面的代码在一个`Panel`控件中创建了两个`TextBox`控件。`Button`控件在面板之外。然后，`HidePanel()`子例程通过将`Panel`的 Visible 属性设置为`False`来控制其可见性:

```
Public Sub HidePanel(s As Object, e As EventArgs)  

  pnlMyPanel.Visible = False  

End Sub  

public void HidePanel(Object s, EventArgs e) {  

  pnlMyPanel.Visible = false;  

}
```

在这种情况下，当用户单击按钮时，会引发`Click`事件并调用`HidePanel()`子例程，该子例程会将`Panel`控件的`Visible`属性设置为`False`。

**T2`PlaceHolder`**

通过代码，`PlaceHolder`控件让我们可以随时在页面上的特定位置动态添加元素。

```
<asp:PlaceHolder id="phMyPlaceHolder" runat="server" />
```

下面的代码在占位符中动态添加了一个新的`HtmlButton`控件。

```
Public Sub Page_Load()  

  Dim btnButton As HtmlButton = New HtmlButton()  

  btnButton.InnerText = "My New Button"  

  phMyPlaceHolder.Controls.Add(btnButton)  

End Sub  

public void Page_Load() {  

  HtmlButton btnButton = new HtmlButton();  

  btnButton.InnerText = "My New Button";  

  phMyPlaceHolder.Controls.Add(btnButton);  

}
```

这就是我们对基本 Web 控件的快速浏览。有关 Web 控件的更多信息，包括每个控件的属性、方法和事件，请看*附录 B，Web 控件参考*。

##### 处理页面导航

网页之间的链接是网络的驱动力。如果没有链接，网络将仅仅是一个简单的基于页面的信息源。链接让我们只需轻轻一点，就能毫不费力地从一页跳到另一页；它们跨越了相关思想之间的鸿沟，而不受地理和政治的限制。本节重点介绍页面导航，使用:

*   `HyperLink`控件
*   导航对象及其方法

假设您创建了一个网站，允许您的用户在一个页面上选择项目。你可以把这个页面叫做`viewcatalog.aspx`。假设您有第二个页面，名为`viewcart.aspx`。一旦用户从`viewcatalog.aspx`选择了一个项目，你可能会想把他们直接链接到`viewcart.aspx`，这样他们就可以跟踪他们的订单。为了实现这一点，我们显然必须将信息从`viewcatalog.aspx`页面传递到`viewcart.aspx`页面。

***利用* `HyperLink` *控制***

`HyperLink`控件在页面上创建一个简单的 HTML 超链接。单击后，用户将被重定向到由 NavigateUrl 属性指定的页面。例如:

```
<asp:HyperLink id="hlAddToCart" NavigateUrl="viewcart.aspx"    

    runat="server" Text="View Cart" />
```

这里，`NavigateUrl`属性指定该链接指向名为`viewcart.aspx`的页面。图 4.3 显示了`HyperLink`控件是如何在浏览器中呈现的。

**图 4.3。超链接控件呈现类似于浏览器中的锚标记。**

![1329_fig3](img/1046da32be380ec2ca61c6b238d2bad1.png)

然而，一旦我们到达新页面，它就无法访问第一页的信息。如果我们需要为用户提供一些连续性的信息，我们需要别的东西。

***导航对象及其方法***

前面的示例呈现了一个类似于 HTML 锚标记的简单控件。然而，一旦链接被跟踪，我们就没有前一页的记录或它包含的任何数据(网络是一种无状态的技术)。

如果我们希望将信息从一个页面传递到下一个页面，我们可以使用下面列出的三种方法之一来创建页面之间的链接:

```
Response.Redirect()
```

从代码导航到第二页。这相当于使用`HyperLink`控件，但是允许我们动态地设置查询字符串的参数。

```
Server.Transfer()
```

结束当前 Web 窗体并开始执行新的 Web 窗体。这个方法只在用户导航到一个新的 Web 表单页面时有效(`.aspx`)。

```
Server.Execute()
```

在显示当前 Web 表单时开始执行新的 Web 表单。两种形式的内容在发送给浏览器的响应中组合在一起。同样，这个方法只在用户导航到 Web 表单页面时有效(`.aspx`)。

将用户从`viewcatalog.aspx`页面重定向到`viewcart.aspx`页面的最简单快捷的方法是使用`Reponse.Redirect()`:

```
Sub linkClk(s As Object, e As EventArgs)   

  Response.Redirect("viewcart.aspx")   

End Sub   

void linkClk(Object s, EventArgs e) {   

  Response.Redirect("viewcart.aspx");   

}
```

然后，您可以使用`LinkButton`控件调用这个子程序，如下所示:

```
<asp:LinkButton id="lbAddToCart" Text="Add To Cart"    

    OnClick="linkClk" runat="server"/>
```

这一次，当您单击`LinkButton`控件时，将引发`Click`事件，调用子例程，并以我们要链接的页面名称作为参数调用`Response.Redirect()`。这样，我们直接从代码重定向到新页面，而不是使用特定的标签。这使我们能够在*查询字符串*中将信息传递给新页面。

查询字符串是一个变量列表和它们各自的值，我们可以将它们附加到页面的 URL，允许我们从该页面的代码中检索这些变量和值。

举例来说，假设您有一个包含以下产品信息的下拉列表:

```
<p><asp:DropDownList id="ddlProducts" runat="server">   

  <asp:ListItem Text="Pants" />   

  <asp:ListItem Text="Shirt" />   

  <asp:ListItem Text="Hat" />   

  <asp:ListItem Text="Socks" />   

</asp:DropDownList></p>   

<p><asp:LinkButton id="lbAddToCart" Text="Add To Cart"    

    OnClick="linkClk" runat="server" /></p>
```

您用来处理链接点击的代码需要找到下拉列表中选定的项目，并将其附加到用户将被重定向到的 URL 的查询字符串中，如下所示:

```
Sub linkClk(s As Object, e As EventArgs)   

  Dim strQueryStr As String = "?Product=" & _   

    Server.UrlEncode(ddlProducts.SelectedItem.Text)   

  Response.Redirect("viewcart.aspx" & strQueryStr)   

End Sub   

void linkClk(Object s, EventArgs e) {   

  string strQueryStr = "?Product=" +   

    Server.UrlEncode(ddlProducts.SelectedItem.Text);   

  Response.Redirect("viewcart.aspx" + strQueryStr);   

}
```

请注意`Server.UrlEncode()`方法的使用，它将查询字符串值中不允许的字符(例如&)转换为浏览器可以理解的 URL 安全字符代码(例如%26)。向查询字符串中添加任意值时，应始终使用此方法。

当用户从下拉列表中选择一个项目并单击`LinkButton`控件时，将打开`viewcart.aspx`页面，所选产品将作为查询字符串的一个参数被追加。这如图 4.4 所示。

**图 4.4。将所选项追加到查询字符串中。**

![1329_fig4](img/f2624794a20a38de83af2ea7e32e9c9c.png)

现在您已经将产品传递到了`viewcart.aspx`页面，您必须从新页面的查询字符串中获取它。我们通过访问请求从查询字符串中获得变量。QueryString 集合，像这样:

```
Sub Page_Load()   

  lblResult.Text = Request.QueryString("Product")   

End Sub   

void Page_Load() {   

  lblResult.Text = Request.QueryString["Product"];   

}
```

这里，我们简单地显示了查询字符串参数`Product`的值，如图 4.5 所示。

**图 4.5。在一个`Page_Load`事件处理程序中设置 label 控件的 text 属性以接受新的参数值。**

![1329_fig5](img/d5ced5db0e5ed7202e44ca9da030bb31.png)

现在，当您选择一个产品并将其添加到购物车中时，结果会显示在重定向页面的一个标签上，标签的`id`为`lblResult`。当然，真正的产品目录和购物车还有很多内容，但在这一部分，我们发现了一个重要的构建模块。

##### 回发

回发可能会让新手感到困惑，因为尽管大多数 ASP.NET 开发人员知道它是什么，但他们似乎无法解释清楚。到目前为止，我们讨论的主题，如子例程、函数和事件，对大多数 Web 开发人员来说并不陌生。HTML 结合客户端 JavaScript 已经做了很多年了。但是，ASP.NET 与这种模型不同，因为它是服务器端的技术，而不是客户端的技术——页面上发生的事件由运行在服务器上的代码处理。为此，ASP.NET 使用回发机制。

当一个事件被触发时，例如，一个按钮被点击，或者网格中的一个项目被选择，页面被提交回服务器进行处理，同时提交的还有关于事件的信息和页面上任何预先存在的数据(通过视图状态)。我们称页面“回发到”服务器。这是一个需要掌握的强大概念，因为是回发让我们在服务器上而不是在客户端的浏览器上运行代码，是回发让我们的服务器代码知道下拉列表中的哪些项被选中，或者用户在文本框中键入了什么信息。

但是，如果您有多个填充了数据库数据的`DropDownList`控件，会发生什么呢？用户可以与这些`DropDownList`控件交互，反过来，我们可以根据他们从下拉菜单中选择的内容来设置页面中的某些选项。虽然这看起来是一个普通的任务，但是对于传统的 ASP 来说，它会产生相当大的开销。问题是，虽然从数据库绑定到下拉菜单的数据永远不会改变，但每次用户从下拉菜单中选择一个项目并进行回发时，都必须再次访问数据库以重新构建页面上每个下拉列表的内容。然而，这在 ASP.NET 不是问题。

在 ASP.NET 中，我们可以用`IsPostBack`属性检查回发，从而避免执行任何不必要的耗时任务。`IsPostBack`是一个页面级属性——这意味着它是页面本身的属性——我们通常在`Page_Load()`事件处理程序中使用它，只有在页面首次加载时才执行代码。考虑下面的例子:

**例 4.4。`PostBack.aspx`**

```
<html>   

<head>   

<script runat="server" language="VB">   

Sub Page_Load(s As Object, e As EventArgs)    

  lblMessage1.Text = Now()   

  If Not IsPostBack Then   

    lblMessage2.Text = Now()   

  End If   

End Sub   

</script>   

</head>   

<body>   

<form runat="server">   

  <p>Not Checking for postback:<br />   

    <asp:Label id="lblMessage1" runat="server" /></p>   

  <p>Checking for postback:<br />   

    <asp:Label id="lblMessage2" runat="server" /></p>   

  <p><asp:Button id="btnClick" Text="Click Me" runat="server" />   

  </p>   

</form>   

</body>   

</html>
```

**例 4.5。`PostBack.aspx`**

```
<html>   

<head>   

<script runat="server" language="C#">   

void Page_Load(Object s, EventArgs e) {   

  lblMessage1.Text = Convert.ToString(DateTime.Now);   

  if (!IsPostBack) {   

    lblMessage2.Text = Convert.ToString(DateTime.Now);   

  }   

}   

</script>   

</head>   

<body>   

<form runat="server">   

  <p>Not Checking for postback:<br />   

    <asp:Label id="lblMessage1" runat="server" /></p>   

  <p>Checking for postback:<br />   

    <asp:Label id="lblMessage2" runat="server" /></p>   

  <p><asp:Button id="btnClick" Text="Click Me" runat="server" />   

  </p>   

</form>   

</body>   

</html>
```

结果将类似于图 4.6。

**图 4.6。IsPostBack 属性检查以确保用户没有重新提交页面。**

![1329_fig6](img/f53d113331ba718efcde24afbb197b9b.png)

在这个例子中，`IsPostBack`检查意味着当点击`Button`控件时，第二个标签不会刷新。类似地，我们可以在`Page_Load()`子例程中使用`IsPostBack`，在每个用户的会话中设置一次数据库驱动的下拉菜单，使在线体验更加流畅，并使我们的应用程序更具可伸缩性。如果回发现在看起来有点混乱，不要担心——我们将在接下来的章节中更多地使用它，所以如果还没有，在几个更实际的例子之后它应该是有意义的。

##### 用 CSS 格式化控件

HTML 被有意设计成很少注意页面上特定项目是如何呈现的细节。解决这些复杂的问题，并根据用户机器的限制和优势定制输出，这就要靠个人浏览器了。虽然我们可以使用 HTML 标记来更改字体样式、大小、颜色等，但这种做法会导致冗长的代码和页面，以后很难重新设计它们的样式。

层叠样式表(CSS) 语言旨在提供现代网页设计者所寻求的控制度、灵活性和活力。这是一个被所有流行浏览器广泛支持的标准，至少在其最老的版本(CSS1)中是如此。

CSS 是 Web 开发人员的强大工具，因为它让我们能够在单个表单中创建一组样式，并将这些样式应用于我们网站中的所有页面。然后，所有的页面在相同的部分使用相同的字体、颜色和大小，给整个网站一种一致的感觉。不管我们的站点包含三个页面还是三百个页面，当我们改变样式表中的样式时，这些改变会立即应用到基于该样式表的所有页面。

***样式和样式表的类型***

有三种不同的方式将样式与特定网页的元素相关联。我已经提到了第一个，通常也是最好的一个，它是一个外部文件:

**外部文件**

通过将您的样式规则放在一个外部样式表中，您可以将这个文件链接到您想要使用那些*样式*的任何网页。这使得更新网站的整体外观变得轻而易举。

**文档宽度**

您可以将页面的样式规则放在页面 head 元素内的一个

**内嵌**

内联样式允许我们使用`style`属性为单个标签设置样式。例如，我们可以在常规 HTML 中创建一个带有`style`属性的文本框，该属性在文本框周围绘制一个边框，如下所示:

```
<input type="text"   

    style="border-style:groove" />
```

CSS 样式规则以两种方式之一创建应用于页面元素的样式(在某种程度上，这是 CSS 工作方式的简化视图。完整的故事请参考 [HTML Utopia:使用 CSS](https://www.sitepoint.com/books/css1/) 进行无表设计(SitePoint，ISBN 0-9579218-2-9)。

**类**

可以说，类是在页面中使用样式的最流行的方式，它允许您设置自定义样式，该样式将应用于任何具有与自定义样式名称相匹配的类属性的标记或控件。

**标签重定义**

重新定义标签会影响某些标准 HTML 标签的外观。例如，默认情况下，`<hr>`标签的宽度通常为 100%,但是您可以在 CSS 中重新定义标签的宽度为 50%。

无论是构建外部的、文档范围的还是内联的样式表，类的属性和标记重定义都使用相同的语法。要在外部样式表文件中创建一个类，可以使用以下语法:

```
.myClass {   

  font-family: arial;   

  font-size: 10pt;   

  color: red;   

}
```

这将被保存在一个扩展名为`.css`的文件中，比如`styles.css`，并通过文档的`<head>`标签中的下面一行链接到 Web 表单中:

```
<link href="styles.css" rel="stylesheet" />
```

类似地，要在整个文档的样式表中定义一个类，可以使用以下语法:

```
<head>   

<style type="text/css">   

  .myClass {   

    font-family: arial;   

    font-size: 10pt;   

    color: red;   

  }   

</style>   

</head>
```

使用内联样式时，请使用以下语法:

```
<span style="font-family: arial; font-size: 10pt; color: red;">My    

Stylized Text</span>
```

对于内联样式，只需用`style`属性将所有属性添加到标签中。上面，我们使用了`<span>`标签，但是其他标签的原理是一样的。

现在你已经对 CSS 背后的一些基本概念有了基本的了解，让我们看看可以在我们的 ASP.NET 应用程序中使用的不同类型的样式。

***样式属性***

使用样式表可以修改许多不同类型的属性。以下是常见类型的列表:

**字体**

此类别为您提供了格式化文本级别元素的能力，包括它们的字体、大小、装饰、粗细、颜色等。

**背景**

此类别允许您自定义对象和文本的背景。通过修改这些值，您可以控制颜色、图像以及是否要重复图像。

**块**

此类别允许您修改段落之间的间距、文本行之间的间距以及文本和单词之间的间距。

**框**

“框”类别提供了对表格的更改和自定义。如果需要修改表格、行或单元格的边框、填充、间距和颜色，可以修改此类别中的元素。

**边框**

此类别允许您在页面元素周围绘制不同颜色、样式和粗细的框。

**列表**

此类别允许您自定义有序列表和无序列表的创建方式。

**定位**

修改定位允许您自由移动和定位标签和控件。

这些类别提供了通常可以使用 CSS 修改的内容列表。随着本书的深入，许多类型的属性将变得显而易见。

***CSS class 属性***

一旦您在样式表中定义了一个类(无论是外部的还是内部的)，您将希望开始将该类与 Web 表单中的元素相关联。您可以将类与 ASP。non-commissioned officer 军士

**你可能已经从我们在[上一章](https://www.sitepoint.com/vb-dot-net-c-sharp-programming/)中的工作中意识到，ASP.NET 的核心是它创建动态表单内容的能力。无论您是创建一个复杂的购物车应用程序，还是创建一个收集用户信息并通过电子邮件发送结果的简单页面，Web 表单都有一个解决方案。它们允许您使用 HTML 控件和 Web 控件来创建用户可以与之交互的动态页面。在这一章中，你将学习到 Web 表单、HTML 控件和 Web 控件，以及 VB.NET 和 C#代码如何改变你看待和开发 Web 的方式。在这一章中，我将向你介绍以下概念:**

*   HTML 控件
*   Web 表单
*   Web 控件
*   处理页面导航
*   用 CSS 格式化控件

在本章的末尾，您将把所有这些概念应用到现实世界的应用程序中！我将介绍您将在本书中构建的 Dorknozzle Intranet 应用程序，并看看您在本章中学到的知识如何应用到项目的一些页面中。请记住，如果你想打印出来离线阅读，你可以[下载 PDF 格式的章节](https://www.sitepoint.com/books/aspnet1/signoff.php)。

##### 使用 HTML 控件

HTML 控件表面上与普通的老式 HTML 4.0 标签相同，但是使用了`runat="server"`属性。对于每一个 HTML 最常见的标签，都有一个相应的服务器端 HTML 控件，尽管微软为每个标签添加了一些标签和一些额外的属性。创建 HTML 控件很容易——我们只需在普通 HTML 标签的末尾添加一个`runat="server"`属性来创建该标签的 HTML 控件版本。表 4.1 给出了当前 HTML 控件类及其相关标签的完整列表。

这些 HTML 控件类都包含在`System.Web.UI.HtmlControls`名称空间中。

因为 HTML 控件是由 ASP.NET 运行时在服务器端处理的，所以我们可以通过页面中其他地方的代码轻松访问它们的属性。如果您熟悉 JavaScript、HTML 和 CSS，那么您会知道在 HTML 标记中操作文本，或者甚至在 HTML 标记中操作内联样式，可能会很麻烦并且容易出错。HTML 控件旨在解决这个问题，它允许您通过选择。NET 语言，例如，使用 VB.NET 或 C#。我们将从查看 HTML 控件库开始，然后当我们处理一个包含 HTML 控件和代码的简单表单时，我们将更详细地探索控件公开的属性。

**表 4.1。HTML 控件类**

![1329_table4.1](img/9e8ba8baab89f1a2cc6c8b0422877248.png)

**T2`HtmlAnchor`**

`HtmlAnchor`控件创建一个服务器端 HTML `<a href="...">`标签。

```
<a href="somepage.aspx" runat="server">Click Here</a>
```

这一行将创建一个带有文本“单击此处”的新超链接一旦链接被点击，用户将被重定向到 href 属性给出的`somepage.aspx`。

**T2`HtmlButton`**

`HtmlButton`控件创建一个服务器端 HTML `<button>`标签。

```
<button id="myButton" OnServerClick="Click" runat="server">Click  

Here</button>
```

请注意，我们在这里使用事件。在 HTML 控件上，我们需要使用`OnServerClick`来指定按钮上的点击的 ASP.NET 处理程序，因为 onclick 是为在客户端用 JavaScript 处理点击而保留的。在本例中，处理程序子例程被称为`Click`，它将在一个脚本块中声明，其形式与我们之前查看的`<asp:Button>`标签的`Click`处理程序相同:

```
<script runat="server" language="VB"> 

Sub Click(s As Object, e As EventArgs) 

  Response.Write(myButton.ID) 

End Sub 

</script> 

<script runat="server" language="C#"> 

void Click(Object s, EventArgs e) { 

  Response.Write(myButton.ID); 

} 

</script>
```

在这种情况下，当用户点击按钮时，会引发`ServerClick`事件，调用`Click()`子例程对其进行处理，并用`Response.Write()`(`Response`对象的`Write()`方法)将`HtmlButton`控件的 ID 写到屏幕上。

**T2`HtmlForm`**

`HtmlForm`控件创建一个服务器端的`<form>`标签。大多数 HTML 控件、Web 控件等。，必须放在一个`HtmlForm`控件内。

```
<form runat="server"> 

<!-- ASP.NET controls in here --> 

</form>
```

**T2`HtmlImage`**

`HtmlImage`控件创建一个服务器端的`<img>`标签。下面的代码展示了我们如何在页面上放置一个`HtmlImage`控件和一个`HtmlButton`:

```
<img id="myimage" src="arrow.gif" runat="server" /> 

<button id="myButton" runat="server" OnServerClick="Click">Click  

Here</button>
```

如果我们添加如下代码，用户可以通过按下按钮来动态更改此图像:

```
<script runat="server" language="VB"> 

Sub Click(s As Object, e As EventArgs) 

  myimage.Src = "welcome.gif" 

End Sub 

</script> 

<script runat="server" language="C#"> 

void Click(Object s, EventArgs e) { 

  myimage.Src = "welcome.gif"; 

} 

</script>
```

如果这些控件和脚本块一起放在一个页面上会发生什么？首先会出现图像`arrow.gif`。当点击`HtmlButton`控件时，它会变成`welcome.gif`。在幕后，当点击按钮时会引发`ServerClick`事件，从而调用`Click()`子例程，并将`HtmlImage`控件的 Src 属性从`arrow.gif`更改为`welcome.gif`。

**T2`HtmlGenericControl`**

`HtmlGenericControl`为没有关联 HTML 控件的 HTML 标签创建一个服务器端控件。最好的例子就是`<span>`和`<div>`标签。下面的例子说明了如何修改`<span>`标签中的文本，将内容从`I like ASP.NET`动态地更改为`Why would anyone need PHP?`。

```
<span id="myGenericControl" runat="server">I like ASP.NET</span> 

<br /> 

<button id="myButton" runat="server" OnServerClick="Click">Click  

Here</button>
```

我们只需添加以下代码来响应`ServerClick`事件并更改文本:

```
<script runat="server" language="VB"> 

Sub Click(s As Object, e As EventArgs) 

  myGenericControl.InnerText = "Why would anyone need PHP?" 

End Sub 

</script> 

<script runat="server" language="C#"> 

void Click(Object s, EventArgs e) { 

  myGenericControl.InnerText = "Why would anyone need PHP?"; 

} 

</script>
```

**T2`HtmlInputButton`**

`HtmlInputButton`控件创建一个服务器端的`<input type="submit">`、`<input type="reset">`或`<input type="button">` HTML 标签。

```
<input type="submit" value="Click Here" runat="server" /> 

<input type="reset" value="Click Here" runat="server" /> 

<input type="button" value="Click Here" runat="server" />
```

与`HtmlButton`一样，您可以使用`OnServerClick`属性将服务器端事件处理程序分配给这种类型的控件。

**T2`HtmlInputCheckBox`**

`HtmlInputCheckBox`控件创建一个服务器端的`<input type="checkbox">` HTML 标签。

```
<input type="checkbox" id="cb1" value="ASP.NET" runat="server" 

  />ASP.NET<br /> 

<input type="checkbox" id="cb2" value="PHP" runat="server" 

  />PHP<br /> 

<input type="checkbox" id="cb3" value="JSP" runat="server" 

  />JSP<br /> 

<input type="checkbox" id="cb4" value="CGI" runat="server" 

  />CGI<br /> 

<input type="checkbox" id="cb5" value="Coldfusion" runat="server" 

  />Coldfusion<br>
```

当您希望允许用户从列表中选择多个项目时，`HtmlInputCheckBox`控件是最佳选择。

**T2`HtmlInputFile`**

`HtmlInputFile`控件在 HTML 中创建一个服务器端的`<input type="file">`标签。这将显示一个文本框和浏览按钮，允许用户从 ASP.NET 页面上传文件。这个标签没有对应的 Web 控件，所以在处理文件上传时通常需要它——即使是 Web 表单(我们稍后将讨论)。

```
<input type="file" id="fileUpload" runat="server" />
```

**T2`HtmlInputHidden`**

`HtmlInputHidden`控件创建一个服务器端的`<input type="hidden">`标签。

```
<input type="hidden" id="hiddenField" runat="server" />
```

尝试从浏览器中查看任何一个 ASP.NET 页面的源代码，您很可能会发现这个标签被用来存储视图状态信息。

**T2`HtmlInputImage`**

`HtmlInputImage`控件创建一个服务器端的`<input type="image">`标签。

```
<input type="image" id="imgMap" runat="server" 

  src="ButtonImage.jpg" />
```

这个标签提供了对`HtmlInputButton`控件的替代。它们的功能是一样的。不同之处在于,`HtmlInputImage`控件使用自定义图像，而不是倾斜的灰色 Windows 风格按钮。当用户单击这种类型的控件时，鼠标坐标也会随表单提交一起发送。

**T2`HtmlInputRadioButton`**

`HtmlInputRadioButton`控件创建一个服务器端单选按钮。例如，下面的代码提供了男性或女性的选择:

```
Gender?<br /> 

<input type="radio" id="radio1" runat="server" />Male<br /> 

<input type="radio" id="radio2" runat="server" />Female
```

类似于`HtmlInputCheckBox`控件，`HtmlInputRadioButton`控件创建一个项目列表供用户选择。然而，区别在于用户一次只能选择一个项目。

**T2`HtmlInputText`**

`HtmlInputText`控件创建一个服务器端的`<input type="text">`或`<input type="password">`标签。

```
Please Login<br /> 

Username:<br /> 

<input type="text" id="username" runat="server" /><br /> 

Password:<br /> 

<input type="password" id="password" runat="server" />
```

前面的代码创建了一个典型的登录屏幕布局。

**T2`HtmlSelect`**

`HtmlSelect`控件创建一个服务器端版本的`<select>`标签，用于创建下拉列表或列表框。下面的代码创建了一个下拉菜单:

```
Select your favorite movie:<br /> 

<select id="selectMovie" runat="server"> 

<option>Star Wars</option> 

<option>Spider Man</option> 

<option>The Godfather</option> 

<option>Lord of the Rings</option> 

</select>
```

下面的代码创建了一个多选列表框:

```
Which of these movies do you like?<br /> 

<select id="selectMovie" runat="server" multiple="true" size="4"> 

<option>Star Wars</option> 

<option>Spider Man</option> 

<option>The Godfather</option> 

<option>Lord of the Rings</option> 

</select>
```

您会注意到主`<select>`标签中的`<option>`标签；这用于表示出现在列表框或下拉菜单中的每个项目。

**`HtmlTable`、`HtmlTableRow`和`HtmlTableCell`**

`HtmlTable`、`HtmlTableRow`和`HtmlTableCell`控件创建了`<table>`、`<tr>`、`<td>`和`<th>`标签的服务器端版本。以下代码创建一个服务器端表:

```
<table id="myTable" border="1" cellspacing="0" cellpadding="0"  

runat="server">  

<tr runat="server" id="row1"> 

<td runat="server" id="cell1">Table Data 1</td> 

<td runat="server" id="cell2">Table Data 2</td> 

</tr> 

<tr runat="server" id="row2"> 

<td runat="server" id="cell3">Table Data 3</td> 

<td runat="server" id="cell4">Table Data 4</td> 

</tr> 

</table> 

<button id="myButton" OnServerClick="Click" runat="server">Click  

Here</button>
```

您可以添加以下代码来响应由`HtmlButton`控件引发的`Click`事件，并将第一个单元格的内容更改为“Hello World”

```
<script runat="server" language="VB"> 

Sub Click(s As Object, e As EventArgs) 

  cell1.InnerText = "Hello World" 

End Sub 

</script> 

<script runat="server" language="C#"> 

void Click(Object s, EventArgs e) { 

  cell1.InnerText = "Hello World"; 

} 

</script>
```

**T2`HtmlTextArea`**

`HtmlTextArea`控件创建了一个服务器端版本的`<textarea>`标签。

```
<textarea cols="60" rows="10" runat="server"></textarea>
```

我们只是简单浏览了一下 HTML 控件，因为根据您的 HTML 经验，它们应该都很熟悉。但是如果你想知道更多关于 HTML 控件的信息，包括每个控件的属性、方法和事件，请看*附录 A，HTML 控件参考*。

##### 处理简单表单

现在你已经对 ASP.NET 页面结构、VB.NET 和 C#语言以及 HTML 控件有了基本的了解，让我们把所有的东西放在一起，创建一个简单的 ASP.NET 应用程序。我们将在 VB.NET 和 C#中创建的应用程序将是一个简单的调查表，它使用以下 HTML 控件:

*   `HtmlForm`
*   `HtmlButton`
*   `HtmlInputText`
*   `HtmlSelect`

让我们从在您最喜欢的代码编辑器中创建新文件开始。以下代码为调查创建了可视界面:

**例 4.1。`SimpleForm.aspx`(节选)**

```
<html>  

<head>  

...  

</head>  

<body>  

<form runat="server">  

  <h2>Take the Survey!</h2>  

  <p>Name:<br />  

  <input type="text" id="txtName" runat="server" /></p>  

  <p>Email:<br />  

  <input type="text" id="txtEmail" runat="server" /></p>  

  <p>Which server technologies do you use?<br />  

  <select id="servermodel" runat="server" multiple="true">  

    <option>ASP.NET</option>  

    <option>PHP</option>  

    <option>JSP</option>  

    <option>CGI</option>  

    <option>Coldfusion</option>  

  </select></p>  

  <p>Do you like .NET so far?<br />  

  <select id="likedotnet" runat="server">  

    <option selected>Yes</option>  

    <option>No</option>  

  </select></p>  

  <p><button id="myButton" OnServerClick="Click" runat="server">  

  Confirm</button></p>  

</form>  

</body>  

</html>
```

从我们已经介绍的 HTML 控件中，你应该对这个页面的外观有一个很好的想法。我们所做的就是将一些`HtmlInputText`控件、一个`HtmlButton`控件和一个`HtmlSelect`控件放入强制`HtmlForm`控件中。记住，HTML 控件本质上只是带有`runat="server"`属性的 HTML 标签。完成后，界面将如图 4.1 所示。

**图 4.1。使用 HTML 控件创建 ASP.NET 页面的界面。**

![1329_fig1](img/d76e14d11bd8bebdd7c4189b01b2672b.png)

当用户点击按钮时，我们将简单地在他们的浏览器中显示他们的响应。在实际的应用程序中，我们可能更倾向于将它保存到数据库中，并以图表的形式显示结果。无论是哪种情况，我们都会访问 HTML 控件的属性，如下面的代码所示:

**例 4.2。`SimpleForm.aspx`(节选)**

```
<script runat="server" language="VB">  

Sub Click(s As Object, e As EventArgs)  

  Response.Write("Your name is: " & txtName.value & "<br />")  

  Response.Write("Your email is: " & txtEmail.value & "<br />")  

  Response.Write("You like to work with: " & servermodel.value & _  

    "<br />")  

  Response.Write("You like .NET: " & likedotnet.value)  

End Sub  

</script>
```

**例 4.3。`SimpleForm.aspx`(节选)**

```
<script runat="server" language="C#">  

void Click(Object s, EventArgs e) {  

  Response.Write("Your name is: " + txtName.Value + "<br />");  

  Response.Write("Your email is: " + txtEmail.Value + "<br />");  

  Response.Write("You like to work with: " + servermodel.Value +  

    "<br />");  

  Response.Write("You like .NET: " + likedotnet.Value);  

}  

</script>
```

正如你在前面章节的例子中看到的，我们将 VB.NET 和 C#代码放在页面的`<head>`部分的服务器端脚本块中。接下来，我们创建一个新的`Click`事件处理程序，它接受两个常用参数。最后，我们使用`Response`对象的`Write()`方法打印出页面中用户的响应。

一旦你写了代码，你可以保存你的工作并从你的浏览器测试结果。输入一些信息，然后单击按钮。当点击按钮时，您输入的内容应该出现在页面的顶部。

##### Web 窗体简介

随着新技术的出现，总会有新的术语需要掌握。ASP.NET 也不例外。有了 ASP.NET，以前用来描述网页的最简单的术语也发生了变化，以反映网页中发生的过程。在我们开始描述 Web 表单所遵循的过程之前，让我们讨论一下 Web 页面的基础概念。

在最基本的层面上，*网页*是包含标记的文本文件。网页应该在浏览器窗口中查看，浏览器窗口解析包含标记的文件，以开发人员设想的布局向用户显示信息。网页可以包括文本、视频、声音、动画、图形，甚至是来自各种技术的“代码”块。

正如您在前面几节中了解到的，HTML 表单是包含一个或多个表单元素的页面，这些元素组合在一个 HTML `<form>`标签中。用户与各种表单元素进行交互，以做出某些选择或提供某些信息；单击提交按钮后，这些信息将被发送到服务器进行处理。这对我们这些 ASP.NET 开发者来说很有用，因为常规的 HTML 表单有一个内置的机制，允许表单提交到服务器。表单提交后，服务器上需要某种额外的技术——在本例中是 ASP.NET——来执行实际的表单处理。

在 ASP.NET，我们称网页*为网络表单*；它们包含 HTML 表单中的表示元素(ASP.NET Web 控件)，以及我们为页面的动态特性添加的任何代码(处理逻辑)。

典型的 Web 表单如图 4.2 所示。

**图 4.2。Web 窗体包含用于处理逻辑的代码和用于表示目的的 Web 控件。**

![1329_fig2](img/9d49bfdb2bd9eff0a02a2e3c96022931.png)

下一节将介绍各种 Web 控件以及如何在 Web 表单中使用它们。它们在外观上与 HTML 非常相似，所以你应该很容易掌握它们。

##### Web 控件简介

正如我们刚刚看到的，Web 表单允许用户使用 *Web 控件*与我们的站点进行交互。有了 Web 控件，微软基本上从零开始重新发明了 HTML。例如，它创建了两个不同的 Web 控件，对应于两个不同版本的 HTML `<select>`标签:一个`DropDownList`控件和一个`ListBox`控件。这意味着在 Web 控件和标准 HTML 标签之间没有直接的一对一的对应关系，就像 HTML 控件一样。Web 控件遵循与 HTML 标签相同的基本模式，但是标签名称以`asp:`开头，并且名称使用“CamelCasing”大写。考虑 HTML `<input>`标签，它在屏幕上创建一个输入文本框:

```
<input type="text" name="username" size="30" />
```

等效的 Web 控件是`TextBox`控件，如下所示:

```
<asp:TextBox id="username" Columns="30" runat="server">  

</asp:TextBox>
```

注意，与许多 HTML 标签不同，Web 控件总是需要一个结束标签(上面的`</asp:TextBox>`部分)。如果我们的 Web 控件标签在其开始和结束标签之间不包含任何内容，我们也可以使用简写的`/>`语法。所以，我们也可以把这个`TextBox`写成这样:

```
<asp:TextBox id="username" Columns="30" runat="server" />
```

总而言之，使用 Web 控件时要记住的要点是:

*   所有 Web 控件必须放在`<form runat="server">/#epc#/ tag to function properly.`中
*   所有 Web 控件都需要`id`和`runat="server"`属性才能正常工作。
*   所有的 Web 控件都遵循相同的模式，但是不同的控件有不同的属性。
*   它们都以前缀`asp`开头，后面跟一个冒号。

Web 控件比 HTML 控件多，有些控件提供了 HTML 所没有的高级功能。我们将在本章和后续章节中讨论的控件如下:

*   基本 Web 控件(*第 4 章，Web 表单和 Web 控件*)
*   验证 Web 控件(*第 5 章，验证控件*)
*   数据控件(*第 9 章，数据网格和数据列表控件*)
*   用户控件(*第十六章，丰富控件和用户控件*)
*   丰富控件(*第 16 章，丰富控件和用户控件*)

##### 基本 Web 控件

基本 Web 控件执行网页的屏幕布局，并在许多方面反映了基于常规 HTML 的 HTML 控件。然而，它们提供了一些新的改进和增强，应该尽可能地用它们来代替 HTML。在本节中，我们将了解该组中的控件，即:

*   `Label`
*   `TextBox`
*   `Button`
*   `Image`
*   `ImageButton`
*   `LinkButton`
*   `HyperLink`
*   `RadioButton`
*   `RadioButtonList`
*   `CheckBox`
*   `CheckBoxList`
*   `DropDownList`
*   `ListBox`
*   `Panel`
*   `PlaceHolder`

**T2`Label`**

在页面上显示静态文本的最简单的方法就是将文本添加到页面的正文中，而不要用任何标签将它括起来。但是，如果您想修改 ASP.NET 代码页面上显示的文本，您可以在一个`Label`控件中显示您的文本。这里有一个典型的例子:

```
<asp:Label id="lblMessage" Text="" runat="server" />
```

下面的代码设置`Label`控件的 Text 属性以显示文本“Hello World”:

```
Public Sub Page_Load()  

  lblMessage.Text = "Hello World"  

End Sub  

public void Page_Load() {  

  lblMessage.Text = "Hello World";  

}
```

阅读这个`Page_Load()`处理程序代码，我们可以看到当页面第一次加载时，ID 为`lblMessage`的`Label`控件的文本属性将被设置为“Hello World”

**T2`TextBox`**

`TextBox`控件用于在屏幕上创建一个框，用户可以在其中键入或阅读标准文本。使用`TextMode`属性，可以将这个 Web 控件设置为显示标准的 HTML 文本输入字段、HTML 密码字段或 HTML 文本区域。下面的代码展示了我们如何在一个简单的登录页面中使用它:

```
<p>Username:  

<asp:TextBox id="txtUser" TextMode="SingleLine" Columns="30"   

    runat="server" /></p>  

<p>Password:  

<asp:TextBox id="txtPassword" TextMode="Password" Columns="30"   

    runat="server" /></p>  

<p>Comments:  

<asp:TextBox id="txtComments" TextMode="MultiLine" Columns="30"   

    Rows="10" runat="server" /></p>
```

在上述三个实例中，属性`TextMode`决定了要呈现的文本框的类型。

**T2`Button`**

默认情况下，`Button`控件呈现的表单提交按钮与 HTML `<input type="Submit">`标签呈现的按钮相同。当单击一个按钮时，包含该按钮的表单被提交给服务器进行处理，同时引发 click 和 command 事件。下面的代码显示了一个`Button`控件和一个`Label`:

```
<asp:Button id="btnSubmit" Text="Submit" runat="server"   

    OnClick="WriteText" />  

<asp:Label id="lblMessage" runat="server" />
```

注意控件上的`OnClick`属性。与`HtmlButton` HTML 控件不同，`OnClick`分配了一个*服务器端*事件处理程序——不需要记住使用`OnServerClick`。当点击按钮时，将引发 Click 事件并调用`WriteText()`子例程。`WriteText()`子例程将包含执行该按钮预期功能的代码，例如为用户显示一条消息:

```
Public Sub WriteText(s As Object, e As EventArgs)  

  lblMessage.Text = "Hello World"  

End Sub  

public void WriteText(Object s, EventArgs e) {  

  lblMessage.Text = "Hello World";  

}
```

重要的是要认识到大多数 Web 控件都有与之相关联的事件，基本思想和技术与`Button`控件的`Click`事件相同。

**T2`Image`**

一个`Image`控件在页面上放置了一个可以从代码中动态访问的图像；它相当于 HTML 中的`<img>`标签。这里有一个例子:

```
<asp:Image id="myImage" ImageUrl="mygif.gif" runat="server"  

    AlternateText="description" />
```

**T2`ImageButton`**

一个`ImageButton`控件类似于一个`Button`控件，但是它使用你提供的图像来代替典型的灰色窗口样式的按钮。例如:

```
<asp:ImageButton id="myImgButton" ImageUrl="myButton.gif"  

    runat="server" />
```

**T2`LinkButton`**

一个`LinkButton`控件在你的页面上呈现一个超链接。从 ASP.NET 电码的角度来看，`LinkButtons`可以像按钮一样处理，因此得名。

```
<asp:LinkButton id="myLinkButon" Text="Click Here" runat="server"  

    />
```

**T2`HyperLink`**

类似于`LinkButton`控件的`HyperLink`控件在页面上创建一个超链接。它比`LinkButton`更简单，处理速度也更快，但是不像`LinkButton`控件，它提供了诸如`Click`事件和验证的特性，`HyperLink`只能用于点击和从一个页面导航到下一个页面。

```
<asp:HyperLink id="myLink" NavigateUrl="http://www.example.com/"   

    ImageUrl="myButton.gif" runat="server">My Link</asp:HyperLink>
```

如果指定了`ImageUrl`属性，它会使控件显示一个链接的图像，而不是所提供的文本。

**T2`RadioButton`**

您可以使用`RadioButton`控件将单个单选按钮逐个添加到页面中。使用 GroupName 属性将单选按钮组合在一起。一次只能从每组中选择一个`RadioButton`控件。

```
<asp:RadioButton id="radSanDiego" GroupName="City"  

    Text="San Diego" runat="server" />  

<asp:RadioButton id="radBoston" GroupName="City" Text="Boston"   

    runat="server" />  

<asp:RadioButton id="radPhoenix" GroupName="City" Text="Phoenix"   

    runat="server" />  

<asp:RadioButton id="radSeattle" GroupName="City" Text="Seattle"   

    runat="Server" />
```

与`RadioButtons`关联的主事件是`CheckChanged`事件；这可以用`OnCheckChanged`属性来处理。

**T2`RadioButtonList`**

像`RadioButton`控件一样，`RadioButtonList`控件代表单选按钮。然而，`RadioButtonList`控件表示一个单选按钮列表，并使用更紧凑的语法。这里有一个例子:

```
<asp:RadioButtonList id="radlFavColor" runat="server">  

  <asp:ListItem Text="Red" Value="red" />  

  <asp:ListItem Text="Blue" Value="blue" />  

  <asp:ListItem Text="Green" Value="green" />  

</asp:RadioButtonList>
```

`RadioButtonList`的一个重要特性是它能够将绑定到数据源上进行*。例如，假设您在数据库中有一个雇员列表。您可以创建一个页面，将数据库中的选择绑定到`RadioButtonList`控件，以动态列出控件中的某些雇员。然后，用户将能够从该列表中选择一个(且只能选择一个)雇员，我们的代码可以决定选择。*

由`RadioButtonList`产生的最有用的事件是`SelectedIndexChanged`事件，您可以用`OnSelectedIndexChanged`属性为其分配一个处理程序。

**T2`CheckBox`**

您可以使用一个`CheckBox`控件来表示一个只能是 yes(选中)或 no(未选中)值的选择。

```
<asp:CheckBox id="chkQuestion" Text="I like .NET!" runat="server"  

  />
```

与`RadioButton`控件一样，与`CheckBox`相关联的主事件是`CheckChanged`事件；这可以用`OnCheckChanged`属性来处理。

**T2`CheckBoxList`**

您可能已经猜到了，`CheckBoxList`控件代表一组复选框；相当于在 row 中使用了几个`CheckBox`控件:

`<asp:CheckBoxList id="chklFavDrinks" runat="server">  
 <asp:ListItem Text="Pizza" Value="pizza" />  
 <asp:ListItem Text="Tacos" Value="tacos" />  
 <asp:ListItem Text="Pasta" Value="pasta" />  
</asp:CheckBoxList>`

像`RadioButtonList`控件一样，`CheckBoxList`控件能够绑定到数据源，并产生一个可以用`OnSelectedIndexChanged`处理的`SelectedIndexChanged`事件。

**T2`DropDownList`**

一个`DropDownList`控件类似于 HTML `<select>`标签。`DropDownList`控件允许您使用下拉菜单从列表中选择一个项目。

```
<asp:DropDownList id="ddlFavColor" runat="server">  

  <asp:ListItem Text="Red" value="red" />  

  <asp:ListItem Text="Blue" value="blue" />  

  <asp:ListItem Text="Green" value="green" />  

</asp:DropDownList>
```

与其他基于集合的控件一样，如`CheckBoxList`和`RadioButtonList`控件，`DropDownList`控件可以绑定到数据库，从而允许您将动态内容提取到下拉菜单中。如你所料，这个控件产生的主事件是`SelectedIndexChanged`，用`OnSelectedIndexChanged`处理。

**T2`ListBox`**

一个`ListBox`控件等同于 HTML `<select>`标签，其`size`属性设置为 2 或更多。`ListBox`控件允许您从多行菜单中选择项目。如果您将`SelectionMode`属性设置为`Multiple`，用户将能够从列表中选择多个项目，如下例所示:

```
<asp:ListBox id="listTechnologies" runat="server"  

    SelectionMode="Multiple">  

  <asp:ListItem Text="ASP.NET" Value="aspnet" />  

  <asp:ListItem Text="JSP" Value="jsp" />  

  <asp:ListItem Text="PHP" Value="php" />  

  <asp:ListItem Text="CGI" Value="cgi" />  

  <asp:ListItem Text="Coldfusion" Value="cf" />  

</asp:ListBox>
```

同样，因为`ListBox`控件是基于集合的控件，所以它可以动态绑定到数据源。该控件提供的最有用的事件是——您猜对了——`SelectedIndexChanged`,以及相应的`OnSelectedIndexChanged`属性。

**T2`Panel`**

`Panel`控件的功能类似于 HTML 中的`<div>`标签，因为标签中的一组项目可以作为一个组来操作。例如，`Panel`可以通过`Button's Click`事件变得可见或隐藏:

```
<asp:Panel id="pnlMyPanel" runat="server">  

  <p>Username:  

    <asp:TextBox id="txtUsername" Columns="30" runat="server" />  

  </p>  

  <p>Password:  

    <asp:TextBox id="txtPassword" TextMode="Password"   

        Columns="30" runat="server" /></p>  

</asp:Panel>  

<asp:Button id="btnHide" Text="Hide Panel" OnClick="HidePanel"   

    runat="server" />
```

上面的代码在一个`Panel`控件中创建了两个`TextBox`控件。`Button`控件在面板之外。然后，`HidePanel()`子例程通过将`Panel`的 Visible 属性设置为`False`来控制其可见性:

```
Public Sub HidePanel(s As Object, e As EventArgs)  

  pnlMyPanel.Visible = False  

End Sub  

public void HidePanel(Object s, EventArgs e) {  

  pnlMyPanel.Visible = false;  

}
```

在这种情况下，当用户单击按钮时，会引发`Click`事件并调用`HidePanel()`子例程，该子例程会将`Panel`控件的`Visible`属性设置为`False`。

**T2`PlaceHolder`**

通过代码，`PlaceHolder`控件让我们可以随时在页面上的特定位置动态添加元素。

```
<asp:PlaceHolder id="phMyPlaceHolder" runat="server" />
```

下面的代码在占位符中动态添加了一个新的`HtmlButton`控件。

```
Public Sub Page_Load()  

  Dim btnButton As HtmlButton = New HtmlButton()  

  btnButton.InnerText = "My New Button"  

  phMyPlaceHolder.Controls.Add(btnButton)  

End Sub  

public void Page_Load() {  

  HtmlButton btnButton = new HtmlButton();  

  btnButton.InnerText = "My New Button";  

  phMyPlaceHolder.Controls.Add(btnButton);  

}
```

这就是我们对基本 Web 控件的快速浏览。有关 Web 控件的更多信息，包括每个控件的属性、方法和事件，请看*附录 B，Web 控件参考*。

##### 处理页面导航

网页之间的链接是网络的驱动力。如果没有链接，网络将仅仅是一个简单的基于页面的信息源。链接让我们只需轻轻一点，就能毫不费力地从一页跳到另一页；它们跨越了相关思想之间的鸿沟，而不受地理和政治的限制。本节重点介绍页面导航，使用:

*   `HyperLink`控件
*   导航对象及其方法

假设您创建了一个网站，允许您的用户在一个页面上选择项目。你可以把这个页面叫做`viewcatalog.aspx`。假设您有第二个页面，名为`viewcart.aspx`。一旦用户从`viewcatalog.aspx`选择了一个项目，你可能会想把他们直接链接到`viewcart.aspx`，这样他们就可以跟踪他们的订单。为了实现这一点，我们显然必须将信息从`viewcatalog.aspx`页面传递到`viewcart.aspx`页面。

***利用* `HyperLink` *控制***

`HyperLink`控件在页面上创建一个简单的 HTML 超链接。单击后，用户将被重定向到由 NavigateUrl 属性指定的页面。例如:

```
<asp:HyperLink id="hlAddToCart" NavigateUrl="viewcart.aspx"    

    runat="server" Text="View Cart" />
```

这里，`NavigateUrl`属性指定该链接指向名为`viewcart.aspx`的页面。图 4.3 显示了`HyperLink`控件是如何在浏览器中呈现的。

**图 4.3。超链接控件呈现类似于浏览器中的锚标记。**

![1329_fig3](img/1046da32be380ec2ca61c6b238d2bad1.png)

然而，一旦我们到达新页面，它就无法访问第一页的信息。如果我们需要为用户提供一些连续性的信息，我们需要别的东西。

***导航对象及其方法***

前面的示例呈现了一个类似于 HTML 锚标记的简单控件。然而，一旦链接被跟踪，我们就没有前一页的记录或它包含的任何数据(网络是一种无状态的技术)。

如果我们希望将信息从一个页面传递到下一个页面，我们可以使用下面列出的三种方法之一来创建页面之间的链接:

```
Response.Redirect()
```

从代码导航到第二页。这相当于使用`HyperLink`控件，但是允许我们动态地设置查询字符串的参数。

```
Server.Transfer()
```

结束当前 Web 窗体并开始执行新的 Web 窗体。这个方法只在用户导航到一个新的 Web 表单页面时有效(`.aspx`)。

```
Server.Execute()
```

在显示当前 Web 表单时开始执行新的 Web 表单。两种形式的内容在发送给浏览器的响应中组合在一起。同样，这个方法只在用户导航到 Web 表单页面时有效(`.aspx`)。

将用户从`viewcatalog.aspx`页面重定向到`viewcart.aspx`页面的最简单快捷的方法是使用`Reponse.Redirect()`:

```
Sub linkClk(s As Object, e As EventArgs)   

  Response.Redirect("viewcart.aspx")   

End Sub   

void linkClk(Object s, EventArgs e) {   

  Response.Redirect("viewcart.aspx");   

}
```

然后，您可以使用`LinkButton`控件调用这个子程序，如下所示:

```
<asp:LinkButton id="lbAddToCart" Text="Add To Cart"    

    OnClick="linkClk" runat="server"/>
```

这一次，当您单击`LinkButton`控件时，将引发`Click`事件，调用子例程，并以我们要链接的页面名称作为参数调用`Response.Redirect()`。这样，我们直接从代码重定向到新页面，而不是使用特定的标签。这使我们能够在*查询字符串*中将信息传递给新页面。

查询字符串是一个变量列表和它们各自的值，我们可以将它们附加到页面的 URL，允许我们从该页面的代码中检索这些变量和值。

举例来说，假设您有一个包含以下产品信息的下拉列表:

```
<p><asp:DropDownList id="ddlProducts" runat="server">   

  <asp:ListItem Text="Pants" />   

  <asp:ListItem Text="Shirt" />   

  <asp:ListItem Text="Hat" />   

  <asp:ListItem Text="Socks" />   

</asp:DropDownList></p>   

<p><asp:LinkButton id="lbAddToCart" Text="Add To Cart"    

    OnClick="linkClk" runat="server" /></p>
```

您用来处理链接点击的代码需要找到下拉列表中选定的项目，并将其附加到用户将被重定向到的 URL 的查询字符串中，如下所示:

```
Sub linkClk(s As Object, e As EventArgs)   

  Dim strQueryStr As String = "?Product=" & _   

    Server.UrlEncode(ddlProducts.SelectedItem.Text)   

  Response.Redirect("viewcart.aspx" & strQueryStr)   

End Sub   

void linkClk(Object s, EventArgs e) {   

  string strQueryStr = "?Product=" +   

    Server.UrlEncode(ddlProducts.SelectedItem.Text);   

  Response.Redirect("viewcart.aspx" + strQueryStr);   

}
```

请注意`Server.UrlEncode()`方法的使用，它将查询字符串值中不允许的字符(例如&)转换为浏览器可以理解的 URL 安全字符代码(例如%26)。向查询字符串中添加任意值时，应始终使用此方法。

当用户从下拉列表中选择一个项目并单击`LinkButton`控件时，将打开`viewcart.aspx`页面，所选产品将作为查询字符串的一个参数被追加。这如图 4.4 所示。

**图 4.4。将所选项追加到查询字符串中。**

![1329_fig4](img/f2624794a20a38de83af2ea7e32e9c9c.png)

现在您已经将产品传递到了`viewcart.aspx`页面，您必须从新页面的查询字符串中获取它。我们通过访问请求从查询字符串中获得变量。QueryString 集合，像这样:

```
Sub Page_Load()   

  lblResult.Text = Request.QueryString("Product")   

End Sub   

void Page_Load() {   

  lblResult.Text = Request.QueryString["Product"];   

}
```

这里，我们简单地显示了查询字符串参数`Product`的值，如图 4.5 所示。

**图 4.5。在一个`Page_Load`事件处理程序中设置 label 控件的 text 属性以接受新的参数值。**

![1329_fig5](img/d5ced5db0e5ed7202e44ca9da030bb31.png)

现在，当您选择一个产品并将其添加到购物车中时，结果会显示在重定向页面的一个标签上，标签的`id`为`lblResult`。当然，真正的产品目录和购物车还有很多内容，但在这一部分，我们发现了一个重要的构建模块。

##### 回发

回发可能会让新手感到困惑，因为尽管大多数 ASP.NET 开发人员知道它是什么，但他们似乎无法解释清楚。到目前为止，我们讨论的主题，如子例程、函数和事件，对大多数 Web 开发人员来说并不陌生。HTML 结合客户端 JavaScript 已经做了很多年了。但是，ASP.NET 与这种模型不同，因为它是服务器端的技术，而不是客户端的技术——页面上发生的事件由运行在服务器上的代码处理。为此，ASP.NET 使用回发机制。

当一个事件被触发时，例如，一个按钮被点击，或者网格中的一个项目被选择，页面被提交回服务器进行处理，同时提交的还有关于事件的信息和页面上任何预先存在的数据(通过视图状态)。我们称页面“回发到”服务器。这是一个需要掌握的强大概念，因为是回发让我们在服务器上而不是在客户端的浏览器上运行代码，是回发让我们的服务器代码知道下拉列表中的哪些项被选中，或者用户在文本框中键入了什么信息。

但是，如果您有多个填充了数据库数据的`DropDownList`控件，会发生什么呢？用户可以与这些`DropDownList`控件交互，反过来，我们可以根据他们从下拉菜单中选择的内容来设置页面中的某些选项。虽然这看起来是一个普通的任务，但是对于传统的 ASP 来说，它会产生相当大的开销。问题是，虽然从数据库绑定到下拉菜单的数据永远不会改变，但每次用户从下拉菜单中选择一个项目并进行回发时，都必须再次访问数据库以重新构建页面上每个下拉列表的内容。然而，这在 ASP.NET 不是问题。

在 ASP.NET 中，我们可以用`IsPostBack`属性检查回发，从而避免执行任何不必要的耗时任务。`IsPostBack`是一个页面级属性——这意味着它是页面本身的属性——我们通常在`Page_Load()`事件处理程序中使用它，只有在页面首次加载时才执行代码。考虑下面的例子:

**例 4.4。`PostBack.aspx`**

```
<html>   

<head>   

<script runat="server" language="VB">   

Sub Page_Load(s As Object, e As EventArgs)    

  lblMessage1.Text = Now()   

  If Not IsPostBack Then   

    lblMessage2.Text = Now()   

  End If   

End Sub   

</script>   

</head>   

<body>   

<form runat="server">   

  <p>Not Checking for postback:<br />   

    <asp:Label id="lblMessage1" runat="server" /></p>   

  <p>Checking for postback:<br />   

    <asp:Label id="lblMessage2" runat="server" /></p>   

  <p><asp:Button id="btnClick" Text="Click Me" runat="server" />   

  </p>   

</form>   

</body>   

</html>
```

**例 4.5。`PostBack.aspx`**

```
<html>   

<head>   

<script runat="server" language="C#">   

void Page_Load(Object s, EventArgs e) {   

  lblMessage1.Text = Convert.ToString(DateTime.Now);   

  if (!IsPostBack) {   

    lblMessage2.Text = Convert.ToString(DateTime.Now);   

  }   

}   

</script>   

</head>   

<body>   

<form runat="server">   

  <p>Not Checking for postback:<br />   

    <asp:Label id="lblMessage1" runat="server" /></p>   

  <p>Checking for postback:<br />   

    <asp:Label id="lblMessage2" runat="server" /></p>   

  <p><asp:Button id="btnClick" Text="Click Me" runat="server" />   

  </p>   

</form>   

</body>   

</html>
```

结果将类似于图 4.6。

**图 4.6。IsPostBack 属性检查以确保用户没有重新提交页面。**

![1329_fig6](img/f53d113331ba718efcde24afbb197b9b.png)

在这个例子中，`IsPostBack`检查意味着当点击`Button`控件时，第二个标签不会刷新。类似地，我们可以在`Page_Load()`子例程中使用`IsPostBack`，在每个用户的会话中设置一次数据库驱动的下拉菜单，使在线体验更加流畅，并使我们的应用程序更具可伸缩性。如果回发现在看起来有点混乱，不要担心——我们将在接下来的章节中更多地使用它，所以如果还没有，在几个更实际的例子之后它应该是有意义的。

##### 用 CSS 格式化控件

HTML 被有意设计成很少注意页面上特定项目是如何呈现的细节。解决这些复杂的问题，并根据用户机器的限制和优势定制输出，这就要靠个人浏览器了。虽然我们可以使用 HTML 标记来更改字体样式、大小、颜色等，但这种做法会导致冗长的代码和页面，以后很难重新设计它们的样式。

层叠样式表(CSS) 语言旨在提供现代网页设计者所寻求的控制度、灵活性和活力。这是一个被所有流行浏览器广泛支持的标准，至少在其最老的版本(CSS1)中是如此。

CSS 是 Web 开发人员的强大工具，因为它让我们能够在单个表单中创建一组样式，并将这些样式应用于我们网站中的所有页面。然后，所有的页面在相同的部分使用相同的字体、颜色和大小，给整个网站一种一致的感觉。不管我们的站点包含三个页面还是三百个页面，当我们改变样式表中的样式时，这些改变会立即应用到基于该样式表的所有页面。

***样式和样式表的类型***

有三种不同的方式将样式与特定网页的元素相关联。我已经提到了第一个，通常也是最好的一个，它是一个外部文件:

**外部文件**

通过将您的样式规则放在一个外部样式表中，您可以将这个文件链接到您想要使用那些*样式*的任何网页。这使得更新网站的整体外观变得轻而易举。

**文档宽度**

您可以将页面的样式规则放在页面 head 元素内的一个

**内嵌**

内联样式允许我们使用`style`属性为单个标签设置样式。例如，我们可以在常规 HTML 中创建一个带有`style`属性的文本框，该属性在文本框周围绘制一个边框，如下所示:

```
<input type="text"   

    style="border-style:groove" />
```

CSS 样式规则以两种方式之一创建应用于页面元素的样式(在某种程度上，这是 CSS 工作方式的简化视图。完整的故事请参考 [HTML Utopia:使用 CSS](https://www.sitepoint.com/books/css1/) 进行无表设计(SitePoint，ISBN 0-9579218-2-9)。

**类**

可以说，类是在页面中使用样式的最流行的方式，它允许您设置自定义样式，该样式将应用于任何具有与自定义样式名称相匹配的类属性的标记或控件。

**标签重定义**

重新定义标签会影响某些标准 HTML 标签的外观。例如，默认情况下，`<hr>`标签的宽度通常为 100%,但是您可以在 CSS 中重新定义标签的宽度为 50%。

无论是构建外部的、文档范围的还是内联的样式表，类的属性和标记重定义都使用相同的语法。要在外部样式表文件中创建一个类，可以使用以下语法:

```
.myClass {   

  font-family: arial;   

  font-size: 10pt;   

  color: red;   

}
```

这将被保存在一个扩展名为`.css`的文件中，比如`styles.css`，并通过文档的`<head>`标签中的下面一行链接到 Web 表单中:

```
<link href="styles.css" rel="stylesheet" />
```

类似地，要在整个文档的样式表中定义一个类，可以使用以下语法:

```
<head>   

<style type="text/css">   

  .myClass {   

    font-family: arial;   

    font-size: 10pt;   

    color: red;   

  }   

</style>   

</head>
```

使用内联样式时，请使用以下语法:

```
<span style="font-family: arial; font-size: 10pt; color: red;">My    

Stylized Text</span>
```

对于内联样式，只需用`style`属性将所有属性添加到标签中。上面，我们使用了`<span>`标签，但是其他标签的原理是一样的。

现在你已经对 CSS 背后的一些基本概念有了基本的了解，让我们看看可以在我们的 ASP.NET 应用程序中使用的不同类型的样式。

***样式属性***

使用样式表可以修改许多不同类型的属性。以下是常见类型的列表:

**字体**

此类别为您提供了格式化文本级别元素的能力，包括它们的字体、大小、装饰、粗细、颜色等。

**背景**

此类别允许您自定义对象和文本的背景。通过修改这些值，您可以控制颜色、图像以及是否要重复图像。

**块**

此类别允许您修改段落之间的间距、文本行之间的间距以及文本和单词之间的间距。

**框**

“框”类别提供了对表格的更改和自定义。如果需要修改表格、行或单元格的边框、填充、间距和颜色，可以修改此类别中的元素。

**边框**

此类别允许您在页面元素周围绘制不同颜色、样式和粗细的框。

**列表**

此类别允许您自定义有序列表和无序列表的创建方式。

**定位**

修改定位允许您自由移动和定位标签和控件。

这些类别提供了通常可以使用 CSS 修改的内容列表。随着本书的深入，许多类型的属性将变得显而易见。

***CSS class 属性***

一旦您在样式表中定义了一个类(无论是外部的还是内部的)，您将希望开始将该类与 Web 表单中的元素相关联。您可以使用`CssClass`属性将类与 ASP.NET Web 控件相关联。下面的示例使用了在文档范围的样式表中定义的类:

```
<html>   

<head>   

<style type="text/css">   

  .dropdownmenu {   

    font-family: Arial;   

    background-color: #0099FF;   

  }   

  .textbox {   

    font-family: Arial;   

    background-color: #0099FF;   

    border: 1px solid;   

  }   

  .button {   

    font-family: Arial;   

    background-color: #0099FF;   

    border: 1px solid;   

  }   

  .text {   

    font-family: Arial, Helvetica, sans-serif;   

    font-size: 10px;   

  }   

</style>   

</head>   

<body>   

<form runat="server">   

<p class="text">Please select a product:</p>   

<p><asp:DropDownList id="ddlProducts" CssClass="dropdownmenu"   

    runat="server">   

  <asp:ListItem Text="Shirt" selected="true" />   

  <asp:ListItem Text="Hat" />   

  <asp:Listitem Text="Pants" />   

  <asp:ListItem Text="Socks" />   

</asp:DropDownList></p>   

<p><asp:TextBox id="txtQuantity" CssClass="textbox" runat="server"   

    /></p>   

<p><asp:Button id="btnAddToCart" CssClass="button" runat="server"   

    Text="Add To Cart" /></p>   

</form>   

</body>   

</html>
```

##### 内部网应用程序的导航菜单和 Web 表单

现在，您已经在 HTML 控件、Web 表单、Web 控件、页面交互、导航和样式表方面有了坚实的基础，您已经准备好开始我们将在本书剩余部分中构建的项目。通过使用 *Dorknozzle Intranet 应用程序*，我希望通过简单的步骤向您介绍真实世界的开发，因为我们一起完成了下面的章节。

***介绍多科纽内网应用***

虽然大多数书给你一系列简单、孤立的例子来说明特定的技术，但这本书有一点不同。这些页面中提供的许多例子将涉及到一个单一项目的工作——一个虚构的 Dorknozzle 公司的内部网应用程序。

我们将在这个应用程序的基础上进行构建，展示对任何类型的 Web 应用程序的开发人员都很重要的许多不同的概念。我们将开发的内部网应用程序将提供以下功能:

**欢迎光临**

向 Web 应用程序的用户显示公司事件信息。

**服务台**

允许任何 Dorknozzle 员工将他们遇到的软件、硬件或计算机问题作为服务台票证提交给 it 管理员。

**员工商店**

员工商店提升公司士气。通过建立一个在线商店，我们将允许多克喷嘴员工购买丰富生活的物品，如杯子，衬衫和鼠标垫。当然，所有人都将自豪地打上傻瓜的标志！

**简讯存档**

另一个提高士气的方法是让员工了解公司事件和新闻。每个月，Dorknozzle 人力资源经理都会向所有员工发送一份公司简讯。

**员工目录**

员工们可能会想打电话给对方，讨论重要的、与公司相关的事务…比如昨晚看电视的事情！员工目录应该让员工找到其他员工的详细信息。

**通讯录**

虽然员工目录存储了方便员工使用的信息，但通讯簿的目的是提供有关公司内所有员工的更详细的信息

**管理工具**

管理员需要一种方法来修改关闭的服务台票证、删除被解雇员工的记录、创建新员工的档案、修改当前员工的信息等等。管理工具部分将为此提供接口。

在我们开始创建所有这些较小的应用程序之前，我们必须构建一个框架，作为整个站点的模板。在本节中，我们将完成开发内部网应用程序的下列介绍性任务:

*   构建导航菜单。
*   创建样式表。
*   为帮助台应用程序设计模板和 Web 表单。

***建筑导航菜单***

一旦它完成，我们虚构的内部网应用程序将有一个 it 服务台，员工商店，时事通讯存档，员工目录，地址簿和管理控制台的模块。显然，我们将需要某种导航菜单来使这些子应用程序容易找到。在这一章中，我们已经学习了许多从一页到另一页导航的方法，我们可以在这里使用这些方法中的任何一种。我们已经讨论了诸如按钮控件、超链接控件和 LinkButton 控件之类的控件，并且探索了从代码中导航的各种对象和方法。尽管所有这些在一定程度上都行得通，但在这种情况下，就性能和实用性而言，只有一种方法最有意义。

在我们开始之前，您需要从本书的代码档案中获取必要的文件。本章的文件包括一个可用于本项目的起始模板，以及在遇到问题时使用的完整版本。

因为我们没有提交任何数据进行处理，所以我们可以消除`Button`和`LinkButton`控件；每个都涉及到服务器的额外工作，以便处理它引发的`Click`事件。因为我们只想从一个页面链接到下一个页面，并且不关心以编程方式执行任何任务，所以我们可以使用更简单的`HyperLink`控件。请记住，我们通过在表单中插入以下代码向页面添加了一个`HyperLink`控件:

```
<asp:HyperLink NavigateUrl="index.aspx" runat="server"     

    Text="Home" />
```

这将添加一个显示文本“Home”的链接

打开文本编辑器，用 ASP.NET 页面所需的标准 HTML 标签创建一个新文件，包括一个带有`runat="server"`属性的空表单。在这个表单中，为帮助台、员工商店、时事通讯存档、员工目录、地址簿和管理工具添加`HyperLink`控件，如下所示:

**例 4.6。`index.aspx`(节选)**

```
<!-- HyperLink controls for navigation -->    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="index.aspx" runat="server" Text="Home"    

    />    

<br />     

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="helpdesk.aspx" runat="server"    

    Text="HelpDesk" />    

<br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="employeestore.aspx" runat="server"    

    Text="Employee Store" />    

<br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="newsletterarchive.aspx" runat="server"    

    Text="Newsletter Archive" />    

<br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="employeedirectory.aspx" runat="server"    

    Text="Employee Directory" />    

<br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="addressbook.aspx" runat="server"    

    Text="Address Book" />    

<br /><br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="admintools.aspx" runat="server"    

    Text="Admin Tools" />    

<!-- End HyperLink controls -->
```

一旦链接被添加到页面上，并且你已经把`book_closed.gif`文件放在一个叫做`Images`的子目录中，你可以保存你的工作(作为`index.aspx`)并且在你的浏览器中查看结果。然而，在现阶段，它看起来相当平淡无奇。我们需要的是一些漂亮的图形来提供视觉吸引力！尽管现代的 Web 设计实践会让我们在页面布局和视觉设计中使用 CSS，但为了专注于应用程序的服务器端方面，我们在这里将求助于 HTML 表格。

打开`index.aspx`并在页面主体的最开始创建以下两个常规(即非服务器端)HTML 表:

**例 4.7。`index.aspx`(节选)**

```
<body>    

<form runat="server">    

<table width="100%" border="0" cellspacing="0" cellpadding="0"    

    background="Images/header_bg.gif">    

  <tr>    

    <td><img src="Images/header_top.gif" width="450" height="142"    

        alt="the official dorknozzle company intranet"    

        /></td>    

  </tr>    

</table>    

<table width="100%" border="0" cellspacing="0" cellpadding="0">    

  <tr>    

    <td width="157"><img src="Images/header_bottom.gif"    

        width="157" height="37" alt="" /></td>    

    <td></td>    

  </tr>    

</table>
```

我们也想把我们的链接放在一个表中。在那里，我们将向主索引页面添加一些新闻条目。再次打开`index.aspx`，将下面的 HTML 表格放在我们已经添加的链接周围:

**例 4.8。`index.aspx`(节选)**

```
<table width="100%" border="0" cellspacing="0" cellpadding="10">    

  <tr>     

    <td valign="top" width="160">    

      <!-- HyperLink controls for navigation -->    

      ...    

      <!-- End HyperLink controls -->    

    </td>    

    <td valign="top">    

      <h1>Company News:</h1>    

      <p>We'll add some news later.</p>    

      <h1>Company Events:</h1>    

      <p>We'll add company events later.</p>    

    </td>    

  </tr>    

</table>    

</form>    

</body>    

</html>
```

结果将类似于图 4.7。

**图 4.7。为 Intranet 导航菜单添加超链接控件。**

![1329_fig7](img/47a134b2a3f8bbfeb49d9ac0c18b3698.png)

一些精心选择的图形所能带来的不同难道不令人惊讶吗？不要忘记将下载的图片放在`Images`子目录中。当然，您可以在代码档案中找到完整的源代码，尽管我建议您在我们进行的过程中自己键入代码，以供练习。

***创建企业样式表***

如果你不介意标准网页的普通外观，那么你可以跳过这一部分。但是，如果您不喜欢标准的蓝色超链接、黑色、Times New Roman 文本和斜面表单控件，这一节正是为您准备的。

正如您已经读到的，样式表为开发人员提供了对 Web 应用程序“外观”的灵活性和控制。在这一节中，我们将探索向我们虚构的 intranet 应用程序添加一个可定制的样式表。我们将为应用程序中的以下元素定义样式:

*   超链接
*   文本(包括正文和标题)
*   盒装控件(包括文本框和下拉菜单)

您可以从创建样式所在的 CSS 文件开始。我已经打开了记事本，并立即将文件保存为应用程序根目录下的`styles.css`,如图 4.8 所示。

**图 4.8。打开记事本，将文件保存为应用程序文件夹根目录下的 styles.css。**

![1329_fig8](img/ec6773dd08440466dd2bce5e85c005fc.png)

现在，让我们将一些样式属性应用于以下标签:

*   `body`
*   `p`
*   `h1`
*   `a:link`
*   `a:hover`

您会注意到这个列表中的`a:link`和`a:hover`项，严格来说它们不是标签。在 CSS 的世界里，这些被称为*伪元素*。`a:link`将选择范围缩小到作为链接的<和>标签(相对于作为目标的`<a name="...">`标签)。给`a:hover`分配属性只会将那些属性应用到用户鼠标悬停的链接上。

我们还将为某些不直接映射到特定 HTML 标签的 Web 控件定义几个类:

```
.textbox
```

对于`<asp:TextBox>`控件，当发送到浏览器时，它们成为`<input type="text">`和`<textarea>`标签。

```
.button
```

对于`<asp:Button>`控件，它们成为`<input type="button">`、`<input type="submit">`和`<input type="reset">`标签。

```
.dropdownmenu
```

对于成为`<select>`标签的`<asp:DropDownList>`控件。

下面是 CSS 规则的代码，它将把所需的基本格式应用到我们的站点。在您的`styles.css`文件中键入以下内容:

```
body {    

  background: #FFFFFF;    

  color: #000000;    

  margin: 0;    

  padding: 0;    

}    

p {    

  font-family: Arial;    

  font-size: 12px;    

}    

h1 {    

  font-family: Arial;    

  font-size: 14px;    

  color: #000000;    

}    

a:link {    

  font-family: Arial;    

  font-size: 12px;    

  color: #000000;    

}     

a:hover {     

  font-family: Arial;    

  font-size: 12px;    

  color: #FF0000;    

}    

.textbox {    

  font-family: Arial;    

  font-size: 12px;    

  border: 1px solid black;    

}    

.button {    

  font-family: Arial;    

  border: 1px solid black;    

  background-color: #CCCCCC;    

}    

.dropdownmenu {    

  font-family: Arial;    

  font-size: 12px;    

  background-color: #CCCCCC;    

}
```

既然已经创建了样式表文件，我们可以通过将下面一行插入到文档的`<head>`标签中来将样式表文件链接到`index.aspx`:

```
<link href="styles.css" rel="stylesheet" />
```

当我们创建 CSS 类时，我们需要将我们定义的 CSS 类(`textbox`、`button`和`dropdownmenu`)分配给相关的控件，但是现在我们简单的 HTML 模板将自动受益于我们重新定义的标签。

记住，我们并不局限于这些风格。如果在我们应用程序的开发过程中，我们决定添加更多的样式，我们只需要打开`styles.css`文件并根据需要添加它们。

此时，您可以保存您的工作，并在浏览器中查看它。

***设计服务台应用程序的 Web 表单***

该项目的最后一部分是添加员工服务台请求 Web 表单。这将是一个网页，允许我们虚构的员工报告硬件、软件和工作站问题。网络表单将被安排为一系列简单的步骤，用户可以按照这些步骤来报告他们的问题:

*   从预定义的潜在问题领域类别中挑选。(`DropDownList`控制)
*   从类别中的预定义主题中挑选。(`DropDownList`控制)
*   键入问题的描述。(多线`TextBox`控制)
*   提交请求。(`Button`控制)

您可以简单地复制`index.aspx`并将其重命名为`helpdesk.aspx`，而不是创建一个新的空白页面并重新键入所有代码(或者如果它已经在您的编辑器中打开，则用新名称保存一个副本)。为适应帮助台界面，代码中唯一要改变的部分是主体中的最后一个表——包含关于`index.aspx`的新闻条目的表。其他一切都保持不变，因为我们希望所有的页面都有一个单一的外观(我们将在后面的章节中看到更好的方法……)。更改表中的最后一列，以创建两个下拉列表、一个多行文本框和一个按钮，如下所示:

`<!-- End HyperLink controls -->    
   </td>    
   <td valign="top">    
     <h1>Employee HelpDesk Request</h1>    
     <p>Problem Category:<br />    
       <asp:DropDownList id="ddlCategory" CssClass="dropdownmenu"    
           runat="server" /></p>    
     <p>Problem Subject:<br />    
       <asp:DropDownList id="ddlSubject" CssClass="dropdownmenu"    
           runat="server" /></p>    
     <p>Problem Description:<br />    
       <asp:TextBox id="txtDescription" CssClass="textbox"    
           Columns="40" Rows="4" TextMode="MultiLine"    
           runat="server" /></p>    
     <p><asp:Button id="btnSubmit" CssClass="button"    
           Text="Submit Request" runat="server" /></p>    
   </td>`

请注意我们是如何将 CSS 类应用到适当的控件中的。

不要担心`DropDownList`控件没有与之关联的项目——类别和主题将在数据库表中预定义。稍后，我们将把这些数据库表绑定到它们各自的控件。

完成后，保存您的工作并在浏览器中查看。

##### 摘要

在本章中，我们讨论了 HTML 控件、Web 表单和 Web 控件。我们还探讨了如何链接页面，以及如何向控件添加样式。你甚至建立了你的第一个项目，把你在这一章和前几章中学到的信息放在一起。

您的 Web 应用程序将主要关注 Web 控件。在下一章，我们将学习如何通过使用 ASP.NET 验证控件来检查那些 Web 控件上的用户输入…

以上就是 SitePoint 的前四章*使用 c#&VB.NET*构建你自己的 ASP.NET 网站。这份关于建立你自己的 ASP.NET 网站的完整指南现已发布。[点击此处](https://www.sitepoint.com/books/aspnet1/)了解更多信息。

这本书不仅包含您刚刚阅读的四章，还包含十三章，涵盖了 Web 表单、验证控件、数据库设计、ADO.NET、构建购物车、错误处理、安全性和身份验证、XML Web services 等等。这本书还包括了一系列精彩的参考资料，这使得它成为你在 ASP.NET 从事网络开发时不可或缺的案头参考。有关更多信息，[请参见本书第](https://www.sitepoint.com/books/aspnet1/)页。

天使主义者传播你的产品，从而提高你的品牌知名度。

“重复访问”的粘性是电子商务网站的额外收获。它让你可以向客户追加销售另一种产品，可能是价格更高、利润率更高的产品。曾经向您订购过产品并体验过您卓越的客户服务——这**就是**卓越，不是吗？—回头客更倾向于再次点餐。

##### 如何让你的网站有粘性？

这个问题没有单一的答案。以下是一些可能对你有用的建议:

*   确保你的网站上有优秀的内容
*   把长文章分成多页
*   在每一页的结尾加上一个“预告”,吸引用户点击你网站的另一部分
*   邀请访问者为您的页面添加书签，以便将来访问
*   为访问者提供免费订阅时事通讯或公告列表
*   添加一个聊天室或公告板，以促进互动，并产生一种社区的感觉，让游客回来更多
*   开展竞赛、投票或调查，让访问者回来查看结果
*   进行测验，并在一段时间后提供答案
*   定期更新网站上的内容，让访问者得到娱乐和信息
*   向现有客户宣布打折销售或特别优惠
*   邀请访客成为注册会员
*   允许人们根据自己的需要个性化你的网站

归根结底，粘性将取决于你与你的观众建立的关系，以及你希望从每个访问者身上得到的结果。如果你为你的网站访问者提供了有价值的建议和令人愉快的体验，让他们再次光临，你从广告或电子商务中获得最大收益只是时间问题。

网上成功就是建立粘性关系。

使用`CssClass`属性的 ET Web 控件。下面的示例使用了在文档范围的样式表中定义的类:

```
<html>   

<head>   

<style type="text/css">   

  .dropdownmenu {   

    font-family: Arial;   

    background-color: #0099FF;   

  }   

  .textbox {   

    font-family: Arial;   

    background-color: #0099FF;   

    border: 1px solid;   

  }   

  .button {   

    font-family: Arial;   

    background-color: #0099FF;   

    border: 1px solid;   

  }   

  .text {   

    font-family: Arial, Helvetica, sans-serif;   

    font-size: 10px;   

  }   

</style>   

</head>   

<body>   

<form runat="server">   

<p class="text">Please select a product:</p>   

<p><asp:DropDownList id="ddlProducts" CssClass="dropdownmenu"   

    runat="server">   

  <asp:ListItem Text="Shirt" selected="true" />   

  <asp:ListItem Text="Hat" />   

  <asp:Listitem Text="Pants" />   

  <asp:ListItem Text="Socks" />   

</asp:DropDownList></p>   

<p><asp:TextBox id="txtQuantity" CssClass="textbox" runat="server"   

    /></p>   

<p><asp:Button id="btnAddToCart" CssClass="button" runat="server"   

    Text="Add To Cart" /></p>   

</form>   

</body>   

</html>
```

##### 内部网应用程序的导航菜单和 Web 表单

现在，您已经在 HTML 控件、Web 表单、Web 控件、页面交互、导航和样式表方面有了坚实的基础，您已经准备好开始我们将在本书剩余部分中构建的项目。通过使用 *Dorknozzle Intranet 应用程序*，我希望通过简单的步骤向您介绍真实世界的开发，因为我们一起完成了下面的章节。

***介绍多科纽内网应用***

虽然大多数书给你一系列简单、孤立的例子来说明特定的技术，但这本书有一点不同。这些页面中提供的许多例子将涉及到一个单一项目的工作——一个虚构的 Dorknozzle 公司的内部网应用程序。

我们将在这个应用程序的基础上进行构建，展示对任何类型的 Web 应用程序的开发人员都很重要的许多不同的概念。我们将开发的内部网应用程序将提供以下功能:

**欢迎光临**

向 Web 应用程序的用户显示公司事件信息。

**服务台**

允许任何 Dorknozzle 员工将他们遇到的软件、硬件或计算机问题作为服务台票证提交给 it 管理员。

**员工商店**

员工商店提升公司士气。通过建立一个在线商店，我们将允许多克喷嘴员工购买丰富生活的物品，如杯子，衬衫和鼠标垫。当然，所有人都将自豪地打上傻瓜的标志！

**简讯存档**

另一个提高士气的方法是让员工了解公司事件和新闻。每个月，Dorknozzle 人力资源经理都会向所有员工发送一份公司简讯。

**员工目录**

员工们可能会想打电话给对方，讨论重要的、与公司相关的事务…比如昨晚看电视的事情！员工目录应该让员工找到其他员工的详细信息。

**通讯录**

虽然员工目录存储了方便员工使用的信息，但通讯簿的目的是提供有关公司内所有员工的更详细的信息

**管理工具**

管理员需要一种方法来修改关闭的服务台票证、删除被解雇员工的记录、创建新员工的档案、修改当前员工的信息等等。管理工具部分将为此提供接口。

在我们开始创建所有这些较小的应用程序之前，我们必须构建一个框架，作为整个站点的模板。在本节中，我们将完成开发内部网应用程序的下列介绍性任务:

*   构建导航菜单。
*   创建样式表。
*   为帮助台应用程序设计模板和 Web 表单。

***建筑导航菜单***

一旦它完成，我们虚构的内部网应用程序将有一个 it 服务台，员工商店，时事通讯存档，员工目录，地址簿和管理控制台的模块。显然，我们将需要某种导航菜单来使这些子应用程序容易找到。在这一章中，我们已经学习了许多从一页到另一页导航的方法，我们可以在这里使用这些方法中的任何一种。我们已经讨论了诸如按钮控件、超链接控件和 LinkButton 控件之类的控件，并且探索了从代码中导航的各种对象和方法。尽管所有这些在一定程度上都行得通，但在这种情况下，就性能和实用性而言，只有一种方法最有意义。

在我们开始之前，您需要从本书的代码档案中获取必要的文件。本章的文件包括一个可用于本项目的起始模板，以及在遇到问题时使用的完整版本。

因为我们没有提交任何数据进行处理，所以我们可以消除`Button`和`LinkButton`控件；每个都涉及到服务器的额外工作，以便处理它引发的`Click`事件。因为我们只想从一个页面链接到下一个页面，并且不关心以编程方式执行任何任务，所以我们可以使用更简单的`HyperLink`控件。请记住，我们通过在表单中插入以下代码向页面添加了一个`HyperLink`控件:

```
<asp:HyperLink NavigateUrl="index.aspx" runat="server"     

    Text="Home" />
```

这将添加一个显示文本“Home”的链接

打开文本编辑器，用 ASP.NET 页面所需的标准 HTML 标签创建一个新文件，包括一个带有`runat="server"`属性的空表单。在这个表单中，为帮助台、员工商店、时事通讯存档、员工目录、地址簿和管理工具添加`HyperLink`控件，如下所示:

**例 4.6。`index.aspx`(节选)**

```
<!-- HyperLink controls for navigation -->    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="index.aspx" runat="server" Text="Home"    

    />    

<br />     

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="helpdesk.aspx" runat="server"    

    Text="HelpDesk" />    

<br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="employeestore.aspx" runat="server"    

    Text="Employee Store" />    

<br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="newsletterarchive.aspx" runat="server"    

    Text="Newsletter Archive" />    

<br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="employeedirectory.aspx" runat="server"    

    Text="Employee Directory" />    

<br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="addressbook.aspx" runat="server"    

    Text="Address Book" />    

<br /><br />    

<img src="Images/book_closed.gif" width="16" height="16" alt="+"    

    />    

<asp:HyperLink NavigateUrl="admintools.aspx" runat="server"    

    Text="Admin Tools" />    

<!-- End HyperLink controls -->
```

一旦链接被添加到页面上，并且你已经把`book_closed.gif`文件放在一个叫做`Images`的子目录中，你可以保存你的工作(作为`index.aspx`)并且在你的浏览器中查看结果。然而，在现阶段，它看起来相当平淡无奇。我们需要的是一些漂亮的图形来提供视觉吸引力！尽管现代的 Web 设计实践会让我们在页面布局和视觉设计中使用 CSS，但为了专注于应用程序的服务器端方面，我们在这里将求助于 HTML 表格。

打开`index.aspx`并在页面主体的最开始创建以下两个常规(即非服务器端)HTML 表:

**例 4.7。`index.aspx`(节选)**

```
<body>    

<form runat="server">    

<table width="100%" border="0" cellspacing="0" cellpadding="0"    

    background="Images/header_bg.gif">    

  <tr>    

    <td><img src="Images/header_top.gif" width="450" height="142"    

        alt="the official dorknozzle company intranet"    

        /></td>    

  </tr>    

</table>    

<table width="100%" border="0" cellspacing="0" cellpadding="0">    

  <tr>    

    <td width="157"><img src="Images/header_bottom.gif"    

        width="157" height="37" alt="" /></td>    

    <td></td>    

  </tr>    

</table>
```

我们也想把我们的链接放在一个表中。在那里，我们将向主索引页面添加一些新闻条目。再次打开`index.aspx`，将下面的 HTML 表格放在我们已经添加的链接周围:

**例 4.8。`index.aspx`(节选)**

```
<table width="100%" border="0" cellspacing="0" cellpadding="10">    

  <tr>     

    <td valign="top" width="160">    

      <!-- HyperLink controls for navigation -->    

      ...    

      <!-- End HyperLink controls -->    

    </td>    

    <td valign="top">    

      <h1>Company News:</h1>    

      <p>We'll add some news later.</p>    

      <h1>Company Events:</h1>    

      <p>We'll add company events later.</p>    

    </td>    

  </tr>    

</table>    

</form>    

</body>    

</html>
```

结果将类似于图 4.7。

**图 4.7。为 Intranet 导航菜单添加超链接控件。**

![1329_fig7](img/47a134b2a3f8bbfeb49d9ac0c18b3698.png)

一些精心选择的图形所能带来的不同难道不令人惊讶吗？不要忘记将下载的图片放在`Images`子目录中。当然，您可以在代码档案中找到完整的源代码，尽管我建议您在我们进行的过程中自己键入代码，以供练习。

***创建企业样式表***

如果你不介意标准网页的普通外观，那么你可以跳过这一部分。但是，如果您不喜欢标准的蓝色超链接、黑色、Times New Roman 文本和斜面表单控件，这一节正是为您准备的。

正如您已经读到的，样式表为开发人员提供了对 Web 应用程序“外观”的灵活性和控制。在这一节中，我们将探索向我们虚构的 intranet 应用程序添加一个可定制的样式表。我们将为应用程序中的以下元素定义样式:

*   超链接
*   文本(包括正文和标题)
*   盒装控件(包括文本框和下拉菜单)

您可以从创建样式所在的 CSS 文件开始。我已经打开了记事本，并立即将文件保存为应用程序根目录下的`styles.css`,如图 4.8 所示。

**图 4.8。打开记事本，将文件保存为应用程序文件夹根目录下的 styles.css。**

![1329_fig8](img/ec6773dd08440466dd2bce5e85c005fc.png)

现在，让我们将一些样式属性应用于以下标签:

*   `body`
*   `p`
*   `h1`
*   `a:link`
*   `a:hover`

您会注意到这个列表中的`a:link`和`a:hover`项，严格来说它们不是标签。在 CSS 的世界里，这些被称为*伪元素*。`a:link`将选择范围缩小到作为链接的<和>标签(相对于作为目标的`<a name="...">`标签)。给`a:hover`分配属性只会将那些属性应用到用户鼠标悬停的链接上。

我们还将为某些不直接映射到特定 HTML 标签的 Web 控件定义几个类:

```
.textbox
```

对于`<asp:TextBox>`控件，当发送到浏览器时，它们成为`<input type="text">`和`<textarea>`标签。

```
.button
```

对于`<asp:Button>`控件，它们成为`<input type="button">`、`<input type="submit">`和`<input type="reset">`标签。

```
.dropdownmenu
```

对于成为`<select>`标签的`<asp:DropDownList>`控件。

下面是 CSS 规则的代码，它将把所需的基本格式应用到我们的站点。在您的`styles.css`文件中键入以下内容:

```
body {    

  background: #FFFFFF;    

  color: #000000;    

  margin: 0;    

  padding: 0;    

}    

p {    

  font-family: Arial;    

  font-size: 12px;    

}    

h1 {    

  font-family: Arial;    

  font-size: 14px;    

  color: #000000;    

}    

a:link {    

  font-family: Arial;    

  font-size: 12px;    

  color: #000000;    

}     

a:hover {     

  font-family: Arial;    

  font-size: 12px;    

  color: #FF0000;    

}    

.textbox {    

  font-family: Arial;    

  font-size: 12px;    

  border: 1px solid black;    

}    

.button {    

  font-family: Arial;    

  border: 1px solid black;    

  background-color: #CCCCCC;    

}    

.dropdownmenu {    

  font-family: Arial;    

  font-size: 12px;    

  background-color: #CCCCCC;    

}
```

既然已经创建了样式表文件，我们可以通过将下面一行插入到文档的`<head>`标签中来将样式表文件链接到`index.aspx`:

```
<link href="styles.css" rel="stylesheet" />
```

当我们创建 CSS 类时，我们需要将我们定义的 CSS 类(`textbox`、`button`和`dropdownmenu`)分配给相关的控件，但是现在我们简单的 HTML 模板将自动受益于我们重新定义的标签。

记住，我们并不局限于这些风格。如果在我们应用程序的开发过程中，我们决定添加更多的样式，我们只需要打开`styles.css`文件并根据需要添加它们。

此时，您可以保存您的工作，并在浏览器中查看它。

***设计服务台应用程序的 Web 表单***

该项目的最后一部分是添加员工服务台请求 Web 表单。这将是一个网页，允许我们虚构的员工报告硬件、软件和工作站问题。网络表单将被安排为一系列简单的步骤，用户可以按照这些步骤来报告他们的问题:

*   从预定义的潜在问题领域类别中挑选。(`DropDownList`控制)
*   从类别中的预定义主题中挑选。(`DropDownList`控制)
*   键入问题的描述。(多线`TextBox`控制)
*   提交请求。(`Button`控制)

您可以简单地复制`index.aspx`并将其重命名为`helpdesk.aspx`，而不是创建一个新的空白页面并重新键入所有代码(或者如果它已经在您的编辑器中打开，则用新名称保存一个副本)。为适应帮助台界面，代码中唯一要改变的部分是主体中的最后一个表——包含关于`index.aspx`的新闻条目的表。其他一切都保持不变，因为我们希望所有的页面都有一个单一的外观(我们将在后面的章节中看到更好的方法……)。更改表中的最后一列，以创建两个下拉列表、一个多行文本框和一个按钮，如下所示:

`<!-- End HyperLink controls -->    
   </td>    
   <td valign="top">    
     <h1>Employee HelpDesk Request</h1>    
     <p>Problem Category:<br />    
       <asp:DropDownList id="ddlCategory" CssClass="dropdownmenu"    
           runat="server" /></p>    
     <p>Problem Subject:<br />    
       <asp:DropDownList id="ddlSubject" CssClass="dropdownmenu"    
           runat="server" /></p>    
     <p>Problem Description:<br />    
       <asp:TextBox id="txtDescription" CssClass="textbox"    
           Columns="40" Rows="4" TextMode="MultiLine"    
           runat="server" /></p>    
     <p><asp:Button id="btnSubmit" CssClass="button"    
           Text="Submit Request" runat="server" /></p>    
   </td>`

请注意我们是如何将 CSS 类应用到适当的控件中的。

不要担心`DropDownList`控件没有与之关联的项目——类别和主题将在数据库表中预定义。稍后，我们将把这些数据库表绑定到它们各自的控件。

完成后，保存您的工作并在浏览器中查看。

##### 摘要

在本章中，我们讨论了 HTML 控件、Web 表单和 Web 控件。我们还探讨了如何链接页面，以及如何向控件添加样式。你甚至建立了你的第一个项目，把你在这一章和前几章中学到的信息放在一起。

您的 Web 应用程序将主要关注 Web 控件。在下一章，我们将学习如何通过使用 ASP.NET 验证控件来检查那些 Web 控件上的用户输入…

以上就是 SitePoint 的前四章*使用 c#&VB.NET*构建你自己的 ASP.NET 网站。这份关于建立你自己的 ASP.NET 网站的完整指南现已发布。[点击此处](https://www.sitepoint.com/books/aspnet1/)了解更多信息。

这本书不仅包含您刚刚阅读的四章，还包含十三章，涵盖了 Web 表单、验证控件、数据库设计、ADO.NET、构建购物车、错误处理、安全性和身份验证、XML Web services 等等。这本书还包括了一系列精彩的参考资料，这使得它成为你在 ASP.NET 从事网络开发时不可或缺的案头参考。有关更多信息，[请参见本书第](https://www.sitepoint.com/books/aspnet1/)页。

## 分享这篇文章