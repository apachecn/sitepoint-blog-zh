# WordPress JSON REST API

> 原文：<https://www.sitepoint.com/wordpress-json-rest-api/>

WordPress 在网络上占有很大的市场份额。REST APIs 是一项正在发展的技术，对开发者来说是一个巨大的机会。知道如何创建 API 以及如何使用它们会给你带来很大的优势。REST API 可以在任何地方使用。在移动应用程序、前端(web 应用程序)或任何其他可上网的设备上。

也许你的客户已经有了一个运行在 WordPress 上的网站，并且还想要一个移动应用程序。您可以使用自定义代码并依赖数据库来创建 API，但这会导致很多挫折。可能他们很着急，想尽快拿到 API。在 WordPress 中，就像安装一个插件一样简单。

在本文中，我们将对 [JSON REST API](https://wordpress.org/plugins/json-rest-api/) 插件进行概述。我们将介绍这个插件是如何工作的，以及 REST 架构的基本原理。

## 准备

在 WordPress 插件目录上抓取最新版本的 [JSON REST API](https://wordpress.org/plugins/json-rest-api/) 。安装并激活它。现在您已经有了一个可以使用的 REST API。您需要一个 REST 客户端来测试和探索它。这项工作有许多工具。不要把自己局限在这四个工具上。还有很多其他的 REST 客户端。

第一个是[卷曲](http://curl.haxx.se/)。它是一个命令行工具，可以向给定的端点发送不同的请求。它不仅与 REST API 相关，而且是一个通用的 **HTTP 请求**工具。其他文章中的许多测试示例都是使用这个工具完成的。它是通用的 HTTP 工具，可以翻译成任何编程语言。

我常用的一个工具是 PHPStorm REST 客户端。因为我在开发时主要使用这个 IDE，所以使用一个可以更快访问的工具对我来说更容易。许多其他 IDE 都集成了 REST 客户端，使得 REST API 的开发更加容易和快速。

第三个也是我极力推荐的是[邮差](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm?hl=en)。这是我用过的最简单、最直观的 REST 客户端。遗憾的是，只能作为 Chrome 扩展使用。如果你使用 Firefox，有一个很好的扩展叫做 [RESTClient](https://addons.mozilla.org/en-US/firefox/addon/restclient/) 。

![Postman and WordPress](img/5812322c218c0b084c33bb59bf7ecfa5.png)

## 探索 REST 架构

你要做的第一件事是检查这个插件是否工作，它的端点在哪里，包含关于模式的所有信息的基本 URL，所有的资源(集合)和路由。插件在标题上添加了一个名为“` Link `”的新字段，所以请在响应的标题中检查它。如果你使用 Postman 或任何其他 GUI 客户端，只需检查标题。如果您使用 cURL，请在主页上执行以下命令:

```
curl -I http://yoursite.com/
```

标题选项仅获取标题。在我的例子中，端点是`local.wordpress.dev/wp-json`。如果您在设置中将永久链接保留为它的默认值，那么您可能会得到这样的链接值:`http://local.wordpress.dev/?json_route=/`。

它只改变重写规则，而不改变任何其他重要的变化。在这两种情况下，一切都是一样的。

`index endpoint`(或 API 端点)是起点。您可以从那里探索整个 API。它提供了关于 API 的所有信息。它给出了关于集合、实体以及它们如何映射到路线的信息。向您的端点发出一个 GET 请求，看看它返回什么。在 REST 客户端上，这很容易，因为您正在处理 GUI 界面。让我们看看如何使用 cURL。

```
curl http://local.wordpress.dev/wp-json/
```

在我的例子中，它返回一个大的 JSON 文件，如下所示。

```
{
"name": "Local WordPress Dev",
"description": "Just another WordPress site",
"URL": "http://local.wordpress.dev",
"routes": {
"/": {
"supports": [
"HEAD",
"GET"
],
"meta": {
"self": "http://local.wordpress.dev/wp-json/"
}
},
"/posts": {
"supports": [
"HEAD",
"GET",
"POST"
],
"meta": {
"self": "http://local.wordpress.dev/wp-json/posts"
},
"accepts_json": true
},
"/posts/<id>": {
"supports": [
"HEAD",
"GET",
"POST",
"PUT",
"PATCH",
"DELETE"
],
"accepts_json": true
},
"/posts/<id>/revisions": {
"supports": [
"HEAD",
"GET"
]
}
},
"authentication": [],
"meta": {
"links": {
"help": "https://github.com/WP-API/WP-API",
"profile": "https://raw.github.com/WP-API/WP-API/master/docs/schema.json"
}
}
}
```

事实上，回复太长了，所以我删除了大部分。索引路由(端点)给出了关于站点标题、描述及其 URL 的信息。您在 API 上可以找到的所有路线也都映射在“路线”字段中。**帖子**，**用户**，**媒体**，**页面**都是收藏。集合是一组实体。一个实体是一篇文章或者一个带有 ID 的页面。

假设我们想看最新的帖子。集合的端点是`api_endpoint/posts`。这将返回最新的帖子。实体是以给定的 ID 作为参数的`api_endpoint/posts/ID`。使用 cURL，命令应该是:

```
curl http://local.wordpress.dev/wp-json/posts
```

并且:

```
curl http://local.wordpress.dev/wp-json/1
```

检索数据是一件好事，但有时我们想发布数据。

## 发布、更新、删除

我想分享一些我在尝试发布或删除数据时遇到的问题。首先，你需要认证你自己。有三种方法可以进行身份验证。

第一个是使用饼干。这是一种在处理 API 时没有广泛使用的方法。当用户在网上导航时，页面倾向于在用户侧存储数据。这里的 cookie 方法有点不同，它使用了 [nonces](http://codex.wordpress.org/WordPress_Nonces) 方法。

第二个是 OAuth 方法。这个插件实现了 OAuth 的 1.0a 版本。这种方法被广泛使用。谷歌、脸书、Twitter 和 Flickr 使用 OAuth 进行第三方认证。OAuth 是第二版，但是他们选择使用 1.0a 版。对于第二版有很大的争议。一些大公司出于不同的原因没有转移到第二版，但主要是担心安全性。

第三个是基本认证。使用这种方法，您必须在每次请求时发送您的用户名和密码。REST 测试的客户端工具通常会自己实现这个方法。这种方法主要用在开发的时候。很少用于生产。在生产中，考虑使用 OAuth。

不幸的是，基本认证插件并没有经常更新。他们把所有的精力都集中在 OAuth 插件上。这个插件不能在[wordpress.org](https://wordpress.org/)上找到，只能在它的 GitHub 库中找到。你必须手动安装它。在插件目录中执行以下命令:

```
git clone https://github.com/WP-API/Basic-Auth basicAuth
```

或者访问 GitHub 页面，下载插件并通过 FTP 手动安装。

然后转到仪表板并激活它。这个插件在非 Apache 服务器上有一些问题。由于我主要使用的是 VVV 的，这是一个流浪安装，它有问题，因为 VVV 是 nginx 自带的。如果您的配置是默认的，请更改 permalink 配置。那对我有用。

![Permalink configuration for REST API](img/f0ec0c99af6c39edb6ccbf28cf9db2ff.png)

现在我们已经设置了身份验证，是时候创建一些帖子了。Postman 实现了基本的身份验证。如果您使用 Postman，请输入用户名和密码，它会在每次发出请求时记住 base 64 编码。要查看它是否工作，请在“index_endpoint/users/me”中发出请求。它返回关于你的信息。

如果您尝试在不使用基本身份验证或任何其他身份验证方法的情况下访问此 URL，API 将返回:

```
[
{
"code": "json_not_logged_in",
"message": "You are not currently logged in."
}
]
```

让我们尝试通过 cURL 发出请求，并包含基本的 Auth 方法:

```
curl --user admin:password http://local.wordpress.dev/wp-json/users/me
```

`--user`选项产生了`admin:password`参数，它基本上就是纯文本的 64 位编码。cURL 通过添加一个额外的字段为自己构建基本 Auth 的请求头。这是它在我的案例中添加的内容:

```
Authorization: Basic YWRtaW46cGFzc3dvcmQ=
```

这是当我尝试使用给定的凭据访问我的配置文件时返回的内容。

```
{
"ID": 1,
"username": "admin",
"name": "admin",
"first_name": "",
"last_name": "",
"nickname": "admin",
"slug": "admin",
"URL": "",
"avatar": "https://gravatar.com/avatar/06e92fdf4a9a63441dff65945114b47f?s=96",
"description": "",
"registered": "2014-07-17T22:59:59+00:00",
"roles": [
"administrator"
],
"capabilities": {
"switch_themes": true,
"edit_themes": true,
"activate_plugins": true,
"edit_plugins": true,
"edit_users": true,
"edit_files": true,
"manage_options": true,
"moderate_comments": true,
"manage_categories": true,
"manage_links": true,
"upload_files": true,
"import": true,
"administrator": true
},
"email": false,
"meta": {
"links": {
"self": "http://local.wordpress.dev/wp-json/users/1",
"archives": "http://local.wordpress.dev/wp-json/users/1/posts"
}
}
}
```

让我们试着删除一个帖子。要删除一篇文章，我们必须知道它的网址。它的网址是`api_endpoint/posts/ID`。将 ID 替换为 1 或所需的任何其他文章 ID。不要忘记你应该使用认证来删除一些东西。

```
curl --user admin:password -X DELETE http://local.wordpress.dev/wp-json/posts/4
```

**如果使用邮递员或类似方式发送上述 URL(http://local.wordpress.dev/wp-json/posts/4 ),并从 URL 字段旁边的下拉列表中选择“删除”请求方法。**

这是我得到的回报:

```
{
"message":"Deleted post"
}
```

发布新记录(例如 post)也很容易。创建一个包含两个字段的新 JSON 文件。一个用于标题，一个用于内容。

```
{
"title": "This is a post",
"content_raw": "This is some content"
}
```

将新数据发送到我们想要创建的实体的集合中。请记住，在发布新实体时，您应该在集合端点中发布它。这里我们再次使用了基本认证。`-X POST`使该请求成为发布请求。`--data`捕获该文件夹中的文件，并将其作为原始数据发送。

```
curl --user admin:password -X POST  http://local.wordpress.dev/wp-json/posts --data @data.json
```

## 进一步探索

不要限制自己。WordPress REST API 团队也有一些与这个插件紧密集成的其他工具。他们有一个[客户端 Cli](https://github.com/WP-API/client-cli) 、[客户端 JS](https://github.com/WP-API/client-js) 和一个[客户端 PHP](https://github.com/WP-API/client-php) 来与你的 API 一起工作。也可以浏览他们的文档以获得更深入的技术信息。

知道如何使用这个插件是一回事，但在这种情况下，我能给出的最好建议是:“不要限制自己”。关于休息的信息太多了。在 SitePoint 上，我们有一个关于[由](https://www.sitepoint.com/best-practices-rest-api-scratch-introduction/)[维托·塔迪亚](https://www.sitepoint.com/author/vtardia/)从头开始构建 REST API 的精彩系列。

## 结论

在 WordPress 上创建 REST API 很容易。如果你使用 WordPress，你不必从头开始创建一个。事实上，索引端点会告诉你关于这个插件的一切以及如何使用它。我们讨论了集合、实体以及如何使用不同的动词(GET、POST、PUT、DELETE)操作一些数据。

开发时的一个重要方面也是身份验证，所以我们也讨论了这一点。在为生产进行开发时，请考虑 OAuth 1.0a。您也可以使用基本的 Auth，但是 OAuth 已经过测试，并得到了许多大公司的支持。

如何看待 WordPress 上的 REST APIs？它会与手工制作的 API 竞争吗？你还知道其他关于 REST 的 WordPress 插件吗？请在下面的评论中告诉我们。

## 分享这篇文章