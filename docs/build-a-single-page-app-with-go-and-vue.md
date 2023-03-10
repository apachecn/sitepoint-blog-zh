# 使用 Go 和 Vue 构建单页应用程序

> 原文：<https://www.sitepoint.com/build-a-single-page-app-with-go-and-vue/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/10/23/build-a-single-page-app-with-go-and-vue)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

单页应用程序(SPAs)通过提供丰富的 UI 交互、快速反馈以及无需下载和安装传统应用程序的轻松感来改善用户体验。现在浏览器是操作系统，网站是 app。虽然 SPA 并不总是解决问题的办法，但对于那些依赖于快速用户交互的应用来说，SPA 越来越普遍。

对最终用户来说，设计良好的水疗中心就像彩虹和独角兽。从开发人员的角度来看，现实可能恰恰相反。认证、路由、状态管理、数据绑定等长期以来在后端解决的棘手问题变成了耗时的前端挑战。幸运的是，像 Vue、React 和 Angular 这样的 JavaScript 框架可以帮助我们制作强大的应用程序，并将更多的时间放在关键功能上，而不是重新发明轮子。

## 关于 Vue.js

谁能比它的创造者尤雨溪更适合描述 Vue 呢？

> Vue(读作`/vjuː/`，类似于 view)是一个用于构建用户界面的渐进式框架。它被从头设计为可增量采用，并且可以根据不同的用例在库和框架之间轻松伸缩。它由一个只关注视图层的可接近的核心库和一个支持库的生态系统组成，支持库可以帮助您处理大型单页面应用程序中的复杂性。

以下是 Vue 的一些优势:

*   平缓的学习曲线和较低的准入门槛
*   提供使用`vue-cli`引导应用程序的能力，省去设置 webpack 和复杂构建管道的麻烦
*   社区爆发式增长！Vue 现在在 GitHub 上的星星比 React 和 Angular 多
*   它足够灵活，可以一个组件一个组件地以合理的速度采用

## 创建您的 Vue + Go 应用程序

在本教程中，您将创建一个单页应用程序，展示对 GitHub 上开源项目的热爱。对于前端，您将使用 Vue 和流行的工具，如`vuex`、`vue-cli`、`vuetify`和`vue-router`。在后端，您将使用 Go 编写一个 REST API，并将您的数据保存在 MongoDB 中。

