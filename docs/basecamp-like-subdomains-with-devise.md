# 带有 Devise 的类似 Basecamp 的子域

> 原文：<https://www.sitepoint.com/basecamp-like-subdomains-with-devise/>

为应用程序构建身份验证是我们都曾遇到过的普通任务。在过去，大多数开发人员会从他们的工具带中拿出 [restful 认证](https://github.com/technoweenie/restful-authentication)。最近，在身份验证方面，一个新成员抢了不少风头，而且理由很充分。

[设计](https://github.com/plataformatec/devise)提供完整的认证解决方案。视图、邮件和一系列常见的身份验证助手(如注册、通过电子邮件确认)以及锁定帐户的能力都是现成的标准功能。

开发自己的身份验证系统是一个很好的练习，而且在大多数情况下，这并不是一件很麻烦的事情。拥有像 Devise 这样稳定、功能丰富、模块化的产品可以节省大量时间。

今天，我们将看看如何在现实世界中实施 Devise。虽然 Devise 的基本特性是众所周知的，但是它的灵活性却被忽略了。我们将看看如何定制用户登录。

如今，多租户应用程序中常见的习语是将子域定义为用户帐户，例如`rubysource.basecamp.com`可以是 Rubysource 的虚构 Basecamp 登录。让我们用设计来做这件事。

## 应用程序

我们将首先从应用程序的本质开始。这将是一个基于 Rails 3.1 的笔记应用程序。在这个阶段，我们将非常简单，在我们的领域中只有三个模型，`Account`、`Note`和`User`。让我们为`Account`和`Note`发动一些发电机，把`User`留给设计。

```
rails g model Account subdomain:string
rails g scaffold Note title:string content:text user_id:integer account_id:integer
```

在我们的`Account`模型中，我们将创建关联，

```
class Account < ActiveRecord::Base
  has_many :users
  has_many :notes, :through => :users
end
```

类似地，对于`Note`,我们如下建立关联。

```
class Note > ActiveRecord::Base
  belongs_to :user
  belongs_to :account
end
```

这里没什么特别的，只是几个有关联的`ActiveRecord`模型。唯一感兴趣的是`Account`模型上的`subdomain`属性。这实际上是请求 URL 中帐户的 slug(在我们前面的例子中是“rubysource”)。

## 引入设备

设计是一个宝石，所以很直截了当地安装。将 Devise 添加到您的 Gemfile，`gem devise`然后运行`bundle install`。

现在在命令行上运行`rails g`并检查添加到我们的应用程序中的生成器。

```
Devise:
  devise
  devise:form_for
  devise:install
  devise:shared_views
  devise:simple_form_for
  devise:views
```

我们将使用明显的`rails g devise:install`让 Devise 在我们的应用程序中运行。

在运行生成器之后，我们应该得到几条指令，告诉我们将应用程序的根设置为映射到某个地方，在我们的布局中有一个 flash 区域，并在我们的开发和测试环境中设置默认邮件程序。严格按照这些去做。我已经将应用程序的根目录设置为指向`notes#index`。

我们需要一个用户模型来验证设备。事实证明，我们可以使用 devise 来为我们生成那个。这将在我们的`config/routes.rb`文件中生成模型、相应的迁移和专门的`devise_for`路径。

在运行迁移之前，我们需要修改`User`模型来关联`Account`。我们也把`Note`联想成`has_many`。

我应该指出，这是决定 Devise 将使用什么模块的最佳时机，比如将`:confirmable`添加到迁移中，但是现在我们将只使用生成器给我们的缺省值。

安装了 Devise 后，保护我们内容的最简单方法是将毯子`before_filter :authenticate_user!`放在`ApplicationController`中。启动服务器并导航至`localhost:3000`，显示通用设备登录表单。

## 知道它有效

现在我们已经设计好了，但它还没有做我们想要做事情。我们需要根据子域进行身份验证。在测试方面有几个选项。诚然，我们真的不想测试设计本身。这是一个第三方的、被广泛使用的软件，所以可以说它已经过很好的测试。然而，我们仍然希望确保我们的定制行为符合我们的预期。我们可以做一些启动本地服务器的工作，并使用出色的`lvh.me`域，这样我们就不必不断地向我们的 hosts 文件添加子域。

但是我们不是比那更好吗？我们需要可重复的自动化测试。让我们使用我们的集成测试和令人惊叹的[水豚](https://github.com/jnicklas/capybara)。默认情况下，水豚在域`www.example.com`下运行我们的套件。然而，通过简单地使用`app_host`助手，我们可以显式地指定测试运行的域。下面是我们使用 [RSpec 请求](https://www.relishapp.com/rspec/rspec-rails/docs/request-specs/request-spec)寻找的行为的基本测试。

```
describe "LoginToAccounts" do
  before do
    other_account = Factory.create(:account)
    @invalid_user = Factory.create(:user, account: other_account)
    @account = Factory.create(:account, subdomain: "test-account")
    @user = Factory.create(:user, account: @account)
    Capybara.app_host = "http://test-account.example.com"
    visit '/'
  end

  describe "log in to a valid account" do
    before do
      fill_in 'Email', with: @user.email
      fill_in 'Password', with: @user.password
      click_button 'Sign in'
    end

    it "will notify me that I have logged in successfully" do
      page.should have_content "Signed in successfully"
    end

  end

  describe "fail login for valid user wrong account" do
    before do
      fill_in 'Email', with: @invalid_user.email
      fill_in 'Password', with: @invalid_user.password
      click_button 'Sign in'
    end

    it "will not notify me that I have logged in successfully" do
      page.should_not have_content "Signed in successfully"
    end

  end
end
```

## 设计灵活性

现在我们已经有了一个测试，是时候实现捕获子域并确保用户验证到正确的帐户的功能了。

当我们安装 Devise 时，它生成一个初始化器，在`config/initializers`目录中恰当地命名为`devise.rb`。这个文件被很好地注释了，并且应该是在 Devise 的普通安装中添加功能的第一步。

您将在该文件的第 33 行看到(基于安装 Devise 的 1.4.7 版),我们可以添加用于认证的请求密钥。只需取消该行的注释并将`:subdomain`添加到键数组中。这将基本上推动这个请求参数来设计要测试的认证方法。

尽管这很简单，但我们还没到那一步。请记住，Devise 在`User`上进行认证，子域属性在`Account`上。现在，我们可以简单地给 user 添加一个子域属性就可以了，但是在我看来这是完全错误的。

相反，我们将覆盖 Devise 使用的`find_for_authentication`方法。在 Devise 接管之前，这个方法是我们的交接点。为了找到我们正在寻找的帐户，我们将从请求中获取子域，并沿链向上传递以进行身份验证。使用 Rails 3+使这变得非常容易。在`User`型号中简单添加

```
def self.find_for_authentication(conditions={})
    conditions[:account_id] = Account.find_by_subdomain(conditions.delete(:subdomain)).id
    super(conditions)
  end
```

在这里，我们弹出`subdomain`参数，在`Account`上进行查找，并将其传递给`find_for_authentication`方法。

有了所有这些，我们可以运行测试，并确保我们有一个合格的套件。演示代码可以在 [Github](https://github.com/bangline/Devise-Demo) 上找到。

## 接下来去哪里？

我们天真的小应用程序有身份验证，没有未经授权的用户可以访问我们所有重要的笔记。关于为什么登录显示什么注释，我们还有一些工作要做。这可以通过常见的多租户习语来解决，即让控制器中的所有数据都从“当前租户”中提取出来，就像 DHH 所做的那样。

这不是我们今天在这里看到的。通常，我会避免测试第三方代码，但我们的应用程序的行为和我们所做的更改要求我们至少要脱帽设计。将这包装成一个集成测试是这样做的最好地方，使用像 Capybara 这样的工具使它变得容易，所以不彻底是犯罪。

对于 Rails 应用程序来说，身份验证是一种我们会反复使用的模式，像 Devise 这样功能丰富的东西可能会让人望而生畏。因此，为什么有些人期待更简单的宝石，如 [Restful 认证](https://github.com/technoweenie/restful-authentication)、[魔法](https://github.com/NoamB/sorcery)或只是推出我们自己的。我建议你花些时间设计。有了很好的理解，您几乎可以将身份验证元素从您的应用程序中删除，并使用 Devise 作为替代。它的设计太棒了，就我个人而言，我还没有发现自己处于与设计对抗的境地。这通常是一个挂钩到它的情况下，以创造正是我所需要的。

## 分享这篇文章