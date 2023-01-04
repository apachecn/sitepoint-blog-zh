# AngularJS 测试提示:测试指令

> 原文：<https://www.sitepoint.com/angular-testing-tips-testing-directives/>

单元测试是软件开发的重要组成部分，因为它们可以帮助你发布更少的错误代码。测试是提高代码质量必须做的几件事情之一。AngularJS 创建时就考虑到了测试，在框架上编写的任何代码都可以很容易地进行测试。

在我上一篇关于测试的文章中，[我介绍了单元测试控制器、服务和提供者](https://www.sitepoint.com/unit-testing-angularjs-services-controllers-providers/)。本文继续讨论使用指令进行测试。指令不同于其他组件，因为它们不是在 JavaScript 代码中作为对象使用，而是在应用程序的 HTML 模板中使用。我们编写指令来执行 DOM 操作，我们不能在单元测试中忽略它们，因为它们起着重要的作用。此外，它们直接影响应用程序的可用性。

我鼓励你去看看过去关于在 AngularJS 测试中模仿依赖关系的文章，因为我们将在这里使用那篇文章中的一些技术。如果你想使用本教程中开发的代码，你可以看看我为你设置的 GitHub 库。

## 测试指令

指令是 AngularJS 中最重要也是最复杂的组件。测试指令很棘手，因为它们不像函数一样被调用。在应用程序中，指令以声明方式应用于 HTML 模板。当模板被编译并且用户与指令交互时，它们的动作被执行。当执行单元测试时，我们需要自动执行用户操作，并手动编译 HTML 以测试指令的功能。

### 设置对象以测试指令

就像用任何语言或使用任何框架测试任何逻辑一样，我们需要在开始测试指令之前获得所需对象的引用。这里要创建的关键对象是一个包含要测试的指令的元素。我们需要用指定的指令编译一段 HTML，以使指令生效。例如，考虑以下指令:

```
angular.module('sampleDirectives', []).directive('firstDirective', function() {
  return function(scope, elem){
    elem.append('<span>This span is appended from directive.</span>');
  };
});
```

指令的生命周期将被启动，编译和链接功能将被执行。我们可以使用`$compile`服务手工编译任何 HTML 模板。下面的`beforeEach`块编译上面的指令:

```
var compile, scope, directiveElem;

beforeEach(function(){
  module('sampleDirectives');

  inject(function($compile, $rootScope){
    compile = $compile;
    scope = $rootScope.$new();
  });

  directiveElem = getCompiledElement();
});

function getCompiledElement(){
  var element = angular.element('<div first-directive></div>');
  var compiledElement = compile(element)(scope);
  scope.$digest();
  return compiledElement;
}
```

编译时，指令的生命周期就开始了。在下一个摘要周期之后，指令对象将处于与它在页面上出现时相同的状态。

如果指令依赖于任何服务来实现其功能，那么在编译指令之前必须模拟这些服务，以便可以在测试中检查对任何服务方法的调用。我们将在下一节看到一个例子。

### 测试链接功能

链接功能是指令定义对象(DDO)最常用的属性。它包含了指令的大部分核心逻辑。这个逻辑包括简单的 DOM 操作、监听发布/订阅事件、观察对象或属性的变化、调用服务、处理 UI 事件等等。我们将尝试涵盖这些场景中的大部分。

#### DOM 操作

让我们从上一节定义的指令的情况开始。该指令将一个`span`元素添加到该指令所应用的元素的内容中。可以通过在指令中找到`span`来测试它。下面的测试用例断言了这种行为:

```
it('should have span element', function () {
  var spanElement = directiveElem.find('span');
  expect(spanElement).toBeDefined();
  expect(spanElement.text()).toEqual('This span is appended from directive.');
});
```

#### 观察者

由于指令在作用域的当前状态下工作，当作用域的状态改变时，它们应该有观察器来更新指令。观察器的单元测试必须操作数据，并通过调用`$digest`强制观察器运行，它必须在摘要周期后检查指令的状态。

下面的代码是上述指令的一个略微修改的版本。它使用`scope`上的一个字段来绑定`span`中的文本:

```
angular.module('sampleDirectives').directive('secondDirective', function(){
  return function(scope, elem){
    var spanElement = angular.element('<span>' + scope.text + '</span>');
    elem.append(spanElement);

    scope.$watch('text', function(newVal, oldVal){
      spanElement.text(newVal);
    });
  };
});
```

测试该指令类似于第一个指令；除了应对照`scope`上的数据进行验证，并应检查更新。下面的测试用例验证指令的状态是否改变:

```
it('should have updated text in span', function () 
  scope.text = 'some other text';
  scope.$digest();
  var spanElement = directiveElem.find('span');
  expect(spanElement).toBeDefined();
  expect(spanElement.text()).toEqual(scope.text);
});
```

同样的技术也可以用来测试观察者的属性。

#### DOM 事件

事件在任何基于 UI 的应用程序中的重要性迫使我们确保它们能够正常工作。基于 JavaScript 的应用程序的优势之一是大多数用户交互都可以通过 API 进行测试。可以使用 API 测试事件。我们可以使用 jqLite API 触发事件，并测试事件内部的逻辑。

考虑以下指令:

```
angular.module('sampleDirectives').directive('thirdDirective', function () {
  return {
      template: '<button>Increment value!</button>',
      link: function (scope, elem) {
        elem.find('button').on('click', function(){
          scope.value++;
        });
      }
    };
  });
```

该指令在每单击一次`button`元素时将`value`属性的值递增 1。该指令的测试用例必须使用 jqLite 的`triggerHandler`触发 click 事件，然后检查值是否增加。这是测试前面代码的方法:

```
it('should increment value on click of button', function () {
  scope.value=10;
  var button = directiveElem.find('button');

  button.triggerHandler('click');
  scope.$digest();

  expect(scope.value).toEqual(11);
});
```

除了这里提到的情况，link 函数还包含涉及与服务或发布/订阅范围事件交互的逻辑。要测试这些案例，您可以遵循我在上一篇文章中讨论的技术。这里也可以应用相同的技术。

编译块的职责类似于链接。唯一的区别是编译块不能使用或操作`scope`，因为在编译运行时范围是不可用的。可以通过检查呈现元素的 HTML 来测试编译块应用的 DOM 更新。

### 测试指令的模板

模板可以通过两种方式应用于指令:使用内联模板或使用文件。我们可以验证模板是否应用于指令，以及模板中是否包含某些元素或指令。

带有内联模板的指令更容易测试，因为它位于同一个文件中。用从文件引用的模板测试一个指令是很棘手的，因为这个指令向`templateUrl`发出一个`$httpBackend`请求。将这个模板添加到`$templateCache`中使得测试任务变得更加容易，并且模板也易于共享。这可以通过使用 [grunt-html2js grunt 任务](https://www.npmjs.com/package/grunt-html2js)来完成。

`grunt-html2js`非常易于配置和使用。它需要 html 文件的源路径和结果脚本必须写入的目标路径。以下是示例代码中使用的配置:

```
html2js:{
  main: {
    src: ['src/directives/*.html'],
    dest: 'src/directives/templates.js'
  }
}
```

现在，我们需要做的就是在代码中引用这个任务生成的模块。默认情况下，`grunt-html2js`生成的模块名称为`templates-main`，但您可以修改。

考虑以下指令:

```
angular.module('sampleDirectives', ['templates-main'])
.directive('fourthDirective', function () {
  return {
    templateUrl: 'directives/sampleTemplate.html'
  };
});
```

模板的内容:

```
<h3>Details of person {{person.name}}<h3>
<another-directive></another-directive>
```

模板有`another-directive`元素，这是另一个指令，也是模板的重要部分。没有`anotherDirective`指令，`fourthDirective`不会按预期工作。因此，我们必须在指令编译后验证以下内容:

1.  如果模板应用于指令元素内部
2.  如果模板包含`another-directive`元素

以下是演示这些情况的测试:

```
it('should applied template', function () {
  expect(directiveElem.html()).not.toEqual('');
});

it('should have another-person element', function () {
  expect(directiveElem.find('another-directive').length).toEqual(1);
});
```

您不需要为指令模板中的每个元素编写测试。如果您觉得模板中的某个元素或指令是强制性的，没有它，指令就不完整，那么添加一个测试来检查这种组件的存在。这样做，如果有人不小心删除了它，您的测试就会抱怨。

### 测试指令的范围

指令的范围可以是下列之一:

1.  与周围元素的范围相同
2.  从周围元素的范围继承
3.  隔离范围

在第一种情况下，您可能不想测试作用域，因为当指令使用相同的作用域时，它不应该修改作用域的状态。但在其他情况下，该指令可能会向驱动指令行为的范围添加一些字段。我们需要测试这些案例。

让我们以一个使用隔离作用域的指令为例。下面是我们必须测试的指令:

```
angular.module('sampleDirectives').directive('fifthDirective', function () {
  return {
    scope:{
      config: '=',
      notify: '@',
      onChange:'&'
    }
  }
};
})
```

在该指令的测试中，我们需要检查隔离范围是否定义了所有三个属性，以及它们是否被赋予了正确的值。在这种情况下，我们需要测试以下情况:

1.  `config`独立范围上的属性应该与范围上的属性相同，并且是双向绑定的
2.  `notify`隔离范围上的属性应该是单向绑定的
3.  `onChange`独立作用域上的属性应该是一个函数，作用域上的方法在被调用时应该被调用

该指令期望在周围的作用域上有所作为，所以它需要一个稍微不同的设置，我们还需要获得一个独立作用域的引用。

下面的代码片段准备了指令的范围并对其进行编译:

```
beforeEach(function() {
  module('sampleDirectives');
  inject(function ($compile, $rootScope) {
    compile=$compile;
    scope=$rootScope.$new();
    scope.config = {
      prop: 'value'
    };
    scope.notify = true;
    scope.onChange = jasmine.createSpy('onChange');
  });
  directiveElem = getCompiledElement();
});

function getCompiledElement(){
  var compiledDirective = compile(angular.element('<fifth-directive config="config" notify="notify" on-change="onChange()"></fifth-directive>'))(scope);
  scope.$digest();
  return compiledDirective;
```

现在我们已经准备好了指令，让我们测试隔离范围是否被分配了正确的属性集。

```
it('config on isolated scope should be two-way bound', function(){
  var isolatedScope = directiveElem.isolateScope();

  isolatedScope.config.prop = "value2";

  expect(scope.config.prop).toEqual('value2');
});

it('notify on isolated scope should be one-way bound', function(){
  var isolatedScope = directiveElem.isolateScope();

  isolatedScope.notify = false;

  expect(scope.notify).toEqual(true);
});

it('onChange should be a function', function(){
    var isolatedScope = directiveElem.isolateScope();

    expect(typeof(isolatedScope.onChange)).toEqual('function');
});

it('should call onChange method of scope when invoked from isolated scope', function () {
    var isolatedScope = directiveElem.isolateScope();
    isolatedScope.onChange();

    expect(scope.onChange).toHaveBeenCalled();
});
```

### 测试要求

一个指令可以严格地或选择性地依赖于一个或一组其他指令。为此，我们有一些有趣的案例要测试:

1.  如果未指定严格要求的指令，则应引发错误
2.  如果指定了严格要求的指令，应该可以工作
3.  如果未指定可选的必需指令，则不应引发错误
4.  应该与可选指令的控制器交互(如果找到的话)

下面的指令在父元素中需要`ngModel`并且可以选择需要`form`:

```
angular.module('sampleDirectives').directive('sixthDirective', function () {
    return {
      require: ['ngModel', '^?form'],
      link: function(scope, elem, attrs, ctrls){
        if(ctrls[1]){
          ctrls[1].$setDirty();
      }
    }
  };
});
```

如您所见，该指令只有在被找到时才与`form`控制器交互。虽然这个例子没有多大意义，但它给出了行为的概念。本指令的测试涵盖了上述情况，如下所示:

```
function getCompiledElement(template){
  var compiledDirective = compile(angular.element(template))(scope);
  scope.$digest();
  return compiledDirective;
}

it('should fail if ngModel is not specified', function () {
  expect(function(){
    getCompiledElement('<input type="text" sixth-directive />');
  }).toThrow();
});

it('should work if ng-model is specified and not wrapped in form', function () {
  expect(function(){
    getCompiledElement('<div><input type="text" ng-model="name" sixth-directive /></div>');
  }).not.toThrow();
});

it('should set form dirty', function () {
  var directiveElem = getCompiledElement('<form name="sampleForm"><input type="text" ng-model="name" sixth-directive /></form>');

  expect(scope.sampleForm.$dirty).toEqual(true);
});
```

### 测试替换

测试`replace`非常简单。我们只需检查编译后的模板中是否存在 directive 元素。你应该这样做:

```
//directive
angular.module('sampleDirectives').directive('seventhDirective', function () {
  return {
    replace: true,
    template: '<div>Content in the directive</div>'
  };
});

//test
it('should have replaced directive element', function () {
  var compiledDirective = compile(angular.element('<div><seventh-directive></seventh-directive></div>'))(scope);
  scope.$digest();

  expect(compiledDirective.find('seventh-directive').length).toEqual(0);
});
```

### 测试交叉

Transclusion 有两种情况:transclude 设置为`true`和 transclude 设置为一个元素。我没见过多少 transclude 设置为 element 的用例，所以我们只讨论 transclude 设置为`true`的情况。

我们需要测试以下内容，以检查该指令是否支持交叉包含的内容:

1.  如果模板有一个带有`ng-transclude`指令的元素
2.  如果内容被保留

为了测试该指令，我们需要在要编译的指令中传递一些 HTML 内容，然后检查上述情况。这是一个使用 transclude 及其测试的指令:

```
//directive
angular.module('sampleDirectives').directive('eighthDirective', function(){
  return{
    transclude: true,
    template:'<div>Text in the directive.<div ng-transclude></div></div>'
  };
});

//test
it('should have an ng-transclude directive in it', function () {
    var transcludeElem = directiveElem.find('div[ng-transclude]');
    expect(transcludeElem.length).toBe(1);
});

it('should have transclude content', function () {
    expect(directiveElem.find('p').length).toEqual(1);
});
```

## 结论

正如您在本文中看到的，与 AngularJS 中的其他概念相比，指令更难测试。同时，它们也不能被忽视，因为它们控制着应用程序的一些重要部分。AngularJS 的测试生态系统让我们更容易测试项目的任何部分。我希望通过这篇教程，你现在更有信心测试你的指令了。请在评论区告诉我你的想法。

如果你想使用本教程中开发的代码，你可以看看我为你设置的 GitHub 库。

## 分享这篇文章