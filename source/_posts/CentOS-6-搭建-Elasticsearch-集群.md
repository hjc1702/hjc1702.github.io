---
title: CentOS 6 搭建 Elasticsearch 集群
date: 2017-08-28 17:27:19
tags: [centos, Elasticsearch, kibana]
---

前司的运维水平太高了，以至于从来没有自己搭建过 es 的集群和相关服务。今天因为业务需要自己搭建了一下，记录一下。

## Elasticsearch

搜索、分析和存储数据

### 依赖

* Java

### 安装

[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/rpm.html)已经非常详细了，不再赘述~

### 启动、检测

CentOS 6 中，用 `init` 管理

```bash
sudo chkconfig --add elasticsearch

sudo service elasticsearch start
sudo service elasticsearch stop
```

进行测试

```bash
curl -XGET 'localhost:9200/?pretty'
```

正常的话，得到类似输出

```json
{
  "name" : "name",
  "cluster_name" : "cluster_name",
  "cluster_uuid" : "cluster_uuid",
  "version" : {
    "number" : "5.5.2",
    "build_hash" : "b2f0c09",
    "build_date" : "2017-08-14T12:33:14.154Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.0"
  },
  "tagline" : "You Know, for Search"
}
```

有错误的话，在 `/var/log/elasticsearch/` 查看日志

单机测试随便玩儿的话，配置就没有必要改了，用默认的就好

### 配置集群

这里假设，两台服务器IP为

```bash
第一台机器: 192.168.0.1
第二台机器: 192.168.0.2
```

配置文件：`/etc/elasticsearch/elasticsearch.yml`

```yml
# ======================== Elasticsearch Configuration =========================
#
# ---------------------------------- Cluster -----------------------------------
# Use a descriptive name for your cluster:
cluster.name: elasticsearch # 所有服务器保持一致
# ------------------------------------ Node ------------------------------------
# Use a descriptive name for the node:
node.name: elasticsearch_node_x # 每个服务器不一样
# Add custom attributes to the node:
node.attr.rack: elasticsearch_node_rx # 每个服务器不一样
# ----------------------------------- Paths ------------------------------------
# Path to directory where to store the data (separate multiple locations by comma):
# 数据和日志位置，按需更改
#path.data: /path/to/data
# Path to log files:
#path.logs: /path/to/logs
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#bootstrap.memory_lock: true
# 因为 Centos6 不支持 SecComp 所以设置为 false
bootstrap.memory_lock: false
bootstrap.system_call_filter: false
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
# Elasticsearch performs poorly when the system is swapping the memory.
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
network.host: 192.168.0.1 # 第一台服务器
network.host: 192.168.0.2 # 第二台服务器
#
# Set a custom port for HTTP:
http.port: 9200
#
# --------------------------------- Discovery ----------------------------------
# Pass an initial list of hosts to perform discovery when new node is started:
discovery.zen.ping.unicast.hosts: ["192.168.0.1:9300", "192.168.0.2:9300"]
#
# Prevent the "split brain" by configuring the majority of nodes (total number of master-eligible nodes / 2 + 1):
discovery.zen.minimum_master_nodes: 2
#
# For more information, consult the zen discovery module documentation.
#
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
gateway.recover_after_nodes: 2
```

启动时可能遇到以下问题：

1. `max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]`
1. `max number of threads [1024] for user [es] is too low, increase to at least [2048]`
1. `max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]`

解决方案见[链接](https://github.com/DimonHo/DH_Note/issues/3)

## Kibana

实现数据可视化

### 安装与运行

在一台服务器安装就好，
依旧是看[官网文档](https://www.elastic.co/guide/en/kibana/current/rpm.html)~

访问 `http://192.168.0.1:5601`

### 配置

配置文件`/etc/kibana/kibana.yml`

```yml
elasticsearch.url: "http://192.168.0.1:9200"
```

## X-Pack

X-Pack 是集成了多种便捷功能的单个插件 — security、alerting、monitoring、reporting、graph 探索和 machine learning

### 安装

在es上安装插件

```bash
./usr/share/elasticsearch/bin/elasticsearch-plugin install x-pack
```

重启es

```bash
sudo service elasticsearch restart
```

在kibana上安装插件

```bash
./usr/share/kibana/bin/kibana-plugin install x-pack
```

重启kibana

```bash
sudo service kibana restart
```

### 配置

安装好之后 `x-pack` 之后访问 es、kibana 需要用户名密码，默认的。

用户名：elastic

密码：changeme

### 修改密码

1. 进入kibana，页面上进行操作
1. 在命令行中操作

```bash
curl -XPUT -u elastic 'localhost:9200/_xpack/security/user/elastic/_password' -H "Content-Type: application/json" -d '{
  "password" : "elasticpassword"
}'

curl -XPUT -u elastic 'localhost:9200/_xpack/security/user/kibana/_password' -H "Content-Type: application/json" -d '{
  "password" : "kibanapassword"
}'
```

至此，elasticsearch，kibana就配置好了，可以开始使用了~~