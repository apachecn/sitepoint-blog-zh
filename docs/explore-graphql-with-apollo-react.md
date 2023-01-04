# 探索 GraphQL 与阿波罗和反应:建立一个超级英雄数据库

> 原文：<https://www.sitepoint.com/explore-graphql-with-apollo-react/>

对围绕 GraphQL 的所有讨论感到好奇，但不确定为什么你应该感到兴奋？你来对地方了！我们将阐明 GraphQL 是什么，并为您提供一些实际操作的机会。

让我们先澄清一下，回答这个 20，000 美元的问题:GraphQL 是什么？不，这不是你 TI-89 上的一个模糊功能。它本质上是一种查询语言——或者更准确地说是查询规范——可以用来从任何数据源获取数据。

更好的是，它允许您在单个网络请求中获取您需要的准确数据——不多不少。虽然这听起来不像是脸书创新工厂里最性感的技术，但你可能会惊喜地发现它是如此有用。

所需要的只是一个充当我们端点的 [Apollo](https://www.apollographql.com/) 服务器和一个使用 Apollo 客户端的 React 应用程序来利用其中的任何数据。我们将首先处理服务器。

## Apollo Server 入门

为了启动我们的 Apollo 服务器，在您喜欢的工作目录中创建一个名为`apollo-server`的文件夹。接下来，进入该目录并运行下面的`npm`命令——您的[已经安装了 npm 和 Node】，对吗？—让阿波罗框架就位:](https://www.sitepoint.com/beginners-guide-node-package-manager/)

```
npm install apollo-server apollo-server-express graphql 
```

既然您已经准备好了 Apollo 服务器的各个部分，是时候告诉我们的服务器应该提供什么了。继续在`apollo-server`目录中创建一个空的`index.js`文件，并向其中添加以下内容:

```
const { ApolloServer, gql } = require('apollo-server'); 
```

这一行只是引入启动 Apollo 服务器和将查询字符串解析成 GraphQL 的查询文档所需的对象。

## 我们的第一个 GraphQL 模式

接下来，让我们添加第一个模式:

```
// This will be our GraphQL schema
const typeDefs = gql` type User {
    id: ID!
    name: String
    superpowers: [Superpower]!
  }

  type Superpower {
    id: ID!
    text: String
  }

  type Query {
    users: [User]
    user(id: ID!): User
  } `; 
```

这里我们添加我们的类型定义。第一个是类型`User`，我们将其定义为具有`id`、`name`和`superpowers`字段的对象。第二个是简单的`id`和`text`来描述每个超级大国。最后，第三个定义了两个可接受的查询——`users`和`user`——分别返回所有用户或与提供的`id`参数匹配的单个用户。

很简单，对吧？

## 添加少量数据

接下来，让我们添加一些模拟数据来使我们的模式变得生动:

```
// This will be our mock data to query
const users = [{
  id: '1',
  name: 'Peter Parker',
  superpowers: [{
    id: '1',
    text: 'Web slinging'
  },{
    id: '2',
    text: 'Spidey sense'
  }]
},{
  id: '2',
  name: 'Tony Stark',
  superpowers: [{
    id: '3',
    text: 'Industrial design'
  },{
    id: '4',
    text: 'Robotic fashion'
  }]
}]; 
```

我们在这里所做的只是向我们的模拟数据添加两个用户。值得指出的是，GraphQL 并不仅限于查询 JavaScript 数组。这可以是任何数据库或其他数据结构。我们只是保持事情简单，专注于手头的任务。

## 不要忘记解决方案

接下来，我们需要告诉 GraphQL 如何解释我们上面定义的查询。这是通过解析器完成的:

```
// This will be a map of functions to return the data described by our schema
const resolvers = {
  Query: {
    users: () => {
      return users
    },
    user: (root, { id }) => {
      return users.find(user => user.id === id);
    },
  },
}; 
```

您会注意到第一个查询`users`不需要任何参数，并返回整个用户列表(至少在理论上是这样，稍后会详细介绍)。第二个查询`user`接受要获取的用户的 ID，并返回所述用户。

## 把所有的放在一起

为了结束我们的 Apollo 服务器，我们只需要实例化一个新的实例并开始监听连接:

```
const server = new ApolloServer({
  typeDefs,
  resolvers,
});

server.listen().then(({ url }) => {
  console.log(`Apollo server started at ${url}`)
}); 
```

在这里，我们将上面创建的模式和解析器发送给`ApolloServer`构造函数。接下来需要做的就是实际启动服务器，但是首先，您的`index.js`应该是这样的:

```
const { ApolloServer, gql } = require('apollo-server');

// This will be our GraphQL schema
const typeDefs = gql` type User {
    id: ID!
    name: String
    superpowers: [Superpower]!
  }

  type Superpower {
    id: ID!
    text: String
  }

  type Query {
    users: [User]
    user(id: ID!): User
  } `;

// This will be our mock data to query
const users = [{
  id: '1',
  name: 'Peter Parker',
  superpowers: [{
    id: '1',
    text: 'Web slinging'
  },{
    id: '2',
    text: 'Spidey sense'
  }]
},{
  id: '2',
  name: 'Tony Stark',
  superpowers: [{
    id: '3',
    text: 'Industrial design'
  },{
    id: '4',
    text: 'Robotic fashion'
  }]
}];

// This will be a map of functions to return the data described by our schema
const resolvers = {
  Query: {
    users: () => {
      return users
    },
    user: (root, { id }) => {
      return users.find(user => user.id === id);
    },
  },
};

const server = new ApolloServer({
  typeDefs,
  resolvers,
});

server.listen().then(({ url }) => {
  console.log(`Apollo server started at ${url}`)
}); 
```

继续使用`node index.js`启动它，并访问`http://localhost:4000/`查看您的劳动成果！

迎接您的应该是一个 GraphQL 平台，它允许您针对自己创建的模式尝试交互式查询。

## 亲自动手

让我们通过在左侧窗格中输入以下查询来测试一下:

```
query {
  user(id: 1) {
    name
  }
} 
```

这里我们使用刚刚设置的`user`查询，并向它传递值为 1 的`id`。我们还告诉 GraphQL，我们只想返回该用户的`name`。单击播放按钮后，左侧窗格中的结果应该如下所示:

```
{
  "data": {
    "user": {
      "name": "Peter Parker"
    }
  }
} 
```

假设你也想看一眼他的超能力。您只需请求该字段:

```
query {
  user(id: 1) {
    name,
    superpowers {
      text
    }
  }
} 
```

我们添加了`superpowers`字段，因为我们只关心文本而不是超级大国 ID，所以我们指定了同样多的内容。现在，结果应该为我们的第一个用户显示每个超级用户:

```
{
  "data": {
    "user": {
      "name": "Peter Parker",
      "superpowers": [
        {
          "text": "Web slinging"
        },
        {
          "text": "Spidey sense"
        }
      ]
    }
  }
} 
```

假设我们想要获取所有用户及其超能力，我们可以依赖我们定义的`users`查询:

```
query {
  users {
    id,
    name,
    superpowers {
      text
    }
  }
} 
```

结果是:

```
{
  "data": {
    "users": [
      {
        "id": "1",
        "name": "Peter Parker",
        "superpowers": [
          {
            "text": "Web slinging"
          },
          {
            "text": "Spidey sense"
          }
        ]
      },
      {
        "id": "2",
        "name": "Tony Stark",
        "superpowers": [
          {
            "text": "Industrial design"
          },
          {
            "text": "Robotic fashion"
          }
        ]
      }
    ]
  }
} 
```

只在乎超能力？我们也可以这样做:

```
query {
  users {
    superpowers {
      text
    }
  }
} 
```

你会得到:

```
{
  "data": {
    "users": [
      {
        "superpowers": [
          {
            "text": "Web slinging"
          },
          {
            "text": "Spidey sense"
          }
        ]
      },
      {
        "superpowers": [
          {
            "text": "Industrial design"
          },
          {
            "text": "Robotic fashion"
          }
        ]
      }
    ]
  }
} 
```

至此，您应该能够体会到 GraphQL 的高度灵活性和魅力。通过一个查询和连接，我们可以检索我们想要的任何数据片段。所需要的只是一个设计良好的模式和支持它的解析器。

更好的是，后端开发人员和前端开发人员几乎可以独立完成他们的工作。有了充当中间人的模式，两个群体都可以有效地避免踩到对方的脚趾。实际上，这就是 GraphQL。然而，在我们结束本教程之前，让我们看看如何将这些查询与实际的 React 应用程序集成。

## 向混合物中引入 React

返回到您的根工作目录，运行以下命令，使用必需的 GraphQL 和 Apollo 库设置一个引导 React 应用程序:

```
npm install -g create-react-app
create-react-app my-graphql
cd my-graphql
npm install apollo-boost react-apollo graphql 
```

接下来，用以下内容替换`src/index.js`的内容:

**index.js:**

```
import React from 'react'
import ReactDOM from 'react-dom'
import { ApolloClient } from 'apollo-client'
import { HttpLink } from 'apollo-link-http'
import { InMemoryCache } from 'apollo-cache-inmemory'
import { ApolloProvider } from 'react-apollo'

import App from './App'

const client = new ApolloClient({
    link: new HttpLink({ uri: 'http://localhost:4000/graphql' }),
    cache: new InMemoryCache()
})

ReactDOM.render(
    <ApolloProvider client={client}>
    <App />
    </ApolloProvider>,
    document.getElementById('root')
) 
```

这里我们需要的是 React 应用程序的常规导入，以及与我们新的 Apollo 服务器接口的 Apollo 客户端。要创建 Apollo 客户端，我们只需要一个到服务器的链接和一个缓存商品的方法。这样，我们只需渲染应用程序。

接下来，我们需要设置应用程序来查询和显示 Apollo 服务器提供的数据。继续用下面的代码替换默认的`src/App.js`:

**App.js:**

```
import React from 'react'
import { Query } from 'react-apollo'
import { gql } from 'apollo-boost'

const TEST_QUERY = gql` {
    user(id: 1) {
      id,
      name,
      superpowers {
        text
      }

  }} `;

const App = () => (
  <Query query={TEST_QUERY}>
  {({ data: { user }, loading }) => {
      if (loading || !user) {
        return <div>Loading ...</div>;
      }
      return (
    <p>
        {user.name} ({user.id}) has the following superpowers:
        <ul>
        {user.superpowers.map(superpower => (
            <li>
                {superpower.text}
            </li>
        ))}
        </ul>
    </p>
      );
    }}
  </Query>
);

export default App 
```

你应该在这里看到一些熟悉的模式。我们首先使用`gql`创建一个 GraphQL 查询文档，请求 ID 为 1 的用户——更具体地说，请求他们的 ID、姓名和超级用户文本。然后我们将带有`Query`标签的文档传递给我们的服务器。在这里，我们可以在一个漂亮的 React 应用程序中格式化结果。

继续使用`npm start`启动服务器，并在`http://localhost:3000/`查看漂亮的结果。

至此，我们的教程就完成了。您已经创建了一个 Apollo 服务器，并为其提供了一些简单的数据结构。您了解了如何编写模式和解析器来定义与数据的交互。然后，您练习查询该数据的特定部分。最后，您将所有这些与一个 React 应用程序结合在一起，该应用程序从服务器检索数据，并以一种格式良好的方式呈现数据。

虽然这应该足以让您开始在未来的项目中集成 GraphQL，但是还有很多东西需要学习。添加和更改数据的突变是一个很好的下一步。

希望你喜欢这篇教程——快乐编码！

## 分享这篇文章