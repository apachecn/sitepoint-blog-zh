# 康康康:铁路授权之舞

> 原文：<https://www.sitepoint.com/cancancan-rails-authorization-dance/>

![Cabaret dancer vector silhouettes set](img/c51ed46d53eceedd6ea29860f43973e9.png)

最近，我写了一篇关于 Rails 的一些流行的[认证解决方案的概述。然而，在许多情况下，仅仅有身份验证是不够的——您可能需要一种授权机制来为不同的用户定义访问规则。有没有现成的解决方案，最好不是很复杂，但仍然灵活？](https://www.sitepoint.com/series/authentication-in-rails/)

认识一下 [CanCanCan](https://github.com/CanCanCommunity/cancancan) ，一个灵活的 Rails 授权解决方案。这个项目最初是由瑞恩·贝茨创作的[康康舞](https://github.com/ryanb/cancan)，他是 [RailsCasts](http://railscasts.com) 的创作者。然而，几年前这个项目变得不活跃，所以社区成员决定创建康康康，作为最初解决方案的延续。

在本文中，我将把 CanCanCan 集成到一个简单的演示项目中，定义访问规则，查看可能的选项，并讨论 CanCanCan 如何减少代码重复。读完这篇文章后，你会对康康康的基本特性有很深的理解，并准备在实际项目中运用它。

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Authorization_with_CanCanCan) 上找到。

sitepoint-cancan.herokuapp.com 有一个工作演示。

## 准备操场

### 规划和奠定基础

要开始在康康康上进行黑客攻击，我们必须首先为我们的实验准备一个操场。我要把我的应用程序叫做 iCan，因为我可以(嘻嘻！):

```
$ rails new iCan -T
```

我将坚持使用 Rails 4.1，但 CanCanCan 也与 Rails 3 兼容。

演示应用程序将向用户展示一个项目列表，包括正在进行的和已经完成的项目。不同角色的用户将具有不同的访问级别:

*   **来宾**将无法访问任何项目。他们只能看到网站的主页。
*   **用户**将只能看到正在进行的项目。他们将无法修改或删除任何内容。
*   **版主**将有权访问所有项目，并能够编辑正在进行的项目。
*   **管理员**将拥有完全访问权限。

我们的任务是介绍这些角色，并为它们定义适当的访问规则。

我更喜欢从 Bootstrap 开始设计应用程序:

*Gemfile*

```
[...]
gem 'bootstrap-sass'
[...]
```

奔跑

```
$ bundle install
```

设置根路由:

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...]
```

创建控制器:

*pages_controller.rb*

```
class PagesController < ApplicationController
  def index
  end
end
```

那景色呢

*views/pages/index . html . erb*

```
<div class="page-header"><h1>Welcome!</h1></div>
```

修改布局以利用 Bootstrap 的样式:

*视图/布局/应用程序. html.erb*

```
[...]
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'iCan', root_path, class: 'navbar-brand' %>
    </div>
    <div id="navbar">
      <ul class="nav navbar-nav">
      </ul>
    </div>
  </div>
</nav>

<div class="container">
  <% flash.each do |key, value| %>
    <div class="alert alert-<%= key %>">
      <%= value %>
    </div>
  <% end %>
  <%= yield %>
</div>
[...]
```

### 伪造认证

好了，我们已经简要讨论了要添加的角色及其访问级别，但首先我们需要介绍某种身份验证。康康康并不在乎你使用什么认证系统。它只要求返回用户记录的`current_user`方法或`nil`存在。

最近，我写了一个关于 Rails 中的认证的[系列文章，所以可以随意选择其中描述的一个解决方案。然而，对于这个演示，我不会使用真正的身份验证来简化事情，而是只关注授权。相反，我要做的是引入一个基本的`User`类和一些简单的方法:](https://www.sitepoint.com/series/authentication-in-rails/)

*型号/用户. rb*

```
class User
  ROLES = {0 => :guest, 1 => :user, 2 => :moderator, 3 => :admin}

  attr_reader :role

  def initialize(role_id = 0)
    @role = ROLES.has_key?(role_id.to_i) ? ROLES[role_id.to_i] : ROLES[0]
  end

  def role?(role_name)
    role == role_name
  end
