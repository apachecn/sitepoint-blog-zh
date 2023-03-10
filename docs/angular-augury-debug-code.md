# 使用角度预测来调试您的代码

> 原文：<https://www.sitepoint.com/angular-augury-debug-code/>

**Augury 是一个开源工具[，允许开发人员分析和调试](https://augury.angular.io/pages/guides/augury.html) Angular 2 和 4 应用程序。**

现代 web 浏览器提供了开发人员控制台来检查页面上的各种元素，这在调试标记、样式和脚本时非常方便。然而，这个控制台不足以调试 Angular 应用程序，这些应用程序通常有许多组件、事件、属性和独立的路由系统。

[占卜](https://augury.angular.io/)是专门为有角度的 app 设计的工具。这是一个用于 [Angular 2+](http://angular.io/) 应用程序的开源调试和分析工具。

Augury 只是 Chrome 的一个扩展，使用起来非常简单，所以你不需要花费大量的时间来学习如何使用这个工具。我们将构建一个样本 Angular 应用程序，然后通过探索我们项目的各个部分来查看运行中的预兆。所以，让我们开始吧！

## 你好，占卜！

Augury 以树的形式可视化应用程序的结构，显示组件及其依赖关系如何相互关联。它还允许您检查对象的属性，并动态地更改它们。除此之外，您可以轻松查看特定组件的源代码，根据需要插入断点，处理事件等等。最后，您可以浏览应用程序的路由系统，以及查看所有被利用模块的完整列表。

Augury 仅作为 Chrome 扩展提供(不幸的是，还没有 Firefox 支持)，安装它就像进入[这个页面](https://chrome.google.com/webstore/detail/augury/elgalmkoelokbchhkhacckoklkejnhcd)并按下*安装*按钮一样简单。之后，您可以通过按`Ctrl` + `Shift` + `I` (Windows/Linux)或`Cmd` + `Opt` + `I` (macOS)打开开发者工具。你会注意到一个叫做*占卜*的新标签出现了。切换到这个标签后，你会看到应用程序的结构或短语“这个应用程序不是一个有角度的应用程序”。我注意到，有时可能需要重新打开开发人员控制台，以便占卜正确地分析页面，所以要小心。

现在我们已经安装了占卜，让我们继续下一部分，准备我们将用作游乐场的示例应用程序！

## 构建示例应用程序

为了看到预言在起作用，我们需要调试一些东西，对吗？在本节中，我将快速引导您创建一个非常简单的应用程序(大致基于来自 [Angular 的官方教程](https://angular.io/tutorial/)的示例应用程序),列出一些用户并允许您编辑他们。或者，你可以从[我的 GitHub repo](https://github.com/bodrovis/Sitepoint-source/tree/master/Debugging_Angular_With_Augury) 获取源代码。

开始之前，如果您还没有安装 [Angular CLI](https://github.com/angular/angular-cli) ，请在您的机器上安装它:

```
npm install -g @angular/cli 
```

接下来，创建我们新项目的框架:

```
ng new sitepoint-augury 
```

通过调整 *src/app/app.component.ts* 文件来更改应用程序的标题:

```
// ...

export class AppComponent {
  title = 'Augury Demo';
} 
```

通过删除代码生成器自动添加的所有文档链接来调整`src/app/app.component.html`,并添加一行`<app-users></app-users>`:

```
<div style="text-align:center">
  <h1>
    Welcome to {{ title }}!
  </h1>
</div>

<app-users></app-users> 
```

当然，我们需要一个`User`组件，所以现在通过运行以下命令来生成它:

```
ng generate component users 
```

按照以下方式更改`src/app/users/user.component.ts`文件:

```
import { Component, OnInit } from '@angular/core';
import { User } from './user.model'; // <--- 1
import { UserService } from './user.service'; // <--- 2

@Component({
  selector: 'app-users',
  templateUrl: './users.component.html',
  styleUrls: ['./users.component.css']
})
export class UsersComponent implements OnInit {
  users: User[];

  selectedUser: User;

  onSelect(user: User): void { // <--- 3
    this.selectedUser = user;
  }

  constructor(private userService: UserService) { } // <--- 4

  ngOnInit() {
    this.getUsers(); // <--- 5
  }

  getUsers(): void { // <--- 6
    this.users = this.userService.getUsers();
  }

} 
```

这里需要注意的主要事项:

1.  我们正在导入一个即将创建的`User`模型。
2.  我们还导入了一个`UserService`。它将简单地返回一个硬编码用户的列表，但是让我们假设他们是从某个远程位置获取的。
3.  我们允许通过点击来选择用户。当前选择的用户存储在单独的`selectedUser`属性中。
4.  使用依赖注入机制连接`userService`。
5.  组件初始化后，加载用户列表。
6.  为了获得用户，我们正在利用我们的`userService`。

这就是这个组件的大部分内容。

接下来，让我们在一个`src/app/users/user.model.ts`文件中创建一个模型。每个用户都有一个 ID，一个名和一个姓:

```
export class User {
  id: number;
  first: string;
  last: string;
} 
```

没什么复杂的。

现在让我们继续处理将在`app/src/users/user.service.ts`文件中定义的`UserService`:

```
import { Injectable } from '@angular/core';
import { User } from './user.model';

@Injectable()
export class UserService {

  constructor() { }

  getUsers(): User[] {
    return [
      {
        id: 1,
        first: 'John',
        last: 'Doe'
      },
      {
        id: 2,
        first: 'Margaret',
        last: 'Brown'
      }
    ]
  }
} 
```

`getUsers`方法简单地返回带有硬编码数据的用户数组。

现在让我们借助`ngFor`来展示我们的用户。此外，我们将添加一个点击事件监听器，并在用户被点击时触发`onSelect`。当这种情况发生时，应该显示一个编辑所选用户的表单(这将在`ngIf`的帮助下完成)。像这样修改`src/app/users/user.component.html`文件:

```
<div *ngFor="let user of users" (click)="onSelect(user)"
[class.selected]="user === selectedUser">
  <p>{{user.last}}, {{user.first}} (ID: {{user.id}})</p>
</div>

<div *ngIf="selectedUser">
  <h3>Edit</h3>
  <label for="first">First</label>
  <input [(ngModel)]="selectedUser.first" placeholder="First name" id="first">

  <label for="last">Last</label>
  <input [(ngModel)]="selectedUser.last" placeholder="Last name" id="last">
</div> 
```

我们正在为选择的用户分配一个`.selected` CSS 类，所以让我们在`src/app/users/user.component.css`文件中为它添加一些简单的样式:

```
.selected {
  font-weight: bold;
} 
```

最后，我们必须将`FormsModule`和`UserService`导入到`src/app/app.module.ts`文件中:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms'; // <---
import { UserService } from './users/user.service'; // <---

import { AppComponent } from './app.component';
import { UsersComponent } from './users/users.component'; 
```

`FormsModule`应该列在`app.module.ts`的`imports`部分，而`UserService`到`providers`:

```
@NgModule({
  declarations: [
    AppComponent,
    UsersComponent
  ],
  imports: [
    BrowserModule,
    FormsModule // <---
  ],
  providers: [
    UserService // <---
  ],
  bootstrap: [AppComponent]
}) 
```

就是这样！我们的示例应用程序已经完成，您可以通过运行以下命令来打开它:

```
ng serve --open 
```

## 组件视图

好了，现在让我们来看看占卜能给我们带来的好处。打开 Chrome，进入`http://localhost:4200`。应该会显示一个用户列表。按`Ctrl` + `Shift` + `I`或`Cmd` + `Opt` + `I`，切换到占卜页签，按左侧窗格 *[组件树](https://augury.angular.io/pages/guides/io-demo.html)* 下的 *AppComponent* :

![Component tree view](img/24087ff56a9bc1958768579f26d62c39.png)

这里有两个窗格:

*   在左边，您可以看到*组件树*，它有一个应用程序组件的列表，这些组件被恰当地嵌套。
*   在右边，有一个用于所选组件的*属性*面板。我们的`AppComponent`只有一个属性`title`，显示在它的值旁边(用箭头标注)。

关于*属性*窗格的有趣之处在于，您可以根据需要更改值，并立即观察到变化。例如，尝试更改`title`:

![Changing the title](img/df065cdc8c913fc71a4d078ba489f448.png)

此外，组件名称旁边还有一个*视图源*链接。按下它将显示所选组件的源代码，您可以在其中轻松插入断点以在任意点停止代码执行:

![Source code for the chosen component](img/83ef199cb51bf705ae35c145c1fde8b9.png)

现在让我们回到*组件树*并选择`UsersComponent`。在*属性*选项卡中，我们将看到一组用户(下面截图中的点#1)以及在*依赖关系*部分(#2)中列出的`UserService`:

![An array of users](img/9b422f93d5f066821e66df4fa4b29e2f.png)

接下来，让我们尝试查看`UsersComponent`的源代码。当相应的选项卡打开时，您可以在例如第 16 行插入一个断点，即`this.selectedUser = user;`。要插入断点，只需点击行号。接下来，尝试选择一个用户，您将看到以下内容:

![The result of selecting a user](img/a77f06f3ce34e49cfd0a83ff16ac087e.png)

因此，代码执行暂停，当前选择的用户显示在橙色框中。一旦调试完这一行，就可以按下*恢复脚本执行*按钮:

![Resuming script execution](img/33652945d3fd11b7f1b82be10aaff01f.png)

程序将继续运行，要么在下一个断点处停止，要么完成它的作业。如果您怀疑应用程序由于数据在某个特定方法中被错误地处理而行为不当，那么断点就非常方便了。您可以根据需要设置尽可能多的断点，这样您就可以逐步探索脚本，并了解在各个步骤中数据的情况。

### 形式

请注意，在您选择一个用户后，`selectedUser`属性将被立即添加。另外，您将看到两个`inputs`被添加到`UsersComponent`中:

![Inputs added to the UsersComponent](img/4d34081e3b745acbe2d1ea06b453a92c.png)

如果我们选择这些输入中的一个，我们将看到关于该控件的一些非常详细的信息，特别是它涉及的型号。如果我更改输入的值，模型也会更新:

![The model updated](img/93c499c3e7914677413f27a072e8a8df.png)

## 依赖注入

正如你所记得的，我们将`UserService`注入到`UsersComponent`中，对吗？这可以通过选择`UsersComponent`并打开一个[喷射器图](https://augury.angular.io/pages/guides/dependency-injection.html)很容易地确认:

![Injector graph](img/84250a6de11630ccb0799c7331be3665.png)

因此，在这里我们可以看到组件层次结构和注射器图本身。此图说明了依赖关系树:

*   `root`是我们的`NgModule`在`app.module.ts`文件中定义的吗
*   接下来，还有一个`AppComponent`
*   最后，还有`UsersComponent`本身。

另外，请注意，这里我们可以看到用红线将`UserService`连接到`UsersComponent`。这证实了服务肯定被注入到组件中。您也可以将鼠标指针悬停在`UserService`节点上，在选项卡底部查看更多详细信息:

![More detailed information at the bottom of the tab](img/5318ec14a15a35a2b124b63a70fc842a.png)

依赖注入可以帮助你确保所有的依赖都被正确地连接起来，因为许多错误的出现仅仅是因为你忘记了导入某个模块。

### NgModules

还可以查看应用程序中使用的所有模块的完整[列表，以及关于提供商、出口和其他内容的信息。就像打开 *NgModules* 标签一样简单:](https://augury.angular.io/pages/guides/module-list.html)

![The NgModules tab](img/47b3690b8ecab3cb2631232a21d804a8.png)

不幸的是，这个选项卡不是交互式的，所以您不能选择一个模块来查看关于它的更多详细信息。尽管如此，它还是能派上用场。

## 路线

最后一个预兆特性是能够[检查你的应用程序的路由系统](https://augury.angular.io/pages/guides/routes.html)。我们在演示应用程序中没有任何路线，但这里有一张取自官方文档的图片，很好地说明了这一功能:

![Inspecting the routing system](img/4b83d8318d69aa3e79ff48d8c13d870d.png)

我们可以轻松地看到应用程序的所有路线。即使路由是延迟加载的，当相应的路由出现时，该模式也会自动更新。多酷啊。

## 结论

在本文中，我们已经了解了用于 Angular 2+应用程序的占卜分析器和调试器。我们已经看到了如何安装和启动这个工具，并讨论了它的所有主要特性，包括组件树、注入图、断点和路由调试。

正如你所看到的，这个工具非常简单，但是非常强大，所以它绝对是每个 Angular 开发人员应该拥有的工具箱！在为客户端开发时，您尝试过使用哪些调试器？你喜欢占卜的特征吗？一如既往的感谢大家陪我，调试愉快。

## 分享这篇文章