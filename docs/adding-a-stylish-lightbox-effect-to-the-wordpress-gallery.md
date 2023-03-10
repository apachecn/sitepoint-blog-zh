# 给 WordPress 画廊添加一个时尚的灯箱效果

> 原文：<https://www.sitepoint.com/adding-a-stylish-lightbox-effect-to-the-wordpress-gallery/>

WordPress 自带内置图库功能。向文章或页面添加图库需要执行几个简单的步骤:

1.  点击*添加媒体*按钮

![add media button](img/7f9ee42e03c429acc38b7d7c6f0463e0.png)

3.  点击*创建图库*链接

![create gallery link](img/5bfa0cb84dba34520feb9b9aa65a210f.png)

5.  通过点击选择您的图像

![select gallery images](img/45a4d43fe3da34e43ea9a63eb887d190.png)

7.  在适当的文本框中至少输入*标题*和 *Alt* 文本，并点击*创建新图库*按钮

![create new gallery button](img/548c6055d04ca7c215e3f9e1cea1c0b3.png)

WordPress 没有提供开箱即用的功能，即可以用一个很酷的灯箱效果来放大每个图库的图片。

事实上，您可以调整图库设置，使图像链接到一个专用页面(即附件页面)、图像文件本身或什么都不链接。

![gallery settings](img/d2d954603598fda8981a58decfca9a17.png)

