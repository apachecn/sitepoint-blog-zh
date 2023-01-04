# 灾难恢复设计–媒体播放器、验证器等！

> 原文：<https://www.sitepoint.com/media-player-validators/>

嘿，伙计们！欢迎来到我的老地方。我会在每个月的第一周来回答你的问题。所以请在 drdesign@sitepoint.com 给我写信吧！

##### 保护属于你的东西

嘿，医生！我经营一个摄影网站，我在网站上放了很多我自己的照片。如何阻止用户从我的页面上拍摄这些照片？——菲利普

Phillipe，这可能是我最常被问到的问题之一。作者认为，既然他们创作了自己的照片，他们就应该有能力保护别人不去篡改它们，这是理所当然的。我希望我能告诉你有一个万无一失的方法来保护你的图像。以下是一些常见的建议以及它们的优点和缺陷:

***无右键脚本:*** 这段 JavaScript 代码会给出一个小小的警告，大意是“嘿，不要偷！”当用户使用右击按钮时，这是保存单个图像的一种相当标准的方式。这种做法的缺点是很多人使用右键来做好事，比如在新窗口中打开链接，在网页上获得更多信息等。

除此之外，它被认为是“不专业的”,许多网络冲浪者会想知道你必须隐藏什么，或者你认为什么是如此特别，可能经常在他们完成之前离开，纯粹是出于烦恼。即使你可以用某种不用右键单击的脚本来保护你的图像，现实是所有的网络浏览器都会保存一份你的图像到用户的机器上以备将来使用，所以一个精明的用户可以很容易地进入他们的缓存并查看你的图像！

水印: 也许最好的解决办法是，在你的照片上放置一个简单的水印，这意味着如果有人窃取了你的照片，他们要么去掉你的水印，要么忽略它，在这种情况下，你确实得到了荣誉。水印的真正缺点是，你越想保护你的图片，你的图片就变得越不可用。当然，在你的图片上斜放一个水印意味着没人能窃取它，但是问题是:会有人看你的网站吗？

***嵌入 Flash 或 Java:*** 许多网页作者试图在他们的网站中使用真实的图像，将图像放在 Flash 电影或 Java 小程序中。虽然这意味着用户不能直接将图像保存到他们的机器上，但他们仍然可以用大多数现代键盘上的 PrintScreen 按钮进行屏幕捕捉。

虽然这可能没有给你带来太多的乐观，但成为在线社区一员的关键之一是分享和成长的能力。为什么要有一个贴有你照片的网站？可能是因为你看到了别人的，欣赏了他们的作品。我鼓励你看看许多在网上有网站的著名摄影师——你会发现很少有人使用任何类型的保护机制，因为网络照片的打印质量很差，在互联网之外不可能有人会使用它们。你当然可以利用这种趋势:提供中小尺寸的图片预览，并在你的网站上出售更大尺寸的照片！

希望这对你有帮助，菲利普。关于禁用右键单击和保护图像或代码的更多细节，请务必留意 Rosie Wise 关于这个主题的文章——它将在下周出现在 SitePoint 上！

##### 定时弹出窗口

设计博士，我想在我的网站上包括一个弹出窗口(为我的广告客户增加可用空间)。我希望当用户访问我的主页时弹出窗口出现，但我希望它在十秒钟后再次消失。我还想在弹出窗口上显示一个计时器，上面写着“关闭中…”并告诉用户在弹出窗口消失前还有多少秒。这是怎么做到的？—詹姆斯

