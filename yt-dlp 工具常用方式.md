# 前言

[yt-dlp](https://github.com/yt-dlp/yt-dlp) 是一款基于 [youtube-dl](https://github.com/ytdl-org/youtube-dl) 和 [youtube-dlc](https://github.com/blackjack4494/yt-dlc) 二次开发的命令行下的视频下载工具。

在此之前，大家可能用的都是 youtube-dl，不可否认我也在用。但实在受不了 youtube-dl 的下载速度，当我知道 yt-dlp 后立马将 youtube-dl 抛弃了。


yt-dlp 工具将 youtube-dl 与 youtube-dlc 做了整合，同时内部增加了多线程下载并做了一些定制化修改。我在使用过发现 yt-dlp 能将速度提到极致。

下面以某国漫（[https://www.youtube.com/watch?v=lHvamusTCK0](https://www.youtube.com/watch?v=lHvamusTCK0)）为例，使用 youtube-dl 和 yt-dlp 的下载速度对比：

![yt-dlp-vs-youtube-dl-1645151899Jfx19M](http://blog-media.knowledge.ituknown.cn/yt-dlp/yt-dlp-vs-youtube-dl-1645151899Jfx19M.png)

很明显，yt-dlp 的下载速度将 youtube-dl 甩了几条街。

yt-dlp 是基于 youtube-dl 二次开发，**是 YouTube 的专属下载工具**。另外，该工具还支持其他的视频网站。比如国内的优酷、爱奇艺、 bilibili 等。

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


# 音视频相关常用方式

## 默认方式下载命令

yt-dlp 下载 Youtube 视频很简单，只需要拷贝浏览器地址栏上的视频链接即可：

```bash
yt-dlp https://www.youtube.com/watch?v=lHvamusTCK0
```

默认情况下，yt-dlp 会自动选择一个视频。

|**Note**|
|:-------|
|YouTube 上的视频都是使用 v 参数指定（就是 av 的意思，同 bilibili）。v 后面的值就是视频号，我们可以直接通过视频号找到指定视频。对于视频专栏（系列视频）则使用 list 参数指定系列号。|

需要特别强调了是：YouTube 上的视频采用音视频分离的方式。如果安装了 ffmpeg，yt-dlp 默认会下载质量最高的视频和音频并进行合并，这也是为什么在之前推荐单独安装 ffmpeg 的原因。

默认情况下，yt-dlp 会自动下载适合的音视频进行合并（如果操作系统中有 ffmpeg 的话默认会下载质量最高的音视频），不过我们也可以借助 `-F` 和 `-f` 参数选择下载指定质量的音视频。


## 列出所有的音视频文件

使用 `-F` 参数可以列出指定视频链接的全部音视频文件：

```bash
yt-dlp -F https://www.youtube.com/watch?v=lHvamusTCK0
```

输出示例：

![yt-dlp-v-F-1645153235ABnGuv](http://blog-media.knowledge.ituknown.cn/yt-dlp/yt-dlp-v-F-1645153235ABnGuv.png)

注意看输出示例中的 VCODEC 栏信息，共分为三类，分别是 images、audio only 和 videi only。也就是说 Youtube 上的视频是音视频分开的。如果你仅仅下载 video only 文件是只有画面没有声音，只下载 audio only 文件则只有声音没有画面。因此在下载时需要同时下载一个 audio only 文件和一个 video only 文件（yt-dlp 默认下载方式会自动下载音视频文件）。

另外，video only 文件分为两类流媒体格式，分别是 mp4 和 webm。webm 是谷歌推出的流媒体格式，与 mp4 区别不大，可以参考文章最后的 webm 文件说明。

输出栏中还有一列 ID 字段，这个是流媒体文件标识，如果想要下载指定的质量的流媒体就需要使用 `-f` 参数指定该 ID（下面会进行说明）。


## 下载指定音视频文件

`-f` 参数则用于下载指定的文件。比如上面示例中的 1920x1080 的 mp4 视频文件，对应的 ID 为 137：

```
137 mp4   1920x1080   25 │    1.24GiB 1158k https │ avc1.640028   1158k video only              1080p, mp4_dash
```

下载命令如下：

```bash
yt-dlp -f 137 https://www.youtube.com/watch?v=lHvamusTCK0
```

另外，`-f` 参数可同时指定下载多个文件，也可以指定范围下载文件。示例：

```
-f 399,137,248: 下载指定文件
-f 135-137,248-250: 下载指定范围文件(ID 不存在没关系, 会在你指定的范围内查找要下载的 ID 文件)
```

需要强调的一点是，Youtube 上的流媒体文件是音视频分离的。因此，你如果使用 `-f` 这种方式下载视频文件必须同时指定要下载的音频文件，否则下载完成后的视频要么只要画面没有声音要么只有声音没有画面。

视频文件和音频文件使用 `+` 分隔，比如要下载的视频文件 ID 为 137，音频文件为 140。则命令如下：

```bash
yt-dlp -f 137+140 https://www.youtube.com/watch?v=lHvamusTCK0
```

不要担心下载完成后会有两个文件，yt-dlp 会使用 ffmpeg 工具自动将下载完成后的视频和音频进行合并成一个流媒体文件。

`-f` 参数除了直接指定 ID 外还可以使用下面这种语法（也是 yt-dlp 默认下载形式）：

```
-f bestvideo+bestaudio
```

这种语法会自动下载质量最高的音视频文件并进行合并（前提是安装了 ffmpeg ）。比如上面的示例中 1920x1080 的视频文件有两个：

```
137 mp4   1920x1080   25 │    1.24GiB 1158k https │ avc1.640028   1158k video only              1080p, mp4_dash
248 webm  1920x1080   25 │    1.08GiB 1014k https │ vp9           1014k video only              1080p, webm_dash
```

bestvideo 形式默认会自动选择 webm 格式，因此我们可以使用下面的语法指定如果没有 mp4 格式的视频文件的话再选择 webm 视频文件：

```
-f bestvide[ext=mp4]/bestvideo[ext=webm]+bestaudio
```

对于音频文件也是一样的，可以指定优选选择 m4a 还是 webm 的音频文件（默认选择 webm）：

```
-f bestvideo[ext=mp4]/bestvideo[ext=webm]+bestaudio[ext=m4a]/bestaudio[ext=webm]
```

### 音视频文件合并

有时候使用 `-f` 参数指定具体音视频文件虽然下载完成了，但是不会自动合并，比如使用下面的命令：

```bash
yt-dlp -f bestvide+bestaudio https://www.youtube.com/video?v=PLpljE1hzFbZxxx
```

得到两个音视频文件：

```bash
bestvide.mp4
bestaudio.m4a
```

遇到这种情况不用担心，我们可以使用 `ffmpeg` 手动执行下合并，命令如下：

```bash
ffmpeg -i bestvide.mp4 -i bestaudio.m4a -c:a copy -c:v copy output.mp4
```

其中 `bestvide.mp4` 就是你下载后的视频文件，`bestaudio.m4a` 是你下载后的音频文件，而最后的 `output.mp4` 就是你合并之后输出的文件了。

## 下载播放列表

Youtube 视频下载怎么能缺少专栏呢（就是常说的电视剧，在 Youtube 上叫播放列表 playlist）。播放列表的 URL 通常是下面的形式：

```
https://www.youtube.com/playlist?list=PLpljE1hzFbZZMIEUSB_XL7UKr3iAwq7X_
```

yt-dlp 也可以直接下载播放列表：

```
yt-dlp https://www.youtube.com/playlist?list=PLpljE1hzFbZZMIEUSB_XL7UKr3iAwq7X_
```

这样会自动下载播放列表中的全部视频，另外也可以使用 `--playlist` 相关参数下载播放列表指定起始位置：

```
--playlist-start NUM            下载播放列表起始位置, 默认1
--playlist-end NUM              下载播放列表结束位置, 默认 last
--playlist-items ITEM_SPEC      下载播放列表里的特定选集. 如 --playlist-items 1,3,5,7-10, 就会下载 1,3,5,7,8,9,10 集
```

|**Note**|
|:-------|
|在下载播放列表指定集之前可以先使用 `-F` 参数看下有哪些选集|


## 断点续传

如果在下载过程中网络突然断了是一件很麻烦的视频（如下日志）：

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

难道要重新下载？不需要，可以使用 `-c` 参数指定断点续传。加上该参数之后就会自动接着上次下载的位置下载了：

```bash
yt-dlp -c -f 137+140 https://www.youtube.com/watch?v=lHvamusTCK0
```


# 字幕相关常用方式

有些 Youtube 视频字幕也是分离的，所以对于某些视频来说我们还需要下载下对应的字幕。字幕主要有下面这些参数：

```
--list-subs               列出所有字幕
--write-sub               下载字幕文件
--all-subs                下载所有可用字幕
--write-auto-sub          下载自动生成的字幕文件
--sub-lang LANGS          下载指定语言的字幕, 多种语言字符之间使用逗号分隔(如: --sub-lang "")
--sub-format FORMAT       字幕文件格式, 如 "srt" 或 "ass/srt/vtt"(首选 ass 格式, 没有就选择 srt. 以此类推)
--embed-sub               把字幕合并到视频中，只支持 mp4、mkv 和 webm 格式的视频
--skip-download           不下载视频(适用于仅下载字幕时使用)
```

下面的示例都以 [MIT 6.824: Distributed Systems](https://www.youtube.com/channel/UC_7WrbZTCODu1o_kfUMq88g) 第十三节课（`https://www.youtube.com/watch?v=4eW5SWBi7vs`）为例做说明。

**特别强调：** `--write-auto-sub` 参数用于下载自动生成的字幕文件，意思就是某些视频本身没有字幕，但是 youtube 比较牛逼，可以自动生成多语言字幕。对于这种本身没有字幕的视频我们就可以借助 `--write-auto-sub` 参数来实现字幕下载。因此，在下载 youtube 视频时推荐都加上 `--write-auto-sub` 参数。

看下示例：

## 列出可用字幕

通常，youtube 自动生成比较多，所以在列出字幕时可以过滤下，比如我只过滤可用的中/英文字幕：

```bash
$ yt-dlp --list-subs https://www.youtube.com/watch\?v\=4eW5SWBi7vs | egrep 'en|zh'
hy       Armenian              vtt, ttml, srv3, srv2, srv1, json3
zh-Hans  Chinese (Simplified)  vtt, ttml, srv3, srv2, srv1, json3
zh-Hant  Chinese (Traditional) vtt, ttml, srv3, srv2, srv1, json3
en-orig  English (Original)    vtt, ttml, srv3, srv2, srv1, json3
en       English               vtt, ttml, srv3, srv2, srv1, json3
fr       French                vtt, ttml, srv3, srv2, srv1, json3
sl       Slovenian             vtt, ttml, srv3, srv2, srv1, json3
tk       Turkmen               vtt, ttml, srv3, srv2, srv1, json3
```

## 字幕文件下载

有时我们可能仅需要下载视频的字幕文件，对于这种场景我们可以加上 `--skip-download` 参数，表示不下载视频仅下载字幕。

比如我下载自动生成的中文和英文字幕文件：


```bash
$ yt-dlp --write-auto-sub --sub-lang "zh-Hans,en" --skip-download https://www.youtube.com/watch\?v\=4eW5SWBi7vs

[youtube] 4eW5SWBi7vs: Downloading webpage
[youtube] 4eW5SWBi7vs: Downloading android player API JSON
[youtube] 4eW5SWBi7vs: Downloading MPD manifest
[youtube] 4eW5SWBi7vs: Downloading MPD manifest
[info] 4eW5SWBi7vs: Downloading subtitles: zh-Hans, en
[info] 4eW5SWBi7vs: Downloading 1 format(s): 243+251
[info] Writing video subtitles to: Lecture 13： Spanner [4eW5SWBi7vs].zh-Hans.vtt
[download] Destination: Lecture 13： Spanner [4eW5SWBi7vs].zh-Hans.vtt
[download] 100% of  512.36KiB in 00:00:01 at 410.79KiB/s
[info] Writing video subtitles to: Lecture 13： Spanner [4eW5SWBi7vs].en.vtt
[download] Destination: Lecture 13： Spanner [4eW5SWBi7vs].en.vtt
[download] 100% of  551.15KiB in 00:00:01 at 526.30KiB/s

$ ls
Lecture 13： Spanner [4eW5SWBi7vs].en.vtt
Lecture 13： Spanner [4eW5SWBi7vs].zh-Hans.vtt
```

## 将字幕内嵌到视频

另外，我们也可以在下载视频时直接将自动生成的字幕文件内嵌到视频中，这里我选择将中/英文字幕直接内嵌到视频中：

```bash
$ yt-dlp --write-auto-sub --sub-lang "en,zh-Hans" --embed-sub https://www.youtube.com/watch?v=4eW5SWBi7vs
```

现在，当我们播放视频时就可以随意切换字幕了：

![yt-dlp-embed-subtitle-to-video](http://blog-media.knowledge.ituknown.cn/yt-dlp/yt-dlp-embed-subtitle-to-video.png)

# 网络相关常用方式

## 设置网络代理

既然下载 Youtube 上的视频怎们能少的了代理呢？

```
--proxy URL
```

示例：

```bash
yt-dlp --proxy sockss://127.0.0.1:8889 -f 137+140 https://www.youtube.com/watch?v=lHvamusTCK0
```

## 设置网络超时时间

另外还可以设置连接超时时间：

```
--socket-timeout SECONDS
```

## 限制最大下载速度

如果在上班时偷偷使用 yt-dlp 下载 Youtube 上的视频，每秒几十兆几十兆的下载很容易被网管监控到。所以聪明的做法应该限制一下 yt-dlp 的最大下载速度：

```
-r, --limit-rate      MAX_RATE
```

参数 `MAX_RATE` 就是你指定的每秒下载速度上限，如 50k、4.2M、1024G。

# 用户认证相关

```
-u, --username USERNAME          指定用户名
-p, --password PASSWORD          指定用户密码
```

# 其他参数

## 查看支持的网站列表

如果想要知道 yt-dlp 工具支不支持你想下载的网站的视频可以使用下面参数看下。该参数会列出所有支持的网站列表：

```
--list-extractors
```

比如我想看看支不支持 bilibili：

```bash
$ yt-dlp --list-extractors | grep bilibili
bangumi.bilibili.com
```

这里显示支持。那我使用 `-F` 参数看下尚硅谷的c语言课程试试：

```bash
$ yt-dlp -F https://www.bilibili.com/video/BV1qJ411z7Hf
[BiliBili] 1qJ411z7Hf: Grabbing original ID via API
[BiliBili] 77774320: Downloading webpage
[BiliBili] 77774320: Extracting videos in anthology
[BiliBili] Downloading anthology 77774320 - add --no-playlist to just download video
[download] Downloading playlist: 尚硅谷C语言零基础快速入门教程
[BiliBili] playlist 尚硅谷C语言零基础快速入门教程: Downloading 171 videos
[download] Downloading video 1 of 171
[BiliBili] 1qJ411z7Hf: Grabbing original ID via API
[BiliBili] 77774320: Downloading webpage
[BiliBili] 77774320: Extracting videos in anthology
[BiliBili] 77774320: Downloading tags
[info] Available formats for 77774320_part1:
ID EXT RESOLUTION │  FILESIZE  TBR PROTO │ VCODEC            VBR ACODEC      ABR
─────────────────────────────────────────────────────────────────────────────────
0  m4a audio only │ ~ 4.85MiB  67k https │ audio only            mp4a.40.2   67k
1  m4a audio only │ ~ 9.59MiB 132k https │ audio only            mp4a.40.2  132k
2  m4a audio only │ ~23.04MiB 319k https │ audio only            mp4a.40.2  319k
3  m4s unknown    │                https │ unknown               unknown
4  m4s unknown    │                https │ unknown               unknown
5  m4s unknown    │                https │ unknown               unknown
6  m4s unknown    │                https │ unknown               unknown
7  m4s unknown    │                https │ unknown               unknown
8  m4s unknown    │                https │ unknown               unknown
9  m4s unknown    │                https │ unknown               unknown
10 m4s unknown    │                https │ unknown               unknown
11 m4s unknown    │                https │ unknown               unknown
12 m4s unknown    │                https │ unknown               unknown
13 m4s unknown    │                https │ unknown               unknown
14 m4s unknown    │                https │ unknown               unknown
15 m4s unknown    │                https │ unknown               unknown
16 m4s unknown    │                https │ unknown               unknown
17 m4s unknown    │                https │ unknown               unknown
18 m4s unknown    │                https │ unknown               unknown
19 m4s unknown    │                https │ unknown               unknown
20 m4s unknown    │                https │ unknown               unknown
21 m4s unknown    │                https │ unknown               unknown
22 m4s unknown    │                https │ unknown               unknown
23 m4s unknown    │                https │ unknown               unknown
24 m4s unknown    │                https │ unknown               unknown
25 mp4 600x360    │ ~ 6.57MiB  90k https │ avc1.64001E       90k video only
26 mp4 600x360    │ ~12.31MiB 170k https │ hev1.1.6.L120.90 170k video only
27 mp4 800x480    │ ~10.02MiB 138k https │ avc1.64001F      138k video only
28 mp4 800x480    │ ~12.08MiB 167k https │ hev1.1.6.L120.90 167k video only
29 mp4 1200x720   │ ~17.16MiB 237k https │ avc1.640020      237k video only
30 mp4 1200x720   │ ~11.78MiB 163k https │ hev1.1.6.L120.90 163k video only
31 mp4 1280x768   │ ~15.40MiB 213k https │ avc1.640028      213k video only
32 mp4 1280x768   │ ~11.96MiB 165k https │ hev1.1.6.L120.90 165k video only
[download] Downloading video 2 of 171
[BiliBili] 1qJ411z7Hf: Grabbing original ID via API
[BiliBili] 77774320: Downloading webpage
[BiliBili] 77774320: Extracting videos in anthology
[BiliBili] 77774320: Downloading tags
[info] Available formats for 77774320_part2:
...
```

那现在我下载课程还不轻而易举？

## 升级 yt-dlp


```
yt-dlp -U
```

# 什么是 WebM 格式

WebM由Google提出，是一个开放、免费的媒体文件格式。

WebM 影片格式其实是以 Matroska（即 MKV）容器格式为基础开发的新容器格式，里面包括了VP8影片轨和 Ogg Vorbis 音轨，其中Google将其拥有的VP8视频编码技术以类似BSD授权开源，Ogg Vorbis 本来就是开放格式。

WebM标准的网络视频更加偏向于开源并且是基于HTML5标准的。相比较而言，我发现同样是 1080p 的视频 WebM 要比 MP4 体积更小，更方便于网络传输，但两者的画面感我看不出有什么差异。

虽然 WebM 有很多好处，但是 MP4 是现在主流的流媒体标准。有些流媒体播放器还不支持 WebM 视频格式，如英特尔对 WebM 的流媒体就存在兼容性问题。
