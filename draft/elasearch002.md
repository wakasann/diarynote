elasearch002



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

