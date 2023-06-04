# 前言

流媒体文件通常都会附带些元数据信息（Metadata）。比如一首歌曲，通过元数据信息我们可以很方便的知道歌手、曲名以及对应的专辑。

为了方便说明，可以看下下面一个流媒体文件信息输出示例：

```bash
$ ffprobe M.Graveyard\ -\ you.flac

...

Input #0, flac, from 'M.Graveyard - you.flac':
  Metadata:
    album           : Thanks you
    artist          : M.Graveyard
    title           : you
    encoder         : Lavf60.3.100
  Duration: 00:03:34.85, start: 0.000000, bitrate: 1365 kb/s
  Stream #0:0: Audio: flac, 44100 Hz, stereo, s32 (24 bit)
```

注意看元数据（Metadata）中的信息，可以看到这个音乐媒体文件的歌曲名叫 you，歌手是 M.Graveyard，对应的专辑是 Thanks you。

之所以介绍这个的原因是，我在某音乐平台下载一首歌曲，当我将歌曲导入到网易云音乐之后发现居然没有识别到歌手（提示未知歌手）。当我我还挺纳闷的，后来通过使用 `ffprobe` 查看文件元数据信息时发现，居然没有 Metadata 数据~

不过，知道了原因之后，只需要使用 `ffmpeg` 加上对应的 Metadata 就好了，因此这里记录下。

# 添加/修改 Metadata

添加修改流媒体文件的元数据信息只需要指定加上 -metadata 参数即可，后面跟随对应的键值（每个键值对应一个 -metadata）。示例：

```bash
$ ffmpeg \
-i M.Graveyard\ -\ you.flac \
-c copy \
-metadata title="曲名" \
-metadata artist="歌手" \
output.flac
```

需要强调的一点是，-metadata 每次都是做覆盖更新。如当前流媒体文件元数据信息已存在 title，当我们指定 `-metadata title="..."` 时，就会将原来的 title 信息覆盖掉。因此，添加和修改操作是一样的。

|**Note**|
|:-------|
|不同的流媒体文件，键值（TAG）区别很大。如果你不知道对应的流媒体文件有哪些键值可以参考下FFmpeg官方文档后本文最后的[参考链接](#参考链接)。|

# 删除指定 Metadata

删除指定 Metadata 与添加/修改如出一辙，使用空key即可（`key=`）。如删除 title 信息：

```bash
$ ffmpeg \
-i M.Graveyard\ -\ you.flac \
-c copy \
-metadata title= \
output.flac
```

# 删除全部 Metadata

如果流媒体文件的 Metadata 很多，一个key一个key的删除有点不现实。简单点，直接使用 `-map_metadata -1` 参数即可：

```bash
$ ffmpeg \
-i M.Graveyard\ -\ you.flac \
-c copy \
-map_metadata -1 \
output.flac
```

# 参考链接

https://wiki.multimedia.cx/index.php/FFmpeg_Metadata

http://mplayerhq.hu/pipermail/ffmpeg-user/2018-December/042292.html

https://write.corbpie.com/adding-metadata-to-a-video-or-audio-file-with-ffmpeg/