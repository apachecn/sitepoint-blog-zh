# 用 Angular 7 中所有新的和值得注意的东西构建一个应用程序

> 原文：<https://www.sitepoint.com/build-an-app-with-everything-new-noteworthy-in-angular-7/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/12/04/angular-7-oidc-oauth2-pkce)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

Angular 7 于本季度早些时候发布，我对它的一些功能感到兴奋。如果你从 Angular 2 开始就一直关注 Angular，你就会知道升级有时会很痛苦。没有 Angular 3，但升级到 Angular 4 并不太糟糕，除了 Angular 的测试基础设施发生了一系列变化。Angular 4 到 Angular 5 没有痛苦，而 Angular 5 到 Angular 6 只需要对使用 RxJS 的类进行修改。

在我开始向您展示如何使用 authn/authz 构建 Angular 应用程序之前，让我们来看看这个版本中有哪些新的和值得注意的内容。

## 升级到角度 7

如果您使用 Angular CLI 创建了您的应用程序，那么您可以使用`ng update`轻松升级到最新版本。

```
ng update @angular/cli @angular/core 
```

您也可以使用[角度更新指南](https://update.angular.io/)获得完整的逐步说明。

![Angular Update Guide](img/0923eca3d0975ebc612bb84529a9ed0e.png)

## Angular 7 有什么新功能

Angular 7 有几个显著的特性，总结如下:

*   **CLI 提示**:这个特性已经添加到 Schematics 中，这样你就可以在运行`ng`命令时提示用户做出选择。
*   **性能增强**:Angular 团队发现许多人将`reflect-metadata`作为一种依赖(而不是一种只针对开发人员的依赖)。如果您使用上述方法进行更新，此依赖关系将会自动移动。Angular 7 还增加了**包预算**，这样当你的包超过特定大小时，你会得到警告。
*   **角形材料**:材料设计在 2018 年有重大更新，角形材料 v7 反映了这些更新。
*   虚拟滚动(Virtual Scrolling):这个特性允许你根据可见性加载/卸载列表的一部分。
*   **拖拽**:角状材质的 CDK 增加了该功能。

捆绑预算是最让我兴奋的功能。我看到很多捆绑包大小很大的有棱角的应用。您希望您的基线成本最小，所以这个特性应该有所帮助。使用 Angular CLI 创建新项目时，在`angular.json`中指定以下默认值。

```
"budgets": [{
  "type": "initial",
  "maximumWarning": "2mb",
  "maximumError": "5mb"
}] 
```

你可以使用 Chrome 的 [data saver](https://support.google.com/chrome/answer/2392284) 扩展来最大限度地了解你的应用程序使用的数据。

关于 Angular 7 新特性的更多细节，请参见 Angular 博客、InfoQ 上的[报道](https://www.infoq.com/news/2018/11/angular-7)或 Angular 项目的[变更日志](https://github.com/angular/angular/blob/master/CHANGELOG.md)。

现在你知道 Angular 7 有多棒了，让我们来看看如何用它创建安全的应用程序！

## 创建安全的 Angular 7 应用程序

创建 Angular 7 应用程序的一个简单方法是使用 [Angular CLI](https://angular.io/cli) 。要安装它，请运行以下命令:

```
npm i -g @angular/cli 
```

以下示例使用 Angular CLI 7.1.0。要验证您使用的是同一版本，您可以运行`ng --version`。

```
 _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/

Angular CLI: 7.1.0
Node: 11.1.0
OS: darwin x64
Angular:
...

Package                      Version
------------------------------------------------------
@angular-devkit/architect    0.11.0
@angular-devkit/core         7.1.0
@angular-devkit/schematics   7.1.0
@schematics/angular          7.1.0
@schematics/update           0.11.0
rxjs                         6.3.3
typescript                   3.1.6 
```

要创建新的应用程序，请运行`ng new ng-secure`。当提示路由时，键入“ **Y** ”。样式表格式与本例无关，所以选择您喜欢的格式。我用了 CSS。

在 Angular CLI 完成创建您的应用程序后，cd 进入其目录，运行`ng new`，并导航到`http://localhost:4200`以查看其外观。

![Default Angular App!](img/7df5235fb1575bdb96169daef05fcae9.png)

## 使用 OIDC 为您的 Angular 7 应用程序添加身份和认证

如果你正在为一个大型企业开发应用程序，你可能想把它们编码成使用同一组用户。如果你正在为你的每个应用创建新的用户商店，停止吧！有更简单的方法。您可以使用 OpenID Connect (OIDC)向您的应用程序添加身份验证，并允许它们使用同一个用户商店。

OIDC 需要身份提供商(或 IdP)。有许多知名的 IDP，如 Google、Twitter 和脸书，但是这些服务不允许您像在 Active Directory 中那样管理您的用户。Okta 允许这样做，*和*你可以使用 Okta 的 OIDC API。

[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)，当你完成后，请回来，这样你可以了解更多关于如何保护你的 Angular 应用的信息！

![Free developer account!](img/5c3612b3558d3d7e2146bdf0ad001b9c.png)

现在你有了一个开发者账户，我将向你展示几个在 Angular 7 应用中添加 OIDC 认证的技巧。但是首先，你需要在 Okta 创建一个新的 OIDC 应用程序。

### 在俄克拉荷马州创建 OIDC 应用程序

登录您的 Okta 开发者账户，导航至**应用** > **添加应用**。点击**网页**，点击**下一个**。给这个应用取一个你容易记住的名字，并指定`http://localhost:4200`为登录重定向 URI。点击**完成**。创建应用程序后编辑它，并将`http://localhost:4200`指定为注销重定向 URI。结果应该类似于下面的截图。

![Okta OIDC App](img/455d9ff4c8e05746d2baf6cfe4ea8b0d.png)

### 使用 angular-oauth2-oidc

[angular-oauth2-oidc](https://github.com/manfredsteyer/angular-oauth2-oidc) 库为 OAuth 2.0 和 oidc 提供支持。它最初是由[曼弗雷德·斯泰尔](https://twitter.com/manfredsteyer)创建的，包括许多社区贡献。

使用以下命令安装 angular-oauth2-oidc:

`npm i angular-oauth2-oidc@5.0.2`

打开`src/app/app.module.ts`并导入`OAuthModule`以及`HttpClientModule`。

```
import { HttpClientModule } from '@angular/common/http';
import { OAuthModule } from 'angular-oauth2-oidc';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    OAuthModule.forRoot()
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

修改`src/app/app.component.ts`以导入`OAuthService`，并将其配置为使用您的 Okta 应用程序设置。添加`login()`和`logout()`方法，以及一个用户名获取器。

```
import { Component } from '@angular/core';
import { OAuthService, JwksValidationHandler, AuthConfig } from 'angular-oauth2-oidc';

export const authConfig: AuthConfig = {
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  redirectUri: window.location.origin,
  clientId: '{yourClientId}'
};

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'ng-secure';

  constructor(private oauthService: OAuthService) {
    this.oauthService.configure(authConfig);
    this.oauthService.tokenValidationHandler = new JwksValidationHandler();
    this.oauthService.loadDiscoveryDocumentAndTryLogin();
  }

  login() {
    this.oauthService.initImplicitFlow();
  }

  logout() {
    this.oauthService.logOut();
  }

  get givenName() {
    const claims = this.oauthService.getIdentityClaims();
    if (!claims) {
      return null;
    }
    return claims['name'];
  }
} 
```

修改`src/app/app.component.html`增加**登录**和**注销**按钮。

```
<h1>Welcome to {{ title }}!</h1>

<div *ngIf="givenName">
  <h2>Hi, {{givenName}}!</h2>
  <button (click)="logout()">Logout</button>
</div>

<div *ngIf="!givenName">
  <button (click)="login()">Login</button>
</div>

<router-outlet></router-outlet> 
```

重启你的应用程序，你应该会看到一个登录按钮。

![App with Login button](img/34becaa284ae137d095a4de94cefa74b.png)

点击登录按钮，登录到您的 Okta 帐户，您应该会看到您的名字和一个注销按钮。

![App with name and Logout button](img/33ddb3a79abf1e2c56253a692f9aefc4.png)

很圆滑，是吧？

### Okta 的 Angular SDK

你也可以使用 [Okta 的 Angular SDK](https://www.npmjs.com/package/@okta/okta-angular) 来实现同样的功能。可以从安装开始。

`npm i @okta/okta-angular@1.0.7`

更改`app.module.ts`以配置您的 Okta 设置并导入`OktaAuthModule`。

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { OktaAuthModule } from '@okta/okta-angular';

const config = {
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  redirectUri: window.location.origin + '/implicit/callback',
  clientId: '{yourClientId}'
};

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    OktaAuthModule.initAuth(config)
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

您可能会注意到重定向 URI 与前一个略有不同。要做到这一点，你需要修改你的 Okta 应用程序并添加`http://localhost:4200/implicit/callback`作为登录重定向 URI。

修改`src/app/app-routing.ts`以获得该路径的路线。

```
import { OktaCallbackComponent } from '@okta/okta-angular';

const routes: Routes = [
  {
    path: 'implicit/callback',
    component: OktaCallbackComponent
  }
]; 
```

将`app.component.ts`更改为使用`OktaAuthService`来确定用户是否通过身份验证。

```
import { Component, OnInit } from '@angular/core';
import { OktaAuthService, UserClaims } from '@okta/okta-angular';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  title = 'ng-secure';
  isAuthenticated: boolean;
  email: string;

  constructor(public oktaAuth: OktaAuthService) {
  }

  async ngOnInit() {
    this.isAuthenticated = await this.oktaAuth.isAuthenticated();
    this.user = await this.oktaAuth.getUser();
    // Subscribe to authentication state changes
    this.oktaAuth.$authenticationState.subscribe( async(isAuthenticated: boolean)  => {
      this.isAuthenticated = isAuthenticated;
      this.user = await this.oktaAuth.getUser();
    });
  }
} 
```

然后更新`app.component.html`使用`isAuthenticated`并显示用户名。

```
<h1>Welcome to {{ title }}!</h1>

<div *ngIf="isAuthenticated">
  <h2>Hi, {{user?.name}}!</h2>
  <button (click)="oktaAuth.logout()">Logout</button>
</div>

<div *ngIf="!isAuthenticated">
  <button (click)="oktaAuth.loginRedirect()">Login</button>
</div>

<router-outlet></router-outlet> 
```

重新启动你的应用程序，你应该可以使用 Okta 的 Angular SDK 登录你的应用程序。

### 使用授权代码流

OAuth 有一个新的规范草案，名为用于基于浏览器的应用的 OAuth 2.0。这是由 Okta 自己的 [Aaron Parecki](https://twitter.com/aaronpk) 创造的，包含了[一个有趣的条款](https://tools.ietf.org/html/draft-parecki-oauth-browser-based-apps#section-7.8)。

> OAuth 2.0 隐式授权流(在 OAuth 2.0 [RFC6749]的第 4.2 节中定义)通过在 HTTP 重定向(前通道)中立即接收访问令牌来工作，而无需代码交换步骤。PKCE [RFC7636](根据第 6 节的要求)无法保护隐式流，因此客户端和授权服务器不得将隐式流用于基于浏览器的应用。

angular-oauth2-oidc 和 Okta 的 Angular SDK 都使用隐式流，这是在 Aaron 的规范草案中最近的讨论之前被接受的实践。那么，你如何遵循亚伦的建议，在你的 Angular 应用程序中使用 PKCE 的授权代码流呢？有几个选项:

*   angular-oauth2-oidc 有一个分支叫做[angular-oauth 2-oidc-code flow](https://www.npmjs.com/package/angular-oauth2-oidc-codeflow)。
*   JS 的 AppAuth 支持授权代码流，完全支持 PKCE。

我试着用 Okta 的 angular-oauth2-oidc-codeflow。我使用了上面 angular-oauth2-oidc 示例中的代码，发现我只需要修改一些东西(在用`npm i angular-oauth2-oidc-codeflow`安装它之后):

1.  进口应该来自`'angular-oauth2-oidc-codeflow'`
2.  `AppComponent`中的`login()`方法应该改为使用 auth 代码流。

```
 login() {
      this.oauthService.initAuthorizationCodeFlow();
    } 
```

做了这些改动后，我试着登录了我原来的 SPA 应用。我收到的错误是`unsupported_response_type`。我尝试用 [PKCE](https://oauth.net/2/pkce/) 创建一个新的原生应用，但是失败了，因为 angular-oauth2-oidc-codeflow 不发送代码挑战。

在我的[构建一个带有电子认证的桌面应用](https://developer.okta.com/blog/2018/09/17/desktop-app-electron-authentication)中，我成功地使用了 AppAuth 和 PKCE。这样做的原因是因为它是一个桌面应用程序，不发送`origin`头。Okta 的令牌端点不允许 CORS(跨源资源共享)，因此它在浏览器客户端中不起作用。

我们希望尽快解决这个问题。随着行业的发展，我们将尽最大努力让我们的图书馆与最佳实践保持同步。同时，我们建议您使用 CSP(内容安全策略)来确保第三方脚本无法访问您的 Angular 应用程序。

参见 [10 种保护 Spring Boot 申请的绝佳方法](https://developer.okta.com/blog/2018/07/30/10-ways-to-secure-spring-boot)了解如何添加 Spring Boot 的 CSP。

你可能还会发现弥迦·西尔弗曼的 [PKCE 命令行](https://github.com/dogeared/pkce-cli)项目很有趣。

## 根据群组限制 Angular 7 应用程序的访问权限

如果你想基于用户组显示/隐藏你的应用组件，你需要向你的 ID 令牌添加“组”声明。登录你的 Okta 账户，导航到 **API** > **授权服务器**，点击**授权服务器**标签，编辑默认的授权服务器。点击**索赔**标签和**添加索赔**。将其命名为“groups”，并将其包含在 ID 标记中。将值类型设置为“Groups”，并将过滤器设置为`.*`的正则表达式。

现在，您可以创建一个 Angular 指令来根据用户组显示/隐藏元素。目前有一个[未解决的问题](https://github.com/okta/okta-oidc-js/issues/36),显示你可能如何着手做这件事。

## 使用自动防护装置控制对路线的访问

Angular 的[路由器文档](https://angular.io/guide/router#guard-the-admin-feature)包括了一个如何创建`AuthGuard`来保护路由的例子，这样它们只对经过认证的用户可用。

Okta 的 Angular SDK 配有一个`OktaAuthGuard`，你可以用它来保护你的路线。在允许用户导航到路由之前，它会验证是否存在有效的访问令牌。下面是如何在`app-routing.module.ts`中配置的例子。

```
import { OktaAuthGuard } from '@okta/okta-angular';

const routes: Routes = [
  { path: 'secure', component: MySecureComponent, canActivate: [OktaAuthGuard] }
] 
```

您可以在 20 分钟内使用 OpenID Connect 和 Okta 为 angular-oauth2-oidc 实现类似的 auth guard，如[Angular authentic 所示](https://developer.okta.com/blog/2017/04/17/angular-authentication-with-oidc)。

```
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot, CanActivate, Router, RouterStateSnapshot } from '@angular/router';
import { OAuthService } from 'angular-oauth2-oidc';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {

  constructor(private oauthService: OAuthService, private router: Router) {}

  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean {
    if (this.oauthService.hasValidIdToken()) {
      return true;
    }

    this.router.navigate(['/']);
    return false;
  }
} 
```

## Angular 7 CLI 教程和带 Spring Boot 的 Angular 7 CRUD

唷，关于 Angular 7 的身份验证信息可真多！对于更直接的角度内容，我邀请你看一看我最近升级到 Angular 7 的几个教程。

最近更新了几个教程使用 Angular 7。

*   [**Angular 7 和 Angular CLI 教程**](https://github.com/mraible/ng-demo) :一个向你展示如何从头开始创建一个 app，以及测试它的教程。包括如何添加角度材料、引导和 Okta 认证的部分。
*   [**用 Angular 7.0 和 Spring Boot 2.1**](https://developer.okta.com/blog/2018/08/22/basic-crud-angular-7-and-spring-boot-2) 构建一个基本的 CRUD 应用:以 Spring Boot 后端和 Angular UI 为特色的教程。

事实上，我非常喜欢玩 Angular 7，我把基本的 CRUD 应用程序教程变成了截屏！

[https://www.youtube.com/embed/HoDzatvGDlI](https://www.youtube.com/embed/HoDzatvGDlI)

## JHipster 和 Angular 7

我是一个名为 JHipster 的项目的委托人。JHipster 允许你快速简单地生成一个有角度的 UI 的 Spring Boot 应用程序。JHipster 团队在其 [5.6.0 版本](https://www.jhipster.tech/2018/11/02/jhipster-release-5.6.0.html)中升级到 Angular 7。您可以使用单个 JDL 文件创建一个带有 Angular 的 JHipster 应用程序。JDL 代表 [JHipster 领域语言](https://www.jhipster.tech/jdl/)。

要查看 JHipster 的运行情况，请使用`npm i generator-jhipster`安装它，并使用以下 JDL 创建一个`app.jh`文件。

```
application {
  config {
    baseName blog,
    applicationType monolith,
    packageName com.jhipster.demo.blog,
    prodDatabaseType mysql,
    cacheProvider hazelcast,
    buildTool maven,
    clientFramework angular,
    useSass true,
    testFrameworks [protractor]
  }
} 
```

JHipster 默认使用 JWT 认证，但是您可以很容易地将其切换为使用 OIDC 认证(提示:只需将`authenticationType oauth2`添加到`app.jh`)。

创建一个`blog`目录并在其中运行`jhipster import-jdl app.jh`。在一两分钟内，你将拥有一个功能齐全(并且经过良好测试)的 Spring Boot + Angular + Bootstrap 应用程序！如果你想给 CRUD 添加实体，请看[这个样本 JDL](https://github.com/jhipster/jdl-samples/blob/master/blog.jh) 。

JDL 提到的例子使用 React 作为它的`clientFramework`。确保将其更改为`angular`以使用角度 7。

如果你以前从未听说过 JHipster，你应该从 InfoQ 下载免费的 JHipster 迷你书！这是我写的一本书，旨在帮助你开始学习当今的 hip 技术:Angular、Bootstrap 和 Spring Boot。5.0 版本是[最近发布的](http://www.jhipster-book.com/#!/news/entry/jhipster-mini-book-v5-now-available)。

## 了解有关 Angular 7、JHipster 和 OAuth 2.0 的更多信息

我希望你喜欢学习 Angular 7 以及如何将 authn/authz 添加到 Angular 应用程序中。我在这个博客上写了很多关于 Angular 的东西。请阅读下面的帖子，了解关于这个现代 web 框架的更多信息。

*   [用 Angular 7.0 和 Spring Boot 2.1 构建一个基本的 CRUD 应用](https://developer.okta.com/blog/2018/08/22/basic-crud-angular-7-and-spring-boot-2)
*   与 React、Spring Boot 和杰普斯特一起建立照片库 PWA
*   [使用 OAuth 2.0 和 JHipster 开发微服务架构](https://developer.okta.com/blog/2018/03/01/develop-microservices-jhipster-oauth)
*   什么是 OAuth 2.0 隐式授权类型？
*   [什么是 OAuth 2.0 授权码授予类型？](https://developer.okta.com/blog/2018/04/10/oauth-authorization-code-grant-type)

如果你喜欢这篇文章，请在社交媒体{ [Twitter](https://twitter.com/oktadev) 、[脸书](https://www.facebook.com/oktadevelopers)、 [LinkedIn](https://www.linkedin.com/company/oktadev/) 、 [YouTube](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q) 上关注我们，了解我们何时发布了其他精彩内容！

## 分享这篇文章