# 在 Deno 中构建一个命令行天气应用程序

> 原文：<https://www.sitepoint.com/deno-build-command-line-weather-app/>

**如果你一直关注我们在 Deno 上的[介绍性](https://www.sitepoint.com/deno-guide/) [文章](https://www.sitepoint.com/node-vs-deno/)，你可能有兴趣尝试编写你的第一个程序。在本文中，我们将逐步安装 Deno 运行时，并创建一个命令行天气程序，该程序将城市名称作为参数，并返回未来 24 小时的天气预报。**

要为 Deno 写代码，我强烈推荐官方 [Deno 插件](https://github.com/denoland/vscode_deno)的 [Visual Studio 代码](https://code.visualstudio.com/)。为了让事情变得更有趣，我们将使用 TypeScript 编写应用程序。

## 安装 Deno

首先，让我们在本地安装 Deno，这样我们就可以开始编写脚本了。这个过程很简单，因为所有三个主要操作系统都有安装程序脚本。

### Windows 操作系统

在 windows 上，您可以从 PowerShell 安装 Deno:

```
iwr https://deno.land/x/install/install.ps1 -useb | iex 
```

### Linux 操作系统

从 Linux 终端，您可以使用以下命令:

```
curl -fsSL https://deno.land/x/install/install.sh |  sh 
```

### 马科斯

在 Mac 上，Deno 可以与 Brew 一起安装:

```
brew install deno 
```

### 安装后

安装过程完成后，您可以通过运行以下命令来检查 Deno 是否已正确安装:

```
deno --version 
```

您现在应该会看到类似这样的内容:

```
deno 1.2.0
v8 8.5.216
typescript 3.9.2 
```

让我们为我们的新项目创建一个文件夹(在您的主文件夹中，或者您喜欢保存您的编码项目的任何地方)并添加一个`index.ts`文件:

```
mkdir weather-app
cd weather-app
code index.ts 
```

注意:正如我上面提到的，我在本教程中使用 VS 代码。如果您使用不同的编辑器，请替换上面的最后一行。

## 获取用户输入

我们的程序将检索给定城市的天气预报，因此在程序运行时，我们需要接受城市名称作为参数。提供给 Deno 脚本的参数以`Deno.args`的形式提供。让我们将这个变量记录到控制台，看看它是如何工作的:

```
console.log(Deno.args); 
```

现在，使用以下命令运行脚本:

```
deno run index.ts --city London 
```

您应该会看到以下输出:

```
[ "--city", "London" ] 
```

尽管我们可以自己解析这个参数数组， [Deno 的标准库](https://deno.land/std)包含了一个名为 [flags](https://deno.land/std/flags) 的模块，它将为我们处理这个问题。要使用它，我们只需在文件的顶部添加一条 import 语句:

```
import { parse } from  "https://deno.land/std@0.61.0/flags/mod.ts"; 
```

*注意:标准库模块文档中的例子会给你一个未版本化的 URL(比如`https://deno.land/std/flags/mod.ts`)，它总是指向代码的最新版本。在您的导入中指定一个版本是一个好的实践，以确保您的程序不会被未来的更新破坏。* *

让我们使用导入的函数将参数数组解析成更有用的东西:

```
const args = parse(Deno.args); 
```

我们还将修改脚本来注销新的`args`变量，看看是什么样子。所以现在你的代码应该是这样的:

```
import { parse } from  "https://deno.land/std@0.61.0/flags/mod.ts";

const args = parse(Deno.args);

console.log(args); 
```

现在，如果您使用与之前相同的参数运行脚本，您应该会看到以下输出:

```
Download https://deno.land/std@0.61.0/flags/mod.ts
Download https://deno.land/std@0.61.0/_util/assert.ts
Check file:///home/njacques/code/weather-app/index.ts
{ _: [], city: "London" } 
```

每当 Deno 运行脚本时，它都会检查新的导入语句。任何远程托管的导入都会被下载、编译和缓存，以备将来使用。`parse`函数为我们提供了一个对象，它有一个包含我们输入的`city`属性。

*注意:如果您出于某种原因需要重新下载脚本的导入，您可以运行`deno cache --reload index.ts`。*

我们还应该为`city`参数添加一个检查，如果没有提供，就用一个错误消息退出程序:

```
if (args.city === undefined) {
    console.error("No city supplied");
    Deno.exit();
} 
```

## 与气象 API 对话

我们将从 [OpenWeatherMap](https://openweathermap.org/) 获取预测数据。您需要注册一个免费帐户，以便获得 API 密钥。我们将使用他们的 [5 天预报 API](https://openweathermap.org/forecast5) ，传递一个城市名作为参数。

让我们添加一些代码来获取预测并将其记录到控制台，看看我们会得到什么:

```
import { parse } from  "https://deno.land/std@0.61.0/flags/mod.ts";

const args = parse(Deno.args);

if (args.city === undefined) {
    console.error("No city supplied");
    Deno.exit();
}

const apiKey = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx';

const res = await fetch(`https://api.openweathermap.org/data/2.5/forecast?q=${args.city}&units=metric&appid=${apiKey}`);
const data = await res.json();

console.log(data); 
```

Deno 尽可能地支持很多浏览器 API，所以在这里我们可以使用`fetch`而不必导入任何外部依赖。我们还利用了对顶级`await`的支持:通常我们必须将任何使用`await`的代码包装在一个`async`函数中，但是 TypeScript 并没有让我们这样做，这使得代码更好一些。

如果您现在尝试运行这个脚本，您将会遇到一条错误消息:

```
Check file:///home/njacques/code/weather-app/index.ts
error: Uncaught PermissionDenied: network access to "https://api.openweathermap.org/data/2.5/forecast?q=London&units=metric&appid=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", run again with the --allow-net flag
    at unwrapResponse ($deno$/ops/dispatch_json.ts:42:11)
    at Object.sendAsync ($deno$/ops/dispatch_json.ts:93:10)
    at async fetch ($deno$/web/fetch.ts:266:27)
    at async index.ts:12:13 
```

默认情况下，所有 Deno 脚本都运行在安全的沙箱中:它们不能访问网络、文件系统或环境变量之类的东西。脚本需要被明确地授予对它们需要访问的系统资源的权限。在这种情况下，错误消息有助于我们了解需要哪个权限以及如何启用它。

让我们用正确的标志再次调用脚本:

```
deno run --allow-net index.ts --city London 
```

这一次，我们应该从 API 得到一个 JSON 响应:

```
{
  cod: "200",
  message: 0,
  cnt: 40,
  list: [
    {
      dt: 1595527200,
      main: {
        temp: 22.6,
        feels_like: 18.7,
        temp_min: 21.04,
        temp_max: 22.6,
        pressure: 1013,
        sea_level: 1013,
        grnd_level: 1011,
        humidity: 39,
        temp_kf: 1.56
      },
      weather: [ [Object] ],
      clouds: { all: 88 },
      wind: { speed: 4.88, deg: 254 },
      visibility: 10000,
      pop: 0,
      sys: { pod: "d" },
      dt_txt: "2020-07-23 18:00:00"
    },
    ...
  ],
  city: {
    id: 2643743,
    name: "London",
    coord: { lat: 51.5085, lon: -0.1257 },
    country: "GB",
    population: 1000000,
    timezone: 3600,
    sunrise: 1595477494,
    sunset: 1595534525
  }
} 
```

您可以查看响应中返回的[的全部细节，但是我们感兴趣的主要是`list`中的预测数据数组。数组中的每个对象都包含一个时间戳(`dt`)，一个包含大气条件(温度、湿度、压力等)细节的`main`对象。)，以及一个包含描述预测天气的对象的`weather`数组。](https://openweathermap.org/forecast5#parameter)

我们将对`main`数组进行迭代，以获得预测的时间、温度和天气状况。让我们从限制记录的数量开始，只覆盖 24 小时的时间段。免费计划中可供我们使用的预测数据仅在三小时间隔内可用，因此我们需要获得八条记录:

```
const  forecast = data.list.slice(0, 8) 
```

我们将映射每个预测项目，并返回一组我们感兴趣的数据:

```
const forecast = data.list.slice(0, 8).map(item => [
    item.dt,
    item.main.temp,
    item.weather[0].description,
]); 
```

如果我们现在尝试运行这个脚本，我们将会得到一个编译错误(如果您使用的是类似 VS 代码的 IDE，那么在您键入代码时也会显示这个错误):**参数' item '隐式地有一个' any '类型。**

TypeScript 要求我们告诉它`item`是什么类型的变量，以便知道我们是否对它做了任何可能在运行时导致错误的事情。让我们添加一个接口，来描述`item`的结构:

```
interface forecastItem {
    dt: string;
    main: { temp: number; };
    weather: { description: string; }[];
} 
```

注意，我们在这里并没有描述*对象的所有*属性，只描述了我们实际将要访问的属性。在我们的情况下，我们知道我们想要哪些属性。

让我们将新类型添加到我们的`map`回调中:

```
const forecast = data.list.slice(0, 8).map((item: forecastItem) => [
    item.dt,
    item.main.temp,
    item.weather[0].description,
]); 
```

如果您使用的是支持 TypeScript 的 IDE，由于我们提供的接口类型，它应该能够在您键入时自动完成`item`的属性。

*   创建服务类别
*   为输出创建接口

## 格式化输出

现在我们已经有了我们想要的数据集，让我们看看如何很好地格式化它以显示给用户。

首先，让我们将时间戳值转换为人类可读的日期。如果我们看一下 Deno 的[第三方模块列表](https://deno.land/x)，搜索“date”，可以在列表中看到 [date-fns](https://www.sitepoint.com/date-fns-javascript-date-library/) 。我们可以使用此处的链接将我们将要使用的功能导入 Deno 应用程序:

```
import { fromUnixTime, format } from  "https://deno.land/x/date_fns@v2.15.0/index.js"; 
```

我们现在可以通过`fromUnixTime`函数传递时间戳，以获得一个日期对象，然后将该对象传递给`format`，以获得我们想要的日期字符串:

```
format(fromUnixTime(item.dt), "do LLL, k:mm", {}) 
```

格式字符串`do LLL, k:mm`将以如下格式给出日期:“24th Jul，13:00”。

*注意:我们将一个空对象作为第三个参数传递给`format`，纯粹是为了消除 IDE 关于预期参数数量的警告。没有它，代码仍然可以很好地运行。*

现在，让我们将温度值四舍五入到一个小数位，并添加一个单位指示器:

```
`${item.main.temp.toFixed(1)}C` 
```

既然我们已经将预测数据格式化并准备好显示，让我们使用 [ascii_table 模块](https://deno.land/x/ascii_table)以一个简洁的小表格将它呈现给用户:

```
import  AsciiTable  from  'https://deno.land/x/ascii_table/mod.ts';

...

const table = AsciiTable.fromJSON({
  title: `${data.city.name} Forecast`,
  heading: [ 'Time', 'Temp', 'Weather'],
  rows: forecast
})

console.log(table.toString()) 
```

保存并运行脚本，现在我们应该已经很好地格式化并呈现了我们所选城市未来 24 小时的预测:

```
.--------------------------------------------.
|              London Forecast               |
|--------------------------------------------|
|      Time       | Temp  |     Weather      |
|-----------------|-------|------------------|
| 23rd Jul, 19:00 | 17.8C | light rain       |
| 23rd Jul, 22:00 | 16.8C | light rain       |
| 24th Jul, 1:00  | 16.0C | broken clouds    |
| 24th Jul, 4:00  | 15.6C | light rain       |
| 24th Jul, 7:00  | 16.0C | broken clouds    |
| 24th Jul, 10:00 | 18.3C | scattered clouds |
| 24th Jul, 13:00 | 20.2C | light rain       |
| 24th Jul, 16:00 | 20.2C | light rain       |
'--------------------------------------------' 
```

## 完整的代码列表

这是一个非常紧凑的脚本，但下面是完整的代码清单:

```
import { parse } from "https://deno.land/std@0.61.0/flags/mod.ts";
import {
  fromUnixTime,
  format,
} from "https://deno.land/x/date_fns@v2.15.0/index.js";
import AsciiTable from "https://deno.land/x/ascii_table/mod.ts";

const args = parse(Deno.args);

if (args.city === undefined) {
  console.error("No city supplied");
  Deno.exit();
}

const apiKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

const res = await fetch(
  `https://api.openweathermap.org/data/2.5/forecast?q=${args.city}&units=metric&appid=${apiKey}`,
);
const data = await res.json();

interface forecastItem {
  dt: string;
  main: { temp: number };
  weather: { description: string }[];
}

const forecast = data.list.slice(0, 8).map((item: forecastItem) => [
  format(fromUnixTime(item.dt), "do LLL, k:mm", {}),
  `${item.main.temp.toFixed(1)}C`,
  item.weather[0].description,
]);

const table = AsciiTable.fromJSON({
  title: `${data.city.name} Forecast`,
  heading: ["Time", "Temp", "Weather"],
  rows: forecast,
});

console.log(table.toString()); 
```

## 摘要

现在，您有了自己的 Deno 命令行程序，可以为您提供未来 24 小时的天气预报。通过学习本教程，您现在应该熟悉如何启动一个新程序，从标准库和第三方导入依赖项，以及授予脚本权限。

那么，已经尝到了为 Deno 编写程序的滋味，接下来你应该去哪里呢？我强烈建议通读一下手册以了解更多关于各种命令行选项和内置 API 的信息，同时关注 SitePoint 以获取更多 Deno 内容！

## 德诺基金会

跟上德诺的步伐。我们的 Deno Foundations collection 帮助您迈出进入 Deno 世界以及更远的第一步，并且我们会不断增加它。我们将为您带来成为职业选手所需的教程。您可以随时参考我们的索引，因为它在我们对 Deno 的介绍结束时更新:

➤ [德诺基金会](https://www.sitepoint.com/deno-guide#foundations)

## 分享这篇文章