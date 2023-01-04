# 编译成 JavaScript 的 10 种语言

> 原文：<https://www.sitepoint.com/10-languages-compile-javascript/>

**本文列出了十种有趣的语言，它们可以编译成 JavaScript，在浏览器或 Node.js 等平台上执行。**

现代应用程序与简单的网站有着不同的需求。但是浏览器是一个有(大部分)固定可用技术的平台，JavaScript 仍然是 web 应用程序的核心语言。任何需要在浏览器中运行的应用程序都必须用该语言实现。

我们都知道 JavaScript 并不是每项任务的最佳语言，当涉及到复杂的应用程序时，它可能会有所欠缺。为了避免这个问题，一些新的语言和现有语言的 transpilers 被创造出来，它们都可以产生可以在浏览器中工作的代码，而不需要编写任何 JavaScript 代码，也不需要考虑语言的局限性。

## 镖

Dart 是一种经典的面向对象的语言，其中一切都是对象，任何对象都是一个类的实例(对象也可以充当函数。)它是专门为浏览器、服务器和移动设备构建的应用程序。它由谷歌维护，是驱动下一代 AdWords UI 的语言，这是谷歌在收入方面最重要的产品，这本身就是其规模力量的证明。

该语言可以翻译成 JavaScript 在浏览器中使用，或者直接由 Dart VM 解释，这也允许您构建服务器应用程序。可以使用 Flutter SDK 开发移动应用程序。

