### SSH隧道反向代理

适用于简单SSH隧道内网穿透

- 修改`/etc/ssh/sshd_config`文件允许远程转发
```
AllowTcpForwarding yes
GatewayPorts yes
```
- 重启SSH服务
```
sudo systemctl restart sshd
```

---

- 本地客户端
```
ssh -R 80:localhost:8080 root@服务器地址 -p 22
```

这会将服务器的`80`端口转发到本地的`8080`端口，通过SSH隧道的方式

- 客户端win系统使用后台进程的方式运行
```
Start-Process -NoNewWindow -FilePath "ssh" -ArgumentList "-R 80:localhost:8080 root@服务器地址 -p 22"
```

- 客户端liunx系统使用后台服务的方式运行

创建一个`ssh-tunnel.service`文件，位于`/etc/systemd/system/`目录下：

```
sudo vim /etc/systemd/system/ssh-tunnel.service
```

```
Description=SSH Tunnel Service
After=network.target

[Service]
ExecStart=/usr/bin/ssh -R 80:localhost:8080 root@服务器地址 -p 22
Restart=always
User=root
# 或者根据需要修改为你的用户

[Install]
WantedBy=multi-user.target
```

```
sudo systemctl daemon-reload
sudo systemctl enable ssh-tunnel.service
sudo systemctl start ssh-tunnel.service
```
