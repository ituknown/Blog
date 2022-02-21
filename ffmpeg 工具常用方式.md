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

ffmpeg 合成视频可以使用 concat 参数。比如我当前目录下有三个 `.ts` 视频：

```bash
$ ls
01.ts 02.ts 03.ts
```

如果将要将这三个 ts 视频按顺序合成的话可以使用下面的命令（视频合成顺序就是你指定的顺序）：

```bash
ffmpeg -i "concat:1.ts|2.ts|3.ts" -c copy file.mp4
```

`-c` 参数是 codec 的意思，即编码器。最后指定输出的视频文件名称，如 file.mp4。

不过，一般一个完整视频的 ts 文件会比较多（经常会遇到尽百个 ts 文件）。对于这种情况再直接使用命令行的话就很难受了，所以我们可以建立一个 file.txt 文件来告诉它要合并哪些文件。文件内部格式如下：

```bash
file '01.ts' 
file '02.ts' 
file '03.ts' 
```

前面为关键词 file， 后面跟上视频的地址和名字。 ffmpeg 将会按照 txt 文件中的顺序将视频合并。然后在命令行中输入如下命令就可以了：

```bash
$ ls
01.ts 02.ts 03.ts file.txt

$ ffmpeg -f concat -i file.txt -c copy file.mp4
```

如果一切正常的话就会看到多出一个名为 file.mp4 的文件。
