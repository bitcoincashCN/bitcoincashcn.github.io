---
layout: post
title:  "BCH重放攻击的情况"
date:   2019-05-21
categories: 教程和实验
tags: BCH Clashic BCH原链 操作教程
---

* content
{:toc}

# 重放攻击的情况





# BCH与BCH原链的重放攻击





# BCH与BSV的重放攻击



# 重放攻击分离的办法

查看分叉之前的ETH地址0xf99e4ab401945fe7b249d500cb56219e803cf79e的余额

在ETH 和ETC的浏览器上查询ETH和ETC余额
ETC:  http://gastracker.io/addr/0xf99e4ab401945fe7b249d500cb56219e803cf79e
ETH: http://etherscan.io/address/0xf99e4ab401945fe7b249d500cb56219e803cf79e

如果这地址保存的全部ETH都是分叉之前的，那么可以看到新的ETH和ETC余额都是一样的。

1， 往待分离的ETH地址发送一点分叉之后（纯的）的ETH, 比如0.1ETH。
如何获取分叉之后的ETH？简单的办法可以从从交易所发送一点ETH到这个地址。当前的交易所基本都只会发送“纯”的ETH，如果发送的是分叉之前的ETH，那就是买一送一，赚了。

2，发送完毕之后，分别查询ETH和ETC余额。
正确无误的话，会发现新ETH余额是原有的余额加上新发送的ETH。
ETC余额保持原有的余额不变。

3, 创建一个新的ETH地址0xf99e4ab401945fe7b249d500cb56219e803cf79e
这个ETH地址是示例地址，实际操作中，请使用自己的地址。

4, 从ETH 和ETC 的混合地址发送0.1 ETH到第三步创建的ETH地址 0xf99e4ab401945fe7b249d500cb56219e803cf79e。

分别到ETH 和ETC 浏览器上查看各自的余额是否正确，符合预期。
5, 将ETH的余额全部发送到新的ETH地址0xf99e4ab401945fe7b249d500cb56219e803cf79e。
第四和第五步可以一次做完。这里分两步的原因是为了先使用少量ETH验证一下。

6, 做完以上5步，到这里旧地址留下余额全部是ETC。

7, 第7步并不是必须的，建议执行。 创建一个新的ETC地址, 选择把剩下的ETC全部转移到一个新的ETC地址。

8,分离完毕。







