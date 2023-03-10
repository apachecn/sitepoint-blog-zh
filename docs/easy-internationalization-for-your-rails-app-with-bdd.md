# 使用 BDD 轻松实现 Rails 应用程序的国际化

> 原文：<https://www.sitepoint.com/easy-internationalization-for-your-rails-app-with-bdd/>

我的公司有一个美国格式的 web 应用程序，我们已经对其进行了修改，供我们的英国分公司使用。然而，我们目前需要进一步修改它，并添加一些功能，这导致我们走上了完全重写的道路。但我相信 Rails 已经扭转了局面。我将向您展示用 Rails“国际化”是多么容易。这将是一系列后续测试中的第一个，包括创建、读取、使用和删除(CRUD)。

我们将从创建一个 Rails 应用程序开始。我喜欢用黄瓜和 factory_girl 来测试，所以我们不需要测试单元。

```
$ rails new international --skip-test-unit
...
$ cd international
international $
```

打开包含我们新应用的文件夹，编辑 gem 文件。我们需要添加测试宝石。

```
group :test do
  gem 'cucumber-rails', '1.2.1'
  gem 'rspec-rails', '2.8.1'
  gem 'database<em>cleaner', '0.7.1'
 gem 'factory</em>girl', '2.4.0'
end
```

保存文件。现在运行 bundler 来安装新的 gems。

```
international $ bundle install
Fetching source index for http://rubygems.org/
Using rake (0.9.2.2)
Using multi<em>json (1.0.4)
Using activesupport (3.1.3)
Using builder (3.0.0)
Using i18n (0.6.0)
Using activemodel (3.1.3)
Using erubis (2.7.0)
Using rack (1.3.6)
Using rack-cache (1.1)
Using rack-mount (0.8.3)
Using rack-test (0.6.1)
Using hike (1.2.1)
Using tilt (1.3.3)
Using sprockets (2.0.3)
Using actionpack (3.1.3)
Using mime-types (1.17.2)
Using polyglot (0.3.3)
Using treetop (1.4.10)
Using mail (2.3.0)
Using actionmailer (3.1.3)
Using arel (2.2.1)
Using tzinfo (0.3.31)
Using activerecord (3.1.3)
Using activeresource (3.1.3)
Using bundler (1.0.21)
Installing nokogiri (1.5.0) with native extensions
Installing ffi (1.0.11) with native extensions
Installing childprocess (0.3.0)
Installing rubyzip (0.9.5)
Installing selenium-webdriver (2.17.0)
Installing xpath (0.1.4)
Installing capybara (1.1.2)
Using coffee-script-source (1.2.0)
Using execjs (1.2.13)
Using coffee-script (2.2.0)
Using rack-ssl (1.3.2)
Using json (1.6.5)
Using rdoc (3.12)
Using thor (0.14.6)
Using railties (3.1.3)
Using coffee-rails (3.1.1)
Installing diff-lcs (1.1.3)
Installing gherkin (2.7.3) with native extensions
Installing term-ansicolor (1.0.7)
Installing cucumber (1.1.4)
Installing cucumber-rails (1.2.1)
Installing database</em>cleaner (0.7.1)
Installing factory_girl (2.4.0)
Using jquery-rails (1.0.19)
Using rails (3.1.3)
Installing rspec-core (2.8.0)
Installing rspec-expectations (2.8.0)
Installing rspec-mocks (2.8.0)
Installing rspec (2.8.0)
Installing rspec-rails (2.8.1)
Using sass (3.1.12)
Using sass-rails (3.1.5)
Using sqlite3 (1.3.5)
Using uglifier (1.2.2)
Your bundle is complete! Use <code>bundle show [gemname]</code> to see where a bundled gem is installed.
```

随着新宝石的加载，我们可以安装黄瓜。

```
international $ rails generate cucumber:install
create config/cucumber.yml
create script/cucumber
 chmod script/cucumber
create features/step_definitions
create features/support
create features/support/env.rb
 exist lib/tasks
create lib/tasks/cucumber.rake
  gsub config/database.yml
  gsub config/database.yml
 force config/database.yml
```

