# 3.3.2. 事件介绍


## 事件的定义

事件所代表的含义, 在于对所得到的流量分类, 根据事件分类之后, 可以根据事件的字段跟流量的字段对接起来, 然后事件的字段, 可以统计, 计算, 触发审核, 或者拉入黑名单.

## 如何使用事件

使用事件需要去定义一个策略,  进入`TH-Nebula`管理后台之后, 点击新建策略, 如图所示:

![13.事件1](http://wx1.sinaimg.cn/large/0060lm7Tly1fxnnnrtbb0j31gc0mrq64.jpg)    

然后输入策略的名字, 以及策略简介

![14.事件2](http://wx1.sinaimg.cn/large/0060lm7Tly1fxnnoq31x9j30qh0bjmxx.jpg)


然后添加事件, 事件总共有 18 种, 例如点击其中一种: [`账户`-`实名验证`]

![15.事件3](http://wx1.sinaimg.cn/large/0060lm7Tly1fxnnpvp5qfj30q70gata4.jpg)

之后选择[`c_ip`], 表示`client_ip`, 即客户端`IP`

![16.事件4](http://wx4.sinaimg.cn/large/0060lm7Tly1fxnnr1nuahj30q40gomyr.jpg)

接下来选择客户端, 然后选择条件, [ 包含 ] 值为 . 那么实际上就是所有`IP`都能被捕获到这个条件中去.

![17.事件5](http://wx3.sinaimg.cn/large/0060lm7Tly1fxnns14c8uj30qd0erdhb.jpg)

接下来点击添加风险名单, 然后编辑参数

![18.事件6](http://wx3.sinaimg.cn/large/0060lm7Tly1fxnnsqyrrjj30q908tgmu.jpg)

这其中的风险类型为`USER`, 风险值为`c_ip`分配好后, 可以在风险分析中查看到, 这个风险决策为审核, 那么当捕获到这个`IP`的时候, 设置为审核, 可以通过`TH-Nebula`查询接口查询到这个状态, 然后结合业务处理这个风险.

这样就使用了一个名字叫做  [ 账号-实名验证 ] 的一个事件

##  事件所拥有的属性

总共有18种事件, 接下来介绍事件所拥有的属性,  这些属性可以用做计算,  后续会说明  

事件:

- 策略名: `TRANSACTION_DEPOSIT`
- 策略备注: 资金存入

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| geo_city | string | 空 |
| user_name | string | 空 |
| transaction_id | string | 空 |
| deposit_amount | string | 空 |
| card_number | string | 空 |
| counterpart_user | string | 空 |
| account_balance_before | string | 空 |
| result | string | 操作结果 |


- 策略名: `ACCOUNT_CERTIFICATION`
- 策略备注: 账户实名认证

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| user_name | string | 空 |
| person_id | string | 空 |
| real_name | string | 空 |
| result | string | 操作结果 |
| geo_city | string | 空 |


- 策略名: `ACCOUNT_LOGIN`
- 策略备注: 账户登录

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| user_name | string | 空 |
| login_verification_type | string | 空 |
| password | string | 密码md5 |
| captcha | string | 验证码 |
| result | string | 操作结果 |
| remember_me | string | 空 |
| login_channel | string | 空 |
| geo_city | string | 空 |


- 策略名: `ACCOUNT_REFERRALCODE_CREATE`
- 策略备注: `http`动态资源访问

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| geo_city | string | 空 |
| user_name | string | 空 |
| referralcode | string | 空 |
| code_type | string | 空 |


- 策略名: `HTTP_STATIC`
- 策略备注: `http`静态资源访问

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| geo_city | string | 空 |


- 策略名: `HTTP_DYNAMIC`
- 策略备注: `http`动态资源访问

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| geo_city | string | 空 |


- 策略名: `ACCOUNT_TOKEN_CHANGE`
- 策略备注: 账户凭证修改

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| user_name | string | 空 |
| old_token | string | 空 |
| new_token | string | 空 |
| token_type | string | 空 |
| captcha | string | 验证码 |
| result | string | 操作结果 |
| geo_city | string | 空 |


- 策略名: `TRANSACTION_ESCROW`
- 策略备注: 第三方支付

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| user_name | string | 空 |
| transaction_id | string | 空 |
| escrow_type | string | 空 |
| escrow_account | string | 空 |
| pay_amount | double | 空 |
| result | string | 操作结果 |
| geo_city | string | 空 |


- 策略名: `ORDER_SUBMIT`
- 策略备注: 提交订单

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| order_id | string | 空 |
| user_name | string | 空 |
| product_id | string | 空 |
| product_type | string | 空 |
| product_attribute | string | 空 |
| product_count | long | 空 |
| product_total_count | long | 空 |
| merchant | string | 商家 |
| order_money_amount | double | 空 |
| order_coupon_amount | double | 空 |
| order_point_amount | double | 空 |
| transaction_id | string | 空 |
| receiver_mobile | string | 空 |
| receiver_address_country | string | 空 |
| receiver_address_province | string | 空 |
| receiver_address_city | string | 空 |
| receiver_address_detail | string | 空 |
| receiver_realname | string | 空 |
| result | string | 操作结果 |
| geo_city | string | 空 |


- 策略名: `ACTIVITY_DO`
- 策略备注: 营销活动

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| username | string | 空 |
| activity_name | string | 空 |
| activity_type | string | 空 |
| activity_gain_count | long | 空 |
| activity_gain_amount | long | 空 |
| activity_pay_amount | long | 空 |
| acticity_counterpart_user | string | 空 |
| result | string | 操作结果 |
| geo_city | string | 空 |


- 策略名: `HTTP_DYNAMIC_DELAY`
- 策略备注: `http`动态资源访问`Delayevent`

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| geo_city | string | 空 |
| delay_strategy | string | 空 |


- 策略名: `ORDER_CANCEL`
- 策略备注: 取消订单

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| order_id | string | 空 |
| user_name | string | 空 |
| merchant | string | 商家 |
| cancel_reason | string | 空 |
| transaction_id | string | 空 |
| result | string | 操作结果 |
| geo_city | string | 空 |



- 策略名: `ACCOUNT_PW_CHANGE`
- 策略备注: 账户密码修改

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| user_name | string | 空 |
| old_password | string | 旧密码 |
| new_password | string | 新密码 |
| verification_token | string | 空 |
| verification_token_type | string | 空 |
| captcha | string | 验证码 |
| result | string | 操作结果 |
| geo_city | string | 空 |


- 策略名: `ACCOUNT_REGISTRATION`
- 策略备注: 账户注册

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| user_name | string | 空 |
| password | string | 密码md5 |
| register_verification_token | string | 空 |
| register_verification_token_type | string | 空 |
| captcha | string | 验证码 |
| result | string | 操作结果 |
| register_realname | string | 空 |
| register_channel | string | 空 |
| invite_code | string | 空 |
| geo_city | string | 空 |


- 策略名: `TRANSACTION_WITHDRAW`
- 策略备注: 资金取现

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| geo_city | string | 空 |
| user_name | string | 空 |
| transaction_id | string | 空 |
| withdraw_amount | string | 空 |
| withdraw_type | string | 空 |
| card_number | string | 空 |
| counterpart_user | string | 空 |
| account_balance_before | string | 空 |
| result | string | 操作结果 |


- 策略名: `HTTP_CLICK`
- 策略备注:`httpclick`事件

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| id | string | 空 |
| pid | string | 空 |
| c_ip | string | 客户端ip |
| sid | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| platform | string | 空 |
| page | string | 空 |
| notices | string | 空 |
| c_port | long | 客户端端口 |
| c_bytes | long | 请求大小 |
| c_body | string | 请求内容 |
| c_type | string | 空 |
| s_ip | string | 服务端ip |
| s_port | long | 服务端端口 |
| s_bytes | long | 响应大小 |
| s_body | string | 响应内容 |
| s_type | string | 空 |
| host | string | 主机地址 |
| uri_stem | string | url问号前部分 |
| uri_query | string | url问号后部分 |
| referer | string | 空 |
| method | string | 请求方法 |
| status | long | 请求状态 |
| cookie | string | 空 |
| useragent | string | 空 |
| xforward | string | 空 |
| request_time | long | 空 |
| request_type | string | 空 |
| referer_hit | string | 空 |
| geo_city | string | 空 |



- 策略名:`HTTP_INCIDENT`
- 策略备注: 风险事件

|字段名|字段类型|字段备注|
|:-------:|:-------:|:-------:|
| c_ip | string | 客户端ip |
| page | string | 空 |
| uid | string | 空 |
| did | string | 空 |
| timestamp | long | 空 |
| notices | string | 空 |
| tags | string | 空 |
| scores | double | 空 |
| strategies | string | 空 |


## 事件的计算
这些事件中包含了很多字段,  这些字段都可以作为次数计算, 在统计次数或者其他条件的时候可以使用, 例如每次`c_ip`访问一次, `result`记为1,  累计叠加, 那么可以10次的时候直接把这个ip作为黑名单处理.
截图说明:

![19.事件10](http://wx1.sinaimg.cn/large/0060lm7Tly1fxnnua2jgpj30qc09nmy6.jpg)

![20.事件11](http://wx2.sinaimg.cn/large/0060lm7Tly1fxnnv7valmj30qe0fmmyv.jpg)
这就是 [`账户`-`实名验证`] 这个事件中 `result` 的叠加计算.
