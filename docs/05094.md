# 资产访问限制方法–阻止不受欢迎的访问者

> 原文：<https://www.sitepoint.com/asset-access-restriction-methods-block-unwanted-visitors/>

当构建一个出色的 web 应用程序或网站时，我们有时希望人们能够将我们的 web 应用程序/网站的一部分嵌入到他们自己的应用程序/网站中。这可能是一个 iframe 持有一个“喜欢”按钮，一个简单的图像，他们想重复使用，甚至我们的整个应用程序嵌入在 iframe 中。

但是我们如何控制谁可以访问，谁可以使用我们的带宽和查询我们的服务呢？

我们将问题定义为*控制对*资产*的访问*

我们所说的资产是指:可以从我们的网站上查询到的任何东西。

## 访问限制:允许一些，阻止所有

当谈到访问控制时，我们进入了安全领域。说到安全性，白名单应该是解决这个问题的方法。控制谁被允许访问您的*资产*比控制谁不被允许访问更容易。要知道互联网上所有的怪物是不可能的。

为了保护我们的资产，我们雇佣了一个看门人，只让我们信任的人进来。一旦被聘用，我们就让他进入我们控制的白名单，让他做所有的重活。问题解决了。但是守门人应该怎么抬？

## 提升策略

根据您希望网守的安全程度和客户的要求，可以使用不同的策略。

常用的方法是检查 Referer 头。这种方法有三大缺点:

1.  当人们使用链接访问您的网站时，也会设置推荐人
2.  推荐人由客户端发送到您的服务器，并且可能会被更改
3.  引用者可能根本没有设置

然而，对于静态资产，如图像、js 和 css，这些缺点不是问题。只有当用户直接访问我们的网站(或从受信任的网站)时，才应加载您的资产。总的想法是阻止其他人热链接他们。因此，推荐人将始终在您的白名单中。除非你不相信自己，否则你会有更大的问题。

## 兄弟，你会举吗？

根据所使用的设置，查询通过一系列的*门*。简单的设置是:客户端- > HTTP 服务器- >应用代码

那么你的看门人坐在哪里？客户实际上不适合访问控制，因为他是一个不可靠的说谎者。另一方面，HTTP 服务器和应用程序代码是有用的选项。两者都给了我们强有力的工具来检查`HTTP_HOST`。

## HTTP 服务器知道如何提升

让 HTTP 服务器处理访问控制的优势在于速度。没有必要为每个请求启动应用程序代码。这可以极大地提高性能，因为我们不需要将整个应用堆栈/线程(例如 mod_php)加载到内存中。

根据您的 HTTP 服务器，可以使用不同的解决方案。

### 街头流氓

在 Apache 中，有两种不同的方法。我们可以使用 mod_rewrite 或者 Allow/Deny。

mod_rewrite 方法:

```
# Turn mod_rewrite on
RewriteEngine On

# if it is not trusted.domain.tld block it
RewriteCond %{HTTP_REFERER} !^trusted\.domain\.tld$ [NC]
RewriteCond %{HTTP_REFERER} !^trusted\.tld$ [NC]
RewriteRule ^ - [F]
```

> 大多数主机提供商都支持 mod_rewrite。

允许/拒绝方法:

```
#specify the files to guard, block all the assets
<files "*.*">
  #block everyone
  Deny from all
  #allow trusted ones
  Allow from trusted.tld trusted.domain.tld
</files>
```

> 并非所有主机都支持这些设置。

### Nginx

nginx 中的 HttpRefererModule 给了我们真正酷的`valid_referers`:所以我们需要做的就是，当一个不可信的域试图访问我们的资产时，返回 http 代码 444:

> …非标准代码 444 关闭连接，不发送任何标头…

```
location / {
  valid_referers trusted.tld trusted.domain.tld;
  if ($invalid_referer) {
    return   444;
  }
}
```

## HTTP 服务器不会思考

这里的大问题是可伸缩性:如果我们有 1000 个域需要能够访问我们的资产怎么办？如果域名列表经常改变怎么办？

对于每一个小小的编辑，我们都需要深入我们的配置文件——手动更改越多，出错的可能性就越大。

## 应用程序代码知道该做什么

在应用程序代码级别进行访问控制意味着更大的灵活性。人们可以很快让他的看门人开始工作:

```
<?php
//the whitelist we control
$whitelist = array(
	'trusted.tld',
	'trusted.domain.tld'
);

//the referer
$referer = parse_url($_SERVER["HTTP_REFERER"], PHP_URL_HOST);

//the gatekeeper
if ( !in_array($referer, $whitelist) )
{
	throw new GateKeeperDoesNotApprove;
}
```

## 那些 iframes 怎么办？

如上所述，依赖推荐人并不总是一个好主意。它不仅是来自我们不可靠的人的数据，它也没有给我们任何线索来判断我们是否在 iframe 中。根本没有办法知道。

不过，我们可以雇一个杀手来帮助我们的看门人。我们的杀手将被派遣到看起来可疑的人那里(例如，有不可信推荐人的人)。杀手将使用 JS 作为他的武器:

```
document.getElementById('container').innerHTML = '';
alert('You just got killed');
```

可悲的是，来自一个不受信任的域的人和使用来自那个不受信任的域的 iframe 访问我们的人有相同的 referer。然而，资产会将 referer 设置为我们的域(即使在 iframe 的情况下)——所以在这里派一个杀手是多余的。简单地拒绝访问就足够了——或者你可以发送一个随机的[小猫图片](http://www.ex-parrot.com/pete/upside-down-ternet.html)。

这就是为什么我们在 iframe 中会有杀手检查。如果是这样，我们让他杀了我们的目标:

```
if (top.location.href != self.location.href) {
    //kill the target
}
```

我们需要知道的唯一事情是让我们的看门人将杀手添加到发送给客户端的有效载荷中。轻松点。

```
//template.tpl
If ( Gatekeeper::doesNotApprove() )
{
    Gatekeeper::sendHitman();
}

//gatekeeper
class Gatekeeper
{

    private static $whitelist = array(); 

    public static function doesNotApprove()
	{
	    return !in_array(
		    parse_url($_SERVER["HTTP_REFERER"], PHP_URL_HOST),
			self::$whitelist
		);
	}

	public static function sendHitman()
	{
	    print '<script>if (top.location.href != self.location.href) { document.getElementById('container').innerHTML = '';alert('You just got killed');}</script>';
	}
}
```

> 此代码未经生产验证。这是一个例子。

## 真正的安全呢？

这里提供的解决方案将保护你免受大多数布吉怪物。但这两种解决方案都不可靠。第一个使用来自客户端的数据，第二个是由客户端运行的 javascript。

安全的方法是使用基于令牌的网关守护设备。OAuth 可能是您想要的人，但这超出了本文的范围。

## 分享这篇文章