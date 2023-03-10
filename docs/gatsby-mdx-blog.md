# 如何用 Gatsby 和 MDX 构建开发者博客

> 原文：<https://www.sitepoint.com/gatsby-mdx-blog/>

你可以很容易地将你的想法发布到 Dev.to、Hashnode 或 Medium 等网站上，但最理想的是完全控制你自己的内容。建立自己的网站和控制自己的内容的工具越来越多。在这篇内容丰富的教程中，我将讲述如何使用[盖茨比](https://www.sitepoint.com/gatsby-guide/)让你的内容大放异彩，以及在这样一个生态系统中你可以获得的额外功能。

我本来用 Jekyll 发布博客，后来换成了 Gatsby，用的是 [Lumen](https://github.com/alxshelepenok/gatsby-starter-lumen) 模板。我从 0 版本开始就在用盖茨比，2017 年 5 月左右。

我将从一个`Hello, World!` Gatsby 项目到一个有代码语法高亮和黑暗模式的主题切换的编码博客。

有一个丰富的插件、启动器和主题生态系统可供 Gatsby 快速启动和运行，但我想采用一种[渐进披露](https://www.jason.af/progressive-disclosure-of-complexity)的方法来展示 Gatsby，重点关注 Gatsby 项目如何工作的基础。

## 为什么是盖茨比？

Gatsby 是一个[静态站点生成器](https://www.sitepoint.com/static-site-generators/)，所以当页面被请求时，不会动态生成页面。Gatsby 站点的构建输出可以托管在 CDN 上，使其全球可用并具有超级可伸缩性。

Gatsby 可以使用 Markdown 文件在站点项目中创建页面。Gatsby 会将 Markdown 文件读入 Gatsby 文件系统，并将 Markdown 转换为 HTML，然后在构建站点时创建静态页面。

最终结果是一个超级快速的网站，在请求页面时几乎没有延迟。

### 降价和 MDX

自 2016 年以来，我一直在 Markdown 中[记录我的发展历程。Markdown 提供了一种在可以转换为 HTML 的纯文本文件中实现简单编辑的方法。](https://scottspence.com/2016/10/hello-world/24/)

MDX(或 JSX 减价)是一个让你在减价文件中写 JSX 的工具，有点像这样:

```
import { RainbowText } from './components/rainbow';
## A Markdown Heading
<RainbowText>Wheeeeeeee</RainbowText> 
```

Gatsby 是迄今为止我用过的与 Markdown 和 MDX 一起工作的最好的框架，因为在你的帖子上使用 frontmatter 不需要特别的注释。

## 我需要什么？

如果你要继续下去，你需要几样东西:

*   一个基本的 web 开发设置:[节点](https://nodejs.org/en/download/)，终端(bash、zsh 或 fish)
*   文本编辑器
*   对 React 的基本理解

如果你没有任何一个，还有 [StackBlitz](https://stackblitz.com/) 和 GitHub [代码空间](https://github.com/codespaces)，你可以在那里创建一个空的 GitHub 库，并从那里开始开发环境。

在下面的例子中，我将使用 VS 代码作为我的文本编辑器，Yarn 作为我首选的包管理器。如果你喜欢 npm，那很好。👍

你也可以在 GitHub 上找到本教程的完整[代码。](https://github.com/sitepoint-editors/my-gatsby-blog)

好了，该开始了！

## 你好，世界！

是时候启动盖茨比项目了。我将从命令行开始执行大部分操作:

```
# create the project directory
mkdir my-gatsby-blog
# change into the directory
cd my-gatsby-blog
# initialise a package.json file
yarn init -y
# initialise the git repo
git init 
```

酷毙了。现在，在进行任何其他操作之前，我需要在安装任何 npm 模块之前添加一个`.gitignore`文件:

```
# create .gitignore file in my directory
touch .gitignore
# add ignore contents with echo
echo "# Project dependencies
.cache
node_modules

# Build directory
public

# Other
.DS_Store
yarn-error.log" > .gitignore 
```

现在我可以安装我需要的所有 npm 优点，而不用 VS 代码 Git 对我大喊太多的活动更改。现在让我们安装一些依赖项来启动并运行 Gatsby:

```
yarn add gatsby react react-dom
# -p is to create parent directories too if needed
mkdir -p src/pages
# create the index (home) page
touch src/pages/index.js 
```

接下来，我们将为项目添加(许多)第一个 React 组件。我将把以下内容添加到我创建的`index.js`文件中:

```
import React from "react";

export default function IndexPage() {
  return <h1>Hello, World!</h1>;
} 
```

我现在准备从命令行运行 Gatsby `develop`命令:

```
# if you're using npm 👇
# $(npm bin)/gatsby develop
yarn gatsby develop 
```

这将启动 Gatsby dev 服务器，并告诉它我的项目可以在端口 8000(默认的 Gatsby 端口)上的浏览器中查看。网址为 [http://localhost:8000/](http://localhost:8000/) 。

直接从命令行界面(CLI)使用 Gatsby 二进制命令是完全可行的，但是大多数人会将可用的命令添加到`package.json`文件的`scripts`部分，如下所示:

```
"scripts": {
  "build": "gatsby build",
  "dev": "gatsby develop",
  "serve": "gatsby serve",
  "clean": "gatsby clean"
}, 
```

作为额外的收获，这里有一些额外的东西可以添加到盖茨比的剧本中。

如果我们不想每次都在同一个端口上运行项目，可以用`-p`标志来改变它，并在其后指定一个端口。比如`gatsby develop -p 8945`。

如果我们想在项目准备好之后打开浏览器选项卡，我们可以将`-o`添加到脚本中。

我将对`serve`脚本做同样的事情，这样我就知道当我构建一个项目时，它是在一个不同于开发的端口上:

```
"scripts": {
  "build": "gatsby build",
  "dev": "gatsby develop -p 8945 -o",
  "serve": "gatsby serve -p 9854 -o",
  "clean": "gatsby clean"
}, 
```

与此同时，强制性的“你好，世界！”欢迎完毕，我可以继续这篇文章的其余部分了！🤓

最后，我将提交到目前为止我所做的更改:

```
# add everything for committing
git add .
# commit to repo
git commit -m 'init project' 
```

## 博客内容

好的，现在这个项目还没有什么大的进展，所以首先我将添加一些内容，再次从命令行添加:

```
# this creates the folders in the root of the project
mkdir -p content/2021/03/{06/hello-world,07/second-post,08/third-post}
# create individual files
touch content/2021/03/06/hello-world/index.mdx
touch content/2021/03/07/second-post/index.mdx
touch content/2021/03/08/third-post/index.mdx 
```

我会在我做的例子中用到这些。

您会注意到文件扩展名`.mdx`。这是一个 MDX 文件。

### 前页

在我为博客添加一些内容之前，我需要说一下前面的事情。

Front matter 是一种存储文件信息的方法，Gatsby 可以使用它来构建页面。现在，我将添加一个帖子的`title`和一个`date`。我也会给他们增加一些内容。这是我们的第一篇文章:

```
---
title: Hello World - from mdx!
date: 2021-03-06
---

My first post!!

## h2 Heading

Some meaningful prose

### h3 Heading

Some other meaningful prose 
```

这是我们的第二个帖子:

```
---
title: Second Post!
date: 2021-03-07
---

This is my second post! 
```

第三个帖子:

```
---
title: Third Post!
date: 2021-03-08
---

This is my third post!

> with a block quote!

And a code block:

```js
const wheeeeee = true;
``` 
```

这些帖子到此为止，因为这些帖子还没有被 Gatsby 识别为页面。我需要让 Gatsby 知道在哪里可以找到要添加到项目中的内容。为此，我将向 Gatsby 添加一个配置文件。

让我们提交我对 Git 所做的更改:

```
# add changed file for committing
git add .
# commit to repo
git commit -m 'add markdown files' 
```

## Gatsby 配置

Gatsby config 用于定义和配置您可以使用的许多 Gatsby 插件。关于盖茨比外挂生态系统的更多信息，请访问。现在，我将再次在终端中创建该文件:

```
touch gatsby-config.js 
```

这将在项目的根目录下创建`gatsby-config.js`，这样我就可以开始配置 Gatsby 来读取我之前创建的`.mdx`文件。

## 盖茨比插件

现在我可以安装和配置 Gatsby 需要的插件来获取和显示我创建的文件。我现在将全部安装，并简要说明它们的用途:

```
yarn add gatsby-plugin-mdx @mdx-js/mdx @mdx-js/react gatsby-source-filesystem 
```

快速查看一下`package.json`，现在显示我已经安装了以下依赖版本:

```
"dependencies": {
  "@mdx-js/mdx": "^1.6.22",
  "@mdx-js/react": "^1.6.22",
  "gatsby": "^3.1.1",
  "gatsby-plugin-mdx": "^2.1.0",
  "gatsby-source-filesystem": "^3.1.0",
  "react": "^17.0.1",
  "react-dom": "^17.0.1"
}, 
```

需要注意的一点是，在 Gatsby 中，不需要用 React 17 在你的组件中导入 React。但是为了完整，也为了避免任何混淆，我将把它包括在这些例子中。

现在我需要配置`gatsby-plugin-mdx`和`gatsby-plugin-mdx`。在`gatsby-config.js`文件中，我会加上这个:

```
module.exports = {
  plugins: [
    `gatsby-plugin-mdx`,
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/content`,
        name: `content`,
      },
    },
  ],
}; 
```

提交到目前为止的更改:

```
git add .
git commit -m 'add gatsby plugins' 
```

## 盖茨比图 QL

现在是时候使用 Gatsby GraphQL 客户端 GraphQL 来查看我在 Gatsby 中的文件了。如果您一直在关注，您可能已经注意到 CLI 指示了两个 URL 位置来查看项目:

```
You can now view my-gatsby-blog in the browser.
⠀
  http://localhost:8000/
⠀
View GraphiQL, an in-browser IDE, to explore your site's data and schema
⠀
  http://localhost:8000/___graphql 
```

我现在将使用`___graphql`(三个下划线)路径来查看文件系统中的文件。

如果这看起来有点吓人，我将尝试涵盖所有可能看起来没有多大意义的部分。如果您继续学习，您应该可以将示例复制到 GraphiQL explorer 中。

当我打开 GraphiQL 浏览器时，我有几个**浏览器**面板。这是项目中所有可用的数据，取决于我在`gatsby-config.js`文件中的配置。

旁边是 **GraphiQL** 查询面板和结果。我将在这里编写 GraphQL 查询来检索我需要的数据。在查询面板的底部还有一个**查询变量**部分，我稍后会谈到它。

最右边是 GraphQL **文档浏览器**。由于 GraphQL 的严格类型，这意味着它能够在其数据上生成自己的文档。但是这超出了本文的范围。

## 用 GraphQL 查询本地文件

接下来，我将在 GraphiQL 查询面板中查询我之前添加的文件。在这个查询中，我将查询在文件的字体中定义的标题和日期:

```
{
  allMdx {
    nodes {
      frontmatter {
        title
        date
      }
    }
  }
} 
```

如果我们将它弹出到查询面板，按下大的播放按钮，我们将在结果面板中获得一些数据。我们还可以使用左侧面板中的**浏览器**来挑选数据。以下是运行查询后我得到的结果:

```
{
  "data": {
    "allMdx": {
      "nodes": [
        {
          "frontmatter": {
            "title": "Hello World - from mdx!",
            "date": "2021-03-06T00:00:00.000Z"
          }
        },
        {
          "frontmatter": {
            "title": "Second Post!",
            "date": "2021-03-07T00:00:00.000Z"
          }
        },
        {
          "frontmatter": {
            "title": "Third Post!",
            "date": "2021-03-08T00:00:00.000Z"
          }
        }
      ]
    }
  },
  "extensions": {}
} 
```

这是一个大的 JSON 对象，包含我们在查询中请求的相关信息。我们很快就会看到如何使用它。目前，这意味着我们可以在盖茨比项目中使用这些数据来制作页面。

## 网站元数据

在`gatsby-config.js`文件中，还有一个指定站点元数据的选项。站点元数据是当我想要重用像站点标题和描述这样的公共数据时使用的。

当我想为搜索引擎优化(SEO)添加 meta 标签时，这将是非常有用的。(同样，稍后会有更多相关内容。)现在，我将使用`siteMetadata`对象在`gatsby-config.js`中定义一些关于站点的基本信息。

我*可以这样直接在`module.exports`中定义站点 metada 吗:*

```
module.exports = {
  siteMetadata: {
    title: `My Gatsby Blog`,
    description: `This is my coding blog.`,
  },
  plugins: [
    // configured plugins here
    {
      // empty for brevity
    },
  ],
}; 
```

站点元数据对象可能会变得有点大，我发现将它保存在自己的对象中可以使推理变得简单一些，因此我将单独定义它:

```
const siteMetadata = {
  title: `My Gatsby Blog`,
  description: `This is my coding blog.`,
}; 
```

然后将`siteMetadata`对象添加到 Gatsby 配置文件中:

```
const siteMetadata = {
  title: `My Gatsby Blog`,
  description: `This is my coding blog.`,
};

module.exports = {
  siteMetadata,
  plugins: [
    // configured plugins here
    {
      // empty for brevity
    },
  ],
}; 
```

现在，我可以再次跳转到 GraphiQL explorer，使用以下查询来查询该站点元数据:

```
{
  site {
    siteMetadata {
      title
      description
    }
  }
} 
```

如果您正在对`gatsby-config.js`文件进行更改，那么停止并重启开发服务器总是一个好主意，所以我将这样做(`Ctrl` + `c`，然后是`yarn develop`，然后在 GraphiQL explorer 中刷新页面并再次运行查询来取回数据:

```
{
  "data": {
    "site": {
      "siteMetadata": {
        "title": "My Gatsby Blog",
        "description": "This is my coding blog."
      }
    }
  },
  "extensions": {}
} 
```

## 制作一个站点元数据挂钩

现在我在 Gatsby 文件系统中有了站点元数据，我可以用 Gatsby 静态查询钩子`useStaticQuery`在任何我想使用它的地方查询它。在将以下内容添加到`src/pages/index.js`文件后，我将关闭开发服务器并重启:

```
import { graphql, useStaticQuery } from "gatsby";
import React from "react";

export default function IndexPage() {
  const {
    site: { siteMetadata },
  } = useStaticQuery(graphql` {
      site {
        siteMetadata {
          title
          description
        }
      }
    } `);
  console.log("=====================");
  console.log(siteMetadata);
  console.log("=====================");
  return <h1>Hello World!</h1>;
} 
```

这里有一个关于一些符号的快速注释:`const { site: { siteMetadata }, }`是在`site`查询中获取数据的快速方法，这里我从`site`对象中提取了`siteMetadata`。这被称为[破坏](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)。

现在，在我再次启动 dev 服务器之后，我可以转到浏览器控制台(在 Windows/Linux 中为`Control` + `Shift` + `J`，在 macOS 中为`Command` + `Option` + `J`，并在控制台输出中看到`siteMetadata`对象。

我得到以下控制台输出:

```
=====================
{title: "My Gatsby Blog", description: "This is my coding blog."}
  description: "This is my coding blog."
  title: "My Gatsby Blog"
  __proto__: Object
===================== 
```

不用担心 404 页面丢失的控制台警告没有找到(`net::ERR_ABORTED 404 (Not Found)`)。我过会儿做那个。

为了避免每次都必须编写这个查询，我想在一个组件中使用它。我将把它抽象成一个独立的钩子:

```
# make a folder for all the hooks to live
mkdir src/hooks
# creathe the file
touch src/hooks/use-site-metadata.js 
```

现在，我将在新创建的`src/hooks/use-site-metadata.js`文件中添加一个挂钩，以按需获取站点元数据:

```
import { graphql, useStaticQuery } from "gatsby";
export const useSiteMetadata = () => {
  const { site } = useStaticQuery(
    graphql` query SITE_METADATA_QUERY {
        site {
          siteMetadata {
            title
            description
          }
        }
      } `
  );
  return site.siteMetadata;
}; 
```

您可能已经注意到这个查询与 GraphiQL explorer 中的查询不同:

```
+ query SITE_METADATA_QUERY {  site {
    siteMetadata {
      title
      description
    }
 } } 
```

这是对查询的命名。因为我将在项目中使用很多查询，所以给它们起一个有意义的名字是有意义的。

现在我将在`src/pages/index.js`文件中实现新的挂钩:

```
import React from "react";
import { useSiteMetadata } from "../hooks/use-site-metadata";

export default function IndexPage() {
  const { title, description } = useSiteMetadata();
  return (
    <>
      <h1>{title}</h1>
      <p>{description}</p>
    </>
  );
} 
```

这就不那么罗嗦了，我可以从`SITE_METADATA_QUERY`中挑选我想要的项目。

是时候提交到目前为止所做的更改了:

```
git add .
git commit -m 'add site metadata and metadata hook' 
```

## 使用主题用户界面的样式

为了设计这个项目，我将使用[主题用户界面](https://theme-ui.com/components)，因为它实现布局和黑暗模式等功能的速度很快。我将详细说明与我正在做的事情相关的内容和原因，尽管这不是如何使用主题 UI 的指南。

主题用户界面还有一些附加的依赖项，它们是:

```
yarn add theme-ui gatsby-plugin-theme-ui @theme-ui/presets 
```

安装好之后，我需要将`gatsby-plugin-theme-ui`添加到`gatsby-config.js`插件数组中:

```
module.exports = {
  siteMetadata,
  plugins: [
    `gatsby-plugin-theme-ui`,
    `gatsby-plugin-mdx`,
    {
      resolve: `gatsby-source-filesystem`,
      // rest of the module unchanged 
```

现在，如果我停止并重新启动开发服务器，我有一个稍微不同的网站！一切都变得有点发青了——准确地说，是长春花！这是`gatsby-plugin-theme-ui`在做它的事情，这个颜色是默认的。

主题 UI 的 Gatsby 插件提供了许多配置选项，其中一些我将在需要时详细介绍。现在，我将创建一个文件夹并定义一个主题对象供主题 UI 使用:

```
# create the folder for the Theme UI theme
mkdir src/gatsby-plugin-theme-ui
# create the theme file
touch src/gatsby-plugin-theme-ui/index.js 
```

在`src/gatsby-plugin-theme-ui/index.js`文件中，我将添加几个主题 UI 预置，定义主题对象，[将`swiss`预置中的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)扩展到`theme`、`theme`、`styles`。

对于黑暗模式，我使用了`deep`主题 UI 预设，并将其扩展到`dark`的`modes`对象中。(很快会有更多相关内容。)现在，我知道这将为我解决很多主题化问题:

```
import { deep, swiss } from "@theme-ui/presets";

const theme = {
  ...swiss,
  colors: {
    ...swiss.colors,
    modes: {
      dark: {
        ...deep.colors,
      },
    },
  },

  styles: {
    ...swiss.styles,
    p: {
      fontFamily: "body",
      fontWeight: "body",
      lineHeight: "body",
      fontSize: 3,
    },
  },
};

export default theme; 
```

现在，如果我重新启动开发服务器(再次，是的，你会学会处理它)，它会看起来更容易接受瑞士主题的应用。在编写的时候，主题 UI 有时不会刷新`localhost`页面，所以需要做一次浏览器页面刷新。

将到目前为止的更改提交给 Git:

```
git add .
git commit -m 'add Theme UI and configure presets' 
```

是时候添加一些 React 组件了！

## 布局组件

Gatsby 没有具体的布局，把这个责任交给了开发者。在这种情况下，我为整个网站做一个布局。在 Gatsby 项目中可以使用许多布局，但是在这个例子中，我只使用一个。

现在我将重构我目前拥有的东西，这样所有的东西都被一个`Layout`组件包装起来。我目前在`src/pages/index.js`中的东西可以用于一个`Header`组件，所以我现在要为`Layout`和`Header`创建几个文件:

```
# create a components folder
mkdir src/components
# create Layout and Header files
touch src/components/header.js src/components/layout.js 
```

现在将标题和描述从`src/pages/index.js`移动到新创建的`src/components/header.js`组件。

我没有在`Header`组件中使用`useSiteMetadata`，而是将我需要的`useSiteMetadata`道具从`Layout`组件传递给消息头，这是消息头将要存在的地方。(稍后将详细介绍。)首先，这里是 header 组件，它位于`src/components/header.js`:

```
import { Link as GatsbyLink } from "gatsby";
import React from "react";
import { Box, Heading, Link } from "theme-ui";

export const Header = ({ siteTitle, siteDescription }) => {
  return (
    <Box as="header" sx={{ bg: "highlight", mb: "1.45rem" }}>
      <Box
        as="div"
        sx={{
          m: "0 auto",
          maxWidth: "640px",
          p: "1.45rem 1.0875rem",
        }}
      >
        <Link as={GatsbyLink} to="/">
          <Heading>{siteTitle}</Heading>
        </Link>
        <Box as="p" variant="styles.p">
          {siteDescription}
        </Box>
      </Box>
    </Box>
  );
}; 
```

我已经使用主题 UI 布局元素添加了一些基本样式。这个看起来和之前有点不一样:`Box`、`Link`、`Heading`……什么？这些都是主题 UI 组件，可以用于布局、表单元素等等。

您可能会注意到添加到`Link`组件的`as={GatsbyLink}`链接属性。这在主题 UI 中使用了`as`道具，并让被传入的组件采用主题 UI 样式。

保罗·斯坎伦有一个很棒的帖子[更详细地解释了](https://paulie.dev/posts/2020/04/gatsby-or-theme-ui-link/)这是如何在主题用户界面中实现的。对于主题 UI 真正全面的解释，还有同一作者的“[理解主题 UI](https://paulie.dev/posts/2021/03/understanding-theme-ui/) ”。

还有来自主题 UI 的`sx`和`variant`道具。`sx`允许将其他样式传递给组件。把它想象成一个相当于 JSX `style={{}}`的道具。`variant` prop 允许将一组预定义的样式从主题应用到正在使用的组件。

现在对于位于`src/components/layout.js`中的`Layout`组件:

```
import React from "react";
import { Box } from "theme-ui";
import { useSiteMetadata } from "../hooks/use-site-metadata";
import { Header } from "./header";

export const Layout = ({ children }) => {
  const { title, description } = useSiteMetadata();
  return (
    <>
      <Header siteTitle={title} siteDescription={description} />
      <Box
        as="div"
        sx={{
          margin: "0 auto",
          maxWidth: "640px",
          padding: "0 1.0875rem 1.45rem",
        }}
      >
        <Box as="main">{children}</Box>
      </Box>
    </>
  );
}; 
```

在这里，我保留了`useSiteMetadata`钩子并传递了`Header`组件需要的道具，同样是用`sx`道具来添加一些基本样式，以便与主包含 div 对齐。然后我为`children`创建一个`main`包装器。

`children`属性是返回`Layout`组件封装的任何东西，这将包括我想要应用布局的任何东西。例如:

```
<Layout>
  <h1>This is wrapped</h1>
</Layout> 
```

这将返回`Layout`组件中的所有内容及其包装的内容。在上面的例子中，这将是由`Layout`组件包装的头部和 H1。

例如，我将返回到索引页面(`src/pages.index.js`)并添加以下内容:

```
import React from "react";
import { Layout } from "../components/layout";

export default function IndexPage() {
  return (
    <>
      <Layout>
        <h1>This is wrapped</h1>
      </Layout>
    </>
  );
} 
```

结果是在`Layout`组件和 H1 `This is wrapped`中提供的头。

## 索引页面帖子查询

现在是时候获取我在开始时创建的文章，并将它们作为可点击链接的列表显示在索引页面上。

为了获得文章信息，我将重新创建我在关于使用 GraphQL 查询本地文件的[一节中创建的查询，并增加一些额外的内容:](#querylocalfileswithgraphql)

```
{
  allMdx(sort: { fields: [frontmatter___date], order: DESC }) {
    nodes {
      id
      slug
      excerpt(pruneLength: 250)
      frontmatter {
        title
        date(formatString: "YYYY MMMM Do")
      }
    }
  }
} 
```

我已经添加了节点的`id`和`slug`。这是`.mdx`文件的路径。

`excerpt`使用一个 Gatsby 函数从文章正文中获取前 250 个字符，还使用另一个内置的 Gatsby 函数为`date`添加了一些格式。

然后，作为按日期降序排列文章的一种方式，我添加了一个排序:`allMdx(sort: { fields: [frontmatter___date], order: DESC }) {`。这是在帖子前面排序的日期问题。

将它添加到 GraphiQL explorer 中会得到以下结果:

```
{
  "data": {
    "allMdx": {
      "nodes": [
        {
          "id": "2bed526a-e5a9-5a00-b9c0-0e33beafdbcf",
          "slug": "2021/03/08/third-post/",
          "excerpt": "This is my third post! with a block quote! And a code block:",
          "frontmatter": {
            "title": "Third Post!",
            "date": "2021 March 8th"
          }
        },
        {
          "id": "89ea266b-c981-5d6e-87ef-aa529e98946e",
          "slug": "2021/03/07/second-post/",
          "excerpt": "This is my second post!",
          "frontmatter": {
            "title": "Second Post!",
            "date": "2021 March 7th"
          }
        },
        {
          "id": "75391ba1-3d6b-539f-86d2-d0e6b4104806",
          "slug": "2021/03/06/hello-world/",
          "excerpt": "My first post!! h2 Heading Some meaningful prose h3 Heading Some other meaningful prose",
          "frontmatter": {
            "title": "Hello World - from mdx!",
            "date": "2021 March 6th"
          }
        }
      ]
    }
  },
  "extensions": {}
} 
```

现在，我可以使用`src/pages/index.js`文件中的查询来获取索引页面中使用的数据。在`IndexPage`函数中，我将通过 GraphQL 查询从给予组件的属性中析构`data`:

```
import { graphql, Link as GatsbyLink } from "gatsby";
import React from "react";
import { Box, Heading, Link } from "theme-ui";
import { Layout } from "../components/layout";

export default function IndexPage({ data }) {
  return (
    <>
      <Layout>
        {data.allMdx.nodes.map(({ id, excerpt, frontmatter, slug }) => (
          <Box
            key={id}
            as="article"
            sx={{
              mb: 4,
              p: 3,
              boxShadow: "0 10px 15px -3px rgba(0, 0, 0, 0.1)",
              border: "1px solid #d1d1d1",
              borderRadius: "15px",
            }}
          >
            <Link as={GatsbyLink} to={`/${slug}`}>
              <Heading>{frontmatter.title}</Heading>
              <Box as="p" variant="styles.p">
                {frontmatter.date}
              </Box>
              <Box as="p" variant="styles.p">
                {excerpt}
              </Box>
            </Link>
          </Box>
        ))}
      </Layout>
    </>
  );
}

export const query = graphql` query SITE_INDEX_QUERY {
    allMdx(sort: { fields: [frontmatter___date], order: DESC }) {
      nodes {
        id
        excerpt(pruneLength: 250)
        frontmatter {
          title
          date(formatString: "YYYY MMMM Do")
        }
        slug
      }
    }
  } `; 
```

这使用了前面详述的组件。注意，`excerpt`、`frontmatter`和`slug`从`data.allMdx.nodes`开始被析构:

```
{data.allMdx.nodes.map(({ excerpt, frontmatter, slug }) => ( 
```

点击链接会把我带到 Gatsby.js 开发 404 页面。那是因为我还没有为`.mxd`文件制作页面。那是下一个。

在继续之前，我将提交到目前为止我所做的事情:

```
git add .
git commit -m 'add Header and Layout components' 
```

## 通过 MDX 使用 Gatsby 文件系统路由 API

我将使用 Gatsby [文件系统路由 API](https://www.gatsbyjs.com/docs/reference/routing/file-system-route-api/) 来获取我之前创建的帖子的文件路径。文件系统路由 API 是从我的 GraphQL 数据以编程方式创建页面的一种方式。

当 Gatsby 在构建时生成文件系统数据时，这种方法对于要作为目标的页面有一个特殊的文件符号。该文件指示节点和 slug。我将首先创建文件，然后详细说明数据来自哪里:

```
# create the route api file
touch src/pages/{mdx.slug}.js 
```

在该文件中，我将为希望包含在该模板中的数据定义一个 GraphQL 查询:

```
import { graphql } from "gatsby";
import { MDXRenderer } from "gatsby-plugin-mdx";
import React from "react";
import { Box } from "theme-ui";

export default function PostPage({ data }) {
  const {
    body,
    frontmatter: { title },
  } = data.mdx;
  return (
    <>
      <Box as="h1" variant="styles.h1" fontSize="4xl">
        {title}
      </Box>
      <MDXRenderer>{body}</MDXRenderer>
    </>
  );
}

export const query = graphql` query POST_BY_SLUG($slug: String) {
    mdx(slug: { eq: $slug }) {
      id
      slug
      body
      frontmatter {
        date
        title
      }
    }
  } `; 
```

现在有很多代码，所以我将分解它。它主要与 GraphQL 查询有关:

```
query POST_BY_SLUG($slug: String) {
  mdx(slug: { eq: $slug }) {
    id
    slug
    body
    frontmatter {
      date
      title
    }
  }
} 
```

查询的开始是用`POST_BY_SLUG($slug: String)`接收一个`slug`，主节点是`mdx`，所以我用`mdx.slug`作为文件名`{mdx.slug}.js`。

如果我将该查询粘贴到我的 GraphiQL explorer 中并按下 play 按钮，我会得到这样的结果:

```
{
  "data": {
    "mdx": null
  },
  "extensions": {}
} 
```

这是因为在 GraphiQL explorer 中没有为`$slug`定义变量。如果你看看查询面板的底部，你会看到有一个查询变量部分。点击这个将展开它。在这里我需要为`slug`添加一个变量。我将用一个文件的路径在花括号中定义它:

```
{
  "slug": "2021/03/08/third-post/"
} 
```

再次运行查询，我将获得该文件的所有数据。为了可读性，我已经注释掉了`body`输出:

```
{
  "data": {
    "mdx": {
      "id": "105a5c78-6a36-56e8-976c-d53d8e6ca623",
      "slug": "2021/01/08/third-post/",
      "body": "function _extends() ...", // compiled MDX here
      "frontmatter": {
        "date": "2021-03-08T00:00:00.000Z",
        "title": "Third Post!"
      }
    }
  },
  "extensions": {}
} 
```

文件系统路由 API 所做的是将单个文件路径传递到页面查询`src/pages/{mdx.slug}.js`中，并将来自该查询的数据返回到页面`({ data })`中被传递到页面的适当位置。

在这个文件中，您可能会注意到我已经从返回的数据中析构了`body`，然后从`frontmatter`中析构了`title`，这是一个两级析构:

```
const {
  body,
  frontmatter: { title },
} = data.mdx; 
```

另一种方法是:

```
const body = data.mdx.body;
const title = data.mdx.frontmatter.title; 
```

使用析构使它不那么冗长。

最后要注意的是,`MDXRenderer`包装了帖子的`body`。这是`.mdx`文件中包含的所有内容，在前面的事块之后。来自 GraphiQL 查询的编译 MDX(被注释掉)是需要包装在`MDXRenderer`中的内容:

```
<MDXRenderer>{body}</MDXRenderer> 
```

我现在将提交更改:

```
git add .
git commit -m 'create file route API file' 
```

## 根包装器概念

现在，单击索引页面上的一个链接会将我带到所需的`.mdx`页面，但它看起来与索引页面有点不同，对吗？

那是因为还没有包装它的版式。这是我可以使用 Gatsby 浏览器 API 并使用`wrapPageElement`函数包装所有页面元素的地方。也是[推荐](https://www.gatsbyjs.com/docs/reference/config-files/gatsby-browser/#wrapPageElement)我在 Gatsby SSR 里用同样的函数。

为了避免在两个文件中重复相同的代码，我将用我要使用的实际代码创建第三个文件，并将其导入到前面提到的两个`gatsby-*`文件中。

首先，我将创建所需的文件:

```
# create gatsby-browser.js and gatsby-ssr.js and root-wrapper.js
touch gatsby-browser.js gatsby-ssr.js root-wrapper.js 
```

根包装文件是我将使用`wrapPageElement`函数的地方:

```
// root-wrapper.js
import React from "react";
import { Layout } from "./src/components/layout";

export const rootWrapper = ({ element }) => {
  return <Layout>{element}</Layout>;
}; 
```

然后，在`gatsby-browser.js`和`gatsby-ssr.js`文件中，我将添加以下内容:

```
import { rootWrapper } from "./root-wrapper";

export const wrapPageElement = rootWrapper; 
```

如果需要对`wrapPageElement`函数进行任何更改，我可以在一个文件`root-wrapper.js`中完成。

是时候停止并重新启动 dev 服务器，看看更改是否生效了！

因为这里使用了布局组件来包装站点上的所有页面元素，所以不再需要将它保存在索引页面上，所以我将从`src/pages/index.js`中删除它:

```
import { graphql, Link as GatsbyLink } from "gatsby";
import React from "react";
import { Box, Heading, Link } from "theme-ui";
- import { Layout } from "../components/layout"; 
export default function IndexPage({ data }) {
 return (
 <> - <Layout>  {data.allMdx.nodes.map(({ id, excerpt, frontmatter, slug }) => (
          <Box
            key={id}
            as="article"
            sx={{
              mb: 4,
              p: 3,
              boxShadow: "0 10px 15px -3px rgba(0, 0, 0, 0.1)",
              border: "1px solid #d1d1d1",
              borderRadius: "15px",
            }}
          >
            <Link as={GatsbyLink} to={`/${slug}`}>
              <Heading>{frontmatter.title}</Heading>
              <Box as="p" variant="styles.p">
                {frontmatter.date}
              </Box>
              <Box as="p" variant="styles.p">
                {excerpt}
              </Box>
            </Link>
          </Box>
 ))} - </Layout>  </>
 ); };
// rest unchanged 
```

在继续之前，我将提交到目前为止所做的更改:

```
git add .
git commit -m 'add root wrapper to Gatsby Browser and SSR' 
```

## 404 页

是时候制作 404 页了！

```
# create the 404.js page
touch src/pages/404.js 
```

在`src/pages/404.js`文件中，我会加一条消息:

```
import React from "react";
import { Box, Heading } from "theme-ui";

export default function NotFound() {
  return (
    <>
      <Heading variant="styles.h1">
        Page not found!
        <span role="img" aria-label="crying face">
          😢
        </span>
      </Heading>
      <Box as="h2" variant="styles.h2">
        It looks like that page doesn't exist
      </Box>
    </>
  );
} 
```

现在我可以直接导航到 404 页面查看:`http://localhost:8000/404`。

注意，当使用`gatsby develop`进行开发时，Gatsby 将继续使用默认的 404 页面，该页面会覆盖您的自定义 404 页面。

提交此内容，然后进入下一部分:

```
git add .
git commit -m 'add 404 page' 
```

## 深色主题切换

黑暗模式是博客编码的一个基本特征。(我半开玩笑地说，以防你不确定！)我将使用主题 UI 颜色模式挂钩`useColorMode`，并在我之前在`theme`对象中定义的两种模式之间进行简单切换。以下是添加到`src/components/header.js`的内容:

```
import { Link as GatsbyLink } from "gatsby";
import React from "react";
+ import { Box, Button, Heading, Link, useColorMode } from "theme-ui"; 
export const Header = ({ siteTitle, siteDescription }) => {
+ const [colorMode, setColorMode] = useColorMode();  return (
    <Box as="header" sx={{ bg: "highlight", mb: "1.45rem" }}>
      <Box
        as="div"
        sx={{
          m: "0 auto",
          maxWidth: "640px",
          p: "1.45rem 1.0875rem",
        }}
      >
        <Link as={GatsbyLink} to="/">
          <Heading>{siteTitle}</Heading>
        </Link>
        <Box as="p" variant="styles.p">
          {siteDescription}
 </Box> + <Button + onClick={(e) => { + setColorMode(colorMode === "default" ? "dark" : "default"); + }} + > + {colorMode === "default" ? "Dark" : "Light"} + </Button>  </Box>
    </Box>
 ); }; 
```

但是这看起来不太好，所以我将使用主题 UI `Flex`组件包装容器，并将按钮移到右边:

```
import { Link as GatsbyLink } from "gatsby";
import React from "react";
+import { Box, Button, Flex, Heading, Link, useColorMode } from "theme-ui"; 
export const Header = ({ siteTitle, siteDescription }) => {
 const [colorMode, setColorMode] = useColorMode();
  return (
    <Box as="header" sx={{ bg: "highlight", mb: "1.45rem" }}>
      <Box
        as="div"
        sx={{
          m: "0 auto",
          maxWidth: "640px",
          p: "1.45rem 1.0875rem",
        }}
 > + <Flex> + <Box sx={{ flex: "1 1 auto", flexDirection: "column" }}>  <Link as={GatsbyLink} to="/">
              <Heading>{siteTitle}</Heading>
            </Link>
            <Box as="p" variant="styles.p">
              {siteDescription}
 </Box> + </Box>  <Button
            onClick={(e) => {
              setColorMode(colorMode === "default" ? "dark" : "default");
            }}
          >
            {colorMode === "default" ? "Dark" : "Light"}
 </Button> + </Flex>  </Box>
    </Box>
 ); }; 
```

在进入下一部分之前，Git commit:

```
git add .
git commit -m 'add theme toggle to header' 
```

## 代码块

代码块现在看起来有点复杂，所以我将在众多方便的主题 UI 包中添加一些语法高亮。我用的这个是[棱镜](https://theme-ui.com/packages/prism)。

我需要安装这个包，并在`gatsby-plugin-theme-ui`文件夹中创建一个名为`components.js`的组件:

```
# install the package
yarn add @theme-ui/prism
# create Theme UI components file
touch src/gatsby-plugin-theme-ui/components.js 
```

在那个文件中，我需要定义我想要应用棱镜样式的地方，这是所有的`pre`和`code`标签:

```
import Prism from "@theme-ui/prism";

export default {
  pre: (props) => props.children,
  code: Prism,
}; 
```

定义好之后，我还需要在`theme`对象中定义我想要使用的 Prism 主题:

```
// scr/gatsby-plugin-theme-ui/index.js

import { deep, swiss } from "@theme-ui/presets";
+ import nightOwl from "@theme-ui/prism/presets/night-owl.json"; 
const theme = {
 ...swiss,
  colors: {
    ...swiss.colors,
    modes: {
      dark: {
        ...deep.colors,
      },
    },
 }, 
 styles: {
 ...swiss.styles, + code: { + ...nightOwl, + },  // remainder of the file unchanged 
```

需要再一次停止和启动 dev 服务器才能看到更改生效！

提交更改并进入下一部分:

```
git add .
git commit -m 'add Prism package and update theme object' 
```

## 向 MDX 添加组件

**下一位是可选的。**降价 JSX 允许 React (JSX)组件包含在降价中。为了演示这一点，我将添加一个`RainbowText`组件，它将在一个动画周期中制作一些颜色的动画。我的动画需要一个额外的依赖:来自`@emotion/react`的`keyframes`。我现在就安装:

```
# create component file
touch src/components/rainbow-text.js
# install @emotion/react
yarn add @emotion/react 
```

如果开发服务器正在运行，这可能会破坏它，所以我现在停止它。

在`src/components/rainbow-text.js`文件中，我将添加这个组件:

```
import { keyframes } from "@emotion/react";
import React from "react";
import { Box } from "theme-ui";

export const RainbowText = ({ children }) => {
  const rainbow = keyframes({
    "0%": {
      backgroundPosition: "0 0",
    },
    "50%": {
      backgroundPosition: "400% 0",
    },
    "100%": {
      backgroundPosition: "0 0",
    },
  });

  return (
    <Box
      as="span"
      variant="styles.p"
      sx={{
        fontWeight: "heading",
        cursor: "pointer",
        textDecoration: "underline",
        ":hover": {
          background:
            "linear-gradient(90deg, #ff0000, #ffa500, #ffff00, #008000, #0000ff, #4b0082, #ee82ee) 0% 0% / 400%",
          animationDuration: "10s",
          animationTimingFunction: "ease-in-out",
          animationIterationCount: "infinite",
          animationName: `${rainbow}`,
          WebkitBackgroundClip: "text",
          WebkitTextFillColor: "transparent",
        },
      }}
    >
      {children}
    </Box>
  );
}; 
```

因为这是可选的，所以我不会详细说明这里发生了什么。只要知道这是一个很好的 CSS 效果。

创建好组件后，我可以将它导入到任何我想要使用的文件中。在这个例子中，我将它添加到`content/2021/03/third-post/index.mdx`。现在我已经添加了组件，文件的不同之处如下:

```
---
title: Third Post!
date: 2021-03-08
---

+ import { RainbowText } from "../../../../../src/components/rainbow-text"; 
This is my third post!

> with a block quote! 
+ <RainbowText>Wheeeeeeee</RainbowText> 
And a code block:

```js
const wheeeeee = true;
``` 
```

再次启动 dev 服务器后，我可以转到添加了该组件的帖子，当我将鼠标悬停在被包装在`<RainbowText>Wheeeeeeee</RainbowText>`中的文本上时，我可以看到动画效果。

你可能会对那个进口货做鬼脸。没完没了。然而，有一种方法可以解决这个问题，使用我之前详述的根包装器概念，并使用`MDXProvider`,它将——咳咳！—向 MDX 提供您传递给它的任何组件。

回到根包装器(`root-wrapper.js`)，我可以用`MDXProvider`包装页面`element`，并将`RainbowText`组件传递给`MDXProvider`:

```
import { MDXProvider } from "@mdx-js/react";
import React from "react";
import { Layout } from "./src/components/layout";
import { RainbowText } from "./src/components/rainbow-text";

const MDXComponents = {
  RainbowText,
};

export const rootWrapper = ({ element }) => {
  return (
    <Layout>
      <MDXProvider components={MDXComponents}>{element}</MDXProvider>
    </Layout>
  );
}; 
```

现在我可以从`.mdx`文件中删除导入:

```
---
title: Third Post!
date: 2021-03-08
---

- import { RainbowText } from "../../../../../src/components/rainbow-text"; 
This is my third post!

> with a block quote! 
<RainbowText>Wheeeeeeee</RainbowText> 
And a code block:

```js
const wheeeeee = true;
``` 
```

在停止并重启 dev 服务器后，我可以转到这篇文章，仍然可以看到`RainbowText`在工作。将组件直接添加到`MDXProvider`的额外好处是，当您想要使用组件时，不需要将组件导入到`.mdx`文档中。它可以通过所有 MDX 文档的提供者获得。

我现在就提交:

```
git add .
git commit -m 'add component for mdx' 
```

## 降价图片

如果我想在我的博客文章中添加图像，我可以将它们包含在 MDX 文件中，如下所示:

```
---
title: Hello World - from mdx!
date: 2021-03-06
---

My first post!!

## h2 Heading

![mdx logo](./mdx-logo.png)

Some meaningful prose

### h3 Heading

Some other meaningful prose 
```

`./mdx-logo.png`是我添加到`content/2021/03/06/hello-world`文件夹中的一个文件，我将它作为一个相对文件引用。不过，不是为了这个。如果我转到 hello world post，显示的图像会被破坏。我需要将`gatsby-remark-images`作为插件添加到`gatsby-plugin-mdx`中，这样它就知道如何处理图像文件:

```
yarn add gatsby-remark-images gatsby-plugin-sharp 
```

然后我需要在`gatsby-config.js`中配置插件:

```
const siteMetadata = {
 title: `My Gatsby Blog`,
 description: `This is my coding blog.`, };

module.exports = {
 siteMetadata,
  plugins: [
 `gatsby-plugin-theme-ui`, + `gatsby-plugin-sharp`, + { + resolve: `gatsby-plugin-mdx`, + options: { + gatsbyRemarkPlugins: [ + { + resolve: `gatsby-remark-images`, + options: { + maxWidth: 640, + }, + }, + ], + }, + }, + { + resolve: `gatsby-source-filesystem`, + options: { + path: `${__dirname}/content/`, + }, + },  {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/content`,
        name: `content`,
      },
    },
 ], }; 
```

额外的`gatsby-source-filesystem`对象是让 Gatsby 知道在哪里寻找要处理的图像。

立即提交:

```
git add .
git commit -m 'add and configure images' 
```

## 搜索引擎优化

如果我想让搜索引擎在网上找到我的内容，SEO 是非常重要的，所以我需要在我的博客上添加相关的 meta 标签。定义所有需要的相关标签可能是一个相当复杂的过程，所以为了节省时间，我创建了一个 [React SEO 组件](https://scottspence.com/2020/05/04/react-seo-component/)，用于在 Gatsby 中生成所有需要的元标签。

我将`yarn add`组件及其工作所需的依赖关系:

```
yarn add react-seo-component react-helmet gatsby-plugin-react-helmet 
```

我需要将`gatsby-plugin-react-helmet`添加到`gatsby-config.js`插件数组中:

```
module.exports = {
 siteMetadata,
 plugins: [ + `gatsby-plugin-react-helmet`,  `gatsby-plugin-theme-ui`,
    `gatsby-plugin-sharp`,
    {
 // rest unchanged 
```

然后是在我需要元标签的站点中使用`SEO`组件。

该组件需要相当多的道具，其中许多在整个站点中只定义一次，所以添加这些道具的最佳位置应该是在`siteMetadata`对象中。然后我可以用`useSiteMetadata`挂钩拉出我需要的东西。

我将向`siteMetadata`对象添加几个属性:

```
const siteMetadata = {
 title: `My Gatsby Blog`,
 description: `This is my coding blog.`, + lastBuildDate: new Date(Date.now()).toISOString(), + siteUrl: `https://dummy-url-for-now.com`, + authorName: `Author McAuthorson`, + twitterUsername: `@authorOfPosts`, + siteLanguage: `en-GB`, + siteLocale: `en_gb`, }; 
```

如果您正在跟进，您可以根据需要更改这些内容。目前,`siteUrl`可能是一个伪 URL。这有助于指向 Open Graph 协议中需要使用的任何图像，例如，当你在 Twitter、脸书、LinkedIn 和 Reddit 上分享你的帖子时，你会看到这个图像。

既然这些附加属性在`siteMetadata`对象上，我将需要能够查询它们。目前`useSiteMetadata`挂钩只有`title`和`description`，所以我现在把其余的加进去:

```
// src/hooks/use-site-metadata.js

import { graphql, useStaticQuery } from "gatsby";
export const useSiteMetadata = () => {
 const { site } = useStaticQuery(
    graphql`
      query SITE_METADATA_QUERY {
        site {
          siteMetadata {
            title
 description + lastBuildDate + siteUrl + authorName + twitterUsername + siteLanguage + siteLocale  }
        }
      }
    `
  );
 return site.siteMetadata; }; 
```

我将在所有页面中添加 SEO 组件。首先，我将在`src/pages/{mdx.slug}.js`页面中做文章页面。这是最复杂的一个，所以我将在这里列出不同之处，并详细说明发生了什么:

```
import { graphql } from "gatsby";
import { MDXRenderer } from "gatsby-plugin-mdx";
import React from "react";
+ import SEO from "react-seo-component"; import { Box } from "theme-ui";
+ import { useSiteMetadata } from "../hooks/use-site-metadata"; 
export default function PostPage({ data }) {
 const {
 body, + slug, + excerpt, + frontmatter: { title, date },  } = data.mdx; + const { + title: siteTitle, + siteUrl, + siteLanguage, + siteLocale, + twitterUsername, + authorName, + } = useSiteMetadata();  return (
 <> + <SEO + title={title} + titleTemplate={siteTitle} + description={excerpt} + pathname={`${siteUrl}${slug}`} + article={true} + siteLanguage={siteLanguage} + siteLocale={siteLocale} + twitterUsername={twitterUsername} + author={authorName} + publishedDate={date} + modifiedDate={new Date(Date.now()).toISOString()} + />  <Box as="h1" variant="styles.h1" fontSize="4xl">
        {title}
      </Box>
      <MDXRenderer>{body}</MDXRenderer>
    </>
 ); }

export const query = graphql`
 query POST_BY_SLUG($slug: String) {
    mdx(slug: { eq: $slug }) {
      id
      slug
 body + excerpt  frontmatter {
        date
        title
      }
    }
 } `; 
```

规范链接需要`siteUrl`、`slug`和`excerpt`(在 SEO 中非常重要)，元描述需要`excerpt`。

我使用`siteMetadata`钩子来获取组件需要的其余信息。`title`和`titleTemplate`用于组成你在浏览器标签中看到的内容。

`article`布尔值用于组件，因此它可以创建 [JSONLD](https://json-ld.org/) 格式的面包屑列表。其余的道具是帮助识别作者和出版日期。😅

太多了。我希望其中的一些有意义！对于这篇文章的范围，我将把它留在那里，但在这个问题上还有很多要深入研究的，我的意思是*很多*！

谢天谢地，`src/pages/index.js`页面简单了一点！

```
import { graphql, Link as GatsbyLink } from "gatsby";
import React from "react";
+ import SEO from "react-seo-component"; import { Box, Heading, Link } from "theme-ui";
+ import { useSiteMetadata } from "../hooks/use-site-metadata"; 
export default function IndexPage({ data }) {
+ const { + title, + description, + siteUrl, + siteLanguage, + siteLocale, + twitterUsername, + } = useSiteMetadata();  return (
 <> + <SEO + title={`Home`} + titleTemplate={title} + description={description} + pathname={siteUrl} + siteLanguage={siteLanguage} + siteLocale={siteLocale} + twitterUsername={twitterUsername} + />  {data.allMdx.nodes.map(({ id, excerpt, frontmatter, slug }) => ( // rest of component unchanged 
```

我有意在这两个例子中省略了图像。如果您有兴趣制作自己的 Open Graph images 以在该组件中使用，请查看"[Open Graph Images with Gatsby and Vercel](https://toast.scottspence.com/2020/04/30/serverless-og-images/)"这篇文章，了解如何使用无服务器函数来实现这一点。🔥

现在我可以构建站点了(几乎可以生产了)，一旦构建好了，我就可以检查 meta 标签的页面源代码了:

```
# build the site first
yarn build
# use gatsby serve to run built site locally
yarn serve 
```

一旦构建完成，我可以使用`yarn serve`让构建好的站点在`localhost:9000`上得到本地服务。在浏览器中，我可以用键盘快捷键`Ctrl` + `u`查看页面源代码。从这里，我可以检查`canonical`元标签，它将是元数据中使用的虚拟 URL。

好吧。将此提交给 Git，然后继续:

```
git add .
git commit -m 'add SEO component :sweat_smile:' 
```

## 推给 GitHub

你可能想知道为什么我在每一节的结尾都做 Git 提交。那是因为我现在要把项目推上 GitHub 了。

我将登录我的 GitHub 帐户，选择右上角我的头像旁边的加号`+`图标，然后选择 **[新仓库](https://github.com/new)** 。

在**存储库名称**中，我将添加项目名称`my-gatsby-blog`，但保留其余的默认值，并单击**创建存储库**。

下一个屏幕给出了将本地项目推送到 GitHub 所需的终端命令:

```
git remote add origin https://github.com/spences10/my-gatsby-blog
git branch -M main
git push -u origin main 
```

一旦你把这些都放入终端并点击`Enter`，刷新 GitHub 页面就可以看到新的项目了！

## 部署

是时候把这个宝贝放到网上了！有很多方法可以做到这一点。因为 Gatsby 构建了一个平面文件结构，所以您可以在任何可以访问 Internet 的文件服务器上托管一个 Gatsby 站点。

有许多服务在 CDN 上提供主机服务，很多是免费的！像 [Netlify](https://www.netlify.com/) 、 [Vercel](https://vercel.com/) 和 [Render](https://render-web.onrender.com/) 这样的服务将允许你通过 CLI、GitHub 集成，或者在 Netlify 的情况下，直接拖放，将你构建的网站推送到他们的 cdn！

### 维克塞尔

要使用 Vercel 进行部署，您需要一个 GitHub、GitLab 或 Bitbucket 帐户进行身份验证。然后会提示您安装 Vercel CLI:

```
yarn global add vercel 
```

我已经安装了它，所以现在的情况是运行 CLI 命令:

```
vc 
```

然后，系统会提示我设置和部署新项目。我将用`Enter`回答所有问题的默认:

```
Set up and deploy “~/repos/my-gatsby-blog”? [Y/n]
Which scope do you want to deploy to?
Link to existing project? [y/N]
What’s your project’s name? (my-gatsby-blog)
In which directory is your code located? ./
> Upload [====================] 99% 0.0sAuto-detected Project Settings (Gatsby.js):
- Build Command: `npm run build` or `gatsby build`
- Output Directory: public
- Development Command: gatsby develop --port $PORT
? Want to override the settings? [y/N] 
```

就是这样。然后给我一个部署 URL，在那里我可以在 Vercel 上查看站点的构建。

从 Vercel 仪表板，我可以配置域，如果我想，也可以从 Vercel 购买一个。我个人使用 Namecheap.com，但它是一个选项。

### Netlify

通过 CLI 使用 Netlify 进行部署与使用 Vercel 非常相似，但是我将进行拖放式创建。

为了认证，我需要 GitHub、GitLab、Bitbucket 或电子邮件帐户中的一个。一旦我验证并登录，我可以在菜单栏中选择**站点**，然后有一个拖放区**想要部署一个新站点而不连接到 Git？将你的站点输出文件夹拖放到这里**。我将在文件浏览器中导航到我的项目的根目录，并将`public`文件夹拖放到拖放区。

Netlify 将构建这些文件，并将它们部署到生成的 URL 以供检查。与 Vercel 非常相似，Netlify 将允许您在那里购买一个域并部署到其上。

### 提供；给予

Render 没有 CLI 或 drop 选项，而是使用 GitHub 集成。为了认证，我需要一个 GitHub，GitLab 或谷歌帐户。一旦我通过验证并登录，我就进入了服务区。从这里，我可以选择**新的静态站点**，然后输入我之前推送到 GitHub 的项目的 GitHub URL。

在下一页，我将对其进行如下设置:

*   **姓名** : `my-gatsby-blog`
*   **分支**:默认值
*   **构建命令** : `yarn build`
*   **发布目录** : `./public`

然后点击**创建静态站点**。

等待 Render 完成它的工作，然后单击项目名称下面的链接来实时查看站点。

Render 还可以选择为站点设置自己的自定义域！

## 可选 Gatsby 插件

有更多的 Gatsby 插件可供选择来增加额外的功能。如果你还想补充的话，我把这些留给你。例如:

*   要不要嵌入 YouTube 视频，推文，Strava runs，CoodePens，Codesandbox？查看[盖茨比-插件-mdx-嵌入](https://www.npmjs.com/package/gatsby-plugin-mdx-embed)。
*   你用的是谷歌/必应搜索控制台吗？然后你需要用 [gatsby-plugin-sitemap](https://www.npmjs.com/package/gatsby-plugin-sitemap) 生成一个站点地图。
*   您想让您的网站作为 PWA 脱机使用吗？添加[盖茨比-插件-离线](https://www.npmjs.com/package/gatsby-plugin-offline)。
*   你想在你的站点的浏览器标签中有一个 favicon 吗？检查一下[盖茨比插件清单](https://www.npmjs.com/package/gatsby-plugin-manifest)。

## 分析学

如果你想知道你的网站有多受欢迎，有分析选项。我在自己的项目中停止使用 Google Analytics 有一段时间了，现在我更喜欢更注重隐私的替代方案。我推荐的是 Fathom Analytics。(如果你想在第一个月的订阅中获得 10 美元的优惠，我有一个[会员链接](https://usefathom.com/ref/HG492L)。)

另一种选择是[似是而非的](https://plausible.io/privacy-focused-web-analytics)，我也听说过它的好处。

为了在 Gatsby 网站上实现 [Fathom Analytics，我需要在我的网站头部添加一个额外的脚本标签。那是什么意思？首先，我需要在我的 Fathom 仪表盘上创建网站，然后转到](https://usefathom.com/integrations/gatsbyjs)[https://app.usefathom.com/#/settings/sites](https://app.usefathom.com/#/settings/sites)，滚动到列表底部，添加我的新网站(`my-gatsby-blog`)，然后点击**获取网站代码**。然后我得到一个带有站点代码的弹出模式。我要把它加到我的盖茨比项目的剧本里。脚本看起来是这样的:

```
<script
  src="https://cdn.usefathom.com/script.js"
  data-spa="auto"
  data-site="ABCDEF"
  defer
></script> 
```

下面是`root-wrapper.js`的不同之处:

```
import { MDXProvider } from "@mdx-js/react";
import React from "react";
+import { Helmet } from "react-helmet"; import Layout from "./src/components/layout";
import RainbowText from "./src/components/rainbow-text";

const MDXComponents = {
 RainbowText, };

export const wrapPageElement = ({ element }) => {
 return ( + <> + <Helmet> + <script + src="https://cdn.usefathom.com/script.js" + spa="auto" + data-site="ABCDEF" + defer + ></script> + </Helmet>  <Layout>
        <MDXProvider components={MDXComponents}>{element}</MDXProvider>
 </Layout> + </>  ); }; 
```

## 包装！

我就说这些。非常感谢你坚持到最后。🙏

我希望你从这本关于从头开始建立一个 Gatsby 项目的内容丰富的指南中得到你所需要的东西！

如果你想和我打招呼，最好的联系方式是在推特上。

## 分享这篇文章*