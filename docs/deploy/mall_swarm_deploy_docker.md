The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.

# Mall-swarm deployment in Linux environment (based on Docker container)

> This article takes the `mall-swarm` project as an example, which mainly introduces how a microservice architecture e-commerce project is deployed under a Docker container, involving the deployment of a large number of system components and the deployment of multiple Spring Cloud microservice applications, based on CentOS7.6 .

## Environment construction

### Basic environment deployment

> `mall-swarm`The system components required for operation are as follows. For the method of installing these components in the Docker container, simply refer to this article:[Mall deployment in Linux environment (based on Docker container)](/deploy/mall_deploy_docker.md) 。

| Components | version number |
| ------------- | ------ |
| JDK           | 1.8    |
| Mysql         | 5.7    |
| Redis         | 3.2    |
| Elasticsearch | 6.4.0  |
| MongoDb       | 3.2    |
| RabbitMq      | 3.7.15 |
| Nginx         | 1.10   |

### Mirror package upload

> A total of 8 application services need to be packaged into Docker images, for specific how to package, please refer to[Build Docker image using Maven plugin](/reference/docker_maven.md).
It should be noted that if no `mall-common`、`mall-mbg`Or`mall-security`Situation, you need to install these modules to the local maven repository in order and then package them.

| Application | version number |
| ------------- | ------ |
| mall-registry | 1.8    |
| mall-config   | 5.7    |
| mall-monitor  | 3.2    |
| mall-gateway  | 6.4.0  |
| mall-admin    | 3.2    |
| mall-portal   | 3.7.15 |
| mall-search   | 1.10   |
| mall-demo     | 1.10   |

After the image is packaged and uploaded, the complete docker warehouse image diagram:

![](../images/mall_swarm_linux_10.png)

## Application deployment

### Deploy mall-registry

- Run the registration center with the following command `mall-registry`：

```bash
docker run -p 8001:8001 --name mall-registry \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/mall-registry/logs:/var/logs \
-d mall/mall-registry:1.0-SNAPSHOT
```

- After running successfully, you can view the registration center console by accessing this address：http://192.168.6.132:8001/

### Deploy mall-config

- Run the configuration center with the following command `mall-config`：

```bash 
docker run -p 8301:8301 --name mall-config \
--link mall-registry:mall-registry \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/mall-config/logs:/var/logs \
-d mall/mall-config:1.0-SNAPSHOT
```

- After running successfully, you can view the configuration information of `mall-admin` in prod environment by visiting this address: http://192.168.6.132:8301/master/admin-prod.yml

- Requirement`Note`What is obtained from the configuration center in the prod environment is the configuration stored in the git repository. If you need to change it, you need to change the git repository configuration in the configuration file application.yml of the mall-config module to your own.

```yaml
spring:
  cloud:
    config:
      server:
        git: #Git repository storage
          uri: https://gitee.com/kratos47mhs/mall-config.git #Change to your own configuration
          username: macro
          password: 123456
          clone-on-start: true
          search-paths: '{application}'
```

### Deploy mall-monitor

-Run the monitoring center with the following command `mall-monitor`：

```bash 
docker run -p 8101:8101 --name mall-monitor \
--link mall-registry:mall-registry \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/mall-monitor/logs:/var/logs \
-d mall/mall-monitor:1.0-SNAPSHOT
```

- After the operation is completed, you can view the information of the monitoring center through this address, the account password is`macro:123456`：http://192.168.6.132:8101

### Deploy mall-gateway

- Run the gateway service with the following command`mall-gateway`：

```bash 
docker run -p 8201:8201 --name mall-gateway \
--link mall-registry:mall-registry \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/mall-gateway/logs:/var/logs \
-d mall/mall-gateway:1.0-SNAPSHOT
```

- After the operation is complete, you can view the dynamic routing rules through this address：http://192.168.6.132:8201/actuator/gateway/routes

### Deploy mall-admin

- Run the admin service with the following command`mall-admin`：

```bash 
docker run -p 8180:8180 --name mall-admin \
--link mysql:db \
--link mall-registry:mall-registry \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/mall-admin/logs:/var/logs \
-d mall/mall-admin:1.0-SNAPSHOT
```

- Access interface documents through the `mall-gateway` gateway service：http://192.168.6.132:8201/mall-admin/swagger-ui.html

### Deploy mall-portal

- Run the front-end service `mall-portal` with the following command:

```bash 
docker run -p 8085:8085 --name mall-portal \
--link mysql:db \
--link redis:redis \
--link mongo:mongo \
--link rabbitmq:rabbit \
--link mall-registry:mall-registry \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/mall-portal/logs:/var/logs \
-d mall/mall-portal:1.0-SNAPSHOT
```

- Access the interface document through the `mall-gateway` gateway service: http://192.168.6.132:8201/mall-portal/swagger-ui.html

### Deploy mall-search

- Run the search service with the following command`mall-search`：

```bash 
docker run -p 8081:8081 --name mall-search \
--link mysql:db \
--link elasticsearch:es \
--link mall-registry:mall-registry \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/mall-search/logs:/var/logs \
-d mall/mall-search:1.0-SNAPSHOT
```

- Access interface documents through the `mall-gateway` gateway service：http://192.168.6.132:8201/mall-search/swagger-ui.html

### Deploy mall-demo

- Run the test service with the following command`mall-demo`：

```bash 
docker run -p 8082:8082 --name mall-demo \
--link mysql:db \
--link mall-registry:mall-registry \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/mall-demo/logs:/var/logs \
-d mall/mall-demo:1.0-SNAPSHOT
```

- Access interface documents through the `mall-gateway` gateway service：http://192.168.6.132:8201/mall-demo/swagger-ui.html

## Run completion effect display

- Registration center console information:

![](../images/mall_swarm_linux_07.png)

- Application information of monitoring center:

![](../images/mall_swarm_linux_08.png)

![](../images/mall_swarm_linux_09.png)

## Visual management tools

> Portainer is a lightweight application that provides a graphical interface for convenient management of Docker environments, including stand-alone environments and cluster environments. Below we will use Portainer to manage applications in Docker containers.

- Official website address：https://github.com/portainer/portainer

- Obtain the Docker image file:

```bash
docker pull portainer/portainer
```

- Use the docker container to run Portainer:

```bash
docker run -p 9000:9000 -p 8000:8000 --name portainer \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /mydata/portainer/data:/data \
-d portainer/portainer
```

- View Portainer's Dashboard information:

![](../images/mall_swarm_linux_01.png)

- View all running container information:

![](../images/mall_swarm_linux_02.png)

- View all downloaded Docker images:

![](../images/mall_swarm_linux_03.png)

- View the statistics of the `mall-portal` application:

![](../images/mall_swarm_linux_04.png)

- View the log information printed during the running of the `mall-portal` application:

![](../images/mall_swarm_linux_05.png)

- Enter the container inside the `mall-portal` application to operate the internal system of the container:

![](../images/mall_swarm_linux_06.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)





