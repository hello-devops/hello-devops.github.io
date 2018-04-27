---
title: Nginx Letsencrypt
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
	
## letsencrypt.ini

{% codeblock %}
    $ cat <<EOT >> letsencrypt-config/ssl.ini
    # This is an example of the kind of things you can do in a configuration file.
    # All flags used by the client can be configured here. Run Certbot with
    # "--help" to learn more about the available options.
    
    # Use a 4096 bit RSA key instead of 2048
    rsa-key-size = 4096
    
    # Uncomment and update to register with the specified e-mail address
    email = 
    
    # Uncomment and update to generate certificates for the specified
    # domains.
    domains = 
    
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
      client_max_body_size 100M;
      ssl_certificate /etc/letsencrypt/live/spoved.org/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/spoved.org/privkey.pem;
    
      location / {
        ...
      }
    }
{% endcodeblock %}
	
성공 하면 nginx 설정 새로 읽어들이도록

{% codeblock %}
    $ sudo systemctl reload nginx
{% endcodeblock %}
	
## 인증서 자동 갱신 CRONTAB

{% codeblock %}
    $ crontab -e
    0 1 * * * certbot renew
    0 1 * * * systemctl reload nginx
{% endcodeblock %}