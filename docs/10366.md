# 教程:Magento 主题开发简介

> 原文：<https://www.sitepoint.com/an-introduction-to-magento-themes/>

2008 年，位于洛杉矶的一家小公司 Varien 悄悄地向全世界发布了一个开源电子商务平台。Magento，后来被称为 magento，迅速吸引了希望从 osCommerce 和 Zen Cart 时代转向更专业、更强大的系统的 web 开发人员。

Magento 已经赢得了灵活和强大的声誉，但对开发人员来说仍然有点神秘。该平台的文档过去和现在都很少。在本教程中，我们将看看 Magento 强大的主题系统，并学习如何为 Magento 建立一个简单的自定义主题。

## Magento 模板

Magento 附带的模板系统初看起来可能有点复杂，但是正如我们将会发现的那样，我们为复杂性付出的代价为我们提供了大量的灵活性和功能。

模板系统有三个组件:

*   PHP 模板文件
*   PHP 块类
*   XML 布局配置

**模板文件**包含您期望模板系统处理的内容，比如 HTML、JavaScript 和一些 PHP。

**块类**允许我们将可重用的功能从 PHP 模板文件转移到 PHP 类中，将来可以在不同的模板文件中再次使用。通常，所有模板文件都有一个关联的块类。Block 类只是普通的 PHP 类，可以通过`$this`变量在模板中访问。

各种助手方法，如`getSkinUrl($path)`或`getUrl($path)`，都包含在所有的块类中。这些方法然后被用在一个模板文件中，例如通过调用:`$this->getSkinurl('images/logo.png')`。

除了为模板文件提供有用的方法之外，块还用于决定如何将模板文件呈现给用户。为此，每个块都有一个`toHtml()`方法。通常，`toHtml()`方法将简单地解析模板文件并将其输出给用户，但是我们可以覆盖该功能并返回我们喜欢的任何内容，比如 XML 或 JSON。

最后， **XML 布局配置**文件是将整个模板文件集合在一起以在浏览器中呈现的“粘合剂”。在 XML 中，我们可以指定想要加载什么样的 PHP 模板/块组合，以及它们在页面上的显示顺序。

## 文件夹结构

毫无疑问，关于 Magento，你首先会注意到的一件事是有数百个文件夹和数千个文件。让我们来看看几个更重要的修改模板和布局的方法:

```
app/
  code/
    {core,local,community}/
      Company/
        Module/
          Block/
  design/
    frontend/
      base/
        default/
          layout/
          template/
      mytheme/
        default/
          layout/
          template/
        christmas/
          layout/
          template/
  skin/
    frontend/
      base/
        default/
          css/
          images/
        mytheme/
          default/
            css/
            images/
          christmas/
            css/
            images/ 
```

所有模板文件都在`app/design/frontend/{$interface}/{$theme}/template`文件夹下的各个文件夹中的`.phtml`文件中。所有布局 XML 文件都可以在`app/design/frontend/{$interface}/{$theme}/layout`文件夹中找到。所有块都将在`app/code/{core,local,community}/{$company}/{$module}/Block`文件夹中。

但是这些界面和主题名称是什么呢？用 Magento 模板术语来说，界面是主题的集合。因此，主题只是一堆布局和模板文件。通常，在给定的 Magento 安装中，您会有一个包含许多主题的界面。该界面将定义你的电子商务网站的整体布局，而一个主题位于它的顶部，并赋予它一个特殊的外观。假设你有一个网站界面和默认主题。例如，当您想定制商店进行假日促销时，您只需在同一界面中创建一个新主题，并在销售期间切换到该主题。

### 包含层次结构

与 WordPress 非常相似，Magento 依赖于层次结构来定位主题文件。每当一个模块指定系统需要加载一个布局文件时，Magento 会在许多地方查找。

举个例子，假设`Mage_Customer`模块请求加载`customer.xml`来定义我们试图显示的`customer/account/login`页面的布局。让我们假设我们已经创建了一个名为“sitepoint”的接口；有三个主题，叫做“圣诞节”、“买五送一”和“违约”；并指定我们希望在 Magento 管理区使用“圣诞节”主题。

Magento 将按顺序检查以下文件夹，查找名为`customer.xml`的文件:

*   应用程序/设计/前端/站点点/圣诞节/布局
*   应用程序/设计/前端/站点点/默认/布局
*   应用/设计/前端/基础/默认/布局

