# 快速入门

##  系统架构

由`sniffer`抓取流量, `sniffer`由开源库`bro`抓取网卡流量, 之后转换为`event`传送到 `online`, `online`通过解析把`event`传送到`Slot`和`Profile`, `online`通过`Esper`计算引擎提供 5 分钟实时计算, 对页面规则也提供实时更新, `Slot`提供原始`event`存储以及`offline`1个小时的离线计算, 而`Profile`的`labrador`提供 30天的计算. 作为`offline`的补充计算. 系统架构如下图所示: 

![1架构](http://wx4.sinaimg.cn/large/0060lm7Tly1fxnn2plcogj30lk0hlq4v.jpg)


## 工作原理


### sniffer工作原理

`sniffer`主要框架由`Bro`开源库提供支持.

[Bro 的github主页](https://github.com/bro/bro)  
[官网文档](https://www.bro.org/sphinx/intro/index.html)

`sniffer`需要关注的地方有制定脚本这一块, 一般脚本位置处于`sniffer`文件目录下:

```
\nebula_sniffer\nebula_sniffer\customparsers
```

需要注意的是, 如果是`Docker`应该是映射目录.

`sniffer` 把抓取到的流量流到了制定的脚本中, 流量会遍历所有添加的类, 这些类分别对流量进行分类, 如果符合条件则通过生成 `Event` 类传输到下游, 下游包括 `Online`, `Offline`等.

制定的脚本详情可以查看*脚本的定制*章节.


### Online 工作原理

`Online`的工作原理由`Esper`开源框架提供支持

[Esper官网](http://www.espertech.com/)

`CEP`，是一种实时事件处理并从大量事件数据流中挖掘复杂模式的技术，全称为`Complex Event Processing`，即复杂事件处理。`Esper`是`CEP`的一个开源实现，它是一个`Java`开发的事件流处理和复杂事件处理引擎。该引擎可应用于网络入侵探测，`SLA`监测，`RFID`读取，航空运输调控，金融方面(风险管理，欺诈探测)等领域。它的特点是能够快速开发出复杂的实时计算策略，并且有着高吞吐量以及低延迟的特点，特别适合大量数据的实时计算.

`TH-Nebula`后台策略模块实时创建 `Esper` 语句 `EPL`, 由 `Esper` 接收这些监控窗口, 并且根据自定义时间进行计算, 如果符合计算条件则输出结果, 这些结果会在风险事件管理中出现, 可进行深一步的判断.

### Offline 工作原理

暂无
