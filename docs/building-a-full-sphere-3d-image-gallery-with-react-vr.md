# 用 React VR 构建全球体三维图库

> 原文：<https://www.sitepoint.com/building-a-full-sphere-3d-image-gallery-with-react-vr/>

**[React VR](https://facebook.github.io/react-vr/index.html) 是脸书的 JavaScript 库，减少了创建 [WebVR](https://w3c.github.io/webvr/) 应用的工作量。你可能会将 React VR 与 Mozilla 的 A-Frame 进行比较，但与编写 HTML 不同，React VR 使用 JavaScript 来创建 WebVR 场景。**

React VR 建立在 WebGL 库 [three.js](https://threejs.org/) 和 React 原生框架之上。这意味着我们能够使用 JSX 标签，反应本地组件，如`<View>`或`<Text>`，或反应本地概念，如 flexbox 布局。为了简化创建 WebVR 场景的过程，React VR 内置了对 3D 网格、灯光、视频、3D 形状或球形图像的支持。

[//giphy.com/embed/3ohzdNNZOlfimsiUQo](//giphy.com/embed/3ohzdNNZOlfimsiUQo)

[通过 GIPHY](https://giphy.com/gifs/sitepoint-reactvr-gallery-3ohzdNNZOlfimsiUQo)

在本文中，我们希望使用 React VR 来构建一个球形图像的查看器。为此，我们将使用四张[等矩形](https://en.wikipedia.org/wiki/Equirectangular_projection)照片，这些照片是我在 [React Conf 2017](http://conf.reactjs.org/) 用我的 [Theta S 相机](https://theta360.com/de/about/theta/s.html)拍摄的。画廊将有四个按钮来交换图像，这将与鼠标和/或 VR 耳机配合使用。你可以在这里下载等矩形图片以及按钮图形[。最后但同样重要的是，我们将通过添加一个简单的按钮过渡来了解动画如何与 React VR 配合工作。](https://github.com/sitepoint-editors/GDVR_REACTVR_SITEPOINT_GALLERY/tree/develop/static_assets)

对于开发，我们在桌面上使用 Chrome 这样的浏览器。为了检查虚拟现实设备的立体渲染是否有效，我们使用了一部装有 Gear VR 的三星手机。理论上，任何支持 WebVR 的移动浏览器都应该能够以立体方式渲染我们的应用程序，以便与 GearVR、谷歌 Cardboard 甚至谷歌 Daydream 配合使用。但是这个库和 API 都还在开发中，所以支持可能不可靠。[这里有一个很好的总结](https://webvr.rocks/)目前支持 WebVR 功能的浏览器。

## 开发设置和项目结构

让我们从安装 React VR CLI 工具开始。然后在一个名为`GDVR_REACTVR_SITEPOINT_GALLERY`的新文件夹中创建一个新的 React VR 项目及其所有依赖项:

```
npm install -g react-vr-cli
react-vr init GDVR_REACTVR_SITEPOINT_GALLERY
cd GDVR_REACTVR_SITEPOINT_GALLERY 
```

要启动本地开发服务器，我们将运行一个 npm 脚本，并在 Chrome 中浏览到`http://localhost:8081/vr/`。

```
npm start 
```

如果你看到一个黑白房间，有楼梯、柱子和一个“你好”文本平面，一切都是正确的。

[//giphy.com/embed/l4FGFGGvkWca8LUys](//giphy.com/embed/l4FGFGGvkWca8LUys)

[通过 GIPHY](https://giphy.com/gifs/sitepoint-reactvr-initial-commit-l4FGFGGvkWca8LUys)

React VR CLI 搭建的最重要的文件和文件夹有:

*   `index.vr.js`。这是应用程序的入口点。目前，该文件包含 React VR 默认场景的完整源代码，正如我们已经在浏览器中看到的那样。
*   `static_assets`。该文件夹应该包含应用程序中使用的所有资产。我们将把等矩形图像和按钮图形放在这个文件夹中。

我们希望我们的项目有三个组成部分:

*   一个 **Canvas** 组件，它保存了完整球体图像的代码
*   一个**按钮**组件，它创建一个 VR 按钮来交换图像
*   一个 **UI** 组件，它用四个按钮组件构建了一个 UI。

这三个组件都有自己的文件，所以让我们创建一个`components`文件夹来包含这些文件。然后，在我们开始创建 Canvas 组件之前，让我们从`index.vr.js`文件中删除搭建的示例代码，使其看起来像这样:

```
/* index.vr.js */
import React from 'react';
import {
  AppRegistry,
  View,
} from 'react-vr';

export default class GDVR_REACTVR_SITEPOINT_GALLERY extends React.Component {
  render() {
    return (
      <View>  </View>
    );
  }
};

AppRegistry.registerComponent('GDVR_REACTVR_SITEPOINT_GALLERY', () => GDVR_REACTVR_SITEPOINT_GALLERY); 
```

## 将球形图像添加到场景中

为了给场景添加一个球形图像，我们将在`components`文件夹中创建一个新文件`Canvas.js`:

```
/* Canvas.js */
import React from 'react';
import {
  asset,
  Pano,
} from 'react-vr';

class Canvas extends React.Component {

  constructor(props) {
    super(props);

    this.state = {
      src: this.props.src,
    }
  }

  render() {
    return (
      <Pano source={asset(this.state.src)}/>
    );
  }
};

export default Canvas; 
```

在前六行代码中，我们导入依赖项。然后，我们声明画布组件，并使用 JSX 语法定义它的呈现方式。

如果你想更多地了解 JSX，我建议你查看【React 和 JSX 入门”。

看一下 JSX 代码，就会发现 Canvas 组件只返回一个组件，React VR `<Pano>`组件。它有一个参数`source` prop，使用一个`asset`函数从`static_assets`文件夹中加载图像。参数引用了一个状态，我们在构造函数中初始化了这个状态。

在我们的例子中，我们不想在画布组件本身中定义路径，而是使用`index.vr.js`文件来定义所有图像路径。这就是为什么`state.src`对象指的是组件的`props`对象。

*查看 [React 的 ReactJS 文档。组件](https://facebook.github.io/react/docs/react-component.html)如果您想了解更多关于状态和道具的知识。*

让我们继续修改`index.vr.js`文件以使用画布组件并将其渲染到场景中:

```
/* index.vr.js */
import React from 'react';
import {
  AppRegistry,
  View,
} from 'react-vr';
import Canvas from './components/Canvas';

export default class GDVR_REACTVR_SITEPOINT_GALLERY extends React.Component {

  constructor() {
    super();

    this.state = {
      src: 'reactconf_00.jpg',
    };
  }

  render() {
    return (
      <View>  <Canvas
          src={this.state.src}
        />  </View>
    );
  }
};

AppRegistry.registerComponent('GDVR_REACTVR_SITEPOINT_GALLERY', () => GDVR_REACTVR_SITEPOINT_GALLERY); 
```

除了已经使用的 React VR 依赖项，我们还需要导入我们的自定义画布组件。接下来，我们在第六行声明应用程序类:

```
/* index.vr.js */
import Canvas from './components/Canvas'; 
```

然后，我们添加`<Canvas>`组件作为`<View>`组件的子组件。我们使用`src`作为组件的道具，因为我们在画布组件中引用了它。浏览器中的外观现在应该显示全景图像，我们应该已经能够与它进行交互。

[//giphy.com/embed/l4FGz7B0fqPFHC0so](//giphy.com/embed/l4FGz7B0fqPFHC0so)

[通过 GIPHY](https://giphy.com/gifs/sitepoint-reactvr-canvas-component-l4FGz7B0fqPFHC0so)

## 创建一个 UI 组件来保存四个按钮

我们现在要做的是创建四个按钮，用户可以触发它们来交换图像。所以我们将添加两个新组件:一个 UI 组件，和它的子组件，一个按钮组件。让我们从按钮组件开始:

```
/* Button.js */
import React from 'react';
import {
  asset,
  Image,
  View,
  VrButton,
} from 'react-vr';

class Button extends React.Component {

  onButtonClick = () => {
    this.props.onClick();
  }

  render () {
    return (
      <View
        style={{
          alignItems: 'center',
          flexDirection: 'row',
          margin: 0.0125,
          width: 0.7,
        }}
      >  <VrButton
          onClick={this.onButtonClick}
        >  <Image
            style={{
              width: 0.7,
              height: 0.7,
            }}
            source={asset(this.props.src)}
          >  </Image>  </VrButton>  </View>
    );
  }
};

export default Button; 
```

为了构建按钮，我们使用 React VR 的`<VrButton>`组件，它是我们在第六行导入的。此外，我们使用一个图像组件将我们的资产图像添加到每个按钮，因为`<VrButton>`组件本身没有外观。像以前一样，我们使用一个道具来定义图像源。我们在这个组件中使用了两次的另一个特性是`style`道具，用来为每个按钮及其图像添加布局值。`<VrButton>`还使用了一个事件监听器`onClick`。

为了给我们的场景添加四个按钮组件，我们将使用 UI 父组件，之后我们将在`index.vr.js`中添加它作为子组件。在编写 UI 组件之前，让我们创建一个 config 对象来定义等矩形图像、按钮图像和按钮本身之间的关系。为此，我们在`index.vr.js`文件中的导入语句后声明一个常量:

```
/* index.vr.js */
const Config = [
  {
    key: 0,
    imageSrc: 'reactconf_00.jpg',
    buttonImageSrc: 'button-00.png',
  },
  {
    key: 1,
    imageSrc: 'reactconf_01.jpg',
    buttonImageSrc: 'button-01.png',
  },
  {
    key: 2,
    imageSrc: 'reactconf_02.jpg',
    buttonImageSrc: 'button-02.png',
  },
  {
    key: 3,
    imageSrc: 'reactconf_03.jpg',
    buttonImageSrc: 'button-03.png',
  }
]; 
```

UI 组件将使用配置中定义的值来处理凝视和点击事件:

```
/* UI.js */
import React from 'react';
import {
  View,
} from 'react-vr';
import Button from './Button';

class UI extends React.Component {

  constructor(props) {
    super(props);

    this.buttons = this.props.buttonConfig;
  }

  render () {
    const buttons = this.buttons.map((button) =>
      <Button
        key={button.key}
        onClick={()=>{
          this.props.onClick(button.key);
        }}
        src={button.buttonImageSrc}
      />
      );

    return (
      <View
        style={{
          flexDirection: 'row',
          flexWrap: 'wrap',
          transform: [
            {rotateX: -12},
            {translate: [-1.5, 0, -3]},
          ],
          width: 3,
        }}
      >  {buttons}  </View>
    );
  }
};

export default UI; 
```

为了设置图像的来源，我们使用已经添加到`index.vr.js`文件中的配置值。我们还使用道具`onClick`来处理点击事件，稍后我们还会将它添加到`index.vr.js`文件中。然后，我们创建按钮配置对象中定义的尽可能多的按钮，稍后将它们添加到将渲染到场景中的 JSX 代码中:

```
/* UI.js */
const buttons = this.buttons.map((button) =>
  <Button
    key={button.key}
    onClick={()=>{
      this.props.onClick(button.key);
    }}
    src={button.buttonImageSrc}
  />
); 
```

现在，我们要做的就是将 UI 组件添加到在`index.vr.js`文件中定义的场景中。因此，我们在导入画布组件之后立即导入 UI 组件:

```
/* index.vr.js */
import UI from './components/UI'; 
```

接下来，我们将`<Canvas>`组件添加到场景中:

```
/* index.vr.js */
<View>  <Canvas
    src={this.state.src}
  />  <UI
    buttonConfig={Config}
    onClick={(key)=>{
      this.setState({src: Config[key].imageSrc});
    }}
  />  </View> 
```

当在浏览器中检查这段代码时，您会注意到此时单击并没有触发图像源交换。为了监听更新的 props，我们必须在构造函数之后向 Canvas 组件添加另一个函数。

*如果你对 React 组件的生命周期感兴趣，你可能想了解一下 [React。React 文档中的组件](https://facebook.github.io/react/docs/react-component.html)。*

```
/* Canvas.js */
componentWillReceiveProps(nextProps) {
  this.setState({src: nextProps.src});
} 
```

浏览器中的测试现在应该成功了，单击按钮图像应该会改变球形图像。

[//giphy.com/embed/3oKIPBaT6gPwxk2AEM](//giphy.com/embed/3oKIPBaT6gPwxk2AEM)

[通过 GIPHY](https://giphy.com/gifs/sitepoint-reactvr-button-component-3oKIPBaT6gPwxk2AEM)

## 为按钮状态转换添加动画

为了使按钮更好地响应用户交互，我们希望添加一些悬停状态以及默认空闲和悬停状态之间的转换。为此，我们将使用[动画库](https://facebook.github.io/react-vr/docs/animated.html)和[缓动函数](https://facebook.github.io/react-vr/docs/easing.html)，然后为每个过渡编写函数:`animateIn`和`animateOut`:

```
/* Button.js */
import React from 'react';
import {
  Animated,
  asset,
  Image,
  View,
  VrButton,
} from 'react-vr';

const Easing = require('Easing');

class Button extends React.Component {

  constructor(props) {
    super();

    this.state = {
      animatedTranslation: new Animated.Value(0),
    };
  }

  animateIn = () => {
    Animated.timing(
      this.state.animatedTranslation,
      {
        toValue: 0.125,
        duration: 100,
        easing: Easing.in,
      }
    ).start();
  }

  animateOut = () => {
    Animated.timing(
      this.state.animatedTranslation,
      {
        toValue: 0,
        duration: 100,
        easing: Easing.in,
      }
    ).start();
  }

  onButtonClick = () => {
    this.props.onClick();
  }

  render () {
    return (
      <Animated.View
        style={{
          alignItems: 'center',
          flexDirection: 'row',
          margin: 0.0125,
          transform: [
            {translateZ: this.state.animatedTranslation},
          ],
          width: 0.7,
        }}
      >  <VrButton
          onClick={this.onButtonClick}
          onEnter={this.animateIn}
          onExit={this.animateOut}
        >  <Image
            style={{
              width: 0.7,
              height: 0.7,
            }}
            source={asset(this.props.src)}
          >  </Image>  </VrButton>  </Animated.View>
    );
  }
};

export default Button; 
```

添加依赖关系后，我们定义一个新的状态来保存我们想要制作动画的平移值:

```
/* Button js */
constructor(props) {
  super();

  this.state = {
    animatedTranslation: new Animated.Value(0),
  };
} 
```

接下来，我们定义两个动画，每个都在一个单独的函数中，分别描述光标进入按钮和光标退出按钮时播放的动画:

```
/* Button.js */
animateIn = () => {
  Animated.timing(
    this.state.animatedTranslation,
    {
      toValue: 0.125,
      duration: 100,
      easing: Easing.in,
    }
  ).start();
}

animateOut = () => {
  Animated.timing(
    this.state.animatedTranslation,
    {
      toValue: 0,
      duration: 100,
      easing: Easing.in,
    }
  ).start();
} 
```

为了在 JSX 代码中使用`state.animatedTranslation`值，我们必须通过添加`<Animated.view>`来使`<View>`组件具有动画效果:

```
/* Button.js */
<Animated.View
  style={{
    alignItems: 'center',
    flexDirection: 'row',
    margin: 0.0125,
    transform: [
      {translateZ: this.state.animatedTranslation},
    ],
    width: 0.7,
  }}
> 
```

当事件监听器`onButtonEnter`和`onButtonExit`被触发时，我们将调用该函数:

```
/* Button.js */
<VrButton
  onClick={this.onButtonClick}
  onEnter={this.animateIn}
  onExit={this.animateOut}
> 
```

在浏览器中测试我们的代码应该显示每个按钮在 z 轴上的位置之间的转换:

[//giphy.com/embed/3og0IOzMY9aNkZ5Acw](//giphy.com/embed/3og0IOzMY9aNkZ5Acw)

[通过 GIPHY](https://giphy.com/gifs/sitepoint-reactvr-button-hover-animation-3og0IOzMY9aNkZ5Acw)

## 构建和测试应用程序

在支持 WebVR 的浏览器中打开您的应用，并导航到您的开发服务器，不要使用`http://localhost:8081/vr/index.html`，而是使用您的 IP 地址，例如`http://192.168.1.100:8081/vr/index.html`。然后，点击`View in VR`按钮，这将打开一个全屏视图，并开始立体渲染。

[//giphy.com/embed/3ohzdDUVq72n3nkX7O](//giphy.com/embed/3ohzdDUVq72n3nkX7O)

[通过 GIPHY](https://giphy.com/gifs/sitepoint-reactvr-stereoscopic-view-3ohzdDUVq72n3nkX7O)

要将你的应用上传到服务器，你可以运行 npm 脚本`npm run bundle`，它将在`vr`目录下用编译后的文件创建一个新的`build`文件夹。在您的 web 服务器上，您应该具有以下目录结构:

```
Web Server
├─ static_assets/
│
├─ index.html
├─ index.bundle.js
└─ client.bundle.js 
```

## 更多资源

这就是我们所要做的用 React VR 创建一个小的 WebVR 应用程序。你可以在 GitHub 上找到[完整的项目代码。](https://github.com/sitepoint-editors/GDVR_REACTVR_SITEPOINT_GALLERY/tree/develop)

React VR 还有几个我们在本教程中没有讨论的组件:

*   有一个用于呈现文本的`Text`组件。
*   四种不同的灯光组件可以用来给场景添加灯光:`AmbientLight`、`DirectionalLight`、`PointLight`和`Spotlight`。
*   一个`Sound`组件将空间声音添加到 3D 场景中的一个位置。
*   要添加视频，可以使用`Video`组件或`VideoPano`组件。一个特殊的`VideoControl`组件增加了对视频播放和音量的控制。
*   使用`Model`组件，我们可以将`obj`格式的 3D 模型添加到应用程序中。
*   一个`CylindricalPanel`组件可以用来将子元素与圆柱体的内表面对齐——例如，对齐用户界面元素。
*   创建 3D 图元有三个组件:一个`sphere`组件、一个`plane`组件和一个`box`组件。

还有，React VR 还在开发中，这也是它只在 Carmel 开发者预览版浏览器中运行的原因。如果你有兴趣了解更多关于 React VR 的信息，这里有一些有趣的资源:

*   [React VR 文档](https://facebook.github.io/react-vr/docs/getting-started.html)
*   [在 GitHub 上反应 VR](https://github.com/facebook/react-vr)
*   [Awesome React VR](https://github.com/nikgraf/awesome-react-vr) ，React VR 资源集合。

如果你想更深入地了解 WebVR，这些文章可能适合你:

*   " [A-Frame:当今将虚拟现实带入网络的最简单方式](https://www.sitepoint.com/a-frame-the-easiest-way-to-bring-vr-to-the-web-today/)"
*   "[通过虚拟现实视图在网络上嵌入虚拟现实](https://www.sitepoint.com/embedding-virtual-reality-across-the-web-with-vr-views/)"

你用过 React VR 吗？你用它做过什么很酷的项目吗？很想在评论里听听大家的看法和经历！

* * *

**如果你喜欢这篇文章，并想从头开始学习 React，请查看我们的[React ES6 方式](https://www.sitepoint.com/premium/courses/react-the-es6-way-2914)课程。**

* * *

*这篇文章由[莫里茨·克罗格](https://www.sitepoint.com/author/morkro/)和[蒂姆·塞韦里恩](https://www.sitepoint.com/author/tseverien/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章