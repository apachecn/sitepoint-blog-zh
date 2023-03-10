# 快速提示:用 Node.js 配置 NGINX 和 SSL

> 原文：<https://www.sitepoint.com/configuring-nginx-ssl-node-js/>

**[NGINX](https://www.nginx.com/) 是一个高性能的 HTTP 服务器，也是一个[反向代理](https://medium.com/intrinsic/why-should-i-use-a-reverse-proxy-if-node-js-is-production-ready-5a079408b2ca)。与传统服务器不同，NGINX 遵循事件驱动的异步架构。因此，内存占用少，性能高。如果你运行的是基于 Node.js 的 web 应用，你应该认真考虑使用 NGINX 作为反向代理。**

NGINX 在服务静态资产方面非常有效。对于所有其他请求，它将与 Node.js 后端对话，并将响应发送给客户机。在本教程中，我们将讨论如何配置 NGINX 来使用 Node.js。我们还将了解如何在 NGINX 服务器中设置 SSL。

*注意:Node 还有一个内置的 HTTPS 模块，可以配置为读取必要的证书文件，而不需要反向代理。您可以在我们的文章[如何将 SSL/TLS 与 Node.js 一起使用](https://www.sitepoint.com/how-to-use-ssltls-with-node-js/)中找到更多信息。*

## 安装 NGINX

假设你的机器上已经安装了 Node.js(如果没有，在这里勾选[，让我们看看如何安装 NGINX。](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)

### 在 Linux 上安装

如果运行的是 Ubuntu，可以使用以下命令安装 NGINX:

```
sudo apt-get update
sudo apt-get install nginx 
```

如果你运行的是 Linux 发行版而不是 Ubuntu，请查看 NGINX 安装文档了解更多信息。

NGINX 安装后会自动启动。

### 在 macOS 上安装

如果你在 macOS 上，你可以使用 [Homebrew](http://brew.sh/) 轻松安装 NGINX。步骤如下:

*   家酿需要目录`/usr/local`被`chown`到你的用户名。因此，首先在终端中运行以下命令:

    ```
    sudo chown -R 'username here' /usr/local 
    ```

*   现在，以下两个命令将在您的系统上安装 NGINX:

    ```
    brew link pcre
    brew install nginx 
    ```

*   安装完成后，您可以键入以下命令来启动 NGINX:

    ```
    sudo nginx 
    ```

*   NGINX 配置文件可以在这里找到:`/usr/local/etc/nginx/nginx.conf`。

### 在 Windows 上安装

对于 Windows 操作系统，请前往 NGINX [下载](http://nginx.org/en/download.html)页面并获取压缩文件。下一步是在命令提示符下解压缩归档文件并移动到目录，如下所示:

```
unzip nginx-1.3.13.zip
cd nginx-1.3.13
start nginx 
```

如你所见，命令`start nginx`将启动 NGINX。

现在安装已经完成，让我们看看如何配置一个简单的服务器。

## 设置 Node.js 服务器

首先，让我们创建一个简单的 Node.js 服务器。我们将从启动一个项目并安装 [Express 包](https://www.npmjs.com/package/express)开始:

```
mkdir node-demo && cd node-demo
npm init -y
npm i express 
```

创建一个名为`server.js`的文件，内容如下:

```
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(port, () => console.log(`Example app listening on port ${port}!`)) 
```

您可以通过运行`node server.js`来启动服务器。

## 配置 NGINX

现在让我们打开 NGINX 默认站点配置文件:

```
sudo nano /etc/nginx/sites-available/default 
```

如果您愿意，可以直接进入目录，用您喜欢的文本编辑器打开配置文件。

当你向下滚动时，你会发现一个`server`块。它看起来像这样:

```
server {
  listen       80;
  server_name  localhost;

  ....
  more config goes here
} 
```

接下来，我们将配置`server`块来满足我们的需求。我们希望将 NGINX 配置为将所有请求传递给 Node.js 服务器。用新块替换上述`server`块，如下所示:

```
server {
  listen       80;
  server_name  localhost;

  location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
} 
```

如您所见，web 服务器将监听`http://localhost`端口`80`。`location /`块告诉 NGINX 如何处理任何传入的请求。我们使用`proxy_pass`指向 Node.js 应用程序，在我们的例子中，它运行在`http://localhost:3000`上。

此时，您应该保存该文件，并根据您的操作系统键入以下内容来重新启动 NGINX。

**Ubuntu:**

```
sudo service nginx restart 
```

或者:

```
sudo /etc/init.d/nginx restart 
```

**苹果电脑:**

```
sudo nginx -s stop && sudo nginx 
```

**视窗:**

```
nginx -s reload 
```

一旦完成，您就可以访问 [http://localhost](http://localhost) 来查看我们的代理运行情况。虽然您正在访问 NGINX web 服务器，但是您将从 Node.js 服务器获得实际的响应。

注意:你需要确保 80 端口上没有其他东西在运行(比如 Apache)。

## 设置 SSL

为了创建与用户浏览器的安全连接，我们需要获取数字证书。通常情况下，你可以从一个认证机构比如[那里得到一个，让我们加密](https://letsencrypt.org/)。如果您走的是让我们加密的路线，请确保使用 [Certbot](https://certbot.eff.org/instructions) 安装证书，它会为您重新配置 NGINX。

对于本地开发(以及跟随本教程)，您还可以[创建一个自签名证书](https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-nginx-for-ubuntu-12-04)。唯一的问题是，当有人访问你的网站时，浏览器会显示“证书不可信”的警告。但是对于在本地机器上的测试来说，这完全没问题。

一旦有了证书和私钥，就可以在 NGINX 中设置 SSL 了。您需要将我们之前的服务器块修改为以下内容:

```
server {
  listen       80;
  listen       443 ssl;
  server_name  localhost;

  ssl_certificate  /etc/nginx/ssl/server.crt;
  ssl_certificate_key /etc/nginx/ssl/server.key;

  location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
} 
```

就是这样！

现在，如果你访问 [https://localhost](https://localhost) ，你的连接将是安全的。上面的配置假设证书和私钥分别位于`/etc/nginx/ssl/server.crt`和`/etc/nginx/ssl/server.key`，但是如果您愿意，您可以更改这些位置。

## 好处:服务于静态资产

将 NGINX 设置在 Node.js 服务器前面的另一个好处是，我们可以轻松地配置它来提供我们的应用程序需要的任何静态资产。这将节省节点处理这些请求的开销。

为此，我们需要向服务器配置添加一个新的`location`块:

```
server {
  listen       80;
  server_name  localhost;

  location / {
    ...
  }

  location /public {
    root /usr/local/var/www;
  }
} 
```

在这个`location`块中，我们将根目录设置为`/usr/local/var/www`，但是如果您愿意，您可以选择不同的目录。因此，每当有对类似`http://localhost/public/somepath/file.html`的请求时，NGINX 将直接从`/usr/local/var/www/public/somepath/file.html`提供文件。

## 结论

在这个简短的教程中，我们看到了如何使用 NGINX 作为 Node.js 应用程序的反向代理，并配置 SSL。通过使用 NGINX 来处理静态资源，还可以减轻节点应用程序的负担。

## 分享这篇文章