# 扩展 October CMS——构建一个软删除插件

> 原文：<https://www.sitepoint.com/extending-octobercms-building-a-soft-delete-plugin/>

开发人员通常坚持使用新的 CMS，因为它简单且可扩展。OctoberCMS 是一个回归基础的 CMS，为开发者和用户提供了一个愉快的体验。在这篇文章中，我将展示 CMS 的一些可扩展性，我们也将尝试一个简单的插件来扩展另一个插件的功能。

![OctoberCMS LOGO](img/d7e82870324d32503e592b67420b607a.png)

## 介绍

每个 CMS 都有一个扩展平台功能的插件系统，我们*通过深入 CMS 的内部工作来测量*可扩展性。然而，我们不仅在这里谈论 CMS，我们还在谈论插件！

如果你开发一个插件，你需要确保其他开发者可以修改你的功能。例如，我们有一个博客插件，用户可以通过在列表中选择来发布文章。这将是一个好主意，火一个事件，说一个新的职位已经发表，另一个开发人员可能会钩到这个事件，并通过电子邮件通知订阅用户！

```
class Posts extends Controller
{
    public function index_onPublish()
    {
        if (($checkedIds = post('checked')) && is_array($checkedIds) && count($checkedIds)) {

            foreach ($checkedIds as $postId) {
                if ((!$post = Post::find($postId)) || !$post->canEdit($this->user))
                    continue;

                $post->publish();
                Event::fire('rainlab.blog.posts.published', [$post]);
            }

            Flash::success('Successfully published those posts.');
        }

        return $this->listRefresh();
    }
} 
```

另一个开发人员可以监听这个事件，对发布的帖子做一些事情。

```
Event::listen('rainlab.blog.posts.published', function($post) {
    User::subscribedTo($post).each(function($user) use($post) {
        Mail::send('emails.notifications.post-published', ['user' => $user, 'post' => $post], function($message) use($user, $post) {
            $message->from('us@example.com', 'New post by ' . $user->name);

            $message->to($user->email);
        });
    });
}); 
```

我们将主要使用事件来挂钩请求周期的不同部分。让我们从一个具体的例子开始，把事情弄清楚。

## Rainlab 博客插件

如果你使用 OctoberCMS 有一段时间了，你一定知道 Rainlab 博客插件。它允许你在后端添加文章并将它们附加到类别中，你可以使用组件在前端显示它们。

在文章列表页面上，我们可以删除文章。但是，如果我们想软删除它们呢？让我们看看我们是否能做到这一点，并了解更多关于 OctoberCMS 的可扩展性。

## 创建新插件

继续使用 scaffolding helper 命令为我们的演示创建一个新插件，并更新`Plugin.php`文件中的插件细节。

```
php artisan create:plugin rafie.blogplus 
```

## 扩展数据库模式

当谈到软删除时，首先想到的是需要出现在数据库中的`deleted_at`字段列。

在`blogplus/updates`文件夹下创建一个名为`create_posts_deleted_at_field.php`的新文件，并更新`version.yaml`文件。

```
// updates/version.yaml

1.0.1:
    - First version of blogplus.
    - create_posts_deleted_at_field.php 
```

```
// updates/create_posts_deleted_at_field.php

class CreatePostsDeletedAtField extends Migration
{

    public function up()
    {
        Schema::table('rainlab_blog_posts', function ($table) {
            $table->timestamp('deleted_at')
                  ->default(null)
                  ->nullable();
        });
    }

    public function down()
    {
        Schema::table('rainlab_blog_posts', function ($table) {
            $table->dropColumn('deleted_at');
        });
    }
} 
```

迁移类将改变`rainlab_blog_posts`表，并添加带有缺省 null 值的`deleted_at`列。不要忘记运行`php artisan plugin:refresh rafie.blogplus`命令以使更改生效。

## 扩展帖子列表

接下来，我们必须将我们的字段添加为要在列表中显示的列。OctoberCMS 为我们提供了一个事件来连接和改变当前显示的小部件(后端列表被认为是一个小部件)。

