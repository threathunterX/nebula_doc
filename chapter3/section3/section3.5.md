# 3.3.7. 运营决策


风控决策引擎是一堆风控规则的集合, 通过不同的分支, 层层规则的递进关系进行运算.而既然是组合的概念, 则在这些规则中, 以什么样的顺序与优先级执行便额外重要.

**自有规则运行优先于外部规则**

举例说明:
自有本地的黑名单库优先于外部的黑名单数据源运行, 如果触发自有本地的黑名单则风控结果可直接终止及输出“拒绝”结论.

##  策略调整以及优化

- 前期可参考系统本身自带策略进行参考, 然后应用开启本地已有风控策略.
- 后期不同的业务场景, 对应不同的策略以及规则, 具体优化需要风控运营人员后期持续跟进, 对现有策略进行优化调整, 以及新增相应规则.


##  回溯

1. 可利用日志查询功能, 对某些疑问点进行批量数据查询以及导出, 随后进行相应的数据分析, 从而优化已有策略以及新增策略.

*日志导出如下*

![23.运营决策.png](http://www.z4a.net/images/2018/11/28/23.png)


## 拉黑阻断机制

针对业务系统拉黑阻断，系统提供以下两种风险数据获取方法：

### 请求api方式获取风险

**`/checkRisk`** 风险检测接口, 业务方主动发起风险检测请求, 判定`USER`, `IP`, `DID`,`ORDER ID`是否有风险.

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

### 直接读取redis获取数据

**推荐方式:**   
采用redis的发布与订阅(publish/subscribe)模式，业务方订阅channel `nebula.realtime.notice`  获取实时通知即可

*数据示例:*
```json
{
	"remark": ">100, avg < 0.8, in 5m, web", //风险备注
	"geo_city": "深圳市", //市
	"checkpoints": "",
	"timestamp": 1552635533123, //触发时间
	"decision": "review", //风险决策，跟策略的配置有关
	"tip": "IP页面停留时间过短Web",
	"variable_values": "",
	"risk_score": 0, //风险分值
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


**备用方法：**   
键名：
`{metrics.db.write}db.write.notice_*`

数据过期时间:
30分钟

获取方法:
通过读取键名为`{metrics.db.write}db.write.notice` (`zset`, 通过时间戳排序) 中的数据`db.write.notice_*`, 与`{metrics.db.write}`进行拼接得到风险数据键名`{metrics.db.write}db.write.notice_*` (`hash`) , 然后获取键值即可获取单条风险数据.

示例:

![redis.png](https://www.z4a.net/images/2019/01/21/redis.png)

*注意:*  
>
> - 这种方式获取的数据时效性极高, 但是得到的数据不是聚合数据.
> - 若当前没有风险数据, 上面提到数据键均有可能不存在.

