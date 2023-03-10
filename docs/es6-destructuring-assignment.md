# ES6 运行中:析构赋值

> 原文：<https://www.sitepoint.com/es6-destructuring-assignment/>

解构任务听起来很复杂。这让我想起了面向对象的术语，比如*封装*和*多态*。我确信他们被选择来使简单的概念看起来更复杂！

实质上，ECMAScript 6 (ES2015)析构赋值允许您从数组或对象中提取单个项，并使用速记语法将它们放入变量中。来自 PHP 的人可能遇到过 [list()](http://php.net/manual/en/function.list.php) 函数，它在一次操作中将数组提取为变量。ES6 将它带到了另一个高度。

假设我们有一个数组:

```
var myArray = ['a', 'b', 'c']; 
```

我们可以通过 ES5 中的索引提取这些值:

```
var
  one   = myArray[0],
  two   = myArray[1],
  three = myArray[2];

// one = 'a', two = 'b', three = 'c' 
```

ES6 析构允许一种更简单、更不容易出错的替代方法:

```
const [one, two, three] = myArray;

// one = 'a', two = 'b', three = 'c' 
```

您可以忽略某些值，例如

```
const [one, , three] = myArray;

// one = 'a', three = 'c' 
```

或者使用 rest 运算符(`...`)提取剩余的元素:

```
const [one, ...two] = myArray;

// one = 'a', two = ['b, 'c'] 
```

析构也作用于对象，例如

```
var myObject = {
  one:   'a',
  two:   'b',
  three: 'c'
};

// ES5 example
var
  one   = myObject.one,
  two   = myObject.two,
  three = myObject.three;

// one = 'a', two = 'b', three = 'c'

// ES6 destructuring example
const {one, two, three} = myObject;

// one = 'a', two = 'b', three = 'c' 
```

在这个例子中，变量名`one`、`two`和`three`匹配对象属性名。我们也可以用任何名字给变量分配属性，例如

```
const myObject = {
  one:   'a',
  two:   'b',
  three: 'c'
};

// ES6 destructuring example
const {one: first, two: second, three: third} = myObject;

// first = 'a', second = 'b', third = 'c' 
```

也可以引用更复杂的嵌套对象，例如

```
const meta = {
  title: 'Destructuring Assignment',
  authors: [
    {
      firstname: 'Craig',
      lastname: 'Buckler'
    }
  ],
  publisher: {
    name: 'SitePoint',
    url: 'https://www.sitepoint.com/'
  }
};

const {
    title: doc,
    authors: [{ firstname: name }],
    publisher: { url: web }
  } = meta;

/*
  doc   = 'Destructuring Assignment'
  name  = 'Craig'
  web   = 'https://www.sitepoint.com/'
*/ 
```

这看起来有点复杂，但是记住在所有的析构赋值中:

*   赋值的左边是**析构目标**——定义被赋值变量的模式
*   赋值的右边是**析构源**——保存被提取数据的数组或对象。

还有一些其他的警告。首先，你不能用花括号开始一个语句，因为它看起来像一个代码块

```
// THIS FAILS
{ a, b, c } = myObject; 
```

您必须声明变量，例如

```
// THIS WORKS
const { a, b, c } = myObject; 
```

或者，如果变量已经声明，则使用括号，例如

```
// THIS WORKS
({ a, b, c } = myObject); 
```

您还应该警惕混合声明的和未声明的变量，例如

```
// THIS FAILS
let a;
let { a, b, c } = myObject;

// THIS WORKS
let a, b, c;
({ a, b, c } = myObject); 
```

这是解构的基础。那么什么时候会有用呢？我很高兴你问了…

## 更容易申报

无需显式定义每个值就可以声明变量，例如

```
// ES5
var a = 'one', b = 'two', c = 'three';

// ES6
const [a, b, c] = ['one', 'two', 'three']; 
```

诚然，解构的版本更长。它更容易阅读，虽然更多的项目可能不是这样。

## 变量值交换

在 ES5 中交换值需要一个临时的第三个变量，但是使用析构要简单得多:

```
var a = 1, b = 2;

// ES5 swap
var temp = a;
a = b;
b = temp;

// a = 2, b = 1

// ES6 swap back
[a, b] = [b, a];

// a = 1, b = 2 
```

你不局限于两个变量。可以重新排列任意数量的项目，例如

```
// rotate left
[b, c, d, e, a] = [a, b, c, d, e]; 
```

## 默认函数参数

假设我们有一个输出我们的`meta`对象的函数:

```
var meta = {
  title: 'Destructuring Assignment',
  authors: [
    {
      firstname: 'Craig',
      lastname: 'Buckler'
    }
  ],
  publisher: {
    name: 'SitePoint',
    url: 'https://www.sitepoint.com/'
  }
};

prettyPrint(meta); 
```

在 ES5 中，有必要解析这个对象以确保适当的默认值可用，例如

```
// ES5 default values
function prettyPrint(param) {
  param = param || {};
  var
    pubTitle = param.title || 'No title',
    pubName = (param.publisher &amp;&amp; param.publisher.name) || 'No publisher';

  return pubTitle + ', ' + pubName;
} 
```

在 ES6 中，我们可以为任何参数分配默认值，例如

```
// ES6 default value
function prettyPrint(param = {}) { 
```

但是，我们可以使用析构来提取值，并在必要时分配默认值:

```
// ES6 destructured default value
function prettyPrint(
  {
    title: pubTitle = 'No title',
    publisher: { name: pubName = 'No publisher' }
  } = {}
) {
  return pubTitle + ', ' + pubName;
} 
```

我不相信这更容易阅读，但它明显更短。

## 从函数中返回多个值

函数只能返回一个值，但可以是复杂的对象或多维数组。析构赋值使这变得更加实际，例如

```
function f() {
  return [1, 2, 3];
}

const [a, b, c] = f();

// a = 1, b = 2, c = 3 
```

## For-of 迭代

考虑一组图书信息:

```
const books = [
  {
    title: 'Full Stack JavaScript',
    author: 'Colin Ihrig and Adam Bretz',
    url: 'https://www.sitepoint.com/store/full-stack-javascript-development-mean/'
  },
  {
    title: 'JavaScript: Novice to Ninja',
    author: 'Darren Jones',
    url: 'https://www.sitepoint.com/store/leaern-javascript-novice-to-ninja/'
  },
  {
    title: 'Jump Start CSS',
    author: 'Louis Lazaris',
    url: 'https://www.sitepoint.com/store/jump-start-css/'
  },
]; 
```

的-of 的 ES6 [类似于`for-in`，除了它提取每个值而不是索引/键，例如](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/for...of)

```
for (const b of books) {
  console.log(b.title + ' by ' + b.author + ': ' + b.url);
} 
```

析构赋值提供了进一步的增强，例如

```
for (const {title, author, url} of books) {
  console.log(title + ' by ' + author + ': ' + url);
} 
```

## 正则表达式处理

正则表达式函数如 [match](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match) 返回匹配项的数组，这可以构成析构赋值的源:

```
const [a, b, c, d] = 'one two three'.match(/\w+/g);

// a = 'one', b = 'two', c = 'three', d = undefined 
```

## 析构赋值支持

析构赋值可能不会彻底改变你的开发生活，但是它可以节省大量的打字工作量！

目前，[对析构赋值](https://kangax.github.io/compat-table/es6/#test-destructuring)的支持很好。它可以在 Node 和所有主流浏览器中使用，除了 Internet Explorer。如果你需要支持旧的浏览器，建议使用编译器，如 [Babel](https://babeljs.io/) 或 [Traceur](https://github.com/google/traceur-compiler) ，它们会将 ES6 的析构赋值转换成 ES5 的等价赋值。

## 分享这篇文章