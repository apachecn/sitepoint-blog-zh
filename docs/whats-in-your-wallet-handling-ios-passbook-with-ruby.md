# 你钱包里有什么？用 Ruby 处理 iOS Passbook

> 原文：<https://www.sitepoint.com/whats-in-your-wallet-handling-ios-passbook-with-ruby/>

![](img/c0cfa43b06c13d284d3acc527ba47245.png)

Passbook(从 iOS 9 开始称为“钱包”)是一个内置的 iOS 应用程序，为用户提供了一种存储和访问“通行证”的方式。将通行证视为某种东西的数字令牌，例如活动门票、会员卡、折扣券、登机牌等。通行证可以通过邮件、信息、应用程序等方式添加到 passbook 中。

通行证对使用者有许多好处。例如，假设用户添加了一张上午 10 点开始的电影票的通行证..当 even 靠近用户以直接访问它时，该通行证将显示在主屏幕上。如果为商店 X 添加了打折的会员通行证，那么每当用户走进商店 X 时，就会显示该通行证。

通行证也有利于通行证提供者。首先，当信息发生变化时，通行证可以作为一种通知手段。例如，电影被取消或登机牌被更改，或者提供商希望为登机牌持有者提供额外的折扣。通过使用通行证，所有这些都是可能的。

今天，我们将了解如何构建一个处理 passbook 生成和推送通知的 iOS passbook 后端。我将使用 Ruby 和 Sinatra。让我们开始吧。

