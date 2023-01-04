# jQuery 插件注册表的返回

> 原文：<https://www.sitepoint.com/the-return-of-the-jquery-plugin-registry/>

对于所有的 jQuery 插件开发者，我们有一些激动人心的消息要宣布。新的 jQuery 插件注册网站最近上线了。这个新网站取代了旧的插件注册表，并将“把重点放在纠正一些困扰旧网站的问题上”。该网站有一个非常简单的界面，允许插件消费者执行基于关键字的搜索。


新网站采用了各种技术，包括 WordPress、Apache、MySQL、PHP、 [Node.js](http://nodejs.org/ "node.js") 和 [GitHub](https://github.com/ "GitHub") 。注册表本身是 GitHub 存储库的集合，每个存储库包含一个或多个 jQuery 插件。如果你想知道注册表是如何工作的，网站的[源代码](https://github.com/jquery/plugins.jquery.com "jquery/plugins.jquery.com")也可以在 GitHub 上找到。

## 发布插件

将插件发布到注册表很简单。首先，编写(并测试)您的代码。接下来，为你的插件取一个独特的名字。名称不能在注册表中重复，所以您可能需要做一些规划。注意，jQuery 团队明确禁止占用包，所以您必须有一个合法的插件。下一步是为 GitHub 存储库设置一个接收后挂钩。每次推送时，post-receive 挂钩都会使您的存储库与 jQuery registry 服务器进行通信。GitHub 为快速[设置接收后挂钩](https://help.github.com/articles/post-receive-hooks "Post-Receive Hooks - github:help")提供了一个有用的教程。您的接收后挂钩应设置为联系以下 URL。

```
http://plugins.jquery.com/postreceive-hook
```

设置挂钩后，您必须创建一个包清单文件。该文件应该位于您的存储库的根目录下，并命名为`*your_project_name*.jquery.json`。顾名思义，清单文件应该包含 [JSON](http://cjihrig.com/blog/json-overview/ "A JSON Overview") 数据。如果您熟悉 Node.js 开发，清单文件将看起来非常类似于 Node 的`package.json`文件。在注册网站上也可以找到[清单文件](http://plugins.jquery.com/docs/package-manifest/ "jQuery Plugin Package Manifest Specification | jQuery Plugin Registry")的具体规范。最后一步是标记您的版本，并将其推送到 GitHub。这可以使用下面显示的命令来完成。此示例假设您发布的是版本 1.0.0。

```
git tag 1.0.0
git push origin --tags

```

一旦你按下按钮，其他一切都会自动为你处理。希望你会利用新的注册网站。如果您对 jQuery 插件非常感兴趣，您甚至可能希望为这个项目做出贡献。

## 分享这篇文章