# 5.1. Sniffer原理及驱动定制



sniffer默认使用的是基于bro流量分析的数据源驱动，但也提供了如：redis、kafka、rabbitmq、logstash、UDPServer、syslog、file等其他数据源驱动的demo供大家参考（无法直接使用，需要根据源数据的格式进行调整）

#### 工作方式
---------
![工作方式.png](https://i.loli.net/2019/04/01/5ca1eae1d280a.png)



#### 生成消息
---------

    **此部分需根据自己业务进行修改定制**


以Nginx + Logstash为例（Nginx输出日志格式配置仅供参考）

```     
1) 行格式
log_format log_line '"$remote_addr" "$remote_port" "$server_addr" "$server_port" "$request_length" \
"$content_length" "$body_bytes_sent" "$request_uri" "$host" "$http_user_agent" "$status" "$http_cookie" \
"$request_method" "$http_referer" "$http_x_forwarded_for" "$request_time" "$sent_http_set_cookie" \
"$content_type" "$upstream_http_content_type" "$request_data"';

例：
<14>Jun 26 17:13:21 10-10-92-198 NGINX[26113]: "114.242.250.233" "65033" "10.10.92.198" "80" "726" "134" \
"9443" "/gateway/shop/getStroeForDistance" "m.lechebang.com" "Mozilla/5.0 (iPhone; CPU iPhone OS 8_1_3
like Mac OS X) AppleWebKit/600.1.4 (KHTML, like Gecko) Mobile/12B466 MicroMessenger/6.1.4 NetType/3G+" "200" \
"-" "POST" "http://m.lechebang.com/webapp/shop/list?cityId=10101&locationId=0&brandTypeId=\
6454&maintenancePlanId=227223&oilInfoId=3906" "0.114"

2) json格式
log_format log_json '{ "@timestamp": "$time_local", '
'"remote_addr": "$remote_addr", '
'"referer": "$http_referer", '
'"request": "$request", '
'"status": $status, '
'"bytes": $body_bytes_sent, '
'"agent": "$http_user_agent", '
'"x_forwarded": "$http_x_forwarded_for", '
'"up_addr": "$upstream_addr",'
'"up_host": "$upstream_http_host",'
'"up_resp_time": "$upstream_response_time",'
'"request_time": "$request_time"'
' }';

例：
{ "@timestamp": "12/Dec/2017:14:30:40 +0800", "remote_addr": "10.88.122.108", "referer": "-", "request": "GET / HTTP/1.1", "status": 304, "bytes":0, "agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36", "x_forwarded": "-", "up_addr": "-","up_host": "-","up_resp_time": "-","request_time": "0.000" }


Nginx日志配置请参考：
    http://nginx.org/en/docs/http/ngx_http_log_module.html#log_format

注：
    日志输出的详细字段、字段顺序、字段名，需要与sniffer中对应驱动的消息格式化处理逻辑一致

```

部署Logstash客户端，抽取Nginx日志输出:
```
#logstash配置示例:
input {
    file {
        path => ['/data/nginx/logs/access.log']
        start_position => "beginning"
        codec => "json"
        tags => ['user']
        type => "nginx"
    }
}
output {
    if [type] == "nginx" {
        #Sniffer logstash驱动模式
        tcp {
            host => "127.0.0.1"
            port => "5044"
            key => "nginx"
            db => "10"
            data_type => "list"
            codec => "line"
        },
        #Sniffer redislist驱动模式
        redis {
            host => "127.0.0.1"
            port => "6379"
            key => "nginx_access_log"
            db => "0"
            data_type => "list"
            codec => "line"
        }
    }
}
```

#### 读取数据
---------

    **此部分需根据自己业务进行修改定制**

修改sniffer.conf
```
#支持同时多源
sources: [logstash,redislist]

#对应redislistdriver.py
redislist:
    driver: redislist
    host: 127.0.0.1
    port: 6379
    interface: any
    instances: 1
    parser:
        name: test
        module: testparser

#对应logstashdriver.py
#其实是TCPServer
logstash:
    driver: logstash
    port: 5044
    instances: 1
    interface: any
    parser:
        name: test
        module: testparser

#对应rabbitmqdriver.py
rabbitmq:
    driver: rabbitmq
    amqp_url: redis://localhost:6379/
    queue_name: test_queue
    exchange_name: test_queue
    exchange_type: direct
    durable: true
    routing_key: test
    instances: 1
    interface: any
    parser:
        name: test
        module: testparser

#对应brohttpdriver.py
default:
    driver: bro
    interface: eth0
    ports: [80, 81, 1080, 3128, 8000, 8080, 8888, 9001, 8081]
    start_port: 48880
    instances: 1
    parser:
        name: test
        module: testparser

#对应syslogdriver.py
syslog:
    driver: syslog
    interface: eth0
    port: 9514
    parser:
        name: test
        module: testparser

#对应kafkadriver.py
kafka:
    driver: kafka
    interface: any
    bootstrap_servers: 127.0.0.1:9092
    parser:
        name: test
        module: testparser

...其他省略...

```

#### 驱动定制(消息格式化)
--------



所有驱动位于:

    目录：
        sniffer/nebula_sniffer/nebula_sniffer/drivers/
        #bro驱动
            brohttpdriver.py
        #基于文件的驱动
            filedriver.py
        #kafka驱动
            kafkadriver.py  
        #logstash驱动
            logstashdriver.py
        #UDPServer驱动
            pktdriver.py
        #rabbitmq驱动
            rabbitmqdriver.py
        #redis驱动
            redislistdriver.py
        #syslog驱动
            syslogdriver.py
            syslogtextdriver.py
        #tshark驱动
            tsharkhttpsdriver.py



sniffer会根据sniffer配置加载对应驱动：

```
def get_driver(config, interface, parser, idx):
    """ global c """

    from complexconfig.configcontainer import configcontainer

    #不同driver，初始化方式不同
    name = config['driver']
    if name == "bro":
        from nebula_sniffer.drivers.brohttpdriver import BroHttpDriver
        embedded = config.get("embedded", True)
        ports = config['ports']
        from nebula_sniffer.utils import expand_ports
        ports = expand_ports(ports)  # extend it
        start_port = int(config['start_port'])
        bpf_filter = config.get("bpf_filter", "")

        home = configcontainer.get_config("sniffer").get_string("sniffer.bro.home")

        if ports and home:
            driver = BroHttpDriver(interface=interface, embedded_bro=embedded, idx=idx, ports=ports, bro_home=home,
                                   start_port=start_port, bpf_filter=bpf_filter)
        elif ports:
            driver = BroHttpDriver(interface=interface, embedded_bro=embedded, idx=idx, ports=ports,
                                   start_port=start_port, bpf_filter=bpf_filter)
        elif home:
            driver = BroHttpDriver(interface=interface, embedded_bro=embedded, idx=idx, bro_home=home,
                                   start_port=start_port, bpf_filter=bpf_filter)
        else:
            driver = BroHttpDriver(interface=interface, embedded_bro=embedded, idx=idx,
                                   start_port=start_port, bpf_filter=bpf_filter)
        return driver

    if name == "tshark":
        from nebula_sniffer.drivers.tsharkhttpsdriver import TsharkHttpsDriver
        interface = interface
        ports = config["ports"]
        bpf_filter = config.get("bpf_filter", "")
        if ports:
            driver = TsharkHttpsDriver(interface=interface, ports=ports, bpf_filter=bpf_filter)
        else:
            driver = TsharkHttpsDriver(interface=interface, bpf_filter=bpf_filter)
        return driver

    if name == "syslog":
        from nebula_sniffer.drivers.syslogdriver import SyslogDriver
        port = int(config["port"])
        driver = SyslogDriver(port)
        return driver

    if name == "packetbeat":
        from nebula_sniffer.drivers.pktdriver import PacketbeatDriver
        port = int(config["port"])
        driver = PacketbeatDriver(port)
        return driver

    if name == "redislist":
        from nebula_sniffer.drivers.redislistdriver import RedisListDriver
        host = config["host"]
        port = int(config['port'])
        password = config.get('password', '')
        driver = RedisListDriver(host, port, password)
        return driver

    if name == "logstash":
        from nebula_sniffer.drivers.logstashdriver import LogstashDriver
        port = int(config['port'])
        driver = LogstashDriver(port)
        return driver

    if name == "rabbitmq":
        from nebula_sniffer.drivers.rabbitmqdriver import RabbitmqDriver
        amqp_url = config['amqp_url']
        queue_name = config['queue_name']
        exchange_name = config['exchange_name']
        exchange_type = config['exchange_type']
        durable = bool(config['durable'])
        routing_key = config['routing_key']
        driver = RabbitmqDriver(amqp_url, queue_name, exchange_name, exchange_type, durable, routing_key)
        return driver

    if name == "kafka":
        from nebula_sniffer.drivers.kafkadriver import KafkaDriver
        topics = config['topics']
        #config['bootstrap_servers']
        #kafka支持的配置参数
        #请参考python kafka库的使用方法
        driver = KafkaDriver(topics, **config)
        return driver

    return None
```


下面是logstash驱动示例：

```
#其他部分代码略过，仅贴出核心代码
#此处示例是对nginx json格式log的处理

class LogstashDriver(Driver):

    ...其他省略...

    #对logstash客户端发送过来的消息进行格式化
    def _recv_msg_fn_in(self, msg, addr):
        """
        log-line：
        36.7.130.69 - [16/Jul/2017:23:58:42 +0800] ffp.hnair.com 1 "GET /FFPClub/upload/index/e9b1bb4a-e1dd-47e1-8699-9828685004b4.jpg HTTP/1.1" 200 487752 - "http://ffp.hnair.com/FFPClub/cn/index.html" "Mozilla/5.0 (Windows NT 6.1; Trident/7.0; rv:11.0; NetworkBench/8.0.1.309-4992258-2148837) like Gecko" "-"

        log-json：
        {"@timestamp_scs": "2017-09-21T12:18:17+08:00", "scs_request_uri": "/site-wap/my/transferin.htm",
         "scs_status": "200", "scs_bytes_sent": "26829", "scs_upstream_cache_status": "-", "scs_request_time": "0.570",
         "scs_upstream_response_time": "0.570", "scs_host": "pay.autohome.com.cn", "scs_remote_addr": "10.20.2.23",
         "scs_server_addr": "10.20.252.33", "scs_upstream_addr": "10.20.252.20:8253", "scs_upstream_status": "200",
         "scs_http_referer": "https://pay.autohome.com.cn/site-wap/activity/upin.htm?__sub_from=A2002027782510100",
         "scs_http_user_agent": "Mozilla/5.0 (Linux; Android 5.1; OPPO A59m Build/LMY47I; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/43.0.2357.121 Mobile Safari/537.36 autohomeapp/1.0+%28auto_android%3B8.4.0%3BOi-CR_rnyywoODk5jv0ve5luKLNxl7AfnEsGsBBPNdWdDtP8ZDdRHA3ePFiDlWOr%3B5.1%3BOPPO%2BA59m%29 auto_android/8.4.0 nettype/wifi",
         "scs_http_X_Forwarded_For": "220.166.199.167"}

        """

        try:
            if not msg:
                return
            msg = msg.strip()
            if not msg:
                return
            self.logger.debug("get log msg %s from address %s", msg, addr)

            #收到消息后,根据消息格式进行解析处理
            try:
                msg = json.loads(msg)
            except Exception as e:
                return

            #从消息中提取字段数据
            c_ip = msg.get('scs_http_X_Forwarded_For', '')
            if c_ip:
                c_ip_group = c_ip.split(',')
                if c_ip_group:
                    c_ip = c_ip_group[-1]
            c_port = 0
            s_port = 80
            c_bytes = 0
            s_bytes = msg.get('scs_bytes_sent', 0)
            if s_bytes == '-':
                s_bytes = 0
            else:
                s_bytes = int(s_bytes)
            status = int(msg.get('scs_status', 0))
            req_body = ''

            args = dict()
            args["method"] = 'GET'
            args["host"] = msg.get('scs_host', '').lower()
            args["uri"] = msg.get('scs_request_uri', '').lower()
            args["referer"] = msg.get('scs_http_referer', '').lower()
            args["user_agent"] = msg.get('scs_http_user_agent', '').lower()
            args["status_code"] = status
            args["status_msg"] = ""
            args["source_ip"] = c_ip
            args["source_port"] = c_port
            args["dest_ip"] = ''
            args["dest_port"] = s_port

            request_time = 0.0
            try:
                ctime = msg['@timestamp_scs']
                ctime = ctime.replace('T', ' ').replace('+08:00', '')
                time_array = time.strptime(ctime, "%Y-%m-%d %H:%M:%S")
                # 转换成时间戳
                request_time = time.mktime(time_array)
            except Exception as e:
                pass

            args["req_time"] = int(request_time * 1000)

            # headers
            args["req_headers"] = {}

            args["resp_headers"] = {}

            # no body for logstash
            args["log_body"] = False
            args["req_body"] = ""
            args["resp_body"] = ""
            args["req_body_len"] = c_bytes
            args["resp_body_len"] = s_bytes
            args["req_content_type"] = ''
            args["resp_content_type"] = ''
            args["req_body"] = req_body

            args["debug_processing"] = False

            self.logger.debug("get http data from logstash: %s", args)

            try:
                #最终格式化为Httpmsg格式
                new_msg = HttpMsg(**args)
            except BeFilteredException as bfe:
                return
            except Exception as err:
                self.logger.debug("fail to parse: %s", args)
                return

            self.logger.debug("get http msg from logstash: %s", new_msg)

            #丢到队列，进行事件提取
            self.put_msg(new_msg)
            self.count += 1
            if self.count % 1000 == 0:
                print "has put {}".format(self.count)
            return new_msg

        except Exception as ex:
            self.logger.error("fail to parse logstash data: %s", ex)

    ...其他省略...

```

#### 事件提取
--------

    **通用模块，无需修改定制**


```
#源码：
#   sniffer/nebula_sniffer/nebula_sniffer/main.py

class Main(object):
        def event_processor(self):
            ...其他省略...

            events = []
            if isinstance(msg, HttpMsg):
                # 对http信息进行处理，返回一个events（事件列表）
                events = self.parser.get_events_from_http_msg(msg)

            ...其他省略...
```


#### 输出事件
--------

    **通用模块，无需修改定制**