为了让 cucumber 工作，我们需要创建数据库，即使我们没有模型。以下命令将完成此操作:

```
international $ rake db:migrate db:test:prepare
```

让我们看看它是否有效。继续运行黄瓜:

```
international $ cucumber
Using the default profile...
0 scenarios
0 steps
0m0.000s
```

它工作了。

现在我们可以创建我们的第一个测试了。在功能文件夹中创建一个名为 manage_locations.feature 的新文件

```
Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location.
    Given there is a location named "location 1"
    When I am on the locations page
    Then I should see "location 1"
```

保存文件，让我们运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/manage</em>locations.feature:7
      Undefined step: "there is a location named "location 1"" (Cucumber::Undefined)
      features/manage<em>locations.feature:7:in <code>Given there is a location named "location 1"'
 When I am on the locations page # features/manage_locations.feature:8
 Undefined step: "I am on the locations page" (Cucumber::Undefined)
 features/manage_locations.feature:8:in</code>When I am on the locations page'
 Then I should see "location 1" # features/manage</em>locations.feature:9
      Undefined step: "I should see "location 1"" (Cucumber::Undefined)
      features/manage_locations.feature:9:in `Then I should see "location 1"'
  1 scenario (1 undefined)
  3 steps (3 undefined)
  0m1.745s
You can implement step definitions for undefined steps with these snippets:
Given /^there is a location named "([^"]<em>)"$/ do |arg1|
 pending # express the regexp above with the code you wish you had
end
When /^I am on the locations page$/ do
 pending # express the regexp above with the code you wish you had
end
Then /^I should see "([^"]</em>)"$/ do |arg1|
 pending # express the regexp above with the code you wish you had
end
```

就像 Cucumber 说的，我们将使用它给我们的模板实现这些步骤。

在/features/step_definitions 文件夹中创建一个名为 location_steps.rb 的新文件，将这些代码片段复制并粘贴到我们刚刚创建的文件中。

```
Given /^there is a location named "([^"]<em>)"$/ do |arg1|
 pending # express the regexp above with the code you wish you had
end
When /^I am on the locations page$/ do
 pending # express the regexp above with the code you wish you had
end
Then /^I should see "([^"]</em>)"$/ do |arg1|
  pending # express the regexp above with the code you wish you had
end
```

让我们保存文件并重新运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 TODO (Cucumber::Pending)
 ./features/step</em>definitions/location<em>steps.rb:2:in <code>/^there is a location named "([^"]*)"$/'
 features/manage_locations.feature:7:in</code>Given there is a location named "location 1"'
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 Then I should see "location 1" # features/step</em>definitions/location_steps.rb:9
  1 scenario (1 pending)
  3 steps (2 skipped, 1 pending)
  0m1.249s
```

正如所料，它没有通过，但有一点点的噪音。我们需要创建一个位置模型，这样我们就可以有位置。使用生成器，创建一个，现在，它只有一个名称属性。

```
international $ rails g model location name:string
invoke active<em>record
create db/migrate/20120118214355</em>create_locations.rb
create   app/models/location.rb
```

随着模型的创建，运行迁移和准备测试数据库的时间到了。

```
international $ rake db:migrate db:test:prepare
==  CreateLocations: migrating ================================================
-- create_table(:locations)
   -> 0.0015s
==  CreateLocations: migrated (0.0016s) =======================================
```

现在我们可以创建一个位置。

我喜欢用 factory_girl。它使得创建测试数据变得容易。([了解更多](http://rubygems.org/gems/factory_girl.))设置需要一点时间，但我觉得值得。

在/features/support 文件夹中，创建一个名为 factories.rb 的文件，并添加以下内容。

```
require 'factory_girl'
FactoryGirl.define do
  factory :location do |f|
    f.name 'test location'
  end
