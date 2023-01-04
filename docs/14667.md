# 用 PHP 和 PEAR 生成电子表格

> 原文：<https://www.sitepoint.com/pear-spreadsheet-excel-writer/>

在上一篇文章[PEAR 入门](https://www.sitepoint.com/article/getting-started-with-pear)中，您已经安装并运行了 PEAR 包管理器。现在，是时候利用[PEAR::spread sheet _ Excel _ Writer](http://pear.php.net/Spreadsheet_Excel_Writer)这个用于生成 Excel 电子表格的库来充分利用 [PEAR](http://pear.php.net) 了。

以下是我们今天要讲的内容:

*   PEAR::Spreadsheet_Excel_Writer 简介
*   寻找方法:介绍 API
*   添加单元格格式:赏心悦目很重要
*   添加 Excel 函数:C1 + D1 = 2！

请注意，我假设您对 Excel 有初步的了解，但不要太认真。你甚至不需要拥有一份拷贝——这里所有的例子都同样适用于 OpenOffice Calc。

##### PEAR::Spreadsheet_Excel_Writer 简介

让我们面对现实吧——虽然你和我可能全神贯注于 XML 标记、选项卡式浏览、Emacs 和 VI 等编辑器，以及最小化系统资源的使用，但计算机使用世界的其余部分正愉快地与 Microsoft Office 等一起前进。虽然他们可能会对您使用 HTML 表格所做的惊人的事情留下一定的印象，但当涉及到处理数字时，Excel 几乎是一个标准。

更重要的是，Excel 被那些处理金融和货币的人广泛使用。换句话说，没有按时支付你账单的会计部门可能正在使用它。让会计师的生活更轻松，他们可能会回报你…

如果您能让您的客户以 Excel 电子表格的形式访问可下载的数字数据，那不是很好吗？好消息是你可以，用[PEAR::spread sheet _ Excel _ Writer](http://pear.php.net/Spreadsheet_Excel_Writer)。

“不可能！”你哭了。Excel 使用一些专有的微软文件格式。这可不行！”

是的，可以。Spreadsheet_Excel_Writer 生成“真实的东西”，包括 Excel 函数、格式等。不，我们不是在这里谈论生成逗号分隔的文件，或者使用 [COM](http://www.php.net/COM) 扩展(或者任何其他扩展，就此而言)。这是用纯 PHP 编写的，在基于 UNIX 的 Web 服务器上运行和在基于 Windows 的服务器上运行一样好——而且没有必要去打扰你的主机提供商。简而言之，PEAR::Spreadsheet_Excel_Writer，加上来自 [PEAR::OLE](http://pear.php.net/OLE) 的额外魔力，“理解”微软的 Excel 文件格式。

让我们花点时间向 [Xavier Noguer](http://www.xavier-noguer.com/) 致敬，他在[Mika Tuupola](http://www.appelsiini.net/~tuupola/)for spread sheet _ Excel _ Writer 的帮助下，出色地将所有这些引入 PHP。

现在，事不宜迟，掌握了 PEAR 的包管理器的全部知识之后，您已经在上个月成功地安装了它(对吗？)，让我们从下载库开始。打开命令提示符并键入:

`$ pear install OLE
$ pear install Spreadsheet_Excel_Writer`

就是这样。我们准备好行动了！

**重要提示！**对于本文中的例子，我使用了 PEAR::OLE 版本 0.5 和 PEAR::Spreadsheet_Excel_Writer 版本 0.7。请注意，随着未来版本的发布，情况可能会有所变化。

##### 寻找你周围的路

首先，让我们生成一个非常简单的电子表格。

```
<?php 

// Include PEAR::Spreadsheet_Excel_Writer 

require_once "Spreadsheet/Excel/Writer.php"; 

// Create an instance 

$xls =& new Spreadsheet_Excel_Writer(); 

// Send HTTP headers to tell the browser what's coming 

$xls->send("test.xls"); 

// Add a worksheet to the file, returning an object to add data to 

$sheet =& $xls->addWorksheet('Binary Count'); 

// Write some numbers 

for ( $i=0;$i<11;$i++ ) { 

  // Use PHP's decbin() function to convert integer to binary 

  $sheet->write($i,0,decbin($i)); 

} 

// Finish the spreadsheet, dumping it to the browser 

$xls->close(); 

?>
```

文件名:example 1 . PHP

将浏览器指向该脚本，假设它知道 Excel(或 OpenOffice Calc)，就会弹出一个电子表格，其中包含二进制的数字 0 到 10。

**存储文件**

在这种情况下，电子表格是动态呈现的—服务器上没有存储任何内容。如果您想生成一个文件，您可以通过向构造函数传递一个合法的路径和文件名，并避免发送 HTTP 头，来省去生成一个没有更改的工作表所需的处理，如下所示:

```
<?php 

// Has a spreadsheet been created? 

if ( !file_exists('sheets/binary.xls') ) { 

  // Include PEAR::Spreadsheet_Excel_Writer 

  require_once "Spreadsheet/Excel/Writer.php"; 

  // Create an instance, passing the filename to create 

  $xls =& new Spreadsheet_Excel_Writer('sheets/binary.xls'); 

  // Add a worksheet to the file, returning an object to add data to 

  $sheet =& $xls->addWorksheet('Binary Count'); 

  // Write some numbers 

  for ( $i=0;$i<11;$i++ ) { 

    // Use PHP's decbin() function to convert integer to binary 

    $sheet->write($i,0,decbin($i)); 

  } 

  // Finish the spreadsheet, dumping it to the browser 

  $xls->close(); 

} 

?>
```

您的电子表格可以在此下载。

文件名:example_2.php

如果您使用的是基于 UNIX 的系统，记得修改存储电子表格的目录的权限，这样 PHP 就可以写入它。

**API 概述**

好了，我们完成了基本的工作。不过，为了充分利用 PEAR::Spreadsheet_Excel_Writer，您需要对这个 API 有更多的了解。PEAR 网站上可用的 API 文档[现在已经过时了(自从那个版本的文档生成以来，它似乎已经增长了很多)。令人欣慰的是，作者在很大程度上已经将内联文档添加到代码中，所以你可以通过下载](http://pear.php.net/manual/en/package.fileformats.spreadsheet-excel-writer.php)[PHP document](http://www.phpdoc.org)并指向包含所有
Spreadsheet_Excel_Writer 源代码的目录来制作自己的 API 文档。如果你需要帮助来开始使用 phpDocumentor(并且原谅推销)，这将在第 2 卷:PHP 选集[的应用](https://www.sitepoint.com/books/phpant1/)中讨论。

您将总是开始工作的主类， *Spreadsheet_Excel_Writer* ，表示对库中所有其他类的访问点。它提供了两个重要的工厂方法(实际上是在父类*spread sheet _ Excel _ Writer _ Workbook*中定义的):

*   `addWorksheet()`–返回*电子表格 _ Excel _ Writer _ 工作表*的一个实例。大部分工作是用这个类的实例完成的(如上)，允许您写入单个工作表的单元格(Excel 电子表格是包含一个或多个工作表的工作簿)。
*   `addFormat()`–返回*spread sheet _ Excel _ Writer _ Format*的实例，该实例用于添加工作表中单元格的可视格式。

该库包含另外三个您应该知道的类，尽管您可能不会发现自己必须直接使用它们:

*   *spread sheet _ Excel _ Writer _ Validator*可以添加单元格验证规则。现在，基本上没有这个类的文档。这似乎是实验性的代码，所以我将在这里避免它。基本上，它似乎提供了对最终用户输入 Excel 单元格的数据执行基本验证的能力。通过扩展类，可以实现更复杂的规则，如根据单元格列表进行验证。spread sheet _ Excel _ Writer _ Workbook 类提供了方法`addValidator()`来创建验证的实例，而 spread sheet _ Excel _ Writer _ Worksheet 允许使用`setValidation()`方法将验证器分配给单元格。
*   *spread sheet _ Excel _ Writer _ Parser*，这是一个 Excel 电子表格函数的解析器，允许您检查函数是否是有效的 Excel 语法。这可以帮助您在 PHP 中向电子表格添加函数时捕获错误。
*   最后，*spread sheet _ Excel _ Writer _ biff Writer*用于生成存储 Excel 文件的二进制文件格式。如果你对 Excel 编程感兴趣，研究它在做什么可能会很有趣，但是，否则，这个库会完全隐藏你，所以你不需要担心。

**零点指标混乱**

我们在上面的例子中看到的一个值得注意的方法是`Spreadsheet_Excel_Writer_Worksheet::write()`方法，您将大量使用它来向单元格添加数据。如果您习惯了 Excel 中单元格的寻址方式，可能会有点困惑。

`write()`的第一个参数是*的行号*。电子表格顶部的第一个行号在 PEAR::Spreadsheet_Excel_Writer 中是 0(零)，而不是 Excel 中的 1。

第二个参数是第*列号*。现在，Excel 中的列是用字母表中的字母来标识的，而不是数字，所以您只需要习惯在两者之间进行转换。字母 F 是字母表中的第 6 个，所以第二个参数是… 5(当然！)—PEAR::spread sheet _ Excel _ Writer 中最左边的列是 0(零)，所以需要减一得到列号。

`write()`的第三个参数是要放入单元格的数据；还有一个可选的第四个参数，用于对单元格应用可视格式。

在 spread sheet _ Excel _ Writer _ Worksheet 类中有更多的方法，例如冻结和解冻工作表的某些部分，以及将工作表整体格式化以便打印。我将在后面的例子中涉及其中的一些，但是您必须自己探索大多数。

##### 添加单元格格式

那么，如何使电子表格看起来漂亮呢？我们可以用 PEAR::Spreadsheet_Excel_Writer 通过使用`addFormat()`函数获取一个类型为 spread sheet _ Excel _ Writer _ Format 的对象来完成这个任务。我们使用它提供的(大量)方法将格式应用于该对象，然后将 spread sheet _ Excel _ Writer _ Worksheet 的`write()`方法传递给它，以将格式分配给我们添加的特定单元格。

作为一个“真实世界”的例子，假设我想让我的在线商店 phpPetstore.com 的用户能够下载他们刚刚购买的商品的“收据”,作为包含单个工作表的工作簿。

我从平常的事情开始我的工作表:

```
<?php  

require_once "Spreadsheet/Excel/Writer.php";  

// Create workbook  

$xls =& new Spreadsheet_Excel_Writer();  

// Create worksheet  

$cart =& $xls->addWorksheet('phpPetstore');
```

接下来(幸运的是，我只使用了四列)，我将向工作表添加一个标题，合并一些单元格来放置它。在这里，您将第一次体验到格式化是如何完成的:

```
// Some text to use as a title for the worksheet  

$titleText = 'phpPetstore: Receipt from ' . date('dS M Y');  

// Create a format object  

$titleFormat =& $xls->addFormat();  

// Set the font family - Helvetica works for OpenOffice calc too...  

$titleFormat->setFontFamily('Helvetica');  

// Set the text to bold  

$titleFormat->setBold();  

// Set the text size  

$titleFormat->setSize('13');  

// Set the text color  

$titleFormat->setColor('navy');  

// Set the bottom border width to "thick"  

$titleFormat->setBottom(2);  

// Set the color of the bottom border  

$titleFormat->setBottomColor('navy');  

// Set the alignment to the special merge value  

$titleFormat->setAlign('merge');  

// Add the title to the top left cell of the worksheet,  

// passing it the title string and the format object  

$cart->write(0,0,$titleText,$titleFormat);  

// Add three empty cells to merge with  

$cart->write(0,1,'',$titleFormat);  

$cart->write(0,2,'',$titleFormat);  

$cart->write(0,3,'',$titleFormat);  

// The row height  

$cart->setRow(0,30);  

// Set the column width for the first 4 columns  

$cart->setColumn(0,3,15);
```

首先注意，我通过代表整个电子表格的$xls 对象调用`addFormat()`获得了格式化对象。然后，我对对象应用一些特定的格式(像`setBold()`这样的方法名称是不言而喻的——参见 API 文档以获得格式方法的完整列表)。

一旦我完成了格式化，我就调用工作表对象$cart 上的`write()`来添加到一个单元格，将格式化对象作为第四个参数传递。

我在这里做的一件不寻常的事情是合并四个细胞。通过在格式化对象上调用 setAlign('merge ')(通常您会使用' left '，' right '或' center ')，我已经告诉 Spreadsheet_Excel_Writer 它应该合并所有应用了该格式的单元格。这就是为什么我创建了三个空单元格并对它们应用了格式。

使用`setRow()`允许我修改行高，使其大于默认的 Excel 行高。此方法还有其他可选的格式参数，例如，允许您将格式对象应用于整行。同样，对于`setColumn()`，我可以设置列宽，并选择性地应用进一步的格式。不同之处在于，`setRow()`仅适用于单个行，而`setColumn()`适用于一系列列。

到目前为止，一切顺利。现在，我需要一些数据添加到工作表中。为了避免使示例变得复杂(通过涉及数据库)，我将使用关联数组的索引数组，我们可以假设它是 SQL select 的结果:

```
$items = array (  

  array( 'description'=>'Parrot'  ,'price'=>34.0,  'quantity'=>1),  

  array( 'description'=>'Snake'  ,'price'=>16.5,  'quantity'=>2),  

  array( 'description'=>'Mouse'  ,'price'=>1.25,  'quantity'=>10),  

);
```

“数据库中的列”是二阶数组的键；“描述”、“价格”和“数量”，所以我们需要做的下一件事是添加列标题以及一个额外的“总计”标题，我将在稍后使用:

```
// Set up some formatting  

$colHeadingFormat =& $xls->addFormat();  

$colHeadingFormat->setBold();  

$colHeadingFormat->setFontFamily('Helvetica');  

$colHeadingFormat->setBold();  

$colHeadingFormat->setSize('10');  

$colHeadingFormat->setAlign('center');  

// An array with the data for the column headings  

$colNames = array('Item','Price($)','Quantity','Total');  

// Add all the column headings with a single call  

// leaving a blank row to look nicer  

$cart->writeRow(2,0,$colNames,$colHeadingFormat);
```

您已经看到了格式。你之前没见过的是`writeRow()`法。这基本上做了与`write()`相同的事情，但是允许您从指定的行号和列号开始，从左到右插入一个数据数组。这是减少代码行的一条便捷捷径。

我想做的另一件事是确保当我们滚动项目列表时，列标题总是可见的。在 Excel 中，这可以通过“冻结”一个“窗格”来实现，即选择一个单元格块，当用户滚动数据时，该单元格块将“悬停”,允许用户看到列标题(在这种情况下),告诉他们数据代表什么。PEAR::Spreadsheet_Excel_Writer 也是如此:

```
// The cell group to freeze  

// 1st Argument - vertical split position  

// 2st Argument - horizontal split position (0 = no horizontal split)  

// 3st Argument - topmost visible row below the vertical split  

// 4th Argument - leftmost visible column after the horizontal split  

$freeze = array(3,0,4,0);  

// Freeze those cells!  

$cart->freezePanes($freeze);
```

请注意，“冻结”是通过工作表对象$cart 直接应用的，而不是通过格式化对象，因为它应用于单元格集合。另一方面，格式适用于单个单元格。

最后，我遍历购物车中的商品，将数据添加到工作表中:

```
// Pseudo data  

$items = array (  

  array( 'description'=>'Parrot'  ,'price'=>34.0,  'quantity'=>1),  

  array( 'description'=>'Snake'  ,'price'=>16.5,  'quantity'=>2),  

  array( 'description'=>'Mouse'  ,'price'=>1.25,  'quantity'=>10),  

);  

// Use this to keep track of the current row number  

$currentRow = 4;  

// Loop through the data, adding it to the sheet  

foreach ( $items as $item ) {  

    // Write each item to the sheet  

  $cart->writeRow($currentRow,0,$item);  

  $currentRow++;  

}
```

这里没有什么特别新的东西，只是您会发现在循环数据时跟踪行号和列号变得很重要，因为需要这些来将数据插入正确的位置。

基本就是这样。如果你是 PHP 中面向对象的新手，乍一看，这可能有点吓人，但是你会注意到所有的方法都有很好的名字，以至于你通常可以通过查看它们来猜测它们的用途。从另一个对象中获取一个对象的概念可能是新的，但是当您仔细想想，通过调用 Workbook 对象的`addWorksheetSheet()`方法创建一个工作表对象，并在工作表的`write()`位置向单元格添加格式化对象是有意义的。

##### 添加 Excel 函数

现在，您可以制作一个看起来不错的电子表格，但是，正如任何 Excel 专业人员可以告诉您的那样，简单地显示原始数据不是很有用。当你开始使用 Excel 的功能(也许你自己也有)对原始数据进行计算，并把它变成更有趣的东西时，生活变得非常有趣。

现在我*不是*Excel 高手(这也不会变成 Excel 教程)，但是很明显我的购物车收据需要更聪明，所以我需要根据我已经添加的数据添加一些计算。对于每一行，我想显示“商品总成本”——原始数据包含商品的单价和购买的商品数量:

```
"total item cost" = "unit price" * "number of items purchased"
```

在 Excel 中，要计算第五行中项目的总计，公式可能是:

```
[Cell D5] =PRODUCT(B5:C5)
```

为了用 PEAR::Spreadsheet_Excel_Writer 实现这一点，我需要稍微修改循环数据的代码:

```
// Use this to keep track of the current row number   

$currentRow = 4;   

// Loop through the data, adding it to the sheet   

foreach ( $items as $item ) {   

    // Write each item to the sheet   

  $cart->writeRow($currentRow,0,$item);   

  // Remember Excel starts counting rows from #1!   

  $excelRow = $currentRow + 1;   

  // Create a PHP string containing the formula   

  $formula = '=PRODUCT(B' . $excelRow . ':C' . $excelRow .')';   

  // Add the formula to the row   

  $cart->writeFormula($currentRow,3,$formula);   

  $currentRow++;   

}
```

添加公式本身非常简单——我们只需使用`writeFormula()`方法。但最重要的(也是最令人困惑的)是我之前提到的——Excel 从 1 开始计算行数，而 PEAR::Spreadsheet_Excel_Writer 从 0(零)开始，因此，在创建函数时，我需要记住这一点，否则我将引用错误的单元格。这就是为什么我创建了变量`$excelRow`，它是`$currentRow`加 1。您可能认为这是作者的设计缺陷，但是请记住:在 PHP 中，像大多数编程语言一样，索引数组以零索引开始。仅仅为了更好地使用 Excel 而试图将它们向前推进一步可能会导致许多错误和维护问题。如果这真的让你很烦，就敲一些函数在两者之间进行转换。

所以，现在我的工作表在每一行显示项目总数。但是如何合计总数，以便客户可以看到将出现在他们的信用卡账单上的数字呢？对于这种情况，只需将所有的项目总计相加，并在另一个单元格中显示结果。

在 Excel 术语中，我需要使用`SUM()`函数来添加项目总数，这些项目出现在列 D:

```
[Grand Total Cell] =SUM(D5:D7)
```

为了在电子表格中体现这一点，在循环结束后，我添加了以下内容:

```
// The first row as Excel knows it - $currentRow was 4 at the start   

$startingExcelRow = 5;   

// The final row as Excel   

// (which is the same as the currentRow once the loop ends)   

$finalExcelRow = $currentRow;   

// Excel formal to sum all the item totals to get the grand total   

$gTFormula = '=SUM(D'.$startingExcelRow.':D'.$finalExcelRow.')';   

// Some more formatting for the grand total cells   

$gTFormat =& $xls->addFormat();   

$gTFormat->setFontFamily('Helvetica');   

$gTFormat->setBold();   

$gTFormat->setTop(1); // Top border   

$gTFormat->setBottom(1); // Bottom border   

// Add some text plus formatting   

$cart->write($currentRow,2,'Grand Total:',$gTFormat);   

// Add the grand total formula along with the format   

$cart->writeFormula($currentRow,3,$gTFormula,$gTFormat);
```

这又变得更加令人兴奋了，但是跟踪 Excel 的行号主要是记住给跟踪 PEAR::Spreadsheet_Excel_Writer 行号的变量加 1。还要注意，我可以对公式产生的输出应用格式。

最后，我通过将电子表格直接发送到浏览器来完成购物车收据:

```
// Send the Spreadsheet to the browser   

$xls->send("phpPetstore.xls");   

$xls->close();   

?>

Filename: phpPetstore.php

就是这样。电子表格已经可以下载了。完成的代码可在[这里](https://www.sitepoint.com/examples/pearexcel/excel_code.zip)获得。

**总结**

如您所见，PEAR::Spreadsheet_Excel_Writer 提供了构建有用的电子表格所需的几乎所有东西，包括格式和函数。而且，因为您还利用了 Excel 的功能，所以您可以自由支配大量的能力。

这个 API 很简洁，一旦你习惯了，就很容易使用。这些类的结构也很好，所以将我在这里所做的扩展到包含大量相互关联的工作表的工作簿是相对容易完成的。但是，请注意，正如您在这里所看到的，如果您不小心的话，您可能会得到一些非常长的脚本，特别是在格式必须被详细定义的情况下。如果你需要用 PEAR::Spreadsheet_Excel_Writer 做一些严肃的工作，那么尽早考虑重用的机会是值得的。您可能会发现有一种特殊的单元格格式不断出现，可以更好地放在一个类中并重用。如果您正在构建一个包含许多相似工作表的工作簿(例如，每个地区用一个工作表细分的销售数字)，编写类作为生成工作表的模板(设计模式提示)可能会节省很多精力。

总的来说，PEAR::Spreadsheet_Excel_Writer 是对您的 PHP 工具箱的一个很好的补充，您的用户正在困扰您，因为他们无法获得您用 HTML 提交给他们的数据的“视图”, Spreadsheet_Excel_Writer 提供了一个方便的替代方案来实现永无止境的新功能列表。更重要的是，它创造了一个“哇”的因素，你可以用它来给潜在的客户留下深刻的印象——特别是如果客户使用 Excel 作为他们的日常生计的话。

当你考虑到 [Jedox 的工作表服务器](http://www.jedox.com/)时，PHP 和 Excel 的主题变得更加有趣，这是一个用于*读取*(这里我们只是编写)Excel 电子表格，并从它们生成 PHP 应用程序的工具。但那是以后的事了...

```

## 分享这篇文章