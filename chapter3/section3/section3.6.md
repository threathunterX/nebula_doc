# 3.3.6. 策略配置


### 序言
- 业务场景的介绍
- 业务场景的例子
- 章结语

#### 业务场景的介绍
对于公司业务细分到不同的场景, 再到定制策略, 以及 Nebula 脚本可能会有些模糊, 对于一开始认识 Nebula 系统可能会对此概念模糊不清, 所以接下来对于不同的场景分别制定策略, 来深入了解策略的定制, 以及 Nebula 脚本的定制。这些场景分别是: 同一个 IP 不断登陆撞库, 同一个IP恶意注册, IP 爬虫业务系统,
#### 业务场景的例子
#### 例子一：同一个 IP 不断登陆撞库
>撞库词语解释: 撞库是黑客通过收集互联网已泄露的用户和密码信息，生成对应的字典表，尝试批量登陆其他网站后，得到一系列可以登录的用户。很多用户在不同网站使用的是相同的帐号密码，因此黑客可以通过获取用户在A网站的账户从而尝试登录B网址，这就可以理解为撞库攻击

假设同一个IP，不断的用不同的账号或者密码一直访问登陆页面，在策略中设定次数之后就可以捕获到这个 IP，业务系统通过对 Nebula 风险接口的访问, 得到对此 IP 的风险判定, 对此 IP 进行弹验证码，封 IP 等处理。

>Nebula 查询风险接口: 请查看 Nebula 官方文档中 2.3.7 运营决策篇章, 得到更加详尽的解释

##### 策略的制定

1. 打开 Nebula 网站, 登陆之后出现为以下页面, 然后打开 ① 链接, 根据 Nebula 版本不同, 可能会有一些改变, 大致如下: ![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijllr7oej21hc0pqq6q.jpg)
2. 点击新建策略, 对策略的各个参数进行设定 ![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijm7eu5xj21hc0q0q6e.jpg)
3. 填写策略的基本信息, 对策略进行分类 ![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijmcjikxj21hb0pydjj.jpg)
4. 接下来是对策略的条款进行设定, 这个是核心部分, 请注意, 以【事件-动态资源请求】选定属性 page，假设 A 公司中的登陆接口是 /login , 那么需要在策略中设定捕获包含 login 接口, 也就是 page 中包含了 login 即认定为登陆接口，此处要根据自己公司的登陆接口做相应的改变，每家公司业务的登陆接口不尽相同, 例如有的公司的登陆接口也可以是 /register ![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijmgtj5rj21hc0pun07.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijsxj9zkj21ha0pv41q.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijt64rwzj21hb0q00vr.jpg)

5. 使用条件判断来计算值, 例如设定某 IP 访问同一页面在 5 分钟内超过 5次, 则认为此 IP 有撞库风险, 这部分理解难度比较高, 还请先照着图片教程做起来, 慢慢摸索理解  
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijteeq4wj21h80q341n.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijtkfr0aj21hc0q2q62.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijtob4g6j21hb0q077f.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijttez1uj21hc0pn77n.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijtx9j9pj21h70q3gos.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1iju08b1yj21h80q0adm.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1iju2ztnwj21hc0q2djm.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1iju75e4mj21hc0q2gpb.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijuag6ioj21ha0q00wd.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijud8xs1j21h70q3juv.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijuhalatj21hc0q2424.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijukbjzdj21ha0pzq6g.jpg)
>需要注意: 上图中的 page 包含 login 需要与登陆接口一一对应, 之前填写的为 login, 现在也应该是 login

