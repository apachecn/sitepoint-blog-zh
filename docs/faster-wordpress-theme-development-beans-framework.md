# 使用 Beans 框架加快 WordPress 主题开发

> 原文：<https://www.sitepoint.com/faster-wordpress-theme-development-beans-framework/>

如果你迫不及待地想要构建自己的自定义 WordPress 主题，使用一个良好编码的、可靠的主题框架是一个很好的开始。框架为 WordPress 主题提供了通用的功能和组件，让你创建自己独特的设计。这极大地加快了开发时间。

在这篇文章中，我将与 WordPress 框架块中的一个新成员进行实验，这个新成员是由[蒂埃里·穆勒](https://github.com/ThierryA)创建的 [Beans](http://www.getbeans.io/) 。詹姆斯·乔治之前也在 SitePoint 的文章[介绍豆子](https://www.sitepoint.com/introducing-beans-streamlined-wordpress-framework/)中涉及过这个话题。

## 关于 Beans 主题框架的更多信息

Beans 是一个**开源**、**免费**的 WordPress 主题框架，拥有许多令人敬畏的特性，这使得它非常适合新手和有经验的开发者。以下是主要功能:

*   Beans 是**移动优先**，完全**响应**，并且足够智能以最适合所用设备的分辨率创建和提供图像。
*   精益代码、缓存、选择性加载资产和缩小技术使 bean 成为令人惊讶的轻量级主题和快速加载主题。当涉及到谷歌页面排名和你的网站访问者的快乐时，这是很棒的。
*   UIkit 前端框架赋予主题干净现代的外观。然而，如果您想知道使用 Beans 进行开发是否会将您与 UIkit 捆绑在一起，请不要担心。Beans 是高度**灵活的**，并且让你**在你需要的时候加载资产**。

是时候拿回你的 Beans 并进行测试了。

## 安装 Beans

你可以从 [Bean 的网站](http://www.getbeans.io/)获取最新版本的 Bean。提取存档的。zip 文件，然后将`tm-beans`文件夹复制到 WordPress 安装的`themes`目录中。

最后，从主题面板中激活 Beans 并查看一下。这是豆子开箱后的样子。

![Beans theme out of the box](img/0b013c126d9ba30e4df24f1f8c674f2d.png)

在较小的屏幕上看，这个主题带有一个漂亮的内置**非画布侧边栏**。

![Beans off-canvas sidebar](img/7429e01c2bd0bed3b655983a11b7365b.png)

你可以从 WordPress 安装程序的外观>部件面板添加部件到这个区域。

![Widgets area in Beans](img/36e75ca715fb777a6f83d9a946f0c720.png)

甚至不用接触一行代码，从**定制器**面板你可以快速添加一个 logo 和一个站点图标，改变背景颜色或者添加一个背景图片，添加一个标题图片，并且从下面的**六个页面布局选项中选择**:

*   全幅–无侧边栏。
*   两栏——主要内容在左边，侧栏在右边。
*   两栏——主要内容在右边，侧栏在左边。
*   三栏——主栏在左边，两个侧栏在右边。
*   三栏–主栏在右边，两个侧栏在左边。
*   三栏——主栏在中间，一个侧栏在左边，一个在右边。

![Beans Customizer layout options](img/f2a10ba2ccfa9a32b62b1ade7305278e.png)

另一方面，如果你像我一样，无法抗拒控制你的主题的每个角落和缝隙的冲动，深入代码是你的下一步。

## 对 Beans 使用子主题

使用 Beans 的最佳方式是作为**父主题**。当您安装更新版本的 Beans 时，向子主题添加修改会保留它们。

下载一个 [Beans Starter 子主题](http://www.getbeans.io/documentation/starter-child-theme/)的副本并解压存档。压缩文件。

以下是您在 Beans 子文件夹中找到的内容:

![Beans child directory structure: functions.php, style.css, style.less, screenshot.png](img/a4cc9ca51cbc70f475da28ed70ce919a.png)

要开始，请遵循以下步骤。

### 1.重命名子主题

很有可能你的主题不会被叫做*豆子孩子*。用您的主题名称给子主题的文件夹重新命名是个好主意。

### 2.在样式表中自定义 WordPress 标题注释

在代码编辑器中打开`style.css`。要使子主题成为您自己的主题，请将您的主题的详细信息添加到主题名称、描述、作者和作者 URI 字段。保存并关闭文档。

### 3.定制`functions.php`

在代码编辑器中打开`functions.php`。该文档中的第一行代码使 Beans 框架可用于子主题:

```
require_once( get_template_directory() . '/lib/init.php' ); 
```

重要的是你**不要删除**这行代码。

接下来，您将找到将 UIKit 框架文件入队所需的代码:

```
add_action( 
  'beans_uikit_enqueue_scripts',
  'beans_child_enqueue_uikit_assets' 
);

function beans_child_enqueue_uikit_assets() {

  beans_compiler_add_fragment( 
    'uikit', 
    get_stylesheet_directory_uri() . '/style.less',
    'less' 
  );

} 
```

上面的代码允许你从子主题中快速覆盖 UIKit Less 变量到你的 heart 的内容。如果您不想使用更少的代码，只需删除这些代码。

最后，下面的代码片段将子主题的`style.css`文件排入队列:

```
add_action( 
  'wp_enqueue_scripts',
  'beans_child_enqueue_assets' 
);

function beans_child_enqueue_assets() {
  wp_enqueue_style( 
    'child-style',
    get_stylesheet_directory_uri() . '/style.css' 
  );
} 
```

如果你打算少用而不是简单的旧 CSS，那就删除上面的代码。

保存您的工作并关闭文件。

### 4.激活主题并调整 Beans 设置

现在，将你的子主题目录复制到 WordPress 安装的主题文件夹中，并从外观>主题面板中激活它。

最后一件事:访问 Appearance > Beans Settings 并确保选中了 **Enable development mode** 选项。

![Beans settings panel from the Appearance Admin menu](img/4a8f3dda15cb67ddfa3998857009adf5.png)

这样做可以让 Beans 动态地编译你的代码。

一切都准备好了，现在开始编写 Beans 驱动的儿童主题吧！

## 用更少的资源修改外观和感觉

从`style.less`开始，定制你的孩子主题的外观和感觉很容易。在您的编辑器中打开它，并开始覆盖一些 UIKit Less 变量以适应您的设计。您将在 Beans 父主题的`lib/assets/less/uikit-overwrite`中找到 UIKit Less 代码。

例如，要更改标题颜色，请覆盖原始变量的值，如下所示:

```
@base-heading-color : #baa8da; 
```

现在，保存并刷新您的页面。网站看起来一样吗？主题标题的颜色应该是这样的:

![New color for headings overwriting UIKit variables](img/40676d3467baec18bfde0fcc3fd7328e.png)

Beans 会自动将 Less 编译成 CSS，而无需您付出任何努力。

## 自定义模板文件

如果您打开任何一个 Beans 模板文件，您很快就会注意到它们包含的只是一行代码。例如，下面是您在`index.php`中发现的内容:

```
<?php
  beans_load_document(); 
```

事实上，您可以通过`functions.php`中的[动作钩子](https://www.sitepoint.com/customizing-wordpress-themes-with-action-hooks/)定制 Beans 模板文件。这听起来有点吓人，但是一旦你熟悉了主题的 API，你会很高兴修改一小段标记不需要覆盖整个模板文件。

这里有几个例子可以帮助你开始。

### 移除 CSS 类

假设你想增加标语的尺寸。

![Tagline as it looks in Beans out of the box](img/d7ded5bb58e8a18aa55d4e0b8894e316.png)

以下是您需要在`functions.php`中填写的内容:

```
beans_remove_attribute( 
  'beans_site_title_tag',
  'class', 
  'uk-text-small' 
); 
```

保存并刷新。现在标语应该看起来更大了。

刚刚发生了什么？

标志行的大小受 UIKit 类`.uk-text-small`的约束。去掉这个类，就达到了预期的目的。为此，您使用 [`beans_remove_attribute()`](http://www.getbeans.io/code-reference/functions/beans_remove_attribute/) 功能。

该函数有三个参数:

*   `$id`。通过检查源文档中的目标元素，可以很容易地找到该参数的值。当处于开发模式时，Beans 向每个 HTML 元素注入一个`data-markup-id`属性。您可以使用该属性的值来获取代码中的元素，并使用 API 对其进行操作。对于标语，`data-markup-id`的值是`beans_site_title_tag`，这是您在上面的函数中作为第一个参数添加的。

    ![data-markup-id value in the Beans source code](img/fe47ed6dcb728647a01b2a008b10263f.png)

*   `$attribute`。这是您想要操作的 HTML 属性的名称。在这种情况下，您希望删除元素的`class`属性。

*   `$value`。这是您想要移除的属性的值。对于这个例子，它是`.uk-text-small`类。

查看其他与 HTML 相关的函数，用一行代码添加和替换 CSS 类等等。

### 添加标记

以下是如何使用**动作钩子**快速添加 HTML 标记片段的方法。

UIKit 捆绑了来自[字体牛逼](http://fontawesome.io/)的好看图标。尝试在每个发布日期元信息前添加一个时钟图标。下面是放入您的`functions.php`文件的代码。

```
beans_add_action( 
  'beans_post_meta_item_date_prepend_markup',
  'beans_child_add_post_meta_date_icon' 
);

            function beans_child_add_post_meta_date_icon() {
        ?><i class="uk-icon-clock-o uk-margin-small-right uk-text-muted"></i><?php
            } 
```

上面的示例代码使用了`beans_post_meta_item_date_prepend_markup`。第一部分——`beans_post_meta_item_date`——是 Beans 的值`data-markup-id`(您可以使用浏览器开发工具检查源代码来发现这一点)。第二部分——`prepend_markup`——是您的标记注入函数为完成其工作而挂接的动作。

要了解更多关于操作 HTML 标记的动作钩子的知识，请访问[标记和属性文档](http://www.getbeans.io/documentation/markup-and-attributes/)页面。在这里，您会发现每一个带有 bean`data-markup-id`的标记都有以下动作挂钩:

*   `{$markup_id}_before_markup`，在开始标记之前触发；
*   `{$markup_id}_prepend_markup`，在开始标记后触发(不适用于自结束标记)；
*   `{$markup_id}_append_markup`，在结束标记之前触发(不适用于自结束标记)；
*   `{$markup_id}_afer_markup`，在结束标记后触发。

最后，您的`beans_child_add_post_meta_date_icon()`函数打印显示图标所需的标记。

如果你是一个超级 WordPress 开发者，正在开发一个发布主题，你也可以利用 Beans Actions 和 HTML API。这样做将使您的主题以与 Beans 框架一致的方式更具可扩展性。

例如，您可以在发布日期元信息之前添加一个时钟图标，就像您在上面的示例中所做的那样，使用以下代码:

```
 beans_add_smart_action( 
  'beans_post_meta_item_date_prepend_markup',
  'beans_child_super_dev_meta_date_icon' 
);

function beans_child_super_dev_meta_date_icon() {
  echo beans_open_markup( 
    'beans_child_date_icon',
    'i', 
    'class=uk-icon-clock-o uk-margin-small-right uk-text-muted' 
    );
  echo beans_close_markup( 'beans_child_date_icon', 'i' );
} 
```

[`beans_add_smart_action()`](http://www.getbeans.io/code-reference/functions/beans_add_smart_action/) 需要以下参数:

*   `$hook`。这是回调函数所挂钩的动作的名称。
*   `$callback`。这是您希望执行的回调函数的名称。

自定义`beans_child_super_dev_meta_date_icon()`功能使用 [`beans_open_markup()`](http://www.getbeans.io/code-reference/functions/beans_open_markup/) 和 [`beans_close_markup()`](http://www.getbeans.io/code-reference/functions/beans_close_markup/) 在你想要的地方喷出 UIKit 时钟图标。

## 演示主题:构建主页网格布局

本文的[可下载演示主题](https://github.com/antonellaP/Beans-Child-SitePoint-Demo)展示了一种完全改变 Beans 中博客索引页面默认外观的方法。

最终结果应该是这样的。

![Grid layout in Blog index page](img/b228ea55d73b0d64ad737d3dd753e1a5.png)

繁重的工作都由 Beans 和 UIKit CSS 类来完成。您只需更改博客索引页面的默认布局选项，并将正确的 UIKit CSS 类添加到标记中。

因为这种布局仅适用于博客索引页面，所以您可以在以下选项中进行选择:

*   将布局代码添加到`functions.php`。只需将片段包装在一个条件语句内，即`if( is_home() ) //Your code here`；或者
*   向子主题添加一个`home.php`页面模板。

我的演示主题有相当多行特定于博客索引页面的代码，因此我选择了第二个选项。事实上，在这种特定的情况下，添加页面模板比多次编写相同的条件语句更好。

您可以自由选择最适合您设计的选项。但是，如果您选择为您的子主题创建一个模板文件，不要忘记在文档的底部添加下面一行代码:

```
// Load Beans document.
beans_load_document(); 
```

### 将默认页面布局更改为全幅

主题的默认*站点范围*布局是两列布局。因此，第一步是将博客索引页面的默认布局*变成全幅布局。*

```
Make page fullwidth as default layout in home page
    add_filter( 'beans_layout', 'beans_child_default_home_layout' );

                function beans_child_default_home_layout() {
                    return 'c';
                } 
```

上面的代码使用了“beans_layout”过滤器挂钩。挂钩函数只是返回全幅布局的 ID，即 **c** 。

您可以在 bean[布局代码片段页面](http://www.getbeans.io/code-snippets/force-a-layout/)上获得您的主题中所有可用布局选项的 ID。

### 构建网格

实现平铺布局的一种方法是添加以下代码。

```
//Adjust the UKIt grid for the index posts page
beans_add_attribute( 
  'beans_content',
  'class', 
  'uk-grid uk-container-center' 
);

beans_add_attribute( 
  'beans_post',
  'class', 
  'uk-width-large-1-3 uk-width-medium-1-2 uk-width-small-1-1' 
); 
```

上面的代码片段执行以下两项任务:

*   它以具有`beans_content`的`data-markup-id`值的标记为目标。然后，它通过添加`.uk-grid`类将这个元素变成一个网格容器；
*   最后，它将所有具有`beans_post`的`data-markup-id`值的元素作为目标。它通过添加以下类将这些元素转化为响应性网格项:`.uk-width-large-1-3 .uk-width-medium-1-2 .uk-width-small-1-1`。将 [UIKit grid classes](http://getuikit.com/docs/grid.html) 翻译成简单的英语，这意味着:在大屏幕上显示三列布局，在中等屏幕上显示两列布局，在小屏幕上显示一列布局。

就这样，恭喜你！剩下的大部分代码只是一点 CSS，让它看起来更好。

## 结论

现在，您应该对 Beans 框架的功能和特性有了很好的理解。接下来去哪里？

您可以:

*   潜入豆子[文档](http://www.getbeans.io/documentation/)和[代码参考](http://www.getbeans.io/code-reference/)；
*   接触不断增长的[代码片段库](http://www.getbeans.io/code-snippets/)；
*   逆向工程的一些免费豆类儿童主题。从本文的[可下载演示主题](https://github.com/antonellaP/Beans-Child-SitePoint-Demo)开始，然后前往[主题管家](http://www.themebutler.com/)获取更多内容。

Beans 是一个相对较新的 WordPress 主题框架，所以如果你碰巧遇到任何错误，或者有任何特定的功能需求，请在 [GitHub](https://github.com/Getbeans/Beans/issues) 上留下你的消息。如果你想加入这个不断发展的主题社区，[在 Twitter 上关注豆子](https://twitter.com/BeansPress)。

为什么不试试豆子，让我知道你的想法？我期待你的主题和你的反馈！

## 分享这篇文章