# 如何创建你自己的定制 WordPress 分类法

> 原文：<https://www.sitepoint.com/custom-wordpress-taxonomies/>

本教程将帮助你概述所有你需要知道的 WordPress 分类法。

我们将从讨论什么是分类法、它们如何相互作用以及它们为什么重要开始。

在本教程的后面，我们将一步一步地创建您自己的定制分类法。

最后，您应该对什么是分类法以及如何利用您自己的自定义分类法来满足您的网站需求有了很好的理解。

## 什么是 WordPress 分类法？

从最简单的意义上来说，分类法是一种使用一组或多组单词将相关项目组合在一起的方法。

WordPress 使用分类法来提供一个简单的机制来将你的文章和页面组合在一起。WordPress 自带了两种主要的分类法:

*   种类
*   标签

这两种分类法为您提供了一种简单的方法，使用对您和您的读者有意义的名称/分组来将您的文章分组。例如，您可以创建一个名为“特色”的新类别，然后您可以为其分配特定的帖子。

当您创建一个类别或标签时，您是在“分类”中创建一个“术语”。知道这一点很有用:类别和标签只是 WordPress 的默认实现。你可以用适合你需要的术语创建你自己的“分类法”。

## 类别和标签管理区

WordPress 自动为你创建管理区域，方便你添加标签和类别。这些管理区域作为子菜单位于“文章”菜单中。

![WordPress Taxonomies_Image 1](img/bf89011b6f02aae9428db514ba912a22.png)

类别和标签管理界面看起来都很相似，这给了你一个简单的方法来给你的网站添加新的术语。

![WordPress Taxonomies_Image 2](img/5ed4fc88b70e61344bfc3327c3a0291c.png)

## 为你的文章分配类别和标签

给你的文章添加类别和标签最简单的方法是通过 WordPress 编辑器。

当你编辑你的文章/页面时，你会注意到一个包含你的类别和标签的元框。这些框通常出现在“发布”元框的下面。您需要使用这些框来将您的术语分配给当前编辑的帖子。

![WordPress Taxonomies_Image 3](img/73a17eafc29fced22e4ba05792f2c42a.png)

标签和分类元框都可以让你为你的文章分配多个术语。您可以通过他们的管理菜单添加您之前创建的术语，或者您可以动态地创建和分配它们(这些新术语将可供将来的其他帖子选择)。

## WordPress 用它的分类法做什么？

WordPress 将利用其内置分类法做一系列事情，包括:

*   创建一个术语列表页面: WordPress 将为这个术语创建一个新页面。术语本身的 URL 将是分类的名称，后跟术语的名称。例如，如果您有一个名为“精选”的“类别”，URL 将是/category/featured。这个页面的目的本质上是作为一个列表页面。

*   **在你的单个帖子上创建一个到单个分类列表页面的链接:**如果你把内置的术语附加到你的帖子上，当你在前端查看你的帖子时，WordPress 会显示一个可点击的术语名称，通常在帖子标题的正下方。点击术语将带您到术语列表页面。

*   **在窗口小部件中列出你的术语:** WordPress 预建了几个“窗口小部件”，可以让你轻松地将内容添加到你网站的窗口小部件区域(比如侧栏和页脚)。你的“类别”和“标签云”窗口小部件会把你的术语拉进来，并显示为可点击的链接。

*   **将您的术语添加到导航菜单:**您的所有标签和类别都被添加到导航管理菜单中，您可以在此定义和构建您的主菜单。这使您可以轻松地创建一个链接，直接指向您最常用的术语。

根据您的主题，可能会有其他领域利用您的分类法。

## 创建您自己的定制分类法

虽然内置的类别和标签可能适合你的组织需求，但通常情况下，你需要更多的方法来将你的帖子分组，特别是如果你使用自定义的帖子类型。

### 如何注册您自己的自定义分类

要创建您自己的定制分类法，您需要使用`register_taxonomy`函数来定义您的分类法。该函数接受如下三个值`register_taxonomy($taxonomy, $object_type, $args)`。这些值的简要总结详述如下:

1.  `$taxonomy`–您正在创建的新分类的名称。当 WordPress 称他们的分类法为“类别”或“标签”时，你可能想给你的分类法取另一个名字，比如“成员”。该名称的长度不得超过 32 个字符，并且只能使用字母和下划线字符。

