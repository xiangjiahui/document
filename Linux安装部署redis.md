# Linux安装部署redis

## 1、到redis官网下载压缩包

## 2、新建redis目录

```shell
mkdir redis
```

## 3、将压缩包移入redis目录并解压

## 4、安装

```shell
cd redis
#进入解压目录
cd redis-7.0
make && make install
```

## 5、查看是否安装成功

```shell
#默认安装目录是usr/local/bin
cd /usr/local/bin
#如果有redis的一些命令则说明安装成功
```

