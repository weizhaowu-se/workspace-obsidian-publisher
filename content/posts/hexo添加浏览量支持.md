---
share: "true"
title: hexo添加浏览量支持
date: 2022-10-10 20:19:35
tags: 
---

## 参考文档

http://ibruce.info/2015/04/04/busuanzi/

## 修改文件

/themes/next/layout/_partials/footer.swig

## 添加内容

```javascript
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js">
</script>
本站总访问量<span id="busuanzi_value_site_pv"></span>次
本站访客数<span id="busuanzi_value_site_uv"></span>人次
```

## 实现效果

![ebe17135db71431eeb05ae56aaf130d1_MD5.png](/images/ebe17135db71431eeb05ae56aaf130d1_MD5.png)

## 遗留

* 单页面访问量显示
* 数据初始化

