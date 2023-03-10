# 使用 Cocoon Gem 在 Rails 中实现更好的嵌套属性

> 原文：<https://www.sitepoint.com/better-nested-attributes-in-rails-with-the-cocoon-gem/>

![Cocoon](img/8576cb474aab9903beda40f7e7a74e15.png)

在本文中，我们将讨论如何使用 Rails 的嵌套属性特性构建更复杂的表单。我将向您展示如何从一个表单中操作多个关联的记录，并正确地设置模型和控制器来启用这个特性。此外，我们将讨论常见的陷阱，并使用名为[茧](https://github.com/nathanvda/cocoon)的宝石增强我们的表单，使其更加动态。该解决方案允许异步添加和删除嵌套字段，同时提供大量定制选项和回调。

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/NestedForms) 获得。

演示应用程序可在 sitepoint-nested-forms.herokuapp.com 获得。

## 构建简单的表单

*在这个演示中，我将使用 Rails 5，但是大部分描述的概念也适用于 Rails 3 和 4*

继续创建一个没有默认测试套件的新应用程序:

```
$ rails new NestedForms -T 
```

假设，有了这个应用程序，我们想要跟踪我们最喜欢的地方和它们的地址。例如，如果我们输入“咖啡馆”作为一个地方，以及一串我们喜欢的咖啡馆的地址。这意味着一个地方可能有许多地址，因此我们将使用关联来描述它:

```
$ rails g model Place title:string
$ rails g model Address city:string street:string place:belongs_to
$ rake db:migrate 
```

确保关联设置正确:

*models/place.rb*

```
[...]
has_many :addresses, dependent: :destroy
[...] 
```

*型号/地址. rb*

```
[...]
belongs_to :place
[...] 
```

现在编写一个基本的`PlacesController`(统治所有人的那个…):

*app/控制器/地点 _ 控制器. rb*

```
class PlacesController < ApplicationController
  def index
    @places = Place.all
  end

  def new
    @place = Place.new
  end

  def create
    @place = Place.new(place_params)
    if @place.save
      redirect_to root_path
    else
      render :new
    end
  end

  private

  def place_params
    params.require(:place).permit(:title)
  end
end 
```

添加路线:

*config/routes.rb*

```
[...]
resources :places, only: [:new, :create, :edit, :update]

root to: 'places#index'
[...] 
```

现在，根页面的视图:

*views/places/index . html . erb*

```
<h1>Places</h1>

<p><%= link_to 'Add place', new_place_path %></p>

<ul><%= render @places %></ul> 
```

添加了`render @places`之后，我们还需要相应的部分:

*views/places/_ place . html . erb*

```
<li>
  <strong><%= place.title %></strong><br>
  <% if place.addresses.any? %>
    Addresses:
    <ul>
      <% place.addresses.each do |addr| %>
        <li>
          <%= addr.city %>, <%= addr.street %>
        </li>
      <% end %>
    </ul>
  <% end %>
</li> 
```

创建地点的视图:

*views/places/new . html . erb*

```
<h1>Add place</h1>

<%= render 'form' %> 
```

包括表单:

*views/places/_ form . html . erb*

```
<%= render 'shared/errors', object: @place %>

<%= form_for @place do |f| %>
  <div>
    <%= f.label :title %>
    <%= f.text_field :title %>
  </div>

  <%= f.submit %>
<% end %> 
```

这里还有另一个部分显示错误:

*views/shared/_ errors . html . erb*

```
<% if object.errors.any? %>
  <div>
    <strong>
      <%= pluralize(object.errors.count, 'error') %> were found
    </strong>

    <ul>
      <% object.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
    </ul>
  </div>
<% end %> 
```

到目前为止一切顺利。然而，就更好的用户体验而言，我希望允许在同一页面上添加 place 的地址，而不是编写一个单独的表单。这也将使我们不用编写额外的控制器来处理地址。这就是嵌套属性发挥作用的地方。

## 添加嵌套属性

嵌套属性背后的想法非常简单。您有一个表单，可以在其中创建一个对象及其相关记录。这个特性可以很快添加，因为它只需要对控制器和模型进行很小的修改，以及一些标记。

