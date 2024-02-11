### docker运行xray

创建配置文件`/etc/xray/config.json`

配置文件示例：
```
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "listen": "0.0.0.0",
            "port": 8080,
            "protocol": "vmess",
            "settings": {
                "clients": [
                    {
                        "id": "d6fe7c7e-dc2d-4339-aadc-e15e4d1a97d8"
                    }
                ]
            },
            "streamSettings": {
                "network": "ws",
                "security": "none",
                "wsSettings": {
                    "path": "/dockerlnmp"
                }
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "tag": "direct"
        }
    ]
}
```

运行：

```
docker run -d -p 8080:8080 --name xray --restart=always -v /etc/xray:/etc/xray teddysun/xray
```

这里的端口要与配置文件里的入站端口相同

---
---
