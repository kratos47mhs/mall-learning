The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.
# Mall integrates Spring Task to implement timed tasks

> This article mainly explains the process of Mall integrating Spring Task, taking batch modification of overtime orders as an example.

## Introduction to the project framework

### SpringTask

> Spring Task is a lightweight timing task tool independently developed by Spring. It is simpler and more convenient than Quartz, and can be used without introducing other dependencies.

### Cron expression

> Cron expression is a string, including 6 ~ 7 time elements, which can be used to specify the execution time of the task in Spring Task.

#### Cron syntax
Seconds Minutes Hours DayofMonth Month DayofWeek

#### Description of each time element in Cron format

Time element | Appearable characters | Effective value range
----|----|----
Seconds | , - * / | 0-59
Minutes | , - * / | 0-59
Hours | , - * / | 0-23  
DayofMonth | , - * / ? L W | 0-31
Month | , - * / | 1-12
DayofWeek | , - * / ? L # | 1-7或SUN-SAT

#### Description of special characters in Cron format

Character | Intention | Examples
----|----|----
, | List enumeration values | Use 5,10 in the Minutes field, which means that it will trigger once every 5 minutes and 10 minutes
\- | Indicates the trigger range | Use 5-10 in the Minutes field, which means that it will be triggered every 5 minutes to 10 minutes
\* | Match any value | Use * in the Minutes field, which means it will be triggered every minute
/ | Start at the start time, trigger once every fixed time | Use 5/10 in the Minutes field, which means that it will be triggered once at 5 minutes, and it will be triggered again every 10 minutes.
? | In Dayof Month and Dayof Week, used to match any value | Use in the Dayof Month field ?, which means that it is triggered once a day
\# | In Dayof Month, determine the day of the week | 1 # 3 means the third Sunday
L | Means last | Use 5 L in Dayof Week, which means it is triggered on the last Thursday
W | Indicates a valid working day (Monday to Friday) | Use 5 W on Dayof Month, if the 5th is Saturday, it will be triggered once on the 4th of the most recent working day

## Business scenario description

- The user places an order for a product；
- The system needs to generate an order based on the information of the product purchased by the user and lock the inventory of the product；
- The system sets the user to cancel the order without paying for 60 minutes；
- Start a scheduled task, check every 10 minutes, if there is an order that has not been paid for overtime, cancel the order and cancel the locked merchandise inventory.

## Integrate Spring Task
> Since the Spring Task already exists in the Spring framework, there is no need to add dependencies.

### Add Spring Task configuration

> Just add an @Enable Scheduling annotation to the configuration class to enable the Spring Task's scheduled task capability.

```java
package com.macro.mall.tiny.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableScheduling;

/**
 * Scheduled task configuration
 * Created by macro on 2019/4/8.
 */
@Configuration
@EnableScheduling
public class SpringTaskConfig {
}
```

### Add Order Time Out Cancel Task to perform scheduled tasks
```java
package com.macro.mall.tiny.component;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

/**
 * Created by macro on 2018/8/24.
 * Order timeout canceled and unlocked inventory timer
 */
@Component
public class OrderTimeOutCancelTask {
    private Logger LOGGER = LoggerFactory.getLogger(OrderTimeOutCancelTask.class);

    /**
     * cron expression：Seconds Minutes Hours DayofMonth Month DayofWeek [Year]
     * Scan every 10 minutes, scan the order placed before the set timeout, and cancel the order if no payment
     */
    @Scheduled(cron = "0 0/10 * ? * ?")
    private void cancelTimeOutOrder() {
        // TODO: 2019/5/3 Here you should call the method of canceling the order, specifically check the source code of the mall project
        LOGGER.info("Cancel order and release locked inventory based on sku number");
    }
}

```

## Project source address
[https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-05](https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-05)

## No public

![Public account picture](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/banner/qrcode_for_macrozheng_258.jpg)