这种定位文件的策略也用于模板文件和皮肤文件。这意味着没有必要将所有的布局、模板和皮肤文件复制到圣诞主题中；我们只复制将要更改的文件。任何未更改的文件都将通过包含层次结构的向下标题来定位和加载。

这种搜索策略使得多个主题非常容易管理。例如，如果我们决定只改变产品页面的模板，那么我们只需要将`app/design/frontend/sitepoint/default/template/catalog/product/view.phtml`复制到`app/design/frontend/sitepoint/christmas/template`目录中。但是如果我们决定 CSS 保持不变，那么我们会将`styles.css`文件留在`skin/frontend/sitepoint/default/css`中，它仍然会被加载。

## 布局文件示例

让我们来看看(并剖析)一个示例布局 XML 文件:

```
<layout>
  <customer_account_register>
    <block name="wrapper">
      <block type="core/template" template="page/html/header.phtml">
        <block name="navigation" template="page/html/navigation.phtml" />
      </block>
    </block>
  </customer_account_register>
</layout> 
```

该文件当前有两个部分:部分定义和块定义。

我们正在定义的部分是针对网站上的`/customer/account/register`页面的，它在 XML 中被称为`customer_account_register`，称为**布局句柄**。

当你访问运行 Magento 的网站上的任何页面时，注意 URL(关闭 SEO 重写)；他们总是以`{$module}/{$controller}/{$action}`开头。这为编辑 XML 文件中的哪个布局句柄提供了大量线索:`{$module}_{$controller}_{$action}`。

在节定义中，我们列出了一些我们希望包含在页面中的块。正如您所看到的，块可以是自封闭的(以`/>`结尾),也可以包含其他块。

块可以有很多属性来帮助我们组织和描述它们在页面上的显示方式。最小程序块定义必须包含一个程序块`type`和一个`name`。

可以通过指定一个**模板**属性来定义与块相关联的模板。前面，我们提到所有模板都必须有一个块类型。这种关系是不对称的；我们可以定义一个没有模板的块。这个块只需使用它的`toHTML()`方法来生成它的输出。

## 深入模板文件

让我们来看看布局 XML 中提到的`header.phtml`文件:

```
<html>
  <head>
    <title><?php echo $this->getTitle() ?></title>
  </head>
  <body>
    <h1><a href="<?php echo $this->getUrl() ?>"><img src="<?php echo $this->getSkinUrl('images/logo.png') ?>" /></a></h1>
    <?php echo $this->getChildHtml('navigation') ?>
    <p>Welcome to my shop!</p>
```

我们可以看到前面提到的`$this`变量的用法。有一些标准方法可供所有块使用，例如:

*   `getUrl($path)`:使用 Magento 管理区域中定义的基本 URL，该方法将生成一个完整的 URL，例如，如果您从开发环境转移到生产环境，这将非常有用。例子用法:`$this->getUrl('customer/account/login')`会产生`http://www.example.com/customer/account/login`。
*   `getSkinUrl($path)`:这和`getUrl()`方法的想法是一样的，但是使用的是“皮肤/前端”中的皮肤目录和 Magento 管理中激活的界面和主题。对皮肤目录中的文件进行的分层查找与对模板文件进行的查找相同。
*   `getChildHtml($name)`:这允许我们调用布局中定义的嵌套或子块。我们很快会更详细地讨论这个问题。

我们的示例布局文件中的`getTitle()`函数只对`core/template`块可用——这是有意义的，因为它的作用是输出页面的`title`元素。

### 嵌套子块

可用的更强大的方法之一是`getChildHtml()`方法。在上面的布局 XML 中，您可以看到`header`块中嵌套了另一个块。Magento 不会知道在模板文件中输出子块的 HTML 的位置，所以我们需要通过调用`getChildHtml($name)`方法显式地定义放在哪里，并提供我们想要显示的子块的名称作为第一个参数。子块是将页面的各个部分分离成可重用组件的好方法。

## 关于布局手柄的更多信息

正如我们前面提到的，布局句柄将 URL 映射到布局文件中的特定区域。Magento 将在每次页面加载时合并不同的布局句柄。这些布局手柄可以扩展和修改，但通常遵循以下原则:

*   `default`
*   `STORE_{$theme}`
*   `THEME_frontend_{$interface}_{$theme}`
*   `{$module}_{$controller}_{$action}`

如果我们想给网站上的所有页面添加内容，必须指定我们可能想到的每一个句柄是愚蠢的，所以我们只使用`<default>`句柄。

当 Magento 向用户呈现一个页面时，它会按照前面讨论的包含层次结构加载它能找到的所有布局 XML 文件，并依次查找上面的句柄。由此可见，任何布局 XML 文件都可以包含系统中任何页面的句柄。

## 引用声明的块

所有的全局块都在`default`句柄中定义。

但是现在我们有一个问题。如果我们想在一些页面的标题块中添加一些内容，而不是在其他页面的标题块中添加，该怎么办？这是否意味着我们需要将标题分别放入每个布局句柄？幸运的是，没有。使用`<reference>`标签解决了这个问题。

引用标记允许您访问另一个块的内部:引用标记内的任何内容都将被放置在目标块内。它如何知道目标是哪个块？您只需将名称属性设置为与目标块相同。这样，您可以从任何布局文件向任何块添加新子块:

```
<layout>
  <default>
    <block name="wrapper">
      <block type="core/template" template="page/html/header.phtml">
        <block name="navigation" template="page/html/navigation.phtml" />
      </block>
      <block type="core/template" template="page/html/footer.phtml" />
    </block>
  </default>
  <customer_account_register>
    <reference name="wrapper">
      <block type="core/template" name="customer_hello" template="customer/helloworld.phtml" />
    </reference>
  </customer_account_register>
</layout>
```

## 删除声明的块

例如，如果我们已经在默认句柄中定义了一个块，但是我们只想从*customer _ account _ register*页面中删除它，该怎么办？这里有一个例子，我们去掉了*客户/账户/注册*页面的页脚:

```
<layout>
  <default>
    <block name="wrapper">
      <block type="core/template" template="page/html/header.phtml">
        <block name="navigation" template="page/html/navigation.phtml" />
      </block>
      <block type="core/template" template="page/html/footer.phtml" />
    </block>
  </default>
  <customer_account_register>
    <reference name="wrapper">
      <block type="core/template" name="customer_hello" template="customer/helloworld.phtml" />
      <remove name="footer" />
    </reference>
  </customer_account_register>
</layout>
```

## 包装和块排序

您可能已经注意到，`header`和`footer`块实际上被一个名为`wrapper`的块所包装，类型为`text/list`。为了解释这是如何工作的，我们需要回到块的`toHtml()`方法。

`text/list`块的`toHtml()`方法将检查它的所有子块，然后分别调用这些子块上的`toHtml()`方法。输出的块的顺序通常是它们在 XML 中出现的顺序；然而，我们可以用`after`和`before`属性覆盖这个行为。所以包装器只是一个其他块的列表，这些块将按照一定的顺序显示。

下面是修改后的布局 XML，对块的位置做了一些修改:

```
<layout>
  <default>
    <block name="wrapper">
      <block type="core/template" template="page/html/header.phtml" >
        <block name="navigation" template="page/html/navigation.phtml" />
      </block>
      <block type="core/template" template="page/html/footer.phtml"/>
    </block>
  </default>
  <customer_account_register>
    <reference name="wrapper">
      <block type="core/template" name="customer_hello" template="customer/helloworld.phtml" after="header" />
    </reference>
  </customer_account_register>
</layout>
```

## 最佳实践

尽管我们还没有完全探索布局和模板，但我觉得有义务让我们开始一些关于最佳实践的讨论。由于 Magento 是如此难以驯服，即使对于最有经验的开发人员来说，也很容易陷入无意中无视最佳实践的陷阱。考虑到这一点，这里有一些主题开发的指导原则。

### 放手/默认

如前所述， *base/default* 是 Magento 在系统中所有更重要的位置都找不到模板文件时使用的主题。正是因为这个原因，Magento 把所有默认的模板和布局文件都放在这里。当你更新 Magento 时，默认模板可能会被修改。任何此类更新都将被写入*基本/默认*文件夹。

如果你已经使用了*基本/默认*主题来包含你的模板和布局变化，它们很有可能会随着更新而丢失。正是因为这个原因，如果你希望你的客户能够升级他们的 Magento 安装，**永远不要修改*基础/默认*！**

