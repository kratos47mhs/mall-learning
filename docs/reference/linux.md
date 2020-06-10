In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) will be obtained immediately.

# Linux commands for developers

> Developers must have Linux commonly used commands. It is absolutely enough to master these commands, based on Center OS 7.6.

## System Service Management

### systemctl
- Output the status of various services in the system:
```shell
systemctl list-units --type=service
```
![](../images/refer_screen_01.png)
- View the running status of the service:
```shell
systemctl status firewalld
```
![](../images/refer_screen_02.png)
- Close the service:
```shell
systemctl stop firewalld
```
![](../images/refer_screen_03.png)
- Start the service:
```shell
systemctl start firewalld
```
![](../images/refer_screen_04.png)
- Restart the service (regardless of whether the current service is started or closed):
```shell
systemctl restart firewalld
```
- Reload configuration information without interrupting service:
```shell
systemctl reload firewalld
```
- Forbid the service to start automatically after booting:
```shell
systemctl disable firewalld
```
![](../images/refer_screen_05.png)
- Set the service to start automatically after booting:
```shell
systemctl enable firewalld
```
![](../images/refer_screen_06.png)

## Document management

### ls
List all files in the current directory (/):
```shell
ls -l /
```
![](../images/refer_screen_07.png)

### pwd
Get the absolute path of the current working directory

![](../images/refer_screen_08.png)

### cd
Change the current working directory:
```shell
cd /usr/local
```

![](../images/refer_screen_09.png)

### date
Display or modify the system time and date;
```shell
date '+%Y-%m-%d %H:%M:%S'
```
![](../images/refer_screen_10.png)

### passwd
Used to set user password:
```shell
passwd root
```

![](../images/refer_screen_11.png)

### su
Change user identity (switch to superuser):
```shell
su -
```
### clear
Used to clear screen information

### man
Display help information for the specified command:
```shell
man ls
```
### who
- Query what operating level the system is in:
```shell
who -r
```
![](../images/refer_screen_12.png)
- Display the users currently logged in to the system:
```shell
who -buT
```
![](../images/refer_screen_13.png)

### free
Display system memory status (in MB):
```shell
free -m
```
![](../images/refer_screen_14.png)

### ps
Display system process running dynamics:
```shell
ps -ef
```
View the running dynamics of the sshd process:
```shell
ps -ef | grep sshd
```
![](../images/refer_screen_15.png)

### top
View instant active processes, similar to the Windows task manager

![](../images/refer_screen_16.png)

### mkdir
Create a directory

![](../images/refer_screen_17.png)

### more
Used to view the contents of the file when the file is too long
10 lines per page to view the boot.log file
```shell
more -c -10 /var/log/boot.log
```
![](../images/refer_screen_18.png)

### cat
View the Linux boot log file and indicate the line number:
```shell
cat -Ab /var/log/boot.log
```

![](../images/refer_screen_19.png)

### touch
Create text.txt file:
```shell
touch text.txt
```

![](../images/refer_screen_20.png)

### rm
- Delete Files:
```shell
rm text.txt
```
- Forcibly delete a directory and its subdirectories:
```shell
rm -rf testdir/
```

![](../images/refer_screen_21.png)

### cp
Copy test 1 directory to test 2 directory
```shell
cp -r /mydata/tes1 /mydata/test2
```
### mv
Move or Replace files:
```shell
mv text.txt text2.txt
```

## Compression and decompression

### tar
- Archive the files in the /etc folder to the file etc.tar (and will not be compressed):
```shell
tar -cvf /mydata/etc.tar /etc
```
- Use gzip to compress the files in the folder /etc to the file etc.tar.gz:
```shell
tar -zcvf /mydata/etc.tar.gz /etc
```
- Compress the folder /etc to the file /etc.tar.bz2 with bzip2:
```shell
tar -jcvf /mydata/etc.tar.bz2 /etc
```
![](../images/refer_screen_22.png)
- View the contents of the compressed package by page (gzip):
```shell
tar -ztvf /mydata/etc.tar.gz |more -c -10
```
![](../images/refer_screen_24.png)
- Unzip the file to the current directory (gzip):
```shell
tar -zxvf /mydata/etc.tar.gz
```
- Unzip the file to the specified directory (gzip):
```shell
tar -zxvf /mydata/etc.tar.gz -C /mydata/etc
```

## Disk and network management

### df
View disk space usage:
```shell
df -hT
```
![](../images/refer_screen_25.png)

### dh
View the size of files and folders in the current directory:
```shell
du -h --max-depth=1 ./*
```
![](../images/refer_screen_26.png)

### ifconfig
Display current network interface status

![](../images/refer_screen_27.png)

### netstat

- View the current routing information:
```shell
netstat -rn
```
![](../images/refer_screen_28.png)

- View all valid TCP connections:
```shell
netstat -an
```
- View the monitoring service started in the system:
```shell
netstat -tulnp
```
![](../images/refer_screen_29.png)

- View the system resource information in the connected state:
```shell
netstat -atunp
```

### wget
Download files from the web

![](../images/refer_screen_30.png)

## File upload and download

### Install upload and download tools

```bash
yum install -y lrzsz
```

### upload files

```bash
rz
```

### download file

```bash
sz fileName
```

## Software installation and management

### rpm

- Install packages：rpm -ivh nginx-1.12.2-2.el7.x86_64.rpm
- Find and remove old nginx server version modules：rpm -qa | grep nginx
- Find packages precisely：rpm -qa nginx
- Query the installation path of the software package：rpm -ql nginx-1.12.2-2.el7.x86_64
- View summary information about the software package：rpm -qi nginx-1.12.2-2.el7.x86_64
- Verify the package contents and installation files are consistent：rpm -V nginx-1.12.2-2.el7.x86_64
- Update packages：rpm -Uvh nginx-1.12.2-2.el7.x86_64
- Remove packages：rpm -e nginx-1.12.2-2.el7.x86_64

### yum

- Install packages： yum install nginx
- Check packages that can be updated：yum check-update
- Update the specified package：yum update nginx
- Find package information in the repository：yum info nginx*
- List all installed packages：yum info installed
- List package names：yum list nginx*
- Find nginx package：yum search nginx

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
