---
layout: post
comments: true
title:  "Spring Social with Neo4j"
date:   2015-11-07 19:47:34 +1300
categories: blog
---

Few weeks ago I wanted to set up a web application project which uses `Spring MVC` with `Neo4j database server`. So I decided to utilize Spring's modules as much as possible and for my requirements Spring framework was the most clean and simple answer. I'm quite a fan of Spring data as well. If you google for `Spring data` you will be able to find about the magical things which Spring data framework can do for you. So basically I was tempted to use `Spring Data Neo4j` for database access layer. Basic implementation of the development framework of the app was pretty quick. Because Spring modules had everything provided which I need.

Next requirement for me was implementing user authentication and authorization. So I went for Spring security and due to my requirement of allowing users to sign in via their social accounts I thought `Spring social` module would be the perfect fit for my app's user authentication.   

I found an online blog which explains how to integrate `Spring social` and `Spring security` for an existing spring based project. I followed the steps provided in the blog article and everything was pretty smooth and worked magically up to the step where I need to persist the Spring social user's connection data in the database.

Spring social project has an in built data repository implementation for relational databases called `JdbcUsersConnectionRepository` which is capable of persisting and reading social users connection data. But they don't have a proper implementation which supports `NoSQL` databases such as `MongoDB` or `Neo4j`. So I had to write my own `Database Access Object` or we call `Data Repository` implementation for Neo4j according to the requirements of Spring social. I did bit of google search to see if some one else have done the same thing and has implemented the Neo4j support for Spring social already. All I found was the github project [Spring-Social-Neo4](https://github.com/maciossek/spring-social-neo4j) which was under development. I took sometime and completed the project with the support of [Mathias](https://github.com/maciossek) so now we have a tested implementation of Neo4j data repository for Spring social project which we can reuse.

#### How to use Spring Social Neo4j lib

1) Add repository to your pom.xml file
    
    <repositories>
        <repository>
            <id>spring-social-neo4j</id>
            <url>https://raw.github.com/shanika/spring-social-neo4j/mvn-repo/</url>
            <snapshots>
                <enabled>true</enabled>
                <updatePolicy>always</updatePolicy>
            </snapshots>
        </repository>
    </repositories>

2) Add dependency to your pom.xml file 
    
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-social-neo4j</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </dependency>

3) Configure UsersConnectionRepository in spring social config

  I have injected Neo4jServer class from Spring data neo4j to get the Neo4j Server URL which is already configured in Spring Data Neo4j. We can directly pass the URL string without using Neo4jServer as well. 
    
    @Autowired
    private Neo4jServer server;
    
    @Override
    public UsersConnectionRepository getUsersConnectionRepository(ConnectionFactoryLocator connectionFactoryLocator) {

        return new Neo4jUsersConnectionRepository(server.url(), connectionFactoryLocator, Encryptors.noOpText());
    } 
    