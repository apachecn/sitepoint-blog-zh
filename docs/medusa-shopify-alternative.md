# Shopify 替代方案:使用 Medusa 构建可定制的灵活电子商务解决方案

> 原文：<https://www.sitepoint.com/medusa-shopify-alternative/>

**本文介绍了 [Medusa](https://medusajs.com/) ，这是一个开源的、以开发者为中心的 Shopify 替代品，它允许根据需要进行定制和扩展。**

Shopify 是一个电子商务平台，为商家提供了一种简单快捷的方式来建立他们的在线商店。在创建在线商店时，这是不想要其他平台的技术复杂性的企业的普遍选择。

然而，众所周知，Shopify 的可扩展性是有限的，试图在其中构建更多的定制逻辑对开发人员来说可能是一种可怕的体验。

Medusa 是一个开源的 Shopify 替代品，旨在解决其中一些扩展问题。

开箱即用，Medusa 提供了与 Shopify 类似的核心电子商务功能，但其开放和可组合的架构在构建定制逻辑或与第三方服务集成时，允许更大的灵活性和更好的开发人员流程。

本文介绍了 Medusa 相对于 Shopify 的优势以及如何开始使用 Medusa。

## 美杜莎的优点

让我们仔细看看美杜莎为开发者提供了什么。

### 以开发者为中心的平台

Medusa 是为开发人员构建的，其主要目的是提供出色的开发人员体验。它由抽象层构建，如果需要任何定制，这些抽象层使它高度可扩展。

由于其[无头](https://www.sitepoint.com/headless-web-design-strategic-advantage/)和抽象架构，添加集成也很容易。开发人员不需要担心使用变通方法来使平台与其他服务和工具一起工作。他们可以扩展现有的抽象层来创建任何集成。

作为一个以开发者为中心的 Shopify 替代方案，Medusa 提供了详细的[文档](https://docs.medusajs.com/)，这些文档不断得到改进，以允许开发者按照简单的步骤构建他们的电子商务平台。

### 美杜莎的无头建筑

![Medusa's architecture](img/90d6c30dffc41730ff19d5870eb6fbf8.png)

无头是将前端和后端解耦的概念。对于后端，这意味着它摆脱了任何表示层的束缚。后端只负责处理系统的数据和逻辑。

后端公开了一组 API，允许任何前端或表示层与之交互。

这种分离让开发人员可以自由选择前端使用哪种语言或框架。开发者可以使用像[盖茨比](https://www.sitepoint.com/gatsby-guide/)和 [Next.js](https://www.sitepoint.com/routing-in-next-js-beginner-guide/) 这样的 [Jamstack](https://www.sitepoint.com/learn-jamstack/) 框架。他们还可以整合不同类型的渠道，如移动应用程序或市场。

Medusa 由三个组件组成:无头服务器、店面和管理面板。尽管 Medusa 提供了两个入门商店——一个用 [Next.js](https://docs.medusajs.com/starters/nextjs-medusa-starter) 构建，另一个用 [Gatsby](https://docs.medusajs.com/starters/gatsby-medusa-starter) 构建，以及一个直观设计的管理面板——开发人员可以完全自由地用任何其他框架从头构建这些组件。他们所需要做的就是与[REST API](https://docs.medusajs.com/api/store/auth)进行交互。

这使得在前端工作的开发人员可以专注于提供独特和良好的用户体验，而无需将店面与后端紧密结合。

### 开源的 Shopify 替代方案

专有解决方案的一个很大的缺点是，你永远不会完全拥有你的技术栈。无论你有多少定制能力，你在功能或工具的选择上总是受到限制。

另一方面，像 Medusa 这样的开源解决方案允许您完全拥有自己的技术堆栈。你可以使用美杜莎作为你的电子商务商店，但你也可以完全定制的核心，并适应任何特定的需求。

这种灵活性保证了企业可以拥有一个完全符合其用例的平台。此外，随着时间的推移，他们的平台可以随着业务需求的增长而增长。

无论他们需要在未来添加更多功能和定制，还是确保平台可以随着时间的推移而扩展，Medusa 都可以做到这一点，没有任何限制。

### 开发商和商家的灵活性

上述优势所带来的灵活性意味着开发者和商家在他们的 Medusa 电子商务平台中选择使用的服务和工具时都有充分的自由。

例如，对于丰富的 CMS 功能，商家可以选择集成 Strapi、Contentful 或任何其他他们想要的服务。

对于可以使用的工具或服务没有限制。在选择本地支付和履行提供商时，这一点甚至更为重要。

所有的集成都是通过[插件](https://docs.medusajs.com/advanced/backend/plugins/overview)完成的。你可以选择现成的插件或者[创建自己的](https://docs.medusajs.com/advanced/backend/plugins/create)。

插件以即插即用的方式安装在您的 Medusa 服务器上。它们在与 Medusa 服务器相同的进程中运行，因此您不需要分配额外的服务器空间或基础设施。

### 高级电子商务功能

抛开所有的技术优势，Medusa 还提供了许多开箱即用的高级电子商务功能，包括以下内容。

#### 多种货币支持

Shopify 业务遇到的一个大问题是支持商店中的多种货币。此功能需要特殊条件，具体取决于您所在的国家或您的计划。

然而，即使启用了此功能，它仍然不能为企业提供格式化和设置每个产品定价的灵活性。

该功能的缺乏及其局限性导致企业为每种货币建立和管理多个商店。这导致了大量额外的手动工作和人为错误。

另一方面，Medusa 提供了开箱即用的多区域支持，并且没有任何限制。您可以根据需要添加任意数量的区域，指定这些区域的货币，并从同一个管理仪表板中分别管理每种货币的每种产品的价格。

![Multiple prices for each currency for a product](img/1f2dbc5fc9119a04c246f140817bb00a.png)

这消除了管理多个商店带来的额外手工工作的需要，并防止了随之发生的人为错误。

#### 自动化 RMA(退货授权)流程

根据 [UPS](https://www.ups.com/media/en/gb/ups_global_paper.pdf) 的统计，73%的购物者表示他们的退货经历会影响他们将来是否会再次从该零售商处购买。因此，为您的电子商务商店提供简单、便捷的退货体验至关重要。

Medusa 提供了完全自动化的退货、换货和索赔流程，从客户请求从店面退回商品到商品被标记为已退回，都可以从管理仪表板轻松处理。

物品也可以用同样的方式交换。这也将自动反映在集成的会计系统，并可以很容易地从管理仪表板管理。

#### 促销 API 和礼品卡

![Discounts in Medusa](img/a253a35667c8bbed1bf2d8da14ec75be.png)

Medusa 提供了一个具有许多定制功能的高级促销 API。商家拥有这种促销 API 的标准用例，你可以在所有平台上找到——例如设置免费送货折扣、固定金额折扣或折扣百分比。

此外，商家可以指定特定于客户群、产品、兑换次数以及更多条件的折扣。

此外，礼品卡可以通过 admin dashboard 轻松添加和管理到 Medusa 中，并且它们的处理方式不同于购买的其他产品。礼品卡也有自己的高级条件和可定制的设置，可以从管理仪表板中指定。

#### 多供应商支持

越来越需要能够创建在线市场电子商务平台的平台。美杜莎的扩展性使其成为创建在线市场的绝佳平台。

一个名为 [Medusa Extender](https://adrien2p.github.io/medusa-extender/#/) 的社区插件让这一点变得更加容易实现。这个插件为您提供了更多的定制能力，可以完全扩展核心并针对市场用例进行修改。

Medusa 还有一个[教程系列](https://dev.to/medusajs/create-an-open-source-commerce-marketplace-part-1-3m5k)，介绍了创建市场的基础知识——包括将用户和产品等不同实体链接到商店，管理每个商店和全球的订单，以及管理商店内的用户及其权限。

#### 其他功能

Medusa 附带了许多 API 和电子商务特性，为开发人员和商家提供了高级功能。

除了提供现成的设置之外，Tax API 还通过抽象来实现，以允许集成第三方服务和工具来处理高级税务。

![Taxes in Medusa](img/78ab91ae8924c8f862616bb3901142a2.png)

PriceList API 为与折扣、客户群等相关的产品提供了许多定价条件。

![Price list in Medusa](img/5af7b31f55839b974368d182f1afc21c.png)

headless 服务器中所有这些可用的设置和功能都可以通过 Gatsby 构建的直观管理仪表板轻松管理。

Medusa 还在开发更多功能，如产品的批量进出口、销售渠道、多仓库支持等。

### 社区和支持

作为一个以开发者为中心的平台和开源解决方案，Medusa 专注于与社区紧密合作，并提供所需的任何支持。

开发人员可以在 [Discord](https://discord.gg/medusajs) 上加入美杜莎的社区，在那里他们可以加入讨论，展示他们与美杜莎的合作，并在使用美杜莎时遇到麻烦时获得帮助。

开发人员几乎可以立即从社区和核心 Medusa 团队获得帮助。他们所要做的就是在`#need-help`频道上问一个问题，然后有人会指导他们解决问题——通常在几个小时之内。

开发人员还可以报告错误，为 Medusa 做贡献，并加入关于 GitHub 库的讨论。

## 如何开始使用美杜莎

本节概述了如何安装 Medusa 服务器、Medusa admin 和 Gatsby Storefront。

### 先决条件

要使用 Medusa，您需要安装至少 14 版的 [Node.js](https://nodejs.org) 。

### 如何设置 Medusa 服务器

首先安装 Medusa CLI 工具:

```
npm install -g @medusajs/medusa-cli 
```

然后，运行以下命令创建新的 Medusa 服务器:

```
medusa new my-medusa-store --seed 
```

这将在`my-medusa-store`目录中安装 Medusa 服务器，该目录也是创建的。`--seed`选项在`my-medusa-store`目录中创建并播种一个 SQLite 数据库。

最后，转到 Medusa 服务器的目录并启动服务器:

```
cd my-medusa-store
medusa develop 
```

默认情况下，服务器运行在端口`9000`上。您可以使用[REST API](https://docs.medusajs.com/api/store)对其进行测试，或者您可以按照本节的其余部分进行操作，并在连接到 Medusa admin 和 storefront 时查看它的运行情况。

还建议您安装一个存储插件来处理产品图像的存储。您可以使用 [MinIO](https://docs.medusajs.com/add-plugins/minio) 、 [S3](https://docs.medusajs.com/add-plugins/s3) 或[数字海洋空间](https://docs.medusajs.com/add-plugins/spaces)。

### 如何安装 Medusa admin

在一个单独的目录中，运行以下命令来克隆 Admin GitHub 存储库，并切换到新创建的目录:

```
git clone https://github.com/medusajs/admin medusa-admin
cd medusa-admin 
```

然后，使用 npm 安装依赖项:

```
npm install 
```

最后，确保 Medusa 服务器仍在运行，并启动 Medusa 管理服务器:

```
npm start 
```

管理员通常在`localhost:7000`运行。在您的浏览器中打开它，您应该会看到一个登录屏幕。

![Admin Login](img/dc10625bdb1839ce8e5be11ac08395c9.png)

要登录，您可以使用带有电子邮件`admin@medusa-test.com`和密码`supersecret`的演示用户凭证。

在管理仪表板上，您可以添加产品、管理订单、管理商店配置等等。

![Admin Dashboard](img/9ca613383327fc0cc2bb3f5db0e2c077.png)

### 如何安装盖茨比店面

如果尚未安装 Gatsby CLI，请安装它:

```
npm install gatsby-cli -g 
```

然后，在与前面不同的目录中，运行以下命令来安装 Gatsby storefront:

```
gatsby new my-medusa-storefront https://github.com/medusajs/gatsby-starter-medusa 
```

然后，切换到新创建的目录，并将`.env.template`重命名为`.env.development`:

```
cd my-medusa-storefront
mv .env.template .env.development 
```

最后，当 Medusa 服务器仍在运行时，启动 Gatsby 服务器:

```
npm start 
```

默认情况下，店面运行在端口`localhost:8000`上。在浏览器中打开它。您应该会看到一些来自 Medusa 服务器的演示产品。您可以将这些产品添加到购物车中，完成结账流程并下订单。您还可以创建一个帐户并查看客户档案功能。

![Gatsby Storefront](img/ee383a6891869358c9762190dfececf3.png)

## 后续步骤

这就是对 Medusa 的介绍，它是一个可行的 Shopify 替代方案，适用于在建立电子商务商店时寻求更多自由和灵活性的开发人员。

设置好 Medusa 服务器、管理和店面后，您可以探索使用 Medusa 可以做的更多事情，包括:

*   整合支付方式，如 [Stripe](https://docs.medusajs.com/add-plugins/stripe) 或 [PayPal](https://docs.medusajs.com/add-plugins/paypal) 。
*   将搜索功能与 [Algolia](https://docs.medusajs.com/add-plugins/algolia) 或 [MeiliSearch](https://docs.medusajs.com/add-plugins/meilisearch) 整合。
*   查看 [API 参考文献](https://docs.medusajs.com/api/store),了解更多关于 Medusa 服务器的信息。
*   了解如何[将美杜莎服务器部署到 Heroku](https://docs.medusajs.com/how-to/deploying-on-heroku) 。

如果您有任何与美杜莎相关的问题，请随时通过 [Discord](https://discord.gg/F87eGuwkTp) 联系美杜莎团队。

## 分享这篇文章