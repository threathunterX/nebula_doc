# 5.2. Sniffer nginx kafka驱动支持

* 了解 openresty
* 安装 openresty
* 测试 openresty 是否正常
* 包含新的 nginx
    * 安装 openresty-kafka 模块
    * 导入 kafka.lua 等依赖
    * 修改 kafka.lua 中的配置
* 原机有 nginx    
    * 安装 lua
    * 重新编译 nginx 的环境依赖
    * 导入 kafka.lua 等依赖
    * 修改 kafka.lua 中的配置
    * 导入 openresty 依赖
    * 安装 lua-resty-kafka 模块

* nginx 的配置
    * 新的 nginx 配置
    * 原有 nginx 配置
* 解释如何收集流量以及计算
* sniffer 相关配置
* 排查错误

### 了解 openresty
----
OpenResty由 Nginx 核心加很多第三方模块组成，默认集成了Lua开发环境，使得Nginx可以作为一个Web Server使用. Nginx有很多的特性和好处，但是在Nginx上开发成了一个难题，Nginx模块需要用C开发，而且必须符合一系列复杂的规则，最重要的用C开发模块必须要熟悉Nginx的源代码，使得开发者对其望而生畏。为了开发人员方便，所以接下来我们要介绍一种整合了Nginx和lua的框架，那就是OpenResty，它帮我们实现了可以用lua的规范开发，实现各种业务，并且帮我们弄清楚各个模块的编译顺序.

### 安装 openresty
----
安装依赖
yum install readline-devel pcre-devel openssl-devel gcc

--1. 下载openresty源码： http://openresty.org/cn/download.html
$ wget https://openresty.org/download/openresty-1.15.8.1rc1.tar.gz


-- 2. 解压tar包
$ tar -zxvf openresty-1.15.8.1rc1.tar.gz

-- 3. 配置编译选项，可以根据你的实际情况增加、减少相应的模块
$ ./configure --prefix=/opt/openresty --with-luajit --without-http_redis2_module --with-http_iconv_module

-- 4. 编译并安装
$ make
$ make install
### 测试安装是否正常
-- 1. 修改配置文件如下：
$ cat /opt/openresty/nginx/conf/nginx.conf
worker_processes  1;
error_log logs/error.log info;


events {
    worker_connections 1024;
}


http {
    server {
        listen 8003;


        location / {
            content_by_lua 'ngx.say("hello world.")';
        }
    }
}


-- 2. 启动nginx
$ /opt/openresty/nginx/sbin/nginx


-- 3. 检查nginx
$ curl http://127.0.0.1:8003/
hello world.

出现 hello world 则是正常的


### 包含新的 nginx

#### 安装 openresty-kafka 模块
1. 首先在 github 上下载开源的 lua-resty-kafka
wget https://github.com/doujiang24/lua-resty-kafka/archive/v0.06.tar.gz
2. 然后解压
tar -zxvf v0.06.tar.gz
3. 到以下目录 复制
cd lua-resty-kafka-0.06/lib/resty
cp -rf kafka /opt/openresty/lualib/resty/
这个目录是openresty的安装目录中的resty 请自行判断, 你安装在哪个位置, resty就对应在哪个位置
4. 这样就完成了

#### 导入 kafka.lua 等依赖
从nginx得到的流量转化成sniffer适用的格式, 先导入kafka.lua脚本
kafka.lua 的位置在 sniffer 的 lua 目录中
导入的位置是 /opt/openresty/lualib/

#### 修改 kafka.lua 中的配置
在 kafka 脚本中的 kafka 的 IP 以及端口需要更改
vim kafka.lua, 修改以下地址

-- 定义kafka broker地址
local broker_list = {
    { host = "172.x.x.x", port = 9092 },
}

### 原机有 nginx    

#### 安装 lua
实际上在下载 openresty 安装包的时候，里面其实已经依赖了lua了，只需要安装就好了
到解压 openresty 安装包中

cd bundle/LuaJIT-2.1-20190228
如果版本不一致就是对一下 LuaJIT 的文件夹就好了

