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

## 支持的网站

具体可以直接浏览 [Github仓库说明至此的视频网站哦](https://github.com/soimort/you-get#supported-sites)

下面是列举的部分网站：

| Site | URL | Videos? | Images? | Audios? |
| :---: | :--- | :---: | :---: | :---: |
| **YouTube** | [https://www.youtube.com/](https://www.youtube.com/) | ✓ |  |  |
| **Twitter** | [https://twitter.com/](https://twitter.com/) | ✓ | ✓ |  |
| VK | [http://vk.com/](http://vk.com/) | ✓ | ✓ |  |
| Vine | [https://vine.co/](https://vine.co/) | ✓ |  |  |
| Vimeo | [https://vimeo.com/](https://vimeo.com/) | ✓ |  |  |
| Vidto | [http://vidto.me/](http://vidto.me/) | ✓ |  |  |
| Videomega | [http://videomega.tv/](http://videomega.tv/) | ✓ |  |  |
| Veoh | [http://www.veoh.com/](http://www.veoh.com/) | ✓ |  |  |
| **Tumblr** | [https://www.tumblr.com/](https://www.tumblr.com/) | ✓ | ✓ | ✓ |
| TED | [http://www.ted.com/](http://www.ted.com/) | ✓ |  |  |
| SoundCloud | [https://soundcloud.com/](https://soundcloud.com/) |  |  | ✓ |
| SHOWROOM | [https://www.showroom-live.com/](https://www.showroom-live.com/) | ✓ |  |  |
| Pinterest | [https://www.pinterest.com/](https://www.pinterest.com/) |  | ✓ |  |
| MusicPlayOn | [http://en.musicplayon.com/](http://en.musicplayon.com/) | ✓ |  |  |
| MTV81 | [http://www.mtv81.com/](http://www.mtv81.com/) | ✓ |  |  |
| Mixcloud | [https://www.mixcloud.com/](https://www.mixcloud.com/) |  |  | ✓ |
| Metacafe | [http://www.metacafe.com/](http://www.metacafe.com/) | ✓ |  |  |
| Magisto | [http://www.magisto.com/](http://www.magisto.com/) | ✓ |  |  |
| Khan Academy | [https://www.khanacademy.org/](https://www.khanacademy.org/) | ✓ |  |  |
| Internet Archive | [https://archive.org/](https://archive.org/) | ✓ |  |  |
| **Instagram** | [https://instagram.com/](https://instagram.com/) | ✓ | ✓ |  |
| InfoQ | [http://www.infoq.com/presentations/](http://www.infoq.com/presentations/) | ✓ |  |  |
| Imgur | [http://imgur.com/](http://imgur.com/) |  | ✓ |  |
| Heavy Music Archive | [http://www.heavy-music.ru/](http://www.heavy-music.ru/) |  |  | ✓ |
| **Google+** | [https://plus.google.com/](https://plus.google.com/) | ✓ | ✓ |  |
| Freesound | [http://www.freesound.org/](http://www.freesound.org/) |  |  | ✓ |
| Flickr | [https://www.flickr.com/](https://www.flickr.com/) | ✓ | ✓ |  |
| FC2 Video | [http://video.fc2.com/](http://video.fc2.com/) | ✓ |  |  |
| Facebook | [https://www.facebook.com/](https://www.facebook.com/) | ✓ |  |  |
| eHow | [http://www.ehow.com/](http://www.ehow.com/) | ✓ |  |  |
| Dailymotion | [http://www.dailymotion.com/](http://www.dailymotion.com/) | ✓ |  |  |
| Coub | [http://coub.com/](http://coub.com/) | ✓ |  |  |
| CBS | [http://www.cbs.com/](http://www.cbs.com/) | ✓ |  |  |
| Bandcamp | [http://bandcamp.com/](http://bandcamp.com/) |  |  | ✓ |
| AliveThai | [http://alive.in.th/](http://alive.in.th/) | ✓ |  |  |
| interest.me | [http://ch.interest.me/tvn](http://ch.interest.me/tvn) | ✓ |  |  |
| **755
ナナゴーゴー** | [http://7gogo.jp/](http://7gogo.jp/) | ✓ | ✓ |  |
| **niconico
ニコニコ動画** | [http://www.nicovideo.jp/](http://www.nicovideo.jp/) | ✓ |  |  |
| **163
网易视频
网易云音乐** | [http://v.163.com/](http://v.163.com/)
[http://music.163.com/](http://music.163.com/) | ✓ |  | ✓ |
| 56网 | [http://www.56.com/](http://www.56.com/) | ✓ |  |  |
| **AcFun** | [http://www.acfun.cn/](http://www.acfun.cn/) | ✓ |  |  |
| **Baidu
百度贴吧** | [http://tieba.baidu.com/](http://tieba.baidu.com/) | ✓ | ✓ |  |
| 爆米花网 | [http://www.baomihua.com/](http://www.baomihua.com/) | ✓ |  |  |
| **bilibili
哔哩哔哩** | [http://www.bilibili.com/](http://www.bilibili.com/) | ✓ |  |  |
| 豆瓣 | [http://www.douban.com/](http://www.douban.com/) | ✓ |  | ✓ |
| 斗鱼 | [http://www.douyutv.com/](http://www.douyutv.com/) | ✓ |  |  |
| Panda
熊猫 | [http://www.panda.tv/](http://www.panda.tv/) | ✓ |  |  |
| 凤凰视频 | [http://v.ifeng.com/](http://v.ifeng.com/) | ✓ |  |  |
| 风行网 | [http://www.fun.tv/](http://www.fun.tv/) | ✓ |  |  |
| iQIYI
爱奇艺 | [http://www.iqiyi.com/](http://www.iqiyi.com/) | ✓ |  |  |
| 激动网 | [http://www.joy.cn/](http://www.joy.cn/) | ✓ |  |  |
| 酷6网 | [http://www.ku6.com/](http://www.ku6.com/) | ✓ |  |  |
| 酷狗音乐 | [http://www.kugou.com/](http://www.kugou.com/) |  |  | ✓ |
| 酷我音乐 | [http://www.kuwo.cn/](http://www.kuwo.cn/) |  |  | ✓ |
| 乐视网 | [http://www.le.com/](http://www.le.com/) | ✓ |  |  |
| 荔枝FM | [http://www.lizhi.fm/](http://www.lizhi.fm/) |  |  | ✓ |
| 秒拍 | [http://www.miaopai.com/](http://www.miaopai.com/) | ✓ |  |  |
| MioMio弹幕网 | [http://www.miomio.tv/](http://www.miomio.tv/) | ✓ |  |  |
| MissEvan
猫耳FM | [http://www.missevan.com/](http://www.missevan.com/) |  |  | ✓ |
| 痞客邦 | [https://www.pixnet.net/](https://www.pixnet.net/) | ✓ |  |  |
| PPTV聚力 | [http://www.pptv.com/](http://www.pptv.com/) | ✓ |  |  |
| 齐鲁网 | [http://v.iqilu.com/](http://v.iqilu.com/) | ✓ |  |  |
| QQ
腾讯视频 | [http://v.qq.com/](http://v.qq.com/) | ✓ |  |  |
| 企鹅直播 | [http://live.qq.com/](http://live.qq.com/) | ✓ |  |  |
| Sina
新浪视频
微博秒拍视频 | [http://video.sina.com.cn/](http://video.sina.com.cn/)
[http://video.weibo.com/](http://video.weibo.com/) | ✓ |  |  |
| Sohu
搜狐视频 | [http://tv.sohu.com/](http://tv.sohu.com/) | ✓ |  |  |
| **Tudou
土豆** | [http://www.tudou.com/](http://www.tudou.com/) | ✓ |  |  |
| 虾米 | [http://www.xiami.com/](http://www.xiami.com/) | ✓ |  | ✓ |
| 阳光卫视 | [http://www.isuntv.com/](http://www.isuntv.com/) | ✓ |  |  |
| **音悦Tai** | [http://www.yinyuetai.com/](http://www.yinyuetai.com/) | ✓ |  |  |
| **Youku
优酷** | [http://www.youku.com/](http://www.youku.com/) | ✓ |  |  |
| 战旗TV | [http://www.zhanqi.tv/lives](http://www.zhanqi.tv/lives) | ✓ |  |  |
| 央视网 | [http://www.cntv.cn/](http://www.cntv.cn/) | ✓ |  |  |
| Naver
네이버 | [http://tvcast.naver.com/](http://tvcast.naver.com/) | ✓ |  |  |
| 芒果TV | [http://www.mgtv.com/](http://www.mgtv.com/) | ✓ |  |  |
| 火猫TV | [http://www.huomao.com/](http://www.huomao.com/) | ✓ |  |  |
| 阳光宽频网 | [http://www.365yg.com/](http://www.365yg.com/) | ✓ |  |  |
| 西瓜视频 | [https://www.ixigua.com/](https://www.ixigua.com/) | ✓ |  |  |
| 快手 | [https://www.kuaishou.com/](https://www.kuaishou.com/) | ✓ | ✓ |  |
| 抖音 | [https://www.douyin.com/](https://www.douyin.com/) | ✓ |  |  |
| TikTok | [https://www.tiktok.com/](https://www.tiktok.com/) | ✓ |  |  |
| 中国体育(TV) | [http://v.zhibo.tv/](http://v.zhibo.tv/)
[http://video.zhibo.tv/](http://video.zhibo.tv/) | ✓ |  |  |
| 知乎 | [https://www.zhihu.com/](https://www.zhihu.com/) | ✓ |  |  |


