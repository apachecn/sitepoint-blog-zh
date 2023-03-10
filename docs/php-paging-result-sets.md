# 面向对象的 PHP:分页结果集

> 原文：<https://www.sitepoint.com/php-paging-result-sets/>

如果作为一名 PHP 开发人员，你发现自己一次又一次地编写相同的代码，或者在脚本中处理越来越多令人窒息的自定义函数和包含文件，那么很可能是时候研究一下面向对象编程(OOP)了。在过去的 5 年左右的时间里，OOP 真正获得了它自己的地位。全世界的应用程序和 Web 开发人员都已经发现了这种软件设计的优点，现在轮到您了！

在本文中，我将向您介绍 PHP 语言中存在的面向对象编程的基本原则和特性，并通过几个简单的例子让您了解它是如何工作的。然后，我们将把这些新发现的知识应用到实际问题中。我们将一起创建一个可重用的组件，处理 MySQL 结果集到页面的分割。

扣好安全带；我看到前面的路上有物体——这将是一次颠簸的旅程！

##### 有什么大想法？

尽管面向对象编程本身并不总是简单的，但是它背后的主要思想还是很容易理解的。

面向对象编程的主要目标是将数据(以变量的形式)与负责处理它们的代码绑定在一起。

因此，OOP 不是让一大堆变量和一大堆 PHP 代码混在一起，而是让你将问题分解成更小的(理想情况下，更有组织的)变量和代码包。然后这些束可以一起工作以产生期望的效果。如果您的工作做得非常好，您甚至可以将这些包中的一部分放到下一个项目中，这样就可以省去下次需要时重新开发该功能的麻烦。

所以我们要明确的是，OOP 实际上并不允许你做任何你在代码和变量的大混乱中已经做不到的事情；这实际上是一种更有条理的工作方式。如果你和我一样，你的 PHP 脚本可以使用组织部门提供的所有帮助！

假设您厌倦了每次用 PHP 开发新的数据库驱动的网站时都要编写数据库连接代码。您可以组合所有变量(用户名、密码、主机名、数据库名等。)和所有代码(连接数据库服务器、选择数据库、检查错误等。)放入一个包中，称为一个**对象**。从那时起，您可以使用该对象来处理您的数据库连接。

##### 不要古板

好吧，让我们面对现实:从二年级几何开始，矩形就不再有趣了。但是他们为我们提供了一个方便简单的例子，如果你真的*和*希望从事体力劳动而进入网络开发，你可以假装他们是砖块。

假设某个几何不及格的二年级学生付钱给你，让你写一个在矩形上进行计算的 PHP 脚本。市场不景气，你没有资格挑挑拣拣，所以你接受了这份工作。

您从一个简单的 HTML 表单开始，允许学生输入矩形的宽度和高度:

```
<form action="domyhomework.php" method="get">  
Width: <input type="text" name="w" /><br />  
Height: <input type="text" name="h" /><br />  
<input type="submit" />  
</form>
```

现在，`domyhomework.php`——处理这个表单的脚本——可以简单地获取宽度和高度，并计算矩形的特征:

```
<?php  
$area = $w * $h;  
$perimeter = ($w + $h) * 2;  
?>  
<html>  
<body>  
<p>Width: <?=$w?><br />  
   Height: <?=$h?></p>  
<p>Area: <?=$area?><br />  
   Perimeter: <?=$perimeter?></p>  
</body>  
</head>
```

很好也很简单，但是如果你获得了编写矩形脚本的名声(嘿，这可能发生！)，您可能会厌倦反复编写计算面积和周长的代码。

如果您已经学习了 PHP 函数，您可能会决定编写两个函数来在包含文件中执行这些计算。假设您编写了一个名为`rect.php`的文件，其中包含以下代码:

```
<?php  
function rect_area($width,$height)  
{  
  return $width * $height;  
}  
function rect_perim($width,$height)  
{  
  return ($width + $height) * 2;  
}  
?>
```

