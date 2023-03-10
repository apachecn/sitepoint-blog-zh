# 如何使用普通 JavaScript 编写 Atom 包

> 原文：<https://www.sitepoint.com/write-atom-packages-using-vanilla-javascript/>

*这篇文章由[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

Atom 是一个现代的、核心的可编程编辑器。这很棒，但是对于不熟悉 [CoffeeScript](http://coffeescript.org/) 的开发者来说，很难理解文档。理解 Atom 的生态系统可能会变得令人困惑。让我们来看一下用 JavaScript 编写 Atom 包的各个方面。

## 理解原子

Atom 是一个基于 [Node.js](https://nodejs.org/) 和 [Chromium](https://www.chromium.org/) 的应用，用 GitHub 的 [Electron](http://electron.atom.io/) 框架编写。这意味着它在技术上是一个运行在桌面上的网络应用程序。Atom 的内部功能被分解成[个微小的核心包](https://github.com/atom)；它们的开发方式与社区中的任何其他包一样。虽然它们都是用 CoffeeScript 编写的，但是也可以用普通的 JavaScript 编写，或者通过 [Babel](https://babeljs.io/) 进行转换。

## 通过 Babel 激活对 ES2015 的全面支持

Babel 是一个[源码到源码的编译器](https://en.wikipedia.org/wiki/Source-to-source_compiler)；将 ECMAScript 2015(以前称为 ES6)代码转换为 ECMAScript 5 代码。由于环境是 Chromium，已经有很多[支持的 ES2015 特性](http://kangax.github.io/compat-table/es6/)可用。但是，与其总是查找实现了哪些，我建议使用 Babel 来传输您的代码。在以后的版本中——当 Chromium 更好地支持 ES2015 时——你可以再次停用 Babel，并保持你的代码库(几乎)不变。

要用 Babel 激活 transpiling，每个文件的开头都需要一个`'use babel';`语句，类似于 ECMAScript 5 中的[严格模式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)。这也使您能够通过省略语句来决定哪些文件应该传输，哪些不应该传输。

## package.json

将 Atom 包视为 npm 模块会有所帮助。您可以像 Node.js 上运行的任何工具一样访问 API。因此，可以添加任何所需的 npm 依赖项。还需要一个 [`package.json`](https://docs.npmjs.com/files/package.json) ，包含项目的所有元数据。基本文件应该如下:

```
{
  "name": "your-package",
  "main": "./lib/main",
  "version": "0.1.0",
  "description": "A short description of your package",
  "keywords": [
    "awesome"
  ],
  "repository": "https://github.com/<your-name>/<package>",
  "license": "MIT",
  "engines": {
    "atom": ">=1.0.0 <2.0.0"
  },
  "dependencies": {
  }
} 
```

重要的键是`main` —定义您的包的主入口点(默认为`index.js`/`index.coffee`)—`engines`—告诉 Atom 您的包在哪个版本上运行。还有一组可选键可用，“wordcount”包文档*(第`package.json` )* 中的[文档](https://atom.io/docs/latest/hacking-atom-package-word-count)。

## 包源代码

你所有的包代码都属于顶层目录`lib/`。我建议把你的入口点也放在这个文件夹里，因为这样可以保持结构的整洁，也便于浏览项目。

主文件必须是维护包的整个生命周期的单例对象。即使您的包只包含一个视图，它也将从这个对象进行管理。你的入口点需要一个`activate()`方法，但是还应该有*可选* `deactivate()`和`serialize()`。

```
// lib/main.js
'use babel';

// This is your main singleton.
// The whole state of your package will be stored and managed here.
const YourPackage = {
  activate (state) {
    // Activates and restores the previous session of your package.
  },
  deactivate () {
    // When the user or Atom itself kills a window, this method is called.
  },
  serialize () {
    // To save the current package's state, this method should return
    // an object containing all required data.
  }
};

export default YourPackage; 
```

### 激活您的包

`activate()`函数是唯一的**必需的**方法。在这里初始化你所有的模块、视图或助手。它被传递一个对象，该对象包含您的包的前一个序列化状态。如果你不序列化你的包中的任何东西，它将是一个空对象。这意味着，序列化什么完全取决于您和您的包架构。

### 使不活动

方法是可选的，但是很重要。当窗口关闭时，或者用户在设置中取消激活它时，Atom 将调用它。当你的包被用户停用，并且你没有处理添加的事件/命令，**它们仍然可用**。当 Atom 关闭窗口时，这不是问题。它会分解事件和命令。但是如果你的包正在看文件或者做其他工作，你应该在`deactivate()`中释放它们。

## 事件订阅

一个包通常订阅多个事件，如添加自定义命令、监听更改或修改文件。可以将这些绑定到一个 [`CompositeDisposable()`](https://atom.io/docs/api/latest/CompositeDisposable) 的实例中，这样就可以一次性处理掉它们。

```
// lib/main.js
import { CompositeDisposable } from 'atom';

const YourPackage = {
  subscriptions: null,

  activate (state) {
    // Assign a new instance of CompositeDisposable...
    this.subscriptions = new CompositeDisposable();

    // ...and adding commands.
    this.subscriptions.add(
      atom.commands.add('atom-workspace', {
        'your-package:toggle': this.togglePackage
      })
    );
  },

  // When your package get's deactivated, all added
  // subscriptions will be disposed of at once.
  deactivate () {
    this.subscriptions.dispose();
  },

  togglePackage () {
    // Code to toggle the package state.
  }
}; 
```

## 把所有的东西连载！

序列化是 Atom 包的一个强大但可选的特性。当窗口关闭、刷新或从以前的会话恢复时，会发生序列化/反序列化。由您来定义哪些组件以及有多少组件应该序列化它们的数据。重要的是它返回 JSON。如果您有一个视图，并且希望它能够被刷新，那么您需要使它与序列化和反序列化兼容。

这个非常基本的组件接受一个对象，该对象将被用作组件的内部数据。然后，您的组件可能会对数据做一些工作，并允许通过`serialize()`方法对其状态进行序列化。

```
// lib/fancy-component.js
class FancyComponent {
  constructor (configData) {
    this.data = configData;
  }

  // This method will be called when the class
  // is restored by Atom.
  static deserialize (config) {
    return new FancyComponent(config);
  }

  // The returned object will be used to restore
  // or save your data by Atom.
  // The "deserializer" key must be the name of your class.
  serialize () {
    return {
      deserializer: 'FancyComponent',
      data: this.data
    };
  }

  doSomethingWithData () {}
}

// Add class to Atom's deserialization system
atom.deserializers.add(FancyComponent);

export default FancyComponent; 
```

为了使这些有用，这个组件必须在你的包 main singleton 中被调用和序列化。

```
// lib/main.js
import FancyComponent from './fancy-component';
import SomeView from './some-view';

const YourPackage = {
  fancyComponent: null,
  someView: null,

  activate (state) {
    // If the component has been saved at a previous session of Atom,
    // it will be restored from the deserialization system. It calls your
    // your components static 'deserialize()' method.
    if (state.fancy) {
      this.fancyComponent = atom.deserializers.deserialize(state.fancy);
    }
    else {
      this.fancyComponent = new FancyComponent({ otherData: 'will be used instead' });
    }

    // More activation logic.
  },

  // As well as your component, your package has a serialize method
  // to save the current state.
  serialize () {
    return {
      fancy: this.fancyComponent.serialize(),
      view: this.someView.serialize()
    };
  }
}; 
```

所有想要序列化的对象都需要使用`serialize()`方法。它必须返回一个“可序列化的对象”，以及一个带有已注册的反序列化器名称的`deserializer`键。根据 Atom，[“它通常是类本身的名字”](https://atom.io/docs/latest/behind-atom-serialization-in-atom)。除此之外，一个类还需要静态的`deserialize()`方法。此方法将对象从以前的状态转换为真正的对象。

为了使这一切成为可能，您必须用`atom.deserializers.add()`将您的类添加到反序列化系统中。

## 窗格和视图

窗格是 Atom 中的单个窗口。它包含所有打开的选项卡，称为“项目”。这些窗格存储在`atom.workspace`对象中。用`atom.workspace.getActivePane()`你正在请求当前的活动窗格。pane 对象不包含任何 DOM 元素，而是包含 Atom 内部组件的所有实例(例如 [`TextEditor`](https://atom.io/docs/api/latest/TextEditor) 、 [`GutterContainer`](https://atom.io/docs/api/latest/Gutter) 、 [`NotificationManager`](https://atom.io/docs/api/latest/NotificationManager) )。理解这些窗格对于为包创建自定义视图至关重要。

您想要添加的视图或任何其他自定义 UI 元素必须用 JavaScript 创建。Atom 完全是用 [**Web 组件**](http://www.html5rocks.com/en/tutorials/webcomponents/customelements/) 构建的，但你不必那样做。定制模式的一个非常基本的例子如下。

```
// lib/custom-view-element.js
export default class YourPackageView {
  constructor (state) {
    this.data = state;
    this.element = document.createElement('div');
    this.message = document.createElement('span');
    this.textNode = document.createTextNode(this.data.content);

    this.element.classList.add('your-package');
    this.message.classList.add('your-package-message');

    this.message.appendChild(this.textNode);
    this.element.appendChild(this.message);
  }

  serialize () {
    return {
      data: this.data
    };
  }

  destroy () {
    this.element.remove();
  }

  getElement () {
    return this.element;
  }

  doSomethingWithData () {}
} 
```

```
// lib/main.js
import YourPackageView from './custom-view-element';
import { CompositeDisposable } from 'atom';

const YourPackage = {
  subscriptions: null,
  packageView: null,
  modal: null,

  activate (state) {
    this.subscriptions = new CompositeDisposable();
    // We don't use the serialization system here because we assume
    // that our view won't work with any data.
    this.packageView = new YourPackageView(state.viewState);
    // Here we add the custom view to the modal panel of Atom.
    this.modal = atom.workspace.addModalPanel({
      item: this.packageView.getElement(),
      visible: false
    });

    this.subscriptions.add(
      atom.commands.add('atom-workspace', {
        'your-package:toggle-modal': this.toggleModal()
      })
    );
  },

  // We destroy both the custom view and Atom's modal.
  deactivate () {
    this.subscriptions.dispose();
    this.packageView.destroy();
    this.modal.destroy();
  },

  serialize () {
    return {
      viewState: this.packageView.serialize()
    };
  },

  toggleView () {
    if (this.modal.isVisible()) {
      this.modal.hide();
    }
    else {
      this.modal.show();
    }
  },

  doSomethingElseWithView () {}
};

export default YourPackage; 
```

`atom.workspace.addModalPanel()`方法向 Atom 的工作空间添加一个模态元素。如果你想添加一个自定义视图到一个窗格中(比如一个设置页面)，还需要做更多的工作。

## 使包可配置

包配置应该在 [JSON 模式](http://json-schema.org/)中描述。要添加设置，您的包对象需要一个带有数据的`config`键。或者，您可以将配置移动到一个`config-schema.json`文件中并`import`它。这使您的配置保持独立，您的架构保持有序。

```
// lib/config-schema.json
{
  "activateHyperMode": {
    "description": "Turns the package into hyper mode.",
    "type": "boolean",
    "default": false
  },
  "setRange": {
    "type": "integer",
    "default": 42,
    "minium": 1,
    "maximum": 9000
  }
} 
```

```
// lib/main.js
import packageConfig from './config-schema.json';

const YourPackage = {
  config: packageConfig,
  subscriptions: null,

  activate (state) {
    // ...
  }
}; 
```

这将自动在包的设置页面上创建配置。可以在 Atom 的 API 文档的[配置页面](https://atom.io/docs/api/latest/Config)上找到所有支持类型的列表。您的设置对象，以及所有其他的包配置，都存储在`atom.config`对象中。

### 获取和设置

您可以使用`get()`和`set()`方法获取和设置配置中的任何键。也可以获取 Atom 的常规设置或其他包的设置。如果你想和其他包交互，你需要[提供和消费服务](https://atom.io/docs/latest/behind-atom-interacting-with-other-packages-via-services)。

```
atom.config.get('yourPackage'); // Returns the entire configuration object
atom.config.get('yourPackage.activateHyperMode'); // Returns false
atom.config.get('core.fileEncoding'); // Returns 'utf8'
atom.config.get('differentPackage');

atom.config.set('yourPackage.activateHyperMode', true);
atom.config.set('yourPackage.myNewValue', 'value'); 
```

### 倾听变化

为了监听变化，你可以通过[观察](https://atom.io/docs/api/latest/Config#instance-observe)配置的变化，或者让一个监听器——称为[`onDidChange()`](https://atom.io/docs/api/latest/Config#instance-onDidChange)——监听一个关键路径。两者都返回一个[一次性](https://atom.io/docs/api/latest/Disposable)你可以`.dispose()`退订。

同样，将它们添加到`CompositeDisposable`的实例允许您一次处理多个事件:

```
this.subscriptions = new CompositeDisposable();

this.subscriptions.add(
  atom.config.observe('core.themes', value => {
    // do something
  })
);

this.subscriptions.add(
  atom.config.onDidChange('yourPackage', ({oldValue, newValue}) => {
    // do something
  })
); 
```

或者，单独处理它们:

```
const observeConfig = atom.config.observe('core.themes', doSomethingCool);
const onChangeConfig = atom.config.onDidChange('yourPackage', doSomethingOnChange);

// later in your code
observeConfig.dispose();
onChangeConfig.dispose(); 
```

## 使用菜单和键盘映射进行微调

菜单和键映射使得用户可以在 Atom 环境中访问包的功能。它们链接到界面的特定命令。如果你的包可以切换，打开一个视图，做一些自定义动作或其他任何事情，它应该对用户可用。

### 添加菜单

菜单定义既可以作为 JSON 文件存储在`menus/`顶层目录中，也可以存储在您的`package.json`的`menus`键中。以下示例向编辑器的`Packages`菜单栏和上下文菜单添加命令。右键单击编辑器内部时，会出现上下文菜单。

```
// menus/your-package.json
"menu": [
  {
    "label": "Packages",
    "submenu": [
      {
        "label": "Your Package",
        "submenu": [
          {
            "label": "Toggle",
            "command": "your-package:toggle"
          }, {
            "label": "Settings",
            "command": "your-package:show-settings"
          }
        ]
      }
    ]
  }
],
"context-menu": {
  "atom-text-editor": [
    {
      "label": "Toggle Your Package",
      "command": "your-package:toggle"
    }
  ]
} 
```

### 键盘映射

使用键盘映射，您可以定义软件包命令的快捷方式。它们被绑定到一个特定的范围，这个范围是一个 CSS 选择器，比如`atom-text-editor`、`atom-text-editor:not([mini])`或者`atom-workspace`。当与选择器匹配的元素处于焦点上，并且使用了击键模式时，将发出您的自定义操作。

```
// keymaps/your-package.json
{
  "atom-text-editor": {
    "alt-shift-e": "your-package:toggle",
    "cmd-ctrl-alt-shift-enter-backspace": "your-package:do-something-crazy"
  },
  "atom-text-editor[mini]": {
    "tab-escape": "your-package:transform"
  }
} 
```

请记住，这些命令必须已经在您的入口点中注册(`atom.commands.add()`)。

## 使用 Chrome 开发工具进行调试

在 Atom 中调试与在 web 上调试没有太大区别。你可以激活`View > Developer > Toggle Developer Tools`下的 Chrome 开发者工具来查看抛出的错误、代码日志或者了解 Atom 的标记。

![Chrome Developer Tools in Atom Editor](img/15fc7d8bfc926277eb26de1bbdce0cf2.png)

## 用 Jasmine 进行单元测试

Atom 使用 [Jasmine](http://jasmine.github.io/) 框架进行测试。测试放在`spec/`顶层目录中，**内的文件必须**以`-spec`结束(如`fancy-component-spec.js`)。运行或发布包并不需要测试，但测试是保证代码质量并确保添加新特性时不会出现问题的好方法。

要运行您的测试，您可以使用`window:run-package-specs`命令或者转到`View > Developer > Run Package Specs`。

如果你想在 Travis CI T1 上运行你的软件包规范，Atom 的博客上有一个简短的 T2 帖子 T3，告诉你如何设置。

## 包装流程

这是一个很大的投入。Atom 的实际流程或执行顺序大致如下(注意:测试不是包流程的一部分)。

1.  Atom 启动并读取您的`package.json`
    *   应用菜单、键映射、样式表和所有其他配置
    *   如果`activationCommands`被定义，它们将被运行
2.  执行主入口点(即`activate()`)
    *   您的软件包魔力(例如，对用户输入做出反应、创建视图、修改文件)开始发挥作用
3.  您停用您的软件包或关闭 Atom
    *   Atom 序列化包状态

## 结论

我希望我的文章能够帮助您对 Atom 包开发有一个基本的了解。还有更多的功能和主题，不幸的是无法在一篇文章中涵盖。看一看[原子飞行手册](https://atom.io/docs/api/v1.4.1/)看看还有什么可能。

你会开发什么包？

## 分享这篇文章