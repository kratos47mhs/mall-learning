The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Mall deployment in Linux environment (based on Docker container)
> This article mainly explains the deployment of mall in the Linux environment in the form of graphics, which involves the installation of Mysql, Redis, Nginx, Rabbit MQ, Elasticsearch, Mongodb, and Spring Boot application deployment in Docker containers, based on Center OS 7.6.

## Docker environment installation
- Install yum-utils：
```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
```
- Add docker repository location for yum source：
```shell
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
- Install docker：
```shell
yum install docker-ce
```
- Start docker：
```shell
systemctl start docker
```

## Mysql installation
- Download the docker image of mysql 5.7：
```shell
docker pull mysql:5.7
```
- Start with docker command：
```shell
  docker run -p 3306:3306 --name mysql \
  -v /mydata/mysql/log:/var/log/mysql \
  -v /mydata/mysql/data:/var/lib/mysql \
  -v /mydata/mysql/conf:/etc/mysql \
  -e MYSQL_ROOT_PASSWORD=root  \
  -d mysql:5.7
```
- Parameter Description
  - -p 3306:3306：Map port 3306 of the container to port 3306 of the host
  - -v /mydata/mysql/conf:/etc/mysql：Hang the configuration folder to the host
  - -v /mydata/mysql/log:/var/log/mysql：Mount the log folder to the host
  - -v /mydata/mysql/data:/var/lib/mysql/：Mount the data folder to the host
  - -e MYSQL_ROOT_PASSWORD=root：Initialize the password of the root user
- Enter the docker container running mysql：
```shell
docker exec -it mysql /bin/bash
```
- Use the mysql command to open the client：
```shell
mysql -uroot -proot --default-character-set=utf8
```
- Create mall database：
```sql
create database mall character set utf8
```
- Install upload and download plugin, and upload docment/sql/mall.sql to Linux server:
```shell
yum -y install lrzsz
```
- Copy the mall.sql file to the / directory of the mysql container:
```shell
docker cp /mydata/mall.sql mysql:/
```
- Import the sql file to the database:
```shell
use mall;
source /mall.sql;
```
- Create a reader account and modify permissions so that any ip can access:
```sql
grant all privileges on *.* to 'reader' @'%' identified by '123456';
```

## Redis installation
- Download the docker image of redis 3.2:
```shell
docker pull redis:3.2
```
- Use the docker command to start:
```shell
  docker run -p 6379:6379 --name redis \
  -v /mydata/redis/data:/data \
  -d redis:3.2 redis-server --appendonly yes
```
- Enter the redis container and use the redis-cli command to connect:
```shell
docker exec -it redis redis-cli
```
![](../images/refer_screen_74.png)

## Nginx installation

### Download the docker image of nginx 1.10:
```shell
docker pull nginx:1.10
```

### Copy nginx configuration from container
- First run the container (in order to copy the configuration file):
```shell
  docker run -p 80:80 --name nginx \
  -v /mydata/nginx/html:/usr/share/nginx/html \
  -v /mydata/nginx/logs:/var/log/nginx  \
  -d nginx:1.10
```
- Copy the configuration file in the container to the specified directory:
```shell
docker container cp nginx:/etc/nginx /mydata/nginx/
```
- Modify the file name:
```shell
mv nginx conf
```
- Terminate and delete the container:
```shell
docker stop nginx
docker rm nginx
```

### Use the docker command to start:
```shell
docker run -p 80:80 --name nginx \
-v /mydata/nginx/html:/usr/share/nginx/html \
-v /mydata/nginx/logs:/var/log/nginx  \
-v /mydata/nginx/conf:/etc/nginx \
-d nginx:1.10
```

## Rabbit MQ installation
- Download the rabbitmq 3.7.15 docker image:
```shell
docker pull rabbitmq:3.7.15
```
- Use the docker command to start:
```shell
  docker run -d --name rabbitmq \
  --publish 5671:5671 --publish 5672:5672 --publish 4369:4369 \
  --publish 25672:25672 --publish 15671:15671 --publish 15672:15672 \
  rabbitmq:3.7.15
