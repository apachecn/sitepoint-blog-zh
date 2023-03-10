# 使用重力表单处理付款

> 原文：<https://www.sitepoint.com/processing-payments-with-gravity-forms/>

本教程将概述如何使用 [Gravity Forms](http://www.gravityforms.com/) 插件结合[Gravity Forms-PayPal 支付标准插件](http://www.gravityforms.com/add-ons/paypal/)插件来创建自定义表单，以处理 WordPress 网站中的支付。

我们将简要地看一下 Gravity Forms 是如何工作的，以及如何使用它的附加支付插件让您在支付的同时获取信息。我们不会关注表单的设计或者它们有多丰富的功能。我打算展示的是如何使用这个插件创建快速简单的表单来捕获数据进行操作(让插件来完成繁重的支付处理)。

## 警告–这些不是免费插件

请注意，Gravity Forms(及其将使用的相关 PayPal 插件)不是免费插件。

Gravity Forms 有一个基于订阅的模型，你可以是一个**【个人】****【商务】**或**【开发者】**用户，你购买插件的访问权。要使用 PayPal 支付功能，您需要拥有**“开发人员”**许可证来下载所需的插件。本教程将需要使用这些插件。

## 什么是重力形式？

Gravity Forms 是一个表单构建插件，允许您使用点击和拖动界面快速轻松地创建定制的表单。您可以使用这个插件创建各种各样的表单，从简单的联系人查询到高级的、有条件构建的表单。

![Gravity Forms](img/8fa61fbca5867a19df42eef820624b2a.png)

使用 Gravity Forms，您可以创建包含所有标准表单元素的表单；文本字段、选择列表、复选框和单选框。此外，它还带有几个有用的元素，如日期选择器，电话号码，捕获和列表。

使用这个插件，你可以很容易地把一个表格放在一起，然后输出到你选择的页面或帖子上(使用他们的短代码)。

![Sample Gravity Form](img/d8e4f33887ffdeda2199a5c4e2967f3f.png)

## 重力形成附加和支付

虽然 Gravity Forms 在创建表单方面做得很好，但有时您的表单需要做额外的处理或逻辑，或者链接到第三方服务。这就是重力形式的附加组件发挥作用的地方。

这些附加的插件/插件可以从他们的在线库中[查看和下载，并且一旦下载，它们可以被上传到你的网站并像普通插件一样被激活。](http://www.gravityforms.com/add-ons/)

由于本教程讨论的是 PayPal 支付，我们将使用 PayPal 支付标准插件。这将使我们能够连接到一个贝宝帐户，并接受付款我们的自定义表单。

还有许多其他 PayPal 重力表格插件；然而，这些通常是为传统的贝宝帐户(或需要特殊功能的帐户)提供的。对于几乎所有的当前帐户(和新的贝宝帐户)，这将是正确的插件选择。

![Gravity Forms PayPal Payments Standard](img/a672fe6fefa83089e96ad75aca71a16c.png)

## 场景–会员表格

为了展示这个插件是如何有用的，我们将使用它来创建一个会员表单，将会员申请提交到一个在线社区。

在我们的场景中，我们希望捕获关于个人的信息，然后处理他们的付款。我们将收集您期望的所有标准信息，如姓名、电子邮件、电话号码等。

一旦他们完成付款，我们将通过电子邮件发送所有信息。

这个场景的目的是让您能够访问成功交易的所有表单数据和信息。一旦你在这一点上，你可以使用这些信息来创建一个会员档案，创建一些自动化的电子邮件或操纵你想要的数据。

## 入门指南

首先，您需要具备以下要素，以便继续学习本教程:

*   重力表单插件
*   Gravity Forms PayPal 支付标准附加组件
*   一个使用最新版本设置的 WordPress 实例(我已经测试过它可以在 WP 3.7+上工作)
*   FTP 访问(我们将使用重力形式的钩子/过滤器，所以你需要访问你的 functions.php 或类似的)

如前所述，Gravity Forms 不是一个免费的插件，你必须购买订阅才能使用。一旦你有了订阅，你就可以下载你需要的插件(对于 PayPal 集成，你需要开发者级别的许可)。

一旦你有了所有这些元素的排序，我们将直接进入创建一个接受付款的形式。

### 步骤 1–将 Gravity 表单连接到 PayPal

因为 Gravity Forms 与 PayPal 一起处理支付和交易，PayPal 需要进行配置，以便在处理支付时通知 Gravity Forms(它将使用 Gravity Forms 更新其交易信息；例如将订单标记为已完成)。

导航到网站的后端，进入“表单”菜单，找到“设置”选项卡。在“设置”标签中，你需要找到名为“PayPal”的子菜单。点击“PayPal”子菜单后，您应该会看到以下屏幕。

![PayPal Settings](img/94b43e386209de521128682d6668ee06.png)

你需要做的是复制本页提供的网址，进入你的 PayPal 账户。您将需要登录到您打算接受付款的帐户，并启用 IPN 处理。您将看到与下图类似的屏幕。

![Instant Payment Notification](img/ee30c797a77d96acd451c62d0aa71ff9.png)

在 PayPal 上更新您的 IPN 设置后，请返回到您网站的设置页面并启用复选框，确认您的 IPN 已正确设置。

**注意:**如果你不做这一步，各种钩子和过滤器将永远不会启动，你将会疑惑为什么什么都不工作。

### 步骤 2–创建您的会员表格

导航到 WordPress 后端，寻找“表单”菜单。您可以按此进入您所有表格的列表，或者您也可以点击“新建表格”按钮。

点击“新建表单”按钮后，将会出现一个窗口。只需输入表单的名称和简短描述，一旦重定向，您将看到以下屏幕。

![Blank Gravity Form](img/3f054547d637254179609bf8eebc0d08.png)

重力形式分为两个部分。左边部分将显示表单中的当前元素；而右边是您可以添加的所有表单元素的列表(方便地按功能分组)。

如果你需要更深入地了解重力形式是如何工作的，我建议你在他们的网站上阅读他们的文档。

#### 添加表单域

您现在需要做的是构建表单本身。对于这个例子，我将把这些字段添加到表单中。请随意跟随或添加您自己的字段。

*   名字-(标准字段-单行文本)
*   姓氏-(标准字段–单行文本)
*   电子邮件—(高级字段—电子邮件)
*   电话—(高级字段—电话)
*   地址-(高级字段-地址)
*   会员类型-(定价字段-产品)
*   总成本–(定价字段–总计)

添加完所有这些字段后，您可以继续配置它们以供使用。

#### 配置您的表单域

您可以选择的大多数表单域都可以配置多个选项。可以用选项填充下拉列表，可以要求处理字段，可以设置验证消息，等等。

在我们的场景中，我们只使用了 7 个字段，因此继续操作，单击每个字段以展开选项列表，并将它们配置如下:

*   **名字、姓氏、电子邮件**–将这些字段标记为“必填”
*   **电话，总费用**–保持原样，没什么特别的
*   **地址**–这些步骤对您来说可能略有不同，但是我决定使用“国际”设置，以及“隐藏地址行 2 字段”，默认国家选择为“澳大利亚”并标记为“必填”
*   **会员类型**–由于这是一种产品，因此有多种选择。我已经选择了“字段类型”**将是“单选按钮”，有两个选项:
    –年度会员(＄0.05)
    –两年会员(＄0.08)
    –此字段也将是“必填”的

一旦你配置了你的表单域，它看起来应该和我的类似

![Gravity Form](img/3ddea17d593679adf24f9dd42a849c88.png)

### 步骤 3–将您的表单与 PayPal 连接

既然我们已经设置了 Gravity 表单并将其连接到您的 PayPal 帐户，我们还需要将您的个人表单连接到 PayPal。

导航到 WordPress 后端，寻找**‘表单’**菜单。在这个菜单下，你会看到一个名为**【贝宝】**的标签。点击此处继续(如果您看不到此处，表示您尚未安装重力表格的 PayPal 附加组件)。

![PayPal Forms](img/c03fdde5565a8b5022ff128a301b271a.png)

根据您使用的版本，以上信息可能会有所不同。如果你没有看到如上图所示，在“表单>(选择你的表单)>表单设置”下查找。

在这个屏幕上，你可以看到你所有的 PayPal 表格。

添加新的付款表格时，您需要提供以下信息:

*   **PayPal 电子邮件地址**–输入用于收款的地址
*   **模式**–选择生产或测试
*   **交易类型**–选择“产品和服务”
*   **Gravity Form**–选择将与此付款相关联的表格。继续并选择您刚刚创建的表单。
*   选择后，您将看到更多信息。该信息与 PayPal 集成相关，包括将您的字段映射到标准 PayPal 字段，选择取消 URL 位置和其他元素。如有需要，请随意填写。

完成并保存后，您应该会看到类似下面的内容。

![PayPal Transaction Settings](img/ecff9dad9a209dafc337cdefe7df53b0.png)

### 步骤 4–将您的会员表格添加到页面

现在表单已经准备好了，您需要做的就是将表单添加到页面中并测试它。

导航到网站的后端，找到“页面”菜单项。创建一个新的页面，如果你仔细看可视化编辑器，会有一个新的按钮；这个按钮会通过一个短代码将你的表单快速添加到你的页面。

![Gravity Forms Shortcode](img/341cc9e6804730998cc333057f1c1ab6.png)

## 使用重力形式的钩子和过滤器

一旦您成功设置了您的表单，您应该能够接受此会员系统的付款。

用户将输入他们的信息，处理贝宝，支付他们的服务，然后可选地返回网站(为每个人都产生通知)。

虽然重力形态会做所有的繁重工作，但你可能需要执行额外的动作或功能，这取决于发生了什么。这就是重力形式变得非常有用的地方，因为它提供了许多我们可以使用的钩子和过滤器。

对于我们的会员系统，我们对 PayPal 插件提供的支付交易挂钩和过滤器感兴趣。[这里有几个有用的](http://www.gravityhelp.com/documentation/page/PayPal_Add-on_Developer_Docs)；然而，对于本教程，我们将着眼于`gform_paypal_fulfillment`钩。

### PayPal 交易后的处理

我们将使用的主要钩子是`gform_paypal_fulfillment`钩子。

这个钩子在 PayPal 交易成功完成后运行，并返回一个有用信息的列表，我们可以在后端操作中使用这些信息。

这个挂钩应该连接到您选择的函数，传递以下四个变量:

*   `$entry`
*   `$config`
*   `$transaction_id`
*   `$amount`

这些变量中的每一个都将包含关于您的事务及其数据的信息。对于这个例子，我们将提取关于这个成员的所有表单数据，然后通过电子邮件发送这些信息。

首先创建一个函数，并把它附加到这个钩子上，如下所示:

```
//function used to hook onto after payments
function membership_payment_processing($entry,$config,$transaction_id,$amount){
        //your post processing to go in here
}
add_action('gform_paypal_fulfillment','membership_payment_processing',10,4);
```

作为一个警告，一定要将钩子的“优先级”设置为 10，将“参数计数”设置为 4(因为这个钩子通常希望这些被传递)。

#### `$entry`内的值

我们创建的新功能将在付款成功处理后立即触发。

我们将研究`$entry`变量，它包含我们事务的一组值。分配给这个数组的值的完整列表可以在 Gravity forms $entry 对象上查看，但是我们需要的主要值是:

*   `form_id`
*   `payment_date`
*   `payment_status`

除了这些命名的值，`$entry`数组还将包含表单上完成的所有字段。这些将以数字方式构造，用字符串表示字段的关键字。例如`'1'`或者`'2'.`

在我们的例子中，我们的第一个字段是`first name`，因此属于`first name`的值将被存储在`$entry`中，键名为`'1'`，因此我们将把它分配给一个变量，如下所示:

```
//assign the first name field to a variable
$first_name = $entry['1']
```

你的表单可能有**堆**字段，这些字段可能被上下移动或在不同时间创建，所以一般来说你不能依赖表单编辑器中的位置是正确的。您可以遍历`$entry`数组的每个键和值，然后将它们分配给一个字符串。一旦你为你所有的值做了这些，你就可以给你自己发一封电子邮件(这样你就可以很容易地看到密钥和它的值)。

```
//collect all entry values
function membership_payment_processing($entry,$config,$transaction_id,$amount){
        $message = '';
        foreach($entry as $key=>$value){
                $message .= 'Key: ' . $key . ' Value: ' . $value;
        }
        //send yourself an email (replace the email address below with yours)
        wp_mail('simonrcodrington@gmail.com','Entry Information`,$message);
}
add_action('gform_paypal_fulfillment','membership_payment_processing',10,4);
```

在此之后，您将能够确定什么键属于什么值，并根据需要分配变量。

#### 提取我们需要的信息

让我们继续我们的例子。对于我们的例子，我们将把表单字段提取到变量中，这样我们就可以存储它们的值。您的代码应该类似于下面的代码(记得先进行一次测试，并确定表单字段的相应字符串值)。

```
//process membership after transaction
function membership_payment_processing($entry,$config,$transaction_id,$amount){
	//first determine if we are processing the correct form
	$form_id = $entry['form_id'];
	if($form_id == 1){

		//collect values values
		$first_name = $entry['1'];
		$last_name = $entry['2'];
		$email = $entry['3'];
		$phone = isset($entry['4']) ? $entry['4'] : ''; 
		$address_street = $entry['5.1'];
		//notice '5.2' is missing, this is address line 2 we removed from the address configuration
		$address_city = $entry['5.3'];
		$address_state = $entry['5.4'];
		$address_postcode = $entry['5.5'];
		$address_country = $entry['5.6'];
		$membership_type = $entry['6'];
		$total_cost = $amount;
		$transaction_id = $transaction_id;

		//format these values into a message that we can send
		//replace values as required
		$to ='simonrcodrington@gmail.com';
		$subject = 'New Membership Application';

		$header_cell_style = 'background: #ccc; text-align: center; padding: 5px 7px 5px 7px;';
		$cell_style = 'background: #eee; text-align: center; padding: 5px 7px 5px 7px;';

		$message = '';
		$message .= '<h2>New Member Application</h2>';
		$message .= '<p>A new member has successfully been processed</p>';
		$message .= '<table border="1" style="border-collapse: collapse;">'; 
		$message .='<tr>';
		$message .='<th style="' . $header_cell_style . '"> First name </td>';
		$message .='<th style="' . $header_cell_style . '"> Last name </td>';
		$message .='<th style="' . $header_cell_style . '"> Email </td>';
		$message .='<th style="' . $header_cell_style . '"> Phone </td>';
		$message .='<th style="' . $header_cell_style . '"> Address Street </td>';
		$message .='<th style="' . $header_cell_style . '"> Address City </td>';
		$message .='<th style="' . $header_cell_style . '"> Address State </td>';
		$message .='<th style="' . $header_cell_style . '"> Address Postcode </td>';
		$message .='<th style="' . $header_cell_style . '"> Address Country </td>';
		$message .='<th style="' . $header_cell_style . '"> Membership Type </td>';
		$message .='<th style="' . $header_cell_style . '"> Total Cost </td>';
		$message .='<th style="' . $header_cell_style . '"> Transaction ID </td>';
		$message .='</tr>';
		$message .='<tr>';
		$message .='<td style="' . $cell_style. '">' . $first_name . '</td>';
		$message .='<td style="' . $cell_style. '">' . $last_name . '</td>';
		$message .='<td style="' . $cell_style. '">' . $email . '</td>';
		$message .='<td style="' . $cell_style. '">' . $phone . '</td>';
		$message .='<td style="' . $cell_style. '">' . $address_street . '</td>';
		$message .='<td style="' . $cell_style. '">' . $address_city . '</td>';
		$message .='<td style="' . $cell_style. '">' . $address_state . '</td>';
		$message .='<td style="' . $cell_style. '">' . $address_postcode . '</td>';
		$message .='<td style="' . $cell_style. '">' . $address_country . '</td>';
		$message .='<td style="' . $cell_style. '">' . $membership_type . '</td>';
		$message .='<td style="' . $cell_style. '">' . $total_cost . '</td>';
		$message .='<td style="' . $cell_style. '">' . $transaction_id . '</td>';
		$message .='</tr>';
		$message .= '</table>';

		//add filter to enable HTML messages
		add_filter('wp_mail_content_type','set_email_to_html'); 

		//send mail
		wp_mail($to,$subject,$message); 

		//remove filter (to set back to plain text email)
		remove_filter( 'wp_mail_content_type', 'set_email_to_html' );

	}
}
add_action('gform_paypal_fulfillment','membership_payment_processing',10,4);

//sets the email content type to HTML
function set_email_to_html(){
	return true;
}
```

如果你对 WordPress mail 更感兴趣，你可以[点击这里查看 codex 页面](http://codex.wordpress.org/Function_Reference/wp_mail)。

我们已经钩住了`gform_paypal_fulfillment`钩子，并确定正在收集正确的表单(在我的例子中是表单 1)。一旦我们知道我们在正确的表单上，我们就收集表单值。

获得所有值后，我们创建一个简单的表结构并填充内容。

我们使用一个名为`wp_mail_content_type`的过滤器。此过滤器决定您的邮件是以纯文本还是 HTML 格式发送。我们在发送电子邮件之前调用这个过滤器，之后我们再次删除它(以确保我们不会产生冲突)。

我们发送邮件到我们想要的地址，它应该被格式化为一个基本的表格(有一些样式)。你应该有类似的东西，取决于你的电子邮件程序。

## 最后

现在，您已经知道如何使用 Gravity Forms 及其 PayPal 插件来接受支付，您可以使用它作为基础来创建更复杂的功能(无需手动集成支付网关)。

## 分享这篇文章