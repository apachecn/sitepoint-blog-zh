# 在你的管理区域扩展文章专栏

> 原文：<https://www.sitepoint.com/extending-post-columns-admin-areas/>

本教程将概述如何通过定制显示的信息栏来扩展你的文章类型的 WordPress 管理界面。

我们将从概述 WordPress 如何默认显示你的文章类型列表以及向你的用户显示什么信息开始。然后，我们将继续讨论需要哪些挂钩，以及我们将如何操作为您的帖子类型显示的列。

最后，我们将为我们的帖子引入额外的元数据，并在我们的专栏中显示这些信息。

最后，您应该能够扩展和修改您的文章类型的管理屏幕，允许您为您的用户显示附加信息和操作。

## WordPress 帖子管理界面

当你创建你自己的文章类型(或使用默认的文章类型，如“文章”和“页面”)时，WordPress 将创建一个管理区域，你可以在其中管理你的文章。这个区域通常是通过主管理菜单从你网站的后端进入的。

![Accessing the WordPress post administration screen](img/938d7944b27d40d36d5a22ceb759524c.png)

在上图中，我们可以选择“文章”管理菜单或其子菜单“所有文章”。

选择这些选项中的任何一个都会将你带到如下所示的文章管理屏幕。

![WordPress Posts Administration Screen](img/337a326fcfa694159d0ff34a818acfca.png)

这个管理屏幕将概述你的文章类型的所有文章。在这个例子中，它将显示你所有的博客文章，但它也可以很容易地显示你的“页面”或你定义的任何自定义文章类型。

## 扩展员额管理界面

现在您已经知道了什么是文章管理，我们现在可以增强这个界面来提供更多的功能。

您想要扩展这些接口的主要原因是为您的最终用户提供额外的操作和信息。

如果你已经扩展了默认的 WordPress 文章类型来包含额外的元信息(或者你已经定义了全新的文章类型)，通常你会想要直接从这个管理区域向用户显示这些信息。

我们将向您展示如何通过定制和填充帖子列来增强这些帖子管理界面。

## 自定义和填充文章列

当 WordPress 显示你的文章类型的管理区域时，它会列出一系列的默认栏目。例如，当查看`Pages`时，您通常会看到如下所示的`Title`、`Author`、`Comments`和`Date`

![The pages administration screen](img/5e550fc57fceacdce5fb36acf72be933.png)

要自定义信息的显示方式，您需要使用两个过滤器，一个用于确定为您的文章类型显示的列，另一个用于填充您的列数据。

### 自定义文章栏

要定制您的内容类型，您需要使用`manage_$post_type_posts_columns`过滤器。

用您的内容类型的名称替换`$post_type`,您将能够决定为该类型显示哪些列。例如，以下是调用此过滤器的几种方式

*   `manage_post_posts_columns`
    *   定制`post`文章类型
*   `manage_page_posts_columns`
    *   定制您的`page`帖子类型
*   `manage_services_posts_columns`
    *   定制一个名为`services`的定制内容类型(您之前已经定义过)

对于我们的例子，我们将使用`manage_page_posts_columns`过滤器来确定为我们的`page`文章类型显示哪些列。

这个过滤器接受一个名为$columns 的参数，这是一个列名及其显示标题的关联数组。这个变量就是你将要与之互动的东西。

现在您可以访问$columns 数组了，您可以在文章类型中添加新的列，删除列，甚至改变它们的显示位置。对于我们的示例，我们将删除一些列并添加新列。

```
//manage the columns of the `page` post type
function manage_columns_for_page($columns){
    //remove columns
    unset($columns['date']);
    unset($columns['comments']);
    unset($columns['author']);

    //add new columns
    $columns['page_featured_image'] = 'Page Featured Image';
    $columns['page_template'] = 'Page Template'; 
    $columns['page_content']    = 'Page Content';

    return $columns;
}
add_action('manage_page_posts_columns','manage_columns_for_page');
```

该函数将首先使用`unset()`函数(删除/销毁变量)删除`date`、`comments`和`author`列。其次，该函数将添加三个新列，分别称为`page_featured_image`、`page_template`和`page_content`。

### 填充文章列

现在您已经确定了您的文章类型将显示哪些列，您将需要连接到另一个过滤器，以便您可以填充您的列。

`manage_$post_type_posts_custom_column`过滤器将用于此目的。

用您的内容类型的名称替换`$post_type`，您将能够准确地确定在您的栏中显示什么内容。例如，以下是调用此过滤器的几种方式

*   `manage_post_posts_custom_column`
    *   填充`post`文章类型列
*   `manage_page_posts_custom_column`
    *   填充`page`文章类型列
