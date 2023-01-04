# 单元:对依赖注入和测试的深入研究

> 原文：<https://www.sitepoint.com/cells-a-deeper-look-into-dependency-injection-and-testing/>

![](img/96598ce4018650dc27bcd1c375502538.png)

在我之前的文章中，你和 Scott 学习了 Cells 的基础知识，这是 Ruby 和 Rails 框架的视图模型层。

过去有一堆访问控制器实例变量、局部变量和全局帮助函数的部分，现在有了一堆单元。细胞是物体。到目前为止，一切顺利。

斯科特现在明白了，每个单元格都代表最终网页的一部分。此外，该单元通过包含呈现该片段所必需的逻辑以及提供呈现模板的能力来提供帮助，就像我们过去在控制器视图中所做的那样。

太好了，但是我们现在要怎么处理这些呢？

早在 cells 还是一个非常年轻的项目的时候，许多开发人员就对将 Cells 用于侧栏、导航标题或其他可重用组件很感兴趣。适当封装的好处以及随之而来的可重用性是这类项目不可避免的优势。

然而，单元格可以用于“一切”，这意味着它们可以替换整个 ActionView 渲染堆栈，并提供整个页面，速度更快，架构更好。

这引起了斯科特的兴趣。

## 用户列表

为什么不实现一个页面，列出注册了 Scott 流行的 web 应用程序的所有用户？

在 Rails 中，这个特性通常位于`UsersController`及其`#index`动作中。让我们使用单元格来代替控制器视图。

## 无布局的页面单元格

为了更好地理解，我们应该从`UsersController`开始，看看页面单元格是如何从那里呈现的。

请注意，我们仍然使用控制器的布局来包装用户列表。这是因为我们想逐步学习如何使用细胞。斯科特喜欢这样。但是 Scott 需要记住，单元格也可以渲染应用程序布局，这使得 ActionView 完全是多余的！这一点我们将在后面探讨。

```
class UsersController < ApplicationController
  def index
    render html: cell("user_index_cell"), layout: true
  end
  ... 
```

控制器所做的只是渲染我们现在必须实现的`UserIndexCell`。您是否注意到没有模型被传递到`cell`调用中？这是因为如果需要，细胞可以自己聚集数据。我们将很快了解处理依赖关系的最佳方式。

使用带有`:html`选项的`render`将简单地将传递的字符串返回给浏览器。使用`layout: true`,它——令人惊讶地——将该字符串包装在控制器的布局中。

这都是特定于 Rails 的。现在，让我们进入真正的细胞。在常规设置中，新的`UserIndexCell`将进入**app/cells/user _ index _ cell . Rb**:

> 在 Trailblazer 中，单元具有不同的命名结构和目录布局。Scott 的用户单元将被称为`User::Cell::Index`并位于`app/concepts/user/cell/index.rb`中，但这是后续文章中完全不同的故事。

```
class UserIndexCell < Cell::ViewModel
  def show
    @model = User.all
    render
  end
end 
```

有了这篇介绍性的文章，这看起来并不新鲜。单元格的`show`方法将通过调用`User.all`来分配`@model`实例变量，然后呈现其视图。

## 视图中的迭代

在视图中，我们可以使用用户集合并显示一个格式良好的用户列表。在传统单元格中，视图位于**app/Cells/user _ index/show . haml**中，如下所示:

```
%h1 All Users

%ul
  - model.each do |user|
    %li
      = link_to user.email, user
      = image_tag user.avatar 
```

由于我们之前分配了`@model`，我们现在可以在视图中使用单元格的内置`model`方法来迭代集合并呈现列表。

Scott 是一个专注的自封的软件架构师，他把眼睛眯成了细缝。想象中的风滚草从他 23 英寸的外部显示器后面经过。沉默。

他现在不喜欢两件事:

为什么细胞提取它的模型？这难道不是从外部世界传入的依赖，比如，控制者？

还有，为什么细胞的视图这么乱？我们不是说过单元格视图应该是无逻辑的吗？这看起来就像是传统 Rails 项目的一部分。

你说得对，斯科特，你的建筑师直觉让你提出了正确的问题。

将数据聚合知识保存在单元中不是好的做法，除非它真的有意义，并且您将单元理解为一个独立的小部件。

## 外部依赖性

每当你分配`@model`的时候，你必须问你自己:*“让别人抓取我的数据不是更好吗？”*。这是在控制器中如何实现的:

