
---
layout: post
title: Elasticsearch学习笔记
description: Elasticsearch学习笔记
category: elasticsearch
---

说明
Elasticsearch版本：6.3.2

# 索引

## 创建索引

```
PUT /items?master_timeout=30s&timeout=30s
```

body

```
{
  "settings": {},
  "mappings": {
    "_doc": {
      "_doc": {
        "properties": {
          "category1_id": {
            "type": "long"
          },
          "category2_id": {
            "type": "long"
          },
          "create_date": {
            "format": "epoch_second",
            "type": "date"
          },
          "item_unique_id": {
            "type": "long"
          },
          "status": {
            "type": "integer"
          }
        }
      }
    }
  },
  "aliases": {}
}
```

## 修改索引

```
PUT /items/_mapping/_doc?master_timeout=30s&timeout=30s
```

body

```
{
  "_doc": {
    "properties": {
      "brand_id": {
        "type": "long"
      },
      "category1_id": {
        "type": "long"
      },
      "category2_id": {
        "type": "long"
      },
      "common_flag": {
        "type": "integer"
      }
    }
  }
}
```

## 删除索引

```
DELETE /items
```

## 索引别名

通过别名不停机修改Mapping结构

https://841809077.github.io/2019/11/29/index-alias-and-modify-index-mapping.html

# 查询

## 查询全部

```
GET /items/_search
{
  "query": {
    "match_all": {}
  }
}
```

items:索引名称，支持正则表达式、多个 index 以逗号分隔

## 查询时创建新字段

```
GET /items/_search
{
  "query": {
    "match_all":{}
  },
  "script_fields": {
    "field_name": {
      "script": {
        "lang": "painless",
        "source":"doc['category0.id'].value+'-'+doc['category1.id'].value+'-'+doc['category2.id'].value"
      }
    }
  }
}
```

查询时通过脚本生成新的字段

## Script Query

```
GET items/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "script": {
            "script": {
              "source": "if(doc['cart_uv'].value == 0){return false;}doc['detail_times'].value/doc['cart_uv'].value > 0",
              "lang": "painless"
            }
          }
        }
      ]
    }
  }
}
```

# 统计

```
GET items/_search
{
  "query": {
    "match_all": {}
  },
  "size": 10, 
  "aggs": {
    "aaa":{
      "range": {
        "field": "sale_price",
        "ranges": [
          {
            "from": 0,
            "to": 1000
          }
        ]
      }
    },
    "bbb":{
      "terms": {
        "field": "category0.id",
        "size": 5
      }
    },
    "ccc":{
      "terms": {
        "field": "category1.id",
        "size": 5
      }, 
      "aggs": {
        "total_price": {
          "sum": {
            "field": "sale_price"
          }
        }
      }
    }
  }
}
```

# analyze

```
GET _analyze
{
  "analyzer": "stop",
  "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
}
```

```
GET index_name/_analyze
{
  "field": "url_path",
  "text": "/a/b/c"
}
```

# explain

```
GET items/doc/BTKJzWwBgRpDanj3GP9G/_explain
{
  "query": {
    "match":{
      "tag_name":"苹果"
    }
  }
}
```

如何做 reindex

参考：

http://bk.poph163.com/2018/07/04/elk%E7%94%B1%E6%B5%85%E5%85%A5%E6%B7%B1/


