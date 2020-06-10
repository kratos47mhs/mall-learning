In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) will be obtained immediately.

# One-click packaging and deployment of front-end applications using Jenkins, that's it 6!

> Last time we talked about using Jenkins one-click packaging to deploy Spring Boot applications. This time we will talk about how to deploy front-end applications with one-click packaging. Taking Vue front-end applications as an example, here we use the code in `mall-admin-web` to Make a presentation.

## 学前准备

> Learning this article requires some knowledge of Jenkins and Nginx. For those unfamiliar partners, you can refer to the following article.

- [One-click packaging and deployment of Spring Boot applications using Jenkins, that's it 6!](https://mp.weixin.qq.com/s/tQqvgSc9cHBtnqRQSbI4aw)
- [You must have no idea about these wonderful uses of Nginx!](https://mp.weixin.qq.com/s/9VZi2suAlomu1IRGy-qdCA)

## Automated deployment in Jenkins

> The packaging of Vue front-end applications depends on the Node JS plug-in, so we first install and configure the plug-in, and then create tasks to package and deploy.

### Install Node JS plugin

- Choose to install the plug-in in system settings -> plug-in management;

![](../images/jenkins_vue_01.png)

- Search and install the `NodeJS` plugin;

![](../images/jenkins_vue_02.png)

### Configure Node JS plugin

- Plug-in configuration in system settings -> global tool configuration;

![](../images/jenkins_vue_03.png)

- Select `Add NodeJS`, after configuring the version number, click Save to complete the setting;

![](../images/jenkins_vue_04.png)

### Create task

> We need to create a task to package and deploy our front-end application, here is my `mall-admin-web` project as an example.

- The task execution process is as follows:

![](../images/jenkins_vue_11.png)

- Build a free style software project:

![](../images/jenkins_vue_05.png)

- Add the relevant configuration of Git code warehouse in the source code management, here I use the code above Gitee, the address is：https://gitee.com/kratos47mhs/mall-admin-web

![](../images/jenkins_vue_06.png)

- Add our `node` environment to the build environment:

![](../images/jenkins_vue_07.png)

- Add a `execute shell` build to compile and package our front-end code:

![](../images/jenkins_vue_08.png)

- The build script is as follows:

```bash
# View version information
npm -v
# Solve the problem that sass stored on Github cannot be downloaded
SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install node-sass
# Replace mirror source with accelerated access from Taobao
npm config set registry https://registry.npm.taobao.org
# Install project dependencies
npm install
# Project packaging
npm run build
```

- Add-one`Use ssh to execute a remote script`The build is used to publish our packaged code to Nginx:

![](../images/jenkins_vue_09.png)

- The remote execution script is as follows:

```bash
docker stop nginx
echo '----stop nginx----'
rm -rf /mydata/nginx/html
echo '----rm html dir----'
cp -r /mydata/jenkins_home/workspace/mall-admin-web/dist /mydata/nginx/html
echo '----cp dist dir to html dir----'
docker start nginx
echo '----start nginx----'
```

- After clicking Save, click Run directly in the task list to complete the automated deployment:

![](../images/jenkins_vue_10.png)

## 遇到的坑

### Node-sass fails to download and causes the build to fail

Since the source of node-sass uses Github, it is often inaccessible. When we build, we need to set the download address of node-sass separately.

```bash
# linux
SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install node-sass
# window
set SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass&& npm install node-sass
```

### Some dependencies cannot be downloaded, causing the build to fail

Due to the problem of slow access to npm sources, some sources may not be able to be downloaded. Instead, use Taobao's npm source to solve it.

```bash
# Set as Taobao's registry source
npm config set registry https://registry.npm.taobao.org
# Set as official registry source
npm config set registry https://registry.npmjs.org
```

## project address

[https://github.com/kratos47mhs/mall-admin-web](https://github.com/kratos47mhs/mall-admin-web)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)

