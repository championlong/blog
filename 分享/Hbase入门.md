# 安装
> 前提安装 ZooKeeper
## Docker
拉取镜像
```shell
docker pull harisekhon/hbase:latest
```
启动容器
```shell
sudo docker run -d -h docker-hbase \
 -p 12181:2181 \
 -p 18080:8080 \
 -p 18085:8085 \
 -p 19090:9090 \
 -p 19000:9000 \
 -p 19095:9095 \
 -p 16000:16000 \
 -p 16010:16010 \
 -p 16201:16201 \
 -p 16301:16301 \
 -p 16020:16020\
 --name hbase \
 harisekhon/hbase
```
配置服务器hosts文件(zookeeper 里使用的是 docker-hbase 这个地址)
```shell
127.0.0.1 docker-hbase 
```
进入容器
```shell
docker exec -it <container ID前缀> bash
```
启动thrift2
```shell
/hbase/bin/hbase-daemon.sh start thrift2
```
使用[HBase Shell](https://hbase.apache.org/book.html#shell)
```
hbase shell
```
进入Hbase WebUI
```url
http://localhost:16010/master-status#userTables
```
日志目录
```shell
cd /hbase/logs
```
