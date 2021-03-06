---
layout: post
title:  "BCH原链单机挖矿教程"
date:   2019-05-23
categories: 教程和实验
tags: BCH Clashic BCH原链 操作教程 挖矿 BCC
---

* content
{:toc}

# 一、挖矿准备

**1.下载钱包**

下载Bitcoin ABC 0.15.1全节点钱包（在BCH的钱包网站bitcoinabc.org下载），注意版本号只能是0.15.1，才对应的BCH原链。

[节点钱包下载](https://download.bitcoinabc.org/0.15.1/)

**2.同步区块数据**

大概有140GB，需要一段时间现在。也可以从网盘下载区块数据，解压后直接拷贝到区块目录下即可。

* [百度网盘区块数据下载](https://pan.baidu.com/s/172fw2j-Kbwr_X3fhorzSKg)

* 提取码：hqsf

如果是新用户，不需要同步旧数据，可以下载修剪版（一键运行）

* [一键运行精简版下载](https://pan.baidu.com/s/1jg9H_fzw6_yIltXcyG-5TQ)
* 提取码：zxi1 
 

**3.手动添加0.15.1版本的节点(可选)**

因为现在节点不多，为了加速同步，建议手动添加若干个节点。

* [bitnode.earn.com](https://bitnodes.earn.com/nodes/)

这里面找到Bitcoin ABC 0.15.1（或者Bitcoin ABC 0.14.6）的节点地址，然后在钱包控制台添加节点：

```
addnode "5.9.151.109:9333" add
addnode "101.200.202.108:8333" add
addnode "35.247.161.53:8333" add
```

让每次启动自动添加节点，用记事本建立bitcoin.conf文件，保存到数据目录下，文件内容如下：

```
addnode=5.9.151.109:9333
addnode=101.200.202.108:8333
addnode=35.247.161.53:8333
addnode=138.201.248.31:8333
addnode=144.76.40.144:8333
addnode=176.9.84.152:8333
```

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

`bfgminer -o http://127.0.0.1:8332/#getcbaddr -u x -p x --no-stratum -S opencl:auto --verbose --generate-to 1HxpKABeYVKdKw9C175oJ5CYmA7YibDy6B --coinbase-sig "bitcoincashcn.github.io" --net-delay`

--generate-to 替换为自己的地址（不过据测试，每次钱包会自动生成新的Coinbase地址，该地址貌似设置与否没有意义）

--no-submit-stale 算力较大的时候建议加上这个参数，减少无效块拒绝。

启动正常的话，可以看到实时算力和运算日志。

## 方法2：矿机本地单机挖矿

前两步与方法1一样。

**3.启动挖矿软件**

将bfgminer命令中的“--no-stratum”参数改为“--stratum-port=3333”

`bfgminer -o http://127.0.0.1:8332/#getcbaddr -u x -p x --stratum-port=3333 -S opencl:auto --verbose --generate-to 1HxpKABeYVKdKw9C175oJ5CYmA7YibDy6B --coinbase-sig "bitcoincashcn.github.io" --net-delay --no-submit-stale`

启动成功后，在CMD命令行下可以看到：
```
C:\Users\user>netstat -na | find "3333"
  TCP    0.0.0.0:3333           0.0.0.0:0              LISTENING
```

**4.配置矿机**

如下图所示，配置矿机即可：

![图片描述](https://bitcoincashcn.github.io/pic/bfgminer.PNG)

URL设置为：***[运行bfgminer的IP地址]*:3333**

或者：**stratum+tcp://*[运行bfgminer的IP地址]*:3333**

Worker和Password均设置为x即可。

比如在本机运行：

`cgminer：cgminer.exe  -o 127.0.0.1:3333 -u x -p x`

蚂蚁U3矿机的.bat文件改为：

`cgminer.exe  --bmsc-options 115200:0.57 -o 127.0.0.1:3333 -u x -p x --bmsc-voltage 0800  --bmsc-freq 1286`

其中IP地址改为运行bfgminer的电脑IP（该电脑需要关闭防火墙）。

运行正常的话，在bfgminer窗口可以看到“PXY 0：”，即为矿机的算力：

![图片描述](https://bitcoincashcn.github.io/pic/bfgminer2.PNG)


## 方法3：小型挖矿设备单机挖矿

前3个步骤与方法2一致

**4.配置小型矿机**
**Antrouter蚂蚁路由器（型号R1-BTC）**

* ssh登录该路由器用户名root (默认密码 : root)
* vi /etc/init.d/cgminer
* 注释掉（#）PARAMS="$AOPTIONS $POOL1 $POOL2 $POOL3 --api-listen --api-network --version-file /usr/bin/compile_time --queue 10"
* 加入一行PARAMS="$AOPTIONS -o 10.3.9.227:3333 -u x -p x" （IP地址替换为运行bfgminer的电脑IP）
* 重启root@antRouter:~# reboot

修改的配置文件如下图所示：

![图片描述](https://bitcoincashcn.github.io/pic/cgminer.PNG)

## 方法4：Nicehash租用算力

* [Nicehash算力租用平台](https://www.nicehash.com)

充值BTC之后，即可用BTC购买算力挖矿，Nicehas支持BCH原链的挖矿。

* 设置BCH原链矿池

【买家页面】-【我的矿池】-添加矿池设置，【用户名】设置为自己的收币地址；设置完毕后，测试矿池连接：

![图片描述](https://bitcoincashcn.github.io/pic/nicehash01.PNG)
![图片描述](https://bitcoincashcn.github.io/pic/nicehash02.PNG)

* 矿池1：bccjp.xuexizu.cn 端口3256

* 矿池2：btcc.xuexizu.cn 端口3256

* 购买算力开挖

进入【买家页面】-【市场】，如下图所示，选择算法以及上一步设置好的矿池，算力可大可小，单价按市场行情走（价格低租不到算力，价格高产生浪费），以图中为例，每天付0.0357BTC可以获得1PH算力一天（1PH=1000T），最低每天租用0.05PH（50T算力），也就是0.0357BTC可以租用20天的50T算力）；金额部分是设置总预算，比如输入0.357BTC，那么就可以跑200天50T算力。

![图片描述](https://bitcoincashcn.github.io/pic/nicehash03.PNG)


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

## 3.队列深度超出

bfgminer如果无法启动，在浏览器检查一下：

http://127.0.0.1:8332/#getcbaddr

如显示，则为正常：

JSONRPC server handles only POST requests

如显示：

Work queue depth exceeded

则遇到了RPC队列深度超出的问题（挖矿速度太快），可以在启动bitcoin-qt（或bitcoind）加上配置参数：

`-rpcworkqueue=100`

# 四、挖矿技巧

## 1.利用EDA降低挖矿难度的技巧

关于EDA的机制：

[EDA机制和运行情况报告](https://bitcoincashcn.github.io/2019/05/22/bcl-eda/)

简单来说，如果2个块出块时间间隔大于12个小时，那么从第9个块开始，难度将会自动下调，连续下降6次，每次下降20%，即难度会下降大概75%，有利于加速出块。当然，这对于链条而言是不安全，低算力挖出来的矿，未来也可能会被覆盖回滚（即所谓51%算力攻击）。

## 2.自建矿池的作用

因为矿池一般有1%的手续费，而且奖励发放有一定的滞后性（比特币规定每挖出一个块都需要等100个块，挖出来的币才能用）。而且大量矿工聚集在少量矿池是不安全的，矿池需要分散化部署，目前测试了ckpool、unomp（开源）矿池均适用于BCC的挖矿，本站有详细安装教程。



