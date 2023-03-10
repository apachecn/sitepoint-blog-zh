# 用 Express、Angular 和 GraphQL 构建一个简单的 Web 应用程序

> 原文：<https://www.sitepoint.com/build-a-simple-web-app-with-express-angular-and-graphql/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/11/30/web-app-with-express-angular-graphql)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

在过去 10 年左右的时间里，用于 web 服务的 REST APIs 的概念已经成为大多数 web 开发人员的谋生之道。最近出现了一个新概念，GraphQL。GraphQL 是一种查询语言，由脸书发明，并于 2015 年向公众发布。在过去的三年里，它引起了不小的轰动。一些人认为这是创建 web APIs 的一种新的革命性方法。传统 REST 和 GraphQL 的主要区别在于查询发送到服务器的方式。在 REST APIs 中，每种类型的资源有不同的端点，对请求的响应由服务器决定。使用 GraphQL，您通常只有一个端点，客户端可以显式地声明应该返回哪些数据。GraphQL 中的单个请求可以包含对底层模型的多个查询。

在本教程中，我将向您展示如何开发一个简单的 GraphQL web 应用程序。服务器将使用 Node 和 Express 运行，客户端将基于 Angular 7。您将看到准备服务器来响应不同的查询是多么容易。与实现 REST 风格的 API 相比，这消除了许多工作。为了提供一个例子，我将创建一个用户可以浏览 ATP 网球运动员和排名的服务。

## 使用 GraphQL 构建您的 Express 服务器

