## Docker部署epusdt

### 创建配置文件

```
mkdir -p epusdt && cd epusdt && touch docker-compose.yml epusdt.conf epusdt.sql
```

#### docker-compose.yml

```
services:
  db:
    image: mariadb:focal
    container_name: mariadb
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=epusdt7890
      - MYSQL_DATABASE=epusdt
      - MYSQL_USER=epusdt
      - MYSQL_PASSWORD=epusdt7890
    volumes:
      - ./epusdt.sql:/docker-entrypoint-initdb.d/epusdt.sql

  redis:
    image: redis:alpine
    restart: always

  epusdt:
    image: stilleshan/epusdt
    container_name: epusdt
    restart: always
    ports:
      - 8000:8000
    depends_on:
      - db
      - redis
    volumes:
      - ./epusdt.conf:/app/.env
```

#### epusdt.conf

```
app_name=epusdt
# 下面配置你的域名，收银台会需要
app_uri=https://epusdt.com
# 是否开启debug，默认false
app_debug=false
# http服务监听端口
http_listen=:8000

# 静态资源文件目录
static_path=/static
# 缓存路径
runtime_root_path=/runtime

# 日志配置
log_save_path=/logs
log_max_size=32
log_max_age=7
max_backups=3

# mysql配置
mysql_host=db
mysql_port=3306
mysql_user=epusdt
mysql_passwd=epusdt7890
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

# TG机器人token
tg_bot_token=
# TG代理url（国内服务器才需要配置）
tg_proxy=
# TG管理员账号的id
tg_manage=
# 对接的认证token
api_auth_token=

#订单过期时间(单位分钟)
order_expiration_time=10

#强制汇率(例如:7.2)
forced_usdt_rate=
```

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


#### 运行

```
docker compose up -d
```


#### 检查服务

```
docker logs epusdt
```

查看epusdt服务出现`http server started on [::]:8000`则表示成功.


然后配置反代域名到 `8000`


#### 独角数卡配置支付

商户ID填你创建的`api_auth_token=`密码

商户密钥填写API地址`https://USDT域名/api/v1/order/create-transaction`


---
---
---

### 另一个版本的bepusdt（快速部署）

```
docker run -d \
  --name bepusdt \
  --restart always \
  -p 7000:7000 \
  -e TG_BOT_TOKEN=机器人token \
  -e TG_BOT_ADMIN_ID=TG账户ID \
  -e AUTH_TOKEN=认证token \
  ghcr.io/sky22333/bepusdt:latest
```

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
                'description' => '您的 Epusdt 地址(例如: https://example.com)',
                'type' => 'input',
            ],
            'epusdt_pay_apitoken' => [
                'label' => 'API Token',
                'description' => '您的 Epusdt Token',
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
