---
title: GO Dockerfile
date: 2018-04-28 02:01:52
categories:
- Ops
- Docker
- Dockerfile
tags:
- ops
- docker
- dockerfile
---
## GO Deploy

{% codeblock Dockerfile %}
    FROM       golang
    MAINTAINER pandora@crazyguys.me

    ENV        DATABASE_ADDRESS="database" \
               REDIS_ADDRESS="redis:6379"

    WORKDIR    $GOPATH

    COPY       * ./src/_project_/

    RUN        apt-get -yqq update && \
               ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime && \
               go get _project_ && \
               go build _project_

    CMD ["./_project_"]
{% endcodeblock %}

database, redis, _project_ 을 각각 상황에 맞게 수정