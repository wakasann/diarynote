elasearch002

视频教程: https://www.bilibili.com/video/BV17a4y1x7zq

## ElasticSearch 插件

1. 下载地址: https://github.com/mobz/elasticsearch-head

2. 运行 head

    ```
   npm install
   npm run start
   ```
3. 访问head http://localhost:9100
4. 跨域问题，修改es配置文件`config/elasticsearch.yml`文件末尾添加

    ```
    http.cors.enabled: true
    http.cors.allow-origin: "*"
    ```

重新启动 elasticsearch



kinbana 汉化

修改`config/kinbana.yml` 在`#i18n.locale: "en"` 下一行添加

```
i18n.locale: "zh-CN"
```



### IK分词器插件

如果要使用中文，建议使用ik分词器!

ik 提供了两个分词算法: ik_smart 和ik_max_word,其中 ik_smart为最少切分,ik_max_word为嘴细粒度划分。

> 安装



1、下载地址 https://github.com/medcl/elasticsearch-analysis-ik

2、下载完毕之后，放入到我们的 elasticsearch的插件目录下

使用 kinbana DevTools进行测试

```
GET _analyze
{
  "analyzer": "ik_smart",
  "text" : "你好,世界"
}

GET _analyze
{
  "analyzer": "ik_max_word",
  "text" : "你好,世界"
}
```

> 更新字典

在es插件目录下的 ik分词器插件中，

o3mh4z7p@linshiyouxiang.net

## ElasticSearch 笔记



Token Filters



```
GET _analyze
{
  "tokenizer": "keyword",
  "char_filter": ["html_strip"],
  "text": ["<b>Hello</b>"]
}
```



```
GET kibana_sample_data_flights/_search
{
  "size": 0,
  "aggs": {
    "flight_dest": {
      "terms": {
        "field": "DestCountry"
      },
      "aggs": {
        "stats_price": {
          "stats": {
            "field": "AvgTicketPrice"
          }
        },
        "wather":{
          "terms": {
            "field": "DestWeather",
            "size": 5
          }
        }
      }
    }
  }
}
```

