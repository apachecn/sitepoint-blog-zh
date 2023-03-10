# Perl 教程:什么是 Perl？

> 原文：<https://www.sitepoint.com/perl-tutorial-whats-perl/>

Perl 是实用提取和报告语言的缩写。这是一种可以在网上免费获得的语言，它被用于各种各样的事情，从编写 CGI 脚本到帮助管理员维护他们的系统。Perl 是由拉里·沃尔创建并维护的。它比 C 慢，但比普通的解释语言快。相反，它在执行时被编译，然后被解释。Perl 编译器确实存在，但它仍在开发中。关于编译后的 Perl 的更多信息，请访问 [Perl 主页](http://www.perl.com)。

正如我前面提到的，Perl 的一个好处是它是免费的。它是在 GNU 许可下分发的，源代码可以从 Perl 主页上获得。这一点，加上它的灵活性和很少的限制，有助于使 Perl 成为一种流行的语言。它还高度独立于平台，已经移植到许多不同的平台上，包括 Unix、Windows，甚至 DOS。代码修改可大可小，这取决于所使用的任何系统特定函数。

您可以在许多地方找到支持，这一事实也鼓励了 Perl 的流行。列表顶部是由许多 Perl 专家管理的[comp.lang.perl/#nlt#/Perl 新闻组](nntp://comp.lang.perl)。

##### Perl 和 CGI

这是对 Perl 和 CGI 的简单解释。如果你已经或想要建立自己的网站，你应该听说过 HTML、Javascript、Java 和 CGI。CGI 代表**公共网关接口**。CGI 不同于 Javascript、HTML 和客户端 Java。CGI 为用任何语言编写的程序提供了一种标准方法，使其在服务器端运行，并与 Web 服务器软件通信以响应对网页的请求。换句话说，CGI 是网站的一部分，它与服务器上运行的其他程序进行通信。

编写 CGI 程序或脚本的主要目的是为你的网站提供更多的交互性。许多网站是静态的，不允许太多的用户交互。其他人有留言簿，图像计数器，并允许您下订单，访问数据库和其他有用的应用程序。

这就是 CGI 的用武之地。为了实现这些类型的交互性，您必须在服务器上运行一个应用程序，例如，该应用程序知道如何处理某些类型的用户输入。正如我提到的，CGI 脚本可以用任何语言编写，但最常用的是 Perl 和 c。Perl 是编写 CGI 脚本的首选语言，因为它在字符串操作方面很有优势。现在其他编程语言如 Java 和 PHP 也越来越受欢迎，因为它们提供了不使用 CGI 的交互性。

CGI 编程无非是用一些特殊类型的输入和一些非常严格的程序输出规则来编程。当用户填写表单(允许他们提交输入的 HTML 标记的集合)时，服务器将表单输入发送到指定的 CGI 程序，该程序依次解析输入的数据并以指定的方式使用它，并返回 HTML 以及对用户请求的回答。

当您选择编写 CGI 脚本的语言时，请确保您选择的语言:

*   使文本操作变得容易
*   能够与其他软件实用程序和库连接
*   能够访问操作系统的环境变量

Perl 满足了上述所有要求，使它成为一种非常好的 CGI 编程语言。如果你想编写自己的 CGI 脚本，我建议你使用 Perl——你会发现它会让你的生活变得容易得多。如果你想获得更多关于 CGI 的细节和它是如何工作的，试试在 [NCSA 网站](http://hoohoo.ncsa.uiuc.edu/cgi/interface.html)上的 CGI 规范(注意，这是一个非常技术性的描述，针对有经验的程序员)。

##### 运行 Perl 程序

注意:这不会教给你关于 Perl 的一切，但是它会给你一个关于语言如何工作，以及如何用 Perl 编程的好主意。

运行 Perl 程序相对简单。一旦你写出了一个程序，你所要做的就是调用 Perl 解释器，以及文件名。

```
perl filename.pl
```

如果在 unix 系统上运行，可以使用 chmod +rx 函数调用使文件可执行。为了做到这一点，您必须将下面一行作为您的 Perl 程序的第一行。

```
#! /usr/local/bin/perl
```

其中“ **/usr/local/bin/perl** ”是您的 perl 解释器的路径。要在 UNIX 上找到此路径，只需键入:

```
which perl
```

在命令行上。

让我们在控制台上练习编写一个小程序。使用任何编辑器键入以下代码:

```
 #! /usr/local/bin/perl`print "Hello World!\n";`
```

现在用. pl 扩展名保存您的程序。让我们从命令行将其命名为 hello.pl，然后键入:

```
perl hello.pl
```

然后，您应该会看到以下输出:

```
Hello World!
```

您刚刚编写了第一个 Perl 程序。恭喜你！

##### 文字

Perl **文字**是按原样表示的值。该值被认为是硬编码到程序中的。Perl 支持以下两种类型的文字:

*   数字
*   用线串

**数字文字**代表数字。**字符串文字**用于识别程序中显示的名称、文本或消息。字符串通常用单引号括起来，这意味着其中出现的任何变量名(下面将详细介绍变量)都不会被插入。也支持双引号字符串文字，它们支持所谓的**变量插值**，这意味着变量名代替了它们的实数值。也支持单引号字符串文字。这些通常允许您运行命令行选项并将输出返回到您的程序。

Perl 还支持代码中的**注释**。perl 注释用#符号表示。当你看到一个#符号时，它意味着其后的所有内容都是一个注释，直到行尾。

##### 变量

所有编程语言都支持变量。变量类似于字符串文字，只是它们保存一个或多个特定的值。Perl 支持三组变量:

*   **标量**标量变量只保存一个值，它可以是一个数字文字，也可以是一个字符串文字。标量变量名总是以$符号开头。
*   一个数组变量可以保存一个列表，或者一个数组的值。这些值可以是数字或字符串。自动分配一个**键**或**下标**值，并且这些值按照输入的顺序保存。数组变量名以@符号开头。
*   **关联数组**关联数组或**哈希**也就是众所周知的，类似于数组。但是主要的区别在于，您，程序员，将键和值分配给散列，而数组自动分配一个键。哈希不会按照输入的顺序保存输入的项目。哈希变量总是以%符号开头。

如上所述，标量值用于跟踪单个信息。Perl 最常用的变量是`$_`。这个特殊的变量名称为 Perl 许多函数的默认变量**,所以要非常熟悉它。**

 **在下面的示例中，我们可以看到一个值被分配给一个标量值以及插值。

```
$var="my Scalar Variable";`print "This is $var";`
```

上面代码的输出将是:`This is my Scalar Variable`

数组包含一个变量列表，有多种不同的处理方式。您可以在初始化期间给数组赋值:

```
@array=('one','two','three');
```

上面的数组将三个值赋给数组。这些值用逗号分隔。数组的第一个下标总是 0，除非你用特殊变量`$[`改变它。这通常不是好的做法，因为您可能会让阅读您代码的人感到困惑。为了读取这个值，你需要引用数组名和你想要的下标。例如，如果我们想要值“二”,我们会说:

```
print $array[1];
```

我们说`$array`是因为我们只引用一个值，后面跟着一个开括号，我们想要的下标，然后是闭括号。这将返回数组中的第二个值。

散列可以用类似于数组的方式初始化。

```
%hash=('key1','value1','key2','value2','key3','value3');
```

主要的区别在于，您必须按照键值顺序放置这些值。所以你输入的第一个值就是第一个键；第二，关联值；第三，第二把钥匙；第四个是与该键相关联的值。从散列中检索值的过程类似于数组，除了不使用`[ ]`(方括号)而是使用`{ }`(大括号)，并且不使用下标数字，而是使用键。如果我们想要检索“值 2”，我们将使用以下内容:

```
print $hash{'key2'};
```

要知道 Perl 变量是区分大小写的，所以$me，$Me，$mE 和$ME 是四个独立的变量。类似地，如果您有一个名为$me 的标量变量、一个名为@me 的数组和一个名为%me 的散列，它们将被解释为三个独立的变量，而不是同一个变量。

##### 经营者

Perl 支持与大多数其他计算机语言相同的运算符集。运算符允许计算机语言对操作数执行操作。正如在每一种计算机语言中一样，Perl 运算符也有一定的优先级。

我们已经遇到了赋值操作符(=)。该运算符将一个变量的值赋给另一个变量:

```
$string1 = $string2;
```

上面的代码将$string2 的值赋给$string1。我们现在将考虑二元和一元算术运算符。

*   加法运算符将两个数相加。
*   减法运算符将两个数相减。
*   乘法运算符将两个数相乘。
*   除法运算符将两个数相除。
*   `op1 % op2`模运算符将返回两个整数操作数相除的余数。
*   取幂运算符将 op1 提升到 op2 的幂。
*   `++op1`预递增运算符将首先增加 op1 的值，然后对其赋值。
*   `op1++`后递增运算符将在 op1 赋值后增加其值。
*   `--op1`预减量运算符将在 op1 赋值前减少其值。
*   `op1--`后递减运算符将在 op1 赋值后减少其值。

**逻辑运算符**主要用于控制程序的流程。Perl 支持的一些逻辑运算符包括:

*   AND 运算符接受两个值，只有当两个值都为真时才返回真。
*   OR 运算符接受两个值，只有当至少一个值为真时才返回真。
*   NOT 运算符将对一个值求反。
*   等于运算符检查两个数值是否相等。
*   这个不等于运算符检查两个数值的不相等性。
*   如果 op1 小于 op2，此数值运算符将返回 true。
*   如果 op1 大于 op2，该数值运算符将返回 true。
*   `op1 <= op2`如果 op1 小于或等于 op2，该数值运算符将返回 true。
*   如果 op1 大于或等于 op2，该数值运算符将返回 true
*   `op1 <=> op2`该数值运算符如果 op1 小于 op2 将返回-1，如果两者相等将返回 0，如果 op2 大于 op1 将返回 1。
*   如果两个字符串相等，该字符串运算符将返回 true。
*   如果两个字符串不相等，该字符串运算符将返回 true。
*   `op1 lt op2`如果 op1 小于 op2，该字符串运算符将返回 true。
*   `op1 le op2`如果 op1 小于或等于 op2，该字符串运算符将返回 true。
*   `op1 gt op2`如果 op1 大于 op2，该字符串运算符将返回 true。
*   `op1 ge op2`如果 op1 大于或等于 op2，该字符串运算符将返回 true。
*   `op1 cmp op2`这个字符串操作符的功能与上面描述的数字< = >操作符的功能相同。

Perl 中还可以使用其他运算符。我们有字符串的连接操作符(。):

```
$string1 . $string2
```

上面的代码将把$string1 和$string2 连接起来，形成一个新的字符串。

我们还有重复操作符(x)，这个操作符将一个字符串重复指定的次数:

```
$string1 x 2;
```

上面的代码将$string1 重复两次。

range 运算符允许我们使用数组或图案形式的范围:

```
@array = (1..50);
```

上面的代码将为数组分配 50 个元素。

正如我们已经看到的，我们有各种各样的操作符处理标量值，但是如果我们想处理数组，我们可以做所谓的“数组拼接”。假设我们有一个有 10 个值的数组。如果我们想把值 5 和 6 赋给两个标量值，我们可以这样做:

```
($one,$two) = @array[4,5]; #remember that arrays start at subscript 0.
```

在上面的代码中，我们只是拼接了数组中的两个值。当我们进行数组拼接时，我们使用@符号，后面是数组名，后面是括号和下标，用逗号分隔。所以上面的代码不是两行，而是一行。我们需要用括号将这两个标量值括起来，以便对它们进行分组。

##### 功能

函数用于将代码组织成可以重用的小块。Perl 使用 sub 关键字声明函数，后跟{开始函数的符号和结束函数的}符号:

```
sub function1 {    

  CODE HERE    

}
```

为了调用一个 Perl 函数，建议您使用&符号后跟函数名来调用它。如果您有参数，它们也可以被传递，所以建议您用括号将它们括起来:

```
&function1;
```

在 Perl 中，函数可以作为不返回值的过程(实际上它们会返回值，但这将在后面讨论)，也可以作为返回值的函数。上面的例子是一个充当过程的函数。下面是一个函数作为函数的例子，因为它返回值(我们也向函数传递值):

```
$answer = &add(1,2);
```

您不仅限于向函数传递标量值。你也可以给它传递数组，但是在最后传递会更容易。

Perl 函数在一个数组中接收它们的值:`@_`

该数组保存传递给函数的所有参数值。所以在上面的例子中，我们传递了 2 个值，我们将以下面的方式检索它们:

```
sub function1 {    

  ($val1, $val2) = @_;    

}
```

或者我们可以这样做:

```
sub function1 {    

  $val1=$_[0];    

  $val2=$_[1];    

}
```

参数是通过值传递的，这意味着您将使用原始参数的副本，而不是实际的参数本身。如果您希望通过引用传递它们，那么您必须直接使用变量(`$_[subscript]`)。

Perl 的变量范围不同于大多数其他语言。你不需要在程序开始的时候或者在函数中声明你的变量:你只需要使用它们。变量的范围总是全局的。如果你在一个函数中使用一个变量，它对程序的其余部分是全局的。如果希望该变量只被该特定函数(以及它可能调用的任何其他函数)中的代码看到，则必须将其声明为局部变量:

```
sub function1 {    

  local($myvar)    

}
```

上面的代码将$myvar 声明为 local，所以程序的其他部分看不到它。

函数之间也可以相互嵌套，您可以创建调用自身的递归函数。

##### 控制语句

Perl 有各种控制语句，允许您控制程序如何运行，以及哪些语句控制每一步。

我们要讨论的第一个语句是 if 语句。if 语句允许您设置条件。语法是:

```
if (condition) {     

  STATEMENTS     

}
```

逻辑如下:如果条件返回 true 值，则执行块中的语句，否则不执行任何操作。if 语句超出了这个范围，允许您拥有一个 **else 语句**:

```
if (condition) {     

  STATEMENTS     

} else {     

  STATEMENTS     

}
```

只有当初始 if 语句中的条件为 false 时，才会执行上述 if 语句的 else 部分。我们还可以通过使用多个 if 语句来改进这一点，并增加更多的灵活性:

```
if (condition){     

  STATEMENTS     

} elsif (condition){     

  STATEMENTS     

} else {     

  STATEMENTS     

}
```

现在我们可以运行多个测试，并从各种选项中选择要执行的内容。

Perl 提供的另一种控制语句是循环语句，它允许我们执行一定次数的指令块。让我们来看看其中的一些陈述:

While 循环有几种用法。我们可以在循环的开始或结束时测试我们的条件。循环开始时测试条件的语法是:

```
while (condition) {     

  STATEMENTS     

}
```

将首先测试条件。如果为真，则将执行这些语句，否则将跳过它们。如果执行了这些语句，则再次测试条件，看是否应该再次执行它们。这一直持续到条件为假。

测试最后条件的语法是:

```
do {     

  STATEMENTS     

} while (condition);
```

如果您决定在循环结束时进行测试，这些语句将至少执行一次。

另一种类型的循环是**直到循环**。与 while 循环不同，until 循环用于在某些条件为假时执行某些语句。但是，就像 while 循环一样，我们可以在循环开始和结束时测试条件:

```
until (condition) {     

  STATEMENTS     

}
```

如果我们想测试循环结束时的条件:

```
do {     

  STATEMENTS     

} until (condition);
```

另一个非常强大的循环是循环的**。与 while 和 until 循环不同，在 while 和 until 循环中，我们不知道要重复语句多少次，for 循环通常用于迭代特定数量的循环。语法是:**

```
for (INITIALIZATION; CONDITION; INCREMENT/DECREMENT) {     

  STATEMENTS     

}
```

首先执行初始化表达式。它可以用来初始化循环内部的变量。条件表达式用作测试，告诉我们是否应该退出循环，递增/递减运算符用于增加初始值或我们选择的任何其他值。

我们要讨论的最后一种循环是 **foreach 循环**。因为 Perl 中最强大的功能之一是数组操作，所以我们当然必须有一个循环来允许我们遍历数组或散列。foreach 循环的语法是:

```
foreach LOOP_VAR (ARRAY) {     

  STATEMENTS     

}
```

LOOP_VAR 变量是可选的。如果没有指定，则使用默认变量$_。ARRAY 是我们的数组的名称。如果我们使用散列，我们将需要能够检索一个标量值，所以我们必须使用函数“keys”或“values”以及散列，以便能够检索一个标量值。检索到的值存储在 LOOP_VAR(或$_)中，我们可以在语句中操作它。

我们还可以在循环中使用某些关键字:

<q>**最后一个**:立即跳出循环</q>

**next** :跳过语句块的剩余部分，继续循环的下一次迭代

**重做**:重启语句块，不测试条件，也不做递增/递减操作

**goto** :跳转到指定标签

##### 文件操作

因为 Perl 是一种编程语言，它有 I/O 操作。但是这是如何应用于文件的呢？文件句柄是 Perl 程序中连接 Perl 程序和外部程序之间的 I/O 的名称。

Perl 有三个自动打开的默认文件句柄。 **STDIN** (标准输入) **STDOUT** (标准输出) **STDERR** (标准误差)。文件句柄有自己的名称空间，不会与 Perl 变量冲突。拉里·沃尔在声明文件句柄时建议使用全大写的名称。

为了打开一个文件句柄，你可以使用 open 函数:

```
open(FILEHANDLE,"filename");
```

FILEHANDLE 是我们的句柄的名称,“filename”是我们希望打开的文件的名称。如果成功，函数返回 true，否则返回 false。

要打开文件进行读取，您可以使用以下两种语法之一:

```
open(FILEHANDLE,"filename");
```

或者

```
open(FILEHANDLE,"<filename");
```

要创建文件，可以使用以下语法:

```
open(FILEHANDLE,">filename");
```

要追加到现有文件，请完成以下操作:

```
open(FILEHANDLE,">>filename");
```

从文件句柄中读取非常简单:

```
open(FILEHANDLE,"test.txt");      

while(<FILEHANDLE>){      

  print "line: $_\n";      

}
```

或者

```
@entire_file = <FILEHANDLE>; # this will read in the entire      

                             # file into the array
```

或者

```
$one_line = <FILEHANDLE>; # this will read one line into      

                          # the scalar variable.
```

要写入已打开进行写入的文件句柄，您需要执行以下操作:

```
print FILEHANDLE "This line will go into our output file";
```

要在二进制模式下打开一个文件，使用 open 函数的方式与前面相同，只是这次使用了一个额外的函数:

```
open(FILEHANDLE,"filename");`binmode(FILEHANDLE);`
```

函数的作用是:在区分文本和二进制文件的系统上，强制对给定的文件句柄进行二进制处理。

一旦我们使用完一个文件句柄，我们应该关闭它:

```
close(FILEHANDLE);
```

如果你在文件句柄关闭前重新打开它，它会自动关闭前一个文件。当你退出程序时，同样的事情也会发生。

还有一些文件测试可以在特定的文件句柄或文件名上运行。以下是一些例子:

```
File Test    Meaning       

   -r        File or directory is readable       

   -w        File or directory is writable       

   -x        File or directory is executable       

   -o        File or directory is owned by user       

   -e        File or directory exists       

   -z        File or directory exist and has zero size       

   -f        Entry is a plain file       

   -d        Entry is a directory       

   -T        File is "Text"       

   -B        File is "Binary" 
```

使用这些测试之一的一个例子可能是:

```
open(FILEHANDLE,"text.txt");      

if(-e FILEHANDLE) {      

  print "File exists.";      

}
```

或者

```
$filename = "/users/abc123/text.txt";      

if(-e $filename){      

  print "File exists.";      

}
```

如您所见，我们可以对文件句柄或我们想要的文件名执行这些文件测试。

##### 目录操作

除了允许您非常容易地操作文件之外，Perl 还允许您以非常直接的方式操作目录——就像操作文件一样。

先说一些基础的东西。为了切换到一个特定的目录，你可以使用 chdir()函数。这个函数有一个参数:您希望更改的目录的名称。

```
chdir("/home/abc123");
```

如果 Perl 能够改变到指定的目录，那么这个函数返回 true 否则，它返回 false。

您可以结合使用*运算符和菱形运算符(<>)来生成目录中的文件列表。这被称为**全球变暖**。如果我在/home/abc123/中，并且我想要一个存在的所有文件的列表，我将执行以下操作:

```
@files_found = </home/abc123/*>;
```

或者，为了只列出 Perl 文件，

```
@files_found = </home/abc123/*.pl>;
```

这将给出目录中与指定模式匹配的所有文件的名称。

您也可以使用目录句柄打开目录。要打开一个目录，可以使用 opendir()函数:

```
opendir(DIRHANDLE,"/home/abc123");
```

现在，您可以使用目录句柄通过 readdir()函数来操作文件。这也将让您访问文件名。如果在标量上下文中调用 readdir()函数，它将返回列表中的下一个文件名，如果在数组上下文中调用它，它将返回目录中所有文件的列表。

```
$filename = readdir(DIRHANDLE); #returns one filename`@filenames = readdir(DIRHANDLE); #returns a list of filenames`
```

使用完目录后，可以使用 closedir()函数关闭它:

```
closedir(DIRHANDLE);
```

##### 模式匹配

**模式匹配**包括在字符串中搜索一系列字符。当进行模式匹配时，如果发现一个模式，那么就说出现了一个**匹配**。

Perl 使用三个主要函数进行模式匹配(尽管模式匹配也可以用在其他函数中，比如 split()函数)。它们是 m//、s///和 tr///。

m//运算符是匹配运算符。这个操作符将告诉我们是否找到了匹配，使用这个操作符的语法是:

```
m/PATTERN/OPTIONS
```

模式指的是我们要搜索的字符序列，选项是可供选择的选项。使用匹配操作符时，如果使用正斜杠，可以省略这些操作符。或者，如果您不希望使用正斜杠，可以用另一个字符替代。但是请记住:您必须使用斜线来指示 Perl 使用匹配操作符。如果您确实使用了一个通常是特殊模式字符的模式定界符，那么您将不能在您指定用于匹配的模式中使用该特殊模式字符。这里有一个例子:

```
m!PATTERN!OPTIONS
```

匹配运算符有一些可以使用的选项:

```
OPTION DESCRIPTION        

  g    Match all possible patterns        

  i    Ignore case        

  m    Test string as multiple lines        

  o    Only evaluate once        

  s    Treat string as single line        

  x    Ignore white space in pattern
```

s///运算符称为替换运算符，该运算符的语法如下:

```
s/PATTERN/REPLACEMENT/OPTIONS
```

PATTERN 保存我们要搜索的模式，REPLACEMENT 保存我们要在找到我们要搜索的模式时用作替换值的值。例如:

```
s/abc/xyz/
```

在上面的例子中，我们确定我们要按这个顺序搜索“abc ”,并用“xyz”替换它。您还可以在替换中使用模式序列变量，这将在后面讨论。替换运算符也有可以使用的选项:

```
OPTION DESCRIPTION        

  g    Change all occurrences of the pattern        

  i    Ignore case in pattern        

  e    Evaluate replacement strings as expression        

  m    Treat string to be matched as multiple lines        

  o    Evaluate only once        

  s    Treat string to be matched as single line        

  x    Ignore white space in pattern
```

最后，翻译操作符为我们提供了用一组字符替换另一组字符的另一种方法。翻译运算符的语法是:

```
tr/STRING1/STRING2/OPTIONS
```

这里，STRING1 包含要替换的字符列表，STRING2 包含替换它们的字符。STRING1 中的第一个字符被 STRING2 中的第一个字符替换，第二个字符被 STRING2 中相应的数字替换，依此类推。

```
tr/abc/def/
```

在上面的例子中，abc 是 STRING1。a 被替换为 d，b 被替换为 e，c 被替换为 f。如果您想将所有字符从大写转换为小写，您可以使用:

```
tr/A-Z/a-z/;
```

如您所见，模式匹配操作支持范围操作符。同样，翻译操作符也有可以使用的选项:

```
OPTION DESCRIPTION       

  c    Translate all characters not specified        

  d    Delete all specified characters        

  s    Replace multiple identical output characters       

       with a single character
```

请记住，如果您正在使用斜杠操作符，并且您的模式还包含一个正斜杠，那么您必须使用转义符“\”对它进行转义。

现在让我们看看如何构建模式:

在进行模式匹配时，默认情况下，使用默认变量($_)的内容来查看所寻找的模式。要使用不同的变量，我们必须使用一个匹配操作符和我之前提到的三个函数之一。

=~运算符将模式绑定到运算符左侧的字符串。这意味着应该在标量变量中搜索模式。

```
$string =~ m/hello/;
```

如上例所示，我们在$string 变量中搜索“hello”字符串。如果找到一个模式，则返回 true(非零值)，否则返回 false(0 值)。

的！~ operator 将模式绑定到运算符左侧的字符串，当找不到模式时将返回 true。

现在让我们讨论模式中的特殊字符:

+字符表示“前面的一个或多个”。这意味着如果我们有一个模式:

```
m/abc+/
```

如果在 abc、abcc、abccc、abccc 等上找到匹配，它应该返回“true”。

[ ]字符允许您定义匹配组的模式。这意味着括号中包含的内容被视为一个组，我们可以从中进行选择:

```
m/a[bc]d/
```

上面的模式说，如果我们找到“abd”或“acd ”,我们将找到一个匹配。

另一个特殊字符是*字符，表示“零个或多个前述字符”。这意味着如果我们有一个模式:

```
m/ab*/
```

我们将为“a”、“ab”、“abb”、“abbb”等等找到匹配项。

那个？字符的意思是“零个或一个前面的事件”。该模式字符的工作方式与*运算符相同，只是接受的最大字符数是 1。

您还可以使用^和$操作符来锚定模式。

^运算符将一个模式锚定到一行的开头:

```
m/^The/
```

在上面的例子中，如果字符串中的一行以“the”开头(或者如果整个字符串以 the 开头，当使用“s”选项时)，我们将找到一个匹配。如果^字符被用在方括号中，并且位于开头，则表示“不在该组中的任何内容”所以要小心——当字符串用括号括起来时，它的含义会改变。

$运算符在行尾固定一个模式，因此:

```
m/end$/
```

在上面的例子中，只有当字符串中的一行以“end”结尾时(或者如果整个字符串以“end”结尾并且使用了“s”选项)，我们才能找到匹配项。

单词边界模式锚指定匹配的模式必须在单词边界上还是在单词边界内。只有当指定的模式位于单词的开头或结尾时，\b 模式锚才匹配，而如果指定的模式位于单词内部，则\B 模式锚匹配。

使用括号中的字符范围时，可以通过使用特殊的字符范围转义序列来缩短这个过程:

```
Sequence  Description                     Range Equiv.       

  \d      Any digit                       [0-9]       

  \D      Not a digit                     [^0-9]       

  \w      Any word character              [_0-9a-zA-Z]        

  \W      Anything not a word character   [^_0-0a-zA-Z]        

  \s      White space                     [ \r\n\r\f]        

  \S      Anything other than white space [^ \r\t\n\f]
```

如果您想匹配单个字符，请使用“.”(句号)字符。这将匹配任何字符(不使用' s '选项时的换行符除外)。

如果要匹配指定的出现次数，请在要指定出现次数的字符后使用{x，y}字符。这里，x 是最小值，y 是最大值。

若要指定替代项，请使用“|”字符。

您可以使用( )字符对部分字符进行分组。这些还允许您在以后重用匹配的模式。为此，请使用\number，其中 number 是( )在输入顺序中的编号。用( )分组的字符也可以有+、*、？和{x，y}作为一个组应用于它们。

这不是一个完整的模式匹配教程，而是对这个主题的介绍。关于模式匹配的更多细节，请尝试阅读 O'Reilly 的 J. Friedl 的[精通正则表达式](http://www.amazon.com/exec/obidos/ASIN/1565922573/webmasterresou05/)。

##### 报告写作

正如我们在本教程开始时所讨论的，Perl 代表实用的提取和报告语言，现在我们将讨论使用 Perl 来编写报告。

Perl 使用一种称为“格式”的书写模板来输出报告。要使用 Perl 的格式特性，您必须:

1.  定义格式
2.  传递将在格式上显示的数据
3.  调用格式

定义格式如下:

```
format FormatName =

fieldline

value_one, value_two, value_three

fieldline

value_one, value_two

.
```

```
FormatName represents the name of the format. The fieldline is the specific way the data should be formatted. The values lines represent the values that will be entered into the field line. You end the format with a single period.

```
fieldline can contain any text or fieldholders. Fieldholders hold space for data that will be placed there at a later date. A fieldholder has the format:

```
@<<<<
```

这个 fieldholder 是左对齐的，字段间距为 5。您必须计算@符号和
 <q>`@>>>>`右对齐
 `@||||`居中
 `@####.##`数值字段占位符
 `@*`多行字段占位符</q>
格式示例如下:

```
format EMPLOYEE =

===================================

@<<<<<<<<<<<<<<<<<<<<<< @<< 

$name $age

@#####.##

$salary

===================================

.
```

只能使用标量变量，不能使用数组或散列。为了调用这个格式声明，我们将使用 write 关键字:

```
write EMPLOYEE; #send to the output
```

问题是格式名通常是一个打开的文件句柄的名字，write 语句会把输出发送到这个文件句柄。因为我们希望将数据发送到 STDOUT，所以我们必须将 EMPLOYEE 与 STDOUT filehandle 相关联。但是，首先，我们必须使用 select()函数确保 STDOUT 是我们选择文件处理程序:

```
select(STDOUT);
```

然后，我们将 EMPLOYEE 与 STDOUT 关联起来，使用特殊变量$~:

```
$~ = "EMPLOYEE";
```

当我们现在执行 write()时，数据将被发送到 STDOUT。记住:如果没有将 STDOUT 设置为默认的文件句柄，可以通过将 select 的返回值赋给一个标量值，并在为这个标量变量指定了与 STDOUT 相关联的格式名之后，将 select 与这个标量变量一起使用，从而恢复到原始的文件句柄。

```

```

## 分享这篇文章**