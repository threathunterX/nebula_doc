# 5.3. Sniffer测试以及debug


* bro模式
    * 成功的启动日志
    * bro的排查流程
* kakfa模式
    * 成功启动的日志
    * kafka的排查流程
* 配置验证程序
    * 安装方式
    * 可以检查的事项
    * 结果验证

Bro 模式

#### 成功的启动日志
正确的启动提示 bro 模式
```
>docker-compose up
/usr/lib/python2.7/site-packages/requests/__init__.py:80: RequestsDependencyWarning: urllib3 (1.22) or chardet (2.2.1) doesn't match a supported version!
RequestsDependencyWarning)
Creating nebula_sniffer_9003 ... done
Attaching to nebula_sniffer_9003
nebula_sniffer_9003 | No handlers could be found for logger "config"
nebula_sniffer_9003 | global conf path using the local path /home/nebula_sniffer/conf
nebula_sniffer_9003 | sniffer conf path using the local path /home/nebula_sniffer/conf
nebula_sniffer_9003 | creat logger nebula.produce
nebula_sniffer_9003 | creat logger nebula.sniffer
nebula_sniffer_9003 | logging debug level is 'debug'
nebula_sniffer_9003 | 2019-06-19 17:05:16: starting sniffer
nebula_sniffer_9003 | 2019-06-19 17:05:16: start to init config
nebula_sniffer_9003 | 2019-06-19 17:05:17: successfully loaded the file config from /home/nebula_sniffer/conf/sniffer.conf
nebula_sniffer_9003 | WebLoader: web_config_loader, sniffer.web_config.config_url:http://127.0.0.1:9001/platform/config, params:{'auth': '1ac1a08630d68a2fdd0b719d5c07f915'}
nebula_sniffer_9003 | 2019-06-19 17:05:17: successfully loaded the web config from http://127.0.0.1:9001/platform/config
nebula_sniffer_9003 | 2019-06-19 17:05:17: successfully loaded config
nebula_sniffer_9003 | 2019-06-19 17:05:17: start to init sentry
nebula_sniffer_9003 | 2019-06-19 17:05:18: start to init Produce
nebula_sniffer_9003 | 2019-06-19 17:05:18: start to init redis
nebula_sniffer_9003 | 2019-06-19 17:05:18: successfully init redis[host=127.0.0.1,port=36379,password=]
nebula_sniffer_9003 | 2019-06-19 17:05:18: start to init metrics
nebula_sniffer_9003 | 2019-06-19 17:05:18: successfully initializing metrics with config {'redis': {'host': '127.0.0.1', 'type': 'redis', 'port': 36379}, 'server': 'redis'}
nebula_sniffer_9003 | 2019-06-19 17:05:18: successfully init auto parsers, event from: http://127.0.0.1:9001/platform/event_models, parser from:
nebula_sniffer_9003 | 2019-06-19 17:05:18: start to processing
nebula_sniffer_9003 | creat logger sniffer.httpmsg
nebula_sniffer_9003 | creat logger sniffer.parser.defaultparser
nebula_sniffer_9003 | creat logger sniffer.driver.bro.1
nebula_sniffer_9003 | *** failed to set config parameter work-stealing.moderate-sleep-duration-us: invalid name
nebula_sniffer_9003 | *** failed to set config parameter work-stealing.relaxed-sleep-duration-us: invalid name
nebula_sniffer_9003 | listening on eth0
nebula_sniffer_9003 |
nebula_sniffer_9003 | bro server start on 127.0.0.1:47001
nebula_sniffer_9003 | receive update_staticresourcesuffix=gif,png,ico,css,js,csv,txt,jpeg,jpg,woff,ttf
nebula_sniffer_9003 | receive update_filteredhosts=
nebula_sniffer_9003 | receive update_filteredurls=
nebula_sniffer_9003 | receive update_filteredservers=
```
#### bro的排查流程
启动日志有一些 warning 没啥关系, 这些是 bro 引擎中的
可以在启动提示中首先确认


