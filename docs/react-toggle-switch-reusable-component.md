# 在 React 中创建一个切换开关作为可重用组件

> 原文：<https://www.sitepoint.com/react-toggle-switch-reusable-component/>

在本文中，我们将使用 React 创建一个受 iOS 启发的拨动开关。这将是一个小型的独立组件，您可以在未来的项目中重用它。我们还将构建一个简单的演示 React 应用程序，它使用我们的自定义 toggle switch 组件。

我们可以为此使用第三方库，但是从头构建允许我们更好地理解我们的代码是如何工作的，并且允许我们完全定制我们的组件。

表单提供了支持用户交互的主要手段。复选框传统上用于采集二进制数据——如*是*或*否*、*真*或*假*、*使能*或*禁用*、*开*或*关*等。尽管一些现代的界面设计在创建切换开关时避开了表单域，但是由于它们更容易访问，我还是坚持使用它们。

这是我们将要构建的组件的屏幕截图:

![Two small and two large toggle switches in both a checked and unchecked state](img/b8c1356655cde4a228e51f6268797f0c.png)

## 入门指南

让我们使用 Create React App 来快速启动和运行 React 应用程序。如果您不熟悉 Create React 应用程序，请查看我们的[入门指南](https://www.sitepoint.com/create-react-app/)。

```
create-react-app toggleswitch 
```

一旦所有东西都安装好了，就切换到新创建的目录，用`yarn start`(或者如果您喜欢的话用`npm start`)启动服务器。这将在 [http://localhost:3000](http://localhost:3000) 启动开发服务器。

接下来，在`src`目录中创建一个`ToggleSwitch`目录。这是我们制造组件的地方:

```
mkdir src/ToggleSwitch 
```

在这个目录中，创建两个文件:`ToggleSwitch.js`和`ToggleSwitch.scss`:

```
touch ToggleSwitch.js ToggleSwitch.scss 
```

最后，将`App.js`修改如下:

```
import React from 'react';
import ToggleSwitch from './ToggleSwitch/ToggleSwitch'

function App() {
  return (
    <ToggleSwitch />
  );
}

export default App; 
```

## 加价

我们可以从一个基本的 HTML 复选框输入表单元素及其必要的属性集开始:

```
<input type="checkbox" name="name" id="id" /> 
```

为了围绕它进行构建，我们可能需要一个带有`class`、`<label>`和`<input />`控件本身的封闭`<div>`。将所有内容相加，我们可能会得到这样的结果:

```
<div class="toggle-switch">
  <input type="checkbox" class="toggle-switch-checkbox" name="toggleSwitch" id="toggleSwitch" />
  <label class="toggle-switch-label" for="toggleSwitch">
    Toggle Me!
  </label>
</div> 
```

最终，我们可以去掉标签文本，使用`<label>`标签来选中或取消选中复选框输入控件。在`<label>`内部，让我们添加两个`<span>`标签，帮助我们构建开关支架和拨动开关本身:

```
<div class="toggle-switch">
  <input type="checkbox" class="toggle-switch-checkbox" name="toggleSwitch" id="toggleSwitch" />
  <label class="toggle-switch-label" for="toggleSwitch">
    <span class="toggle-switch-inner"></span>
    <span class="toggle-switch-switch"></span>
  </label>
</div> 
```

## 转化为反应组分

现在我们知道了 HTML 需要包含什么，我们需要做的就是将 HTML 转换成 React 组件。让我们从这里的一个基本组件开始。我们将使它成为一个类组件，然后我们将把它转换成钩子，因为对于新开发人员来说，使用`state`比`useState`更容易。

将以下内容添加到`src/ToggleSwitch/ToggleSwitch.js`:

```
import React, { Component } from "react";

class ToggleSwitch extends Component {
  render() {
    return (
      <div className="toggle-switch">
        <input
          type="checkbox"
          className="toggle-switch-checkbox"
          name="toggleSwitch"
          id="toggleSwitch"
        />
        <label className="toggle-switch-label" htmlFor="toggleSwitch">
          <span className="toggle-switch-inner" />
          <span className="toggle-switch-switch" />
        </label>
      </div>
    );
  }
}

export default ToggleSwitch; 
```

此时，由于`id`的重复，不可能在同一个视图或同一个页面上有多个 toggle switch 滑块。我们可以在这里利用 React 的组件化方式，但在这个实例中，我们将使用`props`来动态填充值:

```
import React, { Component } from 'react';

class ToggleSwitch extends Component {
  render() {
    return (
      <div className="toggle-switch">
        <input
          type="checkbox"
          className="toggle-switch-checkbox"
          name={this.props.Name}
          id={this.props.Name}
        />
        <label className="toggle-switch-label" htmlFor={this.props.Name}>
          <span className="toggle-switch-inner" />
          <span className="toggle-switch-switch" />
        </label>
      </div>
    );
  }
}

export default ToggleSwitch; 
```

`this.props.Name`将动态填充`id`、`name`和`for`(注意是 React JS 中的`htmlFor`)的值，这样你就可以给组件传递不同的值，并且在同一个页面上有多个实例。还要注意的是，`<span>`标签没有结束`</span>`标签。相反，它像`<span />`一样被封闭在起始标签中，就 JSX [而言，这完全没问题](https://react-cn.github.io/react/tips/self-closing-tag.html)。

通过如下更改`App.js`的内容进行测试:

```
function App() {
  return (
    <>  <ToggleSwitch Name='newsletter' />  <ToggleSwitch Name='daily' />  <ToggleSwitch Name='weekly' />  <ToggleSwitch Name='monthly' />  </>
  );
} 
```

在 http://localhost:3000/(可能使用您的浏览器开发工具)检查结果输出，并确保一切正常工作。

## 造型和 SCSS

我最近写了关于[对 React 组件](https://www.sitepoint.com/react-components-styling-options/)进行样式化的文章，其中我比较了这种可能性的各种方式。在那篇文章中，我得出结论，SCSS 是最好的方法，这就是我们在这里要用的方法。

为了让 SCSS 使用 Create React App，您需要安装 [node-sass 包](https://www.npmjs.com/package/node-sass):

```
yarn add node-sass 
```

我们还需要将正确的文件导入到组件中:

```
// ToggleSwitch.js

import React, { Component } from 'react';
import './ToggleSwitch.scss';
... 
```

现在是造型。这是我们追求的目标的大致轮廓:

*   默认情况下，开关只有`75px`宽，垂直对齐`inline-block`，这样它就和文本在一条线上，不会引起布局问题。
*   我们将确保该控件是不可选择的，这样用户就不能拖放它。
*   我们将隐藏最初的复选框输入。
*   `::after`和`::before`伪元素都需要进行样式化并做成元素，以便将它们放入 DOM 并进行样式化。
*   我们还将添加一些 CSS 过渡，以获得很酷的动画效果。

这是它在 SCSS 的样子。将以下内容添加到`src/ToggleSwitch/ToggleSwitch.scss`:

```
.toggle-switch {
  position: relative;
  width: 75px;
  display: inline-block;
  vertical-align: middle;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  text-align: left;
  &-checkbox {
    display: none;
  }
  &-label {
    display: block;
    overflow: hidden;
    cursor: pointer;
    border: 0 solid #bbb;
    border-radius: 20px;
    margin: 0;
  }
  &-inner {
    display: block;
    width: 200%;
    margin-left: -100%;
    transition: margin 0.3s ease-in 0s;
    &:before,
    &:after {
      display: block;
      float: left;
      width: 50%;
      height: 34px;
      padding: 0;
      line-height: 34px;
      font-size: 14px;
      color: white;
      font-weight: bold;
      box-sizing: border-box;
    }
    &:before {
      content: "Yes";
      text-transform: uppercase;
      padding-left: 10px;
      background-color: #f90;
      color: #fff;
    }
  }
  &-disabled {
    background-color: #ddd;
    cursor: not-allowed;
    &:before {
      background-color: #ddd;
      cursor: not-allowed;
    }
  }
  &-inner:after {
    content: "No";
    text-transform: uppercase;
    padding-right: 10px;
    background-color: #bbb;
    color: #fff;
    text-align: right;
  }
  &-switch {
    display: block;
    width: 24px;
    margin: 5px;
    background: #fff;
    position: absolute;
    top: 0;
    bottom: 0;
    right: 40px;
    border: 0 solid #bbb;
    border-radius: 20px;
    transition: all 0.3s ease-in 0s;
  }
  &-checkbox:checked + &-label {
    .toggle-switch-inner {
      margin-left: 0;
    }
    .toggle-switch-switch {
      right: 0px;
    }
  }
} 
```

假设您一直在跟随，如果您前往位于 [http://localhost:3000/](http://localhost:3000/) 的 dev 服务器，您现在会看到四个样式精美的切换开关。尝试切换它们；他们都应该工作。

也要花一些时间来浏览上面的代码。如果你有什么不确定的，你可以参考 [Sass 文档](https://sass-lang.com/guide)，或者去[网站论坛](https://www.sitepoint.com/community/)问个问题。

### 动态标签

目前，切换选项是硬编码的:

```
.toggle-switch {
  ...
  &-inner {
    ...
    &:before {
      content: "Yes";
      ...
    }
  }
  ...
  &-inner:after {
    content: "No";
    ...
  }
  ...
} 
```

为了使组件更加灵活，我们可以使用 [HTML5 数据属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/data-*)从控件中动态获取这些属性:

```
&:before {
  content: attr(data-yes);
  ...
}
&-inner:after {
  content: attr(data-no);
  ...
} 
```

我们将为测试硬编码数据属性，但在最终版本中将使其更加灵活:

```
// ToggleSwitch.js

class ToggleSwitch extends Component {
  render() {
    return (
      <div className="toggle-switch"> ... <label className="toggle-switch-label" htmlFor={this.props.Name}>  <span className="toggle-switch-inner" data-yes="Ja" data-no="Nein"/>  <span className="toggle-switch-switch" />  </label>  </div>
    );
  }
} 
```

### 较小的组件版本

此外，对于较小的屏幕，使用不带文本的较小版本的 switch 将是一个很好的主意。因此，让我们为它添加一些最小尺寸的样式，并删除文本:

```
.toggle-switch {
  ...
  &.small-switch {
    width: 40px;
    .toggle-switch-inner {
      &:after,
      &:before {
        content: "";
        height: 20px;
        line-height: 20px;
      }
    }
    .toggle-switch-switch {
      width: 16px;
      right: 20px;
      margin: 2px;
    }
  }
} 
```

关于响应性，我们应该改变完整的大小，所以让我们使用 [CSS 缩放函数](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/scale)。这里，我们讨论了所有基于自举的设备响应宽度:

```
.toggle-switch {
  ...
  @media screen and (max-width: 991px) {
    transform: scale(0.9);
  }
  @media screen and (max-width: 767px) {
    transform: scale(0.825);
  }
  @media screen and (max-width: 575px) {
    transform: scale(0.75);
  }
} 
```

您可以通过将`small-switch`类添加到`ToggleSwitch.js`中的父`<div>`元素来测试这一点:

```
class ToggleSwitch extends Component {
  render() {
    return (
      <div className="toggle-switch small-switch"> ... </div>
    );
  }
} 
```

回到开发服务器，测试您的更改。如果你想对照已完成的 SCSS 文件检查你有什么，[你可以在这里找到](https://github.com/sitepoint-editors/ToggleSwitch/blob/master/ToggleSwitch.scss)。

## SCSS 主题化

由于我们可以在 SCSS 使用变量，在我们的应用程序中添加对多种颜色主题的支持变得更加容易。你可以在“ [Sass Theming:永无止境的故事](https://www.sitepoint.com/sass-theming-neverending-story/)”中了解更多信息。我们将在这里使用一些颜色主题，并将所有的原始颜色改为变量。前三行是一组可配置的颜色，这有助于我们对小控件进行主题化:

```
// Colors
$label-colour: #bbb;
$disabled-colour: #ddd;
$toggle-colour: #2F855A;
$white: #fff;

// Styles
.toggle-switch {
  ...
  &-label {
    ...
    border: 0 solid $label-colour;
  }
  &-inner {
    ...
    &:before {
      ...
      background-color: $toggle-colour;
      color: $white;
    }
  }
  &-disabled {
    background-color: $disabled-colour;
    cursor: not-allowed;
    &:before {
      background-color: $disabled-colour;
      cursor: not-allowed;
    }
  }
  &-inner:after {
    ...
    background-color: $label-colour;
    color: $white;
  }
  &-switch {
    ...
    background: $white;
    border: 0 solid $label-colour;
  }
  ...
} 
```

造型就是这样。现在让我们添加一些交互性。

## 交互和 JavaScript

*请注意，以下部分仅包含解释概念的演示代码。您不应该在此部分更新您的实际`ToggleSwitch`组件。*

我们的基本组件将是一个哑组件(也称为表示组件)，其状态将由父组件或容器(如`form`)控制。我们所说的受控是什么意思？好吧，让我们先来看一个不受控制的版本:

```
import React from 'react';

const ToggleSwitch = () => (
  <div>  <input
      type="checkbox"
      className="toggle-switch-checkbox"
    />  </div>
);

export default ToggleSwitch; 
```

当用户与上面的复选框输入交互时，它会自动在选中和未选中状态之间切换，而无需我们编写任何 JavaScript。HTML 输入元素能够管理它们自己的内部状态，它们通过直接更新 DOM 来做到这一点。

然而，在 React 中，建议我们使用[受控组件](https://reactjs.org/docs/forms.html#controlled-components)，如下例所示:

```
import React from 'react';

const ToggleSwitch = ({checked}) => (
  <div>  <input
      type="checkbox"
      className="toggle-switch-checkbox"
      checked={checked}
    />  </div>
);

export default ToggleSwitch; 
```

这里，React 控制复选框输入的状态。与该输入的所有交互都必须通过虚拟 DOM。如果您试图与组件进行交互，什么都不会发生，因为我们还没有定义任何 JavaScript 代码来改变我们传入的属性的值。

为了解决这个问题，我们可以传入一个`onChange`prop——一个在复选框被点击时调用的函数:

```
import React from 'react';

const ToggleSwitch = ({checked, onChange}) => (
  <div>  <input
      type="checkbox"
      className="toggle-switch-checkbox"
      checked={checked}
      onChange={e => onChange(e.target.checked)}
    />  </div>
);

export default ToggleSwitch; 
```

现在，复选框输入是交互式的。用户可以像以前一样打开和关闭组件。这里唯一的区别是状态是由 React 控制的，而不是早期不受控制的版本。通过这样做，我们可以在任何给定的时间通过 JavaScript 轻松地访问组件的状态。我们还可以在声明组件时轻松定义初始值。

现在我们来看看如何使用`ToggleSwitch`组件。下面是一个基于类的简化示例:

```
import React, { Component } from 'react';

class Form extends Component {
  state = { checked : false }

  onChange = newValue => {
    this.setState({ checked: newValue });
  }

  render() {
    return (
      <ToggleSwitch id="toggleSwitch" checked={this.checked} onChange={this.onChange} />
    );
  }
}

export default Form; 
```

现在让我们使用钩子将基于类的组件转换成功能组件:

```
import React, { useState } from 'react';

export default function Form() {
  let [checked, setChecked] = useState(false);

  return (
    <ToggleSwitch id="toggleSwitch" checked={checked} onChange={setChecked} />
  )
} 
```

正如你所看到的，我们通过钩子创建方法使用功能组件，大大减少了行数。

如果 React 中的钩子对你来说是新的，看看我们的指南，“ [React 钩子:如何开始&构建你自己的](https://www.sitepoint.com/react-hooks/)”。

## 正在完成 ToggleSwitch 组件

现在让我们回到我们的`ToggleSwitch`组件。我们需要以下道具:

*   `id`(必选):这是将要传递给复选框输入控件的`id`。没有这个，组件就不会渲染。
*   `checked`(必选):这将保持当前状态，这将是一个布尔值。
*   `onChange`(必选):当输入的`onChange`事件处理程序被触发时，该函数将被调用。
*   `name`(可选):这将是复选框输入的标签文本，但我们一般不会使用它。
*   `small`(可选):这是一个布尔值，它以小模式呈现切换开关，其中不呈现文本。
*   `optionLabels`(可选):如果您没有使用控件的`small`版本，您可能需要将它作为两个值的数组传递给切换开关，这两个值表示文本的 True 和 False。一个例子就是`Text={["Yes", "No"]}`。
*   `disabled`(可选):这将直接传递给`<input type="checkbox" />`。

当不使用`small`版本时，以下`optionLabels`文本将被用作默认文本:

```
// Set optionLabels for rendering.
ToggleSwitch.defaultProps = {
  optionLabels: ["Yes", "No"],
}; 
```

由于大多数道具必须由用户设置，我们不能使用任意值，如果所需的道具没有传入，最好停止渲染。这可以通过一个简单的 JavaScript `if`语句或者一个使用`? :`的三元运算符或者一个短路的`&&`来完成:

```
{this.props.id ? (
  <!-- display the control -->
) : null} 
```

随着我们的应用程序的增长，我们可以通过类型检查来发现很多错误。React 有一些内置的类型检查功能。要对组件的属性运行类型检查，您可以分配特殊的`propTypes`属性。我们可以使用 React 的 [PropType](https://reactjs.org/docs/typechecking-with-proptypes.html) 库来执行上面的道具列表，这是一个独立的库，它导出了一系列验证器，可以用来确保您接收的数据是有效的。

您可以像这样安装它:

```
yarn add prop-types 
```

然后，使用以下命令导入 PropTypes 库:

```
// ToggleSwitch.js
import PropTypes from "prop-types"; 
```

我们将按以下方式定义属性类型:

```
ToggleSwitch.propTypes = {
  id: PropTypes.string.isRequired,
  checked: PropTypes.bool.isRequired,
  onChange: PropTypes.func.isRequired,
  name: PropTypes.string,
  optionLabels: PropTypes.array,
  small: PropTypes.bool,
  disabled: PropTypes.bool
}; 
```

作为解释:

*   `PropTypes.string.isRequired`:字符串值，必须输入。
*   这是一个字符串值，但不是强制的。
*   这是一个接受一个函数作为值的道具，但不是强制的。
*   这是一个布尔值，但不是强制的。
*   这是一个数组值，但不是强制的。

现在我们可以继续使用`ToggleSwitch`组件了。用以下内容替换`src/ToggleSwitch/ToggleSwitch.js`的内容:

```
import React from "react";
import PropTypes from "prop-types";
import './ToggleSwitch.scss';

/*
Toggle Switch Component
Note: id, checked and onChange are required for ToggleSwitch component to function.
The props name, small, disabled and optionLabels are optional.
Usage: <ToggleSwitch id="id" checked={value} onChange={checked => setValue(checked)}} />
*/

const ToggleSwitch = ({ id, name, checked, onChange, optionLabels, small, disabled }) => {

  return (
    <div className={"toggle-switch" + (small ? " small-switch" : "")}>  <input
        type="checkbox"
        name={name}
        className="toggle-switch-checkbox"
        id={id}
        checked={checked}
        onChange={e => onChange(e.target.checked)}
        disabled={disabled}
        />  {id ? (
          <label className="toggle-switch-label" htmlFor={id}>  <span
              className={
                disabled
                  ? "toggle-switch-inner toggle-switch-disabled"
                  : "toggle-switch-inner"
              }
              data-yes={optionLabels[0]}
              data-no={optionLabels[1]}
            />  <span
              className={
              disabled
                ? "toggle-switch-switch toggle-switch-disabled"
                : "toggle-switch-switch"
              }
            />  </label>
        ) : null}  </div>
    );
}

// Set optionLabels for rendering.
ToggleSwitch.defaultProps = {
  optionLabels: ["Yes", "No"],
};

ToggleSwitch.propTypes = {
  id: PropTypes.string.isRequired,
  checked: PropTypes.bool.isRequired,
  onChange: PropTypes.func.isRequired,
  name: PropTypes.string,
  optionLabels: PropTypes.array,
  small: PropTypes.bool,
  disabled: PropTypes.bool
};

export default ToggleSwitch; 
```

最后，为了测试组件，像这样修改`App.js`:

```
import React, { useState } from 'react';
import ToggleSwitch from './ToggleSwitch/ToggleSwitch'

function App() {
  let [newsletter, setNewsletter] = useState(false);

  const onNewsletterChange = (checked) => {
    setNewsletter(checked);
  }

  return (
    <>  <ToggleSwitch id="newsletter" checked={ newsletter } onChange={ onNewsletterChange } />  <label htmlFor="newsletter">Subscribe to our Newsletter</label>  </>
  );
}

export default App; 
```

现在，当您转到 [http://localhost:3000/](http://localhost:3000/) 时，您应该会看到工作开关。

## 使组件键盘可访问

最后一步是使我们的组件键盘可访问。为此，首先像这样修改标签:

```
// ToggleSwitch.js

<label className="toggle-switch-label"
       htmlFor={id}
       tabIndex={ disabled ? -1 : 1 }
       onKeyDown={ e => handleKeyPress(e) }> ... </label> 
```

如你所见，我们添加了一个`tabIndex`属性，根据组件当前是否被禁用，我们将其设置为`1`(可聚焦)或`-1`(不可聚焦)。

我们还声明了一个`handleKeyPress`函数来处理它接收键盘输入:

```
function handleKeyPress(e){
  if (e.keyCode !== 32) return;

  e.preventDefault();
  onChange(!checked)
} 
```

这将检查按下的键是否是空格键。如果是这样，它会阻止浏览器的默认操作(在这种情况下是滚动页面)并切换组件的状态。

这就是你所需要的。现在可以通过键盘访问该组件。

然而，有一个小问题。如果你点击`ToggleSwitch`组件，你会看到整个组件的轮廓，这可能不是你想要的。为了解决这个问题，我们可以稍微改变一下，以确保当它用键盘聚焦时接收到一个轮廓，而不是当它被点击时:

```
// ToggleSwitch.js
<span
  className={
    disabled
      ? "toggle-switch-inner toggle-switch-disabled"
      : "toggle-switch-inner"
  }
  data-yes={optionLabels[0]}
  data-no={optionLabels[1]}
  tabIndex={-1}
/>
<span
  className={
  disabled
    ? "toggle-switch-switch toggle-switch-disabled"
    : "toggle-switch-switch"
  }
  tabIndex={-1}
/> 
```

这里，我们为两个内部的`<span>`元素添加了一个`tabIndex`属性，以确保它们不会获得焦点。

然后，在`ToggleSwitch.scss`中:

```
$focus-color: #ff0;

.toggle-switch {
  ...
  &-label {
    ...
    &:focus {
      outline: none;
      > span {
        box-shadow: 0 0 2px 5px $focus-color;
      }
    }
    > span:focus {
      outline: none;
    }
  }
  ...
} 
```

这将在键盘聚焦时对`ToggleSwitch`的内部`<span>`元素应用样式，而不是在点击时。你可以在这里阅读更多关于这种技术[。它有点粗糙，一旦获得足够广泛的浏览器支持，就应该放弃使用](https://stackoverflow.com/a/45191208)[使用:焦点可见](https://css-tricks.com/almanac/selectors/f/focus-visible/)。

## 一个更完整的例子

最后，我想在下面的 CodeSandbox 中演示一个更完整的使用`ToggleSwitch`组件的例子。

这个演示在同一个页面上使用了多个`ToggleSwitch`组件。后三次切换的状态取决于第一次切换的状态。也就是说，你需要先接受营销邮件，然后才能选择接收哪些邮件。

[https://codesandbox.io/embed/thirsty-bas-de4uy?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/thirsty-bas-de4uy?fontsize=14&hidenavigation=1&theme=dark)

## 摘要

在本文中，我展示了如何使用 React 创建一个可重用的、受 iOS 启发的切换开关。我们看了用 SCSS 设计组件的样式，使它成为一个受控组件，如何通过给它传递道具来定制它，以及如何使它可以通过键盘访问。

你可以在我们的 [GitHub repo](https://github.com/sitepoint-editors/ToggleSwitch) 上找到拨动开关的完整代码。

## 分享这篇文章