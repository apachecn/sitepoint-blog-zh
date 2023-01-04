# 在 WordPress 中使用 Shopify API

> 原文：<https://www.sitepoint.com/using-the-shopify-api-with-wordpress/>

WordPress 非常受小企业欢迎，但越来越多的产品企业也在使用 Shopify 开辟电子商务商店。可以保留一个现有的信息站点，并使用带有自定义页面模板的 Shopify API，将来自 Shopify 的产品提要导入 WordPress 站点。

在[这个演示页面](http://zoerooney.com/demos/shopify-featured-collection/)上有一个产品的工作提要，它从[拉一个演示 Shopify 商店](http://just-a-demo.myshopify.com/)，最终结果如下所示:

![Embedded Shopify Collection Screenshot](img/d203b221898f39d6a349b20bf0ef6f68.png)

我们开始吧！

## 在 Shopify 端设置

为了设置与 Shopify 的集成，您需要将一个集合放入您的 WordPress 页面，并设置一个私有应用程序，以便您可以调用 Shopify API。

### 识别您的收藏

虽然您可能习惯使用 Shopify 模板中的收藏标题或句柄，但出于集成目的，主要标识符是收藏 ID 号。您可以通过在 Shopify admin 中编辑集合并查看 URL 的末尾来找到给定集合的 ID 号:

![Shopify Collection ID Number](img/be224b515b39d2e8606de067fca248c1.png)

确保你选择的收藏中有一些产品，并记下 ID 号，以便在你的 WordPress 网站上使用。

### 创建私人应用程序

当你在 Shopify 的管理中时，你需要创建一个私人应用程序。这就是你通过 Shopify API 访问商店信息的原因。

要创建一个应用程序，在管理页面点击右上角的私人应用程序按钮。在下一页上，单击按钮创建私人应用程序。你需要给应用一个标题，你可以选择包括一个联系电子邮件地址(如果你不输入电子邮件，它将默认为你的用户的电子邮件地址)。

![Creating a Private App](img/d6971170959ab9ff9e3688c210967e76.png)

点击保存按钮，你的应用程序就可以运行了！点击私人应用列表中的应用标题，获取通过 API 连接所需的全套身份验证详细信息。最底部的示例 URL 具有正确的格式，因此这是一个很好的起点。

![Authentication Details](img/debc515d7c81d60f186aa26f605db9fd.png)

完整的示例 URL 是您用来通过 API 访问您的商店的订单信息的 URL，所以您只需要注意 URL 的开头。在本例中，您会注意到以下内容:

```
https://c6bd98e6ae2e0c6945208ec441340af0:2fea2ea43af949b6623e140a82cd482f@just-a-demo.myshopify.com 
```

请注意，出于帐户安全的考虑，这不是一个有效的 URL，所以请不要试图将它用于您自己的测试目的。

一旦你有了收藏 ID 和私人应用 URL，你就在 Shopify 端设置好了！

## WordPress 设置

大部分集成发生在 WordPress 端，您将创建一个自定义模板来显示您选择的收藏。首先，确保你在一个[子主题](https://www.sitepoint.com/build-your-own-wordpress-themes-the-easy-way/)或者一个完全自定义的主题中工作，这个主题不会在更新时被覆盖。

### 创建新的页面模板

对于这个演示，我们将使用我们的收藏提要创建一个定制的页面模板，所以第一步是在主题中创建一个文件名为`page-shopify.php`的新文件。该页面的初始内容应该如下所示:

```
<?php
/**
* Template Name: Shopify Collection Demo
*
*/

get_header(); ?>

<div id="primary" class="full-width">

<?php while ( have_posts() ) : the_post(); ?>

<article id="post-<?php the_ID(); ?>" <?php post_class(); ?>>
<header class="entry-header">
<h1 class="page-title"><?php the_title(); ?></h1>
</header><!-- .entry-header -->

<div class="entry-content">
<?php the_content(); ?>
</div><!-- .entry-content -->

</article>

<?php endwhile; ?>

</div><!-- #primary -->

<?php get_footer(); ?>
```

您可能希望您的 HTML 结构基于模板中的现有页面，因为您的模板可能不会使用与上述示例相同的类、id 或布局 HTML。我在模板中保留了页面标题和正文内容，这样我就可以在产品网格上方放置导入文本。

保存新文件，如果适用，将其上传到开发服务器。

### 创建页面并分配模板

上面的自定义模板例子中最重要的特征是第一个评论块中的模板名称——这允许你在你的 WordPress admin 中将这个模板分配给一个页面。

这就是你接下来要做的——在 WordPress 中创建新页面，并将你的新模板分配给该页面:

![Assigning the Shopify Template to Our Page](img/f2010b4ecbf11de515f1d7cf4d524579.png)

此时，您将能够查看应用了自定义模板的新页面，但不会有太多内容可看，因为我们还没有添加任何集成代码。

### 可选:为 Shopify 数据创建自定义字段

虽然您不一定要完成下一步，但我喜欢为 Shopify 数据使用定制字段，比如集合 ID 和 API 认证 URL。与将数据硬编码到模板中相比，自定义字段让我可以在将来需要时轻松地更改这些信息。虽然 API URL 可能不会改变，但您可能会决定采用不同的集合，而且不必编辑模板就能做出这种改变，这很好。

我使用[高级定制字段(ACF)插件](http://www.advancedcustomfields.com/)来做这类事情，因为它免费、快速、简单，并且为定制字段创建了一个漂亮的界面。您需要设置三个字段:

![Custom fields setup using Advanced Custom Fields](img/6d297e64664631e76a45a7fcbda4d3fd.png)

设置好这些字段并将其应用到自定义页面模板后，您将在编辑新页面时看到它们，并且可以使用它们来输入您之前从您的 Shopify 帐户收集的数据:

![Shopify Data](img/c14df98d27ce5cd7be7b14497c1bd4e0.png)

## 嵌入收藏

现在两个站点的设置都完成了，是时候进入大量的定制代码，通过 Shopify API 来获取您的收藏了。作为参考，你可以在本 GitHub Gist 的[中访问我的演示模板中使用的完整代码。](https://gist.github.com/zoerooney/8969dfb91947e5327481)

我首先在我的常规`entry-content`区域下创建了一个新的 div 来保存我的提要:

```
<div class="row product-feed"></div>
```

### 分配基本变量

首先，我们将创建几个 PHP 变量来保存 Shopify 信息。

#### 使用高级自定义字段

如果您使用高级自定义字段，您的变量设置如下所示:

```
// Set variables
$api_url = get_field( 'shopify_app_api_url' );
$shop_url = get_field( 'shopify_site_url' );
$collection_id = get_field( 'collection_id' );
```

同样，这里的好处是你可以在你的 WordPress admin 中改变这些值，而不需要接触你的模板文件。

#### 硬编码您的价值观

如果您没有使用自定义字段，您仍然可以设置相同的变量，但是您将对值进行硬编码，如下所示:

```
// Set variables
$api_url = 'https://c6bd98e6ae2e0c6945208ec441340af0:2fea2ea43af949b6623e140a82cd482f@just-a-demo.myshopify.com';
$shop_url = 'http://just-a-demo.myshopify.com';
$collection_id = '26542309';
```

从现在开始，我们将在代码中使用这些变量，所以您使用哪种方法并不重要。

### 访问 API 数据

Shopify API 有很好的文档记录，并且有各种各样的访问点。在本教程中，我们将使用其中的两个接入点，[收集](http://docs.shopify.com/api/collect)和[产品](http://docs.shopify.com/api/product)。

#### 获取产品列表

收藏是一种有趣的单元，因为它不是您在 Shopify admin 中与之交互的东西，而是保存关于哪些产品在自定义收藏中的信息的对象。collect 将输出集合中产品的数组，包括每个产品的 ID

因此，我们将从 API 中提取的第一个文件是特色集合的收集数据，我们将通过设置一个相当于收集 JSON 文件的 URL 的变量来完成:

```
// Create the API URL for the Shopify collect
$collects_url = $api_url . '/admin/collects.json?collection_id=' . $collection_id . '&limit=3';
```

这个 URL 的完整格式是`API URL` + `/admin/collects.json?collection_id=` + `COLLECTION ID`，后跟可选参数。我们将变量用于 API URL 和集合 ID，还添加了一个参数来限制返回的产品数量为 3(如果您想返回更多或更少的产品，可以编辑 URL 上的最后一位)。

#### 获取文件内容(通过缓存)

现在我们已经有了那个 URL，我们想要得到它指向的文件，但是为了防止触及 Shopify 的 API 限制(并且提高性能)，我们可以使用一个 [WordPress transient](http://codex.wordpress.org/Transients_API) 来临时缓存文件。

文件中的下一段代码检查名为`shopify_product_feed`的瞬态。如果没有找到，它从 Shopify 获取文件，并设置 transient 将数据存储 4 小时:

```
// Use a transient for the feed URL (performance boost)
if ( false === ( $collects_content = get_transient( 'shopify_product_feed' ) ) ) {
$collects_content = @file_get_contents( $collects_url );

// Put the results in a transient. Expire after 4 hours.
set_transient( 'shopify_product_feed', $collects_content, 4 * HOUR_IN_SECONDS );
}

// Decode the JSON in the file
$collects = json_decode( $collects_content, true );
```

这种瞬变的唯一风险是，你的 feed 可能有点落后于你的收藏——如果你要在 Shopify 上更改你的收藏中的产品，这种更改可能会在长达四个小时内(或者无论你设置瞬变多长时间)不会出现在 WordPress 上。在大多数情况下，对于性能收益来说，这是可以接受的风险。

注意在`@file_get_contents`的开头有一个`@`符号。如果在获取文件内容时出现问题，这可以抑制 PHP 错误，如果达到 API 限制，就会出现这种情况。您可能希望在测试实现时删除它，因为您可能希望这些错误一直出现，直到一切都正常工作。

上面代码片段中的最后一行对文件中的 JSON 数据进行解码，并将其存储在一个数组中。

#### 循环通过收集

现在我们已经获得了用于收集的产品数据的初始数组，我们将遍历它并创建一组变量，稍后我们将使用这些变量在模板中显示数据。这个循环开始于:

```
// Reset variant inventory count
$variant_inventory = 0;

// Loop through products in the collection
for ( $prod = 0; $prod < 3; $prod++ ) {

// Get the product ID for the current product
$product_id = $collects['collects'][$prod]['product_id'];
```

第一行将库存计数器设置为 0——这是我们希望在每个循环实例之前做的事情，以便我们能够独立地检查每个产品的库存。

接下来，我们开始一个`for`循环。如果您已经更改了您的产品编号(通过更改收集 URL 中的限额参数)，您还需要更新该行中的限额(`$prod < 3`)。

在循环中，我们首先获取当前产品的产品 ID，并将其赋给变量`$product_id`。

#### 通过 API 访问产品数据

一旦我们获得了产品 ID，我们就可以返回到 Shopify API 来获取我们将为该产品显示的所有数据。此 API 调用的格式为:

```
// Get the product data from the API (using the ID)
$product_url = $api_url . '/admin/products/' . $product_id . '.json?fields=images,title,variants,handle';

// Decode the JSON for the product data
$product_json = json_decode( @file_get_contents( $product_url ), true );
```

就像 collect 一样，我们将产品数据的 URL 放在一起，但是这一次我们将插入刚刚创建的`$product_id`变量来获取特定产品的数据。URL 末尾的参数允许我们限制 API 返回哪些数据——在本例中，我们只获取产品标题、句柄、图像和变量数据。

虽然缓存收集的数据是个好主意，但我建议不要缓存产品数据(或者如果确实要缓存，设置一个较短的到期时间)。这是因为产品数据(如可用性)更有可能在任何给定时间发生变化，并且您可能希望您的提要反映最新的价格和可用性数据。

因此，这个文件没有瞬态，而是直接访问产品数据，并将产品信息的结果数组赋给变量`$product_json`。

#### 设置产品数据变量

我们将为每个产品显示的前几条数据很容易从阵列中获得:

```
// Set some variables for product data
$current_product = $product_json['product'];
$product_handle = $current_product['handle'];
$product_title = $current_product['title'];
```

该代码片段为当前产品设置一个变量，然后将产品句柄和标题分配给一组变量。

接下来，我们将获得产品图像源。这有点棘手，因为 Shopify 发送原始(全尺寸)图像的 URL，通常比您想要显示的要大得多。我们可以通过使用 Shopify 的文件命名约定修改图像源来获得较小的图像大小，从而避免提供不必要的巨大图像:

```
// Get the product image and modify the file name to get the large size thumbnail
$image_src_parts = pathinfo( $current_product['images'][0]['src'] );
$product_image_src = $image_src_parts['dirname'] . '/' . $image_src_parts['filename'] . '_large.' . $image_src_parts['extension'];
```

在这个片段中，我们使用 PHP 的 [pathinfo](http://php.net/manual/en/function.pathinfo.php) 来分解图像源，然后我们重新构建它并添加`_large`字符串来指定返回哪个大小的缩略图。这依赖于 Shopify 对[图像大小](http://docs.shopify.com/themes/liquid-documentation/filters/url-filters#product_img_url)的文件命名约定，但这些约定一直保持一致，所以我觉得这是一个安全的举动。

我们将显示的每种产品的最后一组信息是其可用性和价格。我们将使用库存来确定一个产品是否应该显示为售完，并且我们将获得关于变体和定价的信息来确定是显示单个价格还是一个范围。

```
// Get product variant information, including inventory and pricing
$variants = $current_product['variants'];
$variant_count = count( $variants );

$variant_price = 0;
$variant_prices = array();
if ( $variant_count > 1 ) :
for ( $v = 0; $v < $variant_count; $v++ ) {
$variant_inventory += $variants[$v]['inventory_quantity'];
$variant_prices[] = $variants[$v]['price'];
}
$price_min = min( $variant_prices );
$price_max = max( $variant_prices );
else :
$variant_price = $variants[0]['price'];
$variant_inventory = $variants[0]['inventory_quantity'];
endif;

?>
```

变量是它们自己的数组，所以这个代码片段将数组赋给一个变量，然后计算存在多少个变量。然后，它将价格设置为零，作为我们每次循环不同产品的重置。

如果有一个以上的变量，我们循环遍历这些变量，将总库存数加起来，同时为所有变量创建一个价格数组。从该数组中，我们计算出最低价格和最高价格，然后将它们显示为产品标题下的一个范围。

如果只有一个变体，我们将获得该变体的价格和库存。

### 展示每个产品

现在我们有了所有的数据，我们将显示设置为变量，我们可以设置显示。

#### 完全加价

```
<div class="product-feed-item">
<a href="<?php echo $shop_url; ?>/products/<?php echo $product_handle; ?>">
<img src="<?php echo $product_image_src; ?>" alt="<?php echo $product_title; ?>"/>
<h3><?php echo $product_title; ?></h3>
<?php if ( $variant_inventory > 0 ) : ?>
<?php if ( $variant_price > 0 ) : ?>
<span class="price small"><?php if ( $variant_price > 0 ) : ?>$<?php echo $variant_price; ?><?php else : ?>FREE<?php endif; ?></span>
<?php elseif ( ( $price_min > 0 ) && ( $price_max > 0 ) ) : ?>
<span class="price small">$<?php echo $price_min; ?> - $<?php echo $price_max; ?></span>
<?php endif; ?>
<?php else : ?>
<span class="sold-out">OUT OF STOCK</span>
<?php endif; ?>
</a>
</div>
```

出于样式目的，每个产品都包装在一个 div 中，带有类“product-feed-item ”,然后将带有产品标题的图像作为 ALT 标记。产品的 URL 是通过将商店 URL 与产品句柄结合起来创建的。

在图像下方，产品标题显示在

### 元素。

#### 产品库存和价格

最后，还有一些条件来检查产品是否有货，是否有一个价格范围:

```
<?php if ( $variant_inventory > 0 ) : ?>
<?php if ( $variant_price > 0 ) : ?>
<span class="price small"><?php if ( $variant_price > 0 ) : ?>$<?php echo $variant_price; ?><?php else : ?>FREE<?php endif; ?></span>
<?php elseif ( ( $price_min > 0 ) && ( $price_max > 0 ) ) : ?>
<span class="price small">$<?php echo $price_min; ?> - $<?php echo $price_max; ?></span>
<?php endif; ?>
<?php else : ?>
<span class="sold-out">OUT OF STOCK</span>
<?php endif; ?>
```

如果产品库存大于零，则显示价格(如果合适，显示一个范围)，但如果小于零，则显示“缺货”字样代替价格。

示例文件中的最后几行结束了产品标记和产品循环。

#### 样式化输出

产品提要准备就绪后，剩下的唯一工作就是一些造型。我在我的演示中包含了一些基本的样式，只是为了给网格一些形状:

```
.product-feed-item {
display: inline-block;
vertical-align: top;
width: 31%;
margin-right: 2%;
text-align: center;
}
.product-feed-item img {
max-width: 100%;
height: auto;
}
```

## 包裹

除了在一个页面上展示一个收藏外，还有很多潜在的应用程序可以将 Shopify API 与 WordPress 集成在一起。我创建的网站已经将特色产品整合到每篇博文中，甚至允许你在 WordPress 中将产品添加到你的 Shopify 购物车中。集成一个集合是更熟悉 API 以及它在 WordPress 环境中如何工作的第一步，但是没有限制！

## 分享这篇文章