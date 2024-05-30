# tmux guide


## 简介

[tmux](https://github.com/tmux/tmux) 是一款优秀的终端复用软件，它包含 `session` 、`window`	、`panel` 三个概念。

![](https://image-static.segmentfault.com/113/222/1132220757-59dd99bf3edf3_articlex)


在linux上安装命令如下：

``` shell
sudo apt-get install tmux
```


## 常用命令

### session

|            功能             |            命令             |
| :-------------------------: | :-------------------------: |
|       进入最近的`session`        |     `tmux a`      |
|       创建 `session`        |     `tmux new -s 名字`      |
|       关闭 `session`        | `tmux kill-session -t 名字` |
|         挂起 `session`         |       `prefix` + `d`       |
|       切换 `session`        |       `prefix` + `s`       |
|       重命名 `session`        |       `prefix` + `$`       |
| 关闭 `Tmux` 杀死所有session |     `tmux kill-server`      |
|     列出已有`Tmux`列表      |          `tmux ls`          |


### windows

|            功能             |            命令             |
| :-------------------------: | :-------------------------: |
|        新建`windows`        |       `prefix` + `c`       |
|        关闭`windows`        |       `prefix` + `&`       |
|        切换`windows`        |      `prefix` + `0~9`      |
|        重命名`windows`        |      `prefix` + `,`      |


### panel

|            功能             |            命令             |
| :-------------------------: | :-------------------------: |
|        左右分`panel`        |      `prefix` + `%`      |
|        上下分`panel`        |      `prefix` + `"`      |
|        关闭 `panel`        |      `prefix` + `x`      |
|        选择 `panel`        |      `prefix` + `o` /   `prefix` + `q`  |
|        调整大小 `panel`        |      `prefix` + `Ctrl+方向键`   |
|        显示时钟	       	 |      `prefix` + `t`   	|



## 翻页翻屏

`prefix` + `[`  进入翻页模式， `PgUp`  `PgDn`  实现上下翻页,  `q` 退出



## 修改指令前缀

```shell
sudo vim ~/.tmux.conf

# 从tmux v1.6版起，支持设置第二个指令前缀
# 将`键作为第二个指令前缀
set-option -g prefix2 ` 
```



## 注意事项

> [!warning] Tmux 和 Conda 环境冲突可能导致代码无法运行

`Tmux `是一个终端复用器，它允许你在一个终端窗口中创建多个会话，并在这些会话之间轻松切换。它本身并不会改变环境，但它可能会影响环境的一些行为，例如会话的路径或环境变量设置。如果你在 `Tmux` 中发现你的代码无法正常运行，一些常见的原因可能是：

- **路径问题**：`Tmux` 会话可能具有不同的当前工作路径。你可以通过在 `Tmux` 中运行 `pwd` 命令来检查当前路径，并确保你的代码的路径设置正确。

- **环境变量**：`Tmux `可能会导致你的`shell`启动时加载不同的环境变量。你可以使用 `printenv` 或 `echo $VARIABLE_NAME` 来检查环境变量是否设置正确。

- **终端设置**：`Tmux` 使用虚拟终端来运行程序，有时候这可能会影响某些程序的行为，特别是涉及到终端控制的程序。

- **权限问题**：有时候 `Tmux` 的会话可能会以不同的用户身份运行，这可能会导致权限问题。


> [!warning] Conda 和 pyenv 同时安装，导致 conda环境被 pyenv覆盖


``` bash

# 在.bashrc中注释掉 pyenv的环境设置，解决环境冲突
if false; then
   # pyenv ...
fi
```