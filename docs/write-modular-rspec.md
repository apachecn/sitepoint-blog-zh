# 编写模块化 RSpec

> 原文：<https://www.sitepoint.com/write-modular-rspec/>

![Cubes block. Assembling concept. On white.](img/eb508504bd722f86cd05e529424da696.png)

RSpec 是 Ruby/Rails 环境中最流行的测试框架。它最大的两个好处是能够编写干净、简洁和模块化的测试，并将其与许多不同的框架(后端、前端、API)相结合..等等。)编写测试脚本最大的痛苦之一是不可避免的重构。本文旨在展示如何制作易于理解和维护的测试脚本，同时减少重构的噩梦。

## 你想象中的项目

为了这篇文章，我们将从以下前提开始:
1。你的站点(“天气很好”)是一个应用程序，它根据用户输入提供一些地区的天气预报和当前天气信息。
2。它公开了一个 REST API(数据格式是 JSON)。
3。为了访问 API，用户需要注册并获得用于后续请求的授权令牌。

通过 API 提供的方法有:

GET/
–城市中的当前天气(token，city)
–城市中某个区域的天气(token，city，area)
–城市中天气的用户输入(token，city)

POST/
–为城市天气创建用户条目(令牌，城市，评论)
–创建用户(令牌，名字，姓氏，电子邮件，密码)
–登录(电子邮件，密码)
–注销(令牌)

DELETE/
–删除用户(令牌，用户)
–删除城市天气的用户条目(令牌，用户，城市)

## 问题是

这个项目刚刚开始。您听说过这个 RSpec 工具，并且您想使用它作为编写测试的主要框架。您从未使用过它，并且是 Ruby 的新手。您正在慢慢地编写您的测试，并且每天都在学习更多的 RSpec/Ruby。你目前已经有了一些编写测试的方法，并且正在工作。很好！随着项目的进展，应用程序的功能越来越多，您可以轻松地编写测试脚本了。这成了你的第二天性，但是你开始意识到你在多个地方写同样的代码。您没有时间重构现有的测试脚本，并且您害怕浪费时间和破坏已经在工作的东西。

人们常说:“如果它没坏，就不要修理它。”但在这种情况下，这并不适用。有许多测试脚本，最终，我们意识到它们是不可维护的。技术部门堆积如山，因为我们没有以明智的方式编写规格。

## 解决方案

测试重构是不可避免的，仅仅因为特性每天都在变化。问题是:你会用容易的方法还是困难的方法来做这件事？为了简单起见，您需要模块化地编写测试以避免代码重复。考虑到应用程序中公开的 API，下面是一些可能的用例:

1.  创建一个新用户，并验证该用户可以登录和注销。
2.  否定-尝试使用不存在的用户登录。
3.  对于现有用户，获取特定城市的天气预报(城市存在)。
4.  NEG–对于现有用户，获取特定城市的天气预报(城市不存在)。
5.  对于现有用户，获取城市特定区域的天气预报。
6.  NEG–对于现有用户，获取城市特定区域的天气预报(区域在负范围内)。
7.  对于现有用户，从特定城市的用户那里获取输入。
8.  对于现有用户，为特定城市添加新输入，并检查该输入是否在输入列表中。
9.  NEG–删除现有用户并尝试使用该用户登录。
10.  删除现有用户，创建新用户，使用该用户登录，并确保特定城市的前一用户的评论已被删除。

这些只是一些案例，当然，还有更多的案例需要报道。坚持这里描述的案例，已经有一些可重用的模式了。我们需要一个公共类来提供发送 REST 请求的方法。此外，有些操作是可重复的，比如创建新用户、登录、注销、获取城市天气预报、获取城市天气的用户输入等。

## 设置 RSpec

首先，是时候设置 RSpec 了。使用标准`rspec --init command`为 RSpec 测试建立初始结构；

```
<root_project_directory>
 - spec
    spec_helper.rb
 .rspec
```

由于测试将是模块化的，并将使用一些初始配置数据，一个好的开始方式是在项目根目录下创建两个额外的目录: **config** 和 **lib** 。每个环境的配置都将存在于一个 **config.yml** 中。**库**将为我们的测试脚本保存可重用的代码。

## 设置 config.yml

**config.yml** 看起来像是:

```
environment:
 server: http://test.weatherisgood.com
 port: 7000
```

