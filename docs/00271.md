# 如何在 JavaScript 中遍历 JSON 响应

> 原文：<https://www.sitepoint.com/loop-through-json-response-javascript/>

从远程服务器获取数据时，服务器的响应通常是 JSON 格式的。在这篇快速技巧文章中，我将演示如何使用 JavaScript 解析服务器的响应，从而访问您需要的数据。

这个过程通常由两个步骤组成:将数据解码为原生结构(比如数组或对象)，然后使用 JavaScript 的一个内置方法遍历该数据结构。在本文中，我将使用大量可运行的示例来涵盖这两个步骤。

## JSON 是什么？

在我们研究如何处理 JSON 之前，让我们花一点时间来理解它是什么(以及它不是什么)。

JSON 代表**J**ava**S**script**O**object**N**rotation。它是一种独立于语言、基于文本的格式，通常用于在 web 应用程序中传输数据。JSON 的灵感来自 JavaScript 对象文字符号，但两者之间存在差异。例如，在 JSON 中，键必须用双引号括起来，而在 object literals 中却不是这样。

在 JSON 中有两种存储数据的方式:

*   名称/值对的集合(也称为 JSON 对象)
*   值的有序列表(也称为 JSON 数组)

当从 web 服务器接收数据时，数据总是一个字符串，这意味着您需要将它转换成您可以使用的数据结构。

如果你想了解更多关于 JSON 的工作原理，请访问 [JSON 网站](https://www.json.org/json-en.html)。

## 从远程 API 获取 JSON

在下面的例子中，我们将使用奇妙的[icanhazdadjook API](https://icanhazdadjoke.com/api)。正如您在它的文档中所看到的，发出一个 GET 请求，其中`Accept`头被设置为`application/json`，将会看到 API 返回一个 JSON 有效载荷。

让我们从一个简单的例子开始:

```
const xhr = new XMLHttpRequest();
xhr.onreadystatechange = () => {
  if (xhr.readyState === XMLHttpRequest.DONE) {
    console.log(typeof xhr.responseText);
    console.log(xhr.responseText);
  }
};
xhr.open('GET', 'https://icanhazdadjoke.com/', true);
xhr.setRequestHeader('Accept', 'application/json');
xhr.send(null);

// string
// {"id":"daaUfibh","joke":"Why was the big cat disqualified from the race? Because it was a cheetah.","status":200} 
```

正如我们所看到的，服务器返回给我们一个字符串。我们需要将它解析成一个 JavaScript 对象，然后才能遍历它的属性。我们可以用 [JSON.parse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) 来做这件事:

```
if (xhr.readyState === XMLHttpRequest.DONE) {
  const res = JSON.parse(xhr.responseText);
  console.log(res);
};

// Object { id: "fiyPR7wPZDd", joke: "When does a joke become a dad joke? When it becomes apparent.", status: 200 } 
```

一旦我们有了作为 JavaScript 对象的响应，就有很多方法可以用来遍历它。

### 使用`for...in`循环

在循环中的[for…遍历对象的所有可枚举属性:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for%E2%80%A6in)

```
const res = JSON.parse(xhr.responseText);

for (const key in res){
  if(obj.hasOwnProperty(key)){
    console.log(`${key} : ${res[key]}`)
  }
}

// id : H6Elb2LBdxc
// joke : What's blue and not very heavy?  Light blue.
// status : 200 
```

请注意，`for...of`循环将遍历整个原型链，所以这里我们使用`hasOwnProperty`来确保属性属于我们的`res`对象。

### 使用`Object.entries`、`Object.values`或`Object.entries`

上述方法的替代方法是使用 [Object.keys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) 、 [Object.values()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/values) 或 [Object.entries()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries) 中的一个。这些将返回一个数组，然后我们可以迭代。

让我们来看看如何使用`Object.entries`。这将返回我们传递给它的对象的键/值对的数组:

```
const res = JSON.parse(xhr.responseText);

Object.entries(res).forEach((entry) => {
  const [key, value] = entry;
  console.log(`${key}: ${value}`);
});

// id: SvzIBAQS0Dd 
// joke: What did the pirate say on his 80th birthday? Aye Matey!
// status: 200 
```

请注意，`const [key, value] = entry;`语法是在 ES2015 语言中引入的[数组析构](https://www.sitepoint.com/es6-destructuring-assignment/)的一个例子。

这要简洁得多，避免了前面提到的原型问题，并且是我首选的遍历 JSON 响应的方法。

## 使用获取 API

虽然上面使用`XMLHttpRequest`对象的方法工作得很好，但它会很快变得难以使用。我们可以做得更好。

[获取 API](https://www.sitepoint.com/introduction-to-the-fetch-api/) 是一个基于承诺的 API，它支持一个更干净、更简洁的语法，并帮助你远离回调地狱。它提供了在`window`对象上定义的`fetch()`方法，您可以用它来执行请求。该方法返回一个承诺，您可以用它来检索请求的响应。

让我们重写前面的例子来使用它:

```
(async () => {
  const res = await fetch('https://icanhazdadjoke.com/', {
    headers: { Accept: 'application/json' },
  });
  const json = await res.json();
  Object.entries(json).forEach(([key, value]) => {
    console.log(`${key}: ${value}`);
  });
})();

// id: 2wkykjyIYDd
// joke: What did the traffic light say to the car as it passed? "Don't look I'm changing!"
// status: 200 
```

获取 API 返回一个[响应流](https://developer.mozilla.org/en-US/docs/Web/API/Response)。这不是 JSON，所以我们需要使用它的 [response.json()](https://developer.mozilla.org/en-US/docs/Web/API/Body/json) 函数，而不是试图在它上面调用`JSON.parse()`。这将返回一个承诺，解析的结果是将响应的正文解析为 JSON。

## 处理数组

正如本文开头提到的，有序的值列表(也称为数组)是有效的 JSON，所以在结束之前，让我们来看看如何处理这样的响应。

对于最后一个例子，我们将使用 [GitHub 的 REST API](https://docs.github.com/en/rest) 来获取用户的存储库列表:

```
(async () => {
  async function getRepos(username) {
    const url = `https://api.github.com/users/${username}/repos`;

    const response = await fetch(url);
    const repositories = await response.json();

    return repositories;
  }

  const repos = await getRepos('jameshibbard');
  console.log(repos);
})();

// Array(30) [ {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, … ] 
```

如您所见，API 返回了一个对象数组。要访问每个单独的对象，我们可以使用常规的`forEach`方法:

```
repos.forEach((repo) => {
  console.log(`{$repo.name} has ${repo.stargazers_count} stars`);
});

// Advanced-React has 0 stars 
// angular2-education has 0 stars
// aurelia-reddit-client has 3 stars
// authentication-with-devise-and-cancancan has 20 stars
// ... 
```

或者，您当然可以使用上面讨论的任何方法来遍历对象的所有属性，并将它们记录到控制台:

```
repos.forEach((repo) => {
  Object.entries(repo).forEach(([key, value]) => {
    console.log(`${key}: ${value}`);
  });
});

// name: Advanced-React
// full_name: jameshibbard/Advanced-React
// private: false
// ... 
```

## 结论

在这个快速技巧中，我们已经了解了 JSON 是什么。我演示了如何将来自服务器的 JSON 响应解析成本机数据结构(比如数组或对象)，以及如何遍历这样的结构，以便访问其中包含的数据。

如果你在这篇文章中遇到任何问题，为什么不去看看 [SitePoint 的论坛](https://www.sitepoint.com/community/c/javascript/33)，那里有很多友好的人可以帮助你。

## 分享这篇文章