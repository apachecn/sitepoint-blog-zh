# 十分钟之内在 WordPress 中创建一个动态小部件

> 原文：<https://www.sitepoint.com/create-wordpress-widget/>

你是否曾试图在 WordPress 中找到特定用途的功能，但却找不到一个插件或小工具来完成你的工作？这篇文章介绍了如何创建一个 WordPress 小部件，即使你对编码了解有限。

WordPress 小部件是代码块——静态的，动态的，或者两者的结合——可以添加到你的 WordPress 站点的任何特定区域。WordPress 中有许多内置的小部件——比如标签云和类别——但是这篇文章可以帮助你在短时间内从头开始创建一个小部件。读完这篇文章可能比你获得知识后创建小部件需要更长的时间！

出于说明的目的，我们正在考虑创建一个带有动态内容的小部件，其形式为[板球](https://en.wikipedia.org/wiki/Cricket)比赛的实时数据。创建动态内容的目的是让你熟悉与 WordPress 小部件相关的各种功能。

### 先决条件

在你开始任何代码之前，请确保你备份了你的代码，因为你可能会以白屏死机而告终。或者，你可能想用 Git 探索 WordPress 的[版本控制。](https://www.sitepoint.com/revisr-git-for-wordpress/)

此外，在尝试创建一个小部件之前，建议您对 PHP 中的 [OOP 有所了解。](http://php.net/manual/en/language.oop5.php)

本教程是在 WordPress 版本 4.9.8 上创建的。

## 代码放在哪里？

在我们讨论开发 WordPress 小部件的细微差别之前，让我们考虑一下在哪里保存代码。答案在于开发这个小部件的目的。如果您希望小部件只出现在特定的主题中，您可以将代码放在主题的`functions.php`文件中。但是，这使得只有在主题处于活动状态时才可以访问小部件。

本教程着重于创建一个可以在你的网站主题中访问的小部件。因此，我们将创建一个新的插件，其中包含显示来自 API 的现场板球比赛列表的小部件。

## 小部件开发基础

要在 WordPress 中创建一个小部件，需要扩展`WP_Widget`类。在 widget 类中，您可以创建一个函数列表:

*   构造函数
*   显示微件内容的`widget`功能
*   如果您需要定义一个接受输入的表单，可以使用`form`函数
*   如果您需要更新小工具的设置，则使用`update`功能。

在显示 cricket 提要的例子中，我们只需要定义前两个函数。此外，一旦我们创建了 widget 类，我们需要用`register_widget`函数注册它。

现在让我们继续在 WordPress 中创建一个基本插件，然后使用一个 API 来生成动态内容。

## 基础:Hello World 小部件

在创建小部件之前，需要创建一个空插件。打开`/wp-content/plugins/`，用你的插件名的[后缀](https://prettylinks.com/2018/03/url-slugs/)创建一个新目录。在这个目录中，创建一个包含以下内容的`index.php`文件。

### 定义一个空插件

```
<?php
/*
Plugin Name: Live Score Custom
Plugin URI: https://www.sitepoint.com/
Description: Get and display sports feeds
Version: 1.0
Author: Shaumik
Author URI: https://www.sitepoint.com/
License: GPL2
*/
?> 
```

这本质上是一个带有注释的空白 PHP 文件，正如在创建新插件的 [WordPress Codex 页面上所建议的那样。当在 WP 管理上显示关于插件的信息时，WordPress 会阅读这些评论。](https://codex.wordpress.org/Writing_a_Plugin)

一旦你创建了带注释的空插件，你可以看到你的插件在 WP 管理的插件列表中是活跃的。在继续下一步之前激活插件。

![Plugin list on WP Admin after creating empty plugin](img/00357cc2dc23cf814acb2aa932ce6c23.png)Plugin list on WP Admin after creating empty plugin

### 在插件中定义一个小部件

下一步是创建一个示例小部件代码。如前所述，我们将扩展`WP_Widget`类。定义了类之后，我们将使用`register_widget`方法注册小部件:

```
class My_Custom_Widget extends WP_Widget {

    public function __construct() {
    }

    public function widget( $args, $instance ) {
        // Define the widget
    }
}
// Register the widget
function my_register_custom_widget() {
    register_widget( 'My_Custom_Widget' );
}
add_action( 'widgets_init', 'my_register_custom_widget' ); 
```

现在我们的框架代码已经准备好了，让我们填充构造函数和`widget`函数:

```
public function __construct() {
    // Define the constructor

    $options = array(
        'classname' => 'custom_livescore_widget',
        'description' => 'A live score widget',
    );

    parent::__construct(
        'live_score_widget', 'Live Score Widget', $options
    );
} 
```

构造函数可以从父构造函数扩展而来。在选项中，我们还可以提供一个 CSS `class`,它在显示时被添加到 HTML DOM 中的整个 widget 元素。接下来，让我们定义小部件显示选项:

```
public function widget( $args, $instance ) {
    // Keep this line
    echo $args['before_widget'];

    echo $args['before_title'] . apply_filters( 'widget_title', 'Live Criket Matches' ) . $args['after_title'];
    echo 'Hello, World!';

    // Keep this line
    echo $args['after_widget'];
} 
```

在`widget`类中，我们定义需要在小部件中显示什么。首先，我们添加一个小部件标题“现场板球比赛”，然后添加一个简单的“Hello，World！”

一旦代码被保存，让我们回到 WP 管理中的**外观>部件**并将新创建的部件添加到页面的一部分。

![Adding Widget in WP Admin](img/fdc5280b6fdd021f40051adf3c3dee90.png)Adding Widget in WP Admin

添加完小部件后，您可以刷新主页并检查小部件是否出现。

![Hello World widget in the home page](img/10a162c483609584db470ce8616d74bd.png)Hello World widget in the home page

您可能会注意到，我们在其中定义小部件的类已经添加到了小部件元素中。如果需要，您可以更改小部件的样式。如果你感兴趣，看看这篇关于如何[定制 WordPress 主题](https://www.sitepoint.com/customize-sitepoint-wordpress-base-theme/)风格的教程。

## 添加实时乐谱选项

到目前为止，我们已经成功地创建了一个与插件相关联的小部件，在 WP Admin 上注册了它，并在我们的主页上显示了它。下一步是从一个 API 获取实时分数提要。

作为演示，我们将使用由[criapi](http://www.cricapi.com/)开发的 API。在免费计划下，您每天可以发出 100 个 API 请求。注册后，您会得到一个 API 密钥，当您向站点发出请求时需要这个密钥。我们将使用“新匹配 API”选项来获取小部件的数据。

需要将 GET 请求发送给带有`apikey`参数的 Match API，以获得匹配列表。获得 API 密钥后，您可以在浏览器上访问以下 URL 来验证 API 是否正常工作:

```
http://cricapi.com/api/matches?apikey=api_key 
```

响应是一个 JSON 字符串，带有匹配列表和一些元数据。我们可以通过修改我们的`widget`函数来模拟请求并提取 PHP 中的数据:

```
public function widget( $args, $instance ) {

// before and after widget arguments are defined by themes
echo $args['before_widget'];

echo $args['before_title'] . apply_filters( 'widget_title', 'Live Criket Matches' ) . $args['after_title'];

// This is where you run the code and display the output
$api_key = 'ONkuR4grRDFyUzOEYoSzeUQolbx2'
$api_url  = "http://cricapi.com/api/matches?apikey=" . $api_key;

//  Initiate curl
$ch = curl_init();
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);

// Will return the response, if false it print the response
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
// Set the url
curl_setopt($ch, CURLOPT_URL, $api_url);
// Execute
$result = curl_exec($ch);
// Closing
curl_close($ch);

// Will dump a json - you can save in variable and use the json
$cricketMatches = json_decode($result);

foreach($cricketMatches->matches as $item) {
    if ($item->matchStarted == true) {
        echo $item->{'team-1'}, ' vs ', $item->{'team-2'}, '<br />';
    }
}

echo $args['after_widget'];

} 
```

以下是我们在代码中遵循的步骤:

*   我们通过 cURL 向 URL 发送一个请求，禁用安全选项(注意 cricAPI URL 是`http`)
*   我们将解码后的 JSON 响应保存在变量`$cricketMatches`中
*   我们得到一个比赛列表，显示那些已经开始的比赛。

现在让我们看一下主页，看看是否显示了所需的数据。

![Widget displaying list of cricket matches](img/f4b4e51198e59ed2b26f07971deba85e.png)Widget displaying list of cricket matches in home page

可以进一步研究数据以显示特定选项。例如，您可以通过向 cricket score API 发送带有您的 API 键和匹配 ID 的请求来获得分数。

### 打包和共享插件

给你。您已经成功地创建了列出现场板球比赛的小部件！您可以通过创建一个`live-score-custom-plugin`目录的 zip 文件，并将该 zip 文件上传到**插件>添加新的>上传插件**来打包插件。

## 最后的想法

在本教程中，我们通过一个插件学习了在 WordPress 中创建一个小部件的基础知识，并提取了体育直播来显示数据。

## 分享这篇文章