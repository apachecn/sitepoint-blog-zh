# 使用 Vue.js 和 Axios 从第三方 API 获取数据

> 原文：<https://www.sitepoint.com/fetching-data-third-party-api-vue-axios/>

在构建您的 JavaScript 应用程序时，您通常会希望从远程数据源获取数据或使用一个 [API](https://en.wikipedia.org/wiki/Application_programming_interface) 。有很多很酷的东西可以用来自一系列[公开可用的 API](https://github.com/toddmotto/public-apis)的数据来完成。

有了 [Vue.js](https://vuejs.org/) ，你可以围绕这些服务中的一个构建一个应用，并在几分钟内开始向用户提供内容。

我将演示如何构建一个简单的新闻应用程序，它将显示当天的热门新闻文章，并允许用户根据他们的兴趣类别进行过滤，从纽约时报 API 获取数据。你可以在这里找到本教程[的完整代码，在这里](https://github.com/sitepoint-editors/vue-news-app)可以找到已完成应用[的现场演示。](https://vue-news-app.stackblitz.io/)

这是最终的应用程序的样子:

![vue-news-final-app](img/2920a672e63fc682c37f6675f945a240.png)

按照本教程，您需要在您的机器上安装 Node.js 和(可选的) [Yarn](https://yarnpkg.com/) 。要安装 Node，你可以去[官方下载页面](https://nodejs.org/en/download/)获取你系统的 Node 二进制文件，或者使用[版本管理器](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)来代替。

安装节点后，要拉入纱线，运行:

```
npm i -g yarn 
```

你还需要 Vue.js 的基础知识。你可以在这里找到一个很好的入门指南。

## 获取 API 密钥

要使用纽约时报的 API，您需要获得一个 API 密钥。所以，如果你还没有，去他们的[注册页面](https://developer.nytimes.com/signup)注册，获得[头条新闻 API](https://developer.nytimes.com/top_stories_v2.json) 的 API 密匙。

![01-nyt-register-api-key](img/048789c9bc37dea460286a38cb996bb2.png)

我们将使用 [top stories](https://developer.nytimes.com/docs/top-stories-product/1/overview) API 端点来获取数据。请注意，这个 API 提供了多个部分，如“家庭”、“旅行”、“艺术”和“科学”。我们需要建立一个过滤器，允许用户选择一个部分，并在其中加载故事。

以下是呼叫示例:

```
https://api.nytimes.com/svc/topstories/v2/arts.json?api-key=yourkey
https://api.nytimes.com/svc/topstories/v2/home.json?api-key=yourkey
https://api.nytimes.com/svc/topstories/v2/science.json?api-key=yourkey
https://api.nytimes.com/svc/topstories/v2/us.json?api-key=yourkey
https://api.nytimes.com/svc/topstories/v2/world.json?api-key=yourkey 
```

随意使用您最喜欢的 REST 客户端(例如 [Hoppscotch](https://hoppscotch.io/) 或[失眠](https://insomnia.rest/))来测试您的 API 调用。

![insomnia rest client](img/25aed65c86381746673e853abc8e422a.png)

## 项目结构

让我们使用 [Vite](https://vitejs.dev/guide/) 快速启动一个 Vue 3 项目，这是一个比 [Vue CLI](https://cli.vuejs.org/) 运行速度更快的开发服务器:

```
yarn create @vitejs/app vue-news-app --template vue

# Install package dependencies
cd vue-news-app
yarn install

# Confirm app can run
yarn dev 
```

在浏览器中打开`localhost:3000`。您应该有以下视图:

![create vue app](img/3f8f1e3e351090d4bcc0af4f0f85f1fd.png)

接下来，让我们安装 [TailwindCSS](https://tailwindcss.com/) 框架来提供一些基本的样式。要执行此操作，您需要停止服务器:

```
yarn add -D tailwindcss@latest postcss@latest autoprefixer@latest

# Generate tailwind.config.js and postcss.config.js files
npx tailwindcss init -p 
```

我们将需要一些额外的软件包工具来帮助我们格式化日期( [date-fns](https://www.sitepoint.com/date-fns-javascript-date-library/) )并限制`abstract`字段的行数(tailwind 的 [line-clamp](https://tailwindcss.com/blog/multi-line-truncation-with-tailwindcss-line-clamp) ):

```
yarn add @tailwindcss/line-clamp date-fns 
```

`@tailwindcss/line-clamp`是需要包含在`tailwind.config.js`中的插件。以下是完整的配置:

```
module.exports = {
  purge: ["./index.html", "./src/**/*.{vue,js,ts,jsx,tsx}"],
  darkMode: false, // or 'media' or 'class'
  theme: {
    extend: {},
  },
  variants: {
    extend: {},
  },
  plugins: [require("@tailwindcss/line-clamp")],
} 
```

接下来，在`src`文件夹中创建一个`index.css`文件，并添加以下代码:

```
@tailwind base;
@tailwind components;
@tailwind utilities;

body {
  @apply antialiased text-green-900 bg-green-50;
  font-family: "Gill Sans", "Gill Sans MT", Calibri, "Trebuchet MS", sans-serif;
}

#app {
  @apply flex flex-col min-h-screen overflow-x-hidden;
} 
```

除了导入所需的 Tailwind CSS 类之外，我们还添加了一些 CSS 设置来帮助我们定义应用程序的默认主题。我们还实现了一个 flex 布局系统，帮助我们为应用程序创建一个粘性的页眉和页脚。

我们需要在`src/main.js`中导入`index.css`:

```
import { createApp } from "vue"
import App from "./App.vue"
import "./index.css"

createApp(App).mount("#app") 
```

现在让我们继续定义我们的应用程序布局。首先，清除`src/components`中的任何现有组件。接下来，在同一个文件夹中，创建这三个文件:

*   `Layout.vue`
*   `Header.vue`
*   `Footer.vue`

为每个文件复制以下代码:

**src/components/footer . vue**:

```
<template>
  <footer
    class="px-4 py-8 text-sm font-bold text-center text-green-100 bg-green-900">
    <p class="text-sm tracking-wide">Copyright (c) 2021 SitePoint</p>
  </footer>
</template> 
```

**src/components/header . vue**:

```
<template>
  <header class="flex justify-center py-6 bg-green-900 place-items-center">
    <img alt="Vue logo" src="../assets/logo.png" width="32" />
    <span class="ml-4 text-lg font-bold text-green-100 md:text-xl">
      Vue News | NYTimes Edition
    </span>
  </header>
</template> 
```

**src/components/layout . vue**:

```
<template>
  <Header />
  <main class="container flex-grow px-4 mx-auto my-12">
    <slot />
  </main>
  <Footer />
</template>

<script>
import Header from "./Header.vue"
import Footer from "./Footer.vue"

export default {
  components: {
    Header,
    Footer,
  },
}
</script> 
```

最后，更新`src/App.vue`:

```
<template>
  <Layout>
    <p>Main content goes here</p>
  </Layout>
</template>

<script>
import Layout from "./components/Layout.vue"

export default {
  components: {
    Layout,
  },
}
</script> 
```

执行`yarn dev`。浏览器应该会自动刷新。

![vue layout](img/d0f4f95e410d97acb992c94d67255323.png)

应用程序布局完成后，我们现在可以开始构建新闻应用程序的核心逻辑了。

## 构建新闻应用组件

我们的应用程序结构将由三个新闻组件和一个容器组成，`src/App.vue`。容器将负责获取 post 数据并填充组件。

首先，我们需要设计布局并寻找这些组件。因此，我们需要一些模拟数据来开始。创建文件`src/posts.json`并用以下数据填充它:

```
{
  "posts": [
    {
      "title": "Stay Healthy When Exercising Outdoors",
      "abstract": "Cold weather workouts do bring unique risks, but a little planning and preparation can help whether you’re going for a winter walk, trekking in snowshoes or sledding with the kids.",
      "url": "https://www.nytimes.com/2021/02/06/at-home/exercise-outdoors-cold-weather.html",
      "byline": "By Kelly DiNardo",
      "published_date": "2021-02-06T23:40:05-05:00",
      "thumbnail": "https://static01.nyt.cimg/2021/02/07/multimedia/07ah-OUTDOOREXERCISE/07ah-OUTDOOREXERCISE-mediumThreeByTwo210.jpg",
      "caption": ""
    },
    {
      "title": "4 Skiers Killed in Avalanche in Utah, Officials Say",
      "abstract": "It was the third such deadly episode in days and the deadliest avalanche in the United States since May 2014, according to the authorities.",
      "url": "https://www.nytimes.com/2021/02/06/us/avalanche-salt-lake-city.html",
      "byline": "By Michael Levenson",
      "published_date": "2021-02-06T20:22:39-05:00",
      "thumbnail": "https://static01.nyt.cimg/2021/02/06/lens/06xp-avalanche-photo2/06xp-avalanche-photo2-mediumThreeByTwo210.jpg",
      "caption": "A helicopter returning to Millcreek Canyon after rescuing one of the four avalanche survivors on Saturday."
    }
  ]
} 
```

我鼓励您复制记录，以便更好地测试我们的组件设计布局，但由于篇幅限制，我不会在这里这样做。

现在让我们开始构建我们的新闻组件。在`src/components`文件夹中，创建以下文件:

*   `NewsCard.vue`
*   `NewsList.vue`
*   `NewsFilter.vue`

为了直观显示所有这些组件是如何组合在一起的，将它们导入到`src/App.vue`中，并将其设置如下:

```
<template>
  <Layout>
    <h2 class="mb-8 text-4xl font-bold text-center capitalize">
      News Section : <span class="text-green-700">{{ section }}</span>
    </h2>
    <NewsFilter v-model="section" />
    <NewsList :posts="posts" />
  </Layout>
</template>

<script>
import Layout from "./components/Layout.vue"
import NewsFilter from "./components/NewsFilter.vue"
import NewsList from "./components/NewsList.vue"

import data from "./posts.json"

export default {
  components: {
    Layout,
    NewsFilter,
    NewsList,
  },
  data() {
    return {
      section: "home",
      posts: data.posts,
    }
  },
}
</script> 
```

我们来分解一下上面的代码:

*   标签是我们显示当前状态值的地方。
*   `NewsFilter`组件将包含一个下拉输入，供用户选择不同的部分。他们需要点击一个按钮来执行获取。我们已经将组件绑定到状态`section`以允许状态同步。
*   `NewsList`组件将使用`NewsCard`组件在一个响应网格中显示帖子。

现在让我们开始处理每个单独的新闻组件。`NewsCard.vue`组件将显示单个帖子的数据。它需要一个道具，`post`:

```
<template>
  <section class="p-4 rounded-lg shadow-lg bg-gray-50 w-80">
    <div class="h-96">
      <a
        class="text-xl font-bold text-center text-green-800 hover:text-green-600 hover:underline"
        :href="post.url"
        target="_blank"
        rel="noreferrer"
      >
        {{ post.title }}
      </a>
      <img
        class="w-full mt-2 rounded"
        :src="post.thumbnail"
        :alt="post.caption"
        height="140"
        width="210"
      />
      <p class="mt-2 text-justify text-gray-700 line-clamp-4">
        {{ post.abstract }}
      </p>
    </div>
    <div>
      <p class="mt-4 font-bold text-gray-600">{{ post.byline }}</p>
      <p class="font-light text-gray-600">
        {{ formatDate(post.published_date) }}
      </p>
    </div>
  </section>
</template>

<script>
import { format } from "date-fns"

export default {
  props: {
    post: {
      type: Object,
      required: true,
    },
  },
  methods: {
    formatDate(strDate) {
      return format(new Date(strDate), "MMMM do, yyyy")
    },
  },
}
</script> 
```

`NewsList.vue`将遍历一个 posts 数组，并在一个响应网格中填充`NewsCards`:

```
<template>
  <div
    class="grid grid-cols-1 gap-6 mt-4 md:grid-cols-2 lg:grid-cols-3 2xl:grid-cols-4 justify-items-center"
  >
    <NewsCard v-for="(post, index) in posts" :key="index" :post="post" />
  </div>
</template>

<script>
import NewsCard from "./NewsCard.vue"
export default {
  props: {
    posts: {
      type: Array,
      required: true,
    },
  },
  components: {
    NewsCard,
  },
}
</script> 
```

接下来，我们有了`NewsFilter`组件，它将允许用户从不同的部分加载帖子。首先，我们需要一个内容文件来存储 Top Stories API 端点支持的所有部分。创建文件`src/components/sections.js`:

```
const sections = [
  "home",
  "arts",
  "automobiles",
  "books",
  "business",
  "fashion",
  "food",
  "health",
  "insider",
  "magazine",
  "movies",
  "nyregion",
  "obituaries",
  "opinion",
  "politics",
  "realestate",
  "science",
  "sports",
  "sundayreview",
  "technology",
  "theater",
  "magazine",
  "travel",
  "upshot",
  "us",
  "world",
]

export default sections 
```

现在让我们创建我们的`NewsFilter.vue`，它包含一个下拉选择输入和一个按钮。我们需要使用`v-model`绑定状态`section`，使其与`App.vue`中的状态同步:

```
<template>
  <div class="flex justify-center p-4 rounded">
    <!-- Start of select dropdown -->
    <div class="relative inline-flex">
      <svg
        class="absolute top-0 right-0 w-2 h-2 m-4 pointer-events-none"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 412 232"
      >
        <path
          d="M206 171.144L42.678 7.822c-9.763-9.763-25.592-9.763-35.355 0-9.763 9.764-9.763 25.592 0 35.355l181 181c4.88 4.882 11.279 7.323 17.677 7.323s12.796-2.441 17.678-7.322l181-181c9.763-9.764 9.763-25.592 0-35.355-9.763-9.763-25.592-9.763-35.355 0L206 171.144z"
          fill="#648299"
          fill-rule="nonzero"
        />
      </svg>
      <select
        class="h-10 pl-5 pr-10 text-gray-600 bg-white border border-gray-300 rounded-lg appearance-none hover:border-gray-400 focus:outline-none"
        v-model="section"
      >
        <option
          v-for="(section, index) in sections"
          :key="index"
          :value="section"
        >
          {{ capitalize(section) }}
        </option>
      </select>
    </div>
    <!-- End of select dropdown -->
    <div class="self-center ml-8">
      <button
        class="px-6 py-2 text-white bg-green-700 rounded hover:bg-green-900"
      >
        Retrieve
      </button>
    </div>
  </div>
</template>

<script>
import { computed } from "vue"
import sectionsData from "./sections"

export default {
  props: {
    modelValue: String,
  },
  setup(props, { emit }) {
    const section = computed({
      get: () => props.modelValue,
      set: value => emit("update:modelValue", value),
    })

    return {
      section,
    }
  },
  data() {
    return {
      sections: sectionsData,
    }
  },
  methods: {
    capitalize(value) {
      if (!value) return ""
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    },
  },
}
</script> 
```

文章“ [Vue 3:数据下降，事件上升](https://www.vuemastery.com/blog/vue-3-data-down-events-up/)”很好地解释了我们用来将`section`状态绑定到`NewsFilter`组件的策略。基本上，这允许子组件更新 props 并与父组件同步。

下面是应用程序当前状态的屏幕截图:

![news components](img/7ae4ad8151e58de3ae5b779b6bbad835.png)

## 使用 Axios 获取远程数据

Axios 是一个基于 promise 的 HTTP 客户端，用于发出 Ajax 请求，将会很好地满足我们的需求。它提供了一个简单而丰富的 API。它非常类似于`fetch` [API](https://www.sitepoint.com/introduction-to-the-fetch-api/) ，但是不需要为旧浏览器添加聚合填充，以及其他一些[微妙之处](https://github.com/mzabriskie/axios/issues/314)。

要安装 axios，请运行:

```
yarn add axios 
```

我们的应用程序的 UI 开发已经完成。我们现在只需要实现远程获取逻辑。以下是纽约时报 API 服务期望的完整 URL 格式的示例:

```
https://api.nytimes.com/svc/topstories/v2/home.json?api-key=your_api_key 
```

首先，让我们将 API 密钥存储在项目根目录下的一个`.env`文件中。以下列格式保存:

```
VITE_NYT_API_KEY=#### 
```

用实际的 API 键替换散列。

由于我们使用的是 Vite，我们需要遵守 Vite 的[手册](https://vitejs.dev/guide/env-and-mode.html)中关于加载环境变量的内容。Vue/CLI 有自己的[指令](https://cli.vuejs.org/guide/mode-and-env.html#environment-variables)来做同样的事情。

现在让我们实现从 NYTimes REST API 端点获取实际帖子的逻辑。简单地相应更新`src/App.vue`:

```
<template>
  <Layout>
    <h2 class="mb-8 text-4xl font-bold text-center capitalize">
      News Section : <span class="text-green-700">{{ section }}</span>
    </h2>
    <NewsFilter v-model="section" :fetch="fetchNews" />
    <NewsList :posts="posts" />
  </Layout>
</template>

<script>
import Layout from "./components/Layout.vue"
import NewsFilter from "./components/NewsFilter.vue"
import NewsList from "./components/NewsList.vue"

import axios from "axios"
const api = import.meta.env.VITE_NYT_API_KEY

export default {
  components: {
    Layout,
    NewsFilter,
    NewsList,
  },
  data() {
    return {
      section: "home",
      posts: [],
    }
  },
  methods: {
    // Helper function for extracting a nested image object
    extractImage(post) {
      const defaultImg = {
        url: "http://placehold.it/210x140?text=N/A",
        caption: post.title,
      }
      if (!post.multimedia) {
        return defaultImg
      }
      let imgObj = post.multimedia.find(
        media => media.format === "mediumThreeByTwo210"
      )
      return imgObj ? imgObj : defaultImg
    },
    async fetchNews() {
      try {
        const url = `https://api.nytimes.com/svc/topstories/v2/${this.section}.json?api-key=${api}`
        const response = await axios.get(url)
        const results = response.data.results
        this.posts = results.map(post => ({
          title: post.title,
          abstract: post.abstract,
          url: post.url,
          thumbnail: this.extractImage(post).url,
          caption: this.extractImage(post).caption,
          byline: post.byline,
          published_date: post.published_date,
        }))
      } catch (err) {
        if (err.response) {
          // client received an error response (5xx, 4xx)
          console.log("Server Error:", err)
        } else if (err.request) {
          // client never received a response, or request never left
          console.log("Network Error:", err)
        } else {
          console.log("Client Error:", err)
        }
      }
    },
  },
  mounted() {
    this.fetchNews()
  },
}
</script> 
```

这里我们已经创建了一个名为`fetchNews`的函数，它包含了执行获取逻辑的逻辑。这个函数将从两个地方调用:

*   `mounted()`生命周期事件
*   `NewsFilter`组件

让我们将功能分解一下，以确保我们理解发生了什么:

*   我们使用了[异步语法](https://www.sitepoint.com/flow-control-callbacks-promises-async-await/)，因为它比使用常规的`Promise`回调语法更简洁。
*   由于我们要执行网络呼叫，许多事情可能会出错。我们将函数的代码包装在一个`try...catch`块中。否则，用户将会遇到一个非描述性的承诺错误。
*   使用 [ES6 模板文字](https://www.sitepoint.com/es6-template-literals-techniques-and-tools/)，我们能够构建一个 URL 字符串，每当用户通过`NewsFilter`组件更改新闻`section`时，该字符串就会自动更新。请注意，API 键也包含在 URL 字符串中。
*   在使用`axios.get()`函数获取结果之后，我们需要解析结果并以一种与我们的 UI 兼容的方式格式化它们，特别是与`NewsCard`组件兼容。我们使用 JavaScript 的`Array.map`函数来创建一个包含格式化数据的新数组。
*   提取图像数据有点棘手。有些帖子缺少`multimedia`字段，即使他们有，也不能保证我们需要的媒体格式存在。在这种情况下，我们返回一个默认的图片 URL — `http://placehold.it/210x140?text=N/A` —并使用文章的标题作为标题。
*   在错误块中，我们检查特定错误属性的存在，以确定发生了哪种错误。您可以使用这些信息来构建一个有用的错误消息。

现在，看一下模板部分，注意到我们已经包含了一个名为`fetch`的新道具，它链接到了`fetchNews`函数。我们需要更新`src/components/NewsFilter.vue`来接受这个道具。下面，我只强调了您应该更改的代码部分:

```
<template>
  ...
  <button
    class="px-6 py-2 text-white bg-green-700 rounded hover:bg-green-900"
    @click="fetch"
  >
    Retrieve
  </button>
  ...
</template>

<script>
export default {
  props: {
    modelValue: String,
    fetch: Function,
  },
}
</script> 
```

为了正确加载 axios 库和 API 密匙，您可能需要重启 dev 服务器。一旦你这样做了，你应该有一个实际职位的看法。下面是应用程序的当前状态。

![axios fetch](img/4ca7974afc133b305a416adbf6949b6f.png)

您应该能够切换和加载不同的新闻部分。

## 最后的润色和演示

我决定添加一些小的(可选的)改动，让应用程序体验更好一点，比如引入一个加载图像。

您可以在下面的堆栈中看到一个演示(功能有限):

[https://stackblitz.com/edit/vue-news-app?embed=1&file=src/App.vue&hideExplorer=1&theme=dark&view=preview](https://stackblitz.com/edit/vue-news-app?embed=1&file=src/App.vue&hideExplorer=1&theme=dark&view=preview)

或者，您可以在此查看现场版本[。](https://vue-news-app.stackblitz.io/)

## 结论

在本教程中，我们学习了如何从头开始一个 Vue.js 项目，如何使用 [axios](https://github.com/mzabriskie/axios) 从 API 获取数据，以及如何使用组件和计算属性处理响应和操纵数据。

现在，我们有了一个围绕 API 服务构建的功能性 Vue.js 3.0 应用程序。通过插入其他一些 API，可以进行大量的改进。例如，我们可以:

*   使用[缓冲 API](https://buffer.com/developers/api) 自动将社交媒体帖子从一个类别中排队
*   使用 [Pocket API](https://getpocket.com/developer/) 标记稍后要阅读的帖子

…等等。

这个项目的全部代码也是托管在 GitHub 上的[，所以你可以克隆、运行和做任何你想做的改进。](https://github.com/sitepoint-editors/vue-news-app)

## 分享这篇文章