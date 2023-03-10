# 用 eZ publish 建立一个电子商务网站

> 原文：<https://www.sitepoint.com/ecommerce-website-ez-publish/>

本教程提供了使用 eZ publish 创建具有在线信用卡处理功能的定制电子商务网站的分步说明。

通过本教程，我将解释如何:

*   设置不同的产品类别
*   发布产品
*   创建定制产品结构
*   为产品页面使用自定义模板
*   延长结账流程
*   整合网站与在线支付网关

不需要编程经验，尽管你可能需要友好的系统管理员的帮助来安装 eZ publish。你也可以使用 [eZ publish LiveCD](http://ez.no/ez_publish/demo) 来跟随这个教程。

##### 要求

为了完成本教程，您需要:

*   Apache Web 服务器
*   PHP，版本 4.3.4 或更高
*   现代关系数据库，如 MySQL 3.23 . x 或更高版本，或 PostgreSQL 7.3 或更高版本。
*   系统管理技能，或者网络服务器的系统管理员的服务

##### 装置

要使用 eZ publish 运行在线业务，您首先需要安装应用程序。你可以在 ez.no 网站上找到[完整的安装文档](http://ez.no/ez_publish/documentation/installation)。

##### 设置

安装后，您必须运行基于 Web 的安装向导来配置 eZ publish 的安装。

当设置向导要求您选择“站点类型”时，选择“商店”，如下所示。

![1472_1](img/988237dc9e5379e45714a170259bb927.png)
[以全尺寸观看此图像](https://www.sitepoint.com/examples/ezecommerce/setup_site_type.png)。

选择网站类型后，会要求您选择网站的功能。除了已经建议的功能之外，选择“新闻”。网站功能选项定义了您可以在网站上发布的内容种类；如果愿意，您可以在安装后更改您的选择。这是配置屏幕。

![1472_2](img/023a57702cb56aa0ce24c416056c7ee7.png)
[以全尺寸观看此图像](https://www.sitepoint.com/examples/ezecommerce/setup_site_functionality.png)。

下面的截图显示了默认的 eZ publish shop 配置在设置后的样子。

![1472_3](img/ea4bb9369f3e2d5309b141c5a43b384f.png)
[以全尺寸观看此图像](https://www.sitepoint.com/examples/ezecommerce/post_install.png)。

##### 用户化

一旦安装了 eZ publish，您就可以开始定制网站了。

eZ publish 电子商务网站的默认设置包括在线商店的基本功能，包括:

*   目录(包括新产品和最受欢迎产品列表等功能)
*   购物车
*   搜索功能

我们的第一个定制是将网站的外观和感觉改为更合适的设计。出于本教程的目的，你可能喜欢使用 eZ publish 附带的标准红色“主题 8”，或者你可能希望[下载额外的主题](http://ez.no/community/contribs/site_styles)。默认情况下，eZ publish 中的菜单显示在页面的顶部和左侧。我们可以改变这一点，创建一个双层顶部菜单，在屏幕顶部的两个层次中包含我们的导航。

另一个有用的变化是让新产品和最受欢迎产品的列表显示在所有页面上，而不仅仅是在产品页面上。

关于如何改变主题以及调整菜单和工具栏的信息，请参阅 SitePoint 的文章，标题为[用 eZ publish](https://www.sitepoint.com/article/build-intranet-ez-publish) 构建一个支持 WebDav 的内部网。要了解更多关于如何用 eZ publish 改变设计的信息，请阅读文章[用 eZ publish 构建无表 CSS 布局](https://www.sitepoint.com/article/table-less-css-ez-publish)。

完成这些定制后，网站看起来像下面的屏幕截图。

![1472_4](img/d6b7de417b2670df73c25d0180e849ff.png)
[以全尺寸观看此图像](https://www.sitepoint.com/examples/ezecommerce/post_configuration.png)。

##### 购物

现在我们已经安装并配置了基本的 eZ publish 安装，我们可以对它进行测试了:让我们购买一个产品。

目前，我们将使用安装中已经存在的一个产品。按照以下步骤测试购物过程:

1.  从“最新产品”列表中选择“黑盒”产品

3.  点击“添加到购物篮”

5.  点击“结帐”

7.  填写酒店账户信息，包括姓名、电子邮件和地址详情

9.  点击“继续”

11.  点击“确认”，订单完成

要验证订单是否已成功完成，请转到管理界面并查看订单列表，您可以通过单击管理界面中的“网上商店”选项卡来访问该列表。下面的屏幕截图显示了两个已完成订单的订单列表。

![1472_5](img/4ab28d464bee5e23fa3b60ef16b0af20.png)

##### 添加新产品

购买测试产品是可以的，但是你可能更感兴趣的是把你自己的产品带进商店。要添加新产品，请转到管理界面，然后:

1.  单击管理界面中的“内容结构”选项卡

3.  点击左侧菜单中的“产品”文件夹

5.  在产品列表底部的下拉列表中选择“产品”(如下图所示)

![1472_6](img/82bd9cb0f0011c26b0fdc6db1be9e43e.png)
[以全尺寸观看此图像](https://www.sitepoint.com/examples/ezecommerce/admin_product_list.png)。

您将看到产品编辑屏幕。填写必要的字段，然后单击“发送以供发布”按钮。您的产品现在可以立即从网站上获得，并且可以购买！您可以在产品文件夹和最新产品列表中找到它。您也可以搜索它，因为 eZ publish 中的所有内容都由内置的搜索引擎自动索引。

![1472_7](img/e832afa4dc1ea4d40ef0660038623f82.png)
[以全尺寸观看此图像](https://www.sitepoint.com/examples/ezecommerce/std_product_edit.png)。

##### 定制产品

大多数在线商店需要容纳比 eZ publish 的标准产品类型具有更多属性的产品。为了适应定制类型的产品，我们将创建一个名为“我的产品”的新内容类；这将形成具有附加属性的产品的基本类型。为此:

1.  转到管理界面中的“设置”选项卡

3.  点击左侧菜单中的“课程”

5.  单击“内容”类组

7.  单击“新建课程”按钮

9.  输入名称“我的产品”作为类名

11.  添加您想要的类属性

13.  单击“确定”按钮

eZ publish 类基本上是一个已定义属性的列表。每个属性可以保存特定格式的信息，例如，文本、XML、图像或价格。

下面是添加和填充所有额外属性后的类编辑屏幕截图。在类编辑屏幕的底部，您会看到一个用于选择数据类型的下拉菜单。您可以将数据类型添加为属性:选择适当的数据类型，然后单击“添加属性”按钮。使用这个工具，您可以定制内容模型以满足您的确切需求。当您添加了所有需要的属性后，单击“OK”按钮保存您的类定义。

将以下属性添加到该类中:

*   标题(文本行)
*   摘要(XML 文本)
*   描述(XML 文本)
*   选项(多选项)
*   图像(图像)
*   价格(价格)

![1472_8](img/81f58edf0a6eb905e1df07c977b94344.png)
[以全尺寸观看此图像](https://www.sitepoint.com/examples/ezecommerce/class_edit.png)。

##### 发布定制产品

现在我们已经创建了“我的产品”产品定义，我们可以创建这种类型的产品。为此，请转到管理界面中的“内容结构”选项卡，选择“我的产品”，然后按照上面的步骤创建产品。

下面的截屏显示了我们新产品的编辑屏幕。这与我们之前看到的标准产品编辑屏幕非常相似，但是请注意，您现在可以填写其他产品选项。因为我们添加了一个 multioption 类型的属性，所以我们可以向我们的产品添加一个或多个选项。期权用于以不同的价格出售产品的变体。像往常一样，点击“发送以发布”按钮发布产品。

![1472_9](img/93f064e841df77532dedf5916bec3404.png)
[以全尺寸观看此图像](https://www.sitepoint.com/examples/ezecommerce/custom_product_edit.png)。

##### 自定义外观

如果您现在查看您的网站，您会看到新产品已经推出。因为我们给产品添加了价格属性，所以它已经可以购物了。您可以将产品添加到购物车中，完成购物过程。

一个问题是，产品在默认模板中看起来不太好，默认模板基本上只列出了可用的属性。为了使产品符合网站的配置文件，我们需要为“我的产品”类的产品创建一个自定义模板。

要创建自定义模板，请转到管理界面并:

1.  点击“设计”选项卡

3.  从左侧菜单中选择“模板”

5.  从“最常用模板”列表中选择“/node/view/full.tpl”

7.  单击“新建覆盖”按钮

*   输入名称“我的全部产品”
*   选择“我的产品”作为类别覆盖键
*   选择“视图”模板

*   单击“确定”按钮

我们现在已经为产品的完整视图创建了一个新的覆盖模板。请注意，每一部分内容——搜索结果、产品列表、右侧工具栏列表等等——都可以以不同的方式显示。全视图是显示任何对象的默认视图。

下面的屏幕截图显示了填写了正确值的覆盖编辑页面。在这个屏幕上点击“OK”按钮后，eZ publish 会生成一个模板文件，可以使用普通的文本编辑器编辑，也可以通过 eZ publish 管理界面进行编辑。

![1472_10](img/aee9cc61864850b7c3e8475d80c735cb.png)

模板覆盖列表页面显示完整产品视图的所有现有覆盖模板的列表。我们新创建的模板将出现在该列表的末尾。单击列表右侧的编辑图标，您将看到如下所示的模板编辑屏幕。

![1472_11](img/e82a263d65be2b3ad62f09f8af524b0a.png)

为了使我们的产品页面更令人愉快，我们编辑了用来显示它的模板。在这个例子中，我使用了如下所示的代码。我通过在

# 标签中显示产品的标题来开始模板。使用带有类`"imageright"`的`<div>`标签将图像右对齐。三个属性——`teaser`、`description`和`options`——然后一个接一个地列出。价格是使用`<h2>`标题标记呈现的。模板的最后一部分是显示“添加到购物篮”按钮的表单。

```
<h1>{$node.name}</h1>  

<div class="imageright">  

{attribute_view_gui attribute=$node.object.data_map.image}  

</div>  

{attribute_view_gui attribute=$node.object.data_map.teaser}  

{attribute_view_gui attribute=$node.object.data_map.description}  

{attribute_view_gui attribute=$node.object.data_map.options}  

<h2>{attribute_view_gui attribute=$node.object.data_map.price}</h2>  

<form method="post" action={"content/action"|ezurl}>  

   <input type="submit" class="defaultbutton"   

          name="ActionAddToBasket"   

          value="{"Add to basket"|i18n("design/base")}" />  

   <input type="hidden" name="ContentNodeID" value="{$node.node_id}" />  

   <input type="hidden" name="ContentObjectID" value="{$node.object.id}" />  

   <input type="hidden" name="ViewMode" value="full" />  

</form>
```

当我们使用我们新创建的模板查看产品时，它如下所示。

![1472_12](img/6b47360826f00e651d97722f7da3fc6d.png)
[以全尺寸观看此图像](https://www.sitepoint.com/examples/ezecommerce/customized_product.png)。

##### 结账流程

eZ publish 中的购物流程是基于工作流系统的。默认情况下，它由以下四个基本步骤组成:

1.  篮子

3.  用户信息

5.  订单确认

7.  订单视图

但是，您可以根据需要在订单确认之前和之后添加步骤来扩展该流程。订单确认前最常添加的步骤为用户提供了从许多不同的运输方式中进行选择的能力。可以插入其他步骤来请求购物者在购买特定产品时提供附加信息；例如，如果您在销售许可证，您可能会询问许可证持有人的姓名。另一个步骤可能允许您展示客户可能也想购买的相关产品。

订单确认后，我们通常会加上信用卡付款。但是也可以添加其他集成，比如在订单完成时将订单信息发送到另一个系统，或者将用户分配到一个用户组。通过在完成订单时将用户分配到特定的用户组，您可以创建一个能够销售内容或软件的商店。将用户分配到组意味着您可以授予该用户额外的权限，例如，在有限的时间内下载受限文件或查看受限内容的权限。工作流由一个或多个工作流事件组成。这些工作流事件是使用 PHP 编程语言通过插件接口创建的，因此您可以在 eZ publish 中自由定制购物流程。

[PayPal](http://www.paypal.com) 信用卡集成与 eZ publish 捆绑在一起。关于如何正确配置这个扩展的信息可以在 [eZ publish 文档](http://ez.no/ez_publish/documentation/building_an_ez_publish_shop/extending_the_shop_with_a_paypal_payment_service )中找到。

##### 获得帮助

就是这样！多亏了 eZpublish，您拥有了一个功能齐全的定制购物车。如果您在开发和定制网站时需要更多帮助，请访问 [eZ publish forums](http://ez.no/community/forum) ，在这里您可以找到所有 eZ publish 解决方案的安装、配置、定制和开发方面的帮助。

## 分享这篇文章