身份验证和用户管理可能是一个大问题，所以当从 SPA 和 [Okta 的 Go JWT 验证器](https://github.com/okta/okta-jwt-verifier-golang)发出请求时，您将使用基于 JSON Web Token (JWT)的身份验证，作为后端的中间件来验证每个请求的用户令牌。

一旦完成，用户将能够通过 OpenID Connect (OIDC)进行认证，在 GitHub 上搜索项目，喜欢这些项目，甚至在需要的地方添加注释！

### 创建 Vue 和 Go 目录结构

为了简单起见，让我们在同一个项目中编写 REST API 和 SPA，从 Go 工作区中的项目目录开始。

Go 项目位于环境变量`$GOPATH`指向的目录中。为了找到当前的`$GOPATH`值，运行:`go env GOPATH`。要了解更多关于 GOPATH 的信息，包括如何自己设置它，请参考官方的 [Go 文档](https://github.com/golang/go/wiki/SettingGOPATH)。

如果您完全是新手，请查看本文以了解项目在 GOPATH 目录中是如何组织的。

定义了 GOPATH 之后，您现在可以为您的项目创建一个目录:

```
mkdir -p $GOPATH/src/github.com/{YOUR_GITHUB_USERNAME}/kudo-oos 
```

为了让你的水疗中心快速起飞，利用 [vue-cli](https://cli.vuejs.org/) 的脚手架功能。CLI 将提示您一系列选项，选择适合本项目的技术:`vue.js`、`vuex`和`webpack`。

通过运行安装`vue-cli`:

```
yarn global add @vue/cli 
```

然后，创建一个新的 Vue 项目:

```
mkdir -p pkg/http/web
cd pkg/http/web
vue create app 
```

您将被提示一系列关于项目构建细节的问题。对于此应用程序，选择所有默认选项。

![Use the vue-cli to initialize a program](img/c97267457a4e2eeeaf7e110425d899ef.png)

恭喜你，你已经创建了你的 Vue.js SPA！通过运行以下命令来尝试一下:

```
cd app
yarn install
yarn serve 
```

在浏览器中打开这个 URL:[http://localhost:8080](http://localhost:8080)，您应该会看到以下内容。

![Vue default page](img/d534e41d5b4a8867d657bdaf1cedc16f.png)

接下来，让我们使用`vuetify`让您的水疗中心变得更现代、更灵敏。

## 添加 Vuetify

[Vuetify](https://vuetifyjs.com/en/) 是一个 Vue.js 组件的集合，它抽象了[材料设计的](https://material.io/design/)概念。Vuetify 提供了开箱即用的特性，包括网格系统、排版、基本布局，以及卡片、对话框、芯片、标签、图标等组件。Vuetify 将为您通向丰富的 UI 铺平道路！

安装 vuetify 时，会提示您一系列问题。为了简单起见，请再次使用默认选项。

```
vue add vuetify 
```

![Add vueify](img/8f84ea76efbba315816eef51348c41b5.png)

再次旋转您的水疗中心，看看 vuetify 的行动。

```
yarn serve 
```

![Vuetify page](img/38a5325e79dd0b95f8ec9ba054fa9d67.png)

## 使用 Okta 为您的 Vue 应用程序添加身份验证

编写安全的用户验证和构建登录页面很容易出错，这可能会导致新项目的失败。Okta 使快速安全地实现所有用户管理功能变得简单。注册一个[免费开发者账户](https://developer.okta.com/signup/)并在 Okta 创建一个 OIDC 应用程序。

![Okta sign-up page](img/781a3f00bc7012aa1a6164eafafebdbe.png)

登录后，单击“添加应用程序”创建一个新的应用程序。

![Okta add application](img/082eabb8e53311e334475e80586881e6.png)

选择“单页应用程序”平台选项。

![Okta create application](img/3bfe7058c7a3cfa7be44c94b15675967.png)

默认的应用程序设置应该和图中的一样。

![Okta application settings](img/f840e6a8c39dbff3537859e39f932bbb.png)

接下来，通过运行以下命令安装 Okta Vue SDK:

```
yarn add @okta/okta-vue 
```

## 创建您的 Vue 应用程序路线

对于这个 app，你只需要 4 个路由，除了登录路由，其他都需要认证。

根路由`/`是我们的登录页面，登录组件将在这里呈现。一旦用户通过了身份验证，我们会将他们重定向到`/me`路径，在这里大部分功能会发生:用户应该能够通过 GitHub 的 REST API 查询 OSS 项目，查询返回的最喜欢的项目，查看关于项目的更多细节，并留下一个注释，描述为什么项目对他们很重要。

注意，`/me`和`repo/:id`都有一个`meta: { requiresAuth: true }`属性，指定用户必须通过身份验证才能访问应用程序的这个区域。如果用户没有通过验证，Okta 插件将使用它重定向到 Okta 的登录页面。

首先，创建`pkg/http/web/app/src/routes.js`并定义以下路线:

```
import Vue from 'vue';
import VueRouter from 'vue-router';
import Auth from '@okta/okta-vue'

import Home from './components/Home';
import Login from './components/Login';
import GitHubRepoDetails from './components/GithubRepoDetails';

Vue.use(VueRouter);
Vue.use(Auth, {
  issuer: {ADD_YOUR_DOMAIN},
  client_id: {ADD_YOUR_CLIENT_ID},
  redirect_uri: 'http://localhost:8080/implicit/callback',
  scope: 'openid profile email'
})

export default new VueRouter({
 mode: 'history',
 routes: [
   { path: '/', component: Login },
   { path: '/me', component: Home, meta: { requiresAuth: true }},
   { name: 'repo-details', path: '/repo/:id', component: GitHubRepoDetails, meta: { requiresAuth: true } },
   { path: '/implicit/callback', component: Auth.handleCallback() }
 ]
}); 
```

确保在指示的地方添加您的`domain`和`client_id`——这些值可以在 Okta 开发者控制台的应用概述页面上找到。调用`Vue.use(Auth, ...)`会将一个`authClient`对象注入到你的 Vue 实例中，这个对象可以通过在你的 Vue 实例中的任何地方调用`this.$auth`来访问。这是您将用来确保用户登录和/或强制用户识别他们自己！

## 创建 Vue 组件

`vue-router`库包含许多组件来帮助开发人员创建动态丰富的 ui。其中一个，`router-view, renders the component for the matched route. In our case, when the user accesses the root route` / `,` vue-router `will render the`登录`component as configured in` routers.js`。

打开`./kudo-oos/pkg/http/web/app/src/components/App.vue`并复制以下代码。

```
<template>
 <v-app>
   <router-view></router-view>
   <Footer />
 </v-app>
</template>

<script>
import Footer from '@/components/Footer.vue'

export default {
 name: 'App',
 components: { Footer },
 data() {
   return {}
 }
}
</script> 
```

对于除匹配路线组件之外的每个路线，Vue 将呈现`Footer`组件。创建`./kudo-oos/pkg/http/web/app/src/components/Footer.vue`并复制下面的代码来创建页脚组件。

```
<template>
 <v-footer class="pa-3 white--text" color="teal" absolute>
   <div>
     Developed with ❤️  by {{YOUR_NAME}} &copy; {{ new Date().getFullYear() }}
   </div>
 </v-footer>
</template> 
```

您的登录页面现在应该如下所示:

![Vue landing page](img/bf69fe29deb875b0b896538bb502885e.png)

随着登录组件的呈现，用户在单击登录按钮后将被重定向到登录页面。

![Okta sign-in](img/050274d6f6a107b4b37f81633d05310f.png)

成功登录后，用户将被重定向回您的应用程序，到达已配置的路由。在我们的应用中，那是`/me`路线。

![Vue app](img/8a60178b900c3eef24280dee4428c1ad.png)

`/me`路径被配置为呈现`Home`组件，后者依次呈现`Sidebar`、Kudos 和 Search `vuetify tabs`。每个选项卡呈现一组特定的`GitHubRepo`

继续创建`./kudo-oos/pkg/http/web/app/src/components/Home.vue`组件。

```
<template>
 <div>
   <SearchBar v-on:search-submitted="githubQuery" />
   <v-container grid-list-md fluid class="grey lighten-4" >
        <v-tabs
       slot="extension"
       v-model="tabs"
       centered
       color="teal"
       text-color="white"
       slider-color="white"
     >
       <v-tab class="white--text" :key="2">
         KUDOS
       </v-tab>
       <v-tab class="white--text" :key="1">
         SEARCH
       </v-tab>
     </v-tabs>
       <v-tabs-items style="width:100%" v-model="tabs">
         <v-tab-item :key="2">
           <v-layout row wrap>
             <v-flex v-for="kudo in allKudos" :key="kudo.id" md4 >
               <GitHubRepo :repo="kudo" />
             </v-flex>
           </v-layout>
         </v-tab-item>
         <v-tab-item :key="1">
           <v-layout row wrap>
             <v-flex v-for="repo in repos" :key="repo.id" md4>
               <GitHubRepo :repo="repo" />
             </v-flex>
           </v-layout>
         </v-tab-item>
       </v-tabs-items>
   </v-container>
 </div>
</template>

<script>
import SearchBar from './SearchBar.vue'
import GitHubRepo from './GithubRepo.vue'
import githubClient from '../githubClient'
import { mapMutations, mapGetters, mapActions } from 'vuex'

export default {
 name: 'Home',
 components: { SearchBar, GitHubRepo },
 data() {
   return {
     tabs: 0
   }
 },
 computed: mapGetters(['allKudos', 'repos']),
 created() {
   this.getKudos();
 },
 methods: {
   githubQuery(query) {
     this.tabs = 1;
     githubClient
       .getJSONRepos(query)
       .then(response => this.resetRepos(response.items) )
   },
   ...mapMutations(['resetRepos']),
   ...mapActions(['getKudos']),
 },
}
</script>

<style>
.v-tabs__content {
  padding-bottom: 2px;
}
</style> 
```

`SearchBar`是`Home`中渲染的第一个组件。当用户在`Sidebar`的文本输入中输入查询时，组件触发对 Github API 的调用。`SearchBar`只是向它的父节点`Home`发出一个事件，它包含了`githubQuery`。

`./kudo-oos/pkg/http/web/app/src/components/SearchBar.vue`应该是这样的:

```
<template>
   <v-toolbar dark color="teal">
     <v-spacer></v-spacer>
     <v-text-field
       solo-inverted
       flat
       hide-details
       label="Search for your OOS project on Github + Press Enter"
       prepend-inner-icon="search"
       v-model="query"
       @keyup.enter="onSearchSubmition"
     ></v-text-field>
     <v-spacer></v-spacer>
     <button @click.prevent="logout">Logout</button>
   </v-toolbar>
</template>

<script>
export default {
   data() {
     return {
       query: null,
     };
   },
   props: ['defaultQuery'],
   methods: {
     onSearchSubmition() {
       this.$emit('search-submitted', this.query);
     },
     async logout () {
       await this.$auth.logout()
       this.$router.push('/')
   }
 }
}
</script> 
```

多亏了`@keyup.enter="onSearchSubmition"`，每当用户点击 enter `onSearchSubmition`时，就会发出带有查询值的`search-submitted`。你可能会问，我们如何捕捉这一事件？简单！在 Home 组件上，当您安装了`Sidebar`组件时，您还添加了一个“监听器”`v-on:search-submitted="githubQuery"`，它在每个`search-submitted`事件上调用`githubQuery`。

`Sidebar`还负责用户的注销。Okta Vue SDK 提供了一种使用方法`this.$auth.logout()`清理会话的简便方法。每当用户注销时，他们都可以被重定向到登录页面。

在`Home`中呈现的第二个组件是`GithupRepo`。该组件在两个选项卡中使用:第一个选项卡`Kudos`代表用户喜爱的 OSS 项目，而`Search`选项卡呈现从 GitHub 返回的 OSS 项目。

```
<template>
 <v-card >
   <v-card-title primary-title>
     <div class="repo-card-content">
       <h3 class="headline mb-0">
         <router-link :to="{ name: 'repo-details', params: { id: repo.id }}" >{{repo.full_name}}</router-link>
       </h3>
       <div>{{repo.description}}</div>
     </div>
   </v-card-title>
   <v-card-actions>
     <v-chip>
       {{repo.language}}
     </v-chip>
     <v-spacer></v-spacer>
     <v-btn @click.prevent="toggleKudo(repo)"  flat icon color="pink">
       <v-icon v-if="isKudo(repo)">favorite</v-icon>
       <v-icon v-else>favorite_border</v-icon>
     </v-btn>
   </v-card-actions>
 </v-card>
</template>

<script>
import { mapActions } from 'vuex';

export default {
 data() {
   return {}
 },
 props: ['repo'],
 methods: {
   isKudo(repo) {
     return this.$store.getters.isKudo(repo);
   },
   ...mapActions(['toggleKudo'])
 }
}
</script>

<style>
.repo-card-content {
  height: 90px;
  overflow: scroll;
}
</style> 
```

您的 SPA 使用`vuex`在一个所有组件都可以访问的集中存储中管理状态。`Vuex`还确保按照一些规则以可预测的方式访问商店。要读取状态，需要定义`getters`，状态的同步变化必须通过`mutations`完成，异步变化通过`actions`完成。

要安装 vuex，请运行:

```
yarn add vuex 
```

你现在需要用`actions`、`mutations`和`getters`创建`./kudo-oos/pkg/http/web/app/src/store.js`。你的初始数据是`{ kudos: {}, repos: [] }`。`kudos`将所有用户喜爱的 OSS 项目保存为一个 JavaScript 对象，其中键是项目 id，值是项目本身。`repos`是保存搜索结果的数组。

在两种情况下，您可能需要改变状态。首先，当用户登录时，您需要从 Go 服务器获取用户的收藏夹 OSS 项目，并通过调用`resetRepos`在商店中设置`repos`。第二，当用户喜欢或不喜欢一个 OSS 项目时，您需要通过调用`resetKudos`来更新商店中的`kudos`,以反映服务器上的变化。

`resetKudos`是同步方法，在每次调用 Go 服务器后，由异步函数内部的`actions`调用。

`Home`组件使用`getters` `allKudos`和`repos`来呈现 Kudos 和 SearchResults 列表。为了知道一个`repo`是否被收藏，你的应用程序需要调用`isKudo` getter。

用下面的代码创建您的`./kudo-oos/pkg/http/web/app/src/store.js`:

```
import Vue from 'vue';
import Vuex from 'vuex';

import APIClient from './apiClient';

Vue.use(Vuex);

const store = new Vuex.Store({
 state: {
   kudos: {},
   repos: [],
 },
 mutations: {
   resetRepos (state, repos) {
     state.repos = repos;
   },
   resetKudos(state, kudos) {
     state.kudos = kudos;
   }
 },
 getters: {
   allKudos(state) {
     return Object.values(state.kudos);
   },
   kudos(state) {
     return state.kudos;
   },
   repos(state) {
     return state.repos;
   },
   isKudo(state) {
     return (repo)=> {
       return !!state.kudos[repo.id];
     };
   }
 },
 actions: {
   getKudos ({commit}) {
     APIClient.getKudos().then((data) => {
       commit('resetKudos', data.reduce((acc, kudo) => {
                              return {[kudo.id]: kudo, ...acc}
                            }, {}))
     })
   },
   updateKudo({ commit, state }, repo) {
     const kudos = { ...state.kudos, [repo.id]: repo };

     return APIClient
       .updateKudo(repo)
       .then(() => {
         commit('resetKudos', kudos)
       });
   },
   toggleKudo({ commit, state }, repo) {
     if (!state.kudos[repo.id]) {
       return APIClient
         .createKudo(repo)
         .then(kudo => commit('resetKudos', { [kudo.id]: kudo, ...state.kudos }))
     }

     const kudos = Object.entries(state.kudos).reduce((acc, [repoId, kudo]) => {
                     return (repoId == repo.id) ? acc
                                                : { [repoId]: kudo, ...acc };
                   }, {});

     return APIClient
       .deleteKudo(repo)
       .then(() => commit('resetKudos', kudos));
   }
 }
});

export default store; 
```

在`actions`内部，您正在执行对 Go 服务器的 ajax 调用。向服务器发出的每个请求都必须经过身份验证，否则服务器会以客户端错误做出响应。当用户登录时，创建一个访问令牌，可以通过调用:`await Vue.prototype.$auth.getAccessToken()`来访问它。这个异步函数返回向服务器发送经过身份验证的请求所需的访问令牌。

Go 服务器为`kudo`资源公开了一个 REST API。您将实现调用 ajax 的方法，以便用`createKudo`创建，用`updateKudo`更新，用`deleteKudo`删除，用`getKudos`列出所有的荣誉。注意，这些方法通过传递端点和 HTTP 动词来调用`perform`方法。`perform`依次用访问令牌填充请求`Authorization`头，这样 Go 服务器就可以验证请求。

用下面的代码创建您的`./kudo-oos/pkg/http/web/app/src/apiClient.js`。

```
import Vue from 'vue';
import axios from 'axios';

const BASE_URI = 'http://localhost:4444';

const client = axios.create({
  baseURL: BASE_URI,
  json: true
});

const APIClient =  {
  createKudo(repo) {
    return this.perform('post', '/kudos', repo);
  },

  deleteKudo(repo) {
    return this.perform('delete', `/kudos/${repo.id}`);
  },

  updateKudo(repo) {
    return this.perform('put', `/kudos/${repo.id}`, repo);
  },

  getKudos() {
    return this.perform('get', '/kudos');
  },

  getKudo(repo) {
    return this.perform('get', `/kudo/${repo.id}`);
  },

  async perform (method, resource, data) {
    let accessToken = await Vue.prototype.$auth.getAccessToken()
    return client({
      method,
      url: resource,
      data,
      headers: {
        Authorization: `Bearer ${accessToken}`
      }
    }).then(req => {
      return req.data
    })
  }
}

export default APIClient; 
```

每个`GithubRepo`都有一个`router-link`到`/repo/:id`来呈现`GithubRepoDetails`组件。`GithubRepoDetails`显示了 OSS 项目的细节，比如这个项目被标了多少次星，以及未解决问题的数量。用户也可以通过点击 Kudo 按钮来留下注释，描述为什么这个项目是特殊的。通过调用`updateKudo`将消息发送到 Go 服务器按钮。

用下面的代码创建您的`./kudo-oos/pkg/http/web/app/src/components/GithubRepoDetails.js`。

```
<template>
  <v-container grid-list-md fluid class="grey lighten-4" >
    <v-layout align-center justify-space-around wrap>
      <v-flex md6>
        <h1 class="primary--text">
          <a :href="repo.html_url">{{repo.full_name}}</a>
        </h1>

        <v-chip class="text-xs-center">
          <v-avatar class="teal">
            <v-icon class="white--text">star</v-icon>
          </v-avatar>
          Stars: {{repo.stargazers_count}}
        </v-chip>

        <v-chip class="text-xs-center">
          <v-avatar class="teal white--text">L</v-avatar>
          Language: {{repo.language}}
        </v-chip>

        <v-chip class="text-xs-center">
          <v-avatar class="teal white--text">O</v-avatar>
          Open Issues: {{repo.open_issues_count}}
        </v-chip>

        <v-textarea
          name="input-7-1"
          label="Show some love"
          value=""
          v-model="repo.notes"
          hint="Describe why you love this project"
        ></v-textarea>
        <v-btn @click.prevent="updateKudo(repo)"> Kudo </v-btn>
        <router-link tag="a" to="/me">Back</router-link>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import { mapActions, mapGetters } from 'vuex';
import githubClient from '../githubClient';

export default {
  data() {
    return {
      repo: {}
    }
  },
  watch: {
    '$route': 'fetchData'
  },
  computed: mapGetters(['kudos']),
  created() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      githubClient
        .getJSONRepo(this.$route.params.id)
        .then((response) => {
          this.repo = Object.assign(response, this.kudos[this.$route.params.id])
        })
    },
    ...mapActions(['updateKudo'])
  }
}
</script> 
```

现在，您的路由器、商店和组件已经就绪，继续修改`./kudo-oos/pkg/http/web/app/src/main.js`以正确初始化您的 SPA。

```
import '@babel/polyfill'
import Vue from 'vue'
import './plugins/vuetify'
import App from './App.vue'
import store from './store'
import router from './routes'

Vue.config.productionTip = process.env.NODE_ENV == 'production';

router.beforeEach(Vue.prototype.$auth.authRedirectGuard())

new Vue({
 store,
 router,
 render: h => h(App)
}).$mount('#app') 
```

请注意，我们正在调用`router.beforeEach(Vue.prototype.$auth.authRedirectGuard())`来查找标记有`meta: {requiresAuth: true}`的路由，如果用户没有登录，则将用户重定向到身份验证流。

## 用 Go 创建 REST API

既然用户可以在前端安全地进行身份验证，那么您需要创建一个用 Go 编写的 HTTP 服务器来处理请求，验证用户是否经过身份验证，并执行 CRUD 操作。

我喜欢使用 [dep 工具](https://github.com/golang/dep)来管理依赖关系，所以在继续之前，一定要从这里安装它[。](https://github.com/golang/dep#installation)

```
dep init
dep ensure -add github.com/okta/okta-jwt-verifier-golang
dep ensure -add github.com/rs/cors
dep ensure -add github.com/globalsign/mgo 
```

现在您需要一个结构来表示 GitHub 存储库。从创建`./kudo-oos/pkg/core/kudo.go`开始，定义下面的结构来表示一个“kudo”(某人对一个特定的回购协议表示称赞)。

```
package core

// Kudo represents a oos kudo.
type Kudo struct {
  UserID      string `json:"user_id" bson:"userId"`
  RepoID      string `json:"id" bson:"repoId"`
  RepoName    string `json:"full_name" bson:"repoName"`
  RepoURL     string `json:"html_url" bson:"repoUrl"`
  Language    string `json:"language" bson:"language"`
  Description string `json:"description" bson:"description"`
  Notes       string `json:"notes" bson:"notes"`
} 
```

接下来，创建`./kudo-oos/pkg/core/repository.go`文件并添加以下接口来表示您可能想要使用的任何持久层的 API。在本文中，我们将使用 MongoDB。

```
package core
// Repository defines the API a repository implementation should follow.
type Repository interface {
  Find(id string) (*Kudo, error)
  FindAll(selector map[string]interface{}) ([]*Kudo, error)
  Delete(kudo *Kudo) error
  Update(kudo *Kudo) error
  Create(kudo ...*Kudo) error
  Count() (int, error)
} 
```

最后，创建实现您刚刚创建的接口的 MongoDB 存储库。创建`./kudo-oos/pkg/storage/mongo.go`并添加以下代码。

```
package storage

import (
  "log"
  "os"

  "github.com/globalsign/mgo"
  "github.com/globalsign/mgo/bson"
  "github.com/{YOUR_GITHUB_USERNAME}/kudo-oos/pkg/core"
)

const (
  collectionName = "kudos"
)

func GetCollectionName() string {
  return collectionName
}

type MongoRepository struct {
  logger  *log.Logger
  session *mgo.Session
}

// Find fetches a kudo from mongo according to the query criteria provided.
func (r MongoRepository) Find(repoID string) (*core.Kudo, error) {
  session := r.session.Copy()
  defer session.Close()
  coll := session.DB("").C(collectionName)

  var kudo core.Kudo
  err := coll.Find(bson.M{"repoId": repoID, "userId": kudo.UserID}).One(&kudo)
  if err != nil {
    r.logger.Printf("error: %v\n", err)
    return nil, err
  }
  return &kudo, nil
}

// FindAll fetches kudos from the database.
func (r MongoRepository) FindAll(selector map[string]interface{}) ([]*core.Kudo, error) {
  session := r.session.Copy()
  defer session.Close()
  coll := session.DB("").C(collectionName)

  var kudos []*core.Kudo
  err := coll.Find(selector).All(&kudos)
  if err != nil {
    r.logger.Printf("error: %v\n", err)
    return nil, err
  }
  return kudos, nil
}

// Delete deletes a kudo from mongo according to the query criteria provided.
func (r MongoRepository) Delete(kudo *core.Kudo) error {
  session := r.session.Copy()
  defer session.Close()
  coll := session.DB("").C(collectionName)

  return coll.Remove(bson.M{"repoId": kudo.RepoID, "userId": kudo.UserID})
}

// Update updates an kudo.
func (r MongoRepository) Update(kudo *core.Kudo) error {
  session := r.session.Copy()
  defer session.Close()
  coll := session.DB("").C(collectionName)

  return coll.Update(bson.M{"repoId": kudo.RepoID, "userId": kudo.UserID}, kudo)
}

// Create kudos in the database.
func (r MongoRepository) Create(kudos ...*core.Kudo) error {
  session := r.session.Copy()
  defer session.Close()
  coll := session.DB("").C(collectionName)

  for _, kudo := range kudos {
    _, err := coll.Upsert(bson.M{"repoId": kudo.RepoID, "userId": kudo.UserID}, kudo)
    if err != nil {
      return err
    }
  }

  return nil
}

// Count counts documents for a given collection
func (r MongoRepository) Count() (int, error) {
  session := r.session.Copy()
  defer session.Close()
  coll := session.DB("").C(collectionName)
  return coll.Count()
}

// NewMongoSession dials mongodb and creates a session.
func newMongoSession() (*mgo.Session, error) {
  mongoURL := os.Getenv("MONGO_URL")
  if mongoURL == "" {
    log.Fatal("MONGO_URL not provided")
  }
  return mgo.Dial(mongoURL)
}

func newMongoRepositoryLogger() *log.Logger {
  return log.New(os.Stdout, "[mongoDB] ", 0)
}

func NewMongoRepository() core.Repository {
  logger := newMongoRepositoryLogger()
  session, err := newMongoSession()
  if err != nil {
    logger.Fatalf("Could not connect to the database: %v\n", err)
  }

  return MongoRepository{
    session: session,
    logger:  logger,
  }
} 
```

## 添加 Go 后端

在创建 HTTP 处理程序之前，您需要编写代码来处理传入的请求负载。

创建`./kudo-oos/pkg/kudo/service.go`并插入下面的代码。

```
package kudo

import (
  "strconv"

  "github.com/{YOUR_GITHUB_USERNAME}/kudo-oos/pkg/core"
)

type GitHubRepo struct {
  RepoID      int64  `json:"id"`
  RepoURL     string `json:"html_url"`
  RepoName    string `json:"full_name"`
  Language    string `json:"language"`
  Description string `json:"description"`
  Notes       string `json:"notes"`
}

type Service struct {
  userId string
  repo   core.Repository
}

func (s Service) GetKudos() ([]*core.Kudo, error) {
  return s.repo.FindAll(map[string]interface{}{"userId": s.userId})
}

func (s Service) CreateKudoFor(githubRepo GitHubRepo) (*core.Kudo, error) {
  kudo := s.githubRepoToKudo(githubRepo)
  err := s.repo.Create(kudo)
  if err != nil {
    return nil, err
  }
  return kudo, nil
}

func (s Service) UpdateKudoWith(githubRepo GitHubRepo) (*core.Kudo, error) {
  kudo := s.githubRepoToKudo(githubRepo)
  err := s.repo.Create(kudo)
  if err != nil {
    return nil, err
  }
  return kudo, nil
}

func (s Service) RemoveKudo(githubRepo GitHubRepo) (*core.Kudo, error) {
  kudo := s.githubRepoToKudo(githubRepo)
  err := s.repo.Delete(kudo)
  if err != nil {
    return nil, err
  }
  return kudo, nil
}

func (s Service) githubRepoToKudo(githubRepo GitHubRepo) *core.Kudo {
  return &core.Kudo{
    UserID:      s.userId,
    RepoID:      strconv.Itoa(int(githubRepo.RepoID)),
    RepoName:    githubRepo.RepoName,
    RepoURL:     githubRepo.RepoURL,
    Language:    githubRepo.Language,
    Description: githubRepo.Description,
    Notes:       githubRepo.Notes,
  }
}

func NewService(repo core.Repository, userId string) Service {
  return Service{
    repo:   repo,
    userId: userId,
  }
} 
```

## 定义 Go HTTP 处理程序

REST API 公开了`kudo`资源来支持像 SPA 这样的客户端。普通的 SPA 将公开端点，因此客户端可以创建、更新、删除和列出资源。例如，当用户登录时，通过`GET /kudos`请求获取认证用户的所有 kudos。

```
 # Fetches all open source projects favorited by the user
GET /kudos
# Fetches a favorited open source project by id
GET /kudos/:id
# Creates (or favorites)  a open source project for the logged in user
POST /kudos
# Updates  a favorited open source project
PUT /kudos/:id
# Deletes (or unfavorites) a favorited open source project
DELETE /kudos/:id 
```

为了支持这一点，您需要添加名为`./kudo-oos/pkg/http/handlers.go`的新文件，并使用精彩的[HTTP outer 库](https://github.com/julienschmidt/httprouter)定义您的 HTTP 处理程序。

```
package http

import (
  "encoding/json"
  "io/ioutil"
  "net/http"
  "strconv"

  "github.com/julienschmidt/httprouter"
  "github.com/{YOUR_GITHUB_USERNAME}/kudo-oos/pkg/core"
  "github.com/{YOUR_GITHUB_USERNAME}/kudo-oos/pkg/kudo"
)

type Service struct {
  repo   core.Repository
  Router http.Handler
}

func New(repo core.Repository) Service {
  service := Service{
    repo: repo,
  }

  router := httprouter.New()
  router.GET("/kudos", service.Index)
  router.POST("/kudos", service.Create)
  router.DELETE("/kudos/:id", service.Delete)
  router.PUT("/kudos/:id", service.Update)

  service.Router = UseMiddlewares(router)

  return service
}

func (s Service) Index(w http.ResponseWriter, r *http.Request, params httprouter.Params) {
  service := kudo.NewService(s.repo, r.Context().Value("userId").(string))
  kudos, err := service.GetKudos()

  if err != nil {
    w.WriteHeader(http.StatusInternalServerError)
    return
  }
  w.WriteHeader(http.StatusOK)
  json.NewEncoder(w).Encode(kudos)
}

func (s Service) Create(w http.ResponseWriter, r *http.Request, params httprouter.Params) {
  service := kudo.NewService(s.repo, r.Context().Value("userId").(string))
  payload, _ := ioutil.ReadAll(r.Body)

  githubRepo := kudo.GitHubRepo{}
  json.Unmarshal(payload, &githubRepo)

  kudo, err := service.CreateKudoFor(githubRepo)

  if err != nil {
    w.WriteHeader(http.StatusInternalServerError)
    return
  }
  w.WriteHeader(http.StatusCreated)
  json.NewEncoder(w).Encode(kudo)
}

func (s Service) Delete(w http.ResponseWriter, r *http.Request, params httprouter.Params) {
  service := kudo.NewService(s.repo, r.Context().Value("userId").(string))

  repoID, _ := strconv.Atoi(params.ByName("id"))
  githubRepo := kudo.GitHubRepo{RepoID: int64(repoID)}

  _, err := service.RemoveKudo(githubRepo)
  if err != nil {
    w.WriteHeader(http.StatusInternalServerError)
    return
  }
  w.WriteHeader(http.StatusOK)
}

func (s Service) Update(w http.ResponseWriter, r *http.Request, params httprouter.Params) {
  service := kudo.NewService(s.repo, r.Context().Value("userId").(string))
  payload, _ := ioutil.ReadAll(r.Body)

  githubRepo := kudo.GitHubRepo{}
  json.Unmarshal(payload, &githubRepo)

  kudo, err := service.UpdateKudoWith(githubRepo)
  if err != nil {
    w.WriteHeader(http.StatusInternalServerError)
    return
  }
  w.WriteHeader(http.StatusOK)
  json.NewEncoder(w).Encode(kudo)
} 
```

## 用 Go 验证 JSON Web 令牌(jwt)

这是 REST API 服务器最重要的组件。如果没有这个中间件，任何用户都可以对数据库执行 CRUD 操作。

如果在 HTTP 授权头中没有提供有效的 JWT，API 调用就会中止，并向客户端返回一个错误。

创建`./kudo-oos/pkg/http/middlewares.go`并粘贴以下代码:

```
package http

import (
  "context"
  "log"
  "net/http"
  "strings"

  jwtverifier "github.com/okta/okta-jwt-verifier-golang"
  "github.com/rs/cors"
)

func OktaAuth(h http.Handler) http.Handler {
  return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    accessToken := r.Header["Authorization"]
    jwt, err := validateAccessToken(accessToken)
    if err != nil {
      w.WriteHeader(http.StatusForbidden)
      w.Write([]byte(err.Error()))
      return
    }
    ctx := context.WithValue(r.Context(), "userId", jwt.Claims["sub"].(string))
    h.ServeHTTP(w, r.WithContext(ctx))
  })
}

func validateAccessToken(accessToken []string) (*jwtverifier.Jwt, error) {
  parts := strings.Split(accessToken[0], " ")
  jwtVerifierSetup := jwtverifier.JwtVerifier{
    Issuer:           "{DOMAIN}",
    ClaimsToValidate: map[string]string{"aud": "api://default", "cid": "{CLIENT_ID}"},
  }
  verifier := jwtVerifierSetup.New()
  return verifier.VerifyIdToken(parts[1])
}

func JSONApi(h http.Handler) http.Handler {
  return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    h.ServeHTTP(w, r)
  })
}

