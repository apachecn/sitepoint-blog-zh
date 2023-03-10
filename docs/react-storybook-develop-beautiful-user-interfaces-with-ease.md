# React Storybook:轻松开发漂亮的用户界面

> 原文：<https://www.sitepoint.com/react-storybook-develop-beautiful-user-interfaces-with-ease/>

![React Storybook](img/c988c99cd022a1d03236aa4bd31a8ce9.png)

当你开始一个新的前端项目时，你通常做的第一件事是创建一个漂亮的设计。您仔细规划和绘制所有的 UI 组件，以及它们可能具有的每个状态或效果。然而，在开发过程中，事情通常会发生变化。新的需求，以及不可预见的用例随处可见。最初漂亮的组件库不能覆盖所有这些需求，你开始用新的设计来扩展它。

如果此时你身边还有一个设计专家，这很好，但是他们经常已经切换到不同的项目，让开发人员去应付这些变化。结果，设计的一致性开始下滑。跟踪库中已有的组件以及它们可能具有的状态和外观变得很困难。

为了避免这种艺术混乱，为所有组件创建单独的文档通常是个好主意。有各种各样的工具可以实现这一目的，但是在本文中，我们将关注一个专门为 React 应用程序设计的工具— [React Storybook](https://getstorybook.io/) 。它允许您轻松浏览您的组件集合及其功能。这种应用的一个活生生的例子就是 [React Native components](http://necolas.github.io/react-native-web/storybook/) 的图库。

## 你为什么需要反应故事书？

那么这个陈列柜有什么帮助呢？为了回答这个问题，让我们试着列出一个参与 UI 组件开发的人员列表，并评估他们的需求。根据您的工作流程，此列表可能会有所不同，但通常有以下几种可能:

### 设计师或 UX 专家

这是负责用户界面外观和感觉的人。项目的原型阶段完成后，设计师通常会离开团队。当新的需求出现时，他们需要快速了解 UI 的当前状态。

### 开发者

开发人员是创建这些组件的人，也可能是风格指南的主要受益者。开发人员的两个主要用例是能够从库中找到合适的组件，并能够在开发过程中测试它们。

### 测试员

这是一个一丝不苟的人，他确保组件按预期实现。测试人员工作的一个主要部分是确保一个组件在各个方面的行为都是正确的。尽管这并没有消除对集成测试的需求，但是从项目本身中分离出来通常会更方便。

### 产品所有者

接受设计和实现的人。产品负责人需要确保项目的每个部分看起来都像预期的那样，并且品牌风格以一致的方式表现出来。

你可能已经注意到，每个人都有一个共同点，那就是在一个地方同时包含所有的组件。在项目本身中找到它们是相当乏味的。想想看，你要花多长时间才能找到项目中所有可能的按钮变化，包括它们的状态(禁用、主要、次要等)？这就是为什么有一个单独的画廊更方便。

如果我已经成功地说服了你，让我们看看如何在一个项目中建立 Storybook。

## 设置 React 故事书

要设置 React Storybook，首先需要一个 React 项目。如果你目前没有合适的，你可以使用 [create-react-app](https://www.sitepoint.com/create-react-app/) 轻松创建一个。

要生成一本故事书，全局安装`getstorybook`

```
npm i -g getstorybook 
```

然后导航到您的项目并运行

```
getstorybook 
```

该命令将做三件事:

*   将 [@kadira/storybook](https://www.npmjs.com/package/@kadira/storybook) 安装到您的项目中。
*   将`storybook`和`build-storybook`脚本添加到您的`package.json`文件中。
*   创建一个包含基本配置的`.storybook`文件夹和一个包含示例组件和故事的`stories`文件夹。

要运行 Storybook，执行`npm run storybook`并打开显示的地址( [http://localhost:9009/](http://localhost:9009/) )。该应用程序应该如下所示:

![React Storybook Default User interface](img/d4534410c26ef9cd78ee96f92ab4c279.png)

## 添加新内容

现在我们已经运行了 React Storybook，让我们看看如何添加新内容。每个新页面都是通过创建故事来添加的。这些是呈现组件的代码片段。由`getstorybook`生成的示例故事如下所示

```
//src/stories/index.js

import React from 'react';
import { storiesOf, action, linkTo } from '@kadira/storybook';
import Button from './Button';
import Welcome from './Welcome';

storiesOf('Welcome', module)
  .add('to Storybook', () => (
    <Welcome showApp={linkTo('Button')}/>
  ));

storiesOf('Button', module)
  .add('with text', () => (
    <Button onClick={action('clicked')}>Hello Button</Button>
  ))
  .add('with some emoji', () => (
    <Button onClick={action('clicked')}>   </Button>
  )); 
```

`storiesOf`函数在导航菜单中创建新的部分，而`add`方法创建新的子部分。你可以自由地以你认为合适的方式构建故事书，但是你不能创建超过两层的层次结构。构造故事书的一个简单方法是创建公共的顶层部分，例如相关元素组的“表单输入”、“导航”或“小部件”，以及单个组件的子部分。

您可以自由选择将您的故事文件放在哪里:放在一个单独的 stories 文件夹中，还是放在组件旁边。我个人更喜欢后者，因为让故事贴近组件有助于保持它们的可访问性和最新性。

故事被加载到包含以下代码的`.storybook/config.js`文件中:

```
import { configure } from '@kadira/storybook';

function loadStories() {
  require('../src/stories');
}

configure(loadStories, module); 
```

默认情况下，它会加载`src/stories/index.js`文件，并希望您将故事导入其中。这有点不方便，因为这需要我们导入我们创建的每个新故事。我们可以修改这个脚本，使用 Webpack 的 [require.context](https://webpack.github.io/docs/context.html) 方法自动加载所有的故事。为了将故事文件与代码的其余部分区分开来，我们可以同意为它们添加一个`.stories.js`扩展名。修改后的脚本应该如下所示:

```
import { configure, addDecorator } from '@kadira/storybook';
import React from 'react';

configure(
  () => {
    const req = require.context('../src', true, /.stories.js$/);
    req.keys().forEach((filename) => req(filename));
  },
  module
);

configure(loadStories, module); 
```

如果您对源代码使用不同的文件夹，请确保它指向正确的位置。重新运行 Storybook 以使更改生效。故事书将会是空的，因为它不再导入`index.js`文件，但是我们将很快修复这个问题。

## 写一个新故事

现在我们已经根据我们的需要稍微修改了故事书，让我们写我们的第一个故事。但是首先我们需要创建一个组件来展示。让我们创建一个简单的`Name`组件，在彩色块中显示一个名字。该组件将有以下 JavaScript 和 CSS。

```
import React from 'react';

import './Name.css';

const Name = (props) => (
  <div className={'name ' + (props.type ? props.type : '')}>{props.name}</div>
)

Name.propTypes = {
  type: React.PropTypes.oneOf(['highlight', 'disabled']),
}

export default Name; 
```

```
.name {
  display: inline-block;
  font-size: 1.4em;
  background: #4169e1;
  color: #fff;
  border-radius: 4px;
  padding: 4px 10px;
}

.highlight {
  background: #dc143c;
}

.disabled {
  background: #999;
} 
```

您可能已经注意到，这个简单的组件可以有三种状态:默认、高亮和禁用。把它们都形象化不是很好吗？让我们为此写一个故事。在组件旁边创建一个新的`Name.stories.js`文件，并添加以下内容:

```
import React from 'react';
import { storiesOf, action, linkTo } from '@kadira/storybook';

import Name from './Name';

storiesOf('Components', module)
  .add('Name', () => (
    <div>
      <h2>Normal</h2>
      <Name name="Louie Anderson" />
      <h2>Highlighted</h2>
      <Name name="Louie Anderson" type="highlight" />
      <h2>Disabled</h2>
      <Name name="Louie Anderson" type="disabled" />
    </div>
  )) 
```

打开故事书，看看你的新组件。结果应该是这样的:

![Name story](img/3262a7e574a68c61bc3224abf71c6a2d.png)

您可以随意试验组件的显示方式以及它的源代码。请注意，由于 React 的热重新加载功能，每当您编辑故事或组件时，更改将立即出现在您的故事书中，而无需手动刷新浏览器。但是，当您添加或删除文件时，可能需要刷新。故事书并不总是注意到这样的变化。

### 查看自定义

如果您想更改您的故事的显示方式，您可以将它们包装在一个容器中。这可以使用`addDecorator`功能来完成。例如，您可以通过向`.storybook/config.js`添加以下代码来为所有页面添加“示例”标题:

```
import { configure, addDecorator } from '@kadira/storybook';
import React from 'react';

addDecorator((story) => (
  <div>
    <h1>Examples</h1>
    {story()}
  </div>
)); 
```

您还可以通过在`storiesOf`之后调用`addDecorator`来定制单独的部分:

```
storiesOf('Components', module)
  .addDecorator(...) 
```

## 出版你的故事书

一旦您完成了您的故事书的工作，并且您觉得它已经可以发布了，您可以通过运行

```
npm run build-storybook 
```

默认情况下，Storybook 内置在`storybook-static`文件夹中。您可以使用`-o`参数更改输出目录。现在你只需要把它上传到你喜欢的托管平台上。

如果你正在 GitHub 上做一个项目，你可以发布你的故事书，只要把它构建到 docs 文件夹中，然后把它推到资源库中。GitHub [可以配置为从那里为你的 GitHub Pages](https://github.com/blog/2233-publish-your-project-documentation-with-github-pages) 网站提供服务。如果您不想将构建好的故事书保存在存储库中，您也可以使用[故事书部署器](https://github.com/kadirahq/storybook-deployer)。

## 构建配置

Storybook 被配置为支持故事中的许多功能。您可以在[中编写与 create-react-app](https://github.com/storybooks/react-storybook/blob/master/src/server/config/babel.js#L24) 相同的 ES2015+语法，但是，如果您的项目使用不同的 Babel 配置，它将自动拾取您的`.babelrc`文件。您还可以导入 JSON 文件和图像。

如果您觉得这还不够，您可以通过在`.storybook`文件夹中创建一个`webpack.config.js`文件来添加额外的 webpack 配置。由此文件导出的配置选项将与默认配置合并。例如，要在您的故事中添加对 SCSS 的支持，只需添加以下代码:

```
module.exports = {
  module: {
    loaders: [
      {
        test: /.scss$/,
        loaders: ["style", "css", "sass"]
      }
    ]
  }
} 
```

但是不要忘记安装 [sass-loader](https://www.npmjs.com/package/sass-loader) 和 [node-sass](https://www.npmjs.com/package/node-sass) 。

您可以添加您想要的任何 webpack 配置，但是，您不能覆盖条目、输出和第一个 Babel 加载程序。

如果您想为开发和生产环境添加不同的配置，您可以导出一个函数。它将通过基本配置和设置为“开发”或“生产”的`configType`变量来调用。

```
module.exports = function(storybookBaseConfig, configType) {
  // add your configuration here

  // Return the altered config
  return storybookBaseConfig;
}; 
```

## 通过插件扩展功能

故事书本身非常有用，但为了让事情变得更好，它还有一些插件。在本文中，我们将只涉及其中的一部分，但请务必稍后查看官方列表。

### 动作和链接

故事书附带了两个预配置的插件:动作和链接。使用它们不需要进行任何额外的配置。

#### 行动

[动作](https://github.com/storybooks/storybook-addon-actions)允许您在“动作记录器”面板中记录由组件触发的事件。看看 Storybook 生成的`Button`故事。它将`onClick`事件绑定到一个`action`助手，后者在 UI 中显示该事件。

*注意:你可能需要重命名包含`Button`故事的文件和/或根据`.storybook/config.js`中的修改改变它的位置。*

```
storiesOf('Button', module)
  .add('with text', () => (
    <Button onClick={action('clicked', 'test')}>Hello Button</Button>
  )) 
```

尝试点击按钮，并注意“动作记录器”中的输出。

![Action logger output](img/369e5690219f31d7b98c547790c0cd8c.png)

#### 链接

[链接](https://github.com/storybooks/storybook-addon-links)插件允许你在组件之间添加导航。它提供了一个可以绑定到任何`onClick`事件的`linkTo`助手:

```
import { storiesOf, linkTo } from '@kadira/storybook';

storiesOf('Button', module)
  .add('with link', () => (
    <Button onClick={linkTo('Components', 'Name')}>Go to Name</Button>
  )); 
```

点击此按钮将进入“组件”部分和“名称”子部分。

### 把手

[旋钮插件](https://github.com/storybooks/storybook-addon-knobs)允许你在运行时直接从 UI 修改 React 属性来定制你的组件。

要安装插件运行:

```
npm i --save-dev @kadira/storybook-addon-knobs 
```

在你可以使用这个插件之前，它需要在 Storybook 上注册。为此，在`.storybook`文件夹中创建一个包含以下内容的`addons.js`文件:

```
import '@kadira/storybook/addons';
import '@kadira/storybook-addon-knobs/register'; 
```

之后，用装饰者包装你的故事。您可以在`.storybook/config.js`中进行全局操作:

```
import { withKnobs } from '@kadira/storybook-addon-knobs';

addDecorator(withKnobs); 
```

一旦我们做到了这一点，我们可以尝试改变我们的`Name`组件故事。现在，我们将能够在 UI 中选择它们，而不是一次拥有组件状态的所有三种变化。我们也将使名称可编辑。将`Name.stories.js`的内容改为:

```
import React from 'react';
import { storiesOf, action, linkTo } from '@kadira/storybook';
import { text, select } from '@kadira/storybook-addon-knobs';

import Name from './Name';

const types = {
  '': '',
  highlight: 'highlight',
  disabled: 'disabled'
}

storiesOf('Components', module)
  .add('Name', () =>  (
    <div>
      <h2>Normal</h2>
      <Name name={text('Name', 'Louie Anderson')} type={select('Type', types)}  />
    </div>
  )) 
```

该插件提供了各种帮助函数来创建不同类型的用户输入，如数字、范围或数组。这里我们将使用文本作为名称，选择作为类型。打开“名称”页面，一个新的“旋钮”标签应该出现在“动作记录器”旁边。尝试更改输入值，并查看被重新渲染的组件。

![Knobs interface](img/e5914e1872387b1912bbe99195df3672.png)

### 信息

信息插件允许你添加更多关于故事的信息，比如它的源代码、描述和反应类型。对于开发人员来说，获得这些信息非常方便。

运行以下命令安装该插件:

```
npm i --save-dev @kadira/react-storybook-addon-info 
```

然后在`.storybook/config.js`文件中注册故事书插件:

```
import { setAddon } from '@kadira/storybook';
import infoAddon from '@kadira/react-storybook-addon-info';

setAddon(infoAddon); 
```

这将向`storiesOf`对象添加一个额外的`addWithInfo`方法来注册您的故事。它有一个稍微不同的 API，接受故事的标题、描述、渲染函数和附加配置作为参数。使用这种方法，我们可以这样重写我们的`Name`故事:

```
import React from 'react';
import { storiesOf, action } from '@kadira/storybook';

import Name from './Name';

storiesOf('Components', module)
  .addWithInfo(
    'Name with info', 
    ` A component to display a colored name tag. `,
    () =>  (
      <Name name="Louie Anderson" />
    ),
    { inline: true },
  ) 
```

inline 参数将使信息默认显示，而不是通过角落中的链接访问。结果将如下所示:

![Info example](img/c6918eaf0725c01801fa32f854605252.png)

## 自动化测试

Storybook 的一个重要方面是将它作为运行自动化测试的平台，这一点在本文中没有涉及。你可以执行任何种类的测试，从单元测试到功能测试和视觉回归测试。不出所料，有几个插件旨在提升 Storybook 作为测试平台的能力。我们不会深入讨论他们的细节，因为他们值得一篇属于他们自己的文章，但仍然想提及他们。

### 规范

[规范插件](https://github.com/mthuret/storybook-addon-specifications)允许你直接在你的故事文件中编写单元测试。每当您打开 Storybook 时，就会执行测试，并且结果会显示在 UI 中。经过一些修补，您也可以使用 Jest 在 CI 环境中运行这个测试。

> 您可能还想:[如何使用 Jest 测试 React 组件](https://www.sitepoint.com/test-react-components-jest/)

### 故事镜头

[Storyshots](https://github.com/storybooks/storyshots) 允许你根据故事执行[笑话快照测试](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html)。快照测试允许您检查组件呈现的 DOM 是否与预期的结果相匹配。非常方便的测试你的组件是否被正确渲染。至少从 DOM 的角度来看是这样的。

## 故事书即服务

Kadira 也提供故事书服务，名为[故事书中心](https://hub.getstorybook.io/)。它允许你和他们一起托管你的故事书，并将协作提升到一个新的水平。除了标准特性之外，它还集成了 GitHub，可以为项目的每个拉请求生成一个新的故事书。你也可以直接在 Storybook 中留下评论，与你的同事讨论变化。

## 结论

如果你觉得维护项目中的 UI 组件开始变得很痛苦，后退一步，看看你错过了什么。也许你所需要的只是一个所有相关方之间的便捷协作平台。在这种情况下，对于您的 React 项目，不要再看了，故事书是您的完美工具。

你已经在用故事书了吗？你打算试一试吗？为什么？或者说，为什么不呢？我很想在评论中听到你的意见。

*这篇文章由[蒂姆·塞韦里恩](https://www.sitepoint.com/author/tseverien/)和[朱利奥·迈纳迪](https://www.sitepoint.com/author/gmainardi/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章