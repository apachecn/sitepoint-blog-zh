# 了解 Rails 和 RSpec 的第一批最佳实践

> 原文：<https://www.sitepoint.com/learn-the-first-best-practices-for-rails-and-rspec/>

*这篇文章由[汤姆·帕金](https://www.sitepoint.com/author/tparkin)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

![](img/118ff59b14fe4f82f83e175873619821.png)

不经测试就编写代码可能看似一帆风顺；不幸的是，当你开始和其他开发人员一起工作时，事情就嘎然而止了。本教程旨在帮助 Ruby on Rails 开发人员(尤其是新手)使用[最佳实践](https://betterspecs.org)以行为驱动的开发方式建立 [RSpec](https://github.com/rspec/rspec) 测试套件。我知道当你刚开始做测试时，测试可能会很痛苦。在这个旅程中，网上有很多资源可以帮助你。我希望这对 Ruby on Rails 的新开发人员有所帮助，因为我刚刚开始测试自己。

在本教程中，我们将学习以下内容:
*如何设置 RSpec、Capybara、Shoulda-Matchers、数据库清理器
*如何使用 Factory Girl Rails 和 Faker
创建工厂*如何编写模型规格
*如何编写控制器规格
*如何编写特性规格
*如何使用 SimpleCov 检查规格覆盖率

## Rails 应用

```
rails new myapp 
```

## 安装 RSpec

打开你的**宝石文件**，将`rspec-rails`宝石添加到`development`和`test`组中:

```
group :development, :test do
  gem 'byebug'
  gem 'rspec-rails', '~> 3.4'
end 
```

安装 gems:

```
bundle install 
```

现在运行:

```
rails generate rspec:install 
```

这将添加 spec 目录和一些框架文件，包括一个**。rspec** 文件。在继续之前，继续删除由我们的 Rails 应用程序生成的**测试**目录。

## should-Matchers 和数据库清理器

打开你的**宝石文件**，添加一个包含`shoulda-matcher`和`database_cleaner`宝石的`test`组:

***Gemfile***

```
group :test do
  gem 'shoulda-matchers', '~> 3.0', require: false
  gem 'database_cleaner', '~> 1.5'
end 
```

运行`bundle install`。

### Shoulda-Matchers 配置

Shoulda-Matchers 为 RSpec 提供了一行匹配器，用于测试 Rails 的功能，我们将简要介绍。打开您的 **spec/rails_helper.rb** 文件，并通过粘贴以下内容来配置`shoulda-matchers`以使用 RSpec:

```
require 'shoulda/matchers'

Shoulda::Matchers.configure do |config|
  config.integrate do |with|
    with.test_framework :rspec
    with.library :rails
  end
end 
```

### 数据库清理器配置

数据库清理器是一套在测试运行之间清理 Ruby 数据库的策略。它有助于确保测试的清洁状态。

为了整合`database_cleaner`，对 **spec/rails_helper.rb** 做如下调整:

```
config.use_transactional_fixtures = false 
```

在您的 spec 目录中创建一个名为 **support** 的新目录:

```
mkdir spec/support 
```

在其中，创建一个新文件 **database_cleaner.rb** ，并粘贴以下内容:

```
RSpec.configure do |config|

  config.before(:suite) do
    DatabaseCleaner.clean_with(:truncation)
  end

  config.before(:each) do
    DatabaseCleaner.strategy = :transaction
  end

  config.before(:each, :js => true) do
    DatabaseCleaner.strategy = :truncation
  end

  config.before(:each) do
    DatabaseCleaner.start
  end

  config.after(:each) do
    DatabaseCleaner.clean
  end
end 
```

这样，`database_cleaner`就建立起来了，它将在每个单元测试和测试套件之间清理数据库。

## 水豚设置

Capybara 是一个自动化框架，用于创建模拟用户如何与您的应用程序交互的功能测试。将`capybara`宝石添加到**宝石文件**中的`:development, :test`组，也就是你添加`rspec-rails`的那个组:

```
gem 'capybara', '~> 2.5' 
```

还有`bundle install`。

打开你的 **spec_helper.rb** 并要求水豚宝石:

```
***spec/spec_helper.rb***
...
require 'capybara/rspec' 
```

## 伪造者和工厂女孩设置

Faker 在为您的测试生成随机数据时非常有用。你将看到如何在制造工厂中使用它。将`faker`宝石添加到**宝石文件**中的`:test`组中:

```
gem 'faker', '~> 1.6.1' 
```

Factory Girl 允许您创建测试中需要的对象，这些对象可以包含默认值。使用 faker，您将能够为您的测试创建随机对象，而不是只使用一个默认值。

将`factory_girl_rails`宝石添加到`:development, :test`组:

```
gem 'factory_girl_rails', '~> 4.5.0' 
```

然后`bundle install`。

此时，您的 **Gemfile** 应该是这样的:

```
source 'https://rubygems.org'

gem 'rails', '4.2.4'
gem 'sqlite3'
gem 'sass-rails', '~> 5.0'
gem 'uglifier', '>= 1.3.0'
gem 'coffee-rails', '~> 4.1.0'
gem 'jquery-rails'
gem 'turbolinks'
gem 'jbuilder', '~> 2.0'
gem 'sdoc', '~> 0.4.0', group: :doc

group :development, :test do
  gem 'byebug'
  gem 'rspec-rails', '~> 3.4'
  gem 'factory_girl_rails', '~> 4.5'
  gem 'capybara', '~> 2.5'
end

group :development do
  gem 'web-console', '~> 2.0'
  gem 'spring'
end

group :test do
  gem 'shoulda-matchers', '~> 3.0', require: false
  gem 'database_cleaner', '~> 1.5'
  gem 'faker', '~> 1.6.1'
end 
```

### 创建工厂

在您的 **spec** 文件夹中创建一个名为 **factories** 的目录，同时添加一个以您的型号命名的文件。例如**联系人. rb**

```
***spec/factories/contacts.rb***

FactoryGirl.define do
  factory :contact do
    full_name     { Faker::Name.name }
    email         { Faker::Internet.email }
    phone_number  { Faker::PhoneNumber.phone_number }
    address       { Faker::Address.street_address }
  end
end 
```

在上面，我刚刚向你展示了如何使用`factory_girl_rails`和`faker`创建夹具。您不必显式输入对象。工厂女孩使用由`faker`生成的随机(假)值来创建工厂，这些工厂将在您运行测试时使用。

## 型号规格

我们希望确保我们在上面创建的工厂对模型有效。在您的**模型/规格**文件夹中为您的模型创建一个文件:

```
***spec/models/contact_spec.rb***

require 'rails_helper'

RSpec.describe Contact, type: :model do
  it "has a valid factory" do
    expect(contact).to be_valid
  end
end 
```

从您的终端运行:

```
rspec spec/models/contact_spec.rb 
```

这将显示一个错误，因为您没有`Contact`型号。转到终端，创建模型，并迁移您的数据库:

```
rails g model Contact full_name:string email:string phone_number:integer address:text
rake db:migrate 
```

再次运行规范和维奥拉！应该会过去:)

我们将使用`shoulda-matchers`来验证`full name`、`email`、`phone number`和`address`的存在，从而结束模型测试。在您的触点型号规格中，创建一个新的`describe`块:

```
***spec/models/contact_spec.rb***

  describe Contact do
    it { is_expected.to validate_presence_of(:full_name) }
    it { is_expected.to validate_presence_of(:email) }
    it { is_expected.to validate_presence_of(:phone_number) }
    it { is_expected.to validate_presence_of(:address) }
  end 
```

运行你的 spec，看着它失败。要让它通过，请转到模型并实现验证代码:

```
***app/model/contact.rb***

class Contact < ActiveRecord::Base
  validates_presence_of :full_name, :email, :phone_number, :address
end 
```

再次运行规范，它将通过。在讨论控制器规格之前，我想解释一下我们上面使用的一些术语。

## RSpec DSL 片段

RSpec 是一种 DSL，用于创建代码预期行为的可执行示例，并组织成组。

`describe`创建一个示例组。它需要一个参数来说明规范是关于什么的。参数可以是类、模块、方法或字符串描述。

`it`创建一个示例并获取该示例的描述。(举例；it `has status code 400`)。将规格说明限制在 40 个字符以内是[的最佳实践](https://betterspecs.org/#short)。如果需要更长，您可能应该考虑使用一个`context`来创建一个`describe`块的“子上下文”。

`expect`让您表达示例中某个对象的预期结果。它取一个物体或木块，与`to`或`not_to`以及[匹配器](https://www.relishapp.com/rspec/rspec-expectations/docs/built-in-matchers)一起使用(例如`eq()`、`be_valid`)。虽然有许多内置的匹配器，但像 Shoulda Matchers 这样的 gems 甚至添加了更多，使您的规范更具表现力和简洁。

## 控制器规格

第一个控制器规范将测试是否创建了具有有效属性的新联系人。打开您的终端并生成控制器:

```
rails g controller Contacts 
```

这将自动为您的控制器规格生成必要的文件夹。你可以在**规格/控制器**看到它们。

是时候编写规范来测试`create`动作了:

```
***spec/controllers/contacts_controller_spec.rb***

require 'rails_helper'

RSpec.describe ContactsController, type: :controller do

  describe "POST #create" do
    context "with valid attributes" do
      it "create new contact" do
        post :create, contact: attributes_for(:contact)
        expect(Contact.count).to eq(1)
      end
    end
  end
end 
```

运行规格:

```
rspec spec/controllers/contacts_controller_spec.rb 
```

这将失败，因为您没有与控制器匹配的路由。打开 routes 文件并将其放入:

```
***config/routes.rb***
...
resources :contacts 
```

再次运行您的规范，它仍然会失败，并出现错误`The action 'create' could not be found for ContactsController`。

打开你的控制器，输入必要的代码来创建一个新的联系人。

```
***app/controllers/contacts_controller.rb***

class ContactsController < ApplicationController
  def new
    @contact = Contact.new
  end

  def create
    @contact = Contact.new(contact_params)

    respond_to do |format|
      if @contact.save
        format.html { redirect_to @contact }
        format.json { render :show, status: :created, location: @contact }
      else
        format.html { render :new }
        format.json { render json: @contact.errors, status: :unprocessable_entity}
      end
    end
  end

  private

  def contact_params
    params.require(:contact).permit(:full_name, :email, :phone_number, :address)
  end
end 
```

保存并再次运行规范，它应该会通过。

按照上面的规范，编写一个使用无效属性创建新联系人的规范。该规范应检查是否创建了联系人:

```
***spec/controllers/contacts_controller_spec.rb***

context "with invalid attributes" do
  it "does not create a new contact" do
    post :create, contact: attributes_for(:invalid_contact)
      expect(Contact.count).to eq(0)
    end
  end
end 
```

以您上面写的规范为例，您可以为其他控制器操作编写规范。

## 功能规格

功能规格是贯穿应用程序的高级测试，确保每个组件都能正常工作。它们通常是从用户的角度编写的。

出于本教程的目的，您将编写一个规范来测试新联系人的创建。使用 capybara gem，规格将填写具有有效属性的表格，并测试显示页面在创建联系人时显示预期文本。

打开终端或文本编辑器，按照以下路径创建文件夹和文件:

```
spec/features/contacts/create_spec.rb 
```

将以下代码粘贴到该文件中:

```
***spec/features/contacts/create_spec.rb***
require 'rails_helper'

RSpec.feature "Contact", :type => :feature do
  scenario "Create a new contact" do
    visit "/contacts/new"

    fill_in "Full name", :with => "My Name"
    fill_in "Email", :with => "my@email.com"
    fill_in "Phone number", :with => "123456789"
    fill_in "Address", :with => "34, Allen Way, OA"

    click_button "Create Contact"

    expect(page).to have_text("My Name")
  end
end 
```

上面的规范测试显示页面是否有文本`My Name`，它是写入`full name`输入的值。运行这个规范应该会失败。

首先给你的控制器添加一个`show`动作:

```
***app/controllers/contacts_controller.rb

def show
  @contact = Contact.find(params[:id])
end 
```

在您的联系人视图中创建以下文件: **new.html.erb** 、 **_form.html.erb** 、 **show.html.erb** ，并填写以下代码:

```
***app/views/contacts/_form.html.erb***

<%= form_for(@contact) do |f| %>
  <% if @contact.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@contact.errors.count, "error") %> prohibited this contact from being saved:</h2>

      <ul>
      <% @contact.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= f.label :full_name %><br>
    <%= f.text_field :full_name %>
  </div>
  <div class="field">
    <%= f.label :email %><br>
    <%= f.text_area :email %>
  </div>
  <div class="field">
    <%= f.label :phone_number %><br>
    <%= f.text_area :phone_number %>
  </div>
  <div class="field">
    <%= f.label :address %><br>
    <%= f.text_area :address %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>

***app/views/contacts/new.html.erb***

<h2>Create new contact</h2>

<%= render 'form' %>

***app/views/contacts/show.html.erb***

<p id="notice"><%= notice %></p>

<p>
  <strong>Full Name:</strong>
  <%= @contact.full_name %>
</p>

<p>
  <strong>Email:</strong>
  <%= @contact.email %>
</p>

<p>
  <strong>Phone Number:</strong>
  <%= @contact.phone_number %>
</p>

<p>
  <strong>Address:</strong>
  <%= @contact.address %>
</p> 
```

运行规范，这次应该会通过。

### 使用 SimpleCov 的覆盖率

SimpleCov gem 是 Ruby 的一个代码覆盖分析工具。您将在应用程序中使用它来查看测试了多少代码。要安装，打开 **Gemfile** 并添加到`test`组:

```
gem 'simplecov', :require => false 
```

运行`bundle install`。

下一次打开你的规格助手是必需的`simplecov`

```
require 'simplecov'
SimpleCov.start 
```

下次运行规范时，将会生成一个名为 **coverage** 的新文件夹。打开浏览器，指向:**your-app-directory/coverage/index . html**查看应用的覆盖率统计数据。您需要将**覆盖文件夹**添加到**中。gitignore** 文件，这样它就不会被添加到您的远程存储库中。

## 结论

本文旨在为您提供为 Rails 应用程序设置 RSpec 的基础。这些是基本步骤，从这里开始，您将能够探索 RSpec 的大量语法，并开始细化您的规范。此时，您应该能够:

*   为您的应用程序设置一个测试套件。
*   编写型号、控制器和功能规格。

这里有更多的资源可以帮助你:

*   [SitePoint RSpec 集合](https://www.sitepoint.com/premium/collections/rspec)
*   [更好的光谱](https://betterspecs.org/)
*   [Ruby 工具箱](https://www.ruby-toolbox.com/#Testing)

通过更多的练习，你很快就能熟练地测试你的 Rails 应用程序。

## 分享这篇文章