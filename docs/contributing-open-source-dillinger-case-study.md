# 为开源做贡献:迪林杰案例研究

> 原文：<https://www.sitepoint.com/contributing-open-source-dillinger-case-study/>

如果你曾试图找一份程序员的工作，你一定注意到有多少公司要求 GitHub 简介、积极的开源努力和你的工作的普遍扩散。

你做的越多，你就越有价值。不幸的是，他们太过于看重数量而不是质量。

但是就像拿破仑·希尔说的——[如果你不能做伟大的事情，就用伟大的方式做小事情](http://www.quotery.com/if-you-cannot-do-great-things-do-small-things-in/)。

## 问题是

在这篇文章中，我们将为一个流行的开源 [MarkDown](http://daringfireball.net/projects/markdown/) 编辑器——[迪林杰](http://dillinger.io)做贡献。Dillinger 是一个在线文本编辑器，它可以让你输入 MarkDown，同时在窗口的另一部分预览它，显示它就像呈现在 HTML 中一样。作为 SitePoint 的 PHP 编辑器，我经常使用它，但我对它的一个不满是“显示 HTML”功能，当选择它时，会弹出一个简单的`div`元素，其中包含我们减价内容的 HTML 版本(文本形式)。为什么这是一个问题？因为我需要手动选择 div 中的文本，如果内容足够长，我需要在向下滚动时这样做。不用说，这很快变得相当乏味。

![](img/c7b4fe805421cc7110c17bbdef712dfc.png)

## 解决方案

理想的解决方案是添加一个“复制 HTML”按钮，简单地将代码放入剪贴板，不需要任何选择。然而，这将需要闪存，在当今荒谬的反闪存世界中，这根本不是一个选项。相反，让我们选择妥协。

我们将把包含我们的 HTML 代码的`div`变成一个文本区域，默认情况下它处于全选模式。然后，任何人只需按 CTRL+C，HTML 内容就在剪贴板中了。即使选择/焦点丢失，也很容易重新选择，只需将光标放在字段内并按 CTRL+A。

### 准备

在为开源项目做贡献时，原作者通常会留下如何做的提示和技巧。有时它在项目的自述文件中，有时会创建一个名为 CONTRIBUTING 的单独文件。在迪林杰上，没有这样的指令(目前)。在中只描述了一个[安装程序，这对我们来说已经足够了。](https://github.com/joemccann/dillinger#installation)

首先，我们首先需要分叉项目。分叉，如果你不熟悉这个术语，是指将一个存储库复制到你自己的帐户中，以便对其进行扩展或制作你自己的版本的过程。当您创建一个 fork 并向其中添加内容时，您可以选择向原作者发送一个 Pull 请求。这让作者知道您已经提出了项目的更新，并且希望它包含在主存储库中，这样该存储库的所有未来分支也包含您新添加的特性/修复。我们分叉回购，现在也可以在这里找到[。尽管到本文发表时，本教程结束时的拉请求将被接受和集成，但请随意跟进。尽管如此，这是一个很好的练习，在当地成立迪林杰！](https://github.com/Swader/dillinger)

在继续之前，请确保您已经安装了最新的节点。

使用命令行，将`cd`放入保存项目和运行安装步骤的文件夹中。我们不会使用插件，所以没有必要配置它们。现在，当你访问 [http://localhost:8080](http://localhost:8080) 时，迪林杰应该正在本地运行。

### 将您的回购协议与原始协议相连接

如果您打算以后黑掉 Dillinger，您可能应该设置一个原始回购的上游远程。这样做是为了让您可以频繁地从原始项目中获取新的更新。在应用程序的文件夹中执行以下操作:

```
git remote add --track master upstream [GIT URL HERE]
```

这在 humanese 中的意思是“在[URL]为这个项目设置另一个代码源，并跟踪它的主分支”。必要时，使用 fetch 命令(`git fetch upstream`)获取代码，然后您可以使用 merge 或 rebase 命令将其合并到您自己的项目中:

```
git merge upstream/master

# or

git rebase upstream/master
```

Git rebase 会覆盖您的分支的历史，这是这种情况下唯一的区别，但实际上是大多数开发人员的首选，因为它允许干净的 pull 请求——干净的历史更具可读性。像[这个帖子](http://blog.bleeds.info/sofa/_design/sofa/_list/post/post-page?startkey=%5B%22Maintaining-a-Fork-With-Git%22%5D)说的:

> `git rebase <upstream>`命令倒回到`<upstream>`和当前分支的共同祖先点，并在最新的`<upstream>`之上重放当前分支的变化。

换句话说，只要有可能，就使用 rebase。

我们现在并不需要这些命令，只是将它们放在手边，以备将来参考如何**更新 GitHub repo 的 fork**。

### 分支和固定

为了对项目做出贡献，我们首先需要创建一个新的分支，因为

1.  黑掉主分支是愚蠢的
2.  GitHub 有每个分支一个拉请求的策略

我们现在就开始吧。

```
git branch showhtml-fix
git checkout showhtml-fix
```

我们现在在自己的分公司。开始黑吧！

在您最喜欢的代码编辑器中打开项目(我使用 PhpStorm，因为它基本上是内置了 PHP 支持的 WebStorm ),并查看以下相关文件:

*   `views/nav.esj`
*   `public/js/dillinger.js`

`.ejs`是 Express view 模板的扩展，您应该不会对它不熟悉。

在`nav.ejs`中，你会在靠近顶部的地方找到下面的代码:

```
 <div id="myModalBody" class="modal-body">
        <pre>
          <div id="myModalBody" class="modal-body">
          </div>
        </pre>
    </div>
```

这是当我们点击“显示 HTML”时显示 HTML 文本内容的模态窗口。当它出现在 Dillinger 中时，您可以通过单击模态窗口上的 Inspect Element 自己找出这一点。在查看了整个项目中“#myModalBody”的其他用法之后(PhpStorm 和 WebStorm 有一个“在路径中查找”选项，可以在整个项目中搜索短语的用法)，我们得出结论，它的唯一用法是 Show HTML，因此，我们可以安全地更改它。将其更改为以下内容:

```
 <div id="myModalBody" class="modal-body">
        <textarea id="modalBodyText"></textarea>
    </div>
```

另外，在`dillinger.js`中找到函数`showHTML`，替换函数`_doneHandler`:

```
 function _doneHandler(jqXHR, data, response){
      // console.dir(resp)
      var resp = JSON.parse(response.responseText)
      $('#myModalBody').text(resp.data)
      $('#myModal').modal()
    }
```

有了这个内容:

```
 function _doneHandler(jqXHR, data, response){
            var resp = JSON.parse(response.responseText)
            $('#modalBodyText').val(resp.data);
            $('#myModal').modal()
        }
```

刷新，现在我们有…这个？

![](img/1ea152955c3918af4892d2940f9b54d2.png)

不是我们想要的，是吧？没关系，我们可以在文本字段上“检查元素”,并做一些动态修改，直到我们得到满意的结果。

我们做的第一件事，是给文本区的宽度和高度 100%。

![](img/d7a65e78fdb61982011ec8b3832bfff6.png)

嗯，这不是我们想要的。这是因为 height 是从父元素继承的——100%表示父元素高度的 100%。我们还需要编辑包含 div 的样式。

我们给它一个绝对位置，这样我们就可以在模态窗口中绝对定位它。然后，我们给它一个 565 像素的宽度，因为整个模态窗口的宽度是固定的 600 像素，565 像素非常合适。为了将它从理论上的模体顶部拉伸到底部，我们使用固定值而不是百分比，确保我们只定义了从边缘的偏移，中间的所有内容都会自动填充。我们给它的上限值为 45px(足以显示模态的标题、关闭按钮和分隔线)，下限值为 10px，足以模拟一个漂亮的边距。突然，我们有了这个:

![](img/7e4ff71c5c146431bae674b27ed5959b.png)

如果你愿意，可以在文本区添加`background-color: transparent`来去掉红色背景。

现在我们知道了需要对 CSS 做什么来得到我们想要的，让我们把它实现到项目的代码中。打开`modals.styl`(。styl 是一个[手写笔](http://learnboost.github.io/stylus/)文件)并粘贴在下面的 CSS 中:

```
#myModalBody {
    position: absolute;
    width: 565px;
    top: 45px;
    bottom: 10px;
}

#modalBodyText {
    width: 100%;
    height: 100%;
    background-color: transparent;
}
```

手写笔文件支持纯 CSS，不熟悉手写笔没问题。

如果我们现在重新加载 Dillinger，我们的样式将不会被应用。这是因为首先需要构建 app——在 app 的文件夹中运行 build.js 脚本会将所有的 js 和 Styl 文件编译成压缩的 JS 和 CSS 文件。在项目的根目录下运行`node build`，等待它完成。不会超过几秒钟。

现在重新加载 Dillinger，注意到一切都如我们所愿——除了一件事。默认情况下，textarea 不处于全选模式。让我们改变这一点。再次替换`doneHandler`功能，这一次使用以下内容:

```
function _doneHandler(jqXHR, data, response){
            var resp = JSON.parse(response.responseText)
            var textarea = $('#modalBodyText')
            $(textarea).val(resp.data)
            $('#myModal').on('shown.bs.modal', function (e) {
                $(textarea).focus().select()
            }).modal()
        }
```

首先缓存获取的文本区域，以避免重复的 jQuery 选择器。然后，我们把数据放在 textarea 中，就像我们之前做的那样，但是在模态窗口上调用 Bootstrap 的`modal()`方法之前，我们定义了一个事件监听器，它在模态出现时触发(在所有 CSS 转换之后)。完成后，我们将焦点放在 textarea 中，并用`select()`方法强制选择所有状态。

如果你现在重新加载 Dillinger，你会看到它的工作和我们的 HTML 内容被自动选中。再次运行`node build`来重建一切。

### 包扎

仍然在您自己的分支上，用消息“升级的 ShowHTML 函数在自动聚焦的全选文本区域中显示 HTML 内容”提交项目。由于构建过程，很多文件会被修改，但这没关系。现在就把我们的分店推上线吧。

```
git push origin showhtml-fix
```

我们的新分支现在已经出现在 GitHub 的回购中。

![](img/debf0c7ccf3693d9cfc217f0d9963086.png)

我们通过点击“比较和拉取请求”发送拉取请求。在编写好描述并链接到相关问题(如下图所示)后，我们点击“发送拉取请求”。

![](img/a243c01da7cde5f15a3a358772df4a9f.png)

## 结论

就是这样！PR 发出后，剩下我们要做的就是等待作者的反馈。如果我们的 PR 被接受，我们需要执行“将您的回购连接到原始版本”一节中的步骤，以将最新版本提取到我们自己的回购中。

希望你在这个贡献过程中学到了一些新东西——如果你有任何建议、问题、反馈或请求，请在下面的评论中留下。

## 分享这篇文章