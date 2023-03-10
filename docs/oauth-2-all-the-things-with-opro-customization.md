# OAuth 2 oPRO 的所有功能:定制

> 原文：<https://www.sitepoint.com/oauth-2-all-the-things-with-opro-customization/>

这是这个系列的第三部分，也是最后一部分。今天，我们将最终确定使用 [oPRO](https://github.com/opro/opro) 构建的身份验证提供程序。具体来说，我将指导您如何使用作用域，为访问令牌实现有限的生命周期，并引入速率限制。此外，我们还将讨论一些高级配置选项，比如使用自定义身份验证解决方案，以及将用户凭证交换为访问令牌。我们继续吧！

服务器和客户端应用程序的源代码可以在 GitHub 上找到[。](https://github.com/bodrovis/Sitepoint-source/tree/master/Auth_Provider_with_Opro)

## 范围

当用户通过 oPRO 进行身份验证时，他们被要求允许对应用程序进行读写操作(或权限的“范围”)。“读取”是强制操作，此权限不能撤销。它只允许一个应用程序通过`allow_oauth!`方法对白名单中的动作执行 GET 请求(参见[上一篇文章](https://www.sitepoint.com/oauth-2-all-the-things-with-opro-users-and-api))。)

另一方面，Write 可以被用户撤销。此权限允许应用程序使用 HTTP DELETE、PATCH 和其他谓词执行任何操作。

如果您需要更细粒度的控制，请修改 oPRO 的初始化文件:

*config/initializer/opro . Rb*

```
[...]
config.request_permissions = [:write, :list, :invite]
[...] 
```

接下来，您需要强制检查这些属性以获得想要的操作。这通过以下方式完成:

```
[...]
require_oauth_permissions :list, only: :index
[...] 
```

如果应用程序没有所需的权限，将引发 401 错误。要跳过权限检查，请使用`skip_oauth_permissions`(就像默认的`skip_before_action`):

```
[...]
skip_oauth_permissions :list, only: :index
[...] 
```

您甚至可以通过重新定义方法`oauth_client_can_#{permission}?`来编写自己的权限检查逻辑:

```
def oauth_client_can_email?
  # your custom logic here
end 
```

不要忘记这些方法必须返回`true`或`false`。

## 限制访问令牌的生命周期

出于安全目的，所有流行的服务都提供了一个有限寿命的访问令牌，您可能也想这样做。默认情况下，oPRO 会生成一个“永恒的”令牌，但这是可以更改的，所以我们现在就开始吧。

修改 oPRO 的初始化文件:

*config/initializer/opro . Rb*

```
[...]
config.require_refresh_within = 12.hours
[...] 
```

设置适合您的值，并且不要忘记重新启动服务器。现在，认证散列中的`expires_in`将包含等于令牌寿命的秒数。让我们将其转换为`DateTime`并存储在表中:

*型号/用户. rb*

```
[...]
def from_opro(auth = nil)
  [...]
  user.expires_at = auth['expires_in'].seconds.from_now
  [...]
end
[...] 
```

如果用户试图使用过期的令牌执行请求，oPRO 将引发 401 错误(未授权)，因此客户端应用程序可能需要从中解救:

```
[...]
rescue_from RestClient::Unauthorized do
  flash[:warning] = 'No access rights to perform that action or your token has expired :('
  redirect_to root_path
end
[...] 
```

我现在要做的是检查令牌是否过期。如果是，刷新并保存一个新的到表中。如您所知，我们在`ApiTestsController`中有一个`before_action :check_token`，这似乎是一个执行此类检查的好地方:

*api_tests_controller.rb*

```
[...]
def check_token
  redirect_to new_opro_token_path and return if
    !current_user || current_user.token_missing? ||
      (current_user.token_expired? && current_user.refresh_token_missing?)
  if current_user.token_expired?
    updated_current_user = current_user.refresh_token!
    if updated_current_user
      login updated_current_user
    else
      flash[:warning] = "There was an error while trying to refresh your token..."
      redirect_to root_path
    end
  end
end
[...] 
```

如果用户没有令牌或令牌已过期，但没有刷新令牌，我们会要求他们再次进行身份认证。否则，如果令牌确实过期，请刷新它并用新令牌让用户登录。

以下是采用的新模型动作:

*型号/用户. rb*

```
[...]
def refresh_token_missing?
  !self.refresh_token.present?
end

def token_expired?
  self.expires_at < Time.current
end

def refresh_token!
  return unless token_expired?
  client = OproApi.new(refresh_token: self.refresh_token)
  User.from_opro(client.refresh!)
end
[...] 
```

`refresh!`是另一个 API 方法:

*型号/opro_api.rb*

```
[...]
def refresh!
  return unless refresh_token
  JSON.parse(RestClient.post(TOKEN_URL,
                             {
                                 client_id: ENV['opro_client_id'],
                                 client_secret: ENV['opro_client_secret'],
                                 refresh_token: refresh_token
                             },
                             accept: :json))
end
[...] 
```

刷新令牌非常类似于获取访问令牌，除了发送`refresh_token`参数而不是`code`。oPRO 用一个完整的认证散列来响应，因此我们将响应传递给`User.from_opro`方法来保存新数据。

现在您可以手动编辑`expires_at`字段，并将其设置为某个过去的日期。刷新令牌过程应该对用户透明！

## 限速

假设应用程序变得非常流行，用户广泛使用 API。然而，你开始注意到一些用户滥用它太多。这可以通过限制用户每天被允许的 API 调用的最大数量来解决。默认情况下，oPRO 不提供任何限制，但是您可以很容易地改变这一点。

有许多方法可以完成这项任务，因此 oPRO 将实现逻辑留给了您。让我们保持简单，在`opro_client_apps`表中创建两个字段:

*   `requests_count`(`integer`)–默认值为 0。每次发出 API 请求时递增；
*   `last_request_at`(`date`)–用于跟踪最后一次请求的时间。如果日期小于今天，则将`requests_count`设置为 0，否则递增 1。

创建并应用相应的迁移:

```
$ rails g migration add_requests_count_and_last_request_at_to_opro_client_apps requests_count:integer last_request_at:date
$ rake db:migrate 
```

在`ApplicationController`中，我们需要修改两个最初为空的方法:`oauth_client_record_access!`和`oauth_client_rate_limited?`:

*application _ controller . Rb*

```
[...]
private

def oauth_client_record_access!(client_id, params)
  client_app = Opro::Oauth::ClientApp.find(client_id)
  if client_app.last_request_at < Date.today
    client_app.last_request_at = Date.today
    client_app.requests_count = 1
  else
    client_app.requests_count += 1
  end
  client_app.save
end
[...] 
```

下面是实现上述逻辑的方法:如果今天没有请求，重置计数器，否则递增 1。`client_id`包含应用程序的 id，`params`包含随请求一起发送的参数(例如，`access_token`)。

*application _ controller . Rb*

```
[...]
def oauth_client_rate_limited?(client_id, params)
  client_app = Opro::Oauth::ClientApp.find(client_id)
  client_app.requests_count > 2
end
[...] 
```

只需找到所需的应用程序，并检查它是否超过了阈值。显然，对于真正的应用程序来说，您需要每天提供更多的可用请求。现在尝试执行几个请求，在第三个请求之后，您将得到一个“未授权”的错误-这意味着我们的速率限制正在按预期工作！

## 其他配置选项

### 使用自定义身份验证解决方案

目前，开箱即用的 oPRO 仅支持 Devise，但是您可以调整几个设置并使用您自己的逻辑:

*config/initializer/opro . Rb*

```
Opro.setup do |config|
  [...]
  config.login_method do |controller, current_user|
    controller.sign_in(current_user, :bypass => true)
  end
  config.logout_method do |controller, current_user|
    controller.sign_out(current_user)
  end
  config.authenticate_user_method do |controller|
    controller.authenticate_user!
  end
  [...]
end 
```

### 用电子邮件和密码交换令牌

如果您可以访问用户的电子邮件和密码，您仍然可以用它们来交换访问令牌。这个过程非常相似，所以让我们更新我们的 API 适配器:

*型号/opro_api.rb*

```
[...]
attr_reader :access_token, :refresh_token, :email, :password

def initialize(access_token: nil, refresh_token: nil, email: nil, password: nil)
  @access_token = access_token
  @refresh_token = refresh_token
  @email = email
  @password = password
end

def authenticate_with_email!
  return unless email && password
  JSON.parse(RestClient.post(TOKEN_URL,
                             {
                                 client_id: ENV['opro_client_id'],
                                 client_secret: ENV['opro_client_secret'],
                                 email: email,
                                 password: password
                             },
                             accept: :json))
end
[...] 
```

如您所见，我们不是发送`code`，而是提供用户的凭证。如果电子邮件和/或密码不正确，将引发 401 错误。不要忘记，你必须使用安全连接，以防止窃取用户的密码！

除此之外，您可能只允许某些应用程序使用此功能。为此，在您的`ApplicationController`中覆盖`oauth_valid_password_auth?`:

*application _ controller . Rb*

```
[...]
def oauth_valid_password_auth?(client_id, client_secret)
  client_id == 'some client id'
end
[...] 
```

当然，这是一个非常幼稚的实现，因为您可能有一组允许的客户机 id(oPRO 称之为“祝福 id”)并对它们进行检查。

继续尝试吧:

```
$ rails c
$ client = OproApi.new(email: 'test@example.com', password: '123')
$ client.authenticate_with_email! 
```

请注意，如果您使用像我在上一节中展示的自定义身份验证解决方案，还需要一个步骤。您必须引入自定义逻辑来检查凭证是否有效。

幸运的是，这只需要一个设置:

*config/initializer/opro . Rb*

```
[...]
config.find_user_for_auth do |controller, params|
  user = User.find(params[:email])
  user.valid_password?(params[:password]) ? user : false
end
[...] 
```

如果凭证无效，该方法应该返回一个用户或`false`。

## 结论

最后，您已经完成了本教程的学习。恭喜你！在我们的旅程中，我们讨论了许多有趣的事情，希望现在您已经准备好将这个身份验证提供程序投入生产，并进一步扩展它。

你用过 oPRO 吗？您会考虑在将来使用它吗？我个人喜欢用它工作，尽管有些东西需要调整。如果你有任何问题，不要犹豫地问。

在接下来的文章中，我将介绍[看门人](https://github.com/doorkeeper-gem/doorkeeper)——构建认证提供者的另一种解决方案，所以很快再见！

## 分享这篇文章