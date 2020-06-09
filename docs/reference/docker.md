In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public Number) get it immediately.

# Developer essential Docker commands

> This article mainly explains the installation of the Docker environment and the use of Docker common commands. Mastering these is very helpful for the deployment of applications in the Docker environment.

## Introduction to Docker

 Docker is an open source application container engine that allows developers to package their applications and dependent packages into a portable image and then publish it to any popular Linux or Windows machine. Using Docker makes it easier to package, test, and deploy applications.

## Docker environment installation
- Install yum-utils：
```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
```
- Add docker repository location for yum source：
```bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
- Install docker:
```bash
yum install docker-ce
```
- Start docker:
```bash
systemctl start docker
```

## Common commands for Docker images

### Search image
```bash
docker search java
```
![](../images/refer_screen_51.png)
### Download image
```bash
docker pull java:8
```
### How to find the version supported by the image
> Since the docker search command can only find out whether the image is available, and cannot find the version supported by the image, we need to search the supported version through the docker hub.

- Enter the official website of docker hub, address：[https://hub.docker.com](https://hub.docker.com)
- Then search for the required image：
![](../images/refer_screen_52.png)
- View the version supported by the image：
![](../images/refer_screen_53.png)

- Download the image：
```bash
docker pull nginx:1.17.0
```

### List images
```bash
docker images
```
![](../images/refer_screen_54.png)
### Delete image
- Delete the image with the specified name
```bash
docker rmi java:8
```
- Delete the image with the specified name (mandatory)
```bash
docker rmi -f java:8
```
- Delete all unreferenced images
```bash
docker rmi `docker images | grep none | awk '{print $3}'`
```
- Force delete all images
```bash
docker rmi -f $(docker images)
```

## Common commands for Docker containers
### Create and start a container
```bash
docker run -p 80:80 --name nginx -d nginx:1.17.0
```
- -d option: indicates running in the background
- --name option: specify the name of the container after running as nginx, and then you can operate the container by name
- -p option: specify port mapping, the format is：hostPort:containerPort

### List containers
- List running containers:
```bash
docker ps
```
![](../images/refer_screen_55.png)
- List all containers
```bash
docker ps -a
```
![](../images/refer_screen_56.png)
### Stop the container
```bash
# $ContainerName and $ContainerId can be queried with the docker ps command
docker stop $ContainerName(Or $ContainerId)
```
for Example
```bash
docker stop nginx
#Or
docker stop c5f5d5125587
```
### Force stop container
```bash
docker kill $ContainerName(Or $ContainerId)
```
### Start a stopped container
```bash
docker start $ContainerName(Or $ContainerId)
```
### Into the container
- First query the pid of the container:
```bash
docker inspect --format "{{.State.Pid}}" $ContainerName(Or $ContainerId)
```
- Enter the container according to the pid of the container:
```bash
nsenter --target "$pid" --mount --uts --ipc --net --pid
```
![](../images/refer_screen_57.png)
### Delete container
- Delete the specified container:
```bash
docker rm $ContainerName(Or $ContainerId)
```
- Delete container by name
```bash
docker rm `docker ps -a | grep mall-* | awk '{print $1}'`
```
- Forcibly delete all containers;
```bash
docker rm -f $(docker ps -a -q)
```
### View container logs
- View all current logs
```bash
docker logs $ContainerName(Or $ContainerId)
```
- View logs dynamically
```bash
docker logs $ContainerName(Or $ContainerId) -f
```
![](../images/refer_screen_58.png)
### View the IP address of the container
```bash
docker inspect --format '{{ .NetworkSettings.IPAddress }}' $ContainerName(Or $ContainerId)
```
![](../images/refer_screen_59.png)
### Modify the container startup method
```bash
docker container update --restart=always $ContainerName
```
### Synchronize host time to container
```bash
docker cp /etc/localtime $ContainerName(Or $ContainerId):/etc/
```
### View docker usage cpu, memory, network, io on the host machine
- View the specified container:
```bash
docker stats $ContainerName(Or $ContainerId)
```
![](../images/refer_screen_60.png)
- View all containers:
```bash
docker stats -a
```
![](../images/refer_screen_61.png)
### View Docker disk usage
```bash
docker system df
```
![](../images/refer_screen_108.png)
### Enter the bash inside the Docker container
```bash
docker exec -it $ContainerName /bin/bash
```
![](../images/refer_screen_62.png)

## Modify the storage location of the Docker image
- Check the storage location of the Docker image:
```bash
docker info | grep "Docker Root Dir"
```
![](../images/refer_screen_63.png)
- Shut down the Docker service:
```bash
systemctl stop docker
```
- Move the directory to the target path:
```bash
mv /var/lib/docker /mydata/docker
```
- Establish a soft connection:
```bash
ln -s /mydata/docker /var/lib/docker
```
![](../images/refer_screen_64.png)
![](../images/refer_screen_65.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)

