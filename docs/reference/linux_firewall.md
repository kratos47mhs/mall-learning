In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) will be obtained immediately.

## Use of Linux Firewall and Iptables

> There are two kinds of firewall software in Linux. Conter OS 7.0 and above use firewall and Conter OS 7.0 and below use iptables. This article will introduce the use of two firewall software.

## Firewall

- Turn on the firewall:
```shell
systemctl start firewalld
```
- Turn off the firewall:
```shell
systemctl stop firewalld
```
- Check the firewall status:
```shell
systemctl status firewalld
```
- Set start service at startup:
```shell
systemctl enable firewalld
```
- Disable start at startup:
```shell
systemctl disable firewalld
```
- Restart the firewall:
```shell
firewall-cmd --reload
```
- Open port (the firewall needs to be restarted after modification to take effect):
```shell
firewall-cmd --zone=public --add-port=8080/tcp --permanent
```
![](../images/refer_screen_31.png)
- View open ports:
```shell
firewall-cmd --list-ports
```
![](../images/refer_screen_32.png)
- Close the port:
```shell
firewall-cmd --zone=public --remove-port=8080/tcp --permanent
```
![](../images/refer_screen_33.png)

## Iptables

### Installation

> Since Center OS 7.0 and above does not pre-install Iptables, we need to install it by ourselves.

- Turn off the firewall before installation
![](../images/refer_screen_34.png)
- Install iptables:
```shell
yum install iptables
```
- Install iptables-services:
```shell
yum install iptables-services
```

### 使用

- Turn on the firewall:
```shell
systemctl start iptables.service
```
![](../images/refer_screen_35.png)
- Turn off the firewall:
```shell
systemctl stop iptables.service
```
- Check the firewall status:
```shell
systemctl status iptables.service
```
- Set the boot to start:
```shell
systemctl enable iptables.service
```
- Set start service at startup:
```shell
systemctl disable iptables.service
```
- Check the chain rules of the filter table (the INPUT chain can see which ports are open):
```shell
iptables -L -n
```
![](../images/refer_screen_36.png)
- View the chain rules of the NAT table:
```shell
iptables -t nat -L -n
```
![](../images/refer_screen_37.png)
- Clear all firewall rules:
```shell
iptables -F
```
```shell
iptables -X
```
```shell
iptables -Z
```
- Add rules to the INPUT chain (open port 8080):
```shell
iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
```
![](../images/refer_screen_38.png)
- Find the line number where the rule is located:
```shell
iptables -L INPUT --line-numbers -n
```
![](../images/refer_screen_39.png)
- Delete the filter rule based on the line number (close port 8080):
```shell
iptables -D INPUT 1
```
![](../images/refer_screen_40.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
