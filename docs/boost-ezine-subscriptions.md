# 使用一个友好的弹出窗口来提高电子杂志订阅

> 原文：<https://www.sitepoint.com/boost-ezine-subscriptions/>

每一个顶尖的互联网营销人员都知道电子杂志广告和促销是以很少甚至零成本为你的网站带来定向流量的最佳方式。如果你经营一个网站，并向你的访问者发送电子杂志，你如何让他们注册接收？他们必须浏览你网站上的 5 个页面才能到达注册页面吗？还是你让他们一进入你的网站就能看到注册页面？

如果他们一来到你的网站就看不到加入你的电子杂志的注册表格，那么你就错过了一起获取他们的电子邮件地址的机会。使用一些简单的 HTML 和一点 JavaScript，你可以很容易地把你的注册表单放到它需要的地方，而不会破坏你的访问者在你的网站上的体验。

##### 答案是弹出窗口

我将要描述的技术是我用来吸引更多订阅者的最好的技术之一。首先，它涉及到使用 JavaScript 创建一个弹出窗口。像许多其他站长一样，我曾经认为使用弹出窗口降低了我网站的专业性。男孩是我错了！

在我的网站上添加一个简单的注册弹出窗口，使我的电子杂志订阅量从每天 20 份增加到超过 100 份！不开玩笑。

然而，访问者不希望每次访问您的站点时都被弹出窗口所困扰。这就是饼干发挥作用的地方。使用 cookies，你可以让他们的浏览器“记住”你的 ezine 注册页面是否已经显示过。如果有，则该页面不会再次显示。

##### 获取和设置 Cookie

首先，我们需要创建通用函数，这些函数将从用户的浏览器中实际获取和设置 cookies。为了通过 JavaScript 访问访问者的 cookies，我们操作了`document.cookie`值。它包含了当这个用户访问我们的网站时为他们设置的所有 cookies。需要注意的是，我们只能访问自己设置的 cookiess，而不能访问其他网站设置的 cookie。

网络浏览器将 Cookies 存储在访问者计算机上的纯文本文件中。浏览器检查其硬盘上的 cookie 文件，以查看其中是否包含我们网站的 cookie；如果有，浏览器会自动为我们加载它们。

每个 cookie 都存储为一个名称/值对。示例 document.cookie 变量如下所示:

```
myCookie=myValue;myName=Mitchell;mySite=www.devarticles.com;
```

我们将创建两个名为`setCookie`和`getCookie`的函数。它们被创建在`<script>`和`</script>`标签之间，就在我们的 HTML 页面的`</head>` 标签之前，就像这样:

```
<script language= "JavaScript"> 

function setCookie(cookieName, cookieValue, cookiePath,  

cookieExpires)  

{ 

 cookieValue = escape(cookieValue);  

 if (cookieExpires == "")  

 {  

 var nowDate = new Date();  

 nowDate.setMonth(nowDate.getMonth() + 6);  

 cookieExpires = nowDate.toGMTString();  

 }  

 if (cookiePath != "")  

 {  

 cookiePath = ";Path=" + cookiePath;  

 }  

 document.cookie = cookieName + "=" + cookieValue +  

 ";expires=" + cookieExpires + cookiePath; 

} 

function getCookie(name)  

{ 

 var cookieString = document.cookie;  

 var index = cookieString.indexOf(name + "=");  

 if (index == -1) 

 { 

 return null;  

 } 

 index = cookieString.indexOf("=", index) + 1;  

 var endstr = cookieString.indexOf(";", index);  

 if (endstr == -1) 

 { 

 endstr = cookieString.length;  

 } 

 return unescape(cookieString.substring(index, endstr)); 

} 

</script>
```

上面显示的`setCookie`函数接受要设置的 cookie 的名称、值、路径和到期日期。它是这样使用的:

```
setCookie('myCookie', 'myValue', '', '');
```

这将设置一个名为“`myCookie`”的 cookie，它将包含值“`myValue`”。`setCookie`函数的最后两个参数是 cookie 路径和到期日期。如您所见，您可以将最后两个参数保留为`''`。如果默认值为空，setCookie 函数将使用默认值。

`getCookie`函数接受要检索的 cookie 的名称，如果存在，则返回其值:

```
var c = getCookie('myCookie');
```

“`c`”现在将包含“`myValue`”。

##### 显示 Ezine 注册窗口

既然我们可以获取并设置 cookies，我们就可以实际使用`setCookie`和`getCookie`函数了。我们将创建一个函数来检查是否设置了特定的 cookie。如果是，那么我们将不会显示电子杂志弹出窗口。另一方面，如果没有设置 cookie，我们将显示 ezine 弹出页面，并设置一个 cookie 来指示注册表单已经显示。这个函数非常简单，看起来像这样:

```
function doPopup()  

{  

  var ezine = getCookie('popupShown');  

  setCookie('popupShown', 'true', '', '');  

  if(ezine == '')  

  { // Show the popup window  

    window.open('ezine.html', 'ezineWin', 'width=500,height=400');  

  }  

}
```

`doPopup`函数首先创建一个名为`ezine`的新变量，该变量将包含“`popupShown`cookie 的值。不管`getCookie()`函数是否返回值，我们都设置了“`popupShown`”cookie。我们的“`setCookie`”功能会自动设置 cookie 保存的时间段。从下面的代码片段中可以看到(取自我们上面创建的`setCookie`函数)，默认值是 6 个月:

```
 var nowDate = new Date();   

 nowDate.setMonth(nowDate.getMonth() + 6);   

 cookieExpires = nowDate.toGMTString();
```

通过在用户每次访问该页面时重置“`popupShown`”cookie，我们可以确保只要他们继续访问该网站至少每六个月一次，该弹出窗口就不会再次显示。

显示实际注册表单的最常见时间是当用户跳转到另一个页面或退出您的站点时。这两个事件都会触发 onUnload 事件处理程序。当这种情况发生时，我们需要告诉浏览器调用 doPopup 函数，所以我们像这样修改`<body>`标签:

```
<body onUnload="doPopup()">
```

现在，如果访问者跳转到我们网站的另一个页面，或者关闭浏览器窗口，那么我们的 ezine 弹出窗口将显示在一个新窗口中。

##### 结论

这是一个非常简单的方法来吸引更多的订阅者到你的电子杂志，它将为小型，中型和大型网站工作！如果您精通 ASP、PHP 或 JSP 等服务器端脚本语言，那么我建议您使用其中一种语言创建注册表单，并在访问者点击提交按钮时将他们的电子邮件地址存储在您的简讯数据库中。

## 分享这篇文章