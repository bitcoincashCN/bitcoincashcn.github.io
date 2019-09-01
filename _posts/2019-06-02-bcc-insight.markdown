---
layout: post
title:  "搭建BCC轻钱包服务教程（前后端）"
date:   2019-06-02
categories: 教程和实验
tags: BCH Clashic BCH原链 操作教程 区块浏览器 BCC Insight BWS bitcore
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

经测试安装bitcore使用V6版本可以通过，而安装bitcore wallet service（钱包服务）使用V10版本可以通过。应用有一个版本同时支持这2个模块，我没有测试。

```
nvm install v6.17.1
nvm install v10.16.0
```

# 二、编译Bitcoin ABC 0.14.6钱包版本

需要安装支持bitcore的Bitcoin ABC 0.14.6版本（这是一个特殊版本，包含支持bitcore的RPC命令，直接下载0.14.6发行版本会出现不兼容问题）

## 2.1 安装第三方库:

* 1.安装libssl, libevent, libboost库等：

```
sudo apt-get update
sudo apt-get install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils
sudo apt-get install libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev
```

* 2.通过添加仓库安装BerkeleyDB：

```
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:bitcoin/bitcoin
sudo apt-get update
sudo apt-get install libdb4.8-dev libdb4.8++-dev
```

* 3.安装UPnP库:

```
sudo apt-get install libminiupnpc-dev
```

* 4.安装ZMQ库

```
sudo apt-get install libzmq3-dev
```

* 5. 安装QT5库

```
sudo apt-get install libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler
```

* 6. 安装二维码库

```
sudo apt-get install libqrencode-dev
```

## 2.2 编译安装bitcoin ABC 0.14.6（BCC）

```
git clone -b tag0.14.6-bitcore https://github.com/bitprim/bitcoin-abc.git
cd bitcoin-abc
./autogen.sh
./configure
sudo make
sudo make install
```

# 三、安装bitcore（Insight区块浏览器）

## 1. 安装bitcore

```
nvm use 6
npm install -g bitcore
bitcore create mynode-abc && cd mynode-abc
bitcore install insight-api insight-ui
```
## 2. 配置文件参考

```
bitcoincashcn@bitcore-node:~/mynode-bcc$ more bitcore-node.json 
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
         "insight-ui": {
          "routePrefix": "",
          "apiPrefix": "api"
      },
      "insight-api": {
          "routePrefix": "api",
          "disableRateLimiter": true
      },
         
          "bitcoind": {
      "spawn": {
        "datadir": "/home/bitcoincashcn/.bitcoin",
        "exec": "/usr/local/bin/bitcoind"
      }
    }
  }
}
```

## 3. bitcoin.conf配置文件

```
server=1
rpcuser=x
rpcpassword=x
rpcport=8332
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
addnode=104.198.86.113:8333
addnode=35.247.161.53:8333
```
## 4. 启动bitcore

```
nvm use 6
cd ~/mynode-abc
bitcore start
```

## 5.后台运行bitcore

如果服务器重启，可用以下命令在后台运行bitcore
```
nvm use 6
cd ~/mynode-abc
nohup bitcore start >/dev/null 2>&1 &
```

## 6. 测试运行

* http://[服务器IP]:3001/insight/

区块还没同步完成的情况：

