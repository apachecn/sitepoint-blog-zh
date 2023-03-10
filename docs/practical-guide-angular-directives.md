# 角度指令实用指南

> 原文：<https://www.sitepoint.com/practical-guide-angular-directives/>

这篇文章主要讨论角度指令——它们是什么，如何使用它们，以及如何构建我们自己的角度指令。

指令可能是角度应用程序中最重要的部分，如果我们仔细想想，最常用的角度单位，组件，实际上是一个指令。

角度组件只不过是一个带有模板的指令。当我们说组件是角度应用程序的构建块时，我们实际上是说指令是角度应用程序的构建块。

## 基本概述

本质上，指令是一个函数，只要 Angular 编译器在 DOM 中找到它，就会执行这个函数。角度指令通过赋予 HTML 新的语法来扩展它的功能。每个指令都有一个名称——要么是预定义的角度名称，如`ng-repeat`,要么是自定义的名称，可以称为任何名称。每个指令都决定了它可以在哪里使用:在`element`、`attribute`、`class`或`comment`中。

默认情况下，从角度版本 2 及更高版本开始，角度指令分为三种不同类型:

### 成分

正如我们前面看到的，组件只是带有模板的指令。在幕后，他们使用指令 API，并给我们一个更清晰的方式来定义它们。

其他两种指令类型没有模板。相反，它们是专门为 DOM 操作定制的。

### 属性指令

属性指令通过改变 DOM 的行为和外观来操纵 DOM。

我们使用属性指令将条件样式应用于元素，显示或隐藏元素，或者根据变化的属性动态地改变组件的行为。

### 结构指令

这些是专门为创建和销毁 DOM 元素而定制的。

一些属性指令——比如显示或隐藏元素的`hidden`——基本上保持了 DOM 的原样。但是结构角度指令对 DOM 不太友好，因为它们在 DOM 中添加或完全删除元素。因此，当使用这些时，我们必须格外小心，因为我们实际上是在改变 HTML 结构。

## 使用现有的角度指令

在 Angular 中使用现有的指令是相当容易的，如果您过去编写过 Angular 应用程序，我很确定您已经使用过它们。`ngClass`指令是现有角度属性指令的一个很好的例子:

```
<p [ngClass]="{'blue'=true, 'yellow'=false}">
    Angular Directives Are Cool!
</p>

<style> .blue{color: blue}
    .yellow{color: yellow} </style> 
```

