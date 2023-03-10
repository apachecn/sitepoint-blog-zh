# 定义你自己的 WordPress 小部件

> 原文：<https://www.sitepoint.com/define-your-own-wordpress-widgets/>

Widgets 是一些小的内容，可以放在你的 WordPress 主题的 widgetized 区域中。小部件可能包含一些简单的静态内容，如新闻订阅表单、介绍性文本、广告或一些动态内容，如您的 Twitter 帐户中最近的推文列表。

在这篇文章中，你将学到:

1.  如何使用文本小部件创建简单的小部件而无需编码。
2.  如何创建通过 WordPress 插件创建的动态数据驱动的窗口小部件。

## 静态小部件

静态小部件可以用来在一个区域中放置一些不变的文本或 HTML 代码。这些很容易创建，对于你需要添加到博客边栏的大多数内容来说，它们已经足够了。

### 创建静态小部件

在这个例子中，我们将创建一个小部件，在博客的侧边栏中显示电子邮件时事通讯的订阅表单。登录到 WordPress 管理面板后，按照下面的步骤操作。

将文本小部件拖放到侧边栏中的一个区域，如下图所示:
![Widgets in the WP Dashbaord](img/ff365e986ce0492a1da8fd5be1ad8a7b.png "2")

展开菜单，粘贴需要的文本或 HTML 代码:
![Adding static widget content](img/0827cc2778e621ac5cb9c8a0f54ec797.png "staticwidgetcontent")

点击**保存**按钮。小工具及其内容将出现在网站前端的侧边栏中，如下图所示:
![static widget displayed in the sidebar](img/633665103afbaf305fb509bb86a7e011.png "staticwidgetonsidebar")

## 动态部件

在本教程中，我们将创建一个小部件，用于在侧边栏中显示 Twitter feed。我将通过创建一个插件来说明这个例子，该插件将一个名为“Twitter Feed”的新小部件添加到小部件界面。为了完成相同的主题，将整个代码放在 WordPress 主题的`functions.php`文件中。

### 创建一个 WordPress 插件

在开始定义小部件之前，我们必须首先创建包含定义的插件。

#### 第一步。创建插件文件

在 WordPress 安装目录下的`wp-content/plugins/`目录中创建一个名为`tweet_feed.php`的文件。

#### 步骤 2:添加插件声明注释

```
<?php

/*
Plugin Name: Twitter Feed Widget
Author: Raj Sekharan
Author URI: http://www.nodesman.com
Description: Add a twitter feed to your sidebar.
*/
```

打开 PHP 代码部分，然后放置注释部分。wordpress 在一个 PHP 文件的前几行中寻找上面的注释来识别它是一个 WordPress 插件文件。此评论部分中的信息将用于在插件管理页面中显示插件。

#### 步骤 3:创建 WP_Widget 的子类

使用描述性名称创建预定义类`WP_Widget`的子类。在这种情况下，我将使用`Twitter_Feed_Widget`。子类应该有四个方法:

1.  `widget()`
2.  `update()`
3.  `form()`
4.  `Twitter_Feed_Widget()`(或者无论你的 widget 类的名字是什么)

即使您的服务器运行的是 PHP5，也有必要定义一个与类名同名的函数。

```
class Twitter_Feed_Widget extends WP_Widget
{
    function  widget($args,$instance)
    {       

    }

   function Twitter_Feed_Widget()
   {

   }

   function  update($new_instance,$old_instance)
   {

   }

   function form($instance)
   {

   }
}
```

这形成了一个框架，小部件的整个定义将放在这个框架中。在上述功能集中，只有`Twitter_Feed_Widget()`和`widget()`功能是强制性的。

**小工具选项面板是可选的**

定义`form()`和`update()`功能是可选的。只有当您的小部件需要来自用户的一些配置输入时，它们才是必要的。如果您不想提供任何小部件选项，可以省略它们。在这种情况下，选项面板将如下图所示。


