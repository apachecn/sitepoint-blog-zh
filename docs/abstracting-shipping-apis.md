# 抽象运输 API

> 原文：<https://www.sitepoint.com/abstracting-shipping-apis/>

您的新定制电子商务商店即将完成。剩下的唯一一件事就是弄清楚如何为你的客户计算运费。你不想对每个地址都采用统一的标准费率，因为你知道你会对一些顾客要价过高，更重要的是对其他顾客要价过低。运费可以根据物品的重量/尺寸和目的地来计算，这不是很好吗？也许你甚至可以提供一个准确的隔夜运费报价！

您有一个 UPS 帐户，并且您已经检查了他们的 API，但是它看起来相当复杂。如果你硬编码你的站点来使用 API，如果你需要改变发货人，你会有很多工作要做。你的堂兄是联邦快递的销售代表，他发誓说他可以给你更好的价格。你的一些客户只有我们的邮政信箱，所以那些项目必须由邮局运送。你是做什么的？

您可能听说过数据库抽象，这种实践允许您使用一组通用的命令来使用许多不同的数据库。这正是你在这里可以做的！为了解决所有这些问题，您可以将运输任务从代码的其余部分中分离出来，并构建一个抽象层。一旦你完成了，不管你是通过 UPS，FedEx，还是 USPS 运输包裹都没关系。您的核心应用程序将调用的功能都是相同的，这让您的生活变得更加轻松！

## UPS 入门

在本文中，我将重点介绍如何使用 UPS API，但通过为不同的托运人(如 FedEx 或 USPS)编写插件，您也可以访问他们的服务，只需对您的核心应用程序进行微不足道的代码更改。

