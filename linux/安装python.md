#conda

# Miniconda

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


# 换pip源

```bash
# 清华源
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 阿里源
pip config set global.index-url http://mirrors.aliyun.com/pypi/simple


# 临时使用
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple  XXXX
```