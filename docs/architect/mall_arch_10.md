The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.

# Mall integrates OSS to realize file upload

> This article mainly explains the process of uploading files by integrating OSS with Mall. It adopts the method of front-end direct transmission after server-side signature.

## OSS
> Alibaba Cloud Object Storage Service (OSS) is a massive, safe, low-cost, and highly reliable cloud storage service provided by Alibaba Cloud. OSS can be used to store massive files such as pictures, audio and video, and logs. Various terminal devices, Web site programs, and mobile applications can directly write or read data to OSS.

### Related concepts in OSS

- Endpoint：Access the domain name, through which you can access the APIs of the OSS service to perform operations such as file upload and download.
- Bucket：A storage space is a container for storing objects. All storage objects must belong to a storage space.
- Object：Objects, objects are the basic unit of data stored by OSS, also known as OSS files.
- AccessKey：Access key refers to the AccessKeyId and AccessKeySecret used in access authentication.

### OSS related settings

#### Open OSS service

- Login to Alibaba Cloud official website；
- Move the mouse to the product tab page, click the object storage OSS, open the OSS product details page；
- On the OSS product details page, click Activate Now.

#### Create storage

- Click the console button in the upper right corner of the webpage to enter the console

![](../images/arch_screen_77.png)

- Select object storage OSS in my cloud product

![](../images/arch_screen_78.png)

- Click the plus sign on the left storage space to create a new storage space

![](../images/arch_screen_79.png)

- Create a new storage space and set the read and write permissions to public

![](../images/arch_screen_80.png)


#### Cross-origin resource sharing (CORS) settings

> Because browsers are under security considerations and do not allow cross-domain resource access, we need to set up cross-domain resource sharing for OSS.

- Select a storage space and open its basic settings

![](../images/arch_screen_81.png)

- Click the settings button across settings

![](../images/arch_screen_82.png)

- Click Create Rule

![](../images/arch_screen_83.png)

- Make cross-domain rule settings

![](../images/arch_screen_84.png)

### Relevant instructions for front-end direct transmission after server-side signature

#### Flow chart

![](../images/arch_screen_85.png)

#### Process introduction

1. The web front end requests the application server to obtain the required parameters for uploading (such as OSS access key ID, policy, callback and other parameters)
2. Application server returns relevant parameters
3. The web front end directly initiates a file upload request to the OSS service
4. After the upload is complete, the OSS service will call back the callback interface of the application server
5. The application server returns a response to the OSS service
6. The OSS service returns the content of the application server callback interface to the web front end

## Integrate OSS to realize file upload

### Add related dependencies in pom.xml

```xml
<!-- OSS SDK related dependencies -->
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>2.5.0</version>
</dependency>
```

### Modify the Spring Boot configuration file

> Modify the application.yml file to add OSS related configuration.

Notice：endpoint、accessKeyId、accessKeySecret、bucketName、callback、The prefix must be changed to your own account OSS related，callback needs to be an address that the public network can access.

```yml
# OSS related configuration information
aliyun:
  oss:
    endpoint: oss-cn-shenzhen.aliyuncs.com # oss external service access domain name
    accessKeyId: test # User ID used in access authentication
    accessKeySecret: test # The key used by the user to encrypt the signature string and oss to verify the signature string
    bucketName: macro-oss # oss storage space
    policy:
      expire: 300 # Signature validity period(S)
    maxSize: 10 # Upload file size(M)
    callback: http://localhost:8080/aliyun/oss/callback # Callback address after successful file upload
    dir:
      prefix: mall/images/ # Upload folder path prefix
```

### Add OSS related Java configuration

> Used to configure OSS Client OSS Client.

```java
package com.macro.mall.tiny.config;

import com.aliyun.oss.OSSClient;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * Created by macro on 2018/5/17.
 */
@Configuration
public class OssConfig {
    @Value("${aliyun.oss.endpoint}")
    private String ALIYUN_OSS_ENDPOINT;
    @Value("${aliyun.oss.accessKeyId}")
    private String ALIYUN_OSS_ACCESSKEYID;
    @Value("${aliyun.oss.accessKeySecret}")
    private String ALIYUN_OSS_ACCESSKEYSECRET;
    @Bean
    public OSSClient ossClient(){
        return new OSSClient(ALIYUN_OSS_ENDPOINT,ALIYUN_OSS_ACCESSKEYID,ALIYUN_OSS_ACCESSKEYSECRET);
    }
}
```

### Add OSS upload policy package object Oss Policy Result

> The parameters required when the front end directly uploads files are returned from the back end.

```java
package com.macro.mall.tiny.dto;

import io.swagger.annotations.ApiModelProperty;

/**
 * Obtain the result of OSS upload file authorization return
 * Created by macro on 2018/5/17.
 */
public class OssPolicyResult {
    @ApiModelProperty("User ID used in access authentication")
    private String accessKeyId;
    @ApiModelProperty("User form upload strategy, base 64 encoded string")
    private String policy;
    @ApiModelProperty("String after signing policy")
    private String signature;
    @ApiModelProperty("Upload folder path prefix")
    private String dir;
    @ApiModelProperty("oss external service access domain name")
    private String host;
    @ApiModelProperty("Callback settings after successful upload")
    private String callback;

    //All getter, setter methods are omitted
}

```

