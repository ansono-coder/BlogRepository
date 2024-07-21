---
title : 个人maven配置中遇到的问题和对settings.xml的一些研究(基于Linux)
categories : 编程技术
tags :
    - java
    - maven
---
## 1.maven环境变量的配置
1. 将解压好的maven文件夹放到/opt目录下（反正我放在这里，目录位置只要自己找的到就没问题）
```bash
$ mv maven/ /opt/
```
2. 编写/etc/profile文件，配置环境变量
```bash
$ vim /etc/profile
```
3. 写入内容如下
```bash
export M2_HOME=/opt/maven/(这里的路径就是maven bin目录的上级目录)
export PATH=$M2_HOME/bin:$PATH
```
4. 保存退出后，刷新环境变量或重启
```bash
$ reboot (重启)
$ source /etc/profile（刷新）
二选一即可,如果选择刷新，则每一个bash界面都得刷新一次
```
5. 测试maven是否配置成功
```bash
$ mvn -v
```
6. 出现以下信息表示配置成功
```bash
Apache Maven 3.8.6 (84538c9988a25aec085021c365c560670ad80f63)
Maven home: /opt/maven
Java version: 17.0.5, vendor: Oracle Corporation, runtime: /opt/jdk17
Default locale: en_GB, platform encoding: UTF-8
OS name: "linux", version: "6.0.12-300.fc37.x86_64", arch: "amd64", family: "unix"

```
## 2.maven的repository路径的自定义
**这里强烈建议我这个方法，避免后续在idea中无法下载插件（我卡了一个下午）**
- 修改maven/conf/settings.xml文件
```xml
<!-- localRepository
    | The path to the local repository maven will use to store artifacts.
    |
    | Default: ${user.home}/.m2/repository
    <localRepository>/path/to/local/repo</localRepository>
    -->
    <localRepository>/usr/local/repository</localRepository>
```
>创建localRepository标签，并在其中写你所要创建仓库的路径，**不要在这个路径下创建repository文件夹**
>
## 3.repository中获取初始的依赖（包括plugins）
1. 启动idea
2. 新建项目,选择如图所示的选项（如果这一块不会，就去看再学学maven）
![idea-maven项目创建](F:\resources\blog-repository\pictures\maven-config\idea-maven项目创建.png)

3. 创建成功后，他会开始下载文件到主用户目录下的.m2文件夹下
![m2文件夹位置](F:\resources\blog-repository\pictures\maven-config\m2文件夹位置.png)
4. 将这个文件夹下的repository文件夹移动到上面自定义的那个repository路径的目录下（这里就是/usr/local下）
5. 到此，初始的repository文件夹就获取完成了

## 4.maven的settings.xml的相关配置
- 配置编译环境(在profiles标签中编写)
```xml

<profile>
       <id>JDK-17</id>
       <activation>
         <activeByDefault>true</activeByDefault>
         <jdk>17</jdk>
       </activation>
       <properties>
         <maven.compiler.source>17</maven.compiler.source>
         <maven.compiler.target>17</maven.compiler.target>
         <maven.compiler.compilerVersion>17</maven.compiler.compilerVersion>
       </properties>
</profile>
```
- 配置下载依赖的镜像库（在mirrors标签中编写）
>主要是因为maven中央仓库的服务器在国外，下载速度慢，
这里选用的是[阿里云的maven镜像仓库](https://developer.aliyun.com/mvn/guide)
>
```xml
<mirror>
         <id>aliyunmaven</id>
         <mirrorOf>*</mirrorOf>
         <name>阿里云公共仓库</name>
         <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```
> 关于mirrorOf的取值和作用：[仰望星空_Star写的blog](https://blog.csdn.net/xuxiaoyinliu/article/details/105941221)
>
- **!!! mirrors当中只能配置一个生效镜像库，当有多个时，默认选择第一个**，如果要配置多个镜像库，需要在profiles中的profile的repositories标签中配置，如下:
```xml
<profile>
       <id>jdk-1.4</id>

       <activation>
         <jdk>1.4</jdk>
       </activation>
 
       <repositories>
         <repository>
           <id>jdk14</id>
           <name>Repository for JDK 1.4 builds</name>
           <url>http://www.myhost.com/maven/jdk14</url>
           <layout>default</layout>
           <snapshotPolicy>always</snapshotPolicy>
         </repository>
       </repositories>
     </profile>

<!--并编写以下标签，选择要生效的仓库-->
<activeProfiles>
     <activeProfile>alwaysActiveProfile</activeProfile>
     <activeProfile>anotherAlwaysActiveProfile</activeProfile>
</activeProfiles>

```
## 5.解决通过idea重新创建maven项目再次配置maven的问题(idea2022.x)
- 打开idea，回到起始页面，选择Customize
![idea-customizePage](F:\resources\blog-repository\pictures\maven-config\idea-customizePage.png)
- 选择 All settings,之后就按照一般配置即可

### 到此，本文结束，记录完成

