---
title: Nginx install
date: 2018-04-27 11:37:03
categories:
- Ops
- Nginx
tags:
- ops
- nginx
---
![](/images/nginx/nginx.png)
## 한국 서버가 아닌 경우

{% codeblock %}
    $ sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
{% endcodeblock %}

## NGINX

nginx.repo 추가

{% codeblock %}
    $ cat <<EOT >> /etc/yum.repos.d/nginx.repo
    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/\$releasever/\$basearch/
    gpgcheck=0
    enabled=1
    EOT
{% endcodeblock %}

repo 추가 후 설치

{% codeblock %}
    $ sudo yum install nginx
    $ sudo systemctl enable nginx.service
    $ sudo systemctl start nginx
{% endcodeblock %}

service 를 쓰는 경우

{% codeblock %}
    $ sudo service nginx start
{% endcodeblock %}