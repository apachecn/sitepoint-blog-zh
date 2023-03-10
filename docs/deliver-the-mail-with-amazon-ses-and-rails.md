# 用亚马逊 SES 和 Rails 递送邮件

> 原文：<https://www.sitepoint.com/deliver-the-mail-with-amazon-ses-and-rails/>

![Screenshot 2016-06-21 13.10.44](img/459e154d3ebf4cde0454a535480a7b1b.png)

亚马逊简单电子邮件服务(Amazon SES)是一种经济高效的电子邮件服务，建立在可靠且可扩展的基础设施之上，是 Amazon.com 为服务其客户群而开发的。借助 Amazon SES，您可以向客户发送交易电子邮件、营销信息或任何其他类型的高质量内容。您还可以使用 Amazon SES 接收消息并将其发送到 Amazon S3 桶，通过 AWS Lambda 函数调用您的自定义代码，或者向 Amazon SNS 发布通知。使用 Amazon SES，您没有最低承诺要求，您可以按需付费，只需为您使用的内容付费。

## 概述

这篇文章将带你快速设置一个 Rails 应用程序，如果你是从 Amazon EC2 实例(部署在 Amazon EC2 上的应用程序)发送的话，每月发送 **62，000 封免费电子邮件**。此外，你还可以在亚马逊 SES 上每月免费收到**1000 封**电子邮件。即使你不在亚马逊 EC2 上，你仍然可以在其他地方(Heroku，DigitalOcean 等)得到最便宜的[价格](https://aws.amazon.com/ses/pricing/)(每 1000 封邮件 0.1 美元)。).对于本文，我们将从 **Amazon SES 沙箱**发送电子邮件，因为离开**沙箱**需要请求 AWS 支持扩展访问。从**沙箱**发送电子邮件有一些限制，比如我们必须验证发送和接收电子邮件的地址。但是，我们可以通过这样做来测试我们的设置，并确保一切设置正确，正确的代码到位，以便在**生产**中发送具有扩展访问权限的电子邮件。

## 为什么亚马逊 SES？

这是最便宜的。虽然有一些限制，如你不能使用专用的 IP 地址和分析不如其他一些替代品。但是如果你需要在预算范围内发送大量电子邮件，这是一个很好的选择。

## 价格比较

这里是 SES 定价与其他一些知名电子邮件提供商的快速比较。

**每月 40，000 封电子邮件的成本:**

| 服务 | 价格 |
| 亚马逊 ses | $4 |
| Mailgun | $15 |
| 邮件快递 | $17 |
| 邮戳 | $30 |
| 你是谁 | $7.5 |
| SendGrid | $10 |

## 入门指南

让我们直接进入编码，快速设置通过 Amazon SES 用我们的 Rails 应用程序发送电子邮件。我们不会担心太多普通的 Rails 设置，而是专注于预期的特性，即创建一个通过 Amazon SES 发送电子邮件的基本 Rails 应用程序。

我运行的是 **Ruby 2.3.0** 和 **Rails 4.2.6** 。

```
rails new my_ses_app 
```

这将创建一个新的 Rails 应用程序。将目录更改为新创建的应用程序:

```
cd my_ses_app 
```

创建具有索引操作的欢迎控制器:

```
rails generate controller welcome index 
```

转到 *config/routes.rb* 文件，删除所有注释掉的示例，这样就可以得到一个干净的 routes 文件来使用。通过添加以下三行来定义 get 路由、post 路由和应用程序根:

```
get 'welcome/index'
post '/send_email', to: 'welcome#send_email', as: 'send_email'
root 'welcome#index' 
```

添加以上三行代码后，routes 文件应该如下所示:

```
Rails.application.routes.draw do
  get 'welcome/index'
  post '/send_email', to: 'welcome#send_email', as: 'send_email'
  root 'welcome#index'
end 
```

将 *send_email* 动作添加到*welcome _ controller(app/controllers/welcome _ controller . Rb)*

```
def send_email
  MyMailer.send_email(name: params[:name], phone: params[:phone], email: params[:email], message: params[:message]).deliver
  redirect_to root_url, notice: "Email sent!"
end 
```

让我们在应用程序根上制作一个发送电子邮件的表单。在将表单添加到视图文件之前，添加 Twitter Bootstrap 以获得一个闪亮优雅的电子邮件表单。将 **bootstrap-sass** 宝石添加到您的*宝石文件*中:

```
gem 'bootstrap-sass' 
```

运行`bundle`命令以确保 Gem 已安装并被我们的 Rails 应用程序获取。您需要删除您的*app/assets/styles/application . CSS*文件中的所有内容，将其重命名为 *application.scss* ，并添加这两行:

```
@import "bootstrap-sprockets";
@import "bootstrap"; 
```

你可以使用 Twitter Bootstrap，现在将这个 HTML 粘贴到*app/views/welcome/index . HTML . erb*:

```
<% flash.each do |name, msg| %>
  <div class="alert alert-<%= name == 'notice' ? "info" : "danger" %> fade in">
    <button class="close" data-dismiss="alert">
      ×
    </button>
    <i class="fa-fw fa fa-info"></i>
    <strong><Info><%= name %>!</strong> <%= raw(msg) %>
  </div>
<% end %>
<h1 class="text-center">AWS SES Email Example</h1>
<div class="container">
  <div class="row">
      <div class="col-md-6 col-md-offset-3">
        <div class="well well-sm">
          <%= form_tag(send_email_path, method: "post", :class=>"form-horizontal") do %>
          <fieldset>
            <legend class="text-center">Contact us</legend>

            <!-- Name input-->
            <div class="form-group">
              <label class="col-md-3 control-label" for="name">Name</label>
              <div class="col-md-9">
                <input id="name" name="name" type="text" placeholder="Your name" class="form-control">
              </div>
            </div>

            <!-- Email input-->
            <div class="form-group">
              <label class="col-md-3 control-label" for="email">Your E-mail</label>
              <div class="col-md-9">
                <input id="email" name="email" type="text" placeholder="Your email" class="form-control">
              </div>
            </div>

            <!-- Message body -->
            <div class="form-group">
              <label class="col-md-3 control-label" for="message">Your message</label>
              <div class="col-md-9">
                <textarea class="form-control" id="message" name="message" placeholder="Please enter your message here..." rows="5"></textarea>
              </div>
            </div>

            <!-- Form actions -->
            <div class="form-group">
              <div class="col-md-12 text-right">
                <button type="submit" class="btn btn-primary btn-lg">Submit</button>
              </div>
            </div>
          </fieldset>
          <% end %>
        </div>
      </div>
  </div>
</div> 
```

我们不会深入这个 HTML 的细节，因为它显然超出了我们预期的功能范围。但这是一个超级简单的 HTML 表单，带有一些 Twitter 引导程序的魔力，给了我们一个很好的电子邮件表单。

在浏览器中转到 [http://localhost:3000/](http://localhost:3000/) ,您应该看到这样一个表单:

![awssesemail_form](img/cfaa3570ce8b59b427ede75142d09a17.png)

让我们为 Rails 应用程序生成一个邮件程序:

```
rails generate mailer my_mailer 
```

这将在 *app/mailers* 目录中添加两个文件 *application_mailer.rb* 和 *my_mailer.rb* ，以及在 *app/views/layouts* 目录中添加两个文件 *mailer.html.erb* 和 *mailer.text.erb* 。现在将`send_email`动作添加到空的*app/mailers/my _ mailer . Rb*文件中，该文件看起来应该是这样的:

```
class MyMailer < ApplicationMailer
  def send_email(options={})
    @name = options[:name]
    @email = options[:email]
    @message = options[:message]
    mail(:to=>"another_email@example.com", :subject=>"Amazon SES Email")
  end
end 
```

将`send_email`动作添加到我们的邮件程序文件后，将*app/mailers/application _ mailer . Rb*文件中的默认**从**电子邮件地址更改为您希望使用的地址。在本教程中，我称它为**some_email@example.com**。

```
class ApplicationMailer < ActionMailer::Base
  default from: "some_email@example.com"
  layout 'mailer'
end 
```

我们现在可以删除*app/views/layouts/mailer . html . erb*，因为我们将针对本教程发送纯文本**电子邮件**。在 *app/view/mailers/* 中创建一个名为 *send_email.text.erb* 的新文件，并添加以下几行:

```
Hi Sarmad,
Somebody contacted you through your site.
Details:

Name: <%= @name %>,

Email: <%= @email %>,

Message: <%= @message %> 
```

这将用作我们电子邮件的文本模板。

打开*config/environments/development . Rb*以更改 **SMTP** 配置，并在本地开发环境中测试电子邮件。将这个应用程序部署到一个**生产**环境不在本文的讨论范围之内。我们将通过 **Amazon SES** 设置、代码和配置，让我们的应用程序准备好每月从 **Amazon EC2** 实例发送 62，000 封免费电子邮件。在 **SitePoint** 上已经有一个[很棒的深入教程](https://www.sitepoint.com/deploy-your-rails-app-to-aws/)，贯穿部署到 EC2。事实上，我亲自阅读了使用 **Capistrano** 在 **Amazon EC2** 上部署的教程。

前往*config/environments/development . Rb*，进行这些更改，让我们的应用程序准备好发送电子邮件。首先将`action_mailer.perform_deliveries`设置为`true`，因为在**开发**环境中默认设置为`false`:

```
config.action_mailer.perform_deliveries = true 
```

接下来，寻找这一行:

```
config.action_mailer.raise_delivery_errors = false 
```

并将其值设置为`true`。如果我们的电子邮件由于某种原因无法发送，这将有助于调试发送错误。

现在将 SMTP 设置信息粘贴到配置块中:

```
config.action_mailer.smtp_settings = {
  :address => "...",
  :port => 587,
  :user_name => ENV["SES_SMTP_USERNAME"], #Your SMTP user
  :password => ENV["SES_SMTP_PASSWORD"], #Your SMTP password
  :authentication => :login,
  :enable_starttls_auto => true
} 
```

我们已经将**地址**留了下来，稍后我们将从**亚马逊 SES** 管理控制台复制该地址。

我们现在需要在亚马逊 SES 管理控制台上做很多事情:

1.  登录 **AWS 管理控制台**。如果你还没有报名参加 **AWS** 的话。这很简单。

![aws_login](img/03cf6e9dcdc34aa9aa4414720b4737a7.png)

3.  这将带您到 AWS 仪表板，寻找 SES。

![aws_home](img/0bd4fa9d4778268b7de540e72ffbf3ed.png)

5.  点击“SES”进入**亚马逊 SES 仪表盘**。点击“电子邮件地址”

![](img/1be2c0944a44d433610ba88d29dc1182.png)

7.  验证两个邮箱地址(你必须有两个邮箱地址才能在**沙箱**模式下完成邮件发送流程)。我已经添加了两个邮件并验证了它们。点击**验证新的电子邮件地址**按钮，添加您的发送和接收电子邮件地址。

![ses_email_adresses](img/b6f67051d9cb4d5b3fcddf7eaf8dfbce.png)


9.  SES 将向您发送一封确认电子邮件，如下所示。

![](img/938ee406f0740d7fe3cd966a33ecdaac.png)

11.  创建您的 **SMTP** 凭据以在您的应用中使用。
12.  复制要在您的应用程序中使用的 **SMTP** 服务器名称。

刷新 **SES 电子邮件地址**页面，您应该会看到您的电子邮件地址已经过验证。重复相同的步骤添加另一个。在*app/mailers/application _ mailer . Rb*中输入一个电子邮件地址，将【some_email@example.com】的**替换为你的**。

```
default from: "some_email@example.com" 
```

并将另一个放入 *app/mailers/my_mailer.rb* ，将*another_email@example.com*替换为你另一个验证过的邮箱地址。

```
mail(:to=>"another_email@example.com", :subject=>"Amazon SES Email") 
```

转到 **SES SMTP 设置**页面

![ses_smtp_settings](img/067c88e390b5b17d1d6e85607a37cb8e.png)

从那里复制**服务器名**，放入*config/environments/development . Rb*中的`action_mailer.smtp_settings`块。替换这条线

```
:address => "<ses server name>", 
```

用你从 *SMTP 设置*页面复制的*服务器名*。

现在*action _ mailer . SMTP _ settings*块看起来应该类似于下面这样

```
config.action_mailer.smtp_settings = {
  :address => "email-smtp.us-east-1.amazonaws.com",
  :port => 587,
  :user_name => ENV["SES_SMTP_USERNAME"], #Your SMTP user
  :password => ENV["SES_SMTP_PASSWORD"], #Your SMTP password
  :authentication => :login,
  :enable_starttls_auto => true
} 
```

点击上图所示的“创建我的 SMTP 凭证”按钮。

![create_smtp_iam_user](img/d2f50bf3054a009963d82e3cb4dd3627.png)

如果你想的话，你可以改变这个用户名，但是现在这个默认的就可以了。

![smtp_username_password](img/233f7c97661d652e682748fe0ca193f4.png)

复制这些凭证并粘贴到您的*config/environments/development . Rb*中，或者更好的是，将它们粘贴到您的环境中:

```
ENV["SES_SMTP_USERNAME"] = "*****" # Replace with your SMTP username
ENV["SES_SMTP_PASSWORD"] = "*****" # Replace with your SMTP password 
```

我们现在都准备好发送电子邮件了。

![email_form](img/62cbc1718ba1f329b57dc5bb5f7ef7f0.png)

点击*提交*。您将看到一封电子邮件发送通知。

![email_sent_notice](img/b11d899e9676a525f32be229906dbd62.png)

让我们前往收件箱，验证电子邮件是否正确发送和接收。

![email_received](img/10c42509cc03effdce277f8e2af2d3cb.png)

这总结了这篇关于设置我们的 *Rails* 应用程序与*亚马逊 SES* 发送电子邮件的帖子。通过这种设置和配置，如果应用程序部署在*亚马逊 EC2* 上，您可以发送 62，000 封免费电子邮件。然而，有些事情你必须密切关注

![sending_statistics](img/1a4116404cb6adc1e2e85dada0689de6.png)

如果你有大量的*退信、投诉*或*拒绝*，你的*亚马逊 SES* 账户会被列入黑名单并被封锁。你可以在这里阅读更多相关信息[。](http://docs.aws.amazon.com/ses/latest/DeveloperGuide/best-practices-bounces-complaints.html)

正如我们所讨论的，我们将从 *Amazon SES Sandbox* 为这篇文章发送电子邮件。你可以从这里请求*亚马逊*将你移出*沙箱*

![sending_limit_increase_request](img/3524c888602a33ebb272f9ce348075a1.png)

你可以在这里阅读更多关于那个[的内容。](http://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html)

## 结论

亚马逊 SES 是发送简单又便宜的电子邮件的绝佳选择。还有其他服务可能比*亚马逊 SES* 更适合，在某些情况下，我希望在未来涵盖这些服务。

## 分享这篇文章