end
```

基本上，有一个包含所有可用角色的字典。初始化时，根据提供的 ID 为用户分配一个角色
。`role?`只是我们稍后将使用的一种常规方法。

现在让我们定义控制器的动作来设置角色:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def update
    id = params[:id].to_i
    session[:id] = User::ROLES.has_key?(id) ? id : 0
    flash[:success] = "Your new role #{User::ROLES[id]} was set!"
    redirect_to root_path
  end
end
```

设置路线:

*config/routes.rb*

```
[...]
resources :sessions, only: [:update]
[...]
```

添加链接以选择角色:

*视图/布局/应用程序. html.erb*

```
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'iCan', root_path, class: 'navbar-brand' %>
    </div>
    <div id="navbar">
      <ul class="nav navbar-nav">
      </ul>

      <ul class="nav navbar-nav pull-right">
        <li class="dropdown">
          <a class="dropdown-toggle" aria-expanded="false" role="button" data-toggle="dropdown" href="#">
            Role
            <span class="caret"></span>
          </a>
          <ul class="dropdown-menu" role="menu">
            <% User::ROLES.each do |k, v| %>
              <li>
                <%= link_to session_path(k), method: :patch do %>
                  <%= v %>
                  <% if v == current_user.role %>
                    <small class="text-muted">(current)</small>
                  <% end %>
                <% end %>
              </li>
            <% end %>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

我在这里依赖于 Bootstrap 的 dropdown 小部件，所以包括它:

*application.js*

```
[...]
//= require bootstrap/dropdown
[...]
```

此外，如果您使用 Turbolinks，包含`jquery-turbolinks`以恢复默认的 jQuery 事件:

*Gemfile*

```
[...]
gem 'jquery-turbolinks'
[...]
```

*application.js*

```
[...]
//= require jquery.turbolinks
[...]
```

最后，介绍一下`current_user`方法:

*application _ controller . Rb*

```
[...]
private

def current_user
  User.new(session[:id])
end

helper_method :current_user
[...]
```

太好了！启动服务器并检查角色是否正确切换。

### 添加项目

最后要做的是添加`Project`模型和对应的控制器。每个项目现在只有一个标题:

```
$ rails g model Project title:string
$ rake db:migrate
```

控制器:

*projects_controller.rb*

```
class ProjectsController < ApplicationController
  def index
    @projects = Project.all
  end
end
```

路线:

*config/routes.rb*

```
[...]
resources :projects
[...]
```

和观点:

*views/projects/index . html . erb*

```
<div class="page-header"><h1>Projects</h1></div>

<% @projects.each do |project| %>
  <div class="well well-sm">
    <h2><%= project.title %></h2>
  </div>
<% end %>
```

我们还利用 *seeds.rb* 将一些演示记录添加到数据库中:

*db/seeds.rb*

```
20.times {|i| Project.create!({title: "Project #{i + 1}"}) }
```

奔跑

```
$ rake db:seed
```

来填充您的`projects`表。

现在操场准备好了，我们可以打开音乐跳康康舞了。

## 整合康康康和定义能力

把康康康放进你的 Gemfile

*Gemfile*

```
[...]
gem 'cancancan', '~> 1.10'
[...]
```

然后跑

```
$ bundle install
```

现在我们必须生成 *ability.rb* 文件，它将存放我们所有的访问规则:

```
$ rails g cancan:ability
```

打开此文件:

*型号/能力. rb*

```
class Ability
  include CanCan::Ability

  def initialize(user)
  end
end
```

你所有的访问规则(都是属于我们的…抱歉)应该放入`initialize`方法。有一些注释掉的例子可以帮助你开始。

`user`参数包含您的`current_user`。在幕后,`Ability`正以如下方式被实例化:

```
def current_ability
  @current_ability ||= Ability.new(current_user)
