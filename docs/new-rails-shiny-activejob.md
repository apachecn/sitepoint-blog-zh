# 新 Rails 闪亮:ActiveJob

> 原文：<https://www.sitepoint.com/new-rails-shiny-activejob/>

![active-job](img/77325a55335db12f40fb518b45a2194c.png)

Rails 4.2 的一个备受期待的特性是 ActiveJob 库的引入。像 Rails 中的许多组件一样，ActiveJob 充当 Ruby 生态系统中一些最流行的队列库的适配器层。

有了新的 ActiveJob 库，选择一个具有独特 API 的队列库将不再是一件需要担心的事情。Rails 现在提供了一个独特的队列接口，允许您根据自己的需要替换队列宝石，而无需更改应用程序代码。想从延迟作业切换到 Backburner 吗？没问题。ActiveJob 让您以最小的痛苦完成这项工作。

另一个很棒的特性是与 ActionMailer 的完全集成。电子邮件总是那些不需要用户知道邮件已经发出就可以完成的任务之一。ActiveJob 还提供了与方法`deliver_later!`和`deliver_now!`相同级别的抽象，具有明显的功能性。

最后，随着 ActiveJob 的加入，Rails 4.2 还将包含全局 ID 库，它提供了模型实例的惟一标识符:`gid://Application/Model/id`。这在作业调度中特别有用，因为我们需要引用模型对象而不是序列化对象本身。调度程序不需要参与特定的模型及其 ID，而是只需要使用全局 ID 来查找确切的模型实例。

为了充分理解 ActiveJob 特性集，我们需要看一下它的核心 **ActiveJob 功能**、 **ActionMailer** 功能和**全局 ID** 。

### 使用 ActiveJob

对于这个例子，我们将利用一个处理地理定位的 Rails 4.2 应用程序(确切地说是 4.2.0.beta2)。在该应用程序中，有一个帐户模型，其中包含邮政编码、城市、州、纬度和经度字段。因为 zipcode 产生列表中其他项目的结果，所以这是我们的应用程序将向用户请求的唯一一个结果。

下面是我们的帐户类的一个示例:

```
{
    "id": null,
    "name":  null,
    "city": null,
    "state": null,
    "zipcode": null,
    "latitude": null,
    "longitude": null,
    "created_at": null,
    "updated_at": null
}
```

如果没有基于作业的系统，查找这些额外数据的简单任务对于应用程序来说可能太慢，无法实时完成。通常，应用程序必须通过第三方服务来收集数据。这是后台工作的完美场景。

让我们为此操作创建第一个作业！

