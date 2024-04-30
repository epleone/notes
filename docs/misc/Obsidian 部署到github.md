---
tags:
  - obsidian
  - todo
---
# Obsidian 部署到github


可以使用MkDocs 来部署 obsidian 文章

[Material for MkDocs](https://link.zhihu.com/?target=https%3A//github.com/squidfunk/mkdocs-material) 算是最强大最专业的博客之一，例如：[Hello 算法](https://link.zhihu.com/?target=https%3A//www.hello-algo.com/) 给人就一个非常专业的感觉。

https://github.com/Jackiexiao/foam-mkdocs-template/blob/master/README-zh.md


##  MkDocs

[搭建技术博客/个人主页 - 使用MkDocs和Material - 知乎](https://zhuanlan.zhihu.com/p/672743170)

### MkDocs 是什么？

MkDocs 是一个用于构建项目文档的工具，它可以将 Markdown 文件转换为静态网站。MkDocs 由 Python 编写，使用 [Python-Markdown](https://link.zhihu.com/?target=https%3A//python-markdown.github.io/) 解析 Markdown 文件，使用 [Pygments](https://link.zhihu.com/?target=https%3A//pygments.org/) 语法高亮代码，使用 [Jinja2](https://link.zhihu.com/?target=https%3A//jinja.palletsprojects.com/en/2.11.x/) 模板引擎渲染 Markdown 文件。

### Material 主题是什么？

Material 主题是一个基于 [Material Design](https://link.zhihu.com/?target=https%3A//material.io/) 的 MkDocs 主题，它提供了一个简洁、现代的界面，支持多种语言，支持自定义主题颜色，支持搜索、导航、侧边栏、标签、分类、评论、Google Analytics、Disqus、多种插件等功能。

## 如何使用MkDocs？

显然MkDocs可以脱离Material主题单独使用，但是Material主题依赖于MkDocs，所以我们需要先安装MkDocs。




## Material for MkDocs

[Installation - Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started/)


[GitHub - squidfunk/mkdocs-material: Documentation that simply works](https://github.com/squidfunk/mkdocs-material)

可以参考Material for MkDocs的源码来理解。





## 部署

[GitHub - jobindjohn/obsidian-publish-mkdocs: A Template to Publish Obsidian/Foam Notes on Github Pages (uses MkDocs)](https://github.com/jobindjohn/obsidian-publish-mkdocs)


### Quick start

1. Create a **new github repository using this template**. Click the green button at the top or use [this link](https://github.com/jobindjohn/obsidian-publish-mkdocs/generate).

[![](https://github.com/jobindjohn/obsidian-publish-mkdocs/raw/main/2021-11-22-22-54-02.png)](https://github.com/jobindjohn/obsidian-publish-mkdocs/blob/main/2021-11-22-22-54-02.png)

2. **Give a name** to your repository. By default your notes will be published at `<https://username.github.io/repo-name/>`
    - Copy only the `main` branch while creating the repo from the template

3. **Clone** the repository you generated **into your Obsidian folder/vault.**

4. **Move your notes** that you would like to make public to the `repo-name/docs` folder.
    - Easiest way to do this would be using drag and drop within Obsidian
    
5. Commit and **push** the changes. Github actions will take care of the rest, publishing your notes using [MkDocs](https://www.mkdocs.org/), with the [Material theme](https://squidfunk.github.io/mkdocs-material/).

6. Go to `Settings > Pages` and select the select the **Source** as your `gh-pages` branch.

[![](https://github.com/jobindjohn/obsidian-publish-mkdocs/raw/main/2021-11-22-22-52-49.png)](https://github.com/jobindjohn/obsidian-publish-mkdocs/blob/main/2021-11-22-22-52-49.png)

**Not working for you?** Open an [issue](https://github.com/jobindjohn/obsidian-publish-mkdocs/issues/new/choose) and let me know what went wrong.




 
> [!todo]
> 还需解决图片显示的问题


## Content tabs

https://squidfunk.github.io/mkdocs-material/reference/content-tabs/

语法如下，需要缩进: 
```
=== "case1"
    ...
=== "case2"
    ...
=== "case3"
    ...
```

**文字示例（不含缩进）**

=== "numpy"
``` python
import numpy as np
```
=== "pytorch"
``` python
import torch
```



**文字示例**

=== "numpy"
    ``` python
    import numpy as np
    ```
=== "pytorch"
    ``` python
    import torch
    ```


**图片示例**

=== "mano 点位顺序图"
    ![[Pasted image 20230614105754.png]]
=== "planX 点位顺序图"
    ![[Pasted image 20230614112249.png]]



**其它示例**

=== "Unordered list"

    * Sed sagittis eleifend rutrum
    * Donec vitae suscipit est
    * Nulla tempor lobortis orci

=== "Ordered list"

    1. Sed sagittis eleifend rutrum
    2. Donec vitae suscipit est
    3. Nulla tempor lobortis orci


## SuperFences

When [SuperFences](https://squidfunk.github.io/mkdocs-material/setup/extensions/python-markdown-extensions/#superfences) is enabled, content tabs can contain arbitrary nested content, including further content tabs, and can be nested in other blocks like [admonitions](https://squidfunk.github.io/mkdocs-material/reference/admonitions/) or blockquotes:


`!!!` 是 [Admonition](https://squidfunk.github.io/mkdocs-material/reference/admonitions/)语法, 和 callout很像, 也需要开启插件。

[Obsidian 插件之 Admonition - 知乎](https://zhuanlan.zhihu.com/p/391252867)

!!! example
    === "Unordered List"

        ``` markdown
        * Sed sagittis eleifend rutrum
        * Donec vitae suscipit est
        * Nulla tempor lobortis orci
        ```

    === "Ordered List"

        ``` markdown
        1. Sed sagittis eleifend rutrum
        2. Donec vitae suscipit est
        3. Nulla tempor lobortis orci
        ```