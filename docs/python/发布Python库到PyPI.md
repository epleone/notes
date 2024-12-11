# 发布Python库到PyPI

## 准备工作

- 注册一个PyPi账号：[https://pypi.org/account/register/](https://pypi.org/account/register/)
- 安装编译工具：`pip install --upgrade build`
- 安装`PyPI`上传工具:  `pip install twine`

## 目录结构

```
[mypro]/
 ├── src/
 │   └── [module]/
 │       ├── __init__.py
 │       └── [core.py]
 ├── LICENSE
 ├── README.md
 ├── requirements.txt
 └── setup.py/pyproject.toml      
```

**目录结构需保持一致**

含有中括号的表示任意名字都可以，比如： `[module]`

其中`__init__.py`用于将目录作为包导入，默认可为空；
`core.py` 是包中的一个模块，用于提供功能供下载者调用.

之前的python版本，需要定义 `setup.py` 去构建库。编写 `setup.py` 可以参考quote2.

新版本的python其实只需要定义`pyproject.toml` 构建配置文件。

可以参考：

[Python 新规范 pyproject.toml 完全解析](https://zhuanlan.zhihu.com/p/666166082)

[现代 Python 项目管理：pyproject.toml 完全指南-CSDN博客](https://blog.csdn.net/linZinan_/article/details/143474783)

[mkdocs-content-tabs/pyproject.toml](https://github.com/epleone/mkdocs-content-tabs/blob/main/pyproject.toml)

## 构建并上传

 基于`setup.py` 构建 :   `python setup.py sdist bdist_wheel`
 
 基于`pyproject.toml` 构建 :   `python -m build`


上传到 `PyPI`

```
twine upload dist/*
```

需要手动输入 `API Token`.

通过设置配置文件 `~/.pypirc`， 或者环境变量
- `TWINE_USERNAME`: 你的 PyPI 用户名
- `TWINE_PASSWORD`: 你的 PyPI API Token


配置文件 `~/.pypirc`

[Packaging and distributing projects - Python Packaging User Guide](https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/#create-an-account)

```
[pypi]
username = __token__
password = <the token value, including the `pypi-` prefix>
```

注意这里的`username` 是固定的，必须设置为 `__token__`。

使用命令`twine check dist/*` 检查配置文件是否OK (需要先生成库)。



> [!quote]
> 1. [如何发布自己的Python库？\_python发布自己的库-CSDN博客](https://blog.csdn.net/weixin_55629186/article/details/137749584)
> 2. [Python将自己的Py文件（或包）发布至pip上\_python发布自己的库-CSDN博客](https://blog.csdn.net/wo_shi_ID/article/details/129627913)
>



