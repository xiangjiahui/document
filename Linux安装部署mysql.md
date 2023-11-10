# Linux下安装部署mysql

## 	1、卸载原来的mysql

```shell
#先检查是否有安装mysql
rpm -qa | grep mysql

#如果没有则可免去此步骤

#如果有，那么就删除所有的mysql文件
rpm -e 文件名
```

## 	2、下载安装mysql文件

```shell
#下载
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm

#rpm安装
rpm -ivh mysql-community-release-el7-5.noarch.rpm

#yum安装mysql-server
yum install -y mysql-server

#启动服务
systemctl start mysqld.service

#查看运行状态
systemctl status mysql
```

## 	3、修改密码

```shell
#首先查看初始密码
grep 'password' /var/log/mysqld.log

#一般是新安装的mysql默认没有密码
mysql -u root -p
#enter键直接进入mysql

mysql>use mysql;
mysql>update user set password=password("设置的密码") where user="root";
#例如 update user set password=password("XJH981117") where user="root";
#刷新权限,必须的
mysql>flush privileges;

#查看ip地址
#直接查看IP地址
hostname -i
#ip命令
ip addr

ifconfig

#如果是阿里云的服务器还要开放端口
iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
```

## 	4、设置远程访问

```shell
grant all privileges on *.* to 'root'@'%' identified by '密码' with grant option;
```