end
```

这将在数据库中创建一个名为“test location”的位置，用于测试目的。您总是可以向工厂传递一个名称，因此它不必是“测试”位置。“我们很快就会看到这样的例子。

在 location_steps.rb 文件中，修改第一个步骤，如下所示:

```
Given /^there is a location named "([^"]*)"$/ do |name|
  Factory(:location, :name => name)
end
```

保存文件并重新运行测试

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 TODO (Cucumber::Pending)
 ./features/step</em>definitions/location<em>steps.rb:6:in <code>/^I am on the locations page$/'
 features/manage_locations.feature:8:in</code>When I am on the locations page'
 Then I should see "location 1" # features/step</em>definitions/location_steps.rb:9
  1 scenario (1 pending)
  3 steps (1 skipped, 1 pending, 1 passed)
  0m1.485s
```

很好…第一步是通过。

接下来，我们将转到位置页面，但我们需要为此创建一条路线。

打开 config 文件夹中的 routes.rb 文件并添加

```
match 'locations/' => 'locations#index', :as => :locations
```

这将把我们带到位置索引页面。我们可以通过在命令行中键入以下命令来检查路由:

```
international $ rake routes
locations /locations(.:format) locations#index
```

在 location_steps.rb 文件中，通过添加以下内容来告诉我们该去哪里:

```
When /^I am on the locations page$/ do
  visit(locations_path)
end
```

保存文件并重新运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
uninitialized constant LocationController (ActionController::RoutingError)
 ./features/step</em>definitions/location<em>steps.rb:6:in <code>/^I am on the locations page$/'
 features/manage_locations.feature:8:in</code>When I am on the locations page'
 Then I should see "location 1" # features/step</em>definitions/location<em>steps.rb:9
 Failing Scenarios:
 cucumber features/manage</em>locations.feature:6 # Scenario: List a location.
  1 scenario (1 failed)
  3 steps (1 failed, 1 skipped, 1 passed)
  0m1.501s
```

它告诉我们没有位置控制器。好了，创建一个，现在只需要担心索引页面。

```
international $ rails g controller locations index
 create app/controllers/locations<em>controller.rb
 route get "locations/index"
 invoke erb
 create app/views/locations
 create app/views/locations/index.html.erb
 invoke helper
 create app/helpers/locations</em>helper.rb
 invoke assets
 invoke   coffee
 create     app/assets/javascripts/locations.js.coffee
 invoke   scss
 create     app/assets/stylesheets/locations.css.scss
```

让我们进入 routes 文件(config/routes.rb ),删除生成控制器时创建的路由。

去掉这条线。

```
get "locations/index"
```

保存文件。现在我们有了一个控制器，让我们重新运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 Then I should see "location 1" # features/step</em>definitions/location<em>steps.rb:9
 TODO (Cucumber::Pending)
 ./features/step</em>definitions/location_steps.rb:10:in <code>/^I should see "([^"]*)"$/'
 features/manage_locations.feature:9:in</code>Then I should see "location 1"'
  1 scenario (1 pending)
  3 steps (1 pending, 2 passed)
  0m1.858s
```

完美。我们只需要显示地点的名称。在/app/views/locations 文件夹中，打开 index.html.erb 文件。我们将添加这段代码，它将循环遍历这些名称:

```
<ul>
  <% @locations.each do |location| %>
    <li>
      <%= location.name %>
    </li>
  <% end %>
</ul>
```

让我们看看如果现在运行测试会发生什么。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 You have a nil object when you didn't expect it!
 You might have expected an instance of Array.
 The error occurred while evaluating nil.each (ActionView::Template::Error)
 ......
 features/manage</em>locations.feature:8:in `When I am on the locations page'
    Then I should see "location 1" # features/step<em>definitions/location</em>steps.rb:9
Failing Scenarios:
cucumber features/manage_locations.feature:6 # Scenario: List a location.
1 scenario (1 failed)
3 steps (1 failed, 1 skipped, 1 passed)
0m1.599s
```

