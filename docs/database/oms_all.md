# 订单模块数据库表设计与解析-订单部分

> 本部分主要对CH新零售平台【CHMALL】的订单及订单设置功能的表进行解析，采用数据库表与功能对照的形式。

## 订单

### 相关表结构

#### 订单表

> 订单表，需要注意的是订单状态：0->待付款；1->待发货；2->已发货；3->已完成；4->已关闭；5->无效订单。

```sql
create table oms_order
(
   id                   bigint not null auto_increment comment '订单id',
   member_id            bigint not null comment '会员id',
   coupon_id            bigint comment '优惠券id',
   order_sn             varchar(64) comment '订单编号',
   create_time          datetime comment '提交时间',
   member_username      varchar(64) comment '用户帐号',
   total_amount         decimal(10,2) comment '订单总金额',
   pay_amount           decimal(10,2) comment '应付金额（实际支付金额）',
   freight_amount       decimal(10,2) comment '运费金额',
   promotion_amount     decimal(10,2) comment '促销优化金额（促销价、满减、阶梯价）',
   integration_amount   decimal(10,2) comment '积分抵扣金额',
   coupon_amount        decimal(10,2) comment '优惠券抵扣金额',
   discount_amount      decimal(10,2) comment '管理员后台调整订单使用的折扣金额',
   pay_type             int(1) comment '支付方式：0->未支付；1->支付宝；2->微信',
   source_type          int(1) comment '订单来源：0->PC订单；1->app订单',
   status               int(1) comment '订单状态：0->待付款；1->待发货；2->已发货；3->已完成；4->已关闭；5->无效订单',
   order_type           int(1) comment '订单类型：0->正常订单；1->秒杀订单',
   delivery_company     varchar(64) comment '物流公司(配送方式)',
   delivery_sn          varchar(64) comment '物流单号',
   auto_confirm_day     int comment '自动确认时间（天）',
   integration          int comment '可以获得的积分',
   growth               int comment '可以活动的成长值',
   promotion_info       varchar(100) comment '活动信息',
   bill_type            int(1) comment '发票类型：0->不开发票；1->电子发票；2->纸质发票',
   bill_header          varchar(200) comment '发票抬头',
   bill_content         varchar(200) comment '发票内容',
   bill_receiver_phone  varchar(32) comment '收票人电话',
   bill_receiver_email  varchar(64) comment '收票人邮箱',
   receiver_name        varchar(100) not null comment '收货人姓名',
   receiver_phone       varchar(32) not null comment '收货人电话',
   receiver_post_code   varchar(32) comment '收货人邮编',
   receiver_province    varchar(32) comment '省份/直辖市',
   receiver_city        varchar(32) comment '城市',
   receiver_region      varchar(32) comment '区',
   receiver_detail_address varchar(200) comment '详细地址',
   note                 varchar(500) comment '订单备注',
   confirm_status       int(1) comment '确认收货状态：0->未确认；1->已确认',
   delete_status        int(1) not null default 0 comment '删除状态：0->未删除；1->已删除',
   use_integration      int comment '下单时使用的积分',
   payment_time         datetime comment '支付时间',
   delivery_time        datetime comment '发货时间',
   receive_time         datetime comment '确认收货时间',
   comment_time         datetime comment '评价时间',
   modify_time          datetime comment '修改时间',
   primary key (id)
);
```

#### 订单商品信息表

> 订单中包含的商品信息，一个订单中会有多个订单商品信息。

```sql
create table oms_order_item
(
   id                   bigint not null auto_increment,
   order_id             bigint comment '订单id',
   order_sn             varchar(64) comment '订单编号',
   product_id           bigint comment '商品id',
   product_pic          varchar(500) comment '商品图片',
   product_name         varchar(200) comment '商品名称',
   product_brand        varchar(200) comment '商品品牌',
   product_sn           varchar(64) comment '商品条码',
   product_price        decimal(10,2) comment '销售价格',
   product_quantity     int comment '购买数量',
   product_sku_id       bigint comment '商品sku编号',
   product_sku_code     varchar(50) comment '商品sku条码',
   product_category_id  bigint comment '商品分类id',
   sp1                  varchar(100) comment '商品的销售属性1',
   sp2                  varchar(100) comment '商品的销售属性2',
   sp3                  varchar(100) comment '商品的销售属性3',
   promotion_name       varchar(200) comment '商品促销名称',
   promotion_amount     decimal(10,2) comment '商品促销分解金额',
   coupon_amount        decimal(10,2) comment '优惠券优惠分解金额',
   integration_amount   decimal(10,2) comment '积分优惠分解金额',
   real_amount          decimal(10,2) comment '该商品经过优惠后的分解金额',
   gift_integration     int not null default 0 comment '商品赠送积分',
   gift_growth          int not null default 0 comment '商品赠送成长值',
   product_attr         varchar(500) comment '商品销售属性:[{"key":"颜色","value":"颜色"},{"key":"容量","value":"4G"}]',
   primary key (id)
);
```

