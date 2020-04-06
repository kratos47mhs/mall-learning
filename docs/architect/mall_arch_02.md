In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account] (# Public number) will be available immediately.

# Mall integrates Swagger-UI to achieve online API documentation

> This article mainly explains how mall achieves a fairly complete online API document by integrating Swagger-UI.

## Introduction to the project framework

### Swagger-UI
> Swagger-UI is a collection of HTML, Javascript, CSS, which can dynamically generate online API documents based on annotations.

#### Common notes

- @Api：Used to modify the Controller class and generate Controller-related document information
- @ApiOperation：Used to modify methods in the Controller class to generate document information related to interface methods
- @ApiParam：It is used to modify the parameters in the interface and generate relevant document information of the interface parameters
- @ApiModelProperty：Used to modify the attributes of the entity class. When the entity class is a request parameter or returns a result, directly generate relevant document information

## Consistent Swagger-UI

### Add project dependencies
> Added Swagger-UI related dependency in pom.xml

```xml
<!--Swagger-UI API document production tool-->
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger2</artifactId>
  <version>2.7.0</version>
</dependency>
<dependency>
  <groupId>io.springfox</groupId>
  <artifactId>springfox-swagger-ui</artifactId>
  <version>2.7.0</version>
</dependency>
```
### Add Swagger-UI configuration

> Add the Java configuration file of Swagger-UI

Note: Swagger has three different options for the scope of generating API documentation
- Generate API documentation for the class below the specified package
- Generate API documentation for the specified annotated class
- Generate API document with specified annotation method


```java
package com.macro.mall.tiny.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

/**
 * Swagger 2 API documentation configuration
 */
@Configuration
@EnableSwagger2
public class Swagger2Config {
    @Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //Generate API documentation for the controller under the current package
                .apis(RequestHandlerSelectors.basePackage("com.macro.mall.tiny.controller"))
                //Generate API documentation for Controller annotated with @Api
//                .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))
                //Generate API documentation for methods annotated with @Api Operation
//                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Swagger UI demo")
                .description("mall-tiny")
                .contact("macro")
                .version("1.0")
                .build();
    }
}

```
### Add Swagger annotation to Pms Brand Controller

> Add Swagger annotation to the original brand management controller

```java
package com.macro.mall.tiny.controller;

import com.macro.mall.tiny.common.api.CommonPage;
import com.macro.mall.tiny.common.api.CommonResult;
import com.macro.mall.tiny.mbg.model.PmsBrand;
import com.macro.mall.tiny.service.PmsBrandService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import io.swagger.annotations.ApiParam;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.*;

import java.util.List;


/**
 * Brand Management Controller
 * Created by macro on 2019/4/19.
 */
@Api(tags = "PmsBrandController", description = "Commodity brand management")
@Controller
@RequestMapping("/brand")
public class PmsBrandController {
    @Autowired
    private PmsBrandService brandService;

    private static final Logger LOGGER = LoggerFactory.getLogger(PmsBrandController.class);

    @ApiOperation("Get a list of all brands")
    @RequestMapping(value = "listAll", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<List<PmsBrand>> getBrandList() {
        return CommonResult.success(brandService.listAllBrand());
    }

    @ApiOperation("Add brand")
    @RequestMapping(value = "/create", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult createBrand(@RequestBody PmsBrand pmsBrand) {
        CommonResult commonResult;
        int count = brandService.createBrand(pmsBrand);
        if (count == 1) {
            commonResult = CommonResult.success(pmsBrand);
            LOGGER.debug("createBrand success:{}", pmsBrand);
        } else {
            commonResult = CommonResult.failed("Operation Failed");
            LOGGER.debug("createBrand failed:{}", pmsBrand);
        }
        return commonResult;
    }

    @ApiOperation("Update specified id brand information")
    @RequestMapping(value = "/update/{id}", method = RequestMethod.POST)
    @ResponseBody
    public CommonResult updateBrand(@PathVariable("id") Long id, @RequestBody PmsBrand pmsBrandDto, BindingResult result) {
        CommonResult commonResult;
        int count = brandService.updateBrand(id, pmsBrandDto);
        if (count == 1) {
            commonResult = CommonResult.success(pmsBrandDto);
            LOGGER.debug("updateBrand success:{}", pmsBrandDto);
        } else {
            commonResult = CommonResult.failed("Operation Failed");
            LOGGER.debug("updateBrand failed:{}", pmsBrandDto);
        }
        return commonResult;
    }

    @ApiOperation("Delete the brand with the specified id")
    @RequestMapping(value = "/delete/{id}", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult deleteBrand(@PathVariable("id") Long id) {
        int count = brandService.deleteBrand(id);
        if (count == 1) {
            LOGGER.debug("deleteBrand success :id={}", id);
            return CommonResult.success(null);
        } else {
            LOGGER.debug("deleteBrand failed :id={}", id);
            return CommonResult.failed("Operation Failed");
        }
    }

    @ApiOperation("Query brand list by page")
    @RequestMapping(value = "/list", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<CommonPage<PmsBrand>> listBrand(@RequestParam(value = "pageNum", defaultValue = "1")
                                                        @ApiParam("page number") Integer pageNum,
                                                        @RequestParam(value = "pageSize", defaultValue = "3")
                                                        @ApiParam("Quantity per page") Integer pageSize) {
        List<PmsBrand> brandList = brandService.listBrand(pageNum, pageSize);
        return CommonResult.success(CommonPage.restPage(brandList));
    }

    @ApiOperation("Get the brand details of the specified id")
    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    @ResponseBody
    public CommonResult<PmsBrand> brand(@PathVariable("id") Long id) {
        return CommonResult.success(brandService.getBrand(id));
    }
}

```

### Modify the MyBatis Generator annotation generation rules

> CommentGenerator is a custom comment generator for MyBatis Generator, modify the addFieldComment method to generate Swagger's @ApiModelProperty annotation to replace the original method annotation, add the addJavaFileComment method, so that it can import @ApiModelProperty in import, otherwise you need to manually import the class, When you need to generate a large number of entity classes, it is a very troublesome thing.
```java
package com.macro.mall.tiny.mbg;

import org.mybatis.generator.api.IntrospectedColumn;
import org.mybatis.generator.api.IntrospectedTable;
import org.mybatis.generator.api.dom.java.CompilationUnit;
import org.mybatis.generator.api.dom.java.Field;
import org.mybatis.generator.api.dom.java.FullyQualifiedJavaType;
import org.mybatis.generator.internal.DefaultCommentGenerator;
import org.mybatis.generator.internal.util.StringUtility;

import java.util.Properties;

/**
 * Custom annotation generator
 * Created by macro on 2018/4/26.
 */
public class CommentGenerator extends DefaultCommentGenerator {
    private boolean addRemarkComments = false;
    private static final String EXAMPLE_SUFFIX="Example";
    private static final String API_MODEL_PROPERTY_FULL_CLASS_NAME="io.swagger.annotations.ApiModelProperty";

    /**
     * Set user-configured parameters
     */
    @Override
    public void addConfigurationProperties(Properties properties) {
        super.addConfigurationProperties(properties);
        this.addRemarkComments = StringUtility.isTrue(properties.getProperty("addRemarkComments"));
    }

    /**
     * Add a comment to the field
     */
    @Override
    public void addFieldComment(Field field, IntrospectedTable introspectedTable,
                                IntrospectedColumn introspectedColumn) {
        String remarks = introspectedColumn.getRemarks();
        //Determine whether to add remarks based on parameters and remarks
        if(addRemarkComments&&StringUtility.stringHasValue(remarks)){
//            addFieldJavaDoc(field, remarks);
            //Special characters in the database need to be escaped
            if(remarks.contains("\"")){
                remarks = remarks.replace("\"","'");
            }
            //Add swagger annotations to model fields
            field.addJavaDocLine("@ApiModelProperty(value = \""+remarks+"\")");
        }
    }

    /**
     * Add comments to model fields
     */
    private void addFieldJavaDoc(Field field, String remarks) {
        //Document comment start
        field.addJavaDocLine("/**");
        //Get remark information of database field
        String[] remarkLines = remarks.split(System.getProperty("line.separator"));
        for(String remarkLine:remarkLines){
            field.addJavaDocLine(" * "+remarkLine);
        }
        addJavadocTag(field, false);
        field.addJavaDocLine(" */");
    }

    @Override
    public void addJavaFileComment(CompilationUnit compilationUnit) {
        super.addJavaFileComment(compilationUnit);
        //Only add the import of swagger annotation class in the model
        if(!compilationUnit.isJavaInterface()&&!compilationUnit.getType().getFullyQualifiedName().contains(EXAMPLE_SUFFIX)){
            compilationUnit.addImportedType(new FullyQualifiedJavaType(API_MODEL_PROPERTY_FULL_CLASS_NAME));
        }
    }
}

```
### Run the code generator to regenerate the code in the mbg package

> Run the main method of com.macro.mall.tiny.mbg.Generator to regenerate the code in mbg, you can see that the @ApiModelProperty annotation has been automatically added in the PmsBrand class according to the database annotation
![](../images/arch_screen_03.png)

### Run the project and view the results

#### Access Swagger-UI interface document address
interface address：http://localhost:8080/swagger-ui.html

![](../images/arch_screen_04.png)

#### Instructions have been added to the request parameters

![](../images/arch_screen_05.png)

#### Added description to the returned result

![](../images/arch_screen_06.png)

### Interface testing directly on the online documentation

![](../images/arch_screen_07.png)

![](../images/arch_screen_08.png)

## Project source address
[https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-02](https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-02)

## No public

![Public account picture](http://macro-oss.oss-cn-shenzhen.aliyuncs.com/mall/banner/qrcode_for_macrozheng_258.jpg)
