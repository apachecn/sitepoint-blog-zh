# 博士设计-弹出窗口，域名，版权和更多

> 原文：<https://www.sitepoint.com/domain-names-copyright/>

***医生在！本周他有一长串的病人在等待——如果你想加入这个队列，请通过电子邮件将你的问题发送到 drdesign@sitepoint.com***

##### 调整已经打开的弹出窗口的大小

嘿，医生，我需要知道如何从父窗口关闭弹出窗口。

 **我有一个带有 4 个 swf 按钮的父页面。每个按钮都激活一个弹出窗口 window _blank，并且每个 window _blank 都有不同的大小。

现在，问题来了。在父页面上选择的第一个按钮会生成一个窗口，但该窗口的大小会应用于从该点开始生成的所有窗口。

我希望每个窗口都弹出到我指定的特定大小，所以我希望每次用户单击按钮时当前窗口都关闭。这将允许窗口根据按钮的弹出窗口规格来调整大小，而不是根据已经打开的窗口的规格。

我让你困惑了吗？我一直在用 javascripts 来修正这个问题，但是我不能让它工作:

```
on (press) { 

    getURL (javascript:closepopup(), _blank); 

} 

on (release) { 

    getURL 

("javascript:openNewWindow('http://www.name,html','thewin', 

'height=370,width=300,toolbar=no,scrollbars=no')", 

_blank); 

}
```** 

**救命！Afiyf**

Afiyf，虽然我是一名医生(以防你还不知道)，是的，我很容易混淆。然而，我有适合你描述的症状的 javascript 药剂。

要从父窗口关闭弹出窗口，我们只需要使用它的对象句柄来调用它。例如，如果我们像这样打开窗户:

```
myWindow = open('http://www.name,html','thewin', 

'height=370,width=300,toolbar=no,scrollbars=no');
```

我们可以从父节点关闭窗口:

```
myWindow.close();
```

然而，可能有一个更简单的方法来解决您的问题，不需要关闭打开的窗口，然后再打开它。你可以简单地调整窗口大小。

这里有一个例子:

```
<script language="javascript"> 

  function popWindow(url, width, height) { 

    popUp = open(url, 'thewin', 'toolbar=1,  

scrollbars=1, location=1, status=1, menubar=1, resizable=1'); 

        popUp.resizeTo(width, height); 

  } 

</script>  

<a href="javascript:popWindow('http://www.webmasterbase.com',10, 10)"> 

popup small</a><br> 

<a href="javascript:popWindow('http://www.promotionbase.com',600, 600)"> 

popup large</a>
```

不过请注意，4 版之前的浏览器可能不支持`resizeTo`。玩得开心！

##### 在 CGI Perl 脚本中使用 SSI

**嗨，设计博士，**

 **我已经成功地使用 SSIs 引入了标准的页眉和页脚代码。shtml 文件。在 CGI 输出中使用相同的页眉和页脚会更好。例如，我有一个 Perl 脚本，它处理一个表单并显示几个页面中的一个作为响应。理想情况下，我可以在脚本创建的 html 代码中放置一个 SSI include 标记。不幸的是，我的主机服务器不处理这些标签。有什么办法能让这行得通吗？谢谢！** 

**伊曼纽尔**

Emmanual，有两种方法可以采用:

你可以从你的 SSI 中调用你的 CGI 脚本，而不是从你的 CGI 脚本中调用你的 SSI，正如你已经提到的那样，这是行不通的。因此，您的 shtml 页面可能看起来像这样:

```
<!--#include virtual="/templates/top.html" --> 

<!--#exec cgi="/cgi-bin/script.cgi"--> 

<!--#include virtual="/templates/bottom.html" -->
```

如果您想在您的 Perl 脚本中模拟一个 SSI 包含文件，您只需在脚本中添加几行代码，它将打开您的包含文件，读取并打印出来(到浏览器)。

下面是一些 Perl 脚本，它打开一个文件，将其内容读入一个数组，然后打印出每一行。

```
open(FILE,"templates/top.html") or dienice("Can't open  

include.html: $!"); 

@ary = <FILE>; 

close(FILE); 

foreach $line (@ary) { 

    chomp($line); 

    print "$linen"; 

}
```

##### 域名的隐蔽重定向

亲爱的设计博士，上周我买了我的第一个域名；该提议包括重定向我的网址和电子邮件。但现在当我浏览我的域名时，浏览器总是显示我的免费主机帐户的名称。

 **我在网上看到可以用“伪装”来掩盖主机名

