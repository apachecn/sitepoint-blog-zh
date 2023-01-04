# 相当纯的函数式编程介绍

> 原文：<https://www.sitepoint.com/an-introduction-to-reasonably-pure-functional-programming/>

*本文由[Panayiotis pvgr Velisarakos](https://github.com/pvgr)、 [Jezen Thomas](https://www.sitepoint.com/author/jthomas/) 和 [Florian Rappl](https://www.sitepoint.com/author/frappl/) 进行同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

当学习编程时，你首先会接触到过程化编程；这是你通过输入一系列命令来控制一台机器的地方。在你理解了一些语言基础之后，比如变量、赋值、函数和对象，你就可以拼凑出一个程序来实现你所设定的目标——你会觉得自己是一个绝对的向导。

成为一名更好的程序员的过程就是获得更强的能力来控制你写的程序，并找到最简单的解决方案，这种方案既要*正确*又要*可读性最强*。随着你成为一名更好的程序员，你将编写更小的函数，实现更好的代码重用，为你的代码编写测试，并且你将获得你编写的程序将继续按照你的意图运行的信心。没有人喜欢查找和修复代码中的错误，所以成为一名更好的程序员也是为了避免某些容易出错的事情。通过经验或听取那些更有经验的人的建议来了解应该避免什么，就像道格拉斯·克洛克福特在 [JavaScript:好的部分](https://vimeo.com/8691412)中的著名解释。

函数式编程给我们提供了降低程序复杂性的方法，它将程序简化为最简单的形式:行为类似纯数学函数的函数。学习函数式编程的原理对你的技能是一个很好的补充，并且会帮助你编写更简单的程序，错误更少。

函数式编程的关键概念是纯函数、不可变值、组合和驯服副作用。

## 纯函数

> 纯函数是这样的函数，给定相同的输入，将总是返回相同的输出，并且没有任何可观察到的副作用。

```
// pure
function add(a, b) {
  return a + b;
} 
```

这个函数是*纯*的。它不依赖于或改变函数之外的任何状态，它将*总是*为相同的输入返回相同的输出值。

```
// impure
var minimum = 21;
var checkAge = function(age) {
  return age >= minimum; // if minimum is changed we're cactus
}; 
```

这个函数*不纯*，因为它依赖于函数外部的外部可变状态。

如果我们把这个变量移到函数内部，它就变得纯净了，我们可以确定我们的函数每次都会正确地检查我们的年龄。

```
// pure
var checkAge = function(age) {
  var minimum = 21;
  return age >= minimum;
}; 
```

纯函数没有副作用。以下是一些需要记住的要点:

*   在函数外部访问系统状态
*   作为参数传递的变异对象
*   进行 HTTP 调用
*   获取用户输入
*   查询 DOM

### 受控突变

你需要注意数组和对象上的 Mutator 方法，它们会改变下面的对象，一个例子就是数组的`splice`和`slice`方法之间的不同。

```
// impure, splice mutates the array
var firstThree = function(arr) {
  return arr.splice(0,3); // arr may never be the same again
};

// pure, slice returns a new array
var firstThree = function(arr) {
  return arr.slice(0,3);
}; 
```

如果我们避免在传递给我们的函数的对象上改变方法，我们的程序就变得更容易推理，我们可以合理地期望我们的函数不会在我们下面切换东西。

```
let items = ['a','b','c'];
let newItems = pure(items);
// I expect items to be ['a','b','c'] 
```

## 纯函数的好处

纯函数比不纯函数有一些好处:

*   更容易**可测试**因为它们唯一的职责是映射输入- >输出
*   结果是可缓存的，因为相同的输入总是产生相同的输出
*   **自文档化**因为函数的依赖关系是显式的
*   更容易使用，因为你不需要担心副作用

因为纯函数的结果是可缓存的，所以我们可以将它们记忆起来，这样昂贵的操作只在第一次调用函数时执行。例如，记忆搜索大型索引的结果将在重新运行时产生巨大的性能改进。

## 不合理的纯函数式编程

将我们的程序简化为纯函数可以极大地降低程序的复杂性。然而，如果我们把功能抽象推得太远，我们的功能程序也可能最终需要雨人的帮助来理解。

```
import _ from 'ramda';
import $ from 'jquery';

var Impure = {
  getJSON: _.curry(function(callback, url) {
    $.getJSON(url, callback);
  }),

  setHtml: _.curry(function(sel, html) {
    $(sel).html(html);
  })
};

var img = function (url) {
  return $('<img />', { src: url });
};

var url = function (t) {
  return 'http://api.flickr.com/services/feeds/photos_public.gne?tags=' +
    t + '&format=json&jsoncallback=?';
};

var mediaUrl = _.compose(_.prop('m'), _.prop('media'));
var mediaToImg = _.compose(img, mediaUrl);
var images = _.compose(_.map(mediaToImg), _.prop('items'));
var renderImages = _.compose(Impure.setHtml("body"), images);
var app = _.compose(Impure.getJSON(renderImages), url);
app("cats"); 
```

花一分钟消化一下上面的代码。

除非你有函数式编程的背景，否则这些抽象(curry，过度使用 compose 和 prop)真的很难理解，执行流程也是如此。下面的代码更容易理解和修改，它也比上面的纯函数方法更清楚地描述了程序，而且代码更少。

*   `app`函数接受一串标签
*   从 [Flickr](https://www.flickr.com/) 获取 JSON
*   从响应中提取 URL
*   构建一个由`<img>`个节点组成的数组
*   将它们插入文档

```
var app = (tags)=> {
  let url = `http://api.flickr.com/services/feeds/photos_public.gne?tags=${tags}&format=json&jsoncallback=?`
  $.getJSON(url, (data)=> {
    let urls = data.items.map((item)=> item.media.m)
    let images = urls.map((url)=> $('<img />', { src: url }) )

    $(document.body).html(images)
  })
}
app("cats") 
```

或者，这个替代 API 使用像`fetch`和`Promise`这样的抽象帮助我们进一步阐明我们的异步动作的含义。

```
let flickr = (tags)=> {
  let url = `http://api.flickr.com/services/feeds/photos_public.gne?tags=${tags}&format=json&jsoncallback=?`
  return fetch(url)
  .then((resp)=> resp.json())
  .then((data)=> {
    let urls = data.items.map((item)=> item.media.m )
    let images = urls.map((url)=> $('<img />', { src: url }) )

    return images
  })
}
flickr("cats").then((images)=> {
  $(document.body).html(images)
}) 
```

**注意:** `fetch`和`Promise`是即将到来的标准，因此它们要求 polyfills 今天使用。

`Ajax`请求和`DOM`操作永远不会是纯粹的，但是我们可以从其余部分中创建一个纯粹的函数，将响应 JSON 映射到一组图像——现在让我们原谅对 jQuery 的依赖。

```
let responseToImages = (resp)=> {
  let urls = resp.items.map((item)=> item.media.m )
  let images = urls.map((url)=> $('<img />', { src: url }))

  return images
} 
```

我们的职能现在只做两件事:

*   映射响应`data`->-
*   映射`urls`->-`images`

“函数式”的方法是为这两个任务创建单独的函数，我们可以使用`compose`将一个函数的响应传递给另一个函数。

```
let urls = (data)=> {
  return data.items.map((item)=> item.media.m)
}
let images = (urls)=> {
  return urls.map((url)=> $('<img />', { src: url }))
}
let responseToImages = _.compose(images, urls) 
```

> compose 返回一个函数，该函数是一个函数列表的组合，每个函数使用后面的函数的返回值。

下面是`compose`正在做的事情，将`urls`的响应传递给我们的`images`函数。

```
let responseToImages = (data)=> {
  return images(urls(data))
} 
```

从右到左阅读要组合的参数有助于理解数据流的方向。

通过将我们的程序简化为纯粹的函数，这给了我们在未来重用它们的更大能力，它们测试起来更简单，并且它们是自文档化的。不利的一面是，当过度使用时(比如在第一个例子中)，这些功能抽象会使事情变得更加复杂，这当然不是我们想要的。重构代码时最重要的问题是:

代码是否更容易阅读和理解？

## 基本功能

现在，我根本没有试图攻击函数式编程。每个开发人员都应该齐心协力地学习基本的函数，这些函数可以让您将编程中的常见模式抽象成更简洁的声明性代码，或者如 Marijn Haverbeke 所说..

> 一个掌握了所有基本功能的程序员，更重要的是，掌握了如何使用这些功能的知识，比一个从零开始的程序员要有效得多。–[雄辩的 JavaScript，Marijn Haverbeke](http://eloquentjavascript.net/1st_edition/chapter6.html)

这里列出了每个 JavaScript 开发人员都应该学习和掌握的基本功能。这也是提高您的 JavaScript 技能，从头开始编写这些函数的好方法。

**数组**

*   [forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)
*   [地图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
*   [过滤器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
*   [减少](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

**功能**

*   告发
*   构成
*   部分的
*   [咖喱](https://lodash.com/docs#curry)

## 少即是多

让我们来看看使用函数式编程概念改进下面代码的一些实际步骤。

```
let items = ['a', 'b', 'c'];
let upperCaseItems = ()=> {
  let arr = [];
  for (let i = 0, ii = items.length; i < ii; i++) {
    let item = items[i];
    arr.push(item.toUpperCase());
  }
  items = arr;
} 
```

**减少功能对共享状态的依赖**

这听起来似乎很明显，但我仍然编写了访问和修改大量外部状态的函数，这使得它们更难测试，也更容易出错。

```
// pure
let upperCaseItems = (items)=> {
  let arr = [];
  for (let i = 0, ii = items.length; i < ii; i++) {
    let item = items[0];
    arr.push(item.toUpperCase());
  }
  return arr;
} 
```

**使用更可读的语言抽象，比如`forEach`来迭代**

```
let upperCaseItems = (items)=> {
  let arr = [];
  items.forEach((item) => {
    arr.push(item.toUpperCase());
  });
  return arr;
} 
```

**使用像`map`这样的高级抽象来减少代码量**

```
let upperCaseItems = (items)=> {
  return items.map((item)=> item.toUpperCase())
} 
```

将函数简化为最简单的形式

```
let upperCase = (item)=> item.toUpperCase()
let upperCaseItems = (items)=> items.map(upperCase) 
```

**删除代码直到它停止工作**

对于这样一个简单的任务，我们根本不需要函数，语言为我们提供了足够的抽象来逐字写出它。

```
let items = ['a', 'b', 'c']
let upperCaseItems = items.map((item)=> item.toUpperCase()) 
```

## 测试

能够简单地测试我们的程序是纯函数的一个主要优点，所以在这一节中，我们将为我们之前看到的 Flickr 模块设置一个测试工具。

启动终端，准备好文本编辑器，我们将使用[摩卡](https://mochajs.org/#getting-started)作为我们的测试运行程序，使用[巴别塔](http://babeljs.io/)来编译我们的 ES6 代码。

```
mkdir test-harness
cd test-harness
npm init -yes
npm install mocha babel-register babel-preset-es2015 --save-dev
echo '{ "presets": ["es2015"] }' > .babelrc
mkdir test
touch test/example.js 
```

Mocha 有一堆方便的函数，比如用于分解测试的`describe`和`it`，以及用于安装和拆卸任务的`before`和`after`等钩子。断言是一个核心节点包，可以执行简单的等式测试，`assert`和`assert.deepEqual`是需要注意的最有用的函数。

让我们在`test/example.js`中编写我们的第一个测试

```
import assert from 'assert';

describe('Math', ()=> {
  describe('.floor', ()=> {
    it('rounds down to the nearest whole number', ()=> {
      let value = Math.floor(4.24)
      assert(value === 4)
    })
  })
}) 
```

打开`package.json`，将`"test"`脚本修改如下

```
mocha --compilers js:babel-register --recursive 
```

然后，您应该能够从命令行运行`npm test`来确认一切正常。

```
Math
  .floor
    ✓ rounds down to the nearest whole number

1 passing (32ms) 
```

嘣。

**注意:**如果您想让 mocha 观察变化并自动运行测试，那么您还可以在这个命令的末尾添加一个`-w`标志，这样在重新运行时会运行得更快。

```
mocha --compilers js:babel-register --recursive -w 
```

### 测试我们的 Flickr 模块

让我们将我们的模块添加到`lib/flickr.js`

```
import $ from 'jquery';
import { compose } from 'underscore';

let urls = (data)=> {
  return data.items.map((item)=> item.media.m)
}
let images = (urls)=> {
  return urls.map((url)=> $('<img />', { src: url })[0] )
}
let responseToImages = compose(images, urls)

let flickr = (tags)=> {
  let url = `http://api.flickr.com/services/feeds/photos_public.gne?tags=${tags}&format=json&jsoncallback=?`
  return fetch(url)
  .then((response)=> response.json())
  .then(responseToImages)
}

export default {
  _responseToImages: responseToImages,
  flickr: flickr,
} 
```

我们的模块公开了两个方法:`flickr`被公开使用，还有一个私有函数`_responseToImages`，这样我们就可以单独测试了。

我们有几个新的依赖项:`jquery`、`underscore`以及`fetch`和`Promise`的聚合填充。为了测试这些，我们可以使用`jsdom`来聚合填充`DOM`对象`window`和`document`，并且我们可以使用`sinon`包来存根化获取 api。

```
npm install jquery underscore whatwg-fetch es6-promise jsdom sinon --save-dev
touch test/_setup.js 
```

打开`test/_setup.js`，我们将使用模块所依赖的全局变量来配置 jsdom。

```
global.document = require('jsdom').jsdom('<html></html>');
global.window = document.defaultView;
global.$ = require('jquery')(window);
global.fetch = require('whatwg-fetch').fetch; 
```

我们的测试可以放在`test/flickr.js`中，在这里我们将断言给定预定义输入的函数输出。我们“stub”或覆盖全局 fetch 方法来拦截和伪造 HTTP 请求，这样我们就可以运行我们的测试，而不会直接命中 Flickr API。

```
import assert from 'assert';
import Flickr from "../lib/flickr";
import sinon from "sinon";
import { Promise } from 'es6-promise';
import { Response } from 'whatwg-fetch';

let sampleResponse = {
  items: [{
    media: { m: 'lolcat.jpg' }
  },{
    media: { m: 'dancing_pug.gif' }
  }]
}

// In a real project we'd shift this test helper into a module
let jsonResponse = (obj)=> {
  let json = JSON.stringify(obj);
  var response = new Response(json, {
    status: 200,
    headers: { 'Content-type': 'application/json' }
  });
  return Promise.resolve(response);
}

describe('Flickr', ()=> {
  describe('._responseToImages', ()=> {
    it("maps response JSON to a NodeList of <img>", ()=> {
      let images = Flickr._responseToImages(sampleResponse);

      assert(images.length === 2);
      assert(images[0].nodeName === 'IMG');
      assert(images[0].src === 'lolcat.jpg');
    })
  })

  describe('.flickr', ()=> {
    // Intercept calls to fetch(url) and return a Promise
    before(()=> {
      sinon.stub(global, 'fetch', (url)=> {
        return jsonResponse(sampleResponse)
      })
    })

    // Put that thing back where it came from or so help me!
    after(()=> {
      global.fetch.restore();
    })

    it("returns a Promise that resolves with a NodeList of <img>", (done)=> {
      Flickr.flickr('cats').then((images)=> {
        assert(images.length === 2);
        assert(images[1].nodeName === 'IMG');
        assert(images[1].src === 'dancing_pug.gif');
        done();
      })
    })

  })
}) 
```

用`npm test`再次运行我们的测试，您应该会看到三个绿色的勾号。

```
Math
  .floor
    ✓ rounds down to the nearest whole number

Flickr
  ._responseToImages
    ✓ maps response JSON to a NodeList of <img>
  .flickr
    ✓ returns a Promise that resolves with a NodeList of <img>

3 passing (67ms) 
```

唷！我们已经成功地测试了我们的小模块和组成它的函数，学习了纯函数以及如何使用函数组合。我们已经将纯的和不纯的分开，它是可读的，由小函数组成，并且经过了很好的测试。代码比上面的*不合理的纯粹*的例子*更容易阅读、理解和修改*，这是我重构代码的唯一目的。

纯函数，使用它们。

## 链接

*   弗里斯比教授的《函数式编程指南》是我见过的最好的函数式编程指南。本文中的许多观点和例子都来自这本书。
*   雄辩的 Javascript——函数式编程@ marijnjh—Marijn Haverbeke 的书一直是我最喜欢的编程入门书籍之一，其中有一章也是关于函数式编程的。
*   下划线——钻研像下划线、 [Lodash](https://lodash.com/) 或 [Ramda](http://ramdajs.com/docs/) 这样的实用程序库是开发人员走向成熟的重要一步。理解如何使用这些函数将极大地减少您需要编写的代码量，并使您的程序更具声明性。

—

暂时就这样吧！感谢您的阅读，我希望您发现这是一篇关于 JavaScript 中函数式编程、重构和测试的很好的介绍。这是一个有趣的范例，目前正在掀起波澜，这主要是由于像 [React](https://facebook.github.io/react/) 、 [Redux](http://redux.js.org/) 、 [Elm](http://elm-lang.org/) 、 [Cycle](http://cycle.js.org/) 和[React vex](http://reactivex.io/)这样的库越来越受欢迎，它们鼓励或加强这些模式。

跳进来吧，水是热的。

## 分享这篇文章