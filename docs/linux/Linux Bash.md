---
tags: [bash]
---
# Linux Bash


## preface

只要记住20个左右的命令，基本就能满足需要了。


> ls = listcd = change directory
> cp = copyrm = remove
> mv = move
> pwd = print work directory
> ps = process status
> df = disk free
> du = disk usage
> mkdir = make directory
> rmdir = remove directory
> su = switch user
> chown = change owner
> chmod = change mode


>-a = all
>-l = list
>-f = force
>-h = -human-readable
>-n = number
>-u = user
>-z = zip
>-r = recursive
> -v = verbose(详细)


## `man`  和 `help`

在Linux中，man和help是两个非常有用的命令，它们提供了关于其他命令的详细信息和使用方法。

### man 

man是“manual”的缩写，意为手册。通过man命令，用户可以查看Linux系统中大多数命令、函数和配置文件的详细说明。

使用方法： `man  命令名`  比如 `man ls`，这将打开一个分页器显示ls命令的详细说明。
使用方向键、Page Up/Down键或空格键进行导航。

按Q键退出man页面。

man页面的结构：

>NAME：            命令的名称和简短描述。
>SYNOPSIS：      命令的语法摘要。
>DESCRIPTION：命令的详细描述和功能。
>OPTIONS：       命令可用的选项和参数。
>EXAMPLES：     使用命令的示例（并非所有man页面都有此部分）。
>SEE ALSO：       相关命令或文档的参考。
>BUGS：              已知的问题或限制（也并非所有页面都有）。

**Tips**：
使用/关键词进行搜索，按n键查找下一个匹配项，按N键查找上一个匹配项。
输入`man -k 关键词`可以搜索包含关键词的手册页描述。

### help 

help命令是bash shell的内建命令，用于显示bash内建命令的帮助信息。对于外部命令（如ls、cat等），help命令通常无法提供帮助，这时应使用man命令。

使用方法：输入`help [命令名]`，例如`help cd`

注意：并非所有的Linux发行版或shell都支持help命令。在某些情况下，可能需要安装或配置额外的软件包或shell来获得此功能。


## 复制文件

``` bash
cp [选项] 源文件或目录 目标文件或目录


# 从A --> B

cp -a dst_A dst_B

```


- `-a` 在拷贝目录时使用。它保留链接、文件属性，并递归地拷贝目录，其作用等于dpR选项的组合。
- `-d` 拷贝时保留链接。
- `-f` 删除已经存在的目标文件而不提示。
- `-i` 和`f`选项相反，在覆盖目标文件之前将给出提示要求用户确认。回答y时目标文件将被覆盖，是交互式拷贝。
- `-p` 此时cp除复制源文件的内容外，还将把其修改时间和访问权限也复制到新文件中。
- `-r `若给出的源文件是一目录文件，此时cp将递归复制该目录下所有的子目录和文件。此时目标文件必须为一个目录名。
- `-l `不作拷贝，只是链接文件。



## 移动文件

``` bash

mv 源文件或目录 目标文件或目录

# A --> B
mv dir_a dir_b

```



## 重命名文件

``` bash

# 修改文件名
mv former_name new_name

# 修改文件夹名
mv former_name/ new_name/

```



## 生成txt

```bash
find /media/sde/test/ -name "*.jpg" > image.txt
```


## 查看当前文件空间大小

```bash
# 只显示一级目录（根目录）的占用大小
du -h --max-depth=0

# 显示一级和二级目录的占用大小，依次类推
du -h --max-depth=1
```

## 改变权限

```bash

chmod -R 777 ./checkpoints/

```


## 软连接


``` bash
ln -s 源地址 目的地址
ln -s <target_path> <link_name>

# 删除 软链接
rm -rf  <link_name>

```

[软件测试|Linux基础教程：ln命令与软链接和硬链接 - 知乎](https://zhuanlan.zhihu.com/p/655747663)



## 解压缩

```shell

# 压缩
tar -zcvf FileName.tar.gz DirName 
# 解压
tar -zxvf FileName.tar.gz



# zip
# 更多参考[ubuntu下解压zip文件](https://blog.csdn.net/zs520ct/article/details/72935141)
unzip test.zip
unzip -d /temp test.zip  # 解压到指定文件夹
```


[批量解压](https://blog.csdn.net/jaye16/article/details/74978408)   

``` bash

"""
假设当前目录下有多个zip文件 
data.zip invoices.zip pictures.zip visit.zip， 
"""

unzip '*.zip' 

或者 

unzip "*.zip" 
```


如果直接  `unzip *.zip`,  它等价于 `unzip data.zip invoices.zip pictures.zip`

此时会报错

> [!error]
Archive: data.zip 
caution: filename not matched: invoices.zip 
caution: filename not matched: pictures.zip 
caution: filename not matched: visit.zip 


因为会认为后面三个zip文件是在第一个zip文件里面的,因此需要 

`unzip '*.zip' `
或者 

`unzip "*.zip" `

或者 

`unzip \*.zip `
来屏蔽掉linux的通配符（man可以看到Be sure to quote any character that might otherwise be interpreted or modified by the operating system, particularly under Unix and VMS.）

或者 

`for z in *.zip; do unzip $z; done


## CUDA

``` bash

# 显示cuda版本
ll /usr/local/cuda

# 或者
nvcc -V



# cuda 切换
sudo rm -rf /usr/local/cuda
sudo ln -s /usr/local/cuda-10.0 /usr/local/cuda

```

## 其它


``` shell
# 查找当前文件夹下 txt 并统计数量
find ./ -name *.txt | wc -l

# 查看运行在gpu上的所有程序
fuser -v /dev/nvidia*

# 解压文件
unzip -d ./B6Random B6Random.zip

# 查看cpu 内存使用情况
htop

# 查看io
iostat

# 查看硬盘
df -hl
# 查看当前目录文件夹空间大小
du -h -d 1

# 查看gpu
nvidia-smi
ns
```



## 错误

1. $‘\r‘: command not found

window的回车符与linux不一样，windows为`\r\n` ，而linux下为`\n`，所以在windows下编辑脚本，在linux执行时`\r`不能被识别

解决方法：

 第一步 vim 脚本名

 第二步 在命令行模式下输入 :set ff = unix 回车

第三步保存 ：wq

成功解决问题

[运行脚本时，./start.sh: line 5: $‘\\r‘: command not found，需用vim编辑器将文件格式化\_start.sh: command not found-CSDN博客](https://blog.csdn.net/qq_39715000/article/details/119956320)

或者使用 `Notepad++` 替换。 为了更清楚的看到换行符的变化首先进行下面设置  
`视图 -> 显示符号 -> 显示行尾符`  
![](notepadpp_line.png)

然后新建一个文本在其中随意输入内容会看到显示「换行符」为 `CR LF`即 `\r \n`。
