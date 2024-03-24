## Docker部署typecho



```
docker run --name typecho-server -p 8080:80 -e TYPECHO_SITE_URL=https://your-domain.com -d joyqi/typecho:nightly-php7.4-apache
```

`https://your-domain.com`替换为你的网址，提前反代好。

重置后台管理员账户：删除站点目录的`config.inc.php`文件，然后重新安装选择保留原有数据库即可

#### 主题文件目录`usr/themes`
