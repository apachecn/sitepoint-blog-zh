# 学习移动:用 HTML，CSS 和 React 创建动画视频

> 原文：<https://www.sitepoint.com/remotion-create-animated-videos-using-html-css-react/>

传统上，创建一个包含文本、动画、颜色和形状的视频需要专业软件，并且可能需要数年的动画培训。如果您可以使用现有的工具集、培训和 web 开发背景来做同样的事情，会怎么样？

Remotion 允许 JavaScript 开发人员重用他们积累的技能和知识，使用 HTML、CSS 和 React JS 创建复杂的动画视频。如果您可以使用 React 渲染文本、制作 CSS 样式的动画或组织 HTML 内容，现在您可以仅使用代码来创建和编辑自己的视频，而不需要视频编辑应用程序或软件。

在本文中，我将介绍使用 Remotion 的过程，并向您介绍我的发现。

你可以在 [GitHub](https://github.com/sitepoint-editors/remotion-example) 上找到这篇文章的完整代码。

## 什么，为什么？

Remotion 是由 Jonny Burger 为 React 制作的视频制作工具包。该工具包允许任何对 React、HTML 或 CSS 有基本了解的人使用代码创建动画视频。

在视频创作领域，由于使用和掌握这些工具需要软件和培训，目前存在很高的准入门槛。通过利用 JavaScript 开发人员现有的工具包，这为更广泛的用户群打开了视频创作空间。随着视频成为代码，我们可以利用现有的模式来实现更有效的视频创作——例如基于参数的自动生成或构建管道。

## 入门指南

幸运的是，Remotion 有一个快速简单的设置过程，带有一个纱线和 npm 初学者工具包。对于这个例子，我们将坚持使用 npm 作为构建和运行工具。在我们开始之前，您需要安装 Node 和 npm。(如需帮助，您可以按照本指南安装节点和 npm。)如果你用的是 Linux，还可以查看[远程安装指南](https://www.remotion.dev/docs/#installation)，因为你可能需要安装额外的工具。设置好节点和 npm 后，让我们通过运行以下代码创建一个新项目:

```
npm init video
```

这将提示您输入项目名称，该名称也用作目录名称。在我们的例子中，它将是`my-video`。进入后，我们可以进入`my-video`目录，通过运行如下启动脚本启动默认视频项目:

```
cd my-video
npm start
```

运行 start 命令后，浏览器应该会自动打开。如果没有，打开浏览器，导航到 [http://localhost:3000/](http://localhost:3000/) 。此功能允许您观看和调试您正在创建的视频。该播放器具有包括播放按钮的控件，允许您预览视频内容。从查看演示示例的代码开始可能也是有用的，Remotion 为如何构建自己的视频提供了指导。

## 你好，世界！

我们将创建自己的视频动画文本“你好，世界！”，以掌握 Remotion 中提供的组件和流程。

首先，让我们删除现有的示例代码(`src`文件夹中的所有内容)，因为我们想要重新开始。然后，让我们在`src`目录下创建一个`Demo`目录，它将保存和管理我们这个项目的所有视频工作。在`Demo`目录下，创建一个`Demo.js`文件:

```
import {Composition, interpolate, Sequence, useCurrentFrame, useVideoConfig} from 'remotion';
import Title from './Title';
import Hello from './Hello';
import "./demo.css";

const Demo = () => {
  return (
  <div className="main-container">
    {/* TODO: add video content */}
  </div>
  );
};

export const DemoVideo = () => {
  return (
  <Composition
    id="Demo"
    component={Demo}
    durationInFrames={150}
    fps={30}
    width={1920}
    height={1080}
    defaultProps={{
      titleText: 'This is my first Remotion video',
      titleColor: 'blue',
    }}
    />
  )
}
```

`Demo`文件导出我们的视频代码。如你所见，我们可以创建一个`Demo`组件来保存我们视频中的所有视觉元素。然后，我们可以导出一个组件来呈现我们视频的`Composition`。`Composition`组件允许我们定义一些基本属性，如视频剪辑的宽度和高度、FPS(每秒帧数)以及将要呈现的特征。我们还从 Remotion 导入了一些实用程序和钩子，以及一些我们将很快创建的附加组件。

目前我们的`Demo`组件是空的，但是让我们给我们的视频添加一些元素:

```
const Demo = ({titleText, titleColor}) => {
  const frame = useCurrentFrame();
  const videoConfig = useVideoConfig();

  const totalOpacity = interpolate(
     frame,
     [videoConfig.durationInFrames - 25, videoConfig.durationInFrames - 15],
     [1, 0],
    {
      extrapolateLeft: 'clamp',
      extrapolateRight: 'clamp',
    }
  );

  return (
    <div className="main-container">
      <div style={{opacity: totalOpacity}}>
        <Sequence from={0} durationInFrames={videoConfig.durationInFrames / 2}>
          <Hello/>
        </Sequence>
        <Sequence from={35} durationInFrames={Infinity}>
          <Title titleText={titleText} titleColor={titleColor} />
        </Sequence>
      </div>
    </div>
  );
};
```

我们已经在文件中添加了很多内容，所以让我们将它们全部分解开来。

首先在我们的渲染部分，我们可以从文件中看到，我们现在可以返回一个具有不透明样式的 div，允许我们在视频的开始和结束时淡入和淡出元素。对于不透明度值，我们使用一个移动辅助对象。`interpolate`功能允许您更好地定义动画，并将动画值映射到当前帧和视频持续时间。在这个例子中，我们传入当前帧。该函数将在生成的每个帧上被调用。输入范围是根据视频的持续时间计算的，输出值的范围是从 0 到 1，因为这是不透明度 CSS 值的范围。当`Demo`组件为每一帧重新渲染时，`interpolate`函数每次都会被调用，并将返回适当的不透明度值。

接下来，我们可以开始在视频屏幕上呈现不同的视觉元素。在本例中，我们需要文本“Hello，World！”淡入视野，然后消失，然后出现文本“这是我的第一个远程视频”。为此，我们可以呈现多个`Sequence`组件。

组件是另一个远程功能，它允许我们定义组件在视频中渲染的方式和时间以及时长。这对于构建想要添加定时或分层元素的复杂视频非常有用，例如本例。每个`Sequence`也将显示在浏览器播放器中，并根据子组件名命名。这允许您实时监控您正在生成的视频以及您正在添加到其中的效果。

Remotion 还提供了一些有用的 React 钩子，在这个例子中，我们使用了`useCurrentFrame`和`useVideoConfig`钩子。`useCurrentFrame`将返回视频所在的当前帧，这对于动画和基于视频播放的当前位置实现动作很有用。`useVideoConfig`将返回一个具有不同值的对象，例如:

*   **width** :视频的宽度——用于定位视频中的元素
*   **height** :视频的高度——用于定位视频中的元素
*   **FPS** :每秒帧数——可以用来决定动画或元素移动的速度
*   **durationInFrames** :以帧为单位的视频总长度——可用于计算动画或`Sequence`显示和隐藏的时间。

在我们的例子中，如上所述，首先我们需要我们的`Hello`组件，文本“Hello，World！”，出现在视频的开头，并在屏幕上停留一半时间。我们通过使用从`useVideoConfigHook`中计算出来的`videoConfig.duration`值来实现这一点。

对于第二个`Sequence`，我们希望我们的`Title`组件文本“这是我的第一个远程视频”，在 35 帧后出现，并在视频的整个持续时间内保持在屏幕上。为了实现这一点，对于`From`我们进入`35`，对于`durationInFrames`我们进入`Infinity`。

为了设计我们的演示组件，我们可以使用 CSS 和内联样式。当使用 CSS 时，我们希望将样式应用于整个视频，所以让我们创建一个`demo.css`文件，它将保存覆盖整个视频区域的任何样式。在我们的示例中，我们希望将背景设为白色，并将项目与 Flexbox 对齐:

```
.main-container {
    flex: 1;
    background-color: white;
}
```

现在让我们更深入地研究我们正在渲染的这些元素。

### 在动画中渲染 React 组件

`Hello`组件将是一个基本的 React 组件，它呈现一个应用了一些内联样式和文本“Hello，World！”的 H1 标签这是我们可以呈现的组件的最简单形式。为了简单起见，我们可以使用内联样式。但是因为这是 React，所以您也可以从 CSS 文件导入样式，并使用类名、样式组件、CSS 模块或任何您已经熟悉的样式模式作为替代。让我们创建`Hello`组件。在`Demo`文件夹中，创建一个新文件`Hello.js`:

```
const Hello = () => {
  return (
    <h1
      style={{
        fontFamily: 'SF Pro Text, Helvetica, Arial',
        fontWeight: 'bold',
        fontSize: 100,
        textAlign: 'center',
        position: 'absolute',
        bottom: 500,
        width: '100%'
      }}
    >
      Hello, World!
    </h1>
  );
};

export default Hello;
```

现在，让我们看一个更复杂的例子。在`Demo`文件夹中，创建一个名为`Title.js`的新文件，并添加以下组件代码:

```
import {spring, useCurrentFrame, useVideoConfig} from 'remotion';

const Title = ({titleText, titleColor, bottom}) => {
  const videoConfig = useVideoConfig();
  const frame = useCurrentFrame();
  const text = titleText.split(' ').map((t) => `  ${t}  `);
  return (
    <h1
      style={{
        fontFamily: 'SF Pro Text, Helvetica, Arial',
        fontWeight: 'bold',
        fontSize: 100,
        textAlign: 'center',
        position: 'absolute',
        bottom: bottom || 160,
        width: '100%',
      }}
    >
      {text.map((t, i) => {
        return (
          <span
            key={t}
            style={{
              color: titleColor,
              marginLeft: 10,
              marginRight: 10,
              transform: `scale(${spring({
                fps: videoConfig.fps,
                frame: frame - i * 5,
                config: {
                  damping: 100,
                  stiffness: 200,
                  mass: 0.5,
                },
              })})`,
              display: 'inline-block',
            }}
          >
            {t}
          </span>
        );
      })}
    </h1>
  );
};

export default Title;
```

我们这里有很多事情要做，所以让我们再一次分解一下正在发生的事情。

Remotion 对 TypeScript 有一流的支持。这不是必需的，但它可以使开发过程更好，因为您将在 IDE 中获得更详细的自动完成建议。然而，为了让这个例子对初学者更友好，我们将只使用普通的 JavaScript。

我们的组件接受两个道具——`titleText`和`titleColor`——稍后将在我们的渲染方法中使用。这表明，使用 React，我们仍然可以在应用程序中传递道具，从而使我们的视频元素可重用和动态。您可能已经注意到，在我们的`Demo`组件中，我们从`Composition`组件传入了道具。这显示了 React 在行动中的威力。我们可以从 React 应用程序的最顶层传入道具，使视频具有响应性，这意味着您可以更改一个文本块来制作新的视频或更改整个视频上下文。

在我们访问了`Title`组件中的 props 之后，我们再次调用 Remotion 钩子来获取`videoConfig`和帧数据。然后，`Title`组件分解传递的文本属性，并结合使用 map 和 CSS 转换一次呈现一个单词。这里我们有机会使用另一个内置的助手函数。`Spring`接受值以帮助生成动画值的平滑输出。我们通过主视频配置的 FPS 来控制动画的速度。帧值控制动画开始的时间，最后我们传入额外的配置选项来控制动画的平滑度。

在我们创建好所有视频组件并准备好之后，我们需要最终在`src`文件夹的根目录下创建一个`index.js`文件，并添加以下内容:

```
import {registerRoot} from 'remotion';
import { DemoVideo } from './Demo/Demo';

registerRoot(DemoVideo);
```

索引文件从 Remotion 导入了`registerRoot`函数，它允许我们渲染视频内容。把这个当成是 [ReactDOM render](https://reactjs.org/docs/react-dom.html#render) 函数，不过是为了移除。然后我们将我们的`DemoVideo`组件传递给`registerRoot`，它将在开发或构建模式下可视化呈现的视频。

我们现在正在导入将由 Remotion 渲染的演示视频。

现在我们已经将所有这些功能组合在一起，我们有了一个完整的动画视频，它提供了 Remotion 提供的不同组件和助手功能的一个示例。

我们可以使用以下命令从项目的根目录运行视频:

```
./node_modules/.bin/remotion preview src/index.js
```

或者，您可以更新`package.json`文件中的`start`脚本:

```
- "start": "remotion preview src/index.tsx", + "start": "remotion preview src/index.js",
```

然后使用`npm start`运行动画。

## 构建星球大战动画

现在，我们对 Remotion 和提供的不同组件有了基本的了解，我们可以挑战自我，享受更多乐趣。让我们建立自己的标志性的星球大战标题介绍屏幕版本。我们希望能够呈现一个闪亮的星形背景，并在屏幕上滚动亮黄色文本。我们可以利用我们从“你好，世界！”作为起点的例子。

让我们从创建我们需要的文件开始。在`src`文件夹中，创建一个`starWarsIndex.js`文件和一个`StarWars`文件夹。在`StarWars`文件夹中，再创建四个文件:`starWars.js`、`starWars.css`、`starsBackground.js`、`starsBackground.css`。

完成后，`src`文件夹应该是这样的:

```
.
├── Demo
│   └── Files from "Hello, World!" demo
├── index.js
├── StarWars
│   ├── starsBackground.css
│   ├── starsBackground.js
│   ├── starWars.css
│   └── starWars.js
└── starWarsIndex.js
```

### 创建滚动文本

首先，我们从一个`StarWarsVideo`组件开始，它将呈现一个`Composition`组件来定义视频属性。随着滚动文本越来越长，我们定义了一个更高的`durationInFrames`数。

将以下内容添加到`src/starWarsIndex.js`:

```
import {registerRoot, Composition, Sequence} from 'remotion';
import {useEffect, useState} from 'react'
import { LoremIpsum } from 'lorem-ipsum';

import Stars from './StarWars/starsBackground';
import StarWars from './StarWars/starWars';

const StarWarsVideo = () => {
  const [textBlocks, setTextBlocks] = useState([]);

  useEffect(() => {
   setTextBlocks([
    lorem.generateSentences(5),
    lorem.generateSentences(5),
    lorem.generateSentences(5),
   ])
  }, [])
  return (
    <>
      <Composition
        id='star-wars'
        component={Video}
        durationInFrames={580}
        fps={30}
        width={1920}
        height={1080}
        defaultProps={{ textBlocks }}
        />
    </>
  );
};

registerRoot(StarWarsVideo);
```

我们还需要定义一些反应状态。在这个星球大战的例子中，我们将利用 React 状态和道具在每次重新加载视频时生成随机文本。使用 [lorem-ipsum](https://www.npmjs.com/package/lorem-ipsum) npm 模块，我们可以使文本每次生成时都具有响应性和不同性。

让我们安装模块:

```
npm i lorem-ipsum
```

然后，在同一个文件中添加:

```
// import statements

const lorem = new LoremIpsum({
  sentencesPerParagraph: {
    max: 8,
    min: 4
  },
  wordsPerSentence: {
    max: 16,
    min: 4
  }
});

const Video = ({ textBlocks }) => {
  return (
   <div>
    <Sequence from={0} durationInFrames={Infinity}>
     <Stars/>
    </Sequence>
    <Sequence from={0} durationInFrames={Infinity}>
     <StarWars
      textBlocks={textBlocks}
     />
    </Sequence>
   </div>
  )
}

const StarWarsVideo = () => { ... };

registerRoot(StarWarsVideo);
```

对于`Sequence`组件，我们可以为视频层叠两个主要组件。`Stars`组件将呈现星空背景，`StarWars`组件将呈现滚动的黄色文本。星星背景使用标准的 CSS 动画和变换来显示星星。`StarWars`组件是我们开始回到基于移动的动画的地方。我们可以使用`Spring`辅助函数来控制顶部位置、旋转和平移 CSS 变换属性，以基于视频中的当前时间来制作文本滚动的动画。

将以下内容添加到`src/starWars.js`:

```
import React from 'react';
import './starWars.css';
import {spring, useCurrentFrame} from 'remotion';

const StarWars = ({ textBlocks }) => {
  const frame = useCurrentFrame()
  const fps = 6000;

  const top = spring({
    frame,
    from: 0,
    to: -6000,
    fps,
  })

  const rotate = spring({
    frame,
    from: 20,
    to: 25,
    fps,
  })

  const translateZ = spring({
    frame,
    from: 0,
    to: -2500,
    fps,
  })

  return (
    <>
      <div className="fade"/>

      <section className="star-wars">
        <div
          className="crawl"
          style={{
            top: `${top}px`,
            transform: `rotateX(${rotate}deg) translateZ(${translateZ}px)`
          }}
        >

          <div className="title">
            <p>Episode IV</p>
            <h1>A New Hope</h1>
          </div>
          {
            textBlocks.map((block, index) => {
              return (
                <p key={index}>{block}</p>
              )
            })
          }
        </div>
      </section>
    </>
  )
}

export default StarWars;
```

注意，我们正在渲染出`textBlocks`道具，它将是我们每次生成视频时的随机文本。

现在剩下的就是创建`Stars`组件。将以下内容添加到`src/starsBackground.js`:

```
import React from 'react';
import './starsBackground.css';

const Stars = () => {
  return (
    <>
      <div id='stars'/>
      <div id='stars2'/>
      <div id='stars3'/>
    </>
  );
}

export default Stars;
```

还将以下样式添加到`src/starsWars.css`:

```
.fade {
  position: relative;
  width: 100%;
  min-height: 60vh;
  top: -25px;
  z-index: 1;
}

.star-wars {
  display: flex;
  justify-content: center;
  position: relative;
  height: 800px;
  color: #feda4a;
  font-family: 'Pathway Gothic One', sans-serif;
  font-size: 500%;
  font-weight: 600;
  letter-spacing: 6px;
  line-height: 150%;
  perspective: 400px;
  text-align: justify;
}

.crawl {
  position: relative;
  top: 9999px;
  transform-origin: 50% 100%;
}

.crawl > .title {
  font-size: 90%;
  text-align: center;
}

.crawl > .title h1 {
  margin: 0 0 100px;
  text-transform: uppercase;
}
```

`src/starsBackground.css`太大，无法在此列出。请从[GitHub repo](https://github.com/sitepoint-editors/remotion-example/blob/main/src/StarWars/starsBackground.css)中获取其内容，并将其添加到您自己的项目中。

这将产生一个全功能的星球大战介绍视频，只用代码创建，没有视频编辑软件。

运行 StarWars 示例的最后一步是将以下脚本添加到`package.json`文件中:

```
"start:starwars": "remotion preview src/starWarsIndex.js",
```

现在我们有了——一个完全用 React 编码的星球大战简介。

如果你想插入真正的《星球大战》文本，从[这里](https://www.inafarawaygalaxy.com/2013/08/the-text-of-every-star-wars-crawl.html)获取并修改`src/starWarsIndex.js`中的`useEffect`方法调用:

```
useEffect(() => {
 setTextBlocks([ - lorem.generateSentences(5), - lorem.generateSentences(5), - lorem.generateSentences(5), + "It is a period of civil war. Rebel spaceships...", + "Pursued by the Empire’s sinister agents..."  ]) }, [])
```

厉害！

## 结论

我们举了两个例子来展示移动的力量，每个例子都有不同的复杂程度。然而，这仅仅是移动能力的皮毛。下面是 Remotion 提供的一些我们没有提到的其他功能。

别忘了，所有的代码都可以在 [GitHub](https://github.com/sitepoint-editors/remotion-example) 上找到。

### 数据提取

要在视频中添加反应元素，您可以在构建时获取数据来帮助填充内容。Remotion 提供钩子来处理数据的获取，比如`continueRender`。`delayRender`也可用于暂停视频内容的渲染，直到数据被提取。这些功能可用于根据输入数据库的数据生成视频。或者他们可以从服务器获取数据——例如，为一篇博客文章创建一个介绍视频，并从服务器获取博客的标题和英雄图像。

### 参数化渲染

从我们前面使用的例子中，我们可以控制传入视频元素的道具流。这使得视频具有响应性。然而，这需要每次都修改代码。使用参数化呈现，您可以将数据作为构建命令的一部分传入。这意味着，作为 CI/CD 流的一部分，您可以根据传入的数据生成视频—例如，自动生成入职视频，将人员的姓名和职务作为道具传入。

### 资产进口

你不需要仅仅使用 CSS 来创建元素。您也可以将图像、现有视频和音频文件等其他资源导入到项目中。

还有更多的附加功能，并且新功能会定期发布。Remotion 是一个完全开发的项目，正在基于 JavaScript 的视频领域取得重大进展。这是一个非常强大的工具，有着尚未发现的可能性。

如果你用 Remotion 做了一些很酷的东西，请在 Twitter 上告诉我。

## 分享这篇文章