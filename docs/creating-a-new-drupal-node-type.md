# 创建新的 Drupal 节点类型

> 原文：<https://www.sitepoint.com/creating-a-new-drupal-node-type/>

Drupal 是一个非常灵活的网站内容管理系统。它提供了许多开箱即用的功能，使我们无需编写任何 PHP 代码就能构建全面的网站。它还提供了通过使用模块来添加更多功能的特性。

默认情况下，Drupal 节点类型是一篇文章和一个页面。这对于基本的网站来说可能是好的，但是如果我们计划有一个更复杂的网站，我们将会想要增加不同的节点类型。我们甚至可以通过编写一个模块来定义它们！

在本文中，您将看到如何编写一个定制的 Drupal 模块，该模块将使用 Drupal 钩子和 API 安装新的节点类型。

## 创建您的模块

让我们首先创建一个模块的基本结构，我们将在其中创建新的内容类型。

要创建一个模块，在 Drupal 安装的`sites/all/modules`目录中创建一个名为`productcustomtype`的新目录。在该文件夹中，然后创建三个文件:

*   `productcustomtype.info`–将包含关于模块的信息，以便 Drupal 能够识别它并显示在列表中。
*   `productcustomtype.install`–将提供您将要为该模块安装或卸载的内容的详细信息。
*   `productcustomtype.module`–将包含实现模块使用的 Drupal 挂钩的代码。

创建目录后，打开`productcustomtype.info`并添加以下代码:

```
name = productcustomtype
description = A new content type of Product
package = Product Custom node Type
core = 7.x
files[] = productcustomtype.install
files[] = productcustomtype.module
```

上面只是为我们的模块定义了一些参数，所以 Drupal 可以使用它并显示关于我们模块的信息。完成后，我们应该会在模块列表中看到我们的模块，如下所示。但是现在不要启用它，我们将在下一步之后启用它。

![drupal-01](img/0a4d7987cec1d4ce5d294db8e6089b1a.png)

## 安装和卸载节点

一旦模块的基本文件就绪，我们就可以开始编写安装定制节点类型的代码了。我们需要实现的第一个钩子是`hook_node_info`。使用这个钩子，一个模块可以在 Drupal 中定义一个或多个节点类型。钩子实现返回一个数组，定义模块将要添加的新节点类型。

为了实现这个钩子，将下面的代码添加到`productcustomtype.module`:

```
<?php
/**
 * Implements hook_node_info()
 */
function productcustomtype_node_info() {
    return array(
        'product' => array(
            'name' => t('Product'),
            'base' => 'product',
            'description' => t('You can define new Products here'),
            'has_title' => TRUE,
            'title_label' => t('Product title')
         )
    );
}
```

该实现返回一个数组，该数组定义了一个新的节点类型产品及其一些属性，如名称、描述(如果有标题的话)和基(用于该内容类型的不同钩子中)。

因为我们已经定义了这个节点类型有一个标题，所以当显示这个节点的表单来添加内容时，我们需要显示标题文本字段。为此，我们必须实现钩子`hook_form`。

`hook_form`钩子用来显示用来创建/编辑节点的表单。钩子在`productcustomtype.module`中实现如下:

```
<?php
/**
 * Implement hook_form()
 */
function product_form($node, $form_state) {
    return node_content_form($node, $form_state);
}
```

我们简单地使用 Drupal API，它给出了一个`hook_form`的实现，并添加了一个标题字段，前提是节点定义设置了`has_title`属性(在我们的例子中已经设置了)。

一旦我们完成了这些，我们需要实现`hook_install`钩子来将 body 字段添加到新的节点类型中。

将实现添加到`productcustomtype.install`中，如下所示:

```
<?php
/**
 * Implements hook_install().
 */
function productcustomtype_install() {
    node_types_rebuild();
    $types = node_type_get_types();|
      node_add_body_field($types['product']); 
}
```

我们首先通过调用 Drupal API `node_types_rebuild()`函数保存由不同模块(包括我们的模块)创建的所有新节点类型。然后我们得到所有的节点类型和我们类型上的`call node_add_body_field()`来添加主体字段。

一旦我们这样做了，我们可以启用我们的模块，这将安装我们的新节点类型。然后，当我们点击*添加内容*时，我们应该能够看到我们的新类型，如下所示:

![drupal-02](img/97fe4ff7346aabf02c3ba7d5ee8eaf98.png)

如果我们添加一个新产品，它也会出现在 Drupal 主页上。

![drupal-03](img/9f55320174bef955cad9d2fe9fe2513f.png)

当用户卸载数据库时，Drupal 中的所有模块都应该清理它们在数据库中创建的任何数据或它们添加的任何类型。为了支持这一点，我们必须在我们的`productcustomtype.install`文件中实现`hook_uninstall`，如下所示:

