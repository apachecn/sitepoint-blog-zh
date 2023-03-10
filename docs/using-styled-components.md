# 如何使用样式化组件重新设计 Unsplash

> 原文：<https://www.sitepoint.com/using-styled-components/>

编写面向未来的 CSS 很难。当您必须编写和维护数千行 CSS 时，会出现冲突的类名、特殊性问题等等。为了解决上述问题，[样式组件](https://www.styled-components.com)应运而生。

Styled Components 使用 JS 编写 CSS 变得容易，并确保没有冲突的类名或特殊性问题，还有许多其他好处。这使得编写 CSS 成为一种乐趣。

在本教程中，我们将探索 JS 中的 CSS 是什么，`styled-components`的优缺点，最后，我们将使用样式化的组件重新设计 Unsplash。完成本教程后，您应该能够快速上手并运行样式化组件。

注意:Styled Components 是专门为 React 设计的，所以你必须使用 React 来使用 Styled Components。

## 先决条件

对于本教程，您需要了解 [React](https://reactjs.org) 的基本知识。

在整个教程中，我们将使用`yarn`。如果你还没有安装`yarn`，那么从这里的[安装](https://yarnpkg.com/en/docs/install)。

为了确保我们在同一页上，这些是本教程中使用的版本:

*   节点 12.6.0
*   npx 6.4.1
*   纱线 1.17.3

## CSS 的演变

在 CSS-in-JS 被创建之前，设计 web 应用程序最常见的方式是在一个单独的文件中编写 CSS，并从 HTML 中链接它。

但这在大型团队中引起了麻烦。每个人都有自己写 CSS 的方式。这导致了特异性问题，并导致每个人都使用`!important`。

然后萨斯来了。Sass 是 CSS 的扩展，它允许我们使用变量、嵌套规则、内联导入等等。它还有助于保持事物的有序性，并允许我们更快地创建样式表。

尽管 Sass 可能被认为是对 CSS 的一种改进，但如果没有适当的系统，它可能会弊大于利。

后来，BEM 进来了。BEM 是一种方法论，它通过让我们编写唯一的类名来减少特殊性问题。BEM 确实解决了特殊性问题，但是它使 HTML 更加冗长。类名可能会变得不必要的长，而且当您有一个巨大的 web 应用程序时，很难想出唯一的类名。

之后 CSS 模块就诞生了。CSS 模块通过工具解决了 Sass 和 BEM 都无法解决的问题——唯一类名的问题，而不是依赖于开发人员给定的名称，这反过来又解决了特殊性问题。CSS 模块在 React 生态系统中获得了巨大的人气，为像 [glamor](https://github.com/threepointone/glamor) 这样的项目铺平了道路。

所有这些新解决方案的唯一问题是开发人员不得不学习新的语法。如果我们可以像在一个`.css`文件中那样用 JS 编写 CSS，那会怎么样？于是`styled-components`就这样产生了。

Styled Components 使用[模板文字](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)，这是 ES6 的一个特性。模板文字是允许嵌入表达式的字符串文字。它们支持多行字符串和字符串插值功能。

样式化组件的主要卖点是它允许我们用 JS 编写精确的 CSS。

样式化组件有很多好处。下面列出了样式化组件的一些优点和缺点。

### 赞成的意见

使用样式化组件有很多好处。

1.  **将关键 CSS 注入 DOM**

    样式组件只在页面上注入关键的 CSS。这意味着用户只下载特定页面所需的 CSS，而不下载其他内容。这样可以更快地加载网页。

2.  **每页更小的 CSS 包**

    因为它只注入页面组件中使用的样式，所以包的大小要小得多。你只加载你需要的 CSS，而不是过多的样式表、规格化器、响应等。

3.  **自动供应商前缀**

    样式组件允许你写你的 CSS，它会根据最新的标准自动添加厂商前缀。

4.  **移除未使用的 CSS**

    使用样式化组件，可以更容易地移除未使用的 CSS 或死代码，因为样式与组件位于同一位置。这也对减小束尺寸有影响。

5.  **主题化很容易**

    样式化组件使得 React 应用程序的主题化变得非常容易。你甚至可以在你的应用程序中有多个主题，并且很容易维护它们。

6.  **减少 HTTP 请求的数量**

    由于没有用于重置、规范化和响应的 CSS 文件，HTTP 请求的数量大大减少。

7.  **唯一类名**

    每次执行构建步骤时，样式化组件都会生成唯一的类名。这可以避免命名冲突或特殊性问题。不再有全局冲突，也不再被迫用`!important`标签来解决它们。

8.  **维护简单**

    样式组件允许您将样式与组件放在一起。这允许无痛维护。不像在一个大的 CSS 文件中，你确切地知道哪种风格影响了你的组件。

### 骗局

当然，没有什么是完美的。让我们看看与样式化组件相关的一些缺点。

1.  **无法缓存样式表**

    一般来说，当用户下次访问网站时，web 浏览器会缓存`.css`文件，因此它不必再次下载相同的`.css`文件。但是使用`styled-components`，使用`<style>`标签将样式加载到 DOM 中。因此，它们不能被缓存，每次用户访问你的网站时都需要请求样式。

2.  **反应特异**

    设计组件时考虑到了 React。因此，它的反应是特定的。如果你使用任何其他框架，那么你就不能使用样式化的组件。

    然而，有一个与`styled-components`非常相似的选择，叫做[情感](https://emotion.sh)，它是框架不可知的。

## 变得实际

现在我们知道了样式化组件的优缺点，让我们开始使用它。

继续使用`create-react-app`构建一个新的 React 项目。为了支撑它，我们将使用`npx`，它允许您临时下载并运行一个包，而不会污染全局名称空间。

在终端中键入以下内容:

```
$ npx create-react-app unsplash-styled-components 
```

然后进入`unsplash-styled-components`目录，安装本教程所需的以下软件包，即`styled-components`和`react-medium-image-zoom`:

```
$ cd unsplash-styled-components
$ yarn add styled-components react-medium-image-zoom 
```

`styled-components`包允许我们用 JS 编写实际的 CSS，而`react-medium-image-zoom`包允许我们放大图像[中等风格](https://medium.com)。

现在，一旦安装完成，从`src/`目录中删除不需要的文件，如`App.css`、`App.test.js`、`index.css`和`logo.svg`。

继续从`index.js`中移除`import './index.css';`，因为不再需要它。

用以下内容替换`App.js`的内容:

```
import React from 'react'

const App = () => <h1>Hello React</h1>

export default App 
```

通过在终端中键入`yarn start`来尝试运行开发服务器。

您应该会看到屏幕上打印的`Hello React`是这样的:

![Hello React](img/0d9f5e4ae4d2deaeb4381f0102b21518.png)

样式化组件使用标记的[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)来样式化你的组件。

假设您想通过使用样式化组件将`h1`样式化为粉色背景上的白色文本。你可以这样做:

```
import React from 'react'
import styled from 'styled-components'

const Header = styled.h1` background-color: pink;
    color: white; `

const App = () => <Header>Hello React</Header>

export default App 
```

它现在应该是这样的:

![Hello React using Styled Components](img/8fddc50343911c1d6d6c18931a04d83a.png)

我们刚刚创建了我们的第一个样式组件`Header`。`Header`组件被分配给`styled.h1`。来自`styled-components`的默认导出`styled`具有基本的 HTML 原语，如`div`、`span`、`h1`、`h2`等。

在`styled.h1`中，我们编写实际的 CSS。注意我们是如何编写 CSS 而不是制作样式对象的。

此外，`Header`组件在浏览器中被替换为一个`h1`标签和一个惟一的类名。一个`style`标签以相同的惟一类名被插入到 DOM 中。这是样式化组件实际工作方式的基础。

我们的样式和组件在同一个文件中。这意味着我们的组件与样式位于同一位置。因此，移除与特定组件相关联的样式更容易，从而消除了死代码。

传统上，在 CSS 中，很难进行推理，因为我们必须找到特定元素的`classname`或`id`或特定的`selector`，然后一个接一个地删除它们。在小项目中这很容易，但是随着项目复杂性的增加，这变得很难。有了样式组件，无论项目有多复杂，添加、编辑和移除样式都变得更加容易，因为它们与组件放在一起。

## 样式化组件中的主题

让我们将主题添加到项目中。

在`src/`目录下创建一个`utils/`文件夹。然后在`utils/`文件夹中创建一个`themes.js`文件。在`themes.js`中粘贴以下内容:

```
export const themes = {
  common: {
    fontFamily:
      "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif",
  },
  light: {
    type: 'light',
    bgColor: 'white',
    color: '#3E4C59',
    searchBgColor: '#E4E7EB',
    boxShadow: '0.8rem 0.8rem 1.5rem gray',
    categoryColor: '#999',
    categoryHoverColor: '#333',
  },
  dark: {
    type: 'dark',
    bgColor: '#1F2933',
    color: '#E4E7EB',
    searchBgColor: '#E4E7EB',
    boxShadow: '0.4rem 0.4rem 1.5rem #111111',
    categoryColor: '#CBD2D9',
    categoryHoverColor: '#9AA5B1',
  },
} 
```

`themes.js`文件包含一个名为`themes`的对象，它有三个属性:`common`、`light`和`dark`。

属性`common`包含主题的通用样式，`light`属性包含浅色主题的样式，`dark`属性包含深色主题的样式。

现在将`App.js`更改如下:

```
import React, { useState } from 'react'
import styled, { ThemeProvider } from 'styled-components'
import { themes } from './utils/themes'

const lightTheme = () => ({
  ...themes['common'],
  ...themes['light'],
})

const darkTheme = () => ({
  ...themes['common'],
  ...themes['dark'],
})

const Header = styled.h1` background-color: pink;
    color: white; `

const App = () => {
  const [theme, setTheme] = useState(lightTheme())
  const setDarkTheme = () => setTheme(darkTheme())
  const setLightTheme = () => setTheme(lightTheme())
    return (
        <ThemeProvider theme={theme}>
            <>
                <Header>Hello React</Header>
                <button onClick={setLightTheme}>Make it light</button>
                <button onClick={setDarkTheme}>Make it dark</button>
            </>
        </ThemeProvider>
    )
}

export default App 
```

首先，我们从`react`引进了`useState`钩子，从`styled-components`引进了`ThemeProvider`，从`./utils/themes`引进了`themes`。

然后我们创建了两个箭头函数，`lightTheme`和`darkTheme`。

`lightTheme`函数从导入的`themes`变量返回一个结合了`common`和`light`属性的对象。

所以基本上，评估后的`lightTheme`函数是这样的:

```
const lightTheme = () => ({
    fontFamily:
        "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif",
    type: 'light',
    bgColor: 'white',
    color: '#3E4C59',
    searchBgColor: '#E4E7EB',
    boxShadow: '0.8rem 0.8rem 1.5rem gray',
    categoryColor: '#999',
    categoryHoverColor: '#333',
}) 
```

`darkTheme`函数从导入的`themes`变量返回一个结合了`common`和`dark`属性的对象。

因此，评估后的`darkTheme`函数如下所示:

```
const darkTheme = () => ({
    fontFamily:
        "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif",
    type: 'dark',
    bgColor: '#1F2933',
    color: '#E4E7EB',
    searchBgColor: '#E4E7EB',
    boxShadow: '0.4rem 0.4rem 1.5rem #111111',
    categoryColor: '#CBD2D9',
    categoryHoverColor: '#9AA5B1',
}) 
```

稍后，在`App`组件中，我们使用了`useState` React 钩子:

```
const App = () => {
  const [theme, setTheme] = useState(lightTheme())
  const setDarkTheme = () => setTheme(darkTheme())
  const setLightTheme = () => setTheme(lightTheme())
    return (
        <ThemeProvider theme={theme}>
            <>
                <Header>Hello React</Header>
                <button onClick={setLightTheme}>Make it light</button>
                <button onClick={setDarkTheme}>Make it dark</button>
            </>
        </ThemeProvider>
    )
} 
```

`useState`采用默认状态，我们将其设置为`lightTheme()`。如果你把它设置为`darkTheme()`，那么默认情况下会显示黑暗主题。`useState`返回两个变量，我们命名为`theme`和`setTheme`。你可以给它们起任何名字。

`theme`包含当前状态——即`lightTheme()`——`setTheme`是用于设置新状态的功能。

然后我们有了`setDarktheme`函数，它通过调用`setTheme`并将函数`darkTheme()`返回的对象传递给它来设置一个黑暗元素。`setLightTheme`功能的工作方式类似。

最后，我们有两个额外的按钮来改变主题——一个改变为`light`另一个为`dark`。

但是要做到这一点，我们需要使用主题来改变我们的`Header`组件。因此，将其更改为以下内容:

```
const Header = styled.h1` background-color: ${props => props.theme.bgColor};
    color: ${props => props.theme.color}; ` 
```

现在灯光主题看起来像这样:

![Selected Light Theme](img/fb353d1fa17ccee5d95df42dee180b60.png)

如果您尝试点击`Dark Theme`按钮，它将显示如下黑暗主题:

![Selected Dark Theme](img/510f54b01e8265294b6dd34bab0ce39a.png)

样式组件有一个特性，可以让你使用道具动态改变你的组件样式。`background-color`变量使用道具`theme.bgColor`，而`color`使用道具`theme.color`。

这里所说的`theme`变量来自于`ThemeProvider`。提供给`ThemeProvider`的道具应该命名为`theme`。不然不行。

现在试着点击按钮来看看主题的变化。

主题改变了，但是点击哪个按钮会让人困惑。让我们添加一个条件，仅在显示深色主题时显示浅色主题按钮，反之亦然。

按如下方式更改`App.js`中的按钮:

```
{theme.type === 'dark' && <button onClick={setLightTheme}>Make it light</button>}
{theme.type === 'light' && <button onClick={setDarkTheme}>Make it dark</button>} 
```

该条件确保根据所选主题只显示相关按钮。

仅在`dark`主题上显示`light`按钮:

![Show Light Button on Dark Theme](img/84db834f8de74fdea78d0932217747ff.png)

仅在`light`主题上显示`dark`按钮:

![Show Dark Button on Light Theme](img/15972dbec4e7c32c5c47186fd0ae8615.png)

继续下载[资产/](https://github.com/deadcoder0904/unsplash-styled-components/tree/master/src/assets) 文件夹并粘贴到`src/`目录中。

这将确保我们拥有这个项目中需要的所有`images`和`icons`。

现在让我们把按钮改成图标。我们将把亮按钮做成太阳图标，暗按钮做成月亮图标。

将图标导入`App.js`:

```
import { ReactComponent as MoonSVG } from './assets/logos/moon.svg'
import { ReactComponent as SunSVG } from './assets/logos/sun.svg' 
```

然后通过扩展 SVG 来设计这些图标的样式。下面将样式添加到`svg`标签中:

```
const SunIcon = styled(SunSVG)` width: 2rem;
  height: 2rem;
  margin-left: auto;
  cursor: pointer; `

const MoonIcon = styled(MoonSVG)` width: 2rem;
  height: 2rem;
  margin-left: auto;
  cursor: pointer; ` 
```

注意我们是如何在`SunIcon`和`MoonIcon`中复制上面的代码的。样式组件支持重用样式。

## 样式化组件中的可重用 CSS

Styled Components 允许我们使用一个名为`css`的特殊辅助函数来重用样式。继续从`styled-components`导入，如下所示:

```
import styled, { css, ThemeProvider } from 'styled-components' 
```

现在，您可以使用`css`道具重用样式，如下所示:

```
const ThemeIcon = css` width: 2rem;
  height: 2rem;
  margin-left: auto;
  cursor: pointer; `

const MoonIcon = styled(MoonSVG)` ${ThemeIcon} `

const SunIcon = styled(SunSVG)` ${ThemeIcon} ` 
```

最后，将按钮更改为以下内容:

```
{theme.type === 'light' && <MoonIcon onClick={setDarkTheme} />}
{theme.type === 'dark' && <SunIcon onClick={setLightTheme} />} 
```

`App.js`文件现在应该是这样的:

```
import React, { useState } from 'react'
import styled, { css, ThemeProvider } from 'styled-components'
import { ReactComponent as MoonSVG } from './assets/logos/moon.svg'
import { ReactComponent as SunSVG } from './assets/logos/sun.svg'
import { themes } from './utils/themes'

const lightTheme = () => ({
  ...themes['common'],
  ...themes['light'],
})

const darkTheme = () => ({
  ...themes['common'],
  ...themes['dark'],
})

const ThemeIcon = css` width: 2rem;
  height: 2rem;
  margin-left: auto;
  cursor: pointer; `

const MoonIcon = styled(MoonSVG)` ${ThemeIcon} `

const SunIcon = styled(SunSVG)` ${ThemeIcon} `

const App = () => {
    const [theme, setTheme] = useState(lightTheme())
  const setDarkTheme = () => setTheme(darkTheme())
  const setLightTheme = () => setTheme(lightTheme())
    return (
        <ThemeProvider theme={theme}>
            <>
                <Header>Hello React</Header>
                {theme.type === 'light' && <MoonIcon onClick={setDarkTheme} />}
                {theme.type === 'dark' && <SunIcon onClick={setLightTheme} />}
            </>
        </ThemeProvider>
    )
}

export default App 
```

该站点现在应该如下所示。它只在`light`主题上显示`moon`图标:

![Show Moon Icon on Light Theme](img/84de3495baf00f02123f23f4b6202e21.png)

并且它只在`dark`主题上显示`sun`图标:

![Show Sun Icon on Dark Theme](img/50fcba52aae5fb9ddd5f722d0c7685d2.png)

## 样式化组件中的全局样式

现在继续在`src/`目录下创建一个`components/`文件夹。在`components/`文件夹中创建一个名为`Global.js`的文件。在其中粘贴以下内容:

```
import { createGlobalStyle } from 'styled-components'

export const GlobalStyle = createGlobalStyle` *, *::before, *::after {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }

    html, body {
        height: 100vh;
        max-width: 100%;
        font-size: 62.5%;
        background-color: ${props => props.theme.bgColor};
        color: ${props => props.theme.color};
        font-family: ${props => props.theme.fontFamily};
    } ` 
```

我们已经从`styled-components`导入了`createGlobalStyle`。这让我们可以创建全局样式。然后我们将`GlobalStyle`变量作为命名导出导出。注意我们如何在`GlobalStyle`中使用主题。为了确保主题化有效，我们需要让`GlobalStyle`成为`ThemeProvider`的子代。不然不行。

继续在`App.js`中导入`Global.js`，并按如下方式使用:

```
import { GlobalStyle } from './components/Global'

.
.
.

const App = () => {
    .
    .
    .
    return (
        <ThemeProvider theme={theme}>
            <>
                <GlobalStyle />
                <Header>Hello React</Header>
                {theme.type === 'light' && <MoonIcon onClick={setDarkTheme} />}
                {theme.type === 'dark' && <SunIcon onClick={setLightTheme} />}
            </>
        </ThemeProvider>
    )
}

.
.
. 
```

全局样式应该应用于我们的 web 应用程序。

应用于`light`主题的全局样式:

![Global Styles on Light Theme](img/0da86bc8e5abe7d9671d033a29e91866.png)

应用于`dark`主题的全局样式:

![Global Styles on Dark Theme](img/6d764a725b86398bf37d754c0f939556.png)

现在我们已经知道了基础知识，让我们为 Unsplash 重新设计创建导航条。首先，从`App.js`中移除`Header`样式的组件。

然后在`App.js`文件中添加以下内容:

```
import { ReactComponent as SearchSVG } from './assets/logos/search.svg'
import { ReactComponent as UnsplashLogoSVG } from './assets/logos/unsplash-logo.svg'
.
.
.

const Nav = styled.nav` display: flex;
  align-items: center;
  background-color: ${props => props.theme.bgColor};
  box-sizing: border-box;
  height: 8.2rem;
  padding-left: 2rem;
  padding-right: 2rem;
  position: fixed;
  width: 100%;
  z-index: 2; `

const UnsplashLogo = styled(UnsplashLogoSVG)` fill: ${props => props.theme.color};
  width: 4rem;
  height: 4rem;
  backface-visibility: hidden;
  box-sizing: border-box;
  overflow: hidden;
  vertical-align: middle; `

const TitleWrapper = styled.div` display: flex;
  flex-direction: column;
  margin-left: 1rem; `

const Title = styled.h1` display: inline-block;
  font-size: 2rem;
  font-weight: 700;
  color: ${props => props.theme.color}; `

const Subtitle = styled.span` font-size: 1.3rem;
  font-weight: 500; `

const SearchWrapper = styled.div` display: flex;
  align-items: center;
  background: ${props => props.theme.searchBgColor};
  border-radius: 3rem;
  border: 0.05rem solid #cbd2d9;
  margin-left: 7rem;
  box-shadow: none;
  :hover {
    border: 0.12rem solid #cbd2d9;
  } `

const SearchIcon = styled(SearchSVG)` fill: #9aa5b1;
  width: 1.6rem;
  height: 1.6rem;
  margin-left: 1.2rem;
  margin-right: 1rem; `

const SearchBar = styled.input` outline: none;
  border: none;
  width: 50rem;
  padding: 1rem 0;
  color: ${props => props.theme.categoryHoverColor};
  background: transparent;
  font-size: 1.4rem;
  font-weight: 300; `

const App = () => {
  ..
  ..
  ..
  return (
    <ThemeProvider theme={theme}>
      <>
        <GlobalStyle />
        <Nav>
          <UnsplashLogo />
          <TitleWrapper>
            <Title>Unsplash</Title>
            <Subtitle>Photos for Everyone</Subtitle>
          </TitleWrapper>
          <SearchWrapper>
            <SearchIcon />
            <SearchBar placeholder="Search free high-resolution photos" />
          </SearchWrapper>
          {theme.type === 'light' && <MoonIcon onClick={setDarkTheme} />}
          {theme.type === 'dark' && <SunIcon onClick={setLightTheme} />}
        </Nav>
      </>
    </ThemeProvider>
  )
}

..
..
.. 
```

注意，我们唯一不同的地方是在`SearchWrapper`组件上添加了一个`:hover`伪类。如果你来自萨斯或 SCSS，那么样式组件是非常相似的。您甚至可以像 Sass 一样在样式化的组件中添加子选择器。

导航现在应该可以正常显示了。

`light`主题上的导航栏:

![Navbar on Light Theme](img/a7981d7929beafe0b64412ca1caac435.png)

`dark`主题上的导航栏:

![Navbar on Dark Theme](img/66cc8ec499ec4503ad59e478ea7cdc7b.png)

让我们创建一个列出类别的组件。在`utils/`文件夹中创建一个文件`categories.js`，并粘贴以下内容:

```
export const categories = [
  'Wallpapers',
  'Textures & Patterns',
  'Nature',
  'Current Events',
  'Architecture',
  'Business & Work',
  'Film',
  'Animals',
  'Travel',
  'Fashion',
  'Food & Drink',
  'Spirituality',
  'Experimental',
  'People',
  'Health',
  'Arts & Culture',
] 
```

另外，在`components/`文件夹中创建一个名为`CategoriesList.js`的组件。

将以下代码粘贴到其中:

```
import React from 'react'
import styled from 'styled-components'
import { categories } from '../utils/categories'

const Categories = styled.div` padding-bottom: 2rem;
  padding-left: 2rem;
  position: fixed;
  margin-top: 8.2rem;
  top: 0;
  bottom: 0;
  overflow-y: scroll;
  width: 23rem;
  background: ${props => props.theme.bgColor};
  z-index: 2; `

const CategoryName = styled.a` background-color: transparent;
  cursor: pointer;
  box-sizing: border-box;
  color: ${props => props.theme.categoryColor};
  display: block;
  font-size: 1.4rem;
  font-weight: 500;
  line-height: 1;
  padding-bottom: 1.7rem;
  padding-top: 2.1rem;
  text-decoration: none;
  white-space: nowrap;
  :hover {
    color: ${props => props.theme.categoryHoverColor};
    outline: none;
  } `

const Category = ({ name }) => <CategoryName href="#">{name}</CategoryName>

export const CategoriesList = () => (
  <Categories>
    {categories.map((category, i) => (
      <Category name={category} key={i} i={i} />
    ))}
  </Categories>
) 
```

现在将它导入到`App.js`中，并将其粘贴到`Nav`组件的正下方:

```
import { CategoriesList } from './components/CategoriesList'
..
..
..

const App = () => {
  ..
  ..
  ..
  return (
    <ThemeProvider theme={theme}>
      <>
        <Nav>
          ..
          ..
          ..
        </Nav>
        <CategoriesList />
        ..
        ..
        ..
      </>
    </ThemeProvider>
  )
}

..
..
.. 
```

现在类别列表将显示在侧栏中。

`light`主题的类别列表:

![Categories List on Light Theme](img/b010bacccb3d243d3c261db35b7eb8fc.png)

`dark`主题的类别列表:

![Categories List on Dark Theme](img/20d046bc85fcbee84a3649510889fefc.png)

让我们创建显示图片的最终组件。在`utils/`文件夹中创建一个名为`pics.js`的文件。将以下内容粘贴到其中:

```
export const pics = [
  require('../assets/pics/asset 0.jpeg'),
  require('../assets/pics/asset 1.jpeg'),
  require('../assets/pics/asset 2.jpeg'),
  require('../assets/pics/asset 3.jpeg'),
  require('../assets/pics/asset 4.jpeg'),
  require('../assets/pics/asset 5.jpeg'),
  require('../assets/pics/asset 6.jpeg'),
  require('../assets/pics/asset 7.jpeg'),
  require('../assets/pics/asset 8.jpeg'),
  require('../assets/pics/asset 9.jpeg'),
  require('../assets/pics/asset 10.jpeg'),
  require('../assets/pics/asset 11.jpeg'),
  require('../assets/pics/asset 12.jpeg'),
  require('../assets/pics/asset 13.jpeg'),
  require('../assets/pics/asset 14.jpeg'),
  require('../assets/pics/asset 15.jpeg'),
  require('../assets/pics/asset 16.jpeg'),
  require('../assets/pics/asset 17.jpeg'),
  require('../assets/pics/asset 18.jpeg'),
  require('../assets/pics/asset 19.jpeg'),
  require('../assets/pics/asset 20.jpeg'),
  require('../assets/pics/asset 21.jpeg'),
  require('../assets/pics/asset 22.jpeg'),
  require('../assets/pics/asset 23.jpeg'),
  require('../assets/pics/asset 24.jpeg'),
  require('../assets/pics/asset 25.jpeg'),
  require('../assets/pics/asset 26.jpeg'),
  require('../assets/pics/asset 27.jpeg'),
  require('../assets/pics/asset 28.jpeg'),
  require('../assets/pics/asset 29.jpeg'),
  require('../assets/pics/asset 30.jpeg'),
  require('../assets/pics/asset 31.jpeg'),
  require('../assets/pics/asset 32.jpeg'),
  require('../assets/pics/asset 33.jpeg'),
  require('../assets/pics/asset 34.jpeg'),
  require('../assets/pics/asset 35.jpeg'),
  require('../assets/pics/asset 36.jpeg'),
  require('../assets/pics/asset 37.jpeg'),
  require('../assets/pics/asset 38.jpeg'),
  require('../assets/pics/asset 39.jpeg'),
  require('../assets/pics/asset 40.jpeg'),
  require('../assets/pics/asset 41.jpeg'),
  require('../assets/pics/asset 42.jpeg'),
  require('../assets/pics/asset 43.jpeg'),
  require('../assets/pics/asset 44.jpeg'),
  require('../assets/pics/asset 45.jpeg'),
  require('../assets/pics/asset 46.jpeg'),
  require('../assets/pics/asset 47.jpeg'),
  require('../assets/pics/asset 48.jpeg'),
  require('../assets/pics/asset 49.jpeg'),
  require('../assets/pics/asset 50.jpeg'),
  require('../assets/pics/asset 51.jpeg'),
  require('../assets/pics/asset 52.jpeg'),
  require('../assets/pics/asset 53.jpeg'),
] 
```

另外，在`components/`文件夹中创建一个`Pic.js`文件，并粘贴以下内容:

```
import React from 'react'
import ImageZoom from 'react-medium-image-zoom'
import styled from 'styled-components'

const ImageWrapper = styled.div` margin: 0.5rem;
  transition: all 200ms ease;
  :hover {
    box-shadow: ${props => props.theme.boxShadow};
    transform: scale(1.005);
  } `

export const Pic = ({ src, i }) => {
  const alt = `Unsplash Redesign #${i}`
  const imageProps = { src, alt }
  return (
    <ImageWrapper>
      <ImageZoom
        image={imageProps}
        zoomImage={imageProps}
        defaultStyles={{
          image: { width: '37rem', height: '48rem' },
        }}
      />
    </ImageWrapper>
  )
} 
```

我们使用的是`react-medium-image-zoom`，它可以让你放大像[中的](https://medium.com/)这样的图像。

现在继续导入`App.js`中的`Pic.js`，并将其粘贴在`CategoriesList`组件的正下方。另外，添加`Grid`和`GridWrapper`样式的组件，如下所示:

```
import { Pic } from './components/Pic'
import { pics } from './utils/pics'

..
..
..

const GridWrapper = styled.div` display: flex;
  justify-content: center; `

const Grid = styled.div` padding-top: 8.2rem;
  padding-left: 23rem;
  display: grid;
  grid-template-columns: repeat(3, 38rem);
  margin-bottom: 1.5rem; `

const App = () => {
  ..
  ..
  ..
  return (
    <ThemeProvider theme={theme}>
      <>
        ..
        ..
        ..
        <CategoriesList />
        <GridWrapper>
          <Grid>
            {pics.map((pic, i) => (
              <Pic src={pic} key={i} i={i} />
            ))}
          </Grid>
        </GridWrapper>
      </>
    </ThemeProvider>
  )
}

..
..
.. 
```

现在应该会出现一个带有图片的网格。

以`light`为主题的 Unsplash 重新设计:

![Unsplash Redesign - Light Theme](img/c500c238103c1cf99d17159c7b136e47.png)

以`dark`为主题的 Unsplash 重新设计:

![Unsplash Redesign - Dark Theme](img/d76410f3c4351c46819d3b7f04bd24e8.png)

我们刚刚使用`styled-components`重新设计了 Unsplash。尝试放大图像，看看它的工作。你可以点击查看现场演示[。](https://unsplash-styled-components.netlify.com/)

## 结论

样式组件使得在 JS 中编写普通的 ol' CSS 变得容易。它允许我们将样式与组件放在一起，这样我们就可以轻松地添加、编辑或删除样式。

我们学习了如何添加全局样式，使用`css`道具重用样式，以及如何应用主题。我们用`styled-components`仅仅触及了表面；它能提供更多的东西。你可以在[官网](https://www.styled-components.com)了解更多。

我们最终使用样式组件重新设计了 Unsplash。完整的代码可以在 [GitHub](https://github.com/deadcoder0904/unsplash-styled-components) 上获得，演示可以在[这里](https://unsplash-styled-components.netlify.com/)获得。

## 分享这篇文章