#### **第四步:定义选项面板界面**

WordPress 希望上面定义的`form()`函数将输入字段输出到用户的接收输入。对于 Twitter Feed 小部件，我们将提供以下选项:

1.  小部件的标题
2.  Twitter 用户的用户名
3.  要显示的推文数量。

下面是 Twitter feed 小部件的`form()`函数的定义:

```
function form($config)
{
?>
    <label for="<?php echo $this->get_field_id("title");  ?>">
    <p>Title: <input type="text"  value="<?php echo $config['title']; ?>" name="<?php  echo $this->get_field_name("title"); ?>" id="<?php  echo $this->get_field_id("title") ?>"></p>
    </label>

    <label for="<?php echo $this->get_field_id("username");  ?>">
    <p>Twitter Username: <input  type="text" value="<?php echo $config['username'];  ?>" name="<?php echo  $this->get_field_name("username"); ?>" id="<?php  echo $this->get_field_id("username") ?>"></p>
    </label>

    <label for="<?php echo  $this->get_field_id("num"); ?>">
    <p>Number Of Tweets: <input  type="text" size="3" value="<?php echo  $config['num']; ?>" name="<?php echo  $this->get_field_name("num"); ?>" id="<?php echo  $this->get_field_id("num") ?>"></p>
    </label>
<?php        
}
```

**`form()`功能解释**

1.  该函数接受一个参数-`$config`。这个变量包含这个小部件的配置值。
2.  选项中的字段放在一个通过 AJAX 请求提交的 `<form>`标签中。这意味着不应该在上面的定义中添加表单标签。WordPress 为您处理小部件数据的保存。
3.  `$this->get_field_name()`和`$this->get_field_id()`函数用于生成表单字段的名称，这样就不会与小部件选项页面上的输入字段发生冲突。

#### 步骤 5:保存选项面板设置

保存在上面选项面板中选择的设置的业务逻辑应该放在`save()`函数中。对于我们的 Twitter feed:

```
function update($newinstance,$oldinstance)
{
    $instance =  $oldinstance;
    //update the username
    $instance['username']=  $newinstance['username'];
    $instance['title'] =  $newinstance['title'];
    $num = (int)$newinstance['num'];
    $num = intval($num);
    $num = ($num>1)?$num:1;
    $instance['url'] = "http://api.twitter.com/1/statuses/user_timeline.json?screen_name={$newinstance['username']}&count={$num}";
    $instance['num'] = $num;
    return $instance;
}
```

**`update()`功能解释**

*   该函数有两个参数:

1.  第一个参数——在本例中为`$newinstances`——包含小部件在选项面板中输入时的配置信息。WordPress 自动检测所有的表单元素，并使用它们的名字作为数组元素。
2.  第二个参数——在本例中为`$oldinstance`——拥有小部件在更改设置之前的配置信息。

*   函数的第 4 行更新了小部件的标题。*   该函数的第 8 行更新 Twitter API 用户 timline JSON 提要的 URL。*   第 9 行保存了 tweets 的数量，以确保该值是一个正整数。

**选项面板不支持文件上传**

您不能在小部件的“选项”面板中添加文件字段。如果您的可配置小部件接受用户的文件输入，那么您必须为小部件的选项创建一个单独的管理屏幕，并在小部件配置表单中添加一个 URL 字段。

#### 步骤 6:定义小部件

现在我们将定义小部件本身。WordPress 希望小部件的 HTML 由一个名为`widget()`的函数生成。下面显示了 Twitter 小部件的定义:

