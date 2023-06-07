# 前言

aria2 是一个跨平台的轻量级命令行下载工具，支持 HTTP/HTTPS、 FTP、BitTorrent 和 Metalink。简称下载神器，摆脱迅雷想想下载小姐姐还不是手到擒来？

Debian/Ubuntu 执行下面命令安装 aria2：

```bash
$ sudo apt install aria2 -y
```

其他 Linux 发行版可以参考：https://command-not-found.com/aria2c

# aria2 配置文件说明

aria2 的默认配置文件主要存储在如下两个位置：

- $HOME/.aria2/aria2.conf（优先级最高）
- $XDG_CONFIG_HOME/aria2/aria2.conf

aria2 在启动时会自动按顺序读取这两处配置，另外我们也可以使用 `--conf-path` 参数修改配置文件位置。我一般会将配置文件放到 `/opt` 目录下：

首先创建一个 aria2c 的配置文件：

```bash
$ sudo vim /opt/aria2/aria2.conf
```

之后在配置文件中输入如下内容：

```properties
# 启用 rpc
enable-rpc=true

# rpc 密码(用于认证)
#rpc-passwd=

# rpc 端口(默认6800)
# 可以通过 http://ip:6800/jsonrpc 进行rpc访问
rpc-listen-port=6800

# 断点续传
continue=true

# 下载存储目录
#dir=/opt/aria2/downloads

# bt 服务器地址(多个使用,分隔)
#
#  示例：
#    http://tracker1/announce,http://tracker2/announce
#
#  GitHub 上有 BT Tracker 列表:
#
#    https://github.com/XIU2/TrackersListCollection (推荐)
#    https://github.com/ngosang/trackerslist
#
#bt-tracker=
```

之后每次使用时使用 `--conf-path` 参数指定该配置文件即可：

```bash
$ aria2c --conf-path /opt/aria2/aria2.conf
```

|**Note**|
|:-------|
|aria2 配置有很多，这里只列举我需要的配置信息。其他配置可以参考官网文档：[https://aria2.github.io/manual/en/html/aria2c.html#options](https://aria2.github.io/manual/en/html/aria2c.html#options)|

# 配置 aria2 开机自启

每次使用 aria2 时都要手动执行命令加载配置文件，总感觉很麻烦。所以我一般会将 aria2 制作成系统服务使用。

不过，aria2 安装时默认并没有对应的 systemd 服务单元。因此，我们需要手动创建 aria2 的 systemd 服务单元。

执行下面命令创建 aria2 的 systemd 服务单元：

```bash
$ sudo vim /usr/lib/systemd/system/aria2.service
```

在服务单元中输入下面内容（更多参数可以自行扩展）：

```shell
[Unit]
Description=Aria2
[Service]
ExecStart=/usr/bin/aria2c --conf-path=/opt/aria2/aria2.conf
[Install]
WantedBy=multi-user.target
```

保存之后执行下面命令，立即启用 aria2 服务：

```bash
$ sudo systemctl enable --now aria2
```

可执行下面命令，验证 aria2 服务是否正常运行：

```bash
$ sudo systemctl status aria2
```

如下图所示，出现 <span style="color: #3F953A;">active (running) </span> 表示成功启用（另外还能看到 rpc 端口号 6800）：

![systemctl-status-aria2.service.png](http://blog-media.knowledge.ituknown.cn/aria2/systemctl-status-aria2.service.png)


--

https://www.bilibili.com/read/cv16612773