```
- Enter the container and open the management function:
```shell
docker exec -it rabbitmq /bin/bash
rabbitmq-plugins enable rabbitmq_management
```
![](../images/refer_screen_75.png)
- Turn on the firewall:
```shell
firewall-cmd --zone=public --add-port=15672/tcp --permanent
firewall-cmd --reload
```
- Visit the address to see if the installation is successful：[http://192.168.3.101:15672/](http://192.168.3.101:15672/)
![](../images/refer_screen_76.png)
- Enter account password and login：guest guest
- Create an account and set its role as an administrator：mall mall
![](../images/refer_screen_77.png)
- Create a new virtual host as：/mall
![](../images/refer_screen_78.png)
- Click the mall user to enter the user configuration page
![](../images/refer_screen_79.png)
- Configure the permission of the virtual host for the mall user
![](../images/refer_screen_80.png)

## Elasticsearch installation
- Download the docker image of elasticsearch 6.4.0:
```shell
docker pull elasticsearch:6.4.0
```
- Modify the size of the virtual memory area, otherwise it will be too small and not going to start:
```shell
sysctl -w vm.max_map_count=262144
```
- Use the docker command to start:
```shell
  docker run -p 9200:9200 -p 9300:9300 --name elasticsearch \
  -e "discovery.type=single-node" \
  -e "cluster.name=elasticsearch" \
  -v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
  -v /mydata/elasticsearch/data:/usr/share/elasticsearch/data \
  -d elasticsearch:6.4.0
```
- During startup, you will find that the /usr/share/elasticsearch/data directory does not have access permissions. You only need to modify the permissions of the /mydata/elasticsearch/data directory and restart it.
```shell
chmod 777 /mydata/elasticsearch/data/
```
- Install the Chinese word breaker IK Analyzer, and restart:
```shell
docker exec -it elasticsearch /bin/bash
#This command needs to be run in the container
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.4.0/elasticsearch-analysis-ik-6.4.0.zip
docker restart elasticsearch
```
- Turn on the firewall:
```shell
firewall-cmd --zone=public --add-port=9200/tcp --permanent
firewall-cmd --reload
```
- Visit will return version information：[http://192.168.3.101:9200/](http://192.168.3.101:9200/)
![](../images/refer_screen_81.png)

## Kibana
- Download the docker image of kibana 6.4.0:
```shell
docker pull kibana:6.4.0
```
- Use the docker command to start:
```shell
  docker run --name kibana -p 5601:5601 \
  --link elasticsearch:es \
  -e "elasticsearch.hosts=http://es:9200" \
  -d kibana:6.4.0
```
- Turn on the firewall:
```shell
firewall-cmd --zone=public --add-port=5601/tcp --permanent
firewall-cmd --reload
```
- Visit the address to test：[http://192.168.3.101:5601](http://192.168.3.101:5601)
![](../images/refer_screen_90.png)

## Mongodb installation
- Download the docker image of mongo 3.2:
```shell
docker pull mongo:3.2
```
- Use the docker command to start:
```shell
  docker run -p 27017:27017 --name mongo \
  -v /mydata/mongo/db:/data/db \
  -d mongo:3.2
```

## All Docker environments are installed
- All downloaded image files:
![](../images/refer_screen_82.png)
- All applications running in the container:
![](../images/refer_screen_83.png)

## Spring Boot application deployment

### Build all Docker images and upload
- Open the comment of using docker plugin in pom.xml:
![](../images/refer_screen_84.png)
- Change docker Host to your own docker server address:
- Build the image and upload:
![](../images/refer_screen_85.png)
![](../images/refer_screen_86.png)

### Deploy mall-admin
```shell
docker run -p 8080:8080 --name mall-admin \
--link mysql:db \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/admin/logs:/var/logs \
-d mall/mall-admin:1.0-SNAPSHOT
```
`Note`：Center OS 7.2 version needs to join this line, otherwise the container time zone and host cannot be synchronized
```shell
-v /etc/timezone:/etc/timezone \
```
### Deploy mall-search
```shell
docker run -p 8081:8081 --name mall-search \
--link elasticsearch:es \
--link mysql:db \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/search/logs:/var/logs \
-d mall/mall-search:1.0-SNAPSHOT
```
### Deploy mall-portal
```shell
docker run -p 8085:8085 --name mall-portal \
--link mysql:db \
--link redis:redis \
--link mongo:mongo \
--link rabbitmq:rabbit \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/portal/logs:/var/logs \
-d mall/mall-portal:1.0-SNAPSHOT
```

### Turn on the firewall
```shell
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-port=8081/tcp --permanent
firewall-cmd --zone=public --add-port=8085/tcp --permanent
firewall-cmd --reload
```
### Access the interface for testing

- mall-admin api interface document address：http://192.168.3.101:8080/swagger-ui.html
![](../images/refer_screen_87.png)
- mall-search api interface document address：http://192.168.3.101:8081/swagger-ui.html
![](../images/refer_screen_88.png)
- Mall-portal API interface document address：http://192.168.3.101:8085/swagger-ui.html
![](../images/refer_screen_89.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
