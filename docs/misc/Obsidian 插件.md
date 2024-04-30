---
tags:
  - obsidian
---

# Obsidian 插件


## Excalidraw

[GitHub - zsviczian/obsidian-excalidraw-plugin: A plugin to edit and view Excalidraw drawings in Obsidian](https://github.com/zsviczian/obsidian-excalidraw-plugin)

Excalidraw 手绘插件，可以画图

## dataview

[Obsidian 插件之 Dataview - 知乎](https://zhuanlan.zhihu.com/p/373623264)
[玩转 Obsidian 08：利用 Dataview 打造自动化 HomePage - 少数派](http://ios.zoopda.com/post/73958)
[Metadata on Tasks and Lists - Dataview](https://blacksmithgu.github.io/obsidian-dataview/annotation/metadata-tasks/)

[Dataview Date Formatting - Help - Obsidian Forum](https://forum.obsidian.md/t/dataview-date-formatting/37456)

自带的query也可以做到类似的效果，
[Search - Obsidian Help](https://help.obsidian.md/Plugins/Search)


## auto-link-title

[GitHub - zolrath/obsidian-auto-link-title: Automatically fetch the titles of pasted links](https://github.com/zolrath/obsidian-auto-link-title)

自动获取链接的名称并修改。
对于不启作用的情况，可以手动选择 `Ctrl + p` 启用



## Filename Heading Sync

[GitHub - dvcrn/obsidian-filename-heading-sync: Obisdian.md plugin to keep the filename and the first header of the file in sync](https://github.com/dvcrn/obsidian-filename-heading-sync)

保持文件名和首标题名称同步



## Highlightr

[GitHub - chetachiezikeuzor/Highlightr-Plugin: A minimal and aesthetically pleasing highlighting menu that makes color-coded highlighting much easier 🎨.](https://github.com/chetachiezikeuzor/Highlightr-Plugin)

<mark style="background: #BBFABBA6;">高亮插件，用html语法高亮文本，支持多种颜色，兼容性好，右键使用。</mark>

==这句话使用obsidian自带高亮。==



## Latex Suite

[GitHub - artisticat1/obsidian-latex-suite: Make typesetting LaTeX as fast as handwriting through snippets, text expansion, and editor enhancements](https://github.com/artisticat1/obsidian-latex-suite)

obsidian下的latex 插件，支持latex快捷方式, 也可以在配置中添加修改。好用，推荐。



## Linter

[GitHub - platers/obsidian-linter: An Obsidian plugin that formats and styles your notes with a focus on configurability and extensibility.](https://github.com/platers/obsidian-linter)

[Obsidian Linter插件：打造统一、美观的笔记环境 - 知乎](https://zhuanlan.zhihu.com/p/662183315)

[obsidian中为什么要用templater和linter插件？ - 知乎](https://zhuanlan.zhihu.com/p/681916655)

[又一个用了就离不开的Obsidian插件，真的太好用了 - 知乎](https://zhuanlan.zhihu.com/p/679602900)


很强的格式化插件。
已经设置将零散的tags 合并成 yaml 中，存储在文件的property中。

Search for ‘Linter:Lint all files in current vault’ after pressing CTRL+P.


Once downloaded and enabled, navigate to settings and click on the second tab from the left, the location of YAML functions.

- Scroll down, and you’ll find the ‘Move Tags to YAML’ section. Enable that, ensuring you have the desired form of action selected. E.g., If you wish to remove the tag from the body of the note, select ‘Remove whole tag’.
- Then, click the three dots and find ‘Lint File’ from the dropdown on a test file, just to ensure it’s behaving as expected _**before**_ Linting every file in your vault.
- Your entire vault can be done with a single command. Search for ‘Linter:Lint all files in current vault’ after pressing CTRL+P.

[How to move tags and links to properties - Help - Obsidian Forum](https://forum.obsidian.md/t/how-to-move-tags-and-links-to-properties/70748/2)


## Local Images

[GitHub - aleksey-rezvov/obsidian-local-images](https://github.com/aleksey-rezvov/obsidian-local-images)

下载网络图片到指定位置，
使用命令 `Download images *`

这个插件有个plus版本，[GitHub - Sergei-Korneev/obsidian-local-images-plus: This repo is a reincarnation of obsidian-local-images plugin which main aim was downloading images in md notes to local storage.](https://github.com/Sergei-Korneev/obsidian-local-images-plus) 

但这个我觉得已经够用了。


## Remotely Save

[GitHub - remotely-save/remotely-save: Yet another unofficial Obsidian plugin allowing users to synchronize notes between local device and the cloud service. Supports S3, Dropbox, OneDrive, webdav.](https://github.com/remotely-save/remotely-save)

文件同步插件，可以将文件同步到 支持`Webdav` 的网盘上。好用。

现在的方案是用git同步，用MkDocs发布。感觉也足够用了。

> [!warning]
> **用webdav连接坚果云显示错误503**

 目前webdav有三个限制：
 
文件上传大小限制：当前 WebDAV 客户端和网页端上传大小的限制是一致的，默认为 500M。

访问频率限制：由于WebDAV协议比较占用系统资源，免费版用户限制访问频率为每30分钟不超过600次请求。付费用户限制访问频率为每30分钟不超过1500次请求。

同步目录数限制：目前坚果云的WebDAV协议单次请求文件数（包含文件和文件夹）为750个，支持分多页多次加载。如果您使用WebDAV的三方工具未实现按分页多次加载，可能会出现文件同步不完整的情况，建议您使用坚果云客户端进行直接同步。

出现503错误一般是超过了访问频率导致的，建议您等待一段时间重新同步即可。


## 其它插件

### commander

[GitHub - phibr0/obsidian-commander: Commander - Obsidian Plugin | Add Commands to every part of Obsidian's user interface](https://github.com/phibr0/obsidian-commander)


### Outliner

[GitHub - vslinko/obsidian-outliner: Work with your lists like in Workflowy or RoamResearch](https://github.com/vslinko/obsidian-outliner)


### Mind Map

[GitHub - lynchjames/obsidian-mind-map: An Obsidian plugin for displaying markdown notes as mind maps using Markmap.](https://github.com/lynchjames/obsidian-mind-map)

### QuickAdd

[GitHub - chhoumann/quickadd: QuickAdd for Obsidian](https://github.com/chhoumann/quickadd)


### BRAT

[Obsidian最强编辑插件之——增强编辑 - 知乎](https://zhuanlan.zhihu.com/p/619924782?utm_id=0)

文本增强，去除空行空格


### 网页保存

用 MaoXian 网摘保存网络文章到 Obsidian

[用 MaoXian 网摘保存网络文章到 Obsidian](https://mika-cn.github.io/maoxian-web-clipper/obsidian-zh-CN.html)



##  Image ClassifyPaste

[GitHub - ostoe/Ob-ImagePastePlugin](https://github.com/ostoe/Ob-ImagePastePlugin)

obsidian直接黏贴文件会使用双链 `![[]]` 语法, 对网上blog兼容性不好。
这个插件可以使用`Markdown` 中常用的 `![]()` 语法， 且可以更自由的设定保存路径，可以在每个文件夹下创建`.assert` 文件夹保存。
这个插件还存在一些bug. 看后续。

> [!tips] MkDocs支持双链显示图片，这个插件的作用不大了。
