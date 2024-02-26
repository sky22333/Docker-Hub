###  alist网盘挂载

```
docker run -d --restart=unless-stopped -v /etc/alist:/opt/alist/data -p 54321:5244 -e PUID=0 -e PGID=0 -e UMASK=022 --name="alist" xhofe/alist:latest
```


##### 设置密码

```
docker exec -it alist ./alist admin set 你的密码
```

进入方式为：`ip:54321`

用户名为：`admin`

端口可自行修改

管理路径为`/@manage`



### 美化

替换自定义头部代码

```
<!-- 引入字符串替换功能的 JavaScript Polyfill -->
<script src="https://polyfill.alicdn.com/v3/polyfill.min.js?features=String.prototype.replaceAll"></script>

<style type="text/css">
    /* 图标和按钮样式设置 */
    .hope-icon {
        color: #393939 !important; /* 设置所有图标颜色为黑色 */
    }

    .hope-button {
        color: #fff !important; /* 设置按钮字体为白色 */
        background-color: #393939 !important; /* 设置按钮背景为黑色 */
    }

    /* 特定元素的背景颜色设置 */
    .hope-ui-dark .hope-c-ivMHWx-hZistB-cv.hope-c-PJLV.hope-c-PJLV-iikaotv-css {
        background-color: #202425 !important; /* 设置特定元素在暗色模式下的背景色 */
    }

    /* 一些文本和图标颜色设置 */
    .hope-c-PJLV-ijzCLcW-css,
    .hope-c-mHASU-kFfbLQ-colorScheme-info,
    .hope-ui-dark .hope-c-ivMHWx-hZistB-cv.hope-c-PJLV.hope-c-PJLV-iikaotv-css svg {
        color: #000000 !important; /* 设置文本和图标颜色为黑色 */
    }

    .hope-c-mHASU-kFfbLQ-colorScheme-info[data-focus] {
        border-color: #000000 !important; /* 设置具有焦点的元素边框颜色为黑色 */
        box-shadow: 0 0 0 2px #ffcdd4 !important; /* 设置具有焦点的元素阴影效果 */
    }

    .hope-ui-light .hope-c-mHASU-kFfbLQ-colorScheme-info svg {
        color: white !important; /* 设置浅色模式下的特定图标颜色为白色 */
    }

    .hope-ui-dark .hope-c-mHASU-kFfbLQ-colorScheme-info svg {
        color: #000000 !important; /* 设置暗色模式下的特定图标颜色为黑色 */
    }

    /*导航条*/ 
    /*白天模式*/
    .hope-ui-light .hope-c-PJLV-idaeksS-css {
        background-color: rgba(255, 255, 255, 0.5) !important;
        border-radius: var(--hope-radii-xl) !important;
    }

    /* 吸附到页面顶部 */
    /*顶部*/
    .hope-c-PJLV-icWrYmg-css {
        background: rgba(255, 255, 255, 0) !important;
    }

    /*导航条*/
    .hope-c-PJLV-icKsjdm-css::after {
        background: rgba(255, 255, 255, 0) !important;
    }

    /*白天模式*/
    .hope-ui-light .hope-c-PJLV-icKsjdm-css {
        background-color: rgba(255, 255, 255, 0.5) !important;
        border-radius: var(--hope-radii-xl) !important;
    }

    /* 工具栏图标的悬停效果设置 */
    .left-toolbar-box svg:hover,
    .center-toolbar svg:hover {
        color: white !important;
        background-color: #393939;
    }

    .hope-c-PJLV-icHSmvX-css {
        border-radius: 5px;
    }

    /* 标题栏样式设置 */
    .hope-ui-light .hope-breadcrumb__list {
        border-radius: 10px !important;
    }

    /* 搜索框样式设置 */
    .hope-c-PJLV-ihYBJPK-css {
        display: none !important; /* 隐藏搜索框 */
    }

    .hope-ui-light .hope-c-PJLV-ikEIIxw-css {
        background-color: rgba(255, 255, 255, .7); /* 设置浅色模式下搜索框的背景色和透明度 */
    }

    .hope-c-dhzjXW.hope-c-XNyZK.hope-c-PJLV.hope-c-PJLV-ijhzIfm-css:hover {
        color: #000000 !important; /* 设置特定元素悬停时的颜色为黑色 */
        background: none; /* 设置特定元素悬停时的背景为透明 */
    }

    /* 弹出框样式设置 */
    .hope-ui-light section[id*="hope-modal-cl"] {
        background-color: rgba(250, 250, 250, .75) !important; /* 设置浅色模式下弹出框的背景色和透明度 */
        backdrop-filter: blur(5px);
    }

    /* 公告板样式设置 */
    .hope-notification__list.hope-c-UdTFD.hope-c-UdTFD-jEXiZO-placement-top-end.hope-c-PJLV.hope-c-PJLV-ijhzIfm-css {
        max-width: fit-content;
    }

    /* 目录框和工具栏样式设置 */
    .hope-ui-light .hope-breadcrumb__list,
    .hope-ui-light .obj-box,
    .hope-ui-light .hope-c-PJLV-ikSuVsl-css,
    .hope-ui-light .hope-c-PJLV-ijgzmFG-css {
        background-color: rgba(255, 255, 255, .6) !important;
    }

    .footer span, .footer a:nth-of-type(1) {
        display: none;
    }

    .hope-ui-light {
        background-image: url(https://api.aixiaowai.cn/mcapi/mcapi.php); /* 设置浅色模式下的背景图（PC） */
        background-position: center;
        background-attachment: fixed;
        background-size: cover;
        background-repeat: no-repeat;
    }

    @media screen and (max-width: 960px) {
        .hope-ui-light {
            background-image: url(https://api.aixiaowai.cn/mcapi/mcapi.php); /* 设置浅色模式下的背景图（手机） */
        }
    }
</style>
```