make

make install

安装好的环境依赖如下
```
==== Installing LuaJIT 2.1.0-beta3 to /usr/local ====
mkdir -p /usr/local/bin /usr/local/lib /usr/local/include/luajit-2.1 /usr/local/share/man/man1 /usr/local/lib/pkgconfig /usr/local/share/luajit-2.1.0-beta3/jit /usr/local/share/lua/5.1 /usr/local/lib/lua/5.1
cd src && install -m 0755 luajit /usr/local/bin/luajit-2.1.0-beta3
cd src && test -f libluajit.a && install -m 0644 libluajit.a /usr/local/lib/libluajit-5.1.a || :
rm -f /usr/local/lib/libluajit-5.1.so.2.1.0 /usr/local/lib/libluajit-5.1.so /usr/local/lib/libluajit-5.1.so.2
cd src && test -f libluajit.so && \
  install -m 0755 libluajit.so /usr/local/lib/libluajit-5.1.so.2.1.0 && \
  ldconfig -n /usr/local/lib && \
  ln -sf libluajit-5.1.so.2.1.0 /usr/local/lib/libluajit-5.1.so && \
  ln -sf libluajit-5.1.so.2.1.0 /usr/local/lib/libluajit-5.1.so.2 || :
cd etc && install -m 0644 luajit.1 /usr/local/share/man/man1
cd etc && sed -e "s|^prefix=.*|prefix=/usr/local|" -e "s|^multilib=.*|multilib=lib|" luajit.pc > luajit.pc.tmp && \
  install -m 0644 luajit.pc.tmp /usr/local/lib/pkgconfig/luajit.pc && \
  rm -f luajit.pc.tmp
cd src && install -m 0644 lua.h lualib.h lauxlib.h luaconf.h lua.hpp luajit.h /usr/local/include/luajit-2.1
cd src/jit && install -m 0644 bc.lua bcsave.lua dump.lua p.lua v.lua zone.lua dis_x86.lua dis_x64.lua dis_arm.lua dis_arm64.lua dis_arm64be.lua dis_ppc.lua dis_mips.lua dis_mipsel.lua dis_mips64.lua dis_mips64el.lua vmdef.lua /usr/local/share/luajit-2.1.0-beta3/jit
ln -sf luajit-2.1.0-beta3 /usr/local/bin/luajit
==== Successfully installed LuaJIT 2.1.0-beta3 to /usr/local ====
```

