# Jenkins学习笔记

## 服务器镜像源配置

如果是新服务器，自带的镜像源速度很慢，所以需要更换为国内的

```shell
下载阿里云的Centos7镜像源配置文件，并直接替换

sudo curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

清理缓存
yum clean all

生成新的缓存
yum makecache

测试
yum update
```





## 安装

### 方式一

安装JDK，因为jenkins需要依赖jdk，并且新版本的jenkins都只支持8版本以上的jdk

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



## 安装Maven

jenkins自动打包部署是需要maven环境的，所以需要在服务器上安装maven

```tex
maven官网
https://maven.apache.org/docs/history.html

其他版本的maven下载
https://dlcdn.apache.org/maven/
```

自行选择zip或tar压缩包，上传到服务器解压缩。然后修改配置文件里的仓库所在位置和添加镜像源

```xml
  <localRepository>E:\maven\repository</localRepository>

	<mirror>
       <id>alimaven</id>
       <mirrorOf>central</mirrorOf>
       <name>aliyun maven</name>
       <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>

    <mirror>
      <id>tencent</id>
      <name>tencent maven mirror</name>
      <url>https://mirrors.tencent.com/nexus/repository/maven-public/</url>
      <mirrorOf>*</mirrorOf>
    </mirror>
```

修改profile文件

```shell
vim /etc/profile
export MAVEN_HOME=/maven/apach-maven-3.6.3
export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin

#重新加载
source /etc/profile

#查看是否成功,如果出现版本相关信息则是成功
mvn -v
```



## 安装git

Jenkins的使用需要git才能拉取代码。

```shell
#直接安装即可，也可以百度其它方式
yum install -y git

#查看安装位置
which git
```





## jenkins配置

### 启动jenkins

```shell
systemctl start jenkins

#访问jenkins
ip地址:端口 #ip地址加上端口号即可，默认端口是8080
```

### jenkins配置

第一次访问需要密码，并且让你创建管理员账户，跟随步骤即可，之后会为你安装插件



#### 新建项目

![image-20250410155111719](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250410155111719.png)



![image-20250410155216943](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250410155216943.png)

这两步之后先配置源码的代码地址和账号密码即可。



#### Jenkins配置maven

先为jenkins配置maven，方便全局使用。

菜单路径是：**dashboard>manage jenkins>tools**。最前的是Maven配置。

第一个配置项 默认settings提供 的配置项选为 **Settings file in filessystem。**

第二个配置项 文件路径 的配置项 填写为服务器上maven的conf配置文件所在路径。比如/maven/apach-maven-3.6.3/conf/settings.xml

第三个配置项不变即可。



maven配置完之后在配置 Git。往下翻看会有 **Git installations**的配置。

**Path to Git executable** 配置为 服务器上 git所在的目录。使用which git查看，然后填写即可。



最后翻到Maven安装配置。

Name配置项自定义即可。

**MAVEN_HOME**配置项填写为服务器上maven所在的位置。比如/maven/apach-maven-3.6.3。



最后应用保存即可。



#### 项目的配置

上面的操作完成之后，就对建立的项目进行配置。首先要确保 源码管理一栏都正常配置了。



然后在左侧菜单栏选择 **Build Steps**。

首先配置 **Invoke top-level Maven targets**。

Maven版本选择之前配置好的maven。

目标填写的内容为 ： clean package -Dmaven.test.skip=true。这是为了打包过程中跳过测试。

然后选择高级，填写里面的POM，也就是项目代码的pom.xml文件所在的位置。



#### 安装 publish over ssh插件

安装这个插件是为了Jenkins、能够完成自动打成jar包，然后上传到服务器，再自动启动项目。



安装完成之后，跟随菜单路径 **dashboard>manage jenkins>system**,一直往下翻到SSH Servers的详细配置。

**Name**自定义即可，**Hostname**填写为服务器的ip地址，**username**为访问服务器的用户名，Remote Directory填写为 / 即可。

然后选择高级，填写访问服务器的需要用的密码即可，最后保存。



完成上述步骤之后，点击创建的项目，进入项目配置，选择左侧菜单栏的**配置按钮**，之后选择菜单栏 **Build Steps** 下面有一个 **Send files or execute commands over SSH**的详细配置。

