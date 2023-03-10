# 如何构建 Trello Chrome 扩展–API 认证

> 原文：<https://www.sitepoint.com/build-trello-chrome-extension-api-authentication/>

在 SitePoint，我们广泛使用 Trello。当然，它有自己的怪癖，可以在各个领域进行一两次改进，但在大多数情况下，它不仅彻底改变了员工的协作体验，也改变了作者和编辑的协作体验。

我最近发现自己需要从一个非会员的特定列表中导出名片。默认情况下，Trello 只支持完整的板导出到 JSON，这使得我在一个有 100 多名成员和数百张卡片的板上的标签崩溃。商店里有一小群 Trello 扩展[，然而奇怪的是，没有一个以任何方式输出列表。](https://chrome.google.com/webstore/search/trello?_category=extensions)

让我们做一个 Chrome 扩展来完成这个任务吧！如果你赶时间，只是想看看最终结果，请看[Github repo](https://github.com/Swader/TrelloHelper)获取本教程代码的最终版本。

## 构建 Chrome 扩展

我认为最好的方法是扩展，因为一个单独的应用程序只做这些导出可能太多了。此外，Trello 有一个漂亮的 API，我们可以用它来获得我们需要的一切。我还认为这将是一个回到扩展开发的很好的过渡，我已经有一段时间没有这样做了。

### 拔靴带

我将重用我值得信赖的 Chrome skel repo——一个我很久以前为 T2 构建的框架扩展，让 Chrome 扩展开发变得更容易。作为参考，如果你想看看我过去写的其他一些 Chrome 扩展教程，请参见这里的[和这里的](https://www.sitepoint.com/series/chrome-extension-for-diigo/)。

我们从将回购克隆到任何文件夹开始。

```
git clone https://github.com/Swader/ChromeSkel_a.git
```

要看它是否能工作，把它加载到 Chrome 中。进入“扩展”标签，点击“加载未打包的扩展”。如果没有该选项，请确保选中右上角的“开发人员模式”复选框。

一旦加载，它应该出现在您的扩展列表中。

![01](img/ea033c6d7a8308ca07f7a3f781c7d54b.png)

### 证明

虽然我们可以简单地用 Chrome 扩展获取屏幕上的数据并解析，但 Trello 有时被证明是不可靠的，并且往往会在高度填充的板上崩溃。这就是为什么我们将只使用 Chrome 扩展与 trello.com 域的集成来创建新的列表上下文菜单选项(一个“导出卡片”选项)，我们将在后台页面执行整个逻辑，通过 API 获取数据。

#### 密钥和秘密

要为 Trello 生成应用密钥，请在登录时访问[https://trello.com/1/appKey/generate](https://trello.com/1/appKey/generate)。这将为您提供一个密钥和秘密，您可以使用您的帐户。在本教程的剩余部分，请将{{KEY}}视为此键，并相应地替换内容。

一旦你有了你的密匙，在`scripts`子文件夹中，创建一个文件`key.js`:

```
// key.js
var APP_KEY = '{{KEY}}';
```

此时，您可以删除`fragments`和`fancy-settings`文件夹。我们不再需要它们了。

### 工作流和清单

扩展的工作流程如下:

*   假设用户打开一个 Trello 板
*   检查所述用户是否授权分机使用他的 Trello 帐户
*   如果是，继续
*   如果没有，打开带有授权按钮的设置页面，让他们完成该过程
*   授权后，自动关闭设置页面并保持扩展授权

对于一个自动打开包含某些内容的新标签的扩展，我们需要将该内容标记为[“web 可访问资源”](https://developer.chrome.com/extensions/manifest/web_accessible_resources)。

为此，创建文件夹`settings`，它将包含我们的设置页面，并更新扩展的清单，如下所示:

```
{
    "name": "Trello Helper",
    "version": "0.1",
    "manifest_version" : 2,
    "description": "Trello Helper adds some much needed functionality to Trello. The current version focuses on exporting card information from lists.",
    "background" : {
        "page" : "background.html",
        "persistent": false
    },
    "page_action" :
    {
        "default_icon": {
            "19": "icons/19.png",
            "38": "icons/38.png"
        }
    },
    "content_scripts": [
        {
            "matches": ["https://trello.com/b/*"],
            "js": [
                "lib/jquery-2.1.1.min.js",
                "scripts/main.js"
            ],
            "run_at": "document_idle"
        }
    ],
    "permissions": [
        "tabs"
    ],
    "icons": {
        "16": "icons/16.png",
        "48": "icons/48.png",
        "128": "icons/128.png"
    },
    "web_accessible_resources": [
        "settings/index.html"
    ],
    "options_page": "settings/index.html"
}
```

这其中的大部分应该很熟悉。我们设置版本，给出一些元数据，定义图标并声明一个[事件页面](https://developer.chrome.com/extensions/event_pages)，加载一些必备的内容脚本(我们需要 jQuery 用于 [Trello JS 客户端库](https://trello.com/docs/gettingstarted/clientjs.html))，最后定义“web_accessible_resources”，这样我们就可以使用我们将要构建的设置页面。我们还将扩展的效果限制在`https://trello.com/b/*`，这意味着只有 board URLs。

### 设置和授权

为了构建我们的设置页面，我们编写了一个简单的 HTML 页面。出于演示的目的，在这种情况下我将保持它非常简单。我们将使用 Foundation 对其进行样式化(目前，只针对按钮，但我们将在未来的帖子中制作更好的设置页面)，因此下载一个 [Foundation essentials 包](http://foundation.zurb.com/develop/download.html)，并将其内容解压缩到`/settings`，以便`index.html`属于该文件夹。您应该有一个如下所示的文件夹结构:

![02](img/f31a6d5227e2f4ecd79354eca0884f3f.png)

如果你有一些额外的文件夹在我的截图中看不到，请随意删除它们。创建文件`settings/js/settings.js`，暂时让它为空。

好了，言归正传。因此，假设用户要么通过进入 Extensions 选项卡中的“Options ”,要么在未经身份验证的情况下试图使用该扩展，最终到达这个页面。让我们用两个 div 构建一个相当基本的 HTML 页面——一个用于用户通过“注销”按钮验证时，另一个用于用户仍未通过验证并需要单击授权按钮时。用以下内容替换`index.html`的内容:

```
<!doctype html>
<html class="no-js" lang="en">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Trello Helper Settings</title>
    <link rel="stylesheet" href="css/foundation.css"/>
    <script src="js/vendor/modernizr.js"></script>
</head>
<body>

<div class="row">
    <div class="large-12 columns">
        <h1>Trello Helper Settings</h1>
    </div>
</div>

<div class="row">
    <div class="large-12 columns">
        <div class="panel" id="trello_helper_loggedout" style="display: none">
            <p>To get going, you'll need to authorize the extension to use your Trello account.</p>

            <p>Do it by clicking the big Authorize button below.</p>

            <a href="#" class="medium success button" id="trello_helper_login">Authorize</a><br/>
        </div>
        <div class="panel" id="trello_helper_loggedin" style="display: none">
            <p>You are already authorized. If something doesn't work, try logging out using the button below, and logging back in.</p>

            <a href="#" class="medium success button" id="trello_helper_logout">Log out</a><br/>
        </div>

    </div>
</div>

<script src="../scripts/key.js"></script>
<script src="js/vendor/jquery.js"></script>
<script src="js/settings.js"></script>
</body>
</html>
```

我们有两个 div，log in 和 log out 按钮，我们拉进我们需要的 JS。在这种情况下，我们使用 Foundation 默认包含的 jQuery，但是如果您选择在项目的其他地方使用您自己下载的 jQuery，这真的没有关系，以防您像我一样获取了一个更新的 jQuery(稍后将详细介绍)。

现在让我们加入一些逻辑。打开`settings.js`并给它这个内容:

```
function init() {

    // Message and button containers
    var lout = $("#trello_helper_loggedout");
    var lin = $("#trello_helper_loggedin");

    // Log in button
    $("#trello_helper_login").click(function () {

    });

    // Log out button
    $("#trello_helper_logout").click(function () {

    });

    if (!localStorage.trello_token) {
        $(lout).show();
        $(lin).hide();
    } else {
        $(lout).hide();
        $(lin).show();
    }
}
$(document).ready(init);
```

通过阅读[的文档](https://trello.com/docs/gettingstarted/index.html#using-client-js)，我们可以发现当一个客户端 app 被认证时，一个`trello_token`将存在于`localStorage`中。这意味着我们可以用它来指示何时展示我们的每个 div。在`init`函数中，我们获取 div，向按钮添加点击处理程序(还没有逻辑)，最后，根据`trello_token`，我们隐藏适当的 div。

例如，当用户通过身份验证时，他们会看到如下屏幕:

![03](img/efb233121b416675f8b76de13e1c90a2.png)

现在让我们获取 Trello JS 客户端。
Trello 文档并没有真正考虑 Chrome 扩展，并建议在从其域中检索 JS 客户端库时将`key`附加到 URL，如下所示:

```
<script src="https://api.trello.com/1/client.js?key=substitutewithyourapplicationkey"></script>
```

我们不能真的这样做，因为我们处理的是一个扩展的封闭环境，而且因为性能原因让它在本地可用会更有意义。因此，我们访问上面的 URL，但是没有关键参数:

```
https://api.trello.com/1/client.js
```

将该文件的内容保存到`lib/trello_client.js`中，然后确保我们的设置页面加载它，方法是将它添加到结束`<body>`标签附近的脚本部分，如下所示:

```
<script src="js/vendor/jquery.js"></script>
<script src="../lib/trello_client.js"></script>
<script src="js/settings.js"></script>
```

这将确保我们的 JavaScript 中有可用的 Trello 对象，允许我们使用[的方法](https://trello.com/docs/gettingstarted/clientjs.html)。让我们先处理注销。将注销按钮的单击处理程序更改为:

```
$("#trello_helper_logout").click(function () {
        Trello.deauthorize();
        location.reload();
    });
```

这就是取消特雷罗授权的全部内容。我们调用方法并重新加载我们所在的页面(即设置屏幕)。

现在，让我们来处理登录，这有点复杂。

```
// Log in button
    $("#trello_helper_login").click(function () {
        Trello.setKey(APP_KEY);
        Trello.authorize(
            {
                name: "Trello Helper Extension",
                type: "redirect",
                expiration: "never",
                interactive: true,
                scope: {read: true, write: false},
                success: function () {
                    // Can't do nothing, we've left the page
                },
                error: function () {
                    alert("Failed to authorize with Trello.")
                }
            });
    });
```

按照在线文档的逻辑，我们看到 Trello 对象有一个`setKey`方法，我们恰当地使用它来设置它。然后，我们调用`authorize`方法。根据文档，使用类型`popup`而不是`redirect`将要求用户在收到密钥时手动将其粘贴到应用程序中，这不是我们想要的。但是，如果我们使用重定向，页面将重定向到身份验证，并在完成后返回。当设置为假时，`interactive`字段否定弹出和重定向，取而代之的是*只检查*值是否存在。根据文档，我们需要首先使用重定向调用常规的 authorize，然后，一旦返回到设置页面，我们需要再次调用它，但是将 interactive 设置为 false，这将使它获取之前重定向提供的令牌。这有点复杂，但确实有效。

不过，还有一个问题。如果我们在设置页面打开后立即调用非交互式的`authorize`，那么我们将在页面上导致一个错误，因为授权后的重定向还没有发生。另一种方法是添加另一个按钮“确认”或类似的东西到我们的设置页面，它出现在从 Trello 重定向回我们的设置页面之后，让用户手动启动非交互授权。不过，这似乎有点像 UX 的噩梦。最终，我选择了第三种解决方案。

将下面的代码保存到`lib/hashSearch.js`中。

```
/*
As found on: http://stackoverflow.com/questions/3729150/retrieve-specific-hash-tags-value-from-url
 */

var HashSearch = new function () {
    var params;

    this.set = function (key, value) {
        params[key] = value;
        this.push();
    };

    this.remove = function (key, value) {
        delete params[key];
        this.push();
    };

    this.get = function (key, value) {
        return params[key];
    };

    this.keyExists = function (key) {
        return params.hasOwnProperty(key);
    };

    this.push= function () {
        var hashBuilder = [], key, value;

        for(key in params) if (params.hasOwnProperty(key)) {
            key = escape(key), value = escape(params[key]); // escape(undefined) == "undefined"
            hashBuilder.push(key + ( (value !== "undefined") ? '=' + value : "" ));
        }

        window.location.hash = hashBuilder.join("&");
    };

    (this.load = function () {
        params = {}
        var hashStr = window.location.hash, hashArray, keyVal
        hashStr = hashStr.substring(1, hashStr.length);
        hashArray = hashStr.split('&');

        for(var i = 0; i < hashArray.length; i++) {
            keyVal = hashArray[i].split('=');
            params[unescape(keyVal[0])] = (typeof keyVal[1] != "undefined") ? unescape(keyVal[1]) : keyVal[1];
        }
    })();
}
```

正如从一个 [StackOverflow 答案](http://stackoverflow.com/questions/3729150/retrieve-specific-hash-tags-value-from-url)中获取的一样，这个小工具帮助我们从 URL 中获取特定散列的值。

当你通过`redirect`模式向 Trello 授权时，它将重定向回它来自的页面，但在 URL 中有一个令牌。这个令牌将是 Trello JS 客户机需要的 auth 令牌。因此，有理由认为，如果我们可以检测到 URL 中存在这个令牌，我们就可以得出结论，我们正在处理来自 Trello 的重定向，因此可以自动安全地触发非交互`authorize`协议。

像这样将`hashSearch`添加到设置页面后…

```
<script src="../scripts/key.js"></script>
<script src="js/vendor/jquery.js"></script>
<script src="../lib/trello_client.js"></script>
<script src="../lib/hashSearch.js"></script>
<script src="js/settings.js"></script>
```

……`settings.js`文件最终应该是这样的:

```
function init() {

    // Check if page load is a redirect back from the auth procedure
    if (HashSearch.keyExists('token')) {
        Trello.authorize(
            {
                name: "Trello Helper Extension",
                expiration: "never",
                interactive: false,
                scope: {read: true, write: false},
                success: function () {},
                error: function () {
                    alert("Failed to authorize with Trello.")
                }
            });
    }

    // Message and button containers
    var lout = $("#trello_helper_loggedout");
    var lin = $("#trello_helper_loggedin");

    // Log in button
    $("#trello_helper_login").click(function () {
        Trello.setKey(APP_KEY);
        Trello.authorize(
            {
                name: "Trello Helper Extension",
                type: "redirect",
                expiration: "never",
                interactive: true,
                scope: {read: true, write: false},
                success: function () {
                    // Can't do nothing, we've left the page
                },
                error: function () {
                    alert("Failed to authorize with Trello.")
                }
            });
    });

    // Log out button
    $("#trello_helper_logout").click(function () {
        Trello.deauthorize();
        location.reload();
    });

    if (!localStorage.trello_token) {
        $(lout).show();
        $(lin).hide();
    } else {
        $(lout).hide();
        $(lin).show();
    }
}
$(document).ready(init);
```

您现在可以试用该扩展了。加载扩展选项卡，单击选项链接并测试身份验证和注销。一切都会好的。

## 结论

在这一部分中，我们构建了扩展的基础，通过自定义的 Foundation-powered 设置屏幕实现了身份验证，并使用了 Trello 的 JavaScript 客户端库。

在下一部分中，我们将构建扩展背后的整个逻辑，并完成所有工作。

## 分享这篇文章