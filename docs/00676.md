# 使用 React Native 开发 Android 应用程序，并在 Google Play 上发布

> 原文：<https://www.sitepoint.com/develop-an-android-app-with-react-native-publish-on-google-play/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/12/26/react-native-android-play-store)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

随着移动应用的使用预计将继续增长，现在是进入市场的最佳时机。Android 是迄今为止全球最受欢迎的操作系统，让手机用户使用 Android 应用的主要方式是谷歌 Play 商店。在本教程中，您将构建一个 React 原生应用程序，我将带您了解从如何选择设计到设置 React 原生环境的所有内容，以及如何充分利用编辑器并将其发布到 Google Play 的提示。

这是最终应用程序的样子:

![Final Result](img/b76e917b84e36ec697328c2b7c6d83c3.png)

对于本教程，我查看了 Karan Goel 的[大型项目列表](https://github.com/karan/Projects#mega-project-list)以获取项目灵感。您将构建**质因数分解**问题，该问题接受一个数字并返回其质因数。

## 确定 React 本地应用的界面主题

除了应用程序逻辑之外，用户界面和主题化是你要为你的应用程序做出的最重要的决定，包括像下拉菜单和按钮这样的控件，以及屏幕上的布局。由于我们使用的是基于 React 的技术，我们将使用[风格的组件](https://www.styled-components.com/)，这是一种流行的、轻量级的 React 应用程序主题化方法(它也[在 React Native](https://www.styled-components.com/docs/basics#react-native) 上运行良好)。有一个[的整个页面](https://github.com/styled-components/awesome-styled-components#built-with-styled-components)列出了组件和基于样式化组件的组件系统。在本教程中，您将使用样式化组件自己构建组件。

## 设置您的 Android 生产环境

为了构建最终的 APK(你将上传到商店的应用程序的格式)，你需要[安装 Android Studio](https://developer.android.com/studio/) 。一旦完成，确保你有 SDK 版本 27，因为这是什么反应原生使用。

### 安装 JDK

您还需要确保安装了最新的 Java 开发工具包(如版本 8)。根据您的平台，安装会有所不同。你可以使用甲骨文的 Java SDK 或者 T2 的 SDKMAN 来安装其他选项，比如 OpenJDK。

### 添加 React 本机 CLI 并初始化框架

接下来，您应该安装 React 本机命令行界面。为此，您应该确保您已经安装了[节点](https://www.google.com/search?q=install+nodejs)，这取决于您的操作系统。(我用的是[8 . 12 . 0 版](https://nodejs.org/dist/latest-v8.x/))。

```
npm install -g react-native-cli@2.0.1 
```

您应该有一个可用的命令`react-native`，它包括 **init** 选项。

```
react-native init prime_components 
```

这将创建`prime_components`目录，并将一个 React 本地项目放入其中，并带有一个可运行的框架。转到目录，[连接你的安卓手机](https://facebook.github.io/react-native/docs/running-on-device)或者[运行模拟器](https://developer.android.com/studio/run/managing-avds)(在[安装了安卓工作室](https://developer.android.com/studio/)之后)，运行项目。

**注意:**如果 Android Studio 提示你先打开一个项目，然后才能创建仿真器，可以指向`prime_components/android`目录。

```
cd prime_components
react-native run-android 
```

![Welcome to React Native](img/ad6edf0809fff3194b5502516ac41b1e.png)

要查看您将上传到 Play Store 的输出，请前往`android/app/build/outputs/apk/debug`。您应该会看到一个大约 8MB 大小的`app-debug.apk`。

### 减少你的 Android 应用程序的输出大小

您希望确保您的用户尽可能少地下载。它已经很小了(大约 8MB ),因为你使用的是命令行(类似 Expo 的东西甚至为基本应用程序产生 25MB ),但我们可以[进一步降低它](https://medium.com/@aswinmohanme/how-i-reduced-the-size-of-my-react-native-app-by-86-27be72bba640)。转到`android/app/build.gradle`，将以下变量更改为`true`:

```
def enableSeparateBuildPerCPUArchitecture = true
def enableProguardInReleaseBuilds = true 
```

您还必须删除`defaultConfig`的`ndk`部分，以删除 ndk abiFilters 错误中的[冲突配置:](https://stackoverflow.com/a/49802507)

```
ndk {
    abiFilters "armeabi-v7a", "x86"
} 
```

现在，重新运行`react-native run-android`之后，您应该在输出目录中看到两个(小得多——在 4MB 到 5MB 之间)apk。

### 将林挺添加到您的 React 本机应用程序中

编写任何 JavaScript(比如 React)都需要工具来确保你没有犯任何明显的错误，这对初学者来说是一个很大的帮助。其中最常见的是 [ESLint](https://eslint.org/) ，它可以直接插入到您的项目和编辑器中。

首先，使用 NPM 添加棉绒:

```
npm install -D eslint@5.9.0 babel-eslint@10.0.1 
```

一组常见的插件是 Airbnb 的配置,所以也添加这些插件:

```
npm install -D eslint-config-airbnb@17.1.0 eslint-plugin-jsx-a11y@6.1.2 eslint-plugin-react@7.11.1 eslint-plugin-import@2.14.0 
```

现在将以下内容放入`.eslintrc.js`(您需要创建这个文件):

```
module.exports = {
  'extends': 'airbnb',
  'parser': 'babel-eslint',
  'env': {
    'jest': true,
  },
  'rules': {
    'no-use-before-define': 'off',
    'react/jsx-filename-extension': 'off',
    'react/prop-types': 'off',
    'comma-dangle': 'off'
  },
  'globals': {
    "fetch": false
  }
} 
```

现在你只需要将插件添加到你的编辑器中。对于 Sublime 有 [ESLint](https://packagecontrol.io/packages/ESLint) ，它用**CTRL+ALT+e**(Mac 上的 **Cmd + Option + e** )给你一个错误(或问题)输出。还有一个[用于 VSCode](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 。

![ESLint](img/0d90934ad19633e3c6daa96c2f6401f5.png)

很多这样的错误可以使用`eslint --fix`命令自动修复，你可以使用 [ESLint Fix](https://packagecontrol.io/packages/ESLint%20Fix) 包从你的编辑器中运行这个命令。

## 为 React Native 添加样式化组件

使用 web 技术(即 HTML、CSS 和 JavaScript)反应和反应本机构建界面。与 React(和 React Native)一起使用的一个非常流行的库是[样式组件](https://github.com/styled-components/styled-components)，它清理了如何将 CSS 添加到组件中。

例如，看看下面的代码，摘自 React 原生示例应用程序(这是您使用`react-init`获得的内容):

```
export default class App extends Component<Props> {
  render() {
    return (
      <View style={styles.container}>
      <Text style={styles.welcome}>Welcome to React Native!</Text>
      <Text style={styles.instructions}>To get started, edit App.js</Text>
      <Text style={styles.instructions}>{instructions}</Text>
    </View>
  );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
}); 
```

声明 DOM 元素(`View`、`Text`)，将它们链接到样式，然后创建样式表。

对于样式化组件，您可以执行以下操作:

```
const Container = styled.View` flex: 1;
  justify-content: center;
  align-items: center;
  background-color: #F5FCFF; `;

const Welcome = styled.Text` fontSize: 20;
  text-align: center;
  margin: 10; `;

const Instructions = styled.Text` text-align: center;
  color: #333333;
  margin-bottom: 5; `;

export default class App extends Component<Props> {
  render() {
    return (
      <Container>
        <Welcome>Welcome to React Native!</Welcome>
        <Instructions>To get started, edit App.js</Instructions>
      </Container>
  );
  }
} 
```

它既干净又可移植(CSS 名称不冲突，等等。).

要安装它，从根目录运行`npm install styled-components@4.1.1`。

## 向 React 本机应用程序添加自定义字体

要将像 [Racing Sans One](https://fonts.google.com/specimen/Racing+Sans+One?selection.family=Racing+Sans+One%7CRoboto) 这样的自定义字体放入您的应用程序，您首先需要下载 ttf 并将其放入`assets/fonts`(您需要创建这个目录)。然后将以下内容添加到您的`package.json`:

```
"rnpm": {
  "assets": [
    "./assets/fonts/"
  ]
} 
```

最后从命令行运行`react-native link`。现在你应该在`android/app/src/main/assets/fonts`中看到你的字体。你现在应该能使用它了。创建一个`components`文件夹，并将以下内容放入`components/Header.js`中:

```
import styled from 'styled-components/native';

export default styled.Text` color: black;
  font-family: RacingSansOne-Regular;
  font-size: 32px;
  margin-top: 120px;
  background-color: transparent;
  text-align: center; `; 
```

然后，将其导入到您的`App.js`中，并在`Text`节点上方添加`<Heading>Welcome</Heading>`:

```
import Header from './components/Header';
...
export default class App extends Component<Props> {
  render() {
    return (
      <View style={styles.container}>
        <Header>Welcome</Header>
        <Text style={styles.welcome}>Welcome to React Native!</Text>
        <Text style={styles.instructions}>To get started, edit App.js</Text>
        <Text style={styles.instructions}>{instructions}</Text>
      </View>
    );
  }
} 
```

您应该得到一个格式良好的标题:

![Custom fonts](img/e41e035ebb27a5c28520e6b9da637b66.png)

## 在 Android 上调整你的应用程序为全屏

要让你的应用不显示标题栏，转到`android/app/src/main/res/values/styled.xml`并在`<styles>`元素中添加以下内容:

```
<item name="android:windowFullscreen">true</item> 
```

现在，当您重新运行时，您应该会看到导航栏消失了。

## 创建 Android 应用程序组件

可能需要很长时间来决定最终的设计，包括布局、颜色和字体。这通常是一个反复的过程。在这里，你将经历如何建立你在开始时看到的最终结果——这是受[相关在线教程](https://kylewbanks.com/blog/react-native-tutorial-part-2-designing-a-calculator)和[造型示例](https://moarwick.github.io/react-super-styled/)的启发——但请记住，获得你喜欢的东西需要时间。

将`App.js`更改如下:

```
import React from 'react';
import { Container, Header, Input, Keypad, ButtonRow } from './components';

const App = () => (
  <Container>
    <Header>Prime Components</Header>
    <Input>123456</Input>
    <Keypad>
      <ButtonRow keys={['1','2','3']} />
      <ButtonRow keys={['4','5','6']} />
      <ButtonRow keys={['7','8','9']} />
      <ButtonRow keys={['0','Clear','Go']} />
    </Keypad>
  </Container>
);

export default App; 
```

您可以在这里看到样式化组件的整洁程度。我们有一个标题、一个输入和一个键盘(所有的名字都由你选择),它们都被一个容器包围着。没有多余的信息。样式发生在组件中。

创建`components`目录。在`components/index.js`内放置以下内容:

```
export { default as Input } from './Input';
export { default as Container } from './Container';
export { default as Header } from './Header';
export { default as Keypad } from './Keypad';
export { default as Button } from './Button';
export { default as ButtonRow } from './ButtonRow'; 
```

这只是一个方便的模块，允许像`App.js`中那样导入，即“从{ Container，Header，Input，Keypad，ButtonRow }导入”。/components '；`.否则，您必须在单独的行上导入每个组件。

把这个放到`components/Container.js`:(注意:在 React Native 中你的组件必须使用大写字母！)

```
import styled from 'styled-components/native';

export default Container = styled.View` flex: 1; `; 
```

非常简单:您正在扩展一个`View`组件，并为其分配一个 flex 值(在这个上下文中意味着“占用一切”)。

`components/Header.js`:

```
import styled from 'styled-components/native';

export default Header = styled.Text` flex: 1.5;
  font-size: 80px;
  font-family: Chathura-ExtraBold;
  background-color: rgb(29, 31, 33);
  color: gold;
  text-align: center; `; 
```

还有一个样式化的文本组件，具有大的居中字体、金色和灰色背景。确保你像以前一样从谷歌字体安装了[查图拉字体](https://fonts.google.com/?selection.family=Chathura) ！

`components/Input.js`:

```
import styled from 'styled-components/native';

export default Input = styled.Text` flex: 2;
  text-align: right;
  font-family: Audiowide-Regular;
  text-align-vertical: center;
  font-size: 70;
  color: firebrick;
  background-color: gold; `; 
```

与之前类似，除了现在使用 Audiowide-Regular 字体(也可以从 Google Fonts 获得[)。](https://fonts.google.com/?selection.family=Audiowide)

`components/Keypad.js`:

```
import styled from 'styled-components/native';

export default Keypad = styled.View` flex: 6;
  background-color: rgb(29, 31, 33);
  padding-top: 10px;
  padding-bottom: 10px; `; 
```

也只是一个样式化的视图(本质上是一个类似 HTML 中的`<div>`的容器)。

`components/ButtonRow.js`:

```
import React from 'react';
import styled from 'styled-components/native';
import { Button } from '.';

const RowBox = styled.View` flex: 1;
  flex-direction: row; `;

export default ButtonRow = ({keys}) => (
  <RowBox>
    { /* https://stackoverflow.com/a/32157488 */ }
    {keys.map(key => (
      <Button text={key} key={key} />
    ))}
  </RowBox>
); 
```

在这里，事情变得复杂了。您正在从当前目录导入一个`Button`(您将马上创建它)，创建一个名为`RowBox`的未导出组件，然后`ButtonRow`被定义为具有一个名为`keys`的[反应属性](https://reactjs.org/docs/components-and-props.html)。

然后，将每个键映射到一个按钮组件。这是一种简单的方式，像使用`for each`一样遍历一个数组，并使用该值来设置`text`和`key`属性(必须设置一个 key 属性来使 DOM 对象唯一！).您将使用文本来呈现按钮。

`components/Button.js`:

```
import React from 'react';
import styled from 'styled-components/native';

/* https://kylewbanks.com/blog/react-native-tutorial-part-2-designing-a-calculator */

/* https://github.com/styled-components/styled-components/issues/149 */
const ButtonBox = styled.TouchableHighlight.attrs({
  underlayColor: '#193441',
})` flex: 1;
  align-items: center;
  justify-content: center;
  background-color: rgb(39, 41, 43);
  border-radius: 10px;
  margin: 5px; `;

const ButtonText = styled.Text` font-size: 30;
  font-family: Orbitron-Bold;
  color: ${props => props.text=="Go" ? "green" : "orange"}; `;

handleButtonPress = (value) => {

};

export default Button = ({text}) => (
  <ButtonBox onPress={() => handleButtonPress()}>
    <ButtonText text={text}>{text}</ButtonText>
  </ButtonBox>
); 
```

这是最后一个组件。首先创建一个`ButtonBox`,作为整个按钮的容器。它使用的是 [TouchableHighlight](https://facebook.github.io/react-native/docs/touchablehighlight) ，这是一个 React 原生组件，当被触摸时会改变颜色。样式是正常的，除了底层颜色(触摸时你看到的颜色)，因为这需要[破解，以在样式组件](https://github.com/styled-components/styled-components/issues/149)中工作。

接下来是文本控件`ButtonText`。为此从谷歌字体安装 Orbitron-BoldT3。使用布尔检查设置颜色(样式化组件的另一个伟大特性，全 CSS 支持)。本质上，如果文本是“Go ”,颜色将为绿色，否则为橙色。

接下来，定义一个函数来处理按钮按压(目前为空)，然后导出将两者结合起来的`Button`组件。使用在`Button`上设置的属性在`ButtonText`上设置`text`属性，这样当您设置样式时，样式化的组件可以看到它。

完成后，您应该会看到应用程序呈现出您在本文顶部看到的效果。

## 为您的 Android 应用添加颜色

当你在实际的手机上观看时，Android 模拟器中的颜色可能并不总是匹配的。这是因为 [Android Oreo 修复了色彩管理](https://www.androidcentral.com/everything-you-need-know-about-android-color-management)以确保所有设备看起来都一样。如果你有类似的问题，看看你是否使用 Android 7 或之前。

你可以限制你的用户使用 Android 8 或更高版本，但目前只有大约 20%的 Android 用户使用这一版本，所以最好在某个地方发布一条消息，告诉他们以后的版本会更好。

## 在反应中系好你的钮扣

这就是基本的设计和布局。现在你需要连接这些按钮，让它们真正做些事情。首先，您需要按下数字(和清除键)在数字显示中添加和删除数字。

为此，您需要了解 React 中的状态。

### 提升状态

我们需要当前数字(要显示的)和修改它的回调函数的单一真实来源。它必须是两者的父级(即`Input`和`Button`)。因为这是应用程序的一部分，你可以把它放到`App.js`的`App`组件中。

首先修改`App.js`并将其转换为一个 React 类(从当前的函数定义开始)，因为[需要它来捕获状态](https://reactjs.org/docs/state-and-lifecycle.html)。

```
import React from 'react';
import { Container, Header, Input, Keypad, ButtonRow } from './components';

class App extends React.Component {
  render() {
    return (
      <Container>
        <Header>Prime Components</Header>
        <Input>123456</Input>
        <Keypad>
          <ButtonRow keys={['1','2','3']} />
          <ButtonRow keys={['4','5','6']} />
          <ButtonRow keys={['7','8','9']} />
          <ButtonRow keys={['0','Clear','Go']} />
        </Keypad>
      </Container>
    );
  }
}

export default App; 
```

`App`现在是一个 [JavaScript ES6 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)，它有一个返回 JSX 的`render()`方法。

重新加载模拟器/应用程序(在模拟器中你可以使用 R+R ),它仍然应该像以前一样渲染。

现在在您的`render()`方法上添加以下内容:

```
constructor(props) {
  super(props);
  this.state = {number:123456};
} 
```

您正在覆盖 [React 构造函数](https://reactjs.org/docs/react-component.html#constructor)，调用父对象(您总是必须这样做)，然后将状态设置为带有一个成员的 JSON 对象:`number`(我们已经将其初始化为旧值:123456)。

现在将此添加到`App.js`中的`return(...)`上方:

```
const { state } = this; 
```

这个[析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)从`this`到一个局部变量的状态。将`<Input>`线改为`<Input>{state.number}</Input>`。现在，输入正在从状态中呈现。重新加载，您应该看到相同的输出(并更改状态构造函数以查看它的变化)。

### 修改 React 中的状态

现在，您需要告诉每个按钮调用应用程序类中定义的函数。将以下内容添加到构造函数中:

```
this.press = this.press.bind(this); 
```

这将使`press`函数可以访问`this`(当您调用`this.setState`时需要用到它)。

接下来，定义一个 press 函数，它只提醒哪个按钮被按下了。

```
press = (value) => {
  alert(value);
}; 
```

现在将这个函数传递给你的按钮行:

```
<ButtonRow func={this.press} keys={['1','2','3']} />
<ButtonRow func={this.press} keys={['4','5','6']} />
<ButtonRow func={this.press} keys={['7','8','9']} />
<ButtonRow func={this.press} keys={['0','Clear','Go']} /> 
```

修改`ButtonRow`以接受这个值，并将其传递给创建的`Button`。

```
export default ButtonRow = ({func,keys}) => (
  <RowBox>
    {keys.map(key => (
      <Button func={func} text={key} key={key} />
    ))}
  </RowBox>
); 
```

最后，更改`Button`以获取该函数，并通过按下的按钮调用它。您也可以移除先前的按钮处理程序。

```
export default Button = ({func,text}) => (
  <ButtonBox onPress={() => func(text)}>
    <ButtonText text={text}>{text}</ButtonText>
  </ButtonBox>
); 
```

现在，当你按下一个按钮时，你应该会看到一个提示:

![Buttons](img/8146c908231c8fa4a3af5e76573a4018.png)

你现在有一个单一的函数，定义在**顶部**，它控制按钮做什么。

### 在 React 中正确使用类属性

为了使事情变得简单一些，你可以在 React 中使用类属性来初始化状态。只需取出构造函数并替换为:

```
state = {
  number: 123456
}; 
```

### 更新您的 React 本机应用程序显示

当按下数字按钮时，您需要将该数字添加到当前数字中。此外，当按下**清除**时，您必须删除。先做那个。将`press()`更改如下:

```
press = (value) => {
  let { number } = this.state;
  if (value === 'Clear') {
    number = Math.floor(number / 10);
    this.setState({ number });
  }
} 
```

首先，使用析构从状态中提取数字的值(使用 linter 之前你会看到它是首选的)。然后如果键是`Clear`，将数字除以十，对其取底(即去掉最后一位)，然后设置状态。这应该足以使清除按钮工作。

现在将以下内容添加到`press()`中的 if 语句中:

```
else if (value != 'Go' && number<1000000) {
  number += value;
  this.setState({ number });
} 
```

因此，如果`Go`没有被按下，并且值小于一百万(你必须在某个地方停下来——手机没有那么快),将值添加到后面(将其解释为字符串而不是整数),并再次设置状态。这应该能让每个按钮都工作。

剩下唯一要做的就是`Go`按钮了。

### 向您的 Android 应用程序添加屏幕

当按下`Go`按钮时，您想要隐藏键盘并显示进度屏幕。为此，您需要另一个状态变量，一个布尔值，所以将它添加到 state 类属性，即

```
state = {
  number: 123456,
  processing: false
}; 
```

另外在**按压**功能中增加一个`Go`的检查:

```
else if (value === 'Go') {
  this.setState({ processing: true });
} 
```

然后创建`components/Processing.js`:

```
import React from 'react';
import styled from 'styled-components/native';

const ProcessingBox = styled.Text` flex: 6;
  font-size: 30;
  text-align: center;
  text-align-vertical: center;
  font-family: Orbitron-Bold;
  color: red;
  background-color: rgb(29, 31, 33);
  padding-top: 10px;
  padding-bottom: 10px; `;

export default () => (
  <ProcessingBox>Processing</ProcessingBox>
); 
```

将它添加到您的`components/index.js`:

```
export { default as Processing } from './Processing'; 
```

然后将其导入到您的`App.js`的顶部:

```
import { Container, Header, Input, Keypad, ButtonRow, Processing } from './components'; 
```

并使用`processing`布尔值来决定是呈现它还是小键盘:

```
{state.processing ? (
  <Processing />
) : (
  <Keypad>
    <ButtonRow func={this.press} keys={['1', '2', '3']} />
    <ButtonRow func={this.press} keys={['4', '5', '6']} />
    <ButtonRow func={this.press} keys={['7', '8', '9']} />
    <ButtonRow func={this.press} keys={['0', 'Clear', 'Go']} />
  </Keypad>
)} 
```

这将检查`state.processing`是否为真，如果是，则显示处理过程。否则，显示键盘。

## 计算质因数

将一个数分解成质因数的最简单的方法是遍历从 2 到`num/2`的所有数(因为任何大于`num/2`的数都不是因数)，并检查它是否能被整除。然而，4 号呢？它不是质数。所以我们还应该检查每个因子是否是质数。

这里描述了一个聪明的算法[T2。从 2 开始——继续用 2 除`num`,对于每种情况，在列表中添加 2，直到它不再被除。](https://www.geeksforgeeks.org/print-all-prime-factors-of-a-given-number/)

```
while (num%2 == 0) {
  addFactor(2);
  num = num/2;
} 
```

现在`num`将是奇数。从 3 开始相同的过程，除了只进行到`num`的平方根，递增 2(仅奇数):

```
for (int i = 3; i <= Math.floor(Math.sqrt(num)); i += 2) {
  while (num%i == 0) {
    add_factor(i);
    num = num/i;
  }
} 
```

注意，在处理平方根这样的事情时，您必须在 JavaScript 中使用 floor，因为 JavaScript 中的所有数字都是浮点数(实际上是双精度数)。

同样地，继续把除数作为因子相加，直到没有余数。

最后一步是检查你剩下的数字是否大于 1。如果是，那一定是质数！

```
if (num > 1) { add_factor(num); } 
```

你很快就会把这一切联系起来。

现在您已经有了显示结果所需的代码。

### 在 React Native 中显示列表

要显示列表，您需要使用 React 本地列表视图，在我们的 cast 中是一个`FlatList`。这需要一个名为`data`的值(对象)列表和一个名为`renderItem`的呈现每一项的方法。

```
<FlatList
  data={[{key: 'a'}, {key: 'b'}]}
  renderItem={({item}) => <Text>{item.key}</Text>}
/> 
```

将`components/Processing.js`更改如下:

```
import React from 'react';
import { FlatList } from 'react-native';
import styled from 'styled-components/native';

const ProcessingBox = styled.View` flex: 6;
  background-color: rgb(29, 31, 33);
  padding-top: 10px;
  padding-bottom: 10px; `;

const Header = styled.Text` font-size: 30;
  text-align: center;
  text-align-vertical: center;
  font-family: Orbitron-Bold;
  color: red;
  padding-top: 10px; `;

const Item = styled.Text` font-size: 20;
  text-align: center;
  text-align-vertical: center;
  font-family: Audiowide-Regular;
  color: firebrick; `;

export default () => (
  <ProcessingBox>
    <Header>Processing</Header>
    <FlatList
      data={[{ key: '2' }, { key: '2' }, { key: '3' }, { key: '5' }]}
      renderItem={({ item }) => <Item>{item.key}</Item>}
    />
  </ProcessingBox>
); 
```

所以你有一个`ProcessingBox`、一个`Header`和一个`Item`，它们在输出中被组合在一起。当您点击 **Go** 时，您应该会看到以下内容:

![Processing](img/b219b0278fbe46b68a84b0ee2a715b43.png)

## 整合 React 本地应用

要使这与应用程序一起工作，您需要从外部控制渲染，因此将渲染方法更改为以下内容:

```
export default ({ running, factors, press }) => (
  <ProcessingBox>
    {running ? (
      <Header>Processing</Header>
    ) : (
      <Header>Finished</Header>
    )}
    <List
      data={factors}
      renderItem={({ item }) => <Item>{item.key}</Item>}
    />
    {!running && (
      <Button func={() => press('Back')} text="Back" />
    )}
  </ProcessingBox>
); 
```

这里，控件接受三个变量，分别控制显示哪个标题、显示的质因数以及用于切换父状态的函数(按钮被按下时使用的相同函数)。

`FlatList`已经被替换为`List`，它只是一个样式化的组件，用于控制列表呈现的大小。在`Processing.js`中增加以下内容:

```
const List = styled.FlatList` flex: 5; `; 
```

还有一个`Button`是你自己控制的。用`import { Button } from '.';`导入。

现在移动到`App.js`并将`<Processing /> tag in` render()`改为:

```
<Processing running={state.running} factors={state.factors} press={this.press} /> 
```

所以你要传递刚才讨论的三个参数，前两个来自状态。现在更改 state 属性以包含这些新值:

```
state = {
  number: 123456,
  factors: [],
  processing: false,
  running: false
}; 
```

现在在下面的`App`类中，放入计算素数的函数:

```
getPrimes = (N) => {
  const factors = [];
  let num = N;
  while (num % 2 === 0) {
    factors.push({ key: '2' });
    num /= 2;
  }
  let i; for (i = 3; i <= Math.floor(Math.sqrt(num)); i += 2) {
    while (num % i === 0) {
      factors.push({ key: `${i}` });
      num /= i;
    }
  }
  if (num > 1) { factors.push({ key: `${num}` }); }
  return factors;
}; 
```

最后，更改`press`函数以获取这些因素并使用它们设置状态(以及正确响应加工屏幕中的**后退**按钮:

```
press = (value) => {
  let { number } = this.state;
  if (value === 'Clear') {
    number = Math.floor(number / 10);
    this.setState({ number });
  } else if (value !== 'Go' && value !== 'Back' && number < 1000000) {
    if (number === 0) number = value; else number += value;
    this.setState({ number });
  } else if (value === 'Go') {
    this.setState({ processing: true });
    let factors = this.getPrimes(number);
    this.setState({ running: false });
    this.setState({ factors });
  } else if (value === 'Back') {
    this.setState({ processing: false });
  }
}; 
```

现在，当你运行应用程序并按下 **Go** 时，你应该会看到一个质因数列表，以及一个 **Back** 按钮，它会带你返回。

![Finished](img/659f6db6677d5d025b65de9483ba0663.png)

编辑数字并再次点击 **Go** 会得到一个新的因素列表。恭喜你，你已经完成了应用程序！

## 设置您的 Android 应用程序名称和图标

为了让部署在手机上的东西看起来更好，你需要设置名称和图标。您可以在`android/app/src/main/res/values/strings.xml`中设置名称:

```
<resources>
    <string name="app_name">Prime Components</string>
</resources> 
```

对于图标，最好在 Android Studio 中打开你的项目(使用`android`目录)并在`assets`中创建一个‘drawable’目录。然后右键单击并说出**新建- >图像资产**。这将允许你导入你的大图标并覆盖用于启动器图标的`ic_launcher`，生成你在游戏商店需要的所有版本。用`react-native run-android`重新部署，你应该会在你的应用列表上看到一个正确的名称和图标。

如果你下次在 Android 上运行`react-native run-android`时遇到捆绑问题，有一个[栈溢出修复](https://stackoverflow.com/a/44476757)可以解决。

## 使用 AppAuth 添加身份验证

身份验证对于计算器来说可能不是至关重要的，但在应用程序的功能保持简单的情况下添加身份验证通常是明智的。添加要素时，您可能希望为用户提供保存计算历史或与朋友共享其工作的选项。这些功能需要 Okta 简化的那种安全用户管理。

首先通过复制`Button.js`添加一个新的`Login.js`组件，并将字体大小改为 20，边框半径和边距改为 0，背景颜色改为更浅的颜色，如`rgb(49, 51, 53)`。

```
import React from 'react';
import styled from 'styled-components/native';

const ButtonBox = styled.TouchableHighlight.attrs({
  underlayColor: '#193441',
})` flex: 1;
  align-items: center;
  justify-content: center;
  background-color: rgb(49, 51, 53);
  border-radius: 0;
  margin: 0; `;

const ButtonText = styled.Text` font-size: 20;
  font-family: Orbitron-Bold;
  color: orange; `;

export default Login = ({func,text}) => (
  <ButtonBox onPress={() => func(text)}>
    <ButtonText text={text}>{text}</ButtonText>
  </ButtonBox>
); 
```

将它添加到组件目录中的`index.js`，在`App.js`的顶部导入，并将`loggedin: false`添加到`App`类状态。最后，在 render 中的`<Input>`控件下面放一个开关来决定在登录按钮上打印什么:

```
 {state.loggedin ? (
      <Login text="Logout" />
    ) : (
      <Login text="Login" />
    )} 
```

当您重新运行项目时，您应该会看到一个新的登录按钮。

注意:如果您没有在`react-native run-android`部署中看到代码的任何变化，这可能是因为您删除了`AndroidManifest.xml`中的互联网权限，该权限将本地使用连接应用程序和您的开发机器。只需将其替换在`application`指令之上:

```
<uses-permission android:name="android.permission.INTERNET" /> 
```

接下来，你需要[注册一个免费的 Okta 开发者账号](https://developer.okta.com/signup/)。然后登录并导航至**应用>添加应用**。选择**原生**，点击**下一步**。选择一个名称并点击**完成**。请注意您的**登录重定向 URI** 和**客户端 ID** ，因为您必须将它们添加到您的应用程序中。

现在安装 [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth) 并链接。

```
react-native install react-native-app-auth@4.0.0
react-native link react-native-app-auth 
```

现在使用您的**重定向 URL** 的基础，例如`com.oktapreview.dev-628819`，将以下内容添加到`android/app/build.gradle`中的`android`配置的`defaultConfig`部分。

```
defaultConfig {
    applicationId "com.prime_components"
    ...
    manifestPlaceholders = [
        appAuthRedirectScheme: "{redirectBase}"
    ]
} 
```

在你的顶部`App.js`从 React 原生包导入**提醒**和**授权**和**从 App Auth 包撤销**:

```
import { Alert } from 'react-native';
import { authorize, revoke } from 'react-native-app-auth'; 
```

在定义您的`App`类之前，添加一个包含 Okta 应用程序详细信息的`config`变量:

```
const config = {
  issuer: 'https://{yourOktaDomain}/oauth2/default',
  clientId: '{clientId}',
  redirectUrl: '{redirectUrl}',
  additionalParameters: {},
  scopes: ['openid', 'profile', 'email', 'offline_access']
}; 
```

登录后，您可以在 Okta 仪表板上找到`{yourOktaDomain}`的值。

现在将`accessToken: ''`添加到您的状态初始化中，并将以下两个函数添加到您的`App`类中:

```
 state = {
    ...
    accessToken: '',
  };

...

authorize = async () => {
  try {
    const authState = await authorize(config);

    this.setState({
      loggedin: true,
      accessToken: authState.accessToken
    });
  } catch (error) {
    Alert.alert('Failed to log in', error.message);
  }
};

revoke = async () => {
  try {
    const { state } = this;
    await revoke(config, {
      tokenToRevoke: state.accessToken,
      sendClientId: true
    });
    this.setState({
      accessToken: '',
      loggedin: false
    });
  } catch (error) {
    Alert.alert('Failed to revoke token', error.message);
  }
}; 
```

最后，将`func={this.authorize}`作为属性添加到登录按钮上，将`func={this.revoke}`添加到注销按钮上。

```
{state.loggedin ? (
  <Login text="Logout" func={this.revoke}/>
) : (
  <Login text="Login" func={this.authorize}/>
)} 
```

现在，当您重新运行并点击`Login`时，您应该会被重定向到 Okta 登录屏幕。使用 Okta 应用程序的一个用户的登录信息应该会把你重定向回你的应用程序，现在显示一个注销按钮。当您点击注销，然后再次登录时，您会注意到系统不会提示您再次登录。要解决这个问题，您可以在`additionalParameters`中添加一个登录提示。

```
const config = {
  ...
  additionalParameters: {prompt: 'login'},
  scopes: ['openid', 'profile', 'email', 'offline_access']
}; 
```

这确保了每次都提示用户输入密码。

## 在 Play Store 上启动您的 Android 应用程序

最后一步是在 Play Store 上发布你所做的事情。标准文档在这个上有[很好的建议，以及一个](https://developer.android.com/studio/publish/)[预发布清单](https://developer.android.com/distribute/best-practices/launch/launch-checklist)。除了阅读开发者政策(这是你应该做的)，你需要[注册一个开发者账户](https://support.google.com/googleplay/android-developer/answer/6112435)并支付 25 美元的注册费。然后你需要创建一个图标。

### 为你的 Android 应用创建一个图标

制作一些独特的、引人注目的、与你的应用相关的东西并不容易。对于本教程，请查看[图标](https://play.google.com/store/apps/details?id=xeus.iconic)(Play Store 上的一个移动应用程序)，它链接到免费的图标库，并让您编辑它们。使用它，您可以创建如下图形:

![App icon](img/4ad132983b35c167119187fc9d9e3c35.png)

### 在 Play Store 控制台中创建 Android 应用程序

打开游戏控制台，点击**创建应用**。选择默认语言，输入名称，点击**创建**。现在，您会看到需要在商店列表中填写很多内容，至少包括:

*   简短的描述
*   冗长的描述
*   两张截图
*   一个[特征图形](https://developer.android.com/distribute/best-practices/launch/feature-graphic)
*   高分辨率图标(512×512)
*   分类(应用类型和类别)
*   电子邮件地址

您还需要指定您是否提供隐私政策。

对于特写图片，你需要的尺寸正好是 1024×500。你可以从 [Pixabay](https://pixabay.com) 获得免版税的图片(例如[这张](https://pixabay.com/en/banner-number-digit-maths-1183443/))，不过你必须用 [Gimp](https://www.gimp.org/) 之类的东西来调整它们的大小(并确保在 Pixabay 上图片可以免费用于商业用途！).

完成后，点击**保存草稿**，你会在*商店列表*旁边的侧面板上看到一个绿色的大勾。

![Store listing](img/76ff5293dd36d847b3638b7f42fe3b8b.png)

这表明您已经完成了列表的详细信息，还需要填写内容分级表以及定价和分发。尽管在这之前你需要发布一个应用程序。

### 应用发布

点击工具条中的**应用发布**。你应该会看到不同测试阶段的**赛道**列表。点击生产轨道中的`MANAGE`。然后点击`CREATE RELEASE`。

你会看到一条关于 Google Play 使用 **App signing 的消息。这是一项新功能，将密钥的管理交给了谷歌。你希望这样做的原因很简单:如果你管理自己的密钥，而你丢失了它们，你将无法再为你的应用发布更新。事实上，你必须用新的名字创建一个新的！但是，请注意，这是一个选项。在这里你会用到它。为此，您将需要[生成一个上传密钥](https://developer.android.com/studio/publish/app-signing#generate-key)。**

*   浏览 React 原生项目的`android`文件夹，在 Android Studio 中打开您的应用
*   转到**构建>生成签名包/ APK**
*   选择 **APK** ，点击**下一步**
*   在**键下存储路径**点击**新建**
*   选择类似`/home/karl/keystores/android.jks`的路径
*   选择密钥库和密钥的密码
*   输入证书信息(注意:这不会显示在应用程序中，只是证书)

![Keystore](img/3e36095e50f4dd96421003ad08e58d46.png)

点击`OK`，点击**下一个**。选择 V1 和 [V2 签名版本](https://developer.android.com/about/versions/nougat/android-7.0#apk_signature_v2)并点击**完成**。构建应该开始，之后你应该在你的项目中的`android/app/release`中看到`app-x86-release.apk`和`app-armeabi-v7a-release.apk`(每个设备架构一个)。

如果你现在尝试上传这些应用程序，你会收到一条错误消息，称“你的应用程序正在使用需要隐私策略的权限”。这是因为 React Native [在幕后添加了 app 权限](https://facebook.github.io/react-native/docs/removing-default-permissions)。要强制它们不被包含，你必须首先将`xmlns:tools="http://schemas.android.com/tools"`添加到`android/app/src/main/AndroidManifest.xml`的`manifest`元素中，然后添加以下内容(更多内容见前面的链接):

```
<uses-permission tools:node="remove" android:name="android.permission.READ_PHONE_STATE" />
<uses-permission tools:node="remove" android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission tools:node="remove" android:name="android.permission.READ_EXTERNAL_STORAGE" /> 
```

你也可以删除提醒窗口和互联网`uses-permission`，因为我们不使用它们。重新构建并重新上传。放入一些发行说明，点击`SAVE`。

### 内容分级

现在发布后，您可以输入评级信息。转到侧边栏中的**内容分级**，阅读关于 IARC 的消息并点击`CONTINUE`。

对于类型，单击“实用程序`at the bottom. Then a questionnaire will show. You can click **No** for all the fields. Click`保存调查问卷`. After this, you can click`计算评级`. You will see a list of how your app will be rated. Scroll to the bottom and click`应用评级”。

这应该会在**内容分级**旁边打勾。剩下的就只有定价和发行了。

### 定价和分销

这一部分也应该是直截了当的，因为你不为你的应用程序收费。只需选择国家/地区栏顶部的**可用**按钮，即可选择所有 143 个可用国家/地区。然后为“面向儿童和包含广告”选择“否”。然后选中内容指南和美国出口法律复选框，并单击`SAVE DRAFT`。

在游戏控制台的顶部，应该会显示**准备发布**。

### 发布您的 Android 应用程序

进入 **App 发布**，点击`EDIT RELEASE`。滚动到底部，点击`REVIEW`。点击`START ROLL-OUT TO PRODUCTION`。点击**确认**。你的应用程序现在应该在顶部显示**待发布**。你现在需要等待几个小时，让谷歌在发布之前检查你的应用程序。

**注意:**在发布到产品之前，也就是发布到每个人之前，先使用测试版来确保一切正常，这可能是值得的！

## 了解有关 Android、React Native 和安全用户管理的更多信息

恭喜你。您刚刚使用 React Native 选择、设计和开发了一个 Android 应用程序，并将其发布到 Play Store。我希望这篇教程对你有所启发。去做点有用的东西发表吧！

如果您有兴趣了解更多关于应用程序设计、使用 Okta 进行本地反应或安全用户管理的信息，请查看以下资源:

*   [创建一个没有 IDE 的基本 Android 应用](https://developer.okta.com/blog/2018/08/10/basic-android-without-an-ide)
*   [使用 TypeScript 和 OAuth 2.0 构建并测试 React 原生应用](https://developer.okta.com/blog/2018/11/29/build-test-react-native-typescript-oauth2)
*   [使用 OAuth 2.0 构建 React 原生应用和认证](https://developer.okta.com/blog/2018/03/16/build-react-native-authentication-oauth-2)

喜欢你今天学到的吗？在 Twitter 上关注我们[，在脸书](https://twitter.com/oktadev)上关注我们[，在 LinkedIn](https://www.facebook.com/oktadevelopers/) 上关注我们[，在 YouTube】上订阅我们的频道](https://www.linkedin.com/company/oktadev/)[。](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)

## 分享这篇文章