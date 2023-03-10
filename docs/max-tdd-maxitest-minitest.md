# 使用 Maxitest 和 Minitest 最大化您的 TDD

> 原文：<https://www.sitepoint.com/max-tdd-maxitest-minitest/>

![Stress Level](img/98efa53b006578860e36d7c627301fa8.png)

[minitest](https://github.com/seattlerb/minitest) 背后的目标可以用一个词来概括:*简单*。Minitest 不碍事，允许您编写表达性和可读性强的测试。这些理想是伟大的，但有时我们希望在我们的测试上洒上一点魔法；这就是 [maxitest](https://github.com/grosser/maxitest/) 所提供的。Maxitest 增加了像`context`块和按行号运行这样的特性，这使得你的测试套件更好一些。但事实远不止如此。让我们开始吧！

虽然我将提到 maxitest 作为我们的“测试套件”，但重要的是要记住 *minitest* 是我们测试背后的真正力量。Maxitest 只是 minitest 的一系列附件，使它看起来更容易。

## 应用程序

Rails 非常适合展示 maxitest 的所有优点，因为我们可以遇到各种形式的测试。出于我们的目的，我们将制作一个包含用户生成内容的新闻应用程序。在本文中，我们将使用测试驱动开发来利用 maxitest 的主要特性并设计我们的应用程序。

让我们开始行动吧:

```
$ rails new news_feed # such a creative title, I know
# ...
$ cd news_feed 
```

现在将 maxitest 和 [minitest-spec-rails](https://github.com/metaskills/minitest-spec-rails) 添加到您的 **Gemfile** 中。

```
group :development, :test do
  # ...
  gem 'maxitest'
  gem 'minitest-spec-rails'
end 
```

minitest-spec-rails gem 通过将`Minitest::Spec`扩展到 rails 自己的测试用例类中，消除了将`Minitest::Spec`与 Rails 集成的痛苦。你可以阅读更多关于[的自述](https://github.com/metaskills/minitest-spec-rails)。

首先，让我们看看我们的 **test_helper.rb** 并为 maxitest 添加一个 require。如果您要将它添加到现有的代码库，您只需将`mini`更改为`maxi`:

```
ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
require 'maxitest/autorun' # requires maxitest for test running
require 'rails/test_help'

class ActiveSupport::TestCase
  # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
  fixtures :all

  # Add more helper methods to be used by all tests here...
end 
```

现在让我们做一些东西来测试。对于基础知识，只需创建一个具有两个属性的用户模型:`first_name`和`last_name`:

```
$ rails g model user first_name:string last_name:string 
```

使用 TDD，我们将把这两个属性连接成一个完整的名称。所以，作为教条式的“测试第一”的人(或者至少希望我们是)，让我们写一个测试吧！

在 **test/models/user_test.rb** 中应该已经生成了一个测试文件。

```
# test/models/user_test.rb

class UserTest < ActiveSupport::TestCase
  describe '#name' do # our method
    it 'should concatenate first and last names' do
      user = User.new(first_name: 'Johnny', last_name: 'Appleseed')
      assert_equal user.name, "#{user.first_name} #{user.last_name}"
    end
  end
end 
```

类`UserTest`继承自`ActiveSupport::TestCase`，后者已经通过 minitest-spec-rails gem 将`Minitest::Spec::DSL`扩展到其中。然后我们用一个`describe`来*描述*我们正在测试的东西。我们用一个`it`块来描述这个方法的一个行为。在内部，测试创建了一个`User`的实例，然后确保`#name`确实返回了`first_name`和`last_name`的连接。

让我们运行测试套件:

```
$ rake test 
```

![test-suite-red-error](img/1bc121d81009447c2e4fd74dd58c7b3e.png)

耶！**红色。**现在让我们实际创建方法来获得**绿色**。

```
# app/models/user.rb

class User < ActiveRecord::Base
  def name
    "#{first_name} #{last_name}"
  end
end 
```

为了这次运行测试，我们将复制并粘贴 maxitest 给我们的代码片段，以便再次运行这个测试(逐行进行)。

```
# the -I option tells mtest to include the 'test' directory
mtest test/models/user_test.rb:5 -I test 
```

![test-suite-first-green](img/f8479255ae340afe2cfb2aebdcef65b1.png)

太好了！如你所见，maxitest 为成功的测试添加了令人满意的绿色。这个特性允许真正的**红绿重构**过程。说到这里，让我们稍微重构一下代码。我认为字符串插值可能不是编写代码的最佳方式(这是假设的；我没有偏好)，所以我将在两个字符串上使用`#join`:

```
# app/models/user.rb

class User < ActiveRecord::Base
  def name
    [first_name, last_name].join(' ')
  end
end 
```

让我们进行测试，以确保我们没有破坏任何东西……(`$ rake test`)我们没有。

## 让所有人

现在我们要对测试进行一点重构，展示一下 maxitest 的`let_all`方法。为了理解`let_all`，理解`let`本身做什么很重要。创建一个访问器方法，该方法被分配给传递给它的块。这与在`describe`块中定义方法没有什么不同。`let_all`是`let`功能的扩展，缓存被传递块的响应。这实际上为您的块创建了一个缓存的全局变量。

注意在 **user_test.rb** 中，我们使用变量赋值设置了`it`块。我们可以将其重构到`describe #name`块内部的`let_all`块中。这很实用，因为`describe #name`模块内的所有`it`模块可以依赖相同的设置数据，因此运行速度更快。

*我们新的重构过的`user_test.rb` :*

```
require 'test_helper'

class UserTest < ActiveSupport::TestCase
  describe '#name' do # our method
    let_all(:user) { User.new(first_name: 'Johnny', last_name: 'Appleseed') }

    it 'should concatenate first and last names' do
      assert_equal user.name, "#{user.first_name} #{user.last_name}"
    end
  end
end 
```

## 让！

另一方面，maxitest 增加了`let!`方法。`let!`确保传递给它的块在每次使用该方法时总是*被*评估。

## 隐性主语:永远不要使用它们。

作为当前代码的一个题外话，让我们看看 maxitest 的一个更古怪的特性:隐式主题。我强烈建议不要使用这个。永远不会。“隐含主题”是[神秘嘉宾](https://robots.thoughtbot.com/mystery-guest)的同义词，这使得代码的可维护性和有用性大大降低。虽然隐式主题可能会节省几秒钟的时间，对您很有用，但是当您或其他开发人员在五个月后将一个突破测试的特性添加到代码库中，而您却不知道测试验证了什么时，您将会感到遗憾。

因此，凭良心说，我不能证明 maxitest 隐含的主题特征。作者本人默认不包含隐含主语。他们本质上非常暴躁。

## 上下文块

我真的希望 minitest 拥有的一个东西是`context`块。在功能上，它们与`describe`块没有什么不同，但是在语义上它们是不同的。有时候使用`context`来编写测试的一部分(尤其是验收测试)更有意义。

Maxitest 用一句话实现了我的愿望:

```
# https://github.com/grosser/maxitest/blob/master/lib/maxitest/autorun.rb#L23

# ...

Minitest::Spec::DSL.send(:alias_method, :context, :describe)

# ... 
```

Context 没有 describe 更多的功能，但是它允许更具表达性和更简单的测试。为了演示上下文块的使用，我们将为帖子列表编写一个验收测试:

```
$ mkdir test/acceptance
$ touch test/acceptance/main_feed_test.rb 
```

测试驱动开发方法的一个伟大之处是它的设计方面。虽然 TDD 对于防止回归是很好的，但是它也允许你在写软件之前设计你的软件。验收测试是一个更高层次的测试，允许你在制作之前设计你的网站的功能。考虑到这一点，让我们为我们的应用程序的主要特性:新闻提要编写一个测试。

在我们的应用程序中，我们有能够发布文本的用户。在我们应用程序的主体中，我们有这些帖子的线索。我们不会添加任何复杂的东西，只是一个简单的 feed 特性。

为了建立这种权利，我们需要一个属于用户的`Post`模型。让我们生成:

```
$ rails g model Post user_id:integer title:string body:text 
```

现在，让我们为我们的帖子和用户添加一些验证和关联:

```
# app/models/post.rb
class Post < ActiveRecord::Base
  validates :title, :body, presence: true

  belongs_to :user
end

# app/models/user.rb
class User < ActiveRecord::Base
  validates :first_name, :last_name, presence: true

  has_many :posts

  # ...
end 
```

现在我们需要为我们的用户和帖子制作一些夹具数据。Rails 在**测试/夹具**中自动生成样本夹具，但是我们需要对它们进行一些定制。以下是我编的一些夹具数据，你可以随意修改:

```
# test/fixtures/users.yml
john:
  first_name: John
  last_name: Appleseed

jane:
  first_name: Jane
  last_name: Doe

# test/fixtures/posts.yml

one:
  user: john
  title: A Taste of Latin Filler Text
  body: Aenean pretium consectetur ex, at facilisis nisl tempor a. Vivamus feugiat sagittis felis. Quisque interdum, risus vel interdum mattis, ante neque vestibulum turpis, iaculis imperdiet neque mi at nisl. Vivamus mollis sit amet ligula eget faucibus. Vestibulum luctus risus nisi, et congue ante consectetur a. Quisque mattis accumsan efficitur. Curabitur porta dolor in nisi consectetur rhoncus. In quis libero scelerisque, sagittis neque at, porta tellus. Aenean metus est, tincidunt sed pellentesque id, aliquet a libero. Sed eget sodales nunc. Fusce lacinia congue felis at placerat.

two:
  user: jane
  title: Something Catchy
  body: I have nothing to say!

three:
  user: jane
  title: A Very Short Post
  body: This is witty and short. 
```

这些 YAML 文件将为我们的测试生成夹具，可以通过`model_plural_name(:fixture_name)`访问。现在我们有了固定装置，让我们设置验收测试环境。Rails 没有附带用于验收测试的测试用例类，所以我们必须自己创建一个:

```
 # test/test_helper.rb

ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
require 'maxitest/autorun'
require 'rails/test_help'
require 'capybara/rails'

class ActiveSupport::TestCase
  fixtures :all
end

class AcceptanceCase < ActiveSupport::TestCase
  include Rails.application.routes.url_helpers
  include Rack::Test::Methods
  include Capybara::DSL

  def app
    Rails.application
  end
end 
```

我们在这里将[水豚](https://github.com/jnicklas/capybara)添加到套件中，所以请确保将`gem 'capybara'`添加到您的 Gemfile 中。

我们创建的类(`AcceptanceCase`)为我们提供了设备、路线助手、机架测试助手和水豚。

现在让我们编写一个测试来开始设计我们的提要特性。

```
require 'test_helper'

class FeedTest < AcceptanceCase
  it 'should exist' do
    get root_path

    assert last_response.ok?, 'GET "/" does not respond OK'
  end
end 
```

现在让我们运行该套件，看看会发生什么:

```
1) Error:
FeedTest#test_0001_should exist:
ActionController::RoutingError: No route matches [GET] "/"
    test/acceptance/feed_test.rb:5:in `block in <class:FeedTest>' 
```

看起来我们没有路线。我们做一个吧。为了有一个路由，我们需要一个控制器，所以让我们生成一个带有`#index`动作的 posts 控制器:

```
$ rails g controller posts index 
```

该生成器还进行 posts 控制器测试。让我们也为那里的路线添加一个测试:

```
# test/controllers/posts_controller_test.rb

require 'test_helper'

class PostsControllerTest < ActionController::TestCase
  describe '#index' do
    it 'GET /' do
      response = get :index
      assert response.ok?
    end
  end
end 
```

让我们再次运行该套件，查看新的错误:

```
 1) Error:
FeedTest#test_0001_should exist:
ActionController::RoutingError: No route matches [GET] "/"
    test/acceptance/feed_test.rb:5:in `block in <class:FeedTest>'

  2) Error:
PostsControllerTest::#index#test_0001_GET /:
ActionController::UrlGenerationError: No route matches {:action=>"index", :controller=>"posts"}
    test/controllers/posts_controller_test.rb:6:in `block (2 levels) in <class:PostsControllerTest>' 
```

我们以前见过第一个错误，但第二个是新的；这是我们的岗位控制器测试。

既然我们已经有了测试覆盖率，也就有了需要实现什么的目标，我们可以写几行代码来让它们通过。

```
# config/routes.rb
Rails.application.routes.draw do
  root to: 'posts#index'
end

# app/controllers/posts_controller.rb
class PostsController < ApplicationController
  def index
  end
end

<!-- app/views/posts/index.html.erb -->

<h1>A Listing of Posts!</h1> 
```

4 行代码后，我们有 2 个新的通过测试！显然，我们现在除了一个工作路线和一个静态页面之外什么都没有，但是它给了我们一个稳定的框架来构建。现在我们可以构建真正的功能了。让我们回到 **feed_test.rb** 。

这里使用一个`context`模块来更好地描述不同*上下文*中的新特性。出于我们的目的，有两种上下文:有帖子和没有帖子。

```
require 'test_helper'

class FeedTest < AcceptanceCase
  it 'should exist' do
    get '/'
    assert last_response.ok?, 'GET "/" does not respond OK'
  end

  context 'with posts' do
    # posts are created before the test runs and can be found in the fixtures
    #
    # post #1 -> posts(:one)
    # post #2 -> posts(:two)
    # post #3 -> posts(:three)
    before do
      visit '/'
    end

    let_all(:posts) { page.all('.post') }

    it 'should list all posts' do
      assert_equal Post.count, posts.count
    end

    it 'should list all post titles' do
      assert_each_post_has 'title'
    end

    it 'should list all post bodies' do
      assert_each_post_has 'body'
    end

    it 'should list all post authors' do
      assert_each_post_has 'user.name'
    end
  end

  context 'without posts' do
    before do
      Post.delete_all

      visit '/'
    end

    it 'should say "There are no posts to be found"' do
      assert page.has_content? 'There are no posts to be found'
    end
  end

  private

  def assert_each_post_has(attribute)
    Post.all.each do |post|
      # The second argument is a message to display if the assertion fails.
      # When looping in a test, it's a best practice to ensure that identifying
      # loop information is returned on failure.
      assert page.has_content?(post.instance_eval(attribute)),
             "Post ##{post.id}'s #{attribute} is not displayed"
    end
  end
end 
```

代码很多，但是很容易理解；它读起来像英语。让我们运行这些测试，看看它们本身是否有足够的描述性，无需解释:

```
Run options: --seed 44175

# Running:

.F..FFFF

Finished in 0.257127s, 31.1131 runs/s, 31.1131 assertions/s.

  1) Failure:
FeedTest::post feed::without posts#test_0001_should say "There are no posts to be found" [/Users/jesse/code/examples/news_feed/test/acceptance/feed_test.rb:47]:
Failed assertion, no message given.

  2) Failure:
FeedTest::post feed::with posts#test_0004_should list all post authors [/Users/jesse/code/examples/news_feed/test/acceptance/feed_test.rb:35]:
Post #113629430's user.name is not displayed

  3) Failure:
FeedTest::post feed::with posts#test_0003_should list all post bodies [/Users/jesse/code/examples/news_feed/test/acceptance/feed_test.rb:31]:
Post #113629430's body is not displayed

  4) Failure:
FeedTest::post feed::with posts#test_0001_should list all posts [/Users/jesse/code/examples/news_feed/test/acceptance/feed_test.rb:23]:
Expected: 3
  Actual: 0

  5) Failure:
FeedTest::post feed::with posts#test_0002_should list all post titles [/Users/jesse/code/examples/news_feed/test/acceptance/feed_test.rb:27]:
Post #113629430's title is not displayed

8 runs, 8 assertions, 5 failures, 0 errors, 0 skips

Focus on failing tests:
mtest test/acceptance/feed_test.rb:46
mtest test/acceptance/feed_test.rb:34
mtest test/acceptance/feed_test.rb:30
mtest test/acceptance/feed_test.rb:22
mtest test/acceptance/feed_test.rb:26 
```

让我们来看看第一个失败:我立刻知道一篇文章的正文(具体来说是#113629430)没有显示在文章提要中。那我们来解决这个问题吧！我把这个列表看作是一个功能指南。它为我提供了完成一个功能的渐进步骤；这太好了！我们*只是*会先修复第一个测试，然后我们会修复其余的。

```
# app/controllers/posts_controller.rb

class PostsController < ApplicationController
  def index
    @posts = Post.all
  end
end

<% # app/views/posts/index.html.erb %>

<h1>A Listing of Posts!</h1>

<ul>
<% @posts.each do |post| %>
  <li><%= post.title %></li>
<% end %>
</ul> 
```

就是这样！现在让我们试试测试… ( `mtest test/acceptance/feed_test.rb:26 -I test` ) **成功！**这很简单，让我们来解决剩下的问题。测试对于允许增量的特性构建是非常好的。要修复接下来的测试，我们所要做的就是查看一个错误，修复它，然后修复下一个。

这是最终产品！

```
<% # app/views/posts/index.html.erb %>

<h1>A Listing of Posts!</h1>

<% if @posts.empty? %>
<p>There are no posts to be found!</p>
<% else %>
<ul>
  <% @posts.each do |post| %>
    <li class="post">
      <%= post.title %>
      <ul>
        <li><strong>Author: </strong> <%= post.user.name %></li>
        <li><em><%= post.body %></em></li>
      </ul>
    </li>
  <% end %>
</ul>
<% end %> 
```

显然，这还不是一个完整的特性，但是我们从 maxitest 中获得了 TDD 的核心和很好的补充。

## 结论

我只是浏览了一个庞大的哲学和一个伟大工具的表面。Minitest 和 maxitest 是一个强大而轻量级的组合，用于在 Ruby 应用程序中实现测试驱动开发。下一次你开始一个新项目的时候，仔细考虑 RSpec，并考虑尝试一下 minitest 和 maxitest。

对更多测试主题感兴趣？我很想听听！欢迎在下方留言评论。

## 分享这篇文章