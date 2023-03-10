# 如何定制 WooCommerce 产品页面

> 原文：<https://www.sitepoint.com/customize-woocommerce-product-pages/>

在规划一个 WooCommerce 商店时，你将不得不回答许多对商店未来成功有严重影响的预先问题。商店的设置是一个严峻的挑战，因为一旦完成，改变商店的设置和设计总是很困难的。要定制 WooCommerce 产品页面，你必须考虑你可用的选项，我将在这篇文章中讨论。

现在，一旦商店开张，下一个挑战就是产品页面上的混乱。WooCommerce 的单品页面有很多元素，对商店的定制设计和设置没有直接帮助。两个常见的罪魁祸首是产品类别和星级。并不是每个商店都需要这两个元素出现在单个产品页面上。同样，其他元素也需要重新定位，以适应商店的定制设计。

所有这些挑战都可以通过 WooCommerce 行动和过滤挂钩轻松解决。我创建了这个简短的列表来演示您可以在产品页面中定制的内容。

## 定制 WooCommerce 产品页面

您可能希望做的第一件事是在不同于默认模板的模板中显示产品。应该加载文件`single-product.php`，它控制在前端显示产品信息的模板文件。

在定制父主题和插件的模板页面时，通常推荐的做法是在主题中复制一份模板。现在只在副本中进行所有更改。这样，如果主题和插件被更新，你的自定义更改将不会受到影响。

几个插件和主题提供了大量的自定义动作和过滤挂钩，允许直接修改主题文件。所有这一切的好处是，您不必仔细检查和修改模板文件的标记。结果是更干净的代码，没有杂乱的文件副本。

我将使用`single-product.php`来调用模板文件，这些模板文件控制产品信息在产品页面上的显示方式和格式。类似地，`content-single-product.php`是产品模板，它被修改以改变产品页面的信息和样式。
现在打开`single_template`并添加以下代码来更改单个产品页面的模板:

```
function get_custom_post_type_template($single_template) {
 global $post;

 if ($post->post_type == 'product') {
      $single_template = dirname( __FILE__ ) . '/single-template.php';
 }
 return $single_template;
}
add_filter( 'single_template', 'get_custom_post_type_template' );

as well as following code include in template _include
add_filter( 'template_include', 'portfolio_page_template', 99 );

function portfolio_page_template( $template ) {

    if ( is_page( 'slug' )  ) {
        $new_template = locate_template( array( 'single-template.php' ) );
        if ( '' != $new_template ) {
            return $new_template ;
        }
    }

    return $template;
} 
```

## 构建定制的 WooCommerce 产品/类别

在这一点上，我假设你有一个活的 WooCommerce 商店，并且非常熟悉建立产品页面和产品类别的过程。总有这样的情况，你需要一个定制的产品页面或定制的产品类别页面。这是通过调整这些模板的自定义模板来完成的。

## 调整 WooCommerce 模板文件

现在请记住，在使用`content-single-product.php`文件时，请修改它，以便您添加的任何产品或类别都应该出现在文件的末尾。另一个好的做法是给它起一个名字，让它与众不同，易于识别。例如，如果您有一个名为“Books”的类别，将文件的名称更改为`content-single-product-books.php`。这个简单的更改将确保您可以立即识别特定类别的正确文件。这个文件用来做各种各样的改变(添加或删除一个侧边栏，改变循环等等。)以确保产品或产品类别页面完全符合您的要求。

下一步是修改 single-product.php 的档案。该文件包含一个循环，该循环决定加载哪些模板来显示产品。

我将添加一个 if 条件，检查产品是否属于某个特定的类别，然后在自定义模板中加载相关的`single-product.php`。现在，没有必要重命名该文件。为了向其中添加代码，请打开该文件并找到以下代码片段:

```
woocommerce_get_template_part( 'content', 'single-product' );
```

您将希望用以下代码替换它

```
 global $post;
$terms = wp_get_post_terms( $post->ID, 'product_cat' );
foreach ( $terms as $term ) $categories[] = $term->slug;

if ( in_array( 'YOURCATEGORY', $categories ) ) {
    woocommerce_get_template_part( 'content', 'single-product-YOURCATEGORY' );
} else {
    woocommerce_get_template_part( 'content', 'single-product' );
}
```

在代码中，找到`YOURCATEGORY`，并用您选择的类别 slug 替换它。此外，您需要用文件的新名称替换 content-single-product.php。使用上面使用的例子，其中类别 slug 是“书籍”，而`content-single-product.php`被重命名为`content-single-product-books.php`。此时，代码看起来像这样:

```
global $post;
$terms = wp_get_post_terms( $post->ID, 'product_cat' );
foreach ( $terms as $term ) $categories[] = $term->slug;
if ( in_array( 'books', $categories ) ) {
    woocommerce_get_template_part( 'content', 'single-product-books' );
} else {
    woocommerce_get_template_part( 'content', 'single-product' );
} 
```

此时，所有文件都已成功编辑和/或重命名。下一步是将这些文件上传到 WooCommerce 商店。为了确保一切顺利进行，确保在主题目录中有一个名为`/woocommerce/`的子文件夹。请记住，这两个文件(`content-single-product.php`和`single-product.php`)将放在同一个文件夹中。在主题的这个文件夹中修改代码元素是一个很好的实践，这样你就可以防止覆盖原始 WooCommerce 文件的难以调试的错误。

最后一步是检查 product 和 product category 页面，以确保在定制模板的代码中所做的所有更改都已被应用，并且完美地显示出来。

## 结论

学习定制 WooCommerce 产品页面和产品类别页面就是添加和修改钩子的事情。了解所有这些变化对这两个页面在商店前端的呈现方式有直接影响，这一点很重要。如果您在代码或想法的任何其他方面需要任何帮助，请留下评论，我会尽快回复您。

## 分享这篇文章