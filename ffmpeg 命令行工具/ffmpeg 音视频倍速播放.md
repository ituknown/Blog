
# 前言

视频流的播放速度可以通过改变每帧的显示时间戳（PTS）来改变。有两种实现方法：

1. 通过将视频导出为 **原始比特流** 格式来实现擦除时间戳，并在创建新时间戳时将其复用到到容器中。

2. 使用 setpts 视频过滤器，但是需要重新编码。

# 使用“原始比特流”实现视频倍速播放

使用“原始比特流”实现音视频倍速播放是无损的，也是推荐的方式。这种方式除了更改时间戳外，还会原样复制视频流。不过前提是：将视频复制到原始比特流格式。

|**Note**|
|:-------|
|使用原始比特流实现倍速播放是推荐的实现方式，但是我在微信中发现视频却被“自动还原”了。比如一个1分钟的视频，当实现二倍速播放时原视频只会有30秒，但是在微信中依然显示1分钟，也不知道什么原因。如果你也遇到这种问题了就不能使用原始比特流实现倍速，而是使用 [使用 setpts 视频过滤器实现倍速播放](#使用-setpts-视频过滤器实现倍速播放)|

## 确定视频编码及帧率

从视频中导出到原始比特流格式我们需要知道该视频的编码格式（如是 H.264 还是 H.265）。如果你不知道目标视频文件是什么编码格式可以借助 `ffprobe` 工具查看。

比如我有一个 mp4 格式的视频文件 input.mp4，我就可以使用 `ffprobe` 查看该视频的头信息：

```bash
$ ffprobe input.mp4

...
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'input.mp4':
  Metadata:
    major_brand     : isom
    minor_version   : 512
    compatible_brands: isomiso2mp41
    encoder         : Lavf59.27.100
    description     : Packed by Bilibili XCoder v2.0.2
  Duration: 00:01:42.74, start: 0.000000, bitrate: 1735 kb/s
  Stream #0:0[0x1](und): Video: hevc (Main) (hev1 / 0x31766568), yuv420p(tv, bt709), 3840x2160 [SAR 1:1 DAR 16:9], 1529 kb/s, 52.47 fps, 52 tbr, 16k tbn (default)
    Metadata:
      handler_name    : VideoHandler
      vendor_id       : [0][0][0][0]
  Stream #0:1[0x2](und): Audio: aac (LC) (mp4a / 0x6134706D), 48000 Hz, stereo, fltp, 195 kb/s (default)
    Metadata:
      handler_name    : SoundHandler
      vendor_id       : [0][0][0][0]
  Stream #0:2[0x3]: Subtitle: mov_text (tx3g / 0x67337874), 0 kb/s (default)
    Metadata:
      handler_name    : SubtitleHandler
```

注意看第一行流信息：

```log
Stream #0:0[0x1](und): Video: hevc (Main) (hev1 / 0x31766568), yuv420p(tv, bt709), 3840x2160 [SAR 1:1 DAR 16:9], 1529 kb/s, 52.47 fps, 52 tbr, 16k tbn (default)
```

这行信息表示，第一个流是视频流，视频编码格式是 hevc（封装格式为 hev1），这个就是我们俗称的 H.265 格式。每一帧的数据表示为 yuv420p，分辨率为3840x2160，该轨道流的比特率为 1529 kb/s，帧率（fps）为每秒钟 52.47 帧。

这样，我们就确定了视频编码格式（H.265）和视频帧率（52.47），同时我的视频时长为 1:42.74。

## 从视频中导出原始比特流

- 导出 H.264 视频原始比特流：

```bash
ffmpeg -i input.mp4 -map 0:v -c:v copy -bsf:v h264_mp4toannexb raw.h264
```

- 导出 H.265 视频原始比特流：

```bash
ffmpeg -i input.mp4 -map 0:v -c:v copy -bsf:v hevc_mp4toannexb raw.h265
```

其中，`-i` 参数后面的 input.mp4 就是你的具体视频文件，最后产生对应的 raw.h264 或 raw.h265 文件，这个文件名是可以自定义的。

## 实现倍速转换

得到原始比特流格式文件之后，我们就可以来实现倍速转换了。倍速转换的原理就是修改 “帧率”。在前面我已经得到的我的视频帧率为 52.47，如果想要实现倍速那我只需要将每秒的帧率按照倍数放大即可，比如我想要实现倍速播放，那么就要将帧率 放大二倍：`52.47 x 2 = 104.94`。

也就是说，实现二倍速播放的帧率为 104.94。因此，我只需要使用 -r 参数指定新的速率即可！

```bash
ffmpeg -fflags +genpts -r 104.94 -i raw.h265 -c:v copy output.mp4
```

但是请注意，这种实现方式只适用于没有音频流的视频文件，即视频没有声音。如果你的视频自带音频流的话，在实现倍速转换时还需要加上音频参数对音频流也做二倍速处理。

修改音频流倍速的主要参数是 `atempo`，所以对具有音频流的视频实现倍速播放应该使用如下命令：

```bash
ffmpeg -fflags +genpts -r 104.94 -i raw.h265 -i input.mp4 -map 0:v -c:v copy -map 1:a -af atempo=2 -movflags faststart output.mp4
```

# 使用 setpts 视频过滤器实现倍速播放

使用视频过滤器来实现倍速播放的原理是过改变每个视频帧的显示时间戳（PTS）来实现。

## 视频流实现倍速播放

基本示例如下：

```
ffmpeg -i input.mkv -an -c:a copy -filter:v "setpts=1*PTS" output.mkv
```

`-filter:v` 参数表示对视频流执行过滤（`v` 表示 video），后面跟的就是具体的过滤器参数，比如 "setpts=1*PTS" 就是正常是视频倍速，如果有多个过滤参数就使用英文 `,` 分隔即可。

如果想要实现快倍速播放就将 PTS 按照倍速缩小即可，比如 "setpts=PTS/2" 或 "setpts=PTS\*0.5" 表示的就是快倍播放，"setpts=PTS/4" 或 "setpts=PTS\*0.25" 表示的就是快4倍播放。

相反，"setpts=PTS\*2" 表示的就是慢倍播放，"setpts=PTS\*4" 表示的就是慢4倍播放。

注意：该命令只针对没有音频流的视频文件，如果你的视频文件存在音频流不要使用该命令实现倍速转换。

## （提取）音频流并实现倍速播放

基本示例如下：

```bash
ffmpeg -i input.mkv -vn -c:a copy -filter:a "atempo=1.0" output.acc
```

`-filter:a` 参数表示对视频中的音频流执行过滤（`a` 表示 audio），后面跟的就是具体的过滤器参数，比如 “atempo=1.0” 就是正常是音频倍速，如果有多个过滤参数就使用英文 `,` 分隔即可。

如果想要对音频实现快倍速播放就将 atempo 按照倍数扩大即可，比如 "atempo=2.0" 表示的就是快倍播放，"atempo=0.5" 表示的就是慢倍播放。

特别强调的一点是，atempo 取值参数为 [0.5,2]。也就是说只能实现快倍或慢倍，无法实现四倍速。如果想要实现四倍速就使用英文 `,` 分隔指定多次即可。比如快四倍：

```bash
ffmpeg -i input.mkv -vn -c:a copy -filter:a "atempo=2.0,atempo=2.0" output.acc
```

|**Note**|
|:-------|
|上面的示例是从视频文件中提取音频流，然后对音频流实现倍速播放。如果想针对音频文件实现倍速，只需要将上面的视频文件替换为音频文件即可，命令不变。|

## 音视频实现倍速播放

有了上面两个示例的只是之后再对音视频实现倍速就简单多了，因为对音视频的倍速操作其实就是前面两个命令的组合：

```bash
ffmpeg -i input.mkv -filter_complex "[0:v]setpts=0.5*PTS[v];[0:a]atempo=2.0[a]" -map "[v]" -map "[a]" output.mkv
```

--

# 资源链接

https://trac.ffmpeg.org/wiki/How%20to%20speed%20up%20/%20slow%20down%20a%20video