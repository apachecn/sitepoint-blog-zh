# 理解 WordPress Hook 系统中的名称空间

> 原文：<https://www.sitepoint.com/understanding-namespaces-wordpress-hook-system/>

钩子是 WordPress 开发者的一个基本概念。在之前关于 SitePoint 的文章中，我们已经学习了[什么是钩子以及它们的重要性，两种类型的钩子:`actions`和`filters`](https://www.sitepoint.com/wordpress-hook-system/) 以及它们如何工作的代码示例，以及[触发动作和过滤器事件的另一种方式，以及如何将静态和非静态类方法挂钩到动作和过滤器](https://www.sitepoint.com/triggering-events-in-wordpress/)。

在这篇文章中，我将介绍如何将实例化类(对象)的方法与动作和过滤器挂钩，如何将命名空间类方法集成到挂钩中，在 WordPress 挂钩系统中使用命名空间的注意事项以及解决方案。

![WordPress Hooks and Namespaces](img/2a4e46a6cf55ae01489edd75363e0693.png)

## 挂钩对象方法

假设你的雇主让你为一个大型新闻网站构建一个广告管理器插件，让广告无缝地插入到新闻内容中。这就是你可能着手建立它的方式。

您可以创建一个包含各种广告网络广告代码的方法的`AdManager`类。

```
class AdManager {

    /**
     * AdSense unit code.
     */
    public function adsense() { ?>
        <script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
        <ins class="adsbygoogle"
             style="display:inline-block;width:336px;height:280px"
             data-ad-client="ca-pub-xxxxxxxxxxxxxxxx"
             data-ad-slot="6762452247"></ins>
        <script>
            (adsbygoogle = window.adsbygoogle || []).push({});
        </script>
    <?php }

    /**
     * Buysellads ad code.
     */
    public function buysellads() {
        // ...
    }
} 
```

假设一个网站主题有一个名为`before_post_content`的`action`，它在文章内容显示之前被触发，你想将`adsense`方法与它挂钩，以便在任何文章内容之前显示广告。你会怎么做？

你试图将方法与类外的动作挂钩，不像我们在[第 2 部分](https://www.sitepoint.com/triggering-events-in-wordpress/)中看到的例子，它是在类构造函数中完成的，如下所示:

```
public function __construct() {
        add_action( 'before_post_content', array( $this, 'adsense' ) );
    } 
```

为了在每个帖子的内容之前显示 Google AdSense 广告，要将`adsense`方法挂接到类外部的`before_post_content`动作(可能在活动网站主题的`functions.php`文件中),必须用类的实例替换`$this`。

```
add_action( 'before_post_content', array( new AdManager(), 'adsense' ) ); 
```

假设该类包含一个返回该类的单一实例的方法。

```
class AdManager {

    // ...

    /**
     * Singleton class instance.
     * 
     * @return AdManager
     */
    public static function get_instance() {

        static $instance = null;

        if ( $instance == null ) {
            $instance = new self();
        }

        return $instance;
    }
} 
```

下面是如何将`adsense`方法与`before_post_content`动作挂钩的方法。

```
add_action( 'before_post_content', array( AdManager::get_instance(), 'adsense' ) ); 
```

## 名称空间

WordPress hook 系统是在 WordPress 中没有[名称空间](http://php.net/manual/en/language.namespaces.php)特性的时候开发的。因此，您可能会发现很难将命名空间函数和类方法与`action`和`filter`挂钩。

假设您的`AdManager`类有一个名称空间`SitePoint\Plugin`，如下所示。

```
namespace SitePoint\Plugin;

class AdManager {

    // ...
} 
```

要将`AdManager`类的`adsense`方法与`before_post_content`动作挂钩，必须在类名前加上如下名称空间:

```
add_action( 'before_post_content', array( SitePoint\Plugin\AdManager::get_instance(), 'adsense' ) ); 
```

如果`class`和`add_action`函数调用在由`SitePoint\Plugin\`命名的同一个 PHP 文件中，则没有必要在类名前添加名称空间，因为它们被同一个全局名称空间覆盖。

类的例子已经够多了，现在让我们来看一个简单的函数。

假设您有下面的命名空间函数要与`wp_head`动作挂钩。

```
namespace SitePoint\Plugin;

function google_site_verification() {
    echo '<meta name="google-site-verification" content="ytl89rlFsAzH7dWLs_U2mdlivbrr_jgV4Gq7wClHDUJ8" />';
} 
```

下面是如何通过在函数前添加名称空间来实现这一点:

```
add_action( 'wp_head', 'SitePoint\Plugin\google_site_verification' ); 
```

## 我对钩子系统的命名空间恐惧

在我的[管理栏&仪表板访问控制](https://wordpress.org/plugins/admin-bar-dashboard-control/)插件中，我注册了一个[卸载挂钩](https://developer.wordpress.org/reference/functions/register_uninstall_hook/)，当插件被卸载时，它会删除插件选项。

像下面几行代码这样简单的事情应该不成问题，其中`PP_Admin_Bar_Control`是类名，`on_uninstall`是卸载时调用的方法。

```
register_uninstall_hook( __FILE__, array( 'PP_Admin_Bar_Control', 'on_uninstall' ) ); 
```

为了确保它的工作，我尝试卸载插件，看看插件选项是否会被删除，但令我惊讶的是，我得到了以下错误。

```
The plugin generated 2137 characters of unexpected output during activation. 
```

请注意，下面是如何用名称空间`ProfilePress\PP_Admin_Bar_Control`定义类和`register_uninstall_hook`函数的。

```
namespace ProfilePress\PP_Admin_Bar_Control;

register_uninstall_hook( __FILE__, array( 'PP_Admin_Bar_Control', 'on_uninstall' ) );

class PP_Admin_Bar_Control {

    // ...

    /** Callback to run when the uninstalled hook is called. */
    public static function on_uninstall() {
        if ( ! current_user_can( 'activate_plugins' ) ) {
            return;
        }

        delete_option( 'abdc_options' );
    }

    // ...
} 
```

你能找出卸载插件时没有触发`on_uninstall`类方法的原因吗？

您可能认为既然`register_uninstall_hook`函数是在名称空间下定义的，那么该类应该被它覆盖，但事实并非如此。您仍然需要将命名空间添加到类的前面，如下所示。

```
register_uninstall_hook( __FILE__, array( 'ProfilePress\PP_Admin_Bar_Control\PP_Admin_Bar_Control', 'on_uninstall' ) ); 
```

我实际上很难解决这个问题。我决定我不想让你经历和我一样的压力和头痛。

## 结论

像这样的怪癖让一些开发者畏缩不前，远离 WordPress。我们不应该忘记 WordPress 是在 PHP 缺乏所有语言改进和功能的时候开发的。我总是试图找出如何规避这些怪癖，然后教给人们。

我希望我已经揭开了 WordPress 中钩子系统的神秘面纱。如果你有任何问题或贡献，请在评论中告诉我们。

## 分享这篇文章