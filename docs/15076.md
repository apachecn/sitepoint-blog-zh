# 灾难恢复设计–从 Javascript 到 PHP

> 原文：<https://www.sitepoint.com/dr-design-javascript-php/>

***候车室已满！[加入队列](mailto:drdesign@sitepoint.com) —好的设计博士下次再见！但是现在，他正卷起袖子，向今天的病人伸出援手……***

##### 梦幻翻车

你好！
**你能告诉我为什么我在 Dreamweaver 中制作的所有翻转都不能在 Mac Explorer v.5 中使用吗？有什么办法能让它们起作用吗？非常非常感谢…**
**来自墨西哥城的阿曼多。**

Amando，我没有看过 Dreamweaver 的翻转 Javascript。有时候越简单越好。我非常喜欢 Andy Mathews (Gravity Digital)的简单翻转 Javascript，他不久前在 SitePoint 论坛上发布了这个脚本。也许这次你会更走运。

```
<script language="javascript"> 

<!-- 

// Pre-load images 

if (document.images) { 

  image1on = new Image(); 

  image1on.src = "images/about_us_on.gif"; 

  image1off = new Image(); 

  image1off.src = "images/about_us_off.gif"; 

} 

function changeImages() { 

  if (document.images) { 

    for (var i=0; i<changeImages.arguments.length; i+=2) { 

      document[changeImages.arguments[i]].src =  

      eval(changeImages.arguments[i+1] + ".src"); 

    } 

  } 

} 

// --> 

</script>
```

下面是它在 HTML 中的用法示例:

```
<a href="about_us.shtml" onmouseover="changeImages('image1',  

'image1on')" onmouseout="changeImages('image1', 'image1off')"> 

<img name="image1" src="images/about_us_off.gif" border=0></a>
```

##### 追踪亲属

嘿，医生，
**我设计并建立了一个专门的家庭照片网站。我在家用电脑上使用 Apache 服务器软件。**

我的问题是:我怎样才能跟踪访问这个网站的人？我已经设置了一个登录名和密码，这应该可以限制访问…然而，随着在家里的口耳相传，我确信我会从远房亲戚那里得到很多点击，鉴于我是一个好奇的人，我想知道谁登录了。

我已经设置了一个登录名和密码，所以我无法通过这种方式区分用户……我还通过 HTML 代码设置了一个屏幕来询问用户名，然后在用户的 PC 上放置一个 cookie，这样他们就可以知道他们访问网站的频率(最终，可以看到网站上是否有任何更新)。
**加里**

加里，我知道你从哪里来。没有什么比关注家庭八卦和了解亲戚们在忙什么更有趣的了。我可能会选择用 PHP 或 ASP 编写一点服务器端脚本。但是，您可能没有使用任何服务器端脚本，所以让我们看看是否可以策划一个巧妙的计划，使用 Javascript 从客户端执行访问者监控！

