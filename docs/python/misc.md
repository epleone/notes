
# misc

``` bash
# 查看cuda版本和torch版本
python -c 'import torch;print(torch.__version__);print(torch.version.cuda)'
```

[ImportError: libtorch\_cuda\_cu.so: cannot open shared object file: No such file or directory-CSDN博客](https://blog.csdn.net/AliceH1226/article/details/128828776)


## python环境

如果需要导入的module的名字是m1，则解释器必须找到m1.py，
1. 它首先在**文件所在的当前目录**查找，
2. 然后是在**环境变量PYTHONPATH**中查找。
    PYTHONPATH可以视为系统的PATH变量一类的东西，其中包含若干个目录。
3. 如果都找不到，Python会察看默认路径。UNIX下，默认路径一般为/usr/local/lib/python/


需要注意在目录`root`下运行 `python bin/run.py` 时。会将 `root/bin` 而不是 `root`加入到`PYTHONPATH`. 如果需要将根目录加入，需要手动设置 `export PYTHONPATH=.`  或者 `set PYTHONPATH=.`(windows bat)。


```bash
export CUDA_VISIBLE_DEVICES=0
export PYTHONPATH=.
python bin/inference.py

# 可以合写为：
export CUDA_VISIBLE_DEVICES=0;export PYTHONPATH=.;python bin/inference.py
```


## 填充字符串

``` python

# 方法一:f-strings, 支持空格和0填充
i = 1 
s = f"{i:5d}.png"   # "    1.png", 用空格填充
s = f"{i:05d}.png"  # "00001.png", 用0填充

# `<` : 左对齐。
# `>` : 右对齐（默认）。
# `^` : 居中对齐。
s = f"{i:<05d}.png"   # '10000.png'
s = f"{i:^05d}.png"   # '00100.png'


# 方法二
# 指定字符串长度为width，前面填充0
str.zfill(width)

# 方法三
# 指定字符串长度为width，默认填充空格，右对齐
str.rjust(width[, fillchar])

# 等价于zfill
str.rjust(width, '0')

# ljust是左对齐
str.ljust(width[, fillchar])

```

