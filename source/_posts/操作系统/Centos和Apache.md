---
title: Centos和Apache
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
# Centos与Apache

Apache
在Apache的安装目录，默认：`/etc/httpd`下有若干文件夹，`conf.d`文件夹是用来存储各个模块的配置文件，`conf.module.d`是用来给各个模块做映入的，也就是很多模块的引入都在这里面，`conf`文件夹主要是主配置文件的目录，`logs`文件夹打印日志的存储地，`moduls`是各个模块的位置


例如：`httpd.conf`：这个文件是apache的主配置文件，其他的`conf.d`和`conf.module.d`内的配置文件夹能够生效也是因为在这里面引入了两个文件夹下的所有文件，如
```shell
#引入了conf.moudles.d下的所有配置文件，也就是所有需要的模块
Include conf.modules.d/*.conf
#引入了conf.d下的所有配置文件，也就是每个模块服务需要做的详细配置
IncludeOptional conf.d/*.conf
```
>在我的主配置文件里面还有一个功能，那就是多域名的配置，这个的主要作用是，针对不同域名的访问，映射到不同的访问文件夹，具体的映射由`vhost.map`文件配置
```shell
RewriteEngine on
RewriteMap lowercase int:tolower
RewriteMap vhost txt:/var/www/vhost.map
RewriteCond ${lowercase:%{SERVER_NAME}} ^(.+)$
RewriteCond ${vhost:%1} ^(/.*)$
RewriteRule ^/(.*)$ %1/$1
```
---
>在开启了SSL服务之后，存在一个问题，由于我的域名证书是单域名证书，所以我只能够给特定的域名SSL授权，所以我配置了一个SSL的但域名拦截重定向，这里的意思是只拦截`ajiehome.cn`和`www.ajiehome.cn`两个服务请求开头的所有服务，并且转到`https`服务
```shell
RewriteEngine On
RewriteCond %{SERVER_PORT} 80
RewriteCond %{HTTP_HOST} ^ajiehome.cn [NC,OR]
RewriteCond %{HTTP_HOST} ^www.ajiehome.cn [NC]
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R,L]
```
---
>如果用下面这个进行拦截重定向的话，会造成所有二级子域名全部转到https，甚至由于正则匹配写的不当会全部重定向到主服务之下，这里的意思是，所有不是443端口的服务请求，全部重定向https下去，
>`SERVER_NAME`：指的是服务，也就是域名部分
>`REQUEST_URI`：指的是请求的地址，也就是域名的后半部分
>`HTTP_HOST`：

```shell
RewriteEngine On
RewriteCond %{SERVER_PORT} !^443 \$
RewriteRule ^(.*)? \$ https://%{SERVER_NAME}%{REQUEST_URI} [L,R]
```

---
例如：`conf.module.d`下的`00-ssl.cof`文件
>`00-ss.conf`文件，里面有一个`mod_ssl`模块的引用，这个文件夹里的模块主要就是给`ssl`服务使用的模块，如：
>
```shell
LoadModule ssl_module modules/mod_ssl.so
```