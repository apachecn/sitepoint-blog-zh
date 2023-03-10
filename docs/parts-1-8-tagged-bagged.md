# JSP 文件——第 1 部分到第 8 部分:标记和打包

> 原文：<https://www.sitepoint.com/parts-1-8-tagged-bagged/>

##### 欢迎使用 JSP 文件！

如果您对学习 JSP 感兴趣，那么您来对地方了！在接下来的几周里，你会在这里找到以下综合教程——所以一定要每周回来查看以获得最新的一期。

如果你需要一些帮助，别忘了给凯文·杨克的[JSP Windows](http://www.webmasterbase.com/article/305)快速入门指南。

JSP 文件—第 1 部分:水果篮子里的紫色小猪
通过这篇介绍性教程来了解 Java 服务器页面，其中包括变量、包含和字符串对象。

JSP 文件—第二部分:黑仔幸运饼干的攻击
条件表达式、逻辑和比较运算符，以及一篮子幸运饼干。你还能要求什么？

JSP 文件—第 3 部分:黑光和白兔
更多的字符串对象方法，以及 JSP 中可用的各种控制结构。

[JSP 文件—第四部分:红色药丸](https://www.sitepoint.com/blog/)
了解 JSP 可以用来处理表单数据，并了解请求对象。

[JSP 文件—第 5 部分:无转发地址](https://www.sitepoint.com/blog/)
用 JSP 构建动态的、数据驱动的网页。

JSP 文件—第 6 部分:优雅状态
学习如何在基于 JSP 的网站上用 Cookie 和会话对象“维护状态”。

JSP 文件—第 7 部分:bug、bean 和银行
消灭 bug，加热 bean，学习更多关于 JSP 的知识。

[JSP 文件—第 8 部分:标记和打包](https://www.sitepoint.com/blog/)
了解如何使用定制标记库加速 JSP 代码开发。

尽情享受吧！

##### JSP 文件—第 1 部分:果篮中的紫色猪

JSP 的故事

自从 Sun Microsystems(又名“网络中的点”)提出 Java 以来，极客们就一直在声嘶力竭地谈论这项技术的奇迹。像“独立于平台的代码”和“编写一次，在任何地方运行”这样的术语已经被广泛使用，甚至 Java 开发新手都知道它们，这种语言也受到脱口秀专家和互联网顾问的欢迎，他们吹捧它是几乎所有跨平台兼容性问题的解决方案。

现在，我们是 Java 的忠实粉丝——我们过去使用过它，将来还会使用它——但是这一系列教程不是关于 Java 的。它实际上是 Java 的一个分支，名为 Java Server Pages，或 JSP，它试图为 Web 开发人员提供一种令人信服的替代传统服务器端脚本语言，如 Perl、PHP 和 ASP。

***如何？一、一点历史……***

在网络的早期，它是绝对数量的(免费！)鼓励人们使用它的内容。使用简单易学的 HTML 语言，任何一个人和他们失聪的奶奶都可以建立一个网页，并在网上联系其他志同道合的人。随着基础设施的改进，内容不再局限于文本；你现在可以在网上浏览图片或观看视频。随着越来越多的人开始在他们的网站上增加交互性，为了满足日益复杂的需求，一批编程语言诞生了。

其中最著名的当然是 Perl，尽管 PHP 和 ASP 也很受欢迎。然而，这些语言的问题是，对 Web 服务器的每个网页请求都会在服务器上生成一个新的进程，随着访问者流量的增加，会导致性能问题。

Java 为这个问题提供了一个解决方案，它使用所谓的“servlet”来创建交互式网站(Java servlet 类似于 Java applet，只是它运行在 Web 服务器上，而不是客户端 Web 浏览器上——如果您现在感到困惑，请等到我们使用 scriptlets 时再看)。Java 还使实体公司将他们的遗留系统与网上可用的新技术连接起来，并通过因特网快速开发和连接后台办公自动化系统成为可能。

然而，servlet 也有自己的问题——一个简单的接口或逻辑修改经常会导致 servlet 发生深远的变化。因此，开发 JSP 是为了将应用程序逻辑从接口中分离出来，这样对其中一个的更改就不会影响到另一个。JSP 与 Apache Group 等开发人员密切合作，使用基于标记的方法(类似于 PHP 和 ASP ),允许设计人员在不影响应用程序逻辑的情况下更改用户界面。

如果您想知道可伸缩性，JSP 支持使用 JavaBeans 或 Enterprise JavaBeans 的基于组件的架构；这允许开发人员创建可重用的代码模块，从而加快开发时间。因为这是 Java，你可以无缝地将 Web 应用程序连接到遗留系统，从而降低将现实世界的业务转移到网络空间的成本。和我们一起说——平台独立性震撼人心！

##### 研究基础

JSP 是基于多层架构的，这可以通过将它与非 JSP 站点上看到的架构进行比较来得到最好的解释。在典型的 Apache/PHP/mySQL 架构中，在一个层次上有一个 Web 服务器和一个数据库服务器，由 PHP 之类的脚本语言负责两者之间的通信，以生成动态内容。虽然这种体系结构对于吸引中等流量的网站来说很好，但是当流量增加，数据库和 Web 服务器的负载增加时，它就开始显露出它的缺点。

另一方面，JSP 体系结构涉及不止一个层次，立即使其更具可伸缩性和可维护性。

如果您想知道这些长词的含义，可伸缩意味着您可以随着流量的增加轻松地增加或“扩大”您的系统，而可维护意味着可以简单地修改系统的一部分——例如，从一个数据库转换到另一个——而不会影响其他领域。

在 JSP 的上下文中，多层架构包括用于静态 HTML 内容的 Web 服务器、用于 JavaBeans 和 servlets 的应用服务器以及用于数据库连接的数据库服务器。此外，您可以将 JSP 与 JavaBeans 和 Java servlets 结合起来创建复杂的 Web 应用程序，这些应用程序建立在以前发布并经过测试的代码模块之上，从而简化代码维护并提高可重用性。

这里需要注意的是，JSP 代码不像 PHP 那样是逐行读取的；它首先被转换成 servlet(程序的字节码版本)，然后被 servlet 引擎(比如 Tomcat)调用来执行所需的动作。一旦 servlet 被执行，结果就被发送回客户机。由于 servlet 引擎必须在第一次编译 servlet，所以在您第一次访问 JSP 页面时，显示它可能需要一点时间；但是，下一次，响应时间将会大大减少，因为 servlet 已经编译好，可以立即使用了。
版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 茶杯里的爪哇咖啡

为了开始使用 JSP，您需要获得 Sun 的 Java 开发工具包、Apache 的 httpd Web 服务器和 mod_jserv 模块以及 Tomcat servlet 引擎的副本，并对它们进行配置，以便它们能够协同工作。本教程假设您已经建立了一个 JSP 开发环境——如果您没有，请看一下[“拼凑一个 JSP 开发环境”](http://www.melonfire.com/community/columns/trog/article.php?id=48)，本教程将指导您完成整个过程。

说完这些，让我们开始实际创建 JSP 页面的细节。在您最喜欢的文本编辑器中打开一个新文件，键入以下代码行:

```
<html>   
<head>   
</head>   
<body>   
<%   
// asking for it!   

out.println("Waiter, can I have a cup of Java, please?");   

%>   
</body>   
</html>
```

用扩展名保存该文件。JSP-例如“coffee . JSP”-在适当的位置，然后通过将浏览器指向它来查看它-例如 http://localhost/JSP/coffee . JSP。您应该会看到类似这样的内容:

```
<html>   
<head>   
</head>   
<body>   

Waiter, can I have a cup of Java, please?   

</body>   
</html> 
```

蚱蜢，这是你的第一个剧本！

在 JSP-lingo 中，“scriptlet”是当用户请求 JSP 页面时由 JSP 引擎执行的代码块。所有 scriptlets 都包含在标记中(类似于 ASP 和 PHP 代码)，如下所示:

```
<%   
... JSP code ...   

out.println("Waiter, can I have a cup of Java, please?");   

... JSP code ...   
%>
```

每个 JSP 语句都以分号结尾——这个约定与 Perl 中使用的相同，省略分号是新手最常犯的错误之一。仅举一个例子，当您从上面的示例中省略分号时，会发生以下情况:

```
Error: 500   
Location: /jsp/coffee.jsp   
Internal Servlet Error:   
org.apache.jasper.JasperException: Unable to compile class:    
Invalid type expression.   
 out.println("Waiter, can I have a cup of Java, please?")   
 ^   
: Invalid declaration.   
 out.write("rnrnrn");   
 ^   
2 errors   
 at org.apache.jasper.compiler.Compiler.compile   
(Compiler.java, Compiled Code)   
 at org.apache.jasper.servlet.JspServlet.doLoadJSP   
(JspServlet.java:462)   
 at   
org.apache.jasper.servlet.JasperLoader12.loadJSP   
(JasperLoader12.java:146)   
 at org.apache.jasper.servlet.JspServlet.loadJSP   
(JspServlet.java:433)   
 at org.apache.jasper.servlet.JspServlet$JspServletWrapper.   
loadIfNecessary(JspServlet.java:152)   
 at org.apache.jasper.servlet.JspServlet$JspServletWrapper.   
service(JspServlet.java:164)   
 at org.apache.jasper.servlet.JspServlet.serviceJspFile   
(JspServlet.java:318)   
 at org.apache.jasper.servlet.JspServlet.service   
(JspServlet.java, CompiledCode)   
 at    
javax.servlet.http.HttpServlet.service(HttpServlet.java:853)   
 at org.apache.tomcat.core.ServletWrapper.doService   
(ServletWrapper.java:404)   
 at org.apache.tomcat.core.Handler.service(Handler.java:286)   
 at org.apache.tomcat.core.ServletWrapper.service   
(ServletWrapper.java:372)   
 at org.apache.tomcat.core.ContextManager.internalService   
(ContextManager.java:797)   
 at org.apache.tomcat.core.ContextManager.service   
(ContextManager.java:743)   
 at org.apache.tomcat.service.connector.Ajp12Connection   
Handler.processConnection(Ajp12ConnectionHandler.java:166)   
 at org.apache.tomcat.service.TcpWorkerThread.runIt   
(PoolTcpEndpoint.java, Compiled Code)   
 at org.apache.tomcat.util.ThreadPool$ControlRunnable.run   
(ThreadPool.java, Compiled Code)   
 at java.lang.Thread.run(Thread.java, Compiled Code)
```

哎呦！

还可以向 JSP 代码添加注释，如上例所示。JSP 支持单行和多行注释块——请看:

```
<%   

// this is a single-line comment   
/* and this is a   
multi-line   
comment */   

%>
```

像 PHP 和 Perl 一样，空白在 JSP 中被忽略。

最后，实际将输出打印到浏览器的语句——正如您将看到的，这是使用“out”对象完成的。因为 JSP 是基于 Java 的，而 Java 是一种面向对象的语言，所以您的大多数 JSP 语句都将包含像这样的对象引用。
版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 输入无名氏

变量是每种编程语言的面包和黄油 JSP 也有它们。变量可以被认为是用来存储数字和非数字数据的编程结构；然后，可以在 JSP 脚本的不同位置使用这些数据。

JSP 支持许多不同的变量类型:整数、浮点数、字符串和数组。与 PHP 不同，PHP 可以根据它保存的数据自动确定变量类型，JSP 要求您在使用每个变量之前显式定义它的类型。

每个变量都有一个名称——在 JSP 中，变量名前面有一个指示变量类型的关键字，并且必须以一个字母开头，后面可以有更多的字母和数字。变量名区分大小写，保留关键字不能用作变量名。

比如“popeye”、“one_two_three”、“bigPirateShip”都是有效的变量名，而“byte”和“123”则是无效的变量名。

以下示例演示了如何在 JSP 文档中使用变量。

```
<html>    
<head>    
</head>    
<body>    
<%!    
// define variables here    
String name = "John Doe";    
%>    

<%    
// code comes here    
out.println("My name is " + name );    
%>    
</body>    
</html>
```

正如您所看到的，我们首先定义了一个名为“name”的变量，对其进行了设置，使其可以保存字符串或字符、数据，并为其赋值(“John Doe”)。然后 println()函数使用这个值在 HTML 页面中显示一条消息。

```
My name is John Doe
```

如果你眼尖的话，你会注意到上例中两个 JSP 块之间的细微差别——第一个看起来像这样:

```
<%!    
...    
%>
```

…而第二个看起来像这样:

```
<%    
...    
%>
```

定义变量的第一个块称为“声明块”；这个块中声明的变量对于 JSP 文档中的每个 scriptlet 都是全局可用的。

您也可以定义一个变量而不为其赋值，或者在稍后阶段为其赋值，例如，以下代码片段是等效的。

```
<%    
String name;    
name = "John Doe";    
%>    

<%    
String name = "John Doe";    
%>
```

##### 把两者结合起来

正如您可以创建变量来保存字符串一样，您也可以创建其他类型的变量:

*   **int—**用于存储整数
*   **char–**用于以 Unicode 格式存储单个字符
*   **float and long-**用于存储浮点数
*   **boolean-**用于存储“真”和“假”值(注意，与 C 和 PHP 等语言不同，JSP 不识别 1 = >真和 0 = >假)

让我们举一个简单的例子，将两个数字相加并显示结果。

```
<html>    
<head>    
</head>    
<body>    
<%!    
 int alpha = 45;    
 int beta = 34;    
 int Sum;    
%>    
<%    
 // add the two numbers    
 Sum = alpha + beta;    

 // display the result    
 out.println("The sum of " + alpha + " and " + beta + " is " + Sum); %>     
</body>     
</html>
```

输出是:

```
The sum of 45 and 34 is 79
```

在这种情况下，我们简单地将两个变量定义为整数值，给它们赋值，然后将它们相加得到总和。

同样，下一个示例演示了如何将字符串相加:

```
<html>    
<head>    
</head>    
<body>    
<%!    
// define the variables    
String apples = "The lion ";    
String oranges = "roars in anger";    
String fruitBasket;    
%>    
<%    
// print the first two strings    
out.println("<b>The first string is</b>:    
" + apples + "<br>"); out.println("<b>The second string     
is</b>: " + oranges + "<br>");    

// concatentate the strings    
fruitBasket = apples + oranges;    

// display    
out.println("<b>And the combination is</b>: " + fruitBasket + "<br>Who     
says you can't add apples and oranges?!");    
 %> </body> </html>
```

输出是:

```
The first string is: The lion    
The second string is: roars in anger    
And the combination is: The lion roars in anger    
Who says you can't add apples and oranges?!
```

在这种情况下，使用+运算符将两个字符串连接在一起，然后通过`println()`显示出来。
版权[梅龙费尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 废人

String 对象附带了许多有用的方法，这些方法在执行字符串操作时会派上用场。

第一个是 `length()` 方法，用来获得(你猜对了！)特定字符串的长度。让我们修改您刚才看到的示例，以演示其工作原理:

```
<html>     
<head>     
</head>     
<body>     
<%!     
// define the variables     
String apples = "Purple pigs ";     
String oranges = "riding orange pumpkins";     
String fruitBasket;     
%>     
<%     
// print the first two strings     
out.println("<b>The first string is</b>: " + apples + "<br>"); out.println     
("<b>The second string is</b>: " + oranges + "<br>");     

// concatentate the strings     
fruitBasket = apples + oranges;     

// display     
out.println("<b>And the combination is</b>: " + fruitBasket + "(" +     
fruitBasket.length() + " characters)<br>Who says you can't add      
apples and oranges?!"); %> </body> </html> 
```

输出是:

```
The first string is: Purple pigs     

The second string is: riding orange pumpkins     

And the combination is: Purple pigs riding orange pumpkins(34 characters)     
Who says you can't add apples and oranges?! 
```

您可以使用 `charAt()` 方法从字符串中提取特定的字符，该方法接受偏移量作为参数。例如，以下代码片段将返回字符“o”:

```
<%     
String name = "Bozo The Clown";     
out.println(name.charAt(3));     
%>
```

注意，偏移量 0 表示第一个字符，因为 Java 和它的许多同类产品一样，使用从零开始的索引。

您还可以使用 substring()方法提取字符串的一部分，该方法允许您指定要提取的字符串段的起点和终点。看看这句话，看看你是否能发现其中隐藏的信息:

```
<%!     
String me = "I am a highly-skilled and hardworking developer!"; %>  No? How about now?      
<%!     
String me = "I am a highly-skilled and hardworking developer!";      
String message; %> <% message = me.substring(0,2) +      
me.substring(15,22) + me.substring(26,27) +      
me.substring(45,48); out.println(message); %>
```

这是输出结果:

```
I killed her!
```

##### 灵魂的字母汤

如果你以前用过 C，你可能已经熟悉了出现在每个 C 程序开头的“include”指令。JSP 支持一个等效的 include()函数，它做的事情基本相同。看看这个简单的例子:

```
<html>      
<head>      
<title> Thought For The Day</title>      
</head>      
<body>      

Thought For The Day:      
<br>      
<%@ include file="thought.html" %>      

</body>      
</html>
```

[thought.html]

```
Ever wonder if illiterate people get the full effect of alphabet soup?
```

在这种情况下，JSP 将自动读取文件“thoughts.html”的内容，并显示如下所示的复合页面:

```
<html>      
<head>      
<title>Thought For The Day</title>      
</head>      
<body>      

Thought For The Day:      
<br>      
Ever wonder if illiterate people get the full effect of alphabet soup?      

</body>      
</html>
```

`include()`函数的一个非常有用和实用的应用是用它在网站的所有页面上包含一个标准的页脚或版权声明，就像这样:

```
<html>      
<head>      
</head>      

<body>      

...your HTML page...      

<br>      

<%@ include file="footer.html" %>      

</body>      
</html>
```

其中“footer.html”包含:

```
<font size=-1 face=Arial>This material copyright Melonfire, 2001\.       
All rights reserved.</font>
```

现在，这个页脚将出现在包含上述 `include()`语句的每一页上——如果您需要更改消息，您只需编辑名为“footer.html”的单个文件！

这就是本周的全部内容。我们已经向您展示了 JSP 的基本构件，下一次，我们将使用这些基本概念来演示 JSP 的控制结构。不要错过！

*注意:本文中的所有例子都已经在装有 Tomcat 3.2 和 JServ 1.1 的 Linux/i586 上进行了测试。这些示例仅用于说明，并不适用于生产环境。YMMV！*
版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### JSP 文件—第 2 部分:黑仔幸运饼干的攻击

虐待

幸运的是，我们关于 JSP 的介绍性文章让您兴奋不已，以至于您在过去的几天里急切地练习变量名，并让您的朋友知道您比他们聪明多少。本周，我们将为您提供 JSP 条件语句和循环的速成课程，帮助您进一步巩固您的声誉。

确保你系紧了安全带——这将是一次地狱之旅！

##### 全部加起来

你会记得，在本教程的第一部分，我们如何使用+运算符将数字和字符串相加。就像加法运算符+一样，JSP 附带了许多其他算术运算符，旨在简化执行数学运算的任务。

以下示例演示了 JSP 中可用的重要算术运算符:

```
<html>       
<head>       
</head>       
<body>       

<%!       
// declare variables       
int alpha = 25;       
int beta = 5;       
int sum, difference, product, quotient, remainder;       
%>       

<%       
// perform operations       
out.println("The sum of " + alpha + " and "        
+ beta + " is " + (alpha + beta) + "<br>");       

out.println("The difference of " + alpha + " and "        
+ beta + " is " + (alpha - beta) + "<br>");       

out.println("The product of " + alpha + " and "        
+ beta + " is " + (alpha *       
beta) + "<br>");       

out.println("The quotient after division of " + alpha        
+ " and " + beta + " is " + (alpha / beta) + "<br>");        
out.println("The remainder after division of " + alpha        
+ " and " + beta + " is " + (alpha % beta) + "<br>"); %>       

</body>       
</html>
```

这是输出结果:

```
The sum of 25 and 5 is 30       
The difference of 25 and 5 is 20       
The product of 25 and 5 is 125       
The quotient after division of 25 and 5 is 5       
The remainder after division of 25 and 5 is 0
```

与所有其他编程语言一样，除法和乘法优先于加法和减法，尽管圆括号可用于赋予特定运算更高的优先级。举个例子，

```
<%       
out.println(10 + 2 * 4);       
%>
```

返回 18，而:

```
<%       
out.println((10 + 2) * 4);       
%>
```

返回 48。

除了这些操作符，JSP 还附带了非常有用的自动递增[++]和自动递减[–]操作符，您将在下一篇文章中看到很多。自动递增运算符将应用该运算符的变量的值递增 1，而自动递减运算符则相反。这里有一个例子:

```
<%!       
int x = 99;       
%>       

<%       
// x = 99       
out.println("Before increment, x = " + x + "<br>");       
x++;       
// x = 100       
out.println("After increment, x = " + x);       
%>
```

JSP 还附带了一堆比较操作符，它们唯一的存在理由是评估表达式并确定它们是真还是假。下面的表格应该会使这一点更加清楚。

假设 x=4，y=10

| 操作员 | 这意味着什么 | 表示 | 结果 |
| == | 等于 | x == y | 错误的 |
| ！= | 不等于 | x！= y | 真实的 |
| > | 大于 | x > y | 错误的 |
| < | 小于 | x < y | 真实的 |
| >= | 大于或等于 | x >= y | 错误的 |
| <= | 小于或等于 | x <= y | 真实的 |

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 昙花一现的风云人物

正如您可以比较数字一样，JSP 也允许您使用一些非常有用的字符串对象方法来比较字符串。

首先，equals()方法允许您检查特定字符串变量的值是否与另一个匹配。下面的例子将演示这一点。

```
<%        
// define variables        
String myFavourite = "chocolate";        
String yourFavourite = "strawberry";        

// compare strings        
if (myFavourite.equals(yourFavourite))        
{        
out.println("A match made in heaven!");        
}        
else        
{        
out.println("Naw - try again!");        
}        
 %>
```

尝试更改变量的值以使它们相互匹配，并在输出改变时惊叹不已。

如果您对`equals()` 方法不感兴趣，JSP 会以`compareTo()` 方法的形式为您提供一个选择，该方法返回一个值，指示两个字符串中哪个更大。看一看:

```
<%        
// define variables        
String alpha = "abcdef";        
String beta = "zyxwvu";        

// compare strings        
out.println(alpha.compareTo(beta));        
 %>
```

在这种情况下，如果变量“beta”的值大于变量“alpha”的值，`compareTo()` 方法将返回一个负整数；如果反过来，比较将返回正整数。如果两个字符串相同，比较将返回 0。

顺便说一下，比较是基于字符串的第一个字符和字符串中的字符数。如果一个字符串的第一个字符的数值大于另一个字符，或者其长度大于另一个字符，则认为该字符串“大于”另一个字符串。在上面的例子中，“z”的数字代码比“a”大，因此比较将返回负整数。但是不要相信我们的话——自己试试看吧！

##### 打开暖气

你为什么需要知道这些？在构建条件表达式时，比较运算符非常有用——在向代码中添加控制例程时，条件表达式也非常有用。控制例程检查某些条件的存在，并根据它们发现的情况执行适当的程序代码。

第一个也是最简单的决策程序是“如果”语句，如下所示:

```
if (condition)        
 {        
 do this!        
 }
```

这里的“条件”指的是条件表达式，其计算结果为 true 或 false。举个例子，

```
if (hard drive crashes)        
 {        
 get down on knees and pray for redemption        
 }
```

或者，用 JSP 行话来说:

```
<%        
if (hdd == 0)        
 {        
 pray();        
 }        
%>
```

如果条件表达式的计算结果为 true，则执行花括号内的所有语句。如果条件表达式的计算结果为 false，则花括号内的所有语句都将被忽略，并且将执行“If”块后面的代码行。

这里有一个简单的程序来说明“如果”语句的基础。

```
<%!        
// declare temperature variable        
int temp = 50;        
 %>        

<%        
// check temperature and display output        
if (temp > 30)        
{        
out.println("Man, it's hot out there!");        
}        
 %>
```

在本例中，定义了一个名为“temp”的变量，并将其初始化为值 50。接下来，使用“if”语句检查“temp”变量的值，如果超过 30，则显示一条消息。请注意我们在条件表达式中使用的大于号(>)条件运算符。

需要注意的重要一点是赋值操作符[=]和等式操作符[==]之间的区别，这也是许多编程新手经常遇到的问题。前者用于为变量赋值，而后者用于测试条件表达式中的相等性。

所以:

```
a = 47;
```

给变量 a 赋值 47，而

```
a == 47
```

测试 a 的值是否等于 47。
版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 做吧，否则…

除了“if”语句之外，JSP 还提供了“if-else”语句，允许您根据表达式的值是真还是假来执行不同的代码块。

“if-else”语句的结构如下所示:

```
if (condition)         
 {         
 do this!         
 }         
else         
 {         
 do this!         
 }
```

在这种情况下，如果条件表达式的计算结果为 false，将执行“else”块的大括号内的所有语句。修改上面的例子，我们有:

```
<%!         
// declare temperature variable         
int temp = 50;         
 %>         

<%         
// check temperature and display output         
if (temp > 30)         
{         
out.println("Man, it's hot out there!");         
}         
else         
{         
out.println("Well, at least it isn't as hot as it could be!"); } %>
```

在这种情况下，如果构造的第一次传递失败(温度*不*大于 30)，控制将转移到第二部分——即“else”语句——而执行“else”块中的代码。您可以通过调整“temp”变量的值来测试这两种可能性，并在浏览器中查看结果输出。

##### 千篇一律的代码

“if-else”结构无疑比基本的“if”结构提供了更多的灵活性，但是仍然将您限制在只有两种可能的行动方案。如果您的脚本需要能够处理两种以上的可能性，您应该使用“if-else if-else”结构，这是您刚刚读到的两种结构的完美结合。

```
if (first condition is true)         
 {         
 do this!         
 }         
else if (second condition is true)         
 {         
 do this!         
 }         
else if (third condition is true)         
 {         
 do this!         
 }         

 ... and so on ...         

else         
 {         
 do this!         
 }
```

看一看它的运行情况:

```
<%!         
// declare temperature variable         
int temp = 20;         
 %>         

<%         
// check temperature and display output         
// what happens if temp is less than 25 degrees         
if (temp >= 25)         
{         
out.println("Man, it's hot out there!");         
}         
// what happens if temp is between 25 and 10 degrees         
else if (temp < 25 && temp > 10)         
{         
out.println("Great weather, huh?!");         
}         
// what happens if temp is less than ten degrees         
else if (temp <= 10)         
{         
out.println("Man, it's freezing out there!");         
}         
// this is redundant, included for illustrative purposes         
else         
{         
out.println("Huh? Somebody screwed up out there!");         
}         
 %>
```

在这种情况下，根据“temp”变量的值，执行适当的代码分支，从而可以编写允许多种可能性的脚本。

这里需要注意的重要一点是:只有当前面的条件被证明为假时，控制才被转移到连续的“如果”分支。或者，在英语中，一旦某个特定的条件表达式得到满足，所有后续的条件表达式都被忽略。

这是另一个例子，这个例子使用星期几来决定显示哪个幸运饼。更改“day”变量，每次看到不同的 cookie。

```
<%!         
String day = "Monday";         
String fortune;         
%>         
<%         
// check day and set fortune         
if (day.equals("Monday"))         
{         
fortune = "Adam met Eve and turned over a new leaf.";         
}         
else if (day.equals("Tuesday"))         
{         
fortune = "Always go to other people's funerals, otherwise          
they won't come to yours."; } else if (day.equals("Wednesday"))         
 { fortune = "An unbreakable toy is useful for breaking other toys."; }         
 else if (day.equals("Thursday")) { fortune = "Be alert - the          
world needs more lerts."; } else if (day.equals("Friday"))         
{ fortune = "Crime doesn't pay, but the hours are good."; }          
else { fortune = "Sorry, closed on the weekend"; }          
// print output out.println(fortune); %         
> Copyright [Melonfire](http://www.melonfire.com/), 2000\. All rights reserved.
```

##### 米兰午餐

如果仔细看看上面的倒数第二个例子，您会注意到条件表达式

```
(temp < 25 && temp > 10)
```

与你迄今为止所习惯的略有不同。这是因为 JSP 还允许您在一种称为“逻辑运算符”的动物的帮助下，将多个条件组合成一个表达式。

下面的表格应该会使这一点更加清楚。

假设δ= 12，γ= 12，ω= 9

| 操作员 | 这意味着什么 | 例子 | 翻译 | 评估为 |
| && | 和 | δ= =γ& &δ>ω | δ等于γ，δ大于ω | 真实的 |
| && | 和 | δ= =γ& &δ | δ等于γ，δ小于ω | 错误的 |
| &#124;&#124; | 运筹学 | δ= =γ&#124; &#124;δ | delta 等于 gamma 或
delta 小于 omega | 真实的 |
| &#124;&#124; | 运筹学 | δ>γ&#124; &#124;δ | δ大于γ或
δ小于ω | 错误的 |
| ！ | 不 | ！三角洲 | 德尔塔不是真的 | 错误的 |

因此，代替像这样丑陋的东西:

```
<%          
if (day == "Thursday")          
 {          
 if (time == "12")          
 {          
 if (place == "Italy")          
 {          
 lunch = "pasta";          
 }          
 }          
}          

 %>
```

你可以有像这样优雅的东西:

```
<%          
if (day == "Thursday" && time == "12" && place == "Italy")          
 {          
 lunch = "pasta";          
 }          

 %>
```

##### 调换东西

最后，JSP 使用“switch”语句来完善其条件表达式，这提供了一种将控制权从一个程序块转移到另一个程序块的替代方法。它看起来是这样的:

```
switch (decision-variable)          
 {          
 case first_condition_is true:          
 do this!          

 case second_condition_is true:          
 do this!          

 case third_condition_is true:          
 do this!          

 ... and so on...          

 default:          
 do this by default!          

 }
```

通过使用“switch”而不是“if-else if-else”重写前面的示例，可以最好地演示“switch”语句。

```
<%!          
int dayOfWeek = 3;          
String fortune;          
 %>          
<%          
// the decision variable here is the day chosen by the           
user switch (dayOfWeek)          
 {          

// first case          
 case 1:          
 fortune = "Adam met Eve and turned over a new leaf.";          
 break;          

// second case          
 case 2:          
 fortune = "Always go to other people's funerals, otherwise           
they won't come to yours.";          
 break;          

 case 3:          
 fortune = "An unbreakable toy is useful for breaking other toys.";          
 break;          

 case 4:          
 fortune = "Be alert - the world needs more lerts.";          
 break;          

 case 5:          
 fortune = "Crime doesn't pay, but the hours are good.";          
 break;          

// if none of them match...          
 default:          
 fortune = "Sorry, closed on the weekend";          
 break;          

 }          
// print output          
out.println(fortune);          
 %>
```

您将注意到的第一件事是，前一个示例中的“day”变量已经被转换为数字“dayOfWeek”变量——这是因为“switch”构造只在决策变量是整数时才起作用。

这里还有几个重要的关键字:“break”关键字用于跳出“switch”语句块并立即转到它后面的行，而“default”关键字用于在传递给“switch”的变量不满足块中列出的任何条件时执行一组默认的语句。

仅此而已。现在，您已经对 JSP 的条件语句有了足够的了解，可以开始编写简单的程序了——所以开始练习吧！下一期，我们将讨论循环，演示其他字符串对象方法，甚至快速浏览一下新的响应对象。

*注意:本文中的所有例子都已经在装有 Tomcat 3.2 和 JServ 1.1 的 Linux/i586 上进行了测试。这些示例仅用于说明，并不适用于生产环境。YMMV！*
版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### JSP 文件—第 3 部分:黑光和白兔

倒计时

上次，您学习了一些 JSP 中可用的各种条件语句和操作符。本周，我们将扩展这些基础知识，向您介绍 JSP 中可用的不同类型的循环，讨论更多的 String 对象方法，并快速浏览新的 Response 对象。

首先，循环。

您可能已经知道，“循环”是一种编程结构，它允许您反复执行一组语句，直到满足预定义的条件。

JSP 中最基本的循环是“while”循环，它看起来像这样:

```
while (condition)           
 {           
 do this!           
 }
```

或者，为了让概念更清晰，

```
while (temperature is below freezing)           
 {           
 wear a sweater           
 }
```

这里的“条件”是一个标准的条件表达式，其计算结果为 true 或 false。因此，如果我们用 JSP 编写上面的例子，它看起来会像这样:

```
while (temp <= 0)           
 {           
 sweater = true;           
 }
```

这里有一个例子:

```
<html>           
<head>           
</head>           
<body>           
<%!           
int countdown=30;           
 %>           
<%           
while (countdown > 0)           
{           
out.println(countdown + "&nbsp;");           
countdown--;           
}           
out.println("<b>Kaboom!</b>");           
 %>           
</body>           
</html>
```

这里，变量“countdown”被初始化为 30，并且使用“while”循环来递减变量的值，直到它达到 0。一旦变量的值为 0，条件表达式的计算结果为 false，并且执行循环后面的行。

以下是输出结果:

```
30 29 28 27 26 25 24 23 22 21 20 19 18 17 16 15 14 13            
12 11 10 9 8 7 6 5 4 3 2 1 Kaboom!
```

##### 使用循环做更多事情

“while”循环有一个注意事项。如果条件表达式第一次计算为 false，花括号中的代码将永远不会执行。如果这不是您想要的，看看“do-while”循环，它在您需要执行一组语句*至少*一次的情况下很方便。

它看起来是这样的:

```
do           
 {           
 do this!           
 } while (condition)
```

例如，以下代码行将不会生成任何输出，因为“while”循环中的条件表达式将始终计算为 false。

```
<%           
int bingo = 366;           

while (bingo == 699)           
 {           
 out.println ("Bingo!");           
 break;           
 }           
%>
```

然而,“do-while”循环的结构是这样的，循环中的语句首先执行，然后检查要测试的条件。使用“do-while”循环意味着花括号中的代码将至少执行一次——不管条件表达式的计算结果是否为真。

```
<%           
int bingo = 366;           

do           
 {           
 out.println ("Bingo!");           
 break;           
 } while (bingo == 699);           
%>
```

你自己试试，看看有什么不同。
版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 过去的结论

只要指定的条件表达式保持为真,“while”和“do-while”循环都会继续迭代。但是经常需要执行特定次数的特定语句集——例如，打印一系列 13 个连续数字，或者将特定的一组Repeat the cell five times. In this case, smart programmers will use the "for" loop …

“for”循环通常如下所示:

```
for (initial value of counter; condition; update counter)            
 {            
 do this!            
 }
```

看起来像胡言乱语？嗯，等一下……这里的“计数器”是一个 JSP 变量，它被初始化为一个数值，并记录循环执行的次数。在每次执行循环之前，测试“条件”——如果它评估为真，循环将再次执行，并且计数器将适当地递增；如果它的计算结果为 false，循环将被中断，后面的行将被执行。

这里有一个简单的例子来演示如何使用这个循环:

```
<html>            
<head>            
<basefont face="Arial">            
</head>            

<body>            
Turning The Tables, JSP-Style!            
<br>            
<%!            
// define the number            
int number = 7;            
int x;            
 %>            
<%            
// use a for loop to calculate tables for that number            
for (x=1; x<=15; x++)            
 {            
 out.println(number + " X " + x + " = " + (number*x) + "<br>");            
 }            
 %>            

</body>            
</html>
```

这是输出结果:

```
Turning The Tables, JSP-Style!            

7 X 1 = 7            
7 X 2 = 14            
7 X 3 = 21            
7 X 4 = 28            
7 X 5 = 35            
7 X 6 = 42            
7 X 7 = 49            
7 X 8 = 56            
7 X 9 = 63            
7 X 10 = 70            
7 X 11 = 77            
7 X 12 = 84            
7 X 13 = 91            
7 X 14 = 98            
7 X 15 = 105
```

让我们仔细分析一下:

一开始，定义了一个变量，包含用于乘法表的数字；我们这里用的是 7，你可能更喜欢用另一个数字。

接下来，构建了一个“`for`”循环，将“x”作为计数器变量。如果您看一下循环的第一行，您会看到“x”已经被初始化为 1，并且被设置为运行不超过 15 次。

最后用`println()`函数取指定的数，乘以计数器的当前值，在页面上显示结果。

##### 打破循环的声音

在处理循环时，有两个重要的关键词你应该知道:“break”和“continue”。

“break”关键字用于在遇到意外情况时退出循环。这方面的一个很好的例子是可怕的“被零除”错误——当一个数被另一个数除时(这个数一直在减少),建议检查除数，并使用“break”语句在它等于零时退出循环。

正如您已经看到的,“continue”关键字用于跳过循环的特定迭代，并立即转到下一个迭代——如下例所示:

```
<%            
int x;            
for (x=1; x<=10; x++)            
 {            
 if (x == 7)            
 {            
 continue;            
 }            
 else            
 {            
 out.println(x + "&nbsp;");            
 }            
 }            
 %>
```

在这种情况下，JSP 将打印一串从 1 到 10 的数字——然而，当它到达 7 时,“continue”语句将导致它跳过这个特定的迭代并返回到循环的顶部。所以你的数字串不会包括 7——自己试试看。
<font size = 1 color = " # aaaaaa " face = " Verdana，Arial，Helvetica" >版权<a href = " http://www . melon fire . com/">melon fire</a>，2000 年。保留所有权利。< /font >

##### 支付吹笛者

在本教程的第一部分中，您已经看到了 String 对象的一些功能。但是随着我们的深入，你需要知道更多一点来充分利用它的力量。

首先，有一个`indexOf()`方法，用于定位一个字符或子串在一个更大的字符串中的第一次出现。如果您运行下面的代码片段，

```
<%             
// define variable             
String storyName = "The Pied Piper Of Hamlin";             

// find index             
int i = storyName.indexOf("i");             

// print index             
out.println("The letter i first occurs at " + i + "              
in the string " + storyName); %>
```

这是您将看到的内容:

```
The letter i first occurs at 5 in the string The              
Pied Piper Of Hamlin
```

是的，第一个字符被视为索引 0，第二个被视为索引 1，依此类推。这些程序员…

与此相反的是 `lastIndexOf()` 函数，用于识别一个字符或子串在一个更大的字符串中的最后一次出现。看一看:

```
<%             
// define variable             
String storyName = "The Pied Piper Of Hamlin";             

// find index             
int i = storyName.lastIndexOf("Pi");             

// print index             
out.println("The string Pi last occurs at " + i + "              
in the string " + storyName); %>
```

输出是:

```
The string Pi last occurs at 9 in the string The              
Pied Piper Of Hamlin
```

如果没有找到字符或子串，该函数将返回一个错误代码-1。

##### 大声尖叫

接下来，当您需要删除字符串末尾的空白时，`trim()`函数就派上了用场。

```
<%             
// define variable             
String whatIWant = " gimme my space ";             

// trim!             
// returns "gimme my space"             
whatIWant.trim();             
 %>
```

`toUpperCase()` 和`toLowerCase()`方法可以方便地改变字符串的大小写。

```
<%             
// define variable             
String someString = "don't SCREam, help is oN the WAy!";             

// uppercase - returns "DON'T SCREAM, HELP IS ON THE WAY!"              
someString.toUpperCase();             

// lowercase - returns "don't scream, help is on the way!"              
someString.toLowerCase(); %> 
```

`startsWith()`和`endsWith()` 函数用于验证字符串是以指定的字符还是字符序列开始还是结束。下面的例子可以清楚地说明这一点。

```
<%             
// define variables             
String alpha = "black light";             
String beta = "white rabbit";             
String prefix = "bl";             
String suffix = "it";             

// check each string for prefixes and suffixes             

if (alpha.startsWith(prefix))             
{             
out.println("The string " + alpha + " starts with              
" + prefix + "<br>"); }             

if (beta.startsWith(prefix))             
{             
out.println("The string " + beta + " starts with              
" + prefix + "<br>"); }             

if (alpha.endsWith(suffix))             
{             
out.println("The string " + alpha + " ends with " +              
suffix + "<br>"); }             

if (beta.endsWith(suffix))             
{             
out.println("The string " + beta + " ends with " +              
suffix + "<br>"); }             

 %>
```

输出是:

```
The string black light starts with bl             
The string white rabbit ends with it
```

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 你说七，我说七

最后，如果你曾经想把字符串转换成数字，下面的例子会告诉你你需要知道的一切。

```
<%              
// converting a string to a number              

// define variables              
String someString = "97";              
int aRandomNumber = 3;              

// at this stage, someString is still treated as a string               
out.println(someString + " plus " + aRandomNumber               
+ " equals " + (someString+aRandomNumber) + "<p>");              

// now convert someString to a number              
int someNumber               
= Integer.parseInt(someString);              

// at this stage, someString has been converted to a               
number, stored in someNumber out.println(someNumber +               
" plus " + aRandomNumber + " equals " + (someNumber+aRandomNumber));              

 %>
```

这是输出。

```
97 plus 3 equals 973              
97 plus 3 equals 100
```

如果您更喜欢反过来做事情，下一个例子值得仔细考虑。

```
<%              
// define variables              
int someNumber = 97;              
int aRandomNumber = 3;              

// at this stage, someNumber is still treated as a number               
out.println(someNumber + " plus " + aRandomNumber + "               
equals " + (someNumber+aRandomNumber) + "<p>");              

// now convert someNumber to a string              
String someString = Integer.toString(someNumber);              

// at this stage, someNumber has been converted to a               
string, stored in someString out.println(someString +               
" plus " + aRandomNumber + " equals " + (someString+aRandomNumber));              

 %>
```

这是输出。

```
97 plus 3 equals 100              
97 plus 3 equals 973
```

结果如何？在 JSP 中进行字符串和数字数据类型之间的转换时，`parseInt()` 和 `toString()`是您最好的朋友。

##### 积极的回应

另一个有趣的对象，也是在最奇怪的地方派上用场的对象，是响应对象。与用于检索信息的请求对象(下一次回来了解更多)相反，响应对象通常用于向浏览器发送信息；这些信息可能包括 HTTP 头、重定向 URL、cookies 和各种其他项目。

由于响应对象是一个“隐式”对象(这样称呼是因为当您想要使用它时不需要显式地创建对象的实例)，您可以立即在 JSP 文档中使用它。下面的例子演示了使用它通过“Cache-Control”头关闭缓存。

```
<%              
response.setHeader("Cache-Control","no-cache");              
 %>              
[/code]              
You can also use it to redirect the client to another URL.              

 `%              

response.setHeader("Location", "error.html");              

 %>` 
您可以使用`sendRedirect()`方法来完成类似的事情——记住在这里使用绝对 URL。

```
<%              
response.sendRedirect("http://my.server.com/error.html");              
 %>
```

您可以使用`setContentType()`方法设置文档的 MIME 类型。

```
<%              
response.setContentType("image/jpeg");              
 %>
```

甚至用`addCookie()` 方法设置一个 cookie 随着本系列的深入，会有更多的介绍。

这就是我们所有的时间。下一次，我们将探索 JSP 响应对象，用于处理来自 HTML 表单的数据…所以你不会想错过这个的！

*注意:本文中的所有例子都已经在装有 Tomcat 3.2 和 JServ 1.1 的 Linux/i586 上进行了测试。这些示例仅用于说明，并不适用于生产环境。YMMV！* 
 版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### JSP 文件——第 4 部分:红色药丸

运用理论

上次，我们为您提供了 JSP 中众多可用控制结构的速成课程——操作符、条件语句和循环。但是在教室里学习理论和在现实世界中实践理论之间有很大的不同——这就是为什么这一期的 JSP 文件致力于讨论如何使用 JSP 从 HTML 表单中提取和使用数据。

在接下来的几页中，我们将向您展示如何使用 JSP 来处理输入到 Web 表单中的数据——从简单的文本框到列表和复选框——我们还将演示如何在 JSP 中使用数组变量。

##### 最后的动作英雄

HTML 表单通常用于获取网站访问者的信息，如他们的姓名、邮寄地址、电话号码等，然后这些信息会以各种不同的方式进行处理。一些网站将其存储在数据库中；别人发邮件给站长；还有一些人只是把它扔进垃圾桶。通过使用 JSP 处理表单，您可以编写简单的代码片段来完成所有这些操作。

让我们从一个简单的例子开始。

```
<html>               

<head>               
<basefont face="Arial">               
</head>               

<body>               

               
<form method="GET" action="matrix.jsp">               
<table cellspacing="5" cellpadding="5" border="0">               

<tr>               
<td>               
<font size="-1">Name, rank and serial, number, soldier!</font>                
</td> <td align="left"> <input type="text" name="name" size="10">                
</td> </tr>               

<tr>               
<td colspan="2" align="center">               
<input type="submit">               
</td>               
</tr>               

</table>               
</form>               

               
</body>               

</html>
```

整个页面中最关键的一行是`<form>`标签:

```
<form method="GET" action="matrix.jsp">               

...               

</form>
```

您可能已经知道，`<FORM>`标记的`ACTION`属性指定了服务器端脚本的名称——在本例中为“matrix . JSP”——它将处理输入到表单中的信息，而 METHOD 属性指定了传递信息的方式。

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 进入矩阵

一旦提交了表单，就会调用脚本“matrix.jsp”来解析表单中输入的日期。此时，脚本只是读取输入到表单中的姓名，并显示包含该姓名的消息；但是，稍后将根据输入的名称对其进行修改，以允许或拒绝访问。

```
<html>                
<head>                
<basefont face="Arial">                
</head>                

<body>                
                
<%                
// matrix.jsp                

// define the variables used in the scriptlet                
String fname;                

// assign values                
fname = request.getParameter("name");                

// print the details                
out.println("Welcome to The Matrix, " + fname + "!");                

%>                
                
</body>                
</html>
```

现在，如果您在表单中输入一些数据(比如“joe”)，您应该会看到以下内容:

```
Welcome to The Matrix, joe!
```

这里需要解释一下。和往常一样，第一步是定义将在整个脚本中使用的变量——在本例中是变量“`fname`”。

```
<%                
// define the variables used in the scriptlet                
String fname;                
%>
```

接下来，必须将表单变量“`name`”的值赋给 JSP 变量“`fname`”——这是通过`getParameter()`方法完成的，该方法接受变量名作为参数并返回变量值。

`getParameter()`方法实际上属于一个叫做 Request 对象的 JSP 对象；与 JSP 中的许多其他对象不同，请求对象是一个“隐式”对象，这样称呼是因为当您想要使用它时，不需要显式地创建对象的实例。`getParameter()`方法只是该对象中可用的许多方法之一，在本教程中，我们还将探索其他一些方法。

一旦表单变量的值被赋给 JSP 变量，就可以像对待其他 JSP 变量一样对待它。在上面的例子中，一个`println()`函数调用负责打印欢迎字符串，其中包含名称。

您还可以使用`POST`方法(它提供了更高的安全性和可靠性)来处理表单数据——只需修改 HTML 表单，使用的方法就是`POST`。

```
<form method="POST" action="matrix.jsp">                

...                

</form>
```

脚本“matrix.jsp”将继续像广告中宣传的那样运行，不需要任何更改。因此,`getParameters()`方法可以用来访问表单变量，而不管提交数据的方法是什么。

您还可以添加一个简单的条件语句来拒绝除最受欢迎者之外的所有人的访问:

```
<html>                
<head>                
<basefont face="Arial">                
</head>                

<body>                
                
<%                
// matrix.jsp                

// define the variables used in the scriptlet                
String fname;                

// assign values                
fname = request.getParameter("name");                

// print the details                
if (fname.equals("neo"))                
{                
out.println("Welcome to The Matrix, Neo!");                
}                
else                
{                
out.println("Leave immediately, Agent!");                
}                
%>                
                
</body>                
</html>
```

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 要求更多

请求对象还附带了许多其他有用的方法——下面的例子演示了其中的一些方法

```
<html>                 
<head>                 
<basefont face="Arial">                 
</head>                 
<body>                 

<table border="1" cellspacing="5" cellpadding="5">                 
<tr>                 
<td><b>Variable</b></td>                 
<td><b>Value</b></td>                 
</tr>                 

<tr>                 
<td>Request protocol</td>                 
<td>                 
<%                 
// protocol                 
out.println(request.getProtocol());                 
%>                 
</td>                 
</tr>                 

<tr>                 
<td>Hostname</td>                 
<td>                 
<%                 
// server name                 
out.println(request.getServerName());                 
%>                 
</td>                 
</tr>                 

<tr>                 
<td>Port</td>                 
<td>                 
<%                 
// server port                 
out.println(request.getServerPort());                 
%>                 
</td>                 
</tr>                 

<tr>                 
<td>Remote username</td>                 
<td>                 
<%                 
// username if using HTTP authentication                 
// null if no authentication out.println(request.getRemoteUser());                 
%>                 
</td>                 
</tr>                 

<tr>                 
<td>Remote address</td>                 
<td>                 
<%                 
// get IP address of client out.println(request.getRemoteAddr());                 
%>                 
</td>                 
</tr>                 

<tr>                 
<td>Client browser</td>                 
<td>                 
<%                 
// client browser identification out.println                 
(request.getHeader("User-Agent"));                 
%>                 
</td>                 
</tr>                 

</table>                 
</body>                 
</html>
```

当您在浏览器中查看该文件时，您可能会看到如下内容:

```
Variable     Value                 
Request protocol     HTTP/1.0                 
Hostname     localhost                 
Port       80                 
Remote username   null                 
Remote address     192.168.0.143                 
Client browser     Mozilla/4.0 (compatible; MSIE 5.5; Windows 95)
```

如果您需要根据远程变量做出决策，所有这些变量都会派上用场，如下例所示:

```
<%                 
String browser = request.getHeader("User-Agent");                 
if(browser.indexOf("MSIE") >= 0)                 
{                 
  // IE-specific code                 
}                 
else if(browser.indexOf("Mozilla") >= 0)                 
{                 
  // Mozilla-specific code                 
}                 
else                 
{                 
  // any other browser                 
}                 
%>
```

注意我们对`indexOf()`方法的使用——您可能在本系列的前几篇文章中还记得这一点。

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 服用一些药物

正如您可以从文本字段中访问数据一样，您也可以使用
 `getParameter()`方法来评估单选按钮和列表框的状态。假设您将上面的表单修改为稍微复杂一点的形式。

```
<html>                  

<head>                  
<basefont face="Arial">                  
</head>                  

<body>                  

                  
<form method="GET" action="pills.jsp">                  

<font size="-1" face="Arial">                  
Gimme                  
<select name="quantity">                  
<option value="10">10</option>                  
<option value="25">25</option>                  
<option value="50">50</option>                  
<option value="100">100</option>                  
</select>                  
of those little                  
<input type="Radio" name="colour" value="red" checked>red                   
<input type="Radio" name="colour" value="blue">blue                    
pills, willya? </font>                  

<p>                  

<input type="Submit">                  
</form>                  

                  
</body>                  

</html>
```

这是“pills.jsp”。

```
<html>                  
<head>                  
<basefont face="Arial">                  
</head>                  

<body>                  
                  
<%                  
// pills.jsp                  

// define the variables and assign values                  
String colour = request.getParameter("colour");                  
String quantity_string = request.getParameter("quantity");                  

// convert string to number                  
int quantity = Integer.parseInt(quantity_string);                  

// process and display                  

// who needs green pills?                  
if (colour.equals("blue"))                  
{                  
out.println("Sorry, we don't carry blue pills here.");                  
}                  
else                  
{                  
  if (quantity >= 50)                  
  {                  
  out.println("Stocking up, are we?");                  
  }                  
  else                  
  {                  
  out.println("Here you go. And would you like fries with that?");                  
  }                  
}                  
%>                  
                  
</body>                  
</html>
```

根据药丸类型和数量的组合，将显示适当的信息。

如您所见，评估单选按钮和列表框几乎与评估常规文本字段完全相同。

在转到其他表单构造——即复选框和多选列表框——之前，您需要理解一种新的变量类型:JSP 数组。

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 甜点是什么？

到目前为止，您使用的变量只包含一个值，例如，

```
int i = 0
```

然而，数组变量完全是另一回事。数组变量最好被认为是一个“容器”变量，它可以包含一个或多个值。举个例子，

```
String[] desserts = {"chocolate mousse", "tiramisu",                    
"apple pie", "chocolate fudge cake"};
```

这里，“甜点”是一个数组变量，它包含值“巧克力慕斯”、“提拉米苏”、“苹果派”和“巧克力软糖蛋糕”。

数组变量对于将相关的值组合在一起特别有用——名字、日期、前女友的电话号码等等。

数组的各个元素通过索引号访问，第一个元素从零开始。因此，要访问元素

```
"chocolate mousse"
```

你可以用这个符号

```
desserts[0]
```

在…期间

```
"chocolate fudge cake"
```

会是

```
desserts[3]
```

–本质上，数组变量名后跟用方括号括起来的索引号。极客们将此称为“零基索引”。

与 PHP 和 Perl 等语言相比，在 JSP 中定义数组变量有些复杂；您需要首先声明变量及其类型，然后实际创建数组。

```
<%                   
// declare type of array                   
String[] desserts;                   

// initialize array                   
// the number indicates the number of elements the array will                    
hold desserts = new String[4];                   

// assign values to array elements                   
desserts[0] = "chocolate mousse";                   
desserts[1] = "tiramisu";                   
desserts[2] = "apple pie";                   
desserts[3] = "chocolate fudge cake";                   
%>
```

或者您可以使用更简单的版本:

```
<%                   
String[] desserts = {"chocolate mousse", "tiramisu",                    
"apple pie", "chocolate fudge cake"}; %> 
```

注意，如果您试图添加比创建数组时指定的数量更多的元素，JSP 将会抛出一系列奇怪的错误消息。

##### 巧克力瘾

为了修改数组中的一个元素，只需给相应的变量赋一个新值。如果你想用“巧克力曲奇”代替“巧克力软糖蛋糕”，你只需使用

```
<%                   
desserts[3] = "chocolate chip cookies";                   
%>
```

该数组现在将显示为

```
String[] desserts = {"chocolate mousse", "tiramisu",                    
"apple pie", "chocolate chip cookies"};
```

JSP 数组只能存储在声明数组变量时指定的数据类型；字符串数组不能保存数字，反之亦然。

最后，您可以使用“length”属性获取数组中的项数，下面的示例演示了这一点:

```
<html>                   
<head>                   
<basefont face="Arial">                   
</head>                   

<body>                   
<%                   
// define an array                   
String[] desserts = {"chocolate mousse", "tiramisu",                    
"apple pie", "chocolate fudge cake"};                   

// display array length                   
out.println("The dessert menu                    
contains " + desserts.length + " items.");  %>  </body> </html>
```

输出是:

```
The dessert menu contains 4 items.
```

您可以使用“`for`”循环来遍历数组的元素，如下例所示。

```
<html>                   
<head>                   
<basefont face="Arial">                   
</head>                   

<body>                   
Desserts available:                   
<ul>                   

<%                   
// define counter                   
int counter;                   

// define an array                   
String[] desserts = {"chocolate mousse", "tiramisu",                    
"apple pie", "chocolate fudge cake"};                   

for (counter=0; counter<desserts.length; counter++)                   
{                   
out.println("<li>" + desserts[counter] + "<br>");                   
}                   
 %>                   

</ul>                   
</body>                   
</html>
```

输出是:

```
Desserts available:                   
Â· chocolate mousse                   
Â· tiramisu                   
Â· apple pie                   
Â· chocolate fudge cake
```

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 整天宅在家看电视或者打电视游戏的人

当处理像复选框和多选列表框这样的表单元素时，数组特别有用。看一下下面的表单，它包括一堆复选框:

```
<html>                    
<head>                    
<basefont face="Arial">                    
</head>                    
<body>                    

Pick your favourite shows:                    
<br>                    
<form action="potato.jsp" method="POST">                    
<input type="checkbox" name="shows" value="Ally McBeal">Ally                     
McBeal <input type="checkbox" name="shows" value="Buffy The                     
Vampire Slayer">Buffy The Vampire Slayer <input type="checkbox"                     
name="shows" value="The Practice">The Practice <input                     
type="checkbox" name="shows" value="Sex And The City">Sex                     
And The City <input type="checkbox" name="shows"                     
value="The Sopranos">The Sopranos <input type="checkbox"                     
name="shows" value="Survivor">Survivor <br> <input type="submit"                     
name="submit" value="Select"> </form>                    

</body>                    
</html>
```

现在，一旦表单被提交，JSP 脚本“potato.jsp”就负责处理各种复选框的状态。复选框中的数据被分配给一个数组，然后该数组用于重新创建所选项目的列表。看一看。

```
<html>                    
<head>                    
<basefont face="Arial">                    
</head>                    
<body>                    

So your favourite shows are:                    
<br>                    
<%                    

// potato.jsp - process list of selected TV shows                    

// define variables                    
String[] Shows;                    

// assign values to variables                    
Shows = request.getParameterValues("shows");                    

out.println("<ul>");                    

// print by iterating through array                    
for(int counter = 0; counter < Shows.length; counter++)                    
{                    
  out.println("<li>" + Shows[counter]);                    
}                    

out.println("</ul>");                    
%>                    

</body>                    
</html>
```

如您所见，首先要做的是创建一个数组来保存复选框数据——在本例中，数组“显示”。然后，请求对象用于通过
 `getParameterValues()`方法(类似于`getParameter()`方法，但是返回一个值列表，而不是单个值)获得所选项目的值，然后这些值被分配给数组`Shows`。然后使用一个“`for`”循环来创建所选项目的列表。

这种技术也可以用于多个选择列表框——这是同一个例子，重写后使用了一个列表框而不是一系列复选框。

```
<html>                    
<head>                    
<basefont face="Arial">                    
</head>                    
<body>                    

Pick your favourite shows:                    
<br>                    
<form action="potato.jsp" method="POST">                    
<select name="shows" multiple>                    
  <option>Ally McBeal</option>                    
     <option>Buffy The Vampire Slayer</option>                    
     <option>The Practice</option>                    
     <option>Sex And The City</option>                    
     <option>The Sopranos</option>                    
     <option>Survivor</option>                    
</select>                    
<br>                    
<input type="submit" name="submit" value="Select">                    
</form>                    

</body>                    
</html>
```

显然，服务器端脚本“potato.jsp”完全不需要修改。

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 击败它，让它屈服

您会注意到，在我们到目前为止展示的所有示例中，我们使用了两个页面——一个包含表单的 HTML 页面和一个处理表单输入并生成适当输出的单独 JSP 脚本。然而，JSP 提供了一种优雅的方法，通过表单的 SUBMIT 按钮将这两个页面合并成一个页面。

您已经看到，一旦表单被提交给 JSP 脚本，所有的表单变量对 JSP 都是可用的。现在，除了用户定义的变量，每次点击表单上的提交按钮，都会创建一个名为“`submit`”的变量。通过测试这个变量是否存在，聪明的 JSP 开发人员可以使用一个 JSP 文档来生成初始表单和提交后的输出。

下面的代码片段演示了如何使用这种技术重写上面的“欢迎使用矩阵”示例。

```
<html>                     
<head>                     
<basefont face="Arial">                     
</head>                     

<body>                     
                     
<%                     
// matrix.jsp                     

// check for submit variable                     
String submit = request.getParameter("submit");                     

if(submit != null)                     
{                     
// form has been submitted, display result                     

// define the variables used in the scriptlet                     
String fname;                     
   f                  
// assign values                     
fname = request.getParameter("name");                     

// print the details                     
out.println("Welcome to The Matrix, " + fname + "!");                     

}                     
else                     
{                     
// display initial form                     
%>                     

<form method="GET" action="matrix.jsp">                     
<table cellspacing="5" cellpadding="5" border="0">                     

<tr>                     
<td>                     
<font size="-1">Name, rank and serial, number, soldier!</font>                      
</td> <td align="left"> <input type="text" name="name" size="10">                      
</td> </tr>                     

<tr>                     
<td colspan="2" align="center">                     
<input name="submit" type="submit">                     
</td>                     
</tr>                     

</table>                     
</form>                     

<%                     
}                     
%>                     
                     
</body>                     
</html>
```

如您所见，该脚本首先测试是否存在“`submit`”变量——如果没有找到，它认为表单尚未提交，因此显示初始表单。

由于`<FORM>`标签的`ACTION`属性引用了相同的 JSP 脚本，一旦表单被提交，将调用相同的脚本来处理表单输入。然而，这一次“`submit`”变量将会存在，因此 JSP 将不会显示初始页面，而是显示结果页面。

请注意，要实现这一点，您的

```
<input type="submit">
```

必须具有值为“`submit`”的`NAME`属性，如下所示:

```
<input type="submit" name="submit">
```

仅此而已。下一次，我们将把 JSP 连接到一个数据库，以便生成动态网页——所以不要错过这一次。在那之前…保持健康！

*注意:本文中的所有例子都已经在装有 Tomcat 3.2 和 JServ 1.1 的 Linux/i586 上进行了测试。这些示例仅用于说明，并不适用于生产环境。YMMV！* 
 版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### JSP 文件–第 5 部分:没有转发地址

工具箱

JSP 提供了许多优于其他服务器端脚本语言的优势——正如您已经看到的，性能只是其中之一。当您将这一性能优势与另一个重要优势(无缝连接到各种数据库服务器的能力)结合起来时，它就变得尤为重要。

通过提供无缝的数据库连接(好吧，它不像 PHP 中可用的数据库连接那样透明，但它仍然很好！)结合更快的响应时间，JSP 允许开发人员构建复杂的、可伸缩的、数据驱动的 Web 应用程序，同时享受较短的开发周期。

“好吧，”努夫说。让我们开门见山吧。

在本文中，我们将演示如何使用 JSP 连接到数据库，从中提取数据，并使用这些数据构建动态网页。我们将构建一个简单的 Web 应用程序，以便让这个过程更加清晰；这也有助于您量化与您可能熟悉的其他服务器端脚本语言相比，JSP 的易用性(或易用性)有多强。

如果你打算尝试下面的例子(推荐)，你需要下载并安装 mySQL 数据库服务器，可以在[http://www.mysql.com/](http://www.mysql.com/)找到。mySQL 是一个快速、可靠的开源数据库管理系统，它以令人感动的价格提供了相当多的功能——它是免费的！

我们假设您已经安装并配置了 mySQL，并且拥有创建和编辑数据库表的适当权限。

由于 Java 中的所有数据库交互都是使用一种称为 JDBC(Java Database Connectivity，Java 数据库连接)的技术进行的，所以您还需要一个允许您连接到 mySQL 数据库服务器的 JDBC 模块。我们假设你已经从[“拼凑一个 JSP 开发环境”](http://www.worldserver.com/mm.mysql//#enl#/, and configured it to talk to your mySQL database, as described in the article /#nl#/http://www.melonfire.com/community/columns/trog/article.php?id=48)下载了 mm.mySQL JDBC 模块。

如果您使用的是 mySQL 以外的数据库，不用担心——JSP 支持所有主流数据库，您也可以使用接下来几页中描述的技术与其他数据库进行对话。您可能需要查阅数据库供应商的手册或网站，了解如何获得必要的软件。

最后，一些 SQL 知识会派上用场。如果你不知道 SQL，不要担心——它非常简单，几分钟的[“说 SQL”教程](http://www.melonfire.com/community/columns/trog/article.php?id=39)就能让你像专家一样执行查询。

所有这些都过去了，让我们实际上把手弄脏吧。

##### 甩了！

如果您熟悉 SQL，您会知道数据库有四种基本的操作类型:

*   选择一条记录；

*   插入记录；

*   更新记录；

*   删除记录。

为了演示这些操作，我们将构建一个小应用程序，它需要上面列出的每个功能——一个允许多个用户在线存储和查看联系信息的地址簿。

和往常一样，在设计数据驱动的应用程序时，首先要考虑的事情之一是数据库的设计(废话！).对于这个应用程序，我们决定使用一个名为“abook”的表，其中包含不同类型联系信息的字段——地址、电话、传真、电子邮件地址等等。系统中的每个用户都有一个唯一的登录 id，数据库中的每个记录都由一个特定的用户“拥有”。

我们创建了一个“转储文件”,可以让您快速创建数据库表和初始记录集——我们建议您将这些数据导入 mySQL 数据库服务器，因为我们将在本文中使用它们。

要导入数据，请下载转储文件，并在 mySQL 提示符下使用以下命令:

```
mysql> mysql -u username -p database < dumpfile
```

或者您可以手动插入内容，以下是您需要的内容:

```
#                      
# Table structure for table 'abook'                      
#                      

DROP TABLE IF EXISTS abook;                      
CREATE TABLE abook (                      
   id int(11) unsigned NOT NULL auto_increment,                      
   uid varchar(255) NOT NULL,                      
   fname varchar(255) NOT NULL,                      
   lname varchar(255) NOT NULL,                      
   tel varchar(255),                      
   fax varchar(255),                      
   email varchar(255),                      
   addr text,                      
   company varchar(255),                      
   comment text,                      
   PRIMARY KEY (id)                      
);                      

#                      
# Dumping data for table 'abook'                      
#                      

INSERT INTO abook (id, uid, fname, lname, tel, fax, email,                       
addr, company,comment) VALUES ( '1', 'john', 'Bugs',                       
'Bunny', '7376222', '', 'bugs@somedomain.com', 'The Rabbit                       
Hole, Dark Woods, Somewhere On Planet Earth', '',                       
'Big-ears in da house!'); INSERT INTO abook (id, uid,                       
fname, lname, tel, fax, email, addr, company,                      
comment) VALUES ( '2', 'john', 'Elmer', 'Fudd', '', '7628739',                       
'fuddman@somedomain.com','', '', ''); INSERT INTO abook (id,                       
uid, fname, lname, tel, fax, email, addr, company,comment)                       
VALUES ( '3', 'joe', 'Peter', 'Parker', '162627 x34', '',                       
'webcrawler@somedomain.com', 'Your Friendly Neighbourhood                       
Newspaper', '', 'My spidey-sense is tingling!');                       
INSERT INTO abook (id, uid, fname, lname, tel, fax, email,                      
addr, company, comment) VALUES ( '4', 'bill',                       
'Clark', 'Kent', '1-800-SUPERMAN', '',                       
'superdude@somedomain.com', '', '', 'Is it a bird? Is                       
it a plane?');
```

这将创建一个名为“`abook`”的表，其中包含不同类型联系人信息的列；这些记录由三个虚构的用户拥有，“比尔”、“约翰”和“乔”。

现在检查数据是否已经通过`SELECT`查询成功导入(SELECT SQL 语句用于从数据库中检索信息)。在 mySQL 命令提示符下输入以下内容:

```
mysql> select uid, fname, lname from abook;
```

在英语中，它的意思是“显示地址簿中的 uid、fname 和 lname 列”。下面是你应该看到的:
 `+------+-------+--------+

| uid  | fname | lname  |

+------+-------+--------+

| john | Bugs  | Bunny  |

| john | Elmer | Fudd   |

| joe  | Peter | Parker |

| bill | Clark | Kent   |

+------+-------+--------+

4 rows in set (0.00 sec)` 
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 风景优美的路线

都在工作？很好。现在，让我们使用 JSP 来做完全相同的事情——在数据库中启动一个`SELECT`查询，并在一个 HTML 页面中显示结果。

```
<html>                       
<head>                       
<basefont face="Arial">                       
</head>                       
<body>                       
<%@ page language="java" import="java.sql.*" %>                       

<%!                       
// define variables                       
String UId;                       
String FName;                       
String LName;                       

// define database parameters                       
String host="localhost";                       
String user="us867";                       
String pass="jsf84d";                       
String db="db876";                       
String conn;                       
%>                       

<table border="2" cellspacing="2" cellpadding="5">                       

<tr>                       
<td><b>Owner</b></td>                       
<td><b>First name</b></td>                       
<td><b>Last name</b></td>                       
</tr>                       

<%                       

Class.forName("org.gjt.mm.mysql.Driver");                       

// create connection string                       
conn = "jdbc:mysql://" + host + "/" + db + "?user=" + user                        
+ "&password=" + pass;                       

// pass database parameters to JDBC driver                       
Connection Conn = DriverManager.getConnection(conn);                       

// query statement                       
Statement SQLStatement = Conn.createStatement();                       

// generate query                       
String Query = "SELECT uid, fname, lname FROM abook";                       

// get result                       
ResultSet SQLResult = SQLStatement.executeQuery(Query);                       

  while(SQLResult.next())                       
  {                       
    UId = SQLResult.getString("uid");                       
    FName = SQLResult.getString("fname");                       
    LName = SQLResult.getString("lname");                       

    out.println("<tr><td>" + UId + "</td><td>" +                        
FName + "</td><td>" + LName + "</td></tr>");                       
  }                       

// close connection                       
SQLResult.close();                       
SQLStatement.close();                       
Conn.close();                       

%>                       

</table>                       
</body>                       
</html>
```

而你会看到这样的东西:
 `

Owner   First name   Last name

john   Bugs     Bunny

john   Elmer     Fudd

joe   Peter     Parker

bill   Clark     Kent` 
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 一步一步来

使用 JSP 从数据库中提取数据包括几个步骤。让我们解剖每一个。

 **1。**首先，我们需要确保 JDBC 连接所需的所有模块对于 JSP 文档都是可用的。这是通过

```
<%@ page                        
...                        
%>
```

指令，用于定义影响 JSP 文档的属性。

```
<%@ page language="java" import="java.sql.*" %>
```

“`import`”属性用于导入脚本执行所需的所有包和类——这里是“`java.sql.*`树中的所有包。

 **2。**接下来，需要声明这个 scriptlet 需要的所有变量；我们保留了一些 SQL 查询的结果，还创建了一些变量来保存特定于数据库的信息，比如数据库服务器的名称、获得访问权限所需的用户名和密码，以及用于所有查询的数据库。该信息用于在稍后阶段构建连接字符串。

 **3。**下一步是加载访问 mySQL 数据库所需的 JDBC 驱动程序——这是通过以下语句完成的

```
Class.forName("org.gjt.mm.mysql.Driver");
```

用于特定数据库的驱动程序的名称总是可以从驱动程序附带的文档中获得。

 **4。**现在驱动程序已经加载完毕，是时候打开到数据库服务器的连接了。这是通过连接对象及其`getConnection()`方法实现的。

`getConnection()`方法需要一个连接字符串作为参数；这个连接字符串是通过将服务器名称、用户名和密码以及要使用的数据库名称组合成一个类似 URL 的字符串来创建的。

```
// create connection string                        
conn = "jdbc:mysql://" + host + "/" + db + "?user=" + user                         
+ "&password=" + pass;                        

// pass database parameters to JDBC driver                        
Connection Conn = DriverManager.getConnection(conn);
```

然后，`getConnect()`方法返回一个连接标识符，用于后续的 SQL 查询。JSP 和数据库服务器之间的所有通信都通过这个连接进行。在这种情况下，`Connection`对象的具体实例被称为“`Conn`”。

 **5。**一旦到数据库的连接可用，`Statement`对象就被用来准备 SQL 语句以供执行。

```
// query statement                        
Statement SQLStatement = Conn.createStatement();
```

 **6。**此时，创建了一个查询:

```
// generate query                        
String Query = "SELECT uid, fname, lname FROM abook";
```

而`ResultSet`对象用于存储查询的结果。

```
// get result                        
ResultSet SQLResult = SQLStatement.executeQuery(Query);
```

 **7。**一旦执行了查询并返回了结果，就可以使用许多方法来遍历结果集。上面的例子使用了`next()`方法，它只是在查询返回的记录列表中前进。一个“`while`”循环用于结合`next()`方法遍历结果集。

```
// get and display each record                        
  while(SQLResult.next())                        
  {                        
    UId = SQLResult.getString("uid");                        
    FName = SQLResult.getString("fname");                        
    LName = SQLResult.getString("lname");                        

    out.println("<tr><td>" + UId + "</td><td>" +                         
FName + "</td><td>" + LName + "</td></tr>");                        
  }
```

顺便提一下，`ResultSet`对象还附带了一个方便的`prev()`方法，它允许您显示前面的记录。

`getString()`方法用于访问当前正在检查的记录中的特定列；这些值作为字符串存储在 JSP 文档中。除了`getString()`方法之外，您还可以使用`getInt()`、`getTimeStamp()`和`getBoolean()`方法来获取列值作为特定的变量类型。

 **8。**最后，查询后返回的每个结果集都会占用一定数量的内存——如果您的系统可能会经历沉重的负载，那么使用各种`close()`方法来释放内存是个好主意。

```
// close connection                        
SQLResult.close();                        
SQLStatement.close();                        
Conn.close();
```

如您所见，通过 JSP 连接到数据库比 PHP 中的等效过程稍微复杂一些。对此你无能为力，只能逆来顺受。
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 你叫什么名字？

现在您已经知道如何连接到数据库，让我们开始开发地址簿应用程序的基本框架。第一个脚本要求输入用户名，然后连接到数据库以显示该用户拥有的条目。

我们将在整个操作中使用一个单独的页面——“submit”变量(您还记得这个技术吧？)用于决定是显示初始表单还是结果页面。看一看:

```
<html>                         
<head>                         
<basefont face="Arial">                         
</head>                         

<body>                         
                         
<%                         
// check submit state                         
String submit = request.getParameter("submit");                         

// form not yet submitted                         
// display initial page                         
if(submit == null)                         
{                         
%>                         

<form action="view.jsp" method="GET">                         
Enter your name:&nbsp;                         
<input type="text" name="name" size="10"> &nbsp; <input                          
type="submit" name="submit" value="Go"> </form>                         

<%                         
}                         
// form submitted, display result                         
else                         
{                         
%>                         

<%@ page language="java" import="java.sql.*" %>                         

<%                         
// get username                         
String uid = request.getParameter("name");                         

// define database parameters                         
String host="localhost";                         
String user="us867";                         
String pass="jsf84d";                         
String db="db876";                         
String conn;                         
%>                         

<h2><% out.println(uid); %>'s Little Black Book</h2>                         
<hr>                         

<table border=1 cellspacing=4 cellpadding=4>                         
<tr>                         
<td><b>First name</b></td>                         
<td><b>Last name</b></td>                         
<td><b>Tel</b></td>                         
<td><b>Fax</b></td>                         
<td><b>Email address</b></td>                         
</tr>                         

<%                         
Class.forName("org.gjt.mm.mysql.Driver");                         

// create connection string                         
conn = "jdbc:mysql://" + host + "/" + db + "?user=" + user                          
+ "&password=" + pass;                         

// pass database parameters to JDBC driver                         
Connection Conn = DriverManager.getConnection(conn);                         

// query statement                         
Statement SQLStatement = Conn.createStatement();                         

// generate query                         
String Query = "SELECT * FROM abook WHERE uid = '" + uid + "'";                         

// get result                         
ResultSet SQLResult = SQLStatement.executeQuery(Query);                         

// display records                         
// if available                         
  while(SQLResult.next())                         
  {                         
    String FName = SQLResult.getString("fname");                         
    String LName = SQLResult.getString("lname");                         
    String Tel = SQLResult.getString("tel");                         
    String Fax = SQLResult.getString("fax");                         
    String Email = SQLResult.getString("email");                         

    out.println("<tr><td>" + FName + "</td><td>" +                          
LName + "</td><td>" + Tel + "</td><td>" + Fax + "</td><td>" +                          
Email + "</td></tr>");                         
  }                         
// close connections                         
SQLResult.close();                         
SQLStatement.close();                         
Conn.close();                         

}                         
 %>                         

</table>                         
                         
</body>                         
</html>
```

正如您所看到的，通过检查"`submit`"变量的值，我们已经成功地将初始页面和结果页面合并到一个 JSP 脚本中。这个脚本只接受一个用户名，连接到数据库，并显示该用户的记录(假设存在)。以“bill”、“joe”或“john”的身份登录，查看该用户可用的记录。

如果您不喜欢单词“null”显示在没有数据的列中，您可以添加几个“`if`”循环来用空格替换它。

```
<%                         
if (Fax.equals("null"))                         
{                         
Fax = "&nbsp;";                         
}                         
%> Copyright [Melonfire](http://www.melonfire.com/), 2000\. All rights reserved.
```

##### 新朋友

到目前为止，我们只是使用`SELECT`查询从数据库中提取信息。但是放点东西进去怎么样？

SQL 爱好者知道这是通过`INSERT`查询实现的。因此，议程上的下一项涉及向地址簿添加新条目。下面是我们将使用的表格:

```
<html>                          
<head>                          
<basefont face="Arial">                          
</head>                          

<body>                          
                          
<h2>Add Address Book Entry</h2>                          

<table border=0 cellspacing=5 cellpadding=5>                          
<form action="add_res.jsp" method="POST">                          

<tr>                          
<td><b>Username</b></td>                          
<td>                          
<select name="uid">                          
<!-- generate list of available usernames from database -->                          
<%@ page language="java" import="java.sql.*" %>                          
<%                          

  // database parameters                          
  String host="localhost";                          
String user="us867";                          
String pass="jsf84d";                          
String db="db876";                          
  String connString;                          

  // load driver                          
  Class.forName("org.gjt.mm.mysql.Driver");                          

  // create connection string                          
  connString = "jdbc:mysql://" + host + "/" + db + "?user="                           
        + user + "&password=" +  pass;                          

  // pass database parameters to JDBC driver                          
  Connection Conn = DriverManager.getConnection(connString);                          

  // query statement                          
  Statement SQLStatement = Conn.createStatement();                          

  // generate query                          
  String Query = "SELECT DISTINCT uid FROM abook";                          

  // get result                          
  ResultSet SQLResult = SQLStatement.executeQuery(Query);                          

  // get and display each record                          
    while(SQLResult.next())                          
    {                          
      String UId = SQLResult.getString("uid");                          

      out.println("<option>" + UId);                          
    }                          

  // close connections                          
  SQLResult.close();                          
  SQLStatement.close();                          
  Conn.close();                          

  %>                          

</select>                          
</td>                          
</tr>                          

<tr>                          
<td>First name</td>                          
<td><input type="Text" name="fname" size="15"></td>                          
</tr>                          

<tr>                          
<td>Last name</td>                          
<td><input type="Text" name="lname" size="15"></td>                          
</tr>                          

<tr>                          
<td>Address</td>                          
<td><textarea name="address"></textarea></td>                          
</tr>                          

<tr>                          
<td>Tel</td>                          
<td><input type="Text" name="tel" size="10"></td>                          
</tr>                          

<tr>                          
<td>Fax</td>                          
<td><input type="Text" name="fax" size="10"></td>                          
</tr>                          

<tr>                          
<td>Email address</td>                          
<td><input type="Text" name="email" size="10"></td>                          
</tr>                          

<tr>                          
<td>Company</td>                          
<td><input type="Text" name="company" size="25"></td>                          
</tr>                          

<tr>                          
<td>Comment</td>                          
<td><input type="Text" name="comment" size="25"></td>                          
</tr>                          

<tr>                          
<td colspan=2><input type="submit" name="submit"                           
value="Add"></td> </tr>                          

</form>                          
</table>                          

                          
</body>                          
</html>
```

如果仔细观察，您会发现该表单执行一个查询来检索系统中当前可用的用户列表，并使用该数据生成一个包含不同用户名的列表框。这使得在插入记录时可以指定每个记录的所有者。

一旦表单被填写并提交，控制权就传递给“add_res.jsp ”,它负责实际执行`INSERT`操作。看一看。

```
<html>                          
<head>                          
<basefont face="Arial">                          
</head>                          
<body>                          
                          

<%@ page language="java" import="java.sql.*" %>                          

<%                          
// add_res.jsp                          

// form data                          
String uid = request.getParameter("uid");                          
String fname = request.getParameter("fname");                          
String lname = request.getParameter("lname");                          
String address = request.getParameter("address");                          
String tel = request.getParameter("tel");                          
String fax = request.getParameter("fax");                          
String email = request.getParameter("email");                          
String company = request.getParameter("company");                          
String comment = request.getParameter("comment");                          

// database parameters                          
String host="localhost";                          
String user="us867";                          
String pass="jsf84d";                          
String db="db876";                          
String conn;                          

Class.forName("org.gjt.mm.mysql.Driver");                          

// create connection string                          
conn = "jdbc:mysql://" + host + "/" + db + "?user=" + user          f                 
+ "&password=" + pass;                          

// pass database parameters to JDBC driver                          
Connection Conn = DriverManager.getConnection(conn);                          

// query statement                          
Statement SQLStatement = Conn.createStatement();                          

// generate query                          
String Query = "INSERT INTO abook (id, uid, fname, lname,                           
tel, fax, email, addr, company, comment) VALUES (NULL, '" +                           
uid + "', '" + fname + "', '" + lname + "', '" + tel                           
+ "', '" + fax + "', '" + email + "', '" + address + "',                           
'" + company + "', '" + comment + "')";                          

// get result code                          
int SQLStatus = SQLStatement.executeUpdate(Query);                          

  if(SQLStatus != 0)                          
  {                          
  out.println("Entry succesfully added.");                          
  }                          
  else                          
  {                          
  out.println("Error! Please try again.");                          
  }                          

// close connection                          
SQLStatement.close();                          
Conn.close();                          

%>                          

                          
</body>                          
</html>
```

这个例子演示了`Statement`对象的另一个方法，即用于`INSERT`或`UPDATE`操作的`executeUpdate()`方法。该方法返回一个结果代码，指示受操作影响的行数——在上面的例子中，该结果代码应该是 1。万一不是…你就有麻烦了！

这里应该注意的是，如果您的`INSERT`语句包含需要转义的特殊字符(比如逗号或单引号)，您最好使用`PreparedStatement`类，它会自动处理转义特殊字符并提供一些性能优势。关于`PreparedStatement`类的讨论已经超出了本教程的范围，但是如果你需要的话，那里有大量的文档。
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 没有转发地址

接下来，更新记录。为了演示这一点，首先要修改倒数第二个示例，使显示的每个条目旁边都有一个编辑选项。下面是修改后的代码:

```
<html>                           
<head>                           
<basefont face="Arial">                           
</head>                           

<body>                           
                           
<%                           
// check submit state                           
String submit = request.getParameter("submit");                           

// form not yet submitted                           
// display initial page                           
if(submit == null)                           
{                           
%>                           

<form action="view.jsp" method="GET">                           
Enter your name:&nbsp;<input type="text" name="name"                            
size="10"> &nbsp; <input type="submit" name="submit"                            
value="Go"> </form>                           

<%                           
}                           
// form submitted, display result                           
else                           
{                           
%>                           

<%@ page language="java" import="java.sql.*" %>                           

<%                           
// get username                           
String uid = request.getParameter("name");                           

// define database parameters                           
String host="localhost";                           
String user="us867";                           
String pass="jsf84d";                           
String db="db876";                           
String conn;                           
%>                           

<h2><% out.println(uid); %>'s Little Black Book</h2>                           
<hr>                           

<table border=1 cellspacing=4 cellpadding=4>                           
<tr>                           
<td><b>First name</b></td>                           
<td><b>Last name</b></td>                           
<td><b>Tel</b></td>                           
<td><b>Fax</b></td>                           
<td><b>Email address</b></td>                           
<td>&nbsp;</td>                           
</tr>                           

<%                           
Class.forName("org.gjt.mm.mysql.Driver");                           

// create connection string                           
conn = "jdbc:mysql://" + host + "/" + db + "?user="                            
+ user + "&password=" + pass;                           

// pass database parameters to JDBC driver                           
Connection Conn = DriverManager.getConnection(conn);                           

// query statement                           
Statement SQLStatement = Conn.createStatement();                           

// generate query                           
String Query = "SELECT * FROM abook WHERE uid = '" + uid + "'";                           

// get result                           
ResultSet SQLResult = SQLStatement.executeQuery(Query);                           

// display records                           
// if available                           
  while(SQLResult.next())                           
  {                           
    String FName = SQLResult.getString("fname");                           
    String LName = SQLResult.getString("lname");                           
    String Tel = SQLResult.getString("tel");                           
    String Fax = SQLResult.getString("fax");                           
    String Email = SQLResult.getString("email");                           

    // get the record number HERE                           
    String ID = SQLResult.getString("id");                           

    // add an edit link to each record with the ID                           
    out.println("<tr><td>" + FName + "</td><td>"                            
+ LName + "</td><td>" + Tel + "</td><td>" + Fax + "</td><td>"                            
+ Email + "</td><td><a + href=edit.jsp?id=" ID + ">edit this                            
entry</a></td></tr>");                           
  }                           

// close connections                           
SQLResult.close();                           
SQLStatement.close();                           
Conn.close();                           

}                           
 %>                           

</table>                           
                           
</body>                           
</html>
```

点击这个链接将激活脚本“edit.jsp ”,并通过`URL GET`方法将记录号传递给它。

现在让我们来看看“edit.jsp”

```
 <html>                           
<head>                           
<basefont face="Arial">                           
</head>                           

<body>                           
                           
<h2>Update Address Book Entry</h2>                           

<%@ page language="java" import="java.sql.*" %>                           

<%                           
// form variables                           
String fid = request.getParameter("id");                           
int id = Integer.parseInt(fid);                           

String fname = "";                           
String lname = "";                           
String tel = "";                           
String fax = "";                           
String email = "";                           
String address = "";                           
String company = "";                           
String comment = "";                           

// database parameters                           
String host="localhost";                           
String user="us867";                           
String pass="jsf84d";                           
String db="db876";                           
String conn;                           

Class.forName("org.gjt.mm.mysql.Driver");                           

// create connection string                           
conn = "jdbc:mysql://" + host + "/" + db + "?user=" + user                            
+ "&password=" + pass;                           

// pass database parameters to JDBC driver                           
Connection Conn = DriverManager.getConnection(conn);                           

// query statement                           
Statement SQLStatement = Conn.createStatement();                           

// generate query                           
String Query = "SELECT * FROM abook where id=" + id;                           

// get result                           
ResultSet SQLResult = SQLStatement.executeQuery(Query);                           

// get and display record                           
  fname = SQLResult.getString("fname");                           
  lname = SQLResult.getString("lname");                           
  tel = SQLResult.getString("tel");                           
  fax = SQLResult.getString("fax");                           
  email = SQLResult.getString("email");                           
  address = SQLResult.getString("addr");                           
  company = SQLResult.getString("company");                           
  comment = SQLResult.getString("comment");                           

// close connection                           
SQLResult.close();                           
SQLStatement.close();                           
Conn.close();                           
 %>                           

<table border=0 cellspacing=5 cellpadding=5>                           
<form action="edit_res.jsp" method="POST">                           

<input type="hidden" name="id" value="<%= id %>">                           

<tr>                           
<td>First name</td>                           
<td><input type="Text" name="fname" size="15" value="<%=                            
fname %>"></td> </tr>                           

<tr>                           
<td>Last name</td>                           
<td><input type="Text" name="lname" size="15" value="<%=                            
lname %>"></td> </tr>                           

<tr>                           
<td>Address</td>                           
<td><textarea name="address"><%= address %></textarea></td> </tr>                           

<tr>                           
<td>Tel</td>                           
<td><input type="Text" name="tel" size="10" value="<%=                            
tel %>"></td> </tr>                           

<tr>                           
<td>Fax</td>                           
<td><input type="Text" name="fax" size="10" value="<%=                            
fax %>"></td> </tr>                           

<tr>                           
<td>Email address</td>                           
<td><input type="Text" name="email" size="10" value="<%=                            
email %>"></td> </tr>                           

<tr>                           
<td>Company</td>                           
<td><input type="Text" name="company" size="25" value="<%=                            
company %>"></td> </tr>                           

<tr>                           
<td>Comment</td>                           
<td><input type="Text" name="comment" size="25" value="<%=                            
comment %>"></td> </tr>                           

<tr>                           
<td colspan=2><input type="submit" name="submit"                            
value="Update"></td> </tr>                           

</form>                           
</table>                           

                           
</body>                           
</html>
```

正如您所看到的，一旦“edit.jsp”接收到记录号，它就连接到数据库，提取记录，然后生成一个简单的表单，表单中已经填入了值(注意我们使用快捷方式`<%= %>`构造来显示变量值)。然后，用户可以自由修改表单中显示的信息；完成后，表单被提交给“edit_res.jsp ”,它负责`UPDATE`操作。

```
<html>                           
<head>                           
<basefont face="Arial">                           
</head>                           
<body>                           
                           

<%@ page language="java" import="java.sql.*" %>                           

<%                           
// edit_res.jsp                           

// form data                           
String fid = request.getParameter("id");                           
int id = Integer.parseInt(fid);                           

String fname = request.getParameter("fname");                           
String lname = request.getParameter("lname");                           
String address = request.getParameter("address");                           
String tel = request.getParameter("tel");                           
String fax = request.getParameter("fax");                           
String email = request.getParameter("email");                           
String company = request.getParameter("company");                           
String comment = request.getParameter("comment");                           

// database parameters                           
String host="localhost";                           
String user="root";                           
String pass="";                           
String db="test";                           
String conn;                           

Class.forName("org.gjt.mm.mysql.Driver");                           

// create connection string                           
conn = "jdbc:mysql://" + host + "/" + db + "?user=" + user                            
+ "&password=" + pass;                           

// pass database parameters to JDBC driver                           
Connection Conn = DriverManager.getConnection(conn);                           

// query statement                           
Statement SQLStatement = Conn.createStatement();                           

// generate query                           
String Query = "UPDATE abook SET fname='" + fname + "', lname='"                            
+ lname + "', tel='" + tel + "', fax='" + fax + "', email='"                            
+ email + "', addr='" + address + "', company='"  + company                            
+ "', comment='"  + comment + "' WHERE id=" + id;                           

// get result code                           
int SQLStatus = SQLStatement.executeUpdate(Query);                           

  if(SQLStatus != 0)                           
  {                           
  out.println("Entry successfully updated.");                           
  }                           
  else                           
  {                           
  out.println("Error! Please try again.");                           
  }                           

// close connection                           
SQLStatement.close();                           
Conn.close();                           

%>                           

                           
</body>                           
</html>
```

当然，我们可以将上面的所有脚本合并到一个 JSP 文档中——但是我们暂时把这项工作留给您。
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 清理

最后，是时候做一点日常保养了。这个例子演示了如何使用`DELETE`语句删除一个特定的条目。同样，基本原则保持不变，只有查询字符串发生了变化。

首先，必须修改初始列表页面，以包含删除特定条目的链接——这类似于添加“编辑此条目”链接的方式。假设这样做了，应该用要删除的记录的编号来调用脚本“delete.jsp”。所以，正如你有链接一样

```
"<a href=edit.jsp?id=" + ID + ">edit this entry</a>" you will now have the additional link
```

```
"<a href=delete.jsp?id=" + ID + ">delete this entry</a>" 
```

我们来看看“delete.jsp”。

```
<html>                            
<head>                            
<basefont face="Arial">                            
</head>                            
<body>                            
                            

<%@ page language="java" import="java.sql.*" %>                            

<%                            
// delete.jsp                            

// form data                            
String fid = request.getParameter("id");                            
int id = Integer.parseInt(fid);                            

// database parameters                            
String host="localhost";                            
String user="us867";                            
String pass="jsf84d";                            
String db="db876";                            
String conn;                            

Class.forName("org.gjt.mm.mysql.Driver");                            

// create connection string                            
conn = "jdbc:mysql://" + host + "/" + db + "?user=" + user                             
+ "&password=" + pass;                            

// pass database parameters to JDBC driver                            
Connection Conn = DriverManager.getConnection(conn);                            

// query statement                            
Statement SQLStatement = Conn.createStatement();                            

// generate query                            
String Query = "DELETE FROM abook WHERE id=" + id;                            

// get result code                            
int SQLStatus = SQLStatement.executeUpdate(Query);                            

  if(SQLStatus != 0)                            
  {                            
  out.println("Entry successfully deleted.");                            
  }                            
  else                            
  {                            
  out.println("Error! Please try again.");                            
  }                            

// close connection                            
SQLStatement.close();                            
Conn.close();                            

%>                            

                            
</body>                            
</html>
```

这就是本期 JSP 文件的全部内容。下一次，我们将看看 JSP 中可用的 HTTP 会话管理功能——所以请确保您会回来看那个！

*注意:本文中的所有例子都已经在装有 Tomcat 3.2 和 JServ 1.1 的 Linux/i586 上进行了测试。这些示例仅用于说明，并不适用于生产环境。YMMV！* 
 版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### JSP 文件–第 6 部分:宽限状态

完美的状态

在过去的几周里，您已经学习了很多关于 JSP 中可用的各种控制结构和对象的知识。您已经看到了如何检索在线表单中发布信息，以及如何将 JSP 文档连接到数据库以生成动态内容。

本周在 JSP 文件中，我们将处理另一个非常有趣的话题——维护网站“状态”的问题。我们将研究解决这一问题的两种常见解决方案——cookie 和基于服务器的会话——并使用简单的例子来说明 JSP 构造，以帮助您识别和跟踪 Web 站点上的客户端请求。

你还会学到比你想知道的更多的东西，关于“维护状态”实际上意味着什么，刚刚描述的每种方法的优点和缺点…而且，如果我们做得对，整个练习会让你发笑。

##### 浪费了，伙计！

这是极客们想要给听力范围内的年轻女性留下深刻印象时互相说的话之一:“HTTP 是无状态的协议，互联网是无状态的开发环境”。简而言之，这意味着作为网络支柱的超文本传输协议无法保留连接到网站的每个客户的身份记忆，因此将对网页的每个请求都视为一个独特和独立的连接，与之前的连接没有任何关系——这与当今一些更具冒险精神的青少年的行为非常相似，他们每天晚上都喝醉， 第二天早上醒来，完全不记得发生了什么，晚上又出去做同样的事情…

现在，只要你漫无目的地从一个网站浏览到另一个网站，这是没有问题的。但是如果你决定从 CDNow.com 买几张碟片呢？在一个“无状态的环境”中，很难跟踪所有你选择购买的商品，因为 HTTP 协议的无状态特性使得它不可能跟踪所选择的商品。

因此，需要一种能够“维护状态”的方法，这种方法允许跟踪客户端连接并维护特定于连接的数据。因此出现了“cookies ”,它允许网站在客户机系统上的一个文件中存储特定于客户机的信息，并在需要时访问文件中的信息。因此，在上面的购物车示例中，所选择的商品将被添加到 cookie 中，并在计费过程中被检索并以合并列表的形式呈现给客户。

为什么叫“饼干”？处理这个账户的公关公司显然在开车时睡着了。

##### 一些基本规则

由于 cookies 用于记录您在特定网站上的活动信息，因此它们只能由创建它们的网站读取。例如，雅虎和 Deja.com 将你的用户名存储在硬盘上的 cookie 中，并在你下次访问他们的网站时使用这些信息自动填写登录表格。这有点像去一家别致的餐厅，让领班直呼你的名字(这是我们最近没有遇到过的事情！)

在深入了解 cookie 技术的本质之前，有几条基本规则是正确的:

1.  单个域不能设置超过二十个 cookies。单个 cookie 的大小不能超过 4 KB。可以设置的最大 cookies 数是 300。

*   向客户端传输 cookie 最常见的方法是通过“Set-Cookie”HTTP 头。

*   cookie 通常拥有五种类型的属性。

第一个是`NAME=VALUE`对，用于存储用户名、电子邮件地址或信用卡号等信息。`NAME`是用于标识 cookie 的字符串，而`VALUE`是存储在 cookie 中的数据。举个例子，

```
clarkkent=superman
```

`EXPIRES`属性定义了 cookie 从系统中自动删除的日期。日期格式必须为“星期几，日-月-年 hh:mm:ss GMT”。举个例子，

```
expires="Sun, 31-Dec-2030 17:51:06 GMT"
```

只要浏览器保持打开，没有明确规定到期日期的 Cookies 就会保持活动状态，一旦浏览器关闭，Cookies 就会被销毁。您可以通过将该属性设置为过去的某个日期来删除现有的 cookie。

`PATH`属性用于设置 Web 服务器上可以访问 cookies 的顶层目录。在大多数情况下，这被设置为

```
path=/
```

以确保服务器上的每个文档都可以访问 cookie。

`DOMAIN`属性用于指定 cookie 链接到的域，`SECURE`属性表示只有在浏览器和服务器之间存在安全协议的情况下才应该设置 cookie。

*   在所有五个属性中，第一个是唯一一个不可选的。

*   每个好的浏览器都为用户提供禁用 cookies 的选项。如果用户决定行使他或她的权利这样做，您的 cookies 将不会被存储，任何访问它们的尝试都将失败。这样做的用户通常是职业罪犯或逃税者。

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 学习写作…

现在，有无数种方法可以在客户端浏览器上创建和读取 cookies 你可以使用 Javascript，你可以使用 PHP，你可以使用任何优秀的编程语言。然而，我们在这里关注的是 JSP——所以让我们来看一个演示如何读写 cookie 的例子。

这是一个简单的点击计数器，它在用户第一次访问网页时创建一个 cookie，然后在以后的每次访问中递增计数器。

```
<%                              
// counter.jsp                              

// declare some variables                              
Cookie cookieCounter = null;                              

// the cookie you want                              
String cookieName = "counter";                              
int cookieFound = 0;                              

// a few more useful variables                              
String tempString;                              
int count=0;                              

// get an array of all cookies available on client                              
Cookie[] cookies = request.getCookies();                              

// iterate through array looking for your cookie                              
for(int i=0; i<cookies.length; i++)                              
{                              
    cookieCounter = cookies[i];                              
    if (cookieName.equals(cookieCounter.getName()))                              
  {                              
  cookieFound = 1;                              
    break;                              
  }                              

}                              

// if found                              
if(cookieFound == 1)                              
{                              

  // get the counter value as string                              
  tempString = cookieCounter.getValue();                              

  // convert it to a number                              
  count = Integer.parseInt(tempString);                              

  // increment it           &nbspf;                  
  count++;                              

  // back to a string                              
  tempString = Integer.toString(count);                              

  // store it in the cookie for future use                              
  cookieCounter.setValue(tempString);                              

  // set some other attributes                              
  cookieCounter.setMaxAge(300);                              
  cookieCounter.setPath("/");                              

  // send cookie to client                              
  response.addCookie(cookieCounter);                              
}                              
// if not found                              
else                              
{                              
  // create a new cookie with counter 0                              
  Cookie alpha = null;                              
  alpha = new Cookie("counter", "0");                              
  alpha.setMaxAge(300);                              
  alpha.setPath("/");                              
  response.addCookie(alpha);                              
}                              
%>                              

<html>                              
<head>                              
<basefont face="Arial">                              
</head>                              

<body>                              

<%                              
// display appropriate message                              
if (count > 0)                              
{                              
out.println("You have visited this page " + count + "                               
time(s)! Don't you have anything else to do, you bum?! ");                               
} else { out.println("Welcome, stranger!"); }  %>                              

</body>                              
</html>
```

当然，这看起来有点复杂——但是一旦我们为你分解它，就不会复杂了。

您需要知道的第一件事是如何在客户机上创建 cookie 这是通过以下代码完成的:

```
<%                              
Cookie alpha = null;                              
alpha = new Cookie("counter", "0");                              
alpha.setMaxAge(300);                              
alpha.setPath("/");                              
response.addCookie(alpha);                              
%>
```

前两行创建了一个 Cookie 对象的新实例—“`alpha`”。cookie 变量“`counter`”随后被初始化并设置为字符串“`0`”。接下来，`Cookie`对象的`setMaxAge()`和`setPath()`方法分别用于设置到期日期(以秒为单位)和 cookie 的可用性。最后，对响应对象的`addCookie()`方法的调用负责将 cookie 实际传输到客户机。

如前所述，唯一不可选的属性是`NAME=VALUE`对。如果你希望你的 cookie 在用户关闭浏览器后仍然可用，你应该明确设置一个截止日期；否则，一旦浏览器关闭，cookie 将被销毁。

`Cookie`对象还附带了一些其他有趣的方法。

*   `setValue(someString)`–将 cookie 的值设置为某个字符串

*   `getValue()`–返回 cookie 的当前值

*   `setPath(someURL)`–将 cookie 的`PATH`属性设置为 someURL

*   `getPath()`–返回`PATH`属性的当前值

*   `setMaxAge(someSeconds)`–设置 cookie 的`EXPIRES`属性，以秒为单位

*   `getMaxAge()`–返回`EXPIRES`属性的当前值

*   `setDomain(someURL)`–设置 cookie 的`DOMAIN`属性

*   `getDomain()`–返回`DOMAIN`属性的当前值

*   `setSecure(flag)`–将 cookie 的`SECURE`属性设置为真或假

*   `getSecure()`–返回`SECURE`属性的当前值

请注意，您只能使用`setValue()`将字符串值保存在 cookie 中，如果您实际上想要存储一个数字(如本例所示)，这需要进行大量的字符串到数字到字符串的转换。

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### …然后阅读

所以写一个小甜饼就够了——但是读一读怎么样呢？这是代码。

```
<%                               
// declare some variables                               
Cookie cookieCounter = null;                               

// the cookie you want                               
String cookieName = "counter";                               
int cookieFound = 0;                               

// a few more useful variables                               
String tempString;                               
int count=0;                               

// get an array of all cookies available on client                               
Cookie[] cookies = request.getCookies();                               

// iterate through array looking for your cookie                               
for(int i=0; i<cookies.length; i++)                               
{                               
    cookieCounter = cookies[i];                               
    if (cookieName.equals(cookieCounter.getName()))                               
  {                               
  cookieFound = 1;                               
    break;                               
  }                               

}                               
%>
```

在读取 cookie 之前，您需要在客户端的硬盘上找到它。由于 JSP 目前不允许您通过名称直接定位和识别 cookie，所以您需要遍历所有可用的 cookie，直到找到您正在寻找的 cookie。在上面的例子中，“`for`”循环就是这样做的；如果找到了 cookie，它会将变量“`cookieFound`”设置为 1，并退出循环。

此时，cookie 被存储在`Cookie`对象“`cookieCounter`”中。然后，您可以使用`getValue()`对象方法获取 cookie 变量的当前值，并在您的脚本中使用它。

```
 <%                               
// if found                               
if(cookieFound == 1)                               
{                               

  // get the counter value as string                               
  tempString = cookieCounter.getValue();                               

  // convert it to a number                               
  count = Integer.parseInt(tempString);                               

  // increment it                               
  count++;                               

  // back to a string                               
  tempString = Integer.toString(count);                               

  // store it in the cookie for future use                               
  cookieCounter.setValue(tempString);                               

  // set some other attributes                               
  cookieCounter.setMaxAge(300);                               
  cookieCounter.setPath("/");                               

  // send cookie to client                               
  response.addCookie(cookieCounter);                               
}                               
%>
```

##### 名称又能代表什么呢

一旦你理解了这两个基本技术，剩下的代码应该很容易破译。如果发现 cookie，计数器变量递增，使用`setValue()`方法向 cookie 写入新值；该计数器变量随后显示在页面上。如果没有找到 cookie，则意味着这是用户第一次访问该页面(或者是在前一个 cookie 过期后进行的访问)；设置新的 cookie 并显示适当的消息。

同样，因为这个例子处理的是数字而不是字符串，所以需要无数次的变形来将 cookie 中的字符串值转换为数字，递增它，然后再将其转换回字符串以存储在 cookie 中。

这是另一个例子，一个简单的形式。输入您的姓名并提交表单–将创建一个包含您输入的姓名的 cookie。当您下次访问该页面时，您的姓名将自动为您填写。

```
<%                               
// form.jsp                               

// declare some variables                               
Cookie thisCookie = null;                               

// the cookie you want                               
String cookieName = "username";                               
int cookieFound = 0;                               
String username = "";                               

// get an array of all cookies available on client                               
Cookie[] cookies = request.getCookies();                               

// iterate through array looking for your cookie                               
for(int i=0; i<cookies.length; i++)                               
{                               
    thisCookie = cookies[i];                               
    if (cookieName.equals(thisCookie.getName()))                               
  {                               
  cookieFound = 1;                               
    break;                               
  }                               

}                               

// if found                               
if(cookieFound == 1)                               
{                               
// get the counter value                               
username = thisCookie.getValue();                               
}                               
%>                               

<html>                               
<head>                               
<basefont face="Arial">                               
</head>                               

<body>                               

<form action="login.jsp" method="post">                               
<table>                               

<tr>                               
<td>Your name</td>                               
<td><input type=text name=username value="<%=                                
username %>">&nbsp;<input type="Submit" value="Click                                
me"></td> </tr>                               

</table>                               
</form>                               

</body>                               
</html>
```

这是初始登录表单“form . JSP”——如您所见，它检查 cookie 的存在，并使用它来填写帐户用户名(如果可用的话)。

表单提交时，调用“login.jsp”处理输入表单的数据；它还会适当地设置 cookie 属性。

```
<%                               
// login.jsp                               

// get values from form                               
String username = request.getParameter("username");                               

// create a new cookie to store the username                               
Cookie alpha = null;                               
alpha = new Cookie("username", username);                               
alpha.setMaxAge(300);                               
alpha.setPath("/");                               

// send it to client                               
response.addCookie(alpha);                               
%>                               

<html>                               
<head>                               
<basefont face="Arial">                               
</head>                               

<body>                               

Get lost, <b><%= username %></b>!                               

</body>                               
</html>
```

很简单，是吧？

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### B 计划

基于 cookie 的方法很常见；许多网站都使用它，因为它灵活、简单，并且独立于服务器端语言(一旦 cookie 被保存到客户机的硬盘上，您就可以使用 JavaScript、PHP、JSP 或……来读取它)，唯一的问题是:它依赖于客户机是否接受 cookie。

因此，另一种常见的方法是当客户访问网站时，使用“会话”来存储特定的信息；该会话数据在访问期间保留，通常在访问结束时销毁。因此，一个会话可以被认为是一个包含大量变量值对的信息篮子；这些变量值对在访问期间存在，并且可以在访问期间的任何时候访问。这种方法为协议的“无状态”本质提供了一种优雅的解决方案，并在当今许多最大的站点上用于跟踪和维护个人和商业交易的信息。

创建的每个会话都与唯一的标识字符串或“会话 ID”相关联；这个字符串被发送到客户机，同时在服务器上创建一个具有相同唯一标识号的临时条目，可以是在平面文件中，也可以是在数据库中。现在可以注册任意数量的“会话变量”——这些是普通变量，可以用来存储文本或数字信息，并且可以在整个会话中读取或写入。

会话 ID 通过 cookie 或 URL GET 方法传输到客户端。反过来，客户机必须用这个会话 ID 引用每个请求，以便服务器知道每个客户机与哪个会话相关联，并为每个客户机使用适当的会话变量。如果客户端不支持 cookies 并且 URL 方法被拒绝或未被使用，会话管理功能和会话变量将对客户端不可用，并且每个请求将被视为第一次发出。

通常，只要用户的浏览器打开，会话就会保持活动状态，或者保持一段预定义的时间。一旦用户的浏览器关闭，或者超过了指定的时间段，会话和其中的所有变量都会被自动销毁。

##### 会话剖析

创建 JSP 会话比编写 cookie 简单得多。为了演示这一点，下面是几页前看到的基于 cookie 的计数器的会话等价物。

```
<html>                                
<head>                                
</head>                                

<body>                                

<%                                
// get the value of the session variable                                
Integer visits = (Integer)session.getValue("counter");                                

// if null                                
if (visits == null)                                
{                                
// set it to 0 and print a welcome message                                
visits = new Integer(0);                                
session.putValue("counter", visits);                                
out.println("Welcome, stranger!");                                
}                                
else                                
{                                
// else increment and write the new value                                
visits = new Integer(visits.intValue() + 1); session.putValue                                
("counter", visits); out.println("You have visited this                                 
page " + visits + " time(s)! Don't you have anything                                 
else to do, you bum?! "); }  %>                                

</body>                                
</html>
```

创建一个会话不需要做太多事情——只需使用`Session`对象的
 `putValue()`方法创建一个或多个会话变量，JSP 就会自动创建一个会话并注册变量。然后，您可以使用`getValue()`方法自动检索会话变量的值。

这里需要注意的重要一点是，在使用`getValue()`时，有必要对会话变量进行类型转换——在上面的例子中，在将变量赋给常规 JSP 变量之前，我们已经在括号中明确说明了变量的类型。因为 JSP 允许您将对象绑定到会话，所以您可以绑定一个整数对象，从而绕过等价 cookie 示例中的一些字符串到数字的转换例程。

有了这些信息，上面的例子阅读起来就简单多了。“if”语句用于处理两种可能的选择:第一次访问者(没有先前的会话)或再次访问者(预先存在的会话)。根据“计数器”变量是否存在，采取适当的行动。

`Session`对象还附带了许多其他有趣的方法——下面是其中的一些:

*   `getId()`–返回包含唯一会话 ID 的字符串

*   `setMaxInactiveInterval(someSeconds)`–在最后一个客户端请求后，保持会话活动几秒钟

*   `invalidate()`–销毁会话

*   `getAttribute()`和`setAttribute()`——如果`getValue()`和`putValue()`不起作用，试试这些

*   `getCreationTime()`–返回创建该会话的时间，以秒为单位，作为 1970 年 1 月 1 日午夜的偏移量

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 拒绝访问

下面是另一个简单的例子，它演示了上面的一些方法，也说明了如何使用 JSP 会话来保护包含敏感信息的网页。

这个示例显示了一个要求您输入姓名的表单(“start.html”)，并在您提交表单后将您带到一个新页面(“login.jsp”)。“login.jsp”创建一个会话来存储您输入的名称，并提供一个到“rootshell.jsp”的链接，这是要保护的敏感文件。

只要会话处于活动状态，任何访问“rootshell.jsp”页面的尝试都会成功。另一方面，如果会话不活动，任何绕过初始表单访问“rootshell.jsp”的尝试都将失败，用户将被重定向到“start.html”。

这是一个相对简单的例子，但是用来演示会话变量的一个更常见的用法。

本例中的所有重定向都是使用`Response`对象完成的(您还记得这一点，不是吗？)

```
<html>                                 
<head>                                 
<basefont face="Arial">                                 
</head>                                 

<body>                                 
<!-- start.html -->                                 
<form action="login.jsp" method="post">                                 
<table>                                 

<tr>                                 
<td>Your name</td>                                 
<td><input type=text name=username>&nbsp;<input                                  
type="Submit" value="Click me"></td> </tr>                                 

</table>                                 
</form>                                 

</body>                                 
</html>
```

一旦表单被提交，“login.jsp”就会接管。

```
<html>                                 
<head>                                 
<basefont face="Arial"                                 
</head>                                 

<body>                                 
<%                                 

// get the form variable                                 
String username = request.getParameter("username");                                 

// create a session                                 
session.putValue("username", username);                                 

// set a timeout period                                 
session.setMaxInactiveInterval(300);                                 

// display a link to the protected file                                 
out.println("Thank you for using this service.");                                  
out.println("Click <a href=rootshell.jsp>here</a> for                                  
root access"); %>                                 

</body>                                 
</html>
```

这是最高机密页面。

```
<html>                                 
<head>                                 
<basefont face="Arial">                                 
</head>                                 

<body>                                 
<%                                 
// rootshell.jsp                                 

// get the username from the session                                 
String username = (String)session.getValue("username");                                 

// if null, security breach!                                 
if (username == null)                                 
{                                 
  response.setHeader("Location", "start.html");                                 
}                                 
else                                 
{                                 
// display the protected page                                 
 %>                                 

Welcome to your root shell, <b><%= username %></b>!                                 
<p>                                 
Your session ID is <% out.println( session.getId() ); %>                                 
<p>                                 
This session will expire in <% out.println(                                 
session.getMaxInactiveInterval() ); %> seconds.                                 

<%                                 
}                                 
 %>                                 

</body>                                 
</html>
```

为了测试这一点，首先登录并找到“rootshell . JSP”——访问它应该没有问题。然后关闭浏览器，再次启动，不经过登录过程，尝试到“rootshell . JSP”；您应该会被自动重定向到登录页面。

仅此而已。现在，您应该对 JSP 如何试图解决“无状态协议”问题有了一个非常清晰的概念，并对如何创建和使用客户端 cookies 和服务器端会话有了一些了解。去练习吧！

*注意:本文中的所有例子都已经在装有 Tomcat 3.2 和 JServ 1.1 的 Linux/i586 上进行了测试。这些示例仅用于说明，并不适用于生产环境。YMMV！* 
 版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### JSP 文件—第 7 部分:bug、Beans 和 Banks

烧尽

没有一个程序员，不管他有多优秀，会一直写出没有错误的代码。因此，大多数编程语言都有捕捉错误并采取补救措施的内置功能。这个动作可以简单到显示一个错误信息，也可以复杂到加热你的计算机内部直到它们着火(开玩笑！)

在接下来的几页中，我们将研究 JSP 中可用的一些错误管理技术，并演示一些用于将独立 JavaBeans 集成到 JSP 脚本中的 JSP 指令。

##### Bugathon

不过，首先来上一堂语义学的速成课。“错误”和“异常”之间有一个重要的区别。

JSP“错误”通常不能被捕获，因为它们通常涉及应用程序开发人员无法控制的问题——比如服务器内存不足、文件丢失或文件系统损坏。

另一方面，术语“异常”是指那些可以被跟踪和控制的错误。例如，如果提供给函数的参数不正确，JSP 将“抛出”一个“无效参数”异常，以及一个堆栈跟踪或问题的详细解释。像这样的异常可以被应用程序“捕获”，并适当地转移到异常处理例程。

由于 JSP 与 Java 有许多共同之处，所以当您得知 JSP 的异常概念几乎完全源自 Java 时，您不会感到惊讶。在 Java 中，异常是一个对象的实例；在 JSP 中，隐式异常对象可用于识别和管理异常。

##### 异常聪明

处理 JSP 异常的过程有两个基本组成部分:

1.  在 JSP 脚本中添加一个指令，标识发生异常时要调用的文件的名称。

*   创建一个适当的“错误页面”，可以选择使用 Exception 对象来获取有关异常的更多详细信息。

让我们用一个简单的例子来说明这个过程。这里有一个将一个数除以零的 JSP 脚本——这个过程肯定会让任何编程语言痛苦地尖叫。

```
<%                                  
int a = 19;                                  
int b = 0;                                  
int c = a/b;                                  
%>
```

以下是输出结果:

```
Error: 500                                  
Internal Servlet Error:                                  
javax.servlet.ServletException: / by zero                                  
  at org.apache.jasper.runtime.PageContextImpl.handlePage                                  
Exception(PageContextImp                                  
l.java:459)                                  
  at _0002fb_0002ejspb_jsp_2._jspService                                  
(_0002fb_0002ejspb_jsp_2.java:72)                                  
  at org.apache.jasper.runtime.HttpJsp                                  
Base.service(HttpJspBase.java:119)                                  
  at javax.servlet.http.HttpServlet.                                  
service(HttpServlet.java:853)                                  
  at org.apache.jasper.servlet.JspServlet                                  
$JspServletWrapper.service(JspServlet.ja                                  
va:177)                                  
  at org.apache.jasper.servlet.JspServlet.                                  
serviceJspFile(JspServlet.java:318)                                  
  at org.apache.jasper.servlet.JspServlet.                                  
service(JspServlet.java, Compiled                                  
Code)                                  
  at javax.servlet.http.HttpServlet.                                  
service(HttpServlet.java:853)                                  
  at org.apache.tomcat.core.ServletWrapper.                                  
doService(ServletWrapper.java:404)                                  
  at org.apache.tomcat.core.Handler.service                                  
(Handler.java:286)                                  
  at org.apache.tomcat.core.ServletWrapper.                                  
service(ServletWrapper.java:372)                                  
  at org.apache.tomcat.core.ContextManager.                                  
internalService(ContextManager.java:79                                  
7)                                  
  at org.apache.tomcat.core.ContextManager.                                  
service(ContextManager.java:743)                                  
  at org.apache.tomcat.service.connector.                                  
Ajp12ConnectionHandler.processConnection                                  
(Ajp12ConnectionHandler.java:166)                                  
  at org.apache.tomcat.service.TcpWorkerThread.                                  
runIt(PoolTcpEndpoint.java,                                  
Compiled Code)                                  
  at org.apache.tomcat.util.ThreadPool$Control                                  
Runnable.run(ThreadPool.java,                                  
Compiled Code)                                  
  at java.lang.Thread.run(Thread.java, Compiled Code)                                  
Root cause:                                  
java.lang.ArithmeticException: / by zero                                  
  at _0002fb_0002ejspb_jsp_2._jspService                                  
(_0002fb_0002ejspb_jsp_2.java:62)                                  
  at org.apache.jasper.runtime.HttpJspBase.                                  
service(HttpJspBase.java:119)                                  
  at javax.servlet.http.HttpServlet.service                                  
(HttpServlet.java:853)                                  
  at org.apache.jasper.servlet.JspServlet$Jsp                                  
ServletWrapper.service(JspServlet.ja                                  
va:177)                                  
  at org.apache.jasper.servlet.JspServlet.                                  
serviceJspFile(JspServlet.java:318)                                  
  at org.apache.jasper.servlet.JspServlet.                                  
service(JspServlet.java, Compiled                                  
Code)                                  
  at javax.servlet.http.HttpServlet.service                                  
(HttpServlet.java:853)                                  
  at org.apache.tomcat.core.ServletWrapper.                                  
doService(ServletWrapper.java:404)                                  
  at org.apache.tomcat.core.Handler.service                                  
(Handler.java:286)                             &nbsfp;    
  at org.apache.tomcat.core.ServletWrapper.                                  
service(ServletWrapper.java:372)                                  
  at org.apache.tomcat.core.ContextManager.                                  
internalService(ContextManager.java:79                                  
7)                                  
  at org.apache.tomcat.core.ContextManager.                                  
service(ContextManager.java:743)                                  
  at org.apache.tomcat.service.connector.                                  
Ajp12ConnectionHandler.processConnection                                  
(Ajp12ConnectionHandler.java:166)                                  
  at org.apache.tomcat.service.TcpWorkerThread.                                  
runIt(PoolTcpEndpoint.java,                                  
Compiled Code)                                  
  at org.apache.tomcat.util.ThreadPool$Control                                  
Runnable.run(ThreadPool.java,                                  
Compiled Code)                                  
  at java.lang.Thread.run(Thread.java, Compiled Code)
```

难看吧。

为了让 JSP 处理这个异常，让用户永远不会看到如此难看的东西，您只需简单地添加一个

```
<%@ page errorPage="error.jsp" %>
```

添加到脚本中，这样看起来就像这样:

```
<%@ page errorPage="error.jsp" %>                                  
<%                                  
int a = 19;                                  
int b = 0;                                  
int c = a/b;                                  
 %>
```

由脚本抛出的异常现在将被 JSP 捕获，并自动路由到“error.jsp”。接下来让我们来看看。
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 讨厌鬼；坏消息

异常处理例程“error.jsp”可以是简单的，也可以是复杂的。如果你想要一些友好和容易理解的东西，你可以用下面的:

```
<html>                                   
<head>                                   
<basefont face="Arial">                                   
</head>                                   

<body bgcolor="white">                                   
<h2>Oops!</h2>                                   
Something bad just happened.                                    
Click here to go back to the main page. </body>                                   

</html>
```

现在，如果你再次运行上面的例子，JSP 应该会自动把你转到这个页面，而不是用又长又烦人的错误消息来恶心你。

上面的脚本只是通知用户发生了错误；它没有说明错误的类型，也没有说明错误发生的原因。如果您也想显示它，您需要使用内置的 Exception 对象来获取关于异常的信息。下一个示例使用修改后的“error.jsp”来说明这一点:

```
<%@ page isErrorPage="true" %>                                   
<html>                                   
<head>                                   
<basefont face="Arial">                                   
</head>                                   

<body bgcolor="white">                                   
<h2>Oops!</h2>                                   
Something bad just happened:                                   
<br>                                   
<b><i><%= exception.getMessage() %></i></b>                                   

</body>                                   
</html>
```

而这一次，输出会更有帮助一点。

```
Oops!                                   
Something bad just happened:                                   
/ by zero
```

特别注意剧本的第一行；该指令

```
<%@ page isErrorPage="true" %>
```

告诉 JSP 这是一个错误页面，并初始化 Exception 对象的实例以供使用。

异常对象附带了几个有用的方法——
`exception.getMessage()`方法用于获取错误描述，而`exception.printStackTrace()`方法用于打印调试信息。

##### 你扔()，我来接住

还可以使用 Java“throw”构造在 JSP 脚本中人为地引发一个异常。这很方便，例如，在验证表单域数据时——如果输入的值不是预期的格式，您可以抛出一个异常(带有信息性错误消息),并将用户重定向到错误页面。

这里有一个如何使用它的例子。这是一个简单的表单，要求您输入一个数字

```
<html>                                   
<head>                                   
<basefont face="Arial">                                   
</head>                                   

<body>                                   

<form action="number.jsp">                                   
Enter a number between 1 and 3                                    
<input type=text name=number size=1> </form>                                   

</body>                                   
</html>
```

这是服务器端的 JSP 脚本，它会检查错误，如果不满足某些条件，就会抛出异常。

```
<html>                                   
<head>                                   
<basefont face="Arial">                                   
</head>                                   

<body>                                   
<%@ page errorPage="error.jsp" %>                                   
<%                                   
String temp = request.getParameter("number");                                   

int number = Integer.parseInt(temp);                                   

if (number != 2)                                   
  {                                   
  throw new Exception ("How dumb can you get?!") ;                                   
  }                                   
 else                                   
   {                                   
  out.println("Hmmm...maybe you're not as dumb as you look!");                                   
  }                                   
 %>                                   

</body>                                   
</html>
```

接下来，简单看一下 JavaBeans 以及它们如何与 JSP 环境集成。
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 豆包

从开发的角度来看，JSP 最吸引人的一点是，它允许将现有的 JavaBeans 轻松集成到 JSP 脚本中(对于那些不了解的人来说，JavaBeans 是面向对象的技术，它允许开发人员构建可重用的 Java 组件或应用程序)。这样做的好处是显而易见的:已经投资 JavaBeans 技术的组织可以利用它以最少的时间和成本快速重用现有的代码模块。

我们不打算在这里深入探讨构建 JavaBean 的细节——关于这个主题有无数的教程，包括 Sun Microsystems 的一个很好的教程。相反，我们将简单地介绍 JSP 构造，它允许您将 Bean 导入到 JSP 脚本中，设置 Bean 属性和访问 Bean 方法。

JavaBeans 通过`<jsp:useBean>`动作被引入 JSP 脚本，该动作创建 Bean 的实例并标识其活动的范围。

下面的代码片段创建了 Bean“ice cream”的一个实例，用惟一的 ID“vanilla”标识它，并将其范围定义为仅限于“page”。

```
<jsp:useBean id="vanilla" scope="page" class="iceCream">                                     
</jsp:useBean>
```

上面的“`scope`”属性定义了 Bean 的影响范围。例如，“`scope=page`”表示该实例将在当前页面保持活动状态，而“`scope=session`”表示该实例将在整个会话期间保持可用。

##### 把它带到银行

与`<jsp:useBean>`密切相关的是`<jsp:setProperty>`，通常用于设置 Bean 属性；这些属性可以显式设置，或者基于请求对象中可用的参数设置(您还记得这一点，不是吗？)

下面的代码片段使用`<jsp:setProperty>`操作将值“7683”分配给 Bean 属性“`accountBalance`”。注意，`<jsp:setProperty>`动作引用了一个先前定义的名为“account”的 Bean 的实例

```
<jsp:useBean id="account" scope="page" class="Bank">                                    
<jsp:setProperty name="account" property="accountBalance"                                     
value="7683" /> </jsp:useBean>
```

如果您想根据请求对象中的数据(比如表单值)设置实例属性，您可以使用

```
<jsp:setProperty name="account" property="*" />
```

JSP 将自动遍历请求对象，将参数名与可用的 Bean 属性相匹配，并适当地赋值。

##### 打开暖气

为了说明这是如何工作的，我们编写了一个简单的 Bean，它接受温度值并在摄氏和华氏刻度之间进行转换。/# l # http://www . webmasterbase . com/examples/JSP files/temperature . zip/# lt #/在这里。/#el#/

这里有一页用到了它:

```
<html>                                    
<head>                                    
<basefont face="Arial">                                    
</head>                                    
<body>                                    
<!-- initialize the Bean, set some defaults -->                                    

<jsp:useBean id="c" scope="page" class="Temperature">                                     
<jsp:setProperty name="c" property="celsius" value="10" />                                    
</jsp:useBean>                                    

<%                                    
// get the current temperature                                    
out.println("Temperature in Celsius is " + c.getCelsius() + "<p>");                                    

// turn up the heat                                    
c.setCelsius(36.8);                                    

// get the current temperature                                    
out.println("Temperature in Celsius is now " + c.getCelsius() + "<p>");                                    

// convert the temperature to Fahrenheit                                    
out.println(c.getCelsius() + " Celsius is " +                                    
c.convertCelsiusToFahrenheit(c.getCelsius()) + " Fahrenheit<p>");                                    

// ...and back again                                    
out.println(c.getFahrenheit() + " Fahrenheit is " +                                    
c.convertFahrenheitToCelsius(c.getFahrenheit()) + " Celsius<p>"); %>                                    

</body>                                    
</html>
```

这是输出结果:

```
Temperature in Celsius is 10.0                                    
Temperature in Celsius is now 36.8                                    
36.8 Celsius is 98.24 Fahrenheit                                    
98.24 Fahrenheit is 36.8 Celsius
```

暂时就这些了。在本系列的下一篇也是最后一篇文章中，我们将探索 JSP 的标记库，它允许 Web 设计者在不了解 JSP 的情况下为他们的 Web 页面添加强大的功能。一定要在这里完成那个！

注意:本文中的所有例子都已经在装有 Tomcat 3.2 和 JServ 1.1 的 Linux/i586 上进行了测试。这些示例仅用于说明，并不适用于生产环境。YMMV！
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### JSP 文件—第 8 部分:标记和打包

预示最后结果（或败局）的先兆

在过去的几周里，我们带您进行了一次错综复杂的 JSP 之旅，从条件语句和循环等基础知识开始，然后快速转向表单处理、会话管理和错误处理等更复杂的内容。

但是天下没有不散的宴席——因此，在 JSP 文件的最后一集，我们将简要介绍这种强大的服务器端脚本语言的其他一些方面。

##### 玩标签

JSP 最令人兴奋的特性之一是能够在 JSP 应用程序中构建和使用定制的“标记库”。“标记库”是一个可重用的 JSP 代码块，通常用来用易于阅读和理解的标记(在外观上类似于 HTML 标记)替换 Java 代码。一旦编写完成，这些标记库就可以反复使用，从而为语言带来了新的可重用性。

除了可重用性之外，从维护的角度来看，标记库还提供了实质性的优势。由于标记库在很大程度上是使用 XML 类型的标记来定义的，因此它们使得将应用程序表示与应用程序逻辑分离成为可能——这反过来意味着从事 Web 应用程序工作的设计者和开发者可以使用标记，而不必担心它如何以及为什么工作。程序代码和最终布局之间的这种分离是大多数设计者梦寐以求的——现在几乎任何知道如何将 Java 和 JSP 代码串在一起的人都可以使用它。

上面讨论的分离的另一个优点是在向标记库添加新特征或对现有特征进行更改时的易用性。因为标记库是可移植和可重用的，所以对它的更改将立即反映在使用该标记库的所有 JSP 页面中。类似地，添加到标签库中的新特性将立即对所有包含该库的页面可用。

标签库正在慢慢流行起来，许多 JSP 开发人员在线免费发布了定制的标签库。这些库通常是为特定任务设计的——数据库连接、字符串操作等等——并且它们为 JSP 开发人员为他们自己的项目挖掘提供了丰富的途径。毕竟，当您可以找到一个功能丰富、经过适当测试且免费的标签库来在线完成同样的工作时，为什么要浪费时间编写 Java 代码来发送电子邮件呢？

##### 向内看

标签库由几个组件组成。首先是用 Java 编写的标记类本身——它包含了使标记发挥作用的所有幕后代码。这个类将在标记库描述符中被引用，并构成我们正在构建的三明治的主体。

接下来，您需要一个“标记库描述符”，这是一个 XML 文件，通常用。TLD 文件扩展名。这个 TLD 包含关于标记库不同方面的信息:标记的名称，与它相关联的 Java 类的名称，功能的简短描述，可选属性和主体内容。

这两个组件通常都打包到一个 JAR 文件中，以便于分发。

现在，我们不打算在这里进入编写标记库的本质——已经有一大堆教程详细讨论了这一点，一些链接包含在本文的末尾。但是，我们要做的是说明如何将标记库合并到 JSP 文档中，以及它如何与脚本的其余部分进行交互。

为了说明这一点，我们将使用 Jakarta 项目的 DATETIME 标记库，该标记库旨在操作日期和时间戳。你可以在这里下载这个库的副本。

一旦你下载了这个库的副本，你需要在开始使用它之前告诉 Tomcat。这里的第一步是决定您计划在其中使用库的上下文——对于这个例子，我们将假设上下文是“/test/”。接下来，将 TLD 文件从发行版复制到上下文的“web-inf/”目录中，并将主 JAR 文件复制到上下文的“web-inf/lib/”目录中。

这里的最后一步是打开驻留在“web-inf/”目录中的“web.xml”文件，并修改它以反映新的标记库——这是通过`<taglib>`指令完成的。

```
<taglib> <taglib-uri>http://jakarta.apache.org/taglibs/                                     
datetime-1.0</taglib-uri>                                     
<taglib-location>/WEB-INF/datetime.tld</taglib-location>                                     
</taglib>
```

该元素指定标记库的位置，以及用于引用它的唯一 URI。
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 会见大力水手

此时，您可以开始在 JSP 页面中使用新的标记库。首先，用“`taglib`”指令声明它——该指令必须出现在页面中的任何自定义标记之前。

```
<%@ taglib uri="http://jakarta.apache.org/taglibs/datetime-1.0"                                      
prefix="popeye" %>
```

URI 是标记库的唯一标识符，必须与“web.xml”中指定的 URI 匹配，而前缀出现在对自定义标记的每次调用中，用于区分同一页面中不同库的标记。

一旦声明了库，就可以开始在 JSP 脚本中使用定制标记了。考虑以下示例，该示例使用 DATETIME 库中的自定义标记来计算自 1970 年 1 月 1 日以来经过的秒数。

```
<html>                                      
<head>                                      
</head>                                      
<body>                                      

<%@ taglib uri="http://jakarta.apache.org/taglibs/datetime-1.0"                                      
prefix="popeye" %>                                      

The number of milliseconds since January 1, 1970 is <popeye:                                      
currenttime/>                                      

</body>                                      
</html>
```

输出是:

```
The number of milliseconds since January 1, 1970 is 987165837280
```

如果您只是想要当前的日期和时间呢？通过组合`<currenttime>`标签和`<format>`标签，日期时间库使它变得很容易！

```
<html>                                      
<head>                                      
</head>                                      
<body>                                      

<%@ taglib uri="http://jakarta.apache.org/taglibs/datetime-1.0"                                      
prefix="popeye" %>                                      

The current date and time is                                      
<popeye:format pattern="hh:mm EEE MMMM dd yyyy"> <popeye:                                      
currenttime/> </popeye:format>                                      

</body>                                      
</html>
```

如果你想知道，你看到的 EEEs 和 MMMs 有格式化代码，用来定义日期和时间的打印格式。以下是输出结果:

```
The current date and time is  06:22 Fri April 13 2001
```

上面的例子也说明了一些标签是如何相互嵌套的——这可以形成强大的组合，也是这种架构的巧妙之处之一。

生成一个日或月的列表怎么样？日期时间库用它的`<weekdays>`和`<months>`标签覆盖了你…

```
<html>                                      
<head>                                      
</head>                                      
<body>                                      

<%@ taglib uri="http://jakarta.apache.org/taglibs/datetime-1.0"                                      
prefix="popeye" %>                                      

<form>                                      
Select a day                                      
<select name="weekday">                                      
<popeye:weekdays id="day">                                      
<option value="<jsp:getProperty name="day" property="dayOfWeek"/>                                      
"> <jsp:getProperty name="day" property="weekday"/> </popeye:weekdays>                                      
</select> </form>                                      

</body>                                      
</html>
```

DATETIME 库还附带了一大堆其他有用的标记——看看文档，了解其他各种可用的特性。
 版权 [Melonfire](http://www.melonfire.com/) ，2000 年。保留所有权利。

##### 你有邮件！

现在，我们最初的论点是，标记库将允许没有 JSP 知识的 Web 设计人员在 Web 页面上实现复杂的功能。虽然你刚刚看到的例子简单明了，但它们并没有真正证明这个论点——毕竟，有理由假设任何有能力的 Web 设计人员都知道如何通过 JavaScript 操作日期和时间值，并且不会为此真正需要一个标记库。因此，让我们尝试一些大多数客户端专家不知道如何处理的事情——电子邮件传递。

通常，从网站生成电子邮件的过程是通过服务器端脚本来处理的——Perl 专家打开一个到 sendmail 的管道，而 PHP 程序员则使用`mail()`函数。这意味着预先了解 PHP 或 Perl——这不是一个网页设计新手应该具备的。JSP 为这个可怜的灵魂提供了什么解决方案呢？

##### 雅加达梅勒图书馆。

使用这个库中的几个简单标记，任何 Web 开发人员都可以快速地将邮件处理功能添加到 Web 页面中——事实上，这个过程比 Perl 和 PHP 中的等效技术更简单。看一看。

```
<html>                                       
<head>                                       
</head>                                       
<body>                                       

<%@ taglib uri="jspmailer" prefix="jmail" %>                                       

<jmail:mail server="mail.server.com" to="recipient@server.com"                                        
from="sender@server.com" subject="This stuff is pretty cool!">                                        
<jmail:message> OK, you've convinced me - this tag library thing                                        
is awesome! Where do I sign up? </jmail:message> <jmail:send/>                                        
</jmail:mail>                                       

</body>                                       
</html>
```

通过简化复杂的代码，像上面这样的定制标记库可以提高团队开发项目的效率和协调性，让开发人员专注于构建更好的工具(库)，让设计人员专注于表示(而不是代码)。随着 JSP 的发展，可以预期标记库的重要性会增加——即使是现在，它们也是选择这种服务器端语言的最有说服力的理由之一。

##### 小程序滑稽动作

您已经看到了 JSP“动作”是如何工作的——例如，在上一篇文章中，我们结合 JavaBeans 演示了`<jsp:useBean>`和`<jsp:setProperty>`动作，而`<jsp:include>`在本系列的第一篇文章中进行了说明。然而，我们错过了几个重要的——因此，我们想向您介绍用于将 Java 小程序合并到网页中的`<jsp:plugin>`。

指令负责生成嵌入和激活 Java applet 所需的所有 HTML 代码。考虑下面的
例子:

```
<html>                                       
<head>                                       
</head>                                       
<body>                                       

<jsp:plugin type="applet" code="NewsTicker.class"                                       
name="newsticker" height="100" width="100">                                       

<jsp:params>                                       
<jsp:param name="x" value="10"/>                                       
<jsp:param name="y" value="25"/>                                       
<jsp:param name="cx" value="90"/>                                       
<jsp:param name="cy" value="114"/>                                       
<jsp:param name="bgcolor" value="102,102,153"/>                                       
<jsp:param name="textcolor" value="0,0,0"/>                                       
<jsp:param name="hilitecolor" value="255,0,0"/>                                       
</jsp:params>                                       

<jsp:fallback>Oops! Something bad happened and I can't display this                                       
applet</jsp:fallback>                                       

</jsp:plugin>                                       

</body>                                       
</html>
```

上面的代码设置了包含在“NewsTicker.class”中的 applet，并向它传递了一组参数的名称-值对。标签`<jsp:param>`用于将这些参数传递给 applet，而指令`<jsp:fallback>`包含错误文本，以防 applet 无法找到或显示。

当 JSP 编译并呈现页面时，上面的代码会自动转换为等效的 HTML。

```
<html>                                       
<head>                                       
</head>                                       
<body>                                       

<OBJECT classid="clsid:8AD9C840-044E-11D1-B3E9-00805F499D93"                                        
width="100" height="100" codebase="http://java.sun.com/products/                                       
plugin/1.2.2/jinstall-1_2_2-win.cab#V                                       
ersion=1,2,2,0">                                       
<PARAM name="java_code" value="NewsTicker.class">                                       
<PARAM name="type" value="application/x-java-applet;">                                       
<PARAM name="cy" value="114">                                       
<PARAM name="cx" value="90">                                       
<PARAM name="bgcolor" value="102,102,153">                                       
<PARAM name="hilitecolor" value="255,0,0">                                       
<PARAM name="y" value="25">                                       
<PARAM name="x" value="10">                                       
<PARAM name="textcolor" value="0,0,0">                                       
<COMMENT>                                       
<EMBED type="application/x-java-applet;"  width="100"  height="100"                                       
pluginspage="http://java.sun.com/products/plugin/"                                       
java_code="NewsTicker.class"                                       
cy=114                                       
cx=90                                       
bgcolor=102,102,153                                       
hilitecolor=255,0,0                                       
y=25                                       
x=10                                       
textcolor=0,0,0                                       
>                                       
<NOEMBED>                                       
</COMMENT>                                       
Oops! Something bad happened and I can't display this applet                                        
</NOEMBED></EMBED> </OBJECT>                                       

</body>                                       
</html>
```

最后，`<jsp:forward>`指令用于自动将客户端请求重定向到另一个文档。下面的代码会自动将客户端重定向到脚本“endzone.jsp”。

```
<jsp:forward page="endzone.jsp" />
```

正如前面的例子一样，额外的参数可以通过`<jsp:param>`传递给新脚本。举个例子，

```
<jsp:forward page="endzone.jsp">                                       
  <jsp:param name="user" value="joe" />                                       
  <jsp:param name="uid" value="653" />                                       
  <jsp:param name="gid" value="1220" />                                       
</jsp:forward>
```

就这样，是时候结束了。我们希望您喜欢它，并希望它成为您进入 JSP 世界的良好起点。

如果您有兴趣了解本系列中讨论的更多主题，可以看看我们的 JSP 部分:关于 [Melonfire Trog](http://www.melonfire.com/community/columns/trog/archives.php?category=JSP) 、 [Sun Microsystems 的 JSP 页面](http://java.sun.com/products/jsp/)、 [Java 文档和参考资料](http://java.sun.com/docs/)，或者[关于标记库的本教程](http://java.sun.com/products/jsp/tutorial/TagLibrariesTOC.html)。另一方面，如果您对我们有任何问题、意见或大笔资金，请给我们写信——我们很乐意收到您的来信！

下次见…保持健康！

*注意:本文中的所有例子都已经在装有 Tomcat 3.2 和 JServ 1.1 的 Linux/i586 上进行了测试。这些示例仅用于说明，并不适用于生产环境。YMMV！* 
 版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

## 分享这篇文章 
```