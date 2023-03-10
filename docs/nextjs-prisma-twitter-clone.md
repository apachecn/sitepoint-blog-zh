# 使用 TypeScript、Prisma 和 Next.js 构建一个 Twitter 克隆

> 原文：<https://www.sitepoint.com/nextjs-prisma-twitter-clone/>

学习 React 这样的工具的最好方法是用它来构建一些东西。 [Next.js](https://nextjs.org/) 是一个强大的框架，帮助你为生产而构建。在本教程中，我们将学习如何使用 [Next.js](https://nextjs.org/) 和 [Prisma](https://www.prisma.io/) 构建 Twitter 的克隆。

我们的应用程序将具有以下功能:

*   使用 [NextAuth](https://next-auth.js.org/) 和 Twitter OAuth 进行认证
*   添加新推文的选项
*   查看推文列表的选项
*   一个选项，查看用户的个人资料，只有他们的推文

我们将要构建的应用程序的代码可以在 [GitHub](https://github.com/sitepoint-editors/twitter-clone-using-nextjs-and-prisma) 上获得。我们将使用[打字稿](https://www.sitepoint.com/typescript-tutorial-for-beginners/)来构建我们的应用程序。

## 预赛

[Next.js](https://nextjs.org/) 是最流行的 [React.js](https://reactjs.org/) 框架之一。它有很多特性，比如服务器端渲染、[类型脚本支持](https://nextjs.org/docs/basic-features/typescript)、[图像优化](https://nextjs.org/docs/basic-features/image-optimization)、 [I18n 支持](https://nextjs.org/docs/advanced-features/i18n-routing)、[文件系统路由](https://nextjs.org/docs/routing/introduction)等等。

Prisma 是 Node.js 和 TypeScript 的一个 [ORM](https://stackoverflow.com/questions/1279613/what-is-an-orm-how-does-it-work-and-how-should-i-use-one) 。它还提供了许多特性，如原始数据库访问、无缝关系 API、原生数据库类型等等。

### 所需软件

为了运行我们的应用程序，我们需要安装以下软件:

*   [码头工人](https://docs.docker.com/get-docker/)
*   [npm](https://nodejs.org/en/download/)
*   [纱线](https://classic.yarnpkg.com/en/docs/install/)
*   [去](https://git-scm.com/downloads)

这些技术将在应用程序中使用:

*   [Next.js](https://nextjs.org/) :用于构建我们的应用
*   [Prisma](https://www.prisma.io/) :用于将数据提取并保存到数据库中
*   [Chakra UI](http://chakra-ui.com/) :为我们的应用添加风格
*   [NextAuth](https://next-auth.js.org/) :用于处理认证
*   [React Query](https://react-query.tanstack.com/) :用于获取和更新我们应用中的数据

## 创建新的 Next.js 应用程序

现在，让我们开始吧！我们将首先通过从终端运行以下命令来创建一个新的 Next.js 应用程序:

```
yarn create next-app 
```

当命令提示时，我们需要输入应用程序的名称。我们可以给它起任何我们想要的名字。然而，在这种情况下，我将把它命名为 **twitter-clone** 。我们应该能够在终端上看到类似的输出:

```
$ yarn create next-app

yarn create v1.22.5
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 🔨  Building fresh packages...

success Installed "create-next-app@10.0.4" with binaries:
      - create-next-app
✔ What is your project named? twitter-clone
Creating a new Next.js app in /twitter-clone.

....

Initialized a git repository.

Success! Created twitter-clone at /twitter-clone
Inside that directory, you can run several commands:

  yarn dev
    Starts the development server.

  yarn build
    Builds the app for production.

  yarn start
    Runs the built app in production mode.

We suggest that you begin by typing:

  cd twitter-clone
  yarn dev 
```

我们现在可以进入 **twitter-clone** 目录，通过运行以下命令启动我们的应用程序:

```
cd twitter-clone && yarn dev 
```

我们的 Next.js 应用程序应该在 [http://localhost:3000](http://localhost:3000/) 上启动并运行。我们应该能够看到以下屏幕:

![Next.js app running on localhost:3000](img/77c1dec25362fcb48fca947c07f00b5d.png)

## 添加 Dockerized PostgreSQL 数据库

接下来，让我们添加一个[Dockerized](https://hub.docker.com/_/postgres)[PostgreSQL](https://www.postgresql.org/)数据库，这样我们可以将用户和推文保存到其中。我们可以在应用程序的根目录下创建一个新的`docker-compose.yml`文件，内容如下:

```
version: "3"

services:
  db:
    container_name: db
    image: postgres:11.3-alpine
    ports:
      - "5432:5432"
    volumes:
      - db_data:/var/lib/postgresql/data
    restart: unless-stopped

volumes:
  db_data: 
```

如果 Docker 正在我们的机器上运行，我们可以从应用程序的根目录执行以下命令来启动 PostgreSQL 容器:

```
docker-compose up 
```

上面的命令将启动 PostgreSQL 容器，并且可以在`postgresql://postgres:@localhost:5432/postgres`访问它。注意，你也可以使用 Postgres 的[本地安装来代替 Dockerized。](https://www.postgresql.org/download/)

## 添加 Chakra UI

[Chakra UI](https://chakra-ui.com/) 是一个非常简单的 React.js 组件库。它非常受欢迎，具有可访问性、支持明暗模式等特性。我们将使用 Chakra UI 来设计我们的用户界面。我们可以通过从应用程序根目录运行以下命令来安装该软件包:

```
yarn add @chakra-ui/react @emotion/react @emotion/styled framer-motion 
```

让我们在`pages`目录中将`_app.js`文件重命名为`_app.tsx`,并用以下内容替换它的内容:

```
// pages/_app.tsx

import { ChakraProvider } from "@chakra-ui/react";
import { AppProps } from "next/app";
import Head from "next/head";
import React from "react";

const App = ({ Component, pageProps }: AppProps) => {
  return (
    <>
      <Head>
        <link rel="shortcut icon" hrefimg/favicon.ico" />
      </Head>
      <ChakraProvider>
        <Component {...pageProps} />
      </ChakraProvider>
    </>
  );
};

export default App; 
```

因为我们添加了一个新的 TypeScript 文件，所以我们需要重启 Next.js 服务器。一旦我们重新启动我们的服务器，我们将得到以下错误:

```
$ yarn dev

yarn run v1.22.5
$ next dev
ready - started server on http://localhost:3000
It looks like you're trying to use TypeScript but do not have the required package(s) installed.

Please install typescript, @types/react, and @types/node by running:

  yarn add --dev typescript @types/react @types/node

If you are not trying to use TypeScript, please remove the tsconfig.json file from your package root (and any TypeScript files in your pages directory). 
```

这是因为我们添加了一个新的 TypeScript 文件，但是没有添加运行它们所必需的依赖项。我们可以通过安装缺失的依赖项来解决这个问题。从我们的应用程序的根目录，我们可以执行以下命令来安装缺少的依赖项:

```
yarn add --dev typescript @types/react @types/node 
```

现在，如果我们启动 Next.js 服务器，我们的应用程序应该编译为:

```
$ yarn dev

yarn run v1.22.5
$ next dev
ready - started server on http://localhost:3000
We detected TypeScript in your project and created a tsconfig.json file for you.

event - compiled successfully 
```

## 添加 NextAuth

[NextAuth](https://next-auth.js.org/) 是 Next.js 的认证库，简单易懂，默认情况下灵活安全。要在我们的应用程序中设置 NextAuth，我们需要从应用程序的根目录运行以下命令来安装它:

```
yarn add next-auth 
```

接下来，我们必须用以下内容更新我们的`pages/_app.tsx`文件:

```
// pages/_app.tsx

import { ChakraProvider } from "@chakra-ui/react";
import { Provider as NextAuthProvider } from "next-auth/client";
import { AppProps } from "next/app";
import Head from "next/head";
import React from "react";

const App = ({ Component, pageProps }: AppProps) => {
  return (
    <>
      <Head>
        <link rel="shortcut icon" hrefimg/favicon.ico" />
      </Head>
      <NextAuthProvider session={pageProps.session}>
        <ChakraProvider>
          <Component {...pageProps} />
        </ChakraProvider>
      </NextAuthProvider>
    </>
  );
};

export default App; 
```

这里，我们用`NextAuthProvider`包装我们的应用程序。接下来，我们必须在`pages/api/auth`目录下创建一个名为`[...nextauth].ts`的新文件，内容如下:

```
// pages/api/auth/[...nextauth].ts

import { NextApiRequest, NextApiResponse } from "next";
import NextAuth from "next-auth";
import Providers from "next-auth/providers";

const options = {
  providers: [
    Providers.Twitter({
      clientId: process.env.TWITTER_KEY,
      clientSecret: process.env.TWITTER_SECRET,
    }),
  ],
};

export default NextAuth(options); 
```

上面的文件将负责使用 [Next.js API routes](https://nextjs.org/docs/api-routes/introduction) 处理我们的认证。接下来，我们将在应用程序的根目录下创建一个名为`.env`的新字段，用以下内容存储所有环境变量:

```
DATABASE_URL="postgresql://postgres:@localhost:5432/postgres?synchronize=true"
NEXTAUTH_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:3000
TWITTER_KEY=""
TWITTER_SECRET="" 
```

Twitter 环境变量将从 [Twitter API](https://developer.twitter.com/en/portal/dashboard) 中生成。我们接下来会这么做。我们可以从 [Twitter 开发者仪表板](https://developer.twitter.com/en/portal/apps/new)创建一个新的 Twitter 应用。

1.  输入名称创建一个新的 Twitter 应用程序，然后点击**完成**按钮。

    ![Create a new Twitter app](img/db5df6bd7de4dc635a60bd259c9347df.png)

2.  在下一屏复制 **API 密钥**、 **API 密钥**和**不记名令牌**。

    ![The credentials of our Twitter app](img/bb06707cdf3a552cbe161fc42d4acb39.png)

3.  在下一个屏幕中，将 **App 权限**从**只读**更改为**读写**。

    ![Twitter app permissions](img/0e17cb583ab07b27346fa4c4ad7e7ef7.png)

4.  点击**认证设置**旁边的**编辑**按钮，启用**三脚认证**。

    ![Authentication settings for our Twitter app](img/f9188487d05abaafb79258cc0b1d90af.png)

5.  启用**三脚 OAuth** 和**向用户**请求电子邮件地址，并添加[http://localhost:3000/API/auth/Callback/Twitter](http://localhost:3000/api/auth/callback/twitter)作为**回调 URL** 。

    ![Edit the authentication settings of our Twitter app](img/3ecfdef8494f64066cdcfdeb3cf7ee3c.png)

6.  **网址**、**服务条款**和**隐私政策**文件可以是任何东西(分别为`https://yourwebsite.com`、`https://yourwebsite.com/terms`、`https://yourwebsite.com/privacy`)。

我们的三条腿 OAuth 现在应该启用了。

![Enable the 3-legged OAuth of our Twitter app](img/612d2a1b91485c2e92c99e1d01f9080f.png)

将来自**步骤 2** 的 **API 密钥**的值粘贴到 **TWITTER_KEY** 环境变量中，并将 **API 秘密密钥**的值粘贴到 **TWITTER_SECRET** 环境变量中。

我们的`.env`文件现在应该是这样的:

```
DATABASE_URL="postgresql://postgres:@localhost:5432/postgres"
NEXTAUTH_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:3000
TWITTER_KEY="1234" // Replace this with your own API key
TWITTER_SECRET="secret" // Replaces this with your own API secret key 
```

现在，如果我们重启 Next.js 服务器并访问[http://localhost:3000/API/auth/Sign in](http://localhost:3000/api/auth/signin)，我们应该能够看到**用 Twitter** 按钮登录:

![Sign in with Twitter button](img/c37e0dcc8e345e665051785f8e07f62f.png)

如果我们点击该按钮，我们将能够授权我们的 Twitter 应用程序，但我们将无法登录我们的应用程序。我们的终端将显示以下错误:

```
[next-auth][warn][jwt_auto_generated_signing_key]
https://next-auth.js.org/warnings#jwt_auto_generated_signing_key 
```

我们将在下一次添加和配置 Prisma 时解决这个问题。

## 添加和配置 Prisma

首先，我们需要安装所有必要的依赖项。我们可以通过从应用程序的根目录运行以下命令来实现这一点:

```
yarn add prisma @prisma/client 
```

接下来，让我们在`lib/clients`目录下创建一个名为`prisma.ts`的新文件，其内容如下:

```
// lib/clients/prisma.ts

import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

export default prisma; 
```

这个`PrismaClient`将在多个文件中重复使用。接下来，我们必须用以下内容更新我们的`pages/api/auth/[...nextauth].ts`文件:

```
....

import prisma from "../../../lib/clients/prisma";
import Adapters from "next-auth/adapters";

....

const options = {
  providers: [
    ....
  ],
  adapter: Adapters.Prisma.Adapter({ prisma }),
};

.... 
```

现在，如果我们访问[http://localhost:3000/API/auth/sign in](http://localhost:3000/api/auth/signin)，我们将在终端上得到以下错误:

```
Error: @prisma/client did not initialize yet. Please run "prisma generate" and try to import it again. 
```

要解决此问题，我们必须执行以下操作:

1.  从我们应用的根目录运行`npx prisma init`:

```
 $ npx prisma init

   Environment variables loaded from .env

   ✔ Your Prisma schema was created at prisma/schema.prisma.
     You can now open it in your favorite editor.

   warn Prisma would have added DATABASE_URL="postgresql://johndoe:randompassword@localhost:5432/mydb?schema=public" but it already exists in .env

   Next steps:
   1. Set the DATABASE_URL in the .env file to point to your existing database. If your database has no tables yet, read https://pris.ly/d/getting-started.
   2. Set the provider of the datasource block in schema.prisma to match your database: postgresql, mysql or sqlite.
   3. Run prisma introspect to turn your database schema into a Prisma data model.
   4. Run prisma generate to install Prisma Client. You can then start querying your database.

   More information in our documentation:
   https://pris.ly/d/getting-started 
```

2.  从我们应用的根目录运行`npx prisma generate`:

```
 $ npx prisma generate
                               4s
   Environment variables loaded from .env
   Prisma schema loaded from prisma/schema.prisma
   Error:
   You don't have any models defined in your schema.prisma, so nothing will be generated.
   You can define a model like this:

   model User {
     id    Int     @id @default(autoincrement())
     email String  @unique
     name  String?
   }

   More information in our documentation:
   https://pris.ly/d/prisma-schema 
```

3.  用 [NextAuth 期望](https://next-auth.js.org/schemas/adapters#prisma-schema)的模式更新`prisma/schema.prisma`文件:

```
 // prisma/schema.prisma

   generator client {
     provider = "prisma-client-js"
   }

   datasource db {
     provider = "postgresql"
     url      = env("DATABASE_URL")
   }

   model Account {
     id                 Int       @id @default(autoincrement())
     compoundId         String    @unique @map("compound_id")
     userId             Int       @map("user_id")
     providerType       String    @map("provider_type")
     providerId         String    @map("provider_id")
     providerAccountId  String    @map("provider_account_id")
     refreshToken       String?   @map("refresh_token")
     accessToken        String?   @map("access_token")
     accessTokenExpires DateTime? @map("access_token_expires")
     createdAt          DateTime  @default(now()) @map("created_at")
     updatedAt          DateTime  @default(now()) @map("updated_at")

     @@index([providerAccountId], name: "providerAccountId")
     @@index([providerId], name: "providerId")
     @@index([userId], name: "userId")
     @@map("accounts")
   }

   model Session {
     id           Int      @id @default(autoincrement())
     userId       Int      @map("user_id")
     expires      DateTime
     sessionToken String   @unique @map("session_token")
     accessToken  String   @unique @map("access_token")
     createdAt    DateTime @default(now()) @map("created_at")
     updatedAt    DateTime @default(now()) @map("updated_at")

     @@map("sessions")
   }

   model User {
     id            Int       @id @default(autoincrement())
     name          String?
     email         String?   @unique
     emailVerified DateTime? @map("email_verified")
     image         String?
     createdAt     DateTime  @default(now()) @map("created_at")
     updatedAt     DateTime  @default(now()) @map("updated_at")
     tweets        Tweet[]

     @@map("users")
   }

   model VerificationRequest {
     id         Int      @id @default(autoincrement())
     identifier String
     token      String   @unique
     expires    DateTime
     createdAt  DateTime @default(now()) @map("created_at")
     updatedAt  DateTime @default(now()) @map("updated_at")

     @@map("verification_requests")
   } 
```

4.  在`prisma/schema.prisma`文件中添加 Tweet 的模式:

```
 // prisma/schema.prisma

   ....

   model Tweet {
     id        Int      @id @default(autoincrement())
     body      String
     userId    Int
     createdAt DateTime @default(now()) @map("created_at")
     updatedAt DateTime @default(now()) @map("updated_at")
     author    User     @relation(fields: [userId], references: [id])

     @@map("tweets")
   } 
```

5.  从我们应用的根目录运行`npx prisma migrate dev --preview-feature`到[创建一个新的迁移](https://www.prisma.io/docs/guides/prisma-guides/prisma-migrate-guides/add-prisma-migrate-to-a-project)。出现提示时，输入迁移的名称(如 **init-database** )。

现在，如果我们访问[http://localhost:3000/API/auth/Sign in](http://localhost:3000/api/auth/signin)并点击**用 Twitter 登录**按钮，我们将使用 Twitter 登录我们的应用。

## 添加一些种子数据

因此，当我们在应用程序上工作时，UI 并不是完全裸露的，让我们添加一些种子数据。

让我们从安装几个依赖项开始:

```
yarn add -D faker ts-node 
```

这将引入 [faker.js](https://www.npmjs.com/package/faker) ，它将帮助我们生成假数据，以及它的 [ts-node](https://www.npmjs.com/package/ts-node) 依赖关系。

接下来，在`prisma`文件夹中创建一个新的`seed.ts`文件，并添加以下内容:

```
import faker from "faker";
import prisma from "../lib/clients/prisma";

async function main() {
  const listOfNewUsers = [...new Array(5)].map(() => {
    return {
      email: faker.internet.email(),
      name: faker.name.findName(),
      image: faker.image.image(),
      tweets: {
        create: {
          body: faker.lorem.sentence(),
        },
      },
    };
  });

  for (let data of listOfNewUsers) {
    const user = await prisma.user.create({
      data,
    });

    console.log(user);
  }
}

main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  }); 
```

我们还需要更新我们的`tsconfig.json`文件，如下所示:

```
{
  "compilerOptions": {
    "target": "es5",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": false,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "commonjs",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "baseUrl": ".",
    "paths": {
      "*": [
        "/*"
      ],
      "components/*": [
        "components/*"
      ],
      "pages/*": [
        "pages/*"
      ],
      "types/*": [
        "types/*"
      ],
      "lib/*": [
        "lib/*"
      ],
    },
  },
  "include": [
    "next-env.d.ts",
    "**/*.ts",
    "**/*.tsx"
  ],
  "exclude": [
    "node_modules"
  ]
} 
```

最后，我们可以运行`npx prisma db seed --preview-feature`来为我们的数据库植入一些测试数据。

## 添加反应查询

[React Query](https://react-query.tanstack.com/) 是在 React.js 应用程序中获取数据的一种非常流行和高效的方式。让我们将 React Query 添加到应用程序中。我们可以通过从应用程序的根目录运行以下命令来安装 React Query:

```
yarn add react-query 
```

接下来，让我们在`lib/clients`目录下创建一个名为`react-query.ts`的新文件，其内容如下:

```
// lib/clients/react-query.ts

import { QueryClient } from "react-query";

const queryClient = new QueryClient();

export default queryClient; 
```

我们还需要用以下内容更新我们的`pages/_app.tsx`文件:

```
// pages/_app.tsx

....

import { QueryClientProvider } from "react-query";
import { Hydrate } from "react-query/hydration";
import queryClient from "../lib/clients/react-query";

const App = ({ Component, pageProps }: AppProps) => {
  return (
    <QueryClientProvider client={queryClient}>
      <Hydrate state={pageProps.dehydratedState}>
        <Head>
          <link rel="shortcut icon" hrefimg/favicon.ico" />
        </Head>
        <NextAuthProvider session={pageProps.session}>
          <ChakraProvider>
            <Component {...pageProps} />
          </ChakraProvider>
        </NextAuthProvider>
      </Hydrate>
    </QueryClientProvider>
  );
};

export default App; 
```

这里，我们用 [QueryClientProvider](https://react-query.tanstack.com/reference/QueryClientProvider#_top) 包装我们的应用程序，它将为我们的应用程序提供一个`QueryClient`。

## 查看推文列表的选项

让我们在`lib/queries`目录下创建一个名为`fetch-tweets.ts`的新文件，内容如下:

```
// lib/queries/fetch-tweets.ts

const fetchTweets = async () => {
  const res = await fetch(`${process.env.NEXT_PUBLIC_API_URL}/api/tweets`);
  const data = await res.json();

  return data;
};

export default fetchTweets; 
```

这个函数将负责获取我们的应用程序中的所有推文。接下来，在`pages`目录中创建一个名为`tweets.tsx`的新文件，其内容如下:

```
// pages/tweets.tsx

import fetchTweets from "../lib/queries/fetch-tweets";
import queryClient from "../lib/clients/react-query";
import { GetServerSideProps, InferGetServerSidePropsType } from "next";
import { useSession } from "next-auth/client";
import Head from "next/head";
import React from "react";
import { useQuery } from "react-query";
import { dehydrate } from "react-query/hydration";

const TweetsPage: InferGetServerSidePropsType<
  typeof getServerSideProps
> = ({}) => {
  const { data } = useQuery("tweets", fetchTweets);
  const [session] = useSession();

  if (!session) {
    return <div>Not authenticated.</div>;
  }

  return (
    <>
      <Head>
        <title>All tweets</title>
      </Head>
      {console.log(JSON.stringify(data, null, 2))}
    </>
  );
};

export const getServerSideProps: GetServerSideProps = async ({ req }) => {
  await queryClient.prefetchQuery("tweets", fetchTweets);

  return {
    props: {
      dehydratedState: dehydrate(queryClient),
    },
  };
};

export default TweetsPage; 
```

[getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching#getserversideprops-server-side-rendering) 是一个 Next.js 函数，帮助获取服务器上的数据。让我们在`pages/api/tweets`目录下创建一个名为`index.ts`的新文件，其内容如下:

```
// pages/api/tweets/index.ts

import prisma from "../../../lib/clients/prisma";
import type { NextApiRequest, NextApiResponse } from "next";

export default async (req: NextApiRequest, res: NextApiResponse) => {
  if (req.method === "POST") {
    try {
      const { body } = req;
      const tweet = await prisma.tweet.create({ data: JSON.parse(body) });

      return res.status(200).json(tweet);
    } catch (error) {
      return res.status(422).json(error);
    }
  } else if (req.method === "GET") {
    try {
      const tweets = await prisma.tweet.findMany({
        include: {
          author: true,
        },
        orderBy: [
          {
            createdAt: "desc",
          },
        ],
      });

      return res.status(200).json(tweets);
    } catch (error) {
      return res.status(422).json(error);
    }
  }

  res.end();
}; 
```

在这里，我们正在检查请求。如果是一个`POST`请求，我们将创建一个新的 tweet。如果是一个`GET`请求，我们将发送包含作者详细信息的所有推文。现在，如果我们访问[http://localhost:3000/tweets](http://localhost:3000/tweets)，我们将在浏览器的控制台中查看所有的推文。

![List of tweets from the API endpoint](img/2ebd0e175f5db5e2e07369bdd4400240.png)

请注意，由于 faker.js 会生成随机数据，因此您在浏览器控制台上看到的记录会与屏幕截图有所不同。我们稍后将添加添加推文的选项。

接下来，让我们构建显示推文列表的用户界面。我们可以在`components/pages/tweets`目录下创建一个名为`index.tsx`的新文件，内容如下:

```
// components/pages/tweets/index.tsx

import { Box, Grid, Stack } from "@chakra-ui/react";
import Tweet from "./tweet";
import React from "react";
import ITweet from "types/tweet";

const TweetsPageComponent = ({ tweets }) => {
  return (
    <Stack spacing={8}>
      <Grid templateColumns={["1fr", "1fr", "repeat(2, 1fr)"]} gap={8}>
        {tweets?.map((tweet: ITweet) => {
          return (
            <Box key={tweet.id}>
              <Tweet tweet={tweet} />
            </Box>
          );
        })}
      </Grid>
    </Stack>
  );
};

export default TweetsPageComponent; 
```

让我们在同一个目录(`components/pages/tweets`)中创建一个名为`tweet.tsx`的新文件，其内容如下:

```
// components/pages/tweets/tweet.tsx

import { Avatar, Box, Stack, Text } from "@chakra-ui/react";
import React, { FC } from "react";

const Tweet: FC = ({ tweet }) => {
  const authorNode = () => {
    return (
      <Stack
        spacing={4}
        isInline
        alignItems="center"
        p={4}
        borderBottomWidth={1}
      >
        <Avatar name={tweet.author.name} src={tweet.author.image} />
        <Stack>
          <Text fontWeight="bold">{tweet.author.name}</Text>
        </Stack>
      </Stack>
    );
  };

  const bodyNode = () => {
    return (
      <Text fontSize="md" p={4}>
        {tweet.body}
      </Text>
    );
  };

  return (
    <Box shadow="lg" rounded="lg">
      <Stack spacing={0}>
        {authorNode()}
        {bodyNode()}
      </Stack>
    </Box>
  );
};

export default Tweet; 
```

接下来，让我们用以下内容更新我们的`pages/tweets.tsx`文件:

```
// pages/tweets.tsx

....

import Page from "../components/pages/tweets";

....

const TweetsPage: InferGetServerSidePropsType<
  typeof getServerSideProps
> = ({}) => {

....

  return (
    <>
      <Head>
        <title>All tweets</title>
      </Head>
      <Page tweets={data} />
    </>
  );

....

}

.... 
```

这里，我们修改了应用程序的界面。现在，如果我们访问[http://localhost:3000/tweets](http://localhost:3000/tweets)，我们应该能够看到以下内容:

![List of tweets](img/732d90eafafc5c2c516f0fc5dee1248f.png)

## 添加新推文的选项

让我们添加一个文本区域，通过它我们可以添加一条新的 tweet。为此，让我们在`components/pages/tweets`目录中创建一个名为`add-new-tweet-form.tsx`的新文件，其内容如下:

```
// components/pages/tweets/add-new-tweet-form.tsx

import {
  Box,
  Button,
  FormControl,
  FormLabel,
  Stack,
  Textarea,
} from "@chakra-ui/react";
import saveTweet from "../../../lib/mutations/save-tweet";
import fetchTweets from "../../../lib/queries/fetch-tweets";
import queryClient from "../../../lib/clients/react-query";
import { useSession } from "next-auth/client";
import React, { ChangeEvent, useState } from "react";
import { useMutation, useQuery } from "react-query";

const AddNewTweetForm = () => {
  const [body, setBody] = useState("");
  const [session] = useSession();
  const { refetch } = useQuery("tweets", fetchTweets);
  const mutation = useMutation(saveTweet, {
    onSuccess: async () => {
      await queryClient.invalidateQueries("tweets");

      refetch();
    },
  });

  if (!session) {
    return <div>Not authenticated.</div>;
  }

  const handleSubmit = () => {
    const data = {
      body,
      author: {
        connect: { email: session.user.email },
      },
    };

    mutation.mutate(data);

    if (!mutation.error) {
      setBody("");
    }
  };

  return (
    <Stack spacing={4}>
      <Box p={4} shadow="lg" rounded="lg">
        <Stack spacing={4}>
          <FormControl isRequired>
            <FormLabel htmlFor="body">What's on your mind?</FormLabel>
            <Textarea
              id="body"
              value={body}
              onChange={(e: ChangeEvent<HTMLTextAreaElement>) =>
                setBody(e.currentTarget.value)
              }
            />
          </FormControl>
          <FormControl>
            <Button
              loadingText="Posting..."
              onClick={handleSubmit}
              isDisabled={!body.trim()}
            >
              Post
            </Button>
          </FormControl>
        </Stack>
      </Box>
    </Stack>
  );
};

export default AddNewTweetForm; 
```

[变异](https://react-query.tanstack.com/reference/useMutation#_top)函数负责向服务器发出`POST`请求。一旦请求成功，它还会重新获取数据。同样，让我们在`lib/mutations`目录下创建一个名为`save-tweet.ts`的新文件，其内容如下:

```
// lib/mutations/save-tweet.ts

const saveTweet = async (body: any) => {
  const res = await fetch(`${process.env.NEXT_PUBLIC_API_URL}/api/tweets`, {
    method: "POST",
    body: JSON.stringify(body),
  });
  const data = await res.json();

  return data;
};

export default saveTweet; 
```

我们还需要用以下内容修改我们的`components/pages/tweets/index.tsx`文件:

```
// components/pages/tweets/index.tsx

....

import AddNewTweetForm from "./add-new-tweet-form";

....

const TweetsPageComponent = ({ tweets }) => {
  return (
    <Stack spacing={8}>
      <Box>
        <AddNewTweetForm />
      </Box>

      ....

    </Stack>
  );
};

export default TweetsPageComponent; 
```

现在，如果我们访问[http://localhost:3000/tweets](http://localhost:3000/tweets)，我们应该能够查看文本区域:

![Textarea to add new tweets](img/734ab134a86822ea35f06fa1289e064f.png)

我们也应该能够添加一个新的推文使用文本区(*这不会推文到您的实际帐户！*):

![Add a new tweet](img/bb2e5fbc8aa2d9d0801340a3df134574.png)

接下来，我们将添加一个选项来查看用户的个人资料，其中只显示该用户发布的 tweets。

## 选择查看用户的个人资料，只有他们的推文

首先，我们将创建一个显示所有用户列表的页面。为此，我们需要在`pages/users`目录中创建一个名为`index.tsx`的新文件，其内容如下:

```
// pages/users/index.tsx

import { GetServerSideProps, InferGetServerSidePropsType } from "next";
import { useSession } from "next-auth/client";
import Head from "next/head";
import React from "react";
import { useQuery } from "react-query";
import { dehydrate } from "react-query/hydration";
import Page from "../../components/pages/users";
import queryClient from "../../lib/clients/react-query";
import fetchUsers from "../../lib/queries/fetch-users";

const MyAccountPage: InferGetServerSidePropsType<
  typeof getServerSideProps
> = ({}) => {
  const { data } = useQuery("users", fetchUsers);
  const [session] = useSession();

  if (!session) {
    return <div>Not authenticated.</div>;
  }

  return (
    <>
      <Head>
        <title>All users</title>
      </Head>
      <Page users={data} />
    </>
  );
};

export const getServerSideProps: GetServerSideProps = async ({ req }) => {
  await queryClient.prefetchQuery("users", fetchUsers);

  return {
    props: {
      dehydratedState: dehydrate(queryClient),
    },
  };
};

export default MyAccountPage; 
```

我们还需要在`lib/queries`目录下创建一个名为`fetch-users.ts`的新文件，内容如下:

```
// lib/queries/fetch-users.ts

const fetchUsers = async () => {
  const res = await fetch(`${process.env.NEXT_PUBLIC_API_URL}/api/users`);
  const data = await res.json();

  return data;
};

export default fetchUsers; 
```

该函数将负责从 API 端点获取所有用户。我们还需要在`components/pages/users`目录下创建一个名为`index.tsx`的新文件，内容如下:

```
// components/pages/users/index.tsx

import { Box, Grid, Stack } from "@chakra-ui/react";
import React from "react";
import User from "./user";

const UsersPageComponent = ({ users }) => {
  return (
    <Stack spacing={8}>
      <Grid templateColumns={["1fr", "1fr", "repeat(2, 1fr)"]} gap={8}>
        {users?.map((user) => {
          return (
            <Box key={user.id}>
              <User user={user} />
            </Box>
          );
        })}
      </Grid>
    </Stack>
  );
};

export default UsersPageComponent; 
```

接下来，让我们在同一个目录(`components/pages/users`)中创建一个名为`user.tsx`的文件，其内容如下:

```
// components/pages/users/user.tsx

import { Avatar, Box, Stack, Text, Button } from "@chakra-ui/react";
import Link from "next/link";
import React, { FC } from "react";

const User: FC = ({ user }) => {
  const authorNode = () => {
    return (
      <Stack
        spacing={4}
        isInline
        alignItems="center"
        p={4}
        borderBottomWidth={1}
      >
        <Avatar name={user.name} src={user.image} />
        <Stack>
          <Text fontWeight="bold">{user.name}</Text>
        </Stack>
      </Stack>
    );
  };

  const bodyNode = () => {
    return (
      <Text fontSize="md" p={4}>
        {user.email}
      </Text>
    );
  };

  const buttonNode = () => {
    return (
      <Box p={4} borderTopWidth={1}>
        <Link href={`/users/${user.id}`}>
          <Button>View profile</Button>
        </Link>
      </Box>
    );
  };

  return (
    <Box shadow="lg" rounded="lg">
      <Stack spacing={0}>
        {authorNode()}
        {bodyNode()}
        {buttonNode()}
      </Stack>
    </Box>
  );
};

export default User; 
```

在`pages/api/users`目录下还有一个名为`index.ts`的文件，其内容如下:

```
// pages/api/users/index.ts

import prisma from "../../../lib/clients/prisma";
import type { NextApiRequest, NextApiResponse } from "next";

export default async (req: NextApiRequest, res: NextApiResponse) => {
  if (req.method === "GET") {
    try {
      const users = await prisma.user.findMany({
        orderBy: [
          {
            createdAt: "desc",
          },
        ],
      });

      return res.status(200).json(users);
    } catch (error) {
      return res.status(422).json(error);
    }
  }

  res.end();
}; 
```

上述函数负责发送所有用户的详细信息。现在，如果我们访问[http://localhost:3000/users](http://localhost:3000/users)，我们应该能够看到用户列表:

![List of users](img/ff1edafdba6c2ba3206c6720c02cff86.png)

现在，让我们创建页面来显示单个用户的详细信息。为此，我们需要在`pages/users`目录中创建一个名为`[id].tsx`的新文件，其内容如下:

```
// pages/users/[id].tsx

import Page from "../../components/pages/users/[id]";
import queryClient from "../../lib/clients/react-query";
import fetchUser from "../../lib/queries/fetch-user";
import { GetServerSideProps, InferGetServerSidePropsType } from "next";
import { getSession, useSession } from "next-auth/client";
import Head from "next/head";
import React from "react";
import { useQuery } from "react-query";
import { dehydrate } from "react-query/hydration";

const MyAccountPage: InferGetServerSidePropsType<typeof getServerSideProps> = ({
  id,
}) => {
  const { data } = useQuery("user", () => fetchUser(parseInt(id as string)));
  const [session] = useSession();

  if (!session) {
    return <div>Not authenticated.</div>;
  }

  return (
    <>
      <Head>
        <title>{session.user.name}'s profile</title>
      </Head>
      <Page user={data} />
    </>
  );
};

export const getServerSideProps: GetServerSideProps = async ({ query }) => {
  await queryClient.prefetchQuery("user", () =>
    fetchUser(parseInt(query.id as string))
  );

  return {
    props: {
      dehydratedState: dehydrate(queryClient),
      id: query.id,
    },
  };
};

export default MyAccountPage; 
```

`query.id`的值决定了当前用户的`id`。我们还需要在`lib/queries`目录下创建一个名为`fetch-user.ts`的新文件，内容如下:

```
// lib/queries/fetch-user.ts

const fetchUser = async (userId: number) => {
  const res = await fetch(
    `${process.env.NEXT_PUBLIC_API_URL}/api/users/${userId}`
  );
  const data = await res.json();

  return data;
};

export default fetchUser; 
```

上面的函数将负责向 API 端点发出`GET`请求。接下来，我们需要在`components/pages/users/[id]`目录下创建一个名为`index.tsx`的新文件，内容如下:

```
// components/pages/users/[id]/index.tsx

import { Avatar, Box, Grid, Stack, Text } from "@chakra-ui/react";
import Tweet from "./tweet";
import React, { FC } from "react";

const UsersPageComponent: FC = ({ user }) => {
  const authorNode = () => {
    return (
      <Stack spacing={4} isInline alignItems="center">
        <Avatar name={user?.name} src={user?.image} />
        <Stack>
          <Text fontWeight="bold" fontSize="4xl">
            {user?.name}
          </Text>
        </Stack>
      </Stack>
    );
  };

  return (
    <Stack spacing={8}>
      {authorNode()}
      <Grid templateColumns={["1fr", "1fr", "repeat(2, 1fr)"]} gap={8}>
        {user?.tweets.map((tweet) => {
          return (
            <Box key={tweet.id}>
              <Tweet tweet={tweet} />
            </Box>
          );
        })}
      </Grid>
    </Stack>
  );
};

export default UsersPageComponent; 
```

接下来，我们需要在同一个目录(`components/pages/users/[id]`)中再创建一个名为`tweet.tsx`的文件，内容如下:

```
// components/pages/users/[id]/tweet.tsx

import { Box, Stack, Text } from "@chakra-ui/react";
import React, { FC } from "react";

const Tweet: FC = ({ tweet }) => {
  const bodyNode = () => {
    return (
      <Text fontSize="md" p={4}>
        {tweet.body}
      </Text>
    );
  };

  return (
    <Box shadow="lg" rounded="lg">
      <Stack spacing={0}>{bodyNode()}</Stack>
    </Box>
  );
};

export default Tweet; 
```

最后，我们需要在`pages/api/users`目录中再创建一个名为`[id].ts`的文件，其内容如下:

```
// pages/api/users/[id].ts

import prisma from "../../../lib/clients/prisma";
import type { NextApiRequest, NextApiResponse } from "next";

export default async (req: NextApiRequest, res: NextApiResponse) => {
  if (req.method === "GET") {
    const userId = parseInt(req.query.id as string);

    try {
      const tweets = await prisma.user.findUnique({
        include: {
          tweets: true,
        },
        where: {
          id: userId,
        },
      });

      return res.status(200).json(tweets);
    } catch (error) {
      console.log(error);

      return res.status(422).json(error);
    }
  }

  res.end();
}; 
```

上述函数将负责发送`id`与`req.query.id`相同的用户的详细信息。我们把它转换成一个数字，因为 Prisma 要求它是数字。现在，如果我们访问[http://localhost:3000/users](http://localhost:3000/users)并点击用户的**查看简介**按钮，我们将能够看到该用户发布的推文列表。

![Profile of a user with all the tweets posted by that user](img/c7d08dc2f33bfedd4773152d41664235.png)

## 结论

在本教程中，我们学习了如何一起使用 Next.js 和 Prisma 来构建 Twitter 的克隆。显然，Twitter 包含许多其他功能，如转发、评论和分享每条推文的功能。然而，本教程应该为构建这样的特性提供基础。

我们构建的应用程序的代码可以在 [GitHub](https://github.com/sitepoint-editors/twitter-clone-using-nextjs-and-prisma) 获得。请随意查看。你也可以在这里查看我们正在开发的应用程序的 **[现场演示](https://twitter-clone-using-nextjs-and-prisma.vercel.app/)** 。

## 分享这篇文章