## 可重复使用的零件

如前所述，我们需要一个公共类来发送 REST 请求。这将是 **sender.rb** 文件中的`Sender`类:

```
require 'rubygems'
require 'rest_client'
require 'json'
require 'yaml'

class Sender
 attr_reader :server_url

 def initialize
  config_yml = YAML::load(File.open("./config/config.yaml"))
  @server_url = config_yml['environment']['server'].to_s + ":" + config_yml['environment']['port'].to_s
 end

 def send_request(endpoint, method, args={}, headers={})
  unless ['GET','POST','DELETE'].include? method
   raise "Incorrect REST method has been specified"
  end

  if method == 'GET'
   begin
    response = RestClient.get(@server_url + endpoint, headers)
    return JSON.parse(response)
   rescue => e
    return JSON.parse(e.response)
   end
  elsif method == 'POST'
   begin
    response = RestClient.post(@server_url + endpoint, args, headers)
    return JSON.parse(response)
   rescue => e
    return JSON.parse(e.response)
   end
  elsif method == 'DELETE'
   begin
    response = RestClient.delete(@server_url + endpoint, headers)
    return JSON.parse(response)
   rescue => e
    return JSON.parse(e.response)
   end
  end
 end
end
```

代码非常简单明了。在构造函数中，从配置数据创建`server_url`。`send_request`方法指定端点、REST 方法、可能的参数和头(用于认证)。现在，我们有了用于发送和解析 REST 请求的集中式类。

下一件事，我们需要的是一个共同的行动(登录/注销，创建新用户，获得天气预报)的实施..等等。)为了简单起见，这些方法存在于单个`*.rb`文件中，但是我们可以使用多个文件来根据功能划分代码。让我们称之为 **weatherisgood_api.rb** (矛盾的是，命名是软件开发中最难的事情之一，可能有更好的名字:) ):

```
require 'rubygems'
require 'json'
require 'sender'

class WeatherIsGoodApi

 attr_reader :sender, :authorization_token

 def initialize
  @sender = Sender.new
 end

 def login(email, password)
  response = @sender.send_request('/auth/login', 'POST', {:email=>email, :password=>password})
  @authorization_token = response['data']['token'] unless response['data']['token'].nil?
  return response['data']['token']
 end

 def logout
  response = @sender.send_request('/auth/logout','POST', {}, {:token=>@authorization_token})
 end

 def create_user(firstname, lastname, email, password)
  response = @sender.send_request('/user/create', 'POST', {:firstname=>firstname, :lastname=>lastname, :email=>email, :password=>password})
 end

 def get_weather_today_for_city(city)
  response = @sender.send_request("/weather/#{city}/today", 'GET', {}, {:token=>@authorization_token})
 end

 def get_users_input_for_weather(city)
  response = @sender.send_request("/weather/#{city}/today/user", 'GET', {}, {:token=>@authorization_token})
 end
end
```

如您所见，针对 API 执行的每个操作都有相应的方法。登录可以获得在后续请求中使用的授权令牌。

## 可重复使用的魔术

有了 REST 调用和公共动作的基本实现，我们可以利用 RSpec 共享概念来进一步划分测试步骤中执行的动作。这很重要，因为某些测试步骤将为这些操作提供不同的输入，并期望不同的输出(一个很好的例子是登录的肯定和否定测试。)为此，使用 RSpec 的`shared_context`，它定义了接受输入参数并在`it`程序块内以与常规`describe`程序块相同的方式执行检查的动作:

