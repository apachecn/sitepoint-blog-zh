# Drupal 走向社会化:在 Drupal 中构建一个“喜欢”模块

> 原文：<https://www.sitepoint.com/drupal-goes-social-building-liking-module-drupal/>

在这篇文章中，我们将看看如何创建一个 Drupal 模块，让你的用户能够*喜欢*你的帖子。该实现将使用 jQuery 进行 AJAX 调用，并异步保存这些数据。

![logo_drupal](img/1c916e37365fcaae8fcf25d267354918.png)

## 创建类似 Drupal 的模块

让我们从创建新的 Drupal 模块开始。为此，我们应该首先在 Drupal 安装的`sites\all\modules\custom`目录中创建一个名为`likepost`的文件夹，如下所示:

![Initial folder structure](img/0df92d63356aacc622ada02b33f05cac.png)

在这个文件夹中，您应该创建一个名为`likepost.info`的文件，其内容如下:

```
name = likepost
description = This module allows the user to like posts in Drupal.
core = 7.x
```

这个文件负责提供关于你的模块的元数据。这允许 Drupal 检测和加载它的内容。

接下来，您应该在同一个目录中创建一个名为`likepost.module`的文件。创建文件后，向其中添加以下代码:

```
/**
 * @file
 * This is the main module file.
 */

 /**
 * Implements hook_help().
 */
function likepost_help($path, $arg) {

    if ($path == 'admin/help#likepost') {
        $output = '<h3>' . t('About') . '</h3>';
        $output .= '<p>' . t('This module allows the user to like posts in Drupal.') . '</p>';
        return $output;
    }
}
```

一旦你完成了这些，你就可以进入你的 Drupal 管理中的模块部分，并且应该能够看到新的模块。暂时不要启用该模块，因为我们会在添加更多功能后再启用。

## 创建模式

一旦创建了模块文件，就可以在模块根文件夹中创建一个`likepost.install`文件。在里面，您将定义一个表模式，它需要为每个用户存储每个帖子上的*赞*。将以下代码添加到文件中:

```
<?php

/**
* Implements hook_schema().
*/
function likepost_schema() {
    $schema['likepost_table_for_likes'] = array(
        'description' => t('Add the likes of the user for a post.'),
        'fields' => array(
            'userid' => array(
                'type' => 'int',
                'not null' => TRUE,
                'default' => 0,
                'description' => t('The user id.'),
            ),

            'nodeid' => array(
                'type' => 'int',
                'unsigned' => TRUE,
                'not null' => TRUE,
                'default' => 0,
                'description' => t('The id of the node.'),
                ),

        ),

        'primary key' => array('userid', 'nodeid'),
    );
    return $schema;
}
```

在上面的代码中，我们实现了 [hook_schema()](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_schema/7) ，以便为我们的表定义模式。在这个钩子中定义的表是在模块安装过程中创建的，在卸载过程中被删除。

我们定义了一个名为`likepost_table_for_likes`的表，它有两个字段:`userid`和`nodeid`。它们都是整数，当用户喜欢一个帖子时，将为每个 userid-nodeid 组合存储一个条目。

一旦添加了这个文件，就可以安装模块了。如果一切正常，您的模块应该没有任何错误地被启用，并且应该在您的数据库中创建表`likepost_table_for_likes`。您还应该在您的`likepost`模块旁边的模块列表中看到启用的帮助链接。如果你点击它，你应该能够看到你在`hook_help()`实现中定义的帮助信息。

![Help Message](img/b01b1610dce3f9ae6ee3db89d7e683ca.png)

## 创建一个菜单回调来处理喜欢

一旦我们启用了这个模块，我们就可以添加一个菜单回调函数，它将处理 AJAX 请求来添加或删除像一样的*。为此，将以下代码添加到您的`likepost.module`文件中*

```
/**
* Implements hook_menu().
*/
function likepost_menu() {
    $items['likepost/like/%'] = array(
        'title' => 'Like',
        'page callback' => 'likepost_like',
        'page arguments' => array(2),
        'access arguments' => array('access content'),
        'type' => MENU_SUGGESTED_ITEM,
    );
    return $items;
}

function likepost_like($nodeid) {
    $nodeid = (int)$nodeid;
    global $user;

    $like = likepost_get_like($nodeid, $user->uid);

    if ($like !== 0) {
        db_delete('likepost_table_for_likes')
        ->condition('userid', $user->uid)
        ->condition('nodeid', $nodeid)
        ->execute();
        //Update the like value , which will be sent as response
        $like = 0;
    } else {
        db_insert('likepost_table_for_likes')
        ->fields(array(
        'userid' => $user->uid,
        'nodeid' => $nodeid
        ))
        ->execute();
        //Update the like value , which will be sent as response
        $like = 1;
    }

    $total_count = likepost_get_total_like($nodeid);
    drupal_json_output(array(
        'like_status' => $like,
        'total_count' => $total_count
        )
    );

}

/**
* Return the total like count for a node.
*/
function likepost_get_total_like($nid) {
    $total_count = db_query('SELECT count(*) from {likepost_table_for_likes} where nodeid = :nodeid',
    array(':nodeid' => $nid))->fetchField();
    return (int)$total_count;
}

/**
* Return whether the current user has liked the node.
*/
function likepost_get_like($nodeid, $userid) {
    $like = db_query('SELECT count(*) FROM {likepost_table_for_likes} WHERE
    nodeid = :nodeid AND userid = :userid', array(':nodeid' => $nodeid, ':userid' => $userid))->fetchField();
    return (int)$like;
}
```