### 看板娘

放入自定义内容部分

```
<script src="https://eqcn.ajz.miesnfu.com/wp-content/plugins/wp-3d-pony/live2dw/lib/L2Dwidget.min.js"></script>
 
<!-- 看板娘 -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/font-awesome/css/font-awesome.min.css">
<script src="https://cdn.jsdelivr.net/gh/stevenjoezhang/live2d-widget@latest/autoload.js"></script>
```

### 滚动字幕（放入元信息——顶部说明）

黑色字体

```
<marquee>
<b id="nr">(｡･∀･)ﾉﾞ嗨，欢迎来到我的小破站。帅气漂亮的小哥哥小姐姐</b> 
</marquee></div>
```

炫彩字体

```
<style>#nr{font-size:20px;margin: 0;background: -webkit-linear-gradient(left,#ffffff,#ff0000 6.25%,#ff7d00 12.5%,#ffff00 18.75%,#00ff00 25%,#00ffff 31.25%,#0000ff 37.5%,#ff00ff 43.75%,#ffff00 50%,#ff0000 56.25%,#ff7d00 62.5%,#ffff00 68.75%,#00ff00 75%,#00ffff 81.25%,#0000ff 87.5%,#ff00ff 93.75%,#ffff00 100%);-webkit-background-clip: text;-webkit-text-fill-color: transparent;background-size: 200% 100%;animation: masked-animation 2s infinite linear;}@keyframes masked-animation {0% {background-position: 0 0;}100% {background-position: -100%, 0;}}</style><div style="background-color:#333;border-radius:25px;box-shadow:0px 0px 5px #f200ff;padding:5px;margin-top:10px;margin-bottom:0px;"><marquee>
<b id="nr">(｡･∀･)ﾉﾞ嗨，欢迎来到我的小破站。帅气漂亮的小哥哥小姐姐</b> </marquee></div>
```