1. 启动的模式为 debug 那么在调试模式下, 有很一些更多额外的日志输出到 logs 文件夹中的日志, 当然日志占用的空间也会越来越多, 比较需要关注的是 sniffer.parser.defaultparser 当你开debug模式的时候, 流量的详情会写入其中, 需要注意的是, 流量巨大的情况记得关了debug模式, 因为日志会非常巨大
2. 主要看创建了 nebula.produce nebula.sniffer sniffer.httpmsg sniffer.parser.defaultparser sniffer.driver.bro.1 几个日志, 日志对应的目录是 /home/nebula_sniffer/logs/ 中, 可以到里面查看详情
3. 查看redis链接情况, redis的作用是将sniffer捕获的流量输出到nebula中, 那么 redis 的链接情况就很重要, 如果没有链接成功, 那么系统就不会计算流量是否是威胁流量, redis 成功链接的日志标志性为 successfully init redis[host=127.0.0.1,port=36379,password=] 这一条
4. 查看sniffer与neubla的链接情况, nebula中有很多配置是实时加载到了sniffer, 也就是你在nebula的web系统中做出了修改, 那么sniffer的配置同样及时刷新, 那么sniffer链接到nebula就很重要, sniffer 链接 nebula 成功链接的日志标志性为 successfully loaded the web config from http://127.0.0.1:9001/platform/config
5. 更加自由高级的解析流量, Produce 这个功能, 当然如果是刚开始使用nebula, 没必要去深究这个功能, 他的启动日志如下:nebula_sniffer_9003 | 2019-06-19 17:05:18: start to processing, 在 logs 中有记录, 可以查看它是否有错误.kakfa模式

kafka 模式

#### 成功启动的日志
成功链接kafka模式的启动日志
```
[root@test-02 /home/wei/sniffer]# docker-compose up
/usr/lib/python2.7/site-packages/requests/__init__.py:80: RequestsDependencyWarning: urllib3 (1.22) or chardet (2.2.1) doesn't match a supported version!
RequestsDependencyWarning)
Starting nebula_sniffer_9003 ... done
Attaching to nebula_sniffer_9003
nebula_sniffer_9003 | No handlers could be found for logger "config"
nebula_sniffer_9003 | global conf path using the local path /home/nebula_sniffer/conf
nebula_sniffer_9003 | sniffer conf path using the local path /home/nebula_sniffer/conf
nebula_sniffer_9003 | creat logger nebula.produce
nebula_sniffer_9003 | creat logger nebula.sniffer
nebula_sniffer_9003 | logging debug level is 'debug'
nebula_sniffer_9003 | 2019-06-19 17:34:35: starting sniffer
nebula_sniffer_9003 | 2019-06-19 17:34:35: start to init config
nebula_sniffer_9003 | 2019-06-19 17:34:35: successfully loaded the file config from /home/nebula_sniffer/conf/sniffer.conf
nebula_sniffer_9003 | WebLoader: web_config_loader, sniffer.web_config.config_url:http://127.0.0.1:9001/platform/config, params:{'auth': '1ac1a08630d68a2fdd0b719d5c07f915'}
nebula_sniffer_9003 | 2019-06-19 17:34:35: successfully loaded the web config from http://127.0.0.1:9001/platform/config
nebula_sniffer_9003 | 2019-06-19 17:34:35: successfully loaded config
nebula_sniffer_9003 | 2019-06-19 17:34:35: start to init sentry
nebula_sniffer_9003 | 2019-06-19 17:34:35: start to init Produce
nebula_sniffer_9003 | 2019-06-19 17:34:35: start to init redis
nebula_sniffer_9003 | 2019-06-19 17:34:35: successfully init redis[host=127.0.0.1,port=36379,password=]
nebula_sniffer_9003 | 2019-06-19 17:34:35: start to init metrics
nebula_sniffer_9003 | 2019-06-19 17:34:35: successfully initializing metrics with config {'redis': {'host': '127.0.0.1', 'type': 'redis', 'port': 36379}, 'server': 'redis'}
nebula_sniffer_9003 | 2019-06-19 17:34:35: successfully init auto parsers, event from: http://127.0.0.1:9001/platform/event_models, parser from:
nebula_sniffer_9003 | 2019-06-19 17:34:35: start to processing
nebula_sniffer_9003 | creat logger sniffer.httpmsg
nebula_sniffer_9003 | creat logger sniffer.parser.defaultparser
nebula_sniffer_9003 | creat logger sniffer.driver.kafka
```
#### kafka的排查流程
无法链接kafka, 解决办法


1. 自行测试好 kafka 的服务, 检查kafka的配置, 需要注意的是 sniffer 需要用的是 nebula_nginx_lua 这个频道, groupid 是 nebula
2. 在nebula的 conf 配置中修改sniffer的配置

vim nebula_sniffer/conf/sniffer.conf
修改第一行
sources: [kafka] 为 sources: [default]
这样使用了 bro 默认的抓取流量引擎
重新启动容器即可

1. kafka 的排查链接是否成功的一些方法

首先下载好 kafka 装好 java 环境
下载
wget http://apache.lauf-forum.at/kafka/2.2.1/kafka_2.11-2.2.1.tgz
解压
> tar -xzf kafka_2.11-1.1.0.tgz
> cd kafka_2.11-1.1.0
在配置中指定启动的端口以及 zookeeper 的端口
默认是 9092
在kafka文件夹目录消费消息
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic nebula_nginx_lua —from-beginning
如果有消息, 那么就是可以正常链接的

