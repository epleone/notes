---
tags:
  - git
  - tips
---

# Git Tips

  
[如何学习Git?](https://www.zhihu.com/question/585086862)

![图片](wx-img_20241220.png)

## 撤销提交

如果你的提交已经推送到远程，通常推荐使用 `git revert` 来撤销提交，这样可以避免重写历史。对于本地操作，`git reset` 是更直接的方法。

### reset

``` bash

# ~1 表示回退1次。 ~2表示回退2次 。。。

# 将该提交的更改保留在暂存区。
git reset --soft HEAD~1

# 保留修改, 但不将其保留在暂存区, 手动add就和 --soft一致
git reset --mixed HEAD~1

# 不保留修改，完全删除这些提交
git reset --hard HEAD~1

```


如果回退多次提交且保留记录(`--soft` or `--mixed`)
比如 `--soft`,  在暂存区保留的版本是最新的版本。就`~3`来说，进行对比发现，是最新版本和回退三次前的版本对比。

### revert

`git revert` 用于撤销一个或多个提交，方法是通过创建一个新的提交来“反转”指定的提交的更改。与 `git reset` 不同，`git revert` 不会改变历史记录，而是会在当前分支上创建一个新的提交，这个新提交会撤销指定提交的更改。


``` bash
# 语法
git revert <commit>

# 撤销最新一次的提交
git revert HEAD


# 撤销制定提交：提交哈希是 `abc123`
git revert abc123


# 撤销多次提交
# 例如撤销从 `abc123` 到 `def456` 之间的所有提交
git revert abc123^..def456
```

## rebase和merge

**合并代码前使用 `rebase`**

``` bash
# 将dev分支改动移动到main后面
git checkout dev
git rebase main

# 在main分支合并 dev分支
git checkout main
git merge dev
```

**直接变基**

将 `dev` 分支上的更改 "移动" 到 `main` 分支的顶部。

``` shell
git checkout main
git rebase dev
```

**直接合并**

``` bash
git checkout main
git merge dev
```


> [!warning] 合并前是否使用`rebase`的区别
>1. 使用`rebase`, 会将`dev`分支移到`main`分支后，合并后不会产生分支图
>2. 解决冲突会在`rebase`阶段，合并就不需要
>3. 如果用冲突，建议退出 `rebase` , 使用`merge` (目前我使用`rebase`处理冲突，会丢失一些提交)


由于命令 `git merge --continue`的存在，使用`vs code` 处理 `rebase` 冲突时会丢失一些提交。



**如何退出合并状态**

``` bash
git rebase --abort
git merge --abort
```

> [!warning] 非必要不使用`rebase`
>  `rebase` 只适用于个人开发，想要保持提交历史清晰、简洁。

### `rebase` 使用场景

基于`rebase`的特性。提交`MR`后，可以使用它处理`dev`分支 和 `main` 分支的冲突。
- 服务端做好`MR`后，先把`main`从远程同步
- 然后切到`dev`分支 执行`rebase`

## 同时重命名本地/远程分支

如果你想将远程分支 `origin/dev_a` 改为 `origin/dev_b`，你可以按照以下步骤操作：

- **1. 重命名本地分支**

首先，确保本地分支名与目标一致，即将本地分支重命名为 `dev_b`（如果还没有重命名的话）：

``` bash
git branch -m dev_b
```

- 2. **删除远程的旧分支`origin/dev_a`**

你需要删除远程仓库中的旧分支 `origin/dev_a`：
``` bash
git push origin --delete dev_a
```

 -  **3. 推送本地分支到远程并设置跟踪关系**

将本地 `dev_b` 分支推送到远程，并设置跟踪远程分支：
``` bash
git push origin dev_b
```
然后，设置本地分支跟踪远程分支：
``` bash
git push --set-upstream origin dev_b
```
 
 - **4. 清理远程的引用**

如果删除远程分支后，远程分支的引用依然存在，你可以运行以下命令来清理：

``` bash
git fetch --prune
```
这样，远程分支名 `origin/dev_a` 就会被替换为 `origin/dev_b`，并且你的本地分支将与远程分支同步。