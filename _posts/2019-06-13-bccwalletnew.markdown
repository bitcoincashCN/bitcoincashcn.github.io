---
layout: post
title:  "BCC钱包安装使用说明"
date:   2019-06-13
categories: 新手入门教程
tags: BCC 教程 新手入门 钱包安装 节点安装 数据同步 钱包使用 Windows MACOS Linux
---

* content
{:toc}

# Windows版本钱包安装
## 1.下载钱包

下载Bitcoin ABC 0.15.1全节点钱包（在BCH的钱包网站bitcoinabc.org下载），注意版本号只能是0.15.1，才对应的BCH原链。

[节点钱包下载链接](https://download.bitcoinabc.org/0.15.1/)

打开链接点击与自己电脑操作系统对应的版本，以WINDOWS系统为例，32位选择1下载，64位选择2下载
 

## 2.安装钱包程序

### 2.1 无需安装的版本

在一个可用空间超过160G的磁盘下创建一个钱包文件夹【BCC原链】。将下载的压缩包复制到【BCC原链】文件夹下，右击压缩包解压缩到当前文件夹下。在【BCC原链】下新建文件夹【BCC钱包】。

![图片描述](https://bitcoincashcn.github.io/pic/wallet01.png)


双击【bitcoin-abc-0.15.1】文件夹找到【bin】文件夹，双击打开找到安装程序【bitcoin-qt.exe】，右击发送到桌面快捷方式。

![图片描述](https://bitcoincashcn.github.io/pic/wallet02.png)
 
双击【bitcoin-qt.exe】打开，选择【使用自定义的数据目录】，这里路径选择刚才创建的文件夹【BCC钱包】
 安装完成后双击前面创建的桌面快捷方式打开钱包，加载完成后显示区块更新页面时关闭钱包。
 
![图片描述](https://bitcoincashcn.github.io/pic/wallet03.png)


## 3.下载数据

### 3.1 从网盘下载区块数据

解压后把【blocks】和【chainstate】两个文件夹复制到【BCC钱包】文件夹下复制和替换原来的两个文件夹。

* [百度网盘区块数据下载](https://pan.baidu.com/s/172fw2j-Kbwr_X3fhorzSKg)
* 提取码：hqsf

![图片描述](https://bitcoincashcn.github.io/pic/wallet04.png)
![图片描述](https://bitcoincashcn.github.io/pic/wallet05.png)

### 3.2下载配置文件【bitcoin.conf】

下载后直接复制到【BCC钱包】文件夹下。

* [百度网盘配置文件下载](https://pan.baidu.com/s/1o1eFnOTvVaABuvNyCUgsdQ)
* 提取码：hrhw


## 4.更新区块数据。

双击桌面快捷方式【bitcoin-qt.exe】打开钱包同步区块数据，请耐心等待，同步完成后钱包右下角显示√符号。
恭喜你，你的BCC原链钱包可以使用啦！
![图片描述](https://bitcoincashcn.github.io/pic/wallet06.png)

## 5.加密钱包。

打开钱包-设置-加密钱包，设置钱包密码。注意，设置完成后务必要记住此密码，否则丢失此密码会失去此钱包内的所有加密货币。（建议将密码写在一个纸质笔记本上）
![图片描述](https://bitcoincashcn.github.io/pic/wallet07.png)

## 6.找到自己的收款地址。

打开钱包-文件-正在接收地址
![图片描述](https://bitcoincashcn.github.io/pic/wallet08.png)

选中该地址，点击左下方的复制，即可发送给付款方接收加密货币

## 7.备份钱包。

把【BCC钱包】文件夹下的wallet.dat文件复制到一个或两个U盘中离线保存。

![图片描述](https://bitcoincashcn.github.io/pic/wallet09.png)

