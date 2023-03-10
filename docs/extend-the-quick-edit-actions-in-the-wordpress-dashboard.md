# 扩展 WordPress 仪表盘中的快速编辑操作

> 原文：<https://www.sitepoint.com/extend-the-quick-edit-actions-in-the-wordpress-dashboard/>

WordPress 管理界面中内置的“快速编辑”功能给了你一个简单快捷的方法来改变你的文章内容，而不需要跳转到完整的文章编辑器。

默认情况下，WordPress 包括几个字段来加速你的编辑过程，比如标题、slug、分类法和元数据，比如发布日期和帖子状态选择器。然而，如果你扩展了你的文章类型，你会发现你不能在这里改变这些值。这就是我们现在要看的。

![quick edit header](img/c936a2ec1b520df45f100d79b69e0066.png)

## 为我们的功能创建插件

当我们修改功能时，创建插件是一个很好的实践(如果你刚刚开始插件开发，你可以找到一个[初学者指南)。首先创建一个类作为插件的入口点。有几个移动部件来启动和运行它，所以从一个基本结构开始。](https://www.sitepoint.com/wordpress-plugin-development-beginners/)

```
/*
Plugin Name: Extend Quick Edit
Plugin URI: https://elevate360.com.au/plugins/extend-quick-edit
Description: Extends the quick-edit interface to display additional post meta
Version: 1.0.0
Author: Simon Codrington
Author URI: http://simoncodrington.com.au
Text Domain: post-quick-edit-extension
Domain Path: /languages
*/

class el_extend_quick_edit{

    private static $instance = null;

    public function __construct(){

        add_action('manage_post_posts_columns', array($this, 'add_custom_admin_column'), 10, 1); //add custom column
        add_action('manage_posts_custom_column', array($this, 'manage_custom_admin_columns'), 10, 2); //populate column
        add_action('quick_edit_custom_box', array($this, 'display_quick_edit_custom'), 10, 2); //output form elements for quickedit interface
        add_action('admin_enqueue_scripts', array($this, 'enqueue_admin_scripts_and_styles')); //enqueue admin script (for prepopulting fields with JS)
        add_action('add_meta_boxes', array($this, 'add_metabox_to_posts'), 10, 2); //add metabox to posts to add our meta info
        add_action('save_post', array($this, 'save_post'), 10, 1); //call on save, to update metainfo attached to our metabox

    }

    //adds a new metabox on our single post edit screen
    public function add_metabox_to_posts($post_type, $post){

    }
    //metabox output function, displays our fields, prepopulating as needed
    public function display_metabox_output($post){

    }
    //enqueue admin js to pre-populate the quick-edit fields
    public function enqueue_admin_scripts_and_styles(){

    }
    //Display our custom content on the quick-edit interface, no values can be pre-populated (all done in JS)
    public function display_quick_edit_custom($column){

    }
    //add a custom column to hold our data
    public function add_custom_admin_column($columns){

    }
    //customise the data for our custom column, it's here we pull in meatdata info
    public function manage_custom_admin_columns($column_name, $post_id){

    }
    //saving meta info (used for both traditional and quick-edit saves)
    public function save_post($post_id){

    }
    //gets singleton instance
    public static function getInstance(){
        if(is_null(self::$instance)){
            self::$instance = new self();
        }
        return self::$instance;
    }

}
$el_extend_quick_edit = el_extend_quick_edit::getInstance();
```

## 添加自定义元字段

因为我们最终想要扩展快速编辑屏幕，我们需要从添加一个新的 meta 字段到我们的帖子开始。我们将添加一个自定义元框，然后输出几种不同类型的表单元素:

*   一个复选框来决定我们的帖子是否会被“精选”，然后我们可以在我们的主题中的某个地方使用独特的风格。
*   用作帖子“评级”的数字列表。这将显示为一个选择元素。这可以被主题用来将帖子输出限制在特定的排名或更高的排名
*   一个简单的文本输入来保存我们的帖子的“副标题”，有点像一个实习生，可以显示在帖子的主标题下面。

如果你是第一次向你的文章类型添加自定义元字段，请随意阅读我们最近的文章[，我们在文章中看到了添加自定义元框/字段](https://www.sitepoint.com/adding-custom-meta-boxes-to-wordpress/)。

```
//adds a new metabox on our single post edit screen
public function add_metabox_to_posts($post_type, $post){

    add_meta_box(
        'additional-meta-box',
        __('Additional Info', 'post-quick-edit-extension'),
        array($this, 'display_metabox_output'),
        'post',
        'side',
        'default'
    );
}
```

现在我们有了元盒，我们需要添加我们的字段。对于我们的每个不同的领域，我们需要输出标记和预选/填充值，如果我们已经设置了它们。

```
//metabox output function, displays our fields, prepopulating as needed
public function display_metabox_output($post){

    $html = '';
    wp_nonce_field('post_metadata', 'post_metadata_field');

    $post_featured = get_post_meta($post->ID, 'post_featured', true);
    $post_rating = get_post_meta($post->ID, 'post_rating', true);
    $post_subtitle = get_post_meta($post->ID, 'post_subtitle', true);

    //Featured post (radio)
    $html .= '<p>';
        $html .= '<p><strong>Featured Post?</strong></p>';
        $html .= '<label for="post_featured_no">';
            if($post_featured == 'no' || empty($post_featured)){
                $html .= '<input type="radio" checked name="post_featured" id="post_featured_no" value="no"/>';
            }else{
                $html .= '<input type="radio" name="post_featured" id="post_featured_no" value="no"/>';
            }
        $html .= ' No</label>';
        $html .= '</br>';
        $html .= '<label for="post_featured_yes">';
            if($post_featured == 'yes'){
                $html .= '<input type="radio" checked name="post_featured" id="post_featured_yes" value="yes"/>';
            }else{
                $html .= '<input type="radio" name="post_featured" id="post_featured_yes" value="yes"/>';
            }
        $html .= ' Yes</label>';
    $html .= '</p>';

    //Internal Rating (select)
    $html .= '<p>';
        $html .= '<p>';
            $html .= '<label for="post_rating"><strong>Post Rating</strong></label>';
        $html .= '</p>';
        $html .= '<select name="post_rating" id="post_rating" value="' . $post_rating .'" class="widefat">';
            $html .= '<option value="1" ' . (($post_rating == '1') ? 'selected' : '') . '>1</option>';
            $html .= '<option value="2" ' . (($post_rating == '2') ? 'selected' : '') . '>2</option>';
            $html .= '<option value="3" ' . (($post_rating == '3') ? 'selected' : '') . '>3</option>';
            $html .= '<option value="4" ' . (($post_rating == '4') ? 'selected' : '') . '>4</option>';
            $html .= '<option value="5" ' . (($post_rating == '5') ? 'selected' : '') . '>5</option>';
        $html .= '</select>';

    $html .= '</p>';

    //Subtitle (text)
    $html .= '<p>';
        $html .= '<p>';
            $html .= '<label for="post_subtitle"><strong>Subtitle</strong></label>';
        $html .= '</p>';
        $html .= '<input type="text" name="post_subtitle" id="post_subtitle" value="' . $post_subtitle .'" class="widefat"/>';
    $html .= '</p>';

    echo $html;

}
```

我们的大部分输出都是不言自明的，我们在显示了我们的 [nonce 字段](https://codex.wordpress.org/Function_Reference/wp_nonce_field)之后输出表单控件，然后填充我们的各种值。编辑帖子时，所有这些内容都将显示在您的 metabox 中，看起来应该与下图类似。

![extend quick edit metabox](img/6257fa131912bc87b872be05a66cb129.png)

## 保存我们的新元数据

因为我们正在添加新的字段，所以我们需要挂钩到`save_post`挂钩，这样我们就可以更新我们的数据。这些信息显示在我们的自定义列上，并最终用于预填充我们的快速编辑控件。将下面的内容复制到插件的`save_post`方法中。

```
//saving meta info (used for both traditional and quick-edit saves)
public function save_post($post_id){

    $post_type = get_post_type($post_id);

    if($post_type == 'post'){

        //check nonce set
        if(!isset($_POST['post_metadata_field'])){
            return false;
        }

        //verify nonce
        if(!wp_verify_nonce($_POST['post_metadata_field'], 'post_metadata')){
            return false;
        }

        //if not autosaving
        if ( defined( 'DOING_AUTOSAVE' ) && DOING_AUTOSAVE ) {
            return false;
        }

        //all good to save
        $featured_post = isset($_POST['post_featured']) ? sanitize_text_field($_POST['post_featured']) : '';
        $post_rating = isset($_POST['post_rating']) ? sanitize_text_field($_POST['post_rating']) : '';
        $post_subtitle = isset($_POST['post_subtitle']) ? sanitize_text_field($_POST['post_subtitle']) : '';

        update_post_meta($post_id, 'post_featured', $featured_post);
        update_post_meta($post_id, 'post_rating', $post_rating);
        update_post_meta($post_id, 'post_subtitle', $post_subtitle);
    }   

}
```

我们函数的第一部分检查我们的 nonce 是否被设置并且有效。这个随机数在元盒的顶部输出，作为一种简单的安全方法。我们收集我们的价值观，净化它们并用 [`update_post_meta`](https://codex.wordpress.org/Function_Reference/update_post_meta) 保存它们。

## 添加自定义列

要将自定义输出添加到快速编辑屏幕，您需要在帖子管理屏幕中至少有一个自定义列；没有办法解决这个问题，因为我们稍后用来输出内容的钩子只有在有自定义管理列时才会触发。

这些列很重要，因为在这里我们将输出我们的元数据的帖子当前值(因为快速编辑输出功能没有预先填充我们的值的能力)。将以下内容复制到你的插件中。

```
//add a custom column to hold our data
public function add_custom_admin_column($columns){
    $new_columns = array();

    $new_columns['post_featured'] = 'Featured?';
    $new_columns['post_rating'] = 'Rating';
    $new_columns['post_subtitle'] = 'Subtitle';

    return array_merge($columns, $new_columns);
}
```

我们挂钩到 [`manage_post_posts_columns`](https://codex.wordpress.org/Plugin_API/Action_Reference/manage_posts_custom_column) 过滤器，将我们自己的定制列添加到`post`内容类型中。在我们的示例中，我们添加了三列，但是您可以根据需要添加任意多或任意少的列。

## 填充我们的自定义列以显示当前值

既然我们已经有了自定义列，我们需要用我们的帖子中的值填充它们。我们挂钩到 [`manage_posts_custom_column`](https://codex.wordpress.org/Plugin_API/Action_Reference/manage_posts_custom_column) 动作并比较`column_name`变量，这样我们就可以填充我们的列。

这里一个重要的部分是，对于我们的每个元值，我们用一个`id`比如`post_featured_{$post_id}`将它们包装在一个`div`中。这是有意的，以便以后当我们需要获取当前元值并预先填充快速编辑屏幕中输出的字段时。

```
//customise the data for our custom column, it's here we pull in metadata info for each post. These will be referred to in our JavaScript file for pre-populating our quick-edit screen
public function manage_custom_admin_columns($column_name, $post_id){

    $html = '';

    if($column_name == 'post_featured'){
        $post_featured = get_post_meta($post_id, 'post_featured', true);

        $html .= '<div id="post_featured_' . $post_id . '">';
        if($post_featured == 'no' || empty($post_featured)){
            $html .= 'no';
        }else if ($post_featured == 'yes'){
            $html .= 'yes';
        }
        $html .= '</div>';
    }
    else if($column_name == 'post_rating'){
        $post_rating = get_post_meta($post_id, 'post_rating', true);

        $html .= '<div id="post_rating_' . $post_id . '">';
            $html .= $post_rating;
        $html .= '</div>';
    }
    else if($column_name == 'post_subtitle'){
        $post_subtitle = get_post_meta($post_id, 'post_subtitle', true);

        $html .= '<div id="post_subtitle_' . $post_id . '">';
            $html .= $post_subtitle;
        $html .= '</div>';
    }

    echo $html;
}
```

## 自定义快速编辑界面

为了定制这个部分，我们需要挂钩到`quick_edit_custom_box`动作。我们在管理界面中的每个帖子都会触发这个动作，呈现所有默认的快速动作，并允许我们添加自己的自定义动作。

对于每个注册的自定义列，该函数被调用一次。我们在这里挂接以扩展接口。

```
//Display our custom content on the quick-edit interface, no values can be pre-populated (all done in JavaScript)
public function display_quick_edit_custom($column){

    $html = '';
    wp_nonce_field('post_metadata', 'post_metadata_field');

    //output post featured checkbox
    if($column == 'post_featured'){
        $html .= '<fieldset class="inline-edit-col-left clear">';
            $html .= '<div class="inline-edit-group wp-clearfix">';
                $html .= '<label class="alignleft" for="post_featured_no">';
                    $html .= '<input type="radio" name="post_featured" id="post_featured_no" value="no"/>';
                $html .= '<span class="checkbox-title">Post Not Featured</span></label>';
                $html .= '<label class="alignleft" for="post_featured_yes">';
                    $html .= '<input type="radio" name="post_featured" id="post_featured_yes" value="yes"/>';
                $html .= '<span class="checkbox-title">Post Featured</span></label>';

            $html .= '</div>';
        $html .= '</fieldset>';
    }
    //output post rating select field
    else if($column == 'post_rating'){     
        $html .= '<fieldset class="inline-edit-col-center ">';
            $html .= '<div class="inline-edit-group wp-clearfix">';
                $html .= '<label class="alignleft" for="post_rating">Post Rating</label>';
                $html .= '<select name="post_rating" id="post_rating" value="">';
                    $html .= '<option value="1">1</option>';
                    $html .= '<option value="2">2</option>';
                    $html .= '<option value="3">3</option>';
                    $html .= '<option value="4">4</option>';
                    $html .= '<option value="5">5</option>';
                $html .= '</select>';
            $html .= '</div>';
        $html .= '</fieldset>';    
    }
    //output post subtitle text field 
    else if($column == 'post_subtitle'){
        $html .= '<fieldset class="inline-edit-col-right ">';
            $html .= '<div class="inline-edit-group wp-clearfix">';
                $html .= '<label class="alignleft" for="post_rating">Post Subtitle</label>';
                $html .= '<input type="text" name="post_subtitle" id="post_subtitle" value="" />';
            $html .= '</div>';
        $html .= '</fieldset>';    
    }

    echo $html;
}
```

输出这些字段的整个过程类似于我们为 metabox 所做的。我们希望显示我们的特色复选框，评级选择字段和文本输入控件。我们的元素使用的类和 WordPress 用于布局的类是一样的。如果你想要一种特殊的风格，你需要尝试使用它们，但是使用`inline-edit-col-left`、`inline-edit-col-center`和`inline-edit-col-right`会帮你调整你的布局。

## 将我们的管理 JavaScript 文件入队

当 WordPress 输出 post admin 屏幕时，快速编辑界面不会被创建。当用户按下**‘编辑’**按钮时，WordPress 将动态创建编辑控件，填充标题、slug、作者和其他信息。

由于这都是动态的，我们不能只在 PHP 中输出我们的元数据，我们需要使用 JavaScript 收集我们的值。

![quick edit example](img/716563f7edf62ca7c3c69e4ca7dbb518.png)

添加以下函数来包含我们的管理员专用 JavaScript 文件。我已经把它添加到插件的基本目录中，但是如果你想把它放在一个专门的 JavaScript 文件夹中，你可以改变它的结构。

```
public function enqueue_admin_scripts_and_styles(){
    wp_enqueue_script('quick-edit-script', plugin_dir_url(__FILE__) . '/post-quick-edit-script.js', array('jquery','inline-edit-post' ));
}
```

## 用值动态填充快速编辑屏幕

我们之前使用的`quick_edit_custom_box`动作输出快速编辑控件的所有表单控件。然而，如前所述，这个控件是用 JavaScript 动态生成的，所以不可能自动预填充我们的元字段。

有两种方法可以用来获取值:

1.  **创建一个定制方法来获取您的信息**:钩住`edit`动作并触发对注册函数的 Ajax 调用。要让它工作，您可以从快速编辑界面传递文章的 ID，然后使用`get_post_meta`函数检索并返回您的值。
2.  **利用 WordPress 现有的功能**:复制现有的 WordPress JavaScript 函数，并扩展它来收集您的自定义值。

第一种方法很好，但是需要更多的工作，需要返回服务器以获取更多信息。我们的第二个选择稍微简单一点，我们可以挂钩到用于呈现快速编辑框的 JavaScript 函数，然后扩展它来收集我们的元数据。这是我们将要走的路线。

打开新加入队列的 JavaScript 文件，并添加以下内容。我们很快就会看到它是如何工作的。

```
/*
 * Post Bulk Edit Script
 * Hooks into the inline post editor functionality to extend it to our custom metadata
 */

jQuery(document).ready(function($){

    //Prepopulating our quick-edit post info
    var $inline_editor = inlineEditPost.edit;
    inlineEditPost.edit = function(id){

        //call old copy 
        $inline_editor.apply( this, arguments);

        //our custom functionality below
        var post_id = 0;
        if( typeof(id) == 'object'){
            post_id = parseInt(this.getId(id));
        }

        //if we have our post
        if(post_id != 0){

            //find our row
            $row = $('#edit-' + post_id);

            //post featured
            $post_featured = $('#post_featured_' + post_id);
            post_featured_value = $post_featured.text(); 
            if(post_featured_value == 'yes'){
                $row.find('#post_featured_yes').val(post_featured_value).attr('checked', true);
            }else{
                $row.find('#post_featured_no').val(post_featured_value).attr('checked', true);
            }

            //post rating
            $post_rating = $('#post_rating_' + post_id);
            $post_rating_value = $post_rating.text();
            $row.find('#post_rating').val($post_rating_value);
            $row.find('#post_rating').children('[value="' + $post_rating_value + '"]').attr('selected', true);

            //post subtitle
            $post_subtitle= $('#post_subtitle_' + post_id);
            post_subtitle_value = $post_subtitle.text();
            $row.find('#post_subtitle').val(post_subtitle_value);

        }

    }

});
```

我们从用`inlineEditPost.edit`获取内联编辑器对象开始。我们保留此副本并替换其功能。在我们的新函数中，我们使用`$inline_editor.apply( this, arguments);`调用旧函数，这确保了我们创建快速编辑框的默认 WordPress 功能得以保留。

现在我们可以触发我们的功能了。我们确保传递给函数的`id`变量是一个`object`，然后从中获得`post_id`。

我们使用这个`post_id`来查找保存元数据的自定义管理列。例如，如果我们正在编辑帖子`2100`，我们可以通过找到 ID 为`post_rating_2100`的列来查找它的帖子评级。

我们找到所有的值，然后相应地预填充表单控件。如果您有更多的字段，您可以使用相同的逻辑来进一步扩展。

当用户点击**更新**按钮时，一个 Ajax 请求将发出并触发一个保存 post 调用；这将触发我们之前挂钩的`save_post`函数，负责更新我们的新元值。

## 把一切都包起来

有几个步骤来得到这一切排序，但正如你可以看到在下图中，这一切都是值得的。您的快速编辑界面现在显示您的元数据，您可以轻松地更新它，而不必打开您的帖子

![Final output](img/f7b7730b31881f1b786f6c49663501ba.png)

这个界面本身就是一个很好的工具，如果你在文章中附加了几种不同类型的元字段，那么将它们扩展到快速编辑界面是一个很好的主意。管理你的文章而不需要手动输入每一篇文章将会节省大量的时间，并使管理你的网站更加容易。

您可以在快速编辑字段中输出您想要的任何类型的信息，因此您可以利用我们在这里所做的并进一步扩展它，以提供更加定制的解决方案。

## 分享这篇文章