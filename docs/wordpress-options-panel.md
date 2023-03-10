# 在 WordPress 中创建自定义选项页面

> 原文：<https://www.sitepoint.com/wordpress-options-panel/>

淘汰旧的；跟上新的潮流。不久前，为 WordPress 主题创建一个选项页面的过程有点像一场噩梦。当然，一旦您理解了这个过程，做起来就相对容易了，但是，这需要大量的工作，包括编写安全检查、创建表单、手动添加隐藏输入等等。

幸运的是，从 WordPress 2.7 开始，由于新的[设置 API](http://codex.wordpress.org/Settings_API) ，这个过程已经被简化了。在本教程中， [*的作者 Jeffrey Way 展示了如何从头开始构建一个选项页面。他还为那些喜欢更直观的方法的人提供了这个教程的视频版本。毫无疑问，你已经在网上看到了很多这样的文章，但这将使这个过程尽可能容易理解——尤其是对那些刚刚开始的人来说！我们开始吧。*](https://www.sitepoint.com/books/wordpress1/)

## 规划成品

首先，让我们组织一下我们的想法，并确定我们希望我们的最终产品是什么样子和做什么。

这个选项页面应该允许我们上传图像，添加文本，也许还可以选择配色方案。[图 1，“完成的选项页面”](#fig_finished_product "Figure 1. The finished Options page")显示了我们希望选项页面在完成时的样子。

**图一。成品选项页**

![The finished Options page](img/f13d498e6c8e56e8c5b3c44b243a50ef.png)

一旦用户在这个表单中输入了值，我们就可以使用有用的 [`get_option`](http://codex.wordpress.org/Function_Reference/get_option) 方法从我们的主题模板文件中检索它们，就像这样:

```
<?php $options = get_option('plugin_options'); ?><h2><?php echo $options['banner_heading']; ?></h2>
```

根据在图 1 的[中输入的值，“完成的选项页面”](#fig_finished_product "Figure 1. The finished Options page")，这将呈现“Buy Me”很方便，对吧？好吧，我们来造这个东西。

## 开始行动

在创建选项页面时，我们有两种选择。我们可以把它当作一个插件，存储在`plugins`文件夹中，或者我们可以把它做成一个特定主题的选项页面，存储在我们主题的`functions.php`文件中(或者另一个包含在`functions.php`中的文件)。在这种情况下，我们将采用后一种解决方案，尽管这两种方法都可行。

由于`functions.php`将为你的主题存储各种功能，最好将你的选项页面保存在它自己的文件中，然后我们可以从`functions.php`中包含它。首先在你的主题文件夹中创建一个名为`functions`的文件夹。在这个文件夹中，创建一个名为`admin-menu.php`的新文件。接下来，我们需要使这个页面在我们的核心函数文件中可见。返回到`functions.php`，这样包含这个文件:

**例 1。`*`theme`* /functions.php` (节选)**

```
<?php require_once(TEMPLATEPATH . '/functions/admin-menu.php'); ?>
```

由于`TEMPLATEPATH`会自动生成到你的主题文件夹的路径，我们只需要继续到`admin-menu.php`的路径。

## 添加选项页面

接下来，我们必须告诉 WordPress 向 WordPress 仪表板添加一个新的选项页面。方便的是，WordPress 提供了有用的`add_options_page`功能。然而，我们不能立即调用这个方法——我们必须先用`admin_menu`钩子注册这个函数:

**例二。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
add_action('admin_menu', 'create_theme_options_page');function create_theme_options_page() {  add_options_page('Theme Options', 'Theme Options', 'administrator', __FILE__, 'build_options_page');}
```

上面的代码指示 WordPress 调用`create_theme_options_page`函数，将页面添加到 WordPress 仪表板的设置部分。`add_options_page`函数需要一些参数:

*   *`$page_title`* :页面的标题

*   *`$menu_title`* :菜单的名称

*   *`$access_privileges`* :谁有权限访问此页面

*   *`$page_name`* :可以是唯一的字符串，但是许多开发人员更喜欢使用`__FILE__`来确保名称的唯一性，并且不会与任何其他页面发生冲突

*   *`$callback`* :处理选项表单创建的函数

我们来分析一下。我们实际上是在说，“向仪表板的设置部分添加一个新页面，使用这些值作为标题和访问权限，然后调用`build_options_page`函数来处理页面的创建。”

[图 2，“WordPress 仪表板中的主题选项链接”](#fig_themeoptions_link "Figure 2. The Theme Options link in the WordPress dashboard")显示了 WordPress 仪表板菜单的设置部分中的链接输出。

**图二。WordPress 仪表盘中的主题选项链接**

![The Theme Options link in the WordPress dashboard](img/48d1b23025b34fbcac6bdcbe1fe0a3b2.png)

## 构建选项页面

我们已经调用了`build_options_page`函数；现在我们需要创建它:

**例 3。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
function build_options_page() {?>  <div id="theme-options-wrap">    <div class="icon32" id="icon-tools"> <br /> </div>    <h2>My Theme Options</h2>    <p>Take control of your theme, by overriding the default settings with your own specific preferences.</p>    <form method="post" action="options.php">      <p class="submit">        <input name="Submit" type="submit" class="button-primary" value="<?php esc_attr_e('Save Changes'); ?>" />      </p>    </form>  </div><?php}
```

**图 3。我们选项页面的基本轮廓**

![The basic outline of our Options page](img/d7ac6dded5ad34b6250e9dc2b62ad168.png)

除了以下几点之外，您应该对此很熟悉:

*   带有`icon32`的`class`的`div`是用来添加那个小图标的。WordPress 有各种各样的图标，用于不同的页面；我们只是借用这个作为我们的自定义页面。

*   默认情况下，WordPress 仪表盘按钮是灰色的。通过应用一个`button-primary`的`class`，WordPress 已经为其编写了样式，我们可以将按钮设置为蓝色，让它更“流行”一点。

*   我们使用`esc_attr_e('Save Changes')`作为提交按钮的值。我们使用翻译功能来确保该文本可以本地化。

记住，如果可能的话，总是使用 WordPress 的内置类。这使得您的页面与仪表板的视觉效果更加一致。

## 登记设置

您一定注意到了——也许有点奇怪——我们没有在表单中添加任何输入。如果我们使用老方法，我们会这样做；然而，使用设置 API，该过程的工作方式略有不同。

我们需要使用`register_setting`函数在数据库中注册我们的新选项。但是在我们这样做之前，我们必须再次监听`admin_init`钩子。当您访问仪表板时，会触发`admin_init`挂钩。这是我们需要注册选项设置的时候。作为第二个参数，我们将传递一个回调函数名，它将处理注册我们的设置的过程:

**例 4。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
add_action('admin_init', 'register_and_build_fields');function register_and_build_fields() {   register_setting('plugin_options', 'plugin_options', 'validate_setting');}
```

此`register_setting`函数接受三个参数:

*   *`$option_group`* :表示设置组名称的字符串

*   *`$option_name`* :选项的名称

*   *`$sanitize_callback`* :可以处理任何特定操作或消毒的回调函数

通过注册这个`plugin_options`设置，我们可以使用下面的代码最终访问它存储的值:

```
$options = get_option('plugin_options');echo $options['value'];
```

既然我们已经将`validate_setting`指定为回调函数，我们需要定义它。我们的回调函数需要接受`$plugin_options`变量，并在完成验证或清理后返回它:

**例 5。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
function validate_setting($plugin_options) {  return $plugin_options;}
```

在下一个例子中，我们将更多地使用这个函数。

接下来，我们需要使用`add_settings_section`方法，它允许我们在选项页面上创建部分——相似项目的组。节让我们组织显示的信息，使其更有用。如果项目需要，您可以创建多个部分，但在大多数情况下，一个部分就足够了。因此，让我们将下面的代码添加到我们的`register_and_build_fields`函数中:

**例 6。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
add_settings_section('main_section', 'Main Settings', 'section_cb', __FILE__);
```

该函数可以接受四个参数:

*   *`$id`* :该部分的唯一标识

*   *`$title`* :页面上字段上方显示的标题

*   *`$callback`* :可以处理截面的创建；如果它被声明了，你必须创建这个函数，否则将会抛出一个错误

*   *`$page`* :定义该节应用的设置页面类型；在我们的例子中，它应该适用于我们的自定义页面，所以我们使用了`__FILE__`作为名称

为了避免抛出错误，我们需要定义刚刚设置的回调函数。很简单:

**例 7。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
function section_cb() {}
```

## 添加字段

下一步是使用`add_settings_field`函数为我们的选项页面添加字段或输入。让我们从`banner_heading`字段开始。下面的代码可以再次添加到`register_and_build_fields`函数中:

**例 8。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
add_settings_field('banner_heading', 'Banner Heading:', 'banner_heading_setting', __FILE__, 'main_section');
```

我们可以向这个函数传递总共六个参数，尽管在这个应用程序中我们只使用五个:

*   *`$id`* :属性的唯一标识

*   *`$title`* :字段的标题；这基本上是输入的标签

*   *`$callback`* :处理输入的创建；从这个函数中，我们将回显出必要的 HTML

*   *`$page`* :该部分要应用的设置页面的类型；同样，在这种情况下，我们使用`__FILE__`作为名称

*   *`$section`* :该字段涉及的区间名称；我们称我们的为`main_section`

*   *`$args`* (可选):任何附加参数，作为数组传递

通过这段代码，我们添加了一个新的设置字段，并将`banner_heading_section`声明为处理输入创建的回调函数。我们现在可以构建这个函数:

**例 9。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
// Banner Headingfunction banner_heading_setting() {  $options = get_option('plugin_options');  echo "<input name='plugin_options[banner_heading]' type='text' value='{$options['banner_heading']}' />";}
```

这段代码需要一点解释。最简单的形式是，函数`echo`输出一个文本框。我们将这个输入命名为`plugin_options[banner_heading]`，以允许我们向之前注册的`plugin_options`设置添加一个新的键。最后，这些输入不会总是空白的。为了提醒管理员每个选项中当前设置了哪些值，我们使用文本框的`value`属性来显示这个特定选项的当前值。

## 渲染输入

我们已经成功注册了`plugin_options`设置，并且添加了一个新字段。接下来，我们必须返回到我们的表单，并指定我们想要插入每个字段或输入的位置。返回到`build_options_page`函数，修改表单如下:

**例 10。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
<form method="post" action="options.php" enctype="multipart/form-data">  *<?php settings_fields('plugin_options'); ?>  <?php do_settings_sections(__FILE__); ?>*  <p class="submit">    <input name="Submit" type="submit" class="button-primary" value="<?php esc_attr_e('Save Changes'); ?>" />  </p></form>
```

请注意，我们添加了两个新函数:

*   这个非常有用的功能将为我们显示所有隐藏的字段，增加我们插件的安全性。在设置 API 出现之前，我们必须手动完成这项工作。该函数接受一个参数:在`register_setting`函数中指定的 *`option_group`* 名称。我们称我们的为`plugin_options`。

*   `do_settings_sections`:这个函数循环遍历我们已经创建的所有字段(目前只有一个),并相应地显示输入。它还接受一个参数，该参数应该等于我们的选项页面的惟一页面 ID。

如果你返回到 WordPress 仪表盘，点击主题选项页面，你会看到一个横幅标题的表单域，如图[图 4 所示，“我们新的横幅标题选项”](#fig_bannerheading "Figure 4. Our new banner heading option")。

**图 4。我们新的横幅标题选项**

![Our new banner heading option](img/d09676d82a5d96696dce8c5672064430.png)

上图中的文本框是空白的，因为我们还没有为`banner_heading`选项设置值。只要我们输入一个值，点击保存更改，并刷新页面，就会显示数据库中选项的当前值。

## 检索选项值

信不信由你，你现在有一个工作选项页面！它只有一个选项，但现在，添加更多选项的过程非常简单。因此，让我们想象一下，我们将“我最棒的横幅标题”的值应用于上面的`plugin_options[banner_heading]`字段。你现在的问题可能是，“我如何在我的主题中检索这个值？”简单。

在主题模板文件中的任何地方，添加以下代码:

```
1<?php $options = get_option('plugin_options'); ?><h2> <?php echo $options['banner_heading']; ?> </h2>
```

这段代码将输出一个`h2`元素，如图 5 中的[所示，“显示横幅标题”](#fig_banner_heading_display "Figure 5. Displaying the banner heading")。

**图 5。显示横幅标题**

![Displaying the banner heading](img/5210dc8c8b181ea8fb56048a395c515c.png)

## 设置选项页面的样式

我们的选项页面在当前的形式下工作得很好，但是让我们稍微改变一下它的样式。要做到这一点，我们可以混合使用 WordPress 的原生`class` es，以及一些我们自己的 CSS。要从选项页面引用样式表，我们必须监听`admin_head`钩子——这个动作将在创建仪表板的`head`部分时运行。我们将在这里插入样式表引用，如下所示:

**例 11。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
// Add stylesheetadd_action('admin_head', 'admin_register_head');function admin_register_head() {  $url = get_bloginfo('template_directory') . '/functions/options_page.css';  echo "<link rel='stylesheet' href='$url' />n";}
```

是回调函数，它将处理样式表的创建。假设我们已经向`functions`文件夹添加了一个新的`options-page.css`样式表，我们可以回显一个指向该文件的链接标签。

在我们开始编辑新创建的样式表之前，让我们给页面添加几个类，WordPress 已经为它们编写了样式。返回`build_options_page`功能，在`theme-options-page` `div`上增加一个`widefat`的`class`:

**例 12。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
<div id="theme-options-wrap" class="widefat">
```

现在，该表单将如[图 6 所示，“添加 widefat 类后的显示”](#fig_widefat "Figure 6. The display after we add the widefat class")。

**图六。我们添加了`widefat` `class`** 后的显示

![The display after we add the widefat class](img/988ab72beafd2bee7c311043deff58d1.png)

这有助于增加一些边界；然而，它仍然需要工作。在我们刚刚创建的新`options-page.css`文件中，添加以下内容:

**例 13。`*`theme`*/功能/选项-page.css`**

```
#theme-options-wrap {  width: 700px;  padding: 3em;  background: white;  background: -moz-linear-gradient(top, #f4f2f2, white 20%, #f4f2f2 80%, white);  background: -webkit-gradient(linear, left top, left bottom, from(#f4f2f2), color-stop(.2, white), color-stop(.8, #f4f2f2), to(white));  border-top: 1px solid white;}#theme-options-wrap #icon-tools {  position: relative;  top: -10px;}#theme-options-wrap input, #theme-options-wrap textarea {  padding: .7em;}
```

有了这个额外的 CSS，表单将如[图 7 所示，“带有新定制 CSS 的选项表单”](#fig_customcss "Figure 7. The Options form with our new custom CSS")。

**图 7。新定制 CSS 的选项表单**

![The Options form with our new custom CSS](img/066215e855571167e7ba97615b3860a4.png)

我们当然可以更进一步，但是，任何更多的都超出了本教程的范围。这里，我们刚刚为基于 Mozilla 和 Webkit 的浏览器添加了宽度、一些填充和微妙的背景渐变。

## 添加更多选项

现在我们已经成功地创建了一个工作选项表单，我们可以充实它并添加更多的字段。让我们添加一个新的部分，允许主题的用户上传一个徽标。

第一步是向我们创建的`register_and_build_fields`函数添加一个新的`add_settings_field`:

**例 14。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
add_settings_field('logo', 'Logo:', 'logo_setting', __FILE__, 'main_section'); // LOGO
```

接下来，我们创建上面指定的`logo_setting`回调函数。这将处理输入的创建和回显:

**例 15。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
function logo_setting() {  echo '<input type="file" name="logo" />';}
```

这样应该就可以了(差不多)——它会向页面添加一个新字段。记住，`do_settings_sections`函数循环遍历所有的设置字段，并将它们呈现在页面上。正如您在图 8 的[“徽标选项的文件上传字段”](#fig_fileupload "Figure 8. The file upload field for the logo option")中看到的，现在显示了一个文件上传字段。

**图 8。徽标选项**的文件上传字段

![The file upload field for the logo option](img/84bfbbb91227ff8da3e535d5bf0fa40b.png)

## 上传文件

好吧，我撒谎了！没错，我们已经向页面添加了输入，但是我们还没有编写逻辑来处理上传和保存图像的过程，以便可以访问它。

因为我们现在从表单上传文件，我们需要向表单添加`enctype`属性:

**例 16。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
<form method="post" action="options.php" enctype="multipart/form-data">
```

该属性决定了表单数据的编码方式。

接下来，我们需要执行一些验证，然后将图像上传到服务器。如果你回头参考一下`register_setting`的定义，你会看到它提供了第三个参数，可以处理任何特定的操作或消毒。我们再次称我们的`validate_setting`:

**例 17。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
register_setting('plugin_options', 'plugin_options', 'validate_setting');
```

这是确保用户上传图像(而不是，例如，`.exe`文件)的最佳位置，然后将文件上传到服务器。更改您的`validate_setting`函数以反映以下代码:

**例 18。`*`theme`*/functions/admin-menu . PHP`(节选)**

```
function validate_setting($plugin_options) { $keys = array_keys($_FILES); $i = 0; foreach ( $_FILES as $image ) {   // if a files was upload   if ($image['size']) {     // if it is an image     if ( preg_match('/(jpg|jpeg|png|gif)$/', $image['type']) ) {       $override = array('test_form' => false);       // save the file, and store an array, containing its location in $file       $file = wp_handle_upload( $image, $override );       $plugin_options[$keys[$i]] = $file['url'];     } else {       // Not an image.        $options = get_option('plugin_options');       $plugin_options[$keys[$i]] = $options[$logo];       // Die and let the user know that they made a mistake.       wp_die('No image was uploaded.');     }   }   // Else, the user didn't upload a file.   // Retain the image that's already on file.   else {     $options = get_option('plugin_options');     $plugin_options[$keys[$i]] = $options[$keys[$i]];   }   $i++; } return $plugin_options;}
```

这将是我们代码中最令人困惑的部分。当一个文件被上传时，它通过`$_FILES`超全局变量变得可用。该数组将包含关于文件的名称、类型、`tmp_name`、错误和大小的信息。然而，在这种情况下，我们不能假设一次只上传一个文件。事实上，您的选项页面可以包含多个文件输入。因此，我们必须使用一个`foreach`语句来过滤`$_FILES`数组中的所有条目。在`foreach`语句中，每个上传图像的数据将通过`$image`数组表示。

我们首先确定图像的大小是否大于 0，以确保用户上传了文件。

接下来，我们执行适度的验证，以确保用户上传的是图像，而不是其他类型的文件。事实上，在任何其他情况下，这种方法都是不够的。用户仍然可以更改文件的扩展名，例如，`.png`，并成功上传他们的文件。但是，因为这个页面已经仅限于管理员，所以这种验证更多的是为了方便他们。如果您需要更强的图像上传保护，请使用`getimagesize` PHP 函数。

这个`preg_match`函数确定正则表达式`jpg|jpeg|png|gif`是否包含在图像类型中。如果是，我们可以假设，最有可能的是，用户上传了图像。

接下来，我们调用`wp_handle_upload` WordPress 函数，它将负责将图像移动并保存到 WordPress 文件夹的`uploads`目录中。

`$file`现在将等于包含上传信息的数据数组——最显著的是保存位置的 URL(通过`$file['url']`访问)。

最后，我们为我们的`plugin_options`选项添加了一个新的键，并使它等于服务器上图像的路径。

以上是上传图片的基本概述。事实上，这个脚本做的比我描述的更多，但是，因为本教程是专门关于创建一个选项页面的，所以我不想偏离这个特定的主题太远。如果你愿意，我们可以在这篇文章的评论中更多地讨论这个函数。

这样一来，就像使用`banner_heading`选项一样，我们可以通过编写以下代码从主题文件中访问我们的徽标:

```
<?php $options = get_option('plugin_options'); ?><img src="<?php echo $options['logo']; ?>" alt="Logo" />
```

## 结论

如果你已经完成了整个教程，给自己一个鼓励。设置 API 最令人困惑的部分是记忆它所有的功能；然而，一旦你这样做了，你就可以获得大量的权力。例如，我们在本教程中只创建了两个选项，但是现在，创建一个新选项将花费您不到一分钟的时间！

那么，如果你想让你的选项页更进一步，下一步会是什么呢？尝试添加一层 JavaScript 来动态创建新选项，而不必返回到插件文件！

## 分享这篇文章