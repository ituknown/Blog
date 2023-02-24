# 字幕的类型

## 外挂字幕

所谓外挂字幕就是单独的字幕文件，字幕文件格式类型一般有srt、vtt、ass等。播放视频时，把外挂字幕和视频放在同一目录下，并在播放器中选择字幕文件，即可以在视频中看到字幕。

比如在当前目录下有一个视频 Mieruko-chan.mkv，另外同级目录还有两个 ass 字幕文件：

```bash
$ ls
Mieruko-chan.chs.ass
Mieruko-chan.cht.ass
Mieruko-chan.mkv
```

当使用视频播放器打开 Mieruko-chan.mkv 文件时，通常会自动加载同级目录下的两个字幕文件，也就有一种 “视频带字幕” 的错觉，这其实是视频播放器的功能（如下图）：

![select-outside-subtitles-cA6tCrw9ODW03BS5K8.png](http://blog-media.knowledge.ituknown.cn/FFmpeg/subtitles/select-outside-subtitles-cA6tCrw9ODW03BS5K8.png)

不过有些视频播放器不会自动加载同级目录下的字幕文件，因此就需要手动选择（点击加载字幕按钮选择本地字幕文件）。

## 软字幕

软字幕也叫内挂字幕、封装字幕、内封字幕，字幕流等，就是把前面的外挂字幕的字幕文件嵌入到视频中作为流的一部分，这样在播放视频时同样的会自动显示字幕。

但是如果一个视频有多个字幕流（比如上图中有两个外挂字幕），那么在播放时还需要手动选择对应的字幕流。

一个完整的视频一般包括视频流、音频流和字幕流。字幕流就是将软字幕嵌入到视频中作为流使用，后期可以使用相关工具提取字幕流或者编辑以及删除等操作（通常而言，推荐使用软字幕）。

我们可以使用 `ffprobe` 查看一个视频是否存在软字幕流，比如下面的输出示例中就说明该视频有字幕流：

```bash
$ ffprobe demo.mkv

...

Stream #0:0: Video: hevc (Main 10), yuv420p10le(tv,bt709/unknown/unknown), 1920x1080, SAR 1:1 DAR 16:9,23.98 fps, 23.98 tbr, 1k tbn (default)
  Metadata:
    BPS-eng         : 6012569
    DURATION-eng    : 00:23:41.087000000
    NUMBER_OF_FRAMES-eng: 34072
    NUMBER_OF_BYTES-eng: 1068048039
    _STATISTICS_WRITING_APP-eng: mkvmerge v48.0.0('Fortress Around Your Heart') 64-bit
    _STATISTICS_WRITING_DATE_UTC-eng: 2022-05-0421:36:50
    _STATISTICS_TAGS-eng: BPS DURATIONNUMBER_OF_FRAMES NUMBER_OF_BYTES
    DURATION        : 00:23:41.086000000
Stream #0:1(jpn): Audio: flac, 48000 Hz, stereo, s32(24 bit) (default)
  Metadata:
    BPS-eng         : 1417173
    DURATION-eng    : 00:23:41.090000000
    NUMBER_OF_FRAMES-eng: 16654
    NUMBER_OF_BYTES-eng: 251741460
    _STATISTICS_WRITING_APP-eng: mkvmerge v48.0.0('Fortress Around Your Heart') 64-bit
    _STATISTICS_WRITING_DATE_UTC-eng: 2022-05-0421:36:50
    _STATISTICS_TAGS-eng: BPS DURATIONNUMBER_OF_FRAMES NUMBER_OF_BYTES
    DURATION        : 00:23:41.090000000
Stream #0:2: Subtitle: ass  #### 这个就是字幕流
  Metadata:
    DURATION        : 00:23:39.930000000
```

| **Note**                                                     |
| :----------------------------------------------------------- |
| 不管是外挂字幕还是软字幕，字幕要想正常显示，播放器必须要支持字幕的渲染。 |

## 硬字幕

如果你有一个视频，不管是在手机上看还是电脑网页上看，不管你是在什么播放器看，点开直接看，视频里就有字幕，而且在任何设备和任何播放软件上看到的字幕都是完全一样的，没有任何跑偏，那这个视频的字幕就是硬字幕。

这种字幕的文字已经不再是文字了，而是将字幕嵌入到视频帧里面，它就像视频水印一样作为视频帧的一分部分了，不管在任何平台字幕看起来都是一样的，而且也不再要求播放器单独对字母进行渲染。比如追美剧看的一般是内嵌中英双语字幕，毕竟简单好用，可以拿来直接看，对小白来说太友好了。

硬字幕缺点也很明显：后期不可以编辑、不可去除字幕。另外，也无法使用 `ffprobe` 查询字幕流信息。

# 字幕格式以及转换

## 常见字幕格式

不同的字幕文件有其对应的格式（针对外挂字幕和软字幕），常见的字幕格式有：

**SRT（SubRip Text，标准外挂字幕格式）**

其制作规范简单，一句时间一句字幕，只包含文字和时间码，没有样式，显示效果由播放器决定，不同的播放器显示出的效果可能差别很大（下面是 srt 字幕文件内容示例）。

![subtitles-srt-example-xZ0R2hL64qWDkH81gu1.png](http://blog-media.knowledge.ituknown.cn/FFmpeg/subtitles/subtitles-srt-example-xZ0R2hL64qWDkH81gu1.png)

**ASS（Advanced Sub Station，高级外挂字幕格式）**

用以实现比传统字幕诸如srt等格式更为复杂的功能，ass 字幕文件通常包含五部分：`[Script Info]`、`[v4+ Styles]`、`[Events]`、`[Fonts]` 以及 `[Graphics]`。支持样式、字体、字幕定位、淡入淡出、简单的特效。如果不缺字体，不同的播放器显示效果基本一致（下面是 srt 字幕文件内容示例）。

![subtitles-ass-example-wX57qkv4M136SDPOFB.png](http://blog-media.knowledge.ituknown.cn/FFmpeg/subtitles/subtitles-ass-example-wX57qkv4M136SDPOFB.png)

**WebVTT （Web Video Text Tracks）字幕格式**

WebVTT是通过HTML5中的 元素来标记额外的文本轨道资源，是一个 `.vtt` 结尾的纯文本文件。不过某些播放器无法正常加载，需要将 vtt 转为 srt 格式（下面是 vtt 字幕文件内容示例）。

![subtitles-vtt-example-gK2G401Y97Hsyw5LRW.png](http://blog-media.knowledge.ituknown.cn/FFmpeg/subtitles/subtitles-vtt-example-gK2G401Y97Hsyw5LRW.png)

**Sbv 字幕格式**

Youtube的字幕格式，它可以通过youtube自动生成字幕文件，文件后缀 `.sbv`。

## 字幕格式转换

字幕转换比较简单，可以直接使用 `ffmpeg` 命令实现 ass、srt、vtt 等字幕格式的相互转换。示例：

```bash
# ass 转 srt
$ ffmpeg -i Mieruko-chan.chs.ass Mieruko-chan.chs.srt

# ass 转 vtt
$ ffmpeg -i Mieruko-chan.chs.ass Mieruko-chan.chs.vtt

# srt 转 ass
$ ffmpeg -i Mieruko-chan.chs.srt Mieruko-chan.chs.ass

# 其他同理...
```

# FFmpeg 视频字幕操作

## 视频添加软字幕

**mkv 添加 srt 字幕：**

```bash
# mkv 添加软字幕
$ ffmpeg -i input.mkv -i subtitles.srt -c copy output.mkv
# 或
$ ffmpeg -i input.mkv -i subtitles.srt -c:v copy -c:a copy -c:s copy output.mkv
```

**mkv 添加 ass 字幕：**

```bash
# mkv 添加软字幕
$ ffmpeg -i input.mkv -i subtitles.ass -c copy output.mkv
# 或
$ ffmpeg -i input.mkv -i subtitles.ass -c:v copy -c:a copy -c:s copy output.mkv
```

**mp4 添加 srt 字幕**

```bash
# mp4 添加软字幕
$ ffmpeg -i input.mp4 -i subtitles.srt -c copy output.mkv
# 或
$ ffmpeg -i input.mp4 -i subtitles.srt -c:v copy -c:a copy -c:s copy output.mkv
```

添加软字幕的原理和流程就跟给视频添加音频一样，这个过程不需要重新编解码，所以速度非常快。

| **Note**                                                     |
| :----------------------------------------------------------- |
| 并非所有的容器都支持字幕流（软字幕），软字幕只有部分容器格式（如mkv）才支持，MP4/MOV等不支持。而且也只有部分播放器支持软字幕或者外挂字幕（如VLC、IINA播放器） |

**扩展**

上面第二条示例命令中都使用了`-c:` 参数，这个参数的意思其实是对指定流指定具体编解码器：

- `-c:v`：选择视频流编解码器
- `-c:a`：选择音频流编解码器
- `-c:s`：选择字幕流编解码器

`copy` 表示选择对应流默认编解码器。以 `-c:s copy` 为例，如果字幕文件是 ass，那么对应的就是 ass 编解码器：

```bash
# mkv 添加软字幕
$ ffmpeg -i input.mkv -i subtitles.ass -c copy output.mkv
# 等同于
$ ffmpeg -i input.mkv -i subtitles.ass -c:v copy -c:a copy -c:s copy output.mkv
# 等同于
$ ffmpeg -i input.mkv -i subtitles.ass -c:v copy -c:a copy -c:s ass output.mkv
```

之所以这么写的原因是为了说明这个编解码器是可以自行指定。以字幕流为例，常用的字幕编解码器有 srt、ass 以及mov_text，具体可以使用 `ffmpeg -codecs` 命令查看。比如这里我的外挂字幕是 ass，但是我可以选择 srt 字幕编解码器：

```bash
$ ffmpeg -i input.mkv -i subtitles.ass -c:v copy -c:a copy -c:s srt output.mkv
```

需要强调的一点是，不同的视频格式对字幕编码器的要求也不同，必须使用适合的字幕编解码器才行（如下表格）：

| **视频格式** | **可用字幕编码器**  |
| :----------- | :------------------ |
| MKV          | copy、ass、srt、ssa |
| MOV          | copy、mov_text      |
| MP4          | copy、mov_text      |

许多飞利浦蓝光播放器、三星智能电视和其他独立播放器只能读取“MKV”文件中的“SRT”字幕流，所以一定根据需要使用。

## 视频添加软字幕 - 多语言

前面示例中只是添加一个字幕（即单语言），其实我们也可以对一个视频添加多个语言的字幕，比如添加简体中文、英文以及繁体中文。缺点是，某些播放器可能不会自动显示字幕，需要手动选择指定语言才会显示。

比如我有一个视频文件 Mieruko-chan.mkv，并且有两个 ass 字幕文件：

```bash
$ ls
Mieruko-chan.chs.ass  # 中文简体
Mieruko-chan.cht.ass  # 中文繁体
Mieruko-chan.mkv      # mkv视频文件
```

如果想将两个 ass 字幕同时添加到 mkv 视频中，可以使用下面的命令：

```bash
$ ffmpeg -i Mieruko-chan.mkv -i Mieruko-chan.chs.ass -i Mieruko-chan.cht.ass \
-c:v copy -c:a copy -c:s copy \
-map 0:v -map 0:a -map 1 -map 2 \
-metadata:s:s:0 language=chs \
-metadata:s:s:1 language=cht \
output.mkv
```

前三行前面都已经介绍过，没什么好说的。主要是看如下几行：

```bash
-map 0:v -map 0:a -map 1 -map 2 \
-metadata:s:s:0 language=chs \
-metadata:s:s:1 language=cht \
```

`-map` 是轨道参数，一个 `map` 就是一个轨道。如果只有一个字幕，就不需要这个参数（前面就没使用）。在这个例子中：

```
-map 0:v 表示第一个轨道是第一个文件输入的视频
-map 0:a 表示第二个轨道是第一个文件输入的音频
-map 1   表示第三个轨道使用第二个文件建立
-map 2   表示第四个轨道使用第三个文件建立


-metadata:s:s: 是字幕参数, 要与输入文件的顺序保持一致:

-metadata:s:s:0 language=chs 第一条字幕的语言设置为中文简体
-metadata:s:s:1 language=cht 第二条字幕的语言设置为中文繁体
```

| **Note**                                                     |
| :----------------------------------------------------------- |
| 语言不能写错或自定义，只能设置成固定的缩写。比如 chs 表示中文简体、cht 表示中文繁体。 |

之后使用 `ffprobe` 看下最终输出的视频的流信息：

```bash
$ ffprobe output.mkv

...

Stream #0:1(jpn): Audio: flac, 48000 Hz, stereo, s32 (24 bit) (default)
  Metadata:
    BPS-eng         : 1417173
    DURATION-eng    : 00:23:41.090000000
    NUMBER_OF_FRAMES-eng: 16654
    NUMBER_OF_BYTES-eng: 251741460
    _STATISTICS_WRITING_APP-eng: mkvmerge v48.0.0 ('Fortress Around Your Heart') 64-bit
    _STATISTICS_WRITING_DATE_UTC-eng: 2022-05-04 21:36:50
    _STATISTICS_TAGS-eng: BPS DURATION NUMBER_OF_FRAMES NUMBER_OF_BYTES
    DURATION        : 00:23:41.090000000
Stream #0:2(jpn): Audio: aac (LC), 48000 Hz, stereo, fltp
  Metadata:
    BPS-eng         : 190019
    DURATION-eng    : 00:23:41.098000000
    NUMBER_OF_FRAMES-eng: 66614
    NUMBER_OF_BYTES-eng: 33754594
    _STATISTICS_WRITING_APP-eng: mkvmerge v48.0.0 ('Fortress Around Your Heart') 64-bit
    _STATISTICS_WRITING_DATE_UTC-eng: 2022-05-04 21:36:50
    _STATISTICS_TAGS-eng: BPS DURATION NUMBER_OF_FRAMES NUMBER_OF_BYTES
    DURATION        : 00:23:41.098000000
Stream #0:3(chs): Subtitle: ass (default) # 第三个轨道是 chs 字幕, 编码格式为 ass
  Metadata:
    DURATION        : 00:23:39.930000000
Stream #0:4(cht): Subtitle: ass           # 第四个轨道是 cht 字幕, 编码格式为 ass
  Metadata:
    DURATION        : 00:23:39.930000000
```

## 视频添加硬字幕

<u>实际上并不推荐使用硬字幕，但这里也需要说下</u>。要在视频流上面加上字幕，需要使用一个叫做 `subtitles` 的滤镜。要使用这个滤镜，在命令中写上 `-vf subtitles=字幕文件名` 。另外，如果文件名包含空格或其他特殊字符，需要使用英文引号包起来： `-vf subtitles="字幕 文件名"`。

ffmpeg 最终都会将字幕格式先转换成 ass 字幕流再将字幕嵌入到视频帧中，这个过程需要重新编解码操作视频流，所以速度非常非常的慢。

**将外挂字幕文件嵌入到  `output.mkv`： **

使用 `subtitles` 滤镜为视频添加字幕（将字幕合成到视频流中，输出文件中不含字幕流）：

```bash
$ ffmpeg -i input.mkv -vf subtitles=subtitles.srt output.mkv
```

**将另一个视频的软字幕流嵌入到 `output.mp4`：**

视频流为 `input.mkv`，字幕流从 `other.mkv` 中提取，并嵌入到 `output.mp4` 文件：

```bash
$ ffmpeg -i input.mkv -vf subtitles=other.mkv output.mp4
```

注意，软字幕流也可以来自 `input.mkv` 视频文件本身：

```bash
$ ffmpeg -i input.mkv -vf subtitles=input.mkv output.mp4
```

**将视频的指定字幕流嵌入到 `output.mp4`： **

比如视频 `input.mkv` 有两个软字幕流，将第二个字幕流嵌入到 `output.mp4` 文件：

```none
ffmpeg -i input.mkv -vf subtitles=input.mkv:si=1 output.mp4
```

关于 `subtitles` 滤镜的详细用法见官网文档：[http://ffmpeg.org/ffmpeg-all.html#subtitles-1](https://p3terx.com/go/aHR0cDovL2ZmbXBlZy5vcmcvZmZtcGVnLWFsbC5odG1sI3N1YnRpdGxlcy0x)

另外，对于 ASS 字幕需要使用 `ass` 滤镜，用法和 `subtitles` 滤镜几乎一样，但它只用于 ASS (Ad­vanced Sub­sta­tion Al­pha) 字幕文件：

```bash
$ ffmpeg -i input.mkv -vf ass=subtitles.ass output.mp4
```

关于 `ass` 滤镜的说明见官网文档：[http://ffmpeg.org/ffmpeg-all.html#ass](https://p3terx.com/go/aHR0cDovL2ZmbXBlZy5vcmcvZmZtcGVnLWFsbC5odG1sI2Fzcw)

| **Note**                                                     |
| :----------------------------------------------------------- |
| 在实际使用中发现 `ass` 和 `subtitles` 最终效果并无区别，但 `ass` 只能使用 ASS 字幕文件，不可以直接使用容器中的字幕流，所以直接使用 `subtitles` 即可，省去了手动提取和转换的过程。 |

## 提取视频字幕

提取视频字幕只针对软字幕，硬字幕无法提取。命令如下：

```bash
$ ffmpeg -i input.mkv output.srt
```

| **Note**                                                     |
| :----------------------------------------------------------- |
| 例子中生成的是 srt 格式的，可以任意生成所需的格式，改一下扩展名即可（如 ass 格式）。 |

# 参考链接

[视频中的硬字幕、软字幕和外挂字幕，怎么分别？](https://zhuanlan.zhihu.com/p/138387967)

[如何使用 FFmpeg 进行视频转码:字幕](https://wiki.fiveyellowmice.com/wiki/如何使用_FFmpeg_进行视频转码:字幕)

[使用FFmpeg将字幕文件集成到视频文件](http://www.yaosansi.com/post/ffmpeg-burn-subtitles-into-video/)

[fmpeg-给视频添加字幕(二十四)](https://blog.csdn.net/qq_21743659/article/details/109305411?share_token=CC419D26-146D-49DA-ABB9-D95E82B2FC34&tt_from=copy_link&utm_source=copy_link&utm_medium=toutiao_ios&utm_campaign=client_share)

[使用 FFmpeg 为视频添加字幕](https://p3terx.com/archives/add-captions-to-your-videos-with-ffmpeg.html)

https://crifan.github.io/media_process_ffmpeg/website/subtitle/embed/

https://gist.github.com/juliendkim/aa6315c234395d88799b7037bde5f897

https://crifan.github.io/media_process_ffmpeg/website/subtitle/

[wikibooks：FFMPEG An Intermediate Guide/subtitle options](https://en.wikibooks.org/wiki/FFMPEG_An_Intermediate_Guide/subtitle_options)
