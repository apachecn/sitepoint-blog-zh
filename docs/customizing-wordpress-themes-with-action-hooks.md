# 用动作挂钩定制 WordPress 主题

> 原文：<https://www.sitepoint.com/customizing-wordpress-themes-with-action-hooks/>

你的 WordPress 主题是定义你的网站外观和感觉的主要核心组件之一。

虽然 WordPress 主题开发已经成为一些开发者和设计师的热门和有利可图的业务，但是所有使用 WordPress 的开发者都应该知道如何定制——如果不是构建——一个高质量的主题。

流行市场中的专业高级主题包含许多内置的特性和组件来定义 WordPress 网站的表现和功能。然而，我们需要能够使主题可扩展和可定制，允许他们的用户脱颖而出，以满足特定的需求。

WordPress 动作钩子为扩展 WordPress 主题提供了一个很好的方法。在本教程中，我们将看看如何有效地使用这些动作钩子。这里我假设你有一些使用 WordPress 动作钩子的基本知识。

## 动作钩子的作用

主题是由开发者的设计者根据他们的想象力构建的。你可能认为你有能力设计出具有所有必要特征的“完美主题”。但是仍然会有人希望你的主题在设计上有一定的修改，然后才愿意在他们自己的网站上使用。

这是我最喜欢的 WordPress 主题 Avada 的截图。

![Avada theme screenshot](img/27ac20038580656fcc11d14b679dc98d.png)

我喜欢功能和设计中的大多数东西，但我不喜欢社交菜单(突出显示)。我更喜欢在这个部分使用我自己的设计。同样，每个用户可能有自己的要求。

没有一个 WordPress 开发者能用任何一个主题设计满足所有人。但是我们可以扩展和修改主题，这样任何人都可以在基本设计和功能的基础上用组件构建一个独特的设计。

WordPress 动作钩子简化了这个过程。有了 action hooks，我们可以根据需要删除现有组件或添加新组件。任何有 WordPress 开发经验的人都可以通过修改主题代码或通过插件添加定制代码来轻松定制现有功能。

## 基本动作挂钩

我们可以选择是否使用动作钩。几乎所有的 WordPress 主题中都有三个动作挂钩。如果没有这些挂钩，任何主题都会受到相当大的限制:

*   `wp_head`

包括在主题的 head 部分，供插件开发者添加他们自己的脚本、样式和必要的信息。调用主题头中的`wp_head`函数，将调用核心代码中的`do_action`函数。

*   `wp_footer`

包括在 HTML 文档的结束 body 标签之前，供插件开发者添加脚本、样式和必要的显示信息。`wp_footer`的工作方式类似于`wp_head`函数，在核心文件中调用`do_action`函数。

*   `comment_form`

用于修改帖子和页面的现有评论表单的设计和字段。

## 开发自定义操作挂钩

现在是时候开发我们自己的特定于主题的定制钩子了。钩子的真正威力来自这种方法，因为我们可以在主题的任何地方定义任意数量的定制钩子。

首先让我们考虑下面的代码，它为我们的主题创建了一个定制的动作钩子。

```
function wpr_after_post_title() {

echo "<img src='promotion-image.jpg' />";

}

add_action('wpr_after_post_title','wpr_after_post_title');
```

如今，许多提供产品或服务的网站都有博客或文章版块来吸引读者和推销他们的产品。所以他们写一些关于产品相关信息的文章。目标是通过博客营销产品。通常，我们会看到广告或促销就放在文章标题的后面。在这种情况下，我们可以使用前面的代码来实现该功能。

`add_action`函数采用应用程序独有的自定义动作名称和函数名称。然后我们可以在文章标题后的主题中添加`do_action`来显示推广横幅，如下面的代码所示。

```
<h2><a href="<?php the_permalink(); ?>"><?php the_title(); ?></a></h2>

<?php do_action('wpr_after_post_title'); ?>
```

像这样在重要的位置使用 action hooks，可以让插件开发者快速添加自定义特性，而无需深入核心主题代码。

## 单个动作钩子的多次执行

一旦你定义了自定义的动作挂钩并将其公开，任何开发者都可以使用各种插件来实现它们。在两个或更多的开发者在他们的插件中实现同一个钩子的情况下，动作的优先级成为一个问题。

考虑下面的代码，其中两个插件实现相同的动作。

```
<?php

function wpr1_after_post_title() {

// Plugin 1

}

add_action('wpr_after_post_title','wpr1_after_post_title');

function wpr2_after_post_title() {

// Plugin 2

}

add_action('wpr_after_post_title','wpr2_after_post_title');

?>
```

