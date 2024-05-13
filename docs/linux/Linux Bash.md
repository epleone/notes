---
tags: [bash]
---
# Linux Bash


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

