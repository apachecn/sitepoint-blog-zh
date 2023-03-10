# 使用 Devise 和 Bootstrap 在轨道上设置 Angular SPA

> 原文：<https://www.sitepoint.com/setting-up-an-angular-spa-on-rails-with-devise-and-bootstrap/>

![Setting Up an Angular SPA on Rails with Devise and Bootstrap](img/80ad357ea8e815e3c98c5366570d984d.png)

*本文最初发表于[jessenovotny.com](http://www.jessenovotny.com/2016/11/22/angular_with_devise/)。*

当我开始编写我的第一个角度单页应用程序(SPA)时，我注意到用于设置和与[design](https://github.com/plataformatec/devise)集成的资源变得单薄或支离破碎。我发现的最有用的指南实际上只是一般 Angular with Rails 演练的一部分。还有其他一些资源要么太复杂，要么太先进，它们并没有真正经历最初的起步阶段。对于一个新程序员来说，最大的挑战之一就是从头开始。我知道，因为我也是其中之一。

我在网上课程中学到的大部分东西都是以越来越先进的小型组件的形式交付的。我开了一个实验室，基础工作已经做好了，所以从零开始建立一个应用程序不需要太多的练习。为了课程完成时间，这是有意义的。此外，你只需要从头开始构建几个应用程序，就能感受到它是如何完成的。如果你还没有到达那里，这个演练将会是你的拿手好戏。

一旦我最终让所有的部分都工作起来，我的第一个 Angular 项目启动并运行，我觉得有必要回馈社区。由于我目前没有足够的“声誉点”来回答关于堆栈溢出的问题，下一个最好的办法就是用 Devise 和 Bootstrap 在 Rails 上建立一个 Angular SPA。以下是*确切地说*我希望我在对这个话题的初步研究中发现了什么。

诚然，web 开发的很大一部分是能够在没有解决方案的情况下解决复杂的问题。我觉得有时候一个新的开发者只是需要一只援助之手。这就是了。

## 入门指南

这个指南是一个跳板。它假设您已经对 Angular、Rails、Devise 和 Bootstrap 有了基本的了解。我选择不探究活动记录，但是我确实触及了活动模型序列化程序，因为它是将模型发送到 JavaScript 前端所必需的。关于这个主题还有很多要学的，这将保证它有自己的一系列指南。同样，我只在可以验证 Bootstrap 工作的时候安装它。

请随意阅读我为本教程制作的视频:

[https://www.youtube.com/embed/CtsC0iRxrAk?rel=0](https://www.youtube.com/embed/CtsC0iRxrAk?rel=0)

<br>

### 安装

若要开始，您需要打开“终端”并导航到您想要创建新应用程序的文件夹。在这个演示中，我在桌面上。

在终端中，您将运行`$ rails new YOUR-APP`,它初始化 Rails，创建一个包含整个框架的目录，并将所有的组件打包到 gems 中。(如果您不熟悉，`$`表示终端命令。)

打开您的`Gemfile`，移除`gem 'turbolinks'`并添加以下内容:

```
 gem 'bower-rails'
gem 'devise'
gem 'angular-rails-templates' #=> allows us to place our html views in the assets/javascripts directory
gem 'active_model_serializers'
gem 'bootstrap-sass', '~> 3.3.6' #=> bootstrap also requires the 'sass-rails' gem, which should already be included in your gemfile 
```

虽然 Bower 对这个项目来说并不重要，但我选择使用它的原因很简单:体验。迟早，我可能会发现自己正在开发一个用 Bower 构建的应用程序，所以为什么不现在就开始玩呢？

鲍尔是什么？你可以在他们的网站 bower.io 上了解更多，但据我所知，它本质上是一个包管理器，就像 Ruby gems 或 npm 一样。您可以用 npm 安装它，但是我选择在本指南中包含`bower-rails` gem。

### 初始化 Gems、创建数据库和添加迁移

现在，我们将安装/初始化这些 gem，创建我们的数据库，添加一个迁移，以便用户可以使用用户名注册，然后使用以下命令将这些迁移应用到我们的模式:

```
 $ bundle install
$ rake db:create #=> create database
$ rails g bower_rails:initialize json  #=> generates bower.json file for adding "dependencies"
$ rails g devise:install #=> generates config/initializers/devise.rb, user resources, user model, and user migration with a TON of default configurations for authentication
$ rails g migration AddUsernametoUsers username:string:uniq #=> generates, well, exactly what it says.
$ rake db:migrate 
```

当你有动力构建你的应用程序时，你可能会有更多的依赖项或“包”，但这里是你开始需要的。将以下供应商依赖关系添加到`bower.json`:

```
 ...
"vendor": {
  "name": "bower-rails generated vendor assets",
  "dependencies": {
    "angular": "v1.5.8",
    "angular-ui-router": "latest",
    "angular-devise": "latest"
  }
} 
```

一旦在 bower.json 中保存了这些更改，您将希望使用以下命令安装这些包，然后从之前安装的“活动模型序列化程序”gem 中生成您的用户序列化程序:

```
 $ rake bower:install
$ rails g serializer user 
```

查找 app/serializer/user _ serializer . Rb 并在`attributes :id`后直接添加`, :username`，这样当 Devise 从 Rails 请求用户信息时，您可以显示他们选择的用户名。这比说“欢迎，jesse@email.com”或者更糟糕的“欢迎，5 uper＄3 cretp 4 ss word”要好得多。开玩笑的，不过说真的，别那么做。

在`class Application < Rails::Application`正下方的`config/application.rb`中增加以下内容:

```
 config.to_prepare do
  DeviseController.respond_to :html, :json
end 
```

由于 Angular 将使用`.json`请求关于用户的信息，我们需要确保 DeviseController 将适当地响应，默认情况下它不会这样做。

### 完成后端设置

我们很快就要完成我们的后端了。只需再做一些调整…

打开`config/routes.rb`，在`devise_for :users`下添加下面一行:`root 'application#index'`。然后用下面的代码片段替换`app/controllers/application_controller.rb`的内容:

```
 class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  before_action :configure_permitted_parameters, if: :devise_controller?
  skip_before_action :verify_authenticity_token

  respond_to :json

  def index
    render 'application/index'
  end

  protected

  def configure_permitted_parameters
    added_attrs = [:username, :email, :password, :password_confirmation, :remember_me]
    devise_parameter_sanitizer.permit :sign_up, keys: added_attrs
    devise_parameter_sanitizer.permit :account_update, keys: added_attrs
  end
end 
```

我们在这里做了一些事情。首先，我们告诉 Rails】是我们的朋友；我们的*只有*观住在`views/application/index.html.erb`；接到 Devise 的电话时，不要担心真实性令牌；哦，我们的用户将有一个用户名。

接下来打开`app/controllers/users_controller.rb`,确保您可以通过任何`/users/:id.json`请求访问 JSON 格式的用户:

```
 class UsersController < ApplicationController
  def show
    user = User.find(params[:id])
    render json: user
  end  
end 
```

不用担心在`routes.rb`中设置`:show`资源。Devise 已经为我们做到了！

默认情况下，Rails 会用`views/layouts/application.html.erb`初始化，但是我们不希望这样(或者更确切地说，我不希望这样)，所以执行以下操作:

*   *把*那个文件移到`app/views/application/`。
*   重命名为`index.html.erb`。
*   将`<%= yield %>`替换为`<ui-view></ui-view>`(除了标题中的脚本/样式标签之外，我们不会呈现任何 erb)。
*   在脚本和样式表 erb 标签中删除任何有关“turoblinks”的内容。
*   将`ng-app="myApp"`作为属性添加到`<body>`标签中。当我们启动我们的服务器时，Angular 将在初始化我们的应用程序之前加载并疯狂地搜索我们的 DOM。

配置后端的最后一步是布置我们的资产管道。鲍尔已经在`vendor/assets/bower_components`为我们安装了一堆东西。同样，我们之前安装了一堆甜蜜的宝石。让我们确保我们的应用程序可以找到这些脚本和样式表:

在`app/assets/javascripts/application.js`中要求:

```
 //= require jquery
//= require jquery_ujs
//= require angular
//= require angular-ui-router
//= require angular-devise
//= require angular-rails-templates
//= require bootstrap-sprockets
//= require_tree . 
```

*注:别忘了去掉`require turbolinks`*

最后，我们必须将`app/assets/stylesheets/application.css`重命名为`application.scss`，并在样式表的末尾添加这两行`@import`:

```
 *
 *= require_tree .
 *= require_self
 */
@import "bootstrap-sprockets";
@import "bootstrap"; 
```

嘣！！现在我们已经设置好了一切，我们可以开始工作在我们的前端。

## 前端

下面是我们的 Angular 应用程序树的预览。由于我们安装了“angular-templates”gem，我们可以将所有的 HTML 文件和所有其他 Angular 文件保存在`assets/javascripts`目录中:

```
 /javascripts/controllers/AuthCtrl.js
/javascripts/controllers/HomeCtrl.js
/javascripts/controllers/NavCtrl.js
/javascripts/directives/NavDirective.js
/javascripts/views/home.html
/javascripts/views/login.html
/javascripts/views/register.html
/javascripts/views/nav.html
/javascripts/app.js
/javascripts/routes.js 
```

首先:让我们在`app.js`中声明我们的应用程序，并注入必要的依赖项:

```
 (function(){
  angular
    .module('myApp', ['ui.router', 'Devise', 'templates'])
}()) 
```

我在这里用生命，原因如下:

> 将 AngularJS 组件包装在一个立即调用的函数表达式(IIFE)中。这有助于防止变量和函数声明在全局范围内存活得比预期的长，这也有助于避免变量冲突。通过为每个文件提供可变的作用域，当您的代码被缩小并打包成单个文件以部署到生产服务器时，这变得更加重要。*—[Codestyle.co 角度指南](http://www.codestyle.co/Guidelines/angularjs)*

### Routes.js

接下来，我们将清除我们的`routes.js`文件。其中一些比我们现在所处的位置领先了一步，但我宁愿现在就把它解决掉，而不是回来:

```
 angular
  .module('myApp')
  .config(function($stateProvider, $urlRouterProvider){
    $stateProvider
      .state('home', {
        url: '/home',
        templateUrl: 'views/home.html',
        controller: 'HomeCtrl'
      })
      .state('login', {
        url: '/login',
        templateUrl: 'views/login.html',
        controller: 'AuthCtrl',
        onEnter: function(Auth, $state){
          Auth.currentUser().then(function(){
            $state.go('home')
          })
        }
      })
      .state('register', {
        url: '/register',
        templateUrl: 'views/register.html',
        controller: 'AuthCtrl',
        onEnter: function(Auth, $state){
          Auth.currentUser().then(function(){
            $state.go('home')
          })
        }
      })
    $urlRouterProvider.otherwise('/home')
  }) 
```

我们刚刚做的被称为我们的 angular 应用程序“myApp”，并调用配置函数，传入`$stateProvider`和`$routerUrlProvider`作为参数。我们可以立即调用`$stateProvider`并开始链接`.state()`方法，这些方法带有两个参数，州名(例如“home”)和一个描述州的数据对象，例如它的 URL、HTML 模板和使用哪个控制器。我们还使用了`$urlRouterProvider`来确保用户只能导航到我们预先设定的状态。

到目前为止，你可能还不熟悉的几个东西是`onEnter`、`$state`和`Auth`。我们稍后会谈到这一点。

现在，让我们构建我们的`home.html`和`HomeCtrl.js`:

```
 <div class="col-lg-8 col-lg-offset-2">
<h1>{{hello}}</h1>
<h3 ng-if="user">Welcome, {{user.username}}</h3>
</div> 
```

```
 angular
  .module('myApp')
  .controller('HomeCtrl', function($scope, $rootScope, Auth){
    $scope.hello = "Hello World"
  }) 
```

您可能想要注释登录/注册状态并运行`$ rails s`以确保一切正常。如果是的话，你会看到一个又大又漂亮的“你好世界”。如果它就在顶部中间，请深呼吸一口气，因为 Bootstrap 正在启动，而`col-lg`的东西正在很好地定位它，而不是卡在左上角。

Angular 所做的是搜索 DOM，找到属性`ng-app`，初始化“myApp ”,从我们的路由器默认导航到`/home`,找到`<ui-view>`指令，实例化我们的`HomeCtrl`,注入`$scope`对象，添加一个`hello`的键，给它赋值`"Hello World"`,然后在`<ui-view>`元素中用这个信息呈现`home.html`。一旦进入视图，Angular 就会扫描任何有意义的命令，如`{{...}}`绑定和`ng-if`指令，并根据需要呈现控制器的信息。我承认这些操作的顺序可能会稍有不同，但是您已经了解了其中的要点。

### 构建 AuthCtrl.js 和 login.html/register.html 文件

既然我们已经得到了所有这些幕后的信息，让我们构建我们的`AuthCtrl.js`和`login.html` / `register.html`文件:

```
 # login.html
<div class="col-lg-8 col-lg-offset-2">
  <h1 class="centered-text">Log In</h1>
  <form ng-submit="login()">
    <div class="form-group">
      <input type="email" class="form-control" placeholder="Email" ng-model="user.email" autofocus>
    </div>
    <div class="form-group">
      <input type="password" class="form-control" placeholder="Password" ng-model="user.password">
    </div>
    <input type="submit" class="btn btn-info" value="Log In">
  </form>
</div> 
```

```
 # register.html
<div class="col-lg-8 col-lg-offset-2">
  <h1 class="centered-text">Register</h1>
  <form ng-submit="register()">
    <div class="form-group">
      <input type="email" class="form-control" placeholder="Email" ng-model="user.email" autofocus>
    </div>
    <div class="form-group">
      <input type="username" class="form-control" placeholder="Username" ng-model="user.username" autofocus>
    </div>
    <div class="form-group">
      <input type="password" class="form-control" placeholder="Password" ng-model="user.password">
    </div>
    <input type="submit" class="btn btn-info" value="Log In">
  </form>
  <br>

  <div class="panel-footer">
    Already signed up? <a ui-sref="home.login">Log in here</a>.
  </div>
</div> 
```

在我用`AuthCtrl`让你不知所措之前，我只想指出你看到的大部分都是引导的 CSS 类，所以你会对它呈现的美丽印象深刻。忽略所有的类属性，其他的都应该很熟悉，比如`ng-submit`、`ng-model`和`ui-sref`，它们取代了我们通常的`href`锚标签属性。现在是授权控制…你准备好了吗？

```
 angular
  .module('myApp')
  .controller('AuthCtrl', function($scope, $rootScope, Auth, $state){
    var config = {headers: {'X-HTTP-Method-Override': 'POST'}}

    $scope.register = function(){
      Auth.register($scope.user, config).then(function(user){
        $rootScope.user = user
        alert("Thanks for signing up, " + user.username);
        $state.go('home');
      }, function(response){
        alert(response.data.error)
      });
    };

    $scope.login = function(){
      Auth.login($scope.user, config).then(function(user){
        $rootScope.user = user
        alert("You're all signed in, " + user.username);
        $state.go('home');
      }, function(response){
        alert(response.data.error)
      });
    }
  }) 
```

这段代码的大部分都来自于[Angular device 文档](https://github.com/cloudspace/angular_devise)，所以我就不赘述了。你现在需要知道的是，`Auth`是由`angular-device`创建的服务，它附带了一些相当牛逼的功能，比如`Auth.login(userParameters, config)`和`Auth.register(userParameters, config)`。这些创建了一个承诺，一旦解决，它返回登录的用户。

我承认我在这里做了一点手脚，把那个用户分配给了`$rootScope`。然而，更好的、更具可伸缩性的方法是创建一个用户服务，将用户存储在那里，然后将用户服务注入到任何需要用户的控制器中。为了简洁起见，我还使用了一个简单的`alert()`函数来代替集成的`ngMessages`或另一个类似于`ngFlash`的服务来发布错误或成功登录事件的通知。

其余的应该是不言自明的。`ng-submit`表单附加到这些`$scope`函数上，`$scope.user`从表单输入的`ng-model`中提取信息，`$state.go()`是一个用于重定向到另一个状态的漂亮函数。

如果你现在回到`routes.js`，所有的`onEnter`逻辑会更有意义。

## 将这一切结合在一起

我把最好的留到了最后，所以让我们构建一个别致的小`NavDirective.js`和`nav.html`来将所有东西集合在一起:

```
 angular
  .module('myApp')
  .directive('navBar', function NavBar(){
    return {
      templateUrl: 'views/nav.html',
      controller: 'NavCtrl'
    }
}) 
```

```
 <div class="col-lg-8 col-lg-offset-2">
  <ul class="nav navbar-nav" >
    <li><a ui-sref="home">Home</a></li>
    <li ng-hide="signedIn()"><a ui-sref="login">Login</a></li>
    <li ng-hide="signedIn()"><a ui-sref="register">Register</a></li>
    <li ng-show="signedIn()"><a ng-click="logout()">Log Out</a></li>
  </ul>
</div> 
```

而更健壮的`NavCtrl.js`:

```
 angular
  .module('myApp')
  .controller('NavCtrl', function($scope, Auth, $rootScope){
    $scope.signedIn = Auth.isAuthenticated;
    $scope.logout = Auth.logout;

    Auth.currentUser().then(function (user){
      $rootScope.user = user
    });

    $scope.$on('devise:new-registration', function (e, user){
      $rootScope.user = user
    });

    $scope.$on('devise:login', function (e, user){
      $rootScope.user = user
    });

    $scope.$on('devise:logout', function (e, user){
      alert("You have been logged out.")
      $rootScope.user = undefined
    });
  }) 
```

我们在这里所做的就是设置在导航链接中使用的函数，比如`ng-hide="signedIn()"`和`ng-click="logout()"`，并向`$scope`添加监听器，这样我们就可以在特定的`devise`事件发生时触发动作。我们还调用了`Auth.currentuser()`，这样当这个控制器被实例化时，我们可以仔细检查我们的`$rootScope.user`对象并显示正确的导航链接。

我们再找一下`app/views/application/index.html`，在`<ui-view>`上面一行加上`<nav-bar></nav-bar>`。由于这不依赖于任何路线，它将总是呈现在我们的主要内容之上。

现在就刷新你的页面吧。你难道不喜欢事情顺利进行吗？希望你在使用过时的包、Ruby 版本或类似的东西时不会遇到任何奇怪的问题。记住，谷歌是你最好的朋友。

总之，我希望这有所帮助！请在下面留下任何问题、评论或建议！

## 分享这篇文章