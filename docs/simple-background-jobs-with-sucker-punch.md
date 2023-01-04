# 简单的背景工作与吸盘冲床

> 原文：<https://www.sitepoint.com/simple-background-jobs-with-sucker-punch/>

在应用程序中运行后台作业是保持用户界面简洁的好方法。无论是发送电子邮件、调用某个 API，还是任何长时间运行的任务，总有一些东西可以移到后台。有时，设置一个队列和工作进程是有意义的，但是其他时候，只要在后台运行该方法并进行最少的代码更改就很好了。进入[吸盘冲床](https://github.com/brandonhilkert/sucker_punch)，单工序异步加工库。使用奇妙的[赛璐珞](https://github.com/celluloid/celluloid/)框架，它让我们在很少的步骤中完成异步工作。

## 设置

首先，你的 app 需要运行在 Ruby 1.9+，JRuby 1.6+或者 Rubinius 2.0(后 2 个是 Ruby 1.9 模式)。然后，通过运行以下命令安装 gem

```
gem install sucker_punch
```

或者通过以下方式将其添加到您的 Gemfile 中

```
gem 'sucker_punch'
```

就是这样！现在让我们看看如何使用它。

## 创建工人

创建一个工人非常简单。它只是一个普通的 ruby 类，包含了`SuckerPunch::Worker`模块，并定义了一个或多个实例方法。Sucker Punch 建议只有一个`perform`方法，但是可以定义和使用任何名称和数量的方法。

```
class SomeWorker
    include SuckerPunch::Worker
    def perform(some_data)
      # Method code. Do some work.
    end
  end
```

## 给工人打电话

在调用 worker 之前，我们需要配置我们的队列:

```
SuckerPunch.config do
    queue name: :some_queue, worker: SomeWorker, workers: 5
    queue name: :welcome_queue, worker: WelcomeEmailWorker, workers: 2
  end
```

我们可以定义尽可能多的队列和尽可能多的工作线程(最好每个队列 2+),但是每个队列只能有一个工作线程类。更多的工作者意味着可以执行更多的并行任务，但是如果您使用连接到外部服务，比如数据库或 memcached，请注意连接的耗尽。
然后，您可以通过以下方式访问队列中的员工

```
SuckerPunch::Queue[:some_queue] # or
  SuckerPunch::Queue.new(:some_queue)
```

这将给我们一个`Celluloid::ActorProxy`对象来包装我们的 worker 对象。
我们可以直接在那个对象上调用`perform`方法(或者我们定义的任何其他方法),或者使用`async`立即返回控制并让它在后台运行。

```
SuckerPunch::Queue[:welcome_queue].perform(1)
  SuckerPunch::Queue[:welcome_queue].async.perform(1)
```

请注意，异步运行作业不会在失败时引发任何异常。它只会无声无息地失败。

## 测试

当然，一切都需要测试！

幸运的是，测试工人是相当容易的。您可以像测试任何 Ruby 类一样测试它。

带有一些 rspec 味道:

```
describe WelcomeEmailWorker 
  let(:user){ FactoryGirl.create :user } 
  let(:worker){ EmailWorker.new } 
  describe "#perform" do 
    it "delivers an email" do 
      expect{ worker.perform(user.id) }.to change{ UserMailer.deliveries.size }.by(1) 
    end 
  end 
end
```

要测试它如何与其他方法集成，有两个选项:

1)测试它调用作业并将其入队。为此，您需要要求`'sucker_punch/testing'`:

```
require 'sucker_punch/testing'
describe User do
  describe "#send_welcome_email" do
    it "delivers an email" do
      let(:user){ FactoryGirl.create :user }
      expect{
        user.send_welcome_email
      }.to change{ SuckerPunch::Queue.new(:email).jobs.size }.by(1)
    end
  end
end
```

2)内联运行作业。这可以通过要求`'sucker_punch/testing/inline'`来完成，作业将*总是*同步运行。

```
require 'sucker_punch/testing'
describe User do
  let(:user){ FactoryGirl.create :user }
  describe "#send_welcome_email" do  
    it "delivers an email" do
      expect{
        user.send_welcome_email
      }.to change{ UserMailer.deliveries.size }.by(1)
    end
  end
end
```

## 考虑

### 在数据库事务内部运行的测试

关于测试，有一件事要记住。如果您在一个事务中运行每个测试，那么您需要将它更改为一个截断策略，用于 Sucker Punch 测试。这里有一个如何使用 DatabaseCleaner 的例子:[https://gist.github.com/kitop/5248674](https://gist.github.com/kitop/5248674)。这是因为 Sucker Punch workers 总是在一个单独的线程中运行该方法，不管它是同步还是异步的。

### 坚持

请记住，Sucker Punch 在单独的线程上运行您的作业，而不是从外部队列进行轮询。这意味着，如果您的应用程序在处理作业时出现故障，默认情况下，它不会通知也不会在任何地方存储该错误，也不会重试。如果你需要更多的控制，你可以写你自己的包装器。也许你可以从 [girl_friday](https://github.com/mperham/girl_friday/blob/master/lib/girl_friday/persistence.rb) 中获得一些灵感，或者使用一些其他的解决方案，比如 [resque](https://github.com/resque/resque) 、 [sidekiq](https://github.com/mperham/sidekiq) 或者 [delayed_job](https://github.com/tobi/delayed_job) 。

### 轨道

如果你正在使用 Rails，工人通常进入`app/workers`目录。您应该小心使用 ActiveRecord 对象和连接。工人应该收到一个记录 id，而不是完整的对象。优选地，工作人员应该将与数据库访问相关的代码包装在一个`ActiveRecord::Base.connection_pool.with_connection`块中，这样就不会耗尽池中的连接。

```
class WelcomeEmailWorker
  include SuckerPunch::Worker

  def perform(user_id)
    ActiveRecord::Base.connection_pool.with_connection do
      user = User.find(user_id)
      UserMailer.welcome(user).deliver
    end
  end
end
```

### 连接

您不仅要小心 ActiveRecord 连接，还要小心任何 redis、memcache 或任何其他可能限制连接的服务。基于这些限制来限制工人的数量也是很重要的。当最多有 10 个连接时，您不希望有 20 个工人！

### 独角兽/乘客

如果你使用 Unicorn 或 Passenger 作为你的网络服务器，还有一个步骤来确保一切都设置好了。也就是在服务器之后运行的块上定义队列。对于 unicorn(仅在设置了`preload_app true`时需要):

```
# config/unicorn.rb
after_fork do |server, worker|
  SuckerPunch.config do
    queue name: :log_queue, worker: LogWorker, workers: 10
  end
end
```

对于乘客:

```
# config/initializers/sucker_punch.rb
if defined?(PhusionPassenger)
  PhusionPassenger.on_event(:starting_worker_process) do |forked|
    SuckerPunch.config do
      queue name: :log_queue, worker: LogWorker, workers: 10
    end
  end
end
```

### 延伸阅读:

*   [并发和数据库连接，Heroku 开发中心](https://devcenter.heroku.com/articles/concurrency-and-database-connections#connection-pool)
*   [赛璐璐的常见问题](https://github.com/celluloid/celluloid/wiki/Frequently-Asked-Questions)
*   [Celluluid 的螺纹安全说明](https://github.com/celluloid/celluloid/wiki/Thread-safety-notes)

## 分享这篇文章