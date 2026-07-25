# 4.2. 数据分析


在获取了足够多的数据（后面称为原始数据）之后, 接下来我们要考虑的事情就是怎么去处理这一些数据, 怎样从数据中获取我们需要的信息.在这里我们有两件事情需要考虑:

- 数据建模
- 数据计算

下面容我向大家一一道来:

## 数据建模

数据建模, `指的是对现实世界各类数据的抽象组织, 确定数据库需管辖的范围, 数据的组织形式等直至转化成现实的数据库`(这里借用某百科的解释) .

通俗来讲, 就是我们现在采集到的是一些杂乱无章的数据, 每一个客户的数据均不相同, 如果我们就这样直接计算分析, 那就得一个客户一套分析方案, 咱们这个项目也就没有执行的必要了. 所以我们需要对我们拿到的数据做一个抽象, 把每一个数据抽象成一个事件, 把每一个事件中的数据字段抽象成一个属性, 然后再做其他的处理.

但是单单只有事件, 好像计算分析不知道该怎么下手, 所以这里我们又引入了变量, 策略两个新模型, 接下来我们简单讲讲这三个模型.


### 事件模型

事件模型, 根据我们首先抽象出下面这种事件模型, 暂时叫做`0.1`版本:

```json
{
	"properties":[
		{
			"name": "c_ip",
			"type": "string",
			"subtype": "",
			"visible_name": "客户端ip",
			"remark": "客户端IP(默认取xforward最后一个)"
		}, {
			"name": "sid",
			"type": "string",
			"subtype": "",
			"visible_name": "session会话ID",
			"remark": "session会话ID"
		}, {
			"name": "uid",
			"type": "string",
			"subtype": "",
			"visible_name": "用户ID",
			"remark": "用户ID"
		}, {
			"name": "did",
			"type": "string",
			"subtype": "",
			"visible_name": "设备ID",
			"remark": "设备ID"
		}, {
			"name": "platform",
			"type": "string",
			"subtype": "",
			"visible_name": "客户端类型",
			"remark": "客户端类型"
		}, {
			"name": "page",
			"type": "string",
			"subtype": "",
			"visible_name": "伪静态页面加工后地址",
			"remark": "伪静态页面加工后地址(全部小写, 去端口)"
		}, {
			"name": "c_port",
			"type": "long",
			"subtype": "",
			"visible_name": "客户端端口",
			"remark": "客户端端口"
		}, {
			"name": "c_bytes",
			"type": "long",
			"subtype": "",
			"visible_name": "请求内容大小",
			"remark": "请求内容大小"
		},
        ……
        , {
			"name": "geo_province",
			"type": "string",
			"subtype": "",
			"visible_name": "地理位置",
			"remark": "地理位置"
		}
	]
}
```

