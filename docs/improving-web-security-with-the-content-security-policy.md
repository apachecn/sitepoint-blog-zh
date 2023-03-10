# 使用内容安全策略提高 Web 安全性

> 原文：<https://www.sitepoint.com/improving-web-security-with-the-content-security-policy/>

内容安全策略(CSP)是一种安全机制，有助于防止内容注入攻击，如跨站脚本(XSS)。这是一个声明性的策略，允许您给浏览器一个白名单，列出它可以从哪里加载资源，浏览器是否可以使用内联样式或脚本，以及它是否可以使用动态 JavaScript 评估——比如通过使用`eval`。如果有人试图从不在这个白名单上的某个地方加载资源，那么该资源的加载将被阻止。

## 它是如何工作的

CSP 目前是由 W3C 应用安全工作组发布的候选推荐标准。它通过`Content-Security-Policy` HTTP 头传递给浏览器，HTTP 头包含一个或多个指令，这些指令将允许浏览器加载资源的域列入白名单。CSP 1.0 有以下指令:

*   `default-src`
*   `script-src`
*   `object-src`
*   `style-src`
*   `img-src`
*   `media-src`
*   `frame-src`
*   `font-src`
*   `connect-src`

顾名思义，`default-src`为剩余的指令设置默认的源列表。如果一个指令没有明确地包含在 CSP 头中，它将回退到使用`default-src`列表中的值。

所有指令都遵循相同的模式:

*   `self`用来指代当前域
*   可以在空格分隔的列表中指定一个或多个 URL
*   `none`表示对于给定的指令不应加载任何内容，例如`object-src 'none'`表示不应加载任何插件，如 Flash 或 Java。

最简单的方法是，我们可以定义一个 CSP，只从当前域加载资源，如下所示:

```
Content-Security-Policy:    default-src 'self';
```

如果试图从任何其他域加载资源，浏览器会阻止该尝试，并且会在控制台中记录一条消息:

![Browser console in Chrome](img/d8e3616c953fa49b0cb69f672e7cec95.png)

默认情况下，CSP 也通过禁止内联脚本和动态代码评估来限制 JavaScript 的执行。这与可以加载资源的白名单相结合，大大有助于防止内容注入攻击。例如，尝试插入内联脚本标记的 XSS 攻击将被阻止:

![Inline script blocked in Chrome](img/fe0be83669bf38b8aacaaeca7172e2fe.png)

加载不包含在 CSP 中的外部脚本的任何尝试也是如此:

![External script blocked in Chrome](img/d11641af10d384d7d95322b16b60133c.png)

URL 中目前不支持路径，所以你不能锁定你的站点只提供来自 http://cdn.example.com/css.的 CSS，你只能通过指定域名来凑合——例如，http://cdn.example.com。然而，您可以使用通配符来指定给定域的所有子域，比如*.mycdn.com。

后续指令不会继承先前指令的规则。CSP 头中包含的每个指令必须明确列出它允许的域/子域。这里的`default-src`和`style-src`都包含`self`，`script-src`和`style-src`都包含 http://cdn.example.com:

```
Content-Security-Policy:    default-src 'self'; 
                            style-src 'self' http://cdn.example.com; 
                            script-src http://cdn.example.com;
```

如果您需要使用`data`URL 来加载资源，您将需要在您的指令中包含`data:`——例如，`img-src 'data:';`。

除了列出域名，`script-src`和`style-src`支持的另外两个特性是`unsafe-inline`和`unsafe-eval`:

*   `unsafe-inline`可以被`style-src`和`script-src`使用，表示允许内嵌<样式>和<脚本>标签。CSP 使用选择加入策略。也就是说，如果不包含`unsafe-inline`，那么所有内联<样式>和<脚本>标签都会被屏蔽。`unsafe-inline`还允许 CSS 的内联`style`属性，并允许内联事件处理程序(onclick，onmouseover 等)。)和`javascript:`网址(比如`<a href="javascript:foobar()">`)。
*   `unsafe-eval`可以被`script-src`使用。同样，它使用选择加入策略，所以如果您的`script-src`没有显式包含`unsafe-eval`，任何动态代码评估——包括使用`eval`，函数构造器，以及将字符串传递给`setTimeout`和`setInterval`——都会被阻塞。

