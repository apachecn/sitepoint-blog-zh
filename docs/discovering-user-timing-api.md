# 发现用户计时 API

> 原文：<https://www.sitepoint.com/discovering-user-timing-api/>

几个月前，[我做了一个关于 html 5 API](https://speakerdeck.com/aurelioderosa/people-dont-give-a-f-star-star-k-of-javascript-codemotion-madrid-2013)的演讲，部分集中在如何测量性能上。一种技术是通过高分辨率时间 API，一种在我的的一篇[以前的文章中描述的 API。该 API 允许您以亚毫秒级的分辨率检索当前时间，而不受系统时钟偏差或调整的影响，这反过来使我们能够准确地测量 JavaScript 代码的性能。虽然这个 API 很有用，但是它给我们留下了在代码中引入大量变量的痛苦。此外，如果您想要测量在几个文件或模块之间分割的代码的性能，您必须引入全局变量。为了解决这些问题，我们可以使用用户计时 API。](https://www.sitepoint.com/discovering-the-high-resolution-time-api/)

## 什么是用户计时 API

用户计时 API 被定义为<q cite="https://www.w3.org/TR/user-timing/">一个接口，通过允许 web 开发人员访问高精度时间戳来帮助他们测量应用程序的性能。</q>换句话说，这个 API 允许您准确地测量和报告 JavaScript 代码的性能，当您需要对代码进行基准测试时，它是完美的。截至<time datetime="2013-12-12">2013 年 12 月 12 日</time>该 API 是 W3C 推荐标准。因此，规格是稳定的，您可以期待很少的变化。

这个 API 处理两个主要概念:标记和测量。第一个实现了`PerformanceMark`接口，而第二个实现了`PerformanceMeasure`接口。这两个接口，都扩展了 [`PerformanceEntry`接口](https://www.w3.org/TR/performance-timeline/#performanceentry)。一个标记代表一个瞬间(时间戳)，而一个度量代表两个标记之间经过的时间。因为它们都扩展了`PerformanceEntry`接口，所以它们拥有相同的四个只读属性:

*   `name`:与用于检索的标记或措施相关的助记名称。
*   `entryType`:指定对象的类型，即是标记还是测量。
*   `startTime`:如果对象是一个标记，这是一个`DOMHighResTimeStamp`，一个使用高分辨率时间 API 的`performance.now()`方法检索的高度精确的时间戳。如果对象是一个测量，它包含测量的开始标记的`DOMHighResTimeStamp`。
*   `duration`:如果对象是标记，则值始终为 0(零)。如果对象是一个度量，它包含两个标记之间经过的时间。

这个用户计时 API 公开了属于`window.performance`对象的四个方法。它们是:

*   `mark(name)`:存储一个`DOMHighResTimeStamp`及相关名称。
*   `clearMarks([name])`:删除一个或所有存储的标记。
*   `measure(name[, mark1[, mark2]])`:存储两个具有指定名称的标记之间经过的时间。
*   `clearMeasures([name])`:删除一个或所有存储的测量。

请注意，传递给`mark()`和`measure()`函数的名字是*而不是*的唯一 id。您可以任意多次使用同一个名称。在这种情况下，当您执行名称检索时，将返回一个按`startTime`属性排序的数组。

在继续之前，让我们看一个简单的例子，它使用了所描述的一些方法。假设我们想测量一个函数的执行时间，然后删除测量结果而不显示数据。执行此任务的代码如下所示:

```
performance.mark("startFoo");
// A time consuming function
foo();
performance.mark("endFoo");

performance.measure("durationFoo", "startFoo", "endFoo");

// Delete all Marks
performance.clearMarks();
// Delete the Measure "durationFoo"
performance.clearMeasure("durationFoo");
```

这个代码片段展示了我们如何调用前面介绍的所有方法。然而，存储时间戳，然后在不使用测量值的情况下删除它们是完全没有用的。为了检索标记和度量的数据，我们需要使用属于`Performance`接口的另外两个方法:`getEntriesByType(type)`和`getEntriesByName(name)`。前者返回由`type`参数指定类型的实体列表(即“马克”代表马克)。后者返回由`name`参数指定名称的实体列表。它们都返回基于`startTime`属性排序的列表。

## 浏览器支持

对这个 API 的支持在桌面和移动浏览器上都相当不错。此外，那些支持这个 API 的不使用供应商前缀。实现用户计时 API 的桌面和移动浏览器是 Internet Explorer 10+、Chrome 25+和 Opera 15+。然而，我们可以预期 Firefox 很快会支持它，因为它处于 W3C 推荐过程的当前阶段。

"好的，但是如果我想在不支持它的浏览器中使用这个 API 呢？"

很高兴你问了！幸运的是，有一个名为 [usertiming.js](https://github.com/nicjansma/usertiming.js) 的聚合填充允许我们使用前面描述的方法。坏消息是，这种 polyfill 只能在支持高分辨率时间 API 及其`performance.now()`方法的浏览器中工作。

## 演示

本节提供了一个简单的演示，允许您尝试本文中解释的概念。这个演示定义了一个简单的表单，有两个输入字段。在它们里面，我们有两个数字，用来模拟一个给定持续时间的耗时函数。我们还测试浏览器支持，如果用户的浏览器不支持 API，我们会显示“不支持 API”消息。如果浏览器支持用户计时 API，我们将一个监听器连接到表单内按钮的`click`事件。单击后，我们运行两个模拟函数并存储时间戳。然后，我们测量经过的时间并显示一些存储的信息。以下代码的现场演示可在[这里](http://aurelio.audero.it/demo/user-timing-api-demo.html)获得。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <meta name="author" content="Aurelio De Rosa">
    <title>User Timing API Demo by Aurelio De Rosa</title>
    <style>
      body
      {
        max-width: 500px;
        margin: 2em auto;
        font-size: 20px;
      }

      h1
      {
        text-align: center;
      }

      .hidden
      {
        display: none;
      }

      .field-wrapper
      {
        margin-bottom: 1em;
      }

      .button-demo
      {
        padding: 0.5em;
        display: block;
        margin: 1em auto;
      }

      .author
      {
        display: block;
        margin-top: 1em;
      }
    </style>
  </head>
  <body>
    <h1>User Timing API</h1>
    <form>
      <div class="field-wrapper">
        <label for="count-ut-1">Test 1: Count until</label>
        <input type="number" id="count-ut-1" value="100000" />
      </div>

      <div class="field-wrapper">
        <label for="count-ut-2">Test 2: Count until</label>
        <input type="number" id="count-ut-2" value="1000000" />
      </div>

      <button type="button" id="button-play-ut" class="button-demo">Run demo</button>
    </form>
    <span id="ut-unsupported" class="hidden">API not supported</span>
    <div id="ut-results" class="hidden">
      <h2>Marks</h2>
      <div id="ut-marks"></div>
      <h2>Measures</h2>
      <div id="ut-measures"></div>
    </div>

    
      Demo created by <a href="http://www.audero.it">Aurelio De Rosa</a>
      (<a href="https://twitter.com/AurelioDeRosa">@AurelioDeRosa</a>)
    

    <script>
      window.performance = window.performance || {};
      if (performance.mark === undefined) {
        document.getElementById('ut-unsupported').classList.remove('hidden');
        document.getElementById('button-play-ut').setAttribute('disabled', 'disabled');
      } else {
        document.getElementById('button-play-ut').addEventListener('click', function() {
          var i;
          var perfMarks;
          var perfMeasures;

          document.getElementById('ut-results').classList.remove('hidden');
          // A time consuming function
          performance.mark("startTime1");
          for(i = 0; i < parseInt(document.getElementById('count-ut-1').value); i++);
            performance.mark("endTime1")

            // Another time consuming function
            performance.mark("startTime2");
            for(i = 0; i < parseInt(document.getElementById('count-ut-2').value); i++);
              performance.mark("endTime2");
              performance.measure("durationTime1", "startTime1", "endTime1");
              performance.measure("durationTime2", "startTime2", "endTime2");
              performance.measure("durationTimeTotal", "startTime1", "endTime2");

              // Print marks
              perfMarks = performance.getEntriesByType("mark");
              document.getElementById('ut-marks').innerHTML = '';
              for (i = 0; i < perfMarks.length; i++) {
                document.getElementById('ut-marks').innerHTML +=
                  "Name: " + perfMarks[i].name + " - " +
                  "Start Time: " + perfMarks[i].startTime + "<br />";
              }

              // Print measures
              perfMeasures = performance.getEntriesByType("measure");
              document.getElementById('ut-measures').innerHTML = '';
              for (i = 0; i < perfMeasures.length; i++) {
                document.getElementById('ut-measures').innerHTML +=
                  "Name: " + perfMeasures[i].name + " - " +
                  "Duration: " + perfMeasures[i].duration + "<br />";
              }
              performance.clearMarks();
              performance.clearMeasures();
        });
      }
    </script>
  </body>
</html>
```

## 结论

本文探讨了用户计时 API，并展示了它如何帮助您测试 JavaScript 代码的性能。表现真的很重要，我们应该为哪怕是最微小的进步而奋斗。

这个 API 没有引入太多的概念，所以你应该不难理解它的属性和方法。此外，它在浏览器中的支持非常好，所以您现在可以可靠地使用它。然而，对于那些不支持用户计时 API 的应用程序(尤其是 Firefox ),可以使用 polyfill。

## 分享这篇文章