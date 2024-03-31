---
share: "true"
title: elasticsearch之文本导入及拼音搜索实现
date: 2020-08-16 11:08:16
tags:
  - elasticsearch
---

# elasticsearch之文本导入及拼音搜索实现

需求：非结构化数据（word，pdf等文档）导入es，实现全文检索的功能（包括拼音检索功能）

本文会先从非结构化数据导入es，还有拼音搜索的实现，以及最后两者的联合应用。

* 本文基于es版本6.8.0

<!--more-->

## 非结构化数据的导入

- 插件：ingest-attachment  版本：6.8.0

### 步骤

1. 建立文本抽取管道（执行时需将//注释内容去掉）

```
PUT /_ingest/pipeline/attachment     //atttachment为管道名称，下文导入数据时会使用
{
  "description": "Extract attachment information",
  "processors": [
    {
      "attachment":{
        "field":"data",                 //管道抽取使用的原数据字段
        "indexed_chars" : -1,
        "ignore_missing":true
      }
    },
    {
     "remove":{"field":"data"}       //抽取完成是否要删除原字段
 }
  ]
}
```

2. 建索引（此处略去，不需要限定字段类型的话可以直接使用默认生成的索引结构）
3. 将想要导入的数据转成base64

此处我使用的是mac自带的base64命令

```
base64 -i 输入文件  -o 输出文件
```

4. 导入数据

```
PUT /pdftest/pdf/1?pipeline=attachment
{
     "data":"5oiR54ix5L2g"
}
```

5. 查询数据

```
//查询命令
GET /pdftest/pdf/1
//查询结果
{
  "_index" : "pdftest",
  "_type" : "pdf",
  "_id" : "1",
  "_version" : 4,
  "_seq_no" : 3,
  "_primary_term" : 3,
  "found" : true,
  "_source" : {
    "attachment" : {   //attachment里面的其他字段可以通过配置管道的时候进行配置
      "content_type" : "text/plain; charset=UTF-8",
      "language" : "lt",
      "content" : "我爱你",    //content内容即为文本转换的内容
      "content_length" : 4
    }
  }
}
//查询命令
POST /pdftest/_search
{
  "query": {
    "match_phrase": {
      "attachment.content": "我爱"
    }
  }
}
```

### 参考

https://www.elastic.co/guide/en/elasticsearch/plugins/6.8/ingest-attachment.html#ingest-attachment

https://blog.csdn.net/wenxindiaolong061/article/details/82562450

https://blog.csdn.net/m0_37739193/article/details/86421246

https://stackoverflow.com/questions/37861279/how-to-index-a-pdf-file-in-elasticsearch-5-0-0-with-ingest-attachment-plugin

https://blog.csdn.net/LazyBoy_Z_z/article/details/82864264

## 拼音搜索的实现

* 插件：https://github.com/medcl/elasticsearch-analysis-pinyin 版本：6.8.0

### 步骤

1. 自定义分词器

要使用「拼音插件」需要在创建索引时使用「自定义模板」并在自定义模板中「自定义分析器」。

```
PUT /doc_resources
{
  "settings": {
    "analysis": {
      "analyzer": {
        "user_name_analyzer": {
          "tokenizer": "ik_smart",
          "filter": "pinyin_first_letter_and_full_pinyin_filter"
        }
      },
      "filter": {
        "pinyin_first_letter_and_full_pinyin_filter": {    //此处的配置可以参见github上插件说明页的可选项配置
          "type": "pinyin",
          "keep_first_letter": true,
          "keep_full_pinyin": true,
          "keep_none_chinese": true,
          "keep_original": true,
          "limit_first_letter_length": 16,
          "lowercase": true,
          "trim_whitespace": true,
          "keep_none_chinese_in_first_letter": true,
          "ignore_pinyin_offset": true
        }
      }
    }
  }
}
```
2. 创建索引mapping
```
POST /doc_resources/doc/_mapping
{
        "properties": {
            "doc_name": {                        //字段名称
                "type": "text",
                "analyzer": "ik_smart",        //指定该字段为ik_smart分词
                "fields": {   
                    "pinyin": {                //子pinyin字段
                        "type": "text",
                        "store": false,
                        "term_vector": "with_offsets",
                        "analyzer": "user_name_analyzer",    //指定为第一步的自定义拼音分词方式
                        "boost": 10
                    }
                }
            }
        } 
}
```