在这篇文章中，我将展示如何将 Jack Moore 的流行的 [jQuery Colorbox](http://www.jacklmoore.com/colorbox/) 插件集成到本地 WordPress 图库中。

因此，单击一个图像将使它以平滑的动画效果弹出。访问者无需离开图库页面就能阅读图片说明并浏览图片。

最终的产品会是这样的。

![What the lightbox effect looks like on the WordPress gallery at the end of this project](img/467052f4fc41adfe79d8620b9f27bc9d.png)

## 为什么不用 WordPress 插件呢？

在这一点上，我可以听到你说:“有很多 WordPress 插件可以让你轻松地在 WordPress 站点上添加图库和 lightbox 功能。jQuery Colorbox 本身作为 WordPress 插件有很多实现。我为什么要自己费心编写这种功能呢？”

首先，我们正在考虑一个非常特殊的功能，也就是说，仅仅给 WordPress 图库添加 lightbox 效果。大多数插件提供了更多的功能，随之而来的还有大量的额外代码。如果你正在寻找高度的灵活性，像添加灯箱效果到文章和页面的图片，自定义链接，或其他类型的内容，从管理面板调整插件的设置，等等。，那就继续走 WP 插件路线吧。然而，如果你的目标是完成一个非常专注的任务，就像现在这样，运行一个 WP 插件可能是大材小用。

另一个原因可能仅仅是个人偏好。对我自己来说，我更愿意使用 WP 插件来完成后端任务，比如发送联系表单、管理购物车、添加会员或论坛功能等。对于大多数与前端开发相关的任务，例如设计站点元素、创建动画、实现 jQuery sliders 和 lightbox，我选择自己动手。

最后，编码是有趣的，有教育意义的(一路上总会有新的东西要学)，在这种情况下，它不会占用你太多时间。

## 你需要什么

接下来，您需要安装一个 WordPress，最好是在本地服务器上，以及一个为本地 WordPress 图库提供 CSS 样式的主题。出于本教程的目的，WordPress 附带的任何主题的子主题都可以。

是时候投入进去了！

## 将 jQuery Colorbox 放入主题文件中

首先:下载一份 jQuery Colorbox 的拷贝，并将压缩文件解压到硬盘中的某个地方。

让 jQuery Colorbox 工作所需的文件如下:

*   `jquery.colorbox-min.js`；
*   `colorbox.css`和`images`文件夹从五个彩盒皮肤之一。这些可以在`example1`、`example2`等文件夹中找到，一直到`example5`。在你的浏览器中尝试每种皮肤，选择你最喜欢的一种。对于本教程，我选择了包含在`example3`文件夹中的皮肤。

接下来，为了保持项目的良好组织，在你的主题文件中添加一个文件夹，并将其命名为`colorbox`。将上面列出的文件放到这个文件夹中。

jQuery Colorbox 自带的文本字符串已经被翻译成多种语言。翻译位于`i18n`文件夹中。您不需要将这个文件夹复制到您的项目中，但是知道这一点很重要。如果你打算为你的主题提供翻译，这是一个很好的资源。

## 将 jQuery Colorbox 集成到您的主题中

将 Colorbox 整合到你的主题中需要一系列的步骤。其中一些步骤处理让 Colorbox 完成工作所必需的 jQuery 代码。另一系列步骤处理让 WordPress“感知”Colorbox 的任务，包括编写 PHP 代码。

我们开始工作吧！

## 创建主 JavaScript 文件

Colorbox 是一个 jQuery 插件，因此您需要创建一个包含必要的初始化和配置代码的 JavaScript 文件。

在你的主题的根目录下添加一个新文件夹，命名为`js`。接下来，在`js`文件夹中，创建一个新文件，并将其命名为`main.js`(或者您喜欢的主题主 JavaScript 文件的任何其他名称)。

![Theme files with the js folder and main.js](img/b432fb894d8fe7a787a6468d881c7a5b.png)

打开`main.js`并输入 **jQuery no conflict wrapper** 以确保 jQuery `$`符号不会与使用相同别名符号的其他脚本冲突:

```
(function($) {

        //All your code goes inside this wrapper

    })(jQuery);
```

现在您的 jQuery 文档已经设置好了。让我们继续前进！

## 编写 jQuery 脚本

初始化 jQuery Colorbox 就像编写下面这行代码一样简单:

```
selector.colorbox();
```

*选择器*代表你想要应用灯箱效果的 HTML 锚元素。

但是，有了这样一个高度可配置的插件，您可以做得更多。有很多设置你可以调整，使 Colorbox 做你想要的。让我们从调整其中一些开始。在 jQuery 无冲突包装器中，编写以下代码片段:

```
//Settings for lightbox
    var cbSettings = {
      rel: 'cboxElement',
      width: '95%',
      height: 'auto',
      maxWidth: '660',
      maxHeight: 'auto',
      title: function() {
        return $(this).find('img').attr('alt');
      }
    }
```

上面的代码在一个名为`cbSettings`的 [JavaScript 对象文字](https://www.sitepoint.com/back-to-basics-javascript-object-syntax/)中存储了一些设置:

*   *rel* 用于将照片分组在一起。设置此属性将启用灯箱中的导航箭头，以便用户可以从一张照片移动到群组中的上一张或下一张照片。 *cboxElement* 是一个类，Colorbox 自动将其添加到附加了 lightbox 功能的锚链接中，但你可以自由地用不同的名称对照片进行分组。
*   *宽度*和*高度*指灯箱尺寸。宽度的百分比值有助于保持组件的流动性和响应性。 *maxWidth* 和 *maxHeight* 是指 lightbox 中加载的内容的最大宽度和最大高度，在本例中是图库照片。
*   *title* 可用于显示 lightbox 中照片的标题。这里它被设置为一个简短的函数，告诉 Colorbox 提取图像的 *alt* 属性的值，并将其用作图像标题。

你可以在 [jQuery Colorbox 网站](http://www.jacklmoore.com/colorbox/)上找到所有可用设置的列表。

设置完成后，初始化 Colorbox:

```
//Initialize jQuery Colorbox   
    $('.gallery a[href$=".jpg"], 
    .gallery a[href$=".jpeg"], 
    .gallery a[href$=".png"], 
    .gallery a[href$=".gif"]').colorbox(cbSettings);
```

上面的代码片段选择了类为`gallery`的元素中的所有锚标记。此外，选定的锚点标签必须有一个以`.jpg`、`.jpeg`、`.png`或`.gif`(最常见的图像文件格式)结尾的`href`值。最后，在这个选择上调用 Colorbox，将`cbSettings`对象作为参数传入。

当代的网页设计是响应式的网页设计，因此 lightbox 应该适应所有的屏幕尺寸，这对我们来说很重要。有鉴于此，添加以下代码片段来处理这一重要方面:

```
//Keep lightbox responsive on screen resize
    $(window).on('resize', function() {
        $.colorbox.resize({
        width: window.innerWidth > parseInt(cbSettings.maxWidth) ? cbSettings.maxWidth : cbSettings.width
      }); 
    });
```

在调整浏览器大小时，触发`$.colorbox.resize()`方法。在这个方法中，上面的代码将`width`值设置为一个 [JavaScript 条件三元运算符](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)语句。该语句执行以下操作:

1.  它检索屏幕的`width`；
2.  它将这个`width`与插件设置中的`maxWidth`值进行比较，在我们的例子中是`660px`；
3.  如果屏幕的`width`大于`660px`，它会将灯箱的`width`调整到`660px`的值。但是，如果屏幕尺寸小于`660px`，灯箱`width`将被调整到设置中规定的`width`，在这种情况下为`95%`。

有关该解决方案的更多信息，请访问 [Colorbox GitHub 页面](https://github.com/jackmoore/colorbox/issues/183#issuecomment-42039671)。

这就是目前 JavaScript 的前沿，稍后你会回头来处理 WordPress 的国际化。让我们来解决这个项目的 WordPress 方面。

## 转到 PHP

目前，WordPress 对你的主题中 jQuery Colorbox 的存在一无所知。插件文件和`main.js`都没有被加载。在 WordPress 中加载样式表和 JavaScript 文件的推荐做法是分别使用*[WP _ enqueue _ style()](https://developer.wordpress.org/reference/functions/wp_enqueue_style/)*和*[WP _ enqueue _ script()](https://developer.wordpress.org/reference/functions/wp_enqueue_script/)*。

在你的主题文件中找到`functions.php`并在代码编辑器中打开它。很可能你的主题已经有一个函数可以让`style.css`入队。如果您找不到它，请根据您的主题修改下面代码片段中的函数。

```
function themeslug_enqueue_styles_scripts() {

       //Colorbox stylesheet
       wp_enqueue_style( 'colorbox', get_template_directory_uri() . 
       '/colorbox/colorbox.css' );

      //Your theme CSS
      wp_enqueue_style( 'themeslug-style', get_stylesheet_uri() );

      //Colorbox jQuery plugin js file
      wp_enqueue_script( 'colorbox', get_template_directory_uri() . 
      '/colorbox/jquery.colorbox-min.js', array( 'jquery'   ), '', true );

      //Add main.js file
      wp_enqueue_script( 'themeslug-script', get_template_directory_uri() . 
      '/js/main.js', array( 'colorbox' ), '', true );

    }

    //Hook the function above
    add_action( 'wp_enqueue_scripts', 'themeslug_enqueue_styles_scripts' );
```

让我们解释一下上面的代码是做什么的。

*   它将所有对`wp_enqueue_style()`和`wp_enqueue_script()`的调用包装在一个名为`themeslug_enqueue_styles_scripts()`的函数中。
*   在`themeslug_enqueue_styles_scripts()`中，Colorbox CSS 文件排在主题样式表之前。
*   Colorbox JavaScript 文件被排队指向作为依赖项的 jQuery 库，即`jquery.colorbox-min.js`仅在 jQuery 库文件已经被加载之后才被加载。
*   接下来是主题的 JavaScript 文件`main.js`，表示`jquery.colorbox-min.js`是一个依赖项。
*   `wp_enqueue_script()`末尾的参数`true`确保 JavaScript 文件在结束的`</body>`标签之前被加载到页面的`footer`区域。
*   最后，`themeslug_enqueue_styles_scripts()`函数被挂接到`wp_enqueue_scripts()`动作中。这确保了样式表和脚本文件在正确的时间被加载。

如果你使用子主题，确保用`get_stylesheet_directory_uri()`替换`get_template_directory_uri()`的所有实例。

另外，不要忘记用你实际的主题的 slug 替换所有的`themeslug`实例。

在这一点上，你的灯箱应该是功能齐全的。然而，我们正在和 WordPress 一起工作，直到 Colorbox 插件的文本串**可以翻译了**，这项工作才算完成。让我们处理最后一项任务。

## 国际化 JQuery 颜色盒文本字符串

建议 WordPress 主题或插件中的所有文本字符串都应该国际化。**国际化**

> …是发展你的主题的过程，所以它可以很容易地被翻译成其他语言。国际化通常被缩写为 i18n(因为在 I 和 T2 n 之间有 18 个字母)。
> 
> [国际化-主题
> 手册](https://developer.wordpress.org/themes/functionality/internationalization/)

WordPress 使用了 *[gettext](http://www.gnu.org/software/gettext/)* 库来完成这项任务。如果你使用过 WordPress 主题或插件，你可能会遇到一些 gettext 函数，比如 [`__()`](http://codex.wordpress.org/Function_Reference/_2) 和 [`_e()`](http://codex.wordpress.org/Function_Reference/_e) 。

这些和其他翻译函数通常包括要翻译的文本字符串作为它们的参数之一，以及主题或插件的 *textdomain* 。这是一个唯一的标识符，使 WordPress 能够区分所有不同的翻译。

jQuery Colorbox 有自己的文本字符串。你可以通过打开`i18n`文件夹中的任何文件来找到它们。我们需要确保这些字符串以某种方式发送到 WordPress，这样它们就可以和你的主题的文本字符串一起国际化。问题是:*我如何将数据从 JavaScript 代码转移到 WordPress？*

答案就在一个叫做 [`wp_localize_script()`](https://developer.wordpress.org/reference/functions/wp_localize_script/) 的伟大的 WordPress 函数中。

`wp_localize_script()`需要三个**必需的**参数:

*   `$handle`。这是文本字符串所在脚本的句柄或**标识符**。这个参数的数据是用于将 Colorbox JavaScript 文件入队的`wp_enqueue_script()`函数中的第一个参数。在这种情况下是'【T2]'；
*   `$object_name`。这是一个由`wp_localize_script()`函数创建的对象，可以从`main.js`中的 JavaScript 代码访问它；
*   `$l10n`。这个参数表示从 JavaScript 代码传递到 WordPress 的实际数据，作为一个键/值对的数组。

让我们来看看`wp_localize_script()`的行动。找到您之前在`functions.php`中编写的`themeslug_enqueue_styles_scripts()`函数。就在将 Colorbox JavaScript 文件入队的代码行下面…

```
wp_enqueue_script( 'colorbox', get_template_directory_uri() . 
      '/colorbox/jquery.colorbox-min.js', array( 'jquery'   ), '', true );
```

…添加以下代码:

```
//Make the Colorbox text translation-ready
    $current = 'current';
    $total = 'total';
    wp_localize_script( 'colorbox', 'themeslug_script_vars', array(
        'current'   => sprintf(__( 'image {%1$s} of {%2$s}', 'themeslug'), $current, $total ),
        'previous'  =>  __( 'previous', 'themeslug' ),
        'next'      =>  __( 'next', 'themeslug' ),
        'close'     =>  __( 'close', 'themeslug' ),
        'xhrError'  =>  __( 'This content failed to load.', 'themeslug' ),
        'imgError'  =>  __( 'This image failed to load.', 'themeslug' )
      ) 
    );
```

上面代码中的常规字符串使用`__('string', 'textdomain')`函数进行国际化。然而，`current`和`total`并没有被用作简单的文本字符串。事实上，Colorbox 使用它们作为占位符来分别计算当前照片的数量和照片总数。为了保留这一功能，上面的代码使用了带有适当占位符的 PHP `sprintf()`函数。

接下来，返回到`main.js`中的 Colorbox 配置脚本，将这段代码添加到`cbSettings` JavaScript 对象中，在`title`之后:

```
current: themeslug_script_vars.current,
        previous: themeslug_script_vars.previous,
        next: themeslug_script_vars.next,
        close: themeslug_script_vars.close,
        xhrError: themeslug_script_vars.xhrError,
        imgError: themeslug_script_vars.imgError
```

现在，Colorbox 配置设置代码如下所示:

```
//Settings for lightbox
    var cbSettings = {
      rel: 'cboxElement',
      width: '95%',
      height: 'auto',
      maxWidth: '660',
      maxHeight: 'auto',
      title: function() {
    	return $(this).find('img').attr('alt');
      },
      current: themeslug_script_vars.current,
      previous: themeslug_script_vars.previous,
      next: themeslug_script_vars.next,
      close: themeslug_script_vars.close,
      xhrError: themeslug_script_vars.xhrError,
      imgError: themeslug_script_vars.imgError
    }
```

太棒了，您刚刚配置了国际化设置。

每个键/值对中的值使用前面创建的`wp_localize_script()`函数中的`themeslug_script_vars`对象。正是这个对象使得 PHP 可以访问 JavaScript 变量。

## 结论

至此，使用 jQuery Colorbox 将 lightbox 功能添加到原生 WordPress gallery 的任务已经完成。

要检查结果，将图库添加到帖子或页面，并从*链接到*图库设置中选择*媒体文件*选项。现在从前端进入画廊，点击一张图片。您应该能够看到图像以圆滑的灯箱效果弹出。

如果你对 lightbox 的外观不满意，你可以使用你的主题的`style.css`文件修改你在 Colorbox 样式表中找到的 CSS 代码。

你更愿意使用 WordPress Colorbox 插件，还是喜欢自己编写功能代码？

让我知道你的想法！

## 分享这篇文章