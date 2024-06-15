# ffmpeg和视频抽帧

## 安装

推荐使用`conda`安装

```bash
# apt 安装
apt install ffmpeg

# conda 安装
conda install ffmpeg

ffmpeg version
```

## 单个抽帧

``` bash
# 将名为123.mp4的视频文件抽成一张张的图片（抽帧）
ffmpeg -i "123.mp4" -r 5 -q:v 2 -f image2 %d.jpg
```

-i 是用来获取输入文件的名称，-i “123.mp4” 就是获取“123.mp4”视频文件。
-r 是设置每秒提取图片的帧数，也即采样率，-r 5的意思就是每秒抽取5张视频帧；
-q:v 2 这个可以提高抽取到的图片的质量的，数字越小，质量最高，1最高；
-f 是设定输出格式，这里image2就是图像解析模式，还有contact连接模式等；


## 批量抽帧

``` bash

#!/usr/bin/env bash
 
videos_root=/home/kjp/data/video/test/SquareDancing  # 视频目录
save_root=/home/kjp/data/video/test/picture  # 视频帧目录
 
for video in $videos_root/*;
do
echo $video
save_dir=$save_root$(basename $video)
if [ ! -d $save_dir ];then
mkdir $save_dir  # 生成文件夹
fi
ffmpeg -i $video -r 2 -q:v 2 -f image2 $save_dir/%05d.jpeg  # 抽帧
 
done

```


python脚本

``` python

import os
from glob import glob

# 将视频长边缩放到1080
def convert_video_to_images(input_file, output_folder):
    ffmpeg_cmd = (f'ffmpeg -i "{input_file}" '
                  f'-vf "scale=\'if(gt(iw,ih), 1080, -1)\':\'if(gt(iw,ih), -1, 1080)\'" '
                  '-q:v 1 -vsync vfr '
                  f'"{output_folder}/%05d.jpg"')

    try:
        os.system(ffmpeg_cmd)
        print('convert successful！')
    except Exception as e:
        print(f'Error: convert {e}')
    pass


if __name__ == "__main__":
    root_folder = "/media/video/data"
    root_output = "/media/video/save_data"

    videos = glob(f"{root_folder}/**/*.mp4", recursive=True)
    for p in videos:
        output_dir = os.path.splitext(p.replace(root_folder, root_output))[0]
        print(f"mkdir {output_dir}")
        if not os.path.exists(output_dir):
            os.makedirs(output_dir)

        convert_video_to_images(p, output_dir)
    pass

```


## 视频处理常用操作

### 缩放

`scale` 是一个视频滤镜（video filter），用于对视频进行缩放。`scale` 参数的格式为`scale=w:h`，其中 `w`表示缩放后的视频宽度，`h` 表示缩放后的视频高度。

**scale 参数有以下三种常用的用法**：

1. 固定宽度或高度，等比例缩放：
	- scale=-1:720 ：将视频的宽度等比例缩放，使得高度为 720 像素。
	- scale=1280:-1：将视频的高度等比例缩放，使得宽度为 1280 像素。
2. 指定宽度和高度，非等比例缩放：
	- scale=640:480：将视频缩放为宽度为 640 像素，高度为 480 像素的大小。
 3. 指定宽度和高度，等比例缩放并填充黑边:
	 - scale=1280:720:force_original_aspect_ratio=decrease,pad=1280:720:(ow-iw)/2:(oh-ih)/2：将视频缩放为宽度为 1280 像素，高度为 720 像素的大小，并在视频周围填充黑边。这种方式可以保持视频的宽高比不变。


这个参数需要搭配`-vf` 参数 ，也即 video filter。

``` bash
ffmpeg -i input.mp4 -vf scale=640:-1 output.mp4

```

将某一边缩放到指定尺寸
``` bash
ffmpeg -i input.mp4 -vf "scale='if(gt(iw,ih),-1,720)':'if(gt(iw,ih),720,-1)'" -c:a copy output.mp4

ffmpeg -i input.mp4 -vf "scale='if(gt(iw,ih),min(iw,720),-1)':'if(gt(iw,ih),-1,min(ih,720))'" -q:v 1 -vsync vfr %05d.png

```

- `-q:v 1`：
    - 这是视频质量的参数选项，`-q:v` 表示视频质量。
    - `1` 是指定的质量参数，数字越小表示质量越高，这里是第一档质量。
- `-vsync vfr`：
    - `-vsync` 是垂直同步参数，`vfr` 表示可变帧率。
    - 这个选项告诉 `ffmpeg` 使用可变帧率输出图片。

`%05d.png` 表示例如 `00001.png`，`00002.png` 等。

参数中可以使用`iw`、`ih`指代原视频宽高。

`if(gt(iw,ih),-1,720)` 语法是 `if(condition, true_value, false_value)` , `gt(iw,ih)` 是指视频的宽度大于高度。

所以`scale='if(gt(iw,ih),-1,720)':'if(gt(iw,ih),720,-1)'` 

- 前半句表示缩放后的视频宽度，如果原视频的宽度大于高度，缩放后的视频宽度为`-1`，否则是`720`。 
- 后半句表示缩放后的视频高度，如果原视频的宽度大于高度，缩放后的视频高度为`720`，否则是`-1`。 


### 裁剪和填补

1、裁剪一个矩形区域：
`ffmpeg -i {input} -vf crop={width}:{height}:{x}:{y} {output}`


2、填补到一个更大的矩形区域：
`ffmpeg -i {input} -vf pad={width}:{height}:{x}:{y}:{color} {output}`

参数中可以使用`iw`、`ih`指代原视频宽高。

### 视频翻转和旋转

1、水平翻转：
`ffmpeg -i {input} -vf hflip {output}`


2、垂直翻转：
`ffmpeg -i {input} -vf vflip {output}`

3、顺时针旋转90°：
`ffmpeg -i {input} -vf transpose=1 {output}`

4、逆时针旋转90°：
`ffmpeg -i {input} -vf transpose=2 {output}`

### 视频盖图

1、整视频盖图：
`ffmpeg -i {video} -i {image} -filter_complex overlay={x}:{y} {output}`

2、从某个时间开始：
`ffmpeg -i {video} -itsoffset {start} -i {image} -filter_complex overlay={x}:{y} {output}`



> [!quote]
> 1. [ffmpeg的安装和视频帧抽取(ubuntu/windows系统通用）\_ffmpeg 视频抽帧-CSDN博客](https://blog.csdn.net/weixin_39931683/article/details/110476502)
> 2. [【FFmpeg】视频压缩：scale](https://zhuanlan.zhihu.com/p/641252482)
> 3. [ffmpeg音视频处理常用操作\_ffmpeg transpose-CSDN博客](https://blog.csdn.net/lweiyue/article/details/122098833)
> 4. [【FFmpeg】FFmpeg常用命令汇总+文档汇总](https://zhuanlan.zhihu.com/p/562692508)
> 5. [ffmpeg 实用命令 -- 缩放与裁切\_ffmpeg 视频画面大小裁剪-CSDN博客](https://blog.csdn.net/weixin_66886617/article/details/135761205)