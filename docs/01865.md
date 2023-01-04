# 在 Rails API 中处理密码和电子邮件更改

> 原文：<https://www.sitepoint.com/handle-password-and-email-changes-in-your-rails-api/>

![](img/ccfbfa7d33f279e6d7aa6ab01747cf17.png)

这是我们关于使用 JWT 从头开始验证的系列文章的第二部分。该系列的第一部分可以在[这里](https://www.sitepoint.com/authenticate-your-rails-api-with-jwt-from-scratch/)找到。在上一个教程中，我们看到了关于 JWT、不同身份验证机制和一组基本身份验证 API(如注册、确认和登录)的快速概述。在这一部分，我们将看到下一组 API，如密码(重置和更改)和电子邮件更新。

> 这个系列不仅仅是一个 JWT 教程。它的主要目标是看看如何从头开始构建您自己的自定义身份验证解决方案，而 JWT 正是我们选择使用的方法。

我们将继续构建我们在第一部分开发的示例应用程序，可以在这里[找到](https://github.com/avinoth/auth-scratch-demo)。如果您想继续下去，您可以查看链接存储库的`part-i`分支。

## 密码

我们在这里讨论的 API 是一个忘记密码的序列。该流程生成密码重置令牌以及供用户验证该令牌的端点。当用户单击通过电子邮件发送给他们的密码重置链接时，将调用该端点。最后一个端点用于最终更改密码。

### 忘记密码

忘记密码端点生成密码重置令牌，将其保存在数据库中，并向用户发送电子邮件。这类似于我们在第一部分看到的确认指令模块。让我们从添加密码重置功能所需的列开始。快跑，

```
rails g migration AddPasswordResetColumnsToUser 
```

在生成的迁移文件中，添加以下内容:

```
add_column :users, :reset_password_token, :string
add_column :users, :reset_password_sent_at, :datetime 
```

这两列就足够了。`reset_password_token`将存储我们生成的令牌，而`reset_password_sent_at`跟踪令牌发送的时间以达到到期目的。现在让我们添加端点。首先生成密码控制器:

```
rails g controller passwords 
```

将以下路由添加到您的 **config/routes.rb** 文件中:

```
post 'password/forgot', to: 'password#forgot'
post 'password/reset', to: 'password#reset' 
```

现在，让我们将上述路线的相应动作添加到**控制器/password_controller.rb** :

```
...
  def forgot
    if params[:email].blank?
      return render json: {error: 'Email not present'}
    end

    user = User.find_by(email: email.downcase)

    if user.present? && user.confirmed_at?
      user.generate_password_token!
      # SEND EMAIL HERE
      render json: {status: 'ok'}, status: :ok
    else
      render json: {error: ['Email address not found. Please check and try again.']}, status: :not_found
    end
  end

  def reset
    token = params[:token].to_s

    if params[:email].blank?
      return render json: {error: 'Token not present'}
    end

    user = User.find_by(reset_password_token: token)

    if user.present? && user.password_token_valid?
      if user.reset_password!(params[:password])
        render json: {status: 'ok'}, status: :ok
      else
        render json: {error: user.errors.full_messages}, status: :unprocessable_entity
      end
    else
      render json: {error:  ['Link not valid or expired. Try generating a new link.']}, status: :not_found
    end
  end
 ... 
```

让我们快速检查一下。在`forgot`动作中，获取 post 请求中的电子邮件并获取用户。如果找到并确认了用户，调用用户模型上的`generate_password_token`并发送电子邮件。邮件发送部分被跳过，但请确保在邮件中包含用户的`password_reset_token`。在`reset`动作中，获取请求中发送的令牌，并通过`password_token_valid?`对其进行验证，通过`reset_password`重置密码。这些方法尚未添加到用户模型中，让我们现在就做:

将以下方法添加到 **models/user.rb** 中:

```
...
def generate_password_token!
  self.reset_password_token = generate_token
  self.reset_password_sent_at = Time.now.utc
  save!
end

def password_token_valid?
  (self.reset_password_sent_at + 4.hours) > Time.now.utc
end

def reset_password!(password)
  self.reset_password_token = nil
  self.password = password
  save!
end

private

def generate_token
  SecureRandom.hex(10)
end
... 
```

在`generate_password_token!`方法中，我们使用`generate_token`方法生成一个令牌，并将其存储在`reset_password_token`列中，同时将`reset_password_sent_at`设置为当前时间。在`password_token_valid?`方法中，验证令牌是否在重置密码到期的最后 4 小时内发送。你可以随意改变它，只要你觉得合适。`reset_password!`方法更新用户的新密码并使重置令牌无效。

重置密码设置完成。您可以通过发送一个 post 请求`/passwords/forgot`来测试它，在请求正文中包含电子邮件，在请求正文中包含新的密码和令牌`/passwords/reset`。现在让我们添加密码更新链接。

### 更新密码

要添加更新密码，请将路线添加到您的路线文件中:

```
put 'password/update', to: 'password#update' 
```

下面是`PasswordsController`中相应的动作:

```
def update
  if !params[:password].present?
    render json: {error: 'Password not present'}, status: :unprocessable_entity
    return
  end

  if current_user.reset_password(params[:password])
    render json: {status: 'ok'}, status: :ok
  else
    render json: {errors: current_user.errors.full_messages}, status: :unprocessable_entity
  end
end 
```

密码`update`操作非常简单。从参数中获取密码，并使用我们之前在用户模型中声明的`reset_password`方法将其保存到 DB 中。现在，您可以通过向`/password/update`发送一个带有新密码的`PUT`请求来测试密码更新 URL。让我们继续下一个大功能，电子邮件更新。

## 电子邮件更新

电子邮件更新允许用户更新其帐户上的主要电子邮件。根据要求，我们应该检查该电子邮件是否已经被任何其他用户使用。如果电子邮件正常，保存它并向新邮件发送一封验证邮件。确认后，我们将用新电子邮件替换主电子邮件，并清除令牌。

因此，总共有两个 API:一个用于发出电子邮件更新请求，一个用于实际更新电子邮件。让我们开始吧。

首先进行迁移，添加必要的列来支持这个模块。生成迁移:

```
rails g migration AddUnconfirmedEmailTouser 
```

向其中添加以下内容并运行`rake db:migrate`:

```
add_column :users, :unconfirmed_email, :string 
```

### 更新

现在，让我们更新这两个端点的路由。将这些行添加到 **config/routes.rb** :

```
...
resources :users, only: [:create, :update] do
    collection do
        post 'email_update'
... 
```

将相应的动作添加到`UsersController`:

```
def update
    if current_user.update_new_email!(@new_email)
      # SEND EMAIL HERE
      render json: { status: 'Email Confirmation has been sent to your new Email.' }, status: :ok
    else
      render json: { errors: current_user.errors.values.flatten.compact }, status: :bad_request
    end
end 
```

还要添加一个`before_action`来对新邮件进行验证，使用标记为`private`的方法将它添加到用户控制器类的顶部:

```
class UsersController < ApplicationController
    before_action :validate_email_update, only: :update
    ...
    ...

    private
    def validate_email_update
      @new_email = params[:email].to_s.downcase

      if @new_email.blank?
        return render json: { status: 'Email cannot be blank' }, status: :bad_request
      end

      if  @new_email == current_user.email
        return render json: { status: 'Current Email and New email cannot be the same' }, status: :bad_request
      end

      if User.email_used?(@new_email)
        return render json: { error: 'Email is already in use.'] }, status: :unprocessable_entity
      end
    end
    ... 
```

在这里，我们检查所请求的电子邮件是否已经被使用，以及该电子邮件是否与该帐户已经拥有的电子邮件相同。如果一切正常，打电话给`update_new_email!`并发送电子邮件。请注意，电子邮件必须发送到用户的`unconfirmed_email`而不是他们的主要用户。我们在这里使用了几个新的模型方法，所以让我们来定义它们。在 **models/user.rb** 中增加以下功能:

```
def update_new_email!(email)
  self.unconfirmed_email = email
  self.generate_confirmation_instructions
  save
end

def self.email_used?(email)
  existing_user = find_by("email = ?", email)

  if existing_user.present?
    return true
  else
    waiting_for_confirmation = find_by("unconfirmed_email = ?", email)
    return waiting_for_confirmation.present? && waiting_for_confirmation.confirmation_token_valid?
  end
end 
```

在这里，在`email_used?`中，除了检查电子邮件是否主要用于任何帐户，我们还检查它是否正在更新并等待确认。这可以根据您的需要删除。在本教程的第一部分中添加了`confirmation_token_valid?`方法。

现在，您可以通过向`/users/update`发送一个请求体中带有`email`的`POST`请求来测试这条路由。

### 电子邮件更新

现在，让我们为电子邮件更新端点添加操作。将此代码添加到`UsersController`:

```
def email_update
  token = params[:token].to_s
  user = User.find_by(confirmation_token: token)

  if !user || !user.confirmation_token_valid?
    render json: {error: 'The email link seems to be invalid / expired. Try requesting for a new one.'}, status: :not_found
  else
    user.update_new_email!
    render json: {status: 'Email updated successfully'}, status: :ok
  end
end 
```

这个动作很简单。通过令牌获取用户，并查看令牌是否有效。如果是，更新邮件并回复。让我们将`update_new_email!`方法添加到用户模型中:

```
def update_new_email!
  self.email = self.unconfirmed_email
  self.unconfirmed_email = nil
  self.mark_as_confirmed!
end 
```

这里，我们用更新的电子邮件替换主电子邮件，并将更新的电子邮件字段设置为`nil`。另外，调用我们在本系列的前一部分中添加的`mark_as_confirmed!`。此方法使确认令牌无效，并设置确认 at 值。电子邮件更新端点现在也启动了。试着用我们在前面的请求体中生成的电子邮件令牌向`/users/email_update`发送一个`POST`请求。

## 结论

至此，我们已经完成了关于 Rails API 认证的两部分教程。概括地说，我们已经实现了 Devise 的认证、确认、密码和重新确认模块。不算太寒酸。

本教程中使用的代码可从[这里](https://github.com/avinoth/auth-scratch-demo)获得。本教程开始的代码可以在[第一部分](https://github.com/avinoth/auth-scratch-demo/tree/part-i)分支中找到。

我希望本教程能帮助您理解身份验证并推出您自己的身份验证系统。感谢阅读。

## 分享这篇文章