然后，您可以在任何需要的文件中使用这些函数。这是我们修改后的`domyhomework.php`:

```
<?php  
require('rect.php'); // Load rectangle functions  
$area = rect_area($w,$h);  
$perimeter = rect_perim($w,$h);  
?>  
<html>  
<body>  
<p>Width: <?=$w?><br />  
   Height: <?=$h?></p>  
<p>Area: <?=$area?><br />  
   Perimeter: <?=$perimeter?></p>  
</body>  
</head>
```

现在，这一切都很好，但是使用面向对象编程，我们可以做得更好！

##### 面向对象的版本

正如我上面解释的那样，OOP 的目标是将数据(在本例中是宽度和高度变量)和处理它们的代码(我们计算面积和周长的两个函数)放在一起。

让我们修改`rect.php`,这样它就不仅仅定义几个函数，而是定义一个类，我们可以从这个类创建(实例化)矩形对象:

```
<?php   
class Rectangle   
{   
  var $width;   
  var $height;   

  function Rectangle($width, $height)   
  {   
    $this->width = $width;   
    $this->height = $height;   
  }   

  function area()   
  {   
    return $this->width * $this->height;   
  }   

  function perimeter()   
  {   
    return ($this->width + $this->height) * 2;   
  }   
}   
?>
```

让我们一次看几行代码，这样我就可以从头到尾解释了:

我们首先声明我们正在编写一个名为`Rectangle`的类。

```
class Rectangle   
{
```

按照惯例，类名应该总是以大写字母开头。这不是 PHP 所要求的，但是通过像这样坚持已建立的标准，你的代码将更容易被其他开发者阅读和维护。

接下来我们声明两个变量:`$width`和`$height`。

```
 var $width;   
  var $height;
```

然而，这些不仅仅是普通的 PHP 变量！因为它们出现在类定义中，所以我们实际上声明了存在于这个类的每个对象中的变量。记住:我们正在绘制构建矩形物体的蓝图。所以基本上我们告诉 PHP 每个矩形都应该有一个宽度和一个高度。在 OOP 的说法中，属于对象的变量被称为**属性**。所以我们刚刚声明了`Rectangle`对象的两个属性。

接下来是一个函数:

```
 function Rectangle($width, $height)   
  {
```

与上面的变量一样，在类定义中声明的函数将属于这个类的每个对象。这个函数很特殊，因为它与类(`Rectangle`)同名。这样的函数被称为**构造函数**，当对象被实例化时被调用，我们很快就会看到。

构造函数负责设置新实例化的对象，以便可以使用。在我们的`Rectangle`类中，我们需要给出我们在初始值之上声明的宽度和高度属性(变量)。正如你在上面看到的，这个构造函数有两个参数:`$width`和`$height`。让我们看看构造函数如何将这些参数值分配给对象中的相应属性:

```
 $this->width = $width;   
    $this->height = $height;   
  }
```

在作为类定义一部分的任何函数中，特殊变量`$this`指的是该函数所属的对象。因此，在上面的代码中，`$this`的两次出现都意味着“这个`Rectangle`对象”。

箭头操作符(`->`)可能一开始看起来很吓人，但它真的相当简单。它用于获取属于一个对象的变量和函数。所以在上面，`$this->width`指的是我们上面声明的`$width`变量——`this`对象的`width`属性。同样，`$this->height`是指属于`this`对象的`$height`变量。

记住这一点，我们的构造函数获取`$width`(传递给函数的第一个参数)并将其赋给`$this->width`(该对象的`width`属性)，获取`$height`(第二个参数)并将其赋给`$this->height`。

稍后我们将看到如何使用这个构造函数创建一个具有给定宽度和高度的新的`Rectangle`对象。现在，让我们来看看(现在已经熟悉的)面积和周长计算函数:

