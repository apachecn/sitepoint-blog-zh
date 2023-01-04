# Flowplayer 禁用暂停

> 原文：<https://www.sitepoint.com/flowplayer-disable-pause/>

**设置 flowplayer 禁用点击停止**的代码片段，以便视频在被点击时不会暂停。


```
//stopping default pause action on click
onBeforePause: function()
{
    console.log('stopping default pause action on click...');
    return false;
},
```

## 分享这篇文章