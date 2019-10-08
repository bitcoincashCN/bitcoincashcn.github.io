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
addnode=34.87.123.94:8333
addnode=34.84.45.131:8333
addnode=101.200.202.108:8333
addnode=34.94.0.183:8333
addnode=35.234.212.90:8333
addnode=35.202.234.195:8333
addnode=34.85.9.127:8333
addnode=35.185.162.139:8333
addnode=35.228.146.233:8333
addnode=34.77.105.9:8333
addnode=34.65.206.150:8333
addnode=35.241.158.159:8333
addnode=35.221.88.65:8333
addnode=34.83.163.174:8333
addnode=34.87.47.98:8333
addnode=35.230.41.68:8333
addnode=35.236.96.29:8333
addnode=35.198.113.12:8333
addnode=35.240.159.234:8333
addnode=35.240.204.74:8333
addnode=34.97.158.99:8333
addnode=35.189.230.92:8333
addnode=34.92.57.112:8333
addnode=35.199.112.215:8333
addnode=35.199.122.91:8333
addnode=35.187.252.2:8333
addnode=35.204.155.79:8333
addnode=35.240.146.6:8333
addnode=35.236.193.211:8333
addnode=35.224.65.4:8333
addnode=51.15.253.90:8333
addnode=35.242.183.145:8333
addnode=47.75.15.2:8333
addnode=176.9.84.152:8333
addnode=138.201.248.31:8333
addnode=176.9.219.119:8434
addnode=144.76.40.144:8333
```

