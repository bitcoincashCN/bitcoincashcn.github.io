---
layout: post
title:  "BCH原链矿池搭建教程"
date:   2019-05-31
categories: 教程和实验
tags: BCH Clashic BCH原链 操作教程 矿池 挖矿 BCC CKPOOL
---

* content
{:toc}

BCH原链可以直接复用BTC/BCH的矿池代码库，搭建矿池作为基础设施。

# 注意事项

* 以下矿池的地址和域名替换为自己搭建的服务器地址和域名（如果没有域名，就直接用IP）
* 安装矿池的前提是服务器上已经运行了节点，并且同步完成数据。
* 矿池的出块地址和私钥，请注意保管和备份。
* 这个矿池好像不会自动分配，适合solo单挖，挖完所有币都在出块地址上。
* 另外有SPLNS版本，有更完善的分配机制，适合多矿工协作，有能力的研究一下。

# 一、矿池搭建准备

* 测试环境系Ubuntu Server 18.04.2 LTS（64Bit）
* 设置bitcoin.conf配置文件（/root/.bitcoin/bitcoin.conf），建立相关数据目录：
```
daemon=1
rpcconnect=127.0.0.1
rpcuser=x
rpcpassword=x
rpcport=8332
rpcthreads=8
rpcallowip=0.0.0.0/0
rpcbind=127.0.0.1
datadir=/home/BCL/block/
```
注意：/home/BCL/block为测试用路径，如采用其它路径，以下配置根据实际路径修改。

* 用root账号运行bitcoind进程，完成区块数据同步
* 用./bitcoin-cli getnewaddress获得矿池出块的地址

# 二、矿池的安装

## CKPOOL

### 1、安装CKPOOL

注意：/home/BCL/ckpool.conf为测试用路径，如采用其它路径，以下配置根据实际路径修改。

* 用sudo运行以下命令
```
apt-get install git   #安装git
git clone https://bitbucket.org/ckolivas/ckpool.git #源代码下载
apt-get install build-essential libpq-dev autoconf automake libtool #安装相关的库
cd ckpool
./autogen.sh
./configure
make
make install
```
* 编辑ckpool.conf文件

```
{
"btcd" :  [
        {
                "url" : "localhost:8332",
                "auth" : "x",
                "pass" : "x",
                "notify" : false
        }
],
"btcaddress" : "128sKcXJV2zAhPx2ExspTAc9UCoewBaqMH",   #替换为本地钱包地址
"btcsig" : "/mined by bitcoincashcn/",
"blockpoll" : 100,
"update_interval" : 30,
"serverurl" : "10.1.101.33:3333",   #替换为矿池外网地址
"mindiff" : 1,
"startdiff" : 128,
"logdir" : "/home/BCL/logs"
}
```
logdir设置为固定目录，以便第二步设置Web服务器。

* 启动运行CKPOOL

不加nohup则在前台运行，终端断开进程终止。

```
 nohup ckpool -A -c /home/BCL/ckpool.conf &
```

### 2、设置CKPOOL状态查询Web服务器

注意：IP地址和路径为举例，实际变更为本地地址和路径。

* 安装apache和php

```
apt install apache2
apt install php
apt-get install libapache2-mod-php
/etc/init.d/apache2 restart
```

创建test.php脚本，保存到/var/www/html目录下,脚本内容为：

```
<?php
echo "Hellow World";
?>
```

* 重启apache服务:

```
systemctl enable apache2
访问：http://10.1.101.33/test.php
```

* 设置网站

建立配置文件：/etc/apache2/sites-available/ckpool.xuexizu.cn.conf

```
<VirtualHost *:80> 
  ServerAdmin gotolab@gmail.com
  ServerName ckpool.xuexizu.cn
  ServerAlias ckpool.xuexizu.cn
  DocumentRoot /home/gotolab/logs/
  ErrorLog /home/gotolab/logs/logs/error.log 
  CustomLog /home/gotolab/logs/logs/access.log combined
 </VirtualHost>
```

* 修改/etc/apache2/apache2.conf，否则没有以上路径的访问权限。

```
<Directory />
    Options FollowSymLinks
    AllowOverride None
    Order deny,allow
    Allow from all
</Directory>
```

如果访问继续报错，需要手动指定目录权限：

```
chown -R $USER:$USER /home/gotolab/logs/
chmod -R 755 /home/gotolab/logs/
```

* 如使用域名，建立静态映射（可选）

```
/etc/hosts
10.1.101.33 ckpool.xuexizu.cn
```

* 建立目录

```
mkdir /home/BCL/logs/logs/
```

* 在/home/BCL/logs目录制作index.html

[下载静态模板](http://solo.ckpool.org/index.html)

[动态模板参考](http://ckpool.org/)

静态模板很简单，而动态模板我也没找到设置说明书。

* 启用站点

```
关闭默认站点：
a2dissite 000-default.conf
启用站点：
a2ensite ckpool.xuexizu.cn.conf
```

* 测试访问

```
http://ckpool.xuexizu.cn
```

# 三、矿工设置

* stratum+tcp://ckpool.xuexizu.cn:3333
* 用户名：1HxpKABeYVKdKw9C175oJ5CYmA7YibDy6B (改为自己接收挖矿奖励的地址）
* 密码：x（随意）

# 四、矿池的基本维护

## 查看矿池余额

```
 ./bitcoin-cli getwalletinfo
{
  "walletversion": 130000,
  "balance": 0.00000000,
  "unconfirmed_balance": 0.00000000,
  "immature_balance": 0.00000000,
  "txcount": 0,
  "keypoololdest": 1560044643,
  "keypoolsize": 100,
  "paytxfee": 0.00000000,
  "hdmasterkeyid": "61648c21139a50081c325e2821944f8f22be3dba"
}
```
immature_balance：已经出块但还未成熟的余额，比特币规定出块后100个块，铸币成熟方可使用


