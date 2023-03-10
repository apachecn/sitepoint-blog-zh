# 用 Bitbucket 云学习 Git

> 原文：<https://www.sitepoint.com/learn-git-with-bitbucket-cloud/>

*本文由 [Bitbucket](https://www.bitbucket.org/product?utm_source=sitepoint&utm_medium=paid-content&utm_content=learn-git-with-bitbucket&utm_campaign=bitbucket_adexp-bbtofu_sitepoint-syn-content) 赞助。感谢您对使 SitePoint 成为可能的赞助商的支持。*

## 目标

通过这篇空间主题教程学习 Git 的基础知识。

## 任务简介

你的任务是通过完成教程和追踪你的团队的所有空间站来学习 Git 的诀窍。本教程中涉及的命令:

*   git clone, git config, git add, git status, git commit, git push, git pull, git branch, git checkout, and git merge

| 时间 | 观众 | 先决条件 |
| --- | --- | --- |
| 30 分钟 | 您是 Git 和 Bitbucket 云的新手 | [你已经安装了 Git](https://www.atlassian.com/git/tutorials/install-git?utm_source=sitepoint&utm_medium=paid-content&utm_content=learn-git-with-bitbucket&utm_campaign=bitbucket_adexp-bbtofu_sitepoint-syn-content) |
|   |   | [您有一个比特币账户](https://bitbucket.org/account/signup?utm_source=sitepoint&utm_medium=paid-content&utm_content=learn-git-with-bitbucket&utm_campaign=bitbucket_adexp-bbtofu_sitepoint-syn-content) |

## 创建 Git 存储库

作为我们新的比特桶空间站管理员，你需要有条理。当你为你的空间站制作文件时，你会希望将它们保存在一个地方，并与队友共享，无论他们在宇宙中的什么地方。对于 Bitbucket，这意味着将所有内容添加到存储库中。让我们创造一个！

*   关于存储库的一些有趣的事实
    *   无论是处理一个文件还是多个文件，您都可以访问本地存储库中的所有文件。
    *   如果您有公共存储库的 URL，则可以在没有 Bitbucket 帐户的情况下查看该存储库。
    *   每个存储库属于一个用户帐户或一个团队。对于用户帐户，该用户拥有存储库。+在团队的情况下，该团队拥有它。
    *   存储库所有者是唯一可以删除存储库的人。如果存储库属于某个团队，管理员可以删除该存储库。
    *   一个代码项目可以由跨多个帐户的多个存储库组成，但也可以是来自单个帐户的单个存储库。
    *   每个存储库都有 2 GB 的大小限制，但是我们建议您的存储库不要超过 1 GB。

### 第一步。创建存储库

最初，您在 Bitbucket 中创建的存储库是空的，里面没有任何代码。没关系，因为你很快就会开始添加一些文件。这个位存储库将是您的文件的中央存储库，这意味着如果您授予其他人权限，他们就可以访问该存储库。创建存储库后，您将把一个版本复制到您的本地系统——这样您就可以从一个存储库中更新它，然后将这些更改转移到另一个存储库中。

![Git repo](img/982cfb54b6acefdb5634da7ce6ca3f27.png)

执行以下操作来创建存储库:

1.  From Bitbucket, click **Repositories > Create repository** button at the top of the page.

    ![Create repository](img/e5a7b6ed3cfe2eab4ad6b72140c85333.png)

    系统显示**创建新存储库**页面。花些时间查看对话框的内容。除了存储库类型之外，您在此页面上输入的所有内容以后都可以更改。

    ![Create a new repository](img/19fcc72662205a8c309073607e7b6273.png)

2.  在**名称**字段输入`BitbucketStationLocations`。Bitbucket 在存储库的 URL 中使用这个**名称**。例如，如果用户`the_best`有一个名为`awesome_repo`的存储库，那么这个存储库的 URL 就是`https://bitbucket.org/the_best/awesome_repo`。
3.  对于**访问级别**，将**这是一个私有存储库框选中**。私有存储库仅对您和您授权访问的人可见。如果未选中此框，每个人都可以看到您的存储库。
4.  为**存储库类型**选择 **Git** 。请记住，在您点击**创建存储库**之后，您不能更改存储库类型。
5.  点击**创建存储库**。Bitbucket 创建您的存储库并显示其**概述**页面。

### 第二步。探索您的新存储库

花一些时间探索您刚刚创建的存储库。您应该在存储库的**概述**页面上:

![Overview page](img/25a45a5be8705576d1135243bfca50be.png)

点击**导航**菜单上的项目，查看每个项目背后的内容。要查看可用于导航这些项目的快捷键，请按下**？**键盘上的键。

当您单击菜单栏上的 **Commits** 选项时，您会发现没有提交，因为您还没有为您的存储库创建任何内容。您的存储库是私有的，您没有邀请任何人访问该存储库，因此现在唯一可以创建或编辑存储库内容的人是您，即存储库所有者。

## 复制您的 Git 存储库并添加文件

现在您有了一个地方来添加和共享您的空间站文件，您需要一种从您的本地系统访问它的方法。为此，您需要将 Bitbucket 存储库复制到您的系统中。Git 将复制存储库称为“克隆”它。当您克隆一个存储库时，您在 Bitbucket 服务器(Git 称之为 origin)和您的本地系统之间创建了一个连接。

![Bitbucket server to Local system](img/ebba436d97aeab8711496ed8b82b4b05.png)

您将从终端使用一大堆 Git 和非 Git 命令。如果你以前从未使用过命令行，[在命令行速成班学习在哪里可以找到它](http://cli.learncodethehardway.org/book/ex1.html)。

### 第一步。将您的存储库克隆到本地系统

从桌面打开浏览器和终端窗口。打开终端窗口后，请执行以下操作:

1.  Navigate to your home (~) directory.

    ```
     $ cd ~ 

    ```

    随着您更多地使用 Bitbucket，您可能会在多个存储库中工作。因此，创建一个目录来包含所有这些存储库是一个好主意。

2.  创建一个目录来包含您的存储库。

    ```
     $ mkdir repos 

    ```

3.  从终端，将您想要工作的目录更新到新的 repos 目录。

    ```
     $ cd ~/repos 

    ```

4.  从 Bitbucket，转到您的**BitbucketStationLocations**存储库。
5.  Click **Clone**.

    系统会显示一个弹出克隆对话框。默认情况下，克隆对话框会根据您的设置将协议设置为 HTTPS 或 SSH。出于本教程的目的，不要更改您的默认协议。

    ![Clone](img/3f2432ff8614f67704f441feb5f8fd03.png)

6.  复制突出显示的克隆命令。
7.  在你的终端窗口中，粘贴你从 Bitbucket 中复制的命令，并按下 **Return** 。
8.  Enter your Bitbucket password when the terminal asks for it. If you created an account by linking to Google, use your password for that account.

    如果您遇到 Windows 密码错误:

    *   在 Microsoft Windows 操作系统和 Git 的某些版本中，您可能会看到类似于以下示例中的错误。
        **Windows 克隆密码错误示例**

        ```
         $ git clone https://emmap1@bitbucket.org/emmap1/bitbucketstationlocations.git Cloning into 'bitbucketspacestation'... fatal: could not read Password for 'https://emmap1@bitbucket.org': No such file or directory 

        ```

    *   如果出现此错误，请在命令行输入以下内容:

        ```
        $ git config --global core.askpass

        ```

    *   然后返回步骤 4，重复克隆过程。bash 代理现在应该提示您输入密码。您应该只需要这样做一次。

    此时，您的终端窗口应该如下所示:

    ```
     $ cd ~/repos
     $ git clone https://emmap1@bitbucket.org/emmap1/bitbucketstationlocations.git Cloning into 'bitbucketstationlocations'... Password warning: You appear to have cloned an empty repository. 

    ```

    你已经知道你的仓库是空的，对吗？请记住，您还没有向其中添加任何源文件。

9.  列出你的 repos 目录的内容，你应该会在里面看到你的`bitbucketstationlocations`目录。

    ```
    $ ls

    ```

恭喜你！您已经将您的存储库克隆到了您的本地系统。

### 第二步。将文件添加到您的本地存储库中，并将其放在 Bitbucket 上

有了本地系统上的存储库，就该开始工作了。你想开始记录你所有的空间站位置。为此，让我们创建一个关于您所有位置的文件。

1.  转到终端窗口，导航到本地存储库的顶层。

    ```
    $ cd ~/repos/bitbucketstationlocations/
    ```

2.  Enter the following line into your terminal window to create a new file with content.

    ```
    $ echo "Earth's Moon" >> locations.txt

    ```

    如果命令行没有返回任何东西，这意味着您正确地创建了文件！

3.  Get the status of your local repository. The [git status](https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-status) command tells you about how your project is progressing in comparison to your Bitbucket repository.

    此时，Git 意识到您创建了一个新文件，您将看到如下内容:

    ```
    $ git status On branch master Initial commit Untracked files: (use "git add ..." to include in what will be committed) locations.txt nothing added to commit but untracked files present (use "git add" to track) 

    ```

    该文件是未跟踪的，这意味着 Git 看到的文件不是以前提交的一部分。状态输出还显示了下一步:添加文件。

4.  Tell Git to track your new `locations.txt` file using the [git add](https://www.atlassian.com/git/tutorials/saving-changes/git-add) command. Just like when you created a file, the git add command doesn’t return anything when you enter it correctly.

    ```
    $ git add locations.txt

    ```

    `git add`命令将变更从工作目录移动到 Git 暂存区。在将一组更改提交到正式历史记录之前，您可以在临时区域准备这些更改的快照。

    ![Working directory to Staging area](img/a551b8329a89f975f7038cce60a2d2d8.png)

5.  Check the status of the file.

    ```
     $ git status On branch master Initial commit Changes to be committed: (use "git rm --cached <file>..." to unstage) new file: locations.txt 

    ```

    现在，您可以看到新文件已被添加(暂存),您可以在准备就绪时提交它。`git status`命令显示工作目录和暂存快照的状态。

6.  Issue the [git commit](https://www.atlassian.com/git/tutorials/saving-changes/git-commit) command with a commit message, as shown on the next line. The -m indicates that a commit message follows.

    ```
     $ git commit -m 'Initial commit' [master (root-commit) fedc3d3] Initial commit 1 file changed, 1 insertion(+) create mode 100644 locations.txt 

    ```

    `git commit`获取分段快照，并将其提交到项目历史中。结合`git add`，这个过程为所有 Git 用户定义了基本的工作流。

    ![Staging area to Commit history](img/54dd7cf851680432f8f4e2addc4aaa6d.png)

    到目前为止，您所做的一切都在您的本地系统上，并且对您的 Bitbucket 存储库不可见，直到您推送这些更改。

    了解更多关于 Git 和远程存储库的知识

    *   Git 与远程存储库(在您的例子中，Bitbucket 是远程存储库)通信的能力是每个基于 Git 的协作工作流的基础。
    *   Git’s collaboration model gives every developer their own copy of the repository, complete with its own local history and branch structure. Users typically need to share a series of commits rather than a single changeset. Instead of committing a changeset from a working copy to the central repository, Git lets you share entire branches between repositories.

        ![Git's Collaboration](img/258da285a97c64f57d4e5067d9d9b101.png)

    *   您管理与其他存储库的连接，并通过将分支“推”到其他存储库来发布本地历史。通过将分支“拉”到您的本地存储库中，您可以看到其他人的贡献。
7.  Go back to your local terminal window and send your committed changes to Bitbucket using [git push origin master](https://www.atlassian.com/git/tutorials/syncing/git-push?utm_source=sitepoint&utm_medium=paid-content&utm_content=learn-git-with-bitbucket&utm_campaign=bitbucket_adexp-bbtofu_sitepoint-syn-content). This command specifies that you are pushing to the master branch (the branch on Bitbucket) on origin (the Bitbucket server).

    您应该会看到类似以下的响应:

    ```
    $ git push origin master Counting objects: 3, done. Writing objects: 100% (3/3), 253 bytes | 0 bytes/s, done. Total 3 (delta 0), reused 0 (delta 0) To https://emmap1@bitbucket.org/emmap1/bitbucketstationlocations.git * [new branch] master -> master Branch master set up to track remote branch master from origin. 

    ```

    您的提交现在位于远程存储库(origin)上。

    ![Local system to Bitbucket server](img/903f3fc286ec274b02ad14f7d766e73e.png)

8.  转到您在 Bitbucket 上的**BitbucketStationLocations**存储库，并单击菜单栏上的**提交**项。
9.  You should see a single commit on your repository. Bitbucket combines all the things you just did into that commit and shows it to you. You can see that the Author column shows the value you used when you configured the Git global file ( ~/.gitconfig).

    ![Author](img/f5cb1a255218ca6d170ba9b807ac6d7d.png)

10.  Click the Source option.

    您应该看到您的存储库中只有一个源文件，即您刚才添加的 locations.txt 文件。

    ![Source](img/96d7507ca6e9997cec14faf307d15b22.png)

还记得第一次创建存储库时的样子吗？现在看起来可能有点不同。

## 从 Bitbucket Cloud 上的 Git 存储库中提取变更

空间站管理员活动列表的下一项，您需要一个关于您的位置的更多详细信息的文件。因为您目前没有很多位置，所以您将直接从 Bitbucket 添加它们。

### 第一步。在 Bitbucket 中创建文件

要添加新的位置文件，请执行以下操作:

1.  From your **BitbucketStationLocations** repository, click **Source** to open the source directory. Notice you only have one file, `locations.txt`, in your directory.

    ![Source](img/047c7b57b2917bbe946ef8c88aa95df5.png)

2.  From the *Source* page, click *New file* in the top right corner. This button only appears after you have added at least one file to the repository.

    将打开一个用于创建新文件的页面，如下图所示。

    ![New File](img/ba51240ed634f51bb2379085bb68bee6.png)

3.  在**文件名**字段中输入`stationlocations`。
4.  从**语法模式**列表中选择 **HTML** 。
5.  将以下 HTML 代码添加到文本框中:

    ```
     <p>Bitbucket has the following space stations:</p> <p> <b>Earth's Moon</b><br> Headquarters </p> 

    ```

6.  点击**提交**。**提交消息**域显示消息:

    ```
    stationlocations created online with Bitbucket.

    ```

7.  点击消息字段下的**提交**。

您现在在 Bitbucket 中有了一个新文件！您将进入一个包含提交详细信息的页面，在这里您可以看到刚才所做的更改:

![Bitbucket commit message](img/80f8d60ff7128bc8779a2452338a1533.png)

如果您想查看到目前为止已经提交的列表，请单击左侧的**提交**链接。

### 第二步。从远程存储库中提取更改

现在我们需要把这个新文件放到您的本地存储库中。这个过程非常简单，基本上与将`locations.txt`文件放入 Bitbucket 的过程相反。

要将文件提取到本地存储库中，请执行以下操作:

1.  打开终端窗口，导航到本地存储库的顶层。

    ```
    $ cd ~/repos/bitbucketstationlocations/

    ```

2.  Enter the `git pull --all` command to pull all the changes from Bitbucket. (In more complex branching workflows, pulling and merging all changes might not be appropriate .) Enter your Bitbucket password when asked for it. Your terminal should look similar to the following:

    ```
    $ git pull --all Fetching origin remote: Counting objects: 3, done. remote: Compressing objects: 100% (3/3), done. remote: Total 3 (delta 0), reused 0 (delta 0) Unpacking objects: 100% (3/3), done. From https://bitbucket.org/emmap1/bitbucketstationlocations fe5a280..fcbeeb0 master -> origin/master Updating fe5a280..fcbeeb0 Fast-forward stationlocations | 5 ++++++++++++++ 1 file changed, 5 insertions(+) create mode 100644 stationlocations 

    ```

    [git pull](https://www.atlassian.com/git/tutorials/syncing/git-pull?utm_source=sitepoint&utm_medium=paid-content&utm_content=learn-git-with-bitbucket&utm_campaign=bitbucket_adexp-bbtofu_sitepoint-syn-content) 命令用一个命令将文件从远程存储库(Bitbucket)合并到本地存储库中。

    ![Bitbucket server to local system](img/b6579ecf0364349c85940e01f88f45d8.png)

3.  导航到本地系统上的存储库文件夹，您将看到刚刚添加的文件。

太棒了。添加了关于您的空间站位置的两个文件之后，您已经在 Bitbucket 和您的本地系统之间执行了基本的 Git 工作流(克隆、添加、提交、推送和拉取)。

## 使用 Git 分支合并文件

作为一名空间站管理员伴随着一定的责任。有时你需要锁定信息，尤其是在绘制太阳系的新位置时。学习分支将允许你更新你的文件，并只在你准备好的时候分享信息。

当你在团队中工作时，分支是最强大的。您可以在自己的分支中处理项目中您自己的部分，从 Bitbucket 中提取更新，然后在主分支准备就绪时将所有工作合并到主分支中。[我们的文档](https://confluence.atlassian.com/bitbucket/branch-or-fork-your-repository-221450630.html)包含了更多关于你为什么想要使用分支的解释。

一个分支代表了你的存储库的一个独立的开发路线。可以把它想象成一个全新的工作目录、临时区域和项目历史。在创建任何新分支之前，您会自动从主分支(称为 master)开始。对于一个可视化的例子，这个图显示了主分支和另一个带有 bug 修复更新的分支。

![Git Branch](img/02bbbb815d9649865beb57ecb819c7a6.png)

### 第一步。创建一个分支并进行更改

创建一个分支，您可以在其中添加尚未准备好提交的空间站未来计划。当您准备好让所有人都知道这些计划时，您可以将更改合并到您的 Bitbucket 存储库中，然后删除不再需要的分支。

理解分支只是提交的指针是很重要的。当你创建一个分支时，Git 需要做的只是创建一个新的指针——它不会创建一组全新的文件或文件夹。在开始之前，您的存储库如下所示:

![Master](img/a4e91937941df862a8f6543aa5b3ee51.png)

要创建分支，请执行以下操作:

1.  转到终端窗口，使用以下命令导航到本地存储库的顶层:

    ```
    $ cd ~/repos/bitbucketstationlocations/

    ```

2.  Create a branch from your terminal window.

    ```
    $ git branch future-plans

    ```

    该命令创建一个分支，但不会将您切换到该分支，因此您的存储库如下所示:

    ![Master and Future-plans](img/480a17c0b7b263835b7c90078ef38679.png)

    存储库历史保持不变。你得到的只是一个指向当前分支的新指针。要开始处理新的分支，您必须签出您想要使用的分支。

3.  Checkout the new branch you just created to start using it.

    ```
    $ git checkout future-plans Switched to branch 'future-plans'

    ```

    `git checkout`命令与`git branch`协同工作。因为你正在创建一个分支来处理一些新的东西，所以每次你创建一个新的分支(用 git branch)，如果你要使用它，你要确保检查它(用`git checkout`)。现在您已经签出了新的分支，您的 Git 工作流如下所示:

    ![Master and Future-plans](img/a74d15df5308f233bb5890dd5b13233e.png)

4.  在本地系统上搜索并打开`bitbucketstationlocations`文件夹。您会注意到，由于新的分支，目录中没有额外的文件或文件夹。
5.  使用文本编辑器打开`stationlocations`文件。
6.  通过添加另一个站点位置对文件进行更改:

    ```
     <p>Bitbucket has the following space stations:</p> <p> <b>Earth's Moon</b><br> Headquarters </p> <p> <b>Mars</b><br> Recreation Department </p> 

    ```

7.  保存并关闭文件。
8.  Enter `git status` in the terminal window. You will see something like this:

    ```
    $ git status On branch future-plans Changes not staged for commit: (use "git add <file>..." to update what will be committed) (use "git checkout -- <file>..." to discard changes in working directory) modified: stationlocations no changes added to commit (use "git add" and/or "git commit -a") 

    ```

    注意到 On 分支`future-plans`线了吗？如果你之前输入了`git status`，那么这条线就在`branch master`上，因为你只有一个`master branch`。在您准备或提交一个变更之前，总是检查这一行，以确保您想要添加变更的分支被检出。

9.  准备好你的文件。

    ```
    $ git add stationlocations

    ```

10.  Enter the `git commit` command in the terminal window, as shown with the following:

    ```
    $ git commit stationlocations -m 'making a change in a branch' [future-plans e3b7732] making a change in a branch 1 file changed, 4 insertions(+) 

    ```

    通过最近的提交，您的存储库看起来像这样:

    ![Feature branch commit](img/fc2dc1b6211c221e82423e74a3c5ecec.png)

    现在是时候将您刚刚做出的更改合并回`master`分支了。

### 第二步。合并您的分支:快进合并

你的空间站正在成长，是时候举行你的火星位置的开幕式了。现在您的未来计划正在变成现实，您可以将您的`future-plans`分支合并到您本地系统的主分支中。

因为您只创建了一个分支并做了一个更改，所以请使用快进分支方法进行合并。您可以进行快进合并，因为您有一个从当前分支顶端到目标分支的线性路径。不是“实际上”合并分支，Git 集成历史所要做的只是将当前分支顶端向上移动(即“快进”)到目标分支顶端。这有效地组合了历史，因为从目标分支可到达的所有提交现在都可以通过当前分支获得。

![Fast-forward](img/dbcf1d850980213fae5220aab3ca487d.png)

这种分支工作流对于变化较小的短期主题分支很常见，但对于长期运行的功能来说并不常见。

要完成快进合并，请执行以下操作:

1.  转到终端窗口，导航到本地存储库的顶层。

    ```
    $ cd ~/repos/bitbucketstationlocations/

    ```

2.  输入`git status`命令以确保您提交了所有的更改，并找出您签出了哪个分支。

    ```
    $ git status On branch future-plans nothing to commit, working directory clean

    ```

3.  切换到主分支。

    ```
    $ git checkout master Switched to branch 'master' Your branch is up-to-date with 'origin/master'.

    ```

4.  Merge changes from the future-plans branch into the master branch. It will look something like this:

    ```
    $ git merge future-plans Updating fcbeeb0..e3b7732 Fast-forward stationlocations | 4 ++++ 1 file changed, 4 insertions(+)

    ```

    实际上，您已经将主分支的指针向前移动到了当前的头部，您的存储库看起来有点像上面的快进合并。

5.  Because you don’t plan on using future-plans anymore, you can delete the branch.

    ```
    $ git branch -d future-plans Deleted branch future-plans (was e3b7732).

    ```

    当您删除`future-plans`时，您仍然可以使用提交 id 从`master`访问分支。例如，如果您想撤销从`future-plans`添加的更改，使用您刚刚收到的提交 id 返回到那个分支。

6.  输入`git status`来查看您的合并结果，它显示您的本地存储库比您的远程存储库领先一个。它看起来会像这样:

    ```
    $ git status On branch master Your branch is ahead of 'origin/master' by 1 commit. (use "git push" to publish your local commits) nothing to commit, working directory clean

    ```

以下是您到目前为止所做的工作:

*   创建了一个分支并将其签出
*   在新的分支机构做了改变
*   将变更提交到新的分支
*   将变更整合回主分支
*   删除不再使用的分支。

接下来，我们需要将所有这些工作推回到您的远程存储库 Bitbucket。

### 第三步。将您的更改推送到 Bitbucket

你想让其他人也能看到新空间站的位置。为此，您可以将本地存储库的当前状态推送到 Bitbucket。

此图显示了当您的本地存储库有中央存储库没有的更改，并且您将这些更改推送到 Bitbucket 时会发生什么。

![Pushing changes to Bitbucket diagram](img/c63b80c422debedbf2b981d7e32cbfec.png)

下面是将您的更改推送到远程存储库的方法:

1.  在终端窗口的存储库目录中，输入 git push origin master 来推送更改。结果会是这样:

    ```
    $ git push origin master Counting objects: 3, done. Delta compression using up to 8 threads. Compressing objects: 100% (3/3), done. Writing objects: 100% (3/3), 401 bytes | 0 bytes/s, done. Total 3 (delta 0), reused 0 (delta 0) To https://emmap1@bitbucket.org/emmap1/bitbucketstationlocations.git fcbeeb0..e3b7732 master -> master

    ```

2.  点击你的 Bitbucket 存储库的**概述**页面，注意你可以在**最近活动**流中看到你的推送。
3.  Click **Commits** and you can see the commit you made on your local system. Notice that the change keeps the same commit id as it had on your local system.

    ![Local system](img/f19028b42421c125119dea983ed0f38e.png)

    您还可以看到提交列表左边的行有一个直接的路径，没有显示分支。这是因为`future-plans`分支从不与远程存储库交互，只与我们创建和提交的变更交互。

4.  点击**分支**，注意页面也没有分支的记录。
5.  点击**源**，然后点击`stationlocations`文件。您可以看到对文件的最后一次更改具有您刚刚推送的提交 id。
6.  Click the file history list to see the changes committed for this file, which will look similar to the following figure.

    ![File history](img/0ed4cfe9a0eb7ee64522aeee440f1b8e.png)

### 你完了！

不确定您是否能够记住刚刚学习的所有 Git 命令？没问题。为我们的[基本 Git 命令](https://www.atlassian.com/git/tutorials/svn-to-git-prepping-your-team-migration/basic-git-commands?utm_source=sitepoint&utm_medium=paid-content&utm_content=learn-git-with-bitbucket&utm_campaign=bitbucket_adexp-bbtofu_sitepoint-syn-content)页面添加书签，这样你就可以在需要的时候参考它。

## 分享这篇文章