```
class UsersController < ApplicationController
  def index
    users = User.all
    render html: cell("user_index_cell", users), layout: true
  end
  ... 
```

现在，控制器的责任是为单元格找到合适的输入。尽管 Rails MVC 与真正的 MVC 相去甚远，但这是控制器应该做的事情。

我们现在也可以简化细胞:

```
class UserIndexCell < Cell::ViewModel
  def show
    render
  end
end 
```

记住，传递给`cell`的第一个参数在单元格及其视图中总是作为`model`可用。但是，请不要与术语*【模型】*混淆。Rails 误解了我们,“模型”总是一个特定的实体。在 OOP 中，模型只是一个对象，在我们的上下文中，这是一个持久对象的数组。

让我们看看现在如何润色视图，并减少其中的逻辑。它的下一个版本将使用实例方法作为“助手”。好一点，但不完美:

```
%h1 All Users

%ul
  - model.each do |user|
    %li
      = link user
      = avatar user 
```

现在由两个“助手”`link`和`avatar`来做这项工作，而不是在视图中保留模型内部。因为我们在迭代，所以我们仍然必须将迭代的用户对象传递给方法——这是一个次优对象设计的结果，我们很快就会解决这个问题。

## Helpers ==实例方法

为了让`link`和`avatar`工作，我们需要向单元格添加实例方法:

```
class UserIndexCell < Cell::ViewModel
  def show
    render
  end

private
  def link(user)
    link_to(user.email, user)
  end

  def avatar(user)
    image_tag(user.avatar)
  end
end 
```

所有表示逻辑现在都被很好地封装为单元中的实例方法。视图被整理了一下，并且只委托给“助手”。

嗯，算是吧，因为 Scott 既不喜欢将`user`实例显式传递给每个助手，也不喜欢手动`each`循环。他皱起鼻子…一定有更好的方法。

## 嵌套单元格

在 OOP 中，当你开始一个接一个地传递对象时，这通常是一个有缺陷的对象设计的标志。

如果我们需要向所有这些助手传递一个实例，为什么不引入另一个单元呢？这个单元负责只呈现单个用户，并在一个对象中包含所有连续的助手调用？

斯科特又戴上了他的白色建筑师帽。“是的，听起来不错。”

合理的结论是为一个用户引入一个新的小区。它将生活在**app/cells/user _ index _ detail _ cell . Rb**中。

众所周知，这个名字非常奇怪，是 Rails 缺少命名空间约定的结果。让我们现在就开始吧，但请记住，下一篇文章将介绍开拓者单元格，其中名称空间和强约定使这看起来更令人愉快:

```
class UserIndexDetailCell < Cell::ViewModel
  def show
    render
  end

private
  def link
    link_to(model.email, model)
  end

  def avatar
    image_tag(model.avatar)
  end
end 
```

我们将`UserIndexCell`中的`link`和`avatar`(是的，删除那段代码，再见)移至`UserIndexDetailCell`。因为后者应该只呈现一个用户，所以我们可以在这里安全地使用`model`,不需要传递任何东西。

以下是**app/cells/user _ index _ detail/show . haml**中的视图——再次声明，斯科特，请耐心听我说。下一篇文章将展示如何在更加简化的结构中实现这一点:

```
%li
  = link
  = avatar 
```

斯科特喜欢这个。简单的观点不能破吧？

现在我们已经实现了两个单元(一个用于页面，一个用于列出的用户)，我们如何连接它们呢？一个简单的嵌套单元格调用就可以做到，如下面的**app/cells/user _ index/show . haml**视图所示:

```
%h1 All Users

%ul
  - model.each do |user|
    = cell("user_index_detail", user) 
```

在有硬编码项目视图的地方，我们现在分派到新的单元格。正如您可能已经猜到的，这个新的细节单元实际上是在每次数组迭代时被实例化和调用的。而且还是比偏音快！

但是，不要把它和助手混淆了。明细单元格不能访问索引单元格，反之亦然。依赖关系必须显式地传递，没有一个单元可以访问另一个单元的内部、实例变量甚至帮助器方法。

无论如何，呈现集合是 Cells 作者已经考虑过的事情。

## 呈现收藏

Cells 提供了一个方便的 API 来呈现集合，而不必手动遍历它们。Scott 喜欢简单的 API，就像他喜欢简单的、无逻辑的视图一样:

```
%h1 All Users

%ul
  = cell("user_index_detail", collection: model) 
```

