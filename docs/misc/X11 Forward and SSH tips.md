---
tags:
  - ssh
---
# X11 Forward and SSH tips
 


## X11 forward

### 需要的软件

[Xming](https://xming.en.softonic.com/download)

[VcXsrv](https://sourceforge.net/projects/vcxsrv/files/vcxsrv/) 

`Windows Terminal`  or  `PowerShell`  or  `cmd`



### 安装 Xming / VcXsrv

1. 安装完成后，打开XLaunch，记住Display Number，一般是0，--> Start no client -->Clipbard-->完成
    - 此步骤可以保存为命令行 类似：vcxsrv.exe :0 -clipboard -multiwindow -ac
2. 设置ip允许，首先打开Xming安装文件夹找到 X*.hosts 文件 (*号为上面Display Number 数字），如本例就是找到X0.hosts文件，打开并在localhost下面一行，添加Linux服务器的IP地址



## SSH

登录Linux系统后，切换到对应的打开软件的账号：

- 在本地的 ~/.ssh/config 添加配置

    ```shell
    Host CS
        hostname 255.255.255.255
        user zhuangqiubin
        ForwardX11 yes
        ForwardX11Trusted yes
    ```


- 设置DISPLAY：export DISPLAY=255.255.255.255 : 0.0 



```shell
# 可以写到~/.bashrc里面去
vim ~/.bashrc

# 我的机器
export DISPLAY=ip地址:0.0
```

- 可能需要使用 `xhost +`

- 在Linux主机上需要的确认配置打开，一般都是打开的：

    ``` shell
    
    vim /etc/ssh/sshd_config
    
    X11Forwrding yes
    ```

### 私钥登录

1. **在windows上:** 使用命令 `ssh-keygen`  生成密钥对。（因为是在windows上连接服务器, 已经生成好的可以复用）
   
``` bash
ssh-keygen -t rsa

# 或者指定生成名称
ssh-keygen -t rsa -b 4096 -C "ubuntu server" -f ~/.ssh/ubuntu_id_rsa

# 会生成两个文件，私钥：ubuntu_id_rsa，公钥：ubuntu_id_rsa.pub
   
```


   [ssh-keygen密钥 - 知乎](https://zhuanlan.zhihu.com/p/514903590)
   

2. **在服务器上:** 将生成的密钥对放入服务器的 `~/.ssh`文件夹下，（如果没有该文件夹，可以自己创建，也可以使用命令 ``` ssh-keygen``` 生成，但要删除它在服务器上生成的密钥对，只需要windows上生成的）

3. **在服务器上:** 使用``` cat id_rsa.pub >> authorized_keys ```, 在服务器上安装公钥

4. **在windows上:**  在 `用户名/.ssh/config` 配置好 `IdentityFile`项

``` yaml

Host server-xx
   HostName 172.xx.xx.72
   User root
   Port xxx
   ForwardX11 yes
   ForwardX11Trusted yes
   IdentityFile C:\Users\admin\.ssh\rsa_id  # 注意对应
   	
```

5. **在windows上:**  在cmd中输入 ``` ssh server-xx```，即可使用私钥免密登录服务器



``` bash
# 生成密钥

ssh-keygen -t rsa -b 4096 -f ~/data/key/id_test_rsa -C "xxxxxx@163.com"
-t 密钥类型, dsa | ecdsa | ed25519 | rsa
-b RSA类型密钥的大小（长度），通常至少应该是 2048，默认 3096
-f 指定私钥的文件名，e.g. ～/.ssh/private_key_name
-C 指定一个注释

#~/data/key下会生成两个文件，私钥：id_test_rsa，公钥：id_test_rsa.pub

# 将公钥放到 ~/.ssh/authorized_keys 文件中
cat id_rsa.pub >> authorized_keys

# 私钥登录
ssh user@host/ip[:port] -i [identity_file]

e.g. root@106.14.23.168 -i ~/.ssh/id_rsa

-i identity_file 指定私钥文件

```
​    

​	[VS Code Remote SSH配置](https://zhuanlan.zhihu.com/p/68577071)

​	[设置 SSH 通过密钥登录](https://www.runoob.com/w3cnote/set-ssh-login-key.html)   


### 文件权限

- `~/.ssh` 目录的权限：应为 `700`（即 `drwx------`）。
- 私钥文件 `id_rsa` 的权限：应为 `600`（即 `-rw-------`）。
- 公钥文件 `id_rsa.pub` 的权限：应为 `644`（即 `-rw-r--r--`）。
- `authorized_keys`文件的权限：应为 `600`（即 `-rw-------`）。

``` bash

# 注意不同文件的权限是不一样的。

# 设置 ~/.ssh 目录的权限700
chmod 700 ~/.ssh

# 设置私钥文件的权限600
chmod 600 ~/.ssh/id_rsa

# 设置公钥文件的权限644
chmod 644 ~/.ssh/id_rsa.pub

# 设置 authorized_keys 文件的权限600
chmod 600 ~/.ssh/authorized_keys

chmod 600 ~/.ssh/config

```


需要在`config`在加上需要访问的网站地址， 文件内容如下：

``` Text
# GitLab.com
Host gitlab.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/ubuntu_id_rsa


# Private GitLab instance
Host gitlab.company.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/example_com_rsa


```

`ssh -T git@gitlab.company.com` 验证是否链接成功。

## 验证

``` shell
# 输入 可弹出时钟窗口
xclock
```


## 参考链接

[x11-forward，使用远程机器的gui程序](https://www.cnblogs.com/zqb-all/p/10327444.html) 

[Xming +Xshell 6 实现远程打开Linux界面](https://blog.csdn.net/akuoma/article/details/82182913) 

[export DISPLAY=:0.0的解释](https://blog.csdn.net/rivarola/article/details/41041931) 

