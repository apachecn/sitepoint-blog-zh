# 用 JavaScript 创建精确的计时器

> 原文：<https://www.sitepoint.com/creating-accurate-timers-in-javascript/>

异步计时器是 JavaScript 中所有基于时间的进程的基石。从时钟和秒表这样显而易见的东西，到视觉效果和动画，到对下拉菜单的可用性至关重要的同步延迟。

但是 JavaScript 计时器的问题是**它们不是很精确**。我们不能仅仅通过增加 *x* 来制作秒表，因为它不会停留在时间上:

```
var time = 0,
    elapsed = '0.0';

window.setInterval(function()
{
    time += 100;

    elapsed = Math.floor(time / 100) / 10;
    if(Math.round(elapsed) == elapsed) { elapsed += '.0'; }

    document.title = elapsed;

}, 100);
```

Web 浏览器和所有应用程序一样，轮流占用一段 <abbr title="Central Processing Unit">CPU</abbr> 时间，它们必须等待的时间会有所不同，这取决于负载。这就是导致异步计时器延迟的原因——一个 200 <abbr title="milliseconds">毫秒</abbr>的计时器实际上可能需要 202 <abbr title="milliseconds">毫秒</abbr>或 204，这将逐渐使秒表超时。

这种情况下的解决方案根本不依赖于计时器的速度，而是在每个循环中查询新的系统时间，并从中获得输出:

```
var start = new Date().getTime(),
    elapsed = '0.0';

window.setInterval(function()
{
    var time = new Date().getTime() - start;

    elapsed = Math.floor(time / 100) / 10;
    if(Math.round(elapsed) == elapsed) { elapsed += '.0'; }

    document.title = elapsed;

}, 100);
```

但是对于不那么字面意义的应用，比如动画——我们能使用同样的方法使它们同样精确吗？

## 打不过系统

最近我一直在做一些视觉过渡，我发现自己正在思考这个问题:如果用户指定一个 5 秒的动画，我们该怎么做才能让这个动画真正持续 5 秒，而不是 5.1 或 5.2 秒？可能是很小的差别，但是小的差别积少成多。不管怎样，让事情变得更好本身就是目的！

言归正传——我们确实可以使用系统时钟来补偿计时器的不准确性。如果我们通过一系列的`setTimeout`调用来运行一个动画——每个实例调用下一个——那么我们所要做的就是**精确地计算出*它有多不准确，然后从下一次迭代中减去这个差值:***

```
var start = new Date().getTime(),
    time = 0,
    elapsed = '0.0';

function instance()
{
    time += 100;

    elapsed = Math.floor(time / 100) / 10;
    if(Math.round(elapsed) == elapsed) { elapsed += '.0'; }

    document.title = elapsed;

    var diff = (new Date().getTime() - start) - time;
    window.setTimeout(instance, (100 - diff));
}

window.setTimeout(instance, 100);
```

为了看到所有这些的实际效果，我准备了一个演示来说明正常计时器和调整计时器之间的区别— **[自调整计时器示例](https://www.sitepoint.com/examples/timeradjust/examples.html)** 。

演示有三个例子:

1.  第一个例子(左)是一个用`setInterval`实现的普通定时器，它显示了迭代计数所用时间和系统时钟实际时间之间的累计差值；
2.  第二个例子(中间)增加了浏览器在每次迭代中所做的工作量，以显示更多的工作意味着更多的延迟，因此更不准确；
3.  第三个例子(右)做的工作和第二个一样多，但现在使用了自调整技术，以说明它对整体精度有多么深远的影响；

这种方法的伟大之处在于，不管计时器有多不准确，调整总是会保持准确。少量的恒定延迟将很容易得到补偿，但同样，由处理器使用量激增(如启动应用程序)导致的延迟突然大幅增加也很容易得到缓解。我们可以从未经调整的例子中看到，即使一次迭代的误差值很小，但是*累积*效应却非常大。

当然，即使调整过的计时器也不能 100%补偿——它调整下一个迭代的*的速度，因此不能补偿最后一个*迭代的*的延迟。但是，无论这种差异达到什么程度，与这种差异乘以成百上千个实例的累积效应相比，都是微不足道的。*

## 随波逐流

我之前说过我在制作动画的时候有了这个想法(为了更新我流行的[图像转换](http://www.brothercake.com/site/resources/scripts/transitions/)),下面是我最终得出的抽象。它实现了一个自调整计时器，根据输入长度和分辨率计算速度和步数，并提供实例时和完成时的回调:

```
function doTimer(length, resolution, oninstance, oncomplete)
{
    var steps = (length / 100) * (resolution / 10),
        speed = length / steps,
        count = 0,
        start = new Date().getTime();

    function instance()
    {
        if(count++ == steps)
        {
            oncomplete(steps, count);
        }
        else
        {
            oninstance(steps, count);

            var diff = (new Date().getTime() - start) - (count * speed);
            window.setTimeout(instance, (speed - diff));
        }
    }

    window.setTimeout(instance, speed);
}
```

下面是其使用的一个简化示例，它以 20 帧/秒的速度在 5 秒内对图像进行不透明度渐变(仅使用标准语法)——**[自调整不透明度渐变](https://www.sitepoint.com/examples/timeradjust/fadeout.html)** :

```
var img = document.getElementById('image');

var opacity = 1;
img.style.opacity = opacity;

doTimer(5000, 20, function(steps)
{
    opacity = opacity - (1 / steps);
    img.style.opacity = opacity;
},
function()
{
    img.style.opacity = 0;
});
```

现在你知道了——自我调节的计时器可以产生更好的动画，让你有信心当你指定一个 5 秒的效果时，你会得到一个持续 5 秒的效果！

*缩略图鸣谢:[育白光](http://www.flickr.com/photos/ytwhitelight/504212595/)*

## 分享这篇文章