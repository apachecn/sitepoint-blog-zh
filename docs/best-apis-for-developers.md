# 面向开发人员的最佳 API

> 原文：<https://www.sitepoint.com/best-apis-for-developers/>

API 在互联网上无处不在，知道如何与它们交互是现代 web 开发人员的一项重要技能。能够访问第三方数据并与第三方数据进行交互可以以许多很酷的方式将您的应用程序提升到一个新的水平！

在本文中，我们将看看开发人员可以访问的一些最好的 API，以及 APILayer 如何使它变得容易。

我们与[养蜂人](https://apilayer.com/?utm_source=Sitepoint&utm_medium=Leads%20Acquisition&utm_content=articleapilayer)合作创作了这篇文章。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

## 什么是 API？

API 代表**应用编程接口**，它可以描述任何允许两个程序相互通信的东西。例如，像`document.insertNode`这样的方法实际上并不是 JavaScript 的一部分:它们是 DOM API 的一部分，允许你的程序与 DOM 通信。

Web APIs 允许您的代码以可控的方式与网站和在线服务进行通信和交互。在大多数情况下，这是通过发送返回一些数据的请求来完成的，这些数据几乎总是 JSON 格式的。然后可以在您的代码中使用这些数据。

许多 API 还允许您将一些工作负载从应用程序转移到外部服务。例如，如果您想翻译一个文档，您可以避免为此编写所有代码，而是使用 API。

## 播放器

APILayer 是一个管理的 API 市场。每个 API 都是由 APILayer 的开发团队精心挑选的，以确保它的安全和稳定，并能够随着您的网站的增长而扩展。所有的 API 都有一个免费计划，你不需要交出任何支付细节就可以开始使用。APILayer 目前拥有近 100 个 API，并且还在不断增加。这些 API 涵盖了从金融到食品的一系列类别，还提供了许多 web 开发工具，如图像处理和拼写检查。

每一个 API 都有不同的付费等级，从免费开始，根据你的请求数量增加价格。一些付费计划也可以提供更多种类的数据。APILayer 使您可以轻松注册感兴趣的 API，并提供实时演示功能，让您可以直接在浏览器中测试 API 的工作方式，并提供一些示例代码来帮助您入门。它还提供了每个 API 的完整文档和评论。

## 与 API 交互

通过 JavaScript 与 API 交互的最简单方法是使用`fetch`。这异步地从一个 URL 请求数据并返回一个[承诺](https://www.sitepoint.com/overview-javascript-promises/)，允许我们访问返回的数据。

让我们看一个简单的例子:

```
fetch("https://geek-jokes.sameerkumar.website/api?format=json")
  .then(response => response.json())
  .then(result => console.log(result.joke))
  .catch(error => console.log('error', error.message)) 
```

这段代码将从 Geek 笑话 API 请求数据，该 API 以 JSON 格式返回一个随机笑话。我们将 API URL 传递给`fetch`函数。这返回了一个承诺，我们可以将`then`方法链接到它——首先，使用`json()`方法将返回的 JSON 转换成 JavaScript 对象，然后简单地将该对象的`joke`属性记录到控制台。我们还在末尾添加了一个 catch 块来记录任何错误。

Fetch 请求接受第二个参数，您可以为请求指定任何选项，包括头。一组相当标准的选项如下所示:

```
const headers = new Headers()

const requestOptions = {
  method: 'GET',
  redirect: 'follow',
  headers
} 
```

然后可以将这些添加到请求中:

```
fetch("https://geek-jokes.sameerkumar.website/api?format=json", requestOptions)
  .then(response => response.json())
  .then(result => console.log(result.joke))
  .catch(error => console.log('error', error.message)) 
```

大多数服务都需要提供一个密钥来授权对 API 的访问。这可以作为标题的一部分或包含在 URL 中。

隐藏您的 API 键非常重要。通常，它们应该存储在服务器上并进行加密。这是为了防止它们在源代码中可见并被其他人使用。当你向该公司付费订阅时，这通常是一个更大的问题，因为该公司不想免费提供他们的代码。

## 六下子滋味

让我们来看看开发人员可以在 APILayer 上访问的六个最好的 API。请注意，对于下面的所有示例，您必须更改访问键(API 键)以匹配您自己的键。

### Spotify

Spotify API 是 APILayer 上最大(也是最酷)的 API 之一。它允许你访问 Spotify 上的大量音乐和播客数据。

使用此 API，您可以获得专辑、专辑曲目、艺术家、艺术家专辑、播放列表、曲目和曲目歌词等数据。这意味着你可以搜索一个艺术家，并收到关于他们所有已出版的作品/专辑的信息，包括何时发行以及署名的作家和音乐家。

下面是一个返回给定艺术家的所有专辑的示例:

```
const headers = new Headers()
headers.append("apikey", "YOUR_API_KEY")

const getAlbums = event => {
  event.preventDefault()
  const requestOptions = {
    method: 'GET',
    redirect: 'follow',
    headers
  }
  fetch("https://api.apilayer.com/spotify/search?q=" + encodeURIComponent(event.target.artist.value), requestOptions)
    .then(response => response.json())
    .then(result => {document.getElementById("albums").innerHTML = result.albums.items.map(x => `<li>${x.data.name}</li>`).join``})
    .catch(error => console.log(error.message))
}

document.getElementById("music").addEventListener("submit", getAlbums) 
```

### 世界新闻

[世界新闻 API](https://apilayer.com/marketplace/world_news-api?utm_source=Sitepoint&utm_medium=Leads%20Acquisition&utm_content=articleworldnews) 让你获得来自世界各地的数以千计的新闻来源。结果来自各种新闻渠道，这意味着你可以从不同的角度获得相同的新闻故事。

API 还返回大量数据，包括每个新闻故事的标题、署名、作者和摘要，以及任何可用的图片。该 API 允许您按日期和地点搜索故事，这意味着您可以从特定地点获得最新的新闻和历史故事。也可以在搜索中指定语言和原产国。还有一个漂亮的人工智能功能，可以将故事分为“正面”或“负面”，允许您更进一步地筛选返回的故事。

下面是一个返回今天五大新闻的示例:

```
const headers = new Headers()
headers.append("apikey", "YOUR_API_KEY")

const requestOptions = {
  method: 'GET',
  redirect: 'follow',
  headers
}

const today = new Date()
const date = `${today.getFullYear()}-${today.getMonth()+1}-${String(today.getDate()).padStart(2,'0')}`

fetch(`https://api.apilayer.com/world_news/search-news?number=5&language=en&earliest-publish-date=${date}`, requestOptions)
  .then(response => response.json())
  .then(result => render(result))
  .catch(error => console.log('error', error))

const render = data => document.getElementById("news").innerHTML = data.news.map(story => `<h2>${story.title}</h2><img src=${story.image}/><section>${story.summary}</section>`).join`` 
```

### 天气烟囱

[Weatherstack API](https://weatherstack.com/?utm_source=Sitepoint&utm_medium=Leads%20Acquisition&utm_content=articleweatherstack) 允许你从世界任何地方访问任何给定位置的当前和历史天气统计数据。当前功能让您可以访问实时天气统计数据，如温度、风向、压力和能见度。

这里有大量的数据要访问，特别是可以追溯到 2008 年 7 月的历史天气功能，这意味着您可以获得 2008 年 7 月以后任何日期任何地方的天气统计数据。地点和日期需要作为参数指定。历史功能还有一个可选的“每小时”参数，可以设置为 1 或 0，这取决于您是否希望天气数据每小时拆分一次。如果选择每小时拆分数据，还可以将可选参数“interval”设置为 1、3(默认)、6、12 或 24，具体取决于您希望如何拆分数据。

历史要素和当前要素都有一个可选的“单位”参数，在该参数中，您可以将数据的单位设置为公制的“m”、华氏的“f”或科学的“s”。

以下是返回给定位置的当前温度的代码示例:

```
const getWeather = event => {
  event.preventDefault()
  const requestOptions = {
    method: 'GET',
    redirect: 'follow',
  }
    fetch("https://api.weatherstack.com/current?access_key=YOUR_API_KEY&query="+encodeURIComponent(event.target.city.value), requestOptions)
    .then(response => response.json())
    .then(result => document.getElementById("weather").innerHTML = `<h1>The current temperature in ${result.location.name} is ${result.current.temperature}℃</h1>`)
    .catch(error => console.log(error.message))
}

document.getElementById("place").addEventListener("submit", getWeather) 
```

### 数字验证

[号码验证 API](https://numverify.com/?utm_source=Sitepoint&utm_medium=Leads%20Acquisition&utm_content=articlenumverify) 允许你查找 200 多个国家的电话号码并验证其真实性。该 API 还将返回有关国家、国家代码、移动运营商和线路类型(移动或固定电话)的详细信息。这将允许您实时验证 web 应用程序用户提供的电话号码。

下面是一个让你在表单中输入电话号码的例子。提交表单将返回该号码是否有效。如果有效，它还会显示电话号码所在的国家:

```
const headers = new Headers()
headers.append("apikey", "YOUR_API_KEY")

const verify = event => {
  event.preventDefault()
  const requestOptions = {
    method: 'GET',
    redirect: 'follow',
    headers
  }
  fetch(`https://api.apilayer.com/number_verification/validate?number=${event.target.number.value}`, requestOptions)
  .then(response => response.json())
  .then(result => render(result))
  .catch(error => console.log('error', error.message))
}

document.getElementById("phone").addEventListener("submit",verify)

const render = data => document.getElementById("verification").textContent = data.valid ? `This is a valid phone number from ${data.country_name}` : `This is not a valid phone number` 
```

### 曾泽普

Zenserp API 是一个 serp(搜索引擎结果页面)API，它允许你以更清晰的对象格式抓取搜索引擎结果页面。其端点包括 Google、图片反向搜索、YouTube、Bing 和 DuckDuckGo。谷歌端点还包括图像、地图、视频、新闻和购物的子端点。结果还包括排名信息，这将有助于您识别热门网页和趋势。

还有一些列表端点可用于生成您自己的请求。这些包括:谷歌语言，谷歌国家，地理位置和谷歌搜索引擎。

给定一个搜索项，API 将返回所有出现的页面，包括它的 URL 和描述。

这里有一个例子，它将返回某个主题的顶级 Google 搜索:

```
const headers = new Headers()

const serp = event => {
  event.preventDefault()
  const requestOptions = {
    method: 'GET',
    redirect: 'follow',
    headers
  }
  fetch(`https://app.zenserp.com/api/v2/search?apikey=YOUR_API_KEY&q=${encodeURIComponent(event.target.q.value)}`, requestOptions)
  .then(response => response.json())
  .then(result => render(result))
  .catch(error => console.log('error', error.message))
}

document.getElementById("search").addEventListener("submit",serp)

const render = data => document.getElementById("result").innerHTML = `The top search result on Google is: <a href="${data.organic[0].url}"><h1>${data.organic[0].title}</h1></a><p>${data.organic[0].description}</p>` 
```

### Zenscrape

Zenscrape API 是一个网络抓取 API，它的基本用途是返回任何给定网站的 HTML 内容。为此，您只需要提供一个参数—目标网站的 URL 和 API 密钥。它还将从任何 JavaScript 或前端框架(如 React 或 Vue)在页面上生成任何动态 HTML。这意味着返回的 HTML 将与用户看到的内容相同。

起初，这似乎不是你需要使用的东西。但是网络抓取——因此这个 API——对于交互和分析统计数据非常有用。例如，您可以使用这个 API 来创建一个应用程序，比较多个不同网站上的产品价格，并向用户显示为该产品提供最佳价值的购物网站。

API 还提供了一个 HTTP 代理接口。HTTP 代理是一种高性能的内容过滤器，可以过滤所有 web 流量，标记任何可能是病毒或侵入性和不需要的数据的可疑内容。该接口允许您使用任何已经依赖于代理的应用程序，您只需使用 API 密钥作为用户名，并包含通常作为密码提供的任何参数。

这还允许您指定特定的代理位置，以获得返回的基于地理位置的特定内容。所使用的代理也会自动轮换，以帮助保持抓取机器人匿名，并阻止网站限制您可以抓取它们的次数。

下面是一个让您输入 URL 并将 HTML 返回到控制台的示例:

```
const headers = new Headers()

const scrape = event => {
  event.preventDefault()
  const requestOptions = {
    method: 'GET',
    redirect: 'follow',
    headers
  }
  fetch(`https://api.allorigins.win/get?url=${encodeURIComponent(`https://app.zenscrape.com/api/v1/get?apikey=YOUR_API_KEY&url=${encodeURIComponent(event.target.url.value)}`)}`,requestOptions)
  .then(response => response.json())
  .then(result => console.log(result.contents))
  .catch(error => console.log('error', error.message))
}

document.getElementById("scrape").addEventListener("submit",scrape) 
```

## 结论

这六个例子仅仅触及了 APILayer 所能提供的皮毛。还有更多可用的 API，所以请确保您充分探索了该网站，并从中获得了乐趣。例如，我们强烈推荐[不良词汇 API](https://apilayer.com/marketplace/bad_words-api?utm_source=Sitepoint&utm_medium=Leads%20Acquisition&utm_content=articlebadword) ，我们俩都很喜欢玩它！

## 分享这篇文章