“…您可以屏蔽目的地地址(也称为伪装)，比如说，
http://www.geocities.com/mydomain/,，这样浏览器中只显示 www.MyDomain.com…”** 

它是服务器选项还是我可以用脚本来完成？如果脚本可以的话，你有没有一个，或者一个我可以找到的链接？最诚挚的问候，埃里克。

Erik，隐藏重定向是域名注册商或任何提供你的 DNS 主机的人需要为你提供的东西。这不是你在 Geocities 网站上用脚本就能实现的。这是坏消息。好消息是，你可以将你的域名转让给一个提供隐藏域名转发的注册服务商，或者使用一个提供该服务的免费 DNS 主机。

许多注册服务商确实提供隐蔽转发(也称为重定向)。我建议看看任何一家 www.enom.com 经销商(我个人不会直接从 enom 购买，因为我认为它们太贵了)。但是，把你的域名转让给另一个注册商，通常需要支付注册商延长注册一年的费用(他们想为他们的麻烦赚点钱)。如果你刚刚注册了这个域名，在最初的 60 天内你不能把它转让给另一个注册商。

对于一个免费的 DNS 主机提供商，我推荐去看看 zoneedit，他们提供一个叫做“WebForward”的免费服务，提供你需要的伪装。要将您的 DNS 托管给注册商以外的人，您需要让您的注册商将您的域名指向托管您的域名记录的 DNS 服务器。通常，您的注册商会有一个控制面板，您可以登录并为您的域设置主要和辅助名称服务器。祝你好运！

##### 用超链接打破框架

你好，医生，
我正在设计一个网络应用程序。当我的用户登录时，我会向他们显示一个页面，页面左侧有一个导航框架，右侧有一个主框架。

现在，我想在导航框架中放置一个注销链接，它会将用户带回到主页面，但目前只在主框架中打开。我该怎么做？
问候，
阿密特

好消息，Amit:这很容易做到。在注销超链接的锚点标记中，只需添加属性 target="_top "即可，例如:

```
<a href="signout.html" target="_top">sign out</a>
```

固定！

##### 保护您的网页

你好，医生，每次我做主页的时候，我的朋友或者我不认识的人都会把主页上的东西偷走！我想知道是否有某种提醒系统可以帮助我让他们远离我的页面！你能帮忙吗？
黎明

道恩，有朋友就像需要敌人的人！？严肃地说，剽窃他人的文字、艺术作品或设计是对知识产权的盗窃。不幸的是，这在今天的网络上太常见了，是互联网提供的信息自由的众多滥用之一。

您可以做几件事情来保护您页面。第一种是将页面访问权限仅限于授权人员。您个人只向您希望能够使用该网站的人(希望是您可以信任的人)授予访问权限。您需要给每个人一个用户 id 和密码，以便他们可以访问您的页面。

限制访问最简单的方法是使用 Apache 的授权模块(mod_auth)。你可能去过使用 mod_auth 的网页。它们会弹出一个对话框，要求你输入用户名和密码(并有一个勾选框，让你告诉你的浏览器记住这些细节)。这不是一个简单的实现方案，需要一整篇文章。事实上，现在就有一个:mod_auth 的 Apache 教程——读一读吧！

如果你仍然希望外界能够访问你的页面，那么要求授权是一种非常严格的方法。一些网站管理员使用的另一个技巧是禁用鼠标右键，这样访问者就不能将你页面上的图像保存到他们的硬盘上。就我个人而言，我反对这种做法，因为它删除了用户浏览器的功能，扰乱了用户的浏览器(这是用户不喜欢的)。此外，它不会阻止那些真正想窃取你的图片的人通过其他方式下载你的图片。马特·密茨凯维奇在他名为[的文章中表达了类似的想法，罗西·怀斯在](http://www.ecommercebase.com/article/35)[的文章中也表达了类似的想法。](http://www.webmasterbase.com/article/748)

除了采取技术手段，你还可以采取社会手段，联系那些抄袭你作品的“朋友”，要求他们停止使用。记住:抄袭他人的作品是违法的。我们这些网站管理员、设计师、开发者和互联网专业人士应该为自己辩护——“抄袭”,也就是知识产权盗窃，是不对的。

这星期到此为止！传播爱！
设计博士。

有问题吗？一个疑问？一个问题？今天就发邮件给[设计博士](mailto:drdesign@sitepoint.com)！

## 分享这篇文章