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

## 6、nginx操作命令

```shell
# 进入/usr/local/nginx/sbin目录下
# 查看nginx命令帮助
./nginx -h
#执行语句后会出现以下nginx命令帮助提示
Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /usr/local/nginx/)
  -c filename   : set configuration file (default: conf/nginx.conf)
  -g directives : set global directives out of configuration file
```

## 7、反向代理配置

```shell
# nginx默认的配置文件是/usr/local/nginx/conf/nginx.conf文件
# 在location节点添加如下配置
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
# 反向代理到后台 Web 服务
proxy_pass http://172.17.0.1:8080;
#前面 3 行配置都是设置一些请求头，主要是最后一行，这一行配置指定了反向代理的目标地址。它使用 proxy_pass 指令将过来的请求转发给后端服务器，这里的目标地址是 http://192.168.76.129:8080
# 最后的IP地址根据实际情况更改
```

## 8、前端打包部署到nginx

````shell
# 前端打包好后，将dist目录里的所有文件上传到nginx的html目录里面
# 最后修改nginx.conf配置文件
# location / 部分：
# try_files $uri $uri/ @router; 尝试查找请求的文件，如果找到则直接返回，否则将请求交给 @router 命名的位置处理。
# root /usr/share/nginx/html; 指定静态文件的根目录，这是一个默认的 Nginx 静态文件目录。
# index index.html index.htm; 指定首页文件。

# location @router 部分：
# rewrite ^.*$ /index.html last; 将所有请求重写到 index.html，这是为了确保前端路由能够正常工作。前端路由通常使用 HTML5 History 模式，这样做可以确保前端路由的 SPA 在浏览器端能够正确处理。

# location /api 部分：
# rewrite ^/api/(.*)$ /$1 break; 将以 /api 开头的请求的 URI 重写为不包含 /api 的形式，因为后端的接口均不是以 /api 开头。
# proxy_set_header 指令用于设置传递给后端的一些头信息。
# proxy_pass http://172.17.0.1:8080; 将请求代理到后端 Web 服务，地址为 http://172.17.0.1:8080。
server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;
    # = 代表精确匹配路径, ^~ 代表匹配以什么开头, ~ 代表按正则表达式匹配,区分大小写,~*代表按正则表达式匹配,不区分大小写

	location = /login {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://192.168.76.129:8088/login;
        }
        
     location ^~ /admin {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://192.168.76.129:8088/admin;
            # 请求长度限制
            client_max_body_size 20m;
     }


    #location / {
    #    try_files $uri $uri/ @router;
    #    root   /usr/local/nginx/html;
    #    index  index.html index.htm;
    # }

    #location @router {
        #rewrite ^.*$ /index.html last;
    #}

    #location /api {
    #   rewrite  ^/api/(.*)$ /$1 break;
    #  proxy_set_header Host $host;
    #   proxy_set_header X-Real-IP $remote_addr;
    #   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        # 反向代理到后台 Web 服务
    #    proxy_pass http://172.17.0.1:8080;
    #}

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
````

## 9、域名选购 & 网站备案 & 配置域名解析

````shell
# 1、访问阿里云https://wanwang.aliyun.com/
# 2、搜索自己想要的域名，然后加入清单，立即购买
# 3、下载阿里云App，进入网站备案功能
# 4、进入后，点击新增网站，安装提示填写相关信息，然后提交
# 5、提交后会有客服联系确认备案网站的信息，然后等待，正常是半个月到一个月，审核通过后会发送短信验证码到本人手机上
# 6、网站备案完成后，回到阿里云控制台的域名列表，对域名点击解析
# 7、进入域名解析列表页后，点击新手引导 ， 如上图所示，勾选网站域名，然后输入需要解析到哪个云服务器的公网 IP，点击确定 。
# 8、接着，在解析列表中就会看到两条域名 DNS 解析规则，分别是：
# www : 将带有 www. 前缀的域名， 解析到对应服务器 IP , 如 www.chezai51.com -> 116.62.199.48;
# @ : 将不带有 www. 前缀的域名，解析到对应服务器 IP , 如 chezai51.com -> 116.62.199.48;
````

## 10、nginx配置SSL证书，实现网站支持Https访问

```shell
# 1、前往阿里云官网，进入控制台搜索ssl，点击进入SSL证书
# 2、依次点击SSL证书、免费证书、创建证书
# 3、创建成功后，接口即可获取一个有效期 1 年的免费版 SSL 证书了，然后点击证书申请
# 4、输入需要绑定的域名，其他保持默认值即可，点击提交审核
# 5、等待SSL证书签发成功后刷新页面，即可看到状态为【已签发】的 SSL 证书了，点击【下载】
# 6、选择 Nginx 类型的 SSL 证书，将其下载到本地，等会配置 Nginx SSL 需要用到
# 7、下载后进行解压，会看到两个文件，分别以 .key 和 .pem 为后缀，在服务器的nginx目录创建一个cert目录，于存放 SSL 证书文件，然后将刚刚解压出来的两个文件上传至 /cert 文件夹下
# 8、进入nginx的conf目录下，创建一个 xxx.conf 文件，此配置文件专门用来配置 SSL 相关内容
# 9、配置内容如下，之后将nginx的默认配置文件改为这个新建的conf文件即可
server {
    listen 80;
    listen  [::]:80;
    server_name quanxiaoha.com www.quanxiaoha.com;
    # 将所有 http 请求 301 重定向到 https 请求，
    return 301 https://www.quanxiaoha.com$request_uri;
}

server { 
    # 监听 443 https 端口 , 启用 HTTP/2 协议。HTTP/2 是 HTTP 协议的下一版本，它引入了一些性能优化，例如多路复用（Multiplexing）和头部压缩，以提高页面加载速度。
    listen 443 ssl http2;
    # 域名，修改成你自己的
    server_name quanxiaoha.com www.quanxiaoha.com;

    client_max_body_size 100M;
    # Nginx 容器中的 ssl 证书存放路径（后续会挂载到宿主机的 /docker/nginx/cert/ 目录下）
    ssl_certificate /etc/nginx/cert/8055644_www.quanxiaoha.com.pem;
    ssl_certificate_key  /etc/nginx/cert/8055644_www.quanxiaoha.com.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    location / {
        try_files $uri $uri/ @router;
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    location @router {
        rewrite ^.*$ /index.html last;
    }

    location /api {
        rewrite  ^/api/(.*)$ /$1 break;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        # 反向代理到后台 Web 服务
        proxy_pass http://172.17.0.1:8080;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

