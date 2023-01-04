# 使用 requestAnimationFrame 的简单动画

> 原文：<https://www.sitepoint.com/simple-animations-using-requestanimationframe/>

制作 DOM 元素动画需要每隔几毫秒修改一次 CSS 样式，以产生运动的错觉。这意味着向`setTimeout`传递一个回调函数，并在该回调函数中修改节点的`style`对象。然后再次调用`setTimeout`来排队下一个动画帧。

从凤凰涅槃中诞生了一个新的辅助函数来编写动画，名为`requestAnimationFrame`。它始于 Firefox 4，并慢慢被包括 IE 10 在内的所有浏览器所采用。幸运的是，很容易使它向后兼容旧的浏览器。

```
window.requestAnimationFrame(callbackFunction);
```

与在指定时间延迟后运行的`setTimeout`不同，`requestAnimationFrame`在浏览器下一次绘制屏幕时运行回调。这可以让你与浏览器的绘制周期同步，这样你就不会画得太频繁或不够频繁，这意味着你的动画会非常流畅，但不会对你的 CPU 要求太高。

## 筛选浏览器的不一致性

目前每个浏览器都有一个前缀版本`requestAnimationFrame`,所以让特性检测哪个版本受支持并引用它:

```
var _requestAnimationFrame = function(win, t) {
  return win["webkitR" + t] || win["r" + t] || win["mozR" + t]
          || win["msR" + t] || function(fn) { setTimeout(fn, 60) }
}(window, "equestAnimationFrame");
```

注意我们是如何使用括号符号来访问`window`对象的属性的。我们使用括号符号，因为我们正在使用字符串连接动态构建属性名。如果浏览器不支持它，我们就退回到一个常规函数，在 60 毫秒后调用`setTimeout`来达到类似的效果。

## 构建外壳

现在让我们构建一个简单的函数，它将重复调用我们的`_requestAnimationFrame`来模拟动画。

为了实现动画，我们需要一个作为入口点的外部函数和一个将被重复调用的内部函数，称为步进函数。

```
function animate() {
  var step = function() {

    _requestAnimationFrame(step);
  }
  step();
}
```

每次调用步进函数时，我们都需要跟踪动画的进度，以便知道何时结束。我们将计算动画应该什么时候结束，并根据每个周期还剩多少时间来确定我们的进度。

```
function animate() {

  var duration = 1000*3,  //3 seconds
      end = +new Date() + duration;

  var step = function() {

    var current = +new Date(),
        remaining = end - current;

    if(remaining < 60) {
      //end animation here as there's less than 60 milliseconds left
      return;

    } else {
      var rate = 1 - remaining/duration;
      //do some animation
    }

    _requestAnimationFrame(step);
  }
  step();
}
```

注意，我们正在做`+new Date()`来获取以毫秒为单位的当前时间。加号将 date 对象强制转换为数字数据类型。

`rate`变量是一个介于 0 和 1 之间的数字，代表动画的进度。

## 让它变得有用

现在我们需要考虑函数的输入和输出。让我们允许函数接受函数和持续时间作为参数。

```
function animate(item) {

  var duration = 1000*item.time,
      end = +new Date() + duration;

  var step = function() {

    var current = +new Date(),
        remaining = end - current;

    if(remaining < 60) {
      item.run(1);  //1 = progress is at 100%
      return;

    } else {
      var rate = 1 - remaining/duration;
      item.run(rate);
    }

    _requestAnimationFrame(step);
  }
  step();
}
```

我们可以这样调用这个函数:

```
animate({
  time: 3,  //time in seconds
  run: function(rate) { /* do something with rate */ }
});
```

在 run 函数中，我将放一些代码，将一个节点的宽度从“100 像素”变成“300 像素”。

```
animate({
  time: 3,
  run: function(rate) {
    document.getElementById("box").style
      .width = (rate*(300 - 100) + 100) + "px";
  }
});
```

## 改进用例

它工作得很好，但是我真正想要的是能够输入一系列函数，这些函数被一个接一个地调用。以便在第一个动画结束后，第二个动画开始播放。我们将把数组视为一个堆栈，一次弹出一个项目。让我们改变输入:

```
function animate(list) {

  var item,
      duration,
      end = 0;

  var step = function() {

    var current = +new Date(),
        remaining = end - current;

    if(remaining < 60) {

      if(item) item.run(1);  //1 = progress is at 100%

      item = list.shift();  //get the next item

      if(item) {
        duration = item.time*1000;
        end = current + duration;
        item.run(0);  //0 = progress is at 0%
      } else {
        return;
      }

    } else {
      var rate = remaining/duration;
      rate = 1 - Math.pow(rate, 3);  //easing formula
      item.run(rate);
    }

    _requestAnimationFrame(step);
  };
  step();
}
```

当动画第一次运行时，`item`为空，`remaining`小于 60 毫秒，所以我们从数组中弹出第一项并开始执行它。在动画的最后一帧，`remaining`也小于 60，所以我们结束当前动画，从数组中弹出下一个项目，并开始制作下一个项目的动画。

还要注意，我已经将`rate`值放入了一个放松公式中。从 0 到 1 的值现在以立方比例增长，使它看起来不那么机械。

要调用动画功能，我们需要:

```
animate([
  {
    time: 2,
    run: function(rate) {
      document.getElementById("box").style
        .width = (rate*(300 - 100) + 100) + "px";
    }
  }, {
    time: 2,
    run: function(rate) {
      document.getElementById("box").style
        .height = (rate*(300 - 100) + 100) + "px";
    }
  }
]);
```

请注意，盒子的宽度是如何先膨胀的，用了 2 秒钟，然后高度又膨胀了 2 秒钟。

## 包装它

让我们稍微清理一下我们的代码。注意到我们打了那么多次电话`getElementById`以至于不再好笑了吗？让我们缓存它，让我们缓存开始和结束值。

```
animate([
  {
    time: 2,
    node: document.getElementById("box"),
    start: 100,
    end: 300,
    run: function(rate) {
      this.node.style
        .width = (rate*(this.end - this.start) + this.start) + "px";
    }
  }
]);
```

注意我们不需要修改 main 函数，因为`run`函数一直是自包含对象的一部分，并且可以通过`this`变量访问对象的所有属性。现在，无论何时运行步进函数，我们都将所有变量缓存起来。

现在你知道了。一个简单的动画助手，利用了旧浏览器的回退功能。

[脚本演示](https://blogs.sitepointstatic.com/examples/simple-animations-using-requestanimationframe/requestAnimationFrameTestcase.htm)

## 分享这篇文章