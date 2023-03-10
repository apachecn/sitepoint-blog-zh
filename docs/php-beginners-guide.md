# PHP 简介:初学者指南

> 原文：<https://www.sitepoint.com/php-beginners-guide/>

**以下文章摘自 *[PHP & MySQL:新手到忍者，第 7 版](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition)* ，这是一本学习构建专业 web 应用程序所需的所有工具、原则和技术的实用指南。在本系列的第二篇教程中，您将学习 PHP 的基础知识，包括语句、变量、操作符、注释和控制结构。**

* * *

*   [用 Docker 建立你的 PHP 开发环境](https://www.sitepoint.com/php-development-environment)
*   PHP 初学者指南
*   【MySQL 入门指南
*   [在 Web 上显示来自 MySQL 的数据:简介](https://www.sitepoint.com/mysql-data-web)

* * *

[![PHP & MySQL: Novice to Ninja](img/0c5e27a112706e9dfe4e0f846efcefa0.png)](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition/)

* * *

现在，您已经启动并运行了服务器，是时候编写您的第一个 PHP 脚本了。PHP 是一种服务器端语言。这个概念可能有点难以理解，尤其是如果你只设计过使用客户端语言如 HTML、CSS 和 JavaScript 的网站。

服务器端语言类似于 JavaScript，它允许您将动态生成的内容嵌入到网页的 HTML 代码中，与单独使用 HTML 相比，您可以更好地控制浏览器窗口中显示的内容。JavaScript 和 PHP 的关键区别在于代码运行的阶段。

从 web 服务器下载网页(嵌入式程序和所有程序)后，web 浏览器会读取并执行像 JavaScript 这样的客户端语言。相比之下，像 PHP 这样的服务器端语言是在网页被发送到浏览器之前由 web 服务器运行的。客户端语言允许您控制页面在浏览器中显示后的行为，而服务器端语言允许您在页面发送到浏览器之前动态生成定制页面。

一旦 web 服务器执行了嵌入在 web 页面中的 PHP 代码，结果将代替页面中的 PHP 代码。浏览器收到页面时看到的只是标准的 HTML 代码。这就是为什么 PHP 被称为“服务器端语言”:它的工作是在服务器上完成的。

注意:在网络的早期——当这本书的第一版出版的时候！— JavaScript 是一种主要在浏览器中使用的客户端脚本语言。然后出现了像 Ajax 这样的技术，它允许 JavaScript 与服务器通信。最近，JavaScript 已经在浏览器和服务器上被用来创建数据库驱动的应用程序。虽然 JavaScript 的这些新用途提供了令人兴奋的可能性，但 PHP 仍然有很大的一席之地——正如本书所展示的那样！

PHP 代码是用 PHP 标签写的。和大多数 HTML 标签一样，PHP 有一个开始标签和结束标签:分别是`<?php`和`?>`。这些 PHP 标签中的任何内容都被视为 PHP 代码，并在*服务器*上运行。

PHP 代码必须放在扩展名为`.php`的文件中。当有人连接到服务器并要求它加载一个扩展名为`.php`的文件时，服务器就会将其作为 PHP 脚本运行。如果你把 PHP 标签放在一个带有`.html`或者除了`.php`之外的任何扩展名的文件中，web 服务器不会运行任何 PHP 代码，PHP 代码会被直接发送到浏览器——浏览器不理解 PHP 代码！

让我们来看一个 PHP 脚本。

**举例:[PHP-random number](https://github.com/spbooks/phpmysql7/tree/PHP-RandomNumber)**

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Random Number</title>
  </head>
  <body>
    <p>Generating a random number between 1 and 10:
      <?php
      echo rand(1, 10);
      ?>
    </p>
  </body>
</html> 
```

要运行这段代码，将其作为`random.php`保存在`public`目录中，并导航到`https://v.je/random.php`。

大部分都是普通的 HTML。只有`<?php`和`?>`之间的线是 PHP 代码。`<?php`标志着一个嵌入式 PHP 脚本的开始，`?>`标志着它的结束。web 服务器被要求解释这两个分隔符之间的所有内容，并在将网页发送到请求浏览器之前将其转换为常规的 HTML 代码。如果您在浏览器中右键单击并选择**查看源**(标签可能因您使用的浏览器而异)您可以看到浏览器显示如下:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Random Number</title>
  </head>
  <body>
    <p>Generating a random number between 1 and 10:
      5
    </p>
  </body>
</html> 
```

服务器上没有包含这种代码的 HTML 文件。这个 HTML 是在发送到浏览器之前在服务器上动态生成的。

尝试运行该脚本几次，注意数字是如何变化的。PHP 代码被用来生成一个随机数，但是当在浏览器中查看源代码时，PHP 代码的所有迹象都消失了。在它的位置，出现了脚本的输出，它看起来就像标准的 HTML。这个例子展示了服务器端脚本的几个优点:

*   **安全**。在上面的例子中，我们将一个由 web 服务器生成的随机数放入网页中。如果我们使用 JavaScript 插入数字，这个数字将在浏览器中生成，有人可能会修改代码来插入特定的数字。
*   **没有浏览器兼容性问题**。PHP 脚本由 web 服务器单独解释，因此不需要担心访问者的浏览器是否支持您使用的语言特性。
*   **访问服务器端资源**。服务器上的代码可以做任何事情；它不局限于浏览器的功能。产生的 HTML 可以从数据库或 Excel 文件生成，也可以是计算的结果，比如用户购物车的总数。
*   **客户端负载减少**。JavaScript 会显著延迟网页的显示(尤其是在移动设备上！)，因为浏览器必须先下载并运行脚本，然后才能显示网页。有了服务器端代码，这个负担就转移到了 web 服务器上，您可以根据应用程序的需要(并且您的钱包可以承受)来构建 web 服务器。
*   **选择**。当编写在浏览器中运行的代码时，浏览器必须理解如何运行提供给它的代码。所有现代浏览器都理解 HTML、CSS 和 JavaScript。要编写一些在浏览器中运行的代码，您必须使用这些语言中的一种。通过在生成 HTML 的服务器上运行代码，您可以选择多种语言，其中之一就是 PHP。

## 基本语法和语句

任何了解 JavaScript、C、C++、C#、Objective-C、Java、Perl 或任何其他 C 派生语言的人都会非常熟悉 PHP 语法。但是，如果您对这些语言不熟悉，或者如果您是编程新手，就没有必要担心。

一个 PHP 脚本由一系列命令组成，或者说是**语句**。每条语句都是一条指令，web 服务器必须遵循这条指令才能执行下一条指令。PHP 语句，像前面提到的语言一样，总是以分号(`;`)结束。

这是一个典型的 PHP 语句:

```
echo 'This is a <strong>test</strong>!'; 
```

这是一个`echo`语句，用来生成内容(通常是 HTML 代码)发送给浏览器。一个`echo`语句简单地获取给定的文本，并将其插入到页面的 HTML 代码中 PHP 脚本所在的位置。

在本例中，我们提供了一个要输出的文本字符串:`This is a <strong>test</strong>!`。注意，文本字符串包含 HTML 标签(`<strong>`和`</strong>`)，这是完全可以接受的。

所以，如果我们把这个语句放到一个完整的网页中，这就是结果代码。

**举例:[PHP-Echo](https://github.com/spbooks/phpmysql7/tree/PHP-Echo)**

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Test page</title>
  </head>
  <body>
    <p><?php echo 'This is a <strong>test</strong>!'; ?></p>
  </body>
</html> 
```

如果您将此文件放在 web 服务器上，然后使用 web 浏览器请求它，您的浏览器将收到以下 HTML 代码:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Test page</title>
  </head>
  <body>
    <p>This is a <strong>test</strong>!</p>
  </body>
</html> 
```

我们之前看到的`random.php`示例包含一个稍微复杂一点的`echo`语句:

```
echo rand(1, 10); 
```

您会注意到，在第一个例子中，PHP 得到了一些可以直接打印的文本，而在第二个例子中，PHP 得到了一个需要遵循的指令。PHP 试图将引号之外的任何内容作为必须遵循的指令来读取。引号内的任何内容都被视为文本——或者，用专业术语来说，被视为一个**字符串**。PHP 不会将字符串作为命令来处理。在应用程序中，它们被视为*数据*。你可以用它们做一些事情，比如把它们发送到浏览器，但是 PHP 不会区别对待不同的字符串。

因此，下面的代码将把字符串`This is a <strong>test</strong>!`直接传递给`echo`命令:

```
echo 'This is a <strong>test</strong>!'; 
```

一个字符串用一个开始引号和一个结束引号来表示。PHP 会将第一个`'`视为字符串的开头，并找到下一个`'`并将其作为字符串的结尾。

引号之外的任何内容都被视为一系列要运行的命令。以下是无效代码:

```
echo This is a <strong>test</strong>!; 
```

因为引号已经被删除，PHP 将首先尝试运行命令`This`，然后是命令`is`，接着是命令`a`，依此类推。因为这些都不是 PHP 中的有效命令，所以上面的代码会产生一条错误消息。如果你想把某个东西当作文本，记得用引号把它括起来！

相反，下面的代码将运行一个有效的命令—内置的“函数”`rand` —来生成一个随机数，然后将结果传递给 echo 命令:

```
echo rand(1, 10); 
```

**功能**是执行特定任务的特殊类型的命令。在后台，PHP 会做一些处理，然后生成一个结果。在这种情况下，`rand`函数将产生一个随机数，但是不同的函数执行不同的任务。

您可以快速识别某个东西是否是一个*函数*，因为它后面有括号。PHP 有许多内置函数，可以让您做各种事情，比如发送电子邮件和处理存储在各种类型的数据库中的信息。

PHP 不会试图运行字符串中的任何东西。下面的代码不会处理`rand`函数:

```
echo 'rand(1, 10)'; 
```

PHP 将把`'rand(1, 10)'`视为一个字符串，并将文本`rand(1, 10)`发送到浏览器，这可能不是您想要做的。理解字符串和*代码*之间的区别很重要。PHP 会将引号外的任何文本视为它应该遵循的一系列命令。引号内的任何内容都是字符串，是 PHP 将处理的*数据*。

PHP 并不试图理解字符串。它们可以包含任何顺序的任何字符。但是代码(任何不在引号内的东西)——本质上是一系列的指令——必须遵循一个计算机能够理解的严格结构。

*注意:使用带有语法突出显示的代码编辑器可以很容易地快速看出某个东西是字符串还是代码。字符串将与需要处理的代码颜色不同。*

下图显示了 Visual Studio 代码编辑器中简单的代码突出显示。

![An example of code highlighting in VS Code](img/cbc5876cdd69f6c917d0362b29d33b2f.png)

PHP 支持单引号(`'`)和双引号(`"`)来括住字符串。大多数情况下，它们是可以互换的。PHP 开发人员倾向于单引号，因为我们经常处理 HTML 代码，这些代码往往包含大量双引号。例如:

```
echo '<a href="http://www.sitepoint.com">Click here</a>'; 
```

如果这里两端都使用了双引号，我们需要通过在前面加一个`\`(称为**转义字符**)来告诉 PHP`href=`后面的引号不是字符串的结尾，并对我们实际上想作为 HTML 的一部分发送给浏览器的任何双引号做同样的处理:

```
echo "<a href=\"http://www.sitepoint.com\">Click here</a>"; 
```

出于这个原因，PHP 开发人员使用单引号，尽管单引号和双引号之间有一些差异。但是，对于我们这里的目的，它们实际上是可以互换的。

一个函数可以被认为是你程序中的一个微型程序。您可以通过使用函数名(例如前面例子中的`rand`)后跟括号来指示 PHP 运行该函数。指令`rand(1, 10)`告诉 PHP 运行名为`rand`的函数。运行一个函数通常也被称为**调用**一个函数。

PHP 中的大多数函数在被调用时都会返回值。一旦值被返回，PHP 的行为就好像您实际上只是在代码中输入了返回值。在`echo rand(1, 10);`示例中，我们的`echo`语句包含对`rand`函数的调用，该函数以文本字符串的形式返回一个随机数。然后，`echo`语句输出函数调用返回的值。

PHP 中的每个函数都可以有一个或多个**参数**，这允许你让函数以稍微不同的方式运行。`rand`函数有两个参数:最小和最大随机数。通过改变传递给函数的值，您能够改变它的工作方式。例如，如果您想要一个介于 1 和 50 之间的随机数，您可以使用以下代码:

```
echo rand(1, 50); 
```

我们用括号(`(1, 50)`)将参数括起来有两个原因。首先，它们表明`rand`是一个你想要调用的函数。其次，它们标记了参数列表的开始和结束——您希望提供的 PHP 语句——以便告诉函数您希望它做什么。对于`rand`函数，您需要提供一个最小值和一个最大值。这些值用逗号分隔。

稍后，我们会看看接受不同类型参数的函数。我们还会考虑不带任何参数的函数。这些函数仍然需要括号，即使它们之间没有输入任何内容。

## 变量、运算符和注释

### 变量

PHP 中的变量与大多数其他编程语言中的变量是相同的。对于门外汉来说，一个**变量**可以被认为是一个假想盒子的名字，任何值都可以放在这个盒子里。下面的语句创建了一个名为`$testVariable`的变量(PHP 中的所有变量名都以美元符号开头)，并为其赋值`3`:

```
$testVariable = 3; 
```

PHP 是一种**松散类型的**语言。这意味着单个变量可以包含任何类型的数据—可以是数字、文本字符串或其他类型的值—并且可以在其生命周期内存储不同类型的值。如果您在上面显示的语句后键入下面的语句，它会给现有的`$testVariable`分配一个新值。虽然`$testVariable`变量过去包含一个数字，但现在它将包含一个文本字符串:

```
$testVariable = 'Three'; 
```

一旦将数据存储在变量中，就可以使用前面的`echo`命令将数据发送到浏览器:

```
$testVariable = 3;
echo $testVariable; 
```

当这段代码运行时，数字“3”将被发送到浏览器并显示在页面上。

除了特定的字符串或数字，还可以存储函数的结果，然后在脚本中使用它:

```
$randomNumber = rand(1, 10);
echo $randomNumber; 
```

### 经营者

我们在最后两条语句中使用的等号叫做**赋值操作符**，因为它是用来给变量赋值的。(在 PHP 中，实际上有一种不同的方式来表示“equals ”,我们将在下面看到。)其他运算符可用于对值执行各种数学运算:

```
$testVariable = 1 + 1;  // assigns a value of 2
$testVariable = 1 - 1;  // assigns a value of 0
$testVariable = 2 * 2;  // assigns a value of 4
$testVariable = 2 / 2;  // assigns a value of 1 
```

从这些例子中，你大概可以看出`+`是**加法运算符**，`-`是**减法运算符**，`*`是**乘法运算符**，`/`是**除法运算符**。这些都被称为**算术运算符**，因为它们对数字执行算术运算。

*注意:上面的每一个算术行都以分号后的“注释”结束。**注释**使你能够描述你的代码在做什么。他们在代码中插入解释文本 PHP 解释器会忽略这些文本。*

*单行注释以`//`开头。单行注释可以在它自己的行上，或者像上面的例子一样，可以放在一行代码的末尾。*

*如果你想让一个评论跨越几行，以`/*`开始，以`*/`结束。PHP 解释器将忽略这两个分隔符之间的所有内容。我将在本书的其余部分使用注释来帮助解释我所展示的一些代码。*

一种将文本字符串连接在一起的操作符叫做**字符串连接操作符**:

```
$testVariable = 'Hi ' . 'there!';  // Assigns a value of 'Hi there!' 
```

变量几乎可以用在任何需要使用值的地方。考虑这一系列陈述:

```
$var1 = 'PHP';          // assigns a value of 'PHP' to $var1
$var2 = 5;              // assigns a value of 5 to $var2
$var3 = $var2 + 1;      // assigns a value of 6 to $var3
$var2 = $var1;          // assigns a value of 'PHP' to $var2
$var4 = rand(1, 12);    // assigns a value to $var4 using the rand() function
echo $var1;             // outputs 'PHP'
echo $var2;             // outputs 'PHP'
echo $var3;             // outputs '6'
echo $var4;             // outputs the random number generated above
echo $var1 . ' rules!'; // outputs 'PHP rules!'
echo '$var1 rules!';    // outputs '$var1 rules!'
echo "$var1 rules!"     // outputs 'PHP rules!' 
```

请特别注意最后两行。如果你将一个变量放在*单引号*内，它将打印变量的名称而不是内容。相反，当使用双引号时，字符串中的变量被替换为变量的内容。

在简单的情况下，将变量放在双引号内是可行的，但是对于本书的大部分内容来说，这是不可行的，因为我们不会使用这么简单的代码。所以习惯串联的做法是个好主意(如倒数第三行所示:`echo $var1 . ' rules!';`)。

## 控制结构

到目前为止，我们看到的 PHP 代码示例要么是向 web 页面输出文本字符串的单语句脚本，要么是按顺序一个接一个执行的一系列语句。如果你曾经用其他语言(如 JavaScript、Objective-C、Ruby 或 Python)编写过程序，你就会知道实用的程序很少这么简单。

就像任何其他编程语言一样，PHP 提供了使您能够影响控制流的工具。也就是说，该语言包含特殊的语句，您可以使用这些语句来偏离到目前为止在我们的示例中占主导地位的一个接一个的执行顺序。这样的语句被称为**控制结构**。不懂？放心吧！几个例子就能很好地说明这一点。

### If 语句

最基本、最常用的控制结构是`if`语句。通过一个`if`语句的程序流可以如下图所示。

![The logical flow of an if statement](img/7c25f67fb09578119a618c5d9cc43c64.png)

PHP 代码中的`if`语句是这样的:

```
if (condition) {
  ⋮ conditional code to be executed if condition is true
} 
```

这个控制结构让我们告诉 PHP 只有在满足某些条件时才执行一组语句。

例如，我们可能想要创建一个模仿掷骰子的游戏，在这个游戏中你必须掷出 6 才能赢。掷骰子可以使用我们之前使用的`rand()`函数建模，从 1 到 6 设置最小值和最大值:

```
$roll = rand(1, 6);

echo 'You rolled a ' . $roll; 
```

如果玩家掷出 6 并且赢了，你可以使用一个`if`语句来打印出一条消息。

**举例:[PHP-dice roll](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll)**

```
$roll = rand(1, 6);

echo 'You rolled a ' . $roll;

if ($roll == 6) {
  echo 'You win!';
} 
```

上述条件中使用的`==`是**等于运算符**，用于比较两个值是否相等。这和用单个`=`有很大区别，单个`=`用于*赋值*，不能用于比较。

`if`语句使用大括号(`{`和`}`)将您希望仅在满足条件时运行的代码括起来。您可以在大括号之间放置任意多行代码；只有满足条件时，代码才会运行。位于右大括号(`}`)之后的任何代码都将一直运行:

```
$roll = rand(1, 6);

echo 'You rolled a ' . $roll;

if ($roll == 6) {
  echo 'You win!'; // This line will only be printed if you roll a 6
}

echo 'Thanks for playing'; // This line will always be printed 
```

*注意:记住输入双等号(`==`)。初学 PHP 程序员的一个常见错误是用一个等号键入这样的条件:*

```
if ($roll = 6)     // Missing equals sign!
```

*该条件使用赋值运算符(`=`)而不是等于运算符(`==`)。因此，实际上* *将*`$roll`的值设置为`6`，而不是将`$roll`的值与数字`6`进行比较。哎呀！

*更糟糕的是，`if`语句会把这个赋值操作作为一个条件，它会认为这是真的，所以`if`语句内的条件代码会一直被执行，不管`$roll`的初始值碰巧是什么。*

如果您将其保存为`diceroll.php`并运行代码，您将看到生成的随机数。如果您运行它直到您赢了，您将在浏览器中看到以下内容:

```
You rolled a 6You win!Thanks for playing 
```

这不是很漂亮。但是因为 PHP 输出 HTML，所以您可以添加一些段落标记来格式化输出。

**举例: [PHP-DiceRoll 格式](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-Formatted)**

```
$roll = rand(1, 6);

echo '<p>You rolled a ' . $roll . '</p>';

if ($roll == 6) {
  echo '<p>You win!</p>';
}

echo '<p>Thanks for playing</p>'; 
```

如果您运行更新后的代码，您会看到它现在在浏览器中打印出来:

```
You rolled a 6

You win!

Thanks for playing 
```

这对于用户来说更加友好。为了使游戏本身更加用户友好，你可能想要向没有掷出 6 并且没有赢的人显示不同的信息。这可以通过一个`else`语句来完成。`else`语句必须跟在`if`之后，如果条件不满足，就会运行。

**举例:[PHP-dice roll-Else](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-Else)**

```
$roll = rand(1, 6);

echo '<p>You rolled a ' . $roll . '</p>';

if ($roll == 6) {
  echo '<p>You win!</p>';
}
else {
  echo '<p>Sorry, you didn\'t win. Better luck next time!</p>';
}

echo '<p>Thanks for playing</p>'; 
```

*注意:因为单词`didn't`包含单引号，所以它需要被“转义”。通过在单引号前加上反斜杠(`\`，它告诉 PHP 不要将`didn't`中的`'`作为字符串的结尾。*

对于 else 语句，一个(且只有一个！)的两个代码块保证运行。如果满足条件，要么运行`if`块中的代码，否则运行`else`块中的代码。

条件可能比单一的相等检查更复杂。一个`if`语句可以包含多个条件。例如，想象一下如果游戏被调整，使得`5`和`6`都是中奖号码。`if`语句可以更改如下。

**举例:[PHP-dice roll-Or](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-Or)**

```
if ($roll == 6 || $roll == 5) {
  echo '<p>You win!</p>';
}
else {
  echo '<p>Sorry, you didn\'t win. Better luck next time!</p>';
} 
```

双管运算符(`||`)的意思是“或”。如果满足任一条件，则满足上述条件。这可以理解为“如果你掷出 6 或者 5”。

然而，这可以用一种更好的方式来表达。`if`语句不限于使用等号(`==`)运算符。他们也可以使用数学上的大于号(`>`)和小于号(`<`)运算符。上面的`if`语句也可以用一个表达式来完成。

**举例:[PHP-dice roll-Greater](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-Greater)**

```
if ($roll > 4) {
  echo '<p>You win!</p>';
}
else {
  echo '<p>Sorry, you didn\'t win. Better luck next time!</p>';
} 
```

如果存储在`$roll`变量中的值大于`4`，则条件`$roll > 4`将被满足，允许我们在单个条件下将 5 和 6 作为中奖号码。如果我们想要 4、5 和 6 作为中奖号码，条件可以改为`$roll > 3`。

像“或”表达式(`||`)一样，有一个“与”表达式只有在两个条件都满足时才满足。我们可以将游戏扩展到两个骰子，并要求玩家掷出两个六来获胜。

**举例:[PHP-dice roll-two dice](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-TwoDice)**

```
$roll1 = rand(1, 6);
$roll2 = rand(1, 6);

echo '<p>You rolled a ' . $roll1 . ' and a ' . $roll2 . '</p>';

if ($roll1 == 6 && $roll2 == 6) {
  echo '<p>You win!</p>';
}
else {
  echo '<p>Sorry, you didn\'t win. Better luck next time!</p>';
}

echo '<p>Thanks for playing</p>'; 
```

只有当`$roll1 == 6`为真*和* `$roll2 == 6`时，才会满足条件`$roll1 == 6 && $roll2 == 6`。这意味着玩家必须在两个骰子上掷出 6 才能赢得游戏。如果我们把 and ( `&&`)换成 or(`||`)——也就是`if ($roll1 == 6 || $roll2 == 6)`——如果玩家在任一骰子上掷出 6，他就赢了。

必要时，我们会考虑更复杂的情况。目前，对`if … else`语句有一个大致的了解就足够了。

*注意:PHP 也允许用`or`代替`||`，用`and`代替`&&`。例如:*

```
if ($roll == 6 or $roll == 5) { … }
```

*在`or`和`||`的工作方式上有一些细微的差别，这些差别会导致意想不到的行为。一般来说，避免使用“拼写”运算符。坚持双管(`||`)和双&符号(`&&`)将有助于防止一些令人困惑的错误。*

### 环

另一种非常有用的控制结构是“循环”。**循环**允许你一遍又一遍地重复相同的代码行。两种重要的循环是`for`循环和`while`循环。让我们看看它们是如何工作的。

#### 对于循环

当你预先知道你需要运行相同的代码多少次时，使用`for`循环。下图显示了一个`for`循环的流程。

![The logical flow of a for loop](img/02c9d629d80d4f7f035d631d6f5c5aa0.png)

下面是它在代码中的样子:

```
for (declare counter; condition; increment counter) {
  ⋮ statement(s) to execute repeatedly as long as condition is true
} 
```

`declare counter`语句在循环开始时执行一次。在执行主体中的语句之前，每次通过循环都会检查`condition`语句。`increment counter`语句每次都是在语句体之后通过循环执行的。

要使用`for`循环数到十，可以使用下面的代码:

```
for ($count = 1; $count <= 10; $count++) {
  echo $count . ' ';
} 
```

这看起来很可怕，因为发生了很多事情，但让我来给你分析一下:

*   `$count = 1;`。您已经熟悉了这段代码。它创建了一个名为`$count`的变量，并将值设置为`1`。
*   `$count <= 10;`。这是条件。可以解读为“当`$count`小于等于 10 时继续循环”。
*   `$count++`。上面写着“每次计数器加 1”。和`$count = $count + 1`一模一样。
*   `echo $count . ' ';`。这将打印计数器的值，后跟一个空格。

本例中的条件使用了`<=`操作符。这类似于小于运算符(`<`)，但是如果被比较的数字*小于或等于第二个*，则计算结果为真。其他可用的运算符包括`>=`(大于或等于)和`!=`(不等于)。

`for`回路的所有三个部分组合在一起形成完整的回路。虽然乍一看，代码似乎有点难以阅读，但是将所有处理控制循环的代码放在同一个地方实际上会使您在习惯语法后更容易理解。本书中的许多例子会用到`for`循环，所以你会有很多机会练习阅读它们。

您可以更改`for`循环的每个部分，以获得不同的结果。例如，让我们看看如何在每次修改最后一部分来改变`for`循环时添加`3`。

循环的最后一部分，`$count++`说*给`$count`* 的值加 1，是`$count = $count + 1`的简写。

通过将`$count++`改为`$count = $count + 3`，可以使用 for 循环以三为单位进行计数。

**举例:[PHP-For](https://github.com/spbooks/phpmysql7/tree/PHP-For)**

```
for ($count = 1; $count <= 10; $count = $count + 3) {
  echo $count . ' ';
} 
```

这将导致以下结果:

```
1 4 7 10 
```

`for`循环可以和其他语句结合起来——比如`if`语句——在每次迭代中执行特定的任务。例如，我们可能希望掷骰子十次并打印结果，而不是每次都刷新骰子游戏页面。

**举例:[PHP-dice roll-many dice](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-ManyDice)**

```
for ($count = 1; $count <= 10; $count++) {
  $roll = rand(1, 6);
  echo '<p>You rolled a ' . $roll . '</p>';

  if ($roll == 6) {
    echo '<p>You win!</p>';
  }
  else {
    echo '<p>Sorry, you didn\'t win. Better luck next time!</p>';
  }
}

echo '<p>Thanks for playing</p>'; 
```

这让我们可以掷骰子十次，而不需要每次都刷新页面。使用循环在功能上等同于复制和粘贴代码十次，并且会产生与下面完全相同的结果:

```
$roll = rand(1, 6);
echo '<p>You rolled a ' . $roll . '</p>';

if ($roll == 6) {
  echo '<p>You win!</p>';
}
else {
  echo '<p>Sorry, you didn\'t win. Better luck next time!</p>';
}

$roll = rand(1, 6);
echo '<p>You rolled a ' . $roll . '</p>';

if ($roll == 6) {
  echo '<p>You win!</p>';
}
else {
  echo '<p>Sorry, you didn\'t win. Better luck next time!</p>';
}

$roll = rand(1, 6);
echo '<p>You rolled a ' . $roll . '</p>';

if ($roll == 6) {
  echo '<p>You win!</p>';
}
else {
  echo '<p>Sorry, you didn\'t win. Better luck next time!</p>';
}

// and so on … 
```

计算机不在乎你用哪种方法:你可以复制/粘贴或使用循环。它只会运行代码。然而，作为开发人员，您会很快意识到循环是更好的选择。如果您想更新代码以允许`5`成为中奖号码，您需要在十个不同的地方更新条件。使用循环，您可以在一个地方更改代码，这将影响循环的每次迭代。如果你发现自己在复制/粘贴代码，总会有更好的方法来实现你想做的事情。

注意:鉴于你对循环的新知识，你应该能够开始为自己编码了。你能完成这些挑战吗？

*   ***挑战 1** :打印 21 到 99 的所有奇数。*
*   ***挑战 2** :打印 9 次表，最大 12 x 9 ( `9 18 27` …等)。)而不使用乘法运算符(`*`)或`if`语句。*
*   ***Challenge 3**: print the nine times table in this format:*

    ```
    1x9 = 9
    2x9 = 18
    … etc.
    ```

    这次，你需要使用乘法运算符！

#### While 循环

另一个常用的 PHP 控制结构是`while`循环。`if … else`语句允许我们根据某些条件选择是否执行一组语句，而`while`循环允许我们使用一个条件来确定我们将重复执行一组语句多少次*次*。

下图显示了`while`循环是如何操作的。

![The logical flow of a while loop](img/ffdb2cec6a04d364b19c412113683255.png)

下面是`while`循环在代码中的样子:

```
while (condition) {
  ⋮ statement(s) to execute repeatedly as long as condition is true
} 
```

`while`循环的工作方式与`if`语句非常相似。当条件为真且语句被执行时，差异就出现了。不再继续执行右大括号(`}`)后面的语句，而是再次检查条件。如果条件仍然为真，则第二次、第三次执行语句，并且只要条件保持为真，就会继续执行。条件第一次评估为 false 时(无论是第一次检查，还是第一百次检查)，执行会立即跳转到右大括号后的`while`循环后面的语句。

您可以使用一个`while`循环来创建一个与`for`循环结果相似的计数器。

**举例:[PHP-while count](https://github.com/spbooks/phpmysql7/tree/PHP-WhileCount)**

```
$count = 1;
while ($count <= 10) {
  echo $count . ' ';
  ++$count;
} 
```

这与`for`循环的工作方式完全相同，您会注意到不同地方有许多相同的语句。我知道这段代码看起来有点吓人，但是让我一行一行地告诉你:

*   `$count = 1;`。第一行创建了一个名为`$count`的变量，并赋予它一个值`1`。
*   `while ($count <= 10)`。第二行是一个`while`循环的开始，条件是`$count`的值小于或等于(`<=`)10。
*   `{`。左大括号标志着`while`循环的条件代码块的开始。这个条件代码通常被称为循环的**体**，只要条件成立，就会被反复执行。
*   `echo $count . ' ';`。这一行只是输出`$count`的值，后跟一个空格。
*   `++$count;`。第四行将`1`加到`$count`的值上。`++$count`是`$count = $count + 1`的快捷方式。`$count++`也会在这里工作！`++`的位置可能很重要，但在这种情况下并不重要。如果`++`在变量名之前，计数器在读取值之前递增。当`$count`为零时，代码`echo ++$count;`将打印`1`，而`echo $count++;`将打印`0`。使用`++`时要小心，因为将其放在错误的位置会导致错误。
*   `}`。右大括号标志着`while`循环体的结束。

下面是这段代码执行时的情况。第一次检查条件时，`$count`的值是`1`，所以条件肯定是真的。输出`$count` ( `1`)的值，`$count`被赋予新值`2`。第二次检查时，条件仍然为真，因此输出值(`2`)并分配一个新值(`3`)。这个过程继续，输出值`3`、`4`、`5`、`6`、`7`、`8`、`9`和`10`。最后，`$count`被赋予值`11`，发现条件为假，结束循环。

下图显示了代码的最终结果。

![The net result of the while loop code](img/2df0342193eb719ef04624aadc9bd50b.png)

While 循环通常不用于这样的简单计数器；这通常是`for`循环的工作。虽然你可以用一个`while`循环来创建一个计数器，但是通常它们是用来保持代码运行，直到有事情发生。例如，我们可能希望继续掷骰子，直到得到 6。当我们写代码时，没有办法知道需要掷多少次骰子:可能要掷一次或数百次才能得到一个 6。所以你可以把骰子滚动成一个`while`环。

**举例:[PHP-dice roll-While](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-While)**

```
$roll = 0;
while ($roll != 6) {
  $roll = rand(1, 6);
  echo '<p>You rolled a ' . $roll . '</p>';

  if ($roll == 6) {
    echo '<p>You win!</p>';
  }
  else {
    echo '<p>Sorry, you didn\'t win, better luck next time!</p>';
  }
} 
```

这将继续滚动骰子，直到掷出 6。每次你运行代码，在你赢之前，它将采取不同数量的掷骰子。

while 语句使用条件`$roll != 6`。为了第一次运行`while`循环，必须将`$roll`变量设置为初始比较值。这就是`while`环线上方的`$roll = 0;`线的用途。通过最初将值设置为零，当`while`循环第一次运行时，满足条件`$roll != 6`，因为`$roll`等于零，而不是 6，循环将开始。如果在开始循环之前没有创建`$roll`变量，您将得到一个错误，因为`$roll`变量在使用之前没有被设置为任何值。

有一种叫做`do … while`的`while`循环，在这种情况下很有用。它允许您无条件地运行一些代码，然后在代码未设置的情况下再次运行它。这采用以下结构:

```
do {
 statement(s) to execute and then repeat if the condition is true
}
while (condition); 
```

对于上面的掷骰子的例子，这允许你忽略第一行。

**举例:[PHP-dice roll-Dow while](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-DoWhile)**

```
do {
  $roll = rand(1, 6);
  echo '<p>You rolled a ' . $roll . '</p>';

  if ($roll == 6) {
    echo '<p>You win!</p>';
  }
  else {
    echo '<p>Sorry, you didn\'t win, better luck next time!</p>';
  }
}
while ($roll != 6); 
```

这一次，因为条件在底部，在运行`while`语句的时候，`$roll`变量已经被赋予了一个值，所以不需要给它一个初始值零来强制循环第一次运行。

注意:PHP 不介意你如何格式化你的代码，空白被忽略。你可以这样写前面的例子:

```
do {
  $roll = rand(
  1,
  6);

  echo '<p>You rolled a ' . $roll . '</p>';

  if (
      $roll == 6
  ) 
  {
    echo '<p>You win!</p>';
  }
  else
  {
    echo '<p>Sorry, you didn\'t win, better luck next time!</p>';
  }
}
while ($roll != 6);
```

脚本将以完全相同的方式执行。不同的程序员有不同的首选样式，比如使用制表符或空格进行缩进，或者将左大括号放在语句的同一行或之后。甚至还有[编码风格指南](https://www.php-fig.org/psr/psr-12/)，指示大括号和中括号应该放在哪里以及代码应该如何格式化。但是计算机不关心大括号是在同一行还是在下一行，所以使用你觉得最舒服的样式。

## 数组

一个**数组**是一种特殊的变量，包含多个值。如果你把一个变量想象成一个包含一个值的盒子，那么一个数组就可以被想象成一个有隔间的盒子，每个隔间可以存储一个单独的值。

要在 PHP 中创建数组，使用方括号(`[`和`]`)包含要存储的值，用逗号分隔:

```
$myArray = ['one', 2, '3']; 
```

*注意:PHP 中的数组也可以使用`array`关键字来定义。下面的代码相当于上面的方括号符号:*

```
$myArray = array('one', 2, 3);
```

*方括号符号是在 PHP 5.4 中引入的，是 PHP 开发人员的首选，因为它更容易键入，并且在控制结构中的圆括号中更容易看到方括号，如`if`语句和`while`循环。*

这段代码创建了一个名为`$myArray`的数组，其中包含三个值:`'one'`、`2`和`'3'`。就像普通变量一样，数组中的每个空格可以包含任何类型的值。在这种情况下，第一个和第三个空格包含字符串，而第二个包含数字。

要访问存储在数组中的值，您需要知道它的**索引**。通常，数组使用数字作为索引来指向它们包含的值，从零开始。也就是说，数组的第一个值(或元素)的索引为 0，第二个值的索引为 1，第三个值的索引为 2，依此类推。因此，数组的第 n 个元素的索引是 n–1。一旦知道了感兴趣的值的索引，就可以通过将该索引放在数组变量名后面的方括号中来检索该值:

```
echo $myArray[0];    // outputs 'one'
echo $myArray[1];    // outputs '2'
echo $myArray[2];    // outputs '3' 
```

存储在数组中的每个值被称为一个**元素**。您可以使用方括号中的键来添加新元素，或者为现有数组元素分配新值:

```
$myArray[1] = 'two';     // assign a new value
$myArray[3] = 'four';    // create a new element 
```

您也可以像往常一样使用赋值运算符将元素添加到数组的末尾，但是变量名后面的方括号要留空:

```
$myArray[] = 'five';
echo $myArray[4];    // outputs 'five' 
```

数组元素可以像其他变量一样使用，在很多情况下，选择使用数组还是多个变量取决于程序员的偏好。但是，数组可以用来解决普通变量解决不了的问题！

还记得上一节的骰子游戏吗？如果结果显示的是英文单词而不是数字，会更方便用户使用。例如，与其说“你掷出了 3”或“你掷出了 6”，不如说“你掷出了 3”或“你掷出了 6”。

要做到这一点，我们需要某种方法将一个数字转换成该数字的英语单词。这可以通过一系列的`if`语句来实现。

**举例:[PHP-dice roll-English-If](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-English-If)**

```
$roll = rand(1, 6);

if ($roll == 1) {
  $english = 'one';
}
else if ($roll == 2) {
  $english = 'two';
}
else if ($roll == 3) {
  $english = 'three';
}
else if ($roll == 4) {
  $english = 'four';
}
else if ($roll == 5) {
  $english = 'five';
}
else if ($roll == 6) {
  $english = 'six';
}

echo '<p>You rolled a ' . $english . '</p>';

if ($roll == 6) {
  echo '<p>You win!</p>';
}
else {
  echo '<p>Sorry, you didn\'t win. Better luck next time!</p>';
}* 
```

这种解决方案是可行的，但是效率非常低，因为你需要为每个可能的掷骰子写一个`if`语句。相反，您可以使用数组来存储每个滚动值:

```
$english = [
  1 => 'one',
  2 => 'two',
  3 => 'three',
  4 => 'four',
  5 => 'five',
  6 => 'six'
]; 
```

`=>`符号(称为**双箭头操作符**)允许您在创建数组时定义键和值。这相当于以下内容:

```
$english = [];
$english[1] = 'one';
$english[2] = 'two';
$english[3] = 'three';
$english[4] = 'four';
$english[5] = 'five';
$english[6] = 'six'; 
```

尽管这两者是等价的，但是使用速记符号所需的代码输入起来要快得多，而且更容易阅读和理解。

既然已经创建了数组，就可以从中读取每个英语单词了:

```
echo $english[3];    // Prints "three"
echo $english[5];    // Prints "five" 
```

在 PHP 中，像`3`这样的数字可以用包含该值的变量替换。这对于数组键也是可能的。例如:

```
$var1 = 3;
$var2 = 5;

echo $english[$var1];    // Prints "three"
echo $english[$var2];    // Prints "five" 
```

知道了这一点，我们就可以把它拼凑起来，并通过使用`$roll`变量从数组中读取相关值来调整骰子游戏，以显示掷骰子的英文单词。

**举例:[PHP-dice roll-English-Array](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-English-Array)**

```
$english = [
  1 => 'one',
  2 => 'two',
  3 => 'three',
  4 => 'four',
  5 => 'five',
  6 => 'six'
];

$roll = rand(1, 6);

echo '<p>You rolled a ' . $english[$roll] . '</p>';

if ($roll == 6) {
  echo '<p>You win!</p>';
}
else {
  echo '<p>Sorry, you didn\'t win, better luck next time!</p>';
} 
```

如您所见，这比一长串`if`语句要干净整洁得多。这里有两大优势。

首先，如果您想要表示一个十面骰子，那么添加到数组中要比为每个数字添加一个额外的`if`语句容易得多。

其次，数组是可重用的。对于有两个骰子的版本，您可以重用`$english`数组，而不是为每个骰子滚动重复所有的`if`语句。

**举例:[PHP-dice roll-English-If-two dice](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-English-If-TwoDice)**

```
$roll1 = rand(1, 6);
$roll2 = rand(1, 6);

if ($roll1 == 1) {
  $english = 'one';
}
else if ($roll1 == 2) {
  $english = 'two';
}
else if ($roll1 == 3) {
  $english = 'three';
}
else if ($roll1 == 4) {
  $english = 'four';
}
else if ($roll1 == 5) {
  $english = 'five';
}
else if ($roll1 == 6) {
  $english = 'six';
}

if ($roll2 == 1) {
  $englishRoll2 = 'one';
}
else if ($roll2 == 2) {
  $englishRoll2 = 'two';
}
else if ($roll2 == 3) {
  $englishRoll2 = 'three';
}
else if ($roll2 == 4) {
  $englishRoll2 = 'four';
}
else if ($roll2 == 5) {
  $englishRoll2 = 'five';
}
else if ($roll2 == 6) {
  $englishRoll2 = 'six';
}

echo '<p>You rolled a ' . $english . ' and a ' . $englishRoll2 . '</p>'; 
```

相反，该阵列可用于两个卷。

**举例:[PHP-dice roll-English-Array-two dice](https://github.com/spbooks/phpmysql7/tree/PHP-DiceRoll-English-Array-TwoDice)**

```
$english = [
  1 => 'one',
  2 => 'two',
  3 => 'three',
  4 => 'four',
  5 => 'five',
  6 => 'six'
];

$roll1 = rand(1, 6);
$roll2 = rand(1, 6);

echo '<p>You rolled a ' . $english[$roll1] . ' and a ' . $english[$roll2] . '</p>'; 
```

虽然数字是数组索引最常见的选择，但还有另一种可能性。你也可以使用字符串作为索引来创建所谓的**关联数组**。之所以这么叫，是因为它*将*值与有意义的指数联系起来。在本例中，我们将日期(以字符串形式)与三个名称相关联:

```
$birthdays['Kevin'] = '1978-04-12';
$birthdays['Stephanie'] = '1980-05-16';
$birthdays['David'] = '1983-09-09'; 
```

像数字索引一样，您也可以对关联数组使用简写符号:

```
$birthdays = [
  'Kevin' => '1978-04-12',
  'Stephanie' => '1980-05-16',
  'David' => '1983-09-09'
]; 
```

现在，如果我们想知道 Kevin 的生日，我们使用姓名作为索引来查找:

```
echo 'Kevin\'s birthday is: ' . $birthdays['Kevin']; 
```

当涉及到 PHP 中的用户交互时，这种类型的数组尤其重要，我们很快就会看到。在本书中，我还将展示数组的其他用途。

*注意:因为`Kevin's`包含一个撇号(单引号), PHP 会将其视为字符串的结尾，所以必须用`\`对其进行转义，以便 PHP 将其视为字符串的一部分，而不是标记结尾。*

## 用户交互和表单

数据库驱动的网站通常需要做的不仅仅是基于数据库数据动态生成页面。你还需要提供一定程度的交互性，即使它只是一个搜索框。

因为 JavaScript 代码可以存在于浏览器中，所以它允许用户与网页进行各种即时交互。当涉及到对用户交互的支持时，服务器端脚本语言(如 PHP)的范围更有限。由于 PHP 代码只有在向服务器发出请求时才被激活，因此用户交互只能以来回的方式进行:用户向服务器发送请求，服务器用动态生成的页面进行回复。

用 PHP 创建交互性的关键是理解我们可以用来发送用户交互信息以及新网页请求的技术。事实证明，PHP 让这变得非常容易。

*注意:随着网络的发展，网络应用程序前端和后端之间的通信选项也在发展。JavaScript 允许在不重新加载页面的情况下与后端通信。例如，Vue.js 这样的 JavaScript 框架可以与 Laravel 这样的 PHP 框架进行通信。然而，就本书的目的而言，我们将专注于单独使用 PHP 能做些什么。*

### 在链接中传递变量

将信息与页面请求一起发送的最简单方法是使用 URL 查询字符串。如果您曾经注意到一个 URL 包含一个跟在文件名后面的问号，那么您已经看到了这种技术的使用。例如，如果你在谷歌上搜索“SitePoint ”,它会带你到一个搜索结果页面，其 URL 如下:

```
http://www.google.com/search?hl=en&q=SitePoint 
```

看到网址里的问号了吗？问号后面的文本包含您的搜索查询(`SitePoint`)。该信息将与对`http://www.google.com/search`的请求一起发送。

让我们自己编写一个简单的例子。创建一个名为`name.html`的常规 HTML 文件(不需要`.php`文件扩展名，因为该文件中不会有任何 PHP 代码)并插入以下链接:

```
<a href="name.php?name=Tom">Hi, I’m Tom!</a> 
```

这是一个到名为`name.php`的文件的链接，但是除了链接到该文件之外，您还将一个变量与页面请求一起传递。变量作为查询字符串的一部分传递，查询字符串是 URL 中问号后面的部分。这个变量叫做`name`，它的值是`Tom`。因此，您已经创建了一个加载`name.php`的链接，它通知包含在该文件中的 PHP 代码`name`等于`Tom`。

要真正理解这个环节的效果，需要看一下`name.php`。将它创建为一个新文件，但是这一次，请注意文件扩展名`.php`。这告诉 web 服务器它可以解释文件中的一些 PHP 代码。在这个新网页的正文中，键入以下内容。

**举例:[PHP-GET](https://github.com/spbooks/phpmysql7/tree/PHP-GET)**

```
<?php
$name = $_GET['name'];
echo 'Welcome to our website, ' . $name . '!';
?> 
```

现在，将这两个文件(`name.html`和`name.php`)放在`public`文件夹中，并在浏览器中加载第一个文件。(网址应该是`https://v.je/name.html`。)单击第一页中的链接来请求 PHP 脚本。结果页面应该显示“欢迎访问我们的网站，汤姆！”，如下图所示。

![The welcome message, seen in the browser](img/b9b50e18898f18c97ffcf3edb96ab50f.png)

让我们仔细看看使这成为可能的代码。这是最重要的一行:

```
$name = $_GET['name']; 
```

使用您从上面的“数组”一节中学到的知识，您可能能够弄清楚这一行是做什么的。它将存储在名为`$_GET`的数组的`'name'`元素中的值赋给一个名为`$name`的新变量。但是`$_GET`阵从何而来？

原来`$_GET`是 PHP 从浏览器接收请求时自动创建的变量之一。PHP 将`$_GET`创建为一个数组变量，包含 URL 查询字符串中传递的任何值。`$_GET`是一个关联数组，所以查询字符串中传递的`name`变量的值可以作为`$_GET['name']`来访问。您的`name.php`脚本将这个值赋给一个普通的 PHP 变量(`$name`)，然后使用`echo`语句将它显示为文本字符串的一部分:

```
echo 'Welcome to our website, ' . $name . '!'; 
```

使用字符串连接操作符(`.`)将`$name`变量的值插入到输出字符串中，我们在“变量、操作符和注释”一节中看到了这个操作符。

但是要小心！这段代码中潜伏着一个安全漏洞！尽管 PHP 是一门容易学习的编程语言，但事实证明，如果您不知道应该采取什么预防措施，那么使用 PHP 的网站也很容易出现安全问题。在我们继续学习这门语言之前，我想确保您能够发现并修复这个特殊的安全问题，因为这可能是当今网络上最常见的问题。

这里的安全问题源于这样一个事实，即`name.php`脚本正在生成一个包含受用户控制的内容的页面——在本例中是受`$name`变量控制的内容。虽然`$name`变量通常会从`name.html`页面上链接的 URL 查询字符串中获得值，但是恶意用户可以编辑 URL 来为`name`变量发送不同的值。

要查看这是如何工作的，再次点击`name.html`中的链接。当您看到生成的页面(带有包含姓名“Tom”的欢迎消息)时，请查看浏览器地址栏中的 URL。它应该看起来像这样:

```
https://v.je/name.php?name=Tom 
```

编辑 URL，在名称前插入一个`<b>`标记，在名称后插入一个`</b>`标记:

```
https://v.je/name.php?name=<b>Tom</b> 
```

点击`enter`来加载这个新的 URL，注意页面中的名称现在是粗体的，如下图所示。

![The name shown in bold](img/d7f8e27cef1c6efc231be8794722c874.png)

*注意:您可能会注意到，一些浏览器会自动将`<`和`>`字符转换成 URL 转义序列(分别为`%3C`和`%3E`，但是无论哪种方式，PHP 都会收到相同的值。*

看到这里发生了什么吗？用户可以在 URL 中键入任何 HTML 代码，而您的 PHP 脚本会毫无疑问地将它包含在生成的页面的代码中。如果代码像`<b>`标签一样无害，那就没有问题，但是恶意用户可能会包含复杂的 JavaScript 代码，执行一些低级操作，比如窃取用户的密码。攻击者所要做的就是在攻击者控制下的其他站点上发布修改后的链接，然后引诱您的某个用户点击它。攻击者甚至可以将链接嵌入到电子邮件中并发送给您的用户。如果您的某个用户点击了该链接，攻击者的代码就会包含在您的页面中，陷阱就会被触发！

我不想用恶意黑客攻击你的用户的言论来吓唬你，尤其是当你刚刚开始学习这门语言的时候。事实是，PHP 作为一种语言最大的弱点是很容易引入这样的安全问题。有些人可能会说，你花在学习按照专业标准编写 PHP 的大部分精力都花在了避免安全问题上。然而，你越早接触这些问题，你就越早习惯于回避它们，它们就越不会成为你未来的绊脚石。

那么，我们如何生成一个包含用户名的页面，而不被攻击者滥用呢？解决方案是将为`$name`变量提供的值视为要在页面上显示的纯文本，而不是包含在页面代码中的 HTML。这是一个微妙的区别，所以让我告诉你我的意思。

再次打开您的`name.php`文件，编辑它包含的 PHP 代码，使它看起来像下面的代码。

**举例:[PHP-GET-Sanitized](https://github.com/spbooks/phpmysql7/tree/PHP-GET-Sanitized)**

```
<?php
$name = $_GET['name'];
echo 'Welcome to our website, ' .
  htmlspecialchars($name, ENT_QUOTES, 'UTF-8') . '!';
?> 
```

这段代码中有很多内容，所以让我来给你分解一下。第一行和之前一样，将来自`$_GET`数组的`'name'`元素的值赋给`$name`。但是接下来的`echo`声明却完全不同。之前我们只是简单地将`$name`变量——裸——转储到`echo`语句中，这个版本的代码使用内置的 PHP 函数`htmlspecialchars`来执行关键的转换。

请记住，出现安全漏洞是因为在`name.php`中，`$name`变量中的 HTML 代码被直接转储到生成页面的代码中，因此可以做 HTML 代码可以做的任何事情。`htmlspecialchars`所做的是将`<`和`>`这样的“特殊 HTML 字符”转换成`&lt;`和`&gt;`这样的 HTML 字符实体，防止它们被浏览器解释为 HTML 代码。一会儿我会给你们演示一下。

首先，让我们仔细看看这个新代码。对`htmlspecialchars`函数的调用是本书中第一个使用多个参数的 PHP 函数的例子。下面是函数调用本身:

```
htmlspecialchars($name, ENT_QUOTES, 'UTF-8') 
```

第一个参数是`$name`变量(要转换的文本)。第二个参数是 PHP“常量”`ENT_QUOTES`，它告诉`htmlspecialchars`转换单引号和双引号以及其他特殊字符。

*注意:一个 PHP **常量**就像一个变量，你不能改变它的值。与变量不同，常量不以美元符号开头。PHP 附带了许多内置常量，如`ENT_QUOTES`，用于控制内置函数，如`htmlspecialchars`。*

第三个参数是字符串`'UTF-8'`，它告诉 PHP 使用什么字符编码来解释您给它的文本。

*注意:您可能已经注意到，本书中所有的 HTML 页面示例都在顶部附近包含以下 meta 元素:*

```
<meta charset="utf-8">
```

这个元素告诉接收这个页面的浏览器这个页面的 HTML 代码被编码为 UTF 8 文本。

UTF 8 是在计算机内存中将文本表示为一系列 1 和 0 的众多标准之一，称为字符编码。如果你想了解字符编码的所有知识，请查看“[网络字符编码权威指南](http://www.sitepoint.com/article/guide-web-character-encoding/)”。

再过几页，我们将到达“在表单中传递变量”一节。通过将您的页面编码为 UTF-8，您的用户可以提交包含数千个外国字符的文本，否则您的站点将无法处理这些文本。

*不幸的是，PHP 的许多内置函数，比如`htmlspecialchars`，默认假设您使用的是更简单的 ISO-8859-1(或 Latin-1)字符编码。因此，在使用这些功能时，你需要让他们知道你正在使用 UTF-8。*

如果可以的话，你还应该告诉你的文本编辑器将你的 HTML 和 PHP 文件保存为 UTF-8 编码的文本。只有当您想要在 HTML 或 PHP 代码中键入高级字符(如弯引号或破折号)或外来字符(如“é”)时，才需要这样做。本书中的代码为了安全起见，使用了 HTML 实体引用(例如，`&rsquo;`表示一个右引号)，不管怎样它都会工作。

在您的浏览器中打开`name.html`，点击现在指向您更新的`name.php`的链接。你会再次看到信息“欢迎来到我们的网站，汤姆！”正如您之前所做的，修改 URL，在名称周围包含`<b>`和`</b>`标记:

```
https://v.je/name.php?name=<b>Tom</b> 
```

这次当您点击`enter`时，您应该看到您输入的实际文本，而不是页面中的名称变成粗体，如下图所示。

![It sure is ugly, but it’s secure!](img/5ce47bbf3b7128171093b6431176c5db.png)

如果您查看页面的源代码，您可以确认`htmlspecialchars`函数完成了它的工作，将`<`和`>`字符分别转换为`&lt;`和`&gt;`实体引用。这可以防止恶意用户将不需要的代码注入到您的站点中。如果他们尝试这样做，代码会无害地显示为页面上的纯文本。

我们将在本书中广泛使用`htmlspecialchars`函数来防范这种安全漏洞。如果你现在还不能掌握如何使用它的细节，不必太担心。不久，你会发现它的使用成为第二天性。现在，让我们来看看一些更高级的方法，在我们请求时将值传递给 PHP 脚本。

在查询字符串中传递单个变量很好，但事实证明，如果您愿意，您可以传递不止一个值给*！让我们看一下上一个例子的稍微复杂一点的版本。再次打开您的`name.html`文件，用这个更复杂的查询字符串将链接改为指向`name.php`:*

```
<a href="name.php?firstname=Tom&amp;lastname=Butler">Hi,
  I’m Tom Butler!</a> 
```

这次，我们的链接传递了两个变量:`firstname`和`lastname`。查询字符串中的变量由&符号(`&`)分隔，在 HTML 中应该写成`&amp;`——是的，甚至在链接 URL 中也是如此！…尽管，如果你错误地使用了`&`，浏览器会*大部分时间*为你修复。通过用一个“与”符号将每个`name=value`对与下一个分开，您可以传递更多的变量。

和以前一样，我们可以在我们的`name.php`文件中使用两个变量值。

**举例:[PHP-GET-two vars](https://github.com/spbooks/phpmysql7/tree/PHP-GET-TwoVars)**

```
<?php
$firstName = $_GET['firstname'];
$lastName = $_GET['lastname'];
echo 'Welcome to our website, ' .
  htmlspecialchars($firstName, ENT_QUOTES, 'UTF-8') . ' ' .
  htmlspecialchars($lastName, ENT_QUOTES, 'UTF-8') . '!';
?> 
```

这个语句现在变得相当大了，但是对你来说还是有意义的。使用一系列字符串连接(`.`)，它输出“欢迎来到我们的网站”，后面是`$firstName`的值(使用`htmlspecialchars`使显示变得安全)、一个空格、`$lastName`的值(同样，用`htmlspecialchars`处理)，最后是一个感叹号。

结果如下图。

![The echoed welcome](img/da4140ba950f80b9c8fbc509fc2c664c.png)

这一切都很好，但是我们仍然没有达到真正的用户交互的目标，即用户可以输入任意信息并由 PHP 处理。继续我们的个性化欢迎消息的例子，我们想邀请用户键入他们的名字，并让它出现在结果页面中。为了让用户能够输入值，我们需要使用一个 HTML 表单。

## 在表单中传递变量

从`name.html`中删除链接代码，并用这个 HTML 代码替换它来创建表单。

**举例:[PHP-GET-Form](https://github.com/spbooks/phpmysql7/tree/PHP-GET-Form)**

```
<form action="name.php" method="get">
  <label for="firstname">First name:</label>
  <input type="text" name="firstname" id="firstname">

  <label for="lastname">Last name:</label>
  <input type="text" name="lastname" id="lastname">

  <input type="submit" value="GO">
</form> 
```

下图显示了浏览器如何显示由此代码生成的表单。

![The form as it appears in a browser](img/f21713036681d5f962620376ebf2b47c.png)

*注意:我在表单中添加了一些 CSS(在示例代码中的`form.css`中可以找到),让它看起来更漂亮一些。我使用的 CSS 非常通用，可以用来以标签-输入-换行符的格式显示任何表单。我将在任何包含表单的页面上包含这个 CSS 文件。*

然而，由于这是一本关于 PHP 和 MySQL 的书，我不会详细讲述 CSS 是如何工作的。查看 SitePoint 的**[html 5&CSS3 for the Real World](https://www.sitepoint.com/premium/books/html5-css3-for-the-real-world-2nd-edition)**获取关于使用 CSS 设计表单样式的建议。

这个表单与我们在上面的“在链接中传递变量”一节中看到的第二个链接具有完全相同的效果(查询字符串中有`firstname=Tom&amp;lastname=Butler`),除了您现在可以输入您喜欢的任何名称。当您单击 submit 按钮(标记为 **GO** )时，浏览器将加载`name.php`并将变量及其值自动添加到查询字符串中。它从`type="text"`输入的名称属性中检索变量的名称，并从用户输入文本字段的文本中获取值。

表单标签的`method`属性用于告诉浏览器如何将变量及其值与请求一起发送。值`get`(如上面的`name.html`中所使用的)导致它们通过查询字符串传递(并出现在 PHP 的`$_GET`数组中)，但是还有一个替代方法。让这些值出现在查询字符串中可能是不可取的，甚至在技术上是不可行的。如果我们在表单中包含一个`<textarea>`元素，让用户输入大量文本，会怎么样？如果一个 URL 的查询字符串包含几个段落的文本，那么这个 URL 就会长得离谱，可能会超过当今浏览器中 URL 的最大长度。另一种方法是让浏览器在幕后以不可见的方式传递信息。

再次编辑您的`name.html`文件。通过将表单方法设置为`post`来修改表单方法:

```
<form action="name.php" method="post">
  <label for="firstname">First name:</label>
  <input type="text" name="firstname" id="firstname">

  <label for="lastname">Last name:</label>
  <input type="text" name="lastname" id="lastname">

  <input type="submit" value="GO">
</form> 
```

method 属性的这个新值指示浏览器将表单变量作为页面请求的一部分以不可见的方式发送，而不是将它们嵌入到 URL 的查询字符串中。

由于我们不再将变量作为查询字符串的一部分发送，它们不再出现在 PHP 的`$_GET`数组中。相反，它们被放在另一个专门为“posted”表单变量保留的数组中:`$_POST`。因此，我们必须修改`name.php`来从这个新数组中检索值。

**举例:[PHP-POST-Form](https://github.com/spbooks/phpmysql7/tree/PHP-POST-Form)**

```
<?php
$firstname = $_POST['firstname'];
$lastname = $_POST['lastname'];
echo 'Welcome to our website, ' .
  htmlspecialchars($firstname, ENT_QUOTES, 'UTF-8') . ' ' .
  htmlspecialchars($lastname, ENT_QUOTES, 'UTF-8') . '!';
?> 
```

下图显示了这个新表单提交后的结果页面。

![The resulting page once the form is submitted](img/46d1a91e2796c9cd0b381156055ec1ca.png)

该表单在功能上与前一个相同。唯一的区别是，当用户点击 **GO** 按钮时加载的页面的 URL 没有查询字符串。一方面，这允许您在表单提交的数据中包含大值(或敏感值，如密码和信用卡号),而不会出现在查询字符串中。另一方面，如果用户将表单提交后的页面加入书签，那么这个书签就没有用了，因为它缺少提交的值。顺便说一句，这是搜索引擎使用查询字符串提交搜索词的主要原因。如果你在 Google 上给一个搜索结果页面加了书签，你可以用那个书签在以后再次执行同样的搜索，因为搜索词包含在 URL 中。

*注意:根据经验，只有当表单提交后，服务器上的**没有改变**时，你才应该使用`GET`表单——比如当你请求一个搜索结果列表时。因为搜索词在 URL 中，所以用户可以将搜索结果页面标记为书签并返回到该页面，而不必再次键入搜索词。但是如果在提交表单之后，删除了一个文件，更新了一个数据库，或者插入了一条记录，那么您应该使用`POST`。这样做的主要原因是，如果用户将页面加入书签(或者按下浏览器中的 back 按钮)，就不会再次触发表单提交，并且可能会创建重复的记录。*

*`POST`也更安全。通过`GET`发送的任何东西都会出现在 URL 中，并存储在用户的历史记录和书签中——对于密码和信用卡详细信息等敏感数据来说，这些位置非常不安全。*

这涵盖了使用表单与 PHP 进行基本用户交互的基础知识。我们将在后面的例子中研究更高级的问题和技术。

## 隐藏接缝

现在，您已经掌握了 PHP 编程语言的基本语法。您知道您可以获取任何 HTML web 页面，使用`.php`文件扩展名重命名它，并向其中注入 PHP 代码以动态生成页面内容。对于一天的工作来说还不错！

然而，在我们进一步讨论之前，我想停下来，用批判的眼光审视一下我们到目前为止讨论过的例子。假设您的目标是创建符合专业标准的数据库驱动的网站，那么我们需要清理一些难看的瑕疵。

本章其余部分的技术将帮助你提高编程技能，使其超越初学者水平，给他们一定的专业润色。在本书的其余部分，我将依靠这些技术来确保，无论这个例子有多简单，您都可以对您交付的产品质量充满信心。

### PHP 模板

在我们到目前为止看到的简单例子中，将 PHP 代码直接插入 HTML 页面是一种合理的方法。然而，随着用于生成普通页面的 PHP 代码数量的增长，维护这种 HTML 和 PHP 代码的混合会变得难以管理。

特别是如果你在一个不太懂 PHP 的 web 设计师团队中工作，将大量神秘的 PHP 代码与 HTML 混合在一起是一个灾难。设计师很容易意外修改 PHP 代码，导致他们无法修复的错误。

一个更健壮的方法是将 PHP 代码的大部分分离出来，让它驻留在自己的文件中，让 HTML 很大程度上不受 PHP 代码的污染。

做到这一点的关键是 PHP `include`语句。使用一个`include`语句，您可以将另一个文件的内容插入到 PHP 代码中该语句的位置。为了向您展示这是如何工作的，让我们重新构建我们之前看到的“数到十”`for`循环示例。

首先在您的`public`目录中创建一个新文件`count.php`。打开文件进行编辑，并键入以下代码:

```
<?php
$output = '';
for ($count = 1; $count <= 10; $count++) {
    $output .= $count . ' ';
}

include 'count.html.php'; 
```

是的，这就是这个文件的*完整*代码。它不包含任何 HTML 代码。到现在为止，您应该已经熟悉了`for`循环，但是让我指出这段代码中有趣的部分:

*   这个脚本将这些数字添加到一个名为`$output`的变量中，而不是从 1 到 10。因此，在这个脚本的开始，我们将这个变量设置为包含一个空字符串。
*   第`$output .= $count . ' ';`行将每个数字(后跟一个空格)添加到`$output`变量的末尾。您在这里看到的`.=`操作符是一种将赋值操作符和字符串连接操作符合二为一，向现有字符串变量末尾添加值的快捷方式。这一行的手写版本是`$output = $output . $count . ' ';`，但是`.=`操作符为您节省了一些输入。
*   `include`语句指示 PHP 在这个位置执行`count.html.php`文件的内容。你可以把`include`语句看作是一种*复制和粘贴*。打开`count.html.php`，复制内容并粘贴到`count.php`，覆盖`include`行，会得到相同的结果。
*   最后，您可能已经注意到文件没有以与开始的`<?php`匹配的`?>`结尾。如果你真的想放进去也可以，但没必要。如果一个 PHP 文件以 PHP 代码结尾，就没有必要指出代码的结尾；文件的结尾为您做了这件事。PHP 世界的精英们通常喜欢把它放在像这样只包含 PHP 代码的文件的末尾。

*注:在本书之外，你有时会在`include`代码中看到文件名周围的括号，好像`include`是一个类似于`date`或`htmlspecialchars`的函数，实际情况远非如此。例如，你可能会看到`include('count.html.php');`，而不是`include 'count.html.php';`。这些括号在使用时，只会使文件名表达式变得复杂，因此在本书中避免使用。另一个流行的单句俏皮话`echo`也是如此。*

由于我们的`count.php`文件的最后一行包含了`count.html.php`文件，接下来您应该创建该文件。

**举例:[PHP-Count-Template](https://github.com/spbooks/phpmysql7/tree/PHP-Count-Template)**

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Counting to Ten</title>
  </head>
  <body>
    <p>
      <?php echo $output; ?>
    </p>
  </body>
</html> 
```

这个文件几乎完全是普通的 HTML，除了输出`$output`变量值的那一行。这是由`count.php`文件创建的同一个`$output`变量。

我们在这里创建的是一个 PHP 模板:一个 HTML 页面，其中只有很小的 PHP 代码片段，可以将动态生成的值插入到一个静态的 HTML 页面中。我们没有在页面中嵌入生成这些值的复杂 PHP 代码，而是将生成这些值的代码放在一个单独的 PHP 脚本中— `count.php`。

像这样使用 PHP 模板使您能够将模板交给前端设计人员，而不用担心他们会对您的 PHP 代码做什么。它还可以让您专注于您的 PHP 代码，而不会被周围的 HTML 代码分散注意力。

我喜欢给我的 PHP 模板文件命名，这样它们就以`.html.php`结尾。然而，就您的 web 服务器而言，这些仍然是`.php`文件；`.html.php`后缀是一个有用的提醒，这些文件包含 HTML 和 PHP 代码。

要查看我们的页面现在如何工作，请在浏览器中键入`https://v.je/count.php`。您将在我们的`count.html.php`模板中看到`count.php`脚本的结果。

### 安全问题

将 HTML 和 PHP 代码分离到不同的文件中的一个问题是，有人可能会运行没有包含在相应 PHP 文件中的`.html.php`代码。这不是一个大问题，但是任何人都可以直接访问`count.html.php`。如果你在浏览器中输入`https://v.je/count.html.php`，你看到的不是从一数到十，而是一条错误信息:`Notice: Undefined variable: output in /websites/default/public/count.html.php on line 9`。

最好不要让人们以你不期望的方式运行代码。根据页面正在做什么，这可能会让他们绕过您的安全检查，查看他们不应该访问的内容。例如，考虑以下代码:

```
if ($_POST['password'] == 'secret') {
  include 'protected.html.php';
} 
```

查看这段代码，似乎您需要提交一个表单并在密码框中键入`secret`，以查看`protected.html.php`中受保护的内容。然而，如果有人可以直接导航到`protected.html.php`并看到页面的内容，这使得安全检查变得多余。通过 URL 访问所有文件还会带来其他潜在的安全问题。避免这样的安全问题很容易。实际上，您可以包含除了`public`目录之外的其他目录中的文件。

你可能很早就想知道为什么开发环境在`default`目录中创建了一个`public`目录，然后我们将所有的文件写到`public`目录中。这个安全问题就是为什么？`public`目录之外的文件都不能通过 URL 访问(通过某人在他们的 web 浏览器中键入文件名)。

可以调整`include`命令来包含来自*另一个目录*的文件。在我们的例子中，该目录将是`default`目录，其中包含我们一直在写入文件的`public`目录。

所以问题是，当包含文件在不同的目录中时，PHP 脚本如何找到它呢？最明显的方法是将包含文件的位置指定为绝对路径。下面是它在 Windows 服务器上的样子:

```
<?php include 'C:/Program Files/Apache Software Foundation/Apache2.2/protected.html.php'; ?> 
```

下面是我们正在使用的 Docker 环境的外观:

```
<?php include '/websites/default/protected.html.php'; ?> 
```

虽然这种方法可行，但并不可取，因为它将您的站点代码与您的 web 服务器配置联系在一起。因为我们正在使用 Docker，部署网站也将使用相同的 Docker 环境，所以这不是一个真正的问题。然而，理想情况下，您应该能够将基于 PHP 的网站放到任何支持 PHP 的 web 服务器上，并观察它的运行。如果您的代码引用特定于一个特定服务器的驱动器和目录，这是不切实际的。即使你*真的*有在一台服务器上工作的奢侈，如果你需要将你的网站移动到那台服务器上的另一个驱动器/目录，你会后悔的。

更好的方法是使用一个*相对*路径——即一个文件相对于当前文件的位置。当您使用`include 'count.html.php'`时，这实际上是一个*相对路径* : `count.html.php`被包含在与被执行的脚本相同的目录中。

要在之上包含一个来自*目录的文件，可以使用下面的代码:*

```
include '../count.html.php'; 
```

`../`位告诉 PHP 在当前脚本的目录上面的目录*中寻找文件。它将在`default`目录中寻找`count.html.php`而不是`public`目录。*

继续将`count.html.php`向上移动到`default`目录中，并修改`count.php`以引用新位置。

**举例:[PHP-Count-Template-Secured](https://github.com/spbooks/phpmysql7/tree/PHP-Count-Template-Secured)**

```
<?php
$output = '';
for ($count = 1; $count <= 10; $count++) {
    $output .= $count . ' ';
}

include '../count.html.php'; 
```

如果你运行上面的代码，它会工作。但是当你以这种方式包含文件时，有一个潜在的问题。相对路径是相对于运行的脚本，而不是相对于每个文件。

也就是说，如果你打开`default/count.html.php`并添加行`include 'count2.html.php';`，你会期望`count2.html.php`包含在`default`目录中。然而，这个路径是相对于一个叫做*的当前工作目录*的东西，当您运行一个 PHP 脚本时，这个目录最初被设置为存储脚本的目录。所以从`count.html.php`运行`include 'count2.html.php';`实际上会尝试从`public`目录加载`count2.html.php`！

当前工作目录在脚本开始时设置，适用于所有的`include`语句，不管它们在什么文件中。更令人困惑的是，可以使用`chdir()`函数更改当前的工作目录。

正因为如此，我们不能依赖以下几点:

```
include '../count.html.php'; 
```

它将工作，但是如果目录被改变，或者`count.php`本身是一个包含文件，它可能不会有我们期望的结果。

为了克服这一点，我们确实需要使用绝对路径。幸运的是，PHP 提供了一个名为`__DIR__`(即单词`DIR`前后的*两个*下划线)的常量，该常量将始终包含包含*当前文件*的路径。

例如，您可以使用以下代码在`public`目录中创建一个名为`dir.php`的文件:

```
echo __DIR__; 
```

这将显示`/websites/default/public`，这是包含`dir.php`的目录的完整路径。要从`public`上面的目录中读取`count.html.php`，可以将`/../`操作符和`__DIR__`常量结合起来:

```
include __DIR__ . '/../count.html.php'; 
```

这将包括文件`/websites/default/public/../count.html`。也就是说，PHP 将在`public`目录中查找，然后向上一级进入`default`并包含`count.html.php`。

这种方法将在任何服务器上工作，因为`__DIR__`将根据文件存储的位置而不同，并且它不依赖于变化的*当前工作目录*。我将使用这种方法在整本书中包含文件。

从现在开始，我们将只把文件写到`public`目录，我们实际上希望用户能够从他们的 web 浏览器直接访问这些文件。`public`目录将包含用户需要直接访问的任何 PHP 脚本，以及浏览器所需的任何图像、JavaScript 和 CSS 文件。任何只被`include`语句引用的文件都将被放在`public`目录之外，因此用户不能直接访问它们。

随着这本书的继续，我将向您介绍几种不同类型的*包含文件*。为了保持有序，明智的做法是将不同类型的包含文件存储在不同的目录中。我们将把模板文件(扩展名为`.html.php`)存储在`default`文件夹中名为`templates`的目录下。然后我们可以使用`include __DIR__ . '../templates/file.html.php';`在`include`语句中引用它们。

### 许多模板，一个控制器

使用`include`语句加载 PHP 模板文件的好处在于，您可以在一个 PHP 脚本中使用*多个* `include`语句，并让它在各种情况下显示不同的模板！

一个 PHP 脚本通过选择几个 PHP 模板中的一个来响应浏览器请求，通常被称为“控制器”。一个**控制器**包含控制哪个模板被发送到浏览器的逻辑。

让我们再来看一个本章前面的例子——提示访问者输入名字和姓氏的欢迎表单。

我们将从表单的 PHP 模板开始。为此，我们可以重用之前创建的`name.html`文件。在`default`中创建一个目录`templates`(如果你还没有的话),并在这个目录中保存一个名为`form.html.php`的`name.html`的副本。在这个文件中，您唯一需要更改的代码是表单标记的 action 属性。

**举例:[PHP-Form-Controller](https://github.com/spbooks/phpmysql7/tree/PHP-Form-Controller)**

```
<!doctype html>
<html>
  <head>
    <title>Enter your name</title>
    <link rel="stylesheet" href="form.css" />
    <meta charset="utf-8">  
  </head>
  <body>
  <form action="" method="post">
    <label for="firstname">First name:</label>
    <input type="text" name="firstname" id="firstname">

    <label for="lastname">Last name:</label>
    <input type="text" name="lastname" id="lastname">

    <input type="submit" value="GO">
  </form>
  </body>
</html> 
```

如您所见，我们将 action 属性留空。这告诉浏览器将表单提交回它接收表单的同一个 URL——在本例中，是包含该模板文件的控制器的 URL。

让我们看看这个例子的控制器。在`public`目录中创建一个包含以下代码的`index.php`文件:

```
<?php
if (!isset($_POST['firstname'])) {
    include  __DIR__ . '/../templates/form.html.php';
} else {
    $firstName = $_POST['firstname'];
    $lastName = $_POST['lastname'];

    if ($firstName == 'Tom' && $lastName == 'Butler') {
        $output = 'Welcome, oh glorious leader!';
    } else {
        $output = 'Welcome to our website, ' .
      htmlspecialchars($firstName, ENT_QUOTES, 'UTF-8') . ' ' .
      htmlspecialchars($lastName, ENT_QUOTES, 'UTF-8') . '!';
    }

    include  __DIR__ . '/../templates/welcome.html.php';
} 
```

乍一看，这段代码应该很熟悉。这很像我们之前写的`name.php`脚本。让我解释一下不同之处:

*   The controller’s first task is to decide whether the current request is a submission of the form in `form.html.php` or not. You can do this by checking if the request contains a `firstname` variable. If it does, PHP will have stored the value in `$_POST['firstname']`.

    是一个内置的 PHP 函数，它会告诉你一个特定的变量(或者数组元素)是否被赋值。如果`$_POST['firstname']`有一个值，`isset($_POST['firstname'])`将为真。如果`$_POST['firstname']`未设置，`isset($_POST['firstname'])`将为假。

    为了可读性，我喜欢把发送表单的代码放在我的控制器的前面。我们需要这个`if`语句来检查`$_POST['firstname']` *是否没有设置*。为此，我们使用 not 操作符(`!`)。通过将该运算符放在函数名之前，可以反转函数返回的值—从 true 到 false，或者从 false 到 true。

    因此，如果请求*不包含`firstname`变量，那么`!isset($_POST['firstname'])`将返回 true，并且`if`语句的主体将被执行。*

*   如果请求不是表单提交，控制器将包含`form.html.php`文件来显示表单。
*   If the request *is* a form submission, the body of the `else` statement is executed instead.

    这段代码从`$_POST`数组中取出`firstname`和`lastname`变量，然后为提交的名字生成适当的欢迎消息。

*   控制器将欢迎消息存储在名为`$output`的变量中，而不是`echo`生成欢迎消息。
*   在生成适当的欢迎消息之后，控制器包含了`welcome.html.php`模板，它将显示该欢迎消息。

剩下的就是将`welcome.html.php`文件写入`templates`目录。这是:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Form Example</title>
  </head>
  <body>
    <p>
      <?php echo $output; ?>
    </p>
  </body>
</html> 
```

就是这样！打开浏览器，指向`https://v.je/index.php`。系统会提示您输入姓名，当您提交表单时，您会看到相应的欢迎消息。在整个过程中，URL 应该保持不变。

你会注意到我让你把文件命名为`index.php`而不是`name.php`或者类似的名字。我用`index.php`的原因是因为它有特殊的含义。`index.php`被称为**目录索引**。如果你在浏览器中访问 URL 时没有指定文件名，服务器会查找名为`index.php`的文件并显示出来。试着在你的浏览器中输入`https://v.je`，你会看到索引页面。

*注意:web 服务器可以有不同的配置，并指定不同的文件作为目录索引。然而，`index.php`无需任何进一步的配置就可以在大多数 web 服务器上工作。*

在提示用户输入名称和显示欢迎消息的整个过程中保持相同的 URL 的好处之一是，用户可以在这个过程中的任何时候为页面添加书签，并获得合理的结果。无论是表单页面还是加入书签的欢迎消息，当用户返回时，表单都会再次出现。在这个例子的前一个版本中，欢迎消息有自己的 URL，返回到那个 URL 而不提交表单会产生一个不完整的欢迎消息(“欢迎来到我们的网站！”)，或者一个 PHP 错误消息，如果服务器运行时启用了错误报告。

注意:在第 11 章中，我们讨论了“会话”,我将向你展示如何在两次访问之间记住用户的名字。

## 打开数据库

在这一章中，我们已经看到了 PHP 服务器端脚本语言的作用，因为我们已经探索了所有基本的语言特性:语句、变量、操作符、注释和控制结构。我们看到的示例应用程序相当简单，但是我们仍然花时间来确保它们具有吸引人的 URL，并且它们生成的页面的 HTML 模板被控制它们的 PHP 代码整理得井井有条。

正如您可能已经开始怀疑的那样，PHP 的真正强大之处在于它的数百个(甚至数千个)内置函数，这些函数允许您访问 MySQL 数据库中的数据、发送电子邮件、动态生成图像，甚至动态创建 Adobe Acrobat PDF 文件。

在第 3 章中，我们将深入研究 PHP 内置的 MySQL 函数，然后创建一个笑话数据库。然后，在第四章，我们将看到如何将笑话数据库发布到网络上。这些任务将为本书的最终目标做好准备:用 PHP 和 MySQL 为你的网站创建一个完整的内容管理系统。

## 分享这篇文章