# 自定义事件和 Ajax 友好页面就绪检查

> 原文：<https://www.sitepoint.com/custom-events-ajax-friendly-page-ready-checks/>

不久前，我开发了一个 Chrome 扩展，允许用户从 Trello 导出列表。你可以在这里看到关于如何做到这一点的简短系列[。不过，这个扩展还有改进的空间。](https://www.sitepoint.com/series/how-to-build-a-trello-chrome-extension/)

![](img/67f41ace5b599be983805420ea98f489.png)

例如，检查板子是否已经改变——考虑到 Trello 是一个不简单的事情，而且 URL 通过不可检测的 pushstate 改变。我们还希望它支持打开多个 Trello 标签，所以只检查网址是不行的。如果我们能够以某种方式确保缓慢的页面加载不会影响“页面就绪”状态，这也是很好的——在最初的扩展中，由于 Trello 的“过度 ajax 化”,页面在内容加载之前就“就绪”了——即使是板的区域也是通过 Ajax 加载的，因此，事件不容易附加到 Trello UI 的核心 DOM 元素。

出于这个目的，也为了让我将来为 Trello 开发扩展更加容易，我决定构建一个 TrelloUI 库，它将为我处理这个特殊的问题。TrelloUI 库将来会扩展其他功能，但是现在，让我们构建我们的“就绪检查器”。

## 我们正在建造的东西

我们将构建一个助手库，我们可以从我们的 Chrome 扩展(或者 Firefox 插件，如果你愿意的话)调用它，它允许我们为事件`trelloui-boardready`的`document`对象附加一个事件监听器。一旦该事件被触发，我们就知道电路板已经加载，我们可以自由地将事件附加到电路板 UI 元素。最后，我们将通过为其他用例添加更多事件来改进它，这样我们就可以在将来广播任何我们想要的事件。

![Antenna mast sign](img/239d95f827436dec609ca67f5cb2728d.png)

我们将在 Chrome 扩展上测试这个库，把它作为一个内容脚本。你可以在 ChromeSkel_a 的新版本上测试它，ChromeSkel _ a 是一个现成可用的框架 Chrome 扩展，或者在我们在之前的系列的[中构建的 Trello Helper 版本上测试。](https://www.sitepoint.com/series/how-to-build-a-trello-chrome-extension/)

你所需要的只是一个编辑器和一个激活了开发者模式的 Chrome(进入 chrome:extensions 并勾选“开发者模式”框)。

## 建造图书馆

让我们开始建造吧。通过激活 Chrome 的开发模式并建立一个测试项目来准备你的环境。

### 内容脚本

Trello 的客户端库需要 jQuery，所以我们将把它包含在我们的项目中。下载一个[最近的副本](https://jquery.com/download/)(最好是版本 2+)并将其作为内容脚本。创建一个名为`trelloui.js`的文件和另一个名为`main.js`的文件，然后也包含它们。您的内容脚本块应该如下所示:

```
"content_scripts": [
        {
            "matches": ["https://trello.com/b/*"],
            "js": [
                "lib/jquery-2.1.1.min.js",
                "lib/TrelloUI/trelloui.js",
                "scripts/main.js"
            ],
            "run_at": "document_idle"
        }
```

你可以选择你想要的文件夹结构——我喜欢把“库”放在“库”里，但这并不重要。

### 拔靴带

在`trelloui.js`中，我们从创建一个新的“类”开始。

```
var TrelloUI = function () {};
```

这只是一个函数，我们将用一些方法属性来扩展它。

### 正在检查结束状态

首先，让我们想想结束状态是什么——`trelloui-boardready`事件什么时候被触发？我们需要有一种方法来检查板子是否已经加载并变得可见，然后通过事件让文档知道它发生了。但是我们需要确保一旦显示板出现，检查就停止，否则我们将永远运行间隔检查器。将以下内容添加到`trelloui.js`:

```
TrelloUI.prototype._checkState = function () {
    return $('#board').hasClass('trelloui-boardready');
};
```

简单——我们添加一个函数来检查 board 元素是否有给定的类。我们可以在事件触发后添加这个类；我们以后再做。但是一次检查这个类对我们没有太大的好处——如果我们想确保这个扩展的功能在页面重载和电路板改变后仍然存在，我们需要不断地检查。我们把上面的改成:

```
var TrelloUI = function () {
    setInterval(this._checkState.bind(this), 1000);
};

TrelloUI.prototype._checkState = function () {
    if (!$('#board').hasClass('trelloui-boardready')) {
        this._registerEvents();
    }
};
```

这利用了“构造函数”,当我们在主代码中调用`new TrelloUI`时，让 TrelloUI 自动设置检查 body 元素是否每秒包含我们想要的类的时间间隔。如果没有，我们调用`_registerEvents`(一个我们还没有写的函数)来添加类，并在显示板一出现就调度事件。

注意，我们使用`this._checkState.bind(this)`而不是`this._checkState`，因为`this`在 setInterval 期间得到了[的分离。](https://stackoverflow.com/questions/15059928/setinterval-invoked-from-inside-an-object-javascript)

### 构建新事件

你可以在这篇文章的[中阅读更多关于创建自定义事件的细节。在我们的例子中，我们将只使用最基本的设置。将构造函数更改为:](https://www.sitepoint.com/javascript-custom-events/)

```
var TrelloUI = function () {
    var eventDefaults = {
        bubbles: true,
        cancelable: true
    };

    this.possibleEvents = {
        boardEvent: new Event('trelloui-boardready', eventDefaults)
    };

    setInterval(this._checkState.bind(this), 1000);
};
```

我们使用`eventDefaults`为我们以后可能想要定义的任何其他事件设置默认值，这样我们就不必不断重复自己。Bubbles 表示事件从它触发的元素冒泡到父元素。可取消意味着如果用户需要，可以用`event.stopPropagation`停止。这些标志现在对我们来说几乎没有任何意义，但是它们是很好的缺省值。然后，我们定义一个内部的`possibleEvents`属性，它包含我们的小实验可以调度的所有可能的事件。

### 选项和构造函数

我们提到以后可能要实现其他事件，所以让我们确保它很容易实现:

```
var TrelloUI = function (options) {
    this._defaultOptions = {
        dispatchBoardReady: false
    };
    this.options = jQuery.extend({}, this._defaultOptions, options);

    var eventDefaults = {
        bubbles: true,
        cancelable: true
    };

    this.possibleEvents = {
        boardEvent: new Event('trelloui-boardready', eventDefaults)
    };

    setInterval(this._checkState.bind(this), 1000);
};
```

这里我们希望 TrelloUI 在电路板准备好的时候调度一个事件，但是我们也考虑到了我们将来实现其他事件的潜在需求。但是默认情况下检查所有的事件将会非常耗费资源。(嗯，不尽然——事实上，除了最弱的计算机之外，所有的计算机都可以成功地处理它们，即使我们处理数百台计算机，但是当我看到网页和扩展程序仅仅因为闲置就占用了 2GB 以上的 RAM 时，我倾向于避免认为资源是理所当然的。)

对于设置和传入选项的简单合并，我们使用 jQuery 的[扩展](https://api.jquery.com/jquery.extend/)。

此设置允许我们执行以下操作来使用 TrelloUI:

```
var tui = new TrelloUI({
        dispatchBoardReady: true
    });
```

在这里，我们告诉 TrelloUI 实例化并留意它触发 boardReady 事件的可能性。如果我们不给它这个选项，构造函数中的默认值将阻止它尝试，以节省资源。

### 事件触发

![Rocket](img/698c1bcddc2ad7cfadd868b375fcc455.png)

最后，让我们构建事件触发功能。

```
TrelloUI.prototype._registerEvents = function () {

    var current = this;

    if (this.options.dispatchBoardReady) {
        var boardInterval = setInterval(function () {
            var board = $('#board');
            if (board && !$(board).hasClass(current.possibleEvents.boardEvent.type)) {
                document.dispatchEvent(current.possibleEvents.boardEvent);
                $(board).addClass(current.possibleEvents.boardEvent.type);
                clearInterval(boardInterval);
            }
        }, 100);
    }
};
```

我们来分解一下。首先，我们将`this`作为局部变量的别名，这样我们就可以在下面的闭包中轻松地使用它。然后，每隔 100 毫秒定义一个时间间隔，如果棋盘元素存在，则首先获取棋盘元素。如果有，并且如果主体仍然没有我们想要的类，我们就调度事件，添加类，并清除间隔。否则，间隔重复。

最后，让我们改进`_checkState`,使其忽略选项是否设置为 false 的检查:

```
TrelloUI.prototype._checkState = function () {
    if (this.options.dispatchBoardReady) {
        if (!$('#board').hasClass(this.possibleEvents.boardEvent.type)) {
            this._registerEvents();
        }
    }
};
```

### 附加事件

如果您现在将以下内容添加到您的`main.js`脚本中，您应该能够将其加载到 Chrome 中，并在您的控制台中看到“Board is ready ”:

```
var tui = new TrelloUI({
        dispatchBoardReady: true
    }
);

document.addEventListener('trelloui-boardready', function() {
    console.log("Board is ready!");
});
```

但是…这仍然不足以让我们从之前的[系列](https://www.sitepoint.com/series/how-to-build-a-trello-chrome-extension/)中延伸出来。在那里，我们与列表交互。并在后列出装载*的板子。显然，我们需要一个`listsReady`事件。*

首先，我们向选项和可能事件列表中添加一个新事件:

```
var TrelloUI = function (options) {
    this._defaultOptions = {
        dispatchBoardReady: false,
        dispatchListsReady: false
    };
    this.options = jQuery.extend({}, this._defaultOptions, options);

    var eventDefaults = {
        bubbles: true,
        cancelable: true
    };

    this.possibleEvents = {
        boardEvent: new Event('trelloui-boardready', eventDefaults),
        listsEvent: new Event('trelloui-listsready', eventDefaults)
    };

    setInterval(this._checkState.bind(this), 1000);
};
```

然后，我们通过添加以下块来更新`_registerEvents`:

```
if (this.options.dispatchListsReady) {
        var listsInterval = setInterval(function() {
            var lists = $('.list');
            if (lists.length > 0 && !$(lists[0]).hasClass(current.possibleEvents.listsEvent.type)) {
                document.dispatchEvent(current.possibleEvents.listsEvent);
                $(lists[0]).addClass(current.possibleEvents.listsEvent.type);
                clearInterval(listsInterval);
            }
        }, 100);
    }
```

如果有列表，并且第一个列表还没有指示就绪的类，则调度该事件并将该类添加到第一个列表中。

最后，让我们通过添加一个新的块来再次调整`_checkState`:

```
if (this.options.dispatchListsReady) {
        var lists = $('lists');
        if (!lists.length || !$(lists[0]).hasClass(this.possibleEvents.listsEvent.type)) {
            this._registerEvents();
        }
    }
```

### 履行

现在实现这些事件就像在主脚本中声明以下内容一样简单:

```
var tui = new TrelloUI({
        dispatchBoardReady: true,
        dispatchListsReady: true
    }
);

document.addEventListener('trelloui-boardready', function() {
    console.log("Board is ready!");
});
document.addEventListener('trelloui-listsready', function() {
    console.log("Lists are ready!");
});
```

现在每次你换板的时候，你都应该被告知板和列表准备好了。添加您的逻辑来代替`console.log`语句，并创造一些奇迹！

## 结论

在这个简短的教程中，我们构建了一个简单的库，用于与 Trello 的 UI 进行交互，这是一个助手，它触发各种“就绪”事件，可以帮助我们检测“ajaxy”部分何时完成加载，以便我们可以正确地与它们进行交互。

我们仍然可以做很多事情来改进这个“库”——例如，移除 jQuery 依赖性，或者将在`_checkState`和`_registerEvents`中相似的代码提取到可以在它们之间共享的东西中。不过现在，它完全符合我们的需求——让我们知道 Trello UI 什么时候可以调整了！想帮忙吗？欢迎在 [Github repo](https://github.com/Swader/TrelloUI) 上提出请求！

## 分享这篇文章