因此，通过在下面的例子中使用 [`ngClass`指令](https://angular.io/api/common/NgClass)，我们实际上是在我们的段落中添加了`blue`类，并且明确地没有添加`yellow`类。因为我们正在改变一个类的外观，而不是改变实际的 HTML 结构，这显然是一个属性指令。但是 Angular 也提供了开箱即用的结构指令，比如`ngIf`:

```
@Component({
  selector: 'ng-if-simple',
  template: `
    <button (click)="show = !show">{{show ? 'hide' : 'show'}}</button>
    show = {{show}}
    <br>
    <div *ngIf="show">Text to show</div>
`
})

class NgIfSimple {
  show: boolean = true;
} 
```

在这个例子中，我们使用 [`ngIf`指令](https://angular.io/api/common/NgIf)通过一个按钮来添加或删除文本。在这种情况下，HTML 结构本身会受到影响，因此这显然是一个结构指令。

关于可用角度指令的完整列表，我们可以查看官方文档。

正如我们看到的，使用角度指令非常简单。角度指令的真正力量来自创造我们自己的能力。Angular 为创建定制指令提供了一个干净简单的 API，这也是我们将在接下来的章节中关注的。

### 创建属性指令

创建指令类似于创建组件。但是在这种情况下，我们使用了`@Directive`装饰器。在我们的例子中，我们将创建一个名为“my-error-directive”的指令，它将以红色突出显示一个元素的背景，以指示一个错误。

对于我们的例子，我们将使用 [Angular 2 快速启动包](https://github.com/angular/quickstart)。我们只需克隆存储库，然后运行`npm install`和`npm start`。它将为我们提供一个样板应用程序，我们可以用它来进行实验。我们将在样板文件的基础上构建我们的示例。

让我们首先在`src/app`文件夹中创建一个名为`app.myerrordirective.ts`的文件，并向其中添加以下代码:

```
import {Directive, ElementRef} from '@angular/core';

@Directive({
    selector:'[my-error]'
})

export class MyErrorDirective{
    constructor(elr:ElementRef){
        elr.nativeElement.style.background='red';
    }
} 
```

从`@angular/core`导入`Directive`后，我们就可以使用它了。首先，我们需要一个选择器，它为指令命名。在这种情况下，我们称之为`my-error`。

最佳实践表明，在命名角度指令时，我们总是使用前缀。这样，我们肯定能避免与任何标准 HTML 属性的冲突。我们也不应该使用`ng`前缀。这是 Angular 使用的，我们不想将自定义创建的角度指令与角度预定义指令混淆。在这个例子中，我们的前缀是`my-`。

然后我们创建了一个类，`MyErrorDirective`。要访问我们 DOM 中的任何元素，我们需要使用 [`ElementRef`](https://angular.io/api/core/ElementRef) 。由于它也属于`@angular/core`包，所以和`Directive`一起导入并使用就很简单了。

然后，我们添加代码来突出显示我们的类的构造函数。

为了能够使用这个新创建的指令，我们需要将它添加到`app.module.ts`文件的声明中:

```
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { MyErrorDirective } from './app.myerrordirective';

import { AppComponent }  from './app.component';

@NgModule({
  imports:      [ BrowserModule ],
  declarations: [ AppComponent, MyErrorDirective ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { } 
```

最后，我们希望利用我们刚刚创建的指令。为此，让我们导航到`app.component.ts`文件并添加以下内容:

```
import { Component } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `<h1 my-error>Hello {{name}}</h1>`,
})
export class AppComponent  { name = 'Angular'; } 
```

最终结果如下所示:

![Angular directives: attribute directive example](img/a4a8133dd382c61905f49d56d0220747.png)

### 创建结构指令

在上一节中，我们看到了如何使用 Angular 创建属性指令。创建结构行为的方法完全相同。我们用指令的代码创建一个新文件，然后将它添加到声明中，最后在组件中使用它。

对于我们的结构化指令，我们将实现一个`ngIf`指令的副本。这样，我们不仅可以实现一个指令，还可以看看角度指令是如何在幕后处理事情的。

让我们从我们的`app.mycustomifdirective.ts`文件开始:

```
import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

@Directive({
    selector: '[myCustomIf]'
})

export class MyCustomIfDirective {

    constructor(
        private templateRef: TemplateRef<any>,
        private viewContainer: ViewContainerRef) { }

    @Input() set myCustomIf(condition: boolean) {
        if (condition) {
            this.viewContainer.createEmbeddedView(this.templateRef);
        } else {
            this.viewContainer.clear();
        }
    }
} 
```

正如我们所看到的，我们使用了几个不同的导入，主要是:`Input`、`TemplateRef`和`ViewContainerRef`。`Input`装饰器用于向组件传递数据。`TemplateRef`用于实例化**嵌入式视图**。嵌入式视图表示要呈现的布局的一部分，它链接到一个模板。最后，`ViewContainerRef`是一个可以附加一个或多个视图的容器。这些组件的工作原理如下:

> 指令通过注入一个`ViewContainerRef`来访问视图容器。通过调用`ViewContainerRef`的`createEmbeddedView`方法并传入模板，嵌入式视图被创建并附加到一个视图容器。我们想使用附加了指令的模板，所以我们传入了注入的`TemplateRef`。 *—摘自 Rangle.io 的 [Angular 2 训练](https://angular-2-training-book.rangle.io/handout/advanced-angular/directives/view_containers_and_embedded_views.html)*

接下来，我们将它添加到我们的声明符中:

```
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { MyErrorDirective } from './app.myerrordirective';
import { MyCustomIfDirective } from './app.mycustomifdirective';

import { AppComponent }  from './app.component';

@NgModule({
  imports:      [ BrowserModule ],
  declarations: [ AppComponent, MyErrorDirective, MyCustomIfDirective ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { } 
```

我们在组件中使用它:

```
import { Component } from '@angular/core';

@Component({
  selector: 'my-app',
  template: `<h1 my-error>Hello {{name}}</h1>
         <h2 *myCustomIf="condition">Hello {{name}}</h2>
             <button (click)="condition = !condition">Click</button>`,
})

export class AppComponent  {
    name = 'Angular';
    condition = false;    
} 
```

结构化指令提供的这种方法非常有用，比如当我们必须根据不同用户的权限为他们显示不同的信息时。例如，网站管理员应该能够查看和编辑所有内容，而普通用户则不能。如果我们使用属性指令将私有信息加载到 DOM 中，那么普通用户和所有用户都可以访问它。

### 角度指令:属性与结构

我们已经看了属性和结构指令。但是我们什么时候应该使用其中之一呢？

答案可能会令人困惑，我们最终可能会使用错误的答案，因为它能解决我们的问题。但是有一个简单的规则可以帮助我们做出正确的选择。基本上，如果当 DOM 不可见时，具有指令的元素在 DOM 中仍然有用，那么我们应该明确地保留它。在这种情况下，我们使用类似于`hidden`的属性指令。但是如果元素没有用，那么我们就应该把它去掉。然而，我们必须小心避免一些常见的陷阱。我们必须避免总是隐藏元素的陷阱，因为这样更容易。这将使 DOM 更加复杂，并可能对整体性能产生影响。还应该避免总是删除和重新创建元素的陷阱。它肯定更干净，但是以牺牲性能为代价。

总而言之，应该仔细分析每种情况，因为理想的解决方案总是对您的应用程序结构、行为和性能的总体影响最小的方案。该解决方案可能是属性指令、结构指令，或者在最常见的情况下，是两者的折衷。

### 结论

在本文中，我们研究了角度指令，它是角度应用程序的核心。我们查看了不同类型的指令，并了解了如何创建符合我们需求的自定义指令。

我希望这篇文章能够让您开始使用角度指令。如果您有任何问题，请随时使用下面的评论部分。

## 分享这篇文章