```
require 'rubygems'
require 'json'

shared_context "Login" do |weatherisgood, email, password|
 it "succeeds for user: #{email}" do
  user_login_success = false
  output = weatherisgood.login(email, password)
  user_login_success = true unless output.nil?
  expect(user_login_success).to eq(true)
 end
end

shared_context "NEG - Login" do |weatherisgood, email, password|
 it "does not succeed for invalid user: #{email}" do
  user_login_failure = false
  output = weatherisgood.login(email, password)
  user_login_failure = true if output.nil?
  expect(user_login_failure).to eq(true)
 end
end

shared_context "Create user" do |weatherisgood, firstname, lastname, email, password|
 it "creates user account for user: #{firstname} #{lastname}" do
  user_account_created = false
  output = weatherisgood.create_user(firstname, lastname, email, password)
  user_account_created = true unless output['data']['success'].nil?
  expect(user_account_created).to eq(true)
 end
end

shared_context "NEG - Create user (user already exists)" do |weatherisgood, firstname, lastname, email, password|
 it "does not create user account for user: #{firstname} #{lastname} since it already exist" do
  error_message = ""
  output = weatherisgood.create_user(firstname, lastname, email, password)
  if output['data']['success'].nil?
   error_message = output['data']['error']
  end
  expect(error_message).to eq('User already exists!')
 end
end

shared_context "Get weather for city" do |weatherisgood, city|
 it "gets weather forecast for city #{city}" do
  weather_forecast_retrieved = false
  output = weatherisgood.get_weather_today_for_city(city)
  # Do some checks here for the content and if everything is ok set weather_forecast_retrieved to true
  expect(weather_forecast_retrieved).to eq(true)
 end
end

shared_context "NEG - Get weather for city" do |weatherisgood, city|
 it "does not get weather forecast for city #{city} that does not exist" do
  error_message = ""
  output = weatherisgood.get_weather_today_for_city(city)
  if output['data']['success'].nil?
   error_message = output['data']['error']
  end
  expect(error_message).to eq('City does not exist!')
 end
end

shared_context "Get user input on weather for city" do |weatherisgood, city|
 it "gets users input on weather forecast for city #{city}" do
  weather_forecast_retrieved = false
  output = weatherisgood.get_users_input_for_weather(city)
  # Do some checks here for the content and if everything is ok set weather_forecast_retrieved to true
  expect(weather_forecast_retrieved).to eq(true)
 end
end
```

请注意，我在一些共享上下文中使用前缀“NEG”来表示否定检查(例如，使用无效凭证登录)。从测试用例中调用。创建**测试*用例* 1_spec.rb** 并将其放置在 **spec** 目录中。为了便于理解和维护，我通常将一个测试用例放在一个`*.rb`文件中。这里有一个例子:

```
require 'rubygems'
require './lib/weatherisgood_api'
require './lib/shared/weatherisgood_spec'

user = "bakir"
password = "mypassword"
city = "Sarajevo"
city_doesnot_exist = "MyCity"
weatherisgood = WeatherIsGoodApi.new

describe "Test Case 1: Login with user and get weather check for city that exists and doesn't exist" do
 context "Login with user #{user}" do
  include_context "Login", weatherisgood, user, password
 end

 context "Get weather for city #{city} that exists" do
  include_context "Get weather for city", weatherisgood, city
 end

 context "Get weather for city #{city_doesnot_exist} that does not exist" do
  include_context "NEG - Get weather for city", weatherisgood, city_doesnot_exist
 end
end
```

需要注意的几件事:
1。我用`context`来划分测试步骤。
2。使用`describe`描述测试用例。使用`context`描述测试步骤。这是区分测试用例与测试步骤的个人偏好。
3。在每个`context`中，我们都有`include_context`和`shared_context`的名字可以调用，必要时传递参数。
4。当运行这个测试用例时，输出(使用中的`--format`文档。rspec 文件)看起来像:

```
Test Case 1: Login with user and get weather check for city that exists and doesn't exist
   Login with user bakir@myemail.com
    succeeds for user: bakir@myemail.com
   Get weather for city that exists
    gets weather forecast for city Sarajevo
   Get weather for city that does not exist
    does not get weather forecast for city MyCity that does not exist

   Finished in 0.00124 seconds
   3 examples, 0 failures
```

正如您所看到的，输出非常清晰，包括:
–测试步骤的描述("使用用户 bakir@myemail.com 登录")
–测试步骤的预期结果("用户成功:bakir@myemail.com")
建议尽可能清晰地描述您的测试步骤，以便输出可读且有用。

## 结论

在本教程中，我试图解释为什么我们需要 RSpec 测试代码的可重用性。REST api 所需的每一个更改都可以在一个地方完成(即`WeatherIsGoodApi`类)。此外，测试中特定特性的预期结果在一个单独的位置进行调整(**中的`shared_context`)这是一种实现可重用性的方法，但是您当然可以想到其他方法。关键是:在你写测试之前要思考，因为从长远来看，它很容易伤害你。如果发生这种情况，返回并更改测试设计的过程可能比从头开始编写更糟糕。**

## 分享这篇文章