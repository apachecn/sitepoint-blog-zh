# 什么是无密码认证，如何实现

> 原文：<https://www.sitepoint.com/what-is-passwordless-authentication/>

[无密码认证](https://en.wikipedia.org/wiki/Passwordless_authentication)是一种用户管理方法，用户无需使用密码或秘密即可登录系统或应用程序。它不是使用基于**知识的**因素(如密码)，而是通过**所有权**因素(如电子邮件账户)或**固有**因素(如人脸识别)来验证用户的身份。

*本文是与 [Frontegg](https://frontegg.com/) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

有许多身份验证方法可以替代密码:

*   一次性代码(OTC)
*   魔法线
*   生物识别登录(指纹、FaceID、语音识别)
*   智能卡或物理令牌
*   数字证书

![Passwordless Authentication methods](img/48f17f09451dbfd7d0087968715f6813.png)

## 无密码认证的普及

您可能已经在不知不觉中使用了“无密码认证”。许多银行应用程序使用指纹和语音识别来验证用户。Slack 使用神奇的链接来认证用户，这是出了名的。

过去几年来，无密码认证的使用稳步增长。认证提供商 Auth0 预测，到 2027 年，无密码认证将[取代密码使用。](https://auth0.com/blog/analysis-of-passwordless-connections/) [Gartner 预测](https://www.gartner.com/smarterwithgartner/embrace-a-passwordless-approach-to-improve-security)到 2022 年，“60%的大型和全球性企业以及 90%的中型企业将在超过 50%的使用案例中实施无密码方法，高于 2018 年的 5%”。

网络上的大玩家也在尽最大努力加速这项技术的应用。在 2022 年的世界密码日，谷歌、微软和苹果宣布了他们的计划，以扩大对通用无密码登录标准的支持。【2022 年 6 月，苹果发布了他们新的[“万能钥匙”功能](https://www.macrumors.com/2022/06/08/apple-passkeys-next-generation-passwords/)，用于登录网站和应用。这一声明实质上意味着苹果将使用 Touch ID 或 Face ID 为该网站创建一个数字密钥。这消除了创建和写下密码的需要。

## 无密码身份验证的优势

无密码身份验证提供了安全性和用户体验优势:

1.  **降低网络钓鱼和密码被盗的风险:**用户不易受到网络钓鱼攻击，他们会被带到虚假网站并输入登录凭据。如果用户不输入密码，他们将不会容易受到[暴力攻击](https://en.wikipedia.org/wiki/Brute-force_attack)，密码数据泄露和其他类型的凭证盗窃。
2.  **减少凭据重复使用:**跨多个服务和帐户重复使用密码会给用户和您的系统带来更大的风险，这是无法避免的。据[报道](https://www.zdnet.com/article/were-all-still-using-the-same-passwords-even-after-theyve-been-breached/)称，64%的人在一次泄露中为其他账户使用了相同的密码。
3.  **不再需要记忆练习:**你的用户不需要记住这么多账户的用户名和密码。有时在多次登录失败后不得不一次又一次地重设密码。
4.  **更快登录:**我们都很忙。建议强密码至少有 16 个字符长，与扫描指纹或打开神奇链接相比，需要很长时间才能输入。

## 无密码身份验证的局限性

无密码身份验证并不完美，从安全性和体验的角度来看，它有一些局限性。

*   **不熟悉的用户体验:**很多人习惯输入或自动填写密码。魔术链或 OTC 的变化可能会让用户感到震惊。
*   **被盗设备或 SIM 卡交换风险:**如果用户的手机被盗，或者他们成为了 [SIM 卡交换骗局的受害者，那么通过短信发送一次性代码可能会使他们易受攻击](https://en.wikipedia.org/wiki/SIM_swap_scam)。
*   **生物识别安全并不完美:**指纹扫描仪、TouchID 和 FaceID [这些年来已经被成功入侵](https://www.sysgroup.com/resources/blog/biometrics-multi-factor-authentication)。

依赖任何单一因素进行身份验证，无论是否有密码，都会带来一定程度的风险。我们建议尽可能使用多因素身份认证(MFA)。

## 无密码认证安全吗？

是的，无密码认证被认为是安全的，但它并不是完全没有风险。没有密码的帐户不必担心密码落入恶意用户手中。这可能通过数据泄露、暴力破解、丢失设备或放错便利贴而发生。

与无密码身份验证相关的许多风险同样适用于其他方法。

如果黑客可以访问你的电子邮件帐户，而你正在使用魔法链接进行无密码认证，他们将能够轻松登录。然而，如果您使用常规密码，这种风险是相同的。坏演员只需要点击“重置密码”并发送重置链接到相同的电子邮件地址。

最后，就像任何其他系统一样，无密码认证系统也容易受到直接攻击来破坏或绕过安全措施。无论您的行为多么安全，存储和验证您的凭证的系统永远不会完全安全。

指纹验证和其他生物特征因素更加难以欺骗，但并非不可能，并提供了一种非常安全的方式来授权自己。

## 无密码身份验证与多因素身份验证(MFA)

多因素身份验证是一种在登录时使用多因素身份验证的方法。这种情况经常发生在使用用户名和密码登录帐户时，然后收到一个 6 位数的一次性代码(OTC)来确认您的设备所有权。

在本例中，OTC 票据贴现商是无密码的。相反，如果你要使用指纹和一次性代码，那么你有一个完全无密码的 MFA 设置。

## 如何在你的网站上实现无密码认证

将无密码身份验证集成到您的应用程序或网站比以往任何时候都更容易。根据您现有的基础架构，现在有许多选项可供您选择:

*   **用户管理解决方案:**这些提供商提供完全托管的服务，不仅提供传统的无密码认证，还提供用户管理和权限管理。
    *   **何时使用:**希望避免所有低价值和高风险开发工作的新系统构建、初创公司和团队。
    *   **何时不使用:**如果您有一套非常定制的认证或用户管理要求，可能不适合他们的系统。
    *   **提供者:** [Frontegg](https://frontegg.com/) ， [Okta/Auth0](https://www.okta.com/) ， [FusionAuth](https://fusionauth.io/%20rel=) ， [Trusona](https://www.trusona.com/) ， [AppWrite](https://appwrite.io/)

*   **认证服务提供商:**这些服务提供用户认证、访问管理和其他服务，如会话管理。
    *   **何时使用:**您有现有的用户管理服务，并且希望有人负责密码和身份验证。
    *   **何时不用:**你的开发经验或资源有限。如果您有一个简单的身份和访问管理模型，您可能需要考虑一个如上所述的全面管理的解决方案。
    *   **提供商:** [AWS Cognito](https://aws.amazon.com/blogs/mobile/implementing-passwordless-email-authentication-with-amazon-cognito/) ，[谷歌身份平台](https://cloud.google.com/identity-platform)，[微软 Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-authentication-passwordless)

## React 无密码认证–speed run

为了演示为您的用户引入无密码方法是多么容易，我们将带您通过一个名为 Frontegg 的提供商的 5 分钟教程。一个自助式端到端用户管理平台，除了其他用户管理功能之外，还提供几种形式的无密码登录方法。

构建登录和认证服务是非常耗时的，并且不会增加用户流量的价值，但是如果你做错了，可能是有害的。随着提供认证的服务变得越来越好，越来越便宜，应该没有太多理由为你的应用程序建立自己的密码验证系统。

1.  ### **Create your free Frontegg account**

通过他们的网站创建您的 [Frontegg](https://frontegg.com) 账户。确保在入职流程中选择 Magic Code 或 Magic Link 作为无密码选项！

![Passwordless Options](img/4843e89745e95309e9679c64ce0531a0.png)

2.  ### **Start the integration process**

一旦你完成了登录框的创建并选择了无密码方法，你会看到一个选项**发布到 Dev。**

Frontegg 使用[环境](https://docs.frontegg.com/docs/environments)(开发、QA、暂存、生产)，为您的认证环境提供唯一的子域、密钥和 URL。

现在，您将被带到一个包含一些示例代码的页面，更重要的是，您的“baseURL”和“clientID”。保持此页面打开，并移至您的 IDE 进行下一步。

3.  ### **Create a React application (skip this step because you already have your own application)**

在您的终端中键入以下命令创建一个新的 Reactreact 应用程序，并导航到新目录。

```
npx create-react-app app-with-frontegg
cd app-with-frontegg
```

4.  ### **Install and import FrontEgg**

运行以下命令安装 Frontegg React 库和 react-router。如果您的应用程序中已经安装了 react-router，您可以跳过安装。

```
npm install @frontegg/react react-router-dom
```

5.  ### **Configure login settings**

在`src/index.js`文件中，添加以下代码。然后回到 Frontegg 页面，从代码示例中找到“baseUrl”和“clientID”。

注意:一旦此入职流程完成，这些值始终可以在您工作区的管理部分找到。

```
import React from 'react';
import ReactDOM from 'react-dom'; // For react 17
// For react 18: import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';

import { FronteggProvider } from '@frontegg/react';

const contextOptions = {
  baseUrl: '## YOUR BASE URL ##',
  clientId: '## YOUR CLIENT ID ##'
};

// For react 18: 
// const root = ReactDOM.createRoot(document.getElementById('root'));
// root.render(
ReactDOM.render(
    <FronteggProvider contextOptions={contextOptions} hostedLoginBox={true}>
        <App />
    </FronteggProvider>,
    document.getElementById('root')
);
```

6.  ### **Redirect to login**

使用 Frontegg useAuth 钩子，您可以确定一个用户是否通过了身份验证。如果用户未通过身份验证，您可以通过 useLoginWithRedirect 挂钩重定向用户登录(如下面的示例所示)。

```
import './App.css';
// import { useEffect } from 'react';
import { ContextHolder } from '@frontegg/rest-api';
import {
  useAuth, useLoginWithRedirect
} from "@frontegg/react";

function App() {
  const { user, isAuthenticated } = useAuth();
  const loginWithRedirect = useLoginWithRedirect();
  // Uncomment this to redirect to login automatically
  // useEffect(() => {
  //   if (!isAuthenticated) {
  // loginWithRedirect();
  //   }
  // }, [isAuthenticated, loginWithRedirect]);
  const logout = () => {
    const baseUrl = ContextHolder.getContext().baseUrl;
    window.location.href = `${baseUrl}/oauth/logout` +
                           `?post_logout_redirect_uri=` +
                           `${window.location}`;
  };
  return (
    <div className="App">
      { isAuthenticated ? (
        <div>
          <div>
            <img src={user?.profilePictureUrl}
                 alt={user?.name}
            />
          </div>
          <div>
            <span>Logged in as: {user?.name}</span>
          </div>
          <div>
            <button onClick={() => alert(user.accessToken)}>
              What is my access token?
            </button>
          </div>
          <div>
            <button onClick={() => logout()}>
              Click to logout
            </button>
          </div>
        </div>
      ) : (
        <div>
          <button onClick={() => loginWithRedirect()}>
            Login
          </button>
        </div>
      )}
    </div>
  );
}

export default App;
```

7.  ### **Log in**

运行`npm start`或在浏览器中打开 [http://localhost:3000](http://localhost:3000) 并点击`Login`按钮。您应该会看到新创建的登录和注册页面。

**注意，这里没有密码字段🎉**

![Frontegg Signup](img/5c8f300866773f59603c757711ff0fe6.png)

点击注册，进入你的邮箱，点击激活我的帐户。

![Frontegg One Time Code](img/4c365bb4a5b9db55b946fff26ea04950.png)

当你想登录时，你只需输入你的电子邮件，等待六位数字代码到达，你就成功了。没有密码，没有烦恼。

## 结论

我希望这份无密码认证指南不仅能帮助您理解这项技术是多么的平易近人，还能帮助您理解它在未来几年将变得多么重要。

## 分享这篇文章