```
<?php
/**
 * Implements hook_uninstall().
 */
function productcustomtype_uninstall() {
    $ournewtype = 'product';
    $sql = 'SELECT nid FROM {node} n WHERE n.type = :type';
    $result = db_query($sql, array(':type' => $ournewtype));
    $nodeids = array();
    foreach ($result as $row) {
        $nodeids[] = $row->nid;
    }
    node_delete_multiple($nodeids);
    node_type_delete($ournewtype);
}
```

我们首先找出所有节点 id，它们是我们安装的内容类型的节点。一旦我们收集了 id，我们就使用 API 函数`node_delete_multiple()`来删除多个节点。然后我们使用`node_type_delete()`函数删除我们的节点类型。

现在，如果我们卸载我们的模块，我们类型的所有节点和我们类型本身都应该被删除。

## 创建额外字段

一旦我们添加了新的节点类型，我们就有了类似于节点的功能。但是如果我们想根据节点类型添加更多的字段呢？例如，如果它是一个产品类型，那么我们可能希望添加两个字段，如价格和数量。

为此，我们必须首先创建字段，然后在我们的节点类型上创建字段实例。我们在`productcustomtype.install`文件中编写了两个新函数来定义字段和字段实例。

```
<?php
function _productcustomtype_installed_fields() {
    $t = get_t();
    return array(
        'productcustomtype_price' => array(
            'field_name' => 'productcustomtype_price',
            'label' => $t('Price of the product'),
            'type' => 'text'
        ),
        'productcustomtype_quantity' => array(
            'field_name' => 'productcustomtype_quantity',
            'label' => $t('Quantity of the product'),
            'type' => 'text'
        )
    );
}

function _productcustomtype_installed_instances() {
    $t = get_t();
    return array(
        'productcustomtype_price' => array(
            'field_name' => 'productcustomtype_price',
            'type' => 'text',
            'label' => $t('Price of the product'),
            'widget' => array(
                'type' => 'text_textfield'
            ),
            'display' => array(
                'example_node_list' => array(
                    'label' => $t('Price of the product'),
                    'type' => 'text'
                )
            )
        ),
        'productcustomtype_quantity' => array(
            'field_name' => 'productcustomtype_quantity',
            'type' => 'text',
            'label' => $t('Quantity of the product'),
            'widget' => array(
                'type' => 'text_textfield'
            ),
            'display' => array(
                'example_node_list' => array(
                    'label' => $t('Quantity of the product'),
                    'type' => 'text'
                )
            )
        )
    );
}
```

然后我们在`productcustomtype.install`中创建一个函数`add_custom_fields()`，它将从数组中读取数据并创建字段和实例。

```
<?php
function add_custom_fields() {
    foreach (_productcustomtype_installed_fields() as $field) {
        field_create_field($field);
    }
    foreach (_productcustomtype_installed_instances() as $fieldinstance) {
        $fieldinstance['entity_type'] = 'node';
        $fieldinstance['bundle'] = 'product';
        print_r($fieldinstance);
        field_create_instance($fieldinstance);
    }
}
```

现在我们可以调用`productcustomtype_install()`中的`add_custom_fields()`函数，这样在安装模块时就安装了字段和字段实例。

```
<?php
function productcustomtype_install() {
    node_types_rebuild();
    $types = node_type_get_types();
    node_add_body_field($types['product']);
    add_custom_fields();
}
```

我们还需要更新我们的`productcustomtype_uninstall()`函数来删除字段和字段实例。

```
<?php
function productcustomtype_uninstall() {
    $ournewtype = 'product';
    $sql = 'SELECT nid FROM {node} n WHERE n.type = :type';
    $result = db_query($sql, array(':type' => $ournewtype));
    $nodeids = array();
    foreach ($result as $row) {
        $nodeids[] = $row->nid;
    }
    node_delete_multiple($nodeids);
    delete_custom_fields();
    node_type_delete($ournewtype);
    field_purge_batch(500);
}

function delete_custom_fields() {
    foreach (array_keys(_productcustomtype_installed_fields()) as $field) {
        field_delete_field($field);
    }
    $instances = field_info_instances('node', 'product');
    foreach ($instances as $instance_name => $fieldinstance) {
        field_delete_instance($fieldinstance);
    }
}
```

再次安装该模块，然后单击“product”添加新产品，我们应该会看到如下所示的两个字段:

![drupal-04](img/0b1f60717a9b6cc6015fa8ec75f60273.png)

保存节点并在预览中看到它后，我们将看到如下字段:

![drupal-05](img/816ed3c60d84be4e471b66799321969b.png)

## 结论

Drupal CMS 提供了许多有用的钩子和一个强大的 API，允许我们执行各种定制。在本文中，我们能够创建一个全新的节点类型，它将出现在 Drupal 的内容部分。我们还使用 Drupal 的 API 将字段附加到这个新的节点类型上，这样任何人都可以使用这个新的节点类型轻松地添加内容。所以，祝您在为 Drupal 站点创建下一个新节点类型时愉快。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章