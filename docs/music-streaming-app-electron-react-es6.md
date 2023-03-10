# 使用 Electron、React 和 ES6 构建音乐流应用程序

> 原文：<https://www.sitepoint.com/music-streaming-app-electron-react-es6/>

*本文由[马克·布朗](https://www.sitepoint.com/author/mbrown)、[丹王子](https://www.sitepoint.com/author/dprince)和[布鲁诺莫塔](https://www.sitepoint.com/author/bmota/)进行同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

由 GitHub 开发的 Electron 是一个框架，它允许你利用你的网页设计技能来构建流畅的、跨平台的桌面应用。在本教程中，我将演示如何将电子的力量与 React、ES6 和 Soundcloud API 结合起来，创建一个时尚的音乐流媒体应用程序，将您最喜爱的音乐直接传输到您的桌面上。我还将演示如何打包应用程序，并将其作为一个可移植的、特定于操作系统的包进行分发。

本教程假设您对 React 有基本的了解。如果你在开始之前想要一本入门书，请查看我们的[入门教程](https://www.sitepoint.com/getting-started-react/)。本教程的代码可以从我们的 [GitHub repo](https://github.com/sitepoint-editors/electron-soundcloudplayer/) 获得。

## 我们正在构建的内容概述

这是我们的应用程序的外观:

![Soundcloud player](img/48c6ca072ca3c70e752db4890ce465b4.png)

我们将使用 React 来创建 UI，使用 SoundCloud API 来获取音轨，使用 Electron 来让应用程序在类似浏览器的环境中运行。正如你所看到的，它将有一个搜索字段，用于搜索要播放的音乐，结果将是每个结果的音频播放器。很像你在 SoundCloud 网站上看到的。

如果你想继续，确保你有一个 SoundCloud 账户和一个 SoundCloud 应用程序。记下 API 键，因为我们稍后会用到它。

## 添加电子和其他依赖项

首先将 Github 上的[电子快速启动报告克隆到一个名为`soundcloud-player`的文件夹中:](https://github.com/atom/electron-quick-start)

```
git clone https://github.com/atom/electron-quick-start soundcloud-player 
```

进入该文件夹，然后打开`package.json`文件并添加以下开发依赖项:

```
"devDependencies": {
  "electron-prebuilt": "^1.2.0",
  "babel-preset-es2015": "^6.9.0",
  "babel-preset-react": "^6.5.0",
  "babelify": "^7.3.0",
  "browserify": "^13.0.1"
} 
```

以下是每个包的简要描述:

*   [电子预构建](https://www.npmjs.com/package/electron-prebuilt)—安装电子预构建二进制文件以供命令行使用。
*   [babel-preset-es 2015](https://www.npmjs.com/package/babel-preset-es2015)—用于将 ES6 代码转换为 ES5 代码(可以在任何现代浏览器中运行)。
*   [babel-preset-react](https://www.npmjs.com/package/babel-preset-react)—用于将 JSX 代码转换成 JavaScript。
*   [babelify](https://www.npmjs.com/package/babelify)—browser ify 的 Babel transformer。
*   [browser ify](https://www.npmjs.com/package/browserify)—构建一个包，您可以在单个`<script>`标签中提供给浏览器。

在`dependencies`下增加以下内容:

```
"dependencies": {
  "node-soundcloud": "0.0.5",
  "react": "^0.14.8",
  "react-dom": "^0.14.8",
  "react-loading": "0.0.9",
  "react-soundplayer": "^0.3.6"
} 
```

以下是每个包的简要描述:

*   [node-SoundCloud](https://www.npmjs.com/package/node-soundcloud)—允许我们调用 SoundCloud API。
*   [React](https://www.npmjs.com/package/react)—React 库。允许我们创建 UI 组件。
*   [react-dom](https://www.npmjs.com/package/react-dom)—允许我们将 React 组件呈现到 DOM 中。
*   [反应-加载](https://www.npmjs.com/package/react-loading)—用作 app 的加载指示器。
*   React-sound player—React 组件，允许我们轻松地为 SoundCloud 创建定制的音频播放器。

一旦你添加了`dependencies`和`devDependencies`，执行`npm install`来安装它们。

最后，添加编译和启动应用程序的脚本。这将允许您运行`npm run compile`来编译应用程序，运行`npm start`来运行它。

```
"scripts": {
  "compile": "browserify -t [ babelify --presets [ react es2015 ] ] src/app.js -o js/app.js",
  "start": "electron main.js"
} 
```

当我们这样做的时候，我们可以删除电子快速启动的特定内容，并添加我们自己的合理默认值。

```
{
  "name": "electron-soundcloud-player",
  "version": "1.0.0",
  "description": "Plays music from SoundCloud",
  "main": "main.js",
  "scripts": {
    "start": "electron main.js",
    "compile": "browserify -t [ babelify --presets [ react es2015 ] ] src/app.js -o js/app.js"
  },
  "author": "Wern Ancheta",
  ...
} 
```

总而言之，你的`package.json`文件现在应该是这样的。

## 项目结构

这是我们打算如何组织我们的项目:

```
.
├── css
│   └── style.css
├── index.html
├── js
├── main.js
├── package.json
├── README.md
└── src
    ├── app.js
    └── components
        ├── ProgressSoundPlayer.js
        └── Track.js 
```

让我们创建那些缺失的目录:

```
mkdir -p css js src/components 
```

以及它们应该包含的文件:

```
touch css/style.css src/app.js src/components/ProgressSoundPlayer.js src/components/Track.js 
```

`js`目录将保存我们应用程序的编译后的 JavaScript,`css`目录保存我们应用程序的样式,`src`目录保存应用程序的组件。

在我们从电子快速启动回购中提取的文件中，我们可以删除以下内容:

```
rm renderer.js LICENSE.md 
```

剩下`main.js`和`ìndex.html`。在这两个文件中，`main.js`负责创建一个新的浏览器窗口，应用程序将在其中运行。然而，我们需要对它进行一些修改。首先调整第 13 行的宽度:

```
mainWindow = new BrowserWindow({width: 1000, height: 600}) 
```

其次，从第 19 行删除以下内容(否则我们的应用程序将初始化，显示开发工具):

```
mainWindow.webContents.openDevTools() 
```

当`main.js`创建新的浏览器窗口时，它将加载`index.html`(我们将在教程的后面查看这个文件)。从这里开始，应用程序将像在浏览器窗口中一样运行。

## 构建应用程序

### 轨道组件

接下来让我们为音频播放器创建`Track`组件(在 [src/components/Track.js](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/components/Track.js) 中)。

首先，我们需要 React 和 React SoundPlayer 提供的一些组件:

```
import React, {Component} from 'react';
import { PlayButton, Progress, Timer } from 'react-soundplayer/components'; 
```

注意，通过使用这个语法，我们有效地从 React 中提取了`Component`类。顾名思义，`Component`用于创建新组件。

然后我们创建一个名为`Track`的新组件，并给它一个`render`方法。请注意，我们正在导出这个类，以便稍后可以将它导入到另一个文件中。

```
export default class Track extends Component {
  render() {
    ...
  }
} 
```

在`render`方法中，我们从接收到的`props`中提取关于当前音轨的信息，然后使用[析构赋值](https://www.sitepoint.com/es6-destructuring-assignment/)将它们赋给自己的变量。这样我们可以用`track`代替`this.props.track`。

```
const { track, soundCloudAudio, playing, seeking, currentTime, duration } = this.props; 
```

然后，我们计算赛道的当前进度:

```
const currentProgress = currentTime / duration * 100 || 0; 
```

并返回组件的用户界面。

```
return (
  <div className="player">
    <PlayButton
      className="orange-button"
      soundCloudAudio={soundCloudAudio}
      playing={playing}
      seeking={seeking} />
    <Timer
      duration={duration}
      className="timer"
      soundCloudAudio={soundCloudAudio}
      currentTime={currentTime} />
    <div className="track-info">
      <h2 className="track-title">{track && track.title}</h2>
      <h3 className="track-user">{track && track.user && track.user.username}</h3>
    </div>
    <Progress
      className="progress-container"
      innerClassName="progress"
      soundCloudAudio={soundCloudAudio}
      value={currentProgress} />
  </div>
); 
```

从上面的代码可以看出，我们有一个非常标准的音频播放器。它有一个播放按钮，一个计时器(显示当前播放时间/持续时间)，上传歌曲的用户的标题和用户名，以及一个进度条。

下面是[完整组件的样子](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/components/Track.js)。

### ProgressSoundPlayer 组件

让我们继续讨论 ProgressSoundPlayer 组件([src/components/ProgressSoundPlayer . js](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/components/ProgressSoundPlayer.js))。这将作为上面创建的`Track`组件的包装器。

除了 React 和`Track`组件，我们还需要导入`SoundPlayerContainer`。`SoundPlayerContainer`是一个更高级别的容器，它用构建音频播放器所需的道具来传播其子容器。

```
import React, {Component, PropTypes} from 'react';
import { SoundPlayerContainer } from 'react-soundplayer/addons';
import Track from './Track'; 
```

接下来，我们将创建`ProgressSoundPlayer`组件。所有这些只是呈现包装了`Track`组件的`SoundPlayerContainer`。注意，我们不需要向`Track`组件传递任何东西，因为`SoundPlayerContainer`会在幕后自动为我们做这件事。然而，我们确实需要传递`resolveUrl`和`clientId`作为`SoundPlayerContainer`的道具。

```
export default class ProgressSoundPlayer extends Component {
  render() {
    const {resolveUrl, clientId} = this.props;
    return (
      <SoundPlayerContainer resolveUrl={resolveUrl} clientId={clientId}>
        <Track />
      </SoundPlayerContainer>
    );
  }
} 
```

最后，我们指定该组件所需的道具。在这种情况下，我们需要在呈现该组件时传入`resolveUrl`和`clientId`。

```
ProgressSoundPlayer.propTypes = {
  resolveUrl: PropTypes.string.isRequired,
  clientId: PropTypes.string.isRequired
}; 
```

指定`propTypes`是进入的一个[好习惯。如果组件需要的属性没有传递给它，这将在开发工具控制台中触发警告。注意，我们不必在前面的`Track`组件中这样做，因为`SoundPlayerContainer`负责传递所有必要的道具。](https://facebook.github.io/react/docs/reusable-components.html#prop-validation)

下面是[完整组件的样子](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/components/ProgressSoundPlayer.js)。

### 主要成分

主文件是 [src/app.js](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/app.js) 。这负责呈现应用程序的完整 UI——即搜索栏和音频播放器。

分解代码，我们首先导入我们需要的所有库。这些都在前面的依赖部分提到过(除了我们创建的`ProgressSoundPlayer`)。

```
import React, {Component} from 'react';
import ReactDOM from 'react-dom';
import ProgressSoundPlayer from './components/ProgressSoundPlayer';
import SC from 'node-soundcloud';
import Loading from 'react-loading'; 
```

添加您的 SoundCloud 客户端 ID:

```
var client_id = 'YOUR SOUNDCLOUD APP ID'; 
```

请注意，您可以使用类似于 [dotenv](https://github.com/motdotla/dotenv) 的东西，这样您就不必将这段数据推送到您的存储库中。

通过提供一个包含您的 soundcloud 客户端 ID 的对象来初始化 node-soundcloud 库。

```
SC.init({
  id: client_id
}); 
```

创建`Main`组件:

```
class Main extends Component {
    ...
} 
```

在类内部，定义构造函数方法。这允许我们添加代码来初始化这个类。在`constructor`方法中，我们调用`super()`来调用`Component`类的构造函数和`Component`类拥有的任何初始化代码。

```
constructor(props){
  super();
} 
```

接下来，我们设置应用程序的默认状态:

*   `query`是默认的搜索查询。
*   `hasResults`用于跟踪组件当前是否有来自 API 的任何结果。
*   `searchResults`存储当前搜索结果。
*   `isLoading`用于跟踪应用程序当前是否正在从 API 获取结果。当此设置为`true`时，微调器变得可见，表示有事情发生。

```
this.state = {
  query: '',
  hasResults: false,
  searchResults: [],
  isLoading: false
}; 
```

然后是`handleTextChange`方法。这用于更新`state`中`query`的值，如果按下 *Enter* 键，也调用`search`方法。当`onKeyUp`事件在搜索字段上被触发时，这个方法被调用。

```
handleTextChange(event){
  this.setState({
    query: event.target.value
  });
  if(event.key === 'Enter'){
    this.search.call(this);
  }
} 
```

之后我们有了`search`方法，它将查询发送到 SoundCloud API 并处理响应。首先，它将`isLoading`状态设置为`true`，这样微调器就可见了。然后，它向 SoundCloud API 的`tracks`端点发出一个`GET`请求。这个端点接受查询作为它的必需参数，但是我们也传入一个额外的`embeddable_by`参数来指定我们只想获取每个人都可以嵌入的轨迹。一旦我们得到响应，我们检查是否有错误，如果没有，我们用搜索结果更新`state`。此时，组件应该重新呈现以显示搜索结果。

```
search(){
  this.setState({
    isLoading: true
  });

  SC.get('/tracks', {
    q: this.state.query,
    embeddable_by: 'all'
  }, (err, tracks) => {
    if(!err){
      this.setState({
        hasResults: true,
        searchResults: tracks,
        isLoading: false
      });
    }
  });
} 
```

`render`方法呈现组件的 UI。这包含一个用于输入歌曲名称或艺术家的搜索字段和一个用于提交搜索的按钮。它还包含几个条件语句，用于呈现`Loading`组件(只有当`isLoading`具有真值时才可见)和搜索结果(只有当`hasResults`为真而`isLoading`为假时才显示)。

```
render(){
  return (
    <div>
      <h1>Electron SoundCloud Player</h1>
      <input type="search"
             onKeyUp={this.handleTextChange.bind(this)}
             className="search-field"
             placeholder="Enter song name or artist..." />
      <button className="search-button"
              onClick={this.search.bind(this)}>Search</button>
      <div className="center">
        {this.state.isLoading && <Loading type="bars" color="#FFB935" />}
      </div>
      {this.state.hasResults && !this.state.isLoading ?
       this.renderSearchResults.call(this) :
       this.renderNoSearchResults.call(this)}
    </div>
  );
} 
```

注意，我们必须用`bind()`来表示`handleTextChange`方法，用`call()`来表示`renderSearchResults`和`renderNoSearchResults`方法。这是因为 React 中的方法在使用 ES6 类语法时不会自动绑定。或者，您可以使用类似于 [decko](https://github.com/developit/decko) 的东西将特定的方法自动绑定到该类。例如:

```
import { bind } from 'decko';

// ...

@bind
handleTextChange(event){
  this.setState({
    query: event.target.value
  });
  if(event.key == 'Enter'){
    this.search();
  }
} 
```

接下来，我们有一个默认调用的方法，因为组件第一次呈现时没有搜索结果。

```
renderNoSearchResults(){
  return (
    <div id="no-results"></div>
  );
} 
```

以及当有搜索结果要显示时调用的方法。这将调用`searchResults`中的`map`方法来遍历所有结果，并在每次迭代中执行`renderPlayer`函数。

```
renderSearchResults(){
  return (
    <div id="search-results">
      {this.state.searchResults.map(this.renderPlayer.bind(this))}
    </div>
  );
} 
```

`renderPlayer`函数接受单个的`track`对象作为它的参数。我们用它作为`key`和`resolveUrl`道具的来源。如果你过去使用过 React，你就会知道当使用`map`方法来呈现列表时，我们总是必须传递一个唯一的`key`，否则 React 就会抱怨。另外两个道具:`clientId`和`resolveUrl`是`ProgressSoundPlayer`组件所需要的。`clientId`是您之前定义的 SoundCloud API 键，`resolveUrl`是引用特定音轨的唯一 URL。这与你在 SoundCloud 上访问特定音轨的页面时获得的 URL 相同。

```
renderPlayer(track){
  return (
    <ProgressSoundPlayer
      key={track.id}
      clientId={client_id}
      resolveUrl={track.permalink_url} />
  );
} 
```

最后，我们将组件呈现到 DOM 中。

```
var main = document.getElementById('main');
ReactDOM.render(<Main />, main); 
```

下面是[完整组件的样子](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/src/app.js)。

### 设计应用程序

应用程序的样式位于 [css/style.css](https://github.com/sitepoint-editors/electron-soundcloudplayer/blob/master/css/style.css) 中。样式表包含每个组件的样式声明(播放按钮、搜索按钮、进度条和我们使用的其他元素)。

### 索引文件

如前所述，当 Electron 的`main.js`文件创建新的浏览器窗口时，它将加载`index.html`。这里没有什么特别的东西，只有带有样式表和 JavaScript 文件的标准 HTML 文件。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Electron Soundcloud Player</title>
    <link rel="stylesheet" href="css/style.css">
  </head>
  <body>
    <div id="main"></div>
    <script src="js/app.js"></script>
  </body>
</html> 
```

## 编译应用程序

在电子环境中，你实际上可以像在标准 Node.js 应用程序中一样需要一些东西。这意味着您实际上可以使用类似于:

```
import fs from 'fs';

const buffer = fs.readFileSync(`${__dirname}/index.html`);
console.log(buffer.toString()); 
```

电子公司会很乐意为你运行它。

但由于我们已经用 ES6 和 JSX 编写了应用程序，我们不能真正使用这个功能。我们有的一个选择是使用 Babel 将 JSX 和 ES6 代码转换成浏览器可读的代码(ES5)。在前面的[依赖部分](#addingelectronandotherdependencies)中，我们安装了所有必要的包，以使其工作。所以现在您所要做的就是执行下面的命令来生成主 JavaScript 文件:

```
npm run compile 
```

## 运行和打包应用程序

你可以通过在你的项目根目录下执行`npm start`来运行这个应用。但那一点都不好玩。你还不如直接在浏览器里运行应用程序，然后就到此为止。相反，我们要做的是将应用程序打包到一个文件夹中。该文件夹将包含应用程序运行所需的所有文件。然后，您可以从该文件夹创建一个归档文件来分发您的应用程序。

要打包应用程序，我们需要安装电子打包程序:

```
npm install electron-packager -g 
```

安装后，您可以从项目的根目录向上一级，执行以下命令:

```
electron-packager ./soundcloud-player SoundCloudPlayer --version=1.2.4 --platform=linux --out=/home/jim/Desktop --arch=all --ignore="(node_modules|src)" 
```

分解这个命令，我们有:

*   `./soundcloud-player`—您的项目目录。
*   `SoundCloudPlayer`—您的应用程序名称。
*   `--version=1.2.0`—您想要使用的电子版本。在撰写本文时，它的版本是 1.2.0，所以如果您在以后阅读这篇文章，只要 API 没有重大变化，您就可以使用最新的版本。
*   `--platform=linux`—您要部署到的平台。在这种情况下，我使用 Linux，因为我在 Ubuntu 上。如果您想为所有主要平台(Windows、OSX、Linux)打包，您可以使用`--all`选项。
*   `--out=/home/wern/Desktop`—输出目录。这是创建包的地方。
*   `--arch=all`—处理器架构。我们已经指定了`all`,这意味着它将为 32 位和 64 位操作系统构建。
*   由于这款应用将与电子和 Chrome 打包在一起，所以它的尺寸会很大。我们唯一能做的防止它进一步膨胀的事情是排除所有我们不需要的文件。因为我们已经编译成一个 JavaScript 文件，我们不再需要在`node_modules`和`src`目录中的任何东西。

你可以在这个项目的主页上了解更多关于电子包装者的信息。您可以在[文档](https://github.com/electron-userland/electron-packager/blob/master/usage.txt)中阅读其他可用的命令行参数。

## 从这里去哪里

在本教程中，我们已经建立了一个非常简单的电子应用程序。它可以工作，但是我们仍然可以改进它。以下是一些可以改进的建议:

*   对搜索结果进行分页。
*   添加一个功能，一旦用户搜索，自动停止播放曲目。
*   移除按钮，直接从`handleTextChange`方法调用 search。
*   [将应用程序打包到 asar 档案文件](http://electron.atom.io/docs/v0.36.8/tutorial/application-packaging/)中，以避免将你的源代码暴露给所有人。
*   如果你真的想把你的应用发布到全世界。您可以为所有主要平台(Windows、OSX 和 Linux)创建安装程序。有一个叫做[电子建造者](https://github.com/loopline-systems/electron-builder)的项目可以让你做到这一点。

要找到更多的灵感，请查看 [SoundNode 应用程序](http://www.soundnodeapp.com/)——一个支持桌面 Mac、Windows 和 Linux 的 SoundCloud 的开源项目。

如果你想学习更多关于电子和使用 web 技术构建桌面应用的知识，我建议你查阅以下资源:

*   [NW . js](http://nwjs.io/)—之前被称为 node-webkit。允许您直接从 DOM 调用节点模块。如果你正在寻找电子替代品，你可能想看看这个。
*   [用 Electron 创建跨平台的桌面节点应用](https://www.sitepoint.com/desktop-node-apps-with-electron/)——最近的 SitePoint 教程。
*   [电子视频教程](https://www.youtube.com/playlist?list=PL1QRvYV-LXn6ESBl7qm1teB1U1CK1B6gv)
*   [电子公文](http://electron.atom.io/docs/v0.36.8/)

## 结论

在本教程中，我们已经学习了如何使用 electronic 创建一个光滑时尚的跨平台应用程序。更好的是，我们已经通过利用我们现有的 web 开发技能做到了这一点。我们还看到了将该应用打包并作为特定于操作系统的捆绑包分发是多么容易。

我很乐意在下面的评论中听到你用电子构建的应用。

## 分享这篇文章