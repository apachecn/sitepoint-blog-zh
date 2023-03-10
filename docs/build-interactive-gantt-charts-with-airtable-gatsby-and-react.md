# 使用 Airtable、Gatsby 和 React 构建交互式甘特图

> 原文：<https://www.sitepoint.com/build-interactive-gantt-charts-with-airtable-gatsby-and-react/>

有了 Gatsby，将不同的数据源集成到一个应用程序中变得非常容易。在本文中，我们将开发一个任务管理工具，它的数据来自 Airtable。我们将使用 React 作为前端，采用混合渲染策略。

这是一个常见的场景:您想要开发一个连接到电子表格应用程序或其他数据源的应用程序。在本文中，我将向您展示如何用 Gatsby 框架实现这种类型的应用程序。在我们的示例应用程序中，任务将从一个 [Airtable](https://www.sitepoint.com/how-to-use-airtable-a-beginners-guide/) 工作区导入，并可视化为一个[甘特图](https://en.wikipedia.org/wiki/Gantt_chart)。用户可以通过拖放来移动任务，之后所有的更改都将与 Airtable 同步。您可以将该项目用作各种日程安排应用程序的模板。

![A simple task table](img/88f1f2018ccdd52c8392f2ecb30b9393.png)

你可以在我的[盖茨比云网站](https://ganttchartgatsbymain.gatsbyjs.io/)上试试这个结果。项目的 src 文件可以在我的 [GitHub 库](https://github.com/aprenzel/gantt-chart-gatsby-public)中找到。

## 设置项目

Gatsby 是一个静态站点生成器。这意味着您用 React 编写应用程序，Gatsby 将您的代码翻译成浏览器可以理解的 HTML 文件。这个构建过程在**服务器端**定期执行，与传统的 web 应用程序形成对比，在传统的 web 应用程序中，HTML 代码首先在客户端的用户浏览器中进行组装。因此，HTML 文件在服务器上是静态可用的(因此称为静态站点生成器)，并且可以在请求时直接发送到客户端。这为用户减少了应用程序的加载时间。

SitePoint 的 [Gatsby 教程](https://www.sitepoint.com/gatsby-guide/)提供了使用这个框架开发应用程序所需的所有信息。如果您想一步一步地开发我的示例应用程序，您应该从下面的大纲开始。

首先，你应该下载并安装 [Node.js](https://nodejs.org/en/download/) 。您可以通过在控制台上键入`node -v`来检查它是否安装正确。应该显示节点的当前版本:

```
node -v
> v14.16.0 
```

对于 Node，我们还获得了 npm，即节点包管理器。有了这个工具，我们现在可以安装 Gatsby CLI:

```
npm install -g gatsby-cli 
```

我们准备使用 Gatsby CLI 创建一个新项目。我把它命名为“甘特图——盖茨比”:

```
gatsby new gantt-chart-gatsby 
```

然后使用命令`cd gantt-chart-gatsby`移动到项目文件夹，并使用命令`gatsby develop`构建项目。现在您可以在 [http://localhost:8000](http://localhost:8000) 上的浏览器中打开该项目的索引页面。一开始，你应该只会看到盖茨比为我们准备的欢迎页。

在下一步中，您应该检查项目的`src`文件夹。子文件夹`src/pages`包含项目各个页面的 React 组件。现在，为索引页面保留`index.js`文件就足够了，因为在我们的示例应用程序中，我们只需要一个页面。您可以删除该文件夹中的其他文件，除了`404.js`(如果有人输入了错误的地址，这可能会很有用)。

如果您用以下代码覆盖`index.js`中的现有代码，这是一个很好的起点:

```
import * as React from 'react'

const IndexPage = () => {
  return (
   <main>
      <title>Gantt Chart</title>
      <h1>Welcome to my Gatsby Gantt Chart</h1> 

    </main>
  )
}

export default IndexPage; 
```

您可以使用命令行上的命令`gatsby develop`再次构建项目，并在浏览器中打开索引页面。现在，您应该会看到一个标题为“欢迎使用我的盖茨比甘特图”的空白页面。

## 用 React 构建前端

### 索引页的第一个版本

我们将把甘特图实现为一个可重用的 React 组件。在下面的小节中详细解释组件的实现之前，我想先展示一下它是如何初始化并嵌入到索引页面中的。所以我建议你在我们完成组件的第一个版本之前不要使用`gatsby develop`命令。(准备好了我会告诉你的！)

在这个示例项目中，我使用了“工作”和“资源”的概念。**作业**是绘制到图表单元格中的任务，可以通过拖放来移动。**资源**包含可以移动作业的行的标签。这些可以是任务的名称，但在其他用例中，也可以是执行任务的人、车辆或机器的名称。

任务和资源作为属性传递给甘特图组件。在将任务管理工具连接到 Airtable 之前，我们用一些 JSON 格式的硬编码测试数据填充列表:

```
import * as React from "react";
import {GanttChart} from "../GanttChart";
import "../styles/index.css";

let j = [
  {id: "j1", start: new Date("2021/6/1"), end: new Date("2021/6/4"), resource: "r1"},
  {id: "j2", start: new Date("2021/6/4"), end: new Date("2021/6/13"), resource: "r2"},
  {id: "j3", start: new Date("2021/6/13"), end: new Date("2021/6/21"), resource: "r3"},
];

let r = [{id:"r1", name: "Task 1"}, {id:"r2", name: "Task 2"}, {id:"r3", name: "Task 3"}, {id:"r4", name: "Task 4"}];

const IndexPage = () => {
  return (
    <main>
      <title>Gantt Chart</title>
      <h1>Welcome to my Gatsby Gantt Chart</h1> 
      <GanttChart jobs={j} resources={r}/>
    </main>
  )
};

export default IndexPage; 
```

### 甘特图的 CSS 样式

下一步，我们在`styles`文件夹中创建一个新的`index.css`文件。(如果文件夹不存在，在项目的文件夹`src`中新建一个文件夹`styles`。)下列 CSS 设置控制甘特图的布局和外观:

```
body{
  font-family: Arial, Helvetica, sans-serif;
}

#gantt-container{
  display: grid;     
}

.gantt-row-resource{
  background-color:whitesmoke;
  color:rgba(0, 0, 0, 0.726);
  border:1px solid rgb(133, 129, 129);
  text-align: center;
  padding: 15px;
}

.gantt-row-period{
  background-color:whitesmoke;
  color:rgba(0, 0, 0, 0.726);
  border:1px solid rgb(133, 129, 129);
  text-align: center;

  display:grid;
  grid-auto-flow: column;
  grid-auto-columns: minmax(40px, 1fr);
}

.period{
  padding: 10px 0 10px 0;
}

.gantt-row-item{
  border: 1px solid rgb(214, 214, 214);
  padding: 10px 0 10px 0;
  position: relative;
  background-color:white;
}

.job{
  position: absolute;
  height:38px;
  top:5px;
  z-index: 100;
  background-color:rgb(167, 171, 245);
  cursor: pointer;
} 
```

### 实现`GanttChart`组件

现在我将更详细地解释`GanttChart`组件的实现。首先，我们需要在`src`文件夹中有一个名为`GanttChart.js`的文件。在本教程中，我只使用了简化版的`GanttChart`一个月(2021 年 6 月)。在 GitHub 上可以找到一个扩展版本，名字是 [GanttChart_extended.js](https://github.com/aprenzel/gantt-chart-gatsby-public/blob/main/src/GanttChart_extended.js) ，其中选择了开始月份和结束月份的字段。

图表分三步建立，分别用函数`initFirstRow`、`initSecondRow`和`initGanttRows`表示:

```
import React from 'react';

export class GanttChart extends React.Component {

    names = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];

    constructor(props) {

        super(props);       

        this.state = {
            dateFrom: new Date(2021,5,1),
            dateTo: new Date(2021,5,30),
        };
    }

    render(){

        let month = new Date(this.state.dateFrom.getFullYear(), this.state.dateFrom.getMonth(), 1);

        let grid_style = "100px 1fr";

        let firstRow = this.initFirstRow(month);
        let secondRow = this.initSecondRow(month);
        let ganttRows = this.initGanttRows(month);

        return (

            <div className="gantt-chart">
                <div id="gantt-container" style={{gridTemplateColumns : grid_style}}>
                    {firstRow}
                    {secondRow}
                    {ganttRows}
                </div>
            </div>
        );
     }

    initFirstRow(month){...}

    initSecondRow(month){...}

    initGanttRows(month){...}

    //helper functions:

    formatDate(d){ 
        return d.getFullYear()+"-"+this.zeroPad(d.getMonth()+1)+"-"+this.zeroPad(d.getDate());  
    }

    zeroPad(n){
        return n<10 ? "0"+n : n;
    }

    monthDiff(d1, d2) {
        let months;
        months = (d2.getFullYear() - d1.getFullYear()) * 12;
        months -= d1.getMonth();
        months += d2.getMonth();
        return months <= 0 ? 0 : months;
    }

    dayDiff(d1, d2){   
        let diffTime = Math.abs(d2 - d1);
        let diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24)); 
        return diffDays;
    }

} 
```

在`initFirstRow`函数中，生成图表表格的第一行。从上图可以看出，第一行由两个网格单元组成。这些被生成为 div，div 又作为子元素被插入到“gantt-container”中(参见上面的清单)。第二个 div 也包含当前月份的标签。

React 要求作为枚举一部分的所有元素都有唯一的“key”属性。这有助于优化渲染性能:

```
 initFirstRow(month){

    let elements = []; let i = 0;

    elements.push(<div key={"fr"+(i++)} className="gantt-row-resource"></div>);

    elements.push(<div key={"fr"+(i++)} className="gantt-row-period"><div className="period">{this.names[month.getMonth()] + " " + month.getFullYear()}</div></div>);

    return elements;
 } 
```

图表表格的下一行在`initSecondRow`函数中生成。我们再次使用相同的原则:为每个表格单元格创建一个 div。您必须确保 div 是正确嵌套的(该行中的第二个 div 包含一个月中每一天的单独 div ),以便 CSS 网格设置(参见`index.css`文件)将产生所需的布局:

```
initSecondRow(month){

    let elements = []; let i=0;

    //first div
    elements.push(<div key={"sr"+(i++)} style={{borderTop : 'none'}} className="gantt-row-resource"></div>);

    let days = [];

    let f_om = new Date(month); //first day of month
    let l_om = new Date(month.getFullYear(), month.getMonth()+1, 0); //last day of month

    let date = new Date(f_om);

    for(date; date <= l_om; date.setDate(date.getDate()+1)){

        days.push(<div key={"sr"+(i++)} style={{borderTop: 'none'}} className="gantt-row-period period">{date.getDate()}</div>);
    }

    //second div in the row with child divs for the individual days
    elements.push(<div key={"sr"+(i++)} style={{border: 'none'}} className="gantt-row-period">{days}</div>);

    return elements;

} 
```

图表表格的其余行在`initGanttRows`函数中生成。它们包含绘制作业的网格单元。同样，渲染是逐行进行的:对于每一行，我们首先放置资源的名称，然后遍历一个月中的每一天。对于特定的日期和资源，每个网格单元都被初始化为一个`ChartCell`组件。通过`cell_jobs`列表，单个单元格被分配了需要绘制到其中的作业(通常这正好是一个作业):

```
initGanttRows(month){

    let elements = []; let i=0;

    this.props.resources.forEach(resource => {

        elements.push(<div key={"gr"+(i++)} style={{borderTop : 'none'}} className="gantt-row-resource">{resource.name}</div>);

        let cells = [];

        let f_om = new Date(month);
        let l_om = new Date(month.getFullYear(), month.getMonth()+1, 0);

        let date = new Date(f_om);

        for(date; date <= l_om; date.setDate(date.getDate()+1)){

            let cell_jobs = this.props.jobs.filter((job) => job.resource == resource.id && job.start.getTime() == date.getTime());

            cells.push(<ChartCell key={"gr"+(i++)} resource={resource} date={new Date(date)} jobs={cell_jobs}/>);
        }

        elements.push(<div key={"gr"+(i++)} style={{border: 'none'}} className="gantt-row-period">{cells}</div>);

    });

    return elements;
} 
```

现在在`GanttChart.js`的末尾为`ChartCell`组件添加以下代码。该组件将图表的单个表格单元格呈现为包含一个或多个作业作为子元素的 div。显示工作的 HTML 代码由`getJobElement`函数提供:

```
class ChartCell extends React.Component {

    constructor(props) {

      super(props);

      this.state = {
        jobs: props.jobs
      }
    }

    render(){

      let jobElements = this.props.jobs.map((job) => this.getJobElement(job));

      return (
        <div 
            style={{borderTop: 'none', borderRight: 'none', backgroundColor: (this.props.date.getDay()==0 || this.props.date.getDay()==6) ? "whitesmoke" : "white" }} 
            className="gantt-row-item">
            {jobElements}
        </div>
      );
    }

    getJobElement(job){

        let d = this.dayDiff(job.start, job.end);

        //Example: a job with a duration of 2 days covers exactly two grid cells, so the width is 2*100% and we have to add up 2px for the width of the grid lines
        return (
        <div    style={{width: "calc("+(d*100)+"% + "+ d + "px)"}} 
                className="job" 
                id={job.id} 
                key={job.id}
        >

        </div>
        );
    }

    dayDiff(d1, d2){   
        let diffTime = Math.abs(d2 - d1);
        let diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24)); 
        return diffDays;
    }
} 
```

此时，您可以使用`gatsby develop`命令从根文件夹构建项目。索引页中的硬编码作业应该在甘特图中可见。它们还不能被拖放，但我们稍后会处理这个问题。

## 从 Airtable 集成数据

是时候将我们的应用程序连接到 Airtable 了，这样我们就可以从那里导入作业和资源。首先，在 [Airtable](https://airtable.com/) 创建一个免费账户。登录后，你会看到一个“未命名的基地”(见下图)。点击**添加一个基地**，然后**从头开始**，为你的基地输入一个名字。我输入“任务管理器”。

![Adding a base](img/c84a3bcfa427b1b11c8069fede69d62b.png)

### 使用“作业”和“资源”表设置 Airtable 库

现在，您可以按照以下步骤为您的数据库定义表:

1.  用字段`id`(字段类型:文本)、`start`(字段类型:日期)和`end`(字段类型:日期)定义表格“作业”。
2.  用字段`id`(字段类型:文本)和`name`(字段类型:文本)定义表格“资源”。
3.  转到表“Jobs”，添加字段类型为“Link to another record”的字段`resource`，然后选择字段`id`作为表“Resource”的查找字段。

完成这些步骤后，您的表格应该如下图所示。

![Task manager](img/2d3593ad848c46843ce831af52a3d667.png)

![Task manager](img/f07c5e71b7a28a914f2195044debb189.png)

### 使用 GraphQL 和 Gatsby 的 Airtable 插件从 Airtable 导入数据

接下来，我们希望将数据从 Airtable 导入到我们的应用程序中。为此，用`npm install --save gatsby-source-airtable`安装插件“gatsby-source-airtable”。然后，修改项目文件夹中的`gatsby-config.js`文件，如下所示:

*   你的 API-Key:[https://airtable.com/account](https://airtable.com/account)
*   你的基地 ID:进入[https://airtable.com/api](https://airtable.com/api)，选择你的基地，接下来的页面会显示你基地的 ID

```
module.exports = {
  siteMetadata: {
    siteUrl: "https://www.yourdomain.tld",
    title: "Gatsby Gantt Chart",
  },
  plugins: [

  "gatsby-plugin-gatsby-cloud",

  {
    resolve: "gatsby-source-airtable",
    options: {
      apiKey: "XXX", // may instead specify via env, see below
      concurrency: 5, // default, see using markdown and attachments for more information
      tables: [
        {
          baseId: "YYY",
          tableName: "Jobs",    
        },
        {
          baseId: "YYY",
          tableName: "Resources",
        }
      ]
    }
  }
  ],
}; 
```

现在我们可以尝试从 Airtable 中获取数据。用`gatsby develop`启动您的应用程序，然后在浏览器中的`http://localhost:8000/___graphql`处打开 GraphiQL 编辑器，并将以下查询粘贴到左侧区域:

```
{
  jobs: allAirtable(filter: {table: {eq: "Jobs"}, data: {}}) {
    edges {
      node {
        data {
          id
          start
          end
          id__from_resource_
          resource
        }
        recordId
      }
    }
  }
  resources: allAirtable(
    filter: {table: {eq: "Resources"}}
    sort: {fields: [data___name], order: ASC}
  ) {
    edges {
      node {
        data {
          id
          name
        }
      }
    }
  }
} 
```

单击箭头符号运行查询。查询的结果应该出现在右侧。

![The result of the query](img/8d1ab45c38fb5ecf371eadffa0a2732c.png)

现在是时候删除`index.js`中带有作业和资源的硬编码列表了。更新`index.js`中的代码，如下所示。这里发生了什么事？首先，在文件的末尾，您可以看到一个所谓的“[页面查询](https://www.gatsbyjs.com/docs/how-to/querying-data/page-query/)，它请求所有的作业和资源。查询的结果被自动分配给组件`IndexPage`的`data`属性。因此，`data`属性在右侧的 GraphiQL 编辑器中准确地存储了您所看到的查询结果。我们可以使用`map`函数将`jobs`和`resources`数组转换成我们喜欢的格式。

即使这看起来有点麻烦，我们也必须为所有作业保留由 Airtable 自动创建的属性`recordID`和`id__from_resource`。这是必要的，以便我们稍后可以通过 Airtable REST API 保存对作业的更改:

```
import * as React from "react"
import { useStaticQuery, graphql } from "gatsby"
import {GanttChart} from "../GanttChart"
import '../styles/index.css';

// markup
const IndexPage = (data) => {

  let j = data.data.jobs.edges.map(edge => {

    let s = new Date(edge.node.data.start);
    s.setHours(0);

    let e = new Date(edge.node.data.end);
    e.setHours(0);

    return {
      airtable_id: edge.node.recordId,
      id:edge.node.data.id,
      start: s,
      end: e,
      resource: edge.node.data.id__from_resource_[0],
      resource_airtable_id: edge.node.data.resource[0]
    };
  });

  let r = data.data.resources.edges.map(edge => {

    return{
      id: edge.node.data.id,
      name: edge.node.data.name
    }
  });

  if(r && j){
    return (
      <main>
        <title>Gantt Chart</title>
        <h1>Welcome to my Gatsby Gantt Chart</h1> 
        <GanttChart jobs={j} resources={r}/> 
      </main>
    )
  }else{
    return (
      <main>
        <title>Gantt Chart</title>
        <h1>Welcome to my Gatsby Gantt Chart</h1> 
        <p>Missing data...</p> 
      </main>
    )
  }
}

export const query = graphql` query{
        jobs: allAirtable(filter: {table: {eq: "Jobs"}, data: {}}) {
          edges {
            node {
              data {
                id
                start
                end
                id__from_resource_
                resource
              }
              recordId
            }
          }
        }
        resources: allAirtable(
          filter: {table: {eq: "Resources"}}
          sort: {fields: [data___name], order: ASC}
        ) {
          edges {
            node {
              data {
                id
                name
              }
            }
          }
        }
      } `
export default IndexPage; 
```

如果您使用`gatsby develop`在本地构建并启动您的应用程序，数据将从 Airtable 中获取并显示在甘特图中。如果你已经根据 [Gatsby 教程](https://www.sitepoint.com/gatsby-guide/)建立了一个 Gatsby Cloud 网站，一旦你将代码更改推送到相关的 GitHub 账户，该网站就会更新。但是，您会注意到，Airtable 查询只在项目构建时执行(不管是在本地还是在 Gatsby Cloud 站点上)。如果您修改了 Airtable 数据库中的数据，除非您重新构建项目，否则更改不会反映在甘特图中。这是典型的盖茨比的服务器端渲染过程。

在下一节中，我们将讨论如何处理数据的变化。

## 实现 Gatsby 和 Airtable 之间的双向同步

在我们的示例中，可以在 Airtable(通过编辑表格单元格)或甘特图(通过拖放)中对数据进行更改。为了同步这些部分，我遵循一种混合策略，包括服务器端和客户端的更新操作。

### 1.将更改从 Airtable 转移到甘特图(服务器端)

Gatsby 提供了 webhooks 来远程触发服务器端的构建过程。如果您拥有专业会员资格，可以配置 Airtable 在某些事件(如创建或更改记录)上自动触发构建挂钩。(你可以在[这里](https://community.airtable.com/t/webhooks-for-records/1966/130)找到更多关于此目的所需设置的详细信息)。

### 2.将更改从 Airtable 转移到甘特图(客户端)

当应用程序在浏览器中使用时，甘特图应该动态地从 Airtable 加载更新(例如，在某个时间间隔)。为了简化这个过程，我们只想以指定的时间间隔重新下载作业和资源的完整列表。为此，我们将使用官方的 Airtable API。

在`IndexPage`组件中，我们使用 [React 的 useState 钩子](https://reactjs.org/docs/hooks-state.html)来设置列表，将作业和资源作为组件的状态。然后我们应用 [useEffect 钩子](https://reactjs.org/docs/hooks-effect.html)来设置一个间隔，一旦组件被初始化，函数`loadDataFromAirtable`应该在这个间隔被调用:

```
const IndexPage = (data) => {

  let j = data.data.jobs.edges.map(edge => {...});
  let r = data.data.resources.edges.map(edge => {...});

  const [resources, setResources] = useState(r);
  const [jobs, setJobs] = useState(j);

  useEffect(() => {
    const interval = setInterval(() => { 

      let jobsLoaded = (j) => { setJobs(j) };
      let resourcesLoaded = (r) => { setResources(r) };

      loadDataFromAirtable(jobsLoaded, resourcesLoaded);

    }, 60000);  

    return () => clearInterval(interval);
  }, []);

  if(resources && jobs){
    return (
      <main>
        <title>Gantt Chart</title>
        <h1>Welcome to my Gatsby Gantt Chart</h1> 
        <GanttChart jobs={jobs} resources={resources}/> 
      </main>
    )
  }else{
    return (
      <main>
        <title>Gantt Chart</title>
        <h1>Welcome to my Gatsby Gantt Chart</h1> 
        <p>Missing data...</p> 
      </main>
    )
  }
} 
```

对于`loadDataFromAirtable`函数的实现，我们看一下 [Airtable API](https://airtable.com/api) 的文档。文档根据所选的库(在我们的例子中是“任务管理器”)进行调整。如果您点击左侧的**作业表**和**列表记录**，您将看到一个 GET 请求的确切结构，以检索“curl”区域中所有作业的数据。这个请求可以使用“fetch”方法在 JavaScript 中非常容易地实现。

因此，为了下载所有作业和资源的数据，我们依次对 Airtable 执行两个异步 GET 请求。我已经屏蔽了确切的 URL，因为它们包含我的个人 API 密钥:

```
function loadDataFromAirtable(onJobsLoaded, onResourcesLoaded){

  let j,r;

  let url_j= "XXXX";
  let url_r= "YYYY";

  fetch(url_j, {headers: {"Authorization": "ZZZZ"}})
  .then(response => response.json())
  .then(data => {

    j = data.records.map(record => {

      let s = new Date(record.fields.start);
      s.setHours(0);

      let e = new Date(record.fields.end);
      e.setHours(0);

       return {
        airtable_id: record.id,
        id: record.fields.id,
        start: s,
        end: e,
        resource: record.fields['id (from resource)'][0],
        resource_airtable_id: record.fields.resource[0]
       };
    });

    onJobsLoaded(j);
  });

  fetch(url_r, {headers: {"Authorization": "ZZZZ"}})
  .then(response => response.json())
  .then(data => {

    r = data.records.map(record => {

       return {
        id: record.fields.id,
        name: record.fields.name
       };
    });

    onResourcesLoaded(r);
  });
} 
```

作为测试，您可以对 Airtable 库中的作业数据进行一些更改。在给定的间隔时间(这里是一分钟)后，甘特图应该会在您的浏览器中自动更新。

### 3.将更改从甘特图转移到 Airtable 库(客户端)

在用户可以修改甘特图之前，我们必须首先使作业可拖动。为此，按如下方式更新`ChartCell`组件:

```
class ChartCell extends React.Component {

    constructor(props) {

      super(props);
    }

    render(){

      let jobElements = this.props.jobs.map((job) => this.getJobElement(job));

      let dragOver = (ev) => {ev.preventDefault()};

      let drop = (ev) => {

        ev.preventDefault(); 

        let job_id = ev.dataTransfer.getData("job");  

        this.props.onDropJob(job_id, this.props.resource.id, this.props.date)

      };

      return (
        <div 
            style={{borderTop: 'none', borderRight: 'none', backgroundColor: (this.props.date.getDay()==0 || this.props.date.getDay()==6) ? "whitesmoke" : "white" }} 
            className="gantt-row-item" onDragOver={dragOver} onDrop={drop}>
            {jobElements}
        </div>
      );
    }

    getJobElement(job){

        let d = this.dayDiff(job.start, job.end);

        return (
        <div    style={{width: "calc("+(d*100)+"% + "+ d + "px)"}} 
                className="job" 
                id={job.id} 
                key={job.id}
                draggable="true"
                onDragStart={this.dragStart}>
        </div>
        );
    }

    dragStart(ev){ ev.dataTransfer.setData("job", ev.target.id);}

    dayDiff(d1, d2){

        let diffTime = Math.abs(d2 - d1);
        let diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24)); 
        return diffDays;
    }
} 
```

用 JavaScript 实现拖放并不特别复杂。你必须实现事件处理程序`onDragStart`(对于可拖动的元素)`onDragOver`和`onDrop`(对于拖放目标)，如本教程中的[所示。](https://www.w3schools.com/html/html5_draganddrop.asp)

我们需要指定哪个处理函数在由`drop`处理程序触发的`onDropJob`事件上被调用。在`initGanttRows`函数中，更新下面一行:

```
cells.push(<ChartCell key={"gr"+(i++)} resource={resource} date={new Date(date)} jobs={cell_jobs} onDropJob={this.dropJob}/>); 
```

在`GanttChart`组件中，添加函数`dropJob`:

```
dropJob(id, newResource, newDate){

      let job = this.props.jobs.find(j => j.id == id );

      let newJob = {};
      newJob.resource = newResource;

      let d = this.dayDiff(job.start, job.end); 
      let end = new Date(newDate);
      end.setDate(newDate.getDate()+d);

      newJob.start = newDate;
      newJob.end = end;

      this.props.onUpdateJob(id, newJob);
  }; 
```

作业列表的实际修改是在`index.js`中的父`IndexPage`组件中完成的。`slice`方法用于创建作业列表的副本。使用拖放操作移动的作业根据其 ID 位于列表中，并被赋予新的属性。之后，通过调用`setJobs`来更新`IndexPage`组件的状态。请注意，就在此时，触发了甘特图组件的重新呈现，现在作业元素出现在它的新位置:

```
const IndexPage = (data) => {

  ...

  let updateJob = (id, newJob) => {

    let new_jobs = jobs.slice();

    let job = new_jobs.find(j => j.id == id );

    job.resource = newJob.resource;
    job.start = newJob.start;
    job.end = newJob.end;

    setJobs(new_jobs);
    updateJobToAirtable(job);
  }

  if(resources && jobs){
    return (
      <main>
        <title>Gantt Chart</title>
        <h1>Welcome to my Gatsby Gantt Chart</h1> 
        <GanttChart jobs={jobs} resources={resources} onUpdateJob={updateJob}/> 
      </main>
    )
  }else{
    ...
  }
} 
```

最后一步，我们要实现`updateJobToAirtable`函数。同样，我们遵循 Airtable API 文档，这一次是在章节**更新记录**:

```
function updateJobToAirtable(job){

    let data = {
      records: [
      {
        id: job.airtable_id,
        fields: {
          id: job.id,
          start: formatDate(job.start),
          end: formatDate(job.end),
          resource: [
            job.resource_airtable_id
          ]
        }
      }
    ]};

 fetch("XXX", {
    method: "PATCH", 
    headers: {"Authorization": "ZZZ", "Content-Type": "application/json"},
    body: JSON.stringify(data)
  });
} 
```

现在，您可以在甘特图中移动作业，并观察表格“作业”如何在 Airtable 库中实时更新。

## 最后的想法

本文中的简单任务管理应用程序表明，服务器端呈现也可以用于具有丰富客户端交互的应用程序。主要优点是快速的初始加载时间，因为 DOM 是在服务器上准备的。特别是对于具有非常复杂的用户界面的应用程序(例如，用于计划任务的仪表板)，这一点非常重要。在客户端定期获取新数据通常不会导致重大的性能问题，因为 React 使用一种[复杂的算法](https://reactjs.org/docs/reconciliation.html)来确定对 DOM 的哪些更改实际上是必要的。

Gatsby 框架通过提供对服务器端呈现的无缝支持以及许多用于从外部源导入数据的插件，极大地简化了开发这种混合应用程序的过程。

## 分享这篇文章