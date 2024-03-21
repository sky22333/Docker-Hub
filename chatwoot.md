### Docker Compose 部署一个强大的客服系统

Docker Compose：
```
version: '3'

services:
  base: &base
    build:
      context: .
      dockerfile: ./docker/Dockerfile
      args:
        BUNDLE_WITHOUT: ''
        EXECJS_RUNTIME: 'Node'
        RAILS_ENV: 'development'
        RAILS_SERVE_STATIC_FILES: 'false'
    tty: true
    stdin_open: true
    image: chatwoot:development
    env_file: .env

  rails:
    <<: *base
    build:
      context: .
      dockerfile: ./docker/dockerfiles/rails.Dockerfile
    image: chatwoot-rails:development
    volumes:
      - ./:/app:delegated
      - node_modules:/app/node_modules
      - packs:/app/public/packs
      - cache:/app/tmp/cache
      - bundle:/usr/local/bundle
    depends_on:
      - postgres
      - redis
      - webpack
      - mailhog
      - sidekiq
    ports:
      - 3000:3000
    env_file: .env
    environment:
      - WEBPACKER_DEV_SERVER_HOST=webpack
      - NODE_ENV=development
      - RAILS_ENV=development
    entrypoint: docker/entrypoints/rails.sh
    command: ["bundle", "exec", "rails", "s", "-p", "3000", "-b", "0.0.0.0"]

  sidekiq:
    <<: *base
    image: chatwoot-rails:development
    volumes:
      - ./:/app:delegated
      - node_modules:/app/node_modules
      - packs:/app/public/packs
      - cache:/app/tmp/cache
      - bundle:/usr/local/bundle
    depends_on:
      - postgres
      - redis
      - mailhog
    environment:
      - NODE_ENV=development
      - RAILS_ENV=development
    command: ["bundle", "exec", "sidekiq", "-C", "config/sidekiq.yml"]

  webpack:
    <<: *base
    build:
      context: .
      dockerfile: ./docker/dockerfiles/webpack.Dockerfile
    image: chatwoot-webpack:development
    volumes:
      - ./:/app:delegated
      - node_modules:/app/node_modules # Node modules shared across containers
      - packs:/app/public/packs
      - cache:/app/tmp/cache
      - bundle:/usr/local/bundle
    ports:
      - "3035" # Webpack dev server
    environment:
      - WEBPACKER_DEV_SERVER_HOST=0.0.0.0
      - NODE_ENV=development
      - NODE_OPTIONS=--openssl-legacy-provider
      - RAILS_ENV=development
    entrypoint: docker/entrypoints/webpack.sh
    command: bin/webpack-dev-server

  postgres:
    image: postgres:12
    restart: always
    ports:
      - '5432:5432'
    volumes:
      - postgres:/data/postgres
    environment:
      - POSTGRES_DB=chatwoot
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=

  redis:
    image: redis:alpine
    restart: always
    command: ["sh", "-c", "redis-server --requirepass \"$REDIS_PASSWORD\""]
    env_file: .env
    volumes:
      - redis:/data/redis
    ports:
      - '6379:6379'

  mailhog:
    image: mailhog/mailhog
    ports:
      - 1025:1025
      - 8025:8025

volumes:
  postgres:
  redis:
  packs:
  node_modules:
  cache:
  bundle:
```

