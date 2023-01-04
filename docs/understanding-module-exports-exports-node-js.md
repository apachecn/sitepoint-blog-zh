# 了解 module.exports 和 Node.js 中的导出

> 原文：<https://www.sitepoint.com/understanding-module-exports-exports-node-js/>

在编程中，模块是独立的功能单元，可以跨项目共享和重用。它们使我们作为开发人员的生活变得更容易，因为我们可以用它们来为我们的应用程序增加我们自己不必编写的功能。它们还允许我们组织和分离我们的代码，使应用程序更容易理解、调试和维护。

在本文中，我将研究如何使用 Node.js 中的模块，重点是如何导出和使用它们。

## 不同的模块格式

由于 JavaScript 最初没有模块的概念，随着时间的推移，出现了各种各样的竞争格式。以下是需要注意的一些主要问题:

*   [异步模块定义(AMD)](https://en.wikipedia.org/wiki/Asynchronous_module_definition) 格式用于浏览器，并使用`define`函数来定义模块。
*   Node.js 中使用了 [CommonJS (CJS)](https://en.wikipedia.org/wiki/CommonJS) 格式，并使用`require`和`module.exports`来定义依赖项和模块。npm 生态系统就是建立在这种模式之上的。
*   [ES 模块](https://www.sitepoint.com/understanding-es6-modules/)格式。从 ES6 (ES2015)开始，JavaScript 支持原生模块格式。它使用一个`export`关键字导出一个模块的公共 API，使用一个`import`关键字导入它。
*   [System.register](https://github.com/systemjs/systemjs/blob/master/docs/system-register.md) 格式被设计成支持 ES5 中的 ES6 模块。
*   [通用模块定义(UMD)](https://riptutorial.com/javascript/example/16339/universal-module-definition--umd-) 格式既可以在浏览器中使用，也可以在 Node.js 中使用。当一个模块需要由多个不同的模块加载器导入时，这很有用。

请注意，这篇文章只涉及 Node.js 中的标准, **CommonJS 格式**。如果你想了解任何其他格式，我推荐 SitePoint 作者尤尔根·范·德·摩尔的[这篇文章](https://www.jvandemo.com/a-10-minute-primer-to-javascript-modules-module-formats-module-loaders-and-module-bundlers/)。

## 需要模块

Node.js 附带了一组内置模块，我们可以在代码中使用它们，而无需安装它们。为此，我们需要使用`require`关键字要求模块，并将结果赋给一个变量。这可以用来调用模块公开的任何方法。

例如，要列出一个目录的内容，可以使用[文件系统模块](https://nodejs.org/api/fs.html)及其`readdir`方法:

```
const fs = require('fs');
const folderPath = '/home/jim/Desktop/';

fs.readdir(folderPath, (err, files) => {
  files.forEach(file => {
    console.log(file);
  });
}); 
```

注意，在 CommonJS 中，模块是同步加载的，并按照它们出现的顺序进行处理。

## 创建和导出模块

现在让我们看看如何创建我们自己的模块，并将其导出以供程序中的其他地方使用。首先创建一个`user.js`文件并添加以下内容:

```
const getName = () => {
  return 'Jim';
};

exports.getName = getName; 
```

现在在同一个文件夹中创建一个`index.js`文件，并添加如下内容:

```
const user = require('./user');
console.log(`User: ${user.getName()}`); 
```

使用`node index.js`运行程序，您应该会在终端上看到以下输出:

```
User: Jim 
```

那么这里发生了什么？嗯，如果你看一下`user.js`文件，你会注意到我们正在定义一个`getName`函数，然后使用`exports`关键字使它可以在其他地方导入。然后在`index.js`文件中，我们导入并执行这个函数。还要注意在`require`语句中，模块名以`./`为前缀，因为它是一个本地文件。还要注意，没有必要添加文件扩展名。

### 导出多个方法和值

我们可以用同样的方式导出多个方法和值:

```
const getName = () => {
  return 'Jim';
};

const getLocation = () => {
  return 'Munich';
};

const dateOfBirth = '12.01.1982';

exports.getName = getName;
exports.getLocation = getLocation;
exports.dob = dateOfBirth; 
```

而在`index.js`:

```
const user = require('./user');
console.log(
  `${user.getName()} lives in ${user.getLocation()} and was born on ${user.dob}.`
); 
```

上面的代码产生了这样的结果:

```
Jim lives in Munich and was born on 12.01.1982. 
```

注意我们给导出的`dateOfBirth`变量起的名字可以是我们喜欢的任何名字(在这个例子中是`dob`)。它不必与原始变量名相同。

### 语法的变化

我还应该提到，可以在进行过程中导出方法和值，而不仅仅是在文件末尾。

例如:

```
exports.getName = () => {
  return 'Jim';
};

exports.getLocation = () => {
  return 'Munich';
};

exports.dob = '12.01.1982'; 
```

多亏了[析构赋值](https://www.sitepoint.com/es6-destructuring-assignment/)，我们可以挑选我们想要导入的内容:

```
const { getName, dob } = require('./user');
console.log(
  `${getName()} was born on ${dob}.`
); 
```

如您所料，该日志记录了:

```
Jim was born on 12.01.1982. 
```

## 导出默认值

在上面的例子中，我们分别导出函数和值。这对于整个应用程序都需要的助手函数来说很方便，但是当你有一个模块只导出一样东西时，更常见的是使用`module.exports`:

```
class User {
  constructor(name, age, email) {
    this.name = name;
    this.age = age;
    this.email = email;
  }

  getUserStats() {
    return ` Name: ${this.name} Age: ${this.age} Email: ${this.email}  `;
  }
}

module.exports = User; 
```

而在`index.js`:

```
const User = require('./user');
const jim = new User('Jim', 37, 'jim@example.com');

console.log(jim.getUserStats()); 
```

上面的代码记录了这一点:

```
Name: Jim
Age: 37
Email: jim@example.com 
```

## `module.exports`和`exports`有什么区别？

在网上旅行时，您可能会遇到以下语法:

```
module.exports = {
  getName: () => {
    return 'Jim';
  },

  getLocation: () => {
    return 'Munich';
  },

  dob: '12.01.1982',
}; 
```

在这里，我们将我们想要导出的函数和值赋给`module`上的一个`exports`属性——当然，这也很好:

```
const { getName, dob } = require('./user');
console.log(
  `${getName()} was born on ${dob}.`
); 
```

这将记录以下内容:

```
Jim was born on 12.01.1982. 
```

那么什么是*`module.exports``exports`的区别呢？一个只是另一个的别名吗？*

 *嗯，有点，但不完全是…

为了说明我的意思，让我们更改`index.js`中的代码来记录`module`的值:

```
console.log(module); 
```

这会产生:

```
Module {
  id: '.',
  exports: {},
  parent: null,
  filename: '/home/jim/Desktop/index.js',
  loaded: false,
  children: [],
  paths:
   [ '/home/jim/Desktop/node_modules',
     '/home/jim/node_modules',
     '/home/node_modules',
     '/node_modules' ] } 
```

如您所见，`module`有一个`exports`属性。让我们给它加点东西:

```
// index.js
exports.foo = 'foo';
console.log(module); 
```

这将输出:

```
Module {
  id: '.',
  exports: { foo: 'foo' },
  ... 
```

将属性分配给`exports`也会将它们添加到`module.exports`。这是因为(至少最初)`exports`是对`module.exports`的引用。

### 那么我应该用哪一个呢？

由于`module.exports`和`exports`都指向同一个对象，通常使用哪个并不重要。例如:

```
exports.foo = 'foo';
module.exports.bar = 'bar'; 
```

这段代码将导致模块的导出对象为`{ foo: 'foo', bar: 'bar' }`。

然而，有一个警告。无论你给什么赋值，都是从你的模块中导出的。

所以，采取以下措施:

```
exports.foo = 'foo';
module.exports = () => { console.log('bar'); }; 
```

这只会导致匿名函数被导出。`foo`变量将被忽略。

如果你想了解更多的不同，我推荐[这篇文章](https://www.hacksparrow.com/nodejs/exports-vs-module-exports.html)。

## 结论

模块已经成为 JavaScript 生态系统不可或缺的一部分，允许我们用更小的部分组成更大的程序。我希望这篇文章很好地介绍了如何在 Node.js 中使用它们，并有助于解开它们的语法之谜。

如果您有任何问题或意见，请随时跳到 [SitePoint 论坛](https://www.sitepoint.com/community/)开始讨论。

## 分享这篇文章*