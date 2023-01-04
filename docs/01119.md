# 如何使用谷歌分析跟踪电子商务交易

> 原文：<https://www.sitepoint.com/track-ecommerce-transactions-google-analytics-reports/>

Google Analytics 是一个非常有用的跟踪网站访客的工具，但是很少有开发者会添加由 JavaScript 片段组成的标准跟踪代码。

除了自定义的*事件*(如果你想知道什么是事件，请参见上面的链接)，我们还可以使用一些额外的代码在 Google Analytics 中跟踪电子商务交易。

## 为什么要在谷歌分析中跟踪电子商务交易？

大多数电子商务系统提供详细的交易统计数据，但你不一定能将它们与会话、设备、人口统计或其他重要指标联系起来。例如，销售额的增长是由 UX 的改进、成功的营销活动还是季节变化引起的？谁知道呢。

在 Google Analytics 中记录交易意味着:

*   产品和用户报告更丰富、更有洞察力
*   您可以将交易直接关联到 AdWords 和其他活动系统
*   你可以评估活动的成功，并专注于那些带来高投资回报的活动
*   您可以授予贵公司员工访问这些报告的权限，而无需将它们添加到电子商务系统中。

让我们开始吧。

## 整体概念

通常，您的用户会将商品添加到购物车，结帐，可能会注册，支付一定金额，然后被重定向到确认屏幕。一旦用户正式结账，交易细节就会显示在谷歌分析中。

电子商务交易数据通常存储在您的后端系统或支付网关的后端系统中。为了在 Google Analytics 中显示这些信息，我们需要更新我们的 JavaScript 代码，这样 Google Analytics 也可以跟踪这些数据。

## 如何启用电子商务跟踪

进入左侧的*管理*区域(cog 图标)，选择您的*酒店*(您的网站)，然后选择*电子商务设置*。将上的【状态】切换到*。*

*注意:相关产品功能[已于 2017 年 4 月](https://developers.google.com/analytics/devguides/reporting/core/v4/changelog)弃用，因此请完全避免。*

*增强的电子商务设置*允许您记录产品印象、促销和优惠券使用等行为。我建议您首先实施标准的电子商务跟踪，然后当您想继续进行更高级的电子商务跟踪时，请查阅[增强型电子商务文档](https://developers.google.com/analytics/devguides/collection/analyticsjs/enhanced-ecommerce)。

最后，打开*视图设置*菜单，如有必要，更改显示为值的*货币。*

## 启用电子商务交易

接下来，使用这个代码片段来启用电子商务交易:

```
ga('require', 'ecommerce'); 
```

*注意:这必须在页面跟踪片段之后和使用任何电子商务功能之前运行。*

## 开始新的交易

接下来，使用以下 JavaScript 代码启动事务:

```
ga('ecommerce:addTransaction', {
  'id': '[transactionId]',
  'affiliation': '[storeName]',
  'revenue': '[totalCost]',
  'shipping': '[shippingCost]',
  'tax': '[taxCost]',
  'currency': '[currency]'
}); 
```

让我们分别看看这个对象中的每一条线…

### [交易 Id](必填)

交易的唯一标识符，如`'ABC-123'`。

### [商店名称](可选)

加盟店或店铺名称，如`'My Online Shop'`。

### [totalCost] (optional)

总成本包括运费和税金。必须使用不带货币符号的字符串编码数字，例如`'12.99'`。

### [运费](可选)

运输的(数字)成本，例如`'2.99'`。

### [taxCost](可选)

(数字)税，例如`'1.64'`。

### [货币](可选)

我们之前已经设置了默认货币，但是如果购物者选择使用不同的货币支付，您也可以指定一个由 [3 个字符组成的 ISO 4217 国家代码](https://support.google.com/analytics/answer/6205902#supported-currencies)，例如 *EUR* (欧元)或 *GBP* (英镑)。

### 向交易中添加项目

使用以下代码片段将项目添加到现有事务中:

```
ga('ecommerce:addItem', {
  'id': '[transactionId]',
  'name': '[productName]',
  'sku': '[productCode]',
  'category': '[categoryName]',
  'price': '[price]',
  'quantity': '[quantity]'
}); 
```

同样，让我们单独看看这个对象中的每一行…

### [交易 Id](必填)

交易的唯一标识符，如`'ABC-123'`。这必须与上述交易中使用的代码相匹配，以澄清该项目与用户已经拥有的购物篮相关。

### [productName](必需)

产品名称，如`'mens blue shirt'`。

### [productCode] (optional)

唯一的产品代码或库存单位(SKU)，如`'MBS-00001'`。

### [categoryName] (optional)

类别或变体，例如`'mens shirts'`。

### [价格](可选)

该项目的数字价格，例如`'9.99'`。

### [数量](可选)

购买的数量，例如`'2'`。

## 结算交易中的项目

如果用户清空购物车，下面的 JavaScript 命令将清除交易中的所有商品:

```
ga('ecommerce:clear'); 
```

## 将交易提交给 Google Analytics

然后，必须使用以下 JavaScript 命令提交包含所有项目的最终交易:

```
ga('ecommerce:send'); 
```

很自然，这将发生在确认屏幕上。

## 最终代码

因此，最终确认屏幕中的 HTML 代码可能如下所示:

```
<!DOCTYPE html>
<html>
<head>
<title>Thank you for your order</title>
</head>
<body>

<h1>Thank you for your order</h1>
<p>A receipt has been sent to you@youremail.com</p>

<!-- standard GA code -->
<script> window.ga=window.ga||function(){(ga.q=ga.q||[]).push(arguments)};ga.l=+new Date;
ga('create', 'UA-XXXXX-Y', 'auto');
ga('send', 'pageview'); </script>
<script async src='https://www.google-analytics.com/analytics.js'></script>

<script> // enable the ecommerce features
ga('require', 'ecommerce');

// start the transaction
ga('ecommerce:addTransaction', {
  'id': '10001',
  'affiliation': 'My Cool Store',
  'revenue': '54.97',
  'shipping': '5.00',
  'tax': '0.00'
});

// add item 1
ga('ecommerce:addItem', {
  'id': '10001',
  'name': 'Learn HTML',
  'sku': 'BOOK00001',
  'category': 'book',
  'price': '14.99',
  'quantity': '1'
});

// add item 2
ga('ecommerce:addItem', {
  'id': '10001',
  'name': 'Learn CSS',
  'sku': 'BOOK00002',
  'category': 'book',
  'price': '14.99',
  'quantity': '1'
});

// add item 3
ga('ecommerce:addItem', {
  'id': '10001',
  'name': 'Learn JavaScript',
  'sku': 'BOOK00003',
  'category': 'book',
  'price': '19.99',
  'quantity': '1'
});

// send transaction
ga('ecommerce:send'); </script>
</body>
</html> 
```

几个小时内，你就可以在谷歌分析的*转换→电子商务*部分查看交易统计数据(以及其他报告)。

有关更多信息，请参考[谷歌分析文档](https://developers.google.com/analytics/devguides/collection/analyticsjs/):

*   [电子商务跟踪](https://developers.google.com/analytics/devguides/collection/analyticsjs/ecommerce)
*   [增强型电子商务](https://developers.google.com/analytics/devguides/collection/analyticsjs/enhanced-ecommerce)

## 分享这篇文章