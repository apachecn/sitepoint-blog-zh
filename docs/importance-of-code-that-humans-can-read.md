# 编写人类可读代码的重要性

> 原文：<https://www.sitepoint.com/importance-of-code-that-humans-can-read/>

*这篇文章由[马特·伯内特](https://www.sitepoint.com/author/mburnett/)、[西蒙·科灵顿](https://www.sitepoint.com/author/scodrington/)和[尼尔森·雅克](https://twitter.com/nilsonjacques)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

你曾经在一次运行中完成一个项目而不需要再看一遍代码吗？我也没有。当在一个老的项目中工作时，你可能想花很少甚至没有时间去弄清楚代码是如何工作的。可读的代码对于保持产品的可维护性和让你自己以及你的同事或合作者开心是必不可少的。

不可读代码的夸张例子可以在 [JS1k](http://js1k.com/) 比赛中找到，比赛的目标是用 1024 个字符或更少的字符编写最好的 JavaScript 应用程序，以及 [JSF*ck](http://www.jsfuck.com/) (顺便说一下，NSFW)，这是一种深奥的编程风格，只使用 6 个不同的字符来编写 JavaScript 代码。查看这些网站上的代码会让你想知道发生了什么。想象一下，编写这样的代码并试图在几个月后修复一个 bug。

如果你经常在网上冲浪，或者构建界面，你可能知道退出一个大而笨重的表单比退出一个看起来简单而小巧的表单更容易。代码也是如此。当被认为更容易阅读和处理时，人们可能会更喜欢处理它。至少它会让你不必沮丧地扔掉你的电脑。

在这篇文章中，我将介绍一些技巧和窍门，让您的代码更具可读性，以及要避免的陷阱。

## 代码拆分

坚持用表单做类比，表单有时被分成几部分，使它们看起来不那么困难。代码也可以做到这一点。通过把它分成几个部分，读者可以跳到与他们相关的部分，而不是在丛林中翻来翻去。

### 跨文件

多年来，我们一直在为网络进行优化。JavaScript 文件也不例外。想想缩小和 pre- [HTTP/2](https://www.sitepoint.com/http2-the-pros-the-cons-and-what-you-need-to-know/) ，我们通过将脚本合并成一个脚本来保存 HTTP 请求。今天，我们可以随心所欲地工作，让像[大口](http://gulpjs.com/)或[咕噜](http://gruntjs.com/)这样的任务运行者处理我们的文件。可以肯定地说，我们可以按照自己喜欢的方式编程，而将优化(比如连接)留给工具。

```
// Load user data from API
var getUsersRequest = new XMLHttpRequest();
getUsersRequest.open('GET', '/api/users', true);
getUsersRequest.addEventListener('load', function() {
    // Do something with users
});

getUsersRequest.send();

//---------------------------------------------------
// Different functionality starts here. Perhaps
// this is an opportunity to split into files.
//---------------------------------------------------

// Load post data from API
var getPostsRequest = new XMLHttpRequest();
getPostsRequest.open('GET', '/api/posts', true);
getPostsRequest.addEventListener('load', function() {
    // Do something with posts
});

getPostsRequest.send(); 
```

### 功能

函数允许我们创建可以重用的代码块。通常，函数的内容是缩进的，这样很容易看到函数的开始和结束。一个好习惯是保持函数简洁——10 行或更少。当一个函数被正确命名时，也很容易理解它被调用时发生了什么。我们稍后将讨论命名约定。

```
// Load user data from API
function getUsers(callback) {
    var getUsersRequest = new XMLHttpRequest();
    getUsersRequest.open('GET', '/api/users', true);
    getUsersRequest.addEventListener('load', function() {
        callback(JSON.parse(getUsersRequest.responseText));
    });

    getUsersRequest.send();
}

// Load post data from API
function getPosts(callback) {
    var getPostsRequest = new XMLHttpRequest();
    getPostsRequest.open('GET', '/api/posts', true);
    getPostsRequest.addEventListener('load', function() {
        callback(JSON.parse(getPostsRequest.responseText));
    });

    getPostsRequest.send();
}

// Because of proper naming, it’s easy to understand this code 
// without reading the actual functions
getUsers(function(users) {
    // Do something with users
});
getPosts(function(posts) {
    // Do something with posts
}); 
```

我们可以简化上面的代码。注意这两个函数几乎是一样的？我们可以应用[不要重复自己](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)(干)原则。这可以防止混乱。

```
function fetchJson(url, callback) {
    var request = new XMLHttpRequest();
    request.open('GET', url, true);
    request.addEventListener('load', function() {
        callback(JSON.parse(request.responseText));
    });

    request.send();
}

// The below code is still easy to understand 
// without reading the above function
fetchJson('/api/users', function(users) {
    // Do something with users
});
fetchJson('/api/posts', function(posts) {
    // Do something with posts
}); 
```

如果我们想通过 POST 请求创建一个新用户呢？此时，一种选择是向函数添加可选参数，向函数引入新的逻辑，使其对于一个函数来说过于复杂。另一种选择是专门为 POST 请求创建一个新函数，这将导致重复代码。

我们可以利用面向对象编程来获得这两者的优势，允许我们创建一个可配置的一次性对象，同时保持它的可维护性。

**注**:如果你需要一本关于面向对象 JavaScript 的入门书，我推荐这个视频:[面向对象 JavaScript 权威指南](https://www.youtube.com/watch?v=PMfcsYzj-9M&feature=youtu.be)

### 面向对象编程

考虑对象，通常称为类，是一组上下文感知的功能。一个对象非常适合放在专用文件中。在我们的例子中，我们可以为 XMLHttpRequest 构建一个基本的包装器。

**HttpRequest.js**

```
function HttpRequest(url) {
    this.request = new XMLHttpRequest();

    this.body = undefined;
    this.method = HttpRequest.METHOD_GET;
    this.url = url;

    this.responseParser = undefined;
}

HttpRequest.METHOD_GET = 'GET';
HttpRequest.METHOD_POST = 'POST';

HttpRequest.prototype.setMethod = function(method) {
    this.method = method;
    return this;
};

HttpRequest.prototype.setBody = function(body) {
    if (typeof body === 'object') {
        body = JSON.stringify(body);
    }

    this.body = body;
    return this;
};

HttpRequest.prototype.setResponseParser = function(responseParser) {
    if (typeof responseParser !== 'function') return;

    this.responseParser = responseParser;
    return this;
};

HttpRequest.prototype.send = function(callback) {
    this.request.addEventListener('load', function() {
        if (this.responseParser) {
            callback(this.responseParser(this.request.responseText));
        } else {
            callback(this.request.responseText);
        }
    }, false);

    this.request.open(this.method, this.url, true);
    this.request.send(this.body);
    return this;
}; 
```

**app.js**

```
new HttpRequest('/users')
    .setResponseParser(JSON.parse)
    .send(function(users) {
        // Do something with users
    });

new HttpRequest('/posts')
    .setResponseParser(JSON.parse)
    .send(function(posts) {
        // Do something with posts
    });

// Create a new user
new HttpRequest('/user')
    .setMethod(HttpRequest.METHOD_POST)
    .setBody({
        name: 'Tim',
        email: 'info@example.com'
    })
    .setResponseParser(JSON.parse)
    .send(function(user) {
        // Do something with new user
    }); 
```

上面创建的`HttpRequest`类现在非常易于配置，因此可以应用于我们的许多 API 调用。尽管实现——一系列连锁的方法调用——更加复杂，但该类的特性易于维护。在实现和可重用性之间找到平衡可能很困难，并且是特定于项目的。

当使用 OOP 时，设计模式是一个很好的补充。尽管它们本身并没有提高可读性，但是一致性确实提高了！

## 人类句法

文件，函数，对象，那些只是粗略的线条。它们让你的代码更容易被扫描。让代码易于*阅读*是一门更加细致入微的艺术。最微小的细节也能产生重大影响。例如，将您的行长度限制为 80 个字符是一个简单的解决方案，通常由编辑通过竖线来强制执行。但还有更多！

### 命名

适当的命名可以使人立即认出来，这样就不必去查找什么是值或者函数是做什么的了。

函数通常是骆驼形状的。以动词开头，后跟主语通常会有帮助。

```
function getApiUrl() { /* ... */ }
function setRequestMethod() { /* ... */ }
function findItemsById(n) { /* ... */ }
function hideSearchForm() { /* ... */ } 
```

对于变量名，尝试应用倒金字塔方法。主体第一，属性第二。

```
 var element = document.getElementById('body'),
    elementChildren = element.children,
    elementChildrenCount = elementChildren.length;

// When defining a set of colours, I prefix the variable with “color”
var colorBackground = 0xFAFAFA,
    colorPrimary = 0x663399;

// When defining a set of background properties, I use background as base
var backgroundColor = 0xFAFAFA,
    backgroundImages = ['foo.png', 'bar.png'];

// Context can make all the difference
var headerBackgroundColor = 0xFAFAFA,
    headerTextColor = 0x663399; 
```

能够区分常规变量和特殊变量也很重要。例如，常量名称通常用大写字母和下划线书写。

```
var URI_ROOT = window.location.href; 
```

类通常是以大写字母开头的驼色。

```
function FooObject {
    // ...
} 
```

一个小细节就是缩写。一些人选择写全大写的缩写，而另一些人选择坚持使用骆驼大小写。使用前者可能会使识别后面的缩写更加困难。

### 紧凑和优化

在许多代码库中，您可能会遇到“特殊”代码来减少字符数，或者提高算法的性能。

一行程序是紧凑代码的一个例子。不幸的是，他们经常依赖黑客或晦涩的语法。如下所示，嵌套的三元运算符是一种常见的情况。尽管它很简洁，但也需要一两秒钟才能理解它做了什么，这与常规的 if 语句相反。小心语法快捷键。

```
// Yay, someone managed to make this a one-liner!
var state = isHidden ? 'hidden' : isAnimating ? 'animating' : '';

// Yay, someone managed to make this readable!
var state = '';
if (isAnimating) state = 'animating';
if (isHidden) state = 'hidden'; 
```

微优化是性能优化，通常影响不大。大多数时候，它们的可读性不如性能较差的对等物。

```
// This may be most performant
$el[0].checked;

// But these are still fast, and are much easier to read
// Source: http://jsperf.com/prop-vs-ischecked/5
$el.prop('checked');
$el.is(':checked');
$el.attr('checked'); 
```

JavaScript 编译器在为我们优化代码方面真的很棒，而且它们会越来越好。除非未优化代码和优化代码之间的差异[明显](http://www.smashingmagazine.com/2015/09/why-performance-matters-the-perception-of-time/)，这通常是在数千或数百万次操作之后，否则推荐使用更容易阅读的代码。

## 非代码

这很讽刺，但是保持代码可读性的更好方法是添加不执行的语法。姑且称之为非代码。

### 空白

我很确定每个开发人员都有其他开发人员提供的代码，或者检查过网站的精简代码——去掉了大部分空白的代码。第一次遇到这种情况会让你大吃一惊。在不同的视觉艺术领域，比如设计和排版，空白空间和填充一样重要。你会想在两者之间找到微妙的平衡。对于这种平衡，每个公司、每个团队、每个开发人员都有不同的看法。幸运的是，有一些普遍认可的规则:

*   每行一个表达式，
*   缩进块的内容，
*   额外的分隔符可以用来分隔代码段。

任何其他规则都应该和你的同事讨论。无论您同意哪种代码风格，一致性都是关键。

```
function sendPostRequest(url, data, cb) {
    // A few assignments grouped together and neatly indented
    var requestMethod = 'POST',
        requestHeaders = {
            'Content-Type': 'text/plain'
        };

    // XMLHttpRequest initialisation, configuration and submission
    var request = new XMLHttpRequest();
    request.addEventListener('load', cb, false);
    request.open(requestMethod, url, false);
    request.send(data);
} 
```

### 评论

就像空白一样，注释是给你的代码添加空气的好方法，但是也允许你给代码添加细节。请务必添加注释以显示:

*   **非显而易见代码**的解释和论证，
*   一个**补丁**解决了哪些 bug 或异常，以及可用的源代码。

```
// Sum values for the graph’s range
var sum = values.reduce(function(previousValue, currentValue) { 
    return previousValue + currentValue;
});
```

并非所有的修复都是显而易见的。添加额外信息可以澄清很多问题:

```
if ('addEventListener' in element) {
    element.addEventListener('click', myFunc);
}
// IE8 and lower do not support .addEventListener, 
// so .attachEvent should be used instead
// http://caniuse.com/#search=addEventListener
// https://msdn.microsoft.com/en-us/library/ms536343%28VS.85%29.aspx
else {
    element.attachEvent('click', myFunc);
} 
```

### 内嵌文档

当编写面向对象的软件时，内联文档可以像常规注释一样，给你的代码一些喘息的空间。它们还有助于阐明属性或方法的目的和细节。许多 ide 使用它们作为提示，生成的文档工具也使用它们！不管是什么原因，写文档是一种很好的练习。

```
/**
 * Create a HTTP request
 * @constructor
 * @param {string} url
 */
function HttpRequest(url) {
    // ...
}

/**
 * Set an object of headers
 * @param {Object} headers
 * @return {HttpRequest}
 */
HttpRequest.prototype.setHeaders = function(headers) {
    for (var header in headers) {
        this.headers[header] = headers[header];
    }

    // Return self for chaining
    return this;
}; 
```

## 回拨谜题

事件和异步调用是很好的 JavaScript 特性，但是它经常使代码更难阅读。

异步调用通常带有回调。有时，您希望按顺序运行它们，或者等待它们都准备好。

```
function doRequest(url, success, error) { /* ... */ }

doRequest('https://example.com/api/users', function(users) {
    doRequest('https://example.com/api/posts', function(posts) {
        // Do something with users and posts
    }, function(error) {
        // /api/posts went wrong
    });
}, function(error) {
    // /api/users went wrong
}); 
```

ES2015(也称为 ES6)中引入了`Promise`对象来解决这两个问题。它允许您简化嵌套的异步请求。

```
function doRequest(url) {
    return new Promise(function(resolve, reject) {
        // Initialise request
        // Call resolve(response) on success
        // Call reject(error) on error
    });
}

// Request users first
doRequest('https://example.com/api/users')
// .then() is executed when they all executed successfully
.then(function(users) { /* ... */ })
// .catch() is executed when any of the promises fired the reject() function
.catch(function(error) { /* ... */ });

// Run multiple promises parallel
Promise.all([
    doRequest('https://example.com/api/users'),
    doRequest('https://example.com/api/posts')
])
.then(function(responses) { /* ... */ })
.catch(function(error) { /* ... */ }); 
```

虽然我们引入了额外的代码，但这更容易正确解释。你可以在这里阅读更多关于承诺的内容: [JavaScript 变得异步(这很棒)](https://www.sitepoint.com/javascript-goes-asynchronous-awesome/)

## ES6/ES2015

如果您了解 ES2015 规范，您可能已经注意到本文中的所有代码示例都是旧版本(除了`Promise`对象)。尽管 ES6 给了我们很棒的特性，但是在可读性方面还是有一些问题。

[粗箭头语法](https://www.sitepoint.com/preparing-ecmascript-6-new-function-syntax/)定义了一个从其父作用域继承`this`值的函数。至少，这是它被设计的原因。很容易用它来定义常规函数。

```
var add = (a, b) => a + b;
console.log(add(1, 2)); // 3 
```

另一个例子是 rest 和 spread 语法。

```
/**
 * Sums a list of numbers
 * @param {Array} numbers
 * @return {Number}
 */
function add(...numbers) {
    return n.reduce(function(previousValue, currentValue) {
        return previousValue + currentValue;
    }, 0);
}

add(...[1, 2, 3]);

/**
 * Sums a, b and c
 * @param {Number} a
 * @param {Number} b
 * @param {Number} c
 * @return {Number}
 */
function add(a, b, c) {
    return a + b + c;
}

add(1, 2, 3); 
```

我的观点是，ES2015 规范引入了许多有用但晦涩难懂、有时令人困惑的语法，这些语法容易被滥用于一行程序。我不想阻止使用这些功能。我想鼓励谨慎使用它们。

## 结论

在项目的每个阶段都要记住保持代码的可读性和可维护性。从文件系统到微小的语法选择，一切都很重要。尤其是在团队中，很难一直执行所有的规则。代码审查会有所帮助，但是仍然会留下人为错误的空间。幸运的是，有工具可以帮助你！

*   js hint–保持代码无错误的 JavaScript linter
*   惯用的——一种流行的代码风格标准，但是可以随意偏离
*   [editor config](http://editorconfig.org/)–定义跨编辑器代码样式

除了代码质量和风格工具之外，还有一些工具可以让代码更容易阅读。尝试不同的语法突出主题，或者尝试一个小地图来查看你的脚本的自上而下的概述([原子](https://atom.io/packages/minimap)，[括号](https://github.com/zorgzerg/brackets-minimap))。

你对编写可读和可维护的代码有什么想法？我很想在下面的评论中听到他们。

## 分享这篇文章