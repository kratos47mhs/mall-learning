In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) get it immediately.

# The commonly used tool classes and methods in Hutool

> Hutool is a Java toolkit that helps us simplify every line of code and avoid reinventing the wheel. If you need to use certain tools and methods, you may wish to find them in Hutool, there may be. This article will introduce common tools and methods in Hutool.

## Installation

The maven project can add the following dependencies in pom.xml:

```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>4.6.3</version>
</dependency>
```

## Common tools

### Convert
Type conversion tool class, used to convert various types of data.

```java 
//Convert to string
int a = 1;
String aStr = Convert.toStr(a);
//Convert to an array of the specified type
String[] b = {"1", "2", "3", "4"};
Integer[] bArr = Convert.toIntArray(b);
//Convert to date object
String dateStr = "2017-05-06";
Date date = Convert.toDate(dateStr);
//Convert to list
String[] strArr = {"a", "b", "c", "d"};
List<String> strList = Convert.toList(String.class, strArr);
```

### DateUtil
Date and time tool class defines some commonly used date and time operation methods.

```java 
//Conversion between Date, Long, and Calendar
//current time
Date date = DateUtil.date();
//Calendar turn to Date
date = DateUtil.date(Calendar.getInstance());
//Timestamp to Date
date = DateUtil.date(System.currentTimeMillis());
//Automatic recognition format conversion
String dateStr = "2017-03-01";
date = DateUtil.parse(dateStr);
//Custom format conversion
date = DateUtil.parse(dateStr, "yyyy-MM-dd");
//Format the output date
String format = DateUtil.format(date, "yyyy-MM-dd");
//Get the part of the year
int year = DateUtil.year(date);
//Get month, count from 0
int month = DateUtil.month(date);
//Get the start and end time of a day
Date beginOfDay = DateUtil.beginOfDay(date);
Date endOfDay = DateUtil.endOfDay(date);
//Calculate the offset date and time
Date newDate = DateUtil.offset(date, DateField.DAY_OF_MONTH, 2);
//Calculate the offset between date and time
long betweenDay = DateUtil.between(date, newDate, DateUnit.DAY);
```

### StrUtil
The string tool class defines some commonly used string manipulation methods.

```java 
//Determine if it is an empty string
String str = "test";
StrUtil.isEmpty(str);
StrUtil.isNotEmpty(str);
//Remove the prefix and suffix of the string
StrUtil.removeSuffix("a.jpg", ".jpg");
StrUtil.removePrefix("a.jpg", "a.");
//Format string
String template = "This is just a placeholder:{}";
String str2 = StrUtil.format(template, "I am a placeholder");
LOGGER.info("/strUtil format:{}", str2);
```

### ClassPathResource

Obtain the files under the class path. Under Tomcat and other containers, the class path is generally WEB-INF/classes.

```java 
//Get the configuration file defined in the src/main/resources folder
ClassPathResource resource = new ClassPathResource("generator.properties");
Properties properties = new Properties();
properties.load(resource.getStream());
LOGGER.info("/classPath:{}", properties);
```

### ReflectUtil
The Java reflection tool class can be used to reflect the methods of obtaining classes and creating objects.

```java 
//Get all methods of a class
Method[] methods = ReflectUtil.getMethods(PmsBrand.class);
//Get the specified method of a class
Method method = ReflectUtil.getMethod(PmsBrand.class, "getId");
//Use reflection to create objects
PmsBrand pmsBrand = ReflectUtil.newInstance(PmsBrand.class);
//Method of reflecting execution object
ReflectUtil.invoke(pmsBrand, "setId", 1);
```

### NumberUtil
Digital processing tools can be used for various types of addition, subtraction, multiplication and division operations and judgment types.

```java 
double n1 = 1.234;
double n2 = 1.234;
double result;
//Add, subtract, multiply, and divide operations on float, double, and Big Decimal
result = NumberUtil.add(n1, n2);
result = NumberUtil.sub(n1, n2);
result = NumberUtil.mul(n1, n2);
result = NumberUtil.div(n1, n2);
//Keep two decimal places
BigDecimal roundNum = NumberUtil.round(n1, 2);
String n3 = "1.234";
//Determine whether it is a number, integer, floating point
NumberUtil.isNumber(n3);
NumberUtil.isInteger(n3);
NumberUtil.isDouble(n3);
```

