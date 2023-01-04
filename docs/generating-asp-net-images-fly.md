# 动态生成 ASP.NET 图像文章

> 原文：<https://www.sitepoint.com/generating-asp-net-images-fly/>

**ASP.NET 和。NET 框架使得动态生成图像的任务变得非常容易。使用经典 ASP，开发人员被迫使用第三方组件，如 ASPImage 和 ASPPicture，进行动态图像操作。幸运的是，有了 ASP.NET，那些日子已经过去了。各种图像处理功能现在直接内置于。NET 框架类。**

的。NET Framework 为图像生成和操作提供了几个类。ASP.NET 开发人员可以使用这些类来绘制各种形状，创建文本，并生成许多不同格式的图像，包括 GIF、JPEG、PNG 等。

现在，对于一个不可避免的问题:为什么你需要为你的 ASP.NET 网页动态生成图像？以下是动态图像生成的几个实际应用:

*   从全尺寸图库图像生成缩略图图库。
*   基于动态用户输入或数据库数据创建动态图表。
*   生成带有随机文本(访问代码)的图像，用于您的用户注册页面，以防止自动注册。

##### 动态创建文本图像

我们用两个吧。位于系统中的. NET 类。绘图命名空间，以说明如何创建图像的飞行与 ASP.NET。这两个框架类是 System。绘图.位图和系统.绘图.图形

为了演示简单的动态 ASP.NET 图像生成，我们将创建一个包含两个下拉框、一个文本框和一个按钮的 ASP.NET 网页。第一个下拉框允许我们选择图像的背景颜色，而第二个下拉框允许我们选择字体。文本框将接收将在图像上显示的文本，按钮将生成图像。下面是显示这些表单元素的代码:

```
<form runat="server"> 

<asp:TextBox runat="server" id="Text" /> 

<br><br> 

<asp:dropdownlist runat="server" id="BackgroundColor"> 

  <asp:ListItem Value="red">Red</asp:ListItem> 

  <asp:ListItem Value="green">Green</asp:ListItem> 

  <asp:ListItem Value="navy">Navy</asp:ListItem> 

  <asp:ListItem Value="orange">Orange</asp:ListItem> 

</asp:dropdownlist> 

<asp:dropdownlist runat="server" id="Font"> 

  <asp:ListItem Value="Arial">Arial</asp:ListItem> 

  <asp:ListItem Value="Verdana">Verdana</asp:ListItem> 

  <asp:ListItem Value="Courier">Courier</asp:ListItem> 

  <asp:ListItem Value="Times New Roman">Times New Roman</asp:ListItem> 

</asp:dropdownlist> 

<br><br> 

<asp:Button runat="Server" id="SubmitButton" Text="Generate Image" /> 

</form>
```

每当您请求一个 ASP.NET 页面时，服务器上就会触发`Page_Load`事件，并调用`Page_Load`子例程。在这个子例程中，您可以检查对页面的请求是否是通过数据回发生成的(例如，用户单击了 submit 按钮)，用户是否从另一个页面进入该页面，或者用户是否只是在浏览器的地址栏中键入了页面 URL。

在这个例子中，知道页面是否被回发是很重要的，因为我们希望只在用户点击提交按钮时生成一个动态图像，而不是在他或她第一次加载页面时。如果 APS.NET 页面是为响应客户端回发而加载的，则`Page.IsPostBack`属性为`true`；否则，就是`false`。所以我们在 Page_Load 子例程中做的第一件事是检查`Page.IsPostBack`的值:

```
If Page.IsPostBack Then
```

如果值是`false`，我们不做任何事情，页面仅仅显示我们上面声明的 ASP.NET 控件。如果值是`true`，我们必须生成一个图像。这是怎么做的。

创建动态生成图像的第一步是创建一个新的`Bitmap`对象，并指定我们正在生成的图像的宽度和高度:

```
Dim oBitmap As Bitmap = New Bitmap(468, 60)
```

下一步是创建一个新的`Graphics`对象，这将允许我们在位图上绘图:

```
Dim oGraphic As Graphics = Graphics.FromImage(oBitmap)
```

然后我们声明一个类型为`System.Drawing.Color`(代表一种 RGB 颜色)的结构，它将定义我们的动态图像的背景颜色:

```
Dim oColor As System.Drawing.Color
```

现在，我们需要从两个下拉框和文本框中获取回发到页面的值:

```
Dim sColor As String = Request("BackgroundColor") 

Dim sText As String = Request("Text") 

Dim sFont As String = Request("Font")
```

我们运行 Select Case 语句来设置`oColor`的值(我们图像的背景颜色)，这取决于`sColor`的值:

```
Select Case sColor 

Case "red" 

          oColor = Color.Red 

  Case "green" 

          oColor = Color.Green 

  Case "navy" 

    oColor = Color.Navy 

  Case "orange" 

    oColor = Color.Orange 

  Case Else 

    oColor = Color.Gray 

End Select
```

接下来，我们创建两个画笔，这将有助于我们绘制我们的形象。第一个，`oBrush`，将用于绘制图像的背景:

```
Dim oBrush As New SolidBrush(oColor)
```

第二个画笔将用于绘制用户输入的文本；我们将画笔颜色硬编码为白色:

