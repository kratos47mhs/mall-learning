The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.

# Mall-swarm deployment in Windows environment

## Development environment

> The environment used in `mall-swarm` is roughly the same as in the `mall` project, you can check for details[Mall deployment in Windows environment](/deploy/mall_deploy_windows.md)。

Simple environment construction process:

- Install IDEA and import project source code;
- Install My Sql, create a `mall` database, and import the `/document/sql/mall.sql` file;
- Install Redis, Elasticsearch, Mongo DB, Rabbit MQ and other environments.

## Project deployment

> The `mall-swarm` project has a starting sequence, everyone should start in the following order.

### Start the registration center `mall-registry`

- Simply run the main function of com.macro.mall.Mall Registry Application;
- After the operation is completed, you can view it through the registration center console: http://localhost:8001

### Start the configuration center `mall-config`

- Simply run the main function of com.macro.mall.Mall Config Application;
- Access the following interface to obtain the configuration information of mall-admin in the dev environment: http://localhost:8301/master/admin-dev.yml

### Start the monitoring center `mall-monitor`

- Just run the main function of com.macro.mall.Mall Monitor Application directly;
- After the operation is completed, you can view it through the monitoring center console: http://localhost:8101
- Enter the account password `macro:123456` to log in to view.

### Start the gateway service `mall-gateway`

- Simply run the main function of com.macro.mall.Mall Gateway Application;
- Visit the following interface for dynamic routing rules: http://localhost:8201/actuator/gateway/routes

### Start the background management service `mall-admin`

- Simply run the main function of com.macro.mall.Mall Admin Application;
- Access the interface document through the `mall-gateway` gateway service: http://localhost:8201/mall-admin/swagger-ui.html

![](../images/mall_swarm_windows_06.png)

- Login interface address：http://localhost:8201/mall-admin/admin/login
- After accessing the login interface and obtaining the token, put the authentication header information to access other interfaces that require login normally:

![](../images/mall_swarm_windows_09.png)

### Start the front desk service `mall-portal`

- Simply run the main function of com.macro.mall.portal.Mall Portal Application;
- Access the interface document through the `mall-gateway` gateway service: http://localhost:8201/mall-portal/swagger-ui.html

![](../images/mall_swarm_windows_07.png)

- Login interface address：http://localhost:8201/mall-portal/sso/login
- The method of calling the interface that requires login is the same as `mall-admin`.

### Start the search service `mall-search`

- Simply run the main function of com.macro.mall.search.Mall Search Application;
- Access the interface document through the `mall-gateway` gateway service: http://localhost:8201/mall-search/swagger-ui.html

![](../images/mall_swarm_windows_10.png)

### Start the test service `mall-demo`

- Simply run the main function of com.macro.mall.Mall Admin Application;
- Access the interface document through the `mall-gateway` gateway service: http://localhost:8201/mall-demo/swagger-ui.html

![](../images/mall_swarm_windows_08.png)

- You can test the remote calling function using Feign by calling Feign Admin Controller, Feign Portal Controller, and Feign Search Controller.

## Show results

- Registration center service information:

![](../images/mall_swarm_windows_01.png)

- Service overview information of monitoring center:

![](../images/mall_swarm_windows_02.png)

![](../images/mall_swarm_windows_03.png)

- Single application details of monitoring center:

![](../images/mall_swarm_windows_04.png)

![](../images/mall_swarm_windows_05.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)


