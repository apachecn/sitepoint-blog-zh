# 用 NodeJS 和 Angular 构建 Twitter 客户端

> 原文：<https://www.sitepoint.com/building-twitter-app-using-angular/>

在本教程中，我们将看看如何用 NodeJS 构建一个基本的 Twitter 客户端，以及一个显示你的家庭时间表的 Angular 应用程序。这是一个快速浏览，介绍了在设置自己的 Twitter 客户端和 Angular 应用程序时需要考虑的事情。

首先，我们将构建一个 NodeJS 服务器，它将处理 Twitter API 和 Angular 应用程序之间的通信。然后，我们将构建 Angular 应用程序来显示您的 Twitter 时间线并与之交互。

虽然您可以在没有任何 NodeJS 或 Angular 经验的情况下阅读本教程，但我将假设您在本文中已经掌握了一些知识。

## 设置项目

你需要在你的机器上安装一个最新版本的[节点。然后确保你有](https://www.sitepoint.com/beginners-guide-node-package-manager/)[角度 CLI](https://www.sitepoint.com/ultimate-angular-cli-reference/#installingangularcli) 。如果您需要这些任务中的任何一项的帮助，所提供的链接为您提供了一个很好的起点。

项目源代码可以在 [GitHub](https://github.com/sitepoint-editors/twitter-angular-client) 上找到。你可以通过用 g it 克隆它或者从回购的 [GitHub 存档](https://github.com/sitepoint-editors/twitter-angular-client/archive/master.zip)下载文件来让它在本地运行。

```
git clone https://github.com/sitepoint-editors/twitter-angular-client 
```

一旦你有了文件，你需要从你的终端运行`npm install`来安装所有的依赖项。然后我们就可以开始工作了！

## 在 NodeJS 中创建 Twitter 客户端

要访问 Twitter 的 API，我们需要注册一个新的“app”，本质上是 Twitter 给我们一套凭证的一种方式。这些对于您的应用程序是唯一的，所以不要在任何地方公开共享它们。当然，你必须有一个 Twitter 账户来访问这些数据。

首先，进入[https://apps.twitter.com/](https://apps.twitter.com/)并选择*创建新应用*。您可以填写应用程序的名称、描述和网站 URL。(你现在可以用一个假的网址。如果你发布你的应用程序，它应该是你真正的网站。)

在那里，您将看到包含您的详细信息的新应用程序页面。进入*密钥和访问令牌*页面，你可以在底部看到一个按钮*创建我的访问令牌*。点击按钮，然后应该会看到四个值:*消费者密钥* (API 密钥)*消费者秘密* (API 秘密)*访问令牌*和*访问令牌秘密*。我们一会儿会用到这些，所以一定要把这些信息放在手边。

### 在 NodeJS 中创建 Twitter 客户端

现在是时候深入我们的 NodeJS 服务器了，它将弥合 Twitter 的 API 和 Angular 应用程序之间的差距。在项目中，您应该会看到`server.js`文件，您需要打开并调整它。

首先，您需要更新包含您之前从 Twitter 应用程序收到的凭证的块。您应该将这些值复制到这里的块中。我们正在使用一个名为 [Twit](https://npmjs.org/package/twit) 的 Twitter 包来帮助我们连接到 Twitter，尽管还有其他具有不同功能级别的可用包。

```
const client = new Twitter({
  consumer_key: 'CONSUMER_KEY',
  consumer_secret: 'CONSUMER_SECRET',
  access_token: 'ACCESS_TOKEN',
  access_token_secret: 'ACCESS_TOKEN_SECRET'
}); 
```

现在我们应该可以连接到 Twitter 了。我们还使用流行的 ExpressJS 来创建和管理我们的服务器。现在您已经安装了凭据，可以运行服务器了。

```
node server 
```

我们的下一步是创建几个路由来处理我们的 Angular 应用程序加载 Twitter 数据所需的 HTTP 请求。我们的第一个途径是获取当前用户，并验证他们的凭证。您提供的访问令牌和密码与您的 Twitter 帐户相关联，因此在这种情况下，您将是授权用户。当这个路由被调用时，它将调用 Twitter `account/verify_credentials`端点并返回一个包含您的用户数据的对象。

```
app.get('/api/user', (req, res) => {
  client.get('account/verify_credentials').then(user => {
    res.send(user)
  }).catch(error => {
    res.send(error);
  });
}); 
```

我们将创建的下一条路线是获取您的家庭时间表。它请求`statuses/home_timeline`端点，并传递一些参数给我们更多我们需要的数据。

由于 Twitter API 上的速率限制，我们实现了一个简单的缓存，它每分钟只请求一次新数据(这是收到错误之前的最大速率)。它基本上跟踪最后一次响应和它被请求的时间，只允许新的 Twitter 请求在一分钟后运行。构建 Twitter 应用程序时，速率限制是一个主要的设计考虑因素。

```
let cache = [];
let cacheAge = 0;

app.get('/api/home', (req, res) => {
  if (Date.now() - cacheAge > 60000) {
    cacheAge = Date.now();
    const params = { tweet_mode: 'extended', count: 200 };
    if (req.query.since) {
      params.since_id = req.query.since;
    }
    client
      .get(`statuses/home_timeline`, params)
      .then(timeline => {
        cache = timeline;
        res.send(timeline);
      })
      .catch(error => res.send(error));
  } else {
    res.send(cache);
  }
}); 
```

最后，我们创建一组路由来处理一条 tweet 的 like/like 和 retweet/un tweet 操作。这不仅能让我们读取数据，还能让我们采取行动。这将要求您将应用程序访问级别设置为*读写*(以防您在 Twitter 应用程序设置中更改了它)。

```
app.post('/api/favorite/:id', (req, res) => {
  const path = (req.body.state) ? 'create' : 'destroy';
  client
    .post(`favorites/${path}`, {id: req.params.id})
    .then(tweet => res.send(tweet))
    .catch(error => res.send(error));
});

app.post('/api/retweet/:id', (req, res) => {
  const path = (req.body.state) ? 'retweet' : 'unretweet';
  client
    .post(`statuses/retweet/${req.params.id}`)
    .then(tweet => res.send(tweet))
    .catch(error => res.send(error));
}); 
```

有许多用于处理 Twitter 数据的 Twitter APIs，但是基本规则是一样的。这里唯一的主要问题是我们已经将凭证硬编码到单个用户，你需要它来设置你自己的 OAuth 服务器(或者使用一个现有的)来处理认证方面，你可以在 [Twitter 认证文档](https://developer.twitter.com/en/docs/basics/authentication/overview/oauth)上了解更多。

## 创建角度应用程序

现在是时候将注意力转向使用我们创建的服务器的 Angular 应用程序了。我们将看看应用程序的关键方面，以及它们如何创建最终结果。我们使用 [Clarity](https://vmware.github.io/clarity) 为 UI 层构建了这个应用程序(它给了我们许多有用的布局组件)，但是除此之外，一切都是有角度的。

要运行 Angular 应用程序，只需运行以下命令，然后打开 http://localhost:4200:

```
ng serve 
```

在应用程序内部，我们在`src/app/tweet.ts`有一个模型，它包含描述 tweet 的大部分属性的 TypeScript 接口(有些已经被省略)。我相信正确地描述你的类型对于大规模和小规模的应用都是很重要的，所以这个界面给了我们一个 tweet 的形状。

### 角度推特服务

首先，我们需要一个可以向 NodeJS 服务器发出请求以获取最新 tweets 的服务。在 Angular 中，HttpClient 是用于发出 HTTP 请求的实用程序，所以我创建了一个 Angular 服务来封装这些调用的逻辑。打开`src/app/twitter.service.ts`，你会看到下面的代码:

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { environment } from '../environments/environment';
import { Tweet } from './tweet';

export interface TwitterResponse {
  data: any;
  resp: any;
}

@Injectable()
export class TwitterService {

  constructor(private http: HttpClient) { }

  user() {
    return this.http.get<TwitterResponse>(`${environment.api}/user`);
  }

  home(since?: string) {
    return this.http.get<TwitterResponse>(`${environment.api}/home?since=${since}`);
  }

  action(property: 'favorite'|'retweet', id: string, state: boolean) {
    return this.http.post<TwitterResponse>(`${environment.api}/${property}/${id}`, {state});
  }
} 
```

这是一个相当基本的服务，它拥有为我们将支持的每个 API 构建请求的方法。`user`方法将返回当前用户(总是您)。`home`方法将返回你的家庭时间表中最近的 200 条推文(或者从指定的最后一条推文以来出现了多少条)。最后，`action`属性通过发送一个布尔值`state`来切换状态，从而处理收藏或转发调用。

这个服务是通用的，每个方法都返回一个可观察的。如果你想了解更多，你可以阅读关于 RXJS 的 [Functional Reactive，但是它们在这里的使用方式类似于承诺的工作方式。我们马上会看到如何使用它们。](https://www.sitepoint.com/functional-reactive-programming-rxjs/)

### 使用 Angular TwitterService 加载用户

我们将在几个地方使用 TwitterService，从加载 AppComponent 开始。我们将使用它来加载用户详细信息(出现在右上角)，并加载主页的 tweets 列表。打开`src/app/app.component.ts`，您应该会看到以下代码:

```
import { Component , OnInit } from '@angular/core';
import { TwitterService } from './twitter.service';
import { Tweet } from './tweet';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss'],
  providers: [TwitterService]
})
export class AppComponent implements OnInit {
  user;

  constructor(private twitter: TwitterService) {}

  ngOnInit() {
    this.twitter.user().subscribe(user => this.user = user.data);
  }
} 
```

AppComponent 使用我们的 TwitterService 做一件主要的事情。组件一初始化，就触发`ngOnInit`方法，并请求用户数据。这里我们使用由`TwitterService.user`方法返回的可观察对象，当我们使用`subscribe`时，它将触发实际的 HTTP 请求。一旦返回，回调函数将存储用户属性，该属性用于在导航栏中显示内容。在下面的组件模板中可以看到用户属性绑定，比如`user.profile_image_url_https`:

```
<clr-main-container>
  <clr-header class="header-4">
    <div class="branding">
      <a class="nav-link">
        <div class="title">Twangular</div>
      </a>
    </div>
    <div class="header-actions" *ngIf="user">
      <a class="nav-link">
        <span class="nav-text">
          <img [src]="user.profile_image_url_https" class="avatar" />
          @{{user.screen_name}}
        </span>
      </a>
    </div>
  </clr-header>
  <div class="content-container">
    <main class="content-area">
      <app-tweets></app-tweets>
    </main>
  </div>
</clr-main-container> 
```

还有，`<app-tweets></app-tweets>`的使用会插入 TweetsComponent，它处理 tweets 的实际加载和显示，所以现在让我们来看看。

### 显示推文列表

为了帮助分离我们的逻辑，我们实际上有两个组件来显示 tweets 列表。tweets 组件管理 tweets 列表，还处理对 NodeJS 服务的赞或转发请求。然后 TweetComponent 用于显示实际的 tweet 格式和显示。我总是建议尝试将组件分成不同的角色，在这种情况下，TweetsComponent 负责处理数据交互，如加载和转发，而 TweetComponent 不知道加载数据，只显示内容。我们将从查看 tweets 组件开始，下面是`src/app/tweets/tweets.component.ts`的内容:

```
import { Component, OnInit, Input, OnDestroy } from '@angular/core';
import { Tweet } from '../tweet';
import { TwitterService } from '../twitter.service';

@Component({
  selector: 'app-tweets',
  templateUrl: './tweets.component.html',
  styleUrls: ['./tweets.component.scss']
})
export class TweetsComponent implements OnInit, OnDestroy {
  inflight = false;
  tweets: Tweet[] = [];
  ids = [];
  timer;
  since = '';

  constructor(private twitter: TwitterService) {}

  ngOnInit() {
    this.getTweets();
    this.timer = setInterval(() => this.getTweets(), 61000);
  }

  ngOnDestroy() {
    if (this.timer) {
      clearInterval(this.timer);
    }
  }

  getTweets() {
    this.twitter.home(this.since).subscribe(tweets => {
      tweets.data.reverse().forEach(tweet => {
        if (this.ids.indexOf(tweet.id_str) < 0) {
          this.ids.push(tweet.id_str);
          this.tweets.unshift(tweet);
        }
      });
      this.since = this.tweets[0].id_str;
      this.cleanUp();
    });
  }

  cleanUp() {
    if (this.tweets.length > 1000) {
      this.tweets.splice(1000);
      this.ids.splice(1000);
    }
  }

  action(action, index) {
    if (this.inflight) {
      return;
    }

    const stateKey = (action.property === 'favorite') ? 'favorited' : 'retweeted';
    const newState = !action.tweet[stateKey];

    this.inflight = true;
    this.twitter.action(action.property, action.tweet.id_str, newState).subscribe(tweet => {
      this.tweets[index][stateKey] = newState;
      this.tweets[index][action.property + '_count'] += (newState) ? 1 : -1;
      this.inflight = false;
    });
  }
} 
```

这个组件负责处理所有的加载和与 tweets 列表的交互。在`ngOnInit`方法中，我们调用该方法来获取推文，并设置每 61 秒重新加载最新推文的时间间隔。请记住，我们可以发出多少个请求是有速率限制的，所以这有助于我们保持在限制之下。`ngOnDestroy`方法只是在组件被移除时重置计时器，这是防止内存泄漏的好方法。

然后我们有了`getTweets`方法，它使用 TwitterService 请求 home timeline。它还传递一个字符串，该字符串包含接收到的最后一条 tweet ID，因此我们可以只请求自该 ID 创建以来的 tweet。当我们订阅时，请求被发出，回调给我们推文列表。因为我们想首先显示最近的 tweet，所以我们反转数组，然后将它们推到现有的 tweet 列表中，更新最新的 tweet ID 引用，然后进行一些清理。如果我们有超过 1000 个条目，我们丢弃剩余的条目以帮助控制内存消耗。

*需要注意的是，我们使用了 tweets 的`id_str`属性。这是因为 JavaScript(以及随后的 JSON)无法准确处理 53 位以上的数字(或者换句话说，JavaScript 无法处理极大的数字，参见[雪花 IDs](https://developer.twitter.com/en/docs/basics/twitter-ids) )。*

`action`方法将用于调用 TwitterService 来收藏或转发一条 tweet。它采取动作(收藏或转发)，然后切换属性的状态。(例如，如果你之前转发了，它将不再转发)。一条推文包含关于你是否已经收藏或转发的元数据，以及存在多少收藏或转发的计数。因为您的收藏或转发操作会改变状态，所以该方法也会相应地更新 tweet 值。

组件的模板可在`src/app/tweets/tweets.component.html`找到，如下所示。这相当简单，因为它遍历一个 tweet 列表，并为每个 tweet 显示一个 TweetComponent 实例。如果 tweet 是一个转发，它也绑定了转发状态。如果是转发，并且是我们真正想要显示的，Twitter 会为原始推文的数据添加一个`retweeted_status`属性。因为我们想要显示转发状态，所以它实际上替换了实际的 tweet。

```
<div class="tweets">
  <div class="card" *ngFor="let tweet of tweets; let i = index">
    <app-tweet *ngIf="tweet.retweeted_status" [tweet]="tweet.retweeted_status" [retweet]="tweet" (action)="action($event, i)"></app-tweet>
    <app-tweet *ngIf="!tweet.retweeted_status" [tweet]="tweet" (action)="action($event, i)"></app-tweet>
  </div>
</div> 
```

该模板展示了 TweetComponent 上输入和输出绑定的使用。输入`[tweet]`和`[retweet]`将数据传递给 TweetComponent，输出`(action)`在动作发生时调用 TweetsComponent 上的`action`方法(收藏或转发动作)。

要查看 tweets 是如何显示的，让我们转到 TweetComponent，它将大量数据绑定到一个 card 组件中，可以在`src/app/tweet/tweet.component.html`找到。

```
<div class="card-header">
  <img [src]="tweet.user.profile_image_url_https" class="avatar" /> {{tweet.user.name}} (@{{tweet.user.screen_name}})
  <span *ngIf="retweet" class="retweeted"><clr-icon shape="sync"></clr-icon> Retweeted by {{retweet.user.name}} (@{{retweet.user.screen_name}})</span>
  <div class="card-header-actions">
    <button type="button" class="btn btn-icon" [ngClass]="{'btn-success': tweet.favorited}" (click)="toggleAction('favorite')"><clr-icon shape="heart"></clr-icon> {{tweet.favorite_count}}</button>
    <button type="button" class="btn btn-icon" [ngClass]="{'btn-success': tweet.retweeted}" (click)="toggleAction('retweet')"><clr-icon shape="share"></clr-icon> {{tweet.retweet_count}}</button>
  </div>
</div>
<div class="card-block">
  <div class="card-img" *ngIf="hasPhoto(tweet)">
      <img [src]="tweet.entities?.media[0].media_url_https" (click)="media = true" />
  </div>
  <p class="card-text" [innerHTML]="tweet | tweet"></p>
</div>
<div class="card-footer" *ngIf="!retweet">
    {{tweet.created_at | amTimeAgo}}
    <clr-icon shape="minus"></clr-icon>
    {{tweet.created_at | date:'medium'}}
</div>
<div class="card-footer" *ngIf="retweet">
    {{retweet.created_at | amTimeAgo}}
    <clr-icon shape="minus"></clr-icon>
    {{retweet.created_at | date:'medium'}}
</div>
<clr-modal [(clrModalOpen)]="media" *ngIf="tweet.entities.media" clrModalSize="lg">
  <h3 class="modal-title"><img [src]="tweet.user.profile_image_url_https" class="avatar" /> {{tweet.user.name}} (@{{tweet.user.screen_name}})
    <span *ngIf="retweet" class="retweeted"><clr-icon shape="sync"></clr-icon> Retweeted by {{retweet.user.name}}</span></h3>
    <div class="modal-body">
      <img [src]="tweet.entities?.media[0].media_url_https" />
    </div>
    <div class="modal-footer" [innerHTML]="tweet | tweet"></div>
</clr-modal> 
```

我将只指出这个模板的几个关键方面。首先，`.card-header-actions`元素中的两个按钮显示了收藏和转发的数量。它们还有一个事件绑定`(click)="toggleAction('favorite')"`,调用 click 上的一个方法来处理动作。该方法将向 TweetsComponent 发出一个事件，这是使用`(action)`事件绑定来捕获的。

还有，你可以看到很多插值绑定，就是`{{tweet.favorite_count}}`。有很多内容要显示，所以这是将文本或内容打印到页面中最简单的方法。

接下来，tweet 的主要文本被直接绑定到`.card-text`元素的 innerHTML 属性，正如您在这里看到的。这是因为我们希望显示 HTML 内容而不仅仅是文本，因为它允许我们注入带有链接的内容。

```
<p class="card-text" [innerHTML]="tweet | tweet"></p> 
```

这种与 innerHTML 的绑定之所以完成，是因为我们有一个自定义管道(稍后我们将回顾这个管道),它解析 tweet 并用链接替换一些内容。例如，如果一条 tweet 中有一个 URL，这将用一个实际的锚链接替换纯文本值。同样，如果推文提到另一个用户，它也会做同样的事情。我们还包括了`amTimeAgo`管道，这是一组用于时间管理的[角管道。](https://github.com/urish/angular-moment)

最后，底部还有一个`clr-modal`元素，这是一个[清晰模态](https://vmware.github.io/clarity/documentation/v0.11/modals)。如果 tweet 包含一张图片，用户点击图片(在上面的`.card-img`元素中可以找到),它将打开一个更大版本的模态。

为了总结这个组件，回顾一下`src/app/tweet/tweet.component.ts`中的组件控制器是很有用的，它定义了一些重要的属性:

```
import { Component, EventEmitter, Output, Input } from '@angular/core';
import { Tweet } from '../tweet';

@Component({
  selector: 'app-tweet',
  templateUrl: './tweet.component.html',
  styleUrls: ['./tweet.component.scss']
})
export class TweetComponent {
  @Input() tweet: Tweet;
  @Input() retweet: Tweet;
  @Output() action = new EventEmitter<{property: string, tweet: Tweet}>();

  hasPhoto(tweet: Tweet) {
    if (tweet.entities.media
        && tweet.entities.media.length
        && tweet.entities.media[0].type === 'photo') {
      return true;
    }
    return false;
  }

  toggleAction(property: 'favorite'|'retweet') {
    this.action.emit({property, tweet: this.tweet});
  }
} 
```

该组件声明了两个输入`@Input() tweet`和`@Input() retweet`，以及一个输出`@Output() action`。这两个输入允许我们绑定要显示的 tweet，如果是 retweet，我们也绑定 tweet 信息。您看到这些值是从 TweetsComponent 模板传递过来的。

当发生某些事情时，输出会提醒父组件，在这种情况下，我们希望在点击这些按钮时提醒收藏或转发 tweet 的操作。这些信息只是简单地传递，就像普通的 JavaScript 事件一样，TweetsComponent 组件将通过`action`方法处理这些信息。

在我们总结显示推文的方式之前，让我们快速看一下这个 TweetPipe，我们用它来格式化和解析推文。

### 使用 TweetPipe 格式化数据

最后一个要回顾的主要功能是 TweetPipe，位于`src/app/tweet.pipe.ts`并显示在下面。它处理 tweet 文本和元数据的解析，以提供格式化:

```
import { Pipe, PipeTransform } from '@angular/core';
import { Tweet } from './tweet';
import { DomSanitizer } from '@angular/platform-browser';

@Pipe({
  name: 'tweet'
})
export class TweetPipe implements PipeTransform {

  constructor(private sanitizer: DomSanitizer) {}

  transform(tweet: Tweet, args?: any): any {
    let text = this.sanitizer.sanitize(tweet.full_text);

    if (tweet.entities.user_mentions) {
      tweet.entities.user_mentions.forEach(mention => {
        text = text.replace(new RegExp(`@${mention.screen_name}`, 'gi'), `<a href="https://twitter.com/${mention.screen_name}" target="_blank">@${mention.screen_name}</a>`);
      });
    }

    if (tweet.entities.urls) {
      tweet.entities.urls.forEach(url => {
        text = text.replace(url.url, `<a href="${url.url}" target="_blank">${url.display_url}</a>`);
      });
    }

    if (tweet.entities.media) {
      tweet.entities.media.forEach(url => {
        text = text.replace(url.url, '');
      });
    }

    text = text.replace(/\n/gm, '<br />');
    return this.sanitizer.bypassSecurityTrustHtml(text);
  }
} 
```

当您创建一个定制管道时，您必须实现`transform`方法并返回您希望显示的值。在这种情况下，我们接收整个 tweet 对象(不仅仅是文本，因为我们需要元数据)，并以几种方式处理它。Twitter 以一致的结构返回数据，因此我们只需检查每个属性，以确定是否存在任何 URL、媒体或提及。如果是，我们用一个链接替换那些值，或者在媒体的情况下，它被删除，因为图像已经被显示。

然而，出于安全原因，Angular 通常会阻止您传递 HTML 并将其绑定到模板中。Angular 允许您绕过它，直接处理净化输入。我们在这里解决这个问题的方法是首先净化 tweet 文本，这将删除任何潜在的危险内容(例如带有`javascript:`或脚本标签的链接)。然后我们修改文本字符串，用链接标签替换提及和 URL。最后，我们使用`DomSanitizer.bypassSecurityTrustHtml`方法绕过显示文本的安全限制。但是，由于我们在开始时对文本进行了净化，因此内容是可信的。

当你有一个这样的管道时，要非常小心安全，我建议你查看一下[角度安全指南](https://angular.io/guide/security)。

## 摘要

这就结束了我们对 Angular Twitter 客户端的快速浏览，我们看到了许多 Angular 的关键特性，并学习了如何构建一个连接到 Twitter API 的基本 NodeJS 服务器。这只是一个基本的例子，但是可以很容易地添加许多额外的功能，比如撰写 tweets、查看用户资料和其他交互。我鼓励你研究一下 Twitter API 文档，看看你有什么选择，看看你还能构建什么！

## 分享这篇文章