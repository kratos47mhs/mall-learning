The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.

# Mall architecture and function overview

> Overview of mall architecture, functions and database structure

## Mall project introduction

The mall project is a set of e-commerce systems, including a front-end mall system and a back-end management system, based on SpringBoot+MyBatis. The front-end mall system includes modules such as home portal, product recommendation, product search, product display, shopping cart, order process, member center, customer service, and help center. The background management system includes modules for commodity management, order management, membership management, promotion management, operation management, content management, statistical reports, financial management, authority management, and settings.

## Project demo

- back-end project demo：[http://www.kratos47mhs.com/admin/index.html](http://www.kratos47mhs.com/admin/index.html)
- Mobile project demonstration：[http://www.kratos47mhs.com/app/index.html](http://www.kratos47mhs.com/app/index.html)

## Technology used in mall

> Mall is implemented using current mainstream technology, covering almost all technologies used in general projects.

Technology | Version | Description
----|----|----
Spring Boot | 2.1.3 | Container + MVC framework
Spring Security | 5.1.4 | Authentication and authorization framework
MyBatis | 3.4.6 | ORM framework  
MyBatisGenerator | 1.3.3 | Data layer code generation
PageHelper | 5.1.8 | My Batis physical paging plugin
Swagger-UI | 2.7.0 | Document production tools
Elasticsearch | 6.2.2 | search engine
RabbitMq |3.7.14 | message queue
Redis | 3.2 | Distributed cache
MongoDb | 3.2 | No Sql database
Docker | 18.09.0 | Application container engine
Druid | 1.1.10 | Database connection pool
OSS | 2.5.0 | Object storage
JWT | 0.9.0 | JWT login support
Lombok | 1.18.6 | reduce boilerplate code for model/data objects

## Overview of the functions implemented by mall

> For details, please see the demo address and experience it for yourself：[http://www.kratos47mhs.com/admin/index.html](http://www.kratos47mhs.com/admin/index.html)

- Product module
  - Product management
  - Product category management
  - Product type management
  - Brand management
- Order module
  - Order management
  - Order settings
  - Return application processing
  - returnReasonSetting
- Sale module
  - Flash Promotion activity management
  - Discount price management
  - Brand recommendation management
  - New product recommendation management
  - Popular recommendation management
  - Subject recommendation management
  - Homepage advertising management

## Mall database table overview
> The mall project currently has 71 data sheets, and the business logic has a certain complexity. Usually, it is enough to make a project reference.

![Mall database table display](../images/mall_mysql_all.png)

### Database table prefix description

- cms_*：Content management module related table
- oms_*：Order management module related table
- pms_*：Product module related table
- sms_*：Sale module related table
- ums_*：User module related table

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
