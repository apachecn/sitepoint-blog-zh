# 如何在 Next.js 网站中使用 IP2Location 转移流量

> 原文：<https://www.sitepoint.com/ip2location-next-js-divert-traffic/>

*本文是与 [IP2Location](https://www.ip2location.com/) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

在这个在线商务已经成为常态的世界里，我们需要建立比以往任何时候都更快、更友好、更安全的网站。在本文中，您将了解如何建立一个 Node.js 支持的网站，该网站能够根据访问者的国家将流量导向相关的登录页面。您还将了解如何阻止匿名流量(例如 Tor ),以消除来自此类网络的风险。

为了实现这些特性，我们将使用由地理 IP 解决方案提供商 [IP2Location](https://www.ip2location.com/) 提供的 IP2Proxy web 服务。web 服务是一个 REST API，它接受 IP 地址并用 JSON 格式的地理位置数据进行响应。

![ip2location website](img/178d820c9c7fb1546dce292dd89a95fb.png)

以下是我们将收到的一些字段:

*   国家名称
*   cityName
*   isProxy
*   代理类型
*   等等。

我们将使用 [Next.js](https://nextjs.org/) 构建一个包含以下登录页面的网站:

*   **主页** : API 抓取和重定向将从该页面触发
*   **登录页面**:受支持的国家将会看到以当地货币显示的产品页面
*   **不可用页面**:其他国家将看到此页面，其中包含加入等候名单的选项
*   **滥用页面**:使用 Tor 网络的访问者将被带到该页面

现在你已经知道了项目计划，让我们看看你需要什么来开始。

## 先决条件

在您的机器上，我强烈推荐以下内容:

*   Node.js 的最新 LTS 版本(v12)
*   故事

Node.js 的旧版本可以，但最新的 LTS(长期支持)版本包含了异步代码领域的性能和调试改进，这是我们将要处理的。Yarn 不是必需的，但是如果您使用它，您将受益于它更快的性能。

我还假设你在以下方面有良好的基础:

*   [反应](https://www.sitepoint.com/learn/react/)
*   [反应钩子](https://www.sitepoint.com/react-hooks/)

如前所述，我们将使用 Next.js 来构建我们的网站。如果你是新手，可以跟随他们的官方[互动教程](https://nextjs.org/learn/basics/getting-started)快速上手。

## IP2Location + Next.js 项目演练

### 项目设置

要设置项目，只需启动终端并导航到您的工作区。执行以下命令:

```
npx create-next-app 
```

随意给你的应用起任何名字。我把我的名字叫做`next-ip2location-example`。安装完成后，导航到项目的根目录并执行`yarn dev`。这将启动 Node.js 开发服务器。如果你打开浏览器并导航到`localhost:3000`，你应该会看到一个标题为“欢迎来到 Next.js”的页面。这应该可以确认我们有一个运行正常的应用程序。停止应用程序并安装以下依赖项:

```
yarn add yarn add next-compose-plugins dotenv-load next-env @zeit/next-css bulma isomorphic-unfetch 
```

我们将使用[布尔玛 CSS 框架](https://bulma.io/)为我们的网站添加开箱即用的样式。因为我们将连接到一个 API 服务，所以我们将建立一个`.env`文件来存储我们的 API 密钥。请注意，该文件不应该存储在存储库中。接下来，在项目的根目录下创建文件`next.config.js.`,并添加以下代码:

```
const withPlugins = require("next-compose-plugins");
const css = require("@zeit/next-css");
const nextEnv = require("next-env");
const dotenvLoad = require("dotenv-load");

dotenvLoad();
module.exports = withPlugins(nextEnv(), css); 
```

上面的配置允许我们的应用程序读取`.env`文件并加载值。请注意，为了在服务器环境中加载，密钥需要有前缀`NEXT_SERVER_`。更多信息请访问 [next-env](https://www.npmjs.com/package/next-env) 包页面。我们将在下一节中设置 API 键。上述配置也给了我们的 Next.js 应用程序通过`zeit/next-css`包预处理 CSS 代码的能力。这将允许我们在应用程序中使用布尔玛 CSS 框架。请注意，我们需要将布尔玛 CSS 代码导入到我们的 Next.js 应用程序中。我很快会告诉你在哪里这样做。

### 获取 IP2Proxy Web 服务的 API 密钥

如前所述，我们需要将访问者的 IP 地址转换成我们可以用来重定向或阻止流量的信息。只需点击以下链接，注册一个免费试用密钥:

*   [IP 2 代理检测 Web 服务](https://www.ip2location.com/web-service/ip2proxy)

![ip2proxy trial key packages](img/9a68836a92439f505b2a5b1cd26e3874.png)

注册后，您将通过电子邮件收到免费的 API 密钥。创建一个`.env`文件，并将其放在项目文件夹的根目录下。将 API 密钥复制到文件中，如下所示:

```
NEXT_SERVER_IP2PROXY_API=<place API key here> 
```

这把免费钥匙会给你 1，000 免费信用点。至少，我们需要以下字段来运行我们的应用程序:

*   国家名称
*   代理类型

如果你看一下 IP2Proxy 页面上的定价部分，你会注意到,`PX2`包会给我们所需的响应。这意味着每个查询将花费我们两个信用点。以下是 URL 应该如何构建的示例:

*   `http://api.ip2proxy.com/?ip=8.8.8.8&key=demo&package=PX2`

您也可以提交没有 IP 的 URL 查询。该服务将使用发送请求的机器的 IP 地址。我们还可以使用`PX8`包来获取 IP2Proxy 检测 Web 服务最顶层包中的所有可用字段，如`isp`和`domain`。

*   `http://api.ip2proxy.com/?key=demo&package=PX8`

在下一节中，我们将构建一个简单的状态管理系统，用于存储将在所有站点页面之间共享的代理数据。

### 在 Next.js 中构建上下文 API

创建文件`context/proxy-context`并插入以下代码:

```
import React, { useState, useEffect, useRef, createContext } from "react";

export const ProxyContext = createContext();

export const ProxyContextProvider = (props) => {
  const initialState = {
    ipAddress: "0.0.0.0",
    countryName: "Nowhere",
    isProxy: false,
    proxyType: "",
  };

  // Declare shareable proxy state
  const [proxy, setProxy] = useState(initialState);
  const prev = useRef();

  // Read and Write Proxy State to Local Storage
  useEffect(() => {
    if (proxy.countryName == "Nowhere") {
      const localState = JSON.parse(localStorage.getItem("ip2proxy"));
      if (localState) {
        console.info("reading local storage");
        prev.current = localState.ipAddress;
        setProxy(localState);
      }
    } else if (prev.current !== proxy.ipAddress) {
      console.info("writing local storage");
      localStorage.setItem("ip2proxy", JSON.stringify(proxy));
    }
  }, [proxy]);

  return (
    <ProxyContext.Provider value={[ipLocation, setProxy]}>
      {props.children}
    </ProxyContext.Provider>
  );
}; 
```

基本上，我们声明了一个名为`proxy`的可共享状态，它将存储从 IP2Proxy web 服务中检索到的数据。API 获取查询将在`pages/index.js`中实现。这些信息将用于将访问者重定向到相关页面。如果访问者试图刷新页面，保存的状态将会丢失。为了防止这种情况发生，我们将使用`useEffect()`钩子在浏览器的本地存储中保存状态。当用户刷新特定的登录页面时，将从本地存储中检索代理状态，因此无需再次执行查询。下面是 Chrome 本地存储的一个快速预览:

![chrome local storage](img/067a255b5aa201308555e8cfad87e6d0.png)

提示:如果您在本教程中遇到问题，清除本地存储可以帮助解决一些问题。

### 显示代理信息

创建文件`components/proxy-view.js`并添加以下代码:

```
import React, { useContext } from "react";
import { ProxyContext } from "../context/proxy-context";

const style = {
  padding: 12,
};

const ProxyView = () => {
  const [proxy] = useContext(ProxyContext);
  const { ipAddress, countryName, isProxy, proxyType } = proxy;

  return (
    <div className="box center" style={style}>
      <div className="content">
        <ul>
          <li>IP Address : {ipAddress} </li>
          <li>Country : {countryName} </li>
          <li>Proxy : {isProxy} </li>
          <li>Proxy Type: {proxyType} </li>
        </ul>
      </div>
    </div>
  );
};

export default ProxyView; 
```

这只是一个显示组件，我们将把它放在每个页面的末尾。我们创建这个只是为了确认我们的获取逻辑和应用程序的状态是否按预期工作。你应该注意到，在我们在应用程序的根中声明了我们的`Context Provider`之前，行`const [proxy] = useContext(ProxyContext)`不会运行。让我们在下一节中开始吧。

### 在 Next.js 应用程序中实现上下文 API 提供程序

创建文件`pages/_app.js`并添加以下代码:

```
import React from "react";
import App from "next/app";
import "bulma/css/bulma.css";
import { ProxyContextProvider } from "../context/proxy-context";

export default class MyApp extends App {
  render() {
    const { Component, pageProps } = this.props;

    return (
      <ProxyContextProvider>
        <Component {...pageProps} />
      </ProxyContextProvider>
    );
  }
} 
```

`_app.js`文件是我们的 Next.js 应用程序的根组件，在这里我们可以与其余的站点页面和子组件共享全局状态。注意，这也是我们为之前安装的布尔玛框架导入 CSS 的地方。设置好之后，现在让我们构建一个用于所有站点页面的布局。

### 构建布局模板

在项目的根目录下创建文件夹`layout`。让我们把文件`components/nav.js`移到`layout/nav.js`。用以下代码替换当前代码:

```
import React from "react";
import Link from "next/link";

const Nav = () => (
  <nav className="navbar" role="navigation" aria-label="main navigation">
    <div className="navbar-brand">
      <a href="/" className="navbar-item">
        <strong>Product Store</strong>
      </a>
    </div>
    <div className="navbar-menu">
      <a className="navbar-item" href="/">
        Home
      </a>
      <Link href="/landing">
        <a className="navbar-item">Landing</a>
      </Link>
      <Link href="/unavailable">
        <a className="navbar-item">Unavailable</a>
      </Link>
      <Link href="/abuse">
        <a className="navbar-item">Abuse</a>
      </Link>
    </div>
  </nav>
);

export default Nav; 
```

请注意，这是一个不完整的导航菜单，因为它应该是完全响应的。请查看 [Navbar 文档](https://bulma.io/documentation/components/navbar/)以添加对平板电脑和移动屏幕的支持。

我还想指出的是，`Home`链接没有使用`Link`组件。我故意这样做，以便当用户点击它时，它将触发一个服务器 GET 请求。其余的链接将只执行客户端导航。

接下来，创建文件`layout/layout.js`并添加以下代码:

```
import Head from "next/head";
import Nav from "./nav";
import ProxyView from "../components/proxy-view";

const Layout = (props) => (
  <div>
    <Head>
      <title>IP2Location Example</title>
      <link rel="icon" href="/favicon.ico" />
    </Head>
    <Nav />
    <section className="section">
      <div className="container">
        {props.children}
        <ProxyView />
      </div>
    </section>
  </div>
);

export default Layout; 
```

现在我们已经定义了`Layout`，让我们开始构建我们的站点页面，从主页开始。

### 构建我们的主页

这是我们将为 IP2Proxy web 服务执行 API 获取查询的地方。我们将在`ProxyContext`状态中保存收到的响应。首先，我们将快速构建 UI。打开文件`pages/index.js`，用以下代码替换现有代码:

```
import Head from "next/head";
import Layout from "../layout/layout";

const Home = () => {
  return (
    <Layout>
      <Head>
        <title>Home</title>
      </Head>

      <section className="hero is-light">
        <div className="hero-body">
          <div className="container">
            <h1 className="title">Home</h1>
            <h2 className="subtitle">
              Checking availability in your country...
            </h2>
          </div>
        </div>
      </section>
    </Layout>
  );
};

export default Home; 
```

现在是使用命令`yarn dev`或`npm run dev`启动 Next.js dev 服务器的好时机。您应该得到以下输出:

![home page layout](img/068c949764f41305f48169af26de2fe5.png)

注意，`ProxyView`组件正在显示我们在`ProxyContextProvider`中设置的初始值。在下一节中，我们将执行一个获取操作并更新这些值。

### 在 IP2Proxy Web 服务上执行获取查询

在这一节中，我们将编写一个异步函数来执行 API fetch 查询。我们将在 [getInitialProps](https://nextjs.org/docs#fetching-data-and-component-lifecycle) 函数中实现这一点。结果将被传递给`Home`组件，并通过`ProxyContext`保存到`proxy`全局状态。此外，我们将使用[内置的错误页面](https://nextjs.org/docs#custom-error-handling)来呈现代码捕获的错误。首先，让我们通过更新`pages/index.js`中的代码来定义`getInitialProps`函数:

```
import fetch from "isomorphic-unfetch";

//const Home...{}

Home.getInitialProps = async ({ req }) => {
  if (req) {
    // This code'll run in server mode
    const api_key = process.env.NEXT_SERVER_IP2PROXY_API || "demo";
    const ipAddress =
      req.headers["x-forwarded-for"] || req.connection.remoteAddress;
    const localAddresses = ["::1", "127.0.0.1", "localhost"];
    // Construct IP2Proxy web service URL
    let proxyUrl = `https://api.ip2proxy.com/?key=${api_key}&package=PX2`;
    // If ipAddress is not localhost, add it to the URL as a parameter
    if (!localAddresses.includes(ipAddress))
      proxyUrl = proxyUrl.concat(`&ip=${ipAddress}`);
    try {
      const response = await fetch(proxyUrl);
      const json = await response.json();
      console.log(json);
      if (json.response != "OK")
        return { errorCode: 500, errorMessage: json.response };
      const { isProxy, proxyType, countryName } = json;
      const newProxy = { ipAddress, isProxy, proxyType, countryName };
      return { newProxy };
    } catch (error) {
      return {
        errorCode: error.code,
        errorMessage: error.message.replace(api_key, "demo"),
      };
    }
  }
  return { newProxy: null }; // This line will run in client mode
};

export default Home; 
```

接下来，让我们更新我们的 Home 组件:

```
import React, { useContext, useEffect } from "react";
import Error from "next/error";
import { ProxyContext } from "../context/proxy-context";

const Home = ({ newProxy, errorCode, errorMessage }) => {
  // Display error messages
  if (errorCode) {
    return <Error statusCode={errorCode} title={errorMessage} />;
  }

  // Save new proxy state
  const [proxy, setProxy] = useContext(ProxyContext);
  useEffect(() => {
    let ignore = false;
    if (newProxy && !ignore) {
      setProxy(newProxy);
    }
    return () => {
      ignore = true;
    };
  }, [newProxy]);

  // return (...
}; 
```

保存更改后，刷新页面。您现在应该看到`ProxyView`组件更新中的字段。记下终端中显示的输出:

```
{
  response: 'OK',
  countryCode: 'KE',
  countryName: 'Kenya',
  proxyType: '-',
  isProxy: 'NO'
} 
```

以上信息应该反映你所在的国家。下一步是重定向，但让我们首先建立我们丢失的登录页面。

### 构建登录页面

构建登录页面非常简单，因为它们具有最少的逻辑。为每个文件添加以下代码后，使用导航菜单来确认每个页面是否正确呈现:

**pages/landing.js** :

```
import React, { useContext } from "react";
import Head from "next/head";
import Layout from "../layout/layout";
import { ProxyContext } from "../context/proxy-context";

const Landing = () => {
  let localPrice = 25000; // Kenyan Shilling
  let exchangeRate = 0;
  let currencySymbol = "";
  const [proxy] = useContext(ProxyContext);
  const { countryName } = proxy;

  switch (countryName) {
    case "Kenya":
      exchangeRate = 1;
      currencySymbol = "KShs.";
      break;
    case "United Kingdom":
      currencySymbol = "£";
      exchangeRate = 0.0076;
      break;
    default:
      break;
  }
  // Format localPrice to currency format
  localPrice = (localPrice * exchangeRate)
    .toFixed(2)
    .replace(/\d(?=(\d{3})+\.)/g, "$&,");

  return (
    <Layout>
      <Head>
        <title>Landing</title>
      </Head>

      <section className="hero is-warning">
        <div className="hero-body">
          <div className="container">
            <h1 className="title">Landing Page</h1>
            <h2 className="subtitle">Product is available in {countryName}!</h2>
            <button className="button is-link">
              <strong>Order Now</strong> - {currencySymbol} {localPrice}{" "}
            </button>
          </div>
        </div>
      </section>
    </Layout>
  );
};

export default Landing; 
```

![landing page](img/7d43ee9fee001bcfead20fc69f6b3ebf.png)

**pages/unavailable.js** :

```
import React, { useContext } from "react";
import Head from "next/head";
import Layout from "../layout/layout";
import { ProxyContext } from "../context/proxy-context";

const Unavailable = () => {
  const [proxy] = useContext(ProxyContext);
  const { countryName } = proxy;

  return (
    <Layout>
      <Head>
        <title>Unavailable</title>
      </Head>

      <section className="hero is-dark">
        <div className="hero-body">
          <div className="container">
            <h1 className="title">
              Sorry. Product is not available in <strong>{countryName}</strong>{" "}
            </h1>
            <h2 className="subtitle">Click to join waiting list</h2>
            <button className="button is-link">
              Subscribe to Alert Notification
            </button>
          </div>
        </div>
      </section>
    </Layout>
  );
};

export default Unavailable; 
```

![unavailable page](img/41642dccae5d73bd3bcab0877b2f71f4.png)

**pages/abuse.js** :

```
import React from "react";
import Head from "next/head";
import Layout from "../layout/layout";

const Abuse = () => (
  <Layout>
    <Head>
      <title>Abuse</title>
    </Head>

    <section className="hero is-danger">
      <div className="hero-body">
        <div className="container">
          <h1 className="title">Sorry! TOR Visitors not allowed</h1>
          <h2 className="subtitle">
            As much as we respect individual privacy, we would rather protect
            ourselves from users abusing privacy networks{" "}
          </h2>
        </div>
      </div>
    </section>
  </Layout>
);

export default Abuse; 
```

![abuse page](img/5c92c026f168eaac4f4d28050920bef1.png)

现在我们已经实现了所有的登录页面，我们需要自动重定向到它们。去下一区。

### 将流量转移到相关的登录页面

为了在 Next.js 中执行重定向，我们需要使用`next/router`包中的`useRouter`钩子来访问`router`组件。我们将使用函数`router.replace()`来执行重定向。我们需要定义两种不同的重定向操作:

1.  第一次重定向是为了阻止 Tor 流量并转向“滥用”页面。
2.  第二次重定向用于将流量转移到“登录”或“不可用”页面。

打开`pages/index.js`并添加以下代码:

```
import { useRouter } from "next/router";

const Home = ({ newProxy, errorCode, errorMessage }) => {
  //...

  // Declare router
  const router = useRouter();

  // Redirect if Proxy Type is TOR
  useEffect(() => {
    if (proxy.proxyType == "TOR") {
      router.replace("/abuse");
    }
  }, [proxy]);

  // Redirect based on visitor's country
  const { countryName } = proxy;
  useEffect(() => {
    if (countryName != "Nowhere" && newProxy.proxyType !== "TOR") {
      redirectPage(router, countryName);
    }
  }, [proxy]);

  //...
};

const redirectPage = (router, countryName) => {
  let redirectPage;
  switch (countryName) {
    case "Kenya": // Replace with your country's name
      redirectPage = "/landing";
      break;
    case "United Kingdom":
      redirectPage = "/landing";
      break;
    default:
      redirectPage = "/unavailable";
  }
  router.replace(redirectPage);
}; 
```

在`redirectPage`函数中，将“肯尼亚”替换为您的国家名称。如果你愿意，你可以添加更多的国家。然而，出于测试目的，我们需要限制国家的数量，因为代理服务支持的国家数量是有限的。说到测试，我们需要首先部署我们的应用程序。

*提示:如果您想在不部署的情况下进行手动测试，只需给`ipAddress`常量分配一个来自另一个国家的 ip 地址。你也可以通过从 TOR IP 的[列表中抓取一个来测试 TOR IP 地址。](https://check.torproject.org/cgi-bin/TorBulkExitList.py?ip=1.1.1.1)*

### 部署我们的 Next.js 应用程序

将我们的 Next.js 应用程序部署到生产服务器的最简单、最快的方法是使用 [Vercel 的无服务器部署平台](https://vercel.com/)。你所要做的就是开始安装他们的 [Now CLI](https://vercel.com/download) 。你只需要验证你的电子邮件地址就可以使用这项免费服务。

如果我们现在部署我们的应用程序，它将按预期运行。然而，它将达到 20 个免费信用限制，因为我们已经设计了我们的应用程序，如果我们没有指定 API 键，就使用`demo`键。要立即上传我们的密钥，只需执行以下命令:

```
now secrets add NEXT_SERVER_IP2PROXY_API <ip2proxy api key> 
```

你可以在 Vercel 仪表盘[这里](https://vercel.com/docs/v2/environment-variables-and-secrets)了解更多关于管理环境变量和秘密的信息。有了这样的定义，部署我们的应用程序就像执行以下命令一样简单:

```
now --prod 
```

该命令将自动运行构建过程，然后将其部署到 Vercel 的服务器上。整个过程应该不到一分钟。生产服务器 URL 将自动复制到您的剪贴板。只需在浏览器上打开一个新标签并粘贴网址。您应该会看到类似下面的内容:

![production deploy](img/06e916bca763e3678ee844e1e1fc2142.png)

### 用免费代理服务测试网站

为了确认我们的应用程序可以基于来源国家进行重定向，我们将使用一个免费的代理服务来模拟不同的位置。只需输入您的应用程序的公共 URL 并选择一个服务器，或者随意离开。

![hma proxy form](img/2a29b393c9c075f121bb17970c2723d4.png)

在我的情况下，肯尼亚和英国将直接登录页面。

![hma proxy landing](img/3e5991d1cdd303af611881ac2fa4ec9a.png)

任何其他国家将重定向到“不可用”页面。

![hma proxy unavailable](img/9a1132d6b66fd954c2ce1b31439f4dac.png)

### 用 Tor 浏览器测试网站

现在，让我们看看我们的网站是否可以阻止来自 Tor 网络的流量。只需访问 [Tor 网站](https://www.torproject.org/)并为您的平台安装 Tor 浏览器。下面是该网站的截图:

![tor browser test](img/c0445900bca0db83be2738f670909558.png)

提示:如果你在电脑上安装 Tor 时遇到困难，你会发现在 Android 或 IOS 设备上安装和运行 Tor 浏览器会更容易。

## 本地数据库选项

在结束本文之前，我想提一下 IP2Location 提供了他们 web 服务的数据库版本。它以 CSV 文件的形式出现，可以导入到您喜欢的任何数据库系统中。您可以使用数据库构建自己的本地 web 服务，而不是查询远程 web 服务，这应该会导致更快的响应。请注意，当您购买数据库时，它会每天更新。因此，您需要每天将下载和导入新数据到本地数据库的过程自动化。

您是否想知道我们如何在无服务器架构中部署数据库？很简单。将您的数据库部署到云服务，如 [MongoDb](https://vercel.com/guides/deploying-a-mongodb-powered-api-with-node-and-vercel) 或 [FaunaDb](https://vercel.com/guides/deploying-nextjs-nodejs-and-faunadb-with-vercel) 。但是，在不同的服务器上部署数据库会抵消拥有本地数据库的好处。我的建议是使用 Docker 容器在同一个服务器或数据中心内打包和部署您的应用程序和数据库，以获得速度优势。

## 摘要

我希望你现在有信心建立一个网站，可以根据用户浏览的国家将用户重定向到相关的登录页面。借助 IP2Location services 提供的信息，您可以通过以下方式使您的网站更上一层楼:

*   向不同的地理位置提供不同的优惠券或优惠
*   通过比较访问者的位置和实际持卡人的地理地址来实施信用卡欺诈检测。

如果您查看一下 [IP2Location web 服务](https://www.ip2location.com/web-service/ip2location)，您会注意到它提供了一组不同的地理位置字段，您可以在您的应用程序中使用。如果你想在一个应用程序中结合 IP2Location 和 IP2Proxy web 服务，你可以看看我之前构建的这个[项目](https://bitbucket.org/brandiqa/next-bulma)，它将向你展示如何做。

## 分享这篇文章