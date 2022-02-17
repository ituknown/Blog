# 前言

[yt-dlp](https://github.com/yt-dlp/yt-dlp) 是一款基于 [youtube-dl](https://github.com/ytdl-org/youtube-dl) 和 [youtube-dlc](https://github.com/blackjack4494/yt-dlc) 二次开发的命令行下的视频下载工具。

在此之前，大家可能用的都是 youtube-dl，不可否认我也在用。但实在受不了 youtube-dl 的下载速度，当我知道 yt-dlp 后立马将 youtube-dl 抛弃了。


yt-dlp 工具将 youtube-dl 与 youtube-dlc 做了整合，同时内部增加了多线程下载并做了一些定制化修改。我在使用过发现 yt-dlp 能将速度提到极致。

下面以某国漫（[https://www.youtube.com/watch?v=lHvamusTCK0](https://www.youtube.com/watch?v=lHvamusTCK0)）为例，使用 youtube-dl 和 yt-dlp 的下载速度对比：

![yt-dlp-vs-youtube-dl-16451013518FPkr3nP](http://blog-media.knowledge.ituknown.cn/yt-dlp/yt-dlp-vs-youtube-dl-16451013518FPkr3nP.png)

很明显，yt-dlp 的下载速度将 youtube-dl 甩了几条街。

yt-dlp 是基于 youtube-dl 二次开发。看着名称像是 YouTube 的专属下载工具，其实不然。该工具不仅支持 YouTube，还支持非常多的视频网站。比如国内的优酷、爱奇艺、 bilibili 等。

|**Note**|
|:-------|
|因为 yt-dlp 是基于 youtube-dl 和 youtube-dlc 做的二次开发，所以在使用时他们的命令及参数基本一直。所以下面的命令说明同样适用于 youtube-dl 和 youtube-dlc。|

# 下载及安装

yt-dlp 的 Github 仓库地址是：[https://github.com/yt-dlp/yt-dlp](https://github.com/yt-dlp/yt-dlp)。

在仓库的 README 说明中有介绍 Unix/Mac/Windows 的安装方式。另外，也可以直接到 [releases](https://github.com/yt-dlp/yt-dlp/releases) 页面下载二进制安装包，下面说下 Unix 和 Mac 的命令行安装方式：

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

如果你使用 Pip 的可以使用下面命令进行安装：

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

默认情况下，yt-dlp 会自动选择一个视频，但是这个视频不一定是质量最好的，我们可以自己选择需要下载的视频。

只需要拷贝 youtube 地址栏上的视频链接即可：

```bash
yt-dlp https://www.youtube.com/watch?v=lHvamusTCK0
```

|**Note**|
|:-------|
|YouTube 上的视频都是使用 v 参数指定（就是 av 的意思，同 bilibili）。v 后面的值就是视频号，我们可以直接通过视频号找到指定视频。对于视频专栏（系列视频）则使用 list 参数指定系列号。|

另外，YouTube 采用了音视频分离的方式，如果安装了 ffmpeg，yt-dlp 会下载质量最好的视频和音频进行合并。不过我们也可以列出所有的音视频文件后手动选择指定质量的音视频：


## 列出所有的音视频文件

可以使用 `-F` 参数列出视频的指定视频的音视频文件：

```bash
yt-dlp -F https://www.youtube.com/watch?v=lHvamusTCK0
```

输出实例：

![yt-dlp-v-F-16451039241JEt0tLb](http://blog-media.knowledge.ituknown.cn/yt-dlp/yt-dlp-v-F-16451039241JEt0tLb.png)