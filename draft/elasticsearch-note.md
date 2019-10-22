elasticsearch

《Elasticsearch 核心技术与实战》Github代码 https://github.com/onebirdrocks/geektime-ELK


运行的环境: windows

安装了 `PowerShell-7.0.0`

1. 下载 elasticsearch和**Kibana** 都可以在华为云  https://mirrors.huaweicloud.com/  上下载到

我安装的 elasticsearch 7.4.0 和 kibana 7.4.0

安装是解压之后，就可以使用的，安装了jdk 1.8.221，在环境变量中，配置了 JAVA_HOME并加入到 path中去了。

启动 powershell,进入 elasticsearch解压的目录,启动 elasticsearch

```
./bin/elasticsearch
```

浏览器可通过`http://localhost:9200` 访问，查看elasticsearch的基本信息

查看安装的插件

```
 ./bin/elasticsearch-plugin list
```

安装一个 `analysis-icu`

```
./bin/elasticsearch-plugin install analysis-icu
```

可用 ` ./bin/elasticsearch-plugin list`再次查看 安装的插件，然后启动 elasticsearch,

可通过`http://localhost:9200/_cat/plugins` 查看安装的列表



`_cat/` 是 elasticsearch 提供的要给 api

* plugins 查看安装的插件
* nodes 查看运行的节点



## 如何在开发机上运行多个 Elasticsearch实例

```
./bin/elasticsearch -E node.name=node1 -E cluster.name=geektime -E path.data=node1_data -d 
./bin/elasticsearch -E node.name=node2 -E cluster.name=geektime -E path.data=node2_data -d 
./bin/elasticsearch -E node.name=node3 -E cluster.name=geektime -E path.data=node3_data -d 
```

`node.name` 节点的名称

`cluster.name` 一个相同的集群的名称 

`pat.data` 为每个节点设置存放数据的地址

*注:* 在 windows下的 powershell 好像不需要最后的 `-d` 参数，我是打开 三个 powershell的窗口，单独运行上面的命令的



* 删除进程 ps grep|elasticsearch / kill pid



## kibana

启动 

```
./bin/kibana
```

默认是 `5601` 端口

`http://localhost:5601`

在 首页 有一个  **Add sample data** 下面有一个 链接，点击进去，可以进行导入 kibana 提供的简单数据



kinana console 

* dev tools
* Search Profiler
* Help + 一些快捷键
     * cmd + / (查看API帮助文档)
     * cmd + option + l
     * cmd + option + 0
     * cmd + option + shift + 0    

kibana plugins

```
bin/kibana-plugin install plugin_location
bin/kibana-plugin list
bin/kibana remove
```

Apps:

* LogTrail
* Own Home
* Shard Allocation
* Corweyor
* Indices View
* Analyze UI
* Cleaner Setting index ttl
* ElastAlert Kibana Plugin

Visualizations:

* 3D Chars
* 3D Graph
* Bmap
* C3JS Visualzations
* Calendar Visualzation
* Cohort analysis
* Collored Metilc Visualzation
* Dendrogram
* Dotplot
* Dropdown
* Enhanced Table
* Enhanced Themap
* Extended Metric

------

## 学习在本机Docker环境中云高兴 ELK Stack

* Docker-compose 相关命令
     * 运行 docker-compose up [-d]
     * docker-compose down
     * docker-compose down -v
     * docker stop / rm containerID

Demo

* 运行 Docker-compose  本地构建 Elasticsearch 分布式特性
* 集成 Cerobro，方便查看集群状态，默认运行在 `9000`端口，可通过`http://localhost:9000`


## logstash

[ **2.4-Logstash安装与导入数据** ]( [https://github.com/onebirdrocks/geektime-ELK/tree/master/part-1/2.4-Logstash%E5%AE%89%E8%A3%85%E4%B8%8E%E5%AF%BC%E5%85%A5%E6%95%B0%E6%8D%AE](https://github.com/onebirdrocks/geektime-ELK/tree/master/part-1/2.4-Logstash安装与导入数据) ) 

参考文档说明导入 movices.csv 数据，启动 logstash

```
bin/logstash -f path_logstash.conf
```