### 重新编译 nginx 的环境依赖
首先以下的相关依赖都是根据 nginx 默认的安装目录, 依赖去配置的, 如果有不同的地方, 需要自己配置一下.
```
nginx 默认配置目录
  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
```
下载 nginx 源码编译
```
wget http://nginx.org/download/nginx-1.16.0.tar.gz

cd nginx-1.16.0

export LUAJIT_LIB=/usr/local/openresty/lualib/ export LUAJIT_INC=/usr/local/openresty/luajit/include/luajit-2.1/

重新配置依赖 要注意的是 openresty 之前放置的目录位置 是 /home/root
./configure --prefix=/usr/local/nginx --with-cc-opt=-O2 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_devel_kit-0.3.1rc1 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/echo-nginx-module-0.61 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/xss-nginx-module-0.06 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_coolkit-0.2 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/set-misc-nginx-module-0.32 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/form-input-nginx-module-0.12 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/encrypted-session-nginx-module-0.08 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/srcache-nginx-module-0.31 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_lua-0.10.14 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_lua_upstream-0.07 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/headers-more-nginx-module-0.33 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/array-var-nginx-module-0.05 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/memc-nginx-module-0.19 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/redis2-nginx-module-0.15 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/redis-nginx-module-0.3.7 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/rds-json-nginx-module-0.15 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/rds-csv-nginx-module-0.09 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_stream_lua-0.0.6 --with-ld-opt=-Wl,-rpath,/usr/local/lib/ --with-stream --with-stream_ssl_module --with-http_ssl_module

参数解释:
–prefix=/usr/local/nginx：nginx安装目录
–add-module=/home/root/openresty-1.15.8.1rc1/bundle/：这个是刚刚下载的openresty安装包
–with-ld-opt=-Wl,-rpath,/usr/local/lib/：lua安装的路径，上面lua安装的时候，默认是这个位
置的

重新编译
make

但切记不要 make install, 否则就覆盖了原有的nginx.
但切记不要 make install, 否则就覆盖了原有的nginx.
但切记不要 make install, 否则就覆盖了原有的nginx.

使用 objs/nginx -V 检验依赖是否正确

nginx version: nginx/1.16.0
built by gcc 7.3.0 (Ubuntu 7.3.0-27ubuntu1~18.04)
built with OpenSSL 1.1.0g  2 Nov 2017
TLS SNI support enabled
configure arguments: --prefix=/usr/local/nginx --with-cc-opt=-O2 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_devel_kit-0.3.1rc1 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/echo-nginx-module-0.61 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/xss-nginx-module-0.06 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_coolkit-0.2 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/set-misc-nginx-module-0.32 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/form-input-nginx-module-0.12 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/encrypted-session-nginx-module-0.08 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/srcache-nginx-module-0.31 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_lua-0.10.14 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_lua_upstream-0.07 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/headers-more-nginx-module-0.33 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/array-var-nginx-module-0.05 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/memc-nginx-module-0.19 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/redis2-nginx-module-0.15 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/redis-nginx-module-0.3.7 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/rds-json-nginx-module-0.15 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/rds-csv-nginx-module-0.09 --add-module=/home/root/openresty-1.15.8.1rc1/bundle/ngx_stream_lua-0.0.6 --with-ld-opt=-Wl,-rpath,/usr/local/lib/ --with-stream --with-stream_ssl_module --with-http_ssl_module

目前的依赖情况是这样的

备份之前的 nginx
cd /usr/local/nginx/sbin/
cp ./nginx ./nginx.old

覆盖 nginx
cd /home/root/nginx-1.16.0/objs
cp ./nginx  /usr/local/nginx/sbin/

覆盖之后测试
cd /usr/local/nginx/sbin

./nginx -t
可以得到是否有问题

./nginx 启动

测试 lua 模块

cd /usr/local/nginx

mkdir lua
cd lua
vim hello.lua
写入
ngx.log(ngx.ERR,"hello");

在nginx文件中更改

location / {
        root /workspace/hexo/public/; index index.html index.htm; access_by_lua_file lua/hello.lua;
}
重启nginx
访问nginx监听的端口, 去查看错误日志, 正常的情况下是可以看到以下的结果

2019/05/09 11:39:26 [error] 14225#0: *1 [lua] hello.lua:1: hello, client: 127.0.0.1, server: localhost, request: "GET / HTTP/1.1", host: "127.0.0.1"

```


### 导入 kafka.lua 等依赖
```
cd /usr/local/nginx/
mkdir lua

导入路径为 /usr/local/nginx/lua
总共 1 个文件
从 sniffer 的 lua 目录中 导入
```

#### 修改 kafka.lua 中的配置
在 kafka 脚本中的 kafka 的 IP 以及端口需要更改
vim kafka.lua, 修改以下地址

-- 定义kafka broker地址
local broker_list = {
    { host = "172.x.x.x", port = 9092 },
}
### 导入 openresty 依赖

首先安装 openresty 的目录是 /opt/openresty 所以以下命令都是按这个命令来了, 如果你是安装在其他地方, 请注意一下

cp -rf /opt/openresty/lualib/resty  /usr/local/share/luajit-2.1.0-beta3/

如果不行请确认以下 luajit 的版本, 基本不会有啥问题

### 安装 lua-resty-kafka 模块

1. 首先在 github 上下载开源的 lua-resty-kafka
wget https://github.com/doujiang24/lua-resty-kafka/archive/v0.06.tar.gz
2. 然后解压
tar -zxvf v0.06.tar.gz
3. 到以下目录 复制
cd lua-resty-kafka-0.06/lib/resty
cp -rf kafka  /usr/local/share/luajit-2.1.0-beta3/resty/