里面的Name选择之前定义的Name。

**Source files**是配置要上传的文件，一般是在  /var/lib/jenkins/workspace/ 这个路径下面的项目所在的位置。例如，jenkins将代码拉取到了这个目录，/var/lib/jenkins/workspace/socketdemo，socketdemo就是代码所在的目录。然后 **Source files**实际要填写的值就是

**socketdemo/target/*.jar**。



**Remove prefix** 是移除路径的前缀文件夹。填写为 **socketdemo/target/**。



**Remote directory** 是要将成的jar包上传到云服务器中的哪个文件夹下。填写为 /app/demo。这个路径是根据实际情况来写。



**Exec command** 是上传完成后，想要执行的脚本，可以实现上传后，自动启动项目。

脚本内容如下

```shell
#!/bin/bash
#首先执行下面的语句，就算给jenkins赋予了权限，可能也无法在服务器上上传jar包
cp /var/lib/jenkins/workspace/yourpath/target/xxx.jar /app/xxx/
APP_NAME=weblog-web-0.0.1-SNAPSHOT.jar

pid=`ps -ef | grep $APP_NAME | grep -v grep|awk '{print $2}'`

function is_exist(){
	pid=`ps -ef | grep $APP_NAME | grep -v grep|awk '{print $2}'`
	if [ -z ${pid} ]; then
		String="notExist"
		echo $String
	else
		String="exist"
		echo $String
	fi
}

str=$(is_exist)
if [ ${str} = "exist" ]; then
	echo " 检测到服务已启动，pid 是 ${pid} "
	kill -9 $pid
else
	echo " 服务未启动 "
	echo "${APP_NAME} is not running"
fi

str=$(is_exist)
if [ ${str} = "exist" ]; then
	echo "${APP_NAME} 已经启动了. pid=${pid} ."
else
	source /etc/profile
	nohup java -Xms300m -Xmx300m -jar /app/weblog/$APP_NAME --spring.profiles.active=prod > /dev/null 2>&1 &
	echo "服务已重新启动 .."
fi
```

再在Build Steps中，增加一个Execute shell的构建选项，这是为了在执行maven打包前，先执行一些shell命令，确保打包能正常执行下去。以下是脚本内容。
```shell
#!/bin/bash

# 设置 Maven 本地仓库路径（可选，根据需要调整）
#export MAVEN_OPTS="-Dmaven.repo.local=/path/to/your/maven/repository"

# 修改目录权限（确保 Jenkins 用户有写入权限）
echo "Granting permissions to Maven repository..."
chown -R jenkins:jenkins /maven/repository/*
echo "Granting permissions succeeded!"

# 执行 Maven install,执行这个是因为如果是微服务项目，一个子模块会需要依赖微服务中其他的模块，所以需要先执行install，将所需的依赖全部安装好。
# /maven/apach-maven-3.6.3/bin/mvn 以这种方式执行maven命令是因为，即使设置了jenkins全局配置的环境，并且也在服务器中有权限，但是在构建时依然会报错，mvn未找到命令。
echo "Starting Maven install build..."
/maven/apach-maven-3.6.3/bin/mvn clean install -DskipTests
echo "Maven install build succeeded!"

# 检查构建结果
if [ $? -eq 0 ]; then
  echo "Build succeeded!"
else
  echo "Build failed! Exiting..."
  exit 1
fi
```



# 注意事项！！！

如果不是用的docker安装Jenkins等一系列软件，而是原生Linux安装，在jenkins工作时，需要对服务器的目录和文件进行读写执行操作，在这个时候，Jenkins会由于权限不够，不能对服务器进行操作，从而构建项目失败。所以需要对jenkins赋予权限。

```shell
#首先查看某个目录或文件的权限
ls -ld 目录


# chown 新所有者:新所属组 文件或文件夹路径
chown -R jenkins:jenkins 目录/文件。
```



另外，项目的jdk版本和springboot版本也会出现不适配的情况。 jdk8 一般适配与springboot 2.x的版本，jdk 8之后适配于 springboot3.x的版本。
