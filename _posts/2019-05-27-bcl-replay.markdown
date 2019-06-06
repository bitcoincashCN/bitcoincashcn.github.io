---
layout: post
title:  "BCH重放攻击的情况"
date:   2019-05-21
categories: 教程和实验
tags: BCH Clashic BCH原链 操作教程 BCC
---

* content
{:toc}

# 重放攻击的情况

BCH在2017年11月的硬分叉，新链没有改变forkid，因此BCC与BCH的交易互相重放。我们在BCC的链条上，经常可以看到BCH的交易，反之亦然。具体的结果就是，某些地址本意是支付一个BCC，但是会同时支付出一个BCH，反之亦然。在接收者不知情的情况下，有大量的BCC因此永久丢失掉了。

比如以下交易，在两条链条都可以看到,均被打包进区块里面：

```
BCH的查询结果：
BCH/bitcoin-abc-0.19.6/bin#  ./bitcoin-cli getrawtransaction df9b4c22f62129e563af3394e4b93c26a1f632357b0b44324aa1a48480ca1290 true
{
  "txid": "df9b4c22f62129e563af3394e4b93c26a1f632357b0b44324aa1a48480ca1290",
  "hash": "df9b4c22f62129e563af3394e4b93c26a1f632357b0b44324aa1a48480ca1290",
  "version": 1,
  "size": 224,
  "locktime": 0,
  "vin": [
    {
      "txid": "10370d2f2866db3aecf108c396398c9b780b5c12d3b49bdbbb81b4ad0e0bb28e",
      "vout": 112,
      "scriptSig": {
        "asm": "3045022100f0194a7a69e792ced165a418b59b0956014c605294e1e71ba30661a3f4b85f99022020950e7fa2faf2000cd604dd3321196b59fdf090e44c6430a869261016b594fc[ALL|FORKID] 04093a20d2d02d2b0d586e8fcdb176f8514761ea693229b933df4d700eb51c885b2644d2206211434fa05e79c9ed5bf5f80d661ad2fb7a4e7c3ae2a71cadacce11",
        "hex": "483045022100f0194a7a69e792ced165a418b59b0956014c605294e1e71ba30661a3f4b85f99022020950e7fa2faf2000cd604dd3321196b59fdf090e44c6430a869261016b594fc414104093a20d2d02d2b0d586e8fcdb176f8514761ea693229b933df4d700eb51c885b2644d2206211434fa05e79c9ed5bf5f80d661ad2fb7a4e7c3ae2a71cadacce11"
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 4.99999775,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 53e2a5d8f12bdf3238ade42521cdaff626c9805b OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a91453e2a5d8f12bdf3238ade42521cdaff626c9805b88ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "bitcoincash:qpf79fwc7y4a7v3c4hjz2gwd4lmzdjvqtvqesum6vd"
        ]
      }
    }
  ],
  "hex": "01000000018eb20b0eadb481bbdb9bb4d3125c0b789b8c3996c308f1ec3adb66282f0d3710700000008b483045022100f0194a7a69e792ced165a418b59b0956014c605294e1e71ba30661a3f4b85f99022020950e7fa2faf2000cd604dd3321196b59fdf090e44c6430a869261016b594fc414104093a20d2d02d2b0d586e8fcdb176f8514761ea693229b933df4d700eb51c885b2644d2206211434fa05e79c9ed5bf5f80d661ad2fb7a4e7c3ae2a71cadacce11ffffffff011f64cd1d000000001976a91453e2a5d8f12bdf3238ade42521cdaff626c9805b88ac00000000",
  "blockhash": "00000000000000000119503e59b3f60e3755b4db96fe51264be9cfa13e38b7f7",
  "confirmations": 3,
  "time": 1559796025,
  "blocktime": 1559796025
}
```