### ICP备案
```
<span style="width:300px;margin:0 auto; padding:20px 0;">
    <a target="_blank" href="https://beian.miit.gov.cn/"
       style="display:inline-block;text-decoration:none;height:20px;line-height:20px;">
        <p style="height:20px;line-height:20px;margin: 0px 0px 0px 5px; color:#939393;">
            ICP备案号：实际的备案号
        </p>
    </a>
</span>
```

### 底部美化

```
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
<style>
.hope-c-PJLV-ikSuVsl-css {
    background: none!important;
}
.markdown-body a {
    color: #000!important;
}
.hope-ui-dark .markdown-body a {
    color: #fff!important;
}
.copyright .link {
    padding: 4px;
    background: rgba(0, 0, 0, 0.3); /* 透明的浅黑色 */
    border-radius: 0 8px 8px 0;
}
.copyright .name {
    padding: 4px;
    background: transparent; /* 或者使用 background: rgba(0, 0, 0, 0); */
    border-radius: 8px 0 0 8px;
    color: #000;
}
.copyright {
    padding: 50px;
    background: rgba(0, 0, 0, 0)!important; /* 透明的浅黑色 */
}
.copyright a {
    color: #fff;
}
br.phone,
br.pad {
    display: none;
}
@media (max-width: 891px) {
    br.pad {
        display: block;
    }
}
@media (max-width: 561px) {
    br.phone {
        display: block;
    }
}
.hope-c-PJLV-ieESZju-css {
    display: none;
}
img.hope-c-PJLV-ibwASZs-css {
    width: auto;
}
.hope-ui-dark .copyright .name {
    background: #000;
}
.runtime {
    margin-top: 20px;
    color: #fff;
    text-align: right!important;
}
.about,
.state {
    width: min(99%, 980px);
    text-align: center;
    padding-inline: 2%;
}
.state {
    margin-top: 20px;
    color: #fff;
}
</style>
```

底部美化的自定义内容：

```
<div class="copyright" align="center"><div class="about"><p>
<span class="name">© 2020-2023</span><span class="link"><a href="https://iymark.com">冰沫记</a></span><br class="phone"><br class="phone">
<span class="name">粤ICP备</span><span class="link"><a href="https://beian.miit.gov.cn/" target="_blank" rel="noopener nofollow noopener">2020085709号</a></span><br class="pad"><br class="pad">
<span class="name">管理员</span><span class="link"><a href="https://pan.iymark.com/@manage" target="_blank" rel="noopener">古哥</a></span><br class="phone"><br class="phone">
<span class="name">云存储合作</span><span class="link"><a href="https://www.upyun.com/?utm_source=lianmeng&utm_medium=referral" target="_blank" rel="noopener nofollow noopener">又拍云</a></span><br class="phone"><br class="phone">
<span class="name">服务器提供</span><span class="link"><a href="https://www.aliyun.com/activity?userCode=wq3yosh9" target="_blank" rel="noopener nofollow noopener">阿里云</a></span>
</p>
<div class="runtime">
<span id="runtime_span"></span>
<script type="text/javascript">
    function show_runtime() {
        window.setTimeout("show_runtime()", 1000);
        X = new Date("2/24/2023 00:00:00");
        Y = new Date();
        T = (Y.getTime() - X.getTime());
        M = 24 * 60 * 60 * 1000;
        a = T / M;
        A = Math.floor(a);
        b = (a - A) * 24;
        B = Math.floor(b);
        c = (b - B) * 60;
        C = Math.floor((b - B) * 60);
        D = Math.floor((c - C) * 60);
        runtime_span.innerHTML = "<span class=\"name\">稳定运行" + A + "天</span><span class=\"link\">" + B + "时" + C + "分" + D + "秒</span>"
    }
    show_runtime();
</script>
</div>
</div>
<div class="state"><p>免责声明：本站为个人网盘，网盘所发布的一切影视、源代码、注册信息及软件等资源仅限用于学习和研究目的</p></div>
</div>
```
