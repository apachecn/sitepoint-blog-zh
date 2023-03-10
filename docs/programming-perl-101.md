# 编程 Perl 101

> 原文：<https://www.sitepoint.com/programming-perl-101/>

****1。)背景信息****

如果你运行一个网站，你可能在某个时候听说过 Perl 或者 CGI。无论您只是听说过它，还是自己安装了一些脚本，或者只是看了一眼代码就脸色苍白，本教程都是为您准备的。

Perl 是一种编程语言。许多人认为 CGI 是一种语言，比如“我要学习 CGI”，但这是错误的。把 CGI 想象成使用 Perl 的方法。您可以使用其他编程语言编写 CGI 脚本，但是 Perl 是最常用的，因为它相对简单和强大。

即使更高效的编程语言开始夺走 Perl 的受欢迎程度，Perl 编程仍然非常受欢迎，学习它并不是浪费时间。例如，PHP 在大多数方面与 Perl 相似。所使用的语法是相似的，许多基础知识也有些相同。如果你懂 Perl，你就已经在学习 PHP 和其他一些语言上有了很好的开端。

尽管 Perl 可能不如 PHP 或 ASP 等其他编程语言高效，但用 Perl 编写的免费软件和共享软件脚本比用任何其他语言编写的都要多得多。换句话说，Perl 不会很快消失。

**2。)几个小规则**

现在您对 Perl 有了一些了解，让我们开始一些基本的命令。

像任何编程语言一样，Perl 也有规则。你需要在特定的地方使用特定的符号来告诉脚本一些东西。Perl 中最基本的规则之一是每一行都必须以分号结束:“`;`”。和每条规则一样，也有一些例外，我们将在后面讨论，但现在只需意识到，您编写的几乎每一行 Perl 代码都将以分号结束。如果您想知道，分号告诉 Perl 它已经到达了一个命令的末尾。

让我们写一个简短的脚本向您介绍一些基础知识。

在任何基于 Perl 的脚本中，像这样的一行出现在最顶端:

`#!/usr/local/bin/perl`

这被戏称为“shebang”行，这是 Perl 语言的另一个需求。shebang 行将浏览器指向 web 主机服务器上 Perl 解释器的路径。把这一行想象成告诉你的浏览器，“在这里你可以找到一个翻译器把我的代码转换成一个函数脚本。”

几乎每个 web 主机都安装了 Perl，尽管它并不总是在上面指定的位置。如果上面的代码行不通，请联系您的系统管理员，向他们询问 Perl 的路径。

注意，您需要在您编写的每个文件中指向 Perl 解释器。(注意:以结尾的文件。cgi，。pl，还有。perl 都可以认为是 Perl 文件。)

从现在开始，本教程假设您对 HTML 和表单域有基本的了解。如果你不知道，那就去 http://www.HtmlGoodies.com 大学阅读那里提供的一些教程。

让我们创建我们的第一个脚本！

**3。)出发**

我们将要创建的脚本将获取您的用户在标准反馈表单中提交的信息，然后加载一个页面，显示他刚刚输入的内容。不是非常有用，但是是一个让我们开始的很好的例子。

在这个例子中，我们将让某人输入他们的名字、城市和喜欢的食物(毕竟，这是我们所有人的基本重要信息，对吗？).

首先，我们用下面的形式创建 HTML 页面:
 `<html>
<head>
<title>Submit Your Information</title>
</head>`

 `# 提交您的信息

<P>
<form action = " display . CGI " method = " post ">
First Name:<input type = " text " size = " 30 " Name = " First "><br>
City:<input type = " text " size = " 30 " Name = " City "><br>
喜欢的美食:< input>
<输入类型=复位名称=复位值= "复位">
</表格>`

你看到那些表单字段的名称了吗？这些是分配给用户将要输入信息的每个表单域的标识值。在这种情况下，它们是“第一”、“城市”和“食物”可以将表单域的名称想象成保存用户输入数据的容器。

其工作方式是，当用户在适当的表单字段中输入姓名时，姓名标签会将自己“附加”到输入的数据上，以便在以后使用时识别。如果用户将单词“Marvin”输入到“first”字段中，单词“Marvin”将被分配“first”标签。您很快就会看到，我们可以使用 Perl 显示任何被赋予“第一个”标签的文本。开始有意义了吗？

现在，您会注意到，我们将表单操作设置为“display.cgi”。表单中的这一行告诉我们，这些信息(当然，带有姓名标签)将被发送到某个文件。在这种情况下，display.cgi .既然，display.cgi 还不存在，那我们就创建它吧！

**4。)Display.cgi**

现在我们已经有了表单(不管你喜欢怎么称呼这个文件，只要确保它有一个. html 或者。htm 扩展名)，我们将继续创建 Perl 脚本来处理数据。整件事的运作方式是:

1.用户将查看表单并填写数据

2.表格上的每个字段和输入的数据都有一个名字

3.输入的数据以及表单字段的名称将被发送到 display.cgi 进行处理

让我们创建 display.cgi！

首先，shebang 行:
 `#!/usr/local/bin/perl` 
接下来，插入这个:
 `require "subparseform.lib";
&Parse_Form;` 
subpseform . lib 是一个广泛使用的文件，它将帮助我们创建这个脚本。在网上搜索一下，你应该能找到它。如果你的搜索失败了，联系我，我会给你一些帮助:mailto:chris@mycoding.com。

现在，请记住:我们希望这个脚本从表单中获取信息，并以这样一种方式显示给用户:“这是您输入的信息。”为此，我们需要告诉脚本“获取”表单信息。但是怎么做呢？我们只需匹配这些标签！

接下来进入这个:
 `$first = $formdata{'first};
$city = $formdata{'city'};
$food = $formdata{'food'};` 
眼熟吗？

注意几件事:首先是“；”符号应该在每一行的末尾。第二，标签名称:它们与表单信息上的标签完全匹配。

注意等号(“=”)。这告诉脚本将输入到表单中的数据分配给一个变量或容器，我们可以在 Perl 脚本中使用这个变量或容器。第一行是“make $first 等于在名为‘first’的表单字段中输入的信息”。

你可能已经注意到了，首先，城市和食物前面有一个美元符号。Perl 要求脚本中使用的所有变量或容器都以$符号开头。

如我之前所说，第一行告诉浏览器，在 HTML 文件中名称为“first”的表单字段现在的名称为“$first”。

这允许我们在 Perl 的其他地方使用“$first”变量。将会发生的情况是,$first 将被用户在反馈表单的“first”字段中输入的内容所替换！

开始明白这是怎么回事了吗？

现在，将它复制并粘贴到 display.cgi 文件中:
 `print "Content-type: text/htmlnn";
print "Thank you, here is the information you entered:<br>";
print "<ul><li>$first</li><li>$city</li><li>$food</li></ul>";` 
第一行告诉脚本您将在一个或多个“print”语句中使用 HTML。现在表单中的信息已经被赋予了以$符号开头的新变量，我们所要做的就是键入这些变量名，脚本将显示用户输入的内容。

您在上面看到的 print 语句只是告诉 Perl 在解析完 HTML 代码后将其发送给浏览器。下面是 Perl 在阅读上面三行时的“想法”:

第一行:嗯…我们将在下面的一个或多个打印语句中使用 HTML 语句。

第二行:好的，这个很简单。我将把引号之间的代码返回给浏览器。

第三行:这一行包含三个名为$first、$city 和$food 的变量，我需要在将这一行代码发送到浏览器之前找到并替换它们。我将从刚刚提交给我的表单中名为“first”的表单字段中获取$first 的值，然后对$city 和$food 进行同样的操作。现在我知道了$first、$city 和$food 代表什么，我可以将这一行代码发送到浏览器，并进行替换。

下面是整个 display.cgi 文件:
 `#!/usr/local/bin/perl`

 `需要“sub pseform . lib”；
&Parse _ Form；

$ name = $ formdata { ' name ' }；
$ city = $ formdata { ' city ' }；
$ food = $ formdata { ' food ' }；` 

`print "Content-type: text/htmlnn";
print "Thank you, here is the information you entered:<br>";
print " - First Name: $first<br> - City: $city<br>";
print " - Favorite Food: $food";` 
假设用户输入姓名“John”，城市“New York”，最喜欢的食物“Pizza”以下是 John 在填写完表格后看到的内容:

谢谢，这是你输入的信息:
–名字:约翰
–城市:纽约州
–最喜欢的食物:披萨

现在，您应该已经基本掌握了 Perl 的一些规则，以及在数据上打上标识“标签”是多么有用。稍后您还将了解到，您可以告诉 Perl 使用这些相同的变量(标记)发送电子邮件。想象一下，让某人输入他的电子邮件地址和姓名，并在主题行中得到一封个性化的电子邮件！

在第 2 部分中，我们将尝试向您的头脑中灌输一些语法规则，探索“if”、“elsif”和“else”命令，甚至探索使用 sendmail 发送自动确认/感谢电子邮件！

欢迎学习本 Perl 教程的第二部分。在第一部分中，向您展示了 Perl 脚本如何获取通过 HTML 提交的信息，并通过使用变量将结果显示给提交表单的人。变量是 Perl 语言的核心，所以今天我们将学习更多关于它们的知识。

**1)Test.html**

创建另一个 HTML 页面，并将其命名为“test.html”

不要忘记用 test.cgi 文件的路径替换表单动作 URL。

