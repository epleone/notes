---
tags:
  - tips
  - obsidian
---
# Obsidian tips
 

官方文档

[Basic formatting syntax - Obsidian Help](https://help.obsidian.md/Editing+and+formatting/Basic+formatting+syntax)

## 搜索

[Search - Obsidian Help](https://help.obsidian.md/Plugins/Search#Search+properties)


如果想搜索标有 `#todo` tag , 语法如下：

``` query_
tag: todo
```

如若想搜索 callout 则需要加上 ” “


``` query_
">[!todo]"
```

[Search for a callout - Obsidian Forum](https://forum.obsidian.md/t/search-for-a-callout-i-e-callout-is-giving-0-results/71146)

> [!warning]
> 代码块里的`query_` 要换成 `query`

## 双链使用技巧



1. 链接整篇文章
格式 ： `[[文章名]]` ，需要预览则在前面添加`!`，变成 `![[文章名]]` 


2. 链接文章下的分级标题
格式 ：`[[文章名 + # +分级标题]]`
**如果是文章内部跳转，则不需要写文章名**

编辑模式 在链接的标题名后面 +# 用对应的预览方法就可以选择自己想要链接的分级标题 点击想要的标题段落

3. 链接分级标题下的段落（块)
格式 ： `[[文章名 + # + ^ + 选择对应的块]]`
即^ 可以链接到对应的块（段落）

创建链接位置：
链接文章代码位置 ： 

随之生成了一个代码，用于在数据库中标记一个块，固定好后的代码是要对应的，最好不要改，防止不匹配

4.自定义代码块在其他文章进行引用
待引用位置 标记格式 ：`要定义的块后面 + 空格 + ^ + 自定义代码(不支持中文)`

定义好的代码 ：
引用的位置：

`[[文章名  # ^ 自定义的代码]]`

**如果是文章内部跳转，则不需要写文章名**，直接 `[[# + 跳转标题]] ` 以及 `[[^ + 跳转位置]]`， 比如 [[#dataview使用]]  或者加上 `|`使用 别名 [[#dataview使用|dataview使用别名]]


5.链接创建别名
格式： `[[文章名 + # + ^ + 自定义的代码 + | +别名]]`


`[[作业#^dd2022730|java]]`



## 脚注

定义： `[^1]: xxxx`
使用： `[^1]`


```
这是一个脚注 [^1]。
或者
这是一个脚注 ^[参考文献1]。


[^1]: 参考文献1
```

这是一个脚注 [^1]。
这是一个脚注 ^[参考文献1]。
注意，脚注只有在阅读视图下才会跳转，而且会隐藏未被引用的脚注，好处是可以从脚注跳转到引用的地方。
参考 [[相机模型以及投影笔记]]

**建议使用双链**， 在参考链接后面加上 `^ref1`,  使用双链跳转  `[[#^ref1 | 参考链接1]]`
效果如下 [[#^ref1|参考链接1]]  [[#^ref2|参考链接2]] 

或者使用

## 引用式链接

这个链接用 1 作为网址变量 [Google][1]
这个链接用 runoob 作为网址变量 [Runoob][runoob]
然后在文档的结尾为变量赋值（网址）

  [1]: http://www.google.com/
  [runoob]: http://www.runoob.com/
  

众所周知，[Markdown 中的链接](https://daringfireball.net/projects/markdown/syntax#link) 是这样写的：

```markdown
This is [an example](http://example.com/ "Title") inline link.

[This link](http://example.net/) has no title attribute.
```

这种广泛使用的链接写作方式被称为行内链接（inline link）。实际上，Markdown 中的链接还有另一种不太常用的写法，即引用式链接（reference-style link），它的形式如下：

```markdown
This is [an example] [id] reference-style link.

[id]: http://example.com/  "Optional Title Here"
```

可以发现，引用式链接在正文中使用了两个方括号，第一个方括号包裹超链接的文本部分，也就是这里的 `[an example]`，紧接着其后的是一个可有可无的空格，接下来使用第二个方括号包裹该链接的标识符，这里用 `[id]` 表示。

在下方的链接部分，首先出现链接的标识符 `[id]`，然后是一个半角冒号 `:`，接着是一个或多个空格（也可以是 Tab 键），然后是链接的 URL，最后是一个可选的链接标题，其格式与行内链接的标题格式一致，使用圆括号 `()`、双引号 `"` 或单引号 `'` 包裹。除此之外，引用式链接还可以省略链接标识符，即省略第二个方括号中的内容，这种链接叫作隐式链接（implicit link），其写作方式如下所示：

```markdown
[Google][]

[Google]: https://www.google.com
```

与行内链接相比，引用式链接有什么好处？John Gruber 在介绍 Markdown 基本语法时就给出了解释：

> The point of reference-style links is not that they’re easier to write. The point is that with reference-style links, your document source is vastly more readable.
> 
> 引用式链接的意义并不在于它们更容易书写，关键在于使用引用式链接可以大大提高 Markdown 源文件的可读性。

的确，在 Markdown 源文件中有大量链接，并且这些链接非常冗长且不够美观的情况下，行内链接不仅会明显降低源文件的可读性，甚至还会干扰我们的写作思路，而引用式链接则可以在很大程度避免这个问题。除此之外，写作过程中，我们往往不想打断写作思路去网上搜索链接，这种情况下就可以先放置一个引用式链接占位符，方便后续进行补充。如果忘记填写链接也没有关系，[Markdown 语法检查器](https://sspai.com/prime/story/markdown-linter-a-primer) 会以错误的形式标注出来，提醒我们修改。

![一份 Markdown 文件，分别使用行内链接和引用式链接的效果对比，很明显右侧引用式链接的可读性更强](一份_Markdown_文件，分别使用行内链接和引用式链接的效果对比，很明显右侧引用式链接的可读性更强.png)

一份 Markdown 文件，分别使用行内链接和引用式链接的效果对比，很明显右侧引用式链接的可读性更强


## Callout

[Callouts - Obsidian Help](https://help.obsidian.md/Editing+and+formatting/Callouts)

[Site Unreachable](https://sspai.com/post/72566)

语法：`> [!callout关键词]`

`> [!callout关键词]+`:  默认展开，显示折叠按钮 
`> [!callout关键词]-`: 默认折叠，显示折叠按钮   
`> [!callout关键词]`:   默认展开，不显示折叠按钮 

> [!todo] 
> Yes!, callouts. 


> [!todo]+ 
> Yes!, callouts. 


> [!todo]-
> Yes!, callouts. 

支持嵌套语法。

> [!question] Can callouts be nested? 
>> [!todo] Yes!, they can. 
> > > [!example] You can even use multiple layers of nesting.

支持的关键词：

>[!abstract]

> [!note]

> [!tips] 

> [!info] 

>[!todo]

> [!done]

> [!hint]

> [!important]

>[!faq]

> [!help]

>[!warning]

> [!error]

>[!bug]

> [!fail]

> [!example]

> [!quote]

> [!cite]

相关插件， callout manager，可以自定义图标和关键词，或者直接用css


可以稍微修改这个功能，实现边注。
![](https://pic1.zhimg.com/v2-26ad600de51a8f21ea04a32edc87cb1e_b.jpg)

如何在 Obsidian 中优雅的进行注释（边注） - Huajin的文章 - 知乎
https://zhuanlan.zhihu.com/p/678900439


## 修改页面宽度设置

1. 找到主题路径 `E:\Obsidian\Markdown\.obsidian\themes\Sanctum`
2. 打开`theme.css`， 搜索 `--file-line-width`
3. 默认值为 `40rem`, 显示的页面太居中了， 修改为 `80rem`，比较舒服

参考： [Obsidian 页面设置\_obsidian 页面宽度\_烟 火的博客-CSDN博客](https://blog.csdn.net/m0_58572221/article/details/127523085)




## 隐藏顶部文件名

 > there's a toggle in the settings under "appearance" called "show inline titles". Just turn that off, and the file name won't be shown inside the note anymore.


[How to hide file name? : r/ObsidianMD](https://www.reddit.com/r/ObsidianMD/comments/yi6pr1/how_to_hide_file_name/)

## 文件默认拖拽位置

![[Pasted image 20240430135452.png]]


[Attaching images to Obsidian - Fork My Brain](https://notes.nicolevanderhoeven.com/obsidian-playbook/Using+Obsidian/02+Making+Notes+in+Obsidian/Attaching+images+to+Obsidian)




# Ref

1. [Obsidian学习从0到1 —— 双链（重点）\_obsidian链接\_wu\~\~的博客-CSDN博客](https://blog.csdn.net/weixin_51684355/article/details/126084867) ^ref1
2. [Obsidian如何实现文章内跳转\_SparKgod1的博客-CSDN博客](https://blog.csdn.net/m0_61882975/article/details/133382851) ^ref2

[^1]:  [如何插入脚注 - Obsidian中文教程 - Obsidian Publish](https://publish.obsidian.md/chinesehelp/08+%E9%AB%98%E9%98%B6%E6%95%99%E7%A8%8B/%E5%A6%82%E4%BD%95%E6%8F%92%E5%85%A5%E8%84%9A%E6%B3%A8)
[^2]: 参考文献1
[^3]: 参考文献2



> [!quote]
> 1. [给 Obsidian 小白的一些建议 - 知乎](https://zhuanlan.zhihu.com/p/665164640)

