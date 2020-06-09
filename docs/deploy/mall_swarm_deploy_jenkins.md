The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Automated deployment under the microservice architecture is implemented using Jenkins!

> In the micro-service architecture, as more and more services, the packaging and deployment of services will become a very troublesome thing. For example, my `mall-swarm` project currently has 8 services that need to be deployed. Is there any way for us to deploy it once, and just click execute to automatically deploy it? Of course there is! Below we use Jenkins to complete the automated deployment in a microservices architecture.

## Basic use of Jenkins

About the basic use of Jenkins can refer to：[One-click packaging and deployment of Spring Boot applications using Jenkins, that's it 6!](https://mp.weixin.qq.com/s/tQqvgSc9cHBtnqRQSbI4aw)

## Execute script preparation

> First, we first prepare the script that needs to be executed remotely.

- The script files are stored in the `/document/sh` directory of the `mall-swarm` project:

![](../images/mall_swarm_jenkins_01.png)

- Before uploading the script, modify the line break format of all script files in IDEA to `LF`, otherwise the script will fail to execute;

![](../images/mall_swarm_jenkins_02.png)

- Upload all script files to the specified directory, here we upload to the `/mydata/sh` directory;

![](../images/mall_swarm_jenkins_03.png)

- Modify all script files to executable files:

```bash
chmod +x ./mall-*
```

![](../images/mall_swarm_jenkins_04.png)

## Create tasks in Jenkins

> Next we will implement automated deployment by creating tasks in Jenkins. Since our `mall-swarm` is a multi-module project, deploying the above is different from the previous single-module project.

### mall-admin

> Since the creation of the execution tasks of each module is similar, the creation of the mall-admin module task will be explained in detail below, and the other modules will be briefly explained.

- First, we choose `Build a free-style software project`, and then enter the task name as mall-admin, configure its Git repository address, here I directly use the address above Gitee:

![](../images/mall_swarm_jenkins_05.png)

- After that, we create a build and build the dependent modules in the `mall-swarm` project, otherwise when building a serviceable module, it will fail because the modules cannot be found;

```bash
# Only install three modules mall-common, mall-mbg, mall-security
clean install -pl mall-common,mall-mbg,mall-security -am
```

- Schematic diagram of dependent project construction:

![](../images/mall_swarm_jenkins_06.png)

- Create another build, build and package the mall-admin module separately:

![](../images/mall_swarm_jenkins_07.png)

- Add a build to execute remote tasks via SSH, used to execute the running script of mall-admin:

![](../images/mall_swarm_jenkins_08.png)

- Click Save to complete the execution task of mall-admin.

### mall-registry

> Mall-registry and other modules are basically the same as the way that mall-admin creates tasks. You only need to modify the `pom.xml` file location and `execute script` location when building the module.

- We can directly copy one from the task of the mall-admin module and create it:

![](../images/mall_swarm_jenkins_09.png)

- Modify the pom.xml file location in the second build to：`${WORKSPACE}/mall-registry/pom.xml`

![](../images/mall_swarm_jenkins_10.png)

- Modify the location of the SSH execution script file in the third build to：`/mydata/sh/mall-registry.sh`

![](../images/mall_swarm_jenkins_11.png)

- Click Save to complete the execution task of mall-registry.

### Other modules

For the execution tasks of other modules, please refer to the creation of mall-admin and mall-registry.

### The task is created

![](../images/mall_swarm_jenkins_12.png)

## Module startup sequence

> Regarding the startup sequence of each module, the `mall-registry` module must be started first, the `mall-config` module must be started second, and other modules have no startup sequence restrictions.

Recommended startup sequence:
- mall-registry
- mall-config
- mall-monitor
- mall-gateway
- mall-admin
- mall-portal
- mall-search
- mall-demo

## Summary

We completed the packaging and deployment of services in the microservice architecture by creating tasks in Jenkins, so that when we modify the code each time, we only need to click the start task to implement one-click packaging and deployment, eliminating frequent packaging and deployment Trouble.

## project address

[https://github.com/kratos47mhs/mall-swarm](https://github.com/kratos47mhs/mall-swarm)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)

