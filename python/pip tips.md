
#python  #pip #tips 

# 多版本python

```sh
在多版本python中，可以使用-m, 调用python下面的pip模块

python -m pip ***
python3.5  -m pip ***
```

  

# 国内镜像

```sh
# 使用清华镜像
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 使用中科大镜像
pip config set global.index-url https://pypi.mirrors.ustc.edu.cn/simple

# 取消镜像
pip config unset global.index-url
```

  

# 清除缓存

```sh
# Ubuntu
sudo rm -r ~/.cache/pip

# Windows
进入目录 C:\Users\"用户名"\AppData\Local\pip，删除该目录下的cache文件夹即可。
```