end
```

例如，如果您不想将这个方法命名为`current_user`，或者您想为`Ability`类使用另一个名称，您可以简单地在`ApplicationController`中覆盖`current_ability`方法。

重命名`current_user`的另一个选择是引入一个别名方法:

```
alias_method :current_user, :my_own_current_user
```

这样`current_ability`就不用重新定义了。点击阅读更多[。](https://github.com/CanCanCommunity/cancancan/wiki/Changing-Defaults)

在我们的例子中,`current_user`总是返回一个用户对象。在真实的身份验证场景中，如果用户没有登录，它可能会返回`nil`。因此，添加所谓的“零保护”是一个好主意:

*型号/能力. rb*

```
[...]
def initialize(user)
  user ||= User.new
end
[...]
```

现在，介绍第一个访问规则，即管理员在任何地方都有完全访问权限:

*型号/能力. rb*

```
[...]
def initialize(user)
  user ||= User.new

  if user.role?(:admin)
    can :manage, :all
  end
end
[...]
```

`can`是定义能力的方法。`:manage`的意思是“执行任何动作”，而`:all`的意思基本上是“对所有事情”。

## 检查能力

让我们在主页上显示一个指向项目列表的链接，并检查用户是否有适当的访问权限:

*views/pages/index . html . erb*

```
<div class="page-header"><h1>Welcome!</h1></div>

<% if can? :index, Project %>
  <%= link_to 'Projects', projects_path, class: 'btn btn-lg btn-primary' %>
<% end %>
```

所以`can?`是检查当前用户是否有权限执行某个动作的方法。`:index`是实际动作的名称，`Project`是执行动作的类。你也可以提供一个对象来代替一个类(我们将在后面看到一个例子)。

还有一个`cannot?`方法，您可能已经猜到了，它执行与`can?`相反的检查。在这里阅读更多。

不幸的是，没有什么可以阻止用户直接访问项目页面(比如“http://localhost:3000/projects”)。因此，我们还必须在控制器内部实施授权检查。这很简单:

*projects_controller.rb*

```
[...]
def index
  @projects = Project.all
  authorize! :index, @project
end
[...]
```

继续尝试以非管理员身份直接访问此页面。应用程序现在会引发一个错误，但这不是非常用户友好的。我们还有另一个问题要解决:如何从“拒绝访问”错误中解救出来？

### 从“拒绝访问”错误中解救出来

Rails 为我们提供了一个很好的`rescue_from`方法，我们可以从`ApplicationController`调用它:

*application _ controller . Rb*

```
[...]
rescue_from CanCan::AccessDenied do |exception|
  flash[:warning] = exception.message
  redirect_to root_path
end
[...]
```

这样，如果`CanCan::AccessDenied`在任何子控制器中被引发，错误将被正确处理。除了`message`，异常还响应`action`(如`:index`)和`subject` ( `Project`)方法。

您可以手动引发“拒绝访问”错误，并提供您自己的消息、操作和主题:

```
raise CanCan::AccessDenied.new("You are not authorized to perform this action!", :custom_action, Project)
```

试试看！点击阅读关于异常处理[的更多信息。](https://github.com/CanCanCommunity/cancancan/wiki/Exception-Handling)

## 添加更多能力

让我们添加几个其他控制器操作来创建一个新项目，并定义谁可以这样做:

*projects_controller.rb*

```
[...]
def new
  @project = Project.new
  authorize! :new, @project
end

def create
  @project = Project.new(project_params)
  if @project.save
    flash[:success] = 'Project was saved!'
    redirect_to root_path
  else
    render 'new'
  end
  authorize! :create, @project
end

private

def project_params
  params.require(:project).permit(:title)
end
[...]
```

观点:

*视图/项目/new.html.erb*

```
<div class="page-header"><h1>New Project</h1></div>

<%= render 'form' %>
```

*views/projects/_ form . html . erb*

```
<%= form_for @project do |f| %>
  <div class="form-group">
    <%= f.label :title %>
    <%= f.text_field :title, class: 'form-control' %>
  </div>

  <%= f.submit 'Post', class: 'btn btn-primary' %>
