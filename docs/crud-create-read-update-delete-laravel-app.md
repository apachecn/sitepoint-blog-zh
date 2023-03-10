# Laravel 应用程序中的 CRUD(创建读取更新删除)

> 原文：<https://www.sitepoint.com/crud-create-read-update-delete-laravel-app/>

如果你想了解更多关于拉勒维尔的知识，请观看我们最近的拉勒维尔课程的老师艾萨克·卡斯蒂洛的[拉勒维尔现场课。](https://www.sitepoint.com/premium/screencasts/live-lesson-on-laravel-with-isaac-castillo)

在前一部分中，我们通过创建数据库、一些控制器、基本路线和简单视图来引导我们的 Laravel CRUD 应用程序。在这一部分中，我们将总结并实现适当的 CRUD。

![Laravel Logo](img/70059f7acc1081bf45f71d2e81252140.png)

如果您想通过 Laravel 的文档进行交互式浏览，请立即阅读第一部分。

## 创建记录

从我们离开的地方继续，让我们创建一个页面，在这里我们将实际执行这个操作。在我们的`TasksController`中，让我们返回这样一个视图:

```
public function create()
{
    return view('tasks.create');
}
```

现在，在我们的视图目录中，让我们创建`tasks/create.blade.php`，并输入一些起始内容:

```
@extends('layouts.master')

@section('content')

<h1>Add a New Task</h1>
<p class="lead">Add to your task list below.</p>
<hr>

@stop
```

此时，我们可以手动创建一个表单，但是 Laravel 提供了一个包来减轻我们的负担—[Illuminate/Html](https://github.com/illuminate/html)。让我们通过运行以下命令来快速获取这些信息:

```
composer require illuminate/html
```

现在，在我们的`config/app.php`文件中，让我们将服务提供商添加到列表中:

```
'Illuminate\Html\HtmlServiceProvider',
```

让我们添加别名:

```
'Form'      => 'Illuminate\Html\FormFacade',
'Html'      => 'Illuminate\Html\HtmlFacade',
```

我们现在可以很容易地在我们的`create.blade.php`文件中创建一个表单。让我们继续使用表单外观和刀片语法来完成这项工作:

```
{!! Form::open([
    'route' => 'tasks.store'
]) !!}

<div class="form-group">
    {!! Form::label('title', 'Title:', ['class' => 'control-label']) !!}
    {!! Form::text('title', null, ['class' => 'form-control']) !!}
</div>

<div class="form-group">
    {!! Form::label('description', 'Description:', ['class' => 'control-label']) !!}
    {!! Form::textarea('description', null, ['class' => 'form-control']) !!}
</div>

{!! Form::submit('Create New Task', ['class' => 'btn btn-primary']) !!}

{!! Form::close() !!}
```

看看我们目前的`create`视图截图。

![Create view default](img/fa221d52fb5803a1005d2e33b21ba254.png)

这里要注意的一件重要的事情是，我已经根据我们丰富的路线列表指定了我们将发布到的路线。我们将使用 store 方法来处理数据，所以让我们回到我们的`TasksController`，并开始处理这些数据。

让我们把头转向拉韦尔的[请求](https://laravel.com/docs/5.0/requests)。

> 请求外观将允许您访问绑定在容器中的当前请求。

根据文档，我们可以通过两种方式获得请求实例。让我们坚持依赖注入方法。我们的`store`方法现在应该是这样的:

```
public function store(Request $request)
{
    //
}
```

现在，我们可以转储信息，看看发布了什么。我们将使用助手函数`dd()`，它默认包含在 Laravel 中。它结合了 Symphony 的 VarDupmer 组件(T4)和 PHP 函数(T1)。将以下内容添加到`store`方法中:

```
dd($request->all());
```

现在提交空表单，您将看到数据。返回并在表单中填写一些虚拟数据，您将看到请求被更新。如果我们对任何验证都不感兴趣，那么将新任务保存到数据库是很容易的。在[雄辩](https://laravel.com/docs/5.0/eloquent)的文档中，我们会注意到我们可以调用`create`方法在我们的表中创建新行。让我们通过向我们的`store`方法添加以下内容来实现这一点。我们还会回到我们来的地方:

```
public function store(Request $request)
{
    $input = $request->all();

    Task::create($input);

    return redirect()->back();
}
```

我们现在准备创建一个新任务。让我们继续输入一些虚拟数据并提交它。啊哦…有个`MassAssignmentException`。Laravel 默认阻止批量赋值，这是一件好事。这只是意味着我们必须声明哪些字段是可批量赋值的。我建议你[仔细阅读这款](https://stackoverflow.com/questions/26724117/laravel-mass-assignment-exception-error)，但这是我们更新后的`Task`型号的样子:

```
class Task extends Model {

    /**
     * Fillable fields
     * 
     * @var array
     */
    protected $fillable = [
        'title',
        'description'
    ];

}
```

现在，让我们再次尝试添加我们的任务。如果我们成功了，我们应该被重定向**回到**我们来的地方，即“创建任务”页面。目前没有迹象表明任务是否被成功添加，但是让我们通过命令行检查数据库:

```
sqlite3 storage/database.sqlite
select * from tasks;
```

我们应该看到返回的表中有我们的新条目。太棒了。成功消息和验证呢？让我们首先验证我们的输入，以确保所有字段都是必填的。Laravel 附带了一个非常容易使用的[验证器类](https://laravel.com/docs/5.0/validation)，快速阅读应该可以让我们立刻上手。我们正在我们的控制器中进行验证，所以让我们看看[部分](https://laravel.com/docs/5.0/validation#controller-validation)。让我们通过将它添加到`store`方法的开头来验证我们的输入:

```
$this->validate($request, [
    'title' => 'required',
    'description' => 'required'
]);
```

现在请注意，如果我们忽略了任何输入，方法的其余部分就不会执行，我们仍然停留在已经输入了任何内容的页面上。Laravel 将自动抛出一个错误，我们可以在我们的刀片模板中访问它。让我们在表单上方插入以下代码片段:

```
@if($errors->any())
    <div class="alert alert-danger">
        @foreach($errors->all() as $error)
            <p>{{ $error }}</p>
        @endforeach
    </div>
@endif
```

现在，我们将看到为我们整齐地写出来的错误。

![Create task errors](img/4f680162a9664ecbb5511fb0efd38f77.png)

成功信息呢？好吧，如果我们的验证器通过，我们的代码的其余部分将执行，我们可以用一个[会话 flash 消息](https://laravel.com/docs/5.0/session#flash-data)重定向回来。更新`store`方法以创建新的 flash 消息:

```
public function store(Request $request)
{
    $this->validate($request, [
        'title' => 'required',
        'description' => 'required'
    ]);

    $input = $request->all();

    Task::create($input);

    Session::flash('flash_message', 'Task successfully added!');

    return redirect()->back();
}
```

现在，我们可以将其添加到我们的刀片模板中:

```
@if(Session::has('flash_message'))
    <div class="alert alert-success">
        {{ Session::get('flash_message') }}
    </div>
@endif
```

这是我们应该看到的。

![Create success message](img/c87f6728d5f3164537f9c8f390cc0c5d.png)

我们现在正在验证和添加任务，并将数据传递回视图进行输出。非常好。我们仍然需要一种方法来查看我们的记录。

## 阅读记录

回到我们的`index`方法，我们现在可以输出到目前为止已经创建的所有任务。将此添加到`index`方法中:

```
public function index()
{
    $tasks = Task::all();

    return view('tasks.index')->withTasks($tasks);
}
```

我们可以像这样访问和输出任务:

```
@foreach($tasks as $task)
    <h3>{{ $task->title }}</h3>
    <p>{{ $task->description}}</p>
    <p>
        <a href="{{ route('tasks.show', $task->id) }}" class="btn btn-info">View Task</a>
        <a href="{{ route('tasks.edit', $task->id) }}" class="btn btn-primary">Edit Task</a>
    </p>
    <hr>
@endforeach
```

这里有一个索引视图的截图。

![Task listing](img/3316c2b778d77ab8920a77c1b00c1906.png)

现在让我们来看看如何显示一条记录。在目前的应用程序中，这可能是不必要的，因为我们已经输出了所有的信息，但我们还是会这样做。如果我们看看我们的路线列表，很明显`tasks.show`路线是我们要走的路。它接受 URL 中的通配符，对于我们的应用程序，我们将使用任务的 ID。和以前一样，我们将创建一个`show.blade.php`文件并扩展我们的主布局:

```
@extends('layouts.master')

@section('content')

<h1>{{ $task->title }}</h1>
<p class="lead">{{ $task->description }}</p>
<hr>

<a href="{{ route('tasks.index') }}" class="btn btn-info">Back to all tasks</a>
<a href="{{ route('tasks.edit', $task->id) }}" class="btn btn-primary">Edit Task</a>

<div class="pull-right">
    <a href="#" class="btn btn-danger">Delete this task</a>
</div>

@stop
```

现在，让我们更新我们的`show`方法:

```
public function show($id)
{
    return view('tasks.show');
}
```

如果我们导航到一个带有随机通配符-`/tasks/320918`-的 URL，我们应该会看到我们的虚拟模板。让我们实际获取正确的任务。使用 concertive，我们可以搜索具有匹配 ID 的记录，如果没有找到，我们将抛出一个可以捕获的`ModelNotFoundException`。如果找到记录，我们可以在视图中访问它。下面是更新后的方法:

```
public function show($id)
{
    $task = Task::findOrFail($id);

    return view('tasks.show')->withTask($task);
}
```

现在在我们看来，我们可以像这样正确地输出记录:

```
<h1>{{ $task->title }}</h1>
<p class="lead">{{ $task->description }}</p>
```

导航到`tasks/1`，您应该会看到输出。

![Single task view](img/9e9bcf434bdc084fdfbe174899aa6ec3.png)

回到我们的`index`视图，我们现在可以输出每个单独任务的链接:

```
<a href="{{ route('tasks.show', $task->id) }}">view</a>
```

注意，我们根据任务 ID 传入了正确的通配符。你现在应该可以很好地点击了！让我们继续编辑。

## 使用表单模型绑定更新记录

现在，你可能意识到为我们的 RESTful 应用准备一个新视图是多么容易。这次也没有什么不同，所以我们将创建`edit.blade.php`文件，拉入主布局，并将相应的控制器方法链接到它。下面是`edit`方法:

```
public function edit($id)
{
    return view('tasks.edit');
}
```

以下是与之匹配的视图:

```
@extends('layouts.master')

@section('content')

<h1>Edit Task - Task Name </h1>
<p class="lead">Edit this task below. <a href="{{ route('tasks.index') }}">Go back to all tasks.</a></p>
<hr>

@stop
```

如果您查看路由列表，您会注意到编辑路由也接受通配符。我们会保持一致，使用 ID。导航到`/tasks/gibberish/edit`将显示虚拟页面，但是让我们引入正确的内容。首先，我们可以更新`index`和`show`视图上的所有“编辑”链接，如下所示:

```
<a href="{{ route('tasks.edit', $task->id) }}">edit</a>
```

再次注意我们是如何调用正确的路由并传入相应的通配符的。在我们的编辑模板中，我们想要一个与我们用来创建任务的表单相似的表单，但是如果表单已经填充了现有的字段，这将会非常有用。多亏了 Laravel 的表单模型绑定，这简直是小菜一碟。我们将复制 create 表单，尽管更好的做法是将其提取到某种形式的片段中。无论如何，我们将复制它，并将我们的模型绑定到它:

```
{!! Form::model($task, [
    'method' => 'PATCH',
    'route' => ['tasks.update', $task->id]
]) !!}

<div class="form-group">
    {!! Form::label('title', 'Title:', ['class' => 'control-label']) !!}
    {!! Form::text('title', null, ['class' => 'form-control']) !!}
</div>

<div class="form-group">
    {!! Form::label('description', 'Description:', ['class' => 'control-label']) !!}
    {!! Form::textarea('description', null, ['class' => 'form-control']) !!}
</div>

{!! Form::submit('Update Task', ['class' => 'btn btn-primary']) !!}

{!! Form::close() !!}
```

注意我们如何在表单中使用补丁请求来保持与 RESTful 资源一致。还要注意我们如何调用变量`$task`，将它绑定到模型，并引用将用于查找表的 ID。这意味着我们必须传递正确的任务。在`TasksController`中，我们可以将`edit`方法更新为:

```
public function edit($id)
{
    $task = Task::findOrFail($id);

    return view('tasks.edit')->withTask($task);
}
```

就像以前一样，如果没有找到 ID，我们将得到`ModelNotFoundException`。此时，我们可以再次复制我们的错误片段，但这一点也不枯燥。不要担心，我们可以通过利用偏导数很容易地解决这个问题。Blade 允许我们通过使用`@include()`指令来引用任何文件。首先，让我们在视图目录中创建一个名为 partials 的文件夹。在那里，我将创建一个名为`alerts`的子目录，然后创建一个名为`errors.blade.php`的文件。让我们将错误片段复制到这个新文件中:

```
@if($errors->any())
    <div class="alert alert-danger">
        @foreach($errors->all() as $error)
            <p>{{ $error }}</p>
        @endforeach
    </div>
@endif
```

现在，我们可以在任何文件中引用它，如下所示:

```
@include('partials.alerts.errors')
```

我们现在可以用这个部分引用替换我们的`create.blade.php`模板中的原始代码片段，并在我们的编辑模板中引用它。现在，整个编辑视图应该如下所示:

```
@extends('layouts.master')

@section('content')

<h1>Editing "{{ $task->title }}"</h1>
<p class="lead">Edit and save this task below, or <a href="{{ route('tasks.index') }}">go back to all tasks.</a></p>
<hr>

@include('partials.alerts.errors')

@if(Session::has('flash_message'))
    <div class="alert alert-success">
        {{ Session::get('flash_message') }}
    </div>
@endif

{!! Form::model($task, [
    'method' => 'PATCH',
    'route' => ['tasks.update', $task->id]
]) !!}

<div class="form-group">
    {!! Form::label('title', 'Title:', ['class' => 'control-label']) !!}
    {!! Form::text('title', null, ['class' => 'form-control']) !!}
</div>

<div class="form-group">
    {!! Form::label('description', 'Description:', ['class' => 'control-label']) !!}
    {!! Form::textarea('description', null, ['class' => 'form-control']) !!}
</div>

{!! Form::submit('Update Task', ['class' => 'btn btn-primary']) !!}

{!! Form::close() !!}

@stop
```

这是我们编辑任务时的视图截图。

![Editing a task](img/1a9a35d411620c2b9a3ca4d0d196ee59.png)

现在让我们进入我们的`update`方法，它将从表单提交中接收数据，并尝试更新我们的记录。像以前一样，我们将验证我们的输入，并在视图中记录错误(如果有的话)。如果验证通过，我们将获取输入，更新任务，保存它，并返回一个成功消息。这个方法看起来是这样的:

```
public function update($id, Request $request)
{
    $task = Task::findOrFail($id);

    $this->validate($request, [
        'title' => 'required',
        'description' => 'required'
    ]);

    $input = $request->all();

    $task->fill($input)->save();

    Session::flash('flash_message', 'Task successfully added!');

    return redirect()->back();
}
```

自己试试看，很管用！您现在可以创建、读取和更新记录，但是让我们跨越最后一个障碍。

## 删除记录

RESTfully 删除记录实际上需要一个删除请求。您可以使用 JavaScript 在控制器之外解决这个问题，但这超出了本文的范围。如果我们查看单个任务，您会注意到我在那里留了一个占位符按钮来删除它。我们实际上需要将它变成一个向`destroy`方法发送删除请求的表单，并在那里处理记录删除。这是我们更新的包含删除表单的展示模板:

```
@extends('layouts.master')

@section('content')

<h1>{{ $task->title }}</h1>
<p class="lead">{{ $task->description }}</p>
<hr>

<div class="row">
    <div class="col-md-6">
        <a href="{{ route('tasks.index') }}" class="btn btn-info">Back to all tasks</a>
        <a href="{{ route('tasks.edit', $task->id) }}" class="btn btn-primary">Edit Task</a>
    </div>
    <div class="col-md-6 text-right">
        {!! Form::open([
            'method' => 'DELETE',
            'route' => ['tasks.destroy', $task->id]
        ]) !!}
            {!! Form::submit('Delete this task?', ['class' => 'btn btn-danger']) !!}
        {!! Form::close() !!}
    </div>
</div>

@stop
```

在我们的`TaskController`中，我们可以在`destroy`方法中处理请求，完善我们的 RESTful 控制器。再一次，雄辩使这变得轻而易举。我们将获取表中的相关记录，删除它，并重定向回任务列表:

```
public function destroy($id)
{
    $task = Task::findOrFail($id);

    $task->delete();

    Session::flash('flash_message', 'Task successfully deleted!');

    return redirect()->route('tasks.index');
}
```

现在，让我们将 flash 消息重构到主布局文件中，这样每当发布 flash 消息时，它就会显示在每个模板上。我们可以将它从创建和编辑模板中删除，并仅将其保留在主布局中，如下所示:

```
<main>
    <div class="container">
        @if(Session::has('flash_message'))
            <div class="alert alert-success">
                {{ Session::get('flash_message') }}
            </div>
        @endif

        @yield('content')
    </div>
</main>
```

现在，导航到一个任务，并删除它。您将被重定向回任务列表，并显示一条提示消息，通知您任务已被成功删除。当然，你的任务列表也会随之更新。

## 包扎

在本教程中，我们已经探讨了许多核心概念，并触及了许多简洁的 Laravel 功能。通过从头开始构建这个 CRUD 应用程序，我们已经体验了 Laravel 工作流。

你应该有一个坚实的基础和理解去继续下去，并建立自己的东西。我强烈建议查看文档的各个部分，看看有什么可用的，并尝试一下。以此为基础，走出去展示你的手工精神。反馈？评论？把它们留在下面！

*通过我们的 [Laravel 现场课程](https://www.sitepoint.com/premium/screencasts/live-lesson-on-laravel-with-isaac-castillo)继续了解 Laravel。一个老师，一节课，然后是你的问题。*

## 分享这篇文章