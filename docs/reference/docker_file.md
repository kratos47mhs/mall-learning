In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) get it immediately.

# Use Dockerfile to build Docker images for Spring Boot applications

> Last time I wrote an article[Build Docker image using Maven plugin](https://mp.weixin.qq.com/s/q2KDzHbPkf3Q0EY8qYjYgw) ，Describes the way to build a docker image through docker-maven-plugin. This method needs to rely on the self-built Registry image warehouse. This article will describe another way to use Dockerfile to build a docker image. This method does not need to rely on a self-built image warehouse, only the application jar package and a Dockerfile file.

## Dockerfile common instructions

### ADD
Used to copy files, format:
```
ADD <src> <dest>
```
Examples：
```shell
# Copy the mall-tiny-docker-file.jar package in the current directory to the / directory of the docker container
ADD mall-tiny-docker-file.jar /mall-tiny-docker-file.jar
```

### ENTRYPOINT
Specify the command to be executed when the docker container starts, the format:
```
ENTRYPOINT ["executable", "param1","param2"...]
```
Examples：
```shell
# Specify to run the jar package when the docker container starts
ENTRYPOINT ["java", "-jar","/mall-tiny-docker-file.jar"]
```

### ENV
Used to set environment variables, format:
```
ENV <key> <value>
```
Examples:
```shell
# Set root password when mysql is running
ENV MYSQL_ROOT_PASSWORD root
```

### EXPOSE
Declare the port that needs to be exposed (only declare that the port will not be opened), format:
```
EXPOSE <port1> <port2> ...
```
Examples：
```shell
# Declare that the service is running on port 8080
EXPOSE 8080
```

### FROM
Specify the required base image, format:
```
FROM <image>:<tag>
```
Examples:
```shell
# This image needs to depend on the image of java 8
FROM java:8
```

### MAINTAINER
Specify the name of the maintainer, format:
```
MAINTAINER <name>
```
Examples:
```shell
MAINTAINER kratos47mhs
```

### RUN
Commands executed during the container construction process, we can use this command to customize the behavior of the container, such as installing some software, creating some files, etc., format:
```
RUN <command>
RUN ["executable", "param1","param2"...]
```
Examples:
```shell
# During the container building process, a mall-tiny-docker-file.jar file needs to be created in the / directory
RUN bash -c 'touch /mall-tiny-docker-file.jar'
```

## Use Dockerfile to build Spring Boot application image

### Write Dockerfile

```shell
# The base image that this image needs to depend on
FROM java:8
# Copy the jar package in the current directory to the / directory of the docker container
ADD mall-tiny-docker-file-0.0.1-SNAPSHOT.jar /mall-tiny-docker-file.jar
# Create a mall-tiny-docker-file.jar file during operation
RUN bash -c 'touch /mall-tiny-docker-file.jar'
# Declare that the service is running on port 8080
EXPOSE 8080
# Specify to run the jar package when the docker container starts
ENTRYPOINT ["java", "-jar","/mall-tiny-docker-file.jar"]
# Specify the name of the maintainer
MAINTAINER kratos47mhs
```

### Package the application using maven

Double-click the package command in IDEA to package:
![](../images/refer_screen_91.png)  
After successful packaging, display:
```shell
[INFO] --- spring-boot-maven-plugin:2.1.3.RELEASE:repackage (repackage) @ mall-tiny-docker-file ---
[INFO] Replacing main artifact with repackaged archive
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 30.749 s
[INFO] Finished at: 2019-06-16T14:11:07+08:00
[INFO] Final Memory: 43M/306M
[INFO] ------------------------------------------------------------------------
```
Upload the application jar package and Dockerfile file to the Linux server:
![](../images/refer_screen_92.png)
![](../images/refer_screen_95.png)
### Build docker image on Linux
Execute the following command in the directory where the Dockerfile is located:
```shell
# -t Represents the specified image repository name/image name: image label. Represents the use of Dockerfile in the current directory
docker build -t mall-tiny/mall-tiny-docker-file:0.0.1-SNAPSHOT .
```

The following information is output:
```shell
Sending build context to Docker daemon  36.37MB
Step 1/5 : FROM java:8
 ---> d23bdf5b1b1b
Step 2/5 : ADD mall-tiny-docker-file-0.0.1-SNAPSHOT.jar /mall-tiny-docker-file.jar
 ---> c920c9e9d045
Step 3/5 : RUN bash -c 'touch /mall-tiny-docker-file.jar'
 ---> Running in 55506f517f19
Removing intermediate container 55506f517f19
 ---> 0727eded66dc
Step 4/5 : EXPOSE 8080
 ---> Running in d67a5f50aa7d
Removing intermediate container d67a5f50aa7d
 ---> 1b8b4506eb2d
Step 5/5 : ENTRYPOINT ["java", "-jar","/mall-tiny-docker-file.jar"]
 ---> Running in 0c5bf61a0032
Removing intermediate container 0c5bf61a0032
 ---> c3614dad21b7
Successfully built c3614dad21b7
Successfully tagged mall-tiny/mall-tiny-docker-file:0.0.1-SNAPSHOT
```
View the docker image:
![](../images/refer_screen_93.png)
### Run mysql service and set up

#### 1.Use the docker command to start:
```shell
docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root  \
-d mysql:5.7
```
#### 2.Enter the docker container running mysql:
```shell
docker exec -it mysql /bin/bash
```
#### 3.Use the mysql command to open the client:
```shell
mysql -uroot -proot --default-character-set=utf8
```
#### 4.Modify the permissions of the root account so that any ip can access:
```sql
grant all privileges on *.* to 'root'@'%'
```
#### 5.Create the mall database:
```sql
create database mall character set utf8
```
#### 6.Copy the mall.sql file to the / directory of the mysql container:
```shell
docker cp /mydata/mall.sql mysql:/
```
#### 7.Import the sql file to the database:
```shell
use mall;
source /mall.sql;
```

### Run the mall-tiny-docker-file application

```shell
docker run -p 8080:8080 --name mall-tiny-docker-file \
--link mysql:db \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/mall-tiny-docker-file/logs:/var/logs \
-d mall-tiny/mall-tiny-docker-file:0.0.1-SNAPSHOT
```
Access interface document address http://192.168.3.101:8080/swagger-ui.html：
![](../images/refer_screen_94.png)

## Project source address

[https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-docker-file](https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-docker-file)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