```
BCH原链的查询结果：
/BCL/bitcoin-abc-0.15.1/bin# ./bitcoin-cli getrawtransaction df9b4c22f62129e563af3394e4b93c26a1f632357b0b44324aa1a48480ca1290 true
{
  "hex": "01000000018eb20b0eadb481bbdb9bb4d3125c0b789b8c3996c308f1ec3adb66282f0d3710700000008b483045022100f0194a7a69e792ced165a418b59b0956014c605294e1e71ba30661a3f4b85f99022020950e7fa2faf2000cd604dd3321196b59fdf090e44c6430a869261016b594fc414104093a20d2d02d2b0d586e8fcdb176f8514761ea693229b933df4d700eb51c885b2644d2206211434fa05e79c9ed5bf5f80d661ad2fb7a4e7c3ae2a71cadacce11ffffffff011f64cd1d000000001976a91453e2a5d8f12bdf3238ade42521cdaff626c9805b88ac00000000",
  "txid": "df9b4c22f62129e563af3394e4b93c26a1f632357b0b44324aa1a48480ca1290",
  "hash": "df9b4c22f62129e563af3394e4b93c26a1f632357b0b44324aa1a48480ca1290",
  "size": 224,
  "version": 1,
  "locktime": 0,
  "vin": [
    {
      "txid": "10370d2f2866db3aecf108c396398c9b780b5c12d3b49bdbbb81b4ad0e0bb28e",
      "vout": 112,
      "scriptSig": {
        "asm": "3045022100f0194a7a69e792ced165a418b59b0956014c605294e1e71ba30661a3f4b85f99022020950e7fa2faf2000cd604dd3321196b59fdf090e44c6430a869261016b594fc[ALL|FORKID] 04093a20d2d02d2b0d586e8fcdb176f8514761ea693229b933df4d700eb51c885b2644d2206211434fa05e79c9ed5bf5f80d661ad2fb7a4e7c3ae2a71cadacce11",
        "hex": "483045022100f0194a7a69e792ced165a418b59b0956014c605294e1e71ba30661a3f4b85f99022020950e7fa2faf2000cd604dd3321196b59fdf090e44c6430a869261016b594fc414104093a20d2d02d2b0d586e8fcdb176f8514761ea693229b933df4d700eb51c885b2644d2206211434fa05e79c9ed5bf5f80d661ad2fb7a4e7c3ae2a71cadacce11"
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 4.99999775,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 53e2a5d8f12bdf3238ade42521cdaff626c9805b OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a91453e2a5d8f12bdf3238ade42521cdaff626c9805b88ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "18eYbh2GHiZ7FhQ2STYXWRwNBYautahxVN"
        ]
      }
    }
  ],
  "blockhash": "000000000000017f30da82672f981ea815b0fe250edf68a85f1264ccdf833c4d",
  "confirmations": 19,
  "time": 1559795924,
  "blocktime": 1559795924
}
```
同时拥有BCC和BCH的地址，需要按照以下办法进行资产分离。

# 分离BCC和BCH资产的办法

* 1、在2017年11月14号（高度504032）之前拥有BCH，那么在此之后就会获得1:1的BCC和BCH。
* 2、在BCC和BCH的区块浏览器查询地址余额，如该地址在两条链条上均有余额，则需要进行分离
* 3、在BCC节点钱包给以上地址发送一个小额BCC，或者在BCH节点钱包给以上地址发送一个小额BCH
* 4、发送完之后，这个地址在两条链条的余额就不一样了，查看浏览器确认。
* 5、在BCC节点钱包，将全部余额转给一个新的BCC地址
* 6、或者在BCH节点钱包，将全部余额转给一个新的BCH地址，分离完毕。

# 交易所没有做资产分离会导致BCC丢失

在ETC（ETH原链）刚出现的时候，有人利用ETC和ETH的重放攻击，从交易所盗取了ETC，相关的办法同样适用于BCH，方法如下（如果交易所已经做了资产分离则无效）：

* 充值BCH到交易所
* 提现
* 查询提现地址是否收到BCC（用节点钱包或浏览器），如收到将BCC分离到新地址。
* 重复以上3个步骤，

# 硬分叉的其他相关问题

## 1、硬分叉一般存在什么危险？

目前我们看到的情况是凡是硬分叉均可能会引起原链分叉，凡是软分叉都不会引起原链分叉。

## 2、硬分叉一般采取什么动作阻止被原链覆盖？

有两种常见办法：

* 修改forkid
* 代码中设置检查点

## 3、BCH和BSV为什么在分叉后没有互相覆盖？

ABC链在556767块率先打出的块中包含了DSV（OP_CHECKDATASIGVERIFY）和CTOR（规范排序）特性，那么SV链认为该区块无效，是不可能承认ABC 556767块，以及所有的后继块的。

反之，SV链在556767块打出的块包含了OP_MUL，是一个ABC不支持的操作码，那么ABC同样认为该区块无效，是不可能承认SV 556767块，以及所有的后继块的。

又如，如果SV打出一个大于32MB的区块，或者在一个脚本中加入超过201个操作码等类似操作，都能造成事实上的不兼容。

因此，任何一方只要打出对方不支持的块，那么这两条链在事实上就分道扬镳了，无论算力多还是算力少，无论区块高度领先还是落后。

这是双向的互相不兼容，而BCH原链和新链是单向的不兼容。

## 4、BCH原链的chainwork有可能超越新链？

这种可能性是存在的：

* 算力的成本随硬件成本下降而下降（比如摩尔定律）。
* BCH原链的算力一旦超过BCH新链，则chainwork反超立即进入倒计时。
* 去中心化的存活能力、抗风险、容错能力更强。
* 利益驱动。

但具体什么时候，无法逆料。放在一个很长的历史眼光去看待这件事情，去中心化的BCC的chainwork始终是存在超越BCH的可能性。


