# 用谷歌检查你的拼写

> 原文：<https://www.sitepoint.com/check-your-spelling-with-google/>

我天生懒惰……这是正确的拼写吗？我现在不用打开 Word，或者震惊地翻阅一本真正的字典，只需将这个单词输入谷歌，然后查看页面顶部的“你是说:”条目。果然传宗接代其实就是“传宗接代”。谢谢谷歌。

因此，为了展示 Google 的 Web 服务 API 在您的。NET 应用程序，这里有一个小应用程序来抓取和显示谷歌中的拼写纠正:

1.你需要[获得许可密钥](http://www.google.com/apis/)才能使用谷歌服务。这是自动的，对所有人都是免费的，但是你只能进行 1000 次查询(或者拼写检查！)一天。

2.从[http://api.google.com/GoogleSearch.wsdl](http://api.google.com/GoogleSearch.wsdl)入口点向您的项目添加一个 Web 引用。

3.将 3 个控件放到你的窗体上:一个标签，一个文本框和一个按钮。

4.在 button click 事件中，创建一个 Google search 对象，并使用 doSpellingSuggestion 方法返回您在表单的 Label 控件中输入的单词的正确拼写:

 `Google.GoogleSearchService search = new Google.GoogleSearchService();
Label1.Text = search.doSpellingSuggestion("xxxxxxxxx",TextBox1.Text);`

就是这样。拼写，感谢谷歌:)

毫无意义？也许吧。酷吗？是的。

## 分享这篇文章