# 用开信刀预览你的 Rails 邮件

> 原文：<https://www.sitepoint.com/preview-your-rails-mail-with-letter-opener/>

![letteropener](img/591de12774ccab12be6422cb034f828a.png)

在本教程中，我将向你展示如何设置和使用 Ryan Bates 的名为“开信刀”的电子邮件预览工具。如果你对使用 Rails 很认真，并且不知道 Ryan 的优秀 Railscasts ，去看看吧。

我们在这里使用的 gem 让你在开发模式下发送申请邮件的那一刻就可以很容易地预览它们。

## 教程详细信息

*   程序:Ruby on Rails，TextMate
*   宝石:[开信刀](https://github.com/ryanb/letter_opener "letter_opener")
*   版本:Ruby 1.8.7 以上，Rails 3.0.10 以上
*   难度:容易
*   预计完成时间:1 小时

## 介绍

假设您有一个向一组用户发送通知电子邮件的 Rails 应用程序。Rails 3 对 Action Mailer 进行了全面的改进，如果您正确配置了应用程序，它将负责发送邮件。但是当你在发展的时候，你可能不想每次做改变的时候都打开邮箱等待邮件的投递。尤其是当你发送 HTML 格式的邮件并且有很多小改动的时候。你需要的是快速简单地预览你的邮件的可能性。这就是开信刀的用武之地。

本教程将带您快速设置一个 Rails 应用程序，编写发送电子邮件的通知邮件程序，并向您展示如何使用开信工具预览它们。

## 快速设置一个新的 Rails 应用程序。

只需转到控制台，设置一个新的 Rails 应用程序:

```
rails new postbox
```

我们称这个应用程序为邮箱。

![img1](img/26a9777442567861ca0ed8742ce0b80c.png)

> "在整个教程中，我们将使用 RVM，ruby 版本管理器，来创建一个特殊的 gemset，以避免污染任何其他项目."

## 安装你的宝石

创建应用程序后，只需进入`postbox/`目录，在您最喜欢的编辑器中打开您的 Gemfile。我们这里用的是 TextMate。

像这样把开信刀宝石添加到你的宝石文件中

```
gem "letter_opener", :group => :development
```

使用`:group => :development`很重要，因为您只想在您的开发环境中使用这个 gem。

![img2](img/8fcaddc2ccf067762ae03dcf1381730b.png)

> "强烈建议将你的宝石分组，这样你就不会在你的环境中混淆它们."

完成之后，运行`bundle install`命令来安装 gems。它看起来像这样:

![img3](img/13d0053a3bb552f54bf273dbb3d20ada.png)

## 步骤 3 设置交付方式

安装完 gem 后，我们需要配置它，以便 Rails 知道在我们的开发环境中使用开信工具。同样，这很简单。在`config/environments/development.rb`文件中只有一行。

在编辑器中打开它，写下:

```
config.action_mailer.delivery_method = :letter_opener
```

![img4](img/04f1c5eac63bb881a274177d5f4f9ebc.png)

现在一切都设置好了，我们可以开始编写通知电子邮件的代码了。

## 步骤 4 编写通知邮件

在控制台上，键入以下命令:

```
rails generate mailer NotificationMailer
```

Rails 会自动为您创建必要的文件。

![img5](img/c3f261fd96ce21bda87520e4cdcdd8a4.png)

让我们一个一个地浏览文件，看看 Rails 为我们烤了什么。

1 app/mailers/notification _ mailer . Rb
2 app/views/notification _ mailer
3 test/functional/notification _ mailer _ test . Rb

### notification_mailer.rb

首先创建的文件是通知邮件程序。它将控制电子邮件的地址和内容等细节。让我们仔细看看。

您将找到发件人地址的默认参数。

![img6](img/e1e65c64a9ae33ab1b64ccff170d5896.png)

> "注意邮件程序总是驻留在`mailers`目录中."

首先，为我们的应用程序更改默认参数。

```
default (:from => "info@postbox.tut", :to=>"somelist@postbox.tut")
```

现在，您从该邮件程序发送的每封电子邮件都将以默认值发送和接收。您可以在任何时候用特定的方法覆盖它。

接下来，我们将编写一个方法(notification_mailer ),实际负责发送电子邮件。它接受一个散列参数。首先，我们设置两个变量，然后告诉 Rails 发送电子邮件。电子邮件的主题设置在最后一行。

```
def notification_mailer(notification)
  @notification = notification
  @url  = "http://postbox.tut/"
  mail(:subject => "New Notification")
end
```

完整的代码如下所示。

![img7](img/d1a74591dc3d6e67a4de6d81a2aeb815.png)

### 视图

接下来，让我们转到相应的`views/`文件夹，那里存放着我们的电子邮件模板。还没有模板，所以我们要设置它们。

![img8](img/11bfba85c906658a3cc96081afe47b10.png)

请注意，我们将为电子邮件设置一个 HTML 和一个文本版本。这样，我们可以发送多部分电子邮件自动没有进一步的努力。

![img9](img/7a36ca62e9f876861e7459e59b88a6b1.png)

> 如果您在相应的文件夹中创建了 HTML **和**文本版本的电子邮件，ActionMailer 将自动发送这两个文件

好了，现在我们已经创建了模板，让我们写一些代码。我们希望用户的收件箱里有漂亮的通知。首先为这个版本的电子邮件写一些 HTML。代码是这样的:

```
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /></pre>
<h1>Notification email.</h1>
<h2><%= @notification.headline %></h2>
<pre>
<%= @notification.content %>

This email was sent out to you, because you are subscribed to the postbox-mailing-list.
If you want to unscribscribe, please use this url: <%= @url %>.
```

请注意，在 h2 和以下段落中，我们通过邮件程序粘贴了一些内容。从我们的`@notification`散列中，我们将填充邮件的标题和内容。可以点击取消订阅时事通讯的链接是由变量`@url`设置的。

请注意，`@url`仅用于演示目的，因为我们不会在本教程中实现退订功能。让我们通过写电子邮件的文本版本来完成这一部分。

```
Notification email.
-------------------
<%= @notification.headline %>

<code><%= @notification.content %>

This email was sent out to you, because you are subscribed to the steamengine-mailing-list.
If you want to unscribscribe, please use this url: <%= @url %>.
```

**确保邮件中的方法(`notification_mailer`)和视图的名称(`notification_mailer.html.erb`)相同。否则，将不会发送电子邮件**

现在我们都完成了…差不多了。我们如何把标题和内容放进邮件里？在我们的应用程序中，我们需要一个通知模型，但是首先让我们进行一个简短的测试。

### 通知 _ 电子邮件 _ 测试. rb

测试和 TDD(测试驱动开发)是 Rails 允许你做的最好的事情。它们在很多情况下都有帮助，无论你有一个大型的分布式团队还是你自己的代码。让我们花一些时间来测试你的邮件。

我们将为我们的邮箱应用程序创建一个单元测试，该应用程序将驻留在`test/unit/`文件夹中，命名为`notification_email_test.rb`。

您可能已经注意到，我们没有使用功能测试，它是由生成器创建的。相反，我们组成一个单元测试。这是因为我们在这里做一些基本的测试。我将让您用功能测试来扩展您的测试。

![img11](img/abf8fdfa32ea26316343cc8049d5c105.png)

```
require 'test_helper'
class NotificationMailerTest < ActionMailer::TestCase

  tests NotificationMailer

  test "notification_email" do

    expected = new_mail
    expected.from    = 'info@postbox.tut'
    expected.to      = 'somelist@postbox.tut'
    expected.subject = "New Notification"
    expected.date    = Time.now

    actual = nil
    assert_nothing_raised { actual = NotificationMailer.notification_mailer(@recipient) }
    assert_not_nil actual

    expected.message_id = '<123@456>'
    actual.message_id = '<123@456>'

    assert_equal expected.encoded, actual.encoded

   end

end
```

让我们来看看测试。首先，我们包括助手，我们一会儿将讨论它。接下来，声明一个继承自`ActionMailer::Testcase`的新类。我们在这里定义了一个名为`notification_email`的测试，我们创建了一个新的电子邮件消息，并给它提供了必要的参数。

接下来，我们将使用我们的`NotifcationMailer`创建实际的电子邮件。使用断言，我们将检查电子邮件是否被正确创建。我们将测试在创建电子邮件时是否引发了任何问题，并检查它是否确实是通过该方法创建的，并且不为空。

通过给这两封邮件分配相同的`message_id`，否则我们的下一个断言将失败。最后一步是将两封邮件相互比较。好，现在我们的代码中有三个断言。让我们检查一下 test_helper。

![img12](img/29936fec27f2ae5f07061be27908151d.png)

```
def new_mail( charset="UTF-8" )
  mail = Mail.new
  mail.mime_version = "1.0"
  if charset
    mail.content_type ["text", "plain", { "charset" => charset }]
  end
  mail
end
```

我们创建了一个名为`new_mail`的方法，它可以在我们需要的时候建立邮件。该方法接受一个参数来设置电子邮件的字符集，以防您想要更改它。如果没有，它将总是回落到默认值。

我们可以在测试本身中完成，但是您应该将这些类型的方法放入 test_helper 中，这样您就可以很容易地在全局级别上更改它们。现在是运行测试的时候了，使用`rake`。

![img13](img/9fe46ac191a6ea6ef8c244133df3411b.png)

好了，显然我们已经做对了一切，因为我们测试的输出是

```
1 tests, 3 assertions, 0 failures, 0 errors
```

继续做测试吧。例如，使用另一个发件人地址，看看会发生什么。

> “测试是 Rails 最重要的特性之一。在编写代码时广泛使用它。当您的代码不断增长，应用程序的复杂性不断增加时，这将帮助您节省大量时间！”

## 步骤 5 通知模型和控制器

现在我们已经做好了发送电子邮件的一切准备(包括测试！)，我们将设置一个漂亮的小界面来编写实际的通知，并使用邮件程序来发送它们。

出于本教程的考虑，我们将使用 Rails 脚手架。记住，我们的视图中有标题和内容，所以我们将在这里实现它们。

```
rails generate scaffold Notification headline:string content:text
```

Rails 现在创建了我们需要的一切:数据库、模型、控制器和视图的迁移。

![img14](img/6a18170a60bcc6c76d84385216e380fc.png)

让我们启动服务器:

```
rails server
```

不要忘记使用`rake db:migrate`迁移数据库，这将为我们建立一个表。

![img15](img/d24bcad12d55b037ef647fd60f268061.png)

![img16](img/23b97befddbe96c3b9782162af63f479.png)

接下来，启动浏览器，指向[http://localhost:3000/notifications](http://localhost:3000/notifications)。它应该是这样的:

![img17](img/b79e1ab694f93600a4f734452e4d9b87.png)

现在，我们导航到新的通知页面，并通过填写表单创建一个新的通知页面。

![img18](img/7eba68981498b9dc3d4cd670ca977bb6.png)

成功大概是这样的。当然，您应该编写测试来确保一切正常。我让你决定。；-)

![img19](img/c10c9aeb0e53e8e1b728140435dc5790.png)

在我们发送电子邮件和看看开信刀的魔力之前，还有最后一件事。希望你还记得我们想知道如何使用这块宝石。我们现在需要发送电子邮件，因此我们转向控制器并修改代码。

![img20](img/4e4c8f078e02f4d065edfaff93c752ef.png)

在`create method`中，我们将添加一行代码，告诉 ActionMailer 如果保存成功就发送通知。

```
def create
  @notification = Notification.new(params[:notification])
  respond_to do |format|
    if @notification.save
      **NotificationMailer.notification_mailer(@notification).deliver**
      format.html { redirect_to(@notification, :notice => 'Notification was successfully created.') }
      format.xml  { render :xml => @notification, :status => :created, :location => @notification }
    else
      format.html { render :action => "new" }
      format.xml  { render :xml => @notification.errors, :status => :unprocessable_entity }
    end
  end
end
```

![img21](img/a1efe3987231d84ea9646d70a0d6cb62.png)

之后，通过 web 界面创建另一个通知。如果我们做的一切都是正确的，浏览器中的另一个选项卡应该会出现，我们可以预览我们刚刚创建的电子邮件！瞧啊。

![img22](img/99ac83309d5495c144de6892a25412ca.png)

如你所见，你有一个很好的方法从你的应用程序中预览电子邮件。现在你可以去修改它们了。作为额外的奖励，试着找出预览在你的磁盘上的存储位置。我给你一个提示，看看`tmp/`目录或者你浏览器的地址栏。

我希望你喜欢学习开信刀。我在本文中使用的 rails 应用程序可以在这里找到。

## 分享这篇文章