### 浏览器支持

[浏览器对 CSP 1.0](http://caniuse.com/#feat=contentsecuritypolicy) 的支持非常好，Internet Explorer 是房间里常见的大象:IE10 和 IE11 通过`X-Content-Security-Policy`头部分支持 CSP，但即使如此，它们似乎也只支持可选的 [`sandbox`指令](https://www.w3.org/TR/CSP2/#directive-sandbox)。

## 使用`report-uri`捕获 CSP 违规

我前面提到过，任何违反 CSP 的行为都会被记录到浏览器控制台中。虽然这在你的站点处于开发阶段时可能没什么问题，但是当你将 CSP 部署到生产环境中时，这就不太实际了。

相反，您可以使用`report-uri`来记录所有 CSP 违规。该指令将 URL 作为其值，并在检测到 CSP 违规时向该 URL 发出 HTTP POST 请求。请求体包含一个 JSON 对象，其中填充了违规的详细信息。

为了说明这一点，假设我们有一个 CSP 如下:

```
Content-Security-Policy:    default-src 'self'; 
                            report-uri: https://example.com/csp/report;
```

这意味着浏览器只允许从我们自己的域中加载资源。然而，我们的网站使用谷歌分析，所以它试图从
www.google-analytics.com 加载 JavaScript。这违反了我们的 CSP，因此下面的 JSON 通过 HTTP POST 请求提交给我们的`report-uri`:

```
{
    "csp-report": {
        "blocked-uri:" "https://ajax.googleapis.com"
        "document-uri:" "http://example.com/index.html"
        "original-policy": "default-src 'self'; report-uri http://example.com/csp/report"
        "referrer:" ""
        "violated-directive": "default-src 'self'"
    }
}
```

## 内容-安全-策略-仅报告

如果您正在考虑实现 CSP，您可以通过使用`Content-Security-Policy-Report-Only` HTTP 头而不是`Content-Security-Policy`来测试您的 CSP。这与 CSP 报头的工作方式相同，但是它只报告了违反*的情况，而没有*通过阻塞受限资源来实际执行策略。您甚至可以同时使用两个头，强制执行一个策略，同时监视任何更改对另一个策略的影响。

## 设置 CSP HTTP 标头

我前面提到过，CSP 被发送一个 HTTP 报头。可以直接在服务器上的服务器配置文件中设置 HTTP 头:

```
# Apache config
Header set Content-Security-Policy "default-src 'self';"

# IIS Web.config
<system.webServer>
    <httpProtocol>
        <customHeaders>
            <add name="Content-Security-Policy" value="default-src 'self';" />
        </customHeaders>
    </httpProtocol>
</system.webServer>

# nginx conf file
add_header Content-Security-Policy "default-src 'self';";
```

或者，许多编程语言/框架支持以编程方式添加 HTTP 头——比如 PHP 的[头](http://php.net/manual/en/function.header.php),或者 Node 的[set header](https://nodejs.org/api/http.html#http_response_setheader_name_value)——因此您可以使用它们来设置您的 CSP 头:

```
# PHP example
header("Content-Security-Policy: default-src 'self'");

# Node.js example
request.setHeader("Content-Security-Policy", "default-src 'self'");
```

## 野外 CSP

让我们看看脸书和 Twitter 是如何实现 CSP 的。首先，这里是脸书(为了可读性，添加了换行符):

```
default-src *;
script-src https://*.facebook.com http://*.facebook.com https://*.fbcdn.net http://*.fbcdn.net *.facebook.net *.google-analytics.com *.virtualearth.net *.google.com 127.0.0.1:* *.spotilocal.com:* 'unsafe-inline' 'unsafe-eval' https://*.akamaihd.net http://*.akamaihd.net *.atlassolutions.com;
style-src * 'unsafe-inline';
connect-src https://*.facebook.com http://*.facebook.com https://*.fbcdn.net http://*.fbcdn.net *.facebook.net *.spotilocal.com:* https://*.akamaihd.net wss://*.facebook.com:* ws://*.facebook.com:* http://*.akamaihd.net https://fb.scanandcleanlocal.com:* *.atlassolutions.com http://attachment.fbsbx.com https://attachment.fbsbx.com;
```

注意脸书是如何在两个子域名中使用通配符的，以及`connect-src`中的端口号。

以下是 Twitter 使用的 CSP:

```
default-src https:;
connect-src https:;
font-src https: data:;
frame-src https: twitter:;
frame-ancestors https:;
img-src https: data:;
media-src https:;
object-src https:;
script-src 'unsafe-inline' 'unsafe-eval' https:;
style-src 'unsafe-inline' https:;
report-uri https://twitter.com/i/csp_report?a=NVQWGYLXFVZXO2LGOQ%3D%3D%3D%3D%3D%3D&ro=false;
```

注意这些指令都包含了`https:`，从而加强了 SSL。

## CSP 第 2 级的变化

[CSP Level 2](https://www.w3.org/TR/CSP2/) 目前是候选推荐标准，定义了一些新的指令和安全措施:

*   `base-uri`控制是否允许文档操作页面的基本 URI。
*   `child-src`取代`frame-src`。
*   `form-action`控制文档提交 HTML 表单的能力。
*   类似于 X-Frame-Options 标题，通过控制如何将这个文档嵌入到其他文档中，实际上是为了替换这个标题。
*   `plugin-types`控制页面可以加载哪些特定的插件——比如 Flash、Java、Silverlight 等。

提交给`report-uri`的 JSON 得到几个额外的字段:`effective-directive`包含被违反的指令的名称；和`status-code`，它包含所请求资源的 HTTP 状态代码，如果资源不是通过 HTTP 请求的，则为零。

CSP Level 2 还包括通过 nonces 和 hashes 保护内联脚本和样式表的能力。

### 使用随机数保护内联样式和脚本

nonce 只是在服务器上生成的随机字符串，包含在 CSP 头中，也包含在内联脚本标记中。因此，我们的带有 nonce 的 CSP 报头看起来像这样:

```
Content-Security-Policy:    default-src 'self';
                            script-src 'self' 'nonce-Xiojd98a8jd3s9kFiDi29Uijwdu';
```

当呈现页面时，相同的 nonce 需要包含在 script 标签的`nonce`属性中，以便内联脚本执行:

```
<script>
    console.log("Script won't run as it doesn't contain a nonce attribute");
</script>

<script nonce="Eskdikejidojdk978Ad8jf">
    console.log("Script won't run as it has an invalid nonce");
</script>

<script nonce="Xiojd98a8jd3s9kFiDi29Uijwdu">
    console.log('Script runs as the nonce matches the nonce in the HTTP header');
</script>
```

### 使用哈希保护内联样式和脚本

要使用这种方法，首先在服务器上计算样式或脚本块的散列，并分别将它包含在`style-src`或`script-src`的 CSP 头中。然后，浏览器在呈现页面之前计算样式/脚本块的哈希。如果浏览器计算的哈希与服务器上计算的哈希匹配，则允许执行样式/脚本块。

在这里，CSP 头包含了指令`script-src`中字符串`console.log('Hello, SitePoint');`的 sha256-base64 编码哈希:

```
Content-Security-Policy: 
                default-src 'self';
                script-src 'self' 'sha256-V8ghUBat8RY1nqMBeNQlXGceJ4GMuwYA55n3cYBxxvs=';
```

当浏览器呈现页面时，它会计算每个内联脚本块的哈希，并将它们与 CSP 头中发送的白名单哈希进行比较。如果找到匹配，脚本就会执行。注意空白是重要的。这个脚本没问题:

```
<script>console.log('Hello, SitePoint');</script>
```

这些内联脚本的散列与标头中的白名单值不匹配，因此它们都不会执行:

```
<script> console.log('Hello, SitePoint');</script>
<script>console.log('Hello, SitePoint'); </script>
<script>console.log('Hello, World');</script>
```

## 结论

在本文中，我们已经了解了 CSP 1.0，以及如何使用它的指令将站点可以加载资源的地方列入白名单。我们了解了如何使用`report-uri`收集违反 CSP 的请求信息，以及脸书和 Twitter 现在如何使用 CSP。最后，我们看了 CSP 2.0 中的一些变化，特别是，如何使用 nonces 和 hashes 为您的内联样式和脚本提供更多保护。

如果您对 CSP 及其工作方式有任何疑问，请在下面发表评论。你试过 CSP 了吗？如果有，进展如何？

## 分享这篇文章