```
 function area()   
  {   
    return $this->width * $this->height;   
  }   

  function perimeter()   
  {   
    return ($this->width + $this->height) * 2;   
  }   
}
```

这些函数非常类似于我们在上一节中声明的`rect_area`和`rect_perim`函数，但是您会注意到这些函数没有任何宽度/高度参数。像我们上面声明的变量(`$width`和`$height`)一样，这些函数将属于从这个类实例化的每个对象。如果我们创建三个不同的`Rectangle`对象，每个对象都有自己的`area`和`perimeter`函数。就像属于对象的变量叫做**属性**一样，属于对象的函数叫做**方法**。

现在，考虑到这一点，应该清楚为什么`area`和`perimeter`方法不需要参数了。这些函数的工作不是计算任何矩形的面积和周长。他们的工作是为*他们的*矩形计算那些值！

所以如果你仔细观察这些函数，你会注意到它们使用`$this->width`和`$this->height`作为它们的宽度和高度值。我们再次使用`$this`来表示“我所属的物体”。

为了查看这个类声明的所有元素如何组合在一起创建一个工作对象，让我们修改`domyhomework.php`来使用我们新的面向对象的方法来处理矩形。

##### 矩形计算器 2.0

在我们开始新的矩形计算脚本之前，我们需要学习如何在类之外创建一个对象。也就是说，我们已经制定了一个矩形的蓝图，但我们还没有真正创建一个。方法如下:

```
new Rectangle(*width*, *height*)
```

关键字`new`告诉 PHP 你想让它创建一个新的对象，这就是这里的神奇之处。通过说 new `Rectangle`，我们告诉 PHP 实例化我们的`Rectangle`类。现在，记住我们声明的构造函数有两个参数——要创建的矩形的宽度和高度——所以我们必须在这里指定这些值( *`width`* 和 *`height`* )。

因此，例如，要创建一个 10 乘 20 的矩形并将其放入名为`$myRectangle`的变量中，我们将使用以下 PHP 代码行:

```
$myRectangle = new Rectangle(10,20);
```

我们可以改变矩形的宽度:

```
$myRectangle->width = 50;
```

然后打印出它的面积:

```
echo $myRectangle->area(); // Prints out '1000'
```

更重要的是，我们可以创建两个矩形，并一起玩它们:

```
$rect1 = new Rectangle(10,20);    
$rect2 = new Rectangle(30,40);    
echo $rect1->area(); // Prints out '200'    
echo $rect2->perimeter(); // Prints out '140'
```

现在我们知道了如何创建和使用一个`Rectangle`对象，我们可以创建一个修改过的`domyhomework.php`脚本:

```
<?php    
require('rect.php'); // Load rectangle functions    
$r = new Rectangle($w,$h);    
?>    
<html>    
<body>    
<p>Width: <?=$r->width?><br />    
   Height: <?=$r->height?></p>    
<p>Area: <?=$r->area()?><br />    
   Perimeter: <?=$r->perimeter()?></p>    
</body>    
</head>
```

看代码多优雅？我们通过指定它的宽度和高度来创建一个矩形，从那时起这个对象为我们处理所有的细节。要确定矩形的面积和周长，我们需要做的就是通过调用相关的方法来获取它们。OOP 的这种特性被称为**封装**，功能隐藏在对象内部，因此使用它的开发人员不需要知道*它是如何工作的。*

