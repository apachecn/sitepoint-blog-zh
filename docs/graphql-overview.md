# GraphQL 概述:使用 React 前端构建待办事项列表 API

> 原文：<https://www.sitepoint.com/graphql-overview/>

*本文由[Panayiotis pvgr Velisarakos](https://github.com/pvgr)同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

想象你想按照食谱烤一个蛋糕。你需要一些配料，以及每种配料的正确数量。如果你能得到一个盒子，里面装着你的食谱需要的所有配料，并且已经按照你的食谱进行了测量和称重，那会怎么样？这肯定会使烘烤变得容易得多。如果你把前端 UI 想象成一个蛋糕，这就是 GraphQL 要做的事情。

在本教程中，我们将编写一个小的 GraphQL 服务器来响应来自[待办事项应用](http://todomvc.com/examples/react/#/)的请求。你可以在[那边的许多应用程序中选择](http://todomvc.com/)，但是因为我最近在做一个 React 项目，所以我会选择 React 作为前端框架。但是，您可以随意选择任何您喜欢的 JavaScript 框架。

## GraphQL

GraphQL 允许我们定义一个查询，在客户机和服务器之间提供一个公共接口，用于数据获取和操作。它处理一种查询语言，允许客户端描述它需要的数据及其形状，这种语言旨在通过为[提供直观而灵活的语法](https://facebook.github.io/graphql/#sec-Overview)来构建客户端应用程序。

这使得客户端从服务器检索数据更加高效。例如，假设客户端只需要 GraphQL 的[实现中的`title`和`id`，那么它应该这样做:](https://todo-graphql-server.herokuapp.com/graphql)

```
query Query {
  todos {
    id,
    title
  }
}
```

它产生结果数据(JSON 格式):

```
{
  "data": {
    "todos": [
      {
        "id": 1446412739542,
        "title": "Read emails"
      },
      {
        "id": 1446412740883,
        "title": "Buy orange"
      },
      {
        "id": 1446412741215,
        "title": "Fix garbage"
      }
    ]
  }
}
```

也许在我们的现场演示中还没有保存数据。这背后的原因是，每次我们运行服务器时，内存中存储 Todo 的数组都会变空。在接下来的几节中，我们将看到如何在数组中添加数据。

正如所看到的，响应格式是在查询中描述的，是由客户机而不是服务器定义的。如标题为[“graph QL 概述——graph QL 和 Node.js 入门”](https://blog.risingstack.com/graphql-overview-getting-started-with-graphql-and-nodejs/)的文章所述，

> GraphQL 查询就像没有属性的 JSON 对象。值得一提的是 *GraphQL 并不是特定于语言的*，它只是客户端和服务器之间的一个规范。任何客户端都应该能够与任何服务器进行通信，如果他们使用相同的语言。

## GraphQL.js 简介

[GraphQL.js](https://github.com/graphql/graphql-js) 是 GraphQL for JavaScript 的参考实现，它提供了两个重要的功能:

1.  构建类型架构。
2.  针对该类型模式提供查询。

需要构建一个映射到代码库的 GraphQL 类型模式。在下面的代码中，我们定义了一个简单的模式。它有一个类型和一个列表`Todo(s)`(其中每个元素有三个字段)*将*解析为一个固定值。此外，还需要提供针对该类型模式的查询结果。

```
var graphql = require ('graphql');

// Here is some dummy data to make this piece of code simpler.
// It will be changeable after introducing mutation.
var TODOs = [
  {
    "id": 1446412739542,
    "title": "Read emails",
    "completed": false
  },
  {
    "id": 1446412740883,
    "title": "Buy orange",
    "completed": true
  }
];

var TodoType = new graphql.GraphQLObjectType({
  name: 'todo',
  fields: function () {
    return {
      id: {
        type: graphql.GraphQLInt
      },
      title: {
        type: graphql.GraphQLString
      },
      completed: {
        type: graphql.GraphQLBoolean
      }
    }
  }
});

var queryType = new graphql.GraphQLObjectType({
  name: 'Query',
  fields: function () {
    return {
      todos: {
        type: new graphql.GraphQLList(TodoType),
        resolve: function () {
          return TODOs;
        }
      }
    }
  }
});

module.exports = new graphql.GraphQLSchema({
  query: queryType
});
```

现在让我们看一下 JavaScript 文件的代码，它用 JSON 给出了结果数据:

```
var graphql = require ('graphql').graphql
var express = require('express')
var graphQLHTTP = require('express-graphql')
var Schema = require('./schema')
var query = 'query { todos { id, title, completed } }'

graphql(Schema, query).then( function(result) {
  console.log(JSON.stringify(result));
  // Prints
  // {
  //   "data":{
  //     "todos":[
  //       {
  //         "id":1446412739542,
  //         "title":"Read emails",
  //         "completed":false
  //       },
  //       {
  //         "id":1446412740883,
  //         "title":"Buy orange",
  //         "completed":true
  //       }
  //     ]
  //   }
  // }
});

var app = express()
  .use('/', graphQLHTTP({ schema: Schema, pretty: true }))
  .listen(8080, function (err) {
    console.log('GraphQL Server is now running on localhost:8080');
  });
```

运行下面的代码可以获得上面代码给出的相同结果。`cURL`不是强制性的，以获得本例的进一步优势。这只是一种更简单的检索数据的方法，不会在浏览器中碰到我们的[例子](https://todo-graphql-server.herokuapp.com/graphql)。请注意，如果您是 Windows 用户，[您可以使用 Windows 命令提示符来运行`cURL`示例](https://support.zendesk.com/hc/en-us/articles/203691436-Installing-and-using-cURL#curl_win)。此外，[在这里你可以找到一个很好的资源来学习如何在你的系统](https://support.zendesk.com/hc/en-us/articles/203691436-Installing-and-using-cURL#install)上安装`cURL`。

```
$ curl -XPOST -H "Content-Type:application/graphql"  -d 'query { todos { title } }' http://localhost:8080
{
  "data": {
    "todos": [
      {
        "title": "Read emails"
      },
      {
        "title": "Buy orange"
      }
    ]
  }
}
```

关于模式的一个重要的事情是，它没有假设数据是如何存储的，因为它只描述了 API 的消费者可以使用什么。数据存储和表示的方式是实现细节。

## 反应

[React](https://facebook.github.io/react/index.html) 是由脸书和 Instagram 开发的用于创建用户界面的 JavaScript 库。许多人选择将 React 视为 [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) 模式中的 V。如[官网](https://facebook.github.io/react/docs/why-react.html)所述，

> 我们构建 React 是为了解决一个问题:用随时间变化的数据构建大型应用程序。这都是关于构建可重用的组件。事实上，唯一的事情就是构建组件。

如果您需要一个指南来做出反应，您可以阅读并观看以下资源:

*   [视频:React 入门](https://www.sitepoint.com/video-getting-started-react/)
*   [React JavaScript 库简介](http://developer.telerik.com/featured/introduction-to-the-react-javascript-framework/)
*   [视频:介绍单向数据流](https://www.sitepoint.com/video-introducing-one-way-data-flow/)

### 简单反应组分

React 组件实现了一个`render()`方法，该方法接收输入数据并返回要显示的内容。[这个例子使用了一种类似 XML 的语法，叫做 JSX](https://facebook.github.io/react/) 。JSX 是可选的，并不要求使用 React。JSX 是一种 JavaScript 语法扩展，看起来类似于 XML。您可以在 React 中使用简单的 JSX 语法转换。

`render()`可以通过`this.props`访问传入组件的输入数据。[下面是一个如何创建 React 组件的简单示例，它也可以作为 CodePen](http://codepen.io/SitePoint/pen/zrEYRb) 获得。

```
var Application = React.createClass({
  render: function() {
    return 
      { this.props.text }
      { this.props.id }
    ;
  }
});
```

有了前面的代码，这就是 JSX 编译器生成的原始 JavaScript 代码。

```
"use strict";
var Application = React.createClass({
  displayName: "Application",
  render: function render() {
    return React.createElement(
      "div",
      null,
      this.props.text,
      this.props.id
    );
  }
});
```

![a simple react component](img/b2d7d868eae49ad785bcfda41b96f439.png)

如果大家想深入了解 React 组件，请花一分钟时间观看视频[组件状态简介](https://www.sitepoint.com/video-an-introduction-to-component-state)。

## 我们例子中的一个走查

首先，我们需要一个服务器(启动并运行)来接收来自 Todo List 应用程序的 GraphQL 查询。这个服务器上面已经写过了。

要运行我们的服务器，请在 CLI 上执行:

```
$ git clone https://github.com/sitepoint-editors/todo-graphql-server.git
$ cd todo-graphql-server
$ npm install
$ npm start
```

![running server locally](img/987c0bef32e5327a7eccbd7d6db4c36a.png)

您必须拥有 Node v4.0.0 或更高版本，因为服务器代码使用了旧版本不支持的 [ES2015 特性](https://babeljs.io/docs/learn-es2015/)。

对端点`/graphql`的任何 POST 请求都将根据我们的 GraphQL 模式执行。要测试一切是否正常，请键入以下代码:

```
$ curl -XPOST -H "Content-Type:application/graphql"  -d 'query { todos { title } }' http://localhost:8080
{
  "data": {
    "todos": []
  }
}
```

尚未保存任何数据。因此，每次我们运行服务器时，内存中存储`todo(s)`的数组就会变空。当然，我们不希望对空数组进行只读访问。我们必须添加和更改数据。这种旨在产生副作用的操作在 GraphQL 中被称为*突变*。定义一个变异与定义一个查询是一样的，并且也返回一个类型化的值。这个想法是，如果某个东西发生了变异，那么它会返回任何变异的东西。

```
var MutationAdd = {
  type: new GraphQLList(TodoType),
  description: 'Add a Todo',
  args: {
    title: {
      name: 'Todo title',
      type: new GraphQLNonNull(GraphQLString)
    }
  },
  resolve: (root, {title}) => {
    TODOs.push({
      id: (new Date()).getTime(),
      title: title,
      completed: false
    });
    return TODOs;
  }
};

var MutationType = new GraphQLObjectType({
  name: 'Mutation',
  fields: {
    add: MutationAdd
  }
});

export var Schema = new GraphQLSchema({
  query: QueryType,
  mutation: MutationType
});
```

上面的箭头(`=>`)是定义函数的[新语法。ES2015 最有趣的新部分之一。](https://www.sitepoint.com/preparing-ecmascript-6-new-function-syntax/)

正如克莱·奥尔索普写的标题为[“你的第一个 GraphQL 服务器”](https://medium.com/@clayallsopp/your-first-graphql-server-3c766ab4f0a2#.us9jdjuy7)的文章中所解释的，

> 突变和查询之间有意义的区别在于，突变是串行处理的，但查询没有这样的保证(事实上，GraphQL 鼓励服务器利用独立查询的固有并行性)。GraphQL 规范给出了服务器必须按顺序处理的一组变异查询的示例:

```
{
  first: changeTheNumber(newNumber: 1) {
    theNumber
  },
  second: changeTheNumber(newNumber: 3) {
    theNumber
  },
  third: changeTheNumber(newNumber: 2) {
    theNumber
  }
}
```

因此，在请求结束时，`theNumber`字段应该具有`2`的值。在这个关于突变的快速介绍之后，我们终于可以在我们的服务器中添加一个`todo`。

```
$ curl -XPOST -H "Content-Type:application/graphql" -d 'mutation { add (title: "Clean garage") { id, title } }' http://localhost:8080
{
  "data": {
    "add": [
      {
        "id": 1446443172937,
        "title": "Clean garage"
      }
    ]
  }
}
```

![playing with the server](img/39ebafee68f3926d89cc31fca5e289c4.png)

天气很凉爽，不是吗？除此之外我们还有更多的添加变异:`toggle`、`toggleAll`、`destroy`、`clearCompleted`。还有`save`。需要注意的一点是，我们在所有的突变中都传递了参数。所有字段都可以接受参数。创建参数非常简单，可以在*函数* `resolve`中捕获。

一天结束时，我们有两种类型的查询:

*   一个用于从服务器获取( *get* )数据；
*   一个用于操作(*创建*、*更新*、*删除*数据。

有了一个正在运行的服务器，我们就可以使用 React: [中制作的 Todo 列表了 React TodoMVC 示例](https://github.com/tastejs/todomvc/tree/gh-pages/examples/react)的一个分支，就像开头提到的那样。要下载它，请执行:

```
$ git clone -b react-graphql https://github.com/sitepoint-editors/todomvc.git
$ cd todomvc
$ npm install
$ node server.js
```

转到`http://localhost:3000`查看正在运行的应用程序。该代码与[原代码](https://github.com/tastejs/todomvc)相比有两大变化。首先，`TodoModel`已经被更改为到达 GraphQL 服务器。

![react component model](img/ed2c1c7a0e766c321b3489ff23f4b30d.png)

其次，服务器中的代理将 GraphQL 请求重定向到我们创建的服务器。有关更多细节，请参见下图。

![server proxy](img/dd0b323eae979e5faa8b2da0a935eeb3.png)

而且，你可以在这里找到一个演示[，在这里](https://todo-graphql-server.herokuapp.com/graphql)找到[。](http://react-graphql.herokuapp.com/)

![graphql overview](img/d0164f10d468ef7031bc999d90d9a00f.png)

## 结论

正如你在本教程中看到的，GraphQL 和 GraphQL.js 是脸书在 2015 年作为一组开源项目发布的非常新的技术。核心思想是 UI 最清楚呈现一组特定的组件需要什么数据。如果您试图从 MVC Todo 列表中选择另一个 JS 框架，并面临任何问题，请随意删除一行。感谢阅读。

## 分享这篇文章