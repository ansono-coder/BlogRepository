---
title : 安装debian服务器的一揽子问题
categories: 编程技术
tag : 
  - linux
---
## 一、安装前的准备工作
### 1. 下载镜像
- 如果是希望该系统成为自己的工作站，请下载完整版镜像
- 如果单纯只是当成一个服务器，直接下载在线安装版镜像即可
### 2. 查看当前机器网络环境
- 无论是虚拟机还是物理机，请在有网络的情况下进行安装
- 如果在没有网络的情况下安装，请在安装完成后自行配置和启动网络（详情请在浏览器中搜索）
## 二、正式安装
### 1. 基本安装步骤
- 流程网上都有，没必要重复记录
## 三、可能会遇到的问题和踩坑点（重点）
### 1.关于分区的问题
> debian中我只给了swap分区和根分区，其余都没给，并未出错
### 2.包管理器安装和配置问题（重中之重！！！）
> 注意：若在联网的情况下，在对apt进行安装后会直接让你选择镜像，这时候千万不要选择，因为debian系统的security包会走国外的debian服务器，会直接导致卡死。选择“返回”键，会提示你是否需要选择一个软件源，直接选择无需软件源，继续安装。
### 3.安装完成后发现无法update以及下载软件
> 注意：这时候我们的debian是没有软件源的，需要我们使用vi等文件编辑器去修改/etc/apt/source.list文件，手动加入软件源后update一下便可下载软件了
### 4.无法搜索到软件openssh
> debian中的openssh软件全名叫openssh-server
### 5.如何启动sshd服务并且设置开机自启动
> 使用systemctl start sshd.service去启动sshd服务，使用systemctl enable ssh去实现开机启动sshd服务
### 6.关于找不到shutdown指令和reboot指令这回事
> 使用systemctl poweroff和systemctl reboot即可
### 7.如何配置网络及静态ip
- 以root的身份进入/etc/network目录下
- cp interfaces interfaces.bak
- vim interfaces
```
# The loopback network interface
auto lo
iface lo inet loopback
 
# private setting
auto 网卡名 
iface 网卡名 inet dhcp

//这里是先获取自动ip，确保分配的ip可用
```

- systemctl restart netwoking 
- ping www.baidu.com  确保网络可行
- ip addr show 网卡名   记下ip
- vim interfaces

```
# The loopback network interface
auto lo
iface lo inet loopback

#上面不动，就是interfaces文件的默认样子，修改下面这些

# The primary network interface
auto 网卡名字
iface 网卡名字  inet static
 address 192.168.2.236     # 这里写刚才记录的ip
 netmask 255.255.255.0
 gateway 192.168.2.254     # 这里和主机网关一样
 dns-nameservers 8.8.8.8  #可配置多个
```





