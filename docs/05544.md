# 在 Angular 应用中实现身份验证

> 原文：<https://www.sitepoint.com/implementing-authentication-angular-applications/>

身份验证和授权是几乎所有重要应用程序的重要组成部分。单页应用程序(spa)也不例外。应用程序可能不会向任何用户公开其所有数据和功能。用户可能需要验证自己才能看到应用程序的某些部分，或者对应用程序执行某些操作。为了识别应用程序中的用户，我们需要让用户登录。

在传统的服务器驱动的应用程序和单页面应用程序中，用户管理的实现方式是不同的。SPA 与其服务器组件交互的唯一方式是通过 AJAX。即使是登录和注销也是如此。

负责识别用户的服务器必须公开一个身份验证端点。SPA 会将用户输入的凭据发送到此端点进行验证。在典型的基于令牌的身份验证系统中，在验证凭证之后，服务可以用访问令牌或包含登录用户的名称和角色的对象来响应。客户端必须在向服务器发出的所有安全 API 请求中使用这个访问令牌。

由于访问令牌将被多次使用，因此最好将其存储在客户端。在 Angular 中，我们可以将值存储在服务或值中，因为它们是客户端的单例对象。但是，如果用户刷新页面，服务中的值或值将会丢失。在这种情况下，最好使用浏览器提供的持久性机制之一来存储令牌；最好是 *sessionStorage* ，因为一旦浏览器关闭就会被清除。

## 实现登录

现在让我们来看看一些代码。假设我们已经实现了所有的服务器端逻辑，并且服务在`/api/login`公开了一个 REST 端点来检查登录凭证并返回一个访问令牌。让我们编写一个简单的服务，通过点击身份验证端点来执行登录操作。我们将在稍后为此服务添加更多功能:

```
app.factory("authenticationSvc", function($http, $q, $window) {
  var userInfo;

  function login(userName, password) {
    var deferred = $q.defer();

    $http.post("/api/login", {
      userName: userName,
      password: password
    }).then(function(result) {
      userInfo = {
        accessToken: result.data.access_token,
        userName: result.data.userName
      };
      $window.sessionStorage["userInfo"] = JSON.stringify(userInfo);
      deferred.resolve(userInfo);
    }, function(error) {
      deferred.reject(error);
    });

    return deferred.promise;
  }

  return {
    login: login
  };
});
```

在实际代码中，您可能希望将存储数据到 sessionStorage 的语句重新分解到一个单独的服务中，因为如果我们这样做，该服务将承担多个责任。我把它留在同一个服务中，以保持演示简单。该服务可以由处理应用程序登录功能的控制器使用。

## 保护路线

我们可能在应用程序中有一组安全路由。如果用户没有登录并试图进入这些路线中的一个，用户应该被定向到登录页面。这可以通过使用路由选项中的`resolve`块来实现。下面的代码片段给出了实现的思路:

```
$routeProvider.when("/", {
  templateUrl: "templates/home.html",
  controller: "HomeController",
  resolve: {
    auth: ["$q", "authenticationSvc", function($q, authenticationSvc) {
      var userInfo = authenticationSvc.getUserInfo();

      if (userInfo) {
        return $q.when(userInfo);
      } else {
        return $q.reject({ authenticated: false });
      }
    }]
  }
});
```

`resolve`块可以包含多个语句块，这些语句块必须在完成时返回 promise 对象。只是澄清一下，上面定义的名字`auth`不是框架定义的；我定义了它。您可以根据用例将名称更改为任何名称。

通过或拒绝路线可能有多种原因。根据场景，您可以在解决/拒绝承诺时传递一个对象。我们还没有在服务中实现`getLoggedInUser()`方法。这是一个从服务返回`loggedInUser`对象的简单方法。

```
app.factory("authenticationSvc", function() {
  var userInfo;

  function getUserInfo() {
    return userInfo;
  }
});
```

上面片段中通过 promise 发送的对象是通过`$rootScope`广播的。如果路由被解析，事件`$routeChangeSuccess`被广播。然而，如果路由失败，事件`$routeChangeError`被广播。我们可以监听`$routeChangeError`事件，并将用户重定向到登录页面。由于事件是在`$rootScope`级别，最好在`run`块中为事件附加处理程序。

```
app.run(["$rootScope", "$location", function($rootScope, $location) {
  $rootScope.$on("$routeChangeSuccess", function(userInfo) {
    console.log(userInfo);
  });

  $rootScope.$on("$routeChangeError", function(event, current, previous, eventObj) {
    if (eventObj.authenticated === false) {
      $location.path("/login");
    }
  });
}]);
```

## 处理页面刷新

当用户点击页面上的刷新时，服务会丢失其状态。我们必须从浏览器的会话存储中获取数据，并将其赋给变量`loggedInUser`。由于工厂只被调用一次，我们可以在初始化函数中设置这个变量，如下所示。

```
function init() {
  if ($window.sessionStorage["userInfo"]) {
    userInfo = JSON.parse($window.sessionStorage["userInfo"]);
  }
}

init();
```

## 注销

当用户退出应用程序时，必须使用请求头中包含的访问令牌来调用相应的 API。一旦用户注销，我们也应该清除 sessionStorage 中的数据。以下示例包含必须添加到认证服务的注销功能。

```
function logout() {
  var deferred = $q.defer();

  $http({
    method: "POST",
    url: logoutUrl,
    headers: {
      "access_token": userInfo.accessToken
    }
  }).then(function(result) {
    $window.sessionStorage["userInfo"] = null;
    userInfo = null;
    deferred.resolve(result);
  }, function(error) {
    deferred.reject(error);
  });

  return deferred.promise;
}
```

## 结论

在单页面应用程序中实现身份验证的方法与传统的 web 应用程序大不相同。因为大部分工作是在客户端执行的，所以用户的状态也必须存储在客户端的某个地方。重要的是要记住，状态也应该在服务器上维护和验证，因为黑客可能会窃取存储在客户端系统上的数据。

本文的源代码可以在 [GitHub](https://github.com/jsprodotcom/source/blob/master/AngularJS-Authentication.zip) 下载。

## 分享这篇文章