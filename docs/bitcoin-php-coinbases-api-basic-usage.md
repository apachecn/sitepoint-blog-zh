# 比特币和带有比特币基地 API 的 PHP 基本用法

> 原文：<https://www.sitepoint.com/bitcoin-php-coinbases-api-basic-usage/>

你有没有想过出售你的服务来换取比特币？这并不奇怪——今天，许多大公司实际上都在这么做。从 OkCupid 到 KhanAcademy，甚至连 WordPress 都在接受比特币。此外，一些国家正在考虑将其作为一种货币。今天，我们将看到如何通过比特币基地 API(及其 SDK)以一种简单的方式在您的网站/应用程序上接受比特币支付。

## 比特币基地 SDK

比特币基地为每个人提供了一些有趣的工具和 SDK，以及一些方便实惠的价格条件。

以下是你必须了解的定价知识:

*   使用比特币基地接收付款是免费的；
*   只有当你想通过银行账户转账时，你才需要支付 1%的费用，但前提是你的销售额超过 1000000 美元(是的，100 万美元)；
*   在比特币网络中，你能做的最小支付是 0.001 BTC。然而，使用比特币基地钱包，这个限额下降到 1 Satoshi(0.00000001 BTC)；

另一个重要(也是有趣)的事情是，你可以选择激活“即时兑换”服务，该服务无需额外步骤，自动将比特币支付金额立即转换为你选择的货币。如果你想避免货币不稳定，尽快转移你的钱，这真的很酷。

也就是说…我们如何实现这种集成？

## 集成类型

就像许多其他在线支付服务一样，比特币基地提供了两种集成到您的 web 应用程序的主要方式。第一种更快更容易。第二个更难一点，但也更深入，适合更大的项目。