### Add the callback parameter object Oss Callback Param after successful OSS upload

> When the OSS upload is successful, the corresponding interface will be called back according to the configuration parameters.

```java
package com.macro.mall.tiny.dto;

import io.swagger.annotations.ApiModelProperty;

/**
 * Callback parameters after successful upload of oss
 * Created by macro on 2018/5/17.
 */
public class OssCallbackParam {
    @ApiModelProperty("Requested callback address")
    private String callbackUrl;
    @ApiModelProperty("The callback is the parameter passed in the request")
    private String callbackBody;
    @ApiModelProperty("The format of the parameters passed in during the callback, such as the form submission form")
    private String callbackBodyType;

    //All getter, setter methods are omitted
}

```

### Oss callback result object after successful OSS upload

> The data object returned in the callback interface encapsulates the information of the uploaded file.

```java
package com.macro.mall.tiny.dto;

import io.swagger.annotations.ApiModelProperty;

/**
 * oss upload file callback result
 * Created by macro on 2018/5/17.
 */
public class OssCallbackResult {
    @ApiModelProperty("File Name")
    private String filename;
    @ApiModelProperty("File size")
    private String size;
    @ApiModelProperty("Mime Type of file")
    private String mimeType;
    @ApiModelProperty("Picture file width")
    private String width;
    @ApiModelProperty("Picture file height")
    private String height;

    //All getter, setter methods are omitted
}

```

### Add OSS service interface Oss Service

```java
package com.macro.mall.tiny.service;

import com.macro.mall.tiny.dto.OssCallbackResult;
import com.macro.mall.tiny.dto.OssPolicyResult;

import javax.servlet.http.HttpServletRequest;

/**
 * oss upload management service
 * Created by macro on 2018/5/17.
 */
public interface OssService {
    /**
     * oss upload strategy generation
     */
    OssPolicyResult policy();

    /**
     * oss upload success callback
     */
    OssCallbackResult callback(HttpServletRequest request);
}

```

### Added OSS service interface OssService implementation class OssServiceImpl

```java
package com.macro.mall.tiny.service.impl;

import cn.hutool.json.JSONUtil;
import com.aliyun.oss.OSSClient;
import com.aliyun.oss.common.utils.BinaryUtil;
import com.aliyun.oss.model.MatchMode;
import com.aliyun.oss.model.PolicyConditions;
import com.macro.mall.tiny.dto.OssCallbackParam;
import com.macro.mall.tiny.dto.OssCallbackResult;
import com.macro.mall.tiny.dto.OssPolicyResult;
import com.macro.mall.tiny.service.OssService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import javax.servlet.http.HttpServletRequest;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * oss upload management service implementation class
 * Created by macro on 2018/5/17.
 */
@Service
public class OssServiceImpl implements OssService {

	private static final Logger LOGGER = LoggerFactory.getLogger(OssServiceImpl.class);
	@Value("${aliyun.oss.policy.expire}")
	private int ALIYUN_OSS_EXPIRE;
	@Value("${aliyun.oss.maxSize}")
	private int ALIYUN_OSS_MAX_SIZE;
	@Value("${aliyun.oss.callback}")
	private String ALIYUN_OSS_CALLBACK;
	@Value("${aliyun.oss.bucketName}")
	private String ALIYUN_OSS_BUCKET_NAME;
	@Value("${aliyun.oss.endpoint}")
	private String ALIYUN_OSS_ENDPOINT;
	@Value("${aliyun.oss.dir.prefix}")
	private String ALIYUN_OSS_DIR_PREFIX;

	@Autowired
	private OSSClient ossClient;

	/**
	 * Signature generation
	 */
	@Override
	public OssPolicyResult policy() {
		OssPolicyResult result = new OssPolicyResult();
		// Storage directory
		SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMdd");
		String dir = ALIYUN_OSS_DIR_PREFIX+sdf.format(new Date());
		// Signature validity period
		long expireEndTime = System.currentTimeMillis() + ALIYUN_OSS_EXPIRE * 1000;
		Date expiration = new Date(expireEndTime);
		// File size
		long maxSize = ALIYUN_OSS_MAX_SIZE * 1024 * 1024;
		// Callback
		OssCallbackParam callback = new OssCallbackParam();
		callback.setCallbackUrl(ALIYUN_OSS_CALLBACK);
		callback.setCallbackBody("filename=${object}&size=${size}&mimeType=${mimeType}&height=${imageInfo.height}&width=${imageInfo.width}");
		callback.setCallbackBodyType("application/x-www-form-urlencoded");
		// Commit node
		String action = "http://" + ALIYUN_OSS_BUCKET_NAME + "." + ALIYUN_OSS_ENDPOINT;
		try {
			PolicyConditions policyConds = new PolicyConditions();
			policyConds.addConditionItem(PolicyConditions.COND_CONTENT_LENGTH_RANGE, 0, maxSize);
			policyConds.addConditionItem(MatchMode.StartWith, PolicyConditions.COND_KEY, dir);
			String postPolicy = ossClient.generatePostPolicy(expiration, policyConds);
			byte[] binaryData = postPolicy.getBytes("utf-8");
			String policy = BinaryUtil.toBase64String(binaryData);
			String signature = ossClient.calculatePostSignature(postPolicy);
			String callbackData = BinaryUtil.toBase64String(JSONUtil.parse(callback).toString().getBytes("utf-8"));
			// Return result
			result.setAccessKeyId(ossClient.getCredentialsProvider().getCredentials().getAccessKeyId());
			result.setPolicy(policy);
			result.setSignature(signature);
			result.setDir(dir);
			result.setCallback(callbackData);
			result.setHost(action);
		} catch (Exception e) {
			LOGGER.error("Signature generation Failed", e);
		}
		return result;
	}

	@Override
	public OssCallbackResult callback(HttpServletRequest request) {
		OssCallbackResult result= new OssCallbackResult();
		String filename = request.getParameter("filename");
		filename = "http://".concat(ALIYUN_OSS_BUCKET_NAME).concat(".").concat(ALIYUN_OSS_ENDPOINT).concat("/").concat(filename);
		result.setFilename(filename);
		result.setSize(request.getParameter("size"));
		result.setMimeType(request.getParameter("mimeType"));
		result.setWidth(request.getParameter("width"));
		result.setHeight(request.getParameter("height"));
		return result;
	}

}

```

