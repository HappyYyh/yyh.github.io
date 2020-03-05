---
title: Elastic Stack 环境部署
tags: ["教程"]
---



## ELK简介

ELK是Elasticsearch、Logstash、Kibana三大开源框架首字母大写简称。市面上也被成为Elastic Stack。

- **Elasticsearch**是一个基于Lucene、分布式、通过Restful方式进行交互的近实时搜索平台框架。
- **Logstash**是ELK的中央数据流引擎，用于从不同目标（文件/数据存储/MQ）收集的不同格式数据，经过过滤后支持输出到不同目的地（文件/MQ/redis/elasticsearch/kafka等）。
- **Kibana**可以将elasticsearch的数据通过友好的页面展示出来，提供实时分析的功能。



## ELK安装

利用docker部署ELK（ElasticSearch + Logstash + Kibana），这里面ELK版本最好一致(官网的7.6版本太慢)

~~~shell
docker pull docker.io/elasticsearch:7.5.0
docker pull docker.io/kibana:7.5.0
docker pull docker.io/logstash:7.5.0
~~~



### ElasticSearch

官网：https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html

~~~shell
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -d --name elasticsearch  elasticsearch
~~~



### Kibana

官网：https://www.elastic.co/guide/en/kibana/current/docker.html

```shell
docker run --link elasticsearch:elasticsearch -p 5601:5601 -d --name kibana kibana
#这里--link是容器创造连接，第一个“elasticsearch”是容器的id或者名称
```

kibana部署完成以后打开网页 访问：http://ip:5601即可进入

ps：如果进去让创建index，无法选择的话需要logstash绑定es的index，可以先创建索引

~~~shell
curl -XPUT http://ip:9200/test
~~~



### Logstash

官网：https://www.elastic.co/guide/en/logstash/current/docker-config.html

创建两个配置文件(下面的ip为公网ip)

logstash.yml

~~~yml
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: [ "http://47.99.53.114/9200" ]
#xpack.monitoring.elasticsearch.username: admin
#xpack.monitoring.elasticsearch.password: admin
~~~

logstash.conf

~~~json
input {
 file {
  path => "/tmp/access_log"
  start_position => "beginning"
 }
}
output {
  elasticsearch {
	 hosts => ["ip:9200"]
	 index => "test" #之前创建的索引
  }
}

~~~

创建/tmp/access_log文件

~~~shell
mkdir /tmp/access_log
~~~

~~~shell
#把两个配置文件以及log文件 映射到容器内部
docker run -d --name logstash -v /tmp/access_log:/tmp/access_log -v ~/docker/logstash/logstash.conf:/usr/share/logstash/pipeline/logstash.conf -v ~/docker/logstash/logstash.yml:/usr/share/logstash/config/logstash.yml 44fa464082cd(imgId)
~~~

向access_log中写入内容

~~~shell
echo "Hello ELK" >> /tmp/access_log
~~~

之后打开kibana,即http://:ip:5604

然后选择create index 即输入test即可（如果输入test无法找到基本上都是logstash和es的通讯问题，仔细检测上面的配置文件）

然后点击discovery，右侧顶部选择时间过滤就可以看到日志



## Beats安装

### Beats简介

beats是一组轻量级采集程序的统称，这些采集程序包括并不限于：

- filebeat: 进行文件和目录采集，主要用于收集日志数据。
- metricbeat: 进行指标采集，指标可以是系统的，也可以是众多中间件产品的，主要用于监控系统和软件的性能。
- packetbeat: 通过网络抓包、协议分析，对一些请求响应式的系统通信进行监控和数据收集，可以收集到很多常规方式无法收集到的信息。
- Winlogbeat: 专门针对windows的event log进行的数据采集。
- Heartbeat: 系统间连通性检测，比如icmp, tcp, http等系统的连通性监控。

其和ELK的关系图如下：

