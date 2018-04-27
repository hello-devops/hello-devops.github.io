---
title: Jenkins install
date: 2018-04-26 21:13:35
categories:
- Ops
- Jenkins
tags:
- ops
- jenkins
---
![](/images/jenkins/jenkins.png)
## JENKINS

{% codeblock %}
    $ sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
    $ sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
    $ sudo yum install jenkins
{% endcodeblock %}

> yum 을 사용하여 설치하면 추후 젠킨스를 업데이트하기 편합니다.

[Jenkins wiki
https://wiki.jenkins.io/display/JENKINS/Installing+Jenkins+on+Red+Hat+distributions](https://wiki.jenkins.io/display/JENKINS/Installing+Jenkins+on+Red+Hat+distributions)

## GIT

yum 사용
이 경우는 보통 구버전의 git이 설치됩니다.

{% codeblock %}
    $ sudo yum install git -y
{% endcodeblock %}
    
최신버전을 다운 받아 설치하는 경우

{% codeblock %}
    $ export GIT_VER=2.17.0
    $ wget https://www.kernel.org/pub/software/scm/git/git-$GIT_VER.tar.gz
    $ tar -zxf git-$GIT_VER.tar.gz
    
    $ sudo yum install curl-devel expat-devel gettext-devel openssl-devel perl-devel zlib-devel gcc -y
    
    $ cd git-$GIT_VER
    $ sudo make prefix=/usr all
    $ sudo make prefix=/usr install
{% endcodeblock %}
    
[Kernel git
https://www.kernel.org/pub/software/scm/git/](https://www.kernel.org/pub/software/scm/git/)

## JDK

{% codeblock %}
    $ yum list java*jdk-devel
    Available Packages
    java-1.6.0-openjdk-devel.x86_64           1:1.6.0.41-1.13.13.1.el7_3           base   
    java-1.7.0-openjdk-devel.x86_64           1:1.7.0.171-2.6.13.0.el7_4           updates
    java-1.8.0-openjdk-devel.i686             1:1.8.0.161-0.b14.el7_4              updates
    java-1.8.0-openjdk-devel.x86_64           1:1.8.0.161-0.b14.el7_4              updates
{% endcodeblock %}

설치할 JDK 버전 선택하여 설치
이 경우는 1.8.0 버전 JDK를 설치

{% codeblock %}
    $ yum install java-1.8.0-openjdk-devel.x86_64 -y
{% endcodeblock %}

설치된 JDK 확인

{% codeblock %}
    $ ls /usr/lib/jvm | grep java
    java
    java-1.8.0
    java-1.8.0-openjdk
    java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64
    java-openjdk
{% endcodeblock %}

[참고자료
http://zetawiki.com/wiki/CentOS_JDK_%EC%84%A4%EC%B9%98](http://zetawiki.com/wiki/CentOS_JDK_%EC%84%A4%EC%B9%98)

## GRADLE

{% codeblock %}
    $ sudo yum install zip unzip -y
    $ curl -s https://get.sdkman.io | bash
    $ bash
    $ sdk install gradle 4.7
{% endcodeblock %}

[Gradle Installation
https://gradle.org/gradle-download/](https://gradle.org/gradle-download/)