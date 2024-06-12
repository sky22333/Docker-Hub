## 代理并加速 Docker Hub 的镜像拉取请求



> [示例站点](https://do.nark.eu.org/)

#### 将 `worker.js` 的内容替换为下面内容
```
import HTML from './docker.html';

export default {
    async fetch(request) {
        const url = new URL(request.url);
        const path = url.pathname;
        const originalHost = request.headers.get("host");
        const registryHost = "registry-1.docker.io";

        if (path.startsWith("/v2/")) {
        const headers = new Headers(request.headers);
        headers.set("host", registryHost);

        const registryUrl = `https://${registryHost}${path}`;
        const registryRequest = new Request(registryUrl, {
            method: request.method,
            headers: headers,
            body: request.body,
            // redirect: "manual",
            redirect: "follow",
        });

        const registryResponse = await fetch(registryRequest);

        console.log(registryResponse.status);

        const responseHeaders = new Headers(registryResponse.headers);
        responseHeaders.set("access-control-allow-origin", originalHost);
        responseHeaders.set("access-control-allow-headers", "Authorization");
        return new Response(registryResponse.body, {
            status: registryResponse.status,
            statusText: registryResponse.statusText,
            headers: responseHeaders,
        });
        } else {
        return new Response(HTML.replace(/{{host}}/g, originalHost), {
            status: 200,
            headers: {
            "content-type": "text/html"
            }
        });
        }
    }
}
```

#### 点击左侧按钮创建一个`docker.html`文件并放入以下代码—暗黑样式
```
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Docker镜像加速站</title>
<style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            line-height: 1.6;
            color: #3d3d3d;
            margin: 0;
            padding: 20px;
            background: #242424;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background: rgba(20, 19, 19, 0.8);
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }
        h1, h2, h3 {
            color: #ece6ec;
        }
        p {
            margin-bottom: 1em;
            color: #f8f8f2;
        }
        pre {
            background: #2d2d2d;
            color: #f8f8f2;
            padding: 20px;
            border-radius: 8px;
            overflow-x: auto;
            position: relative;
        }
        pre::before {
            content: " ";
            display: block;
            position: absolute;
            top: 10px;
            left: 10px;
            width: 12px;
            height: 12px;
            background: #ff5f56;
            border-radius: 50%;
            box-shadow: 20px 0 0 #ffbd2e, 40px 0 0 #27c93f;
        }
        code {
            font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, Courier, monospace;
            font-size: 0.875em;
        }
        .copy-button {
            position: absolute;
            top: 10px;
            right: 10px;
            background: #007aff;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
            opacity: 0;
            transition: opacity 0.3s;
        }
        pre:hover .copy-button {
            opacity: 1;
        }
        .footer {
            text-align: center;
            margin-top: 20px;
            color: #ece6ec;
            font-size: 0.875em;
        }
        .footer a {
            color: #007aff;
            text-decoration: none;
        }
        .footer a:hover {
            text-decoration: underline;
        }
    </style>
</head>
<body>
<div class="container">
<center><h2>自用 Docker Hub 镜像加速</h2></center>
<h3>为了加速镜像拉取，使用以下命令设置<b>registry mirror</b></h3>
<pre><code>
sudo mkdir -p /etc/docker</code><button class="copy-button" onclick="copyCode(this)">复制代码</button></pre>
<pre><code>
sudo tee /etc/docker/daemon.json &lt;&lt;EOF
{
    "registry-mirrors": ["https://{{host}}"]
}
EOF</code><button class="copy-button" onclick="copyCode(this)">复制代码</button></pre>
<pre><code>
sudo systemctl daemon-reload</code><button class="copy-button" onclick="copyCode(this)">复制代码</button></pre>
<pre><code>
sudo systemctl restart docker</code><button class="copy-button" onclick="copyCode(this)">复制代码</button></pre>
<p><h3>不用设置环境也可以直接使用，用法示例：</h3></p>
<pre><code>
docker pull {{host}}/library/mysql:5.7</code><button class="copy-button" onclick="copyCode(this)">复制代码</button></pre>
<p>说明：library是一个特殊的命名空间，它代表的是官方镜像。如果是某个用户的镜像就把library替换为镜像的用户名</p>
</div>
<div class="footer">
    &copy; 自建站点，请勿滥用。<a href="https://hub.docker.com">Docker加速</a>
</div>
<script>
        function copyCode(button) {
            const code = button.previousSibling;
            const textArea = document.createElement('textarea');
            textArea.value = code.textContent;
            document.body.appendChild(textArea);
            textArea.select();
            document.execCommand('copy');
            document.body.removeChild(textArea);
            button.textContent = '已复制';
            setTimeout(() => {
                button.textContent = '复制代码';
            }, 2000);
        }
    </script>
