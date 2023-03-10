# WooCommerce 操作和过滤器来操纵购物车

> 原文：<https://www.sitepoint.com/woocommerce-actions-and-filters-manipulate-cart/>

欢迎阅读掌握 WooCommerce 动作和过滤器系列的第二篇文章。[在上一篇文章](https://www.sitepoint.com/mastering-woocommerce-actions-and-filters/)中，尽管非常简单，我们还是介绍了如何处理客户的账单和送货地址，以及当客户通过 WooCommerce 注册并将其发送到 Salesforce 等第三方网站时会发生什么。

在这第二篇文章中，我们将用一些巧妙的方式操纵购物车，这些方式可能是你在使用 WooCommerce 创建电子商务网站时遇到的真实场景。

## 以编程方式将产品添加到购物车

以编程方式将产品添加到购物车只需要一行代码。你唯一要做的头脑风暴就是决定你什么时候或者为什么要做这件事。在本文的第三部分中，当我们处理真实世界的场景时，我们将对此进行更多的讨论。

将产品添加到购物车所需的全部操作如下:

```
<?php
// Takes the Product ID and the Quantity
WC()->cart->add_to_cart( 73, 1 );
```

需要注意的是，确保不要在每页都运行的动作上运行这个，比如`template_redirect`动作，否则你会在每次页面加载或重新加载时向购物车中添加这些产品中的一个。尽可能避免这样做:

```
<?php
// template_redirect runs once for every page so you'll be
// increasing the quantity by one on every page load
add_action( 'template_redirect', 'add_random_product' );
function add_random_product() {
    WC()->cart->add_to_cart( 73, 1 );
}
```

## 以编程方式从购物车中移除产品

我看到这个问题在各种论坛和网站上被问了无数次，却很少有答案。希望这将有助于你下次想从购物车中删除产品，同样，你唯一要做的头脑风暴是什么时候或为什么你想从购物车中删除产品。下面的代码将阻止任何人从你的商店中结帐。我不知道您为什么要这样做，但它将演示从购物车中移除产品的步骤，这不像上一个示例中我们将产品添加到购物车时那样简单。

```
<?php
add_action( 'template_redirect', 'remove_product_from_cart' );
function remove_product_from_cart() {
    // Run only in the Cart or Checkout Page
    if( is_cart() || is_checkout() ) {
        // Set the product ID to remove
        $prod_to_remove = 56;

        // Cycle through each product in the cart
        foreach( WC()->cart->cart_contents as $prod_in_cart ) {
            // Get the Variation or Product ID
            $prod_id = ( isset( $prod_in_cart['variation_id'] ) && $prod_in_cart['variation_id'] != 0 ) ? $prod_in_cart['variation_id'] : $prod_in_cart['product_id'];

            // Check to see if IDs match
            if( $prod_to_remove == $prod_id ) {
                // Get it's unique ID within the Cart
                $prod_unique_id = WC()->cart->generate_cart_id( $prod_id );
                // Remove it from the cart by un-setting it
                unset( WC()->cart->cart_contents[$prod_unique_id] );
            }
        }

    }
}
```

## 通过编程清空购物车

为了更好地说明如何以编程方式清空购物车，让我们向购物车添加一个按钮，允许客户单击它并清空购物车。

![WooCommerce empty cart button](img/72a9765ef4c54e1ed4310a04a9973f90.png)

让我们使用`woocommerce_proceed_to_checkout`动作来响应我们自己的“提交”按钮，该按钮将为当前客户清空购物车。

```
<?php
add_action( 'woocommerce_proceed_to_checkout', 'insert_empty_cart_button' );
function insert_empty_cart_button() {
    // Echo our Empty Cart button
    echo '<input type="submit" class="button" name="empty_cart" value="Empty Cart" />';
}
```

下一步是监听按钮被点击的情况，这样当它被点击时，我们就可以清空购物车。为此，我们将挂钩到`template_redirect`动作。

```
<?php
// Let's wait for the button to be clicked on
add_action( 'template_redirect', 'empty_cart_button_handler' );
function empty_cart_button_handler() {
    if( isset( $_POST['empty_cart'] ) && $_SERVER['REQUEST_METHOD'] == "POST" ) {
        WC()->cart->empty_cart( true );
    }
}
```

你会注意到，按下按钮后，显示的是`cart-empty.php`而不是常规模板。

![WooCommerce emptied cart](img/be14c2c23c1a5d99ac497759bc00f2d0.png)

既然我们已经建立了如何从购物车中添加或删除产品，甚至完全清空购物车，那么让我们继续构建我们的现实世界场景，在这个场景中了解这种东西会有很大的不同。

## 激励产品

在我们的真实世界场景中，我们将通过构建一个系统来实现这一切，在这个系统中，您可以向所有客户赠送产品作为奖励。嗯，不完全是对你的所有客户，只是那些符合特定要求的人。

### 问题是

我们需要能够分发您选择的产品，作为对您客户的激励。

### 解决方案

建立一个系统，允许您根据以下内容分发奖励产品:

*   购物车中有特定的产品

*   您的订单有最低总金额吗

*   在你的购物车里有一个最小重量

*   拥有特定类别的产品

因为我们将以正确的方式构建，所以您不仅能够为符合这些标准之一的客户提供产品，而且还能够将这些标准混合起来，真正缩小谁得到产品、谁没有得到产品的范围。

你不仅可以通过符合这些标准中的一个来为你的客户提供激励产品，你还可以将它们结合起来。换句话说，例如，您将能够测试购物车中至少有 100 美元和“服装”类别产品的人。

让我们快速看一下我们马上要写的函数，以及每个函数在我们的问题/解决方案场景中的作用。

*   `get_id_from_product( $product, $check_variations = true )`–获取产品 ID 并返回。将变化 ID 考虑在内，因此我们在检查实际产品 ID 之前先检查这些变化 ID。

*   `qualifies_basedon_specific_product( $product_required )`–通过将指定的产品 ID 作为购物车中的商品之一，检查客户是否有资格获得奖励。

*   `qualifies_basedon_weight( $weight_required )`–检查顾客是否有资格获得购物车中的最低重量奖励。

*   `qualifies_basedon_cart_total( $total_required )`–检查客户是否有资格获得税前最低总额奖励。

*   `qualifies_basedon_product_category( $category )`–检查客户是否有资格通过在购物车中放置某个类别的产品来获得奖励。

*   `add_incentive_to_cart( $product_id )`–如果客户符合条件，则将奖励产品添加到购物车中

*   `remove_incentive_from_cart( $product_id )`–如果客户没有资格购买产品，则将奖励产品移至购物车。

*   `qualifies_for_incentive()`–这是奇迹发生的地方，因为它有需要匹配的规则，以便客户有资格获得激励。这个函数将处理我们的激励计划的逻辑。

```
<?php
/**
 * Will extract the Variation ID if available otherwise it will get the Product ID
 * @param $product Product
 * @param bool $check_variations Whether or not to check for variation IDs
 * @return mixed
 */
function get_id_from_product( $product, $check_variations = true ) {
    // Are we taking variations into account?
    if( $check_variations ) {
        // Ternary Operator
        // http://php.net/manual/en/language.operators.comparison.php
        return ( isset( $product['variation_id'] )
            && ! empty( $product['variation_id'])
            && $product['variation_id'] != 0 )
            ? $product['variation_id']
            : $product['product_id'];
    } else {
        // No variations, just need the product IDs
        return $product['product_id'];
    }
}

/**
 * Checks the existence of a specific product in the cart
 * @param $product_required The Product ID required to be in the cart
 * @return bool
 */
function qualifies_basedon_specific_product( $product_required ) {
    /*
     * We only want to run this on the cart or checkout page
     */
    if( is_cart() || is_checkout () ) {
        foreach( WC()->cart->cart_contents as $key => $product_in_cart ) {
            if( $product_required == get_id_from_product( $product_in_cart ) ) {
                return true;
            }
        }
        // Return false in case anything fails
        return false;
    }
}

/**
 * Checks the cart for the weight required to qualify for the incentive
 * @param $weight_required Weight Required
 * @return bool
 */
function qualifies_basedon_weight( $weight_required ) {

    /*
     * We only want to run this on the cart or checkout page
     */
    if( is_cart() || is_checkout () ) {
        if( $weight_required >= WC()->cart->cart_contents_weight ) {
            return true;
        }
    }
    // Return false in case anything fails
    return false;
}

/**
 * Checks the cart for the Total excluding taxes
 * @param $total_required
 * @return bool
 */
function qualifies_basedon_cart_total( $total_required ) {
    /*
     * We only want to run this on the cart or checkout page
     */
    if( is_cart() || is_checkout () ) {
        if( WC()->cart->subtotal_ex_tax >= $total_required ) {
            return true;
        }
    }
    // Return false in case anything fails
    return false;
}

/**
 * Checks the cart to verify whether or not a product from a Category is in the cart
 * @param $category Accepts the Product Category Name, ID, Slug or array of them
 * @return bool
 */
function qualifies_basedon_product_category( $category ) {
    foreach( WC()->cart->cart_contents as $key => $product_in_cart ) {
        if( has_term( $category, 'product_cat', get_id_from_product( $product_in_cart, false ) ) ) {
            return true;
        }
    }
    // Return false in case anything fails
    return false;
}

/**
 * Adds a specific product to the cart
 * @param $product_id Product to be added to the cart
 */
function add_incentive_to_cart( $product_id ) {
    // Check the cart for this product
    $cart_id = WC()->cart->generate_cart_id( $product_id );
    $prod_in_cart = WC()->cart->find_product_in_cart( $cart_id );
    // Add the product only if it's not in the cart already
    if( ! $prod_in_cart ) {
        WC()->cart->add_to_cart( $product_id );
    }
}

/**
 * Removes a specific product from the cart
 * @param $product_id Product ID to be removed from the cart
 */
function remove_incentive_from_cart( $product_id ) {
     $prod_unique_id = WC()->cart->generate_cart_id( $product_id );
    // Remove it from the cart by un-setting it
    unset( WC()->cart->cart_contents[$prod_unique_id] );
}
```

如你所见，这些函数返回“真”或“假”,所以我们可以很容易地混合起来，创建一个非常灵活的激励计划。现在剩下要做的是想出你想要为你的客户设定的规则，以使他们有资格获得激励产品，并编写将与`woocommerce_check_cart_items` WooCommerce 动作相关联的`qualifies_for_incentive()`函数。

```
<?php
/**
 * Checks whether or not the customer qualifies for the incentive
 */
add_action( 'woocommerce_check_cart_items', 'qualifies_for_incentive' );
function qualifies_for_incentive() {
}
```

下面是一些例子，说明如何使用这些功能来创造一些真正独特的东西。

#### 只有一个要求

这里有几个只设置**一个**需求的例子。

##### 购物车中存在的特定产品

```
<?php
// Specific product existing in the cart
function qualifies_for_incentive() {
    // Incentive product we are giving away
    $incentive_product_id = 102;

    if( qualifies_basedon_specific_product( 70 ) ) {
        add_incentive_to_cart( $incentive_product_id );
    } else {
        remove_incentive_from_cart( $incentive_product_id );
    }
}
```

##### 购物车中所有产品的最小重量

```
<?php
// Minimum weight of all the products in the cart
function qualifies_for_incentive() {
    // Incentive product we are giving away
    $incentive_product_id = 102;

    if( qualifies_basedon_weight( 10 ) ) {
        add_incentive_to_cart( $incentive_product_id );
    } else {
        remove_incentive_from_cart( $incentive_product_id );
    }
}
```

##### 不含税的购物车总额

```
<?php
// Cart's Total Excluding Taxes
function qualifies_for_incentive() {
    // Incentive product we are giving away
    $incentive_product_id = 102;

    if( qualifies_basedon_cart_total( 199 ) ) {
        add_incentive_to_cart( $incentive_product_id );
    } else {
        remove_incentive_from_cart( $incentive_product_id );
    }
}
```

##### 购物车中某个类别的产品

```
<?php
// Product From A Category In The Cart
function qualifies_for_incentive() {
    // Incentive product we are giving away
    $incentive_product_id = 102;

    if( qualifies_basedon_product_category( 'premium-quality' ) ) {
        add_incentive_to_cart( $incentive_product_id );
    } else {
        remove_incentive_from_cart( $incentive_product_id );
    }
}
```

## 混合起来

由于我们有一个非常灵活的代码库，您可以混合它，真正使您的激励计划独一无二。下面是更多的例子，展示了根据需要添加更多条件是多么容易。

##### 购物车中某个类别的产品或最小购物车总数

```
<?php
// Product From A Category In The Cart OR Minimum Cart Total
function qualifies_for_incentive() {
    // Incentive product we are giving away
    $incentive_product_id = 102;

    if( qualifies_basedon_product_category( 'premium-quality' )
        || qualifies_basedon_cart_total( 199 ) ) {
        add_incentive_to_cart( $incentive_product_id );
    } else {
        remove_incentive_from_cart( $incentive_product_id );
    }
}
```

##### 类别中的产品和最小购物车总数

```
<?php
// Product From A Category AND Minimum Cart Total
function qualifies_for_incentive() {
    // Incentive product we are giving away
    $incentive_product_id = 102;

    if( qualifies_basedon_product_category( 'premium-quality' )
        && qualifies_basedon_cart_total( 199 ) ) {
        add_incentive_to_cart( $incentive_product_id );
    } else {
        remove_incentive_from_cart( $incentive_product_id );
    }
}
```

你甚至可以得到更高级的东西，创造更复杂的场景。你的下一步是把它变成一个“类”,这样你就可以有一个以上的激励计划，每个计划都有自己独特的资格规则。

本文到此为止。在本系列的第三部分中，我们将使用在新产品/编辑产品屏幕上运行的操作和过滤器。然后，我们将探索如何只使用 API 向“产品屏幕”添加自定义字段。

## 分享这篇文章