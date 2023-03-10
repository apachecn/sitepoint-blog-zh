# 用 PHP 和 Arduino 自制 Twitter 和 Gmail 通知

> 原文：<https://www.sitepoint.com/home-made-twitter-and-gmail-notifications-with-php-and-arduino/>

*本文由 [Claudio Ribeiro](https://www.sitepoint.com/author/cribeiro/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

我有点迷恋 [Twitter](https://twitter.com/assertchris) 。这是我与大多数不幸与我生活在同一个城市的开发者交流的方式。我对物联网项目没那么着迷，但找到时间做这些项目比查看 Twitter 还难。

除非我能同时做到这两点。

自从第十周以来，我一直打算做一个项目，当有人想和我说话时，它会让我知道。一些看起来很酷，同时又无创的东西。这是我想出来的…

![Arduino logo](img/1a9b8ba498102bd0ba9e6d75982e26e1.png)

这些代码的大部分可以在 Github 上找到。我已经用 PHP 7.1 测试过了。

在这篇文章中，我谈了很多关于大头针的事情。我用这个词来表示从组件出来的管脚，以及这些管脚进入的 Arduino 插座。它们通常被称为码头或港口。当我谈到引脚时，我只是指一个可以连接元件或电线的地方。我每次都很明确，所以你会没事的！

## 该项目

我们要做的项目是一个简单的通知系统，连接一个 RGB LED、一个接近传感器和一个 PHP 脚本来收集通知。当收到通知时，RGB LED 将在它们之间循环，将 LED 淡入每种通知类型的适当颜色。

假设有人在 Twitter 上提到了我们。LED 应定期变为蓝色。然后，如果有人给我们发电子邮件，LED 应该在 Twitter 蓝色和 GMail 红色之间循环。我们可以把它扩展到 Fitbit 绿、脸书蓝等等。

如果我们看到了 Twitter 通知，我们应该能够在接近传感器前挥手，Twitter 蓝色应该从旋转中移除，留下 GMail 红色等。

## 硬件

我们可以使用任何 RGB LED。只要我们可以控制它褪色的颜色，我们就可以模拟我们追求的社交网络通知。真的，[差不多就是任何](https://www.sparkfun.com/products/8467)常见的[阳极 RGB LED](https://www.sparkfun.com/products/10821) 。如果你有一个不包括电阻，一定要把这些加到你的电路中。我稍后会谈到这一点…

你做的工作越多，你就越有可能碰到阳极和阴极这两个术语。公共阳极 RGB LED 是一种将一个引脚连接到微控制器或电池的正极引脚，并将三个引脚连接到地的 LED，这取决于您想要的颜色组合。

将红色引脚接地会闭合电路，使电流流过 LED 的红色部分。将红色引脚连接到我们 Arduino 上的脉宽调制(或 PWM)端口，并将该端口接地一半，将减少流经 LED 红色部分的电流量。

不同数量的接地会产生许多不同的颜色——就像你定义的 CSS 颜色一样多。

接下来，我们需要某种接近传感器。最简单的(在我看来)是一个[红外收发器](https://www.sparkfun.com/products/242)。你也可以使用[超声波收发器](https://www.sparkfun.com/products/13959)，但它们在中音范围更好(因为它们有最小的感应距离，所以你必须远离传感器)。

最后，我们需要一个 Arduino。你可以使用另一个微控制器，但这篇文章将特别提到 Arduino，因为我有三个触手可及的控制器，没有其他东西可以测试。

### 将事物联系在一起

![Connecting the circuit](img/93bbcfd05906eb1351f34fac230db704.png)

就他们而言，这是一条相对简单的赛道。我绝不是专家，但我确实设法找到了我买的红外传感器和 RGB LED 的数据表；所以把它们连接起来不会太麻烦。

重要的是，我们的 LED 引脚连接到 PWN 端口，以便我们可以逐渐调整红色、绿色和蓝色的数量，并且传感器引脚连接到模拟端口(在本例中为`A0`)。

*您可能想知道为什么我们不将引脚连接到模拟端口，或者为什么我们不将传感器连接到 PWM 端口。PWM 被设计成模拟开/关的程度，但是实现的方式是通过打开某个东西几分之一秒。*

*在正确的开/关周期频率下，led 看起来只是部分亮。这是我们视力的副作用，就像快速连续播放的 30 幅静态图像看起来像是运动视频一样。*

*我们需要将传感器连接到模拟端口，因为我们确实需要逐步测量，而`A0`将为我们提供这种测量。*

我的 RGB LED 在 3.3v 和 200mA 电流(电阻之前)下工作正常。因此，我可以将它连接到 3.3v 引脚，将 5v 引脚留给传感器的电源。

*我的传感器也有一个 pin 来启用/禁用传感器读数。我将对此进行编码，但请记住，您的传感器也可能有这种情况。如果有，将其连接到任何未使用的输出引脚(如`0`–`8`或`12`–`13`)，并确保将该引脚设置为高电平。*

我们还需要将 Arduino USB 端口连接到开发机器上一个开放的 USB 端口。

## 软件

现在让我们看看我们将用来控制事物的软件。在我们开始告诉 Arduino 做什么之前，我们应该定义一些服务:

```
namespace Notifier;

interface Service
{
    /**
     * Queries the service to trigger notification
     * alerts. Returns true if there are new
     * notifications to show.
     *
     * @return bool
     */
    public function query();

    /**
     * Marks the most recent notifications as seen.
     */
    public function dismiss();

    /**
     * Returns the name of this service.
     *
     * @return string
     */
    public function name();

    /**
     * Returns an array of pin color values,
     * for a common-anode RGB LED.
     *
     * @return int[]
     */
    public function colors();
} 
```

*这是出自`src/Service.php`*

我们连接的每个服务都需要有一种方法来查询新的通知。一旦我们取消了一个通知类型，我们还需要让相关的服务知道。

我们需要能够通过一个友好的名称来识别每个服务，并且我们还需要知道哪个光色与服务相关联。

### 连接到 Twitter

与 Twitter 交流意味着处理 [OAuth](https://dev.twitter.com/oauth) 。没有必要为此编写另一个抽象，所以我们将使用一个相当流行的 Twitter 库:

```
composer require endroid/twitter 
```

我们还需要创建和存储各种 API 键。前往 https://apps.twitter.com/app/new，创建一个新的应用程序。您可以使用任何回调 URL，因为我们无论如何都要覆盖它。

![Creating new Twitter applications](img/961f0442f93dc77c18ade2e8299018fa.png)

*创建新的 Twitter 应用程序*

记下消费者和访问令牌和密钥。将这些东西提交给 Github 通常是一个糟糕的想法，所以我们将它们存储为环境变量。让我们创建两个文件(名为`.env`和`.env.example`):

```
SERVICE_GMAIL_USERNAME=
SERVICE_GMAIL_PASSWORD=

SERVICE_TWITTER_CONSUMER_KEY=
SERVICE_TWITTER_CONSUMER_SECRET=
SERVICE_TWITTER_ACCESS_TOKEN=
SERVICE_TWITTER_ACCESS_TOKEN_SECRET= 
```

*这是出自`.env.example`*

*在做其他事情之前，创建一个`.gitignore`文件，并在其中添加`.env`。那是我们会存储秘密的东西的地方，所以我们肯定不想把它提交给 Github。*

接下来，让我们创建 Twitter 通知服务:

```
namespace Notifier\Service;

use Notifier\Service;
use Endroid\Twitter\Twitter as Client;

class Twitter implements Service
{
    /**
     * @var Client
     */
    private $client;

    /**
     * @var bool
     */
    private $new = false;

    /**
     * @var int
     */
    private $since;

    /**
     * @param string $consumerKey
     * @param string $consumerSecret
     * @param string $accessToken
     * @param string $accessTokenSecret
     */
    public function __construct($consumerKey, ↩
        $consumerSecret, $accessToken, $accessTokenSecret)
    {
        $this->client = new Client(
            getenv("SERVICE_TWITTER_CONSUMER_KEY"),
            getenv("SERVICE_TWITTER_CONSUMER_SECRET"),
            getenv("SERVICE_TWITTER_ACCESS_TOKEN"),
            getenv("SERVICE_TWITTER_ACCESS_TOKEN_SECRET")
        );
    }
} 
```

*这是出自`src/Service/Twitter.php`*

一般来说，在类外创建像`Client`这样的依赖项，并将它们作为构造函数参数引入是个好主意。但是这个客户端只是一个实现细节，我没有接口来提示反对。我认为在内部创建一个新的实例是可以的。

*`getenv`函数获取环境变量——与我们在`.env`中定义的相同。我们很快就会加载它们。*

让我们创建`query`方法:

```
/**
 * @inheritdoc
 *
 * @return bool
 */
public function query()
{
    if ($this->new) {
        return true;
    }

    $parameters = [
        "count" => 1,
    ];

    if ($this->since) {
        $parameters["since_id"] = $this->since;
    }

    $response = $this->client->query(
        "statuses/mentions_timeline",
        "GET", "json",
        $parameters
    );

    $tweets = json_decode($response->getContent());

    if (count($tweets) > 0) {
        $this->new = true;
        $this->since = (int) $tweets[0]->id;
    }

    return $this->new;
} 
```

*这是出自`src/Service/Twitter.php`*

我们将经常查询这项服务，在我们取消推文通知之前，我们希望它们继续显示在 LED 上。因此，如果我们之前发现了新的推文，我们可以提前返回。

如果我们已经在 Twitter 上查询了新的 tweets，我们会将它的 ID 添加到请求参数中。这意味着我们只会在后续的 API 请求中返回新的 tweets。

我们已经在构造函数中连接了客户端。因此，我们可以立即调用`client->query`方法，从提及时间线获取 tweets。如果自`since` ID 以来有任何新的推文，我们会报告新的推文。

我们只需要完成接口:

```
/**
 * @inheritdoc
 */
public function dismiss()
{
    $this->new = false;
}

/**
 * @inheritdoc
 *
 * @return string
 */
public function name()
{
    return "twitter";
}

/**
 * @inheritdoc
 *
 * @return int[]
 */
public function colors()
{
    return [1 - 0.11, 1 - 0.62, 1 - 0.94];
} 
```

*这是出自`src/Service/Twitter.php`*

我们将很快看到如何使用这个类。

### 连接到 Gmail

我们不必使用 OAuth 来连接 GMail，但我们必须启用 [IMAP](http://php.net/function.imap_open) ，并生成一个特定于应用程序的密码。如果您还没有在 PHP 中启用 IMAP，请参考您的安装/操作系统的帮助。当从源代码构建时，通常可以用`--with-imap`标志来安装它。OS X 的[家酿啤酒](http://brew.sh)也是如此:

```
brew install phpXX --with-imap 
```

XX 是你的 PHP 版本号，比如 56 或 71

要创建新的特定于应用程序的密码，请前往 https://security.google.com/settings/security/apppasswords:

![Creating new Google application-specific passwords](img/9e10b3e7ec50e59a618601b0aa47c06e.png)

*创建新的谷歌应用程序专用密码*

一旦你有了新密码，在`.env`中设置它，连同你创建它的电子邮件地址。接下来，让我们连接到 GMail:

```
namespace Notifier\Service;

use Notifier\Service;

class Gmail implements Service
{
    /**
     * @var bool
     */
    private $new = false;

    /**
     * @var array
     */
    private $emails = [];

    /**
     * @var string
     */
    private $username;

    /**
     * @var string
     */
    private $password;

    /**
     * @param string $username
     * @param string $password
     */
    public function __construct($username, $password)
    {
        $this->username = $username;
        $this->password = $password;
    }
} 
```

*这是出自`src/Service/Gmail.php`*

这看起来类似于我们如何开始 Twitter 服务类，但是我们不是创建一个新的 Twitter 客户端，而是使用`imap_open`来获得一个新的收件箱资源。

正如我之前发现的，每次我们想要查看新邮件时，我们都需要重新连接。这个 IMAP 客户端的一个奇怪的副作用…

接下来，我们需要创建`query`方法:

```
/**
 * @inheritdoc
 *
 * @return bool
 */
public function query()
{
    if ($this->new) {
        return true;
    }

    $inbox = imap_open(
        "{imap.gmail.com:993/imap/ssl}INBOX",
        $this->username, $this->password
    );

    if (!inbox) {
        return false;
    }

    $emails = imap_search($inbox, "ALL", SE_UID);

    if ($emails) {
        foreach ($emails as $email) {
            if (!in_array($email, $this->emails)) {
                $this->new = true;
                break;
            }
        }

        $this->emails = array_values($emails);
    }

    return $this->new;
} 
```

*这是出自`src/Service/Gmail.php`*

正如我前面提到的，每次我们想要查看新邮件时，我们都需要重新连接到 IMAP 服务器。因此，我们这样做，搜索收件箱中的所有电子邮件。然后，我们将返回的内容与之前缓存的消息 id 列表进行比较。如果有任何新的，我们报告新的电子邮件。

让我们完成剩下的接口实现:

```
/**
 * @inheritdoc
 */
public function dismiss()
{
    $this->new = false;
}

/**
 * @inheritdoc
 *
 * @return string
 */
public function name()
{
    return "gmail";
}

/**
 * @inheritdoc
 *
 * @return int[]
 */
public function colors()
{
    return [1 - 0.89, 1 - 0.15, 1 - 0.15];
} 
```

*这是出自`src/Service/Gmail.php`*

奇怪的是，YouTube 和 GMail 徽标中使用的 RGB 颜色是 226、38 和 28。我们从一个中减去这些，因为我们设置的 PWM 值越低，共阳极 led 就越亮。这是因为我们设置的 PWM 值越低，彩色引脚接地就越多，引脚接地会导致更强的电流流过 led。

让我们把这些和 Arduino 代码放在一起…

### 正在连接 Arduino

我们没有太多时间来学习 Arduino PHP 编程的基础知识。幸运的是，[我又写了一篇关于它的精彩帖子](https://www.sitepoint.com/php-arduino-and-minecraft-connecting-an-arduino-to-php)。遵循那里的说明，确保安装[大猩猩](https://github.com/oasynnoum/phpmake_serialport)扩展(如果你在 OS X)。

一旦我们安装了 [Firmata](http://firmata.org/wiki/Main_Page) ，我们就可以安装 [Carica](https://github.com/ThomasWeinert/carica-firmata) 库:

```
composer require carica/io dev-master@dev
composer require carica/firmata dev-master@dev 
```

此外，我们还需要安装环境变量库:

```
composer require vlucas/phpdotenv 
```

我们可以通过加载环境变量并连接到 Arduino:

```
require __DIR__ . "/vendor/autoload.php";

use Dotenv\Dotenv;

(new Dotenv(__DIR__))->load();

use Carica\Io;
use Carica\Firmata;

$loop = Io\Event\Loop\Factory::get();

$board = new Firmata\Board(
    Io\Stream\Serial\Factory::create(
        "/dev/cu.usbmodem1421", 57600
    )
);

print "connecting to arduino...";

$board
    ->activate()
    ->done(function () use ($loop, $board) {
        print "done" . PHP_EOL;
    });

$loop->run(); 
```

*这是出自`notifier.php`*

如果您不确定使用哪个端口(代替 my `/dev/cu.usbmodem1421`)，请键入以下内容:

```
ls /dev | grep usbmodem 
```

尝试每个返回的项目，直到您可以成功连接到 Arduino。一旦有了，让我们初始化引脚:

```
// diode pins

$board->pins[10]->mode = Firmata\Pin::MODE_PWM;
$board->pins[10]->analog = 1;

$board->pins[11]->mode = Firmata\Pin::MODE_PWM;
$board->pins[11]->analog = 1;

$board->pins[9]->mode = Firmata\Pin::MODE_PWM;
$board->pins[9]->analog = 1;

// sensor pins

$board->pins[12]->mode = Firmata\Pin::MODE_OUTPUT;
$board->pins[12]->digital = 1;

$board->pins[14]->mode = Firmata\Pin::MODE_ANALOG; 
```

*这是出自`notifier.php`*

这个不多说。每个 RGB LED 引脚被设置为 PWM 模式，并且它们的值被设置为`1`(因此 LED 看起来是关闭的)。由于我的红外传感器有一个启用/禁用引脚，我需要将该引脚设置为`1`(启用)。最后，我们将传感器读取引脚模式设置为模拟。

接下来，让我们连接 Twitter 和 GMail:

```
print "connecting to services...";

$services = new SplQueue();

$services->enqueue([
    new Notifier\Service\Twitter(
        getenv("SERVICE_TWITTER_CONSUMER_KEY"),
        getenv("SERVICE_TWITTER_CONSUMER_SECRET"),
        getenv("SERVICE_TWITTER_ACCESS_TOKEN"),
        getenv("SERVICE_TWITTER_ACCESS_TOKEN_SECRET")
    ), false
]);

$services->enqueue([
    new Notifier\Service\Gmail(
        getenv("SERVICE_GMAIL_USERNAME"),
        getenv("SERVICE_GMAIL_PASSWORD")
    ), false
]);

print "done" . PHP_EOL; 
```

*这是出自`notifier.php`*

我们可以在一个 [`SPLQueue`](http://php.net/manual/en/class.splqueue.php) 中将我们想要连接的每个服务排队。对于先进先出(或 FIFO)对象存储来说，这是一个有用的抽象概念。第二个布尔参数是服务是否有新的通知要显示。我们将在检测到新通知并解除时对此进行更改。

现在，让我们为新通知设置一个重复检查:

```
$loop->setInterval(function () use (&$services) {
    $remaining = count($services);

    while ($remaining--) {
        $next = $services->dequeue();
        $next[1] = $next[0]->query();
        $services->enqueue($next);
    }
}, 1000 * 5); 
```

*这是出自`notifier.php`*

我们可以使用事件循环的`setInterval`方法，它每`1000`毫秒* `5`(或`5`秒)重复一次。我们遍历队列中的每个服务，将它取出，设置它是否应该显示新的通知，然后将它放回队列。

毫无疑问，这是奇怪的语法。但这只是使用队列的副作用。

现在我们需要再次循环服务，轮流将 LED 改变成它们的颜色。我们可以将其设置为每 4 秒显示一种通知类型:

```
$service = null;

$next = function () use ($loop, $board, &$next, ↩
    &$services, &$service) {

    $remaining = count($services);

    while ($remaining--) {
        $next = $services->dequeue();
        $services->enqueue($next);

        if ($next[1]) {
            print "showing {$next[0]->name()}" . PHP_EOL;

            $service = $next;
            break;
        }
    }

    if (!$service) {
        print "no notifications" . PHP_EOL;
        return;
    }

    $colors = $service[0]->colors();

    $board->pins[10]->analog = $colors[0];
    $board->pins[11]->analog = $colors[1];
    $board->pins[9]->analog = $colors[2];

    $loop->setTimeout(function () use ($board, &$service) {
        $board->pins[10]->analog = 1;
        $board->pins[11]->analog = 1;
        $board->pins[9]->analog = 1;

        $service = null;
    }, 1000 * 1.5);

};

$loop->setInterval($next, 1000 * 4); 
```

*这是出自`notifier.php`*

我们使用类似的循环语法来循环每个服务，直到找到需要显示的服务。如果我们找到一个，我们就把它从队列的最前面拉出来，放回到最后。

然后，我们获取它的颜色并将管脚设置为适当的值。在`1.5`秒后，我们再次关闭引脚(通过将它们设置回`1`)。这个`$next`函数每隔`4`秒被调用一次。

最后，我们希望能够通过在红外传感器前挥手来消除通知类型:

```
$loop->setInterval(function() use ($board, ↩
    &$services, &$service) {

    if ($service !== null && ↩
        $board->pins[14]->analog < 0.1) {

        $remaining = count($services);

        while ($remaining--) {
            print "dismissing {$service[0]->name()}" ↩
                . PHP_EOL;

            $next = $services->dequeue();

            if ($next[0]->name() === $service[0]->name()) {
                $service = null;
                $next[0]->dismiss();
                $next[1] = false;
            }

            $services->enqueue($next);
        }
    }
}, 50); 
```

*这是出自`notifier.php`*

如果当前有一个服务正在显示通知，并且传感器正在读取一个低于`0.1`的值，我们认为这意味着传感器前面有一只手(并且它正在解除通知类型)。

我们遍历服务，告诉匹配的服务停止显示通知警报。我们还调用了`dismiss`方法，这样服务将再次开始检查新消息。该检查每`50`毫秒进行一次。

## 结论

使用 Arduino 和 PHP 我们可以做很多有趣的事情。这只是一个有用的项目。当我完成这篇文章的时候，它让我知道了许多新邮件和推文。现在我所要做的就是把它打包到一个项目箱中，然后我就可以把它带到工作中去了！

你喜欢这个吗？也许你对我的下一个项目有想法。请在下面的评论中告诉我们。

## 分享这篇文章