func AccsessLog(h http.Handler) http.Handler {
  return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    log.Printf("%s: %s", r.Method, r.RequestURI)
    h.ServeHTTP(w, r)
  })
}

func Cors(h http.Handler) http.Handler {
  corsConfig := cors.New(cors.Options{
    AllowedHeaders: []string{"Origin", "Accept", "Content-Type", "X-Requested-With", "Authorization"},
    AllowedMethods: []string{"POST", "PUT", "GET", "PATCH", "OPTIONS", "HEAD", "DELETE"},
    Debug:          true,
  })
  return corsConfig.Handler(h)
}

func UseMiddlewares(h http.Handler) http.Handler {
  h = JSONApi(h)
  h = OktaAuth(h)
  h = Cors(h)
  return AccsessLog(h)
} 
```

可以看到，中间件`OktaAuth`使用 [okta-jwt-verifier-golang](https://github.com/okta/okta-jwt-verifier-golang) 来验证用户的访问令牌。

## 定义您的 Go REST API 入口点

打开`./kudo-oos/pkg/cmd/main.go`并添加下面的代码来启动你的 Go web 服务器。

```
package main

import (
  "log"
  "net/http"
  "os"

  web "github.com/{YOUR_GITHUB_USERNAME}/kudo-oos/pkg/http"
  "github.com/{YOUR_GITHUB_USERNAME}/kudo-oos/pkg/storage"
)

