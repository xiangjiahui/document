# Linux安装Nginx

## 1、安装依赖包

```shell
#首先下载pcre依赖包
https://sourceforge.net/projects/pcre/files/pcre/8.37/
#下载好之后上传到服务的nginx文件夹中
#解压依赖包
tar -zxvf pcre

#解压之后进入解压的文件夹中
cd pcre-8.37
#执行命令
./configure

#/configure后 最后一行会出现
#configure: error: You need a C++ compiler for C++ #support.
#执行以下命令即可
yum install -y gcc gcc-c++
#再次执行命令
./configure

#执行完成之后,再次执行make命令
make && make install

#在安装其它的依赖openssl和zlib
yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
```

## 2、下载并解压安装包

```shell
#先建立nginx的文件夹
mkdir nginx

#在nginx目录下下载tar压缩包
wget http://nginx.org/download/nginx-1.12.2.tar.gz

#解压
tar -zxvf nginx-1.12.2.tar.gz
```

## 3、安装nginx

```shell
#进入nginx安装目录
cd /
cd nginx-1.12.2
#执行命令
./configure
#执行make命令
make && make install
```

## 4、启动nginx服务

```shell
#启动服务要进入另一个目录,而不是这个安装目录
cd /usr/local/nginx/sbin
#先查看防火墙有没有开放80端口,没有则需要添加80端口

#在sbin目录下启动nginx
./nginx
```

## 5、访问nginx

```shell
#浏览器访问nginx
192.168.31.129

#如果访问到了Welcome to nginx! 
#安装成功
```

