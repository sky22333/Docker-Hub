## docker部署sudt支付

### 创建配置文件

```
mkdir epusdt && cd epusdt && touch docker-compose.yml epusdt.conf epusdt.sql
```

#### docker-compose.yml

```
version: "3.8"
services:
  db:
    image: mariadb:focal
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=admin520
      - MYSQL_DATABASE=epusdt
      - MYSQL_USER=epusdt
      - MYSQL_PASSWORD=admin520
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

修改`MYSQL_ROOT_PASSWORD`数据库root密码

修改`MYSQL_PASSWORD`数据库用户密码

用户名和数据库名不用修改

epusdt端口为`8333`


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
mysql_passwd=admin520
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
#管理员TG的id
tg_manage=

#api接口认证token
api_auth_token=

#订单过期时间(单位分钟)
order_expiration_time=10

#强制汇率(设置此参数后每笔交易将按照此汇率计算，例如:6.4)
forced_usdt_rate=
```


修改第 3 行`app_uri`为上文为epusdt准备的独立域名

修改第 24 行`mysql_passwd`为上节MYSQL_PASSWORD的用户密码(注意:非 root 密码)

修改第 55 行`api_auth_token=`创建一个强密码用于支付设置中使用.

注意:因为本项目是独立部署到 docker compose 内,所以第 21,33 行已经修改为db,redis,不能使用 127.0.0.1.

修改第 48 行`tg_bot_token=`为上文创建的 Telegram Bot 的Token

修改第 52 行`tg_manage=`改为你的TG的ID


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

此配置勿动！


#### 首次启动

```
docker-compose up -d
```

#### 初始化数据库

将下述命令中的`数据库`的密码改为你的数据库密码，注意需要保留前缀`-p`不能有空格。
如下图执行后无任何显示代表成功,否则将会报错.

```
docker exec -i epusdt-db-1 sh -c 'exec mysql -uepusdt -p数据库密码 epusdt' < epusdt.sql
```

#### 重启服务

```
docker-compose restart
```

#### 检查服务

```
docker logs -f epusdt-epusdt-1
```

查看epusdt服务出现`http server started on [::]:8000`则表示成功.

(可选)如果报错需要重新部署请删除数据库`rm -rf ./mysql/*`


#### 配置反代域名

仅需要将usdt 域名反代到 `8333`

访问域名显示`hello epusdt, https://github.com/assimon/epusdt`表示成功


#### 配置支付

商户ID填你创建的`api_auth_token=`密码

商户密钥填写API地址`https://USDT域名/api/v1/order/create-transaction`


---
---
---

### 另一个版本的epusdt（快速部署）
[项目地址](https://github.com/v03413/bepusdt)


```
docker run -d --restart=always \
-p 8999:8080 \
-e TG_BOT_TOKEN=机器人token \
-e TG_BOT_ADMIN_ID=TG账户ID \
-e AUTH_TOKEN=认证token \
-e APP_URI=支付域名需带HTTPS \
v03413/bepusdt:1.9.22
```

---
---
---
---

### v2board接口


创建文件`EpusdtPay.php`写入以下配置，然后放到支付插件目录。

```
<?php

namespace App\Payments;

use \Curl\Curl;

class EpusdtPay {
    public function __construct($config)
    {
        $this->config = $config;
    }

    public function form()
    {
        return [
            'epusdt_pay_url' => [
                'label' => 'API 地址',
                'description' => '您的 EpusdtPay API 接口地址(例如: https://epusdt-pay.xxx.com)',
                'type' => 'input',
            ],
            'epusdt_pay_apitoken' => [
                'label' => 'API Token',
                'description' => '您的 EpusdtPay API Token',
                'type' => 'input',
            ]
        ];
    }

    public function pay($order)
    {
        $params = [
			"amount" => round($order['total_amount']/100,2),
			"order_id" => $order['trade_no'], 
			'redirect_url' => $order['return_url'],
			'notify_url' => $order['notify_url'],
        ];
        $params['signature'] = $this->sign($params);

        $curl = new Curl();
        $curl->setUserAgent('EpusdtPay');
        $curl->setOpt(CURLOPT_SSL_VERIFYPEER, 0);
        $curl->setOpt(CURLOPT_HTTPHEADER, array('Content-Type:application/json'));
        $curl->post($this->config['epusdt_pay_url'] . '/api/v1/order/create-transaction', json_encode($params));
        $result = $curl->response;
        $curl->close();
        if (!isset($result->status_code) || $result->status_code != 200) {
            abort(500, "Failed to create order. Error: {$result->message}");
        }
        return [
            'type' => 1, // 0:qrcode 1:url
            'data' => $result->data->payment_url
        ];
    }

    public function notify($params)
    {
        $status = $params['status'];
        // 1：等待支付，2：支付成功，3：已过期
        if ($status != 2) {
            die('failed');
        }
        //不合法的数据
        if (!$this->verify($params)) {
            die('cannot pass verification'); 
        }
        return [
            'trade_no' => $params['order_id'],
            'callback_no' => $params['trade_id'],
            'custom_result' => 'ok'
        ];
    }
    
    public function verify($params) {
        return $params['signature'] === $this->sign($params);
    }

    protected function sign(array $params)
    {
        ksort($params);
        reset($params); //内部指针指向数组中的第一个元素
        $sign = '';
        $urls = '';
        foreach ($params as $key => $val) {
            if ($val == '') continue;
            if ($key != 'signature') {
                if ($sign != '') {
                    $sign .= "&";
                    $urls .= "&";
                }
                $sign .= "$key=$val"; //拼接为url参数形式
                $urls .= "$key=" . urlencode($val); //拼接为url参数形式
            }
        }
        $sign = md5($sign . $this->config['epusdt_pay_apitoken']);//密码追加进入开始MD5签名
        return $sign;
    }
}
```

---
---

---

---
### 修复异次元epusdt支付不回调

`app/Pay/Epusdt/Impl/Signature.php`这个文件。

把里面的代码清空，复制下方的代码进行保存即可

```
<?php
declare(strict_types=1);

namespace App\Pay\Epusdt\Impl;

use Kernel\Util\Context;
/**
 * Class Signature
 * @package App\Pay\Kvmpay\Impl
 */
class Signature implements \App\Pay\Signature
{

    /**
     * 生成签名
     * @param array $data
     * @param string $key
     * @return string
     */
    public static function generateSignature(array $data, string $key): string
    {
        ksort($data);
        $sign = '';
        foreach ($data as $k => $v) {
            if ($v == '') continue;
            $sign .= $k . '=' . $v . '&';
        }
        $sign = trim($sign, '&');
        return md5($sign . $key);
    }

    /**
     * @inheritDoc
     */
    public function verification(array $data, array $config): bool
    {   
        $data = json_decode(file_get_contents('php://input'),true); 
        $sign = $data['signature'];
        unset($data['signature']);
        $generateSignature = self::generateSignature($data, $config['key']);
        if ($sign != $generateSignature) {
            return false;
        }
        Context::set(\App\Consts\Pay::DAFA, $data);
        return true;
    }
}
```
