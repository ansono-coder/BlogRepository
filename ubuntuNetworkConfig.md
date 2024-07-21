---
title : VM中的Ubuntu22如何配置静态ip
categories : 编程技术
tags : 
    - linux
    - ubuntu
---
## 配置静态ip的目的
- 只需要通过ssh连接一次，之后无需再改变ssh中ip的配置
- 服务器的ip本就不该在每次重启后改变
## 配置步骤
### 1. 用root权限进入/etc/netplan目录下
- 在终端输入以下指令
```shell
# su root
# cd /etc/netplan
```
### 2. 将原有的配置文件备份或者拷贝一份后改后缀为txt
- 操作后大概是这个样子
```shell
说明：
    - 这里的网卡配置文件后缀为.yaml
    - 网卡文件名不同没有影响
/etc/netplan# ls
01-network-manager-all.txt  01-network-manager-all.yaml
```
### 3. 使用文本编辑器打开.yaml文件
- 删除除了注释和最后一行的**version**属性外的所有文本
- 向该文件中添加以下信息
- **注意：层级一定要和下面的例子保持一致，缩进只能用空格**
```yaml
network:
  renderer: NetworkManager
  ethernets:
      ens33:   # 这里写网卡名字，不知道就用ifconfig查
          dhcp4: false   # false 表示禁止自动分配ip4
          dhcp6: false   # false 表示禁止自动分配ip6
          addresses: [192.168.1.249/24]  # /前面写当前的ip地址([]不要省略)，不知道就用ifconfig查，24表示默认子网掩码，这个不要动
          routes:  # Ubuntu22不支持指定网关，得用这个指定
              - to: default  # 这里照抄
                via: 192.168.1.1 # 注意：这里的网关要和主机的保持一致，写ip4的，具体怎么查就问度娘吧
          nameservers:  # 这里表示DNS
                    addresses: [192.168.1.1] # 一般和网关一致，反正主机上也可以查到，这个和网关的信息在一块儿，同样写iP4的
  version: 2  # 这个我也不知道，是初始的配置文件中自带的，最好别改就行了
```
### 4. 保存退出文本编辑器
### 5. 重启网络服务
- 输入以下指令
```shell
# netplan apply
```
### 6. 测试网络连接状态
```shell
# ping www.baidu.com
```