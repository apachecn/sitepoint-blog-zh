# 使用 WordPress 上下文帮助屏幕启动和运行

> 原文：<https://www.sitepoint.com/wordpress-contextual-help-screens/>

开发者努力构建有益于用户的插件和主题。例如，无论是电子商务系统、预订插件还是画廊展示，都有一两个专门的开发人员努力将一切整合在一起。

另一方面，文档不幸地变成了事后的想法。它通常要么是 WordPress 插件目录上的几个句子，要么是 GitHub 上的一系列注释，或者如果你幸运的话，是一个维护很差的外部网站(可能随时会消失)。

WordPress 自带一个内置的**上下文帮助屏幕**，可以从管理面板的任何地方访问。你可能以前见过这个，它喜欢藏在你页面的右上角(用“帮助”按钮切换)。

![contextual help screen sample](img/15fd8cf22227d582681d6eab9138a5f7.png)

今天我将介绍如何使用 WordPress 上下文帮助屏幕来记录你的主题/插件的重要部分，比如常用的设置、简码或其他功能。

## 与 WP_Screen 交互

要添加你的新帮助标签到你的管理页面，你将与 [`WP_Screen`](https://codex.wordpress.org/Class_Reference/WP_Screen) 类交互，当 WordPress 加载任何管理区域时会引用这个类。

代替直接使用`WP_Screen`类，我们将使用 [`get_current_screen()`](https://codex.wordpress.org/Function_Reference/get_current_screen) 函数来告诉我们当前正在查看哪个管理页面。这告诉我们一些重要的信息，比如我们是否在一个文章类型或分类上，以及我们的基本文件是什么。

### 将 WordPress 上下文帮助屏幕添加到所有管理区域

添加我们的帮助屏幕是一个简单的过程。一旦我们访问了当前的屏幕对象，我们就调用`WP_Screen`类的 [`add_help_tab`](https://codex.wordpress.org/Class_Reference/WP_Screen/add_help_tab) 方法。

```
function add_context_menu_help(){

    //get the current screen object
    $current_screen = get_current_screen();

    //content for help tab
    $content = '<p>Im a help tab, woo!</p>';

    //register our main help tab
    $current_screen->add_help_tab( array(
            'id'        => 'sp_basic_help_tab',
            'title'     => __('Basic Help Tab'),
            'content'   => $content
        )
    );

    //register our secondary help tab (with a callback instead of content)
    $current_screen->add_help_tab( array(
            'id'        => 'sp_help_tab_callback',
            'title'     => __('Help Tab With Callback'),
            'callback'  => 'display_help_tab'
        )
    );
}
add_action('admin_head', 'add_context_menu_help');

//function used to display the second help tab
function display_help_tab(){
    $content = '<p>This is text from our output function</p>';
    echo $content;
} 
```

为 help 选项卡创建输出有两种不同的方式，一种是内联定义内容并将其传递给`add_help_tab`方法，另一种是概述用于输出的函数名。两者都将完美运行。

你只需要提供你的标签`id`、`title`和内容，其余的由 WordPress 完成。

### 选择性添加上下文帮助

通常，你不希望在后端的每一页都显示你的帮助标签，你只希望在它们相关的时候显示它们(例如，当查看一个定制文章类型的编辑屏幕或者一个分类术语列表等)。

在这里，您可以利用通过`get_current_screen()`收集的当前屏幕项目来检测您所在的位置，并有选择地定位您的帮助屏幕。

对于我们的例子，假设我们只想在查看自定义“图书”文章类型的项目时(当我们查看图书项目列表时)添加帮助选项卡。

```
function add_help_screen_to_books(){

    //get the current screen object
    $current_screen = get_current_screen();

    //show only on book listing page
    if($current_screen->post_type == 'book' && $current_screen->base == 'edit'){
        $content = '';
        $content .= '<p>This is a help tab, you can add <strong>whatever</strong> it is you like here, such as instructions </p>';
        $current_screen->add_help_tab( array(
                'id'        => 'sp_book_help_tab',
                'title'     => __('Book Help Tab'),
                'content'   => $content
            )
        );
    }
}
add_action('admin_head', 'add_help_screen_to_books'); 
```

只有在查看图书内容类型的管理列表时，才会显示上面的帮助选项卡。我们通过查看当前的`post_type`和`base`值来做到这一点(base 告诉我们，我们在一个管理编辑屏幕上)。

![contextual help screen book](img/be1ba8fddf8fbfbf9648da7bd9d7a0b3.png)

### 额外收获——更改上下文帮助侧栏

这是你可以用上下文帮助菜单做的另一件好事。您可以自定义边栏来显示额外的信息。

需要注意的一点是调用`set_help_sidebar`的顺序。必须在添加帮助选项卡后调用此方法，否则什么都不会发生。

一个简单的方法是使用具有不同优先级的`admin_head`钩子来确保当你调用`set_help_sidebar`的时候你的标签已经被注册了。

让我们继续添加我们自己的自定义侧边栏。请注意，它只在我们编辑单个帖子(任何类型，如帖子、页面或书籍)时显示。

```
//adds a sidebar to the help context menu
function add_help_sidebar(){

    //get the current screen object
    $current_screen = get_current_screen();

    //show only on listing / single post type screens
    if($current_screen->base == 'edit' || $current_screen->base == 'post'){
        $current_screen->add_help_tab( array(
                'id'        => 'sp_book_sample',
                'title'     => __('Book Help Tab'),
                'content'   => '<p>This is a simple help tab, hi </p>'
            )
        );
        //add the help sidebar (outputs a simple list)
        $current_screen->set_help_sidebar(
            '<ul><li>Here is a list item</li><li>Here is a second item</li><li>Final item</li></ul>'
        );
    }
}
add_action('admin_head', 'add_help_sidebar'); 
```

看到边栏的变化了吗？你想放什么都可以。

![contextual help screen sidebar](img/4fcafc204c56afe6c6e027d79687d99d.png)

## 注释和有用的提示

几个在线资源，包括 [WordPress codex](https://codex.wordpress.org/Class_Reference/WP_Screen/add_help_tab) 建议将你的功能挂在`load-{name}`钩子上。例如，通过使用`add_action('load-post.php')`在加载帖子时挂钩。这可能工作得很好，但是，我发现把它附加到`admin_head`动作上也很好，它为每个页面加载(让你在函数内部决定是否应该添加帮助标签)。

您可以在这些上下文帮助屏幕中使用任何您想要的东西。您可能想要概述我们的短代码的选项(它将只显示在显示可视化编辑器的屏幕上，比如在帖子上)。或者，您可能希望广泛使用上下文菜单并添加快速功能，如查看您最近的帖子及其评论。

## 把一切都包起来

WordPress 上下文帮助菜单很棒。它们很容易使用，你可以定制它们向你的用户提供信息的方式。它们在 WordPress 3.3 中就已经存在了，是储存有用信息的最佳位置。

对于你的下一个主题或插件，你一定要考虑在这些部分添加尽可能多的相关信息。对于用户来说，这比在插件库/外部第三方网站上搜寻信息要容易得多。

## 分享这篇文章