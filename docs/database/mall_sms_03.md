The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Marketing module database table analysis (3)

> This article mainly analyzes the related table of content recommendation functions on the homepage, using the form of database table and function comparison.

## Related table structure

### Home Brand Recommendation Form

> Used to manage the direct supply information of brand manufacturers displayed on the home page.

```sql
create table sms_home_brand
(
   id                   bigint not null auto_increment,
   brand_id             bigint comment '商品品牌id',
   brand_name           varchar(64) comment '商品品牌名称',
   recommend_status     int(1) comment '推荐状态：0->不推荐;1->推荐',
   sort                 int comment '排序',
   primary key (id)
);
```

### New product list

> Used to manage the fresh and good things information displayed on the home page.

```sql
create table sms_home_new_product
(
   id                   bigint not null auto_increment,
   product_id           bigint comment '商品id',
   product_name         varchar(64) comment '商品名称',
   recommend_status     int(1) comment '推荐状态：0->不推荐;1->推荐',
   sort                 int(1) comment '排序',
   primary key (id)
);
```

### Popular recommended product list

> Used to manage the popular recommendation information displayed on the homepage.

```sql
create table sms_home_recommend_product
(
   id                   bigint not null auto_increment,
   product_id           bigint,
   product_name         varchar(64),
   recommend_status     int(1),
   sort                 int(1),
   primary key (id)
);
```

### Home Feature Recommendation Form

> Used to manage the featured selection information displayed on the home page.

```sql
create table sms_home_recommend_subject
(
   id                   bigint not null auto_increment,
   subject_id           bigint comment '专题id',
   subject_name         varchar(64) comment '专题名称',
   recommend_status     int(1) comment '推荐状态：0->不推荐;1->推荐',
   sort                 int comment '排序',
   primary key (id)
);
```

### Home Carousel Advertisement Table

> Used to manage the carousel information displayed on the homepage.

```sql
create table sms_home_advertise
(
   id                   bigint not null auto_increment,
   name                 varchar(100) comment '名称',
   type                 int(1) comment '轮播位置：0->PC首页轮播；1->app首页轮播',
   pic                  varchar(500) comment '图片地址',
   start_time           datetime comment '开始时间',
   end_time             datetime comment '结束时间',
   status               int(1) comment '上下线状态：0->下线；1->上线',
   click_count          int comment '点击数',
   order_count          int comment '下单数',
   url                  varchar(500) comment '链接地址',
   note                 varchar(500) comment '备注',
   sort                 int default 0 comment '排序',
   primary key (id)
);
```


## Management display

### Brand recommendation list
![](../images/database_screen_93.png)

### Choose Brand
![](../images/database_screen_94.png)

### New product recommendation list
![](../images/database_screen_95.png)

### Choose product
![](../images/database_screen_96.png)

### Popular recommendation list
![](../images/database_screen_97.png)

### Choose product
![](../images/database_screen_98.png)

### Subject recommendation list
![](../images/database_screen_99.png)

### Select topic
![](../images/database_screen_100.png)

### Ad list
![](../images/database_screen_101.png)

### Edit ad
![](../images/database_screen_102.png)

## Mobile display

### Homepage carousel
![](../images/database_screen_103.png)
### Direct supply from brand manufacturers
![](../images/database_screen_104.png)
### New product
![](../images/database_screen_105.png)
### Popular recommendation
![](../images/database_screen_106.png)
### Featured Feature
![](../images/database_screen_107.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
