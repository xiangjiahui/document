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

## 6、启动redis

````shell
cd /soft/redis/redis-7.0.2/src
#直接启动
./redis-server

#指定配置文件启动
./redis-server ../redis.conf

#以脚本启动
#!/bin/sh
# chkconfig:   2345 90 10
# Simple Redis init.d script conceived to work on Linux systems
# as it does use of the /proc filesystem.
 
#redis服务器监听的端口
REDISPORT=6379
 
#服务端所处位置
EXEC=/usr/local/bin/redis-server
 
#客户端位置
CLIEXEC=/usr/local/bin/redis-cli
 
#redis的PID文件位置，需要修改
PIDFILE=/var/run/redis_${REDISPORT}.pid
 
#redis的配置文件位置，需将${REDISPORT}修改为文件名，配置文件路径根据实际情况来
CONF="/etc/redis/${REDISPORT}.conf"
 
case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
                echo "$PIDFILE exists, process is already running or crashed"
        else
                echo "Starting Redis server..."
                $EXEC $CONF
        fi
        ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
                echo "$PIDFILE does not exist, process is not running"
        else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
                $CLIEXEC -p $REDISPORT shutdown
                while [ -x /proc/${PID} ]
                do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
        fi
        ;;
    *)
        echo "Please use start or stop as first argument"
        ;;
esac


# 将启动脚本复制到/etc/init.d目录下，本例将启动脚本命名为redisd（通常都以d结尾表示是后台自启动服务）
# 根据自己的情况更改
cp redis_init_script /etc/init.d/redisd

#设置为开机自启动服务器
chkconfig redisd on
#打开服务
service redisd start
#关闭服务
service redisd stop
````

