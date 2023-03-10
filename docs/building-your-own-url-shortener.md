# 建立自己的网址缩写

> 原文：<https://www.sitepoint.com/building-your-own-url-shortener/>

我们大多数人都熟悉在我们的 Twitter 或脸书订阅源上看到类似于 [bit.ly](http://bit.ly "bitly | ♥ your bitmarks") 或[t.co](http://t.co "t.co / Twitter")的网址。这些是缩短的 URL 的例子，它们是一个短的别名或指向一个更长的页面链接的指针。例如，我可以给你发送 http://bit.ly/SaaYw5[的简短网址](http://bit.ly/SaaYw5 "how do I iron a shirt - Google Search")，它会把你转到一个很长的谷歌网址，上面有如何熨衬衫的搜索结果。把 20 个字符的 bit.ly 网址用短信发给你的儿子会容易得多，他正在大学里准备他的第一次重要工作面试。

在这篇文章中，你将学习如何为你的网站创建一个全功能的网址缩写器，无论你是否使用前端控制器/框架，它都可以工作。如果您使用前端控制器，我将向您介绍如何轻松集成这个 URL 缩短器，而无需深入研究控制器的编程。

## 回答一些常见问题

因此，有了 bit.ly 和许多其他类似的网址缩写工具，我们又何必费事去构建自己的呢？这些缩短服务中的大多数甚至有一个易于使用的 API，这样我们可以通过编程生成一个缩短的 URL，并在我们的 PHP 脚本中使用它。

最好的理由是为了方便、美观和品牌认知度。例如，如果你的网站有一个创建大量报告的应用程序，一个非常活跃的博客或一个大相册，就会有很多链接。一个网址缩写器将允许你以编程的方式创建一个干净、简单的链接，可以通过电子邮件发送给你的读者或者发布在你的网站上。拥有自己的网站的明显优势是，你的读者会立刻对你的网站产生品牌认同。

你可能想知道为什么你总是看到缩写的 URL 中字母和数字混在一起。通过每个数字有十个以上的选项(0-9 ),我们能够有更多的组合，同时保持代码尽可能短。

我们将使用的字符是数字 1-9 以及各种大小写字母。我已经删除了所有的元音字母，以防止产生非故意的坏单词链接，并且我已经删除了任何可能相互混淆的字符。这为我们提供了一个列表，每个数字大约有 50 个字符，这意味着两个字符有 2，500 种可能的组合，三个字符有 125，000 种可能性，四个字符有多达 650 万种组合！

## 规划数据库

让我们来布置一下`short_urls`桌子。这是一个简单的表，下面是 create 语句:

```
CREATE TABLE IF NOT EXISTS short_urls (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  long_url VARCHAR(255) NOT NULL,
  short_code VARBINARY(6) NOT NULL,
  date_created INTEGER UNSIGNED NOT NULL,
  counter INTEGER UNSIGNED NOT NULL DEFAULT '0',

  PRIMARY KEY (id),
  KEY short_code (short_code)
)
ENGINE=InnoDB;
```

对于完整的 URL，我们有标准的自动递增主键和字段，URL 的缩写代码(为了更快的检索而索引)，行创建时的时间戳，以及短 URL 被访问的次数。

请注意，`long_url`字段的最大长度为 255 个字符，这对于大多数应用程序来说应该足够了。如果您需要存储更长的 URL，那么您需要将其定义更改为`TEXT`。

现在来看 PHP！

## 创建 URL 短代码

创建和解码短 URL 代码的代码将位于一个名为 short URL 的类中。首先，让我们看看负责创建短代码的代码:

```
<?php
class ShortUrl
{
    protected static $chars = "123456789bcdfghjkmnpqrstvwxyzBCDFGHJKLMNPQRSTVWXYZ";
    protected static $table = "short_urls";
    protected static $checkUrlExists = true;

    protected $pdo;
    protected $timestamp;

    public function __construct(PDO $pdo) {
        $this->pdo = $pdo;
        $this->timestamp = $_SERVER["REQUEST_TIME"];
    }

    public function urlToShortCode($url) {
        if (empty($url)) {
            throw new Exception("No URL was supplied.");
        }

        if ($this->validateUrlFormat($url) == false) {
            throw new Exception(
                "URL does not have a valid format.");
        }

        if (self::$checkUrlExists) {
            if (!$this->verifyUrlExists($url)) {
                throw new Exception(
                    "URL does not appear to exist.");
            }
        }

        $shortCode = $this->urlExistsInDb($url);
        if ($shortCode == false) {
            $shortCode = $this->createShortCode($url);
        }

        return $shortCode;
    }

    protected function validateUrlFormat($url) {
        return filter_var($url, FILTER_VALIDATE_URL,
            FILTER_FLAG_HOST_REQUIRED);
    }

    protected function verifyUrlExists($url) {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_NOBODY, true);
        curl_setopt($ch,  CURLOPT_RETURNTRANSFER, true);
        curl_exec($ch);
        $response = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        curl_close($ch);

        return (!empty($response) && $response != 404);
    }

    protected function urlExistsInDb($url) {
        $query = "SELECT short_code FROM " . self::$table .
            " WHERE long_url = :long_url LIMIT 1";
        $stmt = $this->pdo->prepare($query);
        $params = array(
            "long_url" => $url
        );
        $stmt->execute($params);

        $result = $stmt->fetch();
        return (empty($result)) ? false : $result["short_code"];
    }

    protected function createShortCode($url) {
        $id = $this->insertUrlInDb($url);
        $shortCode = $this->convertIntToShortCode($id);
        $this->insertShortCodeInDb($id, $shortCode);
        return $shortCode;
    }

    protected function insertUrlInDb($url) {
        $query = "INSERT INTO " . self::$table .
            " (long_url, date_created) " .
            " VALUES (:long_url, :timestamp)";
        $stmnt = $this->pdo->prepare($query);
        $params = array(
            "long_url" => $url,
            "timestamp" => $this->timestamp
        );
        $stmnt->execute($params);

        return $this->pdo->lastInsertId();
    }

    protected function convertIntToShortCode($id) {
        $id = intval($id);
        if ($id < 1) {
            throw new Exception(
                "The ID is not a valid integer");
        }

        $length = strlen(self::$chars);
        // make sure length of available characters is at
        // least a reasonable minimum - there should be at
        // least 10 characters
        if ($length < 10) {
            throw new Exception("Length of chars is too small");
        }

        $code = "";
        while ($id > $length - 1) {
            // determine the value of the next higher character
            // in the short code should be and prepend
            $code = self::$chars[fmod($id, $length)] .
                $code;
            // reset $id to remaining value to be converted
            $id = floor($id / $length);
        }

        // remaining value of $id is less than the length of
        // self::$chars
        $code = self::$chars[$id] . $code;

        return $code;
    }

    protected function insertShortCodeInDb($id, $code) {
        if ($id == null || $code == null) {
            throw new Exception("Input parameter(s) invalid.");
        }
        $query = "UPDATE " . self::$table .
            " SET short_code = :short_code WHERE id = :id";
        $stmnt = $this->pdo->prepare($query);
        $params = array(
            "short_code" => $code,
            "id" => $id
        );
        $stmnt->execute($params);

        if ($stmnt->rowCount() < 1) {
            throw new Exception(
                "Row was not updated with short code.");
        }

        return true;
    }
...
```

当我们实例化我们的`ShortUrl`类时，我们将把我们的 PDO 对象实例传递给它。构造函数存储这个引用并设置`$timestamp`成员。

我们调用`urlToShortCode()`方法，向它传递我们希望缩短的长 URL。该方法包装了创建短 URL 代码所需的一切，我们将把它附加到我们的域名中。

`urlToShortCode()`调用`validateUrlFormat()`,它只是使用一个 PHP 过滤器来确保 URL 的格式正确。然后，如果静态变量`$checkUrlExists`为真，`verifyUrlExists()`将被调用，它使用 cURL 来联系 URL 并确保它不会返回 404(未找到)错误。您也可以检查 200 (OK)状态，但是如果页面意外返回 301(已移动)或 401(未授权)响应代码，这可能会导致问题。

有重复的条目是没有意义的，所以代码用`urlExistsInDb()`检查，它在数据库中查询长 URL。如果它找到了 URL，它将返回相应的短代码，否则它将返回 false，因此我们知道我们需要创建它。注意，http://www.example.com 和 http://example.com 是不同的网址，所以如果你想防止这种重复，那么你必须添加一些正则表达式。

`createShortCode()`将以下任务委托给特定方法:

1.  `insertUrlInDb()`将长 URL 插入数据库并返回新行的 ID。
2.  `convertIntToShortCode()`将新行的 ID 转换为基数为 50 的数字方案。
3.  `insertShortCodeInDb()`用新创建的短代码更新行。

当我们想要创建一个短 URL 时，我们所要做的就是实例化该类，将一个 PDO 实例传递给构造函数，用我们想要缩短的长 URL 调用`urlToShortCode()`方法，将返回的短代码附加到域中，并将其传递回请求它的控制器。

```
<?php
include "../include/config.php";
include "../include/ShortUrl.php";

try {
    $pdo = new PDO(DB_PDODRIVER . ":host=" . DB_HOST .
        ";dbname=" . DB_DATABASE,
        DB_USERNAME, DB_PASSWORD);
}
catch (PDOException $e) {
    trigger_error("Error: Failed to establish connection to database.");
    exit;
}

$shortUrl = new ShortUrl($pdo);
try {
    $code = $shortUrl->urlToShortCode($_POST["url"]);
    printf('<p><strong>Short URL:</strong> <a href="%s">%1$s</a></p>',
        SHORTURL_PREFIX . $code);
    exit;
}
catch (Exception $e) {
    // log exception and then redirect to error page.
    header("Location: /error");
    exit;
}
```

## 解码短码

解码短代码并获得长 URL 的代码也是`ShortUrl`类的一部分。我们调用`shortCodeToUrl()`方法，并将从 URI 中提取的短代码传递给它。`shortCodeToUrl()`还接受一个可选参数`$increment`，默认为 true。然后，它委托以下内容:

1.  `validateShortCodeFormat()`确保提供的短代码仅包含字母和数字。
2.  `getUrlFromDb()`在数据库中查询提供的短代码，并返回记录的 id、long_url 和 counter 字段。
3.  如果`$increment`参数为真，则调用`incrementCounter()`来递增该行的计数器字段。

这是班上的其他人:

```
...
    public function shortCodeToUrl($code, $increment = true) {
        if (empty($code)) {
            throw new Exception("No short code was supplied.");
        }

        if ($this->validateShortCode($code) == false) {
            throw new Exception(
                "Short code does not have a valid format.");
        }

        $urlRow = $this->getUrlFromDb($code);
        if (empty($urlRow)) {
            throw new Exception(
                "Short code does not appear to exist.");
        }

        if ($increment == true) {
            $this->incrementCounter($urlRow["id"]);
        }

        return $urlRow["long_url"];
    }

    protected function validateShortCode($code) {
        return preg_match("|[" . self::$chars . "]+|", $code);
    }

    protected function getUrlFromDb($code) {
        $query = "SELECT id, long_url FROM " . self::$table .
            " WHERE short_code = :short_code LIMIT 1";
        $stmt = $this->pdo->prepare($query);
        $params=array(
            "short_code" => $code
        );
        $stmt->execute($params);

        $result = $stmt->fetch();
        return (empty($result)) ? false : $result;
    }

    protected function incrementCounter($id) {
        $query = "UPDATE " . self::$table .
            " SET counter = counter + 1 WHERE id = :id";
        $stmt = $this->pdo->prepare($query);
        $params = array(
            "id" => $id
        );
        $stmt->execute($params);
    }
}
```

## 将这一切结合在一起

构建/改变前端控制器或根据现有框架定制这个包超出了本文的范围，所以我选择将我们的解码逻辑包含在一个名为`r.php` (r 代表重定向)的文件中。我们可以将我们缩短的 URL 写成 http://example.com/r/X4c，其中`r.php`(或者`r/index.php`取决于你的设计)将是控制器。这种格式将很容易集成到几乎任何框架中，而无需触及现有的前端控制器。

另外，如果你想学习如何构建自己的前端控制器，可以看看优秀系列[前端控制器模式介绍](https://www.sitepoint.com/front-controller-pattern-1/ "An Introduction to the Front Controller Pattern, Part 1")。

这种设计的一个优点是，如果你愿意，你可以为你的站点的不同部分使用不同的表来保持短代码有组织并且尽可能的短。http://example.com/b/可以放博客，http://example.com/i/可以放图片。

"但是如果我不使用前端控制器或框架呢？"你问，“我刚才是不是白读了整篇文章？”虽然它不那么漂亮，但是你可以使用格式 http://example.com/r?c=X4c，其中`r/index.php`包含解码脚本。

下面是`r.php`的样子:

```
<?php
include "../include/config.php";
include "../include/ShortUrl.php";

// How are you getting your short code?

// from framework or front controller using a URL format like
// http://.example.com/r/X4c
// $code = $uri_data[1];

// from the query string using a URL format like
// http://example.com/r?c=X4c where this file is index.php in the
// directory http_root/r/index.php
$code = $_GET["c"];

try {
    $pdo = new PDO(DB_PDODRIVER . ":host=" . DB_HOST .
        ";dbname=" . DB_DATABASE,
        DB_USERNAME, DB_PASSWORD);
}
catch (PDOException $e) {
    trigger_error("Error: Failed to establish connection to database.");
    exit;
}

$shortUrl = new ShortUrl($pdo);
try {
    $url = $shortUrl->shortCodeToUrl($code);
    header("Location: " . $url);
    exit;
}
catch (Exception $e) {
    // log exception and then redirect to error page.
    header("Location: /error");
    exit;
}
```

根据您获取短代码的方式，变量`$code`与您的其他配置设置一起设置。我们建立我们的 PDO 连接，实例化一个`ShortUrl`的实例，调用`shortCodeToUrl()`，给它传递短代码，让计数器设置默认值。如果短代码是有效的，您将有一个长 URL，您可以将用户重定向到该 URL。

## 最后

所以你有它，你自己的网址缩写，非常容易添加到你现有的网站。当然，有很多方法可以改进这个包，例如:

*   抽象您的数据库交互以删除冗余代码。
*   添加一种缓存缩短的 URL 请求的方式。
*   在`counter`字段之外，为请求的短 URL 添加一些分析。
*   添加一个过滤掉恶意页面的方法。

我想借此机会感谢 Timothy Boronczyk 在我写作过程中耐心的建议。很荣幸能为 SitePoint 写这篇文章，并与他一起工作。

请随意在 GitHub 上分享本文的示例代码，并分享您的贡献和改进。

谢谢你的阅读，祝你 PHPing 愉快！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章