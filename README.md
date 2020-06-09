# mall learning tutorial
<p>
  <a href="https://github.com/macrozheng/mall"><img src="https://img.shields.io/badge/Back--end-mall-blue" alt="Back-end project"></a>
  <a href="https://github.com/kratos47mhs/mall-admin-web"><img src="https://img.shields.io/badge/Front--end-mall--admin--web-RGB(44%2C%200%2C%2010%2C0)" alt="Front-end project"></a>
</p>

## Synopsis
mall learning tutorial, comprehensive analysis of architecture, business, and technical points. The mall project (25 k+star) is a set of e-commerce system that is implemented using mainstream technology at this stage.
Covering Spring Boot 2.1.3, My Batis 3.4.6, Elasticsearch 6.2.2, Rabbit MQ 3.7.15, Redis 3.2, Mongodb 3.2, Mysql 5.7 and other technologies, using Docker containerized deployment.

## Project Address
- Back-end project：[https://github.com/kratos47mhs/mall](https://github.com/kratos47mhs/mall)
- Front-end project：[https://github.com/kratos47mhs/mall-admin-web](https://github.com/kratos47mhs/mall-admin-web)

## Demo
- Main address：[http://www.kratos47mhs.com](http://www.kratos47mhs.com)
- Alternate address：[https://kratos47mhs.github.io/mall-learning](https://kratos47mhs.github.io/mall-learning)

## Introduction
- [Mall architecture and function overview](https://juejin.im/post/5cf7c305e51d4510b71da5c5)
- [Knowledge points required for mall learning (recommended information)](https://juejin.im/post/5cf7c3aef265da1ba84a7fdc)

## Architecture articles
> Teach you how to build a project skeleton that Mall is using

- [Mall integrates Spring Boot+MyBatis to build a basic skeleton](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_01)
- [Mall integrates Swagger-UI to achieve online API documentation](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_02)
- [Mall integrates Redis to achieve cache function](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_03)
- [Mall integrates Spring Security and JWT to achieve authentication and authorization (1)](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_04)
- [Mall integrates Spring Security and JWT to achieve authentication and authorization (2)](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_05)
- [Mall integrates Spring Task to implement timed tasks](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_06)
- [Mall integrates Elasticsearch to realize product search](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_07)
- [Mall integrates Mongodb to achieve document operation](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_08)
- [Mall integrates Rabbit MQ to realize delayed message](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_09)
- [Mall integrates OSS to realize file upload](https://kratos47mhs.github.io/mall-learning/#/architect/mall_arch_10)

## Business articles
> Comprehensive analysis of the database table structure used in mall

- [Mall database table structure overview](https://kratos47mhs.github.io/mall-learning/#/database/mall_database_overview)
- [Product module database table analysis (1)](https://kratos47mhs.github.io/mall-learning/#/database/mall_pms_01)
- [Product module database table analysis (2)](https://kratos47mhs.github.io/mall-learning/#/database/mall_pms_02)
- [Order module database table analysis (1)](https://kratos47mhs.github.io/mall-learning/#/database/mall_oms_01)
- [Order module database table analysis (2)](https://kratos47mhs.github.io/mall-learning/#/database/mall_oms_02)
- [Order module database table analysis (3)](https://kratos47mhs.github.io/mall-learning/#/database/mall_oms_03)
- [Sale module database table analysis (1)](https://kratos47mhs.github.io/mall-learning/#/database/mall_sms_01)
- [Sale module database table analysis (2)](https://kratos47mhs.github.io/mall-learning/#/database/mall_sms_02)
- [Sale module database table analysis (3)](https://kratos47mhs.github.io/mall-learning/#/database/mall_sms_03)

## Technical Essentials
> Analysis of technical points of some functions in mall

- [A pit stepped on during the use of MyBatis Generator](https://kratos47mhs.github.io/mall-learning/#/technology/mybatis_mapper)
- [Use AOP to record interface access logs in Spring Boot applications](https://kratos47mhs.github.io/mall-learning/#/technology/aop_log)
- [Spring Boot application integrates ELK to realize log collection](https://kratos47mhs.github.io/mall-learning/#/technology/mall_tiny_elk)
- [Separation of front and back-end projects, how to solve cross-domain problems](https://kratos47mhs.github.io/mall-learning/#/technology/springboot_cors)
- [Java 8 has been out for so long, Stream API?](https://kratos47mhs.github.io/mall-learning/#/technology/java_stream)
- [Only four steps are required to integrate Spring Security+JWT to achieve login authentication!](https://kratos47mhs.github.io/mall-learning/#/technology/springsecurity_use)
- [Separated front and back-end projects, how to achieve elegant file storage!](https://juejin.im/post/5e0ca2266fb9a0484a45969c)
- [The front-end and back-end separation projects introduce a problem encountered by Spring Cloud Gateway!](https://kratos47mhs.github.io/mall-learning/#/technology/gateway_cors)

## Deployment
> mall development and production environment

- [Mall deployment in Windows environment](https://juejin.im/post/5d1362de51882551fe065b61)
- [Mall deployment in Linux environment (based on Docker container)](https://juejin.im/post/5d1802ab6fb9a07f0a2df5ae)
- [Mall deployment in Linux environment (based on Docker Compose)](https://juejin.im/post/5d1c98d66fb9a07ebf4b8ad5)
- [Automated deployment of mall in Linux environment (based on Jenkins)](https://juejin.im/post/5e15d7b16fb9a0484f5c3931)
- [Installation and deployment of mall front-end projects](https://juejin.im/post/5d2c7c6b518825076377d7b9)
- [Mall-swarm deployment in Windows environment](https://juejin.im/post/5de3c1a35188256e855b6e54)
- [Mall-swarm deployment in Linux environment (based on Docker container)](https://juejin.im/post/5de65bffe51d4557f71a5ec1)
- [Automated deployment under the microservice architecture is implemented using Jenkins!](https://juejin.im/post/5dfa250e51882579dc6f7c90)


## Advanced
> A set of Spring Cloud tutorials covering most core components, including Spring Cloud Alibaba and distributed transactions Seata, based on Spring Cloud Greenwich and Spring Boot 2.1.7

- [Spring Cloud overall architecture overview](https://juejin.im/post/5d764f05e51d4561fb04bfd7)
- [Spring Cloud Eureka: service registration and discovery](https://juejin.im/post/5d78cd53f265da03d55e8351)
- [Spring Cloud Ribbon: load balancing service invocation](https://juejin.im/post/5d7f9006f265da03951a260c)
- [Spring Cloud Hystrix: Service fault tolerance protection](https://juejin.im/post/5d822d27e51d45621479ad92)
- [Hystrix Dashboard: circuit breaker execution monitoring](https://juejin.im/post/5d88cb58f265da03e4679eff)
- [Spring Cloud OpenFeign: Declarative service invocation based on Ribbon and Hystrix](https://juejin.im/post/5d9c85c3e51d45782c23fab6)
- [Spring Cloud Zuul: API Gateway Service](https://juejin.im/post/5d9f2dea6fb9a04e3e724067)
- [Spring Cloud Config: external centralized configuration management](https://juejin.im/post/5da4709af265da5baa5b06ac)
- [Spring Cloud Bus: Message bus](https://juejin.im/post/5da70d1351882509615bea34)
- [Spring Cloud Sleuth: distributed request link tracking](https://juejin.im/post/5dadb4d36fb9a04e02409a7d)
- [Spring Cloud Consul: Service governance and configuration center](https://juejin.im/post/5db05582f265da4d4c20180f)
- [Spring Cloud Gateway: Next-generation API gateway service](https://juejin.im/post/5db6eed6518825644076d0b6)
- [Spring Boot Admin: Microservice application monitoring](https://juejin.im/post/5db98a2d518825649c730f81)
- [Spring Cloud Security: Getting started with Oauth2](https://juejin.im/post/5dc013bae51d456e817cec30)
- [Spring Cloud Security: Oauth2 combined with JWT](https://juejin.im/post/5dc2bec6f265da4d4f65bebe)
- [Spring Cloud Security: Oauth2 achieves single sign-on](https://juejin.im/post/5dc95a675188256e040db43f)
- [Spring Cloud Alibaba: Nacos is used as a registration center and configuration center](https://juejin.im/post/5dcbf7bc5188250d1f5a78ea)
- [Spring Cloud Alibaba: Sentinel achieves fusing and current limiting](https://juejin.im/post/5dd29bece51d4561e80f9053)
- [Use Seata to completely solve the distributed transaction problem in Spring Cloud](https://juejin.im/post/5dd53a9d5188255d35425a08)
- [The correct posture to create and start a Spring Boot application in IDEA](https://juejin.im/post/5d8b69366fb9a04e3348b06c)

## Reference
> Tutorial on the use of mall related technologies

- [IDEA common settings and recommended plugins](https://juejin.im/post/5d0458085188256aa76bc678)
- [Developer must have Mysql command](https://juejin.im/post/5d00fd40f265da1bb67a11b3)
- [Linux commands for developers](https://juejin.im/post/5d0253845188255e1305c741)
- [Use of Linux Firewall and Iptables](https://juejin.im/post/5d0253fe6fb9a07edb39420d)
- [Navicat practical function: data backup and structure synchronization](https://juejin.im/post/5d00fc865188255fc6384126)
- [Developer essential Docker commands](https://juejin.im/post/5d0781f56fb9a07f014ef6be)
- [Build Docker image using Maven plugin](https://juejin.im/post/5d08e3d26fb9a07ed8424488)
- [Use Docker File to build Docker images for Spring Boot applications](https://juejin.im/post/5d0a25b76fb9a07ed524a438)
- [Use Docker Compose to deploy Spring Boot applications](https://juejin.im/user/5cf7c1d7f265da1bc07e28b7)
- [Postman: API interface debugging tool](https://juejin.im/post/5d5a9032e51d4561db5e3a4a)
- [Set up your own Git repository in 10 minutes](https://juejin.im/post/5d63d600e51d453c135c5af3)
- [Git operation in IDEA, it is enough to read this article!](https://juejin.im/post/5d667fc6e51d453b5d4d8da5)
- [The commonly used tool classes and methods in Hutool](https://juejin.im/post/5d6fb7b0e51d4561c67840de)
- [Virtual machine installation and use of Linux, it is enough to read this article!](https://juejin.im/post/5ddfd1665188256ec024cb7c)
- [You must have no idea about these wonderful uses of Nginx!](https://juejin.im/post/5dee499151882512444014eb)
- [One-click packaging and deployment of Spring Boot applications using Jenkins, that's it 6!](https://juejin.im/post/5df780d3e51d4557ff140b30)
- [One-click packaging and deployment of front-end applications using Jenkins, that's it 6!](https://juejin.im/post/5e0360e951882512400ae872)
- [Github standard star 19 K+Star, 10 minutes self-built object storage service!](https://juejin.im/post/5e09f8fbe51d4575a46f05e5)
- [My Sql master-slave replication, from principle to practice!](https://juejin.im/post/5e1daba46fb9a02fb75d5e92)


## No public

In the serialization of the full set of learning tutorials for the mall project, **follow the public account** to get it for the first time.

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
