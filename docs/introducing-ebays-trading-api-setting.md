# 介绍易贝的交易 API–设置

> 原文：<https://www.sitepoint.com/introducing-ebays-trading-api-setting/>

在这个系列教程中，我将带你浏览 Ebay 的交易 API。Trading API 允许您构建可用于在 Ebay 中销售的应用程序。以下是您可以使用 API 做的一些事情的示例:

*   检索商店信息
*   更新商店偏好设置
*   将产品添加到特定的易贝商店
*   最终产品列表
*   更新产品价格
*   检索产品信息

在本教程中，我们将创建一个应用程序，允许用户通过使用 API 在易贝上创建产品。

## 注册应用程序

你需要做的第一件事是在[易贝开发者网站](https://developer.ebay.com/join/)注册一个开发者账户。

这样做，一旦您验证了您的电子邮件，您将看到一个类似如下的屏幕:

![sucess](img/a02be185f82053afe2be23ca3e7c7ad5.png)

在该页面上，单击“获取您的应用程序密钥”链接，这将引导您进入可以生成沙盒和生产密钥的页面。然后，单击“生成沙盒密钥”按钮，生成可用于测试我们将要构建的应用程序的密钥。另外，单击“生成生产密钥”按钮，生成您在部署应用程序时可以使用的密钥。请注意，这些键可以与易贝提供的任何 API 一起使用，因此您只需这样做一次。

生成密钥后，单击任意密钥(沙盒或生产)上的“配置设置”链接。

在“自定义易贝用户同意书”部分，单击“自定义易贝用户同意书”链接。这将允许您配置应用程序使用的 URL。这基本上是应用程序主页的 URL。进入后，在“管理您的运行名”部分，单击“为您的应用程序生成运行名”链接。RuName 相当于一个应用程序。如果您正在创建将与不同易贝 API 交互的不同应用程序，您可以为每个应用程序创建一个 RuName。生成 RuName 后，将要求您提供以下信息:

*   **显示标题**–当用户稍后授权给你的应用时，你想要显示的应用标题。(如产品创建者)
*   **显示描述**:显示描述是描述你的应用程序做什么的文本。(例如，这个应用程序允许用户轻松地将产品添加到他们的易贝商店)
*   **令牌返还方法**–令牌返还方法是用户授权给你的 app 后，app 将使用的方法。为此，提供的值应该是“FetchToken”。
*   **授权类型**——用户授权给你的应用后，你的应用所做的动作类型。这可以是授权或 ID.verification。为该字段提供“授权”值，因为我们需要用户授权我们的应用程序为他们执行不同的操作。
*   **应用类型**–应用的类型。这应该是“基于网络”。
*   **接受重定向 URL**–当用户授予应用程序权限时，用户被重定向到的 URL。注意，易贝只允许 https URLs。如果你稍后想跟进，我推荐你在 openshift.com[注册一个账户。默认情况下，你托管的应用程序会有 https，而且是免费的。](https://www.openshift.com/)
*   **拒绝重定向 URL** 用户拒绝应用程序时被重定向到的 URL。
*   **隐私政策 URL**–隐私政策 URL。当用户在向应用程序授权的页面中时，该 URL 将包含在应用程序描述中。

## 工具

EBay 的开发者仪表板为我们提供了各种各样的工具。

### 沙盒帐户

让我们看看沙盒用户注册工具。注册沙盒账号后，进入[sandbox.ebay.com](http://sandbox.ebay.com/)测试登录。一旦你验证了它的工作，回到你的易贝[开发者账户页面](https://developer.ebay.com/DevZone/account/Default.aspx)。

### API 测试工具

接下来是 API 测试工具。在该页面上，您可以看到有一堆选项，但它们已经被默认预填充，因此您将对每个字段的作用有所了解。在我们开始使用这个工具之前，这里有一个对每个字段的简要描述:

*   **选择环境**–这允许您在沙盒和生产环境之间切换。很多时候，当你只是从 API 测试工具进行测试时，你会想要使用“沙箱”来进行测试。两者的主要区别在于，沙盒只能与沙盒/测试易贝帐户一起使用，而生产只能与实际的易贝帐户一起使用。
*   **选择一个按键组**–这允许您更改将用于进行 API 调用的按键。大多数时候你并不真的需要去接触它，因为当你选择环境的类型时，键已经改变了。
*   **选择一个 API**–这允许您选择您想要调用的 API。你可能已经知道，易贝有不同的 API 集:交易、寻找、购物、反馈和开放 API。在本教程中，我们主要使用交易 API，所以继续选择它。请注意，在选择交易 API 之后，会显示另一组字段。它会提示您生成用户令牌或手动输入现有令牌。由于我们之前已经创建了一个沙盒帐户，我们只需单击“生成用户令牌”按钮。这会将您重定向到如下所示的页面:

![generate token](img/4329432f12f1ab9e4af20a1583e15862.png)

确认为环境选择了沙盒，然后单击“继续生成令牌”按钮。这将重定向到沙盒易贝网站登录页面。用您的沙盒登录凭证填写它。如果登录成功，它会将您重定向到授权页面:

![agree](img/e08b764f8500aa0440fe6d4d297828ff.png)

同意授权该应用程序。请注意，这是易贝主要用于测试的通用应用程序。这不是我们之前创建的应用程序。之后，您将被重定向到以下页面:

![token generation](img/a1ffeaae20d515fee10a0308fa754c8c.png)

在此页面上，您只需复制生成的令牌，然后单击“保存令牌”按钮。完成后，您可以返回到 API 测试工具页面，将您刚刚复制的用户令牌粘贴到 user token 字段中。

*   **选择一个 API 调用**–您想要请求的 API 中的实际资源。你可以打很多电话。你可以选择任何你感兴趣的东西。

*   **选择一个模板**–该模板的值可以是“完整、通用”或“部分、预填充”。选择“完整、通用”将在 XML 请求框中为您提供空白字段，而选择“部分、预填充”将为您提供一些已经填充的基本示例字段。因为我们才刚刚开始，所以使用“部分预填充”是最好的选择，因为这已经为我们提供了我们所选择的资源所需的一些字段。例如，如果我们为 API 调用字段选择“GetItem ”,这已经为我们提供了一个示例 ItemID 和 WarningLevel，我们所要做的就是单击 XML 请求文本区域下面的“submit”按钮来发起请求。一旦您对使用 API 感到满意，就可以直接将值添加到 XML 请求文本区域，以修改提交给 API 的实际数据。

*   **选择一个调用变体**–API 中的每个资源都可以有不同的变体，但是大多数时候您只想进行一个基本的调用，因此您不必真的修改该字段的默认值。

*   **选择呼叫请求版本**–易贝 API 经历了许多变化，这意味着仍有不同版本的 API 在流动，以支持旧系统。因为我们刚刚开始使用这个 API，所以我们可以选择最新的版本，目前是 885 版本。

*   **选择站点 ID**–您可能已经知道易贝有不同的网站。此字段表示您要拨打电话的实际易贝网站。因为我们只是使用 API 测试工具进行测试，所以我们可以为此提供美国。

*   **Web 服务 URI**–所有请求都发送到的 URL。您不必修改该字段。

*   **HTTP Headers**–针对您向 API 发出的每个请求，在报头中提供的信息。默认情况下使用以下标题:

```
X-EBAY-API-COMPATIBILITY-LEVEL:your-request-version
X-EBAY-API-DEV-NAME:your-dev-id
X-EBAY-API-APP-NAME:your-app-id
X-EBAY-API-CERT-NAME:your-cert-id
X-EBAY-API-SITEID:your-site-id
X-EBAY-API-CALL-NAME:YourAPICallMethod
```

解释不清楚的地方:

*   **X-EBAY-API-SITEID**–你要向其发出请求的易贝网站的 ID。这里有一个[网站 ID 列表，你可以使用](http://developer.ebay.com/DevZone/finding/CallRef/Enums/GlobalIdList.html)。您可能希望找到站点名称，然后记下相应的易贝站点 ID。
*   **X-EBAY-API-CALL-NAME**–您想要调用的 API 中的资源或方法。这里有一个[引用，您可以使用交易 API](http://developer.ebay.com/Devzone/xml/docs/Reference/ebay/index.html) 进行调用。
*   **XML 请求**–请求的主体。交易 API 只支持 XML 和 SOAP 格式。但是 XML 看起来更简洁，所以我们将使用 XML。它也是 API 测试工具中使用的默认格式，所以它确实是正确的。下面是一个示例 XML 请求:

```
<?xml version="1.0" encoding="utf-8"?>
<GetItemRequest xmlns="urn:ebay:apis:eBLBaseComponents">
<ItemID>110072510158</ItemID>
<RequesterCredentials>
<eBayAuthToken>your-user-token</eBayAuthToken>
</RequesterCredentials>
<WarningLevel>High</WarningLevel>
</GetItemRequest>
```

分解一下，它指定了我们使用的 xml 版本和编码:

```
<?xml version="1.0" encoding="utf-8"?>
```

这是我们请求的包装。它使用了`GetItem`请求方法。注意,`GetItem`是 API 中方法的名称，但是我们必须给它加上`Request`。`xmlns`用于指定调用所属的名称空间。大多数情况下，这个值是`urn:ebay:apis:eBLBaseComponents`。我说的大多数时候是指交易 API 中的大多数方法都使用这个特定的名称空间。

```
<GetItemRequest xmlns="urn:ebay:apis:eBLBaseComponents">
</GetItemRequest>
```

接下来是易趣上的项目 ID。

```
<ItemID>110072510158</ItemID>
```

要查找此信息，请在易贝搜索一个项目，然后单击返回的任何结果。该 URL 将类似于以下内容:

```
http://www.ebay.co.uk/itm/Apple-MacBook-Pro-15-4-2-66Ghz-Dual-Core-8GB-RAM-250GB-Latest-OS-X-10-9-/301305989202?pt=UK_Computing_Apple_Laptops_ET&hash=item46273c8852
```

项目 id 是紧接在项目标题后面的一组数字。
接下来是请求者凭证的包装:

```
<RequesterCredentials>
</RequesterCredentials>
```

请求者凭证包装应该包含用户令牌。我们已经看到了如何为沙盒帐户获取用户令牌。获取应用程序的用户令牌基本上是相同的，但我们必须将令牌保存在数据库中，以便我们可以在以后使用它。

```
<eBayAuthToken>your-user-token</eBayAuthToken>
```

接下来是预警级别。这可以具有高或低的值。这应该总是设置为高，告诉易贝返回未识别的，不赞成使用的元素或无效值的警告。这将允许您在请求出现问题时检查响应。

```
<WarningLevel>High</WarningLevel>
```

**调用响应**–API 返回的实际响应。没有必要修改它，因为它只是用于显示响应。

### API 调用使用报告

我们要使用的最后一个工具是 API 调用使用报告。这允许您生成特定时间范围内 API 使用情况的报告。如果您稍后部署应用程序，这是一个很好的工具，因为这将允许您确定您是否超出了您的应用程序所使用的特定 API 的 [API 调用限制](http://go.developer.ebay.com/developers/ebay/ebay-api-call-limits)。在这种情况下，你可以向易贝提交一份申请来增加你的 API 限制。

## 数据库ˌ资料库

我们将为这个应用程序使用 MySQL。将使用以下表格:

*   **设置**–存储通用应用程序设置，如用户令牌和应用程序 id。
*   **store _ settings**–存储创建产品时使用的易贝商店设置。
*   **产品**–存储产品详情。
*   **condition _ types**–存储[项目条件 id](http://developer.ebay.com/Devzone/finding/CallRef/Enums/conditionIdList.html)及其对应的名称。
*   **清单 _ 类型**–存储[清单代码](http://developer.ebay.com/devzone/xml/docs/reference/ebay/types/ListingTypeCodeType.html)。
*   **列表持续时间**–存储[列表持续时间代码](http://developer.ebay.com/devzone/xml/docs/reference/ebay/types/ListingDurationCodeType.html)。

使用 phpmyadmin 之类的工具创建一个新的数据库，然后执行以下 SQL 语句来创建各个表:

**设置**

```
CREATE TABLE IF NOT EXISTS `settings` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_token` text NOT NULL,
  `dev_id` varchar(300) NOT NULL,
  `app_id` varchar(300) NOT NULL,
  `cert_id` varchar(300) NOT NULL,
  `run_name` varchar(300) NOT NULL,
  `site_id` varchar(60) NOT NULL,
  PRIMARY KEY (`id`)
)
```

**商店 _ 设置**

```
CREATE TABLE IF NOT EXISTS `store_settings` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `store_name` varchar(160) NOT NULL,
  `county` varchar(160) NOT NULL,
  `street` varchar(160) NOT NULL,
  `country_code_type` varchar(60) NOT NULL,
  `ebay_website` varchar(160) NOT NULL,
  `postal_code` varchar(60) NOT NULL,
  `category_mapping` tinyint(1) NOT NULL,
  `category_prefill` tinyint(1) NOT NULL,
  `currency_code` varchar(60) NOT NULL,
  `item_location` varchar(300) NOT NULL,
  `dispatch_time` int(11) NOT NULL,
  `listing_duration` varchar(60) NOT NULL,
  `listing_type` varchar(60) NOT NULL,
  `condition_type` varchar(60) NOT NULL,
  `optimal_picturesize` tinyint(1) NOT NULL,
  `out_of_stock_control` tinyint(1) NOT NULL,
  `get_it_fast` tinyint(1) NOT NULL,
  `include_prefilled` tinyint(1) NOT NULL,
  `shipping_profile` varchar(160) NOT NULL,
  `return_profile` varchar(160) NOT NULL,
  `payment_profile` varchar(160) NOT NULL,
  `shipping_service` varchar(160) NOT NULL,
  `shippingservice_priority` int(11) NOT NULL,
  `shippingservice_cost` double NOT NULL,
  `shippingservice_additionalcost` double NOT NULL,
  PRIMARY KEY (`id`)
)
```

**产品**

```
CREATE TABLE IF NOT EXISTS `products` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(80) NOT NULL,
  `category_id` varchar(90) NOT NULL,
  `price` double NOT NULL,
  `qty` int(11) NOT NULL,
  `brand` varchar(160) NOT NULL,
  `description` text NOT NULL,
  PRIMARY KEY (`id`)
)
```

**条件 _ 类型**

```
CREATE TABLE IF NOT EXISTS `condition_types` (
  `id` int(11) NOT NULL,
  `name` varchar(160) NOT NULL,
  PRIMARY KEY (`id`)
)
```

**清单 _ 类型**

```
CREATE TABLE IF NOT EXISTS `listing_types` (
  `name` varchar(160) NOT NULL
)
```

**列表 _ 持续时间**

```
CREATE TABLE IF NOT EXISTS `listing_durations` (
  `name` varchar(60) NOT NULL
)
```

对于条件类型、列表类型和列表持续时间，我们可以直接插入默认数据。为此，还需要执行以下查询。

```
INSERT INTO `listing_durations` (`name`) VALUES
('Days_1'),
('Days_10'),
('Days_120'),
('Days_14'),
('Days_21'),
('Days_3'),
('Days_30'),
('Days_5'),
('Days_60'),
('Days_7'),
('Days_90'),
('GTC');
```

```
INSERT INTO `listing_types` (`name`) VALUES
('AdType'),
('Auction'),
('Chinese'),
('FixedPriceItem'),
('Half'),
('LeadGeneration'),
('PersonalOffer'),
('Shopping'),
('Unknown');
```

```
INSERT INTO `condition_types` (`id`, `name`) VALUES
(1000, 'New'),
(1500, 'New Other'),
(1750, 'New with defects'),
(2000, 'Manufacturer refurbished'),
(2500, 'Seller refurbished'),
(3000, 'Used'),
(4000, 'Very Good'),
(5000, 'Good'),
(6000, 'Acceptable'),
(7000, 'For parts or not working');
```

## 结论

在这一部分中，我们准备了环境，并为第 2 部分和第 3 部分中的平稳开发工作流做好了一切准备。请继续关注后续，我们将开始构建我们的应用程序的逻辑！

## 分享这篇文章