### docker-compose部署mc服务端

```
services:
  minecraft:
    image: itzg/minecraft-server
    environment:
      EULA: "true"
      TYPE: "FORGE"  # 可选使用 Forge 服务器
      VERSION: "1.20.1"  # 可选指定 Minecraft 版本
    ports:
      - "25565:25565"
    volumes:
      - ./data:/data
    stdin_open: true
    tty: true
    restart: always
```


- 更多变量

### 1. **基础配置**
| 变量名               | 默认值       | 说明                                                                 |
|----------------------|--------------|----------------------------------------------------------------------|
| `EULA`               | `"false"`    | 必须设置为 `"true"` 以接受 Minecraft 的最终用户许可协议（EULA）。     |
| `TYPE`               | `"VANILLA"`  | 服务器类型，例如 `"FORGE"`、`"FABRIC"`、`"SPIGOT"` 等。              |
| `VERSION`            | `"LATEST"`   | Minecraft 版本，例如 `"1.20.1"`。                                    |
| `MEMORY`             | `"1G"`       | 分配给服务器的内存大小，例如 `"2G"` 或 `"4G"`。                      |
| `JVM_OPTS`           | `""`         | 自定义 JVM 参数，例如 `"-XX:+UseG1GC"`。                             |
| `JVM_XX_OPTS`        | `""`         | 自定义 JVM 的 `-XX` 参数，例如 `"-XX:+UseStringDeduplication"`。     |

### 2. **服务器类型相关**
| 变量名               | 默认值       | 说明                                                                 |
|----------------------|--------------|----------------------------------------------------------------------|
| `FORGE_VERSION`      | `""`         | 指定 Forge 版本，例如 `"47.3.0"`。                                  |
| `FABRIC_VERSION`     | `""`         | 指定 Fabric 版本，例如 `"0.14.22"`。                                |
| `SPIGOT_DOWNLOAD_URL`| `""`         | 自定义 Spigot 的下载 URL。                                          |

### 3. **网络和连接**
| 变量名               | 默认值       | 说明                                                                 |
|----------------------|--------------|----------------------------------------------------------------------|
| `SERVER_NAME`        | `"Minecraft Server"` | 服务器名称，显示在客户端服务器列表中。                              |
| `MOTD`               | `"A Minecraft Server"` | 服务器欢迎信息（MOTD）。                                           |
| `ONLINE_MODE`        | `"true"`     | 是否启用正版验证，设置为 `"false"` 允许非正版玩家连接。              |
| `MAX_PLAYERS`        | `"20"`       | 服务器最大玩家数量。                                                |
| `VIEW_DISTANCE`      | `"10"`       | 玩家视野距离（区块数）。                                            |
| `ENABLE_RCON`        | `"false"`    | 是否启用 RCON（远程控制）。                                         |
| `RCON_PASSWORD`      | `""`         | RCON 密码。                                                         |
| `RCON_PORT`          | `"25575"`    | RCON 端口。                                                         |

### 4. **世界和游戏设置**
| 变量名               | 默认值       | 说明                                                                 |
|----------------------|--------------|----------------------------------------------------------------------|
| `LEVEL`              | `"world"`    | 世界名称。                                                          |
| `LEVEL_TYPE`         | `"default"`  | 世界类型，例如 `"flat"`、`"largebiomes"`。                          |
| `GAMEMODE`           | `"survival"` | 默认游戏模式，例如 `"creative"`、`"adventure"`。                    |
| `DIFFICULTY`         | `"easy"`     | 游戏难度，例如 `"normal"`、`"hard"`。                               |
| `ALLOW_FLIGHT`       | `"false"`    | 是否允许飞行。                                                      |
| `ALLOW_NETHER`       | `"true"`     | 是否允许下界传送门。                                                |
| `SPAWN_ANIMALS`      | `"true"`     | 是否生成动物。                                                      |
| `SPAWN_MONSTERS`     | `"true"`     | 是否生成怪物。                                                      |
| `SPAWN_NPCS`         | `"true"`     | 是否生成村民。                                                      |
| `GENERATE_STRUCTURES`| `"true"`     | 是否生成结构（如村庄、神庙）。                                      |

### 5. **插件和模组**
| 变量名               | 默认值       | 说明                                                                 |
|----------------------|--------------|----------------------------------------------------------------------|
| `MODS`               | `""`         | 自动下载并安装模组的 URL 列表（以逗号分隔）。                        |
| `REMOVE_OLD_MODS`    | `"false"`    | 是否在启动时删除旧的模组文件。                                      |
| `REMOVE_OLD_MODS_INCLUDE` | `""`    | 指定要删除的模组文件（支持通配符）。                                |
| `REMOVE_OLD_MODS_EXCLUDE` | `""`    | 指定要保留的模组文件（支持通配符）。                                |

### 6. **备份和日志**
| 变量名               | 默认值       | 说明                                                                 |
|----------------------|--------------|----------------------------------------------------------------------|
| `ENABLE_AUTOMATIC_BACKUPS` | `"false"` | 是否启用自动备份。                                                  |
| `BACKUP_INTERVAL`    | `"24h"`      | 自动备份的时间间隔。                                                |
| `BACKUP_TARGET`      | `"world"`    | 备份的目标文件夹。                                                  |
| `LOG_TIMESTAMP`      | `"false"`    | 是否在日志中添加时间戳。                                            |

### 7. **其他配置**
| 变量名               | 默认值       | 说明                                                                 |
|----------------------|--------------|----------------------------------------------------------------------|
| `OPS`                | `""`         | 管理员列表（以逗号分隔的玩家名称）。                                |
| `WHITELIST`          | `""`         | 白名单列表（以逗号分隔的玩家名称）。                                |
| `BANNED_IPS`         | `""`         | 封禁的 IP 列表（以逗号分隔）。                                      |
| `BANNED_PLAYERS`     | `""`         | 封禁的玩家列表（以逗号分隔）。                                      |
| `ENABLE_COMMAND_BLOCK` | `"false"`  | 是否启用命令方块。                                                  |
| `MAX_TICK_TIME`      | `"60000"`    | 服务器最大 tick 时间（毫秒）。                                      |
| `MAX_WORLD_SIZE`     | `"29999984"` | 最大世界大小（区块数）。                                            |
