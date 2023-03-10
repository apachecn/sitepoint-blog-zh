# 易接近的闪光灯部件 1 和 2

> 原文：<https://www.sitepoint.com/accessible-flash-parts-1-2/>

迈向可访问 Flash 内容的一步:为搜索引擎提供实时 Flash 内容，并为 Flash 网站添加书签。

第 1 部分 Flash 的 SEO，从下面开始。

[第 2 部分–书签 Flash，从这里开始。](https://www.sitepoint.com/blog/)

##### 比分

今天的搜索引擎是在与传统的 html 文档的合作中成长起来的；毫不奇怪，它们擅长索引 html 文件中的每一条信息。

太好了！但是，当你的新网站没有 html 内容时会发生什么呢？这种情况在内置于 [Macromedia Flash](http://www.macromedia.com/software/flash/ ) 的网站中最为常见，这些网站的内容被锁定在一个文件中，搜索引擎会完全忽略该文件。

这个问题让我想到了 Flash Web 开发的圣杯:根据当前内容，为没有相关 html 内容的 Flash 网站获得良好的搜索引擎列表，换句话说，就是所谓的 Flash 网站。

在本案例研究中，我将向您展示如何以一种简单、可扩展且对一般网站访问者透明的方式完成这项任务——无需考虑烦人的重定向、刷新或“隐藏”文本。它还有一个额外的好处，即允许将 Flash 网站的各个部分(包括框架和场景)添加到书签中，以供日后参考。

我还将举例说明向搜索引擎提供 Flash 内容的其他方法。

这个过程是使用完全不同的概念构建的，从表面上看，这些概念与闪存毫无关系。然而，结合在一起，它们提供了成功的关键。

利用这种方法，您将拥有一个 Flash 网站，其内容可以通过 URL 或外部超链接直接访问。搜索引擎可以有效地索引 Flash 网站的知识将允许您将 Flash 作为一种可行的技术来推广，以开发和推广您的客户的网站。

##### 最初的想法

当我在建立自己的网站的最初阶段，我想到了开发一个可扩展且易于维护的系统，以允许搜索引擎索引 Flash 网站。

2000 年，我评估了各种选择，并决定使用 Flash 建立网站，因为我知道网络世界(包括一些权威人士)的普遍共识是，这种方法意味着我将把网站的可访问性抛到了九霄云外。全 Flash 网站的罪行包括忽略浏览器的后退和前进按钮*，以及无法使网站被搜索引擎索引(在直接链接的总框架下)。

作为一个喜欢质疑的人，我把网站的目标之一设定为能够很好地解决搜索引擎索引的问题。我的网站必须被搜索引擎完全索引，这样潜在的客户才能在搜索结果列表中找到我。

罗伯特·彭纳运用一些横向思维，用一个框架集证明了这个假设是错误的(这个解决方案可能不是每个人都喜欢)。

随着我的进步，我意识到在我实现这个目标的过程中涉及到许多概念。

##### 概念 1:外部内容是关键

在阅读了很多很多 Flash 资源后，我发现实现 Flash 网站 SEO 的一个关键是将网站内容与 Flash 电影分开——从外部资源加载。这些来源可以是任何东西，从数据库表或 XML 文件到简单的纯文本文件。

我看到的将文本内容存储在 SWF 文件之外的好处是，它可以在多种外部设备中使用。例如，数据库表中的文本可以用在 XHTML、Flash 以及展望未来的 XML 就绪设备中。

我选择存储和检索内容，这是因为我以前使用该技术的经验，是一个用 T2 PHP T3 作为服务器端脚本的 MySQL T1 数据库。然而，您可以使用任何数据库/脚本组合来实现这一点。

现在，存储内容是一回事，可靠地检索和提供内容以供搜索引擎索引和人们在 Flash 电影中观看是另一回事。是时候进行一些搜索引擎研究了…

##### 概念 2:理解索引

这绝不是对搜索引擎如何获取其内容的详尽考察——那会占用更多的枯树！然而，我们仍然需要快速浏览一下网页内容是如何出现在搜索引擎中的。

众所周知的搜索引擎通过机器人获取内容，机器人在网络上跟随链接，并将找到的信息发送回各自的引擎。每个机器人都有一个唯一的名称，称为用户代理字符串(字符串是一段文本的代码，与程序员的鞋带无关)。事实上，几乎每个访问者，机器人，网页浏览器或其他网站提供自己的用户代理字符串。

让我们看几个例子，看看我们在处理什么:

*   Google bot/2.1(+http://www . Google bot . com/bot . html)
*   Mozilla/3.0(Slurp/si；slurp@inktomi.com；http://www.inktomi.com/slurp.html)
*   FAST-web crawler/3.3(crawler @ FAST . no；http://fast.no/support.php?c=faqs/crawler
*   Mozilla/5.0(Windows；u；Windows NT 5.0en-US；rv:1.2.1)壁虎/20021130

上面的前三个用户代理字符串标识来自 Google、Inktomi 和 FAST 的搜索机器人。最后一个是标准的 Mozilla 1.x 标识符。

事实上，著名的搜索引擎给出的一致的用户代理字符串不同于我们的人类访问者，这使得我可以将网站的流量分为两个主要类别和一个次要类别:

1.  搜索机器人

3.  使用网络浏览器的人

5.  未知(提供的用户代理很难识别的访问者)

搜索机器人的核心是浏览器的精简版本。他们忽略了客户端脚本，如 JavaScript，也可以忽略一些技巧，如 Meta 标签刷新和使文本与文档背景颜色相同。

但是服务器端脚本呢？搜索机器人，像它们的网络浏览器兄弟一样，接受服务器发送的页面。我可以让我的 Web 服务器根据用户代理字符串的查询结果发送不同的页面吗？你打赌！

##### 概念 3:从 URL 定位内容

在 HTML 网站中定位内容的起点是 URL，同样的概念也适用于 Flash 网站。当通过 URL 请求页面时，文件名和附加在文件名末尾的变量一起被发送到服务器。

我们都见过这样的事情:

google.com/search/?q=flash+and+search+engines

我的情况是:

/index.php？go=4

文件名中位于`?`字符之后的那部分被称为查询字符串*，脚本 index.php 在运行时可以使用。除此之外，任何发送到 PHP 脚本的变量都可以在一个名为:

http://www . PHP . net/manual/en/reserved . variables . PHP # reserved . variables . GET $ _ GET。

在这种情况下，我们将使用$_GET['go']。(在上面的 Google 案例中，我们有$_GET['q'])

完成这一任务的另一种方法是[重写网址](http://httpd.apache.org/docs-2.0/misc/rewriteguide.html )。例如，而不是这个 index.php？我们可能有/go/4/。这被认为对搜索引擎更友好，但与简单的查询字符串相比，学习曲线更陡峭。是的，像 Google 这样的搜索引擎用查询字符串来索引页面，只要它们保持简短(我通过经验发现，我可以得到查询字符串中有两个变量的列表)。我将把选择权交给您，但是为了简单起见，现在我将坚持使用查询字符串。

我在这里的第一个任务是验证$_GET['go']的内容。这是必要的，因为 URL 可以被网站访问者更改，并且不信任脚本通过`GET` 或`POST`方法接收的任何内容是好的 Web 脚本的核心部分。

所以，让我们从询问落入脚本中的变量开始。我已经完整地注释了实现这一点的 PHP 脚本(带有//或被/** **/包围的行)，所以如果你有恐惧症，请阅读这些！

我们还假设数据库连接是成功的。

```
<?  

/** - getparse.inc.php  

---------- what we want to achieve here -----------  

- check if the 'go' variable exists  

- strip out any content that may be hazardous to the script  

- convert 'go' to an integer for the sql query  

- nb : all content kept in the database is identified by a unique integer.  

- nb : error content is identified in the database by the id '-1'  

**/  

//first a function to parse the 'go' variable  

//functions are not run by the script until called  

function parsego($go_var)  

{  

         //check if the variable is an integer  

         if(!is_int($go_var))  

         {  

             //the variable is not an integer, set it to error content.  

             $go_var=-1;  

         }  

         //return the parsed variable.  

         return $go_var;  

}  

//go is passed from the URL header.  

//it is available in the super global array $_GET as element 'go'  

//$go is the variable used to identify the content to be retrieved.'  

//first check if the variable exists,  

//if not set it to retrieve default content (1).  

if(!$_GET['go'])  

{  

$go=1;  

$title_add = "welcome";  

}  

//$_GET['go'] exists  

else  

{  

   //check the 'go' variable for any 'hazardous' content  

   //it needs to be converted to an integer using intval()  

   $go = parsego(intval($_GET['go']));  

   //check to see if the content requested exists.  

   $checkGo_sql="SELECT id, header FROM sitecontent WHERE id=$go";  

   //run the query  

   //the @ symbol is used to suppress error messages from PHP  

   $checkGo_result=@mysql_query($checkGo_sql);  

   //check the number of rows returned.  

   $checkGo_rows=@mysql_num_rows($checkGo_result);  

   if(!$checkGo_result || $checkGo_rows==0 || !$checkGo_rows)  

   {  

     //if there is an error or if there are no matches,  

     //set to retrieve error content  

     $go=-1;  

     $title_add = "content not found";  

   }  

   else  

   {  

     $go = @mysql_result($checkGo_result, 0, 'id');  

     $title_add = @mysql_result($checkGo_result, 0, 'header');  

   }  

}  

/** ----- results ------------------  

- we have a valid value for $go that can now be  

used to retrieve content from the database.  

- it is targeting either the error content (-1) or existing content (>=1)  

**/  

?>
```

##### 概念 4:检查搜索机器人

现在，我有了一个有效的变量来从数据库表中获取内容，所以是时候检查谁或什么在访问网站了。还是那句话，一切都有充分的注释。对于这个例子，我介绍了一个工具，它可以检查脚本是否正常运行。在一个实时网站中，你可能想要删除代码开头的`if-else`语句。

```
<?  

/** ------ botcheck.inc.php -------  

- determine if the user agent is a known search bot.  

**/  

//for spoofing the system so that it can be checked  

//and validated from the URL.  

//remove this when using live.  

if($_GET['useragent'])  

{  

 $user_agent = $_GET['useragent'];  

}  

//if $_GET['ua'] is not available, treat it as a real request.  

else  

{  

 $user_agent = $_SERVER['HTTP_USER_AGENT'];  

 //$_SERVER['HTTP_USER_AGENT']; is where  

 //PHP holds the user agent string.  

 //for PHP versions older than 4.3, use $HTTP_USER_AGENT;  

}  

//a list of terms found in some searchbot strings in my log files  

//also some text only browsers thrown in. eg Lynx  

//and some that should never be allowed near a Flash movie (web tv)  

$searchbot_short_array = array("FAST-WebCrawler/", "Googlebot/",   

"Googlebot-Image/", "Ask Jeeves/Teoma", "Ask Jeeves",   

"Google WAP Proxy", "Slurp/", "Gigabot/", "Poodle predictor",   

"AlkalineBOT/", "Scooter-", "Scooter/", "ASPSeek/", "Sqworm/",   

"TurnitinBot/", "Lynx/", "Lycos_Spider", "appie", "walhello",   

"WebTV", "LinkWalker", "SurveyBot/", "suzaran", "polybot",   

"webcollage/", "Teleport Pro/", "search.ch", "LWP::Simple",   

"EasyDL", "Minerva", "RPT-HTTPClient", "IA_Archiver",   

"Spinne/", "Webster Pro", "MSProxy", "ZyBorg/",   

"Indy Library", "NPBot", "Girafabot",   

"Gulper Web Bot", "grub-client");  

//traverse the array and look at each element  

//if a bot is found, set a variable for later  

  foreach($searchbot_short_array as $search_for)  

  {  

  //attempt to match the array value against the user agent string.  

  //eregi is a case insensitive regular expression matching function  

  //in a live setting  

  //replace $user_agent with $_SERVER['HTTP_USER_AGENT'];  

       if(eregi($search_for, $user_agent))  

       {  

           $bot = true;  

       }  

  }  

?>
```

例如，如果 Altavista 搜索机器人 Scooter 访问了该网站，`$bot` 变量将被赋值为“`true`”。

##### 概念 5:基于用户代理部署内容

脚本环境中现在有两条重要的信息:

1.  数据库表中内容的有效标识符(`$go`)，以及

3.  网站访问者是否为机器人(`$bot == true`)或未知(`$bot`)不在脚本环境中设置，即“`false`”。

现在是时候根据访问者类型对脚本进行分支，并获取请求的内容了。

```
<?   

/** --- filtering.inc.php   

- display text based content if a bot is found   

- ignore if not.   

**/   

//$bot is set in botcheck.inc.php   

if($bot)   

{   

//show searchbot friendly HTML content according to what    

was decided in getparse.inc.php   

    $getBotFriendly_sql = "SELECT header, content, id FROM sitecontent    

WHERE id=$go";   

    //run the query   

    $getBotFriendly_result=@mysql_query($getBotFriendly_sql);   

    //set some results   

    $content_header = @mysql_result($getBotFriendly_result,    

0, 'header');   

    $content = @mysql_result($getBotFriendly_result,    

0, 'content');   

    //get all the headers and id's for links that the search    

bot can follow.   

    $getAllHeaders_sql = "SELECT id, header FROM sitecontent";   

    $getAllHeaders_result=@mysql_query($getAllHeaders_sql);   

?>   

  <div id="vert">   

    <div id="header">   

    <b>From little things, big things grow :</b>   

    <br />   

    providing real-time Flash content for search engines &    

bookmarking Flash Websites   

    - <b><?echo $content_header?></b>   

    </div>   

    <div id="content">   

    <h4><?echo $content_header?></h4>   

    <?   

    //convert newlines (n or r or c) in the content to <br />   

    echo nl2br($content);   

    ?>   

    <h4>site navigation headers from the database table...</h4>   

    <?   

    //show the links to other current content available for archiving   

    //the search bot will follow these links.   

    while($header_row = @mysql_fetch_array($getAllHeaders_result))   

    {   

       if($header_row['id'] != -1)   

       {   

       echo "Â» <a href="$abspath?go=$header_row[id]"    

title="more on : $header_row[header]">";   

       echo "$header_row[header]</a>n<br />n";   

       }   

    }   

?>   

   </div>   

   <div id="footer">   

   Â    

   </div>   

  </div>   

<?   

//include the html footer (</body></html>)   

include("htmlfoot.inc.php");   

// exit from the script so that the flash content is not    

displayed to the bot.   

exit;   

}   

?>
```

##### 将所有内容链接在一起–将内容标识符传递到闪存中

最后，我需要为 index.php 文件设置一个布局，它将包含上述三个[inclusion](http://www.php.net/manual/en/function.include.php)，并将$go 的值传递到 Flash 电影中。为此，我们将该值附加到闪存文件名的末尾。

```
<?   

/** ------- index.php ---------- **/   

/** ----------- start includes ----------**/   

//connect to mysql server and select the database   

include("database.inc.php");   

//parses the variables passed from the URL   

// and checks go against the database   

include("getparse.inc.php");   

//include the html head content <html><head><title> etc   

//also a <link .. /> to the stylesheet, meta tags   

//and usage of the $title_add variable   

include("htmlhead.inc.php");   

//check to see if the user agent is a bot.   

include("botcheck.inc.php");   

//provide plain text content if the user agent is a bot.   

include("filtering.inc.php");   

/** ---------  end includes -----------**/   

//deploy the flash content for the humans among us   

//note the go variable is being passed into the flash movie,   

//available as _root.go.   

//This is the key to successful bookmarking of Flash movies.   

?>   

  <div id="wrapper">   

  <!-- XHTML1.0 compliant code to deploy flash movie -->   

  <!-- if you are seeing a textarea-like box   

  you have a corrupt Flash Player ActiveX control.   

  Try uninstalling Flash Player then installing the    

latest and greatest -->   

  <object   

   data="<?echo $abspath?>index.swf?go=<?echo $go?>"   

   type="application/x-shockwave-flash" codebase="http://download.   

macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,0,0"   

   width="740"   

   height="420">   

   <param name="movie" value="<?echo $abspath?>index.swf   

?go=<?echo $go?>" />   

   <param name="menu" value="false" />   

   <param name="quality" value="high" />   

   <param name="bgcolor" value="#fefefe" />   

    <a href="http://www.macromedia.com/go/getflashplayer"   

    title="link to the Macromedia Flash Player download page">   

    Get the latest Flash Player from Macromedia   

    </a>   

  </object>   

  </div>   

<?   

//end the html   

include("htmlfoot.inc.php");   

?>
```

##### 让 PHP 与 Flash 对话

这就是 PHP 脚本:三个简单的包含，一个包含将所有东西结合在一起的脚本。

该代码成功地为一个已知的 searchbot 提供了数据库表中的文本内容，并阻止了它索引 Flash html 代码。searchbot 现在将把它找到的文本和超链接发送回其父搜索引擎，并且到该内容的链接将很快出现在搜索引擎列表中。

这个过程的第二部分包括当我们中的一个人激活了搜索引擎列表中的一个超链接时，或者当 URL 被直接输入到浏览器中时——即当`$bot`没有被设置时——会发生什么。在这种情况下，PHP 解释器完全跳过 filtering.inc.php 文本内容的提供，转而处理显示 Flash 电影的代码。

PHP 脚本现在需要将有效的请求传递给 Flash，这是通过将`$go` 变量添加到 Flash 电影文件名来完成的。这样做非常简单但功能强大——它将把`$go`的值传递到电影中，使它作为变量`_root.go`可供 ActionScript 使用。

这是 URL 和 Flash 电影之间的链接，最重要的是，使整个过程成为可能。

##### ActionScript 启动

一旦我的 Flash 网站加载完毕，我就可以根据`_root.go`的值从数据库中检索内容，知道它已经被前面的 PHP 脚本验证过了。这将是搜索引擎使用`$go`作为标识符检索到的相同内容。

有许多方法可以检索 Flash 的动态内容。一种方法使用 LoadVars，这是一种基于 Flash Player 6 的对象，可以与 PHP 等服务器端脚本进行对话。它不能与 Flash Player 的旧版本(低于 6)一起工作，所以如果你的目标播放器是版本 3 到 5，你应该使用 loadVariables 函数。

```
_root.go will be available to the Flash movie from frame 1, so it can instantly be added to a LoadVars object:
```

```
contentFetcher = new LoadVars();    

contentFetcher.id = _root.go;    

contentFetcher.sendAndLoad("requestcontent.php",     

contentDisplay, POST);
```

在这里，我将`_root.go`转移到一个名为`contentFetcher`的新的`LoadVars`对象中，然后将对象内容发送给一个名为 requestcontent.php 的 PHP 脚本。这个 PHP 脚本从数据库表中检索内容，并以 [url 编码格式](http://www.php.net/manual/en/function.urlencode.php)为 Flash 准备内容。脚本成功运行后，将使用 onLoad 事件处理程序检索结果，以便在电影中显示。

##### 实现目标

我最初让搜索引擎索引我的网站的目标实现了！我很快在谷歌的缓存中找到了我的网站。这是我的清单，我的数据库中的所有内容都被编入索引，根本看不到 Flash。对于一个 Flash 开发者来说，矛盾的是，这正是我想要的！

这个系统的美妙之处可以在

*   **简单:**所有工作都由三个基本脚本完成
*   **可扩展性:**每当“人类可读”的内容更新时，搜索引擎的内容就会自动更新
*   透明性:对于机器人和人类这两种类型的网站访问者来说，通过一些简单的脚本，对方的内容是不可见的
*   **平台独立性:**脚本在服务器上运行，所以不兼容的浏览器篡改代码没有问题

就像生活中的大多数事情一样，实现有价值的事情往往会产生积极的持续影响。在这种情况下，我对从 URL 向 Flash 传递变量的修改部分解决了一个可访问性错误，这个错误对许多考虑构建 Flash 网站的人来说是一个障碍——书签。

在这个案例研究的第二部分，我将拿出一些关于如何实现书签的代码。如果你没有数据库，我们将看看你可以使用的一些替代方法，概述该系统的一些优点和缺点，以及未来可能的方向，并讨论关于隐形的快速警告。

注意——你可以在这里下载两篇文章的代码。

##### 意外收获-书签

是啊！书签直接返回一个 Flash 网站是可能的。使用我们在本文第一部分中讨论的 SEO 解决方案，一旦每段内容被加载到 Flash 电影中，就可以通过唯一的 id 来识别它。此外，通过一点 Flash-JavaScript 交互，我可以提供一个链接，允许访问者为当前可见的内容添加书签。

Internet Explorer 4+ for Windows 允许使用一点 IE 特定的 Javascript 将书签 URL 写入其中。出于一些常识性的安全原因，诸如 Mozilla、Opera 之类的浏览器，以及基本上任何不是用于 Windows 的 IE 浏览器，都不允许这种功能。

如果我们在 HTML 超链接中添加一个函数调用，并将这个锚存储在检索到的内容中，Flash 就可以与 JavaScript 进行通信。下面是动作脚本和超链接的代码:

```
bkurl = "http://www.webqs.com/?go=" + contentDisplay.id;     

bktitle = "webqs.com : " + contentDisplay.header;     

<a href="javascript:setBookmark(bkurl, bktitle)">bookmark      

this content</a>
```

简单！当在 Flash 中单击时，这将调用一个名为“`setBookmark`”的 JavaScript 函数，该函数可以放在相关 HTML 页面的`<head>`标记中:

```
<script type="text/javascript">     

<!--     

    function setBookmark(url,pagetitle)     

    //function performs the bookmark action     

    {     

            //code for IE4+ on win32     

            if (navigator.appName == "Microsoft      

Internet Explorer" && navigator.appVersion >= "4.0" &&      

navigator.platform == "Win32")     

            {     

                          window.external.AddFavorite(url, pagetitle);     

            }     

            //this code posts a prompt if not     

            else     

            {     

                          window.prompt('Hit Ctrl-Shift-D      

(Command-D on a Mac, Ctrl-T in Opera) to bookmark this      

page.nCopy and paste this address and title for the      

bookmark :' , url + '  ' + pagetitle);     

            }     

// -->     

</script>
```

请注意，与标准 HTML 页面相比，这种方法有一个很大的缺点，即网站访问者不能简单地点击书签键来存储当前内容的 URL(除非它是所请求内容的 URL)。为什么不呢？因为，每当在 Flash 电影中请求新内容时，URL 在整个过程中都保持不变，不会改变以反映更新的内容。

##### 替代方案—如果我没有数据库怎么办？

上面描述的让搜索引擎索引 Flash 内容的方法是基于这样一个假设，即我们使用数据库作为存储介质。那么，如果您的内容存储在纯文本文件中或嵌入到实际的 Flash 电影中，会发生什么呢？

用 PHP 从纯文本文件中检索内容是一项简单的任务，它使用了该语言内置的文件系统函数。使用`[file()](http://www.php.net/manual/en/function.file.php)` [函数](http://www.php.net/manual/en/function.file.php)将所有内容放入一个数组，然后遍历数组并打印出每个元素。为了将文本文件与 URL 中输入的内容相匹配，我们必须使用一个命名系统，如 1.txt、2.txt 等。您可以将一个“$go.txt”文件作为读取和打印到屏幕上的目标，而不是使用$go 的值从数据库表中获取数据。

如果由于某种原因，您在 Flash 电影中嵌入了文本，请复制嵌入的内容并将其放入文本文件中。然后可以使用上面的方法检索它。您会发现每次更新 Flash 内容时都会这样做，因此考虑迁移到文本文件、XML 文件或数据库来存储内容可能是明智的。

##### 优势

搜索引擎可以索引网站上最新的 Flash 内容，而不必手动更新搜索引擎内容，这一事实对 Flash 网站的可访问性是一个巨大的推动。

此外，我们的客户和我们作为开发人员看到了 Flash 网站在搜索引擎列表中获得良好排名的切实好处，这是基于实际的网站内容。

你的客户可能也有兴趣了解哪些领域的内容更受欢迎。大多数日志文件分析器将带有查询字符串的文件名识别为独立的文件。因此，我们可以向我们的客户提供相关的数字，并根据这些结果进一步定制网站内容。

最重要的是，潜在客户可以被告知，是的，一个 Flash 网站可以被搜索引擎索引——而不会严重增加项目的成本。

##### 不足之处

在将搜索机器人转移到纯文本内容时，值得注意的是，该系统依赖于您保持存储的机器人列表最新和准确，这可能是该解决方案的最大弱点。

当然，这个问题不是一个脚本破坏者，因为任何通过过滤的机器人都会简单地找到保存 Flash 电影的 HTML 标签。不过，保持这个列表的更新取决于开发者——就像其他网站管理员使用 robot.txt 文件来阻止某些机器人访问他们的网站一样。

##### 小心隐形

根据搜索引擎的定义，当像这样的方法被用于向机器人提供与网站中的实际内容不匹配的内容时，就会发生伪装。

来自[谷歌站长的常见问题](http://www.google.com/webmasters/faq.html#cloaking):

<q>*“伪装”一词用于描述一个网站，该网站将经过修改的网页返回给搜索该网站的搜索引擎。换句话说，网络服务器被编程为向谷歌返回不同于向普通用户返回的内容，通常是为了扭曲搜索引擎排名。这可能会误导用户当他们点击搜索结果时会发现什么。为了保持我们搜索结果的准确性和质量，谷歌可能会从我们的索引中永久禁止任何从事掩盖行为以扭曲其搜索排名的网站或网站作者。”*</q>

Altavista 也有一个声明，尽管它对隐形的定义不太清楚。

可以看出，搜索引擎不喜欢伪装，有些甚至禁止网站使用这些技术来获得更好的搜索排名。因此，我强烈建议不要提供与你的人类访客所见不符的内容——不值得被禁止使用像谷歌这样的引擎，谷歌除了是最大的搜索引擎之一，还向许多其他引擎提供搜索内容。

无论如何，你希望你的访问者能看到他们正在搜索的内容，对吗？

##### 未来方向

Flash Player 6 的一个有趣的特性，也是一个有望变得更加稳定的特性，就是命名锚。命名锚点允许从 URL 定位电影中的不同帧和场景，而无需重新加载整个电影。此功能的好处是，当每个命名的关键帧被定位时，URL 会发生变化以反映其唯一的 id。

这将允许访问者通过击书签键来获取与他们当前正在查看的内容相关的 URL。它还允许用户使用浏览器的后退和前进按钮来浏览网站。

目前，命名锚点是在时间轴上手动设置的，这使得从 URL 控制的基于 ActionScript 的 Flash 站点很难实现。不过，这是值得尝试的事情，以便更好地定义其当前的能力和限制。

##### 结论

这里讨论的代码及其实现是向更易访问的 Flash 内容迈进了一步。这些信息可用作构建完全由搜索引擎索引的 Flash 网站的基础，从而提高网站流量，并增强对 Flash 作为可行开发工具的认识。

正如本案例研究中所检验的，流动效应包括:

*   **书签**用于直接返回 Flash 网站的内容
*   **可扩展性和稳定性**在内容添加到网站时对其进行处理，以及
*   **客户信心**Flash 网站可以被编入索引，而无需大量开发和成本开销。

读者们，非常欢迎你们在这个过程和实现中增加和减少。对原始方案进行改进，并使其更加稳定，只会有助于开发人员和设计人员推广他们的 Flash 工作。

使用它，建立它，使它更好！

***进一步资源***

罗伯特·彭纳使用框架集实现的后退/前进按钮:
[http://www . robertpenner . com/experiments/back button/back button . html](http://www.robertpenner.com/experiments/backbutton/backbutton.html )

雅各布·尼尔森 2000 年的经典闪电式抨击。不幸的是，每当 Flash 和 accessibility 被提到一起时，这就是我们的集体意识。因为 URL 声明“使用它”来提供另一种观点:

Flazoom.com Flash 可用性白皮书:
[http://www.flazoom.com/usability/usability_toc.shtml](http://www.flazoom.com/usability/usability_toc.shtml)

Flash——99%好——Flash 网站急救手册
[http://www.flash99good.com/index_flash.html](http://www.flash99good.com/index_flash.html)

## 分享这篇文章