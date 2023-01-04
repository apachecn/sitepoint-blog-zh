# 动手吧:学习辛纳屈，第二部分

> 原文：<https://www.sitepoint.com/just-do-it-learn-sinatra-ii-2/>

在本教程的第一部分，我们设置了 Sinatra 并显示了一些页面。现在有趣的事情真正开始了——我们将使用一个数据库来存储我们的任务。

在本教程中，我们将对本地数据库使用 [SQLite](http://www.sqlite.org/) 。顾名思义，这是一个轻量级的基于文件的数据库，不需要任何配置。如果你还没有安装这个，那么请看[这一页](http://mislav.uniqpath.com/rails/install-sqlite3/)的一些简单说明。

为了与数据库交互，我将使用[数据映射器](http://datamapper.org/)。这是一个 [ORM](http://en.wikipedia.org/wiki/Object-relational_mapping) ，其工作方式类似于活动记录，但是方法和语法略有不同。

为了将数据映射器与 SQLite 一起使用，需要安装以下 gem:

```
$> gem install data_mapper dm-sqlite-adapter sqlite3
```

接下来，我们需要确保在 main.rb 文件的顶部需要 DataMapper gem:

```
require 'sinatra'
require 'data_mapper'
```

现在我们必须设置数据库连接，这只是一行代码，告诉 DataMapper 我们将使用名为“development.db”的 SQLite 数据库，它将保存在与应用程序相同的文件夹中:

```
DataMapper.setup(:default, ENV['DATABASE_URL'] || "sqlite3://#{Dir.pwd}/development.db")
```

## 任务模型

为了将任务保存到数据库中，我们需要创建一个任务类。以下代码位于 main.rb 中数据库连接的下方:

```
class Task
  include DataMapper::Resource
  property :id,           Serial
  property :name,         String, :required => true
  property :completed_at, DateTime
end
DataMapper.finalize
```

Task 类通过行`include DataMapper::Resource`链接到 DataMapper，行`include DataMapper::Resource`将 DataMapper 资源模块作为一个 mixin 包含在内——这就是你创建任何 Ruby 类 DataMapper 资源的方式。在这一行之后，是任务类的属性。属性的类型是`Serial`，它给每个任务一个自动递增的标识符。之后，我们的任务只需要另外两个属性——任务的名称(注意，我已经将这个属性设为必填属性)和完成日期。“DataMapper.finalize”方法用于检查模型的完整性。应该在创建了所有模型之后，应用程序开始与它们交互之前调用它。目前我们只有一个模型，所以它可以放在类定义的末尾。

## 添加任务

为了让我们开始，我们将使用 IRB 创建几个任务。打开命令行提示符，键入以下内容以访问该应用程序:

```
$> irb
ruby-1.9.2-p180 :002 > require './main'
DataObjects::URI.new with arguments is deprecated, use a Hash of URI components (/home/daz/.rvm/gems/ruby-1.9.2-p180/gems/dm-do-adapter-1.1.0/lib/dm-do-adapter/adapter.rb:231:in `new')
=> true
```

当您需要该文件时，可以忽略出现的警告。这是由于 DataMapper 中的一个错误，该错误将在下一版本中修复。别担心，这对应用程序没有任何影响。

在添加任何任务之前，我们需要通过迁移模型来设置 tasks 表。这是使用以下命令完成的:

```
ruby-1.9.2-p180 :003 > Task.auto_migrate!
=> true
```

现在我们准备添加一些任务。这可以在 irb 中使用`create`方法完成。以下是添加一些任务的几个示例:

```
ruby-1.9.2-p180 :004 > Task.create(name: "get the milk")
=> #
ruby-1.9.2-p180 :005 > Task.create(name: "order books") => #
ruby-1.9.2-p180 :006 > Task.create(name: "pick up dry cleaning")
=> #
ruby-1.9.2-p180 :007 > Task.create(name: "phone plumber")
=> #
```

这些任务现在已经保存到数据库中。如果我们想看到它们，我们只需要对 main.rb 中处理根 url 的处理程序做一些小的修改:

```
get '/' do
  @tasks = Task.all
  slim :index
end
```

`Task.all`从数据库中获取所有任务，并将它们作为数组存储在实例变量`@tasks`中。回想一下，实例变量对所有视图都可用，所以我在 index.slim 视图中使用它来遍历每个任务，并将它们显示为一个列表:

```
form action="/" method="POST"
  input type="text" name="task[name]"
  input.button type="submit" value="New Task >>"

h2 My Tasks

ul.tasks
  - @tasks.each do |task|
    li.task= task.name
```

在命令提示符下键入`$> ruby main.rb`启动服务器，重新加载页面，您应该会看到一个漂亮的任务列表。这一切都很好，但我们希望能够从网页上添加任务。这不是问题，事实上，我们已经有了一个表单，我们只需要将它插入到 DataMapper 中，以便将任务保存到数据库中。

在本教程的第一部分中，我们有以下处理程序，用于在提交表单时处理 POST 请求:

```
post '/' do
  @task =  params[:task]
  slim :task
end
```

这只需要对以下内容稍作修改:

```
post '/' do
  Task.create  params[:task]
  redirect to('/')
end
```

这将使用表单中提交的 params 散列中存储的信息创建一个新任务(在本例中，它只是任务的名称)。然后它重定向回主页(这次使用 GET 请求)，这应该显示包括新任务在内的任务列表。在您的浏览器中尝试一下。

## 删除任务

到目前为止，一切顺利，我们可以将任务添加到我们的列表中，但如何删除它们呢？为此，我们需要为每个任务添加一个删除按钮，该按钮将向服务器发送删除请求。当我这样做时，我将把任务的视图代码分离到它自己的单独文件中。将以下代码放入 views 文件夹中另存为“task.slim”的文件中:

```
li.task id=task.id
  = task.name
  form.delete action="/task/#{task.id}" method="POST"
    input type="hidden" name="_method" value="DELETE"
    input type="submit" value="&times;"  title="Delete Task"
```

现在将 index.slim 更改为以下内容:

```
form action="/" method="POST"
  input type="text" name="task[name]"
  input.button type="submit" value="New Task >>"

h2 My Tasks

ul.tasks
  - @tasks.each do |task|
    == slim :task, locals: { task: task }
```

这里有几点需要注意。首先，在任务视图中，我们需要为删除按钮创建一个表单。这样我们就可以包含一个带有属性`name="_method" value="DELETE"`的隐藏字段。这是因为大多数浏览器不理解 HTTP 动词 PUT 和 DELETE，只理解 GET 和 POST。为了解决这个问题，Sinatra 使用隐藏的表单字段来“伪装”浏览器并正确处理请求。这意味着当提交删除表单时，它将被作为删除请求处理。

在索引视图中，请注意我是如何使用以下代码行引用任务视图的:

```
== slim :task, locals: { task: task }
```

这就是 Sinatra 处理偏音的方式——你只需调用一个内嵌模板。在这种情况下，我需要告诉它，我在 task.slim 文件中对 task 的引用与传递给块的任务相同。

剩下要做的就是编写一个删除任务的处理程序。您会注意到 url 在表单的 action 属性中以`/task/#{task.id}`的形式给出。相应的处理程序如下所示:

```
delete '/task/:id' do
  Task.get(params[:id]).destroy
  redirect to('/')
end
```

这段代码通过使用 url 中的 id 找到被点击的任务，然后使用 destroy 方法将其从数据库中删除。然后它会重定向到根页面，在那里会列出剩余的任务。

## 完成任务

最后，我们只需要能够完成任务。还记得当我们创建任务类时，我们指定了一个`completed_at`属性，它告诉我们任务完成的日期。如果属性设置为`nil`，那么我们可以假设任务还没有完成。打开 task.slim 并将其更改为以下内容:

```
li.task
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

我们现在添加了一行来表示任务何时完成(如果已经完成),以及另一个包含隐藏表单字段的表单，该字段指定这是一个 PUT 请求。这是因为我们正在修改任务的属性(实际上，应该使用补丁请求，但直到 Sinatra 1.3 才支持)。根据任务是否已经完成，我们还会显示不同的按钮。如果任务已经完成，那么它会显示一个复选标记，如果它还没有完成，那么它看起来就像一个空的复选框，所以有一些视觉反馈来显示任务的状态。

请注意，action 属性中给出的 url 是完全相同的——这是因为 Sinatra 将对使用的任何 HTTP 动词做出不同的反应。我们现在需要编写一个处理程序来处理这个请求，所以将它添加到 main.rb 的底部:

```
put '/task/:id' do
  task = Task.get params[:id]
  task.completed_at = task.completed_at.nil? ? Time.now : nil
  task.save
  redirect to('/')
end
```

这将根据 url 中的 id 查找任务，然后将 completed_at 属性设置为当前时间(如果尚未设置),如果已经设置，则将其还原为 nil。换句话说，这个按钮将作为一个开关，在正在完成的任务和没有完成的任务之间切换。

重启服务器，尝试添加、删除和完成任务。我们现在有了一个功能齐全的待办事项列表——全部用了不到 40 行代码！它有点粗糙，看起来不是最好的，但是我们将在第 3 部分中解决这个问题并添加多个列表！

## 分享这篇文章