2.  `$object_type`–要附加此分类的文章类型的名称。文章类型既有类别也有标签。您可能希望将新的分类附加到现有的帖子类型，或者您自己以前创建的自定义帖子类型。在这种情况下，您有两种选择:

    *   代表文章类型名称的单个字符串，如`$object_type = 'post'`

    *   文章类型名称的字符串数组，如`$object_type = array('post','page')`

3.  这些是你用来为你的新分类法设置各种选项的参数。您可以设置多个选项。其中一些是强制性的，但大多数是可选的(WordPress 将处理任何缺失的参数):

    *   `label`–分类的复数名称，例如“members”，如果您正在创建成员分类。

    *   `labels`–将用于分类的名称和值的数组。这些用于管理领域。该数组指定了用于您的分类法的所有标签。如果你把这个留空，WordPress 将使用你的`label`值并设置它们。此外，您可以跳过特定的不需要的值，它们将被默认。

        *   `name`–分类的复数名称。

        *   `singular_name`–分类法中一个术语的单数名称。

        *   `menu_name`–显示在 WordPress 管理后端的文本(沿着左侧管理菜单)。

        *   `all_items`–查看分类中的所有术语。

        *   `view_item`–查看分类中的单个术语。

        *   `update_item`–更新单一分类。

        *   `add_new_item`–添加新的术语文本。

        *   `parent_item`–用于层级分类法，通常设置为`Parent $taxonomy_name`。

        *   `parent_item_colon`–同上，但在末尾添加了一个冒号。

        *   `search_items`–查看您的分类时使用的搜索文本。

        *   `popular_items`–常用术语名称，在后端管理部分用于非层级术语。可以轻松设置为`Popular $taxonomy_name`。

        *   `separate_items_with_commas`–此文本显示在非层次分类中，这是显示在分类元框中的单个帖子的文本(直接显示在“添加”按钮下)。

        *   `add_or_remove_items`–此文本为非层次分类显示。只有在单独页面的分类元框中禁用了 JavaScript 时，才会显示该文本。

        *   `choose_from_most_used`–对于非层次分类，此文本显示在分类元框的底部，选择后将显示最常用术语的列表。

        *   `not_found`–只有当您单击“从最常用的分类中选择”突出显示的文本时，此文本才会显示在分类元框内的非层次分类中。一旦点击，WordPress 将寻找最常用的术语。如果没有，将显示此文本。

    *   `public`–确定是否显示您的分类，并能够对其进行查询。

    *   确定 WordPress 是否会为你的分类显示一个管理区域。如果没有设置，你将没有一个区域来管理你的条款。

    *   `show_in_nav_menus`–确定此分类中的术语是否可在导航菜单中选择。

    *   `show_tagcloud`–确定 WordPress 是否会在标签云小部件中包含您的分类术语。

    *   `meta_box_cb`–让您指定一个函数，用于输出您的单个帖子中的分类元框的设计。如果没有选择，WordPress 将使用它的默认值。

    *   `show_admin_column`–这决定了你的分类术语是否会出现在文章列表的新栏中。将此设置为“真”将为您指定的文章类型显示一个新列，该列将显示其所有附加条款。

    *   `hierarchical`–确定您的分类法是可以分级，如父/子(如类别)，还是全部分级(如标签)。

    *   `update_count_callback`–当附加的 post 类型有更新时要调用的函数的名称。当这个分类的文章类型改变时，这个函数将被调用。

    *   `query_var`–确定用于查询帖子类型的名称。默认情况下，这被设置为分类法本身的名称。如果设置为字符串，将使用该字符串。这最好保留其默认值。

    *   `rewrite`–这可以通过多种方式进行设置。将此项设置为“假”将禁用永久链接。如果未设置为 false，您可以指定多个元素，如“slug”。这最好保留其默认的真值。

    *   `capabilities`–确定与分类法交互所需的能力(权限)，如删除、添加、分配。这最好保留其默认值。

    *   `sort`–指定将术语分配给帖子时，它应该记住顺序。

    *   `_builtin`–确定该术语是“内置”分类还是自定义分类。**在创建您的分类时，不应该触及这一点**

