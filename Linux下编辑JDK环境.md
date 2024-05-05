# Linux下编辑JDK环境

## 1、查看已有的jdk环境

```shell
rpm -qa | grep java/jdk
```

## 2、首先要获取 root权限

```shell
su root
```

## 3、删除相关的所有java和jdk环境

```shell
rpm -e --nodeps 环境名称
```

## 4、安装新的jdk环境

###   4.1、首先下载jdk安装包

```shell
wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.rpm 
```

### 	4.2、通过rpm命令安装jdk

```shell
将通过上一步下载好的jdk的rpm执行
首先赋予这个文件相应的权限
chmod 755 对应jdk的rpm文件
rpm -ivh 对应的jdk文件
如果报错可能是没有root权限,需要切换权限
```

### 	4.3、编辑文件

```shell
jdk应该会默认安装在usr/java 这个目录下
vim etc/profile
在文件末尾添加如下内容
export JAVA_HOME=/usr/local/jdk1.8.0_131  #jdk安装目录
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
export PATH=$PATH:${JAVA_PATH}
```

### 	4.4、重启profile文件

```shell
source /etc/profile
```

### 	4.5 查看是否安装成功

```shell
java -version
javac
如果出现内容则安装成功
```

