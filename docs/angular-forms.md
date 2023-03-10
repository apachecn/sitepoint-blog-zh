# 快速创建简单而强大的角度形式

> 原文：<https://www.sitepoint.com/angular-forms/>

表单是许多 web 应用程序的重要组成部分，是输入和编辑基于文本的数据的最常见方式。前端 JavaScript 框架，如 [Angular](https://angular.io/) ，通常有自己惯用的创建和验证表单的方式，你需要掌握这些方式才能高效工作。

Angular 允许您通过提供两种类型的表单来简化这一常见任务:

*   **模板驱动的表单**——可以快速制作的简单表单。
*   **反应式表单**–更复杂的表单，让您更好地控制表单中的元素。

在本文中，我们将为每个方法制作一个简单的示例表单，看看它是如何完成的。

## 先决条件

您不需要知道如何创建 Angular 应用程序的所有细节，就可以理解框架在表单方面的用处。然而，如果你想更好地掌握 Angular，你可以看看这个关于用 Angular 构建 CRUD 应用的 SitePoint 系列文章。

## 要求

在本教程中，我们将使用 [Bootstrap](http://getbootstrap.com/) 。它不是 Angular 应用程序不可或缺的一部分，但是它将通过提供现成的样式来帮助我们进一步简化工作。

您可以这样将它添加到您的应用程序中:

1.  打开命令提示符并导航到项目的文件夹

2.  类型`npm install bootstrap@next`。这将把最新版本的 bootstrap 添加到项目中

3.  编辑`.angular-cli.json`文件并添加一个到引导 CSS 文件的链接

    ```
    "apps": [
    "styles": [
        "../node_modules/bootstrap/dist/css/bootstrap.css"
     ]
    ] 
    ```

    我们不会在这个应用程序中使用引导 JavaScript 文件。

4.  模板驱动的表单和反应式表单都需要`FormsModule`。应将其添加到`app.module`中的应用程序中:

    ```
    import { FormsModule } from '@angular/forms';
    @NgModule({
    imports: [
      BrowserModule,
      FormsModule
    ]
    }) 
    ```

这样一来，我们就可以继续处理表单本身了。

## 模板驱动的表单

让我们假设您想尽快创建一个简单的表单。例如，你需要一份公司注册表格。如何创建表单？

第一步是在视图中创建`<form>`标签。

```
<form #companyForm="ngForm"> 
```

为了提交表单并使用组件中输入字段的信息，我们需要以两种方式修改这个标签:

*   我们将使用`ngForm`指令声明一个模板变量。
*   我们将把`ngSubmit`事件绑定到我们将在组件中创建的方法

```
<form #companyForm="ngForm" (ngSubmit)="submitCompany(companyForm.form);"> 
```

稍后我们将在组件中创建`submitCompany`方法。当表单被提交时它将被调用，我们将通过`companyForm.form`把表单中的数据传递给它。

我们还需要一个提交按钮，不管表单的内容是什么。我们将使用一些引导类来设计按钮的样式。在满足所有数据验证要求之前禁用该按钮是一个好的做法。我们可以使用为表单创建的模板变量来实现这一点。我们将把禁用的属性绑定到`companyForm`对象的有效属性。这样，如果表单无效，按钮将被禁用。

```
<button class="btn btn-primary" [disabled]="!companyForm.valid">Submit</button> 
```

假设我们的简单表单有两个字段——一个是公司名称的输入字段，另一个是公司所属行业的下拉字段。

### 创建表单输入

首先，我们为名称创建一个输入字段:

```
<input type="text" 
       class="form-control" 
       name="company-name"> 
```

现在我们有一个标准的输入，包括类型、名称和类属性。在我们的输入中使用角度方法我们需要做什么？

我们需要对它应用`ngModel`指令。Angular 将创建一个控制对象，并将其与字段相关联。本质上，Angular 在幕后为您做了一些工作。

这是一个很好的时机来提及`ngModel`要求输入域有一个名称，或者表单控件必须在`ngModelOptions`中被定义为独立的。这不是问题，因为我们的表单已经有了一个名称。Angular 将使用名称属性来区分控制对象。

此外，我们应该为输入指定一个模板变量:`#nameField`。Angular 会将`nameField`设置为应用于输入字段的`ngModel`指令。稍后我们将使用它来验证输入字段。这个变量还允许我们在输入时根据字段的值执行一个操作。

现在我们的输入看起来像这样:

```
<input type="text" 
       class="form-control" 
       name="company-name"
       ngModel
       #nameField="ngModel"> 
```

这几乎是一样的，但有几个关键的变化。

### 确认

假设我们希望公司名称字段是必需的，并且最小长度为 3 个字符。这意味着我们必须将`required`和`minlength`属性添加到输入中:

```
<input type="text" 
       class="form-control" 
       name="company-name"
       ngModel
       #nameField="ngModel"
       required
       minlength="3"> 
```

听起来很简单，对吧？如果不满足这两个要求中的任何一个，我们还需要显示一条错误消息。Angular 允许我们在提交表单之前检查输入的值并显示适当的错误消息。

我们可以在用户输入表单时执行这样的检查。首先，在用户开始与表单交互后才显示错误是一个好主意。在我们加载页面后立即显示错误消息是没有用的。这就是为什么我们将在下面的 div 中插入该输入的所有错误消息:

```
<div *ngIf="nameField.touched && nameField.errors"></div> 
```

`ngIf`指令允许我们仅在特定条件为真时显示 div。这里我们将再次使用`nameField`模板变量，因为它与输入相关联。在我们的例子中，只有当输入被触摸并且有问题时，div 才是可见的。好吧，那么错误消息本身呢？

对于我们想要的每个错误消息，我们将在前面提到的 div 中放置另一个 div。我们将为错误消息创建一个新的 div，并再次使用`nameField`模板变量:

```
<div class="alert alert-danger" 
     *ngIf="nameField.errors.required">
     The company name is required
</div> 
```

我们使用“alert alert-danger”引导类来设计文本字段的样式。`nameField`变量具有属性`errors`，它包含一个对象，该对象具有所有当前错误的键值对。`ngIf`指令允许我们仅在不满足“必需”条件时显示该错误信息。我们将对关于最小长度的错误消息使用相同的方法。

```
<div class="alert alert-danger" 
     *ngIf="nameField.errors.minlength">
     The company name should be at least 3 characters long
</div> 
```

仅当不满足`minlength`要求时，该 div 才可见。在这里，我们可以使错误消息更加动态。

目前，我们已经在两个位置指定了最小长度——输入的属性和文本字段。我们可以通过用`minlength`对象的`requiredLength`属性替换硬编码的“3”来改进这一点，如下所示:

```
<div class="alert alert-danger" 
     *ngIf="nameField.errors.minlength">
     The company name should be at least {{ nameField.errors.minlength.requiredLength }} characters long
</div> 
```

这样，错误消息中最小长度的数量将取决于输入的`minlength`属性。

现在，我们将对公司所属行业的下拉字段做同样的事情:

```
<select class="form-control" 
        name="company-industry"
        ngModel
        #industryField="ngModel"
        required> 
```

为了避免在 HTML 中硬编码值，我们将在与此视图相关的组件中为下拉列表创建一个选项列表。

```
export class ContactFormComponent implements OnInit {
  industries = [
    {id: 1, name: "Agriculture"},
    {id: 2, name: "Manufacturing"},
    {id: 3, name: "Energy"},
    {id: 4, name: "Transportation"},
    {id: 5, name: "Finance"}
  ];
} 
```

现在我们可以通过`ngFor`指令在视图中列出所有选项。它将为组件的`industries`数组中的每个元素创建一个选项标签。

```
<option *ngFor="let industry of industries" 
        [value]="industry.id">
        {{ industry.name }}
</option> 
```

该字段的验证非常简单，类似于公司名称字段的验证:

```
<div class="alert alert-danger" 
     *ngIf="industryField.touched && !industryField.valid">
      The industry is required
</div> 
```

现在我们的表单可以提交了。之前我们将`ngSubmit`事件绑定到一个名为`submitCompany`的方法上；现在让我们转到组件并添加它:

```
export class ContactFormComponent implements OnInit {
  submitCompany(form){
    console.log(form.value);
    alert("The form was submitted");
    form.reset();
  }
} 
```

`form`参数将包含表单中的所有数据。另一方面，`form.value`将只包含一个具有表单中字段值的对象。

在这里，我将只在控制台中记录结果，但是您可以对它做任何您想做的事情。我添加了一个警告消息，通知用户表单已提交。这不是必需的，但是显示某种通知是一个很好的做法。`form.reset()`提交后会将表单重置为初始状态，这意味着字段将被清空。

好了，让我们看看我们的表单应该是什么样子:
https://site point-editors . github . io/company-registration-form/

## 反应形式

您可以创建的另一种表单是反应式表单，它允许您自己显式地为表单字段创建控件对象。当您正在构建一个更复杂的表单，并且希望对其行为有更多的控制时，这种方法是一个很好的选择。

让我们假设我们需要创建一个帐户注册表单，它有两个字段，一个是电子邮件，一个是密码。我们也将使用 Bootstrap 来设计这个表单。

第一步是在`app.module`中导入`ReactiveFormsModule`类，因为它是所有反应式表单所必需的:

```
import { ReactiveFormsModule } from "@angular/forms";

@NgModule({
  imports: [
    ReactiveFormsModule
  ]
}) 
```

然后，我们需要在页面的组件中导入`FormGroup`和`FormControl`类，以便显式定义我们的控件对象:

```
import { FormGroup, FormControl } from "@angular/forms"; 
```

现在我们应该创建一个`FormGroup`类的实例，并指定表单中的所有字段。简单来说，我们将列出键值对。关键字是字段的名称，值是表单对象。

```
accountForm = new FormGroup({
    email: new FormControl(),
    password: new FormControl(); 
```

接下来，我们应该创建表单。我们将再次需要`<form>`标签。我们将向它添加`FormGroup`指令，并将 HTML 表单与我们在组件中创建的`accountForm`表单组对象相关联:

```
<form [formGroup]="accountForm"></form> 
```

接下来，我们将创建电子邮件输入字段。我们将对它应用`formControlName`指令，并将其设置为我们在组件`email`中创建的控件列表中的相应键。

```
<input type="text" 
       class="form-control" 
       id="email" 
       formControlName="email"> 
```

我们将对密码字段进行同样的操作:

```
<input type="text" 
       id="password" 
       class="form-control"
       formControlName="password"> 
```

### 确认

下一步是向表单添加验证。对于模板驱动的表单，我们不会使用任何 HTML 属性，比如“required”。相反，我们必须在创建表单控件对象时分配所有的验证器。

我们将回到定义`accountForm`的组件。反应式表单的所有验证器方法都在`Validators`类中定义，我们必须导入这个类:

```
import { FormGroup, FormControl, Validators } from "@angular/forms"; 
```

然后，我们将把验证器分配给控制器中的控件。格式如下:

```
 form = new FormGroup({
    fieldname: new FormControl(
                   initial value, 
                   synchronous validators, 
                   asynchronous validators)
  }); 
```

让我们假设电子邮件和密码字段都是必需的。我们还应该检查电子邮件是否有效。此外，密码应该至少包含一个大写字母、一个小写字母和一个数字。因此，我们将为这两个字段使用来自`Validators`类的`required`和`pattern`验证器。我们将把它们的初始值保留为空字符串。

```
form = new FormGroup({
    email: new FormControl("", 
           [Validators.required, 
            Validators.pattern('[a-zA-z0-9_\.]+@[a-zA-Z]+\.[a-zA-Z]+')]),
    password: new FormControl("", 
              [Validators.required, 
               Validators.pattern('^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z]).{8,}$')])
  }); 
```

现在我们需要转到模板并添加验证消息。我们将使用与模板驱动的表单相同的方式来实现这一点。但是，我们将以不同的方式访问控件对象。在我们的组件中，我们可以定义一个属性，使我们能够以如下形式访问控件:

```
get email(){
    return this.accountForm.get("email");
} 
```

我们可以在模板中访问这个属性。这意味着我们可以只使用`email`，而不是每次我们想要指定验证消息时都编写`this.accountForm.get("email")`。

```
<div *ngIf="email.touched && email.errors">
    <div class="alert alert-danger" *ngIf="email.errors.required">
      The email is required
    </div>
  </div>
  <div *ngIf="email.errors">
      <div class="alert alert-danger" *ngIf="email.errors.pattern">
          The email is not valid
      </div>
  </div> 
```

这样，在用户触摸表单并将其留空后，将出现消息“需要电子邮件”,而在用户键入时，将出现消息“电子邮件无效”。我们可以用同样的方式显示密码字段的验证消息。

让我们继续提交我们的反应式表单。首先，我们可以禁用 submit 按钮，方法类似于我们在模板驱动表单中使用的方法:

```
<button class="btn btn-primary" type="submit"         
        [disabled]="!accountForm.valid">Sign up</button> 
```

我们还需要将`ngSubmit`事件绑定到一个函数，这个函数将在提交时被调用。

```
<form [formGroup]="accountForm" (ngSubmit)="signup()"> 
```

然后，我们需要在控制器中定义该功能:

```
signup(){
    console.log(this.accountForm.value);
    alert('The form was submitted');
    this.accountForm.reset();
} 
```

现在，我们将在控制台中显示提交的数据。我们将在显示确认消息后清除表单字段。

### 异步验证

如果我们可以检查用户试图提交的电子邮件是否已经被使用，那就太好了。如果使用异步验证器，我们甚至可以在用户输入时执行这样的检查。

出于演示的目的，我们将使用一个假的 API-[JSON 占位符](https://jsonplaceholder.typicode.com/)。这是测试应用程序的有用工具，因为它提供了各种数据。例如，它可以提供一个带有电子邮件的用户列表，对于我们的演示应用程序，我们将假设这是现有用户的列表。您可以向它发送 get 和 post 请求，就像使用真正的 API 一样。

我们将在应用程序中创建一个连接到这个 JSON API 的服务，并将一个异步验证器附加到 email 字段。这样，我们将能够检查电子邮件是否已经被使用。

首先，我们将创建服务。我们可以通过 Angular CLI 做到这一点

```
ng g service server.service 
```

然后，我们必须将服务添加到`app.module`中，以便我们可以在应用程序中使用它:

```
import { ServerService } from "./server.service";
@NgModule({
  providers: [
    ServerService
  ],
  bootstrap: [AppComponent]
}) 
```

在我们的服务中，我们需要导入`Injectable`、`Http`和`Observable`类以及`map`和`filter` RxJS 操作符。然后我们将指定测试 API 的 URL。在我们得到结果后，我们将过滤它们，看看是否有一个用户的电子邮件与用户输入的电子邮件相匹配，当我们执行请求时，我们将把它传递给它。

```
@Injectable()
export class ServerService {
  private url = "http://jsonplaceholder.typicode.com/users";

  constructor(private http: Http) { }

  checkUsers(email: string) {
    return this.http
      .get(this.url)
      .map(res => res.json())
      .map(users => users.filter(user => user.email === email))
      .map(users => !users.length);
  }
} 
```

现在我们必须创建验证器，它将使用这个服务来检查电子邮件。我们将创建一个新的类型脚本文件， **custom.validators.ts** 。这将允许我们以更有效的方式分离我们的代码，并重用验证器。在那里，我们将导入`AbstractControl`和`ValidationErrors`类以及`ServerService`。

```
import { AbstractControl, ValidationErrors } from '@angular/forms';
import { ServerService } from './server.service'; 

export class Customvalidators{
    static checkDuplicateEmail(serverService: ServerService) {
        return (control: AbstractControl) => {
          return serverService.checkUsers(control.value).map(res => {
            return res ? null : { duplicateEmail: true };
          });
        };
    }
} 
```

我们创建了一个`serverService`的实例，并调用我们在其中创建的`checkUsers`方法。如果一切正常，定制验证器应该返回`null`，否则返回一个包含描述错误的键值对的对象。

现在，我们将进入组件，将异步验证器应用于电子邮件字段。我们还必须将`ServerService`导入到组件中，并创建它的一个实例，以便执行对我们的测试 API 的请求。

```
import { ServerService } from "../server.service";

constructor(private serverService: ServerService){

}

accountForm = new FormGroup({
   email: new FormControl("", synchronous validators,    
       Customvalidators.checkDuplicateEmail(this.serverService))
}); 
```

剩下唯一要做的就是添加一条验证消息

```
<div *ngIf="email.errors">
     <div class="alert alert-danger" *ngIf="email.errors.duplicateEmail">
          The email is already in use
     </div>
</div> 
```

现在让我们看看我们的表单是什么样子的。
https://sitepoint-editors . github . io/account-registration-form/

## 包扎

如您所见，Angular 允许您对表单进行一些巧妙的处理。通过使它们成为模板驱动的，不仅可以快速创建简单的表单，而且如果需要的话，还可以在其中实现复杂的特性。

## 分享这篇文章