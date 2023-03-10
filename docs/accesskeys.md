# 使用访问键很容易

> 原文：<https://www.sitepoint.com/accesskeys/>

当有人建议他们在自己的网站上添加访问密钥时，相当多的网站开发者仍然会感到害怕。别害怕。这篇文章很短是有原因的。如果你想使用它们，访问键是如此容易添加，你会奇怪为什么你从来没有这样做。

##### 定义的访问键

那么，什么是访问键呢？对于外行来说，它们是人们通过按 ALT (PC)或 CTRL (Mac ),然后按键盘上适当的键(由您通过 accesskey 参数定义)来立即跳转到 HTML 页面的特定部分的一种方式。

对于行动不便的人来说，它们特别有用，他们不用鼠标，在电脑上做任何动作都用键盘。访问键允许他们快速方便地在你的网页内容中跳转。身体健康的用户也会发现它们作为快捷方式同样有用。

我们稍后将讨论 HTML，但是在此之前，值得注意的是 accesskeys 需要您添加的额外内容。如果人们不知道访问键是什么，那么拥有访问键是没有用的——你需要提供一个列出你的访问键的页面。

记住这一点，让我们举一个非常简单的例子。网站`www.buyfoos.com`向网络开发者出售“Foos”。它有一个导航区域和一个内容区域。导航包括主页、关于 Foos、购买 Foos、网站地图和反馈表。那个导航少了什么？

##### 访问密钥详细信息页面！

这是主页的代码。我加粗了对我们重要的部分:

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" 

"https://www.w3.org/TR/html4/loose.dtd"> 

<html> 

<head> 

<title>BuyFoos.com</title> 

<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1"> 

<link href="css/mainstyle.css" rel="stylesheet" type="text/css"> 

</head> 

<body> 

<div id="navigation"> 

<a href="index.html">Home</a> |  

<a href="about.html">About Foos</a> |  

<a href="buy.html">Buy Foos</a> |  

<a href="sitemap.html">Site Map</a> |  

<a href="feedback.html">Feedback</a> |  

<a href="access.html">Accesskey Details</a> 

</div> 

<div id="content"> 

Welcome to buyfoos.com. Etc. Etc. 

</div> 

</body> 

</html>
```

现在，让我们添加一个访问键:

```
<div id="navigation"> 

<a href="index.html" accesskey="q">Home</a> |  

<a href="about.html">About Foos</a> |  

<a href="buy.html">Buy Foos</a> |  

<a href="sitemap.html">Site Map</a> |  

<a href="feedback.html">Feedback</a> |  

<a href="access.html">Accesskey Details</a> 

</div> 

<div id="content"> 

Welcome to buyfoos.com. Etc. Etc. 

</div>
```

仅此而已。PC 上的 ALT+Q，或者 Mac 上的 CTRL+Q，现在会把用户带到主页。不错吧。

但是有些事情不太对劲。访问键可以是你喜欢的任何东西，但是它们真的应该有某种意义。在这一点上，标准是很好的，但是，唉，到目前为止似乎还没有任何标准。然而，我浏览了一些政府网站，以及 W3C 本身，看看这些组织在他们的网站上做了什么。这个建议绝不是一成不变的，如果你认为有更好的方法，那就一定要忽略它，但是相当可靠的“最佳实践”标准似乎是:

H =回家。S =跳过导航，直接进入内容。
K =转到访问键定义页面。
1 到 0(例如 1，2，3… 8，9，0) =主导航项，在我们的例子中:
1 =关于 Foos
2 =购买 Foos
3 =站点地图
4 =反馈

因此，如果我们重新审视我们的 HTML，它可能看起来像这样:

```
<div id="navigation"> 

<a href="index.html" accesskey="h">Home</a> |  

<a href="about.html" accesskey="1">About Foos</a> |  

<a href="buy.html" accesskey="2">Buy Foos</a> |  

<a href="sitemap.html" accesskey="3">Site Map</a> |  

<a href="feedback.html" accesskey="4">Feedback</a> |  

<a href="access.html" accesskey="k">Accesskey Details</a> 

</div> 

<div id="content"> 

<a href="#skip" accesskey="s"></a> 

<a name="skip"></a> 

Welcome to buyfoos.com. Etc. Etc. 

</div>
```

好了，给你。请注意，要进入页面的特定区域，我只需在它所指向的命名锚点旁边插入一个无文本链接。

最后，为了进一步阅读，请查看这篇文章，它将 accesskeys 带到了 CSS 的下一个层次。

## 分享这篇文章