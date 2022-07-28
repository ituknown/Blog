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


# 批量合成视频

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
```

如果得到一些像下面的错误：

```
file.txt: Operation not permitted
```

添加个参数 `-safe 0`：

```bash
$ ffmpeg -f concat -safe 0 -i file.txt -c copy file.mp4
```

如果一切正常的话就会看到多出一个名为 file.mp4 的文件。

# 视频截取

语法示例：

```bash
ffmpeg -ss 00:00:00 -to 00:00:15 -i 源文件名 -vcodec copy -acodec copy 目标文件名 -y
ffmpeg -ss 00:00:00 -t 15 -i 源文件名 -vcodec copy -acodec copy 目标文件名 -y
```

参数说明：

```
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
|参数 `-ss` 最好放在 `-i 源文件名` 的前面，如果放在后面则 `-to` 的含义就变了，变成了与 `-t` 一样的效果了。变成了截取多长视频，而不是截取到视频指定的位置。所以一定要注意 `-ss` 的位置。|

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

# 提取音频

以 video.mp4 提取音频为例：

```bash
ffmpeg -i video.mp4 -vn -y -acodec copy video.aac
```

或

```bash
ffmpeg -i video.mp4 -vn -y -acodec copy video.m4a
```

以上两条命令方式提取 mp3 的话，会报一下错误：

```
[mp3 @ 0x7fe2637062c0] Invalid audio stream. Exactly one MP3 audio stream is required.
Could not write header for output file #0 (incorrect codec parameters ?): Invalid argument
```

其实呢，如果要提取 mp3 的话，不需要加任何参数，直接这么提取就可以了：

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
|之所以选择 `320Kbps` 的码率是因为该值是 mp3 格式的最高码率~|



