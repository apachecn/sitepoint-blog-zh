# 如何构建 Node.js MVC 应用程序

> 原文：<https://www.sitepoint.com/node-js-mvc-application/>

在一个重要的应用程序中，架构和代码本身的质量一样重要。我们可以有写得很好的代码片段，但是如果我们没有良好的组织，随着复杂性的增加，我们会有一段艰难的时间。没有必要等到项目完成一半才开始考虑架构；最好的时间是在开始之前，用我们的目标作为我们选择的灯塔。

例如，Node.js 不像 Ruby 拥有 Rails 框架那样拥有一个对架构和代码组织有强烈意见的事实上的框架。因此，开始用 Node 构建完整的 web 应用程序可能会很困难。

在本教程中，我们将使用 MVC 架构构建笔记应用程序的基本功能。为了实现这一点，我们将为 [Node.js](https://nodejs.org/en/) 和 [SQLite](http://www.sqlite.org/) 采用 [Hapi.js](https://hapi.dev/) 框架作为数据库，使用 [Sequelize.js](https://sequelize.org/v3/) ，加上其他小工具，来加速我们的开发。我们将使用模板语言 [Pug](https://pugjs.org/api/getting-started.html) 来构建视图。

## 什么是 MVC？

模型-视图-控制器(或 MVC)可能是应用程序最流行的架构之一。正如计算机历史上许多其他很酷的事情一样，MVC 模式是在 PARC T2 为 Smalltalk 语言构思的，作为用图形用户界面组织应用程序问题的解决方案。它是为桌面应用程序而创建的，但从那以后，这个想法已经被应用到包括网络在内的其他媒体上。

我们可以用简单的术语来描述 MVC 架构:

*   **Model** :应用程序中处理数据库或任何数据相关功能的部分。
*   **视图**:用户将会看到的所有内容——基本上就是我们将要发送给客户端的页面。
*   控制器:我们站点的逻辑，以及模型和视图之间的粘合剂。在这里，我们调用我们的模型来获取数据，然后我们将这些数据放在视图上发送给用户。

我们的应用程序将允许我们创建、查看、编辑和删除纯文本笔记。它不会有其他功能，但是因为我们已经定义了一个坚实的架构，所以以后添加东西不会有太多麻烦。

本教程假设您的计算机上安装了最新版本的 Node。如果不是这样，请参考我们的[教程，学习如何使用节点](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)运行。

您可以在 GitHub 资源库的[中查看最终的应用程序，这样您就可以对应用程序的结构有一个大致的了解。](https://github.com/sitepoint-editors/notes-board)

## 奠定基础

构建任何 Node.js 应用程序的第一步是创建一个`package.json`文件，它将包含我们所有的依赖项和脚本。npm 可以使用`init`命令为我们完成这项工作，而不是手动创建这个文件:

```
mkdir notes-board
cd notes-board
npm init -y 
```

这个过程完成后，我们将有一个`package.json`文件可以使用。

*注意:如果你不熟悉这些命令，请查看我们的[NPM](https://www.sitepoint.com/beginners-guide-node-package-manager/)初学者指南。*

我们将继续安装 hapi . js——本教程选择的框架。它在简单性、稳定性和适合我们用例的特性之间提供了一个很好的平衡(尽管还有其他选项也很好)。

```
npm install @hapi/hapi@18.4.0 
```

该命令将下载 Hapi.js 并将其作为依赖项添加到我们的`package.json`文件中。

*注意:我们已经指定了 Hapi.js 的 v18.4.0，因为它与节点版本 8、10 和 12 兼容。如果你使用的是 Node 12，你可以选择安装最新版本(哈比神 v19.1.0)。*

现在我们可以创建我们的入口文件—启动一切的 web 服务器。继续在您的应用程序目录中创建一个`server.js`文件，并向其中添加以下代码:

```
"use strict";

const Hapi = require("@hapi/hapi");
const Settings = require("./settings");

const init = async () => {
  const server = new Hapi.Server({ port: Settings.port });

  server.route({
    method: "GET",
    path: "/",
    handler: (request, h) => {
      return "Hello, world!";
    }
  });

  await server.start();
  console.log(`Server running at: ${server.info.uri}`);
};

process.on("unhandledRejection", err => {
  console.log(err);
  process.exit(1);
});

init(); 
```

这将是我们应用程序的基础。

首先，我们指出我们将使用[严格模式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)，这是使用 Hapi.js 框架时的[惯例](https://hapi.dev/policies/styleguide/#strict-mode)。

接下来，我们包含我们的依赖项并实例化一个新的服务器对象，其中我们将连接端口设置为`3000`(端口可以是大于 1023 小于 65535 的任意数字[)。](http://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xml)

我们的服务器的第一个路由将作为一个测试，看看是否一切正常，所以“你好，世界！”消息对我们来说已经足够了。在每个路由中，我们必须定义它将响应的 HTTP 方法和路径(URL ),以及一个处理程序，它是一个处理 HTTP 请求的函数。处理函数可以接受两个参数:`request`和`h`。第一个包含关于 HTTP 调用的信息，第二个将为我们提供处理对该调用的响应的方法。

最后，我们用`server.start()`方法启动我们的服务器。

## 存储我们的设置

将我们的配置变量存储在一个专用文件中是一个很好的做法。这个文件导出一个包含我们的数据的 JSON 对象，其中每个键都是从一个环境变量中分配的——但是没有忘记一个后备值。

在这个文件中，我们还可以根据我们的环境(比如开发或生产)进行不同的设置。例如，出于开发目的，我们可以有一个内存中的 SQLite 实例，但在生产中却有一个真正的 SQLite 数据库文件。

根据当前环境选择设置非常简单。由于我们的文件中也有一个包含`development`或`production`的`env`变量，我们可以像下面这样做来获取数据库设置:

```
const dbSettings = Settings[Settings.env].db; 
```

所以当`env`变量为`development`时`dbSettings`将包含内存数据库的设置，或者当`env`变量为`production`时`dbSettings`将包含数据库文件的路径。

此外，我们可以添加对一个`.env`文件的支持，在这里我们可以本地存储我们的环境变量以用于开发目的。这是通过使用一个像 Node.js 的 [dotenv](https://www.npmjs.com/package/dotenv) 这样的包来完成的，它将从我们项目的根目录读取一个`.env`文件，并自动将找到的值添加到环境中。

*注意:如果你决定也使用一个`.env`文件，确保你安装了带有`npm install dotenv`的包，并把它添加到`.gitignore`，这样你就不会发布任何敏感信息。*

我们的`settings.js`文件将如下所示:

```
// This will load our .env file and add the values to process.env,
// IMPORTANT: Omit this line if you don't want to use this functionality
require("dotenv").config({ silent: true });

module.exports = {
  port: process.env.PORT || 3000,
  env: process.env.NODE_ENV || "development",

  // Environment-dependent settings
  development: {
    db: {
      dialect: "sqlite",
      storage: ":memory:"
    }
  },
  production: {
    db: {
      dialect: "sqlite",
      storage: "db/database.sqlite"
    }
  }
}; 
```

现在，我们可以通过执行以下命令并在 web 浏览器中导航到 [http://localhost:3000](http://localhost:3000) 来启动我们的应用程序:

```
node server.js 
```

*注意:这个项目是在节点 v12.15.0 上测试的。如果你得到任何错误，确保你有一个更新的安装。*

## 定义路线

路由的定义为我们提供了应用程序所支持的功能的概述。要创建我们的附加路由，我们只需复制我们已经在`server.js`文件中的路由结构，改变每个路由的内容。

让我们首先在项目中创建一个名为`lib`的新目录。这里我们将包括所有的 JS 组件。

在`lib`中，我们创建一个`routes.js`文件，并添加以下内容:

```
"use strict";
const Path = require("path");

module.exports = [
  // we’re going to define our routes here
]; 
```

在这个文件中，我们将导出一个包含应用程序每条路线的对象数组。要定义第一条路线，请将以下对象添加到数组中:

```
{
  method: "GET",
  path: "/",
  handler: (request, h) => {
    return "All the notes will appear here";
  },
  config: {
    description: "Gets all the notes available"
  }
}, 
```

我们的第一个路由是针对主页的(`/`)，因为它只会返回信息，所以我们给它分配了一个`GET`方法。现在，它只会给我们“所有的音符都将出现在这里”的信息，我们将在以后为控制器功能更改这一信息。`config`部分的`description`字段仅用于记录目的。

然后，我们在`/note/`路径下为我们的笔记创建四条路线。由于我们正在构建一个 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 应用程序，我们需要为每个动作提供一个路由和相应的 [HTTP 方法](http://www.w3schools.com/tags/ref_httpmethods.asp)。

在前一条路线旁边添加以下定义:

```
{
  method: "POST",
  path: "/note",
  handler: (request, h) => {
    return "New note";
  },
  config: {
    description: "Adds a new note"
  }
},
{
  method: "GET",
  path: "/note/{slug}",
  handler: (request, h) => {
    return "This is a note";
  },
  config: {
    description: "Gets the content of a note"
  }
},
{
  method: "PUT",
  path: "/note/{slug}",
  handler: (request, h) => {
    return "Edit a note";
  },
  config: {
    description: "Updates the selected note"
  }
},
{
  method: "GET",
  path: "/note/{slug}/delete",
  handler: (request, h) => {
    return "This note no longer exists";
  },
  config: {
    description: "Deletes the selected note"
  }
} 
```

我们在前面的路由定义中做了同样的事情，但是这一次我们改变了方法以匹配我们想要执行的动作。

唯一的例外是删除路线。在这种情况下，我们将使用`GET`方法而不是`DELETE`来定义它，并在路径中添加一个额外的`/delete`。这样，我们只需访问相应的 URL 就可以调用删除操作。

*注意:如果你计划实现一个严格的 REST 接口，那么你必须使用`DELETE`方法并删除路径的`/delete`部分。*

我们可以通过用花括号将单词括起来来命名路径中的参数。因为我们将通过一个 slug 来识别音符，所以我们将`{slug}`添加到每个路径中，除了`POST`路径；我们不需要它，因为我们不会与特定的音符交互，而是创建一个音符。

你可以在[官方文档](https://hapi.dev/tutorials/routing/?lang=en_US)上阅读更多关于 Hapi.js 路线的内容。

现在，我们必须将我们的新路线添加到`server.js`文件中。让我们导入文件顶部的 routes 文件:

```
const Routes = require("./lib/routes"); 
```

那么，让我们用以下内容替换我们当前的测试路线:

```
server.route(Routes); 
```

## 构建模型

模型允许我们定义数据的结构以及处理数据的所有功能。

在这个例子中，我们将使用带有 [Sequelize.js](https://sequelize.org/v3/) 的 [SQLite](https://www.sqlite.org/index.html) 数据库，它将使用 ORM ( [对象关系映射](https://en.wikipedia.org/wiki/Object-relational_mapping))技术为我们提供一个更好的界面。它还将为我们提供一个独立于数据库的接口。

### 设置数据库

您可以通过执行以下命令来安装 SQLite 和 Sequelize:

```
npm install sequelize sqlite3 
```

现在用一个名为`index.js`的文件在`lib/`中创建一个`models`目录，该目录将包含数据库和 Sequelize.js 设置，并包含以下内容:

```
"use strict";

const Fs = require("fs");
const Path = require("path");
const Sequelize = require("sequelize");
const Settings = require("../../settings");
const dbSettings = Settings[Settings.env].db;

const sequelize = new Sequelize(
  dbSettings.database,
  dbSettings.user,
  dbSettings.password,
  dbSettings
);
const db = {};

Fs.readdirSync(__dirname)
  .filter(file => file.indexOf(".") !== 0 && file !== "index.js")
  .forEach(file => {
    const model = sequelize.import(Path.join(__dirname, file));
    db[model.name] = model;
  });

db.sequelize = sequelize;
db.Sequelize = Sequelize;

module.exports = db; 
```

首先，我们包括将要使用的模块:

*   `Fs`，读取`models`文件夹中的文件，该文件夹将包含所有的模型
*   `Path`，加入当前目录下每个文件的路径
*   `Sequelize`，这将允许我们创建一个新的序列化实例
*   `Settings`，它包含来自我们项目根目录的`settings.js`文件的数据

接下来，我们创建一个新的`sequelize`变量，它将包含一个带有当前环境的数据库设置的`Sequelize`实例。我们将使用`sequelize`来导入所有的模型，并使它们在我们的`db`对象中可用。

`db`对象将被导出，并将包含每个模型的数据库方法。当我们需要对数据做一些事情时，它将在我们的应用程序中可用。

为了加载所有的模型，我们在`models`目录中寻找所有的文件(除了`index.js`文件)并使用`import`函数加载它们，而不是手动定义它们。返回的对象将为我们提供 CRUD 方法，然后我们将它们添加到`db`对象中。

最后，我们添加`sequelize`和`Sequelize`作为我们的`db`对象的一部分。第一个将在我们的`server.js`文件中使用，以在启动服务器之前连接到数据库，如果您在其他文件中也需要它，为了方便起见，包含第二个。

### 创建我们的笔记模型

在本节中，我们将使用 [Moment.js](http://momentjs.com/) 包来帮助进行日期格式化。您可以使用以下命令安装它并将其作为依赖项包括在内:

```
npm install moment 
```

我们将在`models`目录中创建一个`note.js`文件，这将是我们应用程序中的唯一模型。它将为我们提供所需的所有功能。

将以下内容添加到该文件中:

```
"use strict";

const Moment = require("moment");

module.exports = (sequelize, DataTypes) => {
  const Note = sequelize.define("Note", {
    date: {
      type: DataTypes.DATE,
      get: function() {
        return Moment(this.getDataValue("date")).format("MMMM Do, YYYY");
      }
    },
    title: DataTypes.STRING,
    slug: DataTypes.STRING,
    description: DataTypes.STRING,
    content: DataTypes.STRING
  });

  return Note;
}; 
```

我们导出一个函数，该函数接受一个`sequelize`实例来定义模型，以及一个包含数据库中所有可用类型的`DataTypes`对象。

接下来，我们使用一个对象定义数据的结构，其中每个键对应一个数据库列，键值定义了我们要存储的数据类型。您可以在 [Sequelize.js 文档](http://docs.sequelizejs.com/en/latest/api/datatypes/)中看到数据类型列表。数据库中的表将根据这些信息自动创建。

对于日期列，我们还定义了 Sequelize 应该如何使用 [getter](https://sequelize.org/master/manual/getters-setters-virtuals.html) 函数(`get`键)返回值。我们在返回信息之前表明了这一点。它应该首先通过 Moment 实用程序以更可读的方式进行格式化(`MMMM Do, YYYY`)。

*注意:虽然我们得到了一个简单易读的日期字符串，但它是作为 JavaScript 的 [Date](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象的精确日期字符串产品存储的。所以这不是一个破坏性的操作。*

最后，我们返回我们的模型。

### 同步数据库

我们现在必须同步数据库，然后才能在应用程序中使用它。在`server.js`中，导入文件顶部的模型:

```
// Import the index.js file inside the models directory
const Models = require("./lib/models/"); 
```

接下来，删除以下代码块:

```
await server.start();
console.log(`Server running at: ${server.info.uri}`); 
```

用这个替换它:

```
await Models.sequelize.sync();

await server.start();
console.log(`Server running at: ${server.info.uri}`); 
```

这段代码将使模型与我们的数据库同步。一旦完成，服务器将被启动。

## 构建控制器

控制器是接受来自 Hapi.js 的[请求](https://hapi.dev/api#request-properties)和[响应工具包](https://hapi.dev/api/#response-toolkit)对象的函数。`request`对象包含关于所请求资源的信息，我们使用`reply`将信息返回给客户端。

在我们的应用程序中，我们现在只返回一个 JSON 对象，但是一旦构建好视图，我们就会添加它们。

我们可以认为控制器是将我们的模型与我们的视图连接起来的函数；他们将与我们的模型通信以获取数据，然后将数据返回到视图中。

### 家庭控制器

我们要构建的第一个控制器将处理我们站点的主页。在`lib/controllers`目录下创建一个`home.js`文件，内容如下:

```
"use strict";

const Models = require("../models/");

module.exports = async (request, h) => {
  const result = await Models.Note.findAll({
    order: [["date", "DESC"]]
  });

  return {
    data: {
      notes: result
    },
    page: "Home — Notes Board",
    description: "Welcome to my Notes Board"
  };
}; 
```

首先，我们使用模型的 [`findAll`](https://sequelize.org/master/manual/model-querying-basics.html) 方法获取数据库中的所有音符。这个函数将返回一个承诺，如果它成功了，我们将得到一个包含数据库中所有注释的数组。

我们可以使用传递给`findAll`方法的 options 对象中的`order`参数，按降序排列结果，因此最后一项将首先出现。你可以在 [Sequelize.js 文档](https://sequelize.org/master/class/lib/model.js~Model.html#static-method-findAll)中查看所有可用选项。

一旦我们有了家庭控制器，我们就可以编辑我们的`routes.js`文件。首先，我们导入文件顶部的模块，紧挨着`Path`模块导入:

```
const Home = require("./controllers/home"); 
```

然后，我们将刚刚制作的控制器添加到阵列中:

```
{
  method: "GET",
  path: "/",
  handler: Home,
  config: {
    description: "Gets all the notes available"
  }
}, 
```

您可以通过重启服务器(`node server.js`)并访问 [http://localhost:3000/](http://localhost:3000/) 来检查此时的工作情况。您应该会看到以下响应:

```
{
  "data": { "notes": [] },
  "page":"Home — Notes Board",
  "description":"Welcome to my Notes Board"
} 
```

### 音符控制器的样板文件

因为我们要用一个 slug 来标识我们的笔记，我们可以使用笔记的标题和 [slug](https://www.npmjs.com/package/slug) 库来生成一个，所以让我们安装它，并用下面的命令将它作为一个依赖项包含进来:

```
npm install slug 
```

我们必须在应用程序中定义的最后一个控制器将允许我们创建、读取、更新和删除注释。

我们可以继续在`lib/controllers`目录中创建一个`note.js`文件，并添加以下内容:

```
"use strict";

const { Note } = require("../models/");
const Slugify = require("slug");
const Path = require("path");

module.exports = {
  // Here we’re going to include our functions that will handle the remaining requests in the routes.js file.
}; 
```

### `create`功能

为了给我们的数据库添加一个注释，我们将编写一个`create`函数，该函数将使用包含在有效载荷对象中的数据包装模型上的`create`方法。

在我们要导出的对象中添加以下内容:

```
create: async (request, h) => {
  const result = await Note.create({
    date: new Date(),
    title: request.payload.noteTitle,
    slug: Slugify(request.payload.noteTitle, { lower: true }),
    description: request.payload.noteDescription,
    content: request.payload.noteContent
  });

  // Generate a new note with the 'result' data
  return result;
}, 
```

一旦创建了注释，我们将获取注释数据，并使用`reply`函数将其作为 JSON 发送给客户机。

现在，我们只返回结果，但是一旦我们在下一节构建了视图，我们将能够生成带有新注释的 HTML，并在客户机上动态地添加它。尽管这不是完全必要的，并且将取决于您将如何处理您的前端逻辑，但是我们将返回一个 HTML 块来简化客户端的逻辑。

另外，请注意，当我们使用`new Date()`执行函数时，日期是动态生成的。

### `read`功能

为了只搜索一个元素，我们在模型上使用了`findOne`方法。因为我们通过 slug 来识别笔记，所以`where`过滤器必须包含客户端在 URL ( `http://localhost:3000/note/:slug:`)中提供的 slug:

```
read: async (request, h) => {
  const note = await Note.findOne({
    where: {
      slug: request.params.slug
    }
  });

  return note;
}, 
```

和前面的函数一样，我们只返回结果，这个结果将是一个包含音符信息的对象。一旦我们在[构建视图](#buildingtheviews)部分构建了视图，就会用到它们。

### `update`功能

为了更新注释，我们在模型上使用了`update`方法。它接受两个对象——我们要替换的新值，以及包含带有注释块的`where`过滤器的选项，注释块是我们要更新的注释:

```
update: async (request, h) => {
  const values = {
    title: request.payload.noteTitle,
    description: request.payload.noteDescription,
    content: request.payload.noteContent
  };

  const options = {
    where: {
      slug: request.params.slug
    }
  };

  await Note.update(values, options);
  const result = await Note.findOne(options);

  return result;
}, 
```

在更新我们的数据之后，由于我们的数据库不会返回更新的注释，我们可以再次找到修改过的注释以将其返回给客户端，这样我们就可以在修改完成后立即显示更新后的版本。

### `delete`功能

删除控制器将通过向模型的`destroy`函数提供 slug 来删除注释。然后，一旦注释被删除，我们重定向到主页。为此，我们使用哈比神响应工具包的[重定向](https://hapi.dev/api/?v=18.4.0#-hredirecturi)功能:

```
delete: async (request, h) => {
  await Note.destroy({
    where: {
      slug: request.params.slug
    }
  });

  return h.redirect("/");
} 
```

### 在我们的路线中使用音符控制器

此时，我们应该已经准备好了包含所有 CRUD 操作的 note 控制器文件。但是要使用它们，我们必须将它包含在我们的 routes 文件中。

首先，让我们在`routes.js`文件的顶部导入控制器:

```
const Note = require("./controllers/note"); 
```

我们必须用新函数替换每个处理程序，所以我们应该有如下的 routes 文件:

```
{
  method: "POST",
  path: "/note",
  handler: Note.create,
  config: {
    description: "Adds a new note",
    payload: {
      multipart: true,
    }
  }
},
{
  method: "GET",
  path: "/note/{slug}",
  handler: Note.read,
  config: {
    description: "Gets the content of a note"
  }
},
{
  method: "PUT",
  path: "/note/{slug}",
  handler: Note.update,
  config: {
    description: "Updates the selected note",
    payload: {
      multipart: true,
    }
  }
},
{
  method: "GET",
  path: "/note/{slug}/delete",
  handler: Note.delete,
  config: {
    description: "Deletes the selected note"
  }
} 
```

*注意:我们在末尾包含了没有`()`的函数，因为我们引用了我们的函数而没有调用它们。*

在哈比神 v19 中， [`request.payload.multipart`默认改为`false`](https://github.com/hapijs/hapi/issues/4017)。对于`POST`和`PUT`路线，我们需要将其设置回`true`，因为我们将使用`FormData`对象向服务器传输数据，并且传输的数据将是`multipart/form-data`格式。

## 构建视图

此时，我们的站点正在接收 HTTP 调用，并用 JSON 对象进行响应。为了让它对每个人都有用，我们必须创建页面，以一种良好的方式呈现我们的信息。

在这个例子中，我们将使用 [Pug](https://pugjs.org/api/getting-started.html) (以前的 Jade)模板语言，尽管这不是强制性的，我们可以在 Hapi.js 中使用其他语言。我们将使用 [Vision](https://github.com/hapijs/vision) 插件在我们的服务器中启用视图功能。

*注:如果你不熟悉杰德/哈巴狗，请看我们的[哈巴狗](https://www.sitepoint.com/a-beginners-guide-to-pug/)初学者指南。*

您可以使用以下命令安装软件包:

```
npm install @hapi/vision@5.5.4 pug 
```

在这里，我们正在安装版本 5.5.4 的视觉插件，它与哈比神版本 18 兼容。如果你选择安装哈比神 v19，你可以简单地输入`npm i @hapi/vision`来获得最新版本。

### 笔记组件

首先，我们将构建将在我们的视图中重用的注释组件。此外，我们将在一些控制器函数中使用该组件，在后端动态构建一个注释，以简化客户端的逻辑。

在`lib/views/components`中创建一个名为`note.pug`的文件，内容如下:

```
article.content
  h2.title: a(href=`/note/${note.slug}`)= note.title
  p.subtitle.is-6 Published on #{note.date}
  p=note.content 
```

它由笔记的标题、出版日期和笔记的内容组成。

### 基本布局

基本布局包含页面的公共元素——换句话说，对于我们的例子来说，就是所有不包含内容的东西。在`lib/views/`中创建一个名为`layout.pug`的文件，内容如下:

```
doctype html
head
  meta(charset='utf-8')
  meta(name='viewport' content='width=device-width, initial-scale=1')
  title=page
  meta(name='description' content=description)
  link(rel='stylesheet' href='https://cdn.jsdelivr.net/npm/bulma@0.8.0/css/bulma.min.css')
  script(defer='' src='https://use.fontawesome.com/releases/v5.3.1/js/all.js')
body
  block content
  script(src='/scripts/main.js') 
```

其他页面的内容将被加载到`block content`的位置。另外，请注意，我们将在`title`元素中显示一个页面变量，在`meta(name='description')`元素中显示一个`description`变量。我们稍后将在我们的路线中创建这些变量。

出于样式的目的，我们包括了来自 CDN 的[布尔玛 CSS 框架](https://bulma.io/)和[字体牛逼](https://fontawesome.com/)。我们还在页面底部包含了一个`main.js`文件，它将包含我们为前端定制的所有 JavaScript 代码。请现在在一个`static/public/scripts/`目录中创建该文件。

### 家庭视图

在我们的主页上，我们将显示数据库中所有笔记的列表和一个按钮，该按钮将显示一个模态窗口，该窗口带有一个允许我们通过 Ajax 创建新笔记的表单。

在`lib/views`中创建一个名为`home.pug`的文件，内容如下:

```
extends layout

block content
  section.section
    .container

      h1.title.has-text-centered
        | Notes Board

      .tabs.is-centered
        ul
          li
            a.show-modal(href='#') Publish

      main(container).notes-list
        each note in data.notes
          include components/note
          hr

      .modal
        .modal-background
        .modal-card
          header.modal-card-head
            p.modal-card-title Add note
            button.delete(aria-label='close')
          section.modal-card-body
            form(action='/note' method='POST').note-form#note-form
              .field
                .control
                  input.input(name='noteTitle' type='text' placeholder='Title')
              .field
                .control
                  input.input(name='noteDescription' type='text' placeholder='Short description')
              .field
                .control
                  textarea.textarea(name='noteContent' placeholder='Contents')
              .field
                .control
                  button.button.is-link Save 
```

### 笔记视图

笔记页面与主页非常相似，但是在这种情况下，我们显示了一个菜单，其中包含特定于当前笔记的选项、笔记的内容以及与主页相同的表单，但是已经填充了当前笔记信息，所以当我们更新它时，它就在那里。

在`lib/views`中创建一个名为`note.pug`的文件，内容如下:

```
extends layout

block content
  section.section
    .container
      h1.title.has-text-centered
          | Notes Board

      .tabs.is-centered
        ul
          li: a(href='/') Home
          li: a.show-modal(href='#') Update
          li: a(href=`/note/${note.slug}/delete`) Delete

      include components/note

      .modal
        .modal-background
        .modal-card
          header.modal-card-head
            p.modal-card-title Edit note
            button.delete(aria-label='close')
          section.modal-card-body
            form(action=`/note/${note.slug}` method='PUT').note-form#note-form
              .field
                .control
                  input.input(name='noteTitle' type='text' placeholder='Title' value=note.title)
              .field
                .control
                  input.input(name='noteDescription' type='text' placeholder='Short description' value=note.description)
              .field
                .control
                  textarea.textarea(name='noteContent' placeholder='Contents') #{note.content}
              .field
                .control
                  button.button.is-link Save 
```

### 客户端上的 JavaScript

为了创建和更新 notes，我们将使用一些 JavaScript 来显示/隐藏表单的模态，并通过 Ajax 提交请求。虽然这不是绝对必要的，但我们认为它为用户提供了更好的体验。

这是我们在`static/public/scripts/`目录中的`main.js`文件的内容:

```
// Modal

const modal = document.querySelector(".modal");
const html = document.querySelector("html");

const showModal = () => {
  modal.classList.add("is-active");
  html.classList.add("is-clipped");
};

const hideModal = () => {
  modal.classList.remove("is-active");
  html.classList.remove("is-clipped");
};

document.querySelector("a.show-modal").addEventListener("click", function(e) {
  e.preventDefault();
  showModal();
});

modal.querySelector(".modal .delete").addEventListener("click", function(e) {
  e.preventDefault();
  hideModal();
});

// Form submition

const form = document.querySelector("#note-form");
const url = form.getAttribute("action");
const method = form.getAttribute("method");

const prependNote = html => {
  const notesList = document.querySelector(".notes-list");
  const div = document.createElement("div");
  div.innerHTML = html;
  notesList.insertBefore(div.firstChild, notesList.firstChild);
};

const updateNote = html => {
  const article = document.querySelector("article");
  const div = document.createElement("div");
  div.innerHTML = html;
  article.parentNode.replaceChild(div.firstChild, article);
};

const onSuccess = html => {
  hideModal();
  form.reset();

  if (method === "POST") {
    prependNote(html);
  } else if (method === "PUT") {
    updateNote(html);
  }
};

form.addEventListener("submit", e => {
  e.preventDefault();

  fetch(url, {
    method,
    body: new FormData(form)
  })
    .then(response => response.text())
    .then(text => onSuccess(text))
    .catch(error => console.error(error));
}); 
```

每当用户在模态窗口中提交表单时，我们从表单元素中获取信息，并将其发送到我们的后端，这取决于动作 URL 和方法(`POST`或`PUT`)。然后，我们将得到一个包含新笔记数据的 HTML 块。当我们添加注释时，我们只需将其添加到主页列表的顶部，当我们更新注释时，我们会在注释视图中替换新注释的内容。

### 添加对服务器上视图的支持

为了利用我们的视图，我们必须将它们包含在我们的控制器中，并添加所需的设置。

在我们的`server.js`文件中，让我们在文件顶部导入节点[路径](https://nodejs.org/api/path.html)实用程序，因为我们在代码中使用它来指示视图的路径:

```
const Path = require("path"); 
```

现在，用下面的代码块替换`server.route(Routes);`行:

```
await server.register([require("@hapi/vision")]);

server.views({
  engines: { pug: require("pug") },
  path: Path.join(__dirname, "lib/views"),
  compileOptions: {
    pretty: false
  },
  isCached: Settings.env === "production"
});

// Add routes
server.route(Routes); 
```

在我们添加的代码中，我们首先向 Hapi.js 服务器注册了 [Vision](https://github.com/hapijs/vision) 插件，它将提供视图功能。然后我们为视图添加设置——比如我们将要使用的引擎和视图所在的路径。在代码块的末尾，我们添加回我们的路线。

这将使我们的视图在服务器上工作，但是我们仍然必须声明我们将用于每条路由的视图。

### 设置主视图

打开`lib/controllers/home.js`文件，用以下内容替换`return`语句:

```
return h.view('home', {
  data: {
    notes: result
  },
  page: 'Home — Notes Board',
  description: 'Welcome to my Notes Board'
}); 
```

注册了 Vision 插件后，我们现在在 reply 对象上有了一个可用的`view`方法。我们将使用它来选择我们的`views`目录中的`home`视图，并发送渲染视图时将要使用的数据。

在我们提供给视图的数据中，我们还包括页面标题和搜索引擎的元描述。

如果您想在这一点上进行尝试，请访问 [http://localhost:3000/](http://localhost:3000/) 。你应该会看到一个很漂亮的笔记板，有一个**发布**按钮，它什么也不做。

### 设置音符视图:`create`功能

现在，每当我们创建一个便笺时，我们就从服务器向客户机发送一个 JSON 对象。但是因为我们是用 Ajax 完成这个过程的，所以我们可以用 HTML 格式发送新的注释，以便添加到页面中。为了做到这一点，我们用我们所拥有的数据来呈现*注释*组件。

首先要求 Pug 位于`controllers/note.js`文件的顶部:

```
const Pug = require("pug"); 
```

然后，在`create`方法中，用下面的代码块替换行`return result;`:

```
// Generate a new note with the 'result' data
return Pug.renderFile(
  Path.join(__dirname, "../views/components/note.pug"),
  {
    note: result
  }
); 
```

我们使用来自 Pug 的`renderFile`方法，用我们刚刚从模型中收到的数据来呈现注释模板。

### 设置音符视图:`read`功能

当我们进入一个注释页面时，我们应该得到包含注释内容的注释模板。为此，我们必须将`read`函数的`return note;`行替换为:

```
return h.view("note", {
  note,
  page: `${note.title} — Notes Board`,
  description: note.description
}); 
```

与主页一样，我们选择一个视图作为第一个参数，我们将要使用的数据作为第二个参数。

### 设置音符视图:`update`功能

每次我们更新一个笔记，我们都会像创建新笔记一样回复。用以下代码替换`update`函数中的`return result;`行:

```
// Generate a new note with the updated data
return Pug.renderFile(
  Path.join(__dirname, "../views/components/note.pug"),
  {
    note: result
  }
); 
```

*注意:删除功能不需要视图，因为一旦注释被删除，它将重定向到主页。*

## 提供静态文件

我们在客户端使用的 JavaScript 和 CSS 文件由来自`static/public/`目录的 Hapi.js 提供。但它不会自动发生；我们必须向服务器表明我们想要将这个文件夹定义为公共的。这是使用[惰性](https://github.com/hapijs/inert)包完成的，您可以使用以下命令安装它:

```
npm install @hapi/inert 
```

在`server.js`文件内的`server.register`函数中，导入惰性插件并向哈比神注册，如下所示:

```
await server.register([require("@hapi/vision"), require("@hapi/inert")]); 
```

现在我们必须定义提供静态文件的路径，以及它们在服务器文件系统上的位置。在`routes.js`中导出对象的末尾添加以下条目:

```
{
  // Static files
  method: "GET",
  path: "/{param*}",
  handler: {
    directory: {
      path: Path.join(__dirname, "../static/public")
    }
  },
  config: {
    description: "Provides static resources"
  }
} 
```

这个路径将使用`GET`方法，我们已经用一个包含我们想要公开的目录的对象替换了 handler 函数。

您可以在 [Hapi.js 文档](https://hapi.dev/tutorials/servingfiles/?lang=en_US)中找到更多关于提供静态内容的信息。

## 结论

至此，我们有了一个使用 MVC 架构的非常基本的 Hapi.js 应用程序。尽管在将我们的应用程序投入生产之前，我们仍然需要注意一些事情(比如输入验证、错误处理、错误页面等等)，但是这应该作为学习和构建您自己的应用程序的基础。

如果您想进一步了解这个示例，在完成所有小细节(与架构无关)以使它成为一个健壮的应用程序之后，您可以实现一个认证系统，这样只有注册用户才能发布和编辑注释。但是您的想象力是有限的，所以可以随意分叉[应用程序存储库](https://github.com/sitepoint-editors/notes-board)并进城！

通过进一步阅读深入了解 Node.js:

*   [Node.js Web 开发](https://www.sitepoint.com/premium/books/node-js-web-development-fourth-edition/)
*   [使用 Node.js 实现自动化](https://www.sitepoint.com/premium/books/automating-with-node-js/)

## 分享这篇文章