在上面的代码中，我们实现了 [hook_menu()](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_menu/7) ，这样每当用节点 ID 访问路径`likepost/like`时，它就会调用函数`likepost_like()`。

在`likepost_like()`中，我们获取节点 ID 和登录用户的 ID，并将它们传递给函数`likepost_get_like()`。在函数`likepost_get_like()`中，我们检查我们的表`likepost_table_for_likes`，看看这个用户是否已经喜欢这个帖子。如果他有，我们将删除类似内容，否则我们将插入一个条目。一旦完成，我们用节点 ID 作为参数调用`likepost_get_total_like()`,它计算这篇文章上所有用户的总点赞数。然后使用`drupal_json_output()` API 函数将这些值作为 JSON 返回。

这个菜单回调将从我们的 JQuery AJAX 调用中调用，并将使用它接收到的 JSON 更新 UI。

## 在节点上显示“喜欢”按钮

一旦我们创建了回调，我们需要在每个帖子上显示 like 链接。我们可以通过如下实现`hook_node_view()`来实现:

```
/**
 * Implementation of hook_node_view
 */
function likepost_node_view($node, $view_mode) {
    if ($view_mode == 'full'){
        $node->content['likepost_display'] =  array('#markup' => display_like_post_details($node->nid),'#weight' => 100);

        $node->content['#attached']['js'][] = array('data' => drupal_get_path('module', 'likepost') .'/likepost.js');
        $node->content['#attached']['css'][] = array('data' => drupal_get_path('module', 'likepost') .'/likepost.css');
    } 

}

/**
* Displays the Like post details.
*/
function display_like_post_details($nid) {

    global $user;
    $totalLike =  likepost_get_total_like($nid);
    $hasCurrentUserLiked = likepost_get_like($nid , $user->uid);

    return theme('like_post',array('nid' =>$nid, 'totalLike' =>$totalLike, 'hasCurrentUserLiked' => $hasCurrentUserLiked));

}
/**
* Implements hook_theme().
*/
function likepost_theme() {
    $themes = array (
        'like_post' => array(
            'arguments' => array('nid','totalLike','hasCurrentUserLiked'),
        ),
    );
    return $themes;
}

function theme_like_post($arguments) {
    $nid = $arguments['nid'];
    $totalLike = $arguments['totalLike'];
    $hasCurrentUserLiked = $arguments['hasCurrentUserLiked'];
    global $base_url;
    $output = '<div class="likepost">';
    $output .= 'Total number of likes on the post are ';
    $output .= '<div class="total_count">'.$totalLike.'</div>';

    if($hasCurrentUserLiked == 0) {
        $linkText = 'Like';
    } else {
        $linkText = 'Delete Like';
    }

    $output .= l($linkText, $base_url.'/likepost/like/'.$nid, array('attributes' => array('class' => 'like-link')));

    $output .= '</div>'; 
    return $output;

}
```

在`likepost_node_view()`中，我们检查节点何时处于`full`视图模式，并添加由函数`display_like_post_details()`返回的标记。当使用节点内容上的 attached 属性呈现视图时，我们还附加了自定义的 JS 和 CSS 文件。在函数`display_like_post_details()`中，我们得到帖子的*赞*的总数，以及当前用户是否喜欢该帖子。然后我们调用主题函数，它将调用函数`theme_like_post()`，我们已经在‘hook _ theme’的实现中声明了这个函数，但是如果需要的话，它将允许设计者覆盖。在`theme_like_post()`中，我们相应地创建 HTML 输出。链接上的`href`是`$base_url`和附加在它后面的回调路径。节点 ID 也附加到 URL，它将作为参数传递给回调。

完成后，将文件`likepost.css`添加到模块根文件夹，内容如下:

```
.likepost {
    border-style: dotted;
    border-color: #98bf21;
    padding: 10px;
}

.total_count {
    font-weight: bold;
}

.like-link {
    color:red;
}

.like-link:hover {
    color: red;
}
```

现在，如果你进入一篇文章的完整页面，你会看到类似的文章数量，如下所示。

![](img/39171a59966bbc622a40089dcc639d56.png)

## 添加 jQuery 逻辑

现在我们看到显示的 like 链接，我们只需创建包含以下内容的`likepost.js`文件:

```
jQuery(document).ready(function () {

    jQuery('a.like-link').click(function () {
        jQuery.ajax({
            type: 'POST', 
            url: this.href,
            dataType: 'json',
            success: function (data) {
                if(data.like_status == 0) {
                    jQuery('a.like-link').html('Like');
                }
                else {
                    jQuery('a.like-link').html('Delete Like');
                }

                jQuery('.total_count').html(data.total_count);
            },
            data: 'js=1' 
        });

        return false;
    });
});
```

上面的代码将 click 事件绑定到 like 链接，并向回调菜单函数的 URL 发出 AJAX 请求。后者将相应地更新 like post 计数，然后返回新的总计数和 like 状态，这在 AJAX 调用的 success 函数中用来更新 UI。

![Updated UI with Like count](img/ecb8742a65919606df6a58ff3050625c.png)

## 结论

jQuery 和 AJAX 是创建动态响应网站的强大工具。您可以很容易地在 Drupal 模块中使用它们来向 Drupal 站点添加功能，因为 Drupal 已经利用 jQuery 作为其接口。

有反馈？请在评论中告诉我们！

## 分享这篇文章