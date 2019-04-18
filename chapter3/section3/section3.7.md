### 序言
- 解释流量, 事件, 策略, 风险的关联
- 日志解析的介绍
- 日志解析的例子
- 章结语

#### 解释流量, 事件, 策略, 风险的关联

* 流量: 访问一次页面, 或者是请求一次 API, 在系统中记为一次流量
* 事件: 当一个流量符合一个事件的规则则转化为事件
* 策略: 当事件符合一个策略中的规则则触发风险
* 风险: 由策略规则规定了风险的定义, 风险有次数, 时间, 设备等维度

#### 日志解析的介绍

因为业务千变万化, `TH-Nebula`并不能预知到所有的业务场景, 所以需要自定义化, 可以对一个接口写一个, 甚至多个日志解析去触发策略.日志解析. 为了更加自由定制化的规则, 这部分需要充分理解了策略管理之后才可以理解日志解析, 这部分是依赖策略管理的, 简单来说, 就是以动态流量, 通过规则解析为任意的事件, 从而根据策略的条件触发策略, 这样就捕捉到风险. 然后再对风险做出更多的对策, 抵挡住风险.

#### 如何定制TH-Nebula日志解析？
1. 首先取得所需要监控网站的接口以及页面(page)
2. 对接口的参数需要收集起来,并对参数要明确了解
3. 对接口的类型分类为已有的策略, 例如策略有: 账号-登陆(ACCOUNT_LOGIN), 点击(HTTP_CLICK), 根据接口参数所提供的, 在日志解析中可以利用到, 转化成规则, 分配到策略上, 就可以在有流量的时候, 将流量转化为事件, 将事件分配到策略上, 而是否触发策略, 需要根据策略的规则来定, 之后在详情页面拿到所触发的策略以及流量详情(包含账户信息, IP等等).


#### 例子一：日志解析 解析用户访问

日志的解析可以做到很复杂的定制, 这个例子更偏向于解释日志解析这个功能, 业务风险需要理解后做到更贴切的一些规则. 但例子覆盖基本的操作来解释日志解析的功能.

1, 首先打开 `TH-Nebula` 管理后台.然后打开日志解析的标签.

![](http://ww1.sinaimg.cn/large/66d0828fgy1g25qnxoc4fj217o0midhd.jpg)

2, 点击新建解析, 来设定规则从动态流量转化为设定的事件.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25qupv4txj217r0mcjsx.jpg)

3, 选择  `HTTP_DYNAMIC` 来将所有得到的 `HTTP_DYNAMIC` 转化为想要转换的事件
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25r2ighr8j217m0m7767.jpg)

4, 可以看到图中, 将 c_ip 中包含 . IP , 这句话的意思是, 所有访问的 IP 的流量都转换为我想要的事件, 因为所有 IP 中不可避免的都包含了 .
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25r4h3rrsj217s0mhmz2.jpg)

5, 看图中, 我设定了转换为 `ORDER_SUBMIT` , 上方矩形展示的是 `HTTP_DYNAMIC` 中拥有的字段, 而下方矩形展示的是 `ORDER_SUBMIT` 所拥有的字段, 这些字段在策略中都是可以进行计算的, 这涉及到了策略的复杂度, 就不展开讲了, 详情可以看策略的篇章.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25r9g3xjhj217f0mamzu.jpg)

6, 接下来可以看到, 我在下方设置了 user_name 是等于变量 cookie 中获取 user 的字段中的值的, 这个具体要看公司的业务, 在 cookie 中是如何设置的.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rcgwz2rj217s0mfq5m.jpg)

7, 实际上到这里的简单的规则就设定完毕了.启用这条日志解析就可以了.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25re9b95fj217o0mdmys.jpg)

8, 但是这些流量已经转换为具体的事件, 他并没有发挥捕捉风险的作用, 最关键的地方是需要设定策略, 与日志解析相配合, 达到效果, 符合策略的规则才可以触发策略, 将事件计算为风险, 这样让管理者明显的感知到这些用户是黑产用户, 对其做出封号, 或者其他的措施.

9, 首先打开 `策略管理` , 到具体的分类创建具体的策略.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rlsxq33j217r0mewgj.jpg)

10, 点击 `新建策略` , 填写策略的详细信息
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25roukha0j217n0mg40t.jpg)

11, 在策略规则上, 以简单的填写来解释日志解析的作用, 首先日志解析让流量符合 c_ip 包含 . 的流量转换为 订单提交(`ORDER_SUBMIT`) 的事件, 并且将流量中的 cookie 取出 user 作为这个流量的而策略中,如果事件符合 user_name 包含 threathunter, 则触发这个流量为风险流量, 这是个测试, 尽可能的简单, 我会在攻击页面的时候, 在cookie中带上 user=threathunter 这个key-value, 从而测试这个日志解析为事件, 事件触发策略是否可以成功.

