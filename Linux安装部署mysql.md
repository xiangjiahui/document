# Linux下安装部署mysql

## 	1、卸载原来的mysql

```shell
#先检查是否有安装mysql或者maridb
rpm -qa | grep mysql/db

#如果没有则可免去此步骤

#如果有，那么就删除所有的mysql文件
rpm -e 文件名
```

## 	2、下载安装mysql文件

```shell
#前往官网下载Linux版本
# https://downloads.mysql.com/archives/community/
#新建目录
mkdir /soft/mysql
cd /soft/mysql
mkdir mysql-5.7.3
# 解压安装包到该目录下
tar -xvf mysql-8xxx.el7.x86_64.rpm-bundle.tar -C mysql-5.7.3
#安装mysql依赖的插件
yum install openssl-devel
yum -y install libaio perl net-tools
yum install libncurses*
yum install libtinfo*
# 依次安装rpm包
rpm -ivh mysql-community-common
rpm -ivh mysql-community-libs
rpm -ivh mysql-community-libs
rpm -ivh mysql-community-devel
rpm -ivh mysql-community-client
rpm -ivh mysql-community-server
#启动服务
systemctl start mysqld
#重启
systemctl restart mysqld
#关闭
systemctl stop mysqld
#开机自启
systemctl enable mysqld
#查看初始密码
cat /var/log/mysqld.log
#或者
grep 'password' /var/log/mysqld.log
#连接mysql,输入初始密码
mysql -u root -p
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

#或者这种方式修改密码
ALTER  USER  'root'@'localhost'  IDENTIFIED BY 'xxx';

#如果提示Your password does not satisfy the current policy requirements，就降低密码校验规则
set global validate_password_policy = 0;

#刷新权限,必须的
mysql>flush privileges;

#查看ip地址
#直接查看IP地址
hostname -i
#ip命令
ip addr

ifconfig

#如果是阿里云或者华为其它的云服务器，需要区控制台开放相应的端口
iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
```

## 	4、设置远程访问

```shell
grant all privileges on *.* to 'root'@'%' identified by '密码' with grant option;
```



# 安装部署方法2

```shell
#首先检查是否已有安装mysql或其它db
rpm -qa | grep sql/db
#如果有，则删除
rpm -e --nodeps sql/db

#安装mysql
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum install mysql-server

#权限设置
chown -R mysql:mysql /var/lib/mysql/

#初始化mysql
mysqld --initialize

#验证mysql，如果没有出现对应的版本信息，说明未安装成功
mysqladmin --version

#启动mysql
systemctl start mysqld

#开机自启
systemctl enable mysqld

#使用mysql，初始安装的mysql默认没有密码
mysql

#设置密码
mysql>use mysql;
mysql>update user set password=password("设置的密码") where user="root";
#例如 update user set password=password("XJH981117") where user="root";

#刷新权限,必须的
mysql>flush privileges;

#设置远程访问权限
mysql>grant all privileges on *.* to 'root'@'%' identified by '密码' with grant option;

#开放3306端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent

#重启防火墙
firewall-cmd --reload