```
function widget($args,$instance)
{
     extract($args,EXTR_SKIP);
     if (empty($instance['url']) || empty($instance['username']))
    	  return;
     $title =  ($instance['title'])?$instance['title']:"Latest Tweets";

     $tweets =  $this->get_tweets($instance['url'],$instance['username'],$instance['num']);
     $max = $instance['num'];
     echo $before_widget;
?>
<div class="twitter-feed-widget">
<php echo $before_title; ?>
<strong><?php echo $title; ?></strong>
<php echo $after_title; ?>
<?php
$count=0;
foreach ($tweets as $tweet)
{
    $text = $tweet['tweet'];
?>
<div class="tfw-tweet"><a href="<?php echo $tweet['url'] ?>"><?php echo $text;  ?></a></div><br/>
<?php
    $count++;
    if ($count == $num)
     break;
}
?>
</div>
<?php
echo $after_widget;
}
```

**`widget()`功能解释**

widget 函数有两个参数:`$args`和`$instance`。

1.  $args 变量包含几个主题 relaetd 变量:

1.  `before_widget`–在小工具主体之前打印的 HTML。
2.  > after _ widget–在小部件正文之后打印的 HTML。
3.  `before_title`–在小工具标题前打印的 HTML。
4.  `after_title`–在小工具标题后打印的 HTML。

*   上面提到的变量是由主题作者在`functions.php`文件的主题侧边栏声明中在 WordPress 主题中设置的。
*   变量`$instance`包含这个小部件实例的配置信息，该信息保存在前面步骤中的`update()`函数中。
*   上面的`get_tweets()` 函数将获取在变量`$username`中传递的 twitter 用户的 tweets。tweets 以关联数组的形式返回。该功能的实现如下:

    ```
      function get_tweets($feed_url,$username, $num=10)
       {
           //check if the feed is in the list of feeds
           $feeds = get_option("_tfw_feeds");
           $hash = md5($feed_url);
           $tweets = array();
           if (isset($feeds[$hash]))
           {
               $timeOfLastFetch = intval(@$feeds[$hash]["last_update"]);
               if ($timeOfLastFetch-time() >300) //update the tweets every 5 minutes
               {
                    $whetherUpdateTweets = true;
               }
               else
               {
                     if (is_array($feeds[$hash]['tweets']) && count($feeds[$hash]['tweets'] >= $num))
                     {
                        return $feeds[$hash]['tweets'];
                     }
                     else
                     {
                          $whetherUpdateTweets=true;
                     }
               }
           }
           else
               $whetherUpdateTweets = true;      

           if ($whetherUpdateTweets)
           {
    	       if ($tweetsList = file_get_contents($feed_url))
    	       {
    		   $tweetsList = json_decode($tweetsList);
    		   foreach ($tweetsList as $tweet)
    		   {
    		       $tweets[] = array("tweet"=>$tweet->text,"url"=>"https://twitter.com/#!/{$username}/status/{$tweet->id_str}");
    		   }
    	       }
    	       $hash = md5($feed_url);
    	       $feeds = get_option("_tfw_feeds");
    	       $feeds[$hash] = array("last_update"=>time(),"tweets"=>$tweets);
    	       update_option("_tfw_feeds",$feeds);
           }
           return $tweets;

       }
    ```

*   第一行调用了`extract()`函数。该函数采用一个关联数组。它创建以数组的键命名的变量，并将它们设置为与这些键对应的值。
*   第二行确保如果用户没有保存他们的用户名，小部件根本不会显示。
*   第四行确保`$title`变量总是被设置为可用的字符串。
*   接下来的四行打印上述主题相关变量的内容。
*   `get_tweets()`函数检索 twitter 用户的推文列表。在更新步骤中保存的 RSS 文件名作为参数传递给这个函数。它返回一个关联数组，每个数组都有 tweet 文本和 tweet 的 URL 作为元素。
*   `foreach`循环打印推文。

#### 步骤 7:定义小部件类构造函数

widget 类的构造函数给 WordPress 一些关于 widget 的信息。微件的名称、微件选项面板的尺寸、微件管理页面中微件名称下显示的描述都在此功能中配置。这个构造函数定义了两个数组，然后将它们传递给`WP_Widget`父类。

