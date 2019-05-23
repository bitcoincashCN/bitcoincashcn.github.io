---
layout: post
title:  "BCH原链单机挖矿教程"
date:   2019-05-23
categories: 教程和实验
tags: BCH Clashic BCH原链 操作教程 挖矿
---

* content
{:toc}

# 一、挖矿准备

**1.下载钱包**

下载Bitcoin ABC 0.15.1全节点钱包（在BCH的钱包网站bitcoinabc.org下载），注意版本号只能是0.15.1，才对应的BCH原链。

[节点钱包下载](https://download.bitcoinabc.org/0.15.1/)

**2.同步区块数据**

大概有140GB，需要一段时间现在。稍后提供一份网盘下载快照，可直接拷贝到区块目录下。

**3.手动添加0.15.1版本的节点(可选)**

* [bitnode.earn.com](https://bitnodes.earn.com/nodes/)

这里面找到Bitcoin ABC 0.15.1的节点地址，然后在钱包控制台添加节点：

`addnode "5.9.151.109:9333" add`

`addnode "148.251.68.170:8333" add`

**4.运行getblocktemplate正常**

在控制台运行getblocktemplate，显示挖矿的信息（类似下面例子），如果不正常见【常见问题解决】部分

```
{
  "capabilities": [
    "proposal"
  ],
  "version": 536870912,
  "rules": [
    "csv"
  ],
  "vbavailable": {
  },
  "vbrequired": 0,
  "previousblockhash": "0000000001c0ed1ed2ec9eb99ef62420018d9b7935ad5f6a54b7bee730c1f66e",
  "transactions": [
  ],
  "coinbaseaux": {
    "flags": ""
  },
  "coinbasevalue": 625000000,
  "longpollid": "0000000001c0ed1ed2ec9eb99ef62420018d9b7935ad5f6a54b7bee730c1f66e2",
  "target": "0000000002289c00000000000000000000000000000000000000000000000000",
  "mintime": 1546736282,
  "mutable": [
    "time", 
    "transactions", 
    "prevblock"
  ],
  "noncerange": "00000000ffffffff",
  "sigoplimit": 160000,
  "sizelimit": 8000000,
  "curtime": 1558567160,
  "bits": "1c02289c",
  "height": 802283
}
```
# 二、单机挖矿方法

## 方法1：CPU和GPU挖矿

**1.运行RPC后台服务**

（Windows）下启动bitcoin-qt.exe用以下参数：

`bitcoin-qt.exe -server=1 -rpcuser=x -rpcpassword=x -rpcthreads=4 -rpcallowip=0.0.0.0/0 -rpcport=8332 -rpcbind=127.0.0.1`

参数说明：
-rpcuser：用户名(可以自定义）
-rpcpassword：密码（可以自定义）
-rpcport：监听端口号

启动后，用netstat -na 查看是否有127.0.0.1:8332的端口监听状态(Listening)

在Linux下运行，则将以上参数写入配置~/.bitcoin/bitcoin.conf。

**2.获得挖矿用的地址（可选）**

在控制台用getnewaddress命令获得新的地址，该地址用于挖矿。也可以用老的地址。但注意保管好地址的私钥。

**3.启动挖矿软件**

新版本的比特币客户端取消了本地挖矿功能，因此需要下载单独的挖矿软件。

[bfminer下载](http://bfgminer.org/)

经测试Win32版本可以正常运行

运行命令：

`bfgminer -o http://127.0.0.1:8332/#getcbaddr -u x -p x --no-stratum -S opencl:auto --verbose --coinbase-addr 18YRSL5naB4r3od4zqZ8RWZHXrRYdzrH8W --coinbase-sig "xxx"`

--coinbase-addr 替换为自己的地址

启动正常的话，可以看到实时算力和运算日志。

## 方法2：矿机本地单机挖矿

待续：正在寻找1台矿机完成此部分实验


# 三、常见问题解决

## 1.节点同步完成后显示未同步

节点已经同步到最新区块之后，在控制台运行getblocktemplate，假如报以下错误：

>
Bitcoin is downloading blocks... (code -10)

区块已经下载完成了，但是仍然显示在下载，是因为节点在最后一个块超过当前时间24小时之前，即使已经同步完成，也会显示未同步完成。

解决办法很简单，修改当前系统日期为最后一个块的日期，修改之后客户端很快会显示同步完成，然后再把日期改回来。


## 2.没有连接网络

在控制台运行getblocktemplate，假如报以下错误：

>
Bitcoin is not connected! (code -9)

是因为节点刚启动，还没有与其他节点连接，稍等一下即可。在控制台的网路里面可以看到节点的连接情况。

# 四、挖矿技巧

## 1.利用EDA降低挖矿难度的技巧

关于EDA的机制：

[EDA机制和运行情况报告](https://bitcoincashcn.github.io/2019/05/22/bcl-eda/)

简单来说，如果2个块出块时间间隔大于12个小时，那么从第9个块开始，难度将会自动下调，连续下降6次，每次下降20%，即难度会下降大概75%，有利于加速出块。当然，这对于链条而言是不安全，低算力挖出来的矿，未来也可能会被覆盖回滚（即所谓51%算力攻击）。


