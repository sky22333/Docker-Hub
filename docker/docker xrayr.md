###  docker安装
创建配置文件，并用下面的命令启动
```  
mkdir -p /etc/XrayR && touch /etc/XrayR/config.yml && cd /etc/XrayR
```

```
docker pull cloudorz/xrayr:latest
```
```
docker run --restart=always --name xrayr -d -v /etc/XrayR/config.yml:/etc/XrayR/config.yml --network=host cloudorz/xrayr:latest
```



[配置文件教程地址](https://xrayr-project.github.io/XrayR-doc/xrayr-pei-zhi-wen-jian-shuo-ming/config.html)




###  脚本安装xrayR：
```
wget -N https://raw.githubusercontent.com/XrayR-project/XrayR-release/master/install.sh && bash install.sh
```
### v2bx脚本（支持hy2，vless）
```
wget -N https://raw.githubusercontent.com/wyx2685/V2bX-script/master/install.sh && bash install.sh
```


---

配置文件路径： `/etc/XrayR`

###  手动安装：

[下载github上的xrayr包](https://github.com/XrayR-project/XrayR/releases)  一般下载`XrayR-linux-64.zip`的文件


创建文件
```
mkdir -p /etc/XrayR && cd /etc/XrayR
```
将文件上传到`/etc/XrayR`目录

解压文件
```
unzip /etc/XrayR-linux-64.zip
```

编辑节点配置`/etc/XrayR/config.yml`


后台运行
```
nohup ./XrayR --config config.yml &
```
查看日志
```
tail -f nohup.out

cat /etc/nohup.out
```
查看进程ID
```
ps aux | grep XrayR
```
停止xrayr
```
sudo kill -9 [PID]
```

---


# 二级代理
`config.yml`配置中的`RouteConfigPath`和`OutboundConfigPath`注释去掉

## 配置`route.json`

#### 路由规则
```
{
  "domainStrategy": "IPOnDemand",
  "rules": [
    {
      "type": "field",
      "domain": [
        "domain:chatgpt.com",
        "domain:netflix.com"
      ],
      "outboundTag": "IPv4_out" // 如需分流可改成us1路由
    },
    {
      "type": "field",
      "outboundTag": "IPv4_out",
      "network": "udp,tcp"
    },
    {
      "type": "field",
      "outboundTag": "block",
      "ip": [
        "geoip:private"
      ]
    },
    {
      "type": "field",
      "outboundTag": "block",
      "protocol": [
        "bittorrent"
      ]
    },
    {
       "type": "field",
       "outboundTag": "block",
       "port": "22,23,24,25,107,194,445,465,587,992,3389,6665-6669,6679,6697,6881-6999,7000"
    }
  ]
}
```

#### 域名分流
```
{
  "domainStrategy": "IPOnDemand",
  "rules": [
    {
      "type": "field",
      "domain": [
        "domain:chatgpt.com",
        "domain:netflix.com"
      ],
      "outboundTag": "us1"
    }
  ]
}
```

## 配置`custom_outbound.json`

```
[
  {
    "tag": "IPv4_out",
    "protocol": "freedom",
    "settings": {}
  },
  {
    "tag": "us1",
    "protocol": "socks",
    "settings": {
      "servers": [
        {
          "address": "192.168.1.1",
          "port": 10808,
          "users": [
            {
              "user": "youuser",
              "pass": "youpass"
            }
          ]
        }
      ]
    }
  },
  {
    "protocol": "blackhole",
    "tag": "block"
  }
]
```


# 配置文件解释
```
Log:
  Level: warning # 日志级别: none, error, warning, info, debug
  AccessPath: # /etc/XrayR/access.Log
  ErrorPath: # /etc/XrayR/error.log
DnsConfigPath: # /etc/XrayR/dns.json # dns 配置路径
RouteConfigPath: # /etc/XrayR/route.json # 路由配置路径
InboundConfigPath: # /etc/XrayR/custom_inbound.json # 自定义入站配置路径
OutboundConfigPath: # /etc/XrayR/custom_outbound.json # 自定义出站配置路径
ConnectionConfig:
  Handshake: 4 # 握手时间限制，秒
  ConnIdle: 30 # 连接空闲时间限制，秒
  UplinkOnly: 2 # 下行连接关闭后的时间限制，秒
  DownlinkOnly: 4 # 上行连接关闭后的时间限制，秒
  BufferSize: 64 # 每个连接的内部缓存大小，kB
Nodes:
  - PanelType: "NewV2board" # 面板类型: SSpanel, NewV2board, PMpanel, Proxypanel, V2RaySocks, GoV2Panel, BunPanel
    ApiConfig:
      ApiHost: "http://127.0.0.1:667"
      ApiKey: "123asdoasdsada"
      NodeID: 41
      NodeType: V2ray # 节点类型: V2ray,vmess,vless, Shadowsocks, Trojan, Shadowsocks-Plugin
      Timeout: 30 # API请求的超时时间
      EnableVless: false # 启用Vless用于V2ray类型
      VlessFlow: "xtls-rprx-vision" # 仅支持vless
      SpeedLimit: 0 # Mbps，本地设置将覆盖远程设置，0表示禁用
      DeviceLimit: 0 # 本地设置将覆盖远程设置，0表示禁用
      RuleListPath: # /etc/XrayR/rulelist 本地规则列表文件路径
      DisableCustomConfig: false # 禁用SSpanel的自定义配置
    ControllerConfig:
      ListenIP: 0.0.0.0 # 您要监听的IP地址
      SendIP: 0.0.0.0 # 您要发送包的IP地址
      UpdatePeriodic: 60 # 更新节点信息的时间，秒
      EnableDNS: false # 使用自定义DNS配置，请确保已正确设置dns.json
      DNSType: AsIs # AsIs, UseIP, UseIPv4, UseIPv6，DNS策略
      EnableProxyProtocol: false # 仅适用于WebSocket和TCP
      AutoSpeedLimitConfig:
        Limit: 0 # 警告速度。设置为0以禁用AutoSpeedLimit（mbps）
        WarnTimes: 0 # 连续（WarnTimes）次警告后，用户将被限速。设置为0以立即惩罚超速用户。
        LimitSpeed: 0 # 受限用户的速度限制（单位：mbps）
        LimitDuration: 0 # 限速持续时间（单位：分钟）
      GlobalDeviceLimitConfig:
        Enable: false # 启用用户的全局设备限制
        RedisNetwork: tcp # Redis协议，tcp或unix
        RedisAddr: 127.0.0.1:6379 # Redis服务器地址，或unix socket路径
        RedisUsername: # Redis用户名
        RedisPassword: YOUR_PASSWORD # Redis密码
        RedisDB: 0 # Redis数据库
        Timeout: 5 # Redis请求的超时时间
        Expiry: 60 # 过期时间（秒）
      EnableFallback: false # 仅支持Trojan和Vless
      FallBackConfigs:  # 支持多个fallback
        - SNI: # TLS SNI（服务器名称指示），为空表示任何
          Alpn: # Alpn，为空表示任何
          Path: # HTTP路径，为空表示任何
          Dest: 80 # 必填，fallback的目标，请参考 https://xtls.github.io/config/features/fallback.html 获取详情
          ProxyProtocolVer: 0 # 发送PROXY协议版本，0表示禁用
      DisableLocalREALITYConfig: true  # 禁用本地reality配置
      EnableREALITY: false # 启用REALITY
      REALITYConfigs:
        Show: true # 显示REALITY调试信息
        Dest: www.amazon.com:443 # 必填，与fallback相同
        ProxyProtocolVer: 0 # 发送PROXY协议版本，0表示禁用
        ServerNames: # 必填，客户端可用的serverNames列表，目前不支持*通配符
          - www.amazon.com
        PrivateKey: YOUR_PRIVATE_KEY # 必填，执行'./XrayR x25519'生成
        MinClientVer: # 可选，Xray客户端的最小版本，格式为x.y.z。
        MaxClientVer: # 可选，Xray客户端的最大版本，格式为x.y.z。
        MaxTimeDiff: 0 # 可选，允许的最大时间差，单位为毫秒。
        ShortIds: # 必填，客户端可用的shortIds列表，可用于区分不同的客户端
          - ""
          - 0123456789abcdef
      CertConfig:
        CertMode: http # 获取证书的方式：none, file, http, tls, dns。选择"none"将强制禁用tls配置。
        CertDomain: "node1.test.com" # 证书的域名
        CertFile: /etc/XrayR/cert/node1.test.com.cert # CertMode为file时提供
        KeyFile: /etc/XrayR/cert/node1.test.com.key
        Provider: cloudflare # DNS证书提供商，获取完整支持列表请参考：https://go-acme.github.io/lego/dns/
        Email: test@me.com
        DNSEnv: # DNS提供商使用的DNS环境选项
          ALICLOUD_ACCESS_KEY: aaa
          ALICLOUD_SECRET_KEY: bbb
```
