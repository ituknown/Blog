# 前言

视频容器文件（例如 MP4 视频文件）通常会包含多个元素，它们可以是视频、音频、字幕或其他元数据元素。这些元素通常称为流或轨道。

例如，一个视频可能有 1 个视频流和 2 个音频流，以及多个字幕流，每个音频流包含不同语言的音频。比如一部电影可能有英文音频又有中文音频。当使用中文音频时同步显示中文字幕，使用英文音频是同步显示英文字幕...

如果一个文件中存在多个视频或音频流，则视频播放器只播放一个视频流和一个音频流，其他流需要在偏好设置中手动选择。

本文主要介绍 FFMPEG 做流映射时所使用的 `-map` 参数。

# 流命名约定

当使用 FFMPEG 编辑视频或应用转换时，我们必须引用所提供的输入文件中的特定流。这个在 FFMPEG 有自己的命名约定，如果想玩转 FFMPEG，这个我们必须知道。

FFMPEG 中引用不同流的命名约定遵循以下模式：

$$
i:[a|v|s]:n
$$

**说明：**

- `i`：是参数中指定的输入文件（即 `-i` 参数指定的输入文件），根据 `-i` 参数的顺序，从 0 开始计算。

- `a 或 v 或 s`：指定输入文件所指的流类型。a 表示音频（audio），v 表示视频（video），s 表示字幕（subtitle）。

- `n`：是指的具体流，从0开始。


听起来感觉很复杂，其实不然，举几个例子就理解了：

1. `0:a`：指引用第一个文件的所有音频流（audio）。

2. `0:v`：指引用第一个文件的所有视频流（video）。

3. `0:a:1`：指引用第一个文件的第二个音频流。

4. `0:v:0`：指引用第一个文件的第一个视频流。

5. `1:v:0`：指引用第二个文件的第一个音频流。

现在是不是有点感觉了？


# FFMPEG 中的 map 流映射参数

现在我们知道如何引用特定的流，我们就可以从输入文件中选择目标流。

在 FFMPEG 中，引用流是使用 `-map` 参数实现。让我们看几个例子：

## 示例一

```bash
ffmpeg \
-i english.mp4 \
-i french.mp3 \
-map 0:v:0 \
-map 1:a:0 \
french.mp4:
```

这条命令的含义是：从 english.mp4 (`0:v:0`) 中选择第一个视频流，从 french.mp3 (`1:a:0`) 中选择第一个音频流，并将它们保存到 french.mp4 中。

这里要想明白是如何确定文件，输入文件的顺序就是 `-i` 参数指定的顺序。因为 english.mp4 文件先指定，所以这个就是第一个文件，对应的序号为 0。french.mp3 是第二个指定，所以序号为 1。

## 示例二

```bash
ffmpeg \
-i english.mp4 \
-i french.mp3 \
-map 0 \
-map 1:a:0 \
english-french.mp4
```

这条命令的含义是：从 english.mp4 文件 (`-map 0`) 中选择所有流，从 french.mp4 文件 (`1:a:0`) 选择第一个音频流，之后创建包含两个音频流的 english-french.mp4 文件。

一定要记清楚这些示例，因为在日常工作中经常会使用这些操作。