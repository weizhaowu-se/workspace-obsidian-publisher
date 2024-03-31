---
share: "true"
title: mybatis-threshold参数问题
date: 2020-02-13 15:09:00
tags: 
---

# mybatis-threshold参数问题

## 问题

在mapper里面使用的函数方式使用如下参数

```
@Param("threshold") String threshold
```

mapper.xml里面如下(threshold为该表的一个字段)

```
<select id="queryCountByImageMd5" resultType="java.lang.Integer">
   SELECT
      *
   FROM
      xxxx
   WHERR
       and threshold = #{threshold}
</select>
```

结果报错，提示类型转换失败

## 解决办法

修改参数名称   threshold  ---> testthreshold

```
@Param("testthreshold") String threshold
```

相应修改xml相关参数


