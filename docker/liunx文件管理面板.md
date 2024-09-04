### Liunx文件管理器

```
docker run -d \
    -v /:/srv \
    -v /home:/database \
    -e PUID=$(id -u) \
    -e PGID=$(id -g) \
    -p 7777:80 \
    filebrowser/filebrowser:s6
```

使用`ip:7777`进入文件管理器，账户名为`admin`密码为`admin`

设置—用户管理—用户编辑—增加文件管理命令`unzip tar chmod`

示例`unzip you.zip` /  `chmod -R 777 home`
