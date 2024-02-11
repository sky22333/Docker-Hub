## docker部署sudt支付

### 创建配置文件

```
mkdir epusdt && cd epusdt && touch docker-compose.yml epusdt.conf epusdt.sql
```

#### docker-compose.yml

```
version: "3"
services:
  db:
    image: mariadb:focal
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=CHANGE_YOUR_PASSWORD
      - MYSQL_DATABASE=epusdt
      - MYSQL_USER=epusdt
      - MYSQL_PASSWORD=CHANGE_YOUR_PASSWORD
    volumes:
      - ./mysql:/var/lib/mysql

  redis:
    image: redis:alpine
    restart: always
    volumes:
      - ./redis:/data

  epusdt:
    image: stilleshan/epusdt
    restart: always
    volumes:
      - ./epusdt.conf:/app/.env
    ports:
      - 8333:8000
```

修改MYSQL_ROOT_PASSWORD数据库root密码

修改MYSQL_PASSWORD数据库用户密码

用户名和数据库名不用修改

equsdt端口为8333


#### epusdt.conf

```
app_name=epusdt
#下面配置你的域名，收银台会需要
app_uri=https://dujiaoka.com
#是否开启debug，默认false
app_debug=false
#http服务监听端口
http_listen=:8000

#静态资源文件目录
static_path=/static
#缓存路径
runtime_root_path=/runtime

#日志配置
log_save_path=/logs
log_max_size=32
log_max_age=7
max_backups=3

# mysql配置
mysql_host=db
mysql_port=3306
mysql_user=epusdt
mysql_passwd=CHANGE_YOUR_PASSWORD
# 请修改 epusdt 数据库密码
mysql_database=epusdt
mysql_table_prefix=
mysql_max_idle_conns=10
mysql_max_open_conns=100
mysql_max_life_time=6

# redis配置
redis_host=redis
redis_port=6379
redis_passwd=
redis_db=5
redis_pool_size=5
redis_max_retries=3
redis_idle_timeout=1000

# 消息队列配置
queue_concurrency=10
queue_level_critical=6
queue_level_default=3
queue_level_low=1

#机器人Apitoken
tg_bot_token=
#telegram代理url(大陆地区服务器可使用一台国外服务器做反代tg的url)，如果运行的本来就是境外服务器，则无需填写
tg_proxy=
#管理员userid
tg_manage=

#api接口认证token
api_auth_token=

#订单过期时间(单位分钟)
order_expiration_time=10

#强制汇率(设置此参数后每笔交易将按照此汇率计算，例如:6.4)
forced_usdt_rate=
```


修改第 3 行app_uri为上文为epusdt准备的独立域名

修改第 24 行mysql_passwd为上节MYSQL_PASSWORD的用户密码(注意:非 root 密码)

修改第 55 行api_auth_token=123qweASD创建一个密码用于dujiaoka 支付设置中使用.

注意:因为本项目是独立部署到 docker compose 内,所以第 21,33 行已经修改为db,redis,不能使用 127.0.0.1.

修改第 48 行tg_bot_token=为上文创建的 Telegram Bot 的Token

修改第 52 行tg_manage=为上文创建的 Telegram Bot 的ID


#### epusdt.sql

```
-- auto-generated definition
create table orders
(
    id                   int auto_increment
        primary key,
    trade_id             varchar(32)    not null comment 'epusdt订单号',
    order_id             varchar(32)    not null comment '客户交易id',
    block_transaction_id varchar(128)   null comment '区块唯一编号',
    actual_amount        decimal(19, 4) not null comment '订单实际需要支付的金额，保留4位小数',
    amount               decimal(19, 4) not null comment '订单金额，保留4位小数',
    token                varchar(50)    not null comment '所属钱包地址',
    status               int default 1  not null comment '1：等待支付，2：支付成功，3：已过期',
    notify_url           varchar(128)   not null comment '异步回调地址',
    redirect_url         varchar(128)   null comment '同步回调地址',
    callback_num         int default 0  null comment '回调次数',
    callback_confirm     int default 2  null comment '回调是否已确认？ 1是 2否',
    created_at           timestamp      null,
    updated_at           timestamp      null,
    deleted_at           timestamp      null,
    constraint orders_order_id_uindex
        unique (order_id),
    constraint orders_trade_id_uindex
        unique (trade_id)
);

create index orders_block_transaction_id_index
    on orders (block_transaction_id);

-- auto-generated definition
create table wallet_address
(
    id         int auto_increment
        primary key,
    token      varchar(50)   not null comment '钱包token',
    status     int default 1 not null comment '1:启用 2:禁用',
    created_at timestamp     null,
    updated_at timestamp     null,
    deleted_at timestamp     null
)
    comment '钱包表';

create index wallet_address_token_index
    on wallet_address (token);
```

勿动！


#### 首次启动

```
docker-compose up -d
```

#### 初始化数据库

将下述命令中的`-pCHANGE_YOUR_PASSWORD`的密码改为上述设置的新密码,注意需要保留前缀`-p`,例如上文修改密码`MYSQL_PASSWORD=aaabbbccc`,此处则为`-paaabbbccc`

如下图执行后无任何显示代表成功,否则将会报错.

```
docker exec -i epusdt-db-1 sh -c 'exec mysql -uepusdt -pCHANGE_YOUR_PASSWORD epusdt' < epusdt.sql
```

#### 重启服务

```
docker-compose down
docker-compose up -d
```

#### 检查服务

```
docker logs -f epusdt-epusdt-1
```

查看epusdt服务出现`http server started on [::]:8000`则表示成功.


#### 配置反代域名

仅需要将usdt 域名反代到 8333

访问域名显示`hello epusdt, https://github.com/assimon/epusdt`表示成功


#### 配置支付

商户ID为上述创建的密码`123qweASD`
商户密钥填写API地址`https://usdt域名.com/api/v1/order/create-transaction`


---
---
