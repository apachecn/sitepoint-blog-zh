# 介绍 SitePoint 随机 Hello Bar 插件

> 原文：<https://www.sitepoint.com/sitepoint-random-hello-bar-wordpress-plugin/>

如果你是一个定期的 SitePoint 读者，你可能已经注意到了一个我们称之为*随机 Hello 栏*的小功能。如果你向下滚动这一页足够远，你应该看到它从屏幕的顶部滑入。它应该是这样的:

![An image showing an example of what the SitePoint Random Hello Bar looks like when deployed](img/1c7b41af83765038798e6c574bbb9f3b.png)

我们觉得这是一种在页面上添加广告、产品公告或其他信息的不显眼的方式，所以我们认为是时候与您分享它了。在这篇文章中，我将带你了解我们是如何把它组合在一起的，然后展示一些例子，说明你如何真正把它变成你自己的。如果你喜欢直接跳到代码，可以在 [GitHub](https://github.com/sitepoint/sp-random-hello-bar) 、 [npm](https://www.npmjs.com/package/sp-hello-bar) 或者 [WordPress 插件目录](https://wordpress.org/plugins/sp-random-hello-bar/)找到。

## WordPress 管理界面

我们插件的入口点是`sp-random-hello-bar.php`，插件的主类在`src/SitePoint/RandomHelloBar.php`。

```
 //sp-random-hello-bar.php

require_once(plugin_dir_path( __FILE__ ).'src/SitePoint/RandomHelloBar.php');

\SitePoint\RandomHelloBar::public_actions();

if (is_admin()) {
  \Sitepoint\RandomHelloBar::admin_actions();
} 
```

```
 //src/SitePoint/RandomHelloBar.php

namespace SitePoint;

class RandomHelloBar {
  const PLUGIN_NAME = 'sp-random-hello-bar';

  private static function get_option($option) {
    return get_option(self::PLUGIN_NAME.'-'.$option);
  }

  private static function update_option($option, $value) {
    return update_option(self::PLUGIN_NAME.'-'.$option, $value);
  }

  private static function delete_option($option) {
    return delete_option(self::PLUGIN_NAME.'-'.$option);
  }

} 
```

为了创建我们的管理用户界面，我们将利用 [WordPress 设置 API](https://developer.wordpress.org/plugins/settings/settings-api/) ，因为它允许半自动管理包含设置表单的管理页面。我们通过`add_options_page`功能在**设置**菜单下增加一个 **SP 随机 Hello 栏**子菜单。然后我们注册`sp-random-hello-bar-enabled`和`sp-random-hello-bar-ads`设置以及它们所属的节。

```
 public static function admin_actions() {
  add_action('admin_init', '\Sitepoint\RandomHelloBar::admin_init');

  add_action('admin_menu', function() {
    add_options_page('SP Random Hello Bar', 'SP Random Hello Bar', 'manage_options', self::PLUGIN_NAME, '\SitePoint\RandomHelloBar::options_page');
  });
}

public static function admin_init() {
  register_setting(self::PLUGIN_NAME.'-settings-group', self::PLUGIN_NAME.'-enabled');
  register_setting(self::PLUGIN_NAME.'-settings-group', self::PLUGIN_NAME.'-ads', '\SitePoint\RandomHelloBar::sanitize_ads');

  add_settings_section(self::PLUGIN_NAME.'-section-one', 'Settings', '\SitePoint\RandomHelloBar::section_one_help', self::PLUGIN_NAME);

  add_settings_field(self::PLUGIN_NAME.'-enabled', 'Enabled', function() {
      $setting = esc_attr(self::get_option('enabled'));
      include dirname( __FILE__ ).'/../views/admin/enabled-fields.php';
  }, self::PLUGIN_NAME, self::PLUGIN_NAME.'-section-one');

  add_settings_section(self::PLUGIN_NAME.'-section-two', 'Hello Bar Ads', '\SitePoint\RandomHelloBar::section_two_help', self::PLUGIN_NAME);

  add_settings_section(self::PLUGIN_NAME.'-field-two', null, '\SitePoint\RandomHelloBar::ad_fields', self::PLUGIN_NAME);
}

public static function  options_page() {
  include dirname( __FILE__ ).'/../views/admin/options_page.php';
} 
```

有趣的一点是我们在`src/views/admin/ad_fields.php`中创建了一个单击删除按钮的小技巧，它使用了一个按钮样式的标签和一个隐藏的复选框。

```
 <label class="button">
  <input type="checkbox" name="-ads[][delete]" value="1" onchange="this.form.submit()" style="display: none;" />Delete
</label> 
```

`sp-random-hello-bar-ads`设置将保存一个值数组，每个值由一个`HTML`和`weight`值组成。你可能已经注意到最后一个参数传递给了`register_setting()`的`sp-random-hello-bar-ads`。这是在保存设置之前调用的 sanitize 回调函数。在这种情况下，它有两个目的。它移除任何无效元素，以及任何标记为删除的元素(即，按钮被单击)。一旦清理完毕，这些值将被传递到`save_weighted_ads`，这样我们就可以保存一些额外的设置:`weighted-keys`和`total-weight`，我们稍后在选择要显示的项目时会用到它们。

```
 public static function sanitize_ads($input) {

  foreach($input as $key => $ad) {
    if(!$ad['html'] || !is_numeric($ad['weight']) || isset($ad['delete'])) {
      unset($input[$key]);
    }
  }

  self::save_weighted_ads($input);

  return $input;
}

public static function save_weighted_ads($ads) {
  $weighted_array = array();
  $total_weight   = 0;

  foreach($ads as $key => $val){
    $total_weight += $val['weight'];
    for($i=0; $i
```

我们现在有了一个可以输入随机 Hello Bar 内容并启用/禁用该功能的功能性 UI。

## Ajax 端点

能够输入随机的 Hello Bar 内容固然很好，但是此时无法按需检索内容。让我们解决这个问题。

首先，我们为登录和注销的用户注册 Ajax 挂钩。

```
 public  static function public_actions() {
  if (!\SitePoint\RandomHelloBar::get_option('enabled')) return;

  add_action('wp_ajax_nopriv_get_random_hello_bar', '\SitePoint\RandomHelloBar::get_random_bar');
  add_action('wp_ajax_get_random_hello_bar', '\SitePoint\RandomHelloBar::get_random_bar');
} 
```

两个钩子都调用下面的方法。

```
 public static function get_random_bar() {
  $weighted_keys = self::get_option('weighted-keys');
  $total_weight  = self::get_option('total-weight');
  $rand          = floor($_POST['rand'] * $total_weight);

  if(!$weighted_keys || !$total_weight || !$_POST['rand']) die();

  $ads = self::get_option('ads');

  if(!$ads) die();

  if(!isset($weighted_keys[$rand])) die();

  echo wp_kses_post($ads[$weighted_keys[$rand]]['html']);

  die();
} 
```

它是如何工作的？假设我们保存了三个小节，如下所示:

1.  html: bar1，重量:2
2.  html: bar2，重量:1
3.  html: bar3，重量:1

我们希望 bar1 显示 50%的时间，bar2 和 bar3 各显示 25%的时间。我们还假设客户端已经发送了一个`0.33`的`$_POST['rand']`值。`$_POST['rand']`应该总是一个从`0`到`1`的值，比如 JavaScript `Math.random()`函数生成的值。我们依赖于从客户端发送的`$_POST['rand']`值，而不是在服务器上生成一个随机数，因为这不太可能被缓存。给定这些值，`get_random_bar()`将按如下方式工作:

```
 $weighted_keys = self::get_option('weighted-keys'); // [0, 0, 1, 2]
$total_weight  = self::get_option('total-weight'); // 4
//we can then turn $_POST['rand'] into a number between 0 and $total-weight -1 by doing
$rand          = floor($_POST['rand'] * $total_weight); // floor(0.33 * 4) = 1

$ads = self::get_option('ads'); // [0 => ['html' => 'bar1', 'weight => 2], 1 => ['html' => 'bar2', 'weight' => 1], 2 => ['html' => 'bar3', 'weight' => 1]]

if(!isset($weighted_keys[$rand])) die(); // $weighted_keys[$rand] = $weighted_keys[1] = 0

//we then grab the value at index 1 of $weighted-keys, in this case 0
//and that is the index we use to retrieve a hello bar content from the saved ads option, which would be bar1
echo wp_kses_post($ads[$weighted_keys[$rand]]['html']); // $ads[0]['html'] = 'bar1' 
```

再举一个例子，如果`$_POST['rand'] = 0.66`:

```
 $rand = 2
$weighted_keys[2] = 1;
$ads[1] = 'bar2'; 
```

## 显示内容

插件的服务器端已经准备好给你随机的 Hello 栏了，所以现在是你请求并显示它们的时候了。

在页面滚动中隐藏和显示随机 Hello 栏的机制都封装在`sp-hello-bar` JavaScript 模块中。它包含在`src/js/SpHelloBar.js`的插件中，也可以在 [npm](https://www.npmjs.com/package/sp-hello-bar) 上获得。该模块绝对没有依赖性(甚至没有 jQuery ),并且被设计得尽可能灵活。由于这个原因，模块本身需要一个 throttle 函数传递给它的构造函数，并由您来获取随机 Hello Bar 内容并将其插入 dom。该模块只涉及启动，隐藏和显示随机 Hello 栏。

由于默认的 WordPress 安装有 jQuery 和 Underscore.js，所以很容易创建一个基本的脚本来获取一个随机的 Hello Bar，将其插入到 dom 中，然后启动`sp-hello-bar`来激活它。它非常简单，我们已经在`public/js/basic.js`的插件中为你包含了这样一个脚本。那个文件是由[巴别塔](https://babeljs.io/)从`src/js/basic.js`的 ES6 源代码编译而来的。

```
 import SpHelloBar        from "./SpHelloBar";
import getRandomHelloBar from "./helpers/getRandomHelloBar";

import "../css/basic.css";

(function(window, $, _) {

  const sph = new SpHelloBar({
    throttle: _.throttle
  });

  getRandomHelloBar($, () => sph.init());

})(window, jQuery, _); 
```

正如您所看到的，这个脚本构造了一个从[下划线. js](http://underscorejs.org/) 传入`throttle`函数的`SpHelloBar`实例(throttle 函数用于调节窗口大小和滚动事件)。然后它调用助手函数`getRandomHelloBar`:

```
 export default function ($, cb) {
  $.ajax({
    type : "POST",
    url  : ajax_object.ajax_url,
    data : {
      action : "get_random_hello_bar",
      rand   : Math.random()
    },
    success: function(data, textStatus, XMLHttpRequest) {
      $("body").prepend(data);
      cb();
    }
  });
} 
```

该函数使用`jQuery.ajax`方法从我们的插件中请求随机 Hello Bar 内容。成功后，它将内容插入到主体中，然后触发回调，我们已经将其设置为`SpHelloBar.init()`。

正如我提到的,`sp-hello-bar`模块被设计得灵活且易于扩展。扩展它的一个例子是记录用户何时手动关闭随机 Hello 栏，然后下次不再向他们显示。由于这是一个很好的用户体验，我们也在`public/js/basicStorage.js`中为您提供了这一体验。

```
 import SpHelloBar                          from "./SpHelloBar";
import { checkStorage, disableViaStorage } from "./helpers/disableViaStorage";
import getRandomHelloBar                   from "./helpers/getRandomHelloBar";

(function(window, $, _) {

  const sph = new SpHelloBar({
    throttle: _.throttle
  });

  // extend SpHelloBar
  sph.after("beforeInit", function() {
    checkStorage.call(sph);
  });
  sph.after("onClose", function() {
    disableViaStorage.call(sph);
  });

  getRandomHelloBar($, () => sph.init());

})(window, jQuery, _); 
```

正如你所看到的，它类似于`basic.js`脚本，但是这次我们使用了`after()`方法来挂钩到`beforeInit`和`onClose`阶段。下面显示的`checkStorage`和`disableViaStorage`函数是本地存储的简单包装器。各个生命周期阶段都记录在 [GitHub](https://github.com/sitepoint/sp-random-hello-bar) 上。

```
 const CAN_LOCAL_STORAGE = !!(window && window.localStorage);
const EXPIRE_DAYS       = 14;
const STORAGE_KEY       = 'SpHelloBarDisabled';

function futureDaysInMs(days = EXPIRE_DAYS) {
  return Date.now() + (1000 * 60 * 60 * 24 * days);
}

export function checkStorage() {
  // hello bar may be disabled for the number of days set in EXPIRE_DAYS when user manually closed the bar
  const expiry = (CAN_LOCAL_STORAGE) ? window.localStorage.getItem(STORAGE_KEY) : null;

  if (expiry !== null) {
    const now = Date.now();
    if(expiry > now) {
      this.isEnabled = false;
    } else {
      window.localStorage.removeItem(STORAGE_KEY);
    }
  }
}

export function disableViaStorage() {
  if (CAN_LOCAL_STORAGE) window.localStorage.setItem(STORAGE_KEY, futureDaysInMs());
} 
```

该插件并不假设每个人都想使用我们的基本脚本或基本 CSS，但它很好地通过管理用户界面轻松启用。这可以很容易地通过在我们的选项页面中添加一些设置来实现。

```
 public static function admin_init() {
  register_setting(self::PLUGIN_NAME.'-settings-group', self::PLUGIN_NAME.'-enabled');
  register_setting(self::PLUGIN_NAME.'-settings-group', self::PLUGIN_NAME.'-basic-js');
  register_setting(self::PLUGIN_NAME.'-settings-group', self::PLUGIN_NAME.'-load-css');
  register_setting(self::PLUGIN_NAME.'-settings-group', self::PLUGIN_NAME.'-ads', '\SitePoint\RandomHelloBar::sanitize_ads');

  add_settings_section(self::PLUGIN_NAME.'-section-one', 'Settings', '\SitePoint\RandomHelloBar::section_one_help', self::PLUGIN_NAME);

  add_settings_field(self::PLUGIN_NAME.'-enabled', 'Enabled', function() {
      $setting = esc_attr(self::get_option('enabled'));
      include dirname( __FILE__ ).'/../views/admin/enabled-fields.php';
  }, self::PLUGIN_NAME, self::PLUGIN_NAME.'-section-one');

  add_settings_field(self::PLUGIN_NAME.'-basic-js', 'Enqueue Basic JS', function() {
      $setting = esc_attr(self::get_option('basic-js'));
      include dirname( __FILE__ ).'/../views/admin/basic-js-fields.php';
  }, self::PLUGIN_NAME, self::PLUGIN_NAME.'-section-one');

  add_settings_field(self::PLUGIN_NAME.'-load-css', 'Enqueue Basic CSS', function() {
      $setting = esc_attr(self::get_option('load-css'));
      include dirname( __FILE__ ).'/../views/admin/load-css-fields.php';
  }, self::PLUGIN_NAME, self::PLUGIN_NAME.'-section-one');

  add_settings_section(self::PLUGIN_NAME.'-section-two', 'Hello Bar Ads', '\SitePoint\RandomHelloBar::section_two_help', self::PLUGIN_NAME);

  add_settings_section(self::PLUGIN_NAME.'-field-two', null, '\SitePoint\RandomHelloBar::ad_fields', self::PLUGIN_NAME);
} 
```

然后更新`public_actions`以在适当时将它们排队。

```
 public  static function public_actions() {
  if (!\SitePoint\RandomHelloBar::get_option('enabled')) return;

  add_action('wp_enqueue_scripts', '\SitePoint\RandomHelloBar::enqueuePublicAssets');

  add_action('wp_ajax_nopriv_get_random_hello_bar', '\SitePoint\RandomHelloBar::get_random_bar');
  add_action('wp_ajax_get_random_hello_bar', '\SitePoint\RandomHelloBar::get_random_bar');
}

public static function enqueuePublicAssets() {
  $basic_js = \SitePoint\RandomHelloBar::get_option('basic-js');
  if (in_array($basic_js, array('basic', 'basicStorge'))) {
    wp_enqueue_script(
      self::PLUGIN_NAME.'-basic-script',
      plugins_url('../../public/js/'.$basic_js.'.js' , __FILE__),
      array('jquery', 'underscore')
    );
    wp_localize_script(
      self::PLUGIN_NAME.'-basic-script',
      'ajax_object',
      array('ajax_url' => admin_url( 'admin-ajax.php' ))
    );
  }

  if(\SitePoint\RandomHelloBar::get_option('load-css')) {
    wp_enqueue_style(
      self::PLUGIN_NAME.'-basic-css',
      plugins_url( '../../public/css/basic.css' , __FILE__ )
    );
  }
} 
```

## 让它成为你自己的

虽然插件提供的基本脚本非常有用，但真正的价值在于定制它以满足您的需求。以下是一些可行的建议。

### 仅在指示器存在时加载

提供的基本脚本的一个潜在问题是，它将在所有页面上加载随机 Hello 栏，这可能不是您想要的。例如，如果你改变了你的 WordPress 主题，将`.enableSpHelloBar`添加到你的`single.php`帖子模板的主元素中，你可以使用下面的脚本，并且在特定的帖子页面上只有随机的 Hello 栏。

```
 import SpHelloBar        from "./SpHelloBar";
import getRandomHelloBar from "./helpers/getRandomHelloBar";

import "../css/basic.css";

(function(window, $, _) {

  if (!document.getElementsByClassName('enableSpHelloBar').length) return;

  const sph = new SpHelloBar({
    throttle: _.throttle
  });

  getRandomHelloBar($, () => sph.init());

})(window, jQuery, _); 
```

### 跟踪

另一个常见的用例是跟踪页面中组件的使用。下面的脚本利用`onToggle`和`onClick`事件挂钩来跟踪随机 Hello 栏何时首次显示在页面中，以及其链接何时被单击。

```
 import SpHelloBar        from "./SpHelloBar";
import getRandomHelloBar from "./helpers/getRandomHelloBar";
import trackEvent        from './helpers/trackEvent';

import "../css/basic.css";

(function(window, $, _) {

  const sph = new SpHelloBar({
    throttle: _.throttle
  });

  // extend SpHelloBar
  sph.after('onClick', function() {
    trackEvent.call(sph, 'Click');
  });
  sph.after('onToggle', function() {
    trackEvent.call(sph, 'Impression');
  });

  getRandomHelloBar($, () => sph.init());

})(window, jQuery, _); 
```

```
 export default function trackEvent (label) {
  // only track impressions once
  if (!this.impressionTracked && this.isShown && label == 'Impression') {
    this.impressionTracked = true;
  } else if(this.isShown && label == 'Impression') {
    return;
  }

  // check for the Google Analytics global var
  if (typeof(ga) == 'undefined') return;

  ga('send', 'event', 'SP Hello Bar', label, {
    'nonInteraction': true
  });
} 
```

### 随机 Hello Bar 触发器

默认情况下，当页面向下滚动 300 像素时，会显示随机 Hello 栏。以下脚本显示了如何将自定义数据属性添加到页面上其他位置的元素中，并使用该元素来确定随机 Hello 栏的显示位置。例如，您可以将属性添加到主 post 主体之后的元素中。

```
 import SpHelloBar        from "./SpHelloBar";
import getRandomHelloBar from "./helpers/getRandomHelloBar";

import "../css/basic.css";

(function(window, $, _) {

  let targetOffset = 300;
  if(document.querySelector('[data-hellobar="trigger"]')) targetOffset = document.querySelector('[data-hellobar="trigger"]').offsetTop;

  const sph = new SpHelloBar({
    targetOffset,
    throttle: _.throttle
  });

  getRandomHelloBar($, () => sph.init());

})(window, jQuery, _); 
```

### 固定位置标题

在[站点点](https://www.sitepoint.com)上的导航菜单(标题)是静态定位的，因此它们随着页面内容上下滚动。并不是所有的网站都是这样设置的。例如[的 SitePoint 论坛](https://www.sitepoint.com/community)有一个固定的位置标题。当内容上下滚动时，它会固定在页面顶部。

修复非常简单。随机 Hello 栏的工作原理是，隐藏时将其自身定位在屏幕顶部上方(负的顶部值)，然后将顶部值更改为零，这是可见时屏幕的顶部。为了允许一个固定的标题，你只需要调整标题高度的 top 值。

```
 .SpHelloBar {
  ...

  &.u-show {
    top: 56px;
  }
} 
```

## 结论

SitePoint Random Hello Bar 是一个方便的插件，可以直接开箱即用，但可以快速轻松地定制，以满足您网站的独特需求。我们希望你喜欢它，并随时在评论中提出任何问题！

## 分享这篇文章