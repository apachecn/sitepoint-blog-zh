# 社交网络认证:设置

> 原文：<https://www.sitepoint.com/social-network-authentication-setup/>

几乎每个包含登录选项的网站都包含通过不同社交网络登录的方式。在这一系列文章中，我们将看看如何确保我们的访问者可以通过 Google+登录，并最终通过 Twitter 和脸书等其他网络登录。在最后一篇文章中，我们将仔细研究如何确保用户不会因为使用不同的社交网络而拥有不同的账户。

我们将创建一个框架不可知的包，可以轻松处理来自不同社交网络的用户。在这一部分，我们将看看我们的基本设置。

你可以在这个 [Github 页面](https://github.com/peternijssen/sitepoint-social-login)上查看所有文章的代码。

## 创建您的包

我们从创建我们的包开始。为此，我们只是开始一个没有任何内容的新项目。我们首先需要定义我们的供应商名称。一般来说，人们使用他们的(用户)姓名或公司名称。我将使用`SitePoint`作为我的供应商名称。

接下来，我们还需要一个包名。该名称通常描述您的软件包。由于我们使用社交网络登录，我们将使用`SocialLogin`作为我们的包名。

是时候创建我们的目录结构了。一般来说，建议将您的类存储在`src`目录中。所以让我们从创建目录`src/SitePoint/SocialLogin`开始。

我们将创建一个`composer.json`文件，以便轻松维护我们的依赖关系。

```
{
    "name": "sitepoint/social-login",
    "type": "library",
    "description": "Social login functionality",
    "homepage": "https://www.peternijssen.nl",
    "license": "MIT",
    "authors": [
        {
            "name":     "Peter Nijssen",
            "homepage": "https://www.peternijssen.nl"
        }
    ],
    "require": {
        "php":              ">=5.3.0",
        "lusitanian/oauth": "0.3.*"
    },
    "autoload": {
        "psr-0": { "SitePoint\\SocialLogin": "src" }
    },
    "target-dir": "SitePoint/SocialLogin"
}
```

为了确保我们没有犯任何错误，对`composer.json`文件运行`composer validate`。

## 创建界面

在开始使用社交网络之前，我们将创建一些界面。为此，我们创建了目录`src\SitePoint\SocialLogin\Interfaces`。在这个目录中，我们将创建两个接口。

我们从社交网络界面开始。这个接口将由每个处理社交网络登录的类来实现。

```
<?php

namespace SitePoint\SocialLogin\Interfaces;

interface SocialLoginInterface {

    /**
     * Initializes our service
     */
    public function init();

    /**
     * Returns the login url for the social network
     *
     * @return string
     */
    public function getLoginUrl();

    /**
     * Handles the login callback from the social network
     *
     * @param string $accessToken
     *
     * @return SocialUserInterface
     */
    public function loginCallback($accessToken);
}
```

`init`方法将初始化我们的社交网络登录配置。当用户登录社交网络时，`getLoginUrl`将返回用户重定向到的 URL。每当你从一个社交网络收到一个回答时，就需要调用`LoginCallback`方法。这个方法将返回一个用户。

下一个界面是社交用户界面。从每一个社交网络，我们都会收到大量关于用户的数据。通过创建一个标准化这些数据的类，我们可以很容易地读取和处理这些数据。

```
namespace SitePoint\SocialLogin\Interfaces;

interface SocialUserInterface {

    /**
     * Get the provider name
     *
     * @return string
     */
    public function getProvider();

    /**
     * Get the UID of the user
     *
     * @return string
     */
    public function getUid();

    /**
     * Get the first name of the user
     *
     * @return string
     */
    public function getFirstname();

    /**
     * Get the last name of the user
     *
     * @return string
     */
    public function getLastname();

    /**
     * Get the username
     *
     * @return string
     */
    public function getUsername();

    /**
     * Get the emailaddress
     *
     * @return string
     */
    public function getEmailAddress();

    /**
     * Get the city
     *
     * @return string
     */
    public function getCity();

    /**
     * Get the birthdate
     *
     * @return string
     */
    public function getBirthDate();

    /**
     * Get the gender
     *
     * @return string
     */
    public function getGender();
}
```

## 必需的包

所有与社交网络的通信都将通过所谓的 OAuth 协议。我们将使用一个已经存在的库，而不是自己开发那个部分。

在命令行上运行`composer require lusitanian/oauth:0.3.*`来安装这个库。

### 结论

我们的软件包设置现在已经完成。在下面的文章中，我将向您展示如何通过 Google+登录。在最后一篇文章中，我们将深入探讨如何将账户合并为一个账户。

## 分享这篇文章