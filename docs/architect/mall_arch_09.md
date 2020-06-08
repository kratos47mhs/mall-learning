The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.

# Mall integrates Rabbit MQ to realize delayed message

> This article mainly explains the process of Mall's integration of Rabbit MQ to implement delayed messages. Taking delay messages to cancel overtime orders as an example.

## Introduction to the project framework

### RabbitMQ

> Rabbit MQ is a widely used open source message queue. It is lightweight and easy to deploy, and it can support multiple messaging protocols. Rabbit MQ can be deployed in distributed and joint configurations to meet the needs of high scale and high availability.

#### Rabbit MQ installation and use

1. Install Erlang, download address：[http://erlang.org/download/otp_win64_21.3.exe](http://erlang.org/download/otp_win64_21.3.exe)

![](../images/arch_screen_53.png)

2. Install Rabbit MQ, download address：[https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.14/rabbitmq-server-3.7.14.exe](https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.14/rabbitmq-server-3.7.14.exe)

![](../images/arch_screen_54.png)

3. After the installation is complete, enter the sbin directory under the Rabbit MQ installation directory

![](../images/arch_screen_55.png)

4. Enter cmd in the address bar and press Enter to start the command line, then enter the following command to start the management function：
```
rabbitmq-plugins enable rabbitmq_management
```
![](../images/arch_screen_56.png)

5. Visit the address to see if the installation is successful：[http://localhost:15672/](http://localhost:15672/)

![](../images/arch_screen_57.png)

6. Enter account password and login：guest guest

7. Create an account and set its role as an administrator：mall mall

![](../images/arch_screen_58.png)

8. Create a new virtual host as：/mall

![](../images/arch_screen_59.png)

9. Click the mall user to enter the user configuration page

![](../images/arch_screen_60.png)

10. Configure the permission of the virtual host for the mall user

![](../images/arch_screen_61.png)

11. At this point, the installation and configuration of Rabbit MQ is complete.

#### Rabbit MQ's message model

![](../images/arch_screen_52.png)

Symbol | Chinese name | English name| Description
----|----|----|----
P	|生产者	|Producer |The sender of the message can send the message to the switch
C	|消费者	|Consumer |The receiver of the message gets the message from the queue for consumption
X	|交换机	|Exchange |Receive the message sent by the producer and send it to the specified queue according to the routing key
Q	|队列	|Queue |Store messages from the switch
type | 交换机类型 |type |direct means to send the message directly according to the routing key (orange / black)

### Lombok

> Lombok adds very interesting additional features to the Java language. You can no longer need to write getter, setter and other methods for the entity class, you can have it through a comment.

Notice：Need to install idea's Lombok plugin, and add dependencies in the pom file in the project.

![](../images/arch_screen_48.png)

## Business scenario description
> It is used to solve the problem of how to cancel the order after the user places an order.

- The user performs the order operation (there will be a series of operations such as locking the product inventory, using coupons and points)；
- Generate order, get order id；
- Obtain the set order timeout time (assuming that the set time is 60 minutes without payment to cancel the order)；
- Send a delay message to Rabbit MQ according to the order timeout time, so that it triggers the cancellation of the order after the order times out；
- If the user does not pay, perform the order cancellation operation (a series of operations to release the locked merchandise inventory, return coupons, and return points).

## Integrate Rabbit MQ to realize delayed message

### Add related dependencies in pom.xml

```xml
<!--Message queue related dependencies-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
<!--lombok dependency-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

### Modify the Spring Boot configuration file

> Modify the application.yml file and add Rabbit MQ related configuration under the spring node.

```yml
  rabbitmq:
    host: localhost # rabbitmq connection address
    port: 5672 # Rabbitmq connection port number
    virtual-host: /mall # rabbitmq virtual host
    username: mall # rabbitmq username
    password: mall # rabbitmq password
    publisher-confirms: true # If the callback is required for asynchronous messages, it must be set to true
```

### Add enumeration configuration class Queue Enum

> Constant definitions used to delay message queues and process order cancellation message queues, including switch name, queue name, routing key name

```java
package com.macro.mall.tiny.dto;

import lombok.Getter;

/**
 * Message queue enumeration configuration
 * Created by macro on 2018/9/14.
 */
@Getter
public enum QueueEnum {
    /**
     * Message notification queue
     */
    QUEUE_ORDER_CANCEL("mall.order.direct", "mall.order.cancel", "mall.order.cancel"),
    /**
     * Message notification ttl queue
     */
    QUEUE_TTL_ORDER_CANCEL("mall.order.direct.ttl", "mall.order.cancel.ttl", "mall.order.cancel.ttl");

    /**
     * Exchange name
     */
    private String exchange;
    /**
     * Queue name
     */
    private String name;
    /**
     * Routing key
     */
    private String routeKey;

    QueueEnum(String exchange, String name, String routeKey) {
        this.exchange = exchange;
        this.name = name;
        this.routeKey = routeKey;
    }
}

```

### Add Rabbit MQ configuration
> Used to configure switches, queues, and the binding relationship between queues and switches.

```java
package com.macro.mall.tiny.config;

import com.macro.mall.tiny.dto.QueueEnum;
import org.springframework.amqp.core.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * Message queue configuration
 * Created by macro on 2018/9/14.
 */
@Configuration
public class RabbitMqConfig {

    /**
     * The switch bound to the actual consumption queue of the order message
     */
    @Bean
    DirectExchange orderDirect() {
        return (DirectExchange) ExchangeBuilder
                .directExchange(QueueEnum.QUEUE_ORDER_CANCEL.getExchange())
                .durable(true)
                .build();
    }

    /**
     * The switch to which the order delay queue is bound
     */
    @Bean
    DirectExchange orderTtlDirect() {
        return (DirectExchange) ExchangeBuilder
                .directExchange(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getExchange())
                .durable(true)
                .build();
    }

    /**
     * Order actual consumption queue
     */
    @Bean
    public Queue orderQueue() {
        return new Queue(QueueEnum.QUEUE_ORDER_CANCEL.getName());
    }

    /**
     * Order delay queue (dead letter queue)
     */
    @Bean
    public Queue orderTtlQueue() {
        return QueueBuilder
                .durable(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getName())
                .withArgument("x-dead-letter-exchange", QueueEnum.QUEUE_ORDER_CANCEL.getExchange())//Switch forwarded after expiration
                .withArgument("x-dead-letter-routing-key", QueueEnum.QUEUE_ORDER_CANCEL.getRouteKey())//Route key forwarded after expiration
                .build();
    }

    /**
     * Bind the order queue to the switch
     */
    @Bean
    Binding orderBinding(DirectExchange orderDirect,Queue orderQueue){
        return BindingBuilder
                .bind(orderQueue)
                .to(orderDirect)
                .with(QueueEnum.QUEUE_ORDER_CANCEL.getRouteKey());
    }

    /**
     * Bind the order delay queue to the switch
     */
    @Bean
    Binding orderTtlBinding(DirectExchange orderTtlDirect,Queue orderTtlQueue){
        return BindingBuilder
                .bind(orderTtlQueue)
                .to(orderTtlDirect)
                .with(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getRouteKey());
    }

}
```

#### On the Rabbit MQ management page, you can see the following switches and queues

![](../images/arch_screen_62.png)
![](../images/arch_screen_63.png)

![](../images/arch_screen_64.png)
![](../images/arch_screen_65.png)

#### Switch and queue description

- mall.order.direct（Cancel the switch bound to the order message queue）:The bound queue is mall.order.cancel，Once a message is sent using mall.order.cancel as the routing key，Will be sent to this queue.
- mall.order.direct.ttl（The switch to which the order delay message queue is bound）:The bound queue is mall.order.cancel.ttl，Once a message is sent with the routing key mall.order.cancel.ttl，Will be forwarded to this queue and stored in this queue for a certain time，After the timeout, it will automatically send the message to mall.order.cancel (cancel order message consumption queue).

### Add DelayOrderSender

> Used to send messages to the order delay message queue (mall.order.cancel.ttl).

```java
package com.macro.mall.tiny.component;

import com.macro.mall.tiny.dto.QueueEnum;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.AmqpException;
import org.springframework.amqp.core.AmqpTemplate;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.core.MessagePostProcessor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

/**
 * The sender of the order cancellation message
 * Created by macro on 2018/9/14.
 */
@Component
public class CancelOrderSender {
    private static Logger LOGGER =LoggerFactory.getLogger(CancelOrderSender.class);
    @Autowired
    private AmqpTemplate amqpTemplate;

    public void sendMessage(Long orderId,final long delayTimes){
        //Send a message to the deferred queue
        amqpTemplate.convertAndSend(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getExchange(), QueueEnum.QUEUE_TTL_ORDER_CANCEL.getRouteKey(), orderId, new MessagePostProcessor() {
            @Override
            public Message postProcessMessage(Message message) throws AmqpException {
                //Set the delay in milliseconds for the message
                message.getMessageProperties().setExpiration(String.valueOf(delayTimes));
                return message;
            }
        });
        LOGGER.info("send delay message orderId:{}",orderId);
    }
}

```

### Add CancelOrderReceiver

> Used to receive messages from the message queue (mall.order.cancel) for canceling orders.

```java
package com.macro.mall.tiny.component;

import com.macro.mall.tiny.service.OmsPortalOrderService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

/**
 * Processor to cancel the order message
 * Created by macro on 2018/9/14.
 */
@Component
@RabbitListener(queues = "mall.order.cancel")
public class CancelOrderReceiver {
    private static Logger LOGGER =LoggerFactory.getLogger(CancelOrderReceiver.class);
    @Autowired
    private OmsPortalOrderService portalOrderService;
    @RabbitHandler
    public void handle(Long orderId){
        LOGGER.info("receive delay message orderId:{}",orderId);
        portalOrderService.cancelOrder(orderId);
    }
}

```

### Add Oms Portal Order Service interface

```java
package com.macro.mall.tiny.service;

import com.macro.mall.tiny.common.api.CommonResult;
import com.macro.mall.tiny.dto.OrderParam;
import org.springframework.transaction.annotation.Transactional;

/**
 * Front Order Management Service
 * Created by macro on 2018/8/30.
 */
public interface OmsPortalOrderService {

    /**
     * Generate orders based on submission information
     */
    @Transactional
    CommonResult generateOrder(OrderParam orderParam);

    /**
     * Cancel a single overtime order
     */
    @Transactional
    void cancelOrder(Long orderId);
}

```

### Add the implementation class of OmsPortalOrderService in OmsPortalOrderServiceImpl

```java
package com.macro.mall.tiny.service.impl;

import com.macro.mall.tiny.common.api.CommonResult;
import com.macro.mall.tiny.component.CancelOrderSender;
import com.macro.mall.tiny.dto.OrderParam;
import com.macro.mall.tiny.service.OmsPortalOrderService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

/**
 * Front Order Management Service
 * Created by macro on 2018/8/30.
 */
@Service
public class OmsPortalOrderServiceImpl implements OmsPortalOrderService {
    private static Logger LOGGER = LoggerFactory.getLogger(OmsPortalOrderServiceImpl.class);
    @Autowired
    private CancelOrderSender cancelOrderSender;

    @Override
    public CommonResult generateOrder(OrderParam orderParam) {
        //todo Perform a series of ordering operations, please refer to the mall project
        LOGGER.info("process generateOrder");
        //After the order is completed, a delay message is opened to cancel the order when the user has not paid (order Id should be generated after order)
        sendDelayMessageCancelOrder(11L);
        return CommonResult.success(null, "successfully ordered");
    }

    @Override
    public void cancelOrder(Long orderId) {
        //todo Perform a series of order cancellation operations, please refer to the mall project
        LOGGER.info("process cancelOrder orderId:{}",orderId);
    }

    private void sendDelayMessageCancelOrder(Long orderId) {
        //Get order timeout time, assuming 60 minutes
        long delayTimes = 30 * 1000;
        //Send a delayed message
        cancelOrderSender.sendMessage(orderId, delayTimes);
    }

}

```

### Add OmsPortalOrderController to define interface

```java
package com.macro.mall.tiny.controller;

import com.macro.mall.tiny.dto.OrderParam;
import com.macro.mall.tiny.service.OmsPortalOrderService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * Order Management Controller
 * Created by macro on 2018/8/30.
 */
@Controller
@Api(tags = "OmsPortalOrderController", description = "Order management")
@RequestMapping("/order")
public class OmsPortalOrderController {
    @Autowired
    private OmsPortalOrderService portalOrderService;

    @ApiOperation("Generate orders based on shopping cart information")
    @RequestMapping(value = "/generateOrder", method = RequestMethod.POST)
    @ResponseBody
    public Object generateOrder(@RequestBody OrderParam orderParam) {
        return portalOrderService.generateOrder(orderParam);
    }
}

```

## Conduct an interface test

### Call the order interface

Notice：The delay message time has been set to 30 seconds

![](../images/arch_screen_49.png)

![](../images/arch_screen_50.png)

![](../images/arch_screen_51.png)


## Project source address

[https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-08](https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-08)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
