### Liunx文件管理器

首先在`home`目录下创建`settings.json`配置文件

```
{
  "address": "0.0.0.0",
  "port": 7777,
  "log": "",
  "database": "/home/filebrowser.db",
  "root": "/",
  "auth": true,
  "users": [
    {
      "username": "admin",
      "password": "admin",
      "hash_method": "bcrypt"
    }
  ],
  "commands": []
}
```

启动：
```
docker run \
    -v /:/srv \
    -v /home/filebrowser.db:/database/filebrowser.db \
    -v /home/settings.json:/config/settings.json \
    -e PUID=$(id -u) \
    -e PGID=$(id -g) \
    -p 7777:80 \
    filebrowser/filebrowser:s6
```

使用`ip:7777`进入文件管理器，账户名为`admin`密码为`admin`

设置里增加文件管理命令`unzip tar chmod`
