# 为 Diigo 创建 Chrome 扩展，第 3 部分

> 原文：<https://www.sitepoint.com/creating-chrome-extension-diigo-part-3/>

在第 1 部分中，我们引入了一些新概念，并构建了一个扩展的框架版本，为安装和测试做好了准备。[第 2 部分](https://www.sitepoint.com/creating-chrome-extension-diigo-part-2/)然后带我们看了一些助手方法和错误处理，以及解析我们从 Diigo 得到的结果并过滤出唯一的标签。

在本系列的第 3 部分中，我们将使用到目前为止所做的一切来编写扩展的主体。

## 准备

我清理了我们在前面部分制作的`background.js`文件，所以继续从 [Github](https://gist.github.com/Swader/7833383) 获取它的内容。它本质上是相同的，只是重新格式化和稍微重组了一下。

## 书签事件的侦听器

我们要做的第一件事是为书签事件添加一些侦听器。具体来说，当书签创建、更改或删除发生时，我们希望 Diigo 知道它。

```
chrome.bookmarks.onCreated.addListener(function (id, node) {
    chrome.bookmarks.get(node.parentId, function (parent) {
        if (parent !== false) {
            chrome.bookmarks.get(parent[0].parentId, function (grandparent) {
                /** @namespace grandparent.title */
                if (grandparent[0] !== false && grandparent[0].title == "Tags") {
                    // Bookmark was created in proper location, send to Diigo
                    doRequest(node, parent[0].title);
                }
            });
        }
    });
});

chrome.bookmarks.onRemoved.addListener(function (id, removeInfo) {
    // To be added when API supports it
});

chrome.bookmarks.onChanged.addListener(function (id, changeInfo) {
    // To be added when API supports it
});
```

底部的两个监听器只是占位符，因为 Diigo 还不支持这个功能。我听说他们的 API 很快就会升级，所以我们还是把它们放在那里。

`onCreated`监听器首先检查创建的书签节点是否有父节点。如果是，那么它会检查该父代的父代的名字——如果这个名字是“Tags ”,我们就知道我们得到了正确的文件夹，我们需要提交给 Diigo。现在，这个函数假设你没有其他的带有“Tags”的双亲书签作为祖父母，但是，理论上，这是可能发生的。为了检查这一点，我们还需要为 Diigo 主文件夹添加另一个父级检查，但是我将把这留给您做功课。

然后我们用两个参数调用`doRequest`:实际创建的书签节点，以及创建它的标签文件夹的名称。显然，我们需要这些数据来告诉 Diigo 创建哪个书签，给它哪个标签。但是为什么是`doRequest`？这不是我们的“获取”功能吗？是的——但是你马上就会看到，我们将对它进行修改，使它能够处理我们的扩展的 POST 和 GET 动作。

接下来我们需要做的是将这些参数添加到我们的`doRequest`函数中，并让它对它们的存在或不存在做出反应，就像这样:

```
var doRequest = function (bookmarknode, tag) {
    var xml = new XMLHttpRequest();

    if (bookmarknode !== undefined) {
        if (tag === undefined) {
            console.error("Tag not passed in. Unaware of where to store bookmark in Diigo. Nothing done.");
        } else {
            // Bookmark node was passed in. We're doing a POST for update, create or delete
            // Currently only create is supported
            var uriPart = encodeURI("url=" + bookmarknode.url + "&title=" + bookmarknode.title + "&tags=" + tag);
            xml.open('POST', rootUrl + uriPart);

            xml.setRequestHeader('Authorization', auth);
            xml.send();

            xml.onreadystatechange = function () {
                if (xml.readyState === 4) {
                    if (xml.status === 200) {
                        clog("Successfully created new bookmark in Diigo");
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
 }

}否则{

xml.open('GET '，root URL+" & count = 100 & filter = all & user = "+user)；
XML . setrequestheader(' Authorization '，auth)；
XML . send()；

XML . onreadystatechange = function(){
if(XML . ready state = = = 4){
if(XML . status = = = 200){
process(JSON . parse(XML . responsetext))；如果(可能的错误)

 `！= = undefined){
console . error(XML . status+' '+possible errors

 `);
} else {
console . error(possible errors . other)；
console . error(XML . status)；
}
}
}
}；
}
}；` 

如果提供了`bookmarknode`和`tag`参数并且它们是有效的，那么我们执行 XHR 请求的方式几乎与获取书签的原始请求相同，只是有一个关键的区别——这一次，我们将它变成一个 POST 请求，并将标题、标签和书签名称添加到 URL 中。这就是所需要的——现在 Diigo 可以接受我们的 POST 请求并做出相应的反应。这就是 RESTful API 设计的美妙之处。

## 根书签

现在让我们保存所有的 BBS-root 书签。在`process`函数的初始循环中，我们已经将它们放在了一个数组中，但是我们没有对它们做任何事情。让我们改变这一点。

在第 2 部分中，我们确保“Diigo #BBS”文件夹存在。一旦我们确定这一点，我们就可以开始创建根书签——它们有一个家，我们可以将它们放在那里。

从这里重写`process`函数的一部分:

```
var folderName = 'Diigo #BBS';
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

到

```
var folderName = 'Diigo #BBS';
        chrome.bookmarks.getFirstChildByTitle("1", folderName, function(value) {
            if (value === false) {
                chrome.bookmarks.create({
                    parentId: "1",
                    title: folderName
                }, function (folder) {
                    clog(folderName + " not found and has been created at ID " + folder.id);
                    processTagsFolder(folder, allTags);
                });
            } else {
                processTagsFolder(value, allTags);
            }
        });
```

如您所见，我们添加了一个对`processTagsFolder`函数的新调用。这个函数将“Diigo #BBS”文件夹作为第一个参数传入，将所有标签的数组作为第二个参数传入。不管这个方法以哪种方式执行——不管“Diigo #BBS”文件夹是否预先存在，我们都可以把我们的根书签创建逻辑放在里面。

```
 /**
             * Creates the Tags master folder if it doesn't exist
             * Initiates the check for tag subfolders
             * Creates ROOT bookmarks
             * @param rootNode
             * @param tagsArray
             */
            function processTagsFolder(rootNode, tagsArray) {

                // Get all current root bookmarks, if any
                chrome.bookmarks.getChildren(rootNode.id, function (currentRoots) {
                    var crl = currentRoots.length;
                    var ignoredUrls = [];
                    var rootNumOrig = rootBookmarks.length;
                    if (crl) {
                        var bAmongThem = false;
                        var rootNum = rootNumOrig;
                        // Iterate through all the current items in the root folder
                        while (crl--) {
                            // Check if current item is a URL bookmark, not a folder
                            if (currentRoots[crl].hasOwnProperty('url')) {
                                // Iterate through downloaded bookmarks to see if it's among them
                                bAmongThem = false;
                                while (rootNum--) {
                                    if (rootBookmarks[rootNum].url == currentRoots[crl].url) {
                                        // Found among existing!
                                        bAmongThem = true;
                                        if (rootBookmarks[rootNum].title != currentRoots[crl].title) {
                                            // Does title need updating?
                                            chrome.bookmarks.update(currentRoots[crl].id, {
                                                title: rootBookmarks[rootNum].title
                                            });
                                        }
                                        // Ignore this URL when later adding the downloaded root bookmarks
                                        ignoredUrls.push(rootBookmarks[rootNum].url);
                                        break;
                                    }
                                }
                                if (!bAmongThem) {
                                    // Does not exist in downloaded - needs to be deleted from browser
                                    chrome.bookmarks.remove(currentRoots[crl].id);
                                }
                            }
                        }
                    }

                    // At this point, we know we removed all the bookmarks that are no longer in our Diigo account
                    // Now let's add those that are left

                    while (rootNumOrig--) {
                        if (ignoredUrls.indexOf(rootBookmarks[rootNumOrig].url) === -1) {
                            chrome.bookmarks.create({
                                url: rootBookmarks[rootNumOrig].url,
                                title: rootBookmarks[rootNumOrig].title,
                                parentId: rootNode.id
                            });
                        }
                    }

                });
}
```

简而言之，我们在这里做的是获取所有当前的根书签，看看它们是否在新下载的书签中，如果不在，就删除它们(这意味着它们在 Diigo 中没有被标记为 bbs-root)，最后，我们添加所有其他的书签。如果你尝试一下，这将会非常有效。

如果 Tags 文件夹不存在，我们还需要创建它。在最后一位的正下方添加以下代码:

```
chrome.bookmarks.getFirstChildByTitle(rootNode.id, 'Tags', function (tagsFolder) {
                    if (tagsFolder === false) {
                        chrome.bookmarks.create({
                            parentId: rootNode.id,
                            title: "Tags"
                        }, function (folder) {
                            processTags(folder, tagsArray);
                        });
                    } else {
                        processTags(tagsFolder, tagsArray);
                    }
                });
```

显然，我们已经创建了另一个函数，无论 Tags 文件夹是否预先存在，它都会被调用。我们来定义一下`processTags`。

## 处理标签

```
 /**
             * Creates all non-existent tag subfolders.
             * Removes all tag subfolders that do not have any bookmarks.
             * @param tagsFolder
             * @param tagsArray
             */
            function processTags(tagsFolder, tagsArray) {

                // Remove all unused tag subfolders
                chrome.bookmarks.getChildren(tagsFolder.id, function (currentTagSubfolders) {
                    var numCurrentTags = currentTagSubfolders.length;
                    if (numCurrentTags > 0) {
                        var currentTags = [];
                        var currentTagsIds = {};
                        var cTag;
                        while (numCurrentTags--) {
                            cTag = currentTagSubfolders[numCurrentTags];
                            currentTags.push(cTag.title);
                            currentTagsIds[cTag.title] = cTag.id;
                        }
                        var diff = currentTags.diff(allTags, false);
                        var numUnused = diff.length;
                        if (numUnused) {
                            while (numUnused--) {
                                chrome.bookmarks.removeTree(currentTagsIds `]);
}
}
}
})；

//创建必要的标签子文件夹
var num tags = tags array . length；
while(numTags-){
let title = tags array[numTags]；
chrome . bookmarks . getfirschildbyTitle(tags folder . id，title，function(tag folder){
if(tag folder = = = false){
//需要创建
chrome . bookmarks . create({
parentId:tags folder . id，
 title: title 
 }，function(folder){
addAllBookmarksWithTag(folder)；
})；
} else {
addAllBookmarksWithTag(tag folder)；
}
})；
 } 
 }` 

上面的函数过滤掉了`AllTags`数组(我们从 Diigo 中获取的新标签列表，并使之唯一)和当前存在于“tags”文件夹中的标签子文件夹之间的差异。这种差异代表了 Chrome 书签栏中的那些文件夹，它们在用户的 Diigo 库中不再有任何成员。因此，这些文件夹将从 Chrome 中移除。

清理完成后，该函数遍历最近从 Diigo 下载的标签列表，并创建这些子文件夹，之后调用`addAllBookmarksWithTag`函数。

## 将书签添加到标签子文件夹

```
 /**
             * Adds all bookmarks with given tag to provided folder, if they don't exist.
             * Looks at URL for comparison, not title.
             * @param folder
             */
            function addAllBookmarksWithTag(folder) {

                chrome.bookmarks.getChildren(folder.id, function (children) {
                    var urls = {};
                    if (children.length > 0) {
                        var numChildren = children.length;
                        var subItem;
                        while (numChildren--) {
                            subItem = children[numChildren];
                            urls[subItem.url] = subItem;
                        }
                    }

                    var i = iLength;
                    var key = false;
                    while (i--) {
                        var item = response[i];
                        var tags = item.tags.split(',');
                        if (tags.indexOf(folder.title) > -1) {
                            // Bookmark belongs in folder

                            if (urls.hasOwnProperty(item.url)) {
                                key = item.url;
                            }

                            if (urls.hasOwnProperty(item.url + "/")) {
                                key = item.url + "/";
                            }

                            if (key) {
                                // Bookmark already exists in folder
                                if (urls[key].title != item.title) {
                                    // Title needs an update
                                    clog('Title updated: "' + urls[key].title + '" to "' + item.title + '"');
                                    chrome.bookmarks.update(urls[key].id, {title: item.title});
                                }
                            } else {
                                // Bookmark needs to be created
                                chrome.bookmarks.create({
                                    parentId: folder.id,
                                    title: item.title,
                                    url: item.url
                                }, function (bookmarkItem) {
                                    clog("Created Item: " + bookmarkItem.title + " on " + bookmarkItem.url);
                                });
                            }
                        }
                    }

                });
            }
```

最后，我们将书签添加到它们各自的标签文件夹中。我们首先构建一个对象，包含来自每个文件夹的当前书签 URL 作为键，书签节点本身作为值。

该函数遍历原始结果集，分割标签并检查它当前处理的书签是否属于当前文件夹。“/”技巧是由于 Diigo 有时会在 URL 上随机添加斜线。我们将在后续的“优化”文章中处理这个问题。如果书签属于该文件夹，并且已经在其中，则该函数检查书签标题是否需要更新。如果是，它就更新它。如果书签不在文件夹中，而它应该在，那么它被创建。

## 结论

最后，我们完成了大部分的扩展。还有一些问题需要解决，但大部分工作已经完成。你可以从 [Github](https://gist.github.com/Swader/7836294) 下载`background.js`的最终版本。

在第 4 部分中，我们将着重于让人们登录到扩展中并使用他们自己的帐户，我们将允许添加自定义 API 密钥以防配额问题，并且我们将稍微优化我们的代码。敬请期待！

## 分享这篇文章 
