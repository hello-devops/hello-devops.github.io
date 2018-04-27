---
title: Nginx proxy
date: 2018-04-27 20:05:23
categories:
- Ops
- Nginx
tags:
- ops
- nginx
---
![](/images/nginx/nginx.png)

## 프록시

{% codeblock %}
    upstream backed {
      ip_hash;
      server localhost:8080;
    }
    
    server {
      listen 80;
      server_name ;
      location / {
        proxy_set_header Host \$http_host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_pass http://backend;
      }
    }
{% endcodeblock %}
    
server_name은 내용은 상황에 맞게

## 프록시 + SSL

{% codeblock %}
    upstream backed {
      ip_hash;
      server localhost:8080;
    }
    
    server {
      listen 80;
      server_name ;
      return 301 https://\$host\$request_uri;
    }
    
    server {
      listen 443 ssl;
      server_name ;
      ssl_certificate /etc/letsencrypt/live//fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live//privkey.pem;
    
      location / {
        proxy_set_header Host \$http_host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_pass http://backend;
      }
    }
{% endcodeblock %}

server_name과 ssl_certificate 내용은 상황에 맞게

[공식 문서](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)

## Proxy 접속이 안되는 경우

## (13: Permission denied) while connecting to upstream

{% codeblock %}
    $ cat /var/log/nginx/error.log
{% endcodeblock %}

{% codeblock %}
    ....
    2016/12/10 22:51:05 [crit] 813#813: *1 connect() to 127.0.0.1:8080 failed (13: Permission denied) while connecting to upstream, client: 0.0.0.0, server: spoved.org, request: "GET / HTTP/1.1", upstream: "http://127.0.0.1:8080/", host: "spoved.org", referrer: "https://spoved.org/"
    2016/12/10 22:51:05 [warn] 813#813: *1 upstream server temporarily disabled while connecting to upstream, client: 0.0.0.0, server: spoved.org, request: "GET / HTTP/1.1", upstream: "http://127.0.0.1:8080/", host: "spoved.org", referrer: "https://spoved.org/"
    ....
{% endcodeblock %}

해당 경우 아래 명령어 실행

{% codeblock %}
    $ setsebool -P httpd_can_network_connect on
{% endcodeblock %}

[stackoverflow](https://stackoverflow.com/questions/23948527/13-permission-denied-while-connecting-to-upstreamnginx)