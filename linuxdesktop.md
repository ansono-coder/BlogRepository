---
    title: linux中如何给idea等自安装的软件设置桌面图标
    categories: 编程技术
    tags :
        - linux
---
## 1.找到linux系统自带软件图标的位置
- 这个位置默认在/usr/share/applications/下
## 2. 创建一个.desktop文件
- 具体内容如下：
```shell
gedit /usr/share/applications/idea.desktop
[Desktop Entry]
Name=IDEA #将来图标的名字
Comment=idea开发  #鼠标指示的提示语
Exec=/home/mq/program/idea-163/bin/idea.sh   #你在bash下的启动方式
Icon=/home/mq/program/idea-163/bin/idea.png #图标的定义
Terminal=false #是否在终端运行
Type=Application #应用
Categories=Application;Development;  #application下的编程所属类别
```
## 3.给创建的文件相应的权限
- read,write,execute(rwx 641)
```shell
chmod 755 xx.desktop
```
### 到此，本文结束，记录完成