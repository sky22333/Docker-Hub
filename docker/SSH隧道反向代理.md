### SSH隧道反向代理


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

- 客户端命令
```
ssh -R 80:localhost:8080 root@服务器地址 -p 22
```

这会将服务器的`80`端口转发到本地的`8080`端口，通过SSH隧道的方式
