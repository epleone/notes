---
tags:
  - git
  - tips
---

# Git Tips

  

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