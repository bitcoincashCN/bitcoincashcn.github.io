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
sudo apt-get install build-essential libzmq3-dev
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

git clone https://github.com/osagga/bitcore-node-cash.git && cd bitcore-node-cash
npm install
./bin/bitcore-node start

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



