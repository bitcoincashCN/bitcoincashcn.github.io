---
layout: post
title:  "BCC节点钱包快速部署说明"
date:   2019-08-01
categories: 新手入门教程
tags: BCC 教程 新手入门 钱包安装 节点安装 数据同步 钱包使用 Windows MACOS Linux
---

* content
{:toc}

# 一键运行钱包下载

## Win64版本节点下载（含数据）

* 全节点数据的钱包：

https://pan.baidu.com/s/1E8CMxRRS6a1xGwywqQsCWg 

提取码：1xrc 

* 精简版钱包：

https://pan.baidu.com/s/1ut2hkAU-q8ELH0bS49ZShw 

提取码：qbuj

# 其他操作系统节点下载（不含数据）

https://download.bitcoinabc.org/0.15.1/

# 加速同步的办法

在配置文件中添加节点

下载后用记事本修改bitcoin.conf文件，添加以下配置

```
addnode=node55.bcc123.org:8333
addnode=node88.bcc123.org:8333
addnode=insight.bcc123.org:8333
addnode=35.201.164.75:8333
addnode=34.97.155.204:8333
addnode=101.200.202.108:8333
addnode=35.241.158.159:8333
addnode=34.94.184.251:8333
addnode=bcc55.bcc123.org:8333
addnode=138.201.248.31:8333
addnode=35.194.117.102:8333
addnode=176.9.219.119:8434
addnode=176.9.84.152:8333
addnode=144.76.40.144:8333
addnode=34.93.112.228:8333
addnode=5.9.151.109:9333
addnode=35.221.131.58:8333
addnode=34.65.206.150:8333
addnode=35.222.164.186:8333
addnode=35.189.230.92:8333
addnode=34.90.93.171:8333
addnode=35.187.252.2:8333
```