```
Event::listen('backend.list.extendColumns', function ($widget) {
    // Only for the Posts controller
    if (( ! $widget->getController() instanceof \Rainlab\Blog\Controllers\Posts)) {
        return;
    }

    $widget->addColumns([
                            'deleted_at' => [
                                'label' => 'Deleted',
                                'type'  => 'date'
                            ]
                        ]);
}); 
```

**注意**:上面的代码应该放在`Plugin@boot`方法里面。

我们有一个 if 语句来防止我们的代码在每个页面上执行，然后我们向列表小部件添加一个新列，我们还可以使用`removeColumn`方法删除任何现有的列。查看文档，获取可用色谱柱选项的[列表。](https://octobercms.com/docs/backend/lists#column-options)

![Posts list](img/83fa50de982201986336764bf5dc9683.png)

## 扩展过滤器

文章列表顶部的栏允许用户使用日期、类别等来过滤列表。在我们的例子中，我们需要一个过滤器来显示/隐藏垃圾帖子。

```
// plugin.php

Event::listen('backend.filter.extendScopes', function ($widget) {
    // Only for the Posts controller
    if (( ! $widget->getController() instanceof \Rainlab\Blog\Controllers\Posts)) {
        return;
    }

    $widget->addScopes([
                           'Trashed' => [
                               'label'      => 'Hide trashed',
                               'type'       => 'checkbox',
                               'scope'      => 'trashed'
                           ],
                       ]);
}); 
```

你可以在[文档](http://octobercms.com/docs/backend/lists#list-filters)中阅读更多关于列表过滤器的信息。上面的代码相当简单，只包含几个选项。然而，`scope`属性应该是在相关(`Models\Post`)模型实例中定义的[查询范围](https://laravel.com/docs/5.2/eloquent#query-scopes)方法的名称。

### 可扩展的类

特征提供了一种神奇的 T2 方式，通过添加新的方法、属性、行为等来动态扩展现有的类。在我们的示例中，我们需要向 posts 模型添加一个新方法来处理我们的范围过滤器。

```
// plugin.php

\Rainlab\Blog\Models\Post::extend(function ($model) {
    $model->addDynamicMethod('scopeTrashed', function ($query) {
        return $query->where('deleted_at', '=', null);
    });
}); 
```

我们可以用`addDynamicProperty`、`asExtension`等做同样的事情。让我们刷新帖子列表，看看我们的更改是否有效。

![Show trashed](img/57bac6bcaff90b6eacd8d675ca828ec3.png)


当然，我们还没有任何垃圾帖子，因为我们需要完成最后一部分:拦截帖子的删除，只更新`deleted_at`列。

**提示:**您可以使用`conditions`来指定一个简单的 where 条件，而不是使用`scope`属性。下面的代码给出了与使用模型范围相同的结果。

```
$widget->addScopes([
   'Trashed' => [
       'label'      => 'Hide trashed',
       'type'       => 'checkbox',
       'conditions' => 'deleted_at IS NULL',
   ],
]); 
```

## 雄辩的事件

雄辩在每个动作(创建、更新、删除等)上触发一个事件列表。在这种情况下，我们需要挂钩到删除事件并防止记录被删除。

删除记录时，在执行实际的删除操作之前触发`deleting`事件，之后触发`deleted`事件。如果在`deleting`事件上返回`false`，动作将会中止。

```
// plugin.php

Event::listen('eloquent.deleting: RainLab\Blog\Models\Post', function ($record) {
    $record->deleted_at = Carbon::now();
    $record->save();

    return false;
}); 
```

现在我们准备测试最终结果！继续删除一些记录，然后转到文章列表页面，看看是否可以切换列表中的垃圾项目。

## 结论

本文简要概述了如何扩展 OctoberCMS 平台的不同部分。你可以在文档的[扩展插件章节](http://octobercms.com/docs/plugin/extending)中了解更多。如果您有任何问题或意见，请务必在下面发布！

## 分享这篇文章