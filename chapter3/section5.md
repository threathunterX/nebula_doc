# 3.5. 阻断星云中发现的风险


由于 TH-Nebula 属于旁路分析模式，所以无法主动拦截风险事件，需要与企业端应用进行集成后实现自动阻断的功能。

### 企业端常见的阻断风险方式

● 防火墙/交换机等网络串行设备阻断IP

● 业务中间件阻断 IP 或 session

● 通用 BOTS 拦截页面阻断 IP

● 业务处置中心阻断帐号风险

● 风险判断接口阻断风险业务行为（订单、交易）阻断账户风险

● 登录态实效或增强验证阻断帐号风险

上述阻断方式只是常见的一些阻断手段，采用的手段并不是越多越好，而是结合自身的企业特征选择，重点在于保证覆盖，这里指的覆盖是指不论何种风险自动分析系统，均可在阻断体系中找到自己的位子，同时在发生风险问题的时候能够通过多种不同的维度在应用架构中找到一层可以去阻拦风险行为的。推荐的最佳阻拦机制可见下图所示：
![](http://ww1.sinaimg.cn/large/66d0828fgy1g1p9iktozgj20tu0kqtg3.jpg)**最外层为访客层：**当一个未登录的用户访问网站或应用时，这时只能通过 IP 来去识别用户（设备指纹或 clientlD 等技术不在此处讨论），所以在访客层主要拦截维度是 IP。

**在访客登录某个帐号后进入账号层：**进入帐号层的主要入口包含常见的登录和注册，另外在线找回密码也属于一个帐号层入口，在这一层主要有两种类型的阻断手段：

第一类是在入口处进行风险被动验证，比如视风险判断接口在登录时判断是否要给当前登录用户弹出验证码或者提示更高级别的手机验证或直接禁止用户登录；

另一类是主动风险阻断，这一类是在帐号登录后通过用户登录后行为持续分析是否存在风险的，如果用户的某些特定行为触发了风险规则，而用户又已经登入了帐号，这个时候可以选择将用户登录态置为失效，风险用户就会在下一步操作时发现需要重新登录了，而在再次登录的时候可以通过增强验证或帐号锁定的方式阻断风险。

**帐号层内层是账户层：**这一层在许多企业中区分的可能不是非常明显，但当我们使用支付密码去使用账户资金下单的时候，往往使用的是与登录密码不同的支付密码或支付验证手机，那么这就意味着已经进入了最重要的资金层，在这一层对风险的阻断手法也以保护资金不被盗用为主，包含冻结资金、锁定支付账户或是拦截支付请求亦或订单。

需要注意的是每一层的风险都可被上一层的阻断机制覆盖，但考虑到越向内层的行为越产生业务价值，所以在封禁手段上会更加细致，否则为了拦截订单而去拦截一个 IP 可能会产生误封的风险。

### TH-Nebula 的风险拦截机制

可以从。上一节看出一个成熟的阻断机制并未能够由某个单独的系统完成，TH-Nebula 与拦截链中的各个节点打通是通过两种机制来完成的



## 拉黑阻断机制

针对业务系统拉黑阻断，系统提供以下两种风险数据获取方法：

主动推送：TH-Nebula 可以将分析发现的风险推送至拦截节点进行自动的风险阻断。

被动调用：TH-Nebula 可以将分析发现的风险名单以接口方式提供给拦截节点调用判断风险。

### 被动调用方式

**`/checkRisk`** 风险检测接口, 业务方发起风险检测请求, 判定`USER`, `IP`, `DID`,`ORDER ID`是否有风险.

参数:  
`query`:
需要查询的具体内容, 格式如下(需`url`编码):

```json
{
	"check_item": [
		{
			"k": "USER", //检测类型
			"v": "threathunter_test" //值
		},
		{
			"k": "USER",
			"v": "threathunter"
		}
	],
	"full_respond": true, //获取全部有效事件
	"scene_type": "ORDER" //指定场景
}
```
备注:
>   检测类型:   ` IP` , `DEVICE ID`, `USER`, `ORDERID`  
>   场景: `OTHER`, `VISITOR`, `ACCOUNT`, `MARKETING`, `ORDER`, `TRANSACTION`

`auth`:
 鉴权用，在`nebula_web` 中 `setting.py`中配置:

示例:

```
http://127.0.0.1:9001/checkRisk?auth=7a7c4182f1bef7504a1d3d5eaa51a242&query=%7b>"check_item"%3a%5b%7b"k"%3a"USER"%2c"v"%3a"threathunter_test"%7d%2c%7b"k"%3a"USER"%2c"v"%3a"threathunter"%7d%5d%2c"full_respond"%3atrue%2c"scene_type"%3a"ORDER"%7d
```

![auth.png](http://www.z4a.net/images/2018/12/10/e990124abe12f054ceafdca6975a6179.png)

### 主动推送方式

**推荐方式:**   
采用redis的发布与订阅(publish/subscribe)模式，业务方订阅channel `nebula.realtime.notice`  获取实时通知即可

*数据示例:*
```json
{
	"remark": ">100, avg < 0.8, in 5m, web", //风险备注
	"geo_city": "深圳市", //市
	"checkpoints": "",
	"timestamp": 1552635533123, //触发时间
	"decision": "review", //决策，业务侧以此为处理依据（跟策略的配置有关）
	"tip": "IP页面停留时间过短Web",
	"variable_values": "",
	"risk_score": 0, 
	"test": 0, //是否是测试策略
	"strategy_name": "IP页面停留时间过短Web", //触发策略名
	"expire": 1552635833123, //过期时间，跟策略的配置有关
	"key": "183.15.177.209", //风险值
	"scene_name": "VISITOR", //风险场景: `OTHER`, `VISITOR`, `ACCOUNT`, `MARKETING`, `ORDER`, `TRANSACTION`
	"uri_stem": "112.74.58.210/user", //关联页面
	"trigger_event": "{\"s_ip\": \"172.18.16.169\", \"app\": \"nebula\", \"pid\": \"000000000000000000000000\", \"s_type\": \"text/html; charset=utf-8\", \"uri_stem\": \"112.74.58.210/user\", \"c_bytes\": 0, \"id\": \"5c8b568c12a3650017e176e4\", \"uid\": \"\", \"request_time\": 3, \"platform\": \"\", \"s_body\": \"\", \"sid\": \"\", \"s_port\": 9001, \"method\": \"GET\", \"status\": 200, \"is_static\": false, \"geo_city\": \"\深\圳\市\", \"c_body\": \"\", \"timestamp\": 1552635532701, \"geo_province\": \"\广\东\省\", \"host\": \"112.74.58.210\", \"referer\": \"\", \"c_ip\": \"183.15.177.209\", \"key\": \"183.15.177.209\", \"useragent\": \"python-requests/2.11.1\", \"c_port\": 18311, \"xforward\": \"\", \"c_type\": \"\", \"name\": \"HTTP_DYNAMIC\", \"did\": \"\", \"s_bytes\": 648, \"request_type\": \"\", \"value\": 1.0, \"cookie\": \"group_id=2|1:0|10:1540368603|8:group_id|4:Mg==|30789028a7e8399f5f5ef115fc050e1b3424df25b966755be7554e0048dd29a4; user_id=2|1:0|10:1540368603|7:user_id|4:Mg==|141555b29c711f95ddebea3987820fb90c30a48f506eae9f8cee9b334372ae79; user=attack_test; auth=2|1:0|10:1540368603|4:auth|44:NGJlZTQwMDI0NTExYjM2NDVkNjkzOTM1ZTJmMDllMWY=|34718dcbcac603ee1a38daaa0a05fbafc524873af0fafb0013077a53a28b2d4b\", \"page\": \"112.74.58.210/user\", \"uri_query\": \"\", \"referer_hit\": \"F\"}", //触发事件
	"geo_province": "广东省", //省
	"check_type": "IP" //值类型, 如：` IP` , `DEVICE ID`, `USER`, `ORDERID`
}
```

![image](https://user-images.githubusercontent.com/31437628/54415966-e9eee900-4738-11e9-86fa-7b0229242216.png)


下表列举了 TH-Nebula 的两种风险通知机制如何应用于各个业务环节

|	|访客层	|帐号层	|账户层	|
|---	|---	|---	|---	|
|IP	|推送IP至业务中间件拦截；推送IP至串行网络阻断设备阻断IP(需提供API接口）
推送IP至通用验证码拦截页面	|入口调用Nebula风险判断接口对风险IP弹出验证码防止撞库风险	|订单调用Nebula风险判断接口对风险IP下单时弹出验证码防止机器恶意下单	|
|帐号（用户名、会话ID)	|	|入口调用 TH-Nebula 风险判断接口阻拦风险帐号登录；
登录后 TH-Nebula 推送风险帐号给登录态管理服务拦截已登录风险帐号	|调用 TH-Nebula 风险判断接口对风险帐号修改交易密码请求由使用原密码改为验证交易绑定手机	|
|账号（订单号、流水号等）	|	|	|下单调用 TH-Nebula 风险判断接口阻拦风险交易；
TH-Nebula 推送风险订单号给订单取消接口	|

### 推荐的阻断机制方案

**方案1:NGINX+LUA插件阻断**
![](http://ww1.sinaimg.cn/large/66d0828fgy1g1p9ipvrrkj20xq0fmq7i.jpg)在 Tomcat Cluster 前增加一-组 NGNIX 集群做为 PROXY，使用NGINX+LUA 插件可根据 HTTP 请求报文中的信息实现风险阻断，比如 IP、设备信息等

**GOOD SIDE**: NGNIX 插件我们已经有标准解决方案，增加一-层透明层，提升了架构的灵活性，而且不依赖于后端 WEB 容器和程序语言，是目前成熟互联网公司逐渐采取的方式
**BAD SIDE**：对于一些比较传统稳定的架构，需要做架构改造，带来了一定工作量
**推荐：**对于中间件比较成熟，希望最小代价达到最好效果的用户

### 阻断机制最佳实践

**用户 A：主要利用 TH-Nebula 防范爬虫风险**

拦截实现：客户产品页长期有大量的网页爬虫爬取商品价格信息，在通过 TH-Nebula 策略实现爬虫 IP 自动捕获后，开始进行自动阻断打通，最终实现方案如下：
![](http://ww1.sinaimg.cn/large/66d0828fgy1g1p9j4wbhvj211e0ckjw3.jpg)用户有一个 REDIS 黑名单服务用于拦截风险 IP，可将爬虫的请求拦截在应用服务之前，客户系统从redis中实时读取 TH-Nebula 推送出来的风险名单，提取出触发了爬虫策略的IP并存储到 REDIS 黑名单，客户通过 NGINX 将爬虫请求拦截返回固定页面提示用户访问存在异常。

**用户 B：主要利用 TH-Nebula 防范帐号盗用风险**

拦截实现：客户存在有多个登录入口，各个入口强度不一，客户系统从redis中实时读取 TH-Nebula 推送出来的风险名单，发现撞库行为后，开始进行自动阻断打通，最终实现方案如下
![](http://ww1.sinaimg.cn/large/66d0828fgy1g1p9j7at0hj210w0d20w0.jpg)目前用户B 的单点登录（包含移动和新版页面），旧官网页面已接入风险判断接口提供帐号安全防护，由于所有风险判断请求均在内网完成，耗时在 50 ms 以内，达到业务方的响应速度要求。
