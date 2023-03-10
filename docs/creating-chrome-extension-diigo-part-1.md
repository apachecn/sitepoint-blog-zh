# 为 Diigo 创建 Chrome 扩展，第 1 部分

> 原文：<https://www.sitepoint.com/creating-chrome-extension-diigo-part-1/>

书签服务现在一毛钱一打。当你的职业和爱好需要你保存数百个链接时，事情往往会变得混乱。我最终选择了 Diigo，因为它支持列表和标签——你可以给每个书签添加多个标签，也可以给列表添加每个书签。但是访问这些书签很繁琐——我首先要在一个新的标签页中打开我的 Diigo 库，然后点击我面前列表中的书签。如果我的库很复杂并且嵌套很深，那就更麻烦了——我需要通过点击左边的过滤器来进一步过滤我的搜索，我已经花费了比我应该花费的更多的时间来访问我的书签网站。

## 规划

在这个系列中，我们将创建一个 Google Chrome 扩展，它连接到 Diigo API，检索保存在那里的书签，并将它们同步到 Chrome 书签栏上的一个文件夹中。该文件夹将有几个级别:

1.  根级别。这里会有一个名为“标签”的子文件夹，以及所有用户标签为 bbs-root 的书签。
2.  标签级别。“tags”子文件夹将为用户 Diigo 库中的每个标签包含一个文件夹。进入所述文件夹列出了具有给定标签的所有帖子。

不幸的是，由于 Diigo 的 API 相当不发达，如果标签为空，就没有办法删除标签，如果书签在 Chrome 中被删除，也没有办法从 Diigo 中删除。如果这个 API 出现了，我将把它留给其他人来写后续文章。同样，Diigo API 目前还不支持列表。一旦他们添加了这个功能，用一个“Lists”子文件夹升级这个扩展应该足够简单了。

