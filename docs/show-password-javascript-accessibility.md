# JavaScript 可访问性实验失败的教训

> 原文：<https://www.sitepoint.com/show-password-javascript-accessibility/>

[![Little girl hides her eyes](img/499f6fb1676ad2cba5f53215a7f8d41a.png)](https://www.flickr.com/photos/44124370018@N01/4470620660)

图片来源:daveynin

密码字段有“显示密码”功能是很常见的，这是一个复选框或按钮，可以暂时将字段转换为纯文本，这样用户就可以看到他们输入的密码。

然而，我想到这对于 screenreader 用户来说可能不安全，因为一个盲人可能不知道其他人是否正在看他们的屏幕(例如，在半公共场所，如办公室或会议室)。

密码字段对屏幕阅读器来说是模糊的，就像它们在视觉上一样——屏幕阅读器会对每个键入的字母说 <q>star</q> 或类似的话，而不是宣布字母本身。就像视力正常的用户一样，屏幕阅读器用户没有交互式反馈来确认他们输入的密码是否正确。

所以我想探索编写这个功能的脚本的可能性，以便屏幕阅读器用户可以听到他们输入的内容，而字段仍然保持视觉模糊。这样，带着耳机听音乐的用户就可以安全地知道他们的密码不会泄露给周围的任何人。

没用！

但是在这个过程中，我想起了一些关于可访问 JavaScript 的真实情况，以及 ARIA live regions 的一个不幸的问题，这对可访问脚本有着更广泛的影响。

## 第一个原型

我想到的基本想法非常简单:一个带有`aria-live="assertive"`的元素将被添加到页面中，然后每当密码字段被编辑时，它的值将被复制到该元素中。因为它是一个[活动区域](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions "ARIA Live Regions (developer.mozilla.org)")，新的值会被屏幕阅读器立即宣布，但是它也会被隐藏在[靠左的位置](http://webaim.org/techniques/css/invisiblecontent/#absolutepositioning "Invisible Content Just for Screen Reader Users (webaim.org)")，并且不会出现在 tab 键顺序中，所以它不会很明显。

下面是代码(或者[在自己的页面](http://jspro.brothercake.com/hearpassword/prototype1.html)上查看第一个原型):

[//jsfiddle.net/SitePoint/fb41jLpe/embedded/result,js,html/](//jsfiddle.net/SitePoint/fb41jLpe/embedded/result,js,html/)

如果您使用的是支持 ARIA 的屏幕阅读器，那么<q>听到密码</q>按钮应该可以启用这个功能，并立即说出字段中已经存在的值。然后，当它保持启用状态时，屏幕阅读器会在输入时宣布该值(同时说出<q>星</q>)。

如果您没有使用屏幕阅读器，您将听不到任何声音。虽然使用类似于 [meSpeak.js](http://www.masswerk.at/mespeak/) 的东西来实现直接的文本到语音转换是可能的，但是我不认为这是一个好主意——因为任何需要这个功能的人都已经有了屏幕阅读器。

更重要的是，他们有一个屏幕阅读器，可以控制声音和语速。外部的 <abbr title="Text To Speech">TTS</abbr> 只会碍事。

下面的视频展示了 screenreader 用户(使用 Firefox + NVDA)将会体验到什么:

[https://www.youtube.com/embed/ASerd4xwcCE?showinfo=0&rel=0](https://www.youtube.com/embed/ASerd4xwcCE?showinfo=0&rel=0)

现在一个显而易见的问题是，将整个值复制到一个活动区域意味着该值被视为一个单词；每当用户键入一个字母时，整个值都会被宣布，而不仅仅是他们键入的字母。这与标准文本字段的行为截然不同，在标准文本字段中，语音反馈会在输入时告诉您各个字母。

在那个例子中，我输入了一个可以被解释为单词的密码，但是如果这个值更复杂，比如说`"NCC-1701"`呢？嗯，在这种情况下，NVDA 会说<q>n . c . c 1701</q>，这是有问题的，因为另一个原因:它没有告诉我们，前三个字母是大写字母，而不是小写字母，它根本没有宣布破折号。《大白鲨》的情况稍微好一点，它确实讲破折号，但还是没有提到大写字母。

这是一个非常基本的问题。如果您的密码恰好是一个小写单词，那么这种解决方案会很好，但是如果它是特殊字符的组合，那么您可能根本听不到完整的密码。例如，您可能认为您键入的是`"ncc1701"`而不是`"NCC-1701"`，当我们验证密码时，这种差异显然是显著的！

这甚至不是一个特别困难的例子。如果你的密码很复杂，比如`"goHuE&-A"`——NVDA 会把它读作 <q>go hooey 和</q>，这真的一点用都没有！或者，如果你的密码是类似于`"gate"`的东西，但是你实际上输入了`"gaite"`，那么在这两种情况下，语音反馈将完全相同。

## 第二个原型

也许我们可以通过提供更详细的反馈来改进这一点。每当值改变时，我们可以读出单个字母来响应`keypress`事件，而不是读出整个值。

下面是该方法的修改代码(或者[在自己的页面](http://jspro.brothercake.com/hearpassword/prototype2.html)上查看第二个原型):

[//jsfiddle.net/SitePoint/fb41jLpe/2/embedded/result,js,html/](//jsfiddle.net/SitePoint/fb41jLpe/2/embedded/result,js,html/)

这是它在火狐+ NVDA 中的应用:

[https://www.youtube.com/embed/Ivv-SV-ipmQ?showinfo=0&rel=0](https://www.youtube.com/embed/Ivv-SV-ipmQ?showinfo=0&rel=0)

从某种意义上说，它更好，因为它在你打字时提供了更详细的反馈，但从另一种意义上说，它更糟，因为它限制了你的输入速度。每个字母在说出实际的字符之前仍然是说 <q>star</q> ，所以你必须等待它发生才能听到这个字符；如果你打字更快，那么你就听不到任何字母，直到你停下来，这时你只能听到最后一个字母(如视频所示)。

所以这个更新的原型所做的只是通过创造一个不同的问题来解决一个问题。

## 没有原型了！

也许我们有办法改进它。也许我们可以用空格分割这个值，例如，输出`"h e l l o"`而不是`"hello"`，这样就可以读出单个字母而不是整个单词。这对于字母来说是可行的，但它不能解决不使用大写字母或标点符号的问题，只会使包含空格的密码问题更加复杂。

无论如何，我们现在已经进入了丑陋的黑客领域！有时候，越来越多的黑客攻击只会导致更多的黑客攻击，我们不得不从我们正在做的事情后退一步，并问——整个事情是一个坏主意吗？除非我们能让它像普通的纯文本字段一样响应，否则我们永远不会有直观的解决方案。

就格言而言，这非常有用。如果我们不能创造一个*直观的解决方案*——如果做一件事的唯一方法是无休止地破解一个蹩脚的解决方案的细节——那么也许它一开始就不值得做。

## 这东西是直播的吗？

但有一个更大的问题需要解决，这使得这一切变得无关紧要，那就是**它在 IE + Jaws** 中不工作；因为这是[最受欢迎的组合](http://webaim.org/projects/screenreadersurvey5/#used "Screen Reader User Survey #5 Results (webaim.org)")，在那里失败是一件相当大的事情。

这是 IE + Jaws 中再次使用的第一个原型:

[https://www.youtube.com/embed/rPYcaHrt15A?showinfo=0&rel=0](https://www.youtube.com/embed/rPYcaHrt15A?showinfo=0&rel=0)

你可以看到按下<q>听到密码</q>按钮只是宣布按钮的更新状态，它并不读取实时区域的值。原因归结于`aria-live="assertive"`的行为。

`aria-live`属性有三个可能的值，它们决定了消息应该有多自信:

*   `"off"`表示内容变更未公布。
*   `"polite"`表示只有当用户空闲时才应该宣布这个变化(也就是说，一直等到屏幕阅读器停止它当时正在说的话)
*   `"assertive"`表示应该立即宣布更改(即打断屏幕阅读器当时正在说的话)

但是最近， [Russ Weakley](http://maxdesign.com.au/) 和我用 ARIA live regions 做了一些[测试，为即将到来的](http://maxdesign.com.au/jobs/sample-accessibility/10-notifications/index.html "Injecting content into the page tests (maxdesign.com.au)")[可学课程](https://learnable.com/)做准备，我们的发现是如此重要，以至于它本身就值得有一个加粗的段落:

断言的活区域通常不是断言的。

换句话说，大多数浏览器/屏幕阅读器的组合实际上并不支持`"assertive"`值，它们将它视为与`"polite"`相同。具体来说——只有 Chrome 或 Safari 的 VoiceOver 才会真正打断用户宣布一条断言消息；所有其他测试过的组合(包括 NVDA 或 Jaws 与任何浏览器)不会宣布，直到屏幕阅读器沉默。

在这些原型中，我们有一个被视为礼貌的自信区域。火狐+ NVDA 确实宣布了这个值，因为点击<q>听到密码</q>按钮不会触发其他任何事情——NVDA 不会读取更新后的按钮文本，因为没有其他任何东西被说出，所以可以宣布一条礼貌的消息。然而，在 IE + Jaws 中，单击按钮会导致它读取更新的按钮文本，这意味着阅读器并没有空闲，因此不会公布活动区域。

## 结论

这些都不应该作为不实现标准的<q>显示密码</q>功能的理由(也就是简单地切换字段类型)。屏幕阅读器可以访问这些解决方案，唯一担心的是它们存在潜在的安全/隐私风险。

但是如果另一种选择是不提供任何方式让任何用户看到他们的密码，那么可以说这样做比什么都不做要好。(虽然可能有一个可用性案例说密码字段根本不应该被屏蔽，但是这样的问题超出了本文的范围。)

我们也许可以通过向屏幕阅读器用户解释来减轻风险，我们可以通过在触发按钮上添加`aria-label`来做到这一点。当一个按钮或链接有`aria-label`时，屏幕阅读器将读出标签文本*而不是元素内容的*——所以它必须至少包含相同的信息，但也可以有只与屏幕阅读器用户相关的补充信息(这种技术有[的各种用途](https://dev.opera.com/articles/ux-accessibility-aria-label/ "UX accessibility with aria-label (dev.opera.com)")):

```
<button aria-label="Show password as plain text. Note: this will visually expose your password on the screen.">
  Show Password
</button>

```

不过，最终，从这个实验中重要的是关于可访问脚本的一个基本事实:**如果它不能以可用和直观的方式解决问题，那么它就没有解决问题**。

可访问性不是满足抽象检查的练习，而是关于为有特殊需求的人提高可用性。因此，一个技术上可行但对这些用户群不可用的解决方案，根本不能被认为是可行的。

## 分享这篇文章