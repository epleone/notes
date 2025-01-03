---
tags:
  - git
---

# Git 简明教程

推荐阅读 [git - 简易指南](https://www.bootcss.com/p/git-guide/)

[Git最佳实践，这样用就对了](https://zhuanlan.zhihu.com/p/701612911)

[各位大佬你们团队开发 git 是如何管理的？ - V2EX](https://www.v2ex.com/t/1063393)

​ ​ 本地仓库由 git 维护的三棵“树”组成。

- 第一个是你的`工作目录`，它持有实际文件；
    
- 第二个是`暂存区（Index）`，它像个缓存区域，临时保存你的改动；
    
- 最后是 `HEAD`，它指向你最后一次提交的结果。
    

![](https://www.bootcss.com/p/git-guide/img/trees.png)

## 创建新仓库

​ 创建新文件夹，打开，然后执行

```shell
git init
```

## clone: 克隆仓库

​ 执行如下命令以创建一个本地仓库的克隆版本：

```shell
git clone /path/to/repository
```

​ 如果是远端服务器上的仓库，你的命令会是这个样子：

``` shell
git clone https://github.com/epleone/epleone.github.io.git
```

## add: 添加和提交

​ 你可以提出更改（把它们添加到暂存区），使用如下命令：

``` shell
git add <filename>
git add *
git add ./
```

​ 这是 git 基本工作流程的第一步。

​ 使用如下命令以实际提交改动：

``` shell
git commit -m "代码提交信息"
```

​ 现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库。

## push: 推送改动

​ 你的改动现在已经在本地仓库的 HEAD 中了。执行如下命令以将这些改动提交到远端仓库：

``` shell
git push origin master
```

​ 可以把 master 换成你想要推送的任何分支。

​ 如果你还没有克隆现有仓库，并欲将你的仓库连接到某个远程服务器，你可以使用如下命令添加：

``` shell
git remote add origin <server>
```

​ 如此你就能够将你的改动推送到所添加的服务器上去了。

## checkout + branch: 分支管理

[git中的分支管理：git branch,git checkout，解决git中的分支冲突的方法](https://blog.csdn.net/m0_56132701/article/details/134478506)

[Git分支(branch)详细讲解\_git branch-CSDN博客](https://blog.csdn.net/Marsnowe/article/details/103940394)

​ 分支是用来将特性开发绝缘开来的。在你创建仓库的时候，master 是“默认的”分支。在其他分支上进行开发，完成后再将它们合并到主分支上。

- 查看分支：`git branch`
- 创建分支：`git branch <name>`
- 切换分支：`git checkout <name>` 或者 `git switch <name>`
- 创建并切换分支：`git checkout -b <name>` 或者 `git switch -c <name>`
- 合并某个分支：`git merge <name>`
- 删除分支：`git branch -d <name>`

`git checkout` 和 `git switch` 是 [Git](https://so.csdn.net/so/search?q=Git&spm=1001.2101.3001.7020) 中用于切换分支的命令, `git switch` 是在 Git 2.23 版本引入的，它提供了一种更直观的分支切换方式。

[git checkout和git switch的区别\_git switch和git checkout-CSDN博客](https://blog.csdn.net/lycwhu/article/details/135771596)

**使用checkout 来创建和切换分支**。

### 创建分支

​ 创建一个叫做“feature_x”的分支，并切换过去：

``` shell
git checkout -b feature_x
```

### 切换分支

​ 切换回主分支：

```shell
git checkout master
```

### 查看分支

``` shell
git branch
```

### 删除分支

​ 再把新建的分支删掉：

```shell
git branch -d feature_x
```

​ 除非你将分支推送到远端仓库，不然该分支就是 不为他人所见的：

``` shell
git push origin <branch>
```

### 分离头指针（detached HEAD）

当你切换到一个历史提交时，Git 会进入“分离头指针”状态，此时你不是在一个分支上工作，而是在一个特定的提交上工作。如果你在这种状态下进行修改并提交，新的提交不会在任何分支上。因此，当你切换回其他分支时，你会发现刚刚的提交“消失了”，实际上它们只是没有附加到任何分支上。

你可以使用 `git reflog` 来找到最近的提交。`git reflog` 会记录所有 HEAD 的变动，包括在分离头指针状态下的提交。

``` bash
# 找到你想要的提交的哈希值
git reflog

# 使用哈希值创建一个新的分支
git checkout -b my-new-branch <commit-hash>

# 按 `q` 键退出
```


> [!tips]
> 如果切到历史提交要修改，最好先创建分支
>

## pull = fetch + merge: 更新与合并

​ 要更新你的本地仓库至最新改动，执行：

``` shell
git pull
```

以在你的工作目录中 获取（fetch） 并 合并（merge） 远端的改动。

要合并其他分支到你的当前分支（例如 master），执行：

``` shell
git merge <branch>
```

​在这两种情况下，git 都会尝试去自动合并改动。遗憾的是，这可能并非每次都成功，并可能出现冲突（conflicts）。 这时候就需要你修改这些文件来手动合并这些冲突（conflicts）。改完之后，你需要执行如下命令以将它们标记为合并成功：

``` shell
git add <filename>
```

​ 在合并改动之前，你可以使用如下命令预览差异：

```
git diff <source_branch> <target_branch>
```

> [!warning]

Git中从远程的分支获取最新的版本到本地有这样2个命令：

1. git fetch：相当于是从远程获取最新版本到本地，不会自动merge

``` shell  
git fetch origin master
git log -p master..origin/master
git merge origin/master
```

以上命令的含义：

首先从远程的origin的master主分支下载最新的版本到origin/master分支上,然后比较本地的master分支和origin/master分支的差别,最后进行合并,上述过程其实可以用以下更清晰的方式来进行：

``` shell
git fetch origin master:tmp
git diff tmp 
git merge tmp
``` 

从远程获取最新的版本到本地的test分支上,之后再进行比较合并。

2. git pull：相当于是从远程获取最新版本并merge到本地
``` shell
git pull origin master
```
 

上述命令其实相当于git fetch 和 git merge

在实际使用中，git fetch更安全一些

因为在merge前，我们可以查看更新情况，然后再决定是否合并。

[原文链接](http://www.tech126.com/git-fetch-pull/)

## tag: 标签

​ 为软件发布创建标签是推荐的。这个概念早已存在，在 SVN 中也有。你可以执行如下命令创建一个叫做 1.0.0 的标签：

``` shell
git tag 1.0.0 1b2e1d63ff
```

​ 1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。可以使用下列命令获取提交 ID：

``` shell
git log
```

​ 你也可以使用少一点的提交 ID 前几位，只要它的指向具有唯一性。

## log

​ 如果你想了解本地仓库的历史记录，最简单的命令就是使用:

```shell
git log
```

​ 你可以添加一些参数来修改他的输出，从而得到自己想要的结果。 只看某一个人的提交记录:

```shell
git log --author=bob
```

​ 一个压缩后的每一条提交记录只占一行的输出:

```shell
git log --pretty=oneline
```

​ 或者你想通过 ASCII 艺术的树形结构来展示所有的分支, 每个分支都标示了他的名字和标签:

```shell
git log --graph --oneline --decorate --all
```

​ 看看哪些文件改变了:

```shell
git log --name-status
```

​ 这些只是你可以使用的参数中很小的一部分。更多的信息，参考：

```shell
git log --help
```

## 暂存改动

我们有时会遇到这样的情况，正在`dev`分支开发新功能，做到一半时有人过来反馈一个bug，让马上解决，但是新功能做到了一半你又不想提交，这时就可以使用`git stash`命令先把当前进度（工作区和暂存区）保存起来，然后切换到另一个分支去修改bug，修改完提交后，再切回`dev`分支，使用`git stash pop`来恢复之前的进度继续开发新功能。

[git stash 的用法-CSDN博客](https://blog.csdn.net/csdnlijingran/article/details/96425712)

## 替换本地改动

​ 假如你操作失误（当然，这最好永远不要发生），你可以使用如下命令替换掉本地改动：

```shell
git checkout -- <filename>
```

此命令会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到暂存区的改动以及新文件都不会受到影响。

假如你想丢弃你在本地的所有改动与提交，可以到服务器上获取最新的版本历史，并将你本地主分支指向它：

```shell
git fetch origin
git reset --hard origin/master
```

## 多账号管理

```shell
# 查看全局配置信息
git config --global --list

# 查看当前账号信息
git config --list

# 查看指定键值
git config --global <key>
git config <key>

# 指定key为 user.name
git config --global user.name "用户名"
git config --global user.email "邮箱"

# 检查本地仓库的远程设置
git remote -v

```

在正确切换账号后，push代码提示 `fatal: unable to access XXX, The requested URL returned error:403`

打开`控制面板`->`凭据管理器`->`Windows凭据`

在`普通凭据`中找到github的凭据后, 删除它, 然后重新到仓库执行`git push origin master`去提交你的代码, 它会让你填写GitHub的用户名和密码, 填写正确后, 即可进行正常推送

参考自： [https://blog.csdn.net/qq_36852780/article/details/107374275](https://blog.csdn.net/qq_36852780/article/details/107374275)

建议直接使用`ssh`方式连接。 https://blog.csdn.net/CoderSharry/article/details/131249905

## 排除目录

在Git中排除文件有多种方法。以下是几种常用的方法：

1. 使用.gitignore文件：在项目的根目录下创建一个名为`.gitignore`的文件，并将要排除的文件模式添加到该文件中。每行一个模式。可以使用通配符来匹配文件名、目录名或模式。例如，要排除所有以`.log`结尾的文件，可以在`.gitignore`文件中添加以下内容：

```bash
*.log

# 排除文件夹
__pycache__/
.vscode/
logs/

#排除文件
*.pkl
```

保存并提交`.gitignore`文件后，Git会自动忽略匹配这些模式的文件。

2. 使用命令行参数：在执行Git命令时，可以使用`git add`、`git commit`等命令的参数来排除特定文件或文件模式。例如，要在提交时排除所有以`.log`结尾的文件，可以使用以下命令：

```bash
git commit -a --exclude=*.log
```

这将将所有更改添加到提交中，但排除匹配模式的文件。

3. 使用.git/info/exclude文件：如果您不希望将排除规则添加到版本控制中，可以在`.git/info/exclude`文件中添加它们。此文件的工作方式与`.gitignore`文件相同，但是它只适用于您的本地存储库。

## rebase和merge

[基础扫盲篇：git中rebase和merge的区分 - 知乎](https://zhuanlan.zhihu.com/p/558666061)

[git rebase详解（图解+最简单示例，一次就懂）-CSDN博客](https://blog.csdn.net/weixin_42310154/article/details/119004977)

[在开发过程中使用 git rebase 还是 git merge，优缺点分别是什么？ - 知乎](https://www.zhihu.com/question/36509119)

[详解git rebase，让你走上git大神之路 - 知乎](https://zhuanlan.zhihu.com/p/271677627)

[git rebase为什么不建议在公共分支进行？ - 知乎](https://www.zhihu.com/question/611466013)

这篇blog的说明比较清晰

[git rebase - 知乎](https://zhuanlan.zhihu.com/p/667928131)

## MR/PR流程

### **MR的一般流程(gitlab)**

本地创建分支推送改动到服务器, 打开源代码网址，提交MR, (合并后可选删除分支)。合并到主分支后，你本地切换到master分支（需要处理好冲突，不能有未改动的代码），然后拉取master最新版。 在MR流程中，reviewer可能对dev分支修改的内容提出建议，或者也可以直接增加修改。

#### 从仓库下载

```bash
git clone https://git.xxx.com.cn/xxx
```

#### 本地修改并提交

```bash
# 创建并切换到新的分支`dev`，用于递交合并
git checkout -b dev

# 修改相关内容
# vim xxx.py

# 添加修改到缓冲区
git add ./
git commit -m "xxx"

# 将分支push到服务器
git push -u origin HEAD
```

#### gitlab创建并提交 MR(PR)

打开源代码网址，提交MR.  页面一般会弹出创建MR的提示，如果没有，也可以手动创建MR.

注意：`Assignee` 负责merge, `Reviewer`负责看代码，但没有Merge权限。

#### 本地收尾操作

等到改动被主干版本吸收（中间可能会经过反复修改），本地同步`master`最新版，并删除`dev`分支。

```bash
# 本地切换到master版本并同步远程修改
git switch master
git pull

# 删除本地dev, 同步后应该自动删除了？
git branch -d dev
```

> [!tips]
> 本地切换main分支后同步，本地的dev分支一定要删除吗？
> 1. 如果不删除，直接在dev分支push会报错，因为服务器端的dev分支已被删除，且不会重新创建dev分支了。
> 2. 如果不删除，切到dev分支 执行rebase
>

[git rebase详解（图解+最简单示例，一次就懂）-CSDN博客](https://blog.csdn.net/weixin_42310154/article/details/119004977)

### **PR的一般流程(github)**

如果你想深入了解 PR 开发模式，可以参考 [GitHub 官方文档](https://docs.github.com/zh/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)。

#### Fork 项目

当你第一次提交 PR 时，需要先 Fork 项目到自己的 GitHub 账号下。点击项目右上角的 Fork 按钮，将项目 Fork 到自己的 GitHub 账号下。

![](https://user-images.githubusercontent.com/13503330/223318144-a49c6cef-b1fb-45b8-aa2b-0833d0e3fd5c.png)

接着，你需要将你的 Fork 仓库 Clone 到本地，然后添加官方仓库作为远程仓库：

``` bash
# Clone 你的 Fork 仓库到本地
git clone https://github.com/username/mmpose.git

# 添加官方仓库作为远程仓库
cd mmpose
git remote add upstream https://github.com/open-mmlab/mmpose.git
```

#### Clone 你的 Fork 仓库到本地

``` bash
git clone https://github.com/username/mmpose.git
```

#### 添加官方仓库作为远程仓库

``` bash
cd mmpose
git remote add upstream https://github.com/open-mmlab/mmpose.git
``` 

在终端中输入以下命令，查看远程仓库是否成功添加：

``` bash
git remote -v
```

如果出现以下信息，说明你已经成功添加了远程仓库：

``` Text
origin	https://github.com/{username}/mmpose.git (fetch)
origin	https://github.com/{username}/mmpose.git (push)
upstream	https://github.com/open-mmlab/mmpose.git (fetch)
upstream	https://github.com/open-mmlab/mmpose.git (push)
```

#### 备注

这里对 origin 和 upstream 进行一个简单的介绍，当我们使用 git clone 来克隆代码时，会默认创建一个 origin 的 remote，它指向我们克隆的代码库地址，而 upstream 则是我们自己添加的，用来指向原始代码库地址。当然如果你不喜欢他叫 upstream，也可以自己修改，比如叫 open-mmlab。我们通常向 origin 提交代码（即 fork 下来的远程仓库），然后向 upstream 提交一个 pull request。如果提交的代码和最新的代码发生冲突，再从 upstream 拉取最新的代码，和本地分支解决冲突，再提交到 origin。

## LFS

  如何下载github上用git-lfs上传的大文件

1. 安装 `git lfs`, 见下述述步骤
   
```bash

# 下载
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash

# 安装
sudo apt-get install git-lfs

# 配置
git lfs install


```

2. 克隆存储库，下载到本地 `git clone https://github.com/user/repo.git`

3. 下载大文件：进入存储库目录，并使用以下命令下载大文件 `git lfs pull`


> [!quote]
>   1. [https://github.com/git-lfs/git-lfs/blob/main/INSTALLING.md](https://github.com/git-lfs/git-lfs/blob/main/INSTALLING.md)
>   2. [如何下载github上用git-lfs工具下载的大文件-CSDN博客](https://blog.csdn.net/weixin_42601608/article/details/129518919)

## VS Code

在VS code中，建议使用`Git Graph`插件增强 git, 非常方便。

使用`Git Graph`插件，同步改动，可以选择`拉取` 或者 直接选择`同步`

`pull(拉取)`将服务器的最新改动同步到本地， 服务器 --> 本地

`Sync(同步)`将服务器的最新改动同步到本地，同时也将本地的改动同步到服务器， 服务器 <--> 本地

[GitGraph 插件使用 - 知乎](https://zhuanlan.zhihu.com/p/609737717)

## Git GUI工具

鹅厂自己的 Git GUI 功能全面，新手友好，而且一些特定场景下有优化

[UGit - 让每个人都可以轻松使用Git](https://ugit.qq.com/zh/)

## Git 练习游戏

可以通过游戏练习git

[Learn Git Branching](https://learngitbranching.js.org/?locale=zh_CN)

Git 命令操作可视化

[🌳🚀 CS Visualized: Useful Git Commands - DEV Community](https://dev.to/lydiahallie/cs-visualized-useful-git-commands-37p1#rebase)

## 忽略`.gitignore`

可以直接将 `.gitignore` 文件本身添加到 `.gitignore` 文件中，这样 Git 会忽略它。

不过，这种方法通常不会起作用，因为 Git 会在读取 `.gitignore` 文件时忽略它自己。

可以尝试以下步骤：

``` bash
# 在 `.gitignore` 文件中添加 `.gitignore`

# 移除对 `.gitignore` 文件的跟踪
git rm --cached .gitignore

# 提交更改
git commit -m "Stop tracking .gitignore"

```

## 配置文件 .gitattributes

Git版本管控工具功能强大，在使用过程中，在多人合作的项目开发过程中，经常会遇到提交代码时出现的warning提醒，尤其是换行符。

Linux/Unix/Mac OS操作系统的换行符使用`LF` (`\n`)，而Windows使用`CR (\r) LF(\n)`作为换行符。

`CR`：Carriage Return，回车

`LF`：Line Feed，换行

> [!quote]
> 1. [Git - gitattributes Documentation](https://git-scm.com/docs/gitattributes)
> 2. [.gitattributes 作用详细讲解（git大佬必会技能)-CSDN博客](https://blog.csdn.net/qq_35425070/article/details/106883833)
> 3. [gitattributes配置文件的作用\_.gitattributes-CSDN博客](https://blog.csdn.net/yanlaifan/article/details/135183823)
> 4. [git - Prevent VSCode to CRLF files - Stack Overflow](https://stackoverflow.com/questions/71081340/prevent-vscode-to-crlf-files)
> 5. [VSCode, WSL, Git & line endings (CRLF/LF), I can't get it working... : r/bashonubuntuonwindows](https://www.reddit.com/r/bashonubuntuonwindows/comments/ee2s68/vscode_wsl_git_line_endings_crlflf_i_cant_get_it/)



>[!warning] must set Git in windows to use linux file endings
>  在Windows下使用git, 需要设置linux换行符
>

``` 
# 见 `quote 5`

- You are using Git for Windows.
You must set Git in windows to use linux file endings, OR stop using Git for Windows. Use Git contained in your WSL distribution.


- How do I set Git for Windows to use linux file endings?
You mean with git config --global core.eol lf ? Also I set lf in VSCode, manually...
Or you mean another option?
Maybe the best is really to uninstall Git for Windows, but still I would like to know if you meant something else...


- Set core.autocrlf to input -- this will convert windows newlines to unix when committing, but when checking out it will check out the original unix newlines.
Now, delete all the files in your project (except the .git directory) and check out the latest commit again. This time, when it creates all the files, it will create them with unix newlines and you should be able to see "LF" in the VS code status bar.
Core.eol has no effect when core.autocrlf is set so there's no need to adjust that.

- woohoo i got it working!!
i used @ git for windows autocrlf input, @ wsl autocrlf false.
i deleted the files, made a git checkout -- . from WSL (when i made the checkout from git for windows i had the problem again) and now finally it is working!
thank you all!
**edit: yes, absolutly making the checkout on WSL makes the difference. What is funny because VSCode shows me on both files, also when i checkout on git for windows, that they use LF. sombody maybe knows why it behaves like this?**

```

[it can handle this by auto-converting CRLF line endings into LF when you add a file to the index](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration).

当你`checkin`（提交,  `add a file to the index`）时 ，可以通过设置可以通过设置 `core.autocrlf` 变量为`true`, `false`, `input`，来控制换行符的表现。

- **Windows 开发者**：建议设置 `core.autocrlf` 为 `true`，这样在提交时将 CRLF 转换为 LF，而在检出时将 LF 转换为 CRLF
- **Unix/Linux 开发者**：建议设置 `core.autocrlf` 为 `input`，这样在提交时将 CRLF 转换为 LF，而在检出时不做任何转换
- **跨平台开发团队**：通常设置全局为 `input`，并让 Windows 开发者在各自的仓库中设置为 `true`
- **保持文件原样**：设置 `core.autocrlf` 为 `false`，在提交和检出时都不做转换。

当`checkout`时， 可以通过设置`core.eol`来控制检出的文件的换行符。

`core.eol` 配置项有以下几个可能的值：

- `lf`：将所有行结束符转换为 `LF`。
- `crlf`：将所有行结束符转换为 `CRLF`。
- `native`：根据操作系统的默认行结束符来设置（在 Unix 系统上使用 `LF`，在 Windows 系统上使用 `CRLF`）。


``` bash

# 查看
git config core.autocrlf

# 设置
git config --global core.autocrlf true

# windows下必须设置
git config --global core.autocrlf input

# 取消
git config --global --unset core.autocrlf
```

还有一种做法就是编辑 `.gitattributes` 文件

```
*.txt text eol=lf
```

这条设置的含义是对所有扩展名为 `.txt` 的文本文件，将行尾符统一设置为 `LF`（换行符，Line Feed）。当你检出或提交 `.txt` 文件时，Git 将会自动将文件的行尾符转换为 `LF`。

``` text
* text=auto eol=lf
```

这条设置的含义是：

- `*` 表示对所有文件类型都生效。
- `text=auto` 表示告诉 Git 这些文件是文本文件，Git 应该处理它们的行尾符。
- `eol=lf` 表示将行尾符统一设置为 `LF`（换行符，Line Feed）。
