# 快速提示:解决贝宝 IPN 总是返回“无效”

> 原文：<https://www.sitepoint.com/quick-tip-solution-to-paypal-ipn-always-returning-invalid/>

在使用 PayPal 的 IPN 模拟器开发时，你可能会遇到这样的情况:不管你设置的或[的](https://developer.paypal.com/docs/classic/ipn/integration-guide/IPNSimulator/)[编码是否匹配，所有条件是否有效](http://stackoverflow.com/a/23593082/504357)，验证消息时[总是返回“无效”](https://www.google.com/search?client=opera&q=paypal+ipn+simulator+invalid&sourceid=opera&ie=UTF-8&oe=UTF-8)。

Paypal 开发团队因忽略所有查询而臭名昭著，文档也很难阅读，因此调试这些问题非常困难，可能会耗费您数小时的计费时间。我甚至为测试 IPN 模拟器设置了一个现场服务器，因为担心本地测试时 [ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/) 会出错，甚至给端点添加了一个证书来让 HTTPS 运行——不行。最终，解决方案——通常如此——简单却晦涩难懂。

如果日期字段包含时区标识符，则症状(故障)是由日期字段引起的。然而，所有这些都是由于 PHP 有两种不同的 URL 编码/解码功能:raw 和非 raw。

这里有一个例子。

假设我们在 IPN 模拟器中有这样的约会:

```
Fri Aug 19 2016 09:25:00 GMT+0100 (GMT Daylight Time) 
```

这到达侦听器的末端(在您的 PHP 代码中),如下所示:

```
Fri%20Aug%2019%202016%2009%3A25%3A00%20GMT+0100%20%28GMT%20Daylight%20Time%29 
```

子字符串`GMT+0100`是有问题的，因为 PHP 函数`urldecode`将`+`解释为空格，所以它被解码为:

```
Fri Aug 19 2016 09:25:00 GMT 0100 (GMT Daylight Time) 
```

注意`+`丢失了，变成了一个空格字符。

当它被重新编码发送回 Paypal 进行验证时，验证会失败，因为它不再是字段中的相同值——缺少了`+`。这是一个非常非常小的细节，手动检查字段值时很难发现，但它确实存在。根据 Paypal 文档，这足以使验证返回“无效”。

这个问题有两种解决方案:

1.  使用`rawurlencode`和`rawurldecode`代替它们的非原始对应物。这些代码也对`+`符号进行编码，而不是把它变成一个空格字符，然后一切正常。
2.  使用贝宝 IPN 监听器客户端有这个内置的。我最近给这个提交了一个补丁，效果非常好。

希望这个小提示能让一些人从令人沮丧的谷歌搜索中解脱出来！

## 分享这篇文章