# 社交网络认证:Twitter 和脸书

> 原文：<https://www.sitepoint.com/social-network-authentication-twitter-facebook/>

在本系列的前几部分中，我们创建了初始界面，设置了 Google+登录功能，并讨论了如何将我们的账户合并在一起。在本文中，我们将在应用程序中集成 Twitter 和脸书。你会发现与 Google+文章有很多相似之处，所以如果你能容易地理解那篇文章，你就不会有太多的麻烦。如果你还没看过那篇文章，我建议你先看完再继续这篇文章。

你可以在这个 [Github 页面](https://github.com/sitepoint-examples/sitepoint-social-login)上查看所有文章的代码。

## Twitter 认证

同样，我们从创建以下目录开始:`src/SitePoint/SocialLogin/Twitter`。在这个目录中，我们创建了`TwitterLogin`类。这个类实现了我们在第一篇文章中创建的`SocialLoginInterface`接口。确保名为`service`的属性可用于存储我们的服务。

像 Google+一样，Twitter 也有一些特定的需求，以确保我们可以登录。因此，请确保我们在该类中有以下 3 个属性:

*   客户端 id
*   键
*   回拨 URL

因为我们的应用程序需要这 3 个属性才能工作，所以我们的构造函数将接收这 3 个变量作为参数并设置属性。

到目前为止，你的代码看起来应该和 Google+文章中的第一个例子一样。

我们的下一步是创建我们的 Twitter 服务。我们将使用同一个 OAuth 库，并将在 init 方法中建立一个连接。

在开始之前，我们必须在类的顶部添加一些 use 语句。

```
use OAuth\ServiceFactory;
use OAuth\OAuth1\Service\Twitter;
use OAuth\Common\Storage\Session;
use OAuth\Common\Consumer\Credentials;
```

我们的 init 方法的所有东西现在都存在了。是时候建立我们与 Twitter 的联系了。我们用我们正在使用的 OAuth 库的基本例子来做到这一点。

```
/**
     * Initializes our service
     */
    public function init()
    {
        $storage = new Session();
        $serviceFactory = new ServiceFactory();
        $credentials = new Credentials($this->clientId, $this->key, $this->callbackUrl);
        $this->service = $serviceFactory->createService('twitter', $credentials, $storage);

        return $this;
    }
```

我们的服务现在已经设置好了，所以我们现在可以通过从接口填充其他方法来继续。我们将从`getLoginUrl`方法开始。此方法将返回一个 URL，您将把您的用户重定向到该 URL。

```
/**
 * Returns the login url for the social network
 *
 * @return string
 */
public function getLoginUrl()
{
        $token = $this->service->requestRequestToken();
        return $this->service->getAuthorizationUri(array('oauth_token' => $token->getRequestToken()));
}
```

您可能会注意到，这种方法(以及我们将讨论的下一种方法)与 Google+文章略有不同。这是由于 Google+使用 OAuth 2，而 Twitter 使用 OAuth 1。Twitter 是最早使用 OAuth 的公司之一，因为 OAuth 的最初创建者当时正在 Twitter 工作。Twitter 仍在使用 OAuth 1，尽管他们正在向 OAuth 2 迈进。

下一个要定义的方法是`loginCallback`方法。当用户接受 Twitter 与您的应用程序共享数据时，用户将被重定向回您定义为`callbackUrl`的 URL。当用户请求那个 URL 时，你应该从这个类中调用`loginCallback`方法。

如果一切顺利，我们现在可以从 Twitter 检索数据。

```
/**
 * Handles the login callback from the social network
 *
 * @return SocialUserInterface
 */
    public function loginCallback()
    {
        $storage = new Session();
        $token = $storage->retrieveAccessToken('Twitter');

        $this->service->requestAccessToken(
            $_GET['oauth_token'],
            $_GET['oauth_verifier'],
            $token->getRequestTokenSecret()
        );
        $userData = json_decode($this->service->request('account/verify_credentials.json'), true);
        $twitterUser = new TwitterUser($userData);
        return $twitterUser;
    }
```

这一次，我们在这里使用一个名为`TwitterUser`的类，我们用接口`SocialUserInterface`返回一个类。但是，这个类还没有出现。是时候创造它了！

## Twitter 用户

Twitter 返回数据的方式与 Google+不同，所以我们必须再次规范化。我们需要创建一个名为`TwitterUser`的类，它将实现我们在第一篇文章中创建的`SocialUserInterface`。

在这种情况下，该类的构造函数需要 Twitter 的原始数据。在每个 getter 中，我们将从原始数据中检索我们想要的数据。最后，您的`TwitterUser`类可能看起来像这样。

```
<?php

namespace SitePoint\SocialLogin\Twitter;

use SitePoint\SocialLogin\Interfaces\SocialUserInterface;

class TwitterUser implements SocialUserInterface {

    /**
     * @var mixed user data
     */
    private $userData;

    /**
     * Constructor.
     *
     * @param $userData mixed Raw social network data for this particular user
     */
    public function __construct($userData)
    {
        $this->userData = $userData;
    }

    /**
     * Get the provider name
     *
     * @return string
     */
    public function getProvider()
    {
        return "twitter";
    }

    /**
     * Get the UID of the user
     *
     * @return string
     */
    public function getUid()
    {
        if(array_key_exists('id', $this->userData)) {
            return $this->userData['id'];
        }
        return null;
    }

    /**
     * Get the first name of the user
     *
     * @return string
     */
    public function getFirstname()
    {
        if(array_key_exists('name', $this->userData)) {
            list($firstname, $lastname) = explode(" ", $this->userData['name']); // TODO: Of course you have to make this smarter
            return $firstname;
        }
        return null;
    }

    /**
     * Get the last name of the user
     *
     * @return string
     */
    public function getLastname()
    {
        if(array_key_exists('name', $this->userData)) {
            list($firstname, $lastname) = explode(" ", $this->userData['name']); // TODO: Of course you have to make this smarter
            return $lastname;
        }
        return null;
    }

    /**
     * Get the username
     *
     * @return string
     */
    public function getUsername()
    {
        if(array_key_exists('screen_name', $this->userData)) {
            return $this->userData['screen_name'];
        }

        return null;
    }

    /**
     * Get the emailaddress
     *
     * @return string
     */
    public function getEmailAddress()
    {
        return null;
    }

    /**
     * Get the city
     *
     * @return string
     */
    public function getCity()
    {
        if(array_key_exists('location', $this->userData)) {
            return $this->userData['location'];
        }
        return null;
    }

    /**
     * Get the birthdate
     *
     * @return string
     */
    public function getBirthDate()
    {
        return null;
    }

    /**
     * Get the gender
     *
     * @return string
     */
    public function getGender()
    {
        return null;
    }
}
```

你可能会注意到，我们拥有的信息比 Google+少得多。Twitter 只是没有分享那么多信息。例如，我们遗漏了电子邮件地址，而这是您希望在应用程序中包含的最常见的字段之一。

你唯一能做的就是，在登录 Twitter 后，让用户填写他的电子邮件地址，并保存到你的数据库中。除此之外，您可以尝试将一个 Twitter 帐户与一个已经给定的帐户合并，就像本系列的前一部分一样。

## 脸书登录

根据你从 Google+和 Twitter 的解释中收集的知识，你应该很容易建立脸书版本。这里有 login 类的完整实现。

```
<?php

namespace SitePoint\SocialLogin\Facebook;

use SitePoint\SocialLogin\Interfaces\SocialLoginInterface;
use OAuth\ServiceFactory;
use OAuth\OAuth2\Service\Facebook;
use OAuth\Common\Storage\Session;
use OAuth\Common\Consumer\Credentials;

class FacebookLogin implements SocialLoginInterface {

    /**
     * Facebook service
     *
     * @var string
     */
    protected $service;

    /**
     * OAuth client ID
     *
     * @var string
     */
    protected $clientId;

    /**
     * OAuth key
     *
     * @var string
     */
    protected $key;

    /**
     * Callback url
     *
     * @var string
     */
    protected $callbackUrl;

    /**
     * Constructor.
     *
     * @param $clientId string
     * @param $key string
     * @param $callbackUrl string
     */
    public function __construct($clientId, $key, $callbackUrl)
    {
        $this->clientId = $clientId;
        $this->key = $key;
        $this->callbackUrl = $callbackUrl;
    }

    /**
     * Initializes our service
     */
    public function init()
    {
        $storage = new Session();
        $serviceFactory = new ServiceFactory();
        $credentials = new Credentials($this->clientId, $this->key, $this->callbackUrl);
        $this->service = $serviceFactory->createService(
            'facebook',
            $credentials,
            $storage,
            array(
                Facebook::SCOPE_EMAIL,
                Facebook::SCOPE_USER_BIRTHDAY,
                Facebook::SCOPE_USER_LOCATION
            )
        );

        return $this;
    }

    /**
     * Returns the login url for the social network
     *
     * @return string
     */
    public function getLoginUrl()
    {
        return $this->service->getAuthorizationUri();
    }

    /**
     * Handles the login callback from the social network
     *
     * @param string $accessCode
     *
     * @return SocialUserInterface
     */
    public function loginCallback($accessCode)
    {
        $token = $this->service->requestAccessToken($accessCode);

        // Send a request with it
        $userData = json_decode($this->service->request('/me'), true);
        $facebookUser = new FacebookUser($userData);
        return $facebookUser;
    }
}
```

您可能会注意到，我在创建脸书服务时定义了一些范围。通过定义这些范围，我可以从用户那里获得一些额外的细节。在这种情况下，我也在收集电子邮件地址、生日和位置。请注意，您收集的数据越多，用户拒绝登录您的应用程序的可能性就越大。

## 脸书用户

用户类甚至更简单，没有任何惊喜。完成后，它应该是这样的。

```
<?php

namespace SitePoint\SocialLogin\Facebook;

use SitePoint\SocialLogin\Interfaces\SocialUserInterface;

class FacebookUser implements SocialUserInterface {

    /**
     * @var mixed user data
     */
    private $userData;

    /**
     * Constructor.
     *
     * @param $userData mixed Raw social network data for this particular user
     */
    public function __construct($userData)
    {
        $this->userData = $userData;
    }

    /**
     * Get the provider name
     *
     * @return string
     */
    public function getProvider()
    {
        return "facebook";
    }

    /**
     * Get the UID of the user
     *
     * @return string
     */
    public function getUid()
    {
        if(array_key_exists('id', $this->userData)) {
            return $this->userData['id'];
        }
        return null;
    }

    /**
     * Get the first name of the user
     *
     * @return string
     */
    public function getFirstname()
    {
        if(array_key_exists('first_name', $this->userData)) {
            return $this->userData['first_name'];
        }
        return null;
    }

    /**
     * Get the last name of the user
     *
     * @return string
     */
    public function getLastname()
    {
        if(array_key_exists('last_name', $this->userData)) {
            return $this->userData['last_name'];
        }
        return null;
    }

    /**
     * Get the username
     *
     * @return string
     */
    public function getUsername()
    {
        if(array_key_exists('name', $this->userData)) {
            return str_replace(" ", "_", $this->userData['name']);
        }
        return null;
    }

    /**
     * Get the emailaddress
     *
     * @return string
     */
    public function getEmailAddress()
    {
        if(array_key_exists('email', $this->userData)) {
            return $this->userData['email'];
        }
        return null;
    }

    /**
     * Get the city
     *
     * @return string
     */
    public function getCity()
    {
        if(array_key_exists('location', $this->userData)) {
            return $this->userData['location'];
        }
        return null;
    }

    /**
     * Get the birthdate
     *
     * @return string
     */
    public function getBirthDate()
    {
        if(array_key_exists('birthday', $this->userData)) {
            return $this->userData['birthday'];
        }
        return null;
    }

    /**
     * Get the gender
     *
     * @return string
     */
    public function getGender()
    {
        if(array_key_exists('location', $this->userData)) {
            return $this->userData['location']['name'];
        }
        return null;
    }
}
```

### 快速试验

如果您想运行一个快速测试来测试代码，将 [Github 库](https://github.com/sitepoint-examples/sitepoint-social-login)克隆到您的本地计算机上，并切换到名为`part4`的分支。在这个分支中，你会看到一个`testTwitter.php`和`testFacebook.php`文件。您可以填写两个服务的 API 细节，并在浏览器中运行该文件。当请求该页面时，您将被重定向到社交媒体页面，请求您分享信息。

单击接受时，您将被重定向回您配置为回拨 URL 的页面，显示您的名字和姓氏。

## 结论

在这篇文章中，我们在前几篇文章中设置的基础上做了进一步的工作。除了 Google+，我们现在还可以用 Twitter 和脸书登录。在所有情况下，我们都返回一个规范化的用户对象，我们可以将它添加到我们自己的用户系统中。通过这篇文章，我们来到了这个系列的结尾。它是否给了你一些如何处理社交网络或框架不可知包的见解？欢迎在下面的评论中提出任何问题！

## 分享这篇文章