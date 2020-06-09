In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) get it immediately.

# Set up your own Git repository in 10 minutes

> Git Lab is a web-based Git warehouse management tool that uses the MIT license. We can use it to build our own Git warehouse. This article will introduce how to use Gitlab to quickly build a Git warehouse under Linux.


## Gitlab server build

> Under Linux (Center OS 7.6), we will install Gitlab with Docker. Friends who don’t know Docker can refer to: [Developer Essential Docker Command] (https://mp.weixin.qq.com/s/d_CuljDTJq680NTndAay8g).

### Download Gitlab's Docker image

```bash
docker pull gitlab/gitlab-ce
```

### Run the following command to start Gitlab

> It should be noted that our Gitlab http service runs on port 1080 of the host machine. Here we map the Gitlab configuration, logs and data directory to the specified folder of the host machine to prevent us from losing data after recreating the container.

```bash
docker run --detach \
  --publish 10443:443 --publish 1080:80 --publish 1022:22 \
  --name gitlab \
  --restart always \
  --volume /mydata/gitlab/config:/etc/gitlab \
  --volume /mydata/gitlab/logs:/var/log/gitlab \
  --volume /mydata/gitlab/data:/var/opt/gitlab \
  gitlab/gitlab-ce:latest
```

### Open the designated port of the firewall

> Since Gitlab runs on port 1080, we need to open this port. Be careful not to close the firewall directly, otherwise Gitlab will not start.

```bash
# Open 1080 port
firewall-cmd --zone=public --add-port=1080/tcp --permanent 
# Restart the firewall to take effect
systemctl restart firewalld
# View the open ports
firewall-cmd --list-ports
```

### Visit Gitlab
- Address：[http://192.168.3.101:1080/](http://192.168.3.101:1080/)
- Because Gitlab is slow to start, you need to wait patiently for about 10 minutes. If Gitlab does not start to complete the access, the following error will occur.

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_04.png)
- You can use the docker command to dynamically view the container startup log to know whether gitlab has been started.
```bash
docker logs gitlab -f
```

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_05.png)


## Use of Gitlab

### Gitlab is accessed for the first time after starting, it will let you reset the password of the root account
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_06.png)
### After the reset is complete, enter the account password to log in
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_07.png)
### Choose to create a project, create an organization, create an account
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_08.png)
### Create Organization
First, we need to create an organization, and then create users and projects under this organization, so that users in the same organization can use the projects under the organization.
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_09.png)
### Create user and change password

#### Find the button to add users

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_10.png)
#### Enter username and password to add user
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_11.png)
#### Modify user password in the editing interface
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_12.png)

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_13.png)
### Create project and add README file
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_14.png)

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_15.png)
### Assign users to organizations
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_16.png)

## Git client installation and use

### Download the Git client and install

- download link：https://github.com/git-for-windows/git/releases/download/v2.23.0.windows.1/Git-2.23.0-64-bit.exe
- After the download is complete, click Next to install all the way.

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_01.png)

### clone project

- Find the address of the project clone:

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_17.png)
- Open the Git command line tool:
  
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_18.png)
- Execute the following command to clone the project to the local:

```bash
git clone http://192.168.3.101:1080/kratos47mhs/hello.git

```

### Commit code

Enter the project directory, modify README.md and commit:
```bash
# Enter the project engineering directory
cd hello/
# Add the currently modified file to the staging area
git add .
# Commit code
git commit -m "first commit"
```

### Push to remote repository
```bash
git push
```
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_19.png)

### Pull remote repository code

- Modify the content of the file in readme on Gitlab:

![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_20.png)
- Pull the code:
 ```bash
git pull
```

### Create and commit branches locally

```bash
# Switch and create a dev branch from the current branch
git checkout -b dev
# Push the newly created dev branch to the remote repository
git push origin dev
```
![](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/blog/gitlab_screen_21.png)

### Other common commands

```bash
# Switch to dev branch
git checkout dev
# View the status of local repository files
git status
# View all local branches
git branch
# View submission history
git log
```

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