值得注意的是，谷歌在书签服务方面非常垄断。Chrome 内置了[最大写入限制](http://developer.chrome.com/extensions/bookmarks.html#property-MAX_WRITE_OPERATIONS_PER_HOUR)，这意味着每小时通过`chrome.bookmarks` API 写入的次数不能超过 100 次(创建、更新和删除)。这意味着，如果有人在 Diigo 中有超过 100 个标签/列表/书签，他们的浏览器将需要几个小时才能获取所有的标签/列表/书签，并最终满足于更少的写入(从那时起，只有更新、创建和删除应该不那么常见)。我们还将使用类似 [`let`关键字](http://www.bitfalls.com/2013/10/using-javascript-17-let-keyword-in.html)的 JavaScript 1.7 构造，所以您应该进入`chrome://flags`并启用“实验性 JavaScript”。没有`let`能做到吗？当然可以。但是我坚信，仅仅因为新技术不是无处不在，就远离新技术对开发者和整个网络都是有害的。JS 1.7 出现在 7 年前，大约是互联网时代的三个世纪。除了`let`之外，我们还要用到[【严格模式】](http://stackoverflow.com/questions/8651415/what-is-strict-mode-and-how-is-it-used/)，因为没有它`let`是不能使用的。

请注意，这意味着没有启用实验性 JS 的人将无法安装和使用该扩展，至少在 Chrome 默认启用 JS 1.7 支持之前是如此。

## 拔靴带

首先，让我们创建一个文件夹，在其中保存扩展的源代码。创建一个像这样的文件夹结构，并将 JS 和 JSON 文件留空。

```
/
    icons/
    background.js
    manifest.json
```

接下来我们需要的是填写好的`manifest.json`文件。

```
{
    "name": "Diigo Bookmark Bar Sync",
    "description": "Sync Diigo Bookmarks to Chrome",
    "version": "1.0.0.0",
    "background": {
        "scripts": ["background.js"]
    },
    "permissions": [
        "bookmarks", "https://secure.diigo.com/api/v2/"
    ],
    "browser_action": {
        "default_icon": {
            "19": "icons/19.png",
            "38": "icons/38.png"
        },
        "default_title": "Diigo BBS"
    },
    "icons": {
        "16": "icons/16.png",
        "48": "icons/48.png",
        "128": "icons/128.png"
    },
    "manifest_version": 2
}
```

如果你跟随我之前在 Sitepoint 上的 [Chrome 扩展教程，你应该熟悉所有的键和它们的值。](https://www.sitepoint.com/series/build-a-google-documents-word-count-tool/)

有三个你可能不熟悉的新东西:我们使用 JS 背景页面而不是 HTML(这两者都不相关——JS 明显更快)，我们请求“书签”权限来请求 Chrome 允许我们编辑它们，我们请求访问`https://secure.diigo.com/api/v2/`的权限来帮助我们使用[跨源 ajax](http://developer.chrome.com/extensions/xhr.html) ，或者换句话说，让我们在 Diigo 上进行 ajax 调用而不引发安全标志。

我们还使用了一个 [browser_action](http://developer.chrome.com/extensions/browserAction.html) ，这意味着我们会在 omnibar 旁边一直有一个持久的图标——而不是像页面操作那样，当我们在一个特定的页面上时，图标就在里面。

按照 manifest.json 文件中提到的大小为您的扩展制作一些图标，并将它们添加到 icons 文件夹中，或者只需[下载我的](https://github.com/Swader/ChromeSkel_a/tree/master/icons)并将它们放在那里。

此时，我们可以通过将扩展加载到 extensions 选项卡(chrome://extensions)来测试我们的扩展。确保“开发者模式”被选中，点击“加载解压缩的扩展”，然后将 Chrome 指向你放文件的文件夹。如果一切顺利，扩展的图标应该出现在 omnibar 右边的顶部栏中，如果你把鼠标放在上面，你应该会看到“Diigo BBS”弹出。

### Diigo API

要访问 Diigo 的 API，你需要注册一个 API 密钥。这将为您提供一个随机字符串，您需要将它与每个 Diigo API 请求一起发送，以便识别您自己(实际上，为了识别您的应用程序，每个应用程序将有一个不同的 API 密钥)。

Diigo 的 API 严重欠发达，但 [RESTful](http://en.wikipedia.org/wiki/Representational_State_Transfer) 意味着我们每次都调用相同的 URL 来作用于相同的对象(即书签)，但改变请求类型(获取获取、发布更新和插入、删除删除书签——尚未实现)。我们将很快对此进行更深入的解释。

本质上，与 API 通信就像向 URL 发送一个请求一样简单，其中填充了所需的参数。如果我们假设有一个名为“Joel”的用户，要获取 Joel 的 10 个书签，我们使用`https://secure.diigo.com/api/v2/bookmarks?key=your_api_key&user=joel&count=100&filter=all`

对这个请求的响应要么是出错时的错误代码，要么是 JSON 对象。如果 Joel 没有书签，这个 JSON 对象将不包含任何内容，或者将包含与这些书签上的信息相对应的数据块，就像 API 文档中的示例所示:

```
[
  {
    "title":"Diigo API Help",
    "url":"http://www.diigo.com/help/api.html",
    "user":"foo",
    "desc":"",
    "tags":"test,diigo,help",
    "shared":"yes",
    "created_at":"2008/04/30 06:28:54 +0800",
    "updated_at":"2008/04/30 06:28:54 +0800",
    "comments":[],
    "annotations":[]
  },
  {
    "title":"Google Search",
    "url":"http://www.google.com",
    "user":"bar",
    "desc":"",
    "tags":"test,search",
    "shared":"yes",
    "created_at":"2008/04/30 06:28:54 +0800",
    "updated_at":"2008/04/30 06:28:54 +0800",
    "comments":[],
    "annotations":[]
  }
]
```

一旦我们收到 JSON 数据，就很容易从这些数据中提取出我们需要的一切，但是我们马上就要谈到这一点。

API 文件说

> 身份验证使用 HTTP 基本身份验证，这是一种标准的身份验证方法，在授权请求头中包含 base64 编码的用户名和密码。

..但是既没有解释也没有演示。

它的意思如下:当您在浏览器[中访问 API 的实际 URL 时，尝试单击这个](https://secure.diigo.com/api/v2/bookmarks?key=your_api_key&user=joel&count=100&filter=all)，会提示您输入用户名和密码。

![](img/5c07169a50d0d77ae76484f4b2ed5b61.png)

如果您未能输入正确的凭据，您会得到 403 响应，这意味着您没有足够的访问权限。

![](img/dd618b839baea8b2ae5569e987f03c3a.png)

如果您有适当的凭证，您可以通过两种方式访问 URL:或者输入并提交表单，或者将它们包含在 URL 中，如下所示:`https://myusername:mypassword@secure.diigo.com/api/v2/bookmarks?key=your_api_key&user=joel&count=100&filter=all`其中`myusername`和`mypassword`应该分别由您的信息替换。如果你注册了一个 API 密匙并且拥有一个有效的 Diigo 账户，你甚至可以在你的浏览器中测试这个。您应该获得一个空数组([])或您的书签列表(或者您在 URL 的 user 参数中定义的用户的公共书签)。

那么 base64 编码是什么意思呢？这意味着我们需要通过一个额外的过滤器来运行用户名和密码，只是为了解决密码中的任何奇怪字符。字符串`myuser:mypass`将因此被转换为`bXl1c2VyOm15cGFzcw==`(这里测试一下)。

那么，我们如何将所有这些放在一起？

## 编码和发送

首先，我们需要一种对字符串进行 base64 编码的方法。鉴于 JS 没有内置这个功能，我们可以使用来自 [Webtoolkit](http://www.webtoolkit.info/javascript-base64.html) 的代码。将代码粘贴到您的`background.js`文件中。如果你愿意，你甚至可以缩小它，使它更紧凑。

接下来，我们需要告诉 API 我们想要授权的 URL。这是通过 Authorize 头来完成的，当使用 Ajax 的本地 XHR 对象时，我们可以通过`xml.setRequestHeader('Authorization', auth);`方法来完成，其中`auth`是包含授权数据的字符串。

让我们创建一个通用函数来生成这个 auth 字符串。

```
function make_basic_auth(user, password) {
  var tok = user + ':' + password;
  var hash = Base64.encode(tok);
  return "Basic " + hash;
}
```

正如您所看到的，返回的字符串将是“Basic”+从 user+pass string 计算出的任何值，作为 Base64 值。这个字符串是授权头需要的，以便访问我们将把它发送到的 URL。本质上，这与您通过浏览器访问 URL 时手动输入用户名和密码是一样的。

你可能想知道——难道我们不能像在浏览器中那样，只在 URL 的开头添加 user:pass，而忽略 Base64 业务吗？是的，但是你没有考虑到其他字符，可能会遇到一些无效请求的严重问题——例如,“@”符号表示服务器地址的开头，在密码中包含它会给我们的工作带来麻烦。

最后，让我们向 API 发出一个 XHR 请求。

```
var auth = make_basic_auth('user','pass');
var url = 'https://secure.diigo.com/api/v2/bookmarks?key=your_api_key&user=desireduser&count=100&filter=all';

xml = new XMLHttpRequest();
xml.open('GET', url);
xml.setRequestHeader('Authorization', auth);
xml.send();

xml.onreadystatechange = function() {
    if (xml.readyState === 4) {
        if (xml.status === 200) {
            console.log(xml.responseText);
        } else {
            console.error("Something went wrong!");
        }
    }
};
```

当然，用你的值替换“user”、“pass”、“your_api_key”和“desireduser”。

如果我们现在用一个打开的背景页面重新加载我们的扩展(在扩展屏幕中单击`_generated_background_page.html`以查看我们的扩展的背景页面和控制台错误报告(如果有的话))，我们应该看到一切都工作正常——即背景页面的控制台中应该没有错误，并且应该有“[]”(一个空数组)或类似下图的内容:

![](img/69e6f65ac2a22fb0a496e8c9d2a3530f.png)

## 第一部分结束

在这一部分中，我们引导了我们的扩展，解释、实现并演示了 Diigo API 调用。在第 2 部分中，我们将编写大部分扩展。

## 分享这篇文章