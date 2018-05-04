---
title: NGINX SSL 적용하기 (LetsEncrypt 사용)
date: 2018-04-27 12:00:08
categories:
- Ops
- Nginx
tags:
- ops
- nginx
- ssl
- letsencrypt
---
![](/images/nginx/letsencrypt-logo-horizontal.svg)

NGINX에서 무료 SSL 중 가장 유명한 LetsEncrypt를 사용하여 가지고 있는 도메인을 SSL을 통하여 연결하는 방법입니다.

[Let's Encrypt](https://letsencrypt.org/)

## CentOS

{% codeblock %}
    $ yum install epel-release
    $ yum install certbot
{% endcodeblock %}
	
## AWS

{% codeblock %}
    $ wget https://dl.eff.org/certbot-auto
    $ chmod a+x certbot-auto
    $ ./certbot-auto --debug
{% endcodeblock %}
	
## 도메인 설정

사용하는 네임서버를 통하여 서버와 도메인 연결
[Cloudflare](https://www.cloudflare.com/) 추천

## HTTP 라우팅 설정

{% codeblock %}
    $ sudo vim /etc/nginx/conf.d/default.conf
{% endcodeblock %}
	
server_name 에 도메인 추가
ex) server_name localhost spoved.org;

{% codeblock %}
    $ sudo systemctl reload nginx
{% endcodeblock %}
	
## 방화벽 설정

firewall 기준

{% codeblock %}
    $ firewall-cmd --permanent --zone=public --add-service=http
    $ firewall-cmd --permanent --zone=public --add-service=https
    $ firewall-cmd --reload
{% endcodeblock %}
	
## letsencrypt.ini

{% codeblock %}
    $ cat <<EOT >> letsencrypt-config/ssl.ini
    # This is an example of the kind of things you can do in a configuration file.
    # All flags used by the client can be configured here. Run Certbot with
    # "--help" to learn more about the available options.
    
    # Use a 4096 bit RSA key instead of 2048
    rsa-key-size = 4096
    
    # Uncomment and update to register with the specified e-mail address
    email = pandora@crazyguys.me
    
    # Uncomment and update to generate certificates for the specified
    # domains.
    domains = spoved.org, www.spoved.org
    
    # Uncomment to use a text interface instead of ncurses
    # text = True
    
    # Uncomment to use the standalone authenticator on port 443
    # authenticator = standalone
    # standalone-supported-challenges = tls-sni-01
    
    # Uncomment to use the webroot authenticator. Replace webroot-path with the
    # path to the public_html / webroot folder being served by your web server.
    authenticator = webroot
    webroot-path = /usr/share/nginx/html
    EOT
{% endcodeblock %}
	
## 실제 SSL 적용

{% codeblock %}
    $ certbot certonly -c letsencrypt-config/ssl.ini
    $ ls -al /etc/letsencrypt/live
{% endcodeblock %}
	
성공하면 key가 생성됨.
nginx config 파일 설정

{% codeblock %}
    server {
      listen 80;
      server_name spoved.org;
      return 301 https://$host$request_uri;
    }
    
    server {
      listen 443 ssl;
      server_name  spoved.org;
      ssl_certificate /etc/letsencrypt/live/spoved.org/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/spoved.org/privkey.pem;
    
      location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
      }
    }
{% endcodeblock %}
	
성공 하면 nginx 설정 새로 읽어들이도록

{% codeblock %}
    $ sudo systemctl reload nginx
{% endcodeblock %}

적용 후 사이트에 접속을 해보면 Let's Encypt Authority X3 인증서가 적용된 것을 확인 가능!

![](/images/nginx/letsencrypt.jpg)

## 인증서 자동 갱신 CRONTAB

{% codeblock %}
    $ crontab -e
    0 1 * * * certbot renew
    0 1 * * * systemctl reload nginx
{% endcodeblock %}