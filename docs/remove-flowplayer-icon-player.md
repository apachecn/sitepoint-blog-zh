# 移除播放器上的 Flowplayer 图标

> 原文：<https://www.sitepoint.com/remove-flowplayer-icon-player/>

要删除播放器上的 Flowplayer 图标，首先您需要购买许可证密钥。许可证密钥对为单个域注册的任意数量的子域有效。然后只需将播放器的位置更改为 flow player . commercial-3 . 2 . 11 . swf(http://releases . flow player . org/swf/flow player . commercial-3 . 2 . 11 . swf)并将您的许可证密钥添加到下面的 javascript 中(使用 js 从浏览器地址栏检查该域)。


```
flowplayer("player",
    "http://releases.flowplayer.org/swf/flowplayer.commercial-3.2.11.swf",{
    // product key from your account
    key: '#$7162d2d730cf607ac6d'
});
```

## 分享这篇文章