---
tags: [bash]
---
# Windows bash

- 在Windows上生成图片列表

```bash

# 选择当前文件夹下的 图片文件，并生成绝对路径到 `image_list.txt` 中
Get-ChildItem -Recurse -Include *.jpg, *.png | Select-Object -ExpandProperty FullName | Out-File -FilePath image_list.txt

# 用`\\` 代替 `/` 当文件分割符, 编码设置成UTF8
Get-ChildItem -Recurse -Include *.jpg, *.png | ForEach-Object { $_.FullName -replace '/', '\\\\' } | Out-File -Encoding UTF8 -FilePath image_list.txt


# 剔除结果中不需要的文件
Get-ChildItem -Recurse -Include *.jpg, *.png | Where-Object { $_.Name -notlike "*exclude*.jpg" -and $_.Name -notlike "*exclude*.png" } | Select-Object -ExpandProperty FullName | Out-File -FilePath image_list.txt

Get-ChildItem -Recurse -Include *.jpg, *.png | Where-Object { $_.Name -notlike "*_rslt.*" } | Select-Object -ExpandProperty FullName | Out-File -FilePath image_list.txt


# 用`\\` 代替 `/` 当文件分割符
Get-ChildItem -Recurse -Include *.jpg, *.png | Where-Object { $_.Name -notlike "*_rslt.*" } | ForEach-Object { $_.FullName -replace '/', '\\\\' } | Out-File -Encoding UTF8 -FilePath image_list.txt

```

# cmd bash

```bash
# 生成相对路径，在图片文件夹里运行
dir \*.jpg /B /S > image_list.txt

# 生成绝对路径，跑到上一层目录就行
cd ..
dir images\*.jpg /B /S > image_list.txt
```


# 临时添加环境变量路径

在命令提示符（cmd）或 PowerShell 中，可以使用 `set` 命令临时添加环境变量路径。

在命令提示符中，使用以下命令将路径添加到环境变量中：

shellCopy code

`set PATH=%PATH%;Your\Path\Here`

在 PowerShell 中，使用以下命令将路径添加到环境变量中：

powershellCopy code

`$env:PATH += ";Your\Path\Here"`

请将 `Your\Path\Here` 替换为要添加的路径。确保在路径中使用正确的路径分隔符（在 Windows 中为反斜杠 `\`）。

这样添加的环境变量路径只在当前会话中有效，关闭命令提示符或 PowerShell 后将不再有效。如果需要永久添加环境变量路径，请参考操作系统的设置界面或使用专门的工具来进行配置。

# 脚本注释

[一篇文章彻底搞清楚Windows系统批处理脚本.bat文件的注释\_bat文件注释\_全栈老司机的博客-CSDN博客](https://blog.csdn.net/qq_35421305/article/details/115208862)


# 软链接

**使用管理员权限打开`cmd`**

``` bash
mklink /d D:\project\dst D:\project\src

# 文件夹需要夹/d 文件不需要
```

例子里是创建文件夹软链接 左边的目录是生成的目标， 右边是源文件夹目录

[win10 文件|文件夹软链接 相当于ln -s\_win10软连接文件夹-CSDN博客](https://blog.csdn.net/a2689418930/article/details/125302369)

[windows下的mklink命令 - 姬雨晨 - 博客园](https://www.cnblogs.com/jiyuchen1/p/17987663)