复杂的应用程序还需要一套专门为该任务设计的成熟的库和语言特性，Dart 包括所有这些。一个流行库的例子是 [AngularDart](https://webdev.dartlang.org/angular/) ，Dart 的 Angular 版本。

它允许您编写类型安全的代码，而不会造成太大的干扰。你可以写类型，但是你不需要这么做，因为它们可以被推断出来。这允许快速原型化而不必过多考虑细节，但是一旦你有了工作，你可以添加类型使它更健壮。

关于 VM 中的并发编程，Dart 没有使用共享内存线程(Dart 是单线程的)，而是使用他们所谓的**隔离**，使用他们自己的内存堆，在内存堆中使用消息实现通信。在浏览器中，情况略有不同:不是创建新的隔离，而是创建新的**工人**。

```
// Example extracted from dartlang.org

import 'dart:async';
import 'dart:math' show Random;

main() async {
  print('Compute π using the Monte Carlo method.');
  await for (var estimate in computePi()) {
    print('π ≅ $estimate');
  }
}

/// Generates a stream of increasingly accurate estimates of π.
Stream<double> computePi({int batch: 1000000}) async* {
  var total = 0;
  var count = 0;
  while (true) {
    var points = generateRandom().take(batch);
    var inside = points.where((p) => p.isInsideUnitCircle);
    total += batch;
    count += inside.length;
    var ratio = count / total;
    // Area of a circle is A = π⋅r², therefore π = A/r².
    // So, when given random points with x ∈ <0,1>,
    // y ∈ <0,1>, the ratio of those inside a unit circle
    // should approach π / 4\. Therefore, the value of π
    // should be:
    yield ratio * 4;
  }
}

Iterable<Point> generateRandom([int seed]) sync* {
  final random = new Random(seed);
  while (true) {
    yield new Point(random.nextDouble(), random.nextDouble());
  }
}

class Point {
  final double x, y;
  const Point(this.x, this.y);
  bool get isInsideUnitCircle => x * x + y * y <= 1;
} 
```

更多阅读，我推荐 Dart 的[入门 Dart](https://www.dartlang.org/guides/get-started) 资源。

## 以打字打的文件

[TypeScript](https://www.typescriptlang.org/) 是 JavaScript 的超集。有效的 JavaScript 程序也是有效的 TypeScript，但是增加了静态类型。该编译器还可以作为 ES2015+到当前实现的 transpiler，因此您总是可以获得最新的功能。

与许多其他语言不同，TypeScript 完整地保留了 JavaScript 的精神，只是添加了一些特性来提高代码的可靠性。这些是类型注释和其他与类型相关的功能，它们使编写 JavaScript 变得更加愉快，这要归功于静态分析器等专门工具的启用和其他有助于重构过程的工具。此外，类型的添加改善了应用程序不同组件之间的接口。

支持类型推断，所以不用从头开始写所有的类型。您可以编写快速的解决方案，然后添加所有类型以对您的代码有信心。

TypeScript 还支持高级类型，如交集类型、联合类型、类型别名、区别联合和类型保护。你可以在 [TypeScript 文档](https://www.typescriptlang.org/docs)站点的[高级类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html)页面查看所有这些。

如果使用 React，还可以通过添加 React 类型来支持 JSX:

```
class Person {
  private name: string;
  private age: number;
  private salary: number;

  constructor(name: string, age: number, salary: number) {
    this.name = name;
    this.age = age;
    this.salary = salary;
  }

  toString(): string {
    return `${this.name} (${this.age}) (${this.salary})`;
  }
} 
```

有关 typeScript 的更多信息，请查看 SitePoint 的[typeScript](https://www.sitepoint.com/introduction-to-typescript/)入门文章。

## 榆树

Elm 是一种纯函数式编程语言，可以编译成 JavaScript、HTML 和 CSS。您可以用 Elm 构建一个完整的站点，这使它成为 React 等 JavaScript 框架的一个很好的替代品。用它构建的应用程序会自动使用虚拟 DOM 库，这使得它非常快。一个很大的好处是内置的架构，让您忘记数据流，而专注于数据声明和逻辑。

在 Elm 中，所有函数都是纯函数，这意味着对于给定的输入，它们总是返回相同的输出。除非您指定，否则他们不能做任何其他事情。例如，要访问一个远程 API，你需要创建*命令*函数来与外部世界通信，并创建*订阅*来监听响应。纯度的另一个要点是，值是不可变的:当你需要某样东西时，你创建新的值，而不是修改它们。

Elm 的采用可以是渐进的。使用*端口*与 JavaScript 和其他库进行通信是可能的。尽管 Elm 还没有达到版本 1，但它正被用于复杂和大型的应用程序，这使它成为复杂应用程序的可行解决方案。

Elm 最吸引人的特性之一是初学者友好的编译器，它不是产生难以阅读的消息，而是生成帮助您修复代码的代码。如果你正在学习这门语言，编译器本身会有很大的帮助。

```
module Main exposing (..)
 import Html exposing (..)

-- MAIN

main : Program Never Model Msg
main =
    Html.program
        { init = init
        , update = update
        , view = view
        , subscriptions = subscriptions
        }

-- INIT

type alias Model = String

init : ( Model, Cmd Msg )
init = ( "Hello World!", Cmd.none )

-- UPDATE

type Msg
    = DoNothing

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        DoNothing ->
            ( model, Cmd.none )

-- VIEW

view : Model -> Html Msg
view model =
    div [] [text model]

-- SUBSCRIPTIONS

subscriptions : Model -> Sub Msg
subscriptions model =
    Sub.none 
```

如果您想了解更多信息，SitePoint 有一篇方便的[Elm](https://www.sitepoint.com/functional-reactive-programming-elm-introduction/)入门文章。

## 纯脚本

PureScript 是一种纯函数式的强类型编程语言，由 Phil Freeman 创建。它旨在提供与可用 JavaScript 库的强大兼容性，在精神上类似于 Haskell，但保持 JavaScript 的核心地位。

PureScript 的一个优点是它的极简主义。它不包括任何在其他语言中被认为是必不可少的功能库。例如，您可以使用特定的库来完成任务，而不是在编译器本身中包含生成器和承诺。您可以为您需要的特性选择您想要的实现，这允许在使用 PureScript 时获得高效和个性化的体验，同时保持生成的代码尽可能小。

它的编译器的另一个与众不同的特性是能够在保持与 JavaScript 兼容的同时生成清晰可读的代码，包括库和工具。

和其他语言一样，PureScript 也有自己的[构建工具，名为 Pulp](https://github.com/purescript-contrib/pulp) ，可以和 Gulp 相比，但针对的是用这种语言编写的项目。

关于类型系统——不像 Elm，它是另一种类似 ML 的语言——pure script 支持高级类型特性，比如来自 Haskell 的[高级类型](https://en.wikipedia.org/wiki/Kind_%28type_theory%29)和类型类，允许创建复杂的抽象:

```
module Main where

import Prelude
import Data.Foldable (fold)
import TryPureScript

main =
    render $ fold
      [ h1 (text "Try PureScript!")
      , p (text "Try out the examples below, or create your own!")
      , h2 (text "Examples")
      , list (map fromExample examples)
      ]
  where
    fromExample { title, gist } =
      link ("?gist=" <> gist) (text title)

    examples =
      [ { title: "Algebraic Data Types"
        , gist: "37c3c97f47a43f20c548"
        }
      , { title: "Loops"
        , gist: "cfdabdcd085d4ac3dc46"
        }
      , { title: "Operators"
        , gist: "3044550f29a7c5d3d0d0"
        }
      ] 
```

要进一步使用 PureScript，请查看 GitHub 上的[pure script](https://github.com/purescript/documentation/blob/master/guides/Getting-Started.md)入门指南。

## 咖啡脚本

CoffeeScript 是一种语言，旨在展示 JavaScript 的优点，同时提供更清晰的语法并保持语义不变。虽然这种语言的受欢迎程度近年来一直在下降，但它正在改变方向，最近获得了一个新的主要版本，为 ES2015+功能提供支持。

您在 CoffeeScript 中编写的代码被直接翻译成可读的 JavaScript 代码，并保持与现有库的兼容性。从版本 2 开始，编译器生成与最新版本的 ECMAScript 兼容的代码。例如，每次使用一个`class`，就会在 JavaScript 中得到一个`class`。此外，如果你使用 React，有一个好消息:JSX 与 CoffeeScript 兼容。

编译器的一个非常与众不同的特性是处理以[文字风格](https://en.wikipedia.org/wiki/Literate_programming)编写的代码的能力，在这种情况下，不是在代码中强调，而是额外添加注释，而是首先编写注释，代码只是偶尔出现。这种编程风格是由 Donald Knuth 引入的，它使代码文件非常类似于一篇技术文章。

与其他语言不同，CoffeeScript 代码可以使用库在浏览器中直接解释。因此，如果您想创建一个快速测试，您可以在`text/coffeescript`脚本标签中编写您的代码，并包含编译器，它将动态地将代码翻译成 JavaScript:

```
# Assignment:
number   = 42
opposite = true

# Conditions:
number = -42 if opposite

# Functions:
square = (x) -> x * x

# Arrays:
list = [1, 2, 3, 4, 5]

# Objects:
math =
  root:   Math.sqrt
  square: square
  cube:   (x) -> x * square x

# Splats:
race = (winner, runners...) ->
  print winner, runners

# Existence:
alert "I knew it!" if elvis?

# Array comprehensions:
cubes = (math.cube num for num in list) 
```

CoffeeScript 网站有一个方便的[资源，可以帮助您快速入门。](http://coffeescript.org/v2/#coffeescript-2)

## ClojureScript

ClojureScript 是一个将 Clojure 编程语言翻译成 JavaScript 的编译器。它是一种通用的函数式语言，具有动态类型和对不可变数据结构的支持。

它是这个列表中唯一一个属于 Lisp 编程语言家族的语言，自然地，它拥有许多相同的特性。例如，代码可以被视为数据，并且宏系统是可用的，这使得元编程技术成为可能。与其他 Lisps 不同，Clojure 支持不可变的数据结构，这使得副作用的管理更加容易。

由于使用了括号，这种语法看起来可能会让新手感到害怕，但它这样做有着深刻的原因，从长远来看，您肯定会喜欢它。语法中的极简主义及其语法抽象能力使 Lisp 成为解决需要高度抽象的问题的强大工具。

尽管 Clojure 主要是一种函数式语言，但它不像 PureScript 或 Elm 那样纯粹。副作用仍然会发生，但是其他功能特征仍然存在。

ClojureScript 使用 Google Closure 进行代码优化，并且兼容现有的 JavaScript 库:

```
; Extracted from https://github.com/clojure/clojurescript/blob/master/samples/dom/src/dom/test.cljs

(ns dom.test
  (:require [clojure.browser.event :as event]
            [clojure.browser.dom   :as dom]))

(defn log [& args]
  (.log js/console (apply pr-str args)))

(defn log-obj [obj]
  (.log js/console obj))

(defn log-listener-count []
  (log "listener count: " (event/total-listener-count)))

(def source      (dom/get-element "source"))
(def destination (dom/get-element "destination"))

(dom/append source
            (dom/element "Testing me ")
            (dom/element "out!"))

(def success-count (atom 0))

(log-listener-count)

(event/listen source
              :click
              (fn [e]
                (let [i (swap! success-count inc)
                      e (dom/element :li
                                     {:id "testing"
                                      :class "test me out please"}
                                     "It worked!")]
                  (log-obj e)
                  (log i)
                  (dom/append destination
                              e))))

(log-obj (dom/element "Text node"))
(log-obj (dom/element :li))
(log-obj (dom/element :li {:class "foo"}))
(log-obj (dom/element :li {:class "bar"} "text node"))
(log-obj (dom/element [:ul [:li :li :li]]))
(log-obj (dom/element :ul [:li :li :li]))
(log-obj (dom/element :li {} [:ul {} [:li :li :li]]))
(log-obj (dom/element [:li {:class "baz"} [:li {:class "quux"}]]))

(log-obj source)
(log-listener-count) 
```

要了解更多信息，请访问 ClojureScript 网站的[ClojureScript](https://clojurescript.org/guides/quick-start)资源入门。

## Scala.js

Scala.js 是一个将 Scala 编程语言翻译成 JavaScript 的编译器。Scala 是一种语言，旨在将面向对象和函数式编程的思想融合到一种语言中，以创建一种功能强大且易于采用的工具。

作为一种强类型语言，您可以获得带有部分类型推断的灵活类型系统的好处。大多数值都可以推断出来，但是函数参数仍然需要显式的类型注释。

虽然支持许多常见的面向对象模式(例如，每个值都是一个对象，操作都是方法调用)，但是您也可以获得一些功能特性，比如对一级函数和不可变数据结构的支持。

Scala.js 的一个特殊优势是，您可以从熟悉的面向对象的方法开始，然后根据需要以自己的速度转移到功能更强大的方法，而不必做很多工作。此外，现有的 JavaScript 代码和库与您的 Scala 代码兼容。

初学 Scala 的开发人员会发现这种语言与 JavaScript 没有太大区别。比较以下等效代码:

```
// JavaScript
var xhr = new XMLHttpRequest();

xhr.open("GET",
  "https://api.twitter.com/1.1/search/" +
  "tweets.json?q=%23scalajs"
);
xhr.onload = (e) => {
  if (xhr.status === 200) {
    var r = JSON.parse(xhr.responseText);
    $("#tweets").html(parseTweets(r));
  }
};
xhr.send(); 
```

```
// Scala.js
val xhr = new XMLHttpRequest()

xhr.open("GET",
  "https://api.twitter.com/1.1/search/" +
  "tweets.json?q=%23scalajs"
)
xhr.onload = { (e: Event) =>
  if (xhr.status == 200) {
    val r = JSON.parse(xhr.responseText)
    $("#tweets").html(parseTweets(r))
  }
}
xhr.send() 
```

查看 Scala.js [开始使用 Scala.js](https://www.scala-js.org/tutorial/basic/) 文档了解更多信息。

## 理由

Reason 是由脸书创建和维护的语言，它为 OCaml 编译器提供了一种新的语法，代码可以被翻译成 JavaScript 和本机代码。

作为 ML 家族的一部分和函数式语言本身，它自然提供了一个强大而灵活的类型系统，包括推理、代数数据类型和模式匹配。它还支持不可变数据类型和参数多态性(在其他语言中也称为*泛型*),但是和 OCaml 一样，它也支持面向对象编程。

通过 [bucklescript](https://github.com/BuckleScript/bucklescript) 绑定，可以使用现有的 JavaScript 库。您还可以在原因代码旁边混合使用 JavaScript。插入的 JavaScript 代码不会被严格检查，但是对于快速修复或原型来说，它工作得很好。

如果你是 React 开发人员，[绑定是可用的](https://github.com/reasonml/reason-react)，并且该语言也支持 JSX:

```
/* A type variant being pattern matched */

let possiblyNullValue1 = None;
let possiblyNullValue2 = Some "Hello@";

switch possiblyNullValue2 {
| None => print_endline "Nothing to see here."
| Some message => print_endline message
};

/* Parametrized types */

type universityStudent = {gpa: float};
type response 'studentType = {status: int, student: 'studentType};
let result: response universityStudent = fetchDataFromServer ();

/* A simple typed object */

type payload = Js.t {.
  name: string,
  age: int
};
let obj1: payload = {"name": "John", "age": 30}; 
```

查看原因网站的[开始使用原因](https://reasonml.github.io/guide/javascript/quickstart)指南了解更多信息。

## 哈克斯

Haxe 是一种多范式编程语言，它的编译器既可以生成二进制文件，也可以生成其他语言的源代码。

尽管 Haxe 提供了支持类型推断的严格类型系统，但是如果目标语言支持的话，它也可以作为一种动态语言工作。同样，它支持多种编程风格，如面向对象、泛型和函数式。

当您编写 Haxe 代码时，您可以针对几个平台和语言进行编译，而不必进行相当大的更改。特定于目标的代码块也是可用的。

您可以用相同的代码在 Haxe 中编写后端和前端，并使用 [Haxe Remoting](https://haxe.org/manual/std-remoting-connection.html) 实现通信，对于同步和异步连接都是如此。

正如所料，Haxe 代码与现有的库兼容，但它也提供了一个成熟的标准库:

```
// Example extracted from http://code.haxe.org

extern class Database {
  function new();
  function getProperty<T>(property:Property<T>):T;
  function setProperty<T>(property:Property<T>, value:T):Void;
}

abstract Property<T>(String) {
  public inline function new(name) {
    this = name;
  }
}

class Main {
  static inline var PLAYER_NAME = new Property<String>("playerName");
  static inline var PLAYER_LEVEL = new Property<Int>("playerLevel");

  static function main() {
    var db = new Database();

    var playerName = db.getProperty(PLAYER_NAME);
    trace(playerName.toUpperCase());

    db.setProperty(PLAYER_LEVEL, 1);
  }
} 
```

查看 haaxe 网站的[haaxe](https://haxe.org/documentation/introduction/language-introduction.html)入门页面了解更多信息。

## 偷

Nim 是一种静态类型的多范式编程语言，具有极简和空白敏感的语法，可以编译成 JavaScript 以及 C、C++。

这种语言本身非常小，但是它的元编程能力使得自己实现其他语言内置的特性很有吸引力。它的构建块是宏、模板和泛型，使用它们你可以实现从简单特性到不同范例的东西。本着 Lisp 的精神，这使得 Nim 成为一种非常通用的语言，可以适应您的需要。

Nim 的语法抽象特性允许您根据自己的问题修改语言，使真正的[DSL](https://en.wikipedia.org/wiki/Domain-specific_language)成为可能。如果您有专门的任务要解决，您可以获得更高水平的表现力:

```
# Reverse a string
proc reverse(s: string): string =
  result = ""
  for i in countdown(high(s), 0):
    result.add s[i]

var str1 = "Reverse This!"
echo "Reversed: ", reverse(str1)

# Using templates
template genType(name, fieldname: expr, fieldtype: typedesc) =
  type
    name = object
      fieldname: fieldtype

genType(Test, foo, int)

var x = Test(foo: 4566)
echo(x.foo) # 4566 
```

Nim 站点上有一些有用的[入门](https://nim-lang.org/documentation.html)文档来获取更多信息。

## 结论

如果 JavaScript 不是您最喜欢的语言，您仍然可以创建 web 应用程序，而不必遭受该技术的缺点。创建这些应用程序的选项可以满足各种各样的需求，从 PureScript 这样的纯函数式语言到 Dart 这样的面向对象语言。如果您想要的不仅仅是一对一的语言翻译，您可以选择 Elm，它为您提供了虚拟 DOM 和内置架构等工具。

你尝试过这篇文章中的语言吗，或者你有什么可以推荐的吗？请在评论中告诉我们！

## 分享这篇文章