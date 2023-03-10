# 友好的 URL

> 原文：<https://www.sitepoint.com/friendly-urls/>

所以我希望美国的每个人都熬过了 2007 年的夏令时危机！我不知道你，但我甚至没有感觉到:)

我最近在浏览论坛，当我寻找一个主题来写博客时，我通常会这样做，我看到了 forumposters 的一篇题为“[干净和描述性的 url 的](https://www.sitepoint.com/forums/showthread.php?t=470130 "Clean and descriptive urls")”的帖子。在这篇文章中，forumposters 问道:

“为了让你的 URL 看起来更好，你们这些 CF 开发者做了什么？如果大家愿意分享，我希望看到许多例子和选项”

我认为这对我来说是一个很好的话题，因为我在这个问题上有着丰富的历史和近期经验。

在很长一段时间里，搜索引擎会用查询字符串处理 URL，也就是动态 URL，问号(？)在 URL 中，有所不同。大多数情况下，包含这些查询字符串的页面会比不包含这些字符串的页面排名靠后。所以如果你有网址:

```
http://www.example.com/books/index.cfm?category=coldfusion&author=forta
```

与以下格式的 URL 相比，它在搜索结果中的排名会更低:

```
http://www.example.com/books/coldfusion/forta/
```

所以对于开发者来说，尝试让他们的 URL 变得“干净”是一个相当大的任务，这意味着他们想去掉问号(？)、和符号(&)以及等号(=)。这将导致什么是众所周知的搜索引擎友好的网址，并允许您的网站取得更好的排名。这里有一个快速切线；谷歌表示，它将更好地索引动态网址，因此搜索引擎友好网址的问题正在消失，但现在它更多地变成了用户友好网址的问题。这意味着开发人员、客户和用户希望其他用户可以理解、记住和分享的 URL 比那些更长的动态 URL 更容易。总的来说，友好 URL 的概念更容易被接受。

那么，在创建友好的 URL 时，你有什么选择呢？谢天谢地，根据你的设置、时间和能力，有很多选项可供选择。将动态 URL 转换为友好 URL 的最广泛接受的方法是使用重写模块。这个重写模块将完成把一个友好的 URL 转换成一个动态 URL 的繁重工作，这样你的代码就可以像预期的那样运行。所以使用我们上面的例子，重写模块会变成

```
http://www.example.com/books/coldfusion/forta/
```

到…里面

```
http://www.example.com/books/index.cfm?category=coldfusion&author=forta
```

我们的用户只会看到友好的 URL，但服务器和我们的代码会看到动态的 URL，所有相关的 URL 变量也会被创建供我们使用。

根据您的 web 服务器，您可能已经安装了一个可以使用的重写模块。如果你在 Apache 上运行 ColdFusion，那么你可以随意使用流行的
[mod _ rewrite](http://httpd.apache.org/docs/2.2/misc/rewriteguide.html)对于 IIS 上的用户来说，这需要更多的工作，因为 IIS 不支持开箱即用的重写。值得庆幸的是，尽管有收费和免费的选择，IIS 仍然和 Apache 一样酷。我通常推荐的费用选项是 Helicon 的[isapirevwrite](http://www.isapirewrite.com/)。这是一个 ISAPI 插件，它的行为非常类似于 mod_rewrite，事实上版本 3 使用了完全相同的规则！他们有 1 个站点的免费版本，或者花 99 美元就可以获得无限制 IIS 站点的许可证。如果你预算有限，也可以使用 Ionic 的 ISAPI 重写过滤器,它完全免费，而且非常健壮。

我不打算详细介绍这些产品，因为它们都有自己的小方法，但是大多数重写模块使用某种形式的正则表达式将 URL 从一种形式转换成另一种形式。您可能需要做的大部分翻译只需要基本的正则表达式知识就可以轻松完成。

但是，如果您没有 Apache，并且无法在 IIS 中安装插件来获得这种强大的重写功能，该怎么办呢？你没有桨吗？因为一些公司政治而受苦？一点也不！在我走这条路之前，我只想说，以我的经验来看，重写模块要健壮得多，并且通常会比我们在这里讨论的任何模块执行得都好。然而，说它并不罕见，看到一个处理友好的 URL 问题的编程方式。

404 方法是一种没有被广泛接受并且在开发人员中通常不被接受的方法。这是你为你的站点设置一个 404 页面的地方，并使用一些编码实践(比如 switch case 语句)来检查你是否有一个匹配，然后包含适当的代码使其工作。我不打算走这条路，因为我有很多理由不推荐它。最大的原因是，它真的弄乱了你的网站的统计数据，因为一切都在日志中被报告为 404 错误。我的理解是，许多搜索引擎正在从他们的索引中删除 404 页，但是谁需要搜索引擎呢，是的，试着向客户解释一下！

另一种选择是使用我称之为网关脚本的东西。这个网关脚本允许你从一个中心位置运行所有的东西，使用一些奇特的编码，你可以创建那些友好的 URL，而不会出现一堆 404 错误。事实上，我确信你已经在你阅读的一些 ColdFusion 博客中看到了这一点！大多数使用美妙的 [BlogCFC](http://blogCFC.riaforge.com "BlogCFC") 的用户已经用这种方法有一段时间友好的 URL 了。在 BlogCFC 应用程序中，URL 通常如下所示:

```
http://ray.camdenfamily.com/index.cfm/2007/4/3/Did-you-know-about-the-Log-Viewer-Filter
```

在上面的 URL 中，Ray 在他的站点的根 index.cfm 中运行网关脚本。然后使用一点 CF 代码，他就能够提取“/2007/4/3/你知道那个日志查看器过滤器”并使用它做一些事情。使用网关脚本的好处是 index.cfm 实际上存在于服务器上，因此您的 web 服务器不会将其作为 404 错误返回，并且它还会将整个页面记录为唯一的 URL，因此您仍然可以看到您的用户正在访问哪些页面等。那么他是怎么做到的呢？让我们来看看他的代码:

```
 /**
 * Parses my SES format. Demands /YYYY/MMMM/TITLE or /YYYY/MMMM/DDDD/TITLE
 * One line from MikeD
 *
 * @author Raymond Camden (ray@camdenfamily.com)
 * @version 1, June 23, 2005
 */ 
function parseMySES() {
	//line below from Mike D.
	var urlVars=reReplaceNoCase(trim(cgi.path_info), '.+.cfm/? *', '');
	var r = structNew();
	var theLen = listLen(urlVars,"/");

	if(len(urlVars) is 0 or urlvars is "/") return r;

	//handles categories
	if(theLen is 1) {
			urlVars = replace(urlVars, "/","");
			r.categoryName = urlVars;	
			return r;
	}

	r.year = listFirst(urlVars,"/");
	if(theLen gte 2) r.month = listGetAt(urlVars,2,"/");
	if(theLen gte 3) r.day = listGetAt(urlVars,3,"/");
	if(theLen gte 4) r.title = listLast(urlVars, "/");
	return r;
} 
```

你会注意到的第一件事是，Ray 的博客和函数要求你的 URL 采用特定的格式。这是使用网关脚本时的一种常见做法，但是只需做一点工作和多一点代码，您就可以使您的脚本和 URL 更加通用。

上述功能的基础是接受 CGI。ColdFusion 返回的 PATH_INFO 变量，并解析出“.”之后的所有内容。cfm”并使用正斜杠“/”作为分隔符。电脑生成图像。PATH_INFO 变量在脚本名称后返回额外的路径信息。因此，在上面来自 Ray 博客的示例中，这将是“/2007/4/3/Did-you-know-about-Log-Viewer-Filter”，这是 index.cfm 之后的所有内容。PATH_INFO 变量实际上会返回脚本名称和额外的路径信息，因此它看起来类似于“index . cfm/2007/4/3/Did-you-know-about-Log-Viewer-Filter”。Ray 通过剥离。cfm 和它前面的所有内容，然后解析字符串，如这行代码所示:

```
var urlVars=reReplaceNoCase(trim(cgi.path_info), '.+.cfm/? *', '');
```

一旦他有了一个“干净”的变量集，他就可以用正斜杠“/”作为分隔符把它当作一个列表。你可以从上面的脚本中看到，他只是做了一个简单的列表长度检查来决定哪些变量可以被设置和使用。

使用电脑生成图像。PATH_INFO 变量是创建任何搜索引擎和大多数人都会喜欢的友好的 URL 的好方法。现在，您可以做其他“事情”来帮助删除。cfm 文件扩展名，但这些通常需要 web 服务器端做更多的工作，不值得额外的努力。如果你需要那种“外观”,我推荐你选择上面提到的一个模块。

现在你有了，一些选项可以让你开始走上更友好的 URL 之路。

## 分享这篇文章