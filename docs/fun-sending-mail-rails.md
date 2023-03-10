# 在铁路上发送邮件的乐趣

> 原文：<https://www.sitepoint.com/fun-sending-mail-rails/>

[https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/296598536&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&visual=true](https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/296598536&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&visual=true)



冒着和自己约会的风险，我记得我们没有电子邮件的世界。那是一个孤立的时代，异步通信需要“应答”或“传真”机器。真正绝望的人转向纸和笔，需要一队卡车和飞机来确保信息到达预定的目的地。哦，那么这些黑暗日子里最可怕的是什么呢？Spam 是(据说)人们吃的加工过的肉！

*战栗*

谢天谢地，我们已经从那个时代走了很长一段路。电子邮件已经席卷了整个世界，从极客的工具上升到门外汉的工具，再上升到营销人员和小偷使用的恼人工具的顶峰。尽管许多人预测电子邮件作为一种交流方式将会消亡，但它仍将继续存在。我们都使用它，作为一名 Rails 开发人员，您无疑需要它来与您的用户交流并向他们发送加工过的肉。不要！我的意思是，令人难以置信的重要公报，将改善他们的生活每一天！

今天，我们将讨论使用 Rails 发送电子邮件的基础知识，以及一些您应该了解的问题、专业技巧、服务和精华。

## 基础知识

Rails 使用 ActionMailer 模块来处理电子邮件。动作邮件指南非常棒，非常值得一读。我将介绍一些指南，但是你应该自己阅读。

### 创建邮件程序

为了发送邮件，Rails 需要一个或多个“mailers”，它们是`ActionMailer::Base`的子类。

```
class VikingMailer < ActionMailer::Base
  # Default Mail Values
  default from: 'bloodyvikings@breakfast.com', to: { User.pluck(:email) }
end
```

在上面的代码片段中，我添加了几个默认值。您可以在单个邮件程序方法中覆盖这些值。其他可以有默认值的项目有`subject`和`headers`。

使用 Rails 生成器创建邮件程序是一个好主意

```
rails g mailer VikingAMailer
create  app/mailers/viking_mailer.rb
invoke  erb
create    app/views/viking_mailer
invoke  test_unit
create    test/mailers/viking_mailer_test.rb
create    test/mailers/previews/viking_mailer_preview.rb
```

生成器创建邮件程序文件、邮件程序的视图文件夹、邮件程序的测试文件和预览文件。看起来很像生成控制器，不是吗？确实是。

Mailer 类中的每个方法都类似于 Rails 控制器上的方法。如果你想给新用户发送一封欢迎邮件，在 **app/views/viking_mailer** 目录中给你的 mailer 类添加一个`welcome_email`方法和一个同名的视图。

```
class VikingMailer < ActionMailer::Base
  # Default Mail Values
  default from: 'bloodyvikings@breakfast.com', to: { User.pluck(:email) }

  def welcome_email(user)
    @user = user
    # I am overriding the 'to' default
    mail(to: @user.email, subject: 'Do you have any spam?')
  end
end
```

**app/views/Viking _ mailer/welcome _ email . html . erb**文件可能如下所示:

```
<p>Dear <%= @user.name %>,</p>

<p>We want Spam for breakfast.</p>

<p>Yours truly,</p>
<p>Bloody Vikings</p>
```

注意，`@user`实例变量在视图中是可用的，就像常规的控制器和视图一样。顺便说一下，邮件视图也可以有布局。

只要我们谈到视图，就可以指定电子邮件视图的格式，就像 Rails 视图一样。上面的视图是 **welcome.html.erb** 。如果我们添加一个 **welcome.text.erb** ，那么 ActionMailer 会将它添加到传出的 MIME 消息中。这提供了对你的电子邮件的纯文本和 HTML 版本的控制，这实现了你最疯狂的哑剧梦想。

### [配置](http://guides.rubyonrails.org/action_mailer_basics.html#action-mailer-configuration)

