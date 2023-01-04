# 使用 Pushover 将 Drupal 站点的事件推送到您的手机上

> 原文：<https://www.sitepoint.com/push-drupal-sites-events-phone-pushover/>

在这篇文章中，我将向你展示如何将 [Pushover](https://pushover.net/) 与你的 Drupal 站点集成。我将举例说明如何使用 Pushover 在网站上发生事情时立即通知自己。

我在本文中写的代码也可以在这个库中找到，所以如果你想继续的话，你可以复制它。

![](img/7e5ca0e0ee8c9632dab0fb870396c8c1.png)

## 什么是推倒？

Pushover 是一个网络和移动应用程序，允许您在移动设备上获得实时通知。它的工作方式是，你在你的 Android 或 Apple 设备上安装一个应用程序，使用一个方便的 API，你可以发送应用程序通知。最棒的是，当 Pushover 使用 Google 和 Apple 服务器发送通知时，它或多或少会实时发生(取决于您的互联网连接)。

价格也很实惠。每个平台(Android、Apple 或 desktop)只需支付一次 4.99 美元，您可以在该平台下的任意数量的设备上使用它。在你创建账户的时候，你还可以获得 5 天的免费试用期。

## 我在这里做什么？

在本文中，我将建立一个 Pushover 应用程序，并从我的 Drupal 站点使用它来通知我的手机各种事件。我将给出两个使用 Pushover 的例子:

*   每当一个匿名用户发布了一条等待管理部门批准的评论，我就会向我的手机发送一条通知
*   每当管理员用户 1 登录网站时，我会向我的手机发送一个*紧急*通知(如果你是该管理员帐户的唯一用户，这很有用)。

当然，这些都是例子，你可能觉得没用。但它们只是用来说明使用 Pushover 可以获得的能力。

## 推售账户

首先，快速看一下创建你的 Pushover 帐户。如果您还没有注册，请继续前往 [Pushover](https://pushover.net/login) 并注册，您可以开始为期 5 天的免费试用。然后，进入 [Google Play](https://play.google.com/store/apps/details?id=net.superblock.pushover) 或[应用商店](https://itunes.apple.com/us/app/pushover-notifications/id506088175?ls=1&mt=8)，在你的设备上安装应用。您需要登录您的帐户，并为设备命名。我的简单叫 *Nexus* (提示:我没有 iPhone)。

然后回到 Pushover 网站，你已经可以通过向你自己的一个或所有活动设备发送测试通知来测试它了。你甚至可以指定它应该发出什么声音。

接下来，如果你想使用 API，你需要[创建一个应用程序](https://pushover.net/apps/build)。这将为您生成一个`app_token`供以后使用。应该差不多就是这样了。

## Drupal

现在，Pushover 帐户的创建已经完成，设备应用程序已经安装(在我的情况下是 Android ),我有了我的 Pushover 应用程序，是时候看看我如何实现开始时设定的目标了。作为一个简短的路线图，我将做以下工作:

*   创建自定义 Drupal 模块
*   添加由 Chris Schalenborgh 创建的 [Pushover 类](https://github.com/kryap/php-pushover)(一个方便的包装器，用于包装对 API 的 curl 调用)
*   实现一些挂钩，这些挂钩将根据特定条件触发通知
*   利润

我将在这个例子中使用的定制模块叫做`pushover`，它包含一个`pushover.info`和一个`pushover.module`文件(根据需要)。另外，我将在其中创建一个`lib/Pushover`文件夹来存储我将用来连接 Pushover 的外部类。还有其他——更推荐的方式——将外部库导入 Drupal 模块(参见[库 API](https://www.drupal.org/project/libraries) ),但是为了简洁起见，这样做也可以。

我想在我的`pushover.module`文件中做的第一件事就是导入这个外部类。基于我的文件夹结构，我可以这样做:

```
require_once(DRUPAL_ROOT . '/' . drupal_get_path('module', 'pushover') . '/lib/Pushover/Pushover.php');
```

接下来，我想创建一个可重用的助手函数，它将返回一个可推送的对象。这是一个已经用我自己的缺省值(比如凭证)填充的对象，它接受一些更常见属性的参数。但是首先，我想把我的 Pushover 帐户凭证放到`settings.php`文件中，因为它们不属于我的 git 存储库:

```
$conf['pushover_credentials'] = array(
  'user_token' => 'uCpygdjfsndfi7233sdasdo33Yv',
  'app_token' => 'aKH8Nwsdasdanl342jmsdaBWgoVe',
);
```

显然，这两个令牌现在都无效，但是如果您继续学习，您应该用自己的令牌来替换它们:当您登录到 Pushover 网站时，您在主页面上获得的`user_token`令牌和为您的应用程序生成的`app_token`令牌。

然后我可以继续我前面提到的助手函数:

```
/**
 * Helper function that returns a pushable object using the Pushover class
 * 
 * @param $vars
 * @return bool|Pushover
 */
function pushover_get_pushable($vars) {
  global $conf;
  if (isset($conf['pushover_credentials'])) {
    $push = new Pushover();
    $push->setToken($conf['pushover_credentials']['app_token']);
    $push->setUser($conf['pushover_credentials']['user_token']);
    $push->setTitle($vars['title']);
    $push->setMessage($vars['message']);
    if (isset($vars['url'])) {
      $push->setUrl($vars['url']);
    }
    if (isset($vars['device'])) {
      $push->setDevice($vars['device']);
    }
    $push->setTimestamp(time());

    return $push;
  }
  else {
    return FALSE;
  }
}
```

在这个函数中，如果我的`settings.php`文件中有凭证，我将实例化一个新的 Pushover 对象。否则，我会通过返回 false 而无声地失败。该函数接受一些在对象上设置的参数:标题和消息是必需的，而 url 和设备不是必需的。设备实际上是您希望将此通知限制到的设备的名称。

此外，我设置了当前时间戳，然后返回对象。

接下来，是时候在一些钩子内部使用这个函数了。第一个将是 [hook_comment_insert()](https://api.drupal.org/api/drupal/modules%21comment%21comment.api.php/function/hook_comment_insert/7) :

```
/**
 * Implements hook_comment_insert().
 */
function pushover_comment_insert($comment) {

  // Send a push notification if a new comment is created by an anonymous user
  // and it is not yet published.
  if ($comment->status == 0 && $comment->is_anonymous == TRUE) {
    global $base_url;
    $vars = array(
      'title' => 'New comment on ' . variable_get('site_name') . '!',
      'message' => 'Subject: ' . $comment->subject,
      'url' => $base_url . '/node/' . $comment->nid . '#comment-' . $comment->cid,
      'device' => 'Nexus'
    );
    $pushable = pushover_get_pushable($vars);
    if ($pushable) {
      $pushed = $pushable->send();
      if ($pushed == false) {
        watchdog('Pushover', t('A comment has been created but there was an error pushing that over.'), array(), WATCHDOG_ERROR, NULL);
      }
    }
  }
}
```

在这里，我检查评论者是否匿名，状态是否为 0(安全起见)。如果是这种情况，我用一些关于站点和注释的信息为我的助手函数构建参数数组，并使用`send()`方法发送通知。你会注意到我把它限制在了`Nexus`设备上。

最后，我检查通知是否成功发出(如果 Pushover 服务没有返回成功状态代码`1`，则`send()`方法返回 false)。如果出了问题，我会迅速将它记录到看门狗。

所以现在如果一个匿名用户写了一个评论，我会收到一个推送通知，包括网站名称、评论主题和 URL。漂亮。

![](img/390cbf8a4d8e7ebd79840d19b537efda.png)

现在让我们来看第二个例子，在这个例子中，如果我的 admin 用户登录到站点，我会实现一个紧急通知。如果不是我，我就知道出事了，很可能被黑了。我在一个 [hook_user_login()](https://api.drupal.org/api/drupal/modules%21user%21user.api.php/function/hook_user_login/7) 实现中这样做:

```
/**
 * Implements hook_user_login().
 */
function pushover_user_login(&$edit, $account) {
  // If the admin user logs in, send a push notification.
  if ($account->uid == 1) {
    $whitelist = array('1.1.1.1');
    if (!in_array(ip_address(), $whitelist)) {
      global $base_url;
      $vars = array(
        'title' => 'Admin user sign in',
        'message' => 'Admin user has logged into this site: ' . variable_get('site_name') . '!',
        'url' => $base_url,
      );
      $pushable = pushover_get_pushable($vars);
      if ($pushable) {
        $pushable->setPriority(2);
        $pushable->setRetry(30);
        $pushable->setExpire(60);
        $pushed = $pushable->send();
        if ($pushed == false) {
          watchdog('Pushover', t('An admin user has logged into the site but there was an error pushing this over.'), array(), WATCHDOG_ERROR, NULL);
        }
      }
    }
  }
}
```

在这里，我首先检查登录的用户是否是 id 为 1 的用户(主管理员用户)。然后，我创建一个包含白名单 IP 的阵列，以检查登录的用户。我不想在家里或办公室登录时收到通知(`1.1.1.1`只是一个示例 ip 地址)。

然后，就像之前一样，我用通常的变量得到我的可推送对象(这次没有设备，我希望它发送到我所有的设备)。除此之外，我设置优先级为 2(将其标记为紧急通知)，重试值为 30 秒，过期值为 60 秒。后 2 项与优先级相结合，使得如果我的电话没有确认，则每 30 秒重新发送一次通知，总共持续 60 秒。要了解更多关于 Pushover 的可能选项的信息，请务必查看他们的 [API 文档](https://pushover.net/api)。

这就是了。如果有人用我的管理员帐号登录，我会收到紧急通知。当然，如果很多人都可以用那个帐号登录，那就不太好了，但是你明白了。

## 结论

在本教程中，我向您展示了如何使用 Pushover 从 Drupal 站点向您的手机发送特定事件发生时的通知。我介绍了两个例子，但我相信您可以找到更多例子。我还想提一下，我发现了一个名为 [Pushover](https://www.drupal.org/project/pushover) 的 contrib Drupal 模块，它使用规则来发送 Pushover 通知。我没有真正使用过它，但是如果你想使用 Pushover，并且你的站点已经在使用 Rules 模块的话，一定要检查一下。或者，你知道，你讨厌写代码。

## 分享这篇文章