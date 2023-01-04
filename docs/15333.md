# 初学者的 HTML–第 3 部分

> 原文：<https://www.sitepoint.com/beginners-html-3/>

欢迎来到本教程的第 3 部分！在第二部的结尾，我给你们留下了一个挑战。所以我们先来探讨一下解决方案。

##### 挑战解决方案

我上周设置的挑战是让你给我们创建的页面添加一些链接，这样用户就可以在我们的 3 个页面之间轻松导航。我们有三个页面——主页、链接页和图片页。

在主页上，您应该已经添加了一些类似如下的代码:

```
 <a href="links.html">Favorite Links</a> 

  <br> 

  <a href="pictures.html">Pictures</a>
```

在链接页面上，您应该看到类似于以下内容的内容:

```
 <a href="index.html">Main Page</a> 

  <br> 

  <a href="pictures.html>Pictures</a>
```

最后，在图片页面上，您应该添加如下代码:

```
 <a href="index.html">Main Page</a> 

  <br> 

  <a href="links.html">Favorite Links</a>
```

所以现在，如果你把这些页面中的任何一个载入你的浏览器，你会看到你(和你的访问者！)可以轻松地在页面之间移动。

##### 桌子

你所阅读的一切都是有条理的，从你口袋里无聊的购物清单，到你桌上最新的杂志。就像这些项目一样，HTML 页面应该有一个结构。我相信你可以轻而易举地列出一份购物清单，但是一本杂志怎么样？如果我们按照 HTML 最初创造者的意图使用 HTML，我们今天看到的令人惊叹的图形密集型网站就不可能出现——我们所能制作的只会是简单、乏味的页面。

表格构成了当今几乎所有网页的基本布局。在 SitePoint.com 的主页上，我数出了 34 个独立的表格，所有表格都相互嵌入。这显示了表格对于网页的设计和内容元素是多么的重要。

要创建一个表，您需要使用标签，以及它对应的

标签。您可以在标签中指定许多可选属性，但这些不是必需的(现在还不是！).

一旦有了标签，下一个标签就是标签。标签创建一个表格行，其中可以包含一个或多个信息单元格(或列)。要创建这些可以包含几乎任何其他 HTML 元素的单个单元格，可以使用

| Mark. You can create as many cells as you need, but each row in the table should have the same number of cells as other rows. So let's look at a basic table: |

```
<table> 

  <tr> 

    <td> 

      <b>Name</b> 

    </td> 

    <td> 

      <b>E-Mail Address</b> 

    </td> 

    <td>       

      <b>Phone Number</b> 

    </td> 

  </tr> 

  <tr> 

    <td> 

      Joe Bloggs 

    </td> 

    <td>   

      <a href="mailto:joe.bloggs@hotmail.com">joe.bloggs@hotmail.com</a> 

    </td> 

    <td> 

      555 1234 

    </td> 

  </tr> 

  <tr> 

    <td> 

      John Smith 

    </td> 

    <td> 

      <a href="mailto:john.smith@yahoo.com">john.smith@yahoo.com</a> 

    </td> 

    <td> 

      555 4321 

    </td> 

  </tr> 

</table>
```

现在，我知道这看起来很混乱，所以让我们一部分一部分的来。首先，我们有标签，后面是 3 个单独的表格行。让我们仔细看看其中的一行:

```
 <tr> 

    <td> 

      <b>Name</b> 

    </td> 

    <td> 

      <b>E-Mail Address</b> 

    </td> 

    <td> 

      <b>Phone Number</b> 

    </td> 

  </tr>
```

首先我们有标签，它开始新的一行。然后我们有一个Tag, which starts a new cell in the row. It can be anything in this cell, but in this example, it contains a bold title "Name". Then it's over.标签，它结束表格单元格，为下一个做准备。然后还有两个单元格，后面是行尾。

该示例将生成如下表格:

| **名称** | **电子邮件地址** | **电话号码** |
| 普通人 | [joe.bloggs@hotmail.com](mailto:joe.bloggs@hotmail.com) | 555 1234 |
| 约翰·史密斯 | [john.smith@yahoo.com](mailto:john.smith@yahoo.com) | 555 4321 |

如您所见，它有 3 行，每行有 3 列(或单元格)。

##### 表大小

默认情况下，表格的大小刚好能容纳它所包含的元素，不会更大或更小。您可以使用各种“高度”和“宽度”属性来更改此默认值。您可以用像素或浏览器窗口的百分比来指定高度或宽度。例如:

*   将创建一个高度为 500，宽度为 247 的表格
*   将创建一个与屏幕一样宽的表格，但其高度只能容纳它所容纳的元素

现在你面临一个挑战。向我们上面看到的示例表中添加另一行，列出另一个虚构人物的联系方式。答案在下一页。

***牌桌挑战方案***

您的新表(多了一行)看起来应该是这样的:

