# RAML，RESTful API 建模语言

> 原文：<https://www.sitepoint.com/raml-restful-api-modeling-language/>

在最近的一篇文章中，我[介绍了 Slate](https://www.sitepoint.com/writing-api-documentation-slate/) ，一个专门为编写 API 文档而设计的静态站点生成器。这一次，我要看一些在很多方面甚至更好的东西。

但是首先，如果你允许我花一点时间，我想从引用我自己的那篇文章开始；

> API 的好坏取决于它的文档

我认为这是值得重复的，因为它经常被忽视，这也是这个简短系列文章的动机之一，这些文章介绍了一些帮助您编写优秀文档的工具。

Slate 是一个有用的工具，但是尽管页面本身的结构相对僵化，实际的文档却相当自由。

RAML (RESTful API 建模语言)为描述 RESTful API 提供了一种结构化的、明确的格式。它允许你描述你的 API 端点、每个端点使用的 HTTP 方法、任何参数和它们的格式、你期望的响应等等。

您可以以多种方式使用 RAML

*   它可以在设计和规范阶段用来描述您计划构建的 API
*   您可以使用 RAML 来生成文档
*   RAML 可用于实现交互式 API 控制台
*   您可以针对 RAML 进行测试
*   它可用于生成模拟 API 响应

在本文中，我将主要关注生成文档；在后面的文章中，我将介绍如何将 RAML 集成到 API 测试中。但是首先，让我们看看如何编写一个 RAML 文档。

## 写作障碍

首先要注意的是，RAML 是 [YAML](http://www.yaml.org/) 的衍生物。如果你以前写过 YAML，你会发现使用 RAML 更容易，但如果你没有，那么它相对简单，或者在 Sitepoint 上有一个[教程。](https://www.sitepoint.com/using-yaml-in-php-projects/)

因为它们只是文本文件，所以您当然可以使用任何文本编辑器或 IDE 来创建和编辑它们。然而，更好的选择是使用 Anypoint 平台。它提供了一个 API 编辑器，允许你编辑 RAML 文件，包括自动完成、边输入边验证和一些生成的交互式文档的交互式预览(我们将在后面看到如何自己生成)。

要使用 Anypoint，你首先需要[注册](https://anypoint.mulesoft.com/apiplatform/)——这是免费的——然后添加一个 API，然后点击“在 API Designer 中定义 API”。

如果你更愿意使用 Sublime，你可以使用一个插件。它可以通过包控制获得；只需搜索“RAML 语法高亮”。

让我们看看一个 RAML 文件的结构。

### 基本信息

RAML 文件以声明开头，指明格式:

```
#%RAML 0.8
```

通常，您需要提供一些关于 API 的元信息，例如:

```
title: My Example API
version: v1
```

> `title`是必填字段；`version`是可选的

然后，您应该为您的 API 提供基本 URI。最简单地说，它可以是以下格式:

```
baseUri: http://api.acme.com/
```

您也可以使用 URI 参数。例如，将版本标识符合并到 API URLs 中是常见的做法；如果你也采用这种方法，你可以这样做:

```
baseUri: http://api.acme.com/{version}/
```

我们稍后将更详细地研究 URI 参数，因为它们在描述资源时变得至关重要。但是如果需要，您可以在基本 URI 中包含附加参数。例如，亚马逊的 S3 将你正在使用的桶的名称合并到基本 URI 中。

您还可以通过设置`protocols`字段来指示您的 API 在 HTTPS 是否可用，例如:

```
protocols: [ HTTP, HTTPS ]
```

您可以指定 API 将返回的默认媒体类型，例如:

```
mediaType: application/json
```

稍后，当我们看回应时，我们将看看如何描述多种回应媒体类型，以及对它们有什么期望。

您还可以将额外的文档或页面合并到您的 RAML 文件中。例如:

```
documentation
	- title: Home
		content: |
			This example documentation forms part of a tutorial for a 
			[Sitepoint](https://www.sitepoint.com/) article.
```

> 注意如何在元信息中包含降价。

这里还可以包含一些其他的东西，比如如何实现安全性，以及定义**特征**。稍后我们将研究这些概念。

这就是所涵盖的大部分基本信息，现在让我们转到参考资料。

### 描述资源

继续从 RAML 文件的顶层开始，下一步是概述可用的资源。这里有一个例子:

```
/albums:
	displayName: Album
/artists:
	displayName: Artist
```

> 属性是可选的，但是可以帮助描述资源“在现实生活中”代表什么。

这里我们定义了两个顶级资源，(音乐)专辑和资源。我们还没有描述可以用来与它们交互的 HTTP 方法，但是首先让我们深入一点，看看资源 URIs 和子资源。

让我们假设特定的专辑由它们的 ISRC(国际标准录音代码)来表示；因此，让我们扩展我们的`/albums`资源来描述这一点:

```
/albums:
	/{isrc}:
		uriParameters: 
      isrc:
        description: The International Standard Recording Code which uniquely identifies this album
		type: string
		pattern: ^[a-zA-Z]{2}\-[0-9a-zA-Z]{3}\-\d{2}\-\d{5}$
```

如你所见，URI 参数用花括号表示。`uriParameters`属性可用于提供关于这些参数的附加信息，包括元信息(在本例中是一个`description`)以及它们格式的具体定义——在本例中是数据类型和定义特定格式的正则表达式。

使用嵌套，我们可以描述子资源。例如，假设我们提供一个端点来检索单个专辑的曲目列表:

```
/albums:
	/{isrc}:
		uriParameters: 
      isrc:
        description: The International Standard Recording Code which uniquely identifies this album
		type: string
		pattern: ^[a-zA-Z]{2}\-[0-9a-zA-Z]{3}\-\d{2}\-\d{5}$
		/tracks:
			displayName: Tracklisting
```

您可以拥有任意多的资源嵌套；只要使用 YAML 风格的缩进。

现在让我们继续定义可以在这些资源上使用的方法。

### HTTP 方法

大多数 REST _ ful _ APIs 倾向于实现四种最常见的 HTTP 方法；获取是为了检索，上传是为了更新，发布是为了创建，删除是为了删除资源。我们的示例 API 也不例外。因此，让我们修改我们的相册资源来反映这一点:

```
/albums:
	get:
		description: Retrieve a list of albums
	post:
			description: Create a new album
	/{isrc}:
		uriParameters: 
      isrc:
        description: The International Standard Recording Code which uniquely identifies this album
		type: string
		pattern: ^[a-zA-Z]{2}\-[0-9a-zA-Z]{3}\-\d{2}\-\d{5}$
		get:
			description: Retrieve the specified album
		put:
			description: Update an album		
		delete:
			description: Delete this album
```

这里我们指出对`/albums`的`GET`请求将检索专辑列表，对同一个 URI 的`POST`用于创建一个。向下钻取，对`/albums/{isrc}`的`GET`请求将检索关于特定专辑的信息，使用 URI 参数`isrc`指定。一个`PUT`请求用于更新相册，一个`DELETE`请求用于删除相册。

### 描述响应

现在我们已经定义了我们的资源和可用的方法，我们需要能够描述用户可以预期的响应。

为了证明这一点，我们将提供一些关于特定专辑的额外细节。

为了描述响应，我们需要再深入几个层次。首先，我们需要描述可能的响应代码，然后我们需要将响应分解成它们的组件——通常是响应体——然后是可能的响应媒体类型。这里有一个例子可以更清楚地说明这一点:

```
/albums:
	/{isrc}:		
		get:
			description: Retrieve the specified album
		responses:
       200:
         body:
           application/json:
             schema: |
               { 	"$schema": "http://json-schema.org/schema",
                 	"type": "object",
                 	"description": "An album",
                 	"properties": {                    
										"title": { "type": "string" },
										"artist": { "type": "string" },
										"label": { "type": "string" },
										"year": { "type": "integer" }
									},
									"required": [ "title", "artist" ]
                }
				example: |
                { "title": "Dubnobasswithmyheadman",
									"artist": "Underworld",
									"label": "Junior Boy's Own",
									"year": 1994
                }
```

这里我们展示了一个成功的响应——即一个 HTTP 响应代码为 200 的响应。我们特别感兴趣的是响应体，尽管您也可以在这里定义任何响应头。然后，我们深入研究可用的响应类型；在这种情况下，我们只是表示 JSON——尽管如果 API 支持，您可以自由定义多种响应类型。

一旦我们进入成功响应的主体，我们指定两个属性；`schema`和`example`。

`schema`属性包含一个 [JSON 模式](http://json-schema.org/)，它定义了预期 JSON 的结构。我将很快在另一篇文章中讨论 JSON 模式。`example`属性就包含了这一点，它清楚地表明了调用您的 API 的人会得到什么样的响应。

### 查询参数

我们可以用与定义 URI 参数非常相似的方式定义查询参数(通常用于 GET 请求)。

为了说明，让我们再看一下我们的`/albums`端点，假设我们想要实现分页；因此，我们可能希望提供一个名为`page`的查询参数，它允许请求方指定他们想要检索的页面。

我们可以这样做:

```
/albums:
  get:
    description: Retrieve a list of albums
    queryParameters:
      page:
        description: Specify the page that you want to retrieve
        type: integer
        example: 1
```

同样，查询参数的定义是元信息——比如`description`和`example`——和一些属性的混合，这些属性有助于明确定义 API 期望的内容；这里我们要明确的是`page`参数应该是一个整数。

### 请求日期

让我们再来看看我们的“创建相册”端点，您会记得它涉及到向`/albums` URI 发出`POST`请求。

我们可以通过描述某人需要包含的数据、用于提供数据的机制以及关于各个字段的一些细节来扩展这一点。

这里有一个例子:

```
/albums:
	get:
		description: Retrieve a list of albums
	post:
		description: Create a new album
	body:
		application/x-www-form-urlencoded:
			formParameters:
				isrc:
					description: The International Standard Recording Code which uniquely identifies this album
					type: string
					pattern: ^[a-zA-Z]{2}\-[0-9a-zA-Z]{3}\-\d{2}\-\d{5}$
					required: true
				name:
					description: The name of the album
					type: string
					required: true
				artist:
					description: The name of the artist
					type: string
					required: true
				label:
					description: The label it was released under
					type: string
					required: false
				year:
					description: The year the album was released
					type: integer
					required: false
					minimum: 1900
					maximum: 3000
```

在这里，我们定义了发送到这个端点时预期的请求体。我们指出请求的类型应该是`application/x-www-form-urlencoded`。

接下来，我们用`formParameters`属性将预期的请求体分解成参数。然后我们列出可能的字段，提供关于每个字段的一些元数据，以及预期的类型。我们还可以指出哪些字段是必填的，哪些是可选的，以及一些验证规则——在这种情况下，我们使用正则表达式来指定 ISRC 的格式，以及发布年份的一些相对合理的界限。

### 安全性

您的 API 有可能以某种方式得到保护——无论是使用 OAuth、访问令牌还是简单的 HTTP 基本认证。

在 RAML 中，你可以在文件的顶部定义你的安全方案，以及基本信息。你可以在规范文档中找到一些例子[，但是作为一个例子，让我们看看 OAuth2。安全方案定义可能是这样的:](http://raml.org/spec.html)

```
securitySchemes:
  - oauth_2_0:
      description: |
        Acme uses OAuth2 to authenticate most requests
      type: OAuth 2.0
      describedBy:
        headers:
          Authorization:
            description: |
              Used to send a valid OAuth 2 access token. Do not use
              with the "access_token" query string parameter.
            type: string
        queryParameters:
          access_token:
            description: |
              Used to send a valid OAuth 2 access token. Do not use together with
              the "Authorization" header
            type: string
        responses:
					400:
            description: |
              Bad OAuth request (e.g. wrong consumer key, bad nonce, expired
              timestamp, etc.)
          401:
            description: |
              Bad or expired token. To fix it, re-authenticate the user.          
      settings:
        authorizationUri: https://acme.com/oauth/authorize
        accessTokenUri: https://acme.com/oauth/token
        authorizationGrants: [ code, token ]
```

如果你仔细阅读，你会发现它提供了许多关键信息；

*   `type`表示我们正在实现 OAuth 2.0
*   为了进行认证，正在讨论的 API 期望**或者**一个`Authorization`头**或者**一个`access_token`查询参数
*   它列出了可能的响应、它们的含义以及如何修复它们
*   `settings`是 OAuth 特有的，但仍然至关重要；它告诉用户如何授权、从哪里获得访问令牌以及该 API 支持的 OAuth 授权类型。

然而，这仅仅定义了安全方案；我们仍然需要指出这是我们用来保护我们的 API 的。

一种方法是在 RAML 文件的顶部添加以下内容:

```
securedBy: [oauth_2_0]
```

> 请注意，`oauth_2_0`匹配紧接在`securitySchemes`下面的元素

然而，一些 API 使得一些端点公开可用，但是其他端点可能受到保护。您可以基于每个端点定义安全方法，例如:

```
/albums:
	get:
		securedBy: [null, oauth_2_0]
	post:
		securedBy: [oauth_2_0]
```

这里我们指出，对于获取相册列表，认证是可选的，但是用户*必须*通过认证才能创建相册。

### 特征

您可能会有某些行为、策略或特征在不同的端点之间是通用的。分页就是一个很好的例子；支持分页的各种集合无疑会使用相同的方法来保持 API 的一致性。或者，正如我们在安全性一节中看到的，您可能有不同程度的安全性，例如公共或“需要授权”。

您可以定义**特征**，而不是在多个端点上重复相同的配置。总的来说，这个概念与 PHP 中的特征同义。

下面是一个简单的例子，创建一个特征来表明来自给定端点的结果支持简单分页:

```
traits:
  - paged:
      queryParameters:
		page:
			description: Specify the page that you want to retrieve
			type: integer
			required: true
			example: 1
```

现在您可以使用`is`属性将它应用到一个端点，如下所示:

```
/albums:  
  get:
    is: [ paged ]
```

也可以传入变量。作为一个例子，让我们扩展我们的`paged`特征，在每页中包含一些结果。我们将指定每页的最大结果数，它可以基于每个端点被覆盖。以下是特质的定义:

```
traits:
  - paged:
      queryParameters:
		page:
			description: Specify the page that you want to retrieve
			type: integer
			required: true
			example: 1
        perPage:
			type: integer
			description: The number of results per page, not to exceed <<maxPerPage>>
			maximum: <<maxPerPage>>
```

注意我们如何使用变量`<<maxPerPage>>`作为最大限制，以及将它代入我们生成的文档中。

要使用它，将`GET /albums`端点的定义更改如下:

```
/albums:  
  get:
    is: [ paged : { maxPerPage : 50 } ]
```

在规范中，您会发现更多特征的例子，以及资源类型——它们与特征[共享某些特征。您还会发现大量其他文档，以及一些您可能需要描述的更复杂场景的示例。但是现在让我们来看看 RAML 的一些实际应用。](http://raml.org/spec.html)

## 实践中的 RAML

既然我们已经了解了如何编写一个简单的 RAML 来描述我们的 RESTful API，那么是时候看看我们可以用它做些什么了。

### 生成 API 文档

RAML 是一个非常详细、明确的 API 概述，但是对于我们 API 的消费者来说，它不是非常用户友好。

然而，因为它是一种定义良好的开放格式，所以有一些工具可以帮助我们以一种更加用户友好的方式使用这些信息。最值得注意的是，有一些工具可以获取我们的 RAML 文件并生成基于 HTML 的文档，准备在 Web 上发布。

我们来看几个。

#### 将 RAML 转换为 HTML

##### raml2html

[raml2html](https://github.com/kevinrenskers/raml2html) 工具，顾名思义，可以把你的 raml 文件转换成 html 文档。

以下是开箱即用的屏幕截图:

![An Example set of documentation from the raml2html tool](img/41206b4c96cfcd0792c24589493aeaf7.png)

要使用它，首先通过 [npm](https://www.npmjs.com/) 安装它:

```
npm i -g raml2html
```

然后，要将一个 RAML 文件转换成 HTML，您只需这样做:

```
raml2html api.raml > index.html
```

结果是一组相当合理的 API 文档。它有一些依赖项——比如 JQuery 和 Bootstrap——是从 CDN 中获取的，但是当然你可以根据自己的需要修改生成的 HTML。如果愿意，您可以创建自己的模板，并通过命令行简单地指定它们:

```
raml2html -t custom-template.handlebars -r custom-resource.handlebars -m custom-item.handlebars -i example.raml -o example.html
```

还有一个 [Grunt 插件](https://www.npmjs.com/package/grunt-raml2html)和一个 [Gulp 插件](https://www.npmjs.com/package/gulp-raml2html)，使得生成文档更加容易。

有一定的局限性。安全信息不会复制到 HTML 中，所以如果您使用 OAuth，您可能需要自己单独编写文档。

##### php-raml2html

还有一个 PHP 等价物叫做 [php-raml2html](https://github.com/mikestowe/php-raml2html) 。

以下是该工具输出的屏幕截图:

![An example screenshot of documentation produced using php-raml2html](img/0d39bea3c60d369dbedbb26fac893544.png)

还有一个[在线演示](http://www.mikestowe.com/demos/raml2htmlphp/)，你可以在这里找到[文档](http://www.mikestowe.com/2014/05/raml-2-html.php)。

我个人更喜欢 raml2html，原因有二。首先，默认输出要干净一点。其次，它产生静态 HTML——而 php-raml2html 需要 php 来运行——这意味着它非常适合托管在 Github 页面或类似的页面上。然而，如果您主要是 PHP 开发人员，那么您可能会发现这个工具更容易定制。

### API 控制台

您可以使用 [API 控制台](https://github.com/mulesoft/api-console) web 组件从 RAML 文件生成更复杂的文档。产生的 HTML 在风格上类似于 raml2html，但是它有一个杀手级的特性；它提供了一个控制台，允许人们从文档中与您的 API 进行交互。它负责为您构建任何可用参数的表单，根据您的定义对它们进行验证，并能够确定如何执行身份验证。

为了看到它的实际效果，Twitter 和 T2 Github 都有在线演示。如果你一直在使用 Anypoint 平台，你应该已经看到了；它是用来生成右边栏中的交互式文档的。

API 控制台是使用 Angular 实现的，但是您不一定需要任何使用它的经验。

要快速启动并运行，只需遵循以下步骤:

1.  克隆存储库:`git clone git@github.com:mulesoft/api-console.git`

2.  将`dist`文件夹复制到一个空目录中，以保存您的文档

3.  将您的`.raml`文件复制到该文件夹中的某个位置

4.  如下所示创建一个 HTML 文件:

```
<head>
  <link rel="stylesheet" href="dist/styles/app.css" type="text/css" />
</head>
<body ng-app="ramlConsoleApp" ng-cloak id="raml-console-unembedded">
 <raml-console src="api.raml"></raml-console>
  <script src="dist/scripts/vendor.js"></script>
  <script src="dist/scripts/app.js"></script>
</body>
```

> 修改`<raml-console>`指令的`src`属性以指向正确的文件

1.  通过某种 web 服务器打开文件；无论是 Apache、Ngnix 还是 Connect

这就是全部了。

您也可以将 API 控制台嵌入 iframe 中；详情请参考[项目的自述文件](https://github.com/mulesoft/api-console)。

### 笔记本电脑

API 笔记本是另一种允许人们与你的 API 互动的方式。您可以将可运行的示例嵌入到您的 web 页面中，使用 RAML 来描述底层 API。

这也许最好用一个例子来说明；[点击此处](https://api-notebook.anypoint.mulesoft.com/examples/github)观看使用 API 笔记本与 Github 的 API 交互的演示。

你可能希望[从这里](https://api-notebook.anypoint.mulesoft.com/help/api-guide)开始，因为文档的这个特殊部分是专门针对创建一个运行 RAML 的笔记本的。

## 其他工具

还有许多其他工具可以用来处理 RAML 文件。比如说；

您可以使用这个命令行工具将 RAML 转换成 Markdown 格式。

RAML 转 wiki 工具允许您将 RAML 转换成适合 Confluence 或 JIRA 中 Wiki 的内容。

在这个项目页面上，您会找到一个完整的可用工具列表，用于处理 RAML [。](http://raml.org/projects.html)

### 构建您自己的工具

因为它是一个开放的标准，你可以自由地构建任何你认为有用的东西来使用 RAML 文件。

一个好的起点可能是这个基于 PHP 的 RAML 解析器。它允许您将模式转换为验证对象，提取路由列表等等。

还有针对 [JavaScript](https://github.com/raml-org/raml-js-parser) 、 [Java](https://github.com/raml-org/raml-java-parser) 、 [Python](https://github.com/an2deg/pyraml-parser) 和 [Ruby](https://github.com/coub/raml_ruby) 的解析器。

## 结论

在本文中，我对 RAML——RESTful API 建模语言——有了一个相当全面的了解。它提供了一种用清晰、全面和明确的术语描述 RESTful API 的方法。

也许 RAML 最明显也可能是最有用的用途是生成文档。我们已经研究了一些方法，最著名的是 API 控制台项目，它不仅为您生成基于 HTML 的文档，还提供了一个交互式控制台，允许读者在文档中试用您的 API。

我们还研究了一系列其他工具。作为一个相对较新的标准，预计在不久的将来会看到更多。

如果你想了解更多，我鼓励你通读一下[规范](http://raml.org/spec.html)，你也可以在 Github 上找到[。](https://github.com/raml-org/raml-spec)

## 分享这篇文章