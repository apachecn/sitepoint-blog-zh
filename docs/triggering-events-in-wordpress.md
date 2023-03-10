# WordPress 中触发事件的替代方式

> 原文：<https://www.sitepoint.com/triggering-events-in-wordpress/>

在关于 WordPress 钩子系统的这个系列的[的第一部分中，我们学习了 WordPress 钩子系统和两种类型的钩子`actions`和`filters`以及它们如何工作的一些代码示例。](https://www.sitepoint.com/wordpress-hook-system/)

在本系列的第二部分中，我们将学习在 WordPress 中触发事件的替代方法，以及如何将静态和非静态类方法与`actions`和`filters`挂钩。

![WordPress Hooks](img/46192d8a44f9e9ddf70100567a0d3945.png)

在前一篇文章中，我做了如下陈述:

> 在 WordPress 执行的不同阶段，通常使用 do_actions()和 apply_filters() PHP 函数触发大量事件。这些事件可以通过 add_action()和 add_filter()订阅或挂钩。
> 
> 请注意我对“通常”这个词的使用。还有其他方式可以触发事件。我们将在本教程的第二部分探讨这个问题。

触发事件的其他方式是通过`action`挂钩的`do_action_ref_array()`功能和`filter`挂钩的`apply_filters_ref_array()`功能。

`do_action()`、`do_action_ref_array()`和`apply_filters()`、`apply_filters_ref_array()`都是一样的，每一对分别用来执行与特定动作和过滤器挂钩的功能。区别在于他们如何指定他们的论点。

与`do_action()`和`apply_filters()`不同，`do_action_ref_array()`和`apply_filters_ref_array()`将它们的参数指定为一个数组。

让我们来看一些代码示例，以便更好地理解它们是如何工作的。

## 代码示例

在用户档案更新错误返回和档案更新之前，在 WordPress 中触发动作`user_profile_update_errors`。

假设您在 WordPress 用户档案中添加了一个[自定义字段，并希望在 WordPress 将数据保存到数据库之前验证其输入。这是你需要的钩子。](http://profilepress.net/custom-fields-wordpress-user-profile/)

以下是它在 WordPress core 中的定义。

```
 /**
     * Fires before user profile update errors are returned.
     *
     * @since 2.8.0
     *
     * @param WP_Error &$errors WP_Error object, passed by reference.
     * @param bool     $update  Whether this is a user update.
     * @param WP_User  &$user   WP_User object, passed by reference.
     */
    do_action_ref_array( 'user_profile_update_errors', array( &$errors, $update, &$user ) ); 
```

下面的代码确保名为 city 的自定义配置文件字段(用户可以在其中输入他们的城市)不为空。如果是，将显示一个错误。

```
add_action( 'user_profile_update_errors', function ( $errors, $update, $user ) {
        if ( empty( $_POST['city'] ) ) {
            $errors->add( 'city_empty', __( 'City field cannot be left empty.' ) );
        }
    }, 10, 3 ); 
```

我们来看一个`apply_filters_ref_array()`的代码例子。

下面的代码挂钩到 bbPress 中的`bp_activity_permalink_redirect_url`过滤器，在单个活动项发生重定向之前，将预期的重定向 URL 修改为`http://website.com/custom-page/`。

```
add_filter( 'bp_activity_permalink_redirect_url', function ( $redirect, $activity ) {
        $redirect = 'http://website.com/custom-page/';

        return $redirect;

    }, 10, 2 ); 
```

## 何时使用`do_action_ref_array()`和`apply_filters_ref_array()`

在使你的插件或主题可被其他开发者扩展时，当有许多额外的变量或值要传递给挂钩到`action`和`filter`的函数时，`do_action_ref_array()`和`apply_filters_ref_array()`比`do_action()`和`apply_filters()`更好。

举个例子，你正在开发一个用户注册插件，你定义了一个在注册完成后触发的动作，注册用户的用户名、电子邮件地址、名字、姓氏、地址、城市、州和国家可供与之挂钩的函数使用。下面是使用`do_action()`时代码的样子

```
do_action('after_user_registration_completed', $username, $email, $firstname, $lastname, $address, $city, $state, $country); 
```

注意参数列表是如何让代码行变得又长又难看的。现在把上面的和下面的`do_action_ref_array()`对比一下。

```
do_action_ref_array(
    'after_user_registration_completed',
    array(
        $username,
        $email,
        $firstname,
        $lastname,
        $address,
        $city,
        $state,
        $country
    )
); 
```

## 将类方法与动作和过滤器挂钩

我们一直在研究的代码示例是关于将命名和匿名函数与`action`和`filter`挂钩。

因此，让我们看看如何通过`add_action()`和`add_filter()`在一个类中调用或包含钩子，以便在 WordPress 执行期间进行处理，以及如何将类方法(静态和非静态)与`actions`和`filters`挂钩。

大多数 WordPress 开发者将所有的`add_action()`和`add_filter()`函数调用包含在他们的类构造函数中，然后在实例化时执行，如下所示:

```
class DemoPlugin {
    public function __construct() {
        add_action( 'wp_head', array( $this, 'google_site_verification' ) );

        add_filter( 'the_content', array( $this, 'we_love_sitepoint' ) );
    }

    /**
     * Include Google site verification meta tag to WordPress header.
     */
    public function google_site_verification() {
        echo '<meta name="google-site-verification" content="ytl89rlFsAzH7dWLs_U2mdlivbrr_jgV4Gq7wClHDUJ8" />';
    }

    /**
     * Append and prepend the text "We love SitePoint" to every post content.
     *
     * @param string $content
     *
     * @return string
     */
    public function we_love_sitepoint( $content ) {
        $text    = sprintf( '<div class="notice alert">%s</div>', __( 'We love SitePoint', 'sp' ) );
        $content = $text . $content . $text;

        return $content;
    }
}

new DemoPlugin(); 
```

从上面的代码片段中，您会发现函数和类方法与`action`或`filter`挂钩的方式有所不同，因为对于类方法来说，`add_action()`和`add_filter()`的第二个参数是一个由`$this`(对当前对象的引用)和方法名组成的数组。

对于静态方法，使用类名而不是`$this`。

```
class DemoPlugin {
    public function __construct() {
        add_action( 'wp_head', array( 'DemoPlugin', 'google_site_verification' ) );
    }

    /**
     * Include Google site verification meta tag to WordPress header.
     */
    public static function google_site_verification() {
        echo '<meta name="google-site-verification" content="ytl89rlFsAzH7dWLs_U2mdlivbrr_jgV4Gq7wClHDUJ8" />';
    }
}

new DemoPlugin(); 
```

上面的方法包括了你想与过滤器或动作挂钩的每个静态方法的类名，这违反了[不要重复自己(DRY)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) 的原则，因此会使重构变得困难。

相反，使用常量`__CLASS__`返回声明它的类名。

```
class DemoPlugin {
    public function __construct() {
        add_action( 'wp_head', array( __CLASS__, 'google_site_verification' ) );
    }

    // ...
}

new DemoPlugin(); 
```

虽然我强烈反对这样做，但是还有一种方法可以将静态方法包含到钩子中。

```
class DemoPlugin {
    public function __construct() {
        add_action( 'wp_head', 'DemoPlugin::google_site_verification' );
    }

    // ...
}

new DemoPlugin(); 
```

我看到一些开发人员创建了一个静态类方法，当被调用时，初始化/执行与动作或过滤器挂钩的静态方法，而不是将所有的`add_action()`和`add_filter()`函数调用包含在一个类构造函数中。

```
class DemoPlugin {
    public static function init() {
        add_action( 'wp_head', array( __CLASS__, 'google_site_verification' ) );
        add_filter( 'the_content', array( __CLASS__, 'we_love_sitepoint' ) );
    }

    // ...
}

DemoPlugin::init(); 
```

在这种方法中，所有挂钩到`actions`和`filters`的方法必须是静态的，因为`$this`在静态上下文中是不可访问的。

## 摘要

在关于 WordPress 钩子系列的第二部分中，我们学习了触发动作和过滤器事件的另一种方法，何时使用它们，最后，如何将静态和非静态类方法与动作和过滤器挂钩。

在结论部分，我们将研究如何将一个实例化的类(对象)的方法挂钩到一个动作和过滤器，如何将一个命名空间的类方法集成到一个挂钩中，以及在 WordPress 挂钩系统中使用命名空间的注意事项。

编码快乐！

## 分享这篇文章