没问题詹姆斯。首先，让我们从以前的一篇文章中取出“窗口开启器”代码([从弹出窗口到翻转窗口](http://www.webmasterbase.com/article.php?pid=41&aid=712)):

```
<html> 

<head> 

<script language="JavaScript"> 

<!-- hide from JavaScript-challenged browsers 

function openWindow(url, name) { 

popupWin = window.open(url, name, 'width=400, 

height=600,left=100,top=100') 

} 

// done hiding --> 

</script> 

</head> 

<body onLoad="openWindow('myAnnouncer.html','Announce');"> 

</body> 

</html>
```

这将打开一个小窗口，里面有我们的 myAnnouncer.html 文件。我们的 myAnnouncer.html 文件将如下所示:

```
<html> 

<head> 

</head> 

<body> 

<div id="Clock" align="Center"> </div> 

<script> 

now=10 

function tick()  

{ 

 timeString=now; 

 Clock.innerHTML = "Window will close in: " + timeString; 

 now=now-1; 

 if(now==0) 

 { 

   window.close(); 

 } 

 window.setTimeout("tick();", 1000); 

} 

window.onload = tick; 

</script> 

Put your text here 

</body> 

</html>
```

##### 下拉导航

医生，我想创建一个导航菜单，当你把鼠标放在链接上时，它会‘下拉’…例如，当你把鼠标放在我网站上的‘公司’链接上时，我想显示一个子目录列表。当您将鼠标滑过子目录上的某个项目时，会根据需要显示更多的子目录，等等。这类似于 Windows 用于程序的菜单，程序向右(或向左)层叠到更具体的应用程序中。我如何能建立这个？—杰西卡

杰西卡，我完全知道你在说什么。动态菜单系统的创建应该是谨慎的，并充分考虑到你的用户——所以首先考虑他们。如果你决定这个*就是你想要的*，这里有几个不错的系统可以让你拥有子菜单:

*   [http://www.opencube.com/effect_dqm.html](http://www.opencube.com/effect_dqm.html)
*   [http://www.webreference.com/dhtml/column65/](http://www.webreference.com/dhtml/column65/)
*   [http://www.softcomplex.com/products/tigra_menu/](http://www.softcomplex.com/products/tigra_menu/)

##### 打印与屏幕分辨率

医生:不知道你是否能帮我。我目前正在设计一个 700 像素宽的网站。不幸的是，当页面打印时，文字被截断。我不想在 640 以下设计，因为大多数用户运行在更高的分辨率，但我需要这些页面是可打印的。有什么建议吗？— Kat

Kat，如果你为你的表格或项目指定一个固定的宽度，那么它们将总是以那个宽度显示。一个更好的技巧，尤其是对于打印，是为表格指定一个%宽度。这样，您的页面将很好地缩放到任何大小，并且打印得非常漂亮！

##### 坏表代码

设计博士，你能检查一下我的代码吗？我试图显示 newsid = $comments 表中的所有内容。我有 2 条记录有相同的 newsid，所以它应该显示 2 条记录。相反，它所做的是一直显示 1，但很多时候它会随机显示 2。下面是代码:

```
$sql = "SELECT newsid, comname, comdate, comcontent FROM comments   

WHERE newsid = $comments";   

while ($getnews = mysql_fetch_array(mysql_query($sql))) {   

echo ("<TR><TD>Name: " . $getnews["comname"] . "</TD></TR>");   

echo ("<TR><TD>Posted at: " . $getnews["comdate"] . "</TD></TR>");   

echo ("<TR><TD>Comment: " . $getnews["comcontent"] . "</TD></TR>");  

echo ("<TR><TD><HR></TD></TR>");   

}
```** 

**到底怎么回事？—布莱恩**

Brian，问题是每次你运行“`$getnews = mysql_fetch_array(mysql_query($sql)`”时，你调用的是同一个查询。请尝试以下方法:

```
$sql = "SELECT newsid, comname, comdate, comcontent FROM comments   

WHERE newsid = $comments";   

$r = mysql_query($sql);   

while ($getnews = mysql_fetch_array($r)) {   

echo ("<TR><TD>Name: " . $getnews["comname"] . "</TD></TR>");   

echo ("<TR><TD>Posted at: " . $getnews["comdate"] . "</TD></TR>");   

echo ("<TR><TD>Comment: " . $getnews["comcontent"] . "</TD></TR>");   

echo ("<TR><TD><HR></TD></TR>");   

}
```

##### 嵌入媒体播放器

设计博士，我想将 Windows Media Player 文件嵌入到我的网页中。我该如何做，在哪里可以找到更多信息？—崔西

Trish，在您的页面上使用 Windows Media Player 很容易，只需最少暴露其内置控件。只需使用`<object>`标签将控件嵌入到页面中。

默认情况下，当您使用媒体播放器时，除了视频播放框之外，您还会看到内置的播放、暂停、停止和音量控件。然而，通过将`ShowControls`参数设置为 False，这些控件将被隐藏，这意味着您可以通过脚本提供自己的控制机制。下面是一些基本代码，它们将显示查看器，但不显示任何控件:

```
<OBJECT ID="mPlayer" classid="CLSID:22d6f312-b0f6-11d0-94ab-  

0080c74c7e95" TYPE="application/x-oleobject">   

<PARAM NAME="FileName" VALUE="playback.asf">   

<PARAM NAME="ShowControls" VALUE="FALSE"> </OBJECT>
```

然后，您将使用类似如下的脚本代码:

```
<img src="play.gif" title="Play" onclick="mPlayer.Play()">   

<img src="pause.gif" title="Pause" onclick="mPlayer.Pause()">   

<img src="stop.gif" title="Stop" onclick="mPlayer.Stop()">
```

播放、暂停或停止播放器。媒体播放器公开了许多其他属性、方法和事件，您可以使用它们来自定义您的解决方案。查看[该资源](http://msdn.microsoft.com/nhp/Default.asp?contentid=28000411 </nhp/Default.asp?contentid=28000411>)以获得更多详细的 Windows Media Player 编程信息。

##### 方便的验证器

嘿，医生，你知道有一个网站可以把我的 HTML 代码复制粘贴到一个盒子里，然后它会帮我检查的吗？我使用的是 Dreamweaver，它生成了大量甚至没有使用过的糟糕代码(主要是在使用表格时)。这只是制造了一个混乱，当我开始用 includes 写新闻和其他东西时，一切都变得很糟糕。救命啊！—约旦

Jordan，这里有一些很棒的验证器，你可以看看。这份名单是由 www.sitepointforums.com 的好心人编辑的！

*   [http://validator.w3.org/](http://validator.w3.org/)
*   [https://www.w3.org/People/Raggett/tidy/](https://www.w3.org/People/Raggett/tidy/)
*   [http://jigsaw.w3.org/css-validator/DOWNLOAD.html](http://jigsaw.w3.org/css-validator/DOWNLOAD.html )
*   [http://bobby.cast.org/bobby/html/en/index.jsp](http://bobby.cast.org/bobby/html/en/index.jsp)
*   [http://www.crossmyt.com/hc/htmlchek/htmlchek.html](http://www.crossmyt.com/hc/htmlchek/htmlchek.html )
*   [http://www.unipress.com/cgi-bin/WWWeblint](http://www.unipress.com/cgi-bin/WWWeblint)
*   [http://www.webtechs.com/html-val-svc/](http://www.webtechs.com/html-val-svc/)
*   [http://www.netmechanic.com/toolbox/html-code.htm](http://www.netmechanic.com/toolbox/html-code.htm)

这个月到此为止！传播爱，

[Dr 设计](mailto:drdesign@sitepoint.com)

## 分享这篇文章