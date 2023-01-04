# 用 wp-api-angular 连接 Angular 和 WordPress API

> 原文：<https://www.sitepoint.com/angular-wordpress-wp-api-angular/>

在本教程中，你将学习如何使用 [wp-api-angular](https://github.com/wordpress-clients/wp-api-angular#wpapiusers) 库，该库允许你从 [Angular 2+](https://angular.io) 应用程序中与 [WordPress API](http://v2.wp-api.org/) 进行交互。这个库支持所有主要的 WP 资源，包括用户、帖子、评论、媒体、分类法等。它使用起来也很简单，所以你很快就会明白。

为了查看运行中的库，我们将编写以下特性的代码:

*   使用 JWT 的身份验证
*   列出用户
*   列出帖子
*   创建和编辑帖子
*   删除帖子

到本文结束时，您将熟悉这个库，并准备好自己使用它。

本教程的源代码可以在 [GitHub](https://github.com/sitepoint-editors/angular-wordpress-client) 上获得。

我假设您使用的是 Angular 5，但是所有解释的概念对于 Angular 2 也应该是有效的。

## 奠定基础

### 设置 WordPress

在我们开始编写代码之前，有几件事情需要注意。首先，请注意，我们将要使用的 API 只适用于*自托管版本的 WordPress* 。对于网页版(可以通过 [WordPress 网站](https://wordpress.com/start/about?ref=homepage)配置)，有一个独立的 API，有许多相似的概念，尽管它仍然有很大的不同。

您还必须启用永久链接，这是 API 客户端正常工作所必需的。对于 Nginx，您需要在 *nginx.conf* 文件中添加下面一行:

```
try_files $uri $uri/ /index.php?$args; 
```

关于如何启用永久链接的更多详细信息和解释可以在本 WordPress Codex 指南中找到。

最后，我们应该注意 WordPress 的安全性，正如他们所说，这是最重要的。为此，需要一个名为 [JWT 认证](https://wordpress.org/plugins/wp-jwt-auth/)的特殊插件。我们将使用它，以便在特殊令牌的帮助下认证我们的 API 客户机(这种方法目前很常见)。

差不多就是这样。如果你想了解更多关于 WordPress API 的知识，浏览一下这篇文章。当你准备好了，继续下一步，让我们看看 Angular WordPress 客户端的运行！

### 引导角度应用程序

现在我们已经准备好了 WordPress，通过运行创建一个新的 Angular 应用程序:

```
ng new wp-api 
```

这将为应用程序创建一个框架。我们不打算彻底讨论它的结构，但你可能会在我们的角度系列中找到更多信息。

接下来，`cd`进入目录并安装库本身:

```
cd wp-api
npm install -g typings
npm install wp-api-angular --save 
```

现在我们需要在`src/app/app.module.ts`文件中导入适当的组件:

```
// ... other imports
import { Http } from '@angular/http';
import { HttpClientModule, HttpClient } from '@angular/common/http';
import {
  WpApiModule,
  WpApiLoader,
  WpApiStaticLoader
} from 'wp-api-angular'; 
```

`WpApiModule`也应添加到`imports`块中。注意，我们必须使用出口工厂进行 [AoT 编译](https://angular.io/guide/aot-compiler)或 [Ionic](https://ionicframework.com/) :

```
// ... imports

@NgModule({
  declarations: [
        // ... omitted
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpClientModule, // <---
    WpApiModule.forRoot({ // <---
      provide: WpApiLoader,
      useFactory: (WpApiLoaderFactory),
      deps: [Http]
    })

  ]
    // ...
}) 
```

这是工厂本身:

```
export function WpApiLoaderFactory(http: Http) {
  return new WpApiStaticLoader(http, 'http://YOUR_DOMAIN_HERE/wp-json/wp/v2/', '');
} 
```

别忘了在这里提供自己的域名！

最后，让我们向`app.components.ts`文件添加一些导入:

```
import { Component } from '@angular/core';
import { Observable } from 'rxjs';
import { NgForm } from '@angular/forms';
import { HttpClientModule, HttpClient } from '@angular/common/http';
import { Headers } from '@angular/http';

// ... 
```

我们需要`NgForm`来制作表单，HTTP 模块来与 API 交互，还需要`Headers`来验证客户端。

初始设置已经完成，我们可以进入下一部分。

## 证明

在与 API 交互之前，我们需要引入一个[认证机制](http://v2.wp-api.org/guide/authentication/)。正如我在上面已经提到的，将使用基于令牌的认证，所以让我们给`app.components.ts`添加一个`token`变量:

```
export class AppComponent {  
    token = null;
    // ...
} 
```

另外，通过添加一个新块来调整`app.component.html`文件:

```
<div>
  <app-authentication [(token)]='token'></app-authentication>
</div> 
```

为了实现这一点，需要一个单独的组件，所以现在就生成它:

```
ng generate component authentication 
```

在`src/app/authentication/authentication.component.ts`文件中导入必要的模块:

```
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';
import { HttpClientModule, HttpClient } from '@angular/common/http';
// ... 
```

认证过程将非常简单:用户应该输入他们的登录名和密码，提交表单，如果凭证正确，将返回一个特殊的令牌。然后，这个令牌将被用来执行 API 请求。因此，让我们起草一个用户并为`AuthenticationComponent`添加输入和输出:

```
// ...
export class AuthenticationComponent implements OnInit {
  user = {
    login: '',
    password: ''
  }
  @Input() token;
    @Output() tokenChange = new EventEmitter<string>();

    // ...
} 
```

当然，您可以将用户定义为一个模型，但是对于本演示来说，这不是强制性的。对于构造函数，将`HttpClient`传递给它:

```
// ...
constructor( private http: HttpClient ) { } 
```

接下来编写`auth`方法。这很简单，只需用凭证向正确的 URL 发送 POST 请求，然后等待响应:

```
// ...
auth() {
  this.http.post('http://YOUR_DOMAIN/wp-json/jwt-auth/v1/token', {
    username: this.user.login,
    password: this.user.password
  }).subscribe((data) => {
    if (data['token']) { // if token is returned
      this.token = data['token'];
      this.tokenChange.emit(this.token);
    }
  });
} 
```

同样，不要忘记在 URL 中插入您的域名。

组件已经准备好了，本节最后要做的事情是创建相应的表单。仅当令牌为`null`时才应显示。当提交表单时，应该调用`auth`方法:

```
<form *ngIf='token == null' (ngSubmit)='auth()'>
</form> 
```

通过添加两个字段和一个*提交*按钮来充实表单:

```
<form *ngIf='token == null' (ngSubmit)='auth()'>
    <div class='form-group'>
      <label for='login'>Login</label>
      <input type='text' class='form-control' [(ngModel)]='user.login' name='login' id='login' required>
    </div>

    <div class='form-group'>
      <label for='password'>Password</label>
      <input type='password' class='form-control' [(ngModel)]='user.password' name='password' id='password' required>
    </div>

    <button type="submit" class="btn btn-success">Submit</button>
</form> 
```

就是这样！身份验证功能已经完成，我们可以开始使用 API 本身了。

## 列出用户

通常，通过 API 阅读比写作简单，所以让我们试着列出我们网站的用户。创建一个新的`UserList`组件:

```
ng generate component user-list 
```

在`src/app/user-list/user-list.component.ts`中，你需要导入 [WpApiUsers 模块](https://github.com/wordpress-clients/wp-api-angular#wpapiusers)以及其他一些模块:

```
import { Component, OnInit, Input } from '@angular/core';
import { WpApiUsers } from 'wp-api-angular';
import { Headers } from '@angular/http';
// ... 
```

我们将把用户存储在最初为空的`users`数组中:

```
// ...
export class UserListComponent implements OnInit {
    users = [];
} 
```

将`WpApiUsers`传递给构造函数并调用一个`getUserList`方法:

```
// ...
constructor( private wpApiUsers: WpApiUsers ) {
  this.getUserList();
} 
```

现在我们需要对`getUserList`进行编码。API 客户端提供的每个方法都返回一个可观察值，可以使用`toPromise`将其转换为承诺。因此，要获得所有用户的列表，我们应该调用`getList`方法，将它转换成一个承诺，并用返回的数组给`users`变量赋值:

```
// ...
getUserList() {   
  this.wpApiUsers.getList()
  .toPromise()
  .then( response => {
    let json: any = response.json();
    this.users = json;
  })
} 
```

如你所见，这里没什么复杂的。有趣的是，我们甚至不需要令牌来执行这个方法。因此，只需在一个循环中呈现用户:

```
<div>
  <h2>Users:</h2>
  <div *ngFor="let user of users">
     Name: {{user.name}}
  </div>
</div> 
```

应将`user-list`组件添加到`app.component.html`文件中:

```
<!-- ... -->
<div>
  <user-list></user-list>
</div> 
```

## 使用帖子

### 创建帖子

现在让我们尝试实现一个稍微复杂一些的特性，并允许用户通过 API 添加新的帖子。创建一个单独的`post-new`组件:

```
ng generate component post-new 
```

在`filesrc/app/post-new/post-new.component.ts`文件中导入必要的模块:

```
import { Component, OnInit, Input } from '@angular/core';
import { WpApiPosts } from 'wp-api-angular';
import { Headers } from '@angular/http';
// ... 
```

WpApiPosts 模块将成为这里的主角。

接下来，提供`token`作为输入，并绘制一个`post`模型:

```
// ...
export class PostNewComponent implements OnInit {
  @Input() token;
  new_post = {
    title: '',
    content: '',
    status: 'publish'
    }
} 
```

至少，每个帖子应该包含一个标题、一些内容和状态(我们将其硬编码为`publish`以立即发布新帖子)。

构造函数应该接受`WpApiPosts`:

```
// ...
constructor( private wpApiPosts: WpApiPosts ) { } 
```

现在让我们设计一个方法来添加文章。首先，通过设置`Authorization`头对认证逻辑进行编码:

```
// ...
createPost() {       
  let headers: Headers = new Headers({
    'Authorization': 'Bearer ' + this.token
    });
} 
```

现在我们可以简单地将`headers`变量传递给`WpApiPosts`模块的`create`方法:

```
// ...
createPost() {       
  let headers: Headers = new Headers({
    'Authorization': 'Bearer ' + this.token
  });

  this.wpApiPosts.create(this.new_post, { headers: headers })
  .toPromise()
  .then( response => {
    console.log(response);         
  })
} 
```

形式呢？嗯，其实很简单:

```
<!-- src/app/post-new/post-new.component.html -->
<div>
  <h2> Post Creation </h2>
    <form (ngSubmit)='createPost()'>
      <div class="form-group">
        <label for="title">Post title</label>
        <input type="text" class="form-control" [(ngModel)]='new_post.title' name='title' id="title" required>
      </div>

      <div class="form-group">
        <label for="content">Post content</label>
        <textarea class="form-control" id="content" required [(ngModel)]='new_post.content' name='content'></textarea>
      </div>

      <button type="submit" class="btn btn-success">Submit</button>
    </form>
</div> 
```

当提交表单时，我们调用`createPost`方法。

不要忘记渲染`post-new`组件:

```
<!-- app.component.html -->
<!-- ... -->
<div>
  <h3 *ngIf='token == null'> Please, authorize to create a post </h3>
  <post-new *ngIf='token' [token]='token'></post-new>
</div> 
```

我们检查是否设置了令牌，如果没有，我们要求用户进行身份验证。

### 列出帖子

好的，我们已经添加了创建帖子的功能。为什么我们不把它们也显示在页面上呢？创建另一个组件:

```
ng generate component post-list 
```

导入必要的模块，包括`src/app/post-list/post-list.component.ts`文件中的`WpApiPosts`:

```
import { Component, OnInit, Input } from '@angular/core';
import { WpApiPosts } from 'wp-api-angular';
import { Headers } from '@angular/http';
// ... 
```

提供输入和`posts`数组:

```
// ...
export class PostListComponent implements OnInit {
  @Input() token;
    posts = [];
} 
```

编写应该调用`getPosts`方法的构造函数:

```
// ...
constructor(private wpApiPosts: WpApiPosts) {
  this.getPosts();
} 
```

我们不需要进行身份验证来获取帖子，所以让我们使用与之前相同的方法:

```
// ...
getPosts() {
  this.wpApiPosts.getList()
  .toPromise()
  .then( response => {
    let json: any = response.json();
    this.posts = json;
  });
} 
```

现在呈现帖子的数组:

```
<!-- src/app/post-list/post-list.component.html -->
<div>
  <h2>Latests Posts:</h2>
  <hr>
  <div *ngFor='let post of posts'>
    <h1 [innerHTML]='post.title.rendered'></h1>
    <p [innerHTML]='post.content.rendered'></p>
    <hr>
  </div>
</div> 
```

最后，显示组件:

```
<!-- app.component.html -->
<!-- ... -->
<div>
  <post-list [token]='token'></post-list>
</div> 
```

### 摧毁柱子

接下来，我想增加一个摧毁柱子的能力。这个特性可以在同一个`PostList`组件中实现。只需在每个帖子旁边添加一个*删除*按钮:

```
<!-- src/app/post-list/post-list.component.html -->
<div>
  <h2>Latests Posts:</h2>
  <hr>
  <div *ngFor='let post of posts'>
    <h1 [innerHTML]='post.title.rendered'></h1>
    <p [innerHTML]='post.content.rendered'></p>
        <button *ngIf='token' (click)='deletePost(post.id, $index)'>Delete</button>
    <hr>
  </div>
</div> 
```

请注意，仅当令牌存在时，才会显示此按钮。此外，通过添加`deletePost`方法来调整组件:

```
// src/app/post-list/post-list.component.ts
// ...
deletePost(id: number, index: number) {
  let headers: Headers = new Headers({
    'Authorization': 'Bearer ' + this.token
  });

  this.wpApiPosts.delete(id, { headers: headers })
  .toPromise()
  .then( response => {
    if (response['ok'] == true) {
      this.posts.splice(index,1);
    }       
  })
} 
```

基本上，这里没什么新东西。我们将令牌添加到标题中，并调用`delete`方法，该方法接受文章的 ID 及其在`posts`数组中的索引。如果请求成功，则从阵列中删除 post。

### 编辑帖子

我们今天要介绍的最后一个功能是编辑文章的能力。让我们创建一个新组件:

```
ng generate component post-edit 
```

该组件将从`PostList`中引用。具体来说，我想在每个帖子旁边添加一个*编辑*按钮，并在每次点击时呈现`PostEdit`模板:

```
<!-- src/app/post-list/post-list.component.html -->
<div>
  <h2>Latests Posts:</h2>
  <hr>
  <div *ngFor='let post of posts'>
    <div *ngIf='editingPost != post; else postEdit'>
    <h1 [innerHTML]='post.title.rendered'></h1>
    <p [innerHTML]='post.content.rendered'></p>
    <button *ngIf='token' (click)='deletePost(post.id, $index)'>Delete</button>
    <button *ngIf='token' (click)='updatePost(post)'>Edit</button>
    <hr>
  </div>
</div>

<ng-template #postEdit>
  <post-edit [post]='editingPost' [token]='token' (finish)='editingPost = null; getPosts()'></post-edit>
</ng-template> 
```

通过引入一个`editingPost`变量和一个`updatePost`方法来调整`PostListComponent`，这将为`editingPost`分配一个适当的值:

```
// src/app/post-list/post-list.component.ts
// ...
export class PostListComponent implements OnInit {
  @Input() token;
  posts = [];
    editingPost = null;

    updatePost(post) {
      this.editingPost = post;
    }
} 
```

继续`PostEditComponent`并导入所有需要的模块:

```
// src/app/post-edit/post-edit.component.ts
import { Component, OnInit, Input, EventEmitter, Output } from '@angular/core';
import { WpApiPosts } from 'wp-api-angular';
import { Headers } from '@angular/http';
// ... 
```

该组件将有两个输入:令牌和要编辑的实际文章。同样，我们将有一个输出(`EventEmitter`):

```
// ...
export class PostEditComponent implements OnInit {
  @Input() token;
  @Input() post;
  @Output() finish = new EventEmitter<void>();
  post_edit = {
    title: '',
    content: ''
    }
} 
```

组件一初始化，就给`post_edit`变量分配适当的标题和取自`post`变量的内容:

```
// ...
ngOnInit() {
  this.post_edit['title'] = this.post.title.rendered;
  this.post_edit['content'] = this.post.content.rendered;
} 
```

现在编写`updatePost`方法，它将执行认证。更新帖子并发出一个事件:

```
// ...
updatePost() {
  let headers: Headers = new Headers({
    'Authorization': 'Bearer ' + this.token
  });

  this.wpApiPosts.update(this.post.id, this.post_edit, { headers: headers })
  .toPromise()
  .then( response => {
    this.finish.emit(null);      
  })
} 
```

注意，`update`方法接受文章的 ID 和标题和内容的新值。

下面是编辑帖子的表单:

```
<!-- src/app/post-edit/post-edit.component.html -->
<div>
  <h2> Post Editing </h2>
    <form (ngSubmit)='updatePost()'>
      <div class="form-group">
        <label for="title">Post title</label>
        <input type="text" class="form-control" [(ngModel)]='post_edit.title' name='title' id="title" required>
      </div>

      <div class="form-group">
        <label for="content">Post content</label>
        <textarea class="form-cont  rol" id="content" required [(ngModel)]='post_edit.content' name='content'></textarea>
      </div>

      <button type="submit" class="btn btn-success">Submit</button>
    </form>
</div> 
```

就是这样:编辑功能已经准备好了！您现在可以通过运行以下命令来引导服务器:

```
ng serve --open 
```

并使用应用程序，以确保一切正常运行。

## 结论

在本文中，我们讨论了 Angular 的 WordPress API 客户端的用法。通过引入身份验证特性、列出用户和帖子，以及添加创建和操作帖子的功能，我们已经看到了它的作用。这个客户端允许您使用其他资源，如媒体和评论，但所有这些交互都与我们在这里讨论的非常相似。

希望你现在已经准备好将这里提供的信息应用到实践中，但是不要犹豫，把你的问题发给我！一如既往，谢谢你陪我，直到下一次。

## 分享这篇文章