# 组件思维

> 原文：<https://www.sitepoint.com/thinking-components/>

Web 组件、React、Polymer、Flight——都是用来构建界面组件的。这是一个不同于大型 MVC 和 MVVM 框架的工具集，在计划如何实现接口时需要不同的思维方式。虽然我仍然在服务器应用程序中使用像 MVC 这样的模型，但是我已经开始致力于使用组件方法进行界面开发了。在本文中，我将概述组件思维与 MVC 思维的不同之处，并在一个真实的例子中实现这种方法。

在我看来，MVC 设计的顶部空间是“我如何对我的业务领域建模？我如何对与那个领域交互的过程建模？我如何对接口建模以促进这些过程？”。我认为这个顶部空间不利于良好的组件设计。事实上，当你着手将一个接口分解成可组合的组件时，这与你应该思考的方式截然相反。你最多只能得到微型应用。在最坏的情况下，你会建立上帝组件。您最不想做的事情就是将您的业务领域建模为组件。你的目标应该是建模你能描述的最小的交互抽象部分。

## 为重复使用而设计

而不是“我如何制作这个可忽略的警告面板？”，问自己“如果我添加新的 HTML 元素来促进这种交互，它们会是什么？”。我发现这导致组件安全地远离业务领域，并且本质上在不同的上下文中是最可重用的。

作为另一个例子，不要制作一个可以在任何地方使用的预输入帮助搜索组件，而是制作一个提示性的文本输入组件，它知道在提供输入建议时所涉及的交互。然后创建一个帮助搜索 API 数据组件，它知道如何接收数据请求、与帮助搜索 API 交互以及广播结果。现在，您的提示性文本输入的测试不需要任何 API 的模仿，当您被要求向“标签”字段添加建议时，您可以加入现有的提示性文本输入组件，连接一个与标签 API 对话的简单数据组件，然后就完成了！

## 实际例子——“项目列表”

