The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Installation and deployment of mall front-end projects

> This article mainly explains the installation and deployment of the mall-admin-web mall front-end project under Windows and Linux environments.

## Installation and deployment under Windows

### Download nodejs and install
download link：https://nodejs.org/dist/v8.9.4/node-v8.9.4-x64.msi

### Download the code for mall-admin-web
download link（github）：https://github.com/kratos47mhs/mall-admin-web
download link（码云）：https://gitee.com/kratos47mhs/mall-admin-web

### Open the mall-admin-web project from IDEA
![](../images/tech_screen_12.png)
### Open the console and enter the command to install related dependencies
```shell
npm install
```
![](../images/tech_screen_13.png)

![](../images/tech_screen_14.png)

### The start of the mall back-end environment has been built

#### Run local mall-admin service
![](../images/tech_screen_15.png)

#### Use the command to start mall-admin-web
- Enter the following command in the IDEA console:
```shell
npm run dev
```
![](../images/tech_screen_16.png)
- Visit the address http://localhost:8090 to see the effect:
![](../images/tech_screen_17.png)
- Perform a login operation and find that the local interface is called:
![](../images/tech_screen_18.png)

### Start without building a mall background environment

> Those who have not set up the mall background need to use online api to access, online API address: http://120.27.63.9:8080.

#### Modify the BASE API in the dev.env.js file to an online address

![](../images/tech_screen_19.png)

#### Use the command to start mall-admin-web

- Enter the following command in the IDEA console:
```shell
npm run dev
```
![](../images/tech_screen_16.png)
- Visit the address http://localhost:8090 to see the effect:
![](../images/tech_screen_17.png)
- After logging in, it is found that the online interface is called:
![](../images/tech_screen_20.png)

## Linux deployment

- Modify the configuration of prod.env.js file
![](../images/tech_screen_21.png)
- Use commands to package
```shell
npm run build
```
![](../images/tech_screen_22.png)
- Packaged code location
![](../images/tech_screen_23.png)

- Package the dist directory as a dist.tar.gz file
![](../images/tech_screen_24.png)
- You can refer to the installation of nginx on Linux[Mall deployment in Linux environment (based on Docker container)](https://mp.weixin.qq.com/s/0fVMK107i5bBq8kGQqg8KA)中的nginx部分
- Upload dist.tar.gz to linux server (nginx related directory)
![](../images/tech_screen_27.png)
- Use this command to decompress
```shell
tar -zxvf dist.tar.gz
```
- Delete nginx html folder
```shell
rm -rf html
```
- Move dist folder to html folder
```shell
mv dist html
```
- Run the mall-admin service
```shell
docker start mall-admin
```
- Restart nginx
```shell
docker restart nginx
```
- Visit the homepage and log in：http://192.168.3.101
![](../images/tech_screen_25.png)
- It is found that the Linux server address is called
![](../images/tech_screen_26.png)

## Project source address

[https://github.com/kratos47mhs/mall-admin-web](https://github.com/kratos47mhs/https://github.com/kratos47mhs/mall-admin-web)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
