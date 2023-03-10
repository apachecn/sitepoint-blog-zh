# PHP 选集第 1 卷，第 2 章-面向对象的 PHP

> 原文：<https://www.sitepoint.com/object-oriented-php/>

面向对象的范例是一种编程方法，旨在鼓励开发可维护的、结构良好的应用程序。许多 PHP 程序员将面向对象编程(OOP)视为某种神秘的艺术，因为 PHP 的例子经常只关注解决问题的过程方法。(注意，过程式编程是非面向对象编程的名称。到目前为止，我们在本书中看到的所有代码本质上都是过程化的。)

这是一个遗憾，因为采用面向对象的方法来开发 PHP 应用程序有很多好处，也许最重要的是代码重用。一段写得很好的面向对象的代码可以很容易地用于解决其他项目中的相同问题；我们可以在需要的时候插入它。有越来越多的面向对象的代码库，如 [PEAR](http://pear.php.net/) 和 [PHP 类](http://www.phpclasses.org/)，它们可以节省你解决清晰问题的时间，让你可以专注于应用程序的细节。

在这一章中，你将获得编写面向对象 PHP 的实践基础——并且有很多机会让你动手。教授 OOP 的方法有很多，主题提供了无尽的讨论空间。在我看来，最好的方法是先深入研究，看看如何用 PHP 中的类来完成过程性任务，并在我们进行的过程中添加理论。这是我们将在本章中采用的方法。在 PHP 选集的两个卷中，我将在适当的地方使用 OOP，这将给你更多的例子来学习。特别是，第 7 章，设计模式应该提供一些关于为什么 OOP 是构建你的应用程序的有效方法的见解。

在实践中，学习使用 PHP 提供的对象模型需要我们实现两个目标，这两个目标通常必须同时进行:

*   你需要学习 PHP 类语法和面向对象的术语。
*   您必须实现从过程代码到面向对象代码的“精神飞跃”。

第一步很容易。这是下一个解决方案的主题，更多的例子出现在后面的解决方案中，这些解决方案着眼于更高级的主题。

第二步，即“精神飞跃”，既容易又具有挑战性。一旦你做到了，你将不再考虑一个脚本应该完成的长长的任务列表；相反，您将把编程看作是一组工具的组合，您的脚本将把工作委托给这些工具。

向前跳一点——让您体验一下即将到来的事情——这里有一个简单的例子，使用 PHP 超过一周的人应该都很熟悉:连接到 MySQL 并获取一些数据。常见的程序方法如下所示:

```
<?php 

// Procedural Example 

// Connect to MySQL 

$connection = mysql_connect('localhost', 'harryf', 'secret'); 

// Select desired database 

mysql_select_db('sitepoint', $connection); 

// Perform a query selecting five articles 

$sql = 'SELECT * FROM articles LIMIT 0,5'; 

$result = mysql_query($sql, $connection); 

// Display the results 

while ($row = mysql_fetch_array($result)) { 

  // Display results here 

} 

?>
```

在上面的脚本中，我们直接调用了 PHP 的 MySQL 函数，这些函数作用于我们传递给它们的变量。这通常会导致我们得到一个新的变量，我们可以用它来执行进一步的工作。

解决相同问题的面向对象方法可能如下所示:

```
<?php 

// OOP Example 

// Include MySQL class 

require_once 'Database/MySQL.php'; 

// Instantiate MySQL class, connect to MySQL and select database 

$db = new MySQL('localhost', 'harryf', 'secret', 'sitepoint'); 

// Perform a query selecting five articles 

$sql = 'SELECT * FROM articles LIMIT 0,5'; 

$result = $db->query($sql); // Creates a MySQLResult object 

// Display the results 

while ($row = $result->fetch()) { 

  // Display results here 

} 

?>
```

使用 PHP 的 MySQL 函数处理 MySQL 的细节现在已经委托给一个从 MySQL 类创建的对象(我们将在本书中经常使用它，它在第 3 章 PHP 和 MySQL 中构造)。尽管这个例子可能没有完全说明 OOP 的优势，但是考虑到代码量，它与第一个例子非常相似，它确实显示了一些原始脚本的复杂性现在由 MySQL 类来处理。

比如我们现在不再需要执行两步连接 MySQL 服务器，然后选择一个数据库；相反，我们可以在创建 MySQL 对象时一步完成这两个步骤。此外，如果我们以后希望让脚本从不同的数据库(如 PostgreSQL)获取结果，我们可以使用提供与 MySQL 类相同的应用程序编程接口(API)的相关类——为此，我们只需更改上面示例中的一行代码。我们将在第 7 章“设计模式”中做同样的事情。

面向对象的方法在对象相互交互的情况下真正显示了它的价值。我将把进一步的讨论留到本章的解决方案中，但这是一个重要的概念。随着您对面向对象编程的熟练掌握，您会发现编写复杂的应用程序变得像组装乐高积木一样简单。

在这次讨论中，我将偶尔介绍一下统一建模语言(UML)类图。UML 是用图像描述面向对象程序的标准。如果你以前没有遇到过 UML，也不要担心；图表和代码之间的关系不言自明。

##### 面向对象 PHP 的基础是什么？

假设您没有 OOP 知识，那么最好的起点是从类的基本 PHP 语法开始。你可以简单地把一个类看作是函数和变量的集合。

*阅读精细手册*

PHP 手册包含了大量关于 OOP 的信息:[http://www.php.net/oop](http://www.php.net/oop)

在这里，我们将开发一个简单的例子来帮助我们生成 HTML，它将演示 PHP 中的类和对象的基础知识。这不是一个伟大设计的例子；它只是 PHP 语法的初级读本。让我们从构建网页的过程脚本开始。然后我们会逐渐把它变成一个 PHP 类:

例 2.1。1.php

```
<?php  

// Generates the top of the page  

function addHeader($page, $title)  

{  

  $page .= <<<EOD  

<html>  

<head>  

<title>$title</title>  

</head>  

<body>  

<h1 align="center">$title</h1>  

EOD;  

  return $page;  

}  

// Generates the bottom of the page  

function addFooter($page, $year, $copyright)  

{  

  $page .= <<<EOD  

<div align="center">&copy; $year $copyright</div>  

</body>  

</html>  

EOD;  

  return $page;  

}  

// Initialize the page variable  

$page = '';  

// Add the header to the page  

$page = addHeader($page, 'A Prodecural Script');  

// Add something to the body of the page  

$page .= <<<EOD  

<p align="center">This page was generated with a procedural  

script</p>  

EOD;  

// Add the footer to the page  

$page = addFooter($page, date('Y'), 'Procedural Designs Inc.');  

// Display the page  

echo $page;  

?>
```

在这个例子中值得注意的是我们第一次看到的 heredoc 语法，这是编写 PHP 字符串的另一种方法。不是用引号将文本括起来，而是以`<<<EOD`和新的一行开始，以新的一行和 EOD 结束。如果你好奇的话，PHP 手册可以提供更多的细节。

这个过程性示例使用了两个函数 addHeader 和 addFooter，以及一个全局变量$page。也许这与您自己编写的过程脚本相差不远；也许你已经在每一页中包含了一个包含像`addHeader`和`addFooter`这样的函数的文件。

但是我们如何重构上面的代码以呈现面向对象的形式呢？(重构是在不实际改变代码功能的情况下对代码进行重组的过程。这样做通常是为了方便将来的维护和代码扩展，因为当前的结构会阻碍代码的维护和扩展。)

首先，我们需要一个类来放置两个函数，`addHeader`和`addFooter`:

例 2.2。2.php(节选)

```
<?php  

// Page class  

**class Page {**  

  // Generates the top of the page  

  function addHeader($page, $title)  

  {  

    $page .= <<<EOD  

<html>  

<head>  

<title>$title</title>  

</head>  

<body>  

<h1 align="center">$title</h1>  

EOD;  

    return $page;  

  }  

  // Generates the bottom of the page  

  function addFooter($page, $year, $copyright)  

  {  

    $page .= <<<EOD  

<div align="center">&copy; $year $copyright</div>  

</body>  

</html>  

EOD;  

    return $page;  

  }  

**}**
```

使用 PHP 关键字类，我们可以在类中对两个函数`addHeader`和`addFooter`进行分组。放在类中的函数被称为成员函数，或者更常见的方法。与普通函数不同，方法必须作为类的一部分被调用:

例 2.3。2.php(节选)

```
// Initialize the page variable  

$page = '';  

// Add the header to the page  

$page = Page::addHeader($page, 'A Script Using Static Methods');  

// Add something to the body of the page  

$page .= <<<EOD  

<p align="center">This page was generated with static class  

methods</p>  

EOD;  

// Add the footer to the page  

$page = Page::addFooter($page, date('Y'), 'Static Designs Inc.');  

// Display the page  

echo $page;  

?>
```

这里，我们使用`::`操作符调用了类方法`addHeader`和`addFooter`。剧本实际上和以前一样；然而，我们需要像下面这样调用函数，而不是直接调用我们的函数:

```
$page = Page::addHeader($page, 'A Script Using Static Methods');
```

虽然这不是一个很大的改进，但它确实让我们可以根据工作描述来收集我们的功能。这允许我们用相同的名字调用不同的函数，每个函数分别嵌套在不同的类中。

*静态方法*

到目前为止，我们只使用了一个类作为相关函数的容器。在面向对象的说法中，以这种方式工作的函数被称为静态方法。

实际上，与大多数方法相比，静态方法听起来很无聊。在下面几节中，我们将看到如何用一些成熟的方法来真正展示面向对象的能力。

***类和对象***

类是对象的“蓝图”。也就是说，与您声明和使用的函数不同，类仅仅描述一种类型的对象。在使用它做有用的工作之前，您需要创建一个对象——类的一个实例——使用一个称为实例化的过程。一旦有了对象，就可以调用类中定义的方法。

类不仅包含函数，还可以包含变量。为了让我们在上面开发的`Page`类更有用，我们可能想用方法将一些变量分组，然后将类实例化为一个对象。下面是修改后的`Page`类的代码；下面我来解释一下:

例 2.4。3.php(节选)

```
<?php   

// Page class   

class Page {   

  // Declare a class member variable   

  var $page;   

  // The constructor function   

  function Page()   

  {   

    $this->page = '';   

  }   

  // Generates the top of the page   

  function addHeader($title)   

  {   

    $this->page .= <<<EOD   

<html>   

<head>   

<title>$title</title>   

</head>   

<body>   

<h1 align="center">$title</h1>   

EOD;   

  }   

  // Adds some more text to the page   

  function addContent($content)   

  {   

    $this->page .= $content;   

  }   

  // Generates the bottom of the page   

  function addFooter($year, $copyright)   

  {   

    $this->page .= <<<EOD   

<div align="center">&copy; $year $copyright</div>   

</body>   

</html>   

EOD;   

  }   

  // Gets the contents of the page   

  function get()   

  {   

    return $this->page;   

  }   

}
```

在这个版本的例子中,`Page`类变得更加有用。首先，我们添加了一个成员变量(也称为字段)，用于存储 HTML:

```
 // Declare a class member variable   

  var $page;
```

PHP 关键字`var`用于在类中声明变量。我们也可以在声明变量时给变量赋值，但是我们不能在声明中放置函数调用。例如:

```
 // This is allowed   

  var $page = '';   

  // This is NOT allowed   

  var $page = strtolower('HELLO WORLD');
```

在变量声明之后，我们有一个特殊的方法叫做构造函数。该方法在类被实例化时自动执行。构造函数必须始终与类同名。

*构造函数没有返回值*

构造函数不能返回任何值。它纯粹用于在类被实例化时以某种方式设置对象。如果有帮助的话，可以把构造函数想象成一个一旦建立就自动返回对象的函数，这样你就不需要自己提供返回值了。

也就是说，如果需要，您仍然可以使用不带指定值的 return 命令来立即终止构造函数。

在构造函数中，我们使用了一个特殊的变量，`$this`:

```
 // The constructor function   

  function Page()   

  {   

    $this->page = '';   

  }
```

`$this`在任何方法(包括构造函数)内指向运行该方法的对象。它允许该方法访问属于该特定对象的其他方法和变量。跟在`$this`后面的`->`(箭头)操作符用于指向对象内命名的属性或方法。

在上面的例子中，构造函数将一个空字符串值赋给我们在开始时声明的`$page`成员变量。开始时，`$this`变量的概念可能看起来有些尴尬和混乱，但它是其他编程语言使用的一种常见策略，比如 [Java](http://java.sun.com/) ，允许类成员相互交互。一旦你开始编写面向对象的 PHP 代码，你会很快习惯它，因为你的类包含的几乎每个方法都需要它。

其他类方法中，`addHeader`和`addFooter`几乎和以前一样；但是，请注意，它们不再返回值。相反，它们更新对象的`$page`成员变量，正如您将看到的，这有助于简化使用该类的代码。我们在这里也使用了`addContent`方法；这样，我们可以向页面添加更多的内容(例如，我们自己在对象外格式化的 HTML)。最后，我们有`get`方法，这是唯一返回值的方法。一旦我们完成了页面的构建，我们将使用它来创建 HTML。

所有这些方法都访问`$page`成员变量，这不是巧合。将 PHP 代码(方法)与它所处理的数据(变量)联系起来的能力是面向对象编程最基本的特性。

下面是运行中的类:

例 2.5。3.php(节选)

```
// Instantiate the Page class   

$webPage = new Page();   

// Add the header to the page   

$webPage->addHeader('A Page Built with an Object');   

// Add something to the body of the page   

$webPage->addContent("<p> align="center">This page was " .   

  "generated using an object</p>n");   

// Add the footer to the page   

$webPage->addFooter(date('Y'), 'Object Designs Inc.');   

// Display the page   

echo $webPage->get();   

?>
```

为了使用这个类，我们用 new 关键字实例化了它。从该类创建的对象放在`$webPage`变量中。通过这个变量，我们可以访问对象的所有成员，就像我们使用上面的`$this`变量一样。

对`addHeader`方法的第一次调用演示了这一点:

```
$webPage->addHeader('A Page Built with an Object');
```

只有在最后，在调用`get`方法时，我们才真正从类中获得任何东西。我们不再需要担心传递包含页面内容的变量——类会处理好这一点。

*避免在类中输出*

代替`get`，我们可以赋予`Page`类一个叫做`write`的方法来立即将页面代码发送给浏览器。这将使上面的代码稍微简单一些，因为主脚本不必从对象中获取代码并回显它本身。我们避免这是有原因的。

直接从类内部输出(用 echo 和`printf`之类的语句和函数)通常不是个好主意；这样做会降低你的类的灵活性。允许从类中检索该值使您可以选择在将它发送到浏览器之前对它执行额外的转换，或者将它完全用于其他目的(比如放在电子邮件中！).

还要注意，为了使用这个类，我们必须编写的代码行数比前面的例子中需要的少。虽然不可能通过计算代码行数来确定好的应用程序设计，但很明显，这个类已经使使用它的过程代码变得更加简单。从阅读代码的人的角度来看，发生了什么已经相当清楚了，甚至不用他们去看`Page`类的代码。

***理解范围***

编写几百行以上的过程化 PHP 代码，毫无疑问，您会遇到一个解析器错误，或者更糟糕的是，由于您不小心不止一次地使用了一个函数或变量名而导致的一个神秘错误。当您包含大量文件并且代码变得越来越复杂时，您可能会发现自己对这个问题变得更加偏执。如何阻止这种命名冲突的发生？有一种方法可以帮助解决这个问题，那就是利用作用域对不需要的代码隐藏变量和函数。

作用域是一个上下文，在这个上下文中，您定义的变量或函数与其他作用域相隔离。PHP 有三个可用的作用域:全局作用域、函数作用域和类作用域。在这些作用域中定义的函数和变量对任何其他作用域都是隐藏的。函数和类的作用域是局部作用域，这意味着函数 X 的作用域对函数 Y 的作用域是隐藏的，反之亦然。

类的最大优点是，它们允许你在一个地方定义变量和使用它们的函数，同时对不相关的代码隐藏函数。这突出了关于面向对象范例的一个关键理论点。过程范式最重视函数，变量被视为函数调用之间存储数据的地方。面向对象的范例将重点转移到变量上；这些函数“支持”变量，用于访问或修改变量。

让我们通过一个例子来探究这一点:

```
<?php    

// A global variable    

**$myVariable** = 'Going global';    

// A function declared in the global scope    

function **myFunction()**    

{    

  // A variable in function scope    

  **$myVariable** = 'Very functional';    

}    

// A class declared in the global scope    

class MyClass {    

  // A variable declared in the class scope    

  var **$myVariable** = 'A class act';    

  // A function declared in the class scope    

  function **myFunction()**    

  {    

    // A variable in the function (method) scope    

    **$myVariable** = 'Methodical';    

  }    

}    

?>
```

在上面的例子中，每个`$myVariable`声明实际上是一个单独的变量。它们可以快乐地生活在一起，互不干扰，因为它们各自居住在一个独立的范围内。类似地，两个`myFunction`声明是两个独立的函数，存在于独立的作用域中。因此，PHP 将为您保留它们的所有值。

当您开始在 PHP 应用程序中大量使用面向对象编程时，范围就变得很重要。因为许多类可以有相同名称的方法，所以您可以设计单独的类来提供相同的应用程序编程接口(API)。使用这些类的脚本可以使用相同的方法调用，而不管哪个类被用来实例化它们正在处理的对象。在编写可维护的代码时，这可能是一种非常强大的技术。当我们在本章后面讨论多态性时，我们会更多地关注这一点。

***一条三条线***

下面是我们如何使这个类更容易使用:

例 2.6。4.php(节选)

```
<?php    

// Page class    

class Page {    

  // Declare a class member variable    

  var $page;    

  var $title;    

  var $year;    

  var $copyright;    

  // The constructor function    

  function Page($title, $year, $copyright)    

  {    

    // Assign values to member variables    

    $this->page = '';    

    $this->title = $title;    

    $this->year = $year;    

    $this->copyright = $copyright;    

    // Call the addHeader() method    

    $this->addHeader();    

  }    

  // Generates the top of the page    

  function addHeader()    

  {    

    $this->page .= <<<EOD    

<html>    

<head>    

<title>$this->title</title>    

</head>    

<body>    

<h1 align="center">$this->title</h1>    

EOD;    

  }    

  // Adds some more text to the page    

  function addContent($content)    

  {    

    $this->page .= $content;    

  }    

  // Generates the bottom of the page    

  function addFooter()    

  {    

    $this->page .= <<<EOD    

<div align="center">&copy; $this->year $this->copyright</div>    

</body>    

</html>    

EOD;    

  }    

  // Gets the contents of the page    

  function get()    

  {    

    // Keep a copy of $page with no footer    

    $temp = $this->page;    

    // Call the addFooter() method    

    $this->addFooter();    

    // Restore $page for the next call to get    

    $page = $this->page;    

    $this->page = $temp;    

    return $page;    

  }    

}
```

这一次，我们修改了构造函数，以接受页面页眉和页脚所需的所有变量。一旦值被分配给对象的成员变量，构造函数调用`addHeader`方法，该方法自动构建页面的标题:

```
 // The constructor function    

  function Page($title, $year, $copyright)    

  {    

    // Assign values to member variables    

    $this->page = '';    

    $this->title = $title;    

    $this->year = $year;    

    $this->copyright = $copyright;    

    // Call the addHeader() method    

    $this->addHeader();    

  }
```

如您所见，像成员变量一样，方法可以用`$this`变量调用。

`addHeader`方法本身现在从成员变量中获取它需要的数据。例如:

```
<title>**$this**->**title**</title>
```

我们还更新了`get`方法，以便它在返回`$page`成员变量的内容之前调用`addFooter`方法。这意味着当我们获取完成的页面时，页脚是自动添加的。

```
 // Gets the contents of the page    

  function get()    

  {    

    // Keep a copy of $page with no footer    

    $temp = $this->page;    

    // Call the addFooter() method    

    $this->addFooter();    

    // Restore $page for the next call to get    

    $page = $this->page;    

    $this->page = $temp;    

    return $page;    

  }
```

我们做了一些工作来确保我们可以不止一次地调用`get`,而不用给页面添加额外的页脚，但是这种复杂性被巧妙地隐藏在类中。

现在，在外部使用该类更加容易了:

例 2.7。4.php(节选)

```
// Instantiate the page class     

$webPage = new Page('As Easy as it Gets', date('Y'),     

  'Easy Systems Inc.');     

// Add something to the body of the page     

$webPage->addContent(     

  "<p align="center">It's so easy to use!</p>n");     

// Display the page     

echo $webPage->get();     

?>
```

本质上，现在只使用三行代码就可以构建页面；我还可以重用这个类来生成其他页面。Page 类用 UML 图表示，如图 2.1 所示。

![1264_pageclass](img/4b31f6808bb752123be57c505e4631d0.png)
图 2.1。UML 形式的页面类

成员变量出现在中间区域，而方法出现在底部的框中。此外，加号和减号向其他开发人员表明该类的哪些元素是公共的(`+`)哪些是私有的(`-`)。与 Java 等语言不同，PHP 不强制对象的隐私(在 PHP 5.0 中将添加对类成员的强制隐私约束)。);在上面的例子中，我们可以直接在主脚本中访问`$page`成员变量。因为我们希望对象在没有外界干扰的情况下处理它自己的数据，所以我们在 UML 图中指出，只有那些拥有针对它们的`+`的成员才可供公共使用。带`-`的纯粹是班内内部使用。

这涵盖了 PHP 中类语法的基础知识，应该会让您对类与过程代码的比较有所了解。有了你所学的语法，你应该能够编写包含你经常使用的变量和函数的独立类——这是一项真正有助于整理你的代码并使其更易于维护的任务。这是一个很好的开始，但是面向对象编程的真正力量来自于同时使用多个对象和类。这一章的其余部分将着眼于 PHP 对象模型的一些更高级的方面，包括引用、继承、聚合和组合。

##### PHP 中的引用是如何工作的？

PHP 中大多数关于引用的讨论都以“引用令人困惑”这样的开场白开始，这可能会增加围绕它们的神话。事实上，引用是一个很容易掌握的概念，但是自学成才的 PHP 开发人员只有在开始编写面向对象的应用程序时才真正需要考虑这个概念。在此之前，您可能不知道 PHP 在幕后处理变量的方式。围绕引用存在的许多困惑更多地与那些对 C++或 Java 等其他语言有经验的开发人员试图使用 PHP 有关:特别是 Java，它处理对象引用的方式几乎与 PHP 在版本 4 中采用的方式相反。

*引用与指针*

熟悉 C++或 Java 等编译语言的开发人员应该注意，PHP 中的引用不同于其他语言中的指针。

指针在内存中包含一个指向变量的地址，为了检索变量的内容，它必须被解引用。

在 PHP 中，所有的变量名都自动与内存中的值相联系。使用引用允许我们将两个变量名链接到内存中的同一个值，就好像变量名是相同的一样。然后你可以用一个代替另一个。

***什么是参考文献？***

为了理解引用，我们必须从理解 PHP 在正常情况下(即没有引用的情况下)如何处理变量开始。

默认情况下，当一个变量被传递给其他变量时，PHP 会创建该变量的一个副本。当我说“通过”时，我指的是以下任何一种情况:

**1。**将一个变量传递给另一个变量:

```
<?php     

  $color = 'blue';     

  $settings['color'] = $color;     

?>
```

`$settings['color']`现在包含了`$color`的副本。

**2。**将变量作为参数传递给函数:

```
<?php     

function isPrimaryColor($color)     

{     

  // $color is a copy     

  switch ($color) {     

    case 'red':     

    case 'blue':     

    case 'green':     

      return true;     

      break;     

    default:     

      return false;     

  }     

}     

$color = 'blue';     

if (isPrimaryColor($color)) {     

  echo $color . ' is a primary color';     

} else {     

  echo $color . ' is not a primary color';     

}     

?>
```

当`$color`被传递给函数`isPrimaryColor`时，PHP 在函数内部使用原始`$color`变量的副本。

**3。**向类方法传递变量时也是如此:

```
<?php     

class ColorFilter {     

  var $color;     

  function ColorFilter($color)     

  {     

    // $color is a copy     

    $this->color = $color;     

    // $this->color is a copy of a copy     

  }     

  function isPrimaryColor()     

  {     

    switch ($this->color) {     

      case 'red':     

      case 'blue':     

      case 'green':     

        return true;     

        break;     

      default:     

        return false;     

    }     

  }     

}     

$color = 'blue';     

$filter = new ColorFilter($color);     

if ($filter->isPrimaryColor() ) {     

    echo ($color.' is a primary color');     

} else {     

    echo ($color.' is not a primary color');     

}     

?>
```

类外的原始`$color`被传递给`ColorFilter`的构造函数。构造函数中的`$color`变量是传递给它的版本的副本。然后它被分配给`$this->color`，这使得该版本成为副本的副本。

所有这些传递变量的方法都创建了变量值的副本；这叫做按值传递。

***使用参考***

要使用引用传递，需要使用引用操作符`&`(与号)。例如:

```
<?php      

$color = 'blue';      

$settings['color'] = &$color;      

?>
```

```
$settings['color']/#rc#/ now contains a reference to the original `$color` variable.
```

比较下面的例子，第一个使用 PHP 的默认复制行为:

```
<?php      

$color = 'blue';      

$settings['color'] = $color; // Makes a copy      

$color = 'red'; // $color changes      

echo $settings['color']; // Displays "blue"      

?>
```

第二个涉及到通过引用传递:

```
<?php      

$color = 'blue';      

$settings['color'] = &$color; // Makes a reference      

$color = 'red'; // $color changes      

echo $settings['color']; // Displays "red"      

?>
```

通过引用传递允许我们保持新变量与原始源变量“链接”。对新变量或旧变量的更改将反映在两者的值中。

到目前为止，一切顺利。你可能在想，“这有什么大不了的？只要我们得到预期的结果，PHP 是复制还是引用一个变量又有什么区别呢？”对于在过程化程序中传递的变量，你几乎不需要担心引用。然而，当涉及到对象之间的交互时，如果不通过引用传递对象，很可能会得到您不期望的结果。

***参考文献的重要性***

想象一下，你的网站上有一个允许访问者改变网站外观和感觉的机制——一个用户“控制面板”很可能，为了实现这种功能，您需要对一组包含“外观和感觉”数据的变量进行操作的代码，以便独立于应用程序的其余逻辑来修改它们。

简单地用类来表示这一点，首先，让我们看看将存储与外观相关的数据的类:

例 2.8。5.php(节选)

```
<?php      

// Look and feel contains $color and $size      

class LookAndFeel {      

  var $color;      

  var $size;      

  function LookAndFeel()      

  {      

    $this->color = 'white';      

    $this->size = 'medium';      

  }      

  function getColor()      

  {      

    return $this->color;      

  }      

  function getSize()      

  {      

    return $this->size;      

  }      

  function setColor($color)      

  {      

    $this->color = $color;      

  }      

  function setSize($size)      

  {      

    $this->size = $size;      

  }      

}
```

接下来，我们有一个处理渲染输出的类:

例 2.9。5.php(节选)

```
// Output deals with building content for display      

class Output {      

  var $lookandfeel;      

  var $output;      

  // Constructor takes LookAndFeel as its argument      

  function Output($lookandfeel)      

  {      

    $this->lookandfeel = $lookandfeel;      

  }      

  function buildOutput()      

  {      

    $this->output = 'Color is ' . $this->lookandfeel->getColor() .      

      ' and size is ' . $this->lookandfeel->getSize();      

  }      

  function display()      

  {      

    $this->buildOutput();      

    return $this->output;      

  }      

}
```

注意`Output`类的构造函数。它将`LookAndFeel`的一个实例作为它的参数，以便稍后它可以使用它来帮助构建页面的输出。在这一章的后面，我们将更多地讨论类之间的交互方式。

下面是我们如何使用这些类:

例 2.10。5.php(节选)

```
// Create an instance of LookAndFeel      

$lookandfeel = new LookAndFeel();      

// Pass it to an instance of Output      

$output = new Output($lookandfeel);      

// Display the output      

echo $output->display();      

?>
```

这将显示以下消息:

```
Color is white and size is medium
```

现在，让我们假设，为了响应用户控制面板上的一个选项，您想要对站点的外观和感觉进行一些更改。让我们将此付诸行动:

例 2.11。6.php(节选)

```
$lookandfeel = new LookAndFeel(); // Create a LookAndFeel      

$output = new Output($lookandfeel); // Pass it to an Output      

// Modify some settings      

$lookandfeel->setColor('red');      

$lookandfeel->setSize('large');      

// Display the output      

echo $output->display();
```

使用`setColor`和`setSize`方法，我们将颜色改为“红色”，尺寸改为“大”，对吗？嗯，事实上，没有。输出显示仍然说:

```
Color is white and size is medium
```

这是为什么呢？问题是我们只传递了一个`LookAndFeel`对象的副本给`$output`。所以我们对`$lookandfeel`所做的更改对`$output`用来生成显示的副本没有影响。

为了解决这个问题，我们必须修改`Output`类，这样它就可以使用对给定的`LookAndFeel`对象的引用。我们通过改变构造函数来做到这一点:

例 2.12。7.php(节选)

```
 function Output(&$lookandfeel)      

  {      

    $this->lookandfeel = &$lookandfeel;      

  }
```

请注意，我们在这里必须使用两次引用操作。这是因为变量被传递了两次——第一次传递给构造函数，然后再一次将它放在成员变量中。

完成这些更改后，显示如下:

```
Color is red and size is large
```

总之，通过引用传递使目标变量与源变量保持“链接”,这样，如果一个变量发生变化，另一个变量也会发生变化。

***好与坏的做法***

当处理类和对象时，只要涉及到对象就使用引用是一个好主意。有时，您可能必须对数组执行相同的操作，例如当您希望在不同的代码段中对数组进行排序时。但是，在大多数情况下，普通变量不需要这种处理，原因很简单，当您的代码达到您需要这样做的复杂程度时，您会(我希望！)将变量存储在对象中，并通过引用传递完整的对象。

让我们看看其他一些你可能需要引用的情况…

```
// Make sure $myObject is a reference to       

// the variable created by the new keyword       

$myObject = &new MyClass();
```

这乍一看很奇怪，但是请记住，由 new 关键字创建的变量正在这里传递——即使您看不到它。引用操作符使 PHP 不必创建新创建对象的副本来存储在`$myObject`中。

```
class Bar {       

}       

class Foo {       

  // Return by reference       

  function &getBar()       

  {       

    return new Bar();       

  }       

}       

// Instantiate Foo       

$foo = &new Foo();       

// Get an instance of Bar from Foo       

$bar = &$foo->getBar();
```

在上面的例子中，你会注意到`Foo`类中的`getBar`方法。通过在函数名前面加上引用运算符，函数返回值通过引用传递。注意，在将`getBar`的返回值赋给`$bar`时，我们还必须使用一个引用操作符。当类方法将返回对象时，通常使用这种技术。

以下是哪些不好的做法:

```
function display($message) {       

  echo $message;       

}       

$myMessage = 'Hello World!';       

// Call time pass by reference - bad practice!       

display(&$message);
```

这就是所谓的调用时按引用传递，PHP 使用 php.ini 中的以下设置来控制它:

```
allow_call_time_pass_reference = Off
```

默认情况下，在最近的 PHP 版本中，上述设置应该被切换到 Off 打开它是 PHP 开发者“不赞成”的。关闭时，每当函数调用指定参数应该通过引用传递时，PHP 都会生成警告错误。因此，关闭此设置是个好习惯。

通过引用传递调用时间是一件“坏事”的原因是，通过引用传递调用时间会使代码极难理解。我偶尔会看到 PHP XML 解析器使用调用时传递引用的方式编写——几乎不可能知道发生了什么。

关于变量是否通过引用传递的“决定”属于被调用的函数，而不是调用它的代码。上面正确编写的代码如下所示:

```
// Accept by reference - good practice       

function display(&$message)       

{       

  echo $message;       

}       

$myMessage = 'Hello World!';       

display($message);
```

***性能问题***

根据应用程序的规模，使用引用时可能需要考虑一些性能问题。

在将一个变量复制到另一个变量的简单情况下，PHP 的内部引用计数特性可以防止不必要的内存使用。举个例子，

```
$a = 'the quick brown fox';       

$b = $a;
```

在上面的例子中，$b 的值不会占用任何额外的内存，因为 PHP 的内部引用计数将隐式引用`$b`和`$a`到内存中的相同位置，直到它们的值变得不同。这是 PHP 的一个内部特性，影响性能而不影响行为。我们不需要太担心它。

然而，在某些情况下，使用引用更快，特别是对于大型数组和对象，PHP 的内部引用计数不能使用。因此内容必须被复制。

因此，为了获得最佳性能，您应该执行以下操作:

*   对于简单的值，如整数和字符串，尽可能避免引用。
*   对于复杂的值，如数组和对象，尽可能使用引用。

***引用和 PHP 5***

在 PHP 5 中，引用将不再是一个问题，因为 PHP 在传递对象时的默认行为是通过引用传递。如果你需要一个对象的副本，你可以使用特殊的`__clone`方法来创建副本。

从本质上说，这种改变使 PHP 与大多数面向对象编程语言(如 Java)保持一致，并且肯定会大大减少围绕这个主题的混乱。不过，就目前而言，直到 PHP 5 被广泛采用之前，了解引用是如何工作的非常重要。

##### 我如何利用继承？

继承是面向对象范例的基础之一，也是其强大功能的重要组成部分。继承是不同类之间的关系，其中一个类被定义为另一个类的子类或子类。子类继承父类中定义的方法和成员变量，允许它为父类“增值”。

要了解 PHP 中的继承是如何工作的，最简单的方法是通过例子。假设我们有这个简单的类:

例 2.13。8.php(节选)

```
<?php        

class Hello {        

  function sayHello()        

  {        

    return 'Hello World!';        

  }        

}
```

使用 extends 关键字，我们可以创建一个类，它是 Hello:

例 2.14。8.php(节选)

```
class Goodbye extends Hello {        

  function sayGoodbye()        

  {        

    return 'Goodbye World!';        

  }        

}
```

```
Goodbye is now a child of Hello. Expressed the other way around, Hello is the parent or superclass of Goodbye. Now, we can simply instantiate the child class and have access to the sayHello and the sayGoodbye methods using a single object:
```

例 2.15。8.php(节选)

```
$msg = &new Goodbye();        

echo $msg->sayHello() . '<br />';        

echo $msg->sayGoodbye() . '<br />';        

?>
```

这个例子展示了继承如何工作的基础，但是没有展示它真正的力量…这是伴随着重写而来的。

***压倒***

当我们给子节点中的一个函数取了与父节点中的一个函数相同的名字时会发生什么？一个例子:

例 2.16。9.php(节选)

```
<?php        

class Hello {        

  function getMessage()        

  {        

    return 'Hello World!';        

  }        

}        

class Goodbye extends Hello {        

  function getMessage()        

  {        

    return 'Goodbye World!';        

  }        

}
```

这两个类有相同的方法名`getMethod`。这对 PHP 来说是完全可以接受的——它不会抱怨一个方法被声明两次。

下面是我们使用这些类时发生的情况:

例 2.17。9.php(节选)

```
$hello = &new Hello();        

echo $hello->getMessage() . '<br />';        

$goodbye = &new Goodbye();        

echo $goodbye->getMessage() . '<br />';        

?>
```

输出如下所示:

```
Hello World!        

Goodbye World!
```

通过`$goodbye`对象调用`getMessage`显示“再见世界！”子类中的方法重写父类中的方法。

您也可以让子类在内部使用父类的方法，同时重写它。例如:

例 2.18。10.php

```
<?php        

class Hello {        

  function getMessage()        

  {        

    return 'Hello World!';        

  }        

}        

class Goodbye extends Hello {        

  function getMessage()        

  {        

    $parentMsg = parent::getMessage();        

    return $parentMsg . '<br />Goodbye World!';        

  }        

}        

$goodbye = &new Goodbye();        

echo $goodbye->getMessage() .'<br />';        

?>
```

使用 parent 关键字，我们可以调用父类的方法。

请注意，我们也可以通过名称调用父类来获得完全相同的结果:

```
class Goodbye extends Hello {        

  function getMessage() {        

    $parentMsg = Hello::getMessage();        

    return $parentMsg . '<br />Goodbye World!';        

  }        

}
```

注意，我们已经用 Hello 类的名称替换了 parent 关键字。输出完全相同。但是，使用 parent 可以使您在处理子类时不必记住父类的名称，这是推荐的语法。

从非静态方法调用`parent::getMessage()`或`Hello::getMessage()`与调用静态函数是不同的。这是一个涉及继承的特例。父类中被调用的函数保留对实例数据的访问，因此不是静态的。这可以通过以下方式证明:

例 2.19。11.php

```
<?php        

class A {        

    var $a = 1;        

    function printA()        

    {        

        echo $this->a;        

    }        

}        

class B extends A {        

    var $a = 2;        

    function printA()        

    {        

       parent::printA();        

       echo "nWasn't that great?";        

    }        

}        

$b = new B();        

$b->printA();        

?>
```

上面生成的输出如下:

```
2
```

那不是很棒吗？

PHP 不级联构造函数

大多数面向对象的语言，如 Java，在运行子类中的重写构造函数之前，会自动运行父类的构造函数。这被称为级联构造函数——这是 PHP 没有的特性。

如果您在子类中创建构造函数，请注意您完全重写了父类的构造函数，并且如果您仍然希望父类处理它的对象初始化，则必须从新的构造函数中显式调用它。

覆盖声明的成员变量的实现方式与方法完全相同，尽管您不太可能经常使用这个特性。

***传承在行动***

现在您已经对继承在 PHP 中的使用有了一个大致的概念，是时候看一个例子了，这个例子可以让您更好地理解如何应用继承。

下面的示例为网页实现了一个简单的导航系统，生成出现在页面顶部的 HTML。通过让一个类继承另一个类，可以在需要时向页面添加“线索”导航。

首先，`StandardHeader`类处理为页面顶部生成 HTML，以及提供`setHeader`和`getHeader`方法来访问存储 HTML 的变量。

例 2.20。12.php(节选)

```
<?php         

/**         

 * A standard header for a Web page         

 */         

class StandardHeader {         

  /**         

   * The header HTML is stored here         

   */         

  var $header = '';         

  /**         

   * The constructor, taking the name of the page         

   */         

  function StandardHeader($title)         

  {         

    $html = <<<EOD         

<html>         

<head>         

<title> $title </title>         

</head>         

<body>         

<h1>$title</h1>         

EOD;         

    $this->setHeader($html);         

  }         

  /**         

   * General method for adding to the header         

   */         

  function setHeader($string)         

  {         

    if (!empty($this->header)) {         

      $this->header .= $string;         

    } else {         

      $this->header = $string;         

    }         

  }         

  /**         

   * Fetch the header         

   */         

  function getHeader()         

  {         

    return $this->header;         

  }         

}
```

现在，子类`CategoryHeader`为其父类带来了额外的功能，将“面包屑”链接添加到生成的 HTML 中。我们不需要重新创建`setHeader`和`getHeader`方法，因为这些方法是在`CategoryHeader`被实例化时从`StandardHeader`继承的。

例 2.21。12.php(节选)

```
/**         

 * Subclass for dealing with Categories, building a breadcrumb         

 * menu         

 */         

class CategoryHeader extends StandardHeader {         

  /**         

   * Constructor, taking the category name and the pages base URL         

   */         

  function CategoryHeader($category, $baseUrl)         

  {         

    // Call the parent constructor         

    parent::StandardHeader($category);         

    // Build the breadcrumbs         

    $html = <<<EOD         

<p><a href="$baseUrl">Home</a> >         

<a href="$baseUrl?category=$category">$category</a></p>         

EOD;         

    // Call the parent setHeader() method         

    $this->setHeader($html);         

  }         

}
```

现在让我们使用这两个类:

```
Example 2.22\. 12.php (excerpt)         

// Set the base URL         

$baseUrl = '12.php';         

// An array of valid categories         

$categories = array('PHP', 'MySQL', 'CSS');         

// Check to see if we're viewing a valid category         

if (isset($_GET['category']) &&         

    in_array($_GET['category'], $categories)) {         

  // Instantiate the subclass         

  $header = new **CategoryHeader**($_GET['category'], $baseUrl);         

} else {         

  // Otherwise it's the home page. Instantiate the Parent class         

  $header = new **StandardHeader**('Home');         

}         

// Display the header         

echo $header->getHeader();         

?>         

<h2>Categories</h2>         

<p><a href="<?php echo $baseUrl; ?>?category=PHP">PHP</a></p>         

<p><a href="<?php echo $baseUrl; ?>?category=MySQL">MySQL</a></p>         

<p><a href="<?php echo $baseUrl; ?>?category=CSS">CSS</a></p>         

</body>         

</html>
```

如您所见，上面的控制逻辑寻找一个`$_GET['category']`变量。如果存在，它会创建一个`CategoryHeader`的实例，显示导航以允许用户找到返回主页的路。但是如果它不存在，它会创建一个父节点`StandardHeader`的实例，当用户查看主页时应用(因此不需要面包屑找到返回的路)。

换句话说，继承允许我们添加我们需要的额外功能，而不必复制已经驻留在父类中的逻辑；现有的方法和逻辑可以通过子类重用。

继承提供了一种强大的机制，可以使类模块化，解决特定的问题，同时仍然可以使用共享的方法和变量，而不用考虑我们正在处理的特定对象。

*避免深度继承结构*

作为一般的经验法则，当使用继承来构建类的层次结构时，避免超过两代。

这样做通常是一个糟糕设计的标志，其中类以不同方式交互的机会(见下一个解决方案)被错过了。实际上，拥有两代以上的类通常会导致各种各样的调试问题，并使代码难以维护。例如，很难跟踪您在层次结构中使用的较高的变量名。

##### 物体是如何相互作用的？

除了继承，对象还有其他交互方式；例如，一个对象使用另一个对象。在许多方面，这种交互比继承更重要，这也是面向对象范例显示其真正威力的地方。

一个对象可以通过两种方式使用另一个对象:聚合和组合。

***聚合***

当一个对象被“临时借出”给另一个对象时，就会发生聚合第二个对象通常通过第一个对象的成员函数传递给第一个对象。然后，第一个对象能够调用第二个对象中的方法，允许它为自己的目的使用第二个对象中存储的功能。

聚合的一个常见例子是数据库连接类。假设您将一个数据库连接类传递给某个其他类，然后该类使用该数据库连接类来执行查询。执行查询的类聚集了数据库连接类。

这里有一个使用`MySQL`类的简单例子，我们将在第 3 章 PHP 和 MySQL 中创建它:

例 2.23。13.php

```
<?php          

// Include the MySQL database connection class          

require_once 'Database/MySQL.php';          

// A class which aggregates the MySQL class          

class Articles {          

  var $db;          

  var $result;          

  // Accept an instance of the MySQL class          

  function Articles(&$db)          

  {          

    // Assign the object to a local member variable          

    $this->db = &$db;          

    $this->readArticles();          

  }          

  function readArticles()          

  {          

    // Perform a query using the MySQL class          

    $sql = "SELECT * FROM articles LIMIT 0,5";          

    $this->result = &$this->db->query($sql);          

  }          

  function fetch()          

  {          

    return $this->result->fetch();          

  }          

}          

// Create an instance of the MySQL class          

$db = &new MySQL('localhost', 'harryf', 'secret', 'sitepoint');          

// Create an instance of the Article class, passing it the MySQL          

// object          

$articles = &new Articles($db);          

while ($row = $articles->fetch()) {          

  echo '<pre>';          

  print_r($row);          

  echo '</pre>';          

}          

?>
```

在上面的例子中，我们在`Articles`类之外实例化了`MySQL`类，然后在实例化文章时将它传递给`Articles`构造函数。`Articles`然后能够使用`MySQL`对象来执行特定的查询。在这种情况下，`Articles`聚集了`MySQL`对象。图 2.2 展示了这种与 UML 的关系。

![1264_aggregation](img/f502ad0a043b27e6259592d408fd5e6c.png)
图 2.2。聚合

***作文***

当一个对象“完全拥有”另一个对象时，就发生了合成。即，第一个对象负责创建(实例化)第二个对象。在许多情况下，这可能是有用的，尽管通常情况下，当第一个对象可能是唯一需要使用第二个对象的对象时，就使用组合。

《第 1 章，访问控制》中的一个例子是`Auth`类，它组成了`Session`类的一个实例，在构造函数中创建它:

```
class Auth {          

  ...          

  /**          

   * Instance of Session class          

   * @var Session          

   */          

  var $session;          

  ...          

  function Auth (&$dbConn, $redirect, $md5 = true)          

  {          

    $this->dbConn = &$dbConn;          

    $this->redirect = $redirect;          

    $this->md5 = $md5;          

    $this->session = &new Session();          

    $this->checkAddress();          

    $this->login();          

  }
```

因为`Auth`类需要读写会话变量，而应用程序中只有有限数量的其他不相关的类可能也需要使用`Session`，所以它创建自己的`Session`对象是合乎逻辑的。

图 2.3 展示了这个例子中 UML 的组成。

![1264_composition](img/bf65c0e2a9e8825a3afeff66b94ef3bc.png)
图 2.3。作文

***发现差异***

一般的“思想测试”是为了确定物体 A 是聚集还是组成物体 B，要问“如果物体 A 死了会发生什么？对象 B 还会活着吗？”如果对象 B 比对象 A 死的时间长，则称对象 A 聚合了对象 B。但如果对象 B 在对象 A 死亡时死亡，则称对象 A 合成了对象 B。

就实际开发而言，知道何时应用聚合或组合很重要。

聚合的优点是开销较低，因为单个对象将由许多其他对象共享。当然，聚合数据库连接类是一个好主意；将它与每个想要进行查询的对象组合在一起可能需要您拥有到数据库的多个连接，这将在您的站点吸引大量流量时迅速停止您的应用程序。

组合的优点是从外部使类更容易工作。使用该类的代码不必担心向它传递它需要的其他对象，而在复杂的应用程序中，这通常会变得棘手，并导致设计“变通”组合还有一个优点，就是您可以确切地知道哪个类可以访问组合的对象。使用聚合，共享聚合对象的另一个对象可能会对其状态做一些事情，就使用它的其他类而言，“破坏”该对象。

***多态性***

面向对象编程的另一个强大方面是多态性——不同类共享一个接口的能力。

接口是一个或多个方法，它允许您将一个类用于特定的目的。例如，您可以有两个数据库连接类——一个用于 MySQL，一个用于 PostgreSQL。只要它们都提供了一个`query`方法，您就可以在不同的数据库上交替使用它们来运行查询。`query`方法是两个类共享的简单接口。

共享同一个接口的类通常是从提供公共方法的父类继承而来的。同样，这最好通过例子来理解。

首先，我们定义一个抽象基类`Message`，它提供了公共方法`getMessage`。在`Message`类下面，我们定义了具体的类，每个类创建一个特定的消息。

术语“抽象”和“具体”指的是类的用法，特别是，一个类是否打算被直接使用。抽象类是这样一种类，其中某些功能或结构将由所有子类共享，但不打算直接使用；通常，它有一个或多个没有任何用处的空方法。换句话说，你不应该从抽象类中创建对象。具体类是抽象类的子类，从中可以创建对象。一些语言，比如 Java，在语言语法中提供了对抽象类的支持——这是 PHP 4 所没有的。在设计应用程序时，您仍然可以使用抽象类的概念，尽管您可能会考虑添加文档来告诉使用代码的其他开发人员该类是抽象的。

例 2.24。14.php(节选)

```
<?php           

class Message {           

  var $message;           

  function setMessage($message)           

  {           

    $this->message = $message;           

  }           

  function getMessage()           

  {           

    return $this->message;           

  }           

}           

class PoliteMessage extends Message {           

  function PoliteMessage()           

  {           

    $this->setMessage('How are you today?');           

  }           

}           

class TerseMessage extends Message {           

  function TerseMessage()           

  {           

    $this->setMessage('Howzit?');           

  }           

}           

class RudeMessage extends Message {           

  function RudeMessage()           

  {           

    $this->setMessage('You look like *%&* today!');           

  }           

}
```

现在，我们定义了`MessageReader`类，该类通过其构造函数接受一组`Message` 对象。

例 2.25。14.php(节选)

```
class MessageReader {           

  var $messages;           

  function MessageReader(&$messages) {           

    $this->messages = &$messages;           

    $this->readMessages();           

  }           

  function readMessages() {           

    foreach ($this->messages as $message) {           

      echo $message->getMessage() . '<br />';           

    }           

  }           

}
```

这里需要注意的重要一点是，就`MessageReader`而言，“`Message`对象”是从`Message`类或其子类实例化的任何对象。你看到了吗，在`readMessages`方法中，我们称之为`getMessage`方法。这段代码将在任何有`getMessage`方法的对象上工作——包括任何`Message`的子类。

现在，为了证明这一点，让我们随机使用三个子类创建一些`Message`对象:

例 2.26。14.php(节选)

```
$classNames =           

  array('PoliteMessage', 'TerseMessage', 'RudeMessage');           

$messages = array();           

srand((float)microtime() * 1000000); // Prepares random shuffle           

for ($i = 0; $i < 10; $i++) {           

  shuffle($classNames);           

  $messages[] = new $classNames[0]();           

}           

$messageReader = new MessageReader($messages);           

?>
```

通过创建数组`$classNames`，然后反复移动它，我们可以获取数组的第一个元素，并用它来创建一个新对象:

```
 $messages[] = new $classNames[0]();
```

这是一个可变函数的例子。对表达式`$classNames[0]`求值，以确定要调用的构造函数的名称(`PoliteMessage`、`TerseMessage`或`RudeMessage`)。

最后，`$messages`数组包含十条随机选择的消息，并在实例化时传递给`MessageReader`的构造函数。

以下是一个示例结果:

```
You look like *%&* today!           

Howzit?           

How are you today?           

How are you today?           

How are you today?           

You look like *%&* today!           

How are you today?           

How are you today?           

Howzit?           

How are you today?
```

每次我们执行脚本时，列表都是不同的。

因为所有具体的消息类共享相同的`getMethod`函数(即它们实现相同的接口)，所以`MessageReader`类能够提取数据，而不知道它正在处理哪种特定类型的消息。一组相关类可以互换工作的能力被称为多态性，如图 2.4 中的 UML 图所示。

![1264_polymorphism](img/47ef18b9df547a2df4064a055401cb8a.png)
图 2.4。多态性

一旦你意识到它的价值，面向对象编程的这一方面会变得非常强大。例如，您可能有一个表示 HTML 标签的对象集合，每个对象都是父类`HTMLTag`的子类，它们都从父类继承了一个`render`方法。另一个处理页面呈现的类可以接受一组`HTMLTag`对象，并通过调用每个对象的呈现方法来创建页面。

##### 进一步阅读

*   面向对象的 PHP:分页结果集[https://www.sitepoint.com/article/662](https://www.sitepoint.com/blog/)
*   PHP 参考资料解释[http://www.zez.org/article/articleview/77/](http://www.zez.org/article/articleview/77/)
*   PHP References
    Part 1: [http://www.onlamp.com/pub/a/php/2002/08/15/php_foundations.html](http://www.onlamp.com/pub/a/php/2002/08/15/php_foundations.html)

    第二部分:[http://www . onlamp . com/pub/a/PHP/2002/09/12/PHP _ foundations . html](http://www.onlamp.com/pub/a/php/2002/09/12/php_foundations.html)

*   PHP 引用计数和别名[http://www.zend.com/zend/art/ref-count.php](http://www.zend.com/zend/art/ref-count.php)

在接下来的几周里，请在 SitePoint 上关注 PHP 选集的更多章节！如果你等不及了，[下载样本章节](https://www.sitepoint.com/books/phpant1/signoff.php)，或者[现在就订购你自己的副本](https://www.sitepoint.com/blog/)！

## 分享这篇文章