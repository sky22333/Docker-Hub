## Workers或Pages部署 代理并加速 Docker Hub 的镜像拉取请求




#### 将 `worker.js` 的内容替换为下面内容
```
const DOCKER_REGISTRY = 'https://registry-1.docker.io'

addEventListener('fetch', event => {
  event.passThroughOnException();
  event.respondWith(handleRequest(event.request));
});

async function handleRequest(request) {
  const url = new URL(request.url);
  const host = url.host;
  const path = url.pathname;

  try {
    // 1. 根路径请求时，返回 HTML 页面
    if (path === '/') {
      const HTML = `<!DOCTYPE html><html><body><h1>Docker Hub</h1></body></html>`;
      return new Response(HTML, {
        headers: {
          'content-type': 'text/html;charset=UTF-8',
        },
      });
    }

    // 2. 对 /v2/ 请求返回认证挑战信息
    if (path === '/v2/') {
      return challenge(DOCKER_REGISTRY, host);
    }

    // 3. 对 Token 请求进行处理
    if (path === '/auth/token') {
      return getToken(url);
    }

    // 4. 检查路径是否缺少默认的命名空间
    const parts = path.split('/');
    if (parts.length === 5) {
      // 在仓库名称前插入 "library"
      parts.splice(2, 0, 'library');
      // 构造新的 URL，使用访问时的域名 host 代替固定的 PROXY_REGISTRY
      const newUrl = new URL(`https://${host}`);
      newUrl.pathname = parts.join('/');
      return Response.redirect(newUrl.toString(), 301);
    }

    // 5. 默认转发请求到上游 Docker Registry
    return getData(DOCKER_REGISTRY, request);

  } catch (error) {
    // 捕获并处理异常
    return new Response('Internal Server Error', { status: 500 });
  }
}

/**
 * 返回带有 WWW-Authenticate 挑战头的响应， 用于引导客户端按照 Bearer 认证流程获取 Token
 */
async function challenge(upstream, host) {
  // 请求上游 /v2/ 端点
  const url = new URL(`${upstream}/v2/`);
  const upstreamResponse = await fetch(url);
  const responseBody = await upstreamResponse.text();

  // 构造新的响应头，设置 WWW-Authenticate 挑战信息
  const headers = new Headers();
  headers.set(
    'WWW-Authenticate',
    `Bearer realm="https://${host}/auth/token",service="docker-proxy-worker"`
  );

  return new Response(responseBody, {
    status: upstreamResponse.status,
    statusText: upstreamResponse.statusText,
    headers,
  });
}

/**
 * 处理 Token 请求，调用 Docker 授权服务获取认证 Token
 */
async function getToken(originUrl) {
  const scope = processScope(originUrl);
  
  // 构造 Docker 授权服务的请求 URL
  const tokenUrl = new URL('https://auth.docker.io/token');
  tokenUrl.searchParams.set('service', 'registry.docker.io');
  tokenUrl.searchParams.set('scope', scope);

  return fetch(tokenUrl);
}

/**
 * 代理请求到上游 Docker Registry
 */
async function getData(upstream, req) {
  const originUrl = new URL(req.url);
  const targetUrl = new URL(`${upstream}${originUrl.pathname}`);

  // 创建新的请求对象，保留原始方法和请求头，并设置重定向策略为手动
  const headers = new Headers(req.headers);
  const proxyRequest = new Request(targetUrl, {
    method: req.method,
    headers,
    redirect: 'manual',
  });

  return fetch(proxyRequest);
}

/**
 * 处理 scope 参数，确保仓库名称中包含默认的 "library" 命名空间
 */
function processScope(url) {
  let scope = url.searchParams.get('scope') || '';
  
  if (!scope) return ''; // 如果没有 scope 参数，直接返回空字符串
  
  const parts = scope.split(':');
  if (parts.length === 3 && !parts[1].includes('/')) {
    parts[1] = 'library/' + parts[1];
    scope = parts.join(':');
  }
  return scope;
}
```

<details>
<summary>前端黑色风格</summary>

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

</details>

---

<details>
<summary>前端白色风格</summary>

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
### 使用caddy反代加速docker hub

#### `Caddyfile`配置
```
hub.example.com {
    handle /v2/* {
        reverse_proxy https://registry-1.docker.io {
            header_up Host {http.reverse_proxy.upstream.hostport}
            header_down WWW-Authenticate "https://auth.docker.io" "https://{http.request.host}"
            header_down Location "https://production.cloudflare.docker.com" "https://{http.request.host}"
        }
    }

    handle /token* {
        reverse_proxy https://auth.docker.io {
            header_up Host {http.reverse_proxy.upstream.hostport}
        }
    }

    handle /* {
        reverse_proxy https://production.cloudflare.docker.com {
            header_up Host {http.reverse_proxy.upstream.hostport}
        }
    }
}
```





#### 反代`ghcr.io`
```
example.com {
    handle /v2/* {
        reverse_proxy https://ghcr.io {
            header_up Host {http.reverse_proxy.upstream.hostport}
            header_down WWW-Authenticate "https://ghcr.io" "https://{http.request.host}"
            header_down Location "https://pkg-containers.githubusercontent.com" "https://{http.request.host}"
        }
    }

    handle /token* {
        reverse_proxy https://ghcr.io {
            header_up Host {http.reverse_proxy.upstream.hostport}
        }
    }

    handle /* {
        reverse_proxy https://pkg-containers.githubusercontent.com {
            header_up Host {http.reverse_proxy.upstream.hostport}
        }
    }
}
```


#### 反代`github`
```
example.com {
    reverse_proxy https://github.com {
        header_up Host github.com
        header_up X-Real-IP {remote}
        header_up X-Forwarded-For {remote}
        header_up X-Forwarded-Proto {scheme}
    }

    tls {
        protocols tls1.2 tls1.3
    }
}
```

- 可以直接套cdn，可以配置到一起同时反代，反代对域名有影响，慎重使用。
