# 2.2.2. 二进制安装

## Docker 安装
### Docker 安装
安装一些必要的系统工具：

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

添加软件源信息：

```
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

更新 yum 缓存：

```
sudo yum makecache fast
```

安装 Docker-ce：

```
sudo yum -y install docker-ce
```

至此已经安装完 Docker-ce 了, 请输入以下命令查看安装是否完成

```
docker -v
```

正确的提示, 版本号可能有所不同

```
Docker version 18.09.0, build 4d60db4
```

![2141eb541d4d8721c.png](http://www.z4a.net/images/2018/12/06/2141eb541d4d8721c.png)

设置Docker开机自启：

```
sudo systemctl enable docker
```

### docker-compose 安装

接下来安装 docker-compose, 首先更新 curl 工具

```
yum update curl
```

然后下载 docker-compose 并安装, 只需要执行以下命令即可

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

![docker-compose success](http://www.z4a.net/images/2018/12/06/1f1ac0f349eef4d18.png)

加入执行权限:

```
sudo chmod +x /usr/local/bin/docker-compose
```

接下来输入以下命令验证, 可能会出现以下情况

```
docker-compose -v
```

![32f4a2ce3aca7acbc.png](http://www.z4a.net/images/2018/12/06/32f4a2ce3aca7acbc.png)

可以进入到 /usr/local/bin/ 查看 docker-compose 是否已经存在, 并执行以下命令验证

```
cd  /usr/local/bin/
./docker-compose -v
```

![44da24b4eda922b77.png](http://www.z4a.net/images/2018/12/06/44da24b4eda922b77.png)

实际上在其他地方无法使用 docker-compose 是环境变量的问题, 只要添加环境变量即可, 首先编辑以下文件

```
vim /etc/profile
```

然后在最后一行添加以下环境变量, 如下所示(以及附带图片展示)

```
export PATH="/usr/local/bin/:$PATH"
```

![5ed2c12c5f1d2b350.png](http://www.z4a.net/images/2018/12/06/5ed2c12c5f1d2b350.png)

然后输入以下命令使配置文件生效, 接下来就可以使用 docker-compose 了

```
source /etc/profile
```


## TH-Nebula 安装

### 安装步骤


- 拉取Docker镜像：

	```
	git clone --recursive https://github.com/threathunterX/nebula.git
	cd nebula
	docker-compose pull
	```

- 运行安装脚本

	```
	./ctrl.sh install
	```

	![a2. 安装过程截图](http://www.z4a.net/images/2018/11/29/a2.png)

- 启动系统
	```
	./ctrl.sh start
	```
	![a3. 启动过程截图](http://www.z4a.net/images/2018/11/29/a3.png)

- 查看运行状态
  ```
  ./ctrl.sh status
  ```
  ![a4. 查看运行状态](http://www.z4a.net/images/2018/11/29/a4.png)


## 流量抓取客户端sniffer安装



### 安装步骤

- 拉取Docker镜像：
	```
	git clone --recursive https://github.com/threathunterX/sniffer.git
	cd sniffer
	docker-compose pull
	```

- 进入目录：
	```
	cd sniffer
	```

- 配置修改：
	```
	配置文件docker-compose.yml（直接修改此文件即可）
	
	  environment:
	   - REDIS_HOST=127.0.0.1  # 远程redisIP
	   - REDIS_PORT=16379      # 远程redis端口
	   - NEBULA_HOST=127.0.0.1 # 远程nebula服务IP
	   - NEBULA_PORT=9001      # 远程nebulaIP

	   - SOURCES=default       # 数据源,支持多源
	   
	   #default,使用bro抓取网卡流量
	   - DRIVER_INTERFACE=eth0 # 监听网卡
	   - DRIVER_PORT=80,9001   # 业务服务端口
	   - BRO_PORT=47000

	```

- 启动停止镜像：
	```
	1) 启动镜像
	docker-compose up -d
	2) 停止镜像  
	docker-compose down
	```


## 其他说明

`9001` 端口为 `TH-Nebula`的`http`端口, 可通过 `http://IP：9001`端口的方式访问 `TH-Nebula`界面

`管理员`：threathunter_test ：threathunter

`超级管理员`：threathunter ：threathunter

