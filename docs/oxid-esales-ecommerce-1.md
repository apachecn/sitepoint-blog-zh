# ox id eSales——运转良好的电子商务平台！第一部分

> 原文：<https://www.sitepoint.com/oxid-esales-ecommerce-1/>

电子商务平台有各种不同的形状、大小和功能集。有些人想摘星星，有些人则有一个小的，正如我的美国朋友所说的，妈妈和爸爸的方法。每一种都有自己的优点和缺点，可以满足不同的需求。

也许你经营着一家自己的电子商务商店，或者你在一家专门帮助企业主建立自己的电子商务业务的公司工作。不管你在光谱中处于什么位置，有一个问题是不可避免的:

> 什么是可靠的、功能丰富的、可根据我的需求轻松定制的电子商务平台？

有大量的电子商务平台可用。从 WordPress 的 [WP 电子商务](http://getshopped.org/ "WordPress Ecommerce WordPress Shopping Cart Plugin")、 [Shopify](http://www.shopify.com/ "Ecommerce Software, Online Store Builder, Website Store Hosting") 、 [Highwire](http://www.highwire.com/ "Create Your Own Online Store, Sell on eBay and Sell on Facebook") 、 [BigCommerce](http://www.bigcommerce.com/ "Shopping Cart Software & Ecommerce Software Solutions") 和 [FoxyCart](http://www.foxycart.com/ "FoxyCart") ，到最著名的 Magento。

我对上述任何选择都没有恶意。但是，在使用了其中的一些之后，特别是 Magento，我个人发现使用它们来实现我所需要的功能有点复杂。如果你曾经使用过 [Magento EAV 数据模型](http://www.magentocommerce.com/wiki/2_-_magento_concepts_and_architecture/magento_database_diagram#the_magento_eav_data_model "Magento - Wiki - Magento Database")，你就知道*确切地说*我在说什么。

在这个由 3 部分组成的系列中，我将向您介绍 OXID 一个英语世界不太了解的平台。我之所以说*英语世界，是因为该产品位于德国弗赖堡，因此大部分可用的在线文档都是德语的。* 
OXID 被许多知名厂商使用，包括[奔驰](http://mercedes-benz.com/en/ "Mercedes-Benz International: Sports Fashion TV Design Vehicles History")、[蔡司](http://corporate.zeiss.com/country-page/en_de/home.html "Welcome to Carl Zeiss, Germany")、[卡雷拉](http://www.carrera-toys.com/en/ "Carrera Slot Racing - Race Tracks, Sets & Cars")、[辛多博](http://www.sindopower.com/en/ "SEMIKRON Online Shop | SindoPower | Buy Power Modules Online")。可以说，从这份清单来看，能够建造的商店规模足以满足几乎任何规模的企业。

声明:我确实为 [OXID 认证合作伙伴](http://www.shoptimax.de/ "Shoptimax GmbH")工作。所以我不会骗你。我确实对这个产品有点偏见。但我不是因为这个原因才把它堵上的。我之所以推荐它，是因为尽管它有些古怪，但它是一个简单而有效的平台，不碍事，能让你着手建立和定制你的电子商务商店。

这是这个系列的工作方式:

*   在第 1 部分中，我们将了解如何启动并运行基本的 OXID 安装。
*   在第 2 部分中，我们将介绍定制现有安装的外观和感觉的过程，以及一些关键的后端选项。
*   最后，在第 3 部分中，我们将介绍如何通过编写一个自定义模块来真正增加您的安装的趣味，允许我们显示我们的 Twitter 帐户的最新 tweets。

就这样，让我们开始吧。

## 功能和要求

OXID 有三个版本:社区版、专业版和企业版。出于本系列的目的，我们将关注免费社区版。从根本上说，这三个版本没有太大的区别。然而，下表列出了主要特性，如[官方文档](http://www.oxid-esales.com/en/products/facts/oxid-eshop-community-edition/product-information.html "OXID eSales | Product Information | OXID eSales Community Edition | Facts | Products")中所述。

![oxide1-01](img/006af404d7cfa927d9dd3ff0d6b65f21.png)

商店的要求出奇的低，相当的低。

*   PHP 5.2.10 或更高版本(加上扩展:glib、bcmath、json、php-xml、libxml2、icon tokenizer、mbstring)
*   MySQL 5.0.33 或更高版本
*   Apache 1.3 或更高版本

这有点被掩盖了。像任何好的商店一样，有许多方面需要考虑，比如缓存、代理、etags、gzip 压缩等等。但是对店铺的基本要求还是比较轻量的。

我在本系列中使用的安装将使用我的标准开发环境。这是基于 VirtualBox 虚拟机，带有 Ubuntu Linux 客户操作系统。

它只配置了 512mb 的内存，以及 MySQL 5.1.62、Apache 2 和 PHP 5.4。如果你有一个不太强大的配置，你仍然可以做得很好。

其他的 web 服务器，比如 NginX，Lighttpd 或者微软 IIS，我不敢说。因此，如果你使用其中一种，你的里程数可能会有所不同。然而，如果你能自如地管理它们，请随意使用它们；请在评论中告诉我你的进展。

## 设置过程

好，先[下载一份 OXID 的社区版](http://www.oxid-esales.com/de/community/oxid-eshop-herunterladen.html "OXID eSales | OXID eShop herunterladen | Community")。如果你愿意，你可以填写登记表，或者直接去[下载](http://www.oxid-esales.com/de/community/oxid-eshop-herunterladen/ce-download.html "OXID eSales | CE Download | OXID eShop herunterladen | Community")。下载完成后，从归档文件中提取内容，并将它们放入 Apache 文档根目录中，以便为商店提供服务。

你首先看到的是设置/安装屏幕。然而，我们还没有为我们的安装设置数据库。因此，如果您对设置它感到满意，那么就和具有适当权限的用户一起创建一个新的数据库。下面的指令将做到这一点。

```
CREATE DATABASE oxidtestshop;
GRANT ALL ON oxidtestshop.* TO 'oxidtestshopuser'@'localhost' IDENTIFIED BY '<your_password>';
```

![oxide1-02](img/023384e7b73a5edc413d69b3aede8346.png)

完成后，返回浏览器并逐步完成配置选项。您将看到的第一个屏幕是 pre-check 屏幕，它检查 PHP 至少是最低要求的版本，并且已经加载了所有要求的配置选项和扩展。

您将看到，从红色到绿色，您的设置的严重程度如何。如果您有任何红色项目，请配置或安装它们。如果你有黄色等级的项目，考虑你是否想解决它们。

如果一开始你看到的都是德语，你会在 *Sprache für Installation* 旁边看到一个选择框。点击它并选择英语。安装程序将重新加载为英文版，您可以继续安装。(如果你的德语足够好，那么继续安装。)

### 核心配置

![oxide1-03](img/cb5265047c741d70c26676a2a6d50bf1.png)

在向导的第二步中，有几个选项可供选择:

*   你的市场
*   主要交货国家
*   商店用语
*   定期检查更新

我已经将市场设置为“任何其他”，主要交付国家为澳大利亚(我是澳大利亚人，完全有偏见)，商店语言为英语，并选择定期更新。

这一步的作用是确保商店在目标市场充分本地化，适当扩展本地化选项。

### 商店执照

![oxide1-04](img/14550d7011b19334c8de8fa3a11575e1.png)

选择这些选项后，单击底部的“开始安装”按钮，您将进入“许可条件”页面。这表明商店中的代码受 GPL 保护。如果您觉得合适，请单击“我接受许可条件”，然后单击“继续”。

### 数据库配置

![oxide1-05](img/60db174b152fcffe8b031fbdd02bd17f.png)

现在我们处于数据库配置阶段，所以输入我们之前指定的数据库名称、用户名和密码。我假设您的数据库和 web 服务器在同一个机器上，所以将 hostname 设置为 localhost。最后两个选项值得注意。点击“安装演示数据”和“UTF-8 字符编码”。

演示数据将有助于商店日后变得更有意义。UTF-8 编码将使商店对你想使用的任何语言都更加灵活和友好；总是随手可得的东西。

### 管理员用户配置

![oxide1-06](img/29a5e23e4ad16f9df3184b83fab8283e.png)

现在只差几步了。接下来，您将能够设置商店 URL 和管理员用户详细信息。您将看到商店 URL 和商店根目录的目录以及临时目录已经预先填充。

除非你需要，让它们保持原样。输入管理员用户的用户名和密码，然后单击“保存并继续”。至此，设置完成，商店前端和管理员登录的链接可用。

## 流动商店

![oxide1-07](img/6b7677eae6783198d26d2e6f6c575eaa.png)

通过点击购物，你将被带到商店的前台，看起来就像上面的截图。这显示了在安装过程中加载了我们选择的演示数据时，商店的默认安装是什么样子。

它有一个非常时尚的主题，展示了一个冲浪商店。您可以看到最新的特价商品、最划算的商品、滚动横幅、带有彩色下拉菜单的顶级导航，以及右上角的用户和结账相关菜单选项。很干净，很时尚，也很清新。

![oxide1-08](img/84596299ea2a60d5006818a1a641454b.png)

在上面的截图中，我们已经深入到了风筝冲浪类别。像许多其他电子商务商店一样，它以视觉和文字的形式显示子类别。在商店的底部，你可以清楚地看到所有商店共有的子商品(无论是正常的还是合法的)。

所以总而言之，在前端 OXID 是一个非常现代和功能丰富的电子商务商店，能够满足所有，但最苛刻的或定制的在线商务需求。现在让我们来看看后端，以结束这个介绍。

## 管理商店

![oxide1-09](img/cb71bbaaaa3f38cc414fc52d28cae3ff.png)

在上面的屏幕截图中，您可以看到有绝对丰富的选项可用，从标准基础包括:

*   配置产品
*   产品类别
*   国家
*   制造商/供应商
*   产品属性
*   CMS 页面
*   用户和组管理

然后直接进入更高级的选项:

*   定制/第三方模块
*   第三方扩展
*   支付方式
*   税收和运输规则
*   商店统计

在您的安装中，稍微试验一下，熟悉一下可用的选项以及可以进行多少配置。像所有具有大量特性和可配置复杂性的包一样，要完全熟悉它需要一点时间。

## 结论

这就结束了对 OXID eSales 的广泛介绍。我知道这是一个有点高层次的阶段，我们没有得到太多的手。但这一切都将改变。在第 2 部分中，我们将开始创建一个基于默认的自定义主题。然后，在第 3 部分，我们将创建一个定制模块。所以，请留下来，准备好阅读一些好的代码。

那么你觉得 OXID 怎么样？它的设置和配置不像 Magento 那样复杂，但这并不意味着它不适合您或您的客户的电子商务需求。我希望您已经看到了它是一个功能强大、功能丰富的商店，在本系列的后续部分中，您可以对其进行定制，以满足您的项目需求。你以前用过吗？你愿意为你的客户尝试一下吗？

请在评论中告诉我。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章