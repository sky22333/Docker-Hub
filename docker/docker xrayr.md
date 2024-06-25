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




###  脚本安装：
```
bash <(curl -Ls https://raw.githubusercontent.com/XrayR-project/XrayR-release/master/install.sh)
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



###  审计：



```
    {
        "domainStrategy": "AsIs",
        "rules": [
            {
                "type": "field",
                "outboundTag": "block",
                "ip": [
                    "geoip:private",
                    "geoip:cn"
                ]
            },
            {
                "domain": [
                    "geosite:google"
                ],
                "outboundTag": "IPv4_out",
                "type": "field"
            },
            {
                "type": "field",
                "outboundTag": "block",
                "domain": [
                    "geosite:cn"
                ]
            },
            {
                "type": "field",
                "outboundTag": "block",
                "domain": [
                    "regexp:(api|ps|sv|offnavi|newvector|ulog.imap|newloc)(.map|).(baidu|n.shifen).com",
                    "regexp:(.+.|^)(360|so).(cn|com)",
                    "regexp:(Subject|HELO|SMTP)",
                    "regexp:(torrent|.torrent|peer_id=|info_hash|get_peers|find_node|BitTorrent|announce_peer|announce.php?passkey=)",
                    "regexp:(^.@)(guerrillamail|guerrillamailblock|sharklasers|grr|pokemail|spam4|bccto|chacuo|027168).(info|biz|com|de|net|org|me|la)",
                    "regexp:(.?)(xunlei|sandai|Thunder|XLLiveUD)(.)",
                    "regexp:(..||)(dafahao|mingjinglive|botanwang|minghui|dongtaiwang|falunaz|epochtimes|ntdtv|falundafa|falungong|wujieliulan|zhengjian).(org|com|net)",
                    "regexp:(ed2k|.torrent|peer_id=|announce|info_hash|get_peers|find_node|BitTorrent|announce_peer|announce.php?passkey=|magnet:|xunlei|sandai|Thunder|XLLiveUD|bt_key)",
                    "regexp:(.+.|^)(360).(cn|com|net)",
                    "regexp:(.*.||)(guanjia.qq.com|qqpcmgr|QQPCMGR)",
                    "regexp:(.*.||)(rising|kingsoft|duba|xindubawukong|jinshanduba).(com|net|org)",
                    "regexp:(.*.||)(netvigator|torproject).(com|cn|net|org)",
                    "regexp:(..||)(visa|mycard|gash|beanfun|bank).",
                    "regexp:(.*.||)(gov|12377|12315|talk.news.pts.org|creaders|zhuichaguoji|efcc.org|cyberpolice|aboluowang|tuidang|epochtimes|zhengjian|110.qq|mingjingnews|inmediahk|xinsheng|breakgfw|chengmingmag|jinpianwang|qi-gong|mhradio|edoors|renminbao|soundofhope|xizang-zhiye|bannedbook|ntdtv|12321|secretchina|dajiyuan|boxun|chinadigitaltimes|dwnews|huaglad|oneplusnews|epochweekly|cn.rfi).(cn|com|org|net|club|net|fr|tw|hk|eu|info|me)",
                    "regexp:(.*.||)(miaozhen|cnzz|talkingdata|umeng).(cn|com)",
                    "regexp:(.*.||)(mycard).(com|tw)",
                    "regexp:(.*.||)(gash).(com|tw)",
                    "regexp:(.bank.)",
                    "regexp:(.*.||)(pincong).(rocks)",
                    "regexp:(.*.||)(taobao).(com)",
                    "regexp:(.*.||)(laomoe|jiyou|ssss|lolicp|vv1234|0z|4321q|868123|ksweb|mm126).(com|cloud|fun|cn|gs|xyz|cc)",
                    "regexp:(flows|miaoko).(pages).(dev)"
                ]
            },
            {
                "type": "field",
                "outboundTag": "block",
                "ip": [
                    "127.0.0.1/32",
                    "10.0.0.0/8",
                    "fc00::/7",
                    "fe80::/10",
                    "172.16.0.0/12"
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
            "port": "22,23,24,25,107,194,445,465,587,992,3389,6665-6669,6679,6697,6881-6999,7000,10000-65535"
            }
        ]
    }
```


---


### 根据域名分流到sk5协议节点，实现解锁奈飞和GPT

`config.yml`配置中的`RouteConfigPath`和`OutboundConfigPath`配置注释去掉

配置`route.json`

```
{
  "domainStrategy": "IPOnDemand",
  "rules": [
    {
      "type": "domain",
      "domain": "*.chatgpt.com",
      "outboundTag": "us1"
    },
    {
      "type": "domain",
      "domain": "*.netflix.com",
      "outboundTag": "us1"
    }
  ]
}
```

配置`custom_outbound.json`

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
              "user": "your_username",
              "pass": "your_password"
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
