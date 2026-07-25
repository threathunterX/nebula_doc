# 3.4. 星云系统配置功能

系统配置功能允许管理员对 TH-Nebula 进行细节调整配置以适应不同的需求，配置包含以下 4 个部分：

**邮件报警**

邮件报警配置可通过内部邮件服务器的方式发送风险事件情况
![](https://i.loli.net/2019/04/04/5ca5ecf89684c.png)为了获得 SMTP 的相关配置，您可以需要向公司内的 IT 部门】申请一个用来发送 TH-Nebula 报警的内部邮件账号并输入在这里。

**网络流量过滤**

企业内部往往存在许多不需要进行分析的网络流量，通过网络流量过滤配置您可以将这些请求通过 IP、端口、域名等方式过滤掉
![](https://i.loli.net/2019/04/04/5ca5ee063906f.png)

**日志显示管理**

经过流量的过滤后，所有的请求都会进入计算，但依然有部分请求是会干扰分析师查看用户行为的，比如静态图片的加载请求，在日志显示管理中你可以将某些干扰分析的请求过滤掉，在过滤后这些请求就不会显示在风险分析的详情中了。
![](http://ww1.sinaimg.cn/large/66d0828fgy1g1p9i1y0bwj20xy0p0q7o.jpg)

**敏感字段加密**

在业务请求中出现敏感字段需要脱敏时，可使用该配置对敏感字段进行 SHA1 加密，这里的字段 指的是query请求中的敏感参数,例如[www.xxx.com/login?password=love123&username=test](http://www.xxx.com/login?password=love123&username=test)
![](http://ww1.sinaimg.cn/large/66d0828fgy1g1p9i5y82lj20xk0h8wh3.jpg)
