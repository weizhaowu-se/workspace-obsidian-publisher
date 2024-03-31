---
share: "true"
title: mysql之groupby获得分组指定记录
date: 2020-02-06 10:09:11
tags: 
---

# mysql之groupby获得分组指定记录

## 场景及需求

有如下示例表(表名test)：

| 字段编码 | 字段名  |
| -------- | ------- |
| id       | id      |
| 学科     | subject |
| 成绩     | score   |

如果只是想要获得每个学科的最高分的话，那么

```
select subject, score from test group by subject
```

上述sql能够获得需要的结果

但是更多时候我们想要的不仅仅是这样，我们还想要同时取得学科最高分对应的id，那么我们可以这样写

```
select id, subject, score from (select * from test order by score) group by subject
```

### 原理

group by后获取的第一条记录是默认第一条，那么我们先将其排序之后再去获取的，那么就能够获得到我们想要的那一条记录了

### 注意事项

如果mysql版本为5.7及以上，那么需要在排序时增加一个limit

```
select id, subject, score from (select * from test order by score limit 10000) group by subject
```

但是如果记录大于指定的limit的话，那么就会出现问题，所以这种情况只适用于数据量能够掌控的表。

查了一下之后发现另外一种方法

### 另一种方法

* [mysql实现group by后取各分组的最新一条](https://blog.csdn.net/HXNLYW/article/details/102681680?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)

看了之后发现，这种仅限于你想要取得的字段的限定条件是唯一的情况，并不能够满足我们此种场景下的需求（此场景下的限定条件是最大成绩的那一条，而非最大id的记录）

### 所以

如果想规避limit的问题，那么就

```
select subject, score from test group by subject
```

后再用指定的subject和score去查询出对应的记录吧

```
select * from test  where subject = #{subject} and score = #{score}
```


