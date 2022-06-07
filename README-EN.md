# Nacos Docker

![Docker Pulls](https://img.shields.io/docker/pulls/onewe/nacos-server)
![Docker release build](https://img.shields.io/github/workflow/status/onewe/nacos-docker/Publish%20Docker%20release%20image?label=nacos-release-build)
![Docker snapshot build](https://img.shields.io/github/workflow/status/onewe/nacos-docker/Publish%20Docker%20snapshot%20image?label=nacos-snapshot-build)
![Docker image release version](https://img.shields.io/docker/v/onewe/nacos-server/v2.1.0)
![Docker image snapshot version](https://img.shields.io/docker/v/onewe/nacos-server/snapshot)

This project is [Nacos](https://github.com/alibaba/nacos) Server side docker image source code, and Nacos server side docker standalone mode and cluster mode demo.Since the original project did not have a synchronous build`mysql`image
and do not have snapshot version,So made some modifications on the original project.

Change list:

- Synchronous build mysql5.7
- Synchronous build mysql8.8
- Start nacos without use root
- Simplify build scripts with docker-compose extends
- Remove init.d directory mapping
- Increase the build of the snapshot version

This project is [Nacos](https://github.com/alibaba/nacos) Server side docker image source code, and Nacos server side docker standalone mode and cluster mode demo.

## Project directory

* build：nacos source code of image
* env: docker compose environment variable file
* example: docker-compose example

## Running environment

* [Docker](https://www.docker.com/)

### Precautions

* Since Nacos version 1.3.1,database has been upgraded to 8.0, and downward compatibility.
* The database used in the example demonstration is to customize the official Mysql image and automatically initialize the database script for convenience.
* If you need to customize database,
  Manual initialization is required before starting Nacos for the first time [Database script](https://github.com/alibaba/nacos/blob/develop/distribution/conf/nacos-mysql.sql) .
* The snapshot version is built every 8 hours. If you need to use the snapshot version for practice, change the image label to`snapshot`

## Quick start docker

* Standalone latest
  
  ```shell
  docker run --rm -p 8848:8848 onewe/nacos-server:latest
  ```

* Standalone snapshot
  
  ```shell
  docker run --rm -p 8848:8848 onewe/nacos-server:snapshot
  ```

* Open browser
  
  link：http://127.0.0.1:8848/nacos/

## Quick start docker-compose

Open terminal and execute：

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

* Cluster mode
  
  ```shell
  # Use ip model
  docker-compose -f example/cluster-ip/docker-compose.yaml up
  
  # Use hostname model
  docker-compose -f example/cluster-hostname/docker-compose.yaml up
  
  # Cluster model without mysql
  docker-compose -f example/cluster-embedded/docker-compose.yaml up 
  ```

* Service registration example
  
  ```shell
  curl -X PUT 'http://127.0.0.1:8848/nacos/v1/ns/instance?serviceName=nacos.naming.serviceName&ip=20.18.7.10&port=8080'
  ```

* Service discovery example
  
  ```shell
  curl -X GET 'http://127.0.0.1:8848/nacos/v1/ns/instance/list?serviceName=nacos.naming.serviceName'
  ```

* Push configuration example
  
  ```shell
  curl -X POST "http://127.0.0.1:8848/nacos/v1/cs/configs?dataId=nacos.cfg.dataId&group=test&content=helloWorld"
  ```

* Get configuration example
  
  ```shell
    curl -X GET "http://127.0.0.1:8848/nacos/v1/cs/configs?dataId=nacos.cfg.dataId&group=test"
  ```

* Open Browser
  
  link：http://127.0.0.1:8848/nacos/

## Property configuration list

| Property name            | Description                                             | Options                                                                                                     |
|--------------------------|---------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| MODE                     | Startup mode: cluster/standalone                        | cluster/standalone default **standalone**                                                                   |
| NACOS_SERVERS            | cluster ip address                                      | p1:port1 space ip2:port2 space ip3:port3                                                                    |
| NACOS_SERVER_IP          | IP can be specified in multi-NIC mode                   | default is empty,no configured                                                                              |
| NACOS_DEBUG              | Enable remote DEBUG                                     | y/n default :n, default debug port:9555                                                                     |
| NACOS_DEBUG_PORT         | Remote DEBUG port                                       | default :9555                                                                                               |
| PREFER_HOST_MODE         | Support IP or domian mode                               | hostname/ip default**IP**                                                                                   |
| MEMBER_LIST              | Set the cluster address by environment                  | example:192.168.16.101:8847?raft_port=8807,192.168.16.101?raft_port=8808,192.168.16.101:8849?raft_port=8809 |
| TOMCAT_ACCESSLOG_ENABLED | server.tomcat.accesslog.enabled                         | default :false                                                                                              |
| EMBEDDED_STORAGE         | Enable embedded storage mode                            | `true/false` default : true                                                                                 |
| USE_ONLY_SITE_INTERFACES |                                                         | true/false default false                                                                                    |
| PREFERRED_NETWORKS       | Preferred use ip addresses,array of regular expressions | no configured default empty                                                                                 |
| IGNORED_INTERFACES       | Ignored use ip addresses,array of regular expressions   | no configured default empty                                                                                                     |
| JVM_XMS                  | -Xms                                                    | default :1g                                                                                                      |
| JVM_XMX                  | -Xmx                                                    | default :1g                                                                                                      |
| JVM_XMN                  | -Xmn                                                    | 512m                                                                                                        |
| JVM_MS                   | - XX:MetaspaceSize                                      | default :128m                                                                                                    |
| JVM_MMS                  | -XX:MaxMetaspaceSize                                    | default :320m                                                                                                    |

## Advanced configuration

If the above configuration can not be satisfied,You can mount the `application.properties` file and customize it according to your needs.mount path:`./application.properties:/home/nacos/conf/application.properties`

## Build image

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

## Build parameter list

| Property name          | Description   | Options                                                                        |
| ------------- |---------------|--------------------------------------------------------------------------------|
| NACOS_VERSION | nacos version | pay attention the version number is not prefixed `v`,example:v2.1.0,must:2.1.0 |
