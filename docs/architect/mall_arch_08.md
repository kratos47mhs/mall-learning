The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.

# Mall integrates Mongodb to achieve document operation

> This article mainly explains the process of Mall integration with Mongodb, taking the example of adding, deleting, and querying commodity browsing records in Mongodb.

## Introduction to the project framework

### Mongodb

> Mongodb is a database system built for the rapid development of Internet Web applications. Its data model and persistence strategy is to build a system with high read / write throughput and high automatic disaster recovery scalability.

#### Installation and use of Mongodb

1. Download Mongodb installation package, download address：[https://fastdl.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-3.2.21-signed.msi](https://fastdl.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-3.2.21-signed.msi)

2. Choose the installation path to install

![](../images/arch_screen_37.png)

![](../images/arch_screen_38.png)

3. Create two folders data\\db and data\\log under the installation path

![](../images/arch_screen_39.png)

4. Create the mongod.cfg configuration file under the installation path
```
systemLog:
    destination: file
    path: D:\developer\env\MongoDB\data\log\mongod.log
storage:
    dbPath: D:\developer\env\MongoDB\data\db
```

5. Install as a service (administrator privileges are required to run commands)
```
D:\developer\env\MongoDB\bin\mongod.exe --config "D:\developer\env\MongoDB\mongod.cfg" --install
```
![](../images/arch_screen_40.png)

6. Service related commands
```
Start service：net start MongoDB
Close service：net stop MongoDB
Remove service：D:\developer\env\MongoDB\bin\mongod.exe --remove
```

7. Download the client program：[https://download.robomongo.org/1.2.1/windows/robo3t-1.2.1-windows-x86_64-3e50a65.zip](https://download.robomongo.org/1.2.1/windows/robo3t-1.2.1-windows-x86_64-3e50a65.zip)

7. Unzip to the specified directory，Open robo3t.exe and connect to localhost:27017

![](../images/arch_screen_41.png)

### Spring Data Mongodb

> Similar to Spring Data Elasticsearch, Spring Data Mongodb is a way of operating data storage provided by Spring in the style of Spring Data. It can avoid writing a lot of boilerplate code.

#### Common notes

- @Document:Mark the domain object mapped to the Mongodb document
- @Id:Mark a domain as an ID domain
- @Indexed:Mark a field as the index field of Mongodb

#### Spring-Data data operation

##### Inherit the Mongo Repository interface to get commonly used data manipulation methods

![](../images/arch_screen_42.png)

##### Can use derived queries
> You can query directly by specifying the name of the query method in the interface without implementing it. The following is an example of obtaining browsing records in reverse order according to member id.

```java
/**
 * Member Product Browsing History Repository
 * Created by macro on 2018/8/3.
 */
public interface MemberReadHistoryRepository extends MongoRepository<MemberReadHistory,String> {
    /**
     * Get browsing history in reverse order according to member id
     * @param memberId Member id
     */
    List<MemberReadHistory> findByMemberIdOrderByCreateTimeDesc(Long memberId);
}
```

> The corresponding field will be prompted directly in idea

![](../images/arch_screen_43.png)

##### Use @Query annotation to query with Mongodb's JSON query
```java
@Query("{ 'memberId' : ?0 }")
List<MemberReadHistory> findByMemberId(Long memberId);
```

## Integrate Mongodb to realize document operation

### Add related dependencies in pom.xml
```xml
<!---mongodb related dependencies-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

### Modify the Spring Boot configuration file
> Modify the application.yml file and add Mongodb related configuration under the spring: data node.

```yml
mongodb:
  host: localhost # mongodb connection address
  port: 27017 # mongodb connection port number
  database: mall-port # mongodb connected database
```

### Add Member Browsing Record Document Object Member Read History
> Add @Id annotation to the ID field of the document object, and add @Indexed annotation to the field to be retrieved.

```java
package com.macro.mall.tiny.nosql.mongodb.document;

import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.index.Indexed;
import org.springframework.data.mongodb.core.mapping.Document;

import java.util.Date;

/**
 * User product browsing history
 * Created by macro on 2018/8/3.
 */
@Document
public class MemberReadHistory {
    @Id
    private String id;
    @Indexed
    private Long memberId;
    private String memberNickname;
    private String memberIcon;
    @Indexed
    private Long productId;
    private String productName;
    private String productPic;
    private String productSubTitle;
    private String productPrice;
    private Date createTime;

    //All getter and setter methods are omitted

}

```

### Add Member Read History Repository interface for operating Mongodb
> Inherit the Mongo Repository interface, so that you have some basic Mongodb data manipulation methods, and define a derivative query method.

```java
package com.macro.mall.tiny.nosql.mongodb.repository;


import com.macro.mall.tiny.nosql.mongodb.document.MemberReadHistory;
import org.springframework.data.mongodb.repository.MongoRepository;

import java.util.List;

/**
 * Member Product Browsing History Repository
 * Created by macro on 2018/8/3.
 */
public interface MemberReadHistoryRepository extends MongoRepository<MemberReadHistory,String> {
    /**
     * Get browsing history in reverse order according to member id
     * @param memberId memberId
     */
    List<MemberReadHistory> findByMemberIdOrderByCreateTimeDesc(Long memberId);
}
```

### Add Member Read History Service interface

```java
package com.macro.mall.tiny.service;


import com.macro.mall.tiny.nosql.mongodb.document.MemberReadHistory;

import java.util.List;

/**
 * Member Browsing Record Management Service
 * Created by macro on 2018/8/3.
 */
public interface MemberReadHistoryService {
    /**
     * Generate browsing history
     */
    int create(MemberReadHistory memberReadHistory);

    /**
     * Delete browsing history in bulk
     */
    int delete(List<String> ids);

    /**
     * Get user browsing history
     */
    List<MemberReadHistory> list(Long memberId);
}

```

### Add MemberReadHistoryService interface implementation class MemberReadHistoryServiceImpl

```java
package com.macro.mall.tiny.service.impl;

import com.macro.mall.tiny.nosql.mongodb.document.MemberReadHistory;
import com.macro.mall.tiny.nosql.mongodb.repository.MemberReadHistoryRepository;
import com.macro.mall.tiny.service.MemberReadHistoryService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

/**
 * Member browsing record management service implementation class
 * Created by macro on 2018/8/3.
 */
@Service
public class MemberReadHistoryServiceImpl implements MemberReadHistoryService {
    @Autowired
    private MemberReadHistoryRepository memberReadHistoryRepository;
    @Override
    public int create(MemberReadHistory memberReadHistory) {
        memberReadHistory.setId(null);
        memberReadHistory.setCreateTime(new Date());
        memberReadHistoryRepository.save(memberReadHistory);
        return 1;
    }

    @Override
    public int delete(List<String> ids) {
        List<MemberReadHistory> deleteList = new ArrayList<>();
        for(String id:ids){
            MemberReadHistory memberReadHistory = new MemberReadHistory();
            memberReadHistory.setId(id);
            deleteList.add(memberReadHistory);
        }
        memberReadHistoryRepository.deleteAll(deleteList);
        return ids.size();
    }

    @Override
    public List<MemberReadHistory> list(Long memberId) {
        return memberReadHistoryRepository.findByMemberIdOrderByCreateTimeDesc(memberId);
    }
}
```

### Add Member Read History Controller to define the interface

```java
package com.macro.mall.tiny.controller;

import com.macro.mall.tiny.common.api.CommonResult;
import com.macro.mall.tiny.nosql.mongodb.document.MemberReadHistory;
import com.macro.mall.tiny.service.MemberReadHistoryService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * Member Product Browsing Record Management Controller
 * Created by macro on 2018/8/3.
 */
@Controller
@Api(tags = "MemberReadHistoryController", description = "Member Product browsing record management")
@RequestMapping("/member/readHistory")
public class MemberReadHistoryController {
    @Autowired
    private MemberReadHistoryService memberReadHistoryService;

    @ApiOperation("Create browsing history")
    @RequestMapping(value = "/create", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult create(@RequestBody MemberReadHistory memberReadHistory) {
        int count = memberReadHistoryService.create(memberReadHistory);
        if (count > 0) {
            return CommonResult.success(count);
        } else {
            return CommonResult.failed();
        }
    }

    @ApiOperation("Delete browsing history")
    @RequestMapping(value = "/delete", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult delete(@RequestParam("ids") List<String> ids) {
        int count = memberReadHistoryService.delete(ids);
        if (count > 0) {
            return CommonResult.success(count);
        } else {
            return CommonResult.failed();
        }
    }

    @ApiOperation("Show browsing history")
    @RequestMapping(value = "/list", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<List<MemberReadHistory>> list(Long memberId) {
        List<MemberReadHistory> memberReadHistoryList = memberReadHistoryService.list(memberId);
        return CommonResult.success(memberReadHistoryList);
    }
}

```

## Conduct an interface test

### Add product browsing records to Mongodb

![](../images/arch_screen_44.png)
![](../images/arch_screen_45.png)

### Query product browsing records in Mongodb

![](../images/arch_screen_46.png)
![](../images/arch_screen_47.png)

## Project source address
[https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-07](https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-07)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
