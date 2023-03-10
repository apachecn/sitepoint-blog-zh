# 用 WooCommerce 创建一个 WordPress 电子商务商店

> 原文：<https://www.sitepoint.com/wordpress-ecommerce-store-woocommerce/>

随着电子商务用户数量的不断增加，越来越多的电子商务网站涌现出来。有几个专门的电子商务平台，如 Shopify，为你打理一切，如虚拟主机和 SSL 证书。

电子商务商店也可以使用 WordPress。在这篇文章中，我们将学习如何在 WooCommerce 插件的帮助下在 WordPress 网站上销售实物商品。值得注意的是，WooCommerce 是最受欢迎的电子商务解决方案之一。

## 什么是 WooCommerce？

WooCommerce 是一个非常流行和著名的 WordPress 插件，可以让你在 WordPress 中建立一个电子商务商店。你可以用它在你现有的 WordPress 博客或网站上添加一个商店，或者你可以用 WooCommerce 建立一个独立的电子商务网站。

它完全免费使用，但有大量免费和付费扩展支持。当然，根据您的需求，您可以根据需要选择添加或删除扩展。然而，在很大程度上，WooCommerce 是一个健壮的解决方案，即使没有任何额外的扩展也能很好地运行。

WooCommerce 是由 WooThemes 的人发起并首先维护的。然而，当 WooThemes 被 Automattic(WordPress 的母公司)收购后，WooCommerce 也易手，目前处于 Automattic 的保护之下。这也意味着安全更新、错误修复以及与 WordPress 版本的兼容性根本不是最终用户需要担心的事情。

在销售实物商品方面，WooCommerce 已经成为 WordPress 支持的电子商务领域事实上的标准。你可以从现在许多 WordPress 主题卖家使用“WooCommerce friendly”作为营销标签来销售他们的主题这一事实来衡量它的受欢迎程度。事实上，许多 WordPress 主题都带有定制的 WooCommerce 商店和结帐页面模板，可以帮助你立刻建立一个更好看的电子商务商店！

