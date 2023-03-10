# 快速提示:在 React 中构建一个视频播放器组件

> 原文：<https://www.sitepoint.com/quick-tip-video-player-component-react/>

在这个快速技巧中，我们将创建一个 React 视频组件。这将使您能够定义一个视频列表，并允许您的访问者在它们之间循环。该组件将支持 Vimeo、YouTube 和 Dailymotion，但你可以轻松地扩展它以支持其他视频提供商。

作为一个开发环境，我们将使用 [react-hot-boilerplate](https://github.com/gaearon/react-hot-boilerplate) ，它支持热重载。这意味着我们可以构建我们的组件，并在浏览器中立即看到结果，而无需在每次代码更改时刷新页面。

你可以在我们的 GitHub repo 上找到[源代码，以及文章底部的演示。](https://github.com/sitepoint-editors/react-social-video-sitepoint)

## 从样板文件开始

我们将从样板文件开始设置我们的工作环境。假设您的机器上安装了 git，让我们通过运行以下命令来克隆 repo:

```
git clone https://github.com/gaearon/react-hot-boilerplate 
```

这将在我们所在的目录中创建一个文件夹`react-hot-boilerplate`。现在，让我们导航到该文件夹，通过运行以下命令来安装依赖项:

```
cd react-hot-boilerplate
npm install 
```

接下来，我们需要安装稍后将会用到的查询字符串。

```
npm install --save query-string 
```

最后，我们可以从以下几点开始:

```
npm start 
```

如果事情按计划进行，提示中会出现消息`Listening at localhost:3000`。Webpack 仍然需要处理这些文件，然后才能提供服务。延迟一段时间后，提示中应该会出现一个操作序列，以`webpack: bundle is now VALID`结尾，表示一切准备就绪。

通过在浏览器中导航到 [http://localhost:3000/](http://localhost:3000/) ，你现在应该能够看到“Hello，world”已显示。

## 创建组件

首先要做的是在`src/social-video.js`创建组件文件。目前，组件工作所需的最少代码和一些虚拟呈现内容就足够了。

```
import React, {Component} from 'react';

export default class SocialVideo extends Component {
  render() {
    return (
      <h1>Social Video</h1>
    );
  }
} 
```

接下来，在`src/App.js`中，让我们创建一个视频列表，我们可以使用*下一个*和*上一个*按钮来循环播放。

```
import React, { Component } from 'react';
import SocialVideo from './social-video';

var videos = [
  {
    service: 'youtube',
    video: 'https://www.youtube.com/watch?v=XxVg_s8xAms'
  },
  {
    service: 'vimeo',
    video: 'https://vimeo.com/151715092'
  },
  {
    service: 'dailymotion',
    video: 'http://www.dailymotion.com/video/x3oc771_la-voiture-du-futur_tech'
  }
];

export default class App extends Component {
  constructor (props) {
    super(props);
    this.state = {
      videoIndex: 0
    };
  }

  goToVideo (index) {
    let videoIndex = index;
    if (videoIndex < 0) {
      videoIndex = videos.length - 1;
    } else if (videoIndex >= videos.length) {
      videoIndex = 0;
    }
    this.setState({
      videoIndex
    });
  }

  render() {
    const {service, video} = videos[this.state.videoIndex];
    return (
      <div>
        <SocialVideo service={service} video={video} width={500} height={270} />
        <p>
          <span>{service}: </span>
          <span>{video}</span>
        </p>
        <button onClick={this.goToVideo.bind(this, this.state.videoIndex - 1)}>
          Previous
        </button>
        <button onClick={this.goToVideo.bind(this, this.state.videoIndex + 1)}>
          Next
        </button>
      </div>
    );
  }
} 
```

这里没有什么太花哨的东西。我们正在定义一个数组，其中包含我们想要切换的视频的数据。在`App`状态中，我们保存了数组中的位置，该位置指示哪个视频被选中。我们还显示关于所选视频的信息，并呈现我们的`SocialVideo`组件。现在这个组件没有做太多的事情，所以我们需要去做它。

我们将把视频信息作为`props`传递给`SocialVideo`组件，并使用该信息呈现适当的内容。组件没有自己的状态。这将使其可重复使用和可预测，意味着每次相同的输入等于相同的输出。这是完整的组件。如果您在家继续，请确保在保存文件时打开浏览器，并观察 UI 自动更新。

```
import qs from 'query-string';
import React, {Component, PropTypes} from 'react';

export default class SocialVideo extends Component {
  static propTypes = {
    service: PropTypes.oneOf(['youtube', 'vimeo', 'dailymotion']).isRequired,
    video: PropTypes.string.isRequired
  };

  static urlMap = new Map([
    ['youtube', 'http://www.youtube.com/embed/'],
    ['vimeo', 'https://player.vimeo.com/video/'],
    ['dailymotion', 'http://www.dailymotion.com/embed/video/']
  ]);

  getIdFromVideoString (vString) {
    const urlArr = vString.split('/');
    const idString = urlArr[urlArr.length - 1];
    const queryParams = qs.extract(vString);

    return (queryParams && qs.parse(queryParams).v) || idString || '';
  }

  render() {
    const {service, video, ...htmlTags} = this.props;
    const src = `${SocialVideo.urlMap.get(service)}${this.getIdFromVideoString(video)}`;

    return (
      <iframe
        src={src}
        frameBorder='0'
        webkitAllowFullScreen
        mozallowfullscreen
        allowFullScreen
        {...htmlTags}
      />
    );
  }
} 
```

我们来分析一下。您可能已经注意到，我们包含了查询字符串依赖项。这将用于解析我们发送到组件的视频 URL。

组件需要两个`props`，在`propTypes`中定义:

*   `service` —视频提供商
*   `video` —视频 URL 或 ID

现在让我们看看我们在`render`函数中做了什么。我们从以下内容开始:

```
const {service, video, ...htmlTags} = this.props; 
```

我们在这里做的是[析构](https://www.sitepoint.com/es6-destructuring-assignment/)的`props`以获得`service`和`video`的值，然后将可能已经传递给组件的任何剩余的`props`赋给变量`htmlTags`。

我们现在需要处理`service`和`video`变量来生成一个`src`变量，我们将使用它来创建一个`<iframe>`标签。下面的代码就是这么做的:

```
const src = `${SocialVideo.urlMap.get(service)}${this.getIdFromVideoString(video)}`; 
```

这里我们生成一个字符串，从名为`urlMap`的静态属性中获得默认的嵌入 URL。字符串的第二部分由视频 ID 组成，它是通过调用函数`getIdFromVideoString`生成的。

现在我们已经计算了`src`变量，我们只需要将它呈现给一个`<iframe>`:

```
return (
  <iframe
    src={src}
    frameBorder='0'
    webkitAllowFullScreen
    mozallowfullscreen
    allowFullScreen
    {...htmlTags}
  />
); 
```

请注意我们是如何在 spread 操作符(`...`)之前传递`htmlTags`的，这样如果额外的`props`被发送到组件，比如:`className`、`style`、`width`、`height`等。，它们被插入到`<iframe>`标签中。这使得组件更加可定制和动态。

我们现在可以看到我们的组件在 [http://localhost:3000/](http://localhost:3000/) 播放选定的视频。这也显示在下面的演示中。

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [XXQrKG](http://codepen.io/SitePoint/pen/XXQrKG/) 。