---
title: Centos安装V2ray和Acmesh
date: 2023-06-07 15:58:40
tags:
categories: 
- 操作系统
---
acme.sh作为自动签发ssl证书，为域名提供https的服务
v2ray作为代理使用
acme.sh要做到自动签发就需要在域名服务商哪授权token，用来acme.sh在验证域名所有权的时候使用，所以我们使用阿里云的平台，授权一个能够访问验证域名权限的子用户，并且创建一个Secret

在CentOS中创建Secret的临时key和Secret
```shell
export Ali_Key=""
export Ali_Secret=""
```

安装acme.sh,会在执行该命令的用户目录下生成一个.acme.sh目录，目录为隐藏目录，可以用ls -a查看
```shell
curl  https://get.acme.sh | sh
```

为指定的域名请求证书
```shell
/root/.acme.sh/acme.sh   --issue   --dns dns_ali   -d ajiehome.online  -d www.ajiehome.online
# 或
/root/.acme.sh/acme.sh   --issue   --dns dns_ali   -d v.ajiehome.online
```

将生成的域名ssl密钥文件拷贝到指定的目录
```shell
/root/.acme.sh/acme.sh --install-cert -d ajiehome.online  \
--key-file       /etc/nginx/ssl/ajiehome.online/ajiehome.online.key  \
--fullchain-file /etc/nginx/ssl/ajiehome.online/ajiehome.online.cert \
--reloadcmd     "service nginx force-reload"
```
然后就是nginx的配置项
```conf
server {
        listen  80;
        listen [::]:80;
        server_name ajiehome.online;

        root /usr/share/nginx/ajiehome.online;

        index index.html index.htm;

        rewrite ^(.*)$ https://${server_name}$1 permanent;

        location / {

        }
}
server {

        listen 443 ssl;
        listen [::]:443 ssl;
        server_name ajiehome.online;

        root /usr/share/nginx/ajiehome.online;
        index index.html inex.htm;


        location / {

                proxy_pass http://127.0.0.1:8090;

        }

        ssl_certificate /etc/nginx/ssl/ajiehome.online/ajiehome.online.cert;
        ssl_certificate_key /etc/nginx/ssl/ajiehome.online/ajiehome.online.key;

        ssl_session_timeout 1d;
        ssl_session_cache shared:MozSSL:10m;
        ssl_session_tickets off;

        ssl_protocols         TLSv1.2 TLSv1.3;
        ssl_ciphers           ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
        ssl_prefer_server_ciphers off;

}
```