12, 接下来对风险的处理, 点击编辑参数.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rwmdgltj21gz0pvn1d.jpg)

13, 填写对风险的处理, 基本填写如下, 将 IP 设置为值类型, 将 c_ip 设置为风险值, 这些数据都在其他地方体现, 你可以在其他的页面中找到对触发风险的 ip 有更加详细的分析.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rxjpcboj21h60pyae7.jpg)

14, 对此策略进行测试并且上线处理.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25s0e8i9hj21h90o50us.jpg)

15, 到此所有的设置就完成了, 如果有流量攻击页面或者 API, 那么 cookie 中带有 user=threathunter, 那么一定会触发这个风险, 将风险 IP 作为风险展示到总览页面以及风险名单页面中.

16, 以下是触发风险的结果.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25s3ik5gpj21h40p4n0p.jpg)

例子一总结: 对此, 你应该有一个大致的了解, 可以动手试试看是不是可以做到日志解析中最简单的流量转换为事件, 以及事件导向策略, 从而触发风险的流程.这对贴切公司业务, 高级定制规则是非常重要的开始.

#### 例子二：日志解析 解析 GET 请求
以最常见的 GET 请求的例子来说明日志解析功能的强大, 以及灵活性, 这些对公司业务非常有帮助, 因为公司的业务非常的复杂, 风控系统不可能提前预知公司业务的复杂性, 所以通过日志解析来解析 GET 请求参数,具有很大的灵活性.

1, 首先打开 `TH-Nebula` 管理后台.然后打开日志解析的标签.

![](http://ww1.sinaimg.cn/large/66d0828fgy1g25qnxoc4fj217o0midhd.jpg)

2, 点击新建解析, 来设定规则从动态流量转化为设定的事件.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25qupv4txj217r0mcjsx.jpg)

3, 选择  `HTTP_DYNAMIC` 来将所有得到的 `HTTP_DYNAMIC` 转化为想要转换的事件
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25r2ighr8j217m0m7767.jpg)

4, 可以看到图中, 将 c_ip 中包含 . IP , 这句话的意思是, 所有访问的 IP 的流量都转换为我想要的事件, 因为所有 IP 中不可避免的都包含了 .
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25r4h3rrsj217s0mhmz2.jpg)

5, 看图中, 我设定了转换为 `ORDER_SUBMIT` , 上方矩形展示的是 `HTTP_DYNAMIC` 中拥有的字段, 而下方矩形展示的是 `ORDER_SUBMIT` 所拥有的字段, 这些字段在策略中都是可以进行计算的, 这涉及到了策略的复杂度, 就不展开讲了, 详情可以看策略的篇章.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25r9g3xjhj217f0mamzu.jpg)

6, 接下来可以看到, 我在下方设置了 receiver_email 是等于变量 uri_query 中获取 username 的字段中的值的, 这个具体要看公司的业务, 在 GET请求中的参数都有哪些可以利用的, 这里的 username 的值假设是邮箱账号
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26t2l907kj21760kp76y.jpg)

7, 实际上到这里的简单的规则就设定完毕了.启用这条日志解析就可以了.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25re9b95fj217o0mdmys.jpg)

8, 但是这些流量已经转换为具体的事件, 他并没有发挥捕捉风险的作用, 最关键的地方是需要设定策略, 与日志解析相配合, 达到效果, 符合策略的规则才可以触发策略, 将事件计算为风险, 这样让管理者明显的感知到这些用户是黑产用户, 对其做出封号, 或者其他的措施.

9, 首先打开 `策略管理` , 到具体的分类创建具体的策略.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rlsxq33j217r0mewgj.jpg)

10, 点击 `新建策略` , 填写策略的详细信息
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26tgfh12rj217n0ktac5.jpg)

11, 在策略规则上, 以简单的填写来解释日志解析的作用, 首先日志解析让流量符合 c_ip 包含 . 的流量转换为 订单提交(`ORDER_SUBMIT`) 的事件, 并且将流量中的 GET请求中的参数 username 取出来作为这个流量的数据, 赋值在了 receiver_email , 而策略中,如果事件符合 receiver_email 包含 @, 则触发这个流量为风险流量, 这是个测试, 尽可能的简单, 我会在攻击页面的时候, 在 GET 请求中带上 username=threathunter@threathunter.cn 这个key-value, 从而测试这个日志解析为事件, 事件触发策略是否可以成功.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26tlltu5fj216m0kotb8.jpg)

12, 接下来对风险的处理, 点击编辑参数.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rwmdgltj21gz0pvn1d.jpg)

13, 填写对风险的处理, 基本填写如下, 将 IP 设置为值类型, 将 c_ip 设置为风险值, 这些数据都在其他地方体现, 你可以在其他的页面中找到对触发风险的 ip 有更加详细的分析.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rxjpcboj21h60pyae7.jpg)

