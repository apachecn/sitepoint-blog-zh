# 如何用 React 和 Firebase 构建 MVP

> 原文：<https://www.sitepoint.com/react-firebase-build-mvp/>

当设计一个想法的原型时，建议你简化并只构建将产品推向市场所需的最少核心功能。这有助于你在浪费时间和金钱在没人感兴趣的产品上之前，确定是否有适合你应用的市场。这被称为“最小可行产品”(MVP)。在本教程中，您将学习如何使用 React 和 Firebase(后端即服务平台)构建 MVP。

为了提供最大的价值，我们不会一步一步地开发 React + Firebase 应用程序。相反，我将分解一个工作原型，并使用伪编码语言解释关键概念。实际代码相当冗长，因为它主要是前端逻辑，旨在处理许多方面，如状态管理、响应性 UI 设计和可访问性。

我的目标是向您展示构建链接到无服务器后端的 web 界面的架构设计。我们将使用的完整 Firebase 项目位于我们的 GitHub [库](https://github.com/sitepoint-editors/sitepoint-books-firebase)中。在本教程结束时，您应该能够在自己的 MVP web 项目中应用这些概念。

## 为什么选择 Firebase？

如果你想在一个无服务器平台上构建一个 web 应用程序，你会遇到无数的提供者，他们提供你构建产品所需的所有基本服务。这些服务包括认证、数据库、云功能、存储、分析等等。

如果您要为每个服务挑选最好的，那么您将有一个相当高的学习曲线来弄清楚如何让所有这些服务在您的应用程序中协同工作。您的产品还会面临许多安全风险，因为您的产品需要连接多个终端才能工作。有一些措施可以保护您的应用程序，但是大多数开发人员并不知道需要解决哪些安全问题。

Firebase 是一个后端即服务平台，可以在一个平台下方便地提供许多基本服务。这缩短了学习曲线，也使构建安全的 web 和移动应用程序变得更加容易。

这些 Firebase 服务包括:

*   证明
*   数据库
*   储存；储备
*   云函数
*   分析学
*   主办；主持

## 先决条件

请注意，本教程是为希望快速学习如何在项目中使用 Firebase 的中级到高级 React 开发人员编写的。在继续之前，我提供了一些您需要熟悉的附加主题:

*   [作出反应并结束战斗](https://www.sitepoint.com/react-tailwind-css-build-site/)
*   [反应查询](https://www.sitepoint.com/react-query-fetch-manage-data/)
*   [维特](https://www.sitepoint.com/vitejs-front-end-build-tool-introduction/)——比`create-react-app`更好的选择

## 消防基地项目计划

我们要研究的项目是一个简单的在线图书馆，它将数据组织成书籍、作者和类别。下面是一个实体关系图。

![entity relationship diagram](img/e76dd76c4889bc44a69d8ebffb7339c2.png)

应用程序逻辑被组织成:

*   屏幕容器(页面或视图)
*   表示组件(表格、表格)
*   布局组件(页脚、导航栏)
*   可共享的 UI 组件(警报、模式、页面标题)
*   Firebase 服务(数据库、存储)
*   Firebase 配置脚本(后端连接器)

下面是主项目架构的图示:

![application architecture diagram](img/7012bd03d46f57cfce9e83a51d3485b5.png)

我们将使用以下文件夹结构来组织我们的代码:

```
├── components
│   ├── entity (e.g. book)
│   │   ├── Card.jsx (-> BookCard)
│   │   ├── Detail.jsx (-> BookDetail)
│   │   ├── Form.jsx
│   │   └── List.jsx
│   └── ui
│       └── Component.jsx (e.g. PageHeader, Alert)
├── layout
│   ├── Footer.jsx
│   └── Navbar.jsx
├── screens
│   ├── entity
│   │   ├── Detail.jsx (-> ScreenBookDetail)
│   │   ├── Form.jsx (-> ScreenBookForm)
│   │   └── List.jsx
│   ├── category
│   │   ├── Form.jsx
│   │   └── List.jsx
│   ├── Home.jsx
│   └── NotFound.jsx
└── services
    └── Service.js (e.g. Database, Storage) 
```

`->`符号表示功能组件如何命名的示例。查看本[指南](https://hackernoon.com/structuring-projects-and-naming-components-in-react-1261b6e18d76)，了解更多关于前端应用命名约定的信息。

## Firebase 项目设置

我们将使用的项目是使用 Vite + React 模板搭建的。要在您的工作区中设置项目，只需打开一个终端并执行以下命令:

```
# Clone project
git clone git@github.com:sitepoint-editors/sitepoint-books-firebase.git
cd sitepoint-books-firebase

# Install dependencies
npm install

# Prepare environment config file
cp env.example .env.local 
```

现在还不要启动开发服务器，因为我们需要首先设置我们的 Firebase 后端，这将在下一步中完成。

## Firebase 设置

前往 [Firebase](https://firebase.google.com/) ，用你的谷歌账户登录。然后:

1.  创建一个新的 Firebase 项目，并将其命名为`SitePointBooks`。

2.  不要为此项目启用 Google Analytics。点击**创建项目**按钮。

3.  在 Firebase 控制台中，创建一个新的 **Web 应用**，并将其命名为`sitepoint-books-app`。您可以访问 Firebase 控制台，如下图所示。
    ![firebase-mvp-create-project](img/f10ea1e675ef08c035809686399d20e2.png)

4.  在下一步中，为您的应用程序命名(可以与项目名称相同)，然后单击**注册应用程序**。

5.  在**添加 Firebase SDK** 部分，选择**使用 npm** 并复制输出。
    ![firebase web app config](img/1739a8441543547ae50cb5cd86982003.png)
    有大量 Firebase SDKs 可用。你可以在这里找到更多关于这些[的信息。](https://firebase.google.com/docs/firestore/client/libraries)

6.  最后，记下您的 Firebase 配置，然后单击**继续控制**。

请注意，公开 Firebase API 密钥是安全的，因为对后端资源的访问受到 Firebase 安全规则的保护。也就是说，它们只能由经过身份验证的用户访问。不幸的是，对于本教程，我们需要禁用它们，因为我们不会处理 Firebase 认证。

对于快速设置，您可以简单地将提供的脚本复制到一个`firebase.js`文件中。然而，我更喜欢使用一个`.env.local`文件保存 Firebase 配置设置。下面是一个 Firebase 配置示例:

```
VITE_API_FIREBASE_API_KEY=AIzaSyDfoP234E8waxeN8QZVrkA5LXqjjyPeFYs
VITE_API_FIREBASE_AUTH_DOMAIN=sitepointdemo-26ea0.firebaseapp.com
VITE_API_FIREBASE_PROJECT_ID=sitepointdemo-26ea0
VITE_API_FIREBASE_STORAGE_BUCKET=sitepointdemo-26ea0.appspot.com
VITE_API_FIREBASE_MESSAGING_SENDER_ID=292100755259
VITE_API_FIREBASE_FIREBASE_APP_ID=1:292100755259:web:38be20c9ab080b4ab1b11e 
```

不要使用这些 Firebase 配置设置，因为它们是为临时项目创建的。

请注意，这只会阻止在存储库中存储 Firebase 配置密钥。一旦构建并部署了应用程序，恶意攻击者就很容易通过浏览器检查工具获得这些密钥。这证明了设置身份验证的重要性。

目前，我们的 Firebase 后端是空的。在接下来的步骤中，我们将用数据填充它。

### 云存储

Firebase 的云存储是一项服务，允许开发者存储图像、视频、音频和其他用户生成的内容。对于本教程，我们将只使用它来存储图像。转到存储页面，点击**开始**按钮。将出现一个弹出向导。只需接受默认规则，并为您的默认存储桶选择一个位置。单击 done 后，一会儿就会为您创建一个存储桶。

在下一页上，执行以下操作:

1.  创建以下文件夹:

    *   `categories`
    *   `books`
2.  上传我通过这个 [zip 文件](https://uploads.sitepoint.com/wp-content/uploads/2021/08/1629975705sitepoint-book-images.zip)提供的图片。你应该有下面的结构:
    ![firebase cloud storage](img/16414377ba262b94539aa5eda43feaca.png)

3.  点击**规则**选项卡，更新安全规则，允许我们未经认证的应用程序访问这些文件。请注意，以下设置会将您的存储后端暴露给公众:

    ```
    rules_version = '2';
    service firebase.storage {
      match /b/{bucket}/o {
        match /{allPaths=**} {
          allow read, write: if true;
        }
      }
    } 
    ```

Firebase 使用基于[通用表达式语言](https://github.com/google/cel-spec)的定制语言来定义其安全规则。由于涉及到学习曲线，我们将无法在本文中讨论它。查看关于这个话题的[官方文件](https://firebase.google.com/docs/rules/rules-language?hl=el)。

一旦您使用提供的映像完成了云存储，您就可以继续下一步。

### 云风暴

对于数据库，我们将使用 [Cloud Firestore](https://firebase.google.com/docs/firestore) ，这是一个可扩展的 NoSQL 数据库，允许开发人员使用集合来结构化数据。旧的 [Firebase 实时数据库](https://firebase.google.com/docs/database)也是一个 NoSQL 数据库，但是它将所有数据存储在一个难以查询的平面嵌套 JSON 结构中。

在您的控制台中，进入 Firestore 数据库页面，点击**创建数据库**按钮。应该会出现一个弹出向导:

1.  在第一页上，在测试模式下设置 **Start，以允许在接下来的 30 天内对数据库进行不安全的访问。**
2.  在下一页，设置数据库区域，然后点击**启用**按钮。

一旦数据库初始化完毕，我们就可以继续填充数据库了。在开始填充之前，您应该知道 Firestore 数据库 UI 在链接两个集合时不支持自动链接和验证。因此，您需要打开第二个浏览器选项卡，将一个 ID 从一个记录复制到一个引用字段，如下所示。

![firestore-place-id](img/05bc923881fdc0f6301344081e812916.png)

当您将 ID 值粘贴到引用字段中时，您还需要确保 ID 值周围没有空白。否则，当您执行查询时，将返回一个空对象。这样一来，您就可以开始创建和填充 Firestore 数据库，如下所示:

*   `authors`集合，所有字段类型都是字符串:

    | 名字 |
    | --- |
    | 达伦·琼斯 |
    | 迈克·阿布吉 |

*   `categories`集合，所有字段类型都是字符串:

    | 名字 | 涉及 |
    | --- | --- |
    | java 描述语言 | 类别/javascript.png |
    | 大蟒 | categories/python.png |
    | 反应 | 类别/react.png |

*   `books`集合，除`author_id`和`category_id`外，所有字段类型都是字符串。您必须手动将相关的唯一 id(`place_id`)复制到参考字段中，如上面的屏幕截图所示:

    | 标题 | 学习用 JavaScript 编码 | 数据科学:工具和技能 |
    | --- | --- | --- |
    | **封面** | books/learn-to-code with JavaScript . jpg | 书籍/数据科学-工具和 skills.png |
    | **作者 id(参考)** | /authors/{place id} | /authors/{place id} |
    | **类别标识(参考)** | /类别/{地点 id} | /类别/{地点 id} |
    | **描述** | 这本简单易懂又有趣的指南是您开始编码之旅的最佳起点。您将学习使用 JavaScript——世界上最流行的编程语言——编程，但是您掌握的技术将为您继续使用其他语言打下基础。 | 这本书收集了数据科学中最常用的一些工具的深入指南，如 Pandas 和 PySpark，以及作为数据科学家所需的一些技能。 |
    | **网址** | https://www.sitepoint.com/premium/books/learn-to-code-with-javascript/ | https://www.sitepoint.com/premium/books/data-science-tools-skills/ |

请参见下面的截图，作为如何设置数据库结构的示例。

![cloud firestore database](img/4aeac953b20204fb1b57b17ecaac2e1a.png)

## 启动开发服务器

随着数据库的填充，我们现在可以执行`npm run dev`并浏览到`localhost:3000`来与项目交互。请注意，这是一个为学习而构建的原型应用程序，并非所有功能都已完全实现。

![app book detail](img/5741737927993d62a2dc1381f342632b.png)

## Firebase 项目逻辑

现在让我们开始分解这个项目，以便您可以了解如何构建前端接口来连接 Firebase 后端并与之交互。本教程的重点将主要放在状态管理逻辑上。如果您不熟悉这个 Firebase 项目中使用的用户界面代码，请参考以下关于项目中使用的 UI 库的文档:

*   [TailwindCSS](https://tailwindcss.com/) 和[插件](https://tailwindcss.com/docs/plugins):初级 CSS 框架
*   [HeadlessUI](https://headlessui.dev/) :非样式化 UI 组件的小集合
*   [HeroIcons](https://heroicons.com/) :顺风 CSS 团队收集的手工制作的 SVG 图标
*   [DaisyUI](https://daisyui.com/) : TailwindCSS 组件库
*   [React Hook 表单](https://react-hook-form.com/react):表单状态库
*   [是](https://github.com/jquense/yup):表单验证库

### 按指定路线发送

为涉及两个以上实体的项目构建 CRUD 接口会很快变得复杂。对于路由，我使用了 [React Router](https://www.sitepoint.com/react-router-complete-guide/) 并使用标准化语法实现了一个路由结构。那就是:

*   列出路线:`/{entity}`
*   创建路线:`/{entity}/create`
*   编辑路线:`/{entity}/edit/:id`
*   详细路线:`/{entity}/:id`

下面是在 [`App.jsx`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/App.jsx) 中如何实现路由的简化视图:

```
import React from "react";
import { Route, Switch } from "react-router-dom";

// Layout components
import Footer from "@/layout/Footer";
import Navbar from "@/layout/Navbar";

// Screen(pages or views) containers
import Home from "@/screens/Home";
import NotFound from "@/screens/NotFound";
import ScreenBookList from "@/screens/book/List";
import ScreenBookForm from "@/screens/book/Form";
import ScreenBookDetail from "@/screens/book/Detail";

function App() {
  return (
    <div>  <header>  <Navbar />  </header>  <main>  <Switch>  <Route exact path="/">  <Home />  </Route>  <Route exact path="/book">  <ScreenBookList />  </Route>  <Route path="/book/edit/:id">  <ScreenBookForm />  </Route>  <Route path="/book/detail/:id">  <ScreenBookDetail />  </Route>  <Route path="/book/create">  <ScreenBookForm />  </Route>  <Route component={NotFound} />  </Switch>  </main>  <Footer />  </> );
} 
```

请注意`ScreenBookForm`已被重新用于创建和编辑路线。稍后您将看到如何使用一个表单容器来处理这两种用例。接下来，我们将看看 React 应用程序如何连接到 Firebase 后端。

### 数据库服务

对于 web、mobile 和 Node.js 应用程序，需要在您的项目中安装[官方 Firebase 包](https://www.npmjs.com/package/firebase)。该软件包包含连接到各种后端 Firebase 服务所需的所有工具:

```
npm install firebase 
```

要连接您的云 Firestore 数据库，您需要在 [`firebase.js`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/firebase.js) 中定义以下内容:

```
import firebase from "firebase/app";  // include the Firebase module
import "firebase/firestore"; // access firestore database service

const firebaseConfig = {
  apiKey: import.meta.env.VITE_API_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_API_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_API_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_API_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_API_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_API_FIREBASE_APP_ID,
};
// Initialize Firebase
const app = firebase.initializeApp(firebaseConfig);

export const db = app.firestore();
export default app; 
```

接下来，您可以将`db`对象导入到任何 React 容器中，并开始直接查询数据库。我更喜欢首先创建一个包含所有必要 CRUD 逻辑的`services/DatabaseService.js`:

```
import { db } from "@/firebase";

class DatabaseService {
  collection;

  // Specify 'authors', 'categories', or 'books' as collection name
  constructor(collectionName) {
    this.collection = db.collection(collectionName);
  }

  // returns list of records as an array of javascript objects
  getAll = async () => {
    const snapshot = await this.collection.get();
    return snapshot.docs.map((doc) => {
      return {
        id: doc.id, // append document id to each document
        ...doc.data(),
      };
    });
  };

  // returns a single document in object format
  getOne = async ({ queryKey }) => {
    const { id } = queryKey[1];
    if (!id) return; // entity form is in create mode
    const snapshot = await this.collection.doc(id).get();
    return snapshot.data();
  };

  // resolve a relation, returns the referenced document
  getReference = async (documentReference) => {
    const res = await documentReference.get();
    const data = res.data();

    if (data && documentReference.id) {
      data.uid = documentReference.id;
    }

    return data;
  };

  // save a new document in the database
  create = async (data) => {
    return await this.collection.add(data);
  };

  // update an existing document with new data
  update = async (id, values) => {
    return await this.collection.doc(id).update(values);
  };

  // delete an existing document from the collection
  remove = async (id) => {
    return await this.collection.doc(id).delete();
  };
}

// Create services for each entity type
export const AuthorService = new DatabaseService("authors");

export const CategoryService = new DatabaseService("categories");

export const BookService = new DatabaseService("books"); 
```

上面的代码有两个主要部分:

*   `DatabaseService`类，包含 CRUD 逻辑，即读取(`getAll`、`getOne`)、创建、更新和删除。
*   我们正在使用的每个集合类型的数据库服务实例，即`books`、`categories`和`authors`。我们将在容器(屏幕)组件中使用它来与我们的 Firebase 后端交互。

对`DatabaseService`类的一些附加说明:

*   对于`getAll`方法，当您调用`data.doc()`方法时，您只获得没有对象 ID 的数据值。为了解决这个问题，我们需要调用`doc.id`，并将其与其余的值合并。为了使更新和删除操作能够进行，这是必要的。
*   我将在“解析文档关系”一节中解释`getReference`函数。
*   关于其余的功能，请参考行内注释和 [Firestore 文档](https://firebase.google.com/docs/firestore/)了解更多信息。

有了数据库服务之后，让我们看看如何获取数据并用来填充前端接口。

### 列出文档

既然我们已经建立了数据库服务，我们需要从一个容器中调用它——比如`ScreenAuthorList`。一旦获得了数据，它将通过 props 传递给一个表示组件，即`AuthorList`。

![author list](img/8b730f6b4027e81bf7ef4a88d77ab19e.png)

为了在我们的前端应用程序状态中管理服务器数据，我们将使用 React Query。使用这个包比设置 Redux 或任何其他前端状态管理解决方案要简单得多。下面是一个简化版的 [`ScreenAuthorList.jsx`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/screens/author/List.jsx) 来演示这个概念:

```
import React from "react";
import { useQuery } from "react-query";

import { AuthorService } from "@/services/DatabaseService";
import PageHeading from "@/components/ui/PageHeading";
import AuthorList from "@/components/author/List";

function ScreenAuthorList() {
  const { data, status } = useQuery("authors", AuthorService.getAll);

  return (
    <>  <PageHeading title="Author List" />  <div>{status === "success" && <AuthorList data={data} />}</div>  </>
  );
}

export default ScreenAuthorList; 
```

下面是 [`AuthorList.jsx`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/components/author/List.jsx) 的简化版本，它只接受数据并以表格格式显示:

```
import React from "react";
import { Link } from "react-router-dom";

function AuthorList({ data }) {
  return (
    <div>  <table>  <thead>  <tr>  <th>Name</th>  </tr>  </thead>  <tbody>  {data.map((author, index) => (
            <tr key={index}>  <td>{author.name}</td>  </tr>
          ))}  </tbody>  </table>  </div>
  );
}

export default AuthorList; 
```

注意，我已经排除了`edit`和`delete`按钮，我们将在接下来研究它们。

### 使用确认对话框删除文档

`AuthorList`组件中的**删除**按钮定义如下:

```
<table> ... <tbody>  <tr> ... <td>  <button
          title={`Delete ${author.name}`}
          onClick={() => showDeleteModal(author.id)}
        >  <TrashIcon />  </button>  </td>  </tr>  </table> 
```

让我们看看`showDeleteModal(id)`函数是如何在组件中定义的:

```
import React, { useState } from "react";

function AuthorList({ data, deleteAction }) {
  const [selected, setSelected] = useState(); // set author.id for deletion
  const [openModal, setOpenModal] = useState(false); // show or hide DeleteModal

  const showDeleteModal = (id) => {
    setSelected(id);
    setOpenModal(true);
  };

  const deleteModalAction = () => {
    deleteAction(selected);
    setOpenModal(false);
  };

  const cancelModalAction = () => {
    setOpenModal(false);
  };

  return (
    <div>  <DeleteModal
        open={openModal}
        deleteAction={deleteModalAction}
        cancelAction={cancelModalAction}
      />  <table>// delete button is here</table> </div> );
} 
```

基本上，当调用`showDeleteModal(id)`函数时，会发生以下情况:

*   `selected`状态被设置为当前的`author.id`
*   确认对话框设置为可见

确认对话框，又名 [`DeleteModal.jsx`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/components/ui/DeleteModal.jsx) ，有点过于冗长，无法在这里显示完整的代码。为了简单起见，我使用伪语言来定义它的结构，以使它更具可读性:

```
function DeleteModal({ isOpen, deleteAction, cancelAction }) {
  return (
    <Modal.Root show={isOpen}>  <modal-content>  <p>  {" "} Are you sure you want to permanently remove this record forever?{" "}  </p>  </modal-content>  <modal-footer>  <button onClick={deleteAction}>Delete</button>  <button onClick={cancelAction}>Cancel</button>  </modal-footer>  </Modal.Root>
  );
} 
```

`cancelAction`功能将简单地隐藏确认对话框。`deleteAction`函数将调用负责执行实际文档删除的数据库处理程序。这个处理程序是在容器级别定义的， [`ScreenAuthorList.jsx`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/screens/author/List.jsx) 。下面是代码的简化版本:

```
import { useMutation, useQueryClient } from "react-query";

function ScreenAuthorList() {
  const queryClient = useQueryClient();

  const deleteMutation = useMutation((id) => AuthorService.remove(id), {
    onSuccess: () => {
      queryClient.invalidateQueries("authors");
    },
  });

  const deleteAction = async (id) => {
    deleteMutation.mutateAsync(id);
  };

  return (
    <>  <AuthorList data={data} deleteAction={deleteAction} />  </>
  );
} 
```

成功删除后，我们需要调用`queryClient.invalidateQueries()`来确保更改反映在缓存数据的所有组件上。现在让我们看看如何通过前端 UI 创建和更新文档。

### 创建和更新文档

为了演示如何创建和更新文档，我们将使用 [`AuthorForm.jsx`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/components/author/Form.jsx) ，这是最简单的解释。

![app author form](img/818295fd95a3e838bee075ce77818a44.png)

首先，我们需要查看将用户导向`ScreenAuthorForm`页面的`Create`和`Edit`按钮。这是在 [`AuthorList.jsx`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/components/author/List.jsx) 中完成的组件:

```
import { Link } from "react-router-dom";
import { UserCircleIcon, PencilAltIcon } from "@heroicons/react/outline";

function AuthorList() {
  return (
    <div>  <div>  <Link to="/author/create">  <UserCircleIcon /> New Author </Link>  </div>  <table> ... <td>  <Link to={`/author/edit/${author.id}`} title={`Edit ${author.name}`}>  <PencilAltIcon />  </Link>  </td> ... </table>  </div>
  );
} 
```

[`ScreenAuthorForm`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/screens/author/Form.jsx) 容器被设计用来处理创建和更新作者用例。在更新的情况下，我们需要从 URL 中检索`id`,然后使用它为我们的 Firebase 数据库获取文档。对于 create，我们只呈现表单，不传递任何值:

```
import { useParams } from 'react-router-dom'

function ScreenAuthorForm() {
  const { id } = useParams() // retrieve id from url parameters
  // fetch document
  const { data, isLoading, error, status } = useQuery(
    ['author', { id }],
    AuthorService.getOne
  )

  // Render create form
  if (!id) {
    return (
      <>  <PageHeading title="Create Author" />  <AuthorForm submit={onSubmit} />  </>
    )
  }

  // Render update form
  return (
    <>  <PageHeading title="Edit Author" />  <AuthorForm values={data} submit={onSubmit} />  <> )
} 
```

我们不会详细讨论表单是如何构造的，但是我会为您提供一个简化版的 [`AuthorForm`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/components/author/Form.jsx) 组件:

```
import React, { useState, useEffect } from "react";
import { useForm } from "react-hook-form";

function AuthorForm({ values, submit }) {
  // initialize react-hook-form
  const { register, reset, handleSubmit } = useForm();

  // populate form fields
  useEffect(() => {
    reset(values);
  }, [values]);

  // call container submit handler to save new/updated values
  const onSubmit = (submittedData) => {
    submit(submittedData);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>  <div className="form-control">  <label>Name</label> <input type="text" {...register("name")} /> </div>  <div className="form-footer">  <button type="submit"> Save </button>  <Link to="/author"> Cancel </Link>  </div>  </form>
  );
} 
```

行内注释应该解释代码的主要部分。参考 React Hook Form 的[文档](https://react-hook-form.com/get-started)关于库如何工作。我们还没有查看过`submit`函数，所以现在让我们在 [`ScreenAuthorForm`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/screens/author/Form.jsx) 容器中进行操作:

```
import { useParams, Redirect } from 'react-router-dom'
import { useQuery, useMutation, useQueryClient } from 'react-query'

function ScreenAuthorForm() {
  const { id } = useParams()
  const queryClient = useQueryClient()

  // call the database service to create or update document depending on presence of id
  const saveData = (data) => {
    if (id) {
      return AuthorService.update(id, data)
    } else {
      AuthorService.create(data)
    }
  }

  // create mutation
  const mutation = useMutation((data) => saveData(data), {
    onSuccess: () => {
      if (id) queryClient.invalidateQueries(['author', { id }])
    },
  })

  // track mutation status i.e. return true after successful mutation
  const { isSuccess } = mutation

  // define submit action handler to be passed down as prop to AuthorForm
  const onSubmit = async (submittedData) => {
    mutation.mutate(submittedData)
  }

  // if mutation is successful, redirect to ScreenAuthorList
  if (isSuccess) {
    return <Redirect to="/author" />
  }

  // render create and update form
  return (
    ...
    <AuthorForm submit={onSubmit} />
    ...
  )
  ...
} 
```

行内注释应该解释每个代码块的作用。请务必参考 React Query 的[突变文档](https://react-query.tanstack.com/guides/mutations)来了解它是如何工作的。在下一节中，我们将看看如何显示存储在 Firebase 的云存储服务中的图像。

### 显示图像

在本节中，我们将使用`CategoryCard`来演示图像的渲染。

![app category list](img/4f03e083362e860b072cf1c6dd3cf77e.png)

提醒一下，这里有一个分类数据的例子:

```
{
  "name": "javascript",
  "cover": "categories/javascript.png"
} 
```

如果您导航到您上传到云存储中的一个图像，您将看到以下格式的 URL 链接:

```
gs://<app id>.appspot.com/<folder>/<filename> 
```

浏览器无法处理此链接。需要转换成 HTTP 格式的下载链接。为此，我们需要导入允许我们的应用程序与 Firebase 的存储服务交互的包。这是在`firebase.js`中完成的:

```
...
import 'firebase/storage'

...
export const storage = app.storage() 
```

接下来，我们可以导入`storage`实例，并定义一个在该转换中执行的函数。这已经在 [`StorageService.js`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/services/StorageService.js) 中完成了:

```
import { storage } from "../firebase";

const storageRef = storage.ref(); // access the default bucket

// accepts file path in the format `folder/filename.ext`
const getImageURL = async (filePath) => {
  const url = await storageRef.child(filePath).getDownloadURL();
  return url;
};

const StorageService = {
  getImageURL,
};

export default StorageService; 
```

现在我们已经建立了一个服务来处理图片 URL 转换，我们可以如下定义 [`CategoryCard`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/components/category/Card.jsx) 组件:

```
import React, { useEffect, useState } from "react";
import { Link } from "react-router-dom";
import StorageService from "../../services/StorageService";

function CategoryCard({ category }) {
  const [imageLink, setImageLink] = useState();

  // download the image link
  useEffect(async () => {
    const url = await StorageService.getImageURL(category.cover);
    setImageLink(url);
  }, [category]);

  return (
    <div>  <Link to={`/category/edit/${category.id}`}>  <img src={imageLink} alt={category.name} />  </Link>  </div>
  );
}

export default CategoryCard; 
```

不幸的是，仅仅显示一幅图像就要做相当多的工作。这个问题我们会在后面的总结中讲到。现在，让我们看看另一个问题，您需要允许用户从可用的文件列表中进行选择。

### 列出文件

当处理图像时，我们可以给用户上传新图像或从现有图像中选择的选项。通常现代界面的特色是资产浏览器，它允许你做这两项工作。为了简单起见，我们将只使用一个基本的下拉选择输入。

![Firebase app category form-dropdown](img/3161f35bd30c18d5b0e336127bbde79b.png)

为了从云存储的特定文件夹中获取文件列表，我们需要有一个函数可以在 [`StorageService.js`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/services/StorageService.js) 中处理该任务:

```
// input: folder name
// output: list of fileNames in array format
const listFiles = async (folder) => {
  const listRef = storageRef.child(folder);
  const res = await listRef.listAll();
  const list = res.items.map((itemRef) => itemRef._delegate._location.path_);
  return list;
};

const StorageService = {
  ...listFiles,
}; 
```

随着`listFiles`函数的定义，我们现在可以从 [`CategoryForm`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/components/category/Form.jsx) 组件中调用它:

```
import React, { useState, useEffect } from "react";
import StorageService from "../../services/StorageService";

function CategoryForm({ values, action }) {
  const [coverOptions, setCoverOptions] = useState([]);

  // Get list of available images from cloud storage
  useEffect(async () => {
    const availableFiles = await StorageService.listFiles("categories");
    setCoverOptions(availableFiles);
  }, []);

  return (
    <form> ... <div className="form-control">  <label>Select Cover</label> <select {...register("cover")}> <option disabled="disabled" value="nocover"> Choose a cover </option>  {coverOptions.map((fileName, index) => (
            <option key={index} value={fileName}>  {fileName}  </option>
          ))}  </select>  </div> ... </form>
  );
} 
```

使用异步的`useEffect`函数，我们能够检索文件名，然后通过`coverOptions`状态填充选择字段。在下一节中，我们将看看如何解决文档关系。

### 解析文档关系

如果我们回忆一下`book`实体结构，它包含了名为`author_id`和`category_id`的引用字段。对于大多数数据库系统和 ORM 库，有一个用值填充引用的选项，这样只需要一个查询就可以加载所有需要的数据。

不幸的是，对于 Firestore 数据库，您需要执行额外的查询来加载引用的文档。我们需要在 [`DatabaseService.js`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/services/DatabaseService.js) 中为其定义一个具体的函数:

```
class DatabaseService {
  ...
  getReference = async (documentReference) => {
    const res = await documentReference.get()
    const data = res.data()

    if (data && documentReference.id) {
      data.uid = documentReference.id
    }

    return data
  }
  ...
} 
```

定义了函数后，我们现在可以完全加载带有引用字段的文档了。参见 [`BookDetail`](https://github.com/sitepoint-editors/sitepoint-books-firebase/blob/master/src/components/book/Detail.jsx) 组件为例:

```
import { BookService } from "@/services/DatabaseService";

function BookDetail({ book }) {
  const [author, setAuthor] = useState();
  const [category, setCategory] = useState();

  // Resolve book.author_id document reference
  useEffect(async () => {
    const authorRef = await BookService.getReference(book.author_id);
    setAuthor(authorRef);
  }, [book]);

  // Resolve book.category_id document reference
  useEffect(async () => {
    const categoryRef = await BookService.getReference(book.category_id);
    setCategory(categoryRef);
  }, [book]);

  return (
    <div> ... {category && <p>{category.name}</p>} ... {author && <p>By {author.name}</p>} ... </div>
  );
} 
```

在上面的例子中，我们使用异步`useEffect`钩子来执行额外的查询。在接下来的部分，我们将开始总结文章。

### 其他 Firebase 服务

不幸的是，有许多 Firebase 服务我无法在本文中一一介绍。这些后端服务对于构建你的 MVP Firebase 应用程序是非常必要的。所以我将对其中的几个做一个简短的总结:

*   **[认证](https://firebase.google.com/docs/auth)** 。该服务允许您在 Firebase 应用程序上轻松添加登录功能。它支持电子邮件、社交账户、GitHub 甚至短信认证方式。Firebase auth 与其他 Firebase 服务紧密集成，并且可以很容易地与您的自定义后端集成。
*   **[云功能](https://firebase.google.com/docs/functions)** 。这项服务允许您编写和执行后端代码，以响应由 Firebase 特性和 HTTPS 请求触发的事件。代码用 JavaScript/TypeScript 编写，运行在 Google Cloud 的托管环境中。
*   **[托管](https://firebase.google.com/docs/hosting)** 。这是一项提供 web 应用、静态和动态内容以及微服务托管的服务。内容通过全球 CDN(内容交付网络)提供。
*   **[分析学](https://firebase.google.com/docs/analytics)** 。您可以使用 Google Analytics 通过`firebase/analytics`包为您的 web 应用程序收集使用和行为数据。您可以收集并跟踪关于您的受众的事件和用户属性(如语言、地理语言)。

如前所述，我们配置的安全规则允许对我们的后端进行公共读/写访问。要了解如何保护你的 Firebase 账户，我建议查看一下[安全规则](https://firebase.google.com/docs/rules)。请注意，您还必须在您的应用程序上实现 Firebase 身份验证，以便允许安全地访问数据。

## 摘要

总而言之，您已经学会了如何:

*   构造和组织前端代码
*   注册 Firebase 应用程序
*   填充 Firestore 数据库和存储
*   从 Firebase 后端检索数据和文件
*   在前端用户界面中链接收藏

还有很多 Firebase 服务我们还没有触及。如您所见，在所有后端服务都在同一屋檐下的情况下，构建 MVP 更容易。我们只安装了一个 Firebase 库，它提供了大多数 MVP 需要的大部分后端功能。

我们还没有看到 [Firebase 扩展](https://firebase.google.com/products/extensions)，这是我们可以轻松实现来扩展我们的 Firebase 应用程序的附加功能。这些扩展的示例包括:

*   [调整图像大小](https://firebase.google.com/products/extensions/storage-resize-images)
*   [用 Algolia 搜索](https://firebase.google.com/products/extensions/firestore-algolia-search)
*   [条纹支付](https://firebase.google.com/products/extensions/firestore-stripe-invoices)
*   [缩短网址](https://firebase.google.com/products/extensions/firestore-shorten-urls-bitly)

还有很多。如果你喜欢 Firebase 的体验，并且想要一个更强大的数据库，你可以看看 [Supabase](https://supabase.io/) ，这是一个开源的替代方案，提供了一个 PostgreSQL 数据库。然而，Supabase 在市场上还是一个相当新的产品，在撰写本文时，它还处于测试开发阶段。

## 分享这篇文章