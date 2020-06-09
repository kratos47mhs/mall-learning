The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.

# Use the Maven plugin to build Docker images for Spring Boot applications
> This article mainly describes how to use the Maven plugin to package the SpringBoot application as a Docker image and upload it to the private image warehouse Docker Registry.

## Docker Registry
### Docker Registry 2.0 build
```shell
docker run -d -p 5000:5000 --restart=always --name registry2 registry:2
```
If the image cannot be downloaded, you need to modify the /etc/docker/daemon.json file and add the registry-mirrors key value, and then restart the docker service:
```json
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```
### Docker opens remote API
#### Modify the docker.service file with vim editor
```
vi /usr/lib/systemd/system/docker.service
```
Parts that need to be modified:
```shell
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```
Modified part:
```shell
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
```
#### Let Docker support http upload image
```shell
echo '{ "insecure-registries":["192.168.3.101:5000"] }' > /etc/docker/daemon.json
```
#### After modifying the configuration, you need to use the following command to make the configuration effective
```shell
systemctl daemon-reload
```
#### Restart the Docker service
```shell
systemctl stop docker
systemctl start docker
```
#### Open the firewall's Docker build port
```shell
firewall-cmd --zone=public --add-port=2375/tcp --permanent
firewall-cmd --reload
```

## Use Maven to build Docker images
> The code is modified on the basis of mall-tiny-02.

### Add the dependency of docker-maven-plugin in the pom.xml file of the application

```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>1.1.0</version>
    <executions>
        <execution>
            <id>build-image</id>
            <phase>package</phase>
            <goals>
                <goal>build</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <imageName>mall-tiny/${project.artifactId}:${project.version}</imageName>
        <dockerHost>http://192.168.3.101:2375</dockerHost>
        <baseImage>java:8</baseImage>
        <entryPoint>["java", "-jar","/${project.build.finalName}.jar"]
        </entryPoint>
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}</directory>
                <include>${project.build.finalName}.jar</include>
            </resource>
        </resources>
    </configuration>
</plugin>
```
The related configuration instructions：
- executions.execution.phase:Here is configured to build a docker image when maven packages the application;
- imageName：Used to specify the image name, mall-tiny is the repository name, `${project.artifactId}` is the mirror name, and `${project.version}` is the warehouse name;
- dockerHost：The docker server address to upload to after packaging；
- baseImage：The basic image that the application depends on, here java；
- entryPoint：Commands executed when the docker container starts；
- resources.resource.targetPath：Copy the packaged resource file to this directory；
- resources.resource.directory：The directory of the file to be copied, the application jar package packaged by maven is saved under the target directory；
- resources.resource.include：Files that need to be copied are packaged application jar packages.

### Modify application.yml, change localhost to db
> You can think of the container in docker as an independent virtual machine. Mall-tiny-docker will not be able to access mysql when accessing localhost. The docker container can be accessed through the specified service name db. As for the name db, you can run mall-tiny-docker is specified when the container.

```yml
spring:
  datasource:
    url: jdbc:mysql://db:3306/mall?useUnicode=true&characterEncoding=utf-8&serverTimezone=Asia/Shanghai
    username: root
    password: root
```

### Use IDEA to package the project and build the image
> Note: The dependent basic image needs to be downloaded first, otherwise there will be a timeout when building the image. For example, if I don't have a java 8 image locally, I need to pull down the image first and then use the maven plugin to build it.

- Execute maven's package command:  
![](../images/refer_screen_68.png)
- Successful build:  
![](../images/refer_screen_66.png)
- The image repository already has the image: 
![](../images/refer_screen_67.png)

## Run the mall-tiny-docker project

### Start mysql service
- Use the docker command to start:
```shell
  docker run -p 3306:3306 --name mysql \
  -v /mydata/mysql/log:/var/log/mysql \
  -v /mydata/mysql/data:/var/lib/mysql \
  -v /mydata/mysql/conf:/etc/mysql \
  -e MYSQL_ROOT_PASSWORD=root  \
  -d mysql:5.7
```
- Enter the docker container running mysql:
```shell
docker exec -it mysql /bin/bash
```
- Use the mysql command to open the client:
```shell
mysql -uroot -proot --default-character-set=utf8
```
![](../images/refer_screen_69.png)
- Modify the permissions of the root account so that any ip can access:
```sql
grant all privileges on *.* to 'root'@'%'
```
![](../images/refer_screen_70.png)
- Create the mall database:
```sql
create database mall character set utf8
```
- Use [mall.sql](https://github.com/kratos47mhs/mall-learning/blob/master/document/sql/mall.sql) Copy the file to the / directory of the mysql container:
```shell
docker cp /mydata/mall.sql mysql:/
```
- Import the sql file to the database:
```shell
use mall;
source /mall.sql;
```
![](../images/refer_screen_71.png)
### Start the mall-tiny-docker application service
- Use the docker command to start (--link indicates that the application can use the domain name db to access the mysql service):
```shell
  docker run -p 8080:8080 --name mall-tiny-docker \
  --link mysql:db \
  -v /etc/localtime:/etc/localtime \
  -v /mydata/app/mall-tiny-docker/logs:/var/logs \
  -d mall-tiny/mall-tiny-docker:0.0.1-SNAPSHOT
```
![](../images/refer_screen_72.png)
- Open port 8080:
```shell
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload
```
- Conduct an access test, address：[http://192.168.3.101:8080/swagger-ui.html](http://192.168.3.101:8080/swagger-ui.html)
![](../images/refer_screen_73.png)

## Project source address

[https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-docker](https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-docker)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