func main() {
  httpPort := os.Getenv("PORT")

  repo := storage.NewMongoRepository()
  webService := web.New(repo)

  log.Printf("Running on port %s\n", httpPort)
  log.Fatal(http.ListenAndServe(httpPort, webService.Router))
} 
```

## 跑 Go +Vue SPA

有许多方法可以运行后端和前端应用程序。最简单的方法(出于开发目的)是只使用老式的 Make。

Makefile 包含网站的构建说明。这就像是一个老派版本的`gulp`、`grunt`，以及更多的 hip Node 工具。首先，在项目文件夹的根目录下创建一个名为`Makefile`的文件，并复制以下代码。

```
setup: run_services
    @go run ./cmd/db/setup.go

run_services:
    @docker-compose up --build -d

run_server:
    @MONGO_URL=mongodb://mongo_user:mongo_secret@0.0.0.0:27017/kudos PORT=:4444 go run cmd/main.go

run_client:
    @/bin/bash -c "cd $$GOPATH/src/github.com/klebervirgilio/kudo-oos/pkg/http/web/app && yarn serve" 
```

### 创建 Dockerfile 文件

接下来，您需要创建一个 Dockerfile 文件。这个文件告诉 Docker 如何运行您的应用程序，让您不必为了测试而部署一个真正的 MongoDB 实例。

你需要做的就是创建一个名为`docker-compose.yml`的文件，并复制下面的代码。

```
version: '3'
services:
  mongo:
    image: mongo
    restart: always
    ports:
     - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: mongo_user
      MONGO_INITDB_ROOT_PASSWORD: mongo_secret 
