---
tags:
  - tips
  - vscode
---
# VS Code tips
 


![[Pasted image 20240111155049.png]]



## python 设置

推荐插件
`Error Lens`  ：  错误提示
`Python` :   官方插件
`Pylance`:   代码补全
`Flake8`:     格式检查
`yapf`:         代码格式化


### flake8设置

``` json
 // vs 自带的设置
 // "python.linting.flake8Args": ["--max-line-length=300",
 //	"--ignore=E712,E1101,C0121,C0301,F405,E711,C0103,E0102"]
 "python.linting.flake8Args": ["--max-line-length=256", "--ignore=E501"],

// flake8插件的设置
"flake8.args": [
  "--max-line-length=256",
  "--ignore=E501"
],
```


### yapf 设置

Note this setting has to be in the global scope of `settings.json`, not in a `[python]` block.

- Set style by the following vscode settings which is equal to `yapf --style '{based_on_style: pep8, indent_width: 2}'`

``` bash
  "yapf.args": ["--style", "{based_on_style: pep8, column_limit: 330}"]


```

- Use a style file, like `.style.yapf`, `setup.cfg`, `pyproject.toml`, `~/.config/yapf/style`. For details, see [google/yapf](https://github.com/google/yapf)

``` bash
  "yapf.args": ["--style", "/home/example/.yapf"]
```

[Vscode formatter extension support for python files using yapf](https://marketplace.visualstudio.com/items?itemName=eeyore.yapf)


## cmake

[Getting Started — CMake Tools 1.4.0 documentation](https://vector-of-bool.github.io/docs/vscode-cmake-tools/getting_started.html)
