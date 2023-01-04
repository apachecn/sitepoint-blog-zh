# 具有嵌套属性的复杂 Rails 表单

> 原文：<https://www.sitepoint.com/complex-rails-forms-with-nested-attributes/>

*通过我们的教程[了解 ruby 中的特征类并意识到它们的重要性](https://www.sitepoint.com/premium/screencasts/understanding-eigenclasses-in-ruby-and-realizing-their-importance)在 SitePoint 上学习更多关于 Ruby 的知识。*

Rails 提供了一种强大的机制来轻松创建丰富的表单，称为“嵌套属性”它允许您在表单中组合多个模型，同时保持与简单单模型表单相同的基本代码模式。

在本文中，我将展示使用这种技术的多种不同方式。我假设您熟悉基本的 Rails 表单，即由脚手架命令生成的那种表单。我们将逐步构建一个复杂的表单，允许用户编辑他们的偏好。我们的领域是一个非盈利的管理系统，志愿者(用户)有专业领域和分配给他们的任务。

## 基本形式

让我们从可以编辑用户的基本表单开始。我假设你对这个模式很熟悉，所以我就不解释了。我之所以在这里介绍它，是因为本文的其余部分都将建立在这段代码之上。

首先是一个简单的用户模型，只有一个属性:

```
# app/models/user.rb
class User < ActiveRecord::Base
  validates_presence_of :email
end
```

在整篇文章中，我们将使用同一个控制器。这就是嵌套属性的美妙之处——我们不必更改控制器代码！

```
# app/controllers/users_controller.rb
class UsersController
  def new
    @user = User.new
  end

  def edit
    @user = User.find(params[:id])
  end

  def create
    @user = User.new(params[:user])

    if @user.save
      redirect_to @user
    else
      render :action => 'new'
    end
  end

  def update
    @user = User.find(params[:id])

    if @user.update(params[:user])
      redirect_to @user
    else
      render :action => 'edit'
    end
  end
end
```

我们的基本形式正是由 Rails 脚手架生成的:

```
# app/views/users/_form.html.erb
<%= form_for(@user) do |f| %>
  <% if @user.errors.any? %>
    <div id="error_explanation">
      <h2>
        <%= pluralize(@user.errors.count, "error") %> prohibited
        this user from being saved:
      </h2>

      <ul>
      <% @user.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div>
    <%= f.label :email %>
    <%= f.text_field :email %>
  </div>

  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

好了，让我们开始吧！

## 添加地址

我们将用户的地址记录存储在一个单独的模型中，但是我们希望能够在与用户的其他属性相同的表单上编辑地址。

```
# app/models/user.rb
class User < ActiveRecord::Base
  # ... code from above omitted
  has_one :address
  accepts_nested_attributes_for :address
end

# app/models/address.rb
class Address < ActiveRecord::Base
  belongs_to :user
  validates_presence_of :city
end
```

请注意在`User`模型中添加了`accepts_nested_attributes_for`。这个方法允许您使用相同的批量赋值工具对 User 修改`Address`的实例，这使得简单的表单变得如此琐碎。`accepts_nested_attributes_for`向模型添加一个编写器方法`_attributes`，允许您编写如下代码:

```
user = User.find(1)

# Normal mass-assignment
user.update(:email => 'new@example.com')

# Creates or edits the address
user.update(:address_attributes => {:city => 'Hobart'})
```

您可以看到，如果我们正确设置了表单，我们就不必修改控制器代码，因为编辑地址属性时，您使用的方法与编辑用户电子邮件时使用的方法相同。

为了创建表单，我们将使用`fields_for` builder 方法。这是一个复杂的方法，可以做很多事情。我将通过接下来的例子介绍它的一些行为，而不是提前解释。

首先，你可以传递一个关系名称的符号给`fields_for`,它会从这个关系中直观地知道应该如何呈现字段。我知道这听起来很复杂，但是下面的代码片段应该可以让它更清楚:

```
# app/views/users/_form.html.erb
# ... Form code from above omitted
<%= f.fields_for :address do |ff| %>
  <div>
    <%= ff.label :city %>
    <%= ff.text_field :city %>
  </div>
<% end %>
```

请注意`fields_for`程序块的变量名已更改为`ff`，而不是`f`。在这种情况下，对于`has_one`关系，逻辑是“如果地址存在，显示一个字段来编辑城市属性。否则如果没有地址，就不要显示任何字段。”在这里，我们遇到了第一个绊脚石:如果没有地址时字段是隐藏的，那么我们首先如何创建地址记录呢？由于这是一个视图问题(我们是否显示字段？)，我们想在视图层解决这个问题。我们通过在帮助器中为表单对象设置默认值来实现这一点:

```
# app/helpers/form_helper.rb
module FormHelper
  def setup_user(user)
    user.address ||= Address.new
    user
  end
end

# app/views/users/_form.html.erb
<%= form_for(setup_user(user)) do |f| %>
  ...
```

现在，如果用户没有地址，我们将创建一个新的未保存的地址，该地址将在提交表单时保留。当然，如果它们有地址，则不需要任何操作(`||=`表示“分配这个值，除非它已经有一个值”)。

尝试一下，您会看到 rails 甚至可以正确地累积和显示子对象的错误。它非常整洁。

## 添加任务

一个用户可以被分配许多任务。对于这个例子，一个任务只有一个名字。

```
# app/models/task.rb
class Task < ActiveRecord::Base
  belongs_to :user

  validates_presence_of :name
end

# app/models/user.rb
class User < ActiveRecord::Base
  # ... code from above omitted
  has_many :tasks

  accepts_nested_attributes_for :tasks,
    :allow_destroy => true,
    :reject_if     => :all_blank
end
```

这里新增了两个选项:*允许 _ 销毁*和*拒绝 _ 如果*。当它们变得相关时，我将稍后解释它们。

与地址一样，我们希望在编辑用户的同一表单上分配任务。我们刚刚设置了`accepts_nested_attributes_for`，还有两步:添加正确的`fields_for`输入，并设置默认值。

```
# app/views/users/_form.html.erb
<h2>Tasks</h2>
<%= f.fields_for :tasks do |ff| %>
  <div>
    <%= ff.label :name %>
    <%= ff.text_field :name %>

    <% if ff.object.persisted? %>
      <%= ff.check_box :_destroy %>
      <%= ff.label :_destroy, "Destroy" %>
    <% end %>
  </div>
<% end %>
```

当给定一个有许多关系的名称时，它遍历集合中的每个对象，并为每个记录输出一次给定的字段。因此，对于有两个任务的用户，上面的代码将创建两个文本字段，每个任务一个。

此外，对于保存在数据库中的每个任务，都会创建一个复选框来映射到`_destroy`属性。这是一个由 *allow_destroy* 选项添加的特殊属性。当设置为 true 时，记录将被删除而不是编辑。默认情况下，这种行为是禁用的，所以如果需要的话，记得要显式启用它。

注意，任何持久化记录的 id 都是由`fields_for`自动添加到一个隐藏字段中的，您不需要自己这样做(尽管如果您因为某种原因必须这样做，`fields_for`会很聪明地不再添加它)。)查看生成的 HTML 上的源代码自己看。

我们已创建的表单将允许我们编辑和删除用户的现有任务，但目前无法添加新任务，因为对于没有任务的新用户，`fields_for`将看到一个空关系，并且不呈现任何字段。如上所述，我们通过向视图中的用户添加新的默认任务来解决这个问题。

您可以应用许多不同的 UI 行为，比如使用 javascript 在需要时动态添加新记录。对于这个例子，我们将选择一个简单的行为，在列表的末尾添加三个空白记录，可以随意填充。

```
# app/helpers/form_helper.rb
module FormHelper
  def setup_user(user)
    # ... code from above omitted

    3.times { user.tasks.build }
    user
  end
end
```

`fields_for`将迭代这三个记录，并为它们创建输入。现在，无论用户有多少任务，总会有三个空白文本字段用于添加新任务。这里有一个问题——如果提交一个空白任务，它是一个无效的新记录(空白名称)并导致保存失败，还是从未被填充？默认情况下，Rails 假设是前者，但这通常不是我们想要的。这种行为可以通过将 *reject_if* 选项指定给`accepts_nested_attributes_for`来定制。你可以传递一个为每个属性散列计算的 lambda，如果它应该被拒绝，则返回 true，或者你可以像上面那样使用`:all_blank`快捷方式，这相当于:

```
accepts_nested_attributes_for :tasks,
  :reject_if => proc {|attributes|
    attributes.all? {|k,v| v.blank?}
  }
```

## 更复杂的关系

对于这个应用程序，我们希望用户指定他们感兴趣的非营利领域，如管理或敲门。这是用用户和兴趣之间的多对多关系来建模的。

```
# app/models/interest.rb
class Interest < ActiveRecord::Base
  has_many :interest_users

  validates_presence_of :name
end

# app/models/interest_user.rb
class InterestUser < ActiveRecord::Base
  belongs_to :user
  belongs_to :interest
end

# app/models/user.rb
class User < ActiveRecord::Base
  # ... code from above omitted
  has_many :interest_users
  has_many :interests, :through => :interest_users

  accepts_nested_attributes_for :interest_users,
    :allow_destroy => true
end
```

这里添加的唯一额外概念是`allow_destroy`选项，我们在前面的例子中使用过。顾名思义，这允许我们除了创建和编辑子记录之外，还可以销毁它们。回想一下，默认情况下，这种行为是禁用的，所以我们需要显式地启用它。

和以前一样，在添加了`accepts_nested_attributes_for`之后，向我们的表单添加兴趣复选框还有两个步骤:设置适当的默认值，并使用`fields_for`创建必要的表单字段。让我们从第一个开始:

```
# app/views/users/_form.html.erb
<%= f.fields_for :interest_users do |ff| %>
  <div>
    <%= ff.check_box :_destroy,
          {:checked => ff.object.persisted?},
          '0', '1'
    %>
    <%= ff.label :_destroy, ff.object.interest.name %>
    <%= ff.hidden_field :interest_id %>
  </div>
<% end %>
```

同样，当`fields_for`被赋予一个有许多关系的名称时，它遍历集合中的每个对象，并为每个记录输出一次给定的字段。因此，对于一个有两个兴趣的用户，上面的代码将创建两个复选框，每个复选框对应一个兴趣。

我们知道上面的 *allow_destroy* 选项允许我们发送一个特殊的`_destroy`属性，如果该属性为真，将标记要删除的对象。问题是这与默认复选框的行为相反:当复选框*未被选中*时，我们希望`_destroy`为真，当复选框*被选中*时，我们希望保留记录。这就是`check_box`的最后两个参数(‘0’和‘1’):分别设置选中和未选中的值，将它们从默认值翻转过来。

当我们在那个区域时，我们还需要覆盖决定复选框最初是否被选中的默认逻辑。这就是`:checked => ff.object.persisted?`所做的——如果记录存在于数据库中，那么用户已经表明他们对该区域感兴趣，所以应该选中该框。注意这里使用了`ff.object`来访问循环中的当前记录。你可以在任何`form_for`或`fields_for`中使用这个方法来获取当前对象。

我一直在说检查当前记录是否持久化。当您从数据库中加载一个用户时，当然所有的兴趣记录都会被持久化。问题是只有那些已经选择的兴趣会被显示和检查，然而我们实际上需要显示所有的兴趣，不管它们在过去是否被选择过。这是我们使用前面的`setup_user`方法为没有持久化的兴趣提供“默认”新记录的地方。

```
# app/helpers/form_helper
module FormHelper
  def setup_user(user)
    user.address ||= Address.new
    (Interest.all - user.interests).each do |interest|
      user.interest_users.build(:interest => interest)
    end
    user.interest_users.sort_by! {|x| x.interest.name }
    user/tmp/clean-controllers.md.html
  end
end
```

首先，这段代码为用户当前没有选择的所有兴趣创建一个新的连接记录(`Interest.all - user.interests`)，然后使用就地排序(`sort_by!`)来确保复选框总是以一致的顺序显示。如果不这样做，所有新的未检查记录将被分组在列表的底部。

## 离别赠言

嵌套属性是一种强大的技术，可以快速开发复杂的表单，同时保持代码整洁美观。`fields_for`为遵循嵌套属性模式提供了很多灵活性和选项——[参见文档](http://apidock.com/rails/ActionView/Helpers/FormHelper/fields_for)——你应该总是尝试构建你的表单，以利用`accepts_nested_attributes_for`给你的行为。除了这篇文章之外，只需一点支持动态创建新嵌套记录的 javascript 技巧就能让您的表单真正脱颖而出。

你可以在 github 上下载这篇文章[的完整代码，来体验一下。请在评论中告诉我们你的进展。](https://github.com/RubySource/nested-attributes-demo)

*通过我们的教程[了解 ruby 中的特征类并意识到它们的重要性](https://www.sitepoint.com/premium/screencasts/understanding-eigenclasses-in-ruby-and-realizing-their-importance)在 SitePoint 上学习更多关于 Ruby 的知识。*

## 分享这篇文章