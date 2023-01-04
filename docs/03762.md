# 为 WordPress 创建一个分享最多的帖子插件

> 原文：<https://www.sitepoint.com/create-a-most-shared-posts-plugin-for-wordpress/>

在博客中显示分享数量最多的帖子列表是改善网站用户导航的好方法。在 WordPress 的例子中，一个小部件是实现这一点的理想场所。那些在阅读完当前帖子后不知道该去哪里的用户可能会查看并希望参与到你的博客中分享最多的帖子中。这些通常也是页面浏览量最大的网站，可能会吸引更多的读者。

在本教程中，我们将创建一个基本插件，在一个小部件中显示最常分享的帖子。尽管有很多插件可以用于这个目的，但是它们中的很多都不支持缓存。在本文中，我们将构建的插件是轻量级的，它将缓存过去六个小时内分享最多的前十篇帖子。

![Most Shared Posts Plugin](img/4fef2899e26fc28db564c3169a55ec5c.png)

## 插件目录和文件

从插件开发开始，在你的 **wp-content/plugins** 目录下创建一个名为 **most-shared-widget** 的目录，然后在里面创建一个名为【most-shared-widget.php】的文件。

在**most-shared-widget.php**文件中，添加以下文本使插件可安装。

```
<?php

/*
Plugin Name: Most Shared Posts Widget
Plugin URI: https://www.sitepoint.com
Description: Displays most shared posts in an widget
Author: Narayan Prusty
*/
```

## 怎么求总股？

为了这个插件，我们将只检索在 Twitter 和脸书的帖子的总份额。Twitter 和脸书提供了 URL，我们可以通过传递一个 URL 向其发出 GET 请求，以找到它的总份额。

在脸书上查找总股的网址是:
**【https://api.facebook.com/method/links.getStats?format=json】&URLs = https://www . sitepoint . com**

而在 Twitter 上查找总股份的网址是:
**【http://urls.api.twitter.com/1/urls/count.json? URL = https://www . site point . com**

这两个请求都以 JSON 格式发回数据。以下是回应脸书请求的一个例子:

```
[{"url":"http:\/\/sitepoint.com","normalized_url":"http:\/\/www.sitepoint.com\/","share_count":1343,"like_count":1632,"comment_count":207,"total_count":3182,"click_count":138,"comments_fbid":"501562723433","commentsbox_count":0}]
```

同样，Twitter 的回应看起来是这样的:

```
{"count":72,"url":"http:\/\/sitepoint.com\/"}
```

## 检索和存储总份额