无对象？我们没有从数据库中得到任何信息。让我们通过打开/app/controllers 中的 locations_controller.rb 文件来解决这个问题。更改索引方法:

```
def index
  @locations = Location.all

  respond_to do |format|
    format.html # index.html.erb
    format.json { render json: @locations }
  end
end
```

在 location_step.rb 中，我们需要更改以下内容:

```
Then /^I should see "([^"]*)"$/ do |arg1|
  pending # express the regexp above with the code you wish you had
end
```

收件人:

```
Then /^(?:|I )should see "([^"]*)"$/ do |text|
  if page.respond_to? :should
    page.should have_content(text)
  else
    assert page.has_content?(text)
  end
end
```

保存文件并重新运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 Then I should see "location 1" # features/step</em>definitions/location_steps.rb:9
  1 scenario (1 passed)
  3 steps (3 passed)
  0m1.679s
```

好了，一切正常！第一项测试通过了！谁需要休息？

## 西班牙语？

让我们进入国际化。我们需要做一个 i18n 模块。在/config/initializer/文件夹中创建一个名为 i18n.rb
的新文件

```
#encoding: utf-8

I18n.default_locale = :en

LANGUAGES = [
  ['English', 'en'],
  ["Español".html_safe, 'es']
]
```

这会将默认语言设置为英语，并创建一个我们将支持的语言列表及其语言环境。URL 中将指定区域设置，告诉我们使用哪种语言。我们将在路由中对此进行限定，并添加一个根 URL。别忘了删除/public/index.html！

打开 routes.rb 文件并添加以下行:

```
scope '(:locale)' do
  match 'locations/' => 'locations#index', :as => :locations
  root :to => 'locations#index'
end
```

我们将路由嵌套在:locale 的范围内，因为它在括号中，所以是可选的。

[http://localhost:3000/es](http://localhost:3000/es) 将使用默认的语言环境，英语。 [http://localhost:3000/en](http://localhost:3000/en) 和 [http://localhost:3000/es](http://localhost:3000/es) 将使用相同的控制器和方法，但是将使用 URL 中指定的区域设置

现在，我们需要根据 URL 中的参数(如果提供的话)来设置区域设置。

我们将在带有 before 过滤器的应用程序控制器中实现这一点。打开/app/controllers 文件夹中的 application_colbtroller.rb 文件。

添加这一行:

```
class ApplicationController < ActionController::Base
  before_filter :set</em>i18n<em>locale_from_params
  protect_from_forgery

protected
  def set_i18n_locale_from_params
    if params[:locale]
      if I18n.available_locales.include?(params[:locale].to_sym)
        I18n.locale = params[:locale]
      else
        flash.now[:notice] = "#{params[:locale]} translation not available"
        logger.error
        flash.now[:notice]
      end
    end
  end
  def default_url_options
    { locale: I18n.locale }
  end
end
```

这里它检查一个区域参数。如果有，它会检查该语言环境是否在/config/initializer/i18n . Rb 文件的语言列表中。如果它在列表中，我们将 locale 设置为 param。如果它不在列表中，我们将显示一条消息，说明区域设置不可用。

default_url_options 在没有提供区域设置时设置默认区域设置。因为我们在这一点上已经做了很多更改，所以让我们保存所有的文件并重新运行测试。

```
international $ cucumber

Using the default profile...
Feature: Manage locations
In order to manage locations
As a user
I want to create and edit my locations.

Scenario: List a location. # features/manage<em>locations.feature:6
Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
Then I should see "location 1" # features/step</em>definitions/location_steps.rb:9

