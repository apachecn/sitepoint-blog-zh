# Next.js 中路由的初学者指南，带示例

> 原文：<https://www.sitepoint.com/routing-in-next-js-beginner-guide/>

使用 Next.js 有很多好处，但一个非常有用的特性是它的文件路由系统。这种架构极大地简化了在网站内创建路线的过程。在本教程中，我们将学习如何建立一个 Next.js 项目，以及 Next.js 中的文件路由系统是如何工作的。

我们还将学习如何:

*   创建静态和动态页面
*   用`Link`实现页面过渡，以及它的一些道具
*   使用`useRouter()`钩子从 URL 获取查询参数
*   动态嵌套路由

…还有更多。

我们将通过建立一个作品集页面来了解所有这些。

## Next.js 功能

Next.js 是一个基于 React 的 web 框架，构建在 Node.js 之上，由于它基于 React，所以也遵循了组件架构设计。

Next.js 可以用来构建**静态站点**。这些网站的页面会在构建时预先呈现并提供给用户。简单地说，页面甚至在用户请求之前就已经存在了。

它还允许使用服务器端呈现来生成动态网页(每次用户发出新请求时都会改变的页面)。

Next.js 中的路由架构使得创建和链接页面变得非常容易。我们可以动态传递查询参数，创建动态页面，而不必离开`pages`文件夹。

### 为什么使用 Next.js

Next.js 框架于 2016 年 10 月 25 日首次发布。从那以后，由于几个原因，它成为了最流行的 web 框架之一。

首先，Next.js 基本上是 React。对于来自 React 背景的开发人员来说，这是一个好消息。开发人员仍然可以利用一些 React 特性，比如组件架构、JSX 等等。

其次是使用 Next 来预渲染页面的能力。默认情况下，Next.js 预先生成所有页面，然后在每个用户请求时重用这些页面。由于网站是预先生成的，搜索引擎爬虫可以为 SEO 正确地索引网站。

如前所述，Next.js 中的文件路由系统是一个非常有用的特性，它极大地简化了在网站中创建路由的过程。所以你基本上可以在一个名为`pages`的文件夹中创建一堆`.js`文件，Next.js 将为你的所有路线使用这些文件。它不仅有用，而且非常强大。

### 该项目

该站点将有两条简单路线和两条动态路线(我们稍后将了解它们的含义)。

页面将呈现一个项目列表。点击**查看更多**，我们将被引导至一个项目页面。

页面将呈现一个博客文章列表，我们也可以通过点击**阅读更多**来查看单个博客页面。该页面将包含一个特定职位的详细信息。

为了在接下来演示**路由嵌套**，我们还将为每个博客文章创建一个嵌套的`/comments`路由。例如，我们可以通过访问`localhost:3000/blog/first-post/comments`来查看第一篇文章的评论。

下面是该项目的现场预览:

