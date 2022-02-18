# 前言

[yt-dlp](https://github.com/yt-dlp/yt-dlp) 是一款基于 [youtube-dl](https://github.com/ytdl-org/youtube-dl) 和 [youtube-dlc](https://github.com/blackjack4494/yt-dlc) 二次开发的命令行下的视频下载工具。

在此之前，大家可能用的都是 youtube-dl，不可否认我也在用。但实在受不了 youtube-dl 的下载速度，当我知道 yt-dlp 后立马将 youtube-dl 抛弃了。


yt-dlp 工具将 youtube-dl 与 youtube-dlc 做了整合，同时内部增加了多线程下载并做了一些定制化修改。我在使用过发现 yt-dlp 能将速度提到极致。

下面以某国漫（[https://www.youtube.com/watch?v=lHvamusTCK0](https://www.youtube.com/watch?v=lHvamusTCK0)）为例，使用 youtube-dl 和 yt-dlp 的下载速度对比：

![yt-dlp-vs-youtube-dl-1645151899Jfx19M](http://blog-media.knowledge.ituknown.cn/yt-dlp/yt-dlp-vs-youtube-dl-1645151899Jfx19M.png)

很明显，yt-dlp 的下载速度将 youtube-dl 甩了几条街。

yt-dlp 是基于 youtube-dl 二次开发。看着名称像是 YouTube 的专属下载工具，其实不然。该工具不仅支持 YouTube，还支持非常多的视频网站。比如国内的优酷、爱奇艺、 bilibili 等。

|**Note**|
|:-------|
|因为 yt-dlp 是基于 youtube-dl 和 youtube-dlc 做的二次开发，所以在使用时他们的命令及参数基本一致。所以下面的命令说明同样适用于 youtube-dl 和 youtube-dlc。|

# 下载及安装

yt-dlp 的 Github 仓库地址是：[https://github.com/yt-dlp/yt-dlp](https://github.com/yt-dlp/yt-dlp)。

在仓库的 README 说明中有介绍各种操作系统（Unix/Mac/Windows）的安装方式。另外，也可以直接到 [releases](https://github.com/yt-dlp/yt-dlp/releases) 页面下载二进制安装包进行安装。

下面是类Unix（MacOS、Linux、BSD）操作系统使用 `curl`、`wget` 及 `aria2c` 三种下载工具的安装方式：

```bash
sudo curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
sudo chmod a+rx /usr/local/bin/yt-dlp
```

```bash
sudo wget https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -O /usr/local/bin/yt-dlp
sudo chmod a+rx /usr/local/bin/yt-dlp
```

```bash
sudo aria2c https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp --dir /usr/local/bin -o yt-dlp
sudo chmod a+rx /usr/local/bin/yt-dlp
```

如果你在 MacOS 上使用 HomeBrew 作为包管理工具的话可以直接使用 brew 命令进行安装：

```bash
brew install yt-dlp
```

如果你使用 Pip 可以使用下面命令进行安装：

```bash
python3 -m pip install -U yt-dlp

# 可选依赖安装
python3 -m pip install --no-deps -U yt-dlp
```

对于 Windows 系统，推荐直接到 [releases](https://github.com/yt-dlp/yt-dlp/releases) 页面下载二进制程序包吧。

|**注意**|
|:------|
|yt-dlp 在视频合成时基于 ffmpeg，虽然在程序包中有内置 ffmpeg。但还是推荐在操作系统上单独安装一次，防止视频下载后在进行合并音视频的出现未知错误。|


# 常用方式

# 默认方式下载

yt-dlp 下载 Youtube 视频很简单，只需要拷贝浏览器地址栏上的视频链接即可：

```bash
yt-dlp https://www.youtube.com/watch?v=lHvamusTCK0
```

|**Note**|
|:-------|
|YouTube 上的视频都是使用 v 参数指定（就是 av 的意思，同 bilibili）。v 后面的值就是视频号，我们可以直接通过视频号找到指定视频。对于视频专栏（系列视频）则使用 list 参数指定系列号。|


默认情况下，yt-dlp 会自动选择一个视频。除了默认的下载方式，我们也可以借助 `-F` 和 `-f` 参数选择下载指定质量的视频（见下文）。

另外，YouTube 上的视频基本上采用音视频分离的方式，如果安装了 ffmpeg，yt-dlp 会下载质量最好的视频和音频进行合并。不过我们也可以列出所有的音视频文件后手动选择指定质量的音视频：


## 列出所有的音视频文件

可以使用 `-F` 参数列出视频的指定视频的音视频文件：

```bash
yt-dlp -F https://www.youtube.com/watch?v=lHvamusTCK0
```

输出实例：

![yt-dlp-v-F-1645153235ABnGuv](http://blog-media.knowledge.ituknown.cn/yt-dlp/yt-dlp-v-F-1645153235ABnGuv.png)


# 断点续传

```
[youtube] Q6rCpelpwIk: Downloading webpage
[youtube] Q6rCpelpwIk: Downloading android player API JSON
[youtube] Q6rCpelpwIk: Downloading MPD manifest
[youtube] Q6rCpelpwIk: Downloading MPD manifest
[info] Q6rCpelpwIk: Downloading 1 format(s): 248+251
[dashsegments] Total fragments: 177
[download] Destination: 一念永恒 第51 集 1080 【高清】【无广告】 [Q6rCpelpwIk].f248.webm
[download]  87.6% of ~126.29MiB at 517.79KiB/s ETA 00:45 (frag 155/177)ERROR: unable to download video data: <urlopen error EOF occurred in violation of protocol (_ssl.c:1131)>
[download] Downloading video 9 of 9
[youtube] lUqJ7uFEXEQ: Downloading webpage
WARNING: [youtube] Unable to download webpage: <urlopen error EOF occurred in violation of protocol (_ssl.c:1131)>
[youtube] lUqJ7uFEXEQ: Downloading android player API JSON
WARNING: [youtube] <urlopen error EOF occurred in violation of protocol (_ssl.c:1131)>. Retrying ...
[youtube] lUqJ7uFEXEQ: Downloading android player API JSON (retry #1)
WARNING: [youtube] <urlopen error EOF occurred in violation of protocol (_ssl.c:1131)>. Retrying ...
[youtube] lUqJ7uFEXEQ: Downloading android player API JSON (retry #2)
WARNING: [youtube] <urlopen error EOF occurred in violation of protocol (_ssl.c:1131)>. Retrying ...
[youtube] lUqJ7uFEXEQ: Downloading android player API JSON (retry #3)
[youtube] lUqJ7uFEXEQ: Downloading iframe API JS
WARNING: [youtube] Unable to download webpage: <urlopen error EOF occurred in violation of protocol (_ssl.c:1131)>
[youtube] lUqJ7uFEXEQ: Downloading web player API JSON
WARNING: [youtube] Unable to download API page: <urlopen error EOF occurred in violation of protocol (_ssl.c:1131)> (caused by URLError(SSLEOFError(8, 'EOF occurred in violation of protocol (_ssl.c:1131)')))
WARNING: [youtube] nsig extraction failed: You may experience throttling for some formats
n = XCh__1fyLeAzGpZOW ; player = None
Cannot decrypt nsig without player_url; please report this issue on  https://github.com/yt-dlp/yt-dlp , filling out the "Broken site" issue template properly. Confirm you are on the latest version using -U
WARNING: [youtube] nsig extraction failed: You may experience throttling for some formats
n = Qq8f78Kk5yiw32p4F ; player = None
Cannot decrypt nsig without player_url; please report this issue on  https://github.com/yt-dlp/yt-dlp , filling out the "Broken site" issue template properly. Confirm you are on the latest version using -U
[youtube] lUqJ7uFEXEQ: Downloading initial data API JSON
[info] lUqJ7uFEXEQ: Downloading 1 format(s): 248+251
ERROR: unable to download video data: HTTP Error 403: Forbidden
```

# 什么是 WebM 格式

WebM由Google提出，是一个开放、免费的媒体文件格式。

WebM 影片格式其实是以 Matroska（即 MKV）容器格式为基础开发的新容器格式，里面包括了VP8影片轨和 Ogg Vorbis 音轨，其中Google将其拥有的VP8视频编码技术以类似BSD授权开源，Ogg Vorbis 本来就是开放格式。

WebM标准的网络视频更加偏向于开源并且是基于HTML5标准的。相比较而言，我发现同样是 1080p 的视频 WebM 要比 MP4 体积更小，更方便于网络传输，但两者的画面感我看不出有什么差异。

虽然 WebM 有很多好处，但是 MP4 是现在主流的流媒体标准。有些流媒体播放器还不支持 WebM 视频格式，如英特尔对 WebM 的流媒体就存在兼容性问题。