1 scenario (1 passed)
3 steps (3 passed)
0m1.583s
```

仍然是绿色的。咻。让我们开始做饭吧。
在我们的位置索引页面上，添加一个“位置”标题

打开 managing_locations.feature 文件并添加

```
Scenario: List a location.
Given there is a location named "location 1"
When I am on the locations page
Then I should see "Locations"
And I should see "location 1"
```

保存文件并重新运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 Then I should see "Locations" # features/step</em>definitions/location<em>steps.rb:9
 expected there to be content "Locations" in "Internationalnnnn location 1n n" (RSpec::Expectations::ExpectationNotMetError)
 ./features/step</em>definitions/location<em>steps.rb:11:in <code>/^(?:|I )should see "([^"]*)"$/'
 features/manage_locations.feature:9:in</code>Then I should see "Locations"'
 And I should see "location 1" # features/step</em>definitions/location<em>steps.rb:9
 Failing Scenarios:
 cucumber features/manage</em>locations.feature:6 # Scenario: List a location.
  1 scenario (1 failed)
  4 steps (1 failed, 1 skipped, 2 passed)
  0m1.593s
```

如预期失败。

在/app/views/locations 中打开 index.html.ern 文件并添加

```
<h1>Locations</h1>
```

保存文件并重新运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 Then I should see "Locations" # features/step</em>definitions/location<em>steps.rb:9
 And I should see "location 1" # features/step</em>definitions/location_steps.rb:9
  1 scenario (1 passed)
  4 steps (4 passed)
  0m1.604s
```

我们又回到绿色了。

现在复制相同的测试，但是在标题中我们应该看到“Locaciones”
而不是“Location ”,将 managing_locations.feature 更改为:

```
Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location.
    Given there is a location named "location 1"
    When I am on the locations page
    Then I should see "Locations"
    And I should see "location 1"
  Scenario: List a location.
    Given there is a location named "location 1"
    When I am on the locations page
    Then I should see "Locaciones"
    And I should see "location 1"
```

保存文件并重新运行测试

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 Then I should see "Locations" # features/step</em>definitions/location<em>steps.rb:9
 And I should see "location 1" # features/step</em>definitions/location<em>steps.rb:9
 Scenario: List a location. # features/manage</em>locations.feature:12
    Given there is a location named "location 1" # features/step<em>definitions/location</em>steps.rb:1
    When I am on the locations page # features/step<em>definitions/location</em>steps.rb:5
    Then I should see "Locaciones" # features/step<em>definitions/location</em>steps.rb:9
     expected there to be content "Locaciones" in "InternationalnnLocationsnnn location 1n n" (RSpec::Expectations::ExpectationNotMetError)
     ./features/step<em>definitions/location</em>steps.rb:11:in <code>/^(?:|I )should see "([^"]*)"$/'
features/manage_locations.feature:15:in</code>Then I should see "Locaciones"'
    And I should see "location 1" # features/step<em>definitions/location</em>steps.rb:9
  Failing Scenarios:
  cucumber features/manage_locations.feature:12 # Scenario: List a location.
  2 scenarios (1 failed, 1 passed)
  8 steps (1 failed, 1 skipped, 6 passed)
  0m1.643s
```

它在我们预料的地方失败了。这就是我们之前所做的所有编码得到回报的地方。该翻译了。对于我们需要翻译的单词，我们将调用 I18n.translate，它有一个别名 I18n.t，还有一个名为 t 的助手。我们可以选择您喜欢的任何名称，但是如果我们使用提供的 t helper 函数，以点开始的名称将首先使用模板的名称进行扩展。就这么办吧。

在 index.html.erb 中，更改如下

```
<h1>Locations</h1>
```

到

```
<h1><%= t('.title_html') %></h1>
```

保存文件。我们需要在/config/locales 文件夹中创建一个 en.yml 文件。

```
en:

locations:
    index:
      title_html:    "Locations
```

我们还需要在/config/locales 文件夹中创建一个 es.yml 文件

```
es:

locations:
    index:
      title_html:    "Locaciones"
```