#### 订单操作记录表

> 当订单状态发生改变时，用于记录订单的操作信息。

```sql
create table oms_order_operate_history
(
   id                   bigint not null auto_increment,
   order_id             bigint comment '订单id',
   operate_man          varchar(100) comment '操作人：用户；系统；后台管理员',
   create_time          datetime comment '操作时间',
   order_status         int(1) comment '订单状态：0->待付款；1->待发货；2->已发货；3->已完成；4->已关闭；5->无效订单',
   note                 varchar(500) comment '备注',
   primary key (id)
);
```

### 管理端展现

#### 订单列表
![](../images/database_screen_33.png)

#### 查看订单
![](../images/database_screen_34.png)
![](../images/database_screen_35.png)
![](../images/database_screen_36.png)

#### 订单发货
![输入图片说明](https://images.gitee.com/uploads/images/2021/0202/111717_92552a39_459158.png "屏幕截图.png")

### 移动端展现

#### 不同状态下的订单
![](../images/database_screen_39.png)
![](../images/database_screen_40.png)
![](../images/database_screen_41.png)

#### 订单详情
![](../images/database_screen_42.png)
![](../images/database_screen_43.png)


## 订单设置

### 相关表结构

#### 订单设置表

> 用于对订单的一些超时操作进行设置。

```sql
create table oms_order_setting
(
   id                   bigint not null auto_increment,
   flash_order_overtime int comment '秒杀订单超时关闭时间(分)',
   normal_order_overtime int comment '正常订单超时时间(分)',
   confirm_overtime     int comment '发货后自动确认收货时间（天）',
   finish_overtime      int comment '自动完成交易时间，不能申请售后（天）',
   comment_overtime     int comment '订单完成后自动好评时间（天）',
   primary key (id)
);
```

### 管理端展现

![](../images/database_screen_37.png)

# 订单模块数据库表设计与解析-购物车部分

> 本部分主要对CH新零售平台【CHMALL】购物车功能相关表进行解析，介绍从商品加入购物车到下单的整个流程，涉及购物车优惠计算流程、确认单生成流程、下单流程及取消订单流程。

## 购物车表

> 用于存储购物车中每个商品信息，可用于计算商品优惠金额。

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

## 购物下单流程

### 整体流程示意图

![](../images/database_screen_44.png)

### 移动端流程展示

- 会员选择商品规格  
![](../images/database_screen_45.png)
- 选择购物车中商品去结算  
![](../images/database_screen_46.png)
- 查看确认单  
![](../images/database_screen_47.png)
- 支付订单  
![](../images/database_screen_48.png)
- 支付成功  
![](../images/database_screen_49.png)
- 查看订单  
![](../images/database_screen_50.png)

### 实现逻辑

#### 加入购物车

> 购物车的主要功能就是存储用户选择的商品信息及计算购物车中商品的优惠。

##### 购物车优惠计算流程

![](../images/database_screen_51.jpg)

##### 相关注意点

- 由于商品优惠都是以商品为单位来设计的，并不是以sku为单位设计的，所以必须以商品为单位来计算商品优惠；
- 代码实现逻辑可以参考OmsPromotionServiceImpl类中的calcCartPromotion方法。

#### 生成确认单

> 确认单主要用于用户确认下单的商品信息、优惠信息、价格信息，以及选择收货地址、选择优惠券和使用积分。

##### 生成确认单流程

![](../images/database_screen_52.jpg)

##### 相关注意点

- 总金额的计算：购物车中所有商品的总价；
- 活动优惠的计算：购物车中所有商品的优惠金额累加；
- 应付金额的计算：应付金额=总金额-活动优惠；
- 代码实现逻辑可以参考OmsPortalOrderServiceImpl类中的generateConfirmOrder方法。

#### 生成订单

> 对购物车中信息进行处理，综合下单用户的信息来生成订单。

##### 下单流程

![](../images/database_screen_53.jpg)

##### 相关注意点

- 库存的锁定：库存从获取购物车优惠信息时就已经从`pms_sku_stock`表中查询出来了，lock_stock字段表示锁定库存的数量，会员看到的商品数量为真实库存减去锁定库存；
- 优惠券分解金额的处理：对全场通用、指定分类、指定商品的优惠券分别进行分解金额的计算：
  - 全场通用：购物车中所有下单商品进行均摊；
  - 指定分类：购物车中对应分类的商品进行均摊；
  - 指定商品：购物车中包含的指定商品进行均摊。
- 订单中每个商品的实际支付金额计算：原价-促销优惠-优惠券抵扣-积分抵扣，促销优惠就是购物车计算优惠流程中计算出来的优惠金额；
- 订单号的生成：使用redis来生成，生成规则:8位日期+2位平台号码+2位支付方式+6位以上自增id；
- 优惠券使用完成后需要修改优惠券的使用状态；
- 代码实现逻辑可以参考OmsPortalOrderServiceImpl类中的generateOrder方法。

#### 取消订单

> 订单生成之后还需开启一个延时任务来取消超时的订单。

##### 订单取消流程

![](../images/database_screen_54.jpg)

##### 相关注意点

- 代码实现逻辑可以参考OmsPortalOrderServiceImpl类中的cancelOrder方法。

# 订单模块数据库表设计与解析-退换货部分

> 本部分主要对CH新零售平台【CHMALL】订单退货及订单退货原因设置功能相关表进行解析，采用数据库表与功能对照的形式。

## 订单退货

### 相关表结构

#### 订单退货申请表

> 主要用于存储会员退货申请信息，需要注意的是订单退货申请表的四种状态：0->待处理；1->退货中；2->已完成；3->已拒绝。

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

#### 公司收货地址表

> 用于处理退货申请时选择收货地址。

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

### 管理端展现

- 退货申请列表
![](../images/database_screen_55.png)
- 待处理状态的详情
![输入图片说明](https://images.gitee.com/uploads/images/2021/0202/111850_93e25f03_459158.png "屏幕截图.png")
![输入图片说明](https://images.gitee.com/uploads/images/2021/0202/112018_830fbeba_459158.png "屏幕截图.png")
- 退货中状态的详情
![输入图片说明](https://images.gitee.com/uploads/images/2021/0202/112147_13c277bb_459158.png "屏幕截图.png")
![输入图片说明](https://images.gitee.com/uploads/images/2021/0202/112217_1f12d3ea_459158.png "屏幕截图.png")
- 已完成状态的详情
![输入图片说明](https://images.gitee.com/uploads/images/2021/0202/112252_b70ff44c_459158.png "屏幕截图.png")
![输入图片说明](https://images.gitee.com/uploads/images/2021/0202/112328_7b2fb95a_459158.png "屏幕截图.png")
- 已拒绝状态的详情
![输入图片说明](https://images.gitee.com/uploads/images/2021/0202/112352_5a42f0c7_459158.png "屏幕截图.png")
![](../images/database_screen_63.png)

### 移动端展现
- 在我的中打开售后服务  
![](../images/database_screen_64.png)
- 点击申请退货进行退货申请  
![](../images/database_screen_65.png)
- 提交退货申请  
![](../images/database_screen_66.png)
- 在申请记录中查看退货申请记录  
![](../images/database_screen_67.png)
- 查看退货申请进度详情  
![](../images/database_screen_68.png)

## 订单退货原因设置

### 订单退货原因表

> 用于会员退货时选择退货原因。

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

### 管理端展现

- 退货原因列表  
![](../images/database_screen_69.png)
- 添加退货原因  
![](../images/database_screen_70.png)

### 移动端展现

- 退货申请时选择退货原因  
![](../images/database_screen_71.png)