```

您的应用程序现在可以测试了！运行以下命令开始。

```
make setup
make run_server
make run_client 
```

您的 Go 服务器应该正在监听`0.0.0.0:4444`，您的 SPA 应该正在提供来自`http://localhost:8080`的文件。访问`http://localhost:8080`体验你的新应用！

## 了解有关 Go 和 Vue 的更多信息

Vue.js 是一个强大而简单的框架，拥有惊人的采用率和社区增长。在本教程中，您学习了使用 Vue 和 Go 构建一个全功能、安全的 SPA。
要了解更多关于 Vue.js 的信息，请前往[https://vuejs.org](https://vuejs.org)或查看来自 [@oktadev 团队](https://twitter.com/oktadev)的其他优秀资源:

*   [渐进式网络应用的终极指南](https://developer.okta.com/blog/2017/07/20/the-ultimate-guide-to-progressive-web-applications)
*   [懒惰开发者的 Vue.js 认证指南](https://developer.okta.com/blog/2017/09/14/lazy-developers-guide-to-auth-with-vue)
*   [用 Vue.js 建立一个加密货币比较网站](https://developer.okta.com/blog/2017/09/06/build-a-cryptocurrency-comparison-site-with-vuejs)

如果您有任何问题，请在评论中告诉我们，或者关注并发推特给我们 [@oktadev](https://twitter.com/oktadev) 。

## 分享这篇文章