> 注意:本教程只涉及 passbook 的服务器部分。我建议您事先了解一下 pass 设计和模式。外面有很多好的教程，我推荐这个[两部分系列](http://www.raywenderlich.com/20734/beginning-passbook-part-1)。

### 要求

在我们开始编写代码之前，有几件事对于 pass 创建是必不可少的，都与证书有关。他们是，

1.  p12 证书. pem
2.  p12Key.pem
3.  p12 密码–字符串
4.  WWDRCertificate.pem
5.  NotificationCert.pem(用 p12 密钥签名的 p12 证书)

证书只能从 iOS 开发者访问门户生成。有关如何生成此证书的信息可在[此处](http://www.raywenderlich.com/20734/beginning-passbook-part-1)获得(第节-获取证书！)

## 创建应用程序

#### 文件夹结构

让我们创建应用程序。首先创建如下所示的文件夹、文件夹结构和文件。如果您使用的是下面描述的目录结构之外的任何其他目录结构，请确保也在代码中对其进行更正。

![](img/235d76392925868f5b78ee4b23aaad79.png)

确保将文件放在与上述名称相同的**资产**文件夹中，因为这是惯例。将之前生成的证书放入**证书**文件夹。

#### 必需的文件

现在文件夹结构已经创建，将以下内容添加到您的 **Gemfile** 中:

```
source 'https://rubygems.org'

gem 'sinatra'
gem 'passbook'
gem 'grocer'

gem 'activerecord'
gem 'sinatra-activerecord'
gem 'pg' 
```

我们使用 [Passbook](https://github.com/frozon/passbook) gem 生成通行证，使用[杂货商](https://github.com/grocer/grocer) gem 推送通知。在 Rails 方面，我们将使用 ActiveRecord ORM 和 PostgreSQL。

一旦**gem 文件**被更新，运行`bundle install`。现在将安装所有需要的 gem。

接下来，将以下内容添加到 **app.rb** :

```
 require 'sinatra'
 require 'bundler/setup'
 require 'passbook'
 require 'sinatra/activerecord' 
```

这些是我们将在应用程序中使用的模块。打开**耙子文件**并添加:

```
 require './app'
 require 'sinatra/activerecord/rake'
 Dir.glob('lib/tasks/*.rake').each { |r| load r} 
```

Rakefile 是`rake`命令工作所必需的。我们需要主应用程序文件和 ActiveRecord 的 Rake 模块。最后，从 **lib/tasks** 中加载所有的 rake 文件，以防万一，如果我们想要定制 Rake 任务的话。

接下来，填写配置细节。打开我们在 **config/** 文件夹中创建的文件，添加以下内容。

#### 数据库配置

```
# database.yml
development:
  adapter: postgresql
  database: appname_development
  host: localhost

production:
  adapter: postgresql
  encoding: unicode 
```

#### ActiveRecord 配置

```
#environments.rb
require "sinatra/activerecord"

configure :production, :development do
  db = URI.parse(ENV['DATABASE_URL'] || 'postgres://localhost/appname_development')

  ActiveRecord::Base.establish_connection(
    :adapter => db.scheme == 'postgres' ? 'postgresql' : db.scheme,
    :host     => db.host,
    :username => db.user,
    :password => db.password,
    :database => db.path[1..-1],
    :encoding => 'utf8'
  )
end 
```

#### 存折配置

```
#passbook.rb
Passbook.configure do |passbook|
  passbook.p12_password = 'r@nd0mpa$sw0rd'
  passbook.p12_key = 'certificates/p12_key.pem'
  passbook.p12_certificate = 'certificates/p12_certificate.pem'
  passbook.wwdc_cert = 'certificates/wwdr.pem'
  passbook.notification_gateway = 'gateway.push.apple.com'
  passbook.notification_cert = 'certificates/push_notificfation_certificate.pem'
end 
```

数据库和 ActiveRecord 配置很简单。在 Passbook 配置中，我们提供生成通行证所需的证书和凭证。需要 **app.rb** 中的**配置/环境**和**配置/存折**就可以了。

好了，我们已经成功引导了目录结构，并在其中放置了所有需要的文件。接下来，让我们创建数据库模式。

## 创建数据库模式

#### 迁移

首先，通过在控制台中键入以下命令来创建数据库:

```
rake db:create 
```

我们的应用程序需要 4 个表来处理通行证注册和通知。以下是模式:

![](img/11e503432f65b080295d84a34b955d50.png)

快速浏览一下表格。**通行证**–保存进入通行证的必要信息。
2。**设备**–每当我们的通行证被添加到设备时，苹果将发送的设备标识符和推送令牌。该信息用于发送通行证更新。
3。**注册**–这促进了通行证和设备之间的多对多关系。一台设备可以添加许多通行证，一个通行证可以添加到许多设备，因此我们应该向所有设备发送通行证更新。
4。**日志**——无论何时出现问题，苹果都会向我们的服务器发布错误/警告信息。该表将保存这些日志，并用于故障排除。

> 注意:Apple 明确声明拥有关系的注册表，并在设备注销通行证时删除该行。如果想保留数据，可以添加一列来标记注册`active`或`inactive`。

让我们快速创建四个表。输入以下命令创建迁移(注意命名约定):

```
rake db:create_migration NAME=create_passes
rake db:create_migration NAME=create_devices
rake db:create_migration NAME=create_registrations
rake db:create_migration NAME=create_logs 
```

在迁移文件中，将以下内容分别添加到各自的文件中:

```
### create_passes
def change
  create_table :passes do |t|
    t.string :serial_number, null: false
    t.jsonb :data, null: false
    t.integer :version, default: 1
  end
end

### create_devices
def change
  create_table :devices do |t|
    t.string :identifier, null: false
    t.string :push_token, null: false
  end
end

### create_registrations
def change
  create_table :registrations do |t|
    t.integer :pass_id, null: false
    t.integer :device_id, null: false
  end
end

### create_logs
def change
  create_table :logs do |t|
    t.text :log
  end
end 
```

如您所见，`passes`表保存了通道的 JSON 数据，每个通道的 JSON 数据都不同。每个通行证都将通过其唯一的序列号来识别。另外，`version`包含通行证数据的版本。这在通过推送通知发送通行证更新时使用。

运行`rake db:migrate`来创建表格。 **db/** 文件夹和 **db/schema.rb** 现在应该存在了。

#### 模型

迁移已经完成，表也已创建。让我们为模式创建 ActiveRecord 模型。在之前创建的 **/models** 文件夹中，创建以下文件。再次提醒，注意命名约定。

```
### models/pass.rb
class Pass < ActiveRecord::Base
  validates_uniqueness_of :serial_number

  has_many :registrations
  has_many :devices, through: :registrations
end

### models/device.rb
class Device < ActiveRecord::Base
  validates_uniqueness_of :device_identifier
  validates_uniqueness_of :push_token

  has_many :registrations
  has_many :passes, through: :registrations
end

### models/registration.rb
class Registration < ActiveRecord::Base
  belongs_to :pass
  belongs_to :device
end

### models/log.rb
class Log < ActiveRecord::Base
end 
```

这里没有什么特别的，只是为 ActiveRecord 建立了识别和相应操作的关系。同样，要注意文件和类名的命名。然后需要我们刚刚在 **app.rb** 中创建的模型:

```
### app.rb
require './models/pass'
require './models/device'
require './models/registration'
require './models/log' 
```

## 端点

### 传递生成

好了，我们已经准备好编写我们的第一个端点，它将用于 pass 生成。端点接受 JSON 并返回**。pkpass** 文件。打开你的 **app.rb** 文件，在`require` s 后添加以下内容:

```
post '/passbooks' do
  request.body.rewind
  data = JSON.parse request.body.read

  unless @pass = Pass.find_by(serial_number: data['serialNumber'])
    @pass = Pass.create(serial_number: data['serialNumber'], data: data)
  end
  passbook = Passbook::PKPass.new @pass.data.to_json.to_s
  passbook.addFiles ['assets/logo.png', 'assets/logo@2x.png', 'assets/icon.png', 'assets/icon@2x.png']
  gen_pass = passbook.file
  send_file(gen_pass.path, type: 'application/vnd.apple.pkpass', disposition: 'attachment', filename: "pass.pkpass")
end 
```

我们刚刚创建了一个 post 端点来发送 JSON 数据并使用`.pkpass`文件进行响应。注意，如果 JSON 包含先前生成的 pass 的序列号，我们将忽略发布的数据，并遵从现有的 pass。这是有原因的:

1.  我们不应该允许重复的序列号。
2.  我们在这里不更新 pass，因为它不是一个更新端点。该模式包括一个`version`列，并在此基础上推送 pass 更新通知。如果我们在不检查数据的情况下为每个 pass 生成请求更新 pass，我们可能会向用户发送太多通知，而没有实际的更新。

最好为 pass 更新维护一个单独的端点，但是如果您仍然希望在单个端点中完成，有几种方法。将新数据与旧数据进行比较，只有在数据发生变化时才进行更新。这样就很容易维护 pass 版本。

让我们看看它是如何工作的，我将这个 JSON 发送到应用程序，下面是响应:

![8vz6AjO](img/664fded25c77ff49dd83f85a21ed92f7.png)

酷吧。通行证样式是完全可定制的。您可以在这里阅读更多相关信息–[传递文件](https://developer.apple.com/library/ios/documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html#//apple_ref/doc/uid/TP40012026)

我们已经完成了密码生成部分。接下来，让我们添加 pass 更新端点。

### 传递更新端点

我们的服务器、Apple 的服务器和设备之间的完整通信总共需要 5 个端点。关于它们的更多详细信息，请点击此处—[iOS Passbook web 服务参考](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PassKit_WebService/WebService.html#//apple_ref/doc/uid/TP40011988)

基本上，每当一个设备添加一个 pass，它就会调用我们的服务器(URL 将在 pass JSON 中提到)来注册它自己。我们必须记录推送通知和它发送的设备库标识符。之后，一旦我们有任何 pass 更新，使用设备的推送令牌向苹果服务器发送通知请求(无数据)。一旦发出请求，Apple 将调用端点来获取需要在设备上更新的通行证(记住一个设备可以有多个通行证)。我们会发送需要更新的序列号。然后，苹果会给每个序列号打电话，询问最新版本的通行证。最后，苹果会将更新发送到用户的设备上。就是这样。

除了这个流之外，另外两个端点是未注册的，并发布错误或警告日志。

幸运的是，passbook gem 提供了开箱即用的端点，因此我们只需扩展它并编写我们的逻辑。打开 **app.rb** ,在所有需求之后和路径生成端点之前添加此行:

```
use Rack::PassbookRack 
```

然后，在过程生成端点后添加以下行:

```
module Passbook
  class PassbookNotification
    def self.register_pass(options)
      status = verify_pass_and_token options
      if status
        return status
      end

      @device = Device.where(identifier: options['deviceLibraryIdentifier'], push_token: options['pushToken']).first_or_create
      if Registration.find_by(pass_id: @pass.id, device_id: @device.id).present?
        return {:status => 200}
      else
        Registration.create(pass_id: @pass.id, device_id: @device.id)
        return {:status => 201}
      end
    end

    def self.passes_for_device(options)
      unless valid_device? options['deviceLibraryIdentifier']
        return
      end

      update_tag = options['passesUpdatedSince'] || 0
      passes = @device.passes.where('version > ?', update_tag.to_i)
      if passes.present?
        {'lastUpdated' => Time.now.utc.to_i.to_s, 'serialNumbers' => passes.map{|p| p.serial_number}}
      else
        return
      end
    end

    def self.unregister_pass(options)
      status = verify_pass_and_token options
      if status
        return status[:status] == 401 ? status : {:status => 200}
      end

      unless valid_device? options['deviceLibraryIdentifier']
        return {:status => 401}
      end

      registrations = @device.registrations.where(pass_id: @pass.id)
      if registrations.present?
        registrations.destroy_all
      end
      return {:status => 200}
    end

    def self.latest_pass(options)
      @pass = find_pass_with options['serialNumber']
      unless @pass
        return
      end

      passbook = Passbook::PKPass.new @pass.data.to_json.to_s
      passbook.addFiles ['assets/logo.png', 'assets/logo@2x.png', 'assets/icon.png', 'assets/icon@2x.png']
      {:status => 200, :latest_pass => passbook.stream.string, :last_modified => Time.now.utc.to_i.to_s}
    end

    def self.passbook_log(log)
      log.values.flatten.compact.each do |l|
        Log.create(log: l)
      end
    end
  end
end

def verify_pass_and_token options
  token = ENV['AUTH_TOKEN']
  @pass = find_pass_with options['serialNumber']
  if options['authToken'] != token
    return {:status => 401}
  elsif !@pass
    return {:status => 404}
  else
    return
  end
end

def find_pass_with serial
  Pass.find_by(serial_number: serial)
end

def valid_device? identifier
  @device = Device.find_by(identifier: identifier)
end 
```

就是这样。代码非常简单明了，但是让我们快速浏览一下每个端点发生了什么。

##### 登记通行证

首先验证第一个请求。令牌应该在创建时包含在 pass JSON 中，因为它用于验证请求。接下来，验证序列号是否确实用于已创建的通行证。我们收到一个有效的请求，通过标识符和推送令牌获取或创建设备，然后为设备创建注册并通过。不同的响应代码是苹果公司所期望的。

##### 冒充设备

这是在我们发送推送通知请求后立即调用的端点。

在这个端点中，我们将接收一个标识符和一个`passesUpdatedSince`标志。`passesUpdatedSince`标志非常重要，因为它标识了通行证的版本。您可以使用任何变体来管理版本，但是使用整数形式的时间戳是一个好方法，因为它也可以用于其他目的。如果有许多最新版本的 pass，则用序列号作为数组进行响应。

##### 最新通行证

这是将作为推送通知通信周期的一部分被调用的最终端点。我们将收到在之前的响应中发送的每个序列号的一个请求，并且可以像 pass 生成端点一样生成 pass，用`.pkpass`文件进行响应。

##### 注销通行证

此端点将接收通行证详细信息和设备详细信息。我们正在验证通行证和设备，如果二者都存在且处于活动状态，则将其删除。

##### 存折日志

该端点获取从 Apple 发送的错误/警告，并将它们存储在`logs`表中。

#### 发送推送通知

我们已经准备好发送推送通知了。首先，为 pass 更新添加一个端点。打开 **app.rb** ，要求`grocer`，在通道创建端点后添加以下内容:

```
get '/passbooks/update' do
  request.body.rewind
  data = JSON.parse request.body.read

  unless @pass = find_pass_with(data['serialNumber'])
    @pass = Pass.create(serial_number: data['serialNumber'], data: data)
    {:response => 'Pass newly created.'}.to_json
  else
    @pass.update(data: data, version: Time.now.utc.to_i)
    push_updates_for_pass
    {:response => 'Pass updated and sent push notifications.'}.to_json
  end
end

def push_updates_for_pass
  @pass.devices.each do |device|
    puts "Sending push notification for device - #{device.push_token}"
    Passbook::PushNotification.send_notification device.push_token
  end
end 
```

我们使用 passbook gem `PushNotification.send_notification`方法发送推送通知。执行此命令后，通信周期开始，密码更新将被发送到用户设备:

## 结论

至此，本教程到此结束。本教程中使用的所有代码都托管在 [github](https://github.com/avinoth/passbook-sinatra) 中，可以随意分叉和戳戳。

## 分享这篇文章