*   `manage_services_posts_custom_column`
    *   为名为`services`的自定义内容类型填充列

在我们的教程中，我们将为我们的`page`文章类型填充列，因此我们将调用`manage_page_posts_custom_column`过滤器。

该滤波器接受两个参数，`$column`和`$post_id`。有了这些变量，我们就能够确定定制的是哪一列(因为它将遍历在`$columns`数组中设置的所有列)以及文章的 ID 是什么(所以我们可以使用 WordPress 函数，因为我们知道我们想要从哪个文章中提取数据)。

我们将调用一个函数，传入这些变量，然后寻找我们新创建的列。当我们找到我们正在寻找的列时，我们将收集我们的数据，然后输出它进行显示。

```
//Populate custom columns for `page` post type
function populate_page_columns($column,$post_id){

    //featured image column
    if($column == 'page_featured_image'){
        //if this page has a featured image
        if(has_post_thumbnail($post_id)){
            $page_featured_image = get_the_post_thumbnail($post_id,'thumbnail');
            echo $page_featured_image;
        }else{
            echo 'This page has no featured image'; 
        }
    }

    //page template column
    if($column == 'page_template'){
        //get the current page template being used for the page 
        $page_template_name = get_post_meta( $post_id, '_wp_page_template', true ); 
        //get a listing of all of the page templates for our site
        $page_templates = get_page_templates();
        if(in_array($page_template_name,$page_templates)){
            //search through each template
            foreach($page_templates as $key <= $value){
                //if the template matches our current template, we found it
                if($page_template_name == $value){
                    echo 'This page is using the ' . $key . ' template';    
                }
            }   
        }else{
            echo 'This page is using the default template';
        }   
    }

    //page content column
    if($column == 'page_content'){

        //get the page based on its post_id
        $page = get_post($post_id);
        if($page){
            //get the main content area
            $page_content = apply_filters('the_content', $page->post_content); 
            echo $page_content;
        }
    }
}
add_action('manage_page_posts_custom_column','populate_page_columns',10,2);
```

对于我们的特色图像列，我们正在检查页面是否有特色图像(后缩略图)集。我们调用`has_post_thumbnail()`并传入我们的`$post_id`变量，该变量根据是否为该页面设置了特色图片来返回 true 或 false。

如果我们有一个特色图像，我们调用`get_the_post_thumbnail()`函数，传入我们的`$post_id`变量和我们想要的大小`thumbnail`。然后我们将回显这个图像项，它将出现在我们的列中。

对于我们的页面模板列，我们首先通过查看它的`_wp_page_template`元数据来获取页面正在使用的当前页面模板。这个键保存显示页面时将提供的模板的文件名。

在这之后，我们用`get_page_templates()`函数得到一个包含所有网站页面模板的数组。这将返回一个关联数组，其中的键是模板的漂亮名称(在模板中用短语`template name: name_of_the_template`定义)及其值作为模板的实际文件名(例如 my_page_template.php)

我们检查该页面的页面模板是否存在于所有页面模板的列表中。如果没有，这意味着它正在使用`default`模板(意味着它将显示 page.php)。

如果该值确实存在，那么我们将遍历所有注册的页面模板，并将它们的值与页面的当前 meta 值进行比较。当我们找到一个同名的模板时，我们知道这个模板是正确的，并显示它漂亮的名字(通过回显一个简单的语句)

对于我们的页面内容列，我们调用`get_post()`函数，传入我们的`$post_id`变量。这个函数将检索属于这个 ID 的页面。如果我们找到了一个页面，我们就使用`apply_filters`函数将页面内容收集到一个变量中。我们将`the_content`的值和从`$page`变量(post 对象)中提取的页面内容传递给这个过滤器。一旦收集，我们展示它。

最后，请注意我们是如何将`10`和`2`传递给我们的`add_action`函数的。它们指定这个钩子将在优先级 10 上执行，并且它将传入 2 个参数。在 WordPress Codex 上随意查看`add_action`功能的[文档](http://codex.wordpress.org/Function_Reference/add_action)

下面是你的页面管理界面如何给你的新栏目的一个例子。

![The pages administration screen with our new columns](img/1dc0cc6f8e5faca5c9239a0f07930be0.png)

## 最后

既然你已经知道了如何使用定制的栏目来扩展你的`pages`管理界面，你就可以浏览你所有的文章类型，并定制你想要为你的文章显示的确切数据。

能够定制向用户显示的数据对于定制帖子类型非常有用，因为如果您可以访问每个帖子的 ID(以及所有元数据)，那么您可以输入任何需要的信息。您甚至可能希望在这些列中添加额外的操作，如动态更新内容或选择新媒体，这些都可以通过定制的列来实现。

## 分享这篇文章