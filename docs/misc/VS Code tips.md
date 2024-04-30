---
tags:
  - tips
  - vscode
---
# VS Code tips
 


![[Pasted image 20240111155049.png]]

## flake8设置

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