3. 导入数据

```
PUT /doc_resources/doc/1/
{
  "doc_name": "因他“是香港最受尊重和喜爱的演艺名人之一，对香港电影及音乐贡献良多。其严谨专业的工作态度，足以成为年轻人的典范”，为了“表彰他在表演艺术方面的成就”而授予刘德华荣誉院士称号，"
}
```

4. 在拼音字段上面做检索

```
POST /doc_resources/_search
{
  "query": {
    "match_phrase": {
      "doc_name.pinyin": "liudehua"
    }
  }
}
```

5. 查看分词的结果

```
POST /doc_resources/_analyze
{
  "analyzer": "user_name_analyzer",
  "text": ["刘德华"]
}
```

### 参考

https://github.com/medcl/elasticsearch-analysis-pinyin

https://symonlin.github.io/2019/01/07/elasticsearch-4/

## 联合使用

接下来我们要将上面两个结合起来，使用非结构化文本导入&&拼音检索

* 其实很简单，通过拼音的检索的配置流程我们可以看到，是将pinyin字段作为单独的一个子字段进行存放并且指定分词器为拼音分词器，那么一样的，只要预先定义好索引的结构（我们在非结构化数据导入的时候并没有定义好结构而是让es在导入数据的时候默认生成了），那么就能够实现拼音的效果了，定义的索引结构如下

```
PUT /doc_resources/doc/_mapping
{
    "doc": {
        "properties": {
            "attachment": {
                "properties": {
                    "content": {
                        "type": "text",
                        "analyzer": "ik_smart",
                        "fields": {
                            "keyword": {
                                "type": "keyword",
                                "ignore_above": 256
                            },
                            "pinyin": {
                                "type": "text",
                                "store": false,
                                "term_vector": "with_offsets",
                                "analyzer": "user_name_analyzer",
                                "boost": 10
                            }
                        }
                    },
                    "content_length": {
                        "type": "long"
                    },
                    "content_type": {
                        "type": "text",
                        "fields": {
                            "keyword": {
                                "type": "keyword",
                                "ignore_above": 256
                            }
                        }
                    },
                    "language": {
                        "type": "text",
                        "fields": {
                            "keyword": {
                                "type": "keyword",
                                "ignore_above": 256
                            }
                        }
                    }
                }
            }
        }
    }
}
```

* 导入非结构化数据

```
PUT /doc_resources/doc/2?pipeline=attachment
{
     "data":"5oiR54ix5L2g"
}
```

* 查询数据

```
POST /doc_resources/_search
{
  "query": {
    "match_phrase": {
      "attachment.content.pinyin": "woaini"
    }
  }
}

//查询结果
{
  "took" : 5,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 2.6894288,
    "hits" : [
      {
        "_index" : "doc_resources",
        "_type" : "doc",
        "_id" : "2",
        "_score" : 2.6894288,
        "_source" : {
          "attachment" : {
            "content_type" : "text/plain; charset=UTF-8",
            "language" : "lt",
            "content" : "我爱你",
            "content_length" : 4
          }
        }
      }
    ]
  }
}
```

### 总结一下步骤流程

1. 安装es，安装es插件（文本抽取，拼音分词）
2. 设置管道属性（attachment pipeline）
3. 设置指定索引的拼音分词器属性
4. 创建该索引的mapping结构
5. 文件转成base64，导入es
6. 在pinyin分词字段上进行查询
