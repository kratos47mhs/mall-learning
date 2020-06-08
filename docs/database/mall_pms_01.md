The full set of learning tutorials for the mall project are in serial，[Follow the public account](#No public)Get it the first time.

# Commodity module database table analysis (1)

> This article mainly analyzes the table of the three functions of product category, brand management, and product type, using the form of function and table structure comparison. Table analysis will only mark some fields that need to be understood. For simple fields, please refer to the table notes.

## Categories

### Product Category Table

```sql
create table pms_product_category
(
   id                   bigint not null auto_increment,
   parent_id            bigint comment '上级分类的编号：0表示一级分类',
   name                 varchar(64) comment '名称',
   level                int(1) comment '分类级别：0->1级；1->2级',
   product_count        int comment '商品数量',
   product_unit         varchar(64) comment '商品单位',
   nav_status           int(1) comment '是否显示在导航栏：0->不显示；1->显示',
   show_status          int(1) comment '显示状态：0->不显示；1->显示',
   sort                 int comment '排序',
   icon                 varchar(255) comment '图标',
   keywords             varchar(255) comment '关键字',
   description          text comment '描述',
   primary key (id)
);
```

### Management display

- Product Category list
![](../images/database_screen_02.png)
- Add product category
![](../images/database_screen_01.png)


### Mobile display

![](../images/database_screen_03.png)

## Brand management

### Product brand table

```sql
create table pms_brand
(
   id                   bigint not null auto_increment,
   name                 varchar(64) comment '名称',
   first_letter         varchar(8) comment '首字母',
   sort                 int comment '排序',
   factory_status       int(1) comment '是否为品牌制造商：0->不是；1->是',
   show_status          int(1) comment '是否显示',
   product_count        int comment '产品数量',
   product_comment_count int comment '产品评论数量',
   logo                 varchar(255) comment '品牌logo',
   big_pic              varchar(255) comment '专区大图',
   brand_story          text comment '品牌故事',
   primary key (id)
);
```

### Management display

- Brand list
![](../images/database_screen_04.png)
- Add brand
![](../images/database_screen_05.png)

### Mobile display

![](../images/database_screen_06.png)

## Product Types

> The product type is the product attribute, which mainly refers to the specifications and parameters of the product. The specifications are used for selection when the user purchases the product, and the parameters are used to mark the attribute of the product and filter when searching.

### Related table structure

#### Product attribute category table

```sql
create table pms_product_attribute_category
(
   id                   bigint not null auto_increment,
   name                 varchar(64) comment '名称',
   attribute_count      int comment '属性数量',
   param_count          int comment '参数数量',
   primary key (id)
);
```

#### Product attribute table

> The type field is used to control whether it is a specification or a parameter

```sql
create table pms_product_attribute
(
   id                   bigint not null auto_increment,
   product_attribute_category_id bigint comment '商品属性分类id',
   name                 varchar(64) comment '名称',
   select_type          int(1) comment '属性选择类型：0->唯一；1->单选；2->多选；对应属性和参数意义不同；',
   input_type           int(1) comment '属性录入方式：0->手工录入；1->从列表中选取',
   input_list           varchar(255) comment '可选值列表，以逗号隔开',
   sort                 int comment '排序字段：最高的可以单独上传图片',
   filter_type          int(1) comment '分类筛选样式：1->普通；1->颜色',
   search_type          int(1) comment '检索类型；0->不需要进行检索；1->关键字检索；2->范围检索',
   related_status       int(1) comment '相同属性产品是否关联；0->不关联；1->关联',
   hand_add_status      int(1) comment '是否支持手动新增；0->不支持；1->支持',
   type                 int(1) comment '属性的类型；0->规格；1->参数',
   primary key (id)
);
```

#### Product attribute value table

> If the corresponding parameter is a specification and the specification supports manual addition, then this table is used to store the manually added value; if the corresponding product attribute is a parameter, then this table is used to store the parameter value.

```sql
create table pms_product_attribute_value
(
   id                   bigint not null auto_increment,
   product_id           bigint comment '商品id',
   product_attribute_id bigint comment '商品属性id',
   value                varchar(64) comment '手动添加规格或参数的值，参数单值，规格有多个时以逗号隔开',
   primary key (id)
);
```

#### Product classification and attribute relationship table

> Used to generate filter attributes when searching after selecting a category.

```sql
create table pms_product_category_attribute_relation
(
   id                   bigint not null auto_increment,
   product_category_id  bigint comment '商品分类id',
   product_attribute_id bigint comment '商品属性id',
   primary key (id)
);
```

### Management display

- Product attribute classification list
![](../images/database_screen_07.png)
- Add product attribute category  
![](../images/database_screen_08.png)
- Product specification list
![](../images/database_screen_09.png)
- Product parameter list
![](../images/database_screen_10.png)
- Add product attributes
![](../images/database_screen_11.png)
- When adding a product, select the product attribute category, and the attributes of the category will be displayed for generating sku
![](../images/database_screen_12.png)
- When adding a product, select the product attribute category, and the parameters of the category will be displayed for entry
![](../images/database_screen_13.png)

### Mobile display

- Select product specifications  
![](../images/database_screen_14.png)
- View product parameters  
![](../images/database_screen_15.png)
- Used to select the classified filter when searching for products  
![](../images/database_screen_16.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)