后面发现这个还是有很明显的缺陷, 因为这样建立的事件模型解析出来的都是一类事件, 这给我们后续的计算分析工作带来很大的麻烦, 所以我们又下面的建立``0.2`版本:

```json
{
    "name": "HTTP_DYNAMIC",
	"visible_name": "动态资源请求",
	"properties":[
		{
			"name": "c_ip",
			"type": "string",
			"subtype": "",
			"visible_name": "客户端ip",
			"remark": "客户端IP(默认取xforward最后一个)"
		}, {
			"name": "sid",
			"type": "string",
			"subtype": "",
			"visible_name": "session会话ID",
			"remark": "session会话ID"
		}, {
			"name": "uid",
			"type": "string",
			"subtype": "",
			"visible_name": "用户ID",
			"remark": "用户ID"
		}, {
			"name": "did",
			"type": "string",
			"subtype": "",
			"visible_name": "设备ID",
			"remark": "设备ID"
		}, {
			"name": "platform",
			"type": "string",
			"subtype": "",
			"visible_name": "客户端类型",
			"remark": "客户端类型"
		}, {
			"name": "page",
			"type": "string",
			"subtype": "",
			"visible_name": "伪静态页面加工后地址",
			"remark": "伪静态页面加工后地址(全部小写, 去端口)"
		}, {
			"name": "c_port",
			"type": "long",
			"subtype": "",
			"visible_name": "客户端端口",
			"remark": "客户端端口"
		}, {
			"name": "c_bytes",
			"type": "long",
			"subtype": "",
			"visible_name": "请求内容大小",
			"remark": "请求内容大小"
		},
        ……
        , {
			"name": "geo_province",
			"type": "string",
			"subtype": "",
			"visible_name": "地理位置",
			"remark": "地理位置"
		}
	]
}
```

这个版本我们根据资源的请求方式将事件划分为动态资源请求`HTTP_DYNAMIC`和静态资源请求`HTTP_STATIC`, 但是这个模型距离我们分析的需求还有很大差距, 于是我们根据不同的业务场景(比如登陆, 注册, 下单, 支付等)在这两个分类上加上了其他的事件类型, 于是就出现了`0.3`版本的模型:

```json
{
    "name": "HTTP_DYNAMIC",
	"visible_name": "动态资源请求",
    "remark": "动态资源请求",
    "source": [],
	"properties":[
		{
			"name": "c_ip",
			"type": "string",
			"subtype": "",
			"visible_name": "客户端ip",
			"remark": "客户端IP(默认取xforward最后一个)"
		}, {
			"name": "sid",
			"type": "string",
			"subtype": "",
			"visible_name": "session会话ID",
			"remark": "session会话ID"
		}, {
			"name": "uid",
			"type": "string",
			"subtype": "",
			"visible_name": "用户ID",
			"remark": "用户ID"
		}, {
			"name": "did",
			"type": "string",
			"subtype": "",
			"visible_name": "设备ID",
			"remark": "设备ID"
		}, {
			"name": "platform",
			"type": "string",
			"subtype": "",
			"visible_name": "客户端类型",
			"remark": "客户端类型"
		}, {
			"name": "page",
			"type": "string",
			"subtype": "",
			"visible_name": "伪静态页面加工后地址",
			"remark": "伪静态页面加工后地址(全部小写, 去端口)"
		}, {
			"name": "c_port",
			"type": "long",
			"subtype": "",
			"visible_name": "客户端端口",
			"remark": "客户端端口"
		}, {
			"name": "c_bytes",
			"type": "long",
			"subtype": "",
			"visible_name": "请求内容大小",
			"remark": "请求内容大小"
		},
        ……
        , {
			"name": "geo_province",
			"type": "string",
			"subtype": "",
			"visible_name": "地理位置",
			"remark": "地理位置"
		}
	]
}
```



看上面这个动态资源请求的模型也许并不能看出明显的区别, 下面我们看一个账号登陆的模型:



```json
{
	"app": "nebula",
	"name": "ACCOUNT_LOGIN",
	"visible_name": "账号-登录",
	"remark": "账号-登录",
	"source": [{
		"app": "nebula",
		"name": "HTTP_DYNAMIC"
	}],
	"properties": [{
		"name": "c_ip",
		"type": "string",
		"subtype": "",
		"visible_name": "客户端ip",
		"remark": "客户端IP(默认取xforward最后一个)"
	}, {
		"name": "sid",
		"type": "string",
		"subtype": "",
		"visible_name": "session会话ID",
		"remark": "session会话ID"
	}, {
		"name": "did",
		"type": "string",
		"subtype": "",
		"visible_name": "设备ID",
		"remark": "设备ID"
	}, {
		"name": "platform",
		"type": "string",
		"subtype": "",
		"visible_name": "客户端类型",
		"remark": "客户端类型"
	}, {
		"name": "page",
		"type": "string",
		"subtype": "",
		"visible_name": "伪静态页面加工后地址",
		"remark": "伪静态页面加工后地址(全部小写, 去端口)"
	}, {
		"name": "c_port",
		"type": "long",
		"subtype": "",
		"visible_name": "客户端端口",
		"remark": "客户端端口"
	}, {
		"name": "c_bytes",
		"type": "long",
		"subtype": "",
		"visible_name": "请求内容大小",
		"remark": "请求内容大小"
	},
	……
	, {
		"name": "login_channel",
		"type": "string",
		"subtype": "",
		"visible_name": "登陆渠道",
		"remark": "登陆渠道"
	}]
}
```

我们这个版本增加了一个`source`字段, 表示该事件是以`source`生成的事件, 上面这个账号登陆事件表示的正是如此.到这里我们针对原始数据的处理也就做完了, 事件模型也就基本确定了下来.

### 变量模型

顾名思义, 变量模型是为了方便我们后续的计算分析而增加的一个模型.
一想到变量, 大家可以想到的不外乎是计算, 分类, 过滤, 统计, 聚合之类的概念, 我们自然也不会脱离这些基本的概念, 可以说我们之所以想单独把变量模型化就是为了能更好, 更方便的实现这些概念. 首先与事件类似, 变量肯定需要按照不同的场景以及不同的计算要求进行分类, 例如

- 按照变量生成方式进行分类, 比如按照基础变量与策略引擎配置生成的变量分类.
- 按照不同的计算模式进行分类, 比如top, filtter等.
- ……

于是, 到最终的版本, 我们的变量模型type就分成了filter, aggregate, collector, delaycollector, dual, event, internal, sequence, top. 值得注意的是与事件模型类似, 非event的变量模型均是基于event变量模型上配置生成, 所以我们这里自然需要配置resource字段.

到这新的问题也就出现了, 总感觉还差点什么, 变量就是为了计算, 我们还需要给变量添加各种与计算相关的字段, 比如, 计算函数, 过滤条件, 变量存活时间等, 总之变量模型的设计不是拍拍脑袋就可以完成的事情, 需要慢慢摸索, 积累. 并且模型设计本来就没有一个最好的说法, 后期慢慢完善才是正解. 最后附上我们的最终模型示例:

```
{
	"status": "enable",
	"filter": {
		"type": "and",
		"condition": [{
			"object_subtype": "",
			"object": "value",
			"object_type": "long",
			"value": "5",
			"source": "_ip__strategy__1542255171782__4950E585B3E88194E5A49AE8AEBEE5A487E8AFB7E6B182E799BBE5BD95__counter__2__rt",
			"param": "",
			"operation": ">",
			"type": "simple"
		}]
	},
	"remark": "collector for strategy IP关联多设备请求登录",
	"name": "_ip__strategy__1542255171782__4950E585B3E88194E5A49AE8AEBEE5A487E8AFB7E6B182E799BBE5BD95__collect__rt",
	"hint": {},
	"value_category": "",
	"app": "nebula",
	"period": {},
	"module": "realtime",
	"value_subtype": "",
	"visible_name": "collector for strategy IP关联多设备请求登录",
	"source": [{
		"app": "nebula",
		"name": "_ip__strategy__1542255171782__4950E585B3E88194E5A49AE8AEBEE5A487E8AFB7E6B182E799BBE5BD95__trigger__rt"
	}, {
		"app": "nebula",
		"name": "_ip__strategy__1542255171782__4950E585B3E88194E5A49AE8AEBEE5A487E8AFB7E6B182E799BBE5BD95__counter__2__rt"
	}],
	"value_type": "",
	"groupbykeys": ["c_ip"],
	"function": {
		"object_subtype": "",
		"object": "",
		"object_type": "",
		"param": "IP关联多设备请求登录",
		"source": "_ip__strategy__1542255171782__4950E585B3E88194E5A49AE8AEBEE5A487E8AFB7E6B182E799BBE5BD95__trigger__rt",
		"config": {
			"trigger": "_ip__strategy__1542255171782__4950E585B3E88194E5A49AE8AEBEE5A487E8AFB7E6B182E799BBE5BD95__trigger__rt"
		},
		"method": "setblacklist"
	},
	"type": "collector",
	"dimension": "ip"
}
```


###  策略模型

既然是业务风控, 自然就少不了策略的配置, 风控前期靠开发, 后期靠运营, 而策略则将是后期整个运营的核心.

策略, 就是计算的规则, 就是要告诉我们的计算引擎数据该怎么计算, 如果结合业务来说那就是告诉系统什么样的事件是风险事件, 当然, 如果结合我们整体的系统设计来讲的话, 策略就是告诉系统如何去生成计算变量. 到这里, 大家应该也就清楚了, 策略模型是一个面向用户的模型, 是一个用来配置计算变量的模型, 下面我们来简略地讲讲我们策略模型的设计想法.

首先, 模型分类必然是不可以缺少的, 既然是用于配置计算变量的, 那么跟变量理应也是一致的. 然后既然是配置计算变量, 那么自然也就需要进行左右值的对比, 对比规则(基础运算, 正则匹配等)以及设置生效存活时间之类的字段, 这自然不会是一个一蹴而就的过程, 明显是一个需要我们精益求精, 不断尝试的过程. 最后, 我们给机器识别的字段之后, 在添加几个用户可读的备注说明字段, 到这里策略模型的设计才算是圆满结束, 此处附上我们最终的设计实例.

```json
{
    "status": "inedit",
    "terms": [{
      "scope": "realtime",
      "remark": "",
      "op": "!regex",
      "right": {
        "subtype": "",
        "config": {
          "value": "^\\s*$"
        },
        "type": "constant"
      },
      "left": {
        "subtype": "",
        "config": {
          "field": "page",
          "event": ["nebula", "ORDER_SUBMIT"]
        },
        "type": "event"
      }
}
```


## 数据计算

数据计算这块, 我们根据数据计算需求频率和资源消耗等考量, 把数据计算部分划分为两个部分:

- 实时计算:主要负责时间较短的变量计算以及统计
- 离线计算:主要复制时间较长的变量计算, 统计以及数据持久化

### 实时计算

实时计算这块又根据计算的频率分为实时计算和准实时计算.

- 实时计算

  实时计算提供实时的, 准确的, 基于滑动窗口的统计和计算值, 具体表现为5分钟内数据计算.

  例如:

  (1) 5分钟内某个ip的实时访问总量, 静态资源访问总量, 某个页面的访问总量. 这个直接做过滤和聚合就可以.

  (2) 5分钟内某个ip的post比率. 这种需要多个变量作组合, 来生成新的变量.

  (3) 5分钟内某个ip的爬虫指数. 这个是偏业务的, 由多个变量来组合.

  (4) 5分钟内某个user的不同ip数目.

  这些指标从原始事件中生成, 能反应用户的实时特征, 对安全方面的需求会非常有价值. 这类指标的计算频率会比较大, 相关联的周期和数据会比较小.

- 准实时变量

  准实时计算频度略低, 但能反应较长时间段内的统计量, 例如:

  (1) 当天某个ip的实时访问总量

  (2) 当前小时内某个ip的post请求总量


### 离线计算

离线计算主要是长时间的变量计算和数据持久化. 数据持久化即为数据存储, 将会在架构设计中较为详细说明, 此处不做说明, 这里我们主要聊聊计算部分.

离线计算用来计算更长时间段内的统计量, 例如:

(1) 5天内某个ip的实时访问总量

(2) 30天内某个ip的post请求总量

(3) 3小时内某个ip的静态资源访问总量

这类统计:

(1) 时间跨度比较长, 计算量比较大

(2) 因此要求查询次数不能过高

(3) 值的精确度和时间的精确度做不到100%（出于实现的考虑）
