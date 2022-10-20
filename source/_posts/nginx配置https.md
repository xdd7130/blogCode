---
title: nginx配置https
date: 2022-10-17 20:22:12
categories:
- nginx
tags: 
    - [nginx, linux]
---
## nginx开启 SSL模块
Nginx如果未开启SSL模块，配置Https时提示错误：
```
nginx: [emerg] the “ssl“ parameter requires ngx_http_ssl_module in /usr/local/
```
开启:
#### 切换到源码包,配置https环境
1.找打nginx的安装环境  ，如nginx的安装目录是/usr/local/nginx,源包在 /opt/nginx-1.22.0 目录下
2.切换到源码包：
```
cd /opt/nginx-1.22.0
```
3.进行编译
```
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
```
4.配置完成后，运行命令：
```
make
```
5.make命令执行后，不要进行make install，否则会覆盖安装。
6.备份原有已安装好的nginx：
```
cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak
```
7.停止nginx状态：
```
/usr/local/nginx/sbin/nginx -s stop
```
8.将编译好的nginx覆盖掉原有的nginx：
```
# cd /root/nginx-1.10.1/

# cp ./objs/nginx /usr/local/nginx/sbin/
```
9.提示是否覆盖，输入yes即可。
10.然后启动nginx：
```
# /usr/local/nginx/sbin/nginx
```
11.进入nginx/sbin目录下，通过命令查看模块是否已经加入成功：
```
# cd /usr/local/nginx/sbin/

# ./nginx -V
```
## openSSL 制作证书
一般来说，主流的Web服务软件，通常都基于两种基础密码库：OpenSSL和Java。
Tomcat、Weblogic、JBoss等，使用Java提供的密码库。通过Java的Keytool工具，生成Java Keystore（JKS）格式的证书文件。
Apache、Nginx等，使用OpenSSL提供的密码库，生成PEM、KEY、CRT等格式的证书文件。
此外，IBM的产品，如Websphere、IBM Http Server（IHS）等，使用IBM产品自带的iKeyman工具，生成KDB格式的证书文件。微软Windows Server中的Internet Information Services（IIS），使用Windows自带的证书库生成PFX格式的证书文件。

> X.509#DER 二进制格式证书，常用后缀.cer .crt
> X.509#PEM 文本格式证书，常用后缀.pem
> 有的证书内容是只包含公钥（服务器的公钥），如.crt、.cer、.pem
> 有的证书既包含公钥又包含私钥（服务器的私钥），如.pfx、.p12

![01.png](01.png)

在开发和测试环境一般我们使用openssl或keytool等ssl的密码库工具制作证书
#### 生成私钥
```
openssl genrsa -out rsa_private.key 2048
```
genrsa： 表示使用rsa算法,openssl还支持RSA, DSA, ECDSA, and EdDSA，
2048：表示私钥的大小，单位为位
私钥生成后可使用cat 命令查看，其内容为pem格式
如果生成的私钥需要密码保护，则可在生成私钥时指定加密算法（后续每次使用私钥时都会要求输入访问秘钥的密码）,其中 passout 代替shell 进行密码输入，否则会提示输入密码:
```
openssl genssl -aes256 -passout pass:123456 -out rsa_private.key 2048
```
#### 生成公钥
```
openssl rsa in rsa_private.key -pubout -out rsa_public.key
```
#### 生成自签名证书（不基于csr）
```
openssl req -new -x509 -days 1095 -key private.key -out cert.crt 
```
-x509:表示证书格式
-day: 表示证书的有效天数
根据提示输入相关信息，Common Name 可绑定域名或ip地址，域名可配通配符。

## 配置nginx
```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }
        location /tm {
            proxy_pass   http://{host.ip}:3010/mock/167/tm;
        }
        
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    server {
        listen       443 ssl;
        server_name  localhost;

        ssl_certificate      /usr/local/nginx/conf/tm_https.pem; # openssl req -new -x509 -days 1095 -key private.key -out tm_https.pem 
        ssl_certificate_key  /usr/local/nginx/conf/tm_https.key; # openssl genrsa -out tm_https.key 2048
 
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;

        location / {
            root   html;
            index  index.html index.htm;
        }
        location /tm {
            proxy_pass   http://{host.ip}:3010/mock/167/tm;
        }
    }

}

```