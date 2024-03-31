---
share: "true"
title: elk部署-helloWorld
date: 2020-04-29 21:13:04
tags:
  - elasticsearch
  - kibana
  - logstash
---

# elk部署-helloWorld

## elasticsearch

1. 下载地址 https://www.elastic.co/cn/start
2. 解压文件 elasticsearch-6.2.2
3. 启动，进入bin文件夹，执行

```
./elasticsearch
./elasticsearch -d   #后台模式运行
```

<!--more-->

4. 访问页面  `http://localhost:9200/`，启动成功

```
{
name: "B7lu623",
cluster_name: "elasticsearch",
cluster_uuid: "i4kmAxjsRr-V9v5CDfYn1A",
version: {
number: "6.2.2",
build_hash: "10b1edd",
build_date: "2018-02-16T19:01:30.685723Z",
build_snapshot: false,
lucene_version: "7.2.1",
minimum_wire_compatibility_version: "5.6.0",
minimum_index_compatibility_version: "5.0.0"
},
tagline: "You Know, for Search"
}
```

## kibana

1. 下载地址 https://www.elastic.co/cn/start，需要注意的是版本要需要和elasticsearch对应，否则无法连接
2. 解压文件 kibana-6.2.2-darwin-x86_64
3. 启动，进入bin文件夹，执行

```
./kibana
```

4. 访问页面 http://localhost:5601/
5. 默认情况下kibana连接的elasticsearch地址是 `http://localhost:9200`，如果需要修改的话，可以修改目录 `/config/kibana.yml`文件中的配置 

## logstash

1. 下载地址 https://artifacts.elastic.co/downloads/logstash/logstash-7.6.2.zip
2. 解压
3. 一个最简单的测试，

```
  进入bin目录下，执行   ./logstash -e 'input { stdin {} } output { stdout {} }'
  这个命令的意思是将输入的内容输出到标准输出中，效果如下图
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geayl7bxphj31b00h1gok.jpg)

4. 将日志输出到es当中

* 一般来讲我们是将程序产生的日志文件输出到elasticsearch中，此处我们就直接将elasticsearch的日志输出到es当中，es的日志文件位置在 `logs/elasticsearch.log`
* 新建elasticsearch.conf 的logstash配置文件，放在目录/log-conf/下（手动新建目录）

```
input {
  file {
    path =>"/Applications/sh/elasticsearch-6.2.2/logs/elasticsearch.log"
	type =>"elasticsearch"
	start_position =>"beginning"
	}
}
output {
	elasticsearch {
		hosts=>["127.0.0.1:9200"]
		index =>"es-message-%{+YYYY.MM.dd}"
}       
stdout {
	codec => rubydebug
	}
}
```

* 启动logstash，执行命令  `./bin/logstash -f log-conf/elasticsearch.conf`
* 至此，elk已经部署完成，接下来我们到kibana上进行查看

## 综合查看

* 查看索引

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geaysulclaj311205u75j.jpg)

* 查看索引数据

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geayubbferj31bc0laaeu.jpg)


