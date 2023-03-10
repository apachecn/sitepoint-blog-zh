# ASP 语言基础

> 原文：<https://www.sitepoint.com/asp-language-basics/>

Active Server Pages (ASP)是一项成熟可靠的技术，可用于构建动态 Web 应用程序，为您提供所需的强大功能和灵活性，从基于 Web 的个人照片库到下一个电子商务项目的完整目录和购物车系统，一应俱全。ASP 的一个独特之处是它让你选择你最喜欢的脚本语言，无论是 JavaScript 还是 VBScript 然而，VBScript 是目前最受欢迎的选择。在本文中，我将带您快速了解 VBScript 语言的基本语法，包括变量、操作符和控制结构。

本文是 ASP 教学系列文章的第二篇。具体来说，本系列的目标是教你使用 ASP 创建动态网站所需的所有知识。本文从本系列的前一篇文章[开始使用 ASP](http://webmasterbase.com/article.php/371) 的地方开始。

##### 变量

下面是我在上一篇文章中帮助您创建的第一个 ASP 脚本的清单:

```
1  <html> 

2  <head> 

3  <title> My First ASP Page </title> 

4  </head> 

5  <body> 

6  <% 

7  ' Write out a simple HTML paragraph 

8  Response.Write "<p>This is a test of ASP.</p>" 

9  %> 

10 </body> 

11 </html>
```

正如我在那篇文章中承认的，这是一个相当无趣的 ASP 脚本的例子。实际上，这个脚本没有做任何普通的旧 HTML 页面做不到的事情。当然，我给出了一个稍微有趣一点的例子，它显示了当前的服务器时间，但是要真正有用，脚本需要执行某种形式的计算，或者操纵动态信息以某种有趣的方式呈现出来。

用于编写大多数 ASP 程序的语言，也是我将在整个系列中使用的语言，被称为 **VBScript** 。像大多数编程语言一样，VBScript 允许你在**变量**中存储数据。变量可以简单地认为是内存中存储数据的指定位置。VBScript 是一种被称为**的松散类型语言**，这意味着一个特定的变量可以存储任何类型的信息，可以是一个数字、一段文本、一个日期或者一些更复杂的数据块(与**严格类型语言**相反，在后者中，每个变量只能存储一种类型的信息)。然而，在你使用一个变量之前，你必须**声明**它；也就是说，您必须让 ASP 知道您想要创建一个具有特定名称的变量。

让我们看一个基本的例子来帮助你巩固这些概念。假设您正在编写一个在摄氏温度和华氏温度之间进行转换的网页。在使用摄氏温度的国家，20 摄氏度通常被认为是室温。下面的代码创建一个名为`intRoomTempC`的变量，然后给它赋值 20:

```
Dim intRoomTempC     ' Create a variable 

intRoomTempC = 20    ' Assign the variable a value of 20
```

上面的关键字`Dim`是**维度**的简称，用于告诉 VBScript 创建一个指定名称的变量(本例中为`intRoomTempC`)。你会问，为什么是“维度”？我同意，这不是最明显的选择，但基本上它指的是你要求 VBScript 做什么。创建变量时，VBScript 需要在内存中分配一些空间来存储将放入变量中的任何值，该任务的一部分是计算需要分配的空间的大小(维度)。在任何情况下，创建一个变量都很简单，只需键入`Dim`,后跟变量的名称。

上述示例的第二行为刚刚创建的变量赋值；具体来说，它将数字 20 存储在变量中。等号(`=`)被称为**赋值运算符**，因为它被用来给变量赋值。在本文的过程中，您将会遇到许多其他的操作符，它们对变量和它们存储的值做一些奇怪而奇妙的事情。

你应该总是在给变量赋值之前创建一个变量，而且你通常想在使用变量之前给它赋值。但是，尝试为不存在的变量赋值会导致 VBScript 自动创建一个具有给定名称的新变量。这被称为**隐式声明**，因为当你试图给一个不存在的变量赋值时，一个新的变量被隐式声明。由于您可以自由地对所有变量使用隐式声明，您可能想知道使用 Dim 命令手动创建每个变量有什么意义。

答案与您希望在代码中找到键入错误的难易程度有关。VBScript 提供了另一个命令`Option Explicit`，它使 ASP 不允许隐式声明，而是在您试图为不存在的变量赋值时显示一条错误消息。你为什么希望这一切发生？考虑下面的例子:

```
Dim intRoomTempC     ' Create a variable 

intRomTempC = 20     ' Assign the variable a value of 20
```

如果你有敏锐的眼光，你可能已经注意到变量名在第二行拼错了。这是一种即使是有经验的程序员也会犯的错误。启用隐式声明后，第二行将创建另一个名为`intRomTempC`的新变量，并将值存储在该变量中。现在，如果您的脚本的其余部分期望该值存储在`intRoomTempC`中，您将会遇到麻烦。在大型脚本中，将这样的问题追溯到一个小小的输入错误会非常耗时。这就是`Option Explicit`的用武之地:

```
Option Explicit      ' Disable implicit declaration 

Dim intRoomTempC     ' Create a variable 

intRomTempC = 20     ' Assign the variable a value of 20
```

这一次，ASP 将把输入错误报告为非法的隐式声明，显示一条错误消息，指出输入错误的确切行号。出于这个原因，我倾向于用`Dim`显式声明所有变量，并在所有 ASP 脚本的第一行指定`Option Explicit`。打字的时间可能会稍微长一点，但是当出现问题的时候，这样可以省去很多麻烦。

在同一行上一次创建几个变量有一个捷径。例如，下面的代码行将创建两个变量，`intRoomTempC`和`intFreezingC`:

```
Dim intRoomTempC, intFreezingC  ' Two variables in one line
```

到目前为止，您可能对我的变量命名惯例感到疑惑。上面代码片段中创建的两个变量都以`int`开头。我用这个前缀来表示这些变量将包含整数(整数)。您可以随意命名您的变量，并在其中存储您喜欢的任何类型的数据，但我更喜欢使用这个约定作为每个变量中信息类型的有用提示。这种在变量名前加上类型线索的做法被称为**匈牙利符号**，我将在本系列文章中介绍其他数据类型的前缀。

##### 使用变量

一旦创建并赋值，变量就可以在任何可能用到它所包含的值的地方使用。让我们看一个简单的脚本，它打印出存储在变量`intRoomTempC`中的房间温度。打开您的文本编辑器，键入以下内容(记住，不要键入行号)，然后将文件保存为`PrintRoomTemp.asp`:

```
1  <% Option Explicit %>  

2  <!-- PrintRoomTemp.asp  

3       A simple script that prints out a variable. -->  

4  <html>  

5  <head>  

6  <title> Room Temperature </title>  

7  </head>  

8  <body>  

9  <p>Room temperature in degrees Celsius:   

10 <%  

11 Dim intRoomTempC             ' Declare a variable  

12 intRoomTempC = 20            ' Assign it a value  

13 Response.Write intRoomTempC  ' Write out the value  

14 %>  

15 </p>  

16 </body>  

17 </html>
```

这个程序很像我们上面看到的非常简单的脚本，除了几个明显的例外:

*   ****第 1 行:**** 我们在这个文件的顶部指定了`Option Explicit`来防止变量名的键入错误，如上所述。当使用时，它应该总是出现在文件的顶部，甚至在任何 HTML 标签之前；否则，它将产生一个错误消息。
*   ****第 2-3 行:**** 这只是一个 HTML 注释标签。它实际上不做任何事情，但我只是想我会提到它，以防你不熟悉这些。
*   ****第 11-12 行:**** 现在你应该很清楚了，这两行声明了一个名为`intRoomTempC`的变量，然后给它赋值 20。
*   ****第 13 行:**** 从上一篇文章中，你应该已经习惯看到`Response.Write`用来打印出一些字符串文本(或者参见本文顶部的例子)。但是，在这种情况下，命令后面的值没有用引号括起来。因此，这一行并没有真正打印出“intRoomTempC ”,而是获取了存储在`intRoomTempC`变量中的**值**,并将其打印出来。

将文件上传到您启用 ASP 的 Web 主机，或将文件复制到您计算机上运行的启用 ASP 的 Web 服务器的文档根目录中，然后通过键入适当的 URL(例如，**http://www.myhost.com/~me/PrintRoomTemp.asp**或**http://localhost/printroumtemp . ASP**)在浏览器中打开文件。您将看到一个包含以下文本的网页:

```
Room temperature in degrees Celsius: 20
```

虽然这个脚本中的 ASP 代码实际上只打印出数字 20，但是这个例子的主要目的是演示如何使用存储在变量中的值。在本例中，我们打印出了值，但是正如您将在后面的示例中看到的，变量还可以以许多其他方式使用。

另一种打印变量中存储的值的方法是使用 ASP。通过用特殊的`<%= ... %>`分隔符将变量或其他 ASP 表达式括起来，该变量或表达式的值将被放置在页面中的该位置。再看一下我们刚才看到的例子，但这次我使用了这个很好的快捷方式:

```
1  <% Option Explicit %>  

2  <!-- PrintRoomTemp.asp  

3       A simple script that prints out a variable. -->  

4  <html>  

5  <head>  

6  <title> Room Temperature </title>  

7  </head>  

8  <body>  

9  <%  

10 Dim intRoomTempC             ' Declare a variable  

11 intRoomTempC = 20            ' Assign it a value  

12 %>  

13 <p>Room temperature in degrees Celsius: <%=intRoomTempC%></p>  

14 </body>  

15 </html>
```

请注意，创建变量并为其赋值的代码必须出现在打印变量值的特殊标记之前。虽然看起来不像，但是这个特殊的标签仍然被认为是 ASP 代码，并且将被如此处理。因此，在打印之前，必须给变量赋值是有意义的。

##### 经营者

正如我上面所说的，任何有用的程序都可能必须执行某种计算，或者以某种方式操纵信息。到目前为止，我们已经看到了如何定义信息片段并将它们存储在变量中，为将要完成的工作做准备。在这一节中，我将介绍一些 VBScript 的操作符。使用运算符，您可以获取简单的值(如数字和文本字符串)并对它们执行运算(如加、减或连接)。

让我给你看几个简单的例子，这样你就知道我的意思了:

```
Dim intA, intB, intC ' Three integer variables   

intA = 10            ' Assigns value 10 to intA   

intB = 10 + 1        ' Assigns value 11 to intB (+ is addition)   

intC = intB â€“ intA   ' Assigns value 1 to intC (- is subtraction)   

intA = intB * 3      ' Assigns value 33 to intA (* is multiplication)   

intC = intA / intB   ' Assigns value 3 to intC (/ is division)
```

注意我是如何使用符号`+`、`-`、`*`和`/`来分别执行加法、减法、乘法和除法的。这些符号是**算术运算符**。还要注意，运算符可以用于文字值(`10 + 1`)、变量(`intB â€“ intA`)或两者(`intB * 3`)。

其他不常用的算术运算符包括**模数运算符** ( `MOD`)和**取幂运算符** ( `^`):

```
intC = intB MOD 3    ' Assigns value 2 to intC (MOD is modulus op.)   

intB = intC ^ 2      ' Assigns value 4 to intB (^ is exp/power op.)
```

模数运算符给出整数除法的余数。在上面的第一个示例中，存储在`intB` (11)中的值除以 3 是 9，余数是 2，这个余数是由模数运算符计算的值。在第二行，存储在`intA` (33)中的值除以 3 得到 11，余数为 0，这是模运算的结果。

取幂或幂运算符用于计算一个数的幂。在这种情况下，我们将存储在`intC` (2)中的值提升到二次幂。换句话说，我们用 2 的平方来得到 4，这是由取幂运算符得到的值。

如果愿意，可以在一条语句中执行多个操作。请注意，算术运算遵守与科学计算器相同的优先规则:首先是乘法和除法，然后是加法和减法。当然，如果我们愿意，我们可以使用括号来改变它:

```
intA = 1 + 2 * 3     ' intA = 7   

intB = (1 + 2) * 3   ' intB = 9
```

回想一下，变量不一定要包含数字。它们也可以保存文本字符串；也可以在字符串上执行操作:

```
Dim strName   

strName = "Kevin"   

Response.Write "Hi " & strName & ", how are you today?"
```

上面的代码显示了消息`Hi Kevin, how are you today?`。请注意`&`操作符是如何用于将文本字符串连接在一起的(换句话说，就是连接字符串)。出于明显的原因，`&`被称为**字符串连接操作符**。还要注意，我们使用了表达式`"Hi " & strName & ", how are you today?"`的结果作为由`Response.Write`命令输出的值。没有说我们必须将算术计算或其他操作的结果存储在变量中；如上例所示，如果我们只需要一个地方的值，我们可以简单地在需要的地方输入表达式。最后，注意新的变量名前缀`str`，我用它来表示包含文本字符串的变量。

另一类运算符叫做**比较运算符**。顾名思义，这些运算符用于比较不同的值。算术运算符产生数字，字符串连接运算符产生文本字符串，而比较运算符产生**布尔**值(即真或假)。如前所述，一些例子可以有效地说明这一点:

```
' From above: intA = 7, intB = 9, and intC = 2   

Dim blnD, blnE, blnF    ' Three more variables   

blnD = (intA = 7)       ' blnD = True (= tests equality)   

blnE = (intA = intB)    ' blnE = False (intA doesn't equal intB)   

blnF = (intA <> intB)   ' blnF = True (<> tests inequality)   

blnD = (intA > 0)       ' blnD = True (intA is greater than 0)   

blnE = (intB < 6)       ' blnE = False (intB is not less than 6)   

blnF = (intC >= 2)      ' blnF = True (intC is greater/equal to 2)   

blnD = (intC <= 1)      ' blnD = False (intC isn't lesser/equal to 1)
```

如下图所示，比较运算符有`=`、`<>`、`>`、`<`、`>=`和`<=`。敏锐的读者可能已经注意到' = '在 VBScript 中扮演着双重角色，根据使用它的上下文，充当赋值运算符或相等比较运算符。= '和'< >'也可以用来分别检查两个变量是否包含相同或不同的字符串值。我使用前缀`bln`作为变量名，以表明它们将包含布尔值(真或假)。

您可能想知道这些比较运算符的实际用途。事实上，当要让你的程序在不同的条件下做不同的事情时，它们是必不可少的。然而，要完成这样的壮举，你还需要一个工具:控制结构。

##### 控制结构

不要让名字吓到你；控制结构并没有听起来那么复杂！到目前为止，我们看到的所有示例脚本都有一个共同点:它们以直线方式执行，从上到下，一次执行一条 ASP 语句。简单地说，控制结构让你打破直线模式，允许你的程序对不同的条件做出不同的反应，并在执行重复任务时节省你的打字时间。

让我们从最基本的控制结构开始，一个`If...Then`语句:

```
If condition Then statement
```

**如果**中的`condition`为真，**则**中的`statement`将被执行。如果没有，什么都不会发生，ASP 引擎只是移动到脚本的下一行。这里有一个简单的例子:

```
' Check if intA and intB are equal    

Dim blnABEqual    

blnABEqual = (intA = intB)    

if blnABEqual Then Response.Write "A and B are equal!"
```

这就是我上面承诺的比较运算符的实际应用！上面的第一行(在注释之后)创建了一个名为`blnABEqual`的变量。下一行检查`intA`和`intB`是否相等(也就是说，它们是否包含相同的值)。如果是，则`blnABEqual`被赋值为 true。如果没有，`blnABEqual`被设置为假。如果您打印出这个变量，它将在页面上显示“真”或“假”，这取决于`intA`和`intB`的值。在最后一行，我们有一个使用`blnABEqual`作为条件的`If...Then`语句。如您所见，如果`intA`和`intB`相等(因此`blnABEqual`为真)，程序会打印出一条消息。如果不是(并且`blnABEqual`为假)，那么什么都不会发生。

因为大多数条件只使用一次，所以更常见的是直接在`If...Then`语句中键入条件表达式，而不是使用变量。上面的例子通常是这样的:

```
' Check if intA and intB are equal    

If intA = intB Then Response.Write "A and B are equal!"
```

很漂亮，是吧？像往常一样，使用你最喜欢的方法。下面是作为`If...Then`语句一部分的比较运算符的几个例子:

```
If intA > intB Then Response.Write "A is greater than B"    

If intA < intB Then Respone.Write "A is less than B"    

If intA >= intB Then Response.Write "A is greater or equal to B"    

If intA <= intB Then Response.Write "A is lesser or equal to B"    

If intA <> intB Then Response.Write "A does not equal B"
```

如果条件为真，您可以指定应该执行的一系列语句，而不是单个语句。您可以通过在新的一行上开始语句列表并以`End If`结束列表来实现这一点。这里有一个例子:

```
If blnUserIsKevin Then    

  Response.Write "Hi, Kevin!"    

  Response.Write "How are you today?"    

  ' ... more statements here ...    

End If
```

在我继续之前，先说明一下编码风格。你可能注意到上面例子中的一些行前面有空格。这种被称为**缩进**的编码风格旨在展示代码的结构。也就是说，通过将条件为真时出现的语句缩进两个空格，可以清楚地表明它们在`If...Then`语句中。代码本来可以不用缩进(每一行从左边开始)来编写，它也可以工作得很好，但是结构看起来并不明显。当您进入编写复杂脚本的阶段时，代码的某些段落缩进五级甚至十级是很常见的(例如，从`If...Then`语句到`If...Then`语句)。通过现在养成这个好习惯，你可以确保你的代码在现在和将来都易于阅读。

回到`If...Else`语句，如果你想在条件为假而不是为真时做些什么，有一个方便的快捷方式可以使用。下面是不使用快捷键时代码的样子:

```
' Print message when blnCondition is false    

If blnCondition = False Then    

  Response.Write "blnCondition is false!"    

End If
```

`=`操作符(和`<>`操作符)可以用于布尔值，也可以用于数字类型和文本字符串。因此，当`blnCondition`为假时，上述`If...Then`语句中的条件将为真，当`blnCondition`为真时，条件将为假。我提到的捷径涉及到一种叫做否定运算符的东西。通过将该操作符(`NOT`)放在变量名或某个表达式之前，该变量或表达式的值将从 true 变为 false(反之亦然)。因此，上面的例子可以更简单地写成如下:

```
' Print message when blnCondition is false    

If NOT blnCondition Then    

  Response.Write "blnCondition is false!"    

End If
```

如果`blnCondition`为真，`NOT`操作符将导致条件评估为假，因此不会打印消息。如果`blnCondition`为假，则`NOT`将表达式的值转换为真，并打印消息。值得注意的是，`NOT`操作符实际上并不改变存储在`blnCondition`中的值；它只是为了`If...Then`语句的目的改变了条件的值。

与`If...Then`语句相关的另一个控制结构是`If...Else`语句:

```
If condition Then    

  ' statements to be executed if condition is true    

Else    

  ' statements to be executed if condition is false    

End If
```

在这种情况下，将执行一组或另一组语句。条件决定了。然而，通常情况下，您会有不止两组语句需要选择。通过使用中的另一个`If...Else`语句作为第二个缩进的语句块，您可以测试多个条件，并根据哪个条件为真来执行操作:

```
If condition1 Then     

  ' do this if condition1 is true    

Else    

  If condition2 Then    

    ' do this if condition2 is true    

  Else    

    If condition3 Then    

      ' do this if condition3 is true    

    Else    

      ' do this only if none of the conditions is true    

    End If    

  End If    

End If
```

迷茫？如果你仔细阅读了以上内容，你最终应该能够拼凑出三个相关的陈述，但是你可能会很高兴知道有一个更简单的方法。使用`ElseIf`来指定你的附加条件，你可以去掉嵌套的`If...Else`语句。下面是使用`ElseIf`修改的前一个例子:

```
If condition1 Then     

  ' do this if condition1 is true    

ElseIf condition2 Then    

  ' do this if condition2 is true    

ElseIf condition3 Then    

  ' do this if condition3 is true    

Else    

  ' do this only if none of the conditions is true    

End If
```

##### 环

我将在本文中讨论的其余控制结构必须使您的脚本多次执行相同的操作，而不必一次又一次地为该操作键入命令。这些结构通常被称为**回路**。

第一种循环称为`Do-While`循环。它的名字符合它的功能:当条件保持为真时**反复做**某事**。下面是一个`Do-While`循环的样子:**

```
Do While condition     

  ' a block of commands (the loop body)     

Loop
```

图 1 中的流程图最好地说明了`Do-While`循环的操作。

![Figure 1: A standard Do-While loop](img/66a1334414f0d82ac8cb434ac2a92b31.png)

在大多数情况下，`Do-While`循环的条件是开始为真，但最终在命令块执行一定次数后变为假。下面是一个简单的脚本，它使用了一个`Do-While`循环:

```
1  <% Option Explicit %>     

2  <!-- Countdown.asp     

3       A simple ASP script that counts down from 10\. -->     

4  <html>     

5  <head>     

6  <title> Countdown to Liftoff </title>     

7  </head>     

8  <body>     

10 <%     

11 Dim intCount     

12 intCount = 10     

13     

14 Response.Write "<p>Countdown to liftoff... T Minus "     

15     

16 Do While intCount > 0     

17   Response.Write intCount & "!<br>"     

18   intCount = intCount - 1     

19 Loop     

20     

21 Response.Write "Liftoff! We have liftoff!</p>"     

22 %>     

23 </body>     

24 </html>
```

以下是该计划的新内容:

*   ****第 11-12 行:**** 我们声明一个名为`intCount`的变量，初始值为 10。
*   ****第 16 行:**** 一个只要`intCount`大于 0 就会持续的`Do-While`循环。
*   ****第 18 行:**** 从`intCount`中存储的值减 1

所以当程序第一次到达`Do-While`循环时，`intCount`是 10，所以循环体被执行，打印出“10！”然后从存储在`intCount`中的值中减去 1。然后再次检查条件。9 仍然大于零，所以循环体再次执行(“9！”).这一直持续到第 10 次执行循环体(“1！”)，当从`intCount`中减去 1 使其值为零。现在，当条件被检查时，它是假的；因此，循环停止，执行继续跟随循环。

当您在浏览器中查看脚本的结果时，应该会看到以下内容:

```
Countdown to liftoff... T Minus 10!     

9!     

8!     

7!     

6!     

5!     

4!     

3!     

2!     

1!     

Liftoff! We have liftoff!
```

```
Do-While loops are very handy, but there are two limiting cases you should be aware of:
	*   如果条件一开始就是假的，循环体就不会被执行。*   如果条件永远为真，脚本将一遍又一遍地执行循环，最终将超时，导致显示错误消息。
虽然第一种情况有它的用途，但第二种情况是要小心的。永不结束的循环被称为无限循环。无限循环可能在最意想不到的情况下发生，这是一个令人惊讶的常见错误，即使是有经验的程序员有时也会犯。如果你的一个脚本看起来卡住了，让你的浏览器挂起，很有可能它陷入了一个无限循环。如果您等待的时间足够长，最终该脚本将达到服务器上的脚本时间限制，并出现一条错误消息，说明该脚本超过了最大允许执行时间。要查看这是什么样子，只需在第 16 行更改`Do-While`循环的条件:

```
16 Do While True
```

不用担心；不会伤害你的电脑。不过，在脚本超时之前，这可能会减慢速度。

##### 对于循环

我们将在本文中看到的最终控制结构类似于一个`Do-While`循环，被称为`For`循环。`For`循环为最常见的`Do-While`循环应用提供了一个很好的快捷方式。下面是语法的样子:

```
For counter = start To end [Step step]      

  ' loop body      

Next
```

在上面的例子中，**计数器**是一个变量的名称，它将存储一个值，该值将在每次循环中递增或递减计数，**开始**是计数器变量在第一次循环中应该开始的值，**结束**是计数器变量在最后一次循环中应该具有的值。可选的**步骤**参数是每次循环后计数器变量应该改变的量。如果没有指定，该值将在循环中每次增加 1。`For`回路的操作如图 2 所示。

![Figure 2: A For loop](img/f9cb71e5afeeacb8dde9ced067c95be5.png)

为了在实践中演示一个`For`循环，我修改了上面的倒计时脚本，用一个`For`循环代替了一个`Do-While`循环。请注意，在这种情况下，我们希望计数器变量从 10 递减计数到 1，因此我们指定步长值为-1。

```
1  <% Option Explicit %>      

2  <!-- Countdown.asp      

3       A simple ASP script that counts down from 10\. -->      

4  <html>      

5  <head>      

6  <title> Countdown to Liftoff </title>      

7  </head>      

8  <body>      

9  <%      

10 Dim intCount      

11      

12 Response.Write "<p>Countdown to liftoff... T Minus "      

13      

14 For intCount = 10 To 1 Step -1      

15   Response.Write intCount & "!<br>"      

16 Next      

17      

18 Response.Write "Liftoff! We have liftoff!</p>"      

19 %>      

20 </body>      

21 </html>
```

这个脚本中的代码与带有`Do-While`循环的原始版本非常相似；我们刚刚抄了几个近路！变量`intCount`的创建保持不变(第 10 行)，但是我们不再需要给它一个初始值，因为`For`循环会处理这个问题。从`intCount`变量值中减去 1 的循环体命令也不再需要。因此，`For`循环让你做的是将所有与初始化、检查和更新计数器变量相关的命令(在本例中是`intCount`)合并到一行中，使代码更容易阅读。

尽管任何`For`循环都可以扩展成一个`Do-While`循环，并在循环体的前面和结尾使用适当的语句，但是通常的做法(通常更整洁)是将任何使用计数器变量的循环表示为一个`For`循环。

##### 总结和进一步阅读

学习编程语言的基本语法很少是非常有趣的事情，所以不幸的是，这样的材料并不能成为最令人兴奋的文章。然而，在本文中，您已经了解了 VBScript 编程语言最重要的特性，这些特性通常用于在 ASP 中编写动态网站。我们研究了变量如何让您存储数据以用于计算，哪些运算符可用于将数据放在一起并获得结果，以及哪些控制结构让您可以编写程序，而不是直线运行。

由于篇幅限制，该语言的一些不太基本的特性(例如 s `elect-case`控制结构和逻辑操作符)被省略了，但是这些将在本系列的后续文章中适当地介绍。与此同时，如果你正在寻找 VBScript 语言的完整指南，请访问微软脚本技术网站。

随着 VBScript 平凡细节的消失，您现在应该准备好进入用 ASP 构建动态网站的世界了。这正是我将在下个月的文章中讨论的内容！30 分钟后这里见！

## 分享这篇文章

```