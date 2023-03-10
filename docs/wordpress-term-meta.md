# WordPress 术语 Meta 和 WP_Term 介绍

> 原文：<https://www.sitepoint.com/wordpress-term-meta/>

在 WordPress 中，你可以很容易地为你的文章、页面和其他自定义内容类型保存元数据，然而保存元数据用于你的分类法曾经是一个过于复杂的过程[(我甚至在这里写过一篇关于它的文章！](https://www.sitepoint.com/extending-wordpress-taxonomies/))。

为了让这一切正常工作，您需要将您的术语元数据作为字段保存在您的`wp_options`表中，这意味着如果您有大量的术语或几个定制的分类法，您可能会有数百个甚至数千个额外的条目。

然而，自从 [WordPress 4.4 和](https://codex.wordpress.org/Version_4.4#For_Developers)之后，术语现在是对象，与文章、页面和自定义内容类型一样。这一改变使得添加、移除和更新元数据变得更加容易。

## 元术语的背景故事

早在 **WordPress 2.8** 的时候，社区就一直在推动一种简单的方法来控制术语元数据。这是一个缓慢的过程，但最终术语已经被从头开始重新设计，使用一个类结构。加上 WordPress 4.4 中一些不同的变化，这意味着分类法中的术语(如“标签”、“类别”或“自定义”)现在可以很容易地分配到它们自己的元。

### 旧方式的元数据操作

在 WordPress 4.4 之前，没有明确的方法可以方便地保存术语项的元数据，这是术语构造方式的固有限制。如果您要扩展分类法或术语，您必须使用 [`update_option`](https://codex.wordpress.org/Function_Reference/update_option) 将数据直接保存为站点选项。这并不理想(因为它把选项表弄得乱七八糟)。

[在](https://www.sitepoint.com/extending-wordpress-taxonomies/)之前，我已经写了关于扩展分类法的文章，但是它的基础是当你准备好保存你的元数据时，你可以调用一个看起来像这样的函数:

```
//saving new fields for category
function save_extra_taxonomy_fields($term_id){
    $term = get_term($term_id);
    $term_slug = $term->slug; 
    //collect category image id from posted values
    $term_category_image_id = isset($_POST['category_image_id']) ? sanitize_text_field($_POST['category_image_id']) : ''; 
    //update value and save it as an option
    update_option('category_image_id_' . $term_slug, $term_category_image_id);
}
add_action('create_category','save_extra_taxonomy_fields'); 
```

在上面的例子中，我们执行了附加到`create_category`钩子上的函数(当我们创建一个新的类别术语时触发该函数)。这将寻找我们的价值，消毒后将保存它作为一个选项。虽然这个工作，但它不是很漂亮。

## 添加、更新和删除术语元

要使用术语元，您将使用 [`add_term_meta`](https://developer.wordpress.org/reference/functions/add_term_meta/) 、 [`update_term_meta`](https://codex.wordpress.org/Function_Reference/update_term_meta) 和 [`delete_term_meta`](https://developer.wordpress.org/reference/functions/delete_term_meta/) 函数。这些函数在与新的 UI 元素结合使用时，将允许您保存和更新术语的新元数据。

### 添加术语元

为术语添加元数据涉及到`add_term_meta`函数。您需要指定三个参数和可选的第四个参数。

*   `$term_id`–您想要保存此元数据的术语的 ID
*   `$meta_key`–元数据的键名。这是您引用数据的方式
*   `$meta_value`–数据本身(记得整理)
*   `$unique`(可选)–如果元数据关键字应该是唯一的。默认情况下，这被设置为`false`，这意味着如果另一个键有相同的名称，该功能将覆盖它。将此项设置为`true`以确保唯一性。

作为一个例子，假设对于我们的`category`分类法中的每个术语，我们想要根据有多少文章被分配到这个类别来分配一个新的元数据。有了 WordPress 4.4，我们可以遍历所有的术语，并保存这些新的元数据(供以后在我们的主题或插件中使用)。

```
function add_featured_to_categories(){

    //get all terms from the category taxonomy
    $taxonomy_name = 'category';
    $term_args = array(
        'orderby'       => 'name',
        'hide_empty'    => false,
        'fields'        => 'ids'
    );

    $terms = get_terms($taxonomy_name, $term_args);

    if($terms){

        $term_key = 'term_size';
        $term_value = 'empty';
        $term_unique = true;

        //go through all terms and set the new term meta
        foreach($terms as $term_id){

            $term = get_term($term_id, $taxonomy_name);
            $term_count = $term->count; 

            //determine new meta value
            if($term_count > 10){
                $term_value = 'big';
            }else if($term_count >= 5 && $term_count < 10){
                $term_value = 'medium';
            }else if($term_count >= 1 && $term_count < 5){
                $term_value = 'small';
            }

            //save meta value
            add_term_meta($term_id, $term_key, $term_value, $term_unique);  
        }
    }
}
add_action('init', 'add_featured_to_categories'); 
```

### 阅读术语元

我们可以通过使用`get_term_meta`函数读取保存的术语元数据。这个函数的工作方式类似于用于从帖子中获取元数据的`get_post_meta`函数。使用该函数需要指定一个强制参数，可选的两个参数可用。

*   `$term_id`–从中提取元数据的术语的 ID
*   `$key`(可选)–要返回的单个指定键。如果未指定，则返回所有元数据。
*   `$single`(可选)–如果将返回单个值或一个键或值对。默认为单个值。

让我们看看另一个场景，您可能会发现这很有用。

考虑这样一种情况，我们已经为`category`分类法中的每个术语保存了术语元。这个保存的数据包含我们查看术语时应该显示的图像的 URL。我们希望将此图像显示为一个横幅，位于我们的术语描述或标题下方，但位于我们的帖子列表上方。

```
//given a term, collect its saved image to be displayed
function display_term_meta_image($term_id, $term_taxonomy){

    //get supplied term
    $term = get_term($term_id, $term_taxonomy); 
    if($term){

        $term_image_id = get_term_meta($term_id, 'term_image_id', true);
        if($term_image_id){
            //get the medium image size for display
            $term_image = wp_get_attachment_image_src($term_image_id, 'medium', false);
            echo '<img src="' . $term_image[0] . '" title="' . $term->name . ' image"/>';
        }  
    }
} 
```

现在，在我们的`category.php`或其他子主题模板文件中，我们可以修改显示术语数据的功能。

在我使用`Twenty Fourteen`的情况下，我正在编辑`category.php`文件，并在显示术语描述信息后立即调用我们的新函数。

```
//get the current object (term)
$term_obj = get_queried_object();
//display meta data image for term
if(function_exists('display_term_meta_image')){
    display_term_meta_image($term_obj->term_id, $term_obj->taxonomy);
} 
```

这将在描述下面显示我们的照片，如下所示:

![wp term meta banner](img/419eddd6ab72a18d93dd57efb3aae2dc.png)

### 删除术语元

我们可以像删除帖子一样删除术语元数据。当我们使用 [`delete_term_meta`](https://developer.wordpress.org/reference/functions/delete_term_meta/) 函数时，如果需要，我们需要提供两个强制参数和第三个选项。

*   `$term_id`–要处理的术语的 ID。
*   `$meta_key`–将从术语中删除的元键。
*   `$meta_value`(可选)–仅在值与此值匹配时删除元数据。如果您只想在数据与设定值匹配时删除该数据，请使用此选项。

让我们再来看一个你可能会用到它的场景。想象一下，在一个大项目进行到一半的时候，你已经为每个分类术语保存了一些元数据。您已经发现这些数据中有一些您不再需要了，因此您可能应该清除它们，以免它们弄乱您的数据库。

```
//removed metadata we no longer need for each category term
function delete_old_meta_data_from_category(){

    //get all category terms
    $terms = get_terms('category', array('hide_empty' => false, 'fields' => 'ids'));

    if($terms){
        //list of allowed term keys
        $allowed_term_keys = array('term_image_id', 'term_size');
        //go through all category terms
        foreach($terms as $term_id){
            $term_meta = get_term_meta($term_id); 
            //go through all meta for each term
            foreach($term_meta as $meta_key => $meta_value){
                //if this meta key doesn't exist in our allowed term keys, delete it
                if(!array_key_exists($meta_key, $allowed_term_keys)){
                    delete_term_meta($term_id, $meta_key);
                }
            }
        }
    }
}
add_action('init', 'delete_old_meta_data_from_category'); 
```

这个函数将遍历并删除我们没有在`$allowed_term_keys`变量中指定的任何额外的元数据，从而减少数据库中浪费的空间(当我们有许多不再需要的元数据条目时，这很有用)。

### 向后兼容 WordPress 4.3 和更旧版本

如果您真的热衷于使用这些新的元函数，但又想保护自己不受旧版本的影响，那么您可以创建一些条件功能来确保所有功能都能正常工作。

```
//add a piece of metadata for a term
function add_term_meta_compat($term_id, $term_key, $term_value){

    if(function_exists('add_term_meta')){ //WP4.4+
        add_term_meta($term_id, $term_key, $term_value, true);
    }else{ //Pre WP4.4
        //get term object and data
        $term_object = get_term($term_id);
        $term_taxonomy = $term_object->taxonomy;

        //build final key to save (tax + termid + key)
        $term_final_key = ($term_taxonomy . '_' . $term_id . '_' . $term_key);
        //ensure key isn't longer than 64 characters (max limit)
        $term_final_key = strlen($term_final_key > 64) ? substr($term_final_key, 0, 63) : $term_final_key;
        //add our new option
        add_option($term_final_key, $term_value);
    }
} 
```

我们首先调用`function_exists`来确保新的`add_term_meta`函数被定义。这只适用于 WordPress 4.4 和更新的版本。如果我们有支持，我们使用简单的`add_term_meta`函数将元数据分配给我们的术语。

如果我们没有支持，我们获取 term 对象本身(通过传入的 term ID ),并从中提取`$term_taxonomy`数据，用它来构建我们的最终键值。因为我们要保存到选项表中，所以我们需要确保关键字是惟一的，我们通过将分类名称、术语 ID 和术语关键字添加到一个变量中来实现这一点。我们必须确保密钥长度不超过 64 个字符，如果是这样的话，请将其删减。一旦我们完成了所有这些，我们就可以调用我们的`add_option`函数来保存我们的值。

正如你所看到的，这有点长，但是你可以得到更多的灵活性来支持旧的和新的 WordPress 版本。

## 把一切都包起来

使用这些新的元函数应该使您能够更容易地扩展您的术语来提供独特的功能。例如，您可能希望在术语的顶部添加一个横幅图像或提供元数据，以便有条件地以不同方式显示术语(例如根据显示的术语加载新的模板文件)。

凭借新术语元函数的灵活性和易用性，您可以从今天开始在您的新项目中实现它！

## 分享这篇文章