</body>
</html>
```
接下来，点击右上角的 部署，稍等片刻

最后，返回面板，在 设置，触发器 处设置一个自己的域名，一切就大功告成了
不建议使用自带的 workers.dev 的域名，被墙了


---
<details>
<summary>主页样式二——白色样式</summary>

#### 主页样式二
```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <title>Docker Hub 镜像加速</title>
        <style>
        html, body {
            height: 100%;
        }
        body {
            font-family: "Roboto", "Helvetica", "Arial", sans-serif;
            font-size: 16px;
            color: #333;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
        }
        .container {
            margin: 0 auto;
            max-width: 600px;
            flex-grow: 1;
            display: flex;
            flex-direction: column;
        }
        .header {
            background-color: #438cf8;
            color: white;
            padding: 10px;
            display: flex;
            align-items: center;
        }
        h1 {
            font-size: 24px;
            margin: 0;
            padding: 0;
        }
        .content {
            padding: 32px;
            flex-grow: 1;
        }
        pre {
            background-color: #f4f4f4;
            padding: 16px;
            border-radius: 4px;
            position: relative;
            overflow: auto;
            margin-bottom: 16px;
        }
        code {
            display: block;
            white-space: pre-wrap;
            word-wrap: break-word;
        }
        .copy-button {
            position: absolute;
            top: 8px;
            right: 8px;
            padding: 4px 8px;
            background-color: #438cf8;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
        }
        .footer {
            padding: 5px;
            text-align: center;
            font-size: 15px;
        }
        .footer a {
            color: #438cf8;
            text-decoration: none;
        }
        </style>
        <script>
            function copyCode(button) {
                const code = button.previousElementSibling.innerText;
                navigator.clipboard.writeText(code).then(function() {
                    button.innerText = "已复制";
                    setTimeout(function() {
                        button.innerText = "复制";
                    }, 2000);
                });
            }
        </script>
    </head>
    <body>
        <div class="header">
            <h1>Docker Hub 镜像加速</h1>
        </div>
        <div class="container">
            <div class="content">
                <h2>Docker Hub 镜像加速</h2>
                <p>为了加速镜像拉取，你可以使用以下命令设置 registry mirror</p>
                <pre><code>sudo mkdir -p /etc/docker</code><button class="copy-button" onclick="copyCode(this)">复制</button></pre>
                <pre><code>sudo tee /etc/docker/daemon.json &lt;&lt;EOF
{
    "registry-mirrors": ["https://{{host}}"]
}
EOF</code><button class="copy-button" onclick="copyCode(this)">复制</button></pre>
                <pre><code>sudo systemctl daemon-reload</code><button class="copy-button" onclick="copyCode(this)">复制</button></pre>
                <pre><code>sudo systemctl restart docker</code><button class="copy-button" onclick="copyCode(this)">复制</button></pre>
                <br>
                <p>不用设置环境也可以直接使用，用法示例：</p>
                <pre><code>docker pull {{host}}/library/mysql:5.7</code><button class="copy-button" onclick="copyCode(this)">复制</button></pre>
                <p>说明：library是一个特殊的命名空间，它代表的是官方镜像。如果是某个用户的镜像就把library替换为镜像的用户名</p>
            </div>
        </div>
        <div class="footer">
            <p><a href="https://hub.docker.com" target="_blank">Docker Hub 镜像加速</a></p>
        </div>
    </body>
</html>
```


</details>


---
### 使用nginx反代加速docker hub


#### 安装nginx
```
sudo apt update && sudo apt install -y nginx && sudo systemctl stop nginx
```

申请证书后并放入下面的配置


#### `/etc/nginx/nginx.conf`配置
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 4096;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;

    server {
        listen 80;
        server_name 你的域名;

        location / {
            return 301 https://$host$request_uri;
        }
    }

    server {
        listen 443 ssl;
        server_name 你的域名;

        ssl_certificate /root/.acme.sh/你的域名_ecc/fullchain.cer;
        ssl_certificate_key /root/.acme.sh/你的域名_ecc/你的域名.key;

        ssl_session_timeout 24h;
        ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;

        location / {
            proxy_pass https://registry-1.docker.io;
            proxy_set_header Host registry-1.docker.io;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            proxy_buffering off;
            proxy_set_header Authorization $http_authorization;
            proxy_pass_header Authorization;

            proxy_intercept_errors on;
            recursive_error_pages on;
            error_page 301 302 307 = @handle_redirect;
        }

        location @handle_redirect {
            resolver 1.1.1.1;
            set $saved_redirect_location $upstream_http_location;
            proxy_pass $saved_redirect_location;
        }
    }
}
```

#### 启动nginx
```
sudo nginx -s reload
```
没有报错就说明配置已经生效


设置开机自启
```
sudo systemctl enable nginx
```
