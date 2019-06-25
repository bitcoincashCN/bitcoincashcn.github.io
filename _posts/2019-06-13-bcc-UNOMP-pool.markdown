---
layout: post
title:  "BCC搭建UNOMP矿池教程"
date:   2019-06-13
categories: 教程和实验
tags: BCH Clashic BCH原链 操作教程 矿池 挖矿 BCC UNOMP
---

* content
{:toc}

BCH原链可以直接复用BTC/BCH的矿池代码库，搭建矿池作为基础设施。

# 一、矿池搭建准备

* 测试环境系Ubuntu Server 16.04 LTS（64Bit）
* 已经安装完BCC节点，并且完成区块同步
* 用./bitcoin-cli getnewaddress获得矿池出块的地址
* 需要用到2个地址， 一个用于出块，一个用于收取手续费

# 二、矿池的安装

## 安装nodejs、npm等程序
unomp的主体代码是2014、2015年开发完成的，要依赖nodeljs，而且还不能用最新的版本。
```
sudo apt-get update
sudo apt-get install software-properties-common 
sudo apt-get install npm
sudo apt-get install pyhton
sudo apt-get install build-essential libssl-dev npm nodejs nodejs-legacy
curl https://raw.githubusercontent.com/creationix/nvm/v0.16.1/install.sh | sh
source ~/.profile
```
nvm是nodejs的版本管理器，这里也比较挑剔。
```
nvm install 0.10.25
nvm use 0.10.25
```
现在可以下载并安装unomp了。
```
git clone https://github.com/UNOMP/unified-node-open-mining-portal.git unomp
cd unomp
npm update
```

## 安装redis
为了保证矿池的访问效率，unomp内部也使用了redis内存键值数据库，参考文档：
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-redis
```
sudo apt-get update
sudo apt-get install build-essential
sudo apt-get install tcl8.5
wget http://download.redis.io/releases/redis-stable.tar.gz
tar xzf redis-stable.tar.gz
cd redis-stable
sudo make
sudo make test
sudo make install
cd utils
sudo ./install_server.sh
```

make test时可能会有一点报错（不影响后继安装）：
```
*** [err]: Active defrag in tests/unit/memefficiency.tcl
defrag didn't stop.
Cleanup: may take some time... OK
Makefile:262: recipe for target 'test' failed
make[1]: *** [test] Error 1
make[1]: Leaving directory '/home/gotolab/unomp/redis-stable/src'
Makefile:6: recipe for target 'test' failed
make: *** [test] Error 2
```

make test结束正常的提示是：

```
\o/ All tests passed without errors!
```


用下面两条命令启动和停止redis服务（现在启动就行了）：
```
sudo service redis_6379 start
sudo service redis_6379 stop
```
用下面命令将redis设置为开机自动启动：
```
sudo update-rc.d redis_6379 defaults
```
## 配置文件

### 修改config.json
上面就完成了整个安装过程，为了将程序跑起来，需要修改一些配置文件。首先在unomp文件夹下找到config.json.example文件，复制为config.json。
```
cp config.json.example config.json
```
config.json里有许多配置选项，不明白的保持默认值，我修改的地方用##标注。
```
    "logLevel": "debug",
    "logColors": true,

    "cliPort": 17117,

    "clustering": {
        "enabled": true,
        "forks": "auto"
    },

    "defaultPoolConfigs": {
        "blockRefreshInterval": 1000,
        "jobRebroadcastTimeout": 55,
        "connectionTimeout": 600,
        "emitInvalidBlockHashes": false,
        "validateWorkerUsername": true,
        "tcpProxyProtocol": false,
        "banning": {
            "enabled": true,
            "time": 600,
            "invalidPercent": 50,
            "checkThreshold": 500,
            "purgeInterval": 300
        },
        "redis": {
            "host": "127.0.0.1",
            "port": 6379,
            "db": 0,
            "password": ""  ## 正式运行的矿池需要设置redis的密码
        }
    },

    "website": {
        "enabled": true,
        "host": "0.0.0.0",
        "siteTitle": "UNOMP Beta by shenlb", ## 网站的标题
        "port": 8000, ## 改成自己喜欢的端口，放开云服务相应端口，不建议用80端口，容易跟其他服务冲突
        "stratumHost": "pool.unomp.org", ## 改成自己的域名，无域名直接用IP
        "stats": {
            "updateInterval": 60,
            "historicalRetention": 43200,
            "hashrateWindow": 300,
            "graphColors": ["#058DC7", "#50B432", "#ED561B", "#DDDF00", "#24CBE5", "#64E572",
            "#FF9655", "#FFF263", "#6AF9C4"]
        },
        "adminCenter": {
            "enabled": true,
            "password": "password" ## 修改密码
        }
    },

    "redis": {
        "host": "127.0.0.1",
        "port": 6379,
        "db": 0,
        "password": ""
    },

    "switching": {
        "switch1": {
            "enabled": false,
            "algorithm": "sha256", ## 这是比特币工作量证明的算法
            "ports": {
                "3333": {
                    "diff": 10,  ## 初始难度
                    "varDiff": {
                        "minDiff": 16,
                        "maxDiff": 512,
                        "targetTime": 15,
                        "retargetTime": 90,
                        "variancePercent": 30
                    }
                }
            }
        },
        "switch2": {
            ... ## 其它的挖矿算法，这里略过
        },
        "switch3": {
            ... ## 其它的挖矿算法，这里略过
            }
        }
    },

    "profitSwitch": {
        "enabled": false,
        "updateInterval": 600,
        "depth": 0.90,
        "usePoloniex": true,
        "useBittrex": true
    }
```
## 建立bcc.json

