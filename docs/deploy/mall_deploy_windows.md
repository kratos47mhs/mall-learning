The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Mall deployment in Windows environment

> This article mainly explains the installation of the environment required by the mall project in windows in the form of graphics, mainly including IDEA, Mysql, Redis, Elasticsearch, Mongodb, Rabbit MQ, OSS.

## IDEA

1.Specific reference on the installation and use of IDEA[https://www.youtube.com/user/intellijideavideo/playlists](https://www.youtube.com/user/intellijideavideo/playlists)

2.Search plugin repository, install lombok plugin .

![](../images/arch_screen_101.png)
3.Download the project locally and open it directly.

![](../images/arch_screen_102.png)

![](../images/arch_screen_103.png)

## Mysql

1. Download and install mysql version 5.7, download address：https://dev.mysql.com/downloads/installer/
2. Set database account password：root root
3. Download and install the client connection tool Navicat, download address：http://www.formysql.com/xiazai.html
4. Create database mall
5. Import the mall.sql file under document/sql

## Redis

1.Download Redis, download address：https://github.com/MicrosoftArchive/redis/releases

![](../images/arch_screen_09.png)

2.After downloading, unzip to the specified directory

![](../images/arch_screen_10.png)

3.After entering cmd in the current address bar, execute the redis start command：redis-server.exe redis.windows.conf

![](../images/arch_screen_11.png)

## Elasticsearch

1.Download the zip package of Elasticsearch 6.2.2 and unzip it to the specified directory, download address：[https://www.elastic.co/cn/downloads/past-releases/elasticsearch-6-2-2](https://www.elastic.co/cn/downloads/past-releases/elasticsearch-6-2-2)

![](../images/arch_screen_25.png)

2.Install the Chinese word segmentation plugin, execute the following command in the elasticsearch-6.2.2\bin directory：elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.2.2/elasticsearch-analysis-ik-6.2.2.zip

![](../images/arch_screen_26.png)

3.Run elasticsearch.bat in the bin directory to start Elasticsearch

![](../images/arch_screen_27.png)

4.Download Kibana, as a client to access Elasticsearch, please download the 6.2.2 version of the zip package, and unzip it to the specified directory, download address：[https://artifacts.elastic.co/downloads/kibana/kibana-6.2.2-windows-x86_64.zip](https://artifacts.elastic.co/downloads/kibana/kibana-6.2.2-windows-x86_64.zip)

![](../images/arch_screen_28.png)

5.Run kibana.bat in the bin directory to start the Kibana user interface

![](../images/arch_screen_29.png)

6.Visit [http://localhost:5601](http://localhost:5601) To open the Kibana user interface

![](../images/arch_screen_30.png)

## Mongodb

1.Download Mongodb installation package, download address：[https://fastdl.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-3.2.21-signed.msi](https://fastdl.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-3.2.21-signed.msi)

2.Choose the installation path to install

![](../images/arch_screen_37.png)

![](../images/arch_screen_38.png)

3.Create two folders data\\db and data\\log under the installation path

![](../images/arch_screen_39.png)

4.Create a mongod.cfg configuration file under the installation path
```
systemLog:
    destination: file
    path: D:\developer\env\MongoDB\data\log\mongod.log
storage:
    dbPath: D:\developer\env\MongoDB\data\db
```

5.Install as a service (administrator privileges are required to run commands)
```
D:\developer\env\MongoDB\bin\mongod.exe --config "D:\developer\env\MongoDB\mongod.cfg" --install
```
![](../images/arch_screen_40.png)

6.Service related commands
```
Start service：net start MongoDB
Stop service：net stop MongoDB
Remove service：D:\developer\env\MongoDB\bin\mongod.exe --remove
```

7.Download the client program：[https://download.robomongo.org/1.2.1/windows/robo3t-1.2.1-windows-x86_64-3e50a65.zip](https://download.robomongo.org/1.2.1/windows/robo3t-1.2.1-windows-x86_64-3e50a65.zip)

8.Unzip to the specified directory, open robo3t.exe and connect to localhost:27017

![](../images/arch_screen_41.png)

## RabbitMQ

1.Install Erlang, download address：[http://erlang.org/download/otp_win64_21.3.exe](http://erlang.org/download/otp_win64_21.3.exe)

![](../images/arch_screen_53.png)

2.Install Rabbit MQ, download address：[https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.14/rabbitmq-server-3.7.14.exe](https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.14/rabbitmq-server-3.7.14.exe)

![](../images/arch_screen_54.png)

3.After the installation is complete, enter the sbin directory under the Rabbit MQ installation directory

![](../images/arch_screen_55.png)

4.Enter cmd in the address bar and press Enter to start the command line, then enter the following command to start the management function:
```
rabbitmq-plugins enable rabbitmq_management
```
![](../images/arch_screen_56.png)

5.Visit the address to see if the installation is successful：[http://localhost:15672/](http://localhost:15672/)

![](../images/arch_screen_57.png)

6.Enter account password and login：guest guest

7.Create an account and set its role as an administrator：mall mall

![](../images/arch_screen_58.png)

8.Create a new virtual host as：/mall

![](../images/arch_screen_59.png)

9.Click the mall user to enter the user configuration page

![](../images/arch_screen_60.png)

10.Configure the permission of the virtual host for the mall user

![](../images/arch_screen_61.png)

11.At this point, the installation and configuration of Rabbit MQ is complete.

## OSS

### Open OSS service

- Log on to Alibaba Cloud official website;
- Move the mouse to the product tab, click the object storage OSS, and open the OSS product details page;
- On the OSS product details page, click Activate Now.

### Create storage

- Click the console button in the upper right corner of the webpage to enter the console

![](../images/arch_screen_77.png)

- Select object storage OSS in my cloud product

![](../images/arch_screen_78.png)

- Click the plus sign of the left storage space to create a new storage space

![](../images/arch_screen_79.png)

- Create a new storage space and set the read and write permissions to public

![](../images/arch_screen_80.png)


### Cross-domain resource sharing (CORS) settings

- Select a storage space and open its basic settings

![](../images/arch_screen_81.png)

- Click the settings button across settings

![](../images/arch_screen_82.png)

- Click Create Rule

![](../images/arch_screen_83.png)

- Make cross-domain rule settings

![](../images/arch_screen_84.png)

## mall-admin

- Start the project: directly run the main method of com.macro.mall.Mall Admin Application
- Interface document address：[http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)

## mall-search

- Start the project: directly run the main method of com.macro.mall.search.Mall Search Application
- Interface document address：[http://localhost:8081/swagger-ui.html](http://localhost:8081/swagger-ui.html)
- You need to call the interface to import data before use；[http://localhost:8081/esProduct/importAll](http://localhost:8081/esProduct/importAll)
- If there is a problem that cannot be started, you can delete the data in Elasticsearch before starting

## mall-portal

- Start the mall-portal project: directly run the main method of com.macro.mall.portal.Mall Portal Application
- Interface document address：[http://localhost:8085/swagger-ui.html](http://localhost:8085/swagger-ui.html)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