```
function Twitter_Feed_Widget()
{
   $widget_options = array(
      'classname'=>'widget-tweet',
      'description'=>__('This widget shows the twitter feed for the selected user.')
   );
   $control_options = array(
      'height'=>300,
      'width' =>300
   );
   $this->WP_Widget('twitter_feed_widget','Twitter Feed Widget',$widget_options,$control_options);
}
```

**构造者解释**

1.  父类构造函数的第一个参数是该类定义的小部件的唯一标识符。小部件的内容将包含在一个`<div>`元素中。这个元素的 class 属性将被设置为这个值。
2.  第二个参数是小部件的名称，它将在小部件管理页面中显示给用户。
3.  第三个参数是一个带有以下键的关联数组:
    1.  class name–整个小部件连同标题将被放在一个`<li>`元素中。这个元素的类名将被设置为这个值。
    2.  描述–将在“小部件管理”部分显示的小部件的描述。
4.  第四个参数是一个带有以下键的关联数组:
    1.  Height–小工具选项面板的高度。
    2.  Width–小工具选项面板的宽度。

#### 步骤 8:向 WordPress 注册小部件

定义了 widget 类之后，我们必须让 WordPress 把这个类当作一个 Widget 定义。

首先，我们添加一个附加到`widget_init`钩子的函数:

```
add_action("widgets_init","myplugin_widget_init");
```

WordPress 希望所有想要注册窗口小部件的插件和主题都挂在`widgets_init`钩子上。上面一行代码用`widgets_init`钩子注册了`myplugin_widget_init()`函数。然后，我们在函数体中注册小部件:

```
function myplugin_widget_init()
{
    register_widget("Twitter_Feed_Widget");
}
```

`register_widget()`函数接受一个参数——小部件类的名称。

### 关于 WordPress 小部件的重要说明

**WordPress Widget API 被更改**

在 2.8 版本之前，WordPress 使用了一种不同的方法来定义小部件。您必须将函数名作为回调传递给两个函数——`register_widget_control()`和`register_sidebar_widget()`,而不是定义一个小部件类。

这些函数已被弃用，并将在未来版本中删除。不要使用这种方法创建小部件。但是，你会在现有流行插件的源代码中遇到这些函数。

**缓存小工具输出**

一些动态窗口小部件消耗大量带宽、CPU 和/或其他资源来显示其内容。例如，Twitter Feed 小部件依赖于 Twitter 提供的用户的 Tweet RSS 提要。每次显示小工具时下载提要都会使 Twitter 的服务器超载。这将导致 tweet feed 加载不可靠。

相反，如果提要每 5 或 10 分钟更新一次，Twitter 提要将一直可用。这将避免 Twitter 对你的 WordPress 博客服务器的限制。上面例子中的 Twitter Feed Widget 插件已经实现，这样每 5 分钟才获取一次 Feed。

**添加封闭标记和标识符**

插件生成的小部件将被放置在许多不同主题的区域。尽管你的小工具可能非常有用，但如果它显得格格不入，那将是网站的一个污点。

将小部件显示的所有元素包含在一个`<div>`或`<span>`元素中，并为它们提供适当的`class`和`id` 属性，可以帮助主题定制小部件的外观和感觉，以适应它所处的视觉背景。

## Widgets 让用户的生活更加轻松

WordPress 是世界上最受欢迎的博客平台，因为它简单易用。在实现小部件之前，用户必须打开主题文件，然后在适当的位置插入代码，向侧边栏添加新功能。当大多数博客都非常精通技术的时候，这还不错。

无论你是为客户还是为公众发布创建插件，插件都是一种将控制权交给技术上不如你精通的博客作者的方式，并使你的软件使用体验更好。小部件帮助用户充分利用你的插件，而不用对他们的 WordPress 主题进行危险和令人畏惧的编辑。

## 分享这篇文章