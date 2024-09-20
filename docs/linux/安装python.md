---
tags:
  - conda
  - pip
---

# 安装python

ubuntu下建议使用miniconda作为python环境

[Miniconda — conda documentation](https://docs.conda.io/en/latest/miniconda.html)

- 更多版本信息
[Miniconda hash information — conda documentation](https://docs.conda.io/en/latest/miniconda_hashes.html)

下载对应的版本, linux一般是下载 `*Linux-x86_64.sh`

```bash
# linux一般是下载 `*Linux-x86_64.sh`

# 安装
# 安装过程中，按要求输入，一般输入yes 同意即可
# ! 注意，最后的conda init，可以拒绝，它会中终端上加上conda环境前缀，(base)
bash *Linux-x86_64.sh

# 将Miniconda设置为默认的Python环境
vim ~/.bashrc

# 添加安装路径到环境变量中
export PATH="/path/to/miniconda/bin:$PATH"

# 生效环境变量
source ~/.bashrc

```

- pytorch安装
[Previous PyTorch Versions | PyTorch](https://pytorch.org/get-started/previous-versions/)

- pytorch3D 安装

[pytorch3d/INSTALL.md at main · facebookresearch/pytorch3d · GitHub](https://github.com/facebookresearch/pytorch3d/blob/main/INSTALL.md)

不想自己编译的话，可以安装官方预编译的wheel

> We have prebuilt wheels with CUDA for Linux for PyTorch 1.11.0, for each of the supported CUDA versions, for Python 3.8 and 3.9. This is for ease of use on Google Colab. These are installed in a special way. For example, to install for Python 3.8, PyTorch 1.11.0 and CUDA 11.3
> `pip install --no-index --no-cache-dir pytorch3d -f https://dl.fbaipublicfiles.com/pytorch3d/packaging/wheels/py38_cu113_pyt1110/download.html`

实测 py39_cu113_pyt1121 也加入预编译了, 对应python3.9 下的pytorch 1.12.1版本

可以通过这段代码获取安装脚本

```python
import sys
import torch
pyt_version_str=torch.__version__.split("+")[0].replace(".", "")
version_str="".join([
    f"py3{sys.version_info.minor}_cu",
    torch.version.cuda.replace(".",""),
    f"_pyt{pyt_version_str}"
])

!pip install fvcore iopath

!pip install --no-index --no-cache-dir pytorch3d -f https://dl.fbaipublicfiles.com/pytorch3d/packaging/wheels/{version_str}/download.html
```

安装常用python库

```python
# 安装常用python库
pip install rich
pip install imgaug
pip install h5py
pip install ptflops
pip install flake8
pip install open3d

# 判断matplotlib的TKAgg是否支持x11
python3 -m tkinter

# 安装onnxsim
pip3 install -U pip && pip3 install onnxsim


pip install tensorboard
pip install prefetch_generator
pip install vit-pytorch

# for mano
pip install trimesh
pip install chumpy

# cannot import name `bool` from `numpy`, 降级numpy
pip install numpy==1.23
```

## 包管理

### PIP

#### 国内镜像和源

```bash
# 清华源
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 阿里源
pip config set global.index-url http://mirrors.aliyun.com/pypi/simple


# 临时使用
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple  XXXX
```



```sh
# 使用清华镜像
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 使用中科大镜像
pip config set global.index-url https://pypi.mirrors.ustc.edu.cn/simple

# 取消镜像
pip config unset global.index-url
```

  

#### 多版本pip

```sh
# 在多版本python中，可以使用-m, 调用python下面的pip模块

python -m pip libname
python3.5  -m pip linname

```

  

#### 清除缓存

```sh
# Ubuntu
sudo rm -r ~/.cache/pip

#Windows
进入目录 C:\Users\"用户名"\AppData\Local\pip，删除该目录下的cache文件夹即可。
```

### Conda

> [!todo]

## 环境管理

主流环境管理工具有[pyenv](https://github.com/pyenv/pyenv)、[virtualenv](https://github.com/pyenv/pyenv-virtualenv)、`pipenv` 和 [conda](https://github.com/conda/conda)

1. **pyenv**
功能重点：pyenv主要用于管理不同版本的Python解释器。它允许你在同一系统中安装和使用多个版本的Python，而不会相互冲突。
使用场景：适用于需要在同一系统中切换多个Python版本的场景，如测试不同Python版本的兼容性。

2. **virtualenv**
功能重点：virtualenv是一个用于创建隔离的Python环境的工具。每个环境都可以有自己的Python版本（虽然这通常是由pyenv或系统Python提供的）和一组库。
使用场景：适用于需要为每个项目创建隔离的Python环境的场景，避免不同项目之间的依赖包版本冲突。

3. **pipenv**
功能重点：pipenv旨在将pip和virtualenv的功能结合在一起，提供一个更加便捷的包管理和环境隔离方案。它使用Pipfile和Pipfile.lock来管理依赖，更加直观和方便。
使用场景：适用于需要同时管理项目依赖和隔离环境的场景，特别是在注重开发流程和环境一致性的项目中。

4. **conda**
功能重点：conda是一个跨平台的包和环境管理器，它可以安装Python以及其他语言的包。conda特别适用于处理复杂的依赖关系和非Python包，如在数据科学和机器学习项目中常用的库。Conda不仅限于Python软件包，它还可以用来管理来自不同语言的软件包，如R、Ruby、Lua、Scala、Java、JavaScript、C/C++等，这使得Conda成为一个跨语言的环境管理器。
使用场景：适用于需要管理跨语言依赖、特别是在数据科学和机器学习领域的项目，或者在需要快速安装和管理复杂依赖的环境中。

总结：

**Python版本管理：pyenv**
虚拟环境隔离：virtualenv
依赖管理+虚拟环境：pipenv
**跨平台包和环境管理，适用于复杂依赖：conda**

### pyenv

``` bash
# virtual environments from pyenv
pyenv install 3.6.9
pyenv virtualenv 3.6.9 new-env
pyenv activate new-env
pyenv deactive
# You can also use `pyenv local`
```

[zhuanlan.zhihu.com/p/609726377](https://zhuanlan.zhihu.com/p/609726377)

[zhuanlan.zhihu.com/p/668259686](https://zhuanlan.zhihu.com/p/668259686)

### Conda

``` bash

# virtual environments from conda
conda create -n new-env python=3.6
conda env list
conda activate new-env
conda deactivate

```




``` shell
# 创建环境
conda create -n tensorflow1.15 python=3.8.18
conda activate tensorflow1.15
conda install cudatoolkit=10.0
conda install cudnn=7.3.1

# 安装tf
pip install --user nvidia-pyindex
pip install --user nvidia-tensorflow[horovod]

```

[Tensorflow 1.15 + CUDA + cuDNN installation using Conda - Stack Overflow](https://stackoverflow.com/questions/64811841/tensorflow-1-15-cuda-cudnn-installation-using-conda)