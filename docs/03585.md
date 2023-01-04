# 用 PRMD 记录您的 JSON API 模式

> 原文：<https://www.sitepoint.com/document-your-json-api-schema-with-prmd/>

在最近的一个项目中，我的团队同意我们需要一些方法来验证和记录进出我们 API 的 JSON。我们已经决定使用 [JSON 模式](http://json-schema.org/)作为描述 API 的手段，所以确定如何验证(和记录)JSON 模式是下一步要做的事情。我对如何最好地完成这些崇高的任务做了大量的研究。好吧，好吧，“大量的研究”实际上意味着“寻找已经这样做的宝石”。我也参考了我过去使用 Grape 和 Swagger 的经验，但是我们在这里没有使用 Grape，我也找不到任何可以让我轻松融入 Swagger 的东西。即使我做了，那也只是文件，没有经过验证。

长话短说(那是 TL 的“老人话”；DR)是，如果您想记录您的 API，验证请求和响应，并使用 JSON 模式，这是一个有点努力。有办法，比如[碘量法](https://github.com/mashery/iodocs)，但是我不想非要装 Redis，用 Node 等。只是为了拿到文件。此外，一旦有了模式，有很多工具可以生成文档，但是它们在创建模式方面没有提供太多帮助。我很快认识到，无论我选择哪种工具或方向，都需要大量的手工工作才能完成。我希望这篇文章能让其他有相同目标的人走得更远。

今年早些时候，我花了很长时间认真研究了作为我们 API 的可能平台的 pliny。Pliny 来自 Heroku 的好人们，他们对 API 有所了解。它是基于 Sinatra 的，并带有一些自以为是的(但非常好的)助手，用于日志记录、请求跟踪、版本控制等。如果你正在用 Ruby 编写 API，我强烈建议你去看看。我做了，结果我回答了我们的 JSON 模式需求。

Pliny 利用了来自 Github 用户[交互者](http://github.com/interagent) : [prmd](http://github.com/interagent/prmd) 和[委员会](http://github.com/interagent/committee)的另外两个 gem。这两个 gem 一起标记了您的 JSON 模式/API 需要的团队。prmd 专注于 JSON 模式创建和 API 文档生成，这也是本文的重点。Committee 是一个方法和框架中间件的集合，用于验证您的模式。下一篇文章将关注委员会。

![article](img/d9235ec221ca429d183ca84a30b0a18a.png)

## JSON 模式和 JSON 超级模式

JSON Schema 是创建一种定义 JSON 数据结构的方法的众多尝试之一。目标是能够记录和验证 JSON 提供者/存储库，就像处理数据库模式一样。有规范(目前是草案版本 4)，但我更喜欢[这本由太空望远镜科学研究所(伟大的名字)的 Michael Droettboom 写的在线书籍](http://spacetelescope.github.io/understanding-json-schema/)作为起点。

JSON 超级模式把你的模式变成“超文本”。换句话说，JSON 超级模式描述了应用程序的端点，包括它将接受和提供什么。太空望远镜这本书没有涉及 JSON Hyper Schema，所以我建议你扫描一下规范，阅读一下[这篇文章](https://brandur.org/elegant-apis)。

最后，prmd gem 提供了[这个降价文件](https://github.com/interagent/prmd/blob/master/docs/schemata.md)，在定义您的模式和 prmd 期望的内容时，这个文件非常有用。

我不打算详细介绍该规范，所以我鼓励您阅读这本书。在这篇文章的剩余部分，我假设你理解 JSON 模式和超级模式的基础。

## PRMD

PRMD 的口号是“HTTP APIs 的 JSON 模式工具和文档生成”。换句话说，prmd 允许您生成 JSON 模式，然后必须手动更改/调整以匹配您的 API。一旦模式被完全定义，prmd 提供任务来验证模式是否符合 JSON 模式，并为该 API 生成文档。

使用 prmd 的最终目的是为您的 API 定义一个 JSON 模式，以及支持文档。文档以 [Markdown](http://daringfireball.net/projects/markdown/) 格式生成，这很好。

prmd 提供了一个包含以下命令的可执行文件(`prmd`):

```
init: Scaffold resource schemata
combine: Combine schemata and metadata into single schema
verify: Verify a schema
doc: Generate documentation from a schema
render: Render views from schema 
```

我们将直接使用其中的每一个，或者直接或者通过 Rake 任务，除了`render`，我还没用过它。

## 例子

在本文中，我们的应用程序提供了帐户创建(注册)和验证。我们将定义资源(`account`)和端点(`links`)。我在这里使用的是 Rails，但是 Pliny 是基于 Sinatra 的，所以您应该能够轻松地将这里涉及的概念用于任何 Ruby web 框架。该模式还将公开会话和密码重置的端点。

Rails 应用使用`rails-api`，从 Ruby 2.2.2 开始，Rails 4.2.3。我有一个正在使用的回购协议，所以你可以去看看它是如何设置的。

### PRMD Setup

prmd 需要一个目录来存储模式及其支持文件。制作一个**模式/schemata** 目录:

```
mkdir -p schema/schemata 
```

顶层模式文件将驻留在**模式**中，而单个模式文件(例如`account`的模式文件)将驻留在**模式/模式**中。这只是我们的约定，所以你可以做你喜欢的。

我前面提到的“顶层”文件是描述我们整个 API 的元数据的文件，与特定的资源无关。这是我们的:

```
{
 "description": "Account API",
 "id": "account-api",
 "links": [{
   "href": "https://accounts.ourapi.com",
   "rel": "self"
 }],
 "title": "Accounts"
} 
```

这摘自 prmd 自述文件，满足 JSON 模式的元数据要求。

我觉得我应该提到，你可以使用 JSON 或 YAML 来定义你的 API。我从 YAML 开始，并不太喜欢它。我更喜欢在 JSON 中这样做。在 JSON 中这样做的一个额外好处是，您可以在各种在线 JSON 和 JSON Schema linters 中删除内容，并捕捉拼写错误或孤立括号。

### 帐户模式

生成模式支架的时间:

```
prmd init account > schema/schemata/account.json 
```

这为我们的帐户资源创建了一个基本的 JSON 模式。不幸的是，它没有从我们的模型中引入任何东西，如果我们有模型的话(可能不应该有)，所以必须手动调整 JSON 模式以匹配 API。打开文件，快速通读一遍。我将一部分一部分地剖析它，这样我们就在同一页上了。

#### 常规资源信息

```
{
  "$schema": "http://json-schema.org/draft-04/hyper-schema",
  "title": "Account",
  "description": "The Account resource for the API",
  "stability": "prototype",
  "strictProperties": true,
  "type": [
    "object"
  ],
  .... 
```

如你所见，`$schema`是 JSON 超级模式的草案 4。在`title`和`description`中有几个“修正问题”需要解决，我在上面已经解决了。`stability`指定资源的稳定性(duh)，是`prototype, development, or production`之一。`strictProperties`表示该对象(资源)只具有该`object`中定义的属性。有一个`additionalProperties`属性与`strictProperties`互斥。

#### 定义

`definitions`是将在整个模式中使用的引用属性，因此没有必要在模式定义的上下文中不断地重新定义`id`或`email`。这就是你如何在一个模式中把事情弄干的。

生成的定义有例子，比如`name`和`id`。我修改了下面的代码片段以匹配我们的资源，将`name`替换为`email`，并添加了`password`:

```
"definitions": {
    "id": {
      "description": "unique identifier of account",
      "readOnly": true,
      "format": "uuid",
      "type": [
        "string"
      ]
    },
    "email": {
      "description": "unique email of account",
      "readOnly": true,
      "type": [
        "string"
      ]
    },
    "password": {
      "description": "account password",
      "readOnly": true,
      "type": [
        "string"
      ]
    }
    "identity": {
      "anyOf": [
        {
          "$ref": "/schemata/account#/definitions/id"
        },
        {
          "$ref": "/schemata/account#/definitions/email"
        }
      ]
    },
    "created_at": {
      "description": "when account was created",
      "format": "date-time",
      "type": [
        "string"
      ]
    },
    "updated_at": {
      "description": "when account was updated",
      "format": "date-time",
      "type": [
        "string"
      ]
    }
  } 
```

从`definitions`中获得的一些重要信息包括:

*   每个定义都有一个代表数据类型的`type`。可接受的值有:`"array", "boolean", "integer", "number", "null", "object", "string"`。`format`就是你认为的样子，可能的值有:`"date-time", "email", "hostname", "ipv4", "ipv6", "uri"`。prmd 实际上提供了一个自定义的`uuid`，您可以(应该)将其用于 IDs。
*   说到标识，`identity`属性是资源如何标识特定实例的。在这种情况下，我们可以使用`id`或`email`。`$ref`属性是对`definitions`中属性的*引用*。因此，`"$ref": "/schemata/account#/definitions/email"`引入了我们的电子邮件定义(技术上称为“解引用”)。

其余的属性应该是不言自明的。

#### 链接

`links`部分是 JSON 超级模式规范的一部分，解释了模式支持的端点:

```
"links": [
  {
    "description": "Create a new account.",
    "href": "/accounts",
    "method": "POST",
    "rel": "create",
    "schema": {
      "properties": {
      },
      "type": [
        "object"
      ]
    },
    "title": "Create"
  },
  {
    "description": "Delete an existing account.",
    "href": "/accounts/{(%2Fschemata%2Faccount%23%2Fdefinitions%2Fidentity)}",
    "method": "DELETE",
    "rel": "destroy",
    "title": "Delete"
  },
  {
    "description": "Info for existing account.",
    "href": "/accounts/{(%2Fschemata%2Faccount%23%2Fdefinitions%2Fidentity)}",
    "method": "GET",
    "rel": "self",
    "title": "Info"
  },
  {
    "description": "List existing accounts.",
    "href": "/accounts",
    "method": "GET",
    "rel": "instances",
    "title": "List"
  },
  {
    "description": "Update an existing account.",
    "href": "/accounts/{(%2Fschemata%2Faccount%23%2Fdefinitions%2Fidentity)}",
    "method": "PATCH",
    "rel": "update",
    "schema": {
      "properties": {
      },
      "type": [
        "object"
      ]
    },
    "title": "Update"
  }
], 
```

一个`link`可以包括`href`、`method`、`schema`和`targetSchema`。后两个属性分别是 API 将接受和提供的。`rel`是链接与资源的关系，应为`create`、`destroy`、`self`、`instances`或`update`之一。

我确信你在`href`中看到了一些 JSON 吐槽的特定链接。我说的是这样的事情:

```
"href": "/accounts/{(%2Fschemata%2Faccount%23%2Fdefinitions%2Fidentity)}", 
```

基本上，那真的是`/accounts/{identity}`。还记得我们在`definitions`部分定义了`identity`吗？那个长而奇怪的字符串就是 URL 编码的`/schemata/account/#definitions/identity`。

您可以看到，prmd 基本上生成了“典型的”RESTful 链接，构建了一个类似 CRUD 的 API。在某些情况下，这是一个好的开始。在这种情况下，我们需要做出一些改变。因为这是为了注册和认证:

*   让我们去掉“列出现有帐户”，反正听起来像是一个安全问题。
*   为`session`和`password`流添加链接。
*   签到会是一个`POST`到`/accounts/session`，接受一个`remember_me`参数，会返回一个`token`。
*   密码端点将需要重置令牌。
*   我将非常详细地说明如何定义链接接受和返回的内容。

例如，下面是新的帐户创建/注册模式:

```
{
  "description": "Create a new account.",
  "href": "/account",
  "method": "POST",
  "rel": "create",
  "schema": {
    "properties": {
      "account" : {
        "type" : "object",
        "properties": {
          "email" : { "$ref": "/schemata/account#/definitions/email" },
          "password": {
            "type": "string",
            "description": "The password"
          },
          "remember_me": {
            "type": "boolean",
            "description": "True/false - generate refresh token (optional)"
          }
        },
        "required" : [ "email", "password" ]
      }
    },
    "type": [ "object" ]
  },
  "title": "Create",
  "targetSchema": {
    "type": "object",
    "properties":  {
      "token" : { "$ref": "/schemata/account#/definitions/token" }
    }
  }
} 
```

那么，什么改变了？

*   我扩展了`schema/properties`来接收一个`account`，它有一个`email`、一个`password`和一个可选的`remember_me`属性。电子邮件和密码是对`definitions`中相同项目的引用，但是`remember_me`属性是本地定义的。
*   注意嵌套在 account 中的`required`参数。这允许您在任何级别定义必需的和可选的参数。
*   我添加了`targetSchema`属性，定义了调用将返回的内容。在本例中，是我添加的一个`token`定义，看起来像:

    ```
    token": {
      "type": "string",
      "description": "The token",
      "example" : "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzUxMiJ9.eyJkYXRhIjp7ImlkIjoiMTE0MzYiLCJ0eXBlIjoiYWNjb3VudHMiLCJhdHRyaWJ1dGVzIjp7ImVtYWlsIjoiZ2xlbm4uZ29vZHJpY2hAZ21haWwuY29tIn19LCJzdWIiOiJhY2NvdW50IiwiZXhwIjoxNDM3MjM0OTM0LCJpc3MiOiJVbmlxdWUgVVNBIiwiaWF0IjoxNDM3MTQ4NTM0LCJqdGkiOiI3ZmJiYTgzOS1kMGRiLTQwODItOTBmZC1kNmMwM2YwN2NmMWMifQ.SuAAhWPz_7VfJ2iyQpPEHjAnj_aZ-0-gI4uptFucWWflQnrYJl3Z17vAjypiQB_6io85Nuw7VK0Kz2_VHc7VHZwAjxMpzSvigzpUS4HHjSsDil8iYocVEFlnJWERooCOCjSB9R150Pje1DKB8fNeePUGbkCDH6QSk2BsBzT07yT-7zrTJ7kRlsJ-3Kw2GDnvSbb_k2ecX_rkeMeaMj3FmF3PDBNlkM"
    }, 
    ```

哇哦。太丑了。是的，它是。然而，当我们生成 markdown 文档时会用到`example`,所以我现在就用 ugly 来获取它。

在一个生产应用程序中，有许多类似这样的变化来获得定义良好的模式。这是一个手动的、繁琐的过程，没有人喜欢这样做。然而，拥有文档并能够在测试中和生产站点上验证 JSON 模式的回报是值得的。至少，我们认为是。此外，我们的 API 是集中的(想想微服务),所以每个 JSON 模式工作的范围比大型的、单一的 API 要小。

### 文档生成

辛苦定义 JSON 模式的部分回报是“简单”的 API 文档。是的，我意识到有其他方法可以做到这一点(RAML，养蜂场等。)，所以如果你有好办法，我也不会劝你不要。

虽然 prmd 确实提供了一个可执行文件，但我喜欢使用 Rake 任务来组合模式文件和生成文档。[自述文件](https://github.com/interagent/prmd/blob/388dbe8020a8c3342fcdba7562b2b97cfe9c61e5/README.md#use-as-rake-task)解释了如何创建 Rake 任务。简而言之，我用下面的代码创建了一个**库/任务/模式. rake** :

```
require "prmd/rake_tasks/combine"
require "prmd/rake_tasks/verify"
require "prmd/rake_tasks/doc"

namespace :schema do
  Prmd::RakeTasks::Combine.new do |t|
    t.options[:meta] = "schema/meta.json"
    # use meta.yml if you prefer YAML format
    t.paths << "schema/schemata"
    t.output_file = "schema/authentication-api.json"
  end

  Prmd::RakeTasks::Verify.new do |t|
    t.files << "schema/authentication-api.json"
  end

  Prmd::RakeTasks::Doc.new do |t|
    t.files = { "schema/authentication-api.json" => "schema/authentication-api.md" }
  end
  task default: ["schema:combine", "schema:verify", "schema:doc"]
end 
```

请注意，我已经更改了自述文件中示例的一些路径和文件名，以匹配这个项目。现在，我可以走了

```
rake schema:combine
rake schema:verify
rake schema:doc

or

rake schema 
```

如果有 JSON 错误，`combine`将会失败。这里，我删除了文档中的一个`:`,得到:

```
unable to parse schema/schemata/account.json (#<JSON::ParserError: 795: unexpected token at '{
"$schema": "http://json-schema.org/draft-04/hyper-schema",
  "title": "Authentication API - Account",
  "description": "The Account Schema",
  "stability": "prototype",
  "strictProperties": true,
  "type": [
    "object"
    ],
  "definitions": {
    "id": {
      "description": "unique identifier of account",
      "readOnly": true,
  ...
  Somes files have failed to parse. If you wish to continue without them,please enable faulty_load using --faulty-load 
```

所以，你可以强迫它加载，但我不知道你为什么要这样做。

如果有 JSON 模式错误，那么`verify`任务将会失败。在这里，我制作了`password`a 型`sting`:

```
schema/authentication-api.json: #/definitions/account/links/1/schema/properties/account/properties/password/type: failed schema #/properties/type: No subschema in "anyOf" matched.
schema/authentication-api.json: #/definitions/account/links/0/schema/properties/account/properties/password/type: failed schema #/properties/type: No subschema in "anyOf" matched.
schema/authentication-api.json: #/definitions/account/links/0/schema/properties/account/properties/password/type: failed schema #/properties/type: No subschema in "anyOf" matched.
schema/authentication-api.json: #/definitions/account/links/0/schema/properties/account/properties/password: failed schema #/properties/properties/additionalProperties: Not all subschemas of "allOf" matched. 
```

假设`combine`工作并且`verify`没有发现任何东西，那么`doc`任务创建一个**模式/认证-api.md** 文件。这里有一个片段:

![1](img/b442d97c00cc5ac606668672add327bf.png)

完整的模式文档可以在这里找到。

我可以将这个 markdown 文档添加到 Github Repo 中，或者添加一个使用 Redcarpet 之类的东西来创建 HTML 的 route。重要的一点是文件是可用的。如果你的团队使用 Github，他们也很容易分享。

## 但是等等！还有呢！

我知道你在想什么。“为什么这个家伙要为一些 OK 减价文件经历这一切？他没听说过斯瓦格吗？”我听说过，它不会按我的意思去做。我不认为。我已经提到了在规范/测试中使用模式来验证 JSON 模式，以及使用中间件根据模式接受的内容来验证请求。这超出了 prmd 所能提供的范围。然而，prmd 背后的人正是为此目的编写了委员会 gem。这将是我下一篇文章的主题。您可以利用这段时间来整理您的模式。；)

## 分享这篇文章