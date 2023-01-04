# 防止跨站点请求伪造(CSRF)

> 原文：<https://www.sitepoint.com/preventing-cross-site-request-forgeries/>

跨站点请求伪造(CSRF)是一种常见且严重的利用方式，用户被诱骗执行了他并没有明确打算执行的操作。例如，当用户登录到他最喜欢的网站之一并点击看似无害的链接时，就会发生这种情况。在后台，他的个人资料信息会自动更新为攻击者的电子邮件地址。然后，攻击者可以使用网站的密码重置功能，通过电子邮件向自己发送新密码，她就成功窃取了帐户。用户登录网站时允许执行的任何操作，攻击者都可以代表他/她执行，无论是更新个人资料、向购物车添加商品、在论坛上发布消息，还是几乎任何其他操作。

如果你以前从未听说过 CSRF，或者你在编写代码时没有考虑到预防，那么我不想打断你，但很可能你很容易受到攻击。在本指南中，我将向您展示 CSRF 攻击是如何工作的，以及您可以做些什么来保护您的用户。

## 它是如何工作的

要了解 CSRF 攻击是如何工作的，最好先看看它是如何工作的。为了说明攻击，我想创建一个能够注销活动会话的简单示例。我们将需要一个登录页面(`login.php`)，一个处理脚本来处理会话的登录和注销(`process.php`)，最后是一个示例攻击(`harmless.html`)。

首先，这里是`login.php`的代码:

```
<?php
session_start();
?>
<html>
 <body>
<?php
if (isset($_SESSION["user"])) {
    echo "<p>Welcome back, " . $_SESSION["user"] . "!<br>";
    echo '<a href="process.php?action=logout">Logout</a></p>';
}
else {
?>
  <form action="process.php?action=login" method="post">
   <p>The username is: admin</p>
   <input type="text" name="user" size="20">
   <p>The password is: test</p>
   <input type="password" name="pass" size="20">
   <input type="submit" value="Login">
  </form>
<?php
}
?>
 </body>
</html>
```

`login.php`脚本从初始化会话数据开始。然后检查是否已经设置了`$_SESSION["user"]`,如果是，显示一条欢迎消息和一个注销链接。否则，它会显示登录表单。

这是处理脚本，`process.php`:

```
<?php
session_start();

switch($_GET["action"]) {
    case "login":
        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            $user = (isset($_POST["user"]) &&
                ctype_alnum($_POST["user"]) ? $_POST["user"] : null;
            $pass = (isset($_POST["pass"])) ? $_POST["pass"] : null;
            $salt = '$2a$07$my.s3cr3t.SalTY.str1nG$';

            if (isset($user, $pass) && (crypt($user . $pass, $salt) ==
                crypt("admintest", $salt))) {
                $_SESSION["user"] = $_POST["user"];
            }
        }
        break;

    case "logout":
        $_SESSION = array();
        session_destroy();
        break;
}

header("Location: login.php");
?>
```

`process.php`脚本也是从初始化会话数据开始，然后检查是否有动作要处理。我们使用 PHP 的以及`[ctype_alnum()](http://php.net/ctype_alnum "PHP: ctype_alnum - Manual")`和`[crypt()](http://php.net/crypt "PHP: crypt - Manual")`函数执行一些基本的输入验证，然后相应地设置或销毁会话变量。在脚本的结尾，用户被重定向回`login.php`。

现在让我们把注意力集中在攻击者可能创建的文件上，以利用我们前面例子中的代码。这是漏洞利用代码，`harmless.html`:

```
<html>
 <body>
  <p>This page is harmless... Or is it?</p>
  <!-- Address to target website -->
  <img src="process.php?action=logout" style="display: none;">
 </body>
</html>
```

如果您访问`login.php`并登录您的帐户，然后在登录时您继续访问攻击者的页面，即使您没有单击注销链接，您也会被自动注销。浏览器向服务器发送请求以访问`process.php`脚本，期望它是一个图像文件。处理脚本无法区分用户点击注销链接发起的有效请求和浏览器被诱骗发送的巧尽心思构建的请求。

`harmless.html`文件可能托管在与您登录的服务器完全不同的服务器上，它仍然可以工作，因为攻击者的页面正在使用您在后台打开的会话代表您发出请求。即使您登录的网站是在专用网络上，请求也会从您的 IP 地址提交，就好像是您自己发出的请求一样，因此几乎不可能追踪到攻击的来源。

此外，如果您允许您的用户链接到图片作为个人资料头像或类似的，没有适当的转义和净化用户提供的数据，攻击甚至可能在您自己的网络域。

虽然将某人从网站注销并不令人印象深刻，但`harmless.html`也可以轻松地包含一个隐藏的内嵌框架(而不是图像标签),该框架带有一个在页面加载时自动提交的表单，这将使本指南开头提到的任何攻击成为公平的游戏。

希望现在你明白 CSRF 病毒攻击有多严重，所以让我们来看看你如何预防它们。

## 保护您的用户

为了确保一个动作实际上是由用户而不是第三方执行的，您需要将它与某种惟一的标识符相关联，然后可以对其进行验证。为了防止攻击，我们可以修改`login.php`如下:

```
<?php
// make a random id
$_SESSION["token"] = md5(uniqid(mt_rand(), true));
echo '<a href="process.php?action=logout&csrf=' . $_SESSION["token"] . '">Logout</a></p>';
```

然后为了验证标识符，我们可以修改`process.php`如下:

```
case "logout":
    if (isset($_GET["csrf"]) && $_GET["csrf"] == $_SESSION["token"]) {
        $_SESSION = array();
        session_destroy();
    }
    break;
```

通过这些简单的修改，`harmless.html`将不再起作用，因为攻击者被赋予了额外的任务，必须猜测额外的随机令牌值。为了保护表单，您还可以将标识符包含在隐藏字段中，如下所示，以便它与表单数据的其余部分一起提交。

```
<input type="hidden" name="csrf" value="<?php echo $_SESSION["token"]; ?>">
```

在我自己看来，尽管我尊敬的朋友和同事们出于善意的骚扰，我还是更喜欢使用 PHP 的`[session_id()](http://php.net/session_id "PHP: session_id - Manual")`而不是生成一个随机令牌，因为我并不特别喜欢“通过模糊实现安全性”的方法。除了使用`session_id()`，我还在登录或更新敏感信息时使用`[session_regenerate_id()](http://php.net/session_regenerate_id "PHP: session_regenerate_id - Manual")`,以降低任何会话固定攻击的风险，并且我从不将 id 附加到将存储在浏览器历史中的 URL 上。随意暴露不必要的会话 id 从来都不是一个好主意，但是只要你小心，我认为这是一个更优雅的方法。当然，如果您的网站使用某种不使用会话的身份验证，那么无论如何您都需要生成自己的 id。

## 结论

到目前为止，您应该已经了解了 CSRF 攻击的基本原理，以及可以采取哪些措施来保护您的站点和用户。正如本·富兰克林所说:“一盎司的预防抵得上一磅的治疗。”我相信我们所有人都宁愿花时间来确保我们编写的代码是安全的，而不是处理压力、头痛和围绕妥协的可能诉讼。

图片 via[Blazej Lyjak](http://www.shutterstock.com/gallery-507601p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章