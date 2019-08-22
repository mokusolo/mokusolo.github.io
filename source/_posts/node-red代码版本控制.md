---
title: node-red代码版本控制
date: 2019-08-22 22:04:23
tags: [node-red, git]
---
记录以备查

之前对于node-red代码的备份都是复制~/.node-red/flows_xxx.json文件并保存n个版本，很烦，即使使用Beyond Compare这种对比软件去对比未美化格式的json文件查看更改内容也是一团糟，搜索时发现可以通过更改~/.node-red/settings.js中的设置使flows_xxx.json文件保存时就美化格式。
```
sudo nano ~/.node-red/settings.js
# ctrl+w 搜索 flowFilePretty 找到该行并取消注释
```
复制文件并不是一个好的版本控制解决方案，发现官方在0.18版本开始就有projects一说并支持版本控制，只是在设置里默认不开启。详见[projects](https://nodered.org/docs/user-guide/projects/)

1. 开启projects支持

```
sudo nano ~/.node-red/settings.js
# 找到projects: {enabled: false} 改为 enabled: true
```

2. 重启node-red

```
ls -ll ~/.node-red/
# 会发现多了projects/目录
```

3. 重新访问node-red网页服务地址如127.0.0.1:1880
根据提示创建或者克隆项目

4. 侧边栏信息
![](/img/2019/project_info_sidebar.png)
上图中info页显示当前的项目名

![](/img/2019/project_local_changes.png)
上图中history页中，Local Changes下的Local files显示当前变更的文件，点击文件名可以看到具体节点的变化内容，按+就相当于git add filename的意思；Changes to commit显示已经add还没commit的文件，按commit后会让你输入描述，确定后就是git commit -m "description"。

![](/img/2019/project_commit_history.png)
上图中，Commit History可以看到提交的历史。若设置了远程仓库地址，在这里还能看到本地比远程ahead了几个commit之类的信息，也可以push或者pull操作。

