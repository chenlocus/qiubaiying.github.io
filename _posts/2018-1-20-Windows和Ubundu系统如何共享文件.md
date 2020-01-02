---
layout:     post
title:      Windows和Ubundu系统如何共享文件
subtitle:   
date:       2018-1-20
author:     土猪
header-img: img/post-bg-unix-linux.jpg
catalog: true
tags:
    - ubundu
---


我在自己的windows10主机上装了个虚拟机，虚拟机上装了ubundu操作系统。前不久，我在ubundu上下载了一个50G的大文件，然后我想把这文件共享到我的windows系统上。



先转到ubundu上下载samba：

sudo apt-get install samba

![](https://steemitimages.com/DQmUt5AB68xSh2vzmvdDs5APKP3uhLr7XphNETLo5vCULpu/image.png)


再来设置用户名和密码，holroyd是我在ubundu上的账号：

smbpasswd -a holroyd

![](https://steemitimages.com/DQmaMJiEv8DPdXVVcF8aEeHYmGK6LDXnuAv3XLb39fXiQAS/image.png)


然后再来设置个文件夹，把要分享的文件拷贝过去：

mkdir ~/Desktop/Share


打开这个文件进行编辑：

/etc/samba/smb.conf

编辑结果如下：

[share] 
path = /home/holroyd/share
available = yes 
valid users = holroyd
read only = no 
browsable = yes 
public = yes 
writable = yes

保存下这个文件，执行这个命令：

sudo service smbd restart


从windows访问这个文件夹：

\\ipaddress\share fold

用这个命令看ubundu的机器ip地址：
ifconfig -a

![](https://steemitimages.com/DQmZxvbTh41XrEzeviEvY1jonnPth7K8tqgxwp2SGauvDbx/image.png)

那么我就这样访问：
\\172.23.243.218\share



更多ubundu，python，aws，数据分析的技术文章：

- [Solve a DNS issue in Ubundu VM](http://engineerman.club/2019/01/20/Solve-a-DNS-issue-in-Ubundu-VM/)
- 
  [create an instance in aws for free](http://engineerman.club/2018/11/16/create-an-instance-in-aws-for-free/)

- 
  [build a rest API service to provide market data for yourself](http://engineerman.club/2018/11/16/build-a-rest-API-service-to-provide-market-data-for-yourself/)

- 
  [Schedule regular tasks in AWS](http://engineerman.club/2018/11/16/Schedule-regular-tasks-in-AWS/)

- 
  [How to access the EC2 instance in AWS](http://engineerman.club/2018/11/16/How-to-access-the-EC2-instance-in-AWS/)

- 
  [How to extend my VM harddisk capacity in Ubundu](http://engineerman.club/2018/10/16/How-to-extend-my-VM-harddisk-capacity-in-Ubundu/)

- 
  [get historical data with python](http://engineerman.club/2018/01/22/get-historical-data-with-python/)

- 
  [To share files between Windows and Unbundu](http://engineerman.club/2018/01/20/To-share-files-between-Windows-and-Unbundu/)

- 
  [How to share fold in hosted windows system with VM ubundu](http://engineerman.club/2018/01/20/How-to-share-fold-in-hosted-windows-system-with-VM-ubundu/)

- 
  [An Alternative to Yahoo Finance API in Australian Share Market](http://engineerman.club/2018/01/18/An-Alternative-to-Yahoo-Finance-API-in-Australian-Share-Market/)

- 

- [using financial data to analyze Australian share market with help of python/pandas](http://engineerman.club/2018/01/16/using-financial-data-to-analyze-Australian-share-market-with-help-of-python/)

- 
  [How to host your Application to Heroku to build your own website](http://engineerman.club/2015/01/16/How-to-host-your-Application-to-Heroku-to-build-your-own-website/)

- [virtual machine and windows combination](http://engineerman.club/2010/01/16/virtual-machine-and-windows/)