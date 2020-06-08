The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Order module database table analysis (2)

> This article mainly analyzes the related tables of shopping cart functions and introduces the entire process from the addition of commodities to the shopping cart to the order placement, involving the shopping cart discount calculation process, confirmation order generation process, order placement process and order cancellation process.

## Shopping cart Table

> It is used to store the information of each product in the shopping cart, and can be used to calculate the discount amount of the product.

```sql
create table oms_cart_item
(
   id                   bigint not null auto_increment,
   product_id           bigint comment '商品的id',
   product_sku_id       bigint comment '商品sku的id',
   member_id            bigint comment '会员id',
   quantity             int comment '购买数量',
   price                decimal(10,2) comment '添加到购物车的价格',
   sp1                  varchar(200) comment '销售属性1',
   sp2                  varchar(200) comment '销售属性2',
   sp3                  varchar(200) comment '销售属性3',
   product_pic          varchar(1000) comment '商品主图',
   product_name         varchar(500) comment '商品名称',
   product_brand        varchar(200) comment '商品品牌',
   product_sn           varchar(200) comment '商品的条码',
   product_sub_title    varchar(500) comment '商品副标题（卖点）',
   product_sku_code     varchar(200) comment '商品sku条码',
   member_nickname      varchar(500) comment '会员昵称',
   create_date          datetime comment '创建时间',
   modify_date          datetime comment '修改时间',
   delete_status        int(1) default 0 comment '是否删除',
   product_category_id  bigint comment '商品的分类',
   product_attr         varchar(500) comment '商品销售属性:[{"key":"颜色","value":"银色"},{"key":"容量","value":"4G"}]',
   primary key (id)
);
```

## Shopping order process

### The schematic diagram of the overall process

![](../images/database_screen_44.png)

### Mobile process display

- Members choose product specifications  
![](../images/database_screen_45.png)
- Select the goods in the shopping cart to settle  
![](../images/database_screen_46.png)
- View confirmation  
![](../images/database_screen_47.png)
- Payment order  
![](../images/database_screen_48.png)
- payment successful  
![](../images/database_screen_49.png)
- check order  
![](../images/database_screen_50.png)

### Implementation logic

#### add to Shopping Cart

> The main function of the shopping cart is to store the product information selected by the user and calculate the discount of the goods in the shopping cart.

##### Shopping cart discount calculation process

![](../images/database_screen_51.jpg)

##### Related notes

- Since product discounts are designed in units of products, not in units of sku, it is necessary to calculate product discounts in units of products;
- Code implementation logic can refer to the calc Cart Promotion method in the Oms Promotion Service Impl class.

#### Generate confirmation

> The confirmation form is mainly used for the user to confirm the product information, preferential information and price information of the order, as well as to select the delivery address, select the coupon and use the points.

##### Confirmation process

![](../images/database_screen_52.jpg)

##### Related notes

- Calculation of the total amount: the total price of all items in the shopping cart；
- Calculation of activity discounts: The discount amount of all products in the shopping cart is accumulated；
- Calculation of payable amount: payable amount = total amount-activity discount；
- The code implementation logic can refer to the generate Confirm Order method in the Oms Portal Order Service Impl class.

#### Generate orders

> The information in the shopping cart processed, and the information of the ordering user integrated to generate the order.

##### Order process

![](../images/database_screen_53.jpg)

##### Related notes

- Locking of inventory: The inventory has been queried from the `pms sku stock` table when the shopping cart discount information is obtained. The lock stock field indicates the quantity of locked inventory, and the quantity of goods seen by the member is the real inventory minus the locked inventory;
- Dealing with the amount of discounted coupons: Calculate the amount of breakdown of the coupons for the entire audience, designated categories, and designated products:
  - Common to all audiences: all products placed in the shopping cart are shared equally;
  - Specified categories: the product in the corresponding category in the shopping cart are evenly distributed;
  - Specified products: The specified products included in the shopping cart are evenly distributed.
- Calculation of the actual payment amount of each product in the order: original price-promotional offer-coupon deduction-point deduction, promotional offer is the discount amount calculated in the shopping cart calculation discount process;
- Order number generation: use redis to generate, generation rules: 8-digit date + 2 digit platform number + 2 digit payment method + 6 or more self-increasing id;
- After the use of the coupon is completed, the usage status of the coupon needs to be modified;
- The code implementation logic can refer to the generate Order method in the Oms Portal Order Service Impl class.

#### cancel order

> After the order is generated, a delay task must be started to cancel the overtime order.

##### Order cancellation process

![](../images/database_screen_54.jpg)

##### Related notes

- The code implementation logic can refer to the cancel Order method in the Oms Portal Order Service Impl class.


## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
