# Node.js 中的 HTTP 身份验证

> 原文：<https://www.sitepoint.com/http-authentication-in-node-js/>

上周，在[在 Node.js 中创建 HTTP 服务器](https://www.sitepoint.com/build-a-simple-web-server-with-node-js/ "Creating a HTTP Server in Node.js")中，我介绍了 Node.js 中 HTTP 的基础知识。今天的文章将向您展示如何使用 HTTP 认证对 Node.js 站点进行密码保护。我们将从基本的访问认证开始，然后转到更安全的摘要访问认证。


## 基本访问认证

当用户访问实现身份验证的站点时，系统会提示他/她输入用户名和密码。如果用户提供了有效的凭证，他们将被带到页面的内容，否则他们将被“401 未授权”响应拒绝。最简单的 HTTP 认证类型是[基本访问认证](http://en.wikipedia.org/wiki/Basic_access_authentication "Basic access authentication - Wikipedia")。

### 密码文件

在服务器端，所有用户名和加密的密码都存储在一个密码文件中。Node.js 实用程序， [`htpasswd`](https://github.com/gevorg/htpasswd/ "gevorg/htpasswd") 可以用来管理密码文件。要安装`htpasswd`，使用下面显示的命令。`npm`代表 [Node.js 包管理器](https://npmjs.org/ "npm")，默认与 Node.js 一起安装`npm`用于安装 Node.js 模块。`-g`标志全局安装软件包，意味着它包含在系统的`PATH`变量中。

```
npm install -g htpasswd
```

一旦安装了`htpasswd`,您可以使用下面显示的命令创建新用户。这个示例使用`-c`标志创建一个名为“htpasswd”的新密码文件。在新文件中，添加了一个名为“foo”的用户。`-b`标志允许将密码“bar”指定为命令行的一部分。

```
htpasswd -bc htpasswd foo bar
```

运行命令后，打开“htpasswd”文件。用户“foo”的密码文件条目如下所示。这一行包含用户名和加密的密码。因为这是文件中的第一个也是唯一一个用户，所以这应该是文件中唯一的一行。

```
foo:{SHA}Ys23Ag/5IOWqZCw9QGaVDdHwH00=
```

### Node.js 公司

下一步是为我们的 HTTP 服务器添加认证支持。首先，您需要使用下面的`npm`命令安装`http-auth`模块。

```
npm install http-auth
```

接下来，创建一个名为“basic_auth_server.js”的新文件，并添加如下所示的代码。注意第 2 行引用了`http-auth`模块。在第 3 行到第 7 行，一个配置对象被传递给身份验证模块。`authRealm`字段定义了一个认证领域。`authFile`字段指向我们之前创建的密码文件。`__dirname`当前正在执行的脚本所在的目录。此示例假设“htpasswd”文件与“basic_auth_server.js”位于同一个目录中。`authType`配置字段指示要使用的认证类型。在第 9 行，基本身份验证方案应用于 HTTP 连接。身份验证回调函数为进一步的处理提供已验证的用户名。

```
var http = require("http");
var auth = require("http-auth");
var basic = auth({
  authRealm: "Private area",
  authFile: __dirname + "/htpasswd",
  authType: "basic"
});
var server = http.createServer(function(request, response) {
  basic.apply(request, response, function(username) {
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello " + username);
    response.end();
  });
});

server.listen(80);
console.log("Server is listening");
```

最后，启动服务器。您可以通过导航到 [`http://localhost`](http://localhost) 连接到服务器。系统将提示您输入用户名和密码。提供您之前创建的凭证，浏览器将通过名称向您致意。

### 限制

基本访问身份验证的最大缺点是凭证是以明文形式通过网络发送的。这种类型的身份验证应该仅用于安全(即 HTTPS)连接，以防止窃听。如果安全连接不可用，应该使用更安全的身份验证形式。

## 摘要接入认证

[摘要接入认证](http://en.wikipedia.org/wiki/Digest_access_authentication "Digest access authentication - Wikipedia")是基本认证的更安全的替代方案。使用摘要式身份验证，密码在网络传输之前会被加密。

### 密码文件

摘要式身份验证也使用密码文件。但是，该文件的格式与用于基本身份验证的格式略有不同。为了处理摘要密码文件格式，我们将使用一个名为 [`htdigest`](https://github.com/gevorg/htdigest/ "gevorg/htdigest") 的不同实用程序。使用下面的`npm`命令安装`htdigest`。

```
npm install -g htdigest
```

接下来，使用下面显示的命令创建一个新的密码文件。同样，`-c`标志用于创建名为“htpasswd”的新密码文件。这一次，我们还必须指定一个身份验证领域。在这种情况下，身份验证领域是“私有区域”。在本例中，用户名仍然是“foo”。请注意，命令中没有提供密码。输入命令后，系统会提示您提供密码。

```
htdigest -c htpasswd "Private area" foo
```

运行`htdigest`后，查看新的“htpasswd”文件内部。“foo”条目如下所示。摘要式身份验证文件包含用户名和加密密码，以及基本身份验证文件中不包含的身份验证领域。

```
foo:Private area:b8e1b1c08abcd38173a7dba3ad93a0c3
```

### Node.js 公司

为了将摘要认证整合到我们的服务器中，我们将再次使用`http-auth`模块。如果您一直遵循本教程，那么这个模块应该已经安装在您的机器上了。接下来，创建一个名为“digest_auth_server.js”的新文件来实现您的服务器。服务器代码如下所示。请注意，服务器代码几乎与基本身份验证服务器代码相同。区别在于配置对象的`authType`字段。在这种情况下，`authType`被设置为`"digest"`。可以以与基本身份验证服务器相同的方式访问该服务器。

```
var http = require("http");
var auth = require("http-auth");
var digest = auth({
  authRealm: "Private area",
  authFile: __dirname + "/htpasswd",
  authType: "digest"
});
var server = http.createServer(function(request, response) {
  digest.apply(request, response, function(username) {
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello " + username);
    response.end();
  });
});

server.listen(80);
console.log("Server is listening");
```

## 结论

本文介绍了 HTTP 认证的基础知识。通过遵循这里提供的示例，您的 Node.js 应用程序可以更加安全。但是，您应该意识到，仅有身份验证是不够的。如果安全是首要问题，你的网站应该在 HTTPS 提供服务。在以后的文章中，我将探索 HTTPS 和许多其他优秀的 Node.js 特性。

 **如果你喜欢这篇文章，你会想要了解 SitePoint 最新系列的印刷和电子书。第一个标题是 Don Nguyen 的 Node.js 请访问[网站了解更多信息！](https://www.sitepoint.com/jumpstart/)** 

## **分享这篇文章**