```
<table>  

  <tr>  

    <td>  

      <b>Name</b>  

    </td>  

    <td>  

      <b>E-Mail Address</b>  

    </td>  

    <td>  

      <b>Phone Number</b>  

    </td>  

  </tr>  

  <tr>  

    <td>  

      Joe Bloggs  

    </td>  

    <td>  

      <a href="mailto:joe.bloggs@hotmail.com">joe.bloggs@hotmail.com</a>  

    </td>  

    <td>  

      555 1234  

    </td>  

  </tr>  

  <tr>  

    <td>  

      John Smith  

    </td>  

    <td>  

      <a href="mailto:john.smith@yahoo.com">john.smith@yahoo.com</a>  

    </td>  

    <td>  

      555 4321  

    </td>  

  </tr>  

  <tr>  

    <td>  

      James Bond  

    </td>  

    <td>  

      <a href="mailto:jamesbond@yahoo.com">jamesbond@yahoo.com</a>  

    </td>  

    <td>  

      857 4630  

  </tr>  

</table>
```

如果不是这样，那么回到上一页，再次查看单行的示例，然后尝试添加另一行。如果你答对了，那就做得好！

表格实际上可以包含任何其他 HTML 标记或元素，例如图像或链接。让我们来看看另一个例子——一家销售椅子的商店的价目表。

```
<html>  

  <head>  

    <title>Chairs</title>  

  </head>  

  <body>  

    <table width="50%" align="center">  

      <tr>  

        <td>  

          <b>Title</b>  

        </td>  

        <td>  

          <b>Picture</b>  

        </td>  

        <td>  

          <b>Price</b>  

        </td>  

      </tr>  

      <tr>  

        <td>  

          Small Chair  

        </td>  

        <td>  

          <img src="small.jpg" alt="small chair">  

        </td>  

        <td>  

          $39.99  

        </td>  

      </tr>  

      <tr>  

        <td>  

          Large Chair  

        </td>  

        <td>  

          <img src="large.jpg" alt="large chair">  

        </td>  

        <td>  

           $99.99  

        </td>  

      </tr>  

    </table>  

  </body>  

</html>
```

对于一些练习，看一下代码，看看你是否能算出它有多少行，每行有多少个单元格。一旦你做到了这一点，请继续阅读…

同样，有 3 行(标题行，然后一个是小椅子，一个是大椅子)，每行有 3 个单元格(名称、图片和价格)。最后一个练习是，尝试在表格中每一行添加一个额外的单元格，以适应椅子的大小。请记住，您需要在每一行中添加正确的标签，否则它将不起作用。

完成后的表格应该如下所示:

```
<html>  

  <head>  

    <title>Chairs</title>  

  </head>  

  <body>  

    <table width="50%" align="center">  

      <tr>  

        <td>  

          <b>Title</b>  

        </td>  

        <td>  

         <b>Picture</b>  

        </td>  

        <td>  

          <b>Price</b>  

        </td>  

        <td>  

          <b>Size</b>  

        </td>  

      </tr>  

      <tr>  

        <td>  

          Small Chair  

        </td>  

        <td>  

          <img src="small.jpg" alt="small chair">  

        </td>  

        <td>  

          $39.99  

        </td>  

        <td>  

          2 feet  

        </td>  

      </tr>  

      <tr>  

        <td>  

          Large Chair  

        </td>  

        <td>  

          <img src="large.jpg" alt="large chair">  

        </td>  

        <td>  

          $99.99  

        </td>  

        <td>  

          4 feet  

        </td>  

      </tr>  

    </table>  

  </body>  

</html>
```

##### 读者反馈

正当我准备结束本教程时，我收到了 Marie 的一封邮件，她已经阅读了本教程的前几部分，并对<font>标签有一个问题。她想知道在哪里可以找到常用十六进制颜色代码的图表或列表。为了澄清这一点，你可能记得字体颜色通常使用一系列数字和字母，例如:<font color="#00000">代表黑色。</font></font>

虽然你可以用像“红色”或“蓝色”这样的名字来代表普通的颜色，但对于不同的颜色或色调，你需要使用这些颜色代码。我四处寻找图表，我能找到的最好的是这张图。我希望这有助于回答你的问题，玛丽！

##### 现在去哪里？

现在，您已经对 HTML 及其相关概念有了基本的了解，是时候扩展一下这些知识了。有很多方面你可以学习——为此，我建议你看一下 Laura Lemay 的 [Sams 在 21 天内用 HTML 和 XHTML 自学网络出版](http://www.amazon.com/exec/obidos/ASIN/0672320770/qid=996132896/sr=2-1/104-9041326-3055941)。

或者你可以学习一些其他的语言来帮助网络发展。尝试一些关于服务器端语言的教程，它们会给你比 HTML 更大的能力和灵活性，甚至可能超出你的想象:

*   [ASP 入门](http://www.webmasterbase.com/article/371)
*   使用 PHP 和 MySQL 建立一个数据库驱动的网站
*   [编程 PERL 101](http://www.webmasterbase.com/article/270)

你也可以看一看客户端语言，它允许你在访问者的浏览器中创建各种巧妙而有用的效果:

*   [JavaScript 101](http://www.webmasterbase.com/article/452)

本部分现在结束了这个系列——我希望它对你有所帮助，并且你已经学会了很多 HTML。正如您可能已经猜到的那样，还有很多东西需要学习，但是本教程应该为您提供进一步学习所需的基本知识和理解。如有任何问题，请访问 [SitePoint 社区论坛](http://www.sitepointforums.com)，或者随时给我发电子邮件，告诉我您的任何意见或建议。

## 分享这篇文章