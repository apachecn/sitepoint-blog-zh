# AngularJS 指令实用指南-第二部分

> 原文：<https://www.sitepoint.com/practical-guide-angular-directives-part-two/>

本教程的第一部分提供了 AngularJS 指令的基本概述。在教程的最后，我们还学习了如何隔离指令的范围。本文将从第一部分结束的地方开始。首先，我们将看到如何在指令中访问父作用域的属性，同时保持一个隔离的作用域。接下来，我们将讨论如何通过探索诸如`controller`函数和跨结论这样的概念来为一个指令选择正确的范围。本文以一个笔记应用程序的演练结束。

## 独立范围属性和父范围属性之间的绑定

通常，隔离一个指令的作用域是很方便的，特别是当你操作许多作用域模型的时候。但是，您可能还需要访问指令中的一些父作用域属性，以便代码能够运行。好消息是 Angular 为您提供了足够的灵活性，可以通过绑定有选择地将父范围属性传递给指令。让我们重温一下我们的 [hello world 指令](http://plnkr.co/edit/14q6WxHyhWuVxEIqwww1?p=preview)，当有人在文本字段中键入颜色名称时，它会自动改变背景颜色。还记得我们隔离了指令的作用域，代码停止工作了吗？好吧，让我们现在就开始吧！

假设变量`app`被初始化，引用角度模块。该指令如下所示。

```
app.directive('helloWorld', function() {
return {
scope: {},
restrict: 'AE',
replace: true,
template: '<p style="background-color:{{color}}">Hello World</p>',
link: function(scope, elem, attrs) {
elem.bind('click', function() {
elem.css('background-color','white');
scope.$apply(function() {
scope.color = "white";
});
});
elem.bind('mouseover', function() {
elem.css('cursor', 'pointer');
});
}
};
});
```

下面的代码示例显示了利用指令的标记。

```
<body ng-controller="MainCtrl">
<input type="text" ng-model="color" placeholder="Enter a color"/>
<hello-world/>
</body>
```

该代码目前不起作用。因为我们有一个独立的作用域，所以指令模板中的表达式`{{color}}`根据这个作用域(而不是父作用域)进行计算。但是输入元素上的`ng-model`指令引用了父范围属性`color`。因此，我们需要一种方法来绑定这两个独立的父作用域属性。在 Angular 中，这种绑定可以通过在 HTML 中的 directive 元素上设置属性，在 directive 定义对象中配置`scope`属性来实现。让我们探索几种设置绑定的方法。

### 选项 1:使用`@`进行单向文本绑定

在如下所示的指令定义中，我们已经指定隔离范围属性`color`应该绑定到属性`colorAttr`，该属性应用于 HTML 中的指令。如果查看标记，可以看到表达式`{{color}}`被分配给了`color-attr`。当表达式的值改变时，属性`color-attr`也会改变。这又改变了隔离范围属性`color`。

```
app.directive('helloWorld', function() {
return {
scope: {
color: '@colorAttr'
},
....
// the rest of the configurations
};
});
```

更新后的标记如下所示。

```
<body ng-controller="MainCtrl">
<input type="text" ng-model="color" placeholder="Enter a color"/>
<hello-world color-attr="{{color}}"/>
</body>
```

我们称之为单向绑定，因为使用这种技术，您只能将字符串传递给属性(使用表达式，`{{}}`)。当父范围属性更改时，您的独立范围模型也会更改。您甚至可以在指令中观察这个 scope 属性，并在发生变化时触发任务。但是，反过来就不成立了！您不能通过操纵隔离范围来更改父范围模型。

**注意:**
如果隔离范围属性和属性名相同，可以这样写指令定义:

```
app.directive('helloWorld', function() {
return {
scope: {
color: '@'
},
....
// the rest of the configurations
};
});
```

该指令在 HTML 中调用如下:

```
<hello-world color="{{color}}"/>
```

### 选项 2:使用`=`进行双向绑定

让我们修改指令定义，如下所示。

```
app.directive('helloWorld', function() {
return {
scope: {
color: '='
},
....
// the rest of the configurations
};
});
```

像这样修改 HTML:

```
<body ng-controller="MainCtrl">
<input type="text" ng-model="color" placeholder="Enter a color"/>
<hello-world color="color"/>
</body>
```

与`@`不同，这种技术允许您为属性分配一个实际的范围模型，而不仅仅是简单的字符串。因此，您可以将从简单的字符串和数组到复杂的对象的值传递给独立的作用域。此外，还存在双向绑定。每当父范围属性更改时，相应的独立范围属性也会更改，反之亦然。像往常一样，您可以观察这个 scope 属性的变化。

### 选项 3:使用`&`来执行父作用域中的函数

有时有必要从具有隔离作用域的指令中调用父作用域中定义的函数。为了引用外部作用域中定义的函数，我们使用`&`。假设我们想从指令中调用一个函数`sayHello()`。下面的代码解释了它是如何实现的。

```
app.directive('sayHello', function() {
return {
scope: {
sayHelloIsolated: '&amp;'
},
....
// the rest of the configurations
};
});
```

指令在 HTML 中的用法如下:

```
<body ng-controller="MainCtrl">
<input type="text" ng-model="color" placeholder="Enter a color"/>
<say-hello sayHelloIsolated="sayHello()"/>
</body>
```

这个 [Plunker](http://plnkr.co/edit/k4scWKwtGBJw7lfKGqVJ?p=preview) 例子演示了这个概念。

## 父范围、子范围和独立范围

作为一个 Angular 初学者，在选择正确的指令范围时可能会感到困惑。默认情况下，指令不会创建新范围，而是使用父范围。但是在很多情况下这并不是我们想要的。如果您的指令大量操纵父作用域属性并创建新的属性，可能会污染该作用域。让所有的指令使用相同的父作用域并不是一个好主意，因为任何人都可以修改我们的作用域属性。因此，下面的指导方针可以帮助您为指令选择正确的范围。

1.  **父作用域**(`scope: false`)–这是默认情况。如果您的指令不操作父作用域属性，您可能不需要新的作用域。在这种情况下，使用父作用域是可以的。
2.  **子作用域**(`scope:true`)–这为一个指令创建了一个新的子作用域，该指令通常从父作用域继承而来。如果您在作用域上设置的属性和函数与其他指令和父作用域无关，您可能应该创建一个新的子作用域。这样，您就拥有了由父类定义的所有作用域属性和函数。
3.  **隔离范围**(`scope:{}`)——这就像一个沙盒！如果您要构建的指令是自包含的和可重用的，那么您需要这样做。您的指令可能会创建许多供内部使用的作用域属性和函数，这些属性和函数不应该被外部世界看到。如果是这样，最好有一个隔离的作用域。正如所料，独立范围不继承父范围。

## 跨结论

Transclusion 是一个特性，它可以让我们在任意内容周围包装一个指令。我们可以稍后根据正确的范围提取和编译它，最后将它放在指令模板中的指定位置。如果您在指令定义中设置了`transclude:true`,将会创建一个新的 transcluded 作用域，它通常继承自父作用域。如果您希望具有隔离范围的指令包含任意一段内容，并针对父范围执行它，可以使用 transclusion。

假设我们有一个这样注册的指令:

```
app.directive('outputText', function() {
return {
transclude: true,
scope: {},
template: '<div ng-transclude></div>'
};
});
```

它是这样使用的:

```
<div output-text>
<p>Hello {{name}}</p>
</div>
```

`ng-transclude`表示将包含的内容放在哪里。在这种情况下，DOM 内容`<p>Hello {{name}}</p>`被提取并放入`<div ng-transclude></div>`中。需要记住的重要一点是，表达式`{{name}}`插入的是父作用域中定义的属性，而不是独立作用域中定义的属性。这里有一个用来做实验的扑通扑通的位于[。如果你想了解更多关于示波器的知识，请阅读本文档。](http://plnkr.co/edit/YQBPB9cvGgJiYMuIjzTw?p=preview)

### `transclude:'element'`和`transclude:true`的区别

有时我们需要包含指令所应用的元素，而不仅仅是内容。在这些情况下，使用`transclude:'element'`。与`transclude:true`不同的是，它将元素本身包含在标有`ng-transclude`的指令模板中。作为 transclusion 的结果，您的`link`函数获得了一个预先绑定到正确指令范围的 transclude 链接函数。这个链接函数还被传递给另一个函数，该函数带有一个要被交叉包含的 DOM 元素的克隆。您可以执行一些任务，比如修改克隆并将其添加到 DOM 中。像`ng-repeat`这样的指令使用这种技术来重复 DOM 元素。看看下面的 [Plunker](http://plnkr.co/edit/yFLe7OXj2u8epHXe6a0s?p=preview) ，它使用这种技术重复一个 DOM 元素，并改变第二个实例的背景颜色。

还要注意，通过使用`transclude:'element'`，指令所应用的元素被转换成 HTML 注释。所以，如果你把`transclude:'element'`和`replace:false`结合起来，指令模板实际上被`innerHTML`添加到注释中——这意味着什么也没有发生！相反，如果您选择`replace:true`，指令模板将取代 HTML 注释，一切将按预期运行。将`replace:false`与`transclude:'element'`结合使用，对于想要重复 DOM 元素并且不想保留该元素的第一个实例(它被转换成一个注释)的情况很好。

## `controller`功能和`require`

如果您想让其他指令与您的指令通信，可以使用指令的`controller`功能。在某些情况下，您可能需要通过组合两个指令来创建特定的 UI 组件。例如，您可以将一个`controller`函数附加到一个指令上，如下所示。

```
app.directive('outerDirective', function() {
return {
scope: {},
restrict: 'AE',
controller: function($scope, $compile, $http) {
// $scope is the appropriate scope for the directive
this.addChild = function(nestedDirective) { // this refers to the controller
console.log('Got the message from nested directive:' + nestedDirective.message);
};
}
};
});
```

这段代码将名为`outerDirective`的`controller`附加到指令上。当另一个指令想要通信时，它需要声明它需要您的指令的`controller`实例。这是如下所示完成的。

```
app.directive('innerDirective', function() {
return {
scope: {},
restrict: 'AE',
require: '^outerDirective',
link: function(scope, elem, attrs, controllerInstance) {
//the fourth argument is the controller instance you require
scope.message = "Hi, Parent directive";
controllerInstance.addChild(scope);
}
};
});
```

标记看起来会像这样:

```
<outer-directive>
<inner-directive></inner-directive>
</outer-directive>
```

`require: '^outerDirective'`告诉 Angular 在元素及其父元素上搜索控制器。在这种情况下，找到的`controller`实例作为第四个参数传递给`link`函数。在我们的例子中，我们将嵌套指令的范围发送给了父级。为了进行试验，在浏览器控制台打开的情况下打开这个[按钮](http://plnkr.co/edit/NMWGE6l9p1tBZh3jCfKn?p=preview)。本[角度资源](http://docs.angularjs.org/guide/directive)的最后一节给出了一个很好的内部定向通信的例子。绝对是必读之作！

## 一款笔记应用

在本节中，我们将使用指令构建一个简单的笔记应用程序。我们将利用 HTML5 `localStorage`来存储笔记。最终产品将看起来像[这个](http://embed.plnkr.co/QvxI4LbqfUY3C3XQjN3m/preview)。我们将创建一个呈现记事本的指令。用户可以查看他/她所做笔记的列表。当他点击按钮`add new`时，记事本变得可编辑，并允许创建一个便笺。当点击`back`按钮时，注释会自动保存。在`localStorage`的帮助下，笔记被一个叫做`notesFactory`的工厂保存。工厂代码非常简单明了。所以，让我们只关注指令代码。

### 第一步

我们从注册指令`notepad`开始。

```
app.directive('notepad', function(notesFactory) {
return {
restrict: 'AE',
scope: {},
link: function(scope, elem, attrs) {
},
templateUrl: 'templateurl.html'
};
});
```

请注意关于指令的一些事情:

*   范围是隔离的，因为我们希望指令是可重用的。该指令将具有许多与外部无关的属性和功能。
*   该指令可以用作由`restrict`属性指定的属性或元素。
*   `link`功能最初为空。
*   该指令从`templateurl.html`获取其模板。

### 第二步

下面的 HTML 构成了指令的模板。

```
<div class="note-area" ng-show="!editMode">
<ul>
<li ng-repeat="note in notes|orderBy:'id'">
<a href="#" ng-click="openEditor(note.id)">{{note.title}}</a>
</li>
</ul>
</div>
<div id="editor" ng-show="editMode" class="note-area" contenteditable="true" ng-bind="noteText"></div>
<span><a href="#" ng-click="save()" ng-show="editMode">Back</a></span>
<span><a href="#" ng-click="openEditor()" ng-show="!editMode">Add Note</a></span>
```

需要注意的要点是:

*   `note`对象封装了`title`、`id`和`content`。
*   `ng-repeat`用于遍历`notes`并按照自动生成的`id`的升序排序。
*   我们将有一个属性`editMode`,它将指示我们所处的模式。在编辑模式下，该属性将为`true`，可编辑的`div`将可见。用户在这里写下注释。
*   如果`editMode`是`false`，我们处于查看模式并显示`notes`。
*   这两个按钮也根据`editMode`显示/隐藏。
*   `ng-click`指令用于对按钮点击做出反应。这些方法以及类似于`editMode`的属性将被添加到 scope 中。
*   可编辑的`div`被绑定到`noteText`，它保存用户输入的文本。如果您想编辑一个现有的注释，这个模型用注释内容初始化这个`div`。

### 第三步

让我们在我们的作用域中创建一个名为`restore()`的新函数，它将为我们的应用程序初始化各种控件。当`link`功能运行时，以及每次点击`save`按钮时，都会调用这个函数。

```
scope.restore = function() {
scope.editMode = false;
scope.index = -1;
scope.noteText = '';
};
```

我们在`link`函数中创建这个函数。`editMode`和`noteText`已经解释过了。`index`用于跟踪正在编辑的笔记。如果我们正在创建一个新的音符，`index`是-1。如果我们正在编辑一个现有的笔记，它包含那个`note`对象的`id`。

### 第四步

现在我们需要创建两个作用域函数来处理编辑和保存操作。

```
scope.openEditor = function(index) {
scope.editMode = true;

if (index !== undefined) {
scope.noteText = notesFactory.get(index).content;
scope.index = index;
} else {
scope.noteText = undefined;
}
};

scope.save = function() {
if (scope.noteText !== '') {
var note = {};

note.title = scope.noteText.length > 10 ? scope.noteText.substring(0, 10) + '. . .' : scope.noteText;
note.content = scope.noteText;
note.id = scope.index != -1 ? scope.index : localStorage.length;
scope.notes = notesFactory.put(note);
}

scope.restore();
};
```

这些功能的要点是:

*   `openEditor`准备编辑。如果我们正在编辑一个笔记，它会获取该笔记的内容，并通过`ng-bind`更新可编辑的`div`。
*   如果我们正在创建一个新的笔记，我们需要将`noteText`设置为`undefined`,这样当我们保存笔记时，观察器就会触发。
*   如果函数参数`index`未定义，这意味着用户将创建一个新的便笺。
*   `save`功能在`notesFactory`的帮助下保存便笺。保存后，它会刷新`notes`数组，这样观察器就可以检测到变化，笔记列表也可以更新。
*   `save`函数在最后调用`restore()`来重置控件，这样我们就可以从编辑模式返回到查看模式。

### 第五步

当`link`函数运行时，我们初始化`notes`数组并将`keydown`事件绑定到可编辑的`div`，这样我们的`noteText`模型与`div`内容保持同步。我们使用这个`noteText`来保存笔记内容。

```
var editor = elem.find('#editor');

scope.restore(); // initialize our app controls
scope.notes = notesFactory.getAll(); // load notes

editor.bind('keyup keydown', function() {
scope.noteText = editor.text().trim();
});
```

### 第六步

最后，像使用任何其他 HTML 元素一样使用该指令，并开始记笔记！

```
<h1 class="title">The Note Making App</h1>
<notepad/>
```

## 结论

需要注意的重要一点是，无论我们用 jQuery 做什么，都可以用 Angular 指令用更少的代码来完成。因此，在使用 jQuery 之前，尝试弄清楚同样的事情是否可以在没有任何 DOM 操作的情况下以更好的方式完成。尽量减少 jQuery 与 Angular 的使用。

关于做笔记的演示，删除笔记的功能已经被故意忽略了。鼓励读者尝试并实现这一特性。演示的源代码可以从 [GitHub](https://github.com/jsprodotcom/source/blob/master/AngularJS_Note_Taker-source_code.zip "Demo Source - GitHub") 下载。

## 分享这篇文章