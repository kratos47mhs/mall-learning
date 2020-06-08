The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Order module database table analysis (3)

> This article mainly analyzes the order-returning and order-returning reason setting function related tables, using the form of database table and function comparison.

## Order return

### Related table structure

#### Order return application form

> Mainly used to store member return application information, it should be noted that the order return application form has four states: 0-> pending; 1-> return in progress; 2-> completed; 3-> rejected.

```sql
create table oms_order_return_apply
(
   id                   bigint not null auto_increment,
   order_id             bigint comment '订单id',
   company_address_id   bigint comment '收货地址表id',
   product_id           bigint comment '退货商品id',
   order_sn             varchar(64) comment '订单编号',
   create_time          datetime comment '申请时间',
   member_username      varchar(64) comment '会员用户名',
   return_amount        decimal(10,2) comment '退款金额',
   return_name          varchar(100) comment '退货人姓名',
   return_phone         varchar(100) comment '退货人电话',
   status               int(1) comment '申请状态：0->待处理；1->退货中；2->已完成；3->已拒绝',
   handle_time          datetime comment '处理时间',
   product_pic          varchar(500) comment '商品图片',
   product_name         varchar(200) comment '商品名称',
   product_brand        varchar(200) comment '商品品牌',
   product_attr         varchar(500) comment '商品销售属性：颜色：红色；尺码：xl;',
   product_count        int comment '退货数量',
   product_price        decimal(10,2) comment '商品单价',
   product_real_price   decimal(10,2) comment '商品实际支付单价',
   reason               varchar(200) comment '原因',
   description          varchar(500) comment '描述',
   proof_pics           varchar(1000) comment '凭证图片，以逗号隔开',
   handle_note          varchar(500) comment '处理备注',
   handle_man           varchar(100) comment '处理人员',
   receive_man          varchar(100) comment '收货人',
   receive_time         datetime comment '收货时间',
   receive_note         varchar(500) comment '收货备注',
   primary key (id)
);
```

#### Company delivery address table

> Used to select the delivery address when processing return applications.

```sql
create table oms_company_address
(
   id                   bigint not null auto_increment,
   address_name         varchar(200) comment '地址名称',
   send_status          int(1) comment '默认发货地址：0->否；1->是',
   receive_status       int(1) comment '是否默认收货地址：0->否；1->是',
   name                 varchar(64) comment '收发货人姓名',
   phone                varchar(64) comment '收货人电话',
   province             varchar(64) comment '省/直辖市',
   city                 varchar(64) comment '市',
   region               varchar(64) comment '区',
   detail_address       varchar(200) comment '详细地址',
   primary key (id)
);
```

### Management display

- Return application list
![](../images/database_screen_55.png)
- Pending status details
![](../images/database_screen_56.png)
![](../images/database_screen_57.png)
- Details of status in return
![](../images/database_screen_58.png)
![](../images/database_screen_59.png)
- Details of completed status
![](../images/database_screen_60.png)
![](../images/database_screen_61.png)
- Details of rejected status
![](../images/database_screen_62.png)
![](../images/database_screen_63.png)

### Mobile display
- Open after-sales service in mine  
![](../images/database_screen_64.png)
- Click Apply for return to apply for return  
![](../images/database_screen_65.png)
- Submit a return request  
![](../images/database_screen_66.png)
- View the return application record in the application record  
![](../images/database_screen_67.png)
- View the details of the return application progress  
![](../images/database_screen_68.png)

## Order return reason setting

### Order return reason table

> Used to select the reason for the return when the member returns.

```sql
create table oms_order_return_reason
(
   id                   bigint not null auto_increment,
   name                 varchar(100) comment '退货类型',
   sort                 int,
   status               int(1) comment '状态：0->不启用；1->启用',
   create_time          datetime comment '添加时间',
   primary key (id)
);
```

### Management display

- Return reason list  
![](../images/database_screen_69.png)
- Add return reason  
![](../images/database_screen_70.png)

### Mobile display

- Choose the reason for return when applying for return  
![](../images/database_screen_71.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)