注意这些表单…它们只有一个提交按钮和一个“隐藏”的表单字段。这个表单域对访问者来说显然是不可见的，但是它仍然可以保存一个变量。如果我们单击第一个按钮(标记为“Page 1”)，它会将数据传递给 test.cgi 文件。你问哪个数据？为什么，当然是说“动作”字段的值为“2”的数据！使用这些值，我们可以告诉脚本确定哪个按钮被按下，并显示相应的文本和/或 HTML。

很明显，下一步是创建 test.cgi 文件…让我们现在就开始吧。

2) Test.cgi

创建一个文本文件，并将其保存为“test.cgi”…像往常一样，您需要将 subparseform.lib 文件上传到与 test . CGI 相同的目录中。像以前一样，如果您无法通过网络搜索找到它，请给我发电子邮件(chris @ movieforums.com)，我会将它作为附件发送给您。

继续做事情；将此输入 test.cgi:
`#!/usr/local/bin/perl
require "subparseform.lib";
&Parse_Form;
$action = $formdata{'action'};`

如你所见，我们首先有标准的 shebang 线。其次，我们有一个命令确保 subparseform.lib 位于正确的目录中。第三，我们有一个子例程(现在不要担心这个词)，它被调用来帮助我们处理从 test.html 发送的数据，第四，我们有一行代码从 test.html 获取 action 字段的值，并给它赋以变量“$action”

接下来，输入以下内容:

```
print "Content-type: text/htmlnn";
```

这告诉脚本，我们可能在打印命令中使用了一些 HTML…即使您不使用任何 HTML，我也建议您在脚本中始终使用这一行，以避免潜在的问题。

现在，输入这个:
 `if ($action eq "1") {
print "You must have clicked the first button!";
}` 
这不是很简单的英语，但理解本教程中的许多其他内容要容易得多。以上被称为“如果”命令。看到第一行了吗？翻译成英语，应该是“如果变量' $action '等于 1。”注意这个语句后面的左括号，以及 print 语句后面的右括号。你知道这是怎么回事吗？

“if”语句基本上是告诉脚本，如果$action 等于“1”，那么就处理这两个括号内的代码。如果有人点击了，比如说，第二个按钮，那么$action 变量将等于除 1 之外的值，括号内的代码将不会被处理——脚本将简单地跳过它。

现在，假设您想要显示一些文本，以防有人按下第二个按钮。Perl 能处理这个吗？见鬼耶……输入这个:
 `elsif ($action eq "2") {
print "Well, odds are you clicked the second button...";
}` 
上面的代码相当不言自明。它在各方面都像“如果”语句，除了我们在“如果”前面有一个“els”这句话变成一句话，应该是“Else if”这是在告诉脚本，如果前面的“if”语句为假(即:如果不满足等于条件)，那么看一下这个语句，如果等于“true”(如果$action 变量等于 2)，那么就处理里面的代码。

如果用户按下第三个按钮，创建一个显示文本的语句几乎是完全一样的:
 `elsif ($action eq "3") {
print "Who woulda thought? You pressed the third button!";
}` 
到目前为止，这一切都很好，但是我们需要某种自动防故障机制。使用目前的代码，无论按下三个按钮中的哪一个，都会显示一条消息。但是如果发生了别的事情。例如:如果$action 变量(不管是什么原因)不等于 1、2 或 3，那该怎么办？
无所畏惧，对此也有说法:
 `else {
print "Sorry, there is no page available for that number.";
}` 
A-ha！我们可以有一个“if”语句，两个“else-if”语句，现在还有一个“else”语句。“else”语句是最后一招…如果前三个条件都不满足，就处理 else 语句。它基本上是说“如果上面的语句都不需要处理，就处理我。”这确保了无论$action 变量等于什么，都会显示一条消息。

咻——学够了吗？没想到……继续看。

**3)用 Perl 发送电子邮件**

谢天谢地，Perl 脚本有发送电子邮件的能力。不仅如此，还可以使用变量来指定电子邮件中的文本和发送到的地址。你知道这意味着什么:用户输入他们的电子邮件地址，并收到几乎即时确认电子邮件！放心吧，这样做一点都不。创建一个名为 email.html 的文件，并在其中使用这个 HTML:
 `<html>
<head>
<title>Signup Page</title>
</head>`