### Add Oss Controller to define interface

```java
package com.macro.mall.tiny.controller;


import com.macro.mall.tiny.common.api.CommonResult;
import com.macro.mall.tiny.dto.OssCallbackResult;
import com.macro.mall.tiny.dto.OssPolicyResult;
import com.macro.mall.tiny.service.impl.OssServiceImpl;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;

/**
 * Oss related operation interface
 * Created by macro on 2018/4/26.
 */
@Controller
@Api(tags = "OssController", description = "Oss management")
@RequestMapping("/aliyun/oss")
public class OssController {
    @Autowired
    private OssServiceImpl ossService;

    @ApiOperation(value = "oss upload signature generation")
    @RequestMapping(value = "/policy", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<OssPolicyResult> policy() {
        OssPolicyResult result = ossService.policy();
        return CommonResult.success(result);
    }

    @ApiOperation(value = "oss upload success callback")
    @RequestMapping(value = "callback", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult<OssCallbackResult> callback(HttpServletRequest request) {
        OssCallbackResult ossCallbackResult = ossService.callback(request);
        return CommonResult.success(ossCallbackResult);
    }

}

```

## Conduct an interface test

### Test the interface to get upload strategy

![](../images/arch_screen_66.png)

![](../images/arch_screen_67.png)

![](../images/arch_screen_68.png)

### Start the mall-admin-web front-end project to test the upload interface

- How to start the front-end project, please refer to the readme document of the project：[https://github.com/macrozheng/mall-admin-web](https://github.com/macrozheng/mall-admin-web)

- Click the upload button to add the product brand to test

![](../images/arch_screen_69.png)

- Will call two requests, the first time to access the local interface to get the upload strategy

![](../images/arch_screen_70.png)

![](../images/arch_screen_71.png)

- The second call to the oss service interface for file upload

![](../images/arch_screen_72.png)

![](../images/arch_screen_73.png)

- It can be seen that the above interface call does not pass in the callback parameter callback, so the interface returns 204 no content. This time we try to pass in the callback parameter callback. We can find that the oss service has called back the callback interface we defined and returned the corresponding result.

![](../images/arch_screen_74.png)

![](../images/arch_screen_75.png)

![](../images/arch_screen_76.png)

## Project source address

[https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-09](https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-09)

## Reference material

- Open OSS service：[https://help.aliyun.com/document_detail/31884.html?spm=a2c4g.11186623.6.566.74b87eaebrfQno](https://help.aliyun.com/document_detail/31884.html?spm=a2c4g.11186623.6.566.74b87eaebrfQno)

- Create storage：[https://help.aliyun.com/document_detail/31885.html?spm=a2c4g.11186623.6.567.496228bcVZUZqB](https://help.aliyun.com/document_detail/31885.html?spm=a2c4g.11186623.6.567.496228bcVZUZqB)

- Cross-domain resource sharing（CORS）:[https://help.aliyun.com/document_detail/31928.html?spm=5176.11065259.1996646101.searchclickresult.4d1a5607Pf3e9i](https://help.aliyun.com/document_detail/31928.html?spm=5176.11065259.1996646101.searchclickresult.4d1a5607Pf3e9i)

- Server side signature direct transfer and set upload callback:[https://help.aliyun.com/document_detail/31927.html?spm=a2c4g.11186623.6.1268.2c256506mNqV1t](https://help.aliyun.com/document_detail/31927.html?spm=a2c4g.11186623.6.1268.2c256506mNqV1t)

## No public

![Public account picture](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/banner/qrcode_for_macrozheng_258.jpg)