对于一个具体的例子，让我们来看看如何将一个简单的接口实现为独立的组件。以下模型摘自[99 设计一对一项目](http://99designs.com/projects)系统。虽然 UI 已经被极大地简化了，但是我们将要构建的 JavaScript 是在编写的时候从我们的站点生成的代码。这里是线框:

![Wireframe](img/c793da49bdc092ee7f525ace792d1674.png)

我们可以在三个项目列表之间导航——活动、草稿和存档。每个项目都有一个可以对其执行的操作—归档活动项目、删除草稿或重新激活已归档的项目。在应用程序设计思维中，我们将开始对一个项目建模，并给它一些方法，如“存档”和“删除”，以及一个“状态”属性来跟踪它属于三个列表中的哪一个。将这种推理引入组件设计正是我们想要避免的，所以我们将只关心交互以及促进交互需要什么。

它的核心是每行有一个动作。当执行该操作时，我们希望从列表中删除该行。我们已经摆脱了任何特定于项目的领域知识！此外，我们还计算了每个列表中有多少项。为了限制本文的范围，我们假设每个页面都是在服务器端生成的，选项卡导航会导致整个页面刷新。由于我们不需要强制依赖 JavaScript，我们的动作按钮将是带有`submit`事件处理程序的`form`元素，这些事件处理程序将异步执行表单的动作，并在完成时广播一个事件。

以下是针对单个项目行的一些 HTML:

```
<li>
  <a href="/projects/99" title="View project">Need sticker designs for XYZ Co.</a>
  <div class="project__actions">
    <a href="/projects/99" class="button">View</a>
    <form class="action" action="/projects/99/archive" method="post">
        <button>Archive</button>
    </form>
  </div>
</li>
```

我将利用[航班](http://flightjs.github.io/)来制造我们的组件。Flight 目前是我们 99designs 的默认 JS 组件库，原因我在[我之前的 SitePoint JavaScript 文章](https://www.sitepoint.com/taking-flight-twitter-flight/)中已经描述过了。

这是我们用于处理表单提交和广播事件的`AsyncForm`组件:

```
define(function(require) {
  'use strict';

  var defineComponent = require('flight/lib/component');

  function AsyncForm() {
    this.defaultAttrs({
      broadcastEvent: 'uiFormProcessed'
    });

    this.after('initialize', function() {
      this.on(this.node, 'submit', this.asyncSubmit.bind(this));
    });

    this.asyncSubmit = function(event) {
      event.preventDefault();
      $.ajax({
        'url': this.$node.attr('action'),
        'dataType': 'json',
        'data': this.$node.serializeArray(),
        'type': this.$node.attr('method')
      }).done(function(response, data) {
        this.$node.trigger(this.attr.broadcastEvent, data);
      }.bind(this)).fail(function() {
        // error handling excluded for brevity
      });
    };
  }

  return defineComponent(AsyncForm);
});
```

我们坚持严格的策略，从不为 JavaScript 使用`class`属性，所以我们将为动作表单添加一个`data-async-form`属性，并将组件附加到所有匹配的表单，如下所示:

```
AsyncForm.attachTo('[data-async-form]');
```

现在我们有能力执行动作，并广播一个事件，该事件将在成功时沿 DOM 树向上传播。下一步是监听该事件，并删除它冒泡到的行。为此我们有`Removable`:

```
define(function(require) {
  'use strict';

  var defineComponent = require('flight/lib/component');

  function Removable() {
    this.defaultAttrs({
      'removeOn': 'uiFormProcessed'
    });

    this.after('initialize', function() {
      this.on(this.attr.removeOn, this.remove.bind(this));
    });

    this.remove = function(event) {
      // Animate row removal, remove DOM node, teardown component
      $.when(this.$node
        .animate({'opacity': 0}, 'fast')
        .slideUp('fast')
      ).done(function() {
        this.$node.remove();
      }.bind(this));
    };
  }

  return defineComponent(Removable);
});
```

我们再次向项目行添加一个`data-removable`属性，并将组件附加到行元素:

```
Removable.attachTo('[data-removable]');
```

搞定了。两个小组件，每个组件有一个事件，我们在三个表单中处理了三种类型的动作，处理方式是适度降级的。只剩下一件事了，那就是我们每张账单的数目。这应该很容易，我们所需要的就是每删除一行就将活动选项卡的计数减一。但是等等！当一个活动项目被存档时，存档的计数需要增加，当一个存档的项目被重新激活时，激活的计数需要增加。首先让我们制作一个可以接收指令来改变其编号的`Count`组件:

```
define(function(require) {
  'use strict';

  var defineComponent = require('flight/lib/component');

  function Count() {
    this.defaultAttrs({
      'event': null
    });

    this.after('initialize', function() {
      this.on(document, this.attr.event, this.update.bind(this));
    });

    this.update = function(event, data) {
      this.$node.text(
        parseInt(this.$node.text(), 10) + data.modifier
      );
    }
  }

  return defineComponent(Count);
});
```

我们的`Count`将在 HTML 中表示为类似于`<span data-count>4</span>`的东西。因为`Count`在`document`级别监听事件，我们将把它的`event`属性设为`null`。这将强制使用它来定义该实例应该监听的事件，并防止意外地有多个`Count`实例监听同一个事件的指令。

```
Count.attachTo(
  '[data-counter="active"]',
  {'event': 'uiActiveCountChanged'}
);

Count.attachTo(
  '[data-counter="draft"]',
  {'event': 'uiDraftCountChanged'}
);

Count.attachTo(
  '[data-counter="archived"]',
  {'event': 'uiArchivedCountChanged'}
);
```

难题的最后一部分是让我们的`Removable`实例在被移除时触发一个事件，并修改它们各自的计数器。我们当然不希望组件之间有任何耦合，所以我们给`Removable`一个属性，它是一个事件数组，当它被移除时触发:

```
define(function(require) {
  'use strict';

  var defineComponent = require('flight/lib/component');

  function Removable() {
    this.defaultAttrs({
      'removeOn': 'uiFormProcessed',
      'broadcastEvents': [
        {'event': 'uiRemoved', 'data': {}}
      ]
    });

    this.after('initialize', function() {
      this.on(this.attr.removeOn, this.remove.bind(this));
    });

    this.remove = function(event) {
      // Broadcast events to notify the rest of the UI that this component has been removed
      this.attr.broadcastEvents.forEach(function(eventObj) {
        this.trigger(eventObj.event, eventObj.data);
      }.bind(this));

      // Animate row removal, remove DOM node, teardown component
      $.when(this.$node
        .animate({'opacity': 0}, 'fast')
        .slideUp('fast')
      ).done(function() {
        this.$node.remove();
      }.bind(this));
    };
  }

  return defineComponent(Removable);
});
```

现在，`Count`和`Removable`之间的耦合发生在用例特定的页面脚本中，我们将组件附加到 DOM:

```
define(function(require) {
  'use strict';

  var AsyncForm = require('component_ui/async-form');
  var Count = require('component_ui/count');
  var Removable = require('component_ui/removable');

  $(function() {

    // Enhance action forms
    AsyncForm.attachTo('[data-async-form]');

    // Active Projects
    Count.attachTo(
      '[data-counter="active"]',
      {'event': 'uiActiveCountChanged'}
    );

    Removable.attachTo('[data-removable="active"]',
      {
        'broadcastEvents': [
          {
            'event': 'uiArchivedCountChanged',
            'data' : {'modifier' : 1}
          },
          {
            'event': 'uiActiveCountChanged',
            'data' : {'modifier' : -1}
          }
        ]
      }
    );

    // Draft Projects
    Count.attachTo(
      '[data-counter="drafts"]',
      {'event': 'uiDraftCountChanged'}
    );

    Removable.attachTo(
      '[data-removable="drafts"]',
      {
       'broadcastEvents': [
          {
            'event': 'uiDraftCountChanged',
            'data' : {'modifier' : -1}
          }
        ]
      }
    );

    // Archived Projects
    Count.attachTo('[data-counter="archived"]',
      {'event': 'uiArchivedCountChanged'}
    );

    Removable.attachTo('[data-removable="archived"]',
      {
        'broadcastEvents': [
          {
            'event': 'uiArchivedCountChanged',
            'data' : {'modifier' : -1}
          },
          {
            'event': 'uiActiveCountChanged',
            'data' : {'modifier' : 1}
          }
        ]
      }
    );
  });
});
```

任务完成。我们的计数器对我们的项目列表行一无所知，而项目列表行对它们内部的表单一无所知。没有一个组件是围绕项目列表的概念设计的。

## 最后一分钟添加

我们的 UX 设计师指出，如果有人试图删除草稿时我们要求确认会更好，因为这一操作无法撤消。没问题，我们可以快速创建一个组件来完成这个任务:

```
define(function(require) {
  'use strict';

  var defineComponent = require('flight/lib/component');

  function Confirm() {
    this.defaultAttrs({
      'event': 'click'
    });

    this.after('initialize', function() {
      this.$node.on(this.attr.event, this.confirm.bind(this));
    });

    this.confirm = function(e, data) {
      if (window.confirm(this.$node.data('confirm'))) {
        return true;
      } else {
        e.preventDefault();
      }
    };
  }

  return defineComponent(Confirm);
});
```

将它附加到删除按钮上，我们就得到了想要的东西。如果用户选择“OK ”,确认对话框将截取按钮，并允许表单提交。我们不必改变我们的`AsyncForm`组件，因为我们可以组合这些组件而不会互相干扰。在我们的产品代码中，我们还在 action 按钮上使用了一个`SingleSubmit`组件，它给出了表单已经提交的视觉反馈，并防止了多次提交。

## 最终组件、测试和夹具

希望本文已经展示了将接口分解成可组合的组件可以如何让您的项目受益。组件设计的一个我没有提到的重要好处是它们易于隔离测试，所以这里是最终的组件以及它们的 jasmine 测试和 HTML 测试夹具:

*   [异步形式](https://gist.github.com/andrewk/8841aa648207c54f5608)
*   [可拆卸](https://gist.github.com/andrewk/992abf729227609ba695)
*   [计数](https://gist.github.com/andrewk/c9a3a431624e910d9985)
*   [确认](https://gist.github.com/andrewk/db8a7a6cb24bb0e13a4a)

如果你对我所介绍的内容有任何疑问，请在评论中询问细节，我会尽力帮助你。

## 分享这篇文章