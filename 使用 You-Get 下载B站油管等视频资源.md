# 前言

在B站或有关上看到一个特棒的视频有木有想过将资源下载下载收藏到秘密空间？

你是各种度娘各种谷歌，一顿操作猛如虎 ....

你试过使用谷歌插件 [bilibili哔哩哔哩下载助手](https://chrome.google.com/webstore/detail/bilibili%E5%93%94%E5%93%A9%E5%93%94%E5%93%A9%E4%B8%8B%E8%BD%BD%E5%8A%A9%E6%89%8B/bfcbfobhcjbkilcbehlnlchiinokiijp)，也试过 [youtube-dl](https://ytdl-org.github.io/youtube-dl/) 最后又转战到 IDM。

你会发现：**bilibili哔哩哔哩下载助手** 是真的好用，但是有时候总是遇到资源错误。**youtube-dl** 是各种不支持的解析连接，至于 IDM 需要破解......

这里就介绍一款工具 [You-Get](https://github.com/soimort/you-get)，该工具可利用页面连接进行下载诸如 B站、油管等视频资源，其他的网站也是可以的。

You-Get Github 仓库地址是：[https://github.com/soimort/you-get](https://github.com/soimort/you-get)

除此之外，还至此选择清晰度下载~
# 安装

以 Mac 为例, 使用 `brew` 下载即可：

```bash
$ brew install you-get
```

另外，你也可以直接浏览 [You-Get Github](https://github.com/soimort/you-get) 浏览仓库信息。

安装成功后可以使用 `--help` 命令查看有关帮助信息。

下面以 B站 视频 [《中国惊奇先生》](https://www.bilibili.com/bangumi/play/ss10086/) 为例：
# 下载 B站视频
《中国惊奇先生》B站链接为 [https://www.bilibili.com/bangumi/play/ss10086](https://www.bilibili.com/bangumi/play/ss10086)。

在下载之前先使用 `you-get` 查看视频信息，命令如下：

```bash
$ you-get -i [url] --debug
```

- `-i` 是用于查看视频信息
- `--debug` 是使用 Debug 输出信息（可选）

下面使用该命令进行查看视频资源信息：

```bash
$ you-get -i https://www.bilibili.com/bangumi/play/ss10086
```

输出信息如下：

```
site:                Bilibili
title:               中国惊奇先生
streams:             # Available quality and codecs
    [ DEFAULT ] _________________________________
    - format:        flv
      container:     flv
      quality:       高清 1080P
      size:          1018.0 MiB (1067408284 bytes)
    # download-with: you-get --format=flv [URL]

    - format:        flv720
      container:     flv
      quality:       高清 720P
      size:          1018.0 MiB (1067408284 bytes)
    # download-with: you-get --format=flv720 [URL]

    - format:        flv480
      container:     flv
      quality:       清晰 480P
      size:          1018.0 MiB (1067408284 bytes)
    # download-with: you-get --format=flv480 [URL]

    - format:        flv360
      container:     flv
      quality:       流畅 360P
      size:          673.6 MiB (706331809 bytes)
    # download-with: you-get --format=flv360 [URL]
```

根据提示即可下载具体清晰度，以高清 1080P为例：

```bash
$ you-get --format=flv https://www.bilibili.com/bangumi/play/ss10086
```

最后下载信息如下所示：

```
site:                Bilibili
title:               中国惊奇先生
stream:
    - format:        flv
      container:     flv
      quality:       高清 1080P
      size:          1018.0 MiB (1067408284 bytes)
    # download-with: you-get --format=flv [URL]

Downloading 中国惊奇先生.mp4 ...
13.7% ( 139.6/1018.0MB) ├████████████──────────────────────────────────────────────────────────────────────────┤[ 5/27]  258 kB/s
```

除此之外，你可以使用 `-O` 选项指定文件名。也可以使用 `-o` 选项指定下载到指定目录，不指定即默认下载到当前目录，示例：

**指定下载到具体目录：**

```bash
$ you-get --format=flv [url] -o ~/Download
```

**指定下载到具体目录并指定文件名：**

```bash
$ you-get --format=flv [url] -o ~/Download -O 中国惊奇先生.mp4
```

# 下载B站大会员视频

B 站上有些视频是只有大会员才有权限观看的，`you-get` 没法直接下载这类视频。不过该用具提供了可以添加 cookies 参数：


```
-c COOKIES_FILE, --cookies COOKIES_FILE
                        Load cookies.txt or cookies.sqlite
```

我们只需要使用大会员账号登录后借助 [EditThisCookie](https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg) 等这类能够获取并下载网站 cookies 的工具，将 cookies 下载到本地文件即可：

![EditThisCookieImportCookies-1650165791YByGBoHQ](http://blog-media.knowledge.ituknown.cn/You-Get/EditThisCookieImportCookies-1650165791YByGBoHQ.png)


比如下载后的 cookies 文件名为：cookies.txt。然后使用 `-c` 或 `--cookies` 参数指定该文件即可下载大会员视频了：

```bash
$ you-get -c cookies.txt --format=flv [大会员视频链接]
```

# 使用 Docker

有的时候会觉得直接在机器上安装 `you-get` 不太方便（比如工作电脑？），所以这个时候使用 docker 就显得很有必要的。`you-get` docker 镜像仓库地址是（非作者）：

https://hub.docker.com/r/kmdgeek/you-get

`kmdgeek/you-get` 镜像默认会将文件下载到容器的 `/download` 目录，因此我们只需要将宿主机上的某个目录与该目录做个映射即可。

还是以[《中国惊奇先生》](https://www.bilibili.com/bangumi/play/ss10086/) 为例，使用该镜像将视频下载到我的 `/tmp/download` 目录下：

```bash
$ docker run --rm -v /tmp/download:/download kmdgeek/you-get --format=flv https://www.bilibili.com/bangumi/play/ss10086
```

# 写在最后

如果你以为 **You-Get** 仅仅只能下载视频资源你就 **图样图深破** 了

- 下载图片？

```bash
$ you-get -i https://stallman.org/rms.jpg
Site:       stallman.org
Title:      rms
Type:       JPEG Image (image/jpeg)
Size:       0.06 MiB (66482 Bytes)

$ you-get https://stallman.org/rms.jpg
```

--

https://github.com/soimort/you-get

