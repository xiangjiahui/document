# 	Redis持久化配置

## 1、RDB(快照)持久化

>概念:保存某个时间点的全量数据快照

```bash
# Save the DB to disk.
#
# save <seconds> <changes> [<seconds> <changes> ...]
#
# Redis will save the DB if the given number of seconds elapsed and it
# surpassed the given number of write operations against the DB.
#
# Snapshotting can be completely disabled with a single empty string argument
# as in following example:
#
# save ""
#
# Unless specified otherwise, by default Redis will save the DB:
#   * After 3600 seconds (an hour) if at least 1 change was performed
#   * After 300 seconds (5 minutes) if at least 100 changes were performed
#   * After 60 seconds if at least 10000 changes were performed
#
# You can set these explicitly by uncommenting the following line.
#
# save 3600 1 300 100 60 10000
save 60 1 #代表redis在60秒内写入一条数据进行快照

stop-writes-on-bgsave-error yes #代表当备份进程出错的时候，主进程就停止写入新的操作了。这样是为了保护持久化数据一致性的问题。

rdbcompression yes #表示在备份的时候，需要将rdb文件进行压缩后才去做保存

禁用rdb配置 只需要在save 60 1 下面加上 save "" 
```

自动触发RDB持久化方式

>1、根据redis.conf 配置里的SAVE m n 定时触发（用的是BGSAVE）
>
>2、主从复制时，主节点自动触发
>
>3、Debug Reload
>
>4、执行Shutdown 且没有开启AOF持久化



## 2、AOF(Append -Only - File)持久化

>概念:保存写状态，默认是关闭的

>1、记录下除了查询以外的所有变更数据库状态的指令
>
>2、以append 的形式追加保存到AOF文件中（增量）

```bash
appendonly no #将no设置为yes即可
```

>appendonly 相关的配置 
>
>appendfsync eveerysec 这是默认配置 这是配置AOF 的写入方式的 
>
>eveerysec 代表每隔一秒写入到AOF文件里面
>
>always 代表总是及时的将缓存中数据写入到AOF中
>
>no 代表不写入



## 3、优缺点

>RDB 和 AOF 的优缺点
>
>RDB 优点：全量数据快照，文件小，恢复快
>
>RDB 缺点： 无法保证最近一次快照后的数据
>
>AOF 优点： 可读性较高，适合保存增量数据，数据不易丢失
>
>AOF 缺点： 文件体积大，恢复时间长



## 4、主从复制

```bash
################################# REPLICATION #################################

# Master-Replica replication. Use replicaof to make a Redis instance a copy of
# another Redis server. A few things to understand ASAP about Redis replication.
#
#   +------------------+      +---------------+
#   |      Master      | ---> |    Replica    |
#   | (receive writes) |      |  (exact copy) |
#   +------------------+      +---------------+
#
# 1) Redis replication is asynchronous, but you can configure a master to
#    stop accepting writes if it appears to be not connected with at least
#    a given number of replicas.
# 2) Redis replicas are able to perform a partial resynchronization with the
#    master if the replication link is lost for a relatively small amount of
#    time. You may want to configure the replication backlog size (see the next
#    sections of this file) with a sensible value depending on your needs.
# 3) Replication is automatic and does not need user intervention. After a
#    network partition replicas automatically try to reconnect to masters
#    and resynchronize with them.
#
# replicaof <masterip> <masterport>

# If the master is password protected (using the "requirepass" configuration
# directive below) it is possible to tell the replica to authenticate before
# starting the replication synchronization process, otherwise the master will
# refuse the replica request.
#
# masterauth <master-password>

#只用配置从库，主库不用配置
#主写从读，一主二从，从库不能写，只能读
replicaof 127.0.0.1 6379 #代表将对应的ip和端口的redis作为主库，配置这行信息的redis就是从库

masterauth <master-password> #主库权限密码,没有设置redis密码，该项可不设置
```

>首先需要准备三份redis.conf文件,修改各自的端口和pid等文件的后缀
>
>修改完成后再进行上方的主从复制配置

