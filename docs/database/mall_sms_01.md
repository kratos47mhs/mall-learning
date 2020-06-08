The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Marketing module database table analysis (1)

> This article mainly analyzes the time-limited purchase (second spike) function related table, using the form of database table and function comparison.

## Related table structure

### Flash Promotion form

> Used to store information about Flash Promotion purchase activities, including start time, end time, and online and offline status.

```sql
create table sms_flash_promotion
(
   id                   bigint not null auto_increment,
   title                varchar(200) comment '标题',
   start_date           date comment '开始日期',
   end_date             date comment '结束日期',
   status               int(1) comment '上下线状态',
   create_time          datetime comment '创建时间',
   primary key (id)
);
```

### Flash Promotion purchase schedule

> It is used to store information about Flash Promotion purchases. In a day, a Flash Promotion purchase will have multiple different time periods.

```sql
create table sms_flash_promotion_session
(
   id                   bigint not null auto_increment comment '编号',
   name                 varchar(200) comment '场次名称',
   start_time           time comment '每日开始时间',
   end_time             time comment '每日结束时间',
   status               int(1) comment '启用状态：0->不启用；1->启用',
   create_time          datetime comment '创建时间',
   primary key (id)
);
```

### Flash Promotion purchase and product relationship table

> It is used to store product information related to Flash Promotion purchases. There are multiple sessions in a Flash Promotion purchase, and different events can be set for each session.

```sql
create table sms_flash_promotion_product_relation
(
   id                   bigint not null auto_increment,
   flash_promotion_id   bigint comment '限时购id',
   flash_promotion_session_id bigint comment '编号',
   product_id           bigint comment '商品价格',
   flash_promotion_price decimal(10,2) comment '限时购价格',
   flash_promotion_count int comment '限时购数量',
   flash_promotion_limit int comment '每人限购数量',
   sort                 int comment '排序',
   primary key (id)
);
```

### Flash Promotion purchase log record Table

> It is used to store the member's Flash Promotion purchase reservation record. When some Flash Promotion purchase sessions have not yet started, the member can make a reservation operation. When the session starts, the system will remind.

```sql
create table sms_flash_promotion_log
(
   id                   int not null auto_increment,
   member_id            int comment '会员id',
   product_id           bigint comment '商品id',
   member_phone         varchar(64) comment '会员电话',
   product_name         varchar(100) comment '商品名称',
   subscribe_time       datetime comment '会员订阅时间',
   send_time            datetime comment '发送时间',
   primary key (id)
);
```

## Management display

### Flash Promotion purchase data list
![](../images/database_screen_72.png)

### Edit Flash Promotion purchases
![](../images/database_screen_73.png)

### List of Flash Promotion purchases
![](../images/database_screen_74.png)

### Edit Flash Promotion purchases
![](../images/database_screen_75.png)

### Add items to the Flash Promotion session

#### Click to set up products
![](../images/database_screen_76.png)

#### Click on the product list
![](../images/database_screen_77.png)

#### Select products to add
![](../images/database_screen_78.png)

**Note: Products added to the limited Flash Promotion need to be modified`pms_product`Table`promotion_type`For 5, the discount calculation rule should also be changed to use the Flash Promotion purchase discount.**

### Edit Flash Promotion purchase information
![](../images/database_screen_79.png)

## Mobile display

### A Flash Promotion purchase has been opened
![](../images/database_screen_80.png)

### Flash Promotion purchase in panic buying
![](../images/database_screen_81.png)

### Upcoming Flash Promotion purchase
![](../images/database_screen_82.png)

### You can set an appointment reminder for the upcoming Flash Promotion purchase
![](../images/database_screen_83.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
