The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Mall deployment in Linux environment (based on Docker Compose)
> The simplest way to deploy mall under Linux is to use two docker-compose scripts to complete the deployment. The first script is used to deploy services that Mall depends on (mysql, redis, nginx, rabbitmq, elasticsearch, kibana, mongo), and the second script is used to deploy applications in mall (mall-admin, mall-search, mall -portal).

## Docker environment to build and use
Specific reference：[Developer essential Docker commands](https://mp.weixin.qq.com/s/d_CuljDTJq680NTndAay8g)

## Docker-compose environment setup and use
Specific reference：[Use Docker Compose to deploy Spring Boot applications](https://mp.weixin.qq.com/s/iMl9bJ4SxUsNHBbiS5VUcw)

## docker-compose deployment of mall project

### Operating configuration requirements
Center OS 7.6 version, recommend more than 4G memory

### 部署相关文件

- Database script mall.sql：https://github.com/kratos47mhs/mall/blob/master/document/sql/mall.sql
- nginx configuration file nginx.conf：https://github.com/kratos47mhs/mall/blob/master/document/docker/nginx.conf
- docker-compose-env.yml：https://github.com/kratos47mhs/mall/tree/master/document/docker/docker-compose-env.yml
- docker-compose-app.yml：https://github.com/kratos47mhs/mall/tree/master/document/docker/docker-compose-app.yml

### Preparation before deployment

#### Package and upload the mirror of the mall application
Need to package docker images of mall-admin, mall-search, mall-portal, specific reference：[Use the Maven plugin to build Docker images for Spring Boot applications](https://mp.weixin.qq.com/s/q2KDzHbPkf3Q0EY8qYjYgw)

#### Download all Docker images that need to be installed

```shell
docker pull mysql:5.7
docker pull redis:3.2
docker pull nginx:1.10
docker pull rabbitmq:3.7.15-management
docker pull elasticsearch:6.4.0
docker pull kibana:6.4.0
docker pull mongo:3.2
```

#### elasticsearch

- Need to set the system kernel parameters, otherwise it will not start due to insufficient memory.
```shell
# Change settings
sysctl -w vm.max_map_count=262144
# Make it effective immediately
sysctl -p
```
- Need to create /mydata/elasticsearch/data directory and set permissions, otherwise it will fail to start due to unauthorized access.
```shell
# Create a directory
mkdir /mydata/elasticsearch/data/
# Create and change the permissions of this directory
chmod 777 /mydata/elasticsearch/data
```

#### nginx

You need to copy the nginx configuration file, otherwise it will fail to start because there is no configuration file when mounting.
```shell
# After creating the directory, upload the nginx.conf file to the directory
mkdir /mydata/nginx/
```

### Execute the docker-compose-env.yml script
> Upload the file to the Linux server and execute the docker-compose up command to start all services that Mall depends on.

```yml
version: '3'
services:
  mysql:
    image: mysql:5.7
    container_name: mysql
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root #Set root account password
    ports:
      - 3306:3306
    volumes:
      - /mydata/mysql/data/db:/var/lib/mysql #Data volume mounting
      - /mydata/mysql/data/conf:/etc/mysql/conf.d #Configuration Thematic mounting
      - /mydata/mysql/log:/var/log/mysql #Log Thematic mounting
  redis:
    image: redis:3.2
    container_name: redis
    command: redis-server --appendonly yes
    volumes:
      - /mydata/redis/data:/data #Data volume mounting
    ports:
      - 6379:6379
  nginx:
    image: nginx:1.10
    container_name: nginx
    volumes:
      - /mydata/nginx/nginx.conf:/etc/nginx/nginx.conf #Configuration volume mounting
      - /mydata/nginx/html:/usr/share/nginx/html #Static resource root directory mount
      - /mydata/nginx/log:/var/log/nginx #Log volume mounting
    ports:
      - 80:80
  rabbitmq:
    image: rabbitmq:3.7.15-management
    container_name: rabbitmq
    volumes:
      - /mydata/rabbitmq/data:/var/lib/rabbitmq #Data volume mounting
      - /mydata/rabbitmq/log:/var/log/rabbitmq #Log volume mounting
    ports:
      - 5672:5672
      - 15672:15672
  elasticsearch:
    image: elasticsearch:6.4.0
    container_name: elasticsearch
    environment:
      - "cluster.name=elasticsearch" #Set the cluster name to elasticsearch
      - "discovery.type=single-node" #Start in single node mode
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m" #Set use jvm memory size
    volumes:
      - /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins #Plugin volume mounting
      - /mydata/elasticsearch/data:/usr/share/elasticsearch/data #Data volume mounting
    ports:
      - 9200:9200
  kibana:
    image: kibana:6.4.0
    container_name: kibana
    links:
      - elasticsearch:es #You can use the domain name es to access the elasticsearch service
    depends_on:
      - elasticsearch #kibana Start after elasticsearch starts
    environment:
      - "elasticsearch.hosts=http://es:9200" #Set the address to access elasticsearch
    ports:
      - 5601:5601
  mongo:
    image: mongo:3.2
    container_name: mongo
    volumes:
      - /mydata/mongo/db:/data/db #Data volume mounting
    ports:
      - 27017:27017
```
After uploading, execute the following command in the current directory：
```shell
docker-compose -f docker-compose-env.yml up -d
```
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/refer_screen_103.png)

### Make the following settings for dependent services

When all dependent services are started, you need to make some settings for the following services.

#### mysql

> Need to create a mall database and create an object reader that can be accessed remotely.

- Copy the mall.sql file to the / directory of the mysql container:
```shell
docker cp /mydata/mall.sql mysql:/
```
- Enter the mysql container and perform the following operations:
```shell
#Enter the mysql container
docker exec -it mysql /bin/bash
#Connect to mysql service
mysql -uroot -proot --default-character-set=utf8
#Create a remote access user
grant all privileges on *.* to 'reader' @'%' identified by '123456';
#Create mall database
create database mall character set utf8;
#Use mall database
use mall;
#Import mall.sql script
source /mall.sql;
```

#### elasticsearch

> Need to install the Chinese word breaker IK Analyzer, and restart.

```shell
docker exec -it elasticsearch /bin/bash
#This command needs to be run in the container
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.4.0/elasticsearch-analysis-ik-6.4.0.zip
docker restart elasticsearch
```

#### rabbitmq

> You need to create a mall user and set the virtual host to /mall.

- Visit management page address：[http://192.168.3.101:15672/](http://192.168.3.101:15672/)
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/refer_screen_76.png)
- Enter account password and login：guest guest
- Create an account and set its role as an administrator：mall mall
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/refer_screen_77.png)
- Create a new virtual host as：/mall
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/refer_screen_78.png)
- Click the mall user to enter the user configuration page
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/refer_screen_79.png)
- Configure the permission of the virtual host for the mall user
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/refer_screen_80.png)

