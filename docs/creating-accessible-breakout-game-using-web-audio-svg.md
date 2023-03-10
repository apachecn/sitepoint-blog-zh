# 使用网络音频和 SVG 创建可访问的突围游戏

> 原文：<https://www.sitepoint.com/creating-accessible-breakout-game-using-web-audio-svg/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

作为 WebGL 游戏引擎 [Babylon.js](http://www.babylonjs.com/) 的合著者，听人们在网络会议上讨论可访问性最佳实践，我总是感到有些不安。用 Babylon.js 创建的内容确实是盲人完全无法访问的。让每个人都能访问网络是非常重要的。我比以往任何时候都更加相信[，因为我通过自己的儿子](http://facecacheeduhandicap.com/)亲身感受到了这一点。所以我想以某种方式为网络的可访问性做出贡献。

这就是为什么我决定致力于创建一个使用 WebGL 并且完全可访问的游戏，以证明视觉游戏并非天生不可访问。我选择保持简单，所以我创建了一个突破克隆，你可以在下面的 YouTube 视频中看到它的运行:

[https://www.youtube.com/embed/25quyIGtujk?feature=oembed](https://www.youtube.com/embed/25quyIGtujk?feature=oembed)

你可以在兼容网络音频的浏览器(【caniuse.com】查看列表)中测试它，或者在 Github 上下载或[阅读源代码。](https://github.com/davrous/accessiblebreakout)

现在，让我和你们分享一下这个游戏的背景**故事和所有涉及的实验…**

## 从前

这一切都始于 2014 年[新西兰党大会](http://2014.kiwiparty.fr/)，当时听了[劳拉·卡尔巴格](http://laurakalbag.com/)关于顶级无障碍设计考虑的指导方针。我和[斯特凡·德尚](http://nota-bene.org/)讨论，他是一个可爱、有趣和有才华的家伙，我不知道如何使 WebGL 可访问，以及如何避免人们创建大量不可访问的内容。为了激励我，他向我挑战。可能没有估计后果:“*如果你能设法创建一个可访问的突破游戏，这将是非常酷的！*”。嘣。你在这里看到的种子就在那时被植入了我的大脑。我开始认真思考这个问题，并研究如何创造这样的体验。

首先，我发现在 audiogames.net 的和 game-accessibility.com 的已经有可访问的[音频游戏](https://en.wikipedia.org/wiki/Audio_game)。我还研究了为盲人创建游戏的最佳实践。虽然读起来很有趣，但我没有找到我要找的东西。我不想为盲人创造专门的体验，**我想创造一个通用的游戏，任何人都可以玩**，不管能力如何。我相信网络就是为此而产生的，我的梦想是在我的游戏中拥抱这一理念。我想创造一个独特的体验，让所有类型的用户都可以玩，这样他们就可以一起分享快乐。我想要出色的视觉效果&声音，而不是“看起来很容易理解，这就是为什么它不能那么好”的解决方案。

为此，我开始做一些实验。我拿了一个由我的朋友 David Catuhe 编写的使用 SVG 的小游戏。我对它进行了一点重构，使用 [SVG 视图框](https://www.w3.org/TR/SVG/coords.html#ViewBoxAttribute)来更好地跨屏幕缩放，并用 TypeScript 重写了它。我还使用 Babylon.js 将背景中的动画 2D 画布替换为 WebGL 画布，以添加星域体验。

![Demo game using SVG viewbox](img/cae7ad34ece1ba6f69151752b1b644b2.png)

![Demo game using SVG viewbox](img/aad5cfe79e19decf5d87fd25168b442f.png)

*SVG 视图框的美妙之处在于它可以完美地跨尺寸&分辨率*

这成为我实验的基线。

对于音频，我有几个想法。我想用的主要技巧是**空间声音**让人们不用看屏幕就能知道他们在棋盘上的位置。**这可以通过使用** [网络音频](http://webaudio.github.io/web-audio-api/)来实现。由于我无法接触到视障测试人员，我戴着一副好耳机闭上眼睛来“作弊”。稍后你会看到，用一个真正的盲人用户测试游戏帮助我解决了很多问题，但作为开始，这是一个测试游戏的好方法。

我开始用一个来自 HTML5Rocks 的优秀教程[的样本作为我的指南](http://www.html5rocks.com/en/tutorials/webaudio/games/)来修补 Web Audio API。主要演示在“ *3D 定位声音*部分:

[![Audio games tutorial](img/23fa968c5d3d5aaffb9d9692123a42ab.png)](http://www.html5rocks.com/en/tutorials/webaudio/games/#toc-3d)

然后我把鼠标光标发出的声音换成了球在游戏中的位置。测试结果没有我希望的那么好。光靠声音来准确理解球在屏幕上的位置太复杂了，而且你不能像看到屏幕时那样预测球的方向。尽管如此，我还是觉得当球碰到什么东西——一块砖或一面墙——时发出一些 3D 声音很有趣。这是对任何人都有用的信息，所以我保留了那部分。

由于我在业余时间也是一名作曲家，我的下一个想法是为每块砖的柱子使用特定的钢琴音符，从而增加左右的感觉。默认情况下，我选择了 8 列来覆盖一个八度音阶。我编码了它，它很有趣，但是对游戏性没有帮助。

![Piano note octave](img/f78d158224c87c18b6420670fb5c9f9f.png)

我知道我需要帮助，所以我向我的大儿子展示了我所做的，他想出了最好的解决方案。他告诉我，**使用播放速度和声音效果**来提供球在哪里的信息是有意义的。经过几次测试，我得出了下面的算法:

*   如果球与球拍完全**垂直对齐**，以“正常”速率播放声音。
*   如果球**没有与桨**对齐，**会减慢播放速度**。球离球拍越远，声音就越慢。这将为盲人提供即时反馈，即球不再对齐，他们需要移动球拍以避免错过球。
*   以空间化的方式播放音乐的**声音**:如果球在球拍的中心，X 轴上为 0，X 轴上的–值和+值基于球与球拍的距离。

这个算法的第一次测试非常令人鼓舞——我几乎可以闭着眼睛玩游戏。过了一会儿，我调整了游戏和算法来解决我看到的一些问题。当你看不到球的方向时，你无法预测球的方向，所以当音乐突然变慢时，移动球拍太难了。你只是不能及时调整桨的位置。

为了解决这个问题，我增加了**一些公差**。首先，**桨在【无障碍模式】**下是原来的两倍宽，以弥补看不到。其次，当球到达垂直屏幕的 80%时，我会减慢球的速度，以便让用户有更多的时间在球落地前反弹。最后，一旦球没有对准 **66%的桨宽**，我就改变了播放速率。球拍的其余部分仍然适用于球的碰撞，但是使用这种方法可以使盲人用户**在球将要错过球拍的时候预测到**。

![Game algorithm](img/e16aaa27d969df7509055b3544ea47c9.png)

我对使用这些游戏参数的游戏非常满意。我一直在和我的几个同事一起测试这个游戏，他们可以闭着眼睛玩这个游戏。但是他们都知道突破游戏应该是什么样子，因此，他们的大脑或多或少已经能够预测游戏机制。他们受到了条件限制。

我的最终测试是在 2014 年巴黎国际互联网大会期间进行的，这是一个在法国举行的**著名会议**。我的目标是为[著名的闪电对话](http://www.paris-web.fr/2014/conferences/lightning-talks.php)完成游戏初稿。我对自己的所作所为感到有点紧张，于是再次与斯蒂凡妮见面，分享我的担忧。他告诉我，我应该和西尔维·杜莎托(Sylvie Duchateau)谈谈，她是一位从事网络无障碍工作的盲人女士。

在一次休息时，我和她分享了我的项目和它背后的音频游戏理念。让我惊讶的是，她告诉我她不知道什么是突围赛！这是显而易见的。如果你看不见，一个纯视觉的游戏对你没有太大的吸引力。然而，她觉得有空间音频的游戏很有趣，所以我们试了试。

她戴上我的耳机，我开始玩游戏……令我沮丧的是，她根本就不会玩游戏。音频信息太多，无法准确决定该做什么。我现在应该向左还是向右移动？在与她简单讨论后，她告诉我应该删除一些音频细节。她还建议我避免对音乐使用网络音频空间化(它根据与球拍的距离从中心移动到左侧或右侧)，而是只启用右侧或左侧扬声器，以便提供非常清晰的指示。当她在那里的时候，我很快修改了代码，然后**她马上就能打破她的前两个砖块**。我太开心了，你都无法想象。她甚至问我最好的成绩是多少，这意味着我达到了我的目标，提供一个可访问的游戏——至少对视障人士来说。

处理这一切的主要代码如下:

```
 // To help visually impaired users, the sound is being played at normal rate
// on 66% of the global width. It's to help them anticipating the ball
export const ACCESSIBLE_PAD_TOLERANCE = 0.66;
export const DEFAULT_MUSIC_PLAYRATE = 1.3;

private _updateAccessibilityMusic() {
    var paddleX = this._padX;
    var paddleW = this._padWidth;
    var ballPosition = { x: this._ballX, y: this._ballY };

    var deltaX = paddleW * ((1 - ACCESSIBLE_PAD_TOLERANCE) / 2);

    if (ballPosition.x > deltaX + 10 && ballPosition.x < this._viewPortWidth - (deltaX + 10)) {
        paddleX += paddleW * ((1 - ACCESSIBLE_PAD_TOLERANCE) / 2);
        paddleW = paddleW * ACCESSIBLE_PAD_TOLERANCE;
    }

    // If paddle & ball aligned, sound is played on both ears (X = 0, for center)
    // If the ball is on the left, musicIndicatorX should be negative otherwise positive
    var musicIndicatorX;

    // Position coordinates are in normalized canvas coordinates
    // with -0.5 < x, y = paddleX && ballPosition.x <= paddleX + paddleW) {
            this._music.setPlaybackRate(DEFAULT_MUSIC_PLAYRATE)
            musicIndicatorX = 0;
        }
        else {
            var distanceFromPaddle;
            // Ball is on the left of the paddle
            if (ballPosition.x < paddleX) {
                distanceFromPaddle = paddleX - ballPosition.x;
                musicIndicatorX = -30;
            }
            else {
                distanceFromPaddle = ballPosition.x - paddleX - paddleW;
                musicIndicatorX = 30;
            }
            var distanceFromPaddleNormalized = distanceFromPaddle / this._viewPortWidth;
            // Slowing down the play rate based on the distance from the paddle
            this._music.setPlaybackRate(0.9 * (1 - distanceFromPaddleNormalized));
        }
        // Playing music on left or right speaker based on the ball position from the paddle
        this._music.setPosition(new BABYLON.Vector3(musicIndicatorX, 0.5, 0));
    }
} 
```

**注意:**我用的是巴比伦。JS 声音栈实现在网络音频之上。你可以[阅读我们的文档](http://doc.babylonjs.com/tutorials/16._Playing_sounds_and_music)或者[在 Github](https://github.com/BabylonJS/Babylon.js/tree/master/src/Audio) 上获取代码。

## 我在游戏中加入的其他想法

我无法记住我为了优化游戏的“通用性”而尝试的每一个技巧，所以我将总结一下我实现了什么。

### 语音合成

有些用户可能看不到还剩多少砖块。同样，他们也无法根据视觉效果知道自己是赢了还是输了。这就是为什么我认为使用网络音频语音库 [meSpeak.js](http://www.masswerk.at/mespeak/) 来添加音频线索是个好主意**。然而，在与 [Anthony Ricaud](https://twitter.com/rik24d) 和其他一些参加活动的人讨论之后，事实证明这并不是最好的解决方案。问题是，我将在代码中强制使用特定的声音和速度。然而，辅助技术的用户已经有了**偏好设置——以确定的速度发出某种声音**。因此，最好在游戏过程中使用一个 [ARIA Live Region](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) 来更新用户。我相信我还能做得更多；如果你愿意，请随意增强我的代码，我将不胜感激。**

语音合成目前会告诉你还剩多少砖块需要打破，游戏已经开始或结束(通过输或赢)，以及你的最终得分。作为 ARIA live 区域中的值，screenreaders 将自动向用户读取这些信息。视觉用户不需要机器人的声音来告诉他们发生了什么。

### SVG 样式

我决定在这个游戏中使用 **SVG 有几个原因:它可以在所有屏幕上完美缩放，因为它是基于**向量的**，它可以与 CSS** 结合用于设计**，最后但同样重要的是，它**与 ARIA** 配合得非常好。我在本文前面已经提到了缩放部分，但我还没有做足够的研究来了解 ARIA(除了实时区域)在这种情况下对 SVG 有什么用处。**

另一方面，CSS 对我很有帮助。提醒一下，我的目标是拥有同样的[游戏](https://david.blob.core.windows.net/accessibility/accessiblebreakout/index.html)，拥有同样的代码库，被任何人使用。当你加载游戏时，我会加载默认的样式表，其中包含针对视觉障碍者的优化。原因如下:

*   如果你看不到或者只能看到一部分，**最好从高对比度视觉效果开始**。我默认加载“indexvi.css ”,使用黄色&黑色来获得高对比度的颜色。我还禁用了背景 WebGL 画布，以减少视觉混乱。如果你可以看到，不喜欢这样，你可以取消选中适当的选项，并获得星空和不太生动的视觉效果。
*   如果您完全看不见，**您可以禁用“视觉障碍”选项来启用高质量图形**。这将加载“index.css”样式表并启用 WebGL 背景。多亏了 SVG 与 CSS 的完美结合，我们只需要加载这个新的样式表，剩下的工作就会自动完成。当然，看不见东西的人并不在乎图像质量是好是坏。但是对观看你游戏的人来说更好，因为这表明易访问的游戏不必看起来很简单。
*   如果能看清楚，**取消所有选项。你会有很棒的视觉效果，速度和桨宽会被调整到更困难的程度。你也不会得到音频提示，比如还剩多少砖块，你是赢了还是输了。这是不必要的——这应该是很明显的。**

总之，工作流程如下:

*   At first launch of the game, **we anticipate a visual impairment** and give you a high contrast version of the game:

    ![For visually impaired and blind](img/c8f9e468296099321d947d4b237a7099.png)

*   If you can’t see at all, you can uncheck the “**Visual Impaired**” option to enable great graphics for your surrounding audience. The paddle width remains the same and you still have audio assistance:

    ![For blind](img/63afcf9866cb1103346d530cde12dd4e.png)

*   If you don’t have any visual impairments, you can uncheck everything to make the paddle more narrow and the ball speed faster:

    ![Demo game](img/1c0dd941cadc2308dea1979977a1e327.png)

### 未实施的想法和结论

我给自己的挑战是拥有独立于人的视觉能力的伟大游戏体验。我知道我没有完全履行这一承诺，例如，如果你完全看不见，你不知道屏幕上剩余的砖块在哪里，而如果你能看见或有轻微的视觉障碍，你可能会找到剩余的砖块并调整球的方向来打碎它们。

我最初的想法是，一旦只剩下 10 块砖，就使用语音合成。它可以这样说:“ *4 块砖在左边，4 块在中间，2 块在右边*”。尽管如此，这不是很精确，而且在没有视觉效果的情况下很难改变球的方向。但是也许你们中的一个会找到一个酷的&优雅的解决方案来解决这个问题(提示，提示)。

尽管如此，我还是对这个挑战感到非常高兴，我在尝试解决它的过程中获得了很多乐趣。通过阅读关于可访问性的文章，我学到了很多。我也希望我已经证明了，通过**简单地思考什么是可能的**，可以为人们提供可访问性，甚至是在意想不到的领域。最后但同样重要的是，我了解到通过在你的游戏中启用可访问性，你可以改善每个人的体验。

## JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13424-DEV-sitepoint-article48) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13424-DEV-sitepoint-article48)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article48&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article48&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article48&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article48&utm_campaign=SitePoint)
*   GitHub 上的编码实验室:跨浏览器测试和最佳实践

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=13424-DEV-sitepoint-article48)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=13424-DEV-sitepoint-article48)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com/) 和 [babylonJS](http://babylonjs.com/) 项目)
*   [托管网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations/?WT.mc_id=13424-DEV-sitepoint-article48)(来自律师奶爸和基里尔·赛克谢诺夫，包括[流形。JS](http://manifold.js.com/) 项目)

为网络平台提供更多免费的跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=13424-DEV-sitepoint-article48)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=13424-DEV-sitepoint-article48) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13424-DEV-sitepoint-article48)

## 分享这篇文章