这一切都是从添加长期命名的[accepts _ nested _ attributes _ for](http://apidock.com/rails/ActiveRecord/NestedAttributes/ClassMethods/accepts_nested_attributes_for)方法开始的:

*车型/地点. rb*

```
[...]
accepts_nested_attributes_for :addresses
[...] 
```

添加了这个方法后，我们现在可以通过地址的批量分配机制来操作地址。控制器也需要一些改变:

*places_controller.rb*

```
[...]
private

def place_params
  params.require(:place).permit(:title, addresses_attributes: [:id, :city, :street])
end
[...] 
```

当您提交带有嵌套字段的表单时，`params[:place]`将在键`:addresses_attributes`下包含一个数组。这个数组描述了要添加到数据库中的每个地址。只要我们使用 strong_params，这些新属性就必须被明确允许。

现在将嵌套表单添加到视图中:

*views/places/_ form . html . erb*

```
<%= form_for @place do |f| %>
  <%= render 'shared/errors', object: @place %>
  <div>
    <%= f.label :title %>
    <%= f.text_field :title %>
  </div>

  <div>
    <p><strong>Addresses:</strong></p>

    <%= f.fields_for :addresses do |address| %>
      <div>
        <%= address.label :city %>
        <%= address.text_field :city %>

        <%= address.label :street %>
        <%= address.text_field :street %>
      </div>
    <% end %>
  </div>

  <%= f.submit %>
<% end %> 
```

正如您可能已经猜到的那样， [fields_for](http://api.rubyonrails.org/classes/ActionView/Helpers/FormBuilder.html#method-i-fields_for) 方法添加了嵌套字段。它非常类似于`form_for`方法，但是不提供`form`标签本身。注意，在程序块内部，我使用了一个新的局部变量`address`–*不要*调用它`f`，因为它已经包含了父表单的构建器。

然而，有一个问题。当您访问“New Place”页面时，您不会看到任何嵌套字段，因为很明显,`Place`类的新实例不包含任何嵌套地址。正如 Rails 文档所建议的，简单的解决方法是直接在控制器中构建几个地址:

*places_controller.rb*

```
[...]
def new
  @place = Place.new
  3.times { @place.addresses.build}
end
[...] 
```

事实上，这不是最好的解决方案，我们以后会摆脱它。

现在，您可以启动服务器，导航到“新位置”页面，并尝试创建一个包含一些嵌套地址的位置。但是，事情不可能总是那么顺利吧？如果你像我一样使用 Rails 5.0，你会看到一个非常奇怪的错误“地址必须存在”，阻止表单被提交。这似乎是 Rails 5 中的一个[重大 bug](https://github.com/rails/rails/issues/25198) ，与设置为`true`的新`belongs_to_required_by_default`选项有关。此设置意味着默认情况下必须存在关联的记录。为了从这种行为中全局退出，你可以将`Rails.application.config.active_record.belongs_to_required_by_default`设置为`false`(在*new _ framework _ defaults . Rb*初始化文件中)或者为`belongs_to`方法提供`optional: true`选项。

这里建议的另一个修复涉及到使用`inverse_of`选项:

*models/place.rb*

```
[...]
has_many :addresses, dependent: :destroy, inverse_of: :place
[...] 
```

这个 bug 应该在 Rails 5.1 中修复。

### 一点认可

目前，用户可能会创建一个包含空地址列表的位置，这可能不是您想要的。为了控制这种行为，使用接受 lambda 或`:all_blank`值的`reject_if`选项。`:all_blank`将拒绝所有属性为空的记录。然而，在我们的例子中，如果*的任何*属性为空，我们想要拒绝，所以让我们坚持使用 lambda:

*models/place.rb*

```
[...]
accepts_nested_attributes_for :addresses,
                              reject_if: ->(attrs) { attrs['city'].blank? || attrs['street'].blank? }
[...] 
```

现在任何没有城市或街道的地址都不会被保存到数据库中。

## 摧毁他们

现在可以添加这些地址，但是以后无法删除它们。为了解决这个问题，为`accepts_nested_attributes_for`方法提供了另一种选择:

*models/place.rb*

```
[...]
accepts_nested_attributes_for :addresses, allow_destroy: true,
                              reject_if: ->(attrs) { attrs['city'].blank? || attrs['street'].blank? }
[...] 
```

这仅仅意味着现在嵌套的记录可以被销毁。为了销毁嵌套记录，`_destroy`字段必须设置一个真值(即 1、‘1’、真或‘真’)。这个新字段也必须被允许:

*places_controller.rb*

```
[...]
private

def place_params
  params.require(:place).permit(:title, addresses_attributes: [:id, :city, :street, :_destroy])
end
[...] 
```

添加复选框以标记要删除的嵌套记录:

*views/places/_ form . html . erb*

```
[...]
<div>
  <p><strong>Addresses:</strong></p>

  <%= f.fields_for :addresses do |address| %>
    <div>
      <%= address.label :city %>
      <%= address.text_field :city %>

      <%= address.label :street %>
      <%= address.text_field :street %>

      <%= address.check_box :_destroy %>
    </div>
  <% end %>
</div>
[...] 
```

现在编写两个新的控制器动作:

*places_controller.rb*

```
[...]
def edit
  @place = Place.find_by(id: params[:id])
end

def update
  @place = Place.find_by(id: params[:id])
  if @place.update_attributes(place_params)
    redirect_to root_path
  else
    render :edit
  end
end
[...] 
```

注意动作本身不需要任何特别的改动，真的很棒。

再添加两条路线:

*config/routes.rb*

```
[...]
resources :places, only: [:new, :create, :edit, :update]
[...] 
```

并显示“编辑”链接:

*views/places/_ place . html . erb*

```
<li>
  <strong><%= place.title %></strong> | <%= link_to 'Edit place', edit_place_path(place) %><br>
  [...]
</li> 
```

现在打开任何现有的地方，设置复选框附近的地址，你想摧毁和提交表格！

## 让它充满活力

基本的嵌套形式是完整的，但是，使用起来不是很方便。例如，无法添加三个以上的地址。添加这个特性需要做更多的工作，因为 Rails 不支持开箱即用地动态添加字段。幸运的是，我们已经有了解决方案。叫[茧](https://github.com/nathanvda/cocoon)很牛逼。Cocoon 用 JavaScript 支持嵌套表单，允许动态添加或删除文件。Cocoon 还提供了其他定制功能。此外，它与表单生成器无关，因此可以与基本的 Rails 表单生成器或 SimpleForm 或 Formtastic 等解决方案一起使用。

Cocoon 入门很简单。添加新宝石:

*Gemfile*

```
[...]
gem "cocoon"
[...] 
```

并安装它:

```
$ bundle install 
```

接下来，连接一个新的 JavaScript 文件:

*JavaScript s/application . js*

```
[...]
//= require cocoon
[...] 
```

注意，Cocoon 要求 jQuery 存在。现在将嵌套字段提取到一个单独的部分:

*views/places/_ address _ fields . html . erb*

```
<div class="nested-fields">
  <%= f.label :city %>
  <%= f.text_field :city %>

  <%= f.label :street %>
  <%= f.text_field :street %>

  <%= f.check_box :_destroy %>

  <%= link_to_remove_association "remove address", f %>
</div> 
```

在这里我们遇到了第一个茧的帮手——[`link_to_remove_association`](https://github.com/nathanvda/cocoon#link_to_remove_association)。顾名思义，这个助手创建一个新的链接，异步删除一个关联的记录。该方法接受三个参数(第三个是可选的):

*   要在链接中显示的文本
*   表单对象
*   HTML 选项(类似于传递给`link_to`的选项)

注意，`nested-fields`类是“删除地址”链接工作所必需的。

现在我们需要在表单中使用这个部分:

*views/places/_ form . html . erb*

```
<%= form_for @place do |f| %>
  <%= render 'shared/errors', object: @place %>

  <div>
    <%= f.label :title %>
    <%= f.text_field :title %>
  </div>

  <div>
    <p><strong>Addresses:</strong></p>

    <div id="addresses">
      <%= f.fields_for :addresses do |address| %>
        <%= render 'address_fields', f: address %>
      <% end %>

      <div class="links">
        <%= link_to_add_association 'add address', f, :addresses %>
      </div>
    </div>
  </div>

  <%= f.submit %>
<% end %> 
```

这里我们使用第二个 Cocoon 的助手-[link _ to _ add _ association](https://github.com/nathanvda/cocoon#link_to_add_association)。它呈现一个链接，使用我们一分钟前编写的部分代码动态添加嵌套字段。该方法接受四个参数(第四个是可选的):

*   要在链接中显示的文本
*   表单生成器(父表单，而不是嵌套表单！)
*   关联的名称
*   HTML 选项。这些选项类似于`link_to`接受的选项，但是有一些特殊的参数可用(比如在哪里呈现嵌套字段或者使用哪个部分)，所以一定要浏览[文档](https://github.com/nathanvda/cocoon#link_to_add_association)

差不多就是这样！启动服务器，尝试添加和删除地址。现在这样方便多了，不是吗？

## 茧的背甲

今天我要展示的最后一件事是如何设置 Cocoon 的[回调](https://github.com/nathanvda/cocoon#callbacks-upon-insert-and-remove-of-items)。他们有四个人:

*   `cocoon:before-insert`
*   `cocoon:after-insert`
*   `cocoon:before-remove`
*   `cocoon:after-remove`

使用`cocoon:before-insert`你可以动画显示嵌套字段的外观。让我们在一个新的 CoffeeScript 文件中对此进行编码:

*JavaScript s/global . coffee*

```
jQuery(document).on 'turbolinks:load', ->
  addresses = $('#addresses')

  addresses.on 'cocoon:before-insert', (e, el_to_add) ->
    el_to_add.fadeIn(1000) 
```

只要我在使用 [Turbolinks 5](https://github.com/turbolinks/turbolinks-classic) ，我们就在监听`turbolinks:load`事件。如果出于某种原因，您希望远离 Turbolinks，第一行会简单得多:

*JavaScript s/global . coffee*

```
jQuery -> 
```

需要此文件:

*JavaScript s/application . js*

```
[...]
//= require global
[...] 
```

例如，在`cocoon:after-insert`回调函数中，您可以突出显示添加的字段。jQueryUI 库有一堆[效果](https://jqueryui.com/effect/)可供选择——我将在这个演示中使用“高亮”效果。

添加新宝石:

*Gemfile*

```
gem 'jquery-ui-rails' 
```

安装它:

```
$ bundle install 
```

需要一个新的 JS 文件(注意正确的顺序):

*JavaScript s/application . js*

```
//= require jquery
//= require jquery_ujs
//= require jquery-ui/effect-highlight
//= require cocoon
//= require global
//= require turbolinks 
```

现在利用这个新效果:

*JavaScript s/global . coffee*

```
addresses.on 'cocoon:after-insert', (e, added_el) ->
  added_el.effect('highlight', {}, 500) 
```

要制作一个元素移除的动画，使用`cocoon:before-remove`回调。然而，这里有一个小问题。元素从页面中的实际移除必须延迟，因为否则，我们将无法激活它。

*JavaScript s/global . coffee*

```
addresses.on 'cocoon:before-remove', (e, el_to_remove) ->
  $(this).data('remove-timeout', 1000)
  el_to_remove.fadeOut(1000) 
```

让 Cocoon 延迟 1 秒钟移除元素——刚好够我们播放动画。

最后，让我们显示添加了多少嵌套记录，并动态地更改该计数。添加新的`.count`块:

*views/places/_ form . html . erb*

```
[...]
<div>
  <p><strong>Addresses:</strong></p>

  <div id="addresses">
    <%= f.fields_for :addresses do |address| %>
      <%= render 'address_fields', f: address %>
    <% end %>

    <div class="links">
      <%= link_to_add_association 'add address', f, :addresses %>
    </div>

    <p class="count">Total: <span><%= @place.addresses.count %></span></p>
  </div>
</div>
[...] 
```

接下来，编写一个简单的`recount`函数来改变计数器:

*JavaScript s/global . coffee*

```
jQuery(document).on 'turbolinks:load', ->
  addresses = $('#addresses')
  count = addresses.find('.count > span')

  recount = -> count.text addresses.find('.nested-fields').size()
  [...] 
```

最后，更新`cocoon:after-insert`回调并添加一个名为`cocoon:after-remove`的新回调。该脚本的最终版本如下所示:

*JavaScript s/global . coffee*

```
jQuery(document).on 'turbolinks:load', ->
  addresses = $('#addresses')
  count = addresses.find('.count > span')

  recount = -> count.text addresses.find('.nested-fields').size()

  addresses.on 'cocoon:before-insert', (e, el_to_add) ->
    el_to_add.fadeIn(1000)

  addresses.on 'cocoon:after-insert', (e, added_el) ->
    added_el.effect('highlight', {}, 500)
    recount()

  addresses.on 'cocoon:before-remove', (e, el_to_remove) ->
    $(this).data('remove-timeout', 1000)
    el_to_remove.fadeOut(1000)

  addresses.on 'cocoon:after-remove', (e, removed_el) ->
    recount() 
```

## 极限？

您可能想知道是否有可能以某种方式限制嵌套记录的数量。[accepts _ nested _ attributes _ for](http://api.rubyonrails.org/classes/ActiveRecord/NestedAttributes/ClassMethods.html#method-i-accepts_nested_attributes_for)方法支持`:limit`，它指定了可以处理的关联记录的最大数量。它可以由整数、过程或指向方法的符号提供(过程和方法都必须返回整数)。

然而，在撰写本文时，Cocoon 不支持对嵌套记录的限制。有一个关于这个问题的讨论，但是作者[不认为它](https://github.com/nathanvda/cocoon/issues/176#issuecomment-27851818)是一个核心特性。尽管如此，还有[一个开放的拉取请求](https://github.com/nathanvda/cocoon/pull/206)增加了这个可用的功能，可能在将来的某个时候被合并。

## 结论

在本文中，我们讨论了 Rails 中嵌套属性的用法。我们创建了一个基本的嵌套表单，允许用户添加、编辑和销毁相关记录。后来，我们集成了 Cocoon gem，并用 jQuery 为我们的表单提供了动力，使其具有动态性。

Cocoon 有更多可供定制的选项，所以一定要浏览它的[文档](https://github.com/nathanvda/cocoon)。希望这篇文章对你有用。一如既往，我感谢你和我呆在一起，并很快看到你！

## 分享这篇文章