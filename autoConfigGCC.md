---
title : 适合所有Linux环境的gcc/g++自定义版本安装
categories : 编程技术
tag :
    - c/c++
    - 环境配置
---
## 选择自定义配置的原因
- 使用linux自带的包管理器下载安装的gcc版本我们不能控制
- 如果使用的linux发行版比较老旧，软件仓库维护不够，通过自带的包管理器下载的gcc版本就会很低，对c/c++新特性不能很好的支持
## 特别说明：
- **这种方式安装需要以小时为单位的配置时间，请选择合理的时间操作**
## 如何配置
### 1.去官网下载gcc的源码压缩包
- [GCC官网](https://gcc.gnu.org/)
- 点击相应的版本，进入说明页面
- 找到To obtain GCC please use **our mirror sites** or **our version control system**.
- 点击**our mirror sites**，在列出的一大堆国家镜像地址中选择离本国最近的镜像地址
- 点击后选择**release**
- 在**release**中点击需要的版本
- 下载后缀为**tar.gz**的文件
### 2.编译前的配置工作
- 确保当前linux环境中**存在**gcc，版本无所谓
- 确保当前linux环境中有make指令，没有就通过自带的包管理器下载
- 进入root权限
- 将下载的gcc源码文件解压
- 进入解压好后的目录，输入以下指令
```shell
# ./contrib/download_prerequisites
```
- 保证其确实是将 gmp、mpfr、mpc 等依赖包成功下载下来，才能继续执行下面的安装步骤
- 创建一个保存gcc编译文件的目录，通常取名为“gccx.x.x-build”，路径最好和gcc源码压缩包解压后的目录同级
- 进入该目录，输入下面的指令
```shell
// 说明：
       - ../gcc-12.2.0/configure  这里的意思是使用gcc源码压缩包解压后的目录中的configure程序，所以这里的路径一定要让linux能找到configure程序
       - gcc-12.2.0 这里改成上面gcc源码压缩包解压后的目录名字；
       - /usr/local/gcc-12.2.0 这里改成上面gcc源码压缩包解压后的目录存放位置
       - c,c++ 这里意思是需要那些编译器，默认写这两个，如果只想用c编译器，就去掉c++
       - 除了说明中提到的这些地方，其他地方不需要修改，注意空格
# ../gcc-12.2.0/configure  -prefix=/usr/local/gcc-12.2.0 --enable-checking=release --enable-languages=c,c++ --disable-multilib
```
### 3. 开始编译GCC
- 执行后，原本空的目录会多出一些东西，保持在这个目录不要移动，并执行以下指令
```shell
// 说明：
      - 这里的2指的是当前linux运行的核数，如果是4核服务器，这里就写4
# make -j2 
```
- 接下来，这里会等待几个小时...
- 执行成功后，输入以下指令
```shell
# make install
```
- 再等待一段时间...
- 编译完成
### 4. 检测安装结果
- 进入gcc源码解压后的目录，找到bin文件夹
- 进入bin文件夹执行以下指令
```shell
# gcc --version
# g++ --version
```
- 如果显示出版本信息，表示安装成功
### 5. 配置环境变量
- 使用文本编辑器，如vim等打开**etc/profile**文件
``` shell
# vim /etc/profile
```
- 在文件的末尾添加如下信息
```profile
// 说明：
      - 这里的GCC_HOME就配置gcc源码包解压后的目录，注意，这里不需要指定到bin目录
export GCC_HOME=/usr/local/lib/gcc-x.x.x/
export PATH=$GCC_HOME/bin:$PATH
```
- 保存退出
- 在终端中执行以下指令
```shell
//这只是当前窗口刷新环境变量，全局刷新需要重启
# source /etc/profile
```
## 相关说明
- 如果在上述操作中遇到任何问题导致编译中断，**不要删除整个build文件夹中的内容**
- 根据提示完成相关依赖或者软件的安装，再继续执行上述的**make**指令即可
