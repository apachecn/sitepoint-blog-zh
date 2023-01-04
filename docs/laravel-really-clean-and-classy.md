# 拉勒维尔:它真的干净优雅吗？

> 原文：<https://www.sitepoint.com/laravel-really-clean-and-classy/>

> Laravel 是一个用于 PHP web 开发的干净优雅的框架。Laravel 让您从杂乱无章的代码中解放出来，帮助您使用简单、富于表现力的语法创建精彩的应用程序。发展应该是你享受的创造性体验，而不是痛苦的事情。享受新鲜空气。

这是可以在 Laravel 主页上找到的文字，如果我们相信它，那不是很好吗？让我们通过构建一个简单的 TODO 应用程序来测试这种说法，并看看我们需要为此付出多少努力。

## 迁移

首先，我们将设计数据库。该应用程序需要一个相当简单的模式，一个包含 5 列的表来存储 ID、标题、描述和任务创建和上次更新时的时间戳。

Laravel 有一个名为 migrations 的特性，如果你使用过 Ruby on Rails 之类的其他框架，你可能已经很熟悉了。迁移是用于更新数据库的文件。在执行它们时，它们会以一种您可以轻松地对其应用更新的方式更改您的数据库模式。

对于我们的应用程序，迁移如下所示:

```
<?php
class Create_Todo_Table
{
public function up() {
Schema::table("todos", function($table) {
$table->create();
$table->increments("id");
$table->string("title", 20);
$table->text("description");
$table->timestamps();
});
}

public function down() {
Schema::drop("todos");
}
}
```

执行迁移时调用`up()`方法，恢复迁移时调用`down()`方法。

## 模型

由于 Laravel 是一个 MVC 框架，我们需要创建模型。与您可能知道的任何其他 MVC 框架一样，模型是负责与数据库通信的组件。我们的表格很简单，因此它只需要一个简单的模型:

```
<?php
class Todo extends Eloquent
{
public static $timestamps = true;
}
```

类名是“Todo ”, Laravel 会自动将它与数据库中的`todos`表关联起来。雄辩是 Laravel 使用的模型的 ORM 类，它提供了一种处理数据库对象的平滑方法。

`$timestamps`属性被设置为 true，这意味着每当创建一个新条目或更新一个现有条目时，`created_at`和`updated_at`字段将被相应地更新。

## 控制器

现在让我们创建控制器。控制器是所有业务逻辑所在的位置，因此应该包含以下功能:

*   检索表中的所有条目并列出它们
*   检索具有给定 id 的特定条目的信息
*   删除具有给定 id 的条目
*   撰写表单以添加新条目
*   将新条目添加到数据库中，并编写一条消息来确认添加

这给了我们下面的控制器类五个方法，我们称之为行动。动作的声明必须以前缀“action_”开头。

```
<?php
class Todo_Controller extends Base_Controller
{
public function action_list() {
$todos = Todo::all();
return View::make("list")
->with("todos", $todos);
}

public function action_view($id) {
$todo = Todo::where_id($id)->first();
return View::make("view")
->with("todo", $todo);
}

public function action_delete($id) {
$todo = Todo::where_id($id)->first();
$todo->delete();
return View::make("deleted");
}

public function action_new() {
return View::make("add");
}

public function action_add() {
$todo = new Todo();
$todo->title = Input::get("title");
$todo->description = Input::get("description");
$todo->save();
return View::make("success");
}
}
```

代码非常简单。第一个方法`action_list()`将获得数据库中的所有条目。这就是雄辩使事情变得容易的地方，只需要 Todo::all()来获取它们。然后，它返回一个视图，其中包含数据库中绑定到`$todos`变量的所有条目。

其他方法也很容易阅读。它们主要用于操作数据库对象，并返回绑定了一些数据的视图。最后一个方法可能是一个例外；当提交表单以添加新的 TODO 条目时，将调用`action_add()`。`Input::get()`用于检索提交的表单值。

## 景色

现在我们来到了风景。Laravel 使用自己的 Blade 模板引擎，为我们提供了清晰易读的代码。我将给出第一个视图的例子，列表。通过`action_list()`返回视图下的`make` / `with`语句，我们将`Todo::all()`的所有结果放入`$todos`。我们现在可以在视图中使用它:

```
<h2>Todo list</h2>
<p>{{ HTML::link_to_action("todo@new", "Add new todo") }}</p>
<ul>
@foreach($todos as $todo)
<li>{{ HTML::link_to_action("todo@view", $todo->title, array($todo->id)) }} - {{ HTML::link_to_action("todo@delete", "Delete", array($todo->id)) }}</li>
@endforeach
</ul>
```

这就够了！首先，我们让 Blade 创建一个到控制器动作`action_new()`的链接。然后我们看到了`foreach`语句，它非常类似于 PHP 自带的语句。对于每个任务，我们都创建一个链接来查看条目。链接文本是第二个参数`$todo->title`，接下来应该提供动作的任何参数。我们需要我们想要查看的条目的 ID，这由`$todo->id`提供。我们还创建了一个删除任务的链接，同样包括 TODO 的 ID 作为参数。

## 结论

就是这样，使用 Laravel 制作一个应用程序确实很简单。代码简单易懂。我希望这对您有用，您可以考虑在下一个 PHP 应用程序中使用 Laravel。下次见！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章