如果你有兴趣更详细地阅读 PHP 的面向对象特性，我会让你参考 PHP 手册的第 13 章[。到目前为止，我们所看到的基础知识对于 PHP 中面向对象代码的大多数使用来说已经足够了。](http://www.php.net/oop)

但是矩形已经够多了！让我们现在设计一些有用的东西，好吗？

##### 分页结果集

更令人恼火的是，每次需要时都要从头开始编写代码，这使得您可以在用户可以导航的页面中显示大型数据库结果集。利用我们新发现的面向对象技术，我们可以设计一个可重用的类来为我们工作！

与上面基于矩形的例子不同，设计一个类的最佳方式通常是决定你希望该类的对象如何行为，然后根据这些规范编写代码。在程序员的行话中，我们首先为我们的类定义**接口**。

假设我们有一个笑话数据库(我的书[的读者会熟悉这个例子)，我们有下面的`Jokes`表，其中`AID`列指的是`Authors`表中的条目:](https://www.sitepoint.com/books/?bookid=php-oop)

现在，假设我们想写一个 PHP 脚本，列出给定作者的所有笑话，但一次只显示 5 个，并允许用户浏览这 5 个笑话的页面。

使用传统的脚本，这样的应用程序会在页面中包含一些相对较长的 PHP 代码，但是如果我们使用一个对象来完成所有的工作，它看起来会是这样:

```
<?php     
**require('pagedresults.php');**     

$cnx = @mysql_connect('localhost','kyank','********');     
mysql_select_db('jokes',$cnx);     
**$rs = new MySQLPagedResultSet("select * from jokes where aid=$aid",**     
 **5,$cnx);**     
?>     
<html>     
<head>     
<title>Paged Results Demo</title>     
</head>     
<body>     
<table border="1">     
<?php while (**$row = $rs->fetchArray()**): ?>     
<tr><td><?=$row['JokeText']?></td><td><?=$row['JokeDate']?></td></tr>     
<?php endwhile; ?>     
</table>     
<p><?=**$rs->getPageNav("aid=$aid")**?></p>     
</body>     
</html>
```

粗体部分指出了(类`MySQLPagedResultSet`)对象开始发挥作用的地方。如果你曾经使用过 PHP 很长时间，那么其他的一切看起来都应该很标准。让我们来分解一下:

```
require('pagedresults.php');
```

与矩形示例一样，我们将类定义放在一个单独的文件中。这不仅使 HTML 中的代码更容易混淆，而且使我们在其他页面和站点上重用该类变得容易，只需将该文件包含在任何需要它的页面中。

在我们连接到 MySQL 服务器并选择我们的数据库后，我们创建对象:

```
$rs = new MySQLPagedResultSet("select * from jokes where aid=$aid",     
                              5,$cnx);
```

正如您所看到的，这个类的构造函数有三个参数:

*   SQL 查询本身
*   我们希望每页显示的记录数(在本例中为 5)，以及
*   对数据库连接的引用(`$cnx`)

我们*可能*已经设计了对象，所以我们只传递给它一个普通的 MySQL 结果集，但是我决定将查询处理合并到对象中，这样实例化对象就可以替代通常对`mysql_query`的调用。

一旦从数据库中获得了一个结果集，通常的过程是使用一个`while`循环遍历结果集并打印出它的内容。我们的分页结果集对象(我们刚刚将其实例化为`$rs`)允许我们做同样的事情，使用它的`fetchArray`方法，就像我们通常使用`mysql_fetch_array`函数一样:

```
<?php while ($row = $rs->fetchArray()): ?>
```

所以每次调用`$rs->fetchArray()`都会返回一个数组，代表结果集的一行(放在`$row`变量中)，直到到达当前结果页的末尾。在页面的最后，`$rs->fetchArray()`将返回`false`，从而结束`while`循环。

剩下的就是提供页面之间的导航:

```
<p><?=$rs->getPageNav("aid=$aid")?></p>
```

`getPageNav`方法负责生成导航链接，对于一个 8 页结果集的第 4 页，它看起来像这样:

![Prev 1 2 3 4 5 6 7 8 Next](img/e1022ed1ebe0bca32eecd36c2fac5544.png)该方法创建返回到当前脚本的链接，该脚本在查询字符串中包含一个名为`resultpage`的特殊变量。`MySQLPagedResultSet`的构造函数监视该变量，并使用它来确定显示结果集的哪一页。

因为在大多数情况下，SQL 查询将涉及一个或多个变量(在本例中，`$aid`变量用于选择显示哪个作者的笑话)，所以可以向`getPageNav`传递任何附加的查询字符串元素。在这种情况下，我们传递给它`"aid=$aid"`，这将确保`$aid`变量通过任何生成的链接传递。

既然我们已经看到了对象应该如何工作，我们可以深入研究在`MySQLPagedResultSet`类中实现该接口。

##### 实现类

我对由`MySQLPagedResultSet`类的对象呈现的接口的描述无疑给了您大量关于类本身如何工作的线索。在这最后一部分，我们将深入研究这个类的代码，看看是什么让它起作用。然而，如果你唯一的兴趣是在你的项目中使用这个类，你可以在这里下载`pagedresults.php`。

```
class MySQLPagedResultSet      
{      
  var $results;      
  var $pageSize;      
  var $page;      
  var $row;
```

我们从成员变量列表(也称为属性)开始:

*   `$results`将用于存储实际的 MySQL 结果集。
*   `$pageSize`将存储每页要显示的记录数。
*   `$page`将跟踪显示结果集的哪一页。
*   `fetchArray`方法使用`$row`来跟踪结果集中的当前行。

接下来，构造函数:

```
 function MySQLPagedResultSet($query,$pageSize,$cnx)      
  {      
    global $resultpage;
```

正如我们已经决定的，构造函数接受三个参数 SQL 查询、页面大小(每页记录数)和数据库连接。

我们在这个函数中做的第一件事是获得对全局变量`$resultpage`的访问。这是一个变量，`getPageNav`方法将把它插入到导航链接中，以指示要显示哪个页面，所以我们需要在这里访问它，以确定是否已经指定了一个页面。

<q>**PHP 4.1 注:**从 PHP 4.1 开始，建议禁用`php.ini`中的`register_globals`选项，改为通过一个新的全局数组来访问变量:`$_GET`、`$_POST`、`$_COOKIE`、`$_SERVER`、`$_ENV`或`$_REQUEST`。如果您使用的是 PHP 4.1 或更高版本，并且选择遵从这个建议，那么您可以用`$resultpage = $_GET['resultpage'];`替换第一行。</q>

接下来，构造函数执行给定的查询:

```
 $this->results = @mysql_query($query,$cnx);
```

结果放在`$this->results`中——我们在上面声明的成员变量。在这个过程中，我们初始化了`$this->pageSize`，它将被赋予传递给构造函数的`$pageSize`参数的值:

```
 $this->pageSize = $pageSize;
```

最后，我们需要设置当前页面。我们必须首先查询`$resultpage`变量，看看是否设置了特定的页面。如果发现变量为空(或者错误地设置为负数)，我们将它设置为`1`,以便默认选择第一页:

```
 if ((int)$resultpage <= 0) $resultpage = 1;
```

`(int)`强制 PHP 将`$resultpage`中的值转换成一个整数，以防有人修改了查询字符串并允许一个非数字值进入。

我们还需要确保指定的页面没有超过结果集的结尾。为此，我们使用`getNumPages`方法，稍后我们会看到:

```
 if ($resultpage > $this->getNumPages())      
      $resultpage = $this->getNumPages();
```

最后，有了有效的页码，我们将值传递给`setPageNum`方法，我们很快就会看到:

```
 $this->setPageNum($resultpage);      
  }
```

`getNumPages`方法确定当前结果集中的页数。该方法由该类的其他方法在内部使用，尤其是我们刚刚看到的构造函数。

```
 function getNumPages()      
  {      
    if (!$this->results) return FALSE;      

    return ceil(mysql_num_rows($this->results) /      
                (float)$this->pageSize);      
  }
```

如您所见，找到页数是一个相对简单的计算。我们只是将结果集中的行数(`mysql_num_rows($this->result)`)除以页面大小(`$this->pageSize`)，然后使用 PHP 的`ceil`函数取整。为了确保除法运算产生一个小数值，使得*能够被*取整，我们将`pageSize`属性转换为一个浮点数。

`setPageNum`方法可以用来设置正在查看的结果集的页面。这种方法很有用的情况很少见(试着想象一种情况，你想跳转到结果集的特定页面，但是你不能传递或者想要覆盖`$resultpage`变量)，但是构造函数也用它来设置基于`$resultpage`变量的页面。所以实际上你可以把它看作是构造函数的扩展。

```
 function setPageNum($pageNum)      
  {      
    if ($pageNum > $this->getNumPages() or      
        $pageNum <= 0) return FALSE;      

    $this->page = $pageNum;      
    $this->row = 0;      
    mysql_data_seek($this->results,($pageNum-1) * $this->pageSize);      
  }
```

首先，该方法检查`$pageNum`参数，以确保它是一个在有效页面范围内的数字。如果不是，则返回 false。接下来，它将新的页码存储在`$this->page`中，将`$this->row`重置为零，最后使用 PHP 的`mysql_data_seek`函数移动到 MySQL 结果集中所选页面的第一行，这是根据`$pageNum`和`$this->pageSize`计算的。

接下来，`getPageNum`，返回当前页码:

```
 function getPageNum()      
  {      
    return $this->page;      
  }
```

你会问，为什么是一种`getPageNum`方法？毕竟，如果`$rs`是一个`MySQLResultSet`，你可以从`$rs->page`获得页码，对吗？嗯，看看`setPage`的方法。正如您所看到的，为结果集设置新的页码涉及到很多内容。如果该类的用户决定通过直接修改`$rs->page`来设置页面，这些事情都不会发生，对象也不会像预期的那样运行。

当前对面向对象设计的思考表明，不应该从类外部直接访问任何属性。相反，应该提供方法来“获取”和“设置”该类的用户可能需要访问的每个属性的值。在大多数面向对象的语言中，这可以通过将属性设为“私有”来实现，这样外界就完全无法访问这些属性。

PHP 没有这样的设施来确保数据隐私，但是这个原则仍然适用。我们没有要求类的用户记住，虽然他或她可以直接读取页码，但必须总是使用`setPageNum`来设置页码，而是提供了一个匹配的`getPageNum`方法来检索值，并尽我们所能阻止直接访问属性。

`getPageNum`可能有用的一个实际例子是，如果你想在结果列表的顶部显示“第 X 页，共 Y 页”。这样做的代码应该是:

```
<p>Page <?=$rs->getPageNum()?> of <?=$rs->getNumPages()?>.</p>
```

哇——这么简单的方法居然有这么多讨论！幸运的是，下面两个几乎不需要解释:

```
 function isLastPage()      
  {      
    return ($this->page >= $this->getNumPages());      
  }      

  function isFirstPage()      
  {      
    return ($this->page <= 1);      
  }
```

这两个函数允许该类的用户确定当前页面是否是结果集的第一页和/或最后一页。这两个函数都返回 true 或 false。代码应该是不言自明的。

剩下的就是该类的两个真正的“主力”方法。首先，我们有`fetchArray`，它代替了分页结果集的`mysql_fetch_array`:

```
 function fetchArray()      
  {      
    if (!$this->results) return FALSE;      
    if ($this->row >= $this->pageSize) return FALSE;      
    $this->row++;      
    return mysql_fetch_array($this->results);      
  }
```

如果存储在`$this->results`中的结果集为 false(这将表明查询失败)，或者如果当前行号大于或等于页面大小(这将表明已经到达页面的结尾)，则该方法返回 false。否则，当前行号递增，并调用`mysql_fetch_array`返回结果集中的下一行。

最后是‘大卡胡纳’，`getPageNav`:

```
 function getPageNav($queryvars = '')      
  {
```

如您所见，`$queryvars`参数被赋予了默认值`''`(空字符串)，使其成为可选的，因此如果您不需要在生成的链接的查询字符串中传递任何变量，您可以不带参数调用`getPageNav`。

```
 $nav = '';      
    if (!$this->isFirstPage())      
    {      
      $nav .= "<a href="?resultpage=".      
              ($this->getPageNum()-1).'&'.$queryvars.'">Prev</a> ';      
    }
```

首先，如果当前页面不是第一页(我们使用`isFirstPage`方法检查),我们希望显示“上一页”链接。链接的 URL 是一个纯查询字符串，因为我们只是直接链接回同一个页面。查询字符串包含神奇的`resultpage`变量，它将告诉`MySQLPagedResultSet`对象显示哪个页面。我们还将任何额外的查询变量(如参数`$queryvars`所示)添加到查询字符串的末尾。

这个函数的输出建立在一个名为`$nav`的变量中，我们将在最后返回这个变量，而不是直接回显到输出中。这使得该方法更加灵活，允许类的用户决定如何处理输出。

下一步是构建页面列表:

```
 if ($this->getNumPages() > 1)      
      for ($i=1; $i<=$this->getNumPages(); $i++)      
      {      
        if ($i==$this->page)      
          $nav .= "$i ";      
        else      
          $nav .= "<a href="?resultpage={$i}&".      
                  $queryvars."">{$i}</a> ";      
      }
```

`if`语句确保我们只在不止一个列表的情况下才创建列表。仅仅在结果列表的底部显示数字“1”是没有意义的，不是吗？

然后，我们使用一个非常典型的`for`循环来遍历页码，为每一页输出一个链接的页码，除了当前页(where `$i=$this->page`)，它没有链接。

最后，如果当前页面不是最后一页，我们打印出“下一页”链接(根据`isLastPage`):

```
 if (!$this->isLastPage())      
    {      
      $nav .= "<a href="?resultpage=".      
              ($this->getPageNum()+1).'&'.$queryvars.'">Next</a> ';      
    }      

    return $nav;      
  }      
}
```

写一个类肯定比写同样的工作的裸代码更耗时，但是如果这是一项你在 PHP 开发人员的职业生涯中希望不止做一次的工作，那么像这样一个设计良好的类可以为将来的项目节省大量时间！

##### 关于性能的说明

一次显示一页大型数据库结果集的 PHP 脚本通常利用 MySQL 中的`SELECT`查询的`LIMIT`子句。

使用`LIMIT`，您可以指定您想要获得的结果的数量，以及您想要检索的第一个结果的数量。例如，您可以告诉它您想从第 26 行开始选择最多 5 行。这相当于只检索每页显示 5 条记录的分页结果集的第 6 页上的行。

如果您正在处理的完整结果集与您的页面大小相比可能非常大(例如，如果它包含数百条记录，并且您一次显示五条记录)，让 PHP 从数据库服务器检索完整的结果集(正如本文中的`MySQLPagedResultSet`类所做的那样)在性能方面可能有点浪费。

如果您有兴趣接受挑战，您可以修改本文中的类，以便在提交查询之前，将一个适当的`LIMIT`子句附加到`SELECT`查询的末尾，但是您还需要让它分析查询并生成一个`SELECT COUNT(*)`查询，以确定完整结果集中的记录数。

我认为，除了在极端情况下，拥有一个简单的、可重用的组件来创建分页结果集的便利性(除了不必进行单独的查询来计算结果的开销之外)超过了检索完整结果集所损失的性能。

##### 摘要

在本文中，我不仅向您介绍了面向对象编程的基础知识，并演示了如何在 PHP 中应用它们，而且还向您介绍了一个非常有用的类的开发过程，该类用于将 MySQL 结果集拆分成页面。

也许这个类缺少常规结果集的一些功能，或者您希望显示图形的下一个/上一个链接，而不是我们在这里创建的文本链接？你所需要做的就是给这个类添加更多的方法，让它做你需要的事情！最棒的是，您只需编写一次，就可以在任何未来的项目中重用该功能！

## 分享这篇文章