当提供`:collection`选项时，单元格将为您进行迭代！好消息是，在即将到来的 Cells 5.0 中，由于更多的简化，这将有另一个巨大的性能提升。

斯科特对他的新景观建筑非常满意。他喝了一口马绪真冰镇啤酒，这是对他辛苦得来的口渴的回报，然后冻住了。不，不是冰镇饮料让他变成了盐柱。这是测试！他一行字也没写过。

## 测试细胞

细胞是对象，对象非常容易测试。

那么，一个人从哪里入手这么多的物品呢？我们可以开始测试单个细节单元，只是为了编写测试。Scott 更喜欢 Minitest 而不是 Rspec。然而，这并不意味着 Scott 想要开始另一场关于测试框架的宗教战争。

单元测试包括三个步骤:

1.  设置测试环境，例如使用夹具。
2.  调用实际的单元格。
3.  测试输出。通常，这是使用水豚的精细匹配器来完成的。

说到水豚，为了在 Minitest 中正确使用这个宝石，建议在您的宝石文件中包含适当的宝石:

```
 group :test do
  gem "minitest-rails-capybara"
  ...
 end 
```

在 **test_helper.rb** 中，一些水豚助手不得不混入你的`Spec`基类中。这是为了拯救斯科特一个可怕的头痛，甚至一个偏头痛:

```
Minitest::Spec.class_eval do
  include ::Capybara::DSL
  include ::Capybara::Assertions
end 
```

现在进行实际测试。这个测试文件可以放在**test/cells/user _ index _ detail _ test . Rb**中。

```
class UserCellTest < MiniTest::Spec
  include Cell::Testing
  controller UsersController

  let (:user) { User.create(email: "g@trb.to", avatar: image_fixture) }

  it "renders" do
    html = cell(:user_index_detail, user).()

    html.must_have_css("li a", "g@trb.to")
    html.must_have_css("img")
  end
end 
```

如果你仔细观察，这实际上只是一个单元测试。一个单元测试，其中调用被检查的对象，并断言副作用。

当渲染一个单元格时，副作用应该是发出 HTML，这可以很容易地用水豚来测试。斯科特印象深刻。

## 单元测试=单元测试

有趣的事实是，任何地方都没有魔法发生。

传统的 Rails 助手测试及其复杂的魔术可以欺骗您认为您的代码正在工作，如果您没有将正确的参数传递到单元格中，这个测试就会失败。

您必须聚集正确的数据，实例化并调用对象，然后*然后*您可以检查返回的 HTML 片段。

斯科特挠了挠头。他现在明白细胞测试是什么样的了。它只需要调用和断言。然而，对每个小单元进行单元测试有意义吗？将系统作为一个整体来测试不是更有意义吗，我们只渲染`UserIndexCell`并看看它是否运行？

正确，斯科特。

根据经验，从最上面的单元格开始测试，并尝试从那里断言尽可能多的细节。如果组合的、嵌套的单元格产生了高层次的复杂性，那么将测试分解到较低层次并没有错。

自顶向下方法的好处是，当改变内部结构时，您不必重写一大堆测试。这在“普通”OOP 测试中感觉熟悉吗？是的，因为细胞只是物体。

下面是如何编写一个完整的自顶向下的测试。不用担心内部问题，索引单元格是直接呈现的:

```
it "renders" do
  html = cell(:user_index, collection: [user, user2]).()

  html.must_have_css("li", count: 2)
  html.must_have_css("li a", "g@trb.to")
  html.must_have_css("li a", "2@trb.to")
  # ..
end 
```

注意我们现在如何呈现用户集合，作为一个逻辑结论，断言整个列表，而不仅仅是单个项目。

测试视图组件并不困难。相反的情况是:这等同于使用一个细胞。当您使用定义良好的 API 编写干净、简单的对象时，这种行为是免费的。

有了一些水豚的断言，你可以快速编写测试，确保你的细胞在生产中肯定会工作，使你的视图层坚如磐石。

## 下一步是什么？

我们将为所有的小东西编写单元，将它们作为具有任何复杂程度的集合，并且，最重要的部分，测试那些对象，使其不再崩溃。

在下一篇文章中，我们将讨论 Cells 的一些专业特性，比如将 CSS 和 Javascript 资产打包到 Rails 资产管道中、视图继承、缓存，以及`Trailblazer::Cell`如何引入更直观的文件和命名结构。

干得好，斯科特。让那些东西过来！

## 分享这篇文章