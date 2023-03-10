# 如何使用 React 和 Firebase 创建 Reddit 克隆

> 原文：<https://www.sitepoint.com/reddit-clone-react-firebase/>

**[React](https://facebook.github.io/react/) 是一个非常棒的构建用户界面的前端库。当选择一个后端与它一起使用时，使用 [Firebase](https://firebase.google.com/) 不会错太多，这是一个后端即服务(Baas)，可以轻松地向 React 应用程序添加数据持久性(以及更多其他功能)。**

在本教程中，我们将使用 Firebase 和 [Create React App](https://create-react-app.dev/) 来构建一个功能类似于 [Reddit](https://www.reddit.com/) 的应用程序。它将允许用户提交一个新的职位，然后可以投票表决。我还将演示如何将我们的 Reddit 克隆部署到 [Vercel](https://vercel.com/) 。

一旦你读完了，你就会明白如何设置 Firebase，如何将它连接到你的 React 应用，以及如何部署结果。

![An inventor placing a FireBase heart into a robot creation](img/486f07e1780f610aadcf5a70e960b9b4.png)

## 为什么选择 Firebase？

Firebase 的优势之一是，它使我们能够非常容易地向用户显示实时数据。一旦用户在一个链接上投票，反馈将是即时的。Firebase 的实时数据库将帮助我们开发这个功能。此外，它还将帮助我们理解如何使用 Firebase 引导 React 应用程序。

## 为什么要反应？

React 以使用组件架构创建用户界面而闻名。每个组件可以包含内部[状态](https://facebook.github.io/react/docs/state-and-lifecycle.html)或者作为[道具](https://facebook.github.io/react/docs/components-and-props.html)传递数据。状态和道具是 React 中最重要的两个概念。这两件事有助于我们确定应用程序在任何时间点的状态。如果你不熟悉这些术语，请先阅读 [React 文档](https://facebook.github.io/react/docs/hello-world.html)。

*注意:你也可以使用像 [Redux](http://redux.js.org/) 或者 [MobX](https://mobx.js.org/) 这样的状态容器，但是为了简单起见，我们在本教程中不会使用。*

这是我们将要建造的东西的现场演示。该应用程序的代码可在 [GitHub](https://github.com/sitepoint-editors/reddit-clone) 上获得。

## 设置项目

要继续操作，您需要在您的机器上安装 Node 和 npm。如果您还没有，请访问 Node.js [下载页面](https://nodejs.org/en/download/)，获取您系统的最新版本(npm 与 Node 捆绑在一起)。或者，你可以参考我们关于使用版本管理器安装节点的[教程。](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)

让我们逐步建立我们的项目结构和任何必要的依赖项。

### 引导 React 应用程序

在 Create React App 的帮助下，我们可以使用以下命令创建一个新的 React 应用程序:

```
npx create-react-app reddit-clone 
```

这将在`reddit-clone`目录中搭建一个新的`create-react-app`项目。我们的目录结构应该如下所示:

![Default structure of directory](img/0d196100c8065a62746bf8b46be72151.png)

一旦引导完成，我们就可以进入`reddit-clone`目录并启动开发服务器:

```
cd reddit-clone && npm start 
```

此时，我们可以访问 [http://localhost:3000/](http://localhost:3000/) 并看到我们的应用程序启动并运行。

![Default page of Create React App](img/d9b120536d11105a7591e3bfe286e61d.png)

### 构建应用程序

在启动任何应用程序后，删除所有我们不需要的文件总是一个好的做法。有一些由 Create React App 生成的文件我们不需要，因此我们将删除它们。

我们可以删除以下文件:

1.  `src/App.css`
2.  `src/App.test.js`
3.  `src/index.css`
4.  `src/logo.svg`
5.  `src/serviceWorker.js`
6.  `src/setupTests.js`

我们还可以从我们的`package.json`文件中删除以下依赖项:

1.  `@testing-library/jest-dom`
2.  `@testing-library/react`
3.  `@testing-library/user-event`

我们也可以从我们的`package.json`文件中删除`test`脚本。这是因为我们不会为我们的应用程序编写任何测试。如果你想测试 React 应用，请参考我们的教程，“[如何使用 Jest](https://www.sitepoint.com/test-react-components-jest/) 测试 React 组件”。

我们的`src/index.js`文件应该包含以下内容:

```
import React from "react";
import ReactDOM from "react-dom";
import App from "./app";

ReactDOM.render(
  <React.StrictMode>  <App />  </React.StrictMode>,
  document.getElementById("root")
); 
```

我们将把我们的`src/App.js`重命名为`src/app.js`。将其修改为包含以下内容:

```
import React from "react";

function App() {
  return <div>Hello world!</div>;
}

export default App; 
```

现在，我们可以从根目录使用以下命令重新启动我们的开发服务器:

```
npm start 
```

我们的开发服务器应该在 [http://localhost:3000/](http://localhost:3000/) 上启动并运行，看起来应该如下所示:

![The UI of our application after removing unnecessary files](img/8c5a68203b860d3a896d1c6126ede288.png)

## 创建新的 Firebase 项目

在这一节中，我们将安装 Firebase 并将其集成到我们的应用程序中。

如果你还没有 Firebase 账户，你现在可以通过访问他们的[网站](https://firebase.google.com/)创建一个免费账户。创建好新账户后，登录你的账户，进入[控制台](https://console.firebase.google.com/)页面，点击*创建项目*。

输入你的项目名称(我称我的项目为 *reddit-clone* )，接受条款和条件，然后点击*继续*按钮。

![Step 1 of creating a Firebase project](img/9027613af7d6aafc0a312a198a261d55.png)

在下一步中，您应该选择是否为项目启用 Google Analytics，然后单击*继续*按钮。

![Step 2 of creating a Firebase project](img/4fc26606a7da5333ad014abe8bcf45f1.png)

在第三步中，我们应该选择一个*谷歌分析账户*，然后点击*创建项目*按钮:

![Step 3 of creating a Firebase project](img/1fec572d2b12d4d42dce8061771ea4ca.png)

过一会儿，您会看到一个通知，说明您的新项目已经准备好了。点击*继续*退出向导。

## 在 Firebase 项目中创建新的应用程序

在本节中，我们将从 [Firebase 控制台](https://console.firebase.google.com/)创建一个新的 Firebase 应用程序。我们可以通过选择 Web 选项创建一个 *Web* 应用程序。

![Creating a new Firebase web app: Step 1](img/6004dd083a4feb4f07ba462617b88b16.png)

接下来，我们需要输入项目名称并点击*注册应用*按钮，不要勾选*也设置 Firebase 托管*复选框。

![Creating a new Firebase web app: Step 2](img/9e6b422b32f210a2ca80ac2b53005cdd.png)

现在，您将看到我们新的 Firebase web 应用程序的所有凭证。

![Creating a new Firebase web app: Step 3](img/a8388bc7969a7b567edd7a45d7da2b09.png)

记下这些凭证并点击*继续控制*。

我们现在可以将应用程序的凭证添加到一个[环境文件](https://create-react-app.dev/docs/adding-custom-environment-variables/)中:

```
// .env

REACT_APP_FIREBASE_API_KEY="123456"
REACT_APP_FIREBASE_AUTH_DOMAIN="reddit-clone-123456.firebaseapp.com"
REACT_APP_FIREBASE_PROJECT_ID="reddit-clone-123456"
REACT_APP_FIREBASE_STORAGE_BUCKET="reddit-clone-123456.appspot.com"
REACT_APP_FIREBASE_MESSAGING_SENDER_ID="123456"
REACT_APP_FIREBASE_APP_ID="1:123456:web:123456"
REACT_APP_FIREBASE_MEASUREMENT_ID="G-123456" 
```

*注意:将所有凭证存储在一个环境文件中并将该文件添加到`.gitignore`中总是一个好主意，这样凭证就不会泄露到源代码中。*

接下来，我们可以创建一个新文件`src/lib/firebase.js`来存储我们所有的 Firebase 凭证:

```
import firebase from "firebase";

const firebaseConfig = {
  apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
  authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
  storageBucket: process.env.REACT_APP_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.REACT_APP_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.REACT_APP_FIREBASE_APP_ID,
  measurementId: process.env.REACT_APP_FIREBASE_MEASUREMENT_ID,
};

const initFirebase = firebase.initializeApp(firebaseConfig);
const db = initFirebase.firestore();

export default db; 
```

最后，我们需要安装 [firebase 包](https://www.npmjs.com/package/firebase)，这样我们就可以与我们的数据库进行交互:

```
npm install firebase 
```

## 添加一个新的火焰云火焰风暴

现在，我们需要添加一个新的 Firebase Cloud Firestore —一个可扩展的 NoSQL 云数据库。这可以通过选择 *Cloud Firestore* 链接并点击*创建数据库*按钮来完成。

![Creating a new Firebase Cloud Firestore](img/ab7e5de9454e8293bab46b32dd193cda.png)

接下来，我们将选择在生产模式下启动 Firestore 的选项。

![Selecting the option to start the Firebase Cloud Firestore in production mode](img/e6de948acddce50a1e7fa22000856643.png)

点击*下一个*。在下一个屏幕上，我们需要选择云 Firestore 的位置，然后单击*启用*按钮。

![Selecting the location of the Firebase Cloud Firestore](img/c1ad3b4a8264687a5c155761ac62fc8b.png)

您将看到一条“供应云 Firestore”消息，随后是*设置安全规则*，稍等片刻后，您将被重定向到新项目的控制面板。

## 向 firebase cloud firestorm 添加新系列

接下来，我们需要向刚刚创建的 Firebase Cloud Firestore 添加一个新的集合。我们可以点击*开始收集*按钮。

![Adding a new collection to the Firebase Cloud Firestore:Step 1](img/210a2de746f26777cdd767264d6bcfbb.png)

我们需要给我们的*收藏 ID* 添加一个名称。我们可以称之为*帖子*，因为我们将添加帖子并对帖子进行投票。

![Adding a new collection to the Firebase Cloud Firestore:Step 2](img/eff1a130828bbe0f033e1a74c7551f4b.png)

点击*下一个*。现在是时候向我们的集合添加一个文档了。每个文档都需要一个 ID，所以单击第一个字段中的*自动 ID* 链接。这将生成一个唯一的 ID。

接下来，我们需要添加以下字段:

| 田 | 类型 | 价值 | 屏幕上显示程序运行的图片 |
| --- | --- | --- | --- |
| createdAt | 时间戳 | 当前 | ![Adding a new collection to the Firebase Cloud Firestore:Step 3](img/9c9fa5d4b31050472b89c887054f6a07.png) |
| updatedAt | 时间戳 | 当前 | ![Adding a new collection to the Firebase Cloud Firestore:Step 4](img/639598c938f3e7ac8130db66417e8d36.png) |
| 标题 | 线 | 这是 Firebase 的第一篇文章 | ![Adding a new collection to the Firebase Cloud Firestore:Step 5](img/25dfb68d78f102d78dae6eec00241ed6.png) |
| upvotescout | 数字 | Zero | ![Adding a new collection to the Firebase Cloud Firestore:Step 6](img/51604f086bde6f9fa7c142a70c7e78c0.png) |
| 唐沃特斯科特 | 数字 | Zero | ![Adding a new collection to the Firebase Cloud Firestore:Step 7](img/fd8a61f89383616ce7772f1133fe1bc4.png) |

这就是我们的系列最终的样子:

![Adding a new collection to the Firebase Cloud Firestore:Step 8](img/552d75b69c1494c277b6beea38e0c393.png)

点击*保存*按钮。将创建收藏，您将被重定向到项目的仪表板。

![Adding a new collection to the Firebase Cloud Firestore:Step 9](img/b3845f070a3412350289469ee5eb4b62.png)

## 更新 Firebase 云 Firestore 的规则

如果我们访问*规则*选项卡，我们将看到以下规则:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if false;
    }
  }
} 
```

![Default Firebase Cloud Firestore rules](img/d0ecabfa411a8c8c518814f0485be2ac.png)

我们需要对此进行修改，以允许写操作:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
} 
```

最后，点击*发布*按钮保存我们修改后的规则:

![Saving Firebase Cloud Firestore rules](img/41170dd8c32821cac6e1ab514534d1f2.png)

*注意:更多关于安全规则的信息可以在[这里](https://cloud.google.com/firestore/docs/security/get-started)找到。*

## 将 Firebase 与 Create React 应用程序集成

在本节中，我们将在 React 应用程序上添加以下内容:

1.  添加 Chakra UI 包
2.  查看所有帖子的选项
3.  添加新帖子的选项
4.  用户对帖子投票后，可以选择禁用投票按钮

### 添加 Chakra UI 包

我们将添加 [Chakra UI](https://next.chakra-ui.com/) 包来帮助我们构建应用程序的 UI。这是一个简单、模块化和可访问的 React 组件库。如果你想了解更多，你可以看看他们的[入门指南](https://next.chakra-ui.com/docs/getting-started)。

我们可以使用以下命令安装 Chakra UI:

```
npm install @chakra-ui/core@next 
```

为了让 Chakra UI 正常工作，我们需要在应用程序的根目录设置 [ChakraProvider](https://next.chakra-ui.com/docs/getting-started#setup-provider) 。像这样修改`src/index.js`:

```
import { ChakraProvider } from "@chakra-ui/core";
import React from "react";
import ReactDOM from "react-dom";
import App from "./app";

ReactDOM.render(
  <React.StrictMode>  <ChakraProvider>  <App />  </ChakraProvider>  </React.StrictMode>,
  document.getElementById("root")
); 
```

### 添加查看所有帖子的选项

在这一部分，我们将开发一个列表来显示我们在 Firebase 上的所有帖子。我们需要修改我们的`src/app.js`文件，如下所示:

```
import { Container, Flex, Spinner, VStack } from "@chakra-ui/core";
import React, { useEffect, useState } from "react";
import Post from "./components/post";
import db from "./lib/firebase";

const App = () => {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    // Hook to handle the initial fetching of posts

    db.collection("posts")
      .orderBy("createdAt", "desc")
      .get()
      .then((querySnapshot) => {
        const data = querySnapshot.docs.map((doc) => ({
          id: doc.id,
          ...doc.data(),
        }));

        setPosts(data);
      });
  }, []);

  return (
    <>  <Container maxW="md" centerContent p={8}>  <VStack spacing={8} w="100%">  {posts.map((post) => (
            <Post post={post} key={post.id} />
          ))}  </VStack>  </Container>  </>
  );
};

export default App; 
```

在这里，我们正在做以下事情:

1.  挂钩 [useEffect](https://reactjs.org/docs/hooks-effect.html) 负责从 Firebase 获取初始的一组帖子。希望查询语法相对简单。你可以在这里阅读更多关于在云商店[执行查询的信息。](https://firebase.google.com/docs/firestore/query-data/queries#node.js)
2.  一旦从 Firebase 中获取了帖子，我们就将所有帖子存储在`posts`状态中。
3.  我们通过使用`Post`组件来呈现帖子列表。
4.  `Post`组件负责处理单个帖子的渲染。

接下来，我们需要创建一个包含以下内容的新文件`src/components/post.js`:

```
import { Box, HStack, Text } from "@chakra-ui/core";
import React from "react";

const Post = ({ post }) => {
  return (
    <HStack key={post.id} w="100%" alignItems="flex-start">  <Box bg="gray.100" p={4} rounded="md" w="100%">  <Text>{post.title}</Text>  </Box>  </HStack>
  );
};

export default Post; 
```

这里没什么事情。该组件通过 props 接收帖子，并在一个 [Chakra UI 文本元素](https://chakra-ui.com/text)中显示其标题。

使用`Ctrl` + `C`重启 dev 服务器，然后访问 [http://localhost:3000/](http://localhost:3000/) 。我们应该能够查看我们在 Firestore 中手动输入的帖子。

![Rendering a post on the UI](img/6621da9d3c774d779fb4fdb75f566a96.png)

### 添加添加新帖子的选项

在这一节中，我们将开发一个模型，通过它我们可以添加一篇新文章。为此，我们需要将以下代码添加到我们的`src/app.js`文件中:

```
...
import Navbar from "./components/navbar";
...

const App = () => {
  ...

  return (
    <>  <Navbar />  <Container maxW="md" centerContent p={8}> ... </Container>  </>
  );
}; 
```

我们还需要添加一个包含以下内容的新文件`src/components/navbar.js`:

```
import { Box, Container, Flex } from "@chakra-ui/core";
import React from "react";
import AddNewPost from "./add-new-post";

const Navbar = () => {
  return (
    <Box position="sticky" top={0} p={4} bg="gray.100" zIndex={1}>  <Container maxW="md" centerContent>  <Flex justifyContent="flex-end" w="100%" position="sticky" top={0}>  <AddNewPost />  </Flex>  </Container>  </Box>
  );
};

export default Navbar; 
```

我们还需要添加一个包含以下内容的新文件`src/components/add-new-post.js`:

```
import {
  Button,
  FormControl,
  FormLabel,
  Textarea,
  Modal,
  ModalBody,
  ModalCloseButton,
  ModalContent,
  ModalFooter,
  ModalHeader,
  ModalOverlay,
  HStack,
  useDisclosure,
} from "@chakra-ui/core";
import React, { useState, useEffect } from "react";
import db from "../lib/firebase";

const AddNewPost = () => {
  const { isOpen, onOpen, onClose } = useDisclosure();
  const [title, setTitle] = useState("");
  const [isSaving, setSaving] = useState(false);

  const handleSubmit = async () => {
    const date = new Date();

    await db.collection("posts").add({
      title,
      upVotesCount: 0,
      downVotesCount: 0,
      createdAt: date.toUTCString(),
      updatedAt: date.toUTCString(),
    });

    onClose();
    setTitle("");
  };

  return (
    <>  <Button onClick={onOpen} colorScheme="blue"> Add new post </Button>  <Modal onClose={onClose} isOpen={isOpen} isCentered>  <ModalOverlay>  <ModalContent>  <ModalHeader>Add new post</ModalHeader>  <ModalCloseButton />  <ModalBody>  <FormControl id="post-title">  <FormLabel>Post title</FormLabel>  <Textarea
                  type="post-title"
                  value={title}
                  onChange={(e) => setTitle(e.target.value)}
                />  </FormControl>  </ModalBody>  <ModalFooter>  <HStack spacing={4}>  <Button onClick={onClose}>Close</Button>  <Button
                  onClick={handleSubmit}
                  colorScheme="blue"
                  disabled={!title.trim()}
                  isLoading={isSaving}
                > Save </Button>  </HStack>  </ModalFooter>  </ModalContent>  </ModalOverlay>  </Modal>  </>
  );
};

export default AddNewPost; 
```

`AddNewPost`组件将负责打开一个模态来添加一个新的帖子。我们利用 [Chakra 的使用过的关闭钩子](https://chakra-ui.com/usedisclosure)，一个定制钩子来帮助处理常见的打开、关闭或切换场景。

现在，如果我们访问 [http://localhost:3000/](http://localhost:3000/) ，我们应该能够查看以下内容:

![Rendering the navbar on the UI](img/f8953bdc9e6127fa40b0e09189710024.png)

如果我们点击*添加新帖子*按钮，将出现一个模式，通过该模式我们可以添加新帖子:

![Adding a new post](img/58d68ca0d4a337ba68f4f706c466a7e0.png)

但是，我们需要刷新页面来查看新帖子。我们可以通过在我们的`src/app.js`文件中添加一个新的`useEffect`钩子来解决这个问题:

```
// src/app.js

  useEffect(() => {
    // Hook to handle the real-time updating of posts whenever there is a
    // change in the datastore (https://firebase.google.com/docs/firestore/query-data/listen#view_changes_between_snapshots)

    db.collection("posts")
      .orderBy("createdAt", "desc")
      .onSnapshot((querySnapshot) => {
        const _posts = [];

        querySnapshot.forEach((doc) => {
          _posts.push({
            id: doc.id,
            ...doc.data(),
          });
        });

        setPosts(_posts);
      });
  }, []); 
```

现在，如果我们添加一个新帖子，它将实时可见。

![Adding a new post and viewing it in real-time](img/ee7109b046dc8691bcdee74a1c7dd721.png)

### 添加对帖子投票的选项

在本节中，我们将开发一些按钮，用户可以通过这些按钮对每篇文章进行投票。为此，我们需要将以下代码添加到我们的`src/components/post.js`文件中:

```
...
import VoteButtons from "./vote-buttons";

const Post = ({ post }) => {
  return (
    <HStack key={post.id} w="100%" alignItems="flex-start">  <VoteButtons post={post} /> ... </HStack>
  );
};

export default Post; 
```

接下来，我们需要添加一个新文件`src/components/vote-buttons.js`,内容如下:

```
// src/components/vote-buttons.js

import { IconButton, Text, VStack } from "@chakra-ui/core";
import React, { useState } from "react";
import { FiArrowDown, FiArrowUp } from "react-icons/fi";
import db from "../lib/firebase";

const VoteButtons = ({ post }) => {
  const handleClick = async (type) => {
    // Do calculation to save the vote.
    let upVotesCount = post.upVotesCount;
    let downVotesCount = post.downVotesCount;

    const date = new Date();

    if (type === "upvote") {
      upVotesCount = upVotesCount + 1;
    } else {
      downVotesCount = downVotesCount + 1;
    }

    await db.collection("posts").doc(post.id).set({
      title: post.title,
      upVotesCount,
      downVotesCount,
      createdAt: post.createdAt,
      updatedAt: date.toUTCString(),
    });
  };

  return (
    <>  <VStack>  <IconButton
          size="lg"
          colorScheme="purple"
          aria-label="Upvote"
          icon={<FiArrowUp />}
          onClick={() => handleClick("upvote")}
        />  <Text bg="gray.100" rounded="md" w="100%" p={1}>  {post.upVotesCount}  </Text>  </VStack>  <VStack>  <IconButton
          size="lg"
          colorScheme="yellow"
          aria-label="Downvote"
          icon={<FiArrowDown />}
          onClick={() => handleClick("downvote")}
        />  <Text bg="gray.100" rounded="md" w="100%" p={1}>  {post.downVotesCount}  </Text>  </VStack>  </>
  );
};

export default VoteButtons; 
```

`VoteButtons`组件负责呈现向上投票和向下投票按钮。当用户点击这两个按钮中的任何一个时，就会调用`handleClick`函数。`handleClick`函数负责将投票保存到数据库中。

因为我们使用了[反应图标](https://react-icons.github.io/react-icons/)中的图标，我们需要添加这个包。我们可以通过从根目录运行以下命令来实现这一点:

```
npm install react-icons 
```

现在，如果我们访问 [http://localhost:3000/](http://localhost:3000/) ，我们应该能够查看以下内容:

![Adding the voting buttons](img/bd54519d00eb9c425977073584a02f42.png)

我们应该可以对任何帖子进行投票:

![Voting on each post](img/a6223d72459b13d8e763f25da75fb072.png)

### 增加了一个选项，一旦用户投票后，禁用投票按钮

在上一节中，我们添加了对帖子投票的选项。然而，我们可以看到一个用户可以对一个帖子投票多次。我们可以通过在用户已经投票后禁用投票按钮来解决这个问题。

为此，我们需要将以下代码添加到我们的`src/component/vote-buttons.js`文件中:

```
import React, { useEffect, useState } from "react";

...
const VoteButtons = ({ post }) => {
  const [isVoting, setVoting] = useState(false);
  const [votedPosts, setVotedPosts] = useState([]);

  useEffect(() => {
    // Fetch the previously voted items from localStorage. See https://stackoverflow.com/a/52607524/1928724 on why we need "JSON.parse" and update the item on localStorage. Return "true" if the user has already voted the post.
    const votesFromLocalStorage = localStorage.getItem("votes") || [];
    let previousVotes = [];

    try {
      // Parse the value of the item from localStorage. If the value of the
      // items isn't an array, then JS will throw an error.
      previousVotes = JSON.parse(votesFromLocalStorage);
    } catch (error) {
      console.error(error);
    }

    setVotedPosts(previousVotes);
  }, []);

  const handleDisablingOfVoting = (postId) => {
    // This function is responsible for disabling the voting button after a
    // user has voted. Fetch the previously voted items from localStorage. See
    // https://stackoverflow.com/a/52607524/1928724 on why we need "JSON.parse"
    // and update the item on localStorage.
    const previousVotes = votedPosts;
    previousVotes.push(postId);

    setVotedPosts(previousVotes);

    // Update the voted items from localStorage. See https://stackoverflow.com/a/52607524/1928724 on why we need "JSON.stringify" and update the item on localStorage.
    localStorage.setItem("votes", JSON.stringify(votedPosts));
  };

  const handleClick = async (type) => {
    setVoting(true);
    ...
    // Disable the voting button once the voting is successful.
    handleDisablingOfVoting(post.id);

    setVoting(true);
  };

  const checkIfPostIsAlreadyVoted = () => {
    if (votedPosts.indexOf(post.id) > -1) {
      return true;
    } else {
      return false;
    }
  };

  return (
    <>  <VStack>  <IconButton
          ...
          isLoading={isVoting}
          isDisabled={checkIfPostIsAlreadyVoted()}
        /> ... </VStack>  <VStack>  <IconButton
          ...
          isLoading={isVoting}
          isDisabled={checkIfPostIsAlreadyVoted()}
        /> ... </VStack>  </>
  );
};

export default VoteButtons; 
```

在上述变化中，我们做了以下工作:

1.  我们正在跟踪在我们的[本地存储库](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)中已经投票的`id`帖子。
2.  在一个帖子被投票后，我们将该帖子的`id`添加到我们的`localStorage`中。
3.  当用户在帖子上投票后，我们将禁用投票按钮。当应用程序呈现任何投票的职位将被默认禁用。

![Disabling the vote button after casting a vote](img/a19f2bec6230614fed0519cc3cf7ac86.png)

请注意，通常您会将这类信息存储在数据库中。不幸的是，这超出了我们应用程序的范围，因为这意味着我们需要实现一个完整的用户管理和认证系统。

## 将我们的代码更改推送到 GitHub

我们现在已经完成了向应用程序添加所有特性的工作。在本节中，我们将提交我们的代码，并将其推送到 [GitHub](https://github.com/) 。

### 创建 GitHub 帐户

由于我们将在 GitHub 上存储我们的代码，我们将需要一个 [GitHub 帐户](https://github.com/join)。请注意，当我们在 Vercel 上部署应用程序时，这将是必需的。

### 使用 Git 提交我们的代码

下一步你需要在你的电脑上安装 [Git](https://git-scm.com) 。如果你对 Git 不熟悉，或者你想复习一下，请查看 SitePoint Premium 上的 *[Jump Start Git，第二版](https://www.sitepoint.com/premium/books/jump-start-git-2nd-edition/)* 。

从根目录中，我们可以运行以下命令来存放所有文件:

```
git add --all 
```

*注:更多关于`git add`的信息可从[这里](https://git-scm.com/docs/git-add)获得。*

接下来，我们可以使用以下命令提交文件:

```
git commit -m "Adds all the necessary code" 
```

*注:更多关于`git commit`的信息可从[这里](https://git-scm.com/docs/git-commit)获得。*

### 创建新的 GitHub 资源库

我们可以通过访问 https://github.com/new 来创建一个新的 GitHub 库。

![Creating a new GitHub repository](img/c8736be6a547f3d1d770b7d77160ead7.png)

一旦我们向存储库添加了一个名称，我们就可以点击 *Create repository* 按钮来创建一个新的存储库。

### 将代码推送到我们的 GitHub 库

我们可以使用以下命令将代码推送到 GitHub 存储库:

```
git remote add origin https://github.com/ghoshnirmalya/reddit-clone-app.git
git branch -M main
git push -u origin main 
```

*注意:您需要将“https://github . com/site point-editors/Reddit-clone . git”替换为您的 GitHub 存储库的链接。*

![Pushing the code to our new GitHub repository](img/f245f80f2603b2bad828d5e307bd4a64.png)

仅此而已。我们的应用程序现在处于版本控制之下，并被推送到 GitHub！

## 将应用程序部署到 Vercel

在最后一部分，我们将把代码部署到 [Vercel](https://vercel.com/) 中。

### 创建 Vercel 帐户

首先，前往 Vercel 并[创建一个账户](https://vercel.com/signup)。可以用 GitHub、GitLab、BitBucket 登录。

### 将 Git 存储库导入到 Vercel

我们可以通过点击*导入 Git 库*部分的*继续*按钮来[从 GitHub 导入](https://vercel.com/import)我们的 GitHub 库。

![Importing a Git project to Vercel: Step 1](img/8294b93cd4eb0ab4aa3bae821050d631.png)

接下来，我们需要输入 GitHub 项目的链接，并点击*继续*按钮来部署我们的应用程序。

![Importing a Git project to Vercel: Step 2](img/e750c0b1a02ee63d72f35b50cdf909be.png)

为了让我们的 React 应用程序能够与我们的后端通信，我们需要从我们的`.env`文件中输入所有的环境变量。

![Importing a Git project to Vercel: Step 3](img/5df0041cc95cfe245ad71bd6e5d11107.png)

它应该包含环境变量。

![Importing a Git project to Vercel: Step 4](img/9e673f58993e6ab51530a993ff65a1ca.png)

接下来，我们可以单击 *Deploy* 按钮，这将部署应用程序。

如果我们现在访问部署链接，我们应该能够查看我们部署的应用程序:

![Final UI of our application](img/72b43d73ebad0cd11fa99bd4ef7583f9.png)

## 结论

我们应用程序的现场演示部署在 [Vercel](https://reddit-clone-snowy.vercel.app/) 上，代码可在 [GitHub](https://github.com/sitepoint-editors/reddit-clone) 上获得。

为了减少复杂性和教程的长度，我们没有添加任何身份验证，但显然任何现实世界的应用程序都会需要它。

如果您不想创建和维护一个单独的后端应用程序，或者您想要实时数据而不需要花费太多时间开发 API，那么 Firebase 非常有用。

希望这篇教程对你以后的项目有所帮助。如果有任何反馈，请随时联系我们。

## 分享这篇文章