### Execute the docker-compose-app.yml script

> Upload the file to the linux server and execute the docker-compose up command to start all the mall applications.

```yml
version: '3'
services:
  mall-admin:
    image: mall/mall-admin:1.0-SNAPSHOT
    container_name: mall-admin
    ports:
      - 8080:8080
    external_links:
      - mysql:db #You can use the domain name db to access the mysql service
  mall-search:
    image: mall/mall-search:1.0-SNAPSHOT
    container_name: mall-search
    ports:
      - 8081:8081
    external_links:
      - elasticsearch:es #You can use the domain name es to access the elasticsearch service
      - mysql:db #You can use the domain name db to access the mysql service
  mall-portal:
    image: mall/mall-portal:1.0-SNAPSHOT
    container_name: mall-portal
    ports:
      - 8085:8085
    external_links:
      - redis:redis #You can use the domain name redis to access the redis service
      - mongo:mongo #You can use the domain name mongo to access the mongo service
      - mysql:db #You can use the domain name db to access the mysql service
      - rabbitmq:rabbit #You can use the domain name rabbit to access the rabbitmq service
```

After uploading, execute the following command in the current directory:
```shell
docker-compose -f docker-compose-app.yml up -d
```
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/refer_screen_104.png)

### Turn on the firewall to access all services on other hosts

```shell
systemctl stop firewalld
```

### So far all services have been started normally

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/refer_screen_105.png)

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/refer_screen_106.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
