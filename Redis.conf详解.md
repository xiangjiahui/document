# Redis.conf详解

**network网络配置** 

>bind 127.0.0.1  #绑定ip
>
>protected-mode yes #保护模式
>
>port 6379     #端口设置

**通用general**

>daemonize yes          #以守护方式运行，默认是no  我们需要自己开启为yes
>
>pidfile /www/server/redis/redis.pid        #如果以后台的方式运行，我们就需要指定一个pids文件
>
>#日志
>
>loglevel notice
>
>logfile "/www/server/redis/redis.log"  #日志的位置名
>
>databases 16        #数据库的数量。默认是16个数据库
>
>always-show-logo yes        #总是显示logo



**快照**

**持久化，在规定的时间内，执行了多少多少次操作，则会持久化到文件 .rdb.aof**

reids是内存数据库，如果没有持久化，那么数据断电及失

>#如果900秒内 如果至少有一个1 key进行了修改，我们及进行了持久化操作
>
>save 900 1
>
>#如果300秒内 如果至少有一个10 key进行了修改，我们及进行了持久化操作
>
>save 300 10
>
>#如果60秒内 如果至少有一个10000 key进行了修改，我们及进行了持久化操作
>
>save 60 10000
>
>
>stop-writes-on-bgsave-error yes        #持久化如果出错，是否还需要继续工作
>
>rdbcompression yes                           #是否压缩rdb文件，需要消耗一些cpu的资源
>
>rdbchecksum yes                     #保存rdb文件的时候，进行错误的检查校验
>
>dir /www/server/redis/               #rdb文件保存的目录



**APPEND ONLY模式 aof**

>appendonly no               #默认不开启aof模式的，默认是使用rbd方式持久化的，在大部分所有的情况下，rdb完全够用了
>appendfilename "appendonly.aof"        #持久化的文件的名字
>
>\# appendfsync always        #每次修改都会 sync 消耗性能
>
>appendfsync everysec                #每秒执一次 sync，可能会丢失这1s的数据
>
>\# appendfsync no            #不执行 sync 这个时候操作系统自己同步数据，数据最快