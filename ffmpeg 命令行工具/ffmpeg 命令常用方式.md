# 前言

官网地址：http://ffmpeg.org

GitHub 地址：https://github.com/FFmpeg/FFmpeg

**ffmpeg 安装：**

```bash
# Mac 下使用 HomeBrew 安装
$ brew install -v ffmpeg

# CentOS/RHEL 系统可以使用 YUM 包管理工具安装
$ yum install -y ffmpeg

# Debain/Ubuntu 系列使用 APT 包管理工具安装
$ apt-get install -y ffmpeg
```

|**Note**|
|:-------|
|Windows 系统的话需要去官网下载对应的安装版本，或者直接到二进制下载页面下载：[https://github.com/BtbN/FFmpeg-Builds/releases](https://github.com/BtbN/FFmpeg-Builds/releases)|


安装完成之后在可以在终端输入 `ffmpeg -version` 命令验证是否安装成功，如果输出类似如下的信息即表示安装成功了：

```bash
$ ffmpeg -version
ffmpeg version 4.3.3-0+deb11u1 Copyright (c) 2000-2021 the FFmpeg developers
built with gcc 10 (Debian 10.2.1-6)
configuration: --prefix=/usr --extra-version=0+deb11u1 --toolchain=hardened --libdir=/usr/lib/x86_64-linux-gnu --incdir=/usr/include/x86_64-linux-gnu --arch=amd64 --enable-gpl --disable-stripping --enable-avresample --disable-filter=resample --enable-gnutls --enable-ladspa --enable-libaom --enable-libass --enable-libbluray --enable-libbs2b --enable-libcaca --enable-libcdio --enable-libcodec2 --enable-libdav1d --enable-libflite --enable-libfontconfig --enable-libfreetype --enable-libfribidi --enable-libgme --enable-libgsm --enable-libjack --enable-libmp3lame --enable-libmysofa --enable-libopenjpeg --enable-libopenmpt --enable-libopus --enable-libpulse --enable-librabbitmq --enable-librsvg --enable-librubberband --enable-libshine --enable-libsnappy --enable-libsoxr --enable-libspeex --enable-libsrt --enable-libssh --enable-libtheora --enable-libtwolame --enable-libvidstab --enable-libvorbis --enable-libvpx --enable-libwavpack --enable-libwebp --enable-libx265 --enable-libxml2 --enable-libxvid --enable-libzmq --enable-libzvbi --enable-lv2 --enable-omx --enable-openal --enable-opencl --enable-opengl --enable-sdl2 --enable-pocketsphinx --enable-libmfx --enable-libdc1394 --enable-libdrm --enable-libiec61883 --enable-chromaprint --enable-frei0r --enable-libx264 --enable-shared
libavutil      56. 51.100 / 56. 51.100
libavcodec     58. 91.100 / 58. 91.100
libavformat    58. 45.100 / 58. 45.100
libavdevice    58. 10.100 / 58. 10.100
libavfilter     7. 85.100 /  7. 85.100
libavresample   4.  0.  0 /  4.  0.  0
libswscale      5.  7.100 /  5.  7.100
libswresample   3.  7.100 /  3.  7.100
libpostproc    55.  7.100 / 55.  7.100
```


# 批量视频文件合成

这个用的最多的就是合成 m3u8 视频，某些视频链接🔗是 m3u8 格式。下载下来后会是一堆 `.ts` 视频，每个 `.ts` 视频仅仅只有几秒钟，所以我们需要将这些 `.ts` 视频按照顺序进行合成为一个大的完整视频。

ffmpeg 合成视频可以使用 `concat` 参数。比如我当前目录下有三个 `.ts` 视频：

```bash
$ ls
01.ts 02.ts 03.ts
```

如果将要将这三个 ts 视频按顺序合成的话可以使用下面的命令（视频合成顺序就是你指定的顺序）：

```bash
ffmpeg -i "concat:1.ts|2.ts|3.ts" -c copy file.mp4
```

`-c` 参数是 codec 的意思，即编码器。最后指定输出的视频文件名称，如 file.mp4。

不过，一般一个完整视频的 ts 文件会比较多（经常会遇到几百个 ts 文件）。对于这种情况再直接使用命令行的话就很难受了，所以我们可以建立一个 `file.txt` 文件来告诉它要合并哪些文件。文件内部格式如下：

```bash
file /path/01.ts
file 02.ts
file ./../ 3.ts
```

前面为关键词 file， 后面跟上视频的地址（绝对路径或相对路径）。 ffmpeg 将会按照 txt 文件中的顺序将视频合并。然后在命令行中输入如下命令就可以了：

```bash
$ ffmpeg -f concat -i file.txt -c copy file.mp4
# 或者
$ ffmpeg -f concat -i file.txt -acodec copy -vcodec copy -f mp4 file.mp4
```

如果得到一些像下面的错误：

```log
file.txt: Operation not permitted
```

添加个参数 `-safe 0`：

```bash
$ ffmpeg -f concat -safe 0 -i file.txt -c copy file.mp4
```

如果一切正常的话就会看到多出一个名为 file.mp4 的文件。

**特别说明：**

上面示例只是以 `.ts` 文件为例做说明，并不代表不能合并其他格式的视频文件。`ffmpeg` 的牛逼之处不在于能够合并视频，还可以将音视频分离的两个文件进行合并。

比如将视频文件（example.mp4）和音频文件（example.m4a）进行合并：

```bash
ffmpeg -i example.mp4 -i example.m4a -c:a copy -c:v copy output.mp4
```

# 视频截取

语法示例：

```bash
ffmpeg -ss 00:00:00 -to 00:00:15 -i 源文件名 -vcodec copy -acodec copy 目标文件名 -y
ffmpeg -ss 00:00:00 -t 15 -i 源文件名 -vcodec copy -acodec copy 目标文件名 -y
```

参数说明：

```log
-i                  表示源视频文件
-ss time_start      设置从视频截取开始位置，如 00:00:10 表示从视频的第10s开始截取

-to time_end        设置视频截取的结束位置，如 00:20:00 表示从 time_start 一直截
                    取到视频的第 20 分钟（如果省略则表示开始从 time_start 开始截
                    取全部视频）

-t time_duration    与 -to 一样，不过指定的是具体的时长，单位是秒。如 -t 20 表示从
                    time_start 开始向后截取 20 秒

-vcodec copy        表示使用跟原视频一样的视频编解码器（即不改变任何数据）

-acodec copy        表示使用跟原视频一样的音频编解码器（即不改变任何数据）

-y                  表示如果输出文件已存在则覆盖（可以省略）
```

在做视频截取时视频质量会通常会有一定的损失，如果想要保持原视频质量截取的话可以通过指定 `-vcodec copy -acodec copy` 两个参数。当然了，截取的目标文件的后缀最好也与原文件也保持一致。

|**注意**|
|:-------|
|参数 `-ss` 最好放在 `-i 源文件名` 的前面，如果放在后面则 `-to` 的含义就变了，变成了与 `-t` 一样的效果了。变成了截取多长视频，而不是截取到视频指定的位置。所以一定要特别注意 `-ss` 的位置。|

看个示例：

```bash
$ ls -lh
total 236360
-rw-r--r--@ 1 mingrn97  staff   115M Nov  8 11:33 文件系统.flv
```

视频总时长为 27 分钟，现在以十分钟进行一个切割，共切割成三个视频，之后看下三个视频的大小加起来是否与原视频大小一致：

```bash
$ ffmpeg -ss 00:00:00 -to 00:10:00 -i 文件系统.flv -vcodec copy -acodec copy 文件系统1.flv
$ ffmpeg -ss 00:10:00 -to 00:20:00 -i 文件系统.flv -vcodec copy -acodec copy 文件系统2.flv
$ ffmpeg -ss 00:20:00 -i 文件系统.flv -vcodec copy -acodec copy 文件系统3.flv
```

结果：

```bash
$ ls -lh
total 472920
-rw-r--r--@ 1 mingrn97  staff   115M Nov  8 11:33 文件系统.flv
-rw-r--r--  1 mingrn97  staff    43M Mar 24 17:31 文件系统1.flv
-rw-r--r--  1 mingrn97  staff    42M Mar 24 17:33 文件系统2.flv
-rw-r--r--  1 mingrn97  staff    31M Mar 24 17:33 文件系统3.flv
```

|**Note**|
|:-------|
|注意看第三次截取视频的命令，仅指定了开始截取视频时间（`-ss 00:20:00`），并没有直接截取的结束时间。像这样只有开始没有结束的用法表示的是从指定的开始时间截取到视频结尾。|

# 提取音频

使用ffmpeg可以很轻易从原视频中抽取出其中的音频和视频，抽取音频的命令如下：

```bash
ffmpeg -i input.mp4 -vn -c:a copy output.acc
```

`-vn` 表示 no video，`-c:a` 是 codec of audio 的意思（也可直接使用 `-acodec`），copy 是直接拷贝视频中的原始的音频，这里不会涉及音频的编解码，速度会很快。

对于拷贝后的音频流文件应该使用什么后缀名可以使用 `ffprobe` 进行确定视频中的音频流格式，示例：

```bash
ffprobe input.mp4

...
  Stream #0:1[0x2](und): Audio: aac (LC) (mp4a / 0x6134706D), 48000 Hz, stereo, fltp, 195 kb/s (default)
...
```

这里的 input.mp4 视频文件中的音频流是 acc 编码格式，那我就使用 acc 就好了~

--

如果使用上面两条示例命令提取 mp3 的话，可能会出现类似下面的错误：

```log
[mp3 @ 0x7fe2637062c0] Invalid audio stream. Exactly one MP3 audio stream is required.
Could not write header for output file #0 (incorrect codec parameters ?): Invalid argument
```

这是因为提取 mp3 不需要加任何参数，直接这么提取就可以了：

```bash
ffmpeg -i video.mp4 video.mp3
```

--

需要说明的是，`ffmpeg` 默认会以 `124kbps` 来提取音频，这样如果视频质量很高的话我们就会得到一个全损音质的音频，所以还可以加上 `-ab` 参数指定音频的比特率。

比如以 320k 的比特率输出音频：

```bash
ffmpeg -i video.mp4 -ab 320k video.mp3
```

|**Note**|
|:-------|
|之所以选择 `320Kbps` 的码率是因为该值是 mp3 格式的最高码率~|

# 提取视频

提取视频的命令与音频如出一辙，如下：

```bash
ffmpeg -i input.mp4 -an -c:a copy output.mp4
```

`-an` 表示 no audio，其他关于视频的参数都可以使用，具体就不多说了。


# 视频倍速播放

视频流的播放速度可以通过改变每帧的显示时间戳（PTS）来改变。有两种实现方法：

1）通过将视频导出为 **原始比特流** 格式来实现擦除时间戳，并在创建新时间戳时将其复用到到容器中。

2）使用 setpts 视频过滤器，但是需要重新编码。

## 方法一：使用“原始比特流”实现视频倍速播放

使用“原始比特流”实现音视频倍速播放是无损的，也是推荐的方式。这种方式除了更改时间戳外，还会原样复制视频流。不过前提是：将视频复制到原始比特流格式。

**确定视频编码及帧率：**

将视频复制到原始比特流格式我们需要知道该视频的编码格式，比如 H.264、H.265。如果你不知道目标视频文件是什么编码格式可以借助 `ffprobe` 工具查看。

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

这行信息表示，第一个流是视频流，视频编码格式是 hevc（封装格式为 hev1），这个就是我们俗称的 H.265格式。每一帧的数据表示为 yuv420p，分辨率为3840x2160，这路流的比特率为 1529 kb/s，帧率（fps）为每秒钟 52.47 帧。

这样，我们就确定了视频编码格式（H.265）和视频帧率（52.47），同时我的视频时长为 1:42.74。

**将视频复制到原始比特流格式：**

- 复制 H.264 视频原始比特流格式：

```bash
ffmpeg -i input.mp4 -map 0:v -c:v copy -bsf:v h264_mp4toannexb raw.h264
```

- 复制 H.265 视频原始比特流格式：

```bash
ffmpeg -i input.mp4 -map 0:v -c:v copy -bsf:v hevc_mp4toannexb raw.h265
```

--

其中，`-i` 参数后面的 input.mp4 就是你的具体视频文件，最后产生对应的 raw.h264 或 raw.h265 文件，这个文件名是可以自定义的。

**执行转换：**

得到原始比特流格式文件之后，我们就可以来实现倍速转换了。倍速转换的原理就是修改 “帧率”。在前面我已经得到的我的视频帧率为 52.47，如果想要实现倍速那我只需要将每秒的帧率按照倍数放大即可，比如我想要实现倍速播放，那么就要将帧率 放大二倍：`52.47 x 2 = 104.94`。

也就是说，实现二倍速播放的帧率为 104.94。因此，我只需要使用 -r 参数指定新的速率即可！

```bash
ffmpeg -fflags +genpts -r 104.94 -i raw.h265 -c:v copy output.mp4
```

但是请注意，这种实现方式只适用于没有音频流的视频文件，即视频没有声音。但如果你的视频有声音的话在实现倍速转换时还需要加上音频参数对音频流做二倍数处理。

修改音频流倍速的主要参数是 `atempo`，所以我们的最终命令如下：

```bash
ffmpeg -fflags +genpts -r 104.94 -i raw.h265 -i input.mp4 -map 0:v -c:v copy -map 1:a -af atempo=2 -movflags faststart output.mp4
```

## 方法二：使用 setpts 视频过滤器

使用视频过滤器来实现倍速播放的原理是过改变每个视频帧的显示时间戳（PTS）来实现。

**提取视频流并实现倍速播放：**

基本示例如下：

```
ffmpeg -i input.mkv -an -c:a copy -filter:v "setpts=1*PTS" output.mkv
```

`-filter:v` 参数表示对视频流执行过滤（`v` 表示 video），后面跟的就是具体的过滤器参数，比如 "setpts=1*PTS" 就是正常是视频倍速，如果有多个过滤参数就使用英文 `,` 分隔即可。

如果想要实现快倍速播放就将 PTS 按照倍速缩小即可，比如 "setpts=PTS/2" 或 "setpts=PTS*0.5" 表示的就是快倍播放，"setpts=PTS/4" 或 "setpts=PTS*0.25" 表示的就是快4倍播放。

相反，"setpts=PTS*2" 表示的就是慢倍播放，"setpts=PTS*4" 表示的就是慢4倍播放。

注意：该命令只针对没有音频流的视频文件，如果你的视频文件存在音频流不要使用该命令实现倍速转换。

**提取音频流并实现倍速播放：**

基本示例如下：

```bash
ffmpeg -i input.mkv -vn -c:a copy -filter:a "atempo=1.0" output.acc
```

`-filter:a` 参数表示对视频中的音频流执行过滤（`a` 表示 audio），后面跟的就是具体的过滤器参数，比如 "atempo=1.0" 就是正常是音频倍速，如果有多个过滤参数就使用英文 `,` 分隔即可。

如果想要对音频实现快倍速播放就将 atempo 按照倍数扩大即可，比如 "atempo=2.0" 表示的就是快倍播放，"atempo=0.5" 表示的就是慢倍播放。

特别强调的一点是，atempo 取值参数为 [0.5,2]。也就是说只能实现快倍或慢倍，无法实现四倍速。如果想要实现四倍速就使用英文 `,` 分隔指定多次即可。比如快四倍：

```bash
ffmpeg -i input.mkv -vn -c:a copy -filter:a "atempo=2.0,atempo=2.0" output.acc
```

**对音视频实现倍速播放：**

这个命令其实就是前面两个命令的混合：

```bash
ffmpeg -i input.mkv -filter_complex "[0:v]setpts=0.5*PTS[v];[0:a]atempo=2.0[a]" -map "[v]" -map "[a]" output.mkv
```

--

# 资源链接

https://trac.ffmpeg.org/wiki/How%20to%20speed%20up%20/%20slow%20down%20a%20video