在本文中，我们将完全专注于使用 WooCommerce 销售实物商品。(在下一篇文章的[中，我们将把注意力转向数字商品，然后了解另一个非常方便和受欢迎的 WordPress 电子商务插件，简易数字下载。)](https://www.sitepoint.com/wordpress-selling-digital-goods)

## WooCommerce 入门

一旦你已经安装好了 WordPress，你就可以开始安装 WooCommerce 了。考虑到 WooCommerce 是一个免费的 WordPress 插件，它可以在官方知识库中找到。只需在*添加插件*页面搜索“woocommerce”，安装即可。

下一步是激活 WooCommerce 插件。一旦它被激活，你会注意到 WooCommerce 在 WordPress 管理面板中添加了两个新的菜单项，以及几个子菜单。

![WooCommerce in the admin panel](img/efd6925819191f97ec29b272a60a9ada.png)

但是在去那里之前，如果这是你第一次使用 WooCommerce，你会被重定向到一个设置向导。如果您愿意，可以跳过向导。然而，对于第一次使用的用户来说，使用向导来设置在线商店是一个非常好的主意。你会被问及与你的业务相关的问题，比如它的位置、使用的货币、支付网关、运输条款和条件等。

![The WooCommerce wizard](img/9200a47c47f7fd7381f1a43a5cc525f2.png)

WooCommerce 附带 PayPal 标准支持，但你也可以选择配置你选择的任何其他支付网关。再次值得指出的是，由于 WooCommerce 的流行，许多支付解决方案提供商现在提供免费或高级插件，用于将支付网关集成到 WooCommerce 商店中。

设置向导还会要求您将页面设置为商店、我的帐户和结账页面。WooCommerce 可以自动为您创建这些页面，但是如果您愿意，您也可以选择其他页面来实现相同的目的。请注意，就像博客页面模板一样，这些页面也不应该有自己的内容。

## woo 商务定制

WooCommerce 在 WordPress 管理面板中添加的第一个菜单项是“WooCommerce”。您可以在这里添加折扣和促销优惠券，指定和调整支付网关，以及处理与您的 WooCommerce 商店相关的其他设置。例如，你可以自定义地理定位，激活或停用 WooCommerce 扩展的功能等。

自然，对于新用户来说，这可能会令人望而生畏。拥有如此多的设置和菜单项，WooCommerce 在 WordPress 插件世界中绝不是一条小鱼。但是考虑到经营一个电子商务商店本身需要更多的控制，WooCommerce 让你定制你的商店的几个方面是必要的。

![WooCommerce payment settings](img/6010f692176b151dff95c54c5d5d9c60.png)

设置页面是不言自明的。 *General* 选项卡提供特定于商店的选项，如邮政地址、货币详情等。另一方面，*产品*选项卡允许您定制选项，如测量单位、库存和存货等。还有一个处理可下载产品的选项，比如电子书等等。

![Product settings](img/cbe85e44daf33201d81c95e4a89ecd6f.png)

顾名思义，*运输*页面允许您设置运输规则、条件、退款政策等。*电子邮件*选项卡处理由 WooCommerce 发送给买家的电子邮件模板——关于成功购买、订单确认、订单取消等。您可以选择保持原样，也可以添加您的徽标和其他细节来进一步个性化电子邮件的外观。

一旦你调整和设置好一切，是时候开始把你的产品添加到 WooCommerce 了。

## 向 WooCommerce 添加产品

向你的 WooCommerce 商店添加新产品相当容易，就像你添加一篇新的博客文章一样。在 WooCommerce 中，产品被视为一种自己的帖子类型，拥有自己的属性和其他变量。

在 WordPress 管理面板的*产品*菜单中，你需要做的就是选择*添加新的*。就像博客文章一样，*产品*也有自己的类别、标签和特色图片。但是，主要考虑的是*产品数据*部分。

![Product data](img/a829ee80d7fcec84e932f3ceb7d5f9ac.png)

您可以选择将多个产品组合成一个包，或者将一个给定的产品指定为虚拟或可下载的产品，限制其库存，添加自定义的运输规则等等。

除此之外，您还可以在 WooCommerce 中为产品添加自定义属性。对于属性，我们是指同一产品类型的特殊子类型。例如，如果你在商店里卖 t 恤，你可以根据 t 恤的尺寸添加诸如小号、中号、大号、XL、XXL 等属性。类似地，对于其他几个产品，您可以添加颜色变化作为属性，等等。

![Product data attributes](img/b6e6f42430447d79be96a92110d88b95.png)

一旦完成，你所要做的就是发布产品——你就成功了！

## 利用 WooCommerce 做更多事情

至此，您已经学会了如何建立 WooCommerce。您现在已经熟悉了它的设置，如何调整它，以及如何在您的站点上添加待售产品。

但是还有什么呢？好吧，如果你经营一家正规的电子商务商店，你很有可能会希望前端看起来也很棒。然而，许多博客或其他 WordPress 主题可能没有自带的 WooCommerce 支持。

但是如上所述，有几个对 WooCommerce 友好的 WordPress 主题。在官方知识库中进行简单的搜索就可以得到数百个免费的 WordPress 主题，这些主题可以用来建立一个电子商务网站。如果你想用一些高级的东西，大多数主要的主题卖家和市场，比如 ThemeForest，都提供各种各样的 WooCommece WordPress 主题。

除此之外，如果其他都失败了，或者你真的不想在主题选择上出错，你可以选择[店面](https://wordpress.org/themes/storefront/)。这个特殊的主题是由 WooCommerce 核心贡献者自己设计和开发的，作为新店主的起点。它装载了运行一个合适的电子商务商店可能需要的所有功能。

![Storefront home page](img/1d34b37ef81f8c211a1601d4206ff8ee.png)

从任何标准来看，Storefront 都是一个体面的、有价值的 WooCommerce 主题，如果你正在寻找一个高质量的免费 WordPress 主题，你应该考虑尝试一下。它提供了多种布局和颜色选择，还可以与许多流行的 WooCommerce 扩展无缝集成。

## 扩展网络商务

WooCommerce 发展得如此之大，以至于它有自己的扩展生态系统。你可以认为 WooCommerce 扩展类似于 WordPress 插件。就像 WordPress 插件被用来扩展 WordPress 的特性和功能一样，WooCommerce 扩展被用来扩展 WooCommerce 本身的特性和功能！

事实上，WooCommerce 扩展只不过是 WordPress 插件(是的，你可以像安装和删除其他 WordPress 插件一样安装和删除它们)，但它们需要 WooCommerce 才能工作。此外，就像 WordPress 插件一样，也有一些糟糕的编码和低于标准的 WooCommerce 扩展会真正危及你网站的安全。因此，建议只选择来自高度可靠或值得信赖的来源的 WooCommerce 扩展。

寻找高级 WooCommerce 扩展的一个好地方是官方的 WooCommerce 网站,它提供了几个令人惊叹的功能丰富的 WooCommerce 扩展。然而，如果你在寻找更划算的东西，像 Creative Market 或 ThemeForest 这样的市场是一个很好的起点！

![The WooCommerce Extensions page](img/4631621b6c02b70a0754d8f1d7d35267.png)

最后，对于免费扩展，WordPress 插件库本身是你需要寻找的唯一来源。只需搜索“woo commerce”，你就会看到各种扩展。

![WooCommerce extensions](img/82bd1c9f700cef18fa828367497c632a.png)

您还可以通过搜索支付网关、运输成本计算器等功能来缩小列表。在一天结束时，如果有一个给定的功能是运行一个电子商务商店所需要的，而 WooCommerce 没有提供现成的，那么就必须有一个扩展！这就是 WooCommerce 的受欢迎程度:由于它的高使用率，对扩展的需求也增加了。

### 结论

在这篇文章中，我们了解了 WooCommerce 以及如何使用它在网上销售我们的商品和产品。对于大多数用户来说，WooCommerce 似乎是一个非常方便和理想的选择。

然而，如果你打算出售数字商品，比如可下载的电子书、代码或艺术文件(比如主题、插件或字体)，WooCommerce 仍然可以完成这项工作。但是拥有一个专门为商店定制的插件不是更好吗？是的，确实存在一个这样的插件。在本系列的下一篇文章中，我们将学习简易数字下载，一个这样的 WordPress 插件，可以用来建立一个网上商店，轻松销售可下载的产品。

## 分享这篇文章