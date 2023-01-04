# 在 Drupal 8 中创建定制字段格式化程序

> 原文：<https://www.sitepoint.com/creating-custom-field-formatters-drupal-8/>

请注意，由于撰写本文时 Drupal 8 正在经历开发过程，部分代码可能已经过时。看一下[这个库](https://github.com/upchuk/image_title_caption)，我试图在其中更新示例代码，并使其与最新的 Drupal 8 版本兼容。

随着带注释插件的引入，Drupal 8 中发生了很多变化。我们有一个更精简的方法来描述和发现扩展核心的功能。与许多其他组件一起，以前的 Field API(更大和合并的实体 API 的一部分)现在基于插件。

![](img/077a30fb14c973a02eea34dedb6e8ea1.png)

在本教程中，我们将为现有字段(图像)定义一个自定义字段格式化程序。我们想要实现的是能够显示一个下面有小标题的图像。这个标题将是分配给图像的`title`值(如果有的话)。

我们在这里写的代码可以在[这个库](https://github.com/sitepoint-editors/image_title_caption)中找到，作为`image_title_caption`模块。但是让我们看看如何才能得到最终结果。

## Drupal 模块

让我们从创建一个只有一个文件的新定制模块(`image_title_caption`)开始:

**image _ title _ caption . info . yml**:

```
name: Image title caption
type: module
description: Uses the image title field as a caption
core: 8.x
dependencies:
  - image
```

这里没什么异常。如果需要，我们甚至可以启用该模块。

## 插件

在 Drupal 8 中，字段格式化器(像字段类型和小部件本身)是插件。核心模块或者由核心模块定义，或者可以在`Drupal\Core\Field\Plugin\Field\FieldFormatter`名称空间中找到。就像我们在[之前的文章中看到的自定义模块](https://www.sitepoint.com/building-drupal-8-module-blocks-forms/)，插件放在我们模块的`src/Plugin/`文件夹中。对于字段格式化程序，这将是`src/Plugin/Field/FieldFormatter`目录。

下面你可以看到我们自己的格式化程序类:

**src/Plugin/Field/Field formatter/image title caption . PHP**:

```
<?php

/**
 * @file
 * Contains \Drupal\image_title_caption\Plugin\Field\FieldFormatter\ImageTitleCaption.
 */

namespace Drupal\image_title_caption\Plugin\Field\FieldFormatter;

use Drupal\Core\Field\FieldItemListInterface;
use Drupal\image\Plugin\Field\FieldFormatter\ImageFormatter;

/**
 * Plugin implementation of the 'image_title_caption' formatter.
 *
 * @FieldFormatter(
 *   id = "image_title_caption",
 *   label = @Translation("Image with caption from title"),
 *   field_types = {
 *     "image"
 *   }
 * )
 */
class ImageTitleCaption extends ImageFormatter {

  /**
   * {@inheritdoc}
   */
  public function viewElements(FieldItemListInterface $items) {
    $elements = parent::viewElements($items);
    foreach ($elements as &$element) {
      $element['#theme'] = 'image_title_caption_formatter';
    }

    return $elements;
  }

}
```

这是我们的插件。没别的了。在类声明之上，我们有 [@FieldFormatter](https://api.drupal.org/api/drupal/core!lib!Drupal!Core!Field!Annotation!FieldFormatter.php/class/FieldFormatter/8) 注释，通过它插件被发现。我们指定了一个插件 ID ( `image_title_caption`)、标签和这个格式化程序可以使用的字段类型数组。在我们的例子中，后者只包含默认的`image`字段类型，但是如果我们愿意，我们可以支持更多的类型，甚至是定制的字段类型。数组中的值是插件 ID，所以如果你查看[图像字段类型插件](https://api.drupal.org/api/drupal/core%21modules%21image%21src%21Plugin%21Field%21FieldType%21ImageItem.php/class/ImageItem/8)，你会看到它的 ID 是`image`。

这个类看起来很简单，因为我们从核心图像模块定义的默认`ImageFormatter`插件扩展而来。出于我们的目的，我们需要覆盖的只是负责返回字段数据的渲染数组的`viewElements()`方法。后者可以在`$items`列表中找到，可以使用并准备渲染。

在这个方法中，我们要做的第一件事是确保我们调用项目的父类方法，并将其存储在一个变量中。这已经准备好了要渲染的图像，就像正常情况下一样。

默认情况下，`ImageFormatter`插件(父插件)使用渲染数组中的`image_formatter`主题来输出图像字段值。我们在这里做的是，对于每一个项目，我们用我们自己的主题替换这个主题:`image_title_caption_formatter`。然后，我们像父元素一样返回元素(呈现数组)。

你会在 Drupal 8 中注意到这一点:我们从我们扩展的父类中得到一个很好的指示，告诉我们需要做什么。如果你问我，这比弄清楚一些神奇的钩子或函数做什么要好得多。

## 主题

由于我们上面指定的主题是虚构的，我们需要创建它。在我们模块的`.module`文件中，我们需要实现`hook_theme`:

**image _ title _ caption . module**:

```
/**
 * Implements hook_theme().
 */
function image_title_caption_theme() {
  return array(
    'image_title_caption_formatter' => array(
      'variables' => array('item' => NULL, 'item_attributes' => NULL, 'url' => NULL, 'image_style' => NULL),
    ),
  );
}
```

这应该看起来很熟悉，因为它与 Drupal 7 非常相似。请注意我们传递给这个主题的变量。我们打算覆盖默认的`image_formatter`主题，所以我们也应该在这里传递相同的变量。此外，由于`image_formatter`主题是经过预处理的，我们还需要为我们的主题创建一个预处理程序:

```
/**
 * Implements template_preprocess_image_title_caption_formatter().
 */
function template_preprocess_image_title_caption_formatter(&$vars) {
  template_preprocess_image_formatter($vars);
  $vars['caption'] = String::checkPlain($vars['item']->get('title')->getValue());
}
```

在这个预处理器中，我们执行两个操作:

*   我们确保传递给模板文件的变量将首先被默认的`image_formatter`主题预处理程序预处理。这是为了使所有的变量完全相同，图像正常显示。
*   我们创建了一个名为`caption`的新变量，它将包含图像标题的净化值。

为了净化，我们静态地使用助手`String`类。我们仍然在`.module`文件中，所以我们不能注入它，但是我们需要*在文件的顶部使用*:

```
use Drupal\Component\Utility\String;
```

## 模板

最后，我们需要为新主题创建一个模板文件:

**templates/image-title-caption-formatter . html . twig**:

```
{% if url %}
  <a href="{{ url }}">{{ image }}</a>
{% else %}
  {{ image }}
{% endif %}
{% if caption %}
  <div class="image-caption">{{ caption }}</div>
{% endif %}
```

与 Drupal 7 类似，这个文件的名称很重要，因为它反映了主题名称。至于内容，除了底部印的标题，和`image_formatter`主题用的模板几乎一样。

## 有用吗？

既然我们已经编写了代码，我们需要启用模块，并且如果我们在启用后进行了代码更改，则清除所有缓存。是时候检验一下了。

例如，转到`admin/structure/types/manage/article/display`的文章内容类型字段显示设置。对于图像字段，在`Format`标题下，您应该能够选择`Image with caption from title`格式。保存表格并转到`admin/structure/types/manage/article/fields/node.article.field_image`，确保图像字段标题已启用。

最后，你可以编辑文章，上传图片并指定标题。该标题将继续如此，但另外，它将作为标题显示在图像下方。当然，你仍然可以随心所欲地设计它。

## 结论

在本文中，我们看到了在 Drupal 8 中创建字段格式化程序和扩展默认行为是多么容易。我们只涉及了覆盖这个插件的`viewElements()`,但是我们可以做更多的事情来进一步定制。您也不需要延长`ImageFormatter`。有很多现有的插件，你可以从中扩展或者作为一个例子来使用。

此外，您还可以轻松创建新的字段类型和小部件。这是一个类似的过程，但是您需要考虑一些模式信息，使用不同的注释类并编写更多的代码。但关键是你这样做很灵活。

## 分享这篇文章