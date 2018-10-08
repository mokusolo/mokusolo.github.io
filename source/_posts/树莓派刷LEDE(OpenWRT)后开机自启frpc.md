---
title: 树莓派刷LEDE(OpenWrt)后开机自启frpc
date: 2018-10-08 21:30:47
tags: [树莓派, LEDE, 开机自启, frp]
---
树莓派安装LEDE实现翻墙后，总想搞点事情比如远程下载之类。
因为租房的联通网并没有公网IP也懒得折腾，之前用过ngrok，最近无意中发现frp就用上了。
参考[OpenWrt Init Scripts](https://wiki.openwrt.org/doc/techref/initscripts)设置未果，一直苦于树莓派上的frpc服务没能开机自启，远程时就不敢瞎折腾。
最终参考[deploy-frp-on-openwrt](https://blog.ferstar.org/post/deploy-frp-on-openwrt/)得以解决，似乎是启动顺序问题，在frpc脚本运行到的时候仍有需要的服务未开启。
救命语句
```bash
sleep 30
```
![](/img/lede17.01_autostart_frpc_when_reboot.png)