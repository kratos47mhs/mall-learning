The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.

# Mall integrates Elasticsearch to realize product search

> This article mainly explains the process of mall integration with Elasticsearch, taking the import, query, modification, and deletion of commodity information in Elasticsearch as examples.

## Introduction to the project framework

### Elasticsearch
> Elasticsearch is a distributed, scalable, real-time search and data analysis engine. It can give your data the ability to search, analyze and explore from the beginning of the project, and can be used to achieve full-text search and real-time data statistics.
#### Elasticsearch的安装和使用

1. Download the zip package of Elasticsearch 6.2.2 and unzip it to the specified directory, download address：[https://www.elastic.co/cn/downloads/past-releases/elasticsearch-6-2-2](https://www.elastic.co/cn/downloads/past-releases/elasticsearch-6-2-2)

![](../images/arch_screen_25.png)

2. Install the Chinese word segmentation plugin, execute the following command in the elasticsearch-6.2.2\bin directory：elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.2.2/elasticsearch-analysis-ik-6.2.2.zip

![](../images/arch_screen_26.png)

3. Run elasticsearch.bat in the bin directory to start Elasticsearch

![](../images/arch_screen_27.png)

4. Download Kibana, as a client to access Elasticsearch, please download the 6.2.2 version of the zip package, and unzip it to the specified directory, download address：[https://artifacts.elastic.co/downloads/kibana/kibana-6.2.2-windows-x86_64.zip](https://artifacts.elastic.co/downloads/kibana/kibana-6.2.2-windows-x86_64.zip)

![](../images/arch_screen_28.png)

5. Run kibana.bat in the bin directory to start the Kibana user interface

![](../images/arch_screen_29.png)

6. Visit [http://localhost:5601](http://localhost:5601) To open the Kibana user interface

![](../images/arch_screen_30.png)

### Spring Data Elasticsearch
> Spring Data Elasticsearch is a way of operating data storage provided by Spring in the style of Spring Data. It can avoid writing a lot of boilerplate code.

#### Common notes

##### @Document
```java
//Mark the domain object mapped to the Elasticsearch document
public @interface Document {
  //Index library ranking, the concept of database in mysql
	String indexName();
  //Document type, concept of table in mysql
	String type() default "";
  //The default number of shards
	short shards() default 5;
  //Number of default copies
	short replicas() default 1;

}
```
##### @Id
```java
//Represents the id of the document, the document can be regarded as the concept of table rows in mysql
public @interface Id {
}
```

##### @Field
```java
public @interface Field {
  //Types of fields in the document
	FieldType type() default FieldType.Auto;
  //Whether to create an inverted index
	boolean index() default true;
  //Whether to store
	boolean store() default false;
  //Word breaker rank
	String analyzer() default "";
}
```

```java
//Automatically assign metadata types to documents
public enum FieldType {
	Text,//Character types that will be segmented and indexed
	Integer,
	Long,
	Date,
	Float,
	Double,
	Boolean,
	Object,
	Auto,//Automatically determine the field type
	Nested,//Nested object type
	Ip,
	Attachment,
	Keyword//Types that will not be indexed
}
```

#### Sping Data data operation

##### Inherit the Elasticsearch Repository interface to get commonly used data manipulation methods
![](../images/arch_screen_31.png)

##### Can use derived queries
>You can query directly by specifying the name of the query method in the interface without implementing it. For example, if there is a product name, title and keywords in the product table, you can directly define the following query to perform a full-text search on these three fields.

```java
    /**
     * Search query
     *
     * @param name              Product Name
     * @param subTitle          Product Title
     * @param keywords          Product Keywords
     * @param page              Paging Information
     * @return
     */
    Page<EsProduct> findByNameOrSubTitleOrKeywords(String name, String subTitle, String keywords, Pageable page);
```
> The corresponding field will be prompted directly in idea

![](../images/arch_screen_32.png)

##### Use @Query annotation to query with Elasticsearch's DSL statement
```java
@Query("{"bool" : {"must" : {"field" : {"name" : "?0"}}}}")
Page<EsProduct> findByName(String name,Pageable pageable);
```

## Item usage table description

- `pms_product`：Product Information Table
- `pms_product_attribute`：Product attribute parameter table
- `pms_product_attribute_value`：Table for storing product parameter values

## Integrate Elasticsearch to realize product search

### Add related dependencies in pom.xml
```xml
<!--Elasticsearch related dependencies-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch<artifactId>
</dependency>
```

### Modify the Spring Boot configuration file
> Modify the application.yml file and add Elasticsearch related configuration under the spring node.

```yml
data:
  elasticsearch:
    repositories:
      enabled: true
    cluster-nodes: 127.0.0.1:9300 # es connection address and port number
    cluster-name: elasticsearch # es cluster name 
```

### Add product document object Es Product
> Fields that do not require Chinese word segmentation are set to the @Field (type = FieldType.Keyword) type, and those that require Chinese word segmentation are set to the @Field (analyzer = "ik_max_word", type = FieldType.Text) type.

```java
package com.macro.mall.tiny.nosql.elasticsearch.document;

import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.Field;
import org.springframework.data.elasticsearch.annotations.FieldType;

import java.io.Serializable;
import java.math.BigDecimal;
import java.util.List;

/**
 * Product information in search
 * Created by macro on 2018/6/19.
 */
@Document(indexName = "pms", type = "product",shards = 1,replicas = 0)
public class EsProduct implements Serializable {
    private static final long serialVersionUID = -1L;
    @Id
    private Long id;
    @Field(type = FieldType.Keyword)
    private String productSn;
    private Long brandId;
    @Field(type = FieldType.Keyword)
    private String brandName;
    private Long productCategoryId;
    @Field(type = FieldType.Keyword)
    private String productCategoryName;
    private String pic;
    @Field(analyzer = "ik_max_word",type = FieldType.Text)
    private String name;
    @Field(analyzer = "ik_max_word",type = FieldType.Text)
    private String subTitle;
    @Field(analyzer = "ik_max_word",type = FieldType.Text)
    private String keywords;
    private BigDecimal price;
    private Integer sale;
    private Integer newStatus;
    private Integer recommandStatus;
    private Integer stock;
    private Integer promotionType;
    private Integer sort;
    @Field(type =FieldType.Nested)
    private List<EsProductAttributeValue> attrValueList;

    //All getter and setter methods are omitted
}

```

### Add Es Product Repository interface to operate Elasticsearch
> Inherit the Elasticsearch Repository interface, so that you have some basic Elasticsearch data manipulation methods and define a derivative query method.

```java
package com.macro.mall.tiny.nosql.elasticsearch.repository;

import com.macro.mall.tiny.nosql.elasticsearch.document.EsProduct;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.elasticsearch.repository.ElasticsearchRepository;

/**
 * Product ES operation
 * Created by macro on 2018/6/19.
 */
public interface EsProductRepository extends ElasticsearchRepository<EsProduct, Long> {
    /**
     * Search query
     *
     * @param name              Product Name
     * @param subTitle          Product Title
     * @param keywords          Product Keywords
     * @param page              Paging Information
     * @return
     */
    Page<EsProduct> findByNameOrSubTitleOrKeywords(String name, String subTitle, String keywords, Pageable page);

}

```

### Add EsProductService interface

```java
package com.macro.mall.tiny.service;

import com.macro.mall.tiny.nosql.elasticsearch.document.EsProduct;
import org.springframework.data.domain.Page;

import java.util.List;

/**
 * Product Search Management Service
 * Created by macro on 2018/6/19.
 */
public interface EsProductService {
    /**
     * Import all products from the database to ES
     */
    int importAll();

    /**
     * Delete product based on id
     */
    void delete(Long id);

    /**
     * Create product based on id
     */
    EsProduct create(Long id);

    /**
     * Delete products in bulk
     */
    void delete(List<Long> ids);

    /**
     * Search for names or subtitles based on keywords
     */
    Page<EsProduct> search(String keyword, Integer pageNum, Integer pageSize);

}

```

### Add EsProductService interface implementation class EsProductServiceImpl

```java
package com.macro.mall.tiny.service.impl;

import com.macro.mall.tiny.dao.EsProductDao;
import com.macro.mall.tiny.nosql.elasticsearch.document.EsProduct;
import com.macro.mall.tiny.nosql.elasticsearch.repository.EsProductRepository;
import com.macro.mall.tiny.service.EsProductService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;
import org.springframework.util.CollectionUtils;

import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;


/**
 * Product search management service implementation class
 * Created by macro on 2018/6/19.
 */
@Service
public class EsProductServiceImpl implements EsProductService {
    private static final Logger LOGGER = LoggerFactory.getLogger(EsProductServiceImpl.class);
    @Autowired
    private EsProductDao productDao;
    @Autowired
    private EsProductRepository productRepository;

    @Override
    public int importAll() {
        List<EsProduct> esProductList = productDao.getAllEsProductList(null);
        Iterable<EsProduct> esProductIterable = productRepository.saveAll(esProductList);
        Iterator<EsProduct> iterator = esProductIterable.iterator();
        int result = 0;
        while (iterator.hasNext()) {
            result++;
            iterator.next();
        }
        return result;
    }

    @Override
    public void delete(Long id) {
        productRepository.deleteById(id);
    }

    @Override
    public EsProduct create(Long id) {
        EsProduct result = null;
        List<EsProduct> esProductList = productDao.getAllEsProductList(id);
        if (esProductList.size() > 0) {
            EsProduct esProduct = esProductList.get(0);
            result = productRepository.save(esProduct);
        }
        return result;
    }

    @Override
    public void delete(List<Long> ids) {
        if (!CollectionUtils.isEmpty(ids)) {
            List<EsProduct> esProductList = new ArrayList<>();
            for (Long id : ids) {
                EsProduct esProduct = new EsProduct();
                esProduct.setId(id);
                esProductList.add(esProduct);
            }
            productRepository.deleteAll(esProductList);
        }
    }

    @Override
    public Page<EsProduct> search(String keyword, Integer pageNum, Integer pageSize) {
        Pageable pageable = PageRequest.of(pageNum, pageSize);
        return productRepository.findByNameOrSubTitleOrKeywords(keyword, keyword, keyword, pageable);
    }

}

```

### Add EsProductController to define the interface
```java
package com.macro.mall.tiny.controller;

import com.macro.mall.tiny.common.api.CommonPage;
import com.macro.mall.tiny.common.api.CommonResult;
import com.macro.mall.tiny.nosql.elasticsearch.document.EsProduct;
import com.macro.mall.tiny.service.EsProductService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * Search Product Management Controller
 * Created by macro on 2018/6/19.
 */
@Controller
@Api(tags = "EsProductController", description = "Search Product Management")
@RequestMapping("/esProduct")
public class EsProductController {
    @Autowired
    private EsProductService esProductService;

    @ApiOperation(value = "Import all the products in the database to ES")
    @RequestMapping(value = "/importAll", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult<Integer> importAllList() {
        int count = esProductService.importAll();
        return CommonResult.success(count);
    }

    @ApiOperation(value = "Delete product based on id")
    @RequestMapping(value = "/delete/{id}", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<Object> delete(@PathVariable Long id) {
        esProductService.delete(id);
        return CommonResult.success(null);
    }

    @ApiOperation(value = "Delete products in bulk based on id")
    @RequestMapping(value = "/delete/batch", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult<Object> delete(@RequestParam("ids") List<Long> ids) {
        esProductService.delete(ids);
        return CommonResult.success(null);
    }

    @ApiOperation(value = "Create product based on id")
    @RequestMapping(value = "/create/{id}", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult<EsProduct> create(@PathVariable Long id) {
        EsProduct esProduct = esProductService.create(id);
        if (esProduct != null) {
            return CommonResult.success(esProduct);
        } else {
            return CommonResult.failed();
        }
    }

    @ApiOperation(value = "Simple search")
    @RequestMapping(value = "/search/simple", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<CommonPage<EsProduct>> search(@RequestParam(required = false) String keyword,
                                                      @RequestParam(required = false, defaultValue = "0") Integer pageNum,
                                                      @RequestParam(required = false, defaultValue = "5") Integer pageSize) {
        Page<EsProduct> esProductPage = esProductService.search(keyword, pageNum, pageSize);
        return CommonResult.success(CommonPage.restPage(esProductPage));
    }

}

```

## Conduct an interface test

### Import data from the database to Elasticsearch

![](../images/arch_screen_33.png)
![](../images/arch_screen_34.png)

### Perform product search

![](../images/arch_screen_35.png)
![](../images/arch_screen_36.png)

## Project source address
[https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-06](https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-06)

## No public

![Public account picture](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/banner/qrcode_for_macrozheng_258.jpg)