第一种集成类型包括使用比特币基地提供的商业工具之一。您可以使用[按钮](https://coinbase.com/docs/merchant_tools/payment_buttons)、[页面](https://coinbase.com/docs/merchant_tools/payment_pages)和[框架](https://coinbase.com/docs/merchant_tools/payment_iframes)。如果你正在使用 CMS 或者电子商务(WordPress，WooCommerce，Magento…)有很多你喜欢的插件。

第二种，我们今天将看到一个例子，是服务的[完全集成，无需去比特币基地网站生成一些标准的按钮代码。其实我们会用具体的 PHP SDK 来做。](https://coinbase.com/docs/api/overview)

我们可以用这个 SDK 做什么？关于比特币基地的专门页面很清楚:

*   卖出或买入比特币(或用你的货币兑换)；
*   通过电子邮件或比特币地址发送或请求比特币；
*   作为商户接受比特币支付；
*   将你的比特币储存在一个或多个钱包中；
*   有权访问比特币原始网络数据(区块、交易等等……)；
*   处理小额支付和经常性支付；

每样都来一点。

目前，有三个可用的 SDK:用于 Ruby、Java 和 PHP。还有很多其他语言的非官方库(Python，。NET，Node…)。正如我之前告诉你的，我们将使用的 SDK 是 PHP SDK，你可以[在 GitHub](https://github.com/coinbase/coinbase-php) 上找到。

**注意:**在您进入下一步之前，我假设您能够在比特币基地上创建一个帐户，或者您已经有了一个帐户。

## PHP SDK

### 装置

让我们从项目的包安装开始。如果你看一下 SDK 的 GitHub 页面，你会发现没有任何关于用 Composer 做这件事的内容。然而，简单的搜索将很容易产生`coinbase/coinbase`包的[包装者页面](https://packagist.org/packages/coinbase/coinbase)。

您可以安装它

```
{
	    "require": {
	        "coinbase/coinbase": "dev-master"
	    }
	}
```

在您的`composer.json`文件和一个

```
composer update
```

### 证明

在开始编写代码之前，我们先来谈谈身份验证。为了访问 API 方法，比特币基地有两种方法来验证你是一个开发者。你可以选择一个简单的 API 密匙访问，用一个 API 密匙来拥有更多的安全性。或者，如果您愿意，可以使用 OAuth 2。区别不仅在于复杂程度，还在于你要处理的情况。

比特币基地文档非常清楚:如果你想使用你的账户并对其进行修改，你可以使用 API 密匙系统。如果你想让用户通过你的 app(比如你搭建的客户端)使用他的账号，最好的办法就是使用 OAuth。

**API 密钥+秘密**

一旦你有了一个比特币基地的账户，创建一个 API 密匙真的很简单。你所要做的就是到[这里](https://coinbase.com/settings/api)，点击“+新 API 键”。

第一次，你可能需要向 [Authy](https://www.authy.com/) 确认你的账户。

您将看到的屏幕如下所示:

![The New API Key Screen](img/16a619bc18f3e601b5d934fd001f1f7e.png)

为了获得访问权限，您必须指定要使用的帐户以及要分配给该特定密钥的权限。此外，您可以选择一个或多个 IP 作为白名单。如果不指定任何内容，就不会有白名单。

之后，点击“创建”确认程序，当您想要激活 API 键时，点击“启用”。

**OAuth 2.0**

如果您想使用 OAuth 2.0，您必须遵循类似的过程。但是，这一次，您将不会创建 API 密钥，而是创建 OAuth 应用程序。为此，请前往[https://coinbase.com/oauth/applications](https://coinbase.com/oauth/applications)。在那里，点击“+创建应用程序”。您将看到这样一个屏幕:

![The New OAuth App Screen](img/550c906a65c67dde04f94d6a00bced58.png)

插入您的应用程序名称，如果需要，选择一个图标，并为将来的重定向操作指定一个 URL 列表。

**注意:**你要插入的每个 URL 都必须使用 SSL ( `https://...`)。其他一切都将被忽略。

给出你的确定，你就完成了！您将看到一条确认消息，其中包含您的新客户端 ID 和客户端密码。然而，事情还没有结束:你可以从[的专用页面](https://coinbase.com/docs/api/authentication)中阅读关于认证的其他注释。

### 许可

如果你使用 API，为了更好的安全性，你必须处理权限。在这里你可以看到一个完整的列表:

*   全部:完全访问您的帐户；
*   商家:创建支付按钮，表格，访问商家的基本信息，编辑您的信息和创建新的地址；
*   余额:获取您的实际余额；
*   按钮:创建支付按钮；
*   买:买比特币；
*   联系人:获取您的联系人列表；
*   订单:获取您收到的订单列表；
*   卖:卖比特币；
*   交易:获得你的交易历史；
*   发送:从账户中发送一定数量的比特币；
*   请求:从你的账户中请求比特币；
*   转账:获取买卖历史；
*   recurring_payments:获取经常性付款的列表；
*   oauth_apps:查看、创建和编辑 oauth 应用程序；
*   报告:获取和创建新报告；

### 基本 SDK 使用

既然我们已经将我们的 SDK 包含在项目中，我们的访问设置好了，我们知道我们需要什么，是时候开始了。

首先，我们来看看认证过程的实现。

**通过 API 密钥和密码访问**

没什么复杂的，只是一个简单的指令。

```
$coinbase = Coinbase::withApiKey($coinbaseAPIKey, $coinbaseAPISecret);
```

只需使用 API 密钥和 API 秘密作为`withApiKey()`方法的参数。产生的`$coinbase`对象是我们将在未来的例子中使用的对象。

**通过 OAuth 访问**

在 OAuth 应用程序创建之后，接下来要做的事情是创建一个`$coinbaseOauth`对象，指定我们之前获得的客户端 ID 和客户端秘密。就像这个例子:

```
$coinbaseOauth = new Coinbase_OAuth($_CLIENT_ID, $_CLIENT_SECRET, $_REDIRECT_URL);
	header("Location: " . $coinbaseOauth->createAuthorizeUrl("all"));
```

在用户完成授权过程后，他将被重定向到之前在设置过程中指定的 URL。还将添加一个`code`参数来获得一个有效的令牌。

```
$tokens = $coinbaseOauth->getTokens($_GET['code']);
```

然后，最后一步将是使用我们现在拥有的令牌创建`$coinbase`对象。

```
$coinbase = Coinbase::withOauth($coinbaseOauth, $tokens);
```

你可以使用这个对象，就像你可以使用从 API 密钥认证中得到的那个对象一样，API 是相同的。

**访问您的数据**

访问您的数据非常容易。你要做的就是使用之前设置的`$coinbase`变量。

让我们看看如何检查您的余额:

```
echo $coinbase->getBalance() . " BTC";
```

在这里，您可以看到如何访问用户数据:

```
$user = $coinbase->getUser()

	echo $user->name;
	echo $user->email;
	echo $user->time_zone;
	echo $user->native_currency;
```

使用`$coinbase`对象，你也可以访问一些商家的财产。

```
$user->merchant->company_name;
	$user->merchant->logo;
```

**注意:**`logo`属性返回徽标的 URL。

另外，如果你愿意，你可以使用`getContacts()`获得你的联系人列表。

```
$response = $coinbase->getContacts("user");

	foreach($response->contacts as $contact)
	{
		echo $contact;
		// 'guyaddress@provider.com'
	}
```

**货币数据**

有了这个 PHP SDK，你还可以访问一些关于货币的数据。

```
$currencies = $coinbase->getCurrencies();
	echo $currencies[0]->name;
```

`getCurrencies()`方法返回系统中实际使用的所有货币的列表，以及它们的 ISO 代码。

以下是获取汇率信息的方法:

```
$rates = $coinbase->getExchangeRate();

	echo $rates->btc_to_usd;
	// is the same as...
	echo $coinbase->getExchangeRate('btc', 'usd');
```

`getExchangeRate()`既可以带参数使用，也可以不带参数使用(如您所见，有两种不同的结果)。

您还可以通过`getBuyPrice()`和`getSellPrice()`获得一些关于买卖价格的信息:

```
echo $coinbase->getBuyPrice('1');
	// '125.31'
	echo $coinbase->getSellPrice('1');
	// '122.41'
```

**注:**返回的价格包括 1%的比特币基地手续费和 0.15 美元的银行手续费。

**创建支付按钮**

支付按钮创建方法真的很有用，如果考虑实现难度和最终结果的话。您所要做的就是用一组特定的参数调用`createButton()`方法。

这是签名:

```
createButton($name, $price, $currency, $custom=null, $options=array())
```

…这里有一个例子。

```
$paymentButton = $coinbase->createButton(
		"Order #1", 
		"19.99", 
		"EUR", 
		"TRACKING_CODE_1", 
		array(
        	"description" => "1 item at 19.99"
    	)
    );
```

很简单，是吧？

第一个`$name`参数是您想要创建的付款的“标题”。下一个，`$price`，是你想要的支付金额。第三个是您想要使用的货币，`$custom`是一个特定的交易代码，在支付程序结束后会发回给您。

最后，`$options`数组将允许您以各种方式定制您的按钮:您将能够定制按钮外观或成功支付或失败支付后的返回 URL。如果你想了解更多，我建议你看看官方文件上的[专门页面。](https://coinbase.com/api/doc/1.0/buttons/create.html)

一旦你有了按钮，就很容易得到它的嵌入代码。

```
echo $response->button->code;
	// '93865b9cae83706ae59220c013bc0afd'

	echo $response->embedHtml;
	// '<div class=\"coinbase-button\" data-code=\"93865b9cae83706ae59220c013bc0afd\"></div><script src=\"https://coinbase.com/assets/button.js\" type=\"text/javascript\"></script>'
```

然后，你将无事可做，只能附和`$response->embedHtml`。

## 包扎

第 1 部分到此结束，在第 1 部分中，我们介绍了比特币基地 API SDK 的基本用法和安装。在第 2 部分中，我们将讨论发送和接收资金，以及构建我们的示例应用程序。敬请期待！

## 分享这篇文章