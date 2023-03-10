# 用 VS 代码和 Chrome 调试器调试 JavaScript 项目

> 原文：<https://www.sitepoint.com/debugging-javascript-projects-vs-code-chrome-debugger/>

调试 JavaScript 并不是 JavaScript 编程中最有趣的方面，但却是一项至关重要的技能。本文涵盖了两个工具，它们将帮助您像专家一样调试 JavaScript。

想象一下，JavaScript 中没有`console.log()`函数。我很确定你会问自己的第一个问题是“我如何确认我的代码工作正常？”

答案在于使用调试工具。长期以来，包括我自己在内的大部分开发者都在用`console.log`调试残破的代码。又快又好用。然而，如果你不知道哪里和什么导致了 bug，事情有时会变得很挑剔。通常你会发现自己在你的代码中布满了`console.log`陷阱，看看哪一个会暴露出问题的根源。

为了补救这一点，我们需要改变我们的习惯，开始使用调试工具。有很多工具可以用来调试 JavaScript 代码，比如 Chrome Dev Tools、 [Node Debugger](https://www.sitepoint.com/testing-node-applications/) 、Node Inspect 等等。事实上，每个[主流浏览器](https://www.w3schools.com/js/js_debugging.asp)都提供了自己的工具。

在本文中，我们将了解如何使用 Visual Studio 代码提供的调试工具。我们还将看看如何使用 Chrome 扩展的[调试器，它允许 VS 代码与 Chrome 开发工具集成。一旦我们完成，你就再也不想使用`console.log()`了。](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)

## 先决条件

对于本教程，你只需要在[现代 JavaScript](https://www.sitepoint.com/anatomy-of-a-modern-javascript-application/) 有扎实的基础。我们还将看看如何调试使用 [Mocha 和 Chai](https://www.sitepoint.com/unit-test-javascript-mocha-chai/) 编写的测试。我们将使用一个中断的项目， [debug-example](https://github.com/sitepoint-editors/debug-example) ，来学习如何在不使用单个`console.log`的情况下修复各种 bug。您需要遵循以下内容:

*   [Node.js](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)
*   [Visual Studio 代码](https://code.visualstudio.com/)
*   [Chrome 浏览器](https://www.google.com/chrome/)

首先将[调试示例](https://github.com/sitepoint-editors/debug-example)项目克隆到您的工作区。在 VS 代码中打开项目，并通过集成终端安装依赖项:

```
# Install package dependencies
npm install

# Install global dependencies
npm install -g mocha 
```

现在我们准备学习如何用 VS 代码调试 JavaScript 项目。

## 在 VS 代码中调试 JavaScript

我想让你看的第一个文件是`src/places.js`。你需要打开 VS 代码中的`debug-project`文件夹(*文件* > *打开文件夹*)，并在编辑器中选择文件。

```
const places = [];

module.exports = {
  places,

  addPlace: (city, country) => {
    const id = ++places.length;
    let numType = 'odd';
    if (id % 2) {
      numType = 'even';
    }
    places.push({
      id, city, country, numType,
    });
  },
}; 
```

代码非常简单，如果你有足够的编码经验，你可能会注意到它有一些错误。如果你注意到他们，请忽略他们。如果没有，完美。让我们在底部添加几行代码来手动测试代码:

```
module.exports.addPlace('Mombasa', 'Kenya');
module.exports.addPlace('Kingston', 'Jamaica');
module.exports.addPlace('Cape Town', 'South Africa'); 
```

现在，我相信你一定很想做一个`console.log`来输出`places`的值。但是我们不要那样做。相反，让我们添加**断点**。只需通过左键单击装订线(即行号旁边的空白区域)来添加它们:

![Red dots indicating breakpoints](img/e82f1baadb278767c11c814277825daa.png)

看到边上的红点了吗？这些是断点。断点只是一个视觉指示，告诉调试器工具在哪里暂停执行。接下来，在操作栏上，单击 debug 按钮(表示“不允许有 bug”的图标)。

![The debug panel](img/8dcf279ea6504b9734da93e199300c39.png)

看最上面的部分。你会注意到有一个带红点的齿轮图标。只需点击它。将为您创建一个调试配置文件`launch.json`。像这样更新配置，以便可以在`places.js`上运行 VS 代码的调试器:

```
"configurations": [
  {
    "type": "node",
    "request": "launch",
    "name": "Launch Places",
    "program": "${workspaceFolder}\\src\\places.js"
  }
] 
```

*注意:根据您的操作系统，您可能需要用一个正斜杠(`/`)替换双反斜杠(`\\`)。*

保存文件后，您会注意到调试面板有一个新的下拉菜单， *Launch Places* 。要运行它，您可以:

*   点击调试面板上的绿色播放按钮
*   按下`F5`
*   点击菜单栏上的*调试* > *开始调试*。

使用您喜欢的任何方法，观察调试过程:

![Debug running](img/6ee2ca1aeede8ba0084e348df01c59d7.png)

一旦你点击了调试按钮，许多事情会接连发生。首先，编辑器顶部会出现一个工具栏。它有以下控件:

*   **拖动点锚**:用于将工具栏移动到某个它不会阻挡任何东西的地方
*   **继续**:继续调试会话
*   **跳过**:逐行执行代码，跳过函数
*   **进入**:逐行执行代码，进入函数内部
*   **步出**:如果已经在一个函数中，这个命令会带你出去
*   **重启**:重启调试会话
*   **停止**:停止调试会话。

现在，您会注意到调试会话已经在您的第一个断点处暂停。要继续会话，只需点击*继续*按钮，这将导致执行继续，直到它到达第二个断点并再次暂停。再次点击*继续*将完成执行，调试会话将结束。

让我们通过点击`F5`再次开始调试过程。确保两个断点仍在原位。当您放置断点时，代码在指定行暂停。除非你点击*继续* ( `F5`)或者*跨过* ( `F10`，否则它不会执行那一行。在开始之前，让我们先来看看组成调试面板的各个部分:

*   **变量**:显示当前范围内(即执行点)的局部和全局变量
*   **监视**:您可以手动添加想要监视的变量的表达式
*   **调用栈**:显示高亮代码的调用栈
*   **断点**:显示带有断点的文件列表及其行号。

要向*手表*部分添加一个表达式，只需单击 *+* 符号，然后添加任何有效的 JavaScript 表达式—比如`places.length`。当调试器暂停时，如果表达式在范围内，将打印出值。您还可以将鼠标悬停在当前范围内的变量上。将出现一个弹出窗口，显示它们的值。

当前`places`数组为空。按任意导航控件查看调试是如何进行的。例如，*跨过*将跳转到下一行，而*踏入*将导航到`addPlace`功能。花点时间熟悉一下控制。

一旦你完成了一些步进，将鼠标悬停在`places`变量上。将出现一个弹出窗口。展开其中的值，直到您看到类似的视图:

![The places popup](img/2c481dd01ad55bc9eb4cd86f6cfa12ba.png)

您还可以检查在*变量*部分范围内的所有变量。

![The variables section](img/fe9123c8ab1810e30bd2a326a2e37e57.png)

与我们通常用`console.log`做的事情相比，这是非常棒的。调试器允许我们在更深的层次上检查变量。您可能还注意到了`places`数组输出的几个问题:

1.  数组中有多个空格，即`places[0]`和`places[2]`是`undefined`
2.  `numType`属性显示奇数`id`值的`even`。

现在，只要结束调试会话。我们将在下一节中修复它们。

## 用 Mocha 调试测试

打开`test/placesTest.js`并检查已经编写的代码，以测试`places.test`中的代码。如果您以前从未使用过 Mocha，您需要首先全局安装它以便运行测试。

```
# Install mocha globally
npm install -g mocha

# Run mocha tests
mocha 
```

您也可以运行`npm test`来执行测试。您应该得到以下输出:

![Failing mocha tests](img/051e713697bf288466c5697e80c8b8a8.png)

所有的测试都失败了。为了找出问题，我们将在调试模式下运行测试。为此，我们需要一个新的配置。转到调试面板，点击下拉菜单访问`Add Configuration`选项:

![Add configuration](img/287b12a3e6e3899d00ddf9a93ab68785.png)

将为您打开`launch.json`文件，并弹出几个配置列表供您选择。

![Add mocha tests](img/dd25d3e94c663ba99511681e5e1af26a.png)

只需选择*摩卡测试*。将为您插入以下配置:

```
{
  "type": "node",
  "request": "launch",
  "name": "Mocha Tests",
  "program": "${workspaceFolder}/node_modules/mocha/bin/_mocha",
  "args": [
    "-u",
    "tdd",
    "--timeout",
    "999999",
    "--colors",
    "${workspaceFolder}/test"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}, 
```

默认设置就可以了。回到下拉菜单，选择*摩卡测试*。您需要注释掉您在`places.js`中添加的最后三行；否则测试不会像预期的那样运行。回到`placesTest.js`,在第一次测试失败发生的地方之前添加一个断点。那应该是第七行，上面写着:

```
addPlace('Nairobi', 'Kenya'); 
```

确保在 watch 部分添加一个`places.length`表达式。点击*播放*按钮开始调试会话。

![Mocha test start](img/29091b040da04d2eda52282bb6292c22.png)

测试开始时，`places.length`的读数应该为零。如果你点击*跨过*，`places.length`显示为 2，但只增加了一个位置。这怎么可能呢？

重启调试会话，这次使用*进入*导航到`addPlace`功能。调试器会将您导航到`places.js`。`places.length`的值仍然为零。点击*跳过*执行当前行。

![Mocha test step](img/80759ca32a2e046cacbdbf7b0975843d.png)

啊哈！`places.length`的值刚刚增加了 1，但是我们没有向数组中添加任何东西。这个问题是由改变数组长度的`++`操作符引起的。要解决这个问题，只需将该行替换为:

```
const id = places.length + 1; 
```

这样，我们可以安全地获得`id`的值，而不改变`places.length`的值。当我们仍处于调试模式时，让我们尝试修复另一个问题，即`numType`属性被赋予值`even`，而`id`为 1。问题似乎在于 if 语句中的模数表达式:

![Mocha if error](img/83cbd778dd0790badb225a73f2997c4a.png)

让我们使用调试控制台做一个快速实验。开始为`if`语句键入适当的表达式:

![Mocha debug console](img/7db056ace63f5b919da897b3cda02d18.png)

调试控制台类似于浏览器控制台。它允许您使用当前范围内的变量进行实验。通过在控制台中尝试一些想法，您无需离开编辑器就可以轻松找到解决方案。现在让我们修复失败的 if 语句:

```
if (id % 2 === 0) {
  numType = 'even';
} 
```

重启调试会话，点击*继续*跳过当前断点。第一个测试“能加一个名额”，现在通过了。但是第二个测试不是。为了解决这个问题，我们需要另一个断点。删除当前的断点，并在第 16 行放置一个新的断点，如下所示:

```
addPlace('Cape Town', 'South Africa'); 
```

开始新的调试会话:

![Mocha test2 fail](img/23ed9b81f77366210669a5e80af93c31.png)

那里！看看*变量*部分。甚至在第二次测试开始之前，我们就发现`places`数组已经有了第一次测试创建的数据。这显然污染了我们目前的测试。为了解决这个问题，我们需要实现某种类型的`setup`函数，为每个测试重置`places`数组。要在 Mocha 中做到这一点，只需在测试之前添加以下代码:

```
beforeEach(() => {
  places.length = 0;
}); 
```

重新启动调试器，让它在断点处暂停。现在`places`数组有了一个干净的状态。这应该允许我们的测试不受污染地运行。只需点击*继续*，让其余的测试代码执行。

![Mocha tests passed](img/45f7be1bc28f02ba9c98d5088e622264.png)

所有测试现在都通过了。你应该感觉很棒，因为你已经学会了如何不用写一行`console.log`就能调试代码。现在让我们看看如何使用浏览器调试客户端代码。

## 用 Chrome 调试器调试 JavaScript

既然您已经熟悉了在 VS 代码中调试 JavaScript 的基础知识，我们将看到如何使用 Chrome 扩展的[调试器来调试稍微复杂一点的项目。只需通过操作栏打开市场面板。搜索并安装扩展。](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)

![Debugger for Chrome](img/5f089102acd1151ed46faca17cb1cc71.png)

安装后，点击重新加载激活扩展。让我们快速回顾一下将要调试的代码。web 应用程序主要是一个客户端 JavaScript 项目，通过运行 Express 服务器启动:

```
const express = require('express');

const app = express();
const port = 3000;

// Set public folder as root
app.use(express.static('public'));

// Provide access to node_modules folder
app.use('/scripts', express.static(`${__dirname}/node_modules/`));

// Redirect all traffic to index.html
app.use((req, res) => res.sendFile(`${__dirname}/public/index.html`));

app.listen(port, () => {
  console.info('listening on %d', port);
}); 
```

所有客户端代码都在`public`文件夹里。该项目的依赖项包括 Semantic-UI-CSS、jQuery、Vanilla Router、Axios 和 Handlebars。这是您用`npm start`运行项目时的样子。你必须在浏览器中打开 URL [localhost:3000](http://localhost:3000) 来查看这个应用程序。

![Places missing table](img/09cbffd8a82d12ee0ad857986113cc17.png)

尝试添加一个新位置。当你这么做的时候，你会发现似乎什么都没发生。显然出了问题，所以是时候看看引擎盖下面了。在开始调试会话之前，我们将首先检查代码。打开`public/index.html`。我们目前的重点是这一部分:

```
<!-- TEMPLATES -->
<!-- Places Form Template -->
<script id="places-form-template" type="text/x-handlebars-template"> <h1 class="ui header">
    <i class="map marker alternate icon"></i>
    <div class="content"> Places</div>
  </h1>
  <hr>
  <br>
  <form class="ui form">
    <div class="fields">
      <div class="inline field">
        <label>City</label>
        <input type="text" placeholder="Enter city" id="city" name="city">
      </div>
      <div class="inline field">
        <label>Country</label>
        <input type="text" placeholder="Enter Country" name="country">
      </div>
      <div class="ui submit primary button">Add Place</div>
    </div>
  </form>
  <br>
  <div id="places-table"></div> </script>

<!-- Places Table Template -->
<script id="places-table-template" type="text/x-handlebars-template"> <table class="ui celled striped table">
    <thead>
      <tr>
        <th>Id</th>
        <th>City</th>
        <th>Country</th>
        <th>NumType</th>
      </tr>
    </thead>
    <tbody>
      {{#each places}}
      <tr>
        <td>{{id}}</td>
        <td>{{city}}</td>
        <td>{{country}}</td>
        <td>{{numType}}</td>
      </tr>
      {{/each}}
    </tbody>
  </table> </script> 
```

如果您快速浏览一下，代码似乎是正确的。所以问题一定在`app.js`。打开文件并分析其中的代码。下面是你应该注意的代码部分。花点时间阅读注释，以便理解代码。

```
// Load DOM roots
const el = $('#app');
const placesTable = $('#places-table');

// Initialize empty places array
const places = [];

// Compile Templates
const placesFormTemplate = Handlebars.compile($('#places-form-template').html());
const placesTableTemplate = Handlebars.compile($('#places-table-template').html());

const addPlace = (city, country) => {
  const id = places.length + 1;
  const numType = (id % 2 === 0) ? 'even' : 'odd';
  places.push({
    id, city, country, numType,
  });
};

// Populate places array
addPlace('Nairobi', 'Kenya');

...

// Places View - '/'
router.add('/', () => {
  // Display Places Form
  const html = placesFormTemplate();
  el.html(html);
  // Form Validation Rules
  $('.ui.form').form({
    fields: {
      city: 'empty',
      country: 'empty',
    },
  });
  // Display Places Table
  const tableHtml = placesTableTemplate({ places });
  placesTable.html(tableHtml);
  $('.submit').on('click', () => {
    const city = $('#city').val();
    const country = $('#country').val();
    addPlace(city, country);
    placesTable.html(placesTableTemplate({ places }));
    $('form').form('clear');
    return false;
  });
}); 
```

一切似乎都很好。但是会有什么问题呢？让我们在第 53 行设置一个断点，它说:

```
placesTable.html(tableHtml); 
```

接下来，通过调试面板创建一个 *Chrome* 配置。选择突出显示的选项:

![Chrome debug config](img/f718780aaa88b251d586083d08cfcd20.png)

然后更新 Chrome 配置，如下所示，以匹配我们的环境:

```
{
  "type": "chrome",
  "request": "launch",
  "name": "Launch Chrome",
  "url": "http://localhost:3000",
  "webRoot": "${workspaceFolder}/public"
}, 
```

接下来，使用`npm start`或`node server`正常启动服务器。然后选择*启动 Chrome* 并开始调试会话。Chrome 的一个新实例将在调试模式下启动，执行将在您设置断点的地方暂停。现在是把 Visual Studio 代码和 Chrome 实例放在一起的好时机，这样你可以更高效地工作。

![Debugging JavaScript with Chrome and VS Code](img/d7fd4d568c1419ec1c12656b299eac2b.png)

将鼠标悬停在`placesTable`常量上。出现一个弹出窗口，但它似乎是空的。在观察面板中，添加表达式`el`和`placesTable`。或者，也可以向上滚动到声明常量的地方。

![Element popup](img/f425d3337fd8d2ae583d259809475266.png)

请注意，`el`已填充，但`placesTable`为空。这意味着 jQuery 无法找到元素`#places-table`。让我们回到`public/index.html`，找到这个`#places-table`的位置。

啊哈！我们想要的表格 div 位于第 55 行，就在`places-form-template`的内部。这意味着 div `#places-table`只能在模板`places-form-template`加载后才能找到。要解决这个问题，只需返回到`app.js`并将代码移到第 52 行，就在“Display Places Table”注释之后:

```
const placesTable = $('#places-table'); 
```

保存文件，并重新启动调试会话。当它到达断点时，只需点击*继续*，让代码完成执行。该表现在应该是可见的:

![Chrome fixed table](img/42bb99375c8a21a4203fac6b42ec6463.png)

现在可以移除断点了。让我们尝试添加一个新的地点—例如，南非的开普敦

![Country missing](img/cdfbf68a3eb4f905c942411e081a8c20.png)

嗯……那不对。添加了地点，但没有显示国家。问题显然不在于 HTML 表格代码，因为第一行填充了 country 单元格，所以 JavaScript 端一定发生了什么。打开`app.js`并在第 58 行添加一个断点，其内容如下:

```
addPlace(city, country); 
```

重新启动调试会话，并再次尝试添加新位置。执行应该在您刚刚设置的断点处暂停。开始悬停在相关变量上。您还可以向“监视”面板添加表达式，如下所示:

![Country bug found](img/5dd703fbe500d08ad8f1c56808966a60.png)

如您所见，`country`变量是未定义的，但是`city`变量是未定义的。如果您查看已经在观察面板中设置的 jQuery 选择器表达式，您会注意到`#country`选择器不返回任何内容。这意味着它不存在于 DOM 中。前往`index.html`进行验证。

唉！如果您查看定义了国家输入的第 59 行，它缺少 ID 属性。您需要像这样添加一个:

```
<input type="text" placeholder="Enter Country" name="country" id="country"> 
```

重新启动调试会话，并尝试添加一个新位置。

![Country bug fixed](img/67a879abd1cfbb4d2e7b0d666997fbf3.png)

它现在工作了！在没有`console.log`的情况下修复另一个 bug 做得很好。现在让我们来看看最后一个 bug。

## 调试客户端路由

点击导航栏中的*转换*链接。您应该转到此视图来执行快速转换:

![Convert currency](img/a69215aa6aad0348c5dc4553cf56f3c6.png)

运行良好。那里没有虫子。

其实是有的，而且和形式无关。要发现它们，刷新页面。

只要你点击重新加载，用户就会被导航回`/`，应用的根目录。这显然是一个路由问题，普通路由器包应该处理这个问题。回到`app.js`，寻找这条线:

```
router.navigateTo(window.location.path); 
```

这段代码应该根据提供的 URL 将用户路由到正确的页面。但是为什么没有用呢？让我们在这里添加一个断点，然后导航回`/convert` URL 并尝试再次刷新页面。

刷新后，编辑器会在断点处暂停。悬停在快递`windows.location.path`上方。出现一个弹出窗口，显示值为`undefined`。让我们转到调试控制台，开始键入下面的表达式:

![Navigation error](img/f945741f79086d13a94e7f551a555eca.png)

停下来。调试控制台只是给了我们正确的表达式。应该是读`window.location.pathname`。更正代码行，移除断点，并重新启动调试会话。

导航到`/convert` URL 并刷新。页面应该重新加载正确的路径。厉害！

这是我们要克服的最后一个错误，但是我建议您在调试会话中继续尝试。设置新的断点，以便检查其他变量。例如，检查`router('/convert')`函数中的`response`对象。这演示了在处理新的 REST 端点时，如何使用调试会话来确定 API 请求返回的数据结构。

![Response inspect](img/521baf08828311c989868d966fedd661.png)

## 摘要

现在我们已经到了本教程的末尾，您应该为自己学会了编程中的一项重要技能而感到自豪。学习如何正确调试代码将有助于您更快地修复错误。然而，你应该意识到，这篇文章仅仅触及了可能的表面，你应该看一看 VS 代码的完整的[调试文档](https://code.visualstudio.com/docs/editor/debugging)。在这里你可以找到更多关于特定命令的细节，以及我们没有提到的断点类型，比如[条件断点](https://code.visualstudio.com/docs/editor/debugging#_conditional-breakpoints)。

我希望从现在开始你将停止使用`console.log`来调试，而是像专家一样使用 VS 代码来开始调试 JavaScript！

## 分享这篇文章