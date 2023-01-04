# Darin Haener 为您现场讲解 ES2015

> 原文：<https://www.sitepoint.com/a-live-lesson-in-diving-into-es2015-with-darin-haener/>

## 现场课程—走进 ES2015

SitePoint Premium 的第一堂现场课于本月在 Darin Haener 的指导下进行，他将带您浏览他的高级课程的几个部分，[进入 ES2015](https://www.sitepoint.com/premium/courses/diving-into-es2015-2924) 。观众可以在达林讲解自己的部分课程时向他提问，并尽可能提供最准确的答案。

如果你还没看过 Darin Haener 录制的[现场课程](https://www.sitepoint.com/premium/screencasts/a-live-lesson-in-diving-into-es2015),那就跳过去吧！在本帖中，我们将重点介绍现场课程中提出的要点和回答的问题。

## SitePoint Premium 的实时课程

这些现场课程是 SitePoint 的新内容，旨在帮助您全面理解 SitePoint Premium 各个部分的内容，并让您在课程期间通过聊天与其他开发人员进行互动。它们也是对 SitePoint Premium 的课程是什么样的以及课程中可获得的信息种类的极好预览。无论你是否已经学过这门课，或者你是否从未看过这门课，你都可以参加这个活动并学到很多东西。欢迎所有人！

![Diving into ES2015](img/1ba5b09ca629598fa2ab8f7a15407f69.png)

## 走进 ES2015

课程*深入 ES2015* 涵盖了下一代 JavaScript——es 2015。ES2015 正在成为浏览器的标准，JavaScript 开发人员了解这些新标准至关重要。有了 ES2015 的全面基础，您将准备好使用各种新工具来处理您的项目，因为这些工具已成为 web 标准！本实时课程涵盖了本课程的几个部分。

### 箭头功能

达林解释说，在 ES2015 中，使用[箭头函数](https://www.sitepoint.com/javascript-arrow-functions/)，当你使用箭头函数时，它会自动将该函数绑定到词汇`this`。这一点，以及箭头函数可以产生的压缩响应，有助于更快地编写代码，并使您的代码更容易阅读，箭头函数是 ES2015 如此受开发人员欢迎的原因之一。

### 解构

析构是 ES2015 的一个惊人功能，提供了一种非常简单的方法来分配变量。Darin 介绍了使用析构作为从对象中提取元素的简便方法，而不必键入完整的路径。此外，重构还允许轻松设置数组和对象中的默认值，而不会导致问题，您也将在视频中感受到这一点。

### 参数处理

就参数处理而言，Darin 解释说，在 ES2015 中，参数可以通过各种新的方式进行操作，例如在函数调用中为参数提供内联默认值，而不是在函数本身内部进行处理。它还允许给定 rest 参数，允许根据需要插入尽可能多的参数作为数组值，并允许在数组中使用 spread 运算符。

## 观众对达林的提问

### 问:一旦浏览器完全支持 ES2015，我们是否必须完全跳到 ES2015，并停止像以前那样编写 JavaScript？

**达林·海纳**:大部分浏览器都已经采用了 ES2015。如果你的代码还在运行，那么是的(没问题)。我不知道任何被否决或删除的东西。据我所知，只是增加了一些。

我认为你继续用以前的方式写代码可能是安全的，尽管我不知道我会推荐它，因为你会落后。

### 问:const 和 let 有什么区别？

**达林·海纳**:首先，`const`和`let`、`const`和`let`的作用域都是一样的，但是不同的是`const`无法重新定义。如果我，在我的代码中，说`const a = 1`，然后在同一代码块中，我试着说`const a = 3`，这将抛出一个错误。它不允许我这么做，或者即使我试图说`a = 4`，甚至不说`const`，它也不允许我重新赋值那个变量。`let`会让你这么做的。因此，如果你说`let b = 1`，那么稍后在同一个代码块中，你可以重新分配它并说`b = 3`，这是允许的。

使用`const`和`let`，可以得到相同类型的作用域，只是行为略有不同。

### 问:我通常看到用大写字母定义的常量变量—例如，const MYAGE = 20。这是一个要求还是仅仅是一个好的实践？

Darin Haener :这肯定不是一个要求，但可能是一个很好的实践。几乎每种编程语言都有常量作为大写字母。

### 问:学习 TypeScript 还是坚持使用 JavaScript 值得吗？

**达林·海纳**:据我所知，TypeScript 更严格一点。它有严格的打字系统。我想说，如果你更熟悉像 C 这样使用严格类型的语言，那么 TypeScript 可能会让你感觉更舒服和熟悉，所以继续使用它。

### 问:你对 ES6 的 coffeescript 有什么看法？

Darin Haener :我过去经常使用 coffeescript，但我认为 ES6(或 2015)解决了许多 coffeescript 旨在解决的相同问题，我个人对 coffeescript 的一个问题(这只是我的看法)是有意义的缩进。我个人不喜欢那样。

所以我喜欢 JavaScript 的结构，必须使用括号和大括号来增强代码的可读性。所以从我的角度来看，我不会把它们混在一起，而是各取所需。

## 包装它

如果您未能参与现场课程，并且您尚未观看[录像](https://www.sitepoint.com/premium/screencasts/a-live-lesson-in-diving-into-es2015)，请立即观看！[The Diving into es 2015](https://www.sitepoint.com/premium/courses/diving-into-es2015-2924)site point Premium 课程可供您阅读，如果您正在学习或计划在未来学习，Darin 的现场课程是一个非常宝贵的补充！

我们很高兴他能上第一堂现场课，由 SitePoint 为您带来！我们希望这对许多注册观看直播和提问的 JavaScript 开发人员是一次有益的体验——对那些打算在 SitePoint 上观看录制版本的人也是如此！

## 分享这篇文章