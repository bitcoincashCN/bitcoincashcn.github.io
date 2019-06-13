---
layout: post
title:  "Ubuntu Server的BCC节点安装说明"
date:   2019-06-13
categories: 新手入门教程
tags: BCC 新手教程 新手入门 安装钱包
---

* content
{:toc}


# 1、下载解压
```
cd ~/
mkdir BCC
cd BCC
wget https://download.bitcoinabc.org/0.15.1/linux/bitcoin-abc-0.15.1-x86_64-linux-gnu.tar.gz
tar -zxvf bitcoin-abc-0.15.1-x86_64-linux-gnu.tar.gz
rm bitcoin-abc-0.15.1-x86_64-linux-gnu.tar.gz
```
# 建立bitcoin.conf 文件

```
mkdir ~/.bitcoin/
vi ~/.bitcoin/bitcoin.conf
```
文件内容如下：

```
server=1
rpcconnect=127.0.0.1
rpcuser=x
rpcpassword=x
rpcport=8332
rpcthreads=8
rpcallowip=0.0.0.0/0
rpcbind=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
usecashaddr=0
zmqpubrawtx=tcp://127.0.0.2:28332
zmqpubhashblock=tcp://127.0.0.2:28332
addnode=5.9.151.109:9333
addnode=101.200.202.108:8333
addnode=104.198.86.113:8333
addnode=35.247.161.53:8333
```
默认数据目录为~/.bitcoin，不需要修改。

# ３、设置自动启动脚本

```
cd ~/
vi auto.sh
```
脚本内容如下：
```
#! /bin/sh    
    
proc_name="bitcoind"        #进程名
    
proc_num()                      #查询进程数量
{  
    num=`ps -ef | grep $proc_name | grep -v grep | wc -l`  
    return $num
}
 
proc_num    
number=$?                       #获取进程数量
if [ $number -eq 0 ]            #如果进程数量为0
then                            #重新启动服务器，或者扩展其它内容。
    cd ~/BCC/bitcoin-abc-0.15.1/bin/; ./bitcoind  
fi
```

# ４、设置自动启动

```
touch ~/MyCrontab && nano ~/MyCrontab
```
粘贴以下一行脚本：

```
*/1 * * * * sh ~/auto.sh
```

Ctrl+x退出选择Y保存

设置该脚本自动启动

```
crontab ~/MyCrontab
```

验证
```
crontab -l
```

# 5、验证节点已经自动运行

等一分钟后验证：

```
cd ~/BCC/bitcoin-abc-0.15.1/bin/
./bitcoin-cli getinfo
```
正常显示如下，区块高度逐步增加同步：

```
{
  "version": 150100,
  "protocolversion": 70015,
  "walletversion": 130000,
  "balance": 0.00000000,
  "blocks": 3616,
  "timeoffset": 0,
  "connections": 4,
  "proxy": "",
  "difficulty": 1,
  "testnet": false,
  "keypoololdest": 1560330361,
  "keypoolsize": 100,
  "paytxfee": 0.00000000,
  "relayfee": 0.00001000,
  "errors": ""
}
```