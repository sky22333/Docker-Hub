# acme域名证书脚本

[官方文档](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)

## 安装脚本
 **官方脚本：** 
```
curl https://get.acme.sh | sh
```


## 使用DNS申请

如果是阿里云平台：登录控制台，在 `Access Key` 管理页面创建 `Access Key` 和 `Access Secret`。将下面命令中的`dns_cf`改为`dns_aliyun`然后根据提示输入`Access Key` 和 `Access Secret`。

 **配置Cloudflare插件：** 
```
~/.acme.sh/acme.sh --set-default-ca --server letsencrypt --dns dns_cf
```

 **申请证书：**
```
~/.acme.sh/acme.sh --issue --dns dns_cf -d 你的域名
```
根据提示输入 Cloudflare 邮箱和 API密钥



## 使用HTTP方式申请
```
~/.acme.sh/acme.sh --issue --standalone -d 你的域名
```

## 安装证书
```
~/.acme.sh/acme.sh --install-cert -d 你的域名 \
  --cert-file /etc/ssl/certs/你的域名/cert.pem \
  --key-file /etc/ssl/certs/你的域名/key.pem \
  --fullchain-file /etc/ssl/certs/你的域名/fullchain.pem
```
可以在最后一行添加`--reloadcmd "service nginx reload`直接配置到nginx

 **证书将保存在`/etc/ssl/certs`目录** 

### win系统申请证书

[进入win系统acme官网](https://www.win-acme.com/)

下载程序，执行exe文件，按照提示操作
