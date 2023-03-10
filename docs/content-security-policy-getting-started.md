# 如何开始使用您的网站内容安全策略

> 原文：<https://www.sitepoint.com/content-security-policy-getting-started/>

![security padlock](img/d2c8a19b984af9f26eb7daac95e3d6f5.png)

本文是与 SiteGround 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

网络是基于“同源”政策的。只有在**mysite.com**的代码可以访问 mysite.com 在 cookies、本地存储、Ajax 请求等中的数据。它与其他域隔离，因此来自 evilsite.com**的任何访问尝试都将被拒绝。**

不幸的是，事情从来没有这么简单。现代网站很复杂，加载了各种第三方组件、样式和脚本。从另一个域加载的脚本在当前页面的上下文中运行，可以为所欲为。这个社交网络按钮可以监控访问者，劫持登录 cookies，改变页面内容等等。即使你信任第三方网站，你也可能成为中间人攻击的受害者，在这种攻击中，脚本在到达你手中之前就被修改了。或者，它可以允许用户发起他们自己的[跨站脚本攻击(XXS)](https://wikipedia.org/wiki/Cross-site_scripting) 。

默认情况下，浏览器实现了一种*无所不能的*方法。幸运的是，可以使用内容安全策略(CSP)来应用限制，防止意外的安全问题。CSP 告诉浏览器什么是允许的，例如，在 mysite.com 运行 JavaScript，但只能从文件运行，不能内嵌`<script>`标签。

## 测试你的网站

要检查 CSP 是否在您的网站上实施，请访问[observatory.mozilla.org](https://observatory.mozilla.org/)，输入页面 URL 并点击**扫描我**。那些没有 CSP 保护的人很可能会得到一个 **F** (尽管会进行各种其他检查)。

CSP 应该被认为是银行、在线商店、社交网络和任何实现用户帐户的网站的必备工具。如果你的网站不使用第三方脚本、字体、媒体、小工具或分析工具，那就没那么必要了，但你能肯定它永远不会使用吗？

## 实施内容安全策略

内容安全策略必须由您的开发人员或 web 主机添加到每个页面。它使用由服务器端语言(PHP、Node.js、Ruby 等)设置的**内容安全策略** HTTP 头来定义。)或者在诸如 Apache 的`.htaccess`文件之类的服务器配置中，例如

```
# Apply a CSP to all HTML and PHP files
<FilesMatch "\.(html|php)$">
Header set Content-Security-Policy "policy-definition"
</FilesMatch>
```

(我们将很快讨论“策略定义”值。)

服务器配置文件很实用，因为它们对子文件夹层次结构中的所有页面应用相同的标题。但是，您也可以使用`meta`标签在任何页面的 HTML `<head>`中定义策略:

```
<meta http-equiv="Content-Security-Policy" content="policy-definition">
```

如果您没有权限配置服务器或需要在每个页面上使用不同的策略，这可能是必要的。

## 内容安全策略定义

现在是复杂的部分。CSP 为不同类型的内容定义了允许的域和上下文的白名单。

假设您只想允许从您的域加载脚本。您可以使用下面的 CSP *(请不要真的这么做——这只是一个例子！)*:

```
script-src 'self';
```

然后你意识到你也在从一个 CDN 加载一个第三方库，它可能出现在`mycdn.com`的各个子域上。以空格分隔的列表中添加了一个域通配符:

```
script-src 'self' *.mycdn.com;
```

然后，您会记得您的一些脚本在页面上内联运行—我们也可以这样定义:

```
script-src 'self' *.mycdn.com 'unsafe-inline';
```

我们现在有了一个脚本策略。然而，我们还没有定义其他类型，所以所有的样式表，图像，字体等。将无法加载。为了解决这个问题，我们可以使用`default-src`应用一个默认策略，作为任何未定义类型的后备:

```
default-src 'self'; script-src 'self' *.mycdn.com 'unsafe-inline';
```

请注意，每个内容类型定义都用分号(；).我们现在可以在我们的`.htaccess`文件中使用这个策略:

```
Header set Content-Security-Policy "default-src 'self'; script-src 'self' *.mycdn.com 'unsafe-inline';"
```

或者页面元标签:

```
<meta http-equiv="Content-Security-Policy" 
content="default-src 'self'; script-src 'self' *.mycdn.com 'unsafe-inline';">
```

## CSP 指令参考

全套 CSP 指令:

| 指示的 | 描述 |
| --- | --- |
| `default-src` | 默认回退策略。通常设置为`'self'`或`'none'`以确保必须声明所有其他指令 |
| `style-src` | 有效的样式表源 |
| `script-src` | 有效的 JavaScript 源 |
| `connect-src` | JavaScript 数据检索的有效 Ajax、WebSocket 或 EventSource 源 |
| `form-action` | `form`动作属性的有效来源 |
| `img-src` | 有效的图像源 |
| `font-src` | 有效的字体源 |
| `media-src` | 有效的 HTML5 `audio`和`video`元素源 |
| `object-src` | HTML `object`、`embed`和`applet`元素的有效插件源 |
| `plugin-types` | 由`object`和`embed`调用的插件的有效 MIME 类型，例如`application/pdf` |
| `frame-src` | 有效的`frame`和`iframe`源(现已废弃——使用`child-src`代替) |
| `child-src` | 有效的`frame`和`iframe`源 |
| `frame-ancestors` | `frame`、`iframe`、`object`、`embed`和`applet`元素的有效嵌入源 |
| `sandbox` | 以类似于 [HTML5 `iframe`沙盒属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe#attr-sandbox)的方式为资源启用沙盒。该指令有许多独特的限制:`allow-forms`、`allow-same-origin`、`allow-scripts`、`allow-popups`、`allow-modals`、`allow-orientation-lock`、`allow-pointer-lock`、`allow-presentation`、`allow-popups-to-escape-sandbox`和`allow-top-navigation` |
| `report-uri` | 浏览器可以发布策略失败报告的地址 |

## CSP 源参考

以`-src`结尾的 CSP 源指令支持以下值。可以使用任意数量的空格分隔值:

| 来源 | 描述 |
| --- | --- |
| `'none'` | 防止从任何来源加载，例如`frame-ancestors 'none'`停止显示任何 iframe 或插件的页面。该值不能被其他源跟随 |
| `'self'` | 允许从相同来源(协议、域/IP 和端口)加载 |
| `https:` | 仅允许 HTTPS 连接上的源 |
| `data:` | 允许数据:源，例如`style-src data:`允许在样式表中使用 base64 编码的图像 |
| `*` | 任何 URL 的通配符 |
| `*.domain.com` | 允许来自 domain.com 任何子域的源，即 www.domain.com、cdn.domain.com 等。 |
| `exact.domain.com` | 允许来源于 exact.domain.com |
| `https://exact.domain.com/` | 允许给定域上的 HTTPS 源 |
| `'unsafe-inline'` | 允许在 HTML 中内嵌 CSS、脚本、`javascript:` URIs 和元素事件处理程序，如`onclick` |
| `'unsafe-eval'` | 允许不安全的动态代码使用 JavaScript 的`eval()`函数 |
| `'nonce-*id*'` | 如果`id`匹配 nonce 属性值，允许内联 CSS 或脚本运行，例如`script-src 'nonce-abc123'`在`<script nonce="abc123">...</script>`块内运行内联代码 |
| `'sha256-*hash*'` | 如果文件内容与生成的 SHA-256 哈希值匹配，则允许样式或脚本 |

## CSP 发展建议

从一个严格的默认策略`default-src 'none';`开始，然后根据需要添加更多的权限，这是可行的。对于大多数网站来说，一个好的起点可能是:

```
default-src 'none'; style-src 'self' data:; img-src 'self' data:; script-src 'self'; connect-src 'self';
```

这允许样式、图像、脚本和 Ajax 请求来自同一个来源。

在 web 浏览器中打开您的页面，然后启动开发人员工具控制台。将报告被阻止的资源警告，例如

```
Refused to load the script 'XXX' because it violates the following Content Security Policy directive: "YYY".
```

你可能需要浏览不同的页面，以确保你已经考虑了你的网站需要的所有字体、图像、视频、脚本、插件和 iframes。

## 谷歌服务

谷歌提供了大量的服务，你可能会使用分析、字体、地图等等。不幸的是，这些都是在一系列需要进一步 Ajax 调用、内联执行和数据模式的 URIs 上启用的。您最终可能会得到一个复杂的策略，例如:

```
default-src 'self'; 
style-src 'self' 'unsafe-inline' *.googleapis.com; 
script-src 'self' *.google-analytics.com *.googleapis.com data:; 
connect-src 'self' *.google-analytics.com *.googleapis.com *.gstatic.com data:; 
font-src 'self' *.gstatic.com data:; 
img-src * data:;
```

*(为清晰起见，添加了换行符，但不得在实际代码中使用。)*

在撰写本文时，这是无法避免的，其他第三方供应商也将面临类似的挑战。

## 再次测试

最后，在[observatory.mozilla.org](https://observatory.mozilla.org/)再次重新测试你的页面，幸运的话，你的内容安全策略等级有了显著提高。该工具还将建议旧的浏览器，HTTPS，CORS，MIME，cookies，referrer 和重定向策略头。

实施内容安全策略是防止意外安全问题的重要一步。另一个重要的步骤是选择一个把安全放在心上的主机提供商。我们的合作伙伴 SiteGround 是任何寻找为高级网站安全而构建的虚拟主机平台的人的绝佳选择。

## 分享这篇文章