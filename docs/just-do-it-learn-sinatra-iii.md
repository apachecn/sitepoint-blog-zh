# 就这么做:学习辛纳屈，第三部分

> 原文：<https://www.sitepoint.com/just-do-it-learn-sinatra-iii/>

在本教程的第 2 部分中，我们使用 DataMapper 将任务保存到数据库后端，并创建了一个使用 Sinatra 显示、添加、删除和完成任务的 web 前端。在本教程中，我们将使它看起来更好，并通过让您创建多个任务列表来添加一些额外的功能。

## 添加一些风格

目前，我们的应用程序运行良好，但看起来有点笨重。让我们通过创建样式表来解决这个问题。检查“layout.slim”文件中是否有以下代码行:

```
link rel="stylesheet" media="screen, projection" href="/styles.css"
```

现在创建一个名为“styles.css”的文件，并将其保存在 public 文件夹中，然后添加以下几行 css:

```
.completed{
  text-decoration: line-through;
  }

.tasks{
  padding:0;
  list-style:none;
  width:400px;
  }

.task{
  position:relative;
  padding:2px 0 2px 28px;
  border-bottom: dotted 1px #ccc;
}

form.update{
  position:absolute;
  bottom:2px;
  left:0;
  }
form.update input{
  background:white;
  color:white;
  padding:0 2px;
  border:solid 1px gray;
  cursor:pointer;
}

.tasks li.completed form.update input{
  color:#47FD6B;
  }

form.delete{
  display:inline;
  }

form.delete input{
  background:none;
  cursor:pointer;
  border:none;
  }
```

重新加载页面，你会看到它看起来更好，更像一个实际的任务列表。您可能还注意到，styles 引用了一个“completed”类。目前，我们已经通过添加任务完成日期来显示任务已完成，但这看起来不太好，所以让我们更改任务视图，以便添加一个 completed 类。然后，我们可以使用样式表使完成的任务看起来不同。打开“task.slim ”,将其更改为以下内容:

```
li.task id=task.id class=(task.completed_at.nil? ? "" : "completed")
  = task.name
  form.update action="/task/#{task.id}" method="POST"
    input type="hidden" name="_method" value="PUT"
    -if task.completed_at.nil?
      input type="submit" value="  " title="Complete Task"
    -else
      input type="submit" value="✓" title="Uncomplete Task"
  form.delete action="/task/#{task.id}" method="POST"
    input type="hidden" name="_method" value="DELETE"
    input type="submit" value="&times;" title="Delete Task"
```

这里的关键行在顶部，它使用三元运算符来检查任务是否完成，如果已经完成，则添加 completed 类。CSS 确保任务在完成时被划掉。如果你现在试用这个应用程序，它会开始感觉更好、更自然——点击方框，将任务标记为完成，并获得一些视觉反馈。

## 任务列表

我们现在将添加添加多个任务列表的功能。为此，我们需要创建一个 List 类。列表将包含许多任务。DataMapper 通过使用*关联*显示列表和任务之间的关系来处理这个问题。这是通过在每个类的底部添加一行来实现的。任务模型使用`belongs_to`声明，列表模型使用`has n`声明。在后台，这将向任务类添加一个`list_id`属性，用于跟踪任务属于哪个列表，尽管我们不需要直接访问这个属性。每个类还获得了一些额外的方法，所以你可以使用`List.tasks`访问任务列表，使用`Task.list`访问任务列表。打开 main.rb，添加 List 类并修改 Task 类，如下所示:

```
class Task
  include DataMapper::Resource
  property :id,           Serial
  property :name,         String, :required => true
  property :completed_at, DateTime
  belongs_to :list
end

class List
  include DataMapper::Resource
  property :id,           Serial
  property :name,         String, :required => true
  has n, :tasks, :constraint => :destroy  
end

DataMapper.finalize
```

由于我们已经创建了一些新的模型，我们需要更新底层数据库。这可以使用 DataMapper 的`auto_migrate!`方法来完成。进入控制台并打开 irb:

```
$> irb
require './main'
DataMapper.auto_migrate!
```

请注意，这将删除之前已经存在于数据库中的所有任务。

## 添加和删除列表

我们现在需要创建一些处理程序来处理列表。它们与第 2 部分中的任务处理程序非常相似，在这里完整列出，放在 main.rb 的底部:

```
post '/new/list' do
  List.create params['list']
  redirect to('/')
end

delete '/list/:id' do
  List.get(params[:id]).destroy
  redirect to('/')
end
```

这些应该相当简单——一个处理程序根据表单中给定的参数创建一个新列表，另一个处理程序根据 url 中给定的 id 删除一个列表。我们现在需要对索引页面上的表单做一点小小的更改，以便它用于添加列表而不是任务:

```
form.new action="/new/list" method="POST"
  input type="text" name="list[name]"
  input type="submit" value="+ List"
ul.lists
  - @lists.each do |list|
    == slim :list, locals: { list: list }
```

该视图包含实例变量@lists，它表示数据库中的所有列表。这还不存在，所以我们需要更新相关的处理程序，在 main.rb 中找到以下根 url 的处理程序:

```
get '/' do
  @tasks = Task.all
  slim :index
end
```

并将其更改为以下内容:

```
get '/' do
  @lists = List.all(:order => [:name])
  slim :index
end
```

这将搜索所有列表，而不是所有任务。我们将从数据库中逐个列表地获取任务。

索引视图的最后一部分引用了一个名为“list”的视图，所以我们也需要创建它。这将是用于每个列表并显示任务的视图。创建一个新的文本文件，并将以下内容作为“list.slim”保存在 views 文件夹中:

```
li.list
  h2= list.name
  form.new action="/#{list.id}" method="POST"
    input type="text" name="task[name]"
  ul.tasks
    - list.tasks.each do |task|
      == slim :task, locals: { task: task }
  form.destroy action="/list/#{list.id}" method="POST"
    input type="hidden" name="_method" value="DELETE"
    input type="submit" value="&times;"
```

这实际上非常类似于我们最初在索引视图中的代码。它首先给出列表的名称，然后是一个用于添加任务的表单。接下来是使用第 2 部分中相同任务视图的任务列表。最后一段代码是一个表单，用于访问删除处理程序，以便可以删除列表。

## 向列表添加任务

我们现在只需要做一个小的改变来确保任务被正确地添加。因为它们属于一个列表，我们需要确保我们指定了任务被添加到哪个列表。这是通过将列表的 id 添加到创建任务的 url 来完成的。注意 list.slim 中的这一行:

```
form.new action="/#{list.id}" method="POST"
```

这指定了表单发送到的操作应该包含表单所在列表的 id。目前，我们有以下处理程序来处理添加任务:

```
post '/' do
  Task.create params['task']
  redirect to('/')
end
```

这需要更改为以下内容:

```
post '/:id' do
  List.get(params[:id]).tasks.create params['task']
  redirect to('/')
end
```

该处理程序使用 url 中指定的列表 id，然后在数据库中找到该列表，并使用表单中指定的参数创建一个属于该列表的新任务。

## 更多的风格

我们马上就要到了，剩下要做的就是为列表设计一些样式。打开 styles.css 并添加以下几行:

```
.lists{
  padding:0;
  list-style:none;
  overflow:hidden;
  }

.list{
  float: left;
  width:23%;
  margin:0 1%;
  border-top:solid 5px #ccc;
  }
```

您还需要确保从该文件中删除以下行(应该在第 8 行周围):

```
width:400px;
```

就是这样！你现在应该有一个功能齐全的待办事项应用程序，看起来也很像。最重要的是，main.rb 仍然只有不到 60 行代码！在这个系列的最后一部分，我们将看看如何使用 [Sass](http://sass-lang.com/) 添加更多的风格，并使用 [Heroku](http://www.heroku.com/) 将其部署到 web 上。

## 分享这篇文章