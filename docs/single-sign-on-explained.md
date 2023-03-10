# 解释了单点登录(SSO)

> 原文：<https://www.sitepoint.com/single-sign-on-explained/>

当你为大众开发一个产品时，你很少会想到一个完全独立的、不与任何其他服务交互的产品。当您使用第三方服务时，用户身份验证是一项相对困难的任务，因为不同的应用程序有不同的用户身份验证机制。解决这个问题的一个方法是通过[单点登录](https://en.wikipedia.org/wiki/Single_sign-on)，或者 SSO。

单点登录(SSO)是一个允许用户在通过一次用户认证(即登录)后访问多个服务的过程。这包括在登录到主要服务之后，对用户已经给予许可的所有服务进行认证。除了其他好处之外，SSO 避免了通过密码或其他身份验证系统反复确认身份的单调任务。

让我们更详细地看一下 SSO，我们将使用一个非常著名的服务来演示它的用途和好处。

## 认证过程

单点登录的基本流程如下:

*   第一步是登录主服务(例如，脸书或谷歌)。
*   当您访问一个新服务时，它会将您重定向到原始(或父)服务，以检查您是否登录到该服务。
*   将返回 OTP(一次性密码)令牌。
*   然后，OTP 令牌由来自父服务器的新服务进行验证，并且只有在成功验证之后，用户才被准许进入。

尽管为 SSO 制作 API 是一项繁琐的任务，尤其是在处理安全性方面，但是实现是一项相对容易的任务！

使用 SSO 的一个很好的例子是在 Google 的服务中。您只需登录一个主要的 Google 帐户即可访问不同的服务，如 YouTube、Gmail、Google+、Google Analytics 等。

## 您自己产品的 SSO

当您构建自己的产品时，您需要确保它的所有组件都使用相同的身份验证。当您的所有服务都局限于您自己的代码库时，这很容易做到。然而，有了像 [Disqus](http://disqus.com/) 评论系统和 [Freshdesk](http://freshdesk.com/) 这样用于客户关系管理的流行服务，使用它们而不是从头开始创建自己的服务是一个好主意。

但是使用这种第三方服务会出现问题。因为他们的代码托管在各自的服务器上，所以用户需要明确地登录他们的服务，即使他们已经登录到您的网站。如前所述，解决方案是实现单点登录。

理想情况下，你会得到一对公钥和私钥。您为登录的用户生成一个令牌，并将其与您的公钥一起发送给服务进行验证。通过验证后，用户将自动登录到服务中。为了更好地理解这一点，让我们用一个真实的例子。

## Disqus SSO

Disqus 是一个受欢迎的网站评论托管服务，它提供了许多功能，如社交网络集成、审核工具、分析，甚至可以导出评论。它最初是一个 YCombinator 创业公司，现在已经发展成为世界上最受欢迎的网站之一！

由于 Disqus 评论系统嵌入到您的页面中，如果用户已经在您的网站上登录，则无需再次登录 Disqus，这一点很重要。Disqus 有一个关于如何集成 SSO 的 API 和大量文档。

首先使用您的私有和公共 Disqus API 密钥为登录用户生成一个名为`remote_auth_s3`的密钥。当您将 SSO 注册为 Disqus 中的一个免费插件时，您将获得公钥和私钥。

您将用户信息(id、用户名和电子邮件)作为 JSON 传递给 Disqus 进行身份验证。您生成了一条消息，在页面上呈现 Disqus 系统时需要使用这条消息。为了更好地理解它，让我们看一个用 Python 写的例子。

Github 上的 Disqus 提供了 PHP、Ruby 和 Python 等几种流行语言的代码示例。

## 生成消息

在 GitHub 上找到的示例 Python 代码([)让用户登录到您的网站，如下所示。](https://github.com/disqus/DISQUS-API-Recipes/blob/master/sso/python/sso.py)

```
import base64
import hashlib
import hmac
import simplejson
import time

DISQUS_SECRET_KEY = '123456'
DISQUS_PUBLIC_KEY = 'abcdef'

def get_disqus_sso(user):
    # create a JSON packet of our data attributes
    data = simplejson.dumps({
        'id': user['id'],
        'username': user['username'],
        'email': user['email'],
    })
    # encode the data to base64
    message = base64.b64encode(data)
    # generate a timestamp for signing the message
    timestamp = int(time.time())
    # generate our hmac signature
    sig = hmac.HMAC(DISQUS_SECRET_KEY, '%s %s' % (message, timestamp), hashlib.sha1).hexdigest()

# return a script tag to insert the sso message
    return """<script type="text/javascript">
    var disqus_config = function() {
        this.page.remote_auth_s3 = "%(message)s %(sig)s %(timestamp)s";
        this.page.api_key = "%(pub_key)s";
    }
    </script>""" % dict(
        message=message,
        timestamp=timestamp,
        sig=sig,
        pub_key=DISQUS_PUBLIC_KEY,
    )
```

## 初始化问题注释

然后，在一个 JavaScript 请求中将这个生成的令牌和您的公钥一起发送给 Disqus。如果验证通过，生成的评论系统会让用户登录。如果你有任何困难，可以在 [DIsqus 开发者谷歌群](https://groups.google.com/forum/#!forum/disqus-dev)寻求帮助。

我们可以在[的博客碗](http://theblogbowl.in)上看到 SSO 的实现，这是一个用 Python/Django 开发的博客目录。如果您登录了网站，则应该在呈现 Disqus 评论系统时登录。在这个例子中，`person`对象存储了`id`(对网站上的每个人都是唯一的)`email`和`pen_name`。生成的消息如下所示。

```
sso = get_disqus_sso({ 
    'id': person.id, 
    'email': person.user.email, 
    'username': person.pen_name 
})
```

在前端，您只需打印这个变量来执行脚本。对于现场演示，你可以访问博客碗上的这篇文章并查看底部的评论。当然，您不会登录。

![Screenshot of Disqus without signing in](img/1efa095a1595d1b249c46fe03f8ed573.png "Screenshot of Disqus without signing in")

接下来，[登录博客碗](http://theblogbowl.in/login/)，再次访问[同一个帖子](http://theblogbowl.in/your_voice/what-happened-when-i-reached-the-top-of-hacker-news/view/)(需要登录才能查看效果)。请注意，您已登录到下面的评论系统。

![Screenshot of Disqus after signing in](img/bbd322473f9ee140189ce2070f33f65f.png "Screenshot of Disqus after signing in")

博客碗提供的另一个有趣的功能是在发布内容时匿名(比如[这篇文章](http://theblogbowl.in/your_voice/top-secret-post/view/))。想象一下这样一种情况，你希望一个用户以匿名用户的身份在 Disqus 上发布对评论的回复(比如 Quora 上的[)。我们采取了简单的方法，在 id 后面附加了一个很大的数字。为了给用户关联一个唯一的电子邮件(这样它就不会和用户的其他评论一起出现)，我们也生成一个唯一的电子邮件。这会将您的匿名评论保存在一起，但不会与您的原始个人资料或其他用户的匿名评论相结合。](http://www.quora.com/Anonymity-on-Quora/How-can-I-anonymously-add-content-on-Quora)

![Screenshot of Disqus as anonymous user](img/6d4b939cdfad8a243c2ebda64eb0e0eb.png "Screenshot of Disqus as anonymous user")

这是代码:

```
sso = get_disqus_sso({ 
    'id': 1000000 + person.id, 
    'email': str(person.id) + '-' + str(post.id) + 'anon@theblogbowl.in', 
    'username': 'Anonymous' 
})
```

## 结论

尽管不同服务的 SSO 过程略有不同，但它们背后的基本思想是相同的——生成令牌并验证它！我希望这篇文章已经帮助你深入了解了应用程序如何集成 SSO，也许这将帮助你自己实现 SSO。

如果您有任何更正、问题或自己的经验要与 SSO 分享，请随时发表评论。

## 分享这篇文章