```
cd ~/unomp/coins
vi bcc.json
```

文件粘贴以下内容即可：

```
{
    "name": "BitcoinCashBCC",
    "symbol": "BCC",
    "algorithm": "sha256"
}
```

## BCC矿池设置

在pool_configs文件夹下还需要编辑好一个相应币种的json，新建一个文件名bcc.json。

地址是要改成自己矿池服务器生成的地址（并注意备份好私钥）：

```
{
    "enabled": true,
    "coin": "bcc.json",

    "auxes": [ ], 
    "address": "13y2PJGyYJLUU2TsPMZoxHcXT6iSLtFuhZ",  ## 挖出来的币先由这个地址保存，再按贡献度派发给矿工

    "rewardRecipients": {
         "19uNEdfLYZmnLXQktRj6wBPCapqtdCu3dw" : 1.0  ## 将1%的币发给这个地址，作为矿池手续费收入
    },

    "paymentProcessing": {
        "enabled": true,
        "paymentInterval": 600,
        "minimumPayment": 0.01,
        "daemon": {
            "host": "127.0.0.1",
            "port": 8332,
            "user": "x",   ## 与bitcoind的rpcuser和rpcpassword相对应
            "password": "x"
        }
    },

    "ports": {
        "3032": {
            "diff": 8
        },
        "3008": {
            "diff": 64,
            "varDiff": {
                "minDiff": 8,
                "maxDiff": 512,
                "targetTime": 15,
                "retargetTime": 90,
                "variancePercent": 30
            }
        },
        "3256": {
            "diff": 4123456
        }
    },

    "daemons": [
        {
            "host": "127.0.0.1",
            "port": 8332,
            "user": "x",  ## 与bitcoind的rpcuser和rpcpassword相对应
            "password": "x"
        }
    ],

    "p2p": {
        "enabled": false,
        "host": "127.0.0.1",
        "port": 19333,
        "disableTransactions": false
    },

    "mposMode": {       ## mpos我没有配置
        "enabled": false,
        "host": "127.0.0.1",
        "port": 3306,
        "user": "me",
        "password": "mypass",
        "database": "ltc",
        "checkPassword": false,
        "autoCreateWorker": false
    }
}


```
启动矿池
如果配置无误，现在可以正常启动了。
```
nvm use 0.10.25
node init.js
```
然后在浏览器 访问服务器的8000端口，一个矿池就搭建完成了。

用非root账号安装，导致启动失败，可以试下运行以下命令：

```
nvm use 0.10.25
npm rebuild
npm update
node init.js
```

## 后台运行矿池服务

如果服务器重启，矿池不会自动启动，按以下步骤运行矿池

* 第一步先检查bitcoind是否启动，检查区块同步

* 第二步：

```
source ~/.profile
```

* 第三步：后台运行矿池

```
nvm use 0.10.25
nohup node init.js >/dev/null 2>&1 &
```


# 三、矿工设置

* stratum+tcp://【矿池地址】:3256
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