为了发送电子邮件，需要正确配置 ActionMailer。实际发送电子邮件有几种方法，但最常用的方法是使用简单邮件传输协议(SMTP)。

> [这里](http://email.about.com/cs/standards/a/smtp.htm)是一篇关于 SMTP 基础的好文章。

指南中常见的配置示例:

```
config.action_mailer.delivery_method = :sendmail
# Defaults to:
# config.action_mailer.sendmail_settings = {
#   location: '/usr/sbin/sendmail',
#   arguments: '-i -t'
# }
config.action_mailer.perform_deliveries = true
config.action_mailer.raise_delivery_errors = true
config.action_mailer.default_options = {from: 'no-reply@example.com'}
```

该配置将使用 UNIX `sendmail`程序，引发传送错误，并使您的默认发送者`no-reply@example.com`。正如您可能已经猜到的那样，`perform_deliveries`选项是告诉 Rails 永远不要发送任何邮件的一种方式。在某些情况下，比如在开发环境中，您可能不想浪费发送邮件的时间。

## 下一关

让我们更进一步，好吗？现在你已经了解了发送电子邮件的基本知识，还能做些什么呢？很多。

### 头球

“电子邮件”的一部分是标题。它由*字段*组成，如收件人/发件人地址、主题、消息 ID 和发送日期。还有许多其他的，您可以将它们作为散列参数传递给`mail`方法:

```
def welcome_email(user)
  ...rest of method...
  mail(to: @user.email, subject: "Word", reply_to: 'dev@null.com')
end
```

事实上，`mail`方法只接受一个用于头、交付选项和模板选项的散列参数。通过“delivery_options”，我的意思是您可以指定通常驻留在 Rails 应用程序配置中的 [SMTP 邮件程序设置](http://guides.rubyonrails.org/action_mailer_basics.html#sending-emails-with-dynamic-delivery-options)。模板选项类似于邮件模板的路径。

### 指定内嵌格式

`mail`方法只接受一个散列。嗯，那个，还有一个街区。您可以向`mail`方法传递一个块来指定如何处理特定的格式。

```
def welcome_email(user)
  ...rest of method...
  mail(to: @user.email, subject: "Word", reply_to: 'dev@null.com') do |format|
    format.html { render 'cool_html_template'}
    format.text { render text: 'Get a real mail client!'}
  end
end
```

现在它看起来真的像一个 Rails 控制器。Rails 的核心团队的确很聪明！

### 奇特的电子邮件地址格式

如果您有收件人的姓名，请随意将您的`to`电子邮件地址格式化成这样:

```
#{@user.name} <#{@user.email}>
```

用户会觉得你真的认识他们，而不是垃圾邮件发送者。

### 附件

如果不能发送附件，电子邮件有什么用？谢天谢地，ActionMailer 让发送附件变得轻而易举。只需在 Mailer 方法中的`attachments`散列中添加一个`File`。

```
def welcome_email(user)
  attachments["viking-breakfast.mp4"] = File.read('spamspamspam.mp4')
  mail....
end
```

现在，维京视频将作为电子邮件的附件出现。但是，*内联*附件呢？

你会问，什么是内嵌附件？只有调味加工肉以来最伟大的事情！最好的例子是你超级漂亮的 HTML 邮件中的图片。内联附件允许你在邮件内容中包含图片，而不是像一些愚蠢的猫视频或。点文件(不管怎样，到底是什么东西？).

令人高兴的是，添加内联附件也很简单:

```
attachments.inline['viking-header.jpg'] = File.read('viking-header.jpg')
```

然后，在你看来，这样做:

```
<%= image_tag attachments['viking-header.jpg'].url %>
```

你的 HTML 邮件一定会引起他们的注意。

### 截击机

拦截器是电子邮件发送过程中的钩子。ActionMailer 提供了这个框架，允许在电子邮件发送到互联网之前对其进行修改。你会问，为什么要这么做？一个很好的答案是:防止你的暂存/QA 环境向你的用户发送电子邮件。可以创建一个拦截器来检查 Rails 环境(开发、生产、登台),并将电子邮件发送到登台地址，而不是预期的收件人。现在，您可以在将电子邮件发布到生产环境之前看到它的样子。稍后，我会给你看一个宝石做这件事。

要创建拦截器，定义一个 Ruby 类，编写 hook 方法，并将拦截器添加到配置中。

```
# From the guides
class VikingEmailInterceptor
  def self.delivering_email(message)
    message.to = ['bloddyvikings@breakfast.com']
    message.subject = "We don't have any!"
  end
end
```

然后，在一个初始化器中(比如**config/initializer/Viking _ email _ interceptor . Rb**):

```
ActionMailer::Base.register_interceptor(VikingEmailInterceptor) if Rails.env.staging?
```

现在你所有的邮件都会被`VikingEmailInterceptor`捕获，它们只会被发送到“bloodyvikings@breakfast.com”。

### 异步发送电子邮件

大多数情况下，发送电子邮件是一种“一劳永逸”的操作。当调用邮件服务器或构建邮件视图或其他东西时，可能会有一些延迟。异步发送邮件通常是可取的，因为这意味着用户不必等待他们在应用程序中看不到的东西。

在 Rails 3 . x–4.1 . x 中，后台处理电子邮件的任务落在了开发人员身上。这里有一个[用 Sidekiq](http://blog.remarkablelabs.com/2013/01/using-sidekiq-to-send-emails-asynchronously) 做这件事的很好的例子。这可以在 Rails 3 或 Rails 4 中运行，但是您必须安装 Sidekiq(以及 Redis)并准备好运行。

Rails 4.2 提供的 **ActiveJob** 让这变得简单了一些。ActiveJob 是各种排队系统的标准门面，例如 [Sidekiq](http://sidekiq.org/) 、 [Resque](http://resquework.org/ "Resque") 或[延迟作业](https://github.com/collectiveidea/delayed_job "Delayed Job")。Rails 中第一个利用新的 ActiveJob 队列的方法是 ActionMailer 上的`deliver_later`方法！当然，你需要配置一个前面提到的排队系统，但是一旦你这样做了，你的邮件后台将会很简单。

> 顺便说一下，如果你需要帮助选择或建立一个排队系统，看看这个系列！

## 服务

电子邮件是非常非常大的生意。因此，你可能想知道你的电子邮件是否到达你的维京人，它们是否被打开，你发送了多少封电子邮件，等等。如果您已经将 Rails 应用程序配置为通过 sendmail 使用本地 SMTP 服务器，那么缩放和跟踪就很难实现。使用服务集中发送你的电子邮件，并提供我刚才提到的许多功能。

根据最新统计，大约有 12.5 亿电子邮件服务提供商。以下是一些大狗:

*   [send grid](http://sendgrid.com/)——可能是最知名的电子邮件服务提供商。我们把它作为 Heroku 插件使用，它的界面非常适合调查你的电子邮件活动。
*   MailChimp 的 Mandrill–提供现收现付的价格，非常合理，还有一个不错的 API。我们实际上使用了配置为使用 Mandrill 的 Postfix(http://www.postfix.org/ ),因此我们没有任何特殊的 ActionMailer 配置来使用该服务。所有的配置都是在后缀上执行的。(注意:后缀的东西不需要使用 Mandrill)
*   [邮戳](https://postmarkapp.com)——我没用过邮戳，但是看起来很有意思。它增加了接收电子邮件、将其转换为 JSON 并将其发送到 Rails 应用程序的能力。
*   亚马逊 SES–亚马逊简单的电子邮件服务以非常合理的价格提供了与之前服务相同的内容。我没有用过它，但它在我的清单上，仅仅因为它是亚马逊的。
*   很多很多，比如 [Mailjet](https://www.mailjet.com/?gclid=CNCViZzLqcACFU4F7Aod3yoAcw) 、 [SocketLabs](http://www.socketlabs.com/) 、[邮费](http://postageapp.com/)。

这些服务的配置超出了本文的范围，但是如果您对电子邮件很认真，您必须使用一个。

另一项服务有点不同，那就是 [Apostle.io](http://apostle.io/) Apostle.io 提供了使用他们的服务来定义你的电子邮件模板的能力。这将把你的邮件视图从 Rails 中移除并放到 Apostle.io 上，这样非 Rails 开发者和维京人就可以创建/修改模板了。它非常简单地与 Rails 和我上面提到的大多数服务集成。

## 常见问题

Rails 的一切都有一个尖锐的边缘，需要你非常小心，发送电子邮件也不例外。这里只是几个例子，但是我确信每个 Rails 开发者都可以添加一个。

和控制器一样，邮件程序没有任何请求上下文。缺失的上下文有很多信息，但最重要的是“主机”值。该值用于形成链接。要解决这个问题，请设置`config.action_mailer.default_url_options = {host: ‘vikings.com’}`或将其传递给`url_for`助手。你可以使用`only_path`来生成相对网址，但这对电子邮件来说没用。同样，所有的`_path` url 助手在电子邮件中都是无用的，所以总是使用`_url`助手

下一个是测试你的邮件。我不认为有很多人这样做，这就像吃垃圾邮件而不检查保质期。真的很遗憾，因为测试一个邮件程序非常简单。事实上，指南中有一整节都是关于它的。这个例子来自于:

```
class UserMailerTest < ActionMailer::TestCase
  test "invite" do
    # Send the email, then test that it got queued
    email = UserMailer.create_invite('me@example.com',
                                     'friend@example.com', Time.now).deliver
    assert_not ActionMailer::Base.deliveries.empty?

    # Test the body of the sent email contains what we expect it to
    assert_equal ['me@example.com'], email.from
    assert_equal ['friend@example.com'], email.to
    assert_equal 'You have been invited by me@example.com', email.subject
    assert_equal read_fixture('invite').join, email.body.to_s
  end
end
```

现在，无论您是否害怕在一次测试中测试多项内容或任何其他风格问题，关键是:测试邮件程序类似于测试控制器。动手吧。

Rails 4.1 增加了 ActionMailer 预览，这使得快速抽查您的电子邮件变得很容易。当您使用 Rails 4.1 或更高版本生成邮件程序时，它会在测试目录中创建一个“预览”文件。此预览文件允许您预览邮件程序的各种邮件方法。这里是指南中的[示例。](http://edgeguides.rubyonrails.org/4_1_release_notes.html#action-mailer-previews)

我还没有使用过 ActionMailer 预览版，只是简单地查看了一下。ActionMailer 预览不允许您更改收件人或邮件程序中的其他变量，因此您必须对它们进行硬编码。我有其他更喜欢的选项来查看我的电子邮件。来，让我给你看…

## 宝石

有很多很多 gem 可以用 Rails 和 mail 做各种事情。这里有两个我最喜欢的:

*   [收件人拦截器](https://github.com/croaky/recipient_interceptor)–提供一个简单的拦截器，将你的应用程序发送的所有电子邮件的收件人更改为你指定的一个或多个电子邮件地址。正如我上面提到的，这是一个典型的例子，它改变了测试环境，向测试团队发送电子邮件进行验证。还可以加上主语前缀(像`[STAGING]`)。看一下代码，看看创建拦截器有多简单。
*   Mailcatcher 创建了一个简单的 SMTP 服务器，它捕获 Rails 应用程序发送的所有电子邮件，并提供一个 web 界面来查看它们。我已经使用 MailCatcher 很久了，非常喜欢它。依我拙见，这比 ActionMailer 预览版好得多。

## 签署

现在您已经了解了使用 Rails 发送邮件的所有内容。这篇文章可能会一直写下去，所以我试着混合一些基础知识和一些下一级的知识。我猜你们中的一些人会说:

*   “他为什么不掩护 X？？?"
*   “哦，他完全误解了你！!"
*   “他真的吃垃圾食品吗？?"

如果是的话，请把这些想法写在评论里，我会把我的回复电邮给你。

## 分享这篇文章