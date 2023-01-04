# 使用 Fetch API 和 ES6 生成器的异步 API

> 原文：<https://www.sitepoint.com/asynchronous-apis-using-fetch-api-es6-generators/>

[ECMAScript 6](http://www.ecma-international.org/ecma-262/6.0/) (又名 ECMAScript 2015 或 ES6)为 JavaScript 带来了许多新功能，这将使该语言非常适合大型应用程序。其中一个特性是使用[承诺](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)和[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function%2a)更好地支持异步编程。另一个是添加了 [Fetch API](https://developer.mozilla.org/en/docs/Web/API/Fetch_API) ，旨在取代`XMLHttpRequest`作为与远程资源通信的基础。

Fetch API 的方法返回 ES6 `Promise`对象，这些对象可以与生成器结合使用，构成复杂异步操作的基础。这可能是一系列的异步操作，其中每个操作都依赖于前一个操作返回的值，也可能是为了获得最新更新而必须对服务器重复进行的异步调用。

在本文中，我们将看到如何将 Fetch API 与生成器结合使用来构建异步 API。目前在 [Chrome、Opera、Firefox 和 Android 浏览器](http://caniuse.com/#search=Fetch)中都支持 Fetch API。GitHub 为不支持的浏览器提供了[聚合填充。](https://github.com/github/fetch)

和以往一样，本文的代码可以在我们的 GitHub 库上找到，在文章的底部有一个最终技术的[演示。](#demo)

## 异步操作的生成器

提示:如果你需要复习什么是生成器以及它们是如何工作的，请查看: [ECMAScript 2015:生成器和迭代器](https://www.sitepoint.com/ecmascript-2015-generators-and-iterators/)

那么我们如何使用生成器来执行异步操作呢？如果我们分析发电机的工作方式，我们就会找到答案。

实现迭代器的生成器函数具有以下结构:

```
function *myIterator(){
  while(condition){
    //calculate next value to return
    yield value;
  }
} 
```

关键字`yield`负责返回一个结果，并暂停迭代器函数的执行，直到下一次被调用。它还保留了函数的状态，而不是在下次调用时重新运行，有效地记住了它上次停止的地方。

我们可以不使用 while 循环重新想象上述函数，如下所示:

```
function *myIterator(){
  //calculate value 1
  yield value1;

  //calculate value 2
  yield value2;
  ...

  //calculate value n
  yield valuen;
} 
```

在上述两种情况下，函数的行为是相同的。使用 yield 关键字的唯一原因是暂停函数的执行，直到下一次迭代(这本身似乎有点异步)。由于`yield`语句可以返回任何值，我们也可以返回承诺并让函数运行多个异步调用。

## 通过 Fetch API 使用生成器

**提示:**要复习获取 API，请查看:[获取 API 简介](https://www.sitepoint.com/introduction-to-the-fetch-api/)

如前所述，Fetch API 旨在取代`XMLHttpRequest`。这个新的 API 提供了对 HTTP 请求的每一部分的控制，并返回一个承诺，该承诺根据服务器的响应决定是接受还是拒绝。

### 长轮询

Fetch API 和生成器可以一起使用的用例之一是[长轮询](http://www.pubnub.com/blog/http-long-polling/)。长轮询是一种技术，在这种技术中，客户机不断向服务器发送请求，直到得到响应。在这种情况下，可以使用生成器来不断生成响应，直到响应包含数据。

为了模拟长轮询，我在样本代码中包含了一个 [Express REST API](https://github.com/sitepoint-editors/FetchAPI-Generators/blob/master/server.js) ,在五次尝试之后，它会响应一个城市的天气信息。以下是 REST API:

```
var polls=0;

app.get('/api/currentWeather', function(request, response){
  console.log(polls, polls<5);
  if(polls < 5){
    console.log("sending...empty");
    polls++;
    response.send({});
  }
  else{
    console.log("sending...object");
    response.send({
      temperature: 25,
      sky: "Partly cloudy",
      humid: true
    });
    polls = 0;
  } 
}); 
```

现在，让我们编写一个生成器函数，它多次调用这个 API，并在每次迭代中返回一个承诺。在客户端，我们不知道经过多少次迭代后，我们将从服务器获得数据。因此，这个方法将有一个无限循环，在每次迭代中 ping 服务器，并在每次迭代中返回承诺。下面是此方法的实现:

```
function *pollForWeatherInfo(){
  while(true){
    yield fetch('/api/currentWeather',{
      method: 'get'
    }).then(function(d){
      var json = d.json();
      return json;
    });
  }
} 
```

我们需要一个函数来不断调用这个函数，并在承诺解析后检查值是否存在。这将是一个递归函数，它调用生成器的下一次迭代，并且只在发现从生成器返回的值时停止该过程。以下代码片段显示了此方法的实现和调用此方法的语句:

```
function runPolling(generator){
  if(!generator){
    generator = pollForWeatherInfo();
  }

  var p = generator.next();
  p.value.then(function(d){
    if(!d.temperature){
      runPolling(generator);
    } else {
      console.log(d);
    }
  });
}

runPolling(); 
```

正如我们在这里看到的，对函数`runPolling`的第一次调用创建了`generator`对象。`next`方法返回一个具有`value`属性的对象，在我们的例子中，该对象包含一个由`fetch`方法返回的承诺。当这个承诺解析时，它要么包含一个空对象(如果`polls`变量小于 5，则返回)，要么包含一个包含所需信息的对象。

接下来，我们检查这个对象的`temperature`属性(这表示成功)。如果它不存在，我们将`generator`对象传递回下一个函数调用(以免丢失生成器的状态),或者我们将对象的值打印到控制台。

要看到这一点，从我们的 repo 中获取代码，安装依赖项，启动服务器，然后导航到 [http://localhost:8000](http://localhost:8000) 。您应该在 shell 中看到以下结果:

```
0 true
sending...empty
1 true
sending...empty
2 true
sending...empty
3 true
sending...empty
4 true
sending...empty
5 false
sending...object 
```

并且对象本身被记录到浏览器控制台。

### 多个相关异步调用

我们经常需要实现多个相关的异步调用，其中每个后续的异步操作都依赖于前一个异步操作返回的值。如果我们有一组这样的操作，并且它们必须被多次调用，我们可以将它们放在一个生成器函数中，并在需要时执行它。

为了演示这一点，我将使用 [GitHub 的 API](https://developer.github.com/v3/) 。该 API 为我们提供了用户、组织和回购的基本信息。我们将使用这个 API 来获取一个组织的随机回购的贡献者列表，并在屏幕上显示获取的数据。

为此，我们需要调用三个不同的端点。这些是要执行的任务:

*   获取组织的详细信息
*   如果该组织存在，则获取该组织的回购
*   让参与者参与组织的一个回购(随机选择)

让我们围绕 Fetch API 创建一个包装器函数，以避免重复创建头部和构建请求对象的代码。

```
function wrapperOnFetch(url){
  var headers = new Headers();
  headers.append('Accept', 'application/vnd.github.v3+json');
  var request = new Request(url, {headers: headers});

  return fetch(request).then(function(res){
    return res.json();
  });
} 
```

下面的函数使用上面的函数，并为每次调用生成一个承诺:

```
function* gitHubDetails(orgName) {
  var baseUrl = "https://api.github.com/orgs/";
  var url = baseUrl + orgName;

  var reposUrl = yield wrapperOnFetch(url);
  var repoFullName = yield wrapperOnFetch(reposUrl);
  yield wrapperOnFetch(`https://api.github.com/repos/${repoFullName}/contributors`);
} 
```

现在，让我们编写一段逻辑来调用上面的函数以获取生成器，然后使用从服务器获得的值来填充 UI。由于对生成器的`next`方法的每次调用都返回一个承诺，我们必须将这些承诺链接起来。以下是使用上述函数返回的生成器的代码框架:

```
var generator = gitHubDetails("aspnet");

generator.next().value.then(function (userData) {
  //Update UI

  return generator.next(userData.repos_url).value.then(function (reposData) {
    return reposData;
  });
}).then(function (reposData) {
  //Update UI

  return generator.next(reposData[randomIndex].full_name).value.then(function (selectedRepoCommits) {
    //Update UI
 });
}); 
```

要看到这一点，如上所述，从[我们的 repo](https://github.com/sitepoint-editors/FetchAPI-Generators) 获取代码，安装依赖项，启动服务器，然后导航到 [http://localhost:8000](http://localhost:8000) 。或者只是看看下面的演示(尝试重新运行)。

### 演示

在 [CodePen](http://codepen.io) 上用 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Fetch API 查看 Pen [多个依赖异步调用。](http://codepen.io/SitePoint/pen/gawNxe/)