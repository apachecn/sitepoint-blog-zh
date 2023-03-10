# 为 Diigo 创建 Chrome 扩展，第 2 部分

> 原文：<https://www.sitepoint.com/creating-chrome-extension-diigo-part-2/>

在第 1 部分中，我们介绍了一些新概念，解释了我们将如何构建扩展，并演示了 Diigo API 的使用。在这一部分，我们将创建我们的大部分助手函数，并处理错误处理。

### 错误处理

当 API 返回一个响应时，我们就要考虑所有的边缘情况并充分利用它。依赖请求每次都成功不是一个选项——我们不仅需要考虑就绪状态，还需要考虑潜在的失败。

为了清理代码并使`background.js`更加简洁，我将 Base64 对象压缩成一个缩小的字符串。现在的 background.js 文件看起来像是[这个](https://gist.github.com/Swader/7560888)。如果您按照代码进行操作，您可以从那个开始。

`xml.readyState === 4`部分检查请求是否完成。一旦完成，我们就可以检查状态代码了。只有 200 表示“成功”，其他都表示出了问题。使用可能的响应列表，我们将修改我们的代码来产生一个人类可读的错误描述。

```
var possibleErrors = {
    400: 'Bad Request: Some request parameters are invalid or the API rate limit is exceeded.',
    401: 'Not Authorized: Authentication credentials are missing or invalid.',
    403: 'Forbidden: The request has been refused because of the lack of proper permission.',
    404: 'Not Found: Either you\'re requesting an invalid URI or the resource in question doesn\'t exist (e.g. no such user).',
    500: 'Internal Server Error: Something is broken.',
    502: 'Bad Gateway: Diigo is down or being upgraded.',
    503: 'Service Unavailable: The Diigo servers are too busy to server your request. Please try again later.',
    other: 'Unknown error. Something went wrong.'
};

xml.onreadystatechange = function() {
    if (xml.readyState === 4) {
        if (xml.status === 200) {
            console.log(xml.responseText);
        } else {
            if (possibleErrors `！= = undefined){
console . error(XML . status+' '+possible errors

 `);
} else {
console . error(possible errors . other)；
}
}
}
}；` 

在上面的代码中，我们定义了一组错误消息，并将每条消息绑定到与状态代码对应的键。然后，我们检查代码是否与任何预定义的代码匹配，并将其记录在控制台中。如果请求成功，我们输出 responseText。

上面的错误处理是非常基本的，对终端用户不是很友好。改进它的选项有:错误发生时的警告框、扩展图标变灰、取消激活扩展等等。我将把那件事留给你。

我们还可以将整个 shebang 封装到一个函数中，这样它就被整齐地封装起来，全局名称空间也不会被污染:

```
var doRequest = function() {

    var xml = new XMLHttpRequest();
    xml.open('GET', url);
    xml.setRequestHeader('Authorization', auth);
    xml.send();

    xml.onreadystatechange = function() {
        if (xml.readyState === 4) {
            if (xml.status === 200) {
                console.log(xml.responseText);
            } else {
                if (possibleErrors `！= = undefined){
console . error(XML . status+' '+possible errors

 `);
} else {
console . error(possible errors . other)；
}
}
}
}；
}；

do request()；` 

### 弹出

现在我们有了 responseText，可以处理它了。我们首先需要把它变成一个合适的数组，因为它以字符串的形式对我们来说是没有用的。将`console.log(xml.responseText);`替换为:

```
var response = JSON.parse(xml.responseText);
console.log(response);
```

当您查看生成的背景页面的 JavaScript 控制台时，上面应该会产生一个 JavaScript 对象的 JavaScript 数组。

我在 Diigo 上做了一个名为“testerguy”的测试账号，里面有一些样本书签。你也许应该自己尝试一下，因为在你阅读这篇文章的时候，还不知道会发生什么。

如[第 1 部分](https://www.sitepoint.com/creating-chrome-extension-diigo-part-1/)所述，书签文件夹的结构将是:文件夹根目录下所有“bbs-root”标记的书签，以及“tags”文件夹下子文件夹中的所有标记。这是为了让用户可以通过标记“bbs-root”来区分某些书签的优先级，并确保它们出现在各自的文件夹之外，以便最快地访问。

为了正确地创建书签栏文件夹，我们需要找出所有唯一的标签，创建根文件夹，创建子文件夹“标签”并为我们知道的每个标签创建子文件夹，按此顺序。为了使测试更容易，我们将在扩展中添加一个带有刷新按钮的弹出窗口，重复 XHR 请求。更新`manifest.json` `browser_action`块像这样:

```
"browser_action": {
        "default_icon": {
            "19": "icons/19.png",
            "38": "icons/38.png"
        },
        "default_title": "Diigo BBS",
        "default_popup": "popup/popup.html"
    },
```

并在项目的根目录下创建一个名为`popup`的文件夹。在该文件夹中再创建三个文件:`popup.html`、`popup.js`和`popup.css`，内容如下:

```
<!-- popup.html -->
<!DOCTYPE html>
<html>
<head>
    <title>BBS popup</title>
    <script src="popup.js"></script>
    <link rel="stylesheet" type="text/css" href="popup.css">
    <link rel="icon" href="../icons/19.png">
</head>
<body>
<button id="refreshButton">Refresh</button>
</body>
</html>
```

```
// popup.js
var bg = chrome.extension.getBackgroundPage();

document.addEventListener('DOMContentLoaded', function () {
    document.getElementById('refreshButton').addEventListener('click', function() {
        bg.doRequest();
    });
});
```

```
/* popup.css */
#refreshButton {
    margin: 10px;
}
```

这里的 JS 代码做了以下工作:首先我们获取`background.js`脚本自动生成页面的窗口对象。与必须传递消息的内容脚本相反，弹出脚本可以直接访问后台页面代码。然后，我们将一个点击处理程序绑定到刷新按钮的点击事件，该事件从`background.js`调用我们的`doRequest`方法。

如果您现在重新加载扩展并保持生成的背景页面打开，当您单击 refresh 按钮时，您应该会看到获取的书签的重复输出。

我们现在可以继续在`background.js`中编码。

### 处理响应数组

我们通过遍历所有获取的书签，将它们存储在一个数组中，然后删除重复的标签来找到所有标签。当我们迭代时，我们可以检查所有包含标签“bbs-root”的书签，并在一个单独的变量中记录它们。让我们添加一个`process`函数:

```
var process = function(response) {
    var iLength = response.length;
    if (iLength) {
        console.info(iLength + " bookmarks were found.");
    } else {
        console.info("Response is empty - there are no bookmarks?");
    }
};
```

还有，在函数`doRequest`中，让我们替换

```
var response = JSON.parse(xml.responseText);
console.log(response);
```

用`process(JSON.parse(xml.responseText));`。

重新加载扩展将打印出所选用户找到的书签数量。让我们包含一个助手函数来帮助我们从标签数组中过滤掉重复的标签。这个函数扩展了原生 JavaScript 数组，因此您可以调用它，就像它一直都是内置的一样。将它放在 Base64 部分下，靠近文件顶部:

```
/**
 * Removes duplicate elements from the array
 */
Array.prototype.unique = function () {
    var result = [];
    var len = this.length;
    while (len--) {
        if (result.indexOf(this[len]) == -1) {
            result.push(this[len]);
        }
    }
    this.length = 0;
    len = result.length;
    while (len--) {
        this.push(result[len]);
    }
};
```

现在，让我们构建`process`函数。

```
var process = function(response) {
    var iLength = response.length;
    var allTags = [];
    var rootBookmarks = [];
    if (iLength) {
        console.info(iLength + " bookmarks were found.");
        var i = iLength;
        while (i--) {
            var item = response[i];
            if (item.tags !== undefined && item.tags != "") {
                var tags = item.tags.split(',');
                if (tags.indexOf('bbs-root') > -1) {
                    rootBookmarks.push(item);
                }
                allTags = allTags.concat(tags);
            }
        }
        allTags.unique();
        allTags.sort();
        console.log(allTags);
    } else {
        console.info("Response is empty - there are no bookmarks?");
    }
};
```

我们遍历所有的书签，如果找到的话，并且对于每个书签，我们将它们的“tags”属性转化为一个数组。然后这个数组与我们调用`unique()`删除重复的`allTags`数组合并，并按字母顺序排序。在这个过程中，我们还要注意 bbs-root 标记的书签，并将它们的引用复制到`rootBookmarks`数组中。

我们现在准备操作书签栏。

### 书签栏

首先，我们需要检查“Diigo #BBS”在书签栏中是否作为文件夹存在。如果没有，我们创建它。将以下代码直接放在`allTags.sort();`下:

```
var folderName = 'Diigo #BBS';
        chrome.bookmarks.getChildren("1", function(children) {
            var numChildren = children.length;
            var folderId;
            while (numChildren--) {
                if (children[numChildren].title == folderName) {
                    folderId = children[numChildren].id;
                    break;
                }
            }
            if (folderId === undefined) {
                chrome.bookmarks.create({
                    parentId: "1",
                    title: folderName
                }, function(folder) {
                    folderId = folder.id;
                    console.log(folderName + " not found and has been created at ID " + folder.id);
                });
            }

        });
```

我们首先获得 ID 为 1 的节点的子节点，也就是书签栏(使用 [getTree](http://developer.chrome.com/extensions/bookmarks.html#method-getTree) 可以看到这一点)。然后我们遍历它们，并将它们的标题与我们文件夹的期望名称进行比较。如果找到了文件夹，我们保存它的 ID 并退出循环。如果没有找到，我们就创建它并保存 ID。

现在我们需要找出我们的文件夹是否包含“标签”文件夹。一旦我们这样做了，我们将需要找出我们的“标签”文件夹是否包含一个子文件夹匹配我们找到的每个标签的名称。注意到这里的模式了吗？看起来我们需要一个通用的函数来检查一个书签文件夹是否包含另一个文件夹。我们也可以创建另一个助手方法来检查实际的书签。让我们将以下函数添加到我们的 background.js 文件中(例如，在`process`函数之上):

```
chrome.bookmarks.getFirstChildByTitle = function (id, title, callback) {
    chrome.bookmarks.getChildren(id, function (children) {
        var iLength = children.length;
        while (iLength--) {
            var item = children[iLength];
            if (item.title == title) {
                return callback(item);
            }
        }
        return callback(false);
    });
};

chrome.bookmarks.getFirstChildByUrl = function (id, url, callback) {
    chrome.bookmarks.getChildren(id, function (children) {
        var iLength = children.length;
        while (iLength--) {
            var item = children[iLength];
            if (item.hasOwnProperty('url') && item.url == url) {
                return callback(item);
            }
        }
        return callback(false);
    });
};
```

这些函数几乎完全相同，尽管每个函数都将自己的属性与提供的值进行比较。我们将一个用于文件夹，另一个用于书签。这些方法就像其余的`chrome.bookmarks`名称空间一样是异步的，所以我们需要在使用它们的时候提供回调。

您还可以将它们合并成一个方法，并使用第三个参数来告诉该方法我们正在寻找的属性(标题或 url)，从而更加尊重 DRY 原则。现在我将把这个问题留给您，并在下一篇关注优化的文章中再回来讨论这个问题。

现在让我们重写我们的`process`方法来使用它。

```
 chrome.bookmarks.getFirstChildByTitle("1", folderName, function(value) {
            if (value === false) {
                chrome.bookmarks.create({
                    parentId: "1",
                    title: folderName
                }, function (folder) {
                    console.log(folderName + " not found and has been created at ID " + folder.id);
                });
            }
        });
```

简洁多了，不是吗？当我们考虑进一步的步骤时，很明显我们需要区分现有标签列表和我们刚从服务器获取的标签列表。为此，我们将向原生 JavaScript 数组对象添加两个新的助手方法:`intersect`和`diff`。让我们把它们放在文件的顶部，就在`Array.unique()`所在的地方，同时，让我们把`getFirstChildByTitle`和`getFirstChildByUrl`方法也移到那里。

```
/**
 * Returns an array - the difference between the two provided arrays.
 * If the mirror parameter is undefined or true, returns only left-to-right difference.
 * Otherwise, returns a merge of left-to-right and right-to-left difference.
 * @param array {Array}
 * @param mirror
 * @returns {Array}
 */
Array.prototype.diff = function (array, mirror) {

    var current = this;
    mirror = (mirror === undefined);

    var a = current.filter(function (n) {
        return array.indexOf(n) == -1
    });
    if (mirror) {
        return a.concat(array.filter(function (n) {
            return current.indexOf(n) == -1
        }));
    }
    return a;
};

/**
 * Returns an array of common elements from both arrays
 * @param array
 * @returns {Array}
 */
Array.prototype.intersect = function (array) {
    return this.filter(function (n) {
        return array.indexOf(n) != -1
    });
};
```

最后，让我们在`background.js`文件顶部的相同位置添加一个控制台日志记录的帮助器方法:

```
const CONSOLE_LOGGING = true;
function clog(val) {
    if (CONSOLE_LOGGING) {
        console.log(val);
    }
}
```

现在可以用`clog`替换代码中所有的 console.log()调用。当您需要关闭日志记录时，只需将 CONSOLE_LOGGING 常量切换到`false`，所有输出都将停止。这在从开发转移到生产时非常有用——它引入了非常小的开销，但是减少了准备时间，因为您不需要手动搜索、注释或删除所有控制台输出。

## 第二部分结束

在这一部分中，我们构建了几个对后续工作至关重要的助手函数，并添加了一些基本的错误处理逻辑。在本系列的下一期文章中，我们将构建扩展的主体。敬请期待！ 
