# 使用 HTML 标记

> 原文：<https://www.sitepoint.com/working-with-html-markup/>

最近我也遇到了这种情况。当用户通过 Google 访问网站时，我需要一种方法来突出显示 HTML 块中的关键字。这样，你可以帮助你的用户快速找到他们在谷歌搜索的信息。

然而，一个简单的字符串。替换功能不能解决问题。很明显，它也将取代 HTML 标记中的任何关键词，因此会删除链接或图像。

例如，以关键字“sitepoint”为例，我希望用一些 HTML“site point”来替换它。如果我的图像名称中有“sitepoint”，我会以![](img/c54bbdb18cb818841397f1b8c5684e10.png) sitepoint.jpg" >结束。不是我想要的。

因此，我编写了一个小函数，首先删除一个字符串中的所有 HTML 标签，然后在替换完成后替换它们。我希望它有些用处，亲爱的读者们:

 `private string highlightText(string text, string keyword, string highlightColour) {
//ok strip the tags, but keep them safe
System.Collections.ArrayList a = new
System.Collections.ArrayList();
string temp = text;
//ok, find an < while (temp.IndexOf("<") != -1) { int start = temp.IndexOf("<"); int end = temp.IndexOf(">");
//ok. remove
a.Add(temp.Substring(start,end-start+1));
temp = temp.Substring(0,start)+"`

## 分享这篇文章