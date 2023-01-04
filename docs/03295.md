# 了解 Ember 2 中的组件

> 原文：<https://www.sitepoint.com/understanding-components-in-ember-2/>

*这篇文章由[埃德温·雷诺索](https://www.sitepoint.com/author/ereynoso/)和[尼尔森·雅克](https://twitter.com/nilsonjacques)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

组件是 Ember 应用程序的重要组成部分。它们允许您定义自己的特定于应用程序的 HTML 标记，并使用 JavaScript 实现它们的行为。从 Ember 2.x 开始，组件将取代[视图](http://emberjs.com/deprecations/v1.x/#toc_ember-view)和[控制器](http://emberjs.com/deprecations/v1.x/#toc_objectcontroller)(已经被否决)，并且是构建 Ember 应用程序的推荐方式。

Ember 的组件实现尽可能地遵循 W3C 的 Web 组件规范。一旦定制元素在浏览器中变得广泛可用，将 Ember 组件移植到 W3C 标准并让其他框架使用它们应该很容易。

如果您想了解为什么可路由组件会取代控制器和视图，请查看 Ember 核心团队成员 Yehuda Katz 和 Tom Dale 制作的[短片。](https://www.youtube.com/watch?v=QgycDZjOnIg)

## 标签切换器应用程序

为了深入理解 Ember 组件，我们将构建一个`tab-switcher`小部件。这将包括一组具有相关内容的选项卡。单击选项卡将显示该选项卡的内容，并隐藏其他选项卡的内容。够简单吗？我们开始吧。

和以往一样，你可以在我们的 GitHub repo 上找到本教程的代码，或者如果你想在你的浏览器中试验代码，可以在 T2 的这个 Ember Twiddle 上找到。

## 灰烬成分的剖析

Ember 组件由一个手柄模板文件和一个附带的 Ember 类组成。只有当我们需要与组件进行额外的交互时，才需要实现这个类。组件的使用方式类似于普通的 HTML 标签。当我们构建选项卡切换器组件时，我们将能够像这样使用它:

```
{{tab-switcher}}{{/tab-switcher}} 
```

Ember 组件的模板文件位于目录`app/templates/components`中。类文件存在于`app/components`中。我们使用所有小写字母和由连字符分隔的单词来命名 Ember 组件。这种命名是约定俗成的，因此我们避免了与未来 HTML web 组件的名称冲突。

我们主要的 Ember 组件将是`tab-switcher`。请注意，我说的是主组件，因为我们将有几个组件。您可以将组件与其他组件结合使用。您甚至可以将组件嵌套在另一个父组件中。在我们的`tab-switcher`中，我们将有一个或多个`tab-item`组件，如下所示:

```
{{#each tabItems as |tabItem| }}
  {{tab-item item=tabItem 
             setSelectedTabItemAction="setSelectedTabItem" }}
{{/each}} 
```

如您所见，组件也可以像原生 HTML 元素一样拥有属性。

## 创建一个 Ember 2.x 项目

为了遵循这个教程，您需要创建一个 EMber 2.x 项目。方法如下:

Ember 使用 npm 安装。关于 npm 的教程，你可以[看这里](https://www.sitepoint.com/beginners-guide-node-package-manager/)。

```
npm install -g ember-cli 
```

在撰写本文时，将会引入 1.13 版本

```
ember -v
=> version: 1.13.8 
```

接下来，创建一个新的 Ember 应用程序:

```
ember new tabswitcher 
```

导航到该目录并编辑`bower.json`文件以包含最新版本的 Ember、ember-data 和 Ember-load-initializer:

```
{
  "name": "hello-world",
  "dependencies": {
    "ember": "^2.1.0",
    "ember-data": "^2.1.0",
    "ember-load-initializers": "^ember-cli/ember-load-initializers#0.1.7",
    ...
  }
} 
```

回到终端运行:

```
bower install 
```

Bower 可能会提示您 Ember 的版本解析。从提供的列表中选择 2.1 版本，并在前面加上感叹号，以将解决方案保留到`bower.json`。

接下来启动 Ember CLI 的开发服务器:

```
ember server 
```

最后导航到 [http://localhost:4200/](http://localhost:4200/) 并检查浏览器控制台的版本。

## 创建选项卡切换器组件

让我们使用 Ember 的内置生成器创建一个标签切换器组件:

```
ember generate component tab-switcher 
```

这将创建三个新文件。一个是 HTML 的 Handlebars 文件(`app/templates/components/tab-switcher.hbs`)，第二个是组件类的 JavaScript 文件(`app/components/tab-switcher.js`)，最后一个是测试文件(`tests/integration/components/tab-switcher-test.js`)。测试组件超出了本教程的范围，但是你可以在 Ember 网站上阅读更多相关内容。

现在运行`ember server`来加载服务器并导航到 [http://localhost:4200/](http://localhost:4200/) 。您应该会看到一条标题为“欢迎来到 Ember”的欢迎消息。那么为什么我们的组件没有出现呢？嗯，我们还没有使用它，所以让我们现在这样做。

## 使用组件

打开应用程序模板`app/templates/application.hbs`。在`h2`标签后添加以下内容以使用该组件。

```
{{tab-switcher}} 
```

在 Ember 中，组件有两种用法。第一种方法叫做*内联形式*，就是在没有任何内容的情况下使用它们。这就是我们在这里所做的。第二种方法叫做*块形式*，它允许组件被传递一个手柄模板，这个模板在组件的模板中任何出现`{{yield}}`表达式的地方呈现。在本教程中，我们将坚持使用内联形式。

不过，这仍然没有在屏幕上显示任何内容。这是因为，组件本身没有任何要显示的内容。我们可以通过在组件的模板文件(`app/templates/components/tab-switcher.hbs`)中添加下面一行来改变这一点:

```
<p>This is some content coming from our tab switcher component</p> 
```

现在，当页面重新加载时(这应该会自动发生)，您会看到上面显示的文本。激动人心的时刻！

## 创建选项卡项目组件

现在我们已经设置了我们的主`tab-switcher`组件，让我们创建一些`tab-item`组件来嵌套它。我们可以像这样创建一个新的`tab-item`组件:

```
ember generate component tab-item 
```

现在将新组件的把手文件(`app/templates/components/tab-item.hbs`)更改为:

```
<span>Tab Item Title</span>
{{yield}} 
```

接下来，让我们将三个`tab-items`嵌套在主`tab-switcher`组件中。将`tab-switcher`模板文件(`app/templates/components/tab-switcher.hbs`)更改为:

```
<p>This is some content coming from our tab switcher component</p>

{{tab-item}}
{{tab-item}}
{{tab-item}}

{{yield}} 
```

如上所述，`yield`助手将呈现传递给我们组件的任何手柄模板。然而，这只有在我们以块的形式使用`tab-switcher`时才有用。既然我们不是，我们可以删除`yield`助手。

现在，当我们查看浏览器时，我们会看到三个`tab-item components`，都显示“选项卡项目标题”。我们的组件现在相当静态，所以让我们添加一些动态数据。

## 添加动态数据

当一个 Ember 应用程序启动时，[路由器](http://guides.emberjs.com/v2.1.0/routing/)负责显示模板、加载数据以及设置应用程序状态。它通过将当前 URL 与您定义的路由进行匹配来实现这一点。让我们为我们的应用程序创建一个路由:

```
ember generate route application 
```

对命令行问题回答“否”,以避免覆盖现有的`application.hbs`文件。这也将生成一个文件`app/routes/application.js`。打开它并添加一个模型属性:

```
export default Ember.Route.extend({
  model: function(){
  });
}); 
```

一个[模型](http://guides.emberjs.com/v2.1.0/models/)是一个对象，它表示应用程序呈现给用户的底层数据。用户期望看到的任何东西都应该用模型来表示。在这种情况下，我们将把选项卡的内容添加到模型中。为此，请按如下方式修改文件:

```
import Ember from 'ember';

export default Ember.Route.extend({
  model: function(){
    var tabItems = [
      {
        title: 'Tab 1',
        content: 'Some exciting content for the tab 1'
      },
      {
        title: 'Tab 2',
        content: 'Some awesome content for the tab 2'
      },
      {
        title: 'Tab 3',
        content: 'Some stupendous content for the tab 3'
      }
    ];
    return tabItems;
  }
}); 
```

然后将`tab-switcher`模板文件(`app/templates/components/tab-switcher.hbs`)更改为:

```
{{#each tabItems as |tabItem| }}
  {{tab-item item=tabItem }}
{{/each}} 
```

接下来，将`tab-item`模板文件(`app/templates/components/tab-item.hbs`)的内容更改为:

```
<span>{{item.title}}</span>
{{yield}} 
```

最后将`application.hbs`文件中的`tab-switcher`用法改为:

```
{{tab-switcher tabItems=model}} 
```

这演示了如何将属性传递给组件。我们已经使`item`属性可以被`tab-item`组件模板访问。在页面刷新之后，您现在应该会看到反映模型数据的选项卡项目标题。

## 使用操作添加交互

现在让我们确保当用户点击一个`tab-item`标题时，我们显示那个`tab-item`的内容。将`tab-switcher`模板文件(`app/templates/components/tab-switcher.hbs`)更改为:

```
{{#each tabItems as |tabItem| }}
  {{tab-item item=tabItem setSelectedTabItemAction="setSelectedTabItem" }}
{{/each}}

<div class="item-content">
  {{selectedTabItem.content}}
</div> 
```

这种变化假设我们在`tab-switcher`组件上有一个`tabItem`属性。该属性代表当前选中的`tab-item`。我们目前没有任何这样的财产，所以让我们来处理。

在一个常规的模板中，一个动作会冒泡到一个控制器。在组件模板中，动作会冒泡到组件的类。它不再向上冒泡。

我们需要一种方法向`tab-switcher`组件发送点击动作。这应该在单击它的任何子`tab-item`组件后发生。记得我说过动作被发送到组件的类，而不是层次结构的更高层。

因此，来自子组件的任何动作都不可能到达父组件。不要担心，因为这只是组件的默认行为，有一个解决方法可以绕过它。

简单的解决方法是向`tab-switcher`模板(`app/templates/components/tab-switcher.hbs`)添加一个动作，如下所示:

```
{{#each tabItems as |tabItem| }}
  <div {{action "setSelectedTabItem" tabItem}} >
    {{tab-item item=tabItem setSelectedTabItemAction="setSelectedTabItem" }}
  </div>
{{/each}}

<div class="item-content">
  {{selectedTabItem.content}}
</div> 
```

并将`tab-switcher`类文件(`app/components/tab-switcher.js`)更改为

```
export default Ember.Component.extend({
  actions: {
    setSelectedTabItem: function(tabItem){
      this.set('selectedTabItem', tabItem);
    }
  }
}); 
```

此时，如果您在浏览器中查看我们的应用程序，它将按预期工作。

然而，这种变通办法并没有解决一个动作只冒泡到组件的类的事实，所以让我们用一种可以这样做的方式来做。保留`app/components/tab-switcher.js`中的更改，但将`app/templates/components/tab-switcher.hbs`恢复到之前的状态:

```
<div class="item-content">
  {{selectedTabItem.content}}
</div>

{{#each tabItems as |tabItem| }}
  {{tab-item item=tabItem setSelectedTabItemAction="setSelectedTabItem" }}
{{/each}} 
```

现在让我们将`tab-item`模板改为:

```
<span {{action "clicked" item }}>{{item.title}}</span>
{{yield}} 
```

并将`tab-item`类文件改为:

```
export default Ember.Component.extend({
  actions:{
    clicked: function(tabItem){
      this.sendAction("setSelectedTabItemAction", tabItem);
    }
  }
}); 
```

在这里，您可以看到我们添加了一个动作处理程序来处理对`tab-item`标题的点击。这将一个动作从`tab-item`组件发送到它的父组件`tab-switcher`组件。这个动作和一个参数一起出现在层次结构中，也就是我们点击的`tabItem`。这是为了将其设置为父组件上的当前`tab-item`。

注意，我们使用属性`setSelectedTabItemAction`作为要发送的动作。这不是被发送的实际动作名称，而是包含在属性中的值——在本例中是`setSelectedTabItem`,它是父组件上的处理程序。

## 结论

这就让我们结束了对 Ember 组件的介绍。我希望你喜欢它。在整个 Ember 项目中使用可重用组件的生产力优势是不可低估的(实际上是在整个项目中)。为什么不试一试？本教程的源代码可以在 [GitHub](https://github.com/sitepoint-editors/sitepoint-tabswitcher) 上获得。

你已经在使用 Ember 中的组件了吗？到目前为止，你有什么经历？我很想在评论中听到你的意见。

## 分享这篇文章