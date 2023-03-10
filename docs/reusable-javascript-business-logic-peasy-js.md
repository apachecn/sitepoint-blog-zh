# 用 peasy-js 编写可重用的 JavaScript 业务逻辑

> 原文：<https://www.sitepoint.com/reusable-javascript-business-logic-peasy-js/>

用 peasy-js 编写可重用的 JavaScript 业务逻辑由 Stephan Max 进行同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

![A tablet, a laptop, and a cellphone connect to a lit light bulb via wires](img/93ef43c58d0d491589bb80eda0c6cb78.png)

在编写应用程序时，我们经常将有价值的业务逻辑与框架特定的代码结合起来。例如，当使用 [Angular](https://angularjs.org/) 时，通常将业务逻辑分散在服务、控制器甚至指令中。

这也适用于为后端编写的 JavaScript，其中通常会将我们的[帆](http://sailsjs.org/)(例如)控制器动作与业务逻辑混杂在一起，这些业务逻辑通过 ORM/ODM 直接消耗我们的数据访问逻辑，例如[mongose](http://mongoosejs.com/)，以及其他[横切](https://en.wikipedia.org/wiki/Cross-cutting_concern)关注点。

这种耦合通常会导致代码难以重用、伸缩、测试以及采用或迁移到新技术。

在本文中，我将向您展示如何使用 peasy-js 库来帮助构建您的业务逻辑，使其在应用程序的前端和后端部分之间高度可重用，并在不同框架之间轻松移植。

> **披露**:我是 peasy-js 的作者

## 我们应该停止使用框架吗？

相反，我认为这些框架在客户端和服务器端都提供了巨大的好处。然而，我所建议的是，通过创建对其消费者完全不可知的代码，将我们的业务逻辑抽象成可组合的单元。

通过组件化我们的业务逻辑，我们可以使用任何 JavaScript 客户机、服务器、数据访问技术和可以想象的框架，在任何应用程序架构中轻松地测试、交换、重新安排、重用和使用这些组件。

## 分离您的业务逻辑

peasy-js 是一个中间层框架，它通过以可组合、可重用、可伸缩和可测试的方式创建业务逻辑，使得在我们的应用程序中随意更换 UI、后端和数据访问框架变得很简单。换句话说，peasy-js 通过编写遵循[关注点分离(SoC)](https://en.wikipedia.org/wiki/Separation_of_concerns) 的代码，提供了将我们的业务逻辑抽象成可组合单元的指导。

## 框架疲劳

等等，先别走！

我知道你在想什么，“啊，另一个框架？”。没错，peasy-js 确实是一个微框架。然而，如果我们冒险沿着组件化我们的业务逻辑的道路走下去，无论如何，我们最终都会编写自己的微观框架。

无数的时间被用于 peasy-js 的设计、开发和测试，支持几乎任何可以想象的工作流程。由于进入门槛较低，我希望你会发现在学习上的小投资是非常值得的。

但是，如果您发现 peasy-js 不太适合您，那么希望您能够了解如何使用框架中的一些模式来实现自己的业务层。

## 主要概念

让我们看看 peasy-js 为我们提供了什么:

*   易于使用且灵活的业务和验证规则引擎
*   可伸缩性和可重用性(将业务和验证逻辑从消费代码和框架中分离出来)
*   易测试性

peasy-js 包含四个主要概念。每一个都有一个简短的描述，在整篇文章中会有更深入的介绍。

### 商业服务

一个 [BusinessService](https://github.com/peasy/peasy-js/wiki/BusinessService) 实现代表一个实体(例如用户或项目),负责通过命令公开业务功能。这些命令封装了 CRUD 和其他与业务相关的功能。

### 命令

[命令](https://github.com/peasy/peasy-js/wiki/Command)负责协调初始化逻辑、验证和业务规则执行以及其他逻辑(数据代理调用、工作流逻辑等)的执行。)，分别通过[命令执行流水线](https://github.com/peasy/peasy-js/wiki/Command#command-execution-pipeline)。

### 规则

可以创建一个[规则](https://github.com/peasy/peasy-js/wiki/Business-and-Validation-Rules)来表示验证规则(字段长度或必填)或业务规则(授权、价格有效性等)。).规则由命令使用，可以被链接，被配置为基于先前规则的执行来执行，等等。规则也可以被配置为基于它们的执行结果来运行代码。

### DataProxy

[DataProxy](https://github.com/peasy/peasy-js/wiki/Data-Proxy) 负责数据存储和检索，并作为数据存储的抽象层，包括(但不限于)以下内容:

*   关系数据库——SQLite、MySQL、Oracle、SQL Server 等。
*   文档(NoSQL)数据库——MongoDB、VelocityDB 等。
*   服务–HTTP、SOAP 等。
*   cache stores-redis、Azure 等。
*   队列 rabbitmq、MSMQ 等。
*   文件系统
*   用于测试的内存数据存储

## 示例:Peasy-js 在运行

> **注意:**在 plnkr 上可以看到一个简单的浏览器示例[，它涵盖了本节讨论的所有内容。](http://plnkr.co/edit/IszynC?p=info)

下面是在客户端的 Angular 服务中使用 peasy-js 编写的业务逻辑的一个示例:

**图一**

```
var dataProxy = new CustomerHttpDataProxy();
var service = new CustomerService(dataProxy);
var customer = { name:  "Frank Zappa", birthDate: new Date('12/21/1940') };
var command = service.insertCommand(customer);

command.execute(function(err, result) {
  if (result.success) {
    customer = result.value;
  } else {
    console.log(result.errors);
  }
}); 
```

现在让我们看一个在服务器上的 [Express.js](http://expressjs.com/) 控制器中使用相同业务逻辑的例子:

**图 B**

```
var dataProxy = new CustomerMongoDataProxy();
var service = new CustomerService(dataProxy);
var customer = { name:  "Frank Zappa", birthDate: new Date('12/21/1940') };
var command = service.insertCommand(customer);

command.execute(function(err, result) {
  if (result.success) {
    customer = result.value;
  } else {
    console.log(result.errors);
  }
}); 
```

注意到不同了吗？美妙的是，除了在每个示例中注入到业务服务中的不同数据代理之外，没有任何区别。

请记住，数据代理是我们的数据访问抽象，可以表示文件系统访问、数据库、队列、缓存、内存和 HTTP 通信的具体实现。

这种抽象允许我们基于期望的系统架构和配置来交换数据代理，同时加强 SoC 并使其能够跨代码库重用，并简化测试。可能不会立即显而易见的是，这种方法总是让我们的有效负载服从相同的业务逻辑，而不管我们的数据的来源或目的地。这一切很快就会揭晓。

从消费的角度来看，这真的是全部。使用我们用 peasy-js 开发的业务逻辑将引入一个可识别的主题，不管我们的架构和使用它的技术如何。

说到架构，让我们将注意力转向一种潜在的架构，当以这种方式开发我们的业务逻辑时，这种架构变得容易实现，同时更深入地探索 peasy-js 参与者:

![Sample architecture showing how peasy-js allows you to share business logic between the client and the server](img/24dfc12c0f6936c9773b65cb395b3aff.png)

从左到右，我们看到一个客户端应用程序使用一个框架，如 Angular、React、Backbone 等。为了实现最大的可伸缩性，请注意，我们可以将业务逻辑实现从 UI 框架参与者实现(服务、控制器等)中移出。)到它自己的组件化代码库，或中间层。

接下来，注意中间层与 web 服务器通信。数据代理的出现使这成为可能。参考图 A，使用我们的业务逻辑的角度服务实例化了一个`CustomerHttpDataProxy`。因此，当执行 insert 命令时，它会使提供的有效负载服从任何已配置的业务规则。如果验证成功，我们的数据代理的相应的`insert`函数将被调用，并相应地针对我们配置的客户端点发出 POST 请求。

相反，请注意，我们的 node.js 应用程序也使用我们前端使用的相同业务逻辑。参考图 B，使用我们的业务逻辑的 express 控制器实例化了一个`CustomerMongoDataProxy`。然而，这一次当执行 insert 命令时，我们的数据代理的相应的`insert`函数将使用 MongoDB API 或一个 ORD(比如 Mongoose)对我们的数据库执行插入。

最后，因为我们的数据代理实现遵循相同的接口，所以我们可以根据我们希望如何部署应用程序，将它们注入到我们的业务服务中。在该图中，业务服务使用数据代理，这些代理与客户端上的 HTTP 服务进行交互。但是，一旦 web API 处理了请求，Node.js 托管的相同业务服务就会被注入与数据库、队列、缓存、文件系统等交互的数据代理。

既然我们已经从高层次上理解了 peasy-js 参与者以及它们提供的一些好处，那么让我们来看一下它们的示例实现。

### CustomerHttpDataProxy

```
var CustomerHttpDataProxy = function() {

  var request = require('request');

  return {
    insert: insert
  };

  function insert(data, done) {
    request({
      method: 'POST',
      url: 'http://localhost:3000/customers',
      body: data,
      json = true
    }, function (error, response, body) {
        done(error, body);
      }
    );
  };
}; 
```

### 客户 MongoDataProxy

```
var CustomerMongoDataProxy = function() {

  var connectionString = 'mongodb://localhost:12345/orderEntry';
  var mongodb = require('mongodb').MongoClient;

  return {
    insert: insert
  };

  function insert(data, done) {
    mongodb.connect(connectionString, function(err, db) {
      if (err) { return done(err); }
      var collection = db.collection('customers');
      collection.insert(data, function(err, data) {
        db.close();
        done(err, data);
      });
    });
  };

}; 
```

在这些数据代理代码示例中，请注意它们遵循相同的接口，但是抽象了实现逻辑。这使得我们能够扩展我们的应用程序。我们可以看到，通过交换数据代理，我们现在有了一个真正可重用的中间层，它完全不知道任何消费代码(客户端或服务器)。这个数据代理设计概念是实现可伸缩性和易测试性的关键。

最后，请注意，为了简洁起见，我们只在数据代理中定义了一个插入函数。然而，在真实的生产环境中，我们最有可能公开所有的 CRUD 操作，甚至更多。你可以在这里看到 CustomerMongoDataProxy 的完整实现。

### 客户服务

```
var CustomerService = BusinessService.extend({
  functions: {
    _onInsertCommandInitialization: function(context, done) {
      var customer = this.data;
      utils.stripAllFieldsFrom(customer).except(['name', 'address']);
      utils.stripAllFieldsFrom(customer.address).except(['street', 'zip']);
      done();
    }
  }
}).service; 
```

在这个例子中，我们已经为 CustomerService 公开的 [insertCommand](https://github.com/peasy/peasy-js/wiki/BusinessService#insertcommanddata) 提供了初始化逻辑，它在调用我们的数据代理的`insert`函数之前将字段列入白名单。通过我们的业务服务实现公开的每个默认 CRUD 操作都会公开与每个命令相关联的事件挂钩。这些方法可以在这里查看[。](https://github.com/peasy/peasy-js/wiki/BusinessService#public-functions-instance)

注意，我们使用了静态的`BusinessService.extend`函数，它创建了一个构造函数，通过返回对象的服务成员公开。如果您对这些方法更熟悉，也可以自由使用 ES6 继承或原型继承。两者的样本都可以在[这里](https://github.com/peasy/peasy-js/wiki/BusinessService#creating-a-business-service)找到。

既然我们已经为业务服务的`insertCommand`定义了初始化逻辑，那么让我们创建几个规则并相应地连接它们:

**命名规则**

```
var NameRule = Rule.extend({
  association: "name",
  params: ['name'],
  functions: {
    _onValidate: function(done) {
      if (this.name === "Jimi") {
        this._invalidate("Name cannot be Jimi");
      }
      done();
    }
  }
}); 
```

**图层规则**

```
var AgeRule = Rule.extend({
  association: "age",
  params: ['birthdate'],
  functions: {
    _onValidate: function(done) {
      if (new Date().getFullYear() - this.birthdate.getFullYear() < 50) {
        this._invalidate("You are too young");
      }
      done();
    }
  }
}); 
```

注意，我们在两个代码示例中都使用了静态的`Rule.extend`方法，它为我们创建了一个构造函数。和以前一样，你也可以使用 ES6 或者原型继承(例子[这里](https://github.com/peasy/peasy-js/wiki/Business-and-Validation-Rules#creating-a-rule))。

现在，让我们在客户服务中将它们连接起来:

### 连接我们的规则

```
var CustomerService = BusinessService.extend({
  functions: {
    _onInsertCommandInitialization: function(context, done) {
      var customer = this.data;
      utils.stripAllFieldsFrom(customer).except(['name', 'address']);
      utils.stripAllFieldsFrom(customer.address).except(['street', 'zip']);
      done();
    },
    _getRulesForInsertCommand: function(context, done) {
      var customer = this.data;
      done(null, [
        new NameRule("name", customer.name),
        new AgeRule("age", customer.birthDate)
      ]);
    }
  }
}).service; 
```

在最后一段代码中，我们在业务服务中连接了规则，并将它们注入到插入命令执行管道中。我们已经通过提供`_getRulesForInsertCommand()`函数的实现做到了这一点。

在这个示例中，我们将两个规则都配置为不管彼此的结果如何都执行。例如，如果 NameRule 验证失败，仍然会对 AgeRule 进行评估，反之亦然。

peasy-js 规则的伟大之处在于它们非常灵活，可以编写和配置来支持几乎任何可以想象的场景。例如，我们可以将规则的执行链接起来，只在 NameRule 验证成功的情况下执行 AgeRule，反之亦然。当我们的规则需要从数据存储中获取数据时，这是非常有用的(一个潜在的高开销)。

关于规则的更多信息可以在文档中找到[。](https://github.com/peasy/peasy-js/wiki/Business-and-Validation-Rules)

### 测试我们的业务逻辑

因为 peasy-js 坚持[坚实的](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design))编程原则，所以测试我们的业务服务、命令和规则变得非常容易。

让我们看看如何轻松测试我们的`NameRule`:

```
it("fails when the supplied name is Jimi", () => {
  var rule = new NameRule("Jimi");
  rule.validate(() => {
    expect(rule.valid).toBe(false);
    expect(rule.association).toEqual("name");
  });
});

it("succeeds when the supplied name is not Jimi", () => {
  var rule = new NameRule("James");
  rule.validate(() => {
    expect(rule.valid).toBe(true);
  });
}); 
```

通过保持我们的规则简单和集中，它们不仅变得易于重用，而且非常易于测试。这也适用于测试我们的业务服务和定制命令。

测试本身就是一个很大的话题，所以这是本文的一个很好的结尾。请注意，用 peasy-js 测试我们的业务逻辑非常容易，许多测试样本可以在这里找到。

## 想了解更多？

一个完整的订单输入/库存管理[示例应用程序](https://github.com/peasy/peasy-js-samples)可用，展示了用 peasy-js 编写的中间层。业务逻辑由 Node.js 中托管的 Express.js 应用程序使用，该应用程序公开了一个 web API。该示例易于运行，并附有帮助您在几分钟内开始运行的文档。

peasy-js 鼓励我们编写与我们使用的框架完全分离的业务逻辑。一个有益的副作用是，它使得以多种方式部署我们的代码变得容易。最后，随着我们当前框架的老化，迁移或采用新框架变得几乎无关紧要。

你的应用程序中有类似的模式吗？你认为使用像 peasy-js 这样的东西会帮助你写出更好的代码吗？请在下面的评论中告诉我你的想法！

## 分享这篇文章