这个目录是 luajit-2.1.0-beta3/resty/ 请自行判断, 你安装在哪个位置, resty就对应在哪个位置
4. 这样就完成了



### nginx 的配置
#### 新的 nginx 配置
1. 接下来需要修改nginx配置来启用 kafka.lua 脚本
2. 首先需要开启错误日志 找到#error_log  logs/error.log; 将 '#' 删除
3. 然后需要在server中增加参数
        set $httplog_to_kafka 1;
        set $lualib_path  /opt/openresty/lualib;
4. 以及将流量转发到 lua 中
if ( $httplog_to_kafka ) {
                #content_by_lua 'ngx.print("run kafka.lua.")';
                log_by_lua_file $lualib_path/kafka.lua;
}
5. 以上是简单版, 纯属解释以下, 上个完整版方便你自己的 nginx 对照
```
#user  nobody;
worker_processes  1;

error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       8003;


        #charset koi8-r;

        #access_log  logs/host.access.log  main;
        set $nginx_metrics_enable 0;
        set $httplog_to_kafka 1;
        set $lualib_path  /opt/openresty/lualib;
        location / {
            if ( $httplog_to_kafka ) {
                log_by_lua_file $lualib_path/kafka.lua;
            }
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```


#### 原有 nginx 配置
关键的地方是
```
log_by_lua_file lua/kafka.lua;
```
运行的目录是 lua 中的 kafka.lua
导入的模块位置是 /usr/local/share/luajit-2.1.0-beta3/resty/

```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
            if ( $httplog_to_kafka ) {
                log_by_lua_file lua/kafka.lua;
            }
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```


### 解释如何收集流量以及计算
这部分相对比较简单了, 就是解释一下流量是如何流入到计算出风险的, 首先流量是先经过 nginx 的, 而 openresty 是一个扩展模块, 支持 lua 脚本, 这时候流量会经过我们设定好的 lua 脚本, lua脚本通过设定的分析, 归类为一个结构体, 当然这个结构体是为了适应 sniffer 所支持的格式. 具体可以看 kafka.lua 这个文件中的代码, 也可以看依赖的其他 lua 文件. kafka.lua 这个文件中, 最终把流量解析成一定的格式发送到了 kafka, 然后 sniffer 通过 docker-compose 配置的 kafka,  sniffer 中的 kafka 驱动得到启动, 从 kafka 获得这些流量, 将这些流量处理之后, 发送到了计算模块.

### sniffer 相关配置

```
version: "2"

services:
 nebula_sniffer:
  image : threathunterx/nebula_sniffer:latest
  container_name: nebula_sniffer_9003
  network_mode: "host"
  volumes:
   - ./logs:/home/nebula_sniffer/logs
  environment:
   - DEBUG=True
   - REDIS_HOST=127.0.0.1
   - REDIS_PORT=36379
   - NEBULA_HOST=127.0.0.1
   - NEBULA_PORT=9003
   - SOURCES=kafka
   #bro
   - DRIVER_INTERFACE=eth0
   - DRIVER_PORT=80,8080,9003
   - BRO_PORT=47000
   #kafka
   - BOOTSTRAP_SERVERS=127.0.0.1:9092
   - TOPICS=nebula_nginx_lua
   - GROUP_ID=nebula
```

其实关键的地方在于
1. BOOTSTRAP_SERVERS=kafka 的 IP 和端口
2. SOURCES=kafka 那么 sniffer 是按 kafka 驱动来获得流量的


### 排查错误
排查错误, 主要的错误在于 nginx 与 openresty lua 的程序上, 所以需要在 nginx 上开启 error 日志记录, 上面的教程中有写, 非常的简单. 如果流量进来, 通过 lua 脚本运行有什么错误, 都会在 error.log 日志中体现, 如果没有错误, 那流量在 nginx 这端基本上没有什么问题.

如果在 snffer 还是没办法获取到流量, 那么还是要通过排查 kafka sniffer 方面的日志来解决.
