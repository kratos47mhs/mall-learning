The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public) Get it the first time.

# Marketing module database table analysis (2)

> This article mainly analyzes the coupon function related table, using the form of database table and function comparison.

## Related table structure

### Coupon form

> For storing coupon information, it should be noted that the type of coupon used: 0->general audience; 1->specified category; 2->specified products, different types of coupons use different ranges.

```sql
create table sms_coupon
(
   id                   bigint not null auto_increment,
   type                 int(1) comment '优惠卷类型；0->全场赠券；1->会员赠券；2->购物赠券；3->注册赠券',
   name                 varchar(100) comment '名称',
   platform             int(1) comment '使用平台：0->全部；1->移动；2->PC',
   count                int comment '数量',
   amount               decimal(10,2) comment '金额',
   per_limit            int comment '每人限领张数',
   min_point            decimal(10,2) comment '使用门槛；0表示无门槛',
   start_time           datetime comment '开始使用时间',
   end_time             datetime comment '结束使用时间',
   use_type             int(1) comment '使用类型：0->全场通用；1->指定分类；2->指定商品',
   note                 varchar(200) comment '备注',
   publish_count        int comment '发行数量',
   use_count            int comment '已使用数量',
   receive_count        int comment '领取数量',
   enable_time          datetime comment '可以领取的日期',
   code                 varchar(64) comment '优惠码',
   member_level         int(1) comment '可领取的会员类型：0->无限制',
   primary key (id)
);
```

### Coupon history table

> It is used to store the records of members' receipt and use of coupons. When a member receives a coupon, a record of coupons will be generated. It should be noted that its use status: 0->unused; 1->used; 2 -> has expired.

```sql
create table sms_coupon_history
(
   id                   bigint not null auto_increment,
   coupon_id            bigint comment '优惠券id',
   member_id            bigint comment '会员id',
   order_id             bigint comment '订单id',
   coupon_code          varchar(64) comment '优惠券码',
   member_nickname      varchar(64) comment '领取人昵称',
   get_type             int(1) comment '获取类型：0->后台赠送；1->主动获取',
   create_time          datetime comment '创建时间',
   use_status           int(1) comment '使用状态：0->未使用；1->已使用；2->已过期',
   use_time             datetime comment '使用时间',
   order_sn             varchar(100) comment '订单号码',
   primary key (id)
);
```

### Coupon and merchandise relationship table

> It is used to store the relationship between coupons and products. When the use type of the coupon is a specified product, the coupon and the product need to establish a relationship.

```sql
create table sms_coupon_product_relation
(
   id                   bigint not null auto_increment,
   coupon_id            bigint comment '优惠券id',
   product_id           bigint comment '商品id',
   product_name         varchar(500) comment '商品名称',
   product_sn           varchar(200) comment '商品条码',
   primary key (id)
);
```

### Coupon and product category relationship table

> It is used to store the relationship between coupons and product categories. When the use type of coupons is a specified category, the coupons and commodity categories need to be established.

```sql
create table sms_coupon_product_category_relation
(
   id                   bigint not null auto_increment,
   coupon_id            bigint comment '优惠券id',
   product_category_id  bigint comment '商品分类id',
   product_category_name varchar(200) comment '商品分类名称',
   parent_category_name varchar(200) comment '父分类名称',
   primary key (id)
);
```

## Management display

### Coupon list
![](../images/database_screen_84.png)

### Edit coupon

#### Universal
![](../images/database_screen_85.png)

#### Designated product
![](../images/database_screen_86.png)

#### Designated category
![](../images/database_screen_87.png)

### View coupons
![](../images/database_screen_88.png)

## Mobile display

### my discount coupon

#### Unused
![](../images/database_screen_89.png)

#### Used
![](../images/database_screen_90.png)

#### Expired
![](../images/database_screen_91.png)

### Coupon details
![](../images/database_screen_92.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)