解决这个问题的最快最简单的方法是创建一个默认主题的新界面(只需在 app/design/frontend/{ $ interface }/default 中创建一个新文件夹)；将您想要更改的任何文件复制到该文件夹(遵循与 *base/default* 相同的目录结构)并在那里进行更改。

### 使用 local.xml

你会注意到 *base/default* 的布局文件夹包含了几十个 XML 文件，所有这些文件都与系统中不同的模块相关。当创建你自己的主题来覆盖 *base/default* 功能时，让 Magento 加载*base/default*customer . XML 通常被认为是最佳实践，所以你应该避免将你自己的`customer.xml`放在你的主题的布局文件夹中。相反，将您的更改应用到另一个名为`local.xml`的文件。

`local.xml`文件位于您的`app/design/frontend/{$interface}/{$theme}/layout`目录中，并且在主题化过程结束时可能会包含许多更新句柄。

Magento 会自动加载`local.xml`文件，所以你所做的任何改变都会被载入网站。

# 真实世界的例子

所以我们已经确定了这个理论，对吗？让我们开始为 Magento 制作我们自己的主题。在我们开始之前，您应该[下载 Magento](http://www.magentocommerce.com/download) 并将其安装在您的本地 web 服务器或虚拟机上。

## 安装

首先，让我们创建一个文件夹来存放我们自己的文件。创建以下文件夹结构和文件(用您想要的界面名称替换`mycompany`):

```
app/design/frontend/
  mycompany/
    default/
      layout/
        local.xml
      template/
skin/frontend/
  mycompany/
    default/
      images/
      css/
```

`local.xml`文件暂时可以保持为空。

接下来，让我们在 Magento 管理区域启用我们的主题。在管理仪表板中导航至**系统- >设计**。选择**添加设计变更**并选择你的新主题。如您所见，您可以将设计变更设置为在一年中的特定日期生效。这对于我们之前使用的圣诞节示例来说是完美的，但是现在只需将这些字段留空，让您的设计成为默认设置。

如果刷新前端，您会发现什么都没有改变。这是正常的，因为页面被缓存了。要禁用缓存，以便您可以在开发期间看到您的更改，请转到**系统- >缓存管理**并禁用一切。

回到前端，你会注意到所有的风格都消失了。看一下页面的源代码。所有样式表和图片的 URL 仍然指向`base/default`。这是正常的:Magento 遵循 include 层次结构，已经注意到我们的主题中还不存在有问题的文件。`base/default`没有`styles.css`文件，所以所有的样式都消失了。为了给我们自己一个起点，让我们从`skin/frontend/default/default/css`中取出样式表，并把它们放到我们的`skin/frontend/mycompany/default/css`文件夹中。同样，将图片从`default/default`移动到新主题的`images`文件夹中。当你修改主题的时候，你可以删除任何你不会用到的，但是现在在你工作的时候看看还是不错的。

## 移除块

Magento 附带了一个“返校”广告，在主页的右栏有 PayPal 标志。原来我们卖的东西和学生无关，不接受 PayPal，那就去掉吧。我们想从网站的每个页面上移除这些块，所以我们想把移除代码放在`default`布局句柄中。

首先，我们需要确定块的名称。为了计算出下一步的块名，我们必须启用块调试。进入**系统- >配置- >开发者- >调试**，启用**模板路径提示**和**添加块名到提示**选项。(如果您没有看到这些选项，请确保您处于除屏幕左上角的全局范围切换器之外的任何位置)

现在，当您重新加载前端时，您将看到页面上显示在它上面的每个块的名称。我们需要搜索`app/design/frontend/base/default/layout/`中的布局 XML 文件，以找到带有我们正在寻找的类型和模板的块的名称。如果您的编辑器或 IDE 有一个文件夹范围的搜索选项，这是使用它的好时机！

“返校”广告有块类型`Mage_Core_Block_Template`和模板`callouts/right_col.phtml`。贝宝标志有块类型`Mage_Paypal_Block_Logo`和模板`paypal/partner/logo.phtml`。

经过一些调查工作，我们发现 PayPal 块在`paypal.xml`中定义如下:

```
<block name="paypal.partner.right.logo" template="paypal/partner/logo.phtml"/>
```

以及在`catalog.xml`中定义如下的“返校”块:

```
<block name="right.permanent.callout" template="callouts/right_col.phtml">
  <action method="setImgSrc"><src>images/media/col_right_callout.jpg</src></action>
  <action method="setImgAlt" translate="alt" module="catalog"><alt>Keep your eyes open for our special Back to School items and save A LOT!</alt></action>
</block>
```

不用担心那些`action`标签；重要的是我们现在有了自己的名字:`paypal.partner.right.callout`和`right.permanent.callout`。

要放在我们的 local.xml 中的 XML 如下所示:

```
<?xml version="1.0"?>
<layout>
  <default>
    <remove name="paypal.partner.right.logo" />
    <remove name="right.permanent.callout" />
  </default>
</layout>
```

刷新页面，你应该会发现块已经消失了！

## 重新组织侧边栏

现在，我们已经删除了这些块，我们有一个侧边栏包含一个迷你购物车，“比较产品”框，和一个投票。我们刚刚推出，所以让我们的投票更加突出来吸引客户的反馈可能是个不错的主意。让我们把投票移到列的顶部。

我们要做的就是删除这些块，然后用正确的`before`和`after`属性将它们添加回去，以实现我们想要的布局。

让我们看看组成侧栏的各个部分。我们正在寻找一个`<reference name="right">`，因为块出现在右列；我们还在寻找我们可以在前端的红框中看到的块类型和模板名称。

我们在右边栏中找到了最重要的投票定义:

```
<default>
  <reference name="right">
    <block type="poll/activePoll">
      <action method="setPollTemplate"><template>poll/active.phtml</template><type>poll</type></action>
      <action method="setPollTemplate"><template>poll/result.phtml</template><type>results</type></action>
    </block>
  </reference>
</default>
```

避免被那些`action`标签吓倒；它们所做的只是允许您从布局文件中调用 block 类的方法。我们可以看到，目前没有为块定义`before`或`after` 属性。我们的`local.xml`文件现在可以更新为:

```
<?xml version="1.0"?>
<layout>
  <default>
    <remove name="paypal.partner.right.callout" />
    <remove name="right.permanent.callout" />
    **<reference name="right">
      <remove name="right.poll" />
      <block type="poll/activePoll" before="cart_sidebar">
        <action method="setPollTemplate"><template>poll/active.phtml</template><type>poll</type></action>
        <action method="setPollTemplate"><template>poll/result.phtml</template><type>results</type></action>
      </block>
    </reference>**
  </default>
</layout>
```

## 添加广告

好吧，一切都很顺利。我们整理了右边的专栏，并通过投票收到了大量用户反馈。超级大公司已经与我们接触，他们希望赞助我们的网站，作为回报，他们会在右边一栏展示广告。好消息！让我们开始吧。

我们需要做的第一件事是制作一个模板。我们可以把这个放在`app/design/frontend/mycompany/default/template`的任意文件夹里；我们将创建一个名为`sponsor`的新文件夹，并在其中创建一个名为`superbig.phtml`的文件。该文件可以包含您希望作为广告显示的任何 HTML。

接下来，我们需要将它添加到布局 XML 中。我们可以使用块类型`core/template`，因为我们不需要任何保证创建新块的功能:

```
<?xml version="1.0"?>
<layout>
  <default>
    <remove name="paypal.partner.right.callout" />
    <remove name="right.permanent.callout" />
    <reference name="right">
      <remove name="right.poll" />
      <block name="right.poll" before="cart_sidebar">
        <action method="setPollTemplate"><template>poll/active.phtml</template><type>poll</type></action>
        <action method="setPollTemplate"><template>poll/result.phtml</template><type>results</type></action>
      </block>
      **<block type="core/template" template="sponsor/superbig.phtml" />**
    </reference>
  </default>
</layout>
```

## 添加新的样式表

我们想给目前正在加载的当前`base/default`样式表添加一些定制。让我们包含另一个 CSS 文件，以便我们可以添加我们自己的调整。

页面上的资源加载，比如 JavaScript 和 CSS，是由 Magento 在布局文件中的`head`块处理的。

### `head`区块

让我们来看看目前的`head`区块。它位于`page.xml`:

```
<block name="head" as="head">
  <action method="addJs"><script>prototype/prototype.js</script></action>
  <action method="addJs" ifconfig="dev/js/deprecation"><script>prototype/deprecation.js</script></action>
  <action method="addJs"><script>lib/ccard.js</script></action>
  <action method="addJs"><script>prototype/validation.js</script></action>
  <action method="addJs"><script>scriptaculous/builder.js</script></action>
  <action method="addJs"><script>scriptaculous/effects.js</script></action>
  <action method="addJs"><script>scriptaculous/dragdrop.js</script></action>
  <action method="addJs"><script>scriptaculous/controls.js</script></action>
  <action method="addJs"><script>scriptaculous/slider.js</script></action>
  <action method="addJs"><script>varien/js.js</script></action>
  <action method="addJs"><script>varien/form.js</script></action>
  <action method="addJs"><script>varien/menu.js</script></action>
  <action method="addJs"><script>mage/translate.js</script></action>
  <action method="addJs"><script>mage/cookies.js</script></action>
  <action method="addCss"><stylesheet>css/styles.css</stylesheet></action>
  <action method="addItem"><type>skin_css</type><name>css/styles-ie.css</name><params/><if>lt IE 8</if></action>
  <action method="addCss"><stylesheet>css/widgets.css</stylesheet></action>
  <action method="addCss"><stylesheet>css/print.css</stylesheet><params>media="print"</params></action>
  <action method="addItem"><type>js</type><name>lib/ds-sleight.js</name><params/><if>lt IE 7</if></action>
  <action method="addItem"><type>skin_js</type><name>js/ie6.js</name><params/><if>lt IE 7</if></action>
</block>
```

正如我们之前提到的，`action`标签允许我们调用与块类相关的方法。

为这个块定义了许多方法，但是我们只对几个真正感兴趣:

*   `addJs`:这允许我们包含位于 Magento 安装根目录下的/js 目录中的 JavaScript。
*   这允许我们为我们的主题包含皮肤目录中的资产。

包含样式表的语法是:

```
<action method="addItem"><type>$type</type><name>$name</name><params>$params</params><if>$if</if></action>
```

在 Magento 设计师/开发人员的职业生涯中，最有可能用到的`$type`值是:

*   `skin_js`
*   `skin_css`

顾名思义，它们分别加载 JavaScript 和 CSS，位于主题的皮肤文件夹中，如果没有找到，则遵循包含层次结构。

`$name`指的是您的资产在`skin/frontend/mycompany/default/`文件夹中的位置，因此`skin/frontend/mycompany/default/css/updates.css`中的样式表将有`$name` `css/updates.css`。

对于样式表，`$params`值可以用来将参数传递给`<link />`标签，比如样式表适用的媒体。

`$if`值允许你在 Internet Explorer 的[条件注释](https://reference.sitepoint.com/css/conditionalcomments)中包装一个样式表。

### 添加到`head`块

因此，我们已经介绍了`head`块以及如何使用它来包含各种资产；让我们添加自己的样式表。

我们希望样式表加载到每一页，所以我们将使用`default`布局句柄。我们正在更新一个已经定义的块，所以我们将使用一个`reference`。

我们更新的布局代码现在看起来像这样:

```
<?xml version="1.0"?>
<layout>
  <default>
    <remove name="paypal.partner.right.callout" />
    <remove name="right.permanent.callout" />
    <reference name="right">
      <remove name="right.poll" />
      <block type="poll/activePoll" before="cart_sidebar">
        <action method="setPollTemplate"><template>poll/active.phtml</template><type>poll</type></action>
        <action method="setPollTemplate"><template>poll/result.phtml</template><type>results</type></action>
      </block>
    </reference>
    **<reference name="head">
      <action method="addItem">
        <type>skin_css</type>
        <name>css/updates.css</name>
        <params />
        <if />
      </action>
    </reference>**
  </default>
</layout>
```

## 恭喜你！

这就对了。您已经学习了如何添加样式表、删除块、添加新块，以及如何按照您的意愿使用 Magento。当然，这里还有很多我们没有涉及的内容，但是我希望我已经给了你开发你自己的 Magento 主题的基础。要了解更多，请查看 [Magento 知识库](http://www.magentocommerce.com/knowledge-base)中的[主题化&设计部分](http://www.magentocommerce.com/knowledge-base/categories/category/theming-design/)，以及 [Magento wiki](http://www.magentocommerce.com/wiki/) 。

## 分享这篇文章