# 如何用 WRLD 构建室内办公室地图

> 原文：<https://www.sitepoint.com/how-to-build-an-indoor-office-map-with-wrld/>

*本文是与 [WRLD](https://www.wrld3d.com/?utm_source=sitepoint&utm_medium=referral&utm_campaign=sp-a3-how-to-build-an-indoor-office-map-with-wrld&utm_content=text-sp-home) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

我们就要在 WRLD 平台结束我们的系列了。到目前为止，我们已经了解了如何在外部地图中使用 WRLD。在本教程中，我们将设计和使用一个室内地图(也称为平面布置图，如果你要像德怀特·施鲁特一样迂腐的话)。正如你可能知道的，我认为办公室是这个主题的一个很好的主题。让我们以一段引言开始:

> 我会离开这家公司吗？听着，我很忠诚。事实上，我觉得我在这里得到报酬的部分原因是我的忠诚。但是如果有其他地方更重视忠诚的话…我会去他们最重视忠诚的地方。*–德怀特·k·施鲁特*

在本教程中，我们将讨论以下主题:

*   创建室内地图
*   将地图导入 WRLD
*   突出显示特定房间
*   创建计划并循环执行

> 本教程的代码已经过现代版 Chrome 的测试。

## 创建室内地图

我们将尝试重建办公室，从*办公室*。我认为这将是一个有趣的挑战。我们将从安装一些软件开始。我在苹果电脑上，所以这些是如何在苹果电脑上操作的说明。显然，在 Windows 上要简单得多…

我们需要的第一个软件叫做 QGIS。你可以直接从他们的网站下载，但是 Mac 链接会链接到一个看起来很危险的地方。我更信任可以通过[自制软件](https://brew.sh/)安装的软件，因为我可以看到它在每一步都在做什么。如果你在 Mac 上，并且你还没有自制软件，我建议你安装它。完成后，您可以通过一些 Python 包开始 QGIS 安装:

```
brew install gdal
brew install python 
```

> 当我尝试安装`python`时，Homebrew 告诉我已经安装了 Python。但是，缺少了`pip`命令。我不得不重装`python`，用`brew reinstall python`；然后我不得不使用`brew unlink python && brew link python`重新链接它。在这之后，我有了`pip`命令。

您可以跟进以下问题:

```
pip install matplotlib
pip install psycopg2
brew tap osgeo/osgeo4mac
brew install qgis2 
```

> 所有这些作品，在写作的时候。这并不能保证在你读到这篇文章的时候它会起作用。你可能不得不求助于我前面提到的看起来很正式，但完全不可靠的 QGIS 下载网站。
> 
> 我从一个教程中了解到这个过程，关于[通过自制](http://usabilityetc.com/2016/06/how-to-install-qgis-with-homebrew/)安装 QGIS。

做完这些之后，你应该可以运行`qgis2`命令了…

![“Updates? I just installed this thing!”](img/1fb85077c93aaa11834441403b50940e.png)

你还需要安装一些插件。转到插件→管理和安装插件。安装 OpenLayers 插件和 Lat Lon 工具插件。

![All the plugins](img/38ea0bdead4224e3577dc44de5134ed1.png)

接下来，我们需要找出它在哪里。在节目中，据说办公室位于宾夕法尼亚州斯克兰顿的斯劳大道 1725 号。该建筑的实际位置实际上是在加利福尼亚州全景市萨蒂科伊街 13927 号。

确认地图位置的最简单方法(如果你记得这个地方看起来是什么样子的话)是在谷歌地图之类的东西中搜索地址，谷歌地图提供了街景模式。为了证明全景城市的位置是正确的，我在街景中搜索了那个地址，得到了这个:

![Looks like the office. Mostly.](img/961362f8d6e90f70e1d006e3acda5ba2.png)

现在，是时候开始创建地图了。通过转到 Web → OpenLayers 插件→ OpenStreetMap → OpenStreetMap 添加一个新的 OpenStreetMap 图层。你应该会看到一张缩小的世界地图。进入插件→ Lat Lon Tools →缩放至纬度、经度，输入你想要的坐标(本例中为:34.2107329，-118.4365886)。您可以将比例更改为 1:500，以便更仔细地查看。

![Looks about right](img/0fb0c9ba1b618d2ff9629a338a4db6cc.png)

接下来，单击栅格→地理配准器→地理配准器。这将弹出一个新的空窗口。单击文件→打开栅格以选择平面图。“什么平面图？”，你问。你可以通过搜索“办公室平面图”来下载。

我应该提到，你会被问到你需要什么样的坐标参考系统(CRS 或 SRS)。选择“WGS 84 /伪墨卡托”。我真的不明白为什么，但这是默认设置，而且似乎对我很有效！然后，平面图应该会显示在编辑器中。

![Working with the floor plan](img/65c752ba617d3a31804890d865d12c49.png)

我们需要将室内地图链接到室外地图。单击添加点(在地理配准器窗口中)，然后单击周界点(如建筑物的拐角)。然后单击“从地图画布”并从户外地图中选择相应的点。

重复几次。我知道从技术上来说，将室内地图与室外建筑形状 1:1 匹配可能不准确，但这就是我现在所做的。你也可以这样做。尽量做到准确。

当您对已映射的点感到满意时，单击“设置”→“变换设置”,使设置看起来像这样:

![The transformation settings](img/fcd0926772598be9fae48322a76231e7.png)

其中大部分是默认的，但目标 SRS 应该是 EPSG:3857。点击打开。然后单击绿色的播放按钮(这是开始地理配准按钮)。这将带你回到主 QGIS 窗口，除了现在平面图将覆盖室外地图。您可以在“层”面板中右键单击平面图，然后单击“属性”。在那里，你可以将混合模式调整到不那么刺耳的程度。我喜欢变亮混合模式。

![Blend modes](img/55e32aa4c18bc01a5f6505b64f2d15c6.png)

现在，我们需要开始在平面图上绘制特征。单击图层→创建图层→新建 Shapefile 图层。设置类型为多边形，选择 EPSG 相同的 SRS:3857，在属性列表中添加`type`、`name`和`highlight`属性。“文本数据”和“80”的缺省值对于类型和名称属性是合适的，但是高亮属性需要设置为整数。长度字段中的值不需要更改。单击“确定”并为形状文件命名。

当你在图层面板中看到它时，双击它并将透明度设置为 50%，这样你仍然可以看到下面的地图。形状层需要是最上面的层，所以如果它不在那里，就拖动它。接下来，单击切换编辑按钮(铅笔)，然后单击添加功能按钮(看起来像一个绿色博客，上面有一个黄色图标)。

描绘平面图的边界。当你完成后，右键点击你做好的形状，给它一个类型`building_outline`。这是 WRLD 渲染室内地图的边界。再次单击添加要素按钮，开始追踪墙壁、窗户、门和房间等物体。这些不应该重叠。当你描绘了一个片段，给它相应的类型，比如`wall`、`window`、`door`或者`room`。您通常可以将“名称”字段留空，但是如果您指定了房间名称，WRLD 将会显示该名称。如果您计划使用任何突出显示，您可以在`highlight`属性的`room`特性中添加一个`1`。

![Drawing features](img/339c699d85bb879d20e523ec5cd0d610.png)

如果你画的是房间，而不是组成房间的墙壁、窗户和门，那就不会那么混乱了。这里有一个你可以追踪的元素(及其类型)的完整列表。继续追踪要素，直到获得所需的所有细节。

在将地图导入 WRLD 之前，我们需要确保要素 id 是唯一的。如果您已经给出了唯一的 id，那么您就万事俱备了。否则，通过单击图层→打开属性表打开要素列表，并为每个要素指定一个 ID。顺序不重要。

右键单击 shapefile 图层，然后单击另存为。要上传您的建筑，您现在需要将 CRS 更新为 EPSG: 4326，并将其导出为 geojson 文件，而不是 shapefile。转换为 4326 会将格式从伪墨卡托更改为墨卡托，这意味着您构建的平面图的点现在存储在纬度和经度中 world 室内地图设计人员在将地图转换为 3D 模型时，使用该纬度和经度将地图正确地放置在世界中。将文件保存在某个位置，打开文本编辑器，打开保存形状图层文件的文件夹。创建一个新的 JSON 文件，名为`main.json`:

```
{
    "id": "the-office",
    "name": "The Office",
    "owner": "Dunder Mifflin",
    "location": {
        "type": "Point",
        "coordinates": [-118.4365886, 34.2107329]
    },
    "levels": [
        {
            "id": "the-office-1",
            "name": "1",
            "readable_name": "First floor",
            "z_order": 0,
            "filename": "floor-plan.geojson"
        }
    ]
} 
```

> 这来自 main.js

`id`字段只是让你识别地图的东西。你可以把任何东西放在那里。Name 是地图的可读名称。“业主”是您公司(或声称拥有 WRLD 大楼的公司)的名称。位置是地图上室内地图所在的点。那个数组是`[longitude, latitude]`。

然后，有一系列的水平。每栋建筑可以有多个楼层。我们只设计了一个，所以这是我们在数组中定义的那个。将这两个文件放在一个文件夹中，并从该文件夹创建一个 ZIP 文件。然后，前往[https://mapdesigner.wrld3d.com/indoormap/latest](https://mapdesigner.wrld3d.com/indoormap/latest/?utm_source=sitepoint&utm_medium=referral&utm_campaign=sp-a3-how-to-build-an-indoor-office-map-with-wrld&utm_content=text-sp-mapdesigner-wrld3d-com-indoormap-latest%0A)开始创建地图！

单击“新建室内地图”,并将其命名为。然后，使用纬度和经度搜索合适的建筑。当你看到你想要的建筑，点击它，然后点击认领建筑。请确保您选择下载平面图，因为这将使在 QGIS 中跟踪它变得更容易。接下来，跳到上传地图步骤，上传您创建的 ZIP 文件。需要一些时间来处理。一旦成功，你应该在地图上看到一个按钮点击。点击它会带你进入大楼…

![Inside the building](img/c77d13ffcf9362b9e6dce20679ac04fa.png)

如你所见，我花了一段时间才弄明白，`location`坐标是“经度，纬度”而不是“纬度，经度”。当您对室内地图感到满意时，请点按“公开地图”。

> 这里有一个更长、更详细的创建这些地图的指南。我发现它的一些部分没有帮助，但你的里程可能会有所不同…

## 与室内地图互动

您的地图可能需要一段时间才能获得批准。同时，让我们看看如何与室内地图进行交互。

> 对于教程的这一部分，我们将使用托管在 CodePen 上的代码。代码笔是演示性的，使用本文中的代码摘录和步骤可以获得最佳结果。如果你想要一个本地设置，在之前的教程中寻找如何设置的细节。

让我们首先创建一个地图，并进入一个预制的建筑:

```
const map = L.Wrld.map("map", "f24b71dd92ab9e38cf4aa53806ab813d", {
    center: [56.459801, -2.977928],
    zoom: 15,
    indoorsEnabled: true,
})

map.indoors.on("indoormapenter", () => {
    map.indoors.setFloor(2)
    map.setView([56.459984, -2.978238], 20)
})

map.on("initialstreamingcomplete", () => {
    map.indoors.enter("westport_house")                
}) 
```

> 这是来自 [CodePen](https://codepen.io/assertchris/pen/VQRMqj)

在本例中，我们将移动到 WRLD 办公室的坐标。一旦地图完成加载(通过`initialstreamingcomplete`)，我们就要求地图“进入室内”。在`indoormapenter`事件中，我们将选择的楼层设置为`2`，并进一步放大地图。

一旦您提交的地图获得批准，这也将适用于这些地图。

我们可以通过监听点击事件来与功能交互:

```
map.indoors.on("indoorentityclick", (event) => {
    map.indoors.clearEntityHighlights()
    map.indoors.setEntityHighlights(event.ids[0], [255, 0, 0, 128])
}) 
```

> 这是来自 [CodePen](https://codepen.io/assertchris/pen/VQRMqj)

`indoorentityclick`接收一个事件，该事件包含我们正在点击的特性的 ID。我们可以使用`clearEntityHighlights`和`setEntityHighlights`事件来切换我们想要突出显示的任何自定义颜色。

大概是时候再引用一句了…

> 我偶然看到了婚礼傲客。买了一张《灰熊人》的票，进错了影院。一个小时后，我想我走错了剧院，但我一直在等。因为这就是熊攻击的原因…它们会在你最意想不到的时候出现。–*德怀特……再次*

## 创建可重复的高亮图案

让我们做个实验。让我们制作一个“记录”按钮，记录我们在地图上的点击；然后回放给我们。我们需要一个录音和播放按钮，以及一系列的点击…

```
<button id="record">record</button>
<button id="play">play</button> 
```

> 这是来自 [CodePen](https://codepen.io/assertchris/pen/GQeMem)

现在，让我们用点击事件连接这些按钮:

```
map.indoors.on("indoorentityclick", (event) => {
    map.indoors.clearEntityHighlights()
    map.indoors.setEntityHighlights(event.ids[0], [255, 0, 0, 128])

    clicks.push({
        time: new Date(),
        id: event.ids[0]
    })
})

let recording = false
let started = null
let clicks = []

const record = document.querySelector("#record")
const play = document.querySelector("#play")

record.addEventListener("click", () => {
    if (recording) {
        recording = false
        record.innerText = "record"
    } else {
        recording = true
        record.innerHTML = "stop"
        started = new Date()
        clicks = []
    }
})

play.addEventListener("click", () => {
    map.indoors.clearEntityHighlights()

    for (let click of clicks) {
        setTimeout(() => {
            map.indoors.clearEntityHighlights()
            map.indoors.setEntityHighlights(click.id, [255, 0, 0, 128])  
        }, click.time.getTime() - started.getTime())
    }
}) 
```

> 这是来自 [CodePen](https://codepen.io/assertchris/pen/GQeMem)

我们先来看一下记录按钮事件。单击时，如果记录还没有开始，我们将按钮的文本值更改为`stop`，并清除点击数组。我们还捕获单击 record 按钮的时刻，这样我们就可以相对于它来计时我们的事件。

如果我们再次点击它，我们可以重置按钮文本值，并停止记录。

然后，当我们点击播放按钮时；我们清除所有突出显示的功能，并开始循环浏览“记录”的事件。它们在一个数组中，所以我们可以使用一个`for...of`循环。我们通过获取点击的时间并从中减去`started`时间来为每个点击事件设置超时。所有超时实际上是同时创建的，但它们是相对于记录开始后多长时间触发的。

最后，当特性被点击时，我们需要捕获点击事件。因此，我们通过将点击事件添加到`clicks`数组来劫持我们之前的`indoorentityclick`事件监听器。试一试，录下一些点击，然后回放。挺上瘾的…

![Highlight features](img/0f13335756fcb1e979ad97b38850ac8c.png)

## 创建时间表

让我们扩展这个想法，以包括一个可配置的时间表。假设 WRLD 的某个人白天在办公室里四处走动。或者，想象一下(符合我们的主题)德怀特想要跟踪吉姆的行动…

如果他知道吉姆的时间表，他可以这样做:

```
const schedule = [
    {
        "start": "08:00",
        "duration": "00:30",
        "id": "0003"
    },
    {
        "start": "08:30",
        "duration": "00:30",
        "id": "0004"
    },
    {
        "start": "09:15",
        "duration": "00:45",
        "id": "0028"
    },
    {
        "start": "10:00",
        "duration": "01:00",
        "id": "Meeting Room"
    },
    {
        "start": "12:00",
        "duration": "01:30",
        "id": "0039"
    }
]; 
```

> 这是来自 [CodePen](https://codepen.io/johnbell84/pen/PRqMYg?editors=1111)

我们可以创建一个函数来计算我们正在使用的时间格式的分钟数(即`01:30` → `90`):

```
const timeToMinutes = (time) => {
    let holder = new Date();

    holder.setHours(0);
    holder.setMinutes(0);
    const start = holder.getTime();

    const parts = time.split(":");

    holder.setHours(parseInt(parts[0], 10));
    holder.setMinutes(parseInt(parts[1], 10));
    const end = holder.getTime();

    return (end - start) / 1000 / 60;
};

const schedule = [
    // ...
]

for (let event of schedule) {
    console.log(timeToMinutes(event.duration))
} 
```

> 这是来自 [CodePen](https://codepen.io/johnbell84/pen/PRqMYg?editors=1111)

这将依次打印出`30`、`30`、`45`、`60`和`90`。然后我们需要将这些分钟数加到开始时间上，算出结束时间是:

```
const timeToMinutes = (time) => {
    // ...
}

const startFromTime = (time) => {
    const parts = time.split(":");

    let holder = new Date();
    holder.setSeconds(0);
    holder.setHours(parseInt(parts[0], 10));
    holder.setMinutes(parseInt(parts[1], 10));

    return holder;
};

const endFromStartAndMinutes = (start, minutes) => {
    return new Date(start.valueOf() + (minutes * 1000 * 60));
};

const schedule = [
    // ...
]

for (let event of schedule) {
    const start = startFromTime(event.start)
    const minutes = timeToMinutes(event.duration)
    const end = endFromStartAndMinutes(start, minutes)

    console.log(start, end)
} 
```

> 这是来自 [CodePen](https://codepen.io/johnbell84/pen/PRqMYg?editors=1111)

…这将记录相对于当天的开始和结束时间。您可能希望开始和结束时间相对于固定的一天，所以您需要修改`startFromTime`和`endFromStartAndMinutes`来考虑一天。

给定这些开始和结束时间，我们可以对室内地图进行编码，以显示该员工现在所在的房间:

```
const schedule = [
    // ...
]

setInterval(() => {    
    map.indoors.clearEntityHighlights();

    for (let event of schedule) {
        const start = startFromTime(event.start);
        const minutes = timeToMinutes(event.duration);
        const end = endFromStartAndMinutes(start, minutes);

        const now = new Date();

        if (now >= start && now <= end) {
            console.log("Time is now " + now.toLocaleTimeString() + 
                        ", in the " + start.toLocaleTimeString() +
                        " to " + end.toLocaleTimeString() + 
                        " interval. Highlighting entity " + event.id);
            map.indoors.setEntityHighlights(event.id, [255, 0, 0, 128]);
        }
    }
}, 1000); 
```

> 这是来自 [CodePen](https://codepen.io/johnbell84/pen/PRqMYg?editors=1111)

通过将当前时间与每个事件的开始和结束时间进行比较；我们可以知道员工当前参加了哪个活动(并相应地突出显示该功能)。

> 如果您没有看到任何突出显示的实体，您可能没有在查看代码的确切时间安排任何实体。尝试调整该代码笔的计划，并打开控制台以查看哪个实体应该突出显示。我还从前面的示例中删除了录制/播放代码，以便更容易看到这个演示中发生了什么。

想想这个可以如何扩展。你可以添加每个人的时间表来创建一个虚拟的活动预订系统。或者，您可以创建一个动画可视化来查看是否有任何冲突。这也是应用程序的另一个出发点。

## 摘要

我们花了很多时间学习创建室内地图的细节。WRLD 并不是唯一理解 QGIS 制作的地图的平台，所以如果你想做各种各样的地图制作，这是一项很好的技能。

我们还了解了 QGIS 制作的地图是如何在 WRLD 平台中导入和使用的。我们可以定义包含大量细节的地图，并与它们实时互动。一旦你知道要安装什么以及如何绘制特征，室内制图就非常简单了。

花些时间想一想你可以如何使用它，来模拟你的家或办公室。也许你可以使用室内地图，结合其他传单功能，创建热流图或每个房间的能源使用图。也许您可以创建自己的办公室日程安排应用程序。

> 我应该提一下:如果你想制作定制室内地图，但又不想自己设计，WRLD 提供室内地图服务，这是要收费的。我还没有尝试过更高层次的服务和功能，但 WRLD 似乎在如何将 3D 室内地图公开(如体育场和机场)和保密(如医院和学校)方面非常灵活。根据这个教程，他们还告诉我，他们正在寻求简化室内地图制作的文档和工作流程。目前有点*详细*，但是如果你问我的话，这个结果很值得。想象一下，如果做起来更容易…

我们已经到了 WRLD 之旅的终点。它已经成为我最喜欢的嵌入式服务之一。工作起来很有趣，可以非常详细和灵活。更何况我还没花一分钱去用。我希望你在阅读这些教程的时候和我在写的时候一样开心。让我知道你在 Twitter 上建了什么[。你也可以带着问题联系](https://twitter.com/assertchris) [WRLD](https://twitter.com/wrld3d) ，或者向他们展示你的作品。

## 分享这篇文章