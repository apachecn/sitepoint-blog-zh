# 使用节点和 http 控制台进行 HTTP 调试

> 原文：<https://www.sitepoint.com/http-debugging-with-node-and-http-console/>

http-console 是一个节点模块，它为您提供了执行 http 命令的命令行界面。这对于调试和查看 HTTP 请求到底发生了什么非常有用，不管它们是针对 web 服务器、web 服务还是数据库服务器。

## 装置

要使用 http-console，您需要安装节点。如果你还没有安装它，要么去[http://nodejs.org](http://nodejs.org)下载你的操作系统的安装程序，或者去 Node wiki，如果你喜欢[通过包管理器](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)安装。

接下来，使用 [npm](http://npmjs.org/) 安装 http-console:

```
 $> npm install http-console2 -g 
```

有几点需要注意:

*   我们实际上是在安装 [http 控制台 2](https://github.com/mmalecki/http-console2) ，而不是 [http 控制台](https://github.com/cloudhead/http-console)。http-console2 是 http-console 的一个分支，但包含了对由于在 Node 的新版本中不赞成使用`require.paths`而导致的[错误](https://github.com/cloudhead/http-console/pull/30)的修复。它作为 http-console2 发布到 npm，但是一旦安装，您仍然可以作为 http-console 运行它。
*   我们正在安装带有`-g`全局开关的 http-console2。这意味着您可以从任何地方调用 http-console，因为它安装在您的`$PATH`中的某个位置:

    ```
     $> type http-console
    http-console is /usr/local/bin/http-console 
    ```

要开始使用 http-console，我们只需向它传递我们想要连接的任何东西的 URL 和端口，然后开始发出 http 命令。

## 说 HTTP

让我们连接到服务器并发出一些命令。我们将从简单的事情开始，向 web 服务器发出一些`GET`请求。我会假设，当你读到这封信时，你是一名网络开发人员。而且，作为一名 web 开发人员，你可能已经有了一个运行在 http://localhost 上的 web 服务器。通过键入以下命令，告诉 http-console 连接到它:

```
$> http-console http://localhost
> http-console 0.6.1
> Welcome, enter .help if you're lost.
> Connecting to localhost on port 80. 
```

现在您已经连接好了，可以开始发布命令了。在提示符下输入`GET /`:

```
http://localhost:80/> GET /
HTTP/1.1 200 OK
Server: nginx/1.0.11
Date: Wed, 04 Jan 2012 08:40:04 GMT
Content-Type: text/html
Content-Length: 151
Last-Modified: Mon, 04 Oct 2004 15:04:06 GMT
Connection: keep-alive
Accept-Ranges: bytes

<html>
<head>
<title>Welcome to nginx!</title>
</head>
<body bgcolor="white" text="black">
<center><h1>Welcome to nginx!</h1></center>
</body>
</html> 
```

我们得到完整的 HTTP 响应，包括 HTTP 头和 HTML 本身。您可以通过键入`.q`退出 http-console

让我们尝试另一个命令。最近我写了关于节点的 [express web 框架，我们创建了一个页面来显示最近提到 Sitepoint 的十条推文。我想知道如果我们使用 http-console 在 Twitter 的搜索 API 中查询类似的推文会发生什么？](/express-yourself/)

```
$> http-console http://search.twitter.com
> http-console 0.6.1
> Welcome, enter .help if you're lost.
> Connecting to search.twitter.com on port 80. 
```

现在为`/search.json?q=sitepoint&rpp=10`发出一个`GET`请求:

```
http://search.twitter.com:80/> GET /search.json?q=sitepoint&rpp=10
HTTP/1.1 200 OK
Cache-Control: max-age=15, must-revalidate, max-age=300
Expires: Fri, 17 Feb 2012 22:04:02 GMT
Content-Type: application/json;charset=utf-8
Content-Length: 7749
Vary: Accept-Encoding
Date: Fri, 17 Feb 2012 21:59:02 GMT
X-Varnish: 2065334673
Age: 0
Via: 1.1 varnish
Server: tfe

{
    page: 1,
    since_id: 0,
    max_id_str: '170628259464216576',
    refresh_url: '?since_id=170628259464216576&q=sitepoint',
    completed_in: 0.107,
    results: [
        {
            to_user_id_str: null,
            to_user_name: null,
            id: 170628259464216580,
            iso_language_code: 'en',
            ... 
```

同样，我们得到了 HTTP 头，但是这次我们得到了作为 JSON 的 HTTP 响应体(为了节省空间，省略了完整的 JSON)。

但是我们并不局限于使用 http-console 连接到 web 服务器和 web 服务。我们还可以用它来连接提供 RESTful API 的数据库服务器，比如 [CouchDB](http://couchdb.apache.org/) 。(如果你没有安装 CouchDB，最简单的方法是克隆 https://github.com/iriscouch/build-couchdb 的[并按照 README.md 中的说明进行操作)。](https://github.com/iriscouch/build-couchdb)

假设 CouchDB 正在运行(如果您通过 [build-couchdb](https://github.com/iriscouch/build-couchdb) 安装，启动 couchdb 应该和运行`. ~/path/to/build-couchdb/build/env.sh`，然后运行`couchdb`一样简单)，像这样连接 http-console:

```
$> http-console http://127.0.0.1:5984
> http-console 0.6.1
> Welcome, enter .help if you're lost.
> Connecting to 127.0.0.1 on port 5984. 
```

然后，我们可以对数据库发出命令。让我们得到所有数据库的列表:

```
http://127.0.0.1:5984/> GET /_all_dbs
HTTP/1.1 200 OK
Server: CouchDB/1.1.1 (Erlang OTP/R15B)
Date: Wed, 04 Jan 2012 08:26:18 GMT
Content-Type: text/plain;charset=utf-8
Content-Length: 25
Cache-Control: must-revalidate

[ '_replicator', '_users' ] 
```

创建一个新的数据库怎么样？

```
http://127.0.0.1:5984/> PUT /foodb
... 
HTTP/1.1 201 Created
Server: CouchDB/1.1.1 (Erlang OTP/R15B)
Location: http://127.0.0.1/foodb
Date: Wed, 04 Jan 2012 09:19:05 GMT
Content-Type: text/plain;charset=utf-8
Content-Length: 12
Cache-Control: must-revalidate

{ ok: true } 
```

重新发出`GET /_all_dbs`命令，我们将看到新数据库的列表:

```
http://127.0.0.1:5984/> GET /_all_dbs
HTTP/1.1 200 OK
Server: CouchDB/1.1.1 (Erlang OTP/R15B)
Date: Wed, 04 Jan 2012 09:19:18 GMT
Content-Type: text/plain;charset=utf-8
Content-Length: 33
Cache-Control: must-revalidate

[ '_replicator', '_users', 'foodb' ] 
```

现在让我们向`foodb`数据库添加一个文档。我们需要将 Content-Type 头设置为`application/json`，这很容易通过发出`.j`命令来完成(在 http-console 提示符下键入`.help`来查看所有可用的命令):

```
http://127.0.0.1:5984/> .j
http://127.0.0.1:5984/> POST /foodb
... { "name":"foo", "body":"bar" }
HTTP/1.1 201 Created
Server: CouchDB/1.1.1 (Erlang OTP/R15B)
Location: http://127.0.0.1/foodb/d4a833a173e9d22594b426fd300010a9
Date: Wed, 04 Jan 2012 09:36:30 GMT
Content-Type: text/plain;charset=utf-8
Content-Length: 95
Cache-Control: must-revalidate

{
    ok: true,
    id: 'd4a833a173e9d22594b426fd300010a9',
    rev: '1-de4f3804f6f3d2d3a393bec924951e5a'
} 
```

我们可以发出`HEAD`请求来获取关于文档的信息，`DELETE`请求删除文档，以及`DELETE`请求删除数据库:

```
http://127.0.0.1:5984/> HEAD /foodb/d4a833a173e9d22594b426fd300010a9
HTTP/1.1 200 OK
Server: CouchDB/1.1.1 (Erlang OTP/R15B)
Etag: "1-de4f3804f6f3d2d3a393bec924951e5a"
Date: Wed, 04 Jan 2012 09:36:51 GMT
Content-Type: text/plain;charset=utf-8
Content-Length: 113
Cache-Control: must-revalidate

http://127.0.0.1:5984/> DELETE /foodb/d4a833a173e9d22594b426fd300010a9?rev=1-de4f3804f6f3d2d3a393bec924951e5a
HTTP/1.1 200 OK
Server: CouchDB/1.1.1 (Erlang OTP/R15B)
Etag: "2-3ac7397737175948b7a3a6b7e95d2949"
Date: Wed, 04 Jan 2012 09:40:14 GMT
Content-Type: text/plain;charset=utf-8
Content-Length: 95
Cache-Control: must-revalidate

{
    ok: true,
    id: 'd4a833a173e9d22594b426fd300010a9',
    rev: '2-3ac7397737175948b7a3a6b7e95d2949'
}

http://127.0.0.1:5984/> DELETE /foodb
HTTP/1.1 200 OK
Server: CouchDB/1.1.1 (Erlang OTP/R15B)
Date: Wed, 04 Jan 2012 09:41:49 GMT
Content-Type: text/plain;charset=utf-8
Content-Length: 12
Cache-Control: must-revalidate

{ ok: true } 
```

这是使用 http-console 生成和检查 http 请求的一个快速浏览。我们向 web 服务器发出一个简单的 GET 请求，对 Twitter 的搜索 API 进行 API 调用，并向 CouchDB 服务器发出命令。我同意 YMMV，但是希望您会发现它是对您的 web 开发工具带的一个有用的补充。

## 分享这篇文章