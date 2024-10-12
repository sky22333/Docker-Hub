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
将文件上传到`/etc/XrayR`目录下

解压文件
```
unzip XrayR-linux-64.zip
```

创建系统服务
```
sudo vim /etc/systemd/system/xrayr.service
```
```
[Unit]
Description=XrayR Service
After=network.target nss-lookup.target
Wants=network.target

[Service]
User=root
Group=root
Type=simple
LimitAS=infinity
LimitRSS=infinity
LimitCORE=infinity
LimitNOFILE=999999
WorkingDirectory=/etc/XrayR/
ExecStart=/etc/XrayR/XrayR --config /etc/XrayR/config.yml
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

```
加载服务
sudo systemctl daemon-reload
重启服务
sudo systemctl restart xrayr.service
设置服务开机自启
sudo systemctl enable xrayr.service
检查服务状态
sudo systemctl status xrayr.service
```



---
ws传输配置，域名可留空，可更换伪装域名用来优选cf
```
{
  "path": "/docker",
  "headers": {
    "Host": "v2ray.com"
  }
}
```

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
      "outboundTag": "IPv4_out" //如需域名分流可改成us1路由，使用时需删掉注释
    },
    {
      "type": "field",
      "outboundTag": "IPv4_out", //如需全局分流可改成us1路由，使用时需删掉注释
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

#### 无审计版
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
    },
    {
      "type": "field",
      "outboundTag": "IPv4_out",
      "network": "udp,tcp"
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
    "tag": "IPv6_out",
    "protocol": "freedom",
    "settings": {
      "domainStrategy": "UseIPv6"
    }
  },
  {
    "tag": "us1",
    "protocol": "socks",
    "settings": {
      "servers": [
        {
          "address": "127.0.0.1",
          "port": 1080,
          "users": [
            {
              "user": "admin123",
              "pass": "admin123"
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
  Level: warning # 日志级别: none（无）、error（错误）、warning（警告）、info（信息）、debug（调试）
  AccessPath: # 访问日志路径，例如 /etc/XrayR/access.Log
  ErrorPath: # 错误日志路径，例如 /etc/XrayR/error.log
DnsConfigPath: # DNS 配置路径，例如 /etc/XrayR/dns.json # 有关帮助，请查看 https://xtls.github.io/config/dns.html
RouteConfigPath: # 路由配置路径，例如 /etc/XrayR/route.json # 有关帮助，请查看 https://xtls.github.io/config/routing.html
InboundConfigPath: # 自定义入站配置路径，例如 /etc/XrayR/custom_inbound.json # 有关帮助，请查看 https://xtls.github.io/config/inbound.html
OutboundConfigPath: # 自定义出站配置路径，例如 /etc/XrayR/custom_outbound.json # 有关帮助，请查看 https://xtls.github.io/config/outbound.html
ConnectionConfig:
  Handshake: 4 # 握手时间限制，单位：秒
  ConnIdle: 30 # 连接空闲时间限制，单位：秒
  UplinkOnly: 2 # 在下行关闭时连接的时间限制，单位：秒
  DownlinkOnly: 4 # 在上行关闭后连接关闭的时间限制，单位：秒
  BufferSize: 64 # 每个连接的内部缓存大小，单位：KB
Nodes:
  - PanelType: "NewV2board" # 面板类型: SSpanel、NewV2board、PMpanel、Proxypanel、V2RaySocks、GoV2Panel、BunPanel
    ApiConfig:
      ApiHost: "https://xxxxxx.com" # 面板主机地址
      ApiKey: "123abcd" # API 密钥
      NodeID: 3 # 节点ID
      NodeType: V2ray # 节点类型: V2ray、Vmess、Vless、Shadowsocks、Trojan、Shadowsocks-Plugin
      Timeout: 30 # API 请求的超时时间
      EnableVless: false # 是否启用 Vless（仅适用于 V2ray 类型）
      VlessFlow: "xtls-rprx-vision" # 仅支持 Vless
      SpeedLimit: 0 # Mbps，本地设置将替换远程设置，0 表示禁用
      DeviceLimit: 0 # 本地设置将替换远程设置，0 表示禁用
      RuleListPath: # /etc/XrayR/rulelist 本地规则列表文件的路径
      DisableCustomConfig: false # 禁用 SSpanel 的自定义配置
    ControllerConfig:
      ListenIP: 0.0.0.0 # 监听的 IP 地址
      SendIP: 0.0.0.0 # 发送数据包的 IP 地址
      UpdatePeriodic: 60 # 更新节点信息的时间间隔，单位：秒
      EnableDNS: false # 使用自定义 DNS 配置，请确保 dns.json 配置正确
      DNSType: AsIs # DNS 策略: AsIs、UseIP、UseIPv4、UseIPv6
      EnableProxyProtocol: false # 仅适用于 WebSocket 和 TCP
      AutoSpeedLimitConfig:
        Limit: 0 # 警告速度。设置为 0 以禁用自动速度限制（单位：Mbps）
        WarnTimes: 0 # 连续警告次数后，用户将被限制。设置为 0 将立即惩罚超速用户。
        LimitSpeed: 0 # 限制用户的速度限制（单位：Mbps）
        LimitDuration: 0 # 限制持续时间（单位：分钟）
      GlobalDeviceLimitConfig:
        Enable: false # 启用用户的全局设备限制
        RedisNetwork: tcp # Redis 协议，tcp 或 unix
        RedisAddr: 127.0.0.1:6379 # Redis 服务器地址，或 unix 套接字路径
        RedisUsername: # Redis 用户名
        RedisPassword: YOUR PASSWORD # Redis 密码
        RedisDB: 0 # Redis 数据库
        Timeout: 5 # Redis 请求超时时间
        Expiry: 60 # 过期时间（单位：秒）
      EnableFallback: false # 仅支持 Trojan 和 Vless
      FallBackConfigs:  # 支持多个回退配置
        - SNI: # TLS SNI（服务器名称指示），为空表示匹配任意
          Alpn: # Alpn，为空表示匹配任意
          Path: # HTTP 路径，为空表示匹配任意
          Dest: 80 # 必需，回退的目的地，详细信息请查看 https://xtls.github.io/config/features/fallback.html
          ProxyProtocolVer: 0 # 发送 PROXY 协议版本，0 表示禁用
      DisableLocalREALITYConfig: false  # 禁用本地 REALITY 配置
      EnableREALITY: false # 启用 REALITY
      REALITYConfigs:
        Show: true # 显示 REALITY 调试信息
        Dest: www.amazon.com:443 # 必需，和回退相同
        ProxyProtocolVer: 0 # 发送 PROXY 协议版本，0 表示禁用
        ServerNames: # 必需，客户端可用的服务器名称列表，* 通配符目前不支持。
          - www.amazon.com
        PrivateKey: YOUR_PRIVATE_KEY # 必需，执行 './XrayR x25519' 以生成。
        MinClientVer: # 可选，Xray 客户端的最小版本，格式为 x.y.z。
        MaxClientVer: # 可选，Xray 客户端的最大版本，格式为 x.y.z。
        MaxTimeDiff: 0 # 可选，允许的最大时间差，单位为毫秒。
        ShortIds: # 必需，客户端可用的短 ID 列表，可用于区分不同的客户端。
          - ""
          - 0123456789abcdef
      CertConfig:
        CertMode: dns # 证书获取方式: none（无）、file（文件）、http、tls、dns。选择 "none" 将强制禁用 tls 配置。
        CertDomain: "node1.test.com" # 证书域名
        CertFile: /etc/XrayR/cert/node1.test.com.cert # 如果 CertMode 是 file，则提供此项
        KeyFile: /etc/XrayR/cert/node1.test.com.key # 证书密钥文件
        Provider: alidns # DNS 证书提供者，获取完整支持列表请查看 https://go-acme.github.io/lego/dns/
        Email: test@me.com # 注册证书时的邮箱
        DNSEnv: # DNS ENV 选项，用于 DNS 提供者
          ALICLOUD_ACCESS_KEY: aaa # 阿里云访问密钥
          ALICLOUD_SECRET_KEY: bbb # 阿里云秘密密钥
```
