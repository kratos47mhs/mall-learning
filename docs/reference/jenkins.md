In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) will be obtained immediately.

# One-click packaging and deployment of Spring Boot applications using Jenkins, that's it 6!

> Behind any simple operation, there is a fairly complicated mechanism. This article will take the packaging and deployment of Spring Boot applications in Docker environment as an example to explain in detail how to use one-click packaging and deployment of Spring Boot applications with Jenkins.

## About Jenkins

Jenkins is a leader in open source CI&CD software, providing more than 1,000 plug-ins to support construction, deployment, and automation to meet the needs of any project. We can use Jenkins to build and deploy our project, for example, get the code from our code warehouse, and then package our code into an executable file, and then execute the script through the remote ssh tool to run our project.

## Installation and configuration of Jenkins

### Installation in Docker environment

- Download the Jenkins Docker image:

```bash
docker pull jenkins/jenkins:lts
```

- Run Jenkins in a Docker container:

```bash
docker run -p 8080:8080 -p 50000:5000 --name jenkins \
-u root \
-v /mydata/jenkins_home:/var/jenkins_home \
-d jenkins/jenkins:lts
```

### Jenkins configuration

- After successful operation, visit this address to log in to Jenkins. The first time you log in, you need to enter the administrator password: http://192.168.6.132:8080/

![](../images/jenkins_use_23.png)

- To log in using the administrator password, you can use the following command to obtain the management password from the container startup log:

```bash
docker logs jenkins
```

- Get the administrator password from the log:

![](../images/jenkins_use_24.png)

- Choose the way to install the plug-in, here we directly install the recommended plug-in:

![](../images/jenkins_use_25.png)

- Enter the plug-in installation interface, connect to the network and wait for the plug-in installation:

![](../images/jenkins_use_26.png)

- After the installation is complete, create an administrator account:

![](../images/jenkins_use_27.png)

- For instance configuration, configure the URL of Jenkins:

![](../images/jenkins_use_28.png)

- Click System Management->Plugin Management to install some customized plugins:

![](../images/jenkins_use_14.png)

- Make sure the following plugins are installed correctly:
    - Plugins that manage permissions based on roles：Role-based Authorization Strategy
    - Remotely use ssh plugin：SSH plugin

- Through the system management -> global tool configuration to configure the global tool, such as maven configuration:

![](../images/jenkins_use_15.png)

- Add maven installation configuration:

![](../images/jenkins_use_16.png)

- Add the global ssh configuration in System Management->System Configuration, so that Jenkins can execute remote Linux scripts using ssh:

![](../images/jenkins_use_17.png)

### Role rights management

> We can use Jenkins' role management plugin to manage Jenkins users. For example, we can give all permissions to administrators, operation and maintenance personnel to grant related permissions to perform tasks, and other personnel only to grant viewing permissions.

- Enable role-based rights management in System Management->Global Security Configuration:

![](../images/jenkins_use_18.png)

- Enter the system management->Manage and Assign Roles interface:

![](../images/jenkins_use_19.png)

- Add the relationship between roles and permissions:

![](../images/jenkins_use_20.png)

- Assign roles to users:

![](../images/jenkins_use_21.png)

## Package and deploy Spring Boot applications

> Here we use the `mall-tiny-jenkins` module code in the `mall-learning` project to demonstrate how to enable Jenkins to deploy and deploy Spring Boot applications with one click.

### Upload the code to the Git repository

- First we need to install Gitlab (of course you can also use Github or Gitee), and then upload the code in `mall-tiny-jenkins` to Gitlab, please refer to the use of Gitlab：[Set up your own Git repository in 10 minutes](https://mp.weixin.qq.com/s/6GyYlR9lpVcjgYmHMYLi0w)

- `mall-tiny-jenkins` project source address：https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-jenkins

- After uploading, the display effect in Gitlab is as follows:

![](../images/jenkins_use_01.png)

- One thing you need to `Note` is to change the docker Host address in pom.xml to your own Docker mirror repository address:

![](../images/jenkins_use_22.png)

### Execute script preparation

- Upload the `mall-tiny-jenkins.sh` script file to the `/mydata/sh` directory, the script content is as follows:

```bash
#!/usr/bin/env bash
app_name='mall-tiny-jenkins'
docker stop ${app_name}
echo '----stop container----'
docker rm ${app_name}
echo '----rm container----'
docker run -p 8088:8088 --name ${app_name} \
--link mysql:db \
-v /etc/localtime:/etc/localtime \
-v /mydata/app/${app_name}/logs:/var/logs \
-d mall-tiny/${app_name}:1.0-SNAPSHOT
echo '----start container----'
```

- Add executable permissions to the .sh script:

```bash
chmod +x ./mall-tiny-jenkins.sh  
```

- The .sh script under windows is uploaded to linux for use, and the file format needs to be modified, otherwise it cannot be executed because of the existence of a special format:

```bash
# Use vim editor to modify
vi mall-tiny-jenkins.sh
# View the file format, the default for uploading on windows is dos
:set ff 
# Change the file format to unix
:set ff=unix 
# Save and exit
:wq
```

- Execute the .sh script for test use, you can not execute:
```bash
./mall-tiny-jenkins.sh
```

### Create execution task in Jenkins

- First we need to create a new task:

![](../images/jenkins_use_02.png)

- After setting the task name, choose to build a free-style software project:

![](../images/jenkins_use_03.png)

- Then add our git repository address in the source code management：http://192.168.6.132:1080/kratos47mhs/mall-tiny-jenkins

![](../images/jenkins_use_04.png)

- At this time, you need to add a credential, which is the account password of our git repository:

![](../images/jenkins_use_05.png)

- After completing the selection of the credentials, you can connect to the git repository normally;

![](../images/jenkins_use_06.png)

- After that, we need to add a build and choose to call the top-level maven goal. This build is mainly used to package our source code into a Docker image and upload it to our Docker image repository:

![](../images/jenkins_use_07.png)

- Select our maven version, then set the maven command and specify the pom file location:

![](../images/jenkins_use_08.png)

- Then add a build that executes a remote shell script to execute the .sh script that starts the Docker container after our image is packaged:

![](../images/jenkins_use_09.png)

- The shell commands that need to be set to execute are as follows：/mydata/sh/mall-tiny-jenkins.sh

![](../images/jenkins_use_10.png)

- After clicking save operation, our task is created. In the task list, we can click run to execute the task;

![](../images/jenkins_use_11.png)

- We can view the execution process of the entire task through the console output:

![](../images/jenkins_use_12.png)

- After running successfully, visit this address to view API documentation：http://192.168.6.132:8088/swagger-ui.html

![](../images/jenkins_use_13.png)

## Project source address

https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-jenkins

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)