为了开始使用 UPS，您需要使用您现有的托运人号码在 www.ups.com[注册一个在线帐户。请确保选择一个您暂时可以放心使用的用户名和密码，因为 API 在每次调用时都需要这两个用户名和密码。接下来，前往 https://www.ups.com/upsdeveloperkit](http://www.ups.com "Shipping, Freight, Logistics, and Supply Chain Management from UPS")[注册使用 UPS API。在这里，您将获得 API 密钥，并能够下载不同 API 包的文档。(注意:UPS 网站的这个部分有一个已知的问题，Chrome 有时会返回一个空白页。您可能需要使用备用浏览器。)](https://www.ups.com/upsdeveloperkit "UPS Developer Kit")

请记住，当您使用 UPS API(或任何托运人的 API)时，您同意遵守他们的规则和程序。确保阅读并遵循它们，尤其是在生产中使用代码之前包括它们的说明。

接下来从 github.com/alexfraundorf-com/ship 的 GitHub 下载或克隆 shipping abstraction layer 包，并上传到运行 PHP 5.3 或更高版本的服务器上。打开`includes/config.php`文件。您需要在这里输入您的 UPS 详细信息，并且字段名称应该是不言自明的。请注意，UPS 托运人地址需要与 UPS 为您的帐户记录的地址相匹配，否则将会发生错误。

## 定义装运和包装

现在来定义一个`Shipment`对象。在实例化时，它将接受一个包含接收方信息的数组，如果它与我们的配置文件中的发货方信息不同，还可以接受一个 ship from 地址。

```
<?php
// create a Shipment object
$shipment = new ShipShipment($shipmentData);
```

接下来，我们需要一些关于我们运输的细节。让我们创建一个`Package`对象，它接受重量、包裹尺寸和一些基本选项的可选数组，如描述(如果需要签名)和保险金额。新实例化的`Package`然后被添加到`Shipment`对象中。模拟生命的软件才有意义:每个包裹都属于一个货件，每个货件至少要有一个包裹。

```
<?php
// create a Package object and add it to the Shipment (a 
// shipment can have multiple packages)

// this package is 24 pounds, has dimensions of 10 x 6 x 12
// inches, has an insured value of $274.95, and is being
// sent signature required
$package1 = new ShipPackage(
    24,
    array(10, 6, 12),
    array(
        'signature_required' => true,
        'insured_amount' => 274.95
    )
);
$shipment->addPackage($package1);

// weight and dimensions can be integers or floats,
// although UPS always rounds up to the next whole number.
// This package is 11.34 pounds and has dimensions of
// 14.2 x 16.8 x 26.34 inches
$package2 = new ShipPackage(
    11.34,
    array(14.2, 16.8, 26.34)
);
$shipment->addPackage($package2);
```

### 幕后:装运对象

打开`Awsp/Ship/Shipment.php`，我们将检查`Shipment`对象，它基本上包含了我们的发货插件需要了解的关于货物的所有内容。

构造函数接受装运数据的数组(并将其存储为对象属性),这些数据是接收方的信息，如果不同于发货方的地址，还可以接受发货方的信息。接下来，构造函数调用`sanitizeInput()`来确保数组可以安全使用，并调用`isShipmentValid()`来确保所有需要的信息都已提供。

除此之外，我们有一个公共方法`get()`,它接受一个字段名(数组键)并从装运数据数组返回相应的值，还有公共函数`addPackage()`和`getPackages()`,您猜对了，向装运添加一个包并检索属于装运的`Package`对象。

### 幕后:包装对象

打开`Awsp/Ship/Package.php`，我们将检查`Package`对象，它基本上包含了我们的托运人插件需要了解的关于单个包裹的所有内容。`Package`对象是`Shipment`对象的一部分，`Shipment`可以根据需要拥有任意多个`Package`对象。

`Package`构造函数接受包裹重量、尺寸(以任何顺序)和一组可选选项，如描述、类型、保险金额以及是否需要签名。权重和选项在对象属性中设置，尺寸按从长到短的顺序排列。然后我们将它们按顺序分配给对象属性`$length`、`$width`和`$height`。这对发货插件很重要，因为长度必须总是最长的维度。然后，它使用`isPackageValid()`来确保所有需要的参数都存在并且是正确的类型。最后`calculatePackageSize()`用于计算包裹的尺寸(长度加上周长)，这将被一些托运人插件使用。

`Package`对象提供的其他公共函数有:返回对象属性的`get()`、返回特定选项设置的`getOption()`，以及几个用于转换托运人插件重量和长度的辅助函数。

## 发货人插件

我们有一批包裹，现在我们需要访问我们想要使用的托运人插件。插件将接受`Shipment`对象和`$config`数组(在`includes/config.php`中定义)。

```
<?php
// create the shipper object and pass it the shipment
// and config data
$ups = new ShipUps($shipment, $config);
```

我们的`Ups`对象，或者我们随后创建的任何其他 shipper 插件，将实现`ShipperInterface`，我们的契约允许我们保证无论我们使用哪个 shipper，公共函数(接口)将总是相同的。如摘自`ShipperInterface.php`的这段话所示，我们所有的 shipper 插件都必须有一个`setShipment()`方法来设置对`Shipment`对象的引用，一个`setConfig()`方法来设置配置数组的副本，一个`getRate()`方法来检索运费，一个`createLabel()`方法来创建运输标签。

```
<?php
interface ShipperInterface
{
    public function setShipment(Shipment $Shipment);
    public function setConfig(array $config);
    public function getRate();
    public function createLabel();
}
```

## 获取运费

为了计算我们的包裹的运费，我们将调用我们的`Ups`对象的`getRate()`方法。因为它将执行网络调用，所以我们需要确保将它包装在一个 try/catch 块中，以防出错。

假设我们的数据没有错误，`Ups`对象将我们的信息组织成 UPS API 可识别的格式，发送出去，并将响应处理成一个`RateResponse`对象，该对象对于我们合并的所有发货人都是统一的。

```
<?php
// calculate rates for shipment - returns an instance of 
// RatesResponse
try {
    $rates = $ups->getRate();
}
catch(Exception $e) {
    exit('Error: ' . $e->getMessage());
}
```

我们可以遍历服务阵列，然后显示可用的运输选项:

```
<!-- output rates response -->
<dl>
 <dt><strong>Status</strong></dt>
 <dd><?php echo $rates->status; ?></dd>
 <dt><strong>Rate Options</strong></dt>
 <dd>
  <ul>
<?php
foreach ($rates->services as $service) {
    // display the service, cost, and a link to create the
    // label
    echo '<li>' . $service['service_description'] . ': ' .
        '$' . $service['total_cost'] .
        ' - <a href="?action=label&service_code=' .
        $service['service_code'] . '">Create Label</a></li>';
?>
   <li>Service Message:
    <ul>
<?php
    // display any service specific messages
    foreach($service['messages'] as $message) {
        echo '<li>' . $message . '</li>';
    }
?>
    </ul>
   </li>
<?php
    // display a breakdown of multiple packages if there are
    // more than one
    if ($service['package_count'] > 1) {
?>
   <li>Multiple Package Breakdown:
    <ol>
<?php
        foreach ($service['packages'] as $package) {
            echo '<li>$' . $package['total_cost'] . '</li>';
        }
?>        
    </ol>
   </li>
<?php
    }
}
?>
  </ul>
 </dd>
</dl>
```

### 幕后:RateResponse 对象

`RateResponse`对象本质上是一个简单的对象，它以标准化的格式包含我们的费率数据，因此无论我们使用哪个 shipper 插件，该对象(以及我们如何与之交互)都将是相同的。那才是抽象的真正美！

如果你打开`Awsp/Ship/RateResponse.php`，你会看到这个对象仅仅拥有一个名为`$status`的属性，它总是‘成功’或‘错误’，以及一个名为`$services`的数组。在这个数组中，将有一个由 shipper 插件返回的每个运输选项的元素，每个元素将包含“消息”、“服务代码”、“服务描述”、“总成本”、“货币”、“包裹计数”和一个名为“包裹”的数组，该数组保存每个包裹的以下数据:“基本成本”、“选项成本”、“总成本”、“重量”、“计费重量”和“重量单位”。

有了包含在`RateResponse`对象中的数据，并且可以轻松地从该对象中提取数据，您应该拥有向客户提供运费选项所需的一切。

## 创建运输标签

由于抽象的 API，您的客户对您能够提供的定制运输选项印象深刻，于是他们进行了购买。您已经处理了他们的订单，并准备好创建运输标签。理想情况下，我们需要做的就是在`Shipper`对象上调用`createLabel()`，并传递给它想要的运输选项。

```
<?php
// set label parameters
$params['service_code'] = '03'; // ground shipping

// send request for a shipping label 
try {
    // return the LabelResponse object
    $label = $ups->createLabel($params);
}
catch (Exception $e){
    exit('Error: ' . $e->getMessage());
}
```

除非数据有问题，否则将返回一个`LabelResponse`对象，其中包含请求的状态、运输的总费用、一个数组，该数组包含跟踪号和标签的 base-64 编码图像，以及每个运输标签的图像类型(对于 UPS 是 GIF)。

### 幕后:LabelResponse 对象

类似于`RateResponse`对象，`LabelResponse`对象是一个简单的对象，它以标准化的格式包含我们的标签数据，因此无论我们使用哪个 shipper 插件，对象(以及我们如何与它交互)总是相同的。抽象很牛逼！

如果你打开`Awsp/Ship/LabelResponse.php`,你会看到这个对象仅仅保存了名为`$status`的属性，这些属性总是‘成功’或‘错误’，`$shipment_cost`,这是货物的总成本，还有一个名为`$labels`的数组。在这个数组中，每个标签都有一个元素，每个元素都是一个数组，包含“tracking_number”和“label _ image ”( base-64 编码的标签图像)以及“label_file_type ”(表示图像类型)(我们的 UPS 标签是 GIF 图像)。

有了包含在`LabelResponse`对象中并可轻松从中提取的数据，您将拥有提取、打印和保存您的包裹追踪编码和标签所需的一切。

### 幕后:UPS 托运人插件

在我们的例子中，shipper 插件`Awsp/Ship/Ups.php`的工作是获取`Package`和`Shipment`对象中的标准化输入，并将其转换成 shipper API 可以理解的形式。UPS 提供两种风格的 API，SOAP 和 XML-RPC，并根据需要在每年的 7 月和 12 月进行更新。这个插件使用 2012 年 12 月版的 SOAP API，您需要确保在您的 PHP 安装中启用了`SoapClient`类。

在接受并处理包含`Package`对象的`Shipment`对象和`$config`数组(来自`includes/config.php`之后，构造函数设置一些对象属性和一些所有 API 请求共有的值。

其他公共函数`getRate()`和`createLabel()`处理将所有数据组装成 UPS 能够理解的复杂数组的工作。然后，这些方法中的每一个都调用`sendRequest()`向 UPS API 发送 SOAP 请求并检索响应。然后，各种各样的受保护函数根据请求将 SOAP 响应翻译成标准化的`RateResponse`或`LabelResponse`对象。

## 结论

有很多要读，但你做到了！通过一组简单的调用，您可以通过 UPS API 或任何带有合适插件的托运人 API 请求费率和创建标签。API 的所有神秘和复杂之处都被抽象出来，使我们能够保持它与代码库的其余部分分离，并省去许多未来维护的麻烦。当 shipper 更新他们的 API 时，唯一需要修改的文件应该是 shipper 插件。

UPS 在记录他们的 API 方面做得非常好，为了简洁起见，还有许多功能和选项我没有包括在这个简单的例子中。如果您需要扩展托运人插件，UPS 文档应该始终是您的第一站。

你想看不同发货人的插件吗？请在下面的评论中告诉我。如果有足够的需求，我们可以做这篇文章的后续报道。不过，有一点免费的建议:如果你想通过美国邮政局整合运输，省省你自己的大麻烦，不要浪费时间使用他们的官方 API。请访问[stamps.com](http://www.stamps.com "Stamps.com - Buy Postage Online, Print USPS Stamps and Shipping Labels")或另一家 USPS 认可的供应商。

请随意在我的 GitHub 页面[github.com/alexfraundorf-com/ship](https://github.com/alexfraundorf-com/ship "alexfraundorf-com/ship - GitHub")下载这个抽象库的副本或分叉，并提交您发现的任何错误的问题报告。我会尽最大努力修复它们，并尽快处理更新。

感谢您的阅读，祝您 PHPing 愉快！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章