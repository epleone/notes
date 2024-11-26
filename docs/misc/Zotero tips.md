---
tags:
  - zotero
  - tips
---

# Zotero tips

[Zotero！还有多少惊喜我不知道！ - 知乎](https://zhuanlan.zhihu.com/p/630691757)

[5.1 碎片知识聚沙成塔：细粒度文献笔记](https://zotero.yuque.com/staff-gkhviy/better-notes/ssga9d)

## zotero联动 obsidian

[Zotero + Obsidian联动方案汇总盘点 - 2023最全 - 知乎](https://zhuanlan.zhihu.com/p/651144180)

zotero的文字 图片可以直接拖到白板中，生成zotero链接。

## zotero 自动笔记流

如何根据注释的颜色标签，自动创建相应的标签的笔记内容?

[Zotero7.0笔记流：小白也能玩转的超详细教程！【六插件联用】\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1paWeepEfW/?vd_source=e926bae6bd411e2d3129d4f73c310b9c)

需要安装三个插件

- [zotero-actions-tags](https://github.com/windingwind/zotero-actions-tags)
- [Zotero-style](https://github.com/muisedestiny/zotero-style#readme)
- [Zotero Better Notes](https://github.com/windingwind/zotero-better-notes)

<mark style="background: #BBFABBA6;">在插件`style` 中开启`Annotation Colors`,  添加颜色标注（非必须，但非常推荐）。</mark>

![[Pasted image 20241125151445.png]]

<mark style="background: #BBFABBA6;">在插件`actions-tags` 添加动作，实现功能 `根据注释颜色，添加标签`。</mark>

![[Pasted image 20241125151101.png]]

``` js

/**
 * 注释：根据注释颜色增加标签
 * @author appwcn windingwind
 * @link https://github.com/windingwind/zotero-actions-tags/discussions/339
 */
 
// EDTI TAG MAPPING BELOW
const tags = {
	"#008b8b": "新概念",
    "#ffd400": "创新点",
    "#ff6666": "研究背景",
    "#5fb236": "重点",
    "#2ea8e5": "实验tips",
    "#a28ae5": "例句",
    "#e56eee": "单词",
    "#f19837": "数据集",
    "#aaaaaa": "缺点",
}
 
if (!item) {
    return;
}
 
if (!item.isAnnotation() || !item.annotationColor) {
    return;
}
 
const tag = tags[item.annotationColor];
item.addTag(tag);
return `Tag added: ${tag}`;
```

**这个脚本还有一个问题，改变标注颜色不会改变标签**。详细讨论参考 [issue](https://github.com/windingwind/zotero-actions-tags/discussions/339)。

> [!warning] **颜色代码需要小写**
> 比如 `#008B8B`需要写成`#008b8b` 。
> 否则会报错 `Script Error : Tag data must contain 'tag' property`

 
 
 

<mark style="background: #BBFABBA6;">在插件`Better Notes` 中添加条目模板。</mark>

``` js

${
  await new Promise(async (r) => {
    const getAnnotationsByTag = async (tagName) => {
      const pdfItem = await topItem.getBestAttachment();
      let result = "";
      for (let annoItem of pdfItem.getAnnotations()) {
        if (annoItem.getTags().find(i => i.tag.includes(tagName))) {

          const res = Zotero.EditorInstanceUtilities.serializeAnnotations(
            [
              {
                ...(await Zotero.Annotations.toJSON(annoItem)), ...{ attachmentItemID: annoItem.parentID }
              }
            ]
          );
          result += res.html;
        }
      }
      return result;
    };

    // 辅助函数：检测字符串是否包含中文字符
    const containsChinese = (text) => /[\u4e00-\u9fa5]/.test(text);

    // 检查标题是否为中文
    let isChineseTitle = containsChinese(topItem.getField("title"));

    // 获取作者显示内容
    let creators = topItem.getCreators();
    let authorDisplay = "";
    if (isChineseTitle) {
      // 中文标题：显示第一作者全名，格式为“姓 前名”
      if (creators.length > 0) {
        let firstAuthor = creators[0];
        let fullName = `${firstAuthor.lastName} ${firstAuthor.firstName}`;
        authorDisplay = fullName;
      }
    } else {
      // 非中文标题：显示第一作者姓氏并附加“et al.”
      if (creators.length > 0) {
        let firstAuthorLastName = creators[0].lastName;
        authorDisplay = `${firstAuthorLastName} et al.`;
      }
    }

    // 生成标题，处理中文和英文情况
    let res = `<h1><span style="background-color: #B1A5E1">论文笔记</span>-${topItem.getField("title")}</h1>
<p><strong><span style="color: #283593">原名：</span></strong>${topItem.getField('title')}</p>
<p><strong><span style="color: #283593">作者：</span></strong>${authorDisplay}</p>
<p><strong><span style="color: #283593">发表时间：</span></strong>${topItem.getField('date')}</p>
 
<!-- 本地链接 -->
<tr>
  <td style="color:#193c47; background-color:#dbeedd; padding:8px;">
    ${(() => {
        const attachments = Zotero.Items.get(topItem.getAttachments());
        if (attachments && attachments.length > 0) {
          return `<b><span style="color: #283593">Zotero链接:</span> </b><a href="zotero://open-pdf/0_${attachments[0].key}">${attachments[0].getFilename()}</a>`;
        } else {
          return `<b><span style="color: #283593">Zotero链接:</span> </b>`;
        }
      })()}
  </td>
</tr>
</p>

<h2><span style="color: #A61B41">🌏 研究背景：</span></h2>
<p> ${await getAnnotationsByTag('研究背景')} </p>

<h2><span style="color: #A61B41">✨ 创新点：</span> </h2>
<p>${await getAnnotationsByTag('创新点')}</p>
<p></p >

<h2><span style="color: #A61B41">🙋‍♀️ 新概念和术语：</span> </h2>
<p>${await getAnnotationsByTag('新概念')}</p>
<p></p >

<h2><span style="color: #A61B41">💡 重点关注：</span> </h2>
<p>${await getAnnotationsByTag('重点')}</p>
<p></p >

<h2><span style="color: #A61B41">📚 实验TIPS：</span></h2>
<p>${await getAnnotationsByTag('实验tips')}</p>
<p></p >
 
<h2><span style="color: #A61B41">🔬 数据集：</span></h2>
<p>${await getAnnotationsByTag('数据集')}</p>
<p></p >

<h2><span style="color: #A61B41">📌 待改进：</span></h2>
<p>${await getAnnotationsByTag('缺点')}</p>
<p></p >

<h2><span style="color: #A61B41">📜 英文学习：</span></h2>
<p>${await getAnnotationsByTag('单词')}</p>
<p></p >
<p>${await getAnnotationsByTag('例句')}</p>
<p></p >

<h2><span style="color: #A61B41">💭 思考启发：</span></h2>
<b>TODO</b>
<p></p >

`;

    r(res);
  })
}

```

## 插件推荐

**Zotero 插件开发中文文档**  
[https://zotero.yuque.com/staff-gkhviy/developer/sdihk2](https://zotero.yuque.com/staff-gkhviy/developer/sdihk2)

[JavaScript 教程](https://www.w3school.com.cn/js/index.asp)

[Zotero！还有多少惊喜我不知道！ - 知乎](https://zhuanlan.zhihu.com/p/630691757)

ZOTERO默认的全文保存位置在C盘, 可以在Edit--Preferences--Advanced-Files and Folders下面设置保存路径。

修改后记得把原来的复制过来。

### 1. [ZotFile](https://github.com/jlegewie/zotfile)

### 2. [Zutilo](https://github.com/wshanks/Zutilo)

### 3. Zotero 工具箱

[http://zotfile.com/index.html#changelog](http://zotfile.com/index.html#changelog)

主要功能就是设置快捷键

### 4. **[Zotero Better Notes](https://github.com/windingwind/zotero-better-notes)**

[https://zhuanlan.zhihu.com/p/663491418](https://zhuanlan.zhihu.com/p/663491418)

[note-templates](https://github.com/windingwind/zotero-better-notes/discussions/categories/note-templates)

[5.1 碎片知识聚沙成塔：细粒度文献笔记](https://zotero.yuque.com/staff-gkhviy/better-notes/ssga9d)

### 5. [zotero-pdf-translate](https://github.com/windingwind/zotero-pdf-translate#readme)

### 6. [Zotero Reference](https://github.com/muisedestiny/zotero-reference#readme) (disable)

### 7. **[zotero-gpt](https://github.com/MuiseDestiny/zotero-gpt)**

[https://zhuanlan.zhihu.com/p/621726219](https://zhuanlan.zhihu.com/p/621726219)

[https://github.com/chatanywhere/GPT_API_free](https://github.com/chatanywhere/GPT_API_free)
