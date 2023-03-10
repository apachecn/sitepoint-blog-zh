# Kirki 的快速 WordPress 定制选项

> 原文：<https://www.sitepoint.com/fast-wordpress-customizer-options-development-with-kirki/>

WordPress 用户已经习惯的一件事是，简单，不需要代码的主题定制选项。想法是:下载一个主题，在主题面板中激活它，进入定制面板，开始调整主题的颜色，布局，字体等。，只需点击鼠标。

WordPress 为主题开发者提供了定制 API 。这是一组干净的、面向对象的方法，有助于创建一致的定制界面。在 Customize 面板上，用户可以轻松地进行修改并实时预览，而不必修改 PHP 或 CSS 代码。

使用 Customizer API 开发主题选项，虽然是一个简单且符合逻辑的过程，但需要编写一定量的重复代码。为了减少构建功能和安全定制选项所需的一些步骤，**[Aristeides Stathopoulos](https://github.com/aristath)**一直在开发一个免费的开源插件， [Kirki](http://kirki.org/) 。

在这篇文章中，我将展示如何将 Kirki 集成到你的 WordPress 主题中，以及如何使用它来构建一些定制选项。

## Kirki 是什么？

让我们听听 Kirki 背后的开发者是怎么说的:

> Kirki 不是一个框架。这是一个工具包，允许 WordPress 开发者使用定制器，并通过抽象代码来利用其高级功能和灵活性，使每个人都更容易创建美丽和有意义的用户体验。
> 
> [Kirki 文档](https://github.com/reduxframework/kirki/wiki)

关于这个工具包，我想讲两点。

*   Kirki 不会取代 WordPress 定制 API。它创建了一个“默认 WordPress 方法的包装器，简化了语法，允许你用更少的代码写更多的东西”。您仍然可以在 Kirki API 旁边使用本机定制器方法。更好的是，我们强烈建议您在接触 Kirki 之前先熟悉一下 Customizer API。如果你正在寻找定制对象的有用介绍，可以去 Narayan Prusty 的[开始使用 WordPress 主题定制 API](https://www.sitepoint.com/getting-started-with-the-wordpress-theme-customization-api/) 。
*   Kirki 处于不断发展和改进的状态，这与 WordPress 定制器本身并无不同。因此，任何错误报告，或者对新功能的请求，都可以在 [GitHub repo](https://github.com/reduxframework/kirki) 中找到它们的位置，在这里你可以下载插件的开发版本并为其开发做出贡献。

是时候看看 Kirki 的行动了。如果你想继续下去，准备好一个 WordPress 主题或者获取包含这篇文章中讨论的所有代码的超级最小演示主题。

## 如何在你的主题中包含 Kirki

Kirki 被打包成一个 WordPress 插件。因此，你可以从[WordPress.org 插件库](https://wordpress.org/plugins/kirki/)下载它，或者直接从你的 WordPress 安装后台下载，解压并激活它。

如果你喜欢将 Kirki 作为一个库包含在你的主题中，请遵循下面列出的步骤。

*   将 kirki 目录复制到你的主题文件夹中。
    ![Theme Directory Structure](img/46f812355544952f2b4d0e7efa03cad2.png)
    在本文的演示主题中，Kirki 文件位于名为`inc`的目录中。

*   将这一行添加到`functions.php`，让你的主题与 Kirki‘对话’(确保你调整了 Kirki 文件夹的路径，以匹配你的主题的文件结构)。

```
if ( ! class_exists( 'Kirki' ) ) {
    include_once( dirname( __FILE__ ) . '/inc/kirki/kirki.php' );
}
```

*   添加一个包含 Kirki 配置选项的函数，并将其与`kirki/config`过滤器挂钩。你想给这个函数添加什么由你决定。对于这篇文章，让我们通过添加 Kirki 需要的代码来保持它的最小化，以便“意识到”它的新位置，即主题的文件夹而不是插件的文件夹。

```
function superminimal_customizer_config() {
         $args = array(
        // Only use this if you are bundling the plugin with your theme 
        'url_path'     => get_stylesheet_directory_uri() . '/inc/kirki/',

       );
       return $args;
       }
       add_filter( 'kirki/config', 'superminimal_customizer_config' );
```

在这个配置函数中，你可以控制 WordPress 定制器的外观和感觉来匹配你的主题。最重要的是，您在这里添加了必要的代码，使得插件使用的所有字符串都可以从您的主题中翻译。看看超级迷你演示主题或 Kirki 文档页面中关于如何完成的一些指导。

## 让我们开始添加选项

Kirki 现在准备帮助我们建立一些定制选项。你可以使用`functions.php`或者为任务创建一个专用文件，这由你决定。

定制选项位于**部分**内，这些部分可选地位于**面板**内。在本文的演示项目中，我使用本机定制器方法将所有部分分组到一个专用面板中，如下所示。

```
function superminimal_demo_panels_sections( $wp_customize ) {
     /**
     * Add Panel
     */
     $wp_customize->add_panel( 'sitepoint_demo_panel', array(
      'priority'    => 10,
      'title'       => __( 'SitePoint Demo Panel', 'superminimal' ),
      'description' => __( 'Kirki integration for SitePoint demo', 'superminimal' ),
     ) );

     //More code to come
    }
    add_action( 'customize_register', 'superminimal_demo_panels_sections' );
```

接下来，通过将这段代码放在前面的`add_panel`定制器方法下面，分别为站点**文本颜色**、站点**布局**和**页脚文本**选项添加部分。

```
/**
     * Add a Section for Site Text Colors
     */
     $wp_customize->add_section( 'superminimal_text_colors', array(
      'title'       => __( 'Site Text Colors', 'superminimal' ),
      'priority'    => 10,
      'panel'       => 'sitepoint_demo_panel',
      'description' => __( 'Section description.', 'superminimal' ),
     ) );

     /**
     * Add a Section for Site Layout
     */
     $wp_customize->add_section( 'superminimal_site_layout', array(
      'title'       => __( 'Site Layout', 'superminimal' ),
      'priority'    => 10,
      'panel'       => 'sitepoint_demo_panel',
      'description' => __( 'Section description.', 'superminimal' ),
     ) );

     /**
     * Add a Section for Footer Text
     */
     $wp_customize->add_section( 'superminimal_footer_text', array(
      'title'       => __( 'Footer Text', 'superminimal' ),
      'priority'    => 10,
      'panel'       => 'sitepoint_demo_panel',
      'description' => __( 'Section description.', 'superminimal' ),
     ) );
```

现在，您已经准备好添加第一个 Kirki 驱动的选项。

### 文本颜色选项

所有与选项相关的代码都要放在一个函数中。该功能然后通过`kirki/fields`过滤器过滤。

```
function superminimal_demo_fields( $fields ) {
      //Add code here

      return $fields;
    }
    add_filter( 'kirki/fields', 'superminimal_demo_fields' );
```

让我们给你的 WordPress 主题用户一个简单的方法来改变文本颜色。Kirki 很快做到了这一点。将下面的代码片段添加到`superminimal_demo_fields()`函数中，就在`return $fields;`语句的上方。

```
/**
    * Add a Field to change the body text color in the Text Colors Section
    */
    $fields[] = array(
      'type'        => 'color',
      'setting'     => 'superminimal_body_color',
      'label'       => __( 'Body Color', 'superminimal' ),
      'description' => __( 'Description here', 'superminimal' ),
      'section'     => 'superminimal_text_colors',
      'priority'    => 10,
      'default'     => '#555555',   
      'output'      => array(
        array(
          'element'  => 'body, p',
          'property' => 'color'
        ),
    );
```

Kirki 提供了一个简洁的语法来一次性添加设置和相关控件。

我们来分解一下`$fields[]`数组。

*   `type`指的是用户输入他们所选选项的值的特定控件，在本例中是颜色控件。
*   `setting`是指定制器设置的`id`，用于处理定制器对象的实时预览、保存和清理。
*   `label`和`description`是为了和用户沟通。标签显示了选项的标题，描述提供了选项功能的一些指示。
*   `section`指的是承载该特定颜色控件的部分的`id`。
*   `priority`指该特定颜色控件相对于同一部分内其他控件的位置。
*   `default`被设置为默认的 CSS 颜色值。
*   最后，`output`是应用设置值的令人敬畏的 Kirki 方式。只需输入 CSS 选择器和属性，Kirki 就会处理所有必要的操作。

按照上面的例子，你可以继续为链接添加一个颜色选项。

接下来，让我们为您的主题的用户提供定制站点布局的选项。

### 站点布局选项

Kirki 提供了一系列复杂的定制控件。我最喜欢的是**电台图像**控件。

![Site Layout Section](img/a54f7e1ba19ac9d0a5a25ba9a42ca02c.png)

下面是你如何将它添加到你的主题中，为用户提供改变站点布局的选项。

```
/**
    * Add a Field to change the site layout
    */
    $fields[] = array(
        'type'        => 'radio-image',
        'setting'     => 'superminimal_layout',
        'label'       => __( 'Site Layout', 'superminimal' ),
        'description' => __( 'Description here', 'superminimal' ),
        'section'     => 'superminimal_site_layout',
        'default'     => 'fullwidth',
        'priority'    => 10,
        'choices'     => array(
          'sidebar-left' => trailingslashit( get_template_directory_uri() ) . 'inc/kirki/asseimg/2cl.png',
          'fullwidth' => trailingslashit( get_template_directory_uri() ) . 'inc/kirki/asseimg/1c.png',
          'sidebar-right' => trailingslashit( get_template_directory_uri() ) . 'inc/kirki/asseimg/2cr.png',
        ),
    );
```

将上面的代码放在前一个`$fields[]`数组片段之后。注意代码中没有添加任何`output`参数。这是因为每个 radio image 输入的值不是 CSS 属性值。

从`superminimal_layout`设置中提取值的方法是通过本地定制器`get_theme_mod`方法。然后，将该值用作模板文件中合适的 HTML 元素的 class 属性值。最后，只需要编写适当的 CSS 来实现样式表中的`.fullwidth`、`.sidebar-left`和`.sidebar-right`类的理想布局。

查看如何在[超小型演示主题](https://github.com/antonellaP/Superminimal/tree/master/superminimal)中实现布局选项的详细信息。

### 页脚文本选项

你遇到过多少次 WordPress 主题用户要求你帮助他们替换开发者在主题页脚区的东西？使用 Kirki，添加一个选项让用户可以轻松地管理定制器中的页脚文本需要几分钟的时间。这是代码。

```
/**
    * Add a Field to change the footer text only if checkbox is checked
    */
    $fields[] = array(
        'type'        => 'textarea',
        'setting'     => 'superminimal_footer_text',
        'label'       => __( 'Footer Text', 'superminimal' ),
        'description' => __( 'Add some text to the footer', 'superminimal' ),
        'section'     => 'superminimal_footer_text',
        'default'     => 'Superminimal Theme – Kirki Toolkit Demo for SitePoint',
        'priority'    => 20,
      ),
    );
```

上面的代码输出了一个文本区，用户可以在这里写版权声明、致谢等。

![Footer Text Section](img/18375b2c2523cba16c1ee28abe15e0a9.png)

要提取并显示文本区域中输入的文本，使用`footer.php`中的本地定制器方法`get_theme_mod`，如下所示。

```
<footer id="footer">

      <?php echo get_theme_mod( 'superminimal_footer_text', __( 'Default text here', 'superminimal' ) ); ?>

      <?php wp_footer(); ?>
    </footer>
```

接下来，让我们看看 Kirki 还能做些什么来改善 WordPress 定制器的用户体验。

## 如何添加条件选项

作为一名主题设计师，你不喜欢用过多令人困惑的选项来淹没用户。有些选择不需要做出，直到用户选择了某个特定的选项。定制器为面板、部分和控件提供了方便的 [active_callback](https://developer.wordpress.org/themes/advanced-topics/customizer-api/#contextual-controls-sections-and-panels) 参数。您可以将此参数设置为特定条件，在定制器中显示面板、部分或控件之前必须满足该条件。

Kirki 在这方面能提供什么？

Kirki API 使用[必需的](https://github.com/reduxframework/kirki/wiki/required)参数，根据另一个控件的值在定制器中隐藏或显示一个控件。

例如，假设您希望显示文本区域，以便用户仅在选中复选框时才可以修改页脚文本。为此，将下面的代码添加到`superminimal_footer_text`控件中。

```
'required'  => array(
      array(
        'setting'  => 'superminimal_reveal_footer_text',
        'operator' => '==',
        'value'    => 1,
      ),
    ),
```

将上面的代码片段添加到 textarea 控件的代码中，可以确保在定制器中显示 textarea 控件之前，`superminimal_reveal_footer_text`控件的值等于 1。让我们给`superminimal_demo_fields()`函数添加`superminimal_reveal_footer_text`控件。

```
$fields[] = array(
     'type'        => 'checkbox',
     'setting'     => 'superminimal_reveal_footer_text',
     'label'       => __( 'Change Footer Text', 'superminimal' ),
     'description' => __( 'Description here', 'superminimal' ),
     'section'     => 'superminimal_footer_text',
     'default'     => 0,
     'priority'    => 10,   
    );
```

`superminimal_reveal_footer_text`控件是一个默认设置为 0 的复选框，也就是说，它是未选中的。这将阻止显示文本区域。

![Footer Text Checkbox](img/5545f87cba081bc5d1dd08cec68d6082.png)

只有在选中复选框后，即通过将其值从 0 更改为 1，textarea 才会出现在定制器中。

![Conditional Checkbox Control](img/9999de4f52422b9c43e0c6616906f92b.png)

## 增强实时预览

定制器附带了一个强大的 [JavaScript API](https://developer.wordpress.org/themes/advanced-topics/customizer-api/#the-customizer-javascript-api) 来为预览区域添加 AJAX 功能。此增强功能允许用户实时检查他们的修改，而无需等待整个定制器预览页面刷新。

我们可以用 Kirki 简单地通过向`$fields[]`数组添加一些方便的参数来实现相同的结果。

例如，要将 Ajax 化的实时预览添加到`superminimal_body_color`设置中，请将下面的代码片段添加到适当的`$fields[]`数组中。

```
'transport'   => 'postMessage',
    'js_vars'     => array(
      array(
        'element'  => 'body, p',
        'function' => 'css',
        'property' => 'color',
      ),
    )
```

让我解释一下上面的代码是做什么的。

*   首先，上面的代码将传输方法从**刷新**(默认)，改为**后消息**。这向定制器发出信号，它必须使用 JavaScript 进行实时预览。
*   接下来，`js_vars`参数值表明`body`和`p`元素将使用 CSS 颜色属性进行修改。

Kirki 为`function`参数提供了两个预定义值。如果您添加的设置存储了 CSS 规则，如`background-color`、`color`、`font-size`等，请使用`css`值。如果设置存储了要插入页面的 HTML 标记字符串，则使用`html`值。

作为如何使用`html`值的示例，让我们将 AJAX 实时预览功能添加到管理页脚文本更改的设置中。将这段代码添加到包含`superminimal_footer_text`设置的`$fields[]`数组的末尾。

```
'transport'   => 'postMessage',
    'js_vars'     => array(
      array(
       'element'  => 'footer',
       'function' => 'html'
      ),
    ),
```

就这样，试着在`superminimal_footer_text`部分的文本区写点东西。您很快会注意到预览屏幕中相应的页脚文本会相应地改变，而无需重新加载整个页面。酷！

### 带有单选图像控件的 Ajax 实时预览

可能有这样的情况，即`css`和`html`都不适合启用 AJAX 实时预览的`function`参数。一个恰当的例子是改变站点布局的设置。使用`css`作为函数参数的值没有太大的意义，因为所讨论的设置不存储任何 CSS 属性值。同样，使用`html`也不能完全解决问题。事实上，它最终只会在预览页面上显示出*全角*、*左侧栏*或*右侧栏*，这取决于选择了哪个单选按钮。但这不可能是你想要完成的。

好消息是，您可以插入一个定制的 JavaScript 函数名作为函数参数的值，并且它可以工作！

```
'js_vars'     => array(
      array(
        'element'  => '#content',
        'function' => 'superminimal_customizer_live_preview'
      ),
    ),
```

您需要将自定义函数所在的 JavaScript 文件入队，并将其与`customize_preview_init`动作挂钩。

```
function superminimal_customizer_live_preview() {
      wp_enqueue_script( 'superminimal_css_preview', 
                         get_template_directory_uri().'/js/superminimal-customizer-preview.js', 
                         array( 'customize-preview', 'jquery' ), '', true 
                       );
    }
    add_action( 'customize_preview_init', 'superminimal_customizer_live_preview' );
```

最后，使用本地定制器 JavaScript API 编写处理实时预览的 JavaScript 函数。

```
(function( $ ) {
      "use strict";

      wp.customize( 'superminimal_layout', function( value ) {
        value.bind( function( to ) {
          $( '#content' ).removeClass().addClass( to + ' clearfix' );
        } );
      });

    })( jQuery );
```

Kirki 的伟大之处在于你可以和 WordPress 定制 API 一起使用，而不是取代它。因此，如果情况需要，您可以立即在两个 API 之间轻松切换。

## 资源

还想吃吗？这里有一些很棒的资源。

*   [Kirki 工具包文档](https://github.com/reduxframework/kirki/wiki)。
*   使用 Kirki 定制器构建 WordPress 主题。
*   [主题选项–定制 API](https://developer.wordpress.org/themes/advanced-topics/customizer-api/) ，来自 WordPress.org 主题手册。

## 结论

我已经展示了如何将 Kirki 工具包集成到 WordPress 主题中。

Kirki 正在积极开发和支持中。它的 API 和定制控件已经显著优化了开发定制器主题选项所需的时间。如果你想让你的主题出现在[WordPress.org 主题库](https://wordpress.org/themes/)中，这就变得尤为重要。事实上，提供定制选项的主题是[通过定制器而不是定制选项页面来实现的](https://make.wordpress.org/themes/2015/04/21/this-weeks-meeting-important-information-regarding-theme-options/)。

要深入本文讨论的代码细节，可以从 GitHub 下载[超级最小演示主题](https://github.com/antonellaP/Superminimal/tree/master/superminimal)。

期待您的反馈！

## 分享这篇文章