记得查看[查看自定义分类法上的 WordPress codex](http://codex.wordpress.org/Function_Reference/register_taxonomy)，因为它概述了哪些值是强制的，哪些是可选的(以及哪些值是有效的)。

### 创建您的第一个自定义分类

如您所见，您可以设置几个选项来根据自己的需求调整您的分类法。

虽然你可以单独设置所有的分类法选项，但大多数时候最好让 WordPress 回到默认设置。

对于本教程，我们将创建一个名为`Members`的新分类，它将附加到我们已经存在的`Post` post 类型。

```
//create a function that will attach our new 'member' taxonomy to the 'post' post type
function add_member_taxonomy_to_post(){

    //set the name of the taxonomy
    $taxonomy = 'member';
    //set the post types for the taxonomy
    $object_type = 'post';

    //populate our array of names for our taxonomy
    $labels = array(
        'name'               => 'Members',
        'singular_name'      => 'Member',
        'search_items'       => 'Search Members',
        'all_items'          => 'All Members',
        'parent_item'        => 'Parent Member',
        'parent_item_colon'  => 'Parent Member:',
        'update_item'        => 'Update Member',
        'edit_item'          => 'Edit Member',
        'add_new_item'       => 'Add New Member', 
        'new_item_name'      => 'New Member Name',
        'menu_name'          => 'Member'
    );

    //define arguments to be used 
    $args = array(
        'labels'            => $labels,
        'hierarchical'      => true,
        'show_ui'           => true,
        'how_in_nav_menus'  => true,
        'public'            => true,
        'show_admin_column' => true,
        'query_var'         => true,
        'rewrite'           => array('slug' => 'member')
    );

    //call the register_taxonomy function
    register_taxonomy($taxonomy, $object_type, $args); 
}
add_action('init','add_member_taxonomy_to_post');
```

执行上面的代码将创建一个名为`Members`的新分类，并将其附加到`Post` post 类型。当你浏览你网站的后端时，你会在`Post`菜单中看到一个新的子菜单。点击这个新的`Members`子菜单将带您进入分类管理屏幕。

![WordPress Taxonomies_Image 4](img/b9820ea9f37f6c68b149cbf6ad8205e2.png)

您的分类管理屏幕看起来像是`category`或`tag`屏幕，这取决于您将`hierarchical`参数设置为什么。我们新的`member`界面如下所示:

![WordPress Taxonomies_Image 5](img/9f6a69a56d011365d50da13f95de2e3e.png)

### 创建您的新术语

注册分类后，您现在可以为您的 hearts 内容创建新的术语(在我们的案例中是会员)。

我们创建了三个新的顶级术语。我们的术语叫做`Featured Members`、`Standard Members`和`Seasonal Members`。

我们的特色会员和标准会员是单级会员，而我们的季节会员是多级会员，有两个术语作为其子代，称为`Winter Members`和`Summer Members`。

当你考虑类别时，这很容易想象。类别可以有许多子类别，甚至根本没有子类别。

![WordPress Taxonomies_Image 6](img/aa1eb23d4009d17affd43a3f83f326fa.png)

### 将您的条款添加到帖子中

您可以在帖子中添加任意数量的术语。您可以通过编辑文章本身并使用分类元框附加术语来分配这些术语。

![WordPress Taxonomies_Image 7](img/8602d93544202b817e94d950c667facf.png)

## 与您的分类法/术语交互

一旦您为您的帖子创建并分配了术语，您将需要修改您的主题以利用您的新分类法。

当你查看你的帖子时，WordPress 会显示它的类别和术语相关链接。但是，因为您已经注册了自己的分类法，所以您需要定制您的模板，以确保显示您的术语。

### 更改主题文件以显示新的分类法

因为您已经添加了一个额外的分类法，所以您需要稍微修改一下您的主题模板文件，以确保显示您的术语。这包括编辑一些主题文件和与一些 WordPress 函数交互。

#### 了解您的单个模板文件

如果你正在处理一个子主题(你应该这样)，你可以打开你的 single.php 模板文件。虽然我们不会编辑这个文件，但是知道这个文件处理所有单个`posts`的输出是很重要的。既然我们已经将我们的`member`分类法附加到了`posts`，我们将从这里开始。

你的 single.php 应该看起来像下面这样。我使用 213 作为我的父主题，但总体来说，大多数 single.php 文件有相同的核心功能。

```
<?php
get_header(); ?>
	<div id="primary" class="content-area">
		<div id="content" class="site-content" role="main">
			<?php /* The loop */ ?>
			<?php while ( have_posts() ) : the_post(); ?>
				<?php get_template_part( 'content', get_post_format() ); ?>
				<?php twentythirteen_post_nav(); ?>
				<?php comments_template(); ?>
			<?php endwhile; ?>
		</div><!-- #content -->
	</div><!-- #primary -->
<?php get_sidebar(); ?>
<?php get_footer(); ?>
```

你需要关注的主要部分是`get_template_part('content',get_post_format())`。这个函数实际上会显示你所有的文章内容，我们需要在这里进行修改。

#### 了解您的内容模板文件

content.php 是负责输出文章内容的文件。实际上，它是用来输出所有文章类型内容的主要文件，当没有更具体的模板要加载时，它将加载 content.php。

`get_template_part()`的第一部分将取你试图加载的文件的名称。第二个参数(可选)将查找该文件的特定版本。例如，`get_template_part('content','video')`将寻找一个名为“content-video.php”的文件。当它自己调用时，比如`get_template_part('content’)`，它会寻找一个名为‘content . PHP’的文件

我们想要做的是编辑我们的内容模板部分。然而，这带来了一些困难，因为像分类法这样的元信息在不同的主题中被不同地处理。

*   **二十十三:**在二十十三中，使用`twentythirteen_entry_meta()`功能显示元信息。这个函数循环遍历你的文章的类别/标签，并从 content.php(以及你的所有其他“内容”文件，如“content-video.php”)中调用。

*   **二十十四:**在二十十四中，元信息直接显示在 content.php 内部(此外，每个其他“内容”文件，如“content-video.php”也对此进行手动处理)。

#### 创建一个函数来输出新的分类术语

我们的解决方案将是创建一个快捷的功能，其唯一的工作是显示我们的新“成员”条款。

打开 functions.php，输入以下代码:

```
function display_member_taxonomy_terms($post_id){

    //get all terms assigned to this post
    $member_terms = get_the_terms($post_id,'member'); 
    //if we have member terms assigned to this post
    if($member_terms){
        echo '<div class="member-terms-meta">';
        echo '<span class="term-title"> Member Terms: </span>';
        //loop through each term 
        foreach($member_terms as $term){
            //collect term information and display it
            $term_name = $term->name;
            $term_link = get_term_link($term,'member'); 
            echo '<a href="' . $term_link . '">'; 
                echo '<span class="term">' . $term_name . '</span>';
            echo '</a>';
        }
        echo '</div>';
    }
}
```

#### 在内容文件中调用新函数

一旦你把这个函数添加到你的 functions.php 文件中，打开你的 content.php，找到你的“入口元”输出到哪里。在这个“entry-meta”div 中调用您的新函数。我在标准的 213 元函数之上调用了我们的函数，如下所示:

```
<div class="entry-meta">
	<?php display_member_taxonomy_terms($post->ID); ?>
	<?php twentythirteen_entry_meta(); ?>
	<?php edit_post_link( __( 'Edit', 'twentythirteen' ), '<span class="edit-link">', '' ); ?>
</div><!-- .entry-meta -->
```

当你打开你的单篇文章时，你会看到`member`术语现在显示在标准类别和标签术语的上方(只要你已经分配了成员术语)。

![WordPress Taxonomies_Image 8](img/6ab5d19c455c731fbbf4a5795d313607.png)

#### 理解我们刚刚做了什么

为了显示您的新术语，我们首先从您的 content.php 文件中调用一个名为`display_member_taxonomy_terms($post->ID);`的函数。这个函数接收当前的文章 ID，并试图显示属于我们新分类法的、已经分配给这个文章的所有术语。

我们首先打电话给`$member_terms = get_the_terms($post_id,'member');`来收集这篇文章的条款。`get_the_terms($post_id,$taxonomy_name)`函数接收一篇文章的 ID 和一个分类名称，并给出该分类的所有附加术语。在我们的情况下，我们传递给它当前的文章 id 和“成员”分类。

接下来，我们使用一个简单的 if 语句`if($member_terms)`检查是否有任何结果。如果我们有要显示的术语，我们使用 for 循环`foreach($member_terms as $term)`遍历每个术语。这个循环让我们遍历每个术语并访问它的信息。

最后，我们访问了术语的名称(向用户显示)并使用`get_term_link($term_object,$taxonomy_name)`函数获取了术语的 URL。这个函数接受一个术语对象和分类法的名称，并获得术语本身的直接链接。

## 包装东西

正如你所看到的，创建自定义分类可以为你提供多种新的方式来组织你的网站内容，比如你的文章。

通过创建一个新的分类和相关的术语，并将它们分配给一个帖子，您可以创建一个组织层，您可以利用它来进一步对您的网站内容进行分类。

## 分享这篇文章