首先，确保将`gem 'delayed_job_active_record'`添加到您的 gemfile 中，在终端中运行`bundle install`，并按照 [delayed_job github 页面](https://github.com/collectiveidea/delayed_job)上的指示完整安装延迟作业。

一旦安装了延迟作业(或者您选择的排队系统)，您将需要从终端生成作业类。通过使用以下命令，一个新文件将出现在 **app/jobs** 文件夹中:

```
$ rails g job geolocate_account
create  app/jobs/geolocate_account_job.rb
```

上述命令生成以下类:

```
class GeolocateAccountJob < ActiveJob::Base
  queue_as :default

  def perform(*args)
    # Do something later
  end
end
```

在上面的类中，`perform`动作是任务的所有逻辑所在的地方。对于我们的应用程序，我们希望从邮政编码中找到地理位置。我们将利用 geocoder gem，它为我们提供了一个连接到第三方地理定位服务的 API。代码如下:

```
def perform(account)
  result = Geocoder.search(account.zipcode).first

  if result
    account.latitude = result.latitude
    account.longitude = result.longitude
    account.city = result.city
    account.state = result.state
    account.save!
  end
end
```

现在我们已经完成了我们工作的`perform`方法。让我们打开控制台，调用这个作业，让我们的第一个帐户排队。因为我们的应用程序是新的，所以让我们先添加一个新帐户:

```
Account.create(name: 'Dunder Mifflin', zipcode: '18505')
```

然后用我们的`GeolocateAccountJob`排队开户:

```
GeolocateAccountJob.perform_later Account.first
```

您可以通过运行`Delayed::Job.count`看到作业已经成功入队。您还可以查看日志，查看作业是否已成功提交到数据库。

### 为未来排队

有时，能够在将来对作业进行排队是很重要的，ActiveJob 让您可以做到这一点。例如，作业排队的一般方式如下:

```
GeolocateAccountJob.perform_later Account.first
```

但是，您还可以将作业排队以备将来使用:

```
GeolocateAccountJob.set(wait: Date.tomorrow.noon).perform_later Account.first
```

和

```
GeolocateAccountJob.set(wait_until: 1.day).perform_later Account.first
```

这两个代码片段将产生大致相同的结果。

您还可以为队列作业设置优先级:

```
GeolocateAccountJob.set(queue: :low_priority).perform_later Account.first
```

### 从失败中拯救

ActiveJob 的另一个重要特性是能够捕捉发生在您的`perform`方法中的异常。使用同一个`GeolocateAccountJob`类，让我们修改我们的`perform`方法来抛出一个`ServiceDown`异常:

```
class GeolocateAccountJob < ActiveJob::Base
  queue_as :default

  def perform(account)
     raise Geolocation::ServiceDown
  end
end
```

然后我们可以添加一个 rescue 块来处理我们的异常:

```
class GeolocateAccountJob < ActiveJob::Base
  queue_as :default

  rescue_from(Geolocation::ServiceDown) do |exception|
    # Handle failed exception
  end

  def perform(account)
     raise Geolocation::ServiceDown
  end
end
```

## 与 ActionMailer 集成

ActiveJob 中包含的另一个有益特性是与 ActionMailer 的集成。发送电子邮件肯定是一个应该通过队列执行的功能。不要使用您选择的库的邮件程序方法，而是使用 ActiveJob 本身支持的两种方法`deliver_now!`和`deliver_later!`。

顾名思义，`deliver_now!`立即执行发送。用这个代替[不推荐的](https://github.com/rails/rails/blob/master/actionmailer/lib/action_mailer/message_delivery.rb#L85) `deliver`方法。实现队列的方法是`deliver_later!`。

```
AccountMailer.welcome(Account.first).deliver_now!
AccountMailer.welcome(Account.first).deliver_later!
```

需要像`set`提供的功能？使用这些可选参数！

```
AccountMailer.welcome(Account.first).deliver_later!(wait: 1.hour)
AccountMailer.welcome(Account.first).deliver_later!(wait_until: 10.hours.from_now)
```

## 全局 ID

ActiveJob 库中的最后一个功能是全局 ID，它创建一个 URI 来表示模型实例。这在与工作有关的情况下很有帮助。不用序列化整个对象或 id/类对，只需将实例的全局 ID 保存到作业中。

在作业执行时，全局 ID 用于定位适当的实例。

让我们来看看实际情况:

```
account = Account.first

account.global_id.to_s
=> "gid://sample/Account/1"

GlobalID::Locator.locate account.global_id
=> Account:0x007f9746c8a1b0

account.global_id.app
=> "sample"

account.global_id.model_name
=> "Account"

account.global_id.model_class
=> Account(id: integer, name: string, city: string, state: string, zipcode: string, latitude: integer, longitude: integer, created_at: datetime, updated_at: datetime)

account.global_id.model_id
=> "1"
```

请注意，许多数据是与全局 ID 相关联存储的，这允许您获得关于对象实例的大量元数据。

与全局 ID 关系不太远的是带符号的全局 ID，它类似于全局 ID，只是它是一个带符号的对象。可以使用对象上的`sgid`方法访问签名版本。

```
account.sgid
=> SignedGlobalID:0x007f97470cac98

account.sgid.to_s
=> "BAhJIhtnaWQ6Ly9zYW1wbGUvQWNjb3VudC8xBjoGRVQ=--4b86065bf01ecf72de68ea3d34d69f5241178ea1"
```

`SignedGlobalID`可用于验证和检索`GlobalID`,并包括为签名的全局 ID 设置截止日期的能力。

## ActiveJob 兼容库

截至撰写本文时，ActiveJob 可以支持以下队列库:

*   [回燃器](https://github.com/nesquena/backburner)
*   [延迟工作](https://github.com/collectiveidea/delayed_job)
*   [屈](https://github.com/bkeepers/qu)
*   [阙](https://github.com/chanks/que)
*   [QueueClassic 2.x](https://github.com/QueueClassic/queue_classic)
*   [Resque 1.x](https://github.com/resque/resque/tree/1-x-stable)
*   [Sidekiq](https://github.com/mperham/sidekiq)
*   [运动鞋](https://github.com/jondot/sneakers)
*   [吸盘冲头](https://github.com/brandonhilkert/sucker_punch)

## 包扎

ActiveJob 是对 Rails 框架的一个强大且急需的补充。现在您已经理解了 ActiveJob 库、ActionMailer 对应物和全局 ID，您可以充分利用 ActiveJob 了。我很想听到更多关于你如何在你的应用程序中使用 ActiveJob 的信息，以及你到目前为止学到的东西。请不吝赐教。我们来聊聊吧！

## 分享这篇文章