我们可以使用 [WordPress HTTP API](https://www.sitepoint.com/the-wordpress-http-api/) 向脸书和 Twitter 的 URL 发出 HTTP 请求。然后将检索到的帖子计数存储为帖子元数据。

为了与脸书和 Twitter API 服务器保持友好，我们不会一次检索所有帖子的总分享数，而是在用户查看单个帖子时进行检索。我们还将确保在 6 小时内只检索一篇文章的总分享量，因为分享量在这段时间内不太可能有太大变化。

下面是负责决定何时检索和存储每篇文章的总股份数的代码。将这段代码放到你的**most-shared-widget.php**文件中。

```
function msp_is_post()
{
  if(is_single() && !is_attachment())
  {
    global $post;

    $last_update = get_post_meta($post->ID, "msp_last_update", true);

    if($last_update)
    {
      if(time() - 21600 > $last_update)
      {
        msp_update($post->ID);
      }
    }
    else
    {
      msp_update($post->ID);
    }
  }
}

add_action("wp", "msp_is_post");
```

上面的代码是这样工作的:

*   首先，我们为`wp`动作注册了一个回调。
*   然后我们检查一个帖子是否被浏览。如果是，那么我们继续。
*   接下来，我们检索该帖子的总分享数。如果它不存在，那么我们简单地调用`msp_update`函数，该函数负责检索和存储每个帖子的总份额。如果它存在，那么我们检查它最后一次被检索的时间；如果它是在 6 个小时之前被检索的，那么我们再次更新它，否则我们通过返回函数简单地保留当前值。

下面是`msp_update`函数的实现，它负责检索帖子的总分享数，并将它们存储为帖子元数据:

```
function msp_update($id)
{
  $url = get_permalink($id);

  //facebook shares
  $response = wp_remote_get("https://api.facebook.com/method/links.getStats?format=json&urls=" . $url);
  $body = $response["body"]; 
  $body = json_decode($body);

  if($body[0]->share_count)
  {
    $facebook_count = $body[0]->share_count;
  }
  else
  {
    $facebook_count = 0;
  }

  //twitter shares
  $response = wp_remote_get("http://urls.api.twitter.com/1/urls/count.json?url=" . $url);
  $body = $response["body"]; 
  $body = json_decode($body);

  if($body->count)
  {
    $twitter_count = $body->count;
  }
  else
  {
    $twitter_count = 0;
  }

  $total = $facebook_count + $twitter_count;

  update_post_meta($id, "msp_share_count", $total);
  update_post_meta($id, "msp_last_update", time());
}
```

我们首先使用 WordPress 提供的`get_permalink()`函数将文章 ID 解析为文章 URL。

然后，我们使用 WordPress HTTP API 检索并解析一篇文章的总份额。最后，我们将脸书和 Twitter 的总分享数相加，并将它们作为元数据存储，同时存储的还有当前时间，它表示最后一次更新的时间。

### 创建小部件

我们将使用一个小部件来显示前 10 个共享帖子。我们将使用 WordPress 提供的 [get_posts()](https://www.sitepoint.com/exploring-the-wordpress-get-posts-function/) 函数找到数据，然后使用 [WordPress Transients API](https://www.sitepoint.com/complete-guide-wordpress-transients-api/) 将结果缓存六个小时。

下面是创建一个简单小部件的代码，这个小部件只显示小部件标题。将该代码放在**most-shared-widget.php**文件中。

```
class Most_Shared_Post_Widget extends WP_Widget 
{
    public function __construct() 
    {
        parent::__construct("Most_Shared_Post_Widget", "Display Most Shared Posts", array("description" => __("This plugin displays ten most shared posts in an widget")));
    }

    public function form($instance) 
    {
        if($instance) 
        {
            $title = esc_attr($instance["title"]);
        } 
        else
        {
            $title = "";
        }
        ?>

        <p>
            <label for="<?php echo $this->get_field_id('title'); ?>"><?php echo "Title"; ?></label>  
            <input class="widefat" id="<?php echo $this->get_field_id('title'); ?>" name="<?php echo $this->get_field_name('title'); ?>" type="text" value="<?php echo $title; ?>" />
        </p>

        <?php
    }

    public function update($new_instance, $old_instance) 
    {
        $instance = $old_instance;
        $instance['title'] = strip_tags($new_instance['title']);
        return $instance;
    }

    public function widget($args, $instance) 
    {
        extract($args);
        $title = apply_filters('widget_title', $instance['title']);
        echo $before_widget;

        if($title) 
        {
            echo $before_title . $title . $after_title;
        }

        msp_display_widget();

        echo $after_widget;
    }
}

function msp_register_most_shared_widget() 
{
  register_widget("Most_Shared_Post_Widget");
}

add_action("widgets_init", "msp_register_most_shared_widget");
```

这里，我们通过扩展类`WP_Widget`创建了一个小部件，并使用`register_widget`函数注册了它。

在显示小部件标题后，我们调用`msp_display_widget`函数，该函数负责检索前 10 篇文章并将它们显示为一个列表。

下面是`msp_display_widget`函数的实现:

```
function msp_display_widget()
{
  $posts = get_transient("msp");
  if($posts === false)
  {
    $args = array("posts_per_page" => 10, "meta_key" => "msp_share_count", "orderby" => "meta_value");
    $posts = get_posts($args);
    $json_posts = json_encode($posts);

    echo "<ul>";

    foreach($posts as $post)
    {
      echo "<li><a href='" . get_permalink($post->ID) . "'>" . $post->post_title . "</a></li>";
    }

    echo "</ul>";

    if(count($posts) >= 10)
    {
      set_transient("msp", $json_posts, 21600);
    }
  }
  else
  {
    $posts = json_decode($posts);

    echo "<ul>";

    foreach($posts as $post)
    {
      echo "<li><a href='" . get_permalink($post->ID) . "'>" . $post->post_title . "</a></li>";
    }

    echo "</ul>";
  }
}
```

`msp_display_widget`函数首先检查前十篇文章是否被存储为瞬态，即它在缓存中查找。如果它在缓存中找到了文章，那么它就简单地解析它们，并将它们显示为一个无序列表。否则，它使用`get_posts`函数检索前 10 篇文章并显示出来。显示之后，它通过设置 6 小时的过期时间将结果存储在缓存中。

您可能想知道缓存结果有什么意义？嗯，`get_posts`正在使用 MySQL `orderby`参数，该参数对帖子的总分享数进行排序并检索前 10 个帖子，这是一个昂贵的 MySQL 操作，因此出于性能原因，最好不要在每次显示小部件时都执行该操作。

如果有超过 10 个帖子有分享计数元数据，我们只缓存结果。这样做是为了防止插件安装后的前 6 个小时内不显示任何内容，因为在插件首次显示时，用户可能还没有请求至少 10 篇帖子。

以下是该 widget 在 2015 主题中的外观:

![Most Shared Posts Plugin](img/4fef2899e26fc28db564c3169a55ec5c.png)

## 结论

在这篇文章中，我已经向你展示了如何轻松地为 WordPress 构建你自己的分享最多的帖子插件。现在，您可以继续进行扩展，以显示图像并添加更多信息。

你可以在这里获得这个插件的完整副本。

请在下面分享你自己插件的经验。

## 分享这篇文章