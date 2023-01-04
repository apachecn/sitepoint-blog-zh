# 使用 PHP 和 Twilio 的多因素认证

> 原文：<https://www.sitepoint.com/multi-factor-authentication-with-php-and-twilio/>

有各种方法用于确认人们实际上是他们所说的人:参考用户本人的某些方面(例如，生物测定学)，询问用户知道的某些东西(例如，密码)，或者询问用户实际拥有的某些东西(例如，RFID 卡)。传统网站实施单因素身份验证，只需要用户的密码。但是另一方面，多因素身份验证需要至少两种不同方法的验证，并且安全得多。

使用生物识别技术在你的网站上认证某人可能还有很长的路要走，有许多技术障碍和公民自由问题需要克服。多因素身份认证的一个更实用的方法是实施其他两个类别中的选项，例如要求通过电话(通过 SMS 或语音呼叫)向用户发送密码和确认令牌(用户知道密码，并且有电话)。这就是您将在本文中学习如何实现的内容。

如果你从头开始做，电话集成可能会令人沮丧，而且大多数时候建立自己的基础设施是不实际的(尽管有像 [Asterisk](http://www.asterisk.org/ "Asterisk - The Open Source Telephony Projects | Asterisk") 这样的商用硬件和程序是可能的)。幸运的是， [Twilio](http://www.twilio.com "Twilio Cloud Communications - APIs for Voice, VoIP and TExt Messaging") 提供了基础设施和 API，开发者可以使用它们来编写交互式电话应用程序，而不会有太多麻烦。在本文中，我将利用他们的服务。

你可以通过 Twilio 使用 TwiML (Twilio 标记语言)和他们的 REST API 来拨打和接听电话，发送和接收文本消息。您可以直接使用 API，或者使用可用的[助手库](http://www.twilio.com/docs/libraries "Twilio API Libraries - PHP, C#, Python, Java, and Ruby")。这里我将使用的库是 [twilio-php](https://github.com/twilio/twilio-php "twilio/twilio-php") ，这个库是由 twilio 发布并官方支持的。

那么，您准备好学习如何实现多因素身份认证了吗？请继续阅读！

## 从 PHP 使用 Twilio

连接到 Twilio 服务就像包含 twilio-php 库并创建一个新的`Services_Twilio`类实例一样简单。该对象的构造函数接受您帐户的 SID 和 auth 令牌，在您注册他们的服务后，它们会列在您的 Twilio 帐户的仪表板页面上。

有了可用的`Services_Twilio`实例，您可以通过 account 属性访问它们的 API。account 公开了一个代表您的 Twilio 帐户的实例`Services_Twilio_Rest_Account`。我在这里只使用一个帐户，但有可能有多个子帐户。这对于根据您的需求对您的交互进行分段非常有用。你可以通过阅读 Twilio 文档了解更多关于[子账户](https://www.twilio.com/docs/api/rest/subaccounts "Twilio Docs - API REST Subaccounts")的信息。

```
<?php
require "Services/Twilio.php";

define("TWILIO_SID", "…");
define("TWILIO_AUTH_TOKEN", "…");

$twilio = new Services_Twilio(TWILIO_SID, TWILIO_AUTH_TOKEN);
$acct = $twilio->account;
```

account 实例公开了其他几个属性，其中包括 calls 和 sms_messages。这些是像`Services_Twilio_Rest_Calls`和`Services_Twilio_Rest_SmsMessages`这样的对象的实例，它们分别封装了用于发出调用和消息的 REST 资源。然而，除了它们的`create()`方法之外，你很少使用这些对象，并且文档将公开它们的属性称为“实例资源”我也会这样做，以避免任何混淆。

### 发送短信

通过 SMS 消息实例资源(`$acct->sms_messages`)的`create()`方法来发送 SMS 消息。该方法采用三个参数:您帐户的 Twilio 号码(类似于电子邮件的“发件人地址”)、收件人号码(“收件人地址”)和您的邮件文本(最多 160 个字符)。

```
<?php
$code = "1234"; // some random auth code

$msg = $acct->sms_messages->create(
    "+19585550199", // "from" number
    "+19588675309", // "to" number
    "Your access code is " . $code
);
```

在幕后，twilio-php 库代表您向 Twilio API 发出一些 TwiML 的 POST 请求。调用返回了一个`Services_Twilio_Rest_SmsMessage`实例，它封装了关于消息的信息。您可以在文档中看到可用信息的完整列表[，但是更重要的值可能由`status`和`price`属性公开。`status`显示短信的状态(排队中、发送中、已发送或失败)，而`price`显示短信的费用。](https://www.twilio.com/docs/api/rest/sms "Twilio Docs - API REST SMS")

### 发送语音呼叫

发起语音呼叫是通过 Calls 实例资源(`$acct->calls`)的`create()`方法来完成的。像发送短信一样，你需要提供你的账号、收件人的号码和短信。然而，在这种情况下，消息是描述调用性质的 TwiML 文档的 URL。

```
<?php
// Note spaces between each letter in auth code. This forces
// the speech synthesizer to enunciate each digit.
$code = "1 2 3 4";

$msg = $acct->calls->create(
    "+19585550199", // "from" number
    "+19588675309", // "to" number
    "http://example.com/voiceCall.php?code=" . urlencode($code)
);
```

图书馆再次代表你发出 POST 请求，并进行语音呼叫。当被叫方接听电话时，Twilio 的进程检索并执行回调 URL 的 XML 中提供的命令。在上面启动语音呼叫的示例中，我已经将确认码作为 URL 中的 GET 参数传递给了回调脚本。当 Twilio 检索到 URL 时，PHP 将在呈现响应时使用该参数。

构建调用流只需要少数几个 TwiML 标记，但是可以用它们来定义非常复杂的流(比如电话树菜单等)。).这种场景的基本调用流可以由 PHP 生成，如下所示:

```
<?php
header("Content-Type: text/xml");
$code = isset($_GET["code"]) ? htmlspecialchars($_GET["code"]) : null;
$digit = isset($_POST["Digits"]) ? (int)$_POST["Digits"] : null;
$url = htmlspecialchars($_SERVER["PHP_SELF"]) . "?code=" . $code;

echo '<?xml version="1.0" encoding="UTF-8"?>';
?>
<Response>
<?php
if (is_null($code)) {
?>
 <Say>Sorry. An error occurred.</Say>
<?php
}
else {
?>
 <Gather action="<?php echo $url; ?>" numDigits="1">
<?php
    if (is_null($digit) || $digit == 1) {
?>
  <Say>Your access code is <?php echo $code; ?></Say>
<?php
    }
?>
  <Say>Press 1 to repeat the code.</Say>
 </Gather>
<?php
}
?>
 <Say>Good bye.</Say>
</Response>
```

这里使用的 TwiML 标签是`Response`(根元素)、`Say`(提供将由 Twilio 朗读的文本)和`Gather`(收集用户的输入)。

在朗读子元素`Say`所指示的文本时，Twilio 还将因为`Gather`而监听用户输入，之后暂停五秒钟，以便为用户提供一个窗口来输入她的响应。如果`Gather`超时而没有输入，它退出并执行随后的`Say`文本并终止呼叫。否则，输入将被回发到操作 URL，以便进一步处理。

`Gather` 的 [Twilio 文档非常好地解释了行为和可以用来修改行为的元素属性，甚至列出了几个例子。我建议你快速阅读一下。](http://www.twilio.com/docs/api/twiml/gather "Twilio Docs - API TwiML Gather")

在继续之前，还有最后一件需要注意的事情；我已经在启动脚本中的授权码的每个数字之间添加了空格。这迫使语音合成器说出每个数字，说“1234”而不是“1234”使用语音合成，有时我们在文本中看到的并不总是我们得到的。如果语音对话能让你的被叫者更清晰、更容易理解，那么篡改或拼错语音对话是可以的。

## 实施多因素身份认证

现在，您已经了解了使用 Twilio 进行短信和语音交互的基本工作流程，是时候看看它在登录过程中的位置了。您在这里看到的非常直观，为了清楚起见，我减少了附带的代码，因为您自己的登录过程的细节不可避免地会有所不同。

应该向用户显示一个启动该过程的登录表单。表单提交将引导您验证她的凭证，如果凭证不好就拒绝它们，但是有效的凭证不应该立即向您的应用程序验证用户。相反，考虑用户“部分认证”，这种状态允许她查看第二个表单，请求已经发送到她的电话的代码。只有在她提交了正确的代码之后，用户才应该被授权。

```
<?php
session_start();

require "Services/Twilio.php";

define("TWILIO_SID", "…");
define("TWILIO_AUTH_TOKEN", "…");
define("TWILIO_FROM_NUMBER", "+19585550199");
define("TWILIO_VOICE_URL", "http://example.com/voiceCall.php");

define("DB_HOST", "localhost");
define("DB_DBNAME", "test");
define("DB_USER", "dbuser");
define("DB_PASSWORD", "dbpassword");

define("CRYPT_SALT", '$2a$07$R.gJb2U2N.FmZ4hPp1y2CN');

define("AUTH_CODE_LENGTH", 4);

$db = new PDO("mysql:host=" . DB_HOST . ";dbname=" . DB_DBNAME,
    DB_USER, DB_PASSWORD);
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    // first stage of authentication
    if (empty($_SESSION["username"])) {
        $username = isset($_POST["username"])
            ? $_POST["username"] : "";
        $password = isset($_POST["password"])
            ? crypt($_POST["password"], CRYPT_SALT) : "";

        $query = sprintf("SELECT username, phone_number, code_pref FROM users WHERE username = %s AND password = %s",
           $db->quote($username),
           $db->quote($password));
        $result = $db->query($query);
        $row = $result->fetch(PDO::FETCH_ASSOC);
        $result->closeCursor();

        // invalid username/password provided
        if (!$row) {
            session_unset();
        }
        // valid username/password
        else {
            $_SESSION["isAuthenticated"] = false;
            $_SESSION["username"] = $row["username"];

            // generate and send auth code
            $code = "";
            for ($i = 0; $i < AUTH_CODE_LENGTH; $i++) {
                $code .= rand(0, 9);
            }

            $twilio = new Services_Twilio(TWILIO_SID, TWILIO_AUTH_TOKEN);
            $acct = $twilio->account;

            // send code via voice or SMS depending on
            // the user's preference
            if ($row["code_pref"] == "voice") {
                // add spaces to force enunciation
                $tmpCode = join(" ", string_split($code));
                $msg = $acct->calls->create(
                    TWILIO_FROM_NUMBER,
                    $_row["phone_number"],
                    TWILIO_VOICE_URL . "?code=" .
                        urlencode($tmpCode)
                );
            }
            else {
                $msg = $acct->sms_messages->create(
                    TWILIO_FROM_NUMBER,
                    $_row["phone_number"],
                    "Your access code is " . $code
                );
            }

            // "remember" code in session
            $_SESSION["code"] = $code;
        }
    }
    // second stage authentication
    else {
        $code = isset($_POST["code"]) ? $_POST["code"] : "";
        if ($code == $_SESSION["code"]) {
            $_SESSION["isAuthenticated"] = true;
            unset($_SESSION["code"]);
        }
    }
}

if (!empty($_SESSION["isAuthenticated"])) {
?>
<h1>W00t! You're Authenticated!</h1>
<?php
}
// present login forms
else {
    if (empty($_SESSION["username"])) {
?>
<h1>Login Step 1</h1>
<form action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>" method="post">
 <input type="text" name="username">
 <input type="password" name="password">
 <input type="submit" value="Login">
</form>
<?php
    }
    else {
?>
<h1>Login Step 2</h1>
<form action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>" method="post">
 <input type="text" name="code">
 <input type="submit" value="Confirm">
</form>
<?php
    }
}
```

上面的代码仅仅是说明性的。在您的实际应用程序中，您可能需要考虑添加以下内容:

*   添加将确认码重新发送到用户手机的链接。
*   在代码请求表单上添加一个取消链接，以防用户决定不再继续该流程。关于上面的代码，这样的链接将需要取消设置`$_SESSION["username"]`，因为该值除了存储用户名之外，还充当关于`$_SESSION["isAuthenticated"]`的“部分认证”标志。
*   如果提供了不正确的代码，请添加限制或帐户锁定。
*   根据您的偏执程度或您面临的法规遵从性要求，在某个地方记录身份验证事件。(需要多因素身份认证的应用程序通常非常敏感，足以保证审计跟踪！)

此外，您可能需要考虑为您的目的创建足够复杂的身份验证代码。4 位数的数字代码很常见，但你并不局限于此。如果你选择使用字母或混合的字母数字值，我建议避免容易混淆的值(比如数字 0 对字母 O，数字 1 对字母 I，等等。).

## 摘要

经济实惠的移动设备和 IP 电话的激增增加了与用户交互的额外渠道，在本文中，您将了解一种利用这些渠道的方法，即使用“云通信”服务 Twilio 实现多因素身份认证。

我希望这篇文章对理解多因素身份验证和 Twilio 有所帮助。欢迎在下方发表评论，分享您如何在自己的应用中使用 Twilio 或实施多因素身份验证。我很想听听！

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章