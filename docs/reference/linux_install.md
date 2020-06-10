In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) will be obtained immediately.

# Virtual machine installation and use of Linux, it is enough to read this article!

> This article will introduce the installation and use of Linux system in detail from three aspects: virtual machine installation, Linux system installation, and SSH client tool use.

## Virtual machine installation

> VirtualBox is an open source virtual machine software, produced by Sun, and now developed by Oracle. VirtualBox is known as the strongest free virtual machine software, it has excellent performance and is simple to use. Virtual systems include Windows, Linux, MacOS, Android and other operating systems! This article will use VirtualBox as a virtual machine to install the Linux system.

### Virtual Box installation

- We first download the Virtual Box installation package, download address: https://www.virtualbox.org/wiki/Downloads
- After the download is complete, double-click to run the installation package all the way and click Next:

![](../images/linux_install_01.png)

- You need to customize the installation path halfway:

![](../images/linux_install_02.png)

- Finally, click Finish to complete the installation.

![](../images/linux_install_03.png)

### Create a virtual machine

- Create a Linux virtual machine:

![](../images/linux_install_04.png)

- The size of the allocated virtual machine memory can be determined according to your computer configuration:

![](../images/linux_install_05.png)

- Create a virtual hard disk:

![](../images/linux_install_06.png)

- Set the virtual hard disk file type, choose VDI format here:

![](../images/linux_install_07.png)

- Choose the size of the dynamic allocation space, if your hard disk space is relatively large, you can choose a fixed size:

![](../images/linux_install_08.png)

- Set the location and size of the virtual hard disk:

![](../images/linux_install_09.png)

## Linux installation

> CentOS (Community Enterprise Operating System) is one of the Linux distributions, meaning Chinese as a community enterprise operating system. It comes from the commercial version of Red Hat Enterprise Linux compiled according to the source code released by the open source regulations, so it is highly stable and completely open source. This article will take CentOS 7.6 as an example to introduce the installation of Linux system.

### Download

- download link：http://vault.centos.org/7.6.1810/isos/x86_64/
- download file：CentOS-7-x86_64-DVD-1810.iso

### Installation

- Add a virtual disc for the virtual machine, and the virtual disc is designated as the ISO image file we downloaded:

![](../images/linux_install_10.png)

- Click Start to run the virtual machine:

![](../images/linux_install_11.png)

- After running successfully, select `Install CentOS 7` to install：

![](../images/linux_install_12.png)

- Select the language during system installation, it is recommended to choose the `English` option:

![](../images/linux_install_13.png)

- Some schematic diagrams that need to be set:

![](../images/linux_install_14.png)

- Time zone setting, region selection `Asia`, city selection `Shanghai`:

![](../images/linux_install_15.png)

- Language support Choose to install English or Simplified Chinese installation packages:

![](../images/linux_install_16.png)

- Software installation settings select `Server with GUI`, and choose three additional environments as shown in the figure:

![](../images/linux_install_17.png)

- Disk partition setting, because the memory set by my virtual machine is small, a larger `swap` partition needs to be created. If the memory setting is large, you can directly select automatic partitioning. Here we use manual partitioning:

![](../images/linux_install_18.png)

- Perform manual partition operation as shown in the figure;

![](../images/linux_install_19.png)

- Explanation of several directories on the partition:
    - /：Root partition
    - swap：Swap partition can be used as virtual memory;
    - /boot：Boot data and kernel data of the storage system;
    - /usr：Storage system application software installation information;
    - /var：Storage system log information.

- Network settings, set the host name and perform network activation operations:

![](../images/linux_install_20.png)

- Click `Begin Installation` to install:

![](../images/linux_install_21.png)

- The password of the `root` user can be set during the installation process;

![](../images/linux_install_22.png)

- Restart after completing the installation to enter the system, the first time you need to agree to the agreement and complete the configuration:

![](../images/linux_install_23.png)

- At this time, the host machine cannot directly access the virtual machine, and the network mode of the virtual machine needs to be changed to the bridge mode.

![](../images/linux_install_24.png)


## Xshell use

> Xshell is an SSH connection client tool that can be used to remotely operate Linux systems. Xshell is free for home and school use, provides a valid email, and the download link will be sent to your email.

- Free version application address：https://www.netsarang.com/zh/free-for-home-school/

- After downloading and installing Xshell, open the command line in the Linux system:

![](../images/linux_install_25.png)

- Enter the `ifconfig` command to obtain the IP address;

![](../images/linux_install_26.png)

- After the acquisition is complete, connect through Xshell and enter the account number and password:

![](../images/linux_install_27.png)

- After the connection is completed, you can use the Linux system remotely.

![](../images/linux_install_28.png)

## Other related

### Modify the default startup mode

If you do not want to start the graphical interface by default, you can modify the default startup mode, because the graphical interface is still relatively memory-intensive, use the following command.

```bash
# Change the default level to multi-user text mode
systemctl set-default multi-user.target
# Change the default level to graphical user interface mode
systemctl set-default graphical.target
# Reboot
reboot
```

### Common Linux commands

You can refer to this：[Linux commands for developers](/reference/linux)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)