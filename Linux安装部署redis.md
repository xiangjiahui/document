# Linux安装部署redis

## 1、到redis官网下载压缩包

## 2、新建redis目录

```shell
mkdir redis
```

## 3、将压缩包移入redis目录并解压

````shell
mv redis-xxx.tar /path
tar -xvf redis-xxxx.tar
#path是自己redis的文件目录，根据实际情况
````



## 4、安装

```shell
cd redis
#进入解压目录
cd redis-7.0
make && make install

#如果执行make && make install命令报错，就代表没有安装gcc环境，因为redis是由C语言开发，执行以下命令
yum install -y cpp binutils glibc glibc-kernheaders glibc-common glibc-devel  gcc

#如果执行上面的命令还报错就继续执行以下命令
yum -y install centos-release-scl
yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
scl enable devtoolset-9 bash

#还是报错就继续执行
make MALLOC=libc

#最后执行
make install PREFIX=/data/redis
## 注意PREFIX=/data/redis指定安装目录，可指定可不指定，如果不指定默认是在/data/redis/redis-7.0.2/src/目录下,如果指定则会在/data/redis目录下创建一个bin文件夹，会安装在/data/redis/bin目录下
```

## 5、查看是否安装成功

```shell
#默认安装目录是usr/local/bin
cd /usr/local/bin
#如果有redis的一些命令则说明安装成功
```

