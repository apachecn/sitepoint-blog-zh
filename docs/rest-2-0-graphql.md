# REST 2.0 在这里，它的名字是 GraphQL

> 原文：<https://www.sitepoint.com/rest-2-0-graphql/>

![Abstract network design representing GraphQL querying data](img/0126920b4397c41347612af1fec78436.png)

GraphQL 是一种用于 API 的查询语言。尽管 GraphQL 与 REST 有本质的不同，但它可以作为 REST 的替代品，提供高性能、出色的开发人员体验和非常强大的工具。

在整篇文章中，我们将探讨如何用 REST 和 GraphQL 处理一些常见的用例。本文包含三个项目。您将找到 REST 和 GraphQL APIs 的代码，这些 API 提供有关流行电影和演员的信息，以及一个用 HTML 和 jQuery 构建的简单前端应用程序。

我们将使用这些 API 来研究这些技术的不同之处，以便我们能够确定它们的优势和劣势。然而，首先，让我们快速了解一下这些技术是如何产生的。

## 网络的早期

网络的早期很简单。Web 应用程序开始于早期互联网上的静态 HTML 文档。先进的网站包括存储在数据库(如 SQL)中的动态内容，并使用 JavaScript 增加交互性。绝大多数网络内容是通过台式电脑上的浏览器浏览的，一切都很好。

![Normal Image](img/1fbaf626d703517654d30c28a881e6bd.png)

## REST:API 的崛起

时间快进到 2007 年，史蒂夫·乔布斯推出了 iPhone。除了智能手机将对世界、文化和通信产生深远影响之外，它还使开发人员的生活变得更加复杂。智能手机打乱了发展现状。短短几年，我们突然有了台式机、iPhones、机器人和平板电脑。

作为回应，开发人员开始使用 RESTful APIs 向各种形状和大小的应用程序提供数据。新的开发模型看起来像这样:

![REST Server](img/63fdf1076accd3f38051e9d10c1181f0.png)

## graph QL:API 的发展

GraphQL 是一种用于 API 的**查询语言，由脸书设计并开源。您可以将 GraphQL 视为构建 API 的 REST 的替代方案。REST 是一个概念模型，您可以用它来设计和实现您的 API，而 GraphQL 是一种标准化的语言、类型系统和规范，它在客户机和服务器之间创建了一个强大的契约。让我们所有的设备通过一种标准语言进行通信，简化了创建大型跨平台应用程序的过程。**

使用 GraphQL，我们的图表简化了:

![GraphQL Server](img/e30ba3545faa34e9b6a2a5dbdaaf0dd6.png)

## GraphQL 与 REST

在本教程的其余部分(没有双关语的意思)，我鼓励您遵循代码！您可以在附带的 GitHub repo 中找到本文的代码。

该代码包括三个项目:

1.  RESTful API
2.  一个 GraphQL API 和
3.  用 jQuery 和 HTML 构建的简单客户端网页。

这些项目是有目的的简单，旨在尽可能简单地提供这些技术之间的比较。

如果你想继续做下去，打开三个终端窗口和项目存储库中的`RESTful`、`GraphQL`和`Client`目录的`cd`。从每个目录中，通过`npm run dev`运行开发服务器。一旦你准备好了服务器，请继续阅读:)

## 用 REST 查询

我们的 RESTful API 包含几个端点:

| 端点 | 描述 |
| --- | --- |
| /电影 | 返回包含电影链接的对象数组(例如[{ href:' http://localhost/movie/1 ' }] |
| /movie/:id | 返回 id = :id 的单个电影 |
| /movie/:id/演员 | 返回包含电影中 id = :id 的演员链接的对象数组 |
| /演员 | 返回包含参与者链接的对象数组 |
| /actor/:id | 返回 id = :id 的单个参与者 |
| /actor/:id/电影 | 返回一个对象数组，这些对象包含 id = :id 的演员所出演的电影的链接 |

> **注意**:我们的简单数据模型已经有 6 个端点需要维护和记录。

假设我们是客户端开发人员，需要使用我们的 movies API 用 HTML 和 jQuery 构建一个简单的网页。为了建立这个页面，我们需要关于我们的电影以及电影中出现的演员的信息。我们的 API 拥有我们可能需要的所有功能，所以让我们继续获取数据。

如果您打开一个新的终端并运行

```
curl localhost:3000/movies 
```

您应该会得到如下所示的响应:

```
[
  {
    "href": "http://localhost:3000/movie/1"
  },
  {
    "href": "http://localhost:3000/movie/2"
  },
  {
    "href": "http://localhost:3000/movie/3"
  },
  {
    "href": "http://localhost:3000/movie/4"
  },
  {
    "href": "http://localhost:3000/movie/5"
  }
] 
```

在 RESTful 方式中，API 返回了一个指向实际电影对象的链接数组。然后我们可以通过运行`curl http://localhost:3000/movie/1`来抓取第一部电影，用`curl http://localhost:3000/movie/2`抓取第二部电影，以此类推。

如果你看一下`app.js`,你可以看到我们获取填充页面所需的所有数据的函数:

```
const API_URL = 'http://localhost:3000/movies';
function fetchDataV1() {

  // 1 call to get the movie links
  $.get(API_URL, movieLinks => {
    movieLinks.forEach(movieLink => {

      // For each movie link, grab the movie object
      $.get(movieLink.href, movie => {
        $('#movies').append(buildMovieElement(movie))

        // One call (for each movie) to get the links to actors in this movie
        $.get(movie.actors, actorLinks => {
          actorLinks.forEach(actorLink => {

            // For each actor for each movie, grab the actor object
            $.get(actorLink.href, actor => {
              const selector = '#' + getMovieId(movie) + ' .actors';
              const actorElement = buildActorElement(actor);
              $(selector).append(actorElement);
            })
          })
        })
      })
    })
  })
} 
```

正如你可能注意到的，这并不理想。当一切都结束后，我们对我们的 API 进行了`1 + M + M + sum(Am)`次往返调用，其中 **M** 是电影的数量， **sum(Am)** 是 M 部电影中每部电影的演职员表数量的总和。对于具有少量数据需求的应用程序来说，这可能没问题，但是在大型的生产系统中，这是行不通的。

结论？我们简单的 RESTful 方法是不够的。为了改进我们的 API，我们可能会请后端团队的某个人为我们构建一个特殊的`/moviesAndActors`端点来支持这个页面。一旦该端点准备就绪，我们就可以用一个请求替换我们的`1 + M + M + sum(Am)`网络调用。

```
curl http://localhost:3000/moviesAndActors 
```

现在，它将返回一个类似如下的有效负载:

```
[
  {
    "id": 1,
    "title": "The Shawshank Redemption",
    "release_year": 1993,
    "tags": [
      "Crime",
      "Drama"
    ],
    "rating": 9.3,
    "actors": [
      {
        "id": 1,
        "name": "Tim Robbins",
        "dob": "10/16/1958",
        "num_credits": 73,
        "image": "https://images-na.ssl-images-amazon.cimg/M/MV5BMTI1OTYxNzAxOF5BMl5BanBnXkFtZTYwNTE5ODI4._V1_.jpg",
        "href": "http://localhost:3000/actor/1",
        "movies": "http://localhost:3000/actor/1/movies"
      },
      {
        "id": 2,
        "name": "Morgan Freeman",
        "dob": "06/01/1937",
        "num_credits": 120,
        "image": "https://images-na.ssl-images-amazon.cimg/M/MV5BMTc0MDMyMzI2OF5BMl5BanBnXkFtZTcwMzM2OTk1MQ@@._V1_UX214_CR0,0,214,317_AL_.jpg",
        "href": "http://localhost:3000/actor/2",
        "movies": "http://localhost:3000/actor/2/movies"
      }
    ],
    "image": "https://images-na.ssl-images-amazon.cimg/M/MV5BODU4MjU4NjIwNl5BMl5BanBnXkFtZTgwMDU2MjEyMDE@._V1_UX182_CR0,0,182,268_AL_.jpg",
    "href": "http://localhost:3000/movie/1"
  },
  ...
] 
```

太好了！在一个请求中，我们能够获取填充页面所需的所有数据。回顾我们的`Client`目录中的`app.js`,我们可以看到实际的改进:

```
const MOVIES_AND_ACTORS_URL = 'http://localhost:3000/moviesAndActors';
function fetchDataV2() {
  $.get(MOVIES_AND_ACTORS_URL, movies => renderRoot(movies));
}
function renderRoot(movies) {
  movies.forEach(movie => {
    $('#movies').append(buildMovieElement(movie));
    movie.actors && movie.actors.forEach(actor => {
      const selector = '#' + getMovieId(movie) + ' .actors';
      const actorElement = buildActorElement(actor);
      $(selector).append(actorElement);
    })
  });
} 
```

我们的新应用程序将比上一次迭代快得多，但它仍不完美。如果你打开`http://localhost:4000`看看我们简单的网页，你应该会看到这样的内容:

![Demo App](img/6ca9676fffae0d660db85773355bf1ab.png)

如果仔细观察，您会注意到我们的页面使用了电影的标题和图像，以及演员的姓名和图像(即，我们只使用了电影对象中 8 个字段中的 2 个和演员对象中 7 个字段中的 2 个)。这意味着我们浪费了大约四分之三的通过网络请求的信息！这种额外的带宽使用会对性能和基础设施成本产生非常实际的影响！

精明的后端开发人员可能会对此嗤之以鼻，并快速实现一个名为 fields 的特殊查询参数，该参数接受一个字段名数组，该数组将动态确定在特定请求中应该返回哪些字段。

例如，我们可以用`curl http://localhost:3000/moviesAndActors?fields=title,image`代替`curl http://localhost:3000/moviesAndActors`。我们甚至可能有另一个特殊的查询参数`actor_fields`,它指定应该包含演员模型中的哪些字段。如`curl http://localhost:3000/moviesAndActors?fields=title,image&actor_fields=name,image`。

现在，对于我们的简单应用程序来说，这将是一个近乎最优的实现，但是它引入了一个坏习惯，即我们在客户端应用程序中为特定页面创建自定义端点。当你开始构建一个显示与你的网页不同的信息的 iOS 应用程序和一个显示与 iOS 应用程序不同的信息的 Android 应用程序时，这个问题变得更加明显。

如果我们能够构建一个通用的 API 来明确地表示我们的数据模型中的实体以及这些实体之间的关系，但是不会遭受`1 + M + M + sum(Am)`性能问题的困扰，这不是很好吗？好消息！我们可以！

## 使用 GraphQL 查询

使用 GraphQL，我们可以直接跳到最佳查询，并获取我们需要的所有信息，只需一个简单、直观的查询:

```
query MoviesAndActors {
  movies {
    title
    image
    actors {
      image
      name
    }
  }
} 
```

说真的！要亲自尝试，请打开位于 [http://localhost:5000](http://localhost:5000) 的 GraphQL(基于 GraphQL IDE 的出色浏览器)并运行上面的查询。

现在，让我们再深入一点。

## GraphQL 中的思维

GraphQL 对 API 采取了与 REST 完全不同的方法。它不依赖于像动词和 URIs 这样的 HTTP 结构，而是在我们的数据之上构建了一个直观的查询语言和强大的类型系统。类型系统提供了客户端和服务器之间的强类型协定，而查询语言提供了一种机制，客户端开发人员可以使用这种机制高效地获取任何给定页面所需的任何数据。

GraphQL 鼓励您将数据视为虚拟的信息图。包含信息的实体称为类型，这些类型可以通过字段相互关联。查询从根开始，遍历这个虚拟图，同时一路获取所需的信息。

这个“虚拟图”更明确地表示为一个**模式**。一个**模式**是组成 API 数据模型的类型、接口、枚举和联合的集合。GraphQL 甚至包括一种方便的模式语言，我们可以用它来定义我们的 API。例如，这是我们的电影 API 的模式:

```
schema {
    query: Query
}

type Query {
    movies: [Movie]
    actors: [Actor]
    movie(id: Int!): Movie
    actor(id: Int!): Actor
    searchMovies(term: String): [Movie]
    searchActors(term: String): [Actor]
}

type Movie {
    id: Int
    title: String
    image: String
    release_year: Int
    tags: [String]
    rating: Float
    actors: [Actor]
}

type Actor {
    id: Int
    name: String
    image: String
    dob: String
    num_credits: Int
    movies: [Movie]
} 
```

类型系统为许多了不起的东西打开了大门，包括更好的工具、更好的文档和更高效的应用程序。我们可以讨论的内容太多了，但是现在，让我们跳过这一步，重点介绍几个展示 REST 和 GraphQL 之间差异的场景。

## GraphQL 与 Rest:版本控制

一个简单的谷歌搜索将会产生许多关于版本化(或进化)REST API 的最佳方式的意见。我们不会掉进那个兔子洞，但我想强调这是一个重要的问题。版本控制如此困难的原因之一是，通常很难知道哪些应用程序或设备正在使用哪些信息。

使用 REST 和 GraphQL 添加信息通常很容易。添加该字段，它将流向 REST 客户端，并在 GraphQL 中被安全地忽略，直到您更改查询。然而，删除和编辑信息是另一回事。

在 REST 中，很难知道在字段级别使用了什么信息。我们可能知道一个端点`/movies`正在被使用，但是我们不知道客户端是否正在使用标题、图像或者两者。一个可能的解决方案是添加一个查询参数`fields`来指定返回哪些字段，但是这些参数几乎总是可选的。出于这个原因，您将经常看到演化发生在端点级别，我们在这里引入了一个新的端点`/v2/movies`。这是可行的，但也增加了我们 API 的表面积，并增加了开发人员保持最新和全面文档的负担。

GraphQL 中的版本控制非常不同。每个 GraphQL 查询都需要准确地说明在任何给定的查询中需要哪些字段。事实上，这是强制性的，这意味着我们确切地知道什么信息被要求，并允许我们问的问题，多久和由谁。GraphQL 还包含一些原语，允许我们用不推荐使用的字段和消息来修饰模式，说明它们为什么不推荐使用。

这是 GraphQL 中的版本控制:

![Versioning in GraphQL](img/a84fc10a8d12da357890b5be602ea6b2.png)

## GraphQL vs REST:快取

REST 中的缓存简单而有效。事实上，缓存是 REST 的六个指导性约束之一，并被融入 RESTful 设计中。如果来自端点`/movies/1`的响应表明该响应可以被缓存，那么将来对`/movies/1`的任何请求都可以被缓存中的项目替换。简单。

GraphQL 中的缓存处理略有不同。缓存 GraphQL API 通常需要为 API 中的每个对象引入某种唯一标识符。当每个对象都有一个唯一的标识符时，客户端可以构建使用该标识符可靠地缓存、更新和终止对象的规范化缓存。当客户端发出引用该对象的下游查询时，可以使用该对象的缓存版本。如果您有兴趣了解更多关于 GraphQL 中的缓存是如何工作的，这里有一篇[很好的文章，更深入地讨论了这个主题](http://graphql.org/learn/caching/)。

## GraphQL 与 REST:开发者体验

开发经验是应用程序开发的一个极其重要的方面，也是我们作为工程师投入这么多时间来构建好的工具的原因。这里的比较有些主观，但我认为还是有必要提一下。

REST 屡试不爽，拥有丰富的工具生态系统来帮助开发人员记录、测试和检查 RESTful APIs。也就是说，开发人员为 REST APIs 的扩展付出了巨大的代价。端点的数量很快变得难以承受，不一致性变得更加明显，版本控制仍然很困难。

GraphQL 在开发者体验部门确实出类拔萃。类型系统为 GraphiQL IDE 等优秀的工具打开了大门，文档也内置在模式本身中。在 GraphQL 中，也只有一个端点，并且，不依赖于文档来发现哪些数据是可用的，您有一个类型安全的语言和自动完成，您可以使用它来快速跟上 API 的速度。GraphQL 还被设计为可以与 React 和 Redux 等现代前端框架和工具完美地协作。如果你正在考虑用 React 构建一个应用程序，我强烈推荐你试试 [Relay](https://facebook.github.io/relay/) 或者 [Apollo client](https://github.com/apollographql/apollo-client) 。

## 结论

GraphQL 为构建高效的数据驱动应用程序提供了一套更加固执己见但极其强大的工具。REST 不会很快消失，但是还有很多需要改进的地方，尤其是在构建客户端应用程序的时候。

如果你有兴趣了解更多，请查看 GraphQL 后端服务。在[几分钟内，您将拥有一个部署在 AWS](https://www.youtube.com/watch?v=yaacnYUqY1Q) 上的生产就绪的 GraphQL API，并准备好使用您自己的业务逻辑进行定制和扩展。

我希望你喜欢这篇文章，如果你有任何想法或意见，我很乐意收到你的来信。感谢阅读！

## 分享这篇文章