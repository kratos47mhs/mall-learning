The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Automated deployment of mall in Linux environment (based on Jenkins)

> This article is the exclusive Jenkins automated deployment method for the `mall` project.

## Basic use of Jenkins

About the basic use of Jenkins can refer to：[One-click packaging and deployment of Spring Boot applications using Jenkins, that's it 6!](https://mp.weixin.qq.com/s/tQqvgSc9cHBtnqRQSbI4aw)

## Execute script preparation

> First, we first prepare the script that needs to be executed remotely.

- The script files are stored in the `/document/sh` directory of the `mall` project:

- Before uploading the script, modify the line break format of all script files in IDEA to `LF`, otherwise the script will fail to execute;

![](../images/mall_deploy_jenkins_01.png)

- Upload all script files to the specified directory, here we upload to the `/mydata/sh` directory;

![](../images/mall_deploy_jenkins_02.png)

- Modify all script files to executable files:

```bash
chmod +x ./mall-*
```

![](../images/mall_deploy_jenkins_03.png)

## Create tasks in Jenkins

> Next we will implement automated deployment by creating tasks in Jenkins. Since our `mall` is a multi-module project, deploying the above is different from the previous single-module project.

### mall-admin

> Since the creation of the execution tasks of each module is similar, the creation of the mall-admin module task will be explained in detail below, and the other modules will be briefly explained.
>

- First we choose `Build a free-style software project` mall-admin, and then configure its Git repository address, here I directly use the address above Gitee:

![](../images/mall_deploy_jenkins_04.png)

- Then we create a build to build the dependent modules in the `mall` project, otherwise when building a serviceable module, the build will fail because these modules cannot be found;

```bash
# Only install three modules mall-common, mall-mbg, mall-security
clean install -pl mall-common,mall-mbg,mall-security -am
```

- The schematic diagram of dependent project construction:

![](../images/mall_deploy_jenkins_05.png)

- Create another build, build and package the mall-admin module separately:

![](../images/mall_deploy_jenkins_06.png)

- Add a remote SSH execution task to execute the running script of mall-admin:

![](../images/mall_deploy_jenkins_07.png)

- Click Save to complete the execution task of mall-admin.

### mall-portal

> Mall-portal and other modules are basically consistent with mall-admin's task creation method. You only need to modify the pom.xml file location and script execution location when building the module.

- We can directly copy one from the task of the mall-admin module and create it:

![](../images/mall_deploy_jenkins_08.png)

- Modify the location of the pom.xml file in the second build to: ${WORKSPACE}/mall-portal/pom.xml

![](../images/mall_deploy_jenkins_09.png)

- Modify the location of the SSH execution script file in the third build to: /mydata/sh/mall-portal.sh

![](../images/mall_deploy_jenkins_10.png)

- Click Save to complete the execution of the mall-portal task.

### mall-search

Refer to the creation of mall-admin and mall-portal.

### The task is created

![](../images/mall_deploy_jenkins_11.png)

## project address

[https://github.com/kratos47mhs/mall](https://github.com/kratos47mhs/mall)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
