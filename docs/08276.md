# 确保始终安全使用 console.log

> 原文：<https://www.sitepoint.com/safe-console-log/>

**使用 console.log always** 这个方便的小 JavaScript 代码片段会很安全。在禁用控制台的情况下，Console.log 可能会在 Internet explorer 和其他浏览器中崩溃，安全胜于抱歉！

另请参见:【Firebug console.log 的轻量级包装器

```
(function(a){function b(){}for(var c="assert,count,debug,dir,dirxml,error,exception,group,groupCollapsed,groupEnd,info,log,markTimeline,profile,profileEnd,time,timeEnd,trace,warn".split(","),d;!!(d=c.pop());){a[d]=a[d]||b;}})
(function(){try{console.log();return window.console;}catch(a){return (window.console={});}}());
```

## 分享这篇文章