[https://codesandbox.io/embed/next-portfolio-forked-ww4b9?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/next-portfolio-forked-ww4b9?fontsize=14&hidenavigation=1&theme=dark)

你可以从它的 [GitHub 库](https://github.com/sitepoint-editors/next-portfolio) 中获取代码，在你的机器上运行它，然后随意调整它。您可以删除我的图像，并将您的图像移至`/public`文件夹。您只需要更改标记中的文件名。

## 入门指南

要开始使用 Next，您需要在计算机上安装 Node.js。节点的版本不能低于`12.x`。您可以通过在命令终端键入`node -v`来检查版本。

如果你没有安装 Node.js，或者有旧版本，可以从[这里](https://nodejs.org/)下载最新版本。

下载完成后，我们需要初始化我们的项目。我们可以自动或手动完成这项工作。在本教程中，我们将使用`create-next-app`代码生成器为我们自动构建一个工作框架。

请导航到您希望项目所在的文件夹，并键入以下命令:

```
cd your/path
npx create-next-app next-portfolio
```

最后，运行以下命令:

```
npm run dev
```

如果一切顺利，您应该会在终端窗口上看到以下内容。

![Project all set](img/de0ec67153de745b3b21d4f9191ad7b0.png)

我们可以在网页浏览器上查看页面，网址为 [http://localhost:3000](http://localhost:3000) 。

![Next project now live](img/a769e28b352a793bfede6d03f19bbb44.png)

## Next.js 中基于文件的路由架构

当我们运行该命令时，我们在当前目录中创建了一个名为`next-portfolio`的文件夹。在`next-portfolio`里面，你会发现一些重要的文件夹和文件。

![App directory structure](img/e4c69440ba89a2b205edcf59f5ead4b4.png)

我们最常使用的文件夹是`pages`。接下来，`pages`中定义的每个`.js`文件都映射到一个类似命名的路由:

*   `pages/about.js`将映射到`/about`
*   `pages/contact.js`将映射到`/contact`
*   `pages/blog.js`将映射到`/blog`

以下是典型 Next 项目中 pages 文件夹的高级表示:

```
my-site

└── pages

    └── api // API routes

    ├── _app.js // custom route (will **not** be used as a route)

    ├── index.js // index route (will be rendered at my-site.com)

    ├── about.js // predefined route (will be rendered at my-site.com/about)

    ├── contact.js // predefined route (will be rendered at my-site.com/contact)

    └── blog

        ├── index.js // index route for blog (will be rendered at my-site.com/blog) 

        ├── author.js // predefined route (will be rendered at my-site.com/blog/author)

        ├── [blog-id].js // handles dynamic route (will render dynamcially, based on the url parameter)

        └── [...slug].js // handles catch all route (will be rendered at all routes following my-site.com/blog)
```

每个 React 组件将被捆绑成一个`.js`文件，包含每个页面的标记和逻辑。

### 公共文件夹

Next.js 提供了一个公共文件夹，您可以在其中存储静态资产，如图像、自定义脚本和字体，并从您的组件/代码中引用它们。

我们将在投资组合网站的各个页面中使用以下图片:

*   个人照片。这将在主页上使用(`index.js`)。
*   四个社交媒体图标。这将用于联系页面(`contact.js`)。

### 自定义页面

您可能已经注意到 pages 文件夹中的页面`_app.js`。此页面是自定义页面。定制页面被 Next.js 用作路由的是*而不是*，它们的前缀是下划线(`_`)。

Next.js 使用`_app.js`来初始化网页。这个组件初始化 app，并向下传递`pageProps` prop，这是我们网站中所有嵌套组件需要的数据。

作为根组件，我们可以定义一个希望在所有页面中持久化的布局。

我们还可以使用适用于所有元素的全局样式表，如下例所示:

```
//next-portfolio/pages/_app.js

import Layout from '../components/Layout'
import '../styles/globals.css'

function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  ); 
}

export default MyApp
```

### 索引路线

每当我们导航到诸如`my-site.com`、`my-site.com/blog`或`my-site.com/projects`之类的索引路径(即主页)时，Next.js 将从名为`index.js`的目录中读取任何文件。

所以本质上，`pages/index.js`返回主页的标记，显示在`localhost:3000`。`pages/blog/index.js`返回博客主页的标记，在`localhost:3000/blog`。

在您的代码编辑器中，请转到索引页面并删除所有文件内容。以下标记用于测试目的:

```
// next-portfolio/pages/index.js

import Image from 'next/image'
import Link from 'next/link'

export default function Home() {
  return (
    <div className="container">
      <h1>Hello World</h1>
    </div>  
    )
}
```

*注意:在你的文本编辑器中移动到`next-portfolio/styles`，删除`Home.module.css`，因为我们根本不需要它。*

现在保存文件更改并打开 [http://localhost:3000](http://localhost:3000) 。索引文件中的更改将反映在索引路线上。

![Hello world](img/5b4a7ddf3f5c8ed93c5b8dcf5f4e1c0e.png)

更多的信息将被放入`index.js`文件。主页的顶部将包含导航链接。但是，在主页之前建立其他页面更直观，因此我们可以适当地链接它们。

因此，在构建主页之前，我们需要先创建一些其他页面。

### 静态路由

首先，我们将为我们的投资组合站点创建两条**静态路由**。这些路由呈现**静态数据**:它们不使用来自 URL 的查询参数来呈现数据。

我们将创建的两条静态路由是`about.js`和`contact.js`。这些文件将分别用于`/about`和`/contact`路线。

为此，导航到`next-portfolio/pages`并创建一个名为`about.js`的新文件。“关于”页面的标记将放入其中:

```
// next-portfolio/pages/About.js

export default function About() {
    return (
        <div className="container">
            <h1> About me </h1>
            <p> My name is Kingsley Ubah. I'm a 22-year-old web developer from Nigeria. I'm particularly interested in technical writing. When I'm not coding or writing, I read my favorite books and play some cool video games. I'm a huge fan of good movies and football. Also, don't play with my food!</p>
            <p>I'm skilled in front-end web development. I'm equally good at the back end. Technologies I work well with include React, Node.js, Vue, Next, Gatsby, OAuth, MongoDB, MySQL and many others. </p>
            <p>I could keep going on and on about my life but I can guarantee that you'll be bored in the end. So I'll just end it right here.</p>
        </div>
    )
}
```

*注:当然，你也可以根据自己的喜好将内容定制成自己的一套技能！*

现在请保存文件更改，转到`next-portfolio/styles/globals.css`并输入以下样式:

```
@import url('https://fonts.googleapis.com/css2?family=Lato:wght@300&display=swap');

html,
body {
  padding: 0;
  margin: 0;
  font-family: "lato", sans-serif;
  font-size: 20px;
  background-color: #D7E5f0;  
}

* {
  box-sizing: border-box;
}

h1 {
  font-size: 60px;
}

.logo {
  font-weight: 600;
  font-size: 30px;
}

p {
  font-size: 20px;
  font-weight: 600;
  line-height: 1.2;
}

a {
  text-decoration: none;
  color: black;
}

.container {
  margin: 0 auto;
  max-width: 1200px;
}
```

注意:再说一次，如果你想要一套不同的风格，就去城里吧。

保存更改。在您的网络浏览器上，请导航至[http://localhost:3000/about](http://localhost:3000/about)。

![The current About page](img/397a65595912a2e2393bb18aec308a53.png)

最后，对于静态页面，请在`pages`中创建一个`contact.js`文件，并创建`Contact`组件，如下所示:

```
// next-portfolio/pages/Contact.js

import Image from 'next/image'

export default function Contact() {
    return (
        <div className="container">
            <h1> Contact me </h1>
            <p> I'd love to hear from you. Want to reach out, you can contact me on the 
                following profiles</p>
            <ul className="contact">
                <div className="link">
                    <li>
                        <Image src='/facebook.png' height={20} width={20} /> 
                        <a href='https://facebook.com/UbahTheBuilder'> Like me on Facebook</a>
                      </li>
                </div>
                <div className="link">
                    <li>
                        <Image src='/twitter.png' height={20} width={20} /> 
                        <a href='https://twitter.com/UbahTheBuilder'> Follow me on Twitter</a>
                    </li>
                </div>
                <div className="link">
                    <li>
                        <Image src='/linkedin.png' height={20} width={20} /> 
                        <a href='https://linkedin.com/UbahTheBuilder'> Connect with me on LinkedIn</a>
                    </li>
                </div>
                <div className="link">
                    <li>
                        <Image src='/whatsapp.png' height={20} width={20} /> 
                        <a href='https://whatsapp.com/UbahTheBuilder'> Chat with me on Whatsapp</a>
                      </li>
                </div>
            </ul>

            <form>
                <input type="text" placeholder="your name" /> 
                <br />
                <input type="email" placeholder="your email address"  /> 
                <br />
                <input type="text" placeholder="subject" /> 
                <br />
                <textarea id="message" rows="15" cols="65" placeholder="your message"></textarea> 
                <br />
                <input type="submit" value="Reach out" />
            </form>
        </div>
    )
}
```

从这个组件中，我们返回一个包含社交媒体链接的页面，以及一个联系表单。

对于链接，你会注意到我们导入并使用了由 [next/image](https://nextjs.org/docs/api-reference/next/image) 提供的`Image`组件。

`Image`组件有助于创建更好的优化和响应图像，这些图像随着浏览器窗口的大小而缩放。

为了更好地设置样式，可以随意复制以下样式并粘贴到全局样式表中:

```
/* next-portfolio/styles/globals.css */

/* CONTACT PAGE */
.link {
  width: 500px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 5px 0;
  font-size: 17px;
}

input {
  height: 50px;
  width: 500px;
  margin: 10px 0;
  font-size: 17px;
  padding-left: 3px;
}

input[type=submit] {
  background-color: blue;
  color: white;
  border: none;
}
```

请保存文件更改并导航到[http://localhost:3000/contact](http://localhost:3000/contact)。

![Our current Contact page](img/3e62fc91a55cb77abddb105fa9796fbb.png)

### 客户端路由转换

构建页面是一个过程。用户还必须能够在这些页面之间导航。已经实现了四个页面中的两个，现在让我们完成主页。首先，我们转到索引文件并修改`Home`组件，如下所示:

```
// pages/index.js`

import Image from 'next/image'
import Link from 'next/link'

export default function Home() {
  return (
    <div className="container">
      <div className="navbar">
        <div className="logo">Pragmatic Developer</div>
        <ul>
          <li>
            <Link href="/about">
              <a>About me</a>
            </Link>
          </li>
          <li>
            <Link href="/contact">
              <a>Contact me</a>
            </Link>
          </li>
          <li>
            <Link href="/blog">
              <a>Blog</a>
            </Link>
          </li>
          <li>
            <Link href="/projects">
              <a>Projects</a>
            </Link>
          </li>
        </ul>
      </div>
      <div className="profile">
        <Image src="/me.png" height={200} width={200} alt="My profile image" />        
          <div className="intro">
            <h1>Hi, I'm Kingsley</h1>
            <p>I am a web developer and technical writer</p>
        </div>
      </div>
    </div>
  )
}
```

如果你曾经在 React 应用程序中实现过客户端路由，你可能会熟悉来自 [React 路由器](https://github.com/remix-run/react-router)的 React 的`Link`组件。

Next.js 也为我们提供了一个类似的组件，我们是从 [next/link](https://nextjs.org/docs/api-reference/next/link) 导入的。

`<Link>`组件用于在下一个应用程序中实现页面转换。该组件最大的特点是允许您将查询参数传递给`useRouter`，这是您用来在动态路线上呈现内容的。

在 JSX 标记内部，我们注册了组件，并传入有效的`href`属性，指定我们想要从导航菜单链接到的页面。

该组件还可以接受几个属性，其中一些将在下面的部分中显示。

### `as`

有时您可能想要使用自定义 URL，这可能是为了使 URL 更具可读性和语义性。

为此，您可以将`as`属性传递给`Link`，就像这样:

```
<ul>
    <li>
        <Link href="/about" as="/king">
          <a>About me</a>
        </Link>
    </li>
    <li>
        <Link href="/contact">
            <a>Contact me</a>
        </Link>
    </li>
    <li>
        <Link href="/blog">
          <a>Blog</a>
        </Link>
    </li>
    <li>
      <Link href="/projects">
          <a>Projects</a>
      </Link>
    </li>
</ul>
```

![Image of as](img/0e7a5e0e4e1b47c27db37c54bb7ba4c1.png)

### `preFetch`

我确实提到过 Next.js 作为一个框架允许我们预渲染页面。该属性使我们能够预取在后台呈现“关于”页面所需的资源:

```
<Link href="/about" prefetch>
  <a>About me</a>
</Link>
```

现在保存文件。您可以在全局样式表中随意使用以下样式:

```
/* next-portfolio/styles/globals.css */

/* HOME PAGE */
.navbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.navbar ul {
  display: flex;
}

.profile {
  display: flex;
  max-width: 900px;
  margin: 180px auto;
}

li {
  list-style-type: none;
}

.navbar a {
  text-decoration: none;
  color: rgb(59, 58, 58);
  margin: 0 25px;
  transition: 0.2s;
}

.navbar a:hover {
  background-color: blue;
  color: white;
  padding: 8px 8px;
  border-radius: 6px;
}

.intro {
  margin: 0 90px;
}

.contact a {
  margin: 0 15px;
}
```

将样式保存到文件，并在 web 浏览器上导航到 [http://localhost:3000](http://localhost:3000) 。

![The current home page](img/e988380e7f2105c6a6bcc76002123cce.png)

点击导航菜单上的**联系人**，您会发现我们现在可以从主页转到联系人页面。

## 动态路线

在 Next.js 中，**动态路由**是根据来自 URL 的查询`id`动态呈现内容的特殊路由。

动态路由由特殊文件处理，用`[param].js`约定定义。`param`是从查询对象中获取的。

因此，与为不同的路由定义不同的文件不同，如:

*   `blog/first-post.js`为`/blog/first-post`
*   `blog/second-post.js`为`/blog/second-post`
*   `blog/third-post.js`为`/blog/third-post`

…我们可以定义一个单独的**动态页面**来处理`/blog`中的任何动态路线:

*   博客/[博客-id]。射流研究…

每当导航到上述任何 URL 时，例如:

```
<li><Link href="/blog/1"><a>Visit my first post</a></Link></li>

// 1 is the blog-id which will get sent to the dynamic component 
```

…在动态组件内部，我们可以从 URL 访问查询 ID(即 1、2、3、`first-post`等等)。

我们通过导入并调用 [useRouter()](https://nextjs.org/docs/api-reference/next/router) 钩子来实现。然后我们从`router`对象中解构`param`值，并基于此决定渲染什么。

*所以如果你从一个主页导航到`blog/1`，1 的`:id`可以这样得到:*

```
import {useRouter} from 'next/router'

export default function Blog() {
    const router = useRouter();
    const {id} = router.query;

return (
        <div className="container">
            <h1> You are now reading article {id} </h1> // You are now reading article 1
        </div>
    )

 }
```

您也可以使用查询字符串来代替完整的 URL 路径:

```
<li><Link href="/blog?title=my-first-post"><a>Visit my first post</a></Link></li>
```

*注意:通常，您会使用查询 ID 查询数据库，然后检索将显示在动态页面上的匹配数据记录。在本教程中，我将使用模拟 JSON 数据来简化一切。*

### 创建项目页面

第一个动态页面将为项目。

在`pages`中，创建一个名为`projects`的新文件夹。然后在新文件夹中，创建一个名为`index.js`的文件。

这个文件将返回我们在 web 浏览器上查看[http://localhost:3000/projects](http://localhost:3000/projects)时显示的内容。换句话说，这将是`/projects`的主页。

我们还需要一些用于项目的模拟 JSON 数据。在`pages`中，创建一个名为`projects.json`的文件。然后创建您自己的项目数组，如下所示:

```
// next-portfolio/pages/projects.json

[
    {
        "id": 1,
        "cover": "https://uploads.sitepoint.com/wp-content/uploads/2021/10/1633599028SkilllzLanding.png",
        "title": "Skilllz",
        "slug": "projects/first-project",
        "excerpt": "A Sleek Purple Landing Page For an online training platform. Learn some important CSS concepts by building a landing page"
    },
    {
        "id": 2,
        "title": "Movie Generator App",
        "cover": "https://uploads.sitepoint.com/wp-content/uploads/2021/10/1633599458moviegenerator.png",
        "slug": "projects/second-project",
        "excerpt": "Learn how to build CRUD applications with React and HarperDB. This in depth tutorials covers a lot about API integartion"
    },
    {
        "id": 3,
        "title": "Hacker News Clone",
        "cover": "https://uploads.sitepoint.com/wp-content/uploads/2021/10/1633599423hackernewsclone.png",
        "slug":  "projects/third-project",
        "excerpt": "Have you always wanted to clone a web page? Build a Hacker News Clone using React and HarperDB. Get started with it now"
    }
]
```

JSON 包含我们希望在[http://localhost:3000/projects](http://localhost:3000/projects)显示的项目数据。

之后，我们将这些数据放入标记中，就像这样:

```
// next-portfolio/pages/projects/index.js

import Portfolios  from '../projects.json'
import Link from 'next/link'

export default function Projects() {
    return (
        <div className="container">
            <h1> My Projects </h1>
            <div className="projects">
                    {Portfolios.map(portfolio => {
                        return(
                            <div className="project" key={portfolio.id}>
                                <img src={portfolio.cover} alt="project image" />
                                <h2>{portfolio.title}</h2>
                                <p>{portfolio.excerpt}</p>
                                <Link href={portfolio.slug}><a>View More</a></Link>
                            </div>
                        )}
                    )}
            </div>
        </div>
    )
}
```

我们做的第一件事是导入数据。然后我们使用 JavaScript `map()`函数将每个项目映射到 JSX 模板中。

我们还需要使它更像样，所以可以随意使用以下风格:

```
// next-portfolio/styles/globals.css

/* PROJECTS */
.projects {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
}

.project img {
  height: 100px;
  width: 200px;
}

.project a {
  color: white;
  background-color: black;
  padding: 10px 10px;
  border-radius: 6px;
}

.project {
  max-width: 500px;
  background-color: blue;
  border-radius: 6px;
  color: white;
  padding: 30px 30px;
  margin: 30px 0;
}
```

要在浏览器上查看页面，请导航到[http://localhost:3000/projects](http://localhost:3000/projects)。

![List of projects](img/9f0524b88efb5a01384df126a73a2988.png)

### 单个项目页面

现在，我们需要实现显示单个项目的动态路由。因此，如果我们导航到[http://localhost:3000/projects/1](http://localhost:3000/projects/1)，将显示第一个项目。

在`pages`的项目文件夹中，创建一个名为`[project].js`的新文件。

该文件将呈现单个项目的动态页面 a，如`projects/1`、`projects/2`等。

在该文件中，我们定义了将用于单个项目页面的模板，如下所示:

```
// next-portfolio/pages/projects/[project].js

import {useRouter} from 'next/router'

export default function Project() {
    const router = useRouter();
    const {project} = router.query;

    return (
        <div className="container">
            <div>
                   <h1>This is the {project}</h1> 
                   <p>Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
                   <p>Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
                   <p>Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
            </div>
        </div>
    )
}
```

*注意:从路由器对象中，我们从查询对象中获取查询 ID。通常，您会使用该键对匹配记录进行 API 查询。这样，如果在数据库中找不到匹配的项目，您也可以显示一条错误消息。*

因为我们没有 API 来查询项目，所以我们显示 URL slug 和一些静态的文本。slug 标识呈现的页面。

下图显示了页面如何根据 URL 进行更改。

![Single project page](img/fcb7b519c8789d1ed173717eec29047d.png)

## 嵌套路线

例如，让我们考虑一个博客。当用户导航到`my-site.com/blog`时，会显示一个博客文章列表。

当用户导航到`my-site/blog/first-post`时，显示第一篇博客文章。当他们导航到`my-site/blog/first-post/comments`时，将会出现与第一篇文章相关的所有评论。这叫做**路线嵌套**。

在 Next.js 中，还可以嵌套动态路由。每个子路由都可以访问父路由的查询`:id`。这样，`my-site.com/blog/first-post/comments`将与`my-site.com/blog/second-post/comments`不同，因为您可以使用`useRouter()`从 URL 或查询对象中检查帖子`:id`。

事实上，我们将对我们的博客页面做类似的事情。每篇博文都有自己的评论。换句话说，我们将在另一个名为`[blog].js`的动态页面中嵌套一个名为`[comments].js`的动态页面。

### 创建博客主页

在进入路由嵌套之前，我们将首先创建博客主页。

为此，cd 进入`next-portfolio/pages`并创建一个名为`blog`的文件夹。在新文件夹中，创建一个名为`index.js`的文件。

这个文件将返回在[http://localhost:3000/blog](http://localhost:3000/blog)显示的内容。换句话说，这是该路线的主页。

接下来，我们为博客文章创建数据:

```
// next-portfolio/pages/posts.json

[
    {
        "id": 1,
        "cover": "https://uploads.sitepoint.com/wp-content/uploads/2021/10/1633515082detectcanva.png",
        "title": "How to detect the operating system in React and Render accordingly",
        "slug": "blog/first-post",
        "excerpt": "Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
        "body": "Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat."
    },
    {
        "id": 2,
        "title": "Learn all about the JavaScript reduce method",
        "cover": "https://uploads.sitepoint.com/wp-content/uploads/2021/10/1633515150jsreduce.png",
        "slug": "blog/second-post",
        "excerpt": "Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
        "body": "Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat."
    },
    {
        "id": 3,
        "title": "Understanding React props",
        "cover": "https://uploads.sitepoint.com/wp-content/uploads/2021/10/1633515109react-props-2.png",
        "slug":  "blog/third-post",
        "excerpt": "Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
        "body": "Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Lorem Ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat."
    }
]
```

JSON 数组包含我们将在博客页面上呈现的博客文章。通常这些数据应该从 API 中获取，而不是存储在 JSON 对象中。

接下来，导入并在标记中使用它，如下所示:

```
// next-portfolio/pages/blog/index.js

import Posts from '../posts.json'
import Link from 'next/link'

export default function Blogs() {
    return (
        <div className="container">
            <h1> Latest Posts </h1>
            <div className="posts">
                    {Posts.map(post => {
                        return(
                            <div className="post" key={post.id}>
                                <img src={post.cover} />
                                <h2>{post.title}</h2>
                                <p>{post.excerpt}</p>
                                <Link href={post.slug}>
                                  <a>Read Post</a>
                                </Link>
                            </div>
                        )}
                    )}
            </div>
        </div>
    )
}
```

为了让页面看起来更好，这里有一些样式:

```
// next-portfolio/styles/globals.css

/* BLOG PAGE */
.posts {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 50px;
  max-width: 1200px;
  margin: 0 auto;
}

.post-container {
  margin: 15px auto;
  max-width: 900px;
}

.post-container img {
  width: 100%;
}

.post img {
  height: 300px;
  width: 500px;
}

.posts a {
  background-color: black;
  color: #D7E5f0;
  padding: 10px 10px;
  cursor: pointer;
  margin: 30px 0;
  border-radius: 6px;
}

.post {
  background-color: white;
  margin: 30px 0;
  padding: 30px 30px;
  border-radius: 6px;
}
```

现在请在您的网络浏览器上导航到[http://localhost:3000/blog](http://localhost:3000/blog)。

![Blog page](img/52b239104062b521213fa94847b03073.png)

### 显示单个帖子和嵌套的评论

在这一部分，我们将做两件事:

*   为单个博客文章创建页面
*   创建用于显示注释的动态嵌套路线

为此，进入`pages/blog`并创建一个名为`[blog]`的新文件夹。在该文件夹中，创建两个文件[index]。js 和【评论】。射流研究…

```
my-site

└── pages

    ├── index.js // index route (will be rendered at my-site.com)

    └── blog

        ├── index.js // list of blog post (my-site.com/blog) 

        └── [blog] 

            ├── [index].js // (eg: my-site.com/blog/first-post)

            ├── [comments].js // (eg: my-site.com/blog/first-post/comments) 
```

导航到[索引]。js 并键入以下代码:

```
import {useRouter} from 'next/router'
import Link from 'next/link'
import Posts from '../../posts.json'

export default function Blog() {
    const router = useRouter();
    const {blog} = router.query;
    const fullPath = blog+"/comments";

    if (blog === "first-post") {
    return (
        <div className="post-container">
            <div>
                    <img src={Posts[0].cover} alt="post image" />    
                   <h1> {Posts[0].title}</h1>
                   <p>{Posts[0].body}</p>
                   <p>{Posts[0].body}</p>
                   <p>{Posts[0].body}</p>
                   <hr />
                   <div className="comments">
                        <h3>Comments</h3>
                        <h5>Marina Costa</h5>
                        <p>Absolutely spot on! Thanks for sharing, Kingsley!</p>
                        <Link href={fullPath}>
                          <a>Read all comments for this article</a>
                        </Link>

                   </div>
            </div>
        </div>
    )
    } else if (blog === "second-post") {
        return (
        <div className="post-container">
            <div>
                    <img src={Posts[1].cover} alt="post image"/> 
                    <h1> {Posts[1].title}</h1>
                   <p>{Posts[1].body}</p>
                   <p>{Posts[1].body}</p>
                   <p>{Posts[1].body}</p>
                   <hr />
                   <div className="comments">
                        <h3>Comments</h3>
                        <p>Marina Costa</p>
                        <p>Absolutely spot on! Thanks for sharing, Kingsley!</p>
                        <Link href={fullPath}>
                          <a>Read all comments for this article</a>
                        </Link>

                   </div>
            </div>
        </div>
        )
    } else {
        return (
        <div className="post-container">
            <div>
                    <img src={Posts[2].cover} alt="post image"/> 

                   <h1> {Posts[2].title}</h1>
                   <p>{Posts[2].body}</p>
                   <p>{Posts[2].body}</p>
                   <p>{Posts[2].body}</p>
                   <hr />
                   <div className="comments">
                        <h3>Comments</h3>
                        <h5>Marina Costa</h5>
                        <p>Absolutely spot on! Thanks for sharing, Kingsley!</p>
                        <Link href={fullPath}>
                          <a>Read all comments for this article</a>
                        </Link>

                   </div>
            </div>
        </div>
        )}
}
```

请注意，在真实的项目中，您不需要基于`post :id`的`if`条件语句来呈现。这是因为你通常会将所有的帖子存储在一个数据库中。然后，在 API 中查询与查询 ID 匹配的文章。

这方面的代码如下所示:

```
import Link from 'next/link'

export default function Blog( {post} ) {

    return (
        <div className="post-container">
            <div>
                   <img src={posts.cover} alt="post image" />    
                   <h1> {post.title}</h1>
                   <p>{post.body}</p>
                   <hr />
                   <div className="comments">
                        <h3>Comments</h3>
                        <h5>{post.commenter}</h5>
                        <p>{post.featured_comment}</p>
                        <Link href={post.fullPath}>
                          <a>Read all comments for this article</a>
                        </Link>
                   </div>
            </div>
        </div>
    )}
}

export async const getStaticProps = ({ params }) => {
  const res = await fetch(`https://your-api.com/posts/${params.title}`);
  const post = await res.json();
    return {
      props: { post },
    };
}
```

观察我们如何消除对`useRouter()`的需求。这是因为`getStaticProps()`自动接受来自`param`对象的查询 ID，该对象是上下文对象的一部分。然后，从 API 中检索与该标题匹配的 post 对象，并将其作为`props`传递给`Blog`组件。

现在我们已经建立了获取外部数据的正确方法，是时候来看看单个帖子页面是什么样子了:[http://localhost:3000/blog/first-post](http://localhost:3000/blog/first-post)。

![First blog post](img/6c0d48baffd5fa1187c7fec00229d5bd.png)

### 来自注释的嵌套路由

你还记得我们之前创建的`[comments].js`文件吗？Next.js 会将此页面视为嵌套页面:

```
//next-portfolio/pages/blog/[blog]/[comments].js

import {useRouter} from 'next/router'

export default function Comments() {
    const router = useRouter();
    const {blog} = router.query;

    return (
        <div className="container">
            <div>
                    <h2> You are now reading the comments from the {blog} </h2>
                    <div className="comments">
                        <h3>Comments</h3>
                        <hr />
                        <h5>Marina Costa</h5>
                        <p>Absolutely spot on! Thanks for sharing, Kingsley!</p>
                        <hr />
                        <h5>Marina Costa</h5>
                        <p>Absolutely spot on! Thanks for sharing, Kingsley!</p>
                        <hr />
                        <h5>Marina Costa</h5>
                        <p>Absolutely spot on! Thanks for sharing, Kingsley!</p>
                        <hr />
                        <h5>Marina Costa</h5>
                        <p>Absolutely spot on! Thanks for sharing, Kingsley!</p>
                        <hr />
                        <h5>Marina Costa</h5>
                        <p>Absolutely spot on! Thanks for sharing, Kingsley!</p>
                        <hr />
                        <h5>Marina Costa</h5>
                        <p>Absolutely spot on! Thanks for sharing, Kingsley!</p>                    
                    </div>
            </div>
        </div>
    )
}
```

这是您在现实项目中通常会做的事情:

```
export default function Comments( {comments} ) {

    return (
        <div className="container">
            <div>
                    <h2> You are now reading the comments from the {blog} </h2>
                    <div className="comments">
                        {comments.map(comment => {
                        return(
                            <div className="comment" key={comment.id}>
                                <h5>{comment.name}</h5>
                                <p>{comment.body}</p>
                                <hr />
                            </div>
                        )}
                    )}              
                    </div>
            </div>
        </div>
    )
}

export async const getStaticProps = ({ params }) => {
  const res = await fetch(`https://jsonplaceholder.typicode.com/blog-comments/${params.title}`);
  const comments = await res.json();
    return {
      props: { comments },
    };
}
```

![Comments from first post](img/0cdcc693cc85dfdc3c3bdc3c6dc757fb.png)

### 包扎

js 中的页面路由是 Next 中需要了解的最重要的概念之一。这也是最强大的功能，因为您可以按照自己的意愿构建网站，并通过嵌套在路径之间传递数据。

在本教程中，我们通过构建一个简单的作品集网站，了解了 Next.js 中页面路由的实现。希望你觉得有用。如果您有任何反馈，请通过 [Twitter](https://twitter.com/UbahTheBuilder) 联系我。

## 分享这篇文章