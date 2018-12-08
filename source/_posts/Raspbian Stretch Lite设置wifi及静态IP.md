---
title: Raspbian Stretch Lite设置wifi及静态IP
date: 2018-12-08 12:05:36
tags: [树莓派]
---
记录以备查

1. 设置要连接的wifi名称和密码

```
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```
添加内容
```
network={
    ssid="wifi_name"
    psk="wifi_password"
}
```
2. 设置wifi连接下的静态ip

```
sudo nano /etc/dhcpcd.conf
```
按照上面的例子添加内容，这里假设所在网段为192.168.8.1
```
interface wlan0
static ip_address=192.168.8.111/24
static routers=192.168.8.1
static domain_name_servers=192.168.8.1 8.8.8.8
```

如果要设置有线网的静态ip，把interface wlan0改为interface eth0即可。

3. 以上前提为ssh已连接
在现在的版本镜像中，将image刷入microsd卡后，新建一个没有后缀的ssh文件开启功能。

```
touch ssh
```

参考
1. [树莓派3B命令行配置wifi，远程桌面连接，扩展存储](https://blog.csdn.net/wangteng12345678/article/details/77140773)
2. [Raspberry PI 3静态IP配置](https://blog.csdn.net/u011973222/article/details/72843127)