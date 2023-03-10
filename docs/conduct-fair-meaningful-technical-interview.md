# 如何进行公平而有意义的技术面试

> 原文：<https://www.sitepoint.com/conduct-fair-meaningful-technical-interview/>

当我开始寻找我的第一份网页开发工作时，我申请了几家公司，并接受了面试。我的一些采访对象是财富 500 强公司；我的一些采访对象是羽翼未丰的初创企业。无论公司规模大小，面试过程都非常相似:电话筛选、技术挑战和文化筛选。

技术挑战让我了解了一家公司的特征。如果被问到的问题是公平和有意义的，那么不管我是否通过了技术面试，我都会对这家公司留下好印象。最坏的情况是，我离开时没有工作，但学到了一些新的有用的知识。

如果我被问到的问题超出了某个职位的范围，或者仅仅是一个诡计，那么公司就有疏远我和其他应聘者的风险。我仍然记得我在纽约曼哈顿对一家初创公司的首席技术官的采访。这个人让我描述原型继承和原型继承的区别。第二种继承模式并不存在。面试结束后，我和其他几位应聘者聊了聊，我们都同意——我们永远不会为那家公司工作。

那么什么才算公平有意义呢？公平被认为是问适合申请人未来职位的问题。有意义被认为是提出问题，揭示对基本概念的某种程度的理解。当问题公平且有意义时，受访者和采访者都会受益。

我相信这两个目标都可以用这三个概念来实现:

1.  回收
2.  有约束力的
3.  事件发射器和继承

这些概念中的每一个都是 web 开发人员知识的一部分；然而，这些话题是不相关的，足以给错误回答一个问题的受访者一个正确回答其他问题的机会。

在这三个概念之后有一个给技术面试评分的标准。

### 回收

面试官应该总是要求受访者定义一个概念。这第一步确认了受访者理解被问到的问题。如果面试官没有问这个问题，那么受访者应该主动分享他们对这个概念的理解。没有一个共同的定义，受访者不太可能解决给定的任务。

在达成相互定义后，面试官应该提出一个涉及代码的问题:我想探究你对回调的理解，所以请创建一个知名函数`reduce`的实现。此时，面试官应该用示例输入和输出数据展示对`reduce`的调用。

```
// input
reduce([1,2,3], function(total, value) {
  return total + value;
}, 0);

// output 
6
```

在受访者创建他们的实施之前，访谈者应要求受访者在此过程中大声说话。这一步骤使采访者能够理解被采访者的想法，并防止被采访者在错误的道路上走得太远。

受访者将根据我的经验，使用一个`for`循环创建一个`reduce`的实现:

```
var reduce = function(array, callback, base) {
  for (var i = 0, length = array.length; i < length; i++) {
    base = callback(base, array[i]);
  }

  return base; 
};
```

这个过程的下一步是给问题增加一点复杂性。提示受访者重构他们对`reduce`的实现，以包含另一个众所周知的函数`each`。该请求将要求受访者使用两个回调函数，嵌套在`reduce`中的`each`:

```
var each = function(array, callback) {
  for (var i = 0, length = array.length; i < length; i++) {
    callback(array[i], i, array);
  }
};

var reduce = function(array, callback, base) {
  each(array, function(current, i, array) {
    base = callback(base, current);
  });

  return base;  
};
```

### 有约束力的

重复上一个问题的相同步骤。请受访者定义绑定的概念，请受访者创建一个`bind`的实现，并请受访者大声说话。

关于`bind`，受访者可以使用或不使用`prototype`来创建实施。面试官应该允许受访者首先创建更简单的实现——没有`prototype`。当被问及更高级的实施时，这种方法可以让受访者树立信心。

以下是不带`prototype`的`bind`的输入和输出数据示例:

```
// input: 
bind({name: "Cho"}, function() { 
  return this.name; 
});

// output: 
"Cho"
```

下面是没有`prototype`的`bind`的实现:

```
var bind = function(context, func) {
  return func.apply(context);
};
```

下一步是要求受访者使用`prototype`实现`bind`。下面是一个带有`prototype`的`bind`的输入和输出数据的例子:

```
// input: 
var myFunc = function() { 
  return this.name; 
}; 

myFunc.bind({name: "Cho, again!"}); 

// output: 
"Cho, again!"
```

下面是带有一个`prototype`的`bind`的实现:

```
Function.prototype.bind = function(context) {
  var func = this;

  return func.apply(context);
};
```

如果面试官想进一步增加`bind`的难度，那么请受访者重构他们的`bind`实现以接受论点。

### 事件发射器和继承

与回调和绑定相比，受访者对事件发射器的概念不太熟悉。由于这个原因，访问者应该向受访者澄清，许多短语被用来描述这个概念，如事件系统和事件库。一旦受访者同意双方的定义，就要为期望的实施提出一些限制。

面试官可以用一个准备好的输入和输出数据的例子来实现这个目标:

```
// input:
eventEmitter.on("greet", function() {
  return "Hello, Cho.";
});

eventEmitter.trigger("greet");

// output:
"Hello, Cho."
```

受访者现在准备写一些代码。

```
var EventEmitter = function() {
  this.events = {};
};

EventEmitter.prototype.on = function(event, callback) {
  this.events[event] = callback;
};

EventEmitter.prototype.trigger = function(event) {
  if (!this.events[event]) {
    throw new Error("Event doesn't exist");
  }

  return this.events[event]();
};
```

如果受访者已经在技术挑战中走了这么远，那么请他们使用不同的继承模式来实现事件发射器。这个额外的步骤将测试受访者对不同代码实现的适应程度。

```
var makeEventEmitter = function() {
  var obj = Object.create(prototype);

  obj.events = {};

  return obj;
};

prototype = {};

prototype.on = function(event, callback) {
  this.events[event] = callback;
};

prototype.trigger = function(event) {
  if (!this.events[event]) {
    throw new Error("Event doesn't exist");
  }

  return this.events[event]();
};
```

## 技术面试的标题

评估受访者在技术挑战中的表现时，有许多因素需要考虑。我在面试时会考虑以下因素:

*   一致性:缩进或空白的使用是否一致？
*   命名约定:变量的名字是描述性的吗？
*   测试:是否考虑了多个用例？
*   问题:受访者是否定义了问题的范围？
*   代码熟悉度:申请人是否使用本地方法，而不是重新创建它们？

## 结论

技术面试会给被面试者留下持久的印象。如果面试官的目标是让技术面试对他们和被面试者都有益，那么最好的方法就是问一些既公平又有意义的问题。如果面试官能够实现这个目标，那么对于被面试者来说，最坏的结果就是他们没有得到工作，但是他们带着一些新的有用的知识离开。这对所有相关人员来说都是一个不错的提议。

## 分享这篇文章