<% end %>
```

向顶部菜单添加新链接:

```
[...]
<ul class="nav navbar-nav">
  <% if can? :create, Project %>
    <li><%= link_to 'Add Project', new_project_path %></li>
  <% end %>
</ul>
[...]
```

如您所见，我使用的是`:create`而不是`:new`——如果用户可以创建记录，他们也可以访问“新记录”页面。

现在再增加几个能力:

*型号/能力. rb*

```
def initialize(user)
  user ||= User.new

  if user.role?(:admin)
    can :manage, :all
  elsif user.role?(:moderator)
    can :create, Project
    can :read, Project
  elsif user.role?(:user)
    can :read, Project
  end
end
```

等等，这个`:read`动作是什么意思？那`:index`呢？看起来，CanCanCan 默认引入了一些动作别名:

```
alias_action :index, :show, :to => :read
alias_action :new, :to => :create
alias_action :edit, :to => :update
```

`:read`包含了`:index`和`:show`，而`:create`也意味着能够呼叫`:new`。这非常方便，您可以使用相同的原则轻松定义自己的别名:

```
alias_action :update, :destroy, :to => :modify
```

点击阅读更多[。](https://github.com/CanCanCommunity/cancancan/wiki/Action-Aliases)

最后让我们来处理一下`edit`、`update`、`destroy`动作:

*projects_controller.rb*

```
[...]
def edit
  @project = Project.find(params[:id])
  authorize! :edit, @project
end

def update
  @project = Project.find(params[:id])
  if @project.update_attributes(project_params)
    flash[:success] = 'Project was updated!'
    redirect_to root_path
  else
    render 'edit'
  end
  authorize! :update, @project
end

def destroy
  @project = Project.find(params[:id])
  if @project.destroy
    flash[:success] = 'Project was destroyed!'
  else
    flash[:warning] = 'Cannot destroy this project...'
  end
  redirect_to root_path
  authorize! :destroy, @project
end
[...]
```

观点:

*edit.html.erb*

```
<div class="page-header"><h1>Edit Project</h1></div>

<%= render 'form' %>
```

现在，添加两个按钮来编辑和销毁项目:

*views/projects/index . html . erb*

```
[...]
<% @projects.each do |project| %>
  <div class="well well-sm">
    <h2><%= project.title %></h2>
    <% if can? :update, project %>
      <%= link_to 'Edit', edit_project_path(project), class: 'btn btn-info' %>
    <% end %>

    <% if can? :destroy, project %>
      <%= link_to 'Delete', project_path(project), class: 'btn btn-danger', method: :delete, data: {confirm: 'Are you sure?'} %>
    <% end %>
  </div>
<% end %>
```

我传递的是`project`对象，而不是`Project`类——这样我可以引入更精确的访问规则。例如，我可以添加一条规则，规定用户只能编辑不到 2 小时前添加的项目。

关于能力:

*型号/能力. rb*

```
if user.role?(:admin)
  can :manage, :all
elsif user.role?(:moderator)
  can [:create, :read, :update], Project
elsif user.role?(:user)
  can :read, Project
end
```

注意，`can`方法接受一组动作作为第一个参数。实际上，第二个参数也可以是一个数组:

```
can [:create, :read, :update], [Project, Task]
```

我们可以重写这行

```
can [:create, :read, :update], Project
```

另一种方式是通过排除一些权限:

```
can :manage, Project
cannot :destroy, Project
```

这意味着用户可以对项目做任何事情，但不能破坏它们。请注意这里**行的顺序很重要**。如果您将`cannot`放在`can`之前，用户将能够对项目执行**任何动作**。你可以在这里阅读更多关于优先权的内容。

### 处理代码重复

你不觉得在每个控制器的动作中调用`authorize!`挺繁琐的吗？此外，如果您忘记在某个方法中包含它，该怎么办。康康康也处理这个！使用`load_and_authorize_resource`有助于消除代码重复:

*projects_controller.rb*

```
class ProjectsController < ApplicationController
  load_and_authorize_resource
  [...]
