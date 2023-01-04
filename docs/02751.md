# 在 WordPress 中为脚本和样式排队

> 原文：<https://www.sitepoint.com/enqueuing-scripts-styles-wordpress/>

在开发 WordPress 插件和主题一段时间后，你开始越来越多地考虑你的主题性能，以及它会给你的客户带来什么问题。其中一个问题是脚本和样式入队。在本文中，我们将介绍脚本入队的基础知识以及不同用例的最佳实践。你可以查看这篇文章，了解关于管理你的 WordPress 资产的初级概述。

![WordPress Enqueuing](img/ba920396a155624590c6a71dd959ad04.png)

## 将脚本和样式入队

如果您需要将一些 CSS 或 JavaScript 代码注入到网站的某些页面中，我们倾向于将它们添加到同一个标记文件中，或者使用指向该文件的直接 URL 来添加它们。

```
// ...
<script type="text/javascript">
    // some JS code
</script>

<style type="text/css">
    // some CSS code
</style>
//...

<script type="text/javascript" src="<?= plugins_url('assets/js/main.js') ?>"></script> 
```

然而，这不是添加脚本的推荐方式，缓存插件不会优化和缓存您的资源来提高网站性能。我们需要使用[管理入队脚本](https://codex.wordpress.org/Plugin_API/Action_Reference/admin_enqueue_scripts)钩子。

```
add_action( 'admin_enqueue_scripts', function($hook) {
    $pluginPrefix = "my-prefix";
    wp_enqueue_script( "{$pluginPrefix}main-js", plugins_url('assets/js/main.js'), ['jquery'] );
} ); 
```

[wp_enqueue_script](https://developer.wordpress.org/reference/functions/wp_enqueue_script/) 方法的第一个参数是脚本处理程序名，我们应该总是在处理程序前面加上前缀，以避免任何冲突，并确保我们的脚本总是被加载。

当前的解决方案并不完美，我们的脚本包含在每个后端页面中。我们可以使用传递给回调函数的`$hook`变量来测试某个页面。

```
add_action( 'admin_enqueue_scripts', function($hook) {
    if ( !in_array($hook, array("options-general.php", "post-new.php")) )
        return;

    $pluginPrefix = "my-prefix";
    wp_enqueue_script( "{$pluginPrefix}main-js", plugins_url('assets/js/main.js'), ['jquery'] );
} ); 
```

现在，我们的脚本将只包含在设置和新的帖子页面中。我们可以更进一步，只在用户创建新页面时包含我们的样式。

```
add_action( 'admin_enqueue_scripts', function($hook) {
    if ( $hook == "post-new.php" && isset($_GET['post_type']) && $_GET['post_type'] == "page" )
    {
        $pluginPrefix = "my-prefix";
        wp_enqueue_script( "{$pluginPrefix}main-js", plugins_url('assets/js/main.js'), ['jquery'] );
    }
} ); 
```

## 删除不必要的脚本

大多数开发人员不关心在需要的时候让他们的脚本入队，这就是为什么我们总是会遇到意想不到的错误和奇怪的样式问题。当注意到脚本抛出错误或引起冲突时，您可以在添加自己的脚本之前删除它，这在调试模式下通常很有用。

```
add_action( 'admin_enqueue_scripts', function($hook) {
    $unautorized_styles = [
        'script1',
        'another-script'
    ];

    foreach ( $unautorized_styles as $handle )
    {
        wp_deregister_style( $handle );
    }

    // enqueue my scripts
} ); 
```

## 使用短代码

大多数插件和主题都提供了一组短代码来与网站进行交互，有时需要在页面中注入脚本。遗憾的是，我们不能像以前测试后端那样测试前端页面。

实现这一点的一个简单方法是在`the_content`过滤器上注册一个回调，并测试它是否包含您的短代码。如果它返回 yes，您可以将您的脚本入队，否则不做任何事情。

```
add_filter( 'the_content', function($content) {
    if ( has_shortcode($content, "hello-shortcode") )
    {
        $pluginPrefix = "my-prefix";
        wp_enqueue_script( "{$pluginPrefix}main-js", plugins_url('assets/js/hello-shortcode.js') );
    }
}); 
```

上面的代码工作得很好，并且将只为包含我们的短代码的帖子排队我们的脚本。然而，这种解决方案在高负载网站上有一些严重的性能问题。我们将为这个问题创建一个更复杂的解决方案。

如果你还没有使用 [Composer](http://getcomposer.org/) 来加载你的类，我建议你开始这样做。我们的插件`composer.json`文件如下所示:

```
// composer.json
{
    "name": "Demo plugin",
    "description": "",
    "authors": [
        {
            "name": "Author name",
            "email": "author.name@example.com"
        }
    ],
    "autoload": {
        "psr-4": {
            "ESP\\": "src/"
        }
    }
} 
```

`src/shortcodes/HelloShortcode.php`类保存了我们的短代码定义。

```
// src/shortcodes/HelloShortcode.php

namespace ESP\Shortcodes;

class HelloShortcode
{
    static $loaded;

    public $name;

    public function __construct()
    {
        $this->name = "hello-shortcode";
    }

    public function run()
    {
        static::$loaded = true;

        return "Hello shortcode!";
    }

    public function enqueueScripts()
    {
        if ( static::$loaded == false )
            return;

        $pluginPrefix = "my-prefix";
        wp_enqueue_script( "{$pluginPrefix}main-js", plugins_url('assets/js/hello-shortcode.js') );
    }
} 
```

`run`方法将返回我们的短代码的 HTML 标记，并在使用短代码时将`loaded`静态属性设置为`true`。

当插件被加载时,`enqueueScripts`方法会将我们的样式排队。剩下的部分将注册我们的短代码，以便在帖子内容中被识别。

```
// plugin-file.php

require_once __DIR__."/vendor/autoload.php";

$shortcodes = [
    'ESP\\Shortcodes\\HelloShortcode'
];

foreach ($shortcodes as $shortcode) {
    $shortcode = new $shortcode;
    add_shortcode($shortcode->name, [ $shortcode, 'run' ]);
}

add_action('wp_footer', function() use($shortcodes) {
    foreach ($shortcodes as $shortcode) {
        (new $shortcode)->enqueueScripts();
    }
}); 
```

当然，我们可以避免将这些代码放在主插件文件中，但是我们会让事情简单易读。

`$shortcodes`变量保存了一个可用短码的列表。第一个循环将注册我们的短代码，并使`run`方法成为处理程序。接下来，在关闭`body`标签之前，我们将使用 [`wp_footer`](https://codex.wordpress.org/Plugin_API/Action_Reference/wp_footer) 钩子将我们的短代码脚本排队。

现在，您可以创建一个包含我们的短代码的新页面，并检查我们的脚本是否正确加载。

## 结论

这篇短文总结了如何让你的插件和主题脚本入队，以及避免在你网站的每个页面中加载它们的最好方法。如果你有任何问题或意见，请把它们贴在下面，我会尽我所能回答它们！

## 分享这篇文章