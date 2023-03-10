# 使用客户端证书认证的服务间通信

> 原文：<https://www.sitepoint.com/inter-service-communication-using-client-certificate-authentication/>

假设我们有一项类似谷歌的服务。我们有一个邮件应用和一个社交应用。两者都能够将事件添加到日程表中。我们不希望必须转到邮件应用程序来查看从那里添加到日程的事件，然后切换到社交应用程序来查看从那里添加的事件。相反，我们有另一个日程安排应用程序，包含了我们在这两个应用程序中的所有活动。

这种面向服务的架构很棒，但是如何验证客户机(请求者)有访问服务的权限呢？在这种情况下，电子邮件应用具有在日程安排应用上创建事件的许可。

输入 SSL。

SSL 用于确保客户端和服务器之间的所有数据都是私有的。为了确保所有数据都是私有的，服务器需要 SSL 证书。

SSL 证书有一个密钥对(公钥和私钥)和一个主题，主题是证书/网站所有者的身份。通常，SSL 证书的主题将包含域名、组织名称、地址、城市、州和国家。它还将包含证书的到期日期和负责颁发证书的证书颁发机构的详细信息。(*注意*:任何用证书私钥加密的东西都只能用公钥解密，反之亦然。)

该证书还用于验证该网站是他们所说的那个网站。
最常见的情况是浏览器显示锁图标，告诉用户网站的身份已通过验证。因为 SSL 证书可以用来验证服务器就是他们所说的那个人，所以我们应该能够使用相同的方法来验证客户机。

### 典型的浏览器-网站 SSL 流程。

**认证机构:** Verisign、Godaddy、DigiCert 等
客户端:浏览器
**服务:**网站

*   证书颁发机构(CA)创建一个证书。这是他们的证书，用来证明 CA 的身份。
*   该服务创建一个 CSR(证书签名请求)。CSR 将详细说明网站和公司的身份。
*   然后，服务会将此 CSR 提交给 CA 进行验证。
*   CA 将验证 CSR 中的详细信息。
*   CA 使用他们的证书对 CSR(证书签名请求)进行数字签名。这将创建一个 SSL 证书，然后由服务颁发该证书。

当客户端连接到服务时，它使用 SSL 证书建立加密链接。客户端还尝试验证服务的身份。为此，客户端从服务中获取 SSL 证书，并检查证书是否已过期、是否正用于颁发证书的域，以及证书是否由客户端信任的证书颁发机构(Verisign、Godaddy、DigiCert 等)颁发。客户端保留一个列表，列出它信任的 CA 的所有证书，并与附带 SSL 证书的 CA 进行比较。(注意:浏览器决定信任哪个证书颁发机构，但是您也可以将自己的证书颁发机构添加到浏览器中。例如，这就是让您的浏览器信任自签名证书的方式。)

不是客户端想要验证服务的身份，而是我们想要做相反的事情。

**注意**使用最初的例子，客户端是邮件/社交应用程序，服务是日程安排应用程序。

### 设置 SSL 身份验证

我们需要做的第一件事是创建一个认证机构(我们现在是自己的认证机构)。这将用于签署客户 CSR。因为我们正在反转一切，所以客户端发布服务的 CSR *而不是*。

下面是如何生成私钥和证书请求，然后对证书进行自签名。

```
openssl genrsa -out ca.key 1024
openssl req -new -key ca.key -out ca.csr
openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt
```

然后，您需要将 ca.crt 文件复制到您的服务中。这是我们的可信 ca 列表，仅由我们组成。

如果使用 apache，您需要在 *httpd.conf* 文件中设置选项:

```
...
SSLCACertificateFile /path/to/cacert/ca.crt
SSLVerifyClient require
...
```

查看 apache 的网站了解更多关于其他选项的信息。

对于 NGINX，将以下内容添加到 *nginx.conf* 文件中:

```
http {
    server {
      ……...

     ssl_client_certificate /path/to/cacert/ca.crt;
     ssl_verify_client    on;

     ……..
   }
}
```

查看 [nginx 的网站]( http://wiki.nginx.org/HttpSslModule)获得更多选项。

每个客户端都需要自己的 SSL 证书。客户端将创建一个 CSR，并向证书颁发机构(也就是我们)发送一个请求。

为客户端生成私钥(在本例中为 client.key)和证书请求

```
openssl genrsa -out client.key 1024
openssl req -new -key client.key -out client.csr
```

然后使用我们创建的证书颁发机构证书和密钥(ca.crt，ca.key)对请求进行签名，并返回签名的证书。

```
openssl x509 -req -days 365 -CA ca.crt -CAkey ca.key -CAcreateserial -in client.csr -out client.crt
```

现在，当客户端向服务发出请求时，它应该传递 client.crt

回到我们的第一个例子。我将从邮件应用程序中请求向日程添加活动。我要用[法拉第](https://github.com/lostisland/faraday)因为它很牛逼。

```
request = Faraday::Connection.new https://schedule.com, :ssl => {
      :verify => false,
      :client_cert => OpenSSL::X509::Certificate.new(File.read(/path/to/mail_client.crt)),
      :client_key => OpenSSL::PKey::RSA.new(File.read(/path/to/mail_client.key)),
    }
    request.headers['Accept'] = 'application/json'
    request.headers['Content-Type'] = 'application/json'
    response = request.post "/events", {event: {start_time:"2013-08-01T16:20:00+00:00", end_time:"2013-08-01T18:20:00+00:00", name:"Meeting"}}.to_json
```

`:verify`在这种情况下，意味着客户正在验证服务是否是他们所说的那样(典型的浏览器-网站流程)。如果设置为`true`，还需要添加`:ca_file => 'path/to/cafile'`选项。本例中的`ca_file`是用于创建您的域(即服务)SSL 证书的 CA 文件。如果您从 Godaddy 购买了 SSL 证书，您需要下载他们用来创建 SSL 证书的 CA 证书。(他们这里有一个名单)。

那么为什么用这种方法而不是基本的 auth 或 oauth 呢？最重要的是，在添加更多客户端时，该服务不需要做任何事情。新客户只需创建一个 CSR，并从同一个证书颁发机构请求一个 SSL 证书。

最初从这篇 [集体想法文章中得到了想法。](http://collectiveidea.com/blog/archives/2012/12/17/inter-service-authentication-with-ssl/)

我们还处于起步阶段，但到目前为止，它看起来很有希望。

## 分享这篇文章