### BeanUtil

The JavaBean tool class can be used to convert Map and JavaBean objects and copy object attributes.

```java 
PmsBrand brand = new PmsBrand();
brand.setId(1L);
brand.setName("Xiaomi");
brand.setShowStatus(0);
//Bean to Map
Map<String, Object> map = BeanUtil.beanToMap(brand);
LOGGER.info("beanUtil bean to map:{}", map);
//Map to Bean
PmsBrand mapBrand = BeanUtil.mapToBean(map, PmsBrand.class, false);
LOGGER.info("beanUtil map to bean:{}", mapBrand);
//Bean attribute copy
PmsBrand copyBrand = new PmsBrand();
BeanUtil.copyProperties(brand, copyBrand);
LOGGER.info("beanUtil copy properties:{}", copyBrand);
```

### CollUtil
The collection operation tool class defines some commonly used collection operations.

```java 
//Array to list
String[] array = new String[]{"a", "b", "c", "d", "e"};
List<String> list = CollUtil.newArrayList(array);
//join：Add connection symbol when array is converted to string
String joinStr = CollUtil.join(list, ",");
LOGGER.info("collUtil join:{}", joinStr);
//Convert strings separated by connection symbols into lists
List<String> splitList = StrUtil.split(joinStr, ',');
LOGGER.info("collUtil split:{}", splitList);
//Create a new Map、Set、List
HashMap<Object, Object> newMap = CollUtil.newHashMap();
HashSet<Object> newHashSet = CollUtil.newHashSet();
ArrayList<Object> newList = CollUtil.newArrayList();
//Determine if the list is empty
CollUtil.isEmpty(list);
```

### MapUtil
Map operation tool class can be used to create Map objects and determine whether the Map is empty.

```java 
//Add multiple key-value pairs to Map
Map<Object, Object> map = MapUtil.of(new String[][]{
    {"key1", "value1"},
    {"key2", "value2"},
    {"key3", "value3"}
});
//Determine if Map is empty
MapUtil.isEmpty(map);
MapUtil.isNotEmpty(map);
```

### AnnotationUtil

Annotation tool class can be used to obtain annotations, and the values specified in annotations.

```java 
//Get the list of annotations on the specified class, method, field, and constructor
Annotation[] annotationList = AnnotationUtil.getAnnotations(HutoolController.class, false);
LOGGER.info("annotationUtil annotations:{}", annotationList);
//Get annotation of specified type
Api api = AnnotationUtil.getAnnotation(HutoolController.class, Api.class);
LOGGER.info("annotationUtil api value:{}", api.description());
//Get the value of the specified type of annotation
Object annotationValue = AnnotationUtil.getAnnotationValue(HutoolController.class, RequestMapping.class);
```

### SecureUtil
Encryption and decryption tools can be used for MD 5 encryption.

```java 
//MD5 encryption
String str = "123456";
String md5Str = SecureUtil.md5(str);
LOGGER.info("secureUtil md5:{}", md5Str);
```

### CaptchaUtil
Verification code tools can be used to generate graphical verification codes.

```java 
//Generate verification Captcha
LineCaptcha lineCaptcha = CaptchaUtil.createLineCaptcha(200, 100);
try {
    request.getSession().setAttribute("CAPTCHA_KEY", lineCaptcha.getCode());
    response.setContentType("image/png");//Tell the browser that the output is a picture
    response.setHeader("Pragma", "No-cache");//Disable browser caching
    response.setHeader("Cache-Control", "no-cache");
    response.setDateHeader("Expire", 0);
    lineCaptcha.write(response.getOutputStream());
} catch (IOException e) {
    e.printStackTrace();
}
```

### Other tools

There are many tools in Hutool, you can refer to: [https://www.hutool.cn/](https://www.hutool.cn/)

## Project source address

[https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-hutool](https://github.com/kratos47mhs/mall-learning/tree/master/mall-tiny-hutool)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)

