---
title : VM中的Ubuntu22如何配置ssh启动项
categories : 编程技术
tags : 
    - linux
    - ubuntu
---
## 相关操作如下：
```shell 
//开机自动启动ssh命令
sudo systemctl enable ssh
 
//关闭ssh开机自动启动命令
sudo systemctl disable ssh
 
//单次开启ssh
sudo systemctl start ssh
 
//单次关闭ssh
sudo systemctl stop ssh
 
//设置好后重启系统
reboot
 
//查看ssh是否启动，看到Active: active (running)即表示成功
sudo systemctl status ssh
 
```