在这种情况下，首先执行哪个动作取决于插件名称以及插件在`wp_options`表的`active_plugins`字段中的位置。假设我们想在插件 1 的输出之前显示插件 2 的输出。我们不能用上面的技术让它工作，因为插件 1 首先位于`active_plugins`字段。

此时，操作挂钩的优先级参数变得很有用。我们可以传递一个数值作为第三个参数来定义动作钩子的执行优先级。让我们考虑一下前面带有优先级值的例子。

```
function wpr1_after_post_title() {

// Plugin 1

}

add_action('wpr_after_post_title','wpr1_after_post_title',50);

function wpr2_after_post_title() {

// Plugin 2

}

add_action('wpr_after_post_title','wpr2_after_post_title',40);
```

现在，您将能够根据给定的优先级，在插件 1 功能之前看到插件 2 功能的执行。随着值变高，优先级将变低，因此将在页面中进一步执行。

因此，最好用优先级值来定义您的操作挂钩，以便可以根据所需的定位来调整其他操作的优先级。

## 如何给主题添加可插拔的动作钩子？

WordPress 动作钩子实现由两部分组成。首先我们使用`add_action` 函数定义一个动作钩子。然后我们使用`do_action`执行动作钩子。作为主题开发者，你的责任是在必要的时候执行 action hook。定义和实现动作需要由插件开发者来完成。所以每当你想使用一个动作钩子时，只要把下面的代码放在必要的位置就可以了。

```
<?php do_action("action hook name"); ?>
```

提供动作挂钩使得插件开发者可以很容易地为主题布局添加新的部分和内容。但是，如果有人想改变现有的部分呢？

考虑下面的布局。

![dummy layout screenshot](img/c28022644652c5e77728e9ae3d7f255f.png)

这个屏幕显示了 WordPress 主题的博客文章设计。标题下面可以看到发帖日期和作者。类别和标签也放在最后。假设你不喜欢这些组件的设计，想自己设计。

如果这些部分被直接插入到主题中，插件开发者很难在不修改核心主题代码的情况下改变设计。让我们看看如何提供一个带有行动挂钩的解决方案。

```
<?php

// Post title code

do_action("post_date_and_author");

// Post content

do_action("post_category");

do_action("post_tags");

?>
```

所有的部分都被定义为动作挂钩，而不是直接嵌入到 HTML 文档中。下面的代码在主题函数文件中实现了 **post_date_and_author** 动作。

```
<?php

function post_date_and_author(){

echo "Date area related HTML code";

}

add_action('post_date_and_author','post_date_and_author');

?>
```

现在，您可以替换现有的设计组件，并向主题添加新的组件。下面是前面场景在自定义插件中的实现。

```
<?php

function wpr_post_date_and_author(){

echo "New design for date section by the plugin";

}

add_action('post_date_and_author','wpr_post_date_and_author');

function wpr_remove_custom_actions() {

remove_action('post_date_and_author','post_date_and_author');

}

add_action('init','wpr_remove_custom_actions');

?>
```

在这里，开发人员为日期和作者部分提供了他们自己的设计。主题定义的原始`post_date_and_author`钩子将在`init`动作上被移除。

***重要:*** *要删除一个钩子，添加钩子时的`$function_to_remove`和`$priority`参数必须匹配。这适用于过滤器和动作。移除失败时不会给出警告。–WordPress Codex*

然后，我们用插件特定的函数添加相同的动作来修改日期部分的设计。

以下屏幕预览了如何更改先前的布局以提供不同的设计。

![revised dummy layout screenshot](img/ce555f8221ccf93252a4ab919fa1bca1.png)

## 记录可插入的动作挂钩

您可以添加任意数量的动作挂钩，增加功能，使您的主题高度可定制。插件开发者不知道可用的主题挂钩，除非他们探索源代码来找到它们。因此，理想的做法是记录你的主题中可用的动作钩子。

使用动作挂钩的一种方法是添加脚本、样式和类似的内容，它们在内部工作，而不在浏览器上显示任何内容。另一种方法是直接修改页面布局来添加设计组件或删除现有组件。

如果您使用操作挂钩，您的文档需要包括细节，例如模板中操作挂钩的位置、首选参数、内容的尺寸以及在给定位置首选的设计组件类型。

这是由你来决定在你的主题中应用动作钩子的地方。我会在以下部分使用动作挂钩:

*   在主菜单之后
*   在帖子或页面内容之后
*   文章标题前后
*   页脚部分之前
*   侧边栏小部件之前和之后

现在是时候通过分享我们的经历来互相帮助了。请随意添加你对动作挂钩的建议，以及你如何在你的 WordPress 主题中有效地使用它们。

期待收到你的来信。

## 分享这篇文章