我将使用的技术假设您有一个 Web 日志分析工具，或者您是真正的极客并且通读了您的日志文件。查看可用的[日志分析工具](http://dmoz.org/Computers/Software/Internet/Site_Management/Log_Analysis/)列表。在 Linux 服务器上运行的流行软件包包括 Webalizer、Analog 和 AWStats。你还可以找到在 Windows 下运行的日志分析程序。

现在，为了好玩…

手法有点“锋芒毕露”。被称为“清晰的 gif 间谍软件”或“网络信标”，一些人对在线广告商使用这种技术来跟踪访问者感到不满。但这正是我们想要实现的。如果你运行的是一个商业网站，并且使用了这种方法，那么在你的隐私政策中披露你跟踪使用 cookies 的访问者是明智的(并且符合在线隐私道德)。

“网络信标”使用 Javascript 将查询字符串附加到一个清晰(透明的 1×1 像素)gif 的地址上，该地址被加载到网页中。Web 服务器在提供明文 gif 时会忽略查询字符串，除非 HTTP GET 请求会记录在您的网站访问日志中。当你分析你的日志时，你将能够通过清晰 gif 的请求追踪你的访问者。例如，您将拥有如下条目:

```
[10/Oct/2002:03:16:42 +0000] "GET /clear.gif?name=Mary HTTP/1.1"
```

下面是一个页面的代码，它通过函数`setGif()`跟踪任何“被烹饪”的访问者，并且还将设置一个带有用户名的 cookie，当他们提交表单要求他们输入姓名时。

```
<html> 

<head> 

<script language = "javascript"> 

// set the cookie expiry date to be in twelve months 

expireDate = new Date; 

expireDate.setMonth(expireDate.getMonth() + 12); 

// convert the expiry date to GMT format 

cookieDate = expireDate.toGMTString(); 

// declare userName as global 

var userName = ""; 

// setCookie() sets a cookie with the userName 

// submitted by the form userForm 

function setCookie() { 

  userName = document.userForm.name.value;   

  cookieString = "userName=" + userName + "; expires=" +  

  cookieDate + ";"; 

  document.cookie = cookieString; 

  alert('Welcome ' + userName + '!'); 

  // call function setGif() so that we track this visitor's page view 

  // now that we know their name! 

  setGif(); 

  return false; 

} 

// setGif() will embed the clear gif into the document 

// and append the cookied userName so that we can track 

// the user in our website access log. 

function setGif() { 

  if(document.cookie != "") { 

    userName = document.cookie.split("=")[1]; 

    imageTag = '<img src="clear.gif?name=' + userName + '" width=1  

    height=1>'; 

    document.write(imageTag); 

  } 

} 

</script> 

</head> 

<body> 

<form name = "userForm"> 

Select a username: 

<input type=text name="name" onBlur="return setCookie()"> 

<input type="submit" value="submit" onClick="return setCookie()"> 

</form> 

<script language="javascript"> 

  setGif(); 

</script> 

</body> 

</html>
```

##### 可变分配蓝调

你好，医生，
**我有个小问题要问你。我是 PHP 编程新手，很快发现自己陷入了困境。通过 PHP 连接到 MySQL 数据库后，我使用下面的 PHP 代码将行插入到现有的表中……**

**T2 `// If an artist has been submitted,  
   // add them to the database.  
   if ($addArtist == "Add") {  
     $sql = "INSERT INTO Artists SET  
     Name='$AddName',  
     bio='$AddBio';";  
     if (@mysql_query($sql)) {  
       echo("Artist Added");  
     } else {  
       echo("<p>Error adding submitted order: " .  
            mysql_error() . "</p>");  

     }  
   }`**

 **但是，我还需要同时将行添加到同一数据库下的不同表中。我用这种方式添加它没有成功(只能识别一个 sql 语句，不能两个都识别)…** 

```
 `// If an artist has been submitted,  

    // add it them the database.  

    if ($addArtist == "Add") {  

      $sql = "INSERT INTO Artists SET  

      Name='$AddName',  

      bio='$AddBio'";  

      $sql = "INSERT INTO Pictures SET  

      fileSRC='$AddPicture'";  

      if (@mysql_query($sql)) {  

        echo("Artist Added");  

      } else {  

        echo("<p>Error adding submitted order: " .  

             mysql_error() . "</p>");  

      }  

    }`

I've looked everywhere and have not found a solution.  Please let me know if you can help.

Thank you,

David
大卫，听到有人找到了通往开源启蒙和智慧的道路，这总是让我的心欢欣鼓舞。你很快就会成为一名 PHP 大师！

下面是第二个代码示例的问题。当您将第二个查询字符串赋给变量$sql 时，这将“覆盖”该变量以前的值，即您所赋的第一个查询字符串。这里有一个例子:

```
$myString = "foo";  

$myString = "bar";  

echo $myString;
```

这会输出:
 `bar`
但是，如果我写:
 `$myString = "foo";  

echo $myString;  

echo '<br>';  

$myString = "bar";  

echo $myString;`
这会输出:
 `foo  

bar`
回到您的代码中，您可能想要尝试类似这样的内容:

```
if ($addArtist == "Add") {  

   // first insert the record into Artists  

   $sql = "INSERT INTO Artists SET  

        Name='$AddName',  

        bio='$AddBio'";  

   if (@mysql_query($sql)) {  

      echo("Artist Added");  

   } else {  

      echo("<p>Error adding submitted order: " .  

            mysql_error() . "</p>");  

   }  

   // next insert the record to Pictures  

   $sql = "INSERT INTO Pictures SET  

       fileSRC='$AddPicture'";  

   if (@mysql_query($sql)) {  

      echo("Picture Added");  

   } else {  

      echo("<p>Error adding submitted order: " .  

             mysql_error() . "</p>");  

   }        

}
```

应该可以了！

##### 瞄准一个框架

**博士，
在一个框架集中，我创建了一个链接，链接指向一个应该在另一个框架中加载的页面。链接来自“框架 3”，链接的页面应该出现在“框架 2”——但它没有！！！！！！**

无论我在“目标”属性中输入什么，页面仍然出现在“框架 3”中，这样就移除了我的导航栏。我试过 _top、_parent、default、_blank 等。在“目标”字段中。似乎什么都不管用。救命啊！！！
仁恩

瑞娜，我从你使用感叹号中感觉到一些挫败感。有时，尽管我可能会不顾一切地完成一些编码，但当我想把头靠在显示器上时，我知道是时候放松咖啡因，到外面去，重新发现阳光是什么，并补充维生素 d。

如果您想将一个框架作为另一个框架的目标，您需要使用目标框架的名称。假设我们有以下框架集:

```
<frameset rows="50%,*">   

  <frame name="frame1" src="navigation.html">   

  <frame name="frame2" src="body.html">   

</frameset>
```

navigation.html(加载到帧 1 中)具有将在帧 2 中打开的以下链接:

```
<a href="somepage.html" target="frame2">click here    

to open the page inside frame2.</a>
```

维奥拉。

##### 灯光，摄像机，开拍！

医生，我在 Swish 中为我的网站设计了一些非常时髦的翻转/导航按钮，并把它们导出为。swf (flash)文件。我就是不能让我的 HTML 编辑器(Homesite 4.5)把它们叫出来:(我想:

```
<a href "main.html">img src"img/main.swf" width="200"    

height="32" alt"" border="0"></a><br>
```** 

会做，但运气不好。现在我又回到了无聊的老样子。感谢任何建议，
瑞安

啊啊！那个锚定标签已经损坏了。记住标签总是采用这种形式:

```
<tagType attribute=value attribute2=value2 attribute3=value3>
```

字符串值必须用引号括起来。例如:

```
<img src="myImage.jgp" width=100 height=100 border=0 alt="Just an image">
```

无论如何，这并没有真正让我们更接近解决问题。我很高兴地承认我在这一点上力不从心。你可能想在你的电影中加入一些动作。[这里有一个看起来很有前途的教程](http://www.flashkit.com/tutorials/3rd_Party/Swish/Using_Ac-swish-66/index.php) —祝你好运！如果你需要一些非常方便的 Swish 用户的快速建议，不要忘记在 SitePoint 论坛上提问。

##### 包括页眉和页脚

如何在 html 页面中包含页眉和页脚？
谢谢，
温斯顿

大多数 Web 服务器都支持服务器端包含(SSI)。为了能够在 html 页面中使用 SSI，您需要使用文件扩展名。shtml，以便 Web 服务器知道如何解析该文件并处理 include 指令。下面是一个使用 SSI 包含不同文件的页眉和页脚的示例:

```
<!--#include file="header.html" -->   

<p>Some regular old HTML goes here.</p>   

<!--#include file="footer.html" -->
```

不要忘记用。shtml 扩展！您也可以在 ASP 文件中使用 SSI include 指令。这提醒了我，两个月前我在诊所里见过一个类似的病例。你可以[看看我当时给的建议](http://www.WebmasterBase.com/article/843/21)——希望有帮助。

咨询时间已经结束，但请确保在下个月手术再次开始时，你的问题得到了回答！

## 分享本文

```