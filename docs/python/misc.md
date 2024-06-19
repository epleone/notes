
# misc

``` bash
# 查看cuda版本和torch版本
python -c 'import torch;print(torch.__version__);print(torch.version.cuda)'
```

[ImportError: libtorch\_cuda\_cu.so: cannot open shared object file: No such file or directory-CSDN博客](https://blog.csdn.net/AliceH1226/article/details/128828776)


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

