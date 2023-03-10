# 使用 Chart.js 的 Vue.js 包装器创建漂亮的图表

> 原文：<https://www.sitepoint.com/creating-beautiful-charts-vue-chart-js/>

图表是现代网站和应用程序的重要组成部分。它们有助于呈现文本中难以表达的信息。通过以易于阅读和理解的方式呈现数据，图表还有助于理解通常用文本格式没有意义的数据。

在本文中，我将借助 [Chart.js](http://www.chartjs.org/) 和 [Vue.js](https://vuejs.org/) 向您展示如何以各种类型图表的形式表示数据。Chart.js 是一个为开发人员和设计人员提供的简单而灵活的 JavaScript 图表库，允许使用 HTML5 `canvas`元素绘制不同种类的图表。关于 Chart.js 的一个很好的复习可以在这里阅读[。](https://www.sitepoint.com/creating-beautiful-charts-chart-js/)

Vue.js 是一个渐进的 JavaScript 框架，我们将使用它和 Chart.js 一起演示图表示例。如果你是 Vue.js 的新手，在 SitePoint 上有一个关于使用 Vue.js 的很棒的入门。我们还将使用 [Vue CLI](https://cli.vuejs.org/) 为我们将要构建的演示搭建一个 Vue.js 项目。

![A maid positioning objects representing different types of chart](img/3f12b88231bc154eaf44e668e529319b.png)

## 图表，图表，图表

GitHub 上的 [awesome-vue](https://github.com/vuejs/awesome-vue#charts) repo 上有一个很棒的 Vue 包装器集合，用于各种图表库。然而，由于我们专注于 Chart.js，我们将会看到该库的以下包装器:

*   视图图
*   [vista-chartjs](https://github.com/apertureless/vue-chartjs)
*   [vista 牌踢](https://github.com/ankane/vue-chartkick)

我们将使用这些包装器来演示如何创建不同类型的图表，并介绍每种图表提供的独特功能。

请注意，本教程的源代码是 GitHub 上的[。](https://github.com/sitepoint-editors/vue-charts)

## 使用 Vue CLI 搭建项目

让我们从使用以下命令安装 Vue CLI 开始:

```
npm install -g @vue/cli 
```

完成后，我们可以通过键入以下命令开始搭建项目:

```
vue create chart-js-demo 
```

这将打开一个向导，引导您创建新的 Vue 应用程序。按如下方式回答问题:

```
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Linter
? Use history mode for router? Yes
? Pick a linter / formatter config: ESLint with error prevention only
? Pick additional lint features: Lint on save
? Where do you prefer placing config for Babel etc.? In dedicated config files
? Save this as a preset for future projects? No 
```

现在让我们安装 Chart.js，以及我们的应用程序所需的各种包装器:

```
cd chart-js-demo
npm install chart.js chartkick hchs-vue-charts vue-chartjs vue-chartkick 
```

*提示:如果你使用 npm 5，不再需要`--save`标志，因为现在所有的包都被自动保存了。点击这里了解更多关于[的信息。](http://blog.npmjs.org/post/161081169345/v500)*

让我们测试一下目前我们所拥有的，并运行我们的应用程序:

```
npm run serve 
```

如果一切顺利，您应该能够打开 [localhost:8080](http://localhost:8080) 并看到标准的欢迎页面。

### 基本设置

接下来，我们需要为每个包装器添加一个视图。在`src/views`中创建以下文件:

*   `VueChartJS.vue`
*   `VueChartKick.vue`
*   `VueCharts.vue`

```
touch src/views/{VueChartJS.vue,VueChartKick.vue,VueCharts.vue} 
```

然后在`src/components`中创建以下文件:

*   `LineChart.vue`
*   `BarChart.vue`
*   `BubbleChart.vue`
*   `Reactive.vue`

```
touch src/components/{LineChart.vue,BarChart.vue,BubbleChart.vue,Reactive.vue} 
```

这些与我们将要使用的图表类型相对应。

最后，您可以删除`src/views`中的`About.vue`文件、`src/components`中的`HelloWorld.vue`文件以及`src`中的`assets`文件夹。我们不需要这些。

您的`src`目录的内容应该如下所示:

```
.
├── App.vue
├── components
│   ├── BarChart.vue
│   ├── BubbleChart.vue
│   ├── LineChart.vue
│   └── Reactive.vue
├── main.js
├── router.js
└── views
    ├── Home.vue
    ├── VueChartJS.vue
    ├── VueChartKick.vue
    └── VueCharts.vue 
```

### 添加路线

接下来我们要做的是创建不同的路线，在这些路线中我们可以查看我们上面创建的每个包装器的图表。我们希望有一个`/charts`路径来显示用`vue-charts`包装器制作的图表，`/chartjs`显示用`vue-chartjs`包装器制作的图表，最后`/chartkick`显示用`vue-chartkick`包装器制作的图表。我们还将保留`/`路线以显示`Home`视图。

导航到应用程序的`src`文件夹，打开`router.js`文件。用以下内容替换该文件的内容:

```
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/views/Home'
import VueChartJS from '@/views/VueChartJS'
import VueChartKick from '@/views/VueChartKick'
import VueCharts from '@/views/VueCharts'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Home',
      component: Home
    },
    {
      path: '/chartjs',
      name: 'VueChartJS',
      component: VueChartJS
    },
    {
      path: '/chartkick',
      name: 'VueChartKick',
      component: VueChartKick
    },
    {
      path: '/charts',
      name: 'VueCharts',
      component: VueCharts
    }
  ]
}) 
```

在这里，我们导入了上面创建的 Vue 组件。[组件](https://vuejs.org/v2/guide/components.html)是 Vue 最强大的特性之一。它们帮助我们扩展基本的 HTML 元素来封装可重用的代码。在高层次上，组件是 Vue 的编译器附加行为的定制元素。

最后，我们定义了显示不同图表所需的不同页面的路由和组件。

### 添加导航

我们可以在`src/App.vue`中定义跨组件使用的导航。我们还将添加一些基本的样式:

```
<template>
  <div id="app">
    <div id="nav">
      <ul>
        <li><router-link to="/">Home</router-link></li>
        <li><router-link to="/chartjs">vue-chartjs</router-link></li>
        <li><router-link to="/charts">vue-charts</router-link></li>
        <li><router-link to="/chartkick">vue-chartkick</router-link></li>
      </ul>
    </div>
    <router-view />
  </div>
</template>

<style> #app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
  height: 90px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
  text-decoration: underline;
}

#nav a.router-link-exact-active {
  color: #42b983;
  text-decoration: none;
}

#nav ul {
  list-style-type: none;
  padding: 0;
}

#nav ul li {
  display: inline-block;
  margin: 0 10px;
}

h1 {
  font-size: 1.75em;
}

h2 {
  line-height: 2.5em;
  font-size: 1.25em;
} </style> 
```

这里没有太多革命性的东西。注意`<router-view />`组件的使用，这是我们显示视图的地方。

### 家用部件

如上所述，`Home`组件充当默认(`/`)路由。打开它，用下面的代码块替换内容:

```
<template>
  <section class="hero">
    <div class="hero-body">
      <div class="container">
        <h1>Creating Beautiful Charts Using Vue.js Wrappers For Chart.js</h1>
        <h2>
          Read the article on SitePoint:
          <a href="https://www.sitepoint.com/creating-beautiful-charts-vue-chart-js/">
            Creating Beautiful Charts Using Vue.js Wrappers for Chart.js
          </a>
        </h2>
        <h3>
          Download the repo from GitHub:
          <a href="https://github.com/sitepoint-editors/vue-charts">
            https://github.com/sitepoint-editors/vue-charts
          </a>
        </h3>
      </div>
    </div>
  </section>
</template>

<script> export default {
  name: 'home'
} </script> 
```

### 添加布尔玛

在我们开始添加图表之前，还有一件事。让我们将[布尔玛 CSS 框架](http://bulma.io/)添加到应用程序中。对于 CSS 来说，这应该会使事情变得更容易。

打开`public/index.html`文件，在`head`标签中添加以下内容:

```
<link href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.2/css/bulma.min.css" rel="stylesheet"> 
```

现在，如果你访问 [localhost:8080](http://localhost:8080) ，你应该能够在应用程序的外壳中导航。

最后，我们可以继续创建图表！

## 使用 vue-chartjs 制作图表

`vue-chartjs`是一个 Chart.js 包装器，允许我们轻松地创建可重用的图表组件。可重用性意味着我们可以导入基本图表类并扩展它来创建定制组件。

也就是说，我们将展示可以使用`vue-chartjs`构建的四种类型的图表:折线图、条形图、气泡图和展示反应性的条形图(只要数据集发生变化，图表就会更新)。

### 折线图

为了创建折线图，我们将创建一个组件来呈现这种类型的图表。打开`src/components`文件夹中的`LineChart.vue`组件文件，添加以下代码:

```
<script> //Importing Line class from the vue-chartjs wrapper
  import { Line } from 'vue-chartjs'

  //Exporting this so it can be used in other components
  export default {
    extends: Line,
    data () {
      return {
        datacollection: {
          //Data to be represented on x-axis
          labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
          datasets: [
            {
              label: 'Data One',
              backgroundColor: '#f87979',
              pointBackgroundColor: 'white',
              borderWidth: 1,
              pointBorderColor: '#249EBF',
              //Data to be represented on y-axis
              data: [40, 20, 30, 50, 90, 10, 20, 40, 50, 70, 90, 100]
            }
          ]
        },
        //Chart.js options that controls the appearance of the chart
        options: {
          scales: {
            yAxes: [{
              ticks: {
                beginAtZero: true
              },
              gridLines: {
                display: true
              }
            }],
            xAxes: [ {
              gridLines: {
                display: false
              }
            }]
          },
          legend: {
            display: true
          },
          responsive: true,
          maintainAspectRatio: false
        }
      }
    },
    mounted () {
      //renderChart function renders the chart with the datacollection and options object.
      this.renderChart(this.datacollection, this.options)
    }
  } </script> 
```

我们来讨论一下上面的代码在做什么。我们做的第一件事是从`vue-chartjs`导入我们需要的图表类(在本例中是`Line`)并导出它。

`data`属性包含一个`datacollection`对象，该对象包含我们构建折线图所需的所有信息。这包括 Chart.js 配置，如将在 x 轴上表示的`labels`、`datasets`，将在 y 轴上表示，以及控制图表外观的`options`对象。

`mounted`函数调用`renderChart()`,它使用作为参数传入的`datacollection`和`options`对象来呈现图表。

现在，让我们打开`views/VueChartJS.vue`文件并添加以下代码:

```
<template>
  <section class="container">
    <h1>Demo examples of vue-chartjs</h1>
    <div class="columns">
      <div class="column">
        <h3>Line Chart</h3>
        <line-chart></line-chart>
      </div>
      <div class="column">
        <h3>Bar Chart</h3>
        <!--Bar Chart example-->
      </div>
    </div>
    <div class="columns">
      <div class="column">
        <h3>Bubble Chart</h3>
        <!--Bubble Chart example-->
      </div>
      <div class="column">
        <h3>Reactivity - Live update upon change in datasets</h3>
        <!--Reactivity Line Chart example-->
      </div>
    </div>
  </section>
</template>

<script> import LineChart from '@/components/LineChart'
  import BarChart from '@/components/BarChart'
  import BubbleChart from '@/components/BubbleChart'
  import Reactive from '@/components/Reactive'

  export default {
    name: 'VueChartJS',
    components: {
      LineChart,
      BarChart,
      BubbleChart,
      Reactive
    }
  } </script> 
```

`VueChartJS.vue`文件包含保存 HTML 代码的`template`部分、保存 JavaScript 代码的`script`部分和保存 CSS 代码的`style`部分。

在`template`部分，我们使用布尔玛的`columns`类创建了一个有两列两行的布局。我们还添加了一个将在`script`部分创建的`line-chart`组件。

在`script`部分，我们导入了之前创建的`.vue`文件，并在`components`对象中引用它们。这意味着我们可以像这样在 HTML 代码中使用它们:`line-chart`、`bar-chart`、`bubble-chart`和`reactive`。

现在，如果您导航到`/chartjs`，折线图应该会显示在该页面上。

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )查看 Pen[vue-chart . js–折线图](https://codepen.io/SitePoint/pen/zdGNNz/)。