这些文件保存着译文。还要注意 YAML 的结构类似于文件结构？(/位置/索引)。这在以后会很方便。保存文件并再次重新运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 Then I should see "Locations" # features/step</em>definitions/location<em>steps.rb:9
 And I should see "location 1" # features/step</em>definitions/location<em>steps.rb:9
 Scenario: List a location. # features/manage</em>locations.feature:12
    Given there is a location named "location 1" # features/step<em>definitions/location</em>steps.rb:1
    When I am on the locations page # features/step<em>definitions/location</em>steps.rb:5
    Then I should see "Locaciones" # features/step<em>definitions/location</em>steps.rb:9
     expected there to be content "Locaciones" in "InternationalnnLocationsnnn location 1n n" (RSpec::Expectations::ExpectationNotMetError)
     ./features/step<em>definitions/location</em>steps.rb:11:in <code>/^(?:|I )should see "([^"]*)"$/'
 features/manage_locations.feature:15:in</code>Then I should see "Locaciones"'
    And I should see "location 1" # features/step<em>definitions/location</em>steps.rb:9
  Failing Scenarios:
  cucumber features/manage_locations.feature:12 # Scenario: List a location.
  2 scenarios (1 failed, 1 passed)
  8 steps (1 failed, 1 skipped, 6 passed)
  0m1.643s
```

什么？红色？似乎在第二次测试中，我们从未说过使用 **es** 作为地区，所以默认为 **en** 。

让我们按如下方式解决这个问题:

```
Scenario: List a location.
  Given there is a location named "location 1"
  And I am on the es site
  When I am on the locations page
  Then I should see "Locaciones"
  And I should see "location 1"
```

保存文件并重新运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 Then I should see "Locations" # features/step</em>definitions/location<em>steps.rb:9
 And I should see "location 1" # features/step</em>definitions/location<em>steps.rb:9
 Scenario: List a location. # features/manage</em>locations.feature:12
    Given there is a location named "location 1" # features/step<em>definitions/location</em>steps.rb:1
    And I am on the es site # features/manage<em>locations.feature:14
 Undefined step: "I am on the es site" (Cucumber::Undefined)
 features/manage</em>locations.feature:14:in `And I am on the es site'
    When I am on the locations page # features/step<em>definitions/location</em>steps.rb:5
    Then I should see "Locaciones" # features/step<em>definitions/location</em>steps.rb:9
    And I should see "location 1" # features/step<em>definitions/location</em>steps.rb:9
  2 scenarios (1 undefined, 1 passed)
  9 steps (3 skipped, 1 undefined, 5 passed)
  0m1.723s
  You can implement step definitions for undefined steps with these snippets:
  Given /^I am on the es site$/ do
    pending # express the regexp above with the code you wish you had
  end
```

好吧，那么！它告诉我们该做什么。
打开 location_steps.rb，把这个加进去。

```
Given /^I am on the (.+) site$/ do |language|
  I18n.locale = language
end
```

保存文件并重新运行测试。

```
international $ cucumber
  Using the default profile...
  Feature: Manage locations
  In order to manage locations
  As a user
  I want to create and edit my locations.
  Scenario: List a location. # features/manage<em>locations.feature:6
 Given there is a location named "location 1" # features/step</em>definitions/location<em>steps.rb:1
 When I am on the locations page # features/step</em>definitions/location<em>steps.rb:5
 Then I should see "Locations" # features/step</em>definitions/location<em>steps.rb:9
 And I should see "location 1" # features/step</em>definitions/location<em>steps.rb:9
 Scenario: List a location. # features/manage</em>locations.feature:12
    Given there is a location named "location 1" # features/step<em>definitions/location</em>steps.rb:1
    And I am on the es site # features/step<em>definitions/location</em>steps.rb:17
    When I am on the locations page # features/step<em>definitions/location</em>steps.rb:5
    Then I should see "Locaciones" # features/step<em>definitions/location</em>steps.rb:9
    And I should see "location 1" # features/step<em>definitions/location</em>steps.rb:9
  2 scenarios (2 passed)
  9 steps (9 passed)
  0m1.636s
```

得分！你现在有一个非常简单的多语言网站。我希望您发现使用 BDD 来驱动 Rails 应用程序的国际化很有用。

## 分享这篇文章