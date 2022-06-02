# Nacos Docker

![Docker Pulls](https://img.shields.io/docker/pulls/onewe/nacos-server)
![Docker release build](https://img.shields.io/github/workflow/status/onewe/nacos-docker/Publish%20Docker%20release%20image?label=nacos-release-build)
![Docker snapshot build](https://img.shields.io/github/workflow/status/onewe/nacos-docker/Publish%20Docker%20snapshot%20image?label=nacos-snapshot-build)
![Docker image release version](https://img.shields.io/docker/v/onewe/nacos-server/v2.1.0)
![Docker image snapshot version](https://img.shields.io/docker/v/onewe/nacos-server/snapshot)

本项目是 [Nacos](https://github.com/alibaba/nacos) Server的docker镜像的build源码,以及Nacos server 在docker的单机和集群的运行例子.由于原始项目没有进行同步构建`mysql`镜像
和没有快照版本,故而在原始项目上做了一些修改.

改动:

- 同步构建mysql5.7
- 同步构建mysql8.8
- nacos启动脚本非root用户启动
- docker-compose使用extends精简
- 取消init.d目录的映射
- 增加快照版本的构建

本项目是 [Nacos](https://github.com/alibaba/nacos) Server的docker镜像的build源码,以及Nacos server 在docker的单机和集群的运行例子.

## 项目目录

* build：nacos 镜像制作的源码
* env: docker compose 环境变量文件
* example: docker-compose编排例子

## 运行环境

* [Docker](https://www.docker.com/)

### 注意事项

* 从Nacos 1.3.1版本开始,数据库存储已经升级到8.0, 并且它向下兼容
* 例子演示中使用的数据库是为了方便定制了官方Mysql镜像, 自动初始化的数据库脚本.
* 如果你使用自定义数据库,
  第一次启动Nacos前需要手动初始化 [数据库脚本](https://github.com/alibaba/nacos/blob/develop/distribution/conf/nacos-mysql.sql) .
* 快照版本每隔8小时构建一次,若需要使用快照版本进行体验把镜像标签改为`snapshot`即可

## 快速开始 docker

* Standalone latest
  
  ```shell
  docker run --rm -p 8848:8848 onewe/nacos-server:latest
  ```

* Standalone snapshot
  
  ```shell
  docker run --rm -p 8848:8848 onewe/nacos-server:snapshot
  ```

* 打开浏览器
  
  link：http://127.0.0.1:8848/nacos/

## 快速开始 docker-compose

打开命令窗口执行：

* Clone project
  
  ```shell
  git clone --depth 1 https://github.com/onewe/nacos-docker.git
  cd nacos-docker
  ```

* Standalone Derby
  
  ```shell
  docker-compose -f example/standalone-derby/docker-compose.yaml up
  ```

* Standalone Mysql
  
  ```shell
  # Using mysql 5.7
  docker-compose -f example/standalone-mysql-5.7/docker-compose.yaml up
  
  # Using mysql 8
  docker-compose -f example/standalone-mysql-8/docker-compose.yaml up
  ```

* 集群模式
  
  ```shell
  # Use ip model
  docker-compose -f example/cluster-ip/docker-compose.yaml up
  
  # Use hostname model
  docker-compose -f example/cluster-hostname/docker-compose.yaml up
  
  # Cluster model without mysql
  docker-compose -f example/cluster-embedded/docker-compose.yaml up 
  ```

* 服务注册示例
  
  ```shell
  curl -X PUT 'http://127.0.0.1:8848/nacos/v1/ns/instance?serviceName=nacos.naming.serviceName&ip=20.18.7.10&port=8080'
  ```

* 服务发现示例
  
  ```shell
  curl -X GET 'http://127.0.0.1:8848/nacos/v1/ns/instance/list?serviceName=nacos.naming.serviceName'
  ```

* 推送配置示例
  
  ```shell
  curl -X POST "http://127.0.0.1:8848/nacos/v1/cs/configs?dataId=nacos.cfg.dataId&group=test&content=helloWorld"
  ```

* 获取配置示例
  
  ```shell
    curl -X GET "http://127.0.0.1:8848/nacos/v1/cs/configs?dataId=nacos.cfg.dataId&group=test"
  ```

* 打开浏览器
  
  link：http://127.0.0.1:8848/nacos/

## 属性配置列表

| 属性名称                     | 描述                              | 选项                                                                                                     |
| ------------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------ |
| MODE                     | 系统启动方式: 集群/单机                   | cluster/standalone 默认 **standalone**                                                                   |
| NACOS_SERVERS            | 集群地址                            | p1:port1空格ip2:port2 空格ip3:port3                                                                        |
| NACOS_SERVER_IP          | 多网卡模式下可以指定IP                    | 默认为空,未配置                                                                                               |
| NACOS_DEBUG              | 是否开启远程DEBUG                     | y/n 默认 :n, 默认debug端口:9555                                                                              |
| NACOS_DEBUG_PORT         | 远程DEBUG端口                       | 默认为:9555                                                                                               |
| PREFER_HOST_MODE         | 支持IP还是域名模式                      | hostname/ip 默认**IP**                                                                                   |
| MEMBER_LIST              | 通过环境变量的方式设置集群地址                 | 例子:192.168.16.101:8847?raft_port=8807,192.168.16.101?raft_port=8808,192.168.16.101:8849?raft_port=8809 |
| TOMCAT_ACCESSLOG_ENABLED | server.tomcat.accesslog.enabled | 默认 :false                                                                                              |
| EMBEDDED_STORAGE         | 是否开启集群嵌入式存储模式                   | `true/false` 默认 : true                                                                                 |
| USE_ONLY_SITE_INTERFACES |                                 | true/false 默认 false                                                                                    |
| PREFERRED_NETWORKS       | 倾向使用的ip地址,正则表达式数组               | 未配置默认为空                                                                                                |
| IGNORED_INTERFACES       | 忽略的ip地址,可以是正则表达式                | 未配置默认为空                                                                                                |
| JVM_XMS                  | -Xms                            | 默认 :1g                                                                                                 |
| JVM_XMX                  | -Xmx                            | 默认 :1g                                                                                                 |
| JVM_XMN                  | -Xmn                            | 512m                                                                                                   |
| JVM_MS                   | - XX:MetaspaceSize              | 默认 :128m                                                                                               |
| JVM_MMS                  | -XX:MaxMetaspaceSize            | 默认 :320m                                                                                               |

## 高级配置

如果上述配置列表无法满足,可以把`application.properties`文件挂载出来,根据需求进行定制.挂载路径:`./application.properties:/home/nacos/conf/application.properties`

## 构建镜像

* Clone project
  
  ```shell
  git clone --depth 1 https://github.com/onewe/nacos-docker.git
  cd nacos-docker/build
  ```

* Build nacos image
  
  ```shell
  docker build --build-arg NACOS_VERSION=2.1.0 --target nacos-release -t nacos-server:v2.1.0 .
  ```

* Build mysql5.7 image
  
  ```shell
  docker build --build-arg NACOS_VERSION=2.1.0 --target mysql5.7 -t mysql-57:v2.1.0 .
  ```

* Build mysql8 image
  
  ```shell
  docker build --build-arg NACOS_VERSION=2.1.0 --target mysql8 -t mysql-8:v2.1.0 .
  ```

## 构建参数列表

| 属性名称          | 描述       | 选项                             |
| ------------- | -------- | ------------------------------ |
| NACOS_VERSION | nacos版本号 | 注意版本号没有前缀`v`,例如:v2.1.0,则:2.1.0 |
