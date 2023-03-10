# 用 PHP 对抗招聘者的垃圾邮件——概念验证

> 原文：<https://www.sitepoint.com/fighting-recruiter-spam-with-php-proof-of-concept/>

自从我离开谷歌服务(由于质量问题，而不是隐私问题)，我一直在寻找完美的电子邮件服务。尝试了几个，并且和[的快速邮件](http://fastmail.com)在一起有一段时间了，我意识到[没有这种东西](https://twitter.com/search?q=fastmail%20from%3Abitfalls&src=typd&lang=en)。我对现代电子邮件提供商的最大担忧是，他们在垃圾邮件控制方面都相当糟糕。

我不是指“尼日利亚王子”类型的垃圾邮件，这种垃圾邮件大多被成功拦截(除非你使用的是 fast mail——他们甚至无法识别这些)，而是我真的真的不想收到的东西。典型的例子是，[招聘者垃圾邮件](https://www.linkedin.com/pulse/20140516082146-67624539-dear-recruiters?trk=prof-post)。

![Illustration of blocked email](img/ead3e76570a5a2d97e7c065df469acce.png)

在本教程中，我们将开始构建一个定制的电子邮件处理器，它可以读取单个电子邮件，通过一些预定义的规则运行它们，并对它们采取行动。最终结果将非常类似于许多提供者提供的现成内容，但它将为未来帖子中更高级的方面打下基础。我们应用的使用示例:

*   当检测到招聘人员类型的关键字时，使用模板回复回复电子邮件并删除它。这在某种程度上可以通过大多数电子邮件提供商提供的规则来实现，但是这些规则不是很详细，并且通常不支持变量。
*   当公司在你退订或举报垃圾邮件(如 Ello)后继续向你发送电子邮件时，引擎应记住这些邮件，并在未来自动清除它们。一些提供商(例如 FastMail)不会阻止发件人进入你的收件箱，即使在收到数百份垃圾邮件报告之后。

这样，我们可以保留我们习惯的提供商，但也可以做一些他们的团队不知道如何做的手动改进。

在本帖中，我们将关注第一个用例。

## 拔靴带

如果您已经建立了自己的环境，请随意使用——我们将像往常一样使用我们的 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 盒子，在几分钟内获得一个预制的环境。要跟进，请执行以下命令:

```
git clone https://github.com/swader/homestead_improved hi_mail
cd hi_mail
./bin/folderfix.sh
vagrant up; vagrant ssh
mkdir -p Project/public 
```

如果你以前使用过[宅地改良](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)，你的`etc/hosts`文件中应该已经有`homestead.app`了。如果没有，按照说明添加。框中包含的默认站点指向`~/Code/Project`，这对我们来说足够好了。

进入盒子后，我们将用一些演示代码在`~/Code/Project/public`中创建一个`index.php`文件:

```
<?php
phpinfo(); 
```

这个屏幕立即告诉我们需要知道的东西:php-imap 安装了吗，或者没有？

![screenshot of php-imap shown on the phpinfo screen](img/cd514111a04f5968415e653ec12e6f07.png)

果然，它预装了[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)盒子。如果你缺少 php-imap，请按照[说明](http://php.net/manual/en/imap.installation.php)安装它——我们在继续之前需要它(在 Ubuntu 上，`sudo apt-get install php7.0-imap`应该可以做到)。

作为最后的引导步骤，让我们安装我们将用来与 IMAP 收件箱交互的包: [tedivm/fetch](https://github.com/tedious/Fetch) 。

```
composer require tedivm/fetch 
```

当然，我们需要修改我们的`index.php`文件，以包含 Composer 的自动加载器:

```
<?php

require_once `../vendor/autoload.php`; 
```

## 阅读 IMAP 收件箱

我有一个 Gmail 账户和一个 Fastmail 账户。下面的例子将使用这两个，这样我们可以显示两个收件箱之间的差异，并根据需要进行调整，使我们的项目提供者不可知。

在 PHP 中，内置的`imap`函数像本地的`file`函数一样工作——你创建一个句柄，然后将它传递给其他函数。API 老了(真的真的老了！)，所以它只存在于这种程序形式中。这就是为什么，只要我们可以，我们将使用我们以前安装的获取库。

### Gmail–基本抓取

让我们从一步一步开始，登录我们的 Gmail 帐户。首先，在帐户设置下，在*转发和 POP/IMAP* 选项卡中，确保启用 IMAP 已激活。

![Enabled IMAP in Gmail](img/3b42a436cd7e541496e893c8c49072ac.png)

```
<?php

require_once '../vendor/autoload.php';

use Fetch\Server;

$server = new Server('imap.googlemail.com', 993);
$server->setAuthentication('account@gmail.com', 'password');

$messages = $server->getMessages();
/** @var $message \Fetch\Message */
foreach ($messages as $i => $message) {
    echo "Subject for {$i}: {$message->getSubject()}\n<br>";
} 
```

在获取文档后，我们尝试启动与我们的 Gmail 帐户的连接。不幸的是，如果您激活了 2FA ( *2 因素认证*)，您将看到以下错误:

![Exception requesting an application specific password for Gmail](img/89866eed0356127b6cc6f725b8bdd7d1.png)

这很容易纠正。我们可以进入我们的谷歌账户的[应用密码页面](https://security.google.com/settings/security/apppasswords)并生成一个(从菜单中选择“其他”，给它一个任意的名称，并将密码复制到代码中)。现在如果我们测试一些东西…

![Gmail email subjects displayed on screen](img/b5db71162edc86b4738445b57a33c2ee.png)

太好了——我们收到了 Gmail 邮件。现在让我们连接到 Fastmail。

### 快速邮件–基本抓取

与 Gmail 类似，Fastmail 也支持应用程序密码，但无论你是否使用 2FA，它都需要密码。在这里创建一个。

![Generating an app password for Fastmail](img/3218ee6358f26f6b902b95f49b02a6b2.png)

FastMail 的值如下:

```
$server = new Server('imap.fastmail.com', 993);
$server->setAuthentication('username@fastmail.com', 'password');

$messages = $server->getMessages();
/** @var $message \Fetch\Message */
foreach ($messages as $i => $message) {
    echo "Subject for {$i}: {$message->getSubject()}\n<br>";
} 
```

> 请注意，邮件是以非嵌套的方式获取的，因此即使您的收件箱可能显示 100 这样的数字，实际检索到的电子邮件数量可能会比这多得多，以防一些电子邮件是回复、按上下文分组等等。

Gmail 和 Fastmail 都默认收件箱文件夹，这正是我们想要的。

### 目标电子邮件

根据收件箱中电子邮件的数量，您可能已经注意到了一个巨大的性能问题——获取这些邮件花费了很长时间！显然，如果我们想要及时处理收到的电子邮件，这是行不通的。毕竟，我们的目标是处理所有收到的电子邮件，并在可能的情况下处理它们。

不幸的是，由于电子邮件规范是在互联网的石器时代开发的，所以当新邮件到达时，没有本地方法来获得推送通知。不过，还有另一种方法。

IMAP 支持搜索，这可以包括标志状态。根据[，传入的文档](http://tldrify.com/k1v)应该返回所有未读消息:

```
$messages = $server->search('UNSEEN'); 
```

![A single unseen message from the Gmail inbox](img/1e65f4c7c234c1fc89d465e16121d468.png)

果然，我们的电子邮件声明我们已经成功地为我们正在构建的这个应用程序创建了一个应用程序密码，这封邮件仍未被阅读，就放在收件箱里。成功了，通话也很快！

## 扫描电子邮件

现在我们知道了如何检索未读邮件，是时候分析它们了，如果它们触发了我们的规则检查，就对它们执行一些操作。

让我们来看第一个例子——清除招聘人员的垃圾邮件。招聘人员的电子邮件有不同的形状和大小，所以没有绝对的方法来识别它们。相反，我们应该依靠几个指针，如果给定一个数值，这些指针的总和可以超过给定的阈值。例如，如果 100 是将电子邮件标记为招聘人员垃圾邮件所需的阈值，我们可以生成下表:

| 规则 | 价值 | 香港警察训练学校（Police Training School 的缩写） |
| --- | --- | --- |
| 包含 | 寻找 IT 机会 | One hundred |
| 包含 | PHP 专家？ | Eighty |
| 包含 | 创业公司？ | Ten |
| 包含 | 在 GitHub 上看到了您的个人资料 | Fifty |
| 包含 | explore-group.com | One hundred |
| 从 | @explorerec.com | One hundred |
| 包含 | 新职位 | Twenty |
| 包含 | 急(ly)？需要 | Thirty |
| 包含 | 巨大的加号 | Fifteen |
| 包含 | 全栈开发人员 | Thirty |
| 包含 | 采访？ | Twenty |
| 包含 | 履历 | Sixty |
| 包含 | 技能 | Ten |
| 包含 | 考生？ | Twenty |

> 我要感谢我所有的 Twitter 粉丝，他们[发送了一些招聘人员的垃圾邮件示例](https://twitter.com/bitfalls/status/781096663389728768)并帮助建立了上面的表格。

`explore-group`指的是一个由[残忍持续的垃圾邮件发送者](https://twitter.com/bitfalls/status/555422800195096576)组成的团队，因此他们的电子邮件会自动触发招聘人员的垃圾邮件警报。

值是正则表达式——这允许我们进行部分匹配，这在识别发件人域或可能略有不同但本质上相同的字符串(如“PHP 专家”和“PHP 专家”)时特别有用。

为了提高性能，按照规则的分值降序来检查规则是有意义的。如果其中只有一个触发了 100，那么就没有必要检查其余的。

让我们看看这个的代码。请原谅代码的*意大利面*性质——因为这只是一个概念证明，它将在后续文章中被 OOP 化和打包。

```
<?php

require_once '../vendor/autoload.php';

use Fetch\Message;
use Fetch\Server;

$inboxes = [
    'primary@gmail.com' => [
        'username' => 'primary@gmail.com',
        'password' => 'password',
        'aliases' => ['onealias@gmail.com', 'anotheralias@gmail.com'],
        'smtp' => 'smtp.googlemail.com',
        'imap' => 'imap.googlemail.com'
    ],
    'primary@mydomain.com' => [
        'username' => 'someusername',
        'password' => 'password',
        'aliases' => ['alias@mydomain.com'],
        'smtp' => 'smtp.fastmail.com',
        'smtp_port' => '587',
        'imap' => 'imap.fastmail.com',
        'starttls' => true
    ]
];

$rules = [
    ['contains' => 'finding IT opportunities', 'points' => 100],
    ['contains' => 'PHP specialists?', 'points' => 80],
    ['contains' => 'startups?', 'points' => 10],
    ['contains' => 'saw your profile on GitHub', 'points' => 50],
    ['contains' => 'explore-group\.com', 'points' => 100],
    ['from' => '@explorerec\.com', 'points' => 100],
    ['contains' => 'new position', 'points' => 20],
    ['contains' => 'urgent(ly)? need', 'points' => 30],
    ['contains' => 'huge plus', 'points' => 15],
    ['contains' => 'full-stack developer', 'points' => 30],
    ['contains' => 'interviews?', 'points' => 20],
    ['contains' => 'CV', 'points' => 60],
    ['contains' => 'skills', 'points' => 10],
    ['contains' => 'candidates?', 'points' => 20],
];

$points = [];
foreach ($rules as $key => &$rule) {
    $points[$key] = $rule['points'];
    if (isset($rule['contains'])) {
        $rule['contains'] = '/' . $rule['contains'] . '/i';
    }
    if (isset($rule['from'])) {
        $rule['from'] = '/' . $rule['from'] . '/i';
    }
}
array_multisort($points, SORT_DESC, $rules);

$unreadMessages = [];
foreach ($inboxes as $id => $inbox) {
    $server = new Server($inbox['imap'], 993);
    $server->setAuthentication($inbox['username'], $inbox['password']);
    $unreadMessages[$id] = $server->search('UNSEEN');
}

foreach ($unreadMessages as $id => $messages) {
    echo "Now processing: ".$id. "<br>";
    /**
     * @var Message $message
     */
    foreach ($messages as $i => $message) {

        $spam = isRecruiterSpam($rules, $message) ? '' : 'not';
        echo "Subject for {$i}: {$message->getSubject()} is probably {$spam} recruiter spam.\n<br>";
    }
}

function isRecruiterSpam($rules, Message $message)
{
    $sum = 0;
    foreach ($rules as $rule) {
        if (isset($rule['contains'])) {
            if (preg_match($rule['contains'], $message->getSubject())
                || preg_match($rule['contains'], $message->getHtmlBody())
            ) {
                $sum += $rule['points'];
            }
        } else {
            if (isset($rule['from'])) {
                if (preg_match($rule['from'], $message->getOverview()->from)
                ) {
                    $sum += $rule['points'];
                }
            }
        }
        if ($sum > 99) {
            return true;
        }
    }

    return false;
} 
```

首先，我们定义收件箱和所有必要的配置值。然后，我们按照 points 键的值对 rules 数组进行排序，并通过添加分隔符将字符串转换成正则表达式。接下来，我们从我们所有的账户中提取出所有看不见的消息，然后遍历它们。

在这一点上，我们调用每个上的`isRecruiterSpam`函数，它依次获取`from`字段、主题和 HTML 主体，并对它们进行检查。在每条规则之后，我们检查`$sum`是否超过 100 分，如果是，我们返回 true——我们相当确定该消息是招聘人员的垃圾邮件。否则，我们不断总结，最后如果所有的规则都检查了，结果仍然在 100 以下，则返回 false。

![No messages have been detected as recruiter spam](img/f64b0e13564b1164294c671a98913eb3.png)

在我最初的测试中，没有邮件被标记为招聘人员垃圾邮件。让我们试着从另一个邮箱转发过去的邮件，看看会发生什么。

![Two messages have been flagged as recruiter spam!](img/1bc8989a892492ea7d81850af27efb9c.png)

成功！我们的引擎已成功识别招聘人员垃圾邮件！现在，我们来看看关于回复的问题。

### 发送回复

若要回复消息，我们需要引入另一个包。让我们把它称为 SwiftMailer，因为它是从 PHP 发送电子邮件的事实上的久经考验的标准。

```
composer require swiftmailer/swiftmailer 
```

我们不会在这里讨论 SwiftMailer 的基础知识，其他地方有相关文档[。](https://www.sitepoint.com/sending-email-with-swift-mailer/)

让我们想想现在需要做些什么:

1.  一封邮件，一旦被阅读并被识别为招聘人员垃圾邮件，需要被标记为已读，否则它会在随后的搜索中继续被选中。
2.  回复时，回复应该来自它被发送到的电子邮件地址。
3.  理想情况下，自动回复的邮件应该放在服务器上的另一个文件夹中。这对于定期检查和识别假阳性非常有用。

定义了需求之后，让我们看看代码可能是什么样子。

```
foreach ($unreadMessages as $id => $messages) {
    echo "Now processing: " . $id . "<br>";
    if (!empty($messages)) {
        $mailer = Swift_Mailer::newInstance(
            Swift_SmtpTransport::newInstance(
                $inboxes[$id]['smtp'], $inboxes[$id]['smtp_port'],
                (isset($inboxes[$id]['starttls'])) ? 'tls' : null
            )
                ->setUsername($inboxes[$id]['username'])
                ->setPassword($inboxes[$id]['password'])
                ->setStreamOptions(
                    [
                        'ssl' => [
                            'allow_self_signed' => true,
                            'verify_peer' => false,
                        ],
                    ]
                )

        );
    } else {
        continue;
    }
    /**
     * @var Message $message
     */
    foreach ($messages as $i => $message) {

        if (isRecruiterSpam($rules, $message)) {

            $message->setFlag(Message::FLAG_SEEN);

            $potentialSender = $message->getAddresses('to')[0]['address'];
            $sender = (in_array($potentialSender, $inboxes[$id]['aliases']))
                ? $potentialSender : $inboxes[$id]['aliases'][0];

            $reply = Swift_Message::newInstance('Re: ' . $message->getSubject())
                ->setFrom($message->getAddresses('to')[0]['address'])
                ->setTo($message->getAddresses('from')['address'])
                ->setBody(
                    file_get_contents('../templates/recruiter.html'),
                    'text/html'
                );

            $result = $mailer->send($reply);
        }

    }
} 
```

简而言之:

*   如果`$unreadMessages`下的任何收件箱关键字有一个非空数组(意味着它有一些招聘人员垃圾邮件)，我们就启动一个邮件程序——这是出于性能原因。如果我们有很多收件箱，我们不想构建一个邮件实例，即使是干净的收件箱。
*   我们在准备好邮件程序的情况下遍历这些检测到的垃圾邮件，然后构建一个电子邮件回复。对于收件人，我们选择发件人，对于发件人，我们选择原始电子邮件收件人列表中的第一个地址(如果它在为此收件箱定义的别名中),如果不是，则选择列表中的第一个别名。这是因为一些招聘人员太懒了，他们会群发邮件给一千个人，把自己作为收件人，并把“受害者”放在密件抄送中。
*   最后，我们获取一封准备好的模板邮件的内容，作为邮件的正文，然后发送出去。

此时，我们需要实际编写电子邮件模板:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Recruiter reply</title>
</head>
<body>
<p>Hello!</p>
<p>Please do not be alarmed by the quick reply - it's automated.</p>
<p>Based on your email's contents, you sound like a recruiter. As such, before getting back in touch with me, please read <a href="https://www.linkedin.com/pulse/20140516082146-67624539-dear-recruiters?trk=prof-post">this</a>.</p>
<p>In case we misidentified your intentions and your email, we apologize - our bot is still young and going through some growing pains. If that's the case and you have a genuine non-recruiter concern you'd like to discuss, please feel free to reply directly to this email.</p>
<p>Kind regards,<br>Bruno</p>
</body>
</html> 
```

果然，我们的回复如期而至。

![An automatic reply to recruiter spam](img/c5928ecf353f40738f0575c1500774cc.png)

### 白名单

但是如果招聘人员回复了我们的回复会怎么样呢？你可能会认为它会再次得到自动回复，因为电子邮件链包含了之前引用的电子邮件。不是这样的！是电子邮件提供商将电子邮件连接成链——实际的回复只包含我们自己的内容，所以没有理由担心有人会陷入我们自动引擎的回复循环中。

不过，为了安全起见，我们可以在模板底部添加一些额外的内容，例如:

```
...

<p>Kind regards,<br>Bruno</p>
<p style="text-align: right"><em>sent via our-little-app</em></p>
</body>
</html> 
```

我们可以像这样在规则中定位这个文本:

| 规则 | 价值 | 香港警察训练学校（Police Training School 的缩写） |
| --- | --- | --- |
| 包含 | 通过我们的小应用程序发送 | -1000 |

…但是，我们必须删除早期检测机制，一旦达到 100 分，该机制就会触发肯定的识别，并且将这些白名单规则与黑名单规则放在同一个集合中是很笨拙的。

如果我们能建立一个全新的检测机制来完全跳过黑名单扫描就更好了。这将是一个单独的循环，它还检查正文、主题和标题，但性能影响可以忽略不计，因为 [Fetch](https://github.com/tedious/Fetch) 缓存消息的属性以供重复调用(获取的消息的内容不能更改——如果更改，它是一条新消息——只有标志可以更改)。

我们将创建一个新的`isWhitelisted`函数，并在检查其他规则之前调用它。

```
 if (!isWhitelisted($whitelistRules, $message)
            && isRecruiterSpam($rules, $message)
        ) {

// ...

function isWhitelisted($rules, Message $message) {
    foreach ($rules as $rule) {
        if (isset($rule['contains'])) {
            if (preg_match($rule['contains'], $message->getSubject())
                || preg_match($rule['contains'], $message->getHtmlBody())
            ) {
                return true;
            }
        } else {
            if (isset($rule['from'])) {
                if (preg_match($rule['from'], $message->getOverview()->from)
                ) {
                    return true;
                }
            }
        }
    }
    return false;
} 
```

你会注意到它几乎和`isRecruiterSpam`一样，只是没有点。

自然，我们还需要`$whitelistRules`数组，它在这一点上相当小:

```
$whitelistRules = [
    ['contains' => '/sent via our-little-app/i'],
]; 
```

这样，我们不仅确保了包含我们自动回复的电子邮件被忽略，而且我们还可以轻松地让来自我们认识和信任的人/域的电子邮件通过。再加上许多提供商提供的联系人 API，我们现在对收件箱的控制能力确实是巨大的。

### 文件夹

概念验证的最后一个要求是将我们自动回复的邮件移动到一个单独的文件夹中。IMAP API 支持文件夹，Fetch 已经相当顺利地实现了它，所以这只是几行代码的问题。

首先，我们将在每个收件箱上创建一个名为“自动回复”的文件夹，如果它不存在的话。

```
 $server = new Server($inbox['imap'], 993);
    $server->setAuthentication($inbox['username'], $inbox['password']);

    if (!$server->hasMailBox('autoreplied')) {
        $server->createMailBox('autoreplied');
    }

    $unreadMessages[$id] = $server->search('UNSEEN'); 
```

然后，在回复发送后，我们将*移动*(复制和删除)我们回复的邮件到这个文件夹。

```
$result = $mailer->send($reply);
if ($result) {
    $message->moveToMailBox('autoreplied');
} 
```

果然，它将邮件移动到新创建的文件夹中:

![Message we autoreplied to has been marked as read and moved into a separate folder](img/4d4e93cb327267e8965f16b8ce57a930.png)

## 结论

在本教程中，我们讨论了如何读取收件箱中最近的邮件，按照一些规则运行它们，并根据规则对它们执行一些操作。我们现在有了一种以编程方式过滤收件箱的方法！

到目前为止，您可能已经注意到了这个实现的几个问题，其中一些可能是:

*   没有办法动态定义规则或白名单。您必须更改代码。一个数据库和一个带有各种 CRUD 接口的登录系统会很方便。
*   没有任何缓存，所以每次调用都很慢。
*   随着我们提出更多的规则和条件，`isRecruiterSpam`函数将变得越来越复杂，最终达到完全混乱的地步。如果我们想要一个灵活、可扩展的动态系统，这是我们需要解决的问题——尤其是如果我们想要识别更多类型的电子邮件，而不仅仅是招聘人员的垃圾邮件！
*   向这个应用程序添加更多的功能是乏味的——我们正在用这个代码打破每一个坚实的原则，需要重构。理想情况下，我们希望应用程序可以同时被多个用户使用。不仅如此，我们还希望在用户之间共享一些训练数据，以便更好地保护垃圾邮件。

我们将在后续文章中处理所有这些问题——现在我们已经知道了我们需要的所有东西以及我们的概念证明工作，我们可以清理代码并将其全部转化为值得努力的东西。

接下来，我们将通过适当的设计和构造，把我们的意大利面条脚本实验变成一个多用户应用程序。我们还将使用 cronjob 启动它，并开始构建一个合适的规则引擎。敬请期待！

## 分享这篇文章