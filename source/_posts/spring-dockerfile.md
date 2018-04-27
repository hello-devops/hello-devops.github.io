---
title: Spring Dockerfile
date: 2018-04-28 01:38:17
categories:
- Ops
- Docker
- Dockerfile
tags:
- ops
- docker
- dockerfile
---
## WAR Deploy

{% codeblock Dockerfile %}
    FROM       tomcat
    MAINTAINER pandora@crazyguys.me
    
    ENV        SPRING_PROFILES_ACTIVE="prod"
    
    WORKDIR    /usr/local/tomcat
    
    RUN        apt-get -yqq update && \
               rm -rf ./webapps/* && \
               ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
    
    COPY       ROOT.war ./webapps
    
    EXPOSE     8080
    CMD        ["catalina.sh", "run"]
{% endcodeblock %}

war 파일명이 ROOT.war