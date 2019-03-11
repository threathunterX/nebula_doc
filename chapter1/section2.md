# 1.2. 源码安装

## 环境准备

### 前端环境

- Node.js: 11.x
- npm: 6.x
- webpack: 4.x

### 后端环境

- java: 1.8
- maven: 3.x
- python: 2.7.x

以上环境版本为推荐版本，其他版本可行性请自行测试。

## 编译安装
- nubula编译:
  为了方便，均可使用项目根目录下的`build.sh`脚本进行编译、安装。
  ```
  git clone --recursive https://github.com/threathunterX/nebula.git
  cd nebula
  ```
  编译`apps`：
  ```
  ./build.sh -u -v 1.1.0 --apps 
  ```
  编译`docker`镜像
  ```
  ./build.sh -u -v 1.1.0 --image
  ```
   
- sniffer:
  ```
  git clone --recursive https://github.com/threathunterX/sniffer.git
  cd nebula
  
  编译`docker`镜像
  ```
  docker-compose build
  ``
  
  
`sniffer`流量抓取服务以及`nebula`主项目安装运行方法请参考二进制安装部分
