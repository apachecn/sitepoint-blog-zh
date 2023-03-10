# 如何在 AngularJS 中创建基于表单的指令

> 原文：<https://www.sitepoint.com/form-based-directives-angularjs/>

对用户提交的数据实施复杂的业务约束对大量开发人员提出了独特的挑战。最近，我和我的团队在 GiftCards.com 大学写申请时就面临着这样的挑战。我们需要找到一种方法，允许我们的客户在应用程序的单个视图中编辑多个产品，其中每个产品都有一组唯一的验证规则。

这被证明是具有挑战性的，因为它要求我们在 HTML 源代码中有多个`<form>`标记，并为每个表单实例维护一个验证模型。在确定解决方案之前，我们尝试了许多方法，比如使用`ngRepeat`来显示子表单。我们将为每个产品类型创建一个指令(其中每个指令在其视图中都有一个`<form>`),并将该指令绑定到其父控制器。这允许我们利用 Angular 的子/父表单继承来确保父表单只有在所有子表单都有效时才有效。

在本教程中，我们将构建一个简单的产品评论屏幕(突出显示我们当前应用程序的关键组件)。我们将有两个产品，每个都有自己的指令，每个都有独特的验证规则。将会有一个简单的`checkout`按钮来确保两个表单都有效。

如果你急于看到这一点，你可以直接跳到我们的演示，或者从我们的 [GitHub repo](https://github.com/sitepoint-editors/ngDirectiveForms) 下载代码。

## 简单说说指令

指令是一段 HTML 代码，它通过 AngularJS 的 HTML 编译器(`$compile`)运行，并被附加到 DOM 中。编译器负责遍历 DOM，寻找可以使用其他注册指令转换成对象的组件。指令在一个隔离的范围内工作，并维护它们自己的视图。它们是强大的工具，可以促进可重用组件在整个应用程序中共享。要快速复习，请查阅[这篇 SitePoint 文章](https://www.sitepoint.com/practical-guide-angularjs-directives/)或 [AngularJS 文档](https://docs.angularjs.org/guide/directive)。

指令以两种方式解决了我们的基本问题:首先，每个实例都有一个独立的作用域，其次，指令使用一个编译器通道，编译器通过 Angular 的`ngForm`指令在视图的 HTML 中识别一个表单元素。这个内置指令允许多个嵌套的`form`元素，接受一个可选的`name`属性来实例化一个`Form Controller`，并将返回表单对象。

## 还有一个关于表单控制器的词

当编译器识别出 DOM 中的任何表单对象时，它将使用`ngForm`指令实例化一个`Form Controller`对象。该控制器将扫描所有的`input` `select`和`textarea`元素，并创建适当的控件。控件需要一个`model`属性来设置双向数据绑定，并允许用户通过各种预构建的验证方法进行即时反馈。向消费者提供即时反馈可以让他们在发出 HTTP 请求之前知道哪些信息是有效的。

### 预建的验证方法

Angular 附带 14 种标准验证方法。这些包括`min`、`max`、`required`的验证器，仅举几个例子。它们可以理解和操作几乎所有的 HTML5 输入类型，并且是跨浏览器兼容的。

```
<form name="form" novalidate>
  Size:
  <input type="text" ng-model="size" name="size" ng-required="true" />
  <span ng-show="form.size.$error.required">The value is required!</span>
</form>
```

上面的例子显示了 Angular 中的`ngRequired`指令验证器的用法。该验证确保字段在被认为有效之前被填写。它不验证任何数据，只验证用户输入的内容。拥有属性`novalidate`表示浏览器不应该在提交时进行验证。

> 专业提示:不要在任何角度形式上设置`action`属性。这将防止 Angular 试图确保表单不是以往返方式提交的。

### 自定义验证方法

Angular 提供了一个广泛的 API 来帮助创建定制的验证规则。使用这个 API，您可以为标准验证中没有包括的复杂输入创建和扩展自己的验证规则。我和我的团队依靠一些定制的验证方法来运行服务器使用的复杂正则表达式模式。如果不能运行复杂的正则表达式匹配器，我们可能会向后端服务器发送不正确的数据。这将向用户呈现错误，导致不期望的用户体验。定制验证器使用指令语法，并要求注入`ngModel`。更多信息可查阅[安古拉杰的文档](https://docs.angularjs.org/guide/forms#custom-validation)。

## 创建控制器

这样一来，我们就可以开始我们的应用程序了。你可以在这里找到控制器代码[的概述。](https://github.com/sitepoint-editors/ngDirectiveForms/blob/master/app/scripts/controller/main.js)

控制器将是事物的核心。它只有很少的责任——它的视图将有一个名为`parentForm`的表单元素，它将只有一个属性，它的方法将由`registerFormScope`、`validateChildForm`和`checkout`组成。

### 控制器属性

我们将需要控制器中的一个属性:

```
$scope.formsValid = false;
```

该属性用于维护表单整体有效性的布尔状态。我们使用这个属性来禁用“Checkout”按钮被点击后的状态。

### 方法:registerFormScope

```
$scope.registerFormScope = function (form, id) {
  $scope.parentForm['childForm'+id] = form;
};
```

当调用`registerFormScope`时，它将被传递一个`Form Controller`以及在指令实例化中创建的唯一指令 id。这个方法会将表单范围附加到父表单`Form Controller`。

### 方法:validateChildForm

这是将用于与执行验证的后端服务器协调的方法。当用户编辑内容时调用它，它需要通过额外的验证。我们在概念上不允许指令执行任何外部通信。

请注意，出于本教程的目的，我已经省略了后端组件。相反，我会根据用户输入的金额是否在某个范围内(产品 A 为 10-50，产品 B 为 25-500)来拒绝或解决一个承诺。

```
$scope.validateChildForm = function (form, data, product) {
  // Reset the forms so they are no longer valid
  $scope.formsValid = false;
  var deferred = $q.defer();

  // Logic to validate the form and data
  // Must return either resolve(), or reject() on the promise.
  $timeout(function () {
    if (angular.isUndefined(data.amount)) {
      return deferred.reject(['amount']);
    }

    if ((data.amount < product.minAmount) || 
        (data.amount > product.maxAmount)) {
      return deferred.reject(['amount']);
    }

    deferred.resolve();
  });
  return deferred.promise;
}
```

使用`$q`服务允许指令遵守带有成功和失败状态的接口。根据模型数据的编辑，应用程序界面的性质在“编辑”和“保存”之间变化。应该注意的是，一旦用户开始输入，模型数据就被更新。

### 方法:结帐

点击“结帐”表示用户已经完成编辑并希望结帐。在将模型数据发送到服务器之前，这个可操作项需要验证指令中加载的所有表单都通过了验证。本文的范围不包括用于向服务器发送数据的方法。我鼓励你探索使用[http 服务](https://docs.angularjs.org/api/ng/service/%24http)进行所有客户端到服务器的通信。

```
$scope.checkout = function () {
  if($scope.parentForm.$valid) {
    // Connect with the server to POST data
  }
  $scope.formsValid = $scope.parentForm.$valid;
};
```

这个方法使用 Angular 的能力来使一个子窗体无效。父表单被命名为`parentForm`，以清楚地说明它与子表单的关系。当一个`childForm`使用它的`$setValidity`方法时，它会自动上升到父表单，在那里设置有效性。`parentForm`中的所有表单必须有效，其内部的`$valid`属性才能为真。

## 创建我们的指令

我们的指令必须遵循一个允许完全互操作性和可扩展性的公共接口。我们指令的名称取决于它们包含的产品。

您可以在此处找到指令代码[(产品 A)](https://github.com/sitepoint-editors/ngDirectiveForms/blob/master/app/scripts/directives/productA.js) 和[(产品 B)](https://github.com/sitepoint-editors/ngDirectiveForms/blob/master/app/scripts/directives/productB.js) 的概述。

### 隔离指令范围

每个被实例化的指令都将获得一个孤立的作用域，该作用域局限于该指令，并且不知道外部属性。然而，AngularJS 允许创建利用父作用域方法和属性的指令。当将外部属性传递到本地化范围时，您可以指示希望设置双向数据绑定。

我们的应用程序将需要一些外部双向数据绑定方法和属性:

```
scope: {
  registerFormScope: '=',
  giftData: '=',
  validateChildForm: '=',
  product: '='
},
```

#### 方法:registerFormScope

指令的局部范围中的第一个属性是向控制器注册局部`scope.form`的方法。该指令需要一个管道将本地`Form Controller`对象传递给主`Controller`。

#### Object: giftData

这是将在指令视图中使用的集中模型数据。该信息将是双向数据绑定的，以确保在`Form Controller`中发生的更新将传播到主`Controller`。

#### 方法:validateChildForm

这个方法与在`Controller`中定义的方法相同。当用户更新指令视图中的信息时，将调用此方法。

#### 对象:产品

该对象包含关于所购买产品的信息。我们的演示使用了一个相对较小的对象，只有很少的属性。我的团队的真实世界应用程序有大量的信息，用于在应用程序中做出决策。它被传入`validateChildForm`以提供被验证内容的上下文。

### 定向链接

我们的指令将使用一个`postLink`函数，传递给它一个范围对象。除此之外，`postLink`函数还接受其他几个参数。这些措施如下:

1.  `scope`–用于访问根据指令实例化创建的隔离范围。
2.  `iElement`–用于获得基本物品。从`postLink`函数中更新和修改它被赋予的元素才是安全的。
3.  `iAttrs`–用于访问实例化指令的同一标签上的属性。
4.  `controller`–如果存在外部控制器依赖性，可在链接功能中使用。这些必须对应于`Directive Object`的`require`属性。
5.  `transcludeFn`–该功能与`Directive Object`的`$transclude`参数中所列的功能相同。

`link`负责附加所有 DOM 监听器，并用视图元素更新 DOM。

```
link: function postLink(scope) {
  // Indicates if the form is disabled
  scope.disabled = true;

  scope.saveForm = function () {
    // Code for saving the form data
  };

  // Register form scope
  $timeout(function() {
  });
}
```

#### 注册窗体范围

```
$timeout(function () {
  scope.form.fields = ['name','amount'];
  scope.registerFormScope(scope.form, scope.$id);
});
```

将方法`registerFormScope`包装在`$timeout`中会将执行推迟到执行堆栈的末尾。这为编译器提供了充足的时间来完成控制器和指令之间所有必要的链接。`scope.form.fields`是一个数组，它是在`Form Controller`中找到的属性的名称。这对于设置服务器端验证错误很重要。`registerFormScope`的目的是将`Form Controller`发送到父控制器，允许新创建的表单被设置为`parentForm`的子表单。

#### 信息更改时进行验证

```
scope.saveForm = function () {
  scope.validateChildForm(scope.form, scope.giftData, scope.product)
  .then(function () {
    angular.forEach(scope.form.fields, function (val) {
      scope.form.$setValidity(val, true);
      scope.form[val].$error.server = false;
    });
    scope.disabled = true;
  }, function (invalidFields) {
    angular.forEach(invalidFields, function (val) {
      if (angular.isDefined(scope.form[val])) {
        scope.form[val].$error.server = true;
        scope.form.$setValidity(val, false);
      }
    });
    scope.disabled = false;
  });
};
```

当表单改变并且用户准备好验证它时，指令中的`saveForm`方法被调用。这个方法将依次调用控制器的`validateChildForm`方法，传入`Form Controller`、`scope.giftData`和`scope.product`。控制器返回一个承诺，该承诺将根据附加验证规则被解决或拒绝。

当承诺被拒绝时，控制器将返回无效的字段。这用于使表单(和`parentForm`)无效，并设置额外的字段级错误。在我们的演示中，我们对 amount 字段使用了一个简单的后验证，并且不返回失败的原因。来自`validateChildForm`的拒绝可能复杂，也可能简单，视您的应用需求而定。

当承诺成功返回时，指令需要设置表单中字段的有效性。代码还必须清除任何以前识别的服务器错误。这确保了指令不会错误地向用户提供错误。设置所有带有`$setValidity`的字段链接到控制器中的`parentForm`以设置其有效性，前提是所有子表单都有效。

## 树立我们的观点

视图并不复杂，对于我们的演示，我们将产品配对到以下字段:`name`和`amount`。在下一步中，我们将探索完成这个应用程序所必需的视图。

你可以在这里找到视图代码[(产品 A)](https://github.com/sitepoint-editors/ngDirectiveForms/blob/master/app/views/directives/productA.html) 和[(产品 B)](https://github.com/sitepoint-editors/ngDirectiveForms/blob/master/app/views/directives/productB.html) 的概述。

### 路线视图

```
<div data-ng-app="myApp" ng-controller="stageController">
  <div id="main" class="container">
     <h1>Review Order</h1>

    <form name="parentForm" novalidate>
      <div ng-repeat="gift in gifts" class="row">
        <div class="col-lg-12"
             ng-if="gift.product.type == 'A'"
             product-A data-register-form-scope="registerFormScope"
             data-gift-data="gift.giftData"
             data-validate-child-form="validateChildForm"
             data-product="gift.product">
        </div>
        <div class="col-lg-12"
             ng-if="gift.product.type == 'B'"
             product-B data-register-form-scope="registerFormScope"
             data-gift-data="gift.giftData"
             data-validate-child-form="validateChildForm"
             data-product="gift.product">
        </div>
      </div>
    </form>

    <div class="row">
      <div class="col-lg-12">
        <button class="btn btn-primary" 
                data-ng-click="checkout()">Checkout</button>
        <div class="alert alert-success" 
             data-ng-show="formsValid">All forms are valid!</div>
      </div>
    </div>
  </div>
</div>
```

这个视图很重要，因为它设置了父表单，该父表单将用于包装从产品指令加载的所有子表单。在`ng-repeat`中使用`ng-if`可以确保 DOM 不会被未使用的`Form Controller`错误地填充。

### 方向视图

```
<form name="form" novalidate>
...
  <label for="amountInput">Amount</label>
  <input id="amountInput" name="amount"
         class="text-center form-control" type="tel"
         data-ng-model="giftData.amount" 
         data-ng-pattern="/^(?!\.?$)\d+(\.\d{0,2})?$/" 
         data-ng-required="true"
         data-ng-disabled="disabled"/>
...
  <label>Actions</label>
  <button class="btn btn-info" 
          ng-click="disabled=false;" 
          ng-show="disabled">Edit</button>
  <button class="btn btn-success" 
          ng-click="saveForm()" 
          ng-show="!disabled">Save</button>
...
  <div class="row" data-ng-show="form.$submitted">
    <div class="col-lg-12">
      <div class="alert alert-danger" 
           data-ng-show="form.name.$error.required && form.$submitted">
        Recipient Name is a required field.
      </div>
      <div class="alert alert-danger" 
           data-ng-show="form.amount.$error.pattern && form.$submitted">
        The amount is invalid.
      </div>
      <div class="alert alert-danger" 
           data-ng-show="form.amount.$error.server && form.$submitted">
        The amount is not accepted. Must be between 
        {{ product.minAmount }} and {{ product.maxAmount}}.
      </div>
    </div>
  </div>
...
</form>
```

**注意:**上面的视图在与演示布局相关的地方被截断，对本文不重要。

上面的`amountInput`设置了一个验证模式，将由 Angular 的`ngPattern`验证器执行。上面的字段将使用 Angular 建立的`ngDisabled`指令，该指令评估一个表达式，如果`true`，该字段将被禁用。

在视图的底部，我们显示了所有的错误，以便在用户点击`Save`按钮时向他们提供反馈。这将在子窗体上设置`$submitted`属性。

## 包装它

把所有的碎片放在一起，这就是我们最终得到的结果:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )验证的 Pen [AngularJS 指令表单。](http://codepen.io/SitePoint/pen/MwExLN/)