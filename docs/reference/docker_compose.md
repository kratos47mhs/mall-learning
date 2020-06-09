In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) get it immediately.

# Use Docker Compose to deploy Spring Boot applications

> Docker Compose is a tool for defining and running multiple docker container applications. With Compose, you can use YAML files to configure your application services, and then use one command, you can deploy all the services you configure.

## Installation

### Download Docker Compose:
```shell
curl -L https://github.com/docker/compose/releases/download/1.26.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```
### The permissions to modify the file are executable:
```shell
chmod +x /usr/local/bin/docker-compose
```
### Check whether it has been successfully installed:
```shell
docker-compose --version
```
![](../images/refer_screen_96.png)

## Steps to use Docker Compose

- Use Dockerfile to define the application environment, which is generally needed when the initial mirroring behavior needs to be modified;
- Use docker-compose.yml to define the application services that need to be deployed in order to execute the script for one-time deployment;
- Use the docker-compose up command to deploy all application services at once.

## docker-compose.yml commonly used commands

### image
Specify the name of the running image
```yml
# Is running a mirror of mysql 5.7
image: mysql:5.7
```

### container_name
Configure the container name
```yml
# The container name is mysql
container_name: mysql
```

### ports
Specify host and container port mapping（HOST:CONTAINER）
```yml
# Map port 3306 of the host to port 3306 of the container
ports:
  - 3306:3306
```

### volumes
Mount the file or directory of the host machine into the container（HOST:CONTAINER）
```yml
# Mount external files into mysql container
volumes:
  - /mydata/mysql/log:/var/log/mysql
  - /mydata/mysql/data:/var/lib/mysql
  - /mydata/mysql/conf:/etc/mysql
```

### environment
Configure environment variables
```yml
# Set environment variables for mysqlroot account password
environment:
  - MYSQL_ROOT_PASSWORD=root
```

### links
Services to connect to other containers（SERVICE:ALIAS）
```yml
# You can use the database as the domain name to access the container with the service name as db
links:
  - db:database
```

## Common commands of Docker Compose

### Build, create, and start related containers:
```shell
# -d means run in the background (run Daemon mode)
docker-compose up -d
```
### Stop all related containers:
```shell
docker-compose stop
```
### List all container information:
```shell
docker-compose ps
```

## Deploy applications using Docker Compose

### Write docker-compose.yml file

> Docker Compose divides the managed container into three layers, engineering, service and container. In docker-compose.yml, all services are defined as a project, under the services node is the service, and under the service is the container. Between the container and the container, the service name can be used as the domain name for access. For example, in the mall-tiny-docker-compose service, the db mysql service can be accessed through the address jdbc:mysql://db:3306.

```yml
version: '3'
services:
  # Specify service name
  db:
    # Specify the image used by the service
    image: mysql:5.7
    # Designated container name
    container_name: mysql
    # Specify the port on which the service runs
    ports:
      - 3306:3306
    # Specify the file to be mounted in the container
    volumes:
      - /mydata/mysql/log:/var/log/mysql
      - /mydata/mysql/data:/var/lib/mysql
      - /mydata/mysql/conf:/etc/mysql
    # Specify the environment variables of the container
    environment:
      - MYSQL_ROOT_PASSWORD=root
  # Specify service name
  mall-tiny-docker-compose:
    # Specify the image used by the service
    image: mall-tiny/mall-tiny-docker-compose:0.0.1-SNAPSHOT
    # Designated container name
    container_name: mall-tiny-docker-compose
    # Specify the port on which the service runs
    ports:
      - 8080:8080
    # Specify the file to be mounted in the container
    volumes:
      - /etc/localtime:/etc/localtime
      - /mydata/app/mall-tiny-docker-compose/logs:/var/logs
```
**Note: If you encounter a mall-tiny-docker-compose service that cannot connect to mysql, you need to create a mall database in mysql and import the mall.sql script. For details, please refer to [Run mysql service and setup part] in [Build Docker image for Spring Boot application using Dockerfile] (https://mp.weixin.qq.com/s/U_OcNMpLAJJum_s9jbZLGg).**

### Use maven plugin to build mall-tiny-docker-compose image
![](../images/refer_screen_97.png)

**Note: You can refer to the problematic construction[Use the Maven plugin to build Docker images for Spring Boot applications](https://mp.weixin.qq.com/s/q2KDzHbPkf3Q0EY8qYjYgw)**

### Run Docker Compose command to start all services
First upload docker-compose.yml to the Linux server, and then run the following command in the current directory:
```shell
docker-compose up -d
```
![](../images/refer_screen_98.png)

Access interface document address http://192.168.3.101:8080/swagger-ui.html:

![](../images/refer_screen_94.png)

## Project source address

[https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-docker-compose](https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-docker-compose)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
