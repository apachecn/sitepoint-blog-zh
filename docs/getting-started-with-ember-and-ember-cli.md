# Ember 和 Ember CLI 入门

> 原文：<https://www.sitepoint.com/getting-started-with-ember-and-ember-cli/>

这些年来，恩伯经历了很多变化。其中最大的变化之一是引入了 [Ember CLI](http://www.ember-cli.com/) ，这是一个为 Ember 构建的命令行实用程序。它结合了几个功能，包括生成器、缩小器、CSS 预处理器编译器、自动加载和 ES6 模块加载器。这个命令行工具将帮助你减少花费在设置一些工具上的时间，比如[咕噜](http://gruntjs.com/)和[大口](http://gulpjs.com/)。我们可以说，对于任何全新的 Ember 项目来说，它都可能是这些工具的一个很好的替代品。

在本文中，您将了解如何使用 Ember CLI 构建简单的联系人管理器应用程序。这篇教程与我在 SitePoint 上发表的关于 Ember 的其他[文章有些不同，因为它们没有附带 Ember CLI。然而，这些概念中的大部分仍然适用，所以我建议您看一看它们并继续学习。](https://www.sitepoint.com/?s=Lamin+Sanneh)

[本文的完整代码可以在 GitHub](https://github.com/sitepoint-editors/sitepoint-contactmanager) 上找到。

## 如何安装 Ember CLI

要安装 Ember CLI，您需要先安装几个依赖项。第一个是 [Node.js](https://nodejs.org/) 。您至少需要 0.12.x 版本。接下来，需要安装 [Bower](http://bower.io/) ，可以通过运行以下命令进行操作:

```
npm install -g bower
```

然后，要安装 Ember CLI，请运行以下命令:

```
npm install -g ember-cli
```

### 如何创建一个新的 Ember 项目

在我们开始做令人惊叹的事情之前，您需要打开一个终端并执行以下命令，以便创建一个名为 **contactmanager** 的新项目文件夹:

```
ember new contactmanager
```

第二步，进入目录，然后使用以下命令安装所有 npm 和 Bower 依赖项:

```
cd contactmanager
npm install
bower install
```

此时，通过执行以下命令启动内置的 Ember 服务器:

```
ember serve
```

现在可以通过 URL `localhost:4200`访问您的新应用程序。这是在本地机器上运行的 Ember 应用程序的默认端口，但是如果您愿意，您可以更改它。如果你遵循了指示的所有步骤，你现在应该在你的浏览器中看到一个标题“欢迎来到 Ember”。

## 会员大会和结构

在开始构建我们的应用程序之前，让我们回顾一些 Ember 约定。

### 路由器和路由

路由是 Ember 应用程序的入口点。使用`Router`在文件`app/router.js`中定义路线。它们让你可以访问应用程序不同部分。例如，如果您决定需要管理应用程序中的用户，您必须定义一条`users`路线。您可以使用以下语法来完成此操作:

```
Router.map(function() {
  this.resource('users', function() {});
});
```

这将为我们创建以下 URL:

*   `/users/`
*   `/users/index/`
*   `/users/loading/`

通常，当您定义一个路由时，Ember 希望找到其他相关的类型，比如路由、控制器和模板。我们可以决定显式地创建这些类型，或者让 Ember 为我们创建它们。在许多应用程序中，您很可能必须自己创建它们，但这取决于您。

记住区分`Router`和`Route`是至关重要的。我们上面创建的 URL 结构是使用`Router`完成的。这些只是表明我们想在应用程序中使用这些 URL。我们还没有创建实际的路由，只是创建了这些路由的 URL。要创建一个`Route`，我们必须在 routes 文件夹中遵循这个过程。如果您感到困惑，不要担心，我将在本文的后面深化这个主题。

### 控制器

控制器是一种用于存储视图状态的类型，位于`app/controllers`文件夹中。他们与路线携手合作。在这种情况下，上面的 URL 对应于`/user/`，需要一个名为`/users/`的控制器。同样在这里，我们可以自由选择是否定义它。控制器还为像点击、悬停等视图动作定义事件处理程序。

### 模板

模板是 Ember 的表示部分。你用一种叫做[手柄](http://handlebarsjs.com/)的模板语言编写它，它可以编译成普通的 HTML。模板放在`app/templates`文件夹中。

### 该组件

组件是功能性的独立小块。您可以将它们视为可重用且易于维护的表示和功能的组合。

### 余烬数据

这是一个库，由 Ember 核心团队维护，它补充了 Ember 核心，充当管理数据模型的前端 ORM。还有一些我以前没有用过的替代方法，它们超出了本文的范围，因为我们将使用 Ember-data。

## 应用程序

我们将要构建的联系人管理应用程序将包含一个用户列表，其中包含可供他们使用的联系人信息。该应用程序将允许我们创建、编辑、删除和查看用户。

为了使我们的应用程序简洁，我们将使用 Ember CLI 附带的 fixture 适配器。除了在页面刷新期间不会持久保存数据之外，它充当后端。首先，使用`ember new contactmanager`创建一个新的 Ember 项目，如果你还没有这样做的话。

### 生成用户模型

移动到项目文件夹，并使用以下内容生成用户模型:

```
ember generate model user
```

这将在`app/models`中创建一个名为`user.js`的文件，内容如下:

```
import DS from 'ember-data';

export default DS.Model.extend({
});
```

进行必要的更改，使导出语句看起来像这样:

```
export default DS.Model.extend({
  firstName: DS.attr(),
  lastName: DS.attr(),
  addressLine: DS.attr(),
  postCode: DS.attr(),
  country: DS.attr()
});
```

这定义了我们的用户模型将拥有的属性。

### 生成用户路线

现在，将下面几行添加到您的`router.js`文件中，使一些 URL 对我们可用:

```
Router.map(function() {
  this.resource('users', function() {
    this.route('show',{path: '/:user_id'});
    this.route('edit',{path: '/:user_id/edit'});
  });
});
```

我们有三个新的网址。其中一个是列出用户，另一个是查看单个用户，最后一个是编辑用户的信息。接下来，让我们使用以下内容创建一条用户路线:

```
ember generate route users
```

此路由将用于检索我们的用户列表。使用以下代码片段更改其内容:

```
import Ember from 'ember';

export default Ember.Route.extend({
  model: function(){
    return this.store.find('user');
  }
});
```

### 设置夹具数据并生成用户模板

现在，让我们向应用程序添加一些临时数据。为此，运行命令

```
ember generate adapter application
```

这将在文件夹`app/adapters/`中生成一个名为`application.js`的文件。默认情况下，Ember 使用 RestAdapter 来查询模型。这个适配器假设您有一个后端系统，为您的 Ember 客户端应用程序提供 JSON 数据。由于我们没有后端，在这种情况下，我们希望使用夹具数据。因此，我们将适配器代码更新如下:

```
import DS from 'ember-data';

export default DS.FixtureAdapter.extend({
});
```

并将以下内容添加到您的用户模型中，以创建一些装置。

```
User.reopenClass({
   FIXTURES: [{
      id: 1,
      firstName: 'James',
      lastName: 'Rice',
      addressLine: '66 Belvue Road',
      postCode: 'M235PS',
      country: 'United Kingdom'
   }]
});
```

如果您导航到 URL `localhost:4200/users`，您将只能看到旧的问候消息，而看不到我们刚刚添加的用户设备数据。要查看用户数据，我们需要使用以下命令为用户创建一个模板:

```
ember generate template users
```

这将在文件夹`app/templates/`中创建一个名为`users.hbs`的文件。打开此文件，并按如下方式更新其内容:

```
<ul>
  {{#each user in model}}
    <li>{{user.firstName}} {{user.lastName}} <span>Edit</span></li>
  {{/each}}
</ul>
{{outlet}}
```

现在，您应该看到一个用户列表，每个用户旁边都有一个编辑文本。因为我们在 fixture 数据中只有一个用户，所以我们将只看到一个用户。您可以随意向用户设备添加更多的用户对象。只要确保每个都有一个唯一的 ID。

### 显示单个用户

现在我们已经列出了我们的用户，让我们看看如何显示用户的完整信息。首先。通过更改`li`元素来更改`users`模板中的代码，如下所示:

```
<li>
   {{#link-to 'users.show' user}} {{user.firstName}} {{user.lastName}} {{/link-to}}
   <span>Edit</span>
</li>
```

这应该用一个链接将每个用户名包围起来。当你点击链接时，只有网址会改变，而页面上的所有内容都保持不变。这是因为我们还没有生成一个用户模板。

运行命令:

```
ember generate template users/show
```

此时，创建的模板(`app/templates/users/show.hbs`)为空。打开它并添加以下代码:

```
<p>{{#link-to 'users' }}back{{/link-to}} to Users</p>
<p>First Name: {{model.firstName}}</p>
<p>Last Name: {{model.lastName}}</p>
<p>Address: {{model.addressLine}}</p>
<p>Postcode: {{model.postCode}}</p>
<p>Country: {{model.country}}</p>
```

这样，您应该能够看到您点击的每个用户的完整信息。

### 编辑单个用户

如果您想编辑单个用户，只需遵循几个简单的步骤。首先，通过用一个链接将每个用户名旁边的`Edit`文本包装起来，链接到用户编辑路线。然后，将`Edit`改为

```
{{#link-to 'users.edit' user }}Edit {{/link-to}}
```

接下来，让我们使用以下代码生成一个用户控制器:

```
ember generate controller users/edit
```

在(用户控制器)内部，将内容更改如下:

```
import Ember from 'ember';

export default Ember.Controller.extend({
  actions: {
    saveUser: function(user){
      user.save();
      this.transitionToRoute('users');
    }
  }
});
```

完成后，使用以下内容生成用于编辑用户的模板:

```
ember generate template users/edit
```

在新模板`app/templates/users/edit`中，粘贴以下代码:

```
<p>{{#link-to 'users' }}back{{/link-to}} to Users</p>
<form {{action 'saveUser' model on='submit' }} >
  <p>First Name: {{input value=model.firstName}}</p>
  <p>Last Name: {{input value=model.lastName}}</p>
  <p>Address: {{input value=model.addressLine}}</p>
  <p>Postcode: {{input value=model.postCode}}</p>
  <p>Country: {{input value=model.country}}</p>
  <p><input type="submit" value="Save" ></p>
</form>
```

当我们提交表单时，这段代码调用控制器上的`saveUser()`函数。该函数被传递给正在编辑的用户，并保存修改后的信息。

完成这一更改后，当您单击用户的编辑链接时，您可以编辑他的详细信息。当您单击“保存”按钮时，您可以保存它们，然后您将被重定向回用户列表。万岁！我们现在有一个简单的联系人列表管理器。

您可以将它挂接到一个真正的后端，以便在页面刷新时保存数据，从而将它变成一个完整的应用程序。我还鼓励您在应用程序中添加删除功能，这样您就可以随时删除不需要的用户。

### 结论

Ember <q cite="http://emberjs.com/">是一个构建雄心勃勃的网络应用</q>的框架。它有一个约定胜于配置的哲学，这意味着它是基于几个共同的决定，并且有许多默认(约定)，这使得开发过程对你来说更容易。这样你就不用在开发的时候做很多琐碎的决定了。

我希望您喜欢阅读本教程，并了解如何在您的项目中使用如此强大而简单的 JavaScript 框架。请在下面的评论中告诉我们你的想法。[你可以在 GitHub](https://github.com/sitepoint-editors/sitepoint-contactmanager) 上找到该应用的代码。

## 分享这篇文章