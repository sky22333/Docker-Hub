### s-ui
https://github.com/admin8800/s-ui

### 3x-ui
https://github.com/MHSanaei/3x-ui/blob/main/README.zh_CN.md

#### 容器里的命令
查看面板信息
```
/app/x-ui setting -show true
```
修改用户名和密码
```
/app/x-ui setting -username 用户名 -password 密码 -resetTwoFactor true
```
修改面板路径
```
/app/x-ui setting -webBasePath "admin"
```
修改面板端口
```
/app/x-ui setting -port 端口
```
清除安全令牌
```
/app/x-ui setting -remove_secret
```
重置面板所有设置为默认值（用户名和密码除外）
```
/app/x-ui setting -reset
```
重启容器
```
docker restart 3x-ui
```

#### 127.0.0.1:62789监听失败是因为服务器没有配置本地环回地址
查看
```
ip addr show lo
```
分配
```
sudo ip addr add 127.0.0.1/8 dev lo
```

---
### Reality域名推荐列表
```
addons.mozilla.org
s0.awsstatic.com
d1.awsstatic.com
m.media-amazon.com
www.amazon.com

player.live-video.net
one-piece.com
www.lovelive-anime.jp
www.swift.com
academy.nvidia.com
www.cisco.com
update.microsoft
www.tesla.com
slack.com
www.xbox.com
www.icloud.com
```

### cf优选域名推荐列表
```
www.wetest.vip

# 稳定大厂域名
unpkg.com
www.visa.com.sg
www.okcupid.com
www.udemy.com
visa.cn
```

### 反向代理连接内网家宽

- 公网服务器部署`3xui`并创建`2`个节点，例如一个`9988`端口，一个`19988`端口，基本模板-阻止配置-关掉私人IP
- 
- 家宽内网环境部署`3xui`创建一个出站，填入服务端的`19988`端口的节点，然后添加反向代理配置，`互连`选择刚刚添加的`19988`端口的出站节点，`出站`选择`direct`，然后保存并重启，基本模板-阻止配置-关掉私人IP
- 
- 公网服务器的面板添加反向代理，类型选择`门户`，互连选择`19988`端口的节点，入站选择`9988`端口的节点，然后保存并重启
- 
- 然后使用代理软件连接公网服务器的`9988`端口的节点即可，此时这个节点的出站IP就是内网家宽的IP