![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijunlqm1j21h80pygoq.jpg)
6. 接下来填写 处理措施/添加风险名单 步骤, 此步骤的意义在于, 对符合条件的 IP 进行处理, 例如对此 IP 列为风险名单, 那么业务系统请求 Nebula 风险名单的时候, Nebula 会返回此 IP, 业务系统可以对此 IP 进行不同等级的处理, 例如弹出验证码, 要求重复登陆, 甚至是直接对 此 IP 封禁, 使其无法访问业务系统 ![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijuqd483j21h40pqq6j.jpg)
7. 此时已经完成了策略的配置, 点击'测试' 按钮后再点击 '上线' 按钮, 即可生效, 接下来可以测试 IP撞库 策略是否有效 ![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijut5n6aj21h40nltbp.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijuwub4jj21hc0pw77f.jpg)
8. 使用同一个 IP 对接口 /login 不停的访问, 即可在 '总览' 页面得到已触发策略, 且作为风险名单存在系统之中, 可以对 Nebula 的接口进行访问, 得到此 IP 为有风险的 IP ![](http://ww1.sinaimg.cn/large/66d0828fly1g1ijv24nc9j21h50q976x.jpg)

总结: 以上是实时在线计算 5 分钟内统计同一个 IP 访问登陆接口进行撞库的策略设置



#### 例子二：同一个IP恶意注册

假设同一个IP，不断的用随机的账号密码邮箱去注册账号, 对企业账号风险埋下了极高的风险，在策略中设定某 IP 注册次数之后就可以捕获到这个 IP，业务系统通过对 Nebula 风险接口的访问, 得到对此 IP 的风险判定, 对此 IP 进行弹验证码，封 IP 等处理。

>Nebula 查询风险接口: 请查看 Nebula 官方文档中 2.3.7 运营决策篇章, 得到更加详尽的解释

##### 策略的制定
1. 打开 Nebula 网站, 登陆之后出现为以下页面, 然后打开 ① 链接, 根据 Nebula 版本不同, 可能会有一些改变, 大致如下:
![](http://ww1.sinaimg.cn/large/66d0828fly1g1iks4g9lqj21hc0pqq6q.jpg)
2. 点击新建策略, 对策略的各个参数进行设定
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikt6bj1xj21hc0q0q6e.jpg)
3. 填写策略的基本信息, 对策略进行分类
![](http://ww1.sinaimg.cn/large/66d0828fly1g1iku0pt8xj21hc0pz762.jpg)
4. 接下来是对策略的条款进行设定, 这个是核心部分, 请注意, 以【事件-动态资源请求】选定属性 page，假设 A 公司中的注册接口是 /register , 那么需要在策略中设定捕获包含 register 接口, 也就是 page 中包含了 register 即认定为登陆接口，此处要根据自己公司的注册接口做相应的改变，每家公司业务的注册接口不尽相同, 例如有的公司的注册接口也可以是 /login
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikujar52j21hb0q30v3.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikurw2tkj21hb0pzmzf.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1iky1d9agj21hb0pzmzf.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1iky485inj21h70pugo9.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1iky7io42j21h80pxjtr.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikyajz1aj21ha0q0wh9.jpg)
5. 使用条件判断来计算值, 例如设定某 IP 访问同一页面在 5 分钟内超过 5次, 则认为此 IP 有撞库风险, 这部分理解难度比较高, 还请先照着图片教程做起来, 慢慢摸索理解
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikye47cxj21hc0q2go7.jpg)
>需要注意: 上图中的 page 包含 register 需要与登陆接口一一对应, 之前填写的为 register, 现在也应该是 register
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikyjbleqj21hc0q2q5u.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikymdd9qj21hc0q441d.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikyp53g8j21hc0q176m.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikyssjc3j21ha0pyacf.jpg)
6. 接下来填写 处理措施/添加风险名单 步骤, 此步骤的意义在于, 对符合条件的 IP 进行处理, 例如对此 IP 列为风险名单, 那么业务系统请求 Nebula 风险名单的时候, Nebula 会返回此 IP, 业务系统可以对此 IP 进行不同等级的处理, 例如弹出验证码, 要求重复登陆, 甚至是直接对 此 IP 封禁, 使其无法访问业务系统
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikyxqspyj21ha0pjdio.jpg)
7. 点击保存之后, 在策略管理中, 找到刚刚编写的策略, 依次点击测试, 上线, 使这条策略生效, 请注意, 这是必须做的, 否则该策略无效, 点击上线之后, 稍等一会即可生效.
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikzgw9wsj21h80pvgon.jpg)
8. 最后我们测试注册接口, 在风险事件管理中可以看到成功捕获了风险, 并且对该 IP 列为审核状态
![](http://ww1.sinaimg.cn/large/66d0828fly1g1ikz6ysilj21hc0pvwg3.jpg)

总结: 以上是实时在线计算 5 分钟内统计同一个 IP 恶意注册的策略设置



#### 例子三：IP 爬虫业务系统

>爬虫 词语解释: 网络爬虫（又被称为网页蜘蛛，网络机器人，在FOAF社区中间，更经常的称为网页追逐者），是一种按照一定的规则，自动地抓取万维网信息的程序或者脚本。另外一些不常使用的名字还有蚂蚁、自动索引、模拟程序或者蠕虫

假设同一个IP，使用脚本爬虫不断的访问页面, 爬取业务系统资料，对企业的伤害极高, 在策略中设定某 IP 访问动态资源超过一定的量之后, 这个量的界定在于, 普通用户无法在短时间内达到这么高的量级, 需要根据本身业务系统的量级去调试，那么该 IP 超过量之后, 对该 IP 贴上风险标签, 业务系统通过对 Nebula 风险接口的访问, 得到对此 IP 的风险判定, 对此 IP 进行弹验证码，封 IP 等处理。

>Nebula 查询风险接口: 请查看 Nebula 官方文档中 2.3.7 运营决策篇章, 得到更加详尽的解释

##### 策略的制定
1. 打开 Nebula 网站, 登陆之后出现为以下页面, 然后打开 ① 链接, 根据 Nebula 版本不同, 可能会有一些改变, 大致如下:
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il2rh5emj21hc0pqq6q.jpg)
2. 点击新建策略, 对策略的各个参数进行设定
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il2u49itj21hc0q0q6e.jpg)
3. 填写策略的基本信息, 对策略进行分类
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il2wizpxj21hc0pxjty.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il2zby0bj21h90pwtbf.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il3c0npij21hb0pztbl.jpg)
4. 接下来是对策略的条款进行设定, 这个是核心部分, 请注意, 以【事件-动态资源请求】选定属性 c_ip， c_ip 包含了 . 注意是英文符号 ".", 这表达的意思是, 所有的 IP 都会被作为数据去计算,  之后指定筛选条件, 筛选出符合条件的 IP, 接下来, 会选择条件判断来筛选.
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il3g4rf7j21h60pwju6.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il3nbphbj21hb0q10vp.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il3psfepj21h50pwacw.jpg)
5. 使用条件判断来计算值, 例如设定某 IP 访问同一页面在 5 分钟内获取的动态资源5m 超过 500 次, 则认为此 IP 正在爬取网页信息, 当然这个量的设定可以根据公司的业务去设置, 这部分理解难度可能比较高, 还请先照着图片教程做起来, 慢慢摸索理解
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il3s3yt3j21h60px0wc.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il3utqcoj21hc0pxtbs.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il3x39qkj21h60pwgoj.jpg)
6. 接下来填写 处理措施/添加风险名单 步骤, 此步骤的意义在于, 对符合条件的 IP 进行处理, 例如对此 IP 列为风险名单, 那么业务系统请求 Nebula 风险名单的时候, Nebula 会返回此 IP, 业务系统可以对此 IP 进行不同等级的处理, 例如弹出验证码, 要求重复登陆, 甚至是直接对 此 IP 封禁, 使其无法访问业务系统
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il3z7qbjj21h60q0777.jpg)
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il41wdshj21h80ppadf.jpg)
7. 此时已经完成了策略的配置, 点击'测试' 按钮后再点击 '上线' 按钮, 即可生效, 接下来可以测试 IP撞库 策略
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il445mglj21h80q00vu.jpg)
8. 使用同一个 IP 用脚本不停的爬取网页, 即可在 '总览' 页面得到已触发策略, 且作为风险名单存在系统之中, 可以对 Nebula 的接口进行访问, 得到此 IP 为有风险的 IP
![](http://ww1.sinaimg.cn/large/66d0828fly1g1il46nyc1j21hb0px0v6.jpg)

总结: 以上是实时在线计算 5 分钟内统计同一个 IP 使用脚本爬虫不断的访问页面, 爬取业务系统资料, 捕获且标记为风险 IP 的策略定制

#### 章节语
经过这 3 个策略定制的例子, 对每一步都给出了细致的步骤, 希望能对定制策略者有一些入门级别的帮助, Nebula 这个风控系统的能力不止于此, 希望可以通过这些教程提供一些微小的帮助, 让你可以进入策略定制的大门, 设置一些符合业务系统的需求的策略, 达到帮助抵抗风险的能力.
