## Docker-Compose 部署教程
本文教你如何在命令行使用docker-compose + sqlite来快速部署Xboard  
如果你需要使用Mysql，你需要自行处理好Mysql的安装。

[项目地址](https://github.com/cedar2025/Xboard/tree/dev)

### 部署 (使用docker-compose 2分钟部署)
> 在此提供Xboard安装、快速体验Xboard的步骤。   
使用docker compose + sqlite 快速部署站点（**无需安装Mysql以及redis**）
1. 安装docker
2. 获取Docker compose 文件
```
git clone -b  docker-compose --depth 1 https://github.com/cedar2025/Xboard
cd Xboard
```
3. 执行数据库安装命令
> 选择 **启用sqlite** 和 **Docker内置的Redis**
```
docker compose run -it --rm xboard php artisan xboard:install
```
> 执行这条命令之后，会返回你的后台地址和管理员账号密码（你需要记录下来）  
> 你需要执行下面的 **启动xborad** 步骤之后才能访问后台

4. 启动Xboard
```
docker compose up -d
```
> 安装完成之后即可访问你的站点
5. 访问站点 
> 启动之后网站端口默认为 `7001` , 你可以配置nginx反向代理使用80端口  

> 如果你需要使用mysql，请自行安装Mysql后重新部署

### **更新**
1. 修改版本
```
cd Xboard
vi docker-compose.yaml
```
> 修改docker-compose.yaml 当中image后面的版本号为你需要的版本  
> 如果为版本为latest 则可以忽略这一步，直接进行第二步

2. 更新数据库（可以执行多次都是安全的）
```
docker compose pull
docker compose down
docker compose run -it --rm xboard php artisan xboard:update
docker compose up -d
```
> 即可更新成功

### **回滚**
> 此回滚不回滚数据库，是否回滚数据库请查看相关文档
1. 回退版本  
```
vi docker-compose.yaml
```
> 修改docker-compose.yaml 当中image后面的版本号为更新前的版本号
2. 启动
```
dockcer compose up -d
```

### 注意
启用webman后做的任何代码修改都需要重启生效


---



---



###  审计规则：

####  正则表达式：
```
(.*\.||)(dafahao|mingjinglive|chinaaid|botanwang|xinsheng|rfi|breakgfw|chengmingmag|jinpianwang|xizang-zhiye|breakgfw|qi-gong|voachinese|mhradio|rfa|edoors|edoors|renminbao|soundofhope|zhengjian|dafahao|minghui|dongtaiwang|epochtimes|ntdtv|falundafa|wujieliulan|aboluowang|bannedbook|secretchina|dajiyuan|boxun|chinadigitaltimes|huaglad|dwnews|creaders|oneplusnews|rfa)\.(cn|com|org|net|club|net|fr|tw|hk|eu|info|me)
(.*\.||)(gov|12377|12315|talk.news.pts.org|creaders|zhuichaguoji|efcc.org|cyberpolice|aboluowang|tuidang|epochtimes|nytimes|dafahao|falundafa|minghui|falunaz|zhengjian|110.qq|mingjingnews|inmediahk|xinsheng|bannedbook|ntdtv|falungong|12321|secretchina|epochweekly|cn.rfi)\.(cn|com|org|net|club|net|fr|tw|hk|eu|info|me)
BitTorrent protocol
Private Tracker protocol
(.*.||)(gov|12377|12315|talk.news.pts.org|cread­ers|zhuich­aguoji|efcc.org|cy­ber­po­lice|abolu­owang|tu­idang|epochtimes|ny­times|zhengjian|110.qq|mingjingnews|in­medi­ahk|xin­sheng|banned­book|nt­dtv|12321|se­cretchina|epochweekly|cn.rfi).(cn|com|org|net|club|net|fr|tw|hk)
(torrent|\.torrent|peer_id=|info_hash|get_peers|find_node|BitTorrent|announce_peer|announce\.php\?passkey=)
(.?)(xunlei|sandai|Thunder|XLLiveUD)(.)
(.*\.||)(gash)\.(com|tw)
(.*\.||)(mycard)\.(com|tw)
(.+\.|^)(360|so)\.(cn|com)
(\.guanjia\.qq\.com|qqpcmgr|QQPCMGR)
(api|ps|sv|offnavi|newvector|ulog\.imap|newloc)(\.map|)\.(baidu|n\.shifen)\.com
(^.*@)(guerrillamail|guerrillamailblock|sharklasers|grr|pokemail|spam4|bccto|chacuo|027168).(info|biz|com|de|net|org|me|la)
(.*\.||)(gash)\.(com|tw)
(.*\.||)(mycard)\.(com|tw)
(.*\.||)(taobao)\.(com)
(.*\.||)(metatrader4|metatrader5|mql5)\.(org|com|net)
(.*\.||)(rising|kingsoft|duba|xindubawukong|jinshanduba)\.(com|net|org)
```


####  域名过滤：
```
# 轮子、Fake News
falundafa
minghui
epochtimes
ntdtv
voachinese
appledaily
nextdigital
dalailama
nytimes
bloomberg
independent
freetibet
citizenpowerfor
rfa.org
bbc.com
theinitium.com
tibet.net
jw.org
bannedbook.org
dw.com
storm.mg
yam.com
chinadigitaltimes
ltn.com.tw
mpweekly.com
cup.com.hk
thenewslens.com
inside.com.tw
everylittled.com
cool3c.com
taketla.zaiko.io
news.agentm.tw
sportsv.net
research.tnlmedia.com
ad2iction.com
viad.com.tw
tnlmedia.com
becomingaces.com
pincong.rocks
flipboard.com
soundofhope.org
wenxuecity.com
aboluowang.com
2047.name
shu.best
shenyunperformingarts.org
bbc.co.uk
cirosantilli
wsj.com
rfi.fr
chinapress.com.my
hancel.org
miraheze.org
zhuichaguoji.org
fawanghuihui.org
hopto.org
amnesty.org
hrw.org
irmct.org
zhengjian.org
wujieliulan.com
dongtaiwang.com
wujieliulan.com
ultrasurf.us
yibaochina.com
roc-taiwan.org
creaders.net
upmedia.mg
ydn.com.tw
udn.com
theaustralian.com.au
rfa.org
voacantonese.com
voanews.com
bitterwinter.org
christianstudy.com
learnfalungong.com
# 容易被利用于欺诈
beanfun.com
gashpoint.com
# 政府、学校、金融机构
gov
edu
alipay.com
tenpay.com
unionpay.com
yunshanfu.cn
icbc.com.cn
ccb.com
boc.cn
bankcomm.com
abchina.com
cmbchina.com
psbc.com
cebbank.com
cmbc.com.cn
pingan.com
spdb.com.cn
bank.ecitic.com
cib.com.cn
hxb.com.cn
cgbchina.com.cn
jcbcard.cn
pbccrc.org.cn
adbc.com.cn
# 中国大陆不适用代理的域名
10099.com.cn
10010.com
189.cn
10086.cn
1688.com
jd.com
taobao.com
pinduoduo.com
cctv.com
cntv.cn
tianya.cn
tieba.baidu.com
xuexi.cn
rednet.cn
weibo.com
zhihu.com
douban.com
tmall.com
vip.com
toutiao.com
zijieapi.com
xiaomi.cn
oppo.cn
oneplusbbs.com
bbs.vivo.com.cn
club.lenovo.com.cn
bbs.iqoo.com
realmebbs.com
rogbbs.asus.com.cn
bbs.myzte.cn
club.huawei.com
bbs.meizu.cn
xiaohongshu.com
coolapk.com
bbsuc.cn
tangdou.com
oneniceapp.com
izuiyou.com
pipigx.com
ixiaochuan.cn
duitang.com
renren.com
```



---


---
