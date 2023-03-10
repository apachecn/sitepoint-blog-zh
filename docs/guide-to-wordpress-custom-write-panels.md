# WordPress 自定义书写面板指南

> 原文：<https://www.sitepoint.com/guide-to-wordpress-custom-write-panels/>

随着 WordPress 3.0 的发布，引入了一系列功能，包括将 WordPress MU 合并到主代码库(允许从一个 WordPress 安装中管理多个站点)，以及添加自定义帖子类型。一个已经存在了一段时间的特性是**自定义写作面板**，但由于新的自定义文章类型，它现在变得格外有趣。

![](img/64fbf7cb2c6b9c0c3e0d1b6fca0d52b4.png "custom-panels-display")

自定义书写面板是一个表单字段框(输入框、复选框、单选按钮、文本区等)，在您书写或编辑帖子时显示。这些字段链接到您添加到帖子中的自定义字段。

尽管默认情况下，自定义字段面板显示在编辑文章页面中，但使用起来可能会很棘手，尤其是当您想要输入大量数据时。这就是定制写作面板的用武之地:它们让你创建看起来很光滑的面板，让用户输入你想要的帖子的所有附加信息。

例如，让我们创建一个名为 Books 的自定义文章类型。图书将有一个标题和描述(标题和主要博客内容面板)，但它们还需要一些额外的信息才能正确显示:作者和 ISBN(一个唯一的数字商业图书标识符)。所以，在你的主题的`functions.php`中，你需要添加这段代码:

```
add_action( 'init', 'create_book_type' );
function create_book_type() {
  register_post_type( 'books',
    array(
      'labels' => array(
        'name' => __( 'Books' ),
        'singular_name' => __( 'Book' )
      ),
      'public' => true,
    )
  );
}
```

这是做什么的？

`add_action( 'init', 'create_book_type' );`告诉 WordPress 在`init`动作上运行我们的代码(换句话说，只要 WordPress 加载完毕)，`[register_post_type()](http://codex.wordpress.org/Function_Reference/register_post_type "The WordPress Codex page for register_post_type")`方法添加了图书文章类型。现在，如果您查看您的管理仪表板，您会在左侧导航中看到一个新的图书面板。我们希望将自定义的写面板添加到这个帖子类型中，因此，在`functions.php`中，我们再次添加以下代码:

```
//We set up a few things, like where your
//theme folder is, etc.
define(
  'MY_WORDPRESS_FOLDER',
  $_SERVER['DOCUMENT_ROOT']
);
define(
  'MY_THEME_FOLDER',
  str_replace("\",'/',dirname(__FILE__))
);
define(
  'MY_THEME_PATH',
  '/' . substr(
    MY_THEME_FOLDER,
    stripos(MY_THEME_FOLDER,'wp-content')
  )
);

//This initializes the write panel.
add_action('admin_init','book_meta_init');

function book_meta_init() {
  //This adds our CSS file,
  //so our write panels look pretty.
  wp_enqueue_style(
    'my_meta_css',
    MY_THEME_PATH . '/custom/book_panel.css'
  );

  //This method is the one that actually adds the
  //write panel, named 'Book Information' to the
  //post type 'books'
  add_meta_box(
    'book_meta',
    'Book Information',
    'book_meta',
    'books',
    'advanced',
    'high'
  );
}

// The function below links the panel
// to the custom fields
// ---------------------------------
function book_meta() {
  global $post;
  //The two variables.
  $author = get_post_meta($post->ID,'author',TRUE);
  $isbn = get_post_meta($post->ID,'isbn',TRUE);

  //Call the write panel HTML
  include(MY_THEME_FOLDER .
      '/custom/book_information.php');

  // create a custom nonce for submit
  // verification later
  echo '';
}

//The function below checks the
//authentication via the nonce, and saves
//it to the database.
function my_meta_save($post_id) {
  // authentication checks
  // make sure data came from our meta box
  if(!wp_verify_nonce(
      $_POST['my_meta_noncename',__FILE__)
  ) return $post_id;
  if (!current_user_can('edit_post', $post_id)) {
    return $post_id;
  }
  // The array of accepted fields for Books
  $accepted_fields['books'] = array(
    'author',
    'isbn'
  );
  $post_type_id = $_POST['post_type'];

  //We loop through the list of fields,
  //and save 'em!
  foreach($accepted_fields[$post_type_id] as $key){
    // Set it to a variable, so it's
    // easier to deal with.
    $custom_field = $_POST[$key];

    //If no data is entered
    if(is_null($custom_field)) {

      //delete the field. No point saving it.
      delete_post_meta($post_id, $key);

      // If it is set (there was already data),
      // and the new data isn't empty, update it.
    }
    elseif(isset($custom_field)
&& !is_null($custom_field))
    {
      // update
     update_post_meta($post_id,$key,$custom_field);

      //Just add the data.
    } else {
      // Add?
      add_post_meta($post_id, $key,
        $custom_field, TRUE);
    }
  }
  return $post_id;
}
```

代码注释应该使这个代码片段的工作变得相当明显，所以进入下一步:为自定义编写面板添加 HTML。在你的主题文件夹中，创建一个名为`custom`的新目录，并添加两个文件:一个 CSS 文件用于设计面板的外观，另一个 HTML 文件用于正确显示字段。CSS 文件应该被命名为`book_panel.css`，并包含以下代码:

```
.book_panel .description {
  display: none;
}

.book_panel label {
  display: block;
  font-weight: bold;
  margin: 6px;
  margin-bottom: 0;
  margin-top: 12px;
}

.book_panel label span {
  display: inline;
  font-weight: normal;
}

.book_panel span {
  color: #999;
  display: block;
}

.book_panel textarea,
.book_panel input[type='text'] {
  margin-bottom: 3px;
  width: 100%;
}

.book_panel h4 {
  color: #999;
  font-size: 1em;
  margin: 15px 6px;
  text-transform:uppercase;
}
```

HTML 文件应该命名为
`book_information.php`，它应该包含以下代码:

```
<div class="book_panel">

This panel contains information
for displaying the book

  <label>Author</label>
  <!-- The php checks if there is existing
       data to pre-fill the input fields -->

  <input type="text" name="author" value="<?php
    if(!empty($author)) echo $author;
  ?>"/>

  <label>ISBN</label>
  <!-- The php checks if there is
    existing data to pre-fill the input fields -->
  <input type="text" name="isbn" value="<?php
    if(!empty($isbn)) echo $isbn;
  ?>"/>
</div>
```

如果您按照步骤操作，当您在“图书”面板中添加图书时，将显示一个新的字段框(包含作者和 ISBN)。要在主题中显示这些字段，通过循环调用帖子并获取定制字段信息:

```
$loop = new WP_Query( array(
  'post_type' => 'books',
  'posts_per_page' => 10 )
);
while ($loop->have_posts()) : $loop->the_post();
  the_title();
  the_content();
  echo 'Author: ' .
get_post_meta($post->ID, "author", true);
  echo 'ISBN: ' .
get_post_meta($post->ID, "isbn", true);
endwhile;
```

就是这样:你的文章将显示自定义信息。如果你想了解更多关于 WordPress 的信息，看看我们最新发布的 [*构建你自己的主题*](https://www.sitepoint.com/books/wordpress1/) 。尽情享受吧！

## 分享这篇文章