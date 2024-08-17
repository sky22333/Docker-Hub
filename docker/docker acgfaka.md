## docker部署异次元发卡

#### 拉取源码
```
git clone https://github.com/sky22333/acg-faka.git
```
#### 一键编译启动
```
cd acg-faka
```
```
docker compose up -d
```

#### 配置

`http://你的IP:9000`进入网站，后台路径为`/admin`


```
数据库地址：mysql
数据库名称：acgfakadb
数据库账号：acgfakauser
数据库密码：acgfakapassword
```



#### 更改后台路径

异次元目录`/kernel/kernel.php`，21行 
```
 } elseif (trim($_GET['s'], "/") == 'admin') {
PHP
```
修改`admin`

#### 退出插件市场账号
```
/config/store.php
```


#### 启动插件后自动取消

一般是是服务器时间不同步的问题

打开`/etc/php/8.1/fpm/php.ini`配置文件`php.ini`修正下面的配置，保存并重启

`date.timezone = Asia/Shanghai`

重启
```
sudo systemctl restart php8.1-fpm
```
