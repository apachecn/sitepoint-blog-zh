# 使用 ES6 编写 AngularJS 应用程序

> 原文：<https://www.sitepoint.com/writing-angularjs-apps-using-es6/>

正如大家所知，ECMAScript 6 现在还处于草案阶段，预计将在今年的某个时候最终完成。但是它已经在社区中引起了很多关注，并且浏览器已经开始实现它。我们也有许多 transpilers，比如 Traceur、6to5 和许多其他将 ES6 代码转换成 ES5 兼容代码的工具。社区成员已经开始玩 ES6，他们中的许多人都在博客上写他们所学到的东西。SitePoint 的 JavaScript 频道也有[很多文章](https://www.sitepoint.com/?s=ECMAScript+6)描述了 ES6 的不同特性。

使用 ES6 可以编写任何日常的 JavaScript 代码。要做到这一点，我们需要了解 ES6 的关键特性，并知道哪一部分适合哪一部分。在本文中，我们将了解如何使用 ES6 的特性来构建 AngularJS 应用程序的不同部分，并使用 ES6 模块加载它们。我们将通过构建一个简单的在线书架应用程序来做到这一点，我们将看到它是如何构造和编写的。

与以往一样，这个应用程序的代码可以在我们的 GitHub 库中找到。

### 关于书架应用程序的说明

示例书架应用程序包含以下视图:

1.  **首页:**显示活动书籍列表。可以将图书标记为已读，并从该页面移至存档
2.  **添加图书页面:**通过接受书名和作者名，将一本新书添加到书架上。它不允许重复标题
3.  **存档页面:**列出所有存档的书籍

## 为 ES6 设置应用程序

因为我们将使用 ES6 来编写应用程序的前端部分，所以我们需要一个 transpiler 来使所有浏览器都能理解 ES6 的特性。我们将使用 [Traceur](https://github.com/google/traceur-compiler/wiki/Getting-Started) 客户端库动态编译我们的 ES6 脚本，并在浏览器中运行。这个图书馆在鲍尔街。示例代码在`bower.json`中有这个库的一个条目。

在应用程序的主页上，我们需要添加对此库的引用和以下脚本:

```
traceur.options.experimental = true;
new traceur.WebPageTranscoder(document.location.href).run(); 
```

该应用程序的 JavaScript 代码分为多个文件。使用 ES6 模块加载器将这些文件加载到主文件中。由于现在的浏览器无法理解 ES6 模块，Traceur polyfills 为我们补齐了这个特性。

在示例代码中，`bootstrap.js`文件负责加载主 AngularJS 模块并手动引导 Angular app。我们不能使用`ng-app`来引导应用程序，因为模块是异步加载的。这是该文件中包含的代码:

```
import { default as bookShelfModule} from './ES6/bookShelf.main';
angular.bootstrap(document, [bookShelfModule]); 
```

这里，`bookShelfModule`是包含所有片段的 AngularJS 模块的名称。我们稍后会看到`bookShelf.main.js`文件的内容。使用以下脚本标签将`bootstrap.js`文件加载到`index.html`文件中:

```
<script type="module" src="ES6/bootstrap.js"></script> 
```

## 定义控制器

AngularJS 控制器有两种定义方式:

1.  使用`$scope`的控制器
2.  使用*控制器作为*语法

第二种方法更适合 ES6，因为我们可以定义一个类并将其注册为控制器。与类的实例相关联的属性将通过控制器的别名可见。此外，作为语法的*控制器与`$scope`的耦合相对较少。如果你没有意识到，`$scope`将在 Angular 2 中从框架中移除，因此我们可以通过使用*控制器作为*语法，从现在开始训练我们的大脑减少对`$scope`的依赖。*

尽管 ES6 中的类让我们远离了处理原型的困难，但是它们不支持直接创建私有字段的方式。在 ES6 中创建私有字段有一些间接的方法。其中之一是在模块级使用变量存储值，而不将它们包含在导出对象中。

我们将使用一个 [WeakMap](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) 来存储私有字段。选择 WeakMap 的原因是，一旦对象被垃圾回收，那些以对象为键的条目就会被删除。

如上所述，应用程序的主页加载并显示活动书籍的列表。它依赖于一个服务来获取数据，将一本书标记为已读，或者将它移动到存档。我们将在下一节创建这个服务。为了使注入控制器构造函数的依赖项在实例方法中可用，我们需要将它们存储在 WeakMaps 中。主页的控制器有两个依赖项:执行 Ajax 操作的服务和`$timeout`(用于显示成功消息并在一定时间后隐藏它们)。我们还需要一个私有的`init`方法来在控制器加载时获取所有活动的书籍。所以，我们需要三个 WeakMaps。让我们将 WeakMaps 声明为常量，以防止任何意外的重新分配。

下面的代码片段创建了这些 WeakMaps 和类`HomeController`:

```
const INIT = new WeakMap();
const SERVICE = new WeakMap();
const TIMEOUT = new WeakMap();

class HomeController{
  constructor($timeout, bookShelfSvc){
    SERVICE.set(this, bookShelfSvc);
    TIMEOUT.set(this, $timeout);
    INIT.set(this, () => {
      SERVICE.get(this).getActiveBooks().then(books => {
        this.books = books;
      });
    });

    INIT.get(this)();
  }

  markBookAsRead(bookId, isBookRead){
    return SERVICE.get(this).markBookRead(bookId, isBookRead)
      .then(() => {
        INIT.get(this)();
        this.readSuccess = true;
        this.readSuccessMessage = isBookRead ? "Book marked as read." : "Book marked as unread.";
        TIMEOUT.get(this)(() => {
          this.readSuccess = false;
        }, 2500);
      });
  }

  addToArchive(bookId){
    return SERVICE.get(this).addToArchive(bookId)
      .then(() => {
        INIT.get(this)();
        this.archiveSuccess = true;
        TIMEOUT.get(this)(() => {
          this.archiveSuccess = false;
        }, 2500);
      });
  }
} 
```

上面的代码片段使用了以下 ES6 特性:

1.  如前所述，职业和武器地图
2.  注册回调的[箭头函数语法](https://www.sitepoint.com/javascript-arrow-functions/)。箭头函数内部的`this`引用与外部的`this`引用相同，后者是该类的当前实例
3.  不使用`function`关键字创建方法并将其附加到对象的新语法

让我们应用依赖注入并将该类注册为控制器:

```
HomeController.$inject = ['$timeout', 'bookShelfSvc'];
export default HomeController; 
```

如你所见，我们应用依赖注入的方式没有什么不同——这和我们在 ES5 中的方式是一样的。我们正在从这个模块中导出`HomeController`类。

检查`AddBookController`和`ArchiveController`的代码。它们遵循相似的结构。文件`bookShelf.controllers.js`导入这些控制器并将它们注册到一个模块中。这是该文件中的代码:

```
import HomeController from './HomeController';
import AddBookController from './AddBookController';
import ArchiveController from './ArchiveController';

var moduleName='bookShelf.controllers';

angular.module(moduleName, [])
  .controller('bookShelf.homeController', HomeController)
  .controller('bookShelf.addBookController', AddBookController)
  .controller('bookShelf.archiveController', ArchiveController);

export default moduleName; 
```

`bookShelf.controllers`模块导出它创建的 AngularJS 模块的名称，这样就可以导入到另一个要创建的模块中来创建主模块。

## 定义服务

“服务”在一般意义上和角度上都是一个超负荷的术语！使用的三种类型的服务是:*提供商*、*服务*和*工厂*。其中，提供者和服务被创建为类型的实例，所以我们可以为它们创建类。工厂是返回对象的函数。我能想到两种创建工厂的方法:

1.  和 ES5 一样，创建一个返回对象的函数
2.  具有静态方法的类，该方法返回同一类的实例。这个类将包含必须从工厂对象公开的字段

让我们使用第二种方法来定义工厂。该工厂负责与 Express API 交互，并向控制器提供数据。工厂依赖 Angular 的`$http`服务来执行 Ajax 操作。因为它必须是类中的私有字段，所以我们将为它定义一个 WeakMap。

以下代码片段创建工厂类并将静态方法注册为工厂:

```
var moduleName='bookShelf.services';

const HTTP = new WeakMap();

class BookShelfService
{
  constructor($http)
  {
    HTTP.set(this, $http);
  }

  getActiveBooks(){
    return HTTP.get(this).get('/api/activeBooks').then(result => result.data );
  }

  getArchivedBooks(){
    return HTTP.get(this).get('/api/archivedBooks').then(result => result.data );
  }

  markBookRead(bookId, isBookRead){
    return HTTP.get(this).put(`/api/markRead/${bookId}`, {bookId: bookId, read: isBookRead});
  }

  addToArchive(bookId){
    return HTTP.get(this).put(`/api/addToArchive/${bookId}`,{});
  }

  checkIfBookExists(title){
    return HTTP.get(this).get(`/api/bookExists/${title}`).then(result =>  result.data );
  }

  addBook(book){
    return HTTP.get(this).post('/api/books', book);
  }

  static bookShelfFactory($http){
    return new BookShelfService($http);
  }
}

BookShelfService.bookShelfFactory.$inject = ['$http'];

angular.module(moduleName, [])
  .factory('bookShelfSvc', BookShelfService.bookShelfFactory);

export default moduleName; 
```

这个代码片段使用了 ES6 的以下附加特性(除了类和箭头函数之外):

1.  类中的静态成员
2.  将变量值连接成字符串的字符串模板

## 定义指令

定义一个指令类似于定义一个工厂，只有一个例外——我们必须在`link`函数中提供该指令的一个实例供以后使用，因为`link`函数不是在指令对象的上下文中调用的。这意味着`link`函数中的`this`引用与指令对象不同。我们可以通过静态字段使对象可用。

我们将创建一个属性指令来验证文本框中输入的书名。它必须调用一个 API 来检查标题是否已经存在，如果找到了标题，就使字段无效。对于这个任务，它需要我们在上一节中创建的服务和`$q` for promises。

下面的代码片段创建了一个向模块注册的指令。

```
var moduleName='bookShelf.directives';

const Q = new WeakMap();
const SERVICE = new WeakMap();

class UniqueBookTitle
{
  constructor($q, bookShelfSvc){
    this.require='ngModel';  //Properties of DDO have to be attached to the instance through this reference
    this.restrict='A';

    Q.set(this, $q);
    SERVICE.set(this, bookShelfSvc);
  }

  link(scope, elem, attrs, ngModelController){
    ngModelController.$asyncValidators.uniqueBookTitle = function(value){

      return Q.get(UniqueBookTitle.instance)((resolve, reject) => {
        SERVICE.get(UniqueBookTitle.instance).checkIfBookExists(value).then( result => {
          if(result){
            reject();
          }
          else{
            resolve();
          }
        });
      });
    };
  }

  static directiveFactory($q, bookShelfSvc){
    UniqueBookTitle.instance =new UniqueBookTitle($q, bookShelfSvc);
    return UniqueBookTitle.instance;
  }
}

UniqueBookTitle.directiveFactory.$inject = ['$q', 'bookShelfSvc'];

angular.module(moduleName, [])
  .directive('uniqueBookTitle', UniqueBookTitle.directiveFactory);

export default moduleName; 
```

在这里，我们可以使用 ES6 的 promise API，但是这将涉及到在 promise 产生结果后调用`$rootScope.$apply`。好消息是 AngularJS 1.3 中的 [promise API 支持类似于 ES6 promises](https://docs.angularjs.org/api/ng/service/%24q) 的语法。

## 定义主模块和配置块

现在我们有了包含指令、控制器和服务的模块，让我们将它们加载到一个文件中，并创建应用程序的主模块。让我们从导入模块开始。

```
import { default as controllersModuleName } from './bookShelf.controllers';
import { default as servicesModuleName } from './bookShelf.services';
import { default as directivesModuleName } from './bookShelf.directives'; 
```

配置块定义了应用程序的路由。这可以是一个简单的函数，因为它不需要返回任何值。

```
function config($routeProvider){
  $routeProvider
    .when('/',{
      templateUrl:'templates/home.html',
      controller:'bookShelf.homeController',
      controllerAs:'vm'
    })
    .when('/addBook',{
      templateUrl:'templates/addBook.html',
      controller:'bookShelf.addBookController',
      controllerAs:'vm'
    })
    .when('/archive', {
      templateUrl:'templates/archive.html',
      controller:'bookShelf.archiveController',
      controllerAs:'vm'
    })
    .otherwise({redirectTo:'/'});
}

config.$inject = ['$routeProvider']; 
```

最后，让我们定义主模块并导出它的名称。如果你还记得的话，这个名字在`bootstrap.js`文件中用于手动引导。

```
var moduleName = 'bookShelf';

var app = angular.module(moduleName, ['ngRoute','ngMessages', servicesModuleName, controllersModuleName, directivesModuleName])
  .config(config);

export default moduleName; 
```

## 结论

希望这能让你对使用 ES6 编写 AngularJS 应用有所了解。AngularJS 2.0 是完全使用 ES6 编写的，作为 web 开发人员，我们需要了解在不久的将来我们必须如何编写代码。ES6 解决了多年来困扰 JavaScript 程序员的许多问题，将它与 AngularJS 一起使用非常有趣！

请记住，这个应用程序的示例代码可以在我们的 GitHub 库上找到。

## 分享这篇文章