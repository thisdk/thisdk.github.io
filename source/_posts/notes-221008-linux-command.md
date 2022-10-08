---
title: Linux 命令记录
tags: Linux
categories: 笔记
date: 2022-10-08 14:37:00
---

### 全盘查找文件


``` shell
find / -name "*.log"
```

### 进入Docker容器内部执行命令

``` shell
docker exec -it tomcat bash
```


### Ubuntu 清除多余配置文件

``` shell
dpkg -l |grep ^rc|awk '{print $2}' |xargs dpkg -P
```

### Swap 交换分区创建设置

```
# 创建一个1G大小名为swap的文件
fallocate -l 1G swap

# 设置swap文件为swap分区
mkswap -f swap
```