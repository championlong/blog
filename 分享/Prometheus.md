# 启动
## Prometheus Server
安装Docker：[安装指南](https://docs.docker.com/get-docker/)

创建本地目录用于容器挂载：
```shell
sudo mkdir -p /etc/prometheus
sudo touch prometheus.yml
```

容器启动Prometheus
```shell
docker run -d -p 9090:9090 -v /etc/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```
页面：http://localhost:9090

### 修改prometheus.yml配置
```yml
global:
  scrape_interval: 15s # 每15s拉取一次

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['host.docker.internal:9090']
  - job_name: 'node'
    scrape_interval: 5s # 每5s拉取一次
    static_configs:
      - targets: ['host.docker.internal:9100']
```

## Node Exporter
采集主机数据
```shell
brew install node_exporter
brew services start node_exporter
```
页面：http://localhost:9100/

## Grafana
开源的可视化平台
```shell
docker run -d -p 3000:3000 grafana/grafana
```
添加数据源Data sources配置Connection

页面：http://localhost:3000


# 参考
* [prometheus官方文档](https://prometheus.io/docs/introduction/overview/)
* [prometheus-book](https://yunlzheng.gitbook.io/prometheus-book/parti-prometheus-ji-chu/quickstart)
* [Prometheus 入门到实战](https://p8s.io/docs/)