end
```

实际上，这个方法由两个方法组成:`load_resource`和`authorize_resource`，每一个都非常简单明了。如果您愿意，您可以分别给他们打电话:

```
load_resource
authorize_resource
```

`load_resource`将加载记录，而`authorize_resource`将检查用户是否被授权对该记录执行一个动作(相当于当前动作的名称)。但是对于不同的操作，资源是如何加载的呢？

*   对于`index`，资源(分配给实例变量，名称为复数形式)将被设置为`Model.accessible_by(current_ability)`。`accessible_by`是一个很酷的方法，它只加载当前用户实际可以访问的记录(在我们的例子中，基本用户不能查看已完成的项目——我们将很快介绍这个场景)。
*   对于`show`、`edit`、`update`和`destroy`，将简单地使用`find`方法:`Model.find(params[:id])`加载资源
*   对于`new`和`create`，将使用`new`方法初始化资源。
*   对于自定义(非 CRUD)动作，将使用`find`加载资源，但是这种行为可以修改。

所以，我们的控制器可以简化成这样:

*projects_controller.rb*

```
class ProjectsController < ApplicationController
  load_and_authorize_resource
  [...]

  def update
    if @project.update_attributes(project_params)
      flash[:success] = 'Project was updated!'
      redirect_to root_path
    else
      render 'edit'
    end
  end

  def create
    if @project.save
      flash[:success] = 'Project was saved!'
      redirect_to root_path
    else
      render 'new'
    end
  end

  def destroy
    if @project.destroy
      flash[:success] = 'Project was destroyed!'
    else
      flash[:warning] = 'Cannot destroy this project...'
    end
    redirect_to root_path
  end
end
```

有什么变化？

*   我移除了`authorize!`方法调用，因为`authorize_resource`为我们做了这项工作。
*   其次，我已经完全删除了`index`、`new`和`edit`动作，因为它们是由`load_resource`处理的。
*   第三，我已经从`update`和`destroy`动作中删除了`@project = Project.find(params[:id])`行，也从`create`中删除了`@project = Project.new(project_params)`，因为`load_resource`再次为我们处理了这个问题。

是的，我知道你在想什么。强参数呢？排序和分页呢？如果我想跳过对某些操作的资源加载和授权，该怎么办？如果我需要一个自定义查找工具怎么办？那些都是很棒的问题，让我们一个一个讨论。

**强参数**。初始化资源时，CanCanCan 检查控制器是否响应以下方法:

*   `create_params`或`update_params`。CanCanCan 将根据当前操作使用这些方法之一来净化输入。这很酷，因为您可以为创建和更新定义不同的清理规则。
*   如果没有定义`create_params`或`update_params`方法，康康康将搜索`<model_name>_params</model_name>`方法——这就是我们在演示中使用的方法(`project_params`)。
*   最后，CanCanCan 将搜索一个静态名为`resource_params`的方法。
*   您还可以提供自己的杀毒方法的名称来覆盖这个默认行为:`load_and_authorize_resource param_method: :my_sanitizer`。
*   如果 CanCanCan 在控制器中找不到这些方法中的任何一个，并且没有设置自定义消毒程序，它将正常初始化资源。

**覆盖资源加载**。例如，我希望索引页面上的项目按照创建日期降序排列。您可以轻松做到这一点:

*projects_controller.rb*

```
[...]
before_action :load_projects, only: :index
load_and_authorize_resource

[...]

private

def load_projects
  @projects = Project.accessible_by(current_ability).order('created_at DESC')
end

