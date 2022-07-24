# Linux安装部署tomcat

## 	1、首先去官网下载tomcat

```shell
#可以在浏览器上下载
https://tomcat.apache.org/download-80.cgi

#也可以直接在Linux上下载
 wget https://dlcdn.apache.org/tomcat/tomcat-8/v8.5.78/bin/apache-tomcat-8.5.78.tar.gz
```

## 	2、解压文件

```shell
tar -zxvf tomcat压缩包
```

## 	3、添加防火墙端口

```shell
#tomcat默认是8080,Linux没有这个端口,需要添加新的端口
#添加端口首先需要防火墙是开启状态
firewall-cmd --zone=public --add-port=8080/tcp --permanent

#重启防火墙
firewall-cmd --reload

#查看所有的防火墙端口
firewall-cmd --list-all

#禁止firewall开机启动
systemctl disable firewalld.service
```

## 	4、启动tomcat

```shell
#进入tomcat的安装目录
cd tomcat8080
cd bin

#启动
sh startup.sh
```

## 	5、访问tomcat

```html
//这里是自己的Linux访问地址
192.168.31.129:8080/
```

## 	6、新建另一个tomcat

```shell
#复制原来的tomcat文件夹
cp -r /tomcat/tomcat8080 /tomcat/tomcat8081

#修改配置文件
cd tomcat8081
cd conf
vim server.xml

#修改信息
#将第一行的port修改
<Server port="8005">
#原来是8005, 改掉即可

#修改后面的端口信息
<Connector port="8080">

#将其改成8081
#这样就有了两个tomcat,以后可以与nginx配置负载均衡
```

