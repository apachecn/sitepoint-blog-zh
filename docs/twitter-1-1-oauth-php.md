# 如何将 OAuth 认证添加到您的 Twitter 应用程序中

> 原文：<https://www.sitepoint.com/twitter-1-1-oauth-php/>

感谢推特。你不仅[取消了对公共 Twitter 时间轴](/rip-twitter-api-1/)的开放访问，你还期望开发者与[神秘的认证文档](https://dev.twitter.com/docs/auth/application-only-auth)竞争！

我们很多人只是想在自己的网站上展示自己的推文，但很明显 Twitter 更喜欢我们使用他们的小工具。尽管有[错综复杂的 Twitter 指令](https://dev.twitter.com/docs)，但是如果您使用有才华的(非 Twitter)开发人员提供的库，在您精心制作的 API 1.0 应用程序中实现 OAuth 还是相当简单的。

在下面的例子中，我们将把 Twitter OAuth 认证应用到一个 PHP 应用程序，该应用程序以前从一个 REST URL 解析时间线数据，比如:

[https://api.twitter.com/1/statuses/user_timeline.json?screen_name=craigbuckler](https://api.twitter.com/1/statuses/user_timeline.json?screen_name=craigbuckler)

还有 C 的 [Twitter 库](https://dev.twitter.com/docs/twitter-libraries)，Clojure，。NET、Go、Java、Node.js、Lua、Objective-C、Perl、Python 和 Ruby——指令都差不多。

## 步骤 1:创建您的 Twitter 应用程序

前往[dev.twitter.com/apps/](https://dev.twitter.com/apps/)，使用你的 Twitter ID 和密码登录。这可以是任何帐户；你的应用程序将能够读取任何其他用户的时间线，而无需他们的知识或许可*(我意识到鉴于 Twitter 的新政策，这没有多大意义，但我没有制定规则！)*

点击**创建新应用**按钮，输入应用的名称和描述。网站应该是一个你可以下载代码的页面，但是，既然你还在写代码，输入你的主页 URL，以后再改。将回拨 URL 留空。

完成验证码并点击**创建**。

## 步骤 2:创建访问令牌

在应用程序页面的详细信息选项卡底部，单击**创建我的访问令牌**按钮。现在您将看到各种字符串:

1.  OAuth: **消费者密钥**
2.  OAuth: **消费者秘密**
3.  令牌:**访问令牌**
4.  令牌:**访问令牌秘密**

让这一页开着，你很快就会用到它们。

## 步骤 3:下载 OAuth 库

从 github.com/abraham/twitteroauth[下载 Abraham Williams 的 PHP Twitter OAuth 库。](https://github.com/abraham/twitteroauth)

这个项目包含了许多文件，但是你真正需要的是[Twitter auth 文件夹](https://github.com/abraham/twitteroauth/tree/master/twitteroauth)中的**OAuth.php**和**twitteroauth.php**。将两者复制到应用程序中合适的文件夹。

## 第 4 步:修改你的时间轴抓取代码

您的 PHP 现在应该使用这样的代码来获取用户的时间线:

```
require('twitteroauth.php'); // path to twitteroauth library

$consumerkey = '11111111';
$consumersecret = '22222222';
$accesstoken = '33333333';
$accesstokensecret = '44444444';

$twitter = new TwitterOAuth($consumerkey, $consumersecret, $accesstoken, $accesstokensecret);

$tweets = $twitter->get('https://api.twitter.com/1.1/statuses/user_timeline.json?screen_name=craigbuckler&count=10');
print_r($tweets); 
```

变量`$consumerkey`、`$consumersecret`、`$accesstoken`和`$accesstokensecret`必须设置为上面的[步骤 2 中生成的 Twitter 代码。](#step2)

然后必须向`$twitter->get`方法传递一个适当的 REST URL。这个例子显示了我的，所以你应该为`screen_name`参数*输入你自己的 Twitter ID(除非你特别想显示我抱怨 Twitter 的 tweets？)*

运行代码，如果幸运的话，应该会出现一串推文…就像在 Twitter 警方坚持限制访问公共信息之前一样。你现在需要根据[显示要求](https://dev.twitter.com/terms/display-requirements)来格式化它们。嘘嘘。

注意 TwitterOAuth 的 get()方法运行 PHP 的`json_decode`命令并返回一个对象。如果您更喜欢关联数组，请将 twitteroauth.php 的第 144 行改为:

```
return json_decode($response, true);
```

希望这能提供几个月的宽限，直到 Twitter 迫使我们迁移到 API 2.0 并经历更多的考验。祝你好运。

## 分享这篇文章