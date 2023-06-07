# 前言

[aria2](https://github.com/aria2/aria2) 是一个跨平台的轻量级命令行下载工具，支持 HTTP/HTTPS、BT 以及 Metalink。有了这款下载神器，再想要下载小姐姐的教学视频还不是手到擒来？

主流的 Linux 发行版包管理工具都能直接进行安装 aria2，具体可以参考：[https://command-not-found.com/aria2c](https://command-not-found.com/aria2c)。

下面是 Debian/Ubuntu 发行版命令示例：

```bash
$ sudo apt install aria2 -y
```

# aria2 配置文件说明

aria2 的配置文件默认存储在如下两个位置：

- $HOME/.aria2/aria2.conf（优先级最高）
- $XDG_CONFIG_HOME/aria2/aria2.conf

aria2 在启动时会自动按顺序读取这两处配置，另外我们也可以在启动时使用 `--conf-path=` 参数指定自定义的配置文件。

我一般将自定义的配置文件放到 `/opt` 目录下：

首先创建一个 aria2c 的配置文件：

```bash
$ sudo vim /opt/aria2/aria2.conf
```

我常用的配置内容如下：

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
dir=/opt/aria2/downloads

# bt 服务器地址(多个使用,分隔). GitHub 上有 BT Tracker 列表:
#  https://github.com/XIU2/TrackersListCollection (推荐)
#  https://github.com/ngosang/trackerslist
#
#bt-tracker=http://tracker1/announce,http://tracker2/announce

# 代理
#all-proxy=[http://][USER:PASSWORD@]HOST[:PORT]
```

之后每次使用时使用 `--conf-path` 参数指定该配置文件即可：

```bash
$ aria2c --conf-path /opt/aria2/aria2.conf
```

|**Note**|
|:-------|
|aria2 配置有很多，这里只列举我需要的配置信息。其他配置可以参考官网文档：[https://aria2.github.io/manual/en/html/aria2c.html#options](https://aria2.github.io/manual/en/html/aria2c.html#options)|

# 配置 aria2 开机自启

每次使用 aria2 时都要手动执行命令加载配置文件，总感觉很麻烦。所以我一般会将 aria2 制作成系统服务使用，之后使用[RPC接口](https://aria2.github.io/manual/en/html/aria2c.html#rpc-interface)实现下载。

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