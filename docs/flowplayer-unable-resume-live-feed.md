# Flowplayer 无法恢复实时馈送

> 原文：<https://www.sitepoint.com/flowplayer-unable-resume-live-feed/>

我正在尝试找到一种方法来恢复在 flowplayer 中暂停的直播流。我想知道为什么我暂停后似乎不能恢复我的流。

标准代码暂停流:

```
$f('fms1').pause()
```

恢复流的标准代码:

```
$f('fms1').resume();
```

我还尝试了以下方法，但无济于事:

```
$f('fms1').getClip().update();
$f('fms1').play();
```

更多尝试:

```
$f('fms1').pause();
$f('fms1').resume();
$f('fms1').getClip().update();
$f('fms1').play();
```

## 分享这篇文章