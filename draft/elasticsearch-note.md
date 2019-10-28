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

```
input {
  file {
    path => "D:/dev/data/movies.csv"
    start_position => "beginning"
	sincedb_path => "D:/dev/logstash/mydata/moive.txt"
  }
}
filter {
  csv {
    separator => ","
    columns => ["id","content","genre"]
  }

  mutate {
    split => { "genre" => "|" }
    remove_field => ["path", "host","@timestamp","message"]
  }

  mutate {

    split => ["content", "("]
    add_field => { "title" => "%{[content][0]}"}
    add_field => { "year" => "%{[content][1]}"}
  }

  mutate {
    convert => {
      "year" => "integer"
    }
    strip => ["title"]
    remove_field => ["path", "host","@timestamp","message","content"]
  }

}
output {
   elasticsearch {
     hosts => "http://localhost:9200"
     index => "movies"
     document_id => "%{id}"
   }
  stdout {}
}
```



````
[2019-10-23T09:58:45,519][ERROR][logstash.javapipeline    ][main] Pipeline aborted due to error {:pipeline_id=>"main", :exception=>#<ArgumentError: The "sincedb_path" argument must point to a file, received a directory: "D:/dev/logstash/mydata/moive">,
````



sincedb_path 需要是一个文件，而不是一个目录

------

文檔CURD

```
//create document.自动生成 _id
POSt users/_doc
{
  "user": "Mkie",
  "post_date": "2019-10-23 00:01:22",
  "message": "hello"
}

//create document,指定id,如果id存在，报错

PUT users/_doc/1?op_type=create
{
  "user": "kangkang",
  "post_date": "2019-10-23 02:01:22",
  "message": "hello2"
}


//create document,指定id已经存在，就报错
PUT users/_create/1
{
  "user": "lulu",
  "post_date": "2019-10-23 03:01:22",
  "message": "hello3"
}

//get document by id
GET users/_doc/1


PUT users/_doc/1
{
  "user": "my mike"
}

//在原文档增加字段


POST users/_update/1
{
  "doc":{
    "post_date" : "2019-10-10 23:56:56",
    "message": "just message"
  }
}


DELETE usrs/_doc/1
```



bulk 操作

每个操作的失败不会影响其它操作

批量读取 - mget



```
GEt /_mget
{
  "docs":[
  	{
  	   "_index" : "user",
  	   "_id": 1,
  	},
  	{
  	   "_index": "comments",
  	   "_id": 1
  	}
  ]
}
```

msearch `_msearch`

------

正排索引和倒排索引



-----

analysis与analyzer

analyzer 分词器

使用 analyzer api



```
get /_analyze

POST books/_analyzer
{
 "filed": "title"
}
```



Standard Analyzer

* 默认分词器
* 按词划分
* 小写处理

Simple Analyzer

* 按照非字母，非字母都被去除

Whitespace Analyzer

* 按照空格划分

Stop Analyzer

* 相比Simple Analyzer
* 



language analyzer



icu analyzer

需要按照插件

```
elasticsearch-plugin install analysis-icu
```

提供了Unicode的支持，更好的支持亚洲语音

更多的中文分词器 

* IK
* THULAC

```
//standard
GET _analyze
{
  "analyzer": "standard",
  "text": "He my polite be object oh change. Consider no mr am overcame yourself throwing sociable children. Hastily her totally conduct may. "
}

//simple
GET _analyze
{
  "analyzer": "simple",
  "text": "He my polite be object oh change. Consider no mr am overcame yourself throwing sociable children. Hastily her totally conduct may. "
}

//stop
GET _analyze
{
  "analyzer": "stop",
  "text": "Six started far placing saw respect females old. Civilly why how end viewing attempt related enquire visitor. Man particular insensible celebrated conviction stimulated principles day. "
}

//whitespace
GET _analyze
{
  "analyzer": "whitespace",
  "text": "Six started far placing saw respect females old. Civilly why how end viewing attempt related enquire visitor. Man particular insensible celebrated conviction stimulated principles day."
}

//keyword
GET _analyze
{
  "analyzer": "keyword",
  "text": "Six started far placing saw respect females old. Civilly why how end viewing attempt related enquire visitor. Man particular insensible celebrated conviction stimulated principles day."
}

//pattern,default \w
GET _analyze
{
  "analyzer": "pattern",
  "text": "2 Six started far-placing saw respect females old. Civilly why how end viewing attempt related enquire visitor. Man particular insensible celebrated conviction stimulated principles day."
}

GET _analyze
{
  "analyzer": "english",
  "text": "2 Six started far-placing saw respect females old. Civilly why how end viewing attempt related enquire visitor. Man particular insensible celebrated conviction stimulated principles day."
}

GET _analyze
{
  "analyzer": "icu_analyzer",
  "text": "他說的的確在理“"
}

GET _analyze
{
  "analyzer": "standard",
  "text": "他說的的確在理“"
}
```



-----

search api

* URL Search 使用 `q`
* Request Body search 

/_search

/index1/_search



-----

URL Search

* q
* df 默认字段
* sort
* profile

```
GET /movies/_search?q=2012&df=title
{
  "profile":"true"
}

//泛查询
GET /movies/_search?q=2012
{
  "profile":"true"
}

//指定查询
GET /movies/_search?q=title:2012
{
  "profile":"true"
}

//使用引号
GET /movies/_search?q=title:"Beautiful Mind"
{
  "profile":"true"
}

//查找美丽心灵，Mind 泛查询
GET /movies/_search?q=title:Beautiful Mind
{
  "profile":"true"
}

//分组，bool查询
GET /movies/_search?q=title:(Beautiful Mind)
{
  "profile":"true"
}


//查找美丽心灵
GET /movies/_search?q=title:(Beautiful AND Mind)
{
  "profile":"true"
}


GET /movies/_search?q=title:(Beautiful NOT Mind)
{
  "profile":"true"
}

//%2B 加号
GET /movies/_search?q=title:(Beautiful %2BMind)
{
  "profile":"true"
}

GET /movies/_search?q=year:>=1990
{
  "profile":"true"
}

GET /movies/_search?q=title:b*
{
  "profile":"true"
}

GET /movies/_search?q=title:beautiful~1
{
  "profile":"true"
}

GET /movies/_search?q=title:"Lord Rings"~2
{
  "profile":"true"
}
```

------

Request body search

```
POST kibana_sample_data_ecommerce/_search
{
  "_source": ["order_date"],
  "sort": [
    {
      "order_date": {
        "order": "desc"
      }
    }
  ],
  "query": {
    "match_all": {}
  }
}

//脚本字段
GET kibana_sample_data_ecommerce/_search
{
  "script_fields": {
    "new_field": {
      "script": {
        "lang":"painless",
        "source": "doc['order_date'].value+'_hello'"
      }
    }
  }, 
  "query": {
    "match_all": {}
  }
}

POST movies/_search
{
  "query": {
    "match": {
      "title": "Last Christmas"
    }
  }
}

POST movies/_search
{
  "query": {
    "match": {
      "title": {
        "query": "Last Christmas", 
        "operator": "AND"
      }
      
    }
  }
}

POST movies/_search
{
  "query": {
    "match_phrase": {
      "title": {
        "query": "one love"
      }
      
    }
  }
}

POST movies/_search
{
  "query": {
    "match_phrase": {
      "title": {
        "query": "one love",
        "slop": 1
      }
      
    }
  }
}
```

query_string

simple query string query

