In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account] (# 公 号) will be available immediately.

# Mall integrates Redis to achieve cache function

> This article mainly explains the process of Mall integration with Redis, taking the storage verification of SMS verification code as an example.

## Redis installation and startup

> Redis is a high-performance key-value database developed in C language, which can be used for data caching and is mainly used to handle high-access loads of large amounts of data.


- Download Redis, download address: https://github.com/MicrosoftArchive/redis/releases

! [] (../ images / arch_screen_09.png)

- After downloading, unzip to the specified directory

! [] (../ images / arch_screen_10.png)

-After entering cmd in the current address bar, execute the redis start command: redis-server.exe redis.windows.conf

! [] (../ images / arch_screen_11.png)

## Integrate Redis

### Add project dependencies
> Added Redis related dependencies in pom.xml

`` `xml
<!-redis dependency configuration->
<dependency>
  <groupId> org.springframework.boot </ groupId>
  <artifactId> spring-boot-starter-data-redis </ artifactId>
</ dependency>
`` `
### Modify the Spring Boot configuration file

> Add Redis configuration and Redis custom key configuration in application.yml.
#### Add Redis configuration under spring node

`` `yml
  redis:
    host: localhost # Redis server address
    database: 0 # Redis database index (default is 0)
    port: 6379 # Redis server connection port
    password: # Redis server connection password (default is blank)
    jedis:
      pool:
        max-active: 8 # The maximum number of connections in the connection pool (use a negative value to indicate no limit)
        max-wait: -1ms # connection pool maximum blocking wait time (use a negative value to indicate no limit)
        max-idle: 8 # The largest idle connection in the connection pool
        min-idle: 0 # The smallest idle connection in the connection pool
    timeout: 3000ms # connection timeout time (ms)
`` `

#### Add Redis custom key configuration under the root node

`` `yml
# Custom redis key
redis:
  key:
    prefix:
      authCode: "portal: authCode:"
    expire:
      authCode: 120 # Verification code expiration time
`` `

### Add RedisService interface to define some common Redis operations

`` `java
package com.macro.mall.tiny.service;

/ **
 * Redis operation Service,
 * Objects and arrays are stored in json format
 * Created by macro on 2018/8/7.
 * /
public interface RedisService {
    / **
     * Storing data
     * /
    void set (String key, String value);

    / **
     * retrieve data
     * /
    String get (String key);

    / **
     * Set overdue time
     * /
    boolean expire (String key, long expire);

    / **
     * delete data
     * /
    void remove (String key);

    / **
     * Self-increasing operation
     * @param delta increment step
     * /
    Long increment (String key, long delta);

}

`` `

### Inject StringRedisTemplate to implement RedisService interface

`` `java
package com.macro.mall.tiny.service.impl;

import com.macro.mall.tiny.service.RedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

/ **
 * Redis operation Service implementation class
 * Created by macro on 2018/8/7.
 * /
@Service
public class RedisServiceImpl implements RedisService {
    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void set (String key, String value) {
        stringRedisTemplate.opsForValue (). set (key, value);
    }

    @Override
    public String get (String key) {
        return stringRedisTemplate.opsForValue (). get (key);
    }

    @Override
    public boolean expire (String key, long expire) {
        return stringRedisTemplate.expire (key, expire, TimeUnit.SECONDS);
    }

    @Override
    public void remove (String key) {
        stringRedisTemplate.delete (key);
    }

    @Override
    public Long increment (String key, long delta) {
        return stringRedisTemplate.opsForValue (). increment (key, delta);
    }
}

`` `

### Add UmsMemberController
> Add an interface to obtain the verification code based on the phone number and an interface to verify the verification code

`` `java
package com.macro.mall.tiny.controller;

import com.macro.mall.tiny.common.api.CommonResult;
import com.macro.mall.tiny.service.UmsMemberService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

/ **
 * Member login registration management Controller
 * Created by macro on 2018/8/3.
 * /
@Controller
@Api (tags = "UmsMemberController", description = "Member login registration management")
@RequestMapping ("/ sso")
public class UmsMemberController {
    @Autowired
    private UmsMemberService memberService;

    @ApiOperation ("Get Verification Code")
    @RequestMapping (value = "/ getAuthCode", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult getAuthCode (@RequestParam String telephone) {
        return memberService.generateAuthCode (telephone);
    }

    @ApiOperation ("Determine whether the verification code is correct")
    @RequestMapping (value = "/ verifyAuthCode", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult updatePassword (@RequestParam String telephone,
                                 @RequestParam String authCode) {
        return memberService.verifyAuthCode (telephone, authCode);
    }
}

`` `

### Add UmsMemberService interface

`` `java
package com.macro.mall.tiny.service;

import com.macro.mall.tiny.common.api.CommonResult;

/ **
 * Member Management Service
 * Created by macro on 2018/8/3.
 * /
public interface UmsMemberService {

    / **
     * Generate verification code
     * /
    CommonResult generateAuthCode (String telephone);

    / **
     * Determine whether the verification code and mobile phone number match
     * /
    CommonResult verifyAuthCode (String telephone, String authCode);

}

`` `

### Add UmsMemberService interface implementation class UmsMemberServiceImpl

> When generating a verification code, add a custom Redis key value to the mobile phone number to generate a Redis key, store the verification code as the value in Redis, and set the expiration time to the time you configured (120s here) When verifying the verification code, the verification code stored in Redis is obtained according to the mobile phone number and compared with the incoming verification code.

`` `java
package com.macro.mall.tiny.service.impl;

import com.macro.mall.tiny.common.api.CommonResult;
import com.macro.mall.tiny.service.RedisService;
import com.macro.mall.tiny.service.UmsMemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import org.springframework.util.StringUtils;

import java.util.Random;

/ **
 * Member Management Service implementation class
 * Created by macro on 2018/8/3.
 * /
@Service
public class UmsMemberServiceImpl implements UmsMemberService {
    @Autowired
    private RedisService redisService;
    @Value ("$ {redis.key.prefix.authCode}")
    private String REDIS_KEY_PREFIX_AUTH_CODE;
    @Value ("$ {redis.key.expire.authCode}")
    private Long AUTH_CODE_EXPIRE_SECONDS;

    @Override
    public CommonResult generateAuthCode (String telephone) {
        StringBuilder sb = new StringBuilder ();
        Random random = new Random ();
        for (int i = 0; i <6; i ++) {
            sb.append (random.nextInt (10));
        }
        // The verification code is bound to the mobile phone number and stored in redis
        redisService.set (REDIS_KEY_PREFIX_AUTH_CODE + telephone, sb.toString ());
        redisService.expire (REDIS_KEY_PREFIX_AUTH_CODE + telephone, AUTH_CODE_EXPIRE_SECONDS);
        return CommonResult.success (sb.toString (), "Successfully obtained verification code");
    }


    // Verify the entered verification code
    @Override
    public CommonResult verifyAuthCode (String telephone, String authCode) {
        if (StringUtils.isEmpty (authCode)) {
            return CommonResult.failed ("Please enter the verification code");
        }
        String realAuthCode = redisService.get (REDIS_KEY_PREFIX_AUTH_CODE + telephone);
        boolean result = authCode.equals (realAuthCode);
        if (result) {
            return CommonResult.success (null, "Verification code verification succeeded");
        } else {
            return CommonResult.failed ("Verification code is incorrect");
        }
    }

}

`` `
### Run the project
> Visit Swagger's API document address http: // localhost: 8080 / swagger-ui.html to test the interface.

! [] (../ images / arch_screen_12.png)

## Project source address
[https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-03](https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-03)

## No public

! [Public Account Picture] (http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/banner/qrcode_for_macrozheng_258.jpg)