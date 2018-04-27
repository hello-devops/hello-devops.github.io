---
title: Spring Boot database
date: 2018-04-27 23:34:29
categories:
- Dev
- SpringBoot
tags:
- dev
- springboot
- database
---
## MySQL 연동

![](/images/mysql/mysql-logo.svg)

{% codeblock build.gradle %}
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    
    // https://mvnrepository.com/artifact/mysql/mysql-connector-java
    runtime group: 'mysql', name: 'mysql-connector-java', version: '6.0.6'
{% endcodeblock %}

{% codeblock application.properties %}
    spring.jpa.show-sql=true
    spring.datasource.url=jdbc:mysql://127.0.0.1:3306/databasename?useUnicode=true&charaterEncoding=utf-8&useSSL=false
    spring.datasource.username=
    spring.datasource.password=
    spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
    spring.datasource.testOnBorrow=true
    spring.datasource.validationQuery=SELECT 1
    spring.jpa.hibernate.use-new-id-generator-mappings=false
    spring.messages.basename=Resources
    spring.messages.encoding=UTF-8
    spring.jpa.database-platform=org.hibernate.dialect.MySQL5Dialect
{% endcodeblock %}

{% codeblock User.java lang:java %}
    @Entity
    @Table
    public class User {
        @Id
        @GeneratedValue
        Long id;
        String name;
    }
{% endcodeblock %}

{% codeblock UserRepository.java lang:java %}
    public interface UserRepository extends JpaRepository<User, Long> {
        User findOneById(long id);
        User findOneByName(String name);
    }
{% endcodeblock %}

[JPA 공식문서](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)