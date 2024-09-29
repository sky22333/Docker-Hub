## certbot申请域名证书

---
---

### HTTP方式
**1：安装certbot**
```
apt update && apt install certbot -yq
```
**2：申请证书**
临时占用80端口
```
sudo certbot certonly --standalone --non-interactive --agree-tos -d example.com
```
替换`example.com`示例域名，多个`-d`可为多个域名申请

**3：查看自动续期任务**
```
sudo certbot renew --dry-run
```
默认情况下，Certbot会将证书存储在`/etc/letsencrypt/live/`目录下

---
---

### Cloudflare的DNS方式

1：`Cloudflare`进入`我的个人资料` -> `API令牌`，创建一个新的令牌，权限为：`区域——DNS——编辑`，区域资源为：`包括——特定区域——选择域名`。

2：安装DNS插件
```
sudo apt install python3-certbot-dns-cloudflare -yq
```
3：创建配置文件`cloudflare.ini`，配置文件填入`api_token`
```
dns_cloudflare_api_token = YOUR_API_TOKEN
```

4：申请
```
sudo certbot certonly --dns-cloudflare --dns-cloudflare-credentials ./cloudflare.ini --non-interactive --agree-tos -d example.com
```


---
---

### 阿里云DNS方式

1：进入`访问控制` -> `Access Keys`，创建一个`Access Key ID`和`Access Key Secret`

2：安装插件
```
sudo apt install python3-certbot-dns-aliyun -yq
```
3：配置`aliyun.ini`
```
dns_aliyun_access_key = YOUR_ACCESS_KEY_ID
dns_aliyun_secret_key = YOUR_ACCESS_KEY_SECRET
```

4：申请
```
sudo certbot certonly --dns-aliyun --dns-aliyun-credentials ./aliyun.ini --non-interactive --agree-tos -d example.com
```





---
---
---

## Docker-compose申请

```
services:
  https-portal:
    image: steveltn/https-portal
    ports:
      - '80:80'
      - '443:443'
    environment:
      DOMAINS: 'example.com'            # 你的域名
      STAGE: 'production'               # 生产环境
    volumes:
      - ./https-data:/var/lib/https-portal     # 证书存储文件
    restart: always
```


- 强制续期证书：`FORCE_RENEW: 'true'`

#### dns方式申请

```
services:
  https-portal:
    image: steveltn/https-portal
    environment:
      DOMAINS: 'example.com'          # 你的域名
      STAGE: 'production'
      DNS_PROVIDER: 'cloudflare'                   # 或 'alidns'
      CLOUDFLARE_EMAIL: 'email@example.com'        # 对于Cloudflare
      CLOUDFLARE_API_KEY: 'cloudflare-api-token'   # 对于Cloudflare
      # ALI_KEY: 'ali-access-key-id'               # 对于阿里云
      # ALI_SECRET: 'ali-access-key-secret'        # 对于阿里云
    volumes:
      - ./https-data:/var/lib/https-portal         # 证书存储文件
    restart: always
```

- 强制续期证书：`FORCE_RENEW: 'true'`