![beat-description](http://image.yangyhao.top/blog/ELK%E6%90%AD%E5%BB%BA-beat-description.png)



### Metricbeat

**简介：**

- 将 Metricbeat 部署到您所有的 Linux、Windows 和 Mac 主机，并将它连接到 Elasticsearch 就大功告成啦：您可以获取系统级的 **CPU 使用率、内存、文件系统、磁盘 IO 和网络 IO 统计数据**，以及获得如同系统上 top 命令类似的各个进程的统计数据。

- Metricbeat 提供多种内部模块，用于从服务中收集指标，例如 **Apache、NGINX、MongoDB、MySQL、PostgreSQL、Prometheus、Redis** 等等。安装简单，完全零依赖性。只需在配置文件中启用您所需的模块即可。将收集到的数据发送到elasticsearch或logstash中。
  



**安装：**

**yum安装（centos系统）：**https://www.elastic.co/guide/en/beats/metricbeat/7.6/setup-repositories.html#_yum

1、安装授权

~~~shell
sudo rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
~~~

2、在 `/etc/yum.repos.d/`下创建`elastic.repo`文件、配置yum源

然后在其中添加：

~~~
[elastic-7.x]
name=Elastic repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
~~~

3、yum安装

~~~shell
sudo yum install metricbeat
~~~

4、配置metricbeat

~~~shell
vi /etc/metricbeat/metricbeat.yml
~~~

~~~yml
#主要修改kibana和es的ip
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["ip:9200"]
  
name: xxx

setup.kibana:
  # Kibana Host
  # Scheme and port can be left out and will be set to the default (http and 5601)
  # In case you specify and additional path, the scheme is required: http://localhost:5601/path
  # IPv6 addresses should always be defined as: https://[2001:db8::1]:5601
  host: "ip:5601"
  
#修改dashboard参数为true  
setup.dashboards.enabled: true
~~~

5、启动

~~~shell
sudo systemctl enable metricbeat
~~~

6、检测状态

~~~shell
sudo systemctl status metricbeat
~~~

7、查看启动的模块

~~~shell
sudo metricbeat modules list
~~~

8、启动其他模块

~~~shell
sudo metricbeat modules enable nginx
~~~



打开kibana在dashboard里面搜索System ，点击选中**[Metricbeat System] Overview ECS**即可看到如下仪表盘：

![metricbeat-system](http://image.yangyhao.top/blog/ELK%E6%90%AD%E5%BB%BA-metricbeat-system.png)



### FileBeat

**概念：**

Filebeat是一个日志文件托运工具，在你的服务器上安装客户端后，filebeat会监控日志目录或者指定的日志文件，追踪读取这些文件（追踪文件的变化，不停的读），并且转发这些信息到elasticsearch或者logstarsh中存放。



**APT安装(debian系统)**：https://www.elastic.co/guide/en/beats/filebeat/current/setup-repositories.html

1、下载签名

~~~shell
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
~~~

2、安装apt-transport-https

~~~shell
sudo apt-get install apt-transport-https
~~~

3、保存es仓库

~~~shell
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
~~~

4、安装filebeat

~~~shell
sudo apt-get update && sudo apt-get install filebeat
~~~



5、修改/etc/file/beat/filebeat.yml

~~~yml
paths:
    - /var/log/*.log
    
setup.dashboards.enabled: true

#多服务器下用name指定服务器名称，也可以用tags
name: 114

setup.kibana:
# Kibana Host
# Scheme and port can be left out and will be set to the default (http and 5601)
# In case you specify and additional path, the scheme is required: http://localhost:5601/path
# IPv6 addresses should always be defined as: https://[2001:db8::1]:5601
  host: "ip:5601"

#-------------------------- Elasticsearch output ------------------------------
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["ip:9200"]

~~~

6、开始准备预定义环境

```
filebeat setup -e
```

如果最后报错了，那么需要根据情况进行修复。



如果启动报错，可以用如下命令检测：

~~~shell
sudo filebeat -e -c filebeat.yml
#或者输入到文件中
sudo filebeat -e >filebeat.log 2>&1 &
~~~



7、启动

~~~shell
sudo service filebeat start
#查看状态
sudo service filebeat status
~~~



8、查看是否部署成功

​	部署完成以后可以在kibana页面中点击左侧`Management`按钮，然后选择`Index patterns`，出现**filebeat-***即说明ok



#### 读取文件日志

在filebeat的总览配置文件`filebeat.yml`里面有一个fileInput属性：

~~~yml
filebeat.inputs:

# Each - is an input. Most options can be set at the input level, so
# you can use different inputs for various configurations.
# Below are the input specific configurations.

- type: log

  # Change to true to enable this input configuration.
  enabled: true

  # Paths that should be crawled and fetched. Glob based paths.
  paths:
    - /var/log/spaas/stdout.log
~~~

其中的paths可以是一个数组路径，配置好地址后就可以把这路径下日志当成输入日志了；

详细配置点[这里](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-filebeat-options.html)

之后在kibana页面上选中Discover按钮，在左侧选中`filebeat*`这个索引(可以点检change按钮进行切换索引)，之后就会展示出上面配置的路径下的日志，这里以springboot日志为例，效果如下：

![filebeat-pathLog](http://image.yangyhao.top/blog/ELK%E6%90%AD%E5%BB%BA-filebeat-pathLog.png)





#### 读取Postgresql日志

~~~shell
sudo filebeat modules enable postgresql

#编辑配置文件
sudo vim /etc/filebeat/modules.d/postgresql.yml
#修改path
- module: postgresql
  # All logs
  log:
    enabled: true

    # Set custom paths for the log files. If left empty,
    # Filebeat will choose the paths depending on your OS.
    var.paths: ["/var/log/postgresql/11/main/*.log"]
~~~

打开kibana在dashboard里面搜索postgresql，点击选中**[Filebeat PostgreSQL] Overview ECS**即可看到如下仪表盘：

![filebeat-postgresql](http://image.yangyhao.top/blog/ELK%E6%90%AD%E5%BB%BA-filebeat-postgresql.png)

> ps：postgresql默认没有打开日志，所以这里这是简单的展示了连接信息



#### 读取Nginx日志

~~~shell
#启动nginx模块
sudo filebeat modules enable nginx
cd /etc/filebeat/modules.d/
#编辑配置文件
sudo vim nginx.yml

#编辑
- module: nginx
  # Access logs
  access:
    enabled: true

    # Set custom paths for the log files. If left empty,
    # Filebeat will choose the paths depending on your OS.
    var.paths: ["/var/log/nginx/access.log"]

  # Error logs
  error:
    enabled: true

    # Set custom paths for the log files. If left empty,
    # Filebeat will choose the paths depending on your OS.
    var.paths: ["/var/log/nginx/error.log"]

~~~

打开kibana在dashboard里面搜索Nginx，点击选中**[Filebeat Nginx] Overview ECS**即可看到如下仪表盘：

![filebeat-nginx](http://image.yangyhao.top/blog/ELK%E6%90%AD%E5%BB%BA-filebeat-nginx.png)

