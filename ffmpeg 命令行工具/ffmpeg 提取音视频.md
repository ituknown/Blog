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