14, 对此策略进行测试并且上线处理.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26tp1cxm4j217k0kqmyx.jpg)

15, 到此所有的设置就完成了, 如果有流量攻击页面或者 API, 那么 GET 中带有 username中包含 @ 那么一定会触发这个风险, 将风险 IP 作为风险展示到总览页面以及风险名单页面中.

16, 以下是触发风险的结果.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26tu1ywexj21750ku41g.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26tun9uvxj217f0knjte.jpg)

#### 例子三：日志解析 解析 POST 请求
以最常见的 POST 请求的例子来说明日志解析功能的强大, 以及灵活性, 这些对公司业务非常有帮助, 因为公司的业务非常的复杂, 风控系统不可能提前预知公司业务的复杂性, 所以通过日志解析来解析 POST 请求参数,具有很大的灵活性.

1, 首先打开 `TH-Nebula` 管理后台.然后打开日志解析的标签.

![](http://ww1.sinaimg.cn/large/66d0828fgy1g25qnxoc4fj217o0midhd.jpg)

2, 点击新建解析, 来设定规则从动态流量转化为设定的事件.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25qupv4txj217r0mcjsx.jpg)

3, 选择  `HTTP_DYNAMIC` 来将所有得到的 `HTTP_DYNAMIC` 转化为想要转换的事件
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25r2ighr8j217m0m7767.jpg)

4, 可以看到图中, 将 c_ip 中包含 . IP , 这句话的意思是, 所有访问的 IP 的流量都转换为我想要的事件, 因为所有 IP 中不可避免的都包含了 .
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25r4h3rrsj217s0mhmz2.jpg)

5, 看图中, 我设定了转换为 `ORDER_SUBMIT` , 上方矩形展示的是 `HTTP_DYNAMIC` 中拥有的字段, 而下方矩形展示的是 `ORDER_SUBMIT` 所拥有的字段, 这些字段在策略中都是可以进行计算的, 这涉及到了策略的复杂度, 就不展开讲了, 详情可以看策略的篇章.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25r9g3xjhj217f0mamzu.jpg)

6,  接下来可以看到, 我在下方设置了 transaction_id 是等于变量 从 POST 参数中获取 order_number 字段的值, 这个具体要看公司的业务, 在 POST 请求中的参数都有哪些可以利用的, 这里的 order_number 的值假设是订单号, 可以用于策略的计算 
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26u4fjxyjj216y0ksq5p.jpg)

7, 实际上到这里的简单的规则就设定完毕了.启用这条日志解析就可以了.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25re9b95fj217o0mdmys.jpg)

8, 但是这些流量已经转换为具体的事件, 他并没有发挥捕捉风险的作用, 最关键的地方是需要设定策略, 与日志解析相配合, 达到效果, 符合策略的规则才可以触发策略, 将事件计算为风险, 这样让管理者明显的感知到这些用户是黑产用户, 对其做出封号, 或者其他的措施.

9, 首先打开 `策略管理` , 到具体的分类创建具体的策略.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rlsxq33j217r0mewgj.jpg)

10, 点击 `新建策略` , 填写策略的详细信息
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26ud5ldj4j217q0krmzf.jpg)

11, 在策略规则上, 以简单的填写来解释日志解析的作用, 首先日志解析让流量符合 c_ip 包含 . 的流量转换为 订单提交(`ORDER_SUBMIT`) 的事件, 并且将流量中的 POST 请求中的参数 order_number 取出来作为这个流量的数据, 赋值在了 transaction_id , 而策略中,如果事件符合 transaction_id 包含 0, 假设订单号至少有一个 0, 则触发这个流量为风险流量, 这是个测试, 尽可能的简单, 我会在攻击页面的时候, 在 POST 请求中带上 order_number=0888888880 这个key-value, 从而测试这个日志解析为事件, 事件触发策略是否可以成功.

12, 接下来对风险的处理, 点击编辑参数.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rwmdgltj21gz0pvn1d.jpg)

13, 填写对风险的处理, 基本填写如下, 将 IP 设置为值类型, 将 c_ip 设置为风险值, 这些数据都在其他地方体现, 你可以在其他的页面中找到对触发风险的 ip 有更加详细的分析.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g25rxjpcboj21h60pyae7.jpg)

14, 对此策略进行测试并且上线处理.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26uowzboaj21780ksac4.jpg)

15, 到此所有的设置就完成了, 如果有流量攻击页面或者 API, 那么 POST 中带有 order_number 中包含 0 那么一定会触发这个风险, 将风险 IP 作为风险展示到总览页面以及风险名单页面中.

16, 以下是触发风险的结果.
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26wgk56r1j21770ksgof.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fgy1g26wnww89hj21770ktdhp.jpg)