#### .env 配置
```
# 用于验证已签名cookie的完整性。因此，请确保设置了一个安全的值
SECRET_KEY_BASE=replace_with_lengthy_secure_hex

# 替换为您计划用于应用的URL
FRONTEND_URL=http://0.0.0.0:3000
# 使用专用URL用于帮助中心页面
# HELPCENTER_URL=http://0.0.0.0:3000

# 如果设置了该变量，所有未经认证的页面将回退到默认的语言设置。
# 每当创建一个新账户时，默认语言将是 DEFAULT_LOCALE 而不是 en
# DEFAULT_LOCALE=cn

# 如果您计划使用CDN来存储您的资源，请设置资产CDN主机
ASSET_CDN_HOST=

# 强制所有访问该应用使用SSL，缺省设置为false
FORCE_SSL=false

# 这允许您控制在您的Chatwoot安装上的新注册
# true : 缺省选项，允许注册
# false : 禁用所有与注册相关的端点
# api_only: 禁用注册UI，但您可以通过账户API创建注册
ENABLE_ACCOUNT_SIGNUP=false

# Redis配置
REDIS_URL=redis://redis:6379
# 如果您在使用docker-compose，请将此变量值设置为任意字符串，
# 它将作为运行在docker-compose内的redis服务的密码，以确保安全
REDIS_PASSWORD=
# 通过传递哨兵主机和端口列表来使用Redis哨兵，例如 sentinel_host1:port1,sentinel_host2:port2
REDIS_SENTINELS=
# 使用哨兵时需要Redis哨兵主名称，默认值为"mymaster"。
# 您可以使用"SENTINEL masters"命令找到主名称列表
REDIS_SENTINEL_MASTER_NAME=

# 缺省情况下Chatwoot将把REDIS_PASSWORD作为哨兵的密码值
# 使用下面的环境变量自定义哨兵的密码。
# 如果哨兵没有配置密码，请使用空字符串
# REDIS_SENTINEL_PASSWORD=

# Heroku中修复Redis高级断裂的配置
# 启用以下配置
# 参考：https://github.com/chatwoot/chatwoot/issues/2420
# REDIS_OPENSSL_VERIFY_MODE=none

# Postgres数据库配置变量
# 您可以留空POSTGRES_DATABASE。生产环境中的数据库默认名称是chatwoot_production
# POSTGRES_DATABASE=
POSTGRES_HOST=postgres
POSTGRES_USERNAME=postgres
POSTGRES_PASSWORD=
RAILS_ENV=development
# 更改Postgres查询超时限制。默认为14秒。仅在需要时修改。
# POSTGRES_STATEMENT_TIMEOUT=14s
RAILS_MAX_THREADS=5

# 所有发出的电子邮件都将使用该邮件地址发送
# 可以使用 `email@yourdomain.com` 或 `BrandName <email@yourdomain.com>`
MAILER_SENDER_EMAIL=Chatwoot <accounts@chatwoot.com>

# 为HELO检查设置的SMTP域名密钥
SMTP_DOMAIN=chatwoot.com
# 如果在开发环境中使用docker-compose，将值设置为"mailhog"，
# 在其他环境中将值设置为"localhost"或您的SMTP地址
# 如果SMTP_ADDRESS为空，Chatwoot将尝试使用sendmail(postfix)
SMTP_ADDRESS=
SMTP_PORT=1025
SMTP_USERNAME=
SMTP_PASSWORD=
# plain,login,cram_md5
SMTP_AUTHENTICATION=
SMTP_ENABLE_STARTTLS_AUTO=true
# 可以是: 'none', 'peer', 'client_once', 'fail_if_no_peer_cert'，见 http://api.rubyonrails.org/classes/ActionMailer/Base.html
SMTP_OPENSSL_VERIFY_MODE=peer
# 如果您的SMTP服务器需要，请注释掉以下环境变量
# SMTP_TLS=
# SMTP_SSL=
# 邮件接收
# 当启用会话持续性时设置回复邮件的域
MAILER_INBOUND_EMAIL_DOMAIN=
# 根据接收邮件的入口通道设置此值
# 可能的值包括：
# relay 对于 Exim、Postfix、Qmail
# mailgun 对于 Mailgun
# mandrill 对于 Mandrill
# postmark 对于 Postmark
# sendgrid 对于 Sendgrid
RAILS_INBOUND_EMAIL_SERVICE=
# 根据邮件入口服务使用以下之一
# 参考：https://edgeguides.rubyonrails.org/action_mailbox_basics.html
# 将此设置为您选择的密码，并在入站Webhook中使用
RAILS_INBOUND_EMAIL_PASSWORD=

# 存储
ACTIVE_STORAGE_SERVICE=local

# 亚马逊 S3
# 文档：https://www.chatwoot.com/docs/configuring-s3-bucket-as-cloud-storage
S3_BUCKET_NAME=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_REGION=

# 日志设置
# 如果您希望将日志写入文件，请禁用
RAILS_LOG_TO_STDOUT=true
LOG_LEVEL=info
LOG_SIZE=500
# 如果您想使用lograge而不是rails logger，请配置此环境变量
#LOGRAGE_ENABLED=true

### 以下环境变量仅在您设置社交媒体频道时需要

# Facebook
# 文档：https://www.chatwoot.com/docs/facebook-setup
FB_VERIFY_TOKEN=
FB_APP_SECRET=
FB_APP_ID=
# Twitter
# 文档：https://www.chatwoot.com/docs/twitter-app-setup
TWITTER_APP_ID=
TWITTER_CONSUMER_KEY=
TWITTER_CONSUMER_SECRET=
TWITTER_ENVIRONMENT=

# Slack集成
SLACK_CLIENT_ID=
SLACK_CLIENT_SECRET=

# Google OAuth
GOOGLE_OAUTH_CLIENT_ID=
GOOGLE_OAUTH_CLIENT_SECRET=
GOOGLE_OAUTH_CALLBACK_URL=

# 如果您正在使用定制构建的移动应用，更改此环境变量
# 移动应用环境变量
IOS_APP_ID=L7YLMN4634.com.chatwoot.app
ANDROID_BUNDLE_ID=com.chatwoot.app

# 为移动应用配置
# FCM服务器密钥

# 性能和错误监控配置
# Elastic APM
# Sentry
# LogRocket
# Microsoft Clarity
# Google Tag Manager

# 环境配置结束
```


#### [Docker部署教程](https://www.chatwoot.com/docs/self-hosted/deployment/docker)

#### [集成到网页的教程](https://www.chatwoot.com/docs/product/channels/live-chat/create-website-channel)

#### [环境变量教程](https://www.chatwoot.com/docs/self-hosted/configuration/environment-variables#the-env-file)
