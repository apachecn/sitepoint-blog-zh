# 延迟作业最佳实践

> 原文：<https://www.sitepoint.com/delayed-jobs-best-practices/>

![Best Practice concept stamp](img/cf0aa9332a132360454d72244bc83db2.png)

在 HTTP 请求处理周期之外，异步执行任务的需求迟早会出现在所有 web 应用程序中。可以帮助你处理这种需求的一个法宝是[延迟工作](https://rubygems.org/gems/delayed_job)。还有其他的，像 [resque](https://rubygems.org/gems/resque) 和 [Sidekiq](https://rubygems.org/gems/sidekiq) 。我已经使用了很多，我不得不说我真的很喜欢`delayed_job`，因为它与我的 RDBMS 后端(通常是 MySQL 或 PostgreSQL)集成得如此之好。

有很多关于延迟工作的帖子，有大量有用的信息。在本文中，我将介绍我在处理延迟作业时应用的一些实践，例如:

*   保存延迟作业的表上的增强功能
*   排队作业时的良好实践，包括自定义延迟作业
*   使用 Rails 控制台管理作业
*   使用 Web 界面管理作业
*   使用延迟作业进行测试
*   标记日志记录

我将在我的演示应用程序中使用 Rails 和 ActiveRecord，所以请随意创建一个 Rails 应用程序并跟随它。你需要将`delayed_job`添加到你的**gem 文件**中。

## 用于保存延迟作业的表

如果您运行以下命令:

```
rails generate delayed_job:active_record
```

您将获得以下迁移:

```
def self.up
  create_table :delayed_jobs, :force => true do |table|
    table.integer  :priority, :default => 0, :null => false
    table.integer  :attempts, :default => 0, :null => false
    table.text     :handler,                 :null => false
    table.text     :last_error
    table.datetime :run_at
    table.datetime :locked_at
    table.datetime :failed_at
    table.string   :locked_by
    table.string   :queue

    table.timestamps
  end

  add_index :delayed_jobs, [:priority, :run_at], :name => 'delayed_jobs_priority'
end
```

### 迁移的建议优化

#### 在队列列上添加索引

您需要在`queue`前查询您的工作。如果您的数据库表中有很多作业，那么通过`queue`进行查询将会扫描整个表，并且需要花费很多时间来完成。在这种情况下，索引非常有用。

```
# optimization #1
#
def self.up
  create_table :delayed_jobs, :force => true do |table|
    # ... #
  end

  # ... #
  add_index :delayed_jobs, [:queue], :name => 'delayed_jobs_queue'
end
```

#### MySQL `longtext`优化

延迟作业收到的一些异常可能会很长。如果您使用的是 MySQL，`handler`和`last_error`字段可能不够长。将它们的数据类型改为`longtext`来避免这个问题。如果您使用 PostgreSQL，这将不是问题。

```
# optimization #2
#
def self.up
  create_table :delayed_jobs, :force => true do |table|
    # ... #

    # replace the migration for column +handler+ with
    table.column :handler, :longtext, :null => false

    # replace the migration for column +last_error+ with
    table.column :last_error, :longtext

    # ... #
  end

  # ... #
  add_index :delayed_jobs, [:queue], :name => 'delayed_jobs_queue'
end
```

#### 延迟实体的列

通常，创建作业是为了处理与业务实体相关的后台任务。例如，向用户发送电子邮件。

我使用两列来存储对这个业务实体实例的引用。因此，我能够快速查询与特定业务实体类型或实例相关的职务条目。我还添加了相应的索引，以便这些查询能够快速完成。

```
# optimization #3
#
def self.up
  create_table :delayed_jobs, :force => true do |table|
    # ... #

    # replace the migration for column +handler+ with
    table.column :handler, :longtext, :null => false

    # replace the migration for column +last_error+ with
    table.column :last_error, :longtext

    # ... #

    table.integer :delayed_reference_id
    table.string  :delayed_reference_type
  end

  # ... #
  add_index :delayed_jobs, [:queue],                  :name => 'delayed_jobs_queue'
  add_index :delayed_jobs, [:delayed_reference_id],   :name => 'delayed_jobs_delayed_reference_id'
  add_index :delayed_jobs, [:delayed_reference_type], :name => 'delayed_jobs_delayed_reference_type'
end
```

稍后，我将向您展示我是如何填充这两列的。

## 排队作业

### 不要删除失败的作业

默认情况下，延迟工作线程一旦达到最大尝试次数，就会删除失败的作业。当您想要找到问题的根源并进行故障排除时，这可能会很烦人。我的建议是将失败的作业留在数据库中，并设置一个流程来处理失败的作业。

您可以使用以下语句设置此配置属性的值:

```
Delayed::Worker.destroy_failed_jobs = false
```

在延迟的作业初始化器中。

### 考虑最大运行时间值

您可能需要考虑最大运行时配置属性应该具有什么值。默认为`4.hours`。但是，如果您不希望有如此长时间运行的任务，最好减少该值。当达到这个限制时，这样做将杀死被延迟的工人，并允许作业失败，以便另一个工人可以继续它。此外，对于那些你期望在短时间内运行但花费了更长时间的任务，你会得到通知(通过在`error`和`failure`钩子上的电子邮件通知)。

另一方面，我在`4.hours`还不够的项目上做过延期工作，我必须增加那个价值。*咳嗽*

```
Delayed::Worker.max_run_time = 15.minutes
```

会将此限制设置为 15 分钟。

### 不要使用一个队列

不要使用`default`或只使用一个队列。这不会扩展。即使在项目开始时只有几个工人/工作，也要从给队列起一个有意义的名字开始。将具有不同业务上下文的作业分发到不同的队列。

例如，如果你有注册邮件，将它们放在`registration_emails`队列中，而给你的应用用户的电子邮件通知可能会放在`email_notifications`队列中。

如果您有不同的队列，它们更容易管理。如果有一个您想要手动或用脚本处理的异常，您可以停止工作器并删除所有的`email_notifications`队列条目。如果您的所有作业都在同一个队列中，那么这样的任务处理起来会更加复杂。

将作业分发到不同的队列还允许启动不同的工作线程来处理不同的队列。我的建议是每个队列至少有一个工人。因此，您将并行执行您的作业。

### 使用自定义延迟作业

您可以使用`handle_asynchronously`来声明对方法的调用应该异步处理。我很少使用这种技术。我更喜欢在我的项目的**作业**文件夹中声明定制的延迟作业对象。使用定制的延迟作业允许我微调我在`delayed_jobs`表中存储的内容。

下面是一个自定义作业示例。假设我有一份处理视频的工作(我的应用程序是`VideoStreamer`)。

```
# app/jobs/video_streamer/process_video_job.rb
#
module VideoStreamer
  class ProcessVideoJob < Struct.new(:video_id)
    # ... #
  end
end
```

我在 **jobs** 文件夹中创建了 **video_streamer** 文件夹，以及 **processvideo_job.rb** 文件来保存延迟的定制作业代码。自定义作业的类以我的应用程序的名称命名。另外，我在类名后面加上了后缀`Job`。使用`Struct`，我存储视频的 ID(这个作业相关的业务实体实例)。该属性将被命名为`video_id`。

#### 实现排队挂钩

我为`enqueue`钩子注册了一个钩子处理程序。每当这个类的新作业被放入队列时，钩子处理我想要完成的项目。

```
# app/jobs/video_streamer/process_video_job.rb
#
module VideoStreamer
  class ProcessVideoJob < Struct.new(:video_id)
    def enqueue(job)
      job.delayed_reference_id   = video_id
      job.delayed_reference_type = 'VideoStreamer::Video'
      job.save!
    end
  end
end
```

如您所见，当一个作业排队时，视频(业务实体)被存储。

当然，在`enqueue`中也有想做更复杂事情的时候。在下面的例子中，只有当`status`有正确的值时，我才接受入队。然后，我将`status`更新为值`processing`,以表明正在处理特定的视频实例:

```
module VideoStreamer
  class ProcessVideoJob < Struct.new(:video_id)
    def enqueue(job)
      check_and_update_status

      job.delayed_reference_id   = video_id
      job.delayed_reference_type = 'VideoStreamer::Video'
      job.save!
    end

    private

    def check_and_update_status
      video = VideoStreamer::Video.find video_id
      raise StandardError.new("Video: #{video.id} is not on status 'new' (status: #{video.status}") unless video.status == 'new'
      video.status = 'processing'
      video.save!
    end
  end
end
```

#### 实施成功挂钩

为了在作业成功处理后更新状态，我实现了`success`钩子:

```
module VideoStreamer
  class ProcessVideoJob < Struct.new(:video_id)
    # ... #

    def success(job)
      update_status('success')
    end

    private

    def update_status(status)
      video = VideoStreamer::Video.find video_id
      video.status = status
      video.save!
    end

    # ... #
  end
end
```

#### 工具错误挂钩

例如，您的定制作业中的`error`挂钩可以发送电子邮件警告或更改相关业务实体的状态。`error`方法可以访问`exception`,后者会给出关于错误的信息。请注意，该错误表示暂时失败，如果还有尝试，另一个工作进程将尝试再次运行您的后台任务。

```
module VideoStreamer
  class ProcessVideoJob < Struct.new(:video_id)
    def enqueue(job)
      # ... #
    end

    def success(job)
      # ... #
    end

    def error(job, exception)
      update_status('temp_error')
      # Send email notification / alert / alarm
    end

    private

    # ... #
  end
end
```

#### 机具故障挂钩

例如，当作业永久失败且不会重试时，使用失败挂钩发送电子邮件警报或更改相关业务实体的状态。如果已将失败的作业配置为保留在数据库表中，则可以手动重试该作业。

```
module VideoStreamer
  class ProcessVideoJob < Struct.new(:video_id)
    def enqueue(job)
      # ... #
    end

    def success(job)
      # ... #
    end

    def error(job, exception)
      # ... #
    end

    def failure(job)
      update_status('failure')
      # Send email notification / alert / alarm / SMS / call ... whatever
    end

    private

    # ... #
  end
end
```

#### 机具执行吊钩-下放-提升

这个挂钩是最关键的，否则工作什么都做不了。您的实现应该非常简单，并将实际工作委托给模型或其他服务对象。真正的实现不应该是`perform`实现的一部分。这将确保您可以执行实现的逻辑，而不必有延迟作业的实例。此外，在单元测试中测试逻辑会更容易。因此，最起码:

```
module VideoStreamer
  class ProcessVideoJob < Struct.new(:video_id)
    def enqueue(job)
      # ... #
    end

    def success(job)
      # ... #
    end

    def error(job, exception)
      # ... #
    end

    def failure(job)
      # ... #
    end

    def perform
      video = VideoSteamer::Video.find video_id
      video.process!
    end

    private

    # ... #
  end
end
```

提出任何异常都是绝对必要的，这样它们就可以由工人来处理(工人将根据需要调用`error`和`failure`)。不要轻信`perform`可能提出的任何例外。在上面的例子中，`video.process!`可能会引发一个我允许冒泡的异常。定位商业实体也是如此。我使用`#find()`并给出`video_id`，如果找不到业务实体，这将引发一个异常。例如，不要使用`find_by_id()`，它不会引发这样的异常。

如果执行任务的服务对象在需要的时候没有抛出错误(可能它返回`false`)，那么`perform`应该抛出一个错误。

```
def perform
  video = VideoSteamer::Video.find video_id
  raise StandardError.new("Failed to process video with id: #{video.id}") unless video.process?
end
```

## 通过 Rails 控制台管理作业

延迟作业包括启动/停止作业的脚本界面。但是，在某些情况下，我希望停止运行工作线程，并手动启动/停止特定的作业。我通常在开发环境中这样做，在那里我很少有后台运行人员运行。我使用特定的`Delayed::Job` API 调用从控制台手动运行它们。此外，在生产环境中，我曾经遇到过这样的情况，我不得不停止运行工人，并使用相同的技术对作业进行细粒度的手动操作。

这里有一些我认为在这种情况下有用的 API 命令:

### 用相应的模型查询 delayed_jobs 表

我使用`Delayed::Job`模型来查询`delayed_jobs`表。例如，以下语句返回属于`email_notification`队列并引发错误的作业:

```
Delayed::Job.where(queue: 'email_notifications').where.not(last_error: nil)
```

请理解，您可以使用您的`delayed_jobs`表中的任何列来构建您的`where`子句。

### 哪个班级会处理我的工作？

当我想知道哪个`class`将处理一个作业(通常是一个失败的作业)时，我查询`handler`:

```
handler = Delayed::Job.last.handler
```

这是一个 YAML 序列化对象实例。

#### 自定义延迟作业

下面是我的`VideoStreamer::ProcessVideoJob`的输出示例:

```
"--- !ruby/struct:VideoStreamer::ProcessVideoJob\nvideo_id: 68\n"
```

这个输出告诉我，特定的延迟作业实例是一个处理 ID = 68 的视频的任务。

#### 申请邮件

如果您异步发送电子邮件，那么 YAML 序列化会有所不同:

```
"--- !ruby/object:Delayed::PerformableMailer\nobject: !ruby/class 'UserNotifierMailer'\nmethod_name: :new_user_registration\nargs:\n- 35\n"
```

所有序列化的邮件程序都是`!rubyobject:Delayed::PerformableMailer`，后面是你的应用程序中的邮件程序类(见`!ruby/class 'UserNotifierMailer'`)。您可以看到用于发送电子邮件的实际方法(`new_user_registration`)及其参数(`35`)。

#### 设计邮寄者

如果你异步发送你的设计电子邮件(我使用[device-async](https://github.com/mhfs/devise-async)gem 来做这件事)，那么 YAML 序列化看起来像这样:

```
"--- !ruby/object:Delayed::PerformableMethod\nobject: !ruby/object:Devise::Async::Backend::DelayedJob {}\nmethod_name: :perform\nargs:\n- :confirmation_instructions\n- User\n- '317'\n- daD9bVQ2d_kaR3abyS7X\n- {}\n"
```

同样，您可以看到哪封电子邮件可能会失败，以及它的运行时参数。在上面的示例中，失败的电子邮件是向 ID = 317 的用户发送确认指令的邮件。

### 反序列化同一队列中的不同作业

如果同一队列中有不同的作业类型，则很难管理按作业类型分组的作业。例如，您可能想要计算一个名为`solr_indexing`的队列中每种类型的排队作业的数量，该队列为后台索引处理各种类。

如果您的`delayed_jobs`表上仅有的信息是`handler`(即`delayed_reference_type`没有帮助)，您将不得不使用`handler`。

重点是`handler`根据我们上面讨论的序列化的类存储不同的序列化对象。

`YAML.load(dj.handler)`将反序列化你的序列化对象。

#### 自定义延迟作业

如果排队作业是自定义延迟作业

```
dj = Delayed::Job.last
dj.handler                   # Assume: "--- !ruby/struct:VideoStreamer::ProcessVideoJob\nvideo_id: 68\n"
job = YAML.load(dj.handler)  # +job+ will be instance of +VideoStreamer::ProcessVideoJob+ struct with
                             # +video_id+ attribute set to +68+
```

#### 申请邮件

如果是邮件程序对象，则实例化的作业是一个`Delayed::PerformableMailer`:

```
=> #<Delayed::PerformableMailer:0x0000000a050898 @object=UserNotifierMailer, @method_name=:new_user_registration, @args=[35]>
```

如您所见，这个处理程序响应`object`，它是实际的邮件程序实例，`method_name`，它是将用于发送电子邮件的邮件程序实例方法，以及`args`，它包含该方法的运行时参数。

#### 设计邮寄者

当处理程序是一个`Devise`邮件程序时，就多了一层抽象。作业类型为`Delayed::PerformableMethod`:

```
=> #<Delayed::PerformableMethod:0x0000000a0bb800 @object=#<Devise::Async::Backend::DelayedJob:0x0000000a0bf090>, @method_name=:perform, @args=[:confirmation_instructions, "User", "317", "daD9bVQ2d_kaR3abyS7X", {}]>
```

正如您所看到的，这个函数响应`object`，它是`Devise::Async::Backend::DelayedJob`的一个实例；`method_name`，它是调用这个实例的方法；以及`args`，它包含这个方法的运行时参数。这个`args`数组包含实际的 email 方法及其实参。

`YAML.load(dj.handler)`可能返回不同的对象类型，如果您想编写一个脚本来操作属于同一个队列的全部或部分作业，您可能需要实现某种类型的`is_a?(....)`逻辑。

### 出错作业的例外是什么？

当我想查看某个作业的异常详细信息时，我会检查`last_error`列。

### 从 Rails 控制台运行作业，无需排队

假设您有一个作业，并且希望从 rails 控制台手动运行它，但是，**您不希望将它放入延迟作业队列生命周期循环**:

```
# This will run your job but will not go through the delayed job lifecycle loop
job = VideoStreamer::ProcessVideoJob.new(68)
job.perform
```

它可以工作，但是不会执行任何注册的钩子。

我很少使用这种方法，但它有时会派上用场。

### 在 Rails 控制台中实例化一个 Worker

当您希望再次运行失败的作业，但希望通过控制台而不是后台运行的工作线程来完成时，这非常方便:

```
dw = Delayed::Worker.new
```

简单，嗯？

### 从 Rails 控制台中运行失败的作业

假设您有一个失败的作业，并且想要从控制台中再次手动运行它:

```
dw = Delayed::Worker.new
dj = Delayed::Job.last # assuming that the last job is the failed one, otherwise use a proper query to
                       # locate it
dw.run dj
```

就是这样。如果作业再次失败，您的 worker 将运行该作业并调用相应的错误和失败挂钩。如果成功，它将从队列中删除它。

## 使用 Web 界面管理作业

使用 [delayed_job_web](https://rubygems.org/gems/delayed_job_web) 接口访问您的排队作业。将以下行添加到您的路线中:

```
mount DelayedJobWeb => "/delayed_job"
```

这将允许您使用类似`https://www.myapp.com/delayed_job`的地址访问管理界面。

我还有一个 **delayed_job_web.rb** 初始化器(在我的**配置/初始化器**文件夹中):

```
DelayedJobWeb.use Rack::Auth::Basic do |username, password|
  # authenticate
  user = User.find_by_username(username)
  return false unless user.authenticate(password)

  # authorize. I am using cancancan for authorization. You can use any other authorization gem you see fit.
  ability = Ability.new(user)
  can = ability.can? :manage, Delayed::Job
  raise CanCan::AccessDenied unless can
  true
end
```

这允许我对访问`/delayed_job`的请求进行认证和授权。

## 延迟作业测试

### 测试时不要排队或嘲笑

当测试您的应用程序代码时(使用任何类型的测试、单元测试、集成测试或 UI 测试)，不要排队或模仿您的延迟工作任务。对你来说，这可能是一种奇怪的做法，但对我来说，它已经被证明是非常宝贵的。我需要看看它们是否破裂。如果任务耗时太长或者有递归，我可能会决定模仿这个工作。但总的来说，我不会嘲笑我的工作。

要让延迟的作业简单地执行您的任务而不将其排队，请在初始化器中执行以下操作:

```
Delayed::Worker.delay_jobs = !%w[ test ].include?(Rails.env)
```

上面这个不会在`test`环境中排队作业。

### 测试时禁用立即执行

但是，可能会有一些测试需要测试队列功能。我用特殊的标签包装这些测试，并要求延迟作业将它们排队。

#### RSpec

当使用`RSpec`时，我有一个`around(:each)`配置，允许使用一个`:delayed_job`标签:

```
# spec/spec_helper.rb

RSpec.configure do |config|
  # ... other config here ... #

  config.around(:each, :delayed_job) do |example|
    old_value = Delayed::Worker.delay_jobs
    Delayed::Worker.delay_jobs = true
    Delayed::Job.destroy_all

    example.run

    Delayed::Worker.delay_jobs = old_value
  end

  # ... other config here ... #
end
```

在示例运行之前，我启用了作业队列并删除了所有现有的作业。在示例运行之后，我将延迟的作业队列设置回原来的状态。

现在，当我想编写一个使用延迟作业队列的规范时，我会执行以下操作:

```
it 'should queue the job`, delayed_job: true do
  ...
end
```

#### 黄瓜

```
# features/support/hooks.rb
Around('@delayed_job') do |scenario, block|
  old_value = Delayed::Worker.delay_jobs
  Delayed::Worker.delay_jobs = true
  Delayed::Job.destroy_all

  block.call

  Delayed::Worker.delay_jobs = old_value
end
```

类似于`RSpec`配置，我使用一个带有标签`@delayed_job`的`Around`钩子。然后，我标记了我想要使用真实队列的场景:

```
@delayed_job
Scenario: As a User when I sign up there is a new user registration email queued
```

## 标记日志记录

您可能知道，Rails 支持[标记日志](http://api.rubyonrails.org/classes/ActiveSupport/TaggedLogging.html)。我还配置了延迟作业来使用标记日志记录。

为了做到这一点，我使用了`Delayed::Plugin`技术:

```
module Delayed
  module Plugins
    class TaggedLogging < Delayed::Plugin
      Delayed::Worker.logger = Rails.logger

      callbacks do |lifecycle|
        lifecycle.around(:execute) do |worker, *args, &block|
          Rails.logger.tagged "Worker:#{worker.name_prefix.strip}", "Queues:#{worker.queues.join(',')}" do
            block.call(worker, *args)
          end
        end

        lifecycle.around(:invoke_job) do |job, *args, &block|
          Rails.logger.tagged "Job:#{job.id}" do
            block.call(job, *args)
          end
        end
      end
    end
  end
end
```

如您所见，我正在捕捉钩子`around(:execute)`和`around(:invoke_job)`，并使用`Rails.logger`来实现标记日志记录。我记录了工人姓名、队列和作业 id。

不要忘记在你的`delayed_job`初始化器中注册你的`Delayed::Plugin`子类:

```
Delayed::Worker.plugins << Delayed::Plugins::TaggedLogging
```

顺便说一下，如果你想知道你可以挂钩到哪些事件，请参见此处的[行。](https://github.com/collectiveidea/delayed_job/blob/master/lib/delayed/lifecycle.rb#L4)

## 结论

在这篇文章中，我介绍了一些使用延迟工作 gem 处理后台工作的实践。我希望这些建议对你有所帮助。

## 分享这篇文章