# 委员会对 JSON 的验证

> 原文：<https://www.sitepoint.com/json-validation-by-committee/>

![comm](img/78a2b91035c86e347c30a779e4e8fc61.png)

在我的[上一篇文章](https://www.sitepoint.com/document-your-json-api-schema-with-prmd/)中，我讲述了创建一个 JSON 模式来描述我的服务将接受并返回的 JSON 的步骤。该流程依靠 prmd gem 来提供初始的 JSON 模式模板，以及验证和文档生成。总而言之，这是一个感觉应该更加自动化的过程。我经历了相当多的痛苦来创建模式，在我的文本编辑器中手工定义类型和链接。在工作的最后，我有一个`.json`文件，它描述了我的 API 将接受和返回的内容，以及匹配的 Markdown 文档。

在那篇文章中，我承诺会做得更好。在这里，编写验证我的 Rails API 返回的 JSON 的单元测试是可能的。生产应用程序将拒绝不符合我们的 JSON 模式的请求。那听起来像一个奇妙的地方吗？对我来说很重要。

这篇文章将构建这片土地，向您展示如何将模式输入到由[委员会 gem](http://github.com/interagent/committee) 提供的工具中，使用这些工具来验证测试和应用程序请求。

## 帐户应用程序

我使用与 prmd 帖子相同的应用程序。这是一个提供账户信息的 Rails API 应用程序。它不是一个完整的应用程序，因为这里的例子不需要它。你可以细读这个应用程序来查看完整的模式。就今天的目的而言，我们只需要:

*   RSpec 测试验证 JSON 模式。
*   根据 JSON 模式对应用程序进行验证的请求。

### 设置

查看我正在使用的宝石的[宝石文件](https://github.com/sitepoint/prmd-json-schema/blob/master/Gemfile)。显然，最重要的是委员会宝石。

### 测试/规格

好了，下面是黄铜税。我想确保`AccountsController`根据我的 JSON 模式返回适当的响应。让我们看看模式中最简单的`link`:

```
# In the "links" section of schema/schemata/account.json
{
  "description": "Info for existing account.",
   "href": "/accounts/{(%2Fschemata%2Faccount%23%2Fdefinitions%2Fidentity)}",
  "method": "GET",
  "rel": "self",
  "title": "Info",
  "targetSchema": {
    "type": [
      "object"
    ],
    "properties": {
      "data": {
        "type": [
          "object"
        ],
        "properties": {
          "id": {
            "$ref": "#/definitions/account/definitions/id"
          },
          "email": {
            "$ref": "#/definitions/account/definitions/email"
          }
        }
      }
    },
    "required": [
      "data"
    ]
  }
} 
```

`/account/#{account.id}`路径是一个带有如下响应的`GET`请求:

```
{
  "data": {
    "id": 1234,
    "email": "someone@somewhere.com"
  }
} 
```

目前，`AccountController#show`看起来像:

```
def show
  render json: current_account
end 
```

(注意:`current_account`是一个简单的帮助器，根据`params[:id]`找到账户)

这是一个规范的开始:

```
describe "GET #show" do
  # Using FactoryGirl
  let(:account) { create(:account, email: email, password: password) }
  let(:email) { Faker::Internet.email }
  let(:password) { Faker::Internet.password }

  it "conforms to schema" do
    get :show, id: account.id
    # HOW???
  end
end 
```

我们如何编写一个验证响应的测试？这是对`/accounts/#{account_id}`的响应，目前是这样的:

```
{
  "id":3,
  "email":"adriana@rodriguez.net",
  "encrypted_password":"$2a$10$D6TkSSCRU4WHwVUzGsV.BeEoIRoBuJEZkuWCj.Ys4PlNJzvmoulzm",
  "password_salt":"$2a$10$D6TkSSCRU4WHwVUzGsV.Be","jti":null,
  "reset_password_token":null,
  "refresh_token":null,
  "reset_password_sent_at":null,
  "created_at":"2015-08-16T14:21:50.061Z",
  "updated_at":"2015-08-16T14:21:50.061Z"
  } 
```

显然，这不是我们想要的。幸运的是，委员会 gem 在`Committee::Test::Methods`中提供了测试方法，这需要`include` d 到规范中:

```
RSpec.describe AccountsController, type: :controller do
  include Committee::Test::Methods
  ... 
```

我们对这个规范最感兴趣的方法叫做`assert_schema_conform`。为了让匹配器在 Rails 中工作，有少量的擦鞋动作，因为该方法期望`schema_path`、`last_request`和`last_response`存在。RSpec `let`语法使这变得简单:

```
describe "GET #show" do
  # Using FactoryGirl
  let(:account) { create(:account, email: email, password: password) }
  let(:email) { Faker::Internet.email }
  let(:password) { Faker::Internet.password }
  let(:schema_path) { "#{Rails.root}/schema/authentication-api.json" }
  let(:last_response) { response }
  let(:last_request) { request }

  it "conforms to schema" do
    get :show, id: account.id
    assert_schema_conform
  end
end 
```

`schema_path`指向用 prmd 生成的 JSON 模式文件。`last_response`和`last_request`是来自 Sinatra 测试的约定。

运行规格(`rspec`)会产生以下结果:

```
1) AccountsController GET #show when the token is valid conforms to schema
   Failure/Error: assert_schema_conform
   Committee::InvalidResponse:
     Invalid response.

     #: failed schema #/definitions/account/links/4/targetSchema: "data" wasn't supplied.
   # /Users/ggoodrich/.rvm/gems/ruby-2.2.2@sp-json-schema/gems/committee-1.9.1/lib/committee/response_validator.rb:37:in `call'
   # /Users/ggoodrich/.rvm/gems/ruby-2.2.2@sp-json-schema/gems/committee-1.9.1/lib/committee/test/methods.rb:23:in `assert_schema_conform'
   # ./spec/controllers/accounts_controller_spec.rb:40:in `block (4 levels) in <top (required)>' 
```

嘣！这就是我们想要的。规范抱怨响应不包括`data`。让我们更改帐户的序列化方式:

```
# app/controllers/accounts_controller.rb
...

def show
  account_props = {
    data: {
      id: current_account.id,
      email: current_account.email
    }
  }
  render json: account_props
end
... 
```

我敢肯定，这会非常有效:

```
 1) AccountsController GET #show when the token is valid conforms to schema
    Failure/Error: assert_schema_conform
    Committee::InvalidResponse:
      Invalid response.

      #/data/id: failed schema #/definitions/account/links/4/targetSchema/properties/data/properties/id: 4 is not a string.
... 
```

什么什么？？？好极了。我忘记了我将帐户 ID 定义为 UUID，它是一个字符串:

```
...
"id": {
  "description": "unique identifier of account",
  "readOnly": true,
  "format": "uuid",
  "type": [
    "string"
  ]
},
... 
```

我们使用 UUIDs 作为帐户标识符，但我没有在应用程序中实现它。由于 JSON 模式是由团队评审并同意的，所以它是正确的，并且是“真理的来源”。在我们的工作中，我们遇到过两种情况，达成一致的模式不是实现返回的模式。这可能为我们节省了几轮*糟糕-修复-部署*，这使得所有的努力都是值得的。此外，它激励团队保持模式同步。`</moraleOfStory>`

回到我们的测试，在完成制作`id`UUID 的步骤后(这比 SQLite3 更难，顺便说一下)，测试通过了。检查 UUID 台阶的回购协议。

委员会还将验证请求路径是否在模式`links`中。例如，如果我将 JSON 模式`GET`帐户路径更改为`/account/{id}`(我删除了 an’s’)并重新运行规范，结果是:

```
1) AccountsController GET #show when the token is valid conforms to schema
   Failure/Error: assert_schema_conform
   Committee::InvalidResponse:
     `GET /accounts/c0adbb04-7706-4da8-8c04-d82079b72287` undefined in schema.
... 
```

非常好。委员会将测试有效链接(路径和方法)和响应格式。我们现在已经意识到委员会提供的主要好处之一。

## 实时请求/响应验证

委员会提供的另一个项目是中间件，它根据 JSON 模式验证传入的请求。中间件有两种风格:`Committee::Middleware::RequestValidation`和`Committee::Middleware::ResponseValidation`。像其他中间件一样，它们被添加到 Rails 配置文件中:

```
#config/application.rb

module SpJsonSchemaRails
  class Application < Rails::Application
    ...
    schema_file = "#{Rails.root}/schema/authentication-api.json"
    if File.exists?(schema_file)
      config.middleware.use Committee::Middleware::RequestValidation, schema: JSON.parse(File.read(schema_file)), strict: true
      config.middleware.use Committee::Middleware::ResponseValidation, schema: JSON.parse(File.read(schema_file))
    end
    ...
  end
end 
```

这里，快速检查模式文件是否存在，然后使用中间件。每个中间件都有自己的选项集，允许一些微调。

### 请求验证

`Committee::Middleware::RequestValidation`采取以下选项(直接来自[自述](https://github.com/interagent/committee#committeemiddlewarerequestvalidation)):

*   `allow_form_params`:指定在可能的情况下，输入也可以指定为 application/x-www-form-urlencoded 参数。这不适用于更复杂的模式验证。
*   `allow_query_params`:指定验证时将查询字符串参数考虑在内(默认为 true)。
*   `check_content_type`:指定应根据 jsonschema 定义验证 content_type。(默认为真)。
*   `error_class`:指定用于格式化和输出验证错误的类(默认为 Committee::ValidationError)
*   `optimistic_json`:即使没有 Content-Type: application/json，也会尝试解析请求体中的 JSON，然后返回到其他选项(默认为 false)。
*   `prefix`:挂载中间件，以配置好的前缀响应。
*   `raise`:在出错时引发异常，而不是用一般错误体来响应(默认为 false)。
*   `strict`:将中间件置于严格模式，这意味着模式中未定义的路径将用 404 响应，而不是运行(默认为 false)。

让我们在应用程序中使用 curl 来看看这是如何工作的:

```
# This path doesn't exist and we're using 'strict: true'
curl http://localhost:3000/account
=> {"id":"not_found","message":"That request method and path combination isn't defined."}

# This one should work, right? :)
curl http://localhost:3000/accounts/953cff00-23ac-47dc-bb68-4b391e75aae7
==> {"id":"invalid_response","message":"Invalid response.\n\n#/data/id: failed schema #/definitions/account/links/4/targetSchema/properties/data/properties/id: 953CFF0023AC47DCBB684B391E75AAE7 is not a valid uuid."} 
```

哇哦！那是什么？似乎我们在规范返回的内容和应用程序实际返回的内容之间存在差异。嗯。好吧，让我们利用这个机会指出响应验证工作**咳嗽**。

好吧，一个小小的修正就可以了:

```
# app/controllers/accounts_controller.rb
...

def show
  account_props = {
    data: {
      id: current_account.id.to_s, # to_s makes the UUID not BELIKETHIS
      email: current_account.email
    }
  }
  render json: account_props
end
.

curl http://localhost:3000/accounts/953cff00-23ac-47dc-bb68-4b391e75aae7
=> {"data":{"id":"953cff00-23ac-47dc-bb68-4b391e75aae7","email":"email@email.com"}} 
```

在一个真正的应用程序中，你应该使用序列化框架，比如[active _ model _ serializer](https://github.com/rails-api/active_model_serializers)或者 [roar](https://github.com/apotonick/roar) ，所以你可能会避免这个问题。

这里重要的一点是无效的请求不会到达您的控制器。这叫做*快速失败*，所有酷孩子都在这么做。

### 响应验证

`Committee::Middleware::ResponseValidation`有以下选项(同样，直接来自自述文件):

*   `error_class`:指定用于格式化和输出验证错误的类(默认为`Committee::ValidationError`)
*   `prefix`:挂载中间件，以配置好的前缀响应。
*   `raise`:在出错时引发异常，而不是用一般错误体来响应(默认为 false)。
*   `validate_errors`:也验证非 2xx 响应(默认为假)

对我来说，这里最有趣的选项是`error_class`。这允许您指定用于返回错误的类。我们真的想要这个选项，这样我们的错误就可以符合[JSON API](http://jsonapi.org/format/#errors)。

我们已经看到了一个响应验证工作的例子，所以让我们继续看看 Committee 还为聚会带来了什么。

## 中断服务器

Committee 的一个非常酷的特性是能够根据 JSON 模式来确定服务器。如果我们将下面一行添加到 **config/application.rb** 中:

```
...other config...
use Committee::Middleware::Stub, schema: JSON.parse(File.read("#{Rails.root/schema/authentication-api.json}")) 
```

并启动服务器，我们可以点击 JSON 模式中定义的任何链接，而不必实现它们。这太酷了。

例如，我们还没有接触到`POST /account/session`，它是“登录”链接。JSON 模式接受一个带有`email`和`password`的`account`参数，并返回一个`token`:

```
{
  "description": "Sign in (generate token)",
  "href": "/account/session",
  "method": "POST",
  "rel": "",
  "schema": {
    "properties": {
      "account": {
        "type": [
          "object"
        ],
        "properties": {
          "email": {
            "$ref": "#/definitions/account/definitions/email"
          },
          "password": {
            "type": [
              "string"
            ],
            "description": "The password"
          },
          "remember_me": {
            "type": [
              "boolean"
            ],
            "description": "True/false - generate refresh token (optional)"
          }
        }
      }
    },
    "type": [
      "object"
    ],
    "required": [
      "account"
    ]
  },
  "targetSchema": {
    "type": [
      "object"
    ],
    "properties": {
      "token": {
        "$ref": "#/definitions/account/definitions/token"
      }
    }
  },
  "title": "Sign In"
},
... 
```

我可以发出一个`curl`请求并得到预期的结果:

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X POST http://localhost:3000/account/session  -d '{"account": {"email": "test@test.com", "password":"password"}}'

HTTP/1.1 200 OK
Content-Type: application/json
Etag: W/"d36cc07cb95b45bc67964243f0c35795"
Cache-Control: max-age=0, private, must-revalidate
X-Request-Id: a86b8f12-6d83-4eb8-a750-01331e802243
X-Runtime: 0.002147
Server: WEBrick/1.3.1 (Ruby/2.2.2/2015-04-13)
Date: Sun, 16 Aug 2015 20:47:15 GMT
Content-Length: 546
Connection: Keep-Alive

{"token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzUxMiJ9.eyJkYXRhIjp7ImlkIjoiMTE0MzYiLCJ0eXBlIjoiYWNjb3VudHMiLCJhdHRyaWJ1dGVzIjp7ImVtYWlsIjoiZ2xlbm4uZ29vZHJpY2hAZ21haWwuY29tIn19LCJzdWIiOiJhY2NvdW50IiwiZXhwIjoxNDM3MjM0OTM0LCJpc3MiOiJVbmlxdWUgVVNBIiwiaWF0IjoxNDM3MTQ4NTM0LCJqdGkiOiI3ZmJiYTgzOS1kMGRiLTQwODItOTBmZC1kNmMwM2YwN2NmMWMifQ.SuAAhWPz_7VfJ2iyQpPEHjAnj_aZ-0-gI4uptFucWWflQnrYJl3Z17vAjypiQB_6io85Nuw7VK0Kz2_VHc7VHZwAjxMpzSvigzpUS4HHjSsDil8iYocVEFlnJWERooCOCjSB9R150Pje1DKB8fNeePUGbkCDH6QSk2BsBzT07yT-7zrTJ7kRlsJ-3Kw2GDnvSbb_k2ecX_rkeMeaMj3FmF3PDBNlkM"} 
```

响应依赖于为 JSON 模式文件中的每个字段定义的“示例”。

Committee 还提供了一个`committee_stub`可执行文件，它将启动一个基于相同模式文件的服务器:

```
committee-stub -p 3000 schema/authentication-api.json 
```

BANGO，工作 API 服务器。多酷啊。

这里明显的用例是在您处理实现的同时，能够向其他团队或设计人员提供服务器的“工作”实现。

## 加入委员会

委员会 gem 为任何用 Ruby 创建 API 服务器的团队提供了一些急需的功能。虽然今天的例子使用 Rails，但是 prmd 和 committee gems 都是为基于 Sinatra 的框架 [pliny](http://github.com/interagent/pliny) 创建的。如果您正在创建 API，您需要开始将 JSON 视为应用程序的一个成熟部分。PRMD 和委员会给了你这样做的工具。

如果你有任何想法或认为我做错了什么，请在评论中告诉我。

## 分享这篇文章