# 使用谷歌 API 追踪你的排名

> 原文：<https://www.sitepoint.com/track-rank-google-api/>

2002 年，谷歌为其流行的搜索引擎实现了一个 API，允许网络开发者在他们自己的网站上进行谷歌搜索。使用 SOAP(简单对象访问协议)，API 允许搜索和结果检索在后台运行。许多人也喜欢利用 Google API 来定制搜索结果在他们网站上的显示方式。然而，能够访问 Google API 的一个鲜为人知的好处是，您可以使用它在 Google 中找到您网站的搜索排名，而不必访问 Google 网站。

开发者和网站所有者通常对他们的网站在某些关键词上的排名感兴趣。传统的方法是在 google.com(或你浏览器的谷歌搜索栏)中输入你的搜索词，然后手动筛选出现的结果页面，寻找你的网址。如果您的网站没有针对您搜索的关键字进行优化，您的 URL 可能会排名很低(例如，在第 50 页！).在这种情况下，至少可以说，寻找你的网址是很乏味的。

幸运的是，web 服务可以自动完成这个耗时的过程。但是，这些服务中有许多对您可以查询的记录数量有限制。例如，一些网络服务会在第 200 条记录后停止寻找你的网址。

在本文中，我将介绍一个脚本，它使您能够利用 Google API 来查找您的站点在 Google 中的排名。该脚本有一个内置特性，如果有错误，它会再次查询 Google 服务器，并允许您限制返回结果的数量。我已经打包了这个脚本，所以您可以[下载我们将在本文](https://www.sitepoint.com/examples/googleapi/googleapisearch.zip)中使用的代码。

请注意，在撰写本文时，Google API 仍处于测试阶段。因此，Google API 在未来可能会有一些变化，尽管我不认为这些变化会太剧烈。

对于那些对钻研代码不感兴趣的人，请随意[查看我做的一个小演示](http://web-developer.sitecritic.net/google_api_search.php)看看我在说什么(不过，你需要注册一个【http://www.google.com/apis】T2 谷歌 API 密匙)。

##### 要求

***谷歌键***

在运行脚本之前，您需要从 Google 获得一个开发者密钥。如果你没有，你需要在 [Google API 网页](http://www.google.com/apis)注册一个 Google 账户。该键允许您每天最多进行 1000 次查询；每次将返回十个结果。

注册帐户后，下载 API 工具包。在里面你会找到一个名为`GoogleSearch.wsdl`的文件。将此文件上传到您的服务器。

***梨皂***

这个包是用 PHP 实现发送和接收 XML 消息的 SOAP(简单对象访问协议)协议的几个尝试之一。

许多开发人员更喜欢 nu SOAP(PEAR SOAP 的一个轻量级版本)或 PHP 5 内置的 SOAP 扩展。我选择使用 PEAR SOAP，因为它更容易集成到我的其他 PEAR 应用程序中。如果您没有使用 SOAP 或 web 服务的经验，不要担心！您只需要确保您的主机已经安装了 PEAR SOAP 包。如果没有，你需要从[http://pear.php.net/package/SOAP](http://pear.php.net/package/SOAP)手动安装(或者让你的系统管理员安装)。

***PHP 4 版或更高版本***

该脚本是用 PHP 编写的，所以您的主机也必须支持 PHP。

##### 参数

在开始查询 Google 服务器之前，我们需要设置以下参数。这些变量可以在脚本中设置，也可以通过表单设置。要了解这里发生的事情，请访问谷歌网站，点击搜索框旁边的“高级搜索”链接。高级设置中出现的一些参数类似于 Google API 中使用的参数。

*   **`key` :** 这是你谷歌 API 开发者的钥匙。你确实申请了一个谷歌 API 账户，不是吗？
*   **`q` :** 这是您在搜索中使用的术语或短语。
*   **`maxResults` :** 表示每次查询返回的结果数。通常，您希望该值为 10，这是最大值。
*   **`start` :** 开始查询的第 n 条记录。例如，如果要显示搜索结果 20 到 29，请将 start 变量设置为 20，将 maxResults 变量设置为 10。
*   **`filter` :** 这是一个布尔变量，表示重复的结果是否应该包含在结果列表中。这应该设置为 true 或 false。例如，如果不希望返回重复项，请将过滤器设置为 true。您的结果将被过滤掉重复的内容。
*   **`restrict` :** 您可以将您的搜索结果限制在特定类别，如美国政府、Linux、Macintosh 和 FreeBSD。如果您希望搜索整个网站，请将此项留空。
*   **`safeSearch` :** 如果您想从搜索结果中过滤掉成人内容，请将此项设为 true。
*   **`lr` :** 这个变量可以用来将你的搜索结果限制在特定的语言。如果您将其留空，结果将默认为仅英文网站。
*   **`oe` :** 可以用来控制结果的输出编码。如果您想要使用 UTF-8 的默认输出，请将其留空。

##### 肮脏的工作

该代码使用 while 循环来搜索指定的 URL，一次搜索十个结果。如果 Google 返回一个错误，脚本将暂停 15 秒，然后继续查询。如果错误很严重(比如达到 1000 个查询的限制)，脚本将会结束。while 循环允许脚本继续运行，直到找到您的 URL，或者达到搜索结果的最大数量(由您指定)。如果您在前 1000 条记录中找不到您的 URL，很可能您的网站仍在 Google 沙箱中，或者尚未被 Google 索引。

对于初学者来说，代码可能看起来吓人，但是逻辑很简单。如果你有一点编程经验，你应该可以通过阅读评论轻松地解决这个问题。

`while ($loop) {
// Instantiate the SOAP_WSDL Class.
$wsdl = new SOAP_WSDL('GoogleSearch.wsdl');
// Get the WSDL Proxy Class
$soapclient = $wsdl->getProxy();
// Start Google API query
$result = $soapclient->doGoogleSearch($key,$q,$start,$maxResults,
$filter,$restrict,$safeSearch,$lr,$ie,$oe);
// If error occurs
if (PEAR::isError($result)) {
$message = $result->message;
echo "An error occured: $message<p>";
// pause for 15 secs
sleep(15);
// if too many queries, quit loop
if (preg_match("/Daily limit/i", $message)) {
$loop = false;
}
// if invalid key, quit loop
else if (preg_match("/Invalid/i", $message)){
$loop = false;
}
// if normal timeout message, continue
else {
echo "Retrying in 15 secs. <br/>";
}
}
// if no errors, check for the occurrence of your URL in the results.
else {
$desc = "";
// Step up the starting record for the next loop
$start += $maxResults;
// the actual ranking
$position = $start- $maxResults;
// Search for url
urlSearch($position, $result);
}
}
// display the final output
echo $output;`

##### 从返回的 Google 结果中搜索您的 URL

在前面的代码中，结果类被传递给`urlSearch`函数。首先，该函数检查起始记录是否在您指定的搜索范围内。收到搜索结果后，foreach 循环将尝试将您的 URL 与结果进行匹配。如果找到匹配，将显示您的网站的详细信息，循环将结束。否则，while 循环将继续…

`function urlSearch($position, &$result) {
global $loop;
global $output;
global $resultLimit;
global $maxResults;
global $desc;
global $q;
global $myUrl;
// Get total results from the search
$count =$result->estimatedTotalResultsCount;
// Get the resultElements class
$elements = $result->resultElements;
// if searched results reach limit set by you, quit loop
if ($position == $resultLimit) {
echo "You have restricted the search results to $resultLimit";
$loop = false;
}
else if ($count > 0) {
echo "$position records<br/>";
foreach ($elements as $item) {
$position++;
// if the URL is found, display it and quit loop
if (preg_match("/$myUrl/i", $item->URL)) {
$size = $item->cachedSize;
$title = $item->title;
$url = $item->URL;
$snippet = $item->snippet;
$desc .= "<p>$title [Cache Size: $size]<br/> <a href="$url" target="_blank">$url</a><br />";
$desc .= "$snippet</p>";
$output = "<br /> $count results found. Your URL is ranked #{$position} for the keyword "$q". It will appear on page ".ceil($position/ $maxResults)." in the Google Search Engine. Your listing will appear as follows: <p>$desc</p>";
// now exit the while loop
$loop = false;
}
}
}
// if Google returns no result, quit loop
else {
$output = "No results found for the keyword "$q"";
$loop = false;
}
}`

##### 添加表单

我们需要一个表单向脚本提交参数，以便查询 Google API 服务器。这个简单的表单应该包含几个重要的字段:

*   统一资源定位器
*   谷歌钥匙
*   关键字
*   最大搜索结果

表单的 HTML 显示如下:

`<form id="form1" name="form1" method="post" action="">
<p>Your URL:
<input type="text" name="myUrl" />
eg. sitecritic.net <br/>`

Google API Key:
<input type = " text " name = " Key "/>
eg . evwopl 34 dfds 2md 4354651 kspk/YoYE<br/>
关键字:
<input type = " text " name = " q "/>
eg .网站评论< br / >

最大搜索结果:
<input name = " result limit " type = " text " value = " 500 " size = " 4 "/>
例如:500 < /p >

<输入 type= "提交" name= "提交" value= "提交"/>
</p>
</form>

##### 结论

除非你的网站已经很好的建立了，并且已经有了一个很好的谷歌排名，否则在谷歌中跟踪它会是一个乏味的过程。这个脚本可以让您的工作变得更加轻松，它可以让您自动搜索结果，直到达到“每天 1000 个查询”的限制。通过理解相关的逻辑，您还可以将脚本移植到其他语言。也可以设置自动任务(例如 Unix 上的 cron 作业或 Windows 计划任务)在一天中的特定时间运行脚本，并在搜索完成后通过电子邮件向您发送结果。

别忘了:[的完整源代码可以下载](https://www.sitepoint.com/examples/googleapi/googleapisearch.zip)。

## 分享这篇文章