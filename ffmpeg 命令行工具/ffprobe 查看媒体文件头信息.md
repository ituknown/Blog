# 前言

`ffmpeg` 命令行工具都知道，但是该工具包下面其实还有其他几个比较实用的命令， `ffprobe` 就是其一！

该工具主要用户查看媒体文件的头或流信息，比如编码格式、时长、帧率等等。使用起来也特别简单，只需要在命令后面加上媒体文件名即可：

```bash
ffprobe [文件名]
```

# 查看mp3格式文件信息

下面以我本地的 風の住む街.mp3 示例 mp3 文件为例说明：

```bash
$ ffprobe 風の住む街.mp3

...
Input #0, mp3, from 'example.mp3':
  Metadata:
    title           : 風の住む街
    album           : 西尔德斯 新世纪·亚洲
    artist          : 磯村由紀子
    genre           : Blues
  Duration: 00:04:45.99, start: 0.025056, bitrate: 327 kb/s
  Stream #0:0: Audio: mp3, 44100 Hz, stereo, fltp, 320 kb/s
    Metadata:
      encoder         : LAME3.99r
    Side data:
      replaygain: track gain - -4.600000, track peak - unknown, album gain - unknown, album peak - unknown,
  Stream #0:1: Video: mjpeg, none(bt470bg/unknown/unknown), 90k tbr, 90k tbn (attached pic)
    Metadata:
      comment         : Other
```

对于 mp3 文件我们主要关注两行信息：

**第一行：**

```log
Duration: 00:04:45.99, start: 0.025056, bitrate: 327 kb/s
```

该信息主要包含 mp3 媒体文件的时长信息、开始播放时间信息以及文件的比特率。以该媒体文件为例，该mp3的总时长是4分45秒99毫秒，开始播放时间是0.025056，整个文件的比特率是 327 kb/s。

**第二行：**

```log
Stream #0:0: Audio: mp3, 44100 Hz, stereo, fltp, 320 kb/s
```

这行信息表示，第一个流是音频流，编码格式是MP3格式，采样率是44.1KHz，声道是立体声，采样表示格式是 fltp，这路流的比特率 320 kb/s。

# 查看mp4格式文件信息

```bash
$ ffprobe example.mp4

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

对于视频流信息有几行信息特别重要（根据你的视频情况而定）：

**第一行信息：**

```log
Duration: 00:01:42.74, start: 0.000000, bitrate: 1735 kb/s
```

这行信息表示用于展示视频基本时长信息，比如该视频文件的总时长为 1分42秒74毫秒，开始播放时间是0，整个文件的比特率是 1735 kb/s。

**第二行信息：**

```log
Stream #0:0[0x1](und): Video: hevc (Main) (hev1 / 0x31766568), yuv420p(tv, bt709), 3840x2160 [SAR 1:1 DAR 16:9], 1529 kb/s, 52.47 fps, 52 tbr, 16k tbn (default)
```

这行信息表示，第一个流是视频流，视频编码格式是 hevc（封装格式为 hev1），这个就是我们俗称的 H.265格式。每一帧的数据表示为 yuv420p，分辨率为3840x2160，这路流的比特率为 1529 kb/s，帧率（fps）为每秒钟 52.47 帧。

|**Note**|
|:-------|
|视频编码格式是在某些时候特别重要，比如你想使用 ffmpeg 按原始比特流法 实现视频倍速播放那么知道这个视频格式就显得尤为重要。|

**第三行信息：**

```log
Stream #0:1[0x2](und): Audio: aac (LC) (mp4a / 0x6134706D), 48000 Hz, stereo, fltp, 195 kb/s (default)
```

这行信息表示第二个流是音频流，编码方式为 ACC（封装格式为 mp4a），并且采用的Profile是LC规格，采样率是 48K Hz，声道是立体声，这路流的比特率 195 kb/s。

| **Note**                                                               |
|:-----------------------------------------------------------------------|
| 音频流不是每个视频都会有，假如你的视频没有声音那么就不会有对应的音频流！ |

**第四行信息：**

```log
Stream #0:2[0x3]: Subtitle: mov_text (tx3g / 0x67337874), 0 kb/s (default)
```

这行信息表示第三个流是字幕流，编码格式是 mov_text（封装格式为 tx3g）。

| **Note**                                                               |
|:-----------------------------------------------------------------------|
| 字幕流不是每个视频都会有，假如你的视频没有字幕那么就不会有对应的音频流！ |

# 扩展

## hevc 和 h.265的区别

hevc中文名称是高校视频编码，也被称为H.265，因此hevc就是H.265，两者是同一个事物，所以hevc和H.265之间没有区别，可以通过蓝光最佳视频压缩方法实现两倍的压缩。

H.265是新的编码协议，即 H.264的升级版。H.265标准保留H.264原来的某些技术，同时对一些相关的技术加以改进。新技术使用先进的技术用以改善码流、编码质量、延时和算法复杂度之间的关系，达到最优化设置。

H.265主要也包含，帧内预测（intra prediction）、帧间预测（inter prediction）、转换（transform）、量化（quantization）、去区块滤波器（deblocking filter）、熵编码（entropy coding）等模块。

## fps是什么?

玩过游戏的玩家都会在屏幕上方看到一行带有 fps 的小字，一般后面都会加上时刻在变动的数值，那么fps是什么意思呢？

fps 指的是画面每秒传输帧数，通俗来讲就是指动画或视频的画面数，因此经常能在FPS后面看到一些数值，数值越高，画面就越流畅，相反画面就会卡顿。

另外，我们也可以将 fps 理解为刷新率，通常好一点的显示器刷新率都在60Hz（即60帧/秒）以上。当刷新率太低时，肉眼就能感觉到屏幕的闪烁，不连贯，对图像显示效果和视觉感观产生不好的影响。对于电影来说，帧率通常是 24fps，即以每秒24张画面的速度播放，也就是一秒钟内在屏幕上连续投射出25张静止画面。

fps 其实是三个英文单词的缩写：其中的 f 就是英文单词 Frame（画面、帧），p 就是 Per（每），s 就是 Second（秒）。用中文表达就是多少帧每秒，或每秒多少帧。电影是24fps，通常简称为24帧。