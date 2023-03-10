# 使用 Nodejitsu 实现 Node.js 应用程序的云部署

> 原文：<https://www.sitepoint.com/cloud-deployment-of-node-js-applications-with-nodejitsu/>

SitePoint 明天将发布新书 [Jump Start Node.js](https://www.sitepoint.com/premium/library "The Jump Start Series: Fast books for busy people.") 。为了庆祝这本书的发布，JSPro 今天将为您带来一篇特殊的 Node.js 文章。今天的文章将教你如何使用 [Nodejitsu](http://nodejitsu.com/ "Node.js cloud products and services | Nodejitsu Inc.") 在云中部署 Node.js 应用程序。

## 关于 Nodejitsu

Nodejitsu Inc .是一家云计算公司，总部位于纽约市中心。更具体地说，他们提供了专注于 Node.js 的平台即服务(PaaS)产品。Nodejitsu 还提供了在云中轻松部署应用程序的工具。他们还提供了一个名为 [Haibu](https://github.com/nodejitsu/haibu "nodejitsu/haibu") (日语为 hive)的免费工具，你可以用它在自己的硬件上生成 Node.js 云。虽然我觉得有义务至少提到 Haibu，但今天我们将针对 Nodejitsu 生产堆栈。

## 注册 Nodejitsu

在使用 Nodejitsu 云之前，您需要注册一个帐户。幸运的是，Nodejitsu 为个人开发者提供了一个免费的计划。要注册账户，请前往 [Nodejitsu 主页](http://nodejitsu.com/ "Node.js cloud products and services | Nodejitsu Inc.")。找到“免费试用 Nodejitsu”按钮并单击它。接下来，创建一个新用户名，并提供您的电子邮件地址。您可以选择添加计划用 Nodejitsu 部署的应用程序类型的描述。

提交注册后，您将收到一封包含进一步说明的确认电子邮件。您需要安装 Nodejitsu 的命令行实用程序`jitsu`。要用`npm`安装`jitsu`，使用以下命令。在 Unix 系统上，您应该使用`sudo`来执行命令。

```
npm install jitsu -g
```

接下来，使用`jitsu`确认您的帐户。您收到的确认电子邮件应该包含类似于下面所示的命令。运行该命令后，系统会提示您为新帐户创建密码。创建密码后，您就可以开始部署应用程序了！

```
jitsu users confirm your_username confirmation_code
```

## 创建 Nodejitsu 应用程序

JSPro 最近发表了一篇关于在 Node.js 中创建一个非常基本的 HTTP 服务器的文章。我们将使用这个简单的 web 服务器作为我们的第一个 Nodejitsu 应用程序。web 服务器的代码如下所示。将此代码添加到一个文件中，并保存为“web_server.js”。

```
var http = require("http");
var server = http.createServer(function(request, response) {
  response.writeHead(200, {"Content-Type": "text/html"});
  response.write("<!DOCTYPE "html">");
  response.write("<html>");
  response.write("<head>");
  response.write("<title>Hello World Page</title>");
  response.write("</head>");
  response.write("<body>");
  response.write("Hello World!");
  response.write("</body>");
  response.write("</html>");
  response.end();
});

server.listen(80);
```

接下来，使用`jitsu`部署应用程序。执行此操作的命令如下所示。

```
jitsu deploy
```

### package.json 文件

在部署应用程序之前，它需要一个“package.json”文件。在 Node.js 环境中，项目应该包含一个“package.json”文件，该文件指定关于项目的元数据。正如文件扩展名所暗示的，“package.json”包含了 [JSON](http://cjihrig.com/blog/json-overview/ "A JSON Overview") 数据。典型的包文件将包含包的名称、依赖信息、版本信息和任何其他相关的配置数据。

当您尝试部署一个不包含“package.json”文件的项目时，`jitsu`将在完成一个简短的向导后为您创建一个文件。下面显示的清单包含相关的向导数据。`App name`是您的应用程序的名称。您将使用`subdomain`在线访问您的申请，因此它应该包括您的用户名。`scripts.start`字段表示用于启动应用程序的脚本文件。`version`字段代表您的应用程序的版本。最后，我们不需要为`engines.node`指定一个值，因为任何 0.8 版本都足以满足我们的目的。

```
App name:  web_server
subdomain:  webserver.your_username
scripts.start:  web_server.js
version:  1.0.0
engines.node:  (0.8.x)

```

一旦您完成了向导，您的 package.json 文件应该如下所示。

```
{
  "scripts": {
    "start": "web_server.js"
  },
  "version": "1.0.0",
  "engines": {
    "node": "0.8.x"
  },
  "name": "web_server",
  "subdomain": "webserver.your_username"
}
```

最后，键入`yes`确认设置。

## 结论

如果一切配置正确，您的 web 服务器现在应该运行在 Nodejitsu 云中。您可以通过导航到`http://*subdomain*.jit.su`来访问服务器。您应该用“package.json”文件中的`subdomain`值替换`*subdomain*`。比如我的服务器运行在[http://web server . cjihrig . JIT . su](http://webserver.cjihrig.jit.su/ "Hello World Page")。

当然，这只是 Nodejitsu 的冰山一角。我建议尝试一下`jitsu`。例如，通过输入`jitsu apps`可以获得管理应用程序的命令列表。你也应该去看看[柔道手册](https://github.com/nodejitsu/handbook "nodejitsu/handbook")。

我们的 JSPro 读者中有人有使用 Nodejitsu 的经验吗？

## 分享这篇文章