![](https://bitcoincashcn.github.io/pic/insight.PNG)

区块同步完成的情况：

![](https://bitcoincashcn.github.io/pic/insight01.PNG)

## 7.启用SSL（HTTPS）

* 安装证书服务

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot python-certbot-nginx
sudo certbot --nginx
```

*nginx启动反向代理

```
vi /etc/nginx/sites-enabled/default
```

注释掉原来404那一行，添加配置如下：

```
        location / {
        #try_files $uri $uri/ =404;
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        }
```

*重启nginx

```
/etc/init.d/nginx restart
```

# 四、安装BWS（钱包服务）

## 1.安装数据库

```
sudo apt-get install mongodb
```

## 2.安装BWS
```
nvm use v10.16.0
git clone https://github.com/bitpay/bitcore-wallet-service.git
cd bitcore-wallet-service
npm install
```
如果提示需要运行以下命令修复错误，则运行一次：
```
npm audit fix
```

## 3.配置文件

config.js配置文件内容如下，其中注意地址格式为legacy、provider为insight，而url则为insight的连接：

```
var config = {
  basePath: '/bws/api',
  disableLogs: false,
  port: 3232,
  storageOpts: {
    mongoDb: {
      uri: 'mongodb://localhost:27017/bws',
    },
  },
  lockOpts: {
    //  To use locker-server, uncomment this:
    lockerServer: {
      host: 'localhost',
      port: 3231,
    },
  },
  messageBrokerOpts: {
    //  To use message broker server, uncomment this:
    messageBrokerServer: {
      url: 'http://localhost:3380',
    },
  },
  blockchainExplorerOpts: {
    bch: {
      livenet: {
        addressFormat: 'legacy',  // copay, cashaddr, or legacy
        provider: 'insight',
        url: 'http://insight.xuexizu.cn:3001',
      },
    },
  },
};
module.exports = config;
```

BWS和bitcore insight区块浏览器可以分别安装在2台不同服务器上。


## 4.启动BWS

```
npm start
```

# 五、测试轻钱包Copay

注意：

1. 注意备份钱包，并将备份保存多份。
2. 全节点钱包是最安全的，大额资金请转账到全节点钱包里的地址保存。
3. 钱包全部密钥信息均保存在本地，如丢失备份无法恢复。
4. 不是每个版本都可以用，不要更新到新版本。

## 1、下载安装

目前已经测试能用的版本包括Copay V4.8.1以下的版本。

* 下载地址：https://github.com/bitpay/copay/releases
* 本说明测试使用的是V3.8.2版本：https://github.com/bitpay/copay/releases/tag/v3.8.2
* Windows版本下载链接：https://github.com/bitpay/copay/releases/download/v3.8.2/Copay.exe
* 安卓APK下载（4.8版本以下）：https://apkpure.co/copay-bitcoin-wallet/


## 2、钱包设置BCH（BCC）支持

![](https://bitcoincashcn.github.io/pic/copay01.png)

## 3、删掉自带的BTC钱包

在创建新钱包之前，删掉启动时创建的BTC钱包，以免混淆。

## 4、增加BCC钱包


新建钱包的币种选择“BCH”
（必须的）钱包服务地址填写（以下两个任何一个）：

1. 地址：http://go.xuexizu.cn:3232/bws/api
2. 地址：http://pay.xuexizu.cn:3232/bws/api

![](https://bitcoincashcn.github.io/pic/copay02.png)
![](https://bitcoincashcn.github.io/pic/copay03.png)
![](https://bitcoincashcn.github.io/pic/copay04.png)

可以自行设置密码和保护短语（后面备份时再设置亦可），然后点击创建钱包即可。钱包创建完成后，进入设置备份。

![](https://bitcoincashcn.github.io/pic/copay05.png)

## 5、BCC钱包使用

Copay钱包的地址格式并非传统格式的地址，需要转换，复制钱包的接收地址到以下网址即可进行转换显示为传统地址：

https://explorer.viabtc.com/bch/tool/address-converter

![](https://bitcoincashcn.github.io/pic/copay06.png)
![](https://bitcoincashcn.github.io/pic/copay07.png)

注意：

1. 对外接收、挖矿、浏览器查询都需要用传统格式的地址。
2. 对外发币，则需要将接收方传统格式的地址转换为Copay格式（格式错了发不出去）。
3. 这三种格式的地址，本质上是同一个地址，只是软件使用不同的编码方式。
4. 较高版本的默认用cashaddr格式，同理，对外发币时，需要将目标转换为cashaddr格式再发送。

发送成功如下所示：

![](https://bitcoincashcn.github.io/pic/copay08.png)



## 6、钱包备份

Copay除了用助记词备份钱包之外，还可以用导出钱包的方式进行备份，导出钱包需要设置密码，未来恢复钱包时需要输入这里设置的密码（否则恢复不了）。

![](https://bitcoincashcn.github.io/pic/copay09.png)

点击下载会弹出保存窗口：

![](https://bitcoincashcn.github.io/pic/copay10.png) 

## 7、钱包恢复

短语恢复容易搞错，因此建议使用文本备份即恢复方式。

* 短语恢复：按顺序打字输入12个字，每个字中间要有1个空格，即可恢复
* 文件/文本恢复：直接选择备份文件，输入密码即可恢复

恢复后要注意查看钱包服务地址，原来用哪个地址仍然用哪个地址。

私钥完全保存在备份文件，跟服务器没有关系，因此，即使全球服务器均关闭，通过备份文件（只要自己记得加密密码），仍然可以还原出钱包的私钥，还原出私钥。

## 8、用备份文件还原私钥的具体办法：

1、	用记事本打开备份的json文件
![](https://bitcoincashcn.github.io/pic/copay11.png) 

2、	用解码工具，将以上乱码粘贴到”Ciphertext”框里，输入备份时设置的密码，即可解码
测试用的解码工具是：https://bitwiseshiftleft.github.io/sjcl/demo/

![](https://bitcoincashcn.github.io/pic/copay12.png) 

3、解码后的文本里面的xPrivkey就是私钥
![](https://bitcoincashcn.github.io/pic/copay13.png) 
4、直接用base58解码，Linux命令如下：

```
$printf "xprv9s21ZrQH143K2osyrqDAxXvkwcEEuqn5AHASSksH5GFKTnazytoCtKr5yC8uJV51yro7qgqEFkzuSA7UenX1Z19PmLRv7SqewNu2Dkgw66T" | base58 -dc | xxd -p 
```

得到这串数值，最后的64位（32字节）就是私钥：

0488ade40000000000000000004b5ed010550e5a6735cb4241c7e36cc5ad361954defc6b742025bbed48fc93b4008cc0e2fe5c2e19be43a37e038452298d3307ae7770d64e8e91d1a3a124823965

对这串私钥进行一顿操作：
```
$ priv="cc0e2fe5c2e19be43a37e038452298d3307ae7770d64e8e91d1a3a124823965"
$ pub_suffix=01
$ mainnet_prefix=80
$ ext_priv_mainnet=$mainnet_prefix$priv
$ wif_enc_mainnet_compressed=$(printf "$ext_priv_mainnet$pub_suffix" | xxd -r -p | base58 -c)
$ echo "WIF Mainnet (Compressed): "$wif_enc_mainnet_compressed
```
得到私钥如下：
```
WIF Mainnet (Compressed): 5KN9uLxJJ7fSCszee8e7pBU5VzRV8EAQhCQC9FYsBR915MbZKSu
```
在钱包节点的控制台，使用Importorivkey导入以上私钥，即可以实现恢复。

以上测试只是说明私钥的恢复不依赖于任何人和服务器，实际中的操作建议类似这些工具可以下载到本地运行，断网操作，还原出私钥后，将私钥文本拷贝到U盘，在另外一台联网电脑上恢复，恢复成功后，将私钥存的币转到新地址。





