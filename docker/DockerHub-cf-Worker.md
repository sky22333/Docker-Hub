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

#### 点击左侧按钮创建一个`docker.html`文件并放入以下代码
```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <title>Docker Hub 镜像加速</title>
        <style>
        html {
        height: 100%;
        }
        body {
        font-family: "Roboto", "Helvetica", "Arial", sans-serif;
        font-size: 16px;
        color: #333;
        margin: 0;
        padding: 0;
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        }
        .container {
            margin: 0 auto;
            max-width: 600px;
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
        }
        .footer {
            background-color: #f2f2f2;
            padding: 10px;
            text-align: center;
            font-size: 14px;
        }
        pre {
            background-color: #f8f8f8;
            border: 1px solid #ddd;
            padding: 10px;
            overflow-x: auto;
        }
        code {
            font-family: "Courier New", Courier, monospace;
            background-color: #f8f8f8;
            padding: 2px 4px;
        }
        </style>
    </head>
    <body>
        <div class="header">
            <h1>Mirror Usage</h1>
        </div>
        <div class="container">
            <div class="content">
                <p>镜像加速说明</p>
                <p>
                    为了加速镜像拉取,你可以使用以下命令设置 registry mirror:
                </p>
                <pre><code>
sudo tee /etc/docker/daemon.json <<EOF
{
    "registry-mirrors": ["https://{{host}}"]
}
EOF
                </code></pre>
                <br/>
                <p>
                    为了避免 Worker 用量耗尽，你可以手动 pull 镜像然后 re-tag 之后 push 至本地镜像仓库。
                </p>
            </div>
        </div>
        <div class="footer">
            <p>自用 Docker Hub 镜像加速</p>
        </div>
    </body>
</html>
```
接下来，点击右上角的 部署，稍等片刻

最后，返回面板，在 设置，触发器 处设置一个自己的域名，一切就大功告成了
不建议使用自带的 workers.dev 的域名，被墙了



