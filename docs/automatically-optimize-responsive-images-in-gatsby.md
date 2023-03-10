# 如何自动优化《盖茨比》中的回应型形象

> 原文：<https://www.sitepoint.com/automatically-optimize-responsive-images-in-gatsby/>

图像优化——至少以我的经验来看——一直是构建高速网站的一大难题。没有合适的工具，平衡图像质量和带宽效率是一项艰巨的任务。Photoshop 之类的照片编辑工具非常适合修饰、裁剪和调整位图图像的大小。不幸的是，他们并不擅长为网络创建 100%优化的图像。

幸运的是，我们有构建工具的扩展包，可以为我们快速优化图像:

*   **大口** : [大口-图像民](https://www.npmjs.com/package/gulp-imagemin)
*   **咕噜声** : [咕噜声-图像 min](https://www.npmjs.com/package/grunt-image)
*   **网页包** : [影像 min 网页包](https://www.npmjs.com/package/imagemin-webpack)
*   **包裹** : [包裹-插件-图像敏](https://www.npmjs.com/package/parcel-plugin-imagemin)

不幸的是，只有图像优化是不够的。你需要确保整个网站反应灵敏，在所有屏幕尺寸下看起来都很棒。这可以通过 CSS 很容易地完成，但问题就在这里:

你应该为大屏幕还是小屏幕优化你的图像？

如果你的大多数观众使用移动设备访问你的网站，那么合理的选择是为小屏幕优化图像。然而，很可能一个重要的收入来源是来自 17 英寸以上大屏幕的游客。你肯定不想忽视他们。

幸运的是，我们有技术可以为不同的屏幕尺寸提供优化的响应图像。这意味着我们需要生成多个具有不同分辨率的优化图像，以适应特定的屏幕尺寸或响应断点。

对于 WordPress 网站所有者来说，这种图片优化需要使用插件和第三方服务。这些响应图像的创建不能在主机服务器上完成，否则会显著降低用户访问网站的速度，因此需要第三方服务。

如果你用盖茨比来经营你的网站，那么你很幸运。此功能是内置的，并且已经为您进行了配置，以优化您的响应图像。你只需要放入一些图片，写一点代码，将你的响应图片和你的网页连接起来。当您运行`gatsby build`命令时，图像会为您优化。这使您无需要求第三方服务来为您执行优化。它只是在您的部署机器上完成。

在接下来的章节中，我们将学习:

*   《盖茨比》中的形象优化
*   如何优化网页上的图像
*   如何优化降价帖子中的图片

## 先决条件

在我们开始之前，我想指出的是，本教程是为那些刚刚开始使用 Gatsby 并且希望专门学习如何处理图像的开发人员准备的。我假设您已经在以下主题上打下了良好的基础:

*   [反应](https://www.sitepoint.com/javascript/react/)
*   GraphQL

本教程不涵盖盖茨比的初学者概念——我们在这里有[一本《盖茨比入门指南》](https://www.sitepoint.com/how-to-build-your-first-static-site-with-gatsby/)。解决了这个问题，就可以进入下一部分来设置我们的演示项目了。您可以在此查看已完成的源项目[。](https://github.com/brandiqa/gatsby-image-demo)

## 演示项目设置

假设您的系统上已经安装了 Node.js 的最新版本，让我们快速设置一个 Gatsby starter 项目:

```
npm install -g gatsby-cli
gatsby new gatsby-image-demo
cd new gatsby-image-demo
npm start 
```

这个起始项目包括创建和呈现响应性优化图像所需的必要依赖项和配置。如果您使用了不同的起始项目，或者您喜欢从一个完全空白的项目开始，那么您需要做以下事情:

```
npm install gatsby-image gatsby-transformer-sharp gatsby-plugin-sharp gatsby-source-filesystem 
```

接下来，您需要在`gatsby-config.js`中配置依赖关系，如下所示:

```
plugins:[
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/src/images`,
        name: 'images'
      }
    },
    'gatsby-transformer-sharp',
    'gatsby-plugin-sharp',
] 
```

如果你打开`http://localhost:8000/`，你应该有 **Gatsby 默认启动**页面，上面有一个优化的宇航员 PNG 图像。请随意查看项目代码，看看图像是如何加载和渲染的。

看起来很复杂，对吧？在下一节中，我们将看看这是怎么回事。

## 解读《盖茨比》中的意象优化

使用 React 组件 [**Gatsby Image**](https://www.gatsbyjs.org/packages/gatsby-image/) 在网页上呈现优化的响应式图像。看起来是这样的:

```
import Image from 'gatsby-image';

<!--  Fixed Image Example -->
<Image fixed={fixed} />

<!--  Fluid Image Example -->
<Image fluid={fluid} /> 
```

如上面的代码示例所示，`gatsby-image`组件设计用于处理两种类型的图像:

*   **固定**:宽度和高度固定的图像
*   **流体**:具有最大宽度和高度的图像

如果你想显示视网膜图像，固定是有用的。请注意，如果浏览器窗口的大小小于图像宽度，将会出现滚动条。对于**流体**，图像将根据浏览器窗口大小自动调整大小。更小或更大的图像将自动交换以适应设定的视窗。

既然我们已经讨论了渲染，那么如何向`gatsby-image`组件提供图像数据呢？

我们使用 **GraphQL** 来加载一个图像，以便在网页上使用。这种查询语言允许我们从本地文件系统、WordPress 站点或自定义 API 访问图像。您将需要一个特殊的插件来访问特定的位置类型:

*   [盖茨比源文件系统](https://www.gatsbyjs.org/packages/gatsby-source-filesystem)
*   [盖茨比 WordPress 来源](https://www.gatsbyjs.org/packages/gatsby-source-wordpress)
*   [Gatsby 源码自定义 API](https://www.gatsbyjs.org/packages/gatsby-source-custom-api)

GraphQL 不仅获取资产，还能够在将它们返回到调用函数之前对它们进行处理。在图像优化的情况下，我们正在处理以下插件:

*   [盖茨比外挂夏普](https://www.gatsbyjs.org/packages/gatsby-plugin-sharp/)
*   [盖茨比变压器夏普](https://www.gatsbyjs.org/packages/gatsby-transformer-sharp/)

Gatsby 插件 Sharp 是一个底层助手插件，它实际上是在不损失图像质量的情况下减小图像尺寸。它使用[夏普图像处理库](https://github.com/lovell/sharp)来执行这项任务。对于 JPEGs，它生成默认质量级别为 50 的渐进图像。对于 PNGs，它使用质量设置为 50-75 的 [pngquant](https://github.com/pornel/pngquant) 库。

Gatsby Transformer Sharp 插件负责创建响应图像。换句话说，它执行调整大小和裁剪功能，以生成不同分辨率的图像，从而在移动设备、平板电脑和大屏幕设备上实现最佳显示。

在下一节中，我们将看看上述技术的实际用法。

## 优化网页上的图像

让我们首先将一些图像放到`src/images`文件夹中:

![01-source-images](img/24128c082e5d381b4dfc3da505b89b01.png)

你可以随意使用硬盘上或互联网上的任何图片。如果你打算使用高分辨率的 DSLR 照片，我建议你至少把大小降低到 700kb 以下。使用大图像会不必要地延长构建优化过程，并且会增大项目存储库的大小。

接下来，让我们弄清楚将用于查询响应优化图像的 GraphQL 查询。在浏览器中打开`http://localhost:8000/___graphql`，启动 GraphQL 浏览器和查询界面。在资源管理器面板上，记下我们可用的所有节点。在我们的例子中，我们只对`file`和`childImageSharp`节点感兴趣。下面是我构建的一个简单查询。资源管理器面板将列出可用于定义查询的所有参数和节点:

![02-graphql-explorer](img/0ea06b78514094dbdbfc4776bb38c254.png)

现在我们已经定义了一个 GraphQL 查询，让我们创建一个新页面，比如说`grado.js`。在下面的代码示例中，我们将渲染`fixed`和`fluid`图像。然而，对于查询部分，我们将使用`GatsbyImageSharpFluid`和`GatsbyImageSharpFluid`查询片段，而不是列出所有需要的子节点(即`src`、`sizes`、`srcSet`等)。请注意，GraphQL 查询浏览器还不支持**查询片段**。

```
import React from "react"
import Image from 'gatsby-image';
import { graphql } from "gatsby"

import Layout from "../components/layout"
import SEO from "../components/seo"

const GradoPage = ({data}) => (
  <Layout>  <SEO title="Grado" />  <h1>Grado Headphones ShowCase</h1>  <h3>Fluid</h3>  <Image fluid={data.gradoFluidImage.childImageSharp.fluid} />  <br/>  <h3>Fixed</h3>  <Image fixed={data.gradoFixedImage.childImageSharp.fixed} />  <p>Grado Rs2e</p>  </Layout>
)

export default GradoPage

export const pageQuery = graphql` query {
    gradoFluidImage: file(relativePath: { eq: "grado-rs2e.jpg" }) {
      childImageSharp {
        fluid(maxWidth: 1000) {
          ...GatsbyImageSharpFluid
        }
      }
    }
    gradoFixedImage: file(relativePath: { eq: "grado-rs2e.jpg" }) {
      childImageSharp {
        fixed(width: 600, height: 401) {
          ...GatsbyImageSharpFixed
        }
      }
    }
  } ` 
```

假设 Gatsby 仍在运行，导航到`localhost:8000/grado`:

![03-fixed-vs-fluid](img/9daa7abbf6abe0956fd6246bd494ede5.png)

上面的例子将向你展示流动图像和固定图像之间的视觉差异。流体图像将始终适合容器宽度，而固定图像将保持静态，无论视口大小如何。

在下一个代码示例中，我们将看看如何在同一页面上列出多个`fluid`图像:

```
const GradoPage = ({data}) => (
  <Layout>  <SEO title="Grado" />  <h1>Grado Headphones ShowCase</h1>  <h3>Grado</h3>  <Image fluid={data.grado.childImageSharp.fluid} />  <br/>  <h3>Grado Boxed</h3>  <Image fluid={data.gradoBox.childImageSharp.fluid} />  <br/>  <h3>Grado Mounted</h3>  <Image fluid={data.gradoMounted.childImageSharp.fluid} />  </Layout>
)

export default GradoPage

export const pageQuery = graphql` query {
    grado: file(relativePath: { eq: "grado-rs2e.jpg" }) {
      childImageSharp {
        fluid(maxWidth: 1000) {
          ...GatsbyImageSharpFluid
        }
      }
    }

    gradoBox: file(relativePath: { eq: "grado-rs2e-box.jpg" }) {
      childImageSharp {
        fluid(maxWidth: 1000) {
          ...GatsbyImageSharpFluid
        }
      }
    }

    gradoMounted: file(relativePath: { eq: "grado-rs2e-mounted.jpg" }) {
      childImageSharp {
        fluid(maxWidth: 1000) {
          ...GatsbyImageSharpFluid
        }
      }
    }
  } ` 
```

页面应该会自动刷新。您应该会看到页面上显示的所有图像。如果你尝试直接从浏览器下载其中一张图片，你会发现它的尺寸已经缩小了。在我的例子中，如果我把浏览器缩小到最小的宽度,“Grado Box”图像就会减少到 19.5 KB。当我在 17 英寸 1920×1080 的屏幕上最大化浏览器时，图像大小增加到 60.1 KB，看起来仍然很清晰。考虑到我放在`images`文件夹中的源图像重量为 618KB，分辨率为 2500x1800px，这些数字相当惊人。

您可能已经注意到这个查询看起来是多余的。我们可以通过创建自己的查询片段来简化，如下所示:

```
export const fluidImage = graphql` fragment fluidImage on File {
    childImageSharp {
      fluid(maxWidth: 1000) {
        ...GatsbyImageSharpFluid
      }
    }
  } `;

export const pageQuery = graphql` query {
    grado: file(relativePath: { eq: "grado-rs2e.jpg" }) {
       ...fluidImage
    }

    gradoBox: file(relativePath: { eq: "grado-rs2e-box.jpg" }) {
       ...fluidImage
    }

    gradoMounted: file(relativePath: { eq: "grado-rs2e-mounted.jpg" }) {
       ...fluidImage
    }
  } ` 
```

## 优化降价帖子和页面中的图像

有两种方法可以优化 Markdown 帖子和页面中的图像:

### 1.特色图片

特色图片通常放在元数据部分。您只需要指定一个名为`featuredImage`的字段，就像这样:

```
---
title: First Post
featuredImage: ./first-post-image.png
---

Place content here 
```

接下来，您需要处理 Markdown 模板文件中的`featuredImage`,如下所示:

```
//src/templates/blog-post.js
---
export const query = graphql` query PostQuery($slug: String!) {
    markdownRemark(fields: { slug: { eq: $slug } }) {
      html
      frontmatter {
        title
        featuredImage {
          childImageSharp {
            fluid(maxWidth: 800) {
              ...GatsbyImageSharpFluid
            }
          }
        }
      }
    }
  } ` 
```

您还需要导入`gatsby-image`包，以便呈现优化的响应图像:

```
//src/templates/blog-post.js

import Img from "gatsby-image"

export default({data}) {
  let post = data.markdownRemark
    let featuredImgFluid = post.frontmatter.featuredImage.childImageSharp.fluid
    return(
        <Layout>  <div>  <h1>{post.frontmatter.title}</h1>  <Img fluid={featuredImgFluid} />  <div dangerouslySetInnerHTML={{ __html: post.html }} />  </div>  </Layout>
    )
} 
```

就是这样。你所有的降价帖子都会有针对响应屏幕优化的`featuredImage`字段。

### 2.内嵌图像

对于 Markdown 帖子和页面中使用的内嵌图像，您只需安装以下插件:

```
npm install gatsby-remark-images 
```

你还需要安装`gatsby-plugin-sharp`和`gatsby-source-filesystem`。接下来，您需要如下配置`gatsby-config.js`中的`gatsby-remark-images`:

```
module.exports = {
  plugins: [
    `gatsby-plugin-sharp`,
    {
      resolve: `gatsby-plugin-mdx`,
      options: {
        gatsbyRemarkPlugins: [
          {
            resolve: `gatsby-remark-images`,
            options: {
              maxWidth: 1200,
            },
          },
        ],
      },
    },
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/src/pages`,
      },
    },
  ],
} 
```

在你的 Markdown 帖子和图片中，你可以使用默认的语法来渲染图片。优化将自动为您完成:

```
![Post image](./my-first-post-image.png) 
```

## 摘要

我希望你现在明白如何优化 Gatsby 站点的图片。有几个场景我们在这里没有涉及到——你可以在相关的 Gatsby 包页面上读到更多关于它们的内容:

*   [如何优化背景图像](https://www.gatsbyjs.org/packages/gatsby-background-image/)
*   [如何优化远程图像](https://www.gatsbyjs.org/packages/gatsby-plugin-remote-images/)

## 分享这篇文章