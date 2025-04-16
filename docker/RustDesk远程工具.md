RustDesk是一个开源的远程工具，[项目地址](https://github.com/rustdesk/rustdesk)


自建服务端
```
services:
  rustdesk-server:
    container_name: rustdesk-server
    ports:
      - 21115:21115
      - 21116:21116
      - 21116:21116/udp
      - 21117:21117
      - 21118:21118
      - 21119:21119
    image: rustdesk/rustdesk-server-s6:latest
    environment:
      - "RELAY=域名:21117" #域名不可开启小云朵,只是伪装IP使用
      - "ENCRYPTED_ONLY=1"
    volumes:
      - ./data:/data
    restart: unless-stopped
```


点击 ID 右侧的菜单按钮如下，选择“ ID/中继服务器”。

ID/中继服务器ID/中继服务器

ID 服务器:域名:21116 #域名不可开启小云朵,只是伪装IP使用

中继服务器:域名:21117 #域名不可开启小云朵,只是伪装IP使用

Key

第一次运行时，会自动产生一对加密私钥和公钥（分别位于运行目录下的id_ed25519和id_ed25519.pub文件中），其主要用途是为了通讯加密。

如果您在上一步骤中没有填写Key:(公钥文件id_ed25519.pub中的内容)，将无法连接。

日志中可以看到 Key信息

查看日志：`docker logs rustdesk-server`