[...]
```

这个想法是，`load_resource`将把资源加载到实例变量中，如果它还没有被设置的话。只要我添加了`before_action`来设置`@projects`，`load_resource`就不会为`index`动作做任何事情。重要的是把`before_action` **放在** `load_and_authorize_resource`之前。

注意，在`load_projects`中，我使用`accessible_by`只加载当前用户有权访问的记录。

**跳过资源**的加载和授权。如果出于某种原因你想跳过这些动作，只需写下:

```
load_and_authorize_resource only: :index
# or
load_and_authorize_resource except: :index
```

**自定义发现者**。正如我们所见，`load_resource`使用`find`方法加载资源。这很容易通过提供`find_by`选项来改变:

```
load_resource find_by: :title
authorize_resource
```

康康康是非常灵活的，你可以很容易地覆盖它的默认行为！完整的信息可以在这里找到[。](https://github.com/CanCanCommunity/cancancan/wiki/Authorizing-controller-actions)

## 为能力添加条件

现在，假设每个项目都有`ongoing`布尔属性。用户应该只能访问正在进行的项目，而版主可以查看所有项目，但只能更新正在进行的项目。

首先，添加一个新的迁移:

```
$ rails g migration add_ongoing_to_projects ongoing:boolean
```

修改迁移文件:

*XXX _ add _ ongoing _ to _ projects . Rb*

```
[...]
def change
  add_column :projects, :ongoing, :boolean, default: true
  add_index :projects, :ongoing
end
[...]
```

应用迁移:

```
$ rake db:migrate
```

不要忘记更新表格:

*views/projects/_ form . html . erb*

```
<%= form_for @project do |f| %>
  <div class="form-group">
    <%= f.label :title %>
    <%= f.text_field :title, class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :ongoing %>
    <%= f.check_box :ongoing %>
  </div>

  <%= f.submit 'Post', class: 'btn btn-primary' %>
<% end %>
```

并修改允许的属性列表:

*projects_controller.rb*

```
[...]
def project_params
  params.require(:project).permit(:title, :ongoing)
end
[...]
```

现在能力:

*型号/能力. rb*

```
[...]
if user.role?(:admin)
  can :manage, :all
elsif user.role?(:moderator)
  can :create, Project
  can :update, Project do |project|
    project.ongoing?
  end
  can :read, Project
elsif user.role?(:user)
  can :read, Project, ongoing: true
end
[...]
```

`can`在定义规则时，接受条件块或条件散列，使其更加具体。对于散列，
只使用表列很重要，因为这些条件将与`accessible_by`方法一起使用。在这里阅读更多。

现在，切换到`user`角色并打开项目页面。在控制台中，您应该会看到类似如下的输出:

```
SELECT "projects".* FROM "projects" WHERE "projects"."ongoing" = 't' ORDER BY created_at DESC
```

这意味着`accessible_by`工作正常——它只自动加载用户可以使用*能力. rb* 中提供的条件访问的资源。真的很酷。

## 强制授权

如果您想检查授权是否发生在每个控制器中，您可以将`check_authorization`添加到`ApplicationController`:

*application _ controller . Rb*

```
class ApplicationController < ActionController::Base
  check_authorization
  [...]
end
```

如果在某个动作中没有执行授权，将引发`CanCan::AuthorizationNotPerformed`错误。尽管如此，对于某些控制器，我们还是想跳过这个检查，因为任何用户都应该能够访问主页并在角色之间切换。这很简单:

*pages_controller.rb*

```
class PagesController < ApplicationController
  skip_authorization_check
end
```

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  skip_authorization_check
end
```

`skip_authorization_check`也接受`only`和`except`选项。此外，`check_authorization`接受`if`和`unless`选项来定义检查授权是否发生的条件，例如:

```
check_authorization if: :admin_subdomain?

private

def admin_subdomain?
  request.subdomain == "admin"
end
```

点击阅读更多[。](https://github.com/CanCanCommunity/cancancan/wiki/Ensure-Authorization)

## 结论

在本文中，我们讨论了 CanCanCan，这是一个很好的 Rails 授权解决方案。我希望现在您有信心在您的项目中使用它并实现更复杂的场景。我真的鼓励你浏览康康康的 wiki，因为它有非常有用的例子。

感谢您的阅读！一如既往，任何读者都有权对本文发表反馈意见:)。再见！

## 分享这篇文章