# 为 WordPress 创建一个反广告插件

> 原文：<https://www.sitepoint.com/creating-an-anti-adblock-plugin-for-wordpress/>

编者按:广告屏蔽(以及一般的在线广告)是一个有争议的话题。一些人完全不喜欢广告，并在原则上屏蔽它们，另一些人说这剥夺了出版商的收入。本教程是一个开发人员的妥协尝试。你怎么想呢?请在下面的评论中告诉我们。

在本教程中，我们将创建一个 WordPress 插件，它可以为使用 AdBlock 的用户禁用网站，或者向来自网站同一域的 AdBlock 用户显示替代广告。

![Adblock Ad on Ted Talks](img/915a33050154fb37179764cced597858.png)

如果你的网站完全依赖广告来产生收入，并且用户消耗大量资源，这个插件会很有用。例如，视频共享网站可能希望禁止网站为安装了 AdBlock 的用户播放视频或显示替代广告。如上图所示，TED.com 就是这样一个例子。你可以看到 TED.com 要求你解除对他们网站广告的封锁。

### AdBlock 是如何工作的？

AdBlock 维护各种广告网络域名列表和广告特定关键字列表。

AdBlock 的工作原理是阻止图像、iframe、脚本和 Flash HTTP 请求，这些请求与已知的广告商域名或其列表上的广告商特定关键字相匹配。请求被阻止后，它还会更改被阻止的 HTML 元素的 CSS 属性以隐藏它们。

AdBlock 允许您通过添加过滤器来扩展可以阻止的内容。