`<body>
<h3>Signup Today!</h3>
<form action="http://www.yourdomain.com/cgi-bin/email.cgi" method="post">
<br>
<b>Email Address:</b> <input type="text" name="email">
<br>
<input type="submit" value="Signup">
</body>
</html>` 
同样，不要忘记用 email.cgi 文件的路径替换表单动作 URL。接下来，创建一个新的文本文件，将其命名为“email.cgi”，并将其输入其中(前几行应该看起来很熟悉):
 `#!/usr/local/bin/perl
require "subparseform.lib";
&Parse_Form;
$email = $formdata{'email'};` 
好了，现在我们进入未知的领域。打起精神:
 `open (EMAILS, ">>emails.txt");
flock(EMAILS, 2);
print EMAILS "$emailn";
flock(EMAILS, 8);
close (EMAILS);` 
让我们一个一个来。第一行告诉脚本打开一个文件——emails . txt。如果这个文件不存在，Perl 将自己创建一个。如果您计划让 Perl 创建该文件，请确保您的目录已经设置了必要的权限。如果你不知道我说的目录权限是什么意思，那么只需将一个名为 emails.txt 的空白文本文件上传到 email.html 和 email.cgi 所在的目录。脚本中的第一行还为该文件指定了一个名称:“emails”

第二种方式利用了一种叫做“文件锁定”的东西这样做是为了确保当脚本仍在处理这段代码时，打开的文件不会改变。想象一下，如果有人利用这个脚本试图在 emails.txt 中输入他们的信息，就像其他人也在做同样的事情一样，会发生什么？哪个会先走？这为腐败和一堆混乱的文本留下了隐患。

文件锁定在这里显然被简称为“flock”在这种情况下，它用于确保调用 emails.txt 文件的其他脚本不被允许打开它以供使用，直到该脚本正在使用它，或者直到文件锁定命令已经结束。

第三行很简单:它将用户在“email”字段中输入的内容打印到 emails.txt 中，后跟一个换行符——这里用换行符表示:“`n`”。

接下来输入这个:
 `print "Content-type: text/htmlnn";
print "The email address '$email' has been successfully entered. ";
print "Thank you. You will receive a confirmation email shortly.";` 
第四行解锁 emails.txt 文件用于其他地方，第五行正式关闭它。到目前为止，如果这还不明显的话，这个脚本将用户输入的电子邮件地址输入到 emails.txt 文件中，开始新的一行，并在下次输入电子邮件时重复整个过程。正因为如此，你可以允许人们填写表格，将他们的电子邮件存储在文本文件中，并随时查看。这些地址将被拆分，以便文件的每一行都有一个地址。

但是我们在上面的打印声明中向用户承诺的确认邮件呢？别担心，下面是代码:
 `$from = "your@email.com";
open (MAIL, "|/usr/sbin/sendmail -t") || &ErrorMessage;
print MAIL "To: $email nFrom: $fromn";
print MAIL "Subject: Confirmation Emailn";
print MAIL "Thank you for signing up. Your email address ($email) has been successfully ";
print MAIL "stored. Have a nice day.n";
close (MAIL);
sub ErrorMessage {
print "<P>The server has a problem. Aborting script. n";
exit;
}` 
第一行给了$from 变量一个值——更改“you@email.com”文本以反映您的地址，但确保在“@”符号前面保留反斜杠。这是避免解析错误所必需的。

第二行指定服务器上“sendmail”的路径，如果该路径不正确或由于某种原因没有找到，它将在标题“sub ErrorMessage”下显示找到的错误消息

第 3、4 和 5 行将文本打印到电子邮件中…它打印一个小标题，显示电子邮件发送给谁($email，用户最初在表单中输入的电子邮件)，以及发送电子邮件的人的地址($from)。第 6 行关闭了邮件，剩下的就是前面提到的错误消息。

下面是完整的 email.cgi 脚本:
 `#!/usr/local/bin/perl`

 `需要“sub pseform . lib”；
&Parse _ Form；

$ email = $表格资料{'email'}：

打开(EMAILS，" > > EMAILS . txt ")；
羊群(电子邮件，2)；
打印邮件“$ emailn”；
羊群(电子邮件，8)；
关闭(邮件)；

打印“内容类型:text/html nn”；
打印“您的电子邮件地址(＄email)已成功输入。”；
打印“谢谢。您将很快收到一封确认电子邮件。”；` 

`$from = "your@email.com";
open (MAIL, "|/usr/sbin/sendmail -t") || &ErrorMessage;
print MAIL "To: $email nFrom: $fromn";
print MAIL "Confirmation Emailnn";
print MAIL "Thank you for signing up. Your email address ($email) has been successfully ";
print MAIL "stored. Have a nice day.n";
close (MAIL);
sub ErrorMessage {
print "<P>The server has a problem. Aborting script. n";
exit;
}` 
**4)总结**

祝贺您——假设我没有在第一部分的某个地方忘记您，您现在已经知道了 Perl 的基本语法，如何打印文本，如何通过 HTML 表单处理用户提交的信息，如何用 Perl 发送电子邮件，如何根据提交的信息显示不同的消息，以及如何将用户变量存储在文本文件中以便于查看。

希望您现在能够满怀信心而不是恐惧地看待 Perl 和其他编程语言。请随意使用和修改这里提供的代码，就像是您自己的代码一样。

祝你好运，并快乐编码！

## 分享这篇文章