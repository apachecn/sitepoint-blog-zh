# 偏执狂:跨站点脚本

> 原文：<https://www.sitepoint.com/cross-site-scripting/>

他们在监视你，你知道吗？他们已经观察你很长时间了，想方设法地干扰你和你的网站。

好吧，你认为你的代码是安全的，嗯？为您的产品安装了最新的便捷加密技术，您的补丁程序和服务包都已准备就绪。但是你知道吗？你在你的网站上犯了一个严重的错误，而你自己可能都不知道。

##### 问题是

如果你在 querystring 上获取信息，然后在页面上显示出来，哦，兄弟，你有问题了…你已经成熟了，可以使用跨站点脚本了。除非你已经知道我要说什么，否则请继续读下去。

假设您将一个用户的名字从一个页面传递到另一个页面，然后在页面上显示带有`Response.Write`的 querystring 值，您正在给自己设置灾难！看看这个无辜的查询字符串:

```
http://whatzit.com/whatthe/WebForm1.aspx?fName=Lumpy 
```

你试图让可怜的 Lumpy 的用户体验更明亮一点，所以你很好地执行了下面的代码:

```
Response.Write(\"Hello \" + Request.QueryString(\"fName\")); 
```

当您运行此代码时，您会得到以下输出:

```
Hello Lumpy 
```

下面是产生“Hello Lumpy”输出的查询字符串:

```
http://whatzit.com/whatthe/WebForm1.aspx?fName=Lumpy 
```

但是如果我是邪恶的埃迪，我会趁你不注意的时候偷偷在你身上放一点 JavaScript！

```
http://whatzit.com/whatthe/WebForm1.aspx?fName= <script language='javascript'>alert("beotch");</script> 
```

你猜怎么着？如果你把这个粘贴到你的网址上，浏览器会弹出一个漂亮的小框告诉你“be otch”…呃，不管那是什么意思！

这到底是怎么发生的？我的天啊。使用跨站点脚本漏洞，您在 JavaScript 中执行的任何代码都可以通过管道进入您的站点。

查看此网址…

```
http://whatzit.com/whatthe/WebForm1.aspx?fName=<script language='javascript'>window.navigate('http://mrPron');</script> 
```

好吧。现在变得可怕了……'但是，'你在想，'那又怎么样？我的意思是，谁在乎我是否可以把 JavaScript 粘贴到别人的网站上..?'嗯，等一下。看看下面这一行…它会让你思考。

```
<a href="http:// whatzit.com/whatthe/WebForm1.aspx?fName=<script language='javascript'>window.navigate('http://mrPron');</script>">Mole Hair Removal</a>
```

我给某人发送了一个看似有效的 URL 链接，事实上，也许他们*确实*访问了该网站，但他们也获得了其他东西……我在链接中嵌入的讨厌的小 JavaScript。

很糟糕，是吧？想象一下，有人发送你的网址，然后最终用户知道的下一件事是，他们面对着一张描述各种不可提及的和在线赌场出价的奇怪图片…你明白了。一些你不想让你奶奶在期待小强尼照片的时候看到的东西…

那么，如何防止跨站脚本呢？呵呵，我还以为你不会问呢！

##### 解决方案

首先，让我们搞清楚几件事——要聪明，不要愚蠢。遵循这些简单的规则:

1.  If you’re expecting a particular type of data, check to ensure that it is what you’re expecting.
2.  Check the length — if you expect a fName of only 25 characters, chop extra characters off and drop ’em. Don’t give evil Eddie any sort of chance to do a lot of damage.
3.  寻找无效字符——比如`<`或`>`或者无处不在的`;`。不要只拿你从查询字符串中得到的任何东西；质疑你所有的投入。不要相信任何人。真的。

好了，这里有一小段代码——显然，您会想充实它以适合您的特定站点:

```
private bool checkValueQS(string QS) 

{ 

Regex r = new Regex("[^0-9a-zA-Z]"); 

// Find a single match in the string. 

Match m = r.Match(QS);  

if (m.Success)  

{ 

   return true; 

} 

return false; 

}
```

这不是火箭科学——在概念上很简单。我所做的只是尝试对数字 0-9 和有效字母 a-z 和 A-Z 进行非匹配。其他任何内容都是禁止的。然后，您可以重定向试图传入' ol 脚本标记的恶意最终用户。

下次你想检查 Lumpy 的时候试试这个:

```
private void Page_Load(object sender, System.EventArgs e) 

{ 

if (Request.QueryString["fName"] != null) 

{                 

if (checkValueQS(Request.QueryString["fName"].ToString()) == false) 

  { 

   Response.Write("Hello " + Request.QueryString["fName"]); 

   } 

   else 

   { 

   Response.Write("Hello... JERK!"); 

    } 

} 

}
```

注意，如果最终用户试图传递除数字或字母之外的任何东西，他们会被告知`Response.Write("Hello... JERK");`

小心跨站脚本。这是一个可以很容易解决的严重问题。记住，当涉及到用户输入时，你永远不要过于偏执…即使他们正在看着你。

## 分享这篇文章