亚历克西斯·乌尔里希[写了关于广告拦截技术](https://www.sitepoint.com/ad-blockers-dont-fight-understand/)的文章，如果你有兴趣阅读更多关于这个主题的内容。

### 设置插件目录和文件

这是我们将要创建的插件的目录结构:

```
--alternative
	-alternative.php
	-custom.css
	-index.js

```

`alternative`是插件目录。如果你想改名字，没问题。只要确保你不把它叫做关键字特定阻塞将触发的任何东西，否则`index.js`的入队将失败。

### 创建管理面板菜单项

首先，我们需要添加一个反广告设置页面到 WordPress 管理面板。在创建设置页面之前，我们需要为“设置”父菜单项创建一个子菜单项。

下面是我们将用来创建子菜单项的代码。将其放在`alternative.php`文件中。

```
function add_anti_adblock_menu_item()
   {
      add_submenu_page("options-general.php", "Anti Adblock", "Anti Adblock", "manage_options", "anti-adblock", "anti_adblock_page"); 
   }

   function anti_adblock_page()
   {
   }

   add_action("admin_menu", "add_anti_adblock_menu_item");
```

这段代码创建了一个菜单项，并在其上附加了一个设置页面。

### 创建设置页面

接下来，我们需要使用 [WordPress 设置 API](https://www.sitepoint.com/wordpress-options-panel/) 来用想要的选项填充反广告设置页面。

下面是使用 WordPress 设置 API 在设置页面显示各种选项的代码。将此文件放在`alternative.php`文件中。

```
function anti_adblock_page()
   {
      ?>
         <div class="wrap">
            <h1>Anti Adblock</h1>

            <form method="post" action="options.php">
               <?php
                  settings_fields("anti_adblock_config_section");

                  do_settings_sections("anti-adblock");

                  submit_button(); 
               ?>
            </form>
         </div>

      <?php
   }

   function anti_adblock_settings()
   {
      add_settings_section("anti_adblock_config_section", "", null, "anti-adblock");

      add_settings_field("disable_website", "Do you want to disable website?", "disable_website_checkbox", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("disable_website_url", "Image to display when website is disabled", "disable_website_image_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_code", "Do you want to display alternative ads code", "alternative_ads_checkbox", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_selector_1", "Alternaive Ad Code 1 Selector", "alternative_ads_selector_1_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_code_1", "Alternaive Ad Code 1", "alternative_ads_code_1_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_selector_2", "Alternaive Ad Code 2 Selector", "alternative_ads_selector_2_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_code_2", "Alternaive Ad Code 2", "alternative_ads_code_2_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("custom_css", "Custom CSS", "custom_css_input_field", "anti-adblock", "anti_adblock_config_section");

      register_setting("anti_adblock_config_section", "disable_website");
      register_setting("anti_adblock_config_section", "disable_website_url");
      register_setting("anti_adblock_config_section", "alternative_ads_code");
      register_setting("anti_adblock_config_section", "alternative_ads_selector_1");
      register_setting("anti_adblock_config_section", "alternative_ads_code_1");
      register_setting("anti_adblock_config_section", "alternative_ads_selector_2");
      register_setting("anti_adblock_config_section", "alternative_ads_code_2");
      register_setting("anti_adblock_config_section", "custom_css");
   }

   function disable_website_checkbox()
   {  
      ?>
         <input type="checkbox" name="disable_website" value="1" <?php checked(1, get_option('disable_website'), true); ?> /> Check for Yes
      <?php
   }

   function disable_website_image_input_field()
   {
      ?>
         <input name="disable_website_url" type="txt" value="<?php echo get_option('disable_website_url'); ?>" />
      <?php
   }

   function alternative_ads_checkbox()
   {
      ?>
         <input type="checkbox" name="alternative_ads_code" value="1" <?php checked(1, get_option('alternative_ads_code'), true); ?> /> Check for Yes
      <?php
   }

   function alternative_ads_selector_1_input_field()
   {
      ?>
         <input name="alternative_ads_selector_1" type="txt" value="<?php echo get_option('alternative_ads_selector_1'); ?>" />
      <?php
   }

   function alternative_ads_code_1_input_field()
   {
      ?>
         <textarea name="alternative_ads_code_1"><?php echo get_option("alternative_ads_code_1"); ?></textarea>
      <?php
   }

   function alternative_ads_selector_2_input_field()
   {
      ?>
         <input name="alternative_ads_selector_2" type="txt" value="<?php echo get_option('alternative_ads_selector_2'); ?>" />
      <?php
   }

   function alternative_ads_code_2_input_field()
   {
      ?>
         <textarea name="alternative_ads_code_2"><?php echo get_option("alternative_ads_code_2"); ?></textarea>
      <?php
   }

   function custom_css_input_field()
   {
	  $css = ".anti-adblock-textarea{display: none}" . get_option("custom_css");
      file_put_contents(plugin_dir_path(__FILE__) . "custom.css", $css);

      ?>
         <textarea name="custom_css"><?php echo get_option("custom_css"); ?></textarea>
      <?php  
   }

   add_action("admin_init", "anti_adblock_settings");
```

我们的设置页面应该是这样的:

![Anti AdBlock Setting Page](img/18d7f0cb7c6378b39756f153727657e1.png)

在这里，我们可以选择是显示替代广告，还是完全禁止安装了 AdBlock 的用户访问网站。

我假设你的网站有两个广告位置。如果有更多的字段，请在设置页面中添加更多的字段。

对于替代广告，您首先需要为保存广告的 HTML 元素提供选择器，然后是广告代码。确保广告代码没有任何广告特定的关键字，或者它们的 URL 没有指向另一个广告网络。否则它们也会被阻塞。通过使用浏览器开发工具检查元素，您可以找到广告容器的选择器。这将为您提供 HTML 元素的类名或 id。

最后，我们有一个输入框添加自定义 CSS 样式的替代广告。这个 CSS 被刷新到一个`custom.css`文件中，该文件将被嵌入到前端。

### 将数据从 WordPress 传递到 JavaScript

我们将检测 AdBlock，然后使用 JavaScript 阻止或显示替代广告。为此，我们需要将数据从插件设置传递给 JavaScript。

将数据从 WordPress 传递到 JavaScript 有多种方法，我更喜欢使用 HTML5 数据属性来传递数据。

将以下内容放入`alternative.php`文件。

```
function anti_adblock_footer_code()
   {
      if(get_option("disable_website") == 1)
      {
         ?>
            <span id="anti-adblock-disable-website" data-value="true"></span>
            <span id="anti-adblock-disable-website-url" data-value="<?php echo get_option('disable_website_url'); ?>"></span>
         <?php
      }
      else
      {
         ?>
            <span id="anti-adblock-disable-website" data-value="false"></span>
         <?php  
      }

      if(get_option("alternative_ads_code"))
      {
         //change this if your are adding more fields.
         $count = 2;

         ?>
            <span id="anti-adblock-alternative-ads" data-value="true" data-count="<?php echo $count; ?>"></span>
         <?php

         for($iii = 1; $iii <= $count; $iii++)
         {
            ?>
               <textarea class="anti-adblock-textarea" id="alternative_ads_selector_<?php echo $iii; ?>"><?php echo get_option("alternative_ads_selector_" . $iii); ?></textarea>
               <textarea class="anti-adblock-textarea" id="alternative_ads_code_<?php echo $iii; ?>"><?php echo esc_html(get_option("alternative_ads_code_" . $iii)); ?></textarea>
            <?php
         }
      }
      else
      {
         ?>
            <span id="anti-adblock-alternative-ads" data-value="false"></span>
         <?php
      }
   }

   function anti_adblock_style_script()
   {
      wp_register_style("anti-adblock-custom", plugin_dir_url(__FILE__) . "custom.css");
      wp_enqueue_style("anti-adblock-custom");

      wp_enqueue_script('anti-adblock-script', plugin_dir_url(__FILE__) . "index.js", array("jquery"), '1.0.0', true);
   }

   add_action("wp_footer","anti_adblock_footer_code");
   add_action("wp_enqueue_scripts", "anti_adblock_style_script");
```

### 使用 JavaScript 检测 AdBlock

使用 JavaScript 没有直接检测 AdBlock 的方法。我们必须使用 JavaScript 创建一个沙盒环境，并检查测试广告横幅对用户是否可见。

将下面的 JavaScript 放在`index.js`文件中，它负责检测 AdBlock 的存在。

```
function adblock_detect() {
    var iframe = document.createElement("iframe");
    iframe.height = "1px";
    iframe.width = "1px";
    iframe.id = "ads-text-iframe";
    iframe.src = "https://example.com/ads.html";

    document.body.appendChild(iframe);

    setTimeout(function() {
        var iframe = document.getElementById("ads-text-iframe");
        if (iframe.style.display == "none" || iframe.style.display == "hidden" || iframe.style.visibility == "hidden" || iframe.offsetHeight == 0) {

            adblock_blocking_ads();

            iframe.remove();
        } else {
            iframe.remove();
        }
    }, 100);
}

function adblock_blocking_ads(){}
```

### 使用 JavaScript 禁用网站或显示替代广告

一旦我们检测到 AdBlock 是否屏蔽了我们页面上的广告，我们需要根据插件设置采取行动。

下面是 JavaScript 代码，它根据我们的偏好禁用网站或显示替代广告。

将以下代码添加到`index.js`文件中。

```
function adblock_blocking_ads()
{
  var blockwebsite = document.getElementById("anti-adblock-disable-website").getAttribute("data-value");
  var alternativeads = document.getElementById("anti-adblock-alternative-ads").getAttribute("data-value");

  if(blockwebsite == "true")
  {
    var url = document.getElementById("anti-adblock-disable-website-url").getAttribute("data-value");
    document.body.innerHTML = "<div style='position: fixed; width: 100%; height: 100%; background-color:black; background-repeat: no-repeat; background-position: center center; background-image: url(" + url + ");'></div>";
  }
  else if(alternativeads == "true")
  {
    var count = document.getElementById("anti-adblock-alternative-ads").getAttribute("data-count");

    for(var iii = 1; iii <= count; iii++)
    {
      var selector = document.querySelector("#alternative_ads_selector_" + iii).innerHTML;

      if(selector != null)
      {
        document.querySelector(selector).innerHTML = htmlDecode(document.querySelector("#alternative_ads_code_" + iii).innerHTML);
      }
    }
  }
}

function htmlDecode(input) {
  var e = document.createElement('div');
  e.innerHTML = input;
  return e.childNodes.length === 0 ? "" : e.childNodes[0].nodeValue;
}

window.addEventListener("load", function(){
  adblock_detect();
}, false);
```

## 我们完整的插件源代码

概括地说，我们的插件需要以下文件:

```
--alternative
	-alternative.php
	-custom.css
	-index.js

```

下面是我们的 AdBlock 插件的完整源代码。

### alternative.php

```
<?php

   /*
      Plugin Name: Anti AdBlock
      Plugin URI: https://www.sitepoint.com/
      Description: This plugin lets you display alternative ads and also lets you disable website.
      Version: 1.0
      Author: Narayan Prusty
      Author URI: http://qnimate.com
      License: GPL2
   */

   function add_anti_adblock_menu_item()
   {
      add_submenu_page("options-general.php", "Anti Adblock", "Anti Adblock", "manage_options", "anti-adblock", "anti_adblock_page"); 
   }

   function anti_adblock_page()
   {
      ?>
         <div class="wrap">
            <h1>Anti Adblock</h1>

            <form method="post" action="options.php">
               <?php
                  settings_fields("anti_adblock_config_section");

                  do_settings_sections("anti-adblock");

                  submit_button(); 
               ?>
            </form>
         </div>

      <?php
   }

   add_action("admin_menu", "add_anti_adblock_menu_item");

   function anti_adblock_settings()
   {
      add_settings_section("anti_adblock_config_section", "", null, "anti-adblock");

      add_settings_field("disable_website", "Do you want to disable website?", "disable_website_checkbox", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("disable_website_url", "Image to display when website is disabled", "disable_website_image_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_code", "Do you want to display alternative ads code", "alternative_ads_checkbox", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_selector_1", "Alternaive Ad Code 1 Selector", "alternative_ads_selector_1_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_code_1", "Alternaive Ad Code 1", "alternative_ads_code_1_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_selector_2", "Alternaive Ad Code 2 Selector", "alternative_ads_selector_2_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("alternative_ads_code_2", "Alternaive Ad Code 2", "alternative_ads_code_2_input_field", "anti-adblock", "anti_adblock_config_section");
      add_settings_field("custom_css", "Custom CSS", "custom_css_input_field", "anti-adblock", "anti_adblock_config_section");

      register_setting("anti_adblock_config_section", "disable_website");
      register_setting("anti_adblock_config_section", "disable_website_url");
      register_setting("anti_adblock_config_section", "alternative_ads_code");
      register_setting("anti_adblock_config_section", "alternative_ads_selector_1");
      register_setting("anti_adblock_config_section", "alternative_ads_code_1");
      register_setting("anti_adblock_config_section", "alternative_ads_selector_2");
      register_setting("anti_adblock_config_section", "alternative_ads_code_2");
      register_setting("anti_adblock_config_section", "custom_css");
   }

   function disable_website_checkbox()
   {  
      ?>
         <input type="checkbox" name="disable_website" value="1" <?php checked(1, get_option('disable_website'), true); ?> /> Check for Yes
      <?php
   }

   function disable_website_image_input_field()
   {
      ?>
         <input name="disable_website_url" type="txt" value="<?php echo get_option('disable_website_url'); ?>" />
      <?php
   }

   function alternative_ads_checkbox()
   {
      ?>
         <input type="checkbox" name="alternative_ads_code" value="1" <?php checked(1, get_option('alternative_ads_code'), true); ?> /> Check for Yes
      <?php
   }

   function alternative_ads_selector_1_input_field()
   {
      ?>
         <input name="alternative_ads_selector_1" type="txt" value="<?php echo get_option('alternative_ads_selector_1'); ?>" />
      <?php
   }

   function alternative_ads_code_1_input_field()
   {
      ?>
         <textarea name="alternative_ads_code_1"><?php echo get_option("alternative_ads_code_1"); ?></textarea>
      <?php
   }

   function alternative_ads_selector_2_input_field()
   {
      ?>
         <input name="alternative_ads_selector_2" type="txt" value="<?php echo get_option('alternative_ads_selector_2'); ?>" />
      <?php
   }

   function alternative_ads_code_2_input_field()
   {
      ?>
         <textarea name="alternative_ads_code_2"><?php echo get_option("alternative_ads_code_2"); ?></textarea>
      <?php
   }

   function custom_css_input_field()
   {
      $css = ".anti-adblock-textarea{display: none}" . get_option("custom_css");
      file_put_contents(plugin_dir_path(__FILE__) . "custom.css", $css);

      ?>
         <textarea name="custom_css"><?php echo get_option("custom_css"); ?></textarea>
      <?php  
   }

   add_action("admin_init", "anti_adblock_settings");

   function anti_adblock_footer_code()
   {
      if(get_option("disable_website") == 1)
      {
         ?>
            <span id="anti-adblock-disable-website" data-value="true"></span>
            <span id="anti-adblock-disable-website-url" data-value="<?php echo get_option('disable_website_url'); ?>"></span>
         <?php
      }
      else
      {
         ?>
            <span id="anti-adblock-disable-website" data-value="false"></span>
         <?php  
      }

      if(get_option("alternative_ads_code"))
      {
         //change this if your are adding more fields.
         $count = 2;

         ?>
            <span id="anti-adblock-alternative-ads" data-value="true" data-count="<?php echo $count; ?>"></span>
         <?php

         for($iii = 1; $iii <= $count; $iii++)
         {
            ?>
               <textarea class="anti-adblock-textarea" id="alternative_ads_selector_<?php echo $iii; ?>"><?php echo get_option("alternative_ads_selector_" . $iii); ?></textarea>
               <textarea class="anti-adblock-textarea" id="alternative_ads_code_<?php echo $iii; ?>"><?php echo esc_html(get_option("alternative_ads_code_" . $iii)); ?></textarea>
            <?php
         }
      }
      else
      {
         ?>
            <span id="anti-adblock-alternative-ads" data-value="false"></span>
         <?php
      }
   }

   function anti_adblock_style_script()
   {
      wp_register_style("anti-adblock-custom", plugin_dir_url(__FILE__) . "custom.css");
      wp_enqueue_style("anti-adblock-custom");

      wp_enqueue_script('anti-adblock-script', plugin_dir_url(__FILE__) . "index.js", array("jquery"), '1.0.0', true);
   }

   add_action("wp_footer","anti_adblock_footer_code");
   add_action("wp_enqueue_scripts", "anti_adblock_style_script");
```

### 索引. js

```
function adblock_detect() {
    var iframe = document.createElement("iframe");
    iframe.height = "1px";
    iframe.width = "1px";
    iframe.id = "ads-text-iframe";
    iframe.src = "https://example.com/ads.html";

    document.body.appendChild(iframe);

    setTimeout(function() {
        var iframe = document.getElementById("ads-text-iframe");
        if (iframe.style.display == "none" || iframe.style.display == "hidden" || iframe.style.visibility == "hidden" || iframe.offsetHeight == 0) {

            adblock_blocking_ads();

            iframe.remove();
        } 
        else 
        {

            iframe.remove();
        }
    }, 100);
}

function adblock_blocking_ads()
{
  var blockwebsite = document.getElementById("anti-adblock-disable-website").getAttribute("data-value");
  var alternativeads = document.getElementById("anti-adblock-alternative-ads").getAttribute("data-value");

  if(blockwebsite == "true")
  {
    var url = document.getElementById("anti-adblock-disable-website-url").getAttribute("data-value");
    document.body.innerHTML = "<div style='position: fixed; width: 100%; height: 100%; background-color:black; background-repeat: no-repeat; background-position: center center; background-image: url(" + url + ");'></div>";
  }
  else if(alternativeads == "true")
  {
    var count = document.getElementById("anti-adblock-alternative-ads").getAttribute("data-count");

    for(var iii = 1; iii <= count; iii++)
    {
      var selector = document.querySelector("#alternative_ads_selector_" + iii).innerHTML;

      if(selector != null)
      {
        document.querySelector(selector).innerHTML = htmlDecode(document.querySelector("#alternative_ads_code_" + iii).innerHTML);
      }
    }
  }
}

function htmlDecode(input) {
  var e = document.createElement('div');
  e.innerHTML = input;
  return e.childNodes.length === 0 ? "" : e.childNodes[0].nodeValue;
}

window.addEventListener("load", function(){
  adblock_detect();
}, false);
```

### 截屏

这是当你为 AdBlock 用户禁用整个网站时的样子。

![Disable the whole website for AdBlock users](img/4450af5810cb2fc6e1ac188909657d15.png)

这是当你想显示替代广告时的样子。

![Display alternative ads for AdBlock users](img/c86876bb42ae6ab6c442f4b1e38ab22a.png)

### 提醒一句

如前所述，在网站被禁用的情况下提供图像时，请确保图像 URL 不包含任何特定于广告的关键字。否则他们也会被封锁。

同样，确保替代广告不指向任何其他广告网络或不指向带有特定广告关键词的 URL。否则替代广告也会被屏蔽。在这篇文章中，我所说的替代广告指的是来自同一个域名的广告服务器。

### 最后的想法

根据我自己的分析数据，我可以说 25%的网站访问者使用 AdBlock。如果你从“按印象付费”网络提供广告，那么这个插件可以马上增加你网站 25%的收入。

如果你不想屏蔽你的网站，那么你可以使用替代广告功能来请求用户将你的网站列入白名单。

## 分享这篇文章