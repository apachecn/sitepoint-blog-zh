# 注册 PayPal 教程(3/3):设置您真正的 PAYPAL 商业帐户

> 原文：<https://www.sitepoint.com/register-paypal-tutorial-3-3-real-register-paypal-project/>

1.  解释 PayPal 的工作原理(IPN 和 PDT 流程)。 [第一章](http://www.jquery4u.com/tutorials/register-paypal-tutorial-3-1/)
2.  给出一个很好的例子来说明如何注册与贝宝合作，包括数据库，控制器和 jQuery 功能的前端表单。并解释如何设置一个贝宝帐户(沙盒和真正的贝宝商业帐户的例子)。 [第二章](http://www.jquery4u.com/tutorials/register-paypal-tutorial-3-2-real-register-paypal-project/)

本教程给出了 PayPal 流程的大图(PDT 和 IPN)，以及一个从起点到终点的真实项目。很好的解释和图片，详细的注释在代码行。希望它对所有读者都有用。

## 第三章

在设置贝宝沙盒与一个真正的项目，我们可以开始设置我们的业务贝宝帐户，并开始发布我们的项目和赚钱。

### 设置真正的 PayPal 商业帐户

正版 PayPal 商业账户面板与沙盒略有不同，但其他设置都大同小异。所以我只是指出设置在哪里，设置应该和前面的例子一样。

首先申请你的商务 PayPal 账户，但是我们跳过这一步，因为 PayPal 商务账户在不同的情况和公司有很大的不同。如你所见，有许多个人敏感的细节，我不得不掩盖。所以，点击个人资料链接。

然后，在源代码中，注释掉与“沙盒”相关的所有内容，并更改标记，取消注释沙盒代码旁边的代码。

我强调了代码中的变化:

1.您需要将重定向 URL 更改为 PayPal，而不是沙盒:(注意:代码片段包含'>'和' & '，它们应该是'>'和' & ')

```
public function registerSubmit()
{
// more code here ...
// return url to paypal
// return json_encode(array('result' => true, 'url' => "https://www.sandbox.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=HA9DZBCKXKCL2&item_number=".$itemId."&amount=".$this->amount));
        return json_encode(array('result' => true, 'url' => "https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=SSZF9WEUY87GS&item_number=".$itemId."&amount=".$this->amount));
```

2._PDT 变更:

```
private function _PDT()
{
// more code here ...
// $auth_token = "_PJaHiwRfwMmWzW-9nuPuSguYxC-1d9KpxaasaNANtIvyOcmqY6jXNkRmxW";
$auth_token = "OxDenzKmrWPyEXU0YzIg2zs-VAe7ufCADyjbfxF_RpREL4rLEslZrSa21R4";$req .= "&tx=$tx_token&at=$auth_token";// post back to PayPal system to validate$header = "POST /cgi-bin/webscr HTTP/1.0rn";

// $header .= "Host: www.sandbox.paypal.comrn";
$header .= "Host: www.paypal.comrn";

$header .= "Content-Type: application/x-www-form-urlencodedrn";
$header .= "Content-Length: " . strlen($req) . "rnrn";

// $fp = fsockopen ('ssl://www.sandbox.paypal.com', 443, $errno, $errstr, 30); // open socket
$fp = fsockopen ('ssl://www.paypal.com', 443, $errno, $errstr, 30); // open socket
// more code here ...
}
```

3._IPN 的变化:

```
private function _PDT()
{
// more code here ...
// $url= 'https://www.sandbox.paypal.com/cgi-bin/webscr';
$url= 'https://www.paypal.com/cgi-bin/webscr';
// more code here ...
// curl_setopt($ch, CURLOPT_HEADER , array('Host: www.sandbox.paypal.com'));
curl_setopt($ch, CURLOPT_HEADER , array('Host: www.paypal.com'));
// more code here ...
}
```

![](img/e99bd440c8be46ee2926def07dd73991.png)

然后转到“我的销售工具”，你可以设置你的 PDT，IPN 和贝宝按钮。

### 然后，搞定！谢谢大家！

我希望你喜欢我关于如何使用 Paypal 设置自动支付的快速教程。

## 分享这篇文章