我将从实现服务器开始。我假设您的系统上安装了**节点**，并且`npm`命令可用。我还将使用 SQLite 来存储数据。为了创建数据库表和导入数据，我将使用`sqlite3`命令行工具。如果您还没有安装`sqlite3`，请前往 [SQLite 下载页面](https://www.sqlite.org/download.html)，安装包含*命令行 shell* 的包。

首先，创建一个包含服务器代码的目录。我简单地称我的为`server/`。在目录中运行

```
npm init -y 
```

接下来，您将不得不使用基本服务器所需的所有包来初始化项目。

```
npm install --save express@4.16.4 cors@2.8.4 express-graphql@0.6.12 graphql@14.0.2 sqlite3@4.0.2 
```

### 将数据导入 Express 服务器

接下来，让我们创建数据库表并将一些数据导入其中。我将利用杰夫·萨克曼免费提供的 ATP 网球排名。在系统的某个目录下克隆 GitHub 库。

```
git clone https://github.com/JeffSackmann/tennis_atp.git 
```

在本教程中，我将只使用这个库中的两个文件，`atp_players.csv`和`atp_rankings_current.csv`。在你的`server/`目录下启动 SQLite。

```
sqlite3 tennis.db 
```

这将创建一个包含数据的文件`tennis.db`，并给出一个命令行提示，您可以在其中键入 SQL 命令。让我们创建我们的数据库表。在 SQLite3 shell 中粘贴并运行以下内容。

```
CREATE TABLE players(
  "id" INTEGER,
  "first_name" TEXT,
  "last_name" TEXT,
  "hand" TEXT,
  "birthday" INTEGER,
  "country" TEXT
);

CREATE TABLE rankings(
  "date" INTEGER,
  "rank" INTEGER,
  "player" INTEGER,
  "points" INTEGER
); 
```

SQLite 允许您将 CSV 数据快速导入到您的表中。只需在 SQLite3 shell 中运行以下命令。

```
.mode csv
.import {PATH_TO_TENNIS_DATA}/atp_players.csv players
.import {PATH_TO_TENNIS_DATA}/atp_rankings_current.csv rankings 
```

在上面的例子中，用下载网球数据存储库的路径替换`{PATH_TO_TENNIS_DATA}`。现在，您已经创建了一个数据库，其中包含所有 ATP 排名的网球运动员，以及今年所有现役运动员的排名。你已经准备好离开 SQLite3 了。

```
.quit 
```

### 实施 Express 服务器

现在让我们实现服务器。打开一个新文件`index.js`，这是服务器应用程序的主要入口点。从快速和 CORS 基础开始。

```
const express = require('express');
const cors = require('cors');

const app = express().use(cors()); 
```

现在导入 SQLite，打开`tennis.db`中的网球数据库。

```
const sqlite3 = require('sqlite3');
const db = new sqlite3.Database('tennis.db'); 
```

这创建了一个变量`db`,您可以在这个变量上发出 SQL 查询并获得结果。

现在您已经准备好深入 GraphQL 的魔力了。将以下代码添加到您的`index.js`文件中。

```
const graphqlHTTP = require('express-graphql');
const { buildSchema } = require('graphql');

const schema = buildSchema(` type Query {
    players(offset:Int = 0, limit:Int = 10): [Player]
    player(id:ID!): Player
    rankings(rank:Int!): [Ranking]
  }

  type Player {
    id: ID
    first_name: String
    last_name: String
    hand: String
    birthday: Int
    country: String
  }

  type Ranking {
    date: Int
    rank: Int
    player: Player
    points: Int
  } `); 
```

前两行导入`graphqlHTTP`和`buildSchema`。函数`graphqlHTTP`插入到 Express 中，能够理解和响应 GraphQL 请求。`buildSchema`用于从字符串创建 GraphQL 模式。让我们更详细地看一下模式定义。

两种类型`Player`和`Ranking`反映了数据库表的内容。这些将被用作 GraphQL 查询的返回类型。如果仔细观察，您会发现`Ranking`的定义包含一个类型为`Player`的`player`字段。此时，数据库只有一个引用`players`表中一行的`INTEGER`。GraphQL 数据结构应该用它引用的播放器替换这个整数。

`type Query`定义了允许客户端进行的查询。在这个例子中，有三个查询。`players`返回一个由`Player`结构组成的数组。该列表可以由一个`offset`和一个`limit`来限制。这将允许在玩家列表中翻页。`player`查询通过其`ID`返回单个玩家。`rankings`查询将返回给定玩家等级的`Ranking`对象数组。

为了让您的生活变得简单一点，创建一个实用函数，它发出一个 SQL 查询并返回一个在查询返回时解析的`Promise`。这很有帮助，因为`sqlite3`接口是基于回调的，但是 GraphQL 在承诺方面做得更好。在`index.js`中添加以下功能。

```
function query(sql, single) {
  return new Promise((resolve, reject) => {
    var callback = (err, result) => {
      if (err) {
        return reject(err);
      }
      resolve(result);
    };

    if (single) db.get(sql, callback);
    else db.all(sql, callback);
  });
} 
```

现在是时候实现支持 GraphQL 查询的数据库查询了。GraphQL 使用一种叫做`rootValue`的东西来定义对应于 GraphQL 查询的函数。

```
const root = {
  players: args => {
    return query(
      `SELECT * FROM players LIMIT ${args.offset}, ${args.limit}`,
      false
    );
  },
  player: args => {
    return query(`SELECT * FROM players WHERE id='${args.id}'`, true);
  },
  rankings: args => {
    return query(
      `SELECT r.date, r.rank, r.points,
              p.id, p.first_name, p.last_name, p.hand, p.birthday, p.country
      FROM players AS p
      LEFT JOIN rankings AS r
      ON p.id=r.player
      WHERE r.rank=${args.rank}`,
      false
    ).then(rows =>
      rows.map(result => {
        return {
          date: result.date,
          points: result.points,
          rank: result.rank,
          player: {
            id: result.id,
            first_name: result.first_name,
            last_name: result.last_name,
            hand: result.hand,
            birthday: result.birthday,
            country: result.country
          }
        };
      })
    );
  }
}; 
```

前两个查询非常简单。它们由简单的`SELECT`语句组成。结果被直接传回。`rankings`查询稍微复杂一点，因为需要一个`LEFT JOIN`语句来组合两个数据库表。之后，结果被转换成 GraphQL 查询的正确数据结构。请注意在所有这些查询中，`args`是如何包含从客户端传入的参数的。您不需要担心检查缺失值、分配默认值或检查正确的类型。这些都是由 GraphQL 服务器为您完成的。

剩下要做的就是创建一条路线并将`graphqlHTTP`函数链接到其中。

```
app.use(
  '/graphql',
  graphqlHTTP({
    schema,
    rootValue: root,
    graphiql: true
  })
);

app.listen(4201, err => {
  if (err) {
    return console.log(err);
  }
  return console.log('My Express App listening on port 4201');
}); 
```

`graphiql`为您提供了一个很好的用户界面，您可以在上面测试对服务器的查询。

要启动服务器运行，请执行以下操作:

```
node index.js 
```

然后打开浏览器并导航至`http://localhost:4201/graphql`。您将看到一个针对 GraphQL 查询的交互式测试平台。

![GraphQL rankings query](img/0af19a3d57c0c8f32afdc7b0626996a7.png)

## 添加您的 Angular 7 客户端

没有客户端的 web 应用是什么？在这一节中，我将带您完成一个使用 Angular 7 的单页面应用程序的实现。首先，创建一个新的角度应用。如果您尚未这样做，请在您的系统上安装最新版本的 angular 命令行工具。

```
npm install -g @angular/cli@7.1.0 
```

根据您的操作系统，您可能需要使用`sudo`来运行这个命令。现在您可以创建一个新的角度应用程序。在新目录运行中:

```
ng new AngularGraphQLClient 
```

这将创建一个新目录，并在其中安装一个 Angular 应用程序所需的所有包。将提示您两个问题。回答*是*在应用中包含路由。我将在本教程中使用的样式表将是简单的 CSS。

该应用程序将包含三个与主`app`模块相关联的组件。您可以通过导航到刚刚创建的目录并运行以下三个命令来生成它们。

```
ng generate component Home
ng generate component Players
ng generate component Ranking 
```

这将在`src/app`中创建三个目录，并为每个组件添加组件`.ts`代码文件、`.html`模板和`.css`样式表。为了在 Angular 中使用 GraphQL，我将使用 *Apollo* 库。在 angular 中设置阿波罗是一个简单的命令。

```
ng add apollo-angular 
```

该命令将安装许多节点模块。它还会在`/src/app/`文件夹的文件`graphql.module.ts`中创建一个角度模块，并将其导入到主`app`模块中。在这个文件中，您会看到这一行

```
const uri = ''; // <-- add the URL of the GraphQL server here 
```

把它改成

```
const uri = 'http://localhost:4201/graphql'; 
```

这指定了可以找到 GraphQL 服务的 URI。

**注意:**如果你想在安装完 Apollo Angular 之后生成任何组件，你就需要指定组件所属的模块。因此，生成上面的 *Home* 组件将变成

```
ng generate component Home --module app 
```

为了将值绑定到 HTML 中的输入元素，我将使用 Forms 模块。打开`src/app/app.module.ts`并添加

```
import { FormsModule } from '@angular/forms'; 
```

到文件的顶部。然后在`@NgModule`声明中将`FormsModule`添加到`imports`数组中。

### 在 Angular 中创建您的布局和布线

现在打开`src/index.html`。这个文件包含了你的 Angular 应用程序将要存在的 HTML 容器。您将需要一些外部 CSS 和 JavaScript 资源来美化您的应用程序的设计。在`<head>`标签中添加以下几行。这将包括一些最小的材料设计造型。

```
<link
  rel="stylesheet"
  href="https://fonts.googleapis.com/icon?family=Material+Icons"
/>
<link
  rel="stylesheet"
  href="https://code.getmdl.io/1.3.0/material.indigo-pink.min.css"
/>
<script defer src="https://code.getmdl.io/1.3.0/material.min.js"></script> 
```

接下来，打开`src/app.component.html`，将内容替换为以下内容。

```
<div class="mdl-layout mdl-js-layout mdl-layout--fixed-header">
  <div class="mdl-layout__header mdl-layout__header--waterfall">
    <div class="mdl-layout__header-row">
      <span class="mdl-layout-title" routerLink="/">
        <i class="material-icons">home</i> Angular with GraphQL
      </span>
      <!-- Add spacer, to align navigation to the right in desktop -->
      <div class="mdl-layout-spacer"></div>
      <!-- Navigation -->
      <ul class="mdl-navigation">
        <li class="mdl-navigation__link" routerLink="/">Home</li>
        <li class="mdl-navigation__link" routerLink="/players">Players</li>
        <li class="mdl-navigation__link" routerLink="/ranking">Rankings</li>
        <li
          class="mdl-navigation__link"
          *ngIf="!isAuthenticated"
          (click)="login()"
        >
          Login
        </li>
        <li
          class="mdl-navigation__link"
          *ngIf="isAuthenticated"
          (click)="logout()"
        >
          Logout
        </li>
      </ul>
    </div>
  </div>

  <div class="mdl-layout__drawer">
    <ul class="mdl-navigation">
      <li class="mdl-navigation__link" routerLink="/">Home</li>
      <li class="mdl-navigation__link" routerLink="/players">Players</li>
      <li class="mdl-navigation__link" routerLink="/ranking">Rankings</li>
      <li
        class="mdl-navigation__link"
        *ngIf="!isAuthenticated"
        (click)="login()"
      >
        Login
      </li>
      <li
        class="mdl-navigation__link"
        *ngIf="isAuthenticated"
        (click)="logout()"
      >
        Logout
      </li>
    </ul>
  </div>

  <div class="mdl-layout__content content"><router-outlet></router-outlet></div>
</div> 
```

这创建了一个基本的布局，带有一个顶栏和几个链接，将不同的组件加载到`router-outlet`中。为了加载应用程序可用的路线，您应该修改`app-routing.module.ts`。在顶部你会看到`routes`数组的声明。

```
const routes: Routes = []; 
```

将此行替换为以下内容。

```
import { PlayersComponent } from './players/players.component';
import { HomeComponent } from './home/home.component';
import { RankingComponent } from './ranking/ranking.component';

const routes: Routes = [
  {
    path: '',
    component: HomeComponent
  },
  {
    path: 'players',
    component: PlayersComponent
  },
  {
    path: 'ranking',
    component: RankingComponent
  }
]; 
```

现在，路由器知道在选择特定路由时将哪些组件放入出口。此时，您的应用程序已经显示了这三个页面，顶栏中的链接会将它们加载到应用程序的内容区域。

最后，让我们给页面一些样式。在`app.component.css`中粘贴以下内容。

```
.content {
  padding: 1rem;
  display: flex;
  justify-content: center;
} 
```

### 以角度添加组件

您已经准备好实现组件了。让我们从让用户浏览数据库中所有网球运动员的组件开始。将以下内容复制到文件`src/app/players/players.component.ts`中。接下来，我将向您介绍该文件每个部分的含义。

```
import { Component, OnInit } from '@angular/core';
import { Apollo, QueryRef } from 'apollo-angular';
import gql from 'graphql-tag';

const PLAYERS_QUERY = gql` query players($offset: Int) {
    players(offset: $offset, limit: 10) {
      id
      first_name
      last_name
      hand
      birthday
      country
    }
  } `;

@Component({
  selector: 'app-players',
  templateUrl: './players.component.html',
  styleUrls: ['./players.component.css']
})
export class PlayersComponent implements OnInit {
  page = 1;
  players: any[] = [];

  private query: QueryRef<any>;

  constructor(private apollo: Apollo) {}

  ngOnInit() {
    this.query = this.apollo.watchQuery({
      query: PLAYERS_QUERY,
      variables: { offset: 10 * this.page }
    });

    this.query.valueChanges.subscribe(result => {
      this.players = result.data && result.data.players;
    });
  }

  update() {
    this.query.refetch({ offset: 10 * this.page });
  }

  nextPage() {
    this.page++;
    this.update();
  }

  prevPage() {
    if (this.page > 0) this.page--;
    this.update();
  }
} 
```

该文件的前三行包含驱动组件所需的导入。

```
import { Component, OnInit } from '@angular/core';
import { Apollo, QueryRef } from 'apollo-angular';
import gql from 'graphql-tag'; 
```

除了核心的角度进口，这使得`apollo-angular`的`Apollo`和`QueryRef`以及`graphql-tag`的`gql`可用。后者直接用于创建 GraphQL 查询。

```
const PLAYERS_QUERY = gql` query players($offset: Int) {
    players(offset: $offset, limit: 10) {
      id
      first_name
      last_name
      hand
      birthday
      country
    }
  } `; 
```

`gql`标签获取模板字符串并将其转换成查询对象。这里定义的查询将要求服务器返回一个玩家数组，其中填充了玩家的所有字段。`limit`参数将导致服务器最多返回 10 条记录。可以将 offset 参数指定为查询的参数。这将允许通过播放器翻页。

```
@Component({
  selector: 'app-players',
  templateUrl: './players.component.html',
  styleUrls: ['./players.component.css']
})
export class PlayersComponent implements OnInit {
  page = 0;
  players: any[] = [];

  private query: QueryRef<any>;

  constructor(private apollo: Apollo) {}
} 
```

`PlayersComponent`的属性指定了组件的状态。属性`page`存储玩家列表中的当前页面。`players`将包含将在表格中显示的玩家数组。还有一个存储查询的`query`变量。每当用户导航到另一个页面时，都需要重新获取数据。构造函数将注入`apollo`属性，以便您可以访问 GraphQL 接口。

```
ngOnInit() {
  this.query = this.apollo
    .watchQuery({
      query: PLAYERS_QUERY,
      variables: {offset : 10*this.page}
    });

    this.query.valueChanges.subscribe(result => {
      this.players = result.data && result.data.players;
    });
  } 
```

在组件生命周期的初始化阶段，将调用`ngOnInit`方法。这是播放器组件启动数据加载的地方。这是通过`this.apollo.watchQuery`实现的。通过传递`PLAYERS_QUERY`和`offset`参数的值。您现在可以使用`valueChanges.subscribe`订阅任何数据更改。该方法接受一个回调，该回调将使用从服务器获得的数据设置`players`数组。

```
update() {
  this.query.refetch({offset : 10*this.page});
}

nextPage() {
  this.page++;
  this.update();
}

prevPage() {
  if (this.page>0) this.page--;
  this.update();
} 
```

为了四舍五入，`nextPage`和`prevPage`将增加或减少`page`属性。通过使用新参数调用`query`上的`refetch`，服务器请求被发出。当收到数据时，将自动调用订阅回调。

该组件附带的 HTML 模板存储在`players.component.html`中。将以下内容粘贴到其中。

```
<table
  class="mdl-data-table mdl-js-data-table mdl-data-table--selectable mdl-shadow--2dp"
>
  <tr>
    <th class="mdl-data-table__cell--non-numeric">First Name</th>
    <th class="mdl-data-table__cell--non-numeric">Last Name</th>
    <th class="mdl-data-table__cell--non-numeric">Hand</th>
    <th>Birthday</th>
    <th class="mdl-data-table__cell--non-numeric">Country</th>
  </tr>
  <tr *ngFor="let player of players">
    <td class="mdl-data-table__cell--non-numeric">{{player.first_name}}</td>
    <td class="mdl-data-table__cell--non-numeric">{{player.last_name}}</td>
    <td class="mdl-data-table__cell--non-numeric">{{player.hand}}</td>
    <td>{{player.birthday}}</td>
    <td class="mdl-data-table__cell--non-numeric">{{player.country}}</td>
  </tr>
</table>

<div class="paging">
  <button
    class="mdl-button mdl-js-button mdl-button--colored"
    (click)="prevPage()"
  >
    <i class="material-icons">arrow_back</i>
  </button>
  Page {{page+1}}
  <button
    class="mdl-button mdl-js-button mdl-button--colored"
    (click)="nextPage()"
  >
    <i class="material-icons">arrow_forward</i>
  </button>
</div> 
```

这将在表格中显示玩家列表。在表格下面，我添加了分页链接。

排名组件基本上遵循相同的模式。`src/app/ranking.component.ts`看起来是这样的。

```
import { Component, OnInit } from '@angular/core';
import { Apollo, QueryRef } from 'apollo-angular';
import gql from 'graphql-tag';

const RANKINGS_QUERY = gql` query rankings($rank: Int!) {
    rankings(rank: $rank) {
      date
      rank
      points
      player {
        first_name
        last_name
      }
    }
  } `;

@Component({
  selector: 'app-ranking',
  templateUrl: './ranking.component.html',
  styleUrls: ['./ranking.component.css']
})
export class RankingComponent implements OnInit {
  rank: number = 1;
  rankings: any[];
  private query: QueryRef<any>;

  constructor(private apollo: Apollo) {}

  ngOnInit() {
    this.query = this.apollo.watchQuery({
      query: RANKINGS_QUERY,
      variables: { rank: Math.round(this.rank) }
    });

    this.query.valueChanges.subscribe(result => {
      this.rankings = result.data && result.data.rankings;
    });
  }

  update() {
    return this.query.refetch({ rank: Math.round(this.rank) });
  }
} 
```

如你所见，大部分代码与`players.component.ts`中的非常相似。`RANKINGS_QUERY`的定义查询在一段时间内拥有特定排名的玩家。注意，该查询仅请求玩家的`first_name`和`last_name`。这意味着服务器不会发送任何客户端没有要求的额外玩家数据。

排名组件的模板包含一个文本字段和按钮，用户可以在其中输入排名并重新加载页面。下面是玩家列表。这是`ranking.component.html`的内容。

```
<h1>Rankings</h1>
<input class="mdl-textfield__input" type="text" id="rank" [(ngModel)]="rank" />

<button
  class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect"
  (click)="update()"
>
  Update
</button>
<table
  class="mdl-data-table mdl-js-data-table mdl-shadow--2dp"
  *ngIf="rankings"
>
  <tr>
    <th>Rank</th>
    <th>Date</th>
    <th>Points</th>
    <th class="mdl-data-table__cell--non-numeric">First Name</th>
    <th class="mdl-data-table__cell--non-numeric">Last Name</th>
  </tr>
  <tr *ngFor="let ranking of rankings">
    <td>{{ranking.rank}}</td>
    <td>{{ranking.date}}</td>
    <td>{{ranking.points}}</td>
    <td class="mdl-data-table__cell--non-numeric">
      {{ranking.player.first_name}}
    </td>
    <td class="mdl-data-table__cell--non-numeric">
      {{ranking.player.last_name}}
    </td>
  </tr>
</table> 
```

要启动客户端，请运行:

```
ng serve 
```

确保服务器也在运行，以便客户端可以成功请求数据。

![Angular rankings page](img/436a8178ffbf9057d01141c6f85b7c9a.png)

## 为您的 Express + Angular GraphQL 应用程序添加访问控制

每个 web 应用程序最重要的特性之一是用户认证和访问控制。在这一节中，我将指导您完成向 Angular 应用程序的服务器和客户端部分添加身份验证所需的步骤。这通常是编写应用程序最令人畏惧的部分。使用 Okta 极大地简化了这项任务，并使每个开发人员都可以使用安全认证。如果你还没有这样做，用 Okta 创建一个开发者帐户。访问 https://developer.okta.com/并选择**创建免费账户**。

![Okta signup page](img/1ab10e08e3bf70fe3ff2aa6656fae4df.png)

填好表格，自己注册。注册完成后，您可以看到您的开发者仪表板。

![Okta dashboard](img/ce4f9e54ce80b123ca86ddc81f83aca2.png)

从仪表盘的顶部菜单中，选择**应用**，然后点击绿色的**添加应用**按钮添加应用。

![Okta SPA wizard](img/af5b101e9a905c5581d39c664a295b00.png)

你会看到不同类型的应用程序可供选择。您正在注册一个**单页 App** 。在下一页，您将看到应用程序的设置。这里端口号预先填充为 8080。默认情况下，Angular 使用端口 4200。因此，您必须将端口号更改为 4200。

![GraphQL rankings query](img/91ffe72777cbb368e289ca0e6b237bf3.png)

完成后，您将获得一个 **ClientId** 。在您的客户机和服务器应用程序中都需要它。你还需要你的 Okta 开发者域名。这是您登录 Okta 开发者仪表板时在页面顶部看到的 URL。

### 保护您的 Angular 客户端

为了在 Angular 客户机上使用 Okta 认证，您必须安装`okta-angular`库。在客户端应用程序的基目录中，运行以下命令。

```
npm install @okta/okta-angular@1.0.7 apollo-link-context@1.0.10 --save 
```

现在打开`src/app/app.module.ts`。在文件的顶部添加 import 语句。

```
import { OktaAuthModule } from '@okta/okta-angular'; 
```

现在将该模块添加到`app`模块的`imports`列表中。

```
OktaAuthModule.initAuth({
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  redirectUri: 'http://localhost:4200/implicit/callback',
  clientId: '{yourClientId}'
}); 
```

当您导航到 Okta 仪表板时，您需要替换您在浏览器中看到的`yourOktaDomain`发展领域。另外，用您在注册应用程序时获得的客户机 ID 替换`yourClientId`。现在，您可以在整个应用程序中使用 Okta 身份验证了。接下来，您将实现从应用程序登录和注销。打开`app.component.ts`，从`okta-angular`进口`OktaAuthService`。将以下代码粘贴到文件中。

```
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';
import { OktaAuthService } from '@okta/okta-angular';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  public title = 'My Angular App';
  public isAuthenticated: boolean;

  constructor(public oktaAuth: OktaAuthService) {
    this.oktaAuth.$authenticationState.subscribe(
      (isAuthenticated: boolean) => (this.isAuthenticated = isAuthenticated)
    );
  }

  async ngOnInit() {
    this.isAuthenticated = await this.oktaAuth.isAuthenticated();
  }

  login() {
    this.oktaAuth.loginRedirect();
  }

  logout() {
    this.oktaAuth.logout('/');
  }
} 
```

`OktaAuthService`服务通过构造函数注入。然后它被用来设置`isAuthenticated`标志。`subscribe`方法订阅了一个回调函数，每当登录状态改变时就会触发该函数。`isAuthenticated`在`ngOnInit`阶段初始化，以反映首次加载应用程序时的登录状态。`login`和`logout`处理登录和退出的过程。为了使认证有效，`okta-angular`使用了一个叫做`implicit/callback`的特殊路径。在文件`app-routing.module.ts`中添加以下导入。

```
import { OktaCallbackComponent, OktaAuthGuard } from '@okta/okta-angular'; 
```

通过将以下内容添加到`routes`数组中，`implicit/callback`路线现在链接到了`OktaCallbackComponent`。

```
{
  path: 'implicit/callback',
  component: OktaCallbackComponent
} 
```

这就是登录和注销所需的全部内容。但是应用程序还没有受到保护。对于您想要访问控制的任何路由，您都必须添加一个授权保护。幸运的是这很容易。在您想要保护的每个路由中添加`canActivate`属性。将以下内容添加到`players`和`ranking`路线。

```
canActivate: [OktaAuthGuard]; 
```

这就是全部了。现在，当用户试图访问球员视图时，他将被重定向到 Okta 登录页面。登录后，用户将被重定向回产品视图。

您已经保护了客户端页面，但是在您继续保护后端之前，让我们花点时间考虑一下服务器将如何对用户进行身份验证。Okta 使用一个不记名令牌来标识用户。每个请求都必须将无记名令牌发送到服务器。为此，客户端必须确保将承载令牌添加到 HTTP 报头中。您所需要做的就是在`graphql.module.ts`中添加几行代码。在文件的顶部导入以下内容。

```
import { OktaAuthService } from '@okta/okta-angular';
import { setContext } from 'apollo-link-context'; 
```

然后修改`createApollo`函数添加承载令牌。

```
export function createApollo(httpLink: HttpLink, oktaAuth: OktaAuthService) {
  const http = httpLink.create({ uri });

  const auth = setContext((_, { headers }) => {
    return oktaAuth.getAccessToken().then(token => {
      return token ? { headers: { Authorization: `Bearer ${token}` } } : {};
    });
  });

  return {
    link: auth.concat(http),
    cache: new InMemoryCache()
  };
} 
```

### 保护您的 Express GraphQL 服务器

通过向服务器应用程序添加快速中间件功能来保护服务器。为此，您将需要一些额外的库。切换到您的服务器目录并运行命令

```
npm install @okta/jwt-verifier@0.0.13 body-parser@1.18.3 express-bearer-token@2.2.0 
```

接下来，让我们在服务器根文件夹中名为`auth.js`的单独文件中创建该函数。

```
const OktaJwtVerifier = require('@okta/jwt-verifier');

const oktaJwtVerifier = new OktaJwtVerifier({
  clientId: '{yourClientId}',
  issuer: 'https://{yourOktaDomain}/oauth2/default'
});

module.exports = async function oktaAuth(req, res, next) {
  try {
    const token = req.token;
    if (!token) {
      return res.status(401).send('Not Authorized');
    }
    const jwt = await oktaJwtVerifier.verifyAccessToken(token);
    req.user = {
      uid: jwt.claims.uid,
      email: jwt.claims.sub
    };
    next();
  } catch (err) {
    return res.status(401).send(err.message);
  }
}; 
```

同样，您必须用开发域和客户端 id 替换`yourOktaDomain`和`yourClientId`。这个函数的目的很简单。它检查请求中是否存在令牌字段。如果存在，`oktaJwtVerifier`检查令牌的有效性。如果一切正常，调用`next()`表示成功。否则，返回一个`401`错误。您现在所要做的就是确保应用程序中使用了该函数。将以下 require 语句添加到`index.js`文件中。

```
const bodyParser = require('body-parser');
const bearerToken = require('express-bearer-token');
const oktaAuth = require('./auth'); 
```

然后用下面的方法修改`app`的声明。

```
const app = express()
  .use(cors())
  .use(bodyParser.json())
  .use(bearerToken())
  .use(oktaAuth); 
```

`bearerToken`中间件将寻找一个承载令牌，并将其添加到请求中，以便`oktaAuth`找到它。通过这个简单的添加，您的服务器将只允许提供有效身份验证的请求。

## 了解有关 Express、Angular 和 GraphQL 的更多信息

在这个简单的教程中，我向您展示了如何使用 GraphQL 创建一个带有 Angular 的单页面应用程序。使用 Okta 服务以最小的努力实现了用户认证。

我还没有谈到如何使用 GraphQL 来添加或修改数据库中的数据。在 GraphQL 语言中，这被称为*突变*。要了解更多关于使用 Apollo 的突变，请查看[手册页](https://www.apollographql.com/docs/angular/basics/mutations.html)。

这个项目的完整代码可以在[https://github . com/okta developer/okta-graph QL-angular-example](https://github.com/oktadeveloper/okta-graphql-angular-example)找到。

如果您有兴趣了解有关 Express、Angular、GraphQL 或安全用户管理的更多信息，我建议您查看以下资源:

*   [用 Express 和 GraphQL 构建一个简单的 API 服务](https://developer.okta.com/blog/2018/09/27/build-a-simple-api-service-with-express-and-graphql)
*   [用 Spring Boot 和 GraphQL 构建一个安全的 API](https://developer.okta.com/blog/2018/08/16/secure-api-spring-boot-graphql)
*   [构建并理解 Express 中间件](https://developer.okta.com/blog/2018/09/13/build-and-understand-express-middleware-through-examples)
*   [Angular 6:有什么新功能，为什么要升级？](https://developer.okta.com/blog/2018/05/09/upgrade-to-angular-6)
*   [用 Angular 和 Node](https://developer.okta.com/blog/2018/10/30/basic-crud-angular-and-node) 构建一个基本的 CRUD App

喜欢你今天学到的吗？我们希望您在 Twitter 和[上关注我们的](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)[并订阅我们的 YouTube 频道](https://twitter.com/oktadev)！

## 分享这篇文章