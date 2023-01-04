# 初学者的 HTML–第 1 部分

> 原文：<https://www.sitepoint.com/beginners-html-1/>

HTML 是目前网络上最流行的语言。没有它，互联网就不会像现在这样。HTML 可以被认为是互联网的组成部分——就像房子的砖块和砂浆。所以是每个 Web 开发者必须要学习的！

人们可以选择遵循许多不同的 HTML 标准，但我们将使用 HTML 4.01。这不是最新的标准，但却是互联网上最广泛采用的标准

##### 入门指南

学习 HTML 需要几样东西:

*   文本编辑器(例如:EditPlus、记事本等。但*不是*写字板或 Word)
*   浏览器(例如:Internet Explorer、Opera 等)

您将在文本编辑器中编写 HTML 代码，然后在 Web 浏览器中查看它的形状。在本教程中，我将使用 EditPlus 作为我的文本编辑器，使用 Internet Explorer 5.5 作为我的浏览器，但是欢迎您使用任何您觉得合适的工具。

HTML 由简单的“标签”(包含在< and >中)和文本组成。标签控制页面的布局和外观，并在查看文档时由浏览器解释。所有标签的格式都是“`<name>`”——尖括号之外的任何内容都被认为是您希望访问者看到的文本。对于每一个标签(有几个例外——但我们稍后会谈到),都必须有一个结束标签。这与开始标记完全相同，只是在标记名称前加了一个“/”，例如“`</tag>`”。

每个 HTML 页面都必须符合这个基本布局:

`<html>
 <head>
   <title>Title</title>
 </head>
 <body>
   content
 </body>
</html>`

这一开始可能会让人感到困惑，所以让我们一行一行地来看一下:

```
<html> This is the opening tag of every HTML page - it tells the browser that the page uses HTML.

<head> This tells the browser that the header has started. The header contains information about the page that doesn't display in the main body of the browser window.

```

```
<title>Title</title>	The <title> tag tells the browser that the text that follows is the title of the page. It will appear in the title bar of the browser and also in the task bar (next to the start menu on Windows computers).

```

```
</head> This signifies the end of the header - anything that appears after this, and is not a tag, will be displayed to the user.

```

```
<body> This is the start of the main body of the page.

```

```
</body> The end of the main body of the page.

```

```
</html> The end of the entire Web page.

看到了吗？毕竟没那么难！这是(或者应该是)每个 HTML 页面的基本布局。该页面格式良好(带缩进)，且完全符合最新的 W3C 标准。

格式和结构

现在你已经知道了一个基本的 HTML 页面是什么样子，是时候放一些文本进去，看看它们是如何组合在一起的。因此，加载您的文本编辑器，将我们之前看到的结构键入(或剪切并粘贴)到这个空白页中。然后在页面正文中添加一些文本。它可以是你喜欢的任何东西——就像你在 Word 中输入一样格式化它。之后，您应该有一个基本上类似这样的页面:

```

```
<html> 

  <head> 

    <title>My First Web Page</title> 

  </head> 

  <body> 

    Welcome to my first Web page! 

    The purpose of this Web page is to learn some HTML so that 

    perhaps I can make a really fantastic Web page 

    later on. 

    Please come back and visit soon, 

    James 

  </body> 

</html>
```

现在，将文件保存为“网页. html”或类似的文件。如果您使用记事本，您将需要把文件名放在引号中(“filename.html”)，以便正确保存。然后在 Windows 资源管理器中双击该文件，它将打开您选择的浏览器，可能是 Internet Explorer。但是，与您之前键入的格式良好、段落分明的文本不同，所有文本都将显示在一行中。这是因为 HTML 不注意代码本身的格式。你可以用更多的 HTML 标签来解决这个问题，比如:

*   这个标签创建一个文本段落，在它之前留一个空行。
*   这是换行符——它开始一个新的行。这是我们第一个不需要结束标签的标签例子(也就是说，没有所谓的`</br>`标签)。

因此，应用标签后，您的页面看起来应该是这样的:

```
<html>  

  <head> 

    <title>My First Web Page</title> 

  </head> 

  <body> 

    <p> 

      Welcome to my first web page!   

    </p> 

    <p> 

      The purpose of this web page is to learn some HTML so that 

      perhaps I can make a really fantastic web page 

      later on. 

    </p> 

    <p> 

      Please come back and visit soon, 

      <br> 

      James 

    </p> 

  </body> 

</html> 

现在，你可能已经发现了一些东西。我包含了`<p>`和`</p>`标签来表示段落的开始和结束。虽然这不是完全必要的，但是这样做是很好的实践，并且意味着您将完全符合未来的 HTML 标准。为了整洁，我还缩进了代码，这当然不是必须的，但我总是这样做，以使代码更容易阅读和修改。

现在你知道了如何构建一个基本的 HTML 页面，如何添加换行符，以及创建一个普通的网页需要哪些标签。下一步是考虑使用不同的字体和字体大小，以及格式化页面中文本的颜色。

##### 字体和文本格式

既然您已经可以创建一个基本的 Web 页面，那么是时候让它看起来更漂亮一点了。这可能包括使用你自己的字体、颜色和文本大小来使页面对读者更有吸引力。

所有字体的改变都是用`<font>`标签完成的。有许多属性可以和`<font>`标签一起使用来控制文本的外观。标签本身包含一个属性(如`<font color="red">`)。标签是“字体”，属性是“颜色”，值是“红色”属性用在许多不同的标签中，不仅仅是字体。

有许多不同的标签可以用来指定字体的各个方面:

*   `<font face="Verdana">`这允许你指定将要使用的实际字体。但是，该字体只有在用户的计算机上存在时才会显示，所以选择一种大多数访问者都会有的流行字体。
*   `<font color="red">`这允许你指定文本的颜色。大多数标准颜色名称(如红色、蓝色等)都可以，尽管有一些特殊的代码提供了更广泛的颜色。我们稍后将详细讨论这些内容！
*   `<font size="4">`该属性允许您指定文本的大小。它们不是你在文字处理器上使用的尺寸，而是特殊尺寸——所以你可能需要做一些实验来得到你想要的尺寸。

您也可以使用属性的组合，例如:

<q>`<font face="Times New Roman" color="yellow" size="4">`</q>

现在让我们在页面中添加一些格式。这是现在的页面，没有任何格式:

 `<html>   

  <head>  

    <title>My First Web Page</title>  

  </head>  

  <body>  

    <p>  

      Welcome to my first Web page!    

    </p>  

    <p>  

      The purpose of this Web page is to learn some HTML  

      so that perhaps I can make a really fantastic Web  

      page later on.  

    </p>  

    <p>  

      Please come back and visit soon,  

      <br>  

      James  

    </p>  

  </body>  

</html>`

对于字体和颜色:

```
<html>   

  <head>  

    <title>My First Web Page</title>  

  </head>  

  <body>  

    <p>  

      <font size="4" face="Verdana, Arial" color="red">  

        Welcome to my first Web page!    

      </font>  

    </p>  

    <p>  

      <font face="Arial">  

        The purpose of this Web page is to learn some  

        HTML so that perhaps I can make a really  

        fantastic Web page later on.  

      </font>  

    </p>  

    <p>  

      <font face="Arial">  

        Please come back and visit soon,  

      </font>  

      <br>  

      <font color="blue" size="+1">  

        James  

      </font>  

    </p>  

  </body>  

</html> 

现在，您可能会注意到该代码块中有一些不熟悉的东西。首先，第 7 行包含字体 face 的一个不寻常的值:

`<font face="Verdana, Arial">`

这告诉浏览器使用 Verdana 作为其第一个字体选项，但是如果查看者的计算机上没有安装 Verdana，则尝试使用 Arial。如果 Verdana 和 Arial 都不在用户的计算机上，那么将使用浏览器的默认字体(通常类似于 Times New Roman)。

使用的第二个技巧是在最终的字体标签中。我用过:`<font size="+1">`。这告诉浏览器将字体大小设置为比正常字体大一号。默认的字体大小在不同的浏览器和电脑上可能会有所不同，所以最好使用这种技术(称为相对大小)来确保每个人都获得正确的文本大小。您可以指定+1 使其变大，指定-1 使其变小。

我们的页面现在开始成形了。但是仍然缺少一些东西，比如粗体和斜体。你可以设置文本为粗体或斜体，用-你猜对了-更多的 HTML 标签！

	*   `<b> text </b>`加粗*   `<i> text </i>`斜体*   `<u> text </u>`下划线
这是添加了所有内容的最后一页:

```
<html>   

  <head>  

    <title>My First Web Page</title>  

  </head>  

  <body>  

    <p>  

      <font size="4" face="Verdana, Arial" color="red">  

        <u>  

          Welcome to my first Web page!    

        </u>  

      </font>  

    </p>  

    <p>  

      <font face="Arial">  

        The purpose of this Web page is to learn some  

        HTML so that perhaps I can make a really  

        fantastic Web page later on.  

      </font>  

    </p>  

    <p>  

      <font face="Arial">  

        Please come back and visit soon,  

      </font>  

      <br>  

      <font color="blue" size="+1">  

        <u><i>James</i></u>  

      </font>  

    </p>  

  </body>   

</html>
```

还有另外一种改变页面外观的技术你应该知道:级联样式表，简称 CSS。CSS 是一种极其强大但相当复杂的指定所有格式的方式。这篇教程太深入了，所以我要给你指出这篇教程学习 CSS 的方向。
文章是 [CSS 很容易！](http://www.webmasterbase.com/article.php/309)，作者是凯文·杨克。它应该帮助你掌握 CSS 的基本知识，并希望在你的一些页面中使用它。我在本教程中使用了它的替代品(即`<font>`标签),因为 CSS 与一些旧浏览器(3.0 及更早版本)不兼容。
在本系列的下一部分，我们将学习如何创建到其他网页的链接，以及到我们自己页面的其他部分的链接。

```

## 分享这篇文章

```