```
Dim oBrushWrite As New SolidBrush(Color.White)
```

我们将使用第一个笔刷来填充图像矩形，因为新位图的默认颜色是黑色。我们调用`oGraphic`的`FillRectangle`方法，以用户选择的颜色(oColor)绘制背景:

```
oGraphic.FillRectangle(oBrush, 0, 0, 468, 60)
```

到目前为止，我们已经改变了图像的背景颜色，但是我们还没有写任何文本。我们将使用`oGraphic`对象的`DrawString`方法来做到这一点。`DrawString`方法有几个重载。这是我们要用的一个:

```
DrawString(String, Font, Brush, FPoint)
```

第一个参数就是我们要写的字符串；第二个参数定义要使用的字体；第三个参数定义了一个笔刷(这将是我们上面创建的第二个笔刷)；最后一个参数定义了相对于图像左上角的绘制起点。然而，在我们调用`DrawString`方法之前，我们需要创建一个`Font`类的实例，以及定义绘图起点的`FPoint`结构。

用户为文本选择了一种字体，所以我们将`sFont`作为第一个参数传递，并硬编码第二个参数，它定义了字体大小:

```
Dim oFont As New Font(sFont, 13)
```

然后，我们定义一个类型为`PointF`的结构，它表示一对有序的 X 和 Y 坐标，定义了二维平面中的一个点。同样，我们只是对文本的绘制起点进行硬编码，因为在我们的示例中这是不可配置的:

```
Dim oPoint As New PointF(5F, 5F)
```

我们现在可以调用`DrawString`方法并绘制我们的文本:

```
oGraphic.DrawString(sText, oFont, oBrushWrite, oPoint)
```

剩下要做的最后一件事是在服务器上保存新创建的映像:

```
oBitmap.Save(Server.MapPath("generated_image.jpg"), ImageFormat.Jpeg)
```

这段代码将图像保存为 JPEG 格式，但是您也可以将其保存为 GIF、TIFF、PNG 或任何其他流行的图像格式。

最后，我们显示一个指向新图像的链接:

```
Response.Write("View the generated image <a target=""_blank"" href=""generated_image.jpg"">here</a>")
```

如果您不想将图像保存在服务器文件系统上，可以使用以下代码将其直接发送到浏览器:

```
Response.ContentType = "image/jpeg" 

oBitmap.Save (Response.OutputStream, ImageFormat.Jpeg)
```

事实上，您可以将生成动态图像的代码放在一个单独的 ASP.NET 页面中，并从其他 ASP.NET 页面中引用该页面，如下所示:

```
<img src="image_generate.aspx">
```

当然，您的`image_generate.aspx`页面必须有适当的图像内容类型，例如:

```
Response.ContentType = "image/jpeg"
```

或者:

```
Response.ContentType = "image/gif"
```

您可以在本文末尾找到该页面的完整源代码。

##### 动态创建缩略图

现在让我们来看看图片，一步步地了解在 ASP.NET 动态创建缩略图的过程。

首先，我们将创建一个名为`thumbnail.aspx`的 ASP.NET 页面并导入系统。图纸和系统。制图。成像类:

```
<%@ Import Namespace="System.Drawing" %> 

<%@ Import Namespace="System.Drawing.Imaging" %>
```

thumbnail.aspx 页面将在其查询字符串中获得 3 个参数:

*   `image` —将被调整大小为缩略图的图像文件的文件路径
*   `w` —缩略图所需的宽度
*   `h` —缩略图所需的高度

在`Page_Load`子例程中，我们声明 2 个系统。绘图.图像对象— `oImage`和`oThumbnail`。然后，我们用下面一行获得图像文件的绝对路径:

```
sImagePath = Server.MapPath(Request.QueryString("image")) 
```

注意:请注意这里的安全含义。通过在查询字符串中传递一个棘手的图像参数，黑客可以说服脚本尝试访问和显示服务器上的任何文件，无论它是否是您网站的一部分。为了防止这种情况，您需要确保您的文件系统权限配置正确；此外，在这个脚本中，参数的一些验证逻辑不会出错。然而，出于本文的目的，我们将坚持这种幼稚的方法。

下一步，我们实际上使用 FromFile 方法创建 oImage 对象，并传递我们在上一步中获得的绝对文件路径。

```
oImage = oImage.FromFile(sImagePath)
```

然后我们得到期望的缩略图宽度和高度:

```
iWidth = Request.QueryString("w")  

iHeight = Request.QueryString("h")
```

下一步是使用`GetThumbnailImage method`，它从一个图像对象中获取一个缩略图:

```
oThumbnail = oImage.GetThumbnailImage(iWidth, iHeight, Nothing, Nothing)
```

我们传递缩略图的宽度和高度作为前两个参数。对于本文来说，不传递任何东西作为`GetThumbnailImage`方法的第三和第四个参数就足够了，不需要任何进一步的解释。

最后，和前面的例子一样，我们使用下面的代码将图像直接发送到浏览器:

```
Response.ContentType = "image/jpeg" 

oThumbnail.Save (Response.OutputStream, ImageFormat.Jpeg)
```

就这么简单！

这两个例子的完整功能的 VB.NET 源代码可以从这里下载。

## 分享这篇文章