1. 启动失败, 无法链接 kafka 会出现以下日志

```
[root@test-02 /home/wei/sniffer]# docker-compose up
/usr/lib/python2.7/site-packages/requests/__init__.py:80: RequestsDependencyWarning: urllib3 (1.22) or chardet (2.2.1) doesn't match a supported version!
RequestsDependencyWarning)
Creating nebula_sniffer ... done
Attaching to nebula_sniffer
nebula_sniffer | No handlers could be found for logger "config"
nebula_sniffer | using the local path /home/nebula_sniffer/conf
nebula_sniffer | using the local path /home/nebula_sniffer/conf
nebula_sniffer | creat logger nebula.produce
nebula_sniffer | creat logger nebula.sniffer
nebula_sniffer | WebLoader: web_config_loader, sniffer.web_config.config_url:http://127.0.0.1:9001/platform/config, params:{'auth': '1ac1a08630d68a2fdd0b719d5c07f915'}
nebula_sniffer | creat logger sniffer.httpmsg
nebula_sniffer | creat logger sniffer.parser.defaultparser
nebula_sniffer | creat logger sniffer.driver.kafka
nebula_sniffer | Process Process-1:
nebula_sniffer | Traceback (most recent call last):
nebula_sniffer | File "/usr/lib64/python2.7/multiprocessing/process.py", line 258, in _bootstrap
nebula_sniffer | self.run()
nebula_sniffer | File "/usr/lib64/python2.7/multiprocessing/process.py", line 114, in run
nebula_sniffer | self._target(*self._args, **self._kwargs)
nebula_sniffer | File "/home/nebula_sniffer/sniffer.py", line 131, in run_task
nebula_sniffer | main.start()
nebula_sniffer | File "/home/nebula_sniffer/nebula_sniffer/main.py", line 71, in start
nebula_sniffer | self.driver.start()
nebula_sniffer | File "/home/nebula_sniffer/nebula_sniffer/drivers/kafkadriver.py", line 138, in start
nebula_sniffer | self.consumer = KafkaConsumer(self.topics,**self.config)
nebula_sniffer | File "/usr/lib/python2.7/site-packages/kafka/consumer/group.py", line 353, in __init__
nebula_sniffer | self._client = KafkaClient(metrics=self._metrics, **self.config)
nebula_sniffer | File "/usr/lib/python2.7/site-packages/kafka/client_async.py", line 239, in __init__
nebula_sniffer | self.config['api_version'] = self.check_version(timeout=check_timeout)
nebula_sniffer | File "/usr/lib/python2.7/site-packages/kafka/client_async.py", line 865, in check_version
nebula_sniffer | raise Errors.NoBrokersAvailable()
nebula_sniffer | NoBrokersAvailable: NoBrokersAvailable
nebula_sniffer | creat logger main.client-any-1
nebula_sniffer | terminating
nebula_sniffer exited with code 0
```


配置验证程序

#### 安装方式

1. 将 sniffer_nebula_test.py requirements.txt 放置在 sniffer 文件夹中
2. 安装依赖:
```
pip3 install -r requirements.txt

requirements.txt 内容
certifi==2019.6.16
chardet==3.0.4
idna==2.8
kafka==1.3.5
numpy==1.16.4
pandas==0.24.2
python-dateutil==2.8.0
pytz==2019.1
PyYAML==5.1.1
redis==3.2.1
requests==2.22.0
six==1.12.0
urllib3==1.25.3
```
1. python3 sniffer_nebula_test.py
2. 输入监控的网址接口or IP地址端口, 输入的ip端口是 openresty nginx 监控的流量到lua脚本, lua脚本到kafka)

#### 可以检查的事项


1. 检查配置文件
2. 检查是否打开了debug模式, debug会生成更多的日志, 占据硬盘空间, 需要注意使用
3. 抓取流量的源(sources mode)
4. 检查nebula_web是否可以访问, 这关系到sniffer拉取nebula的配置, 以及登录后的操作
5. 检查redis是否可以读取, 检查是否可以连接, 并且写入读取数据
6. 如果 sources mode 是 kafka, 检查是否可以连接, 并且写入读取数据
7. 如果 sources mode 是 kafka, 检查openresty nginx 是否工作, 把数据通过lua脚本输出到 kafka
8. 如果 sources mode 是 bro(default), 目前的程序无法轻易检测到, 需要到日志中分析启动情况

#### 结果验证
结果验证如下, 这样的结果是正常的. 如果有错误, 会有相应的提示, 改动配置然后再次检查就可以了
![ZexLNR.png](https://s2.ax1x.com/2019/06/26/ZexLNR.png)



