# Jenkins学习笔记

## 安装

### 方式一

安装JDK，因为jenkins需要依赖jdk

```shell
yum -y install java-1.8.0-openjdk*
```

安装jenkins

```shell
#Jenkins下载地址
#https://mirrors.jenkins-ci.org/redhat/
#上传到服务器进行安装
rpm -ivh rpm -ivh jenkins-2.432-1.1.noarch.rpm
```

修改jenkins配置

```shell
#这是旧版本配置文件路径
vim /etc/sysconfig/jenkins

#新版本配置文件路径
#/usr/lib/systemd/system/jenkins.service
```

修改内容如下

```shell
JENKINS_USER='root'
JENKINS_PORT='8088'
```

启动jenkins

```shell
systemctl start jenkins
```



### 方式二

先安装jdk

```shell
yum install -y java
```

安装jenkins

````shell
#网络原因,会导致速度较慢
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
yum install jenkins -y

#安装完成之后查看Jenkins相关目录和文件
rpm -ql jenkins
````

启动jenkins

````shell
systemctl start jenkins

#如果是阿里云或者其它的一些云服务器，还需要开发端口
#例如阿里云，就需要到服务器控制台，选择安全组，配置对应端口的入规则，例如配置jenkins默认的8080安全组入规则
````



## 使用

````shell
#安装启动完成，端口也配置完成之后，访问jenkins
#按照步骤即可
````

修改文件，修改jenkins插件下载的地址，将其修改为国内的镜像地址，这样下载速度才会很快

```shell
#先修改服务器的jenkins文件
sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json

#在jenkins网页里的配置
#在Manage Jenkins > Plugins > Advanced settings,修改最后面的Update site里的URl
https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

#最后在jenkins网页中的地址栏输入restart来重启jenkins, ip:端口/restart
#例如: http://127.0.0.1:8080/restart
```

汉化

```shell
#在Manage Jenkins > Plugins > Avaliable plugins 里面下载汉化插件
#搜索chinese,安装重启之后，就对jenkins完成了汉化
```

