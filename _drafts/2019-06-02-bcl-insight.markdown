---
layout: post
title:  "BCH原链安装Insight浏览器教程"
date:   2019-06-02
categories: 教程和实验
tags: BCH Clashic BCH原链 操作教程 区块浏览器
---

* content
{:toc}

# 一、NodeJS快速安装

环境设定：ubuntu 18.04 LTS Server (64bit）

## 安装NPM

```
sudo apt install npm
```

## 安装nvm

执行：
```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```

再执行：
```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
```
执行完输入nvm查看是否安装完成。


## 安装nodejs

需要安装V4版本：

```
nvm install v4.*
nvm use 4
```

# 二、安装Insight浏览器

## 1、下载bitcore-node版本库

`git clone https://github.com/satoshilabs/bitcore-node`

进入 bitcore-node 目录，打开 package.json ，将L34 "preinstall": "./scripts/download" , 删除;


## 2、安装依赖

进入 bitcore-node 目录，运行 npm i 安装依赖，得到完整的 bitcore-node 工程；此时得到文件 bitcore-node/bin/bitcore-node；

## 3、复制bitcoind文件

下载bitcoin-abc 0.15.1的钱包，将bitcoind 复制进 bitcore-node 目录，需要保证 bitcore-node/bin 下有 bitcoind 文件；


## 4、建立节点

运行 ./bitcore-node create mynode ；生成 mynode 目录(该步骤中产生的任何错误提示可以忽略)；

进入 mynode 目录，打开 package.json 并将依赖中的 bitcore-node 删除；运行 npm i ，安装依赖；

## 5、安装insight-api和insight-ui

运行以下命令安装依赖: 

```
../bitcore-node install https://github.com/satoshilabs/insight-api
```

以及

```
../bitcore-node install insight-ui
```

## 6、设置只允许运行一个bitcore-lib实例

检查 bitcore-node/bin/mynode/node_modules/insight-api/node_modules/bitcore-lib/index.js及 bitcore-node/bin/mynode/node_modules/bitcore-message/node_modules/bitcore-lib/index.js 两个文件，如果有，则在第七行加上 return;（只允许一个bitcore-lib实例）;修改效果如下：

```
bitcore.versionGuard = function(version) {  return;
```
## 7、启动工程

回到 mynode 目录，运行以下命令启动工程：

```
../bitcore-node start
```

后台运行：

```
nohup ../bitcore-node start &
```

服务器重启后，如果直接用root帐号运行报错，用户账号登录后运行sudo -s，再启动节点则正常。

## 8、配置文件说明

* 默认区块文件保存在mynode/data/

* 节点配置文件mynode/bitcore-node.json

```
datadir: 区块文件保存目录
port: 服务端口，默认
```
* 区块配置文件mynode/data/bitcoin.conf

可以增加addnode配置，增加连接节点（因为目前Bitcoin ABC 0.15.1节点较少），直接连接到已经同步完成的节点，加速同步。

# 三、测试运行

* http://[服务器IP]:3001/insight/

区块还没同步完成的情况：

![](https://bitcoincashcn.github.io/pic/insight.PNG)

# 附件、安装Insight浏览器（第二种办法）

bitcore安装不支持root帐号，如果出现权限报错，使用以下命令设置目录权限：

```
Execute as a ubuntu user (non-root)
For each directory, change user from root to ubuntu
$ sudo chown ubuntu:staff <directoryName>
```
实际我用的命令是：

```
sudo chmod -R 777 /usr/local/
```


## 0、安装NPM

```
sudo apt install npm
```

## 安装nodejs

```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```

再执行：
```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
```

执行完输入nvm查看是否安装完成。
```
nvm install 0.12.18
```
## 1、安装bitcore

```
npm install -g bitcore
```

## 2、建立3个配置文件

在~/.bitcoin目录建立以下三个配置文件

```
cd ~/
mkdir .bitcoin
cd .bitcoin
vim bitcoin.conf
```
* bitcoin.conf文件 
```
server=1
testnet=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:28332
zmqpubhashblock=tcp://127.0.0.1:28332
rpcallowip=127.0.0.1
uacomment=bitcore
rpcuser=bitcoin
rpcpassword=local321
```

* bitcore-node.json文件

```
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "bitcoind",
    "insight-api",
    "insight-ui",
    "web"
  ],
  "servicesConfig": {
    "bitcoind": {
      "spawn": {
        "datadir": "~/.bitcoin",
        "exec": "~/.bitcoin/bitcoind"
      }
    }
  }
}
```

* package.json文件

```
{
  "description": "A full Bitcoin node build with Bitcore",
  "repository": "https://github.com/user/project",
  "license": "MIT",
  "readme": "README.md",
  "dependencies": {
    "bitcore-lib": "^v0.13.19",
    "bitcore-node": "^3.1.3",
    "insight-api": "^0.4.3",
    "insight-ui": "^0.4.0"
  }
}
```

## 3、拷贝bitcoind

拷贝编译好的bitcoind运行文件到~/.bitcoin目录

## 4、安装

在~/.bitcoin目录运行

```
npm install
```

## 5、启动进程

```
bitcored
```

## 6、测试运行

* http://[服务器IP]:3001/insight/

