# 带有 jQuery 的简单 Ajax

> 原文：<https://www.sitepoint.com/ajax-jquery-2/>

## 示例 2–使用 jQuery 聊天

为了展示 jQuery 的真正威力，我们将制作一个全功能的 Ajax 聊天应用程序。这将允许用户发布消息，并自动更新自己——所有这些都不需要任何页面刷新。因为我们现在正在处理一个稍微复杂一点的应用程序，所以我也将带您更深入地了解 jQuery，演示其他方便的函数。

首先，我们将计划应用程序。这个应用程序不需要太多东西——前端、后端和 jQuery 库。然而，将有一个公平的应用程序的后端和前端的代码位，所以我们的基本计划会让你知道从系统中期待什么。

***策划服务器端***

我们的应用程序的后端需要处理消息提交并输出消息。考虑到这一点，让我们来整理一下后端代码的大致轮廓:

*   连接到数据库
*   如果提交了消息

*   将消息插入数据库
*   删除旧邮件

*   从数据库获取消息并显示为 XML

如您所见，这一切都非常简单明了。如果您必须用另一种语言编写后端，您可以使用这个计划作为指南。

***策划客户端***

前端必须使用 Ajax 调用后端，类似于我们在第一个例子中使用的方法。它必须处理消息发布表单的提交，并定期用新提交的消息更新聊天窗口。然而，我们将在这里添加另一个特性——我们将使用当前的 UNIX 时间戳来确定哪些消息已经被下载，并且只获取新的消息，从而减少带宽使用和服务器负载。下面是前端代码的大致轮廓:

*   页面加载时

*   将当前时间戳设置为 0(所有消息将在此之后发布，即所有消息将被获取)
*   调用函数获取新消息

*   功能:获取新消息

*   使用 POST 向服务器发送请求
*   调用函数来解析 XML 响应
*   添加一个计时器，在一秒钟后调用该函数(如果服务器负载过高，则增加频率)

*   功能:解析新消息的 XML

*   按照 XML 中的指定设置当前时间戳
*   如果返回的状态码是‘2’，没有新消息，那么结束函数调用
*   否则，对于响应中的每条消息，按以下格式将其添加到消息窗口的顶部:

*   **作者:**留言

*   提交表单时:

*   使用 POST 向服务器发送请求，指定:

*   作者姓名(用户指定)
*   消息文本(用户指定)
*   操作无，这是一个 post 请求
*   对服务器的最后一次请求的时间戳

*   清空消息输入框的内容，以便用户可以开始键入另一条消息
*   调用函数来解析 XML 响应(以便发布的消息立即可见)

这个计划可能看起来比后端复杂得多，但是多亏了 jQuery，相关的代码相当短。

***规划数据库***

我们将使用 MySQL 数据库来存储消息(尽管任何 SQL 数据库都可以，只需对代码做一些调整)。我们需要一个有四列的表:一列是每条消息的 id，两个文本列存储消息的作者和消息本身，一个数字时间戳列存储我们的 UNIX 时间戳。下面是创建该表的查询:

```
CREATE TABLE `messages` (  `id` int(7) NOT NULL auto_increment,  `user` varchar(255) NOT NULL,  `msg` text NOT NULL,  `time` int(9) NOT NULL,  PRIMARY KEY  (`id`) );
```

因为我们无法知道消息的长度，所以现在我们将使用一个文本字段。

***服务器端代码(XML)***

 *在构建后端时，让我们首先决定后端应该输出什么(以确定后端和前端之间的接口)，并从那里向后工作。下面是一个简单的 XML 结构:

```
<?xml version="1.0"?> <response>  <status>1</status>  <time>1170323512</time>  <message>    <author>John Citizen</author>    <text>Hello world!</text>  </message>  <message>    <author>John Citizen</author>    <text>Hello world again!</text>  </message> </response>
```

请注意，我添加了标签`'status'`，其值为`'1'`。正如我上面提到的，状态码`1`将代表有新消息的成功请求，`2`代表没有新消息的成功请求。消息标记的每个实例都包括作者和他或她的消息。

***【PHP】***

现在，到后端。我必须用 PHP 来完成，但是因为输出是 XML，所以你可以用任何语言来编写后端——Perl，ASP，任何你喜欢的语言。让我们从逻辑上开始，定义一些配置值，以便我们以后可以轻松地更改它们。我们需要数据库连接细节、我们希望存储在数据库中的消息数量(数据库可以处理数千行，因此这个数字可以设置得相当高)，以及当用户进入聊天时要显示的消息数量。代码如下:

```
$dbhost = "localhost"; $dbuser = "root"; $dbpass = ""; $dbname = "chat"; $store_num = 10; $display_num = 10;
```

现在我们需要了解后端本身的基础知识。数据库连接是必需的，但是我们还需要确保 Internet Explorer 不缓存请求，并且输出被视为 XML。为了确保我们能够识别代码中的任何错误，我们将错误报告设置为“所有错误”。为了方便地处理请求数据，我们将为请求中的每个参数设置一个变量；每个变量都将请求参数的值作为其值。这几行字起了作用:

```
error_reporting(E_ALL); header("Content-type: text/xml"); header("Cache-Control: no-cache"); $dbconn = mysql_connect($dbhost,$dbuser,$dbpass); mysql_select_db($dbname,$dbconn); foreach($_POST as $key => $value)  $$key = mysql_real_escape_string($value, $dbconn);
```

foreach 行检查所有的 POST 数据，为每个参数创建一个变量，并给它分配一个相应的值(例如，`path/to/file.php?variable=value`会将`$variable`设置为`"value"`)。这简化了获取请求数据的过程，因为我们不必手动指定它。

接下来，我们来看看主要功能。在这一点上，我们处理将消息插入数据库，并根据要显示的消息数量检索最近的几条消息，如`$display_num`中所定义的。我提到过，当我们计划前端时，我们将指定一个动作来声明消息正在被提交。我们现在需要检查这个动作——让我们给参数`'action'`赋值`'postmsg'`,指定我们正在执行这个检查，并将数据作为新行插入数据库；我们将在数据库中插入当前的 UNIX 时间戳。

然而，我们还需要清理数据库。根据您的数据库空间限制，您可能希望限制存储的文章数量。一般来说，记录消息是不受欢迎的，所以我决定默认存储十条消息。我们将使用一个函数来获取最后插入的行的 id，并根据那个`id`的值来确定要删除的行。例如，如果我们插入第 11 条消息，我们将从最后一条消息(11)的`id`中减去存储的消息(10)的数量，得到`id`阈值(在本例中为 1)。然后，我们可以删除所有`id`等于或小于该阈值的消息，在本例中，这将导致我们删除第一条消息。感谢 SQL，我们可以在一个查询中完成所有这些。

下面是检查`'postmsg'`动作、将消息插入数据库，以及动态清除消息的代码片段:

```
if(@$action == "postmsg") {  mysql_query("INSERT INTO messages (`user`,`msg`,`time`)        VALUES ('$name','$message',".time().")");  mysql_query("DELETE FROM messages WHERE id <= ".        (mysql_insert_id($dbconn)-$store_num),$dbconn); }
```

使用其他服务器端技术的开发人员应该能够很容易地编写等效的代码。注意，我们调用 time 函数来获取当前的 UNIX 时间戳。我们可以有把握地假设，time 返回的值在脚本执行期间很可能不会改变(即使在一个很慢的服务器上，这个脚本的执行时间也不到百分之一秒)。因此，当我们稍后向前端返回时间戳时，我们可以再次调用时间函数，该值应该仍然是可靠的。

剩下的代码处理从数据库获取最新消息并将它们输出为 XML 的工作。这就是我上面概述的 XML 发挥作用的地方。然而，大部分代码存在于 MySQL 查询中。我们利用 SQL 的强大功能来处理大多数处理任务，因此脚本执行时间不会受到影响。以下是我们的 SQL 查询的要求:

*   它应该只获取每条消息的作者和文本。
*   它应该只获取以前没有下载过的消息——客户机有最近一次请求的时间戳，所以这个时间戳可以插入到 SQL 查询中。
*   它应该对消息进行排序，使最新的消息排在最后，允许它们以相反的顺序输出。
*   它应该将获取的消息数量限制在配置中定义的数量。

任何熟悉 SQL 的人都会同意这都是相当简单的东西。对于我们其余的人来说，下面是实现这个功能的代码。首先，查询:

```
$messages = mysql_query("SELECT user,msg             FROM messages             WHERE time>$time             ORDER BY id ASC             LIMIT $display_num",$dbconn);
```

代码的其余部分是基本的。如果没有返回结果，则将状态代码设置为 0；否则，将其设置为 1。输出初始 XML、每条消息的 XML 和最终 XML。仅此而已！代码如下:

```
if(mysql_num_rows($messages) == 0) $status_code = 2; else $status_code = 1;   echo "<?xml version="1.0"?>n"; echo "<response>n"; echo "t<status>$status_code</status>n"; echo "t<time>".time()."</time>n"; while($message = mysql_fetch_array($messages)) {  echo "t<message>n";  echo "tt<author>$message[user]</author>n";  echo "tt<text>$message[msg]</text>n";  echo "t</message>n"; } echo "</response>";
```

最终的代码都在附加的 zip 文件中，所以不要担心将它复制到您的文本编辑器中。既然后端已经完成，我们可以继续有趣的工作了 HTML 和 jQuery！

***客户端代码(HTML)***

 *在我们进入 jQuery 之前，我们需要构建页面的 HTML 原型。这样，当我们必须使用 jQuery 确定选择获取或更新哪些元素时，我们就知道该怎么做了。我们不需要太多:一个包装器 div、一段消息、一个包含用户名和消息字段的表单以及一个提交按钮。一个简短显示的加载消息会增加最后一笔——我们可以在适当的时候使用 jQuery 删除它。这是 HTML:

```
<div id="wrapper"> <p id="messagewindow"><span id="loading">Loading...</span></p> <form id="chatform"> Name: <input type="text" id="author" /> Message: <input type="text" id="msg" /> <input type="submit" value="ok" /><br /> </form> </div>
```

**Go to page:** [1](https://sitepoint.com/ajax-jquery) | [2](https://sitepoint.com/ajax-jquery-2/) | [3](https://sitepoint.com/ajax-jquery-3/)**

## **分享这篇文章**